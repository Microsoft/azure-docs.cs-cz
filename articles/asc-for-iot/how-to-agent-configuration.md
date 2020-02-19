---
title: Konfigurace Azure Security Center pro agenta IoT | Microsoft Docs
description: Naučte se nakonfigurovat Azure Security Center pro agenty zabezpečení IoT pro použití s Azure Security Center pro službu zabezpečení IoT.
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
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461245"
---
# <a name="tutorial-configure-security-agents"></a>Kurz: konfigurace agentů zabezpečení

Tento článek vysvětluje Azure Security Center pro agenty zabezpečení IoT a podrobně popisuje, jak je změnit a nakonfigurovat. 

> [!div class="checklist"]
> * Konfigurace agentů zabezpečení
> * Změna chování agenta úpravou dvojitých vlastností
> * Zjistit výchozí konfiguraci

## <a name="agents"></a>Agenti

Azure Security Center pro agenty zabezpečení IoT shromažďují data ze zařízení IoT a provádějí bezpečnostní akce ke zmírnění zjištěných ohrožení zabezpečení. Konfigurace agenta zabezpečení je ovladatelné pomocí sady vlastností modulu, které můžete přizpůsobit. Obecně platí, že sekundární aktualizace těchto vlastností jsou nečasté.  

Azure Security Center objekt konfigurace s dvojitou vlastností zabezpečení IoT je objekt formátu JSON. Objekt konfigurace je sada přidaných vlastností, které můžete definovat pro řízení chování agenta. 

Tyto konfigurace vám pomůžou přizpůsobit agenta pro každý požadovaný scénář. Například automatické vyloučení některých událostí nebo udržování spotřeby energie na minimální úroveň je možné konfigurací těchto vlastností.  

