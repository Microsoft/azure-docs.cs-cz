---
title: Konfigurace Centra zabezpečení Azure pro agenta IoT| Dokumenty společnosti Microsoft
description: Zjistěte, jak nakonfigurovat agenty zabezpečení Azure Security Center pro IoT pro použití se službou zabezpečení Azure Security Center for IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: f95c445a-4f0d-4198-9c6c-d01446473bd0
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: mlottner
ms.openlocfilehash: 70396cdcaf8b6e2ac66619290eea35a7b260cd9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461245"
---
# <a name="tutorial-configure-security-agents"></a>Kurz: Konfigurace agentů zabezpečení

Tento článek vysvětluje Azure Security Center pro agenty zabezpečení IoT a podrobnosti o tom, jak je změnit a nakonfigurovat. 

> [!div class="checklist"]
> * Konfigurace agentů zabezpečení
> * Změna chování agenta úpravou vlastností dvojčete
> * Zjištění výchozí konfigurace

## <a name="agents"></a>Agenti

Agenti zabezpečení Azure Security Center pro IoT shromažďují data ze zařízení IoT a provádějí akce zabezpečení ke zmírnění zjištěných chyb zabezpečení. Konfigurace agenta zabezpečení je kontrolovatelná pomocí sady vlastností dvojčete modulu, které můžete přizpůsobit. Obecně sekundární aktualizace těchto vlastností jsou zřídka.  

Azure Security Center pro dvojče agenta zabezpečení IoT objekt konfigurace je objekt formátu JSON. Objekt konfigurace je sada řiditelných vlastností, které můžete definovat pro řízení chování agenta. 

Tyto konfigurace vám pomohou přizpůsobit agenta pro každý požadovaný scénář. Například automatické vyloučení některých událostí nebo udržování spotřeby energie na minimální úrovni je možné nakonfigurovat tyto vlastnosti.  

