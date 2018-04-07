---
title: Použití Azure Stream Analytics tools pro sadu Visual Studio
description: Tento článek popisuje, jak začít používat Azure Stream Analytics Tools pro sadu Visual Studio.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2018
ms.openlocfilehash: eaaee36a2f4dd5a313f8871cea5fd885812fe351
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="use-azure-stream-analytics-tools-for-visual-studio"></a>Použití Azure Stream Analytics tools pro sadu Visual Studio
Azure Stream Analytics tools pro Visual Studio jsou obecně dostupné. Tyto nástroje povolte bohatší možnosti pro uživatele Stream Analytics k řešení potíží a také zápisu složitých dotazů a i psát dotazy místně. Úloha Stream Analytics také můžete exportovat do projektu sady Visual Studio.

## <a name="introduction"></a>Úvod
V tomto kurzu můžete další informace o použití Stream Analytics tools pro Visual Studio k vytvoření, vytváření, testování místně, spravovat a ladit vaše úlohy Stream Analytics. Po vytvoření úlohy, můžete nastavit nepřetržitý proces integraci a nasazení do Azure pomocí balíčku Nuget CI/CD, další informace najdete v tématu [Stream Analytics VS nástroje nastavit kanál CI/CD](stream-analytics-tools-for-visual-studio-cicd.md) článku.

Po dokončení tohoto kurzu, budete moci:

* Seznamte se s Stream Analytics tools pro sadu Visual Studio.
* Nakonfigurujte a nasaďte úloha Stream Analytics.
* Otestujte úlohu místně s místní ukázková data.
* Monitorování použijte k řešení potíží.
* Exportujte stávající úlohy na projekty.

## <a name="prerequisites"></a>Požadavky
Požadovaný k dokončení tohoto kurzu potřebujete:

* Dokončete kroky až "Vytvořit úlohu služby Stream Analytics" v tomto kurzu [sestavení řešení IoT pomocí služby Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-build-an-iot-solution-using-stream-analytics). 
* Nainstalujte Visual Studio 2017, Visual Studio 2015 nebo Visual Studio 2013 Update 4. Enterprise (Ultimate nebo Premium), Professional a Community jsou podporované tyto edice. Express edition není podporován. 
* Postupujte podle [pokyny k instalaci](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install) k instalaci služby Stream Analytics tools pro sadu Visual Studio.

## <a name="create-a-stream-analytics-project"></a>Vytvoření projektu Stream Analytics
V sadě Visual Studio, vyberte **soubor** > **nový projekt**. V seznamu šablon na levé straně vyberte **Stream Analytics**a potom vyberte **Azure Stream Analytics aplikace**.
V dolní části stránky, zadejte projektu **název**, **umístění**, a **název řešení** jako u jiných projektů.

![Vytvoření nového projektu](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-01.png)

Projekt **Projedou** se generuje ve **Průzkumníku řešení**.

![Projedou projekt v Průzkumníku řešení](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-create-project-02.png)

## <a name="choose-the-correct-subscription"></a>Vyberte správné předplatné.
1. Na **zobrazení** nabídce vyberte možnost **Průzkumníka serveru** v sadě Visual Studio.

2. Přihlaste se pomocí účtu Azure. 

## <a name="define-input-sources"></a>Definování vstupních zdrojů
1. V **Průzkumníku řešení**, rozbalte **vstupy** uzlu a přejmenujte **Input.json** k **EntryStream.json**. Double-click **EntryStream.json**.

2. Pro **vstupní Alias**, zadejte **EntryStream**. Všimněte si, že vstupní alias se používá ve skriptu dotazu.

3. Pro **typ zdroje**, vyberte **datový proud**.

4. Pro **zdroj**, vyberte **centra událostí**.

5. Pro **Service Bus Namespace**, vyberte **TollData** možnost v rozevíracím seznamu.

6. Pro **název centra událostí**, vyberte **položka**.

7. Pro **název zásady centra událostí**, vyberte **RootManageSharedAccessKey** (výchozí hodnota).

8. Pro **formát serializace událostí**, vyberte **Json**a pro **kódování**, vyberte **UTF8**.
   
   Nastavení vypadat takto:
   
   ![Zadejte nastavení](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-01.png)
   
9. V dolní části stránky, vyberte **Uložit** ukončíte průvodce. Teď můžete přidat další vstupní zdroj pro vytvoření konec datového proudu. Klikněte pravým tlačítkem myši **vstupy** uzel a vyberte možnost **novou položku**.
   
   ![Nová položka](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-02.png)
   
10. V místním okně, vyberte **Stream Analytics vstup**a změňte **název** k **ExitStream.json**. Vyberte **Přidat**.
   
    ![Přidat novou položku](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-03.png)
   
