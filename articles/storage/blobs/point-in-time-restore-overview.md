---
title: Obnovení bodu v čase pro objekty blob bloku (Preview)
titleSuffix: Azure Storage
description: Obnovení k určitému bodu v čase pro objekty blob bloku zajišťuje ochranu proti náhodnému odstranění nebo poškození tím, že vám umožní obnovit účet úložiště do předchozího stavu v daném časovém okamžiku.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 1187b01fa623264055edecf21ea5c9d35d59a152
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2020
ms.locfileid: "90068298"
---
# <a name="point-in-time-restore-for-block-blobs-preview"></a>Obnovení bodu v čase pro objekty blob bloku (Preview)

Obnovení k bodu v čase poskytuje ochranu proti náhodnému odstranění nebo poškození tím, že umožňuje obnovit data objektů blob bloku do dřívějšího stavu. Obnovení k určitému bodu v čase je užitečné ve scénářích, kdy uživatel nebo aplikace nechtěně odstraní data nebo kde chyba aplikace poškozuje data. Obnovení k určitému bodu v čase umožňuje také scénáře testování, které před spuštěním dalších testů vyžadují vrácení sady dat do známého stavu.

Další informace o tom, jak povolit obnovení k určitému bodu v čase pro účet úložiště, najdete v tématu [povolení a Správa obnovení k určitému bodu v čase pro objekty blob bloku (Preview)](point-in-time-restore-manage.md).

## <a name="how-point-in-time-restore-works"></a>Jak funguje obnovení k časovému okamžiku

Pokud chcete povolit obnovení k určitému bodu v čase, vytvořte zásadu správy pro účet úložiště a zadejte dobu uchování. Během doby uchování můžete obnovit objekty blob bloku z současného stavu do stavu v předchozím bodě v čase.

Chcete-li zahájit obnovení k určitému bodu v čase, zavolejte operaci [obnovit rozsahy objektů BLOB](/rest/api/storagerp/storageaccounts/restoreblobranges) a určete bod obnovení v čase UTC. Můžete zadat lexicographical rozsahy názvů kontejnerů a objektů blob, které se mají obnovit, nebo vynecháte rozsah pro obnovení všech kontejnerů v účtu úložiště. Pro každou operaci obnovení se podporuje až 10 lexicographical rozsahů.

Azure Storage analyzuje všechny změny provedené v zadaných objektech blob mezi požadovaným bodem obnovení, který je zadaný v čase UTC, a v současné době. Operace obnovení je atomická, takže se buď úspěšně dokončí obnovení všech změn, nebo selže. Pokud existují objekty blob, které se nedají obnovit, operace se nezdařila a operace čtení a zápisu do ovlivněných kontejnerů se obnoví.

V účtu úložiště se dá spustit jenom jedna operace obnovení. Operaci obnovení nelze zrušit, jakmile probíhá, ale můžete provést druhou operaci obnovení, která vrátí zpět první operaci.

Operace **obnovit rozsahy objektů BLOB** vrátí ID obnovení, které jedinečně identifikuje operaci. Chcete-li zjistit stav obnovení k určitému bodu v čase, zavolejte operaci **získat stav obnovení** s ID obnovení vráceným z operace **obnovit rozsahy objektů BLOB** .

Mějte na paměti následující omezení operací obnovení:

- Blok, který byl nahrán prostřednictvím [bloku Put](/rest/api/storageservices/put-block) nebo [bloku Put z adresy URL](/rest/api/storageservices/put-block-from-url), ale není potvrzen prostřednictvím [seznamu blokovaných](/rest/api/storageservices/put-block-list)objektů, není součástí objektu blob, takže není obnoven v rámci operace obnovení.
- Objekt BLOB s aktivním zapůjčením nejde obnovit. Pokud je objekt BLOB s aktivním zapůjčením zahrnutý do rozsahu objektů blob, které se mají obnovit, operace obnovení se nezdařila.
- Snímky se v rámci operace obnovení nevytváří ani neodstraňují. Do předchozího stavu se obnoví jenom základní objekt BLOB.
- Pokud se objekt BLOB přesunul mezi horkou a studenou vrstvou v období od současného a bodu obnovení, obnoví se objekt blob do předchozí úrovně. Objekt blob, který se přesunul do archivní úrovně, se ale neobnoví.

