---
title: Metody ověřování pro Azure Security Center pro náhled IoT | Dokumentace Microsoftu
description: Další informace o různé metody ověřování k dispozici při použití Azure Security Center pro služby Azure IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 10b38f20-b755-48cc-8a88-69828c17a108
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 634d1aedfaf868766e3c1bf97373b9c310885835
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65198400"
---
# <a name="security-agent-authentication-methods"></a>Metody ověřování zabezpečení agenta 

> [!IMPORTANT]
> Azure Security Center pro IoT je aktuálně ve verzi public preview.
> Tato verze preview je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se používat pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tento článek vysvětluje různé metody ověřování, která vám pomůže s agentem AzureIoTSecurity k ověření pomocí služby IoT Hub.

U každého zařízení připojili k Azure Security Center (ASC) pro IoT ve službě IoT Hub se vyžaduje modul zabezpečení. K ověření zařízení, můžete použít ASC pro IoT jedním ze dvou způsobů. Zvolte metodu, která je nejvhodnější pro vaše stávající řešení IoT. 

> [!div class="checklist"]
> * Možnost zabezpečení modulu
> * Možnosti zařízení

## <a name="authentication-methods"></a>Metody ověřování

Dvě metody pro agenta AzureIoTSecurity provádět ověřování:

 - **Modul** režim ověřování<br>
   Modul je ověřený nezávisle na dvojče zařízení.
   Tento typ ověřování použijte, pokud chcete zabezpečení agenta pro použití s metodu ověřování vyhrazené modul zabezpečení (pouze symetrický klíč).
        
 - **Zařízení** režim ověřování<br>
    V této metodě nejprve agenta zabezpečení ověří s identitou zařízení. Po počáteční ověřování provádí ASC pro agenta IoT **REST** volání do IoT Hubu pomocí rozhraní REST API s ověřovacími údaji ze zařízení. ASC pro agenta IoT pak požadavky zabezpečení modulu ověřování a dat ze služby IoT Hub. V posledním kroku ASC IoT agent provádí ověření proti ASC pro modul IoT.
    
    Tento typ ověřování použijte, pokud chcete agenta zabezpečení znovu použít stávající metodu ověřování zařízení (s podpisem držitele certifikátu nebo symetrický klíč). 

Zobrazit [parametry instalace agenta zabezpečení](#security-agent-installation-parameters) na další informace o konfiguraci.
                                
## <a name="authentication-methods-known-limitations"></a>Metody ověřování známé omezení

- **Modul** režim ověřování podporuje jen ověřování symetrickým klíčem.
- Certifikát podepsaný certifikační Autoritou nepodporuje **zařízení** režim ověřování.  

## <a name="security-agent-installation-parameters"></a>Parametry instalace agenta zabezpečení

Když [nasazení agenta zabezpečení](how-to-deploy-agent.md), podrobnosti o ověřování musí být zadané jako argumenty.
Tyto argumenty jsou popsány v následující tabulce.


|Parametr|Popis|Možnosti|
|---------|---------------|---------------|
|**Identita**|Režim ověřování| **Modul** nebo **zařízení**|
|**type**|Typ ověřování|**SymmetricKey** nebo **SelfSignedCertificate**|
|**filePath**|Absolutní úplnou cestu k souboru, který obsahuje certifikát nebo symetrický klíč| |
|**gatewayHostname**|Plně kvalifikovaný název domény služby IoT Hub|Příklad: ContosoIotHub.azure-devices.net|
|**deviceId**|ID zařízení|Příklad: MyDevice1|
|**certificateLocationKind**|Umístění úložiště certifikátů|**LocalFile** nebo **Store**|


Při použití instalační skript agenta zabezpečení, automaticky provede následující konfiguraci.

Ověření zabezpečení agenta ručně upravit, upravte konfigurační soubor. 

## <a name="change-authentication-method-after-deployment"></a>Po nasazení změnit metodu ověřování

Při nasazování agenta zabezpečení pomocí instalačního skriptu, se automaticky vytvoří konfigurační soubor.

Chcete-li po nasazení změnit metody ověřování, je potřeba ruční úprava konfiguračního souboru.


### <a name="c-based-security-agent"></a>C#– na základě zabezpečení agenta

Upravit _Authentication.config_ s následujícími parametry:

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

### <a name="c-based-security-agent"></a>Zabezpečení na základě C agenta

Upravit _LocalConfiguration.json_ s následujícími parametry:

```json
"Authentication" : {
    "Identity" : "",
    "AuthenticationMethod" : "",
    "FilePath" : "",
    "DeviceId" : "",
    "HostName" : ""
}
```

## <a name="see-also"></a>Další informace najdete v tématech
- [Přehled zabezpečení agentů](security-agent-architecture.md)
- [Nasazení agenta pro zabezpečení](how-to-deploy-agent.md)
- [Přístup k datům raw zabezpečení](how-to-security-data-access.md)
