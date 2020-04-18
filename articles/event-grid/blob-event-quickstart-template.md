---
title: Odeslání událostí úložiště objektů Blob do webového koncového bodu – šablona
description: Pomocí Azure Event Grid a šablony Azure Resource Manager vytvořte účet úložiště objektů Blob a přihlaste se k odběru jeho událostí. Pošlete události do Webhooku.
services: event-grid
keywords: ''
author: spelluru
ms.author: spelluru
ms.date: 02/27/2020
ms.topic: quickstart
ms.service: event-grid
ms.custom: subject-armqs
ms.openlocfilehash: 86dc7a4ed05ceae5c7a641ffef23bd75ec48ceea
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605527"
---
# <a name="route-blob-storage-events-to-web-endpoint-by-using-azure-resource-manager-template"></a>Směrování událostí úložiště objektů blob do koncového bodu webu pomocí šablony Azure Resource Manager

Azure Event Grid je služba zpracování událostí pro cloud. V tomto článku použijete **šablonu Azure Resource Manager** k vytvoření účtu úložiště objektů Blob, přihlášení k odběru událostí pro toto úložiště objektů blob a aktivaci události k zobrazení výsledku. Obvykle odesíláte události do koncového bodu, který data události zpracuje a provede akce. Pro zjednodušení tohoto článku však budete události odesílat do webové aplikace, která shromažďuje a zobrazuje zprávy.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

### <a name="create-a-message-endpoint"></a>Vytvoření koncového bodu zpráv

Před přihlášením k odběru událostí úložiště objektů blob vytvoříme koncový bod pro zprávy události. Koncový bod obvykle provede akce na základě dat události. Pro zjednodušení tohoto rychlého startu nasadíte [předem připravenou webovou aplikaci](https://github.com/Azure-Samples/azure-event-grid-viewer), která zobrazuje zprávy události. Nasazené řešení zahrnuje plán služby App Service, webovou aplikaci App Service a zdrojový kód z GitHubu.

1. Vyberte **Nasadit do Azure** a nasaďte řešení do svého předplatného. Na webu Azure Portal zadejte hodnoty pro parametry.

    [Nasazení do Azure](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json)
1. Dokončení nasazení může trvat několik minut. Po úspěšném nasazení si webovou aplikaci prohlédněte, abyste se ujistili, že funguje. Ve webovém prohlížeči přejděte na: `https://<your-site-name>.azurewebsites.net`

1. Zobrazí se web, na který se však zatím neodeslaly žádné události.

   ![Zobrazení nového webu](./media/blob-event-quickstart-portal/view-site.png)

## <a name="create-a-storage-account-with-an-event-grid-subscription"></a>Vytvoření účtu úložiště s předplatným Služby Event Grid

### <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je ze [šablon Azure QuickStart](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage).

[!code-json[<Azure Resource Manager template create Blob storage Event Grid subscription>](~/quickstart-templates/101-event-grid-subscription-and-storage/azuredeploy.json)]

V šabloně jsou definovány dva prostředky Azure:

* [**Microsoft.Storage/storageAccounts:**](/azure/templates/microsoft.storage/storageaccounts)vytvořte účet úložiště Azure.
* [**"Microsoft.Storage/storageAccounts/providers/eventSubscriptions**](/azure/templates/microsoft.eventgrid/eventsubscriptions): vytvořte předplatné Azure Event Grid pro účet úložiště.

### <a name="deploy-the-template"></a>Nasazení šablony

1. Vyberte následující odkaz pro přihlášení do Azure a otevřete šablonu. Šablona vytvoří trezor klíčů a tajný klíč.

    [![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-event-grid-subscription-and-storage%2Fazuredeploy.json)

2. Zadejte **koncový bod**: zadejte adresu `api/updates` URL webové aplikace a přidejte ji na adresu URL domovské stránky.
3. Vyberte **Koupit,** chcete-li šablonu nasadit.

  Portál Azure se tady používá k nasazení šablony. Můžete taky použít Azure PowerShell, Azure CLI a REST API. Další metody nasazení najdete v [tématu Nasazení šablon](../azure-resource-manager/templates/deploy-powershell.md).

> [!NOTE]
> Další ukázky šablon Azure Event Grid najdete [zde](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Eventgrid).

## <a name="validate-the-deployment"></a>Ověření nasazení

Podívejte se na webovou aplikaci znovu a všimněte si, že do ní byla odeslána událost ověření odběru. Vyberte ikonu oka a rozbalte data události. Služba Event Grid odešle událost ověření, aby koncový bod mohl ověřit, že data události chce přijímat. Webová aplikace obsahuje kód pro ověření odběru.

![Zobrazení události odběru](./media/blob-event-quickstart-portal/view-subscription-event.png)

Nyní aktivujeme událost, abychom viděli, jak služba Event Grid distribuuje zprávu do vašeho koncového bodu.

Událost pro úložiště objektů blob aktivujete nahráním souboru. Soubor nemusí obsahovat žádný konkrétní obsah. V tomto článku se předpokládá, že máte soubor testfile.txt, ale můžete použít jakýkoli soubor.

Když soubor nahrajete do úložiště objektů Blob Azure, event grid odešle zprávu koncovému bodu, který jste nakonfigurovali při přihlášení k odběru. Zpráva je ve formátu JSON a obsahuje pole s jednou nebo více událostmi. V následujícím příkladu zpráva JSON obsahuje pole s jednou událostí. Zobrazte svou webovou aplikaci a všimněte si, že se přijala událost vytvoření objektu blob.

![Zobrazení výsledků](./media/blob-event-quickstart-portal/view-results.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již není potřeba, [odstraňte skupinu prostředků](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group
).

## <a name="next-steps"></a>Další kroky

Další informace o šablonách Azure Resource Manageru najdete v následujících článcích:

* [Dokumentace správce prostředků Azure](/azure/azure-resource-manager)
* [Definování prostředků v šablonách Azure Resource Manageru](/azure/templates/)
* [Šablony Azure Quickstart](https://azure.microsoft.com/resources/templates/)
* [Šablony Azure Event Grid](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Eventgrid).
