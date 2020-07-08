---
title: Snímky objektů BLOB
titleSuffix: Azure Storage
description: Naučte se vytvořit snímek objektu BLOB jen pro čtení pro zálohování dat objektů BLOB v daném časovém okamžiku.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/02/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: cd8771afdc9c4462faaff758ffdd073382f69f53
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85550976"
---
# <a name="blob-snapshots"></a>Snímky objektů BLOB

Snímek je verze objektu BLOB jen pro čtení, která je pořízena v určitém časovém okamžiku.

> [!NOTE]
> Správa verzí objektů BLOB (Preview) nabízí alternativní způsob, jak zachovat historické kopie objektu BLOB. Další informace najdete v tématu [Správa verzí objektů BLOB (Preview)](versioning-overview.md).

## <a name="about-blob-snapshots"></a>O snímcích objektů BLOB

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

Snímek objektu BLOB je stejný jako základní objekt BLOB s tím rozdílem, že identifikátor URI objektu BLOB má hodnotu **DateTime** připojenou k identifikátoru URI objektu blob, aby označoval čas, kdy se snímek povedl. Pokud je například identifikátor URI objektu blob stránky `http://storagesample.core.blob.windows.net/mydrives/myvhd` , je identifikátor URI snímku podobný `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z` .

> [!NOTE]
> Všechny snímky sdílí identifikátor URI základního objektu BLOB. Jediným rozdílem mezi základním objektem BLOB a snímkem je připojená hodnota **DateTime** .
>

Objekt BLOB může mít libovolný počet snímků. Snímky zůstanou zachované, dokud se explicitně neodstraní, což znamená, že snímek nemůže být živý základní objekt BLOB. Můžete vytvořit výčet snímků přidružených k základnímu objektu BLOB a sledovat aktuální snímky.

Když vytvoříte snímek objektu blob, vlastnosti systému objektu BLOB se zkopírují do snímku se stejnými hodnotami. Metadata základního objektu BLOB se také zkopírují do snímku, pokud při vytváření snímku neurčíte samostatná metadata. Až snímek vytvoříte, můžete ho číst, kopírovat nebo odstranit, ale nemůžete ho upravovat.

Všechna zapůjčení přidružená k základnímu objektu BLOB neovlivňují snímek. U snímku nelze získat zapůjčení.

Soubor VHD se používá k uložení aktuálních informací a stavu disku virtuálního počítače. Disk můžete odpojit v rámci virtuálního počítače nebo ho vypnout a pak pořídit snímek jeho souboru VHD. Tento soubor snímku můžete použít později k načtení souboru VHD v daném časovém okamžiku a znovu vytvořit virtuální počítač.

## <a name="understand-how-snapshots-accrue-charges"></a>Vysvětlení způsobu nabíhání nákladů na snímky

Vytvoření snímku, který je kopií objektu BLOB jen pro čtení, může mít za následek další poplatky za ukládání dat do vašeho účtu. Při návrhu aplikace je důležité vědět, jak se tyto poplatky můžou snížit, abyste mohli minimalizovat náklady.

### <a name="important-billing-considerations"></a>Důležité informace o fakturaci

Následující seznam obsahuje klíčové body, které je potřeba vzít v úvahu při vytváření snímku.

- Váš účet úložiště se za jedinečné bloky nebo stránky účtuje bez ohledu na to, jestli jsou v objektu BLOB nebo ve snímku. U vašeho účtu se neúčtují další poplatky za snímky přidružené k objektu blob, dokud neaktualizujete objekt blob, na kterém jsou založené. Po aktualizaci základního objektu BLOB se odliší od jeho snímků. Pokud k tomu dojde, budou se vám účtovat jedinečné bloky nebo stránky v každém objektu BLOB nebo snímku.
- Když nahradíte blok v rámci objektu blob bloku, tento blok se následně účtuje jako jedinečný blok. To platí i v případě, že má blok stejné ID bloku a stejná data jako ve snímku. Po opětovném potvrzení bloku se odliší od jeho protějšku v jakémkoli snímku a za jeho data se vám budou účtovat poplatky. Totéž platí pro stránku v objektu blob stránky, který je aktualizovaný se stejnými daty.
- Nahrazení objektu blob bloku voláním metody [UploadFromFile] [dotnet_UploadFromFile], [UploadText] [dotnet_UploadText], [UploadFromStream] [dotnet_UploadFromStream] nebo [UploadFromByteArray] [dotnet_UploadFromByteArray] nahradí všechny bloky v objektu BLOB. Pokud máte snímek přidružený k tomuto objektu blob, všechny bloky v základním objektu BLOB a snímku se teď odchýlit a budou se vám účtovat všechny bloky v obou objektech blob. To platí i v případě, že data v základním objektu BLOB a snímku zůstanou stejná.
- Blob service Azure nemá žádný způsob, jak určit, jestli dva bloky obsahují identická data. Každý blok, který je nahraný a potvrzený, se považuje za jedinečný, a to i v případě, že má stejná data a stejné ID bloku. Vzhledem k tomu, že se poplatky za jedinečné bloky, je důležité vzít v úvahu, že aktualizace objektu blob, který má snímek, má za následek další jedinečné bloky a další poplatky.

