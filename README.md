# ansible-fortigate-create_interface

  hosts: firewall
  connection: httpapi
  tasks:
  - name: LACP Interface oluşturuluyor.
    fortios_system_interface:
      state: present
      system_interface:
         vdom: root
         name: "LACP"
         type: "aggregate"
         allowaccess: ['ssh','ping']
         status: up
         lacp_mode: active
         lacp_speed: fast
         member:
               - interface_name: port3
               - interface_name: port4
  - name: Vlan-ID, Vlan isim ve Network detayları dosyadan okunuyor.
    read_csv:
       path: list.csv
    register: liste

  - name: Vlan'lar açılıyor.
    fortios_system_interface:
      state: present
      system_interface:
         vdom: root
         name: '{{ data.name }}'
         type: "vlan"
         vlanid: '{{ data.id }}'
         interface: "LACP"
         mode: "static"
         ip: '{{ data.ip_address }}'
         allowaccess: ['ssh','ping']
         status: down
         dhcp_relay_agent_option: enable
         dhcp_relay_service: enable
         dhcp_relay_type: regular
         dhcp_relay_ip: 10.25.80.40


    loop: "{{ liste.list }}"
    loop_control:
      loop_var: data

