---
title: Experimentální přednastavení kódování obsahu – Azure | Dokumentace Microsoftu
description: Tento článek popisuje, kódování obsahu ve službě Azure Media Services
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
ms.openlocfilehash: ddb7bfd2437af806c8db75068c50545e69867ea0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65151007"
---
# <a name="experimental-preset-for-content-aware-encoding"></a>Experimentální přednastavení kódování obsahu

Aby bylo možné připravit obsah pro doručování pomocí [streamování s adaptivní přenosovou rychlostí](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), video musí být zakódovaný, aby na více přenosových rychlostí (nejvyšších po nejnižší). Aby bylo možné zajistit bezproblémové snížení kvality, jako přenosové rychlosti klesnou tedy rozlišení videa. Výsledkem je takzvané kódování žebříku – tabulku rozlišení a přenosových rychlostí; Media Services najdete v článku [integrované kódovací Předvolby](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset).

## <a name="overview"></a>Přehled

Zvýšit zájem o překonání přístup založený na jeden – přednastavení – přizpůsobí all – videa po publikování Netflix jejich [blogu](https://medium.com/netflix-techblog/per-title-encode-optimization-7e99442b62a2) v prosinci 2015. Od té doby byly vydány více řešení pro kódování obsahu na webu Marketplace. Zobrazit [v tomto článku](https://www.streamingmedia.com/Articles/Editorial/Featured-Articles/Buyers-Guide-to-Per-Title-Encoding-130676.aspx) Přehled. Cílem je mít na paměti obsahu k přizpůsobení nebo vyladit kódování přenosových rychlostí na složitost jednotlivé video. V každé řešení je přenosové rychlosti, jejichž překročení zvýšení kvality není perceptive – kodér pracuje na tuto hodnotu optimální s přenosovou rychlostí. Další úroveň optimalizace je vybrat řešení na základě obsahu – například video s Powerpointovou prezentaci. nijak přínosné přejít níže 720p. Když budete pokračovat, kodér úkol k optimalizaci nastavení pro každý snímek ve videu. Netflix popsané [takový přístup](https://medium.com/netflix-techblog/optimized-shot-based-encodes-now-streaming-4b9464204830) v roce 2018.

V roku 2017, společnost Microsoft vydala [adaptivní streamování](autogen-bitrate-ladder.md) přednastavenou k vyřešení problému variabilita kvalitu a rozlišení videa zdroje. Naši zákazníci měli různou kombinaci obsahu, některé na 1080p, ostatní na 720p a několik na SD a nižší rozlišení. Kromě toho všechny zdrojový obsah byl vysoce kvalitní mezzanines z filmů nebo televize studios. Adaptivní streamování přednastavených adresy těchto problémů tím, že zajišťuje, že adaptivní žebříček přenosových rychlostí nikdy nepřesáhne rozlišení nebo průměrnou bitrate vstupní soubor mezzanine.

Experimentální předvolba kódování obsahu rozšiřuje tento mechanismus začleněním vlastní logiku, která umožňuje kodér hledání optimální s přenosovou rychlostí hodnotu pro dané řešení, ale bez nutnosti rozsáhlé výpočetní analýzu. Net výsledkem je, že toto nové přednastavení vytvoří výstup, který má nižší přenosovou rychlostí než adaptivní streamování přednastavení, ale na vyšší kvality. Zobrazit následující ukázkové grafy, které zobrazují porovnání pomocí metrik kvality, jako je [PSNR](https://en.wikipedia.org/wiki/Peak_signal-to-noise_ratio) a [VMAF](https://en.wikipedia.org/wiki/Video_Multimethod_Assessment_Fusion). Zdroj se vytváří zřetězením krátké klipy vysokou složitost snímky z videa a televizních pořadů, určený k zátěžovému kodér. Podle definice této přednastavených vytvoří výsledky, které se liší od obsahu pro obsah – také znamená, že pro nějaký obsah, nemusí být s přenosovou rychlostí výrazné snížení nebo zvýšení kvality.

![Kurz – narušení (RD) křivky pomocí PSNR](media/cae-experimental/msrv1.png)

**Obrázek 1: Kurz – narušení (RD) křivky použitím PSNR metrik pro vysokou složitost zdroj**

![Kurz – narušení (RD) křivky pomocí VMAF](media/cae-experimental/msrv2.png)

**Obrázek 2: Kurz – narušení (RD) křivky použitím VMAF metrik pro vysokou složitost zdroj**

Přednastavený kontext aktuálně je vyladěný pro vysokou složitost, vysoce kvalitní zdroj videa (filmy, televizní pořady). Práce se přizpůsobit obsah s nízkou složitost (například prezentace aplikace PowerPoint), a také slabší kvalita videa. Tato předvolba stejnou sadu řešení také využívá jako adaptivní streamování předvolby. Microsoft pracuje na metody vybrat minimální sadu řešení na základě obsahu. Takto se výsledky pro jinou kategorii zdrojový obsah, kde bylo možné určit, že tento vstup byl špatné kvality (mnoho komprese artefakty z důvodu nízkou přenosové rychlosti) kodér. Všimněte si, že se experimentální přednastavení, kodéru se rozhodli vytvořit pouze jeden výstupní vrstvě – dostatečně nízko přenosovou rychlostí, tak, aby většina klientů bude možné přehrát datový proud bez zablokování.

![Pomocí PSNR křivky vzdálené plochy](media/cae-experimental/msrv3.png)

**Obrázek 3: VP křivky pomocí PSNR pro vstup nízké kvality (ve 1080p)**

![Pomocí VMAF křivky vzdálené plochy](media/cae-experimental/msrv4.png)

**Obrázek 4: VP křivky pomocí VMAF pro vstup nízké kvality (ve 1080p)**

## <a name="use-the-experimental-preset"></a>Použít experimentální přednastavení

Můžete vytvořit transformace, které používají předvolby následujícím způsobem. Pokud používáte kurz [takovou situaci](stream-files-tutorial-with-api.md), můžete aktualizovat kód následujícím způsobem:

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
> Předpona "experimentální" zde slouží k signalizaci, že základní algoritmy se dál vyvíjejí. Zde můžete a budou změny v průběhu času logikou používanou pro generování žebříky s přenosovou rychlostí s cílem slučuje na algoritmus, který je robustní a adaptují se pro širokou škálu vstupních podmínek. Kódování úloh pomocí tento přednastavených budou být se fakturuje na základě výstupních minut a výstupního prostředku můžete doručovaným z našich koncových bodů streamování v protokolech, jako je například DASH nebo HLS.

## <a name="next-steps"></a>Další postup

Teď, když jste se dozvěděli o tato nová možnost optimalizovat vaše videa, Zveme vás, můžete vyzkoušet. Pošlete nám svůj názor pomocí odkazů na konci tohoto článku nebo nám více přímo na zapojení <amsved@microsoft.com>.
