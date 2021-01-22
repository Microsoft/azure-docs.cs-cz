---
title: Pokyny k migraci na základě scénáře kódování | Microsoft Docs
description: Tento článek vám poskytne kódování na základě scénářů, které vám pomůže při migraci z Azure Media Services V2 na v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 2d122bdeb98de624d9053852b9bee4595b0ef8c8
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98690404"
---
# <a name="encoding-scenario-based-migration-guidance"></a>Pokyny k migraci na základě scénáře kódování

![logo Průvodce migrací](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![kroky migrace 2](./media/migration-guide/steps-4.svg)

Tento článek vám poskytne kódování na základě scénářů, které vám pomůže při migraci z Azure Media Services V2 na v3.

## <a name="prerequisites"></a>Požadavky

Než se pustíte do změny svého pracovního postupu kódování, doporučujeme pochopit rozdíly v způsobu správy úložiště.  V AMS V3 se Azure Storage rozhraní API používá ke správě účtů úložiště přidružených k vašemu účtu Media Services.

> [!NOTE]
> Úlohy a úlohy vytvořené v v2 se v v3 nezobrazují, protože nejsou přidružené k transformaci. Doporučení je přepnuto na transformace v3 a úlohy.

## <a name="encoding-workflow-comparison"></a>Porovnání pracovního postupu kódování

Počkejte několik minut, než se podíváte na vývojové diagramy níže, abyste pomohli vizuální porovnání pracovních postupů kódování pro v2 a v3.

### <a name="v2-encoding-workflow"></a>Verze V2 – pracovní postup kódování

Chcete-li zobrazit větší verzi, klikněte na obrázek níže.

[![Pracovní postup kódování pro v2 ](./media/migration-guide/V2-pretty.svg)](./media/migration-guide/V2-pretty.svg#lightbox)

1. Nastavení
    1. Vytvořte Asset nebo použijte existující Asset. Pokud používáte nový Asset, nahrajte do tohoto prostředku obsah. Pokud používáte existující Asset, měli byste použít kódování souborů, které již existují v assetu.
    2. Získejte hodnoty následujících položek:
        - ID nebo objekt procesoru médií
        - Řetězec kodéru (název) kodéru, který chcete použít
        - ID assetu nového prostředku nebo ID prostředku stávajícího prostředku
    3. Pro monitorování Vytvořte odběr oznámení na úrovni úlohy nebo úlohy nebo obslužnou rutinu události sady SDK.
2. Vytvořte úlohu, která obsahuje úlohu nebo úkoly. Každý úkol by měl zahrnovat výše uvedené položky a:
    - Direktiva, že je třeba vytvořit výstupní prostředek.  Výstupní Asset vytváří systém.
    - Volitelný název výstupního prostředku
3. Odešlete úlohu.
4. Sledujte úlohu.

### <a name="v3-encoding-workflow"></a>Verze v3 – pracovní postup kódování

[![Pracovní postup kódování pro v3 ](./media/migration-guide/V3-pretty.svg)](./media/migration-guide/V3-pretty.svg#lightbox)

1. Nastavení
    1. Vytvořte Asset nebo použijte existující Asset. Pokud používáte nový Asset, nahrajte do tohoto prostředku obsah. Pokud používáte existující Asset, měli byste použít kódování souborů, které již existují v assetu. *Do tohoto prostředku byste neměli nahrávat další obsah.*
    1. Vytvořte výstupní Asset.  Výstupní Asset je místo, kde se budou ukládat kódované soubory a vstupní a výstupní metadata.
    1. Získat hodnoty pro transformaci:
        - Přednastavení kodéru Standard
        - Skupina prostředků AMS
        - Název účtu AMS
    1. Vytvořte transformaci nebo použijte existující transformaci.  Transformace jsou opakovaně použitelné. Není nutné vytvářet novou transformaci pokaždé, když chcete odeslat úlohu.
1. Vytvoření úlohy
    1. Pro úlohu Získejte hodnoty pro následující položky:
        - Název transformace
        - Základní identifikátor URI pro adresu URL SAS pro váš Asset, zdrojovou cestu HTTPs vaší sdílené složky nebo místní cestu souborů. `JobInputAsset`Jako vstup lze použít také název assetu.
        - Názvy souborů
        - Výstup assetů
        - Skupina prostředků
        - Název účtu AMS  
1. Pomocí [Event Grid](monitor-events-portal-how-to.md) můžete monitorovat svoji úlohu.
1. Odešlete úlohu.

## <a name="custom-presets-from-v2-to-v3-encoding"></a>Vlastní přednastavení z kódování v2 až V3

Pokud váš kód v2 volal standardní kodér s vlastní předvolbumi, před odesláním úlohy nejdřív musíte vytvořit novou transformaci s vlastním přednastaveným standardem kodéru.

Vlastní přednastavení jsou nyní JSON a již nejsou založená na formátu XML. Znovu vytvořte přednastavení ve formátu JSON podle vlastního přednastaveného schématu, jak je definované v dokumentaci k [transformaci Open API (Swagger)](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/examples/transforms-create.json) .


<!-- removed because this is covered in the tutorials
Common custom [encoding](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json) scenarios:
        1. Create a custom Single Bitrate MP4 encode
        1. Create a custom [Adaptive Bitrate Encoding Ladder](autogen-bitrate-ladder.md)
        1. Creating Sprite Thumbnails
        1. Creating Thumbnails (see below for your preferred method)
        1. [Sub Clipping](subclip-video-rest-howto.md)
        1. Cropping
-->

## <a name="input-and-output-metadata-files-from-an-encoding-job"></a>Vstupní a výstupní soubory metadat z úlohy kódování

Ve verzích v2 se vstupní a výstupní soubory metadat XML generují jako výsledek úlohy kódování. V v3 se formát metadat změnil z XML na JSON. Další informace o metadatech najdete v tématu [vstupní metadata](input-metadata-schema.md) a [výstupní metadata](output-metadata-schema.md).

## <a name="premium-encoder-to-v3-standard-encoder-or-partner-based-solutions"></a>Kodér úrovně Premium na standardní kodér nebo řešení založená na partnerech

Rozhraní API v2 už nepodporuje kodér Premium. Pokud jste dříve používali kódování na základě pracovního postupu Premium pro kódování HEVC, měli byste migrovat na nový [kodér Standard](media-encoder-standard-formats.md) v3 s podporou kódování HEVC.

Pokud budete potřebovat pokročilé funkce pracovního postupu kodéru Premium, doporučujeme začít používat řešení Azure Advanced Encoding partnering, od [představované komunikace, telekomunikačních](https://imaginecommunications.com)nebo [Bitmovin](https://bitmovin.com). [](https://www.telestream.net)

## <a name="jobs-with-inputs-that-are-on-https-hosted-urls"></a>Úlohy se vstupy v adresách URL hostovaných HTTPS

Úlohy v systému V3 teď můžete odeslat ze souborů uložených v úložišti Azure, místně uložených nebo externích webových serverech, a to pomocí [podpory vstupu úlohy http (S)](job-input-from-http-how-to.md).

Pokud jste dříve použili pracovní postupy ke kopírování souborů ze souborů objektů blob Azure do prázdných assetů před odesláním úloh, možná budete moct zjednodušit svůj pracovní postup předáním adresy URL SAS pro soubor v úložišti objektů BLOB v Azure přímo do úlohy.

## <a name="indexer-v1-audio-transcription-to-the-new-audioanalyzer-basic-mode"></a>Indexer v1 audio přepisu pro nový AudioAnalyzer "základní režim"

Pro zákazníky, kteří používají procesor indexeru v1 v rozhraní v2 API, je potřeba vytvořit transformaci, která vyvolá nové `AudioAnalyzer` v [základním režimu](how-to-create-basic-audio-transform.md) před odesláním úlohy.

## <a name="encoding-transforms-and-jobs-concepts-tutorials-and-how-to-guides"></a>Informace o kódování, transformacích a úlohách, kurzech a návody

### <a name="concepts"></a>Koncepty

- [Kódování videa a zvuku pomocí Media Services](encoding-concept.md)
- [Formáty a kodeky Standard Encoder](media-encoder-standard-formats.md)
- [Zakódovat pomocí automaticky vygenerovaného žebříka přenosové rychlosti](autogen-bitrate-ladder.md)
- [K vyhledání optimální přenosové hodnoty pro dané řešení použijte předvolbu kódování zohledňující obsah.](content-aware-encoding.md)
- [Rezervované jednotky médií](concept-media-reserved-units.md)
- [Vstupní metadata](input-metadata-schema.md)
- [Výstupní metadata](output-metadata-schema.md)
- [Dynamické balení v Media Services V3: zvukové kodeky](dynamic-packaging-overview.md#audio-codecs-supported-by-dynamic-packaging)

### <a name="tutorials"></a>Kurzy

- [Kurz: kódování vzdáleného souboru na základě adresy URL a streamu pro video – .NET](stream-files-dotnet-quickstart.md)
- [Kurz: nahrávání, kódování a streamování videí pomocí Media Services V3](stream-files-tutorial-with-api.md)

### <a name="how-to-guides"></a>Průvodce postupy

- [Vytvoření vstupu úlohy z adresy URL HTTPS](job-input-from-http-how-to.md)
- [Vytvoření vstupu úlohy z místního souboru](job-input-from-local-file-how-to.md)
- [Vytvoří základní transformaci zvuku.](how-to-create-basic-audio-transform.md)
- S využitím .NET
  - [Jak kódovat pomocí vlastní transformace – .NET](customize-encoder-presets-how-to.md)
  - [Jak vytvořit překryv pomocí Media Encoder Standard](how-to-create-overlay.md)
  - [Jak generovat miniatury pomocí kodéru Standard s .NET](media-services-generate-thumbnails-dotnet.md)
- S využitím rozhraní příkazového řádku Azure
  - [Jak kódovat pomocí vlastní transformace – Azure CLI](custom-preset-cli-howto.md)
- S využitím REST
  - [Jak kódovat pomocí vlastní transformace – REST](custom-preset-rest-howto.md)
  - [Postup generování miniatur pomocí kodéru Standard s REST](media-services-generate-thumbnails-rest.md)
- [Rozříznout video při kódování pomocí Media Services-.NET](subclip-video-dotnet-howto.md)
- [Rozstřihnout video při kódování pomocí Media Services-REST](subclip-video-rest-howto.md)

## <a name="samples"></a>ukázky

[V ukázkách kódu můžete také porovnat kód v2 a V3](migrate-v-2-v-3-migration-samples.md).

## <a name="next-steps"></a>Další kroky

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
