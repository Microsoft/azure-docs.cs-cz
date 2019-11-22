---
title: Použití vlastního informačního kanálu NuGet v Azure Dev Spaces
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/17/2019
ms.topic: conceptual
description: Pomocí vlastního informačního kanálu NuGet můžete získat přístup k balíčkům NuGet v prostoru pro vývoj Azure a používat je.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, kontejnery
manager: gwallace
ms.openlocfilehash: ee14d999872f6e739321c144831d60a4ae6f9388
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279951"
---
#  <a name="use-a-custom-nuget-feed-in-an-azure-dev-space"></a>Použití vlastního informačního kanálu NuGet v prostoru pro vývoj v Azure

Kanál NuGet poskytuje pohodlný způsob, jak zahrnout zdroje balíčků do projektu. Azure Dev Spaces musí mít přístup k tomuto informačnímu kanálu, aby bylo možné správně nainstalovat závislosti v kontejneru Docker.

## <a name="set-up-a-nuget-feed"></a>Nastavení kanálu NuGet

Přidejte [odkaz na balíček](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) pro vaši závislost do souboru `*.csproj` pod uzlem `PackageReference`. Příklad:

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

Vytvořte soubor [NuGet. config](https://docs.microsoft.com/nuget/reference/nuget-config-file) ve složce projektu a nastavte `packageSources` a `packageSourceCredentials` oddíly pro váš informační kanál NuGet. Oddíl `packageSources` obsahuje adresu URL vašeho informačního kanálu, která musí být přístupná z vašeho clusteru AKS. `packageSourceCredentials` jsou přihlašovací údaje pro přístup k informačnímu kanálu. Příklad:

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

Aktualizujte fázemi a zkopírujte soubor `NuGet.Config` do image. Příklad:

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> Ve Windows, `NuGet.Config`, `Nuget.Config`a `nuget.config` všechny fungují jako platné názvy souborů. V systému Linux je pro tento soubor pouze `NuGet.Config` platný název souboru. Vzhledem k tomu, že Azure Dev Spaces používá Docker a Linux, musí mít tento soubor název `NuGet.Config`. Pojmenování můžete opravit ručně nebo spuštěním `dotnet restore --configfile nuget.config`.


Pokud používáte Git, neměli byste mít přihlašovací údaje pro váš informační kanál NuGet ve správě verzí. Přidejte `NuGet.Config` do `.gitignore` pro váš projekt, aby se soubor `NuGet.Config` nepřidal do správy verzí. Azure Dev Spaces bude tento soubor potřebovat během procesu sestavení image kontejneru, ale ve výchozím nastavení respektuje pravidla definovaná v `.gitignore` a `.dockerignore` během synchronizace. Pokud chcete změnit výchozí nastavení a povolíte Azure Dev Spaces synchronizaci `NuGet.Config` souboru, aktualizujte soubor `azds.yaml`:

```yaml
build:
useGitIgnore: true
ignore:
- "!NuGet.Config"
```

Pokud Git nepoužíváte, můžete tento krok přeskočit.

Při příštím spuštění `azds up` nebo `F5` v Visual Studio Code nebo v aplikaci Visual Studio se Azure Dev Spaces synchronizuje soubor `NuGet.Config` použije k instalaci závislostí balíčku.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [NuGet a o tom, jak funguje](https://docs.microsoft.com/nuget/what-is-nuget).