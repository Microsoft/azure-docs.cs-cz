---
title: Nastavení horizontálního navýšení kapacity serveru během zotavení po havárii virtuálních počítačů VMware a fyzických serverů pomocí azure site recovery | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak nastavit horizontální navýšení kapacity procesu serveru během zotavení po havárii virtuálních měn VMware a fyzické servery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/23/2019
ms.author: ramamill
ms.openlocfilehash: 1b6084b4e93f3dc17f633f1b8496f9c26e7f576f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257144"
---
# <a name="scale-with-additional-process-servers"></a>Škálování s dalšími procesními servery

Ve výchozím nastavení se při replikaci virtuálních počítačů VMware nebo fyzických serverů do Azure pomocí [site recovery](site-recovery-overview.md)nainstaluje procesní server v počítači konfiguračního serveru a používá se ke koordinaci přenosu dat mezi site recovery a místní infrastrukturou. Chcete-li zvýšit kapacitu a škálovat nasazení replikace, můžete přidat další samostatné procesní servery. Tento článek popisuje, jak nastavit horizontální navýšení kapacity procesu serveru.

## <a name="before-you-start"></a>Než začnete

### <a name="capacity-planning"></a>Plánování kapacity

Ujistěte se, že jste provedli [plánování kapacity](site-recovery-plan-capacity-vmware.md) pro replikaci společnosti VMware. To vám pomůže určit, jak a kdy byste měli nasadit další procesní servery.

Z verze 9.24 je při výběru procesního serveru přidánnávod pro nové replikace. Procesní server bude na základě určitých kritérií označen jako V pořádku, Varování a Kritický. Chcete-li porozumět různým scénářům, které mohou ovlivnit stav procesu serveru, zkontrolujte [výstrahy procesového serveru](vmware-physical-azure-monitor-process-server.md#process-server-alerts).

> [!NOTE]
> Použití součásti klonovaného procesního serveru není podporováno. Postupujte podle kroků v tomto článku pro každý ps horizontální navýšení kapacity.

### <a name="sizing-requirements"></a>Požadavky na dimenzování 

Ověřte požadavky na velikost shrnuté v tabulce. Obecně platí, že pokud máte škálovat nasazení na více než 200 zdrojových počítačů nebo máte celkovou denní míru změn více než 2 TB, potřebujete další procesní servery pro zpracování objemu provozu.

| **Další procesní server** | **Velikost disku mezipaměti** | **Rychlost změny dat** | **Chráněné počítače** |
| --- | --- | --- | --- |
|4 vCPU (2 sokety \@ * 2 jádra 2,5 GHz), 8 GB paměti |300 GB |250 GB nebo méně |Replikujte 85 nebo méně počítačů. |
|8 virtuálních procesorů (2 sokety * 4 jádra \@ 2,5 GHz), 12GB paměť |600 GB |250 GB až 1 TB |Replikujte mezi 85-150 stroji. |
|12 virtuálních procesorů (2 sokety * 6 jader \@ 2,5 GHz) 24GB paměť |1 TB |1 TB až 2 TB |Replikujte mezi 150-225 počítači. |

Kde je každý chráněný zdrojový počítač konfigurován se 3 disky o velikosti 100 GB.

### <a name="prerequisites"></a>Požadavky

Požadavky pro další proces server jsou shrnuty v následující tabulce.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="download-installation-file"></a>Stáhnout instalační soubor

Stáhněte instalační soubor procesního serveru následujícím způsobem:

1. Přihlaste se k portálu Azure a přejděte do trezoru služby Recovery Services.
2. Otevřít **site recovery infrastructure** > **VMWare and Physical Machines** > **Configuration Servers** (v části Pro vmware & fyzické počítače).
3. Vyberte konfigurační server, chcete-li přejít k podrobnostem o serveru. Potom klepněte na tlačítko **+ Proces ový server**.
4. V **nabídce Procesní server** >  **Zvolte místo nasazení procesního serveru**, vyberte **Nasadit procesní server s horizontálním navýšením kapacity místně**.

   ![Stránka Přidat servery](./media/vmware-azure-set-up-process-server-scale/add-process-server.png)
1. Klikněte **na Stáhnout sjednocené nastavení obnovení webu Microsoft Azure**. Tím se stáhne nejnovější verze instalačního souboru.

   > [!WARNING]
   > Verze instalace procesního serveru by měla být stejná nebo starší než verze konfiguračního serveru, kterou používáte. Jednoduchý způsob, jak zajistit kompatibilitu verzí, je použití stejného instalačního programu, který jste naposledy použili k instalaci nebo aktualizaci konfiguračního serveru.

## <a name="install-from-the-ui"></a>Instalace z ui

Nainstalujte následujícím způsobem. Po nastavení serveru migrujete zdrojové počítače, abyste jej používali.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-add-process-server.md)]


## <a name="install-from-the-command-line"></a>Instalace z příkazového řádku

Nainstalujte spuštěním následujícího příkazu:

```
UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
```

Kde jsou parametry příkazového řádku následující:

[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]

Například:

```
MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
cd C:\Temp\Extracted
UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "PS" /InstallLocation "D:\" /EnvType "VMWare" /CSIP "10.150.24.119" /PassphraseFilePath "C:\Users\Administrator\Desktop\Passphrase.txt" /DataTransferSecurePort 443
```
### <a name="create-a-proxy-settings-file"></a>Vytvoření souboru nastavení proxy serveru

Pokud potřebujete nastavit proxy server, parametr ProxySettingsFilePath převezme jako vstup soubor. Soubor můžete vytvořit následujícím způsobem a předat jej jako vstupní parametr ProxySettingsFilePath.

```
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address"
* ProxyPort = "Port"
* ProxyUserName="UserName"
* ProxyPassword="Password"
```

## <a name="next-steps"></a>Další kroky
Informace o [správě nastavení procesního serveru](vmware-azure-manage-process-server.md)
