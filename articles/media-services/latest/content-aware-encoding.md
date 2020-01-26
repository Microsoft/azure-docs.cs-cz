---
title: Přednastavení pro kódování s podporou obsahu Azure Media Services |
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
ms.openlocfilehash: 34b4386d536a9845c8a48e7e54612548367b52bc
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759342"
---
# <a name="a-preset-for-content-aware-encoding"></a>Přednastavení pro kódování zohledňující obsah

Aby bylo možné připravit obsah pro doručování [datovým proudem s adaptivní přenosovou rychlostí](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), je nutné zakódovat video s více přenosovými rychlostmi (vysoká až nízká). Aby bylo zajištěno řádné snížení kvality, protože přenosová rychlost je snížena, je rozlišením videa. Výsledkem je, že se označuje jako kódovací žebřík – tabulka s rozlišením a přenosovými rychlostmi; Přečtěte si Media Services [předdefinované předvolby kódování](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset).

## <a name="overview"></a>Přehled

Po Netflix publikování [blogu](https://medium.com/netflix-techblog/per-title-encode-optimization-7e99442b62a2) v prosinci 2015 je důležité přesunout se do pohybu po jednom přednastaveném přístupu všech videí. Od té doby byly na webu Marketplace vydány více řešení pro kódování s podporou obsahu; Přehled najdete v [tomto článku](https://www.streamingmedia.com/Articles/Editorial/Featured-Articles/Buyers-Guide-to-Per-Title-Encoding-130676.aspx) . Nápad je vědět o obsahu – pro přizpůsobení nebo ladění kódovacích žebříků ke složitosti jednotlivých videí. U každého řešení existuje přenosová rychlost, nad kterou se jakékoliv zvýšení kvality neperceptive – kodér pracuje s touto optimální přenosovou hodnotou. Další úrovní optimalizace je výběr rozlišení na základě obsahu – například video prezentace v PowerPointu nepřináší dál nižší verzi než 720p. Dále může být kodér vydaný pro optimalizaci nastavení pro každý snímek ve videu. Netflix popisuje [takový přístup](https://medium.com/netflix-techblog/optimized-shot-based-encodes-now-streaming-4b9464204830) v 2018.

V rané 2017 společnost Microsoft vydala přednastavení [adaptivního streamování](autogen-bitrate-ladder.md) , které řeší problém proměnlivosti kvality a rozlišení zdrojových videí. Naši zákazníci měli různou škálu obsahu, některé na webu 1080p, dalších ve 720p a pár v SD a nižších rozlišeních. Kromě toho ne všechen zdrojový obsah byl vysoce kvalitní mezzanines z filmu nebo TV studia. Přednastavení adaptivního streamování řeší tyto problémy tím, že zajišťuje, že žebřík přenosů nikdy nepřekračuje rozlišení nebo průměrnou rychlost vstupního mezzanineu.

Nová předvolba kódování zohledňující obsah rozšiřuje tento mechanismus tím, že začleňuje vlastní logiku, která umožňuje, aby kodér hledal optimální přenosovou hodnotu pro dané řešení, ale bez nutnosti rozsáhlé výpočetní analýzy. Tato předvolba vytvoří sadu rychlostmi zarovnaných na skupinu GOP. Vzhledem k jakémukoli vstupnímu obsahu služba provádí počáteční odlehčenou analýzu vstupního obsahu a výsledky používá k určení optimálního počtu vrstev, vhodné rychlosti a nastavení rozlišení pro doručování pomocí adaptivního streamování. Tato předvolba je zvláště platná pro videa s nízkou a střední složitostí, kde výstupní soubory budou s nižšími přenosovými rychlostmi, než je přednastavení adaptivního streamování, ale kvalita, která uživatelům poskytuje dobrý zážitek. Výstup bude obsahovat soubory MP4 se zakládaným videem a zvukem.

Podívejte se na následující ukázkové grafy, které znázorňují porovnání pomocí metrik kvality, jako je [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) a [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion). Zdroj byl vytvořen zřetězením krátkých klipů vysoce složitých snímků z filmů a televizních pořadů, které mají za cíl zátěž kodéru. Podle definice Tato předvolba vytvoří výsledky, které se liší od obsahu k obsahu – to také znamená, že u určitého obsahu se nemusí významně snížit přenosová rychlost nebo zlepšení kvality.

![Frekvence – deformace (RD) – křivka pomocí PSNR](media/cae-experimental/msrv1.png)

**Obrázek 1: křivka deformace (RD) pomocí metriky PSNR pro zdroj s vysokou složitostí**

![Frekvence – deformace (RD) – křivka pomocí VMAF](media/cae-experimental/msrv2.png)

**Obrázek 2: křivka deformace (RD) pomocí metriky VMAF pro zdroj s vysokou složitostí**

Níže jsou uvedeny výsledky jiné kategorie zdrojového obsahu, kde kodér dokázal určit, že vstup má špatnou kvalitu (mnohé kompresní artefakty z důvodu nízké přenosové rychlosti). Všimněte si, že u přednastaveného obsahu, kodér se rozhodl vytvořit jenom jednu výstupní vrstvu – s dostatečně nízkou přenosovou rychlostí, aby většina klientů mohla přehrát datový proud bez zastavení.

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
         // This sample uses the new preset for content-aware encoding
         PresetName = EncoderNamedPreset.ContentAwareEncoding
      }
   }
};
```

> [!NOTE]
> Základní algoritmy jsou předmětem dalších vylepšení. V průběhu času mohou být v průběhu času provedeny změny logiky používané pro generování přenosových žebříků s cílem poskytnout algoritmus, který je robustní a přizpůsobí se široké škále vstupních podmínek. Úlohy kódování s použitím této předvolby se budou dál účtovat na základě výstupních minut a výstupní Asset se dá doručit z našich koncových bodů streamování v protokolech, jako jsou POMLČKy a HLS.

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s touto novou možností optimalizace vašich videí, vás budeme zvát k tomu, abyste si ji vyzkoušeli. Pomocí odkazů na konci tohoto článku můžete poslat nám svůj názor.
