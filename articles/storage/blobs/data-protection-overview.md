---
title: Přehled ochrany dat
titleSuffix: Azure Storage
description: Možnosti ochrany dat dostupné pro Blob Storage a Azure Data Lake Storage Gen2 data umožňují chránit vaše data před smazáním nebo přepsáním. Pokud potřebujete obnovit data, která byla odstraněna nebo přepsána, tato příručka vám pomůže vybrat možnost obnovení, která je pro váš scénář nejvhodnější.
services: storage
author: tamram
ms.service: storage
ms.date: 04/09/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: prishet
ms.subservice: common
ms.openlocfilehash: 90c83397089b77d30694041a37debc0731ea2a38
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304252"
---
# <a name="data-protection-overview"></a>Přehled ochrany dat

Azure Storage poskytuje ochranu dat pro Blob Storage a Azure Data Lake Storage Gen2, která vám pomůžou připravit se na scénáře, kdy potřebujete obnovit data, která byla odstraněna nebo přepsána. Je důležité si představit, jak nejlépe chránit vaše data před incidentem, který by mohl ohrozit. Tato příručka vám může pomáhat s rozhodnutím předem, které funkce ochrany dat váš scénář vyžaduje, a jak je implementovat. Pokud potřebujete obnovit data, která byla odstraněna nebo přepsána, tento přehled obsahuje také pokyny, jak pokračovat v závislosti na vašem scénáři.

