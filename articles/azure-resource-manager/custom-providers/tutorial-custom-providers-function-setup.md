---
title: Nastavení Azure Functions
description: V tomto kurzu se naučíte vytvořit aplikaci Azure Function App a nastavit ji tak, aby fungovala s vlastními poskytovateli Azure.
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 55554678047faeedd16b78dea61a42d50fd59491
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98737316"
---
# <a name="set-up-azure-functions-for-azure-custom-providers"></a>Nastavení Azure Functions pro vlastní zprostředkovatele Azure

Vlastní poskytovatel je kontrakt mezi Azure a koncovým bodem. S vlastními poskytovateli můžete měnit pracovní postupy v Azure. V tomto kurzu se dozvíte, jak nastavit aplikaci Azure Functions tak, aby fungovala jako koncový bod vlastního poskytovatele.

## <a name="create-the-azure-function-app"></a>Vytvoření aplikace Azure Function App

> [!NOTE]
> V tomto kurzu vytvoříte jednoduchý koncový bod služby, který používá aplikaci Azure Function App. Vlastní zprostředkovatel ale může používat libovolný veřejně přístupný koncový bod. K alternativám patří Azure Logic Apps, Azure API Management a Web Apps funkce Azure App Service.

Pokud chcete začít tento kurz, měli byste nejdřív postupovat podle kurzu [Vytvoření první aplikace funkce Azure Functions v Azure Portal](../../azure-functions/functions-get-started.md). Tento kurz vytvoří funkci Webhooku .NET Core, kterou lze upravit v Azure Portal. Základem pro aktuální kurz je také základ.

## <a name="install-azure-table-storage-bindings"></a>Instalace vazeb Azure Table Storage

Postup při instalaci vazeb úložiště tabulek Azure:

1. Přejít na kartu **integrace** pro HttpTrigger.
1. Vyberte **+ Nový vstup**.
1. Vyberte **Azure Table Storage**.
1. Nainstalujte rozšíření Microsoft. Azure. WebJobs. Extensions. Storage, pokud ještě není nainstalované.
1. Do pole **název parametru tabulky** zadejte **tableStorage**.
1. Do pole **název tabulky** zadejte **myCustomResources**.
1. Výběrem **Uložit** Uložte aktualizovaný vstupní parametr.

![Přehled vlastního zprostředkovatele zobrazujícího vazby tabulek](./media/create-custom-provider/azure-functions-table-bindings.png)

## <a name="update-restful-http-methods"></a>Aktualizace metod HTTP RESTful

Pokud chcete nastavit funkci Azure, aby zahrnovala metody žádosti RESTful vlastního zprostředkovatele:

1. Přejít na kartu **integrace** pro HttpTrigger.
1. V části **vybrané metody HTTP** vyberte **Get**, **post**, **Delete** a **Put**.

![Přehled vlastního zprostředkovatele zobrazujícího metody HTTP](./media/create-custom-provider/azure-functions-http-methods.png)

## <a name="add-azure-resource-manager-nuget-packages"></a>Přidat Azure Resource Manager balíčky NuGet

> [!NOTE]
> Pokud soubor projektu C# chybí v adresáři projektu, můžete ho přidat ručně. Případně se zobrazí po instalaci rozšíření Microsoft. Azure. WebJobs. Extensions. Storage do aplikace Function App.

Dále aktualizujte soubor projektu C# tak, aby zahrnoval užitečné knihovny NuGet. Tyto knihovny usnadňují analýzu příchozích požadavků od vlastních poskytovatelů. Použijte postup [Přidání rozšíření z portálu](../../azure-functions/functions-bindings-register.md) a aktualizujte soubor projektu C# tak, aby zahrnoval následující odkazy na balíčky:

```xml
<PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
<PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
<PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
```

Následující element XML je příkladem souboru projektu jazyka C#:

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

V tomto kurzu nastavíte aplikaci funkcí Azure, která bude fungovat jako koncový bod vlastního zprostředkovatele Azure.

Další informace o tom, jak vytvořit RESTful koncový bod vlastního zprostředkovatele, najdete v tématu [kurz: vytváření koncového bodu vlastního zprostředkovatele RESTful](./tutorial-custom-providers-function-authoring.md).