> [!IMPORTANT]
> Když provádíte operaci obnovení, Azure Storage blokuje operace s daty u objektů BLOB v rozsahu obnovování po dobu trvání operace. Operace čtení, zápisu a odstranění jsou v primárním umístění blokované. Z tohoto důvodu nemusí operace, jako je například výpis kontejnerů v Azure Portal, fungovat podle očekávání, zatímco probíhá operace obnovení.
>
> Operace čtení ze sekundárního umístění můžou během operace obnovení pokračovat, pokud je účet úložiště geograficky replikovaný.

> [!CAUTION]
> Obnovení k bodu v čase podporuje pouze obnovení operací pouze pro objekty blob bloku. Operace na kontejnerech nelze obnovit. Pokud odstraníte kontejner z účtu úložiště voláním operace [odstranění kontejneru](/rest/api/storageservices/delete-container) během obnovování k určitému bodu v čase, nelze tento kontejner obnovit pomocí operace obnovení. V rámci verze Preview místo odstranění kontejneru odstraňte jednotlivé objekty blob, pokud je budete chtít obnovit.

### <a name="prerequisites-for-point-in-time-restore"></a>Předpoklady pro obnovení k bodu v čase

Obnovení k bodu v čase vyžaduje, aby byly povolené následující funkce Azure Storage:

- [Obnovitelné odstranění](soft-delete-overview.md)
- [Změnit kanál (Preview)](storage-blob-change-feed.md)
- [Správa verzí objektů BLOB](versioning-overview.md)

Než povolíte obnovení k bodu v čase, povolte tyto funkce pro účet úložiště. Před tím, než je povolíte, si nezapomeňte zaregistrovat verze Preview pro náhled kanálu změn a verzí objektů BLOB.

### <a name="retention-period-for-point-in-time-restore"></a>Doba uchování pro obnovení k určitému bodu v čase

Když pro účet úložiště povolíte obnovení k určitému bodu v čase, zadáte dobu uchování. Objekty blob bloku v účtu úložiště se dají obnovit během doby uchování.

Doba uchování začíná, když povolíte obnovení k určitému bodu v čase. Mějte na paměti, že nemůžete obnovit objekty blob do stavu před začátkem doby uchování. Pokud jste například povolili obnovení k určitému bodu v čase, může 1. až do 30 dnů trvat, a až do 15 dnů budete moct obnovení obnovit. 1. června můžete data obnovit z 1 až 30 dnů.

Doba uchování pro obnovení k určitému bodu v čase musí být alespoň jeden den kratší než doba uchování zadaná pro obnovitelné odstranění. Například pokud je doba uchování obnovitelného odstranění nastavená na 7 dní, pak doba uchování obnovení k určitému bodu v čase může být v rozmezí od 1 do 6 dnů.

### <a name="permissions-for-point-in-time-restore"></a>Oprávnění k obnovení k časovému okamžiku

Aby bylo možné zahájit operaci obnovení, musí mít klient oprávnění pro zápis do všech kontejnerů v účtu úložiště. Pokud chcete udělit oprávnění k autorizaci operace obnovení s Azure Active Directory (Azure AD), přiřaďte roli **Přispěvatel účtu úložiště** k objektu zabezpečení na úrovni účtu úložiště, skupiny prostředků nebo předplatného.

## <a name="about-the-preview"></a>O verzi Preview

Obnovení k bodu v čase se podporuje jenom pro účty úložiště pro obecné účely verze 2. Pomocí obnovení k bodu v čase lze obnovit pouze data z horké a studené úrovně přístupu.

Následující oblasti podporují obnovení k bodu v čase ve verzi Preview:

- Střední Kanada
- Kanada – východ
- Francie – střed

