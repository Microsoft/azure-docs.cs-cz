---
title: Vizualizace datových anomálií v událostech v reálném čase odesílaných do služby Azure Event Hubs | Microsoft Docs
description: Kurz – Vizualizace datových anomálií v událostech v reálném čase odesílaných do služby Microsoft Azure Event Hubs
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.author: shvija
ms.date: 08/08/2018
ms.topic: tutorial
ms.service: event-hubs
ms.custom: mvc
ms.openlocfilehash: 04a9a3b3df44814d680f01595d70ced08a946591
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2018
ms.locfileid: "40004109"
---
# <a name="tutorial-visualize-data-anomalies-in-real-time-events-sent-to-azure-event-hubs"></a>Kurz: Vizualizace datových anomálií v událostech v reálném čase odesílaných do služby Azure Event Hubs

V případě služby Azure Event Hubs můžete pomocí Azure Stream Analytics kontrolovat příchozí data a vytahovat anomálie, které pak můžete vizualizovat v Power BI. Řekněme, že máte tisíce zařízení, která neustále odesílají data v reálném čase do centra událostí a tato data představují až miliony událostí za sekundu. Jak v takovém množství dat kontrolovat anomálie nebo chyby? Co když zařízení odesílají například transakce provedené platebními kartami a vy potřebujete zachytávat případy, kdy během 5 sekund dojde k několika transakcím ve více zemích? K tomu může dojít, když někdo krade platební karty a pak je používá k nákupu věcí po celém světě ve stejnou dobu. 

V tomto kurzu budete tento příklad simulovat. Spustíte aplikaci, která vytváří transakce provedené platebními kartami a odesílá je do centra událostí. Pak budete číst datový proud v reálném čase pomocí služby Azure Stream Analytics, která oddělí platné transakce od neplatných, a pak pomocí Power BI vizuálně identifikujete transakce označené jako neplatné.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vytvoření oboru názvů služby Event Hubs
> * Vytvoření centra událostí
> * Spuštění aplikace, která odesílá transakce provedené platebními kartami
> * Konfigurace úlohy Stream Analytics pro zpracování těchto transakcí
> * Konfigurace vizualizace Power BI pro zobrazení výsledků

K dokončení tohoto kurzu potřebujete předplatné Azure. Pokud ho nemáte, [vytvořte si bezplatný účet][] před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

