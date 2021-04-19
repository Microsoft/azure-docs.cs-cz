---
title: Přehled účtu úložiště
titleSuffix: Azure Storage
description: Přečtěte si o různých typech účtů úložiště v Azure Storage. Zkontrolujte pojmenování účtů, úrovně výkonu, úrovně přístupu, redundanci, šifrování, koncové body a další.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/19/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: d4874ad6688fa85f0c511632498938817bb218f7
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714195"
---
# <a name="storage-account-overview"></a>Přehled účtu úložiště

Účet úložiště Azure obsahuje všechny vaše Azure Storage datové objekty: objekty blob, soubory, fronty, tabulky a disky. Účet úložiště poskytuje jedinečný obor názvů pro data Azure Storage, která jsou přístupná odkudkoli na světě přes protokol HTTP nebo HTTPS. Data v účtu úložiště Azure jsou trvalá a vysoce dostupná, zabezpečená a rozsáhlá.

Informace o vytvoření účtu úložiště Azure najdete v tématu [Vytvoření účtu úložiště](storage-account-create.md).

## <a name="types-of-storage-accounts"></a>Typy účtů úložiště

Azure Storage nabízí několik typů účtů úložiště. Každý typ podporuje různé funkce a má svůj vlastní cenový model. Před vytvořením účtu úložiště zvažte tyto rozdíly a určete typ účtu, který je pro vaše aplikace nejvhodnější.

Následující tabulka popisuje typy účtů úložiště, které společnost Microsoft doporučuje, pro většinu scénářů:

