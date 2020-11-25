---
title: Řešení potíží se zabezpečením a řízením přístupu
description: Naučte se řešit problémy zabezpečení a řízení přístupu v Azure Data Factory.
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/19/2020
ms.author: lle
ms.reviewer: craigg
ms.openlocfilehash: 21a1523016502bd7b0a8682461f1fc16acda2ebb
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "96008717"
---
# <a name="troubleshoot-azure-data-factory-security-and-access-control-issues"></a>Řešení potíží s Azure Data Factory zabezpečení a řízení přístupu

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje běžné metody řešení potíží při zabezpečení a řízení přístupu v Azure Data Factory.

## <a name="common-errors-and-messages"></a>Běžné chyby a zprávy

### <a name="invalid-or-empty-authentication-key-issue-after-disabling-public-network-access"></a>Neplatný nebo prázdný problém s ověřovacím klíčem po zakázání přístupu k veřejné síti

#### <a name="symptoms"></a>Příznaky

Po zakázání přístupu k veřejné síti pro Data Factory vyvolá modul runtime Integration runtime chybu "ověřovací klíč je neplatný nebo prázdný".

#### <a name="cause"></a>Příčina

Problém je pravděpodobně způsoben problémem překladu názvů DNS, protože zakázání veřejného připojení a vytvoření privátního koncového bodu nemá k dispozici žádnou nápovědu pro opětovné připojení.

#### <a name="resolution"></a>Řešení

1. PsPing se plně kvalifikovaný název domény ADF a zjistilo se, že vyrovnávací paměť přestala veřejným koncovým bodem ADF, a to i po tom, co je zakázané.

1. Změňte soubor hostitele ve virtuálním počítači tak, aby mapoval privátní IP adresu na plně kvalifikovaný název domény, a znovu spusťte PsPing. Vyrovnávací paměť bude moct přejít na správnou privátní IP adresu ADF a pak.

1. Znovu zaregistrujte prostředí Integration runtime v místním prostředí a my ho najdete v provozu.
 

### <a name="unable-to-register-ir-authentication-key-on-self-hosted-vms-due-to-private-link"></a>Nepovedlo se zaregistrovat ověřovací klíč IR na virtuálních počítačích s místním hostováním z důvodu privátního propojení.

#### <a name="symptoms"></a>Příznaky

Nepovedlo se zaregistrovat ověřovací klíč IR na virtuálním počítači s místním hostováním, protože je povolené privátní propojení.

Informace o chybě jsou uvedené níže:

`
Failed to get service token from ADF service with key *************** and time cost is: 0.1250079 seconds, the error code is: InvalidGatewayKey, activityId is: XXXXXXX and detailed error message is Client IP address is not valid private ip Cause Data factory couldn’t access the public network thereby not able to reach out to the cloud to make the successful connection.
`

#### <a name="cause"></a>Příčina

Problém může být způsoben virtuálním počítačem, na kterém se SHIR pokouší nainstalovat. Přístup k veřejné síti by měl být povolen pro připojení ke cloudu.

#### <a name="resolution"></a>Řešení

 **Řešení 1:** Problém můžete vyřešit pomocí následujících kroků:

1. Přejděte na odkaz níže: 
    
    https://docs.microsoft.com/rest/api/datafactory/Factories/Update

1. Klikněte na volbu **vyzkoušet** a vyplňte všechny požadované podrobnosti. Do **těla** vložte i následující vlastnost:

    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ``` 

1. Spusťte funkci kliknutím na tlačítko **Spustit** na konci stránky. Ujistěte se, že byste získali kód odpovědi 200. Vlastnost, kterou jsme vložili, se zobrazí také v definici JSON.

1. Pak se můžete znovu pokusit přidat klíč pro ověřování IR do prostředí Integration runtime.


**Řešení 2:** K řešení můžete použít následující článek:

https://docs.microsoft.com/azure/data-factory/data-factory-private-link

Zkuste povolit přístup k veřejné síti pomocí uživatelského rozhraní.

![Povolit přístup k veřejné síti](media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access.png)

## <a name="next-steps"></a>Další kroky

Další pomoc při řešení potíží najdete v následujících zdrojích informací:

*  [Privátní odkaz pro Data Factory](data-factory-private-link.md)
*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Žádosti o Data Factory funkcí](https://feedback.azure.com/forums/270578-data-factory)
*  [Videa Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Stránka s otázkou Microsoft Q&](/answers/topics/azure-data-factory.html)
*  [Fórum přetečení zásobníku pro Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informace o Twitteru týkající se Data Factory](https://twitter.com/hashtag/DataFactory)