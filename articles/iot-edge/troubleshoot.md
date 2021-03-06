---
title: Azure IoT Edge 문제 해결 | Microsoft Docs
description: 일반적인 문제를 해결하고, Azure IoT Edge에 대한 문제 해결 기술을 배웁니다.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/26/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a6102a6bc28486c24134bbc172b9e8a7e1a61244
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39308040"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Azure IoT Edge에 대한 일반적인 문제 및 해결 방법

작업 환경에서 Azure IoT Edge를 실행하는 동안 문제가 발생할 경우 이 문서에서 문제 해결 방법을 참조하세요. 

## <a name="standard-diagnostic-steps"></a>표준 진단 단계 

문제가 발생하는 경우 장치로 전달되거나 장치로부터 전달되는 메시지 및 컨테이너 로그를 검토하여 IoT Edge 장치의 상태를 자세히 알아봅니다. 이 섹션의 명령 및 도구를 사용하여 정보를 수집합니다. 

### <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>IoT Edge 보안 관리자의 상태 및 해당 로그를 확인합니다.

Linux에서:
- IoT Edge 보안 관리자의 상태를 보려면:

   ```bash
   sudo systemctl status iotedge
   ```

- IoT Edge 보안 관리자의 로그를 보려면:

    ```bash
    sudo journalctl -u iotedge -f
    ```

- IoT Edge 보안 관리자의 더욱 자세한 로그를 보려면:

   - iotedge 디먼 설정을 편집합니다.

      ```bash
      sudo systemctl edit iotedge.service
      ```
   
   - 다음 줄을 업데이트합니다.
    
      ```
      [Service]
      Environment=IOTEDGE_LOG=edgelet=debug
      ```
    
   - IoT Edge 보안 디먼을 다시 시작합니다.
    
      ```bash
      sudo systemctl cat iotedge.service
      sudo systemctl daemon-reload
      sudo systemctl restart iotedge
      ```

Windows에서:
- IoT Edge 보안 관리자의 상태를 보려면:

   ```powershell
   Get-Service iotedge
   ```

- IoT Edge 보안 관리자의 로그를 보려면:

   ```powershell
   # Displays logs from today, newest at the bottom.
 
   Get-WinEvent -ea SilentlyContinue `
   -FilterHashtable @{ProviderName= "iotedged";
     LogName = "application"; StartTime = [datetime]::Today} |
   select TimeCreated, Message |
   sort-object @{Expression="TimeCreated";Descending=$false} |
   format-table -autosize -wrap
   ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>IoT Edge 보안 관리자가 실행되지 않는 경우 yaml 구성 파일을 확인합니다.

> [!WARNING]
> YAML 파일은 들여쓰기로 탭을 포함할 수 없습니다. 2 공백을 대신 사용합니다.

Linux에서:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Windows에서:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="check-container-logs-for-issues"></a>문제에 대한 컨테이너 로그 확인

IoT Edge 보안 디먼이 실행되면 컨테이너의 로그를 확인하여 문제를 검색합니다. 배포된 컨테이너부터 살펴본 후, IoT Edge 런타임을 구성하는 컨테이너(Edge Agent 및 Edge Hub)를 살펴봅니다. Edge Agent 로그는 일반적으로 각 컨테이너의 수명 주기에 대한 정보를 제공합니다. Edge Hub는 메시징 및 라우팅에 대한 정보를 제공합니다. 

   ```cmd
   iotedge logs <container name>
   ```

### <a name="view-the-messages-going-through-the-edge-hub"></a>Edge 허브를 따라 메시지 확인

Edge Hub에 표시되는 메시지를 확인하여, edgeAgent 및 edgeHub 런타임 컨테이너에서 제공되는 자세한 로그를 통해 장치 속성 업데이트에 대한 정보를 수집합니다. 이러한 컨테이너에서 자세한 로그를 켜려면 yaml 구성 파일에서 `RuntimeLogLevel`을 설정합니다. 파일을 열려면:

Linux에서:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Windows에서:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

기본적으로 `agent` 요소는 아래와 같은 모양입니다.

   ```yaml
   agent:
     name: edgeAgent
     type: docker
     env: {}
     config:
       image: mcr.microsoft.com/azureiotedge-agent:1.0
       auth: {}
   ```

`env: {}`을 다음으로 바꿉니다.

> [!WARNING]
> YAML 파일은 들여쓰기로 탭을 포함할 수 없습니다. 2 공백을 대신 사용합니다.

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

