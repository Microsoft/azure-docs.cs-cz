---
title: Konfigurace pro agenta IoT ve verzi Preview ASC | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat agenty pro použití s ASC pro IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: f95c445a-4f0d-4198-9c6c-d01446473bd0
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 8bb9f0bc57e03ae0897e77acaa30ec85a3541646
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58649369"
---
# <a name="tutorial-configure-security-agents"></a>Kurz: Konfigurace agentů zabezpečení

> [!IMPORTANT]
> ASC pro IoT je aktuálně ve verzi public preview.
> Tato verze preview je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se používat pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tento článek vysvětluje ASC pro agenta zabezpečení IoT, jak změnit jejich konfigurace ASC pro agenty zabezpečení IoT.

> [!div class="checklist"]
> * Konfigurace agentů zabezpečení
> * Změnit chování agenta tak, že upravíte vlastnosti dvojčat
> * Zjistit výchozí konfigurace

## <a name="agents"></a>Agenti

ASC IoT zabezpečení agentů shromažďování dat ze zařízení IoT a provádět akce zabezpečení ke zmírnění zjištěná ohrožení zabezpečení. Konfigurace agenta zabezpečení je řídit, používáte sadu vlastnosti dvojčete modulu, které můžete přizpůsobit. Obecně platí jsou úlohy s řídkým sekundární aktualizace těchto vlastností.  

Pro objekt konfigurace dvojčete agenta zabezpečení IoT od ASC se formát objektu .json. Objekt konfigurace je sada nastavitelných vlastností, které můžete definovat pro řízení chování agenta. 

Tato konfigurace vám umožní přizpůsobit agenta pro každý scénář vyžaduje. Například automaticky bez některé události nebo udržování spotřeba energie na minimální úroveň jsou možné konfigurací těchto vlastností.  

