---
title: Azure로의 Contoso 마이그레이션 개요 | Microsoft Docs
description: Contoso에서 온-프레미스 데이터 센터를 Azure로 마이그레이션하는 데 사용되는 마이그레이션 전략과 시나리오에 대한 개요를 제공합니다.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: raynew
ms.openlocfilehash: be9302d47766ddc7d75496e28e9b18a8e870c878
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2018
ms.locfileid: "39216243"
---
# <a name="contoso-migration-overview"></a>Contoso 마이그레이션: 개요


이 문서에서는 가상의 조직인 Contoso에서 온-프레미스 인프라를 [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) 클라우드로 마이그레이션하는 방법에 대해 설명합니다. 

이 문서는 가상 회사 Contoso가 Azure로 마이그레이션하는 방법을 보여 주는 문서 시리즈 중 첫 번째입니다. 이 시리즈에는 정보 및 마이그레이션 인프라를 설정하고, 다양한 유형의 마이그레이션을 실행하는 방법을 보여 주는 시나리오가 포함되어 있습니다. 시나리오가 점점 더 복잡해지므로 점차 문서를 추가할 예정입니다. 이러한 문서에서는 Contoso 회사에서 마이그레이션 임무를 수행하는 방법을 보여 주지만, 일반적인 참조를 위한 일반적인 참조를 위한 조언과 구체적인 지침이 도처에서 제공됩니다.

## <a name="introduction"></a>소개

Azure는 포괄적인 클라우드 서비스 집합에 대한 액세스를 제공합니다. 개발자와 IT 전문가는 이러한 서비스를 사용하여 데이터 센터의 글로벌 네트워크를 통해 다양한 도구 및 프레임워크에서 응용 프로그램을 빌드하고, 배포하고, 관리할 수 있습니다. 비즈니스가 디지털 전환과 관련된 문제에 직면하면서 Azure 클라우드를 사용하면 리소스 및 운영을 최적화하고, 고객 및 직원과 소통하고, 제품을 혁신하는 방법을 파악하는 데 도움이 됩니다.

하지만 속도와 유연성, 최소화된 비용, 성능 및 안정성 측면에서 클라우드가 제공하는 모든 장점에도 불구하고, 앞으로 얼마간은 많은 조직에서 온-프레미스 데이터센터를 사용할 필요가 있습니다. 클라우드 채택 장벽에 대한 대응으로 Azure는 온-프레미스 데이터센터와 Azure 공용 클라우드 간의 브리지를 구축하는 하이브리드 클라우드 전략을 제공합니다. 예를 들어 Azure Backup과 같은 Azure 클라우드 리소스를 사용하여 온-프레미스 리소스를 보호하거나, Azure 분석을 사용하여 온-프레미스 워크로드에 대한 인사이트를 얻을 수 있습니다. 

하이브리드 클라우드 전략의 일환으로 Azure는 온-프레미스 앱 및 워크로드를 클라우드로 마이그레이션할 수 있는 성장하는 솔루션을 제공합니다. 간단한 단계를 통해 온-프레미스 리소스를 종합적으로 평가하여 Azure 클라우드에서 어떻게 실행될지를 파악할 수 있습니다. 그런 다음, 심층적인 평가를 통한 확신을 가지고 리소스를 Azure로 마이그레이션할 수 있습니다. Azure에서 리소스가 시작되어 실행되면 리소스를 최적화하여 액세스, 유연성, 보안 및 안정성을 유지하고 향상시킬 수 있습니다.

## <a name="migration-strategies"></a>마이그레이션 전략

클라우드로 마이그레이션하기 위한 전략은 다시 호스트, 리팩터링, 아키텍트 변경 또는 다시 빌드라는 네 가지 광범위한 범주로 나뉩니다. 비즈니스 영향 요소와 마이그레이션 목표에 따라 채택하는 전략이 달라집니다. 여러 전략을 채택할 수도 있습니다. 예를 들어, 간단한 앱이나 비즈니스에 중요하지 않은 앱은 다시 호스트(리프트 앤 시프트)하고 더 복잡한, 중요 비즈니스용 앱은 아키텍트를 변경하도록 선택할 수 있습니다. 그러면 전략을 살펴보겠습니다.


