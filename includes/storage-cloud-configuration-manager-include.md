---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: cedfd719a5f0aeed6fc2e932d3aa5189b83c9796
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96025492"
---
[Knihovna správce konfigurace Microsoft Azure pro .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) poskytuje třídu pro potřeby analýzy připojovacího řetězce z konfiguračního souboru. Třída [CloudConfigurationManager](/previous-versions/azure/reference/mt634650(v=azure.100)) analyzuje nastavení konfigurace. Analyzuje nastavení pro klientské aplikace, které běží na ploše, na mobilním zařízení, na virtuálním počítači Azure nebo v cloudové službě Azure.

Chcete-li odkazovat na `CloudConfigurationManager` balíček, přidejte následující `using` direktivy:

```csharp
using Microsoft.Azure; //Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage;
```

Tady je příklad, který ukazuje, jak z konfiguračního souboru získat připojovací řetězec:

```csharp
// Parse the connection string and return a reference to the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
```

Použití nástroje Azure Configuration Manager není povinné. Můžete také použít rozhraní API, například [třídu ConfigurationManager](/dotnet/api/system.configuration.configurationmanager).NET Framework.