11. Klikněte dvakrát na **ExitStream.json** v projektu a postupujte podle stejné kroky jako vstupní datový proud vyplnit pole. Pro **název centra událostí**, nezapomeňte zadat **ukončete**, jak je znázorněno na následujícím snímku obrazovky:
   
    ![Nastavení ExitStream](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-04.png)
   
   Nyní jste definovali dva vstupní datové proudy.
   
   ![Dva vstupní datové proudy](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-05.png)
   
   Dál přidejte referenčního datového vstupu pro objekt blob, který obsahuje car registrační data.
   
12. Klikněte pravým tlačítkem myši **vstupy** uzlu v projektu a pak postupujte podle stejný proces pro vstupy datového proudu. Pro **typ zdroje**, vyberte **referenční data**a pro **vstupní Alias**, zadejte **registrace**.
   
    ![Nastavení registrace](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-input-06.png)
   
13. Vyberte **úložiště** účet, který obsahuje možnost s **TollData**. Název kontejneru je **TollData**a **vzorek cesty** je **registration.json**. Tento název souboru je velká a malá písmena a musí být psaný malými písmeny.

14. Vyberte **Uložit** ukončíte průvodce.

Nyní jsou definovány všechny vstupy.

## <a name="define-output"></a>Definujte výstupní
1. V **Průzkumníku řešení**, rozbalte **vstupy** uzel a poklikejte na soubor **Output.json**.

2. Pro **Alias pro výstup**, zadejte **výstup**. Pro **jímky**, vyberte **SQL Database**.

3. Pro **databáze** název, zadejte **TollDataDB**.

4. Pro **uživatelské jméno**, zadejte **tolladmin**. Pro **heslo**, zadejte **123toll!**. Pro **tabulky**, zadejte **TollDataRefJoin**.

5. Vyberte **Uložit**.

   ![Nastavení ukládání výstupu](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-define-output-01.png)
 
## <a name="stream-analytics-query"></a>Stream Analytics dotazu
V tomto kurzu se pokusí odpovězte několik obchodní otázky, které se vztahují k dat pro výběr poplatků. Jsme sestavit dotazy, které lze použít v Stream Analytics k poskytování odpovídající odpovědi. Než začnete vaše první práce Stream Analytics, podíváme se na syntaxi dotazu a jednoduchého scénáře.

### <a name="introduction-to-stream-analytics-query-language"></a>Úvod do služby Stream Analytics dotazovací jazyk
Řekněme, že je potřeba počítat počet vozidel, které projedou stánek zadejte. Protože tento datový proud událostí je souvislý, je nutné definovat v časovém intervalu. Pojďme upravit dotaz a "jak množství prostředků zadejte projedou stánek každé tři minuty?" Toto měření se obvykle označuje jako počet přeskakující.

Podívejme se na dotaz služby Stream Analytics, na který odpoví na tuto otázku:

        SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count 
        FROM EntryStream TIMESTAMP BY EntryTime 
        GROUP BY TUMBLINGWINDOW(minute, 3), TollId 

Jak vidíte, Stream Analytics používá dotazovací jazyk, který je například SQL. Přidá několik rozšíření k určení souvisejících s časem aspekty dotazu.

