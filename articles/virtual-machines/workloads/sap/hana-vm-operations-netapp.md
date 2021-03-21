---
title: SAP HANA konfigurace ANF virtuálních počítačů Azure | Microsoft Docs
description: Azure NetApp Files doporučení pro úložiště pro SAP HANA.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP, Azure, ANF, HANA, Azure NetApp Files, snímek
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/23/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: de8af71928ad6a83d4930e4e6e0b8dd257148111
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101666617"
---
# <a name="nfs-v41-volumes-on-azure-netapp-files-for-sap-hana"></a>Svazky NFS v4.1 ve službě Azure NetApp Files pro SAP HANA

Azure NetApp Files poskytuje nativní sdílené složky systému souborů NFS, které se dají použít pro svazky **/Hana/Shared**, **/Hana/data** a **/Hana/log** . Použití sdílených složek systému souborů NFS založených na ANF pro svazky **/Hana/data** a **/Hana/log** vyžaduje použití protokolu NFS v 4.1. Protokol NFS verze 3 není podporován pro použití **/Hana/data** a **/Hana/log** svazků při založení sdílených složek na ANF. 


> [!IMPORTANT]
> Protokol NFS v3 implementovaný v Azure NetApp Files **není podporován pro** použití pro **/Hana/data** a **/Hana/log**. Použití NFS 4,1 je povinné pro svazky **/Hana/data** a **/Hana/log** z funkčního bodu zobrazení. Vzhledem k tomu, že je možné použít pro svazek **/Hana/Shared** systém souborů NFS v3 nebo NFS verze 4.1, ze funkčního hlediska.

## <a name="important-considerations"></a>Důležité informace

Při zvažování Azure NetApp Files SAP NetWeaver a SAP HANA si pamatujte na následující důležité informace:

- Minimální fond kapacit je 4 TiB  
- Minimální velikost svazku je 100 GiB
- Azure NetApp Files a všech virtuálních počítačů, ve kterých jsou připojené Azure NetApp Files svazky, musí být ve stejné oblasti Azure Virtual Network nebo v [partnerských virtuálních sítích](../../../virtual-network/virtual-network-peering-overview.md) .
- Je důležité mít virtuální počítače nasazené v těsné blízkosti úložiště Azure NetApp pro nízkou latenci.  
- Vybraná virtuální síť musí mít podsíť, delegovanou na Azure NetApp Files
- Ujistěte se, že latence z databázového serveru na ANF svazek je měřená a menší než 1 milisekunda.
- Propustnost svazku Azure NetApp je funkcí kvóty svazku a úrovně služeb, jak je uvedeno v části [úroveň služby pro Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). Při změně velikosti svazků NetApp Azure HANA se ujistěte, že výsledná propustnost splňuje požadavky na systém HANA.
- Zkuste "konsolidovat" svazky a dosáhnout tak vyššího výkonu většího objemu, například pro/sapmnt,/usr/SAP/trans,... Pokud je to možné  
- Azure NetApp Files nabízí [zásady exportu](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md): můžete řídit povolené klienty, typ přístupu (čtení&zápisu, jen pro čtení atd.). 
- Azure NetApp Files funkce zatím nereaguje na zóny. Aktuálně Azure NetApp Files funkce není nasazená ve všech zónách dostupnosti v oblasti Azure. Mějte na paměti, že v některých oblastech Azure máte vliv na potenciální latenci.   
- ID uživatele pro ADM s <b>identifikátorem SID</b>a ID skupiny pro `sapsys` virtuální počítače musí odpovídat konfiguraci v Azure NetApp Files. 

> [!IMPORTANT]
> U SAP HANA úloh je nízká latence kritická. Spolupracujte se zástupcem Microsoftu a zajistěte, aby se virtuální počítače a Azure NetApp Files svazky nasadily v těsné blízkosti.  

> [!IMPORTANT]
> Pokud dojde k neshodě mezi ID uživatele pro <b>ADM a</b>ID skupiny `sapsys` mezi virtuálním počítačem a konfigurací služby Azure NetApp, zobrazí se oprávnění k souborům na svazcích Azure NetApp připojených k virtuálnímu počítači `nobody` . Nezapomeňte zadat správné ID uživatele pro ADM s <b>identifikátorem SID</b>a ID skupiny pro `sapsys` , když se při [připojování nového systému do Azure NetApp Files nový systém](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) .


