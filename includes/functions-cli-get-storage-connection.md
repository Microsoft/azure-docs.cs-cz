---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 5cb345ef2d20f75066e90f9e6478be27f925b1b0
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673385"
---
### <a name="retrieve-the-azure-storage-connection-string"></a>Načtení připojovacího řetězce úložiště Azure

Dříve jste vytvořili účet Úložiště Azure pro použití aplikací funkce. Připojovací řetězec pro tento účet se bezpečně uchovává v nastavení aplikace v Azure. Stažením nastavení do souboru *local.settings.json* můžete toto připojení použít do fronty úložiště ve stejném účtu při místním spuštění funkce. 

1. V kořenovém adresáři projektu spusťte `<app_name>` následující příkaz, který nahradí název aplikace funkce z předchozího rychlého startu. Tento příkaz přepíše všechny existující hodnoty v souboru.

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. Otevřete *soubor local.settings.json* `AzureWebJobsStorage`a vyhledejte hodnotu s názvem , což je připojovací řetězec účtu úložiště. Název `AzureWebJobsStorage` a připojovací řetězec v jiných částech tohoto článku.

> [!IMPORTANT]
> Vzhledem k tomu, *že local.settings.json* obsahuje tajné klíče stažené z Azure, vždy vyloučit tento soubor ze správy zdrojového kódu. Soubor *.gitignore* vytvořený s projektem místních funkcí ve výchozím nastavení vyloučí soubor.