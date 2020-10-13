---
title: Metody ověřování agenta zabezpečení
description: Přečtěte si o různých metodách ověřování, které jsou k dispozici při používání programu Defender pro službu IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: 896baa70fb6e18165a025459a063e33d705261ba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90936267"
---
# <a name="security-agent-authentication-methods"></a>Metody ověřování agenta zabezpečení

V tomto článku se dozvíte o různých metodách ověřování, které můžete použít s agentem AzureIoTSecurity k ověřování pomocí IoT Hub.

Pro každé zařízení, které je připojené k programu Defender pro IoT v IoT Hub, se vyžaduje modul zabezpečení. K ověření zařízení může Defender pro IoT používat jednu ze dvou metod. Vyberte metodu, která funguje nejlépe pro vaše stávající řešení IoT.

> [!div class="checklist"]
> * SecurityModule – možnost
> * Možnost zařízení

## <a name="authentication-methods"></a>Metody ověřování

Tyto dvě metody pro agenta AzureIoTSecurity provádějí ověřování:

- Režim ověřování **SecurityModule**<br>
Agent se ověřuje pomocí identity modulu zabezpečení nezávisle na identitě zařízení.
Tento typ ověřování použijte v případě, že chcete, aby Agent zabezpečení používal vyhrazenou metodu ověřování prostřednictvím modulu zabezpečení (jenom symetrický klíč).

- Režim ověřování **zařízení**<br>
V této metodě se Agent zabezpečení nejprve ověřuje pomocí identity zařízení. Po počátečním ověřování provede agent Defender for IoT pro IoT Hub volání **REST** pomocí REST API s ověřovacími daty daného zařízení. Agent Defender pro IoT pak požádá o metodu ověřování a data modulu zabezpečení z IoT Hub. V posledním kroku provede Defender pro agenta IoT ověřování pomocí modulu Defender for IoT.

Tento typ ověřování použijte v případě, že chcete, aby Agent zabezpečení znovu použil existující metodu ověřování zařízení (certifikát podepsaný svým držitelem nebo symetrický klíč).

Informace o tom, jak nakonfigurovat, najdete v tématu [parametry instalace agenta zabezpečení](#security-agent-installation-parameters) .

## <a name="authentication-methods-known-limitations"></a>Známá omezení metod ověřování

- Režim ověřování **SecurityModule** podporuje pouze ověřování symetrického klíče.
- CA-Signed certifikát není podporován režimem ověřování **zařízení** .

## <a name="security-agent-installation-parameters"></a>Parametry instalace agenta zabezpečení

Při [nasazování agenta zabezpečení](how-to-deploy-agent.md)musí být jako argumenty zadány podrobnosti ověřování.
Tyto argumenty jsou popsány v následující tabulce.

|Název parametru pro Linux | Název parametru Windows | Parametr zkrácený |Description|Možnosti|
|---------------------|---------------|---------|---------------|---------------|
|ověřování-identita|AuthenticationIdentity|aui|Identita ověřování| **SecurityModule** nebo **zařízení**|
|ověřování – metoda|Parametr|aum|Metoda ověřování|**SymmetricKey** nebo **SelfSignedCertificate**|
|Cesta k souboru|FilePath|f|Absolutní úplná cesta k souboru obsahujícímu certifikát nebo symetrický klíč| |
|název hostitele|Název hostitele|HN|Plně kvalifikovaný název domény IoT Hub|Příklad: ContosoIotHub.azure-devices.net|
|ID zařízení|DeviceId|dži|ID zařízení|Příklad: MyDevice1|
|certifikát – druh umístění|CertificateLocationKind|CL|Umístění úložiště certifikátů|**Místní_soubor** nebo **Store**|
|

Při použití skriptu install Security Agent se provede následující konfigurace automaticky. Chcete-li upravit ověřování agenta zabezpečení ručně, upravte konfigurační soubor.

## <a name="change-authentication-method-after-deployment"></a>Změna metody ověřování po nasazení

Při nasazování agenta zabezpečení pomocí instalačního skriptu je automaticky vytvořen konfigurační soubor.

Chcete-li po nasazení změnit metody ověřování, je nutné ručně upravit konfigurační soubor.

### <a name="c-based-security-agent"></a>Agent zabezpečení založený na jazyce C#

Upravte _Authentication.config_ s následujícími parametry:

```xml
<Authentication>
  <add key="deviceId" value=""/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value=""/>
  <add key="type" value=""/>
  <add key="identity" value=""/>
  <add key="certificateLocationKind" value="" />
</Authentication>
```

### <a name="c-based-security-agent"></a>Agent zabezpečení založený na jazyce C

Upravte _LocalConfiguration.js_ s následujícími parametry:

```json
"Authentication" : {
    "Identity" : "",
    "AuthenticationMethod" : "",
    "FilePath" : "",
    "DeviceId" : "",
    "HostName" : ""
}
```

## <a name="see-also"></a>Viz také

- [Přehled agentů zabezpečení](security-agent-architecture.md)
- [Nasadit agenta zabezpečení](how-to-deploy-agent.md)
- [Přístup k nezpracovaným datům zabezpečení](how-to-security-data-access.md)