V dokumentaci k Azure Storage se *Ochrana dat* vztahuje na strategie pro ochranu účtu úložiště a dat v něm, aby se odstranila nebo upravila, nebo aby se data obnovila, až se odstranila nebo změnila. Azure Storage taky nabízí možnosti pro *zotavení po havárii*, včetně několika úrovní redundance, které chrání vaše data před výpadky služeb kvůli problémům s hardwarem nebo přirozeným katastrofám, a převzetí služeb při selhání spravované zákazníkem v případě, že datové centrum v primární oblasti nebude k dispozici. Další informace o tom, jak jsou vaše data chráněná před výpadky služby, najdete v tématu [zotavení po havárii](#disaster-recovery).

## <a name="recommendations-for-basic-data-protection"></a>Doporučení pro základní ochranu dat

Pokud hledáte základní pokrytí ochrany dat pro váš účet úložiště a data, která obsahuje, společnost Microsoft doporučuje, abyste při zahájení tohoto postupu vybrali následující kroky:

- Nakonfigurujte na účtu úložiště zámek Azure Resource Manager pro ochranu účtu před odstraněním nebo změnou konfigurace. [Víc se uč...](../common/lock-account-resource.md)
- Povolte pro účet úložiště obnovitelné odstranění kontejneru a obnovte odstraněný kontejner a jeho obsah. [Víc se uč...](soft-delete-container-enable.md)
- Uložení stavu objektu BLOB v pravidelných intervalech:
  - U Blob Storage úloh povolte správu verzí objektů BLOB tak, aby při každém přepsání objektu BLOB automaticky ukládala stav vašich dat. [Víc se uč...](versioning-enable.md)
  - U Azure Data Lake Storage úloh proveďte ruční snímky, abyste ušetřili stav vašich dat v určitém časovém okamžiku. [Víc se uč...](snapshots-overview.md)

Tyto možnosti a další možnosti ochrany dat pro jiné scénáře jsou podrobněji popsány v následující části.

Přehled nákladů týkajících se těchto funkcí najdete v tématu [Přehled důležitých nákladů](#summary-of-cost-considerations).

## <a name="overview-of-data-protection-options"></a>Přehled možností ochrany dat

Následující tabulka shrnuje možnosti, které jsou k dispozici v Azure Storage pro běžné scénáře ochrany dat. Pokud se chcete dozvědět víc o dostupných možnostech, vyberte scénáře, které se vztahují k vaší situaci. Všimněte si, že v tuto chvíli nejsou k dispozici všechny funkce pro účty úložiště s povoleným hierarchickým oborem názvů.

| Scenario | Možnost ochrany dat | Doporučení | Výhody ochrany | K dispozici pro Data Lake Storage |
|--|--|--|--|--|
| Zabrání odstranění nebo úpravě účtu úložiště. | Azure Resource Manager zámek<br />[Víc se uč...](../common/lock-account-resource.md) | Pokud chcete zabránit odstranění účtu úložiště, zamkněte všechny účty úložiště pomocí Azure Resource Manager zámku. | Chrání účet úložiště proti změnám v souvislosti s odstraněním nebo konfigurací.<br /><br />Nechrání kontejnery ani objekty BLOB v účtu proti odstranění nebo přepsání. | Yes |
| Znemožní odstranění nebo úpravu kontejneru a jeho objektů BLOB v intervalu, který ovládáte. | Zásady neměnnosti na kontejneru<br />[Víc se uč...](storage-blob-immutable-storage.md) | Nastavte zásady neměnnosti pro kontejner, aby chránily důležité firemní dokumenty, například, aby splňovaly zákonné nebo zákonné požadavky na dodržování předpisů. | Chrání kontejner a jeho objekty blob ze všech odstranění a přepsání.<br /><br />Když vstoupí v platnost právní nebo uzamčené zásady uchovávání informací na základě času, účet úložiště se taky chrání před odstraněním. Kontejnery, pro které nebyla nastavena žádná zásada neměnnosti, nejsou chráněny před odstraněním. | Ano, ve verzi Preview |
| Obnoví odstraněný kontejner v zadaném intervalu. | Obnovitelné odstranění kontejneru (Preview)<br />[Víc se uč...](soft-delete-container-overview.md) | Povolte obnovitelné odstranění kontejneru pro všechny účty úložiště s minimálním intervalem uchovávání 7 dní.<br /><br />Pokud chcete chránit jednotlivé objekty BLOB v kontejneru, povolte správu verzí objektů BLOB a obnovitelné odstranění objektů BLOB společně s podprogramovým odstraněním kontejneru.<br /><br />Kontejnery, které v samostatných účtech úložiště vyžadují různá období uchovávání dat. | Odstraněný kontejner a jeho obsah se může obnovit v rámci doby uchování.<br /><br />Obnovit se dají jenom operace na úrovni kontejneru (např. [odstranění kontejneru](/rest/api/storageservices/delete-container)). Obnovitelné odstranění kontejneru vám neumožňuje obnovit jednotlivý objekt BLOB v kontejneru, pokud je tento objekt BLOB odstraněný. | Ano, ve verzi Preview |
| Automaticky uloží stav objektu BLOB v předchozí verzi, pokud je přepsán. | Správa verzí objektů BLOB<br />[Víc se uč...](versioning-overview.md) | Povolte správu verzí objektů BLOB společně s možnostmi obnovitelné odstranění kontejnerů a obnovitelného odstranění objektů BLOB pro účty úložiště, ve kterých potřebujete optimální ochranu dat objektů BLOB.<br /><br />Ukládat data objektů blob, která nevyžadují správu verzí v samostatném účtu, abyste omezili náklady. | Každá operace zápisu objektu BLOB vytvoří novou verzi. Aktuální verze objektu BLOB může být obnovena z předchozí verze, pokud je aktuální verze odstraněna nebo přepsána. | No |
| Obnovení odstraněné verze objektu BLOB nebo objektu BLOB v zadaném intervalu. | Obnovitelné odstranění objektu BLOB<br />[Víc se uč...](soft-delete-blob-overview.md) | Povolte obnovitelné odstranění objektů BLOB pro všechny účty úložiště s minimálním intervalem uchovávání 7 dní.<br /><br />Pro zajištění optimální ochrany dat objektů BLOB povolte možnost Správa verzí objektů BLOB a obnovitelné odstranění kontejneru společně s objektem BLOB Soft redelete.<br /><br />Ukládat bloby, které vyžadují různá období uchování v samostatných účtech úložiště. | Odstraněné verze objektu BLOB nebo objektu BLOB se můžou obnovit v rámci doby uchování. | No |
| Obnoví sadu objektů blob bloku k předchozímu bodu v čase. | Obnovení k určitému bodu v čase<br />[Víc se uč...](point-in-time-restore-overview.md) | Chcete-li použít obnovení k určitému bodu v čase, chcete-li se vrátit k dřívějšímu stavu, navrhněte aplikaci, aby se místo odstranění kontejnerů odstranily jednotlivé objekty blob bloku. | Sada objektů blob bloku může být v určitém okamžiku v minulosti vrácena do jejich stavu.<br /><br />Vráceny se pouze operace provedené u objektů blob bloku. Nevrátí se žádné operace prováděné na kontejnerech, objektech blob stránky nebo doplňovací objekty blob. | No |
| Ručně uložte stav objektu BLOB v daném časovém okamžiku. | Snímek objektu BLOB<br />[Víc se uč...](snapshots-overview.md) | Doporučuje se jako alternativa pro správu verzí objektů blob, pokud není pro váš scénář vhodný, protože se jedná o náklady nebo jiné okolnosti, nebo pokud má účet úložiště povolený hierarchický obor názvů. | Objekt BLOB může být obnoven ze snímku, pokud je objekt BLOB přepsán. Pokud je objekt BLOB odstraněný, odstraní se i snímky. | Ano, ve verzi Preview |
| Objekt BLOB se dá odstranit nebo přepsat, ale data se pravidelně zkopírují do druhého účtu úložiště. | Vyveďte vlastní řešení pro kopírování dat do druhého účtu pomocí Azure Storage replikace objektů nebo pomocí nástroje, jako je AzCopy nebo Azure Data Factory. | Doporučuje se pro ochranu s ohledem na neočekávané úmyslné akce nebo nepředvídatelné scénáře.<br /><br />Vytvořte druhý účet úložiště ve stejné oblasti jako primární účet, abyste se vyhnuli poplatkům za výstup. | Data je možné obnovit z druhého účtu úložiště, pokud je primární účet ohrožen jakýmkoli způsobem. | AzCopy a Azure Data Factory jsou podporovány.<br /><br />Replikace objektů není podporována. |

## <a name="data-protection-by-resource-type"></a>Ochrana dat podle typu prostředku

Následující tabulka shrnuje Azure Storage možnosti ochrany dat v závislosti na prostředcích, které chrání.

| Možnost ochrany dat | Chrání účet před odstraněním. | Chrání kontejner před odstraněním. | Chrání objekt BLOB před odstraněním. | Chrání objekt BLOB proti přepsání. |
|--|--|--|--|--|
| Azure Resource Manager zámek | Yes | Ne<sup>1</sup> | No | No |
| Zásady neměnnosti na kontejneru | Ano<sup>2</sup> | Yes | Yes | Yes |
| Obnovitelné odstranění kontejneru | No | Yes | No | No |
| Verze objektu blobing<sup>3</sup> | No | No | Yes | Yes |
| Částečný Delete objektu BLOB<sup>3</sup> | No | No | Yes | Yes |
| Obnovení bodu v čase<sup>3</sup> | No | No | Yes | Yes |
| Snímek objektu BLOB | No | No | No | Yes |
| Shrnutí řešení pro kopírování dat na druhý účet<sup>4</sup> | No | Yes | Yes | Yes |

<sup>1</sup> Azure Resource Manager zámek nechrání kontejner před odstraněním.<br />
<sup>2</sup> Pokud je v případě kontejneru platný pracovní prostor nebo zamčené zásady uchovávání informací, účet úložiště je taky chráněný před odstraněním.<br />
<sup>3</sup> aktuálně se nepodporuje pro úlohy Data Lake Storage.<br />
<sup>4</sup> AzCopy a Azure Data Factory jsou možnosti, které jsou podporované pro úlohy Blob Storage a data Lake Storage. Replikace objektů se podporuje jenom pro Blob Storage úlohy.<br />

## <a name="recover-deleted-or-overwritten-data"></a>Obnovení odstraněných nebo přepsaných dat

Pokud potřebujete obnovit data, která byla přepsána nebo odstraněna, postupujte podle postupu v závislosti na tom, jaké možnosti ochrany dat jste povolili a které prostředky byly ovlivněny. Následující tabulka popisuje akce, které můžete provést pro obnovení dat.

| Odstraněný nebo přepsaný prostředek | Možné akce obnovení | Požadavky na obnovení |
|--|--|--|
| Účet úložiště | Pokus o obnovení odstraněného účtu úložiště<br />[Víc se uč...](../common/storage-account-recover.md) | Účet úložiště byl původně vytvořen s modelem nasazení Azure Resource Manager a byl odstraněn během posledních 14 dnů. Od odstranění původního účtu se nevytvořil nový účet úložiště se stejným názvem. |
| Kontejner | Obnovení nepodmíněného odstraněné kontejneru a jeho obsahu<br />[Víc se uč...](soft-delete-container-enable.md) | Obnovitelné odstranění kontejneru je povolené a doba uchování pro částečný odstranění kontejneru ještě nevypršela. |
| Kontejnery a objekty blob | Obnovení dat z druhého účtu úložiště | Všechny operace kontejneru a objektů BLOB byly efektivně replikovány do druhého účtu úložiště. |
| Objekt BLOB (libovolný typ) | Obnovení objektu BLOB z předchozí verze<sup>1</sup><br />[Víc se uč...](versioning-enable.md) | Správa verzí objektů BLOB je povolená a objekt BLOB má jednu nebo více předchozích verzí. |
| Objekt BLOB (libovolný typ) | Obnovení podmíněného odstraněného objektu BLOB<sup>1</sup><br />[Víc se uč...](soft-delete-blob-enable.md) | Je povolené obnovitelné odstranění objektu BLOB a interval doby uchování obnovitelného odstranění vypršel. |
| Objekt BLOB (libovolný typ) | Obnovení objektu BLOB ze snímku<br />[Víc se uč...](snapshots-manage-dotnet.md) | Objekt BLOB má jeden nebo více snímků. |
| Sada objektů blob bloku | Obnovení sady objektů blob bloku do jejich stavu v dřívějším bodě v čase<sup>1</sup><br />[Víc se uč...](point-in-time-restore-manage.md) | Obnovení k bodu v čase je povolené a bod obnovení je v intervalu uchování. Účet úložiště nebyl napadený nebo poškozený. |
| Verze objektu BLOB | Obnovení softwarové odstraněné verze<sup>1</sup><br />[Víc se uč...](soft-delete-blob-enable.md) | Je povolené obnovitelné odstranění objektu BLOB a interval doby uchování obnovitelného odstranění vypršel. |

<sup>1</sup> aktuálně se nepodporuje pro úlohy Data Lake Storage.

## <a name="summary-of-cost-considerations"></a>Souhrn informací o nákladech

Následující tabulka shrnuje cenové požadavky na různé možnosti ochrany dat popsané v této příručce.

| Možnost ochrany dat | Důležité informace o nákladech |
|-|-|
| Azure Resource Manager zámek pro účet úložiště | Pro konfiguraci zámku na účtu úložiště se neúčtují žádné poplatky. |
| Zásady neměnnosti na kontejneru | Bez poplatků za konfiguraci zásad neměnnosti v kontejneru. |
| Obnovitelné odstranění kontejneru | Bez poplatků za povolení nepodmíněného odstranění kontejneru pro účet úložiště. Data v kontejneru s odstraněnou příčtením se účtují stejnou sazbou jako aktivní data, dokud se neodstraní odstraněný kontejner bez trvalého odstranění. |
| Správa verzí objektů BLOB | Bez poplatků za povolení správy verzí objektů BLOB pro účet úložiště. Po povolení správy verzí objektů BLOB vytvoří každá operace zápisu nebo odstranění objektu BLOB v účtu novou verzi, což může vést ke zvýšení nákladů na kapacitu.<br /><br />Verze objektu BLOB se účtuje na základě jedinečných bloků nebo stránek. Náklady se proto zvyšují, protože se základní objekt BLOB liší od konkrétní verze. Změna úrovně objektu BLOB nebo objektu BLOB může mít vliv na fakturaci. Další informace najdete v tématu [ceny a fakturace](versioning-overview.md#pricing-and-billing).<br /><br />Pomocí správy životního cyklu můžete podle potřeby odstraňovat starší verze, abyste mohli řídit náklady. Další informace najdete v tématu [optimalizace nákladů díky automatizaci úrovní přístupu Azure Blob Storage](storage-lifecycle-management-concepts.md). |
| Obnovitelné odstranění objektu BLOB | Bez poplatků za povolení obnovitelného odstranění objektů BLOB pro účet úložiště Data v objektu BLOB s odstraněným odstraněnou se účtují stejnou sazbou jako aktivní data, dokud se neodstraní objekt BLOB s odstraněnou podmnožinou. |
| Obnovení k určitému bodu v čase | Bez poplatků za povolení obnovení k určitému bodu v čase pro účet úložiště; povolení obnovení k časovému okamžiku ale umožňuje také vytvářet verze objektů blob, obnovitelné odstranění a změny kanálu, z nichž každá může mít za následek další poplatky.<br /><br />Po provedení operace obnovení se vám bude účtovat obnovení k určitému bodu v čase. Náklady na operaci obnovení závisí na množství obnovených dat. Další informace najdete v tématu [ceny a fakturace](point-in-time-restore-overview.md#pricing-and-billing). |
| Snímky objektů BLOB | Data ve snímku se účtují na základě jedinečných bloků nebo stránek. Náklady se proto zvyšují vzhledem k tomu, že se základní objekt BLOB odchyluje od snímku. Změna úrovně objektu BLOB nebo snímku může mít vliv na fakturaci. Další informace najdete v tématu [ceny a fakturace](snapshots-overview.md#pricing-and-billing).<br /><br />Pomocí správy životního cyklu můžete podle potřeby odstraňovat starší snímky, abyste mohli řídit náklady. Další informace najdete v tématu [optimalizace nákladů díky automatizaci úrovní přístupu Azure Blob Storage](storage-lifecycle-management-concepts.md). |
| Kopírování dat do druhého účtu úložiště | Údržba dat v druhém účtu úložiště bude mít za následek náklady na kapacitu a transakce. Pokud se druhý účet úložiště nachází v jiné oblasti než zdrojový účet, pak se do tohoto druhého účtu nakopírují data a naúčtují se také poplatky za výstup. |

## <a name="disaster-recovery"></a>Zotavení po havárii

Azure Storage vždycky uchovává více kopií vašich dat, takže je chráněná před plánovanými a neplánovanými událostmi, včetně přechodných selhání hardwaru, sítě nebo výpadků napájení a obrovského přirozeného katastrofy. Redundance zajišťuje, že váš účet úložiště splňuje požadavky na dostupnost a odolnost i v případě selhání. Další informace o tom, jak nakonfigurovat účet úložiště pro vysokou dostupnost, najdete v tématu [Azure Storage redundance](../common/storage-redundancy.md).

V případě, že v datovém centru dojde k selhání, pokud je váš účet úložiště redundantní v rámci dvou geografických oblastí (geograficky redundantní), máte možnost převzít služby při selhání účtu z primární oblasti do sekundární oblasti. Další informace najdete v tématu [převzetí služeb při selhání při zotavení po havárii a účtu úložiště](../common/storage-disaster-recovery-guidance.md).

Převzetí služeb při selhání spravované zákazníkem se v současné době nepodporuje u účtů úložiště s povoleným hierarchickým oborem názvů. Další informace najdete v tématu [funkce služby Blob Storage dostupné v Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="next-steps"></a>Další kroky

- [Redundance Azure Storage](../common/storage-redundancy.md)
- [Zotavení po havárii a převzetí služeb při selhání účtu úložiště](../common/storage-disaster-recovery-guidance.md)
