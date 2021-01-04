---
title: Příprava dat pro službu Custom Speech – Speech
titleSuffix: Azure Cognitive Services
description: Při testování přesnosti rozpoznávání řeči od Microsoftu nebo školení vašich vlastních modelů budete potřebovat zvuková a textová data. Na této stránce se zabýváme typy dat, jak je používat a spravujeme.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: af5ed0296ce99a4450fffec6b047285307ed0ff2
ms.sourcegitcommit: d488a97dc11038d9cef77a0235d034677212c8b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2020
ms.locfileid: "97709295"
---
# <a name="prepare-data-for-custom-speech"></a>Příprava dat pro službu Custom Speech

Při testování přesnosti rozpoznávání řeči od Microsoftu nebo školení vašich vlastních modelů budete potřebovat zvuková a textová data. Na této stránce se zabýváme typy dat, které jsou potřeba pro vlastní modely řeči.

## <a name="data-diversity"></a>Rozmanitost dat

Text a zvuk, který se používá k otestování a výukě vlastního modelu, musí zahrnovat ukázky z nejrůznějších mluvčích a scénářů, které budete potřebovat pro rozpoznávání vašeho modelu.
Vezměte v úvahu tyto faktory při shromažďování dat pro testování a školení vlastních modelů:

* Data zvukového a hlasového zvuku musí pokrývat typy ústních příkazů, které budou vaši uživatelé dělat při interakci s modelem. Například model, který vyvolává a snižuje požadavky na teplotu v prohlášeních, které můžou lidé udělat, si tyto změny vyžádají.
* Vaše data musí zahrnovat všechny odchylky řeči, které váš model bude potřebovat pro rozpoznávání. Mnohé faktory se můžou lišit v rozpoznávání řeči, včetně akcentů, dialektů, rozbalování jazyků, stáří, pohlaví, hlasové rozteči, úrovně zatížení a denního času.
* Musíte zahrnout ukázky z různých prostředí (vnitřního, venkovního a silničního hluku), kde se bude model používat.
* Zvuk se musí shromažďovat pomocí hardwarových zařízení, která bude produkční systém používat. Pokud váš model potřebuje identifikovat rozpoznávání řeči zaznamenané na záznamovém zařízení s proměnlivou kvalitou, musí také tyto různé scénáře představovat zvuková data, která zadáte pro vedení modelu.
* Později můžete do modelu přidat další data, ale je třeba dbát na to, aby datová sada byla různorodá a reprezentativní pro potřeby vašich projektů.
* Zahrnutí dat, která *nejsou* obsažena v rámci vašich požadavků na rozpoznávání vlastního modelu, může způsobit poškození kvality rozpoznávání, takže neobsahují data, která model nemusí přepisovat.

Model vyškolený v podmnožině scénářů může v těchto scénářích provádět pouze dobře. Pečlivě si vyberte data, která představují kompletní rozsah scénářů, které budete potřebovat pro rozpoznávání vlastního modelu.

> [!TIP]
> Začněte s malými sadami ukázkových dat, která se shodují s jazykem, a akustickým využitím modelu.
> Například zaznamenejte malý, ale reprezentativní vzorek zvuku na stejném hardwaru a ve stejném akustickém prostředí, ve kterém bude model Hledat v produkčních scénářích.
> Malé datové sady reprezentativních dat můžou vystavovat problémy předtím, než budete investovat do shromažďování mnohem větších datových sad pro školení.

## <a name="data-types"></a>Typy dat

Tato tabulka obsahuje seznam povolených datových typů, kdy se má použít každý datový typ a doporučené množství. Pro vytvoření modelu není vyžadován každý datový typ. Požadavky na data se budou lišit v závislosti na tom, zda vytváříte testovací nebo školicí model.

