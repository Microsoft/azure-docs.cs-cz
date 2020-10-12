---
title: Přednastavení pro kódování s podporou obsahu – Azure Media Services
description: Tento článek popisuje kódování zohledňující obsah v Microsoft Azure Media Services V3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 5d08e09905841f6068f2bac45680a8e5a011c158
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89297361"
---
# <a name="use-the-content-aware-encoding-preset-to-find-the-optimal-bitrate-value-for-a-given-resolution"></a>K vyhledání optimální přenosové hodnoty pro dané řešení použijte předvolbu kódování zohledňující obsah.

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Aby bylo možné připravit obsah pro doručování [datovým proudem s adaptivní přenosovou rychlostí](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), je nutné zakódovat video s více přenosovými rychlostmi (vysoká až nízká). Tím se zajistí řádné snížení kvality, protože přenosová rychlost je snížena, takže se jedná o rozlišení videa. Například kódování s více přenosovými rychlostmi používá pro kódovací žebřík, který se označuje jako tabulka rozlišení a přenosové rychlosti, informace o Media Services [vestavěných přednastavení kódování](/rest/api/media/transforms/createorupdate#encodernamedpreset).

Měli byste znát obsah, který zpracováváte, a přizpůsobit nebo ladit žebřík pro kódování na složitost jednotlivého videa. U každého řešení existuje přenosová rychlost, nad kterou se jakékoliv zvýšení kvality neperceptive – kodér pracuje s touto optimální přenosovou hodnotou. Další úrovní optimalizace je výběr rozlišení na základě obsahu – například video prezentace v PowerPointu nepřináší dál nižší verzi než 720p. Dále může být kodér vydaný pro optimalizaci nastavení pro každý snímek ve videu. 

Přednastavení [adaptivního streamování](autogen-bitrate-ladder.md) od Microsoftu částečně řeší problém proměnlivosti kvality a rozlišení zdrojových videí. Naši zákazníci mají různou škálu obsahu, některé na webu 1080p, dalších ve 720p a pár v SD a nižších rozlišeních. Kromě toho ne všechen zdrojový obsah je vysoce kvalitní mezzanines z filmu nebo TV studia. Přednastavení adaptivního streamování řeší tyto problémy tím, že zajišťuje, že žebřík přenosů nikdy nepřekračuje rozlišení nebo průměrnou rychlost vstupního mezzanineu. Tato předvolba však neověřuje vlastnosti zdrojového kódu kromě rozlišení a přenosové rychlosti.

## <a name="the-content-aware-encoding"></a>Kódování zohledňující obsah 

Přednastavení kódování zohledňující obsah rozšiřuje mechanismus "s adaptivní přenosovou rychlostí", který začleňuje vlastní logiku, která umožňuje kodéru vyhledat optimální přenosovou hodnotu pro dané řešení, ale bez nutnosti rozsáhlé výpočetní analýzy. Tato předvolba vytvoří sadu rychlostmi zarovnaných na skupinu GOP. Vzhledem k jakémukoli vstupnímu obsahu služba provádí počáteční odlehčenou analýzu vstupního obsahu a výsledky používá k určení optimálního počtu vrstev, vhodné rychlosti a nastavení rozlišení pro doručování pomocí adaptivního streamování. Tato předvolba je zvláště platná pro videa s nízkou a střední složitostí, kde výstupní soubory budou s nižšími přenosovými rychlostmi, než je přednastavení adaptivního streamování, ale kvalita, která uživatelům poskytuje dobrý zážitek. Výstup bude obsahovat soubory MP4 se zakládaným videem a zvukem.

Následující ukázkové grafy znázorňují porovnání pomocí metrik kvality, jako je [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) a [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion). Zdroj byl vytvořen zřetězením krátkých klipů vysoce složitých snímků z filmů a televizních pořadů, které mají za cíl zátěž kodéru. Podle definice Tato předvolba vytvoří výsledky, které se liší od obsahu k obsahu – to také znamená, že u určitého obsahu se nemusí významně snížit přenosová rychlost nebo zlepšení kvality.

![Frekvence – deformace (RD) – křivka pomocí PSNR](media/content-aware-encoding/msrv1.png)

**Obrázek 1: křivka deformace (RD) pomocí metriky PSNR pro zdroj s vysokou složitostí**

![Frekvence – deformace (RD) – křivka pomocí VMAF](media/content-aware-encoding/msrv2.png)

**Obrázek 2: křivka deformace (RD) pomocí metriky VMAF pro zdroj s vysokou složitostí**

Níže jsou uvedeny výsledky jiné kategorie zdrojového obsahu, kde kodér dokázal určit, že vstup má špatnou kvalitu (mnohé kompresní artefakty z důvodu nízké přenosové rychlosti). Všimněte si, že u přednastaveného obsahu, kodér se rozhodl vytvořit jenom jednu výstupní vrstvu – s dostatečně nízkou přenosovou rychlostí, aby většina klientů mohla přehrát datový proud bez zastavení.

![Křivka VP pomocí PSNR](media/content-aware-encoding/msrv3.png)

**Obrázek 3: křivka RD pomocí PSNR pro vstup s nízkou kvalitou (v 1080p)**

![Křivka VP pomocí VMAF](media/content-aware-encoding/msrv4.png)

**Obrázek 4: křivka RD pomocí VMAF pro vstup s nízkou kvalitou (v 1080p)**

## <a name="how-to-use-the-content-aware-encoding-preset"></a>Jak používat přednastavení kódování zohledňující obsah 

Transformace, které používají tuto předvolbu, můžete vytvořit následujícím způsobem. 

Kurzy, které používají výstupy transformace, najdete v části [Další kroky](#next-steps) . Výstupní Asset se dá doručovat z Media Services koncových bodů streamování v protokolech, jako jsou MPEG-SPOJOVNÍKy a HLS (jak je znázorněno v kurzech).

> [!NOTE]
> Ujistěte se, že jste použili předvolbu **ContentAwareEncoding** , která není ContentAwareEncodingExperimental.

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
> Úlohy kódování s použitím `ContentAwareEncoding` přednastavení se účtují na základě výstupních minut. 
  
## <a name="next-steps"></a>Další kroky

* [Kurz: nahrávání, kódování a streamování videí pomocí Media Services V3](stream-files-tutorial-with-api.md)
* [Kurz: Kódování vzdáleného souboru na základě adresy URL a streamování videa – REST](stream-files-tutorial-with-rest.md)
* [Kurz: kódování vzdáleného souboru na základě adresy URL a streamu pro video – CLI](stream-files-cli-quickstart.md)
* [Kurz: kódování vzdáleného souboru na základě adresy URL a streamu pro video – .NET](stream-files-dotnet-quickstart.md)
* [Kurz: kódování vzdáleného souboru na základě adresy URL a streamu Node.jsvidea ](stream-files-nodejs-quickstart.md)
