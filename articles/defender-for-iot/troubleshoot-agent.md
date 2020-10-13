---
title: Řešení potíží s spuštěním agenta zabezpečení (Linux)
description: Řešení potíží při práci s Azure Defenderem pro agenty zabezpečení IoT pro Linux
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
ms.openlocfilehash: e9904e9157a560e2a4853a1a9cd37977defe73ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90935099"
---
# <a name="security-agent-troubleshoot-guide-linux"></a>Průvodce odstraňováním potíží s agenty zabezpečení (Linux)

Tento článek vysvětluje, jak řešit potenciální problémy v procesu spuštění agenta zabezpečení.

Azure Defender pro IoT Agent se spouští okamžitě po instalaci. Proces spuštění agenta zahrnuje čtení místní konfigurace, připojení k Azure IoT Hub a načítání vzdálené zdvojené konfigurace. Selhání v některém z těchto kroků může způsobit selhání agenta zabezpečení.

V tomto průvodci odstraňováním potíží se dozvíte, jak:

> [!div class="checklist"]
> * Ověřit, zda je spuštěn Agent zabezpečení
> * Získat chyby agenta zabezpečení
> * Pochopení a náprava chyb agenta zabezpečení

## <a name="validate-if-the-security-agent-is-running"></a>Ověřit, zda je spuštěn Agent zabezpečení

1. Pokud chcete ověřit, že je Agent zabezpečení spuštěný, počkejte pár minut po instalaci agenta a spusťte následující příkaz.
     <br>

    **Agent jazyka C**

    ```bash
    grep "ASC for IoT Agent initialized" /var/log/syslog
    ```

    **Agent C#**

    ```bash
    grep "Agent is initialized!" /var/log/syslog
    ```

1. Pokud příkaz vrátí prázdný řádek, nelze úspěšně spustit agenta zabezpečení.

## <a name="force-stop-the-security-agent"></a>Vynutit zastavení agenta zabezpečení

V případech, kdy se Agent zabezpečení nemůže spustit, Zastavte agenta pomocí následujícího příkazu a pak pokračujte v následující chybové tabulce:

```bash
systemctl stop ASCIoTAgent.service
```

## <a name="get-security-agent-errors"></a>Získat chyby agenta zabezpečení

1. Chyby agenta zabezpečení načtěte spuštěním následujícího příkazu:

    ```bash
    grep ASCIoTAgent /var/log/syslog
    ```

1. Příkaz získat chybu agenta zabezpečení načte všechny protokoly vytvořené v programu Defender pro agenta IoT. Pomocí následující tabulky můžete porozumět chybám a provést správné kroky pro nápravu.

> [!Note]
> Protokoly chyb jsou zobrazovány v chronologickém pořadí. Nezapomeňte si poznamenat časové razítko každé chyby, aby bylo možné vaši nápravu vyřešit.

## <a name="restart-the-agent"></a>Restartujte agenta.

1. Až vyhledáte a opravíte chybu agenta zabezpečení, zkuste restartovat agenta spuštěním následujícího příkazu.

    ```bash
    systemctl restart ASCIoTAgent.service
    ```

1. Opakujte předchozí proces, aby se zastavila operace zastavit, a načtěte chyby, pokud Agent nadále neplní proces po spuštění.

## <a name="understand-security-agent-errors"></a>Vysvětlení chyb agenta zabezpečení

Většina chyb agenta zabezpečení se zobrazuje v následujícím formátu:

```
Defender for IoT agent encountered an error! Error in: {Error Code}, reason: {Error sub code}, extra details: {error specific details}
```

