---
lab:
    title: 'LAB 09_Azure DNS'
    module: '모듈 02 - 플랫폼 보호'
---

# 랩: Azure DNS

**Scenario**

이 모듈에서는 DNS 기본 사항과 Azure DNS를 구체적으로 구현하는 방법에 대해 학습합니다. DNS 기본사항에서는 DNS 도메인, 영역, 레코드 유형 및 resolution 방법을 검토하십시오. Azure DNS에서는 위임(delegation), 메트릭, 경고 및 DNS 호스팅 체계를 다룹니다.

**목표**

이 랩은 다음 내용을 포함합니다.

 * Azure DNS Basics
 * Azure DNS 구현


### 연습 1: DNS 영역

#### 작업 1: DNS 영역 생성

1.  Azure 포털에 로그인한다.

2.  **DNS 영역**을 탐색하여 클릭한다. 

     ![Screenshot](../Media/Module-2/2c8b996d-d6b5-4cfa-9832-55b2479aa5fe.png)

1. **+ 추가**를 클릭한다.

     ![Screenshot](../Media/Module-2/cb81d587-ad5d-45b7-a251-a6743fbf5a8b.png)

4.  **DNS 영역 만들기** 블레이드에서 다음 값을 사용한 후, **검토 + 만들기**를 클릭한다. 유효성 검사에 통과하면 **만들기**를 클릭한다.

     | **설정** | **값** | 
     |------|---|---|
     | **구독** | **이 랩에서 사용할 구독의 이름** |
     | **리소스 그룹** | 새로 만들기: **az5000209** |
     | **이름** | **고유한 이름으로 설정(예시 : krazurelab.com)** |
     | **리소스 그룹 위치** | 동남아시아|

     ![Screenshot](../Media/Module-2/8a6454d2-1b27-4f54-91e8-69c764406c78.png)


### 연습 2: Azure 포털을 통한 DNS 레코드 및 레코드 셋 관리

Azure 포털을 사용하여 DNS 레코드와 레코드 셋을 관리하는 방법을 실습합니다.

#### 작업 1: 레코드 세트에 새로운 레코드 추가

1.  Azure 포털에서 이전 작업에서 배포한 DNS 영역을 탐색하여 클릭한다. 

    **참고:** Each DNS zone is its own resource, and information such as number of record-sets and name servers are viewable from this view. 

 
3.  Click **+ Record Set**.
 
     ![Screenshot](../Media/Module-2/51a2fd36-c2df-468d-91f7-9eb0791dd7ba.png)

4.  Enter **testrecord** for the name and **1.2.3.4** as the IP address and click **OK**.

     ![Screenshot](../Media/Module-2/6e491490-0b00-4dda-b0e3-28a3f1784171.png)

#### Task 2: Update a record

1.  In the Overview blade for your DNS zone, select the testrecord you created.

      ![Screenshot](../Media/Module-2/8c10684e-05bf-46dd-9d85-599bcd4cb54b.png)
 
2.  Under IP Address add the test address of **4.3.2.1** and click **Save**.

     ![Screenshot](../Media/Module-2/cf207752-7e3b-4b88-9514-c272d5cdd971.png)
 
#### Task 3: Remove a record from a record set


You can use the Azure portal to remove records from a record set. Note that removing the last record from a record set does not delete the record set.


1.  In the Overview pane for your DNS zone, select the testrecord you created.

     ![Screenshot](../Media/Module-2/8c10684e-05bf-46dd-9d85-599bcd4cb54b.png)

2.  Select **Delete** and click **Yes** when prompted.

      ![Screenshot](../Media/Module-2/e841dc4f-440d-4244-a3a8-386d10c65dec.png)
 

**Work with NS and SOA records**

NS and SOA records that are automatically created are managed differently from other record types.

**Modify SOA records**

You cannot add or remove records from the automatically created SOA record set at the zone apex (name = "\@"). However, you can modify any of the parameters within the SOA record (except "Host") and the record set TTL.

**Modify NS records at the zone apex**

The NS record set at the zone apex is automatically created with each DNS zone. It contains the names of the Azure DNS name servers assigned to the zone.

You can add additional name servers to this NS record set, to support co-hosting domains with more than one DNS provider. You can also modify the TTL and metadata for this record set. However, you cannot remove or modify the pre-populated Azure DNS name servers.

Note that this applies only to the NS record set at the zone apex. Other NS record sets in your zone (as used to delegate child zones) can be modified without constraint.

**Delete SOA or NS record sets**

You cannot delete the SOA and NS record sets at the zone apex (name = "\@") that are created automatically when the zone is created. They are deleted automatically when you delete the zone.


You are then prompted to confirm you are wanting to delete the DNS zone. Deleting a DNS zone also deletes all records that are contained in the zone.


| WARNING: Prior to continuing you should remove all resources used for this lab.  To do this in the **Azure Portal** click **Resource groups**.  Select any resources groups you have created.  On the resource group blade click **Delete Resource group**, enter the Resource Group Name and click **Delete**.  Repeat the process for any additional Resource Groups you may have created. **Failure to do this may cause issues with other labs.** |
| --- |
