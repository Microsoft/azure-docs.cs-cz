---
title: Použití vlastního informačního kanálu NuGet
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/17/2019
ms.topic: conceptual
description: Použijte vlastní NuGet feed pro přístup a použití balíčků NuGet v Azure Dev Space.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, kontejnery
manager: gwallace
ms.openlocfilehash: 39984a3b3a1be64a497fb8088559ccfcdee4f1c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325730"
---
# <a name="use-a-custom-nuget-feed-with-azure-dev-spaces"></a>Použití vlastního informačního kanálu NuGet s Azure Dev Spaces

NuGet kanál poskytuje pohodlný způsob, jak zahrnout zdroje balíčků v projektu. Azure Dev Spaces potřebuje přístup k tomuto kanálu, aby se závislosti správně nainstalovaly v kontejneru Dockeru.

## <a name="set-up-a-nuget-feed"></a>Nastavení informačního kanálu NuGet

Přidejte [odkaz na balíček](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) `*.csproj` pro vaši `PackageReference` závislost v souboru pod uzlovým. Například:

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

Vytvořte soubor [NuGet.Config](https://docs.microsoft.com/nuget/reference/nuget-config-file) ve složce `packageSources` `packageSourceCredentials` projektu a nastavte oddíly a pro informační kanál NuGet. Tato `packageSources` část obsahuje adresu URL zdroje, která musí být přístupná z clusteru AKS. Jsou `packageSourceCredentials` pověření pro přístup k informačníkanál. Například:

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

Aktualizujte soubory Dockerfiles `NuGet.Config` a zkopírujte soubor do obrazu. Například:

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> V systému `Nuget.Config`Windows `nuget.config` , `NuGet.Config`, a všechny funguje jako platné názvy souborů. Na Linuxu `NuGet.Config` je platný název souboru pouze pro tento soubor. Vzhledem k tomu, že Azure Dev `NuGet.Config`Spaces používá Docker a Linux, musí být tento soubor pojmenován . Pojmenování můžete opravit ručně nebo `dotnet restore --configfile nuget.config`spuštěním .


Pokud používáte Git, neměli byste mít pověření pro váš nugetový kanál ve správu verzí. Přidejte `NuGet.Config` `.gitignore` do projektu tak, `NuGet.Config` aby soubor nebyl přidán do správy verzí. Azure Dev Spaces bude potřebovat tento soubor během procesu sestavení image kontejneru, ale ve výchozím nastavení respektuje pravidla definovaná v `.gitignore` a `.dockerignore` během synchronizace. Chcete-li změnit výchozí nastavení a povolit synchronizaci souboru `NuGet.Config` azure dev spaces, aktualizujte `azds.yaml` soubor:

```yaml
build:
useGitIgnore: true
ignore:
- "!NuGet.Config"
```

Pokud nepoužíváte Git, můžete tento krok přeskočit.

Při příštím spuštění `azds up` nebo `F5` přístupů v kódu Visual Studio nebo Visual `NuGet.Config` Studio, Azure Dev Spaces synchronizuje soubor použít k instalaci závislostí balíčků.

## <a name="next-steps"></a>Další kroky

Další informace o [NuGet a jak to funguje](https://docs.microsoft.com/nuget/what-is-nuget).