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
ms.openlocfilehash: 1c2a4f1e463fff278981de2297662a94cca8944e
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850782"
---
## <a name="prepare-your-repository"></a>Příprava úložiště

Automatické sestavení získat ze serveru aplikace služby Kudu sestavení, ujistěte se, že kořenového adresáře úložiště má správné soubory v projektu.

| Modul runtime | Soubory kořenového adresáře |
|-|-|
| ASP.NET (jenom Windows) | _*.sln_, _*.csproj_, nebo _default.aspx_ |
| Jádro ASP.NET | _*.sln_ nebo _*.csproj_ |
| PHP | _index.php_ |
| Ruby (pouze Linux) | _Gemfile_ |
| Node.js | _Server.js_, _app.js_, nebo _package.json_ pomocí spuštění skriptu |
| Python (jenom Windows) | _\*.PY_, _requirements.txt_, nebo _souboru runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_, nebo  _iisstart.htm_ |
| Webové úlohy | _\<job_name > / run. \<rozšíření >_ pod _aplikace\_Data/úlohy/průběžné_ (pro nepřetržité webové úlohy) nebo _aplikace\_dat, úlohy nebo aktivaci_ (pro aktivaci Webové úlohy). Další informace najdete v tématu [Kudu WebJobs dokumentace](https://github.com/projectkudu/kudu/wiki/WebJobs) |
| Functions | V tématu [průběžné nasazování pro Azure Functions](../articles/azure-functions/functions-continuous-deployment.md#continuous-deployment-requirements). |

Přizpůsobit vaše nasazení, zahrňte _.deployment_ soubor v kořenovém adresáři úložiště. Další informace najdete v tématu [přizpůsobení nasazení](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) a [vlastní skript nasazení](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Pokud vyvíjíte v sadě Visual Studio, umožní [Visual Studio pro vás vytvořil úložiště](/vsts/git/tutorial/creatingrepo?view=vsts&tabs=visual-studio). Projekt je okamžitě připravená k nasazení pomocí Git.
>
>

