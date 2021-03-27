---
title: Redigování obličeje v Azure Media Services V3 API | Microsoft Docs
description: Azure Media Services V3 poskytuje možnost detekce a redigování pro rozpoznávání tváře, která umožňuje odeslat videosoubor, detekovat plošky a aplikovat rozostření na ně v jednom kombinovaném průchodu nebo pomocí dvou fází zpracování, které umožňují úpravy. Tento článek ukazuje, jak pomocí přednastavení rozpoznávání tváře v rozhraní V3 API naredigování obličeje.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/25/2021
ms.author: johndeu
ms.custom: devx-track-csharp
ms.openlocfilehash: 6db93aa369366936c90446c41406eafe9ee6e414
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630362"
---
# <a name="redact-faces-with-the-face-detector-preset"></a>Redigování obličeje s přednastaveným obličejovým detektorem

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Rozhraní Azure Media Services V3 API obsahuje přednastavený detektor pro rozpoznávání tváře, který nabízí škálovatelnou detekci obličeje a redigování (rozostření) v cloudu. Naredigování obličeje umožňuje upravit vaše video, aby se rozostří plošky vybraných jednotlivců. Je možné, že budete chtít použít službu redigování obličeje ve scénářích veřejného zabezpečení a média pro příspěvky. Několik minut záběrů, které obsahují více plošek, může trvat hodiny na redigování ručně, ale s tímto předplatným proces redigování obličeje bude vyžadovat pouze několik jednoduchých kroků.

Tento článek obsahuje podrobné informace o **přednastaveném rozpoznávání obličeje** a ukazuje, jak ho použít s Azure Media Services SDK pro .NET.

## <a name="compliance-privacy-and-security"></a>Dodržování předpisů, ochrana osobních údajů a zabezpečení
 
Jako důležité připomenutí musíte dodržovat všechny použitelné zákony při používání analýz v Azure Media Services. Nemusíte používat Azure Media Services ani žádnou jinou službu Azure způsobem, který porušuje práva ostatních. Před nahráním videí, včetně jakýchkoli biometrických dat, do služby Azure Media Services pro zpracování a ukládání, musíte mít všechna patřičná práva, včetně všech příslušných souhlasů, od jednotlivců ve videu. Pokud se chcete dozvědět o dodržování předpisů, ochraně osobních údajů a zabezpečení v Azure Media Services, [podmínky Azure Cognitive Services](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/). Zásady ochrany osobních údajů od Microsoftu a jejich zpracování najdete v [prohlášení o zásadách ochrany osobních údajů](https://privacy.microsoft.com/PrivacyStatement)od Microsoftu, na základě [podmínek pro online služby](https://www.microsoft.com/licensing/product-licensing/products) (OST) a v [dodatku pro zpracování dat](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) (DPA). Další informace o ochraně osobních údajů, včetně uchovávání, odstranění nebo zničení dat, jsou k dispozici v OST a [zde](../video-indexer/faq.md). Pomocí Azure Media Services souhlasíte s tím, že jste vázáni Cognitive Services podmínky, OST, DPA a prohlášení o zásadách ochrany osobních údajů.


## <a name="face-redaction-modes"></a>Režimy redigování obličeje

Redigování obličeje funguje tak, že detekuje obličeje v každém snímku videa a sleduje objekt Face v čase dopředu a dozadu, aby se stejná osoba mohla rozmazaný i z jiných úhlů. Automatizovaný proces redigování je složitý a neprovádí vždy rozostření každého obličeje 100% garantuje. Z tohoto důvodu může být předvolba použita pro oboustranný režim ke zlepšení kvality a přesnosti rozostření přes fázi úprav před odesláním souboru pro poslední úspěšnost rozostření. 

Kromě plně automatického **kombinovaného** režimu vám pracovní postup pro oboustranný režim umožňuje zvolit plošky, které chcete Rozostřit (nebo ne Rozostřit) prostřednictvím seznamu ID obličeje. Chcete-li provést úpravy v libovolném snímku, použije přednastavení ve formátu JSON soubor metadat jako vstup druhého průchodu. Tento pracovní postup je rozdělen na režimy **analyzování** a **redigování** .

Můžete také jednoduše kombinovat dva režimy v jednom průchodu, který spouští obě úlohy v jedné úloze. Tento režim se nazývá **Kombinovaná**.  V tomto článku ukázkový kód ukazuje, jak použít zjednodušený **kombinovaný** režim single Pass pro ukázkový zdrojový soubor.

### <a name="combined-mode"></a>Kombinovaný režim

Tím se v jednom průchodu vytvoří videosoubor v souboru MP4, aniž by bylo potřeba provádět ruční úpravy souboru JSON. Výstupem ve složce assets pro úlohu bude jeden soubor. mp4, který obsahuje rozmazaných ploch pomocí vybraného efektu rozostření. Použijte vlastnost rozlišení nastavenou na **SourceResolution** , abyste dosáhli nejlepších výsledků pro redigování.

| Fáze | Název souboru | Poznámky |
| --- | --- | --- |
| Vstupní Asset |"ignite-sample.mp4" |Video ve formátu WMV, MOV nebo MP4 |
| Předvolba konfigurace |Konfigurace faceho detektoru | **režim**: FaceRedactorMode. kombined,  **blurType**: blurType. med, **Resolution**: AnalysisResolution. SourceResolution |
| Výstupní Asset |"ignite-redacted.mp4 |Video s rozmazaným efektem použitým pro obličeje |

### <a name="analyze-mode"></a>Režim analýzy

Úspěšnost **analýzy** obousměrného pracovního postupu provede vstup videa a vytvoří soubor JSON se seznamem umístění obličeje, obrázku ID obličeje a obrázků jpg každé zjištěné plochy. 

| Fáze | Název souboru | Poznámky |
| --- | --- | --- |
| Vstupní Asset |"ignite-sample.mp4" |Video ve formátu WMV, MPV nebo MP4 |
| Předvolba konfigurace |Konfigurace faceho detektoru |**režim**: FaceRedactorMode. analyze, **Resolution**: AnalysisResolution. SourceResolution|
| Výstupní Asset |ignite-sample_annotations.jsna |Data poznámek pro umístění obličeje ve formátu JSON. Tuto možnost může upravit uživatel, aby bylo možné upravovat rozmazaný ohraničovací rámečky. Viz ukázka níže. |
| Výstupní Asset |foo_thumb% 06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg] |Oříznutý jpg každé zjištěné plochy, kde číslo označuje labelId obličej |

