---
title: Experimentální přednastavení pro kódování s podporou obsahu – Azure | Microsoft Docs
description: Tento článek popisuje kódování zohledňující obsah v Microsoft Azure Media Services V3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/05/2019
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: 9389466b6291542563c068706479bf981c5880da
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896141"
---
# <a name="experimental-preset-for-content-aware-encoding"></a>Experimentální přednastavení pro kódování zohledňující obsah

Aby bylo možné připravit obsah pro doručování [datovým proudem s adaptivní přenosovou rychlostí](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), je nutné zakódovat video s více přenosovými rychlostmi (vysoká až nízká). Aby bylo zajištěno řádné snížení kvality, protože přenosová rychlost je snížena, je rozlišením videa. Výsledkem je, že se označuje jako kódovací žebřík – tabulka s rozlišením a přenosovými rychlostmi; Přečtěte si Media Services [předdefinované předvolby kódování](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset).

## <a name="overview"></a>Přehled

Po Netflix publikování [blogu](https://medium.com/netflix-techblog/per-title-encode-optimization-7e99442b62a2) v prosinci 2015 je důležité přesunout se do pohybu po jednom přednastaveném přístupu všech videí. Od té doby byly na webu Marketplace vydány více řešení pro kódování s podporou obsahu; Přehled najdete v [tomto článku](https://www.streamingmedia.com/Articles/Editorial/Featured-Articles/Buyers-Guide-to-Per-Title-Encoding-130676.aspx) . Nápad je vědět o obsahu – pro přizpůsobení nebo ladění kódovacích žebříků ke složitosti jednotlivých videí. U každého řešení existuje přenosová rychlost, nad kterou se jakékoliv zvýšení kvality neperceptive – kodér pracuje s touto optimální přenosovou hodnotou. Další úrovní optimalizace je výběr rozlišení na základě obsahu – například video prezentace v PowerPointu nepřináší dál nižší verzi než 720p. Dále může být kodér vydaný pro optimalizaci nastavení pro každý snímek ve videu. Netflix popisuje [takový přístup](https://medium.com/netflix-techblog/optimized-shot-based-encodes-now-streaming-4b9464204830) v 2018.

V rané 2017 společnost Microsoft vydala přednastavení [adaptivního streamování](autogen-bitrate-ladder.md) , které řeší problém proměnlivosti kvality a rozlišení zdrojových videí. Naši zákazníci měli různou škálu obsahu, některé na webu 1080p, dalších ve 720p a pár v SD a nižších rozlišeních. Kromě toho ne všechen zdrojový obsah byl vysoce kvalitní mezzanines z filmu nebo TV studia. Přednastavení adaptivního streamování řeší tyto problémy tím, že zajišťuje, že žebřík přenosů nikdy nepřekračuje rozlišení nebo průměrnou rychlost vstupního mezzanineu.

Experimentální přednastavení kódování zohledňující obsah rozšiřuje tento mechanismus tím, že začleňuje vlastní logiku, která umožňuje, aby kodér hledal optimální přenosovou hodnotu pro dané řešení, ale bez nutnosti rozsáhlých výpočetních analýz. Výsledkem je, že tato nová předvolba vytvoří výstup, který má nižší přenosovou rychlost než přednastavení adaptivního streamování, ale s vyšší kvalitou. Podívejte se na následující ukázkové grafy, které znázorňují porovnání pomocí metrik kvality, jako je [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) a [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion). Zdroj byl vytvořen zřetězením krátkých klipů vysoce složitých snímků z filmů a televizních pořadů, které mají za cíl zátěž kodéru. Podle definice Tato předvolba vytvoří výsledky, které se liší od obsahu k obsahu – to také znamená, že u určitého obsahu se nemusí významně snížit přenosová rychlost nebo zlepšení kvality.

![Frekvence – deformace (RD) – křivka pomocí PSNR](media/cae-experimental/msrv1.png)

**Obrázek 1: křivka deformace (RD) pomocí metriky PSNR pro zdroj s vysokou složitostí**

![Frekvence – deformace (RD) – křivka pomocí VMAF](media/cae-experimental/msrv2.png)

**Obrázek 2: křivka deformace (RD) pomocí metriky VMAF pro zdroj s vysokou složitostí**

Předvolba je aktuálně vyladěná pro vysokou složitost a vysoce kvalitní zdrojová videa (filmy, televizní pořady). Práce probíhá s cílem přizpůsobovat obsah s nízkou složitostí (například prezentace aplikace PowerPoint) a také kvalitní videa. Tato předvolba také používá stejnou sadu rozlišení jako přednastavení adaptivního streamování. Microsoft pracuje na metodách pro výběr minimální sady rozlišení na základě obsahu. Takto jsou výsledky jiné kategorie zdrojového obsahu, kde kodér dokázal určit, že vstup má špatnou kvalitu (mnohé kompresní artefakty z důvodu nízké přenosové rychlosti). Všimněte si, že pomocí experimentální předvolby se kodér rozhodl vytvořit pouze jednu výstupní vrstvu – s dostatečně nízkou rychlostí, aby většina klientů mohla přehrát datový proud bez zastavení.

![Křivka VP pomocí PSNR](media/cae-experimental/msrv3.png)

**Obrázek 3: křivka VP s použitím PSNR pro vstup s nízkou kvalitou (v 1080p)**

![Křivka VP pomocí VMAF](media/cae-experimental/msrv4.png)

**Obrázek 4: křivka RD pomocí VMAF pro vstup s nízkou kvalitou (v 1080p)**

## <a name="use-the-experimental-preset"></a>Použití experimentální předvolby

Transformace, které používají tuto předvolbu, můžete vytvořit následujícím způsobem. Při použití [takového](stream-files-tutorial-with-api.md)kurzu můžete kód aktualizovat následujícím způsobem:

```csharp
TransformOutput[] output = new TransformOutput[]
{
   new TransformOutput
   {
      // The preset for the Transform is set to one of Media Services built-in sample presets.
      // You can customize the encoding settings by changing this to use "StandardEncoderPreset" class.
      Preset = new BuiltInStandardEncoderPreset()
      {
         // This sample uses the new experimental preset for content-aware encoding
         PresetName = EncoderNamedPreset.ContentAwareEncodingExperimental
      }
   }
};
```

> [!NOTE]
> Předpona "experimentální" se tady používá k signalizaci, že se základní algoritmy pořád rozvíjejí. V průběhu času mohou být v průběhu času provedeny změny logiky používané k tvorbě přenosových žebříků, s cílem sbližování algoritmu, který je robustní a přizpůsobený nejrůznějším vstupním podmínkám. Úlohy kódování s použitím této předvolby se budou dál účtovat na základě výstupních minut a výstupní Asset se dá doručit z našich koncových bodů streamování v protokolech, jako jsou POMLČKy a HLS.

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s touto novou možností optimalizace vašich videí, vás budeme zvát k tomu, abyste si ji vyzkoušeli. Můžete nám poslat zpětnou vazbu pomocí odkazů na konci tohoto článku nebo nás přímo na <amsved@microsoft.com>.
