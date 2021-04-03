---
title: Microsoft Azure Data Box Disk – přehled | Microsoft Docs
description: Tento článek popisuje službu Azure Data Box Disk, což je cloudové řešení, které umožňuje přenášet velké objemy dat do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: overview
ms.date: 06/18/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand what Data Box Disk is and how it works so I can use it to import on-premises data into Azure.
ms.openlocfilehash: ea9353ed8c1938fa9b33585a0650b4507c671451
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92125026"
---
# <a name="what-is-azure-data-box-disk"></a>Co je Azure Data Box Disk?

Řešení Microsoft Azure Data Box Disk umožňuje odesílat do Azure rychle, levně a bezpečně terabajty místních dat. Rychlejší bezpečný přenos dat je zajišťován pomocí 1 až 5 disků SSD (Solid-State Disk), které dostanete. Tyto 8 TB šifrovaných disků se odesílají do vašeho datacentra prostřednictvím regionálního dopravce.

Disky můžete v Azure Portal rychle konfigurovat, připojit a odemknout pomocí služby Data Box. Data zkopírujete na disky a ty pošlete zpět do Azure. V datacentru Azure se vaše data automaticky nahrají z disků do cloudu pomocí rychlého nahrávacího propojení privátní sítě.

## <a name="use-cases"></a>Případy použití

Prostřednictvím služby Data Box Disk můžete přenést terabajty dat i v případě, že máte omezené připojení k síti. Data můžete přesunout jednorázově, přesunovat je pravidelně nebo provést počáteční hromadný přesun a posléze data přenášet pravidelně.

- **Jednorázová migrace** – při tomto typu migrace se do Azure přesune velký objem místních dat. Příkladem může být přesun dat z offline pásek za účelem archivace dat ve studeném úložišti Azure.
- **Přírůstkový přenos** – při tomto typu migrace provedete počáteční hromadný přesun pomocí služby Data Box Disk (kompletní) a potom provádíte přírůstkové přenosy přes síť. Pro přesun záložních kopií do Azure se například využívají Commvault a Data Box Disk. Tato migrace je následována zkopírováním přírůstkových dat pomocí sítě do Azure Storage.
- **Pravidelné nahrávání** – provádí se v případech, kdy pravidelně dochází ke generování velkých objemů dat, která je nutná přesunout do Azure. Příkladem může být výzkum energie, kdy se u vrtných plošin a soustavy větrných generátorů generuje videoobsah.

### <a name="ingestion-of-data-from-data-box"></a>Přijímání dat z Data Box

Poskytovatelé Azure a poskytovatelé jiných výrobců Azure mohou ingestovat data z Azure Data Box. Služby Azure, které poskytují příjem dat z Azure Data Box zahrnují:

- **SharePoint Online** – k migraci obsahu sdílené složky do SharePointu online použijte Azure Data box a nástroj pro migraci služby SharePoint (SPMT). Pomocí Data Box odeberete závislost na síti WAN pro přenos dat. Další informace najdete v tématu [použití Azure Data box Heavy k migraci obsahu sdílené složky do SharePointu Online](data-box-heavy-migrate-spo.md).

- **Synchronizace souborů Azure** – replikuje soubory z vaší data box do sdílené složky Azure a umožní vám centralizovat vaše souborové služby v Azure a přitom zachovat místní přístup k vašim datům. Další informace najdete v tématu [nasazení synchronizace souborů Azure](../storage/files/storage-sync-files-deployment-guide.md).

- **HDFS ukládá** – migruje data z místního úložiště Hadoop systém souborů DFS (DISTRIBUTED File System) (HDFS) vašeho clusteru hadoop do Azure Storage pomocí data box. Další informace najdete v tématu [migrace z úložiště Prem HDFS do Azure Storage s Azure Data box](../storage/blobs/data-lake-storage-migrate-on-premises-hdfs-cluster.md).

- **Azure Backup** – umožňuje přesunout velké zálohy důležitých podnikových dat prostřednictvím offline mechanismů do trezoru Azure Recovery Services. Další informace najdete v tématu [přehled Azure Backup](../backup/backup-overview.md).

Data Data Box můžete používat s mnoha poskytovateli služeb mimo Azure. Například:

