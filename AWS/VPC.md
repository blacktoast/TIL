# AWS - VPC
>[AWS Certified Solutions Architect - Associate 2018](https://www.udemy.com/aws-certified-solutions-architect-associate/) 강의를 듣고 내용 정리  

![VPC Diagram](https://github.com/Integerous/TIL/blob/master/AWS/img/VPCdiagram.png?raw=true)
## What is a VPC ?
VPC는 클라우드 내의 **가상 데이터 센터**.

## What can you do with a VPC ?
- 내가 선택한 서브넷에 인스턴스를 실행할 수 있다.
- 각각의 서브넷에 커스텀 IP주소 범위를 할당할 수 있다.
- 서브넷 간의 Route tables를 설정할 수 있다.
  - Route Tables : 어떤 서브넷이 다른 서브넷들과 speak 할 수 있는지 정해놓은 것.
- 인터넷 게이트웨이를 생성하고 우리의 VPC에 붙일 수 있다.
- AWS 자원들을 더욱 안전하게 관리할 수 있다.
  - 서브넷 사용, 특정 IP주소 막기, 네트워크 ACLS 등을 통해서.
- 인스턴스 보안그룹
- 서브넷 네트워크 접근 관리 리스트(ACLS)

## Default VPC vs Custom VPC

### Default VPC
- 사용하기 쉽고, 인스턴스를 즉시 배포할 수 있게한다.
- Default VPC의 모든 서브넷들은 인터넷에 연결할 수 있다.
- 프라이빗 서브넷이 없다.
- 각각의 EC2 인스턴스는 퍼블릭 IP와 프라이빗IP를 모두 가지고 있다

### Custom VPC
- 프라이빗 서브넷이 있다.
- 각각의 EC2 인스턴스는 프라이빗 IP만 가지고 있다.

## VPC Peering
- 프라이빗 IP주소들을 이용해서 VPC 끼리 다이렉트 네트워크 루트로 연결한다.
- 인스턴스들은 같은 프라이빗 네트워크에 있는 것처럼 여겨진다.
- 다른 AWS 계정의 VPC와도 연결할 수 있고, 같은 계정의 VPC와도 연결할 수 있다.
- VPC A와 B, A와 C가 연결되어있어도 B와 C가 연결되어있지 않으면 연결되지 않는다. (we don't have transitive peering in VPC)  
![](https://github.com/Integerous/TIL/blob/master/AWS/img/VPCtransitivePeering.png?raw=true)

## Summary
- VPC는 AWS 내의 논리적 데이터센터라 생각하면 된다.
- 구성
  - Internet Gateways 또는 Virtual Private Gateways
  - Route Tables
  - Network Acess Control Lists
  - Subnets
  - Security Groups
- 1 Subnet = 1 Availability Zone
- Security Groups는 Stateful 하다.(네트워크 연결 상태를 추적할 수 있다.)
- Network Access Control Lists는 Stateless 하다.
- NO TRANSITIVE PEERING

## VPC 실습 1
### 1. **VPC 만들기**
  - Your VPCs 로 이동
  - Create VPC
  - Name tag 및 IPv4 CIDR block 설정 (가장 큰 범위는 10.0.0.0/16)
  - IPv6 CIDR block = Amazon provided IPv6 CIDR block 으로 설정해야 나중에 더 많은 옵션이 생긴다.
  - Tenancy 선택
    - Default: 다른 고객과 하드웨어를 공유한다.(Multi tenant environment)
    - Dedicate: 독립된 하드웨어를 사용하므로 더 비싸다.  
### 2. **현재까지 상태**  
![](https://github.com/Integerous/TIL/blob/master/AWS/img/VPC%20with%20Public%20&%20Private%20Subnet1.png?raw=true)
### 3. **Subnet 만들기**
  - Subnets로 이동
  - Create Subnets
  - VPC 선택
  - Name tag 설정(강사는 CIDR block range와 AZ을 나타내기위해 `10.0.1.0 - us-east-1a` 라고 설정한다.)
  - Availability Zone 설정 (us-east-1a)
  - IPv4 CIDR block 설정 (10.0.1.0/24)
  - IPv6 CIDR block 선택 (강사는 안함)
  - Yes, Create 클릭
### 4. 각 서브넷 CIDR 블록에서 첫 4개의 IP 주소와 마지막 IP 주소는 사용자가 사용할 수 없으므로 인스턴스에 할당할 수 없습니다. 예를 들어 10.0.0.0/24 CIDR 블록의 서브넷에서는 다음 5개 IP 주소가 예약되어 있습니다.
  - 10.0.0.0: 네트워크 주소.
  - 10.0.0.1: AWS에서 VPC 라우터용으로 예약한 주소.
  - 10.0.0.2: AWS에서 예약한 주소. DNS 서버의 IP 주소는 항상 VPC 네트워크 범위를 기초로 2를 더한 것입니다. 그러나 에서는 각 서브넷 범위를 기초로 2를 더한 것도 예약합니다. CIDR 블록이 여러 개인 VPC의 경우, DNS 서버의 IP 주소가 기본 CIDR에 위치합니다. 자세한 내용은 Amazon DNS 서버 단원을 참조하십시오.
  - 10.0.0.3: AWS에서 앞으로 사용하려고 예약한 주소.
  - 10.0.0.255: 네트워크 브로드캐스트 주소. VPC에서는 브로드캐스트를 지원하지 않으므로, 이 주소를 예약합니다.
### 5. **두번째 Subnet 만들기**
  - IPv4 CIDR block 설정 (10.0.2.0/24)
  - AZ 설정 (us-east-1b)
### 6. **현재까지 상태**    
![](https://github.com/Integerous/TIL/blob/master/AWS/img/VPC%20with%20Public%20&%20Private%20Subnet2.png?raw=true)
### 7. **Internet Gateway 만들기**
  - Create Internet Gateway
  - Name tag 설정 (강의에서는 MyIGW) 및 Create
  - Attach to VPC
  - VPC 선택 후 Yes, Attach
  - *하나의 Internet Gateway는 하나의 VPC 에만 붙일 수 있다.*
### 8. **Route Table 만들기**
  - 새로운 서브넷을 만들면 default로 main route table에 연결된다. 하지만 매번 서브넷을 provisioning 할 때마다 main route table이 인터넷 Accessable하기 때문에 새로운 route table을 만든다.
  - Create Route Table
  - Name tag (강의에서는 MyInternetRouteOut) 및 VPC 설정
  - Yes, Create
### 9. **Route Table의 인터넷 연결 설정하기**
  - 새로 생성한 Route Table 선택
  - 밑의 Routes 선택하고 Edit 클릭
  - Add another route (0.0.0.0/0 , target = 좀전에 생성한 Interent Gateway)
  - Save
  - Add another route (::/0 , target = 위와 동일) 로 IPv6도 설정해준다.
  - 이제 이 route에 연결되는 모든 서브넷은 Public 서브넷이 된다.
### 10. **Subnet을 Route Table과 연결하기**
  - Route Tables -> 밑에 Subnet Associations 클릭
  - Edit
  - Public Subnet으로 사용할 서브넷(10.0.1.0)을 선택하고 Save. (나머지 한 서브넷은 Private이 된다.)
  - Subnets로 가서 오른쪽 끝부분에 Auto-assign Public IP을 확인하면 새로 추가한 서브넷들은 No로 되어있다.
  - Public Subnet으로 사용할 서브넷을 선택하고
  - Subnet Actions -> Modify auto-assign IP settings -> Enable auto-assign public IPv4 address 체크
  - Save
### 11. **Public 서브넷을 위한 EC2 생성**
  - EC2로 이동
  - Launch Instance 클릭
  - Amazon Linux AMI 클릭
  - Network를 Default VPC에서 Custom VPC로 변경
  - Subnet 선택 (10.0.1.0) 
  - next next
  - Add tag ( Name / WebServer01)
  - next
  - Security group 생성 (강의에서는 Web-DMZ)
    - SSH / 22 / 0.0.0.0/0
    - HTTP / 80 / 0.0.0.0/0, ::/0
  - Keypair 선택 후 시작
### 12. **Private 서브넷을 위한 EC2 생성**
  - Subnet 선택 (10.0.2.0)
  - Add tag ( Name / MyPrivateServer )
  - Security 그룹은 default로
  - Keypair는 위에꺼랑 같은 것
### 13. **ssh로 접속**
### 14. **현재까지 상태**  
![](https://github.com/Integerous/TIL/blob/master/AWS/img/VPC%20with%20Public%20&%20Private%20Subnet3.png?raw=true)  
### 15. **이후에 해야할 것**
  - 두 개의 보안그룹은 각각 Public 과 Default고,
  - 두 개의 서브넷은 각각 Public과 Private 이다.
  - 그래서 두 개의 인스턴스는 서로 다른 보안그룹과 AZ 때문에 서로 연결할 수 없는 상태이다.
  - 그러므로 이후에는 연결할 수 있도록.

## VPC 실습 2
>실습 1에 이어 Public 서브넷을 통해 Private 서브넷에 접속하도록 하는 것이 핵심
### 1. 인스턴스명 및 보안그룹 수정
  - MyPrivateServer -> MyMySQL
  - Security group 생성
    - 그룹이름,descripting 설정 (강의에서는 My-RDS-SG)
    - VPC 선택 (acloudguruVPC)
    - add rule
      - SSH / 22 / 10.0.1.0/24
      - MYSQL/Aurora / 3306 / 10.0.1.0/24
      - HTTP / 80 / 10.0.1.0/24
      - HTTPS / 443 / 10.0.1.0/24
      - All ICMP-IPv4 / 0 - 65535 / 10.0.1.0/24
  - 인스턴스로 가서 -> MyMySQL 선택 -> action -> change security group 선택 -> My-RDS-SG로 변경
### 2. ssh로 Public 서버에서 Private 서버로 접속해보기 (실무에서는 절대 하지 말 것)
  - ping 10.0.2.143
  - nano mypvk.pem -> 키 내용 복붙
  - chmod 400 mypvk.pem
  - ssh ec2-user@10.0.2.143 -i mypvk.pem
  
## NAT Instances & NAT Gateways
### 1. NAT Instance 설정하기
  - EC2로 이동
  - 인스턴스 시작하기
  - Community AMIs 선택
  - nat 검색
  - amzn-ami-vpc-nat-hvm~.gp2 선택
  - VPC 와 Subnet 선택
  - Tag 설정 Name / NAT-INSTANCE
  - 보안그룹 web-DMZ 선택
  - 시작
  - 보안그룹 web-DMZ에 Https / 443 / 0.0.0.0,::/0 추가
  - NAT-INSTANCE 선택 후, Actions - Networking - Change Source/Dest. Check 선택
    - 각각의 EC2 인스턴스는 디폴트로 source/destination을 체크한다. 이것은 인스턴스가 주고 받는 모든 트래픽의 source 또는 destination이 되어야한다는 뜻이다. 하지만 NAT 인스턴스는 스스로가 source/destination이 아니더라도 반드시 트래픽을 주고받을 수 있어야 한다. 그러므로 NAT 인스턴스의 source/destination checks를 반드시 disable it 해야한다.
  
  - VPC - Route Tables 로 이동
  - Default Route 테이블을 선택하고 여기에서 NAT 인스턴스에 의해 Route out 되도록 하기 위해 밑에 Routes 클릭해서 Add another route 클릭
  - Destiantion에 0.0.0.0/0 넣고 Target에는 MyIGW가 아닌 NAT 인스턴스 선택. 이로써 NAT 인스턴스에서 바깥세상으로 나가는 Route out 설정된 것.
### 2. NAT 인스턴스의 한계
- NAT 인스턴스로 구성하는것은 단일 인스턴스에 단일 AZ이기 때문에 병목현상에 취약하고, 문제발생시 Private Subnet 안의 모든 서비스가 인터넷 엑세스를 잃게 된다.
- Auto scaling group에 추가하거나 multiple AZ과 multiple Route out to internet을 가질 수 있지만, 점점 복잡해질 뿐이다.
- 다행히 아마존이 NAT Gateway를 출시했고, 이를 통해 단일 인스턴스와 단일 AZ에 의존하는 것에서 벗어날 수 있다.

### 3. NAT Gateway 설정하기
  - VPC로 이동
  - NAT Gateway는 IPv4로 동작하고, Egress Only Internet Gateway는 IPv6로 동작한다.
  - NAT Gateway 선택 및 Create NAT Gateway 클릭
  - Subnet은 커스텀 VPC의 퍼블릭 서브넷 선택
  - Create New EIP 클릭
  - Create a NAT Gateway 클릭 (생성되는데 15분정도 소요)
  - NAT Gateways로 가서 만든 NAT Gateway의 Status가 available이 되면 Route Tables로 이동
  - Default Route Table 선택 및 밑의 Routes 클릭
  - Add another route (Destination: 0.0.0.0/0  Target: NAT Gateway) 및 save.
  - EC2 인스턴스로 이동

### 4. NAT Gateway와 NAT Instance 비교
  - Availability
    - NAT Gateway : 독립적인 아키텍쳐를 구축하기 위해 각각의 AZ에 NAT Gateway를 생성할 수 있다.
    - NAT Instance : 인스턴스들 간의 failover(시스템 대체 작동)를 관리하기 위해 script 사용
  - Bandwidth
    - NAT Gateway : 10Gbps 까지 지원
    - NAT Instance : 인스턴스 타입의 대역폭에 따라 다름
  - Maintenance
    - NAT Gateway : AWS에 의해 관리된다.
    - NAT Instance : 직접 관리해야한다. (ex 소프트웨어를 업데이트하거나 인스턴스에 OS patch)  
  - Performance
    - NAT Gateway : 소프트웨어가 NAT 트래픽을 핸들링하기 위해 최적화되어있다.
    - NAT Instance : A generic Amazon Linux AMI that's configured to perform NAT
  - Cost
    - NAT Gateway : 사용한 NAT 게이트웨이의 수, 사용시간, NAT 게이트웨이를 통해 보낸 데이터의 크기에 따라 요금이 부과된다.
    - NAT Instance : 사용한 NAT 인스턴스의 수, 사용시간, 인스턴스의 타입과 사이즈에 따라 요금이 부과된다.
  - Type and Size
    - NAT Gateway : Uniform offering - 타입과 사이즈를 결정할 필요없다.
    - NAT Instance : 예상되는 워크로드에 따라 알맞는 인스턴스 타입과 사이즈를 선택해야한다.
  - Public IP Addresses
    - NAT Gateway : 생성시에 NAT Gateway와 연결시킬 수 있는 Elastic IP address를 선택한다.
    - NAT Instance : Elastic IP address 또는 Public IP address를 사용한다.
  - Private IP Addresses
    - NAT Gateway : 게이트웨이를 생성할 때 서브넷의 IP주소 범위에서 자동으로 선택된다.
    - NAT Instance : 인스턴스를 런칭할때 서브넷의 IP주소 범위에서 특정 private IP주소를 할당한다.
  - Security Groups
    - NAT Gateway : NAT Gateway와 연결할 수 없다. 대신 보안그룹을 NAT 게이트웨이 뒤의 리소스와 연결하여 인바운드/아웃바운드 트래픽을 제어할 수 있다.
    - NAT Instance : NAT Instance와 그 뒤의 리소스와 연결하여 인바운드/아웃바운드 트래픽을 제어한다.
  - Network ACLs
    - NAT Gateway : 네트워크 ACL을 사용하여 NAT 게이트웨이가 위치하고 있는 서브넷에서 보내고 받는 트래픽을 제어한다.
    - NAT Instance : 네트워크 ACL을 사용하여 NAT 인스턴스가 위치하고 있는 서브넷에서 보내고 받는 트래픽을 제어한다.
  - Flow Logs
    - NAT Gateway : 트래픽을 캡쳐하기 위해 flow logs를 사용한다.
    - NAT Instance : 트래픽을 캡쳐하기 위해 flow logs를 사용한다.
  - Port Forwarding
    - NAT Gateway : 지원하지 않는다.
    - NAT Instance : 포트포워딩을 위해 수동으로 설정을 커스터마이징 해야한다.
  - Bastion Servers
    - NAT Gateway : 지원하지 않는다.
    - NAT Instance : bastion 서버로서 사용한다.
  - Traffic Metrics
    - NAT Gateway : NAT 게이트웨이의 CloudWatch 지표를 확인.
    - NAT Instance : 인스턴스의 CloudWatch 지표 확인
  - Timeout behavior
    - NAT Gateway : 연결 제한시간이 초과하면 NAT 게이트웨이는 연결을 계속하려하는 NAT 게이트웨이 뒤의 리소스로 RST 패킷을 반환한다.(FIN 패킷 안보냄)
    - NAT Instance : 연결 제한시간이 초과하면 NAT 인스턴스는 NAT 인스턴스 뒤의 리소스로 FIN 패킷을 전송하여 연결을 닫는다.
  - IP fragmentation
    - NAT Gateway : UDP 프로토콜에서 IP 조각화된 패킷의 전달을 지원한다. TCP 및 ICMP 프로토콜에 대해서는 조각화를 지원하지 않고, 이러한 프로토콜의 조각화된 패킷은 삭제된다.
    - NAT Instance : UDP, TCP 및 ICMP 프로토콜에 대해 IP 조각화된 패킷의 재수집을 지원한다.

### 5.