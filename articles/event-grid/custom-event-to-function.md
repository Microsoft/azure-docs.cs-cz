---
title: 'Úvodní příručka: Odeslání vlastních událostí do funkce Azure – grid událostí'
description: 'Úvodní příručka: Pomocí Azure Event Grid a Azure CLI nebo portálu publikujte téma a přihlaste se k odběru této události. Funkce Azure se používá pro koncový bod.'
services: event-grid
keywords: ''
author: banisadr
ms.author: babanisa
ms.date: 11/15/2019
ms.topic: quickstart
ms.service: event-grid
ms.openlocfilehash: 5e38571cf84537fd722093b96cd277743e8ce80c
ms.sourcegitcommit: fab450a18a600d72b583ecfbe6c5e53afd43408c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80292149"
---
# <a name="quickstart-route-custom-events-to-an-azure-function-with-event-grid"></a>Úvodní příručka: Směrování vlastních událostí do funkce Azure pomocí mřížky událostí

Azure Event Grid je služba zpracování událostí pro cloud. Funkce Azure je jedním z podporovaných obslužných rutin událostí. V tomto článku vytvoříte pomocí webu Azure Portal vlastní téma, přihlásíte se k jeho odběru a aktivujete událost, abyste viděli výsledek. Události odešlete do funkce Azure.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-azure-function"></a>Vytvoření funkce Azure

Před přihlášením k odběru vlastního tématu vytvoříme funkci pro zpracování událostí. Na webu Azure Portal klikněte na "Vytvořit prostředek" a zadejte 'funkce', pak zvolte "Aplikace funkce" a klikněte na vytvořit. V části Skupina prostředků vyberte možnost Vytvořit nový a pojmenujte ji. Budete používat pro zbytek kurzu. Pojmenujte aplikaci function App, ponechte přepínač Publikovat na "Kód", vyberte libovolný běh a oblast a pak stiskněte klávesu create.

Jakmile je aplikace funkce připravena, přejděte na ni a klikněte na tlačítko "+ Nová funkce". Vyberte možnost In-portal pro vývojové prostředí a stiskněte dál. V části vytvořit funkci zvolte "Další šablony", chcete-li zobrazit další šablony a pak vyhledat "Aktivační událost sítě událostí Azure" a vyberte ji. Pokud tuto aktivační událost používáte poprvé, bude pravděpodobně nutné rozšíření nainstalovat kliknutím na tlačítko Instalovat.

![Aktivační událost události funkce](./media/custom-event-to-function/grid-trigger.png)

Po instalaci rozšíření klikněte na pokračovat, pojmenujte svou funkci a stiskněte klávesu Create.

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="create-a-custom-topic"></a>Vytvoření vlastního tématu

Téma Event Gridu poskytuje uživatelsky definovaný koncový bod, do kterého odesíláte události. 

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. V levé navigační nabídce vyberte **Všechny služby,** vyhledejte **mřížku událostí**a vyberte **Témata mřížky událostí**. 

    ![Vybrat témata mřížky událostí](./media/custom-event-to-function/select-event-grid-topics.png)
3. Na stránce **Témata mřížky událostí** vyberte **+ Přidat** na panelu nástrojů. 

    ![Tlačítko Přidat mřížku událostí](./media/custom-event-to-function/add-event-grid-topic-button.png)

4. Na stránce **Vytvořit téma** postupujte takto:

    1. Zadejte jedinečný **název** pro vlastní téma. Název tématu musí být jedinečný, protože je reprezentován položkou DNS. Nepoužívejte název zobrazený na obrázku. Místo toho vytvořte vlastní název - musí být mezi 3-50 znaky a obsahovat pouze hodnoty a-z, A-Z, 0-9 a "-".
    2. Vyberte **předplatné**Azure .
    3. Vyberte stejnou skupinu prostředků z předchozích kroků.
    4. Vyberte **umístění** pro téma mřížky událostí.
    5. Zachovat výchozí hodnotu **Schéma mřížky událostí** pro pole **Schéma události.** 

       ![Stránka Vytvořit téma](./media/custom-event-to-function/create-custom-topic.png)
    6. Vyberte **Vytvořit**. 

