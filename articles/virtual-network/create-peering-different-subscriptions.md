---
title: Vytvoření partnerského vztahu – Resource Manager – různých předplatných služby Azure virtual network
titlesuffix: Azure Virtual Network
description: Zjistěte, jak vytvořit virtuální síť vytvoření partnerského vztahu mezi virtuálními sítěmi vytvořenými prostřednictvím Resource Manageru, které existují v různých předplatných Azure.
services: virtual-network
documentationcenter: ''
author: jimdial
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: jdial;anavin
ms.openlocfilehash: f06f0b5392ebb60cd852d3c2eb201478b31ae167
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54014960"
---
# <a name="create-a-virtual-network-peering---resource-manager-different-subscriptions"></a>Vytvoření partnerského vztahu virtuálních sítí - Resource Manageru, různá předplatná

V tomto kurzu zjistíte, jak vytvořit virtuální síť vytvoření partnerského vztahu mezi virtuálními sítěmi vytvořenými prostřednictvím Resource Manageru. Virtuální sítě existovat v různých předplatných. Partnerský vztah dvou virtuálních sítí umožňuje prostředků v různých virtuálních sítích komunikovat mezi sebou stejnou šířku pásma a čekací doba jakoby byly ve stejné virtuální síti. Další informace o [partnerský vztah virtuálních sítí](virtual-network-peering-overview.md).

