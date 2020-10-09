---
title: Shromažďovat protokoly prostředků z prostředku Azure a analyzovat je pomocí Azure Monitor
description: Kurz konfigurace nastavení diagnostiky pro shromažďování protokolů prostředků z prostředku Azure do Log Analyticsho pracovního prostoru, kde je lze analyzovat pomocí dotazu protokolu.
ms.subservice: ''
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 12/15/2019
ms.openlocfilehash: b68d4779d33d2f8a1f1c2bae35f438ad2ccccabb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90090070"
---
# <a name="tutorial-collect-and-analyze-resource-logs-from-an-azure-resource"></a>Kurz: shromáždění a analýza protokolů prostředků z prostředku Azure

Protokoly prostředků poskytují přehled o podrobnostech o provozu prostředku Azure a jsou užitečné pro monitorování jejich stavu a dostupnosti. Prostředky Azure generují protokoly prostředků automaticky, ale musíte nakonfigurovat, kde se mají shromažďovat. Tento kurz vás provede procesem vytvoření nastavení diagnostiky pro shromažďování protokolů prostředků pro prostředek v rámci vašeho předplatného Azure a jeho analýze pomocí dotazu protokolu.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření pracovního prostoru Log Analytics v Azure Monitor
> * Vytvoření nastavení diagnostiky pro shromáždění protokolů prostředků 
> * Vytvoření jednoduchého dotazu protokolu pro analýzu protokolů


## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu potřebujete prostředek Azure, který se má monitorovat. Můžete použít libovolný prostředek v předplatném Azure, který podporuje nastavení diagnostiky. Chcete-li zjistit, zda prostředek podporuje nastavení diagnostiky, přejděte do příslušné nabídky v Azure Portal a ověřte, zda je v části **monitorování** v nabídce možnost **nastavení diagnostiky** .


