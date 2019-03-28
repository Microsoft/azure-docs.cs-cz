---
title: Jak pokynů a upravte ASC pro dvojče zařízení IoT v ASC pro náhled IoT | Dokumentace Microsoftu
description: Zjistěte, jak upravit ASC pro dvojče modulu zabezpečení IoT pro ASC pro IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 1bc5dc86-0f33-4625-b3d3-f9b6c1a54e14
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 4bb25db7feddea66e75d9dd069aaec7785faf738
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541944"
---
# <a name="modify-an-asc-for-iot-module-twin"></a>Upravit ASC pro dvojče zařízení IoT

> [!IMPORTANT]
> ASC pro IoT je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tento článek vysvětluje, jak změnit existující konfiguraci **dvojče modulu AzureIoTSecurity** pro ze stávajících zařízení. 

Zobrazit [vytvořit ASC pro modul IoT](quickstart-create-security-twin.md) se naučíte vytvořit nový modul zabezpečení pro nové zařízení.  

## <a name="modification-considerations"></a>Důležité informace o úpravách

> [!IMPORTANT]
> Všechny konfigurace v konfiguraci dvojčete přepíše výchozí konfiguraci. Pokud již není k dispozici v konfiguraci dvojčete specifickou konfiguraci, použije se výchozí konfiguraci. 

## <a name="configuration-schema-and-validation"></a>Schéma konfigurace a ověření 

Ujistěte se, že k ověření vaší konfigurace agenta před tímto [schématu](https://github.com/Azure/asc-for-iot/schema/security_module_twin). Agent se nespustí, pokud objekt konfigurace neodpovídá schématu.

 
Pokud je spuštěn agent, objekt konfigurace se změní na není platnou konfiguraci (konfigurace neodpovídá schématu), agent bude ignorovat neplatná konfigurace a budou i nadále používat aktuální konfiguraci. 

## <a name="edit-a-property"></a>Upravit vlastnosti  

Nastavte všechny vlastní vlastnosti uvnitř objektu konfigurace agenta v rámci AzureIoTSecurity dvojčete modulu. 

Nastavení vlastnosti, přidejte klíč vlastnosti objektu konfigurace s požadovanou hodnotu. Pokud chcete použít výchozí hodnotu vlastnosti, odeberte vlastnost z objektu konfigurace:

```json
"desired": { //AzureIoTSecurity Module Identity Twin – desired properties section  
  "azureiot*com^securityAgentConfiguration^1*0*0": { //ASC for IoT Agent 
      // configuration section  
    "hubResourceId": "/subscriptions/82392767-31d3-4bd2-883d-9b60596f5f42/resourceGroups/myResourceGroup/providers/Microsoft.Devices/IotHubs/myIotHub",     
    "lowPriorityMessageFrequency": "PT1H",     
    "highPriorityMessageFrequency": "PT7M",    
    "eventPriorityFirewallConfiguration": "High",     
    "eventPriorityConnectionCreate": "Off" 
  } 
}, 
```

## <a name="properties"></a>Vlastnosti 
Následující tabulka obsahuje všechny konfigurovatelné vlastnosti, které řídí ASC pro agenty IoT. 
          

| Název| Status | Platné hodnoty| Výchozí hodnoty| Popis |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|highPriorityMessageFrequency|Povinné: false |Platné hodnoty: Doba trvání ve formátu ISO 8601 |Výchozí hodnota: PT7M |Maximální doba před zprávy s vysokou prioritou se odesílají.|
|lowPriorityMessageFrequency |Povinné: false|Platné hodnoty: Doba trvání ve formátu ISO 8601 |Výchozí hodnota: PT5H |Maximální doba před zprávy s nízkou prioritou jsou odeslány.| 
|snapshotFrequency |Vyžadovat: false|Platné hodnoty: doba trvání ve formátu ISO 8601 |Výchozí hodnota PT13H |Časový interval pro vytváření snímků stav zařízení.| 
|maxLocalCacheSizeInBytes |Povinné: false |Platné hodnoty: |Výchozí hodnota: 2560000, větší než 8192 | Maximální povolená velikost úložiště (v bajtech) pro zprávu mezipaměti agenta. Maximální množství místa, které můžou ukládat zprávy na zařízení, před odesláním zprávy.| 
|maxMessageSizeInBytes |Povinné: false |Platné hodnoty: Kladné číslo větší než 8192, menší než 262144 |Výchozí hodnota: 204800 |Maximální povolená velikost agenta na cloudové zprávě. Toto nastavení určuje maximální množství dat odeslaných v každé zprávě. |
|eventPriority${EventName} |Povinné: false |Platné hodnoty: Vysoká, nízká vypnuto |Výchozí hodnoty: |Priorita každý agent vygeneruje událost. | 
|

### <a name="events"></a>Události

Následující seznam událostí, které jsou všechny události ASC pro IoT agenta můžete shromažďovat z vašich zařízení. Pomocí dvojčete modulu AzureIotSecurity konfigurace, které z těchto událostí se budou shromažďovat a rozhodnout jejich prioritu ve vašem řešení. 
 
|Název události| Vlastnost PropertyName | Výchozí hodnota| Události snímků| Podrobné informace o stavu  |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|Diagnostické události|eventPriorityDiagnostic| Vypnuto| False| Diagnostické události týkající se agenta. Tuto událost použijte pro podrobné protokolování.| 
Chyba konfigurace |eventPriorityConfigurationError |Nízká |False |Analýza konfigurace agenta se nezdařila. Ověřte konfiguraci proti schématu.| 
|Události vynechané statistiky |eventPriorityDroppedEventsStatistics |Nízká |True|Statistika událostí týkající se agenta. |
|Statistika zpráv|eventPriorityMessageStatistics |Nízká |True |Agent související statistiky zpráv. |
|Připojené hardwaru|eventPriorityConnectedHardware |Nízká |True |Snímek veškerý hardware připojené k zařízení.|
|Naslouchající porty|eventPriorityListeningPorts |Vysoký |True |Snímek všech otevřených naslouchající porty na zařízení.|
| Vytvoření procesu |eventPriorityProcessCreate |Nízká |False |Vytvoření zařízení procesu audity.|
| Ukončit proces|eventPriorityProcessTerminate |Nízká |False |Audity zpracování ukončení na zařízení.| 
 Systémové informace |eventPrioritySystemInformation |Nízká |True |Snímek systémové informace, například operační systém nebo procesoru.|
|Místní uživatele| eventPriorityLocalUsers |Vysoký |True|Snímek registrovaných místních uživatelů v rámci systému. |
|Přihlásit|  eventPriorityLogin |Vysoký|False|Auditování událostí přihlášení k zařízení (místní a vzdálené přihlášení).|
|Vytvoření připojení |eventPriorityConnectionCreate|Nízká|False|Audity připojení TCP vytvořili do a ze zařízení. |
|Konfigurace brány firewall| eventPriorityFirewallConfiguration|Nízká|True|Snímek konfigurace brány firewall na zařízení (pravidla brány firewall). |
|Směrným plánem operačního systému| eventPriorityOSBaseline| Nízká|True|Zkontrolujte snímek směrným plánem operačního systému zařízení.| 
|


## <a name="next-steps"></a>Další postup

- Přečtěte si ASC pro IoT [– přehled](overview.md)
- Další informace o ASC pro IoT [architektury](architecture.md)
- Porozumění a prozkoumávání [ASC pro IoT výstrahy](concept-security-alerts.md)
- Dozvíte se víc o přístup k vaší [zabezpečení dat](how-to-security-data-access.md)
