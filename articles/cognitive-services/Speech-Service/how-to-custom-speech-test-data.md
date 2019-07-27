---
title: Příprava testovacích dat pro službu Custom Speech-Speech
titleSuffix: Azure Cognitive Services
description: Bez ohledu na to, jestli testujete, abyste zjistili, jak přesné rozpoznávání řeči Microsoftu je nebo školení vašich vlastních modelů, budete potřebovat data (ve formě zvukového a/nebo textu). Na této stránce se zabýváme typy dat, způsob jejich použití a způsob jejich správy.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 8821ce46c65ac8bca36f006ef77bcaf475b0573d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559610"
---
# <a name="prepare-data-for-custom-speech"></a>Příprava dat pro Custom Speech

Bez ohledu na to, jestli testujete, abyste zjistili, jak přesné rozpoznávání řeči Microsoftu je nebo školení vašich vlastních modelů, budete potřebovat data ve formě zvuku a textu. Na této stránce se zabýváme typy dat, způsob jejich použití a způsob jejich správy.

## <a name="data-types"></a>Typy dat

Tato tabulka obsahuje seznam povolených datových typů, kdy se má použít každý datový typ a doporučené množství. Pro vytvoření modelu není vyžadován každý datový typ. Požadavky na data se budou lišit v závislosti na tom, zda vytváříte testovací nebo školicí model.