## <a name="log-in-to-azure"></a>Přihlaste se k Azure.
Přihlaste se k Azure Portal v [https://portal.azure.com](https://portal.azure.com) .


## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru
Pracovní prostor Log Analytics v Azure Monitor shromažďuje a indexuje data protokolů z nejrůznějších zdrojů a umožňuje pokročilou analýzu pomocí výkonného dotazovacího jazyka. Před vytvořením nastavení diagnostiky pro odeslání dat do tohoto pracovního prostoru Log Analytics musí existovat. V předplatném Azure můžete použít existující pracovní prostor nebo ho vytvořit pomocí následujícího postupu. 

> [!NOTE]
> I když můžete pracovat s daty v Log Analytics pracovních prostorech v nabídce **Azure monitor** , můžete vytvářet a spravovat pracovní prostory v nabídce **Log Analytics pracovní prostory** .

1. Ze **všech služeb**vyberte **Log Analytics pracovní prostory**.
2. V horní části obrazovky klikněte na **Přidat** a pro pracovní prostor zadejte následující podrobnosti:
   - **Log Analytics pracovní prostor**: název nového pracovního prostoru. Tento název musí být globálně jedinečný v rámci všech předplatných Azure Monitor.
   - **Předplatné**: vyberte předplatné, do kterého chcete uložit pracovní prostor. Nemusí se jednat o stejné předplatné jako monitorovaný prostředek.
   - **Skupina prostředků**: Vyberte existující skupinu prostředků nebo vytvořte novou kliknutím na **vytvořit novou** . Nemusí se jednat o stejnou skupinu prostředků, která je stejná jako monitorovaný prostředek.
   - **Umístění**: Vyberte oblast Azure nebo vytvořte novou. To nemusí být stejné jako stejné umístění jako monitorovaný prostředek.
   - **Cenová úroveň**: jako cenová úroveň vyberte průběžné *platby* . Tuto cenovou úroveň můžete později změnit. Kliknutím na odkaz **Log Analytics ceny** získáte další informace o různých cenových úrovních.

    ![Nový pracovní prostor](media/tutorial-resource-logs/new-workspace.png)

3. Kliknutím na tlačítko **OK** vytvořte pracovní prostor.

## <a name="create-a-diagnostic-setting"></a>Vytvoření nastavení diagnostiky
[Nastavení diagnostiky](../platform/diagnostic-settings.md) definuje, kde se mají odesílat protokoly prostředků pro konkrétní prostředek. Jedno diagnostické nastavení může mít několik [cílů](../platform/diagnostic-settings.md#destinations), ale v tomto kurzu budeme používat jenom Log Analytics pracovní prostor.

1. V části **monitorování** v nabídce prostředku vyberte **nastavení diagnostiky**.
2. Měli byste mít zprávu "není definováno žádné nastavení diagnostiky". Klikněte na **Přidat nastavení diagnostiky**.

    ![Nastavení diagnostiky](media/tutorial-resource-logs/diagnostic-settings.png)

3. Každé nastavení diagnostiky má tři základní části:
 
   - **Název**: nemá žádný významný vliv a měl by být pro vás jednoduchý.
   - **Cílová umístění**: jedno nebo více cílů pro odeslání protokolů. Všechny služby Azure sdílejí stejnou sadu tří možných cílů. Každé nastavení diagnostiky může definovat jedno nebo více cílů, ale ne více než jeden cíl určitého typu. 
   - **Kategorie**: Kategorie protokolů, které se mají odeslat do každého cíle. Sada kategorií se bude pro každou službu Azure lišit.

4. Vyberte **Odeslat do Log Analytics pracovní prostor** a potom vyberte pracovní prostor, který jste vytvořili.
5. Vyberte kategorie, které chcete shromáždit. Definici dostupných kategorií najdete v dokumentaci ke každé službě.

    ![Nastavení diagnostiky](media/tutorial-resource-logs/diagnostic-setting.png)

6. Kliknutím na **Uložit** uložte nastavení diagnostiky.

    
 
 ## <a name="use-a-log-query-to-retrieve-logs"></a>Použití dotazu protokolu k načtení protokolů
Data se načítají z Log Analyticsho pracovního prostoru pomocí dotazu protokolu napsaného v KQL (Kusto Query Language). Přehledy a řešení v Azure Monitor poskytnou dotazy protokolu pro načtení dat pro určitou službu, ale můžete pracovat přímo s dotazy protokolu a jejich výsledky v Azure Portal s Log Analytics. 

1. V části **monitorování** v nabídce prostředku vyberte **protokoly**.
2. Log Analytics se otevře s prázdným oknem dotazu s oborem nastaveným na váš prostředek. Všechny dotazy budou obsahovat pouze záznamy z daného prostředku.

    > [!NOTE]
    > Pokud jste protokoly otevřeli z nabídky Azure Monitor, obor se nastaví na pracovní prostor Log Analytics. V takovém případě budou všechny dotazy zahrnovat všechny záznamy v pracovním prostoru.
   
    ![Snímek obrazovky se zobrazí v protokolech pro aplikaci logiky, ve které se zvýrazní nový dotaz s názvem aplikace logiky.](media/tutorial-resource-logs/logs.png)

4. Služba uvedená v příkladu zapisuje protokoly prostředků do tabulky **AzureDiagnostics** , ale další služby mohou zapisovat do jiných tabulek. Seznamte se s [podporovanými službami, schématy a kategoriemi pro protokoly prostředků Azure](../platform/resource-logs-schema.md) pro tabulky používané různými službami Azure.

    > [!NOTE]
    > Několik služeb zapisuje do tabulky AzureDiagnostics protokoly prostředků. Pokud spustíte Log Analytics v nabídce Azure Monitor, budete muset přidat `where` příkaz se `ResourceProvider` sloupcem pro určení konkrétní služby. Když začnete Log Analytics z nabídky prostředku, pak je obor nastavený na pouze záznamy z tohoto prostředku, aby tento sloupec nebyl povinný. Ukázkové dotazy najdete v dokumentaci ke službě.


5. Zadejte dotaz a kliknutím na tlačítko **Spustit** prozkoumejte výsledky. 
6. Kurz týkající se psaní dotazů protokolu najdete v tématu Začínáme [s dotazy protokolu v Azure monitor](../log-query/get-started-queries.md) .

    ![Dotaz protokolu](media/tutorial-resource-logs/log-query-1.png)




## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili, jak shromažďovat protokoly prostředků do Log Analyticsho pracovního prostoru, dokončete kurz týkající se zápisu dotazů protokolu pro analýzu těchto dat.

> [!div class="nextstepaction"]
> [Začínáme s dotazy protokolů v Azure Monitoru](../log-query/get-started-queries.md)
