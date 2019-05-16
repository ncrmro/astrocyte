sudo apt install stress-ng

stress-ng --class cpu --sequential 4 -t 60 -v --tz
