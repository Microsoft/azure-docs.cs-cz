---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: d53c41752d57a27ebea9bd60f7e723dab1e7308a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190835"
---
## <a name="run-the-function-locally"></a>Místní spuštění funkce

Spusťte svou funkci spuštěním místního hostitele runtime Azure Functions ze složky *LocalFunctionProj:*

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

Na konci výstupu by se měly objevit následující řádky: 

<pre>
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

        HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...

</pre>

>[!NOTE]  
> Pokud httpexample nezobrazí, jak je uvedeno níže, pravděpodobně jste spustili hostitele z ve složce *HttpExample.* V takovém případě použijte **kombinaci kláves**+**C** k zastavení hostitele, přejděte do nadřazené složky *LocalFunctionProj* a znovu spusťte předchozí příkaz.

Zkopírujte adresu `HttpExample` URL funkce z tohoto výstupu do prohlížeče `?name=<your-name>`a přidejte `http://localhost:7071/api/HttpExample?name=Functions`řetězec dotazu a změte úplnou adresu URL jako . Prohlížeč by měl zobrazit `Hello Functions`zprávu jako :

![Výsledek funkce spuštěné místně v prohlížeči](./media/functions-run-function-test-local-cli/function-test-local-browser.png)

Terminál, ve kterém `func start` jste spustili také zobrazuje výstup protokolu, jak si dělat požadavky.

Až budete připraveni, použijte **kombinaci kláves Ctrl**+**C** a zvolte `y` zastavení hostitele funkcí.