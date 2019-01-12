---
title: V reálném čase analýza sentimentu na Twitteru s využitím Azure Stream Analytics
description: Tento článek popisuje, jak pomocí Stream Analytics pro analýza sentimentu na Twitteru v reálném čase. Podrobné pokyny z generování událostí na data na živých řídicích panelů.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
manager: kfile
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/29/2017
ms.openlocfilehash: 86fa7fab6897802fd4f18936f2d7bb0700829837
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2019
ms.locfileid: "54231134"
---
# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>V reálném čase analýza sentimentu na Twitteru ve službě Azure Stream Analytics

Zjistěte, jak vytvářet řešení pro analýzu subjektivního hodnocení pro analýzy sociálních médií přenesením události Twitteru v reálném čase do služby Azure Event Hubs. Pak použijte zápis dotazu Azure Stream Analytics k analýze dat a buď uložit výsledky později nebo řídicí panel můžete a [Power BI](https://powerbi.com/) k poskytování přehledů v reálném čase.

Nástroje pro analýzu sociálních médií pomoct organizacím porozumět populárních témat. Populárních tématech jsou témata a postojů, které mají velký počet příspěvků v sociálních sítích. Analýza mínění, kterému se taky říká *stanovisko dolování*, používá nástroje analýzy sociálních médií k určení postojů směrem k produktu, nápad a podobně. 

Analýza trendů v reálném čase Twitter je skvělé příkladem analytických nástrojů, protože model předplatného hashtag umožňuje poslouchat určitá klíčová slova (hashtagy) a vývoj analýzu subjektivního hodnocení ve informačního kanálu.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Scénář: Analýza subjektivního hodnocení sociálních médií v reálném čase

Společnost, která má sdělovacích webu má zájem o získání výhodu před konkurencí podle zobrazujících obsahu webu, který je okamžitě relevantní své čtenáře. Společnost používá analýza sociálních médií na témata, která jsou relevantní pro čtenáře tímto způsobem analýzu subjektivního hodnocení v reálném čase ve dat Twitteru.

K určení aktuálních témat v reálném čase na Twitteru, společnost potřebuje k hlavním tématům analýzy v reálném čase o svazku tweetu a mínění. Jinými slovy je potřeba mínění analytický modul analýzy, která je založena na této sociálních médií informačního kanálu.

## <a name="prerequisites"></a>Požadavky
V tomto kurzu použijete klientská aplikace, která se připojí k Twitteru a vyhledá tweety, které obsahují některé hashtagy (kterou můžete nastavit). Pokud chcete spustit aplikaci a analýza tweetů pomocí Azure Stream Analytics, musíte mít následující:

* Předplatné Azure
* Účet na Twitteru 
* Aplikace Twitter a [přístupového tokenu OAuth](https://dev.twitter.com/oauth/overview/application-owner-access-tokens) pro danou aplikaci. Zajišťuje základní pokyny k vytvoření aplikace Twitter později.
* Aplikace TwitterWPFClient, která čte informační kanál sítě Twitter. Pokud chcete získat tuto aplikaci, stáhněte si [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) soubor z Githubu a pak rozbalte balíček do složky v počítači. Pokud chcete zobrazit zdrojový kód a spusťte aplikaci v ladicí program, můžete získat zdrojový kód z [Githubu](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TwitterClient). 

## <a name="create-an-event-hub-for-streaming-analytics-input"></a>Vytvoření centra událostí pro vstupní Stream Analytics

Ukázková aplikace generuje událostí a odesílá je do centra událostí Azure. Služba Azure event hubs jsou upřednostňovanou metodou příjem událostí pro Stream Analytics. Další informace najdete v tématu [dokumentace ke službě Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md).


### <a name="create-an-event-hub-namespace-and-event-hub"></a>Vytvořit obor názvů centra událostí a Centrum událostí
V tomto postupu vytvoříte obor názvů centra událostí a pak přidáte do daného oboru názvů centra událostí. Obory názvů centra událostí se používají k logickému seskupení souvisejících událostí Service bus instancí. 

1. Přihlaste se k webu Azure portal a klikněte na tlačítko **vytvořit prostředek** > **Internet of Things** > **centra událostí**. 

2. V **vytvoření oboru názvů** okně zadejte název oboru názvů, jako například `<yourname>-socialtwitter-eh-ns`. Můžete použít libovolný název pro obor názvů, ale název musí být platnou adresu URL a musí být jedinečný v Azure. 
    
3. Vyberte předplatné a vytvořte nebo zvolte skupinu prostředků a potom klikněte na tlačítko **vytvořit**. 

    ![Vytvoření oboru názvů centra událostí](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-namespace.png)
 
4. Po dokončení nasazení oboru názvů najdete obor názvů centra událostí v seznamu prostředků Azure. 

5. Klikněte na nový obor názvů a v okně oboru názvů klikněte na tlačítko  **+ &nbsp;centra událostí**. 

    ![Tlačítko Přidat Centrum událostí pro vytvoření nové Centrum událostí ](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-button.png)    
 
6. Název nové Centrum událostí `socialtwitter-eh`. Můžete použít jiný název. Pokud tak učiníte, si poznamenejte, protože je budete později potřebovat názvu. Není nutné nastavit další možnosti pro Centrum událostí.

    ![Okno pro vytvoření nové Centrum událostí](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub.png)
 
7. Klikněte na možnost **Vytvořit**.


### <a name="grant-access-to-the-event-hub"></a>Udělit přístup do centra událostí

Předtím, než se proces může odesílat data do centra událostí, musí mít Centrum událostí zásady, které povolí odpovídající přístup. Zásady přístupu vytváří připojovací řetězec, který obsahuje informace o autorizaci.

1.  V okně oboru názvů událostí klikněte na tlačítko **Event Hubs** a pak klikněte na název vaší nové Centrum událostí.

2.  V okně centra událostí, klikněte na tlačítko **zásady sdíleného přístupu** a potom klikněte na tlačítko  **+ &nbsp;přidat**.

    >[!NOTE]
    >Ujistěte se, že pracujete s centrem událostí, ne obor názvů centra událostí.

3.  Přidání zásad s názvem `socialtwitter-access` a **deklarace identity**vyberte **spravovat**.

    ![Okno pro vytvoření nových zásad přístupu centra událostí](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-shared-access-policy-manage.png)
 
4.  Klikněte na možnost **Vytvořit**.

5.  Po nasazení zásady, klikněte na něj v seznamu zásad sdíleného přístupu.

6.  Najít pole s popiskem **PŘIPOJOVACÍ řetězec – primární klíč** a klikněte na tlačítko kopírování vedle připojovacího řetězce. 
    
    ![Zkopírování klíče primární připojovací řetězec ze zásad přístupu](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-shared-access-policy-copy-connection-string.png)
 
7.  Vložte připojovací řetězec do textového editoru. Tento připojovací řetězec budete potřebovat pro další části se po provedení některé malými úpravami.

    Připojovací řetězec vypadá takto:

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh

    Všimněte si, že připojovací řetězec obsahuje více párů klíč hodnota oddělené středníky: `Endpoint`, `SharedAccessKeyName`, `SharedAccessKey`, a `EntityPath`.  

    > [!NOTE]
    > Pro zabezpečení se odebraly částí připojovacího řetězce v příkladu.

8.  V textovém editoru, odeberte `EntityPath` pár z připojovacího řetězce (Nezapomeňte odebrat středník, který mu předchází). Jakmile budete hotovi, připojovací řetězec vypadá takto:

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=


## <a name="configure-and-start-the-twitter-client-application"></a>Nakonfigurujte a spusťte klientskou aplikaci Twitter
Klientská aplikace načte přímo z Twitteru tweet události. Aby bylo možné učinit, potřebuje oprávnění k volání rozhraní Twitter API Streaming. Pokud chcete nakonfigurovat oprávnění, vytvoříte aplikaci na Twitteru, který generuje jedinečné přihlašovací údaje (například OAuth token). Potom můžete nakonfigurovat pomocí těchto přihlašovacích údajů při volání rozhraní API klientské aplikaci. 

### <a name="create-a-twitter-application"></a>Vytvoření aplikace Twitter
Pokud již nemáte aplikaci Twitter, který používáte pro účely tohoto kurzu, můžete jeden vytvořit. Musíte již mít účtu sítě Twitter.

> [!NOTE]
> Přesný postup na Twitteru pro vytváření aplikací a získání klíče, tajné kódy a tokenu může změnit. Pokud tyto pokyny neodpovídají, co se zobrazí na webu Twitter, naleznete v dokumentaci pro vývojáře Twitter.

1. Přejděte [stránku Správa aplikací Twitter](https://apps.twitter.com/). 

2. Vytvořte novou aplikaci. 

    * Pro adresu URL webu zadejte platnou adresu URL. Nemusí být živého webu. (Není možné určit pouze `localhost`.)
    * Ponechte prázdné pole zpětného volání. Zpětná volání nevyžaduje, aby klientská aplikace, které používáte pro účely tohoto kurzu.

    ![Vytvoření aplikace na Twitteru](./media/stream-analytics-twitter-sentiment-analysis-trends/create-twitter-application.png)

3. Volitelně můžete změňte oprávnění aplikace jen pro čtení.

4. Po vytvoření aplikace přejděte **klíče a přístupové tokeny** stránky.

5. Kliknutím na tlačítko Generovat token a přístup tajný klíč přístupového tokenu.

Zachovat tyto informace po ruce, protože ho budete potřebovat v dalším postupu.

>[!NOTE]
>Klíče a tajné kódy pro aplikaci Twitter poskytují přístup ke svému účtu na Twitteru. S takovými informacemi nakládat jako s citlivými informacemi, stejně jako heslo Twitter. Například nelze vložit tyto informace v aplikaci, která poskytnout ostatním uživatelům. 


### <a name="configure-the-client-application"></a>Konfigurovat klientskou aplikaci
Vytvořili jsme klientskou aplikaci, která se připojuje k datům pomocí Twitteru [API Streaming na Twitteru](https://dev.twitter.com/streaming/overview) shromažďování událostí tweet o konkrétní sadu témat. Aplikace používá [Sentiment140](http://help.sentiment140.com/) open source nástroj, který přiřazuje následující mínění hodnotu pro každý tweet:

* 0 = záporná
* 2 = neutrální
* 4 = pozitivní

Po události tweet byla přiřazena hodnota mínění, jsou vloženy do centra událostí, který jste vytvořili dříve.

Předtím, než aplikaci spustí, vyžaduje určité informace z vašeho počítače, jako je Twitter klíče a připojovací řetězec centra událostí. Můžete zadat informace o konfiguraci následujícími způsoby:

* Spusťte aplikaci a pak zadejte klíče, tajné klíče a připojovací řetězec pomocí uživatelského rozhraní aplikace. Pokud to uděláte, použijí se konfigurační informace pro aktuální relaci, ale není uložen.
* Upravit soubor .config aplikace a nastavte hodnoty existuje. Tento postup se opakuje informace o konfiguraci, ale také to znamená, že tyto potenciálně citlivé informace jsou uloženy ve formátu prostého textu ve vašem počítači.

Následující postup popisuje oba přístupy. 

1. Ujistěte se, že jste stáhli a odblokujte [TwitterWPFClient.zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) aplikace, jak je uvedeno v požadavcích.

2. K nastavení hodnoty v době běhu (a pouze pro aktuální relaci), spusťte `TwitterWPFClient.exe` aplikace. Pokud aplikace zobrazí výzva, zadejte následující hodnoty:

    * Twitter uživatelský klíč (klíč rozhraní API).
    * Twitter uživatelský tajný klíč (tajný klíč rozhraní API).
    * Přístupový Token služby Twitter.
    * Na Twitteru tajný klíč přístupového tokenu.
    * Informace o připojovacím řetězci, který jste předtím uložili. Ujistěte se, že používáte připojovací řetězec, který jste odebrali `EntityPath` páru klíč hodnota z.
    * Klíčová slova Twitteru, které chcete zjistit mínění u.

   ![TwitterWpfClient aplikace spuštěná, zobrazuje zakryt nastavení](./media/stream-analytics-twitter-sentiment-analysis-trends/wpfclientlines.png)

3. Nastavit hodnoty trvale, pomocí textového editoru otevřete soubor TwitterWpfClient.exe.config. Pak v `<appSettings>` elementu, to udělat:

    * Nastavte `oauth_consumer_key` na uživatelský klíč pro Twitter (klíč rozhraní API). 
    * Nastavte `oauth_consumer_secret` na Twitteru uživatelský tajný klíč (tajný klíč rozhraní API).
    * Nastavte `oauth_token` na přístupový Token služby Twitter.
    * Nastavte `oauth_token_secret` na Twitteru tajný klíč tokenu přístupu.

    Dále v `<appSettings>` elementu, proveďte tyto změny:

    * Nastavte `EventHubName` na název centra událostí (to znamená na hodnotu cesta entity).
    * Nastavte `EventHubNameConnectionString` na připojovací řetězec. Ujistěte se, že používáte připojovací řetězec, který jste odebrali `EntityPath` páru klíč hodnota z.

    `<appSettings>` Oddíl vypadá jako v následujícím příkladu. (Pro přehlednost a zabezpečení, jsme zabaleny některé řádky a odebrat některé znaky.)

    ![Konfigurační soubor aplikace TwitterWpfClient v textovém editoru, zobrazuje Twitteru klíče a tajné kódy a připojovacího řetězce centra událostí](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-tiwtter-app-config.png)
 
4. Pokud už jste nezahájili aplikaci, spusťte nyní TwitterWpfClient.exe. 

5. Klikněte na tlačítko start zelené shromažďovat sociální mínění. Zobrazit události Tweet s **CreatedAt**, **tématu**, a **SentimentScore** hodnoty odesílaných do vašeho centra událostí.

    ![Spuštění aplikace TwitterWpfClient zobrazující seznam tweetů](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-app-listing.png)

    >[!NOTE]
    >Pokud se zobrazí chyby a datového proudu tweetů zobrazí v dolní části okna nevidíte, zkontrolujte klíče a tajné kódy. Také zkontrolujte připojovací řetězec (ujistěte se, že neobsahuje `EntityPath` klíč a hodnotu.)


## <a name="create-a-stream-analytics-job"></a>Vytvoření úlohy Stream Analytics

Teď, když jsou události tweet streamování v reálném čase z Twitteru, můžete nastavit úlohu Stream Analytics analyzovat tyto události v reálném čase.

1. Na webu Azure Portal, klikněte na tlačítko **vytvořit prostředek** > **Internet of Things** > **úlohy Stream Analytics**.

2. Název úlohy `socialtwitter-sa-job` a určete předplatné, skupinu prostředků a umístění.

    Je vhodné umístit tato úloha a centra událostí ve stejné oblasti pro zajištění nejlepšího výkonu a tak, že neplatíte k přenosu dat mezi oblastmi.

    ![Vytvoření nové úlohy Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/newjob.png)

3. Klikněte na možnost **Vytvořit**.

    Vytvoření úlohy a na portálu se zobrazí podrobnosti o úloze.


## <a name="specify-the-job-input"></a>Zadejte vstup úlohy

1. Ve vaší úlohy Stream Analytics v části **topologie úlohy** uprostřed okno úlohy, klikněte na tlačítko **vstupy**. 

2. V **vstupy** okna, klikněte na tlačítko  **+ &nbsp;přidat** a potom vyplňte okno s těmito hodnotami:

    * **Vstupní alias**: Použijte název `TwitterStream`. Pokud použijete jiný název, poznamenejte to protože ji budete potřebovat později.
    * **Typ zdroje**: Vyberte **datový proud**.
    * **Zdroj**: Vyberte **centra událostí**.
    * **Možnost importu**: Vyberte **použijte Centrum událostí z aktuálního předplatného**. 
    * **Obor názvů služby Service bus**: Vyberte obor názvů centra událostí, který jste vytvořili dříve (`<yourname>-socialtwitter-eh-ns`).
    * **Centrum událostí**: Vyberte Centrum událostí, který jste vytvořili dříve (`socialtwitter-eh`).
    * **Název zásady centra událostí**: Vyberte zásady přístupu, kterou jste vytvořili (`socialtwitter-access`).

    ![Vytvořit nový vstup pro úlohu Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-new-input.png)

3. Klikněte na možnost **Vytvořit**.


## <a name="specify-the-job-query"></a>Zadejte dotaz na úlohu

Stream Analytics podporuje jednoduchý a deklarativní dotazu model, který popisuje transformace. Další informace o jazyku, najdete v článku [referenčních informacích k Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx).  V tomto kurzu vám pomůže vytvářet a testovat několik dotazů nad daty Twitter.

Chcete-li porovnat číslo zmínky mezi témata, můžete použít [aktivační událost pro Přeskakující okno](https://msdn.microsoft.com/library/azure/dn835055.aspx) získat počet zmínky podle témat každých pět sekund.

1. Zavřít **vstupy** okno, pokud jste tak již neučinili.

2. V **přehled** okna, klikněte na tlačítko **upravit dotaz** u horního napravo od pole dotazu. Azure zobrazuje vstupy a výstupy, které jsou nakonfigurovány pro úlohy a umožňuje vám vytvořit dotaz, který umožňuje transformovat vstupní datový proud, jako jsou odeslána do výstupu.

3. Ujistěte se, že je spuštěna aplikace TwitterWpfClient. 

3. V **dotazu** okna, klikněte na tečky vedle `TwitterStream` vstup a potom vyberte **ukázková data ze vstupu**.

    ![Možnosti nabídky ukázková data pro položku projektu Stream Analytics, s "Ukázková data ze vstupu" vybraná](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-sample-data-from-input.png)

    Otevře se okno, které umožňuje určit, kolik ukázkových dat získáte definovat z hlediska dobu ke čtení vstupního datového proudu.

4. Nastavte **minut** 3 a pak klikněte na tlačítko **OK**. 
    
    ![Možnosti vzorkování vstupního datového proudu s "3" – vybraný počet minut.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-input-create-sample-data.png)

    Azure ukázky data ze vstupního datového proudu za 3 minuty a vás upozorní, když jsou ukázková data připravená. (To nějakou dobu trvá.) 

    Ukázková data jsou dočasně uložena a jsou dostupná, dokud je otevřené okno dotazu. Pokud okno dotazu zavřete, ukázková data odstraněna a budete muset vytvořit novou sadu ukázková data. 

5. Změňte dotaz v editoru kódu takto:

    ```
    SELECT System.Timestamp as Time, Topic, COUNT(*)
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY TUMBLINGWINDOW(s, 5), Topic
    ```

    Pokud nepoužili `TwitterStream` jako alias pro vstup, nahraďte váš alias pro `TwitterStream` v dotazu.  

    Tento dotaz používá **TIMESTAMP BY** – klíčové slovo k určení pole časového razítka v datové části pro použití v dočasné výpočty. Pokud toto pole není zadán, oddílová operace se provádí na základě doby, které byly přijaty každé události centra událostí. Další informace najdete v části "Čas doručení vs čas aplikace" [referenční příručka k Stream Analytics Query](https://msdn.microsoft.com/library/azure/dn834998.aspx).

    Tento dotaz také přistupuje pomocí časového razítka na konci každé okno **System.Timestamp** vlastnost.

5. Klikněte na tlačítko **Test**. Spuštění dotazu na data, která je vzorkována.
    
6. Klikněte na **Uložit**. Tento dotaz uloží jako součást úlohy Stream Analytics. (Ho neukládá ukázková data.)


## <a name="experiment-using-different-fields-from-the-stream"></a>Experimentování s využitím různých polí z datového proudu 

Následující tabulka obsahuje pole, které jsou součástí JSON streamovaná data. Bez obav experimentovat v editoru dotazů.

|Vlastnost JSON | Definice|
|--- | ---|
|createdAt | Čas, který byl vytvořen tweetu|
|Téma | Téma, který odpovídá zadané klíčové slovo|
|SentimentScore | Skóre mínění z Sentiment140|
|Autor | Popisovač Twitteru, který odeslal tweet|
|Text | Úplný text tweetu|


## <a name="create-an-output-sink"></a>Vytvoření výstupní jímky

Nyní jste definovali datového proudu událostí, Centrum událostí vstupu pro ingestování událostí a dotaz, který provádí transformaci u datového proudu. Posledním krokem je definování výstupní jímky pro úlohu.  

V tomto kurzu můžete zapisovat události agregované tweet z dotazu úlohy do Azure Blob storage.  Můžete také odeslat výsledky do služby Azure SQL Database, Azure Table storage, služby Event Hubs nebo Power BI, v závislosti na vaše aplikace potřebuje.

## <a name="specify-the-job-output"></a>Zadejte výstup úlohy

1. V **topologie úlohy** klikněte na tlačítko **výstup** pole. 

2. V **výstupy** okna, klikněte na tlačítko  **+ &nbsp;přidat** a potom vyplňte okno s těmito hodnotami:

    * **Alias pro výstup**: Použijte název `TwitterStream-Output`. 
    * **Jímka**: Vyberte **Úložiště objektů blob**.
    * **Možnosti importu**: Vyberte **používání úložiště blob z aktuálního předplatného**.
    * **Účet úložiště**. Vyberte **vytvořit nový účet úložiště.**
    * **Účet úložiště** (druhého pole). Zadejte `YOURNAMEsa`, kde `YOURNAME` je vaše jméno nebo jiný jedinečný řetězec. Název lze použít pouze malá písmena a číslice a musí být jedinečné v Azure. 
    * **Kontejner**. Zadejte `socialtwitter`.
    Název účtu úložiště a název kontejneru se používají společně k zajištění identifikátor URI pro úložiště objektů blob, následujícím způsobem: 

    `http://YOURNAMEsa.blob.core.windows.net/socialtwitter/...`
    
    !["Nový výstup" okno pro úlohu Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-output-blob-storage.png)
    
4. Klikněte na možnost **Vytvořit**. 

    Azure vytvoří účet úložiště a automaticky vygeneruje klíč. 

5. Zavřít **výstupy** okno. 


## <a name="start-the-job"></a>Spuštění úlohy

Vstup úlohy, dotazu a výstupu jsou uvedeny. Jste připraveni spustit úlohu Stream Analytics.

1. Ujistěte se, že je spuštěna aplikace TwitterWpfClient. 

2. V okně úlohy klikněte na tlačítko **Start**.

    ![Spuštění úlohy Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-output.png)

3. V **spuštění úlohy** okně pro **čas spuštění výstupu úlohy**vyberte **nyní** a potom klikněte na tlačítko **Start**. 

    !["Spuštění úlohy" okno pro úlohu Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-job-blade.png)

    Azure vás upozorní, když byla úloha spuštěna, a v okně úlohy, stav se zobrazí jako **systémem**.

    ![Spuštěná úloha](./media/stream-analytics-twitter-sentiment-analysis-trends/jobrunning.png)

## <a name="view-output-for-sentiment-analysis"></a>Zobrazení výstupu pro analýzu mínění

Poté, co vaše úloha byla spuštěna a je zpracování streamů v reálném čase Twitteru, můžete zobrazit výstup pro analýzu mínění.

Můžete použít nástroje, jako je [Průzkumníka služby Azure Storage](https://storageexplorer.com/) nebo [Průzkumníka služby Azure](http://www.cerebrata.com/products/azure-explorer/introduction) k zobrazení výstupu úlohy v reálném čase. Z tohoto místa můžete použít [Power BI](https://powerbi.com/) rozšířit vaši aplikaci a vložte přizpůsobeného řídicího panelu jako na následujícím snímku obrazovky:

![Power BI](./media/stream-analytics-twitter-sentiment-analysis-trends/power-bi.png)


## <a name="create-another-query-to-identify-trending-topics"></a>Vytvořit jiný dotaz k identifikaci populárních témat

Je na základě jiný dotaz, můžete použít k pochopení sentimentu Twitter [posuvné okno](https://msdn.microsoft.com/library/azure/dn835051.aspx). Chcete-li identifikace populárních témat, vyhledejte témata, které překračují prahovou hodnotu pro zmínky ve stanoveném čase.

Pro účely tohoto kurzu vyhledejte témata, která se nachází více než 20krát v posledních 5 sekund.

1. V okně úlohy klikněte na tlačítko **Zastavit** o zastavení úlohy. 

2. V **topologie úlohy** klikněte na tlačítko **dotazu** pole. 

3. Změňte dotaz na následující:

    ```    
    SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY SLIDINGWINDOW(s, 5), topic
    HAVING COUNT(*) > 20
    ```

4. Klikněte na **Uložit**.

5. Ujistěte se, že je spuštěna aplikace TwitterWpfClient. 

6. Klikněte na tlačítko **Start** restartovat úlohu pomocí nový dotaz.


## <a name="get-support"></a>Získat podporu
Potřebujete další pomoc, vyzkoušejte naše [fóru Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další postup
* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
