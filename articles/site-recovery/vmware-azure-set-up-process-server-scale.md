---
title: Nastavení procesového serveru se škálováním na více instancí během zotavení po havárii virtuálních počítačů VMware a fyzických serverů s Azure Site Recovery | Microsoft Docs
description: Tento článek popisuje, jak nastavit procesový Server se škálováním na více instancí během zotavení po havárii virtuálních počítačů VMware a fyzických serverů.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/23/2019
ms.author: ramamill
ms.openlocfilehash: 40f912122e6ffb9cccbd32a747f6f0d46fd6c330
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87292805"
---
# <a name="scale-with-additional-process-servers"></a>Škálování pomocí dalších procesových serverů

Při replikaci virtuálních počítačů VMware nebo fyzických serverů do Azure pomocí [Site Recovery](site-recovery-overview.md)se ve výchozím nastavení na počítači konfiguračního serveru nainstaluje procesový Server a použije se ke koordinaci přenosu dat mezi Site Recovery a místní infrastrukturou. Pokud chcete zvýšit kapacitu a škálovat nasazení replikace, můžete přidat další samostatné procesové servery. Tento článek popisuje, jak nastavit procesový Server se škálováním na více instancí.

## <a name="before-you-start"></a>Než začnete

### <a name="capacity-planning"></a>Plánování kapacity

Ujistěte se, že jste provedli [plánování kapacity](site-recovery-plan-capacity-vmware.md) pro replikaci VMware. To vám pomůže určit, jak a kdy byste měli nasadit další procesové servery.

Od verze 9,24 se pokyny přidávají během výběru procesového serveru pro nové replikace. Procesový Server bude označen jako dobrý, varovný a kritický na základě určitých kritérií. Pro pochopení různých scénářů, které mohou ovlivnit stav procesového serveru, zkontrolujte [výstrahy procesového serveru](vmware-physical-azure-monitor-process-server.md#process-server-alerts).

> [!NOTE]
> Použití komponenty klonovaného procesového serveru se nepodporuje. Postupujte podle kroků v tomto článku pro každé horizontální navýšení kapacity PS.

### <a name="sizing-requirements"></a>Požadavky na velikost 

Ověřte požadavky na velikost shrnuté v tabulce. Obecně platí, že pokud potřebujete škálovat nasazení na více než 200 zdrojových počítačů nebo máte celkovou denní četnost změn více než 2 TB, budete potřebovat další procesové servery pro zpracování objemu provozu.

| **Další procesový Server** | **Velikost disku mezipaměti** | **Frekvence změny dat** | **Chráněné počítače** |
| --- | --- | --- | --- |
|4 vCPU (2 sokety × 2 jádra \@ 2,5 GHz), 8 GB paměti |300 GB |250 GB nebo méně |Replikujte 85 nebo méně počítačů. |
|8 vCPU (2 sokety × 4 jádra \@ 2,5 GHz), 12 GB paměti |600 GB |250 GB až 1 TB |Replikace mezi 85-150 počítači. |
|12 vCPU (2 sokety × 6 jader \@ 2,5 GHz) 24 GB paměti |1 TB |1 TB až 2 TB |Replikace mezi 150-225 počítači. |

Kde je každý chráněný zdrojový počítač nakonfigurovaný se 3 disky o velikosti 100 GB.

### <a name="prerequisites"></a>Požadavky

Požadavky na další procesový Server jsou shrnuté v následující tabulce.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="download-installation-file"></a>Stáhnout instalační soubor

Instalační soubor pro procesový Server si stáhněte následujícím způsobem:

1. Přihlaste se k Azure Portal a přejděte do svého trezoru Recovery Services.
2. Otevřete **Site Recovery infrastruktury**  >  **VMware a fyzických počítačů**  >  **Konfigurace serveru** (v části pro & fyzické počítače VMware).
3. Vyberte konfigurační server pro přechod k podrobnostem o serveru. Pak klikněte na **+ procesový Server**.
4. V nabídce **Přidat procesový Server**  >   **Zvolte, kam chcete procesový Server nasadit**, a vyberte **nasadit místní procesový Server s horizontálním škálováním kapacity**.

   ![Stránka Přidat servery](./media/vmware-azure-set-up-process-server-scale/add-process-server.png)
1. Klikněte na **stáhnout Microsoft Azure Site Recovery Unified Setup**. Tím se stáhne nejnovější verze instalačního souboru.

   > [!WARNING]
   > Verze instalace procesového serveru musí být stejná jako verze konfiguračního serveru, kterou používáte, nebo starší. Jediným způsobem, jak zajistit kompatibilitu verzí, je použít stejný instalační program, který jste nedávno použili k instalaci nebo aktualizaci konfiguračního serveru.

## <a name="install-from-the-ui"></a>Instalace z uživatelského rozhraní

Nainstalujte následujícím způsobem. Po nastavení serveru migrujete zdrojové počítače tak, aby se používaly.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-add-process-server.md)]


## <a name="install-from-the-command-line"></a>Instalace z příkazového řádku

Nainstalujte spuštěním následujícího příkazu:

```
UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMware/NonVMware>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
```

Parametry příkazového řádku jsou následující:

[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]

Například:

```
MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
cd C:\Temp\Extracted
UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "PS" /InstallLocation "D:\" /EnvType "VMware" /CSIP "10.150.24.119" /PassphraseFilePath "C:\Users\Administrator\Desktop\Passphrase.txt" /DataTransferSecurePort 443
```
### <a name="create-a-proxy-settings-file"></a>Vytvoření souboru s nastavením proxy

Pokud potřebujete nastavit proxy server, parametr ProxySettingsFilePath jako vstup převezme soubor. Soubor můžete vytvořit následujícím způsobem a předat jako vstupní parametr ProxySettingsFilePath.

```
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address"
* ProxyPort = "Port"
* ProxyUserName="UserName"
* ProxyPassword="Password"
```

## <a name="next-steps"></a>Další kroky
Další informace o [správě nastavení procesového serveru](vmware-azure-manage-process-server.md)
