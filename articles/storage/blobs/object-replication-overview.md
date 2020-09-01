---
title: Přehled replikace objektů (Preview)
titleSuffix: Azure Storage
description: Replikace objektů (Preview) asynchronně kopíruje objekty blob bloku mezi zdrojovým účtem úložiště a cílovým účtem. Replikaci objektů můžete použít k minimalizaci latence při čtení požadavků, ke zvýšení efektivity výpočetních úloh, k optimalizaci distribuce dat a k minimalizaci nákladů.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/28/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: edbce017b1090a029d49c9f2f8812e7e65133fcb
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2020
ms.locfileid: "89073127"
---
# <a name="object-replication-for-block-blobs-preview"></a>Replikace objektů pro objekty blob bloku (Preview)

Replikace objektů (Preview) asynchronně kopíruje objekty blob bloku mezi zdrojovým účtem úložiště a cílovým účtem. Mezi scénáře podporované replikací objektů patří:

- **Minimalizace latence.** Replikace objektů může snížit latenci pro požadavky na čtení tím, že klientům umožní využívat data z oblasti, která je blíže fyzické blízkosti.
- **Zvyšte efektivitu výpočetních úloh.** Díky replikaci objektů můžou výpočetní úlohy zpracovat stejné sady objektů blob bloku v různých oblastech.
- **Optimalizace distribuce dat.** Můžete zpracovávat nebo analyzovat data v jednom umístění a potom replikovat pouze výsledky do dalších oblastí.
- **Optimalizace nákladů.** Po dokončení replikace dat můžete snížit náklady přesunutím na úroveň archivu pomocí zásad správy životního cyklu.

Následující diagram znázorňuje, jak replikace objektů replikuje objekty blob bloku z účtu zdrojového úložiště v jedné oblasti do cílových účtů ve dvou různých oblastech.

:::image type="content" source="media/object-replication-overview/object-replication-diagram.svg" alt-text="Diagram znázorňující, jak funguje replikace objektů":::

Informace o tom, jak nakonfigurovat replikaci objektů, najdete v tématu [Konfigurace replikace objektů (Preview)](object-replication-configure.md).

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="object-replication-policies-and-rules"></a>Zásady a pravidla replikace objektů

Když konfigurujete replikaci objektů, vytvoříte zásadu replikace, která určuje zdrojový a cílový účet úložiště. Zásada replikace obsahuje jedno nebo více pravidel, která určují zdrojový kontejner a cílový kontejner, a označuje, které objekty blob bloku ve zdrojovém kontejneru budou replikovány.

Po nakonfigurování replikace objektů Azure Storage pravidelně kontrolovat kanál změny pro zdrojový účet a asynchronně replikuje všechny operace zápisu nebo odstranění do cílového účtu. Latence replikace závisí na velikosti replikované objekty blob bloku.

> [!IMPORTANT]
> Vzhledem k tomu, že jsou data objektů blob bloku replikována asynchronně, není zdrojový účet a cílový účet hned synchronizován. V tuto chvíli není k dispozici žádná smlouva SLA, jak dlouho trvá replikace dat do cílového účtu.

### <a name="replications-policies"></a>Zásady replikace

Při konfiguraci replikace objektů se na zdrojovém účtu a cílovém účtu vytvoří zásada replikace prostřednictvím poskytovatele prostředků Azure Storage. Zásady replikace se identifikují podle ID zásad. Aby mohla replikace probíhat, musí mít zásady na zdrojovém a cílovém účtu stejné ID zásad.

Účet úložiště může sloužit jako zdrojový účet pro až dva cílové účty. A cílový účet pravděpodobně nemá více než dva zdrojové účty. Zdrojové a cílové účty se můžou nacházet v různých oblastech. Můžete nakonfigurovat samostatné zásady replikace pro replikaci dat do každého cílového účtu.

### <a name="replication-rules"></a>Pravidla replikace

Pravidla replikace určují, jak bude Azure Storage replikovat objekty blob ze zdrojového kontejneru do cílového kontejneru. Pro každou zásadu replikace můžete zadat až 10 pravidel replikace. Každé pravidlo definuje jeden zdrojový a cílový kontejner a každý zdrojový a cílový kontejner se dá použít jenom v jednom pravidle.

Když vytvoříte pravidlo replikace, zkopírují se ve výchozím nastavení jenom nové objekty blob bloku, které jsou následně přidané do zdrojového kontejneru. Můžete také určit, že se zkopírují nové i existující objekty blob bloku, nebo můžete definovat vlastní obor kopírování, který kopíruje objekty blob bloku vytvořené ze zadaného času.

Můžete také zadat jeden nebo více filtrů jako součást pravidla replikace pro filtrování objektů blob bloku podle předpony. Když zadáte předponu, zkopírují se do cílového kontejneru jenom objekty blob odpovídající této předponě ve zdrojovém kontejneru.