| Datový typ | Používá se pro testování. | Doporučené množství | Používá se pro školení. | Doporučené množství |
|-----------|-----------------|----------|-------------------|----------|
| [Zvuk](#audio-data-for-testing) | Ano<br>Použito pro vizuální kontrolu | 5 zvukových souborů | Ne | – |
| [Audio + přepisy s popiskem](#audio--human-labeled-transcript-data-for-testingtraining) | Ano<br>Používá se k vyhodnocení přesnosti. | 0,5 – 5 hodin zvukového přenosu | Ano | 1-20 hodin zvukového přenosu |
| [Související text](#related-text-data-for-training) | Ne | Není k dispozici | Ano | 1-200 MB souvisejícího textu |

Při učení nového modelu začněte s [souvisejícím textem](#related-text-data-for-training). Tato data budou již vylepšit rozpoznávání speciálních pojmů a frází.

Soubory by měly být seskupené podle typu do datové sady a nahrané jako soubor. zip. Každá datová sada může obsahovat pouze jeden datový typ.

> [!TIP]
> Pokud chcete rychle začít, zvažte použití ukázkových dat. <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/sampledata/customspeech" target="_target">Ukázková Custom Speechová data <span class="docon docon-navigate-external x-hidden-focus"></span> </a> najdete v tomto úložišti GitHubu.

## <a name="upload-data"></a>Nahrání dat

Data odešlete tak, že přejdete na <a href="https://speech.microsoft.com/customspeech" target="_blank">portál <span class="docon docon-navigate-external x-hidden-focus"></span> Custom Speech </a>. Na portálu klikněte na **nahrát data** a spusťte průvodce a vytvořte svou první datovou sadu. Před tím, než budete moci odeslat data, budete požádáni o výběr datového typu řeči pro datovou sadu.

![Snímek obrazovky, který zvýrazní možnost nahrávání zvuku z portálu pro rozpoznávání řeči.](./media/custom-speech/custom-speech-select-audio.png)

Každá datová sada, kterou nahráváte, musí splňovat požadavky na datový typ, který zvolíte. Data musí být před nahráním správně naformátována. Správně naformátovaná data zajišťuje jejich správné zpracování službou Custom Speech. Požadavky jsou uvedené v následujících oddílech.

Po nahrání datové sady máte několik možností:

* Můžete přejít na kartu **testování** a vizuálně zkontrolovat jenom zvuk nebo zvuk + přepisně pojmenovaných dat.
* Můžete přejít na kartu **školení** a použít zvuk a data o přepisu, nebo související textová data, a využít tak vlastní model.

## <a name="audio-data-for-testing"></a>Zvuková data pro testování

Zvuková data jsou ideální pro testování přesnosti základního rozpoznávání řeči a textu od společnosti Microsoft nebo vlastního modelu. Mějte na paměti, že zvuková data se používají ke kontrole přesnosti řeči s ohledem na výkon konkrétního modelu. Pokud hledáte přesnost modelu, použijte [zvuk + přepisně označené data](#audio--human-labeled-transcript-data-for-testingtraining).

Pomocí této tabulky zajistěte, aby byly vaše zvukové soubory správně formátované pro použití s Custom Speech:

| Vlastnost                 | Hodnota                 |
|--------------------------|-----------------------|
| Formát souboru              | RIFF (WAV)            |
| Vzorkovací frekvence              | 8 000 Hz nebo 16 000 Hz |
| Kanály                 | 1 (mono)              |
| Maximální délka na zvuk | 2 hodiny               |
| Formát ukázky            | PCM, 16 bitů           |
| Formát archivu           | .zip                  |
| Maximální velikost archivu     | 2 GB                  |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!TIP]
> Při nahrávání školicích a testovacích dat nemůže být velikost souboru ZIP větší než 2 GB. Pokud potřebujete další data pro školení, rozdělte je do několika souborů zip a nahrajte je samostatně. Později můžete zvolit výuku z *více* datových sad. Můžete však testovat pouze z *jedné* datové sady.

Pomocí <a href="http://sox.sourceforge.net" target="_blank" rel="noopener">Sox <span class="docon docon-navigate-external x-hidden-focus"></span> </a> ověřte vlastnosti zvuku nebo převeďte existující zvuk do příslušných formátů. Níže jsou uvedeny některé příklady, jak lze jednotlivé aktivity provést prostřednictvím příkazového řádku SoX:

| Aktivita | Popis | SoX – příkaz |
|----------|-------------|-------------|
| Kontrolovat zvukový formát | Pomocí tohoto příkazu ověřte<br>formát zvukového souboru. | `sox --i <filename>` |
| Převod zvukového formátu | Tento příkaz slouží k převodu<br>zvukový soubor do jednoho kanálu, 16bitová, 16 KHz. | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>Audio + přepisované údaje přepisu pro testování/školení

Chcete-li změřit přesnost přesnosti řeči na text od společnosti Microsoft při zpracování zvukových souborů, je nutné zadat přepisy (Word-by-Word) pro porovnání. I když je přepis uživatelsky popisku často časově náročný, je nutné vyhodnotit přesnost a vyškolit model pro vaše případy použití. Mějte na paměti, že vylepšení v oblasti rozpoznávání budou stejně vhodná jako poskytnutá data. Z tohoto důvodu je důležité, aby se nahrály jenom přepisy s vysokou kvalitou.

Zvukové soubory mohou mít na začátku a na konci záznamu tiché ukončení. Pokud je to možné, zahrňte před a po řeči do každého ukázkového souboru alespoň poloviční sekundu tichého běhu. I když zvuk s nízkým objemem záznamů nebo hluku na pozadí není užitečný, neměli byste snížit svůj vlastní model. Před shromažďováním ukázek zvuku vždy zvažte upgrade mikrofonu a hardwaru pro zpracování signálu.

| Vlastnost                 | Hodnota                               |
|--------------------------|-------------------------------------|
| Formát souboru              | RIFF (WAV)                          |
| Vzorkovací frekvence              | 8 000 Hz nebo 16 000 Hz               |
| Kanály                 | 1 (mono)                            |
| Maximální délka na zvuk | 2 hodiny (testování)/60 s (školení) |
| Formát ukázky            | PCM, 16 bitů                         |
| Formát archivu           | .zip                                |
| Maximální velikost souboru ZIP         | 2 GB                                |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!NOTE]
> Při nahrávání školicích a testovacích dat nemůže být velikost souboru ZIP větší než 2 GB. Můžete provést test pouze z *jedné* datové sady, nezapomeňte ji zachovat v rámci příslušné velikosti souboru. Kromě toho každý školicí soubor nemůže být delší než 60 sekund, jinak dojde k chybě.

Aby bylo možné řešit problémy, jako je odstraňování nebo nahrazování slov, je nutné, aby se vylepšilo rozpoznávání dat s větším množstvím dat. Obecně se doporučuje zadat přepisy slova po slovech přibližně 10 až 20 hodin zvukového přenosu. Přepisy všech souborů WAV by měl obsahovat jediný soubor prostého textu. Každý řádek souboru s přepisem by měl obsahovat název jednoho zvukového souboru a za ním odpovídající přepis. Název souboru a přepis by měly být oddělené tabulátorem (\t).

  Například:
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```

> [!IMPORTANT]
> Přepis by měl být kódovaný ve formátu UTF-8 BOM (značka pořadí bajtů).

Přepisy se budou normalizovat, aby je mohl systém zpracovat. Existují však některé důležité normalizace, které je nutné provést před odesláním dat do sady Speech Studio. Příslušný jazyk, který se má použít, když připravujete přepisy, najdete v tématu [Vytvoření přepisu s popiskem](how-to-custom-speech-human-labeled-transcriptions.md) .

Až shromáždíte zvukové soubory a odpovídající přepisy, před nahráním na <a href="https://speech.microsoft.com/customspeech" target="_blank">portál <span class="docon docon-navigate-external x-hidden-focus"></span> Custom Speech </a>je zabalíte jako jeden soubor. zip. Níže je příklad datové sady se třemi zvukovými soubory a soubor přepisu s popisem:

> [!div class="mx-imgBorder"]
> ![Výběr zvuku z portálu pro rozpoznávání řeči](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

Seznam doporučených oblastí pro vaše předplatná služby Speech najdete v tématu [Nastavení účtu Azure](custom-speech-overview.md#set-up-your-azure-account) . Nastavení předplatných pro rozpoznávání řeči v jedné z těchto oblastí zkrátí dobu potřebnou k učení modelu.

## <a name="related-text-data-for-training"></a>Související textová data pro školení

Názvy produktů nebo funkce, které jsou jedinečné, by měly obsahovat související textová data pro školení. Související text pomáhá zajistit správné rozpoznávání. K dispozici jsou dva typy souvisejících textových dat pro zlepšení rozpoznávání:

| Datový typ | Jak tato data zlepšují rozpoznávání |
|-----------|------------------------------------|
| Věty (projevy) | Zvyšte přesnost při rozpoznávání názvů produktů nebo slovníku specifického pro konkrétní obor v kontextu věty. |
| Výslovnost | Zlepšení výslovnosti neobvyklých pojmů, akronymů nebo jiných slov pomocí nedefinovaných výslovnosti. |

Věty lze zadat jako jeden textový soubor nebo více textových souborů. Chcete-li zlepšit přesnost, používejte textová data, která se blíží očekávanému mluvenému projevy. Výslovnost by se měla zadat jako jediný textový soubor. Všechno se dá zabalit jako jeden soubor zip a nahrát na <a href="https://speech.microsoft.com/customspeech" target="_blank">Custom Speech portál <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.

### <a name="guidelines-to-create-a-sentences-file"></a>Pokyny k vytvoření souboru s větami

Pokud chcete vytvořit vlastní model pomocí vět, budete muset zadat seznam ukázek projevy. Projevy _nemusí_ být úplné nebo gramaticky správné, ale musí přesně odpovídat mluvenému vstupu, který očekáváte v produkčním prostředí. Pokud chcete, aby určité výrazy měly vyšší váhu, přidejte několik vět, které obsahují tyto konkrétní výrazy.

Vzhledem k obecným pokynům je přizpůsobení modelu nejúčinnější, když je školicí text co nejblíže k reálnému textu očekávanému v produkčním prostředí. Do školicího textu by měly být zahrnuty žargonu specifické pro doménu a fráze, jejichž cílem je vylepšit. Pokud je to možné, zkuste mít jednu větu nebo klíčové slovo pod samostatným řádkem. Pro klíčová slova a fráze, které jsou pro vás důležité (například názvy produktů), je můžete zkopírovat několikrát. Ale mějte na paměti, že nekopírují příliš mnoho – může to mít vliv na celkovou míru rozpoznávání.

Pomocí této tabulky zajistěte, aby byl správně naformátován váš související datový soubor pro projevy:

| Vlastnost | Hodnota |
|----------|-------|
| Kódování textu | UTF-8 BOM |
| Počet promluv na řádek | 1 |
| Maximální velikost souboru | 200 MB |

Navíc se budete chtít přihlédnout k následujícím omezením:

* Nepoužívejte opakující se znaky víckrát než čtyřikrát. Například: "AAAA" nebo "uuuu".
* Nepoužívejte speciální znaky ani znaky UTF-8 uvedené výše `U+00A1` .
* Identifikátory URI se odmítnou.

### <a name="guidelines-to-create-a-pronunciation-file"></a>Pokyny pro vytvoření souboru s výslovností

Pokud se nejedná o běžné výrazy bez standardních výslovností, které vaše uživatelé narazí nebo používají, můžete pro zlepšení rozpoznávání zadat vlastní soubor výslovnosti.

> [!IMPORTANT]
> Nedoporučujeme používat vlastní soubory výslovnosti ke změně výslovnosti běžných slov.

Zahrnuje příklady mluveného utterance a vlastní výslovnost pro každý z nich:

| Rozpoznaný/zobrazený formulář | Mluvený formulář |
|--------------|--------------------------|
| 3CPO | tři c. p o |
| CNTK | c n t k |
| Institut | i \ e |

Mluvený formulář je fonetická sekvence, která je vypsána. Může se skládat z písmen, slov, slabik nebo kombinace všech tří.

Přizpůsobená výslovnost je k dispozici v angličtině ( `en-US` ) a němčině ( `de-DE` ). V této tabulce jsou uvedeny podporované znaky podle jazyka:

| Jazyk | Národní prostředí | Znaky |
|----------|--------|------------|
| Angličtina | `en-US` | `a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |
| Němčina | `de-DE` | `ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |

Pomocí následující tabulky ověřte, zda je váš související datový soubor pro výslovnost ve správném formátu. Soubory výslovnosti jsou malé a měly by mít velikost jenom pár kilobajtů.

| Vlastnost | Hodnota |
|----------|-------|
| Kódování textu | BOM UTF-8 (ANSI je také podporováno pro angličtinu) |
| počet výslovnosti na řádek | 1 |
| Maximální velikost souboru | 1 MB (1 KB pro úroveň Free) |

## <a name="next-steps"></a>Další kroky

* [Kontrola dat](how-to-custom-speech-inspect-data.md)
* [Vyhodnocení dat](how-to-custom-speech-evaluate-data.md)
* [Trénování modelu](how-to-custom-speech-train-model.md)
* [Nasazení modelu](./how-to-custom-speech-train-model.md)