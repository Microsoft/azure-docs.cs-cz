---
title: Použití vlastního informačního kanálu NuGet v Azure Dev Spaces
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/17/2019
ms.topic: conceptual
description: Pomocí vlastního informačního kanálu NuGet můžete získat přístup k balíčkům NuGet v prostoru pro vývoj Azure a používat je.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, kontejnery
manager: gwallace
ms.openlocfilehash: 9df095011c1ff66ff0c85993c7c85dffe62623b8
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70873257"
---
#  <a name="use-a-custom-nuget-feed-in-an-azure-dev-space"></a>Použití vlastního informačního kanálu NuGet v prostoru pro vývoj v Azure

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

Vytvořte soubor [NuGet. config](https://docs.microsoft.com/nuget/reference/nuget-config-file) ve složce projektu a nastavte `packageSources` oddíly a `packageSourceCredentials` pro váš informační kanál NuGet. `packageSources` Oddíl obsahuje adresu URL vašeho informačního kanálu, která musí být přístupná z vašeho clusteru AKS. `packageSourceCredentials` Přihlašovací údaje pro přístup k informačnímu kanálu. Příklad:

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

Aktualizujte fázemi tak, aby `NuGet.Config` se soubor zkopíroval do image. Příklad:

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> V systému Windows `NuGet.Config`, `Nuget.Config`, a `nuget.config` všechny fungují jako platné názvy souborů. V systému Linux je `NuGet.Config` pouze platný název souboru pro tento soubor. Vzhledem k tomu, že Azure Dev Spaces používá Docker a Linux, musí `NuGet.Config`mít tento soubor název. Pojmenování můžete opravit ručně nebo spuštěním `dotnet restore --configfile nuget.config`.


Pokud používáte Git, neměli byste mít přihlašovací údaje pro váš informační kanál NuGet ve správě verzí. Přidejte `NuGet.Config` dopro`NuGet.Config`svůj projekt, aby se soubor nepřidal do správy verzí. `.gitignore` Azure dev Spaces bude tento soubor potřebovat během procesu sestavení image kontejneru, ale ve výchozím nastavení respektuje pravidla definovaná v `.gitignore` a `.dockerignore` během synchronizace. Pokud chcete změnit výchozí nastavení a povolíte Azure dev Spaces synchronizaci `NuGet.Config` souboru, `azds.yaml` aktualizujte soubor:

```yaml
build:
useGitIgnore: true
ignore:
- “!NuGet.Config”
```

Pokud Git nepoužíváte, můžete tento krok přeskočit.

Při příštím spuštění `azds up` `F5` nebo přihlášení do Visual Studio Code nebo sady Visual Studio se Azure dev Spaces synchronizuje `NuGet.Config` soubor, který použije k instalaci závislostí balíčku.

## <a name="next-steps"></a>Další postup

Přečtěte si další informace o [NuGet a o tom, jak funguje](https://docs.microsoft.com/nuget/what-is-nuget).