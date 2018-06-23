---
title: Co je vlastní hlasové? -Služby azure kognitivní | Microsoft Docs
description: Tento článek přehledy Microsoft převod textu na řeč hlasové přizpůsobení, které vám umožní vytvořit hlasu rozpoznatelném, jeden z typu značky.
services: cognitive-services
author: noellelacharite
manager: nolach
ms.service: cognitive-services
ms.topic: article
ms.date: 05/07/2018
ms.author: nolach
ms.openlocfilehash: ad5af799fd46dc51b85432999f986de8cdb056ec
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343602"
---
# <a name="creating-custom-voice-fonts"></a>Vytváření vlastní hlasové písem

Přizpůsobení hlasové Microsoft převod textu na řeč (převod textu na ŘEČ) umožňuje vytvářet rozpoznatelném, jeden z typu hlasu pro vaší značkou: *hlasové písma.* 

Vytvoření vaší hlasové písma, ujistěte se, studio záznam a nahrajte související skripty, jako jsou Cvičná data. Služba potom vytvoří model jedinečný hlasové přizpůsobená pro nahrávání. Pak můžete toto písmo hlasové syntetizovat řeči. 

Můžete začít s malé množství dat pro testování konceptu. Další data zadáte, čím více, ale přirozený a professional váš hlas vyznívá.

Přizpůsobení hlasu je k dispozici pro angličtinu (en US) a kontinentálním čínština (zh-CN).

## <a name="prerequisites"></a>Požadavky

Převod textu na řeč hlasová přizpůsobení funkce je aktuálně v soukromém náhledu. [Vyplňte formulář žádosti](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0N8Vcdi8MZBllkZb70o6KdURjRaUzhBVkhUNklCUEMxU0tQMEFPMjVHVi4u) pro přístup.

Budete také muset:

