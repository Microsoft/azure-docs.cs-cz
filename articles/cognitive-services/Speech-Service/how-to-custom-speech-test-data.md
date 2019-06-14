---
title: Připravte testovací data pro vlastní řeč – hlasové služby
titlesuffix: Azure Cognitive Services
description: Určuje, zda testujete naleznete v tématu Jak přesné rozpoznávání řeči společnosti Microsoft je nebo trénování vlastních modelů, budete potřebovat dat (ve formě zvuk a/nebo text). Na této stránce si probereme typy dat, jak se používají a jejich správě.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 04da6b7a904bed8fa0990afe77b7fc81bec5a76e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67064029"
---
# <a name="prepare-data-for-custom-speech"></a>Příprava dat pro Custom Speech

Určuje, zda testujete naleznete v tématu Jak přesné rozpoznávání řeči společnosti Microsoft je nebo trénování vlastních modelů, budete potřebovat data ve formě zvuk a text. Na této stránce si probereme typy dat, jak se používají a jejich správě.

## <a name="data-types"></a>Typy dat

Tato tabulka shrnuje přijaté datové typy, pokud každý datový typ by měl použít a doporučené množství. Každý datový typ je potřeba vytvořit model. Požadavky na data se budou lišit v závislosti na tom, jestli jste vytvoření testu nebo trénování modelu.

