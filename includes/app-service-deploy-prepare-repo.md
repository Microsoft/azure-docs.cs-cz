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
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/26/2020
ms.locfileid: "67836822"
---
## <a name="prepare-your-repository"></a>Příprava úložiště

Chcete-li získat automatické sestavení ze serveru Azure App Service Kudu Build, ujistěte se, že kořenový adresář úložiště obsahuje ve vašem projektu správné soubory.

| Modul runtime | Soubory kořenového adresáře |
|-|-|
| ASP.NET (jenom Windows) | _*. sln_, _*. csproj_nebo _Default. aspx_ |
| ASP.NET Core | _*. sln_ nebo _*. csproj_ |
| PHP | _index. php_ |
| Ruby (jenom Linux) | _Gemfile_ |
| Node.js | _Server. js_, _App. js_nebo _Package. JSON_ s úvodním skriptem |
| Python | . py, _požadavky. txt_nebo _runtime. txt_ _ \*_ |
| HTML | _Default. htm_, _Default. html_, _Default. ASP_, _index. htm_, _index. html_nebo _soubor Iisstart. htm_ |
| Webové úlohy | _\<job_name>/run. rozšíření \<>_ v _části\_data aplikací/úlohy/průběžné_ pro nepřetržité webové úlohy nebo _data aplikací\_/úlohy/aktivované_ pro aktivované webové úlohy. Další informace najdete v [dokumentaci k WebJobs v Kudu](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Functions | Viz [průběžné nasazování pro Azure Functions](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment). |

Chcete-li přizpůsobit nasazení, zahrňte soubor *. Deployment* do kořenového adresáře úložiště. Další informace najdete v tématu [přizpůsobení nasazení](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) a [vlastního skriptu nasazení](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Pokud vyvíjíte v aplikaci Visual Studio, nechte [Visual Studio vytvořit úložiště za vás](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio). Projekt je hned připravený k nasazení pomocí Gitu.
>

