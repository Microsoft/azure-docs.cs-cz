---
title: Kurz k instalaci a nasazení Linuxu C# agenta služby Azure Security Center pro náhled IoT | Dokumentace Microsoftu
description: Zjistěte, jak nainstalovat ASC pro agenta IoT na 32bitová i 64bitová verze systému Linux.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: b0982203-c3c8-4a0b-8717-5b5ac4038d8c
ms.service: ascforiot
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 50e21e1d0937f4543435fe2f2e5e01e06a2b1e3c
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541561"
---
# <a name="tutorial-deploy-asc-for-iot-c-based-security-agent-for-linux"></a>Kurz: Nasazení ASC pro IoT C#– na základě zabezpečení agenta pro Linux

> [!IMPORTANT]
> ASC pro IoT je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tento kurz vysvětluje, jak nainstalovat a nasadit ASC pro IoT C#-agent zabezpečení založeného na Linuxu.

V tomto kurzu se naučíte: 
> [!div class="checklist"]
> * Instalace
> * Ověření nasazení
> * Odinstalace agenta
> * Řešení potíží 

## <a name="prerequisites"></a>Požadavky

Další platformy a chuti agenta, najdete v části [vyberte správné zabezpečení agenta](select-deploy-agent.md).

1. Nasaďte agenta nástroje zabezpečení, se vyžadují oprávnění místního správce na počítači, který chcete nainstalovat. 

1. [Vytvoření modulu zabezpečení](quickstart-create-security-twin.md) zařízení.

## <a name="installation"></a>Instalace 

Pokud chcete nasadit agenta zabezpečení, postupujte takto:

1. Stáhnout nejnovější verzi na svůj počítač z [Githubu](https://aka.ms/iot-security-github-cs).

1. Extrahujte obsah balíčku a přejděte _/Install_ složky.

1. Přidat spuštěné oprávnění **InstallSecurityAgent skript** spuštěním `chmod +x InstallSecurityAgent.sh` 

1. Potom spusťte: 

   ```
   ./InstallSecurityAgent.sh -i -aui <authentication identity>  -aum <authentication method> -f <file path> -hn <host name>  -di <device id> -cl <certificate location kind>
   ```
   
   Zobrazit [ke konfiguraci ověřování](concept-security-agent-authentication-methods.md) Další informace o ověření parametrů.

Skript provede následující akce:

- Nainstaluje požadavky.

- Přidá uživatele služby (pomocí interaktivního přihlášení, zakázáno).

- Nainstaluje agenta jako **démon** – předpokladem zařízení používá **systemd** pro správu služeb.

- Nakonfiguruje **sudoers** smí agent k provádění určitých úloh jako uživatel root.

- Nakonfiguruje agenta pomocí zadaného ověřovacího parametry.


Potřebujete další pomoc, spuštěním skriptu s parametrem – nápovědy: `./InstallSecurityAgent.sh --help`

### <a name="uninstall-the-agent"></a>Odinstalace agenta

Pokud chcete agenta odinstalovat, spusťte skript s parametrem – u: `./InstallSecurityAgent.sh -u`. 

> [!NOTE]
> Odinstalace neodebere chybějící požadované součásti, které byly nainstalované při instalaci.

## <a name="troubleshooting"></a>Řešení potíží  

1. Spuštěním kontroly stavu nasazení:

    `systemctl status ASCIoTAgent.service`

2. Povolení protokolování.  
   Pokud agenta nepodaří spustit, zapněte protokolování zobrazíte další informace.

   Zapnutí protokolování podle:

   1. Otevřete konfigurační soubor pro úpravy v libovolném editoru Linux:

        `vi /var/ASCIoTAgent/General.config`

   1. Upravte následující hodnoty: 

      ```
      <add key="logLevel" value="Debug"/>
      <add key="fileLogLevel" value="Debug"/>
      <add key="diagnosticVerbosityLevel" value="Some" /> 
      <add key="logFilePath" value="IotAgentLog.log"/>
      ```
       **LogFilePath** hodnota je konfigurovatelná. 

       > [!NOTE]
       > Doporučujeme vám zapnout protokolování **vypnout** po dokončení odstraňování potíží. Opuštění protokolování **na** zvýšení protokolování využití velikost a data souborů.

   1. Restartujte agenta spuštěním:

       `systemctl restart ASCIoTAgent.service`

   1. Zobrazte soubor protokolu pro další informace o selhání.  

       Umístění souboru protokolu je: `/var/ASCIoTAgent/IotAgentLog.log`

       Změňte cestu k umístění souboru podle názvu, který jste zvolili pro **logFilePath** v kroku 2. 

## <a name="next-steps"></a>Další postup

- Přečtěte si ASC pro služby Azure IoT [– přehled](overview.md)
- Další informace o ASC pro IoT [architektury](architecture.md)
- Povolit [služby](quickstart-onboard-iot-hub.md)
- Přečtěte si [– nejčastější dotazy](resources-frequently-asked-questions.md)
- Vysvětlení [výstrahy](concept-security-alerts.md)