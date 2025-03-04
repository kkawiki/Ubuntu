# ChatGPT를 통한 우분투 네트워크 세팅
# sudo su 를 통해 관리자 권한 먼저 접근 후 작업하면 용이했음

Ubuntu 24.04에서 네트워크 설정을 Static(고정 IP) 으로 설정하려면 Netplan을 사용해야 해. 아래 과정을 차례대로 따라 하면 돼.
________________________________________
1. 네트워크 인터페이스 확인
먼저, 현재 사용 중인 네트워크 인터페이스를 확인해야 해.


```
ip link show
```
보통 eth0, ens33, ens192 등의 이름으로 되어 있어. 여기서는 ens33이라고 가정할게.
________________________________________
2. Netplan 설정 파일 수정
Netplan 설정 파일은 /etc/netplan/ 디렉터리에 위치해 있어.
기본적으로 yaml 파일(예: 00-installer-config.yaml)이 존재하는데, 없으면 새로 생성하면 돼.
```
sudo nano /etc/netplan/00-installer-config.yaml
```
파일을 열고 아래와 같이 설정해 줘.
✅ Static IP 설정 예제
```
yaml
복사편집
network:
  version: 2
  ethernets:
    ens33:  # 자신의 인터페이스 이름으로 변경
      addresses:
        - 192.168.1.100/24  # 고정 IP 설정
      gateway4: 192.168.1.1  # 기본 게이트웨이
      nameservers:
        addresses:
          - 8.8.8.8  # 구글 DNS
          - 8.8.4.4
```
위 설정에서
•	addresses 항목에 고정 IP 및 서브넷 마스크(예: 192.168.1.100/24)를 입력

•	gateway4 항목에 기본 게이트웨이(예: 192.168.1.1)를 입력

•	nameservers 항목에 DNS 서버(예: 8.8.8.8, 8.8.4.4)를 입력
________________________________________
3. 설정 적용 및 확인
파일을 저장한 후, 설정을 적용해.
```
sudo netplan apply
```
문제가 발생하면 디버깅을 위해 아래 명령어로 확인 가능해.
```
sudo netplan try
```
설정이 제대로 적용되었는지 확인하려면 다음 명령어를 실행하면 돼.
```
ip addr show ens33  # 인터페이스 이름에 맞게 변경
```
또는
```
ip route
```
________________________________________
4. 재부팅 후 설정 유지 확인
재부팅 후에도 설정이 유지되는지 확인하려면
```
sudo reboot
```
재부팅 후 네트워크 설정을 다시 확인해 봐.
________________________________________
✅ 추가 설정이 필요한 경우
1) DHCP로 다시 변경하려면?
위 설정을 DHCP로 되돌리려면 00-installer-config.yaml을 아래처럼 수정하면 돼.
```
network:
  version: 2
  ethernets:
    ens33:
      dhcp4: true
```
적용 후 다시 실행
```
sudo netplan apply
```
2) IPv6 설정 추가하려면?
IPv6도 함께 설정하려면 아래처럼 추가하면 돼.
```
network:
  version: 2
  ethernets:
    ens33:
      addresses:
        - 192.168.1.100/24
        - 2001:db8::100/64  # IPv6 주소 추가
      gateway4: 192.168.1.1
      gateway6: 2001:db8::1  # IPv6 게이트웨이 추가
      nameservers:
        addresses:
          - 8.8.8.8
          - 8.8.4.4
```
________________________________________
이렇게 설정하면 Ubuntu 24.04에서 Static IP가 적용된 네트워크 설정이 완료돼!

