---
title: Přesunout mainframových úložiště do služby Azure Storage
description: Masivně škálovatelné úložiště Azure pomůžou organizacím mainframových migrace a modernizujte aplikace z14 IBM.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: storage
ms.openlocfilehash: dc78f87d9b47745119da91b8ed1f8f6c8572968c
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190445"
---
# <a name="move-mainframe-storage-to-azure"></a>Přesunout mainframových úložiště do Azure

Spouštění úloh sálové v Microsoft Azure, je potřeba vědět, jak porovnat vaše možnosti do Azure. Prostředky masivně škálovatelné úložiště může pomoct organizacím začít modernizovat bez opuštění aplikace, které spoléhají na to.

Azure nabízí mainframových jako funkce a kapacitu úložiště, který je srovnatelný s IBM z14 systémy (aktuální model v době psaní tohoto textu). Tento článek vysvětluje, jak získat srovnatelné výsledků v Azure.

## <a name="mainframe-storage-at-a-glance"></a>Mainframových úložiště na první pohled

Sálové počítače IBM charakterizuje úložiště dvěma způsoby. První je přímý přístup úložné zařízení (DASD). Druhým je sekvenční úložiště. Pro správu úložiště, poskytuje sálových Data zařízení úložiště správy Subsystem (DFSMS). Spravuje přístup k datům pro různá zařízení úložiště.

