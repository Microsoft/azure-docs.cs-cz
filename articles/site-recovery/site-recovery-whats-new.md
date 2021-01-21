---
title: Co je nového v Azure Site Recovery
description: Obsahuje souhrn nových funkcí a nejnovější aktualizace služby Azure Site Recovery.
ms.topic: conceptual
ms.date: 01/20/2021
ms.openlocfilehash: 2778f1af99512c4594d2d688eea7e04b3b81ae47
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621756"
---
# <a name="whats-new-in-site-recovery"></a>Co je nového ve službě Site Recovery

Služba [Azure Site Recovery](site-recovery-overview.md) se průběžně aktualizuje a vylepšuje. V tomto článku získáte informace o nejnovějších vydáních, nových funkcích a novém obsahu. Tato stránka je pravidelně aktualizována.

V kanálu [aktualizací Azure](https://azure.microsoft.com/updates/?product=site-recovery) můžete postupovat a přihlásit se k odběru oznámení o Site Recovery aktualizací.

## <a name="supported-updates"></a>Podporované aktualizace

Pro Site Recovery komponenty podporujeme N-4 verze, kde N je nejnovější vydaná verze. V následující tabulce je jejich přehled.

**Aktualizace** |  **Sjednocené nastavení** | **Vajíčka konfiguračního serveru** | **Agent služby mobility** | **Poskytovatel Site Recovery** | **Agent Recovery Services**
--- | --- | --- | --- | --- | ---
[Kumulativní aktualizace 53](https://support.microsoft.com/help/4602881/)  | 9.40.5850.1 | 5.1.6537.0 | 9.40.5850.1 | 5.1.6537.0  | 2.0.9202.0
[Kumulativní aktualizace 52](https://support.microsoft.com/help/4597409/)  | 9.39.5796.1 | 5.1.6458.0 | 9.39.5796.1 | 5.1.6458.0  | 2.0.9196.0
[Kumulativní aktualizace 51](https://support.microsoft.com/help/4590304)  | 9.38.5761.1 | 5.1.6400.0 | 9.38.5761.1 | 5.1.6400.0  | 2.0.9193.0
[Kumulativní aktualizace 50](https://support.microsoft.com/help/4582666/) | 9.37.5724.1 | 5.1.6347.0 | 9.37.5724.1 | 5.1.6347.0  | 2.0.9192.0
[Kumulativní aktualizace 49](https://support.microsoft.com/help/4578241/) | 9.36.5696.1 | 5.1.6315.0 | 9.36.5696.1 | 5.1.6315.0 | 2.0.9188.0


[Přečtěte si další informace](service-updates-how-to.md) o instalaci a podpoře aktualizací.

## <a name="updates-january-2021"></a>Aktualizace (leden 2021)

### <a name="update-rollup-53"></a>Kumulativní aktualizace 53

[Kumulativní aktualizace 53](https://support.microsoft.com/help/4602881/update-rollup-53-for-azure-site-recovery) poskytuje následující aktualizace:

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace Site Recovery agentů a zprostředkovatelů, jak je popsáno v souhrnu.
**Opravy a vylepšení problémů** | Řada oprav a vylepšení, jak je popsáno v souhrnu. 
**Zotavení po havárii virtuálního počítače Azure** | Replikace pro značky je teď podporovaná. Všechny značky přidané do virtuálních počítačů Azure, disků a síťových karet ve zdrojové oblasti se replikují do počítačů v cílové oblasti.<br/><br/> Virtuální počítače Azure s Debian 10 se teď podporují pro replikaci.
**Virtuální počítač VMware/fyzické zotavení po havárii do Azure** | Aktualizace zahrnuje vylepšení protokolování pro replikaci virtuálních počítačů VMware do Azure a vylepšené zasílání zpráv o chybách.<br/><br/> Virtuální počítače VMware a fyzické počítače s Debian 10 se teď podporují pro replikaci.


## <a name="updates-november-2020"></a>Aktualizace (listopad 2020)

### <a name="update-rollup-52"></a>Kumulativní aktualizace 52

[Kumulativní aktualizace 52](https://support.microsoft.com/help/4597409/update-rollup-52-for-azure-site-recovery) poskytuje následující aktualizace:

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace Site Recovery agentů a zprostředkovatelů, jak je popsáno v souhrnu.
**Opravy a vylepšení problémů** | Řada oprav a vylepšení, jak je popsáno v souhrnu, včetně nové podpory Linux pro službu mobility.
**Zotavení po havárii virtuálního počítače Azure** | Nyní podporováno pro virtuální počítače se systémem RHEL 8,3 a Oracle Linux 7,9
**Virtuální počítač VMware/fyzické zotavení po havárii do Azure** | Nyní se podporuje pro virtuální počítače se systémem RHEL 8,3, Oracle Linux 7.9/8.3.

## <a name="updates-october-2020"></a>Aktualizace (říjen 2020)

### <a name="update-rollup-51"></a>Kumulativní aktualizace 51

[Kumulativní aktualizace 51](https://support.microsoft.com/help/4590304/update-rollup-51-for-azure-site-recovery) poskytuje následující aktualizace:

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace Site Recovery agentů a zprostředkovatelů, jak je popsáno v souhrnu.
**Opravy a vylepšení problémů** | Řada oprav a vylepšení, jak je popsáno v souhrnu, včetně nové podpory Linux pro službu mobility.

## <a name="updates-september-2020"></a>Aktualizace (září 2020)

### <a name="update-rollup-50"></a>Kumulativní aktualizace 50

[Kumulativní aktualizace 50](https://support.microsoft.com/help/4582666/update-rollup-50-for-azure-site-recovery) poskytuje následující aktualizace:

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace Site Recovery agentů a zprostředkovatelů, jak je popsáno v souhrnu.
**Opravy a vylepšení problémů** | Řada oprav a vylepšení, jak je popsáno v souhrnu.

## <a name="updates-august-2020"></a>Aktualizace (srpen 2020)

### <a name="update-rollup-49"></a>Kumulativní aktualizace 49

[Kumulativní aktualizace 49](https://support.microsoft.com/help/4578241/update-rollup-49-for-azure-site-recovery) poskytuje následující aktualizace:

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace Site Recovery agentů a zprostředkovatelů, jak je popsáno v souhrnu.
**Opravy a vylepšení problémů** | Řada oprav a vylepšení, jak je popsáno v souhrnu, včetně nové podpory Linux pro službu mobility.

## <a name="updates-july-2020"></a>Aktualizace (červenec 2020)

### <a name="update-rollup-48"></a>Kumulativní aktualizace 48

[Kumulativní aktualizace 48](https://support.microsoft.com/help/4573888/update-rollup-48-for-azure-site-recovery) poskytuje následující aktualizace:

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace Site Recovery agentů a zprostředkovatelů, jak je popsáno v souhrnu.
**Opravy a vylepšení problémů** | Řada oprav a vylepšení, jak je popsáno v souhrnu.

> [!NOTE]
> Kumulativní aktualizace 48 obsahuje známý problém s povolením replikace pro počítače se systémem Linux zašifrované pomocí ADE. [Přečtěte si další informace](./azure-to-azure-troubleshoot-errors.md#enable-protection-failed-as-the-installer-is-unable-to-find-the-root-disk-error-code-151137).

### <a name="update-rollup-47"></a>Kumulativní aktualizace 47

[Kumulativní aktualizace 47](https://support.microsoft.com/help/4570609/update-rollup-47-for-azure-site-recovery) poskytuje následující aktualizace:

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace Site Recovery agentů a zprostředkovatelů, jak je popsáno v souhrnu.
**Opravy a vylepšení problémů** | Řada oprav a vylepšení, jak je popsáno v souhrnu.

## <a name="updates-june-2020"></a>Aktualizace (červen 2020)

### <a name="update-rollup-46"></a>Kumulativní aktualizace 46

[Kumulativní aktualizace 46](https://support.microsoft.com/help/4564347/update-rollup-46-for-azure-site-recovery) poskytuje následující aktualizace:

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace Site Recovery agentů a zprostředkovatelů, jak je popsáno v souhrnu.
**Opravy a vylepšení problémů** | Řada oprav a vylepšení, jak je popsáno v souhrnu.

## <a name="updates-march-2020"></a>Aktualizace (březen 2020)

### <a name="update-rollup-45"></a>Kumulativní aktualizace 45

[Kumulativní aktualizace 45](https://support.microsoft.com/help/4550047/update-rollup-45-for-azure-site-recovery) poskytuje následující aktualizace:

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace Site Recovery agentů a zprostředkovatelů, jak je popsáno v souhrnu.
**Opravy a vylepšení problémů** | Řada oprav a vylepšení, jak je popsáno v souhrnu.

## <a name="updates-january-2020"></a>Aktualizace (leden 2020)

### <a name="update-rollup-44"></a>Kumulativní aktualizace 44

[Kumulativní aktualizace 44](https://support.microsoft.com/help/4538187/update-rollup-44-for-azure-site-recovery) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Neexistovaly žádné aktualizace pro poskytovatele Site Recovery a agenty.
**Opravy a vylepšení problémů** | Řada oprav a vylepšení, jak je popsáno v souhrnu.

### <a name="azure-vmware-disaster-recovery"></a>Zotavení po havárii Azure VMware

Virtuální počítače Azure teď podporují virtuální počítače, které umožňují šifrování v klidovém případě pomocí klíčů spravovaných zákazníkem. [Přečtěte si další informace](azure-to-azure-how-to-enable-replication-cmk-disks.md).


### <a name="update-rollup-43"></a>Kumulativní aktualizace 43

[Kumulativní aktualizace 43](https://support.microsoft.com/help/4537047/update-rollup-43-for-azure-site-recovery) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace Site Recovery agentů a zprostředkovatelů (jak je popsáno v souhrnu)
**Opravy a vylepšení problémů** | Řada oprav a vylepšení (jak je popsáno v souhrnu)


## <a name="updates-november-2019"></a>Aktualizace (listopad 2019)

### <a name="update-rollup-42"></a>Kumulativní aktualizace 42

[Kumulativní aktualizace 42](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace Site Recovery agentů a zprostředkovatelů (jak je popsáno v souhrnu)
**Opravy a vylepšení problémů** | Řada oprav a vylepšení (jak je popsáno v souhrnu)


### <a name="azure-vm-disaster-recovery"></a>Zotavení po havárii virtuálního počítače Azure

Nové funkce pro zotavení po havárii virtuálních počítačů Azure jsou shrnuté v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
**UEFI** | Site Recovery teď podporuje zotavení po havárii pro virtuální počítače Azure pomocí architektury spouštění založené na rozhraní UEFI.
**Linux** | Site Recovery teď podporuje virtuální počítače Azure se systémem Linux s Azure Disk Encryption (ADE).
**2. generace** | Všechny virtuální počítače Azure generace 2 se teď podporují pro zotavení po havárii.
**Oblasti** | Teď můžete povolit zotavení po havárii pro virtuální počítače Azure v geograficky Norsku.

### <a name="vmware-to-azure-disaster-recovery"></a>Zotavení po havárii VMware do Azure

Nové funkce pro zotavení po havárii z VMware do Azure jsou shrnuté v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
**UEFI** | Site Recovery teď podporuje zotavení po havárii pro virtuální počítače VMware pomocí architektury spouštění založené na rozhraní UEFI.<br/><br/> Mezi podporované operační systémy patří Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, SLES 12 SP4, RHEL 8.

## <a name="update-to-servicing-stack-updatesha-2"></a>Aktualizace pro údržbu aktualizací zásobníku/SHA-2

Pro zotavení po havárii virtuálních počítačů Azure do sekundární oblasti nebo místních virtuálních počítačů VMware nebo fyzických serverů do Azure Pamatujte na toto:

- Z verze 9.30.5407.1 rozšíření služby mobility (pro virtuální počítače Azure) a agenta služby mobility (pro VMware/fyzické počítače) musí některé operační systémy počítače používat aktualizaci zásobníku údržby a SHA-2. Podrobnosti jsou uvedeny v následující tabulce.
- Nainstalujte aktualizaci a SHA-2 v souladu s propojenou znalostí. SHA-1 se od září 2019 nepodporuje a pokud podepisování kódu SHA-2 není povolené, rozšíření agenta se nenainstaluje nebo upgraduje podle očekávání.
- Přečtěte si další informace o [upgradu a požadavcích SHA-2](https://aka.ms/SHA-2KB).

**Operační systém** | **Virtuální počítač Azure** | **VIRTUÁLNÍ počítač VMware/fyzický počítač**
--- | --- | ---
**Windows 2008 R2 SP1** | [Aktualizace servisního zásobníku](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [Aktualizace servisního zásobníku](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)
**Windows 2008 SP2** | [Aktualizace servisního zásobníku](https://support.microsoft.com/help/4493730)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [Aktualizace servisního zásobníku](https://support.microsoft.com/help/4493730)<br/> [SHA-2](https://support.microsoft.com/help/4474419)
**Windows 7 SP1** | [Aktualizace servisního zásobníku](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [Aktualizace servisního zásobníku](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419).



## <a name="updates-october-2019"></a>Aktualizace (říjen 2019)

### <a name="update-rollup-41"></a>Kumulativní aktualizace 41

[Kumulativní aktualizace 41](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace Site Recovery agentů a zprostředkovatelů (jak je popsáno v souhrnu)
**Opravy a vylepšení problémů** | Řada oprav a vylepšení (jak je popsáno v souhrnu)



### <a name="azure-vm-disaster-recovery"></a>Zotavení po havárii virtuálního počítače Azure

Nové funkce pro zotavení po havárii virtuálních počítačů Azure jsou shrnuté v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
**Nastavení testovacího převzetí služeb při selhání** | Při nastavování testovacího převzetí služeb při selhání teď můžete nakonfigurovat nastavení pro virtuální počítač a síť testovacího převzetí služeb při selhání, včetně IP adresy, NSG, interního vyrovnávání zatížení a veřejné IP adresy pro každou síťovou kartu počítače. Tato nastavení jsou volitelná a nemění aktuální chování. Pokud tato nastavení nenakonfigurujete, můžete v době testování převzetí služeb při selhání zvolit virtuální síť Azure. [Přečtěte si další informace](https://azure.microsoft.com/blog/customize-networking-for-dr-drills-azure-site-recovery/).
**Plány obnovení** | Plány obnovení jsou teď omezené na 100 virtuálních počítačů, aby se zajistila spolehlivost převzetí služeb při selhání.

### <a name="vmware-to-azure-disaster-recovery"></a>Zotavení po havárii VMware do Azure

Nové funkce pro zotavení po havárii z VMware do Azure jsou shrnuté v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
**Plány obnovení** | Plány obnovení jsou teď omezené na 100 virtuálních počítačů, aby se zajistila spolehlivost převzetí služeb při selhání.


## <a name="updates-september-2019"></a>Aktualizace (září 2019)

### <a name="update-rollup-40"></a>Kumulativní aktualizace 40

[Kumulativní aktualizace 40](https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace Site Recovery agentů a zprostředkovatelů (jak je popsáno v souhrnu)
**Opravy a vylepšení problémů** | Řada oprav a vylepšení (jak je popsáno v souhrnu)




### <a name="azure-vm-disaster-recovery"></a>Zotavení po havárii virtuálního počítače Azure

Nové funkce pro zotavení po havárii virtuálních počítačů Azure jsou shrnuté v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
**Vyčištění po navrácení služeb po obnovení** | Po převzetí služeb při selhání sekundárním Azure a následné navrácení služeb po obnovení do primární oblasti Site Recovery automaticky vyčistí počítače v sekundární oblasti. Nemusíte ručně odstraňovat virtuální počítače a síťové karty.
**Testovací převzetí služeb při selhání zachovává IP adresu** | IP adresu zdrojového virtuálního počítače teď můžete zachovat během postupu zotavení po havárii a vybrat statickou IP adresu testovacího převzetí služeb při selhání.

### <a name="vmwarephysical-server-disaster-recovery"></a>Zotavení po havárii VMware/fyzický server

Funkce přidané v tomto měsíci jsou shrnuté v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
Výstrahy nového procesového serveru | Přidali jsme nové výstrahy procesového serveru. [Přečtěte si další informace](vmware-physical-azure-monitor-process-server.md).

### <a name="hyper-v-disaster-recovery"></a>Zotavení po havárii technologie Hyper-V

Funkce přidané v tomto měsíci jsou shrnuté v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
Účet úložiště | Site Recovery teď podporuje použití účtů úložiště s bránou firewall povolenou pro zotavení po havárii Hyper-V do Azure.  Můžete vybrat účty úložiště s podporou brány firewall jako cílový účet nebo úložiště mezipaměti. Pokud používáte účet s povolenou bránou firewall, ujistěte se, že jste povolili možnost Povolit důvěryhodné služby společnosti Microsoft.<br/><br/> To je podporováno pro virtuální počítače Hyper-V s nástrojem System Center VMM nebo bez něj.


## <a name="updates-august-2019"></a>Aktualizace (srpen 2019)

### <a name="update-rollup-39"></a>Kumulativní aktualizace 39

[Kumulativní aktualizace 39](https://support.microsoft.com/help/4517283/update-rollup-39-for-azure-site-recovery) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace Site Recovery agentů a zprostředkovatelů (jak je popsáno v souhrnu)
**Opravy a vylepšení problémů** | Řada oprav a vylepšení (jak je popsáno v souhrnu)


### <a name="azure-vm-disaster-recovery"></a>Zotavení po havárii virtuálního počítače Azure

Nové funkce pro zotavení po havárii virtuálních počítačů Azure jsou shrnuté v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
**Šifrování bez služby Azure AD** | Šifrování bez aplikace Azure AD se teď podporuje pro replikaci virtuálních počítačů Azure na spravované disky se systémem Windows.
**Síťové prostředky pro převzetí služeb při selhání** | Při převzetí služeb při selhání do jiné oblasti teď můžete k virtuálnímu počítači připojit nastavení síťových prostředků (skupin zabezpečení sítě, Vyrovnávání zatížení, veřejné IP adresy).

## <a name="updates-july-2019"></a>Aktualizace (červenec 2019)

### <a name="update-rollup-38"></a>Kumulativní aktualizace 38

[Kumulativní aktualizace 38](https://support.microsoft.com/help/4513507/) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace Site Recovery agentů a zprostředkovatelů (jak je popsáno v souhrnu)
**Opravy a vylepšení problémů** | Řada oprav a vylepšení (jak je popsáno v souhrnu)


### <a name="general"></a>Obecné

Site Recovery teď podporuje použití účtů úložiště pro obecné účely v2 pro úložiště mezipaměti nebo cílové úložiště. Dřív byla podporovaná jenom verze V1.

### <a name="vmware-to-azure-disaster-recovery"></a>Zotavení po havárii VMware do Azure

Při replikaci virtuálního počítače Azure se spravovanými disky teď můžete replikovat disky až do 8 TB.


## <a name="updates-june-2019"></a>Aktualizace (červen 2019)

### <a name="update-rollup-37"></a>Kumulativní aktualizace 37

[Kumulativní aktualizace 37](https://support.microsoft.com/help/4508614/) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace Site Recovery agentů a zprostředkovatelů (jak je popsáno v souhrnu)
**Opravy a vylepšení problémů** | Řada oprav a vylepšení (jak je popsáno v souhrnu)


### <a name="vmwarephysical-server-disaster-recovery"></a>Zotavení po havárii VMware/fyzický server

Funkce přidané v tomto měsíci jsou shrnuté v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
**Oddíly GPT** | Od kumulativní aktualizace 37 a vyšší (služba mobility verze 9.25.5241.1) je v rozhraní UEFI podporováno až pět oddílů GPT. Před touto aktualizací se podporovaly čtyři.



## <a name="updates-may-2019"></a>Aktualizace (květen 2019)

### <a name="update-rollup-36"></a>Kumulativní aktualizace 36

[Kumulativní aktualizace 36](https://support.microsoft.com/help/4503156) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace Site Recovery agentů a zprostředkovatelů (jak je popsáno v souhrnu)
**Opravy a vylepšení problémů** | Řada oprav a vylepšení (jak je popsáno v souhrnu)

### <a name="azure-vm-disaster-recovery"></a>Zotavení po havárii virtuálního počítače Azure

Funkce přidané v tomto měsíci jsou shrnuté v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
**Replikace přidaných disků** | Povolte replikaci datových disků přidaných do virtuálního počítače Azure, který je už povolený pro zotavení po havárii. [Přečtěte si další informace](azure-to-azure-enable-replication-added-disk.md).
**Automatické aktualizace** | Při konfiguraci automatických aktualizací pro rozšíření služby mobility, které běží na virtuálních počítačích Azure povolených pro zotavení po havárii, teď můžete vybrat existující účet Automation, který se má použít, místo použití výchozího účtu vytvořeného nástrojem Site Recovery. [Přečtěte si další informace](azure-to-azure-autoupdate.md).


### <a name="vmwarephysical-server-disaster-recovery"></a>Zotavení po havárii VMware/fyzický server

Funkce přidané v tomto měsíci jsou shrnuté v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
**Monitorování procesového serveru** | V případě zotavení po havárii místních virtuálních počítačů VMware a fyzických serverů můžete monitorovat a řešit potíže s procesovým serverem pomocí vylepšeného generování sestav a upozornění na stav serveru. [Přečtěte si další informace](vmware-physical-azure-monitor-process-server.md).





## <a name="updates-march-2019"></a>Aktualizace (březen 2019)

### <a name="update-rollup-35"></a>Kumulativní aktualizace 35

[Kumulativní aktualizace 35](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace Site Recovery agentů a zprostředkovatelů (jak je popsáno v souhrnu)
**Opravy a vylepšení problémů** | Řada oprav a vylepšení (jak je popsáno v souhrnu)

### <a name="vmwarephysical-server-disaster-recovery"></a>Zotavení po havárii VMware/fyzický server

Funkce přidané v tomto měsíci jsou shrnuté v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
**Spravované disky** | Replikace místních virtuálních počítačů VMware a fyzických serverů se teď přímo na spravované disky v Azure. Místní data se odesílají do účtu úložiště mezipaměti v Azure a body obnovení se vytvoří ve službě Managed disks v cílovém umístění. Tím zajistíte, že nepotřebujete spravovat víc cílových účtů úložiště.
**Konfigurační server** | Site Recovery teď podporuje konfigurační servery s více síťovými kartami. Před registrací konfiguračního serveru v trezoru přidejte další adaptéry k virtuálnímu počítači konfiguračního serveru. Pokud přidáte později, budete muset Server v trezoru znovu zaregistrovat.


## <a name="updates-february-2019"></a>Aktualizace (únor 2019)

### <a name="update-rollup-34"></a>Kumulativní aktualizace 34

[Kumulativní aktualizace 34](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace Site Recovery agentů a zprostředkovatelů (jak je popsáno v souhrnu).
**Opravy a vylepšení problémů** | Řada oprav a vylepšení (jak je popsáno v souhrnu).


### <a name="update-rollup-33"></a>Kumulativní aktualizace 33

[Kumulativní aktualizace 33](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace Site Recovery agentů a zprostředkovatelů (jak je popsáno v souhrnu).
**Opravy a vylepšení problémů** | Řada oprav a vylepšení (jak je popsáno v souhrnu).


### <a name="azure-vm-disaster-recovery"></a>Zotavení po havárii virtuálního počítače Azure

Funkce přidané v tomto měsíci jsou shrnuté v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
**Mapování sítě** | Pro zotavení po havárii virtuálního počítače Azure teď můžete použít jakoukoli dostupnou cílovou síť, když povolíte replikaci.
**SSD úrovně Standard** | Nyní můžete nastavit zotavení po havárii pro virtuální počítače Azure pomocí [SSD úrovně Standard disků](../virtual-machines/disks-types.md#standard-ssd).
**Prostory úložiště s přímým přístupem** | Zotavení po havárii pro aplikace běžící na Azure VM Apps můžete nastavit pomocí [prostory úložiště s přímým přístupem](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) pro vysokou dostupnost.  Použití Prostory úložiště s přímým přístupem (S2D) společně s Site Recovery poskytuje komplexní ochranu úloh virtuálních počítačů Azure. S2D umožňuje hostovat hostovaný cluster v Azure. To je užitečné hlavně v případě, že virtuální počítač je hostitelem kritické aplikace, například SAP ASCS Layer, SQL Server nebo souborového serveru se škálováním na více systémů.


### <a name="vmwarephysical-server-disaster-recovery"></a>Zotavení po havárii VMware/fyzický server

Funkce přidané v tomto měsíci jsou shrnuté v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
**Systém souborů Linux BRTFS** | Site Recovery teď podporuje replikaci virtuálních počítačů VMware se systémem souborů BRTFS. Replikace není podporovaná, pokud:<br/><br/>– Po povolení replikace se změní dílčí svazek systému souborů BTRFS.<br/><br/>– Systém souborů je rozložen na více disků.<br/><br/>– Systém souborů BTRFS podporuje RAID.
**Windows Server 2019** | Byla přidána podpora pro počítače se systémem Windows Server 2019.


## <a name="updates-january-2019"></a>Aktualizace (leden 2019)


### <a name="accelerated-networking-azure-vms"></a>Akcelerované síťové služby (virtuální počítače Azure)

Akcelerované síťové služby umožňují virtuálnímu počítači pomocí rozhraní SR-IOV (single-root I/O Virtualization), což zvyšuje výkon sítě. Pokud povolíte replikaci pro virtuální počítač Azure, Site Recovery zjistí, jestli je povolené urychlené síťové služby. Pokud je, po převzetí služeb při selhání Site Recovery automaticky nakonfiguruje akcelerované sítě na virtuálním počítači Azure v cílové replice pro [Windows](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) i [Linux](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

[Přečtěte si další informace](azure-vm-disaster-recovery-with-accelerated-networking.md).

### <a name="update-rollup-32"></a>Kumulativní aktualizace 32

[Kumulativní aktualizace 32](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace Site Recovery agentů a zprostředkovatelů (jak je popsáno v souhrnu).
**Opravy a vylepšení problémů** | Řada oprav a vylepšení (jak je popsáno v souhrnu).

### <a name="azure-vm-disaster-recovery"></a>Zotavení po havárii virtuálního počítače Azure

Funkce přidané v tomto měsíci jsou shrnuté v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
**Podpora Linuxu** | Přidala se podpora pro RedHat Workstation 6/7 a nové verze jádra pro Ubuntu, Debian a SUSE.
**Prostory úložiště s přímým přístupem** | Site Recovery podporuje virtuální počítače Azure s využitím Prostory úložiště s přímým přístupem (S2D).

### <a name="vmware-vmsphysical-servers-disaster-recovery"></a>Zotavení po havárii virtuálních počítačů VMware/fyzických serverů

Funkce přidané v tomto měsíci jsou shrnuté v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
**Podpora Linuxu** | Přidala se podpora pro RedHat Enterprise Linux 7,6, RedHat Workstation 6/7, Oracle Linux 6,10 a Oracle Linux 7,6 a nové verze jádra pro Ubuntu, Debian a SUSE.


### <a name="update-rollup-31"></a>Kumulativní aktualizace 31

[Kumulativní aktualizace 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) nabízí následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace Site Recovery agentů a zprostředkovatelů (jak je popsáno v souhrnu).
**Opravy a vylepšení problémů** | Řada oprav a vylepšení (jak je popsáno v souhrnu).

### <a name="vmware-vmsphysical-servers-replication"></a>Replikace virtuálních počítačů nebo fyzických serverů VMware

Funkce přidané v tomto měsíci jsou shrnuté v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
**Podpora Linuxu** |  Byla přidána podpora pro Oracle Linux 6,8, Oracle Linux 6,9 a Oracle Linux 7,0 s jádrem kompatibilním s Red Hat a pro UEK (Enterprise kernel kernel) verze 5.
**LVM** | Byla přidána podpora pro LVM a LVM2 svazky.<br/><br/> Je teď podporovaný adresář/Boot na diskovém oddílu a na svazcích LVM.
**Adresáře** | Byla přidána podpora pro tyto adresáře nastavená jako samostatné oddíly nebo systémy souborů, které nejsou na stejném systémovém disku:<br/><br/> /(root),/Boot,/usr,/usr/local,/var,/etc.
**Windows Server 2008** | Byla přidána podpora pro dynamické disky.
**Převzetí služeb při selhání** | Vylepšená doba převzetí služeb při selhání pro virtuální počítače VMware, kde storvsc a vsbus nejsou spouštěcí ovladače.
**Podpora rozhraní UEFI** | Virtuální počítače Azure nepodporují typ spouštění UEFI. Teď můžete migrovat místní fyzické servery s rozhraním UEFI do Azure pomocí Site Recovery. Site Recovery migruje server tak, že před migrací převede typ spouštění na BIOS. Site Recovery dřív podporoval tento převod jenom pro virtuální počítače. Podpora je dostupná pro fyzické servery se systémem Windows Server 2012 nebo novějším.

### <a name="azure-vm-disaster-recovery"></a>Zotavení po havárii virtuálního počítače Azure

Funkce přidané v tomto měsíci jsou shrnuté v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
**Podpora Linuxu** | Byla přidána podpora pro Oracle Linux 6,8, Oracle Linux 6,9 a Oracle Linux 7,0 s jádrem kompatibilním s Red Hat a pro UEK (Enterprise kernel kernel) verze 5.
**Systém souborů Linux BRTFS** | Podporováno pro virtuální počítače Azure.
**Virtuální počítače Azure v zónách dostupnosti** | Replikaci můžete povolit do jiné oblasti pro virtuální počítače Azure nasazené v zónách dostupnosti. Teď můžete povolit replikaci pro virtuální počítač Azure a nastavit cíl pro převzetí služeb při selhání na jednu instanci virtuálního počítače, virtuální počítač ve skupině dostupnosti nebo virtuální počítač v zóně dostupnosti. Nastavení nemá vliv na replikaci. [Přečtěte](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/) si oznámení.
**Úložiště s podporou brány firewall (portál/PowerShell)** | Byla přidána podpora pro [účty úložiště s povoleným bránou firewall](../storage/common/storage-network-security.md).<br/><br/> Můžete replikovat virtuální počítače Azure s nespravovanými disky v účtech úložiště s povolenými firewally do jiné oblasti Azure pro zotavení po havárii.<br/><br/> Účty úložiště s povoleným firewallem můžete používat jako cílové účty úložiště pro nespravované disky.<br/><br/> Podporováno na portálu a pomocí PowerShellu.

## <a name="updates-december-2018"></a>Aktualizace (prosince 2018)

### <a name="automatic-updates-for-the-mobility-service-azure-vms"></a>Automatické aktualizace služby mobility (virtuální počítače Azure)

Site Recovery přidat možnost pro automatické aktualizace rozšíření služby mobility. Rozšíření služby mobility je nainstalované na každém virtuálním počítači Azure replikovaném pomocí Site Recovery. Pokud povolíte replikaci, vyberte, jestli se má Site Recovery povolit správu aktualizací rozšíření.

Aktualizace nevyžadují restartování virtuálního počítače a neovlivňují replikaci. [Přečtěte si další informace](azure-to-azure-autoupdate.md).

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Cenová kalkulačka pro zotavení po havárii virtuálního počítače Azure

Zotavení po havárii virtuálních počítačů Azure má za následek náklady na licencování virtuálních počítačů a náklady na síť a úložiště. Azure poskytuje [cenovou kalkulačku](https://aka.ms/a2a-cost-estimator) , která vám umožní zjistit tyto náklady. Site Recovery teď poskytuje [příklad cenového odhadu](https://aka.ms/a2a-cost-estimator) , který ceny ukázkového nasazení vychází z aplikace na základě tří vrstev s použitím šesti virtuálních počítačů s 12 HDD úrovně Standard disky a 6 SSD úrovně Premium disky.

- Ukázka předpokládá, že se změní data o velikosti 10 GB za den pro Standard a 20 GB pro Premium.
- Pro konkrétní nasazení můžete změnit proměnné pro odhadované náklady.
- Můžete zadat počet virtuálních počítačů, počet a typ spravovaných disků a očekávanou celkovou rychlost změny dat očekávanou v rámci virtuálních počítačů.
- Navíc můžete použít kompresní faktor k odhadu nákladů na šířku pásma.

[Přečtěte](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) si oznámení.


## <a name="updates-october-2018"></a>Aktualizace (říjen 2018)

### <a name="update-rollup-30"></a>Kumulativní aktualizace 30

[Kumulativní aktualizace 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace Site Recovery agentů a zprostředkovatelů (jak je popsáno v souhrnu).
**Opravy a vylepšení problémů** | Řada oprav a vylepšení (jak je popsáno v souhrnu).

### <a name="azure-vm-disaster-recovery"></a>Zotavení po havárii virtuálního počítače Azure
Funkce přidané v tomto měsíci jsou shrnuté v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
**Podpora oblastí** | Přibyla Site Recovery podpora pro Austrálie – střed 1 a Austrálie – střed 2.
**Podpora pro šifrování disků** | Byla přidána podpora pro zotavení po havárii virtuálních počítačů Azure šifrovaných pomocí Azure Disk Encryption (ADE) s aplikací Azure AD. [Přečtěte si další informace](azure-to-azure-how-to-enable-replication-ade-vms.md).
**Vyloučení disku** | Během replikace virtuálních počítačů Azure se teď automaticky vyloučí neinicializované disky.
**Úložiště s podporou brány firewall (PowerShell)** | Byla přidána podpora pro [účty úložiště s povoleným bránou firewall](../storage/common/storage-network-security.md).<br/><br/> Můžete replikovat virtuální počítače Azure s nespravovanými disky v účtech úložiště s povolenými firewally do jiné oblasti Azure pro zotavení po havárii.<br/><br/> Účty úložiště s povoleným firewallem můžete používat jako cílové účty úložiště pro nespravované disky.<br/><br/> Podporováno pouze pomocí prostředí PowerShell.


### <a name="update-rollup-29"></a>Kumulativní aktualizace 29

[Kumulativní aktualizace 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace Site Recovery agentů a zprostředkovatelů (jak je popsáno v souhrnu).
**Opravy a vylepšení problémů** | Řada oprav a vylepšení (jak je popsáno v souhrnu).


## <a name="updates-august-2018"></a>Aktualizace (srpen 2018)

### <a name="update-rollup-28"></a>Kumulativní aktualizace 28

[Kumulativní aktualizace 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) poskytuje následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace Site Recovery agentů a zprostředkovatelů (jak je popsáno v souhrnu).
**Opravy a vylepšení problémů** | Řada oprav a vylepšení (jak je popsáno v souhrnu).

### <a name="azure-vm-disaster-recovery"></a>Zotavení po havárii virtuálního počítače Azure
Funkce přidané v tomto měsíci jsou shrnuté v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
**Podpora Linuxu** | Přidáno podporováno pro RedHat Enterprise Linux 6,10; CentOS 6,10.<br/><br/>
**Cloudová podpora** | Podporované zotavení po havárii pro virtuální počítače Azure v německém cloudu.
**Zotavení po havárii mezi předplatnými** | Podpora pro replikaci virtuálních počítačů Azure v jedné oblasti do jiné oblasti v jiném předplatném, v rámci stejného Azure Active Directory tenanta. [Přečtěte si další informace](https://aka.ms/cross-sub-blog).

### <a name="vmware-vmphysical-server-disaster-recovery"></a>Zotavení po havárii virtuálního počítače VMware/fyzického serveru
Funkce přidané v tomto měsíci jsou shrnuté v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
**Podpora Linuxu** | Byla přidána podpora pro RedHat Enterprise Linux 6,10, CentOS 6,10.<br/><br/> Virtuální počítače se systémem Linux, které používají styl oddílu tabulka oddílů GUID (GPT) v režimu kompatibility se starším systémem BIOS, jsou nyní podporovány. Další informace najdete v tématu [Nejčastější dotazy k virtuálním počítačům Azure](../virtual-machines/faq-for-disks.md) .
**Zotavení po havárii pro virtuální počítače po migraci** | Podpora pro povolení zotavení po havárii do sekundární oblasti pro místní virtuální počítač VMware migrované do Azure, aniž byste před povolením replikace museli odinstalovat službu mobility na virtuálním počítači.
**Windows Server 2008** | Podpora pro migraci počítačů se systémem Windows Server 2008 R2/2008 64-bit a 32-bit.<br/><br/> Jenom migrace (replikace a převzetí služeb při selhání). Navrácení služeb po obnovení se nepodporuje.

## <a name="updates-july-2018"></a>Aktualizace (červenec 2018)

### <a name="update-rollup-27-july-2018"></a>Kumulativní aktualizace 27 (červenec 2018)

[Kumulativní aktualizace 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery) nabízí následující aktualizace.

**Aktualizace** | **Podrobnosti**
--- | ---
**Zprostředkovatelé a agenti** | Aktualizace Site Recovery agentů a zprostředkovatelů (jak je popsáno v souhrnu).
**Opravy a vylepšení problémů** | Řada oprav a vylepšení (jak je popsáno v souhrnu).

### <a name="azure-vm-disaster-recovery"></a>Zotavení po havárii virtuálního počítače Azure

Funkce přidané v tomto měsíci jsou shrnuté v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
**Podpora Linuxu** | Byla přidána podpora pro Red Hat Enterprise Linux 7,5.

### <a name="vmware-vmphysical-server-disaster-recovery"></a>Zotavení po havárii virtuálního počítače VMware/fyzického serveru

Funkce přidané v tomto měsíci jsou shrnuté v tabulce.

**Funkce** | **Podrobnosti**
--- | ---
**Podpora Linuxu** | Byla přidána podpora pro Red Hat Enterprise Linux 7,5 SUSE Linux Enterprise Server 12.



## <a name="next-steps"></a>Další kroky

Aktualizujte si naše aktualizace na stránce s [aktualizacemi Azure](https://azure.microsoft.com/updates/?product=site-recovery) .