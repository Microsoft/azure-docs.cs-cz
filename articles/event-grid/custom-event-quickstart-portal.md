---
title: 'Rychlý Start: odesílání vlastních událostí do webového koncového bodu – Event Grid Azure Portal'
description: 'Rychlý Start: použijte Azure Event Grid a Azure Portal k publikování vlastního tématu a přihlášení k odběru událostí pro toto téma. Události jsou zpracovávány webovou aplikací.'
services: event-grid
keywords: ''
author: spelluru
ms.author: spelluru
ms.date: 11/05/2019
ms.topic: quickstart
ms.service: event-grid
ms.custom: seodec18
ms.openlocfilehash: 398e63ec9a8b9e1b16d8ffcee538351fc6572de9
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720793"
---
# <a name="quickstart-route-custom-events-to-web-endpoint-with-the-azure-portal-and-event-grid"></a>Rychlý Start: směrování vlastních událostí na webový koncový bod pomocí Azure Portal a Event Grid

Azure Event Grid je služba zpracování událostí pro cloud. V tomto článku vytvoříte pomocí webu Azure Portal vlastní téma, přihlásíte se k jeho odběru a aktivujete událost, abyste viděli výsledek. Obvykle odesíláte události do koncového bodu, který data události zpracuje a provede akce. Pro zjednodušení tohoto článku však budete události odesílat do webové aplikace, která shromažďuje a zobrazuje zprávy.

Až budete hotovi, uvidíte, že se data události odeslala do webové aplikace.

