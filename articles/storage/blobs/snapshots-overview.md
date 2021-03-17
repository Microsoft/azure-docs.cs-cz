---
title: Snímky objektů BLOB
titleSuffix: Azure Storage
description: Pochopte, jak fungují snímky objektů BLOB a jak se účtují.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/02/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: debce0a1b4c09bb89cdceb1cd29e59e1976c939a
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539144"
---
# <a name="blob-snapshots"></a>Snímky objektů BLOB

Snímek je verze objektu BLOB jen pro čtení, která je pořízena v určitém časovém okamžiku.

> [!NOTE]
> Správa verzí objektů BLOB nabízí lepší způsob, jak zachovat předchozí verze objektu BLOB. Další informace najdete v tématu [Správa verzí objektů BLOB](versioning-overview.md).

## <a name="about-blob-snapshots"></a>O snímcích objektů BLOB

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

Snímek objektu BLOB je stejný jako základní objekt BLOB s tím rozdílem, že identifikátor URI objektu BLOB má hodnotu **DateTime** připojenou k identifikátoru URI objektu blob, aby označoval čas, kdy se snímek povedl. Pokud je například identifikátor URI objektu blob stránky `http://storagesample.core.blob.windows.net/mydrives/myvhd` , je identifikátor URI snímku podobný `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z` .

> [!NOTE]
> Všechny snímky sdílí identifikátor URI základního objektu BLOB. Jediným rozdílem mezi základním objektem BLOB a snímkem je připojená hodnota **DateTime** .

Objekt BLOB může mít libovolný počet snímků. Snímky zůstanou zachované, dokud se explicitně neodstraní, ať už nezávisle, nebo jako součást operace [odstranění objektu BLOB](/rest/api/storageservices/delete-blob) pro základní objekt BLOB. Můžete vytvořit výčet snímků přidružených k základnímu objektu BLOB a sledovat aktuální snímky.

Když vytvoříte snímek objektu blob, vlastnosti systému objektu BLOB se zkopírují do snímku se stejnými hodnotami. Metadata základního objektu BLOB se také zkopírují do snímku, pokud při vytváření snímku neurčíte samostatná metadata. Až snímek vytvoříte, můžete ho číst, kopírovat nebo odstranit, ale nemůžete ho upravovat.

Všechna zapůjčení přidružená k základnímu objektu BLOB neovlivňují snímek. U snímku nelze získat zapůjčení.

Soubor VHD se používá k uložení aktuálních informací a stavu disku virtuálního počítače. Disk můžete odpojit v rámci virtuálního počítače nebo ho vypnout a pak pořídit snímek jeho souboru VHD. Tento soubor snímku můžete použít později k načtení souboru VHD v daném časovém okamžiku a znovu vytvořit virtuální počítač.

## <a name="understand-how-snapshots-accrue-charges"></a>Vysvětlení způsobu nabíhání nákladů na snímky

### <a name="important-billing-considerations"></a>Důležité informace o fakturaci

Následující seznam obsahuje klíčové body, které je potřeba vzít v úvahu při vytváření snímku.

- Váš účet úložiště se za jedinečné bloky nebo stránky účtuje bez ohledu na to, jestli jsou v objektu BLOB nebo ve snímku. U vašeho účtu se neúčtují další poplatky za snímky přidružené k objektu blob, dokud neaktualizujete objekt blob, na kterém jsou založené. Po aktualizaci základního objektu BLOB se odliší od jeho snímků. Pokud k tomu dojde, budou se vám účtovat jedinečné bloky nebo stránky v každém objektu BLOB nebo snímku.
- Když nahradíte blok v rámci objektu blob bloku, tento blok se následně účtuje jako jedinečný blok. To platí i v případě, že má blok stejné ID bloku a stejná data jako ve snímku. Po opětovném potvrzení bloku se odliší od jeho protějšku v jakémkoli snímku a za jeho data se vám budou účtovat poplatky. Totéž platí pro stránku v objektu blob stránky, který je aktualizovaný se stejnými daty.
- Aktualizace objektu blob bloku voláním metody, která přepíše celý obsah objektu blob, nahradí všechny bloky v objektu BLOB. Pokud máte snímek přidružený k tomuto objektu blob, všechny bloky v základním objektu BLOB a snímku se teď odchýlit a budou se vám účtovat všechny bloky v obou objektech blob. To platí i v případě, že data v základním objektu BLOB a snímku zůstanou stejná.
- Blob service Azure nemá žádný způsob, jak určit, jestli dva bloky obsahují identická data. Každý blok, který je nahraný a potvrzený, se považuje za jedinečný, a to i v případě, že má stejná data a stejné ID bloku. Vzhledem k tomu, že se poplatky za jedinečné bloky, je důležité vzít v úvahu, že aktualizace objektu blob, který má snímek, má za následek další jedinečné bloky a další poplatky.

