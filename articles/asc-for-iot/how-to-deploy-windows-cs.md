---
title: Instalace agenta Jazyka C# na zařízení se systémem Windows
description: Přečtěte si, jak nainstalovat agenta Azure Security Center pro IoT na 32bitová nebo 64bitová zařízení s Windows.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 4d7d2f0a423a50f85160a856480eaa973be7e2b0
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537606"
---
# <a name="deploy-an-azure-security-center-for-iot-c-based-security-agent-for-windows"></a>Nasazení agenta zabezpečení Azure Security Center pro IoT založeného na C# pro Windows

Tato příručka vysvětluje, jak nainstalovat Azure Security Center pro Agent a zabezpečení založené na IoT C # v systému Windows.

V této příručce se naučíte:

> [!div class="checklist"]
> * Instalace
> * Ověření nasazení
> * Odinstalace agenta
> * Řešení potíží

## <a name="prerequisites"></a>Požadavky

Další platformy a varianty agentů najdete v [tématu Výběr správného agenta zabezpečení](how-to-deploy-agent.md).

1. Místní práva správce v počítači, do který chcete nainstalovat.

1. [Vytvořte bezpečnostní modul](quickstart-create-security-twin.md) pro zařízení.

## <a name="installation"></a>Instalace

Chcete-li nainstalovat agenta zabezpečení, použijte následující pracovní postup:

1. Nainstalujte do zařízení agenta Azure Security Center for IoT Windows C#. Stáhněte si nejnovější verzi do počítače z úložiště Azure Security Center for IoT [GitHub](https://github.com/Azure/Azure-IoT-Security-Agent-CS).

1. Extrahujte obsah balíčku a přejděte do složky /Install.

1. Sem otevřete prostředí Windows PowerShell jako správce.
1. Spuštěním skriptu InstallSecurityAgent přidejte spuštěná oprávnění:

    ```
    Unblock-File .\InstallSecurityAgent.ps1
    ```

    pak spustit:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```

    Příklad:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```

    Další informace o parametrech ověřování naleznete v tématu [Konfigurace ověřování](concept-security-agent-authentication-methods.md).

Tento skript provádí následující akce:

* Nainstaluje požadavky.
* Přidá uživatele služby (s interaktivním přihlášením zakázáno).
* Nainstaluje agenta jako **systémovou službu**.
* Konfiguruje agenta s zadanými parametry ověřování.

Další nápovědu získáte pomocí příkazu Get-Help v PowerShellu.

Příklad získání nápovědy:```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>Ověření stavu nasazení

Zkontrolujte stav nasazení agenta spuštěním:

```sc.exe query "ASC IoT Agent"```

### <a name="uninstall-the-agent"></a>Odinstalace agenta

Odinstalace agenta:

1. Spusťte následující skript prostředí PowerShell s parametrem **-mode** nastaveným na **Odinstalovat**.

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ```

## <a name="troubleshooting"></a>Řešení potíží

Pokud se agentnepodaří spustit, zapněte protokolování (protokolování je ve výchozím nastavení *vypnuto)* chcete-li získat další informace.

Zapnutí protokolování:

1. Otevřete konfigurační soubor (General.config) pro úpravy pomocí standardního editoru souborů.

1. Upravte následující hodnoty:

   ```xml
   <add key="logLevel" value="Debug" />
   <add key="fileLogLevel" value="Debug"/>
   <add key="diagnosticVerbosityLevel" value="Some" />
   <add key="logFilePath" value="IoTAgentLog.log" />
   ```

    > [!NOTE]
    > Po dokončení řešení potíží doporučujeme **vypnout** protokolování. Ponecháte-li **přihlašování,** zvětší se velikost souboru protokolu a využití dat.

1. Restartujte agenta spuštěním následujícího Prostředí PowerShell nebo příkazového řádku:

    **Powershell**

     ```
     Restart-Service "ASC IoT Agent"
     ```

   – nebo –

    **Cmd**

     ```
     sc.exe stop "ASC IoT Agent"
     sc.exe start "ASC IoT Agent"
     ```

1. Další informace o chybě naleznete v souboru protokolu. Soubor protokolu by byl přítomen v pracovním adresáři, kde spustíme skript. 

   Umístění souboru protokolu:`.\IoTAgentLog.log`

## <a name="next-steps"></a>Další kroky

* Přečtěte si [přehled](overview.md) služby Azure Security Center for IoT
* Další informace o Azure Security Center pro [architekturu](architecture.md) IoT
* Povolení [služby](quickstart-onboard-iot-hub.md)
* Přečtěte si [nejčastější dotazy](resources-frequently-asked-questions.md)
* Principy [výstrah](concept-security-alerts.md)
