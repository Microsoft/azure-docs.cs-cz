---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: 55c64048e0604987c5a4c26961e5617106358e76
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "84436170"
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

::: zone pivot="programming-language-java"
```
mvn clean package 
mvn azure-functions:run
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
> Pokud se HttpExample nezobrazí, jak je uvedeno níže, pravděpodobně jste spustili hostitele mimo kořenovou složku projektu. V takovém případě pomocí **kombinace kláves CTRL +** + **C** zastavte hostitele, přejděte do kořenové složky projektu a spusťte předchozí příkaz znovu.

Zkopírujte adresu URL vaší `HttpExample` funkce z tohoto výstupu do prohlížeče a přidejte řetězec dotazu `?name=<your-name>` , jako je třeba celá adresa URL `http://localhost:7071/api/HttpExample?name=Functions` . V prohlížeči by se měla zobrazit zpráva jako `Hello Functions` :

![Výsledek funkce místně spuštěný v prohlížeči](./media/functions-run-function-test-local-cli/function-test-local-browser.png)

Terminál, ve kterém jste spustili projekt, také zobrazuje výstup protokolu při provádění požadavků.

Až budete připraveni, použijte **CTRL** + **C** a vyberte možnost `y` zastavit hostitele funkcí.