* Účet Azure ([ZDARMA zaregistrovat](https://azure.microsoft.com/free/ai/) pokud ji ještě nemáte).

* Předplatné služby řeči. [Vytvořit](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices) Pokud jste tak ještě neučinili.

    ![Vytvořit panel](media/custom-voice/create-panel.png)

Po vytvoření předplatného, můžete najít dva klíče předplatné na panelu Rychlý start nebo na panelu přehled nové předplatné. Můžete použít buď klíč.

Nakonec připojte vaše předplatné na portál pro vlastní hlasové následujícím způsobem.

1. Přihlaste se k [vlastní hlasové portál](https://customvoice.ai) pomocí stejného účtu Microsoft používá k aplikování pro přístup.

2. Přejděte na 'Předplatná' název účtu vpravo nahoře.

    ![Předplatná](media/custom-voice/subscriptions.png)

3. Na stránce 'předplatných, zvolte připojit stávající předplatné.

     ![Připojit stávající předplatné](media/custom-voice/connect-existing-sub.png)

4. Vložte svůj klíč předplatného do tabulky, jak je uvedeno níže.

     ![Přidat předplatné](media/custom-voice/add-subscription.png)

Jste připravení přejít!

## <a name="prepare-recordings-and-transcripts"></a>Příprava záznamy a přepisy

Datové sady školení hlasu se skládá z sadu zvukové soubory, společně s textového souboru obsahujícího přepisy všech těchto zvukových souborů.

Můžete připravit tyto soubory v obou směrech: buď napsat skript a jej číst talentu hlasové, nebo použijte veřejně dostupné zvuk a transcribe je na text. V takovém případě upravte disfluencies z zvukových souborů, například "ální" a dalších zvuků výplň, zadrhává, mumbled slova nebo mispronunciations.

K vytvoření dobrý hlasové písmo, je důležité, aby záznamy se provádějí v tiché místnosti s vysoce kvalitní mikrofon. Konzistentní svazku hovořícího míra, hovořícího výšky a výrazovou zkoušky prezentace řeči jsou nezbytné pro vytváření kvalitních digitální hlasu. K vytvoření hlasu pro použití v provozním prostředí, doporučujeme, že používáte professional záznam studio a hlasové talentu.

### <a name="audio-files"></a>Zvukové soubory

Zvukové soubory, musí obsahovat jeden utterance (například jeden věty nebo jeden zapnout dialogové okno systému). Všechny soubory musí být ve stejném jazyce (vícejazyčný vlastní hlasy se nepodporují). Zvukové soubory také každý musí mít jedinečný číselný název souboru s příponou názvu souboru `.wav`.

Zvukové soubory měli připravit následujícím způsobem. Ostatní formáty nejsou podporovány a budou odmítnuty.

| **Vlastnost** | **Hodnota** |
| ------------ | --------- |
| Formát souboru  | RIFF (WAV)|
| Míra vzorkování| 16 000 Hz |
| Kanály     | 1 (monophonic)  |
| Ukázka formátu| PCM, 16 bitů |
| Název souboru    | Číselné s `.wav` rozšíření |
| Formát archivu| ZIP      |
| Archiv maximální velikost|200 MB|

Umístěte sadu zvukové soubory do jediné složky bez podadresáře a balíček celý nastavit jako jeden soubor archivu ZIP.

> [!NOTE]
> Na portálu aktuálně importuje ZIP archivy až 200 MB. Však může být několik archivů nahrán. Maximální počet datových sad, které jsou povoleny je že 10 ZIP soubory bezplatné předplatné uživatelů a 50 pro standardní předplatné uživatele.

### <a name="transcripts"></a>Přepisy

Přepis soubor je prostý textového souboru Unicode (UTF-16 little endian). Každý řádek souboru přepis musí mít název zvukového souboru, za nímž následuje znaku tabulátoru (bod kódu 9) a nakonec jeho přepis. Jsou povoleny žádné prázdné řádky.

Příklad:

```
0000000001  This is the waistline, and it's falling.
0000000002  We have trouble scoring.
0000000003  It was Janet Maslin.
```

Systém vlastní hlasové normalizuje přepisy převod textu na malá a odebráním nadbytečné interpunkce. Je důležité, že přepisy jsou 100 % přesností na odpovídající zvukových záznamů.

> [!TIP]
> Při vytváření produkční převod textu na řeč hlasy, vyberte utterances (nebo skripty zápisu) vzhledem k tomu výslovnosti pokrytí a efektivitu.

## <a name="upload-your-datasets"></a>Nahrát vaše datové sady

Jakmile připravíte zvukový soubor archivu a přepisy, odešlete prostřednictvím [vlastní hlasové služby portálu](https://customvoice.ai).

> [!NOTE]
> Datové sady nelze upravit, až se nahrají. Pokud jste zapomněli zahrnout přepisy některých zvukových souborů, například nebo neúmyslně zvolte nesprávný pohlaví, je potřeba znovu načíst celou datovou sadu. Zkontrolujte nastavení a datové sady důkladně před zahájením nahrávání.

1. Přihlaste se k portálu.

2. Zvolte **Data** v části vlastní hlasové na hlavní stránce. 

    ![Moje projekty](media/custom-voice/my-projects.png)

    Tabulka Moje hlasové Data se zobrazí. Ho je prázdný, pokud ještě jste zatím neodeslali žádné datové sady hlasu.

3. Klikněte na tlačítko **importovat data** otevřete stránku pro odesílání nová datová sada.

    ![Import hlasové dat](media/custom-voice/import-voice-data.png)

4. Zadejte název a popis v polí. 

5. Vyberte národní prostředí pro váš hlas písem. Ujistěte se, že informace o národním prostředí odpovídá jazyku záznam dat a skripty. 

6. Vyberte pohlaví mluvčího jejichž hlasové, kterou používáte.

7. Vyberte skript a zvukových souborů pro nahrání. 

8. Klikněte na tlačítko **Import** odeslat data. U větších datových sad importu může trvat několik minut.

> [!NOTE]
> Bezplatné předplatné uživatelů můžete nahrát dvě datové sady v čase. Uživatelé Standard předplatného můžete nahrát současně pět datových sad. Pokud dosáhnete limitu, počkejte alespoň jeden z vaše datové dokončení importu a potom to zkuste znovu.

Po dokončení nahrávání se zobrazí znovu Moje hlasové dat tabulky. Měli byste vidět položku, která odpovídá na právě nahráli datovou sadu. 

Datové sady se automaticky ověří po odeslání. Ověřování dat zahrnuje řadu kontrol zvukových souborů a ověřit jejich formát souboru, velikost a míry vzorkování. Kontroly na soubory přepis Zkontrolujte formát souboru a provádět některé normalizaci text. Utterances jsou přepsány použití funkce rozpoznávání řeči a výsledný text se porovná se přepis, které jste zadali.

![Moje hlasových dat.](media/custom-voice/my-voice-data.png)

V následující tabulce jsou uvedeny stavy zpracování pro importované datové sady. 

| Stav | Význam
| ----- | -------
| `NotStarted` | Byla přijata datovou sadu a zařazených do fronty pro zpracování
| `Running` | Vaše datová sada je ověřován
| `Succeeded` | Datovou sadu byl ověřen a může být teď použít k sestavení hlasové písma

Po dokončení ověření zobrazí celkový počet odpovídajících utterances pro všechny vaše datové sady ve sloupci Utterance.

Můžete si stáhnout sestavu a zkontrolujte výslovnosti skóre a úroveň šumu pro každou z vaší záznamy. Rozsahy skóre výslovnosti od 0 do 100; skóre nižší než 70 obvykle znamená neshody řeči chyby nebo skriptu. Velkou zvýraznění může snížit vaše výslovnosti skóre a ovlivnit generovaný digitální hlasu.

Vyšší poměr signál šumu (SNR) označuje nižší šumu v vaší zvukovém souboru. Obvykle můžete obrátit SNR 50 + záznam prostřednictvím professional studia. Zvuk se SNR pod 20 může způsobit zřejmé šumu v generované hlasu.

Vezměte v úvahu znovu záznam všech utterances s nízkou výslovnosti skóre nebo nízký signál šumu poměry. Pokud znovu záznam není možné, může vyloučit tyto utterances z datovou sadu.

## <a name="build-your-voice-font"></a>Sestavení vaší hlasové písma

Jakmile byl ověřen datovou sadu, můžete vytvořit vaše vlastní hlasové písma. 

1. Zvolte **modely** v rozevírací nabídce "Vlastní hlasové". 
 
    Zobrazí se v tabulce Moje hlasové písem se seznamem všechny vlastní hlasové písma, které jste už vytvořili.

1. Klikněte na tlačítko **vytvořit hlasy** pod názvem tabulky. 

    Zobrazí se stránka pro vytvoření hlasové písmo. V prvním řádku tabulky se zobrazuje aktuální národní prostředí. Změňte národní prostředí pro vytvoření hlasu v jiném jazyce. Národní prostředí musí být stejný jako datové sady používá k vytvoření hlasu.

1. Stejně jako při, který jste nahráli datovou sadu, zadejte název a popis, který vám pomůže identifikovat tento model. 

    Název, který zde zadáte, bude název, který použijete k určení hlasu ve vaší žádosti pro rozpoznávání řeči souhrnnou jako součást SSML vstupu, takže zvolte pečlivě. Pouze písmena, číslice a pár znaků interpunkce jako '-', '_' '(','), jsou povoleny.

    Běžně se používají pole popisu je záznam názvy datových sad, které jste použili k vytvoření modelu.

1. Zvolte pohlaví váš hlas písma. Musí se shodovat pohlaví datové sady.

1. Vyberte datových sad, kterou chcete použít pro trénování hlasové písmo. Všechny datové sady použité musí být ze stejné mluvčího.

1. Klikněte na tlačítko **vytvořit** zahajte proces vytváření váš hlas písma.

    ![Vytvoření modelu](media/custom-voice/create-model.png)

Nový model se zobrazí v tabulce Moje hlasové písem. 

![Vlastní hlasové písma](media/custom-voice/my-voice-fonts.png)

Zobrazí stav odráží proces převodu datovou sadu pro hlasové písmo, jak je vidět tady.

| Stav | Význam
| ----- | -------
| `NotStarted` | Vaši žádost o vytvoření písma hlasu je zařadit do fronty pro zpracování
| `Running` | Probíhá vytváření váš hlas písma
| `Succeeded` | Vaše písma hlasu byl vytvořen a mohou být nasazeny

Cvičení čas se liší v závislosti na objemu zvuk zpracování dat. Typické časy v rozsahu od o 30 minut ve velkém množství utterances 40 hodin pro 20 000 utterances.

> [!NOTE]
> Bezplatné předplatné uživatelů můžete cvičení dvě písma hlasové najednou. Uživatelé Standard předplatného můžete současně cvičení tři hlasy. Pokud dosáhnete limitu, počkejte alespoň jeden hlas písma dokončení školení a akci opakujte.

## <a name="test-your-voice-font"></a>Testování vaší hlasové písma

Po vaší hlasové písma úspěšně, můžete otestovat ji ještě před nasazením pro použití. Klikněte na tlačítko **Test** ve sloupci operace. Testovací stránka se zobrazí pro vybrané hlasové písmo. Tabulka je prázdný, pokud jste dosud odeslány veškeré testovací žádosti pro hlasu.

![Vlastní hlasové písma, část 2](media/custom-voice/my-voice-fonts2.png)

Klikněte na tlačítko **Test s textem** tlačítko pod názvem tabulky k zobrazení místní nabídky pro odesílání žádostí text. Můžete odeslat testovací žádost jako prostý text nebo SSML. Maximální velikost vstupní je 1 024 znaků, včetně všech značek pro SSML požadavek. Jazyk textu musí být stejná jako jazyk hlasového písma.

![Hlas na testování písma](media/custom-voice/voice-font-testing.png)

Po vyplnění do textového pole a potvrzení vstupní režimu, klikněte na tlačítko **Ano** odeslat žádost o test a vrátíte se na stránku testu. Tabulka nyní obsahuje položky, která odpovídá novou žádost a ve sloupci Stav teď známé. Ho může trvat několik minut syntetizovat řeči. Když ve sloupci Stav čte bylo úspěšné, si můžete stáhnout zadávání textu ( `.txt` souboru) a výstupní zvuková ( `.wav` souboru) a audition pozdější pro kvality.

![Hlas na testování písma, část 2](media/custom-voice/voice-font-testing2.png)

## <a name="create-and-use-a-custom-endpoint"></a>Vytvořit a použít vlastní koncový bod

Po úspěšně jste vytvořili a otestovat hlasové modelu, můžete nasadit v koncový bod vlastní převod textu na řeč. Při vytváření žádosti o převod textu na řeč přes rozhraní REST API pak použijete tento koncový bod místo obvyklé koncový bod. Svůj vlastní koncový bod může být volána pouze předplatné, které jste použili k nasazení písmo.

Chcete-li vytvořit nové vlastní koncový bod, zvolte **koncové body** vlastní hlasové nabídce v horní části stránky. Stránka nasazení se zobrazí s její tabulkou aktuální vlastní hlasové koncových bodů, pokud existuje.

Klikněte **nasazení hlasy** tlačítko vytvořte nový koncový bod. V koncovém bodě vytvořit"stránka aktuální národní prostředí se odrazí v první řádek tabulky. Vytvoření nasazení pro jiný jazyk, změňte zobrazených národní prostředí. (Musí se shodovat hlasové, které nasazujete.) Zadejte název a popis svůj vlastní koncový bod.

Z nabídky předplatného zvolte předplatné, které chcete použít. Bezplatné předplatné uživatelů může mít jenom jeden model v čase nasazení. Uživatelé Standard předplatného můžete vytvořit až 20 koncových bodů, každou s vlastním vlastní hlasové.

![Vytvoření koncového bodu](media/custom-voice/create-endpoint.png)

Po výběru modelu, který má být nasazen, klikněte na **vytvořit**. Stránka nasazení se znovu zobrazí, nyní se záznam pro váš nový koncový bod. To může trvat několik minut pro vytvoření instance nového koncového bodu. Pokud je stav nasazení úspěšné, koncový bod je připraven k použití.

![Moje nasazené hlasy](media/custom-voice/my-deployed-voices.png)

Stav nasazení je úspěšné, vaše nasazené hlasové písma zobrazí se koncové body v u tabulky nasazené hlasy. Tento identifikátor URI můžete použít přímo v požadavku HTTP.

Online testování koncového bodu je také k dispozici prostřednictvím portálu vlastní hlasové. Chcete-li otestovat váš koncový bod, zvolte **testování koncových bodů** z rozevírací nabídky vlastní hlasu. Koncový bod testování stránky se zobrazí. Zvolte hlasové, které jste nasadili a do textového pole zadejte text, který má být oznamována (v prostém textu nebo formátu SSML).

> [!NOTE] 
> Při použití SSML, `<voice>` značky musíte zadat název dáte vlastní hlasové, když jste ji vytvořili.

Klikněte na tlačítko **přehrání** a poslechnout si text používaný v vaše vlastní hlasové písma.

![Koncový bod testování](media/custom-voice/endpoint-testing.png)

Vlastní koncový bod je stejně jako standardní koncový bod pro převod textu na řeč požadavky. V tématu [REST API](rest-apis.md) Další informace.

## <a name="next-steps"></a>Další postup

- [Získat zkušební verze předplatného řeči](https://azure.microsoft.com/try/cognitive-services/)
- [Rozpoznávat řeč v jazyce C#](quickstart-csharp-windows.md)
