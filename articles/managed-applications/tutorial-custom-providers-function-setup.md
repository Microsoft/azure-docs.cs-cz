---
title: Nastavení funkce Azure pro Azure Vlastní zprostředkovatelé
description: V tomto kurzu se přenášejí prostřednictvím jak vytvořit funkci Azure functions a nastavení pro práci s Azure Vlastní zprostředkovatelé
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: d7e4de43659db88bfd9aad40cc3b9f1753189bba
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67799991"
---
# <a name="setup-azure-functions-for-azure-custom-providers"></a>Nastavení funkce Azure pro Azure Vlastní zprostředkovatelé

Vlastní zprostředkovatelé umožňují přizpůsobit pracovní postupy v Azure. Vlastní zprostředkovatel je kontrakt mezi Azure a `endpoint`. V tomto kurzu se projít procesem nastavení funkce Azure fungovat jako vlastního zprostředkovatele `endpoint`.

V tomto kurzu je rozdělen do následujících kroků:

- Vytvoření funkce Azure functions
- Instalace Azure Table vazby
- Aktualizace metod rozhraní RESTful HTTP.
- Přidání balíčků NuGet pro Azure Resource Manageru

V tomto kurzu budete stavět v následujících kurzech:

- [Vytvoření první funkce Azure na webu Azure portal](../azure-functions/functions-create-first-azure-function.md)

## <a name="creating-the-azure-function"></a>Vytvoření funkce Azure functions

> [!NOTE]
> V tomto kurzu budeme vytvářet koncového bodu služby jednoduché pomocí funkce Azure, ale vlastního zprostředkovatele můžete použít všechny veřejné přístupné `endpoint`. Azure Logic Apps, Azure API Management a Azure Web Apps jsou některé skvělé alternativy.

Pokud chcete spustit tento kurz, postupujte podle kurzu [vytvoření první funkce Azure na webu Azure Portal](../azure-functions/functions-create-first-azure-function.md). Tento kurz vytvoří funkce webhooku .NET core, která lze upravit na webu Azure Portal.

## <a name="install-azure-table-bindings"></a>Instalace Azure Table vazby

Tato část projdou rychlé kroky pro instalaci vazby služby Azure Table storage.

1. Přejděte `Integrate` kartu HttpTrigger.
2. Klikněte na `+ New Input`.
3. Vyberte `Azure Table Storage`.
4. Nainstalujte `Microsoft.Azure.WebJobs.Extensions.Storage` Pokud ještě není nainstalovaná.
5. Aktualizace `Table parameter name` na "tableStorage" a `Table name` na "myCustomResources".
6. Uložte aktualizovaný vstupní parametr.

![Přehled vlastního zprostředkovatele](./media/create-custom-providers/azure-functions-table-bindings.png)

## <a name="update-restful-http-methods"></a>Aktualizace metod rozhraní RESTful HTTP.

Tato část projdou rychlé kroky pro vytvoření funkce Azure Functions zahrnout metody vlastního poskytovatele RESTful požadavků.

1. Přejděte `Integrate` kartu HttpTrigger.
2. Aktualizace `Selected HTTP methods` na: GET, POST, DELETE a PUT.

![Přehled vlastního zprostředkovatele](./media/create-custom-providers/azure-functions-http-methods.png)

## <a name="modifying-the-csproj"></a>Úpravy csproj

> [!NOTE]
> Pokud csproj se v adresáři nebyl nalezen, je možné přidat ručně nebo se jednou zobrazí `Microsoft.Azure.WebJobs.Extensions.Storage` rozšíření je nainstalované na funkci.

V dalším kroku budeme aktualizovat soubor csproj zahrnout užitečné knihovny NuGet, které vám usnadní analýzu příchozích požadavků z vlastního zprostředkovatele. Postupujte podle kroků uvedených v [přidat rozšíření z portálu](../azure-functions/install-update-binding-extensions-manual.md) a aktualizaci souboru csproj zahrnout následující odkazy na balíček:

```xml
<PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
<PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
<PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
```

Ukázkový soubor csproj:

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <TargetFramework>netstandard2.0</TargetFramework>
    <WarningsAsErrors />
  </PropertyGroup>
  <ItemGroup>
    <PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
    <PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
    <PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
  </ItemGroup>
</Project>
```

## <a name="next-steps"></a>Další postup

V tomto článku nastavíme funkce Azure fungovat jako vlastního zprostředkovatele Azure `endpoint`. Pokračujte dalším článku se dozvíte, jak si můžete vytvořit vlastního poskytovatele RESTful `endpoint`.

- [Kurz: Koncový bod RESTful vlastního zprostředkovatele pro tvorbu](./tutorial-custom-providers-function-authoring.md)
