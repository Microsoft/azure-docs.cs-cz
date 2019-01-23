---
title: Vytvoření účtu úložiště Azure Data Lake Storage Gen2 | Dokumentace Microsoftu
description: Rychle se naučíte vytvořit nový účet úložiště s přístupem k Data Lake Storage Gen2 pomocí webu Azure portal, prostředí Azure PowerShell nebo rozhraní příkazového řádku Azure
services: storage
author: jamesbak
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: quickstart
ms.date: 12/06/2018
ms.author: jamesbak
ms.openlocfilehash: c32e79098cb50600e10d353392e8ceb13d5014b2
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54475029"
---
# <a name="quickstart-create-an-azure-data-lake-storage-gen2-storage-account"></a>Rychlý start: Vytvoření účtu úložiště Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 [podporuje hierarchické službu Namespace](data-lake-storage-introduction.md) poskytující nativní adresářového souborový systém přizpůsobené pro práci s souboru systému HDFS (Hadoop Distributed). Přístup k datům v Data Lake Storage Gen2 je z HDFS možný prostřednictvím [ovladače ABFS](data-lake-storage-abfs-driver.md).

Tento rychlý start ukazuje, jak vytvořit účet pomocí webu [Azure Portal](https://portal.azure.com/), [Azure PowerShellu](https://docs.microsoft.com/powershell/azure/overview) nebo prostřednictvím [Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest).

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete. 

|           | Požadavek |
|-----------|--------------|
|Portál     | Žádný         |
|PowerShell | Tento rychlý start vyžaduje PowerShell verze modulu Az.Storage **0,7** nebo novější. Aktuální verzi zjistíte spuštěním `Get-Module -ListAvailable Az.Storage` příkazu. Pokud po spuštění tohoto příkazu se zobrazí žádné výsledky, nebo pokud verze jiných než **0,7** se zobrazí, pak budete muset upgradovat modul prostředí powershell. Zobrazit [upgradovat modul prostředí powershell](#upgrade-your-powershell-module) části této příručky.
|Rozhraní příkazového řádku        | Pokud se chcete přihlásit k Azure s spouštět příkazy Azure CLI, máte dvě možnosti: <ul><li>Příkazy rozhraní příkazového řádku můžete spouštět na webu Azure Portal ve službě Azure Cloud Shell. </li><li>Můžete nainstalovat rozhraní příkazového řádku a příkazy rozhraní příkazového řádku spouštět místně.</li></ul>|

Při práci s příkazovým řádkem můžete spustit Azure Cloud Shell nebo nainstalovat rozhraní příkazového řádku místně.

### <a name="use-azure-cloud-shell"></a>Použití služby Azure Cloud Shell

Azure Cloud Shell je volně dostupné prostředí Bash, které můžete spustit přímo z webu Azure Portal. Má předinstalované rozhraní Azure CLI, které je nakonfigurované pro použití s vaším účtem. Klikněte na tlačítko **Cloud Shell** v nabídce v pravé horní části webu Azure Portal:

[![Cloud Shell](./media/data-lake-storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

Toto tlačítko spustí interaktivní prostředí, které můžete použít k provedení kroků v tomto rychlém startu:

[![Snímek obrazovky okna služby Cloud Shell na portálu](./media/data-lake-storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Místní instalace rozhraní příkazového řádku

Azure CLI můžete také nainstalovat a používat místně. Tento rychlý start vyžaduje použití Azure CLI verze 2.0.38 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-storage-account-with-azure-data-lake-storage-gen2-enabled"></a>Vytvoření účtu úložiště s povolenou službou Azure Data Lake Storage Gen2

Než vytvoříte účet, musíte nejprve vytvořit skupinu prostředků, která funguje jako logický kontejner účtů úložiště nebo jiných vámi vytvořených prostředků Azure. Pokud chcete vyčistit prostředky vytvořené v tomto rychlém startu, stačí jednoduše odstranit skupinu prostředků. Odstraněním skupiny prostředků odstraníte také přidružený účet úložiště a všechny další prostředky, které jsou k příslušné skupině prostředků přidružené. Další informace o skupinách prostředků najdete v [přehledu Azure Resource Manageru](../../azure-resource-manager/resource-group-overview.md).

> [!NOTE]
> Abyste mohli využít funkce Data Lake Storage Gen2, musí být nově vytvořené účty úložiště typu **StorageV2 (pro obecné účely verze 2)**.  

Další informace o účtech úložiště najdete v [přehledu účtu Azure Storage](../common/storage-account-overview.md).

Při pojmenování účtu úložiště mějte na paměti tato pravidla:

- Názvy účtů úložiště musí mít délku 3 až 24 znaků a můžou obsahovat jenom číslice a malá písmena.
- Název vašeho účtu úložiště musí být jedinečný v rámci Azure. Žádné dva účty úložiště nemůžou mít stejný název.

## <a name="create-an-account-using-the-azure-portal"></a>Vytvoření účtu na webu Azure Portal

Přihlaste se k webu [Azure Portal](https://portal.azure.com).

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Při vytváření skupiny prostředků na webu Azure Portal použijte tento postup:

1. Na webu Azure Portal rozbalením nabídky na levé straně otevřete nabídku služeb a zvolte **Skupiny prostředků**.
2. Kliknutím na tlačítko **Přidat** přidáte novou skupinu prostředků.
3. Zadejte název nové skupiny prostředků.
4. Vyberte předplatné, ve kterém chcete novou skupinu prostředků vytvořit.
5. Vyberte umístění pro tuto skupinu prostředků.
6. Klikněte na tlačítko **Vytvořit**.  

   ![Snímek obrazovky znázorňující vytvoření skupiny prostředků na webu Azure Portal](./media/data-lake-storage-quickstart-create-account/create-resource-group.png)

### <a name="create-a-general-purpose-v2-storage-account"></a>Vytvoření účtu úložiště pro obecné účely verze 2

Pokud chcete vytvořit účet úložiště pro obecné účely verze 2 na webu Azure Portal, postupujte takto:

> [!NOTE]
> Hierarchický obor názvů je v současné době dostupný ve všech veřejných oblastech. Momentálně není k dispozici v suverénních cloudech.

1. Na webu Azure Portal rozbalením nabídky na levé straně otevřete nabídku služeb a zvolte **Všechny služby**. Potom přejděte dolů do části **Úložiště** a zvolte **Účty úložiště**. V okně **Účty úložiště**, které se zobrazí, zvolte **Přidat**.
2. Vyberte vaše **předplatné** a **skupiny prostředků** jste vytvořili dříve.
3. Zadejte název účtu úložiště.
4. Nastavte **Umístění** na **Západní USA 2**.
5. Ponechte tato pole výchozí nastavení: **Výkon**, **druh účtu**, **replikace**, **úroveň přístupu**.
6. Vyberte předplatné, ve kterém chcete vytvořit účet úložiště.
7. Vyberte **Další: Upřesnit >**
8. Ponechte hodnoty v rámci **zabezpečení** a **VIRTUÁLNÍCH sítí** pole výchozí nastavení.
9. V části **Data Lake Storage Gen2 (Preview)** nastavte **Hierarchický obor názvů** na **Povoleno**.
10. Klikněte na tlačítko **revize + vytvořit** k vytvoření účtu úložiště.

    ![Snímek obrazovky znázorňující vytvoření účtu úložiště na webu Azure Portal](./media/data-lake-storage-quickstart-create-account/azure-data-lake-storage-account-create-advanced.png)

Na portálu jste vytvořili účet úložiště.

### <a name="clean-up-resources"></a>Vyčištění prostředků

Odebrání skupiny prostředků pomocí webu Azure Portal:

1. Na webu Azure Portal rozbalením nabídky na levé straně otevřete nabídku služeb a zvolte **Skupiny prostředků**. Zobrazí se seznam skupin prostředků.
2. Vyhledejte skupinu prostředků, kterou chcete odstranit, a klikněte pravým tlačítkem na tlačítko **Další** (**...** ) na pravé straně seznamu.
3. Vyberte **Odstranit skupinu prostředků** a potvrďte tuto akci.

## <a name="create-an-account-using-powershell"></a>Vytvoření účtu pomocí PowerShellu

Nejdřív nainstalujte nejnovější verzi [PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget) modulu.

Potom upgradovat modul prostředí powershell, přihlaste se ke svému předplatnému Azure, vytvořte skupinu prostředků a vytvořte účet úložiště.

### <a name="upgrade-your-powershell-module"></a>Upgrade modulu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

K interakci s Data Lake Storage Gen2 pomocí prostředí PowerShell, budete muset nainstalovat verzi modulu Az.Storage **0,7** nebo novější.

Začněte tak, že otevřete relaci Powershellu se zvýšenými oprávněními.

Instalace modulu Az.Storage

```powershell
Install-Module Az.Storage -Repository PSGallery -RequiredVersion 0.7.0 -AllowPrerelease -AllowClobber -Force
```

> [!NOTE]
> Moduly Azure Powershellu Az jsou upřednostňované moduly pro práci se službami Azure v prostředí Powershell. Další informace najdete v tématu [představení nového modulu Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

### <a name="log-in-to-your-azure-subscription"></a>Přihlaste se ke svému předplatnému Azure

Použití `Login-AzAccount` příkaz a postupujte podle pokynů na obrazovce pokynů k ověření.

```powershell
Login-AzAccount
```

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Chcete-li vytvořit novou skupinu prostředků pomocí Powershellu, použijte [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) příkaz: 

> [!NOTE]
> Hierarchický obor názvů je v současné době dostupný ve všech veřejných oblastech. Momentálně není k dispozici v suverénních cloudech.

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-quickstart-resource-group"
$location = "westus2"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

### <a name="create-a-general-purpose-v2-storage-account"></a>Vytvoření účtu úložiště pro obecné účely verze 2

Chcete-li vytvořit účet úložiště pro obecné účely verze 2 v Powershellu s místně redundantním úložištěm (LRS), použijte [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) příkaz:

```powershell
$location = "westus2"

New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name "storagequickstart" `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind StorageV2 `
  -EnableHierarchicalNamespace $True
```

### <a name="clean-up-resources"></a>Vyčištění prostředků

Chcete-li odebrat skupinu prostředků a její přidružené prostředky, včetně nového účtu úložiště, použijte [odebrat AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) příkaz: 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="create-an-account-using-azure-cli"></a>Vytvoření účtu pomocí Azure CLI

Pokud chcete spustit Azure Cloud Shell, přihlaste se k [webu Azure portal](https://portal.azure.com).

Pokud se chcete přihlásit k místní instalaci rozhraní příkazového řádku, spusťte přihlašovací příkaz:

```cli
az login
```

### <a name="add-the-cli-extension-for-azure-data-lake-gen-2"></a>Přidat rozšíření rozhraní příkazového řádku pro Azure Data Lake Gen 2

K interakci s Data Lake Storage Gen2 pomocí rozhraní příkazového řádku, budete muset přidat rozšíření do vašeho prostředí.

K tomu, zadejte následující příkaz s použitím službě Cloud Shell nebo místní prostředí: `az extension add --name storage-preview`

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Pokud chcete vytvořit novou skupinu prostředků pomocí Azure CLI, použijte příkaz [az group create](/cli/azure/group#az_group_create).

```azurecli-interactive
az group create `
    --name storage-quickstart-resource-group `
    --location westus2
```

> [!NOTE]
> > Hierarchický obor názvů je v současné době dostupný ve všech veřejných oblastech. Momentálně není k dispozici v suverénních cloudech.

### <a name="create-a-general-purpose-v2-storage-account"></a>Vytvoření účtu úložiště pro obecné účely verze 2

Pokud chcete vytvořit účet úložiště pro obecné účely verze 2 pomocí Azure CLI s využitím místně redundantního úložiště, použijte příkaz [az storage account create](/cli/azure/storage/account).

```azurecli-interactive
az storage account create `
    --name storagequickstart `
    --resource-group storage-quickstart-resource-group `
    --location westus2 `
    --sku Standard_LRS `
    --kind StorageV2 `
    --hierarchical-namespace true
```

### <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odebrat skupinu prostředků a její přidružené prostředky, včetně nového účtu úložiště, použijte příkaz [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili účet úložiště s možnostmi Data Lake Storage Gen2. Zjistěte, jak nahrávat a stahovat objekty BLOB z účtu úložiště, najdete v následujícím tématu.

* [AzCopy V10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
