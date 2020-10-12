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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "67836822"
---
## <a name="prepare-your-repository"></a>Příprava úložiště

Chcete-li získat automatické sestavení ze serveru Azure App Service Kudu Build, ujistěte se, že kořenový adresář úložiště obsahuje ve vašem projektu správné soubory.

| Modul runtime | Soubory kořenového adresáře |
|-|-|
| ASP.NET (jenom Windows) | _*. sln_, _*. csproj_nebo _Default. aspx_ |
| Jádro ASP.NET | _*. sln_ nebo _*. csproj_ |
| PHP | _index. php_ |
| Ruby (jenom Linux) | _Gemfile_ |
| Node.js | _server.js_, _app.js_nebo _package.jsna_ pomocí spouštěcího skriptu |
| Python | _ \* . py_, _requirements.txt_nebo _runtime.txt_ |
| HTML | _default.htm_, _default.html_, _Default. ASP_, _index.htm_, _index.html_nebo _iisstart.htm_ |
| WebJobs | _\<job_name>/Run.\<extension>_ v _části \_ data aplikací/úlohy/nepřetržité_ pro nepřetržité webové úlohy nebo _ \_ data aplikací/úlohy/aktivované_ pro aktivované webové úlohy. Další informace najdete v [dokumentaci k WebJobs v Kudu](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Funkce | Viz [průběžné nasazování pro Azure Functions](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment). |

Chcete-li přizpůsobit nasazení, zahrňte soubor *. Deployment* do kořenového adresáře úložiště. Další informace najdete v tématu [přizpůsobení nasazení](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) a [vlastního skriptu nasazení](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Pokud vyvíjíte v aplikaci Visual Studio, nechte [Visual Studio vytvořit úložiště za vás](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio). Projekt je hned připravený k nasazení pomocí Gitu.
>

