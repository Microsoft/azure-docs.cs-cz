---
title: Automatizované nasazení virtuálních počítačů s rychlým startem konfigurace Azure App
description: Tento rychlý Start ukazuje, jak použít modul Azure PowerShell a šablony Azure Resource Manager k nasazení úložiště konfigurace aplikace Azure. Pak použijte hodnoty z úložiště k nasazení virtuálního počítače.
author: lisaguthrie
ms.author: lcozzens
ms.date: 08/11/2020
ms.topic: quickstart
ms.service: azure-app-configuration
ms.custom:
- mvc
- subject-armqs
ms.openlocfilehash: 7b7dd00d3495c24733ecdc213e0e25f8bc9640eb
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "88661465"
---
# <a name="quickstart-automated-vm-deployment-with-app-configuration-and-resource-manager-template-arm-template"></a>Rychlý Start: automatizované nasazení virtuálních počítačů s konfigurací aplikace a šablonou Správce prostředků (šablona ARM)

Naučte se používat šablony Azure Resource Manager a Azure PowerShell k nasazení úložiště konfigurace aplikace Azure, přidání hodnot klíč-hodnota do úložiště a použití hodnot klíčového klíče v úložišti k nasazení prostředku Azure, jako je třeba virtuální počítač Azure v tomto příkladu.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="review-the-templates"></a>Kontrola šablon

