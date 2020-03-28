---
title: Shromažďování protokolů prostředků z prostředku Azure a analýza pomocí Azure Monitoru
description: Kurz konfigurace nastavení diagnostiky pro shromažďování protokolů prostředků z prostředku Azure do pracovního prostoru Analýzy protokolů, kde je lze analyzovat pomocí dotazu protokolu.
ms.subservice: ''
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 12/15/2019
ms.openlocfilehash: d356042d65c419163de4951e64a635a22ea90e6d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78269194"
---
# <a name="tutorial-collect-and-analyze-resource-logs-from-an-azure-resource"></a>Kurz: Shromažďování a analýza protokolů prostředků z prostředku Azure

Protokoly prostředků poskytují přehled o podrobné mandatáře azure a jsou užitečné pro sledování jejich stavu a dostupnosti. Prostředky Azure generují protokoly prostředků automaticky, ale je nutné nakonfigurovat, kde by měly být shromažďovány. Tento kurz vás provede procesem vytváření diagnostického nastavení pro shromažďování protokolů prostředků pro prostředek v předplatném Azure a jeho analýzu pomocí dotazu protokolu.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření pracovního prostoru Analýzy protokolů v Azure Monitoru
> * Vytvoření diagnostického nastavení pro shromažďování protokolů prostředků 
> * Vytvoření jednoduchého dotazu protokolu pro analýzu protokolů


## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu budete potřebovat prostředek Azure ke sledování. V předplatném Azure, které podporuje nastavení diagnostiky, můžete použít libovolný prostředek. Chcete-li zjistit, zda prostředek podporuje nastavení diagnostiky, přejděte do jeho nabídky na webu Azure Portal a ověřte, zda v části **Monitorování** v nabídce je možnost **Nastavení diagnostiky.**


