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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67836822"
---
## <a name="prepare-your-repository"></a>Příprava úložiště

Chcete-li získat automatické sestavení ze serveru sestavení Služby Azure App Service Kudu, ujistěte se, že kořen úložiště má ve vašem projektu správné soubory.

| Modul runtime | Kořenové soubory adresářů |
|-|-|
| ASP.NET (pouze windows) | _*.sln_, _*.csproj_nebo _default.aspx_ |
| ASP.NET Core | _*.sln_ nebo _*.csproj_ |
| PHP | _Index.php_ |
| Ruby (pouze Linux) | _Gemfile_ |
| Node.js | _server.js_, _app.js_nebo _package.json_ se skriptem start |
| Python | .py , _requirements.txt_nebo _runtime.txt_ _ \*_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_nebo _iisstart.htm_ |
| Webové úlohy | _\<job_name>/běh. rozšíření \<>_ v části _\_Data/úlohy/souvislé_ pro nepřetržité webové úlohy nebo _Data/úlohy aplikací/aktivované\__ pro aktivované webové úlohy. Další informace naleznete v [dokumentaci k kudu webjobs](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Funkce | Viz [Průběžné nasazení pro funkce Azure](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment). |

Chcete-li nasazení přizpůsobit, zahrňte soubor *.deployment* do kořenového adresáře úložiště. Další informace naleznete v [tématu Customize deployments](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) and [Custom deployment script](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Pokud vyvíjíte v sadě Visual Studio, nechte [Visual Studio vytvořit úložiště pro vás](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio). Projekt je okamžitě připraven k nasazení pomocí Gitu.
>

