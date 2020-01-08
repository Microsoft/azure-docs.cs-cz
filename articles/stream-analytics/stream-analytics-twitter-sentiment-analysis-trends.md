---
title: Analýza mínění na Twitteru v reálném čase pomocí Azure Stream Analytics
description: Tento článek popisuje, jak použít Stream Analytics analýzy mínění pro Twitter v reálném čase. Podrobné pokyny pro generování událostí pro data na aktivním řídicím panelu.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/09/2019
ms.openlocfilehash: f3ab21d55b7d59bb58760bfba452b4ea2d103496
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75369894"
---
# <a name="real-time-twitter-sentiment-analysis-in-azure-stream-analytics"></a>Analýza mínění ve službě Twitter v reálném čase v Azure Stream Analytics

Naučte se vytvářet řešení analýzy mínění pro analýzu sociálních médií díky využití Twitterových událostí v reálném čase do Azure Event Hubs. Pak napíšete Azure Stream Analytics dotaz k analýze dat a uložení výsledků pro pozdější použití nebo vytvoření [Power BIho](https://powerbi.com/) řídicího panelu pro poskytování přehledů v reálném čase.

Nástroje pro analýzu sociálních médií můžou organizacím pochopit témata týkající se trendů. Témata týkající se trendů jsou předměty a postojů, které mají velký objem příspěvků na sociálních médiích. Analýza mínění, která se také nazývá *dolování*, využívá analytické nástroje pro sociální média k určení postojů k produktu nebo nápadu. 

Analýza trendů na Twitteru v reálném čase je skvělým příkladem analytického nástroje, protože model předplatného hashtagu vám umožňuje naslouchat konkrétním klíčovým slovům (hashtagy) a vyvíjet mínění analýzu informačního kanálu.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Scénář: analýza míněníů na sociálních médiích v reálném čase

Společnost, která má web s novinkami na pracovišti, má za následek získání výhody oproti konkurenci díky obsahu webu, který je okamžitě relevantní pro příslušné čtenáře. Společnost používá analýzu sociálních médií v tématech, která jsou relevantní pro čtenáře, díky analýze mínění dat na Twitteru v reálném čase.

Aby bylo možné v reálném čase na Twitteru identifikovat témata týkající se trendů, společnost potřebuje analýzy v reálném čase o svazku na disku a mínění pro klíčové témata.

## <a name="prerequisites"></a>Požadavky
V této příručce se naučíte používat klientskou aplikaci, která se připojuje k Twitteru a hledá tweety s určitými hashtagy (které můžete nastavit). Pokud chcete spustit aplikaci a analyzovat tweety pomocí Azure Stream Analytics, musíte mít následující:

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).
* Účet na [Twitteru](https://twitter.com) .
* Aplikace TwitterWPFClient, která čte kanál Twitteru. Pokud chcete získat tuto aplikaci, Stáhněte si soubor [TwitterWPFClient. zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) z GitHubu a pak balíček rozbalte do složky ve vašem počítači. Pokud chcete zobrazit zdrojový kód a spustit aplikaci v ladicím programu, můžete získat zdrojový kód z [GitHubu](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TwitterClient). 

## <a name="create-an-event-hub-for-streaming-analytics-input"></a>Vytvoření centra událostí pro vstup Stream Analytics

Ukázková aplikace generuje události a předává je do centra událostí Azure. Centra událostí Azure jsou upřednostňovanou metodou ingestování událostí pro Stream Analytics. Další informace najdete v dokumentaci k [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md).

### <a name="create-an-event-hub-namespace-and-event-hub"></a>Vytvoření oboru názvů centra událostí a centra událostí
Vytvořte obor názvů centra událostí a pak přidejte do tohoto oboru názvů centrum událostí. Obory názvů centra událostí se používají k logickému seskupení souvisejících instancí sběrnice událostí. 

1. Přihlaste se k Azure Portal a klikněte na **vytvořit prostředek** > **Internet věcí** > **centrum událostí**. 

2. V okně **vytvořit obor názvů** zadejte název oboru názvů, například `<yourname>-socialtwitter-eh-ns`. Můžete použít libovolný název oboru názvů, ale název musí být platný pro adresu URL a musí být jedinečný v rámci Azure. 
    
3. Vyberte předplatné a vytvořte nebo zvolte skupinu prostředků a pak klikněte na **vytvořit**. 

    ![Vytvoření oboru názvů centra událostí](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-namespace.png)
 
4. Po dokončení nasazení oboru názvů vyhledejte v seznamu prostředků Azure obor názvů centra událostí. 

5. Klikněte na nový obor názvů a v okně oboru názvů klikněte na **+&nbsp;centrum událostí**. 

    ![Tlačítko Přidat centrum událostí pro vytvoření nového centra událostí](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub-button.png)    
 
6. Pojmenujte nové `socialtwitter-eh`centra událostí. Můžete použít jiný název. Pokud to uděláte, poznamenejte si ho, protože ho budete potřebovat později. Pro centrum událostí není nutné nastavovat žádné jiné možnosti.

    ![Okno pro vytvoření nového centra událostí](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-eventhub.png)
 
7. Klikněte na **Vytvořit**.


### <a name="grant-access-to-the-event-hub"></a>Udělení přístupu k centru událostí

Předtím, než může proces odesílat data do centra událostí, musí mít centrum událostí zásadu, která umožňuje odpovídající přístup. Zásady přístupu vytváří připojovací řetězec, který obsahuje informace o autorizaci.

1.  V okně obor názvů událostí klikněte na **Event Hubs** a pak klikněte na název nového centra událostí.

2.  V okně centrum událostí klikněte na **zásady sdíleného přístupu** a pak klikněte na **+&nbsp;přidat**.

    >[!NOTE]
    >Ujistěte se, že pracujete s centrem událostí, nikoli s oborem názvů centra událostí.

3.  Přidejte zásadu nazvanou `socialtwitter-access` a pro **deklaraci identity**vyberte **Spravovat**.

    ![Okno pro vytvoření nové zásady přístupu k centru událostí](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-shared-access-policy-manage.png)
 
4.  Klikněte na **Vytvořit**.

5.  Po nasazení zásady klikněte na ni v seznamu zásad sdíleného přístupu.

6.  Vyhledejte box s názvem **připojovací řetězec – primární klíč** a klikněte na tlačítko Kopírovat vedle připojovacího řetězce. 
    
    ![Kopíruje se klíč primárního připojovacího řetězce ze zásad přístupu.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-shared-access-policy-copy-connection-string.png)
 
7.  Vložte připojovací řetězec do textového editoru. Tento připojovací řetězec budete potřebovat pro další část, a to po provedení některých malých úprav.

    Připojovací řetězec vypadá takto:

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh

    Všimněte si, že připojovací řetězec obsahuje několik párů klíč-hodnota oddělených středníky: `Endpoint`, `SharedAccessKeyName`, `SharedAccessKey`a `EntityPath`.  

    > [!NOTE]
    > Z důvodu zabezpečení byly části připojovacího řetězce v příkladu odebrány.

8.  V textovém editoru odeberte z připojovacího řetězce dvojici `EntityPath` (Nezapomeňte odebrat středník, který ho předchází). Až budete hotovi, připojovací řetězec vypadá takto:

        Endpoint=sb://YOURNAME-socialtwitter-eh-ns.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=


## <a name="configure-and-start-the-twitter-client-application"></a>Konfigurace a spuštění klientské aplikace Twitter
Klientská aplikace získává události v rámci služby Twitter přímo z Twitteru. Aby to bylo možné, potřebuje mít oprávnění volat rozhraní API pro streamování Twitteru. Ke konfiguraci tohoto oprávnění vytvoříte aplikaci na Twitteru, která generuje jedinečné přihlašovací údaje (například token OAuth). Pak můžete nakonfigurovat, aby klientská aplikace používala tyto přihlašovací údaje při volání rozhraní API. 

### <a name="create-a-twitter-application"></a>Vytvoření aplikace Twitter
Pokud ještě nemáte aplikaci Twitter, kterou můžete použít pro tento průvodce, můžete ji vytvořit. Musíte mít už účet na Twitteru.

> [!NOTE]
> Přesný proces na Twitteru pro vytvoření aplikace a získání klíčů, tajných kódů a tokenu se může změnit. Pokud se tyto pokyny neshodují s tím, co vidíte na webu Twitteru, přečtěte si dokumentaci pro vývojáře na Twitteru.

1. Ve webovém prohlížeči klikněte na [Twitter pro vývojáře](https://developer.twitter.com/en/apps)a vyberte **vytvořit aplikaci**. Může se zobrazit zpráva oznamující, že je potřeba požádat o vývojářský účet pro Twitter. Nebojte se tak a po schválení vaší aplikace by se měl zobrazit potvrzovací e-mail. Schválení pro vývojářský účet může trvat několik dní.

   ![Potvrzení účtu vývojáře pro Twitter](./media/stream-analytics-twitter-sentiment-analysis-trends/twitter-dev-confirmation.png "Potvrzení účtu vývojáře pro Twitter")

   ![Podrobnosti o aplikaci Twitter](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details.png "Podrobnosti o aplikaci Twitter")

2. Na stránce **Create an application** (Vytvoření aplikace) zadejte podrobnosti o nové aplikaci a pak vyberte **Create your Twitter application** (Vytvořit aplikaci Twitter).

   ![Podrobnosti o aplikaci Twitter](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details-create.png "Podrobnosti o aplikaci Twitter")

3. Na stránce aplikace vyberte kartu **klíče a tokeny** a zkopírujte hodnoty **klíč rozhraní API příjemce** a **tajného klíče rozhraní API příjemce**. Pokud chcete generovat přístupové tokeny, vyberte také možnost **vytvořit** v části **přístupový token a tajný klíč přístupového tokenu** . Zkopírujte hodnoty **Access Token** (Přístupový token) a **Access Token Secret** (Tajný klíč přístupového tokenu).

    ![Podrobnosti o aplikaci Twitter](./media/stream-analytics-twitter-sentiment-analysis-trends/twitter-app-key-secret.png "Podrobnosti o aplikaci Twitter")

Uložte hodnoty, které jste načetli pro aplikaci Twitter. Hodnoty budete potřebovat později v postupu.

>[!NOTE]
>Klíče a tajné klíče pro aplikaci Twitter poskytují přístup k vašemu účtu na Twitteru. Tyto informace považovat za citlivé, stejně jako u vašeho hesla na Twitteru. Například tyto informace nevložíte do aplikace, kterou udělíte ostatním. 


### <a name="configure-the-client-application"></a>Konfigurace klientské aplikace
Vytvořili jsme klientskou aplikaci, která se připojuje k datům na Twitteru pomocí [rozhraní API streamování na Twitteru](https://dev.twitter.com/streaming/overview) ke shromažďování událostí na základě konkrétní sady témat. Aplikace používá open source nástroj [Sentiment140](http://help.sentiment140.com/) , který každému poli mínění přiřadí následující hodnotu:

* 0 = negativní
* 2 = neutrální
* 4 = pozitivní

Po přiřazení míněních událostí do centra událostí, které jste vytvořili dříve, se dostanou do centra událostí, které jste vytvořili dříve.

Před spuštěním aplikace bude nutné od sebe vyžadovat určité informace, jako jsou klíče Twitteru a připojovací řetězec centra událostí. Informace o konfiguraci můžete zadat následujícími způsoby:

* Spusťte aplikaci a pak použijte uživatelské rozhraní aplikace k zadání klíčů, tajných kódů a připojovacího řetězce. Pokud to uděláte, informace o konfiguraci se použijí pro vaši aktuální relaci, ale neuloží se.
* Upravte soubor. config aplikace a nastavte požadované hodnoty. Tento přístup uchovává informace o konfiguraci, ale také to znamená, že tyto potenciálně citlivé informace jsou uloženy ve formátu prostého textu na vašem počítači.

Následující postup dokumentuje oba přístupy. 

1. Ujistěte se, že jste stáhli a stáhli aplikaci [TwitterWPFClient. zip](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/TwitterClient/TwitterWPFClient.zip) , jak je uvedeno v části požadavky.

2. Chcete-li nastavit hodnoty v době běhu (a pouze pro aktuální relaci), spusťte aplikaci `TwitterWPFClient.exe`. Až se aplikace zobrazí, zadejte následující hodnoty:

    * Klíč příjemce Twitteru (klíč rozhraní API).
    * Tajný klíč příjemce Twitteru (tajný klíč rozhraní API).
    * Přístupový token Twitteru.
    * Tajný kód přístupového tokenu Twitteru
    * Informace o připojovacím řetězci, které jste uložili dříve. Ujistěte se, že používáte připojovací řetězec, ze kterého jste odebrali `EntityPath` dvojici klíč-hodnota.
    * Klíčová slova Twitteru, pro která chcete určit mínění.

   ![Spuštěná aplikace TwitterWpfClient, která zobrazuje překrytá nastavení](./media/stream-analytics-twitter-sentiment-analysis-trends/wpfclientlines.png)

3. Chcete-li hodnoty nastavit trvale, pomocí textového editoru otevřete soubor TwitterWpfClient. exe. config. Poté v prvku `<appSettings>` proveďte tuto akci:

   * Nastavte `oauth_consumer_key` na klíč příjemce Twitteru (klíč rozhraní API). 
   * Nastavte `oauth_consumer_secret` na tajný klíč uživatele Twitteru (tajný kód rozhraní API).
   * Nastavte `oauth_token` přístupového tokenu Twitteru.
   * Nastavte `oauth_token_secret` pro tajný kód přístupového tokenu Twitteru.

     Později v prvku `<appSettings>` proveďte tyto změny:

   * Nastavte `EventHubName` na název centra událostí (tj. na hodnotu v cestě k entitě).
   * Nastavte `EventHubNameConnectionString` na připojovací řetězec. Ujistěte se, že používáte připojovací řetězec, ze kterého jste odebrali `EntityPath` dvojici klíč-hodnota.

     Oddíl `<appSettings>` vypadá jako v následujícím příkladu. (Pro přehlednost a zabezpečení jsme nabaleni některé řádky a odebrali jsme některé znaky.)

     ![Konfigurační soubor aplikace TwitterWpfClient v textovém editoru, který zobrazuje klíče a tajné kódy pro Twitter, a informace o připojovacím řetězci centra událostí](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-tiwtter-app-config.png)
 
4. Pokud jste tuto aplikaci ještě nespustili, spusťte nyní TwitterWpfClient. exe. 

5. Pokud chcete shromažďovat sociální mínění, klikněte na zelené tlačítko Start. Události v poli se zobrazí s hodnotami **CreatedAt**, **témat**a **SentimentScore** odesílaných do centra událostí.

    ![Je spuštěná aplikace TwitterWpfClient, která zobrazuje seznam tweety.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-app-listing.png)

    >[!NOTE]
    >Pokud se zobrazí chyby a v dolní části okna se nezobrazí datový proud tweety, zkontrolujte klíče a tajné kódy. Zkontrolujte také připojovací řetězec (Ujistěte se, že nezahrnuje klíč a hodnotu `EntityPath`.)


## <a name="create-a-stream-analytics-job"></a>Vytvoření úlohy Stream Analytics

Teď, když jsou události ve službě Twitter streamované v reálném čase, můžete nastavit úlohu Stream Analytics pro analýzu těchto událostí v reálném čase.

1. V Azure Portal klikněte na **vytvořit prostředek** > **Internet věcí** **úlohu > Stream Analytics**.

2. Pojmenujte úlohu `socialtwitter-sa-job` a zadejte předplatné, skupinu prostředků a umístění.

    Je vhodné umístit úlohu a centrum událostí ve stejné oblasti, aby se co nejlépe vyzpůsobilo, a nebudete platit za přenos dat mezi oblastmi.

    ![Vytvoření nové úlohy Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/newjob.png)

3. Klikněte na **Vytvořit**.

    Úloha se vytvoří a na portálu se zobrazí podrobnosti o úloze.


## <a name="specify-the-job-input"></a>Zadat vstup úlohy

1. V úloze Stream Analytics klikněte v části **topologie úlohy** uprostřed okna úlohy na možnost **vstupy**. 

2. V okně **vstupy** klikněte na **+&nbsp;přidat** a pak vyplňte okno těmito hodnotami:

   * **Alias vstupu**: použijte název `TwitterStream`. Pokud použijete jiný název, poznamenejte si ho, protože ho budete potřebovat později.
   * **Typ zdroje**: vyberte **datový proud**.
   * **Zdroj**: vyberte **centrum událostí**.
   * **Možnost importu**: vyberte **použít centrum událostí z aktuálního předplatného**. 
   * **Obor názvů služby Service Bus**: vyberte obor názvů centra událostí, který jste vytvořili dříve (`<yourname>-socialtwitter-eh-ns`).
   * **Centrum událostí**: Vyberte centrum událostí, které jste vytvořili dříve (`socialtwitter-eh`).
   * **Název zásady centra událostí**: vyberte zásadu přístupu, kterou jste vytvořili dříve (`socialtwitter-access`).

     ![Vytvoří nový vstup pro úlohu Stream Analytics.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-twitter-new-input.png)

3. Klikněte na **Vytvořit**.


## <a name="specify-the-job-query"></a>Zadat dotaz úlohy

Stream Analytics podporuje jednoduchý deklarativní model dotazu, který popisuje transformace. Další informace o tomto jazyce najdete v referenčních informacích k jazyku [Azure Stream Analytics dotazů](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference).  Tento průvodce vám pomůže vytvořit a otestovat několik dotazů přes data z Twitteru.

Chcete-li porovnat počet zmínk mezi tématy, můžete použít [bubnové okno](https://docs.microsoft.com/stream-analytics-query/tumbling-window-azure-stream-analytics) a získat počet slov, který bude v tématu každých pět sekund.

1. Pokud jste to ještě neudělali, zavřete okno **vstupy** .

2. V okně **Přehled** klikněte na **Upravit dotaz** v pravém horním rohu pole dotazu. Azure obsahuje seznam vstupů a výstupů nakonfigurovaných pro úlohu a umožňuje vytvořit dotaz, který vám umožní transformovat vstupní datový proud při odeslání do výstupu.

3. Ujistěte se, že je spuštěna aplikace TwitterWpfClient. 

3. V okně **dotazu** klikněte na tečky vedle `TwitterStream`ho vstupu a pak vyberte **vzorová data ze vstupu**.

    ![Možnosti nabídky pro použití ukázkových dat pro položku úlohy Stream Analytics s vybranou možností ukázková data ze vstupu](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-sample-data-from-input.png)

    Otevře se okno, které umožňuje určit, kolik ukázkových dat se má získat, a to podle toho, jak dlouho se vstupní datový proud má číst.

4. Nastavte **minuty** na 3 a pak klikněte na **OK**. 
    
    ![Možnosti vzorkování vstupního datového proudu s vybranou možností 3 minuty.](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-input-create-sample-data.png)

    Ukázky Azure 3 minuty v hodnotě dat ze vstupního datového proudu a upozorní vás, až budou ukázková data připravena. (To trvá krátce.) 

    Ukázková data jsou dočasně uložena a jsou dostupná, dokud je otevřené okno dotazu. Pokud zavřete okno dotazu, budou ukázková data zahozena a budete muset vytvořit novou sadu ukázkových dat. 

5. Změňte dotaz v editoru kódu na následující:

    ```
    SELECT System.Timestamp as Time, Topic, COUNT(*)
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY TUMBLINGWINDOW(s, 5), Topic
    ```

    Pokud jste nepoužili `TwitterStream` jako alias pro vstup, nahraďte alias pro `TwitterStream` v dotazu.  

    Tento dotaz používá klíčové slovo **timestamp by** k určení pole časového razítka v datové části, které se má použít při dočasném výpočtu. Není-li toto pole zadáno, bude operace okna provedena pomocí času, kdy byla každá událost doručena do centra událostí. Další informace najdete v části "doba doručení vs – čas použití aplikace" v tématu [Stream Analytics odkaz na dotaz](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference).

    Tento dotaz také přistupuje k časovému razítku pro konec každého okna pomocí vlastnosti **System. timestamp** .

5. Klikněte na tlačítko **test**. Dotaz se spustí pro data, která jste navzorkoval.
    
6. Klikněte na možnost **Uložit**. Tím se dotaz uloží jako součást úlohy Stream Analytics. (Ukázková data neuloží.)


## <a name="experiment-using-different-fields-from-the-stream"></a>Experimentovat s různými poli z datového proudu 

V následující tabulce jsou uvedena pole, která jsou součástí dat streamování JSON. Nebojte se experimentovat v editoru dotazů.

|Vlastnost JSON | Definice|
|--- | ---|
|CreatedAt | Čas vytvoření protváře|
|Téma | Téma, které odpovídá zadanému klíčovému slovu|
|SentimentScore | Mínění skóre z Sentiment140|
|Autor | Popisovač Twitteru, který odeslal propracovanou adresu|
|Text | Úplný text|


## <a name="create-an-output-sink"></a>Vytvoření výstupní jímky

Nyní jste definovali datový proud událostí, vstup centra událostí pro ingestování událostí a dotaz k provedení transformace v datovém proudu. Posledním krokem je definování výstupní jímky pro úlohu.  

V této příručce se naučíte zapisovat agregované události se změnami z dotazu úlohy do úložiště objektů BLOB v Azure.  Výsledky můžete také nabízet Azure SQL Database, Azure Table Storage, Event Hubs nebo Power BI, a to v závislosti na potřebách vaší aplikace.

## <a name="specify-the-job-output"></a>Zadejte výstup úlohy.

1. V části **topologie úlohy** klikněte na **výstupní** pole. 

2. V okně **výstupy** klikněte na **+&nbsp;přidat** a pak vyplňte okno těmito hodnotami:

   * **Alias pro výstup**: použijte název `TwitterStream-Output`. 
   * **Jímka**: vyberte **úložiště objektů BLOB**.
   * **Možnosti importu**: vyberte **použít úložiště objektů BLOB z aktuálního předplatného**.
   * **Účet úložiště**. Vyberte **vytvořit nový účet úložiště.**
   * **Účet úložiště** (druhý rámeček). Zadejte `YOURNAMEsa`, kde `YOURNAME` je vaše jméno nebo jiný jedinečný řetězec. Název lze použít pouze malá písmena a číslice a musí být jedinečné v Azure. 
   * **Kontejner**. Zadejte `socialtwitter`.
     Název účtu úložiště a název kontejneru se společně používají k poskytnutí identifikátoru URI pro úložiště objektů blob, například takto: 

     `http://YOURNAMEsa.blob.core.windows.net/socialtwitter/...`
    
     ![Okno Nový výstup pro úlohu Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-create-output-blob-storage.png)
    
4. Klikněte na **Vytvořit**. 

    Azure vytvoří účet úložiště a automaticky vygeneruje klíč. 

5. Zavřete okno **výstupy** . 


## <a name="start-the-job"></a>Spuštění úlohy

Je určen vstup, dotaz a výstup úlohy. Jste připraveni začít úlohu Stream Analytics.

1. Ujistěte se, že je spuštěna aplikace TwitterWpfClient. 

2. V okně úloha klikněte na **Spustit**.

    ![Spustit úlohu Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-output.png)

3. V okně **Spustit úlohu** pro **čas spuštění výstupu úlohy**vyberte **nyní** a potom klikněte na tlačítko **Spustit**. 

    ![Okno spustit úlohu pro úlohu Stream Analytics](./media/stream-analytics-twitter-sentiment-analysis-trends/stream-analytics-sa-job-start-job-blade.png)

    Azure vás upozorní na zahájení úlohy a v okně úlohy se stav zobrazuje jako **spuštěný**.

    ![Úloha spuštěna](./media/stream-analytics-twitter-sentiment-analysis-trends/jobrunning.png)

## <a name="view-output-for-sentiment-analysis"></a>Zobrazit výstup pro analýzu mínění

Po spuštění úlohy a zpracování streamu Twitteru v reálném čase můžete zobrazit výstup pro mínění analýzu.

K zobrazení výstupu úlohy v reálném čase můžete použít nástroj, jako je [Průzkumník služby Azure Storage](https://storageexplorer.com/) , nebo [Průzkumníka Azure](https://www.cerebrata.com/products/azure-explorer/introduction) . Z tohoto místa můžete použít [Power BI](https://powerbi.com/) k rozšiřování aplikace tak, aby zahrnovala přizpůsobený řídicí panel, jako je ten, který je znázorněný na následujícím snímku obrazovky:

![Power BI](./media/stream-analytics-twitter-sentiment-analysis-trends/power-bi.png)


## <a name="create-another-query-to-identify-trending-topics"></a>Vytvoření dalšího dotazu pro identifikaci témat trendů

Další dotaz, pomocí kterého můžete pochopit Twitter mínění, je založený na [posuvných oknech](https://docs.microsoft.com/stream-analytics-query/sliding-window-azure-stream-analytics). Chcete-li identifikovat témata týkající se trendů, hledejte témata, která v určeném časovém intervalu vycházejí z prahové hodnoty.

Pro účely tohoto postupu se můžete podívat na témata, která se za posledních 5 sekund zmiňují více než 20 minut.

1. V okně úlohy kliknutím na tlačítko **zastavit** úlohu zastavte. 

2. V části **topologie úlohy** klikněte na pole **dotazu** . 

3. Změňte dotaz na následující:

    ```    
    SELECT System.Timestamp as Time, Topic, COUNT(*) as Mentions
    FROM TwitterStream TIMESTAMP BY CreatedAt
    GROUP BY SLIDINGWINDOW(s, 5), topic
    HAVING COUNT(*) > 20
    ```

4. Klikněte na možnost **Uložit**.

5. Ujistěte se, že je spuštěna aplikace TwitterWpfClient. 

6. Kliknutím na tlačítko **Spustit** restartujete úlohu pomocí nového dotazu.


## <a name="get-support"></a>Získat podporu
Potřebujete další pomoc, vyzkoušejte naše [fóru Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další kroky
* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
