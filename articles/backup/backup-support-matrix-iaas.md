---
title: Azure Backup matice podpory pro zálohování virtuálních počítačů Azure
description: Poskytuje přehled podpory nastavení a omezení při zálohování virtuálních počítačů Azure pomocí služby Azure Backup.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: raynew
ms.openlocfilehash: 637ec63d1c3e5a3eb1ce26765b1ab22e9823e9a1
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317421"
---
# <a name="support-matrix-for-azure-vm-backup"></a>Matice podpory pro zálohování virtuálních počítačů Azure
Můžete použít [služby Azure Backup](backup-overview.md) pro zálohování místních počítačů a úlohy a Azure virtual machines (VM). Tento článek shrnuje podporu nastavení a omezení při zálohování virtuálních počítačů Azure pomocí Azure Backup.

Další matice podpory:

- [Matice podpory Obecné](backup-support-matrix.md) pro službu Azure Backup
- [Systém podpory replikace z](backup-support-matrix-mabs-dpm.md) zálohování pro Azure Backup server nebo System Center Data Protection Manager (DPM)
- [Systém podpory replikace z](backup-support-matrix-mars-agent.md) pro zálohování pomocí agenta Microsoft Azure Recovery Services (MARS)

## <a name="supported-scenarios"></a>Podporované scénáře

Tady je způsob zálohování a obnovení virtuálních počítačů Azure pomocí služby Azure Backup.

**Scénář** | **Backup** | **Agent** |**Obnovení**
--- | --- | --- | ---
Přímé zálohování virtuálních počítačů Azure  | Zálohujte celý virtuální počítač.  | Žádný agent je potřeba na virtuálním počítači Azure. Azure Backup nainstaluje a používá rozšíření [agenta virtuálního počítače Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) , na kterém běží na virtuálním počítači. | Obnovte takto:<br/><br/> - **Vytvoření základního virtuálního počítače**. To je užitečné, pokud virtuální počítač nemá žádnou zvláštní konfiguraci například více IP adres.<br/><br/> - **Obnovení disku virtuálního počítače**. Obnovení disku. Pak ho připojit k existujícímu virtuálnímu počítači nebo vytvořit nový virtuální počítač z disku pomocí prostředí PowerShell.<br/><br/> - **Výměna disku virtuálního počítače**. Pokud virtuální počítač existuje a používá spravované disky (nešifrované), můžete obnovit disk a použít k nahrazení existujícího disku na virtuálním počítači.<br/><br/> - **Obnovení konkrétních souborů a složek**. Soubory nebo složky můžete obnovit z virtuálního počítače namísto z celý virtuální počítač.
Přímé zálohování virtuálních počítačů Azure (jenom Windows)  | Zálohujte konkrétní soubory nebo složky/svazku. | Nainstalujte [agenta služeb zotavení Azure](backup-azure-file-folder-backup-faq.md).<br/><br/> Můžete spustit agenta MARS společně s rozšířením zálohování pro agenta virtuálního počítače Azure k zálohování virtuálních počítačů na úrovni souboru nebo složky. | Obnovte určité soubory a složky.
Zálohování virtuálních počítačů Azure na záložní server  | Zálohování souborů/složek nebo svazků; Stav/úplné systému soubory systému; data aplikací do nástroje System Center DPM nebo Microsoft Azure Backup Server (MABS).<br/><br/> Aplikace DPM nebo MABS se potom zálohuje do trezoru záloh. | Na virtuálním počítači nainstalujte agenta ochrany aplikace DPM nebo MABS. MARS agent se instaluje na aplikaci DPM nebo MABS.| Obnovení souborů/složek nebo svazků; Stav/úplné systému soubory systému; data aplikací.

