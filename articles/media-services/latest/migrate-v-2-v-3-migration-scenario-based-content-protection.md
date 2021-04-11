---
title: Pokyny k migraci obsahu Protection
description: Tento článek poskytuje pokyny na základě scénáře ochrany obsahu, které vám pomůžou při migraci z Azure Media Services V2 na v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 04/05/2021
ms.author: inhenkel
ms.openlocfilehash: a481759da3f1e7d67accdca7b4322db53abbcb0c
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490943"
---
# <a name="content-protection-scenario-based-migration-guidance"></a>Pokyny k migraci na základě scénáře ochrany obsahu

![logo Průvodce migrací](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![kroky migrace 2](./media/migration-guide/steps-4.svg)

Tento článek poskytuje podrobné informace a pokyny k migraci případů použití ochrany obsahu z rozhraní v2 API na nové rozhraní API Azure Media Services V3.

## <a name="protect-content-in-v3-api"></a>Ochrana obsahu v rozhraní V3 API

V novém rozhraní API V3 použijte podporu pro [multi-klíčové](architecture-design-multi-drm-system.md) funkce.

Konkrétní postup najdete v tématu koncepty ochrany obsahu, kurzy a návody na konci tohoto článku.

> [!NOTE]
> Zbývající část tohoto článku popisuje, jak můžete migrovat ochranu obsahu V2 na verzi V3 pomocí .NET.  Pokud potřebujete pokyny nebo vzorový kód pro jiný jazyk nebo metodu, vytvořte prosím pro tuto stránku problém GitHubu.

## <a name="v3-visibility-of-v2-assets-streaminglocators-and-properties"></a>V3 viditelnost prostředků, StreamingLocators a vlastností v2

V rozhraní v2 API, `Assets` , `StreamingLocators` a `ContentKeys` byly použity k ochraně obsahu streamování. Při migraci do rozhraní V3 API jsou vaše rozhraní v2 API `Assets` , `StreamingLocators` a `ContentKeys` všechny dostupné automaticky v rozhraní V3 API a veškerá data v nich jsou k dispozici pro přístup.

Nemůžete ale *aktualizovat* žádné vlastnosti entit v2 prostřednictvím rozhraní V3 API, které jste vytvořili v v2.

Pokud potřebujete aktualizovat, změnit nebo změnit obsah uložený v entitách v2, aktualizujte je pomocí rozhraní v2 API nebo vytvořte nové entity rozhraní V3 API, které chcete migrovat.

## <a name="asset-identifier-differences"></a>Rozdíly v identifikátorech prostředků

K migraci budete potřebovat přístup k vlastnostem nebo klíčům obsahu z prostředků v2.  Je důležité si uvědomit, že rozhraní v2 API používá `AssetId` jako primární identifikační klíč, ale nové rozhraní API V3 používá jako primární identifikátor *název entity správy prostředků Azure* .  (Vlastnost v2 `Asset.Name` se nepoužívá jako jedinečný identifikátor.) S rozhraním API V3 se teď název Assetu v2 zobrazí jako `Asset.Description` .

Pokud jste například dříve vytvořili prostředek v2 s ID `nb:cid:UUID:8cb39104-122c-496e-9ac5-7f9e2c2547b8` , identifikátor je nyní na konci identifikátoru GUID `8cb39104-122c-496e-9ac5-7f9e2c2547b8` . Tato hodnota se zobrazí při výpisu prostředků v2 prostřednictvím rozhraní V3 API.

Všechny prostředky, které byly vytvořeny a publikovány pomocí rozhraní v2 API, budou mít `ContentKeyPolicy` `ContentKey` v rozhraní V3 API a místo výchozí zásady klíče obsahu v `StreamingPolicy` .

Další informace najdete v dokumentaci [zásad pro klíč obsahu](https://docs.microsoft.com/azure/media-services/latest/drm-content-key-policy-concept) a v dokumentaci k [zásadám streamování](https://docs.microsoft.com/azure/media-services/latest/stream-streaming-policy-concept) .

## <a name="use-azure-media-services-explorer-amse-v2-and-amse-v3-tools-side-by-side"></a>Použití nástrojů Azure Media Services Explorer (AMSE) v2 a AMSE V3 vedle sebe

Pomocí [nástroje v2 Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0) společně s [nástrojem V3 Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) Porovnejte data pro vytvoření a publikování assetu pomocí rozhraní API v2. Vlastnosti by měly být viditelné, ale v různých umístěních.

## <a name="use-the-net-content-protection-migration-sample"></a>Použijte ukázku migrace obsahu .NET Content Protection

Můžete najít ukázku kódu pro porovnání rozdílů v identifikátorech assetů pomocí [v2tov3MigrationSample](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/v2tov3Migration) v části ContentProtection v ukázkách kódu Media Services.

## <a name="list-the-streaming-locators"></a>Seznam lokátorů streamování

Můžete zadat dotaz na `StreamingLocators` přidružený k assetům vytvořeným v rozhraní v2 API pomocí nové metody V3 [ListStreamingLocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators) v entitě Asset.  Také odkaz na verzi sady .NET Client SDK [ListStreamingLocatorsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocatorsasync?view=azure-dotnet&preserve-view=true)

Výsledky `ListStreamingLocators` metody vám poskytne `Name` a `StreamingLocatorId` z lokátoru spolu s `StreamingPolicyName` .

## <a name="find-the-content-keys"></a>Vyhledání klíčů obsahu

Chcete-li najít `ContentKeys` použití s vaší `StreamingLocators` , můžete zavolat metodu [StreamingLocator. ListContentKeysAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.streaminglocatorsoperationsextensions.listcontentkeysasync?view=azure-dotnet&preserve-view=true) .  

Další informace o ochraně obsahu v rozhraní V3 API najdete v článku [Ochrana obsahu pomocí Media Services dynamické šifrování.](https://docs.microsoft.com/azure/media-services/latest/drm-content-protection-concept)

## <a name="change-the-v2-contentkeypolicy-keeping-the-same-contentkey"></a>Změňte ContentKeyPolicy v2 tak, aby zachovalo stejné ContentKey.

Nejdřív byste měli zrušit publikování (odebrání všech lokátorů streamování) na Assetu prostřednictvím sady v2 SDK. Jak na to:

1. Odstraňte lokátor.
1. Zrušit propojení `ContentKeyAuthorizationPolicy` .
1. Zrušit propojení `AssetDeliveryPolicy` .
1. Zrušit propojení `ContentKey` .
1. Odstraňte `ContentKey` .
1. Vytvořte nový `StreamingLocator` ve verzi V3 pomocí hodnoty V3 `StreamingPolicy` a `ContentKeyPolicy` zadáním konkrétního identifikátoru klíče obsahu a hodnoty klíče.

> [!NOTE]
> Je možné odstranit Lokátor v2 pomocí rozhraní V3 API, ale neodebere klíč obsahu ani zásady klíče obsahu, pokud byly vytvořeny v rozhraní v2 API.

## <a name="content-protection-concepts-tutorials-and-how-to-guides"></a>Koncepty, kurzy a návody k ochraně obsahu

### <a name="concepts"></a>Koncepty

- [Ochrana obsahu pomocí Media Services dynamického šifrování](drm-content-protection-concept.md)
- [Návrh systému ochrany obsahu s více variantami DRM s využitím řízení přístupu](architecture-design-multi-drm-system.md)
- [Media Services V3 se šablonou licence PlayReady](drm-playready-license-template-concept.md)
- [Přehled šablon licencí Media Services V3 with Widevine](drm-widevine-license-template-concept.md)
- [Konfigurace a licenční požadavky pro Apple FairPlay](drm-fairplay-license-overview.md)
- [Zásady streamování](stream-streaming-policy-concept.md)
- [Zásady pro klíč obsahu](drm-content-key-policy-concept.md)

### <a name="tutorials"></a>Kurzy

[Rychlý Start: použití portálu k šifrování obsahu](drm-encrypt-content-how-to.md)

### <a name="how-to-guides"></a>Průvodce postupy

- [Získání podpisového klíče ze stávajících zásad](drm-get-content-key-policy-dotnet-how-to.md)
- [Online streamování FairPlay pro iOS s Media Services V3](drm-offline-fairplay-for-ios-concept.md)
- [Online streamování Widevine pro Android s Media Services V3](drm-offline-widevine-for-android.md)
- [Online streamování PlayReady pro Windows 10 s Media Services V3](drm-offline-playready-streaming-for-windows-10.md)

## <a name="samples"></a>ukázky

- [v2tov3MigrationSample](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/v2tov3Migration)
- [V ukázkách kódu můžete také porovnat kód v2 a V3](migrate-v-2-v-3-migration-samples.md).

## <a name="tools"></a>nástroje

- [Nástroj V3 Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer)
- [V2 Azure Media Services Explorer – nástroj](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0)
