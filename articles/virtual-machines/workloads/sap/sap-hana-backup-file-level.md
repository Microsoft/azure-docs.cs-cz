---
title: SAP HANA Azure Backup na úrovni souborů | Microsoft Docs
description: Existují dvě hlavní možnosti zálohování SAP HANA na virtuálních počítačích Azure. Tento článek se zabývá SAP HANA Azure Backup na úrovni souborů.
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: juergent
ms.openlocfilehash: 0ff226a156721382a289af0f2a8a0f898a57bab9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101667972"
---
# <a name="sap-hana-azure-backup-on-file-level"></a>SAP HANA Azure Backup na úrovni souborů

## <a name="introduction"></a>Úvod

V tomto článku se seznámíte s [příručkou pro zálohování SAP HANA v Azure Virtual Machines](./sap-hana-backup-guide.md), která poskytuje přehled a informace o tom, jak začít, a další podrobnosti o Azure Backupch služby a snímcích úložiště. 

Různé typy virtuálních počítačů v Azure umožňují, aby byl připojený jiný počet virtuálních pevných disků. Přesné podrobnosti jsou popsány v části [velikosti pro virtuální počítače se systémem Linux v Azure](../../sizes.md). Pro testy uvedené v této dokumentaci jsme použili GS5 virtuální počítač Azure, který umožňuje 64 připojených datových disků. V případě větších SAP HANA systémů už může být u souborů dat a protokolů, které jsou v kombinaci s softwarovým obložením pro optimální propustnost v/v, zavedený velký počet disků. Další podrobnosti o navrhovaných konfiguracích disků pro nasazení SAP HANA na virtuálních počítačích Azure najdete v článku [SAP HANA konfigurace úložiště virtuálních počítačů Azure](./hana-vm-operations-storage.md). Tato doporučení zahrnují taky doporučení na místo na disku pro místní zálohování.

