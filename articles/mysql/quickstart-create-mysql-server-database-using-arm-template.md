---
title: 'Rychlý Start: Vytvoření šablony Azure DB pro MySQL – ARM'
description: V tomto rychlém startu se dozvíte, jak vytvořit server Azure Database for MySQL s integrací virtuální sítě pomocí Azure Resource Manager šablony.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 05/19/2020
ms.openlocfilehash: 0e7fcf51d9c663ca4a289f54972f00ef037cb323
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542265"
---
# <a name="quickstart-use-an-arm-template-to-create-an-azure-database-for-mysql-server"></a>Rychlý Start: použití šablony ARM k vytvoření serveru Azure Database for MySQL

Azure Database for MySQL je spravovaná služba, pomocí které můžete spouštět, spravovat a škálovat vysoce dostupné databáze MySQL v cloudu. V tomto rychlém startu použijete šablonu Azure Resource Manager (šablona ARM) k vytvoření serveru Azure Database for MySQL s integrací virtuální sítě. Server můžete vytvořit v Azure Portal, Azure CLI nebo Azure PowerShell.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Nasazení do Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-mysql-with-vnet%2fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/).

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

* Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/).
* Pokud chcete spustit kód místně, [Azure PowerShell](/powershell/azure/).

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

* Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/).
* Pokud chcete spustit kód místně, [Azure CLI](/cli/azure/).

---

## <a name="review-the-template"></a>Kontrola šablony

Server Azure Database for MySQL vytvoříte s definovanou sadou výpočetních prostředků a prostředků úložiště. Další informace najdete v tématu [Azure Database for MySQL cenové úrovně](concepts-pricing-tiers.md). Server vytvoříte v rámci [skupiny prostředků Azure](../azure-resource-manager/management/overview.md).

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-managed-mysql-with-vnet/).

:::code language="json" source="~/quickstart-templates/101-managed-mysql-with-vnet/azuredeploy.json":::

Šablona definuje pět prostředků Azure:

* [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
* [**Microsoft. Network/virtualNetworks/podsítí**](/azure/templates/microsoft.network/virtualnetworks/subnets)
* [**Microsoft. DBforMySQL/servery**](/azure/templates/microsoft.dbformysql/servers)
* [**Microsoft. DBforMySQL/servery/virtualNetworkRules**](/azure/templates/microsoft.dbformysql/servers/virtualnetworkrules)
* [**Microsoft. DBforMySQL/servery/firewallRules**](/azure/templates/microsoft.dbformysql/servers/firewallrules)

Další příklady šablon Azure Database for MySQL najdete v [galerii šablon rychlý Start](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Dbformysql&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Nasazení šablony

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Vyberte následující odkaz pro nasazení šablony Azure Database for MySQL serveru do Azure Portal:

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Nasazení do Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-managed-mysql-with-vnet%2fazuredeploy.json)

Na stránce **nasadit Azure Database for MySQL se sítí VNET** :

1. V poli **Skupina prostředků** vyberte **vytvořit novou** , zadejte název nové skupiny prostředků a vyberte **OK**.

2. Pokud jste vytvořili novou skupinu prostředků, vyberte **umístění** pro skupinu prostředků a nový server.

3. Zadejte **název serveru** , **přihlašovací jméno správce** a **přihlašovací heslo správce**.

    :::image type="content" source="./media/quickstart-create-mysql-server-database-using-arm-template/deploy-azure-database-for-mysql-with-vnet.png" alt-text="Nasazení Azure Database for MySQL s oknem VNet, šablonou Azure pro rychlý Start, Azure Portal":::

4. Pokud chcete, změňte další výchozí nastavení:

    * **Předplatné** : předplatné Azure, které chcete použít pro server.
    * **Kapacita SKU** : kapacita Vcore, která může být *2* (výchozí), *4* , *8* , *16* , *32* nebo *64*.
    * **Název SKU** : Předpona úrovně SKU, rodina SKU a kapacita skladové položky, které jsou spojeny podtržítky, například *B_Gen5_1* , *GP_Gen5_2* (výchozí) nebo *MO_Gen5_32*.
    * **Velikost SKU MB** : velikost úložiště pro Azure Database for MySQL server (standardně *5120* ) v megabajtech.
    * **Úroveň SKU** : vrstva nasazení, jako je například *Basic* , *GeneralPurpose* (výchozí), nebo *MemoryOptimized*.
    * **Rodina SKU** : *COMPUTE GEN4 –* nebo *Gen5* (výchozí), která indikuje generování hardwaru pro nasazení serveru.
    * **MySQL verze** : verze serveru MySQL, která se má nasadit, například *5,6* nebo *5,7* (výchozí nastavení).
    * **Dny uchovávání záloh** : požadované období pro uchování geograficky redundantního zálohování ve dnech (výchozí *7* ).
    * **Geograficky redundantní zálohování** : *povoleno* nebo *zakázáno* (výchozí nastavení), v závislosti na geograficky se zotavení po havárii (GEO-Dr).
    * **Virtual Network název** : název virtuální sítě (výchozí *azure_mysql_vnet* ).
    * **Název podsítě** : název podsítě (výchozí *azure_mysql_subnet* ).
    * **Virtual Network název pravidla** : název pravidla virtuální sítě, které povoluje podsíť (výchozí *AllowSubnet* ).
    * **Předpona adresy virtuální** sítě: Předpona adresy virtuální sítě (výchozí *10.0.0.0/16* ).
    * **Předpona podsítě** : Předpona adresy pro podsíť (výchozí *10.0.0.0/16* ).

5. Přečtěte si podmínky a ujednání a potom vyberte Souhlasím **s výše uvedenými podmínkami a ujednáními**.

6. Vyberte **Koupit**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Pomocí následujícího interaktivního kódu vytvořte nový Azure Database for MySQL server pomocí šablony. Kód vás vyzve k zadání nového názvu serveru, názvu a umístění nové skupiny prostředků a jména a hesla účtu správce.

Chcete-li spustit kód v Azure Cloud Shell, vyberte **vyzkoušet** v horním rohu libovolného bloku kódu.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter a name for the new Azure Database for MySQL server"
$resourceGroupName = Read-Host -Prompt "Enter a name for the new resource group where the server will exist"
$location = Read-Host -Prompt "Enter an Azure region (for example, centralus) for the resource group"
$adminUser = Read-Host -Prompt "Enter the Azure Database for MySQL server's administrator account name"
$adminPassword = Read-Host -Prompt "Enter the administrator password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location # Use this command when you need to create a new resource group for your deployment
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-mysql-with-vnet/azuredeploy.json `
    -serverName $serverName `
    -administratorLogin $adminUser `
    -administratorLoginPassword $adminPassword

Read-Host -Prompt "Press [ENTER] to continue ..."
```

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

Pomocí následujícího interaktivního kódu vytvořte nový Azure Database for MySQL server pomocí šablony. Kód vás vyzve k zadání nového názvu serveru, názvu a umístění nové skupiny prostředků a jména a hesla účtu správce.

Chcete-li spustit kód v Azure Cloud Shell, vyberte **vyzkoušet** v horním rohu libovolného bloku kódu.

```azurecli-interactive
echo "Enter a name for the new Azure Database for MySQL server:" &&
read serverName &&
echo "Enter a name for the new resource group where the server will exist:" &&
read resourceGroupName &&
echo "Enter an Azure region (for example, centralus) for the resource group:" &&
read location &&
echo "Enter the Azure Database for MySQL server's administrator account name:" &&
read adminUser &&
echo "Enter the administrator password:" &&
read adminPassword &&
params='serverName='$serverName' administratorLogin='$adminUser' administratorLoginPassword='$adminPassword &&
az group create --name $resourceGroupName --location $location &&
az deployment group create --resource-group $resourceGroupName --parameters $params --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-managed-mysql-with-vnet/azuredeploy.json &&
echo "Press [ENTER] to continue ..."
```

---

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Pomocí těchto kroků můžete zobrazit přehled nového serveru Azure Database for MySQL:

1. V [Azure Portal](https://portal.azure.com)vyhledejte a vyberte **Azure Database for MySQL servery**.

2. V seznamu databáze vyberte nový server. Zobrazí se stránka s **přehledem** nového serveru Azure Database for MySQL.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

Chcete-li zobrazit podrobnosti o serveru Azure Database for MySQL, spusťte následující interaktivní kód. Bude nutné zadat název nového serveru.

```azurepowershell-interactive
$serverName = Read-Host -Prompt "Enter the name of your Azure Database for MySQL server"
Get-AzResource -ResourceType "Microsoft.DBforMySQL/servers" -Name $serverName | ft
Write-Host "Press [ENTER] to continue..."
```

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

Chcete-li zobrazit podrobnosti o serveru Azure Database for MySQL, spusťte následující interaktivní kód. Budete muset zadat název a skupinu prostředků nového serveru.

```azurecli-interactive
echo "Enter your Azure Database for MySQL server name:" &&
read serverName &&
echo "Enter the resource group where the Azure Database for MySQL server exists:" &&
read resourcegroupName &&
az resource show --resource-group $resourcegroupName --name $serverName --resource-type "Microsoft.DbForMySQL/servers"
```

---

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, která odstraní prostředky ve skupině prostředků.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. V [Azure Portal](https://portal.azure.com)vyhledejte a vyberte **skupiny prostředků**.

2. V seznamu Skupina prostředků vyberte název vaší skupiny prostředků.

3. Na stránce **Přehled** vaší skupiny prostředků vyberte **Odstranit skupinu prostředků**.

4. V potvrzovacím dialogovém okně zadejte název vaší skupiny prostředků a pak vyberte **Odstranit**.

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

---

## <a name="next-steps"></a>Další kroky

Podrobný kurz, který vás provede procesem vytvoření šablony ARM, najdete v těchto tématech:

> [!div class="nextstepaction"]
> [ Kurz: vytvoření a nasazení první šablony ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