Další podrobnosti najdete v tématu o [čas management](https://msdn.microsoft.com/library/azure/mt582045.aspx) a [oddílová](https://msdn.microsoft.com/library/azure/dn835019.aspx) konstrukce použitý v dotazu z webu MSDN.

Teď, když jste napsali svůj první dotaz služby Stream Analytics, otestujte ji pomocí ukázkových datových souborů umístěné ve složce TollApp v následující cestě:

**..\TollApp\TollApp\Data**

Tato složka obsahuje následující soubory:

* Entry.json
* Exit.json
* Registration.json

## <a name="question-number-of-vehicles-entering-a-toll-booth"></a>Otázka: Počet vozidel zadávání stánek projedou
V projektu, klikněte dvakrát na **Script.asaql** otevřete skript v editoru. Vložte skript v předchozí části do editoru. Editor dotazů podporuje technologii IntelliSense, barevné zvýrazňování syntaxe a značky k chybě.

![Editor dotazů](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-query-01.png)
 
### <a name="test-stream-analytics-queries-locally"></a>Testování dotazů Stream Analytics místně
Můžete napřed zkompilovat dotaz, jestli jsou všechny chyby syntaxe.

1. K ověření tohoto dotazu proti ukázková data, použijte místní ukázkových dat kliknutím pravým tlačítkem myši na vstupu a výběrem možnosti **přidat místní vstup**.
   
   ![Přidat místní vstup](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-01.png)
   
2. V místním okně vyberte ukázková data z vaší místní cesta. Vyberte **Uložit**.
   
   ![Přidat místní vstup](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-02.png)
   
   Soubor s názvem **local_EntryStream.json** je automaticky přidán do složky vstupy.
   
   ![Seznam souborů místní vstupní složky](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-add-local-input-03.png)
   
3. Vyberte **spustit místně** v editoru dotazů. Nebo můžete stisknutím klávesy F5.
   
   ![Spustit místně](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-01.png)
   
   Můžete získat z výstupu konzoly výstupní cesta. Stisknutím libovolné klávesy otevření složky výsledek.
   
   ![Místní spuštění](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-02.png)
   
4. Zkontrolujte výsledky v místní složce.
   
   ![Výsledek místní složky](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-local-run-03.png)
   
   
### <a name="sample-input"></a>Ukázka vstup
Také můžete zkusit vstupní data z vstupního zdroje do místního souboru. Klikněte pravým tlačítkem na vstupní konfigurační soubor a vyberte **ukázková Data**. 

![Ukázková data](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-01.png)

Všimněte si teď můžete zkusit pouze služby event hubs nebo centra IoT. Jiných vstupních zdrojů nejsou podporovány. V dialogovém okně automaticky otevírané okno vyplňte místní cesta k uložení ukázková data. Vyberte **ukázka**.

![Ukázková data konfigurace](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-02.png)
 
Můžete sledovat průběh v **výstup** okno. 

![Ukázkový výstup dat](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-sample-data-03.png)
 
### <a name="submit-a-stream-analytics-query-to-azure"></a>Odeslat dotaz do služby Azure Stream Analytics
1. V **Editor dotazů**, vyberte **odeslat do Azure** v editoru skriptů.

   ![Odeslat do Azure](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-01.png)
 
2. Vyberte **vytvoření nové úlohy Azure Stream Analytics**. Pro **název úlohy**, zadejte **TollApp**. Vyberte správný **předplatné** v rozevíracím seznamu. Vyberte **odeslání**.

   ![Spustit úlohu](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-submit-job-02.png)

 
### <a name="start-the-job"></a>Spustit úlohu
Teď se vytvoří úlohu, a automaticky se otevře zobrazení úloh. 
1. Kliknutím na tlačítko zelenou šipku spustíte úlohu.

   ![Úloha tlačítko Start](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-01.png)
 
2. Zvolte výchozí nastavení a vyberte **spustit**.
 
   ![Spuštění úlohy](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-02.png)

   Zobrazí stav úlohy změnit tak, aby **systémem**, a nejsou k dispozici události vstupu a výstupu.

   ![Souhrn úlohy a metriky](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-start-job-03.png)

## <a name="check-results-in-visual-studio"></a>Výsledky kontroly v sadě Visual Studio
1. Otevřete Průzkumníka serveru Visual Studia a klikněte pravým tlačítkem myši **TollDataRefJoin** tabulky.

2. Vyberte **zobrazit Data tabulky** na zobrazení výstupu úlohy.
   
   ![Zobrazit Data tabulky](media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-check-results.jpg)
   

### <a name="view-job-metrics"></a>Zobrazit úlohy metriky
Statistikami základní úlohy se zobrazují v **úlohy metriky**. 

![Metriky úlohy](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-metrics-01.png)

 
## <a name="list-the-job-in-server-explorer"></a>Seznam úloh v Průzkumníku serveru
V **Průzkumníka serveru**, vyberte **úlohy Stream Analytics** a pak vyberte **aktualizovat**. Úlohu se zobrazí pod **úlohy Stream Analytics**.

![Seznam úloh](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-list-jobs-01.png)


## <a name="open-the-job-view"></a>Otevřete zobrazení úloh
Rozbalte uzel vaší úlohy a dvakrát klikněte na **zobrazení úloh** uzlu otevřete zobrazení úloh.

![Zobrazení úlohy](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-job-view-01.png)


## <a name="export-an-existing-job-to-a-project"></a>Exportovat stávající úloze do projektu
Existují dva způsoby stávající úloze můžete exportovat do projektu.
* V **Průzkumníka serveru**v části **úlohy Stream Analytics** uzel, klikněte pravým tlačítkem na uzel úlohy. Vyberte **exportovat do nového projektu Stream Analytics**.
   
   ![Exportovat do nového projektu Stream Analytics](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-01.png)
   
   Vygenerovaný projektu se zobrazí v **Průzkumníku řešení**.
   
    ![Úloha Průzkumník řešení](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-02.png)
   
* V zobrazení úloh, vyberte **generovat projektu**.
   
   ![Vytvořit projekt](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-export-job-03.png)
   
## <a name="known-issues-and-limitations"></a>Známé problémy a omezení
 
* Místní testování nebude fungovat, pokud dotaz obsahuje geograficky prostorových funkce.
* Podpora Editor není k dispozici pro přidání nebo změna JavaScript UDF.
* Místní testování nepodporuje ukládání výstupu do formátu JSON. 
* Podpora není k dispozici pro výstup Power BI a ADLS výstup.



## <a name="next-steps"></a>Další postup
* [Úvod do služby Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme s použitím Azure Stream Analytics](stream-analytics-get-started.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics query language – referenční informace](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Správa Azure Stream Analytics odkazu k REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)


