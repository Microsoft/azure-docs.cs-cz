---
title: Vytvoření partnerského vztahu virtuálních sítí – různá předplatná
titlesuffix: Azure Virtual Network
description: Naučte se, jak vytvořit partnerský vztah virtuální sítě mezi virtuálními sítěmi vytvořenými prostřednictvím Správce prostředků, které existují v různých předplatných Azure ve stejném nebo jiném tenantovi Azure Active Directory.
services: virtual-network
documentationcenter: ''
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/09/2019
ms.author: kumud
ms.openlocfilehash: 1f8b7162787e870337918794f353ab8dc94c1965
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102518241"
---
# <a name="create-a-virtual-network-peering---resource-manager-different-subscriptions-and-azure-active-directory-tenants"></a>Vytvoření partnerského vztahu virtuální sítě – Správce prostředků, různých předplatných a klientů Azure Active Directory

V tomto kurzu se naučíte vytvořit partnerský vztah virtuální sítě mezi virtuálními sítěmi vytvořenými prostřednictvím Správce prostředků. Virtuální sítě existují v různých předplatných, které můžou patřit do různých tenantů Azure Active Directory (Azure AD). Partnerský vztah dvou virtuálních sítí umožňuje prostředkům v různých virtuálních sítích vzájemně komunikovat se stejnou šířkou pásma a latencí, jako kdyby byly prostředky ve stejné virtuální síti. Přečtěte si další informace o [partnerském vztahu virtuálních sítí](virtual-network-peering-overview.md).