5. Po vytvoření vlastního tématu se zobrazí oznámení o úspěchu. Vyberte **Přejít do skupiny prostředků**. 

   ![Zobrazení oznámení o úspěchu](./media/custom-event-to-function/success-notification.png)

6. Na stránce **Skupina prostředků** vyberte téma mřížky událostí. 

   ![Výběr zdroje tématu mřížky událostí](./media/custom-event-to-function/select-event-grid-topic.png)

7. Zobrazí se stránka **Téma mřížky událostí** pro mřížku událostí. Nechte tuto stránku otevřenou. Můžete použít později v rychlém startu. 

    ![Domovská stránka tématu mřížky událostí](./media/custom-event-to-function/event-grid-topic-home-page.png)

## <a name="subscribe-to-custom-topic"></a>Přihlášení k odběru vlastního tématu

K odběru tématu Event Gridu se přihlašujete, aby služba Event Grid věděla, které události chcete sledovat a kam má tyto události odesílat.

1. Nyní na stránce **Téma události** pro vlastní téma vyberte na panelu nástrojů + **Odběr událostí.**

   ![Přidání odběru události](./media/custom-event-to-function/new-event-subscription.png)

2. Na stránce **Vytvořit odběr událostí** postupujte takto:
    1. Zadejte **název** předplatného události.
    3. Vyberte **funkci Azure** pro **typ koncového bodu**. 
    4. Zvolte **Vybrat koncový bod**. 

       ![Zadání hodnot pro odběr události](./media/custom-event-to-function/provide-subscription-values.png)

    5. Pro koncový bod funkce vyberte předplatné Azure a skupinu prostředků, ve které se vaše aplikace funkce nachází, a pak vyberte aplikaci funkcí a funkci, kterou jste vytvořili dříve. Zvolte **Potvrdit výběr**.

       ![Zadání adresy URL koncového bodu](./media/custom-event-to-function/provide-endpoint.png)

    6. Zpět na stránce **Vytvořit odběr události** vyberte **Vytvořit**.

## <a name="send-an-event-to-your-topic"></a>Odeslání události do tématu

Nyní aktivujeme událost, abychom viděli, jak služba Event Grid distribuuje zprávu do vašeho koncového bodu. K odeslání testovací události do vlastního tématu použijte Azure CLI nebo PowerShell. Obvykle by aplikace nebo služba Azure odesílala data události.

V prvním příkladu se používá Azure CLI. Načte adresu URL a klíč vlastního tématu a data ukázkové události. Místo položky `<topic name>` použijte název vlastního tématu. Vytvoří se ukázková data události. Element JSON `data` je datová část vaší události. V tomto poli může být libovolný JSON ve správném formátu. Můžete také použít pole subject (předmět) pro pokročilé směrování a filtrování. CURL je nástroj, který odesílá požadavky HTTP.


### <a name="azure-cli"></a>Azure CLI
1. Na webu Azure Portal vyberte **Cloud Shell**. V levém horním rohu okna Cloud Shell uyberte **Bash.** 

    ![Cloud Shell - Bash](./media/custom-event-quickstart-portal/cloud-shell-bash.png)
1. Spusťte následující příkaz, abyste získali **koncový bod** pro téma: Po zkopírování a vložení příkazu aktualizujte **název tématu** a **název skupiny prostředků** před spuštěním příkazu. 

    ```azurecli
    endpoint=$(az eventgrid topic show --name <topic name> -g <resource group name> --query "endpoint" --output tsv)
    ```
2. Spusťte následující příkaz, abyste získali **klíč** pro vlastní téma: Po zkopírování a vložení příkazu aktualizujte **název tématu** a název **skupiny prostředků** před spuštěním příkazu. 

    ```azurecli
    key=$(az eventgrid topic key list --name <topic name> -g <resource group name> --query "key1" --output tsv)
    ```
