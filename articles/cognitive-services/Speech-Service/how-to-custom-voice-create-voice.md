---
title: Vytvoření vlastní hlasové služby
titleSuffix: Azure Cognitive Services
description: Až budete připraveni nahrát data, přejděte na portál Vlastní hlas. Vytvořte nebo vyberte vlastní hlasový projekt. Projekt musí sdílet správný jazyk/národní prostředí a vlastnosti pohlaví jako data, která chcete použít pro hlasové školení.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: bbe1d651a7d2d2cac1b1aa78b815b2797ad185c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "76717330"
---
# <a name="create-a-custom-voice"></a>Vytvoření vlastního hlasu

V [části Příprava dat pro vlastní hlas](how-to-custom-voice-prepare-data.md)jsme popsali různé typy dat, které můžete použít k trénování vlastního hlasu, a požadavky na jiný formát. Jakmile data připravíte, můžete je začít nahrávat na [portál Vlastní hlas](https://aka.ms/custom-voice-portal)nebo prostřednictvím rozhraní API pro vlastní hlasové školení. Zde popisujeme kroky školení vlastního hlasu prostřednictvím portálu.

> [!NOTE]
> Tato stránka předpokládá, že jste si přečetli [Možnost Začít s vlastním hlasem](how-to-custom-voice.md) a Připravit data pro vlastní [hlas](how-to-custom-voice-prepare-data.md)a vytvořili jste vlastní hlasový projekt.

Zkontrolujte jazyky podporované pro vlastní hlas: [jazyk pro přizpůsobení](language-support.md#customization).

## <a name="upload-your-datasets"></a>Nahrání datových sad

Až budete připraveni nahrát data, přejděte na [portál Vlastní hlas](https://aka.ms/custom-voice-portal). Vytvořte nebo vyberte vlastní hlasový projekt. Projekt musí sdílet správný jazyk/národní prostředí a vlastnosti pohlaví jako data, která chcete použít pro hlasové školení. Vyberte `en-GB` například, zda jsou zvukové nahrávky, které máte, provedeny v angličtině s britským přízvukem.

Přejděte na kartu **Data** a klikněte na **Nahrát data**. V průvodci vyberte správný datový typ, který odpovídá připravenému typu.

Každá nahraná datová sada musí splňovat požadavky na daný datový typ. Před nahráním je důležité data správně naformátovat. Tím zajistíte, že data budou přesně zpracována službou Custom Voice. Přejděte na [Připravit data pro vlastní hlas](how-to-custom-voice-prepare-data.md) a ujistěte se, že vaše data byla správně naformátována.

> [!NOTE]
> Uživatelé bezplatného předplatného (F0) mohou nahrát dvě datové sady současně. Uživatelé standardního předplatného (S0) mohou nahrát pět datových sad současně. Pokud dosáhnete limitu, počkejte, dokud alespoň jedna z vašich datových sad nedokončí import. Opakujte.

> [!NOTE]
> Maximální počet povolených datových sad na jedno předplatné je 10 souborů ZIP pro bezplatné předplacené uživatele (F0) a 500 pro uživatele se standardním předplatným (S0).

Datové sady jsou automaticky ověřeny, jakmile stisknete tlačítko pro nahrávání. Ověření dat zahrnuje řadu kontrol zvukových souborů za účelem ověření jejich formátu, velikosti a vzorkovací frekvence. Opravte případné chyby a odešlete je znovu. Při úspěšném spuštění požadavku na import dat byste měli vidět v tabulce dat položku, která odpovídá datové sadě, kterou jste právě odeslali.

V následující tabulce jsou uvedeny stavy zpracování pro importované datové sady:

| Stav | Význam |
| ----- | ------- |
| Zpracování | Vaše datová sada byla přijata a je zpracovávána. |
| Úspěch | Vaše datová sada byla ověřena a nyní může být použita k vytvoření hlasového modelu. |
| Failed | Vaše datová sada se během zpracování nezdařila z mnoha důvodů, například chyby souborů, problémy s daty nebo problémy se sítí. |

Po dokončení ověření uvidíte celkový počet odpovídajících promluv pro každou z vašich datových sad ve sloupci **Utterances.** Pokud vybraný datový typ vyžaduje segmentaci dlouhého zvuku, tento sloupec odráží pouze projevy, které jsme pro vás segmentovali, a to buď na základě přepisů, nebo prostřednictvím služby přepisu řeči. Můžete dále stáhnout ověřenou datovou sadu a zobrazit podrobné výsledky úspěšně importovaných promluv a jejich přepisy mapování. Tip: segmentace dlouhého zvuku může zpracování dat trvat déle než hodinu.

U datových sad en-US a zh-CN si můžete dále stáhnout sestavu a zkontrolovat skóre výslovnosti a hladinu šumu pro každou z vašich nahrávek. Skóre výslovnosti se pohybuje od 0 do 100. Skóre nižší než 70 obvykle označuje chybu řeči nebo neshodu skriptů. Silný přízvuk může snížit skóre výslovnosti a ovlivnit generovaný digitální hlas.

Vyšší poměr signálu k šumu (SNR) indikuje nižší šum ve zvuku. Obvykle můžete dosáhnout 50 + SNR nahráváním v profesionálních studiích. Zvuk s SNR pod 20 může mít za následek zřejmý šum v generovaném hlasu.

Zvažte opětovné zaznamenání všech promluv s nízkým skóre výslovnosti nebo špatným poměrem signálu k šumu. Pokud nelze znovu zaznamenat, můžete vyloučit tyto projevy z datové sady.

## <a name="build-your-custom-voice-model"></a>Vytvořte si vlastní hlasový model

Po ověření datové sady ji můžete použít k vytvoření vlastního hlasového modelu.

1.  Přejděte **na text na řeč > vlastní hlasové > školení**.

2.  Klepněte na **položku Model vlaku**.

3.  Dále zadejte **název** a **popis,** které vám pomohou identifikovat tento model.

    Pečlivě si vyberte název. Název, který zde zadáte, bude název, který použijete k určení hlasu v žádosti o syntézu řeči jako součást vstupu SSML. Povolena jsou pouze písmena, čísla a několik \_interpunkčních znaků, například -, , a (', '). Pro různé hlasové modely používejte různé názvy.

    Pole **Popis** se běžně používá k zaznamenání názvů datových sad, které byly použity k vytvoření modelu.

4.  Na stránce **Vybrat trénovací data** zvolte jednu nebo více datových sad, které chcete použít pro trénování. Před odesláním zkontrolujte počet promluv. Můžete začít s libovolnýpočet projevy pro en US a zh-CN hlasové modely. Pro ostatní národní prostředí je nutné vybrat více než 2 000 projevy, aby bylo možné trénovat hlas.

    > [!NOTE]
    > Duplicitní zvukové názvy budou odebrány z tréninku. Ujistěte se, že vybrané datové sady neobsahují stejné zvukové názvy ve více souborech ZIP.

    > [!TIP]
    > Použití datových sad ze stejného reproduktoru je vyžadováno pro dosažení výsledků kvality. Pokud datové sady, které jste odeslali k trénování, obsahují celkový počet méně než 6 000 různých promluv, trénete hlasový model pomocí techniky statistické parametrické syntézy. V případě, že vaše trénovací data překročí celkový počet 6 000 různých projevy, zahájíte proces školení s technikou syntézy zřetězení. Technologie zřetězení může obvykle vést k přirozenějším a vyšší věrností hlasových výsledků. Pokud chcete trénovat model s nejnovější technologií Neural TTS, která dokáže vytvořit digitální hlas odpovídající veřejně [dostupným neurálním hlasům](language-support.md#neural-voices), [obraťte](https://go.microsoft.com/fwlink/?linkid=2108737) se na tým Vlastní hlas .

5.  Kliknutím na **Trénovat** začněte vytvářet svůj hlasový model.

Tabulka Školení zobrazí novou položku, která odpovídá tomuto nově vytvořenému modelu. V tabulce se také zobrazí stav: Zpracování, Úspěšné, Neúspěšné.

Zobrazený stav odráží proces převodu datové sady na hlasový model, jak je znázorněno zde.

| Stav | Význam |
| ----- | ------- |
| Zpracování | Vytváří se váš hlasový model. |
| Úspěch | Váš hlasový model byl vytvořen a lze jej nasadit. |
| Failed | Váš hlasový model selhal při trénování z mnoha důvodů, například neviditelné problémy s daty nebo problémy se sítí. |

Doba tréninku se liší v závislosti na objemu zpracovávaných zvukových dat. Typické časy se pohybují od asi 30 minut pro stovky projevy na 40 hodin pro 20 000 projevy. Jakmile je vaše model školení úspěšné, můžete začít testovat.

> [!NOTE]
> Uživatelé bezplatného předplatného (F0) mohou trénovat jedno hlasové písmo současně. Uživatelé standardního předplatného (S0) mohou trénovat tři hlasy současně. Pokud dosáhnete limitu, počkejte, dokud alespoň jedno z vašich hlasových písem nedokončí školení, a akci opakujte.

> [!NOTE]
> Maximální počet hlasových modelů, které mohou být trénované na jedno předplatné, je 10 modelů pro bezplatné uživatele předplatného (F0) a 100 pro uživatele se standardním předplatným (S0).

Pokud používáte funkci školení neuronové hlas, můžete zvolit trénovat model optimalizovaný pro scénáře streamování v reálném čase nebo neuronový model HD optimalizovaný pro asynchronní [syntézu dlouhého zvuku](long-audio-api.md).  

## <a name="test-your-voice-model"></a>Testování hlasového modelu

Po úspěšném sestrojit hlasové písmo, můžete jej otestovat před nasazením pro použití.

1.  Přejděte **na převod textu na řeč > vlastní hlasové > testování**.

2.  Klepněte na **tlačítko Přidat test**.

3.  Vyberte jeden nebo více modelů, které chcete testovat.

4.  Zadejte text, který má hlas (hlasy) promluvit. Pokud jste zvolili testování více modelů najednou, stejný text bude použit pro testování pro různé modely.

    > [!NOTE]
    > Jazyk textu musí být stejný jako jazyk hlasového písma. Testovat lze pouze úspěšně trénované modely. V tomto kroku je podporován pouze prostý text.

5.  Klikněte na **Vytvořit**.

Po odeslání žádosti o test se vrátíte na zkušební stránku. Tabulka nyní obsahuje položku, která odpovídá vašemu novému požadavku a sloupci stavu. Syntetizovat řeč může trvat několik minut. Když je ve **sloupci**stavu uvedeno úspěšné , můžete přehrát zvuk nebo stáhnout textový vstup (soubor .txt) a zvukový výstup (soubor WAV) a dále se uchýlení o jeho kvalitu.

Výsledky testů můžete také najít na stránce podrobností jednotlivých modelů, které jste vybrali pro testování. Přejděte na kartu **Školení** a kliknutím na název modelu zadejte stránku podrobností modelu.

## <a name="create-and-use-a-custom-voice-endpoint"></a>Vytvoření a použití vlastního hlasového koncového bodu

Po úspěšném vytvoření a otestování hlasového modelu jej nasadíte do vlastního koncového bodu převodu textu na řeč. Potom použít tento koncový bod místo obvyklého koncového bodu při vytváření požadavků na řeč prostřednictvím rozhraní REST API. Váš vlastní koncový bod lze volat pouze podle předplatného, které jste použili k nasazení písma.

Chcete-li vytvořit nový vlastní koncový bod hlasu, přejděte **na informace o převodu textu na řeč > vlastní hlasové > nasazení**. Vyberte **Přidat koncový bod** a zadejte **název** a **popis** vlastního koncového bodu. Pak vyberte vlastní hlasový model, který chcete přidružit k tomuto koncovému bodu.

Po klepnutí na tlačítko **Přidat** se v tabulce koncového bodu zobrazí položka pro nový koncový bod. Může trvat několik minut k vytvoření instance nového koncového bodu. Pokud je stav nasazení **úspěšné**, koncový bod je připraven k použití.

> [!NOTE]
> Uživatelé bezplatného předplatného (F0) mohou mít nasazený pouze jeden model. Uživatelé standardního předplatného (S0) mohou vytvořit až 50 koncových bodů, každý s vlastním hlasem.

> [!NOTE]
> Chcete-li použít vlastní hlas, musíte zadat název hlasového modelu, použít vlastní identifikátor URI přímo v požadavku HTTP a použít stejné předplatné k předání ověřování služby TTS.

Po nasazení koncového bodu se název koncového bodu zobrazí jako odkaz. Kliknutím na odkaz zobrazíte informace specifické pro koncový bod, jako je klíč koncového bodu, adresa URL koncového bodu a ukázkový kód.

Online testování koncového bodu je také k dispozici prostřednictvím vlastního hlasového portálu. Chcete-li otestovat koncový bod, zvolte **Zkontrolovat koncový bod** ze stránky **podrobností koncového bodu.** Zobrazí se stránka testování koncového bodu. Zadejte text, který má být vysloven (ve formátu prostého textu nebo [SSML](speech-synthesis-markup.md) do textového pole. Chcete-li slyšet text mluvený ve vlastním hlasovém písmu, vyberte **Přehrát**. Tato testovací funkce bude účtována proti využití vlastní syntézy řeči.

Vlastní koncový bod je funkčně identický se standardním koncovým bodem, který se používá pro požadavky na řeč. Další informace najdete v [tématu REST API.](rest-text-to-speech.md)

## <a name="next-steps"></a>Další kroky

* [Průvodce: Záznam hlasových ukázek](record-custom-voice-samples.md)
* [Odkaz na rozhraní API pro převod textu na řeč](rest-text-to-speech.md)
* [Dlouhé zvukové rozhraní API](long-audio-api.md)
