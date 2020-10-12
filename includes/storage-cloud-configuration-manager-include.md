---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 5102866cdda51ef545fd95b32946cb17c6e40a3c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "72038165"
---
[Knihovna správce konfigurace Microsoft Azure pro .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) poskytuje třídu pro potřeby analýzy připojovacího řetězce z konfiguračního souboru. Třída [CloudConfigurationManager](https://msdn.microsoft.com/library/azure/mt634650.aspx) analyzuje nastavení konfigurace. Analyzuje nastavení pro klientské aplikace, které běží na ploše, na mobilním zařízení, na virtuálním počítači Azure nebo v cloudové službě Azure.

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
