---
title: Poradce při potížích s spuštěním agenta zabezpečení (Linux)
description: Poradce při potížích práce s Azure Security Center pro agenty zabezpečení IoT pro Linux.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2019
ms.author: mlottner
ms.openlocfilehash: 935a99dd34b0a4e3d4970e8d91f9332d2bc1489a
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310568"
---
# <a name="security-agent-troubleshoot-guide-linux"></a>Průvodce odstraňováním potíží s agenty zabezpečení (Linux)

Tento článek vysvětluje, jak vyřešit potenciální problémy v procesu spuštění agenta zabezpečení.

Azure Security Center pro agenta IoT se spustí ihned po instalaci. Proces spuštění agenta zahrnuje čtení místní konfigurace, připojení k Azure IoT Hub a načítání konfigurace vzdálenédvojče. Selhání v některém z těchto kroků může způsobit selhání agenta zabezpečení.

V tomto průvodci odstraňováním potíží se dozvíte, jak:

> [!div class="checklist"]
> * Ověřit, zda je spuštěn agent zabezpečení
> * Získání chyb agenta zabezpečení
> * Principy a náprava chyb agentů zabezpečení

## <a name="validate-if-the-security-agent-is-running"></a>Ověřit, zda je spuštěn agent zabezpečení

1. Chcete-li ověřit, je agent zabezpečení je spuštěn, počkejte několik minut po instalaci agenta a spusťte následující příkaz.
     <br>

    **C agent**

    ```bash
    grep "ASC for IoT Agent initialized" /var/log/syslog
    ```

    **Agent jazyka C#**

    ```bash
    grep "Agent is initialized!" /var/log/syslog
    ```

1. Pokud příkaz vrátí prázdný řádek, agent a zabezpečení nelze úspěšně spustit.

## <a name="force-stop-the-security-agent"></a>Vynucení zastavení agenta zabezpečení

V případech, kdy agent zabezpečení nelze spustit, zastavte agenta pomocí následujícího příkazu a pokračujte k následující tabulce chyb:

```bash
systemctl stop ASCIoTAgent.service
```

## <a name="get-security-agent-errors"></a>Získání chyb agenta zabezpečení

1. Načtěte chyby agenta zabezpečení spuštěním následujícího příkazu:

    ```bash
    grep ASCIoTAgent /var/log/syslog
    ```

1. Příkaz chyby agenta zabezpečení get načte všechny protokoly vytvořené Centrem zabezpečení Azure pro agenta IoT. V následující tabulce můžete porozumět chybám a provést správné kroky k nápravě.

> [!Note]
> Chybové protokoly jsou zobrazeny v chronologickém pořadí. Nezapomeňte si poznamenat časové razítko každé chyby, které vám pomůže s nápravou.

## <a name="restart-the-agent"></a>Restartovat agenta

1. Po vyhledání a opravě chyby agenta zabezpečení zkuste agenta restartovat spuštěním následujícího příkazu.

    ```bash
    systemctl restart ASCIoTAgent.service
    ```

1. Opakujte předchozí proces načíst zastavit a načíst chyby, pokud agent nadále nezdaří proces spuštění.

## <a name="understand-security-agent-errors"></a>Vysvětlení chyb agenta zabezpečení

Většina chyb agenta zabezpečení je zobrazena v následujícím formátu:

```
Azure Security Center for IoT agent encountered an error! Error in: {Error Code}, reason: {Error sub code}, extra details: {error specific details}
```

