---
title: 'Rychlý Start: odeslání událostí služby Blob Storage do webového koncového bodu – portál'
description: 'Rychlý Start: pomocí Azure Event Grid a Azure Portal vytvořte účet úložiště objektů BLOB a přihlaste se k odběru jeho událostí. Odešle události do Webhooku.'
ms.date: 07/07/2020
ms.topic: quickstart
ms.openlocfilehash: 46cd88558334239a1a9971c63b8b2608def3c4d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96005686"
---
# <a name="quickstart-route-blob-storage-events-to-web-endpoint-with-the-azure-portal"></a>Rychlý Start: směrování událostí služby Blob Storage do webového koncového bodu pomocí Azure Portal

Azure Event Grid je služba zpracování událostí pro cloud. V tomto článku pomocí webu Azure Portal vytvoříte účet úložiště objektů blob, přihlásíte se k odběru událostí tohoto úložiště objektů blob a aktivujete událost pro zobrazení výsledku. Obvykle odesíláte události do koncového bodu, který data události zpracuje a provede akce. Pro zjednodušení tohoto článku však budete události odesílat do webové aplikace, která shromažďuje a zobrazuje zprávy.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

Až budete hotovi, uvidíte, že se data události odeslala do webové aplikace.

![Zobrazení výsledků](./media/blob-event-quickstart-portal/view-results.png)

## <a name="create-a-storage-account"></a>Vytvoření účtu úložiště