### <a name="minimize-costs-with-snapshot-management"></a>Minimalizace nákladů pomocí správy snímků

Doporučujeme pečlivě spravovat vaše snímky, abyste se vyhnuli dodatečným poplatkům. Můžete dodržovat tyto osvědčené postupy, které vám pomohou minimalizovat náklady vzniklé úložištěm vašich snímků:

- Odstraňte a znovu vytvořte snímky přidružené k objektu BLOB při aktualizaci objektu blob, a to i v případě, že se aktualizujete se stejnými daty, pokud návrh aplikace nevyžaduje, abyste zachovali snímky. Odstraněním a opětovným vytvořením snímků objektu BLOB se můžete ujistit, že se objekty BLOB a snímky neodchylují.
- Pokud udržujete snímky pro objekt blob, vyhněte se volání metod, které přepíší celý objekt BLOB při aktualizaci objektu BLOB. Místo toho aktualizujte nejmenším možným počtem bloků, aby se zajistilo nízké náklady.

### <a name="snapshot-billing-scenarios"></a>Scénáře fakturace snímků

Následující scénáře ukazují, jak se účtují poplatky za objekt blob bloku a jeho snímky.

## <a name="pricing-and-billing"></a>Ceny a fakturace

Vytvoření snímku, který je kopií objektu BLOB jen pro čtení, může mít za následek další poplatky za ukládání dat do vašeho účtu. Při návrhu aplikace je důležité vědět, jak se tyto poplatky můžou snížit, abyste mohli minimalizovat náklady.

Snímky objektů blob, jako jsou verze objektů blob, se účtují stejnou sazbou jako aktivní data. Jak se účtují snímky, záleží na tom, jestli jste explicitně nastavili vrstvu základního objektu BLOB nebo pro některý z jeho snímků (nebo verzí). Další informace o úrovních objektů BLOB najdete v tématu [Azure Blob Storage: horká, studená a archivní úroveň přístupu](storage-blob-storage-tiers.md).

