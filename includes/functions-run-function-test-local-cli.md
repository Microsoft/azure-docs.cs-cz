---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: d400533039ea74a878cb8e543c22de02ee77e4f5
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106283248"
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

1. Zkopírujte adresu URL vaší `HttpExample` funkce z tohoto výstupu do prohlížeče a přidejte řetězec dotazu `?name=<YOUR_NAME>` , jako je třeba celá adresa URL `http://localhost:7071/api/HttpExample?name=Functions` . V prohlížeči by se měla zobrazit zpráva odpovědi, která vrací zpět hodnotu řetězce dotazu. Terminál, ve kterém jste spustili projekt, také zobrazuje výstup protokolu při provádění požadavků.

1. Až budete hotovi, použijte **CTRL** + **C** a vyberte možnost `y` zastavit hostitele funkcí.
