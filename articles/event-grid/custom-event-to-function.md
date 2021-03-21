---
title: 'Rychlý Start: odeslání vlastních událostí do funkce Azure Function-Event Grid'
description: 'Rychlý Start: pomocí Azure Event Grid a Azure CLI nebo portálu můžete publikovat téma a přihlásit se k odběru této události. Pro koncový bod se používá funkce Azure Functions.'
ms.date: 07/07/2020
ms.topic: quickstart
ms.openlocfilehash: 4fe4753de41443a0537636933364c7b69b25cb27
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791723"
---
# <a name="quickstart-route-custom-events-to-an-azure-function-with-event-grid"></a>Rychlý Start: směrování vlastních událostí do funkce Azure pomocí Event Grid

Azure Event Grid je služba zpracování událostí pro cloud. Azure Functions je jednou z podporovaných obslužných rutin událostí. V tomto článku vytvoříte pomocí webu Azure Portal vlastní téma, přihlásíte se k jeho odběru a aktivujete událost, abyste viděli výsledek. Události odešlete do funkce Azure Functions.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-azure-function"></a>Vytvoření funkce Azure
Před přihlášením k odběru vlastního tématu vytvořte funkci pro zpracování událostí. 

1. Vytvořte aplikaci funkcí pomocí pokynů v tématu [Vytvoření aplikace Function App](../azure-functions/functions-get-started.md).
2. Vytvořte funkci pomocí **triggeru Event Grid**. Pokud tuto aktivační událost použijete poprvé, můžete tuto možnost vybrat kliknutím na nainstalovat a instalaci rozšíření nainstalovat.
    1. Na stránce **Function App** v nabídce vlevo vyberte **funkce** , vyhledejte **Event Grid** v části šablony a potom vyberte **Azure Event Grid Trigger**. 

        :::image type="content" source="./media/custom-event-to-function/function-event-grid-trigger.png" alt-text="Vybrat aktivační událost Event Grid":::
3. Na stránce **Nová funkce** zadejte název funkce a vyberte **vytvořit funkci**.

    :::image type="content" source="./media/custom-event-to-function/new-function-page.png" alt-text="Nová stránka funkce":::
4. Pomocí stránky **Code + test** zobrazte existující kód funkce a aktualizujte ji. 

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="create-a-custom-topic"></a>Vytvoření vlastního tématu

Téma Event Gridu poskytuje uživatelsky definovaný koncový bod, do kterého odesíláte události. 

