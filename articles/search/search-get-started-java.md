---
title: Začínáme s Azure Search v Javě | Dokumentace Microsoftu
description: Jak vytvořit hostovanou cloudovou vyhledávací aplikaci v Azure pomocí programovacího jazyka Java.
services: search
author: jj09
manager: jlembicz
ms.service: search
ms.topic: conceptual
ms.date: 07/14/2016
ms.author: jjed
ms.openlocfilehash: d95abab1d1a489deba2ebc260e1e32963aa5639e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="get-started-with-azure-search-in-java"></a>Začínáme s Azure Search v Javě
> [!div class="op_single_selector"]
> * [Azure Portal](search-get-started-portal.md)
> * [.NET](search-howto-dotnet-sdk.md)
> 
> 

Naučte se sestavit vlastní vyhledávací aplikaci Java, která k hledání používá službu Azure Search. V tomto kurzu se pomocí [rozhraní REST API služby Azure Search](https://msdn.microsoft.com/library/dn798935.aspx) vytvoří objekty a operace, které se použijí v tomto cvičení.

Pokud chcete tuto ukázku spustit, musíte mít službu Azure Search, ke které se můžete zaregistrovat na webu [Azure Portal](https://portal.azure.com). Podrobné pokyny najdete v tématu [Vytvoření služby Azure Search na portálu](search-create-service-portal.md).

Pro vytvoření a testování tohoto příkladu jsme použili následující software:

* [Integrované vývojové prostředí Eclipse pro vývojáře v jazyce Java EE](https://eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/lunar) Ujistěte se, že jste stáhli verzi EE. Jeden z kroků ověření vyžaduje funkci, která se nachází pouze v této edici.
* [JDK 8u40](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Apache Tomcat 8.0](http://tomcat.apache.org/download-80.cgi)

## <a name="about-the-data"></a>Informace o datech
Tato ukázková aplikace používá data agentury [United States Geological Services (USGS)](http://geonames.usgs.gov/domestic/download_data.htm), která jsou filtrovaná pro stát Rhode Island, aby se zmenšila velikost datové sady. Pomocí těchto dat sestavíme vyhledávací aplikaci, která najde významné budovy, například nemocnice a školy, a geologické prvky, jako jsou vodní toky, jezera a vrcholy.

Program **SearchServlet.java** v této aplikaci sestaví a načte index pomocí konstruktoru [Indexer](https://msdn.microsoft.com/library/azure/dn798918.aspx), přičemž načte filtrovanou sadu dat USGS z veřejné databáze Azure SQL Database. Předdefinované přihlašovací údaje a informace o připojení k online zdroji dat jsou uvedené v kódu programu. Z hlediska přístupu k datům není potřeba žádná další konfigurace.

> [!NOTE]
> U této sady dat jsme použili filtr, abychom dodrželi omezení 10 000 dokumentů pro cenovou úroveň Free. Pokud používáte úroveň Standard, toto omezení se na vás nevztahuje a můžete upravit tento kód, aby používal větší datovou sadu. Podrobnosti týkající se kapacity u jednotlivých cenových úrovní najdete v tématu [Omezení](search-limits-quotas-capacity.md).
> 
> 

## <a name="about-the-program-files"></a>O souborech programu
Následující seznam popisuje soubory, které se vztahují k tomuto příkladu.

* Search.jsp: Poskytuje uživatelské rozhraní.
* SearchServlet.java: Poskytuje metody (podobně jako řadič v MVC).
* SearchServiceClient.java: Zpracovává požadavky HTTP.
* SearchServiceHelper.java: Pomocná třída, která poskytuje statické metody.
* Document.java: Poskytuje datový model.
* config.properties: Nastavuje adresu URL služby Search a klíč api-key.
* Pom.xml: Závislost Maven

<a id="sub-2"></a>

## <a name="find-the-service-name-and-api-key-of-your-azure-search-service"></a>Nalezení názvu služby a klíče api-key služby Azure Search
Všechna volání rozhraní API REST služby Azure Search vyžadují, abyste zadali adresu URL služby a klíč api-key. 

1. Přihlaste se k [Portálu Azure](https://portal.azure.com).
2. Na panelu odkazů klikněte na **Služba Search** a zobrazte výpis všech služeb Azure Search zřízených pro předplatné.
3. Vyberte službu, kterou chcete použít.
4. Na řídicím panelu služby uvidíte dlaždice se základními informacemi a ikonu klíče pro přístup ke klíčům správce.
   
      ![][3]
5. Zkopírujte adresu URL služby a klíč správce. Budete je potřebovat později, až je budete přidávat do souboru **config.properties**.

## <a name="download-the-sample-files"></a>Stažení ukázkových souborů
1. Přejděte na [search-java-indexer-demo](https://github.com/Azure-Samples/search-java-indexer-demo) on GitHub.
2. Klikněte na **Stáhnout ZIP**, uložte soubor .zip na disk a potom z něj extrahujte všechny soubory. Zvažte extrahování souborů do pracovního prostoru Java, aby bylo později snazší projekt najít.
3. Ukázkové soubory jsou jen pro čtení. Klikněte pravým tlačítkem na vlastnosti složky a vymažte atribut jen pro čtení.

Všechny následné úpravy souborů a spouštěné příkazy se budou provádět na souborech v této složce.  

## <a name="import-project"></a>Import projektu
1. V prostředí Eclipse zvolte **Soubor** > **Import** > **Obecné** > **Existující projekty do pracovního prostoru**.
   
    ![][4]
2. V okně **Vybrat kořenový adresář** přejděte do složky obsahující ukázkové soubory. Vyberte složku, která obsahuje složku .project. Projekt by se měl zobrazit v seznamu **Projekty** jako vybraná položka.
   
    ![][12]
3. Klikněte na **Dokončit**.
4. Pomocí **Prohlížeče projektu** můžete zobrazit a upravit soubory. Pokud ještě není otevřený, klikněte na **Okno** > **Zobrazit zobrazení** > **Prohlížeč projektu** nebo ho otevřete pomocí klávesové zkratky.

## <a name="configure-the-service-url-and-api-key"></a>Konfigurace adresy URL služby a klíče api-key
1. V **Prohlížeči projektu**, dvakrát klikněte na soubor **config.properties**, abyste upravili nastavení konfigurace obsahující název serveru a klíč api-key.
2. Podívejte se na postup uvedený dříve v tomto článku, kde jste našli adresu URL služby a klíč api-key v [webu Azure Portal](https://portal.azure.com), abyste získali hodnoty, které nyní zadáte do souboru **config.properties**.
3. V souboru **config.properties** nahraďte položku „Api Key“ klíčem api-key pro vaši službu. Další, název služby (první komponenta adresy URL http://servicename.search.windows.net) nahradí "service name" ve stejném souboru.
   
    ![][5]

## <a name="configure-the-project-build-and-runtime-environments"></a>Konfigurace prostředí projektu, buildu a běhového prostředí
1. V prostředí Eclipse v Prohlížeči projektu klikněte pravým tlačítkem myši na projekt > **Vlastnosti** > **Omezující vlastnosti projektu**.
2. Vyberte **Dynamic Web Module**, **Java** a **JavaScript**.
   
    ![][6]
3. Klikněte na **Použít**.
4. Vyberte **Okno** > **Předvolby** > **Server** > **Běhová prostředí** > **Přidat**.
5. Rozbalte položku Apache a vyberte verzi serveru Apache Tomcat, kterou jste dříve nainstalovali. V našem systému jsme nainstalovali verzi 8.
   
    ![][7]
6. Na další stránce zadejte instalační adresář Tomcat. V počítači s Windows to bude pravděpodobně C:\Program Files\Apache Software Foundation\Tomcat *verze*.
7. Klikněte na **Dokončit**.
8. Vyberte **Okno** > **Předvolby** > **Java** > **Nainstalovaná prostředí JRE** > **Přidat**.
9. V okně **Přidat prostředí JRE**, vyberte **Standardní virtuální počítač**.
10. Klikněte na **Další**.
11. V definici prostředí JRE v kořenovém adresáři JRE klikněte na **Adresář**.
12. Přejděte do adresáře **Program Files** > **Java** a vyberte sadu JDK, kterou jste dříve nainstalovali. Je důležité vybrat jako prostředí JRE sadu JDK.
13. V okně Nainstalovaná prostředí JRE zvolte **JDK**. Vaše nastavení by mělo vypadat jako na následujícím snímku obrazovky.
    
    ![][9]
14. Volitelně vyberte **Okno** > **Webový prohlížeč** > **Internet Explorer**, aby se aplikaci spustila v okně externího prohlížeče. Použití externího prohlížeče poskytuje lepší uživatelské prostředí webové aplikace.
    
    ![][8]

Nyní jste dokončili úlohy konfigurace. V dalším kroku sestavíte a spustíte projekt.

## <a name="build-the-project"></a>Sestavení projektu
1. V Prohlížeči projektu klikněte pravým tlačítkem na název projektu a zvolte **Spustit jako** > **Build Maven...**, abyste nakonfigurovali projekt.
   
    ![][10]
2. V okně Upravit konfiguraci v části Cíle zadejte „clean install“ a pak klikněte na **Spustit**.

Stavové zprávy se zobrazují v okně konzoly. Měli byste vidět zprávu o úspěšném sestavení, která oznamuje sestavení projektu bez chyb.

## <a name="run-the-app"></a>Spusťte aplikaci
V tomto posledním kroku spustíte aplikaci v běhovém prostředí místního serveru.

Pokud jste v prostředí Eclipse ještě neurčili běhové prostředí serveru, budete to muset učinit nyní.

1. V Prohlížeči projektu rozbalte položku **WebContent**.
2. Klikněte pravým tlačítkem na **Search.jsp** > **Spustit jako** > **Spustit na serveru**. Vyberte server Apache Tomcat a potom klikněte na **Spustit**.

> [!TIP]
> Pokud jste k uložení projektu použili jiný pracovní prostor než výchozí, budete muset upravit **Konfiguraci spuštění**, aby odkazovala na umístění projektu a nedošlo k chybě při spuštění serveru. V Prohlížeči projektu klikněte pravým tlačítkem na **Search.jsp** > **Spustit jako** > **Konfigurace spuštění**. Vyberte server Apache Tomcat. Klikněte na **Argumenty**. Klikněte na **Pracovní prostor** nebo **Systém souborů**, abyste nastavili složku obsahující projekt.
> 
> 

Při spuštění aplikace by se mělo zobrazit okno prohlížeče, které poskytuje vyhledávací pole pro zadání termínů.

Před kliknutím na tlačítko **Hledat** počkejte přibližně jednu minutu, aby měla služba čas na vytvoření a načtení indexu. Pokud se zobrazí chyba HTTP 404, bude třeba před dalším pokusem ještě chvíli počkat.

## <a name="search-on-usgs-data"></a>Hledání v datech USGS
Sada dat USGS obsahuje záznamy, které se vztahují ke státu Rhode Island. Pokud u prázdného vyhledávacího pole kliknete na tlačítko **Hledat**, obdržíte prvních 50 položek, což je výchozí nastavení.

Když zadáte hledaný výraz, vyhledávací web bude mít s čím pracovat. Zkuste zadat místní název. Roger Williams byl prvním guvernérem státu Rhode Island. Je po něm pojmenovaná celá řada parků, budov a škol.

![][11]

Může taky zkusit kterýkoli z těchto výrazů:

* Pawtucket
* Pembroke
* goose +cape

## <a name="next-steps"></a>Další postup
Toto je první kurz služby Azure Search založený na Javě a sadě dat USGS. Postupně ho budeme rozšiřovat o ukázky dalších vyhledávacích funkcí, které by se vám ve vlastních řešeních mohly hodit.

Pokud již máte základní vědomosti o službě Azure Search, můžete tuto ukázku použít jako odrazový můstek pro další experimentování, případně rozšíření [stránky vyhledávání](search-pagination-page-layout.md) nebo implementaci [fasetové navigace](search-faceted-navigation.md). Můžete taky zdokonalit stránku výsledků hledání přidáním počtů a dávkováním dokumentů, aby se výsledky daly procházet po stránkách.

Jste nováčky ve službě Azure Search? Doporučujeme vyzkoušet ostatní kurzy a vytvořit si představu o tom, co se dá vytvořit. Pokud hledáte další zdroje, přejděte na [stránku dokumentace](https://azure.microsoft.com/documentation/services/search/). Přístup k dalším informacím vám poskytnou taky odkazy v [Seznamu videí a kurzů](search-video-demo-tutorial-list.md).

<!--Image references-->
[1]: ./media/search-get-started-java/create-search-portal-1.PNG
[2]: ./media/search-get-started-java/create-search-portal-21.PNG
[3]: ./media/search-get-started-java/create-search-portal-31.PNG
[4]: ./media/search-get-started-java/AzSearch-Java-Import1.PNG
[5]: ./media/search-get-started-java/AzSearch-Java-config1.PNG
[6]: ./media/search-get-started-java/AzSearch-Java-ProjectFacets1.PNG
[7]: ./media/search-get-started-java/AzSearch-Java-runtime1.PNG
[8]: ./media/search-get-started-java/AzSearch-Java-Browser1.PNG
[9]: ./media/search-get-started-java/AzSearch-Java-JREPref1.PNG
[10]: ./media/search-get-started-java/AzSearch-Java-BuildProject1.PNG
[11]: ./media/search-get-started-java/rogerwilliamsschool1.PNG
[12]: ./media/search-get-started-java/AzSearch-Java-SelectProject.png
