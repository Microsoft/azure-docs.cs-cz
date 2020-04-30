---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 02/09/2020
ms.author: glenga
ms.openlocfilehash: 749b733039e89421ac33ef76a11f3291b296e718
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "80673186"
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
> Pokud se HttpExample nezobrazí, jak je uvedeno níže, pravděpodobně jste spustili hostitele mimo kořenovou složku projektu. V takovém případě pomocí **kombinace kláves CTRL +**+**C** zastavte hostitele, přejděte do kořenové složky projektu a spusťte předchozí příkaz znovu.

Zkopírujte adresu URL vaší `HttpExample` funkce z tohoto výstupu do prohlížeče a přidejte řetězec `?name=<your-name>`dotazu, jako je třeba `http://localhost:7071/api/HttpExample?name=Functions`celá adresa URL. V prohlížeči by se měla zobrazit zpráva `Hello Functions`jako:

![Výsledek funkce místně spuštěný v prohlížeči](./media/functions-run-function-test-local-cli/function-test-local-browser.png)

Terminál, ve kterém jste spustili projekt, také zobrazuje výstup protokolu při provádění požadavků.

Až budete připraveni, použijte **CTRL**+**C** a vyberte možnost `y` zastavit hostitele funkcí.