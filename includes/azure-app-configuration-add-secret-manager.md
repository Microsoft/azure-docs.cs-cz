---
ms.openlocfilehash: c3c5b8ef94b507cad433e587c9ebfc2ec16c0ff9
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91440424"
---
## <a name="add-secret-manager"></a>Přidat správce tajných klíčů

Nástroj s názvem správce tajných dat uchovává citlivá data pro vývojovou práci mimo strom projektu. Tento přístup pomáhá zabránit náhodnému sdílení tajných kódů aplikace ve zdrojovém kódu. Provedením následujících kroků povolíte použití Správce tajných klíčů v projektu ASP.NET Core:

#### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

Přejděte do kořenového adresáře projektu a spuštěním následujícího příkazu povolte tajné úložiště v projektu:

```dotnetcli
dotnet user-secrets init
```

`UserSecretsId`Do souboru *. csproj* se přidá element obsahující identifikátor GUID:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    
    <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

</Project>
```

#### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

1. Otevřete soubor *. csproj* .

1. Přidejte `UserSecretsId` element do souboru *. csproj* , jak je znázorněno zde. Můžete použít stejný identifikátor GUID, nebo můžete tuto hodnotu nahradit vlastními.

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
    
        <PropertyGroup>
            <TargetFramework>netcoreapp2.1</TargetFramework>
            <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
        </PropertyGroup>
    
        <ItemGroup>
            <PackageReference Include="Microsoft.AspNetCore.App" />
            <PackageReference Include="Microsoft.AspNetCore.Razor.Design" Version="2.1.2" PrivateAssets="All" />
        </ItemGroup>
    
    </Project>
    ```
    
1. Uložte soubor *. csproj* .

---

> [!TIP]
> Další informace o Správci tajných klíčů najdete v tématu [bezpečné úložiště tajných kódů aplikací ve vývoji v ASP.NET Core](/aspnet/core/security/app-secrets).
