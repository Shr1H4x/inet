#==============================================================================
# === NETWORK INFO FUNCTION ===
#==============================================================================

inet() {
  local blue="\033[94m"
  local yellow="\033[33m"
  local reset="\033[0m"

  echo -e "${blue}----IP Networks Available----${reset}"
  printf "${blue}%-17s %-17s %-20s %-18s %-4s %-6s${reset}\n" "Interface" "IP_Address" "Broadcast" "Subnet_Mask" "CIDR" "Status"
  echo -e "${yellow}-----------------------------------------------------------------------------------------${reset}"

  ip -4 -o addr show | awk '
BEGIN {
  red    = "\033[31m"   # UP
  green  = "\033[32m"   # TUN/TAP
  gray   = "\033[37m"   # DOWN
  cyan   = "\033[36m"
  reset  = "\033[0m"

  # Read interface states
  while (( "ls /sys/class/net" | getline iface ) > 0) {
    cmd = "cat /sys/class/net/" iface "/operstate"
    cmd | getline st
    close(cmd)
    # status_table[iface] = (st ~ /up/ ? "UP" : "DOWN)
    if (st == "up")
    status_table[iface] = "UP"
else if (st == "unknown")
    status_table[iface] = "UNKNOWN"
else if (st == "dormant")
    status_table[iface] = "DORMANT"
else
    status_table[iface] = "DOWN"

  }
}

function cidr_to_mask(c,    mask, i, val) {
  mask=""
  for (i=0;i<4;i++) {
    if (c>=8) { mask=mask"255"; c-=8 }
    else { val=256-2^(8-c); mask=mask val; c=0 }
    if (i<3) mask=mask"."
  }
  return mask
}

# Tunnel detector
function is_tunnel(iface) {
  return iface ~ /^(tun|tap|wg|zt|tailscale|ppp|vpn)[0-9]*/
}

{
  split($4,a,"/")
  ipaddr=a[1]
  cidr=a[2]
  mask=cidr_to_mask(cidr)
  brd="N/A"
  for(i=1;i<=NF;i++) if($i=="brd") brd=$(i+1)

  iface=$2
  status=status_table[iface]

  if (is_tunnel(iface))
    color = red        # VPNs always scream RED
else if (status == "UP")
    color = red
else if (status == "UNKNOWN" || status == "DORMANT")
    color = cyan
else
    color = gray


  printf "%s%-17s%s %s%-17s%s %s%-20s%s %s%-18s%s %s%-4s%s %s%-6s%s\n",
    color,iface,reset,
    color,ipaddr,reset,
    color,brd,reset,
    color,mask,reset,
    color,cidr,reset,
    color,status,reset
}'


  # Public IP
  echo
  echo -e "${blue}----Public_IP----${reset}"
  local pub_ip4=$(curl -4 -s --connect-timeout 5 ifconfig.me 2>/dev/null)
  local pub_ip6=$(curl -6 -s --connect-timeout 5 ifconfig.me 2>/dev/null)
  [ -n "$pub_ip4" ] && echo -e "IPv4: \033[31m$pub_ip4${reset}" || echo -e "IPv4: \033[36mNot reachable${reset}"
  [ -n "$pub_ip6" ] && echo -e "IPv6: \033[31m$pub_ip6${reset}" || echo -e "IPv6: \033[36mNot reachable${reset}"

  echo
  echo -e "${blue}----Tor_Public_IP (via SOCKS)----${reset}"
  local tor_services=(
    "https://ident.me"
    "https://ipinfo.io/ip"
    "https://api.ipify.org"
  )
  local tor_ip=""
  for svc in "${tor_services[@]}"; do
    tor_ip=$(curl --socks5-hostname 127.0.0.1:9050 --connect-timeout 8 -s "$svc" 2>/dev/null)
    [[ -n "$tor_ip" ]] && break
  done

  if [[ -n "$tor_ip" ]]; then
    echo -e "Tor IPv4: \033[31m$tor_ip${reset}"
  else
    echo -e "Tor IPv4: \033[36mTor not started${reset}"
  fi
}