| Kód chyby | Chybový kód chyby | Podrobnosti o chybě | Opravit C | Opravit C # |
|:-----------|:---------------|:--------|:------------|:------------|
| Místní konfigurace | Chybějící konfigurace | V místním konfiguračním souboru chybí konfigurace. Chybová zpráva by měla uvádět, který klíč chybí. | Přidejte chybějící klíč do/var/LocalConfiguration.jsv souboru. Podrobnosti najdete v části [cs-localconfig-reference](azure-iot-security-local-configuration-c.md) .| Do souboru General.config přidejte chybějící klíč. Podrobnosti najdete v tématu [c#-localconfig-reference](azure-iot-security-local-configuration-csharp.md) . |
| Místní konfigurace | Nejde analyzovat konfiguraci. | Hodnotu konfigurace nelze analyzovat. Chybová zpráva by měla obsahovat stav, který klíč nelze analyzovat. Hodnotu konfigurace nelze analyzovat buď proto, že hodnota není v očekávaném typu, nebo je hodnota mimo rozsah. | Opravte hodnotu klíče v/var/LocalConfiguration.jsv souboru tak, aby odpovídala schématu LocalConfiguration. Podrobnosti najdete v tématu [c#-localconfig-reference](azure-iot-security-local-configuration-csharp.md) . |  Opravte hodnotu klíče v souboru General.config tak, aby odpovídala schématu. Podrobnosti najdete v části [cs-localconfig-reference](azure-iot-security-local-configuration-c.md) .|
| Místní konfigurace | Formát souboru | Nepovedlo se analyzovat konfigurační soubor. | Konfigurační soubor je poškozený, Stáhněte agenta a znovu ho nainstalujte. | |
| Vzdálená konfigurace | Časový limit | Agentovi se nepovedlo načíst v rámci časového limitu vlákna azureiotsecurity modulu. | Ujistěte se, že je konfigurace ověřování správná, a zkuste to znovu. | Agentovi se nepovedlo načíst v rámci časového limitu vlákna modulu azureiotsecurity. | Ujistěte se, že je konfigurace ověřování správná, a zkuste to znovu. |
| Authentication | Soubor neexistuje. | Soubor v dané cestě neexistuje. | Ujistěte se, že soubor v dané cestě existuje, nebo přejděte na **LocalConfiguration.jsv** souboru a změňte konfiguraci **FilePath** . | Zajistěte, aby soubor v dané cestě existoval, nebo přejděte do souboru **Authentication.config** a změňte konfiguraci **FilePath** .|
| Authentication | Oprávnění souboru | Agent nemá dostatečná oprávnění k otevření souboru. | Udělte uživateli **asciotagent** oprávnění ke čtení souboru v dané cestě. | Ujistěte se, že je soubor přístupný. |
| Authentication | Formát souboru | Daný soubor nemá správný formát. | Ujistěte se, že je soubor ve správném formátu. Podporované typy souborů jsou. pfx a. pem. | Ujistěte se, že je soubor platným souborem certifikátu. |
| Authentication | Neautorizováno | Agent nemohl provést ověření proti IoT Hub s danými přihlašovacími údaji. | Ověřte konfiguraci ověřování v souboru LocalConfiguration, Projděte konfiguraci ověřování a ujistěte se, že jsou všechny podrobnosti správné, ověřte, že tajný klíč v souboru odpovídá ověřené identitě. | Ověřte konfiguraci ověřování v Authentication.config, Projděte si konfiguraci ověřování a ujistěte se, že jsou všechny podrobnosti správné, a ověřte, že tajný klíč v souboru odpovídá ověřené identitě.
| Authentication | Nenalezeno | Zařízení/modul se našel. | Ověřit konfiguraci ověřování – Ujistěte se, že je název hostitele správný, že zařízení existuje v IoT Hub a má neazureiotsecurityý modul. |  Ověřit konfiguraci ověřování – Ujistěte se, že je název hostitele správný, že zařízení existuje v IoT Hub a má neazureiotsecurityý modul. |
| Authentication | Chybějící konfigurace | V souboru *Authentication.config* chybí konfigurace. Chybová zpráva by měla uvádět, který klíč chybí. | Do *LocalConfiguration.js* souboru přidejte chybějící klíč.| Do souboru *Authentication.config* přidejte chybějící klíč. Podrobnosti najdete v tématu [c#-localconfig-reference](azure-iot-security-local-configuration-csharp.md) . |
| Authentication | Nejde analyzovat konfiguraci. | Hodnotu konfigurace nelze analyzovat. Chybová zpráva by měla obsahovat stav, který klíč nelze analyzovat. Hodnotu konfigurace nelze analyzovat, protože buď hodnota není očekávaného typu, nebo je hodnota mimo rozsah. |Opravte hodnotu klíče v **LocalConfiguration.jsv** souboru. |Opravte hodnotu klíče v souboru **Authentication.config** tak, aby odpovídala schématu. Další informace najdete v tématu [cs-localconfig-reference](azure-iot-security-local-configuration-c.md) .|
|

## <a name="next-steps"></a>Další kroky

- Přečtěte si téma [Přehled](overview.md) služby Defender for IoT.
- Další informace o programu Defender pro [architekturu](architecture.md) IoT
- Povolení služby Defender pro IoT [Service](quickstart-onboard-iot-hub.md)
- Přečtěte si [Nejčastější dotazy](resources-frequently-asked-questions.md) ke službě Defender for IoT
- Přečtěte si, jak získat přístup k [nezpracovaným datům zabezpečení](how-to-security-data-access.md)
- Vysvětlení [doporučení](concept-recommendations.md)
- Vysvětlení [výstrah](concept-security-alerts.md) zabezpečení
