---
title: Vytvoření vlastní hlasové služby řeči
titleSuffix: Azure Cognitive Services
description: Až budete připraveni na nahrání dat, přejdete na vlastní hlasový portál. Vytvořte nebo vyberte vlastní projekt hlasu. Projekt musí sdílet správný jazyk/národní prostředí a vlastnosti pohlaví jako data, která chcete použít pro hlasové školení.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: 3747033fcaf65e0c6da07e9f1bb625771958bb4f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91319057"
---
# <a name="create-a-custom-voice"></a>Vytvoření vlastního hlasu

V části [Příprava dat pro vlastní hlas](how-to-custom-voice-prepare-data.md)jsme popsali různé typy dat, které můžete použít k učení vlastního hlasu a různých požadavků na formát. Po přípravě dat je můžete začít nahrávat do [vlastního hlasového portálu](https://aka.ms/custom-voice-portal)nebo pomocí rozhraní API pro vlastní hlasové školení. Tady popisujeme kroky školení vlastního hlasu prostřednictvím portálu.

> [!NOTE]
> Tato stránka předpokládá, že jste si přečetli [Začínáme s vlastním hlasem](how-to-custom-voice.md) a [připravují data pro vlastní hlas](how-to-custom-voice-prepare-data.md)a vytvořili vlastní hlasový projekt.

Podívejte se na jazyky podporované pro vlastní hlas: [jazyk pro přizpůsobení](language-support.md#customization).

## <a name="upload-your-datasets"></a>Nahrání datových sad

Až budete připraveni na nahrání dat, přejdete na [vlastní hlasový portál](https://aka.ms/custom-voice-portal). Vytvořte nebo vyberte vlastní projekt hlasu. Projekt musí sdílet správný jazyk/národní prostředí a vlastnosti pohlaví jako data, která chcete použít pro hlasové školení. Například vyberte, `en-GB` jestli se zvukové nahrávky provedly v angličtině s zvýrazňující velkou Británie.

Přejděte na kartu **data** a klikněte na **Odeslat data**. V průvodci vyberte správný datový typ, který odpovídá, co jste připravili.

Každá datová sada, kterou nahráváte, musí splňovat požadavky na datový typ, který zvolíte. Před nahráním dat je důležité data správně naformátovat. Tím se zajistí, že se data budou přesně zpracovávat pomocí vlastní hlasové služby. Přejít na [Příprava dat pro vlastní hlas](how-to-custom-voice-prepare-data.md) a zajistěte, aby byla data ve správném formátu.

> [!NOTE]
> Uživatelé bezplatného předplatného (F0) můžou nahrávat dvě datové sady současně. Standardní předplatné (S0) uživatelé můžou nahrávat pět datových sad současně. Pokud dosáhnete limitu, počkejte na dokončení importu alespoň jedné z vašich datových sad. Pak to zkuste znovu.

> [!NOTE]
> Maximální počet datových sad povolených pro import na jedno předplatné je 10 souborů. zip pro uživatele bezplatného předplatného (F0) a 500 pro uživatele Standard Subscription (S0).

Datové sady se po stisknutí tlačítka Odeslat automaticky ověřují. Ověřování dat zahrnuje sérii kontrol zvukových souborů pro ověření jejich formátu, velikosti a vzorkovací frekvence. Opravte chyby, pokud nějaké existují, a odešlete je znovu. Po úspěšném zahájení žádosti o import dat by se měla zobrazit položka v tabulce dat, která odpovídá datové sadě, kterou jste právě Nahráli.

V následující tabulce jsou uvedeny stavy zpracování pro importované datové sady:

| State | Význam |
| ----- | ------- |
| Zpracování | Vaše datová sada byla přijata a je zpracovávána. |
| Úspěšný | Vaše datová sada byla ověřena a lze ji nyní použít k sestavení hlasového modelu. |
| Neúspěšný | Vaše datová sada se během zpracování v důsledku mnoha důvodů nezdařila, například chyby souborů, problémy s daty nebo problémy se sítí. |

Po dokončení ověření uvidíte celkový počet odpovídajících projevy pro každou datovou sadu ve sloupci **projevy** . Pokud datový typ, který jste vybrali, vyžaduje segmentaci dlouhého zvuku, tento sloupec odráží jenom projevy, které jsme pro vás segmentoval buď na základě vašich přepisů, nebo prostřednictvím služby přepisu řeči. Můžete ještě více stáhnout datovou sadu, která je ověřená, aby se zobrazily podrobné výsledky projevy úspěšně naimportované a jejich přepisů mapování. Pomocný parametr: dlouhé segmentace zvuku může trvat déle než hodinu, než se zpracování dat dokončí.

U datových sad en-US a zh-CN můžete dál stahovat sestavu pro kontrolu skóre výslovnosti a úrovně hluku pro každé vaše nahrávky. Skóre výslovnosti je rozsah od 0 do 100. Skóre nižší než 70 (normálně) indikuje chybu řeči nebo neshoda skriptu. Hodně zdůraznění může snížit vaše výsledky výslovnosti a ovlivnit vygenerovaný digitální hlas.

Vyšší poměr mezi signálem a hlukem (SNR) znamená nižší šum ve zvukovém prostředí. Můžete obvykle dosáhnout 50 + SNR záznamem na Professional studia. Zvuk s SNRem nižším než 20 může vést k zjevnému šumu ve vygenerovaném hlasu.

Zvažte opětovné zaznamenávání všech projevy s nízkými výslovnostmi nebo nedostatečnými poměry k hluku. Pokud se nemůžete znovu nahrávat, můžete tyto projevy z datové sady vyloučit.

## <a name="build-your-custom-voice-model"></a>Sestavení vlastního hlasového modelu

Po ověření datové sady ji můžete použít k sestavení vlastního hlasového modelu.

1.  Přejděte na **text na řeč > Custom Voice > [název projektu] > školení**.

2.  Klikněte na **výuka model**.

3.  Potom zadejte **název** a **Popis** , který vám usnadní identifikaci tohoto modelu.

    Vyberte název pečlivě. Název, který zde zadáte, bude název, který použijete k zadání hlasu v žádosti o Shrnutí řeči v rámci vstupu SSMLu. Povolena jsou pouze písmena, číslice a znaky interpunkce, například-, \_ a (', '). Pro různé modely hlasu použijte jiné názvy.

    Běžné použití pole **Description** je záznam názvů datových sad, které byly použity k vytvoření modelu.

4.  Na stránce **Vybrat data školení** zvolte jednu nebo více datových sad, které chcete použít pro školení. Před odesláním projevy ověřte jeho počet. Můžete začít s libovolným počtem projevy pro hlasové modely en-US a zh-CN. Pro ostatní národní prostředí musíte vybrat více než 2 000 projevy, aby bylo možné naučit hlas.

    > [!NOTE]
    > Z školení se odeberou duplicitní názvy zvuku. Ujistěte se, že datové sady, které vyberete, neobsahují stejné zvukové názvy v několika souborech. zip.

    > [!TIP]
    > Pro výsledky kvality se vyžaduje použití datových sad ze stejného mluvčího. Pokud datové sady, které jste odeslali pro školení, obsahují celkový počet menší než 6 000 jedinečných projevy, budete svůj hlasový model předávat prostřednictvím techniky syntézy statistických ukazatelů. V případě, že vaše školicí data překročí celkový počet 6 000 jedinečných projevy, zahájíte školicí proces s technikou syntézy zřetězení. Technologie zřetězení obvykle může mít za následek větší přirozený a vyšší přesnost hlasových výsledků. Pokud chcete vytvořit model s nejnovější technologií neuronové TTS, která může vytvořit digitální hlasový ekvivalent veřejně dostupných [hlasů neuronové](language-support.md#neural-voices), [obraťte se na vlastního hlasového týmu](https://go.microsoft.com/fwlink/?linkid=2108737) .

5.  Klikněte na **výuka** a začněte vytvářet svůj hlasový model.

V tabulce školení se zobrazí nová položka, která odpovídá nově vytvořenému modelu. V tabulce se zobrazí také stav: zpracování, úspěch, selhalo.

Zobrazený stav odráží proces převodu datové sady na hlasový model, jak je znázorněno zde.

| State | Význam |
| ----- | ------- |
| Zpracování | Probíhá vytváření hlasového modelu. |
| Úspěšný | Váš hlasový model byl vytvořen a lze jej nasadit. |
| Neúspěšný | Váš hlasový model se v rámci školení nezdařil z důvodu mnoha důvodů, například problémy s nezpracovanými daty nebo problémy se sítí. |

Doba školení se liší v závislosti na objemu zpracovaných zvukových dat. Typický časový rozsah od přibližně 30 minut po stovky projevy až 40 hodin za 20 000 projevy. Po úspěšném školení o modelu ho můžete začít testovat.

> [!NOTE]
> Uživatelé bezplatného předplatného (F0) mohou současně vyškolit jedno písmo hlasu. Standardní předplatné (S0) uživatelé můžou doškolit tři hlasy současně. Pokud dosáhnete limitu, počkejte, dokud alespoň jedno z vašich hlasových písem dokončí školení, a akci opakujte.

> [!NOTE]
> Maximální počet hlasových modelů, které je možné vyškolet na předplatné, je 10 modelů pro uživatele bezplatného předplatného (F0) a 100 pro uživatele Standard Subscription (S0).

Pokud používáte funkci neuronové Voice Training, můžete si vybrat, abyste mohli vytvořit model optimalizovaný pro scénáře streamování v reálném čase nebo model HD neuronové optimalizované pro asynchronní [dlouhodobé](long-audio-api.md)zpracování dat.  

## <a name="test-your-voice-model"></a>Testování hlasového modelu

Po úspěšném vytvoření hlasového písma ho můžete před nasazením pro použití otestovat.

1.  Přejděte na **text na řeč > Custom Voice > [název projektu] > testování**.

2.  Klikněte na **Přidat test**.

3.  Vyberte jeden nebo více modelů, které chcete testovat.

4.  Zadejte text, který chcete mluvit s hlasem. Pokud jste vybrali možnost testování více modelů najednou, bude stejný text použit pro testování různých modelů.

    > [!NOTE]
    > Jazyk textu musí být stejný jako jazyk vašeho hlasového písma. Testovat lze pouze úspěšně proučené modely. V tomto kroku je podporován pouze prostý text.

5.  Klikněte na **Vytvořit**.

Jakmile odešlete žádost o test, vrátíte se na stránku test. Tabulka nyní obsahuje položku, která odpovídá vaší nové žádosti a sloupci Stav. Vysyntetizování řeči může trvat několik minut. Když se sloupec Stav **úspěšně**dožádaný, můžete přehrát zvuk, nebo si stáhnout textový vstup (soubor. txt) a zvukový výstup (soubor. wav) a další Audition k jeho kvalitě.

Výsledky testu můžete také najít na stránce podrobností u jednotlivých modelů, které jste vybrali pro testování. Přejděte na kartu **školení** a kliknutím na název modelu zadejte stránku s podrobnostmi o modelu.

## <a name="create-and-use-a-custom-voice-endpoint"></a>Vytvoření a použití vlastního koncového bodu hlasu

Po úspěšném vytvoření a otestování vašeho hlasového modelu ho nasadíte do vlastního koncového bodu pro převod textu na řeč. Tento koncový bod pak použijete místo obvyklého koncového bodu při vytváření požadavků na převod textu na řeč prostřednictvím REST API. Váš vlastní koncový bod může být volán pouze předplatným, které jste použili k nasazení písma.

Pokud chcete vytvořit nový vlastní hlasový koncový bod, přečtěte si **text na řeč > vlastního nasazení hlasových >**. Vyberte **přidat koncový bod** a zadejte **název** a **Popis** vlastního koncového bodu. Pak vyberte vlastní hlasový model, který chcete přidružit k tomuto koncovému bodu.

Po kliknutí na tlačítko **Přidat** se v tabulce koncového bodu zobrazí položka pro nový koncový bod. Vytvoření instance nového koncového bodu může trvat několik minut. Po **úspěšném**stavu nasazení je koncový bod připravený k použití.

> [!NOTE]
> Uživatelé bezplatného předplatného (F0) můžou mít nasazený jenom jeden model. Standardní předplatné (S0) může vytvořit až 50 koncových bodů, z nichž každý má vlastní hlas.

> [!NOTE]
> Pokud chcete použít vlastní hlas, musíte zadat název hlasového modelu, použít vlastní URI přímo v požadavku HTTP a použít stejné předplatné k předání ověřování služby TTS.

Po nasazení koncového bodu se název koncového bodu zobrazí jako odkaz. Kliknutím na odkaz zobrazíte informace, které jsou specifické pro váš koncový bod, jako je klíč koncového bodu, adresa URL koncového bodu a ukázkový kód.

Online testování koncového bodu je dostupné taky prostřednictvím vlastního hlasového portálu. Chcete-li otestovat koncový bod, vyberte možnost **zkontrolovat koncový bod** na stránce s **podrobnostmi koncového** bodu. Zobrazí se stránka testování koncového bodu. Do textového pole zadejte text, který chcete vymluveného textu (ve formátu prostého textu nebo [SSML](speech-synthesis-markup.md) ). Chcete-li slyšet text mluvený ve vlastním hlasovém písmu, vyberte možnost **Přehrát**. Tato funkce testování se bude účtovat za vaše vlastní použití funkce pro syntézu řeči.

Vlastní koncový bod je funkčně stejný jako standardní koncový bod, který se používá pro požadavky převodu textu na řeč. Další informace najdete v tématu [REST API](rest-text-to-speech.md) .

## <a name="next-steps"></a>Další kroky

* [Příručka: záznam ukázek hlasu](record-custom-voice-samples.md)
* [Reference k rozhraní API pro převod textu na mluvené slovo](rest-text-to-speech.md)
* [Rozhraní API pro dlouhé zvukové zařízení](long-audio-api.md)
