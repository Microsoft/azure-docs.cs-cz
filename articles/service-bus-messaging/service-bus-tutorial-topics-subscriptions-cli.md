---
title: Použití rozhraní příkazového řádku Azure k vytvoření Service Bus témat a předplatných
description: V tomto rychlém startu se dozvíte, jak vytvořit Service Bus téma a odběry k tomuto tématu pomocí Azure CLI.
ms.date: 06/23/2020
ms.topic: quickstart
author: spelluru
ms.author: spelluru
ms.openlocfilehash: 080b089efa276779420f6d9bc8e76272f1e65788
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2020
ms.locfileid: "90069692"
---
# <a name="use-azure-cli-to-create-a-service-bus-topic-and-subscriptions-to-the-topic"></a>Použití rozhraní příkazového řádku Azure k vytvoření Service Busho tématu a odběrů tématu
V tomto rychlém startu pomocí Azure CLI vytvoříte Service Bus téma a pak pro toto téma vytvoříte odběry. 

## <a name="what-are-service-bus-topics-and-subscriptions"></a>Co jsou témata a předplatné služby Service Bus?
Témata a předplatné služby Service Bus podporují komunikační model zasílání zpráv *publikování/přihlášení*. Součásti distribuované aplikace při používání témat a předplatných nekomunikují navzájem přímo. Místo toho si zprávy vyměňují prostřednictvím tématu, které slouží jako zprostředkovatel.

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

Na rozdíl od Service Busch front, ve kterých každá zpráva zpracovává jeden uživatel, témata a odběry poskytují formu komunikace 1: n pomocí vzoru pro publikování a odběr. K jednomu tématu můžete zaregistrovat několik předplatných. Při odeslání zprávy do tématu bude zpráva zpřístupněná všem předplatným, aby ji nezávisle zpracovala. Předplatné tématu se podobá virtuální frontě, která obdrží kopii zpráv, které byly odeslány do tématu. Volitelně můžete zaregistrovat pravidla filtru pro téma na základě jednotlivých předplatných, což umožňuje filtrovat nebo omezit, které zprávy na téma jsou přijímány v rámci předplatných tématu.

Service Bus témata a předplatná umožňují škálovat na zpracování velkého počtu zpráv napříč velkým počtem uživatelů a aplikací.

## <a name="prerequisites"></a>Předpoklady
Pokud ještě nemáte předplatné Azure, můžete si vytvořit [bezplatný účet][free account] před tím, než začnete.

V tomto rychlém startu použijete Azure Cloud Shell, které můžete spustit po přihlášení k Azure Portal. Podrobnosti o Azure Cloud Shell najdete v tématu [přehled Azure Cloud Shell](../cloud-shell/overview.md). Na svém počítači můžete také [nainstalovat](/cli/azure/install-azure-cli) a používat Azure PowerShell. 

## <a name="create-a-service-bus-topic-and-subscriptions"></a>Vytvoření tématu Service Busu a odběrů
Každý [odběr tématu](service-bus-messaging-overview.md#topics) může přijímat kopie všech zpráv. Témata jsou co do protokolu a sémantiky plně kompatibilní s frontami služby Service Bus. Témata služby Service Bus podporují širokou škálu pravidel pro výběr s podmínkami filtrů, včetně volitelných akcí, kterými se nastavují nebo upravují vlastnosti zprávy. Při každé shodě s pravidlem se vytvoří zpráva. Další informace o pravidlech, filtrech a akcích získáte pomocí tohoto [odkazu](topic-filters.md).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Azure Cloud Shell spustíte tak, že vyberete ikonu zobrazenou na následujícím obrázku. Přepněte do režimu **bash** , pokud je Cloud Shell v režimu **PowerShellu** . 

    :::image type="content" source="./media/service-bus-quickstart-powershell/launch-cloud-shell.png" alt-text="Spustit Cloud Shell":::
3. Spuštěním následujícího příkazu vytvořte skupinu prostředků Azure. Pokud chcete, aktualizujte název skupiny prostředků a její umístění. 

    ```azurecli-interactive
    az group create --name MyResourceGroup --location eastus
    ```
4. Spuštěním následujícího příkazu vytvořte obor názvů pro zasílání zpráv Service Bus. Aktualizujte název oboru názvů tak, aby byl jedinečný. 

    ```azurecli-interactive
    namespaceName=MyNameSpace$RANDOM
    az servicebus namespace create --resource-group MyResourceGroup --name $namespaceName --location eastus
    ```
5. Spuštěním následujícího příkazu vytvořte téma v oboru názvů. 

    ```azurecli-interactive
    az servicebus topic create --resource-group MyResourceGroup   --namespace-name $namespaceName --name MyTopic
    ```
6. Vytvoření prvního odběru tématu
    
    ```azurecli-interactive
    az servicebus topic subscription create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --name S1    
    ```
6. Vytvoření druhého předplatného pro téma
    
    ```azurecli-interactive
    az servicebus topic subscription create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --name S2    
    ```
6. Vytvoření třetího předplatného pro téma
    
    ```azurecli-interactive
    az servicebus topic subscription create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --name S3    
    ```
7. Vytvoří filtr pro první odběr pomocí filtru s použitím vlastních vlastností ( `StoreId` je jedním z `Store1` , `Store2` , a `Store3` ).

    ```azurecli-interactive
    az servicebus topic subscription rule create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --subscription-name S1 --name MyFilter --filter-sql-expression "StoreId IN ('Store1','Store2','Store3')"    
    ```
8. Vytvoření filtru u druhého předplatného pomocí filtru pomocí vlastností Customer Customer ( `StoreId = Store4` )

    ```azurecli-interactive
    az servicebus topic subscription rule create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S2 --name MySecondFilter --filter-sql-expression "StoreId = 'Store4'"    
    ```
9. Vytvořte filtr pro třetí odběr pomocí filtru pomocí vlastností zákazníka ( `StoreId` nikoli v `Store1` , `Store2` , `Store3` nebo `Store4` ).

    ```azurecli-interactive
    az servicebus topic subscription rule create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --subscription-name S3 --name MyThirdFilter --filter-sql-expression "StoreId NOT IN ('Store1','Store2','Store3', 'Store4')"     
    ```
10. Spusťte následující příkaz, který získá primární připojovací řetězec pro obor názvů. Tento připojovací řetězec slouží k připojení k frontě a posílání a přijímání zpráv. 

    ```azurecli-interactive
    az servicebus namespace authorization-rule keys list --resource-group MyResourceGroup --namespace-name $namespaceName --name RootManageSharedAccessKey --query primaryConnectionString --output tsv    
    ```

    Poznamenejte si připojovací řetězec a název tématu. Slouží k odesílání a přijímání zpráv. 
    

## <a name="next-steps"></a>Další kroky
Informace o tom, jak odesílat zprávy do tématu a přijímat tyto zprávy prostřednictvím předplatného, najdete v následujícím článku: vyberte programovací jazyk v obsahu. 

> [!div class="nextstepaction"]
> [Publikování a přihlášení k odběru zpráv](service-bus-dotnet-how-to-use-topics-subscriptions.md)


[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Install the Azure CLI]: /cli/azure/install-azure-cli
[az group create]: /cli/azure/group#az_group_create