K provedení změn použijte [schéma](https://aka.ms/iot-security-github-module-schema) konfigurace agenta zabezpečení služby Azure Security Center pro IoT.  

## <a name="configuration-objects"></a>Objekty konfigurace 

Vlastnosti související se všemi Azure Security Center pro agenta zabezpečení IoT jsou umístěné v objektu konfigurace agenta v části požadované vlastnosti modulu **azureiotsecurity** . 

Pokud chcete upravit konfiguraci, vytvořte a upravte tento objekt uvnitř **azureiotsecurity** identity modulu. 

Pokud objekt konfigurace agenta neexistuje v modulu **azureiotsecurity** s dvojitou hodnotou, všechny hodnoty vlastností agenta zabezpečení jsou nastaveny na výchozí. 

```json
"desired": {
  "ms_iotn:urn_azureiot_Security_SecurityAgentConfiguration": {
  } 
}
```

## <a name="configuration-schema-and-validation"></a>Schéma konfigurace a ověření 

Ujistěte se, že jste ověřili konfiguraci agenta proti tomuto [schématu](https://aka.ms/iot-security-github-module-schema). Agent se nespustí, pokud objekt konfigurace neodpovídá schématu.

 
Pokud je v době, kdy je spuštěn Agent nástroje, změněn objekt konfigurace na neplatnou konfiguraci (konfigurace neodpovídá schématu), Agent bude ignorovat neplatnou konfiguraci a bude pokračovat v používání aktuální konfigurace. 

### <a name="configuration-validation"></a>Ověření konfigurace

Azure Security Center pro agenta zabezpečení IoT oznamuje svou aktuální konfiguraci v části vykázané vlastnosti v modulu **azureiotsecurity** s dvojitou identitou.
Agent hlásí všechny dostupné vlastnosti, pokud vlastnost nebyla nastavena uživatelem, a proto agent nahlásí výchozí konfiguraci.

Chcete-li ověřit konfiguraci, porovnejte hodnoty nastavené v požadovaném oddílu s hodnotami uvedenými v hlášené části.

Pokud dojde k neshodě mezi požadovanými a oznámenými vlastnostmi, agent nemohl tuto konfiguraci analyzovat.

Ověřte požadované vlastnosti na [schématu](https://aka.ms/iot-security-github-module-schema), opravte chyby a nastavte požadované vlastnosti znovu.

> [!NOTE]
> Výstraha chyby konfigurace bude vyvolána od agenta v případě, že agent nemohl analyzovat požadovanou konfiguraci.
> Porovnejte hlášené a požadované části, abyste zjistili, jestli se výstraha pořád používá.

## <a name="editing-a-property"></a>Úprava vlastnosti 

Všechny vlastní vlastnosti musí být nastaveny uvnitř objektu konfigurace agenta v rámci modulu **azureiotsecurity** s dvojitou přesností.
Chcete-li použít výchozí hodnotu vlastnosti, odeberte vlastnost z objektu konfigurace.

### <a name="setting-a-property"></a>Nastavení vlastnosti

1. V IoT Hub vyhledejte a vyberte zařízení, které chcete změnit.

1. Klikněte na zařízení a pak na modul **azureiotsecurity** .

1. Klikněte na možnost **Nevlákenovaná identita modulu**.

1. Upravte vlastnosti, které chcete změnit v modulu zabezpečení.
   
   Pokud například chcete konfigurovat události připojení s vysokou prioritou a shromažďovat události s vysokou prioritou každých 7 minut, použijte následující konfiguraci.
   
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

1. Klikněte na možnost **Uložit**.

### <a name="using-a-default-value"></a>Použití výchozí hodnoty

Chcete-li použít výchozí hodnotu vlastnosti, odeberte vlastnost z objektu konfigurace.

## <a name="default-properties"></a>Výchozí vlastnosti 

Následující tabulka obsahuje ovladatelné vlastnosti Azure Security Center pro agenty zabezpečení IoT.

Výchozí hodnoty jsou k dispozici ve správném schématu v [GitHubu](https\://aka.ms/iot-security-module-default).

| Název| Status | Platné hodnoty| Výchozí hodnoty| Popis |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|highPriorityMessageFrequency|Požadováno: false |Platné hodnoty: doba trvání ve formátu ISO 8601 |Výchozí hodnota: PT7M |Maximální časový interval před odesláním zpráv s vysokou prioritou.|
|lowPriorityMessageFrequency |Požadováno: false|Platné hodnoty: doba trvání ve formátu ISO 8601 |Výchozí hodnota: PT5H |Maximální doba před odesláním zpráv s nízkou prioritou.| 
|snapshotFrequency |Vyžadovat: false|Platné hodnoty: doba trvání ve formátu ISO 8601 |Výchozí hodnota PT13H |Časový interval pro vytváření snímků stavu zařízení.| 
|maxLocalCacheSizeInBytes |Požadováno: false |Platné hodnoty: |Výchozí hodnota: 2560000, větší než 8192 | Maximální povolená velikost úložiště (v bajtech) pro mezipaměť pro zprávy agenta. Maximální místo, na které je povoleno ukládat zprávy na zařízení před odesláním zprávy.| 
|maxMessageSizeInBytes |Požadováno: false |Platné hodnoty: kladné číslo větší než 8192, menší než 262144 |Výchozí hodnota: 204800 |Maximální povolená velikost agenta pro zprávu cloudu Toto nastavení řídí maximální množství dat odesílaných v každé zprávě. |
|eventPriority${EventName} |Požadováno: false |Platné hodnoty: vysoká, nízká, off |Výchozí hodnoty: |Priorita každé události generované agentem | 

### <a name="supported-security-events"></a>Podporované události zabezpečení

|Název události| Vlastnost PropertyName | Výchozí hodnota| Událost snímku| Podrobnosti o stavu  |
|----------|------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|---------------|
|Diagnostická událost|eventPriorityDiagnostic| Vypnuto| False| Diagnostické události související s agentem. Tuto událost použijte pro podrobné protokolování.| 
|Chyba konfigurace |eventPriorityConfigurationError |Nízká |False |Agentovi se nepovedlo analyzovat konfiguraci. Ověřte konfiguraci oproti schématu.| 
|Statistika vyřazených událostí |eventPriorityDroppedEventsStatistics |Nízká |True|Statistika událostí souvisejících s agenty |
|Připojený hardware|eventPriorityConnectedHardware |Nízká |True |Snímek veškerého hardwaru připojeného k zařízení.|
|Naslouchající porty|eventPriorityListeningPorts |Vysoký |True |Snímek všech otevřených naslouchajících portů na zařízení.|
|Vytvoření procesu |eventPriorityProcessCreate |Nízká |False |Audituje vytváření procesů na zařízení.|
|Ukončení procesu|eventPriorityProcessTerminate |Nízká |False |Audituje ukončení procesu v zařízení.| 
|Systémové informace |eventPrioritySystemInformation |Nízká |True |Snímek systémových informací (například: operační systém nebo CPU).| 
|Místní uživatelé| eventPriorityLocalUsers |Vysoký |True|Snímek registrovaných místních uživatelů v rámci systému. |
|Přihlásit|  eventPriorityLogin |Vysoký|False|Auditujte události přihlášení do zařízení (místní a Vzdálená přihlášení).|
|Vytvoření připojení |eventPriorityConnectionCreate|Nízká|False|Audituje připojení TCP vytvořená do a ze zařízení. |
|Konfigurace brány firewall| eventPriorityFirewallConfiguration|Nízká|True|Snímek konfigurace brány firewall zařízení (pravidla brány firewall). |
|Základní hodnoty operačního systému| eventPriorityOSBaseline| Nízká|True|Snímek kontroly standardních hodnot operačního systému zařízení|
|
 

## <a name="next-steps"></a>Další kroky

- [Vysvětlení Azure Security Center pro doporučení IoT](concept-recommendations.md)
- [Prozkoumejte Azure Security Center pro výstrahy IoT](concept-security-alerts.md)
- [Přístup k nezpracovaným datům zabezpečení](how-to-security-data-access.md)
