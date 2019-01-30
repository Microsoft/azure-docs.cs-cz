---
title: Jak vytvořit vlastní hlasové písmo
titlesuffix: Azure Cognitive Services
description: Tento článek je základní informace o přizpůsobení hlasové převod textu na řeč, které vám umožní vytvořit hlas rozpoznatelných, jeden z druhu značky.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: panosper
ms.openlocfilehash: db397c5aff598124b78ab98c861b184b01ddbb90
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55227042"
---
# <a name="creating-custom-voice-fonts"></a>Vytvoření vlastního hlasového písma

Přizpůsobení hlasové převod textu na ŘEČ umožňuje vytvářet rozpoznat, jeden z druhu hlas pro hodnotu značky: *hlasového písma.*

K vytvoření hlasového písma, provedete studio nahrávání a nahrát přidružené skripty jako trénovací data. Služba potom vytvoří jedinečný hlas modelu, která je vyladěná pro nahrávání. Můžete použít tento hlasového písma tak, aby odpovídaly řeči.

Můžete začít s menším objemem dat pro testování konceptu. Ale čím více dat zadáte, čím více přirozený a professional zvuky svůj hlas.

## <a name="prerequisites"></a>Požadavky

Potřebujete účet Azure a předplatné služby řeči. [Vytvořte si ho](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started) Pokud jste tak již neučinili. Připojení k portálu vlastní hlasové vašeho předplatného, jak je znázorněno zde.