| Kód chyby | Dílčí kód chyby | Podrobnosti o chybě | Náprava C | Náprava C # |
|:-----------|:---------------|:--------|:------------|:------------|
| Místní konfigurace | Chybějící konfigurace | V místním konfiguračním souboru chybí konfigurace. Chybová zpráva by měla uvádět, který klíč chybí. | Přidejte chybějící klíč do souboru /var/LocalConfiguration.json, podrobnosti naleznete v [dokumentu cs-localconfig-reference.](azure-iot-security-local-configuration-c.md)| Přidejte chybějící klíč do souboru General.config, podrobnosti naleznete v [odkazu c#-localconfig-reference.](azure-iot-security-local-configuration-csharp.md) |
| Místní konfigurace | Konfigurace analýzy převeze | Hodnotu konfigurace nelze analyzovat. Chybová zpráva by měla uvádět, který klíč nelze analyzovat. Hodnotu konfigurace nelze analyzovat, protože hodnota není v očekávaném typu nebo je hodnota mimo rozsah. | Opravte hodnotu klíče v souboru /var/LocalConfiguration.json tak, aby odpovídal schématu LocalConfiguration, podrobnosti naleznete v [odkazu c#-localconfig-reference.](azure-iot-security-local-configuration-csharp.md) |  Upravte hodnotu klíče v souboru General.config tak, aby odpovídal schématu, podrobnosti naleznete v [tématu cs-localconfig-reference.](azure-iot-security-local-configuration-c.md)|
| Místní konfigurace | Formát souboru | Analýza konfiguračního souboru se nezdařila. | Konfigurační soubor je poškozen, stáhněte agenta a znovu nainstalujte. | |
| Vzdálená konfigurace | Časový limit | Agent nemohl načíst dvojče modulu azureiotsecurity v rámci časového času. | Zkontrolujte správnou konfiguraci ověřování a akci opakujte. | Agent nemohl načíst dvojče modulu azureiotsecurity v časovém rámci. | Zkontrolujte správnou konfiguraci ověřování a akci opakujte. |
| Authentication | Soubor neexistuje | Soubor v dané cestě neexistuje. | Ujistěte se, že soubor existuje v dané cestě, nebo přejděte do souboru **LocalConfiguration.json** a změňte **konfiguraci filepath.** | Ujistěte se, že soubor existuje v dané cestě, nebo přejděte do souboru **Authentication.config** a změňte **konfiguraci filePath.**|
| Authentication | Oprávnění k souboru | Agent nemá dostatečná oprávnění k otevření souboru. | Udělte **uživateli asciotagent** oprávnění ke čtení souboru v dané cestě. | Zkontrolujte, zda je soubor přístupný. |
| Authentication | Formát souboru | Daný soubor není ve správném formátu. | Zkontrolujte, zda je soubor ve správném formátu. Podporované typy souborů jsou .pfx a .pem. | Zkontrolujte, zda je soubor platným souborem certifikátu. |
| Authentication | Neautorizováno | Agent nebyl schopen ověřit proti Služby IoT Hub s danými přihlašovacími údaji. | Ověřte konfiguraci ověřování v souboru LocalConfiguration, projděte konfiguraci ověřování a ujistěte se, že jsou všechny podrobnosti správné, ověřte, zda tajný klíč v souboru odpovídá ověřené identitě. | Ověřte konfiguraci ověřování v souboru Authentication.config, projděte konfiguraci ověřování a ujistěte se, že jsou všechny podrobnosti správné, a poté ověřte, zda tajný klíč v souboru odpovídá ověřené identitě.
| Authentication | Nebyl nalezen. | Zařízení / modul byl nalezen. | Ověření konfigurace ověřování – ujistěte se, že název hostitele je správný, zařízení existuje v centru IoT Hub a má modul dvojčete azureiotsecurity. |  Ověření konfigurace ověřování – ujistěte se, že název hostitele je správný, zařízení existuje v centru IoT Hub a má modul dvojčete azureiotsecurity. |
| Authentication | Chybějící konfigurace | V souboru *Authentication.config* chybí konfigurace. Chybová zpráva by měla uvádět, který klíč chybí. | Přidejte chybějící klíč do souboru *LocalConfiguration.json.*| Přidejte chybějící klíč do souboru *Authentication.config,* podrobnosti naleznete v [odkazu c#-localconfig-reference.](azure-iot-security-local-configuration-csharp.md) |
| Authentication | Konfigurace analýzy převeze | Hodnotu konfigurace nelze analyzovat. Chybová zpráva by měla uvádět, který klíč nelze analyzovat. Hodnotu konfigurace nelze analyzovat, protože buď není očekávaného typu, nebo je hodnota mimo rozsah. |Opravte hodnotu klíče v souboru **LocalConfiguration.json.** |Upravte hodnotu klíče v souboru **Authentication.config** tak, aby odpovídala schématu, podrobnosti naleznete v [odkazu cs-localconfig-reference.](azure-iot-security-local-configuration-c.md)|
|

## <a name="restart-the-agent"></a>Restartovat agenta

1. Po vyhledání a opravě chyby agenta zabezpečení restartujte agenta spuštěním následujícího příkazu:

    ```bash
    systemctl restart ASCIoTAgent.service
    ```

1. V případě potřeby opakujte předchozí procesy vynutit zastavení agenta a načíst chyby, pokud agent nadále nezdaří proces spuštění.

## <a name="next-steps"></a>Další kroky

- Přečtěte si [přehled](overview.md) služby Azure Security Center for IoT
- Další informace o Azure Security Center pro [architekturu](architecture.md) IoT
- Povolení [služby](quickstart-onboard-iot-hub.md) Azure Security Center for IoT
- Přečtěte si [nejčastější dotazy](resources-frequently-asked-questions.md) ke Službě Azure Security Center for IoT
- Zjistěte, jak získat přístup k [nezpracovaným bezpečnostním datům](how-to-security-data-access.md)
- Principy [doporučení](concept-recommendations.md)
- Principy [výstrah](concept-security-alerts.md) zabezpečení