## <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Určení velikosti databáze HANA v Azure NetApp Files

Propustnost svazku Azure NetApp je funkce velikosti svazku a úrovně služby, jak je uvedeno v části [úroveň služby pro Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). 

Důležité je pochopit, že se jedná o poměr výkonu a že existují fyzická omezení pro LIF (logické rozhraní) SVM (virtuální počítač úložiště).

Následující tabulka ukazuje, že by mohlo být vhodné vytvořit velký "standardní" svazek pro ukládání záloh a že nemá smysl vytvořit "extrémně" svazek větší než 12 TB, protože by byla překročena kapacita fyzické šířky pásma jednoho LIF. 

Maximální propustnost pro LIF a jednu relaci Linux je mezi 1,2 a 1,4 GB/s. Pokud požadujete větší propustnost pro/Hana/data, můžete pomocí SAP HANA rozdělit datové svazky rozdělit aktivitu vstupu/výstupu během opětovného načtení dat nebo HANA úložných bodů napříč více datovými soubory HANA, které se nacházejí ve více sdílených složkách systému souborů NFS. Další podrobnosti o prokládaných datových svazcích HANA najdete v těchto článcích:

- [Příručka pro správce HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.05/en-US/40b2b2a880ec4df7bac16eae3daef756.html?q=hana%20data%20volume%20partitioning)
- [Blog o SAP HANA – dělení datových svazků](https://blogs.sap.com/2020/10/07/sap-hana-partitioning-data-volumes/)
- [Poznámka ke SAP #2400005](https://launchpad.support.sap.com/#/notes/2400005)
- [Poznámka ke SAP #2700123](https://launchpad.support.sap.com/#/notes/2700123)


| Velikost  | Propustnost Standard | Propustnost – Premium | Propustnost Ultra |
| --- | --- | --- | --- |
| 1 TB | 16 MB/s | 64 MB/s | 128 MB/s |
| 2 TB | 32 MB/s | 128 MB/s | 256 MB/s |
| 4 TB | 64 MB/s | 256 MB/s | 512 MB/s |
| 10 TB | 160 MB/s | 640 MB/s | 1 280 MB/s |
| 15 TB | 240 MB/s | 960 MB/s | 1 400 MB/s |
| 20 TB | 320 MB/s | 1 280 MB/s | 1 400 MB/s |
| 40 TB | 640 MB/s | 1 400 MB/s | 1 400 MB/s |

Je důležité pochopit, že data se zapisují do stejného SSD do back-endu úložiště. Kvóta výkonu z fondu kapacit byla vytvořena, aby bylo možné spravovat prostředí.
Klíčové ukazatele výkonu jsou pro všechny velikosti databází HANA stejné. V téměř všech případech tento předpoklad neodráží realitu a očekává se, že zákazník. Velikost systémů HANA nemusí nutně znamenat, že malý systém vyžaduje nízkou propustnost úložiště – a velký systém vyžaduje vysokou propustnost úložiště. Obecně ale můžeme očekávat vyšší propustnost pro větší databázové instance HANA. V důsledku pravidel pro změny velikosti SAP pro základní hardware tyto větší instance HANA také poskytují více prostředků procesoru a vyšší paralelismus v rámci úloh, jako je načítání dat po restartování instancí. V důsledku toho by měly být velikosti svazků přijaty na očekávání a požadavky zákazníků. A neřídí se jenom čistě požadavky na kapacitu.

Při návrhu infrastruktury pro SAP v Azure byste měli znát minimální požadavky na propustnost úložiště (pro systémy výroby) pomocí SAP, který se přeloží na minimální propustnost:

| Typ svazku a vstupně-výstupní typ | Minimální klíčový ukazatel výkonu, který vyžádané SAP | Úroveň služeb úrovně Premium | Ultra – úroveň služby |
| --- | --- | --- | --- |
| Zápis svazku protokolu | 250 MB/s | 4 TB | 2 TB |
| Zápis datového svazku | 250 MB/s | 4 TB | 2 TB |
| Čtení objemu dat | 400 MB/s | 6,3 TB | 3,2 TB |

Vzhledem k tomu, že všechny tři klíčové ukazatele výkonu jsou požadovány, musí být velikost **/Hana/data** svazku směrem k větší kapacitě, aby splňovala minimální požadavky na čtení.

U systémů HANA, které nevyžadují velkou šířku pásma, můžou být velikosti svazků ANF menší. A v případě, že systém HANA vyžaduje větší propustnost, může být svazek přizpůsoben změnou velikosti kapacity online. Pro záložní svazky nejsou definovány žádné klíčové ukazatele výkonu. Propustnost svazku pro zálohování je však zásadní pro dobře fungující prostředí. Protokol – výkon datového svazku musí být navržený tak, aby splňoval očekávání zákazníků.

> [!IMPORTANT]
> Nezávisle na kapacitě, kterou nasazujete na jednom svazku NFS, se očekává, že propustnost stabilní úrovně v rozsahu od 1.2 do 1,4 GB/s (šířka pásma), kterou využívá spotřebitel ve virtuálním počítači. To se musí udělat se základní architekturou nabídky ANF a souvisejícími omezeními pro Linux Sessions pro systém souborů NFS. Údaje o výkonu a propustnosti popsané v článku [výsledky testování testů výkonnosti pro Azure NetApp Files](../../../azure-netapp-files/performance-benchmarks-linux.md) byly provedeny na jednom sdíleném svazku systému souborů NFS s několika klientskými virtuálními počítači a v důsledku více relací. Tento scénář se liší od scénáře, který je v SAP k disměrnému scénáři. Místo měření propustnosti z jednoho virtuálního počítače na svazek systému souborů NFS. Hostováno v ANF.

Aby splňovala požadavky na minimální propustnost SAP pro data a protokol a podle pokynů pro **/Hana/Shared**, Doporučené velikosti by vypadaly takto:

| Svazek | Velikost<br /> Premium Storage úroveň | Velikost<br /> Úroveň úložiště Ultra Storage | Podporovaný protokol NFS |
| --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v 4.1 |
| /hana/data | 6,3 TiB | 3,2 TiB | v 4.1 |
| /Hana/Shared škálování | Min (1 TB, 1 x RAM)  | Min (1 TB, 1 x RAM) | V3 nebo v 4.1 |
| škálování na více instancí/Hana/Shared | 1 x RAM pracovního uzlu<br /> za 4 uzly pracovního procesu  | 1 x RAM pracovního uzlu<br /> za 4 uzly pracovního procesu  | V3 nebo v 4.1 |
| /hana/logbackup | 3 x RAM  | 3 x RAM | V3 nebo v 4.1 |
| /hana/backup | 2 x RAM  | 2 x RAM | V3 nebo v 4.1 |

Pro všechny svazky se důrazně doporučuje systém souborů NFS verze 4.1.

Velikosti záložních svazků se odhadují. Na základě procesů úloh a operací je potřeba definovat přesné požadavky. V případě záloh můžete konsolidovat mnoho svazků pro různé SAP HANA instancí na jeden (nebo dva) větší svazky, což může mít nižší úroveň služby ANF.

> [!NOTE]
> Azure NetApp Files, doporučení pro změnu velikosti uvedená v tomto dokumentu cílí na minimální požadavky, které SAP vyjadřují na jejich poskytovatele infrastruktury. V reálných scénářích nasazení zákazníků a úloh, které nemusí být dostatečné. Tato doporučení použijte jako výchozí bod a přizpůsobte je na základě požadavků konkrétního zatížení.  

Proto byste měli zvážit, jak nasadit podobnou propustnost pro ANF svazky, jak je uvedeno pro úložiště Ultra disk již. Vezměte v úvahu také velikosti pro velikosti uvedené pro svazky pro různé skladové položky virtuálních počítačů, které jsou v tabulkách s Ultra diskem již provedeny.

> [!TIP]
> Můžete znovu nastavit velikost svazků Azure NetApp Files dynamicky bez nutnosti `unmount` svazků, zastavit virtuální počítače nebo zastavit SAP HANA. To umožňuje flexibilitu v závislosti na očekávané i nepředvídatelné propustnosti požadavků.

Dokumentace k nasazení SAP HANA konfigurace škálování na více instancí s pohotovostním uzlem pomocí systému souborů NFS v 4.1, který je hostovaný v ANF, se zveřejňuje v SAP HANA škálování na více instancích [s pohotovostním uzlem na virtuálních počítačích Azure s Azure NetApp Files na SUSE Linux Enterprise Server](./sap-hana-scale-out-standby-netapp-files-suse.md).


## <a name="availability"></a>Dostupnost
Aktualizace a upgrady systému ANF se uplatní, aniž by to ovlivnilo prostředí zákazníka. Definovaná [smlouva SLA je 99,99%](https://azure.microsoft.com/support/legal/sla/netapp/).


## <a name="volumes-and-ip-addresses-and-capacity-pools"></a>Svazky a IP adresy a fondy kapacit
S ANF je důležité pochopit, jak je základní infrastruktura sestavena. Fond kapacit je pouze struktura, což usnadňuje vytvoření modelu fakturace pro ANF. Fond kapacit nemá žádný fyzický vztah k základní infrastruktuře. Pokud vytvoříte fond kapacit, vytvoří se prostředí, které se dá účtovat, ne další. Při vytváření svazku se první SVM (virtuální počítač úložiště) vytvoří na clusteru s několika NetApp systémy. Pro tento SVM se vytvoří jedna IP adresa pro přístup ke svazku. Pokud vytvoříte několik svazků, všechny svazky se v tomto SVM rozdělují přes tento cluster s více řadiči NetApp. I když dostanete jenom jednu IP adresu, rozdělí se data na několik řadičů. ANF má logiku, která automaticky distribuuje úlohy zákazníka, jakmile svazky nebo kapacita nakonfigurovaného úložiště dosáhnou interní předem definované úrovně. Takové případy si můžete všimnout, protože pro přístup ke svazkům bude přiřazená nová IP adresa.

##<a name="log-volume-and-log-backup-volume"></a>Svazek protokolu a svazek zálohy protokolu
Protokol "svazek" (**/Hana/log**) se používá k zápisu do online protokolu opětovného provedení. Proto existují otevřené soubory umístěné na tomto svazku a nemají žádný smysl pro snímek tohoto svazku. Soubory protokolu opětovného provedení služby online se archivují nebo zálohují do svazku zálohy protokolu, jakmile je soubor protokolu online opětovného přihlášení plný nebo se spustí zálohování protokolu znovu. Pro zajištění přiměřeného výkonu zálohování vyžaduje záložní svazek protokolu dobrou propustnost. Pokud chcete optimalizovat náklady na úložiště, může to být smyslem pro konsolidaci svazku log-Backup u více instancí HANA. Takže více instancí HANA využívá stejný svazek a zapisuje zálohy do různých adresářů. Pomocí takové konsolidace můžete získat více propustností, protože je potřeba, aby svazek byl trochu větší. 

Totéž platí pro svazek, který používáte k zápisu úplných záloh databáze HANA do.  
 

## <a name="backup"></a>Backup
Kromě záloh streamování a back-SAP HANA mailové služby Azure, která je popsaná v [Průvodci zálohováním článku pro SAP HANA v Azure Virtual Machines](./sap-hana-backup-guide.md), Azure NetApp Files otevře možnost provádět zálohování snímků na základě úložiště. 

SAP HANA podporuje:

- Zálohy snímků založené na úložišti z SAP HANA 1,0 SPS7 na
- Podpora zálohování snímků založeného na úložišti pro prostředí MDC (multi Database Container) HANA z SAP HANA 2,0 SPS4 na


Vytvoření záloh snímků na základě úložiště je jednoduché čtyři kroky: 
1. Vytvoření snímku databáze HANA (interní) – aktivita, kterou nebo nástroje potřebujete provést 
1. SAP HANA zapisuje data do datových souborů, aby se vytvořil konzistentní stav úložiště – HANA provede tento krok v důsledku vytvoření snímku HANA.
1. Vytvořte snímek na svazku **/Hana/data** v úložišti – krok, který budete potřebovat k provedení nástrojů. Na svazku **/Hana/log** není potřeba provádět snímek.
1. Odstraňte snímek databáze HANA (interní) a obnovte normální provoz – krok, který nebo nástroje potřebujete provést.

> [!WARNING]
> Chybějící poslední krok nebo neúspěšné provedení posledního kroku má vážný dopad na požadavky na paměť SAP HANA a může vést k zastavení SAP HANA

```
BACKUP DATA FOR FULL SYSTEM CREATE SNAPSHOT COMMENT 'SNAPSHOT-2019-03-18:11:00';
```

![Záloha snímku ANF pro SAP HANA](media/hana-vm-operations-netapp/storage-snapshot-scenario.png)

```
az netappfiles snapshot create -g mygroup --account-name myaccname --pool-name mypoolname --volume-name myvolname --name mysnapname 
```

![Zálohování snímků ANF pro SAP HANA Část2](media/hana-vm-operations-netapp/storage-snapshot.png)

```
BACKUP DATA FOR FULL SYSTEM CLOSE SNAPSHOT BACKUP_ID 47110815 SUCCESSFUL SNAPSHOT-2020-08-18:11:00';
```

Tuto proceduru zálohování snímků můžete spravovat různými způsoby pomocí různých nástrojů. Jedním z příkladů je skript Pythonu "ntaphana_azure. py" dostupný na GitHubu [https://github.com/netapp/ntaphana](https://github.com/netapp/ntaphana) je to vzorový kód, který je poskytován "tak, jak je" bez jakékoli údržby a podpory.



> [!CAUTION]
> Samotný snímek není chráněnou zálohou, protože se nachází ve stejném fyzickém úložišti jako svazek, na který jste právě snímky nastavili. Je nutné chránit alespoň jeden snímek za den do jiného umístění. To se dá udělat ve stejném prostředí, ve vzdálené oblasti Azure nebo v úložišti objektů BLOB v Azure.


Pro uživatele produktů CommVault Backup je druhá možnost CommVault IntelliSnap V. 11.21 a novější. Tato nebo novější verze CommVault nabídky Azure NetApp Files podporu. Další informace najdete v článku [CommVault IntelliSnap 11,21](https://documentation.commvault.com/11.21/essential/116350_getting_started_with_backup_and_restore_operations_for_azure_netapp_file_services_smb_shares_and_nfs_shares.html) .


### <a name="back-up-the-snapshot-using-azure-blob-storage"></a>Zálohování snímku pomocí Azure Blob Storage
Zálohování do Azure Blob Storage je cenově výhodné a rychlou metodou pro ukládání záloh snímků úložiště databáze HANA založených na ANF. Chcete-li uložit snímky do úložiště objektů BLOB v Azure, je upřednostňovaným nástrojem AZCopy. Stáhněte si nejnovější verzi tohoto nástroje a nainstalujte ji například do adresáře bin, kde je nainstalovaný skript Pythonu z GitHubu.
Stáhněte si nejnovější nástroj AzCopy:

```
root # wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1
Saving to: ‘azcopy_v10.tar.gz’
```

Nejpokročilejší funkcí je možnost synchronizace. Pokud použijete možnost synchronizace, AzCopy ponechá synchronizaci zdrojového a cílového adresáře. Použití parametru **--Delete-Destination** je důležité. Bez tohoto parametru AzCopy neodstraňuje soubory v cílové lokalitě a využití prostoru na cílové straně by mělo růst. Vytvořte kontejner objektů blob bloku v účtu úložiště Azure. Pak vytvořte klíč SAS pro kontejner objektů BLOB a synchronizujte složku snímku s kontejnerem objektů BLOB v Azure.

Například pokud by měl být denní snímek synchronizován do kontejneru objektů blob Azure, aby chránil data. A pouze jeden snímek by měl být zachován, příkaz níže lze použít.

```
root # > azcopy sync '/hana/data/SID/mnt00001/.snapshot' 'https://azacsnaptmytestblob01.blob.core.windows.net/abc?sv=2021-02-02&ss=bfqt&srt=sco&sp=rwdlacup&se=2021-02-04T08:25:26Z&st=2021-02-04T00:25:26Z&spr=https&sig=abcdefghijklmnopqrstuvwxyz' --recursive=true --delete-destination=true
```

## <a name="next-steps"></a>Další kroky
Přečtěte si článek:

- [SAP HANA vysoké dostupnosti pro virtuální počítače Azure](./sap-hana-availability-overview.md)