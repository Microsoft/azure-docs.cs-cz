---
title: Přesunutí úložiště sálových počítačů do úložiště Azure
description: Masivně škálovatelné prostředky úložiště Azure můžou pomoci organizacím založeným na sálových počítačích migrovat a modernizovat aplikace IBM z14.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: storage
ms.openlocfilehash: 86419811cdf2c11204caae0ca5bf6f65fba063d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76288910"
---
# <a name="move-mainframe-storage-to-azure"></a>Přesunutí úložiště sálových počítačů do Azure

Chcete-li v Microsoft Azure spouštět úlohy sálových počítačů, musíte vědět, jak se možnosti sálového počítače srovnávají s Azure. Masivně škálovatelné prostředky úložiště mohou organizacím pomoci začít se modernizovat, aniž by opustily aplikace, na které spoléhají.

Azure poskytuje funkce podobné sálovým počítačům a úložnou kapacitu, která je srovnatelná se systémy založenými na IBM z14 (nejnovější model k tomuto zápisu). V tomto článku se dozvíte, jak získat srovnatelné výsledky v Azure.

## <a name="mainframe-storage-at-a-glance"></a>Přehled úložiště sálových počítačů

Sálový počítač IBM charakterizuje úložiště dvěma způsoby. Prvním z toho je paměťové zařízení s přímým přístupem (DASD). Druhým je sekvenční úložiště. Pro správu úložiště poskytuje sálový počítač subsystém Správa úložiště datového zařízení (DFSMS). Spravuje přístup k datům k různým úložným zařízením.

