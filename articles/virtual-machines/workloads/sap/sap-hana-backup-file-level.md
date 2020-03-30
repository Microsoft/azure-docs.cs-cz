---
title: Zálohování SAP HANA Azure na úrovni souborů | Dokumenty společnosti Microsoft
description: Existují dvě hlavní možnosti zálohování pro SAP HANA na virtuálních počítačích Azure, tento článek pokrývá SAP HANA Azure Backup na úrovni souboru
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: juergent
ms.openlocfilehash: 93b67936166eb73db5e9a15db42c2c6135794108
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78271391"
---
# <a name="sap-hana-azure-backup-on-file-level"></a>Zálohování SAP HANA Azure na úrovni souborů

## <a name="introduction"></a>Úvod

Tento článek je související článek [zálohování průvodce pro SAP HANA na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide), který poskytuje přehled a informace o začínáme a další podrobnosti o azure zálohování služby a snímky úložiště. 

Různé typy virtuálních počítačů v Azure umožňují jiný počet připojených virtuálních disponiálů. Přesné podrobnosti jsou [popsány ve velikostech pro virtuální počítače s Linuxem v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes). Pro testy uvedené v této dokumentaci jsme použili virtuální počítač GS5 Azure, který umožňuje 64 připojených datových disků. U větších systémů SAP HANA může být již značný počet disků přijata pro data a soubory protokolu, případně v kombinaci s prokládání softwaru pro optimální propustnost disku VO. Další podrobnosti o navržených konfiguracích disků pro nasazení SAP HANA na virtuálních počítačích Azure načtěte v článku [konfigurace virtuálního úložiště virtuálních počítačů SAP HANA Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage). Uvedená doporučení jsou zahrnuta také doporučení místa na disku pro místní zálohy.

