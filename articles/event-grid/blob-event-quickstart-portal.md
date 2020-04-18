---
title: 'Úvodní příručka: Odeslání událostí úložiště objektů Blob do webového koncového bodu – portál'
description: 'Úvodní příručka: Pomocí Azure Event Grid a portálu Azure vytvořte účet úložiště objektů Blob a odebíráte si jeho události. Odeslat události webhooku.'
services: event-grid
keywords: ''
author: spelluru
ms.author: spelluru
ms.date: 04/16/2020
ms.topic: quickstart
ms.service: event-grid
ms.custom: seodec18
ms.openlocfilehash: ada451b6bb3578a2903e9bd832b98981d7029d1d
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605803"
---
# <a name="quickstart-route-blob-storage-events-to-web-endpoint-with-the-azure-portal"></a>Úvodní příručka: Směrování událostí úložiště objektů blob do koncového bodu webu pomocí portálu Azure

Azure Event Grid je služba zpracování událostí pro cloud. V tomto článku pomocí webu Azure Portal vytvoříte účet úložiště objektů blob, přihlásíte se k odběru událostí tohoto úložiště objektů blob a aktivujete událost pro zobrazení výsledku. Obvykle odesíláte události do koncového bodu, který data události zpracuje a provede akce. Pro zjednodušení tohoto článku však budete události odesílat do webové aplikace, která shromažďuje a zobrazuje zprávy.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

Až budete hotovi, uvidíte, že se data události odeslala do webové aplikace.

![Zobrazení výsledků](./media/blob-event-quickstart-portal/view-results.png)

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. Pokud chcete úložiště objektů blob, vyberte **Vytvořit prostředek**. 

1. Výběrem **Úložiště** vyfiltrujte dostupné možnosti a vyberte **Účet úložiště – objekt blob, soubor, tabulka, fronta**.

   ![Výběr úložiště](./media/blob-event-quickstart-portal/create-storage.png)

   Pokud se chcete přihlásit k odběru událostí, vytvořte si účet úložiště pro obecné účely verze 2 nebo účet úložiště objektů blob.
   
1. Na stránce **Vytvořit účet úložiště** postupujte takto:
    1. Vyberte své předplatné Azure. 
    2. Pro **skupinu prostředků**vytvořte novou skupinu prostředků nebo vyberte existující skupinu. 
    3. Zadejte název účtu úložiště. 
    4. Vyberte **Zkontrolovat a vytvořit**. 

       ![Počáteční kroky](./media/blob-event-quickstart-portal/provide-blob-values.png)    
    5. Na stránce **Revize + vytvoření** zkontrolujte nastavení a vyberte **Vytvořit**. 

        >[!NOTE]
        > Pouze účty úložiště druhu **StorageV2 (pro obecné účely v2)** a Integrace událostí podpory **BlobStorage.** **Úložiště (genral účel v1)** *nepodporuje* integraci s Event Grid.

## <a name="create-a-message-endpoint"></a>Vytvoření koncového bodu zpráv