Verze Preview zahrnuje tato omezení:

- Obnovení objektů blob bloku Premium se nepodporuje.
- Obnovení objektů blob na archivní úrovni se nepodporuje. Pokud se například objekt blob na horké úrovni před dvěma dny přesunul na archivní úroveň a operace obnovení provádí obnovení k bodu před třemi dny, objekt blob se neobnoví na horké úrovni.
- Obnovení Azure Data Lake Storage Gen2 plochých a hierarchických oborů názvů není podporováno.
- Obnovení účtů úložiště pomocí klíčů poskytnutých zákazníkem se nepodporuje.

> [!IMPORTANT]
> Obnovení bodu v čase je ve verzi Preview určeno pouze pro neprodukční použití. Smlouvy o úrovni produkčních služeb (SLA) nejsou aktuálně k dispozici.

### <a name="register-for-the-preview"></a>Zaregistrovat se pro verzi Preview

Pokud se chcete zaregistrovat ve verzi Preview, spusťte následující příkazy:

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
# Register for the point-in-time restore preview
Register-AzProviderFeature -FeatureName RestoreBlobRanges -ProviderNamespace Microsoft.Storage

# Register for change feed (preview)
Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage

# Register for Blob versioning
Register-AzProviderFeature -FeatureName Versioning -ProviderNamespace Microsoft.Storage

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az feature register --namespace Microsoft.Storage --name RestoreBlobRanges
az feature register --namespace Microsoft.Storage --name Changefeed
az feature register --namespace Microsoft.Storage --name Versioning
az provider register --namespace 'Microsoft.Storage'
```

---

### <a name="check-registration-status"></a>Ověřit stav registrace

Registrace pro obnovení bodu v čase je automatická a měla by trvat méně než 10 minut. Chcete-li zjistit stav registrace, spusťte následující příkazy:

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName RestoreBlobRanges

Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Changefeed

Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Versioning
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/RestoreBlobRanges')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/Changefeed')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/Versioning')].{Name:name,State:properties.state}"
```

---

## <a name="pricing-and-billing"></a>Ceny a fakturace

Fakturace pro obnovení k bodu v čase závisí na množství zpracovaných dat k provedení operace obnovení. Množství zpracovaných dat vychází z počtu změn, ke kterým došlo mezi bodem obnovení a současným okamžikem. Například za předpokladu poměrně konstantní frekvence změny pro blokování dat objektů BLOB v účtu úložiště, operace obnovení, která se vrátí za čas 1 den, by cena obnovení, která se vrátí za čas po dobu 10 dní, byla 1 desetinná.

Chcete-li odhadnout náklady na operaci obnovení, Projděte si protokol změn kanálu, abyste mohli odhadnout množství dat, která byla během období obnovení změněna. Například pokud doba uchovávání kanálu pro změnu je 30 dní a velikost kanálu změn je 10 MB, pak obnovení do výše uvedeného bodu 10 dnů by se za účet LRS v této oblasti dozvědělo přibližně o třetinu ceny. Obnovení do bodu, který je starší než 27 dní, by mělo mít náklady přibližně devět desetin cen uvedených v seznamu.

Další informace o cenách pro obnovení k bodu v čase najdete v tématu ceny za objekty [blob bloku](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="ask-questions-or-provide-feedback"></a>Položte otázky nebo poskytněte zpětnou vazbu.

Chcete-li klást otázky na verzi Preview k určitému bodu v čase nebo poskytnout zpětnou vazbu, obraťte se na společnost Microsoft na adrese pitrdiscussion@microsoft.com .

## <a name="next-steps"></a>Další kroky

- [Povolte a spravujte obnovení k určitému bodu v čase pro objekty blob bloku (Preview).](point-in-time-restore-manage.md)
- [Změna podpory kanálu v Azure Blob Storage (Preview)](storage-blob-change-feed.md)
- [Povolení obnovitelného odstranění pro objekty blob](soft-delete-enable.md)
- [Povolení a správa verzí objektů BLOB](versioning-enable.md)
