---
title: Nastavit procesový server v Azure k selhání zpět při zotavení po havárii virtuálních počítačů VMware a fyzických serverů s Azure Site Recovery | Dokumentace Microsoftu
description: Tento článek popisuje, jak nastavit procesový server v Azure, k selhání obnovení z Azure do místního během zotavení po havárii virtuálních počítačů VMware a fyzických serverů.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 2ef08831706a018422ea3700c8eb63bd6f95c469
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52834943"
---
# <a name="scale-for-failback-with-additional-process-servers"></a>Škálování pro navrácení služeb po obnovení pomocí dalších procesových serverů

Ve výchozím nastavení se při replikaci virtuálních počítačů VMware nebo fyzických serverů do Azure s využitím [Site Recovery](site-recovery-overview.md), procesový server je nainstalovaný na počítači serveru konfigurace a slouží k přenosu dat mezi Site Recovery koordinuje a v místní infrastruktuře. Ke zvýšení kapacity a horizontální navýšení kapacity nasazení replikace, přidáte další samostatné procesových serverů. Tento článek popisuje, jak to provést.

## <a name="before-you-start"></a>Než začnete

### <a name="capacity-planning"></a>Plánování kapacity

Ujistěte se, že jste provedli [plánování kapacity](site-recovery-plan-capacity-vmware.md) pro replikaci VMware. To pomáhá zjistit, jak a kdy byste měli nasadit dalších procesových serverů.

### <a name="sizing-requirements"></a>Požadavky na velikost 

Zkontrolujte požadavky na velikost uvedené v tabulce. Obecně platí Pokud budete muset škálovat nasazení tak, aby více než 200 zdrojové počítače, nebo máte celkem denní četnost více než 2 TB změn dat, je třeba dalších procesových serverů, které zvládnou objem přenosů.

| **Další procesový server** | **Velikost mezipaměti disku** | **Frekvence změny dat** | **Chráněné počítače** |
| --- | --- | --- | --- |
|4 virtuální procesory (2 sockets * 2 jádra \@ 2,5 GHz), 8 GB paměti |300 GB |Aby se 250 GB nebo méně |Replikace počítačů 85 nebo méně. |
|8 virtuálních procesorů (2 sockets * 4 jádra \@ 2,5 GHz), 12 GB paměti |600 GB |250 GB až 1 TB |Replikace mezi 85 150 počítačů. |
|12 virtuálních procesorů (2 sockets * 6 jader \@ 2,5 GHz) 24 GB paměti |1 TB |1 TB na 2 TB |Replikace mezi 150 225 počítačů. |

Kde každý chráněný zdrojový počítač je nakonfigurován se 3 disky o 100 GB.

### <a name="prerequisites"></a>Požadavky

V následující tabulce jsou shrnuté požadavky pro další procesový server.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]



## <a name="download-installation-file"></a>Stáhněte instalační soubor

Stáhněte instalační soubor pro procesový server následujícím způsobem:

1. Přihlaste se k webu Azure portal a přejděte do vašeho trezoru služby Recovery Services.
2. Otevřít **infrastruktura Site Recovery** > **VMWare a fyzických počítačů** > **konfigurační servery** (v části pro VMware a fyzické Počítače).
3. Vyberte konfigurační server přejdete na podrobnosti o serveru. Pak klikněte na tlačítko **+ procesový Server**.
4. V **přidat procesový server** >  **zvolit, kam chcete procesový server nasadit**vyberte **nasazení Scale-out procesový Server v místním**.

  ![Přidat stránku servery](./media/vmware-azure-set-up-process-server-scale/add-process-server.png)
1. Klikněte na tlačítko **stáhněte si Microsoft Azure Site Recovery sjednocené instalace**. Tato akce stáhne nejnovější verzi instalačního souboru.

  > [!WARNING]
  Verze procesového serveru instalace by měla být stejná nebo nižší než, verze konfiguračního serveru máte spuštěné. Jednoduchý způsob, jak zajistit kompatibilitu verze je použijte stejnou instalační program, který jste naposledy použili k instalaci nebo aktualizaci konfiguračního serveru.

## <a name="install-from-the-ui"></a>Instalace z uživatelského rozhraní

Nainstalujte následujícím způsobem. Po nastavení serveru, můžete migraci zdrojové počítače jeho použití.

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
MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
cd C:\Temp\Extracted
UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "PS" /InstallLocation "D:\" /EnvType "VMWare" /CSIP "10.150.24.119" /PassphraseFilePath "C:\Users\Administrator\Desktop\Passphrase.txt" /DataTransferSecurePort 443
```
### <a name="create-a-proxy-settings-file"></a>Vytvořte soubor nastavení proxy serveru

Pokud je potřeba nastavit proxy server, přebírá parametr ProxySettingsFilePath soubor jako vstup. Můžete následujícím způsobem vytvořte tento soubor a předat ji jako vstupní parametr ProxySettingsFilePath.

```
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address"
* ProxyPort = "Port"
* ProxyUserName="UserName"
* ProxyPassword="Password"
```

## <a name="next-steps"></a>Další postup
Další informace o [server nastavení řízení procesu](vmware-azure-manage-process-server.md)
