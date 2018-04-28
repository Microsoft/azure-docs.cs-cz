---
title: Nasazení aplikace .NET v kontejneru do Azure Service Fabric | Microsoft Docs
description: Naučíte se, jak v sadě Visual Studio zabalit aplikaci .NET do kontejneru Dockeru. Tato nová aplikace typu kontejner se pak nasadí do clusteru Service Fabric.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 04a6fbc56d3c65cfb53339c4178dfa36e2aeb4ea
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="deploy-a-net-application-in-a-windows-container-to-azure-service-fabric"></a>Nasazení aplikace .NET v kontejneru Windows do Azure Service Fabric

V tomto kurzu se dozvíte, jak nasadit existující aplikaci ASP.NET v kontejneru Windows do Azure.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit projekt Dockeru v sadě Visual Studio
> * Kontejnerizovat existující aplikaci
> * Nastavit průběžnou integraci pomocí sady Visual Studio a VSTS

## <a name="prerequisites"></a>Požadavky

1. Nainstalujte [Docker CE pro Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description), abyste mohli spouštět kontejnery ve Windows 10.
2. Seznamte se s [Rychlým startem pro kontejnery Windows 10][link-container-quickstart].
3. Stáhněte si ukázkovou aplikaci [Fabrikam Fiber CallCenter][link-fabrikam-github].
4. Nainstalujte [Azure PowerShell][link-azure-powershell-install].
5. Nainstalujte [rozšíření Continuous Delivery Tools for Visual Studio 2017][link-visualstudio-cd-extension].
6. Vytvořte si [předplatné Azure][link-azure-subscription] a [účet služby Visual Studio Team Services][link-vsts-account]. 
7. [Vytvořte cluster v Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

## <a name="create-a-cluster-on-azure"></a>Vytvoření clusteru v Azure
Aplikace Service Fabric se spouští v clusteru, což je síťově propojená sada virtuálních nebo fyzických počítačů. [Nastavte cluster Service Fabric běžící v Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md) před tím, než vytvoříte a nasadíte svou aplikaci. Při vytváření clusteru zvolte skladovou položku, která podporuje spouštění kontejnerů (například Windows Server 2016 Datacenter s kontejnery).

## <a name="containerize-the-application"></a>Kontejnerizace aplikace

Když teď máte v Azure spuštěný cluster Service Fabric, jste připraveni vytvořit a nasadit kontejnerizovanou aplikaci. Abychom naši aplikaci mohli začít spouštět v kontejneru, potřebujeme do projektu v sadě Visual Studio přidat **podporu Dockeru**. Po přidání **podpory Dockeru** do aplikace se stanou dvě věci. Zaprvé se do projektu přidá soubor _Dockerfile_. Tento nový soubor popisuje způsob sestavení image kontejneru. Zadruhé se pak do řešení přidá nový projekt _docker-compose_. Tento nový projekt obsahuje několik souborů docker-compose. Soubory docker-compose je možné použít k popisu způsobu spouštění kontejneru.

Další informace o práci s [kontejnerovými nástroji v sadě Visual Studio][link-visualstudio-container-tools].

### <a name="add-docker-support"></a>Přidání podpory Dockeru

Otevřete v sadě Visual Studio soubor [FabrikamFiber.CallCenter.sln][link-fabrikam-github].

Klikněte pravým tlačítkem na projekt **FabrikamFiber.Web** > **Přidat** > **Podpora Dockeru**.

### <a name="add-support-for-sql"></a>Přidání podpory jazyka SQL

Tato aplikace používá jako poskytovatele dat SQL, proto se ke spuštění aplikace vyžaduje server SQL. Přidejte referenci na image kontejneru SQL Serveru do souboru docker-compose.override.yml.

V sadě Visual Studio otevřete **Průzkumníka řešení**, vyhledejte **docker-compose** a otevřete soubor **docker-compose.override.yml**.

Přejděte do uzlu `services:` a přidejte uzel `db:`, který pro kontejner definuje položku SQL Serveru.

```yml
  db:
    image: microsoft/mssql-server-windows-developer
    environment:
      sa_password: "Password1"
      ACCEPT_EULA: "Y"
    ports:
      - "1433"
    healthcheck:
      test: [ "CMD", "sqlcmd", "-U", "sa", "-P", "Password1", "-Q", "select 1" ]
      interval: 1s
      retries: 20
```

>[!NOTE]
>Pro účely místního ladění můžete použít jakýkoli SQL Server, kterému dáváte přednost, pokud je dostupný z vašeho hostitele. Nicméně **localdb** nepodporuje komunikaci `container -> host`.

>[!WARNING]
>Spouštění SQL Serveru v kontejneru nepodporuje zachovávání dat. Když se kontejner zastaví, vaše data se vymažou. Nepoužívejte tuto konfiguraci pro produkční prostředí.

Přejděte do uzlu `fabrikamfiber.web:` a přidejte podřízený uzel `depends_on:`. Tím se zajistí spuštění služby `db` (kontejner SQL Serveru) před naší webovou aplikací (fabrikamfiber.web).

```yml
  fabrikamfiber.web:
    depends_on:
      - db
```

### <a name="update-the-web-config"></a>Aktualizace webové konfigurace

Vraťte se do projektu **FabrikamFiber.Web** a aktualizujte připojovací řetězec v souboru **web.config** tak, aby odkazoval na SQL Server v kontejneru.

```xml
<add name="FabrikamFiber-Express" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />

<add name="FabrikamFiber-DataWarehouse" connectionString="Data Source=db,1433;Database=FabrikamFiber;User Id=sa;Password=Password1;MultipleActiveResultSets=True" providerName="System.Data.SqlClient" />
```

>[!NOTE]
>Pokud při sestavování vaší webové aplikace pro vydání chcete použít jiný SQL Server, přidejte do souboru web.release.config další připojovací řetězec.

### <a name="test-your-container"></a>Test kontejneru

Pokud chcete spustit a ladit aplikaci ve svém kontejneru, stiskněte **F5**.

V aplikaci Edge se otevře definovaná úvodní stránka vaší aplikace s použitím IP adresy kontejneru na interní síti NAT (obvykle 172.x.x.x). Další informace o ladění aplikací v kontejnerech pomocí sady Visual Studio 2017 najdete v [tomto článku][link-debug-container].

![příklad aplikace fabrikam v kontejneru][image-web-preview]

Kontejner je teď připravený k sestavení a zabalení do aplikace Service Fabric. Jakmile na svém počítači budete mít sestavenou image kontejneru, můžete ji nasdílet do jakéhokoli registru kontejnerů a stáhnout a spustit na jakémkoli hostiteli.

## <a name="get-the-application-ready-for-the-cloud"></a>Příprava aplikace pro cloud

Abychom aplikaci připravili na spouštění v Service Fabric v Azure, musíme provést dva kroky:

1. Zveřejnit port, na kterém chceme mít přístup k naší webové aplikaci v clusteru Service Fabric.
2. Zajistit pro naši aplikaci databázi SQL připravenou pro produkční prostředí.

### <a name="expose-the-port-for-the-app"></a>Zveřejnění portu pro aplikaci
Cluster Service Fabric, který jsme nakonfigurovali, má ve výchozím nastavení ve službě Azure Load Balancer, která vyrovnává příchozí provoz do clusteru, otevřený port *80*. Náš kontejner můžeme na tomto portu zveřejnit prostřednictvím souboru docker-compose.yml.

V sadě Visual Studio otevřete **Průzkumníka řešení**, vyhledejte **docker-compose** a otevřete soubor **docker-compose.yml**.

Upravte uzel `fabrikamfiber.web:` a přidejte podřízený uzel `ports:`.

Přidejte položku řetězce `- "80:80"`. Váš soubor docker-compose.yml by měl vypadat přibližně takto:

```yml
  version: '3'

  services:
    fabrikamfiber.web:
      image: fabrikamfiber.web
      build:
        context: .\FabrikamFiber.Web
        dockerfile: Dockerfile
      ports:
        - "80:80"
```

### <a name="use-a-production-sql-database"></a>Použití produkční databáze SQL
Při spouštění v produkčním prostředí potřebujeme ukládat naše data v databázi. V současné době neexistuje způsob, jak zaručit trvalost dat v kontejneru, proto na SQL Server v kontejneru nemůžete ukládat produkční data.

Doporučujeme využít službu Azure SQL Database. Pokud chcete nastavit a spustit spravovaný SQL Server v Azure, přejděte na článek s [rychlými starty pro službu Azure SQL Database][link-azure-sql].

>[!NOTE]
>Nezapomeňte změnit připojovací řetězec k serveru SQL v souboru **web.release.config** v projektu **FabrikamFiber.Web**.
>
>Pokud není dostupná žádná databáze SQL, tato aplikace selže bez výpadku. Pokud se tak rozhodnete, můžete pokračovat a nasadit aplikaci bez serveru SQL.

## <a name="deploy-with-visual-studio-team-services"></a>Nasazení pomocí Visual Studio Team Services

Pokud chcete nastavit nasazení pomocí Visual Studio Team Services, je potřeba nainstalovat [rozšíření Continuous Delivery Tools for Visual Studio 2017][link-visualstudio-cd-extension]. Toto rozšíření usnadňuje nasazení do Azure tím, že nakonfiguruje Visual Studio Team Services a nasadí aplikaci do clusteru Service Fabric.

Abyste mohli začít, váš kód musí být hostovaný ve správě zdrojového kódu. Ve zbytku této části se předpokládá použití **gitu**.

### <a name="set-up-a-vsts-repo"></a>Nastavení úložiště VSTS
V pravém dolním rohu sady Visual Studio klikněte na **Přidat do správy zdrojového kódu** > **Git** (nebo jakákoli možnost, které dáváte přednost).

![stisknutí tlačítka správy zdrojového kódu][image-source-control]

V podokně _Team Explorer_ stiskněte **Publikovat úložiště Git**.

Vyberte název vašeho úložiště VSTS a stiskněte **Úložiště**.

![publikování úložiště do VSTS][image-publish-repo]

Když je teď váš kód synchronizovaný se zdrojovým úložištěm VSTS, můžete nakonfigurovat průběžnou integraci a průběžné doručování.

### <a name="setup-continuous-delivery"></a>Nastavení průběžného doručování

V _Průzkumníku řešení_ klikněte pravým tlačítkem na **řešení** > **Konfigurovat průběžné doručování**.

Vyberte předplatné Azure.

Nastavte **Typ hostitele** na **Cluster Service Fabric**.

Natavte hodnotu **Cílový hostitel** na cluster Service Fabric, který jste vytvořili v předchozí části.

Zvolte **Registr kontejnerů**, do kterého chcete kontejner publikovat.

>[!TIP]
>Pomocí tlačítka **Upravit** vytvořte registr kontejnerů.

Stiskněte **OK**.

![nastavení průběžné integrace v Service Fabric][image-setup-ci]
   
   Po dokončení konfigurace se váš kontejner nasadí do Service Fabric. Kdykoli do úložiště nasdílíte změny, provede se nové sestavení a vydání.
   
   >[!NOTE]
   >Sestavení imagí kontejnerů trvá přibližně 15 minut.
   >První nasazení do clusteru Service Fabric způsobí stažení základních imagí kontejnerů Windows Serveru Core. Dokončení stahování trvá dalších 5 až 10 minut.

Přejděte do aplikace Fabrikam Call Center s použitím adresy URL vašeho clusteru, například *http://mycluster.westeurope.cloudapp.azure.com*.

Teď, když jste kontejnerizovali a nasadili řešení Fabrikam Call Center, můžete otevřít [Azure Portal][link-azure-portal] a podívat se na spuštěnou aplikaci v Service Fabric. Pokud chcete aplikaci vyzkoušet, otevřete webový prohlížeč a přejděte na adresu URL vašeho clusteru Service Fabric.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvořit projekt Dockeru v sadě Visual Studio
> * Kontejnerizovat existující aplikaci
> * Nastavit průběžnou integraci pomocí sady Visual Studio a VSTS

V další části kurzu se dozvíte, jak nastavit [monitorování vašeho kontejneru](service-fabric-tutorial-monitoring-wincontainers.md).

<!--   NOTE SURE WHAT WE SHOULD DO YET HERE

Advance to the next tutorial to learn how to bind a custom SSL certificate to it.

> [!div class="nextstepaction"]
> [Bind an existing custom SSL certificate to Azure Web Apps](app-service-web-tutorial-custom-ssl.md)

## Next steps

- [Container Tooling in Visual Studio][link-visualstudio-container-tools]
- [Get started with containers in Service Fabric][link-servicefabric-containers]
- [Creating Service Fabric applications][link-servicefabric-createapp]
-->

[link-debug-container]: /dotnet/articles/core/docker/visual-studio-tools-for-docker
[link-fabrikam-github]: https://aka.ms/fabrikamcontainer
[link-container-quickstart]: /virtualization/windowscontainers/quick-start/quick-start-windows-10
[link-visualstudio-container-tools]: /dotnet/articles/core/docker/visual-studio-tools-for-docker
[link-azure-powershell-install]: /powershell/azure/install-azurerm-ps
[link-servicefabric-create-secure-clusters]: service-fabric-cluster-creation-via-arm.md
[link-visualstudio-cd-extension]: https://aka.ms/cd4vs
[link-servicefabric-containers]: service-fabric-get-started-containers.md
[link-servicefabric-createapp]: service-fabric-create-your-first-application-in-visual-studio.md
[link-azure-portal]: https://portal.azure.com
[link-sf-clustertemplate]: https://aka.ms/securepreviewonelineclustertemplate
[link-azure-pricing-calculator]: https://azure.microsoft.com/pricing/calculator/
[link-azure-subscription]: https://azure.microsoft.com/free/
[link-vsts-account]: https://www.visualstudio.com/team-services/pricing/
[link-azure-sql]: /azure/sql-database/

[image-web-preview]: media/service-fabric-host-app-in-a-container/fabrikam-web-sample.png
[image-source-control]: media/service-fabric-host-app-in-a-container/add-to-source-control.png
[image-publish-repo]: media/service-fabric-host-app-in-a-container/publish-repo.png
[image-setup-ci]: media/service-fabric-host-app-in-a-container/configure-continuous-integration.png