Pokud jste nezměnili úroveň objektu BLOB nebo snímku, budou se vám účtovat jedinečné bloky dat v rámci tohoto objektu blob, jeho snímky a libovolné verze, které může mít. Další informace najdete v tématu [fakturace, pokud nebyla vrstva objektů BLOB explicitně nastavena](#billing-when-the-blob-tier-has-not-been-explicitly-set).

Pokud jste změnili úroveň objektu BLOB nebo snímku, bude se vám účtovat celý objekt bez ohledu na to, jestli je objekt BLOB a snímek nakonec ve stejné vrstvě. Další informace najdete v tématu [fakturace, pokud byla vrstva objektů BLOB explicitně nastavena](#billing-when-the-blob-tier-has-been-explicitly-set).

Další informace o fakturaci pro verze objektů BLOB najdete v tématu [Správa verzí objektů BLOB](versioning-overview.md).

### <a name="billing-when-the-blob-tier-has-not-been-explicitly-set"></a>Fakturace v případě, že vrstva objektů BLOB nebyla explicitně nastavena

Pokud jste vrstvu objektů BLOB pro základní objekt BLOB nebo žádný z jeho snímků nestavili explicitně, budou se vám účtovat jedinečné bloky nebo stránky v rámci objektu blob, jeho snímky a libovolné verze, které může mít. Data sdílená v rámci objektu BLOB a jeho snímky se účtují jenom jednou. Po aktualizaci objektu BLOB se data v základním objektu BLOB odchylují od dat uložených ve svých snímcích a jedinečná data se účtují podle bloku nebo stránky.

Když nahradíte blok v rámci objektu blob bloku, tento blok se následně účtuje jako jedinečný blok. To platí i v případě, že má blok stejné ID bloku a stejná data jako ve snímku. Po opětovném potvrzení bloku se odliší od jeho protějšku ve snímku a bude vám účtováno za jeho data. Totéž platí pro stránku v objektu blob stránky, který je aktualizovaný se stejnými daty.

Úložiště objektů BLOB nemá způsob, jak určit, zda dva bloky obsahují identická data. Každý blok, který je nahraný a potvrzený, se považuje za jedinečný, a to i v případě, že má stejná data a stejné ID bloku. Vzhledem k tomu, že se poplatky za jedinečné bloky dostanou, je důležité mít na paměti, že aktualizace objektu BLOB v případě, kdy má tento objekt BLOB snímky nebo verze, bude mít za následek další jedinečné bloky a další poplatky.

Pokud má objekt BLOB snímky, volejte operace aktualizace pro objekty blob bloku tak, aby aktualizovaly nejnižší možný počet bloků. Operace zápisu, které umožňují jemně odstupňovanou kontrolu nad bloky, jsou [vloženy do bloku](/rest/api/storageservices/put-block) a [seznamu blokovaných umístění](/rest/api/storageservices/put-block-list). Operace [Put BLOB](/rest/api/storageservices/put-blob) na druhé straně nahrazuje celý obsah objektu blob, takže může vést k dalším poplatkům.

Následující scénáře ukazují, jak se účtují poplatky za objekt blob bloku a jeho snímky, pokud nebyla vrstva objektů BLOB explicitně nastavena.

#### <a name="scenario-1"></a>Scénář 1

Ve scénáři 1 se základní objekt BLOB po pořízení snímku neaktualizoval, takže se poplatky účtují jenom pro jedinečné bloky 1, 2 a 3.

![Diagram 1 znázorňující fakturaci jedinečných bloků v základním objektu BLOB a snímku](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-1.png)

#### <a name="scenario-2"></a>Scénář 2

Ve scénáři 2 se základní objekt BLOB aktualizoval, ale snímek ne. Blok 3 byl aktualizován, a přestože obsahuje stejná data a stejné ID, není stejný jako blok 3 ve snímku. Výsledkem je, že se účtu účtuje čtyři bloky.

![Diagram 2 znázorňující fakturaci jedinečných bloků v základním objektu BLOB a snímku](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-2.png)

#### <a name="scenario-3"></a>Scénář 3

Ve scénáři 3 se základní objekt BLOB aktualizoval, ale snímek ne. Blok 3 byl nahrazen blokem 4 v základním objektu blob, ale snímek stále odráží blok 3. Výsledkem je, že se účtu účtuje čtyři bloky.

![Diagram 3 znázorňující fakturaci jedinečných bloků v základním objektu BLOB a snímku](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-3.png)

#### <a name="scenario-4"></a>Scénář 4

Ve scénáři 4 byl základní objekt BLOB zcela aktualizován a neobsahuje žádné původní bloky. V důsledku toho se účet účtuje za všechny osm jedinečných bloků.

![Diagram 4 znázorňující fakturaci jedinečných bloků v základním objektu BLOB a snímku](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-4.png)

> [!TIP]
> Vyhněte se volání metod, které přepisují celý objekt blob, a místo toho aktualizujte jednotlivé bloky, abyste zachovali náklady.

### <a name="billing-when-the-blob-tier-has-been-explicitly-set"></a>Fakturace při explicitně nastavené úrovni objektu BLOB

Pokud jste explicitně nastavili úroveň objektu BLOB pro objekt BLOB nebo snímek (nebo verzi), bude se vám účtovat celá délka obsahu objektu v nové vrstvě bez ohledu na to, jestli sdílí bloky s objektem v původní úrovni. Účtuje se vám také celá velikost obsahu nejstarší verze v původní úrovni. Všechny verze nebo snímky, které zůstanou v původní úrovni, se účtují za jedinečné bloky, které mohou sdílet, jak je popsáno v tématu [fakturace, pokud nebyla vrstva objektů BLOB explicitně nastavena](#billing-when-the-blob-tier-has-not-been-explicitly-set).

#### <a name="moving-a-blob-to-a-new-tier"></a>Přesun objektu BLOB na novou úroveň

Následující tabulka popisuje chování fakturace objektu BLOB nebo snímku při jeho přesunutí do nové úrovně.

| Pokud je vrstva objektů BLOB nastavena explicitně na... | Pak se vám bude účtovat... |
|-|-|
| Základní objekt BLOB se snímkem | Základní objekt BLOB v nové vrstvě a nejstarší snímek v původní vrstvě a všechny jedinečné bloky na dalších snímcích. <sup>1</sup> |
| Základní objekt BLOB s předchozí verzí a snímkem | Základní objekt BLOB v nové vrstvě, nejstarší verze v původní vrstvě a nejstarší snímek v původní vrstvě, včetně všech jedinečných bloků v jiných verzích nebo snímcích<sup>1</sup>. |
| Snímek | Snímek v nové vrstvě a základní objekt BLOB v původní vrstvě a všechny jedinečné bloky na dalších snímcích. <sup>1</sup> |

<sup>1</sup> Pokud jsou k dispozici jiné předchozí verze nebo snímky, které nebyly přesunuty z původní úrovně, jsou tyto verze nebo snímky účtovány na základě počtu jedinečných bloků, které obsahují, jak je popsáno v tématu [fakturace, pokud nebyla vrstva objektů BLOB explicitně nastavena](#billing-when-the-blob-tier-has-not-been-explicitly-set).

Následující diagram znázorňuje, jak se účtují objekty, když se objekt BLOB s snímky přesune na jinou úroveň.

:::image type="content" source="media/snapshots-overview/snapshot-billing-tiers.png" alt-text="Diagram znázorňující, jak se účtují objekty, když je objekt BLOB se snímky explicitně vrstvený":::

Explicitní nastavení vrstvy pro objekt blob, verzi nebo snímek nelze vrátit zpět. Pokud objekt BLOB přesunete do nové úrovně a pak ho přesunete zpátky do původní úrovně, bude se vám účtovat úplná délka obsahu objektu, i když sdílí bloky s jinými objekty v původní úrovni.

Operace, které explicitně nastavily úroveň objektu blob, verze nebo snímku, zahrnují:

- [Nastavení úrovně objektu blob](/rest/api/storageservices/set-blob-tier)
- [Vložit objekt BLOB](/rest/api/storageservices/put-blob) se zadanou úrovní
- [Seznam blokovaných umístění](/rest/api/storageservices/put-block-list) se zadanou úrovní
- [Kopírovat objekt BLOB](/rest/api/storageservices/copy-blob) se specifikovanou úrovní

#### <a name="deleting-a-blob-when-soft-delete-is-enabled"></a>Odstranění objektu blob, když je povolené obnovitelné odstranění

Pokud je povoleno podmíněné odstranění objektů blob, pokud odstraníte nebo přepíšete základní objekt blob, který má explicitně nastavenou vrstvu, budou se všechny předchozí verze nebo snímky objektu BLOB s přístupným odstraněnou délkou účtovat s plnou délkou obsahu. Další informace o tom, jak se ve spolupráci se správou verzí a obnovitelného odstraňování objektů blob, najdete v tématu [Správa verzí objektů BLOB a obnovitelné odstranění](versioning-overview.md#blob-versioning-and-soft-delete).

Následující tabulka popisuje chování fakturace u objektu blob, který je podmíněně odstraněn, v závislosti na tom, jestli je Správa verzí povolená nebo zakázaná. Pokud je povolená Správa verzí, vytvoří se nová verze, když se odstraněný objekt BLOB. Když je Správa verzí zakázaná, měkké odstranění objektu BLOB vytvoří snímek s jemným odstraněním.

| Pokud přepíšete základní objekt BLOB s explicitně nastavenou vrstvou... | Pak se vám bude účtovat... |
|-|-|
| Pokud jsou povolené obnovitelné odstranění objektů BLOB a správa verzí | Všechny existující verze s plnou délkou obsahu bez ohledu na úroveň. |
| Pokud je povolené obnovitelné odstraňování objektů blob, ale je zakázaná správa verzí | Všechny existující snímky obnovitelného odstranění s plnou délkou obsahu bez ohledu na úroveň. |

## <a name="next-steps"></a>Další kroky

- [Správa verzí objektů BLOB](versioning-overview.md)
- [Vytvoření a Správa snímku objektu BLOB v .NET](snapshots-manage-dotnet.md)
- [Zálohování nespravovaných disků virtuálních počítačů Azure pomocí přírůstkových snímků](../../virtual-machines/windows/incremental-snapshots.md)
