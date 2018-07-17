---
title: Co je vlastní hlasové? – Azure Cognitive Services | Dokumentace Microsoftu
description: Tento článek přehledy Microsoft textu na řeč hlasové přizpůsobení, které vám umožní vytvořit hlas rozpoznatelných, jeden z druhu značky.
services: cognitive-services
author: noellelacharite
manager: nolach
ms.service: cognitive-services
ms.topic: article
ms.date: 05/07/2018
ms.author: nolach
ms.openlocfilehash: 011358e223db419f31e0181b05ce6f89479dba81
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39070939"
---
# <a name="creating-custom-voice-fonts"></a>Vytvoření vlastního hlasového písma

Přizpůsobení hlasové Microsoft textu na řeč umožňuje vytvářet rozpoznat, jeden z druhu hlas pro hodnotu značky: *hlasového písma.* 

K vytvoření hlasového písma, provedete studio nahrávání a nahrát přidružené skripty jako trénovací data. Služba potom vytvoří jedinečný hlas modelu, která je vyladěná pro nahrávání. Potom můžete tento hlasového písma tak, aby odpovídaly řeči. 

Můžete začít s menším objemem dat pro testování konceptu. Ale čím více dat zadáte, čím více přirozený a professional zvuky svůj hlas.

Přizpůsobení hlasu je k dispozici pro Americkou angličtinu (en US) a Španělsko čínština (zh-CN).

## <a name="prerequisites"></a>Požadavky

Převod textu na řeč hlasová přizpůsobení funkce je aktuálně ve verzi private preview. [Vyplňte formulář přihlášky](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0N8Vcdi8MZBllkZb70o6KdURjRaUzhBVkhUNklCUEMxU0tQMEFPMjVHVi4u) považovat pro přístup.

Potřebujete účet Azure a předplatné služby řeči. [Vytvořit] (https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/get-started) Pokud jste tak již neučinili. Vaše předplatné připojení k portálu vlastní hlasové následujícím způsobem.

