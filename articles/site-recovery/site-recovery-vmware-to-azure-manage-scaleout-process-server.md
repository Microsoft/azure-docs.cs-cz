---
title: " Správa serveru proces Škálováním na více systémů v Azure Site Recovery | Microsoft Docs"
description: "Tento článek popisuje, jak nastavit a spravovat Server proces Škálováním na více systémů v Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 02/22/2018
ms.author: anoopkv
ms.openlocfilehash: b2c2f8c6a10ca5098956de2402925bd9422212f8
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/24/2018
---
# <a name="manage-a-scale-out-process-server"></a>Správa serveru proces Škálováním na více systémů

Škálováním na více systémů procesový Server funguje jako koordinátor pro přenos dat mezi služby Site Recovery a na místní infrastrukturu. Tento článek popisuje, jak můžete nastavit, konfigurovat a spravovat proces serveru Škálovaného na více systémů.

## <a name="prerequisites"></a>Požadavky
Následují doporučený hardware, software a konfiguraci sítě, které jsou nezbytné k nastavení procesového serveru se Škálováním na.

> [!NOTE]
> [Plánování kapacity](site-recovery-capacity-planner.md) je důležitý krok zajistit nasazení procesového serveru Škálovaného na více systémů s konfigurací této sady požadavků na zatížení. Další informace o [škálování vlastnosti pro Server se Škálováním na proces](#sizing-requirements-for-a-configuration-server).

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="downloading-the-scale-out-process-server-software"></a>Stahování softwaru Škálováním na více systémů procesového serveru
1. Přihlaste se k portálu Azure a přejděte do trezoru služeb zotavení.
2. Přejděte do **infrastruktury obnovení lokality** > **konfigurační servery** (v části pro VMware a fyzické počítače).
3. Vyberte svůj server konfigurace můžete rozbalit stránce s podrobnostmi o konfiguračním serveru.
4. Klikněte **+ procesový Server** tlačítko.
5. V **přidejte procesový server** vyberte **místní Server proces nasazení horizontální** možnost z **zvolte, kam chcete procesový server nasadit** rozevíracího seznamu.

  ![Přidat stránku servery](./media/site-recovery-vmware-to-azure-manage-scaleout-process-server/add-process-server.png)
6. Klikněte **stáhnout instalaci služby Microsoft Azure Site Recovery Unified** odkaz ke stažení nejnovější verze serveru se Škálováním procesu instalace.

  > [!WARNING]
  Verze procesový Server se Škálováním na musí být rovna nebo menší než konfigurační Server verze ve vašem prostředí. Jednoduchý způsob, jak zajistit kompatibilitu verze se má používat stejný Instalační služby bits, které jste naposledy použili k instalovat nebo aktualizovat konfigurační Server.

## <a name="installing-and-registering-a-scale-out-process-server-from-gui"></a>Instalace a registrace serveru se Škálováním na proces z grafického uživatelského rozhraní
Pokud máte pro horizontální škálování vašeho nasazení nad 200 zdrojového počítače nebo celkový denní míry změn více než 2 TB, je třeba servery další proces pro zpracování provozu.

Zkontrolujte [velikost doporučení pro servery proces](#size-recommendations-for-the-process-server)a pak postupujte podle těchto pokynů můžete nastavit na procesní server. Po nastavení serveru, migrovat zdrojový počítač používat.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-add-process-server.md)]


## <a name="installing-and-registering-a-scale-out-process-server-using-command-line"></a>Instalace a registrace serveru se Škálováním na proces pomocí příkazového řádku

```
UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
```

### <a name="sample-usage"></a>Využití vzorků
```
MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /xC:\Temp\Extracted
cd C:\Temp\Extracted
UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "PS" /InstallLocation "D:\" /EnvType "VMWare" /CSIP "10.150.24.119" /PassphraseFilePath "C:\Users\Administrator\Desktop\Passphrase.txt" /DataTransferSecurePort 443
```

### <a name="scale-out-process-server-installer-command-line-arguments"></a>Škálováním na více systémů procesový Server instalační program argumenty příkazového řádku.
[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]


### <a name="create-a-proxy-settings-configuration-file"></a>Vytvoření souboru konfiguračního nastavení proxy serveru
Parametr ProxySettingsFilePath vezme jako vstupní soubor. Vytvořte soubor v následujícím formátu a předejte ji jako vstupní parametr ProxySettingsFilePath.
```
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address"
* ProxyPort = "Port"
* ProxyUserName="UserName"
* ProxyPassword="Password"
```
## <a name="modifying-proxy-settings-for-scale-out-process-server"></a>Úprava nastavení proxy serveru pro škálování procesového serveru
1. Přihlásit se k serveru proces Škálováním na více systémů.
2. Otevřete okno příkazového prostředí PowerShell pro správu.
3. Spusťte následující příkaz
  ```
  $pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
  net stop obengine
  net start obengine
  ```
4. Dále přejděte do adresáře **%PROGRAMDATA%\ASR\Agent** a spusťte následující příkaz
  ```
  cmd
  cdpcli.exe --registermt

  net stop obengine

  net start obengine

  exit
  ```

## <a name="re-registering-a-scale-out-process-server"></a>Opakováním registrace serveru proces Škálováním na více systémů
[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

* Dále otevřete příkazový řádek správce.
* Přejděte do adresáře **%PROGRAMDATA%\ASR\Agent** a spusťte příkaz

```
cdpcli.exe --registermt

net stop obengine

net start obengine
```

## <a name="upgrading-a-scale-out-process-server"></a>Upgrade serveru proces Škálováním na více systémů
[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]

## <a name="decommissioning-a-scale-out-process-server"></a>Vyřazení z provozu Škálováním na více systémů procesového serveru
1. Zajistěte, aby:
  - Zobrazuje stav připojení konfigurační Server jako **připojeno** na portálu Azure
  - Procesový Server je stále moci komunikovat s konfiguračním serverem.
2. Přihlaste se k procesový server jako správce
3. Otevřete ovládací panely > Program > odinstalovat programy
4. Odinstalujte programy v pořadí zadané následující:
  * Microsoft Azure Site obnovení konfigurace serveru nebo procesový Server
  * Microsoft Azure Site Recovery konfigurace serveru závislosti
  * Agent Microsoft Azure Recovery Services

Může trvat až 15 minut pro odstranění procesový Server tak, aby odrážela na portálu Azure.

  > [!NOTE]
  Pokud je procesový server nemůže komunikovat s konfiguračním serverem (stav připojení portálu není připojen), je třeba provést následující kroky, abyste ji vymazat z konfiguračního serveru.

## <a name="unregistering-a-disconnected-scale-out-process-server-from-a-configuration-server"></a>Zrušení registrace odpojený server proces Škálováním na více systémů z konfigurace serveru

[!INCLUDE [site-recovery-vmware-upgrade-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="sizing-requirements-for-a-scale-out-process-server"></a>Změna velikosti požadavky pro Server proces Škálováním na více systémů

| **Další procesového serveru** | **Velikost disku mezipaměti** | **Míry změny dat** | **Chráněné počítače** |
| --- | --- | --- | --- |
|4 Vcpu (2 sockets * 2 jádra @ 2,5 GHz), 8 GB paměti |300 GB |250 GB nebo méně |Replikovat počítače 85 nebo méně. |
|8 Vcpu (2 sockets * 4 jádra @ 2,5 GHz), 12 GB paměti |600 GB |250 GB až 1 TB |Replikovat mezi 85 150 počítačů. |
|12 Vcpu (2 sockets * @ 2,5 GHz 6 jader) 24 GB paměti |1 TB |1 TB 2 TB |Replikovat mezi 150 225 počítačů. |