| Typ účtu úložiště | Podporované služby | Možnosti redundance | Model nasazení | Využití |
|--|--|--|--|--|
| Standardní pro obecné účely v2 | Objekt blob, soubor, fronta, tabulka a Data Lake Storage<sup>1</sup> | LRS/GRS/RA-GRS<br /><br />ZRS/GZRS/RA-GZRS<sup>2</sup> | Správce prostředků<sup>3</sup> | Základní typ účtu úložiště pro objekty blob, soubory, fronty a tabulky. Doporučuje se ve většině scénářů pomocí Azure Storage. |
| Premium – objekty blob bloku<sup>4</sup> | Pouze objekty blob bloku | LRS<br /><br />ZRS<sup>2</sup> | Správce prostředků<sup>3</sup> | Účty úložiště s charakteristikami výkonu Premium pro objekty blob bloku a doplňovací objekty blob. Doporučuje se u scénářů s vysokými sazbami transakcí nebo scénářů, které používají menší objekty nebo vyžadují konzistentně nízkou latenci úložiště.<br />[Víc se uč...](../blobs/storage-blob-performance-tiers.md) |
| Podíly souborů úrovně Premium<sup>4</sup> | Jenom sdílené složky souborů | LRS<br /><br />ZRS<sup>2</sup> | Správce prostředků<sup>3</sup> | Jenom soubory účtů úložiště s charakteristikami výkonu Premium. Doporučuje se pro podnikové nebo vysoce výkonné škálování aplikace.<br />[Víc se uč...](../files/storage-files-planning.md#management-concepts) |
| Objekty blob stránky úrovně Premium<sup>4</sup> | Jenom objekty blob stránky | LRS | Správce prostředků<sup>3</sup> | Typ účtu Premium Storage jenom pro objekty blob stránky.<br />[Víc se uč...](../blobs/storage-blob-pageblob-overview.md) |

<sup>1</sup> Azure Data Lake Storage je sada funkcí vyhrazených pro analýzy velkých objemů dat, která je založená na službě Azure Blob Storage. Data Lake Storage se podporuje jenom pro účty úložiště pro obecné účely v2 s povoleným hierarchickým oborem názvů. Další informace o Data Lake Storage Gen2 najdete v tématu [Úvod do data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md).

<sup>2</sup> – redundantní úložiště (ZRS) a Geografická zóna redundantní úložiště (GZRS/RA-GZRS) jsou k dispozici pouze pro účty standard pro obecné účely v2, Premium blob bloku a prémiové sdílení souborů v určitých oblastech. Další informace o možnostech redundance Azure Storage najdete v tématu [Azure Storage redundance](storage-redundancy.md).

<sup>3</sup> Azure Resource Manager je doporučený model nasazení pro prostředky Azure, včetně účtů úložiště. Další informace najdete v tématu [přehled správce prostředků](../../azure-resource-manager/management/overview.md).

<sup>4</sup> účty úložiště ve vrstvě výkonu Premium využívají disky Solid State (SSD) pro nízkou latenci a vysokou propustnost.

Podporovány jsou i starší účty úložiště. Další informace najdete v tématu [starší typy účtů úložiště](#legacy-storage-account-types).

## <a name="storage-account-endpoints"></a>Koncové body účtu úložiště

Účet úložiště poskytuje jedinečný obor názvů v Azure pro vaše data. Každý objekt, který je uložen v Azure Storage má adresu, která obsahuje jedinečný název účtu. Kombinace názvu účtu a koncového bodu služby Azure Storage tvoří koncové body pro váš účet úložiště.

Při pojmenování účtu úložiště mějte na paměti tato pravidla:

- Názvy účtů úložiště musí mít délku 3 až 24 znaků a můžou obsahovat jenom číslice a malá písmena.
- Váš název účtu úložiště musí být jedinečný v rámci Azure. Žádné dva účty úložiště nemohou mít stejný název.

Následující tabulka uvádí formát koncového bodu pro každou službu Azure Storage Services.

| Služba úložiště | Koncový bod |
|--|--|
| Blob Storage | `https://<storage-account>.blob.core.windows.net` |
| Data Lake Storage Gen2 | `https://<storage-account>.dfs.core.windows.net` |
| Azure Files | `https://<storage-account>.file.core.windows.net` |
| Queue Storage | `https://<storage-account>.queue.core.windows.net` |
| Table Storage | `https://<storage-account>.table.core.windows.net` |

Vytvořte adresu URL pro přístup k objektu v účtu úložiště připojením umístění objektu v účtu úložiště ke koncovému bodu. Například adresa URL objektu BLOB bude vypadat přibližně takto:

`http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*`

Svůj účet úložiště můžete také nakonfigurovat tak, aby používal vlastní doménu pro objekty blob. Další informace najdete v tématu [Konfigurace vlastního názvu domény pro účet Azure Storage](../blobs/storage-custom-domain-name.md).  

## <a name="migrating-a-storage-account"></a>Migrace účtu úložiště

Následující tabulka shrnuje a odkazuje na pokyny k přesunutí, upgradu nebo migraci účtu úložiště:

| Scénář migrace | Podrobnosti |
|--|--|
| Přesunutí účtu úložiště do jiného předplatného | Azure Resource Manager poskytuje možnosti pro přesunutí prostředku do jiného předplatného. Další informace najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../../azure-resource-manager/management/move-resource-group-and-subscription.md). |
| Přesunutí účtu úložiště do jiné skupiny prostředků | Azure Resource Manager poskytuje možnosti pro přesunutí prostředku do jiné skupiny prostředků. Další informace najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../../azure-resource-manager/management/move-resource-group-and-subscription.md). |
| Přesunutí účtu úložiště do jiné oblasti | Pokud chcete přesunout účet úložiště, vytvořte jeho kopii v jiné oblasti. Pak přesuňte data do tohoto účtu pomocí AzCopy nebo jiného nástroje podle vašeho výběru. Další informace najdete v tématu [přesunutí účtu Azure Storage do jiné oblasti](storage-account-move.md). |
| Upgrade na účet úložiště pro obecné účely v2 | Účet úložiště pro obecné účely v1 nebo účet Blob Storage můžete upgradovat na účet pro obecné účely v2. Tato akce se nedá vrátit zpátky. Další informace najdete v tématu [upgrade na účet úložiště pro obecné účely v2](storage-account-upgrade.md). |
| Migrace klasického účtu úložiště na Azure Resource Manager | Model nasazení Azure Resource Manager je modelem nasazení Classic nadřazený s ohledem na funkčnost, škálovatelnost a zabezpečení. Další informace o migraci účtu klasického úložiště na Azure Resource Manager najdete v tématu [Migrace účtů úložiště](../../virtual-machines/migration-classic-resource-manager-overview.md#migration-of-storage-accounts) v **migraci prostředků IaaS podporovaných platformou z klasických na Azure Resource Manager**. |

## <a name="transferring-data-into-a-storage-account"></a>Přenos dat do účtu úložiště

Microsoft poskytuje služby a nástroje pro import dat z místních úložných zařízení nebo poskytovatelů cloudového úložiště třetích stran. Které řešení použijete, záleží na množství převáděných dat. Další informace najdete v tématu [Přehled migrace Azure Storage](storage-migration-overview.md).

## <a name="storage-account-encryption"></a>Šifrování účtu úložiště

Všechna data v účtu úložiště se na straně služby automaticky šifrují. Další informace o šifrování a správě klíčů najdete v tématu [Azure Storage Encryption for data v klidovém umístění](storage-service-encryption.md).

## <a name="storage-account-billing"></a>Fakturace účtu úložiště

VyAzure Storage účty na základě využití účtu úložiště. Všechny objekty v rámci účtu úložiště se fakturují společně jako skupina. Náklady na úložiště se počítají v závislosti na následujících faktorech:

- **Oblast** odkazuje na geografickou oblast, ve které je účet založený.
- **Typ účtu** odkazuje na typ účtu úložiště, který používáte.
- **Úroveň přístupu** odkazuje na model využití dat, který jste zadali pro účet úložiště pro obecné účely v2 nebo BLOB.
- **Kapacita** odkazuje na to, kolik z vašich účtů úložiště používáte k ukládání dat.
- **Redundance** určuje, kolik kopií vašich dat se uchovává najednou a v jakých umístěních.
- **Transakce** odkazují na všechny operace čtení a zápisu na Azure Storage.
- **Výstup dat** odkazuje na všechna data přenesená z oblasti Azure. Když k datům v účtu úložiště přistupovala aplikace, která neběží ve stejné oblasti, účtují se vám poplatky za výstup dat. Informace o použití skupin prostředků k seskupení dat a služeb ve stejné oblasti za účelem omezení nákladů na výstup najdete v tématu [co je skupina prostředků Azure?](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group).

Na [stránce s cenami Azure Storage](https://azure.microsoft.com/pricing/details/storage/) najdete podrobné informace o cenách na základě typu účtu, kapacity úložiště, replikace a transakcí. [Podrobnosti o cenách přenosu dat](https://azure.microsoft.com/pricing/details/data-transfers/) poskytují podrobné informace o cenách pro výstup dat. Pomocí [cenové kalkulačky Azure Storage](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) můžete odhadnout náklady.

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="legacy-storage-account-types"></a>Starší typy účtů úložiště

Následující tabulka popisuje starší typy účtů úložiště. Společnost Microsoft tyto typy účtů nedoporučuje, ale je možné ji použít v určitých scénářích:

| Typ starší verze účtu úložiště | Podporované služby | Možnosti redundance | Model nasazení | Využití |
|--|--|--|--|--|
| Standardní pro obecné účely v1 | Objekt blob, soubor, fronta, tabulka a Data Lake Storage | LRS/GRS/RA-GRS | Správce prostředků, klasický | Účty pro obecné účely v1 nemůžou mít nejnovější funkce nebo nejnižší ceny za gigabajt. Zvažte použití v těchto scénářích:<br /><ul><li>Vaše aplikace vyžadují model nasazení Azure Classic.</li><li>Vaše aplikace jsou náročné na transakce nebo využívají značnou šířku pásma geografické replikace, ale nevyžadují velkou kapacitu. V tomto případě může být obecným účelem v1 největší ekonomická volba.</li><li>Používáte verzi Azure Storage REST API, která je starší než 2014-02-14 nebo Klientská knihovna s verzí nižší než 4. x, a nemůžete upgradovat aplikaci.</li></ul> |
| Úložiště objektů BLOB úrovně Standard | Objekt BLOB (jenom objekty blob bloku a doplňovací objekty BLOB) | LRS/GRS/RA-GRS | Resource Manager | Společnost Microsoft doporučuje použít místo toho standardní účty pro obecné účely v2, a to i když je to možné. |

## <a name="next-steps"></a>Další kroky

- [Vytvoření účtu úložiště](storage-account-create.md)
- [Upgrade na účet úložiště pro obecné účely v2](storage-account-upgrade.md)
- [Obnovení odstraněného účtu úložiště](storage-account-recover.md)