1. Přihlaste se k [vlastní hlasové portál](https://customvoice.ai) pomocí stejného účtu Microsoft, který jste použili k použití pro přístup.

2. Pod názvem vašeho účtu v pravém horním rohu, přejděte na **předplatná**.

    ![Předplatná](media/custom-voice/subscriptions.png)

3. Na stránce předplatných vyberte **připojit stávající předplatné**. Všimněte si, že hlasové služby podporuje různých oblastech. Zkontrolujte oblast, kde klíč předplatného. byla vytvořena a ujistěte se, že připojení k portálu správné dílčí klíč.  

4. Vložte váš klíč předplatného do tabulky, jak je znázorněno v následujícím příkladu. Každé předplatné má dva klíče, a můžete použít kteroukoli z nich.

     ![Přidat předplatné](media/custom-voice/add-subscription.png)

Jste připraveni začít!

## <a name="prepare-recordings-and-transcripts"></a>Příprava záznamy a přepisy

Hlasové trénovací datové sady se skládá ze sady zvukové soubory, spolu s textový soubor, který obsahuje záznamy o studiu zvukových souborů.

Tyto soubory dvě možnosti, jak můžete připravit. Buď napsat skript a jeho číst talentu hlasu nebo použít veřejně dostupné zvuk a přepisy na text. Pokud tak učiníte ten, upravte disfluencies z zvukové soubory, jako je například "um" a dalších přednastavené zvuky, zadrhává, mumbled slova nebo mispronunciations.

K vytvoření dobré hlasového písma, ujistěte se, záznamy v tichém místnosti s vysoce kvalitní mikrofon. Konzistentní svazku mluvený míra mluvený rozteč a expresivní zkoušky prezentace řeči jsou nezbytné pro vytváření skvělých hlasových digitální.

Pokud chcete vytvořit hlasový vstup pro použití v produkčním prostředí, doporučujeme že použít profesionální záznam studio a způsobu vyjadřování talentu. Další informace najdete v tématu [jak záznam hlasu ukázky pro vlastní hlasové](record-custom-voice-samples.md).

### <a name="audio-files"></a>Zvukové soubory

Každý zvukový soubor by měl obsahovat jeden utterance (například jedné věty nebo jeden zapnout systému dialogového okna). Všechny soubory musí být ve stejném jazyce. (Hlasy vícejazyčné vlastní nejsou podporovány.) Zvukové soubory musí také mít jedinečný číselný název souboru s příponou názvu souboru `.wav`.

Zvukové soubory potřeba se připravit následujícím způsobem. Další formáty nejsou podporovány a budou odmítnuty.

| **Vlastnost** | **Hodnota** |
| ------------ | --------- |
| Formát souboru  | RIFF (.wav)|
| Vzorkovací frekvence| aspoň 16 000 Hz |
| Vzor formátu| PCM, 16-bit |
| Název souboru    | Číselné hodnoty a s `.wav` rozšíření |
| Formát archivu| .zip      |
| Maximální velikost|200 MB|

> [!NOTE]
> soubory WAV pomocí vzorkovací frekvenci nižší než 16 000 Hz budou odmítnuty. Pokud soubor ZIP obsahuje ve vlnách. s jinou vzorkovací frekvence, budou importovány pouze ty rovná nebo je vyšší než 16 000 Hz.
> Portálu aktuálně importy ZIP archivuje až 200 MB. Však můžete nahrát několik archivů. Maximální počet povolený datové sady je 10 zip soubory bezplatné předplatné uživatelů a 50 pro standardní předplatné uživatele.

### <a name="transcripts"></a>Záznamy o studiu

Přepis soubor je soubor s prostým textem (ANSI, UTF-8, UTF-8-BOM, UTF-16-LE nebo být UTF-16). Každý řádek v souboru určené k transkripci musí mít název zvukový soubor, za nímž následuje znak tabulátoru (bod kódu 9) a nakonec jeho přepisu. Jsou povolené žádné prázdné řádky.

Příklad:

```
0000000001  This is the waistline, and it's falling.
0000000002  We have trouble scoring.
0000000003  It was Janet Maslin.
```

Systém vlastní hlasové normalizuje záznamy o studiu převedením text na malá písmena a odebrat nadbytečné interpunkce. Je důležité, že záznamy o studiu jsou 100 % přesných přepisů odpovídající zvukových nahrávek.

> [!TIP]
> Při vytváření produkčního převod textu na řeč hlasů, vyberte projevy (nebo skripty), zkuste do účtu zapsané ve fonetické pokrytí a efektivitu. Máte potíže při načítání výsledků požadujete? [Obraťte se na tým vlastní hlasové](mailto:speechsupport@microsoft.com) najít najdete další informace o s námi na více instancí.

## <a name="upload-your-datasets"></a>Nahrání datových sad

Po přípravě archivu zvukový soubor a záznamy o studiu, odešle je prostřednictvím [vlastní hlasové služby portálu](https://customvoice.ai).

> [!NOTE]
> Datové sady nelze upravovat, jakmile se nahrály. Pokud zapomenete patří záznamy o studiu některých zvukové soubory, například nebo náhodně zvolit nesprávné pohlaví, musíte nahrát celou datovou sadu znovu. Zkontrolujte nastavení datové sady a důkladně před zahájením nahrávání.

1. Přihlaste se k portálu.

2. Na hlavní stránce v části **vlastní hlasové**vyberte **Data**.   

    **Moje hlasové** tabulky se zobrazí. To je prázdný, pokud jste neodeslali žádné datové sady hlasové ještě.

3. Otevřete stránku pro nahrávání novou datovou sadu, vyberte **importovat data**.

    ![Import hlasová Data](media/custom-voice/import-voice-data.png)

4. Zadejte název a popis do pole, která jsou k dispozici.

5. Vyberte národní prostředí pro hlasová písma. Ujistěte se, že informace o národním prostředí odpovídá jazyku záznam dat a skriptů.

6. Vyberte pohlaví mluvčího jehož hlasové, které používáte.

7. Vyberte skript a zvukové soubory k nahrání.

8. Vyberte **Import** nahrát data. U větších datových sad může import trvat několik minut.

> [!NOTE]
> Uživatelé bezplatné předplatné můžete nahrát dvě datové sady v čase. Předplatné standard sady mohou uživatelé odeslat pět datových sad současně. Pokud limit překročíte, počkejte aspoň jeden z datových sad dokončení importu. Pak to zkuste znovu.

Po dokončení nahrávání **Moje hlasová Data** tabulky se zobrazí znovu. Měli byste vidět položku, která odpovídá datovou sadu, kterou jste právě nahráli.

Datové sady se automaticky ověří po odeslání. Ověření dat obsahuje řadu kontrol zvukové soubory, chcete-li ověřit jejich formát souboru, velikost a vzorkovací frekvenci. Kontroly soubory určené k transkripci ověřte formát souboru a provést některé normalizace text. Projevy jsou přepsány, rozpoznávání řeči. Výsledný text je pak porovnána s přepisu, které jste zadali.

![Moje hlasová Data](media/custom-voice/my-voice-data.png)

V následující tabulce jsou uvedeny stavy zpracování pro importované datové sady:

| Stav | Význam
| ----- | -------
| `NotStarted` | Vaše datová sada byla přijata a zařadily do fronty ke zpracování.
| `Running` | Vaše datová sada se ověřuje.
| `Succeeded` | Vaše datová sada je potvrzená a může se teď dá sestavit hlasového písma.

Po dokončení ověření můžete zobrazit celkový počet odpovídajících projevy pro každý z datových sad v **Utterance** sloupce.

Sestavy ke kontrole skóre výslovnost a úroveň šumu pro každý z vašich nahrávek, si můžete stáhnout. Výslovnost skóre je v rozsahu 0 až 100. Skóre pod 70 obvykle znamená neshodou řeči chyba nebo skriptu. Silná zvýraznění může snížit vaše skóre výslovnost a ovlivnit generované digitální hlasu.

Vyšší poměr signálu šumu (SNR) znamená nižší šumu v zvuku. Obvykle můžete oslovit SNR víc než 50 zaznamenáváním na profesionální aplikace. Zvuk se SNR pod 20 může vést k zřejmé šumu v generované hlasu.

Vezměte v úvahu znovu záznam jakékoli projevy s nízkou výslovnost skóre nebo nízký poměr signálu šumu. Pokud nejde znovu záznam, může být těchto projevů vyloučit z datové sady.

## <a name="build-your-voice-font"></a>Sestavení hlasového písma

Po ověření datové sady můžete vytvářet vlastní hlasové písmo.

1.  V **vlastní hlasové** rozevírací nabídky vyberte **modely**.

    **Moje hlasová písma** tabulky se zobrazí, výpis všechny vlastní hlasové písmo jste už vytvořili.

1. V části název tabulky, vyberte **vytvořit hlasy**.

    Zobrazí se stránka pro vytvoření hlasového písma. Aktuální národní prostředí se zobrazí v prvním řádku tabulky. Změňte národní prostředí pro vytváření hlasový vstup v jiném jazyce. Národní prostředí musí být stejné jako u datových sad, které jsou použity k vytvoření hlasu.

1. Stejně jako při odesílání vaší datové sadě, zadejte název a popis, který vám pomůže identifikovat tento model.

    Zvolte název pečlivě. Název, které zadáte tady bude název, který se používá k určení hlasu ve vaší žádosti pro syntézu řeči, jako součást SSML vstup. Pouze písmena, číslice a pár znaků interpunkce, jako `-`, `_`, a `(', ')` jsou povoleny.

    Běžně **popis** pole je záznam názvech datových sad, které byly použity k vytvoření modelu.

1. Zvolte pohlaví hlasového písma. Musí se shodovat pohlaví datové sady.

1. Vyberte datových sad, které chcete použít pro trénování hlasového písma. Všech datových sad, které používáte, musí být ze stejné mluvčího.

1. Klikněte na tlačítko **vytvořit** a začněte vytvářet hlasového písma.

    ![Vytvoření modelu](media/custom-voice/create-model.png)

Se zobrazí v novém modelu **Moje hlasová písma** tabulky.

![Moje hlasová písma](media/custom-voice/my-voice-fonts.png)

Stav, který se zobrazí odráží proces převodu vaše datová sada hlasového písma, jak je znázorněno zde.

| Stav | Význam
| ----- | -------
| `NotStarted` | Vaše žádost o vytvoření hlasového písma se zařadí do fronty ke zpracování.
| `Running` | Vytváří se hlasového písma.
| `Succeeded` | Hlasového písma se vytvořil a mohou být nasazeny.

Školení čas se liší v závislosti na objemu zpracovaných dat zvuku. Typické doby rozsahu o 30 minut, než stovky projevy až 40 hodin pro 20 000 projevy.

> [!NOTE]
> Uživatelé bezplatné předplatné trénovat jeden hlasového písma v čase. Předplatné standard sady uživatelů současně trénovat tři hlasy. Pokud limit překročíte, počkejte aspoň jeden hlasová písma dokončení školení a pak to zkuste znovu.

## <a name="test-your-voice-font"></a>Testování hlasového písma

Po úspěšném sestavení hlasového písma, takže ji můžete otestovat před nasazením pro použití. V **operace** sloupci vyberte **Test**. Testovací stránka se zobrazí pro vybrané hlasového písma. Je tabulka prázdná, pokud ještě neodeslali jste žádné požadavky testu pro hlasu.

Chcete-li zobrazit místní nabídku pro odesílání žádostí textu, vyberte **Test s textem** tlačítko pod názvem tabulky. Můžete odeslat požadavek testu jako prostý text nebo SSML. Maximální velikost vstupu je 1 024 znaků, včetně všech značek pro daný požadavek SSML. Jazyk textu musí být stejný jako jazyk hlasového písma.

Po vyplnění do textového pole a potvrzení vstupní režim, vyberte **Ano** odeslat žádost o test a vrátíte se k testovací stránce. Tabulka teď obsahuje položku, která odpovídá vaší novou žádost a ve sloupci stav. Může trvat několik minut tak, aby odpovídaly řeči. Když uvádí, že sloupec Stav na **proběhlo úspěšně**, si můžete stáhnout textové zadání ( `.txt` soubor) a výstupní zvuková ( `.wav` souboru) a audition ten kvality.

## <a name="create-and-use-a-custom-endpoint"></a>Vytvoření a použití vlastního koncového bodu

Po jste úspěšně vytvořili a otestování modelu hlasové, nasaďte do vlastního koncového bodu převodu textu na řeč. Pak použijete tento koncový bod místo obvyklých koncového bodu při vytváření žádosti o převod textu na řeč přes rozhraní REST API. Vlastní koncový bod lze volat pouze podle předplatného, který jste použili k nasazení písma.

Chcete-li vytvořit nové vlastní koncový bod, zvolte **koncové body** z **vlastní hlasové** nabídce v horní části stránky. **Mé nasazené hlasy** se zobrazí stránka s jeho tabulkou aktuální koncové body vlastní hlasové, pokud existuje. Aktuální národní prostředí se projeví v první řádek tabulky. Vytvoření nasazení pro jiný jazyk, změňte zobrazený národní prostředí. (Musí se shodovat hlasové, které nasazení provádíte.)

Chcete-li vytvořit nový koncový bod, vyberte **nasazení hlasy** tlačítko. Zadejte název a popis vlastní koncový bod.

Z **předplatné** nabídek, zvolte předplatné, které chcete použít. Uživatelé bezplatné předplatné může mít pouze jeden model nasazení po jednom. Standardní předplatné uživatele můžete vytvořit až 20 koncové body, každý s vlastním hlasem vlastní.

![Vytvoření koncového bodu](media/custom-voice/create-endpoint.png)

Po výběru modelu, který má být nasazen, vyberte **vytvořit**. **Mé nasazené hlasy** stránku znovu objeví, nyní s položkou pro nový koncový bod. Může trvat několik minut pro vytvoření instance nového koncového bodu. Pokud je stav nasazení **Succeeded**, koncový bod je připraven k použití.

![Mé nasazené hlasů](media/custom-voice/my-deployed-voices.png)

Pokud je stav nasazení **Succeeded**, koncovým bodem nasazeného hlasového písma se zobrazí v **mé nasazené hlasy** tabulky. Tento identifikátor URI můžete použít přímo v jednom požadavku HTTP.

Online testování koncového bodu je také k dispozici prostřednictvím portálu pro vlastní hlasové. Chcete-li otestovat váš koncový bod, zvolte **testování koncových bodů** z **vlastní hlasové** rozevírací nabídky. Koncový bod testování stránky se zobrazí. Zvolte nasazené vlastní hlasové a zadejte text, který má být španělským (prostý text nebo formátu SSML) do textového pole.

> [!NOTE]
> Při použití SSML, `<voice>` značky musí být zadán název, který jste zadali vlastní hlasové při jeho vytváření. Pokud publikujete prostý text, je vždy použito vlastní hlasové.

Pokud chcete slyšet text, kterým se mluví ve vaší vlastní hlasové písmo, vyberte **Přehrát**.

![Testování koncového bodu](media/custom-voice/endpoint-testing.png)

Vlastní koncový bod je funkčně stejný jako standardní koncový bod, který se používá pro převod textu na řeč požadavky. Zobrazit [rozhraní REST API](rest-apis.md) Další informace.

## <a name="language-support"></a>Podpora jazyků

Přizpůsobení hlasu je k dispozici v těchto jazycích:

| Jazyk | Národní prostředí |
|----------|--------|
| Čínština (Španělsko) | zh-CN |
| English (US) | en-US |
| Francouzština | fr-FR |
| Němčina | de-DE |
| italština | IT-IT |

> [!NOTE]
> Francouzština, němčina a italština školení způsobu vyjadřování začíná u datové sady projevy 2 000 +. Čínština – angličtina dvojjazyčné modely jsou také podporovány s datovou sadou projevy 2 000 +.

## <a name="next-steps"></a>Další postup

- [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Záznam vašich ukázek hlasu](record-custom-voice-samples.md)
