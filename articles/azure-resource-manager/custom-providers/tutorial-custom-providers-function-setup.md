---
title: Nastavení Azure Functions
description: Tento kurz se zabývá tím, jak vytvořit aplikaci pro funkce Azure a nastavit ji tak, aby fungovala s vlastními zprostředkovateli Azure.
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: b6984bfdf7ec078d02999bccf77f3592cfbcab27
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75649985"
---
# <a name="set-up-azure-functions-for-azure-custom-providers"></a>Nastavení funkcí Azure pro vlastní zprostředkovatele Azure

Vlastní zprostředkovatel je smlouva mezi Azure a koncovýbod. S vlastními poskytovateli můžete změnit pracovní postupy v Azure. Tento kurz ukazuje, jak nastavit aplikaci funkce Azure tak, aby fungovala jako koncový bod vlastního zprostředkovatele.

## <a name="create-the-azure-function-app"></a>Vytvoření aplikace pro funkce Azure

> [!NOTE]
> V tomto kurzu vytvoříte jednoduchý koncový bod služby, který používá aplikaci funkce Azure. Vlastní zprostředkovatel však můžete použít libovolný veřejně přístupný koncový bod. Alternativy zahrnují Azure Logic Apps, Azure API Management a web apps funkce Azure App Service.

Chcete-li spustit tento kurz, měli byste nejprve postupovat podle kurzu [Vytvořte si první aplikaci funkce Azure na webu Azure Portal](../../azure-functions/functions-create-first-azure-function.md). Tento kurz vytvoří funkci webhooku .NET core, kterou lze upravit na webu Azure Portal. To je také základem pro aktuální výukový program.

## <a name="install-azure-table-storage-bindings"></a>Instalace vazeb úložiště Azure Table

Instalace vazeb úložiště Azure Table:

1. Přejděte na kartu **Integrace** pro httptrigger.
1. Vyberte **+ Nový vstup**.
1. Vyberte **Azure Table Storage**.
1. Pokud ještě není nainstalované, nainstalujte rozšíření Microsoft.Azure.WebJobs.Extensions.Storage.
1. Do pole **Název parametru tabulky** zadejte **tableStorage**.
1. Do pole **Název tabulky** zadejte **myCustomResources**.
1. Chcete-li uložit aktualizovaný vstupní parametr, vyberte **možnost Uložit.**

![Vlastní přehled zprostředkovatele zobrazující vazby tabulek](./media/create-custom-provider/azure-functions-table-bindings.png)

## <a name="update-restful-http-methods"></a>Aktualizovat metody HTTP RESTful

Nastavení funkce Azure tak, aby zahrnovala metody vlastních požadavků restful zprostředkovatele:

1. Přejděte na kartu **Integrace** pro httptrigger.
1. V části **Vybrané metody HTTP**vyberte **GET**, **POST**, **DELETE**a **PUT**.

![Vlastní přehled zprostředkovatele zobrazující metody HTTP](./media/create-custom-provider/azure-functions-http-methods.png)

## <a name="add-azure-resource-manager-nuget-packages"></a>Přidání balíčků NuGet azure resource manageru

> [!NOTE]
> Pokud váš soubor projektu C# chybí v adresáři projektu, můžete jej přidat ručně. Nebo se zobrazí po microsoft.azure.webjobs.extensions.storage rozšíření je nainstalován v aplikaci funkce.

Dále aktualizujte soubor projektu C# tak, aby zahrnoval užitečné knihovny NuGet. Tyto knihovny usnadňují analýzu příchozích požadavků od vlastních poskytovatelů. Postupujte podle pokynů pro [přidání rozšíření z portálu](../../azure-functions/install-update-binding-extensions-manual.md) a aktualizujte soubor projektu Jazyka C# tak, aby obsahoval následující odkazy na balíček:

```xml
<PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
<PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
<PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
```

Následující element XML je ukázkovým souborem projektu jazyka C#:

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

## <a name="next-steps"></a>Další kroky

V tomto kurzu nastavíte aplikaci funkce Azure tak, aby fungovala jako koncový bod vlastního zprostředkovatele Azure.

Informace o tom, jak vytvořit koncový bod vlastního zprostředkovatele RESTful, naleznete [v tématu Návod: Vytváření koncového bodu vlastního zprostředkovatele RESTful](./tutorial-custom-providers-function-authoring.md).

