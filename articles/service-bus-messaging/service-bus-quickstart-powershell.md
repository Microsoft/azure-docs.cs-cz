---
title: Vytvoření fronty Service Bus pomocí Azure PowerShell
description: V tomto rychlém startu se dozvíte, jak vytvořit obor názvů Service Bus a frontu v oboru názvů pomocí Azure PowerShell.
author: spelluru
ms.author: spelluru
ms.date: 08/12/2020
ms.topic: quickstart
ms.devlang: dotnet
ms.custom:
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: f7bf9e5435b00ee3076422cccbe689038051499d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537095"
---
# <a name="use-azure-powershell-to-create-a-service-bus-namespace-and-a-queue"></a>Vytvoření oboru názvů Service Bus a fronty pomocí Azure PowerShell
V tomto rychlém startu se dozvíte, jak vytvořit obor názvů Service Bus a frontu pomocí Azure PowerShell. Také se dozvíte, jak získat autorizační přihlašovací údaje, které může klientská aplikace použít k odesílání a přijímání zpráv do fronty. 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]


## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu se ujistěte, že máte předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si vytvořit [bezplatný účet][] před tím, než začnete. 

V tomto rychlém startu použijete Azure Cloud Shell, které můžete spustit po přihlášení k Azure Portal. Podrobnosti o Azure Cloud Shell najdete v tématu [přehled Azure Cloud Shell](../cloud-shell/overview.md). Na svém počítači můžete také [nainstalovat](/powershell/azure/install-Az-ps) a používat Azure PowerShell. 


## <a name="provision-resources"></a>Zřízení prostředků
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Azure Cloud Shell spustíte tak, že vyberete ikonu zobrazenou na následujícím obrázku: 

    :::image type="content" source="./media/service-bus-quickstart-powershell/launch-cloud-shell.png" alt-text="Spustit Cloud Shell":::
3. V dolním Cloud Shell okně přepněte z **bash** do **PowerShellu**. 

    :::image type="content" source="./media/service-bus-quickstart-powershell/cloud-power-shell.png" alt-text="Přepnout do režimu PowerShellu":::    
4. Spuštěním následujícího příkazu vytvořte skupinu prostředků Azure. Pokud chcete, aktualizujte název skupiny prostředků a její umístění. 

    ```azurepowershell-interactive
    New-AzResourceGroup –Name ContosoRG –Location eastus
    ```
5. Spuštěním následujícího příkazu vytvořte obor názvů pro zasílání zpráv Service Bus. V tomto příkladu `ContosoRG` je skupina prostředků, kterou jste vytvořili v předchozím kroku. `ContosoSBusNS` je název oboru názvů Service Bus vytvořeného v této skupině prostředků. 

    ```azurepowershell-interactive
    New-AzServiceBusNamespace -ResourceGroupName ContosoRG -Name ContosoSBusNS -Location eastus
    ```
6. Spusťte následující příkaz, který vytvoří frontu v oboru názvů, který jste vytvořili v předchozím kroku. 

    ```azurepowershell-interactive
    New-AzServiceBusQueue -ResourceGroupName ContosoRG -NamespaceName ContosoSBusNS -Name ContosoOrdersQueue 
    ```
7. Získá primární připojovací řetězec pro obor názvů. Tento připojovací řetězec slouží k připojení k frontě a posílání a přijímání zpráv. 

    ```azurepowershell-interactive    
    Get-AzServiceBusKey -ResourceGroupName ContosoRG -Namespace ContosoSBusNS -Name RootManageSharedAccessKey
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

[bezplatný účet]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
