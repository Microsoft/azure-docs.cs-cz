---
title: Přesuňte sálové úložiště do Azure Storage
description: Široce škálovatelné prostředky úložiště Azure umožňují organizacím založeným na sáloví migrovat a modernizovat aplikace IBM Z14.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: storage
ms.openlocfilehash: 639764c9cae5e27e90a55da00b246807e49d2a20
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88652844"
---
# <a name="move-mainframe-storage-to-azure"></a>Přesunout sálové úložiště do Azure

Pokud chcete provozovat sálové úlohy na Microsoft Azure, musíte znát, jak se možnosti vašeho sálového počítače porovnají s Azure. Vysoce škálovatelné prostředky úložiště mohou organizacím pomáhat začít modernizovat bez opuštění aplikací, které spoléhají.

Azure poskytuje funkce podobné sálovým systémům a kapacitu úložiště, která je srovnatelná s Z14 systémy založenými na IBM (nejaktuálnější model v tomto psaní). V tomto článku se dozvíte, jak získat srovnatelné výsledky v Azure.

## <a name="mainframe-storage-at-a-glance"></a>První pohled na sálové úložiště

Sálové úložiště IBM charakterizuje úložiště dvěma způsoby. První je úložné zařízení s přímým přístupem (DASD). Druhým je sekvenční úložiště. Pro správu úložiště poskytuje sálový podsystém pro správu úložiště datových zařízení (DFSMS). Spravuje přístup k datům do různých úložných zařízení.

