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
ms.openlocfilehash: f783245b17a880a67b62aa24a3feabb9b113053b
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102122251"
---
## <a name="prepare-your-repository"></a>Příprava úložiště

Chcete-li získat automatizované buildy z Azure App Service Server sestavení, ujistěte se, že kořenový adresář úložiště obsahuje ve vašem projektu správné soubory.

| Runtime (Modul runtime) | Soubory kořenového adresáře |
|-|-|
| ASP.NET (jenom Windows) | _*. sln_, _*. csproj_ nebo _Default. aspx_ |
| ASP.NET Core | _*. sln_ nebo _*. csproj_ |
| PHP | _index. php_ |
| Ruby (jenom Linux) | _Gemfile_ |
| Node.js | _server.js_, _app.js_ nebo _package.jsna_ pomocí spouštěcího skriptu |
| Python | _\* . py_, _requirements.txt_ nebo _runtime.txt_ |
| HTML | _default.htm_, _default.html_, _Default. ASP_, _index.htm_, _index.html_ nebo _iisstart.htm_ |
| WebJobs | _\<job_name>/Run.\<extension>_ v _části \_ data aplikací/úlohy/nepřetržité_ pro nepřetržité webové úlohy nebo _\_ data aplikací/úlohy/aktivované_ pro aktivované webové úlohy. Další informace najdete v [dokumentaci k WebJobs v Kudu](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Functions | Viz [průběžné nasazování pro Azure Functions](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment). |

Chcete-li přizpůsobit nasazení, zahrňte soubor *. Deployment* do kořenového adresáře úložiště. Další informace najdete v tématu [přizpůsobení nasazení](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) a [vlastního skriptu nasazení](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Pokud vyvíjíte v aplikaci Visual Studio, nechte [Visual Studio vytvořit úložiště za vás](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio&preserve-view=true). Projekt je hned připravený k nasazení pomocí Gitu.
>

