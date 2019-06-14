---
title: Jak připravit data pro vlastní hlasové funkce – hlasové služby
titlesuffix: Azure Cognitive Services
description: Vytvořte vlastní hlas pro hodnotu značky s hlasové služby Azure. Zadejte studio záznamy a související skripty, služba generuje jedinečný hlas modelu vyladěné tak, aby zaznamenané hlasu. Používejte Tento hlas tak, aby odpovídaly řeči v produktů, nástrojů a aplikací.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: erhopf
ms.openlocfilehash: 23ba45804632163cc5de41ac2353091b7cf850c1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063188"
---
# <a name="prepare-data-to-create-a-custom-voice"></a>Příprava dat k vytvoření vlastní hlasový vstup

Jakmile budete připraveni vytvořit vlastní řeč pro vaši aplikaci, prvním krokem je ke shromažďování, zvukové záznamy a související skripty spustit trénování modelu hlasu. Služba používá tato data k vytvoření jedinečný hlas optimalizovaná tak, aby odpovídaly plaformu záznamy. Po natrénovali hlasu zahájením syntetizační řeči ve svých aplikacích.

Můžete začít s menším objemem dat k vytvoření testování konceptu. Nicméně Čím více dat, které poskytujete, další přirozeného vlastní hlasové se zvuk. Předtím, než můžete trénování modelu řeč, budete potřebovat, zvukové záznamy a přepisy přidružený text. Na této stránce si probereme, datových typů, jak se používají a jak spravovat každý.

## <a name="data-types"></a>Typy dat

Hlasové trénovací datové sady obsahuje zvukové záznamy a textový soubor s přidružené přepisů. Každý zvukový soubor by měl obsahovat jeden utterance (jedné věty nebo jedné zapnout pro dialogové okno systém) a musí být méně než 15 sekund.

V některých případech nemusí mít správné datová sada připravená a bude chcete otestovat vlastní hlasové školení k dispozici zvukové soubory, kratší nebo delší, s nebo bez něj záznamy o studiu. Poskytujeme nástroje (beta verze), které pomáhají rozdělit zvuku do projevy a připravit je pomocí záznamy o studiu [rozhraní API služby Batch určené k transkripci](batch-transcription.md).

Tato tabulka shrnuje datové typy a jak každý slouží k vytvoření vlastní řeč modelu.

| Typ dat | Popis | Kdy je použít | Vyžaduje další služby | Množství pro trénování modelu | Locale(s) |
| --------- | ----------- | ----------- | --------------------------- | ----------------------------- | --------- |
| **Jednotlivé projevy + odpovídající přepisu** | Soubor (ZIP) zvukové soubory (.wav) jako jednotlivé projevy. Každý zvukový soubor by měl být 15 sekund nebo méně, spárovaná s formátovaný přepisu (.txt). | Profesionální záznamy s odpovídajícími záznamy o studiu | Připraveno pro vzdělávání. | Žádné striktní požadavek pro en US a zh-CN. Více než 2 000 + odlišné projevy pro jiné národní prostředí. | Všechny vlastní hlasové národních prostředí |
| **Dlouhé zvuk + přepisu (beta verze)** | Soubor (ZIP) dlouhý, nesegmentovaný zvukové soubory (delší než 20 sekund), spárovaná s přepisu (.txt), který obsahuje všechny mluveného slova. | Zvukové soubory a odpovídající záznamy o studiu, ale nejsou segmentovat do projevy. | Segmentace (s použitím určené k transkripci batch).<br>Zvukový formát transformace vyžadováno. | Žádné striktní požadavek pro en US a zh-CN. | `en-US` a `zh-CN` |
| **Pouze zvuku (beta verze)** | Soubor (ZIP) zvukové soubory bez řádné záznamy o studiu. | Stačí zvukové soubory, které jsou k dispozici bez záznamy o studiu. | Segmentace + generování přepisu (pomocí služby batch určené k transkripci).<br>Zvukový formát transformace vyžadováno.| Žádné striktní požadavek pro `en-US` a `zh-CN`. | `en-US` a `zh-CN` |

Soubory by měly seskupené podle typu do datové sady a uložit jako soubor zip. Každá datová sada může obsahovat pouze jednoho datového typu.

> [!NOTE]
> Maximální počet datových sadách povoleno importovat na jedno předplatné je 10 zip soubory zdarma uživatelé předplatného (F0) a 500 uživatelů standardní předplatné (S0).

## <a name="individual-utterances--matching-transcript"></a>Jednotlivé projevy + odpovídající přepisu

Můžete připravit záznamů jednotlivých projevy a odpovídající přepisu dvěma způsoby. Buď napsat skript a jeho číst talentu hlasu nebo použít veřejně dostupné zvuk a přepisy na text. Pokud tak učiníte ten, upravte disfluencies z zvukové soubory, jako je například "um" a dalších přednastavené zvuky, zadrhává, mumbled slova nebo mispronunciations.

