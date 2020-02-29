---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: d53c41752d57a27ebea9bd60f7e723dab1e7308a
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/29/2020
ms.locfileid: "78190835"
---
## <a name="run-the-function-locally"></a>Místní spuštění funkce

Spusťte funkci spuštěním místního hostitele modulu runtime Azure Functions ze složky *LocalFunctionProj* :

::: zone pivot="programming-language-csharp,programming-language-powershell,programming-language-javascript,programming-language-python"
```
func start
```
::: zone-end

::: zone pivot="programming-language-typescript"
```
npm install
npm start
```
::: zone-end

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
> Pokud se HttpExample nezobrazí, jak je uvedeno níže, pravděpodobně jste spustili hostitele ve složce *HttpExample* . V takovém případě pomocí **kombinace kláves Ctrl**+**C** zastavte hostitele, přejděte do nadřazené složky *LocalFunctionProj* a spusťte předchozí příkaz znovu.

Zkopírujte adresu URL funkce `HttpExample` z tohoto výstupu do prohlížeče a připojovat řetězec dotazu `?name=<your-name>`a provede úplnou adresu URL, jako je `http://localhost:7071/api/HttpExample?name=Functions`. V prohlížeči by se měla zobrazit zpráva, například `Hello Functions`:

![Výsledek funkce místně spuštěný v prohlížeči](./media/functions-run-function-test-local-cli/function-test-local-browser.png)

Terminál, ve kterém jste spustili `func start`, zobrazuje také výstup protokolu při provádění požadavků.

Až budete připraveni, použijte **Ctrl**+**C** a kliknutím na `y` zastavte hostitele Functions.