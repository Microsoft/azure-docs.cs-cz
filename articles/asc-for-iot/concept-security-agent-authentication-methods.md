---
title: Metody ověřování pro Azure Security Center pro IoT| Dokumenty společnosti Microsoft
description: Přečtěte si o různých metodách ověřování, které jsou k dispozici při používání služby Azure Security Center for IoT.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68596465"
---
# <a name="security-agent-authentication-methods"></a>Metody ověřování agenta zabezpečení 

Tento článek vysvětluje různé metody ověřování, které můžete použít s agentem AzureIoTSecurity k ověření pomocí služby IoT Hub.

Pro každé zařízení na palubě centra zabezpečení Azure pro IoT v centru IoT Hub je vyžadován modul zabezpečení. K ověření zařízení, Azure Security Center pro IoT můžete použít jednu ze dvou metod. Vyberte si metodu, která nejlépe vyhovuje vašemu stávajícímu řešení IoT. 

> [!div class="checklist"]
> * Bezpečnostní modul, volba
> * Možnost zařízení

## <a name="authentication-methods"></a>Metody ověřování

Dvě metody pro agenta AzureIoTSecurity k provedení ověřování:

 - Režim ověřování **modulu SecurityModule**<br>
   Agent je ověřen pomocí identity modulu zabezpečení nezávisle na identitě zařízení.
   Tento typ ověřování použijte, pokud chcete, aby agent zabezpečení používal vyhrazenou metodu ověřování prostřednictvím modulu zabezpečení (pouze symetrický klíč).
        
 - **Režim** ověřování zařízení<br>
    V této metodě agent zabezpečení nejprve ověří s identitou zařízení. Po počátečním ověření provede Azure Security Center pro agenta IoT volání **rest** do služby IoT Hub pomocí rozhraní REST API s ověřovacími daty zařízení. Azure Security Center pro agenta IoT pak požaduje metodu ověřování modulu zabezpečení a data z centra IoT Hub. V posledním kroku Azure Security Center pro ioT agent provede ověřování proti Azure Security Center pro IoT modul.
    
    Tento typ ověřování použijte, pokud chcete, aby agent zabezpečení znovu použil existující metodu ověřování zařízení (certifikát podepsaný svým držitelem nebo symetrický klíč). 

Informace o konfiguraci najdete v [tématu Parametry instalace agenta zabezpečení.](#security-agent-installation-parameters)
                                
## <a name="authentication-methods-known-limitations"></a>Známá omezení o metodách ověřování

- Režim ověřování **modulu SecurityModule** podporuje pouze symetrické ověřování pomocí klíče.
- Režim ověřování zařízení nepodporuje certifikát podepsaný certifikační **autoritou.**  

## <a name="security-agent-installation-parameters"></a>Parametry instalace agenta zabezpečení

[Při nasazování agenta zabezpečení](how-to-deploy-agent.md)musí být jako argumenty poskytnuty podrobnosti ověřování.
Tyto argumenty jsou popsány v následující tabulce.


|Název parametru Linuxu | Název parametru systému Windows | Parametr zkrácené |Popis|Možnosti|
|---------------------|---------------|---------|---------------|---------------|
|identita ověřování|Identita ověřování identity|aui|Ověřovací identita| **Bezpečnostní modul** nebo **zařízení**|
|metoda ověřování|Metoda ověřování|Aum|Metoda ověřování|**SymmetricKey** nebo **SelfSignedCertificateCertificate**|
|cesta k souboru|Filepath|f|Absolutní úplná cesta pro soubor obsahující certifikát nebo symetrický klíč| |
|název hostitele|HostName|Hn|Hlavní qdn centra IoT Hub|Příklad: ContosoIotHub.azure-devices.net|
|id zařízení|DeviceId|Di|ID zařízení|Příklad: MyDevice1|
|druh umístění certifikátu|Nastavíse certifikátKind|Cl|Umístění úložiště certifikátu|**Místní soubor** nebo **úložiště**|
|


Při použití skriptu agenta zabezpečení instalace se automaticky provede následující konfigurace. Chcete-li upravit ověřování agenta zabezpečení ručně, upravte konfigurační soubor. 

## <a name="change-authentication-method-after-deployment"></a>Změna metody ověřování po nasazení

Při nasazování agenta zabezpečení pomocí instalačního skriptu se automaticky vytvoří konfigurační soubor.

Chcete-li změnit metody ověřování po nasazení, je nutné ruční úpravy konfiguračního souboru.


### <a name="c-based-security-agent"></a>Agent zabezpečení založený na c#

Upravit _soubor Authentication.config_ s následujícími parametry:

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

### <a name="c-based-security-agent"></a>Agent zabezpečení na bázi C

Upravte _soubor LocalConfiguration.json_ s následujícími parametry:

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
- [Přehled bezpečnostních agentů](security-agent-architecture.md)
- [Nasazení agenta zabezpečení](how-to-deploy-agent.md)
- [Přístup k nezpracovaným datům zabezpečení](how-to-security-data-access.md)
