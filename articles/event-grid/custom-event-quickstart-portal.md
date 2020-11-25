---
title: 'Rychlý Start: odesílání vlastních událostí do webového koncového bodu – Event Grid Azure Portal'
description: 'Rychlý Start: použijte Azure Event Grid a Azure Portal k publikování vlastního tématu a přihlášení k odběru událostí pro toto téma. Události jsou zpracovávány webovou aplikací.'
ms.date: 07/07/2020
ms.topic: quickstart
ms.openlocfilehash: 592e2d6b7393da8cb55a457b022d6c2358048cfe
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013662"
---
# <a name="quickstart-route-custom-events-to-web-endpoint-with-the-azure-portal-and-event-grid"></a>Rychlý Start: směrování vlastních událostí na webový koncový bod pomocí Azure Portal a Event Grid

Azure Event Grid je služba zpracování událostí pro cloud. V tomto článku vytvoříte pomocí webu Azure Portal vlastní téma, přihlásíte se k jeho odběru a aktivujete událost, abyste viděli výsledek. Obvykle odesíláte události do koncového bodu, který data události zpracuje a provede akce. Pro zjednodušení tohoto článku však budete události odesílat do webové aplikace, která shromažďuje a zobrazuje zprávy.

## <a name="prerequisites"></a>Požadavky
[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="create-a-custom-topic"></a>Vytvoření vlastního tématu

Téma Event Gridu poskytuje uživatelsky definovaný koncový bod, do kterého odesíláte události. 

1. Přihlaste se k [Azure Portal](https://portal.azure.com/).
2. Do panelu hledání v tématu zadejte **Event Grid témata** a potom v rozevíracím seznamu vyberte **Event Grid témata** . 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-event-grid-topics.png" alt-text="Vyhledat a vybrat Event Grid témata":::
3. Na stránce **Event Grid témata** vyberte **+ Přidat** na panelu nástrojů. 

    :::image type="content" source="./media/custom-event-quickstart-portal/add-event-grid-topic-button.png" alt-text="Přidat Event Grid – tlačítko tématu":::
4. Na stránce **vytvořit téma** postupujte podle následujících kroků:
    1. Vyberte své **předplatné** Azure.
    2. Vyberte existující skupinu prostředků nebo vyberte **vytvořit novou** a zadejte **název** **skupiny prostředků**.
    3. Zadejte jedinečný **název** vlastního tématu. Název tématu musí být jedinečný, protože je reprezentován položkou DNS. Nepoužívejte název zobrazený na obrázku. Místo toho vytvořte vlastní název – musí mít 3-50 znaků a obsahovat pouze hodnoty a-z, A-Z, 0-9 a "-".
    4. Vyberte **umístění** tématu Event Grid.
    5. V dolní části stránky vyberte **zkontrolovat + vytvořit** . 

        :::image type="content" source="./media/custom-event-quickstart-portal/create-custom-topic.png" alt-text="Vytvořit stránku tématu":::
    6. Na stránce **Revize + vytvořit** tématu na stránce **vytvořit téma** vyberte **vytvořit**. 
    
        :::image type="content" source="./media/custom-event-quickstart-portal/review-create-page.png" alt-text="Kontrola nastavení a vytvoření":::
5. Po úspěšném nasazení zadejte **Event Grid témata** na panelu hledání a v rozevíracím seznamu vyberte **Event Grid témata** , jako jste to předtím. 
6. Ze seznamu vyberte téma, které jste vytvořili. 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-event-grid-topic.png" alt-text="Vyberte své téma ze seznamu.":::

7. Zobrazí se stránka **Event Grid téma** pro vaše téma. Tuto stránku nechte otevřenou. Použijete ji později v rychlém startu. 

    :::image type="content" source="./media/custom-event-quickstart-portal/event-grid-topic-home-page.png" alt-text="Domovská stránka Event Gridho tématu":::

## <a name="create-a-message-endpoint"></a>Vytvoření koncového bodu zpráv
Před vytvořením předplatného pro vlastní téma vytvořte koncový bod pro zprávu události. Koncový bod obvykle provede akce na základě dat události. Pro zjednodušení tohoto rychlého startu nasadíte [předem připravenou webovou aplikaci](https://github.com/Azure-Samples/azure-event-grid-viewer), která zobrazuje zprávy události. Nasazené řešení zahrnuje plán služby App Service, webovou aplikaci App Service a zdrojový kód z GitHubu.

1. Na stránce článek vyberte **nasadit do Azure** a nasaďte řešení do svého předplatného. Na webu Azure Portal zadejte hodnoty pro parametry.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"  alt="Button to Deploy to Aquent." /></a>
1. Dokončení nasazení může trvat několik minut. Po úspěšném nasazení si webovou aplikaci prohlédněte, abyste se ujistili, že funguje. Ve webovém prohlížeči přejděte na: `https://<your-site-name>.azurewebsites.net`

    V případě neúspěšného nasazení si přečtěte chybovou zprávu. Důvodem může být to, že název webu je již obsazený. Znovu nasaďte šablonu a vyberte jiný název lokality. 
1. Zobrazí se web, na který se však zatím neodeslaly žádné události.

   ![Zobrazení nového webu](./media/custom-event-quickstart-portal/view-site.png)

## <a name="subscribe-to-custom-topic"></a>Přihlášení k odběru vlastního tématu

K odběru tématu Event Gridu se přihlašujete, aby služba Event Grid věděla, které události chcete sledovat a kam má tyto události odesílat.

1. Nyní na stránce **Event Grid téma** pro vlastní téma vyberte **+ odběr události** na panelu nástrojů.

    :::image type="content" source="./media/custom-event-quickstart-portal/new-event-subscription.png" alt-text="Tlačítko Přidat odběr události":::
2. Na stránce **vytvořit odběr události** použijte následující postup:
    1. Zadejte **název** odběru události.
    3. Vyberte **webový Hook** pro **Typ koncového bodu**. 
    4. Zvolte **Vybrat koncový bod**. 

        :::image type="content" source="./media/custom-event-quickstart-portal/provide-subscription-values.png" alt-text="Zadání hodnot pro odběr události":::
    5. Jako koncový bod webhooku zadejte adresu URL vaší webové aplikace a do adresy URL domovské stránky přidejte `api/updates`. Zvolte **Potvrdit výběr**.

        :::image type="content" source="./media/custom-event-quickstart-portal/provide-endpoint.png" alt-text="Zadání adresy URL koncového bodu":::
    6. Zpátky na stránce **vytvořit odběr události** vyberte **vytvořit**.

3. Podívejte se na webovou aplikaci znovu a všimněte si, že do ní byla odeslána událost ověření odběru. Vyberte ikonu oka a rozbalte data události. Služba Event Grid odešle událost ověření, aby koncový bod mohl ověřit, že data události chce přijímat. Webová aplikace obsahuje kód pro ověření odběru.

    ![Zobrazení události odběru](./media/custom-event-quickstart-portal/view-subscription-event.png)

## <a name="send-an-event-to-your-topic"></a>Odeslání události do tématu

Nyní aktivujeme událost, abychom viděli, jak služba Event Grid distribuuje zprávu do vašeho koncového bodu. K odeslání testovací události do vlastního tématu použijte Azure CLI nebo PowerShell. Obvykle by aplikace nebo služba Azure odesílala data události.

V prvním příkladu se používá Azure CLI. Načte adresu URL a klíč vlastního tématu a data ukázkové události. Místo položky `<topic name>` použijte název vlastního tématu. Vytvoří se ukázková data události. Element JSON `data` je datová část vaší události. V tomto poli může být libovolný JSON ve správném formátu. Můžete také použít pole subject (předmět) pro pokročilé směrování a filtrování. CURL je nástroj, který odesílá požadavky HTTP.


### <a name="azure-cli"></a>Azure CLI
1. V Azure Portal vyberte **Cloud Shell**. Cloud Shell se otevře v dolním podokně webového prohlížeče. 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-cloud-shell.png" alt-text="Vybrat ikonu Cloud Shell":::
1. V levém horním rohu okna Cloud Shell vyberte **bash** . 

    ![Cloud Shell – bash](./media/custom-event-quickstart-portal/cloud-shell-bash.png)
1. Spusťte následující příkaz, který získá **koncový bod** pro téma: po zkopírování a vložení příkazu aktualizujte **název tématu** a **název skupiny prostředků** před spuštěním příkazu. Do tohoto koncového bodu tématu budete publikovat ukázkové události. 

    ```azurecli
    endpoint=$(az eventgrid topic show --name <topic name> -g <resource group name> --query "endpoint" --output tsv)
    ```
2. Spusťte následující příkaz, který získá **klíč** pro vlastní téma: po zkopírování a vložení příkazu aktualizujte **název tématu** a název **skupiny prostředků** před spuštěním příkazu. Toto je primární klíč Event Grid tématu. Pokud chcete tento klíč získat z Azure Portal, přepněte se na kartu **přístupové klíče** na stránce **Event Grid tématu** . Aby bylo možné odeslat událost do vlastního tématu, budete potřebovat přístupový klíč. 

    ```azurecli
    key=$(az eventgrid topic key list --name <topic name> -g <resource group name> --query "key1" --output tsv)
    ```
3. Zkopírujte následující příkaz s definicí události a stiskněte klávesu **ENTER**. 

    ```json
    event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
    ```
4. Spusťte následující příkaz **kudrlinkou** pro odeslání události: v příkazu `aeg-sas-key` se záhlaví nastaví na přístupový klíč, který jste dříve získali. 

    ```
    curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
    ```

### <a name="azure-powershell"></a>Azure PowerShell
V druhém příkladu se k provedení podobných kroků používá PowerShell.

1. V Azure Portal vyberte možnost **Cloud Shell** (nebo přejít na `https://shell.azure.com/` ). Cloud Shell se otevře v dolním podokně webového prohlížeče. 

    :::image type="content" source="./media/custom-event-quickstart-portal/select-cloud-shell.png" alt-text="Vybrat ikonu Cloud Shell":::
1. V **Cloud Shell** v levém horním rohu okna Cloud Shell vyberte **PowerShell** . Podívejte se na ukázku obrázku okna **Cloud Shell** v části Azure CLI.
2. Nastavte následující proměnné. Po zkopírování a vložení každého příkazu aktualizujte **název tématu** a **název skupiny prostředků** před spuštěním příkazu:

    **Skupina prostředků**:
    ```powershell
    $resourceGroupName = "<resource group name>"
    ```

    **Název Event Grid tématu**:    
    ```powershell
    $topicName = "<topic name>"
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
Právě jste aktivovali událost a služba Event Grid odeslala zprávu do koncového bodu, který jste nakonfigurovali při přihlášení k odběru. Podívejte se na webovou aplikaci, abyste si zobrazili událost, kterou jste právě odeslali.

:::image type="content" source="./media/custom-event-quickstart-portal/event-grid-viewer-end.png" alt-text="Event Grid Viewer":::

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud chcete pokračovat v práci s touto událostí, nevyčišťujte prostředky vytvořené v rámci tohoto článku. V opačném případě odstraňte prostředky, které jste v rámci tohoto článku vytvořili.

1. V nabídce vlevo vyberte **skupiny prostředků** . Pokud ho v nabídce vlevo nevidíte, v nabídce vlevo vyberte **všechny služby** a vyberte **skupiny prostředků**. 

    ![Skupiny prostředků](./media/custom-event-quickstart-portal/delete-resource-groups.png)
1. Vyberte skupinu prostředků, na které se má spustit stránka **skupiny prostředků** . 
1. Na panelu nástrojů vyberte **Odstranit skupinu prostředků** . 
1. Potvrďte odstranění zadáním názvu skupiny prostředků a vyberte **Odstranit**. 

    Další skupina prostředků, kterou vidíte v imagi, byla vytvořena a používána oknem Cloud Shell. Pokud neplánujete použít okno Cloud Shell později, odstraňte ho. 

## <a name="next-steps"></a>Další kroky

Když teď víte, jak vytvářet vlastní témata a odběry událostí, zjistěte, s čím vám služba Event Grid ještě může pomoct:

- [Informace o službě Event Grid](overview.md)
- [Směrování událostí služby Blob Storage do vlastního webového koncového bodu](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Monitorování změn virtuálního počítače pomocí služeb Azure Event Grid a Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Streamování velkých objemů dat do datového skladu](event-grid-event-hubs-integration.md)