Před přihlášením k odběru událostí úložiště objektů blob vytvoříme koncový bod pro zprávy události. Koncový bod obvykle provede akce na základě dat události. Pro zjednodušení tohoto rychlého startu nasadíte [předem připravenou webovou aplikaci](https://github.com/Azure-Samples/azure-event-grid-viewer), která zobrazuje zprávy události. Nasazené řešení zahrnuje plán služby App Service, webovou aplikaci App Service a zdrojový kód z GitHubu.

1. Vyberte **Nasadit do Azure** a nasaďte řešení do svého předplatného. 

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>
2. Na stránce **Vlastní nasazení** postupujte takto: 
    1. Ve **skupině Prostředků**vyberte skupinu prostředků, kterou jste vytvořili při vytváření účtu úložiště. Bude pro vás jednodušší vyčistit po dokončení kurzu odstraněním skupiny prostředků.  
    2. Do **pole Název webu**zadejte název webové aplikace.
    3. Do **pole Název plánu hostování**zadejte název plánu služby App Service, který chcete použít pro hostování webové aplikace.
    4. Zaškrtněte **políčko, protože souhlasím s výše uvedenými podmínkami**. 
    5. Vyberte **Koupit**. 

       ![Parametry nasazení](./media/blob-event-quickstart-portal/template-deploy-parameters.png)
1. Dokončení nasazení může trvat několik minut. Vyberte Výstrahy (ikona zvonku) na portálu a pak vyberte **Přejít na skupinu prostředků**. 

    ![Výstraha – přechod na skupinu prostředků](./media/blob-event-quickstart-portal/navigate-resource-group.png)
4. Na stránce **Skupina prostředků** vyberte v seznamu prostředků webovou aplikaci, kterou jste vytvořili. V tomto seznamu se také zobrazí plán služby App Service a účet úložiště. 

    ![Vybrat web](./media/blob-event-quickstart-portal/resource-group-resources.png)
5. Na stránce **App Service** pro webovou aplikaci vyberte adresu URL pro přechod na web. Adresa URL by měla `https://<your-site-name>.azurewebsites.net`být v tomto formátu: .
    
    ![Přechod na web](./media/blob-event-quickstart-portal/web-site.png)

6. Potvrďte, že se web zobrazují, ale zatím na něj nebyly zveřejněny žádné události.

   ![Zobrazení nového webu](./media/blob-event-quickstart-portal/view-site.png)

[!INCLUDE [event-grid-register-provider-portal.md](../../includes/event-grid-register-provider-portal.md)]

## <a name="subscribe-to-the-blob-storage"></a>Přihlášení k odběru úložiště objektů blob

K odběru tématu se přihlašujete, aby služba Event Grid věděla, které události chcete sledovat a kam má tyto události odesílat.

1. Na portálu přejděte na účet Azure Storage, který jste vytvořili dříve. V levé nabídce vyberte **Všechny prostředky** a vyberte účet úložiště. 
2. Na stránce **Účet úložiště** vyberte **Události** v levé nabídce.
1. Vyberte **Další možnosti** a **Webhook**. Události odesíláte do aplikace prohlížeče pomocí webového háčku pro koncový bod. 

   ![Výběr webhooku](./media/blob-event-quickstart-portal/select-web-hook.png)
3. Na stránce **Vytvořit odběr událostí** postupujte takto: 
    1. Zadejte **název** předplatného události.
    2. Vyberte **webový hák** pro **typ koncového bodu**. 

       ![Vybrat typ koncového bodu webového háčku](./media/blob-event-quickstart-portal/select-web-hook-end-point-type.png)
4. V **části Koncový bod**klikněte na Vybrat koncový **bod**, `api/updates` zadejte adresu URL webové `https://spegridsite.azurewebsites.net/api/updates`aplikace a přidejte ji na adresu URL domovské stránky (například: ) a pak vyberte **Potvrdit výběr**.

   ![Potvrdit výběr koncového bodu](./media/blob-event-quickstart-portal/confirm-endpoint-selection.png)
5. Teď na stránce **Vytvořit odběr událostí** vyberte **Vytvořit** a vytvořte odběr události. 

   ![Výběr protokolů](./media/blob-event-quickstart-portal/create-subscription.png)

1. Podívejte se na webovou aplikaci znovu a všimněte si, že do ní byla odeslána událost ověření odběru. Vyberte ikonu oka a rozbalte data události. Služba Event Grid odešle událost ověření, aby koncový bod mohl ověřit, že data události chce přijímat. Webová aplikace obsahuje kód pro ověření odběru.

   ![Zobrazení události odběru](./media/blob-event-quickstart-portal/view-subscription-event.png)

Nyní aktivujeme událost, abychom viděli, jak služba Event Grid distribuuje zprávu do vašeho koncového bodu.

## <a name="send-an-event-to-your-endpoint"></a>Odeslání události do koncového bodu

Událost pro úložiště objektů blob aktivujete nahráním souboru. Soubor nemusí obsahovat žádný konkrétní obsah. V tomto článku se předpokládá, že máte soubor testfile.txt, ale můžete použít jakýkoli soubor.

1. Na webu Azure Portal přejděte na účet úložiště objektů Blob a na stránce **Přehled** vyberte **Kontejnery.**

   ![Výběr objektů blob](./media/blob-event-quickstart-portal/select-blobs.png)

1. Vyberte **+ Kontejner**. Pojmenujte kontejner a použijte libovolnou úroveň přístupu a vyberte **Vytvořit**. 

   ![Přidání kontejneru](./media/blob-event-quickstart-portal/add-container.png)

1. Vyberte nový kontejner.

   ![Výběr kontejneru](./media/blob-event-quickstart-portal/select-container.png)

1. Pokud chcete nahrát soubor, vyberte **Nahrát**. Na stránce **Nahrát objekt blob** projděte a vyberte soubor, který chcete nahrát pro testování, a pak na této stránce vyberte **Nahrát.** 

   ![Výběr nahrání](./media/blob-event-quickstart-portal/upload-file.png)

1. Přejděte k testovacímu souboru a nahrajte ho.

1. Právě jste aktivovali událost a služba Event Grid odeslala zprávu do koncového bodu, který jste nakonfigurovali při přihlášení k odběru. Zpráva je ve formátu JSON a obsahuje pole s jednou nebo více událostmi. V následujícím příkladu zpráva JSON obsahuje pole s jednou událostí. Zobrazte webovou aplikaci a všimněte si, že byla přijata událost **vytvořená objektem blob.** 

   ![Událost vytvořená objektem blob](./media/blob-event-quickstart-portal/blob-created-event.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete pokračovat v práci s touto událostí, nevyčišťujte prostředky vytvořené v rámci tohoto článku. V opačném případě odstraňte prostředky, které jste v rámci tohoto článku vytvořili.

Vyberte skupinu prostředků a pak vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

Když teď víte, jak vytvářet vlastní témata a odběry událostí, zjistěte, s čím vám služba Event Grid ještě může pomoct:

- [Informace o službě Event Grid](overview.md)
- [Směrování událostí služby Blob Storage do vlastního webového koncového bodu](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)
- [Monitorování změn virtuálního počítače pomocí služeb Azure Event Grid a Logic Apps](monitor-virtual-machine-changes-event-grid-logic-app.md)
- [Streamování velkých objemů dat do datového skladu](event-grid-event-hubs-integration.md)
