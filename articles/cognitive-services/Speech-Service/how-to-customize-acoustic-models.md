---
title: Vytvoření akustického modelu s využitím služby Speech – Microsoft Cognitive Services | Microsoft Docs
description: Přečtěte si, jak vytvořit akustický model s využitím služby Speech ve službách Microsoft Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: tutorial
ms.date: 06/25/2018
ms.author: panosper
ms.openlocfilehash: 32dc9015f5e1a58d02571eb84e6ceeb0e9e8582d
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39069035"
---
# <a name="tutorial-create-a-custom-acoustic-model"></a>Kurz: Vytvoření vlastního akustického modelu

Vytvoření vlastního akustického modelu je užitečné, pokud je vaše aplikace určená pro použití v konkrétním prostředí, například v autě, pro určitá zařízení nebo podmínky pro nahrávání nebo pro určité uživatele. Ukázky zahrnují řeč s přízvukem, určité zvuky na pozadí nebo použití určitého mikrofonu pro nahrávání.

Na této stránce se naučíte tyto postupy:
> [!div class="checklist"]
> * Příprava dat
> * Import sady akustických dat
> * Vytvoření vlastního akustického modelu

Pokud ještě nemáte účet služeb Cognitive Services, vytvořte si [bezplatný účet](https://azure.microsoft.com/try/cognitive-services) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Ujistěte se, že je váš účet služeb Cognitive Services připojený k předplatnému, a to otevřením stránky s [předplatnými služeb Cognitive Services](https://customspeech.ai/Subscriptions).

Můžete kliknout na tlačítko **Connect existing subscription** (Připojit stávající předplatné) a připojit se k předplatnému služby Speech vytvořenému na webu Azure Portal.

Informace o vytvoření předplatného služby Speech na webu Azure Portal najdete na stránce s [úvodními informacemi](get-started.md).

## <a name="prepare-the-data"></a>Příprava dat

K přizpůsobení akustického modelu pro konkrétní doménu je nutná kolekce hlasových dat. Ta může být v rozsahu od několika promluv po stovky hodin řeči. Tato kolekce zahrnuje sadu zvukových souborů s hlasovými daty a textový soubor s přepisy všech zvukových souborů. Zvuková data by měla vystihovat scénář, ve kterém chcete rozpoznávání používat.

Příklad:

*   Pokud byste chtěli lépe rozpoznávat řeč v hlučném provozním prostředí, měly by zvukové soubory zahrnovat řeč lidí v hlučném provozu.
*   Pokud vás zajímá optimalizace výkonu pro jednoho mluvčího, například chcete pořídit přepis všech důležitých projevů prezidenta Franklina D. Roosevelta, měly by zvukové soubory zahrnovat jenom řadu ukázek od tohoto mluvčího.

Sadu akustických dat pro přizpůsobení akustického modelu tvoří dvě součásti: (1) sada zvukových souborů, která obsahuje hlasová data, a (2) soubor obsahující přepisy všech zvukových souborů.

### <a name="audio-data-recommendations"></a>Doporučení pro zvuková data

*   Všechny zvukové soubory v sadě dat by měly být uložené ve zvukovém formátu WAV (RIFF).
*   Zvuk musí mít vzorkovací frekvenci 8 kHz nebo 16 kHz a vzorkovací hodnoty by měly být uložené jako 16bitová celá čísla se znaménkem (krátká) nekomprimovaného formátu PCM.
*   Podporují se jenom zvukové soubory s jedním kanálem (mono).
*   Délka zvukových souborů musí být v rozmezí od 100 ms do 1 minuty. V ideálním případě by měl každý zvukový soubor začínat a končit tichem po dobu nejméně 100 ms, obvyklá doba trvání ticha bývá mezi 500 ms a 1 sekundou.
*   Pokud je ve vašich datech šum na pozadí, doporučuje se mít v datech také nějaké ukázky s delšími segmenty ticha, například několik sekund před nebo po mluveném obsahu.
*   Každý zvukový soubor by měl obsahovat jedinou promluvu, například jednu větu pro diktování, jeden dotaz nebo jeden obrat dialogového systému.
*   Každý zvukový soubor v sadě dat musí mít jedinečný název souboru a příponu wav.
*   Sada zvukových souborů by měla být umístěná v jediné složce bez podadresářů a celá sada zvukových souborů by měla být sbalená jako jeden soubor archivu ZIP.

> [!NOTE]
> Importy dat přes webový portál jsou momentálně omezené na 2 GB, což je maximální velikost sady akustických dat. Odpovídá to přibližně 17 hodinám zvukového záznamu na frekvenci 16 kHz nebo 34 hodinám zvukového záznamu na frekvenci 8 kHz. Následující tabulka obsahuje souhrn hlavních požadavků na zvuková data.
>

| Vlastnost | Hodnota |
|---------- |----------|
| Formát souboru | RIFF (WAV) |
| Vzorkovací frekvence | 8 000 Hz nebo 16 000 Hz |
| Kanály | 1 (mono) |
| Vzorkovací formát | PCM, 16bitový celočíselný |
| Doba trvání souboru | 0,1 sekundy < doba trvání < 60 sekund |
| Ticho na začátku a na konci | > 0,1 sekundy |
| Formát archivu | ZIP |
| Maximální velikost archivu | 2 GB |

## <a name="language-support"></a>Podpora jazyků

Pro vlastní jazykové modely **převodu řeči na text** se podporují následující jazyky.

Kliknutím zobrazíte úplný seznam [podporovaných jazyků](supported-languages.md).

### <a name="transcriptions-for-the-audio-dataset"></a>Přepisy sady zvukových dat

Přepisy všech souborů WAV by měl obsahovat jediný soubor prostého textu. Každý řádek souboru s přepisem by měl obsahovat název jednoho zvukového souboru a za ním odpovídající přepis. Název souboru a přepis by měly být oddělené tabulátorem (\t).

  Příklad:
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```
> [!NOTE]
> Přepis by měl být kódovaný ve formátu UTF-8 BOM.

Přepisy se budou normalizovat, aby je mohl systém zpracovat. Existuje však několik důležitých normalizací, které musí uživatel provést _před_ nahráním dat do služby Custom Speech Service. Informace o vhodném jazyku při přípravě přepisů najdete v [pokynech pro přepis](prepare-transcription.md).

Následující kroky se provádějí na [portálu služby Speech](https://customspeech.ai).

## <a name="import-the-acoustic-data-set"></a>Import sady akustických dat

Když jsou zvukové soubory a přepisy připravené, je možné je importovat na webový portál služby.

Pokud to chcete udělat, nejprve zkontrolujte, že jste přihlášení k [portálu služby Speech](https://customspeech.ai). Pak klikněte na rozevírací nabídku Custom Speech na horním pásu karet a vyberte Adaptation Data (Adaptační data). Pokud do služby Custom Speech Service nahráváte data poprvé, zobrazí se prázdná tabulka Datasets (Datové sady). 

Klikněte na tlačítko Import (Importovat) na řádku Acoustic Datasets (Sady akustických dat) a na webu se zobrazí stránka pro nahrání nové sady dat.

![vyzkoušení](media/stt/speech-acoustic-datasets-import.png)

Do odpovídajících textových polí zadejte _Name_ (Název) a _Description_ (Popis). K udržení přehledu o různých sadách dat, které nahráváte, jsou užitečné uživatelsky přívětivé popisy. Pak klikněte na Choose File (Zvolit soubor) pro Transcription File (Soubor s přepisem) a WAV files (Soubory WAV) a vyberte soubor prostého textu s přepisem a archiv ZIP se soubory WAV. Po dokončení přípravy nahrajte data kliknutím na Import (Importovat). Vaše data se pak nahrají. U větších sad dat to může trvat několik minut.

Po dokončení nahrávání se vrátíte k tabulce Acoustic Datasets (Sady akustických dat), ve které se zobrazí položka odpovídající vaší sadě akustických dat. Všimněte si, že má přiřazené jedinečné ID (GUID). U dat bude uvedený také stav, který odráží jejich aktuální stav. Během zařazování dat do fronty ke zpracování bude jejich stav NotStarted (Nespuštěno), během ověřování bude jejich stav Running (Spuštěno), a až budou data připravená k použití, bude jejich stav Complete (Dokončeno).

Součástí ověření dat je řada kontrol zvukových souborů, které ověřují formát souboru, délku a vzorkovací frekvenci, a kontrol souborů s přepisy, které ověřují formát souboru a provádějí určitou normalizaci textu.

Když je stav Succeeded (Úspěšně dokončeno), můžete kliknutím na Details (Podrobnosti) zobrazit sestavu ověřování akustických dat. Zobrazí se počet promluv, které prošly a neprošly ověřováním, spolu s podrobnostmi o promluvách, které ověřováním neprošly. V následujícím příkladu dva soubory WAV ověřováním neprošly, protože mají nesprávný zvukový formát (jeden soubor v této sadě dat má nesprávnou vzorkovací frekvenci a druhý má nesprávný formát souboru).

![vyzkoušení](media/stt/speech-acoustic-datasets-report.png)

Pokud chcete v nějakém okamžiku změnit název nebo popis sady dat, můžete kliknout na odkaz Edit (Upravit) a změnit tyto položky. Zvukové soubory ani přepisy není možné změnit.

## <a name="create-a-custom-acoustic-model"></a>Vytvoření vlastního akustického modelu

Jakmile je stav sady akustických dat Complete (Dokončeno), můžete ji použít k vytvoření vlastního akustického modelu. Pokud to chcete udělat, klikněte v rozevírací nabídce Custom Speech (Vlastní řeč) na Acoustic Models (Akustické modely). Zobrazí se tabulka s názvem Your models (Vaše modely), ve které jsou uvedené všechny vaše vlastní akustické modely. Pokud se jedná o vaše první použití, bude tato tabulka prázdná. Aktuální národní prostředí je uvedené v názvu tabulky. V současné době lze akustické modely vytvářet jenom pro americkou angličtinu.

Pokud chcete vytvořit nový model, klikněte pod názvem tabulky na Create New (Vytvořit nový). Tak jako dříve zadejte název a popis, které vám pomůžou tento model identifikovat. Do pole Description (Popis) je například možné zaznamenat počáteční model a sadu akustických dat, pomocí kterých se daný model vytvořil. Pak v rozevírací nabídce vyberte Base Acoustic Model (Základní akustický model). Základní model je model, který je výchozím bodem pro vaše vlastní nastavení. Můžete si vybrat ze dvou základních akustických modelů. _Akustický model Microsoftu pro vyhledávání a diktování_ je vhodný pro řeč určenou přímo pro aplikaci, jako jsou příkazy, vyhledávací dotazy nebo diktování. _Konverzační model Microsoftu_ je vhodný pro rozpoznávání hovorové řeči. Tento typ řeči je obvykle určený pro jinou osobu a vyskytuje se v call centrech nebo na schůzkách. Latence pro částečné výsledky je v konverzačních modelech vyšší než v modelech pro vyhledávání a diktování.

> [!NOTE]
> Momentálně zavádíme náš nový univerzální model, který má za cíl řešit všechny scénáře. Výše uvedené modely zůstanou také veřejně dostupné.

Dále vyberte pomocí rozevírací nabídky akustická data, která chcete použít k provedení vlastního nastavení.

![vyzkoušení](media/stt/speech-acoustic-models-create2.png)

Můžete volitelně vybrat, aby se po dokončení zpracování provedlo testování přesnosti vašeho nového modelu. Tato akce spustí u zadané sady akustických dat vyhodnocení převodu řeči na text s využitím přizpůsobeného akustického modelu a zobrazí sestavu výsledků. Pokud chcete toto testování provést, zaškrtněte políčko Accuracy Testing (Testování přesnosti). Pak v rozevírací nabídce vyberte jazykový model. Pokud jste ještě žádné vlastní jazykové modely nevytvořili, bude rozevírací seznam obsahovat jenom základní jazykové modely. Podívejte se na [popis](how-to-customize-language-model.md) základních jazykových modelů v průvodci a vyberte ten, který je nejvhodnější.

Nakonec vyberte sadu akustických dat, kterou chcete použít k vyhodnocení vlastního modelu. Pokud provádíte testování přesnosti, je k získání realistické představy o výkonu modelu důležité vybrat akustická data, která se liší od těch použitých k vytvoření modelu. Testování přesnosti na trénovacích datech vám neumožní vyhodnotit výkon upraveného modelu za skutečných podmínek. Výsledek bude příliš optimistický. Upozorňujeme také, že testování přesnosti je omezené na maximálně 1000 promluv. Pokud je sada akustických dat pro testování větší, vyhodnotí se jenom prvních 1000 promluv.

Až budete chtít spustit proces přizpůsobování, stiskněte Create (Vytvořit).

V tabulce akustických modelů se teď zobrazí nová položka, která odpovídá tomuto novému modelu. Stav procesu se odráží v tabulce. Tento stav může mít hodnoty Waiting (Čekající), Processing (Zpracování) a Complete (Dokončeno).

![vyzkoušení](media/stt/speech-acoustic-models-creating.png)

## <a name="next-steps"></a>Další kroky

- [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Rozpoznávání řeči v C#](quickstart-csharp-dotnet-windows.md)
- [Ukázková data Git](https://github.com/Microsoft/Cognitive-Custom-Speech-Service)