![Zobrazení výsledků](./media/custom-event-quickstart-portal/view-result.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="create-a-custom-topic"></a>Vytvoření vlastního tématu

Téma Event Gridu poskytuje uživatelsky definovaný koncový bod, do kterého odesíláte události. 

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. V levém navigačním panelu vyberte **všechny služby** , vyhledejte **Event Grid**a vyberte **Event Grid témata**. 

    ![Vybrat Event Grid témata](./media/custom-event-quickstart-portal/select-event-grid-topics.png)
3. Na stránce **Event Grid témata** vyberte **+ Přidat** na panelu nástrojů. 

    ![Přidat Event Grid – tlačítko tématu](./media/custom-event-quickstart-portal/add-event-grid-topic-button.png)
4. Na stránce **vytvořit téma** postupujte podle následujících kroků:
    1. Zadejte jedinečný **název** vlastního tématu. Název tématu musí být jedinečný, protože je reprezentován položkou DNS. Nepoužívejte název zobrazený na obrázku. Místo toho vytvořte vlastní název – musí mít 3-50 znaků a obsahovat pouze hodnoty a-z, A-Z, 0-9 a "-".
    2. Vyberte své **předplatné** Azure.
    3. Vyberte existující skupinu prostředků nebo vyberte **vytvořit novou**a zadejte **název** **skupiny prostředků**.
    4. Vyberte **umístění** tématu Event Grid.
    5. Pro pole **schéma události** ponechte výchozí hodnotu **Event Grid Schema** . 

       ![Vytvořit stránku tématu](./media/custom-event-quickstart-portal/create-custom-topic.png)
    6. Vyberte **Vytvořit**. 
5. Po vytvoření vlastního tématu se zobrazí oznámení o úspěchu. Vyberte **Přejít do skupiny prostředků**. 

   ![Zobrazení oznámení o úspěchu](./media/custom-event-quickstart-portal/success-notification.png)
6. Na stránce **Skupina prostředků** vyberte téma Event Grid. 

   ![Výběr prostředku tématu Event gridu](./media/custom-event-quickstart-portal/select-event-grid-topic.png)
7. Pro událost Event Grid se zobrazí stránka **Event Grid téma** . Tuto stránku nechte otevřenou. Použijete ji později v rychlém startu. 

    ![Domovská stránka Event Gridho tématu](./media/custom-event-quickstart-portal/event-grid-topic-home-page.png)

## <a name="create-a-message-endpoint"></a>Vytvoření koncového bodu zpráv
Před vytvořením předplatného pro vlastní téma vytvořte koncový bod pro zprávu události. Koncový bod obvykle provede akce na základě dat události. Pro zjednodušení tohoto rychlého startu nasadíte [předem připravenou webovou aplikaci](https://github.com/Azure-Samples/azure-event-grid-viewer), která zobrazuje zprávy události. Nasazené řešení zahrnuje plán služby App Service, webovou aplikaci App Service a zdrojový kód z GitHubu.

1. Na stránce článek vyberte **nasadit do Azure** a nasaďte řešení do svého předplatného. Na webu Azure Portal zadejte hodnoty pro parametry.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>
1. Dokončení nasazení může trvat několik minut. Po úspěšném nasazení si webovou aplikaci prohlédněte, abyste se ujistili, že funguje. Ve webovém prohlížeči přejděte na: `https://<your-site-name>.azurewebsites.net`
1. Zobrazí se web, na který se však zatím neodeslaly žádné události.

   ![Zobrazení nového webu](./media/custom-event-quickstart-portal/view-site.png)

## <a name="subscribe-to-custom-topic"></a>Přihlášení k odběru vlastního tématu

K odběru tématu Event Gridu se přihlašujete, aby služba Event Grid věděla, které události chcete sledovat a kam má tyto události odesílat.

1. Nyní na stránce **Event Grid téma** pro vlastní téma vyberte **+ odběr události** na panelu nástrojů.

   ![Přidání odběru události](./media/custom-event-quickstart-portal/new-event-subscription.png)
2. Na stránce **vytvořit odběr události** použijte následující postup:
    1. Zadejte **název** odběru události.
    3. Vyberte **webový Hook** pro **Typ koncového bodu**. 
    4. Zvolte **Vybrat koncový bod**. 

       ![Zadání hodnot pro odběr události](./media/custom-event-quickstart-portal/provide-subscription-values.png)
    5. Jako koncový bod webhooku zadejte adresu URL vaší webové aplikace a do adresy URL domovské stránky přidejte `api/updates`. Zvolte **Potvrdit výběr**.

       ![Zadání adresy URL koncového bodu](./media/custom-event-quickstart-portal/provide-endpoint.png)
    6. Zpátky na stránce **vytvořit odběr události** vyberte **vytvořit**.

3. Podívejte se na webovou aplikaci znovu a všimněte si, že do ní byla odeslána událost ověření odběru. Vyberte ikonu oka a rozbalte data události. Služba Event Grid odešle událost ověření, aby koncový bod mohl ověřit, že data události chce přijímat. Webová aplikace obsahuje kód pro ověření odběru.

    ![Zobrazení události odběru](./media/custom-event-quickstart-portal/view-subscription-event.png)

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

1. V Azure Portal vyberte možnost **Cloud Shell** (případně přejít na https://shell.azure.com/). V levém horním rohu okna Cloud Shell vyberte **PowerShell** . Podívejte se na ukázku obrázku okna **Cloud Shell** v části Azure CLI.
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
Právě jste aktivovali událost a služba Event Grid odeslala zprávu do koncového bodu, který jste nakonfigurovali při přihlášení k odběru. Podívejte se na webovou aplikaci, abyste si zobrazili událost, kterou jste právě odeslali.

```json
{
  "id": "974",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2019-03-28T01:11:59+00:00",
  "data": {
    "make": "Ducati",
    "model": "Monster"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/myegridrg/providers/Microsoft.EventGrid/topics/myegridtopic"
}
```

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud chcete pokračovat v práci s touto událostí, nevyčišťujte prostředky vytvořené v rámci tohoto článku. V opačném případě odstraňte prostředky, které jste v rámci tohoto článku vytvořili.

1. V nabídce vlevo vyberte **skupiny prostředků** . Pokud ho v nabídce vlevo nevidíte, v nabídce vlevo vyberte **všechny služby** a vyberte **skupiny prostředků**. 
2. Vyberte skupinu prostředků, na které se má spustit stránka **skupiny prostředků** . 
3. Na panelu nástrojů vyberte **Odstranit skupinu prostředků** . 
4. Potvrďte odstranění zadáním názvu skupiny prostředků a vyberte **Odstranit**. 

    ![Skupiny prostředků](./media/custom-event-quickstart-portal/delete-resource-groups.png)

    Další skupina prostředků, kterou vidíte v imagi, byla vytvořena a používána oknem Cloud Shell. Pokud neplánujete použít okno Cloud Shell později, odstraňte ho. 

## <a name="next-steps"></a>Další kroky

Když teď víte, jak vytvářet vlastní témata a odběry událostí, zjistěte, s čím vám služba Event Grid ještě může pomoct:

- [Informace o službě Event Grid](overview.md)
- [Směrování událostí služby Blob Storage do vlastního webového koncového bodu](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Monitorování změn virtuálního počítače pomocí služeb Azure Event Grid a Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Streamování velkých objemů dat do datového skladu](event-grid-event-hubs-integration.md)
