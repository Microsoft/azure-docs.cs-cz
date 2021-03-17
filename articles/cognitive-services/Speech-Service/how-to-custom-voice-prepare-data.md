---
title: Příprava dat pro vlastní službu hlasového rozpoznávání řeči
titleSuffix: Azure Cognitive Services
description: Pomocí služby Speech vytvořte vlastní hlas pro vaši značku. Zadáváte nahrávky studia a přidružené skripty, služba generuje jedinečný hlasový model vyladěný na zaznamenaný hlas. Využijte tento hlas k syntetizaci řeči ve svých produktech, nástrojích a aplikacích.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: 22db0006e6abb38439a2db46584559dcd9ce3f3a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101722360"
---
# <a name="prepare-data-to-create-a-custom-voice"></a>Příprava dat pro vytvoření vlastního hlasu

Až budete připraveni vytvořit vlastní hlas pro převod textu na řeč, je prvním krokem shromáždění zvukového záznamu a přidružených skriptů pro zahájení školení v hlasovém modelu. Služba Speech používá tato data k vytvoření jedinečného hlasu optimalizovaného pro vyhledání hlasu v záznamech. Po školení hlasu můžete v aplikacích začít syntetizovat řeč.

Než budete moct naučit vlastní hlasový model pro převod textu na řeč, budete potřebovat zvukové nahrávky a související text. Na této stránce zkontrolujeme typy dat, způsob jejich použití a způsob jejich správy.

> [!NOTE]
> Pokud chcete naučit neuronové hlas, musíte zadat profil hlasového talentů se souborem s vyjádřením informací o zvuku, který je k dispozici v hlasovém talentůu, abyste mohli využít jeho data o řeči k učení vlastního hlasového modelu. Při přípravě skriptu nahrávání se ujistěte, že jste zahrnuli níže uvedenou větu. 

> "I [stav vašeho jména a příjmení] si uvědomte, že nahrávky mého hlasu budou použity uživatelem [State název společnosti] k vytvoření a použití syntetické verze mého hlasu."
Tato věta se použije k ověření, jestli se školicí data provádějí stejnou osobou, která tento souhlas provede. Tady si můžete přečíst další informace o [ověřování hlasových talentů](/legal/cognitive-services/speech-service/custom-neural-voice/data-privacy-security-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) .

