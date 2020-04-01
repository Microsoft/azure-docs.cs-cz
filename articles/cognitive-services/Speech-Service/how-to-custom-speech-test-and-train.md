---
title: Příprava dat pro vlastní řeč – služba Řeč
titleSuffix: Azure Cognitive Services
description: Při testování přesnosti rozpoznávání řeči společnosti Microsoft nebo trénování vlastních modelů budete potřebovat zvuková a textová data. Na této stránce se zabýváme typy dat, jak je používat a spravovat.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/27/2020
ms.author: dapine
ms.openlocfilehash: d6e377ca4f33c0160267b23daaaaef5e07c271bb
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398686"
---
# <a name="prepare-data-for-custom-speech"></a>Příprava dat pro vlastní řeč

Při testování přesnosti rozpoznávání řeči společnosti Microsoft nebo trénování vlastních modelů budete potřebovat zvuková a textová data. Na této stránce se zabýváme typy dat, jak je používat a spravovat.

## <a name="data-types"></a>Typy dat

V této tabulce jsou uvedeny přijaté datové typy, kdy by měl být použit každý datový typ, a doporučené množství. Ne každý datový typ je nutné vytvořit model. Požadavky na data se budou lišit v závislosti na tom, zda vytváříte test nebo trénujete model.

| Datový typ | Používá se pro testování | Doporučené množství | Používá se pro školení | Doporučené množství |
|-----------|-----------------|----------|-------------------|----------|
| [Zvuk](#audio-data-for-testing) | Ano<br>Používá se pro vizuální kontrolu | 5+ zvukových souborů | Ne | N/a |
| [Audio + Přepisy označené člověkem](#audio--human-labeled-transcript-data-for-testingtraining) | Ano<br>Používá se k vyhodnocení přesnosti | 0,5-5 hodin zvuku | Ano | 1-1 000 hodin zvuku |
| [Související text](#related-text-data-for-training) | Ne | N/a | Ano | 1-200 MB souvisejícího textu |

Soubory by měly být seskupeny podle typu do datové sady a odeslány jako soubor ZIP. Každá datová sada může obsahovat pouze jeden datový typ.

> [!TIP]
> Chcete-li rychle začít, zvažte použití ukázkových dat. Podívejte se na toto úložiště GitHubu pro <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/sampledata/customspeech" target="_target">ukázková vlastní data <span class="docon docon-navigate-external x-hidden-focus"></span> řeči</a>

## <a name="upload-data"></a>Nahrání dat

Chcete-li data nahrát, přejděte na <a href="https://speech.microsoft.com/customspeech" target="_blank">portál <span class="docon docon-navigate-external x-hidden-focus"> </span>Vlastní řeč </a>. Na portálu klikněte na **Nahrát data,** abyste průvodce spustili a vytvořili první datovou sadu. Než vám umožní nahrát data, budete požádáni o výběr datového typu řeči pro vaši datovou sadu.

![Výběr zvuku z řečového portálu](./media/custom-speech/custom-speech-select-audio.png)

Každá nahraná datová sada musí splňovat požadavky na daný datový typ. Vaše data musí být před nahráním správně naformátována. Správně formátovaná data zajistí, že budou přesně zpracována službou Vlastní řeč. Požadavky jsou uvedeny v následujících částech.

Po nahrání datové sady máte několik možností:

* Můžete přejít na kartu **Testování** a vizuálně zkontrolovat pouze zvuk nebo zvuk + data přepisu s lidským označením.
* Můžete přejít na kartu **Školení** a použít data audio + lidský přepis nebo související textová data k trénování vlastního modelu.

## <a name="audio-data-for-testing"></a>Zvuková data pro testování

Zvuková data jsou optimální pro testování přesnosti základního modelu převodu řeči na text společnosti Microsoft nebo vlastního modelu. Mějte na paměti, že zvuková data se používají ke kontrole přesnosti řeči s ohledem na výkon konkrétního modelu. Pokud chcete kvantifikovat přesnost modelu, použijte [audio + data přepisu s lidským označením](#audio--human-labeled-transcript-data-for-testingtraining).

V této tabulce můžete zajistit, aby byly zvukové soubory správně formátovány pro použití s vlastní řečí:

| Vlastnost                 | Hodnota                 |
|--------------------------|-----------------------|
| Formát souboru              | RIFF (WAV)            |
| Vzorkovací frekvence              | 8 000 Hz nebo 16 000 Hz |
| Kanály                 | 1 (mono)              |
| Maximální délka na zvuk | 2 hodiny               |
| Ukázkový formát            | PCM, 16bitový           |
| Archivní formát           | .zip                  |
| Maximální velikost archivu     | 2 GB                  |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!TIP]
> Při nahrávání trénovacích a testovacích dat nesmí velikost souboru ZIP překročit 2 GB. Pokud požadujete více dat pro školení, rozdělte je do několika souborů ZIP a nahrajte je samostatně. Později můžete trénovat z *více* datových sad. Test však lze testovat pouze z *jedné* datové sady.

Pomocí <a href="http://sox.sourceforge.net" target="_blank" rel="noopener">soxu <span class="docon docon-navigate-external x-hidden-focus"></span> </a> ověřte vlastnosti zvuku nebo převeďte existující zvuk do příslušných formátů. Níže jsou uvedeny některé příklady, jak lze provádět každou z těchto aktivit prostřednictvím příkazového řádku SoX:

| Aktivita | Popis | SoX, příkaz |
|----------|-------------|-------------|
| Kontrola formátu zvuku | Pomocí tohoto příkazu můžete zkontrolovat<br>ve formátu zvukového souboru. | `sox --i <filename>` |
| Převést zvukový formát | Pomocí tohoto příkazu můžete převést<br>jednoho kanálu, 16bitového, 16 KHz. | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>Audio + člověkem označené přepisové údaje pro testování / školení

Chcete-li měřit přesnost přesnosti řeči na text společnosti Microsoft při zpracování zvukových souborů, je nutné pro porovnání poskytnout přepisy označené člověkem (slovo za slovem). Zatímco přepis s lidským označením je často časově náročný, je nutné vyhodnotit přesnost a trénovat model pro vaše případy použití. Mějte na paměti, že zlepšení rozpoznávání bude pouze tak dobré, jako poskytnuté údaje. Z tohoto důvodu je důležité, aby byly nahrány pouze vysoce kvalitní přepisy.

| Vlastnost                 | Hodnota                               |
|--------------------------|-------------------------------------|
| Formát souboru              | RIFF (WAV)                          |
| Vzorkovací frekvence              | 8 000 Hz nebo 16 000 Hz               |
| Kanály                 | 1 (mono)                            |
| Maximální délka na zvuk | 2 hodiny (testování) / 60 s (trénink) |
| Ukázkový formát            | PCM, 16bitový                         |
| Archivní formát           | .zip                                |
| Maximální velikost zipu         | 2 GB                                |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!NOTE]
> Při nahrávání trénovacích a testovacích dat nesmí velikost souboru ZIP překročit 2 GB. Můžete testovat pouze z *jedné* datové sady, ujistěte se, že ji zachovat v příslušné velikosti souboru. Navíc každý soubor školení nesmí překročit 60 sekund jinak dojde k chybě.

Chcete-li řešit problémy, jako je odstranění nebo nahrazení slova, je nutné značné množství dat ke zlepšení rozpoznávání. Obecně se doporučuje zadat přepisy slovo za slovem pro zhruba 10 až 1 000 hodin zvuku. Přepisy všech souborů WAV by měl obsahovat jediný soubor prostého textu. Každý řádek souboru s přepisem by měl obsahovat název jednoho zvukového souboru a za ním odpovídající přepis. Název souboru a přepis by měly být oddělené tabulátorem (\t).

  Například:
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```

> [!IMPORTANT]
> Přepis by měl být kódovaný ve formátu UTF-8 BOM (značka pořadí bajtů).

Přepisy se budou normalizovat, aby je mohl systém zpracovat. Existují však některé důležité normalizace, které je třeba provést před odesláním dat do aplikace Speech Studio. Vhodný jazyk, který se má použít při přípravě přepisů, najdete v [tématu Jak vytvořit přepis označený člověkem.](how-to-custom-speech-human-labeled-transcriptions.md)

Po shromáždění zvukových souborů a odpovídajících přepisů je před nahráním na <a href="https://speech.microsoft.com/customspeech" target="_blank">portál <span class="docon docon-navigate-external x-hidden-focus"> </span>Vlastní řeč </a>zabalte jako jeden soubor ZIP . Níže je uveden příklad datové sady se třemi zvukovými soubory a souborem přepisu s lidským označením:

> [!div class="mx-imgBorder"]
> ![Výběr zvuku z řečového portálu](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

## <a name="related-text-data-for-training"></a>Související textová data pro školení

Názvy produktů nebo funkce, které jsou jedinečné, by měly obsahovat související textová data pro školení. Související text pomáhá zajistit správné rozpoznávání. Pro zlepšení rozpoznávání lze poskytnout dva typy souvisejících textových dat:

| Datový typ | Jak tyto údaje zlepšují rozpoznávání |
|-----------|------------------------------------|
| Věty (projevy) | Zlepšete přesnost při rozpoznávání názvů produktů nebo slovníku specifického pro dané odvětví v kontextu věty. |
| Výslovnosti | Zlepšete výslovnost neobvyklých termínů, akronymů nebo jiných slov s nedefinovanými výslovnostmi. |

Věty mohou být poskytnuty jako jeden textový soubor nebo více textových souborů. Chcete-li zlepšit přesnost, použijte textová data, která je blíže k očekávané mluvené projevy. Výslovnosti by měly být poskytnuty jako jeden textový soubor. Vše lze zabalit jako jeden soubor zip a nahrát na <a href="https://speech.microsoft.com/customspeech" target="_blank">portál <span class="docon docon-navigate-external x-hidden-focus"> </span>vlastní řeči </a>.

### <a name="guidelines-to-create-a-sentences-file"></a>Pokyny pro vytvoření souboru vět

Chcete-li vytvořit vlastní model pomocí vět, budete muset zadat seznam ukázkových projevů. Projevy _nemusí_ být úplné nebo gramaticky správné, ale musí přesně odrážet mluvený vstup, který očekáváte v produkčním prostředí. Pokud chcete, aby určité termíny měly zvýšenou váhu, přidejte několik vět, které obsahují tyto konkrétní termíny.

Jako obecné pokyny je přizpůsobení modelu nejúčinnější, pokud je text školení co nejblíže skutečnému textu očekávanému ve výrobě. Do trénovacího textu by měl y být zahrnuty žargon a fráze specifické pro doménu, které chcete vylepšit. Pokud je to možné, pokuste se mít jednu větu nebo klíčové slovo řízené na samostatném řádku. U klíčových slov a frází, které jsou pro vás důležité (například názvy produktů), je můžete několikrát zkopírovat. Ale mějte na paměti, nekopírujte příliš mnoho - mohlo by to ovlivnit celkovou míru uznání.

V této tabulce můžete zajistit, aby byl související datový soubor pro projevy správně formátován:

| Vlastnost | Hodnota |
|----------|-------|
| Kódování textu | UTF-8 BOM |
| Počet promluv na řádek | 1 |
| Maximální velikost souboru | 200 MB |

Kromě toho budete chtít účet pro následující omezení:

* Vyhněte se opakování znaků více než čtyřikrát. Například: "aaaa" nebo "uuuu".
* Nepoužívejte speciální znaky nebo znaky UTF-8 výše `U+00A1`.
* Identifikátory URI budou odmítnuty.

### <a name="guidelines-to-create-a-pronunciation-file"></a>Pokyny pro vytvoření souboru výslovnosti

Pokud existují neobvyklé termíny bez standardních výslovností, se kterými se uživatelé setkají nebo které budou používat, můžete poskytnout vlastní soubor výslovnosti pro zlepšení rozpoznávání.

> [!IMPORTANT]
> Nedoporučuje se používat vlastní soubory výslovnosti ke změně výslovnosti běžných slov.

To zahrnuje příklady mluvené utterance a vlastní výslovnost pro každý:

| Rozpoznaný/zobrazený formulář | Mluvená forma |
|--------------|--------------------------|
| 3CPO | tři c p o |
| CNTK | c n t k |
| Ieee | i trojitý e |

Mluvená forma je fonetický sekvence je napsán. Může se skládat z písmene, slov, slabik nebo kombinace všech tří.

Vlastní výslovnost je k dispozici`en-US`v angličtině`de-DE`( ) a němčině ( ). V této tabulce jsou uvedeny podporované znaky podle jazyka:

| Jazyk | Národní prostředí | Znaky |
|----------|--------|------------|
| Angličtina | `en-US` | `a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |
| Němčina | `de-DE` | `ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |

V následující tabulce zajistíte, že související datový soubor pro výslovnosti je správně formátován. Soubory výslovnosti jsou malé a měly by mít velikost pouze několik kilobajtů.

| Vlastnost | Hodnota |
|----------|-------|
| Kódování textu | Kusovník UTF-8 (ANSI je také podporován pro angličtinu) |
| Počet výslovností na řádek | 1 |
| Maximální velikost souboru | 1 MB (1 KB pro bezplatnou úroveň) |

## <a name="next-steps"></a>Další kroky

* [Kontrola dat](how-to-custom-speech-inspect-data.md)
* [Vyhodnocení dat](how-to-custom-speech-evaluate-data.md)
* [Trénování vašeho modelu](how-to-custom-speech-train-model.md)
* [Nasazení modelu](how-to-custom-speech-deploy-model.md)
