---
title: zahrnout soubor
description: zahrnout soubor
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: add0d392f39ab476c6d75f704d5b2e2e0faaa77c
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836822"
---
## <a name="prepare-your-repository"></a>Příprava úložiště

Získat automatické sestavení ze sestavení serveru Azure App Service Kudu, ujistěte se, že kořenového adresáře úložiště má správné soubory ve vašem projektu.

| Modul runtime | Kořenový adresář souborů |
|-|-|
| Technologie ASP.NET (jenom Windows) | _*.sln_, _*.csproj_, nebo _default.aspx_ |
| ASP.NET Core | _*.sln_ nebo _*.csproj_ |
| PHP | _index.php_ |
| Ruby (pouze Linux) | _Gemfile_ |
| Node.js | _Server.js_, _app.js_, nebo _package.json_ s spouštěcí skript |
| Python | _\*.PY_, _souboru requirements.txt_, nebo _souboru runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_, nebo  _iisstart.htm_ |
| Webové úlohy | _\<hodnotu job_name > / run. \<rozšíření >_ pod _aplikace\_Data/úlohy/průběžné_ pro nepřetržité webové úlohy, nebo _aplikace\_Data/úlohy/triggered_ pro aktivaci WebJobs. Další informace najdete v tématu [Kudu WebJobs – dokumentace](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Funkce | Zobrazit [průběžné nasazování pro službu Azure Functions](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment). |

Chcete-li přizpůsobit nasazení, zahrňte *.deployment* souboru v kořenovém adresáři úložiště. Další informace najdete v tématu [přizpůsobení nasazení](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) a [vlastní skript nasazení](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Pokud vyvíjíte v sadě Visual Studio, umožní [sady Visual Studio můžete vytvořit úložiště](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio). Projekt je okamžitě připravená k nasazení pomocí Gitu.
>

