---
title: Ladění aplikací v místním kontejneru Dockeru | Dokumentace Microsoftu
description: Zjistěte, jak upravit aplikaci, která běží v místním kontejneru Dockeru, aktualizujte kontejneru prostřednictvím operace Edit and aktualizace a nastavení ladění zarážky
services: azure-container-service
documentationcenter: na
author: ghogen
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
ms.openlocfilehash: 07a7c1e11d8ca20ff4f42abcb84961cb7cd9e0e1
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44298201"
---
# <a name="debugging-apps-in-a-local-docker-container"></a>Ladění aplikací v místním kontejneru Dockeru
## <a name="overview"></a>Přehled
Visual Studio 2017 poskytuje konzistentní způsob, jak vyvíjet v kontejneru Dockeru pro Linux a ověřit vaše aplikace v místním prostředí.
Není nutné restartovat kontejneru pokaždé, když provedete změně kódu.
Tento článek ukazuje, jak použít funkci "Upravit a aktualizovat" ke spuštění základní webové aplikace v ASP.NET v místním kontejneru Dockeru, proveďte potřebné změny a pak aktualizujte prohlížeč, aby se tyto změny.
Tento článek také ukazuje, jak nastavit zarážky pro ladění.

> [!NOTE]
> Podpora kontejnerů Windows bude dostupná v budoucí verzi.
>
>

## <a name="prerequisites"></a>Požadavky
Musí být nainstalované následující nástroje.

* [Visual Studio 2017](https://www.visualstudio.com/downloads/)
* [Sada SDK Microsoft ASP.NET Core 1.0](https://go.microsoft.com/fwlink/?LinkID=809122)

Pro spouštění kontejnerů Docker místně, musíte klienta místní docker.
Můžete použít [nástrojů Dockeru](https://www.docker.com/products/docker-toolbox), což vyžaduje Hyper-V se deaktivuje, nebo můžete použít [Docker pro Windows](https://www.docker.com/get-docker), které používá technologie Hyper-V a vyžaduje Windows 10.

Pokud pomocí nástrojů Dockeru, budete muset [konfigurace klienta Dockeru](vs-azure-tools-docker-setup.md)

## <a name="1-create-a-web-app"></a>1. Vytvoření webové aplikace
[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-add-docker-support"></a>2. Přidání podpory Dockeru
[!INCLUDE [Add docker support](../includes/vs-azure-tools-docker-add-docker-support.md)]

## <a name="3-edit-your-code-and-refresh"></a>3. Upravit kód a aktualizace
Pokud chcete rychle iterovat změny, můžete spustit aplikaci v rámci kontejneru a pokračovat v provádění změn, zobrazení je jako při použití služby IIS Express.

1. Nastavte konfiguraci řešení na `Debug` a stiskněte klávesu  **&lt;CTRL + F5 >** k sestavení image dockeru a spouštět místně.

    Jakmile se image kontejneru je sestavený Build a běží v kontejneru Dockeru, spustí aplikace Visual Studio webové aplikace ve vašem výchozím prohlížeči.
    Pokud používáte prohlížeč Microsoft Edge nebo jinak obsahuje chyby, přečtěte si téma [Poradce při potížích s](vs-azure-tools-docker-troubleshooting-docker-errors.md) oddílu.
2. Přejděte na stránku o, což je, kde budeme provádět změny.
3. Vraťte se do sady Visual Studio a otevřete `Views\Home\About.cshtml`.
4. Na konec souboru přidejte následující obsah HTML a uložte změny.

    ```
    <h1>Hello from a Docker Container!</h1>
    ```
5. Okno výstup, sledovat, i když se dokončí sestavení .NET a uvidíte tyto řádky, přepněte zpět do prohlížeče a aktualizujte stránku o.

   ```
   Now listening on: http://*:80
   Application started. Press Ctrl+C to shut down
   ```
6. Vaše změny se použily!

## <a name="4-debug-with-breakpoints"></a>4. Ladění se zarážkami
Často změny potřebovat další kontroly, využívá funkce ladění sady Visual Studio.

1. Vraťte se do sady Visual Studio a otevřete `Controllers\HomeController.cs`
2. Nahraďte obsah metody About() následujícími způsoby:

   ```
   string message = "Your application description page from within a Container";
   ViewData["Message"] = message;
   ````
3. Nastavit zarážku na levé straně `string message`... řádku.
4. Spuštění  **&lt;F5 >** pro spuštění ladění.
5. Přejděte na stránku o narazila na zarážku.
6. Přepněte do sady Visual Studio zobrazíte zarážky a zkontrolovat hodnoty zprávy.

   ![][2]

## <a name="summary"></a>Souhrn
Podpora pro Docker v sadě Visual Studio 2017 umožňuje získat produktivitu místně, pracovat s produkční realitu vývoje v kontejneru Dockeru.

## <a name="troubleshooting"></a>Řešení potíží
[Řešení potíží s vývojem Docker sady Visual Studio](vs-azure-tools-docker-troubleshooting-docker-errors.md)

## <a name="more-about-docker-with-visual-studio-windows-and-azure"></a>Další informace o Dockeru pomocí sady Visual Studio, Windows a Azure
* [Nástroje dockeru pro Azure DevOps](http://aka.ms/dockertoolsforvsts) – sestavování a nasazování kontejnerů dockeru
* [Nástroje dockeru pro Visual Studio Code](http://aka.ms/dockertoolsforvscode) – jazykové služby pro úpravy souborů dockeru ve více scénářích e2e už
* [Informace o kontejneru Windows](http://aka.ms/containers)– informace o Windows serveru a Nano Server
* [Azure Container Service](https://azure.microsoft.com/services/container-service/) - [obsahu Azure Container Service](http://aka.ms/AzureContainerService)
* Další příklady práce s Dockerem, naleznete v tématu [práci s Dockerem](https://github.com/Microsoft/HealthClinic.biz/wiki/Working-with-Docker) z [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 Connect [ukázka](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/). Další rychlé starty z demoverze HealthClinic.biz najdete v článku [Rychlé starty nástrojů pro vývojáře Azure](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).

## <a name="various-docker-tools"></a>Různé nástroje Dockeru
[Některé nástroje skvělé dockeru (Steve Lasker blogu)](https://blogs.msdn.microsoft.com/stevelasker/2016/03/25/some-great-docker-tools/)

## <a name="good-articles"></a>Dobré články
[Úvod do Mikroslužeb serveru nginx](https://www.nginx.com/blog/introduction-to-microservices/)

## <a name="presentations"></a>Prezentace
* [Steve Lasker: VS Live Las Vegas 2016 - e2e Dockeru](https://github.com/SteveLasker/Presentations/blob/master/VSLive2016/Vegas/)
* [Úvod do ASP.NET Core @ build 2016 – Pokud jste na ukázku](https://channel9.msdn.com/Events/Build/2016/B810)
* [Vývoj aplikací .NET v kontejnerech, Channel 9](https://blogs.msdn.microsoft.com/stevelasker/2016/02/19/developing-asp-net-apps-in-docker-containers/)

[2]: ./media/vs-azure-tools-docker-edit-and-refresh/breakpoint.png
