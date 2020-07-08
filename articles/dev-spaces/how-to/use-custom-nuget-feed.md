---
title: Použití vlastního informačního kanálu NuGet
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/17/2019
ms.topic: conceptual
description: Pomocí vlastního informačního kanálu NuGet můžete získat přístup k balíčkům NuGet v prostoru pro vývoj Azure a používat je.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, kontejnery
manager: gwallace
ms.openlocfilehash: 39984a3b3a1be64a497fb8088559ccfcdee4f1c6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "74325730"
---
# <a name="use-a-custom-nuget-feed-with-azure-dev-spaces"></a>Použití vlastního informačního kanálu NuGet s Azure Dev Spaces

Kanál NuGet poskytuje pohodlný způsob, jak zahrnout zdroje balíčků do projektu. Azure Dev Spaces musí mít přístup k tomuto informačnímu kanálu, aby bylo možné správně nainstalovat závislosti v kontejneru Docker.

## <a name="set-up-a-nuget-feed"></a>Nastavení kanálu NuGet

Přidejte [odkaz na balíček](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) pro vaši závislost do `*.csproj` souboru pod `PackageReference` uzlem. Příklad:

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

Vytvořte soubor [NuGet.Config](https://docs.microsoft.com/nuget/reference/nuget-config-file) ve složce projektu a nastavte `packageSources` `packageSourceCredentials` oddíly a pro váš informační kanál NuGet. `packageSources`Oddíl obsahuje adresu URL vašeho informačního kanálu, která musí být přístupná z vašeho clusteru AKS. `packageSourceCredentials`Přihlašovací údaje pro přístup k informačnímu kanálu. Příklad:

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

Aktualizujte fázemi tak, aby se `NuGet.Config` soubor zkopíroval do image. Příklad:

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> V systému Windows,, `NuGet.Config` `Nuget.Config` a `nuget.config` všechny fungují jako platné názvy souborů. V systému Linux `NuGet.Config` je pouze platný název souboru pro tento soubor. Vzhledem k tomu, že Azure Dev Spaces používá Docker a Linux, musí mít tento soubor název `NuGet.Config` . Pojmenování můžete opravit ručně nebo spuštěním `dotnet restore --configfile nuget.config` .


Pokud používáte Git, neměli byste mít přihlašovací údaje pro váš informační kanál NuGet ve správě verzí. Přidejte `NuGet.Config` do `.gitignore` pro svůj projekt, aby se `NuGet.Config` soubor nepřidal do správy verzí. Azure Dev Spaces bude tento soubor potřebovat během procesu sestavení image kontejneru, ale ve výchozím nastavení respektuje pravidla definovaná v `.gitignore` a `.dockerignore` během synchronizace. Pokud chcete změnit výchozí nastavení a povolíte Azure Dev Spaces synchronizaci `NuGet.Config` souboru, aktualizujte `azds.yaml` soubor:

```yaml
build:
useGitIgnore: true
ignore:
- "!NuGet.Config"
```

Pokud Git nepoužíváte, můžete tento krok přeskočit.

Při příštím spuštění `azds up` nebo `F5` přihlášení do Visual Studio Code nebo sady Visual Studio se Azure dev Spaces synchronizuje soubor, který `NuGet.Config` použije k instalaci závislostí balíčku.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [NuGet a o tom, jak funguje](https://docs.microsoft.com/nuget/what-is-nuget).