---
title: Metody ověřování pro Azure Security Center IoT | Microsoft Docs
description: Přečtěte si o různých metodách ověřování, které jsou k dispozici při použití Azure Security Center pro službu IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 10b38f20-b755-48cc-8a88-69828c17a108
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 16f7f91e02d118d9f9a295ebb79a6cd0187dd9fd
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596465"
---
# <a name="security-agent-authentication-methods"></a>Metody ověřování agenta zabezpečení 

V tomto článku se dozvíte o různých metodách ověřování, které můžete použít s agentem AzureIoTSecurity k ověřování pomocí IoT Hub.

Pro každé zařízení připojené k Azure Security Center pro IoT v IoT Hub se vyžaduje modul zabezpečení. Pro ověření zařízení Azure Security Center pro IoT může použít jednu ze dvou metod. Vyberte metodu, která funguje nejlépe pro vaše stávající řešení IoT. 

> [!div class="checklist"]
> * SecurityModule – možnost
> * Možnost zařízení

## <a name="authentication-methods"></a>Metody ověřování

Tyto dvě metody pro agenta AzureIoTSecurity provádějí ověřování:

 - Režim ověřování **SecurityModule**<br>
   Agent se ověřuje pomocí identity modulu zabezpečení nezávisle na identitě zařízení.
   Tento typ ověřování použijte v případě, že chcete, aby Agent zabezpečení používal vyhrazenou metodu ověřování prostřednictvím modulu zabezpečení (jenom symetrický klíč).
        
 - Režim ověřování **zařízení**<br>
    V této metodě se Agent zabezpečení nejprve ověřuje pomocí identity zařízení. Po počátečním ověřování provede Azure Security Center pro agenta IoT volání **REST** do IoT Hub pomocí REST API s ověřovacími daty daného zařízení. Azure Security Center agent IoT pak požádá o metodu ověřování modulu zabezpečení a data z IoT Hub. V posledním kroku provádí Azure Security Center pro agenta IoT ověřování pomocí modulu Azure Security Center for IoT.
    
    Tento typ ověřování použijte v případě, že chcete, aby Agent zabezpečení znovu použil existující metodu ověřování zařízení (certifikát podepsaný svým držitelem nebo symetrický klíč). 

Informace o tom, jak nakonfigurovat, najdete v tématu [parametry instalace agenta zabezpečení](#security-agent-installation-parameters) .
                                
## <a name="authentication-methods-known-limitations"></a>Známá omezení metod ověřování

- Režim ověřování **SecurityModule** podporuje pouze ověřování symetrického klíče.
- Režim ověřování **zařízení** nepodporuje certifikát podepsaný certifikační autoritou.  

## <a name="security-agent-installation-parameters"></a>Parametry instalace agenta zabezpečení

Při [nasazování agenta zabezpečení](how-to-deploy-agent.md)musí být jako argumenty zadány podrobnosti ověřování.
Tyto argumenty jsou popsány v následující tabulce.


|Název parametru pro Linux | Název parametru Windows | Parametr zkrácený |Popis|Možnosti|
|---------------------|---------------|---------|---------------|---------------|
|ověřování-identita|AuthenticationIdentity|aui|Identita ověřování| **SecurityModule** nebo **zařízení**|
|ověřování – metoda|Parametr|aum|Metoda ověřování|**SymmetricKey** nebo **SelfSignedCertificate**|
|Cesta k souboru|FilePath|f|Absolutní úplná cesta k souboru obsahujícímu certifikát nebo symetrický klíč| |
|název hostitele|název hostitele|HN|Plně kvalifikovaný název domény IoT Hub|Příklad: ContosoIotHub.azure-devices.net|
|ID zařízení|DeviceId|dži|ID zařízení|Příklad: MyDevice1|
|certifikát – druh umístění|CertificateLocationKind|cl|Umístění úložiště certifikátů|**Místní_soubor** nebo **Store**|
|


Při použití skriptu install Security Agent se provede následující konfigurace automaticky. Chcete-li upravit ověřování agenta zabezpečení ručně, upravte konfigurační soubor. 

## <a name="change-authentication-method-after-deployment"></a>Změna metody ověřování po nasazení

Při nasazování agenta zabezpečení pomocí instalačního skriptu je automaticky vytvořen konfigurační soubor.

Chcete-li po nasazení změnit metody ověřování, je nutné ručně upravit konfigurační soubor.


### <a name="c-based-security-agent"></a>C#Agent zabezpečení založený na bázi

Upravte soubor _Authentication. config_ následujícími parametry:

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

Upravte _LocalConfiguration. JSON_ s následujícími parametry:

```json
"Authentication" : {
    "Identity" : "",
    "AuthenticationMethod" : "",
    "FilePath" : "",
    "DeviceId" : "",
    "HostName" : ""
}
```

## <a name="see-also"></a>Viz také:
- [Přehled agentů zabezpečení](security-agent-architecture.md)
- [Nasadit agenta zabezpečení](how-to-deploy-agent.md)
- [Přístup k nezpracovaným datům zabezpečení](how-to-security-data-access.md)