[DASD](https://en.wikipedia.org/wiki/Direct-access_storage_device) odkazuje na samostatném zařízení jako sekundární úložiště (ne v paměti), který umožňuje jedinečnou adresu má být použit pro přímého přístupu k datům. Původně byly doby DASD použít na otáčejících se discích, magnetické válce nebo datových buňkách. Nyní termín můžete také použít k zařízení úložiště SSD (Solid-State Drive), storage area network (SAN), síti připojené úložiště (NAS) a optické jednotky. Pro účely tohoto dokumentu se DASD odkazuje na otáčejících se discích, sítě SAN a jednotky SSD.

Na rozdíl od úložiště DASD sekvenční úložiště na sálového počítače odkazuje na zařízení, jako jsou páskové jednotky, kde je k němu přistupovat z výchozí bod, pak číst nebo zapisovat na řádku data.

Zařízení úložiště jsou obvykle připojeny použití vlákének připojení (FICON) nebo ke kterým se přistupuje přímo na pomocí Service bus vstupně-výstupních operací sálových [HiperSockets](https://www.ibm.com/support/knowledgecenter/zosbasics/com.ibm.zos.znetwork/znetwork_85.htm), technologie IBM pro vysokorychlostní komunikaci mezi oddíly na server se službou hypervisor.

Většina sálových počítačích odděleného úložiště do dvou typů:

- *Online úložiště* (také označované jako úložiště s vrstvami hot) je potřebná pro každodenní operace. DASD úložiště se obvykle používá pro tento účel. Sekvenční úložiště, jako je například denního typu zálohování (logická nebo fyzická), ale je také možné pro tento účel.

- *Úložiště archivu* (označované také jako studeného úložiště), není zaručeno připojit v daném okamžiku. Místo toho je připojit a získat přístup podle potřeby. Úložiště archivu je často implementované pomocí sekvenční páskové zálohování (logická nebo fyzická) pro úložiště.

## <a name="mainframe-versus-io-latency-and-iops"></a>Sálové počítače a vstupně-výstupní latence a vstupně-výstupních operací

Sálové počítače se často používají pro aplikace, které vyžadují vysoký výkon vstupně-výstupních operací a nízkou latenci vstupně-výstupních operací. Můžete to provést pomocí připojení FICON HiperSockets a vstupně-výstupních operací zařízení. V případě HiperSockets používají pro připojení přímo do kanálu vstupně-výstupních operací mainframových aplikací a zařízení, latenci mikrosekundy jde dosáhnout.

## <a name="azure-storage-at-a-glance"></a>Azure storage na první pohled

Azure infrastructure-as--service ([IaaS](https://azure.microsoft.com/overview/what-is-iaas/)) možnosti pro úložiště poskytují srovnatelné mainframových kapacity.

Společnost Microsoft nabízí za petabajtů úložiště pro aplikace hostované v Azure a máte několik možností úložiště. Tyto sahají od úložiště SSD pro vysoce výkonné úložiště objektů blob s nízkými náklady pro velkokapacitních paměťových zařízení a archivy. Kromě toho Azure poskytuje možnost datové redundance úložiště – objekt, který přijímá další úsilí k nastavení prostředí mainframových.

Azure storage je dostupné jako [Azure Disks](/azure/virtual-machines/windows/managed-disks-overview), [Azure Files](/azure/storage/files/storage-files-introduction), a [Azure Blobs](/azure/storage/blobs/storage-blobs-overview) jako v následující tabulce najdete souhrn. Další informace o [při použití každé](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks).

<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Type</th><th>Popis</th><th>Případy vhodného použití:</th></tr>
</thead>
<tbody>
<tr><td>Soubory Azure
</td>
<td>
Poskytuje rozhraní SMB, klientských knihoven a <a href="https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api">REST</a> rozhraní, které umožňuje přístup odkudkoli k uložených souborů.
</td>
<td><ul>
<li>Zvedněte a shift aplikace do cloudu, pokud aplikace používá rozhraní API systému nativní soubor pro sdílení dat mezi ním a dalších aplikací běžících v Azure.</li>
<li>Store vývoje a ladicí nástroje, které je potřeba přistupovat z mnoha virtuálních počítačů.</li>
</ul>
</td>
</tr>
<tr><td>Azure Blobs
</td>
<td>Nabízí klientské knihovny a <a href="https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api">REST</a> rozhraní, které umožňuje nestrukturovaných dat ukládají a získávají ve velkém měřítku v objektech BLOB bloku. Podporuje také <a href="/azure/storage/blobs/data-lake-storage-introduction">Azure Data Lake Storage Gen2</a> pro řešení pro analýzu velkých objemů dat organizace.
</td>
<td><ul>
<li>Scénáře datových proudů a náhodným přístupem podporovat v aplikaci.</li>
<li>Máte přístup k datům aplikace odkudkoli.</li>
<li>Vytvoření data lake enterprise v Azure a provádět analýzy velkých objemů dat.</li>
</ul></td>
</tr>
<tr><td>Disky Azure
</td>
<td>Nabízí klientské knihovny a <a href="https://docs.microsoft.com/rest/api/compute/disks">REST</a> rozhraní, které umožňuje dat trvale uložena a k němu přistupovat z připojeného virtuálního pevného disku.
</td>
<td><ul>
<li>Zvedněte a shift aplikace, které používají rozhraní API systému nativní soubor pro čtení a zápis dat do trvalé disky.</li>
<li>Store data, která nevyžaduje přístup mimo virtuální počítač, ke kterému je připojený disk.</li>
</ul></td>
</tr>
</tbody>
</table>
<!-- markdownlint-enable MD033 -->

## <a name="azure-hot-online-and-cold-archive-storage"></a>Azure (online) horké a studené (archiv) úložiště

Typ úložiště pro daný systém závisí na požadavky na systém, včetně velikosti úložiště, propustnost a vstupně-výstupních operací. Pro službu storage DASD-type na sálového počítače aplikace v Azure obvykle používají disky Azure disk storage místo. Za úložiště archivu mainframových se používá úložiště objektů blob v Azure.

Disky SSD poskytují nejvyšší výkon úložiště v Azure. (V době psaní tohoto dokumentu) k dispozici jsou následující možnosti:

| Type         | Velikost           | IOPS                  |
|--------------|----------------|-----------------------|
| Ultra SSD    | 4 GB až 64 TB  | vstupně-výstupních operací 1 200 k 160,000 |
| Premium SSD  | 32 GB až 32 TB | 12 na 15 000 vstupně-výstupních operací     |
| SSD úrovně Standard | 32 GB až 32 TB | 12 až 2 000 vstupně-výstupních operací      |

BLOB storage poskytuje největší objem úložiště v Azure. Kromě velikost úložiště Azure nabízí spravované i nespravované úložiště. Spravované úložiště Azure stará o správu základní účty úložiště. Uživatel provede pomocí nespravovaného úložiště, odpovědnost za nastavení účtů úložiště Azure vhodné velikosti podle požadavků na úložiště.

## <a name="next-steps"></a>Další postup

- [Migrace mainframů](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Změna mainframových hostování na Azure Virtual Machines](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [Přesunout výpočetní sálové počítače do Azure](mainframe-compute-Azure.md)
- [Rozhodování, jestli použít Azure Blobs, soubory Azure nebo Azure Disks](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks)
- [Standardní spravované disky SSD pro úlohy virtuálních počítačů Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd)

### <a name="ibm-resources"></a>IBM prostředky

- [Paralelní Sysplex na IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS a párování zařízení: Nad rámec základní informace](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Vytváří se požadovaní uživatelé pro Db2 pureScale instalace funkce](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt - vytvořit instanci příkaz](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [Db2 pureScale skupinový databázové řešení](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Microsoft Azure Government cloud pro mainframových aplikací](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft a FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>Další zdroje migrace

- [Modernizace Alliance platformy: IBM Db2 v Azure](https://www.platformmodernization.org/pages/ibmdb2azure.aspx)
- [Azure virtuální datové centrum Lift and Shift Průvodce](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