Zdrojové a cílové kontejnery musí existovat, aby bylo možné je zadat v pravidle. Po vytvoření zásad replikace bude cílový kontejner jen pro čtení. Jakékoli pokusy o zápis do cílového kontejneru selžou s kódem chyby 409 (Konflikt). Můžete ale zavolat operaci [nastavit vrstvu objektů BLOB](/rest/api/storageservices/set-blob-tier) u objektu BLOB v cílovém kontejneru a přesunout ho do archivní úrovně. Další informace o archivní úrovni najdete v tématu [Azure Blob Storage: horká, studená a archivní úroveň přístupu](storage-blob-storage-tiers.md#archive-access-tier).

## <a name="about-the-preview"></a>O verzi Preview

Replikace objektů se podporuje jenom pro účty úložiště pro obecné účely verze 2. Replikace objektů je k dispozici ve verzi Preview v následujících oblastech:

- Francie – střed
- Kanada – východ
- Střední Kanada
- USA – východ 2
- USA – střed

Aby bylo možné používat replikaci objektů, musí být zdrojový i cílový účet umístěn v jedné z těchto oblastí. Účty můžou být ve dvou různých oblastech.

Během období Preview se replikace dat mezi účty úložiště nevztahují na další náklady.

> [!IMPORTANT]
> Verze Preview replikace objektů je určena pouze pro neprodukční použití. Smlouvy o úrovni produkčních služeb (SLA) nejsou aktuálně k dispozici.

### <a name="prerequisites-for-object-replication"></a>Požadavky na replikaci objektů

Replikace objektů vyžaduje, aby byly povolené následující funkce Azure Storage: 
- [Změna kanálu](storage-blob-change-feed.md)
- [Správa verzí](versioning-overview.md)

Před konfigurací replikace objektů povolte příslušné požadavky. Ve zdrojovém účtu musí být povolený kanál změn a na zdrojovém i cílovém účtu musí být povolená Správa verzí objektů BLOB. Další informace o povolení těchto funkcí najdete v těchto článcích:

- [Povolení a zákaz kanálu změn](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [Povolení a správa verzí objektů BLOB](versioning-enable.md)

Před tím, než je povolíte, si nezapomeňte zaregistrovat verze Preview pro náhled kanálu změn a verzí objektů BLOB.

Povolení změny kanálu a správy verzí objektů BLOB může mít za následek další náklady. Další podrobnosti najdete na [stránce s cenami Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

### <a name="register-for-the-preview"></a>Zaregistrovat se pro verzi Preview

Můžete se zaregistrovat pro náhled replikace objektů pomocí PowerShellu nebo rozhraní příkazového řádku Azure CLI. Pokud jste to ještě neudělali, zaregistrujte si také verze Preview pro náhled kanálu změn a verzí objektů BLOB.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pokud se chcete zaregistrovat ve verzi Preview pomocí PowerShellu, spusťte následující příkazy:

```powershell
# Register for the object replication preview
Register-AzProviderFeature -FeatureName AllowObjectReplication -ProviderNamespace Microsoft.Storage

# Register for change feed (preview)
Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage

# Register for blob versioning (preview)
Register-AzProviderFeature -FeatureName Versioning -ProviderNamespace Microsoft.Storage

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud se chcete zaregistrovat ve verzi Preview pomocí Azure CLI, spusťte následující příkazy:

```azurecli
az feature register --namespace Microsoft.Storage --name AllowObjectReplication
az feature register --namespace Microsoft.Storage --name Changefeed
az feature register --namespace Microsoft.Storage --name Versioning
az provider register --namespace 'Microsoft.Storage'
```

---

### <a name="check-registration-status"></a>Ověřit stav registrace

Stav žádostí o registraci můžete zjistit pomocí PowerShellu nebo rozhraní příkazového řádku Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Pokud chcete zjistit stav žádostí o registraci pomocí PowerShellu, spusťte následující příkazy:

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowObjectReplication

Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Changefeed

Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Versioning
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete zjistit stav žádostí o registraci pomocí Azure CLI, spusťte následující příkazy:

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/AllowObjectReplication')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/Changefeed')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/Versioning')].{Name:name,State:properties.state}"
```

---

## <a name="ask-questions-or-provide-feedback"></a>Položte otázky nebo poskytněte zpětnou vazbu.

Chcete-li klást otázky týkající se náhledu replikace objektů nebo poskytnutí zpětné vazby, obraťte se na společnost Microsoft na adrese AzureStorageFeedback@microsoft.com . Nápady a návrhy týkající se Azure Storage jsou vždy vítá vás [fórum Azure Storage Feedback](https://feedback.azure.com/forums/217298-storage).

## <a name="next-steps"></a>Další kroky

- [Konfigurace replikace objektů (Preview)](object-replication-configure.md)
- [Změna podpory kanálu v Azure Blob Storage (Preview)](storage-blob-change-feed.md)
- [Povolení a správa verzí objektů BLOB](versioning-enable.md)