Standardní způsob, jak spravovat zálohování nebo obnovení na úrovni souboru, je zálohování na základě souborů prostřednictvím SAP HANA studia nebo prostřednictvím příkazů SAP HANA SQL. Další informace najdete v článku [SAP HANA SQL a Přehled systémových zobrazení](https://help.sap.com/viewer/4fe29514fd584807ac9f2a04f6754767/2.0.05/en-US/3859e48180bb4cf8a207e15cf25a7e57.html).

![Na tomto obrázku je znázorněno dialogové okno položky nabídky zálohování v SAP HANA studiu](media/sap-hana-backup-file-level/backup-menue-dialog.png)

Na tomto obrázku je znázorněno dialogové okno položky nabídky zálohování v SAP HANA studiu. Když zvolíte typ &quot; souboru, &quot; jeden musí zadat cestu v systému souborů, kde SAP HANA zapisuje záložní soubory. Obnovení funguje stejným způsobem.

I když je tato volba zvukem jednoduchá a přímá, je k dispozici nějaký ohled. Virtuální počítač Azure má omezení počtu datových disků, které se dají připojit. V závislosti na velikosti požadavků databáze a propustnosti disku nemusí být možné ukládat SAP HANA záložní soubory do systémů souborů virtuálního počítače. to může zahrnovat softwarové obložení na více datových disků. V tomto článku jsou k dispozici různé možnosti přesunutí těchto souborů zálohy a Správa omezení velikosti souborů a výkonu při zpracování terabajtů dat.

Další možností, která nabízí větší volnost v oblasti celkové kapacity, je úložiště objektů BLOB v Azure. I když je jeden objekt BLOB taky omezený na 1 TB, celková kapacita jednoho kontejneru objektů BLOB je v současnosti 500 TB. Kromě toho zákazníkům dává možnost vybrat si, že se nazývá &quot; studená služba &quot; BLOB Storage, která má cenovou výhodu. Podrobnosti o studeném úložišti objektů BLOB najdete v tématu [Azure Blob Storage: horká, studená a archivní úroveň přístupu](../../../storage/blobs/storage-blob-storage-tiers.md?tabs=azure-portal) .

Pro zajištění vyšší bezpečnosti použijte geograficky replikovaný účet úložiště pro uložení záloh SAP HANA. Podrobnosti o redundanci úložiště a replikaci úložiště najdete v tématu [Azure Storage redundance](../../../storage/common/storage-redundancy.md) .

Jedna z nich by mohla umístit vyhrazené virtuální pevné disky pro SAP HANA zálohy ve vyhrazeném účtu úložiště záloh, který je geograficky replikovaný. Nebo jinak může zkopírovat virtuální pevné disky, které udržují SAP HANA zálohy do geograficky replikovaného účtu úložiště, nebo do účtu úložiště, který je v jiné oblasti.

## <a name="azure-blobxfer-utility-details"></a>Podrobnosti o nástroji Azure blobxfer

K ukládání adresářů a souborů v Azure Storage může jeden použít CLI nebo PowerShell nebo vyvíjet nástroj pomocí jedné ze [sad SDK Azure](https://azure.microsoft.com/downloads/). Pro kopírování dat do služby Azure Storage je k dispozici také nástroj AzCopy připravený k použití. (Další informace najdete v tématu [přenos dat pomocí nástroje AzCopy Command-Line](../../../storage/common/storage-use-azcopy-v10.md)).

Proto se blobxfer použil k kopírování souborů zálohy SAP HANA. Je to open source, který používá mnoho zákazníků v produkčních prostředích a je dostupný na [GitHubu](https://github.com/Azure/blobxfer). Tento nástroj umožňuje jedné kopírování dat přímo do úložiště objektů blob Azure nebo sdílené složky Azure. Nabízí také řadu užitečných funkcí, jako je MD5 hash nebo automatické paralelismuy při kopírování adresáře s více soubory.

## <a name="sap-hana-backup-performance"></a>Výkon zálohování SAP HANA
V této kapitole jsou popsány požadavky na výkon. Dosažená čísla nemusí představovat nejaktuálnější stav, protože existuje stabilní vývoj pro dosažení lepší propustnosti Azure Storage. V důsledku toho byste měli provádět jednotlivé testy pro svou konfiguraci a oblast Azure.

![Tento snímek obrazovky je konzola SAP HANA Backup v SAP HANA studiu](media/sap-hana-backup-file-level/backup-console-hana-studio.png)

Na tomto snímku obrazovky se zobrazuje konzola SAP HANA Backup nástroje SAP HANA Studio. K provedení zálohy 230 GB na jednom disku úložiště Azure HDD úrovně Standard připojeném k virtuálnímu počítači HANA trvalo použití systému souborů XFS na jednom disku přibližně 42 minut.

![Tento snímek obrazovky je YaST na testovacím virtuálním počítači SAP HANA.](media/sap-hana-backup-file-level/one-backup-disk.png)

Tento snímek obrazovky je YaST na testovacím virtuálním počítači SAP HANA. Pro SAP HANA Backup můžete zobrazit jeden disk o 1 TB. Zálohování na 230 GB trvalo přibližně 42 minut. Kromě toho 5 200 GB disků bylo připojeno a byl vytvořen softwarový md0 RAID s prokládáním na těchto pěti datových discích Azure.

![Opakování stejné zálohy u softwarového pole RAID s prokládáním v pěti připojených datových discích Azure Standard Storage](media/sap-hana-backup-file-level/five-backup-disks.png)

Opakuje stejnou zálohu na softwarovém poli RAID s prokládáním přes pět připojených datových disků Azure úrovně Standard, což je doba zálohování od 42 minut až do 10 minut. K virtuálnímu počítači byly připojeny disky bez ukládání do mezipaměti. Toto cvičení ukazuje důležitost propustnosti zápisu na disk pro dosažení dobrého času zálohování. Můžete přepnout na Azure SSD úrovně Standard Storage nebo Azure Premium Storage a urychlit tak proces optimálního výkonu. Obecně se úložiště Azure Standard HDD nedoporučuje a používá se jenom pro demonstrační účely. Doporučujeme, abyste pro produkční systémy používali minimálně úložiště Azure SSD úrovně Standard nebo Azure Premium Storage.

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>Kopírování souborů SAP HANA zálohování do Azure Blob Storage
Údaje o výkonu, čísla doby trvání zálohování a kopírovaná čísla doba kopírování nemusí představovat nejnovější stav technologie Azure. Microsoft neustále vylepšuje úložiště Azure, aby poskytoval větší propustnost a nižší latence. Proto jsou tato čísla pouze pro demonstrační účely. V oblasti Azure, kterou si zvolíte, je potřeba otestovat své individuální potřeby, abyste mohli posoudit, že je to pro vás nejvhodnější metoda.

Další možností pro rychlé uložení SAP HANAch záložních souborů je úložiště objektů BLOB v Azure. Jeden kontejner objektů BLOB má omezení o velikosti přibližně 500 TB, dostatečně pro SAP HANA systémy, používá M32ts, M32ls, M64ls a GS5 typy virtuálních počítačů Azure, aby se zajistilo dostatečné SAP HANA zálohování. Zákazníci si můžou vybrat mezi &quot; horkou &quot; a &quot; studeným &quot; úložištěm objektů BLOB (viz [Azure Blob Storage: horká, studená a archivní úroveň přístupu](../../../storage/blobs/storage-blob-storage-tiers.md?tabs=azure-portal)).

Pomocí nástroje blobxfer je snadné zkopírovat SAP HANA záložní soubory přímo do úložiště objektů BLOB v Azure.

![Tady vidíte soubory úplné SAP HANA zálohování souborů.](media/sap-hana-backup-file-level/list-of-backups.png)

Můžete zobrazit soubory úplné SAP HANA zálohování souborů. Z těchto čtyř souborů má největší velikost přibližně 230 GB.

![Přibližně 3000 sekund trvalo zkopírovat 230 GB do kontejneru objektů BLOB ve standardním účtu úložiště Azure](media/sap-hana-backup-file-level/copy-duration-blobxfer.png)

V počátečním testu se nepoužívá hodnota hash MD5. pro zkopírování 230 GB do kontejneru objektů BLOB standardního úložiště Azure trvalo přibližně 3000 sekund.

Konzola zálohování HANA Studio umožňuje jednu omezit maximální velikost souborů záložních souborů HANA. Ve vzorovém prostředí se zvýšil výkon tak, že má více menších souborů zálohy místo jednoho velkého 230 souboru.

Nastavení omezení velikosti záložního souboru na straně HANA&#39;t vylepšit čas zálohování, protože soubory jsou zapsány sekvenčně. Omezení velikosti souboru bylo nastaveno na 60 GB, takže záloha vytvořila čtyři soubory velkých datových souborů místo jednoho souboru 230-GB. Použití několika záložních souborů může být nezbytné pro zálohování databází HANA, pokud vaše cíle zálohování mají omezení velikosti souborů objektů BLOB.

![Pokud chcete otestovat paralelismus nástroje blobxfer, je maximální velikost souboru pro zálohy HANA nastavená na 15 GB.](media/sap-hana-backup-file-level/parallel-copy-multiple-backup-files.png)

Pokud chcete otestovat paralelismus nástroje blobxfer, je maximální velikost souborů pro zálohy HANA nastavená na 15 GB, což vedlo k 19 záložním souborům. Tato konfigurace zaznamenala čas, kdy blobxfer kopírovat 230 GB do úložiště objektů blob Azure od 3000 sekund až po 875 sekund.

Při kopírování záloh provedených na místních discích do jiných umístění, jako je Azure Blob Storage, pamatujte na to, že šířka pásma využitá procesem paralelního kopírování je účetním serverem a kvótou úložiště pro jednotlivé typy virtuálních počítačů. V důsledku toho je potřeba vyrovnávat dobu trvání kopírování oproti síti a šířce pásma úložiště, které vyžaduje normální zatížení, které běží na virtuálním počítači. 


## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>Kopírovat SAP HANA záložní soubory do sdílené složky NFS

Microsoft Azure nabízí nativní sdílené složky systému souborů NFS prostřednictvím [Azure NetApp Files](https://azure.microsoft.com/services/netapp/). V kapacitě můžete pro ukládání a správu záloh vytvářet různé svazky v desítkách TBs. Tyto svazky můžete také na základě technologie NetApp snímků. Azure NetApp Files (ANF) se nabízí ve třech různých úrovních služeb, které poskytují jinou propustnost úložiště. Další podrobnosti najdete v článku [úrovně služeb pro Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). Můžete vytvořit a připojit svazek systému souborů NFS z ANF, jak je popsáno v článku [rychlý Start: nastavení Azure NetApp Files a vytvoření svazku NFS](../../../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md?tabs=azure-portal).

Kromě použití nativních svazků NFS systému Azure prostřednictvím ANF existují různé možnosti vytváření vlastních nasazení, která poskytují sdílené složky systému souborů NFS v Azure. Všechny mají nevýhodu, kterou potřebujete k nasazení a správě těchto řešení sami. Některé z těchto možností jsou popsány v těchto článcích:

- [Vysoká dostupnost pro NFS na virtuálních počítačích Azure na SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md)
- [GlusterFS na virtuálních počítačích Azure s Red Hat Enterprise Linuxem pro SAP NetWeaver](./high-availability-guide-rhel-glusterfs.md)

Sdílené složky NFS vytvořené pomocí výše popsaných prostředků se dají použít k přímému spuštění záloh HANA proti nebo kopírování záloh provedených na místních discích do těchto sdílených složek NFS.

> [!NOTE]
> SAP HANA podporu systému souborů NFS v3 a NFS v4. x. Žádný jiný formát jako SMB se systémem souborů CIFS není podporován pro zápis záloh HANA proti. Viz také [SAP Support – poznámka #1820529](https://launchpad.support.sap.com/#/notes/1820529)

## <a name="copy-sap-hana-backup-files-to-azure-files"></a>Kopírování souborů SAP HANA zálohování do souborů Azure

Sdílenou složku služby soubory Azure je možné připojit v rámci virtuálního počítače Azure Linux. Článek [použití služby Azure File Storage se systémem Linux](../../../storage/files/storage-how-to-use-files-linux.md) poskytuje podrobné informace o tom, jak provést konfiguraci. Pro omezení souborů Azure nebo souborů Azure Premium si přečtěte článek věnované [škálovatelnosti a cílům výkonu Azure Files](../../../storage/files/storage-files-scale-targets.md).

> [!NOTE]
> Služba SMB se systémem souborů CIFS není podporovaná SAP HANA k zápisu záloh HANA proti. Viz také [SAP Support note #1820529](https://launchpad.support.sap.com/#/notes/1820529). V důsledku toho můžete toto řešení použít jenom jako konečný cíl zálohování databáze HANA, které bylo provedeno přímo na místních připojených discích.
> 

V testu, který se provádí se soubory Azure, ne se soubory Azure Premium, trvalo přibližně 929 sekund na kopírování 19 záložních souborů s celkovým objemem 230 GB. Očekáváme, že čas, který využíváme Azure Premium Files, je lepší. Je ale potřeba mít na paměti, že je třeba mít na paměti, že je potřeba si vyvážit, že si budete muset vyrovnávat zájmy rychlého kopírování s požadavky, které vaše zatížení má na šířku pásma Vzhledem k tomu, že každý typ virtuálního počítače Azure vynutil kvótu šířky pásma sítě, musíte zůstat v rozsahu této kvóty s úlohou a kopií záložních souborů.

![Tento obrázek ukazuje, že trvalo přibližně 929 sekund až po zkopírování 19 SAP HANA záložních souborů.](media/sap-hana-backup-file-level/parallel-copy-to-azure-files.png)


Ukládání SAP HANA záložních souborů v souborech Azure může být zajímavou možností. Obzvláště díky lepší latenci a propustnosti souborů Azure Premium.

## <a name="next-steps"></a>Další kroky
* [Průvodce zálohováním pro SAP HANA v Azure Virtual Machines](sap-hana-backup-guide.md) poskytuje přehled a informace o tom, jak začít.
* Informace o tom, jak vytvořit vysokou dostupnost a naplánovat zotavení po havárii SAP HANA v Azure (velké instance), najdete v tématu [SAP Hana (velké instance) vysoká dostupnost a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md).