K vytvoření dobré hlasového písma, vytvořte záznamy v tichém místnosti s vysoce kvalitní mikrofon. Konzistentní svazku mluvený míra mluvený rozteč a expresivní zkoušky prezentace řeči je nezbytné.

> [!TIP]
> Pokud chcete vytvořit hlasový vstup pro použití v produkčním prostředí, doporučujeme že použít profesionální záznam studio a způsobu vyjadřování talentu. Další informace najdete v tématu [jak záznam hlasu ukázky pro vlastní hlasové](record-custom-voice-samples.md).

### <a name="audio-files"></a>Zvukové soubory

Každý zvukový soubor by měl obsahovat jeden utterance (jedné věty nebo jedné zapnout dialogové okno systému), méně než 15 sekund. Všechny soubory musí být ve stejném jazyce mluvené slovo. Vlastní převod textu na řeč hlasy vícejazykového nejsou podporovány, s výjimkou bi čínština – angličtina – jazycích. Zvukové soubory, musí mít jedinečný číselný název souboru příponu názvu souboru.

Při přípravě zvuk, postupujte podle následujících pokynů.

| Vlastnost | Hodnota |
| -------- | ----- |
| Formát souboru | RIFF (.wav) seskupeny do souboru ZIP |
| Vzorkovací frekvence | aspoň 16 000 Hz |
| Vzor formátu | PCM, 16-bit |
| Název souboru | Číselné literály, s rozšířením WAV. Žádné duplicitní názvy povolené. |
| Délka zvuku | Kratší než 15 sekund |
| Formát archivu | .zip |
| Maximální velikost | 200 MB |

> [!NOTE]
> soubory WAV pomocí vzorkovací frekvenci nižší než 16 000 Hz budou odmítnuty. Pokud soubor ZIP obsahuje soubory .wav s jinou vzorkovací frekvence, budou importovány pouze ty rovná nebo je vyšší než 16 000 Hz. Portálu aktuálně importy ZIP archivuje až 200 MB. Však můžete nahrát několik archivů.

### <a name="transcripts"></a>Záznamy o studiu

Přepis souboru je textový soubor. Tyto pokyny slouží k přípravě vašeho přepisů.

| Vlastnost | Hodnota |
| -------- | ----- |
| Formát souboru | Prostý text (TXT) |
| Formát kódování | ANSI/ASCII, UTF-8, UTF-8-BOM, UTF-16-LE nebo být UTF-16. Zh-CN nepodporují kódování ANSI/ASCII a UTF-8. |
| Počet promluv na řádek | **Jeden** – každý řádek v souboru určené k transkripci by měl obsahovat název zvukové soubory, za nímž následuje odpovídající přepis. Název souboru a přepis by měly být oddělené tabulátorem (\t). |
| Maximální velikost souboru | 50 MB |

Níže je příklad, jak záznamy o studiu jsou uspořádány utterance podle utterance v jednom souboru .txt:

```
0000000001[tab] This is the waistline, and it's falling.
0000000002[tab] We have trouble scoring.
0000000003[tab] It was Janet Maslin.
```
Je důležité, že záznamy o studiu jsou 100 % přesných přepisů odpovídající zvuku. Chyby v záznamy o studiu představí ztrátu kvality při výuce.

> [!TIP]
> Při vývoji produkčních převod textu na řeč hlasů, vyberte projevy (nebo skripty) vyžadující do účtu zapsané ve fonetické pokrytí a efektivitu. Máte potíže při načítání výsledků požadujete? [Obraťte se na vlastní hlasové](mailto:speechsupport@microsoft.com) týmu zjištění najdete další informace o s námi.

## <a name="long-audio--transcript-beta"></a>Dlouhé zvuk + přepisu (beta verze)

V některých případech nemusí mít segmentované zvuk, které jsou k dispozici. Poskytujeme prostřednictvím portálu pro vlastní hlasové můžete segmentovat dlouho zvukové soubory a vytvořte přepisů service (beta verze). Zachovat v paměti, tato služba bude účtovat směrem k využití předplatného speech to text.

> [!NOTE]
> Segmentace dlouho zvuk služby bude využívat funkci batch určené k transkripci řeči na text, který podporuje pouze standardní předplatné (S0) uživatelů. Během zpracování segmentace, zvukové soubory a záznamy o studiu se také odešlou do služby Custom Speech service pro upřesnění model rozpoznávání, aby se dalo zlepšit přesnost pro vaše data. Během tohoto procesu bude zachovávat žádná data. Po dokončení segmentace pouze projevy segmentované a jejich mapování záznamy o studiu se uloží pro stahování a školení.

### <a name="audio-files"></a>Zvukové soubory

Při přípravě zvuk segmentace, postupujte podle následujících pokynů.

