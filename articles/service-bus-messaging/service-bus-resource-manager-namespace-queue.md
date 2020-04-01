---
title: Vytvoření oboru názvů a fronty Azure Service Bus pomocí šablony Azure
description: 'Úvodní příručka: Vytvoření oboru názvů service bus a fronty pomocí šablony Azure Resource Manager'
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: a6bfb5fd-7b98-4588-8aa1-9d5f91b599b6
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: quickstart
ms.custom: subject-armqs
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 03/30/2020
ms.author: spelluru
ms.openlocfilehash: b08253104eeb61f6bb09fde507473d235a996494
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422633"
---
# <a name="quickstart-create-a-service-bus-namespace-and-a-queue-using-an-azure-resource-manager-template"></a>Úvodní příručka: Vytvoření oboru názvů service bus a fronty pomocí šablony Správce prostředků Azure

Tento článek ukazuje, jak používat šablonu Azure Resource Manager, která vytvoří obor názvů Service Bus a fronty v rámci tohoto oboru názvů. Článek vysvětluje, jak určit, které prostředky jsou nasazeny a jak definovat parametry, které jsou určeny při spuštění nasazení. Tuto šablonu můžete použít pro vlastní nasazení nebo ji upravit, aby splňovala vaše požadavky.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

Žádný

## <a name="create-a-service-bus-namespace-and-a-queue"></a>Vytvoření oboru názvů service bus a fronty

### <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je ze [šablon Azure QuickStart](https://azure.microsoft.com/resources/templates/201-servicebus-create-queue).

:::code language="json" source="~/quickstart-templates/201-servicebus-create-queue/azuredeploy.json" range="1-75" highlight="31-63":::

Mezi prostředky definované v šabloně patří:

- [**Obory názvů Microsoft.ServiceBus**](/azure/templates/microsoft.servicebus/namespaces)
- [**Microsoft.ServiceBus/obory názvů/fronty**](/azure/templates/microsoft.servicebus/namespaces/queues)

> [!NOTE]
> Následující šablony Azure Resource Manager jsou k dispozici ke stažení a nasazení.
>
> * [Vytvoření oboru názvů service bus s pravidlem fronty a autorizace](service-bus-resource-manager-namespace-auth-rule.md)
> * [Vytvoření oboru názvů Service Bus s tématem a odběrem](service-bus-resource-manager-namespace-topic.md)
> * [Vytvoření oboru názvů Service Bus](service-bus-resource-manager-namespace.md)
> * [Vytvoření oboru názvů Service Bus s tématem, předplatným a pravidlem](service-bus-resource-manager-namespace-topic-with-rule.md)

Další šablony najdete v [šablonách Azure Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular)

### <a name="deploy-the-template"></a>Nasazení šablony

Pomocí této šablony nasadíte obor názvů Service Bus s frontou.

[Fronty služby Service Bus](service-bus-queues-topics-subscriptions.md#queues) nabízejí doručení zpráv FIFO prvním u v prvním zobrazení, první ven (FIFO) jednomu nebo více konkurenčním spotřebitelům.

Chcete-li nasazení spustit automaticky, klepněte na následující tlačítko: Vytvořte novou skupinu prostředků pro nasazení, abyste ji mohli později snadno vyčistit.

[![Nasazení do Azure](./media/service-bus-resource-manager-namespace-queue/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="verify-the-deployment"></a>Ověření nasazení

1. **Chcete-li** zobrazit stav nasazení, vyberte nahoře oznámení. Počkejte, až bude nasazení úspěšné. Potom vyberte **Přejít na skupinu prostředků** v oznámení a přejděte na stránku skupiny prostředků, která obsahuje obor názvů Service Bus. 

    ![Oznámení z nasazení](./media/service-bus-resource-manager-namespace-queue/notification.png)
2. Potvrďte, že se v seznamu prostředků zobrazuje obor názvů služby Service Bus. 

    ![Skupina prostředků – obor názvů](./media/service-bus-resource-manager-namespace-queue/resource-group-namespace.png)
3. Vyberte obor názvů ze seznamu a zobcemte stránku **Obor názvů sběrnice služby.** 

## <a name="cleanup-resources"></a>Vyčištění prostředků

1. Na webu Azure Portal přejděte na stránku **Skupiny prostředků** pro vaši skupinu prostředků.
2. Na panelu nástrojů vyberte **Odstranit skupinu prostředků**. 
3. Zadejte název skupiny prostředků a vyberte **Odstranit**. 

    ![Skupina prostředků - odstranit](./media/service-bus-resource-manager-namespace-queue/resource-group-delete.png)

## <a name="next-steps"></a>Další kroky

Podívejte se na následující téma, které ukazuje, jak vytvořit autorizační pravidlo pro obor názvů nebo frontu:

[Vytvoření pravidla autorizace služby Service Bus pro obor názvů a frontu pomocí šablony Správce prostředků Azure](service-bus-resource-manager-namespace-auth-rule.md)

Přečtěte si, jak spravovat tyto prostředky v těchto článcích:

* [Správa služby Service Bus pomocí PowerShellu](service-bus-manage-with-ps.md)
* [Správa prostředků služby Service Bus pomocí Průzkumníka sběrnice](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Service Bus namespace and queue template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
