---
title: Nastavit procesní server v Azure pro virtuální počítač VMware a fyzické server navrácení služeb po obnovení s Azure Site Recovery | Microsoft Docs
description: Tento článek popisuje, jak nastavit procesní server v Azure, pro navrácení služeb po obnovení virtuálních počítačů Azure k VMware.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 06/10/2018
ms.author: raynew
ms.openlocfilehash: 3e53954341136a293052f9af755515a5552432fe
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300843"
---
# <a name="set-up-additional-process-servers-for-scalability"></a>Nastavit další proces serverů pro škálovatelnost

Ve výchozím nastavení, pokud replikujete virtuální počítače VMware nebo fyzických serverů do Azure pomocí [Site Recovery](site-recovery-overview.md), procesový server je nainstalován na serveru, konfigurace a slouží ke koordinaci přenosu dat mezi Site Recovery a v místní infrastruktuře. Pokud chcete zvýšit kapacitu a škálování nasazení replikace, můžete přidat další samostatný proces serverů. Tento článek popisuje, jak to provést.

## <a name="before-you-start"></a>Než začnete

### <a name="capacity-planning"></a>Plánování kapacity

Ujistěte se, které jste provedli [plánování kapacity](site-recovery-plan-capacity-vmware.md) pro replikaci VMware. To umožňuje identifikovat jak a kdy měli byste nasadit další proces servery.

### <a name="sizing-requirements"></a>Změna velikosti požadavky 

Ověřte nastavení velikosti požadavky shrnuto v tabulce. Obecně platí Pokud budete muset změnit měřítko nasazení na více než 200 zdrojového počítače, nebo máte celkem denně změn počet více než 2 TB, je třeba servery další proces pro zpracování provozu.

| **Další procesového serveru** | **Velikost disku mezipaměti** | **Míry změny dat** | **Chráněné počítače** |
| --- | --- | --- | --- |
|4 Vcpu (2 sockets * 2 jádra @ 2,5 GHz), 8 GB paměti |300 GB |250 GB nebo méně |Replikovat počítače 85 nebo méně. |
|8 Vcpu (2 sockets * 4 jádra @ 2,5 GHz), 12 GB paměti |600 GB |250 GB až 1 TB |Replikovat mezi 85 150 počítačů. |
|12 Vcpu (2 sockets * @ 2,5 GHz 6 jader) 24 GB paměti |1 TB |1 TB 2 TB |Replikovat mezi 150 225 počítačů. |

### <a name="prerequisites"></a>Požadavky

V následující tabulce jsou shrnuté požadavky na další procesový server.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]


## <a name="download-installation-file"></a>Stáhněte instalační soubor

Stáhněte instalační soubor pro procesový server následujícím způsobem:

1. Přihlaste se k portálu Azure a přejděte do trezoru služeb zotavení.
2. Otevřete **infrastruktury obnovení lokality** > **VMWare a fyzické počítače** > **konfigurační servery** (v části pro VMware a fyzické Počítače).
3. Vyberte konfigurační server můžete rozbalit podrobnosti serveru. Pak klikněte na tlačítko **+ procesový Server**.
4. V **přidejte procesový server** >  **zvolte, kam chcete procesový server nasadit**, vyberte **místní Server proces nasazení horizontální**.

  ![Přidat stránku servery](./media/vmware-azure-set-up-process-server-scale/add-process-server.png)
1. Klikněte na tlačítko **stáhnout Microsoft Azure Site Recovery sjednocený instalační program**. Tato akce stáhne nejnovější verzi instalačního souboru.

  > [!WARNING]
  Verze procesového serveru instalace musí být stejná jako, nebo starší než, verze konfigurace serveru, je třeba systémem. Jednoduchý způsob, jak zajistit kompatibilitu verze se má používat stejný instalační program, který jste naposledy použili instalaci nebo aktualizaci konfigurační server.

## <a name="install-from-the-ui"></a>Nainstalujte z uživatelského rozhraní

Nainstalujte následujícím způsobem. Po nastavení serveru, migrovat zdrojový počítač používat.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-add-process-server.md)]


## <a name="install-from-the-command-line"></a>Instalace z příkazového řádku

Nainstalujte spuštěním následujícího příkazu:

```
UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
```

Kde parametry příkazového řádku jsou následující:

[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]

Příklad:

```
MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /xC:\Temp\Extracted
cd C:\Temp\Extracted
UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "PS" /InstallLocation "D:\" /EnvType "VMWare" /CSIP "10.150.24.119" /PassphraseFilePath "C:\Users\Administrator\Desktop\Passphrase.txt" /DataTransferSecurePort 443
```
### <a name="create-a-proxy-settings-file"></a>Vytvořte soubor nastavení proxy serveru

Pokud potřebujete nastavit proxy server, parametr ProxySettingsFilePath vezme jako vstupní soubor. Můžete vytvořit soubor takto a předejte ji jako vstupní parametr ProxySettingsFilePath.

```
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address"
* ProxyPort = "Port"
* ProxyUserName="UserName"
* ProxyPassword="Password"
```

## <a name="next-steps"></a>Další postup
Další informace o [Správa zpracovat nastavení serveru](vmware-azure-manage-process-server.md)
