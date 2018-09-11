---
title: zahrnout soubor
description: zahrnout soubor
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/05/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: c212bda3b59037f99139e02ee6adc63b0084cbe5
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44305610"
---
## <a name="prepare-your-repository"></a>Příprava úložiště

Získat automatické sestavení ze sestavovacího serveru App Service Kudu, ujistěte se, že kořenového adresáře úložiště má správné soubory ve vašem projektu.

| Modul runtime | Kořenový adresář souborů |
|-|-|
| Technologie ASP.NET (jenom Windows) | _*.sln_, _*.csproj_, nebo _default.aspx_ |
| Jádro ASP.NET | _*.sln_ nebo _*.csproj_ |
| PHP | _index.php_ |
| Ruby (pouze Linux) | _Gemfile_ |
| Node.js | _Server.js_, _app.js_, nebo _package.json_ s spouštěcí skript |
| Python (jenom Windows) | _\*.PY_, _souboru requirements.txt_, nebo _souboru runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_, nebo  _iisstart.htm_ |
| Webové úlohy | _\<hodnotu job_name > / run. \<rozšíření >_ pod _aplikace\_Data/úlohy/průběžné_ (pro průběžné WebJobs) nebo _aplikace\_Data/úlohy/triggered_ (pro aktivaci WebJobs). Další informace najdete v tématu [Kudu WebJobs – dokumentace](https://github.com/projectkudu/kudu/wiki/WebJobs) |
| Functions | Zobrazit [průběžné nasazování pro službu Azure Functions](../articles/azure-functions/functions-continuous-deployment.md#continuous-deployment-requirements). |

Chcete-li přizpůsobit nasazení, zahrňte _.deployment_ souboru v kořenovém adresáři úložiště. Další informace najdete v tématu [přizpůsobení nasazení](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) a [vlastní skript nasazení](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Pokud vyvíjíte v sadě Visual Studio, umožní [sady Visual Studio můžete vytvořit úložiště](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio). Projekt je okamžitě připravená k nasazení pomocí Gitu.
>
>