Postup vytvoření partnerského vztahu virtuální sítě se liší v závislosti na tom, jestli jsou virtuální sítě ve stejném nebo jiném předplatném, a na [modelu nasazení Azure](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , prostřednictvím kterého se virtuální sítě vytvářejí. Naučte se vytvořit partnerský vztah virtuálních sítí v jiných scénářích výběrem scénáře z následující tabulky:

|Model nasazení Azure  | Předplatné Azure  |
|--------- |---------|
|[Obě Resource Manager](tutorial-connect-virtual-networks-portal.md) |Stejné|
|[Jedna Resource Manager, druhá Classic](create-peering-different-deployment-models.md) |Stejné|
|[Jedna Resource Manager, druhá Classic](create-peering-different-deployment-models-subscriptions.md) |Různé|

Partnerský vztah virtuální sítě nelze vytvořit mezi dvěma virtuálními sítěmi nasazenými prostřednictvím modelu nasazení Classic. Pokud potřebujete propojit virtuální sítě, které byly vytvořeny prostřednictvím modelu nasazení Classic, můžete k propojení virtuálních sítí použít [VPN Gateway](../vpn-gateway/tutorial-site-to-site-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Azure.

Tento kurz je virtuálními sítěmi ve stejné oblasti. Můžete také vytvořit partnerský vztah virtuálních sítí v různých [podporovaných oblastech](virtual-network-manage-peering.md#cross-region). Doporučujeme, abyste se seznámili s [požadavky a omezeními partnerských vztahů](virtual-network-manage-peering.md#requirements-and-constraints) před vytvořením partnerského vztahu virtuálních sítí.

K vytvoření partnerského vztahu virtuálních sítí můžete použít [Azure Portal](#portal), [rozhraní příkazového řádku](#cli) Azure (CLI), azure [PowerShell](#powershell)nebo [šablonu Azure Resource Manager](#template) . Vyberte některou z předchozích odkazů nástrojů, abyste přešli přímo k postupu při vytváření partnerského vztahu virtuálních sítí pomocí vašeho nástroje podle vlastního výběru.

Pokud jsou virtuální sítě v různých předplatných a odběry jsou přidružené k různým klientům Azure Active Directory, před pokračováním proveďte následující kroky:
1. Přidejte uživatele z každého tenanta služby Active Directory jako [uživatele typu Host](../active-directory/external-identities/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) v opačném Azure Active Directory tenantovi.
1. Každý uživatel musí přijmout pozvánku uživatele typu host z opačného tenanta Azure Active Directory.

## <a name="create-peering---azure-portal"></a><a name="portal"></a>Vytvoření partnerských vztahů – Azure Portal

Následující kroky používají pro každé předplatné různé účty. Pokud používáte účet, který má oprávnění k oběma předplatným, můžete použít stejný účet pro všechny kroky, přeskočit kroky pro odhlášení z portálu a přeskočit kroky pro přiřazení dalších uživatelských oprávnění k virtuálním sítím.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako *Usera*. Účet, se kterým se přihlašujete, musí mít potřebná oprávnění k vytvoření partnerského vztahu virtuální sítě. Seznam oprávnění najdete v tématu [oprávnění partnerského vztahu virtuálních sítí](virtual-network-manage-peering.md#permissions).
2. Vyberte **+ vytvořit prostředek**, vyberte **sítě** a pak vyberte **virtuální síť**.
3. Vyberte nebo zadejte následující příklady hodnot pro následující nastavení a pak vyberte **vytvořit**:
    - **Název**: *myVnetA*
    - **Adresní prostor**: *10.0.0.0/16*
    - **Název podsítě**: *výchozí*
    - **Rozsah adres podsítě**: *10.0.0.0/24*
    - **Předplatné**: vyberte předplatné A.
    - **Skupina prostředků**: vyberte **vytvořit novou** a zadejte *myResourceGroupA* .
    - **Umístění**: *východní USA*
4. Do pole **Hledat prostředky** v horní části portálu zadejte *myVnetA*. Pokud se zobrazí ve výsledcích hledání, vyberte **myVnetA** .
5. V seznamu svislých možností na levé straně vyberte **řízení přístupu (IAM)** .
6. V části **řízení přístupu myVnetA (IAM)** vyberte **+ Přidat přiřazení role**.
7. V poli **role** vyberte **Přispěvatel sítě** .
8. V poli **Vybrat** vyberte *UserB* nebo zadejte e-mailovou adresu UserB, kterou chcete vyhledat.
9. Vyberte **Uložit**.
10. V části **myVnetA-Access Control (IAM)** vyberte **vlastnosti** ze svislého seznamu možností na levé straně. Zkopírujte **ID prostředku**, které se používá v pozdějším kroku. ID prostředku je podobné jako v následujícím příkladu: `/subscriptions/<Subscription Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/virtualNetworks/myVnetA` .
11. Odhlaste se z portálu jako UserA a pak se přihlaste jako UserB.
12. Proveďte kroky 2-3, zadejte nebo vyberte následující hodnoty v kroku 3:

    - **Název**: *myVnetB*
    - **Adresní prostor**: *10.1.0.0/16*
    - **Název podsítě**: *výchozí*
    - **Rozsah adres podsítě**: *10.1.0.0/24*
    - **Předplatné**: vyberte předplatné B.
    - **Skupina prostředků**: vyberte **vytvořit novou** a zadejte *myResourceGroupB* .
    - **Umístění**: *východní USA*

13. Do pole **Hledat prostředky** v horní části portálu zadejte *myVnetB*. Pokud se zobrazí ve výsledcích hledání, vyberte **myVnetB** .
14. V části **myVnetB** vyberte **vlastnosti** ze svislého seznamu možností na levé straně. Zkopírujte **ID prostředku**, které se používá v pozdějším kroku. ID prostředku je podobné jako v následujícím příkladu: `/subscriptions/<Subscription ID>/resourceGroups/myResourceGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB` .
15. V části **myVnetB** vyberte **řízení přístupu (IAM)** a pak proveďte kroky 5-10 pro myVnetB, a to zadáním **Usera** v kroku 8.
16. Odhlaste se z portálu jako UserB a přihlaste se jako UserA.
17. Do pole **Hledat prostředky** v horní části portálu zadejte *myVnetA*. Pokud se zobrazí ve výsledcích hledání, vyberte **myVnetA** .
18. Vyberte **myVnetA**.
19. V části **Nastavení** vyberte **partnerské vztahy**.
20. V části **myVnetA-peering** vyberte **+ Přidat** .
21. V části **Přidat partnerský vztah** zadejte nebo vyberte následující možnosti a pak vyberte **OK**:
     - **Název**: *myVnetAToMyVnetB*
     - **Model nasazení virtuální sítě**: vyberte **Správce prostředků**.
     - **Znám moje ID prostředku**: zaškrtněte toto políčko.
     - **ID prostředku**: Zadejte ID prostředku z kroku 14.
     - **Povolení přístupu k virtuální síti:** Ujistěte se, že je vybraná možnost **povoleno** .
    V tomto kurzu se žádná další nastavení nepoužívají. Pokud se chcete dozvědět o všech nastaveních partnerských vztahů, přečtěte si téma [Správa partnerských vztahů virtuálních sítí](virtual-network-manage-peering.md#create-a-peering).
22. Partnerský vztah, který jste vytvořili, se zobrazí po výběru **OK** v předchozím kroku o krátké čekání. Datum **zahájení** se zobrazí ve sloupci **stav partnerského vztahu** pro partnerský vztah **myVnetAToMyVnetB** , který jste vytvořili. Nastavili jste partnerský vztah myVnetA k myVnetB, ale teď musíte mít rovnocenné myVnetB pro myVnetA. Partnerský vztah se musí vytvořit v obou směrech, aby bylo možné vzájemně komunikovat prostředky ve virtuálních sítích.
23. Odhlaste se z portálu jako UserA a přihlaste se jako UserB.
24. Proveďte znovu kroky 17-21 pro myVnetB. V kroku 21 pojmenujte partnerský vztah *myVnetBToMyVnetA*, vyberte *myVnetA* pro **virtuální síť** a v poli **ID prostředku** zadejte ID z kroku 10.
25. Pár sekund po výběru **OK** pro vytvoření partnerského vztahu pro myVnetB, partnerský vztah **myVnetBToMyVnetA** , který jste právě vytvořili, je uvedený **ve sloupci** **stav partnerského vztahu** .
26. Odhlaste se z portálu jako UserB a přihlaste se jako UserA.
27. Proveďte znovu kroky 17-19. **Stav partnerského** vztahu pro partnerský vztah **MyVnetAToVNetB** je teď **připojený** taky. Partnerský vztah se úspěšně naváže po zobrazení **připojení** ve sloupci **stav partnerského vztahu** pro obě virtuální sítě v partnerském vztahu. Všechny prostředky Azure, které vytvoříte v obou virtuálních sítích, teď můžou vzájemně komunikovat prostřednictvím jejich IP adres. Pokud pro virtuální sítě používáte výchozí překlad názvů Azure, prostředky ve virtuálních sítích nebudou schopné překládat názvy mezi virtuálními sítěmi. Pokud chcete přeložit názvy mezi virtuálními sítěmi v partnerském vztahu, musíte vytvořit vlastní server DNS. Přečtěte si, jak nastavit [překlad názvů pomocí vlastního serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
28. **Volitelné**: i když se v tomto kurzu nezabývá vytvářením virtuálních počítačů, můžete vytvořit virtuální počítač v každé virtuální síti a připojit se z jednoho virtuálního počítače k druhému a ověřit připojení.
29. **Volitelné**: Chcete-li odstranit prostředky, které vytvoříte v tomto kurzu, proveďte kroky v části [odstranění prostředků](#delete-portal) v tomto článku.

## <a name="create-peering---azure-cli"></a><a name="cli"></a>Vytvoření partnerského vztahu – Azure CLI

V tomto kurzu se pro každé předplatné používá jiný účet. Pokud používáte účet, který má oprávnění k oběma předplatným, můžete použít stejný účet pro všechny kroky, přeskočit kroky pro odhlášení z Azure a odebrat řádky skriptu, které vytvářejí přiřazení role uživatele. Nahraďte UserA@azure.com a UserB@azure.com ve všech následujících skriptech s uživatelskými jmény, která používáte pro Usera a UserB.

Následující skripty:

- Vyžaduje Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete upgrade, přečtěte si téma [instalace Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Funguje v prostředí bash. Možnosti spuštění skriptů Azure CLI na klientovi s Windows najdete v tématu věnovaném [instalaci Azure CLI ve Windows](/cli/azure/install-azure-cli-windows).

Místo instalace rozhraní příkazového řádku a jeho závislostí můžete použít Azure Cloud Shell. Služba Azure Cloud Shell je volně dostupné prostředí Bash, které můžete spustit přímo z portálu Azure Portal. Má předinstalované rozhraní Azure CLI, které je nakonfigurované pro použití s vaším účtem. Ve skriptu, který následuje, vyberte tlačítko **vyzkoušet** , které vyvolá Cloud Shell, které se můžete přihlásit ke svému účtu Azure pomocí.

1. Otevřete relaci CLI a přihlaste se k Azure jako UserA pomocí `azure login` příkazu. Účet, se kterým se přihlašujete, musí mít potřebná oprávnění k vytvoření partnerského vztahu virtuální sítě. Seznam oprávnění najdete v tématu [oprávnění partnerského vztahu virtuálních sítí](virtual-network-manage-peering.md#permissions).
2. Zkopírujte následující skript do textového editoru na počítači, nahraďte `<SubscriptionA-Id>` ID předplatného, pak zkopírujte upravený skript, vložte ho do relace CLI a stiskněte klávesu `Enter` . Pokud své ID předplatného neznáte, zadejte `az account show` příkaz. Hodnota pro **ID** ve výstupu je vaše ID vašeho předplatného.

    ```azurecli-interactive
    # Create a resource group.
    az group create \
      --name myResourceGroupA \
      --location eastus

    # Create virtual network A.
    az network vnet create \
      --name myVnetA \
      --resource-group myResourceGroupA \
      --location eastus \
      --address-prefix 10.0.0.0/16

    # Assign UserB permissions to virtual network A.
    az role assignment create \
      --assignee UserB@azure.com \
      --role "Network Contributor" \
      --scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

3. Odhlaste se z Azure jako UserA pomocí `az logout` příkazu a pak se přihlaste k Azure jako UserB. Účet, se kterým se přihlašujete, musí mít potřebná oprávnění k vytvoření partnerského vztahu virtuální sítě. Seznam oprávnění najdete v tématu [oprávnění partnerského vztahu virtuálních sítí](virtual-network-manage-peering.md#permissions).
4. Vytvořte myVnetB. Zkopírujte obsah skriptu v kroku 2 do textového editoru v počítači. Nahraďte `<SubscriptionA-Id>` ID SubscriptionB. Změňte 10.0.0.0/16 na 10.1.0.0/16, změňte vše na B a všechny BS na. zkopírujte upravený skript, vložte ho do relace CLI a stiskněte `Enter` .
5. Odhlaste se z Azure jako UserB a přihlaste se k Azure jako UserA.
6. Vytvořte partnerský vztah virtuálních sítí z myVnetA do myVnetB. Zkopírujte následující obsah skriptu do textového editoru na svém počítači. Nahraďte `<SubscriptionB-Id>` ID SubscriptionB. Skript spustíte tak, že zkopírujete upravený skript, vložíte ho do relace CLI a stisknete klávesu ENTER.

    ```azurecli-interactive
        # Get the id for myVnetA.
        vnetAId=$(az network vnet show \
          --resource-group myResourceGroupA \
          --name myVnetA \
          --query id --out tsv)

        # Peer myVNetA to myVNetB.
        az network vnet peering create \
          --name myVnetAToMyVnetB \
          --resource-group myResourceGroupA \
          --vnet-name myVnetA \
          --remote-vnet /subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/VirtualNetworks/myVnetB \
          --allow-vnet-access
    ```

7. Zobrazte stav partnerského vztahu pro myVnetA.

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroupA \
      --vnet-name myVnetA \
      --output table
    ```

    Stav je **zahájeno**. Po vytvoření partnerského vztahu pro myVnetA z myVnetB se změní na **připojeno** .

8. Odhlaste se ze služby UserA z Azure a přihlaste se k Azure jako UserB.
9. Vytvořte partnerský vztah z myVnetB do myVnetA. Zkopírujte obsah skriptu v kroku 6 do textového editoru v počítači. Nahraďte `<SubscriptionB-Id>` ID předplatného a změňte vše jako na B a všechny BS na. Po provedení změn zkopírujte upravený skript, vložte jej do relace CLI a stiskněte klávesu `Enter` .
10. Zobrazte stav partnerského vztahu pro myVnetB. Zkopírujte obsah skriptu v kroku 7 do textového editoru v počítači. Změňte hodnotu na B pro název skupiny prostředků a virtuální sítě, zkopírujte skript, vložte upravený skript do relace CLI a potom stiskněte klávesu `Enter` . Stav partnerského vztahu je **připojen**. Stav partnerského vztahu myVnetA se změní na **připojeno** po vytvoření partnerského vztahu z MyVnetB do myVnetA. UserA se můžete přihlásit zpátky do Azure a dokončit krok 7 a ověřit stav partnerského vztahu pro myVnetA.

    > [!NOTE]
    > Partnerský vztah není navázán, dokud stav partnerského vztahu není **připojen** k oběma virtuálním sítím.

11. **Volitelné**: i když se v tomto kurzu nezabývá vytvářením virtuálních počítačů, můžete vytvořit virtuální počítač v každé virtuální síti a připojit se z jednoho virtuálního počítače k druhému a ověřit připojení.
12. **Volitelné**: Chcete-li odstranit prostředky, které vytvoříte v tomto kurzu, proveďte kroky v části [odstranění prostředků](#delete-cli) v tomto článku.

Všechny prostředky Azure, které vytvoříte v obou virtuálních sítích, teď můžou vzájemně komunikovat prostřednictvím jejich IP adres. Pokud pro virtuální sítě používáte výchozí překlad názvů Azure, prostředky ve virtuálních sítích nebudou schopné překládat názvy mezi virtuálními sítěmi. Pokud chcete přeložit názvy mezi virtuálními sítěmi v partnerském vztahu, musíte vytvořit vlastní server DNS. Přečtěte si, jak nastavit [překlad názvů pomocí vlastního serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

## <a name="create-peering---powershell"></a><a name="powershell"></a>Vytvoření partnerského vztahu – PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

V tomto kurzu se pro každé předplatné používá jiný účet. Pokud používáte účet, který má oprávnění k oběma předplatným, můžete použít stejný účet pro všechny kroky, přeskočit kroky pro odhlášení z Azure a odebrat řádky skriptu, které vytvářejí přiřazení role uživatele. Nahraďte UserA@azure.com a UserB@azure.com ve všech následujících skriptech s uživatelskými jmény, která používáte pro Usera a UserB.

1. Potvrďte, že máte Azure PowerShell verze 1.0.0 nebo novější. Můžete to provést spuštěním služby doporučujeme `Get-Module -Name Az` nainstalovat nejnovější verzi [modulu PowerShell AZ Module](/powershell/azure/install-az-ps). Pokud s Azure PowerShellem začínáte, podívejte se na [Přehled Azure PowerShellu](/powershell/azure/?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Spusťte relaci PowerShellu.
3. V PowerShellu se přihlaste k Azure jako UserA zadáním `Connect-AzAccount` příkazu. Účet, se kterým se přihlašujete, musí mít potřebná oprávnění k vytvoření partnerského vztahu virtuální sítě. Seznam oprávnění najdete v tématu [oprávnění partnerského vztahu virtuálních sítí](virtual-network-manage-peering.md#permissions).
4. Vytvořte skupinu prostředků a virtuální síť A. Zkopírujte následující skript do textového editoru na svém počítači. Nahraďte `<SubscriptionA-Id>` ID předplatného. Pokud své ID předplatného neznáte, zadejte `Get-AzSubscription` příkaz, který chcete zobrazit. Hodnota **ID** ve vráceném výstupu je vaše ID vašeho předplatného. Skript spustíte tak, že zkopírujete upravený skript, vložíte ho do PowerShellu a pak stisknete `Enter` .

    ```powershell
    # Create a resource group.
    New-AzResourceGroup `
      -Name MyResourceGroupA `
      -Location eastus

    # Create virtual network A.
    $vNetA = New-AzVirtualNetwork `
      -ResourceGroupName MyResourceGroupA `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus

    # Assign UserB permissions to myVnetA.
    New-AzRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

5. Odhlaste se ze služby UserA z Azure a přihlaste se UserB. Účet, se kterým se přihlašujete, musí mít potřebná oprávnění k vytvoření partnerského vztahu virtuální sítě. Seznam oprávnění najdete v tématu [oprávnění partnerského vztahu virtuálních sítí](virtual-network-manage-peering.md#permissions).
6. Zkopírujte obsah skriptu v kroku 4 do textového editoru v počítači. Nahraďte `<SubscriptionA-Id>` ID pro předplatné B. Změňte 10.0.0.0/16 na 10.1.0.0/16. Změňte vše jako na B a všechny BS na A. Skript spustíte tak, že zkopírujete upravený skript, vložíte ho do PowerShellu a pak stisknete `Enter` .
7. Odhlaste se ze služby UserB z Azure a přihlaste se UserA.
8. Vytvořte partnerský vztah z myVnetA do myVnetB. Zkopírujte následující skript do textového editoru na svém počítači. Nahraďte `<SubscriptionB-Id>` ID předplatného B. Skript spustíte tak, že zkopírujete upravený skript, vložíte ho do PowerShellu a pak stisknete `Enter` .

   ```powershell
   # Peer myVnetA to myVnetB.
   $vNetA=Get-AzVirtualNetwork -Name myVnetA -ResourceGroupName myResourceGroupA
   Add-AzVirtualNetworkPeering `
     -Name 'myVnetAToMyVnetB' `
     -VirtualNetwork $vNetA `
     -RemoteVirtualNetworkId "/subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/virtualNetworks/myVnetB"
   ```

9. Zobrazte stav partnerského vztahu pro myVnetA.

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName myResourceGroupA `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Stav je **zahájeno**. Po nastavení partnerského vztahu pro myVnetA z myVnetB se změní na **připojeno** .

10. Odhlaste se ze služby UserA z Azure a přihlaste se UserB.
11. Vytvořte partnerský vztah z myVnetB do myVnetA. Zkopírujte obsah skriptu v kroku 8 do textového editoru v počítači. Nahraďte `<SubscriptionB-Id>` ID předplatného a a změňte vše jako na B a všechny BS na. Skript spustíte tak, že zkopírujete upravený skript, vložíte ho do PowerShellu a pak stisknete `Enter` .
12. Zobrazte stav partnerského vztahu pro myVnetB. Zkopírujte obsah skriptu v kroku 9 do textového editoru na svém počítači. Změňte hodnotu na B pro název skupiny prostředků a virtuální sítě. Pokud chcete skript spustit, vložte upravený skript do PowerShellu a pak stiskněte `Enter` . Stav je **připojen**. Stav partnerského vztahu **myVnetA** se změní na **připojeno** po vytvoření partnerského vztahu z **myVnetB** do **myVnetA**. UserA se můžete přihlásit zpátky do Azure a dokončit krok 9 znovu, abyste ověřili stav partnerských vztahů pro myVnetA.

    > [!NOTE]
    > Partnerský vztah není navázán, dokud stav partnerského vztahu není **připojen** k oběma virtuálním sítím.

    Všechny prostředky Azure, které vytvoříte v obou virtuálních sítích, teď můžou vzájemně komunikovat prostřednictvím jejich IP adres. Pokud pro virtuální sítě používáte výchozí překlad názvů Azure, prostředky ve virtuálních sítích nebudou schopné překládat názvy mezi virtuálními sítěmi. Pokud chcete přeložit názvy mezi virtuálními sítěmi v partnerském vztahu, musíte vytvořit vlastní server DNS. Přečtěte si, jak nastavit [překlad názvů pomocí vlastního serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

13. **Volitelné**: i když se v tomto kurzu nezabývá vytvářením virtuálních počítačů, můžete vytvořit virtuální počítač v každé virtuální síti a připojit se z jednoho virtuálního počítače k druhému a ověřit připojení.
14. **Volitelné**: Chcete-li odstranit prostředky, které vytvoříte v tomto kurzu, proveďte kroky v části [odstranění prostředků](#delete-powershell) v tomto článku.

## <a name="create-peering---resource-manager-template"></a><a name="template"></a>Vytvoření partnerského vztahu – šablona Správce prostředků

1. Pokud chcete vytvořit virtuální síť a přiřadit příslušná [oprávnění](virtual-network-manage-peering.md#permissions), proveďte kroky v částech [portál](#portal), [Azure CLI](#cli)nebo [PowerShell](#powershell) v tomto článku.
2. Uložte text, který následuje, do souboru na místním počítači. Nahraďte `<subscription ID>` ID předplatného Usera. Soubor můžete uložit jako vnetpeeringA.jsnapříklad.

   ```json
   {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
    "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "myVnetA/myVnetAToMyVnetB",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/<subscription ID>/resourceGroups/PeeringTest/providers/Microsoft.Network/virtualNetworks/myVnetB"
                }
            }
            }
        ]
   }
   ```

3. Přihlaste se k Azure jako UserA a nasaďte šablonu pomocí [portálu](../azure-resource-manager/templates/deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template), [PowerShellu](../azure-resource-manager/templates/deploy-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template-or-bicep-file)nebo rozhraní příkazového [řádku Azure CLI](../azure-resource-manager/templates/deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template-or-bicep-file). Zadejte název souboru, do kterého jste uložili ukázkový text JSON v kroku 2.
4. Zkopírujte vzorový kód JSON z kroku 2 do souboru ve vašem počítači a udělejte změny v řádcích, které začínají na:
   - **název**: změňte *myVnetA/myVnetAToMyVnetB* na *myVnetB/myVnetBToMyVnetA*.
   - **ID**: NAHRAĎte `<subscription ID>` ID předplatného UserB a změňte *myVnetB* na *myVnetA*.
5. Znovu proveďte krok 3, přihlaste se k Azure jako UserB.
6. **Volitelné**: i když se v tomto kurzu nezabývá vytvářením virtuálních počítačů, můžete vytvořit virtuální počítač v každé virtuální síti a připojit se z jednoho virtuálního počítače k druhému a ověřit připojení.
7. **Volitelné**: Pokud chcete odstranit prostředky, které vytvoříte v tomto kurzu, postupujte podle kroků v části [odstranění prostředků](#delete) tohoto článku, a to pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure.

## <a name="delete-resources"></a><a name="delete"></a>Odstranění prostředků
Po dokončení tohoto kurzu možná budete chtít odstranit prostředky, které jste v tomto kurzu vytvořili, takže se vám neúčtují poplatky za využití. Odstraněním skupiny prostředků se odstraní také všechny prostředky, které jsou ve skupině prostředků.

### <a name="azure-portal"></a><a name="delete-portal"></a>Azure Portal

1. Přihlaste se k Azure Portal jako UserA.
2. Do vyhledávacího pole portálu zadejte **myResourceGroupA**. Ve výsledcích hledání vyberte **myResourceGroupA**.
3. Vyberte **Odstranit**.
4. Odstranění potvrďte tak, že do pole **Zadejte název skupiny prostředků** zadáte **myResourceGroupA** a pak vyberete **Odstranit**.
5. Odhlaste se z portálu jako UserA a přihlaste se jako UserB.
6. Proveďte kroky 2-4 pro myResourceGroupB.

### <a name="azure-cli"></a><a name="delete-cli"></a>Azure CLI

1. Přihlaste se k Azure jako UserA a spusťte následující příkaz:

   ```azurecli-interactive
   az group delete --name myResourceGroupA --yes
   ```

2. Odhlaste se z Azure jako UserA a přihlaste se jako UserB.
3. Spusťte následující příkaz:

   ```azurecli-interactive
   az group delete --name myResourceGroupB --yes
   ```

### <a name="powershell"></a><a name="delete-powershell"></a>PowerShell

1. Přihlaste se k Azure jako UserA a spusťte následující příkaz:

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupA -force
   ```

2. Odhlaste se z Azure jako UserA a přihlaste se jako UserB.
3. Spusťte následující příkaz:

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupB -force
   ```

## <a name="next-steps"></a>Další kroky

- Před vytvořením partnerského vztahu virtuálních sítí pro produkční použití důkladně se seznamte s důležitými [omezeními a chováním partnerských vztahů virtuálních sítí](virtual-network-manage-peering.md#requirements-and-constraints) .
- Přečtěte si o [nastaveních partnerského vztahu virtuálních sítí](virtual-network-manage-peering.md#create-a-peering).
- Naučte se [vytvářet síťové topologie centra a paprsků](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) s využitím partnerského vztahu virtuálních sítí.