3. Zkopírujte následující příkaz s definicí události a stiskněte **klávesu ENTER**. 

    ```json
    event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/motorcycles", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "make": "Ducati", "model": "Monster"},"dataVersion": "1.0"} ]'
    ```
4. Chcete-li událost odeslat, spusťte následující příkaz **Zvlnění:**

    ```
    curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
    ```

### <a name="azure-powershell"></a>Azure PowerShell
V druhém příkladu se k provedení podobných kroků používá PowerShell.

1. Na webu Azure Portal vyberte **Cloud** `https://shell.azure.com/`Shell (případně přejděte na). V levém horním rohu okna Cloud Shellu vyberte **PowerShell.** Podívejte se na ukázkovou bitovou kopii okna **Cloud Shell** v části Azure CLI.
2. Nastavte následující proměnné. Po zkopírování a vložení každého příkazu aktualizujte **název tématu** a **název skupiny prostředků** před spuštěním příkazu:

    ```powershell
    $resourceGroupName = <resource group name>
    $topicName = <topic name>
    ```
3. Chcete-li získat koncový **bod** a **klíče** pro toto téma, spusťte následující příkazy:

    ```powershell
    $endpoint = (Get-AzEventGridTopic -ResourceGroupName $resourceGroupName -Name $topicName).Endpoint
    $keys = Get-AzEventGridTopicKey -ResourceGroupName $resourceGroupName -Name $topicName
    ```
4. Připravte akci. Zkopírujte a spusťte příkazy v okně Prostředí cloud. 

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
5. K odeslání události použijte rutinu **Invoke-WebRequest.** 

    ```powershell
    Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
    ```

### <a name="verify-in-the-event-grid-viewer"></a>Ověřit v prohlížeči mřížky událostí
Právě jste aktivovali událost a služba Event Grid odeslala zprávu do koncového bodu, který jste nakonfigurovali při přihlášení k odběru. Přejděte do funkce aktivované pomocí funkce Event Grid a otevřete protokoly. Měli byste vidět kopii datové části události v protokolech. Pokud se nejprve nerozhodnete otevřít okno protokolů, nebo se znovu připojit a zkuste znovu odeslat testovací událost.

![Protokol aktivačních událostí úspěšné funkce](./media/custom-event-to-function/successful-function.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud chcete pokračovat v práci s touto událostí, nevyčišťujte prostředky vytvořené v rámci tohoto článku. V opačném případě odstraňte prostředky, které jste v rámci tohoto článku vytvořili.

1. V levé nabídce vyberte **Skupiny prostředků.** Pokud ji v levé nabídce nevidíte, vyberte v levé nabídce **všechny služby** a vyberte **Skupiny zdrojů**. 
2. Vyberte skupinu prostředků, kterou chcete spustit stránku **Skupina prostředků.** 
3. Na panelu nástrojů vyberte **Odstranit skupinu prostředků.** 
4. Potvrďte odstranění zadáním názvu skupiny prostředků a vyberte **Odstranit**. 

    ![Skupiny prostředků](./media/custom-event-to-function/delete-resource-groups.png)

    Druhá skupina prostředků, kterou vidíte na obrázku, byla vytvořena a používána v okně Prostředí cloud. Pokud později neplánujete použít okno Cloud Shell, odstraňte ho. 

## <a name="next-steps"></a>Další kroky

Když teď víte, jak vytvářet témata a odběry událostí, zjistěte, s čím vám služba Event Grid ještě může pomoct:

- [Informace o službě Event Grid](overview.md)
- [Směrování událostí služby Blob Storage do vlastního webového koncového bodu](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Monitorování změn virtuálního počítače pomocí služeb Azure Event Grid a Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Streamování velkých objemů dat do datového skladu](event-grid-event-hubs-integration.md)
