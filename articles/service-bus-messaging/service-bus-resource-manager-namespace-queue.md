---
title: Vytvoření Azure Service Busého oboru názvů a fronty pomocí šablony Azure
description: 'Rychlý Start: vytvoření oboru názvů Service Bus a fronty pomocí šablony Azure Resource Manager'
documentationcenter: .net
author: spelluru
ms.topic: quickstart
ms.custom: subject-armqs
ms.tgt_pltfrm: dotnet
ms.date: 06/23/2020
ms.author: spelluru
ms.openlocfilehash: 69cffb6000df9d8b058d92231c130ea8a601e6d2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88660627"
---
# <a name="quickstart-create-a-service-bus-namespace-and-a-queue-using-an-arm-template"></a>Rychlý Start: vytvoření oboru názvů Service Bus a fronty pomocí šablony ARM

Tento článek ukazuje, jak použít šablonu Azure Resource Manager (šablonu ARM), která vytvoří obor názvů Service Bus a frontu v rámci tohoto oboru názvů. V tomto článku se dozvíte, jak určit, které prostředky se nasazují a jak definovat parametry, které jsou zadané při spuštění nasazení. Tuto šablonu můžete použít pro vlastní nasazení nebo ji upravit, aby splňovala vaše požadavky.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/201-servicebus-create-queue).

:::code language="json" source="~/quickstart-templates/201-servicebus-create-queue/azuredeploy.json":::

Mezi prostředky, které jsou definované v šabloně, patří:

- [**Microsoft. ServiceBus/obory názvů**](/azure/templates/microsoft.servicebus/namespaces)
- [**Microsoft. ServiceBus/obory názvů/fronty**](/azure/templates/microsoft.servicebus/namespaces/queues)

> [!NOTE]
> Pro stažení a nasazení jsou k dispozici následující šablony ARM.
>
> * [Vytvoření oboru názvů Service Bus s použitím fronty a autorizačního pravidla](service-bus-resource-manager-namespace-auth-rule.md)
> * [Vytvoření oboru názvů Service Bus s tématem a předplatným](service-bus-resource-manager-namespace-topic.md)
> * [Vytvoření oboru názvů Service Busu](service-bus-resource-manager-namespace.md)
> * [Vytvoření oboru názvů Service Bus s použitím tématu, předplatného a pravidla](service-bus-resource-manager-namespace-topic-with-rule.md)

Další šablonu můžete najít v [šablonách rychlého startu Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular) .

## <a name="deploy-the-template"></a>Nasazení šablony

Pomocí této šablony nasadíte obor názvů Service Bus s frontou.

[Fronty Service Bus](service-bus-queues-topics-subscriptions.md#queues) nabízejí doručování zpráv first in, First out (FIFO) do jednoho nebo více konkurenčních zákazníků.

Pokud chcete nasazení spustit automaticky, klikněte na následující tlačítko: Vytvořte novou skupinu prostředků pro nasazení, abyste se později mohli snadno vyčistit.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="validate-the-deployment"></a>Ověření nasazení

1. V horní části vyberte **oznámení** , aby se zobrazil stav nasazení. Počkejte, dokud nebude nasazení úspěšné. Pak vyberte **Přejít do skupiny prostředků** v oznamovací zprávě a přejděte na stránku pro skupinu prostředků, která obsahuje obor názvů Service Bus. 

    ![Oznámení z nasazení](./media/service-bus-resource-manager-namespace-queue/notification.png)
2. Ověřte, že se v seznamu prostředků zobrazuje obor názvů Service Bus. 

    ![Skupina prostředků – obor názvů](./media/service-bus-resource-manager-namespace-queue/resource-group-namespace.png)
3. Vyberte obor názvů ze seznamu, aby se zobrazila stránka **Service Bus obor názvů** . 

## <a name="clean-up-resources"></a>Vyčištění prostředků

1. V Azure Portal přejděte na stránku **skupiny prostředků** pro vaši skupinu prostředků.
2. Na panelu nástrojů vyberte **Odstranit skupinu prostředků**. 
3. Zadejte název skupiny prostředků a vyberte **Odstranit**. 

    ![Skupina prostředků – odstranit](./media/service-bus-resource-manager-namespace-queue/resource-group-delete.png)

## <a name="next-steps"></a>Další kroky

Podívejte se na následující téma, kde se dozvíte, jak vytvořit autorizační pravidlo pro obor názvů nebo frontu:

[Vytvoření autorizačního pravidla Service Bus pro obor názvů a frontu pomocí šablony ARM](service-bus-resource-manager-namespace-auth-rule.md)

Naučte se spravovat tyto prostředky zobrazením těchto článků:

* [Správa služby Service Bus pomocí PowerShellu](service-bus-manage-with-ps.md)
* [Správa prostředků Service Bus pomocí Průzkumníka Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Service Bus namespace and queue template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/management/manage-resources-powershell.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/management/manage-resources-cli.md