Standardní způsob správy zálohování/obnovení na úrovni souboru je pomocí zálohování založeného na souborech prostřednictvím sap HANA Studio nebo prostřednictvím příkazů SAP HANA SQL. Další informace naleznete v článku [ODKAZ NA SAP HANA SQL and System Views .](https://help.sap.com/hana/SAP_HANA_SQL_and_System_Views_Reference_en.pdf)

![Tento obrázek znázorňuje dialog položky nabídky zálohování v aplikaci SAP HANA Studio](media/sap-hana-backup-file-level/backup-menue-dialog.png)

Tento obrázek znázorňuje dialogové okno položky nabídky zálohování v aplikaci SAP HANA Studio. Při výběru &quot;&quot; typu souboru je třeba zadat cestu v systému souborů, kde SAP HANA zapisuje záložní soubory. Obnovení funguje stejným způsobem.

I když tato volba zní jednoduše a rovně vpřed, tam jsou některé úvahy. Virtuální počítač Azure má omezení počtu datových disků, které lze připojit. V závislosti na velikosti databáze a požadavcích na propustnost disku nemusí být kapacita pro ukládání záložních souborů SAP HANA do souborových systémů virtuálního počítače, v závislosti na velikosti databáze a požadavcích na propustnost disku, což může zahrnovat softwarové prokládání na více datových discích. Různé možnosti pro přesunutí těchto záložních souborů a správu omezení velikosti souboru a výkonu při zpracování terabajtů dat jsou uvedeny dále v tomto článku.

Další možností, která nabízí větší svobodu, pokud jde o celkovou kapacitu, je úložiště objektů blob Azure. Zatímco jeden objekt blob je také omezena na 1 TB, celková kapacita jednoho kontejneru objektu blob je aktuálně 500 TB. Navíc dává zákazníkům možnost vybrat si &quot;takzvané&quot; skvělé úložiště objektů blob, které má nákladovou výhodu. Podrobnosti o skvělém úložišti objektů blob v [Azure: horké, studené a archivní vrstvy přístupu](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal) najdete v tématu Azure Blob storage.

Z důvodu větší bezpečnosti použijte k uložení záloh SAP HANA účet geograficky replikovaného úložiště. Podrobnosti o redundanci úložiště a replikaci úložiště najdete v tématu Redundance azure [storage.](https://docs.microsoft.com/azure/storage/common/storage-redundancy)

Jeden by mohl umístit vyhrazené Virtuální počítače pro zálohy SAP HANA ve vyhrazeném účtu úložiště záloh, který je geograficky replikován. Nebo jinak jeden mohl zkopírovat Virtuální počítače, které udržují zálohy SAP HANA na účet geograficky replikované úložiště nebo na účet úložiště, který je v jiné oblasti.

## <a name="azure-blobxfer-utility-details"></a>Podrobnosti nástroje Blobxfer Azure

Chcete-li ukládat adresáře a soubory v úložišti Azure, jeden můžete použít cli nebo PowerShell, nebo vyvinout nástroj pomocí jednoho z [Sad Azure SDK .](https://azure.microsoft.com/downloads/) K dispozici je také nástroj pro použití připravený k použití, AzCopy, pro kopírování dat do úložiště Azure. (viz [Přenos dat pomocí nástroje příkazového řádku AzCopy).](../../../storage/common/storage-use-azcopy.md)

Proto blobxfer byl použit pro kopírování SAP HANA záložní soubory. Je to open source, používá mnoho zákazníků v produkčním prostředí a je k dispozici na [GitHubu](https://github.com/Azure/blobxfer). Tento nástroj umožňuje kopírovat data přímo do úložiště objektů blob Azure nebo sdílené složky Azure. Nabízí také řadu užitečných funkcí, jako je hash md5 nebo automatický paralelismus při kopírování adresáře s více soubory.

## <a name="sap-hana-backup-performance"></a>Výkon zálohování SAP HANA
V této kapitole jsou popsány důležité informace o výkonu. Dosažená čísla nemusí představovat nejnovější stav, protože je stabilní vývoj k dosažení lepší propustnost k úložišti Azure. V důsledku toho byste měli provést individuální testy pro konfiguraci a oblast Azure.

![Tento snímek obrazovky je ze zálohovací konzole SAP HANA v SAP HANA Studio](media/sap-hana-backup-file-level/backup-console-hana-studio.png)

Tento snímek obrazovky ukazuje záložní konzolu SAP HANA aplikace SAP HANA Studio. Trvalo asi 42 minut provést zálohu 230 GB na jednom disku úložiště Azure Standard HDD připojeném k virtuálnímu počítači HANA pomocí systému souborů XFS na jednom disku.

![Tento snímek obrazovky je yast na SAP HANA test virtuálního domu](media/sap-hana-backup-file-level/one-backup-disk.png)

Tento snímek obrazovky je YaST na sap hana test virtuálního počítači. Můžete vidět jeden disk 1 TB pro zálohování SAP HANA. Zálohování 230 GB trvalo asi 42 minut. Kromě toho bylo připojeno pět 200GB disků a vytvořen software RAID md0, s prokládáním nad těchto pět datových disků Azure.

![Opakování stejné zálohy na softwarovém raidu s prokládáním na pěti připojených datových discích Azure pro standardní úložiště](media/sap-hana-backup-file-level/five-backup-disks.png)

Opakování matné zálohy na softwarovém raidu s prokládáním na pěti připojených datových discích standardního úložiště Azure přineslo dobu zálohování ze 42 minut na 10 minut. Disky byly připojeny bez ukládání do mezipaměti k virtuálnímu počítače. Toto cvičení ukazuje důležitost propustnost zápisu disku pro dosažení dobré doby zálohování. Můžete přepnout na Azure Standard SSD storage nebo Azure Premium Storage, abyste dále urychlili proces pro optimální výkon. Obecně platí, že standardní úložiště pevných disků Azure se nedoporučuje a používá se pouze pro demonstrační účely. Doporučení je použít minimálně Azure Standard SSD storage nebo Azure Premium Storage pro produkční systémy.

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>Kopírování záložních souborů SAP HANA do úložiště objektů blob Azure
Čísla výkonu, čísla doby zálohování a čísla doby trvání kopírování, která jsou uvedena, nemusí představovat nejnovější stav technologie Azure. Microsoft neustále vylepšuje úložiště Azure, aby poskytovalo větší propustnost a nižší latenci. Proto jsou čísla pouze pro demonstrační účely. Musíte otestovat individuální potřeby v oblasti Azure podle vašeho výběru, aby bylo možné posoudit pomocí metody je pro vás nejlepší.

Další možností rychlého ukládání záložních souborů SAP HANA je úložiště objektů blob Azure. Jeden kontejner blob má limit přibližně 500 TB, dost pro systémy SAP HANA, pomocí M32ts, M32ls, M64ls a GS5 Typy virtuálních počítačů Azure, udržovat dostatek záloh SAP HANA. Zákazníci mají &quot;na&quot; &quot;výběr&quot; mezi aktivním a studeným úložištěm objektů blob (viz [úložiště objektů Blob Azure: horké, studené a archivní úrovně přístupu).](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal)

Pomocí nástroje blobxfer je snadné zkopírovat záložní soubory SAP HANA přímo do úložiště objektů blob Azure.

![Zde je možné vidět soubory plné zálohy souborů SAP HANA](media/sap-hana-backup-file-level/list-of-backups.png)

Můžete vidět soubory úplné zálohy souborů SAP HANA. Ze čtyř souborů má největší velikost zhruba 230 GB.

![Zkopírování 230 GB do kontejneru blob účtu standardního úložiště Azure trvalo zhruba 3000 sekund.](media/sap-hana-backup-file-level/copy-duration-blobxfer.png)

Nepoužití hash md5 v počátečním testu, trvalo zhruba 3000 sekund zkopírovat 230 GB do kontejneru blob účtu standardníúložiště Azure.

Konzola zálohování HANA Studio umožňuje omezit maximální velikost souboru záložních souborů HANA. V ukázkovém prostředí zlepšila výkon tím, že měla více menších záložních souborů namísto jednoho velkého souboru o velikosti 230 GB.

Nastavení limitu velikosti záložního souboru na straně HANA&#39;nezlepší dobu zálohování, protože soubory jsou zapsány postupně. Limit velikosti souboru byl nastaven na 60 GB, takže záloha vytvořila čtyři velké datové soubory namísto jednoho souboru o velikosti 230 GB. Použití více záložních souborů se může stát nutností pro zálohování databází HANA, pokud vaše cíle zálohování mají omezení velikosti souborů velikosti objektů blob.

![Chcete-li otestovat paralelismus nástroje blobxfer, byla maximální velikost souboru pro zálohy HANA nastavena na 15 GB](media/sap-hana-backup-file-level/parallel-copy-multiple-backup-files.png)

Chcete-li otestovat paralelismus nástroje blobxfer, maximální velikost souboru pro zálohy HANA byla pak nastavena na 15 GB, což mělo za následek 19 záložních souborů. Tato konfigurace přinesla čas pro blobxfer zkopírovat 230 GB do úložiště objektů blob Azure z 3000 sekund až 875 sekund.

Při zkoumání kopírování záloh provedených na místních discích do jiných umístění, jako je úložiště objektů blob Azure, mějte na paměti, že šířka pásma používaná případným paralelním procesem kopírování se vyrovnává se sítí nebo kvótou úložiště vašeho jednotlivého typu virtuálního počítače. V důsledku toho je třeba vyvážit dobu trvání kopie proti šířce pásma sítě a úložiště, kterou vyžaduje normální úloha spuštěná ve virtuálním provozu. 


## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>Kopírování záložních souborů SAP HANA do sdílené složky systému souborů NFS

Microsoft Azure nabízí nativní sdílené složky systému souborů NFS prostřednictvím [souborů Azure NetApp](https://azure.microsoft.com/services/netapp/). Můžete vytvořit různé svazky desítek TB kapacity pro ukládání a správu záloh. Můžete také snímek těchto svazků na základě technologie NetApp. Soubory Azure NetApp (ANF) se nabízí ve třech různých úrovních služeb, které poskytují různou propustnost úložiště. Další podrobnosti našlápne článek [Úrovně služeb pro soubory Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). Svazek systému souborů NF můžete vytvořit a připojit z anf, jak je popsáno v článku [Úvodní příručka: Nastavení souborů Azure NetApp a vytvoření svazku systému souborů NFS](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes?tabs=azure-portal).

Kromě použití nativních svazků systému sdílení systému sítě Azure prostřednictvím ANF existují různé možnosti vytváření vlastních nasazení, která poskytují sdílené složky systému nfs v Azure. Všechny mají tu nevýhodu, kterou potřebujete k nasazení a správě těchto řešení sami. Některé z těchto možností jsou popsány v těchto článcích:

- [Vysoká dostupnost pro systém windows na virtuálních počítačích Azure na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)
- [GlusterFS na virtuálních počítačích Azure s Red Hat Enterprise Linuxem pro SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)

Sdílené složky systému souborů NFS vytvořené výše popsanými prostředky lze použít k přímému spuštění záloh HANA proti nebo ke kopírování záloh, které byly provedeny proti místním diskům do těchto sdílených složek systému souborů NFS.

> [!NOTE]
> SAP HANA podporuje nfs v3 a NFS v4.x. Jakýkoli jiný formát, jako je SMB se systémem souborů CIFS, není podporován pro zápis záloh HANA. Viz také [poznámka podpory SAP #1820529](https://launchpad.support.sap.com/#/notes/1820529)

## <a name="copy-sap-hana-backup-files-to-azure-files"></a>Kopírování záložních souborů SAP HANA do souborů Azure

Je možné připojit sdílené složky Azure Files uvnitř virtuálního počítače Azure Linux. Článek [Jak používat úložiště souborů Azure s Linuxem](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-linux) obsahuje podrobnosti o tom, jak provést konfiguraci. Omezení souborů Azure nebo souborů Azure premium narážka na článek [Škálovatelnost souborů Azure a cíle výkonu](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets).

> [!NOTE]
> SMB se souborovým systémem CIFS není podporován SAP HANA pro zápis záloh HANA. Viz také [poznámka podpory SAP #1820529](https://launchpad.support.sap.com/#/notes/1820529). V důsledku toho můžete toto řešení použít pouze jako konečný cíl zálohy databáze HANA, která byla provedena přímo proti místním připojeným diskům.
> 

V testu provedeném proti souborům Azure, ne soubory Azure Premium trvalo přibližně 929 sekund zkopírovat 19 záložních souborů s celkovým objemem 230 GB. Očekáváme, že čas pomocí souborů Azure Premium bude mnohem lepší. Je však třeba mít na paměti, že je třeba vyvážit zájmy rychlé kopie s požadavky, které má vaše pracovní vytížení na šířku pásma sítě. Vzhledem k tomu, že každý typ virtuálního počítače Azure vynucuje kvótu šířky pásma sítě, musíte zůstat v rozsahu této kvóty s vaší úlohou a kopií záložních souborů.

![Tento obrázek ukazuje, že kopírování 19 záložních souborů SAP HANA trvalo přibližně 929 sekund.](media/sap-hana-backup-file-level/parallel-copy-to-azure-files.png)


Ukládání záložních souborů SAP HANA v souborech Azure může být zajímavá možnost. Zejména s vylepšenou latencí a propustností souborů Azure Premium.

## <a name="next-steps"></a>Další kroky
* [Průvodce zálohováním pro SAP HANA na virtuálních počítačích Azure](sap-hana-backup-guide.md) poskytuje přehled a informace o začínáme.
* Informace o tom, jak vytvořit vysokou dostupnost a plán pro zotavení po havárii SAP HANA v Azure (velké instance), najdete v [tématu SAP HANA (velké instance) vysokou dostupnost a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md).