파일을 저장하고 IoT Edge 보안 관리자를 다시 시작합니다.

IoT Hub 및 IoT Edge 장치 간에 전송되는 메시지를 확인할 수도 있습니다. Visual Studio Code용 [Azure IoT 도구 키트](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) 확장을 사용하여 이러한 메시지를 확인합니다. 자세한 지침은 [Handy tool when you develop with Azure IoT](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/)(Azure IoT로 개발할 때 사용할 수 있는 편리한 도구)를 참조하세요.

### <a name="restart-containers"></a>컨테이너 다시 시작
로그 및 메시지에서 정보를 검토한 후에는 컨테이너를 다시 시작할 수 있습니다.

```
iotedge restart <container name>
```

IoT Edge 런타임 컨테이너를 다시 시작합니다.

```
iotedge restart edgeAgent && iotedge restart edgeHub
```

### <a name="restart-the-iot-edge-security-manager"></a>IoT Edge 보안 관리자를 다시 시작합니다.

문제가 여전히 지속되는 경우 IoT Edge 보안 관리자를 다시 시작할 수 있습니다.

Linux에서:

   ```cmd
   sudo systemctl restart iotedge
   ```

Windows에서:

   ```powershell
   Stop-Service iotedge -NoWait
   sleep 5
   Start-Service iotedge
   ```

## <a name="edge-agent-stops-after-about-a-minute"></a>Edge Agent는 약 1분 후에 중지됩니다.

Edge Agent는 시작되고 약 1분 동안 실행되다가 중지됩니다. 로그에는 Edge Agent가 AMQP를 통해 IoT Hub에 연결하려고 한 다음, 약 30초 후에 WebSocket을 통한 AMQP를 사용하여 연결을 시도한다고 표시됩니다. 실패할 경우 Edge Agent가 종료됩니다. 