> Vlastní neuronové hlas je k dispozici s omezeným přístupem. Ujistěte se, že rozumíte [požadavkům na AI](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) a [použijete přístup tady](https://aka.ms/customneural). 

## <a name="data-types"></a>Typy dat

Datová sada pro hlasové školení obsahuje zvukové nahrávky a textový soubor s přidruženými přepisy. Každý zvukový soubor by měl obsahovat jednu utterance (jednu větu nebo jednu jako systém dialogového okna) a musí být kratší než 15 sekund.

V některých případech nemusíte mít správnou datovou sadu, která je připravená a bude chtít testovat vlastní hlasové školení s dostupnými zvukovými soubory, které jsou krátké i dlouhé, s přepisy nebo bez nich. Poskytujeme nástroje (beta), které vám pomůžou rozdělit zvuk do projevy a připravit přepisy pomocí [rozhraní API služby Batch pro přepis](batch-transcription.md).

Tato tabulka obsahuje seznam datových typů a jejich využití k vytvoření vlastního hlasového modelu pro převod textu na řeč.

| Datový typ | Popis | Kdy je použít | Vyžaduje se další zpracování. | 
| --------- | ----------- | ----------- | --------------------------- |
| **Individuální projevy + vyhovující přepis** | Kolekce (. zip) zvukových souborů (. wav) jako samostatného projevy. Každý zvukový soubor by měl mít délku 15 sekund nebo méně, spárováno s formátovaným přepisem (. txt). | Profesionální nahrávky s vyhovujícími Přepisy | Připraveno pro školení. |
| **Dlouhý zvuk + přepis (beta verze)** | Kolekce (ZIP) dlouhých, nesegmentované zvukové soubory (delší než 20 sekund) spárované s přepisem (. txt), který obsahuje všechna mluvený text. | Máte zvukové soubory a vyhovující přepisy, ale nesegmentují se na projevy. | Segmentace (pomocí dávkového přepisu).<br>V případě potřeby transformuje formát zvuku. | 
| **Jenom zvuk (beta verze)** | Kolekce (. zip) zvukových souborů bez přepisu. | Máte k dispozici pouze zvukové soubory bez přepisů. | Segmentace a generování přepisu (pomocí dávkového přepisu).<br>V případě potřeby transformuje formát zvuku.| 

Soubory by měly být seskupené podle typu do datové sady a nahrané jako soubor zip. Každá datová sada může obsahovat pouze jeden datový typ.

> [!NOTE]
> Maximální počet datových sad povolených pro import na jedno předplatné je 10 souborů zip pro uživatele bezplatného předplatného (F0) a 500 pro uživatele Standard Subscription (S0).

## <a name="individual-utterances--matching-transcript"></a>Individuální projevy + vyhovující přepis

Záznamy jednotlivých projevy a vyhovující přepisy můžete připravit dvěma způsoby. Napište skript a vyčtěte ho hlasovým talentůem, nebo využijte veřejně dostupný zvuk a přepisovat ho na text. Pokud to uděláte, upravte disfluencies ze zvukových souborů, jako je "um" a další zvuky s výplní, stutters, mumbled slova nebo nesprávné výslovnosti.

Pokud chcete vytvořit dobrý hlasový model, vytvářejte nahrávky v tiché místnosti pomocí vysoce kvalitního mikrofonu. Základem je konzistentní objem, míra speaking, rozteč mluveného slova a vyjádření mannerisms řeči.

> [!TIP]
> Pokud chcete vytvořit hlas pro použití v produkčním prostředí, doporučujeme použít profesionální záznamový Studio a hlasový talentů. Další informace najdete v tématu [Jak nahrávat ukázky hlasu pro vlastní hlas](record-custom-voice-samples.md).

### <a name="audio-files"></a>Zvukové soubory

Každý zvukový soubor by měl obsahovat jednu utterance (jednu větu nebo jednu sadu dialogových oken), která trvá méně než 15 sekund. Všechny soubory musí být ve stejném mluveném jazyce. Vlastní hlasy textu na řeč ve více jazycích nejsou podporované, s výjimkou Chinese-Englishch obousměrných jazyků. Každý zvukový soubor musí mít jedinečný číselný název souboru s příponou názvu souboru. wav.

Při přípravě zvuku postupujte podle těchto pokynů.

| Vlastnost | Hodnota |
| -------- | ----- |
| Formát souboru | RIFF (. wav) seskupené do souboru. zip |
| Vzorkovací frekvence | Minimálně 16 000 Hz |
| Formát ukázky | PCM, 16 bitů |
| Název souboru | Číslo s příponou. wav. Nejsou povoleny žádné duplicitní názvy souborů. |
| Délka zvuku | Kratší než 15 sekund |
| Formát archivu | .zip |
| Maximální velikost archivu | 2048 MB |

> [!NOTE]
> soubory. wav s vzorkovací frekvencí nižší než 16 000 Hz budou odmítnuty. Pokud soubor. zip obsahuje soubory. wav s různými vzorkovacími sazbami, naimportují se jenom ty, které se rovnají nebo jsou vyšší než 16 000 Hz. Portál aktuálně importuje soubory. archivu. zip až do 200 MB. Lze však odeslat více archivů.

### <a name="transcripts"></a>Přepisů

Soubor přepisu je soubor s prostým textem. Pomocí těchto pokynů Připravte přepisy.

| Vlastnost | Hodnota |
| -------- | ----- |
| Formát souboru | Prostý text (. txt) |
| Formát kódování | ANSI/ASCII, UTF-8, UTF-8-BOM, UTF-16-LE nebo UTF-16-. Pro kódování zh-CN, ANSI/ASCII a UTF-8 nejsou podporovány. |
| Počet promluv na řádek | **Jeden** -každý řádek souboru přepisu by měl obsahovat název jednoho ze zvukových souborů následovaný odpovídajícím přepisem. Název souboru a přepis by měly být oddělené tabulátorem (\t). |
| Maximální velikost souboru | 2048 MB |

Níže je uveden příklad, jak jsou přepisy uspořádány utterance by utterance v jednom souboru. txt:

```
0000000001[tab] This is the waistline, and it's falling.
0000000002[tab] We have trouble scoring.
0000000003[tab] It was Janet Maslin.
```
Je důležité, aby přepisy byly 100% přesného přepisu odpovídajícího zvukového záznamu. Chyby v přepisech zavedou ke ztrátě kvality během školení.

## <a name="long-audio--transcript-beta"></a>Dlouhý zvuk + přepis (beta verze)

V některých případech nemusí být k dispozici segmentace zvuku. Prostřednictvím vlastního hlasového portálu poskytujeme službu (beta), která vám umožní segmentovat dlouhé zvukové soubory a vytvářet přepisy. Mějte na paměti, že se tato služba bude účtovat ke svému používání předplatného pro převod řeči na text.

> [!NOTE]
> Služba segmentace dlouhého zvuku bude využívat funkci dávkového přepisu pro převod řeči na text, která podporuje pouze uživatele se standardním předplatným (S0). Během zpracování segmentace se vaše zvukové soubory a Přepisy odešlou taky službě Custom Speech k upřesnění modelu rozpoznávání, aby bylo možné pro vaše data zlepšit přesnost. Během tohoto procesu se nezachovají žádná data. Po segmentaci budou uloženy pouze projevy segmentované a jejich přepisy mapování pro vaše stahování a školení.

### <a name="audio-files"></a>Zvukové soubory

Při přípravě zvuku pro segmentaci postupujte podle těchto pokynů.

| Vlastnost | Hodnota |
| -------- | ----- |
| Formát souboru | RIFF (. wav) se vzorkovací frekvencí alespoň 16 kHz-16 bitů v PCM nebo. mp3 s přenosovou rychlostí minimálně 256 KB/s, seskupenou do souboru. zip |
| Název souboru | Podporované znaky ASCII a Unicode. Nejsou povoleny žádné duplicitní názvy. |
| Délka zvuku | Déle než 20 sekund |
| Formát archivu | .zip |
| Maximální velikost archivu | 2048 MB |

Všechny zvukové soubory by se měly seskupovat do souboru ZIP. Soubory. wav a soubory. mp3 můžete vložit do jednoho zvukového souboru ZIP. Například můžete nahrát soubor ZIP obsahující zvukový soubor s názvem "kingstory. wav", 45-Second-Long a jiný zvuk s názvem "queenstory.mp3", 200-Second-Long. Všechny soubory. mp3 se po zpracování transformují do formátu. wav.

### <a name="transcripts"></a>Přepisů

Přepisy musí být připravené ke specifikacím uvedeným v této tabulce. Každý zvukový soubor musí odpovídat přepisu.

| Vlastnost | Hodnota |
| -------- | ----- |
| Formát souboru | Prostý text (. txt) seskupený do souboru. zip |
| Název souboru | Použít stejný název jako shodný zvukový soubor |
| Formát kódování | Pouze UTF-8-BOM |
| Počet promluv na řádek | Bez omezení |
| Maximální velikost souboru | 2048 MB |

Všechny soubory přepisů v tomto datovém typu by se měly seskupovat do souboru ZIP. Například jste nahráli soubor ZIP obsahující zvukový soubor s názvem "kingstory. wav", 45 sekund dlouhý a druhý s názvem "queenstory.mp3", 200 sekund. Budete potřebovat nahrát další soubor zip, který obsahuje dva přepisy, jeden s názvem kingstory.txt, druhý queenstory.txt. V každém souboru s prostým textem vám poskytneme úplný přepis pro odpovídající zvuk.

Po úspěšném nahrání datové sady vám pomůžeme segmentovat zvukový soubor na projevy na základě poskytnutého přepisu. Můžete kontrolovat segmentované projevy a vyhovující přepisy stažením datové sady. K segmentované projevy se automaticky přiřazují jedinečná ID. Je důležité, abyste se ujistili, že přepisy, které poskytnete, jsou 100% přesné. Chyby v přepisech můžou snížit přesnost během segmentace zvuku a dále zavádět ztráty kvality ve fázi školení, která přichází později.

## <a name="audio-only-beta"></a>Jenom zvuk (beta verze)

Pokud pro zvukové nahrávky nemáte nějaké přepisy, nahrajte data pomocí možnosti **jenom zvuk** . Náš systém vám může přispět k segmentování a přepisovatí vašich zvukových souborů. Mějte na paměti, že se tato služba bude počítat se svým používáním předplatného pro převod řeči na text.

Při přípravě zvuku postupujte podle těchto pokynů.

> [!NOTE]
> Služba segmentace dlouhého zvuku bude využívat funkci dávkového přepisu pro převod řeči na text, která podporuje pouze uživatele se standardním předplatným (S0).

| Vlastnost | Hodnota |
| -------- | ----- |
| Formát souboru | RIFF (. wav) se vzorkovací frekvencí alespoň 16 kHz-16 bitů v PCM nebo. mp3 s přenosovou rychlostí minimálně 256 KB/s, seskupenou do souboru. zip |
| Název souboru | Podporované znaky ASCII a Unicode. Není povolený žádný duplicitní název. |
| Délka zvuku | Déle než 20 sekund |
| Formát archivu | .zip |
| Maximální velikost archivu | 2048 MB |

Všechny zvukové soubory by se měly seskupovat do souboru ZIP. Po úspěšném nahrání datové sady vám pomůžeme segmentovat zvukový soubor na projevy v závislosti na naší službě pro přepis služby Speech Batch. K segmentované projevy se automaticky přiřazují jedinečná ID. Pomocí rozpoznávání řeči budou vygenerovány vyhovující přepisy. Všechny soubory. mp3 se po zpracování transformují do formátu. wav. Můžete kontrolovat segmentované projevy a vyhovující přepisy stažením datové sady.

## <a name="next-steps"></a>Další kroky

- [Vytvoření vlastního hlasu](how-to-custom-voice-create-voice.md)
- [Příručka: záznam ukázek hlasu](record-custom-voice-samples.md)