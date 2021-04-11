---
title: Microsoft Azure Media Services běžných scénářích | Microsoft Docs
description: Tento článek obsahuje přehled scénářů Microsoft Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: add2a4aba1f7664b919e88992b2a1d71e8291b53
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226765"
---
# <a name="microsoft-azure-media-services-common-scenarios"></a>Microsoft Azure Media Services běžných scénářů

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. Podívejte se na nejnovější verzi [Media Services V3](../latest/media-services-overview.md). Podívejte se taky na [pokyny k migraci z v2 na V3](../latest/migrate-v-2-v-3-migration-introduction.md) .

Microsoft Azure Media Services (AMS) umožňuje bezpečně nahrávat, ukládat, kódovat a balit obsah (video nebo zvuk) doručovaný na vyžádání i živě streamovaný různým klientům (například do televizí, počítačů a mobilních zařízení).

Tento článek popisuje běžné scénáře doručování obsahu živě nebo na vyžádání.

## <a name="overview"></a>Přehled

### <a name="prerequisites"></a>Požadavky

* Účet Azure: Pokud účet nemáte, můžete si během několika minut vytvořit bezplatný zkušební účet. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com).
* Účet Azure Media Services. Další informace najdete v článku o [vytvoření účtu](media-services-portal-create-account.md).
* Koncový bod streamování, ze kterého chcete streamovat obsah, musí být ve stavu **Spuštěno**.

    Po vytvoření účtu AMS se do vašeho účtu přidá **výchozí** koncový bod streamování ve stavu **Zastaveno** . Pokud chcete spustit streamování vašeho obsahu a využít výhod dynamického balení a dynamického šifrování, musí koncový bod streamování být ve stavu **Spuštěno**.

### <a name="commonly-used-objects-when-developing-against-the-ams-odata-model"></a>Běžně používané objekty při vývoji na základě modelu AMS OData

Následující obrázek ukazuje některé z nejčastěji používaných objektů při vývoji na základě modelu Media Services OData.

Kliknutím na obrázek zobrazíte jeho plnou velikost.  