예제 Edge Agent 로그:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent. 
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5) 
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP... 
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket... 
```

### <a name="root-cause"></a>근본 원인
호스트 네트워크의 네트워킹 구성 때문에 Edge Agent가 네트워크에 연결하지 못합니다. 에이전트는 AMQP(포트 5671)를 통해 먼저 연결을 시도합니다. 이 작업이 실패하면 WebSocket(포트 443)을 시도합니다.

IoT Edge 런타임은 각 모듈이 통신할 네트워크를 설정합니다. Linux에서 이 네트워크는 브리지 네트워크입니다. Windows에서는 NAT를 사용합니다. 이 문제는 NAT 네트워크를 사용하는 Windows 컨테이너를 사용하는 Windows 장치에서 좀 더 자주 발생합니다. 

### <a name="resolution"></a>해결 방법
이 브리지/NAT 네트워크에 할당된 IP 주소가 인터넷에 연결되는 경로가 있는지 확인합니다. 경우에 따라 호스트의 VPN 구성은 IoT Edge 네트워크를 재정의합니다. 

## <a name="edge-hub-fails-to-start"></a>Edge Hub가 시작되지 못합니다.

Edge Hub가 시작되지 못하고 로그에 다음 메시지가 출력됩니다. 

```output
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n) 
```

### <a name="root-cause"></a>근본 원인
호스트 컴퓨터의 일부 다른 프로세스가 포트 443에 바인딩되어 있습니다. Edge Hub는 게이트웨이 시나리오에서 사용하기 위해 포트 5671 및 443을 매핑합니다. 다른 프로세스가 이미 포트에 바인딩되어 있으면 이 포트 매핑은 실패합니다. 

### <a name="resolution"></a>해결 방법
포트 443을 사용하는 프로세스를 찾아 중지합니다. 이 프로세스는 일반적으로 웹 서버입니다.

## <a name="edge-agent-cant-access-a-modules-image-403"></a>Edge Agent에서 모듈의 이미지에 액세스할 수 없습니다(403).
컨테이너가 실행되지 못하고 Edge Agent 로그에 403 오류가 표시됩니다. 

### <a name="root-cause"></a>근본 원인
Edge Agent가 모듈의 이미지에 액세스할 수 있는 권한이 없습니다. 

### <a name="resolution"></a>해결 방법
레지스트리 자격 증명이 배포 매니페스트에서 제대로 지정되었는지 확인합니다.

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>IoT Edge 보안 디먼이 유효하지 않은 호스트 이름으로 실패합니다.

`sudo journalctl -u iotedge` 명령이 실패하고 다음 메시지를 출력합니다. 

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

### <a name="root-cause"></a>근본 원인
IoT Edge 런타임은 64자 미만인 호스트 이름만을 지원할 수 있습니다. 일반적으로 실제 컴퓨터에서는 문제가 되지 않지만 가상 머신에서 런타임을 설정할 때 문제가 발생할 수 있습니다. 특히 Azure에서 호스팅되는 Windows 가상 머신에 자동으로 생성된 호스트 이름이 너무 긴 경향이 있습니다. 

### <a name="resolution"></a>해결 방법
이 오류를 표시하는 경우 가상 머신의 DNS 이름을 구성한 다음, 설정 명령에서 DNS 이름을 호스트 이름으로 설정하여 해결할 수 있습니다.

1. Azure Portal에서 가상 머신의 개요 페이지로 이동합니다. 
2. DNS 이름에서 **구성**을 선택합니다. 가상 머신에 DNS 이름이 이미 구성되어 있으면 새 이름을 구성할 필요가 없습니다. 

   ![DNS 이름 구성](./media/troubleshoot/configure-dns.png)

3. **DNS 이름 레이블**에 대한 값을 입력하고 **저장**을 선택합니다.
4. 새 DNS 이름을 복사합니다.이 형식은 **\<DNSnamelabel\>.\<vmlocation\>.cloudapp.azure.com**이어야 합니다.
5. 가상 머신 내에서 다음 명령을 사용하여 DNS 이름으로 IoT Edge 런타임을 설정합니다.

   - Linux에서:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   - Windows에서:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="stability-issues-on-resource-constrained-devices"></a>리소스가 제한된 장치의 안정성 문제 
Raspberry Pi와 같이 제한된 장치를 사용하는 경우, 특히 이 장치를 게이트웨이로 사용하는 경우에는 안정성 문제가 발생할 수 있습니다. 증상에는 에지 허브 모듈의 메모리 부족 예외가 있으며, 다운스트림 장치를 연결할 수 없거나 몇 시간 후 장치가 원격 분석 메시지를 보내지 않습니다.

### <a name="root-cause"></a>근본 원인
에지 런타임에 속하는 에지 허브는 기본적으로 성능에 최적화되어 있으며 많은 양의 메모리를 할당하려고 합니다. 이런 상황이 제한된 에지 장치에는 이상적이지 않으며 안정성 문제를 유발할 수 있습니다.

### <a name="resolution"></a>해결 방법
에지 허브에 대해 환경 변수 **OptimizeForPerformance**를 **false**로 설정합니다. 이 작업을 수행하는 방법에는 다음 두 가지가 있습니다.

UI의 경우: 

포털의 *장치 세부 정보*->*모듈 설정*->*고급 Edge 런타임 설정 구성*에서 *에지 허브*에 대해 *false*로 설정된 *OptimizeForPerformance*라는 환경 변수를 만듭니다.

![optimizeforperformance][img-optimize-for-perf]

**또는**

배포 매니페스트의 경우:

```json
  "edgeHub": {
    "type": "docker",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
      "createOptions": <snipped>
    },
    "env": {
      "OptimizeForPerformance": {
          "value": "false"
      }
    },
```
## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>Windows에서 IoT Edge 디먼 로그를 가져올 수 없습니다.
Windows에서 `Get-WinEvent`를 사용할 때 EventLogException을 가져오면 레지스트리 항목을 확인합니다.

### <a name="root-cause"></a>근본 원인
`Get-WinEvent` PowerShell 명령은 레지스트리 항목을 사용하여 특정 `ProviderName` 별로 로그를 찾습니다.

### <a name="resolution"></a>해결 방법
IoT Edge 디먼에 대한 레지스트리 항목을 설정합니다. 다음 콘텐츠를 사용하여 **iotedge.reg** 파일을 만들고, 파일을 두 번 클릭하거나 `reg import iotedge.reg` 명령을 사용하여 Windows 레지스트리로 가져옵니다.

```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```


## <a name="next-steps"></a>다음 단계
IoT Edge 플랫폼에서 버그를 찾았나요? 지속적인 제품 개선을 위해 [문제를 제출](https://github.com/Azure/iotedge/issues)하세요. 

<!-- Images -->
[img-optimize-for-perf]: ./media/troubleshoot/OptimizeForPerformanceFalse.png
