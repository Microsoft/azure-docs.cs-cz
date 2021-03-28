---
title: Pokyny k migraci obsahu Protection
description: Tento článek poskytuje pokyny na základě scénáře ochrany obsahu, které vám pomůžou při migraci z Azure Media Services V2 na v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 03/26/2021
ms.author: inhenkel
ms.openlocfilehash: 7ef41b76f343d8997feebc4a366deda7ce6a2afa
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644057"
---
# <a name="content-protection-scenario-based-migration-guidance"></a>Pokyny k migraci na základě scénáře ochrany obsahu

![logo Průvodce migrací](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![kroky migrace 2](./media/migration-guide/steps-4.svg)

Tento článek poskytuje podrobné informace a pokyny k migraci případů použití ochrany obsahu z rozhraní v2 API na nové rozhraní API Azure Media Services V3.

## <a name="protect-content-in-v3-api"></a>Ochrana obsahu v rozhraní V3 API

V novém rozhraní API V3 použijte podporu pro [multi-klíčové](design-multi-drm-system-with-access-control.md) funkce.

Konkrétní postup najdete v tématu věnovaném konceptům, kurzům a návodům k ochraně obsahu.

## <a name="visibility-of-v2-assets-streaminglocators-and-properties-in-the-v3-api-for-content-protection-scenarios"></a>Viditelnost prostředků v2, StreamingLocators a vlastností v rozhraní V3 API pro scénáře ochrany obsahu

Při migraci na rozhraní V3 API zjistíte, že potřebujete mít přístup k některým vlastnostem nebo klíčům obsahu z prostředků v2. Jedním z klíčových rozdílů je, že rozhraní v2 API použije **AssetId** jako primární identifikační klíč a nové rozhraní API V3 používá název entity správy prostředků Azure jako primární identifikátor.  Vlastnost v2 **Asset.Name** se obvykle nepoužívá jako jedinečný identifikátor, takže při migraci na verzi 3 zjistíte, že se názvy assetů v2 teď zobrazí v poli **Asset. Description** .

Pokud jste například dříve provedli prostředek v2 s ID **"NB: CID: UUID: 8cb39104-122c-496e-9ac5-7f9e2c2547b8"**, zjistíte, že při výpisu starých prostředků v2 prostřednictvím rozhraní API V3 bude název nyní částí identifikátoru GUID na konci (v tomto případě **"8cb39104-122c-496e-9ac5-7f9e2c2547b8"**.)

Můžete zadat dotaz na **StreamingLocators** přidružené k assetům vytvořeným v rozhraní v2 API pomocí nové metody V3 [ListStreamingLocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators) v entitě Asset.  Také odkaz na verzi sady .NET Client SDK [ListStreamingLocatorsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocatorsasync?view=azure-dotnet)

Výsledky metody **ListStreamingLocators** vám poskytnou **název** a **StreamingLocatorId** lokátoru spolu s **StreamingPolicyName**.

Chcete-li najít **ContentKeys** , který se používá ve vaší **StreamingLocators** pro ochranu obsahu, můžete zavolat metodu [StreamingLocator. ListContentKeysAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.streaminglocatorsoperationsextensions.listcontentkeysasync?view=azure-dotnet) .  

Všechny **prostředky** , které byly vytvořeny a publikovány pomocí rozhraní v2 API, budou mít v [zásadách streamování](https://docs.microsoft.com/azure/media-services/latest/streaming-policy-concept)k dispozici [zásady pro klíč obsahu](https://docs.microsoft.com/azure/media-services/latest/content-key-policy-concept) i klíč obsahu definované v rozhraních API v3.

Další informace o ochraně obsahu v rozhraní V3 API najdete v článku [Ochrana obsahu pomocí Media Services dynamické šifrování.](https://docs.microsoft.com/azure/media-services/latest/content-protection-overview)

## <a name="how-to-list-your-v2-assets-and-content-protection-settings-using-the-v3-api"></a>Jak zobrazit seznam prostředků v2 a nastavení ochrany obsahu pomocí rozhraní V3 API

V rozhraní v2 API byste běžně používali **assety**, **StreamingLocators** a **ContentKeys** k ochraně obsahu streamování.
Při migraci do rozhraní API V3 jsou vaše Assety rozhraní API v2, StreamingLocators a ContentKeys automaticky vystavené v rozhraní V3 API a všechna data, která jsou v nich dostupná, jsou k dispozici pro přístup.

## <a name="can-i-update-v2-properties-using-the-v3-api"></a>Můžu aktualizovat vlastnosti v2 pomocí rozhraní V3 API?

Ne, nemůžete aktualizovat žádné vlastnosti u entit v2 prostřednictvím rozhraní V3 API, které bylo vytvořeno pomocí StreamingLocators, StreamingPolicies, zásad pro klíče obsahu a klíčů obsahu v v2.
Pokud potřebujete aktualizovat, změnit nebo změnit obsah uložený v entitách v2, budete ho muset aktualizovat prostřednictvím rozhraní v2 API nebo vytvořit nové entity rozhraní V3 API, abyste je mohli migrovat do popředí.

## <a name="how-do-i-change-the-contentkeypolicy-used-for-a-v2-asset-that-is-published-and-keep-the-same-content-key"></a>Návody změnit ContentKeyPolicy používané pro prostředek v2, který je publikovaný a zachovat stejný klíč obsahu?

V této situaci byste nejdřív měli zrušit publikování (odebrání všech lokátorů streamování) na Assetu pomocí sady v2 SDK (odstraňte lokátor, odpojte zásady autorizace klíčů obsahu, odpojíte zásady doručení assetů, odpojíte klíč obsahu, odstraníte klíč obsahu) a pak vytvoříte novou **[StreamingLocator](https://docs.microsoft.com/azure/media-services/latest/streaming-locators-concept)** ve formátu V3 pomocí direktivy V3 [StreamingPolicy](https://docs.microsoft.com/azure/media-services/latest/streaming-policy-concept) a [ContentKeyPolicy](https://docs.microsoft.com/azure/media-services/latest/content-key-policy-concept).

Při vytváření **[StreamingLocator](https://docs.microsoft.com/azure/media-services/latest/streaming-locators-concept)** je nutné zadat konkrétní identifikátor klíče obsahu a hodnotu klíče.

Všimněte si, že je možné odstranit Lokátor v2 pomocí rozhraní V3 API, ale neodebere se klíč obsahu ani zásady klíče obsahu použité v případě, že byly vytvořeny v rozhraní v2 API.  

## <a name="using-amse-v2-and-amse-v3-side-by-side"></a>Použití AMSE v2 a AMSE V3 vedle sebe

Při migraci obsahu z verze V2 na V3 doporučujeme nainstalovat [Nástroj v2 Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0) společně s [nástrojem V3 Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) , abyste pomohli porovnat data, která se zobrazují vedle sebe, pro Asset vytvořený a publikovaný přes rozhraní API v2. Vlastnosti by měly být viditelné, ale v poněkud různých umístěních.  


## <a name="content-protection-concepts-tutorials-and-how-to-guides"></a>Koncepty, kurzy a návody k ochraně obsahu

### <a name="concepts"></a>Koncepty

- [Ochrana obsahu pomocí Media Services dynamického šifrování](content-protection-overview.md)
- [Návrh systému ochrany obsahu s více variantami DRM s využitím řízení přístupu](design-multi-drm-system-with-access-control.md)
- [Media Services V3 se šablonou licence PlayReady](playready-license-template-overview.md)
- [Přehled šablon licencí Media Services V3 with Widevine](widevine-license-template-overview.md)
- [Konfigurace a licenční požadavky pro Apple FairPlay](fairplay-license-overview.md)
- [Zásady streamování](streaming-policy-concept.md)
- [Zásady pro klíč obsahu](content-key-policy-concept.md)

### <a name="tutorials"></a>Kurzy

[Rychlý Start: použití portálu k šifrování obsahu](encrypt-content-quickstart.md)

### <a name="how-to-guides"></a>Průvodce postupy

- [Získání podpisového klíče ze stávajících zásad](get-content-key-policy-dotnet-howto.md)
- [Online streamování FairPlay pro iOS s Media Services V3](offline-fairplay-for-ios.md)
- [Online streamování Widevine pro Android s Media Services V3](offline-widevine-for-android.md)
- [Online streamování PlayReady pro Windows 10 s Media Services V3](offline-plaready-streaming-for-windows-10.md)

## <a name="samples"></a>ukázky

[V ukázkách kódu můžete také porovnat kód v2 a V3](migrate-v-2-v-3-migration-samples.md).

## <a name="tools"></a>nástroje

- [Nástroj V3 Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer)
- [V2 Azure Media Services Explorer – nástroj](https://github.com/Azure/Azure-Media-Services-Explorer/releases/tag/v4.3.15.0)