1. Přihlaste se k [vlastní hlasové portál](https://customvoice.ai) pomocí stejného účtu Microsoft, používá k aplikování pro přístup.

2. Přejdete na "Předplatné" pod názvem vašeho účtu v pravém horním rohu.

    ![Předplatná](media/custom-voice/subscriptions.png)

3. Na stránce "Předplatné" zvolte připojit existující předplatné.

     ![Připojit stávající předplatné](media/custom-voice/connect-existing-sub.png)

4. Vložte váš klíč předplatného do tabulky, jak je znázorněno níže. Každé předplatné má dva klíče a můžete použít některou z nich.

     ![Přidat předplatné](media/custom-voice/add-subscription.png)

Jste připraveni začít!

## <a name="prepare-recordings-and-transcripts"></a>Příprava záznamy a přepisy

Hlasové trénovací datové sady se skládá ze sady zvukové soubory, spolu s textový soubor obsahující záznamy o studiu všech těchto zvukové soubory.

Můžete připravit těchto souborů v obou směrech: buď napsat skript a jeho číst talentu hlas, nebo použít veřejně dostupné zvuk a přepisy na text. V takovém případě upravte disfluencies z zvukové soubory, jako je například "um" a dalších přednastavené zvuky, zadrhává, mumbled slova nebo mispronunciations.

K vytvoření dobré hlasové písmo, je důležité, že záznamy se provádějí v tichém místnosti s vysoce kvalitní mikrofon. Konzistentní svazku mluvený míra mluvený rozteč a expresivní zkoušky prezentace řeči jsou nezbytné pro vytváření skvělých hlasových digitální. Pokud chcete vytvořit hlasový vstup pro použití v produkčním prostředí, doporučujeme že použít profesionální záznam studio a způsobu vyjadřování talentu. Další podrobnosti najdete v tématu [jak záznam hlasu ukázky pro vlastní hlasové](record-custom-voice-samples.md).

### <a name="audio-files"></a>Zvukové soubory

Každý zvukový soubor by měl obsahovat jeden utterance (například jedné věty nebo jeden zapnout systému dialogového okna). Všechny soubory musí být ve stejném jazyce (vícejazyčné vlastní hlasy nejsou podporovány). Zvukové soubory musí také mít jedinečný číselný název souboru s názvem souboru s příponou `.wav`.

Zvukové soubory potřeba se připravit následujícím způsobem. Další formáty nejsou podporovány a budou odmítnuty.

| **Vlastnost** | **Hodnota** |
| ------------ | --------- |
| Formát souboru  | RIFF (WAV)|
| Vzorkovací frekvence| aspoň 16 000 Hz |
| Vzorkovací formát| PCM, 16 bitů |
| Název souboru    | Číselné hodnoty a s `.wav` rozšíření |
| Formát archivu| ZIP      |
| Maximální velikost archivu|200 MB|

Umístí do jedné složce bez jeho podadresářích sadu zvukové soubory a nastavit jako jeden soubor archivu ZIP celý balíček.

> [!NOTE]
> Soubory wave s vzorkovací frekvenci nižší než 16 000 Hz budou odmítnuty. V případech, kde soubor zip obsahuje ve vlnách. s jinou vzorkovací frekvence budou importovány pouze ty rovná nebo je vyšší než 16 000 Hz.
> Na portálu aktuálně importuje ZIP archivuje až 200 MB. Však může být nahrát několik archivů. Maximální počet povolený datové sady je že 10 ZIP soubory bezplatné předplatné uživatelů a 50 pro standardní předplatné uživatele.

### <a name="transcripts"></a>Záznamy o studiu

Přepis soubor je soubor s prostým textem (ANSI/UTF-8/UTF-8-BOM/UTF-16-LE/UTF-16-BE). Každý řádek v souboru určené k transkripci musí mít název zvukový soubor, za nímž následuje znak tabulátoru (bod kódu 9) a nakonec jeho přepisu. Jsou povolené žádné prázdné řádky.

Příklad:

```
0000000001  This is the waistline, and it's falling.
0000000002  We have trouble scoring.
0000000003  It was Janet Maslin.
```

Systém vlastní hlasové normalizuje záznamy o studiu převod textu na malé a odebráním nadbytečné interpunkce. Je důležité, že záznamy o studiu jsou 100 % přesností na odpovídající zvukové záznamy.

> [!TIP]
> Při vývoji produkčních převod textu na řeč hlasů, vyberte projevy (nebo skripty) vzhledem k tomu zapsané ve fonetické pokrytí a efektivitu.

## <a name="upload-your-datasets"></a>Nahrání datových sad

Po přípravě archivu zvukový soubor a záznamy o studiu, odešle je prostřednictvím [vlastní hlasové služby portálu](https://customvoice.ai).

> [!NOTE]
> Datové sady nelze upravovat, jakmile se nahrály. Pokud jste zapomněli obsahovat záznamy o studiu některých zvukové soubory, například nebo náhodně zvolit nesprávné pohlaví, musíte nahrát celou datovou sadu znovu. Zkontrolujte nastavení datové sady a důkladně před zahájením nahrávání.

1. Přihlaste se k portálu.

2. Zvolte **Data** v části vlastní hlasové na hlavní stránce. 

    ![Moje projekty](media/custom-voice/my-projects.png)

    Zobrazí se tabulka Moje hlasová Data. Není prázdná, pokud jste ještě nepřidali žádné datové sady hlasu.

3. Klikněte na tlačítko **importovat data** otevřete stránku pro nahrávání nová datová sada.

    ![Import hlasová Data](media/custom-voice/import-voice-data.png)

4. Zadejte název a popis do polí Item. 

5. Vyberte národní prostředí pro hlasová písma. Ujistěte se, že informace o národním prostředí odpovídá jazyku záznam dat a skriptů. 

6. Vyberte pohlaví mluvčího jehož hlasové, který používáte.

7. Zvolte skript a zvukové soubory k nahrání. 

8. Klikněte na tlačítko **Import** nahrát data. U větších datových sad může import trvat několik minut.

> [!NOTE]
> Uživatelé bezplatné předplatné můžete nahrát dvě datové sady v čase. Předplatné standard sady mohou uživatelé odeslat pět datových sad současně. Pokud limit překročíte, počkejte aspoň jeden z datových sad dokončení importování, a zkuste to znovu.

Po dokončení nahrávání se zobrazí znovu tabulce Moje hlasová Data. Měli byste vidět položku, která odpovídá k vaší datové sadě právě nahráli. 

Datové sady se automaticky ověří po odeslání. Ověření dat obsahuje řadu kontrol zvukové soubory, chcete-li ověřit jejich formát souboru, velikost a vzorkovací frekvenci. Kontroly soubory určené k transkripci ověřte formát souboru a provést některé normalizace text. Projevy se převede pomocí rozpoznávání řeči a výsledný text je ve srovnání s přepisu, které jste zadali.

![Moje hlasová Data](media/custom-voice/my-voice-data.png)

V následující tabulce jsou uvedeny stavy zpracování pro importované datové sady. 

| Stav | Význam
| ----- | -------
| `NotStarted` | Byla přijata vaše datová sada a do fronty pro zpracování
| `Running` | Vaše datová sada je ověřován
| `Succeeded` | Vaše datová sada je potvrzená a může se teď dá sestavit hlasového písma

Po ověření se dokončí, zobrazí se celkový počet odpovídajících projevy pro každý z datových sad ve sloupci Utterance.

Sestavy ke kontrole skóre výslovnost a úroveň šumu pro každý z vašich nahrávek, si můžete stáhnout. Výslovnost skóre je v rozsahu 0 až 100; skóre pod 70 obvykle znamená neshodou řeči chyba nebo skriptu. Silná zvýraznění může snížit vaše skóre výslovnost a ovlivnit generované digitální hlasu.

Vyšší poměr signálu šumu (SNR) znamená nižší šumu v zvuku. Obvykle můžete oslovit SNR víc než 50 zaznamenáváním prostřednictvím profesionální aplikace. Zvuk se SNR pod 20 může vést k zřejmé šumu v generované hlasu.

Vezměte v úvahu znovu záznam jakékoli projevy s nízkou výslovnost skóre nebo nízký poměr signálu šumu. Pokud znovu záznam není možné, může tyto projevy vyloučit z datové sady.

## <a name="build-your-voice-font"></a>Sestavení hlasového písma

Jakmile se ověřil vaši datovou sadu, můžete vytvářet vlastní hlasové písmo. 

1. Zvolte **modely** v rozevírací nabídce "Vlastní hlasové". 
 
    Zobrazí se tabulky Moje hlasová písma se seznamem všechny vlastní hlasová písma, kterou jste již vytvořili.

1. Klikněte na tlačítko **vytvořit hlasy** pod názvem tabulky. 

    Zobrazí se stránka pro vytvoření hlasového písma. Aktuální národní prostředí se zobrazí v prvním řádku tabulky. Změňte národní prostředí pro vytváření hlasový vstup v jiném jazyce. Národní prostředí musí být stejný jako datové sady používá k vytvoření hlasu.

1. Stejně jako při odesílání vaší datové sadě, zadejte název a popis, který vám pomůže identifikovat tento model. 

    Název, který zde zadáte, bude název, který používáte k určení hlasu ve vaší žádosti pro syntézu řeči jako součást vstup SSML, proto zvolte pečlivě. Pouze písmena, číslice a několik interpunkční znaky, jako je "-", "_" '(',')' jsou povoleny.

    Běžně se používají pole popisu je poznamenejte si názvy datových sad, které byly použity k vytvoření modelu.

1. Zvolte pohlaví hlasového písma. Musí se shodovat pohlaví datové sady.

1. Vyberte datových sad, které chcete použít pro trénování hlasového písma. Všechny datové sady použité musí být ze stejné mluvčího.

1. Klikněte na tlačítko **vytvořit** a začněte vytvářet hlasového písma.

    ![Vytvoření modelu](media/custom-voice/create-model.png)

Nový model se zobrazí v tabulce Moje hlasová písma. 

![Moje hlasová písma](media/custom-voice/my-voice-fonts.png)

Včetně zobrazeného stavu odráží proces převodu vaše datová sada hlasového písma, jak je znázorněno zde.

| Stav | Význam
| ----- | -------
| `NotStarted` | Vaše žádost o vytvoření hlasového písma zařadily do fronty ke zpracování
| `Running` | Probíhá vytváření hlasového písma
| `Succeeded` | Hlasového písma se vytvořil a mohou být nasazeny

Školení čas se liší v závislosti na objemu zpracovaných dat zvuku. Typické doby rozsahu o 30 minut, než stovky projevy až 40 hodin pro 20 000 projevy.

> [!NOTE]
> Uživatelé bezplatné předplatné trénovat jeden hlasového písma v čase. Předplatné standard sady uživatelů současně trénovat tři hlasy. Pokud limit překročíte, počkejte aspoň jeden hlasová písma dokončení školení a akci opakujte.

## <a name="test-your-voice-font"></a>Testování hlasového písma

Jakmile hlasového písma je úspěšně sestavená, takže ji můžete otestovat před nasazením pro použití. Klikněte na tlačítko **Test** ve sloupci operace. Testovací stránka se zobrazí pro vybrané hlasového písma. Je tabulka prázdná, pokud ještě neodeslali jste žádné požadavky testu pro hlasu.

![Moje hlasová písma, část 2](media/custom-voice/my-voice-fonts2.png)

Klikněte na tlačítko **Test s textem** tlačítko pod názvem tabulky k zobrazení místní nabídky pro odesílání žádostí text. Můžete odeslat požadavek testu jako prostý text nebo SSML. Maximální velikost vstupu je 1 024 znaků, včetně všech značek pro požadavek SSML. Jazyk textu musí být stejný jako jazyk hlasového písma.

![Hlasové písmo testování](media/custom-voice/voice-font-testing.png)

Jakmile vyplníte textové pole a potvrzení vstupní režim, kliknutím na tlačítko **Ano** odeslat žádost o test a vrátíte se k testovací stránce. Tabulka teď obsahuje položku, která odpovídá novou žádost a ve sloupci nyní známý stav. Může trvat několik minut tak, aby odpovídaly řeči. Sloupec Stav na čtení byl úspěšný, si můžete stáhnout textové zadání ( `.txt` soubor) a výstupní zvuková ( `.wav` soubor) a audition ten kvality.

![Hlasové písmo testování, část 2](media/custom-voice/voice-font-testing2.png)

## <a name="create-and-use-a-custom-endpoint"></a>Vytvoření a použití vlastního koncového bodu

Po úspěšně jste vytvořili a testování modelu hlasové, nasaďte vlastní převod textu na řeč koncový bod. Pak použijete tento koncový bod místo obvyklých koncového bodu při vytváření žádosti o převod textu na řeč přes rozhraní REST API. Vlastní koncový bod lze volat pouze podle předplatného, který jste použili k nasazení písma.

Chcete-li vytvořit nové vlastní koncový bod, zvolte **koncové body** vlastní hlasové nabídce v horní části stránky. Nasazení se zobrazí, s jeho tabulkou aktuální koncové body vlastní hlasové, pokud existuje.

Klikněte na tlačítko **nasazení hlasy** pro vytvoření nového koncového bodu. Stránka vytvořit koncový bod"aktuální národní prostředí se projeví v první řádek tabulky. Vytvoření nasazení pro jiný jazyk, změňte zobrazený národní prostředí. (Musí se shodovat hlasové, které nasazujete.) Zadejte název a popis vlastní koncový bod.

V nabídce předplatné vyberte předplatné, pro kterou chcete použít. Uživatelé bezplatné předplatné může mít pouze jeden model nasazení po jednom. Standardní předplatné uživatele můžete vytvořit až 20 koncové body, každý s vlastním hlasem vlastní.

![Vytvoření koncového bodu](media/custom-voice/create-endpoint.png)

Po výběru modelu, který má být nasazen, klikněte na **vytvořit**. Stránka nasazení znovu objeví, nyní s položkou pro nový koncový bod. Může trvat několik minut pro vytvoření instance nového koncového bodu. Když je úspěšně nahlášen stav nasazení, koncový bod je připraven k použití.

![Mé nasazené hlasů](media/custom-voice/my-deployed-voices.png)

Když je úspěšně nahlášen stav nasazení, koncový bod nasazené hlasového písma se zobrazí v tabulka nasazené hlasy. Tento identifikátor URI můžete použít přímo v jednom požadavku HTTP.

Online testování koncového bodu je také k dispozici prostřednictvím portálu pro vlastní hlasové. Chcete-li otestovat váš koncový bod, zvolte **testování koncových bodů** z rozevírací nabídky vlastní hlasové. Koncový bod testování stránky se zobrazí. Zvolte hlasový vstup, který jste nasadili a do textového pole zadejte text, který má být používán (ve formátu prostého textu nebo SSML formátu).

> [!NOTE] 
> Při použití SSML, `<voice>` značky musí být zadán název jste zadali vlastní hlasové při jeho vytváření.

Klikněte na tlačítko **Přehrát** potěšit text, kterým se mluví ve vaší vlastní hlasové písmo.

![Testování koncového bodu](media/custom-voice/endpoint-testing.png)

Vlastní koncový bod je funkčně stejný jako standardní koncový bod pro převod textu na řeč požadavky. Zobrazit [rozhraní REST API](rest-apis.md) Další informace.

## <a name="next-steps"></a>Další postup

- [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Rozpoznávání řeči v jazyce C#](quickstart-csharp-dotnet-windows.md)
