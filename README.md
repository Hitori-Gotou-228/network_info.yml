# network_info.yml
---
- name: Backup network configuration
  hosts: rtr
  gather_facts: yes
  become: yes
  tasks:
    - name: Gather OSPF configuration (FRR)
      fetch:
        src: /etc/frr/frr.conf
        dest: /etc/ansible/NETWORK_INFO/{{ inventory_hostname }}/frr.conf
        flat: yes

    - name: Gather firewall rules (iptables)
      fetch:
        src: /etc/iptables.rules.v4
        dest: /etc/ansible/NETWORK_INFO/{{ inventory_hostname }}/iptables_rules.v4
        flat: yes

    - name: Gather network settings (interfaces)
      fetch:
        src: /etc/network/interfaces
        dest: /etc/ansible/NETWORK_INFO/{{ inventory_hostname }}/interfaces
        flat: yes

    - name: Gather DHCP settings
      fetch:
        src: /etc/dhcp/dhcpd.conf
        dest: /etc/ansible/NETWORK_INFO/{{ inventory_hostname }}/dhcpd.conf
        flat: yes
      when: inventory_hostname == "hq-rtr"

    - name: Ensure NETWORK_INFO directory exists on BR-SRV
      delegate_to: localhost
      run_once: true
      file:
        path: "/etc/ansible/NETWORK_INFO"
        state: directory
        mode: '0755'
