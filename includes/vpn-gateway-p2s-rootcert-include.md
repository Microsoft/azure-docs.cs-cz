---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/11/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4c8e7e5272f180c482ca7fdd44302f49eb888b25
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "67174867"
---
엔터프라이즈 솔루션을 사용하여 생성된 루트 인증서를 사용하거나(추천) 자체 서명된 인증서를 생성합니다. 루트 인증서가 만들어지면 공용 인증서 데이터(프라이빗 키 아님)를 Base64 인코딩된 X.509 .cer 파일로 내보냅니다. 그런 다음, 공용 인증서 데이터를 Azure 서버에 업로드합니다.

* **엔터프라이즈 인증서:** 엔터프라이즈 솔루션을 사용 하는 경우 기존 인증서 체인을 사용할 수 있습니다. 사용하려는 루트 인증서용 .cer 파일을 획득합니다.
* **자체 서명 된 루트 인증서:** 엔터프라이즈 인증서 솔루션을 사용 하지 않는 경우 자체 서명 된 루트 인증서를 만듭니다. 그렇지 않으면 만든 인증서가 P2S 연결과 호환되지 않으며, 연결하려고 할 때 클라이언트에 연결 오류 메시지가 표시됩니다. Microsoft Azure PowerShell, MakeCert, 또는 OpenSSL을 사용할 수 있습니다. 다음 문서의 단계에서는 호환되는 자체 서명된 루트 인증서를 생성하는 방법을 설명합니다.

  * [Windows 10 PowerShell 지침](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site.md): Windows 10 및 PowerShell에서 인증서를 생성해야 합니다. 루트 인증서에서 생성된 클라이언트 인증서는 지원되는 모든 P2S 클라이언트에 설치할 수 있습니다.
  * [MakeCert 지침](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-makecert.md): 사용할 Windows 10 컴퓨터에 액세스할 수 없는 경우 MakeCert를 사용하여 인증서를 생성합니다. MakeCert는 더 이상 사용되지 않지만, 인증서를 생성하는 데에는 여전히 사용할 수 있습니다. 루트 인증서에서 생성한 클라이언트 인증서는 지원되는 모든 P2S 클라이언트에 설치할 수 있습니다.
  * [Linux 지침](../articles/vpn-gateway/vpn-gateway-certificates-point-to-site-linux.md)
