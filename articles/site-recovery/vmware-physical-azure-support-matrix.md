---
title: Podporu pro virtuální počítače VMware a fyzické servery replikující se do Azure s Azure Site Recovery je dostupná | Microsoft Docs
description: Shrnuje podporované operační systémy a součásti pro replikaci virtuálních počítačů VMware a fyzické serveru do Azure pomocí Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2018
ms.author: raynew
ms.openlocfilehash: c80b47dcf2477fda5406c2e6efe3ee87ad1188d6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="support-matrix-for-vmware-and-physical-server-replication-to-azure"></a>Podporu pro VMware a fyzické serveru replikaci do Azure.

Tento článek obsahuje souhrn podporovaných součásti a nastavení pro zotavení po havárii virtuálních počítačů VMware do Azure pomocí [Azure Site Recovery](site-recovery-overview.md).

## <a name="replication-scenario"></a>Scénář replikace

**Scénář** | **Podrobnosti**
--- | ---
Virtuální počítače VMware | Replikace virtuálních počítačů VMware místně do Azure. Můžete nasadit tento scénář na portálu Azure nebo pomocí prostředí PowerShell.
Fyzické servery | Replikace fyzických serversto Windows nebo Linuxem místní Azure. Můžete nasadit tento scénář v portálu Azure.

## <a name="on-premises-virtualization-servers"></a>Místní virtualizace serverů

**Server** | **Požadavky** | **Podrobnosti**
--- | --- | ---
VMware | vCenter Server verze 6.5, 6.0, nebo 5.5 nebo vSphere verze 6.5, 6.0 nebo 5,5 | Doporučujeme vám, že používáte vCenter server.<br/><br/> Doporučujeme vám, že vSphere hostitelů a serverů vCenter nacházejí ve stejné síti jako procesní server. Ve výchozím nastavení spouští součásti serveru proces na konfiguračním serveru, tak to bude síť, ve kterém můžete nastavit konfigurační server, pokud nastavíte vyhrazené procesový server. 
Fyzické | neuvedeno

## <a name="site-recovery-configuration-server"></a>Konfigurace serveru pro obnovení lokality

Konfigurační server je na místním počítači, který spouští součásti Site Recovery, včetně konfigurační server, procesový server a hlavní cílový server. Pro replikaci VMware nastavíte konfigurační server se všemi požadavky, pomocí šablony OVF k vytvoření virtuálního počítače VMware. Pro replikaci fyzický server můžete nastavit počítač konfigurace serveru ručně.