- **[CommVault](http://documentation.commvault.com/commvault/v11/article?p=97276.htm)** – umožňuje migrovat velké objemy dat na Microsoft Azure pomocí Azure Data box.
- **[Veeam](https://helpcenter.veeam.com/docs/backup/hyperv/osr_adding_data_box.html?ver=100)** – umožňuje zálohovat a replikovat velké objemy dat z počítače s Hyper-V do vaší data box.

Seznam dalších poskytovatelů služeb mimo Azure, které se integrují s Data Box, najdete v tématu [Azure Data box partneři](https://cloudchampions.blob.core.windows.net/db-partners/PartnersTable.pdf).

## <a name="the-workflow"></a>Pracovní postup

Typický postup zahrnuje tyto kroky:

1. **Objednávka** – vytvořte objednávku v Azure Portal, zadejte informace o expedici a cílový Azure Storage účet pro vaše data. Pokud jsou disky k dispozici, Azure zašifruje, připraví a odešle disky s identifikačním číslem pro sledování zásilky.

2. **Příjem** – po doručení disky vybalíte a připojíte k počítači s daty, která chcete zkopírovat. Odemknete disky.

3. **Kopírování dat** – příslušná data přetažením zkopírujete na disky.

4. **Vrácení** – připravíte a odešlete disky zpět do datacentra Azure.

5. **Nahrání** – data se automaticky zkopírují z disků do Azure. Disky jsou bezpečně vymazány podle pokynů organizace NIST (National Institute of Standards and Technology).

V průběhu tohoto procesu budete upozorněni e-mailem na všechny změny stavu. Další informace o podrobném postupu najdete v článku o [nasazení služby Data Box Disk na portálu Azure Portal](data-box-disk-quickstart-portal.md).

## <a name="benefits"></a>Výhody

Data Box Disk slouží k přesunu velkých objemů dat do Azure bez sebemenšího dopadu na síť. Toto řešení má následující výhody:

- **Rychlost** – služba Data Box Disk používá připojení USB 3.0 a je tak schopná do Azure přesunout až 35 TB dat za dobu kratší než týden.

- **Snadné použití** – služba Data Box je snadno použitelné řešení.

  - Disky využívají připojení USB a jejich nastavení nezabere téměř žádný čas.
  - Disky jsou malé, a proto se s nimi snadno manipuluje.
  - Disky nemají žádné požadavky na externí napájení.
  - Disky je možné používat se serverem datacentra, desktopem i laptopem.
  - Toto řešení poskytuje kompletní sledování pomocí Azure Portal.

- **Zabezpečení** – Data Box Disk má integrovanou ochranu disků, dat a samotné služby.
  - Disky jsou odolné vůči neoprávněné manipulaci a podporují možnosti zabezpečené aktualizace.
  - Data na discích jsou neustále chráněna pomocí 128bitového šifrování AES.
  - Disky je možné odemknout pouze klíčem, který najdete na portálu Azure Portal.
  - Služba samotná je chráněna funkcemi zabezpečení Azure.
  - Po nahrání dat do Azure se disky zcela vymažou podle standardů NIST 800-88r1.  

Další informace najdete v článku, který se věnuje [zabezpečení a ochraně dat ve službě Data Box Disk](data-box-disk-security.md).

## <a name="features-and-specifications"></a>Funkce a specifikace

| Specifikace                                          | Description              |
|---------------------------------------------------------|--------------------------|
| Hmotnost                                                  | < 1 kg na krabici. Až 5 disků v poli                |
| Dimenze                                              | Disk – 2,5“ SSD |
| Kabely                                                  | 1 kabel USB 3.1 pro každý disk|
| Kapacita služby Storage na jednu objednávku                              | 40 TB (využitelnost cca 35 TB)|
| Kapacita úložiště disku                                   | 8 TB (využitelnost cca 7 TB)|
| Datové rozhraní                                          | USB   |
| Zabezpečení                                                | Předběžné šifrování pomocí nástroje BitLocker a zabezpečené aktualizace <br> Disky chráněné klíčem <br> Data šifrovaná za všech okolností  |
| Rychlost přenosu dat                                      | Až 430 MB/s v závislosti na velikosti souboru      |
|Správa                                               | portál Azure |

## <a name="region-availability"></a>Dostupnost v oblastech

Informace o dostupnosti oblastí najdete v [produktech Azure, které jsou dostupné v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Data Box Disk taky můžete nasadit v cloudu Azure Government. Další informace najdete v tématu [co je Azure Government?](../azure-government/documentation-government-welcome.md).

## <a name="pricing"></a>Ceny

Informace o cenách najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/databox/disk/).

## <a name="next-steps"></a>Další kroky

- Projděte si [požadavky služby Data Box Disk](data-box-disk-system-requirements.md).
- Seznamte se s [omezeními služby Data Box Disk](data-box-disk-limits.md).
- Rychlé nasazení řešení [Azure Data Box Disk](data-box-disk-quickstart-portal.md) na webu Azure Portal