---
title: Vytvořit vlastní hlasové funkce – hlasové služby
titlesuffix: Azure Cognitive Services
description: Až budete připravení nahrát data, přejděte na portál pro vlastní hlasové. Vytvořte nebo vyberte vlastní hlasové projektu. Projekt musí sdílet správné jazyka/národního prostředí a vlastnosti pohlaví jako data máte v úmyslu použít pro trénování hlasu.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: erhopf
ms.openlocfilehash: fad69c4108d747c44eccf37b81adf2c7c615cb58
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65156843"
---
# <a name="create-a-custom-voice"></a>Vytvořit vlastní hlasový vstup

V [připravit data pro vlastní hlasové](how-to-custom-voice-prepare-data.md), jsme popsané různé datové typy, které můžete použít k trénování vlastních hlasový vstup a požadavky na jiný formát. Jakmile dokončíte přípravu dat, můžete začít nahrají je do [vlastní hlasové portál](http://aka.ms/custom-voice-portal), nebo prostřednictvím rozhraní API pro vlastní hlasové školení. Tady popisujeme kroky přípravy vlastní hlasové prostřednictvím portálu.

> [!NOTE]
> Tato stránka předpokládá, že jste si přečetli [začít pracovat s vlastní hlasové](how-to-custom-voice.md) a [připravit data pro vlastní hlasové](how-to-custom-voice-prepare-data.md)a vytvořili vlastní hlasové projektu.

Zkontrolujte jazyky podporované pro vlastní hlasové: [jazyk pro přizpůsobení](language-support.md#customization).

## <a name="upload-your-datasets"></a>Nahrání datových sad

Až budete připravení nahrát data, přejděte [vlastní hlasové portál](http://aka.ms/custom-voice-portal). Vytvořte nebo vyberte vlastní hlasové projektu. Projekt musí sdílet správné jazyka/národního prostředí a vlastnosti pohlaví jako data máte v úmyslu použít pro trénování hlasu. Vyberte například `en-GB` Pokud zvukových záznamů, je nutné se provádí v angličtině s UK zvýraznění.

Přejděte **Data** kartě a klikněte na tlačítko **nahrání dat**. V průvodci vyberte správného datového typu, který odpovídá, co jste připravili.

Každou datovou sadu, které nahrajete, musí splňovat požadavky pro datový typ, který zvolíte. Je důležité správně formátovat data před odesláním. Tím se zajistí, že se pomocí vlastní hlasové služby přesně zpracovat data. Přejděte na [připravit data pro vlastní hlasové](how-to-custom-voice-prepare-data.md) a ujistěte se, že vaše data byla správně naformátovaná.

> [!NOTE]
> Uživatelé bezplatné předplatné (F0) najednou můžete nahrát dvě datové sady. Standardní předplatné (S0) mohou uživatelé odeslat pět datových sad současně. Pokud limit překročíte, počkejte aspoň jeden z datových sad dokončení importu. Pak to zkuste znovu.

> [!NOTE]
> Maximální počet datových sadách povoleno importovat na jedno předplatné je 10 zip soubory zdarma uživatelé předplatného (F0) a 500 uživatelů standardní předplatné (S0).

Datové sady se automaticky ověří po kliknutí na tlačítko Uložit. Ověření dat obsahuje řadu kontrol zvukové soubory, chcete-li ověřit jejich formát souboru, velikost a vzorkovací frekvenci. Opravte případné chyby a znovu odešlete. Při importu dat požadavek úspěšně vyvolán, měli byste vidět položku v tabulce dat, která odpovídá datovou sadu, kterou jste právě nahráli.

V následující tabulce jsou uvedeny stavy zpracování pro importované datové sady:

| Stav | Význam |
| ----- | ------- |
| Zpracování | Vaše datová sada byla přijata a se zpracovává. |
| Úspěch | Vaše datová sada je potvrzená a můžete nyní používat k sestavení modelu hlasu. |
| Selhalo | Vaše datová sada selhal při zpracování z mnoha důvodů, například soubor chyby, problémy dat nebo problémů se sítí. |

Po dokončení ověření můžete zobrazit celkový počet odpovídajících projevy pro každý z datových sad v **projevy** sloupce. Pokud datový typ, který jste zvolili vyžaduje dlouho zvuk segmentace, v tomto sloupci jsou pouze údaje projevy, které můžeme mít segmentované, buď v závislosti na své záznamy nebo prostřednictvím služby určené k transkripci řeči. Můžete dál stáhnout datovou sadu ověřen, chcete-li zobrazit podrobné výsledky projevy, které byly úspěšně naimportovány a jejich mapování přepisy. Tip: více než jedna hodina dokončení zpracování dat může trvat dlouho zvuk segmentace.

Pro datové sady en US a zh-CN můžete dál stáhnout sestavu zkontrolovat skóre výslovnost a úroveň šumu pro každý z vašich nahrávek. Výslovnost skóre je v rozsahu 0 až 100. Skóre pod 70 obvykle znamená neshodou řeči chyba nebo skriptu. Silná zvýraznění může snížit vaše skóre výslovnost a ovlivnit generované digitální hlasu.

Vyšší poměr signálu šumu (SNR) znamená nižší šumu v zvuku. Obvykle můžete oslovit SNR víc než 50 zaznamenáváním na profesionální aplikace. Zvuk se SNR pod 20 může vést k zřejmé šumu v generované hlasu.

Vezměte v úvahu znovu záznam jakékoli projevy s nízkou výslovnost skóre nebo nízký poměr signálu šumu. Pokud nejde znovu záznam, může být těchto projevů vyloučit z datové sady.

## <a name="build-your-custom-voice-model"></a>Sestavit model ve vaší vlastní hlasové

Po ověření datové sady můžete vytvářet vlastní hlasové modelu.

1.  Přejděte do **převod textu na řeč > vlastní hlasové > školení**.

2.  Klikněte na tlačítko **Train model**.

3.  Pak zadejte **název** a **popis** ke snadnější identifikaci tohoto modelu.

    Zvolte název pečlivě. Název, které zadáte tady bude název, který se používá k určení hlasu ve vaší žádosti pro syntézu řeči, jako součást SSML vstup. Pouze písmena, číslice a pár znaků interpunkce jako - \_a (,) jsou povoleny. Použijte jiné názvy pro jiný hlas modely.

    Běžně **popis** pole je záznam názvech datových sad, které byly použity k vytvoření modelu.

4.  Z **vyberte trénovacích dat** zvolte jednu nebo více datových sad, které chcete použít k trénování. Zkontrolujte počet projevy před jejich odesláním. Můžete začít s libovolným počtem projevy pro modely en US a zh-CN hlasu. Pro jiné národní prostředí je nutné vybrat více než 2 000 projevy do ji moci natrénovat hlasový vstup.

    > [!NOTE]
    > Duplicitní názvy zvuku se odebere z školení. Ujistěte se, že datové sady, kterou vyberete neobsahují zvukový stejné názvy do více souborů ZIP.

    > [!TIP]
    > Pomocí datové sady ze stejné mluvčího se vyžaduje pro kvality výsledků. Při odeslání pro trénovací datové sady obsahují celkový počet méně než 6 000 jedinečných projevy, bude natrénování modelu hlas prostřednictvím statistické ukazatelů syntézu technika. V případě, kdy trénovacích dat překročí celkový počet 6 000 jedinečných projevy se spustí řízený proces školení s techniku syntézu zřetězení. Zřetězení technologie obvykle může vést k hlasu přirozenější a vyšší přesnosti výsledků. [Obraťte se na tým vlastní hlasové](mailto:speechsupport@microsoft.com) Pokud budete chtít vyškolíme model pomocí nejnovějších technologiích neuronových sítí převod textu na ŘEČ, které mohou způsobit digitální hlasový vstup ekvivalentní veřejně dostupné [neuronových sítí hlasy](language-support.md#neural-voices).

5.  Klikněte na tlačítko **trénování** můžete začít vytvářet svůj hlas model.

Školení tabulce nový záznam, který odpovídá na tuto nově vytvořenou modelu. V tabulce se také zobrazí stav: Zpracování, bylo úspěšné, neúspěšné.

Stav, který se zobrazí odráží proces převodu datové sady do modelu hlasu, jak je znázorněno zde.

| Stav | Význam |
| ----- | ------- |
| Zpracování | Vytváří se váš model hlasu. |
| Úspěch | Váš model hlasové vytvořilo a je možné nasadit. |
| Selhalo | Váš model hlasové selhal v školení z mnoha důvodů, například nezobrazený data problémy nebo problémů se sítí. |

Školení čas se liší v závislosti na objemu zpracovaných dat zvuku. Typické doby rozsahu o 30 minut, než stovky projevy až 40 hodin pro 20 000 projevy. Jakmile se úspěšně trénování modelu, můžete spustit a otestovat ho.

> [!NOTE]
> Uživatelé bezplatné předplatné (F0) současně trénovat hlasových písem. Standardní předplatné (S0) uživatelů současně trénovat tři hlasy. Pokud limit překročíte, počkejte aspoň jeden hlasová písma dokončení školení a pak to zkuste znovu.

> [!NOTE]
> Maximální počet modelů hlasové povolené zaškolení na jedno předplatné je 10 modelů pro uživatele bezplatné předplatné (F0) až 100 uživatelů standardní předplatné (S0).

## <a name="test-your-voice-model"></a>Testování modelu hlasu

Po úspěšném sestavení hlasového písma, takže ji můžete otestovat před nasazením pro použití.

1.  Přejděte do **převod textu na řeč > vlastní hlasové > testování**.

2.  Klikněte na tlačítko **testu přidat**.

3.  Vyberte jeden nebo více modelů, které chcete testovat.

4.  Zadejte text, který má voice(s) mluvit. Pokud jste vybrali k otestování několik modelů v jednom okamžiku, použije se stejný text pro testování různých modelů.

    > [!NOTE]
    > Jazyk textu musí být stejný jako jazyk hlasového písma. Pouze úspěšně trénované modely můžete otestovat. V tomto kroku se podporuje jenom prostý text.

5.  Klikněte na možnost **Vytvořit**.

Po odeslání testovací požadavek, vrátíte se k testovací stránce. Tabulka teď obsahuje položku, která odpovídá vaší novou žádost a ve sloupci stav. Může trvat několik minut tak, aby odpovídaly řeči. Když uvádí, že sloupec Stav na **Succeeded**, můžete přehrát zvuk nebo stáhnout textové zadání (soubor .txt) a zvuku výstup (soubor WAV) a další audition ten kvality.

Výsledky testů můžete také najít na stránce podrobností každé modelů, které jste vybrali pro testování. Přejděte **školení** kartu a klikněte na název modelu přejděte na stránku podrobností modelu.

## <a name="create-and-use-a-custom-voice-endpoint"></a>Vytvoření a použití vlastní hlasové endpoint

Po jste úspěšně vytvořili a otestování modelu hlasové, nasaďte do vlastního koncového bodu převodu textu na řeč. Pak použijete tento koncový bod místo obvyklých koncového bodu při vytváření žádosti o převod textu na řeč přes rozhraní REST API. Vlastní koncový bod lze volat pouze podle předplatného, který jste použili k nasazení písmo.

Pokud chcete vytvořit nový koncový bod vlastní hlasové, přejděte na **převod textu na řeč > vlastní hlasové > nasazení**. Vyberte **přidat koncový bod** a zadejte **název** a **popis** pro váš vlastní koncový bod. Vyberte vlastní hlasové model, který chcete přidružit k tomuto koncovému bodu.

Po klepnutí **přidat** tlačítko, v tabulce koncových bodů se zobrazí záznam pro nový koncový bod. Může trvat několik minut pro vytvoření instance nového koncového bodu. Pokud je stav nasazení **Succeeded**, koncový bod je připraven k použití.

> [!NOTE]
> Uživatelé bezplatné předplatné (F0) mohou mít pouze jeden model nasadit. Uživatelé Standard předplatného (S0) můžete vytvořit až 50 koncové body, každý s vlastním hlasem vlastní.

> [!NOTE]
> Pokud chcete použít vlastní hlasové, musíte zadat název modelu hlasové, použijte vlastní identifikátor URI přímo v jednom požadavku HTTP a používat stejné předplatné ověřování převod textu na ŘEČ služby předávání.

Po nasazení je váš koncový bod, název koncového bodu se zobrazí jako odkaz. Klikněte na odkaz zobrazíte informace o konkrétní do vašeho koncového bodu, jako jsou klíče koncového bodu, adresa URL koncového bodu a ukázky kódu.

Online testování koncového bodu je také k dispozici prostřednictvím portálu pro vlastní hlasové. Chcete-li otestovat váš koncový bod, zvolte **zkontrolujte koncový bod** z **podrobnosti koncového bodu** stránky. Koncový bod testování stránky se zobrazí. Zadejte text, který budou (buď jako prostý text nebo [SSML formátu](speech-synthesis-markup.md) v textovém poli. Pokud chcete slyšet text, kterým se mluví ve vaší vlastní hlasové písmo, vyberte **Přehrát**. Tato funkce testování se bude poplatek využití syntézu řeči vlastní.

Vlastní koncový bod je funkčně stejný jako standardní koncový bod, který se používá pro převod textu na řeč požadavky. Zobrazit [rozhraní REST API](rest-text-to-speech.md) Další informace.

## <a name="next-steps"></a>Další postup

* [Průvodce: Záznam vašich ukázek hlasu](record-custom-voice-samples.md)
* [Převod textu na řeč reference k rozhraní API](rest-text-to-speech.md)
