---
title: Jak používat vlastní NuGet kanál v Azure Dev prostorech | Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: johnsta
ms.author: johnsta
ms.date: 05/11/2018
ms.topic: article
description: Použijte vlastní NuGet kanálu přístup a používání balíčků NuGet v prostoru Dev Azure.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, kontejnery
manager: ghogen
ms.openlocfilehash: 3badd15bcfd09c97b43744a20c5df05f4ff57e84
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
#  <a name="use-a-custom-nuget-feed-in-an-azure-dev-space"></a>Použít vlastní NuGet kanálu v prostoru Dev Azure

Informační kanál NuGet představuje pohodlný způsob pro zahrnutí zdroje balíčku do projektu. Azure prostory Dev bude muset být schopni přistupovat tento informační kanál v pořadí pro závislosti možné správně nainstalovaný v kontejner Docker.

## <a name="set-up-a-nuget-feed"></a>Nastavit NuGet kanálu

Nastavení NuGet kanálu:
1. Přidat [balíček odkaz](https://docs.microsoft.com/en-us/nuget/consume-packages/package-references-in-project-files) v `*.csproj` souboru pod `PackageReference` uzlu.

   ```xml
   <ItemGroup>
       <!-- ... -->
       <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
       <!-- ... -->
   </ItemGroup>
   ```

2. Vytvoření [NuGet.Config](https://docs.microsoft.com/en-us/nuget/reference/nuget-config-file) soubor ve složce projektu.
     * Použití `packageSources` části, chcete-li vaše NuGet kanálu umístění. Důležité: K informačnímu kanálu NuGet musí být veřejně přístupná.
     * Použití `packageSourceCredentials` část konfigurace uživatelského jména a hesla pověření. 

   ```xml
   <packageSources>
       <add key="Contoso" value="https://contoso.com/packages/" />
   </packageSources>

   <packageSourceCredentials>
       <Contoso>
           <add key="Username" value="user@contoso.com" />
           <add key="ClearTextPassword" value="33f!!lloppa" />
       </Contoso>
   </packageSourceCredentials>
   ```

3. Pokud používáte správu zdrojového kódu:
    - Referenční dokumentace `NuGet.Config` ve vaší `.gitignore` tak nejsou omylem potvrzení přihlašovací údaje k úložišti zdrojového souboru.
    - Otevřete `azds.yaml` souborů ve vašem projektu a najděte `build` části a vložte následující fragment kódu zajistit, aby `NuGet.Config` souboru se budou synchronizovat do Azure, aby se použít během procesu vytváření bitové kopie kontejneru. (Ve výchozím nastavení, prostory Dev Azure nebude synchronizovat soubory, které odpovídají `.gitignore` a `.dockerignore` pravidla.)

        ```yaml
        build:
        useGitIgnore: true
        ignore:
        - “!NuGet.Config”
        ```


## <a name="next-steps"></a>Další postup

Po dokončení výše uvedené kroky, při příštím spuštění `azds up` (nebo stiskněte tlačítko `F5` v VSCode nebo Visual Studio), bude synchronizovat Azure Dev prostory `NuGet.Config` souboru do Azure, který je pak využívaných `dotnet restore` k instalaci balíčku závislosti v kontejneru.