Šablony použité v tomto rychlém startu jsou ze [šablon Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/). [První šablona](https://azure.microsoft.com/resources/templates/101-app-configuration-store/) vytvoří úložiště konfigurace aplikace:

:::code language="json" source="~/quickstart-templates/101-app-configuration-store/azuredeploy.json" range="1-37" highlight="27-35":::

V této šabloně je definovaný jeden prostředek Azure:

- [Microsoft. AppConfiguration/configurationStores](/azure/templates/microsoft.appconfiguration/2019-10-01/configurationstores): vytvořte úložiště konfigurace aplikace.

[Druhá šablona](https://azure.microsoft.com/resources/templates/101-app-configuration/) vytvoří virtuální počítač s použitím hodnot klíč-hodnota ve Storu. Před provedením tohoto kroku je potřeba přidat klíčové hodnoty pomocí portálu nebo rozhraní příkazového řádku Azure CLI.

:::code language="json" source="~/quickstart-templates/101-app-configuration/azuredeploy.json" range="1-217" highlight="77, 181,189":::

## <a name="deploy-the-templates"></a>Nasazení šablon

### <a name="create-an-app-configuration-store"></a>Vytvoření úložiště konfigurace aplikace

1. Vyberte následující obrázek a přihlaste se k Azure a otevřete šablonu. Šablona vytvoří úložiště konfigurace aplikace.

    [![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration-store%2Fazuredeploy.json)

1. Vyberte nebo zadejte následující hodnoty.

    - **předplatné**: vyberte předplatné Azure, které se používá k vytvoření úložiště konfigurace aplikace.
    - **Skupina prostředků**: vyberte **vytvořit novou** a vytvořte novou skupinu prostředků, pokud nechcete použít existující skupinu prostředků.
    - **Oblast**: vyberte umístění pro skupinu prostředků.  Například **východní USA**.
    - **Název úložiště**konfigurace: zadejte nový název úložiště konfigurace aplikace.
    - **Umístění**: zadejte umístění úložiště konfigurace aplikace.  Použijte výchozí hodnotu.
    - **Název SKU**: zadejte název SKU úložiště konfigurace aplikace. Použijte výchozí hodnotu.

1. Vyberte **Zkontrolovat a vytvořit**.
1. Ověřte, že se na stránce zobrazuje **úspěšné ověření**, a pak vyberte **vytvořit**.

Poznamenejte si název skupiny prostředků a název úložiště konfigurace aplikace.  Tyto hodnoty budete potřebovat při nasazení virtuálního počítače.
### <a name="add-vm-configuration-key-values"></a>Přidat konfigurační klíč virtuálního počítače – hodnoty

Po vytvoření úložiště konfigurace aplikace můžete pomocí Azure Portal nebo Azure CLI přidat klíčové hodnoty do úložiště.

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a pak přejděte do nově vytvořeného úložiště konfigurace aplikace.
1. V nabídce vlevo vyberte **Průzkumník konfigurace** .
1. Vyberte **vytvořit** a přidejte následující páry klíč-hodnota:

   |Klíč|Hodnota|Popisek|
   |-|-|-|
   |windowsOsVersion|2019 – Datacenter|šablona|
   |diskSizeGB|1023|šablona|

   Zachovat **typ obsahu** v prázdném

Pokud chcete použít rozhraní příkazového řádku Azure, přečtěte si téma [práce s klíčovými hodnotami v úložišti konfigurace aplikace Azure](./scripts/cli-work-with-keys.md).

### <a name="deploy-vm-using-stored-key-values"></a>Nasazení virtuálního počítače pomocí uložených hodnot klíčů

Teď, když jste do úložiště přidali klíčové hodnoty, jste připraveni nasadit virtuální počítač pomocí šablony Azure Resource Manager. Šablona odkazuje na klíče **windowsOsVersion** a **diskSizeGB** , které jste vytvořili.

> [!WARNING]
> Šablony ARM nemůžou odkazovat na klíče v úložišti konfigurace aplikace s povoleným privátním odkazem.

1. Vyberte následující obrázek a přihlaste se k Azure a otevřete šablonu. Šablona vytvoří virtuální počítač pomocí uložených hodnot klíčů v úložišti konfigurace aplikace.

    [![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-app-configuration%2Fazuredeploy.json)

1. Vyberte nebo zadejte následující hodnoty.

    - **předplatné**: vyberte předplatné Azure, které se používá k vytvoření virtuálního počítače.
    - **Skupina prostředků**: buď zadejte stejnou skupinu prostředků jako úložiště konfigurace aplikace, nebo vyberte **vytvořit novou** a vytvořte novou skupinu prostředků.
    - **Oblast**: vyberte umístění pro skupinu prostředků.  Například **východní USA**.
    - **Umístění**: zadejte umístění virtuálního počítače. použijte výchozí hodnotu.
    - **Uživatelské jméno správce**: zadejte uživatelské jméno správce pro virtuální počítač.
    - **Heslo správce**: zadejte heslo správce pro virtuální počítač.
    - **Popisek názvu domény**: Zadejte jedinečný název domény.
    - **Název účtu úložiště**: Zadejte jedinečný název účtu úložiště přidruženého k virtuálnímu počítači.
    - **Skupina prostředků úložiště konfigurace aplikace**: zadejte skupinu prostředků, která obsahuje úložiště konfigurace aplikace.
    - **Název úložiště konfigurace aplikace**: zadejte název úložiště konfigurace aplikace Azure.
    - **Klíč SKU virtuálního počítače**: zadejte **windowsOsVersion**.  Toto je název hodnoty klíče, který jste přidali do úložiště.
    - **Disk s velikostí disku**: zadejte **diskSizeGB**. Jedná se o název hodnoty klíče, který jste přidali do úložiště.

1. Vyberte **Zkontrolovat a vytvořit**.
1. Ověřte, že se na stránce zobrazuje **úspěšné ověření**, a pak vyberte **vytvořit**.

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a potom přejděte k nově vytvořenému virtuálnímu počítači.
1. V nabídce vlevo vyberte **Přehled** a ověřte, jestli je **SKU** **2019 – Datacenter**.
1. V nabídce vlevo vyberte **disky** a ověřte, jestli je velikost datového disku **2013**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, úložiště konfigurace aplikace, virtuální počítač a všechny související prostředky. Pokud plánujete používat úložiště konfigurace aplikace nebo virtuální počítač v budoucnu, můžete ho odstranit. Pokud nebudete tuto úlohu nadále používat, odstraňte všechny prostředky vytvořené tímto rychlým startem spuštěním následující rutiny:

```azurepowershell-interactive
Remove-AzResourceGroup `
  -Name $resourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili virtuální počítač pomocí šablony Azure Resource Manager a hodnot klíč-hodnota z konfigurace aplikace Azure.

Další informace o vytváření dalších aplikací s konfigurací aplikace Azure najdete v následujícím článku:

> [!div class="nextstepaction"]
> [Rychlý Start: Vytvoření aplikace ASP.NET Core s využitím konfigurace aplikace Azure](quickstart-aspnet-core-app.md)
