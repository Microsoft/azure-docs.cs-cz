---
title: Vytvoření partnerského vztahu – Resource Manager - různých předplatných Azure virtuální sítě | Microsoft Docs
description: Naučte se vytvořit virtuální síť partnerský vztah mezi virtuální sítě vytvořené pomocí Správce prostředků, které existují v různých předplatných Azure.
services: virtual-network
documentationcenter: ''
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: jdial;anavin
ms.openlocfilehash: eccfdf519d16595f2bda77675d62bff4bf14b4f8
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="create-a-virtual-network-peering---resource-manager-different-subscriptions"></a>Vytvoření virtuální sítě partnerský vztah – Resource Manager, různých předplatných 

V tomto kurzu zjistíte vytvořit virtuální síť partnerský vztah mezi virtuální sítě vytvořené pomocí Správce prostředků. Virtuální sítě existovat v různých předplatných. Partnerský vztah dva prostředky umožňuje virtuální sítě v různých virtuálních sítích ke komunikaci mezi sebou stejným šířky pásma a latence, jako by byl prostředky ve stejné virtuální síti. Další informace o [partnerský vztah virtuální sítě](virtual-network-peering-overview.md). 

Postup vytvoření virtuální sítě partnerského vztahu se liší v závislosti na tom, jestli virtuální sítě jsou ve stejné nebo jiné, odběry a které [modelu nasazení Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuální sítě se vytvářejí pomocí. Naučte se vytvořit virtuální síť partnerský vztah v jiných scénářích tak, že vyberete scénář v následující tabulce:

|Model nasazení Azure  | Předplatné Azure  |
|--------- |---------|
|[I Resource Manager](tutorial-connect-virtual-networks-portal.md) |stejné|
|[Jeden Resource Manager, jeden classic](create-peering-different-deployment-models.md) |stejné|
|[Jeden Resource Manager, jeden classic](create-peering-different-deployment-models-subscriptions.md) |Odlišné|

Virtuální síť partnerský vztah nelze vytvořit mezi dvěma virtuálními sítěmi nasazené prostřednictvím modelu nasazení classic. Pokud potřebujete připojení virtuální sítě, které byly obě vytvořené pomocí modelu nasazení classic, můžete použít Azure [brány VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) připojení virtuální sítě. 

V tomto kurzu partnerský vztah virtuálních sítí ve stejné oblasti. Můžete také peer virtuální sítě v různých [podporované oblasti](virtual-network-manage-peering.md#cross-region). 

Můžete použít [portál Azure](#portal), Azure [rozhraní příkazového řádku](#cli) (CLI) Azure [prostředí PowerShell](#powershell), nebo [šablony Azure Resource Manageru](#template)vytvoření virtuální sítě partnerského vztahu. Vyberte některé z předchozích odkazy nástroj přejít přímo na kroky pro vytvoření virtuální sítě partnerský vztah pomocí vaší nástroje.

## <a name="portal"></a>Vytvoření partnerského vztahu – portál Azure

Tento kurz používá různé účty pro každé předplatné. Pokud používáte účet, který má oprávnění pro oba odběry, můžete použít stejný účet pro všechny kroky, přeskočte postup protokolování mimo portál a přeskočit kroky pro přiřazení jiný uživatel oprávnění k virtuálním sítím.

1. Přihlaste se k [portál Azure](https://portal.azure.com) jako *uživatele*. Účet, ke kterému se přihlásíte, musí mít potřebná oprávnění k vytvoření virtuální sítě partnerského vztahu. Seznam oprávnění najdete v tématu [virtuální sítě partnerského vztahu oprávnění](virtual-network-manage-peering.md#permissions).
2. Vyberte **+ vytvořit prostředek**, vyberte **sítě**a potom vyberte **virtuální síť**.
3. Vyberte nebo zadejte následující ukázkové hodnoty pro následující nastavení a potom vyberte **vytvořit**:
    - **Name**: *myVnetA*
    - **Adresní prostor**: *10.0.0.0/16*
    - **Název podsítě**: *výchozí*
    - **Rozsah adres podsítě**: *10.0.0.0/24*
    - **Předplatné**: Vyberte předplatné A.
    - **Skupina prostředků**: vyberte **vytvořit nový** a zadejte *myResourceGroupA*
    - **Umístění**: *východní USA*
4. V **vyhledávání prostředků** pole v horní části portálu, typ *myVnetA*. Vyberte **myVnetA** při zobrazí ve výsledcích hledání. 
5. Vyberte **přístup k ovládacímu prvku (IAM)** ze seznamu svislé možností na levé straně.
6. V části **myVnetA – řízení přístupu (IAM)**, vyberte **+ přidat**.
7. Vyberte **Přispěvatel sítě** v **Role** pole.
8. V **vyberte** vyberte *b*, nebo zadejte e-mailovou adresu společnosti b ji najít. Seznam uživatelů, zobrazí se ze stejné klienta Azure Active Directory jako virtuální síť, kterou jste nastavení pro partnerský vztah. Pokud nevidíte b, je pravděpodobné, protože b je v jiné klienta služby Active Directory, než uživatele. Pokud se chcete připojit virtuální sítě v různých klientech služby Active Directory, můžete se připojit je [Azure VPN Gateway](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), místo abyste virtuální sítě partnerský vztah.
9. Vyberte **Uložit**.
10. V části **myVnetA – řízení přístupu (IAM)**, vyberte **vlastnosti** ze seznamu svislé možností na levé straně. Kopírování **ID prostředku**, která je použita v pozdější fázi. Podobně jako v následujícím příkladu je ID prostředku: /subscriptions/<Subscription Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/virtualNetworks/myVnetA.
11. Odhlaste se z portálu jako uživatele a přihlaste se jako b.
12. Proveďte kroky 2 – 3, zadat nebo vybrat následující hodnoty v kroku 3:

    - **Název**: *myVnetB*
    - **Adresní prostor**: *10.1.0.0/16*
    - **Název podsítě**: *výchozí*
    - **Rozsah adres podsítě**: *10.1.0.0/24*
    - **Předplatné**: Vyberte předplatné B.
    - **Skupina prostředků**: vyberte **vytvořit nový** a zadejte *myResourceGroupB*
    - **Umístění**: *východní USA*

13. V **vyhledávání prostředků** pole v horní části portálu, typ *myVnetB*. Vyberte **myVnetB** při zobrazí ve výsledcích hledání.
14. V části **myVnetB**, vyberte **vlastnosti** ze seznamu svislé možností na levé straně. Kopírování **ID prostředku**, která je použita v pozdější fázi. Podobně jako v následujícím příkladu je ID prostředku: /subscriptions/<Susbscription ID>/resourceGroups/myResoureGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB.
15. Vyberte **přístup k ovládacímu prvku (IAM)** pod **myVnetB**a potom proveďte kroky 5 až 10 pro myVnetB, zadávání **uživatele** v kroku 8.
16. Odhlaste se z portálu jako b a přihlaste se jako uživatele.
17. V **vyhledávání prostředků** pole v horní části portálu, typ *myVnetA*. Vyberte **myVnetA** při zobrazí ve výsledcích hledání.
18. Vyberte **myVnetA**.
19. V části **nastavení**, vyberte **partnerských vztahů**.
20. V části **myVnetA - partnerských vztahů**, vyberte **+ přidat**
21. V části **partnerský vztah přidat**, zadejte, nebo vyberte následující možnosti, poté vyberte **OK**:
     - **Name**: *myVnetAToMyVnetB*
     - **Virtuální síť modelu nasazení**: vyberte **Resource Manager**.
     - **Vím Moje ID prostředku**: Zaškrtněte toto políčko.
     - **ID prostředku**: Zadejte ID prostředku z kroku 14.
     - **Povolit přístup k virtuální síti:** Ujistěte se, že **povoleno** je vybrána.
    Žádné další nastavení použitá v tomto kurzu. Další informace o všech nastaveních partnerského vztahu, přečtěte si [spravovat virtuální sítě partnerských vztahů](virtual-network-manage-peering.md#create-a-peering).
22. Partnerský vztah, kterou jste vytvořili zobrazuje krátký čekání po výběru **OK** v předchozím kroku. **Iniciované** , je uvedena ve **stav partnerského vztahu** sloupec pro **myVnetAToMyVnetB** partnerského vztahu, můžete vytvořit. Jste peered myVnetA k myVnetB, ale teď musí peer myVnetB k myVnetA. Partnerský vztah, musí být vytvořený v obou směrech povolit prostředky ve virtuálních sítích ke komunikaci mezi sebou.
23. Odhlaste se z portálu jako uživatele a přihlaste se jako b.
24. Proveďte kroky 17 21 znovu pro myVnetB. V kroku č. 21, název partnerského vztahu *myVnetBToMyVnetA*, vyberte *myVnetA* pro **virtuální síť**a zadejte ID z kroku 10 v **ID prostředku**pole.
25. Několik sekund po výběru **OK** vytvoření partnerského vztahu pro myVnetB, **myVnetBToMyVnetA** partnerský vztah, kterou jste právě vytvořili je označené **připojeno** v **Stav partnerského vztahu** sloupce.
26. Odhlaste se z portálu jako b a přihlaste se jako uživatele.
27. Proveďte kroky 17-19 znovu. **Stav partnerského vztahu** pro **myVnetAToVNetB** partnerského vztahu je nyní také **připojeno**. Partnerského vztahu je úspěšně vytvořeno po uvidíte **připojeno** v **stav partnerského vztahu** sloupec pro obě virtuální sítě v partnerském vztahu. Veškeré prostředky Azure, kterou vytvoříte na buď virtuální sítě je nyní možné vzájemně komunikovat prostřednictvím jejich IP adresy. Pokud používáte překlad výchozí Azure pro virtuální sítě, nejsou prostředky ve virtuálních sítích překládat názvy virtuálních sítí. Pokud chcete překládat názvy virtuálních sítí v partnerský vztah, musíte vytvořit vlastní server DNS. Zjistěte, jak nastavit [překladu IP adresy serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
28. **Volitelné**: když vytváření virtuálních počítačů není zahrnutý v tomto kurzu, můžete vytvořit virtuální počítač v každé virtuální sítě a připojení z jednoho virtuálního počítače na druhý k ověření připojení.
29. **Volitelné**: Pokud chcete odstranit prostředky, které vytvoříte v tomto kurzu, proveďte kroky v [odstranit prostředky](#delete-portal) tohoto článku.

## <a name="cli"></a>Vytvoření partnerského vztahu - rozhraní příkazového řádku Azure

Tento kurz používá různé účty pro každé předplatné. Pokud používáte účet, který má oprávnění pro oba odběry, můžete použít stejný účet pro všechny kroky, přeskočte postup protokolování mimo Azure a odstranit řádky skriptu, které vytvořit přiřazení role uživatele. Nahraďte UserA@azure.com a UserB@azure.com ve všech z následujících skriptů s uživatelských jmen, kterou používáte pro uživatele a b. Obě virtuální sítě, které chcete rovnocenných počítačů musí být v rámci předplatných přidružený ke stejné klienta Azure Active Directory.  Pokud se chcete připojit virtuální sítě v různých klientech služby Active Directory, můžete se připojit je [Azure VPN Gateway](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), místo abyste virtuální sítě partnerský vztah.

Tyto skripty:

- Vyžaduje Azure CLI verze verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Funguje v prostředí Bash. Možnosti spuštění skriptů Azure CLI na klientovi s Windows najdete v tématu věnovaném [spuštění Azure CLI ve Windows](../virtual-machines/windows/cli-options.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

Místo instalace rozhraní příkazového řádku a jeho závislé součásti, můžete použít prostředí cloudové služby Azure. Služba Azure Cloud Shell je volně dostupné prostředí Bash, které můžete spustit přímo z portálu Azure Portal. Má předinstalované rozhraní Azure CLI, které je nakonfigurované pro použití s vaším účtem. Vyberte **vyzkoušet** tlačítko ve skriptu, který následuje, které vyvolá prostředí cloudu, který se může přihlásit k účtu Azure s. 

1. Otevřete relaci rozhraní příkazového řádku a přihlaste se k Azure jako uživatele pomocí `azure login` příkaz. Účet, ke kterému se přihlásíte, musí mít potřebná oprávnění k vytvoření virtuální sítě partnerského vztahu. Seznam oprávnění najdete v tématu [virtuální sítě partnerského vztahu oprávnění](virtual-network-manage-peering.md#permissions).
2. Zkopírujte následující skript do textového editoru ve vašem počítači, nahraďte `<SubscriptionA-Id>` s ID SubscriptionA, zkopírujte upravené skriptu, vložte jej v relaci příkazového řádku a stiskněte klávesu `Enter`. Pokud si nejste jisti Id předplatného, zadejte příkaz 'az účet zobrazit'. Hodnota **id** ve výstupu je ID vašeho předplatného.

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
    
3. Odhlaste se z Azure jako uživatele pomocí `az logout` příkazů a přihlaste se k Azure jako b. Účet, ke kterému se přihlásíte, musí mít potřebná oprávnění k vytvoření virtuální sítě partnerského vztahu. Seznam oprávnění najdete v tématu [virtuální sítě partnerského vztahu oprávnění](virtual-network-manage-peering.md#permissions).
4. Vytvořte myVnetB. Zkopírujte obsah skriptů v kroku 2 do textového editoru ve vašem počítači. Nahraďte `<SubscriptionA-Id>` s ID SubscriptionB. Změna 10.0.0.0/16 10.1.0.0/16, změnit všechny, B a všechny Bs A. kopií upravené skript, vložte jej do relace rozhraní příkazového řádku a stiskněte klávesu `Enter`. 
5. Odhlaste se z Azure jako b a přihlaste se k Azure jako uživatele.
6. Vytvoření partnerského vztahu z myVnetA k myVnetB virtuální sítě. Zkopírujte následující skript obsah do textového editoru ve vašem počítači. Nahraďte `<SubscriptionB-Id>` s ID SubscriptionB. Spustit skript, zkopírujte upravené skript, vložte jej do relace rozhraní příkazového řádku a stiskněte klávesu Enter.
 
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
          --remote-vnet-id /subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/VirtualNetworks/myVnetB \
          --allow-vnet-access
    ```

7. Zobrazte stav partnerského vztahu myVnetA.

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroupA \
      --vnet-name myVnetA \
      --output table
    ```

    Stav je **získaných**. Změní na **připojeno** po vytvoření partnerského vztahu k myVnetA z myVnetB.

8. Odhlášení uživatele z Azure a přihlaste se k Azure jako b.
9. Vytvoření partnerského vztahu z myVnetB k myVnetA. Zkopírujte obsah skriptů v kroku 6 do textového editoru ve vašem počítači. Nahraďte `<SubscriptionB-Id>` s ID pro SubscriptionA a změňte všechny, B a všechny Bs do A. Po provedení změny, zkopírujte upravené skript, vložte jej do relace rozhraní příkazového řádku a stiskněte klávesu `Enter`.
10. Zobrazte stav partnerského vztahu myVnetB. Zkopírujte obsah skriptů v kroku 7 do textového editoru ve vašem počítači. Změna A na B pro skupinu prostředků a virtuální sítě, zkopírujte skript, vložte upravené skriptu v relaci příkazového řádku a stiskněte klávesu `Enter`. Stav partnerského vztahu je **připojeno**. Změny partnerského vztahu stav myVnetA **připojeno** po vytvoření partnerského vztahu z myVnetB k myVnetA. Může přihlásit uživatele zpět do Azure a dokončení kroku 7 znovu a ověřte stav partnerského vztahu myVnetA. 

    > [!NOTE]
    > Partnerský vztah nebyl určen, dokud je stav partnerského vztahu **připojeno** pro obě virtuální sítě.

11. **Volitelné**: když vytváření virtuálních počítačů není zahrnutý v tomto kurzu, můžete vytvořit virtuální počítač v každé virtuální sítě a připojení z jednoho virtuálního počítače na druhý k ověření připojení.
12. **Volitelné**: Pokud chcete odstranit prostředky, které vytvoříte v tomto kurzu, proveďte kroky v [odstranit prostředky](#delete-cli) v tomto článku.

Veškeré prostředky Azure, kterou vytvoříte na buď virtuální sítě je nyní možné vzájemně komunikovat prostřednictvím jejich IP adresy. Pokud používáte překlad výchozí Azure pro virtuální sítě, nejsou prostředky ve virtuálních sítích překládat názvy virtuálních sítí. Pokud chcete překládat názvy virtuálních sítí v partnerský vztah, musíte vytvořit vlastní server DNS. Zjistěte, jak nastavit [překladu IP adresy serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
 
## <a name="powershell"></a>Vytvoření partnerského vztahu – prostředí PowerShell

Tento kurz používá různé účty pro každé předplatné. Pokud používáte účet, který má oprávnění pro oba odběry, můžete použít stejný účet pro všechny kroky, přeskočte postup protokolování mimo Azure a odstranit řádky skriptu, které vytvořit přiřazení role uživatele. Nahraďte UserA@azure.com a UserB@azure.com ve všech z následujících skriptů s uživatelských jmen, kterou používáte pro uživatele a b. Obě virtuální sítě, které chcete rovnocenných počítačů musí být v rámci předplatných přidružený ke stejné klienta Azure Active Directory.  Pokud se chcete připojit virtuální sítě v různých klientech služby Active Directory, můžete se připojit je [Azure VPN Gateway](../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md), místo abyste virtuální sítě partnerský vztah.

1. Nainstalujte nejnovější verzi modulu [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) pro PowerShell. Pokud s Azure PowerShellem začínáte, podívejte se na [Přehled Azure PowerShellu](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Spusťte relaci prostředí PowerShell.
3. V prostředí PowerShell, přihlaste se k Azure jako uživatele tak, že zadáte `login-azurermaccount` příkaz. Účet, ke kterému se přihlásíte, musí mít potřebná oprávnění k vytvoření virtuální sítě partnerského vztahu. Seznam oprávnění najdete v tématu [virtuální sítě partnerského vztahu oprávnění](virtual-network-manage-peering.md#permissions).
4. Vytvořte skupinu prostředků a virtuální síť A. kopie následující skript do textového editoru ve vašem počítači. Nahraďte `<SubscriptionA-Id>` s ID SubscriptionA. Pokud si nejste jisti Id předplatného, zadejte `Get-AzureRmSubscription` příkaz k jeho zobrazení. Hodnota **Id** ve vrácené výstupu je ID vašeho předplatného. Spustit skript, zkopírujte upravené skript, vložte jej do prostředí PowerShell a potom stiskněte klávesu `Enter`.

    ```powershell
    # Create a resource group.
    New-AzureRmResourceGroup `
      -Name MyResourceGroupA `
      -Location eastus

    # Create virtual network A.
    $vNetA = New-AzureRmVirtualNetwork `
      -ResourceGroupName MyResourceGroupA `
      -Name 'myVnetA' `
      -AddressPrefix '10.0.0.0/16' `
      -Location eastus

    # Assign UserB permissions to myVnetA.
    New-AzureRmRoleAssignment `
      -SignInName UserB@azure.com `
      -RoleDefinitionName "Network Contributor" `
      -Scope /subscriptions/<SubscriptionA-Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/VirtualNetworks/myVnetA
    ```

5. Odhlášení uživatele z Azure a přihlaste se b. Účet, ke kterému se přihlásíte, musí mít potřebná oprávnění k vytvoření virtuální sítě partnerského vztahu. Seznam oprávnění najdete v tématu [virtuální sítě partnerského vztahu oprávnění](virtual-network-manage-peering.md#permissions).
6. Zkopírujte obsah skriptů v kroku 4 do textového editoru ve vašem počítači. Nahraďte `<SubscriptionA-Id>` s ID předplatného B. změnu 10.0.0.0/16 k 10.1.0.0/16. Změňte všechny, B a všechny Bs A. Se spustit skript, zkopírujte upravené skript, vložte do prostředí PowerShell a potom stiskněte klávesu `Enter`.
7. Odhlaste se b z Azure a přihlášení uživatele.
8. Vytvoření partnerského vztahu z myVnetA k myVnetB. Zkopírujte následující skript do textového editoru ve vašem počítači. Nahraďte `<SubscriptionB-Id>` s ID předplatného B. Se spustit skript, zkopírujte upravené skript, vložte v prostředí PowerShell a potom stiskněte klávesu `Enter`.
 
    ```powershell
    # Peer myVnetA to myVnetB.
    $vNetA=Get-AzureRmVirtualNetwork -Name myVnetA -ResourceGroupName myResourceGroupA
    Add-AzureRmVirtualNetworkPeering `
      -Name 'myVnetAToMyVnetB' `
      -VirtualNetwork $vNetA `
      -RemoteVirtualNetworkId "/subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/virtualNetworks/myVnetB"
    ```

9. Zobrazte stav partnerského vztahu myVnetA.

    ```powershell
    Get-AzureRmVirtualNetworkPeering `
      -ResourceGroupName myResourceGroupA `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Stav je **získaných**. Změní na **připojeno** po nastavení partnerského vztahu k myVnetA z myVnetB.

10. Odhlášení uživatele z Azure a přihlaste se b.
11. Vytvoření partnerského vztahu z myVnetB k myVnetA. Zkopírujte obsah skriptů v kroku 8 do textového editoru ve vašem počítači. Nahraďte `<SubscriptionB-Id>` s ID předplatného A a změňte všechny, B a všechny Bs do A. Spustit skript, zkopírujte upravené skript, vložte jej do prostředí PowerShell a potom stiskněte klávesu `Enter`.
12. Zobrazte stav partnerského vztahu myVnetB. Zkopírujte obsah skriptů v kroku 9 do textového editoru ve vašem počítači. Změna A na B pro skupinu prostředků a virtuální sítě. Pokud chcete spustit skript, vložte upravené skript do prostředí PowerShell a stiskněte klávesu `Enter`. Stav je **připojeno**. Stav partnerského vztahu **myVnetA** změny **připojeno** po vytvoření partnerského vztahu z **myVnetB** k **myVnetA**. Může přihlásit uživatele zpět v Azure a dokončení kroku 9 znovu a ověřte stav partnerského vztahu myVnetA. 

    > [!NOTE]
    > Partnerský vztah nebyl určen, dokud je stav partnerského vztahu **připojeno** pro obě virtuální sítě.

    Veškeré prostředky Azure, kterou vytvoříte na buď virtuální sítě je nyní možné vzájemně komunikovat prostřednictvím jejich IP adresy. Pokud používáte překlad výchozí Azure pro virtuální sítě, nejsou prostředky ve virtuálních sítích překládat názvy virtuálních sítí. Pokud chcete překládat názvy virtuálních sítí v partnerský vztah, musíte vytvořit vlastní server DNS. Zjistěte, jak nastavit [překladu IP adresy serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

13. **Volitelné**: když vytváření virtuálních počítačů není zahrnutý v tomto kurzu, můžete vytvořit virtuální počítač v každé virtuální sítě a připojení z jednoho virtuálního počítače na druhý k ověření připojení.
14. **Volitelné**: Pokud chcete odstranit prostředky, které vytvoříte v tomto kurzu, proveďte kroky v [odstranit prostředky](#delete-powershell) v tomto článku.

## <a name="template"></a>Vytvoření partnerského vztahu - šablony Resource Manageru

1. Vytvořte virtuální síť a přiřaďte odpovídající [oprávnění](virtual-network-manage-peering.md#permissions), proveďte kroky v [portál](#portal), [rozhraní příkazového řádku Azure](#cli), nebo [prostředí PowerShell](#powershell)části tohoto článku.
2. Uložte text, který následuje do souboru v místním počítači. Nahraďte `<subscription ID>` s ID uživatele pro předplatné. Soubor může uložit jako vnetpeeringA.json, např.

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

3. Přihlaste se k Azure jako uživatele a nasadit pomocí šablony [portál](../azure-resource-manager/resource-group-template-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template), [prostředí PowerShell](../azure-resource-manager/resource-group-template-deploy.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-a-template-from-your-local-machine), nebo [rozhraní příkazového řádku Azure](../azure-resource-manager/resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template). Zadejte název souboru, který jste uložili text json příklad v kroku 2.
4. Zkopírujte json příklad z kroku 2 do souboru na váš počítač a proveďte změny na řádky, které začínají:
    - **název**: Změna *myVnetA/myVnetAToMyVnetB* k *myVnetB/myVnetBToMyVnetA*.
    - **ID**: Nahraďte `<subscription ID>` s ID předplatného a změňte na b *myVnetB* k *myVnetA*.
5. Dokončení kroku 3 znovu, přihlášení do Azure jako b.
6. **Volitelné**: když vytváření virtuálních počítačů není zahrnutý v tomto kurzu, můžete vytvořit virtuální počítač v každé virtuální sítě a připojení z jednoho virtuálního počítače na druhý k ověření připojení.
7. **Volitelné**: Pokud chcete odstranit prostředky, které vytvoříte v tomto kurzu, proveďte kroky v [odstranit prostředky](#delete) tohoto článku, pomocí portálu Azure, PowerShell nebo rozhraní příkazového řádku Azure.

## <a name="delete"></a>Odstraňte prostředky
Po dokončení tohoto kurzu, můžete chtít odstranit z prostředků, které jste vytvořili v tomto kurzu, aby vám zbytečně nenabíhaly poplatky za používání. Odstranění skupiny prostředků se také odstraní všechny prostředky, které jsou ve skupině prostředků.

### <a name="delete-portal"></a>Portál Azure

1. Přihlaste se k portálu Azure jako uživatele.
2. V dialogovém okně hledání portálu zadejte **myResourceGroupA**. Ve výsledcích hledání vyberte **myResourceGroupA**.
3. Vyberte **Odstranit**.
4. Potvrďte odstranění, v **název skupiny prostředků typu** zadejte **myResourceGroupA**a potom vyberte **odstranit**.
5. Odhlaste se z portálu jako uživatele a přihlaste se jako b.
6. Dokončete kroky 2 až 4 pro myResourceGroupB.

### <a name="delete-cli"></a>Azure CLI

1. Přihlaste se k Azure jako uživatele a spusťte následující příkaz:

    ```azurecli-interactive
    az group delete --name myResourceGroupA --yes
    ```
2. Odhlaste se z Azure jako uživatele a přihlaste se jako b.
3. Spusťte následující příkaz:

    ```azurecli-interactive
    az group delete --name myResourceGroupB --yes
    ```

### <a name="delete-powershell"></a>PowerShell

1. Přihlaste se k Azure jako uživatele a spusťte následující příkaz:

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroupA -force
    ```

2. Odhlaste se z Azure jako uživatele a přihlaste se jako b.
3. Spusťte následující příkaz:

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroupB -force
    ```

## <a name="next-steps"></a>Další postup

- Důkladně Seznamte se s důležité [partnerského vztahu omezení virtuální sítě a chování](virtual-network-manage-peering.md#requirements-and-constraints) před vytvořením virtuální sítě partnerský vztah pro produkční použití.
- Další informace o všech [partnerského vztahu nastavení virtuální sítě](virtual-network-manage-peering.md#create-a-peering).
- Zjistěte, jak [vytvořit rozbočovače a příčkou topologie sítě](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) s partnerský vztah virtuální sítě.