1. Přihlaste se k [Azure Portal](https://portal.azure.com/).

1. Pokud chcete úložiště objektů blob, vyberte **Vytvořit prostředek**. 

1. Výběrem **Úložiště** vyfiltrujte dostupné možnosti a vyberte **Účet úložiště – objekt blob, soubor, tabulka, fronta**.

   ![Výběr úložiště](./media/blob-event-quickstart-portal/create-storage.png)

   Pokud se chcete přihlásit k odběru událostí, vytvořte si účet úložiště pro obecné účely verze 2 nebo účet úložiště objektů blob.
   
1. Na stránce **vytvořit účet úložiště** proveďte následující kroky:
    1. Vyberte své předplatné Azure. 
    2. V případě **skupiny prostředků** vytvořte novou skupinu prostředků nebo vyberte některou z existujících. 
    3. Zadejte název účtu úložiště. 
    4. Vyberte **Zkontrolovat a vytvořit**. 

       ![Počáteční kroky](./media/blob-event-quickstart-portal/provide-blob-values.png)    
    5. Na stránce **Revize + vytvořit** zkontrolujte nastavení a vyberte **vytvořit**. 

        >[!NOTE]
        > Integraci událostí podporují jenom účty úložiště typu **StorageV2 (obecné účely v2)** a **BlobStorage** . **Úložiště (Genral pro účely V1)** *nepodporuje integraci* s Event Grid.

## <a name="create-a-message-endpoint"></a>Vytvoření koncového bodu zpráv

Před přihlášením k odběru událostí úložiště objektů blob vytvoříme koncový bod pro zprávy události. Koncový bod obvykle provede akce na základě dat události. Pro zjednodušení tohoto rychlého startu nasadíte [předem připravenou webovou aplikaci](https://github.com/Azure-Samples/azure-event-grid-viewer), která zobrazuje zprávy události. Nasazené řešení zahrnuje plán služby App Service, webovou aplikaci App Service a zdrojový kód z GitHubu.

1. Vyberte **Nasadit do Azure** a nasaďte řešení do svého předplatného. 

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png" alt="Button to Deploy to Aquent." /></a>
2. Na stránce **vlastní nasazení** proveďte následující kroky: 
    1. V poli **Skupina prostředků** vyberte skupinu prostředků, kterou jste vytvořili při vytváření účtu úložiště. Až budete s kurzem hotovi, budete ho moct vyčistit tím, že odstraníte skupinu prostředků.  
    2. Jako **název lokality** zadejte název webové aplikace.
    3. Jako **název plánu hostování** zadejte název app Serviceho plánu, který se má použít pro hostování webové aplikace.
    4. Zaškrtněte políčko pro souhlasím **s podmínkami a ujednáními uvedenými nahoře**. 
    5. Vyberte **Koupit**. 

       ![Parametry nasazení](./media/blob-event-quickstart-portal/template-deploy-parameters.png)
1. Dokončení nasazení může trvat několik minut. Na portálu vyberte možnost výstrahy (ikona zvonku) a pak vyberte **Přejít do skupiny prostředků**. 

    ![Výstraha – přejít na skupinu prostředků](./media/blob-event-quickstart-portal/navigate-resource-group.png)
4. Na stránce **Skupina prostředků** v seznamu prostředků vyberte webovou aplikaci, kterou jste vytvořili. V tomto seznamu se zobrazí také plán App Service a účet úložiště. 

    ![Vybrat web](./media/blob-event-quickstart-portal/resource-group-resources.png)
5. Na stránce **App Service** vaší webové aplikace vyberte adresu URL, která se má přejít na web. Adresa URL by měla být v tomto formátu: `https://<your-site-name>.azurewebsites.net` .
    
    ![Přejít na web](./media/blob-event-quickstart-portal/web-site.png)

6. Ověřte, že se vám zobrazuje web, ale ještě se do něj nepublikovaly žádné události.

   ![Zobrazení nového webu](./media/blob-event-quickstart-portal/view-site.png)

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-the-blob-storage"></a>Přihlášení k odběru úložiště objektů blob

K odběru tématu se přihlašujete, aby služba Event Grid věděla, které události chcete sledovat a kam má tyto události odesílat.

1. Na portálu přejděte na účet Azure Storage, který jste vytvořili dříve. V nabídce vlevo vyberte **všechny prostředky** a vyberte svůj účet úložiště. 
2. Na stránce **účet úložiště** v nabídce vlevo vyberte položku **události** . 
1. Vyberte **Další možnosti** a **Webhook**. Události posíláte do aplikace prohlížeče pomocí webového zavěšení pro koncový bod. 

   ![Výběr webhooku](./media/blob-event-quickstart-portal/select-web-hook.png)
3. Na stránce **vytvořit odběr události** proveďte následující kroky: 
    1. Zadejte **název** odběru události.
    2. Zadejte **název** **systémového tématu**. Další informace o systémových tématech najdete v tématu [Přehled systémových témat](system-topics.md).

       ![Zadejte názvy pro odběr událostí a systémové téma.](./media/blob-event-quickstart-portal/event-subscription-name-system-topic.png)
    2. Vyberte **webový Hook** pro **Typ koncového bodu**. 

       ![Vybrat typ koncového bodu webového zavěšení](./media/blob-event-quickstart-portal/select-web-hook-end-point-type.png)
4. V případě **koncového bodu** klikněte na **Vybrat koncový bod** a zadejte adresu URL webové aplikace a přidejte `api/updates` ji na domovskou stránku URL (například: `https://spegridsite.azurewebsites.net/api/updates` ) a pak vyberte **potvrdit výběr**.

   ![Potvrdit výběr koncového bodu](./media/blob-event-quickstart-portal/confirm-endpoint-selection.png)
5. Nyní na stránce **vytvořit odběr události** vyberte **vytvořit** a vytvořte odběr události. 

   ![Výběr protokolů](./media/blob-event-quickstart-portal/create-subscription.png)

1. Podívejte se na webovou aplikaci znovu a všimněte si, že do ní byla odeslána událost ověření odběru. Vyberte ikonu oka a rozbalte data události. Služba Event Grid odešle událost ověření, aby koncový bod mohl ověřit, že data události chce přijímat. Webová aplikace obsahuje kód pro ověření odběru.

   ![Zobrazení události odběru](./media/blob-event-quickstart-portal/view-subscription-event.png)

Nyní aktivujeme událost, abychom viděli, jak služba Event Grid distribuuje zprávu do vašeho koncového bodu.

## <a name="send-an-event-to-your-endpoint"></a>Odeslání události do koncového bodu

Událost pro úložiště objektů blob aktivujete nahráním souboru. Soubor nemusí obsahovat žádný konkrétní obsah. V tomto článku se předpokládá, že máte soubor testfile.txt, ale můžete použít jakýkoli soubor.

1. V Azure Portal přejděte do svého účtu BLOB Storage a na stránce **Přehled** vyberte **kontejnery** .

   ![Výběr objektů blob](./media/blob-event-quickstart-portal/select-blobs.png)

1. Vyberte **+ Kontejner**. Poskytněte kontejneru název a použijte libovolnou úroveň přístupu a vyberte **vytvořit**. 

   ![Přidání kontejneru](./media/blob-event-quickstart-portal/add-container.png)

1. Vyberte nový kontejner.

   ![Výběr kontejneru](./media/blob-event-quickstart-portal/select-container.png)

1. Pokud chcete nahrát soubor, vyberte **Nahrát**. Na stránce **nahrát objekt BLOB** vyhledejte a vyberte soubor, který chcete nahrát pro testování, a pak na této stránce vyberte **nahrát** . 

   ![Výběr nahrání](./media/blob-event-quickstart-portal/upload-file.png)

1. Přejděte k testovacímu souboru a nahrajte ho.

1. Právě jste aktivovali událost a služba Event Grid odeslala zprávu do koncového bodu, který jste nakonfigurovali při přihlášení k odběru. Zpráva je ve formátu JSON a obsahuje pole s jednou nebo více událostmi. V následujícím příkladu zpráva JSON obsahuje pole s jednou událostí. Zobrazte si webovou aplikaci a Všimněte si, že byla přijata událost **vytvořeného objektu BLOB** . 

   ![Událost vytvoření objektu BLOB](./media/blob-event-quickstart-portal/blob-created-event.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete pokračovat v práci s touto událostí, nevyčišťujte prostředky vytvořené v rámci tohoto článku. V opačném případě odstraňte prostředky, které jste v rámci tohoto článku vytvořili.

Vyberte skupinu prostředků a pak vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

Když teď víte, jak vytvářet vlastní témata a odběry událostí, zjistěte, s čím vám služba Event Grid ještě může pomoct:

- [Informace o službě Event Grid](overview.md)
- [Směrování událostí služby Blob Storage do vlastního webového koncového bodu](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Monitorování změn virtuálního počítače pomocí služeb Azure Event Grid a Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Streamování velkých objemů dat do datového skladu](event-grid-event-hubs-integration.md)