1. Přihlaste se k [Azure Portal](https://portal.azure.com/).
2. V levém navigačním panelu vyberte **všechny služby** , vyhledejte **Event Grid** a vyberte **Event Grid témata**. 

    ![Vybrat Event Grid témata](./media/custom-event-to-function/select-event-grid-topics.png)
3. Na stránce **Event Grid témata** vyberte **+ Přidat** na panelu nástrojů. 

    ![Přidat Event Grid – tlačítko tématu](./media/custom-event-to-function/add-event-grid-topic-button.png)

4. Na stránce **vytvořit téma** postupujte podle následujících kroků:

    1. Zadejte jedinečný **název** vlastního tématu. Název tématu musí být jedinečný, protože je reprezentován položkou DNS. Nepoužívejte název zobrazený na obrázku. Místo toho vytvořte vlastní název – musí mít 3-50 znaků a obsahovat pouze hodnoty a-z, A-Z, 0-9 a "-".
    2. Vyberte své **předplatné** Azure.
    3. Vyberte stejnou skupinu prostředků z předchozích kroků.
    4. Vyberte **umístění** tématu Event Grid.
    5. Pro pole **schéma události** ponechte výchozí hodnotu **Event Grid Schema** . 

       ![Vytvořit stránku tématu](./media/custom-event-to-function/create-custom-topic.png)
    6. Vyberte **Vytvořit**. 

5. Po vytvoření vlastního tématu se zobrazí oznámení o úspěchu. Vyberte **Přejít do skupiny prostředků**. 

   ![Zobrazení oznámení o úspěchu](./media/custom-event-to-function/success-notification.png)

6. Na stránce **Skupina prostředků** vyberte téma Event Grid. 

   ![Výběr prostředku tématu Event gridu](./media/custom-event-to-function/select-event-grid-topic.png)

7. Pro událost Event Grid se zobrazí stránka **Event Grid téma** . Tuto stránku nechte otevřenou. Použijete ji později v rychlém startu. 

    ![Domovská stránka Event Gridho tématu](./media/custom-event-to-function/event-grid-topic-home-page.png)

## <a name="subscribe-to-custom-topic"></a>Přihlášení k odběru vlastního tématu

K odběru tématu Event Gridu se přihlašujete, aby služba Event Grid věděla, které události chcete sledovat a kam má tyto události odesílat.

1. Nyní na stránce **Event Grid téma** pro vlastní téma vyberte **+ odběr události** na panelu nástrojů.

   ![Přidání odběru události](./media/custom-event-to-function/new-event-subscription.png)

2. Na stránce **vytvořit odběr události** použijte následující postup:
    1. Zadejte **název** odběru události.
    3. Jako **Typ koncového bodu** vyberte **Azure Function** . 
    4. Zvolte **Vybrat koncový bod**. 

       ![Zadání hodnot pro odběr události](./media/custom-event-to-function/provide-subscription-values.png)

    5. V případě koncového bodu funkce vyberte předplatné Azure a skupinu prostředků, ve které je Function App, a pak vyberte Function App a funkci, kterou jste vytvořili dříve. Zvolte **Potvrdit výběr**.

       ![Zadání adresy URL koncového bodu](./media/custom-event-to-function/provide-endpoint.png)
    6. Tento krok je nepovinný, ale doporučuje se pro produkční scénáře. Na stránce **vytvořit odběr události** přepněte na kartu **Pokročilé funkce** a nastavte hodnoty **maximálního počtu událostí na dávku** a **upřednostňovanou velikost dávky v kilobajtech**. 
    
        Dávkování vám může poskytnout vysokou propustnost. Pro **Maximum událostí na jednu dávku** nastavte maximální počet událostí, které bude předplatné zahrnovat do dávky. Upřednostňovaná velikost dávky nastaví upřednostňovanou horní mez velikosti dávky v kilobajtech, ale může být překročena, pokud je jediná událost větší než tato prahová hodnota.
    
        :::image type="content" source="./media/custom-event-to-function/enable-batching.png" alt-text="Povolit dávkování":::
    6. Na stránce **vytvořit odběr události** vyberte **vytvořit**.

## <a name="send-an-event-to-your-topic"></a>Odeslání události do tématu

Nyní aktivujeme událost, abychom viděli, jak služba Event Grid distribuuje zprávu do vašeho koncového bodu. K odeslání testovací události do vlastního tématu použijte Azure CLI nebo PowerShell. Obvykle by aplikace nebo služba Azure odesílala data události.

V prvním příkladu se používá Azure CLI. Načte adresu URL a klíč vlastního tématu a data ukázkové události. Místo položky `<topic name>` použijte název vlastního tématu. Vytvoří se ukázková data události. Element JSON `data` je datová část vaší události. V tomto poli může být libovolný JSON ve správném formátu. Můžete také použít pole subject (předmět) pro pokročilé směrování a filtrování. CURL je nástroj, který odesílá požadavky HTTP.


### <a name="azure-cli"></a>Azure CLI
1. V Azure Portal vyberte **Cloud Shell**. V levém horním rohu okna Cloud Shell vyberte **bash** . 

    ![Cloud Shell – bash](./media/custom-event-quickstart-portal/cloud-shell-bash.png)
1. Spusťte následující příkaz, který získá **koncový bod** pro téma: po zkopírování a vložení příkazu aktualizujte **název tématu** a **název skupiny prostředků** před spuštěním příkazu. 

    ```azurecli
    endpoint=$(az eventgrid topic show --name <topic name> -g <resource group name> --query "endpoint" --output tsv)
    ```
2. Spusťte následující příkaz, který získá **klíč** pro vlastní téma: po zkopírování a vložení příkazu aktualizujte **název tématu** a název **skupiny prostředků** před spuštěním příkazu. 

    ```azurecli
    key=$(az eventgrid topic key list --name <topic name> -g <resource group name> --query "key1" --output tsv)
    ```
3. Zkopírujte následující příkaz s definicí události a stiskněte klávesu **ENTER**. 

    ```json
    event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
    ```
4. Spusťte následující příkaz **kudrlinkou** pro odeslání události:

    ```
    curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
    ```

### <a name="azure-powershell"></a>Azure PowerShell
V druhém příkladu se k provedení podobných kroků používá PowerShell.

1. V Azure Portal vyberte možnost **Cloud Shell** (nebo přejít na `https://shell.azure.com/` ). V levém horním rohu okna Cloud Shell vyberte **PowerShell** . Podívejte se na ukázku obrázku okna **Cloud Shell** v části Azure CLI.
2. Nastavte následující proměnné. Po zkopírování a vložení každého příkazu aktualizujte **název tématu** a **název skupiny prostředků** před spuštěním příkazu:

    ```powershell
    $resourceGroupName = <resource group name>
    $topicName = <topic name>
    ```
3. Spusťte následující příkazy, abyste získali **koncový bod** a **klíče** pro téma:

    ```powershell
    $endpoint = (Get-AzEventGridTopic -ResourceGroupName $resourceGroupName -Name $topicName).Endpoint
    $keys = Get-AzEventGridTopicKey -ResourceGroupName $resourceGroupName -Name $topicName
    ```
4. Připraví událost. Zkopírujte a spusťte příkazy v okně Cloud Shell. 

    ```powershell
    $eventID = Get-Random 99999

    #Date format should be SortableDateTimePattern (ISO 8601)
    $eventDate = Get-Date -Format s

    #Construct body using Hashtable
    $htbody = @{
        id= $eventID
        eventType="recordInserted"
        subject="myapp/vehicles/motorcycles"
        eventTime= $eventDate   
        data= @{
            make="Ducati"
            model="Monster"
        }
        dataVersion="1.0"
    }
    
    #Use ConvertTo-Json to convert event body from Hashtable to JSON Object
    #Append square brackets to the converted JSON payload since they are expected in the event's JSON payload syntax
    $body = "["+(ConvertTo-Json $htbody)+"]"
    ```
5. K odeslání události použijte rutinu **Invoke-WebRequest** . 

    ```powershell
    Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
    ```

### <a name="verify-in-the-event-grid-viewer"></a>Ověření v prohlížeči Event Grid
Právě jste aktivovali událost a služba Event Grid odeslala zprávu do koncového bodu, který jste nakonfigurovali při přihlášení k odběru. Přejděte do funkce aktivované Event Grid a otevřete protokoly. V protokolech by se měla zobrazit kopie datové části události. Pokud si nejste jistí, že jste nejdřív otevřeli okno protokoly, nebo se znovu připojte a zkuste znovu odeslat testovací událost.

![Úspěšný protokol triggeru funkce](./media/custom-event-to-function/successful-function.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud chcete pokračovat v práci s touto událostí, nevyčišťujte prostředky vytvořené v rámci tohoto článku. V opačném případě odstraňte prostředky, které jste v rámci tohoto článku vytvořili.

1. V nabídce vlevo vyberte **skupiny prostředků** . Pokud ho v nabídce vlevo nevidíte, v nabídce vlevo vyberte **všechny služby** a vyberte **skupiny prostředků**. 
2. Vyberte skupinu prostředků, na které se má spustit stránka **skupiny prostředků** . 
3. Na panelu nástrojů vyberte **Odstranit skupinu prostředků** . 
4. Potvrďte odstranění zadáním názvu skupiny prostředků a vyberte **Odstranit**. 

    ![Skupiny prostředků](./media/custom-event-to-function/delete-resource-groups.png)

    Další skupina prostředků, kterou vidíte v imagi, byla vytvořena a používána oknem Cloud Shell. Pokud neplánujete použít okno Cloud Shell později, odstraňte ho. 

## <a name="next-steps"></a>Další kroky

Když teď víte, jak vytvářet témata a odběry událostí, zjistěte, s čím vám služba Event Grid ještě může pomoct:

- [Informace o službě Event Grid](overview.md)
- [Směrování událostí služby Blob Storage do vlastního webového koncového bodu](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Monitorování změn virtuálního počítače pomocí služeb Azure Event Grid a Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Streamování velkých objemů dat do datového skladu](event-grid-event-hubs-integration.md)