#### <a name="output-example"></a>Příklad výstupu

```json
{
  "version": 1,
  "timescale": 24000,
  "offset": 0,
  "framerate": 23.976,
  "width": 1280,
  "height": 720,
  "fragments": [
    {
      "start": 0,
      "duration": 48048,
      "interval": 1001,
      "events": [
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [
          {
            "index": 13,
            "id": 1138,
            "x": 0.29537,
            "y": -0.18987,
            "width": 0.36239,
            "height": 0.80335
          },
          {
            "index": 13,
            "id": 2028,
            "x": 0.60427,
            "y": 0.16098,
            "width": 0.26958,
            "height": 0.57943
          }
        ],

    ... truncated
```

### <a name="redact-blur-mode"></a>Režim redigování (rozostření)

Druhý průchod pracovního postupu přebírá větší počet vstupů, které musí být sloučeny do jediného assetu.

To zahrnuje seznam ID, která se mají Rozostřit, původní video a poznámky JSON. Tento režim používá poznámky pro použití rozostření na vstupním videu.

Výstup z průchodu Analyze nezahrnuje původní video. Video se musí nahrát do vstupního assetu pro úlohu režimu redigování a vybrané jako primární soubor.

| Fáze | Název souboru | Poznámky |
| --- | --- | --- |
| Vstupní Asset |"ignite-sample.mp4" |Video ve formátu WMV, MPV nebo MP4. Stejné video jako v kroku 1. |
| Vstupní Asset |"ignite-sample_annotations.json" |poznámky soubor metadat ze fáze One s volitelnými úpravami, pokud chcete změnit rozostřené plošky. To je nutné upravit v externí aplikaci, kódu nebo textovém editoru. |
| Vstupní Asset | "ignite-sample_IDList.txt" (volitelné) | Nepovinný nový řádek oddělený seznam ID tváře k redigování Pokud je ponecháno prázdné, všechny plošky ve zdroji budou aplikovány rozostření. Seznam můžete použít k selektivnímu výběru možnosti Rozostřit konkrétní plošky. |
| Přednastavení obličejového detektoru  |Předvolba konfigurace  | **režim**: FaceRedactorMode. redigován, **blurType**: blurType. med |
| Výstupní Asset |"ignite-sample-redacted.mp4" |Video s rozostřením aplikovaným na základě poznámek |

