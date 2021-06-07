> 通过shell的方式启动 facebook 的 webDriverAgent

### 关键词
`wda` `iOS自动化` `iproxy` `webDriverAgent`

```bash
#!/usr/bin/env bash
: '
@File   : start.sh
@Author : 悠然
@Date   : 2020-03-31
@Desc   : 启动WDA
'

#UDID=$1
#PORT=$2
#WDA_PATH=$3
UDID=$(idevice_id -l | head -n1)
PORT=8100
BAT_PORT=8090
CURRENT_PATH=$(cd "$(dirname "$0")" && pwd)
ROOT_PATH=$1
WDA_PATH="$ROOT_PATH"/WebDriverAgent/WebDriverAgent.xcodeproj
URL_HOST="http://localhost:$PORT/status"
SOURCE_HOST="http://localhost:$PORT/source"


print_success(){
  printf "\x1B[0;32m%s\n\x1B[0m" "$1"
}

print_faile(){
  printf "\x1B[0;31m%s\n\x1B[0m" "$1"
}

check_return() {
  value=$?
	if [ $value -ne 0 ]
	then
		print_faile "$1失败"
		exit 1
	else
		print_success "$1成功"
	fi
}

del_wda(){
  echo "删除 wda"
  isInstalled=$(ideviceinstaller -l | grep WebDriverAgentRunner);
  if [ -n "$isInstalled" ]; then
    ideviceinstaller -U com.layzui.test.xctrunner > /dev/null
    check_return '删除 wda'
  fi
}

clear_process_sys(){
  # 清理系统级进程
  CMD_LINE="ps -A | grep -v grep | grep $1 | grep $2 | awk '{print \$1}' | xargs kill -9"
  bash -c "$CMD_LINE"
  check_return "关闭$1:$2服务"
}

clear_port_service(){
  CMD_LINE="lsof -i:$1 | awk 'NR>1{print \$2}' | xargs kill -9"
  bash -c "$CMD_LINE"
  check_return "关闭$1端口服务"
}

clear_service(){
  id="id=$UDID"
  clear_process_sys xcodebuild "$id"
  clear_port_service $PORT
}

check_real_success(){
  retry_time=1
  while ((retry_time--)); do
    if ! check_wda_status; then
      return 1
    fi
    sleep 2
  done
  return 0
}

start_wda(){
  xcode_log="$1"/xcodebuild-"$2".log

  if ! [ -x "$WDA_PATH" ]; then
      print_faile "文件不存在：$WDA_PATH"
      exit 1
  fi
  # echo "xcodebuild -scheme WebDriverAgentRunner -destination \"id=$UDID\" -project \"$WDA_PATH\" test"
  # echo "xcodebuild -scheme WebDriverAgentRunner -destination \"id=$UDID\" -project "$WDA_PATH" test > "$xcode_log" 2>&1 &"
  for (( i = 1; i <= 5; i = i + 1 ))
  do
    echo "start wda service"
    if ((i == 2)); then
      del_wda
    fi
    # cmd="xcodebuild -scheme WebDriverAgentRunner -destination \"id=$UDID\" -project \"$WDA_PATH\" test > \"$xcode_log\" 2>&1"
    # nohup "$("$cmd")" &
    bash_file="$ROOT_PATH"/wda
    echo "nohup xcodebuild -scheme WebDriverAgentRunner -destination \"id=$UDID\" -project \"$WDA_PATH\" test > \"$xcode_log\" 2>&1 &" > "$bash_file"
    chmod 777 "$bash_file"
    bash -c "open -a Terminal.app $bash_file"
    # sh "$bash_file"
    # hup_file="$ROOT_PATH"/hup_file
    # nohup xcodebuild -scheme WebDriverAgentRunner -destination "id=$UDID" -project "$WDA_PATH" test > "$xcode_log" 2>&1 > "$hup_file" &
    # nohup xcodebuild -scheme WebDriverAgentRunner -destination "id=$UDID" -project "$WDA_PATH" test &
    rety_time=20
    sleep_time=3
    while ((rety_time > 0))
    do
      # echo -ne "#"
      printf "%s" "#"
      # is_start=$(tail -30 "$xcode_log" | grep ServerURLHere)
      # is_start=$(curl $URL_HOST -s | grep sessionId )
      # if [ -n "$is_start" ]; then
      if check_wda_status '#'; then
        break
      fi
      ((rety_time--))
      sleep $sleep_time
    done
    if check_real_success; then
        return 0
    fi
    echo '#'
  done
  print_faile '服务启动失败'
  # shellcheck disable=SC2242
  exit 1
}

start_proxy(){
  echo "start proxy service"
  iproxy_log="$1"/iproxy-"$2".log
  iproxy "$PORT" "$PORT" "$UDID" > "$iproxy_log" 2>&1 &
  print_success "start proxy success"
}

check_status(){
  echo 'check_status'
  CMD_LINE="ps -a | grep -v grep | grep xcodebuild | awk '{print \$1, \$8}'"
  xcode_status=$(bash -c "$CMD_LINE")
  echo "$xcode_status"
  strings=${xcode_status// / }
  echo "${strings[@]}"
  udids="${strings[1]}"
  echo "$udids"
  pid="${strings[0]}"
  echo "$pid"
  strings=("${udids//:/ }")
  udids="${strings[0]}"
  echo "$udids"
}

check_wda_status(){
  key=$1
  value=$(curl --connect-timeout 5 "$URL_HOST" -s | grep sessionId)
  if [ -n "$value" ]; then
    value=$(echo "$value"|sed 's/^[ \t]*//g')
    if [ -n "$key" ]; then
      echo "$key"
    fi
    echo "$value"
    return 0
  fi
  return 1
}

handle_service(){
  if check_wda_status ''; then
    start_bat "$log_dir" "$time"
    print_success "启动成功"
    echo "ServerURL:"
    echo "$URL_HOST "
    echo "$SOURCE_HOST"
    exit 0
  fi
}

handle_device(){
  if [ -z "$UDID" ]; then
    print_faile "没有找到任何设备，请重新插入"
    exit 1
  else
    echo "UDID:$UDID"
  fi
}

clear_process(){
  CMD_LINE="ps -a | grep -v grep | grep $1 | grep $2 | awk '{print \$1}' | xargs kill -9"
  bash -c "$CMD_LINE"
  check_return "关闭$1:$2服务"
}

start(){
  echo "-------------- 启动服务 --------------"
  handle_device

  time=$(date '+%Y-%m-%d %H:%M')
  log_dir="$ROOT_PATH"/log/
  mkdir -p "$log_dir"

  # 服务状态检查
  handle_service

  clear_service
  start_proxy "$log_dir" "$time"
  start_wda "$log_dir" "$time"

  echo "log path: $log_dir/log/"
  echo "ServerURL: $URL_HOST "
  print_success "启动成功"
  sleep 2
}

start



```