| Datový typ | Použito testování | Množství | Používá se pro školení. | Množství |
|-----------|-----------------|----------|-------------------|----------|
| [Zvuk](#audio-data-for-testing) | Ano<br>Použito pro vizuální kontrolu | 5 zvukových souborů | Ne | N/a |
| [Audio + přepisy s popiskem](#audio--human-labeled-transcript-data-for-testingtraining) | Ano<br>Používá se k vyhodnocení přesnosti. | 0,5 – 5 hodin zvukového přenosu | Ano | 1 – 1 000 hodin zvukového přenosu |
| [Související text](##related-text-data-for-training) | Ne | N/a | Ano | 1-200 MB souvisejícího textu |

Soubory by měly být seskupené podle typu do datové sady a nahrané jako soubor zip. Každá datová sada může obsahovat pouze jeden datový typ.

## <a name="upload-data"></a>Nahrání dat

Až budete připraveni k nahrávání dat, klikněte na **nahrát data** a spusťte průvodce a vytvořte svou první datovou sadu. Před tím, než budete moci odeslat data, budete požádáni o výběr datového typu řeči pro datovou sadu.

![Výběr zvuku z portálu pro rozpoznávání řeči](./media/custom-speech/custom-speech-select-audio.png)

Každá datová sada, kterou nahráváte, musí splňovat požadavky na datový typ, který zvolíte. Před nahráním dat je důležité data správně naformátovat. Tím se zajistí, že data budou přes službu Custom Speech přesně zpracována. Požadavky jsou uvedené v následujících oddílech.

Po nahrání datové sady máte několik možností:

* Můžete přejít na kartu **testování** a vizuálně zkontrolovat jenom zvuk nebo zvuk + přepisně pojmenovaných dat.
* Můžete přejít na kartu **školení** a použít zvuk a data o přepisu, nebo související textová data, a využít tak vlastní model.

## <a name="audio-data-for-testing"></a>Zvuková data pro testování

Zvuková data jsou ideální pro testování přesnosti základního rozpoznávání řeči a textu od společnosti Microsoft nebo vlastního modelu. Mějte na paměti, že zvuková data se používají ke kontrole přesnosti řeči s ohledem na výkon konkrétního modelu. Pokud hledáte přesnost modelu, použijte [zvuk + přepisně označené data](#audio--human-labeled-transcript-data-for-testingtraining).

Pomocí této tabulky zajistěte, aby byly vaše zvukové soubory správně formátované pro použití s Custom Speech:

| Vlastnost | Hodnota |
|----------|-------|
| Formát souboru | RIFF (WAV) |
| Vzorkovací frekvence | 8 000 Hz nebo 16 000 Hz |
| Kanály | 1 (mono) |
| Maximální délka na zvuk | 2 hodiny |
| Vzorový formát | PCM, 16 bitů |
| Formát archivu | .zip |
| Maximální velikost archivu | 2 GB |

Pokud Váš zvuk nevyhovuje těmto vlastnostem nebo chcete zjistit, jestli to není, doporučujeme, abyste si stáhli [Sox](http://sox.sourceforge.net) a zkontrolovali nebo převedli zvuk. Níže jsou uvedeny některé příklady, jak lze jednotlivé aktivity provést prostřednictvím příkazového řádku:

| Aktivita | Popis | Sox – příkaz |
|----------|-------------|-------------|
| Kontrolovat zvukový formát | Pomocí tohoto příkazu můžete kontrolovat formát zvukového souboru. | `sox --i <filename>` |
| Převod zvukového formátu | Tento příkaz slouží k převodu zvukového souboru na jeden kanál, 16., 16 KHz. | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>Audio + přepisované údaje přepisu pro testování/školení

Chcete-li změřit přesnost přesnosti řeči na text od společnosti Microsoft při zpracování zvukových souborů, je nutné zadat přepisy (Word-by-Word) pro porovnání. I když je přepis uživatelsky popisku často časově náročný, je nutné vyhodnotit přesnost a vyškolit model pro vaše případy použití. Mějte na paměti, že vylepšení v oblasti rozpoznávání budou stejně vhodná jako poskytnutá data. Z tohoto důvodu je důležité, aby se nahrály jenom přepisy s vysokou kvalitou.  

| Vlastnost | Value |
|----------|-------|
| Formát souboru | RIFF (WAV) |
| Vzorkovací frekvence | 8 000 Hz nebo 16 000 Hz |
| Kanály | 1 (mono) |
| Maximální délka na zvuk | 60 s |
| Vzorový formát | PCM, 16 bitů |
| Formát archivu | .zip |
| Maximální velikost souboru ZIP | 2 GB |

Aby bylo možné řešit problémy, jako je odstraňování nebo nahrazování slov, je nutné, aby se vylepšilo rozpoznávání dat s větším množstvím dat. Obecně se doporučuje zadat přepisy slova po slovech přibližně 10 až 1 000 hodin zvukového přenosu. Přepisy všech souborů WAV by měl obsahovat jediný soubor prostého textu. Každý řádek souboru s přepisem by měl obsahovat název jednoho zvukového souboru a za ním odpovídající přepis. Název souboru a přepis by měly být oddělené tabulátorem (\t).

  Příklad:
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```
> [!NOTE]
> Přepis by měl být kódovaný ve formátu UTF-8 BOM (značka pořadí bajtů).

Přepisy se budou normalizovat, aby je mohl systém zpracovat. Existuje však několik důležitých normalizací, které musí uživatel provést _před_ nahráním dat do služby Custom Speech Service. Příslušný jazyk, který se má použít, když připravujete přepisy, najdete v tématu [Vytvoření přepisu](how-to-custom-speech-human-labeled-transcriptions.md) s popiskem.

Až shromáždíte zvukové soubory a odpovídající přepisy, měli byste je před nahráním na portál Custom Speech zabalit jako jeden soubor. zip. Toto je ukázková datová sada se třemi zvukovými soubory a soubor přepisu s popiskem:

![Výběr zvuku z portálu pro rozpoznávání řeči](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

## <a name="related-text-data-for-training"></a>Související textová data pro školení

Pokud máte názvy produktů nebo funkce, které jsou jedinečné, a chcete se ujistit, že jsou správně rozpoznané, je důležité zahrnout související textová data pro školení. K dispozici jsou dva typy souvisejících textových dat pro zlepšení rozpoznávání:

| Datový typ | Jak tato data zlepšují rozpoznávání |
|-----------|------------------------------------|
| Projevy a/nebo věty | Ty můžou zlepšit přesnost při rozpoznávání názvů produktů nebo slovníku specifického pro konkrétní obor v kontextu věty. |
| Výslovnost | Ty můžou zlepšit výslovnost neobvyklých pojmů, akronymů nebo jiných slov pomocí nedefinovaných výslovnosti. |

Projevy lze zadat jako jeden nebo více textových souborů. Čím blíže jsou textová data, která se budou přecházet, tím větší je pravděpodobnost zvýšení přesnosti. Výslovnost by se měla zadat jako jediný textový soubor. Všechno se dá zabalit jako jeden soubor zip a nahrát na Custom Speech portál.

### <a name="guidelines-to-create-an-utterances-file"></a>Pokyny k vytvoření souboru projevy

Pokud chcete vytvořit vlastní model pomocí souvisejícího textu, budete muset zadat seznam ukázek projevy. Tyto projevy nemusí být doplňovat věty nebo gramaticky správné, ale musí přesně odrážet mluvený vstup, který očekáváte v produkci. Pokud chcete, aby určité výrazy měly vyšší váhu, můžete do svého souvisejícího datového souboru přidat několik vět, které obsahují tyto konkrétní výrazy.

Pomocí této tabulky zajistěte, aby byl správně naformátován váš související datový soubor pro projevy:

| Vlastnost | Hodnota |
|----------|-------|
| Kódování textu | UTF-8 BOM |
| Počet promluv na řádek | 1 |
| Maximální velikost souboru | 200 MB |

Navíc se budete chtít přihlédnout k následujícím omezením:

* Nepoužívejte opakující se znaky víckrát než čtyřikrát. Například: "AAAA" nebo "uuuu".
* Nepoužívejte speciální znaky ani znaky UTF-8 nad U + 00A1.
* Identifikátory URI se odmítnou.

### <a name="guidelines-to-create-a-pronunciation-file"></a>Pokyny pro vytvoření souboru s výslovností

Pokud se nejedná o běžné výrazy bez standardních výslovností, které vaše uživatelé narazí nebo používají, můžete pro zlepšení rozpoznávání zadat vlastní soubor výslovnosti.

> [!IMPORTANT]
> Tato funkce se nedoporučuje používat ke změně výslovnosti běžných slov.

Zahrnuje příklady mluveného utterance a vlastní výslovnost pro každý z nich:

| Rozpoznaný/zobrazený formulář | Mluvené slovo formuláře |
|--------------|--------------------------|
| 3CPO | tři c. p o |  
| CNTK | c n t k |
| INSTITUT | i \ e |

Mluvený formulář je fonetická sekvence, která je vypsána. Může se skládat z písmen, slov, slabik nebo kombinace všech tří.

Přizpůsobená výslovnost je k dispozici v angličtině (EN-US) a v němčině (de-DE). V této tabulce jsou uvedeny podporované znaky podle jazyka:

| Jazyk | Národní prostředí | Postavy |
|----------|--------|------------|
| Angličtina | en-US | a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z |
| Němčina | de-DE | ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z |

Pomocí této tabulky zajistěte, aby váš související datový soubor pro výslovnost byl správně naformátován. Soubory výslovnosti jsou malé a neměly by překročit několik aktualizací KB.

| Vlastnost | Hodnota |
|----------|-------|
| Kódování textu | BOM UTF-8 (ANSI je také podporováno pro angličtinu) |
| počet výslovnosti na řádek | 1 |
| Maximální velikost souboru | 1 MB (1 KB pro úroveň Free) |

## <a name="next-steps"></a>Další postup

* [Kontrola dat](how-to-custom-speech-inspect-data.md)
* [Vyhodnocení dat](how-to-custom-speech-evaluate-data.md)
* [Výuka modelu](how-to-custom-speech-train-model.md)
* [Nasazení modelu](how-to-custom-speech-deploy-model.md)
