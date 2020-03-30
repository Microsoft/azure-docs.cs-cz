---
title: Přednastavení kódování podle obsahu – Mediální služby Azure
description: Tento článek popisuje kódování podle obsahu ve službě Microsoft Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/24/2020
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: 3ea6c4226a59ba020a477cc5811033ff3dc3c2e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76772111"
---
# <a name="use-the-content-aware-encoding-preset-to-find-the-optimal-bitrate-value-for-a-given-resolution"></a>Pomocí přednastavení kódování podle obsahu vyhledejte optimální hodnotu přenosového toku pro dané rozlišení.

Aby bylo možné připravit obsah pro doručování [adaptivním datovým tokem](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), musí být video kódováno při více přenosových rychlostech (od nejvyšší po nejnižší hodnotu). To zajišťuje ladné zhoršení kvality, protože je snížen atožný, takže je rozlišení videa. Takové vícenásobné kódování přenosové rychlosti využívá takzvaný kódovací žebřík – tabulku rozlišení a přenosových rychlostí, viz [předvolby kódování](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset)Media Services .

Měli byste si být vědomi obsahu, který zpracováváte, a přizpůsobit/naladit kódovací žebřík na složitost jednotlivých videí. Při každém rozlišení je přenosový tok, za kterým není jakékoli zvýšení kvality vnímavé – kodér pracuje s touto optimální hodnotou přenosového toku. Další úrovní optimalizace je výběr rozlišení na základě obsahu – například video prezentace aplikace PowerPoint nemá prospěch z toho, že klesne pod 720p. Pokud jde o další, kodér může být pověřen optimalizací nastavení pro každý snímek v rámci videa. 

Přednastavení [adaptivního streamování](autogen-bitrate-ladder.md) společnosti Microsoft částečně řeší problém variability kvality a rozlišení zdrojových videí. Naši zákazníci mají různou kombinaci obsahu, někteří na 1080p, jiní na 720p a několik na SD a nižší rozlišení. Kromě toho, ne všechny zdrojové obsah je vysoce kvalitní mezaniny z filmových nebo televizních studií. Přednastavení Adaptivní streamování řeší tyto problémy tím, že zajišťuje, že datový tok žebřík nikdy nepřekročí rozlišení nebo průměrný datový tok vstupního mezaninu. Toto přednastavení však nezkoumá vlastnosti zdroje jiné než rozlišení a přenosovou rychlost.

## <a name="the-content-aware-encoding"></a>Kódování podle obsahu 

Přednastavení kódování podle obsahu rozšiřuje mechanismus "adaptivní přenosové rychlosti" začleněním vlastní logiky, která umožňuje kodéru hledat optimální hodnotu datového toku pro dané rozlišení, ale bez nutnosti rozsáhlé výpočetní analýzy. Toto přednastavení vytváří sadu MP4 zarovnaných do gop. Vzhledem k tomu, jakýkoli vstupní obsah, služba provede počáteční zjednodušenou analýzu vstupního obsahu a používá výsledky k určení optimálního počtu vrstev, vhodného datového toku a nastavení rozlišení pro doručení adaptivním streamováním. Toto přednastavení je obzvláště účinné pro videa s nízkou a střední složitostí, kde výstupní soubory budou mít nižší přenosové rychlosti než přednastavení adaptivního streamování, ale v kvalitě, která divákům stále přináší dobrý zážitek. Výstup bude obsahovat soubory MP4 s video a audio prokládáním

Následující ukázkové grafy ukazují porovnání pomocí metrik kvality, jako [jsou PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) a [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion). Zdroj byl vytvořen zřetězením krátkých klipů s vysokou složitostí snímků z filmů a televizních pořadů, které měly zdůraznit kodér. Podle definice toto přednastavení vytváří výsledky, které se liší od obsahu k obsahu - to také znamená, že pro nějaký obsah nemusí dojít k významnému snížení toku nebo zlepšení kvality.

![Křivka zkreslení rychlosti (RD) pomocí PSNR](media/content-aware-encoding/msrv1.png)

**Obrázek 1: Křivka zkreslení rychlosti (RD) pomocí metriky PSNR pro zdroj s vysokou složitostí**

![Křivka zkreslení rychlosti (RD) pomocí VMAF](media/content-aware-encoding/msrv2.png)

**Obrázek 2: Křivka zkreslení rychlosti (RD) pomocí metriky VMAF pro zdroj s vysokou složitostí**

Níže jsou uvedeny výsledky pro jinou kategorii zdrojového obsahu, kde kodér byl schopen určit, že vstup byl nekvalitní (mnoho kompresní artefakty z důvodu nízkého toku). Všimněte si, že s přednastavením obsahu se kodér rozhodl vytvořit pouze jednu výstupní vrstvu - při dostatečně nízkém datovém toku, aby většina klientů mohla přehrávat datový proud bez zastavení.

![Křivka VP pomocí PSNR](media/content-aware-encoding/msrv3.png)

**Obrázek 3: Křivka VP pomocí PSNR pro vstup nízké kvality (při 1080p)**

![Křivka RD pomocí VMAF](media/content-aware-encoding/msrv4.png)

**Obrázek 4: Křivka RD s použitím VMAF pro vstup nízké kvality (při 1080p)**

## <a name="how-to-use-the-content-aware-encoding-preset"></a>Použití přednastavení kódování podle obsahu 

Můžete vytvořit transformace, které používají toto přednastavení následujícím způsobem. 

> [!TIP]
> Kurzy, které používají tranformní výstupy, najdete v části [Další kroky.](#next-steps) Výstupní datový zdroj lze doručit z koncových bodů streamování media services v protokolech, jako je MPEG-DASH a HLS (jak je znázorněno v kurzech).


```csharp
TransformOutput[] output = new TransformOutput[]
{
   new TransformOutput
   {
      // The preset for the Transform is set to one of Media Services built-in sample presets.
      // You can customize the encoding settings by changing this to use "StandardEncoderPreset" class.
      Preset = new BuiltInStandardEncoderPreset()
      {
         // This sample uses the new preset for content-aware encoding
         PresetName = EncoderNamedPreset.ContentAwareEncoding
      }
   }
};
```

> [!NOTE]
> Úlohy kódování `ContentAwareEncoding` pomocí přednastavení se účtují na základě výstupních minut. 

## <a name="next-steps"></a>Další kroky

* [Kurz: Nahrávání, kódování a streamování videí pomocí Mediálních služeb v3](stream-files-tutorial-with-api.md)
* [Kurz: Zakódujte vzdálený soubor na základě adresy URL a streamujte video - REST](stream-files-tutorial-with-rest.md)
* [Kurz: Zakódujte vzdálený soubor na základě adresy URL a streamujte video - CLI](stream-files-cli-quickstart.md)
* [Kurz: Zakódujte vzdálený soubor na základě adresy URL a streamujte video - .NET](stream-files-dotnet-quickstart.md)
* [Kurz: Zakódujte vzdálený soubor na základě adresy URL a streamujte video - Node.js](stream-files-nodejs-quickstart.md)