Pomocí [schématu](https://aka.ms/iot-security-github-module-schema) konfigurace agenta zabezpečení Azure Security Center pro IoT můžete provádět změny.  

## <a name="configuration-objects"></a>Konfigurační objekty 

Vlastnosti související s každým agentem zabezpečení Azure Security Center for IoT jsou umístěny v objektu konfigurace agenta v části požadované vlastnosti modulu **azureiotsecurity.** 

Chcete-li upravit konfiguraci, vytvořte a upravte tento objekt uvnitř identity dvojčete modulu **azureiotsecurity.** 

Pokud objekt konfigurace agenta neexistuje v dvojčeti modulu **azureiotsecurity,** všechny hodnoty vlastností agenta zabezpečení jsou nastaveny na výchozí. 

```json
"desired": {
  "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
  } 
}
```

## <a name="configuration-schema-and-validation"></a>Schéma konfigurace a ověření 

Ujistěte se, že ověřit konfiguraci agenta proti tomuto [schématu](https://aka.ms/iot-security-github-module-schema). Agent se nespustí, pokud objekt konfigurace neodpovídá schématu.

 
Pokud je v době, kdy je agent spuštěn, dojde ke změně objektu konfigurace na neplatnou konfiguraci (konfigurace neodpovídá schématu), agent nepoužije neplatnou konfiguraci a bude pokračovat v používání aktuální konfigurace. 

### <a name="configuration-validation"></a>Ověření konfigurace

Agent zabezpečení Azure Security Center for IoT hlásí svou aktuální konfiguraci v části ohlášené vlastnosti identity modulu **azureiotsecurity.**
Agent hlásí všechny dostupné vlastnosti, pokud vlastnost nebyla nastavena uživatelem, agent hlásí výchozí konfiguraci.

Chcete-li ověřit konfiguraci, porovnejte hodnoty nastavené v požadované části s hodnotami uvedenými v části uvedené.

Pokud existuje neshoda mezi požadované a hlášené vlastnosti, agent nebyl schopen analyzovat konfiguraci.

Ověřte požadované vlastnosti proti [schématu](https://aka.ms/iot-security-github-module-schema), opravte chyby a nastavte požadované vlastnosti znovu!

> [!NOTE]
> Upozornění na chybu konfigurace bude aktivována z agenta v případě, že agent nebyl schopen analyzovat požadovanou konfiguraci.
> Porovnejte nahlášený a požadovaný oddíl, abyste zjistili, zda výstraha stále platí

## <a name="editing-a-property"></a>Úprava vlastnosti 

Všechny vlastní vlastnosti musí být nastaveny uvnitř objektu konfigurace agenta v rámci dvojčete modulu **azureiotsecurity.**
Chcete-li použít výchozí hodnotu vlastnosti, odeberte vlastnost z objektu konfigurace.

### <a name="setting-a-property"></a>Nastavení vlastnosti

1. Ve svém Centru IoT Hub vyhledejte a vyberte zařízení, které chcete změnit.

1. Klikněte na své zařízení a pak na **modul azureiotsecurity.**

1. Klikněte na **Modul Identity Twin**.

1. Upravte vlastnosti, které chcete změnit v modulu zabezpečení.
   
   Chcete-li například nakonfigurovat události připojení jako vysokou prioritu a shromažďovat události s vysokou prioritou každých 7 minut, použijte následující konfiguraci.
   
    ```json
    "desired": {
        "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
            "highPriorityMessageFrequency": {
                "value": "PT7M"
            },
            "eventPriorityConnectionCreate": {
                "value": "High"
            }
        }
    }
    ```

1. Klikněte na **Uložit**.

### <a name="using-a-default-value"></a>Použití výchozí hodnoty

Chcete-li použít výchozí hodnotu vlastnosti, odeberte vlastnost z objektu konfigurace.

## <a name="default-properties"></a>Výchozí vlastnosti 

Následující tabulka obsahuje kontrolovatelné vlastnosti Azure Security Center pro agenty zabezpečení IoT.

Výchozí hodnoty jsou k dispozici ve správném schématu v [GitHubu](https\://aka.ms/iot-security-module-default).

| Name (Název)| Status | Platné hodnoty| Výchozí hodnoty| Popis |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|highPriorityMessageFrequency|Povinné: false |Platné hodnoty: Doba trvání ve formátu ISO 8601 |Výchozí hodnota: PT7M |Maximální časový interval před odesláním zpráv s vysokou prioritou.|
|lowPriorityMessageFrequencyFrequency |Povinné: false|Platné hodnoty: Doba trvání ve formátu ISO 8601 |Výchozí hodnota: PT5H |Maximální doba před odesláním zpráv s nízkou prioritou.| 
|snapshotFrequency |Vyžadovat: false|Platné hodnoty: Doba trvání ve formátu ISO 8601 |Výchozí hodnota PT13H |Časový interval pro vytvoření snímků stavu zařízení.| 
|maxLocalCacheSizeInBajtů |Povinné: false |Platné hodnoty: |Výchozí hodnota: 2560000, větší než 8192 | Maximální úložiště (v bajtů) povoleno pro mezipaměť zpráv agenta. Maximální velikost místa povolena pro ukládání zpráv v zařízení, před odesláním zpráv.| 
|maxMessageSizeInBajtů |Povinné: false |Platné hodnoty: Kladné číslo, větší než 8192, menší než 262144 |Výchozí hodnota: 204800 |Maximální povolená velikost agenta pro cloudovou zprávu. Toto nastavení určuje množství maximálního množství dat odeslaných v každé zprávě. |
|eventPriorit${Název_události} |Povinné: false |Platné hodnoty: Vysoká, Nízká, Vypnutá |Výchozí hodnoty: |Priorita každé události generované agentem | 

### <a name="supported-security-events"></a>Podporované události zabezpečení

|Název události| PropertyName | Výchozí hodnota| Událost snímku| Stav podrobností  |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|Diagnostická událost|eventPriorityDiagnostic| Vypnuto| False| Diagnostické události související s agentem. Tuto událost použijte pro podrobné protokolování.| 
|Chyba konfigurace |eventPrioritConfigurationError |Nízká |False |Agentovi se nepodařilo analyzovat konfiguraci. Ověřte konfiguraci proti schématu.| 
|Statistiky zrušených událostí |eventPriorityDroppedEventsStatistics |Nízká |True|Statistika událostí souvisejících s agentem. |
|Připojený hardware|eventPriorityConnectedHardware |Nízká |True |Snímek veškerého hardwaru připojeného k zařízení.|
|Naslouchací porty|eventPriorityListeningPorts |Vysoká |True |Snímek všech otevřených naslouchacích portů v zařízení.|
|Vytvoření procesu |eventPriorityProcessCreate |Nízká |False |Audity procesu vytváření na zařízení.|
|Proces ukončen|eventPriorityProcessTerminate |Nízká |False |Audity ukončení procesu na zařízení.| 
|Systémové informace |eventPrioritySystemInformation |Nízká |True |Snímek systémových informací (například Operační systém nebo procesor).| 
|Místní uživatelé| eventPriorityLocalUsers |Vysoká |True|Snímek registrovaných místních uživatelů v rámci systému. |
|Přihlásit|  eventPriorityLogin |Vysoká|False|Auditujte události přihlášení do zařízení (místní a vzdálená přihlášení).|
|Vytvoření připojení |eventPriorityConnectionCreate|Nízká|False|Audituje připojení TCP vytvořená do zařízení a ze zařízení. |
|Konfigurace brány firewall| eventPriorityFirewallConfiguration|Nízká|True|Snímek konfigurace brány firewall zařízení (pravidla brány firewall). |
|Výchozí hodnota operačního plánu| eventPriorityOSBaseline| Nízká|True|Snímek základní kontroly operačního operačního operačního systémem zařízení.|
|
 

## <a name="next-steps"></a>Další kroky

- [Principy doporučení Centra zabezpečení Azure pro IoT](concept-recommendations.md)
- [Prozkoumejte výstrahy Centra zabezpečení Azure pro IoT](concept-security-alerts.md)
- [Přístup k nezpracovaným datům zabezpečení](how-to-security-data-access.md)
