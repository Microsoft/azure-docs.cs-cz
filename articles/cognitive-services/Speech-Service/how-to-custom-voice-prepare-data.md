---
title: Jak připravit data pro vlastní hlas - řeč služby
titleSuffix: Azure Cognitive Services
description: Vytvořte vlastní hlas pro vaši značku pomocí služby Řeč. Zadáte studiové nahrávky a přidružené skripty, služba generuje jedinečný hlasový model naladěný na nahraný hlas. Pomocí tohoto hlasu můžete syntetizovat řeč ve svých produktech, nástrojích a aplikacích.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: 5427e9f996fb77d455aa8064fc7cb1c65e1fcf7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74805973"
---
# <a name="prepare-data-to-create-a-custom-voice"></a>Příprava dat pro vytvoření vlastního hlasu

Až budete připraveni vytvořit vlastní hlas převodu textu na řeč pro vaši aplikaci, prvním krokem je shromáždit zvukové nahrávky a přidružené skripty a začít trénovat hlasový model. Služba Řeč používá tato data k vytvoření jedinečného hlasu naladěného tak, aby odpovídal hlasu v nahrávkách. Po trénování hlasu můžete začít syntetizovat řeč ve svých aplikacích.

Můžete začít s malým množstvím dat a vytvořit tak ověření koncepce. Čím více dat však poskytnete, tím přirozenější bude váš vlastní hlas. Než budete moci trénovat svůj vlastní model hlasu pro převod textu na řeč, budete potřebovat zvukové nahrávky a související přepisy textu. Na této stránce zkontrolujeme typy dat, způsob jejich použití a způsob jejich správy.

## <a name="data-types"></a>Typy dat

Datová sada hlasového trénovaní obsahuje zvukové nahrávky a textový soubor s přidruženými přepisy. Každý zvukový soubor by měl obsahovat jednu promluvu (jednu větu nebo jednu odbočku pro dialogový systém) a měl by mít kratší než 15 sekund.

V některých případech nemusí být připravena správná datová sada a budete chtít otestovat vlastní hlasový trénink s dostupnými zvukovými soubory, krátkými nebo dlouhými, s přepisy nebo bez přepisů. Poskytujeme nástroje (beta), které vám pomohou segmentovat zvuk do promluv a připravit přepisy pomocí [rozhraní API pro batch transkripce](batch-transcription.md).

V této tabulce jsou uvedeny typy dat a způsob, jakým se jednotlivé typy používají k vytvoření vlastního hlasového modelu pro převod textu na řeč.