| Vlastnost | Hodnota |
| -------- | ----- |
| Formát souboru | RIFF (.wav) s vzorkovací frekvenci aspoň 16 khz – 16 bitů v PCM nebo .mp3 s přenosovou rychlostí aspoň 256 kb/s, seskupených do souboru ZIP |
| Název souboru | Pouze znaky ASCII. Selže, znaky Unicode v názvu (například čínské znaky, nebo symboly, jako je "-"). Žádné duplicitní názvy povolené. |
| Délka zvuku | Déle než 20 sekund. |
| Formát archivu | .zip |
| Maximální velikost | 200 MB |

Všechny zvukové soubory by měly být seskupeny do souboru zip. Je možné vložit do jeden zip zvuku WAV soubory a soubory MP3, ale žádné podsložky je povolen v souboru zip. Například můžete nahrát soubor zip obsahující zvukový soubor s názvem "kingstory.wav", 45. sekundu dlouho a jiné jeden s názvem "queenstory.mp3", 200sekundu – long, bez jakékoli podsložek. Všechny soubory mp3 se bude transformovat do formátu WAV po zpracování.

### <a name="transcripts"></a>Záznamy o studiu

Záznamy o studiu, musí být připravené na specifikacích uvedených v této tabulce. Zvukové soubory, musí mít odpovídající řádné záznamy o studiu.

| Vlastnost | Hodnota |
| -------- | ----- |
| Formát souboru | Prostý text (TXT) seskupeny do soubor .zip |
| Název souboru | Použijte stejný název jako odpovídající zvukový soubor |
| Formát kódování | UTF-8-BOM pouze |
| Počet promluv na řádek | Bez omezení |
| Maximální velikost souboru | 50 MILIONŮ |

Všechny záznamy o studiu soubory v tomto typu dat by měl být seskupeny do souboru zip. V souboru zip je povolené žádné podsložky. Například jste nahráli soubor zip obsahující zvukový soubor s názvem "kingstory.wav" 45 sekund a jiné jeden s názvem "queenstory.mp3", 200 sekund dlouho. Je potřeba nahrát jiný soubor zip obsahující dva záznamy o studiu, jednu s názvem "kingstory.txt", v jiných jeden "queenstory.txt". V rámci každého souboru ve formátu prostého textu bude poskytovat úplné správné přepis pro odpovídající zvuk.

Po úspěšném odeslání vaší datové sadě vám pomůžeme rozdělit zvukový soubor do projevy založené na přepisu k dispozici. Stáhněte si datové sady můžete zkontrolovat segmentovaným projevy a odpovídající záznamy o studiu. Jedinečné ID se automaticky přiřadí segmentovaným projevy. Je důležité, aby záznamy o studiu, které poskytnete jsou 100 % přesností. Chyby v záznamy o studiu může snížit přesnost během zvuku segmentace a další zavést ztrátu kvality ve fázi školení, která se dodává později.

## <a name="audio-only-beta"></a>Pouze zvuku (beta verze)

Pokud nemáte přepisy pro zvukové záznamy, použít **jenom zvuk** možnost odeslat data. Náš systém můžete segmentovat a přepisy zvukové soubory. Mějte na paměti, tato služba se bude počítat směrem k využití předplatného speech to text.

Při přípravě zvuk, postupujte podle následujících pokynů.

> [!NOTE]
> Segmentace dlouho zvuk služby bude využívat funkci batch určené k transkripci řeči na text, který podporuje pouze standardní předplatné (S0) uživatelů.

| Vlastnost | Hodnota |
| -------- | ----- |
| Formát souboru | RIFF (.wav) s vzorkovací frekvenci aspoň 16 khz – 16 bitů v PCM nebo .mp3 s přenosovou rychlostí aspoň 256 kb/s, seskupených do souboru ZIP |
| Název souboru | Pouze znaky ASCII. Selže, znaky Unicode v názvu (například čínské znaky, nebo symboly, jako je "-"). Žádné duplicitní název povolen. |
| Délka zvuku | Déle než 20 sekund. |
| Formát archivu | .zip |
| Maximální velikost | 200 MB |

Všechny zvukové soubory by měly být seskupeny do souboru zip. V souboru zip je povolené žádné podsložky. Po úspěšném odeslání vaší datové sadě vám pomůžeme rozdělit zvukový soubor do projevy podle našich služba batch určené k transkripci řeči. Jedinečné ID se automaticky přiřadí segmentovaným projevy. Odpovídající záznamy o studiu bude vygenerován pomocí rozpoznávání řeči. Všechny soubory mp3 se bude transformovat do formátu WAV po zpracování. Stáhněte si datové sady můžete zkontrolovat segmentovaným projevy a odpovídající záznamy o studiu.

## <a name="next-steps"></a>Další postup

- [Vytvořit vlastní hlasový vstup](how-to-custom-voice-create-voice.md)
- [Průvodce: Záznam vašich ukázek hlasu](record-custom-voice-samples.md)