[![Diagram znázorňující některé z nejčastěji používaných objektů při vývoji s datovým modelem Azure Media Services objektů.](./media/media-services-overview/media-services-overview-object-model-small.png)](./media/media-services-overview/media-services-overview-object-model.png#lightbox)

Celý model můžete zobrazit [zde](https://m.eet.com/media/1170326/ms-part1.pdf).  

## <a name="protect-content-in-storage-and-deliver-streaming-media-in-the-clear-non-encrypted"></a>Ochrana obsahu v úložišti a doručování streamovaných médií v nešifrované podobě

![Pracovní postup videa na vyžádání (VoD)](./media/scenarios-and-availability/scenarios-and-availability01.png)

1. Nahrajte do prostředku vysoce kvalitní mediální soubor.

    Použití možnosti šifrování úložiště u vašeho majetku za účelem ochrany vašeho obsahu během nahrávání a v klidovém úložišti se doporučuje.

1. Zakódujte ho do sady souborů MP4 s adaptivní přenosovou rychlostí.

    Použití možnosti šifrování úložiště u výstupního prostředku za účelem ochrany obsahu v klidovém prostředí se doporučuje.

1. Nakonfigurujte zásady doručení assetu (používané dynamickým balením).

    Pokud váš asset používá šifrování úložiště, **musíte** nakonfigurovat zásady doručení assetu.
1. Publikujte asset tím, že vytvoříte lokátor OnDemand.
1. Streamujte publikovaný obsah.

## <a name="protect-content-in-storage-deliver-dynamically-encrypted-streaming-media"></a>Ochrana obsahu v úložišti, doručování dynamicky šifrovaných streamovaných médií

![Ochrana technologií PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

1. Nahrajte do prostředku vysoce kvalitní mediální soubor. Na asset použijte možnost šifrování úložiště.
1. Zakódujte ho do sady souborů MP4 s adaptivní přenosovou rychlostí. Na výstupní asset použijte možnost šifrování úložiště.
1. Pokud chcete asset během přehrávání dynamicky šifrovat, vytvořte šifrovací klíč obsahu.
1. Nakonfigurujte zásady autorizace klíče obsahu.
1. Nakonfigurujte zásady doručení assetu (používané dynamickým balením a dynamickým šifrováním).
1. Publikujte asset tím, že vytvoříte lokátor OnDemand.
1. Streamujte publikovaný obsah.

## <a name="deliver-progressive-download"></a>Doručení progresivního stahování

1. Nahrajte do prostředku vysoce kvalitní mediální soubor.
1. Zakódujte ho do jednoho souboru MP4.
1. Publikujte asset tím, že vytvoříte lokátor OnDemand nebo SAS. Pokud používáte lokátor SAS, bude se obsah stahovat z úložiště objektů blob v Azure. Nemusíte mít koncové body streamování ve stavu spuštěno.
1. Progresivně stáhněte obsah.

## <a name="delivering-live-streaming-events"></a>Doručování živě streamovaných událostí

1. Ingestujte živý obsah pomocí různých protokolů pro živé streamování (například RTMP nebo technologie Smooth Streaming).
1. (volitelně) Kódujte datový proud na datový proud s adaptivní přenosovou rychlostí.
1. Zobrazte náhled živého streamování.
1. Doručovat obsah prostřednictvím:
    1. Běžné protokoly streamování (například MPEG POMLČKy, hladce, HLS) přímo pro vaše zákazníky,
    1. Do Content Delivery Network (CDN) pro další distribuci, nebo
    1. Záznam a uložení ingestního obsahu pro pozdější streamování (video na vyžádání).

Při živém streamování můžete zvolit jednu z následujících cest:

### <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Práce s kanály, které přijímají živé datové proudy s více přenosovými rychlostmi z místních kodérů (průchozí)

Následující diagram znázorňuje hlavní části platformy AMS, které se podílejí na **průchozím** pracovním postupu.

![Diagram znázorňující hlavní části platformy A M S, které jsou součástí pracovního postupu "předávacího".](./media/scenarios-and-availability/media-services-live-streaming-current.png)

Další informace najdete v článku o [práci s kanály, které přijímají živé streamování s více přenosovými rychlostmi z místních kodérů](media-services-live-streaming-with-onprem-encoders.md).

### <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Práce s kanály, které mají povolené kódování v reálném čase pomocí služby Azure Media Services

Následující diagram znázorňuje hlavní části platformy AMS, které se podílejí na pracovním postupu živého streamování, kde je kanál povolený pro živé kódování pomocí Media Services.

![Živý pracovní postup](./media/scenarios-and-availability/media-services-live-streaming-new.png)

Další informace najdete v článku o [práci s kanály, které mají povolené kódování v reálném čase pomocí služby Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

## <a name="consuming-content"></a>Konzumace obsahu

Služba Azure Media Services nabízí nástroje, které potřebujete k vytvoření dynamických aplikací pro klientské přehrávače pro většinu platforem včetně: zařízení iOS, zařízení Android, Windows, Windows Phone, Xbox a set top boxy.

## <a name="enabling-azure-cdn"></a>Povolení Azure CDN

Služba Media Services podporuje integraci s Azure CDN. Informace o povolení Azure CDN najdete v článku o [správě koncových bodů streamování v účtu Media Services](media-services-portal-manage-streaming-endpoints.md).

## <a name="scaling-a-media-services-account"></a>Škálování účtu Media Services

Zákazníci AMS můžou ve svých účtech AMS škálovat koncové body streamování, zpracování médií a úložiště.

* Zákazníci Media Services si můžou zvolit koncový bod streamování **Standard**, nebo koncový bod streamování **Premium**. Koncový bod streamování **Standard** je vhodný pro většinu úloh streamování. Zahrnuje stejné funkce jako koncové body streamování **Premium** a automaticky škáluje šířku odchozího pásma.

    Koncové body streamování **Premium** jsou vhodné pro pokročilé úlohy a poskytují vyhrazenou a škálovatelnou kapacitu šířky pásma. Zákazníci, kteří mají koncový bod streamování **Premium**, ve výchozím nastavení získají jednu jednotku streamování (SU). Koncový bod streamování je možné škálovat přidáním jednotek streamování. Každá jednotka streamování poskytuje aplikaci další kapacitu šířky pásma. Další informace o škálování koncových bodů streamování **Premium** najdete v tématu [Škálování koncových bodů streamování](media-services-portal-scale-streaming-endpoints.md).

* Účet Media Services je přidružený k typu rezervované jednotky, který určuje rychlost zpracování vašich úloh zpracování médií. Můžete si vybrat mezi následujícími typy rezervovaných jednotek: **S1**, **S2** nebo **S3**. Například stejná úloha kódování bude rychlejší, když použijete typ rezervované jednotky **S2**, než kdybyste použili typ **S1**.

    Kromě určení typu rezervované jednotky můžete určit, že se má účet zřídit pomocí **rezervovaných jednotek** (ru). Počet zřízených RU určuje počet úloh médií, které je možné v daném účtu zpracovávat současně.

    > [!NOTE]
    > RU fungují pro paralelní provádění veškerého zpracování médií, včetně úloh indexování pomocí Azure Media Indexeru. Ale na rozdíl od kódování se úlohy indexování s rychlejšími rezervovanými jednotkami nezpracovávají rychleji.

    Další informace najdete v tématu [škálování zpracování multimédií](media-services-portal-scale-media-processing.md).

* Svůj účet Media Services můžete škálovat také tím, že k němu přidáte účty úložiště. Každý účet úložiště je omezen na 500 TB. Další informace najdete v tématu [Správa účtů úložiště](./media-services-managing-multiple-storage-accounts.md).

## <a name="next-steps"></a>Další kroky

[Migrace na Media Services v3](../latest/media-services-overview.md)

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