[DASD](https://en.wikipedia.org/wiki/Direct-access_storage_device) odkazuje na samostatné zařízení pro sekundární úložiště (ne v paměti), které umožňuje používat pro přímý přístup k datům jedinečnou adresu. V původním případě se pojem DASD aplikuje na rotující disky, magnetické bubny nebo datové buňky. Nicméně termín se teď může vztahovat i na úložná zařízení Solid-State (SSD), sítě SAN (Storage Area Network), síťové připojené úložiště (NAS) a optické jednotky. Pro účely tohoto dokumentu DASD odkazuje na rotující disky, sítě SAN a SSD.

Na rozdíl od úložiště DASD, sekvenční úložiště v sálovém počítači odkazuje na zařízení, jako jsou páskové jednotky, ve kterých se k datům dostanete z počátečního bodu, a pak si je můžete přečíst nebo zapsat na řádku.

Úložná zařízení jsou obvykle připojená pomocí připojení k vláknu (FICON) nebo se k nim dají získat přímým použitím [HiperSockets](https://www.ibm.com/support/knowledgecenter/zosbasics/com.ibm.zos.znetwork/znetwork_85.htm), technologie IBM pro vysokorychlostní komunikaci mezi oddíly na serveru s hypervisorem.

Většina sálových počítačů oddělené úložiště do dvou typů:

- *Online úložiště* (také známé jako horké úložiště) je potřeba pro každodenní operace. K tomuto účelu se obvykle používá úložiště DASD. Pro tento účel se ale dá použít sekvenční úložiště, například denní zálohování na pásku (logický nebo fyzický).

- *Úložiště archivu* (označované také jako studené úložiště) není zaručeno, že bude v daný okamžik připojen. Místo toho je připojen a je k němu přistupovat podle potřeby. Archivní úložiště se často implementuje pomocí sekvenčního zálohování na pásku (logických nebo fyzických) pro úložiště.

## <a name="mainframe-versus-io-latency-and-iops"></a>Sálový a vstupně-výstupní latence a IOPS

Sálové počítače se často používají pro aplikace, které vyžadují vysoký výkon v/v a nízkou latenci v/v. Můžou to udělat pomocí FICON připojení k vstupně-výstupním zařízením a HiperSockets. Když se HiperSockets používá k připojení aplikací a zařízení přímo k vstupně-výstupnímu kanálu pro sálové počítače, může být dosaženo latence v mikrosekundách.

## <a name="azure-storage-at-a-glance"></a>První pohled na Azure Storage

Možnosti infrastruktury Azure jako služby ([IaaS](https://azure.microsoft.com/overview/what-is-iaas/)) pro úložiště poskytují srovnatelnou kapacitu sálového počítače.

Microsoft nabízí petabajty úložiště pro aplikace hostované v Azure a máte několik možností úložiště. Tento rozsah je od úložiště SSD vysokým výkonem pro úložiště objektů BLOB s nízkými náklady pro velkokapacitní úložiště a archivy. Azure navíc poskytuje možnost redundance dat pro úložiště – to je něco, co při vytváření v sálovém prostředí trvá více úsilí.

Služba Azure Storage je dostupná jako [disky Azure](../../../managed-disks-overview.md), [soubory Azure](../../../../storage/files/storage-files-introduction.md)a [objekty blob Azure](../../../../storage/blobs/storage-blobs-overview.md) , které jsou shrnuté v následující tabulce. Přečtěte si další informace o tom, [kdy použít jednotlivé](../../../../storage/common/storage-introduction.md).

<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Typ</th><th>Description</th><th>Použijte v případě, že chcete:</th></tr>
</thead>
<tbody>
<tr><td>Soubory Azure
</td>
<td>
Poskytuje rozhraní protokolu SMB, klientské knihovny a rozhraní <a href="https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api">REST</a> , které umožňuje přístup odkudkoli do uložených souborů.
</td>
<td><ul>
<li>Nazvednutí a posunutí aplikace do cloudu, když aplikace používá nativní rozhraní API systému souborů ke sdílení dat mezi IT a dalšími aplikacemi běžícími v Azure.</li>
<li>Ukládání nástrojů pro vývoj a ladění, které je potřeba mít k dispozici z mnoha virtuálních počítačů.</li>
</ul>
</td>
</tr>
<tr><td>Objekty blob Azure
</td>
<td>Poskytuje klientské knihovny a rozhraní <a href="https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api">REST</a> , které umožňuje ukládání nestrukturovaných dat a jejich použití v obrovských škálováních objektů blob bloku. Také podporuje <a href="/azure/storage/blobs/data-lake-storage-introduction">Azure Data Lake Storage Gen2</a> pro řešení pro analýzy velkých objemů dat v podniku.
</td>
<td><ul>
<li>Podpora streamování a scénářů s náhodným přístupem v aplikaci.</li>
<li>Mít přístup k datům aplikací odkudkoli.</li>
<li>Sestavte Data Lake Datacenter v Azure a proveďte analýzu velkých objemů dat.</li>
</ul></td>
</tr>
<tr><td>Disky Azure
</td>
<td>Poskytuje klientské knihovny a rozhraní <a href="https://docs.microsoft.com/rest/api/compute/disks">REST</a> , které umožňuje trvalé uložení dat a získání jejich pøístupu z připojeného virtuálního pevného disku.
</td>
<td><ul>
<li>Aplikace přenesené a posunutí, které používají rozhraní API nativního systému souborů ke čtení a zápisu dat na trvalé disky.</li>
<li>Ukládat data, která se nevyžadují k získání pøístupu mimo virtuální počítač, ke kterému je disk připojený</li>
</ul></td>
</tr>
</tbody>
</table>
<!-- markdownlint-enable MD033 -->

## <a name="azure-hot-online-and-cold-archive-storage"></a>Azure Hot (online) a studené (archivní) úložiště

Typ úložiště pro daný systém závisí na požadavcích systému, včetně velikosti úložiště, propustnosti a IOPS. V případě DASD typu úložiště v sálových počítačích obvykle aplikace v Azure používají místo toho úložiště diskových jednotek Azure. Pro úložiště archivu sálového počítače se v Azure používá úložiště objektů BLOB.

SSD poskytují nejvyšší výkon úložiště v Azure. K dispozici jsou následující možnosti (od zápisu tohoto dokumentu):

| Typ         | Velikost           | IOPS                  |
|--------------|----------------|-----------------------|
| SSD úrovně Ultra    | 4 GB až 64 TB  | 1 200 až 160 000 IOPS |
| SSD úrovně Premium  | 32 GB až 32 TB | 12 až 15 000 IOPS     |
| SSD úrovně Standard | 32 GB až 32 TB | 12 až 2 000 IOPS      |

BLOB Storage poskytuje největší objem úložiště v Azure. Kromě velikosti úložiště nabízí Azure spravované i nespravované úložiště. Se spravovaným úložištěm Azure se postará o správu základních účtů úložiště. V případě nespravovaného úložiště vezme uživatel zodpovědnost za nastavení účtů úložiště Azure odpovídající velikosti pro splnění požadavků na úložiště.

## <a name="next-steps"></a>Další kroky

- [Migrace sálového počítače](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Opětovné hostování sálového počítače v Azure Virtual Machines](../overview.md)
- [Přesunout do Azure výpočetní sálové počítače](mainframe-compute-Azure.md)
- [Rozhodnutí o použití objektů blob Azure, souborů Azure nebo disků Azure](../../../../storage/common/storage-introduction.md)
- [Managed Disks SSD úrovně Standard pro úlohy virtuálních počítačů Azure](../../../disks-types.md#standard-ssd)

### <a name="ibm-resources"></a>Prostředky IBM

- [Paralelní Sysplex v IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS a spojovací zařízení: rámec základních](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Vytváření požadovaných uživatelů pro instalaci funkce Db2 pureScale](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt – vytvoření instance – příkaz](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Řešení clusterové databáze Db2 pureScale](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Microsoft Azure Government Cloud pro sálové aplikace](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft a FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>Další zdroje migrace

- [Průvodce zvednutím a posunutím virtuálního datového centra Azure](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