[DASD](https://en.wikipedia.org/wiki/Direct-access_storage_device) odkazuje na samostatné zařízení pro sekundární (ne v paměti) úložiště, které umožňuje jedinečnou adresu, která má být použita pro přímý přístup k datům. Původně se termín DASD vztahoval na rotující disky, magnetické bubny nebo datové buňky. Nyní však může termín platit také pro ssd paměťová zařízení (SSD), sítě s kapacitou úložiště (SAN), úložiště připojené k síti (NAS) a optické jednotky. Pro účely tohoto dokumentu DASD odkazuje na rotující disky, SAN a SSD.

Na rozdíl od úložiště DASD sesekvenční úložiště na sálovém počítači odkazuje na zařízení, jako jsou páskové jednotky, kde jsou data přístupná z počátečního bodu a poté čtete nebo zapisujte v řádku.

Úložná zařízení jsou obvykle připojena pomocí připojení fiber (FICON) nebo jsou přístupná přímo na sběrnici IO sálového počítače pomocí [HiperSockets](https://www.ibm.com/support/knowledgecenter/zosbasics/com.ibm.zos.znetwork/znetwork_85.htm), což je technologie IBM pro vysokorychlostní komunikaci mezi oddíly na serveru s hypervizátorem.

Většina sálových systémů odděluje úložiště do dvou typů:

- *Online úložiště* (také považované za horké) je potřebné pro každodenní provoz. K tomuto účelu se obvykle používá úložiště DASD. Pro tento účel však lze také použít sekvenční úložiště, jako je například denní zálohování na pásku (logické nebo fyzické).

- *Archivní úložiště* (označované také jako chladírenské úložiště) není zaručeno, že bude namontováno v daném okamžiku. Místo toho je připojen a přistupovat podle potřeby. Úložiště archivu je často implementováno pomocí sekvenčních páskových záloh (logických nebo fyzických) pro úložiště.

## <a name="mainframe-versus-io-latency-and-iops"></a>Sálový počítač versus latence vi a viopy

Sálové počítače se často používají pro aplikace, které vyžadují vysoký výkon vi a nízké latence vi. Mohou to provést pomocí připojení FICON k iO zařízení a HiperSockets. Při HiperSockets se používají k připojení aplikací a zařízení přímo k kanálu iO sálového počítače, latence v mikrosekundách lze dosáhnout.

## <a name="azure-storage-at-a-glance"></a>Přehled úložišť Azure

Možnosti infrastruktury Azure jako služby[(IaaS)](https://azure.microsoft.com/overview/what-is-iaas/)pro úložiště poskytují srovnatelnou kapacitu sálových počítačů.

Microsoft nabízí úložiště v hodnotě petabajtů pro aplikace hostované v Azure a máte několik možností úložiště. Ty sahají od úložiště SSD pro vysoký výkon až po nízkonákladové úložiště objektů blob pro velkokapacitní úložiště a archivy. Azure navíc poskytuje možnost redundance dat pro úložiště – něco, co vyžaduje větší úsilí k nastavení v prostředí sálových počítačů.

Úložiště Azure je k dispozici jako [Azure Disky](/azure/virtual-machines/windows/managed-disks-overview), [Azure Files](/azure/storage/files/storage-files-introduction)a [Azure Blobs](/azure/storage/blobs/storage-blobs-overview) jako následující tabulka shrnuje. Přečtěte si další informace o [tom, kdy použít jednotlivé .](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)

<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Typ</th><th>Popis</th><th>Použijte, pokud chcete:</th></tr>
</thead>
<tbody>
<tr><td>Soubory Azure
</td>
<td>
Poskytuje rozhraní SMB, klientské knihovny a rozhraní <a href="https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api">REST,</a> které umožňuje přístup odkudkoli k uloženým souborům.
</td>
<td><ul>
<li>Výtah a přesunout aplikaci do cloudu, když aplikace používá nativní souborového systému API pro sdílení dat mezi ním a dalšími aplikacemi spuštěnými v Azure.</li>
<li>Ukládat vývojové a ladicí nástroje, které je potřeba přistupovat z mnoha virtuálních aplikací.</li>
</ul>
</td>
</tr>
<tr><td>Objekty blob Azure
</td>
<td>Poskytuje klientské knihovny a rozhraní <a href="https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api">REST,</a> které umožňuje nestrukturovaná data, které mají být uloženy a přistupovat v masivním měřítku v blob bloku. Podporuje také <a href="/azure/storage/blobs/data-lake-storage-introduction">Azure Data Lake Storage Gen2</a> pro podniková řešení pro analýzu velkých objemů dat.
</td>
<td><ul>
<li>Podpora streamování a náhodného přístupu scénáře v aplikaci.</li>
<li>Mějpřístupně k datům aplikací odkudkoli.</li>
<li>Vytvořte podnikové datové jezero v Azure a provádějte analýzu velkých objemů dat.</li>
</ul></td>
</tr>
<tr><td>Disky Azure
</td>
<td>Poskytuje klientské knihovny a rozhraní <a href="https://docs.microsoft.com/rest/api/compute/disks">REST,</a> které umožňuje trvale ukládat data a přistupovat k nim z připojeného virtuálního pevného disku.
</td>
<td><ul>
<li>Zvedejte a přesouvejte aplikace, které používají nativní api systému souborů ke čtení a zápisu dat na trvalé disky.</li>
<li>Ukládat data, která není nutné přistupovat z mimo virtuální hod, ke kterému je připojen disk.</li>
</ul></td>
</tr>
</tbody>
</table>
<!-- markdownlint-enable MD033 -->

## <a name="azure-hot-online-and-cold-archive-storage"></a>Azure horké (online) a studené (archivní) úložiště

Typ úložiště pro daný systém závisí na požadavcích systému, včetně velikosti úložiště, propustnosta a viposlužby. Pro úložiště typu DASD na sálovém počítači aplikace v Azure obvykle používají úložiště disků Azure. Pro úložiště archivu sálových počítačů se úložiště objektů blob používá v Azure.

Ssd disy poskytují nejvyšší výkon úložiště v Azure. K dispozici jsou následující možnosti (od psaní tohoto dokumentu):

| Typ         | Velikost           | IOPS                  |
|--------------|----------------|-----------------------|
| SSD úrovně Ultra    | 4 GB až 64 TB  | 1 200 až 160 000 IOPS |
| SSD úrovně Premium  | 32 GB až 32 TB | 12 až 15 000 IOPS     |
| SSD úrovně Standard | 32 GB až 32 TB | 12 až 2 000 IOPS      |

Úložiště objektů blob poskytuje největší objem úložiště v Azure. Kromě velikosti úložiště nabízí Azure spravované i nespravované úložiště. Díky spravovanému úložišti se Azure stará o správu základních účtů úložiště. S nespravovaným úložištěm přebírá uživatel odpovědnost za nastavení účtů úložiště Azure odpovídající velikosti, aby splňovaly požadavky na úložiště.

## <a name="next-steps"></a>Další kroky

- [Migrace sálového počítače](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Rehosting sálového počítače na virtuálních počítačích Azure](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [Přesunutí výpočetních prostředků sálového počítače do Azure](mainframe-compute-Azure.md)
- [Rozhodování o tom, kdy použít objekty BLOB Azure, soubory Azure nebo disky Azure](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)
- [Standardní spravované disky SSD pro úlohy virtuálních počítačů Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd)

### <a name="ibm-resources"></a>Zdroje pro IBM

- [Paralelní Sysplex na IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS a spojovací zařízení: Nad rámec základů](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Vytvoření požadovaných uživatelů pro instalaci funkce Db2 pureScale](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt - příkaz Vytvořit instanci](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Řešení clusterované databáze Db2 pureScale](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Cloud Microsoft Azure Government pro sálové aplikace](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft a FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>Více zdrojů pro migraci

- [Průvodce výtahem a směnami virtuálního datového centra Azure](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
