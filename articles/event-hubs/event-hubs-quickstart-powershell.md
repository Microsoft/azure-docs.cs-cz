---
title: Rychlý start Azure – Zpracování streamů událostí pomocí PowerShellu | Microsoft Docs
description: Tento rychlý start popisuje, jak odesílat a přijímat události služby Azure Event Hubs pomocí PowerShellu a ukázkové aplikace .NET.
services: event-hubs
author: sethmanheim
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 06/26/2018
ms.author: sethm
ms.openlocfilehash: 9216372038db7a6f97cfc8034f715b34de08d83c
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37132176"
---
# <a name="quickstart-process-event-streams-using-powershell-and-net-standard"></a>Rychlý start: Zpracování streamů událostí pomocí PowerShellu a .NET Standard

Azure Event Hubs je vysoce škálovatelná platforma pro streamování dat a služba pro ingestování, která je schopná přijmout a zpracovat miliony událostí za sekundu. V tomto rychlém startu se dozvíte, jak pomocí PowerShellu vytvořit centrum událostí a pak do centra událostí odesílat a přijímat z něj události pomocí sady .NET Standard SDK.

K dokončení tohoto rychlého startu potřebujete předplatné Azure. Pokud ho nemáte, vytvořte si [bezplatný účet][] před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento kurz, ujistěte se, že máte následující:

- [Visual Studio 2017 s aktualizací Update 3 (verze 15.3, 26730.01)](http://www.visualstudio.com/vs) nebo novější.
- [NET Standard SDK](https://www.microsoft.com/net/download/windows) verze 2.0 nebo novější.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud používáte PowerShell místně, k dokončení tohoto rychlého startu je potřeba, abyste měli nejnovější verzi PowerShellu. Pokud PowerShell potřebujete nainstalovat nebo upgradovat, přečtěte si téma [Instalace a konfigurace Azure PowerShellu](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0).

## <a name="provision-resources"></a>Zřízení prostředků

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logická kolekce prostředků Azure, kterou potřebujete k vytvoření centra událostí. 

Následující příklad vytvoří skupinu prostředků v oblasti USA – východ. Nahraďte `myResourceGroup` názvem skupiny prostředků, kterou chcete použít:

```azurepowershell-interactive
New-AzureRmResourceGroup –Name myResourceGroup –Location eastus
```

### <a name="create-an-event-hubs-namespace"></a>Vytvoření oboru názvů služby Event Hubs

Po vytvoření skupiny prostředků v ní vytvořte obor názvů služby Event Hubs. Obor názvů služby Event Hubs poskytuje jedinečný plně kvalifikovaný název domény, ve které můžete vytvořit centrum událostí. Nahraďte `namespace_name` jedinečným názvem vašeho oboru názvů:

```azurepowershell-interactive
New-AzureRmEventHubNamespace -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Location eastus
```

### <a name="create-an-event-hub"></a>Vytvoření centra událostí

Teď, když máte obor názvů služby Event Hubs, v něm vytvořte centrum událostí:

```azurepowershell-interactive
New-AzureRmEventHub -ResourceGroupName myResourceGroup -NamespaceName namespace_name -EventHubName eventhub_name
```

### <a name="create-a-storage-account-for-event-processor-host"></a>Vytvoření účtu úložiště pro třídu Event Processor Host

Event Processor Host zjednodušuje příjem událostí ze služby Event Hubs tím, že spravuje kontrolní body a paralelní přijímače. K vytváření kontrolních bodů vyžaduje Event Processor Host účet úložiště. Spuštěním následujících příkazů vytvoříte účet úložiště a získáte jeho klíče:

```azurepowershell-interactive
# Create a standard general purpose storage account 
New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name storage_account_name -Location eastus -SkuName Standard_LRS 
e
# Retrieve the storage account key for accessing it
Get-AzureRmStorageAccountKey -ResourceGroupName myResourceGroup -Name storage_account_name
```

### <a name="get-the-connection-string"></a>Získání připojovacího řetězce

Připojovací řetězec je potřeba pro připojení k centru událostí a zpracování událostí. Připojovací řetězec získáte spuštěním:

```azurepowershell-interactive
Get-AzureRmEventHubKey -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Name RootManageSharedAccessKey
```

## <a name="stream-into-event-hubs"></a>Streamování do služby Event Hubs

Teď můžete začít streamovat do služby Event Hubs. Ukázky si můžete stáhnout nebo naklonovat přes Git z [úložiště Event Hubs](https://github.com/Azure/azure-event-hubs).

### <a name="ingest-events"></a>Ingestování událostí

Pokud chcete začít streamovat události, stáhněte si z GitHubu ukázku [SampleSender](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender) nebo pomocí následujícího příkazu naklonujte [úložiště Event Hubs na GitHubu](https://github.com/Azure/azure-event-hubs):

```bash
git clone https://github.com/Azure/azure-event-hubs.git
```

Přejděte do složky \azure-event-hubs\samples\DotNet\Microsoft.Azure.EventHubs\SampleSender a načtěte soubor SampleSender.sln do sady Visual Studio.

Dále do projektu přidejte balíček NuGet [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/).

V souboru Program.cs nahraďte následující zástupné hodnoty názvem vašeho centra událostí a připojovacím řetězcem:

```C#
private const string EhConnectionString = "Event Hubs connection string";
private const string EhEntityPath = "Event Hub name";

```

Teď ukázku sestavte a spusťte. Uvidíte, jak se události ingestují do vašeho centra událostí:

![][3]

### <a name="receive-and-process-events"></a>Příjem a zpracování událostí

Teď si stáhněte ukázku přijímače Event Processor Host, který bude přijímat právě odeslané zprávy. Stáhněte si z GitHubu ukázku [SampleEphReceiver](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) nebo pomocí následujícího příkazu naklonujte [úložiště Event Hubs na GitHubu](https://github.com/Azure/azure-event-hubs):

```bash
git clone https://github.com/Azure/azure-event-hubs.git
```

Přejděte do složky \azure-event-hubs\samples\DotNet\Microsoft.Azure.EventHubs\SampleEphReceiver a načtěte soubor řešení SampleEphReceiver.sln do sady Visual Studio.

Dále do projektu přidejte balíčky NuGet [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) and [Microsoft.Azure.EventHubs.Processor](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/).

V souboru Program.cs nahraďte následující konstanty odpovídajícími hodnotami:

```C#
private const string EventHubConnectionString = "Event Hubs connection string";
private const string EventHubName = "Event Hub name";
private const string StorageContainerName = "Storage account container name";
private const string StorageAccountName = "Storage account name";
private const string StorageAccountKey = "Storage account key";
```

Teď ukázku sestavte a spusťte. Uvidíte, jak vaše ukázková aplikace přijímá události:

![][4]

Na webu Azure Portal můžete pro daný obor názvů služby Event Hubs zobrazit rychlost zpracovávání událostí, jak je znázorněno níže:

![][5]

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení tohoto rychlého startu můžete odstranit skupinu prostředků a obor názvů, účet úložiště a centrum událostí, které obsahuje. Nahraďte `myResourceGroup` názvem skupiny prostředků, kterou jste vytvořili. 

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořil obor názvů služby Event Hubs a další prostředky potřebné k odesílání a přijímání událostí z centra událostí. Další informace najdete v následujícím kurzu:

> [!div class="nextstepaction"]
> [Vizualizace datových anomálií v datových streamech Event Hubs](event-hubs-tutorial-visualize-anomalies.md)

[bezplatný účet]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install and Configure Azure PowerShell]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[New-AzureRmResourceGroup]: https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png
