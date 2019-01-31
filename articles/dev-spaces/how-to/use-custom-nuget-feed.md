---
title: Jak používat vlastní NuGet informačního kanálu Azure Dev prostorů | Dokumentace Microsoftu
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: johnsta
ms.author: johnsta
ms.date: 05/11/2018
ms.topic: article
description: Použijte vlastní NuGet informační kanál k přistupovat a používat balíčky NuGet v prostoru vývoj Azure.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, kontejnery
manager: ghogen
ms.openlocfilehash: 21a70100fe186e176dfe8eb7c247d83a5d4705bd
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55466400"
---
#  <a name="use-a-custom-nuget-feed-in-an-azure-dev-space"></a>Použít vlastní NuGet kanálu v prostoru vývoj Azure

Informační kanál NuGet poskytuje pohodlný způsob, jak zahrnout zdroje balíčků v projektu. Azure Dev mezery bude muset být přístup k tohoto informačního kanálu v pořadí závislosti správně nainstalovat v kontejneru Dockeru.

## <a name="set-up-a-nuget-feed"></a>Nastavení informačního kanálu NuGet

Nastavení informačního kanálu NuGet:
1. Přidat [odkaz na balíček](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) v `*.csproj` soubor `PackageReference` uzlu.

   ```xml
   <ItemGroup>
       <!-- ... -->
       <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
       <!-- ... -->
   </ItemGroup>
   ```

2. Vytvoření [NuGet.Config](https://docs.microsoft.com/nuget/reference/nuget-config-file) souboru ve složce projektu.
     * Použití `packageSources` části tak, aby odkazovaly na váš NuGet kanálu umístění. Důležité: Informačního kanálu NuGet musí být veřejně přístupná.
     * Použití `packageSourceCredentials` části a nakonfigurujte přihlašovací údaje uživatelského jména a hesla. 

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

3. Pokud používáte zdrojového kódu:
    - Referenční dokumentace `NuGet.Config` ve vašich `.gitignore` souboru tak, že nemáte omylem potvrzení přihlašovacích údajů do zdrojového úložiště.
    - Otevřít `azds.yaml` souborů ve vašem projektu a vyhledejte `build` části a vložte následující fragment kódu a zkontrolujte, že `NuGet.Config` souboru se budou synchronizovat do Azure tak, aby ji používalo během procesu sestavení image kontejneru. (Ve výchozím nastavení, Azure Dev prostory synchronizovat soubory, které odpovídají `.gitignore` a `.dockerignore` pravidla.)

        ```yaml
        build:
        useGitIgnore: true
        ignore:
        - “!NuGet.Config”
        ```


## <a name="next-steps"></a>Další postup

Po dokončení výše uvedené kroky, při příštím spuštění `azds up` (nebo stiskněte tlačítko `F5` ve VSCode nebo Visual Studio), prostory vývoj Azure budou synchronizovat `NuGet.Config` souboru do Azure, což je pak využívaných `dotnet restore` k instalaci balíčku závislosti v kontejneru.