Použijte pro konfiguraci agenta zabezpečení IoT ASC [schématu](https://aka.ms/iot-security-github-module-schema) provádět změny.  

## <a name="configuration-objects"></a>Objekty konfigurace 

Každý ASC pro agenta zabezpečení IoT související vlastnosti se nachází v objektu konfigurace agenta, v části požadované vlastnosti z **azureiotsecurity** modulu. 

Upravit konfiguraci, vytvářet a upravovat tento objekt uvnitř **azureiotsecurity** identity dvojčete modulu. 

Pokud v neexistuje objekt konfigurace agenta **azureiotsecurity** dvojče zařízení, všechny hodnoty vlastností agenta zabezpečení jsou nastavené na výchozí. 

```json
"desired": {
  "azureiot*com^securityAgentConfiguration^1*0*0": {
  } 
}
```

Ujistěte se, že chcete zkontrolovat změny konfigurace agenta před tímto [schématu](https://aka.ms/iot-security-github-module-schema).
Agent se nespustí, pokud objekt konfigurace neodpovídá schématu.

## <a name="configuration-schema-and-validation"></a>Schéma konfigurace a ověření 

Ujistěte se, že k ověření vaší konfigurace agenta před tímto [schématu](https://aka.ms/iot-security-github-module-schema). Agent se nespustí, pokud objekt konfigurace neodpovídá schématu.

 
Pokud je spuštěn agent, objekt konfigurace se změní na není platnou konfiguraci (konfigurace neodpovídá schématu), agent bude ignorovat neplatná konfigurace a budou i nadále používat aktuální konfiguraci. 

## <a name="editing-a-property"></a>Úpravy vlastností 

Všechny vlastní vlastnosti musí být nastavena v objektu konfigurace agenta v rámci **azureiotsecurity** dvojčete modulu.
Pokud chcete použít výchozí hodnotu vlastnosti, odeberte vlastnost z objektu konfigurace.

### <a name="setting-a-property"></a>Nastavení vlastnosti

1. Ve službě IoT Hub vyhledejte a vyberte zařízení, které chcete změnit.

1. Klikněte na tlačítko na vašem zařízení a potom na **azureiotsecurity** modulu.

1. Klikněte na **dvojče zařízení Identity**.

1. Upravte požadované vlastnosti zabezpečení modulu.
   
   Třeba ke konfiguraci připojení událostí s vysokou prioritou a shromažďování událostí s vysokou prioritou každých 7 minut, použijte následující konfiguraci.
   
   ```json
    "desired": {
      "azureiot*com^securityAgentConfiguration^1*0*0": {
        "highPriorityMessageFrequency": "PT7M",    
        "eventPriorityConnectionCreate": "High" 
      } 
    }, 
    ```

1. Klikněte na **Uložit**.

### <a name="using-a-default-value"></a>Použití výchozí hodnoty

Pokud chcete použít výchozí hodnotu vlastnosti, odeberte vlastnost z objektu konfigurace.

## <a name="default-properties"></a>Výchozí vlastnosti 

Následující tabulka obsahuje nastavitelných vlastností ASC pro agenty zabezpečení IoT.

Výchozí hodnoty jsou k dispozici ve správné schéma v [Githubu](https://aka.ms/iot-security-module-default).

| Název| Status | Platné hodnoty| Výchozí hodnoty| Popis |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|highPriorityMessageFrequency|Povinné: false |Platné hodnoty: Doba trvání ve formátu ISO 8601 |Výchozí hodnota: PT7M |Maximální doba před zprávy s vysokou prioritou se odesílají.|
|lowPriorityMessageFrequency |Povinné: false|Platné hodnoty: Doba trvání ve formátu ISO 8601 |Výchozí hodnota: PT5H |Maximální doba před zprávy s nízkou prioritou jsou odeslány.| 
|snapshotFrequency |Vyžadovat: false|Platné hodnoty: doba trvání ve formátu ISO 8601 |Výchozí hodnota PT13H |Časový interval pro vytváření snímků stav zařízení.| 
|maxLocalCacheSizeInBytes |Povinné: false |Platné hodnoty: |Výchozí hodnota: 2560000, větší než 8192 | Maximální povolená velikost úložiště (v bajtech) pro zprávu mezipaměti agenta. Maximální množství místa, které můžou ukládat zprávy na zařízení, před odesláním zprávy.| 
|maxMessageSizeInBytes |Povinné: false |Platné hodnoty: Kladné číslo větší než 8192, menší než 262144 |Výchozí hodnota: 204800 |Maximální povolená velikost agenta na cloudové zprávě. Toto nastavení určuje maximální množství dat odeslaných v každé zprávě. |
|eventPriority${EventName} |Povinné: false |Platné hodnoty: Vysoká, nízká vypnuto |Výchozí hodnoty: |Priorita každý agent vygeneruje událost | 

### <a name="supported-security-events"></a>Události podporované zabezpečení

|Název události| Vlastnost PropertyName | Výchozí hodnota| Události snímků| Podrobnosti stavu  |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|Diagnostické události|eventPriorityDiagnostic| Vypnuto| False| Diagnostické události týkající se agenta. Tuto událost použijte pro podrobné protokolování.| 
|Chyba konfigurace |eventPriorityConfigurationError |Nízká |False |Analýza konfigurace agenta se nezdařila. Ověřte konfiguraci proti schématu.| 
|Události vynechané statistiky |eventPriorityDroppedEventsStatistics |Nízká |True|Statistika událostí týkající se agenta. |
|Statistika zpráv|eventPriorityMessageStatistics |Nízká |True |Agent související statistiky zpráv. |
|Připojené hardwaru|eventPriorityConnectedHardware |Nízká |True |Snímek veškerý hardware připojené k zařízení.|
|Naslouchající porty|eventPriorityListeningPorts |Vysoký |True |Snímek všech otevřených naslouchající porty na zařízení.|
|Vytvoření procesu |eventPriorityProcessCreate |Nízká |False |Vytvoření zařízení procesu audity.|
|Ukončit proces|eventPriorityProcessTerminate |Nízká |False |Audity zpracování ukončení na zařízení.| 
|Systémové informace |eventPrioritySystemInformation |Nízká |True |Snímek informace o systému (například: Operační systém nebo procesor).| 
|Místní uživatele| eventPriorityLocalUsers |Vysoký |True|Snímek registrovaných místních uživatelů v rámci systému. |
|Přihlásit|  eventPriorityLogin |Vysoký|False|Kontrola událostí přihlášení k zařízení (místní a vzdálené přihlášení).|
|Vytvoření připojení |eventPriorityConnectionCreate|Nízká|False|Audity připojení TCP vytvořili do a ze zařízení. |
|Konfigurace brány firewall| eventPriorityFirewallConfiguration|Nízká|True|Snímek konfigurace brány firewall na zařízení (pravidla brány firewall). |
|Směrným plánem operačního systému| eventPriorityOSBaseline| Nízká|True|Zkontrolujte snímek směrným plánem operačního systému zařízení.|
 

## <a name="next-steps"></a>Další postup

- [Vysvětlení ASC pro IoT doporučení](concept-recommendations.md)
- [Prozkoumejte ASC pro IoT výstrahy](concept-security-alerts.md)
- [Přístup k datům raw zabezpečení](how-to-security-data-access.md)