**Komponenta** | **Požadavky**
--- |---
Procesorová jádra | 8 
Paměť RAM | 12 GB
Počet disků | 3 disky<br/><br/> Disky zahrnují disku operačního systému, proces disku mezipaměti serveru a jednotka pro uchování pro navrácení služeb po obnovení.
Volné místo na disku | 600 GB místa na požadované pro proces mezipaměti serveru.
Volné místo na disku | 600 GB místa vyžaduje jednotka pro uchování.
Operační systém  | Windows Server 2012 R2 nebo Windows Server 2016 | 
Národní prostředí operačního systému | Angličtina (en-us) 
PowerCLI | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0") by měly být nainstalovány.
Role Windows Serveru | Nepovolíte: <br> – Active Directory Domain Services <br>– Internet Information Service <br> – Hyper-V |
Zásady skupiny| Nepovolíte: <br> -Zabránit přístupu do příkazového řádku. <br> -Znemožnit přístup k registru nástroje pro úpravy. <br> -Důvěřujete logiku pro přílohy souborů. <br> -Zapněte provádění skriptu. <br> [Další informace](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Ověřte, že je:<br/><br/> -Nemáte dříve existující výchozí web <br> -Aktivovat [anonymní ověřování](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Aktivovat [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) nastavení  <br> -Nemají dříve existující web nebo aplikaci naslouchá na portu 443<br>
Typ síťový adaptér | VMXNET3 (Pokud je nasazený jako virtuální počítač VMware) 
Typ IP adresy | Statická 
Porty | použít pro ovládací prvek kanál orchestration 443)<br>9443 používá pro přenos dat

## <a name="replicated-machines"></a>Replikovaných počítačů

Site Recovery podporuje replikaci jakoukoli úlohu spuštěnou na počítači, podporované.

**Komponenta** | **Podrobnosti**
--- | ---
Nastavení počítače | Počítače, které se replikují do Azure, musí splňovat [požadavky pro Azure](#azure-vm-requirements).
Operační systém Windows | 64bitová verze systému Windows Server 2016 (jádra serveru, serveru pomocí možnosti práce s počítačem), Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 s v minimálně SP1. Windows 2016 Nano Server není podporován.
Operační systém Linux | Red Hat Enterprise Linux: 5.2 k 5,11, 6.1 k 6.9 7.0 až 7,4 <br/><br/>CentOS: 5.2 k 5,11, 6.1 k 6.9 7.0 až 7,4 <br/><br/>Ubuntu 14.04 LTS server[ (podporované verze jádra)](#ubuntu-kernel-versions)<br/><br/>Ubuntu 16.04 LTS server[ (podporované verze jádra)](#ubuntu-kernel-versions)<br/><br/>Debian 7/Debian 8[ (podporované verze jádra)](#debian-kernel-versions)<br/><br/>Oracle Enterprise Linux 6.4, 6.5 systémem Red Hat kompatibilní jádra nebo nedělitelné Enterprise jádra verze 3 (UEK3) <br/><br/>SUSE Linux Enterprise Server 11 SP3, SUSE Linux Enterprise Server 11 SP4 <br/><br/>Upgrade replikovaných počítačů z SP3 SP4 není podporován. K upgradu, zakažte replikaci a povolte ho znovu po dokončení upgradu.

>[!NOTE]
>
> - Na Linuxových distribucích jsou podporovány pouze uložené jádra, které jsou součástí distribuční podverze verzi nebo aktualizaci.
>
> - Mezi hlavní Linux není podporována verze distribuční upgrade chráněné počítače. Pokud chcete upgradovat, zakažte replikaci, upgrade operačního systému a pak povolení replikace znovu.
>

### <a name="ubuntu-kernel-versions"></a>Verze Ubuntu jádra


**Podporované verze** | **Verze Azure je služba Site Recovery Mobility** | **Verze jádra** |
--- | --- | --- |
14.04 LTS | 9.12 | 3.13.0-24-Generic k 3.13.0-132-generic,<br/>3.16.0-25-Generic k 3.16.0-77-generic,<br/>3.19.0-18-Generic k 3.19.0-80-generic,<br/>4.2.0-18-Generic k 4.2.0-42-generic,<br/>4.4.0-21-Generic k 4.4.0-96-generic |
14.04 LTS | 9.13 | 3.13.0-24-Generic k 3.13.0-137-generic,<br/>3.16.0-25-Generic k 3.16.0-77-generic,<br/>3.19.0-18-Generic k 3.19.0-80-generic,<br/>4.2.0-18-Generic k 4.2.0-42-generic,<br/>4.4.0-21-Generic k 4.4.0-104-generic |
14.04 LTS | 9.14 | 3.13.0-24-Generic k 3.13.0-142-generic,<br/>3.16.0-25-Generic k 3.16.0-77-generic,<br/>3.19.0-18-Generic k 3.19.0-80-generic,<br/>4.2.0-18-Generic k 4.2.0-42-generic,<br/>4.4.0-21-Generic k 4.4.0-116-generic |
14.04 LTS | 9.15. | 3.13.0-24-Generic k 3.13.0-144-generic,<br/>3.16.0-25-Generic k 3.16.0-77-generic,<br/>3.19.0-18-Generic k 3.19.0-80-generic,<br/>4.2.0-18-Generic k 4.2.0-42-generic,<br/>4.4.0-21-Generic k 4.4.0-119-generic |
16.04 LTS | 9.12 | 4.4.0-21-Generic k 4.4.0-96-generic,<br/>4.8.0-34-Generic k 4.8.0-58-generic,<br/>4.10.0-14-Generic k 4.10.0-35-generic |
16.04 LTS | 9.13 | 4.4.0-21-Generic k 4.4.0-104-generic,<br/>4.8.0-34-Generic k 4.8.0-58-generic,<br/>4.10.0-14-Generic k 4.10.0-42-generic |
16.04 LTS | 9.14 | 4.4.0-21-Generic k 4.4.0-116-generic,<br/>4.8.0-34-Generic k 4.8.0-58-generic,<br/>4.10.0-14-Generic k 4.10.0-42-generic,<br/>4.11.0-13-Generic k 4.11.0-14-generic,<br/>4.13.0-16-Generic k 4.13.0-36-generic,<br/>4.11.0-1009-Azure k 4.11.0-1016-azure,<br/>4.13.0-1005-Azure k 4.13.0-1011-azure |
16.04 LTS | 9.15. | 4.4.0-21-Generic k 4.4.0-119-generic,<br/>4.8.0-34-Generic k 4.8.0-58-generic,<br/>4.10.0-14-Generic k 4.10.0-42-generic,<br/>4.11.0-13-Generic k 4.11.0-14-generic,<br/>4.13.0-16-Generic k 4.13.0-38-generic,<br/>4.11.0-1009-Azure k 4.11.0-1016-azure,<br/>4.13.0-1005-Azure k 4.13.0-1012-azure |



### <a name="debian-kernel-versions"></a>Verze debian jádra


**Podporované verze** | **Verze Azure je služba Site Recovery Mobility** | **Verze jádra** |
--- | --- | --- |
Debian 7 | 9.14, 9.15. | 3.2.0-4-amd64 k 3.2.0-5-amd64, 3.16.0-0.bpo.4-amd64 |
Debian 8 | 9.14, 9.15. | 3.16.0-4-amd64 k 3.16.0-5-amd64, 4.9.0-0.bpo.4-amd64 k 4.9.0-0.bpo.5-amd64 |


## <a name="linux-file-systemsguest-storage"></a>Linux souboru systémy nebo hosta úložiště

**Komponenta** | **Podporuje se**
--- | ---
systémy souborů | ext3, ext4, XFS.
Správce svazků | LVM2.
Vícenásobný software | Mapovač zařízení.
Zařízení úložiště Paravirtualized | Zařízení exportovaná paravirtualizovanými ovladači se nepodporují.
Blokovat více fronty vstupně-výstupní operace zařízení | Nepodporuje se.
Fyzické servery se řadič úložiště HP CCISS | Nepodporuje se.
Adresáře | Tyto adresáře (Pokud nastavený jako samostatné oddíly/souboru systems) všechny musí být na stejném disku operačního systému na zdrojovém serveru: / (kořenová), / Boot, USR, /usr/local, /var, etc.</br></br> / Boot by měla být na oddílu disku a nesmí být svazek LVM.<br/><br/>
Požadavky na volné místo| 2 GB v oddílu/root <br/><br/> 250 MB v instalační složce
XFSv5 | Funkce XFSv5 v systémech souborů s XFS, jako je například metadata kontrolního součtu, jsou podporovány ze služby Mobility verze dále 9.10. Nástroje xfs_info zkontrolujte tzv XFS pro oddíl. Pokud ftype nastavena na hodnotu 1, funkce XFSv5 jsou používány.



## <a name="network"></a>Síť

**Komponenta** | **Podporuje se**
--- | ---
Seskupování síťových adaptérů sítě hostitele | Pro virtuální počítače VMware podporován. <br/><br/>Není podporováno pro replikaci fyzického počítače.
Hostitelské sítě VLAN | Ano.
Hostitelské sítě IPv4 | Ano.
Síť hostitele IPv6 | Ne.
Seskupování síťových adaptérů sítě hosta/server | Ne.
Host server sítě IPv4 | Ano.
Host server sítě IPv6 | Ne.
Host server síti statickou IP adresu (Windows) | Ano.
Host server síti statickou IP adresu (Linux) | Ano. <br/><br/>Virtuální počítače jsou nakonfigurované pro používání protokolu DHCP na navrácení služeb po obnovení.
Host server sítě několik síťových adaptérů | Ano.


## <a name="azure-vm-network-after-failover"></a>Azure síť virtuálních počítačů (po převzetí služeb při selhání)

**Komponenta** | **Podporuje se**
--- | ---
Azure ExpressRoute | Ano
Interní nástroj pro vyrovnávání zatížení | Ano
ELB | Ano
Azure Traffic Manager | Ano
Více síťovými Kartami | Ano
Vyhrazená IP adresa | Ano
IPv4 | Ano
Zachovat zdrojové IP adresy | Ano
Koncové body služby Azure virtuální sítě<br/><br/> (Brány firewall úložiště azure a virtuální sítě) | Ne

## <a name="storage"></a>Úložiště
**Komponenta** | **Podporuje se**
--- | ---
Hostitele systému souborů NFS | Ano pro VMware<br/><br/> Ne pro fyzické servery
Síť SAN (ISCSI) hostitele | Ano
Multipath hostitele (MPIO) | Ano, testována s Microsoft DSM EMC PowerPath 5.7 SP4, EMC PowerPath DSM CLARiiON
Host server VMDK | Ano
Host server rozhraním EFI/UEFI| Partial (migrace do Azure pro Windows Server 2012 a novější virtuální počítače VMware pouze) </br></br> Viz poznámka na konci tabulky
Disk sdíleného clusteru hosta/server | Ne
Šifrované disk hosta/server | Ne
Host server systému souborů NFS | Ne
Host server protokolu SMB 3.0 | Ne
Host server RDM | Ano<br/><br/> Není k dispozici pro fyzické servery
Disk Host server > 1 TB | Ano<br/><br/>Až 4095 GB
Host server na disku s velikost fyzického sektoru 4K logické a 4 kB | Ano
Disk Host server s 4K logické a velikost fyzického sektoru 512 bajtů | Ano
Host server svazek s prokládané disku > 4 TB <br><br/>Správa logických svazků (LVM)| Ano
Host server - prostory úložiště | Ne
Disk přidat nebo odebrat aktivní hosta/server | Ne
Host server - vyloučení disku | Ano
Multipath hosta/server (MPIO) | neuvedeno

> [!NOTE]
> Rozhraní UEFI spouštění virtuálních počítačů VMware s Windows serverem 2012 nebo novější se dají migrovat na Azure. Platí následující omezení:

> - Je podporována pouze migrace do Azure. Navrácení služeb po obnovení k místní lokalitě VMware není podporována.
> - Server by neměla mít více než čtyři oddíly na disk operačního systému.
> - Vyžaduje verzi služby Mobility 9.13 nebo novější.
> - Není podporováno pro fyzických serverů.

## <a name="azure-storage"></a>Úložiště Azure

**Komponenta** | **Podporuje se**
--- | ---
(Locally redundant storage) Místně redundantní úložiště | Ano
Geograficky redundantní úložiště | Ano
Geograficky redundantní úložiště s přístupem pro čtení | Ano
Studeného úložiště | Ne
Horkého úložiště| Ne
Objekty blob bloku | Ne
Šifrování v klidovém stavu (šifrování služby úložiště)| Ano
Premium Storage | Ano
Import a export služby | Ne
Koncové body služby virtuální sítě<br/><br/> Úložiště brány firewall a virtuální sítě, které jsou nakonfigurované na cílový účet úložiště nebo mezipaměti úložiště (používá se k uložení dat replikace) | Ne
Účty úložiště obecné účely v2 (horká a studená vrstev) | Ne

## <a name="azure-compute"></a>Výpočtů Azure

**Funkce** | **Podporuje se**
--- | ---
Skupiny dostupnosti | Ano
ROZBOČOVAČE | Ano
Managed Disks | Ano

## <a name="azure-vm-requirements"></a>Požadavky virtuálního počítače Azure

Místní virtuální počítače, které se replikují do Azure, musí splňovat požadavky na virtuální počítač Azure souhrnu v této tabulce. Při obnovení lokality spustí kontrolu požadovaných součástí, se nepodaří, pokud nejsou splněny některé požadavky.

**Komponenta** | **Požadavky** | **Podrobnosti**
--- | --- | ---
Hostovaný operační systém | Ověřte [podporované operační systémy](#replicated machines). | Kontrola selže, pokud není podporován. 
Architektura operačního systému hosta | 64bitová verze. | Kontrola selže, pokud není podporován. 
Velikost disku operačního systému | Až 2 048 GB. | Kontrola selže, pokud není podporován. 
Počet disků operačního systému | 1 | Kontrola selže, pokud není podporován.  
Počet datových disků | 64 nebo méně. | Kontrola selže, pokud není podporován.  
Velikost datového disku | Až 4095 GB | Kontrola selže, pokud není podporován. 
Síťové adaptéry | Několik adaptérů jsou podporovány. | 
Sdílený virtuální pevný disk | Nepodporuje se. | Kontrola selže, pokud není podporován. 
FC disku | Nepodporuje se. | Kontrola selže, pokud není podporován. 
BitLocker | Nepodporuje se. | Dříve než povolíte replikaci pro počítač, musí se zakázat nástroj BitLocker. | 
název virtuálního počítače | Od 1 do 63 znaků.<br/><br/> Pouze písmena, číslice a pomlčky.<br/><br/> Název počítače musí začínat a končit písmenem nebo číslicí. |  Aktualizujte hodnotu v vlastnosti počítače ve službě Site Recovery.


## <a name="vault-tasks"></a>Úložiště úlohy

**Akce** | **Podporuje se**
--- | ---
Přesunutí trezoru rámci skupiny prostředků<br/><br/> V rámci a napříč odběrů | Ne
Přesunout úložiště, sítě, virtuální počítače Azure mezi skupinami prostředků<br/><br/> V rámci a napříč odběrů | Ne


## <a name="mobility-service"></a>Služba mobility

**Název** | **Popis** | **nejnovější verzi** | **Podrobnosti**
--- | --- | --- | --- | ---
Azure Site Recovery sjednocený instalační program | Koordinuje komunikaci mezi místními servery VMware a Azure <br/><br/> Nainstalovat na místní servery VMware | 9.12.4653.1 (k dispozici z portálu) | [Nejnovější funkce a opravy](https://aka.ms/latest_asr_updates)
Služba mobility | Koordinuje replikaci mezi místními VMware servery/fyzické servery a Azure nebo sekundární lokality<br/><br/> Nainstalovat na virtuální počítač VMware nebo fyzických serverů, které chcete replikovat | 9.12.4653.1 (k dispozici z portálu) | [Nejnovější funkce a opravy](https://aka.ms/latest_asr_updates)


## <a name="next-steps"></a>Další postup
[Zjistěte, jak](tutorial-prepare-azure.md) Příprava Azure pro zotavení po havárii virtuálních počítačů VMware.