#### <a name="example-output"></a>Příklad výstupu

Toto je výstup z IDList s jedním vybraným ID.

Příklad foo_IDList.txt

```
1
2
3
```

## <a name="blur-types"></a>Typy rozostření

V **kombinovaném** režimu nebo v režimu **redigování** existuje pět různých režimů rozostření, ze kterých můžete vybírat prostřednictvím vstupní konfigurace JSON: **Nízká**, **med**, **High**, **box** a **Black**. Ve výchozím nastavení se používá **med** .

Můžete najít ukázky níže uvedených typů rozostření.

### <a name="example-settings-for-face-detector-preset"></a>Příklad nastavení pro přednastavený detektor Face
[!code-csharp[Main](../../../media-services-v3-dotnet/VideoAnalytics/FaceRedactor/Program.cs#FaceDetectorPreset)]


#### <a name="low"></a>Nízká

![Příklad nastavení rozostření s nízkým rozlišením](./media/media-services-face-redaction/blur-1.png)

#### <a name="med"></a>Tahač

![Příklad nastavení rozostření středního rozlišení](./media/media-services-face-redaction/blur-2.png)

#### <a name="high"></a>Vysoká

![Příklad nastavení rozostření s vysokým rozlišením](./media/media-services-face-redaction/blur-3.png)

#### <a name="box"></a>Box

![Režim box pro použití při ladění výstupu.](./media/media-services-face-redaction/blur-4.png)

#### <a name="black"></a>Black

![Režim černého pole pokrývá všechny plošky s černými poli.](./media/media-services-face-redaction/blur-5.png)

## <a name="elements-of-the-output-json-file"></a>Prvky výstupního souboru JSON

MP pro redigování poskytuje detekci a sledování polohy s vysokou přesností, které dokáže detekovat až 64 lidských plošek ve snímku videa. Přední plošky poskytují nejlepší výsledky, zatímco strany a malé plošky (menší nebo rovny 24x24 pixelů) jsou náročné.

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

## <a name="net-sample-code"></a>Vzorový kód .NET

Následující program ukazuje použití **kombinovaného** režimu redigování s jedním průchodem:

- Vytvořte Asset a nahrajte do něj mediální soubor.
- Nakonfigurujte přednastavený detektor pro rozpoznávání tváře, který používá nastavení Mode a blurType.
- Vytvoří novou transformaci pomocí přednastavení Face snímače.
- Stáhněte si výstupní videosoubor ve formátu redigování.

## <a name="download-and-configure-the-sample"></a>Stažení a konfigurace ukázky

Do svého počítače naklonujte pomocí následujícího příkazu úložiště GitHub obsahující ukázku .NET:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet.git
 ```

Ukázka se nachází ve složce [FaceRedactor](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoAnalytics/FaceRedactor) . Ve staženém projektu otevřete [appsettings.js](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/VideoAnalytics/FaceRedactor/appsettings.json) . Nahraďte hodnoty přihlašovacími údaji, které jste získali při [přístupu k rozhraním API](./access-api-howto.md).

**Volitelně** můžete zkopírovat ukázkový soubor **[. env](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/sample.env)** v kořenovém adresáři úložiště a v něm vyplnit podrobnosti a přejmenovat tento soubor na **. env** (Všimněte si tečky na přední straně!), aby se mohla používat ve všech ukázkových projektech v úložišti.  Tím se eliminuje nutnost mít naplnění appsettings.jssouborů v každé ukázce a zároveň vás chrání před kontrolou libovolného nastavení do vlastních klonovaných úložišť ve službě Git hub.

#### <a name="examples"></a>Příklady

Tento kód ukazuje, jak nastavit **FaceDetectorPreset** pro rozostření **kombinovaného** režimu.

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoAnalytics/FaceRedactor/Program.cs#FaceDetectorPreset)]

Tento ukázka kódu ukazuje, jak je předvolba předána do objektu transformace během vytváření. Po vytvoření transformace může být úloha odeslána přímo na ni.

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoAnalytics/FaceRedactor/Program.cs#FaceDetectorPresetTransform)]

## <a name="next-steps"></a>Další kroky

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