| Datový typ | Popis | Kdy je použít | Je vyžadována další služba | Množství pro školení modelu | Národní prostředí (národní prostředí) |
| --------- | ----------- | ----------- | --------------------------- | ----------------------------- | --------- |
| **Jednotlivé projevy + odpovídající přepis** | Kolekce (.zip) zvukových souborů (.wav) jako jednotlivé projevy. Každý zvukový soubor by měl mít délku 15 sekund nebo méně, spárovaný s formátovaným přepisem (.txt). | Profesionální nahrávky s odpovídajícími přepisy | Připraven na trénink. | Žádný pevný požadavek pro en-US a zh-CN. Více než 2 000+ odlišných promluv pro jiná národní prostředí. | [Všechna vlastní hlasová národní prostředí](language-support.md#customization) |
| **Dlouhý zvuk + přepis (beta)** | Kolekce (.zip) dlouhých, nesegmentovaných zvukových souborů (delších než 20 sekund), spárované s přepisem (.txt), který obsahuje všechna mluvená slova. | Máte zvukové soubory a odpovídající přepisy, ale nejsou segmentovány do projevy. | Segmentace (pomocí dávkového přepisu).<br>Transformace zvukového formátu tam, kde je to požadováno. | Žádný pevný požadavek  | [Všechna vlastní hlasová národní prostředí](language-support.md#customization) |
| **Pouze zvuk (beta)** | Kolekce (.zip) zvukových souborů bez přepisu. | Máte k dispozici pouze zvukové soubory bez přepisů. | Segmentace + generování přepisu (pomocí dávkového přepisu).<br>Transformace zvukového formátu tam, kde je to požadováno.| Žádný pevný požadavek | [Všechna vlastní hlasová národní prostředí](language-support.md#customization) |

Soubory by měly být seskupeny podle typu do datové sady a odeslány jako soubor zip. Každá datová sada může obsahovat pouze jeden datový typ.

> [!NOTE]
> Maximální počet povolených datových sad na jedno předplatné je 10 souborů ZIP pro bezplatné předplacené uživatele (F0) a 500 pro uživatele se standardním předplatným (S0).

## <a name="individual-utterances--matching-transcript"></a>Jednotlivé projevy + odpovídající přepis

Můžete připravit nahrávky jednotlivých projevy a odpovídající přepis dvěma způsoby. Buď napište skript a přečti ho hlasovým talentem, nebo použijte veřejně dostupný zvuk a přepište ho na text. Pokud tak učiníte, upravte disfluencies ze zvukových souborů, jako je "um" a další výplň zvuky, koktání, mumlal slova, nebo mispronunciations.

Chcete-li vytvořit dobré hlasové písmo, vytvořte nahrávky v tiché místnosti s vysoce kvalitním mikrofonem. Konzistentní objem, mluvící rychlost, mluvení hřiště, a expresivní manýrismy řeči jsou nezbytné.

> [!TIP]
> Chcete-li vytvořit hlas pro produkční použití, doporučujeme použít profesionální nahrávací studio a hlasové talenty. Další informace naleznete v tématu [Záznam hlasových ukázek pro vlastní hlas](record-custom-voice-samples.md).

### <a name="audio-files"></a>Zvukové soubory

Každý zvukový soubor by měl obsahovat jednu promluvu (jednu větu nebo jednu otočení dialogového systému), kratší než 15 sekund. Všechny soubory musí být ve stejném mluveném jazyce. Vícejazyčné vlastní hlasy převodu textu na řeč nejsou podporovány, s výjimkou čínsko-anglické dvojjazyčné. Každý zvukový soubor musí mít jedinečný číselný název souboru s příponou WAV.

Při přípravě zvuku postupujte podle těchto pokynů.

| Vlastnost | Hodnota |
| -------- | ----- |
| Formát souboru | RIFF (.wav), seskupený do souboru ZIP |
| Vzorkovací frekvence | Nejméně 16 000 Hz |
| Ukázkový formát | PCM, 16bitový |
| Název souboru | Číselné s rozšířením WAV. Nejsou povoleny žádné duplicitní názvy souborů. |
| Délka zvuku | Kratší než 15 sekund |
| Archivní formát | .zip |
| Maximální velikost archivu | 2048 MB |

> [!NOTE]
> Soubory WAV s vzorkovací frekvencí nižší než 16 000 Hz budou odmítnuty. Pokud soubor ZIP obsahuje soubory WAV s různými vzorkovacími frekvencemi, budou importovány pouze soubory, které se rovnají nebo jsou vyšší než 16 000 Hz. Portál v současné době importuje archivy ZIP do 200 MB. Lze však nahrát více archivů.

### <a name="transcripts"></a>Přepisy

Transkripční soubor je soubor ve formátu prostého textu. Pomocí těchto pokynů si můžete připravit přepisy.

| Vlastnost | Hodnota |
| -------- | ----- |
| Formát souboru | Prostý text (.txt) |
| Formát kódování | ANSI/ASCII, UTF-8, UTF-8-BOM, UTF-16-LE nebo UTF-16-BE. Pro kódování zh-CN, ANSI/ASCII a UTF-8 nejsou podporovány. |
| Počet promluv na řádek | **Jeden** - Každý řádek transkripčního souboru by měl obsahovat název jednoho ze zvukových souborů, následovaný odpovídajícím přepisem. Název souboru a přepis by měly být oddělené tabulátorem (\t). |
| Maximální velikost souboru | 2048 MB |

Níže je uveden příklad uspořádání přepisů utterance podle utterance v jednom souboru TXT:

```
0000000001[tab] This is the waistline, and it's falling.
0000000002[tab] We have trouble scoring.
0000000003[tab] It was Janet Maslin.
```
Je důležité, aby přepisy byly 100% přesné přepisy odpovídajícího zvuku. Chyby v přepisech způsobí ztrátu kvality během tréninku.

> [!TIP]
> Při vytváření produkčních hlasů převodu textu na řeč vyberte projevy (nebo psát skripty), které berou v úvahu foetické pokrytí a efektivitu. Máte potíže se získáním požadovaných výsledků? [Chcete-li](mailto:speechsupport@microsoft.com) se dozvědět více o tom, jak nás nechat konzultovat, obraťte se na tým Custom Voice.

## <a name="long-audio--transcript-beta"></a>Dlouhý zvuk + přepis (beta)

V některých případech nemusí být segmentovaný zvuk k dispozici. Prostřednictvím vlastního hlasového portálu poskytujeme službu (beta), která vám pomůže segmentovat dlouhé zvukové soubory a vytvářet přepisy. Nezapomeňte, že tato služba bude účtována za použití předplatného převodu řeči na text.

> [!NOTE]
> Služba segmentace dlouhého zvuku bude využívat funkci dávkového přepisu funkce převodu řeči na text, která podporuje pouze uživatele standardního předplatného (S0). Během zpracování segmentace budou zvukové soubory a přepisy odeslány také službě Vlastní řeč, aby se zpřesnit model rozpoznávání tak, aby bylo možné zlepšit přesnost vašich dat. Během tohoto procesu nebudou uchovávána žádná data. Po dokončení segmentace budou pro stahování a školení uloženy pouze projevy segmentované a jejich přepisy mapování.

### <a name="audio-files"></a>Zvukové soubory

Při přípravě zvuku pro segmentaci postupujte podle těchto pokynů.

| Vlastnost | Hodnota |
| -------- | ----- |
| Formát souboru | RIFF (.wav) s vzorkovací frekvencí nejméně 16 khz-16-bit v PCM nebo .mp3 s přenosovou rychlostí alespoň 256 kb/s, seskupený do souboru ZIP |
| Název souboru | Podporované znaky ASCII a Unicode. Nejsou povoleny žádné duplicitní názvy. |
| Délka zvuku | Delší než 20 sekund |
| Archivní formát | .zip |
| Maximální velikost archivu | 2048 MB |

Všechny zvukové soubory by měly být seskupeny do souboru zip. Je to ok, aby .wav soubory a .mp3 soubory do jednoho audio zip. Můžete například nahrát soubor zip obsahující zvukový soubor s názvem "kingstory.wav", 45 sekund dlouhý a další zvuk s názvem "queenstory.mp3", 200 sekund dlouhý. Všechny soubory MP3 budou po zpracování transformovány do formátu WAV.

### <a name="transcripts"></a>Přepisy

Přepisy musí být připraveny podle specifikací uvedených v této tabulce. Každý zvukový soubor musí být spárován s přepisem.

| Vlastnost | Hodnota |
| -------- | ----- |
| Formát souboru | Prostý text (.txt), seskupený do .zip |
| Název souboru | Použít stejný název jako odpovídající zvukový soubor |
| Formát kódování | Pouze UTF-8-BOM |
| Počet promluv na řádek | Bez omezení |
| Maximální velikost souboru | 2048 MB |

Všechny soubory přepisů v tomto datovém typu by měly být seskupeny do souboru zip. Například jste nahráli soubor zip obsahující zvukový soubor s názvem "kingstory.wav", dlouhý 45 sekund a další s názvem "queenstory.mp3", dlouhý 200 sekund. Budete muset nahrát další soubor zip obsahující dva přepisy, jeden s názvem 'kingstory.txt', druhý 'queenstory.txt'. V rámci každého souboru prostého textu poskytnete úplný správný přepis odpovídajícího zvuku.

Po úspěšném nahrání datové sady vám pomůžeme segmentovat zvukový soubor do promluv na základě poskytnutého přepisu. Můžete zkontrolovat segmentované projevy a odpovídající přepisy stažením datové sady. Jedinečná ID budou přiřazena k segmentovaným projevům automaticky. Je důležité, abyste se ujistili, že přepisy, které poskytnete, jsou 100% přesné. Chyby v přepisech mohou snížit přesnost během segmentace zvuku a dále zavést ztrátu kvality ve fázi školení, která přichází později.

## <a name="audio-only-beta"></a>Pouze zvuk (beta)

Pokud nemáte přepisy zvukových nahrávek, nahrajte data pomocí možnosti **Pouze zvuk.** Náš systém vám může pomoci segmentovat a přepisovat vaše zvukové soubory. Mějte na paměti, že tato služba se bude započítávat do vašeho předplatného řeči na text.

Při přípravě zvuku postupujte podle těchto pokynů.

> [!NOTE]
> Služba segmentace dlouhého zvuku bude využívat funkci dávkového přepisu funkce převodu řeči na text, která podporuje pouze uživatele standardního předplatného (S0).

| Vlastnost | Hodnota |
| -------- | ----- |
| Formát souboru | RIFF (.wav) s vzorkovací frekvencí nejméně 16 khz-16-bit v PCM nebo .mp3 s přenosovou rychlostí alespoň 256 kb/s, seskupený do souboru ZIP |
| Název souboru | Podporované znaky ASCII a Unicode. Není povolen žádný duplicitní název. |
| Délka zvuku | Delší než 20 sekund |
| Archivní formát | .zip |
| Maximální velikost archivu | 2048 MB |

Všechny zvukové soubory by měly být seskupeny do souboru zip. Jakmile je vaše datová sada úspěšně nahrána, pomůžeme vám segmentovat zvukový soubor do promluv na základě naší služby přepisu dávky řeči. Jedinečná ID budou přiřazena k segmentovaným projevům automaticky. Odpovídající přepisy budou generovány prostřednictvím rozpoznávání řeči. Všechny soubory MP3 budou po zpracování transformovány do formátu WAV. Můžete zkontrolovat segmentované projevy a odpovídající přepisy stažením datové sady.

## <a name="next-steps"></a>Další kroky

- [Vytvoření vlastního hlasu](how-to-custom-voice-create-voice.md)
- [Průvodce: Záznam hlasových ukázek](record-custom-voice-samples.md)
