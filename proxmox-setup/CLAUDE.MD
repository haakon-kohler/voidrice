# Proxmox homelab setup

- Target: Proxmox VE 9.2 (Debian trixie) on an old laptop, reachable at root@<PROXMOX-IP> via SSH key auth
- Run all changes on the server via `ssh root@<PROXMOX-IP> '<command>'`
- Networking (current, verified 2026-07-17):
  - Primary: wired ethernet on vmbr0 (bridge-ports <eth-interface>, static <ip>.52/24, gateway <ip>.1)
  - Backup: WiFi on wlp1s0, SSID <HOME-SSID>, DHCP (currently <ip>.165 — may change), auto-failover if ethernet drops
  - WiFi config: /etc/wpa_supplicant/wpa_supplicant-wlp1s0.conf (hashed PSK) + /etc/network/interfaces.d/wlp1s0
  - /etc/dhcp/dhclient-enter-hooks.d/wlp1s0-metric sets WiFi route metric 200 — needed because ifupdown2 ignores the `metric` stanza option; don't remove it
  - /etc/sysctl.d/80-linkdown-failover.conf sets ignore_routes_with_linkdown=1 so failover actually works; don't remove it
  - Failover live-tested: with ethernet down, SSH and internet work over WiFi; wired auto-preferred again on reconnect
- WiFi SSID/password: I will provide when asked — do not guess
- Repos: pve-no-subscription active; enterprise + ceph sources disabled (.disabled). apt update verified clean 2026-07-17, dist-upgraded same day
- HandleLidSwitch=ignore set via /etc/systemd/logind.conf.d/lid.conf (verified active)
- sleep/suspend/hibernate/hybrid-sleep.target all masked — laptop must never sleep; wireless-tools installed
- Check current Proxmox 9 docs when unsure; don't assume from older versions