**전략** | **정의** | **사용하는 경우** 
--- | --- | --- 
**다시 호스트** | “리프트 앤 시프트” 마이그레이션이라고도 합니다. 이 옵션을 사용하면 코드 변경이 필요하지 않으므로 기존 앱을 Azure로 빠르게 마이그레이션할 수 있습니다. 각 앱이 현재 상태대로 마이그레이션되므로 코드 변경과 관련된 위험이나 비용 없이 클라우드의 혜택을 얻게 됩니다. | 앱을 빠르게 클라우드로 이동해야 하는 경우.<br/><br/> 앱을 수정하지 않고 이동하려는 경우.<br/><br/> 마이그레이션 후 [Azure IaaS](https://azure.microsoft.com/overview/what-is-iaas/) 확장성을 활용할 수 있도록 앱 아키텍처가 구성된 경우.<br/><br/> 앱이 비즈니스에 중요하지만 앱 기능을 즉시 변경할 필요가 없는 경우.
**리팩터링** | “다시 패키지”라고도 하는 리팩터링은 [Azure PaaS](https://azure.microsoft.com/overview/what-is-paas/)에 연결하여 클라우드 제공을 사용할 수 있도록 하려면 앱에 대한 최소한의 변경이 필요합니다.<br/><br/> 예를 들어, 기존 앱을 Azure App Service 또는 AKS(Azure Kubernetes Service)로 마이그레이션할 수 있습니다.<br/><br/> 또는 관계형 및 비관계형 데이터베이스를 Azure SQL Database 관리되는 인스턴스, Azure Database for MySQL, Azure Database for PostgreSQL, Azure Cosmos DB 등의 옵션으로 리팩터링할 수도 있습니다. | 앱을 Azure에서 작동하도록 쉽게 다시 패키지할 수 있는 경우.<br/><br/> Azure에서 제공하는 혁신적인 DevOps 방법을 적용하려는 경우 또는 워크로드에 대한 컨테이너 전략을 사용하여 DevOps를 고려하는 경우.<br/><br/> 리팩터링의 경우, 기존 코드 베이스의 이식성과 사용 가능한 개발 기술을 고려해야 합니다.
**아키텍처 변경** | 마이그레이션 재설계는 앱 기능과 코드베이스를 수정하고 확장하여 클라우드 확장성을 위한 앱 아키텍처를 최적화하는 데 집중합니다.<br/><br/> 예를 들어, 모놀리식 응용 프로그램을 함께 작동하고 쉽게 확장되는 마이크로 서비스 그룹으로 나눌 수 있습니다.<br/><br/> 또는 관계형 및 비관계형 데이터베이스를 완전히 관리되는 DBaaS 솔루션(예: Azure SQL Database Managed Instance, Azure Database for MySQL, Azure Database for PostgreSQL 및 Azure Cosmos DB)으로 재설계할 수 있습니다. | 새 기능을 통합하거나 클라우드 플랫폼에서 효율적으로 작동하도록 앱을 전반적으로 수정해야 하는 경우.<br/><br/> 기존 응용 프로그램 투자를 사용하고, 확장성 요구 사항을 충족하고, 혁신적인 Azure DevOps 방법을 적용하고, 가상 머신 사용을 최소화하려는 경우.
**다시 빌드** | 다시 빌드는 Azure 클라우드 기술을 사용하여 처음부터 앱을 다시 빌드하는 방식으로 작업을 수행합니다.<br/><br/> 예를 들어 Azure Functions, Azure AI, Azure SQL Database Managed Instance, Azure Cosmos DB 등의 클라우드 원시 기술을 사용하여 개발 가능한 앱을 빌드할 수 있습니다. | 신속한 개발을 원하며, 기존 앱의 기능과 수명이 제한적인 경우.<br/><br/> 비즈니스 혁신(Azure에서 제공하는 DevOps 사례 포함)을 신속하게 처리하고, 클라우드 원시 기술을 사용하여 새 응용 프로그램을 빌드하고, AI, 블록체인 및 IoT의 향상된 기능을 활용할 준비가 된 경우

## <a name="migration-articles"></a>마이그레이션 문서

시리즈의 문서는 아래 표에 요약되어 있습니다.  

- 각 마이그레이션 시나리오는 마이그레이션 전략을 결정하는 약간씩 다른 비즈니스 목표를 기반으로 합니다.
- 배포 시나리오마다 비즈니스 영향 요소와 목표, 제안된 아키텍처, 마이그레이션을 수행하는 단계, 마이그레이션이 완료된 후의 정리 및 다음 단계에 대한 권장 사항 정보를 제공합니다.

**문서** | **세부 정보** | **상태**
--- | --- | ---
문서 1: 개요 | Contoso 마이그레이션 전략, 문서 시리즈 및 사용할 샘플 앱에 대해 간략히 설명합니다. | 이 문서의 내용:
[문서 2: Azure 인프라 배포](contoso-migration-infrastructure.md) | Contoso에서 마이그레이션을 위해 온-프레미스 및 Azure 인프라를 준비하는 방법에 대해 설명합니다. 모든 마이그레이션 문서에 동일한 인프라가 사용됩니다. | 사용 가능
[문서 3: Azure로 마이그레이션할 온-프레미스 리소스 평가](contoso-migration-assessment.md)  | Contoso가 VMware에서 실행되는 온-프레미스 2계층 SmartHotel 앱 평가를 실행하는 방법을 보여 줍니다. Contoso에서 [Azure Migrate](migrate-overview.md) 서비스를 사용하여 앱 VM을 평가하고, Database Migration Assistant를 사용하여 앱 SQL Server 데이터베이스를 평가합니다. | 사용 가능
[문서 4: 앱을 Azure VM 및 SQL Managed Instance에 다시 호스트](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso가 온-프레미스 SmartHotel 앱에 대해 Azure로의 리프트 앤 시프트 마이그레이션을 실행하는 방법을 보여줍니다. Contoso에서 Azure Site Recovery를 사용하여 앱 프런트 엔드 VM을 마이그레이션하고, Azure Database Migration Service를 사용하여 앱 데이터베이스를 SQL Managed Instance로 마이그레이션합니다. | 사용 가능
[문서 5: 앱을 Azure VM에 다시 호스트](contoso-migration-rehost-vm.md) | Contoso에서 Azure Site Recovery 서비스를 사용하여 SmartHotel 앱 VM을 Azure VM으로 마이그레이션하는 방법을 보여 줍니다. | 사용 가능
[문서 6: 앱을 Azure VM 및 SQL Server Always On 가용성 그룹에 다시 호스트](contoso-migration-rehost-vm-sql-ag.md) | Contoso에서 Azure Site Recovery를 사용하여 SmartHotel 앱을 마이그레이션하여 앱 VM을 마이그레이션하는 방법 및 Azure Database Migration Service를 사용하여 앱 데이터베이스를 AlwaysOn 가용성 그룹에서 보호되는 SQL Server 클러스터로 마이그레이션하는 방법을 보여 줍니다. | 사용 가능
[문서 7: Azure VM에서 Linux 앱 다시 호스트](contoso-migration-rehost-linux-vm.md) | Contoso에서 Azure Site Recovery를 사용하여 Linux osTicket 앱을 Azure VM으로 리프트 앤 시프트 방식으로 마이그레이션하는 방법을 보여 줍니다. | 사용 가능
[문서 8: Azure VM 및 Azure MySQL에서 Linux 앱 다시 호스트](contoso-migration-rehost-linux-vm-mysql.md) | Contoso에서 Azure Site Recovery를 사용하여 Linux osTicket 앱을 Azure VM으로 마이그레이션하고, MySQL Workbench를 사용하여 앱 데이터베이스를 Azure MySQL 서버 인스턴스로 마이그레이션하는 방법을 보여 줍니다. | 사용 가능
[문서 9: Azure Web Apps 및 Azure SQL 데이터베이스에서 앱 리팩터링](contoso-migration-refactor-web-app-sql.md) | Contoso에서 SmartHotel 앱을 Azure Web App으로 마이그레이션하고, 앱 데이터베이스를 Azure SQL Server 인스턴스로 마이그레이션하는 방법을 보여 줍니다. | 사용 가능
[문서 10: Azure Web Apps 및 Azure MySQL에서 Linux 앱 리팩터링](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso에서 Linux osTicket 앱을 지속적인 업데이트를 위해 GitHub와 통합된 여러 사이트의 Azure Web App으로 마이그레이션하는 방법을 보여 줍니다. 앱 데이터베이스를 Azure MySQL 서버 인스턴스로 마이그레이션합니다. | 사용 가능
[문서 11: VSTS에서 TFS 리팩터링](contoso-migration-tfs-vsts.md) | Contoso가 해당 온-프레미스 TFS(Team Foundation Server) 배포를 Azure에서 VSTS(Visual Studio Team Services)로 마이그레이션하는 방법을 보여줍니다. | 사용 가능
[문서 12: Azure 컨테이너 및 Azure SQL Database에서 앱 재설계](contoso-migration-rearchitect-container-sql.md) | Contoso가 SmartHotel 앱을 Azure로 마이그레이션하고 아키텍처를 변경하는 방법을 보여줍니다. 웹앱 계층을 Windows 컨테이너 및 Azure SQL Database의 앱 데이터베이스로 아키텍처를 변경합니다. | 사용 가능
[문서 13: Azure에서 앱 다시 빌드](contoso-migration-rebuild.md) | Contoso에서 다양한 Azure 기능과 서비스(App Services, Azure Kubernetes, Azure Functions, Cognitive Services 및 Cosmos DB 포함)를 사용하여 SmartHotel 앱을 다시 빌드하는 방법을 보여 줍니다. | 사용 가능






### <a name="demo-apps"></a>데모 앱

문서에서는 두 개의 데모 앱인 SmartHotel과 osTicket을 사용합니다.

- **SmartHotel360**: 이 앱은 Azure로 작업하는 경우, 사용할 수 있는 테스트 앱으로 Microsoft에서 개발했습니다. 오픈 소스로 제공되므로 [GitHub](https://github.com/Microsoft/SmartHotel360)에서 다운로드할 수 있습니다. SQL Server 데이터베이스에 연결된 ASP.NET 앱입니다. 현재 이 앱은 Windows Server 2008 R2와 SQL Server 2008 R2를 실행하는 두 개의 VMware VM에 있습니다. 앱 VM은 온-프레미스로 호스트되며 vCenter Server를 통해 관리됩니다.
- **osTicket**: Linux에서 실행되는 오픈 소스 서비스 데스크 발권 앱입니다. [GitHub](https://github.com/osTicket/osTicket)에서 다운로드할 수 있습니다. 현재 이 앱은 Apache 2, PHP 7.0 및 MySQL 5.7을 사용하여 Ubuntu 16.04 LTS를 실행하는 두 개의 VMware VM에 있습니다.
    

## <a name="next-steps"></a>다음 단계

Contoso가 마이그레이션을 준비하도록 온-프레미스 및 Azure 인프라를 설정하는 [방법을 알아보세요](contoso-migration-infrastructure.md).



