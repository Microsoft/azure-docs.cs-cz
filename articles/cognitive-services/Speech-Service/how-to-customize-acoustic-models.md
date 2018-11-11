---
title: 'Kurz: Vytvoření akustického modelu s využitím služby Speech'
titlesuffix: Azure Cognitive Services
description: Zjistěte, jak vytvořit akustický model s využitím služby Speech ve službách Azure Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: tutorial
ms.date: 06/25/2018
ms.author: panosper
ms.openlocfilehash: 70fc9c34599f27eb5d67b79ef823f8037ae55ba9
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50215238"
---
# <a name="tutorial-create-a-custom-acoustic-model"></a>Kurz: Vytvoření vlastního akustického modelu

Vytvoření vlastního akustického modelu je užitečné, pokud je vaše aplikace určená pro použití v konkrétním prostředí, například v autě, pro určitá zařízení nebo podmínky pro nahrávání nebo pro určité uživatele. Ukázky zahrnují řeč s přízvukem, určité zvuky na pozadí nebo použití určitého mikrofonu pro nahrávání.

V tomto článku získáte informace o těchto tématech:
> [!div class="checklist"]
> * Příprava dat
> * Import sady akustických dat
> * Vytvoření vlastního akustického modelu

Pokud ještě nemáte účet služeb Azure Cognitive Services, vytvořte si [bezplatný účet](https://azure.microsoft.com/try/cognitive-services) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Ujistěte se, že je váš účet služeb Cognitive Services připojený k předplatnému, a to otevřením stránky s [předplatnými služeb Cognitive Services](https://cris.ai/Subscriptions).

Můžete vybrat **Connect existing subscription** (Připojit stávající předplatné) a připojit se k předplatnému služby Speech vytvořenému na webu Azure Portal.

Informace o vytvoření předplatného služby Speech na webu Azure Portal najdete na stránce [Vyzkoušejte si službu Speech zdarma](get-started.md).

## <a name="prepare-the-data"></a>Příprava dat

K přizpůsobení akustického modelu pro konkrétní doménu je nutná kolekce hlasových dat. Tato kolekce může být v rozsahu od několika promluv po stovky hodin řeči. Kolekce zahrnuje sadu zvukových souborů s hlasovými daty a textový soubor s přepisy všech zvukových souborů. Zvuková data by měla vystihovat scénář, ve kterém chcete rozpoznávání používat.

Příklad:

* Pokud byste chtěli lépe rozpoznávat řeč v hlučném provozním prostředí, měly by zvukové soubory zahrnovat řeč lidí v hlučném provozu.
* Pokud vás zajímá optimalizace výkonu pro jednoho mluvčího &mdash; například chcete pořídit přepis všech důležitých projevů prezidenta Franklina D. Roosevelta &mdash;, měly by zvukové soubory zahrnovat jenom řadu ukázek od tohoto mluvčího.

Sada akustických dat pro přizpůsobení akustického modelu se skládá ze dvou částí: (1) sada zvukových souborů obsahujících hlasová data a (2) soubor obsahující přepisy všech zvukových souborů.

### <a name="audio-data-recommendations"></a>Doporučení pro zvuková data

* Všechny zvukové soubory v sadě dat by měly být uložené ve zvukovém formátu WAV (RIFF).
* Zvuk musí mít vzorkovací frekvenci 8 kHz nebo 16 kHz a vzorkovací hodnoty by měly být uložené jako 16bitová celá čísla se znaménkem (krátká) nekomprimovaného formátu PCM.
* Podporují se jenom zvukové soubory s jedním kanálem (mono).
* Zvukové soubory můžou mít délku 100 mikrosekund až 1 minuta, ale ideálně by měly mít 10 až 12 sekund. V ideálním případě by měl každý zvukový soubor začínat a končit tichem po dobu nejméně 100 mikrosekund, obvyklá doba trvání ticha bývá mezi 500 mikrosekundami a 1 sekundou.
* Pokud je ve vašich datech šum na pozadí, doporučujeme mít v datech také nějaké ukázky s delšími segmenty ticha &mdash; například několik sekund &mdash; před nebo po mluveném obsahu.
* Každý zvukový soubor by měl obsahovat jedinou promluvu &mdash; například jednu větu pro diktování, jeden dotaz nebo jeden obrat dialogového systému.
* Každý zvukový soubor v sadě dat musí mít jedinečný název souboru a příponu .wav.
* Sada zvukových souborů by měla být umístěná v jediné složce bez podadresářů a celá sada zvukových souborů by měla být sbalená jako jeden soubor archivu .zip.

> [!NOTE]
> Importy dat přes webový portál jsou momentálně omezené na 2 GB, což je maximální velikost sady akustických dat. Tato velikost odpovídá přibližně 17 hodinám zvukového záznamu na frekvenci 16 kHz nebo 34 hodinám zvukového záznamu na frekvenci 8 kHz. Následující tabulka obsahuje souhrn hlavních požadavků na zvuková data:
>

| Vlastnost | Hodnota |
|---------- |----------|
| Formát souboru | RIFF (WAV) |
| Vzorkovací frekvence | 8 000 hertzů (Hz) nebo 16 000 Hz |
| Kanály | 1 (mono) |
| Vzorkovací formát | PCM, 16bitový celočíselný |
| Doba trvání souboru | 0,1 sekundy < doba trvání < 12 sekund | 
| Ticho na začátku a na konci | > 0,1 sekund |
| Formát archivu | .zip |
| Maximální velikost archivu | 2 GB |

> [!NOTE]
> Názvy souborů můžou obsahovat pouze znaky latinky a odpovídat formátu název_souboru.přípona.

## <a name="language-support"></a>Podpora jazyků

Úplný seznam podporovaných jazyků pro vlastní jazykové modely **převodu řeči na text** najdete v tématu [Podporované jazyky pro službu Speech](language-support.md#speech-to-text).

### <a name="transcriptions-for-the-audio-dataset"></a>Přepisy sady zvukových dat

Přepisy všech souborů WAV by měl obsahovat jediný soubor prostého textu. Každý řádek souboru s přepisem by měl obsahovat název jednoho zvukového souboru a za ním odpovídající přepis. Název souboru a přepis by měly být oddělené tabulátorem (\t).

  Příklad:
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```
> [!NOTE]
> Přepis by měl být kódovaný ve formátu UTF-8 BOM (značka pořadí bajtů).

Přepisy se budou normalizovat, aby je mohl systém zpracovat. Existuje však několik důležitých normalizací, které musí uživatel provést _před_ nahráním dat do služby Custom Speech Service. Informace o tom, jak určit vhodný jazyk, který se použije při přípravě přepisů, najdete v [pokynech pro přepis pro službu Speech](prepare-transcription.md).

Kroky v následujících částech proveďte na [portálu služby Speech](https://cris.ai).

## <a name="import-the-acoustic-dataset"></a>Import sady akustických dat

Jakmile připravíte zvukové soubory a přepisy, bude možné je importovat na webový portál služby.

Pokud je chcete importovat, nejprve se ujistěte, že jste přihlášeni k [portálu služby Speech](https://cris.ai). Pak v rozevíracím seznamu **Custom Speech** na pásu karet vyberte **Adaptation Data** (Adaptační data). Pokud do služby Custom Speech Service nahráváte data poprvé, zobrazí se prázdná tabulka **Datasets** (Datové sady). 

Na řádku **Acoustic Datasets** (Sady akustických dat) vyberte tlačítko **Import** (Importovat) a na webu se zobrazí stránka pro nahrání nové sady dat.

![Stránka Import Acoustic Data (Import akustických dat)](media/stt/speech-acoustic-datasets-import.png)

Do polí **Name** (Název) a **Description** (Popis) zadejte odpovídající informace. K udržení přehledu o různých sadách dat, které nahráváte, jsou užitečné uživatelsky přívětivé popisy. 

V polích **Transcriptions file (.txt)** (Soubor s přepisy – .txt) and **Audio files (.zip)** (Zvukové soubory – .zip) vyberte **Browse** (Procházet) a pak vyberte soubor prostého textu s přepisem a archiv ZIP se soubory WAV. Po dokončení přípravy nahrajte data výběrem možnosti **Import** (Importovat). Vaše data se nahrají. U větších datových sad může proces importu trvat několik minut.

Po dokončení nahrávání se vraťte k tabulce **Acoustic Datasets** (Sady akustických dat). Zobrazí se položka, která odpovídá vaší sadě akustických dat. Všimněte si, že má přiřazené jedinečné ID (GUID). Během zařazování dat do fronty ke zpracování bude jejich aktuální stav *NotStarted* (Nespuštěno), během ověřování bude jejich stav *Running* (Spuštěno), a až budou data připravená k použití, bude jejich stav *Complete* (Dokončeno).

Součástí ověření dat je řada kontrol zvukových souborů, které ověřují formát souboru, délku a vzorkovací frekvenci, a kontrol souborů s přepisy, které ověřují formát souboru a provádějí určitou normalizaci textu.

Když je stav *Succeeded* (Úspěšně dokončeno), můžete výběrem možnosti **Details** (Podrobnosti) zobrazit sestavu ověřování akustických dat. Zobrazí se počet promluv, které prošly a neprošly ověřováním, spolu s podrobnostmi o promluvách, které neprošly. V příkladu na následujícím obrázku dva soubory WAV neprošly ověřováním kvůli nesprávnému zvukovému formátu. V této sadě dat má jeden soubor nesprávnou vzorkovací frekvenci a druhý soubor má nesprávný formát souboru.

![Stránka Adaptation Data Details (Podrobnosti o adaptačních datech)](media/stt/speech-acoustic-datasets-report.png)

Pokud chcete změnit název nebo popis sady dat, můžete vybrat odkaz **Edit** (Upravit) a změnit tyto položky. Položky zvukových souborů ani přepisů není možné změnit.

## <a name="create-a-custom-acoustic-model"></a>Vytvoření vlastního akustického modelu

Jakmile bude stav sady akustických dat *Complete* (Dokončeno), můžete ji použít k vytvoření vlastního akustického modelu. Pokud to chcete udělat, v rozevíracím seznamu **Custom Speech** vyberte **Acoustic Models** (Akustické modely). V tabulce **Your models** (Vaše modely) se zobrazí všechny vaše vlastní akustické modely. Pokud se jedná o vaše první použití, bude tato tabulka prázdná. V názvu tabulky se zobrazí aktuální národní prostředí. V současné době můžete akustické modely vytvářet jenom pro americkou angličtinu.

Pokud chcete vytvořit nový model, pod názvem tabulky vyberte **Create New** (Vytvořit nový). Tak jako dříve zadejte název a popis, které vám pomůžou tento model identifikovat. Do pole **Description** (Popis) můžete například zaznamenat počáteční model a sadu akustických dat, pomocí kterých jste daný model vytvořili. 

Pak v rozevíracím seznamu **Base Acoustic Model** (Základní akustický model) vyberte základní model. Základní model je výchozím bodem pro vaše přizpůsobení. Můžete si vybrat ze dvou základních akustických modelů:
* **Akustický model pro vyhledávání a diktování Microsoftu** je vhodný pro řeč určenou přímo pro aplikaci, jako jsou příkazy, vyhledávací dotazy nebo diktování. 
* **Konverzační model Microsoftu** je vhodný pro rozpoznávání hovorové řeči. Tento typ řeči je obvykle určený pro jinou osobu a vyskytuje se v call centrech nebo na schůzkách. 

Latence pro částečné výsledky je v konverzačních modelech vyšší než v modelech pro vyhledávání a diktování.

> [!NOTE]
> Momentálně zavádíme náš nový **univerzální** model, který má za cíl řešit všechny scénáře. Výše uvedené modely zůstanou také veřejně dostupné.

Pak v rozevíracím seznamu **Acoustic Data** (Akustická data) vyberte akustická data, která chcete použít k provedení přizpůsobení.

![Stránka Create Acoustic Model (Vytvoření akustického modelu)](media/stt/speech-acoustic-models-create2.png)

Po dokončení zpracování můžete volitelně vybrat, aby se provedlo testování přesnosti vašeho nového modelu. Tento test spustí u zadané sady akustických dat vyhodnocení převodu řeči na text s využitím přizpůsobeného akustického modelu a zobrazí sestavu výsledků. Pokud chcete toto testování provést, zaškrtněte políčko **Accuracy Testing** (Testování přesnosti). Pak v rozevíracím seznamu vyberte jazykový model. Pokud jste ještě žádné vlastní jazykové modely nevytvořili, v rozevíracím seznamu se zobrazí jenom základní jazykové modely. Informace o výběru nejvhodnějšího jazykového modelu najdete v [kurzu vytvoření vlastního jazykového modelu](how-to-customize-language-model.md).

Nakonec vyberte sadu akustických dat, kterou chcete použít k vyhodnocení vlastního modelu. Pokud provádíte testování přesnosti, je k získání realistické představy o výkonu modelu důležité vybrat sadu akustických dat, která se liší od sady použité k vytvoření modelu. Testování přesnosti na trénovacích datech neumožňuje vyhodnotit výkon upraveného modelu za skutečných podmínek. Výsledek bude příliš optimistický. Upozorňujeme také, že testování přesnosti je omezené na maximálně 1 000 promluv. Pokud je sada akustických dat pro testování větší, vyhodnotí se jenom prvních 1 000 promluv.

Až budete chtít spustit proces přizpůsobování, vyberte **Create** (Vytvořit).

V tabulce akustických modelů se zobrazí nová položka, která odpovídá tomuto novému modelu. V tabulce se zobrazí také stav procesu: *Waiting* (Čekající), *Processing* (Zpracování) nebo *Complete* (Dokončeno).

![Stránka Acoustic Models (Akustické modely)](media/stt/speech-acoustic-models-creating.png)

## <a name="next-steps"></a>Další kroky

- [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Rozpoznávání řeči v jazyce C#](quickstart-csharp-dotnet-windows.md)
- [Ukázková data Git](https://github.com/Microsoft/Cognitive-Custom-Speech-Service)