## <a name="log-in-to-azure"></a>Přihlaste se k Azure.
Přihlaste se k [https://portal.azure.com](https://portal.azure.com)portálu Azure na adrese .


## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru
Pracovní prostor Log Analytics ve službě Azure Monitor shromažďuje a indexuje data protokolu z různých zdrojů a umožňuje pokročilou analýzu pomocí výkonného dotazovacího jazyka. Před vytvořením diagnostického nastavení pro odesílání dat do něj musí existovat pracovní prostor Log Analytics. Můžete použít existující pracovní prostor ve vašem předplatném Azure nebo vytvořit jeden s následujícím postupem. 

> [!NOTE]
> Zatímco s daty můžete pracovat v pracovních prostorech Log Analytics v nabídce **Azure Monitor,** vytváříte a spravujete pracovní prostory v nabídce **pracovníprostory Analýzy protokolů.**

1. V **možnostech Všechny služby**vyberte **pracovní prostory Log Analytics**.
2. V horní části obrazovky klikněte na **Přidat** a zadejte následující podrobnosti o pracovním prostoru:
   - **Pracovní prostor Log Analytics**: Název nového pracovního prostoru. Tento název musí být globálně jedinečný ve všech předplatných Azure Monitoru.
   - **Předplatné**: Vyberte předplatné pro uložení pracovního prostoru. To nemusí být stejné předplatné stejné jako prostředek, který je sledován.
   - **Skupina prostředků**: Vyberte existující skupinu prostředků nebo klepněte na **tlačítko Vytvořit nový** a vytvořte novou. Nemusí se jedná o stejnou skupinu prostředků, která je stejná jako u sledovaného prostředku.
   - **Umístění**: Vyberte oblast Azure nebo vytvořte novou. To nemusí být stejné umístění stejné jako prostředek, který je sledován.
   - **Cenová úroveň**: Jako cenovou úroveň vyberte *průběžně splacená.* Tuto cenovou úroveň můžete později změnit. Kliknutím na cenový odkaz **Log Analytics** se dozvíte víc o různých cenových úrovních.

    ![Nový pracovní prostor](media/tutorial-resource-logs/new-workspace.png)

3. Chcete-li vytvořit pracovní prostor, klepněte na tlačítko **OK.**

## <a name="create-a-diagnostic-setting"></a>Vytvoření diagnostického nastavení
[Diagnostická nastavení](../platform/diagnostic-settings.md) definují, kam mají být odesílány protokoly prostředků pro konkrétní prostředek. Jedno diagnostické nastavení může mít více [cílů](../platform/diagnostic-settings.md#destinations), ale v tomto kurzu použijeme pouze pracovní prostor Analýzy protokolů.

1. V části **Monitorování** v nabídce zdroje vyberte **Nastavení diagnostiky**.
2. Měli byste mít zprávu "Žádné diagnostické nastavení definováno". Klepněte na **tlačítko Přidat diagnostické nastavení**.

    ![Nastavení diagnostiky](media/tutorial-resource-logs/diagnostic-settings.png)

3. Každé diagnostické nastavení má tři základní části:
 
   - **Název**: To nemá žádný významný vliv a měl by být jednoduše popisný pro vás.
   - **Cíle**: Jeden nebo více cílů pro odeslání protokolů. Všechny služby Azure sdílejí stejnou sadu tří možných cílů. Každé diagnostické nastavení může definovat jeden nebo více cílů, ale ne více než jeden cíl určitého typu. 
   - **Kategorie**: Kategorie protokolů, které mají být odeslány do každého z cílů. Sada kategorií se bude lišit pro každou službu Azure.

4. Vyberte **Odeslat do pracovního prostoru Log Analytics** a pak vyberte pracovní prostor, který jste vytvořili.
5. Vyberte kategorie, které chcete shromáždit. Definice dostupných kategorií naleznete v dokumentaci ke každé službě.

    ![Diagnostické nastavení](media/tutorial-resource-logs/diagnostic-setting.png)

6. Chcete-li uložit nastavení diagnostiky, klepněte na tlačítko **Uložit.**

    
 
 ## <a name="use-a-log-query-to-retrieve-logs"></a>Načtení protokolů pomocí dotazu protokolu
Data se načítají z pracovního prostoru Analýzy protokolů pomocí dotazu protokolu napsaného v kusto dotazovacím jazyce (KQL). Přehledy a řešení ve službě Azure Monitor budou poskytovat dotazy protokolu k načtení dat pro konkrétní službu, ale můžete pracovat přímo s dotazy protokolu a jejich výsledky na webu Azure Portal s Log Analytics. 

1. V části **Monitorování** v nabídce zdroje vyberte **protokoly**.
2. Log Analytics se otevře s prázdným oknem dotazu s oborem nastaveným na váš prostředek. Všechny dotazy budou obsahovat pouze záznamy z tohoto prostředku.

    > [!NOTE]
    > Pokud jste otevřeli protokoly z nabídky Azure Monitor, obor by se nastavil na pracovní prostor Analýzy protokolů. V takovém případě budou všechny dotazy zahrnovat všechny záznamy v pracovním prostoru.
   
    ![Protokoly](media/tutorial-resource-logs/logs.png)

4. Služba zobrazená v příkladu zapisuje protokoly prostředků do tabulky **AzureDiagnostics,** ale jiné služby mohou zapisovat do jiných tabulek. Viz [Podporované služby, schémata a kategorie pro protokoly prostředků Azure](../platform/diagnostic-logs-schema.md) pro tabulky používané různými službami Azure.

    > [!NOTE]
    > Více služeb zapisovat protokoly prostředků do tabulky AzureDiagnostics. Pokud spustíte Analýzu protokolů z nabídky Azure Monitor, `where` budete `ResourceProvider` muset přidat příkaz se sloupcem k určení vaší konkrétní služby. Při spuštění analýzy protokolů z nabídky prostředku je obor nastaven pouze na záznamy z tohoto prostředku, takže tento sloupec není povinný. Ukázkové dotazy naleznete v dokumentaci ke službě.


5. Zadejte dotaz a kliknutím na **Spustit** zkontrolujte výsledky. 
6. Viz [Začínáme s dotazy protokolu v Azure Monitoru](../log-query/get-started-queries.md) pro kurz o psaní dotazů protokolu.

    ![Protokolovací dotaz](media/tutorial-resource-logs/log-query-1.png)




## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili shromažďovat protokoly prostředků do pracovního prostoru Log Analytics, dokončete kurz na psaní dotazů protokolu k analýze těchto dat.

> [!div class="nextstepaction"]
> [Začínáme s dotazy protokolů v Azure Monitoru](../log-query/get-started-queries.md)
