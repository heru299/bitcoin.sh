#!/bin/bash
### BEGIN INIT INFO
# Provides:          BitCoinMining
# Required-Start:
# Required-Stop:
# Default-Start:
# Default-Stop:
# X-Interactive:     false
# Short-Description: Start/stop BitCoing Mining poclbm-mod
### END INIT INFO

# This is my private script for /etc/init.d/bitcoin
# It's gonna be executed everytime my debian starts up.
# I do not use my GPU a lot like the most Linux users (Minecraft excepted).
# This is becaus I run my bitcoin miner everyday.
# Use with this care.

# Define as startup do:
# $ wget -O /etc/init.d/bitcoin https://gist.github.com/
# chmod +x /etc/init.d/bitcoin
# update-rc.d bitcoin defaults

# Path to poclbm mining script
path_poclbm=/path/to/poclbm/dir/

# Pool data
POOLUSER=username@mail.com
POOLPASS=password
POOLURL=pit.deepbit.net
POOLPORT=8332

# Environment
BTCLOG=/var/log/bitcoin.log
BTCLOCK=/var/lock/bitcoinmining

case "$1" in
  start)
    echo $$ > $BTCLOCK
    cd $path_poclbm
    python poclbm-mod.py -d 0 -v -w 128 -f 60 -a 10 --user=$POOLUSER --pass=$POOLPASS -o $POOLURL -p $POOLPORT > $BTCLOG &
    ;;
  stop)
    if [ -r $BTCLOCK ]; then
        BTCPID=$(cat $BTCLOCK)
        # Increase PID because of its childy spawned command poclbm
        ((BTCPID++))
        kill $BTCPID
        rm $BTCLOCK
        exit 0
    fi
    ;;
  *)
    echo "Usage: /etc/init.d/bitcoin {start|stop}"
    exit 1
    ;;
esac
exit 0
