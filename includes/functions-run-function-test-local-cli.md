---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: 749b733039e89421ac33ef76a11f3291b296e718
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673186"
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

::: zone pivot="programming-language-java"
```
mvn clean package 
mvn azure-functions:run
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
> Pokud httpexample nezobrazí, jak je uvedeno níže, pravděpodobně jste spustili hostitele mimo kořenovou složku projektu. V takovém případě pomocí **klávesy Ctrl**+**C** zastavte hostitele, přejděte do kořenové složky projektu a znovu spusťte předchozí příkaz.

Zkopírujte adresu `HttpExample` URL funkce z tohoto výstupu do prohlížeče `?name=<your-name>`a přidejte `http://localhost:7071/api/HttpExample?name=Functions`řetězec dotazu a změte úplnou adresu URL jako . Prohlížeč by měl zobrazit `Hello Functions`zprávu jako :

![Výsledek funkce spuštěné místně v prohlížeči](./media/functions-run-function-test-local-cli/function-test-local-browser.png)

Terminál, ve kterém jste projekt spustili, také zobrazuje výstup protokolu při vyřizování požadavků.

Až budete připraveni, použijte **kombinaci kláves Ctrl**+**C** a zvolte `y` zastavení hostitele funkcí.