- Nainstalovat sadu [Visual Studio](https://www.visualstudio.com/). 
- K analýze výstupu z úlohy Stream Analytics potřebujete účet Power BI. [Power BI si můžete vyzkoušet zdarma](https://app.powerbi.com/signupredirect?pbi_source=web).

## <a name="set-up-resources"></a>Příprava prostředků

Pro účely tohoto kurzu potřebujete obor názvů služby Event Hubs a centrum událostí. Tyto prostředky můžete vytvořit pomocí Azure CLI nebo Azure PowerShellu. Použijte pro všechny prostředky stejnou skupinu a umístění. Na konci můžete odebrat vše v jednom kroku odstraněním skupiny prostředků.

V následujících částech najdete podrobnější popis požadovaných kroků. Postupujte podle pokynů pro rozhraní příkazového řádku *nebo* PowerShell a proveďte následující kroky:

1. Vytvořte [skupinu prostředků](../azure-resource-manager/resource-group-overview.md). 

2. Vytvořte obor názvů služby Event Hubs. 

3. Vytvořte centrum událostí.

> [!NOTE]
> V každém skriptu jsou nastavené proměnné, které budete potřebovat v pozdější části kurzu. Patří mezi ně název skupiny prostředků ($resourceGroup), obor názvů centra událostí (**$eventHubNamespace**) a název centra událostí (**$eventHubName**). V pozdějších částech tohoto článku se na tyto hodnoty odkazuje s použitím předpony symbolu dolaru ($), abyste věděli, že se nastavily ve skriptu.

<!-- some day they will approve the tab control; 
  When that happens, put CLI and PSH in tabs. -->

### <a name="set-up-your-resources-using-azure-cli"></a>Nastavení prostředků pomocí Azure CLI

Zkopírujte tento skript a vložte ho do služby Cloud Shell. Za předpokladu, že jste již přihlášeni, se skript provede jeden řádek po druhém.

K proměnným, které musí být globálně jedinečné, je zřetězená hodnota `$RANDOM`. Při spuštění skriptu a nastavení proměnných se vygeneruje náhodný číselný řetězec a zřetězí se ke konci pevného řetězce. Tím se zajistí jeho jedinečnost.

```azurecli-interactive
# Set the values for location and resource group name.
location=westus
resourceGroup=ContosoResourcesEH

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The Event Hubs namespace name must be globally unique, so add a random number to the end.
eventHubNamespace=ContosoEHNamespace$RANDOM
echo "Event Hub Namespace = " $eventHubNamespace

# Create the Event Hubs namespace.
az eventhubs namespace create --resource-group $resourceGroup \
   --name $eventHubNamespace \
   --location $location \
   --sku Standard

# The event hub name must be globally unique, so add a random number to the end.
eventHubName=ContosoEHhub$RANDOM
echo "event hub name = " $eventHubName

# Create the event hub.
az eventhubs eventhub create --resource-group $resourceGroup \
    --namespace-name $eventHubNamespace \
    --name $eventHubName \
    --message-retention 3 \
    --partition-count 2

# Get the connection string that authenticates the app with the Event Hubs service.
connectionString=$(az eventhubs namespace authorization-rule keys list \
   --resource-group $resourceGroup \
   --namespace-name $eventHubNamespace \
   --name RootManageSharedAccessKey \
   --query primaryConnectionString \
   --output tsv)
echo "Connection string = " $connectionString 
```

### <a name="set-up-your-resources-using-azure-powershell"></a>Nastavení prostředků pomocí Azure PowerShellu

Zkopírujte tento skript a vložte ho do služby Cloud Shell. Za předpokladu, že jste již přihlášeni, se skript provede jeden řádek po druhém.

K proměnným, které musí být globálně jedinečné, je zřetězená hodnota `$(Get-Random)`. Při spuštění skriptu a nastavení proměnných se vygeneruje náhodný číselný řetězec a zřetězí se ke konci pevného řetězce. Tím se zajistí jeho jedinečnost.

```azurepowershell-interactive
# Log in to Azure account.
Login-AzureRMAccount

# Set the values for the location and resource group.
$location = "West US"
$resourceGroup = "ContosoResourcesEH"

# Create the resource group to be used  
#   for all resources for this tutorial.
New-AzureRmResourceGroup -Name $resourceGroup -Location $location

# The Event Hubs namespace name must be globally unique, so add a random number to the end.
$eventHubNamespace = "contosoEHNamespace$(Get-Random)"
Write-Host "Event Hub Namespace is " $eventHubNamespace

# The event hub name must be globally unique, so add a random number to the end.
$eventHubName = "contosoEHhub$(Get-Random)"
Write-Host "Event hub Name is " $eventHubName

# Create the Event Hubs namespace.
New-AzureRmEventHubNamespace -ResourceGroupName $resourceGroup `
     -NamespaceName $eventHubNamespace `
     -Location $location

# Create the event hub.
$yourEventHub = New-AzureRmEventHub -ResourceGroupName $resourceGroup `
    -NamespaceName $eventHubNamespace `
    -Name $eventHubName `
    -MessageRetentionInDays 3 `
    -PartitionCount 2

# Get the event hub key, and retrieve the connection string from that object.
# You need this to run the app that sends test messages to the event hub.
$eventHubKey = Get-AzureRmEventHubKey -ResourceGroupName $resourceGroup `
    -Namespace $eventHubNamespace `
    -AuthorizationRuleName RootManageSharedAccessKey

# Save this value somewhere local for later use.
Write-Host "Connection string is " $eventHubKey.PrimaryConnectionString
```

## <a name="run-app-to-produce-test-event-data"></a>Spuštění aplikace a vygenerování testovacích dat událostí

[Ukázky na GitHubu](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet) pro službu Event Hubs zahrnují [aplikaci detektoru anomálií](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/AnomalyDetector), která generuje testovací data za vás. Simuluje používání platebních karet tím, že do centra událostí zapisuje transakce provedené platebními kartami. Zároveň občas pro jednu platební kartu zapíše několik transakcí v několika oblastech, aby se označily jako anomálie. Pokud chcete tuto aplikaci spustit, postupujte následovně: 

1. Z GitHubu si stáhněte [ukázky pro službu Azure Event Hubs](https://github.com/Azure/azure-event-hubs/archive/master.zip) a místně je rozbalte.

2. Přejděte do složky \azure-event-hubs-master\samples\DotNet\AnomalyDetector\ a dvojím kliknutím na soubor AnomalyDetector.sln otevřete řešení v sadě Visual Studio. 

3. Otevřete soubor Program.cs a nahraďte **Event Hubs connection string** připojovacím řetězcem, který jste si uložili při spuštění skriptu. 

4. Nahraďte **Event Hub name** názvem vašeho centra událostí. Stisknutím klávesy F5 spusťte aplikaci. Aplikace začne odesílat události do vašeho centra událostí a pokračuje, dokud neodešle 1 000 událostí. Existuje několik případů, kdy aplikace musí být spuštěná, abyste mohli načíst data. V následujících pokynech jsou tyto případy na příslušných místech uvedené.

## <a name="set-up-azure-stream-analytics"></a>Nastavení služby Azure Stream Analytics

Teď můžete streamovat data do svého centra událostí. Pokud chcete tato data využít ve vizualizaci Power BI, začněte nastavením úlohy Stream Analytics pro příjem dat, která se pak předají do vizualizace Power BI.

### <a name="create-the-stream-analytics-job"></a>Vytvoření úlohy služby Stream Analytics

1. Na portálu Azure Portal klikněte na **Vytvořit prostředek**. Do vyhledávacího pole zadejte **stream analytics** a stiskněte **Enter**. Vyberte **Úloha Stream Analytics**. Na panelu úloh Stream Analytics klikněte na **Vytvořit**. 

2. Zadejte o úloze následující informace:

   **Název úlohy:** Použijte **contosoEHjob**. Toto pole obsahuje název úlohy, který musí být globálně jedinečný.

   **Předplatné:** Vyberte vaše předplatné.

   **Skupina prostředků:** Použijte stejnou skupinu prostředků jako pro centrum událostí (**ContosoResourcesEH**).

   **Umístění:** Použijte stejné umístění, které používáte v instalačním skriptu (**USA – západ**).

   ![Snímek obrazovky předvádějící vytvoření nové úlohy Azure Stream Analytics.](./media/event-hubs-tutorial-visualize-anomalies/stream-analytics-add-job.png)

    Pro zbývající pole můžete použít výchozí hodnoty. Klikněte na možnost **Vytvořit**. 

### <a name="add-an-input-to-the-stream-analytics-job"></a>Přidání vstupu úlohy Stream Analytics

Pokud na portálu nejste v podokně **Úloha Stream Analytics**, můžete se ke své úloze Stream Analytics vrátit kliknutím na **Skupiny prostředků** a výběrem vaší skupiny prostředků (**ContosoResourcesEH**). Tato akce zobrazí všechny prostředky ve skupině a vy můžete vybrat svou úlohu Stream Analytics. 

Vstupy pro úlohu Stream Analytics jsou transakce provedené platebními kartami z centra událostí.

> [!NOTE]
> Hodnoty pro proměnné začínající na symbol dolaru ($) se nastavují ve spouštěcích skriptech v předchozích částech. Tady musíte při zadávání hodnot těchto polí, tedy oboru názvů služby Event Hubs a názvu centra událostí, použít stejné hodnoty.

1. V části **Topologie úlohy** klikněte na **Vstupy**.

2. V podokně **Vstupy** klikněte na **Přidat vstup streamu** a vyberte Event Hubs. Na obrazovce, která se zobrazí, vyplňte následující pole:

   **Alias pro vstup:** Použijte **contosoinputs**. Toto pole obsahuje název vstupního datového proudu, který se používá při definování dotazu na data.

   **Předplatné:** Vyberte vaše předplatné.

   **Obor názvů služby Event Hubs:** Vyberte obor názvů vašeho centra událostí ($**eventHubNamespace**). 

   **Název centra událostí:** Klikněte na **Použít existující** a vyberte vaše centrum událostí ($**eventHubName**).

   **Název zásady služby Event Hubs:** Vyberte **RootManageSharedAccessKey**.

   **Skupina příjemců služby Event Hubs:** Toto pole ponechte prázdné, aby se použila výchozí skupina příjemců.

   Pro zbývající pole můžete použít výchozí hodnoty.

   ![Snímek obrazovky předvádějící přidání vstupního datového proudu do úlohy Stream Analytics.](./media/event-hubs-tutorial-visualize-anomalies/stream-analytics-inputs.png)

5. Klikněte na **Uložit**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Přidání vstupu úlohy Stream Analytics

1. V části **Topologie úlohy** klikněte na **Výstupy**. Toto pole obsahuje název výstupního datového proudu, který se používá při definování dotazu na data.

2. V podokně **Výstupy** klikněte na **Přidat** a potom vyberte **Power BI**. Na obrazovce, která se zobrazí, vyplňte následující pole:

   **Alias pro výstup:** Použijte **contosooutputs**. Toto pole obsahuje jedinečný alias pro výstup. 

   **Název datové sady:** Použijte **contosoehdataset**. Toto pole obsahuje název datové sady, kterou chcete v Power BI použít. 

   **Název tabulky:** Použijte **contosoehtable**. Toto pole obsahuje název tabulky, kterou chcete v Power BI použít. 

   Pro zbývající pole můžete použít výchozí hodnoty.

   ![Snímek obrazovky předvádějící nastavení výstupu úlohy Stream Analytics.](./media/event-hubs-tutorial-visualize-anomalies/stream-analytics-outputs.png)

3. Klikněte na **Autorizovat** a přihlaste se ke svému účtu Power BI.

4. Pro zbývající pole můžete použít výchozí hodnoty.

5. Klikněte na **Uložit**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurace dotazu pro úlohu Stream Analytics

Tento dotaz slouží k načtení dat, která se nakonec odešlou do vizualizace Power BI. Používá vstup **contosoinputs** a výstup **contosooutputs**, které jste definovali dříve při nastavování úlohy. Tento dotaz načte transakce provedené platebními kartami, které považuje za podvodné, tedy transakce, ve kterých se stejné číslo platební karty použilo během pěti sekund k několika transakcím v různých oblastech.

1. V části **Topologie úlohy** klikněte na **Dotaz**.

2. Nahraďte dotaz následujícím dotazem: 

   ```SQL
   /* criteria for fraud:
      credit card purchases with the same card
      in different locations within 5 seconds
   */
   SELECT System.Timestamp AS WindowEnd, 
     COUNT(*) as FraudulentUses      
   INTO contosooutputs
   FROM contosoinputs CS1 TIMESTAMP BY [Timestamp]
       JOIN contosoinputs CS2 TIMESTAMP BY [Timestamp]
       /* where the credit card # is the same */
       ON CS1.CreditCardId = CS2.CreditCardId
       /* and time between the two is between 0 and 5 seconds */
       AND DATEDIFF(second, CS1, CS2) BETWEEN 0 AND 5
       /* where the location is different */
   WHERE CS1.Location != CS2.Location
   GROUP BY TumblingWindow(Duration(second, 1))
   ```

4. Klikněte na **Uložit**.

### <a name="test-the-query-for-the-stream-analytics-job"></a>Test dotazu pro úlohu Stream Analytics 

1. Spusťte aplikaci detektoru anomálií, která bude do centra událostí odesílat data, zatímco nastavujete a spouštíte test. 

2. V podokně Dotaz klikněte na tečky vedle vstupu **contosoinputs** a pak vyberte **Ukázková data ze vstupu**.

3. Zadejte, že chcete data za tři minuty, a klikněte na **OK**. Počkejte, dokud se nezobrazí oznámení o dokončeném vzorkování dat.

4. Klikněte na **Test** a ujistěte se, že získáváte výsledky. Výsledky se zobrazí v části **Výsledky** v dolním podokně napravo pod dotazem.

5. Zavřete podokno dotazu.

### <a name="run-the-stream-analytics-job"></a>Spuštění úlohy Stream Analytics

V úloze Stream Analytics klikněte na **Spustit**, pak na **Nyní** a pak na **Spustit**. Jakmile se úloha úspěšně spustí, stav úlohy se změní ze **Zastaveno** na **Spuštěno**.

## <a name="set-up-the-power-bi-visualizations"></a>Nastavení vizualizací Power BI

1. Spusťte aplikaci detektoru anomálií, která bude do centra událostí odesílat data, zatímco nastavujete vizualizaci Power BI. Možná ji budete muset spustit několikrát, protože při každém spuštění vygeneruje pouze 1 000 transakcí.

2. Přihlaste se ke svému účtu [Power BI](https://powerbi.microsoft.com/).

3. Přejděte do části **Můj pracovní prostor**.

4. Klikněte na **Datové sady**.

   Měla by se zobrazit datová sada, kterou jste určili při vytváření výstupu pro úlohu Stream Analytics (**contosoehdataset**). Zobrazení datové sady může na začátku 5 až 10 minut trvat.

5. Klikněte na **Řídicí panely**, pak na **Vytvořit** a vyberte **Řídicí panel**.

   ![Snímek obrazovky s tlačítky Řídicí panely a Vytvořit.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-add-dashboard.png)

6. Zadejte název řídicího panelu a pak klikněte na **Vytvořit**. Použijte název **Anomálie platebních karet**.

   ![Snímek obrazovky se zadáváním názvu řídicího panelu.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-dashboard-name.png)

7. Na stránce řídicího panelu klikněte na **Přidat dlaždici**, v části **DATA V REÁLNÉM ČASE** vyberte **Vlastní streamovaná data** a pak klikněte na **Další**.

   ![Snímek obrazovky se zadáváním zdroje pro dlaždici.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-add-card-real-time-data.png)

8. Vyberte vaši datovou sadu (**contosoehdataset**) a klikněte na **Další**.

   ![Snímek obrazovky se zadáváním datové sady.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-dashboard-select-dataset.png)

9. Jako typ vizualizace vyberte **Karta**. V části **Pole** klikněte na **Přidat hodnotu** a vyberte **fraudulentuses**.

   ![Snímek obrazovky se zadáváním typu vizualizace a polí.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-add-card-tile.png)

   Klikněte na **Další**.

10. Nastavte nadpis na **Podvodná použití** a podnadpis na **Součet za posledních několik minut**. Klikněte na tlačítko **Použít**. Dlaždice se uloží na váš řídicí panel.

    ![Snímek obrazovky se zadáváním nadpisu a podnadpisu pro dlaždici řídicího panelu.](./media/event-hubs-tutorial-visualize-anomalies/power-bi-tile-details.png)

11. Přidejte další vizualizaci. Zopakujte prvních několik kroků:

   * Klikněte na **Přidat dlaždici**.
   * Vyberte **Vlastní streamovaná data**. 
   * Klikněte na **Další**.
   * Vyberte vaši datovou sadu a klikněte na **Další**. 

12. V části **Typ vizualizace** vyberte **Spojnicový graf**.

13. V části **Osy** klikněte na **Přidat hodnotu** a vyberte **windowend**. 

14. V části **Hodnoty** klikněte na **Přidat hodnotu** a vyberte **fraudulentuses**.

15. V části **Časové okno k zobrazení** vyberte posledních pět minut. Klikněte na **Další**.

16. Jako nadpis zadejte **Zobrazení podvodných použití v průběhu času**, podnadpis dlaždice ponechte prázdný a klikněte na **Použít**. Vrátíte se na váš řídicí panel.

17. Znovu spusťte aplikaci detektoru anomálií, která do centra událostí odešle nějaká data. Uvidíte, jak se dlaždice **Podvodná použití** mění s tím, jak data analyzuje, a data se zobrazí ve spojnicovém grafu. 

    ![Snímek obrazovky s výsledky v Power BI](./media/event-hubs-tutorial-visualize-anomalies/power-bi-results.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odebrat všechny prostředky, které jste vytvořili, odeberte data vizualizací Power BI a pak odstraňte skupinu prostředků. Odstraněním skupiny prostředků se odstraní všechny prostředky, které tato skupina obsahuje. V tomto případě se odebere centrum událostí, obor názvů služby Event Hubs, úloha Stream Analytics a samotná skupina prostředků. 

### <a name="clean-up-resources-in-the-power-bi-visualization"></a>Vyčištění prostředků ve vizualizaci Power BI

Přihlaste se ke svému účtu Power BI. Přejděte do části **Můj pracovní prostor**. Na řádku s názvem vašeho řídicího panelu klikněte na ikonu koše. Pak přejděte do části **Datové sady** a kliknutím na ikonu koše odstraňte příslušnou datovou sadu (**contosoehdataset**).

### <a name="clean-up-resources-using-azure-cli"></a>Vyčištění prostředků pomocí Azure CLI

Chcete-li odebrat skupinu prostředků, použijte příkaz [az group delete](/cli/azure/group?view=azure-cli-latest#az-group-delete).

```azurecli-interactive
az group delete --name $resourceGroup
```

### <a name="clean-up-resources-using-powershell"></a>Vyčištění prostředků pomocí PowerShell

K odebrání skupiny prostředků použijte příkaz [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:
> [!div class="checklist"]
> * Vytvoření oboru názvů služby Event Hubs
> * Vytvoření centra událostí
> * Spuštění aplikace, která simuluje události a odesílá je do centra událostí
> * Konfigurace úlohy Stream Analytics pro zpracování událostí odeslaných do centra
> * Konfigurace vizualizace Power BI pro zobrazení výsledků

Přejděte k dalšímu článku, kde najdete další informace o službě Azure Event Hubs.

> [!div class="nextstepaction"]
> [Začínáme s odesíláním zpráv do služby Azure Event Hubs v .NET Standard](event-hubs-dotnet-standard-getstarted-send.md)

[vytvořte si bezplatný účet]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