Další informace o zálohování [pomocí zálohování serveru](backup-architecture.md#architecture-back-up-to-dpmmabs) a asi [požadavky na podporu](backup-support-matrix-mabs-dpm.md).

## <a name="supported-backup-actions"></a>Nepodporuje zálohování akce

**Akce** | **Podpora**
--- | ---
Povolit zálohování při vytváření virtuálního počítače Azure s Windows | Podporované pro:  Windows Server. 2019 (jádro Datacenter nebo Datacenter), Windows Server 2016 (jádro Datacenter nebo Datacenter); Windows Server 2012 R2 Datacenter; Windows Server 2008 R2 (verze RTM a SP1)
Povolit zálohování při vytváření virtuálního počítače s Linuxem | Podporované pro:<br/><br/> - Ubuntu Server: 1710, 1704, 1604 (LTS), 1404 (LTS)<br/><br/> – Red Hat: RHEL 6.7, 6.8, 6.9, 7.2, 7.3, 7.4<br/><br/> -Operačním systémem SUSE Linux Enterprise Server: 11 SP4, 12 SP2, 12 SP3<br/><br/> -Debian: 8, 9<br/><br/> - CentOS: 6.9, 7.3<br/><br/> -Oracle Linux: 6.7, 6.8, 6.9, 7.2, 7.3
Zálohování virtuálního počítače, která virtuální počítač vypnout/offline/hledání | Podporuje se.<br/><br/> Snímek je konzistentní při selhání pouze, není konzistentní s aplikací.
Zálohování disků po migraci na spravované disky | Podporuje se.<br/><br/> Zálohování bude pokračovat v práci. Nevyžaduje se žádná akce.
Zálohování spravované disky po povolení zámku skupiny prostředků | Nepodporuje se.<br/><br/> Azure Backup nejde odstranit starších bodů prostředků a zálohování se spustí při dosažení maximálního počtu bodů obnovení.
Upravit zásady zálohování pro virtuální počítač | Podporuje se.<br/><br/> Virtuální počítač bude zálohovat pomocí nastavení plán a uchovávání dat v nové zásady. Pokud jsou nastavení uchovávání prodlouží, existující body obnovení jsou označeny a udržovat. Pokud jste se zkrátí, bude nedochází k vyřazení v rámci další úlohy čištění a nakonec odstraní existující body obnovení.
Zrušit úlohu zálohování | Během procesu snímku podporována.<br/><br/> Není podporováno při snímku přenášejí do trezoru.
Zálohování virtuálního počítače do jiné oblasti nebo předplatného |  Nepodporuje se.
Zálohy za den (prostřednictvím rozšíření virtuálního počítače Azure) | Jeden naplánované zálohování za den.<br/><br/> Můžete vytvořit až čtyři zálohy na vyžádání za den.
Zálohy za den (prostřednictvím agenta MARS) | Tři naplánované zálohy za den.
Zálohy za den (přes aplikaci DPM nebo MABS) | Dvě naplánované zálohy za den.
Zálohování, měsíčních nebo ročních   | Není podporováno při zálohování se rozšíření virtuálního počítače Azure. Pouze denní nebo týdenní podporována.<br/><br/> Můžete nastavit zásady pro denní nebo týdenní zálohy uchovávat po dobu uchování, měsíčních nebo ročních.
Nastavení automatické hodiny | Nepodporuje se.<br/><br/> Azure Backup nepodporuje upravit automaticky změny letního času při zálohování virtuálního počítače.<br/><br/>  Ručně podle potřeby upravte zásadu.
[Funkce zabezpečení pro hybridní zálohy](https://docs.microsoft.com/azure/backup/backup-azure-security-feature) |  Zakázání funkce zabezpečení není podporován.

## <a name="operating-system-support-windows"></a>Podpora operačního systému (Windows)

Následující tabulka shrnuje podporované operační systémy, při zálohování virtuálních počítačů Windows Azure.

**Scénář** | **Podporu operačního systému**
--- | ---
Zálohování pomocí rozšíření agenta virtuálního počítače Azure | Klient Windows: Nepodporuje se<br/><br/> Windows Server. 2019 (jádro Datacenter nebo Datacenter), Windows Server 2016 (jádro Datacenter nebo Datacenter); Windows Server 2012 R2 Datacenter; Windows Server 2008 R2 (verze RTM a SP1)
Zálohování pomocí agenta MARS | [Podporované](backup-support-matrix-mars-agent.md#support-for-direct-backups) operačních systémů.
Zálohování pomocí aplikace DPM nebo MABS | Podporované operační systémy pro zálohování pomocí [MABS](backup-mabs-protection-matrix.md) a [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807).

## <a name="support-for-linux-backup"></a>Podpora pro zálohování Linux

Zde je, co je podporováno, pokud chcete zálohování počítačů s Linuxem.

**Akce** | **Podpora**
--- | ---
Zálohování virtuálních počítačů Azure s Linuxem s agentem virtuálního počítače Azure s Linuxem | Konzistentní zálohování souborů.<br/><br/> Konzistentní zálohování pomocí [vlastní skripty](backup-azure-linux-app-consistent.md).<br/><br/> Během obnovení můžete vytvořit nový virtuální počítač, obnovení disku a jeho použití k vytvoření virtuálního počítače, nebo obnovení disku a použít k nahrazení disku v existující virtuální počítač. Můžete také obnovit jednotlivé soubory a složky.
Zálohování virtuálních počítačů Azure s Linuxem pomocí agenta MARS | Nepodporuje se.<br/><br/> Agenta MARS lze nainstalovat pouze na počítačích s Windows.
Zálohování virtuálních počítačů Azure s Linuxem pomocí aplikace DPM nebo MABS | Nepodporuje se.

## <a name="operating-system-support-linux"></a>Podpora operačního systému (Linux)

Zálohování virtuálních počítačů Azure s Linuxem, Azure Backup podporuje Linux [distribuce schválené pro Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros). Je třeba počítat s následujícím:

- Azure Backup nepodporuje základního OS Linux.
- Azure Backup nepodporuje 32bitové operační systémy.
- Jiné přineste vlastní distribuce Linuxu mohou fungovat tak dlouho, dokud [agenta virtuálního počítače Azure pro Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) je k dispozici na virtuálním počítači a pokud je podporován Python.
- Azure Backup nepodporuje virtuální počítač spuštěný  **Python verze 2.6* pouze** s proxy server nakonfigurovaný.



## <a name="backup-frequency-and-retention"></a>Frekvence zálohování a uchovávání

**Nastavení** | **Omezení**
--- | ---
Maximální počet bodů obnovení na chráněnou instanci (počítač/úloha) | 9999.
Čas vypršení platnosti maximální pro bod obnovení | Bez omezení.
Maximální frekvence zálohování do trezoru (rozšíření virtuálního počítače Azure) | Jednou za den.
Maximální frekvence zálohování do trezoru (agenta MARS) | Tři zálohy za den.
Maximální frekvence zálohování do DPM nebo MABS | Každých 15 minut pro SQL Server.<br/><br/> Jednou za hodinu pro ostatní úlohy.
Uchování bodu obnovení | Denní, týdenní, měsíční a roční.
Maximální doba uchovávání | Závisí na četnosti zálohování.
Body obnovení na disku aplikace DPM nebo MABS | 64 pro souborové servery a 448 pro servery aplikací.<br/><br/> Body obnovení pásky neomezené pro místní aplikace DPM.

## <a name="supported-restore-methods"></a>Podporované metody

**Obnovení – metoda** | **Podrobnosti**
--- | ---
Vytvoření nového virtuálního počítače | Vytvoření virtuálního počítače během procesu obnovení. <br/><br/> Tato možnost získá základní virtuální počítač zprovozněný. Můžete určit virtuální počítač název, skupinu prostředků, virtuální síť, podsíť a úložiště.  
Obnovení disku | Můžete obnovit disk a použijte ji k vytvoření virtuálního počítače.<br/><br/> Když vyberete tuto možnost, Azure Backup kopíruje data z trezoru na účet úložiště, kterou jste vybrali. Úloha obnovení vytvoří šablonu. Stáhněte si tuto šablonu, použijte k určení vlastní nastavení virtuálního počítače a vytvoření virtuálního počítače.<br/><br/> Tato možnost umožňuje zadat další nastavení, která předchozí způsob vytvoření virtuálního počítače.<br/><br/>
Nahrazení existujícího disku | Můžete obnovit disk a potom použít obnoveného disku Výměna disku, který je připojený virtuální počítač.
Obnovení souborů | Obnovení souborů z zvolený bod obnovení. Stažení skriptu pro připojení disku virtuálního počítače z bodu obnovení. Potom procházet diskové svazky k vyhledání souborů a složek, kterou chcete obnovit a až budete hotovi, odpojte disk.

## <a name="support-for-file-level-restore"></a>Podpora pro obnovení na úrovni souboru

**Obnovení** | **Podporuje se**
--- | ---
Obnovení souborů v operačních systémech | Můžete obnovit soubory na jakýkoli počítač, který má stejné (nebo kompatibilní) operačního systému jako zálohování virtuálního počítače. Zobrazit [tabulky kompatibilní operační systém](backup-azure-restore-files-from-vm.md#system-requirements).
Obnovení souborů na klasické virtuální počítače | Nepodporuje se.
Obnovení souborů ze šifrovaných virtuálních počítačů | Nepodporuje se.
Obnovení souborů ze sítě síťově omezené účty úložiště | Nepodporuje se.
Obnovení souborů na virtuálních počítačích pomocí prostorů úložiště systému Windows | Obnovení není podporováno ve stejném virtuálním počítači.<br/><br/> Místo toho obnovení souborů na kompatibilní virtuální počítače.
Obnovení souborů na virtuální počítač s Linuxem pomocí pole LVM/diskového pole raid | Obnovení není podporováno ve stejném virtuálním počítači.<br/><br/> Obnovte na kompatibilní virtuální počítače.
Obnovení souborů s nastavením speciální sítě | Obnovení není podporováno ve stejném virtuálním počítači. <br/><br/> Obnovte na kompatibilní virtuální počítače.

## <a name="support-for-vm-management"></a>Podpora pro správu virtuálních počítačů

Následující tabulka shrnuje podporu pro zálohování během úlohy správy virtuálních počítačů, jako je například přidání nebo výměna disky virtuálních počítačů.

**Obnovení** | **Podporuje se**
--- | ---
Obnovení mezi předplatné/oblasti a zóny. | Nepodporuje se.
Obnovit do existujícího virtuálního počítače | Použijte možnost nahradit disku.
Obnovení disku s účtem úložiště, které jsou povolené pro Azure Storage Service Encryption (SSE) | Nepodporuje se.<br/><br/> Obnovte účet, který nemá povolené SSE.
Obnovení k účtům hybridní úložiště | Nepodporuje se.<br/><br/> Podle typu účtu úložiště, všechny obnovených disků bude premium nebo standard a ne smíšené.
Obnovení do účtu úložiště s využitím zónově redundantní úložiště (ZRS) | Nepodporuje se.
Obnovení virtuálního počítače přímo do skupiny dostupnosti | Za spravované disky můžete obnovit disk a použijte možnost sady dostupnosti v šabloně.<br/><br/> Není podporováno pro nespravované disky. Pro nespravované disky obnovení disku a pak vytvořte virtuální počítač ve skupině dostupnosti.
Obnovit zálohování nespravované virtuální počítače po upgradu na spravovaných virtuálních počítačů| Podporuje se.<br/><br/> Můžete obnovit disky a pak vytvořit spravovaný virtuální počítač.
Obnovení virtuálního počítače pro bod obnovení před migrací virtuálního počítače na managed disks | Podporuje se.<br/><br/> Obnovit do nespravované disky (výchozí), převést na spravovaný disk obnovených disků a vytvoření virtuálního počítače se spravovanými disky.
Obnovení virtuálního počítače, který je odstraněný. | Podporuje se.<br/><br/> Virtuální počítač můžete obnovit z bodu obnovení.
Obnovení řadiče domény (DC) virtuální počítač, který je součástí konfigurace s více řadiči domény prostřednictvím portálu | Podporované Pokud obnovení disku a vytvoření virtuálního počítače pomocí prostředí PowerShell.
Obnovit virtuální počítač v jiné virtuální sítě |   Podporuje se.<br/><br/> Virtuální síť musí být ve stejném předplatném a oblasti.

## <a name="vm-compute-support"></a>Podpora výpočetních virtuálních počítačů

**Compute** | **Podpora**
--- | ---
Velikost virtuálního počítače |   Libovolné velikosti virtuálního počítače Azure s nejméně 2 jádra procesoru a 1 GB paměti RAM.<br/><br/> [Další informace](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)
Zálohování virtuálních počítačů v [skupiny dostupnosti](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability#availability-sets) | Podporuje se.<br/><br/> Virtuální počítač do dostupné nastavení nelze obnovit pomocí možnosti rychlé vytvoření virtuálního počítače. Místo toho při obnovování virtuálního počítače obnovení disku a jeho použití k nasazení virtuálního počítače, nebo obnovení disku a použít k nahrazení existujícího disku.
Zálohování virtuálních počítačů v [zóny dostupnosti](https://docs.microsoft.com/azure/availability-zones/az-overview) |  Nepodporuje se.
Zálohování virtuálních počítačů, které jsou nasazeny pomocí [výhody použití hybridních (HUB)](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) | Podporuje se.
Zálohování virtuálních počítačů, které jsou nasazené [škálovací sady](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview) |  Nepodporuje se.
Zálohování virtuálních počítačů, které byly nasazeny [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?filters=virtual-machine-images)<br/><br/> (Vydáno nakladatelstvím Microsoft třetích stran) |  Podporuje se.<br/><br/> Virtuální počítač musí být spuštěný podporovaný operační systém.<br/><br/> Při obnovení souborů na virtuálním počítači, můžete obnovit pouze na kompatibilní operační systém (ne starší nebo novější operační systém).
Zálohování virtuálních počítačů nasazených z vlastní image (třetích stran) |   Podporuje se.<br/><br/> Virtuální počítač musí být spuštěný podporovaný operační systém.<br/><br/> Při obnovení souborů na virtuálním počítači, můžete obnovit pouze na kompatibilní operační systém (ne starší nebo novější operační systém).
Zálohování virtuálních počítačů, které se migrují do Azure  | Podporuje se.<br/><br/> Zálohování virtuálního počítače, musí nainstalovat agenta virtuálního počítače na migrovaných počítačů.



## <a name="vm-storage-support"></a>Podpora úložiště virtuálního počítače

**Komponenta** | **Podpora**
--- | ---
Azure datových disků virtuálního počítače | Zálohování virtuálního počítače s 16 nebo méně datových disků.
Velikost datového disku | Jednotlivé disk může být až 4 095 GB.<br/><br/> Pokud vaše trezory běží nejnovější verze služby Azure Backup (označuje se jako rychlé obnovení), disk o velikosti až 4 TB jsou podporovány. [Další informace](backup-instant-restore-capability.md).
Typ úložiště | Standardní HDD, SSD na úrovni standard, premium SSD. <br/><br/> SSD na úrovni standard se podporuje, pokud vaše trezory služby jsou upgradovány na nejnovější verzi nástroje zálohování virtuálních počítačů Azure (označují se termínem rychlé obnovení). [Další informace](backup-instant-restore-capability.md).
Managed Disks | Podporuje se.
Šifrovanými disky | Podporuje se.<br/><br/> Virtuální počítače Azure povolené s Azure Disk Encryption je možné zálohovat (s nebo bez aplikace Azure AD).<br/><br/> Šifrované virtuální počítače nelze obnovit na úrovni souboru nebo složky. Je nutné obnovit celý virtuální počítač.<br/><br/> Můžete povolit šifrování na virtuálních počítačích, které jsou již chráněny službou Azure Backup.
Disky s akcelerátor zápisu | Nepodporuje se.<br/><br/> Pokud používáte nejnovější verzi zálohování virtuálních počítačů Azure (označované jako [rychlé obnovení](backup-instant-restore-capability.md)), můžete vyloučit disky s akcelerátor zápisu ze zálohy.
Zálohování disků s odstraněním duplicit | Nepodporuje se.
Přidání disku do chráněného virtuálního počítače | Podporuje se.
Změna velikosti disku v chráněném virtuálním počítači | Podporuje se.

## <a name="vm-network-support"></a>Podpora sítě virtuálních počítačů


**Komponenta** | **Podpora**
--- | ---
Počet síťových rozhraní (NIC) | Až do maximální počet síťových adaptérů pro určité velikosti virtuálních počítačů Azure nepodporuje.<br/><br/> Síťové adaptéry jsou vytvořeny při vytvoření virtuálního počítače během procesu obnovení.<br/><br/> Počet síťových adaptérů v obnoveném virtuálním počítači zrcadlí počet síťových adaptérů na virtuálním počítači při povolování ochrany. Po povolení ochrany odebrání síťových rozhraní nemá vliv na počet.
Externí/interní služby load balancer |   Podporuje se. <br/><br/> [Další informace](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) o obnovení virtuálních počítačů s nastavením speciální sítě.
Víc vyhrazených IP adres |    Podporuje se. <br/><br/> [Další informace](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) o obnovení virtuálních počítačů s nastavením speciální sítě.
Virtuální počítače s více síťovými adaptéry  | Podporuje se. <br/><br/> [Další informace](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) o obnovení virtuálních počítačů s nastavením speciální sítě.
Virtuální počítače s veřejnými IP adresami    | Podporuje se.<br/><br/> Přidružit existující veřejné IP adresy k síťovému rozhraní, nebo vytvořit adresu a přidružte jej k síťovému rozhraní po dokončení obnovení.
Skupina zabezpečení sítě (NSG) na síťovou kartu a podsítě. |   Podporuje se.
Vyhrazená IP adresa (staticky) | Nepodporuje se.<br/><br/> Nelze zálohovat virtuální počítač s vyhrazenou IP adresu a není definovaný koncový bod.
Dynamická IP adresa |    Podporuje se.<br/><br/> Pokud síťové karty na zdroji virtuální počítač používá dynamické přidělování IP adres, ve výchozím nastavení síťového rozhraní na obnovený virtuální počítač bude používat ho příliš.
Azure Traffic Manager   | Podporuje se.<br/><br/>Pokud je zálohování virtuálního počítače je v Traffic Manageru, přidejte do stejné instance Traffic Manageru obnovený virtuální počítač.
Azure DNS | Podporuje se.
Vlastní DNS |    Podporuje se.
Odchozí připojení přes server proxy protokolu HTTP | Podporuje se.<br/><br/> Ověřený proxy server nepodporuje.
Koncové body služby virtuální sítě   | Podporuje se.<br/><br/> Brány firewall a nastavení účtu úložiště virtuální sítě by měly umožnit přístup ze všech sítí.



## <a name="vm-security-and-encryption-support"></a>Podpora šifrování a zabezpečení virtuálních počítačů

Azure Backup podporuje šifrování přenášených i uložených dat:

Síťový provoz do Azure:

- Provoz zálohování z serverů do trezoru služby Recovery Services je šifrován pomocí rozšířené šifrování Standard 256.
- Zálohovaná data se odesílají prostřednictvím zabezpečeného spojení HTTPS.
- Zálohovaná data se ukládají do trezoru služby Recovery Services v šifrovaném tvaru.
- Můžete mít jenom heslo k odemknutí těchto dat. Microsoft nelze dešifrovat data záloh a kdykoli.

  > [!WARNING]
  > Po nastavení trezoru pouze máte k šifrovacímu klíči přístup. Microsoft nikdy udržuje kopii a nemá přístup ke klíči. Pokud dojde ke ztrátě klíče, Microsoft nemůže zálohovaná data obnovit.

Zabezpečení dat:

- Při zálohování virtuálních počítačů Azure, budete muset nastavit šifrování *v rámci* virtuálního počítače.
- Azure Backup podporuje Azure Disk Encryption, která používá nástroj BitLocker na Windows virtual machines a nám **dm-crypt** na virtuální počítače s Linuxem.
- Na straně back-endu Azure Backup používá [šifrování služby Azure Storage](../storage/common/storage-service-encryption.md), které chrání neaktivní uložená data.


**Počítač** | **Při přenosu** | **V klidovém stavu**
--- | --- | ---
Místní počítače Windows bez DPM nebo MABS | ![Ano][green] | ![Ano][green]
Virtuální počítače Azure | ![Ano][green] | ![Ano][green]
Virtuální počítače na místní nebo Azure pomocí aplikace DPM | ![Ano][green] | ![Ano][green]
Na – místní nebo virtuální počítače Azure s MABS | ![Ano][green] | ![Ano][green]



## <a name="vm-compression-support"></a>Podpora komprese virtuálních počítačů

Backup podporuje kompresi provoz zálohování dle souhrnu v následující tabulce. Je třeba počítat s následujícím:

- Pro virtuální počítače Azure rozšíření virtuálního počítače čte data přímo z účtu úložiště Azure přes síť úložiště. Není potřeba tento provoz komprimovat.
- Pokud používáte DPM nebo MABS, můžete šetřit šířku pásma pomocí komprese dat před se zálohováním do DPM nebo MABS.

**Počítač** | **Komprimovat MABS/DPM (TCP)** | **Zkomprimujte do trezoru (HTTPS)**
--- | --- | ---
Místní počítače Windows bez DPM nebo MABS | Není k dispozici | ![Ano][green]
Virtuální počítače Azure | Není k dispozici | Není k dispozici
Virtuální počítače na místní nebo Azure pomocí aplikace DPM | ![Ano][green] | ![Ano][green]
Na – místní nebo virtuální počítače Azure s MABS | ![Ano][green] | ![Ano][green]


## <a name="next-steps"></a>Další postup

- [Zálohování virtuálních počítačů Azure](backup-azure-arm-vms-prepare.md).
- [Zálohování počítačů Windows přímo](tutorial-backup-windows-server-to-azure.md), bez zálohování serveru.
- [Nastavit MABS](backup-azure-microsoft-azure-backup.md) pro zálohování do Azure a pak znovu úloh rozšiřování MABS.
- [Nastavení DPM](backup-azure-dpm-introduction.md) pro zálohování do Azure a pak zálohování úloh DPM.

[green]: ./media/backup-support-matrix/green.png
[yellow]: ./media/backup-support-matrix/yellow.png
[red]: ./media/backup-support-matrix/red.png