| Typ dat | Použít testování | Množství | Používá pro školení | Množství |
|-----------|-----------------|----------|-------------------|----------|
| [Zvuk](#audio-data-for-testing) | Ano<br>Používá pro vizuální kontrolu | víc než 5 zvukové soubory | Ne | N/a |
| [Zvuk + lidských s názvem bez přípony záznamy o studiu](#audio--human-labeled-transcript-data-for-testingtraining) | Ano<br>Používá k vyhodnocení přesnost | 0,5 – 5 hodin zvuku | Ano | 1 – 1000 hodin zvuku |
| [Text související s](##related-text-data-for-training) | Ne | N/a | Ano | Text související s 1 200 MB |

Soubory by měly seskupené podle typu do datové sady a uložit jako soubor zip. Každá datová sada může obsahovat pouze jednoho datového typu.

## <a name="upload-data"></a>Nahrání dat

Až budete připravení nahrát data, klikněte na tlačítko **nahrání dat** ke spuštění průvodce a vytvořit první datovou sadu. Budete vyzváni k výběru řeči datový typ pro datovou sadu, před umožňuje nahrát data.

![Vyberte zvuk z portálu řeči](./media/custom-speech/custom-speech-select-audio.png)

Každou datovou sadu, které nahrajete, musí splňovat požadavky pro datový typ, který zvolíte. Je důležité správně formátovat data před odesláním. Tím se zajistí, že bude Custom Speech service přesně zpracovat data. Požadavky jsou uvedené v následujících částech.

Po odeslání vaší datové sadě máte několik možností:

* Můžete přejít **testování** kartu a vizuálně zkoumat jenom zvuk nebo zvukový + lidských s názvem bez přípony určené k transkripci data.
* Můžete přejít **školení** kartu a používat data přepisu zvuku + lidské nebo text související s k trénování vlastních modelů.

## <a name="audio-data-for-testing"></a>Zvuková data pro účely testování

Zvukových dat je ideální pro testování přesnost modelu řeči na text od Microsoftu směrného plánu nebo vlastního modelu. Mějte na paměti, zvukových dat se používá ke kontrole přesnost řeči s ohledem na výkon konkrétních modelů. Pokud potřebujete k vyčíslení přesnost modelu, použijte [přepisu zvuku + lidských s názvem bez přípony data](#audio--human-labeled-transcript-data-for-testingtraining).

Tato tabulka slouží k zajištění, že jsou správně naformátovaná zvukových souborů pro použití s Custom Speech:

| Vlastnost | Hodnota |
|----------|-------|
| Formát souboru | RIFF (WAV) |
| Vzorkovací frekvence | 8 000 jednotek Hz nebo 16 000 Hz |
| Kanály | 1 (mono) |
| Maximální délka na zvuk | 2 hodiny |
| Vzor formátu | PCM, 16-bit |
| Formát archivu | .zip |
| Maximální velikost | 2 GB |

Pokud vaše zvuk nebude splňuje tyto vlastnosti nebo chcete provést kontrolu, pokud nemá, doporučujeme stahování [sox](http://sox.sourceforge.net) zkontrolovat nebo převod zvuku. Níže jsou příklady jak každý z těchto činností lze provést prostřednictvím příkazového řádku:

| Aktivita | Popis | Příkaz SOx |
|----------|-------------|-------------|
| Zkontrolujte formát zvuku | Pomocí tohoto příkazu zkontrolujte formát zvukového souboru. | `sox --i <filename>` |
| Převést formát zvuku | Tento příkaz slouží k zvukový soubor převést na jeden kanál, 16 bitů 16 KHz. | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>Data přepisu zvuku + lidských s názvem bez přípony pro testování a školení

K měření přesnost přesnost řeči na text od Microsoftu, při zpracování zvukové soubory, je nutné zadat lidských s názvem bez přípony přepisů (word po slovech) pro porovnání. Přepis lidských s názvem bez přípony je často časově náročné, je nezbytné k vyhodnocení, přesnost a pro trénování modelu pro vaše případy použití. Mějte na paměti, vylepšení v rozpoznávání bude jenom tak dobré jako data k dispozici. Z tohoto důvodu je důležité, že se nahrají pouze vysoce kvalitní záznamy o studiu.  

| Vlastnost | Hodnota |
|----------|-------|
| Formát souboru | RIFF (WAV) |
| Vzorkovací frekvence | 8 000 jednotek Hz nebo 16 000 Hz |
| Kanály | 1 (mono) |
| Maximální délka na zvuk | 60 s |
| Vzor formátu | PCM, 16-bit |
| Formát archivu | .zip |
| Zip maximální velikost | 2 GB |

Pro řešení potíží s jako word odstranění nebo nahrazení, významné množství dat se vyžaduje pro zlepšení rozpoznávání. Obecně se doporučuje zadejte slovo slovo přepisů zhruba 10 až 1 000 hodin zvukového záznamu. Přepisy všech souborů WAV by měl obsahovat jediný soubor prostého textu. Každý řádek souboru s přepisem by měl obsahovat název jednoho zvukového souboru a za ním odpovídající přepis. Název souboru a přepis by měly být oddělené tabulátorem (\t).

  Příklad:
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```
> [!NOTE]
> Přepis by měl být kódovaný ve formátu UTF-8 BOM (značka pořadí bajtů).

Přepisy se budou normalizovat, aby je mohl systém zpracovat. Existuje však několik důležitých normalizací, které musí uživatel provést _před_ nahráním dat do služby Custom Speech Service. Příslušný jazyk pro použití při přípravě vašeho přepisů, naleznete v tématu [vytvoření přepisu se lidských s názvem bez přípony](how-to-custom-speech-human-labeled-transcriptions.md)

Po informací jsme shromáždili zvukové soubory a odpovídající přepisů, se má zabalit jako jednoho souboru .zip před nahráním na portál Custom Speech. Toto je objekt dataset příkladu tři zvukové soubory a přepis lidských s názvem bez přípony souboru:

![Vyberte zvuk z portálu řeči](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

## <a name="related-text-data-for-training"></a>Související textových dat pro školení

Pokud máte názvy produktů nebo funkce, které jsou jedinečné a chcete, abyste měli jistotu, že jsou správně rozpoznány, je důležité zahrnout související textových dat pro vzdělávání. Je možné poskytnout dva druhy dat text související s zlepšení rozpoznávání:

| Typ dat | Jak tato data zlepšuje rozpoznávání |
|-----------|------------------------------------|
| Projevy a/nebo vět | Při rozpoznávání názvy produktů nebo slovníku konkrétní obory můžete využít v rámci kontextu větu tyto můžete zlepšit přesnost. |
| Výslovnosti | Toto může zlepšit výslovnost neobvyklé podmínky, zkratky nebo jiná slova s nedefinované výslovnosti. |

Projevy lze zadat jako jeden nebo více textových souborů. Čím blíž text, který data jsou na co se říká, tím větší pravděpodobnost, že je zlepšit přesnost. Výslovnosti by měl být ve formě jednoho textového souboru. Všechno, co lze zabalit jako soubor zip jeden a nahráli na portál Custom Speech.

### <a name="guidelines-to-create-an-utterances-file"></a>Pokyny k vytvoření souboru projevy

Pokud chcete vytvořit vlastní model s použitím text související s, budete muset zadat seznam projevy vzorku. Tyto projevy nemusíte být úplných větách nebo gramaticky opravit, ale musí přesně odpovídat mluvené slovo vstup, který očekáváte, že v produkčním prostředí. Pokud chcete větší váhu určité podmínky, můžete přidat několik věty do souboru související data, která zahrnují tyto konkrétní podmínky.

Tato tabulka slouží k zajištění správný formát souboru souvisejících dat pro projevy:

| Vlastnost | Hodnota |
|----------|-------|
| Kódování textu | UTF-8 BOM |
| Počet promluv na řádek | 1 |
| Maximální velikost souboru | 200 MB |

Kromě toho je vhodné pro následující omezení:

* Vyhněte se opakujících se znaků více než čtyři časy. Příklad: "aaaa" nebo "uuuu".
* Nepoužívejte speciální znaky ani znaky UTF-8 výše U + 00A1.
* Identifikátory URI budou odmítnuty.

### <a name="guidelines-to-create-a-pronunciation-file"></a>Pokyny k vytvoření souboru výslovnost

Pokud jsou obvyklé podmínky bez standardní výslovnosti, které vaši uživatelé budou dojde k nebo použít, můžete zadat vlastní výslovnost souboru pro zlepšení rozpoznávání.

> [!IMPORTANT]
> Nedoporučujeme tuto funkci použít ke změně výslovnost běžná slova.

To zahrnuje příklady mluvené slovo utterance a vlastní výslovnosti pro každou:

| Mluvené slovo formuláře | Nerozpoznal/zobrazí formulář |
|--------------|--------------------------|
| o tři c p | 3CPO |  
| k c n t | CNTK |
| i ztrojnásobit e | IEEE |

Mluvené slovo formuláře je zapsané ve fonetické sekvence států. To může být tvořen písmeno, slova, slabik nebo s použitím kombinace všech tří.

Přizpůsobené Výslovnost je k dispozici v angličtině (en US) a němčina (de-DE). Tato tabulka zobrazuje podporované znaky v jazyce:

| Jazyk | Národní prostředí | Postavy |
|----------|--------|------------|
| Angličtina | en-US | a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z |
| Němčina | de-DE | ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z |

Tato tabulka slouží k zajištění, že související datový soubor pro výslovnosti správný formát. Výslovnost soubory jsou malé a by neměl překročit několik znalostní báze.

| Vlastnost | Hodnota |
|----------|-------|
| Kódování textu | BOM kódování UTF-8 (ANSI je také podporována pro angličtina) |
| počet výslovnosti na řádek | 1 |
| Maximální velikost souboru | 1 MB (1 KB pro úroveň free) |

## <a name="next-steps"></a>Další postup

* [Kontrola dat](how-to-custom-speech-inspect-data.md)
* [Vyhodnocení vašich dat](how-to-custom-speech-evaluate-data.md)
* [Vyzkoušejte svůj model](how-to-custom-speech-train-model.md)
* [Nasazení modelu](how-to-custom-speech-deploy-model.md)