Postup vytvoření partnerského vztahu virtuálních sítí se liší v závislosti na tom, jestli jsou virtuální sítě ve stejné nebo jiné, předplatných a které [model nasazení Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuální sítě se vytvářejí prostřednictvím. Zjistěte, jak vytvořit virtuální síť vytvoření partnerského vztahu v jiných scénářích tak, že vyberete scénář v následující tabulce:

|Model nasazení Azure  | Předplatné Azure  |
|--------- |---------|
|[Obě Resource Manageru](tutorial-connect-virtual-networks-portal.md) |Stejné|
|[Jedna Resource Manager, druhá classic](create-peering-different-deployment-models.md) |Stejné|
|[Jedna Resource Manager, druhá classic](create-peering-different-deployment-models-subscriptions.md) |Odlišné|

Partnerský vztah virtuální sítě nejde vytvořit mezi dvěma virtuálními sítěmi, které jsou nasazené prostřednictvím modelu nasazení classic. Pokud potřebujete k propojení virtuálních sítí, které byly obě vytvořené prostřednictvím modelu nasazení classic, můžete použít Azure [VPN Gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) k propojení virtuálních sítí.

V tomto kurzu partnerský vztah virtuálních sítí ve stejné oblasti. Můžete také vytvořit partnerský vztah virtuálních sítí v různých [podporované oblasti](virtual-network-manage-peering.md#cross-region). Je doporučeno, aby měli seznámit s [partnerského vztahu požadavky a omezení](virtual-network-manage-peering.md#requirements-and-constraints) před partnerský vztah virtuálních sítí.

Můžete použít [webu Azure portal](#portal), Azure [rozhraní příkazového řádku](#cli) (CLI) Azure [PowerShell](#powershell), nebo [šablony Azure Resource Manageru](#template)k vytvoření partnerského vztahu virtuálních sítí. Vyberte některou z předchozích nástroj odkazy můžete přejít přímo na kroky pro vytvoření partnerského vztahu virtuálních sítí pomocí nástrojů podle výběru.

## <a name="portal"></a>Vytvoření partnerského vztahu – Azure portal

Pokud předplatné, které jsou přidružené k různým tenantům Azure Active Directory, které chcete vytvořit partnerský vztah virtuálních sítí, postupujte podle kroků v části tohoto článku rozhraní příkazového řádku a Powershellu. Portál nemá podporu na vytvoření partnerského vztahu virtuálních sítí patřících do odběry z jiných klientů Active Directory.

Následující kroky používají různé účty pro každé předplatné. Pokud používáte účet, který má oprávnění k oběma předplatným, můžete použít stejný účet pro všechny kroky, přeskočte kroky pro přihlášení z portálu a přeskočte kroky pro přiřazení oprávnění pro jiné uživatele k virtuálním sítím.

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) jako *UserA*. Účet, který jste přihlášení, musí mít potřebná oprávnění k vytvoření partnerského vztahu virtuálních sítí. Seznam oprávnění najdete v tématu [virtuálních sítí oprávnění pro partnerské vztahy](virtual-network-manage-peering.md#permissions).
2. Vyberte **+ vytvořit prostředek**vyberte **sítě**a pak vyberte **virtuální síť**.
3. Vyberte nebo zadejte následující ukázkové hodnoty pro následující nastavení a pak vyberte **vytvořit**:
    - **Název**: *myVnetA*
    - **Adresní prostor**: *10.0.0.0/16*
    - **Název podsítě**: *výchozí*
    - **Rozsah adres podsítě**: *10.0.0.0/24*
    - **Předplatné**: Vyberte předplatné A.
    - **Skupina prostředků**: Vyberte **vytvořit nový** a zadejte *myResourceGroupA*
    - **Umístění**: *USA – východ*
4. V **vyhledat prostředky** pole v horní části portálu zadejte *myVnetA*. Vyberte **myVnetA** když se zobrazí ve výsledcích hledání. 
5. Vyberte **řízení přístupu (IAM)** z svislý seznam možností na levé straně.
6. V části **myVnetA – řízení přístupu (IAM)** vyberte **+ přidat přiřazení role**.
7. Vyberte **Přispěvatel sítě** v **Role** pole.
8. V **vyberte** vyberte *UserB*, nebo zadejte e-mailovou adresu vaší UserB ji najít.
9. Vyberte **Uložit**.
10. V části **myVnetA – řízení přístupu (IAM)** vyberte **vlastnosti** z svislý seznam možností na levé straně. Kopírovat **ID prostředku**, který se používá v pozdější fázi. Podobně jako v následujícím příkladu je ID prostředku: /subscriptions/<Subscription Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/virtualNetworks/myVnetA.
11. Odhlaste se z portálu jako UserA a přihlaste se jako UserB.
12. Proveďte kroky 2 až 3, zadáním nebo výběrem následující hodnoty v kroku 3:

    - **Název**: *myVnetB*
    - **Adresní prostor**: *10.1.0.0/16*
    - **Název podsítě**: *výchozí*
    - **Rozsah adres podsítě**: *10.1.0.0/24*
    - **Předplatné**: Vyberte předplatné služby serveru B.
    - **Skupina prostředků**: Vyberte **vytvořit nový** a zadejte *myResourceGroupB*
    - **Umístění**: *USA – východ*

13. V **vyhledat prostředky** pole v horní části portálu zadejte *myVnetB*. Vyberte **myVnetB** když se zobrazí ve výsledcích hledání.
14. V části **myVnetB**vyberte **vlastnosti** z svislý seznam možností na levé straně. Kopírovat **ID prostředku**, který se používá v pozdější fázi. Podobně jako v následujícím příkladu je ID prostředku: /subscriptions/<Susbscription ID>/resourceGroups/myResoureGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB.
15. Vyberte **řízení přístupu (IAM)** pod **myVnetB**a potom proveďte kroky 5 až 10 pro myVnetB zadávání **UserA** v kroku 8.
16. Odhlaste se z portálu jako UserB a přihlaste se jako UserA.
17. V **vyhledat prostředky** pole v horní části portálu zadejte *myVnetA*. Vyberte **myVnetA** když se zobrazí ve výsledcích hledání.
18. Vyberte **myVnetA**.
19. V části **nastavení**vyberte **partnerské vztahy**.
20. V části **myVnetA - partnerské vztahy**vyberte **+ přidat**
21. V části **přidat partnerský vztah**, zadejte, nebo vyberte následující možnosti, pak vyberte **OK**:
     - **Název**: *myVnetAToMyVnetB*
     - **Model nasazení virtuální sítě**:  Zvolte **Resource Manager** (Správce prostředků).
     - **Znám svoje ID prostředku**: Zaškrtněte toto políčko.
     - **ID prostředku**: Zadejte ID prostředku z kroku 14.
     - **Povolit přístup k virtuální síti:** Ujistěte se, že **povoleno** zaškrtnuto.
    Žádná ostatní nastavení se používají v tomto kurzu. Další informace o všech nastavení partnerského vztahu, přečtěte si [spravovat partnerské vztahy virtuálních sítí](virtual-network-manage-peering.md#create-a-peering).
22. Partnerský vztah, kterou jste vytvořili se zobrazí po výběru krátkém čekání **OK** v předchozím kroku. **Zahájené** je uveden v **stav partnerského vztahu** sloupce **myVnetAToMyVnetB** partnerský vztah, můžete vytvořit. Jste vytvoření partnerského vztahu myVnetA k myVnetB, ale teď musí vytvořit partnerský vztah myVnetB k myVnetA. Partnerský vztah, musí být vytvořeny v obou směrech, aby prostředky ve virtuálních sítích komunikovat mezi sebou.
23. Odhlaste se z portálu jako UserA a přihlaste se jako UserB.
24. Proveďte kroky 17 21 za myVnetB. V kroku 21 název partnerského vztahu *myVnetBToMyVnetA*vyberte *myVnetA* pro **virtuální síť**a zadejte ID z kroku 10 **ID prostředku**pole.
25. Několik sekund, po výběru **OK** vytvoření partnerského vztahu pro myVnetB, **myVnetBToMyVnetA** partnerský vztah, kterou jste právě vytvořili je uvedený s **připojeno** v **Stav partnerského vztahu** sloupce.
26. Odhlaste se z portálu jako UserB a přihlaste se jako UserA.
27. Zopakujte kroky 17 – 19. **Stav partnerského vztahu** pro **myVnetAToVNetB** partnerského vztahu je teď také **připojeno**. Partnerský vztah se úspěšně navázán, až uvidíte **připojeno** v **stav partnerského vztahu** sloupec pro obě virtuální sítě v partnerském vztahu. Veškeré prostředky Azure, které vytvoříte v obou virtuálních sítích jsou nyní schopen komunikovat mezi sebou pomocí jejich IP adres. Pokud používáte výchozí rozlišování názvů Azure pro virtuální sítě, prostředky ve virtuálních sítích nejsou překládat názvy mezi virtuálními sítěmi. Pokud chcete překlad názvů přes partnerský vztah virtuální sítě, musíte vytvořit vlastní server DNS. Zjistěte, jak nastavit [překladu IP adresy serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
28. **Volitelné**: Když se v tomto kurzu nevztahuje vytváření virtuálních počítačů, můžete vytvoření virtuálního počítače v obou virtuálních sítích a připojit z jednoho virtuálního počítače do jiného, a ověření připojení.
29. **Volitelné**: Odstraňte prostředky, které vytvoříte v tomto kurzu, dokončete kroky [odstraňte prostředky](#delete-portal) části tohoto článku.

## <a name="cli"></a>Vytvoření partnerského vztahu – rozhraní příkazového řádku Azure

Tento kurz používá různé účty pro každé předplatné. Pokud používáte účet, který má oprávnění k oběma předplatným, můžete použít stejný účet pro všechny kroky, přeskočte kroky pro přihlášení z Azure a odebrat řádky skriptu, které vytvářejí přiřazení rolí uživatele. Nahraďte UserA@azure.com a UserB@azure.com ve všech z následujících skriptů se uživatelská jména, že používáte UserA a UserB. Pokud jsou virtuální sítě v různých předplatných, a že předplatná jsou přidružená k různým tenantům Azure Active Directory, než budete pokračovat proveďte následující kroky:
 - Přidat uživatele z každého tenanta Active Directory jako [uživatele typu Host](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) v opačné tenanta Azure Active Directory.
 - Každý uživatel má k přijetí pozvánky uživatelů typu Host z opačné tenanta Azure Active Directory.

Následující skripty:

- Vyžaduje použití Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete upgrade, přečtěte si téma [instalace Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Funguje v prostředí Bash. Možnosti spuštění skriptů Azure CLI na klientovi s Windows najdete v tématu věnovaném [instalaci Azure CLI ve Windows](/cli/azure/install-azure-cli-windows).

Místo instalace rozhraní příkazového řádku a jeho závislosti, můžete použít Azure Cloud Shell. Služba Azure Cloud Shell je volně dostupné prostředí Bash, které můžete spustit přímo z portálu Azure Portal. Má předinstalované rozhraní Azure CLI, které je nakonfigurované pro použití s vaším účtem. Vyberte **vyzkoušet** tlačítka ve skriptu, který následuje, které vyvolá cloudové prostředí, které se můžete přihlásit ke svému účtu Azure s. 

1. Otevřete relaci příkazového řádku a přihlášení do Azure jako pomocí UserA `azure login` příkazu. Účet, který jste přihlášení, musí mít potřebná oprávnění k vytvoření partnerského vztahu virtuálních sítí. Seznam oprávnění najdete v tématu [virtuálních sítí oprávnění pro partnerské vztahy](virtual-network-manage-peering.md#permissions).
2. Zkopírujte následující skript do textového editoru ve vašem počítači, nahraďte `<SubscriptionA-Id>` s ID SubscriptionA, zkopírujte upravený skript, vložte ho do relace příkazového řádku a stisknutím klávesy `Enter`. Pokud si nejste jisti Id předplatného, zadejte příkaz "az account show". Hodnota pro **id** ve výstupu je vaše ID předplatného.

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
    
3. Odhlaste se jako uživatel a s použitím Azure `az logout` příkaz a připojte se k Azure jako UserB. Účet, který jste přihlášení, musí mít potřebná oprávnění k vytvoření partnerského vztahu virtuálních sítí. Seznam oprávnění najdete v tématu [virtuálních sítí oprávnění pro partnerské vztahy](virtual-network-manage-peering.md#permissions).
4. Vytvoření myVnetB. Do textového editoru ve vašem počítači zkopírujte obsah skriptu v kroku 2. Nahraďte `<SubscriptionA-Id>` s ID předplatném SubscriptionB. Změna 10.0.0.0/16 10.1.0.0/16 změnit všechno tak, aby B a všechny Bs A. kopii upravený skript vložte ji do relace příkazového řádku a stisknutím klávesy `Enter`. 
5. Odhlaste se z Azure jako UserB a přihlaste se k Azure jako UserA.
6. Vytvoření virtuální sítě partnerských vztahů ze myVnetA na myVnetB. Zkopírujte následující obsah skriptu do textového editoru ve vašem počítači. Nahraďte `<SubscriptionB-Id>` s ID předplatném SubscriptionB. Spusťte skript, zkopírujte upravený skript, vložte ho do relace prostředí rozhraní příkazového řádku a stiskněte klávesu Enter.

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

    Zobrazí se stav **iniciováno**. Změní na **připojeno** po vytvoření partnerského vztahu pro myVnetA z myVnetB.

8. Odhlaste se uživatel a z Azure a přihlaste se k Azure jako UserB.
9. Vytvoření partnerského připojení z myVnetB do myVnetA. Zkopírujte obsah skriptu v kroku 6 do textového editoru ve vašem počítači. Nahraďte `<SubscriptionB-Id>` s ID SubscriptionA a změnit všechny jde o B a a. všechny Bs Po provedení změny, zkopírujte upravený skript, vložte ho do relace příkazového řádku a stisknutím klávesy `Enter`.
10. Zobrazte stav partnerského vztahu myVnetB. Do textového editoru ve vašem počítači zkopírujte obsah skriptu v kroku 7. Změna A na B pro skupinu prostředků a názvy virtuálních sítí, zkopírujte skript, vložte upravený skript v relaci příkazového řádku a stiskněte klávesu `Enter`. Stav partnerského vztahu je **připojeno**. Stav partnerského vztahu myVnetA změní na **připojeno** po vytvoření partnerského připojení z myVnetB do myVnetA. Uživatel a se můžete přihlásit zpátky do Azure a dokončení kroku 7 znovu a ověřte stav partnerského vztahu myVnetA. 

    > [!NOTE]
    > Partnerský vztah není navázán, dokud stav partnerského vztahu je **připojeno** pro obě virtuální sítě.

11. **Volitelné**: Když se v tomto kurzu nevztahuje vytváření virtuálních počítačů, můžete vytvoření virtuálního počítače v obou virtuálních sítích a připojit z jednoho virtuálního počítače do jiného, a ověření připojení.
12. **Volitelné**: Chcete-li odstranit prostředky, které vytvoříte v tomto kurzu, proveďte kroky v [odstraňte prostředky](#delete-cli) v tomto článku.

Veškeré prostředky Azure, které vytvoříte v obou virtuálních sítích jsou nyní schopen komunikovat mezi sebou pomocí jejich IP adres. Pokud používáte výchozí rozlišování názvů Azure pro virtuální sítě, prostředky ve virtuálních sítích nejsou překládat názvy mezi virtuálními sítěmi. Pokud chcete překlad názvů přes partnerský vztah virtuální sítě, musíte vytvořit vlastní server DNS. Zjistěte, jak nastavit [překladu IP adresy serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
 
## <a name="powershell"></a>Vytvoření partnerského vztahu – PowerShell

Tento kurz používá různé účty pro každé předplatné. Pokud používáte účet, který má oprávnění k oběma předplatným, můžete použít stejný účet pro všechny kroky, přeskočte kroky pro přihlášení z Azure a odebrat řádky skriptu, které vytvářejí přiřazení rolí uživatele. Nahraďte UserA@azure.com a UserB@azure.com ve všech z následujících skriptů se uživatelská jména, že používáte UserA a UserB. Pokud jsou virtuální sítě v různých předplatných, a že předplatná jsou přidružená k různým tenantům Azure Active Directory, než budete pokračovat proveďte následující kroky:
 - Přidat uživatele z každého tenanta Active Directory jako [uživatele typu Host](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) v opačné tenanta Azure Active Directory.
 - Každý uživatel má k přijetí pozvánky uživatelů typu Host z opačné tenanta Active Directory.

1. Potvrďte, že máte verzi 6.5.0 nebo vyšší. Můžete to provést spuštěním `Get-Module -Name AzureRm` doporučujeme nainstalovat nejnovější verzi prostředí PowerShell [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) modulu. Pokud s Azure PowerShellem začínáte, podívejte se na [Přehled Azure PowerShellu](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json). 
2. Spusťte relaci Powershellu.
3. V prostředí PowerShell, přihlaste se k Azure jako UserA tak, že zadáte `Connect-AzureRmAccount` příkazu. Účet, který jste přihlášení, musí mít potřebná oprávnění k vytvoření partnerského vztahu virtuálních sítí. Seznam oprávnění najdete v tématu [virtuálních sítí oprávnění pro partnerské vztahy](virtual-network-manage-peering.md#permissions).
4. Vytvořte skupinu prostředků a virtuální sítě a zkopírujte následující skript do textového editoru ve vašem počítači. Nahraďte `<SubscriptionA-Id>` s ID SubscriptionA. Pokud si nejste jisti Id předplatného, zadejte `Get-AzureRmSubscription` příkaz k jeho zobrazení. Hodnota pro **Id** ve vrácené výstupu je ID vašeho předplatného. Spusťte skript, zkopírujte upravený skript, vložte ji do prostředí PowerShell a stiskněte klávesu `Enter`.

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

5. Odhlaste UserA z Azure a přihlaste se UserB. Účet, který jste přihlášení, musí mít potřebná oprávnění k vytvoření partnerského vztahu virtuálních sítí. Seznam oprávnění najdete v tématu [virtuálních sítí oprávnění pro partnerské vztahy](virtual-network-manage-peering.md#permissions).
6. Zkopírujte obsah skriptu v kroku 4 do textového editoru ve vašem počítači. Nahraďte `<SubscriptionA-Id>` s ID předplatného B. změnu 10.0.0.0/16 k 10.1.0.0/16. Změnit všechny jde o B a všechny Bs a. Spusťte skript, zkopírujte upravený skript, vložte do prostředí PowerShell a stiskněte klávesu `Enter`.
7. Odhlaste UserB z Azure a přihlaste se UserA.
8. Vytvoření partnerského připojení z myVnetA do myVnetB. Zkopírujte následující skript do textového editoru ve vašem počítači. Nahraďte `<SubscriptionB-Id>` s ID předplatného služby serveru B. Spusťte skript, zkopírujte upravený skript, vložte do prostředí PowerShell a stiskněte klávesu `Enter`.
 
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

    Zobrazí se stav **iniciováno**. Změní na **připojeno** po nastavení partnerského vztahu pro myVnetA z myVnetB.

10. Odhlaste UserA z Azure a přihlaste se UserB.
11. Vytvoření partnerského připojení z myVnetB do myVnetA. Zkopírujte obsah skriptu v kroku 8 do textového editoru ve vašem počítači. Nahraďte `<SubscriptionB-Id>` s ID předplatného A a změňte všechny jde o B a a. všechny Bs Spusťte skript, zkopírujte upravený skript, vložte ji do prostředí PowerShell a stiskněte klávesu `Enter`.
12. Zobrazte stav partnerského vztahu myVnetB. Do textového editoru ve vašem počítači zkopírujte obsah skriptu v kroku 9. Změna A na B pro skupinu prostředků a názvy virtuálních sítí. Spustit skript, vložte upravený skript do prostředí PowerShell a stiskněte klávesu `Enter`. Zobrazí se stav **připojeno**. Stav partnerského vztahu z **myVnetA** změny **připojeno** po vytvoření partnerského připojení z **myVnetB** k **myVnetA**. Uživatel a se můžete přihlásit zpátky v do Azure a dokončení kroku 9 znovu a ověřte stav partnerského vztahu myVnetA. 

    > [!NOTE]
    > Partnerský vztah není navázán, dokud stav partnerského vztahu je **připojeno** pro obě virtuální sítě.

    Veškeré prostředky Azure, které vytvoříte v obou virtuálních sítích jsou nyní schopen komunikovat mezi sebou pomocí jejich IP adres. Pokud používáte výchozí rozlišování názvů Azure pro virtuální sítě, prostředky ve virtuálních sítích nejsou překládat názvy mezi virtuálními sítěmi. Pokud chcete překlad názvů přes partnerský vztah virtuální sítě, musíte vytvořit vlastní server DNS. Zjistěte, jak nastavit [překladu IP adresy serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

13. **Volitelné**: Když se v tomto kurzu nevztahuje vytváření virtuálních počítačů, můžete vytvoření virtuálního počítače v obou virtuálních sítích a připojit z jednoho virtuálního počítače do jiného, a ověření připojení.
14. **Volitelné**: Chcete-li odstranit prostředky, které vytvoříte v tomto kurzu, proveďte kroky v [odstraňte prostředky](#delete-powershell) v tomto článku.

## <a name="template"></a>Vytvoření partnerského vztahu – šablona Resource Manageru

Pokud jsou virtuální sítě v různých předplatných, a že předplatná jsou přidružená k různým tenantům Azure Active Directory, než budete pokračovat proveďte následující kroky:
 - Přidat uživatele z každého tenanta Active Directory jako [uživatele typu Host](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) v opačné tenanta Azure Active Directory.
 - Každý uživatel má k přijetí pozvánky uživatelů typu Host z opačné tenanta Active Directory.
 
1. Vytvořte virtuální síť a přiřaďte příslušný [oprávnění](virtual-network-manage-peering.md#permissions), proveďte kroky v [portál](#portal), [rozhraní příkazového řádku Azure](#cli), nebo [Powershellu](#powershell)částech tohoto článku.
2. Uložte následující text do souboru na místním počítači. Nahraďte `<subscription ID>` uživatel a ID předplatného. Soubor může uložit jako vnetpeeringA.json, např.

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

3. Připojte se k Azure jako UserA a nasazení pomocí šablony [portál](../azure-resource-manager/resource-group-template-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template), [PowerShell](../azure-resource-manager/resource-group-template-deploy.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-a-template-from-your-local-machine), nebo [rozhraní příkazového řádku Azure](../azure-resource-manager/resource-group-template-deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template). Zadejte název souboru, který jste uložili v kroku 2 k ukázkovému textu json.
4. Zkopírujte json příklad z kroku 2 do souboru ve vašem počítači a proveďte změny řádků, které začínají řetězcem:
    - **Název**: Změna *myVnetA/myVnetAToMyVnetB* k *myVnetB/myVnetBToMyVnetA*.
    - **ID**: Nahraďte `<subscription ID>` s ID předplatného a změnit jeho UserB *myVnetB* k *myVnetA*.
5. Dokončení kroku 3, přihlášení k Azure jako UserB.
6. **Volitelné**: Když se v tomto kurzu nevztahuje vytváření virtuálních počítačů, můžete vytvoření virtuálního počítače v obou virtuálních sítích a připojit z jednoho virtuálního počítače do jiného, a ověření připojení.
7. **Volitelné**: Odstraňte prostředky, které vytvoříte v tomto kurzu, dokončete kroky [odstraňte prostředky](#delete) části tohoto článku pomocí webu Azure portal, Powershellu nebo rozhraní příkazového řádku Azure.

## <a name="delete"></a>Odstranit prostředky
Po dokončení tohoto kurzu, můžete chtít odstranit prostředky, které jste vytvořili v tomto kurzu, takže se vám neúčtovaly poplatky za využívání. Odstranění skupiny prostředků se odstraní také všechny prostředky, které jsou ve skupině prostředků.

### <a name="delete-portal"></a>Azure Portal

1. Přihlaste se k webu Azure portal jako UserA.
2. Portálového vyhledávacího pole zadejte **myResourceGroupA**. Ve výsledcích hledání vyberte **myResourceGroupA**.
3. Vyberte **Odstranit**.
4. Potvrďte odstranění, v **zadejte název skupiny prostředků** zadejte **myResourceGroupA**a pak vyberte **odstranit**.
5. Odhlaste se z portálu jako UserA a přihlaste se jako UserB.
6. Proveďte kroky 2 až 4 pro myResourceGroupB.

### <a name="delete-cli"></a>Azure CLI

1. Připojte se k Azure jako UserA a spusťte následující příkaz:

    ```azurecli-interactive
    az group delete --name myResourceGroupA --yes
    ```
2. Odhlaste se z Azure jako UserA a přihlaste se jako UserB.
3. Spusťte následující příkaz:

    ```azurecli-interactive
    az group delete --name myResourceGroupB --yes
    ```

### <a name="delete-powershell"></a>PowerShell

1. Připojte se k Azure jako UserA a spusťte následující příkaz:

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroupA -force
    ```

2. Odhlaste se z Azure jako UserA a přihlaste se jako UserB.
3. Spusťte následující příkaz:

    ```powershell
    Remove-AzureRmResourceGroup -Name myResourceGroupB -force
    ```

## <a name="next-steps"></a>Další postup

- Důkladně Seznamte se s důležité [omezení partnerských vztahů virtuálních sítí a chování](virtual-network-manage-peering.md#requirements-and-constraints) před vytvořením partnerský vztah pro produkční použití.
- Další informace o všech [nastavení partnerského vztahu virtuálních sítí](virtual-network-manage-peering.md#create-a-peering).
- Zjistěte, jak [vytvoření hvězdicové síťové topologie](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) s využitím partnerského vztahu virtuální sítě.
