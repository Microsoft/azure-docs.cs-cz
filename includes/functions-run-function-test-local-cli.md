---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 11d426016cfe1a8a9ff843da518f57c08881be5d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96842292"
---
## <a name="run-the-function-locally"></a>Místní spuštění funkce

1. Spusťte funkci spuštěním místního hostitele modulu runtime Azure Functions ze složky *LocalFunctionProj* :

    ```
    func start
    ```

    Ke konci výstupu by se měly zobrazit následující řádky: 
    
    <pre>
    ...
    
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    Http Functions:
    
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    
    </pre>
    
    >[!NOTE]  
    > Pokud se HttpExample nezobrazí, jak je uvedeno výše, pravděpodobně jste spustili hostitele mimo kořenovou složku projektu. V takovém případě pomocí **kombinace kláves CTRL +** + **C** zastavte hostitele, přejděte do kořenové složky projektu a spusťte předchozí příkaz znovu.

1. Zkopírujte adresu URL vaší `HttpExample` funkce z tohoto výstupu do prohlížeče a přidejte řetězec dotazu `?name=<YOUR_NAME>` , jako je třeba celá adresa URL `http://localhost:7071/api/HttpExample?name=Functions` . V prohlížeči by se měla zobrazit zpráva jako `Hello Functions` :

    ![Výsledek funkce místně spuštěný v prohlížeči](./media/functions-run-function-test-local-cli/function-test-local-browser.png)

1. Terminál, ve kterém jste spustili projekt, také zobrazuje výstup protokolu při provádění požadavků.

1. Až budete hotovi, použijte **CTRL** + **C** a vyberte možnost `y` zastavit hostitele funkcí.