### <a name="minimize-cost-with-snapshot-management"></a>Minimalizace nákladů pomocí správy snímků

Doporučujeme pečlivě spravovat vaše snímky, abyste se vyhnuli dodatečným poplatkům. Můžete dodržovat tyto osvědčené postupy, které vám pomohou minimalizovat náklady vzniklé úložištěm vašich snímků:

- Odstraňte a znovu vytvořte snímky přidružené k objektu BLOB při aktualizaci objektu blob, a to i v případě, že se aktualizujete se stejnými daty, pokud návrh aplikace nevyžaduje, abyste zachovali snímky. Odstraněním a opětovným vytvořením snímků objektu BLOB se můžete ujistit, že se objekty BLOB a snímky neodchylují.
- Pokud udržujete snímky pro objekt blob, vyhněte se volání [UploadFromFile] [dotnet_UploadFromFile], [UploadText] [dotnet_UploadText], [UploadFromStream] [dotnet_UploadFromStream] nebo [UploadFromByteArray] [dotnet_UploadFromByteArray] pro aktualizaci objektu BLOB. Tyto metody nahrazují všechny bloky v objektu blob, což způsobí, že základní objekt BLOB a jeho snímky se budou významně odchýlit. Místo toho aktualizujte nejmenší možný počet bloků pomocí metod [PutBlock] [dotnet_PutBlock] a [PutBlockList] [dotnet_PutBlockList].

### <a name="snapshot-billing-scenarios"></a>Scénáře fakturace snímků

Následující scénáře ukazují, jak se účtují poplatky za objekt blob bloku a jeho snímky.

#### <a name="scenario-1"></a>Scénář 1

Ve scénáři 1 se základní objekt BLOB po pořízení snímku neaktualizoval, takže se poplatky účtují jenom pro jedinečné bloky 1, 2 a 3.

![Prostředky Azure Storage](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-1.png)

#### <a name="scenario-2"></a>Scénář 2

Ve scénáři 2 se základní objekt BLOB aktualizoval, ale snímek ne. Blok 3 byl aktualizován, a přestože obsahuje stejná data a stejné ID, není stejný jako blok 3 ve snímku. Výsledkem je, že se účtu účtuje čtyři bloky.

![Prostředky Azure Storage](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-2.png)

#### <a name="scenario-3"></a>Scénář 3

Ve scénáři 3 se základní objekt BLOB aktualizoval, ale snímek ne. Blok 3 byl nahrazen blokem 4 v základním objektu blob, ale snímek stále odráží blok 3. Výsledkem je, že se účtu účtuje čtyři bloky.

![Prostředky Azure Storage](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-3.png)

#### <a name="scenario-4"></a>Scénář 4

Ve scénáři 4 byl základní objekt BLOB zcela aktualizován a neobsahuje žádné původní bloky. V důsledku toho se účet účtuje za všechny osm jedinečných bloků. K tomuto scénáři může dojít, pokud používáte metodu aktualizace, například [UploadFromFile] [dotnet_UploadFromFile], [UploadText] [dotnet_UploadText], [UploadFromStream] [dotnet_UploadFromStream] nebo [UploadFromByteArray] [dotnet_UploadFromByteArray], protože tyto metody nahrazují celý obsah objektu BLOB.

![Prostředky Azure Storage](./media/snapshots-overview/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>Další kroky

- [Vytvoření a Správa snímku objektu BLOB v .NET](snapshots-manage-dotnet.md)
- [Zálohování nespravovaných disků virtuálních počítačů Azure pomocí přírůstkových snímků](../../virtual-machines/windows/incremental-snapshots.md)
