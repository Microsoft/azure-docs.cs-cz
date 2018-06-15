---
title: Ladění aplikací v místním kontejner Docker | Microsoft Docs
description: Zjistěte, jak upravit aplikaci, která běží v místní kontejner Docker, aktualizujte kontejneru prostřednictvím úpravy a aktualizace a nastavit zarážky ladění
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: ''
ms.assetid: 480e3062-aae7-48ef-9701-e4f9ea041382
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 07/22/2016
ms.author: mlearned
ms.openlocfilehash: 7df8bb2dd61466fd9af6492277d54cf0ffdfeb0b
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/20/2017
ms.locfileid: "26781390"
---
# <a name="debugging-apps-in-a-local-docker-container"></a>Ladění aplikací v místním kontejneru Dockeru
## <a name="overview"></a>Přehled
Visual Studio Tools for Docker zajišťuje konzistentní způsob pro vývoj v a ověřit svoji aplikaci místně v kontejner Linux Docker.
Nemáte restartuje kontejner pokaždé, když provedete kód změnit.
Tento článek ukazuje, jak používat funkci "Upravit a aktualizovat" a spusťte ASP.NET Core webové aplikace v místním kontejner Docker, proveďte potřebné změny a pak aktualizujte prohlížeč a sledovat provedené změny.
Tento článek také ukazuje, jak nastavit zarážky pro ladění.

> [!NOTE]
> Podpora kontejnerů Windows budou přicházet v budoucí verzi
>
>

## <a name="prerequisites"></a>Požadavky
Tyto nástroje musí být nainstalován.

* [Nejnovější verze sady Visual Studio](https://www.visualstudio.com/downloads/)
* [Microsoft ASP.NET Core 1.0 SDK](https://go.microsoft.com/fwlink/?LinkID=809122)

Ke spuštění Docker kontejnery místně, budete potřebovat místní docker klienta.
Můžete použít [Docker nástrojů](https://www.docker.com/products/docker-toolbox), což vyžaduje, aby technologie Hyper-V se zakáže, nebo můžete použít [Docker pro systém Windows](https://www.docker.com/get-docker), která využívá technologie Hyper-V a vyžaduje Windows 10.

Pokud používáte Docker sada nástrojů, budete muset [konfigurace klienta Docker](vs-azure-tools-docker-setup.md)

## <a name="1-create-a-web-app"></a>1. Vytvoření webové aplikace
[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-add-docker-support"></a>2. Přidání podpory Docker
[!INCLUDE [Add docker support](../includes/vs-azure-tools-docker-add-docker-support.md)]

## <a name="3-edit-your-code-and-refresh"></a>3. Upravit kód a aktualizace
Rychle iterovat změny, můžete spustit aplikaci v rámci kontejneru a nadále provádět změny, jako byste službou IIS Express je zobrazení.

1. Nastavte konfiguraci řešení na `Debug` a stiskněte klávesu  **&lt;kombinaci kláves CTRL + F5 >** na vytvoření bitové kopie docker a spustit místně.

    Jakmile bitovou kopii kontejneru byla vytvořena a spuštěna v kontejner Docker, Visual Studio spustí webové aplikace ve výchozí prohlížeč.
    Pokud používáte prohlížeč Microsoft Edge nebo v opačném případě obsahuje chyby, přečtěte si téma [Poradce při potížích s](vs-azure-tools-docker-troubleshooting-docker-errors.md) části.
2. Přejděte na stránku o, což je, kde vytvoříme naše změnit.
3. Vraťte se na Visual Studio a otevřete `Views\Home\About.cshtml`.
4. Přidejte do něj následující obsah HTML na konec souboru a uložte změny.

    ```
    <h1>Hello from a Docker Container!</h1>
    ```
5. Prohlížení ve výstupním okně po dokončení sestavení rozhraní .NET a zobrazit tyto řádky, přepněte zpět na prohlížeči a aktualizujte stránku o.

   ```
   Now listening on: http://*:80
   Application started. Press Ctrl+C to shut down
   ```
6. Změny se aplikovaly!

## <a name="4-debug-with-breakpoints"></a>4. Ladění pomocí zarážek
Často změny potřebovat další kontroly, využívat funkce ladění sady Visual Studio.

1. Vraťte se na Visual Studio a otevřít`Controllers\HomeController.cs`
2. Nahraďte obsah metody About() s následujícími službami:

   ```
   string message = "Your application description page from within a Container";
   ViewData["Message"] = message;
   ````
3. Nastavit zarážky nalevo od `string message`... řádku.
4. Stiskněte tlačítko  **&lt;F5 >** spustit ladění.
5. Přejděte na stránku o narazí vaší zarážce.
6. Přepněte do zobrazení zarážce sady Visual Studio a zjistit hodnotu zprávy.

   ![][2]

## <a name="summary"></a>Souhrn
S [2015 nástroje sady Visual Studio pro Docker](https://aka.ms/DockerToolsForVS), můžete získat na produktivitu místně, pracovat s produkční realism vývoje v rámci kontejner Docker.

## <a name="troubleshooting"></a>Řešení potíží
[Řešení potíží s Visual Studio Docker vývoj](vs-azure-tools-docker-troubleshooting-docker-errors.md)

## <a name="more-about-docker-with-visual-studio-windows-and-azure"></a>Další informace o Docker s Visual Studio, Windows a Azure
* [Docker nástrojů pro Visual Studio](http://aka.ms/dockertoolsforvs) – vývoj kódu .NET Core v kontejneru
* [Docker nástrojů pro Visual Studio Team Services](http://aka.ms/dockertoolsforvsts) – sestavení a nasazení kontejnerů docker
* [Docker nástrojů pro Visual Studio Code](http://aka.ms/dockertoolsforvscode) -services jazyk pro úpravy docker soubory s více scénářů e2e přicházející
* [Informace o kontejneru systému Windows](http://aka.ms/containers)-informace o systému Windows Server a Nano Server
* [Azure Container Service](https://azure.microsoft.com/services/container-service/) - [obsahu Azure Container Service](http://aka.ms/AzureContainerService)
* Další příklady práce s Docker, najdete v části [práce s Docker](https://github.com/Microsoft/HealthClinic.biz/wiki/Working-with-Docker) z [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 připojit [ukázku](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/). Další rychlé starty z demoverze HealthClinic.biz najdete v článku [Rychlé starty nástrojů pro vývojáře Azure](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).

## <a name="various-docker-tools"></a>Různé nástroje Docker
[Některé skvělé docker nástroje (blog Steva Lasker)](https://blogs.msdn.microsoft.com/stevelasker/2016/03/25/some-great-docker-tools/)

## <a name="good-articles"></a>Dobrý články
[Úvod do Mikroslužeb z NGINX](https://www.nginx.com/blog/introduction-to-microservices/)

## <a name="presentations"></a>Prezentace
* [Steve Lasker: VS Live Las Vegas 2016 - Docker e2e](https://github.com/SteveLasker/Presentations/blob/master/VSLive2016/Vegas/)
* [Úvod do ASP.NET Core @ sestavení 2016 – kde jste v ukázce](https://channel9.msdn.com/Events/Build/2016/B810)
* [Vývoj aplikací .NET v kontejnerech, Channel 9](https://blogs.msdn.microsoft.com/stevelasker/2016/02/19/developing-asp-net-apps-in-docker-containers/)

[2]: ./media/vs-azure-tools-docker-edit-and-refresh/breakpoint.png
