---
title: Rychlý Start – vytvoření fronty Service Bus pomocí Azure CLI | Microsoft Docs
description: V tomto rychlém startu se dozvíte, jak pomocí rozhraní příkazového řádku Azure vytvořit obor názvů Service Bus a potom do fronty v tomto oboru názvů.
author: spelluru
ms.topic: quickstart
ms.date: 08/12/2020
ms.author: spelluru
ms.openlocfilehash: 14892b114f3bb8a0bb6c07f7cdd01a174b931cf9
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95810633"
---
# <a name="use-the-azure-cli-to-create-a-service-bus-namespace-and-a-queue"></a>Vytvoření oboru názvů Service Bus a fronty pomocí rozhraní příkazového řádku Azure
V tomto rychlém startu se dozvíte, jak vytvořit obor názvů Service Bus a frontu pomocí rozhraní příkazového řádku Azure CLI. Také se dozvíte, jak získat autorizační přihlašovací údaje, které může klientská aplikace použít k odesílání a přijímání zpráv do fronty. 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="prerequisites"></a>Požadavky
Pokud ještě nemáte předplatné Azure, můžete si vytvořit [bezplatný účet][free account] před tím, než začnete.

V tomto rychlém startu použijete Azure Cloud Shell, které můžete spustit po přihlášení k Azure Portal. Podrobnosti o Azure Cloud Shell najdete v tématu [přehled Azure Cloud Shell](../cloud-shell/overview.md). Na svém počítači můžete také [nainstalovat](/cli/azure/install-azure-cli) a používat Azure PowerShell. 

## <a name="provision-resources"></a>Zřízení prostředků
1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
2. Azure Cloud Shell spustíte tak, že vyberete ikonu zobrazenou na následujícím obrázku. Přepněte do režimu **bash** , pokud je Cloud Shell v režimu **PowerShellu** . 

    :::image type="content" source="./media/service-bus-quickstart-powershell/launch-cloud-shell.png" alt-text="Spustit Cloud Shell":::
3. Spuštěním následujícího příkazu vytvořte skupinu prostředků Azure. Pokud chcete, aktualizujte název skupiny prostředků a její umístění. 

    ```azurecli-interactive
    az group create --name ContosoRG --location eastus
    ```
4. Spuštěním následujícího příkazu vytvořte obor názvů pro zasílání zpráv Service Bus.

    ```azurecli-interactive
    az servicebus namespace create --resource-group ContosoRG --name ContosoSBusNS --location eastus
    ```
5. Spuštěním následujícího příkazu vytvořte frontu v oboru názvů, který jste vytvořili v předchozím kroku. V tomto příkladu `ContosoRG` je skupina prostředků, kterou jste vytvořili v předchozím kroku. `ContosoSBusNS` je název oboru názvů Service Bus vytvořeného v této skupině prostředků. 

    ```azurecli-interactive
    az servicebus queue create --resource-group ContosoRG --namespace-name ContosoSBusNS --name ContosoOrdersQueue
    ```
6. Spusťte následující příkaz, který získá primární připojovací řetězec pro obor názvů. Tento připojovací řetězec slouží k připojení k frontě a posílání a přijímání zpráv. 

    ```azurecli-interactive
    az servicebus namespace authorization-rule keys list --resource-group ContosoRG --namespace-name ContosoSBusNS --name RootManageSharedAccessKey --query primaryConnectionString --output tsv    
    ```

    Poznamenejte si připojovací řetězec a název fronty. Slouží k odesílání a přijímání zpráv. 


## <a name="next-steps"></a>Další kroky
V tomto článku jste v oboru názvů vytvořili obor názvů Service Bus a frontu. Informace o tom, jak odesílat a přijímat zprávy z fronty, najdete v části v tématu **posílání a přijímání zpráv** v jednom z následujících rychlých startů. 

- [.NET](service-bus-dotnet-get-started-with-queues.md)
- [Java](service-bus-java-how-to-use-queues.md)
- [JavaScript](service-bus-nodejs-how-to-use-queues.md)
- [Python](service-bus-python-how-to-use-queues.md)
- [PHP](service-bus-php-how-to-use-queues.md)
- [Ruby](service-bus-ruby-how-to-use-queues.md)

[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

