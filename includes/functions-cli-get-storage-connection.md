---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 5cb345ef2d20f75066e90f9e6478be27f925b1b0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "80673385"
---
### <a name="retrieve-the-azure-storage-connection-string"></a>Načtení připojovacího řetězce Azure Storage

Dříve jste vytvořili účet Azure Storage, který bude používat aplikace Function App. Připojovací řetězec pro tento účet je bezpečně uložený v nastavení aplikace v Azure. Stažením nastavení do *local.settings.jsv* souboru můžete toto připojení použít do fronty úložiště ve stejném účtu, když se funkce místně spustí. 

1. Z kořenového adresáře projektu spusťte následující příkaz a nahraďte `<app_name>` názvem vaší aplikace Function za předchozím rychlým startem. Tento příkaz přepíše všechny existující hodnoty v souboru.

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. Otevřete *local.settings.js* a vyhledejte hodnotu s názvem `AzureWebJobsStorage` , což je připojovací řetězec účtu úložiště. Název `AzureWebJobsStorage` a připojovací řetězec použijete v dalších částech tohoto článku.

> [!IMPORTANT]
> Vzhledem k tomu, že *local.settings.jsna* obsahuje tajné kódy stažené z Azure, tento soubor vždycky vylučte ze správy zdrojového kódu. Soubor *. gitignore* vytvořený pomocí projektu místní funkce vyloučí soubor ve výchozím nastavení.