---
title: Vytvoření partnerského vztahu virtuální sítě – různá předplatná
titlesuffix: Azure Virtual Network
description: Zjistěte, jak vytvořit partnerský vztah virtuální sítě mezi virtuálními sítěmi vytvořenými prostřednictvím Správce prostředků, které existují v různých předplatných Azure.
services: virtual-network
documentationcenter: ''
author: anavinahar
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/09/2019
ms.author: anavin
ms.openlocfilehash: d085279167b498b13cfb79b97703cfdff7d6dd8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245119"
---
# <a name="create-a-virtual-network-peering---resource-manager-different-subscriptions"></a>Vytvoření partnerského vztahu virtuální sítě – Správce prostředků, různá předplatná

V tomto kurzu se naučíte vytvářet partnerský vztah virtuální sítě mezi virtuálními sítěmi vytvořenými prostřednictvím Správce prostředků. Virtuální sítě existují v různých předplatných. Partnerský vztah dvou virtuálních sítí umožňuje prostředkům v různých virtuálních sítích vzájemnou komunikaci se stejnou šířkou pásma a latencí, jako by byly prostředky ve stejné virtuální síti. Další informace o [partnerském vztahu virtuální sítě](virtual-network-peering-overview.md).

Kroky k vytvoření partnerského vztahu virtuální sítě se liší v závislosti na tom, zda virtuální sítě jsou ve stejné nebo jiné, odběry a které [model nasazení Azure](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuální sítě jsou vytvořeny prostřednictvím. Zjistěte, jak vytvořit partnerský vztah virtuální sítě v jiných scénářích výběrem scénáře z následující tabulky:

|Model nasazení Azure  | Předplatné Azure  |
|--------- |---------|
|[Obě Resource Manager](tutorial-connect-virtual-networks-portal.md) |Stejné|
|[Jedna Resource Manager, druhá Classic](create-peering-different-deployment-models.md) |Stejné|
|[Jedna Resource Manager, druhá Classic](create-peering-different-deployment-models-subscriptions.md) |Různé|

Partnerský vztah virtuální sítě nelze vytvořit mezi dvěma virtuálními sítěmi nasazenými prostřednictvím klasického modelu nasazení. Pokud potřebujete připojit virtuální sítě, které byly vytvořeny prostřednictvím klasického modelu nasazení, můžete k připojení virtuálních sítí použít bránu Azure [VPN Gateway.](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

Tento kurz partnery virtuálnísítě ve stejné oblasti. Můžete také partnerské virtuální sítě v různých [podporovaných oblastech](virtual-network-manage-peering.md#cross-region). Doporučujeme seznámit se s [požadavky a omezeními partnerského vztahu](virtual-network-manage-peering.md#requirements-and-constraints) před partnerským vztahem virtuálních sítí.

K vytvoření partnerského vztahu virtuální sítě můžete použít [portál Azure](#portal), [rozhraní příkazového řádku](#cli) Azure (CLI), Azure [PowerShell](#powershell)nebo [šablonu Azure Resource Manageru.](#template) Vyberte některý z předchozích odkazů na nástroje, chcete-li přejít přímo na kroky pro vytvoření partnerského vztahu virtuální sítě pomocí zvoleného nástroje.

Pokud jsou virtuální sítě v různých předplatných a předplatná jsou přidružena k různým tenantům Služby Azure Active Directory, proveďte před pokračováním následující kroky:
1. Přidejte uživatele z každého klienta služby Active Directory jako [uživatele typu Host](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) v opačném tenantovi služby Azure Active Directory.
1. Každý uživatel musí přijmout pozvánku pro uživatele typu Host z opačného klienta služby Azure Active Directory.

## <a name="create-peering---azure-portal"></a><a name="portal"></a>Vytvoření partnerského vztahu – portál Azure

Následující kroky používají různé účty pro každé předplatné. Pokud používáte účet, který má oprávnění k oběma předplatným, můžete použít stejný účet pro všechny kroky, přeskočit kroky pro odhlášení z portálu a přeskočit kroky pro přiřazení oprávnění jiného uživatele k virtuálním sítím.

1. Přihlaste se k [portálu Azure](https://portal.azure.com) jako *UserA*. Účet, pomocí kterých se přihlásíte, musí mít potřebná oprávnění k vytvoření partnerského vztahu virtuální sítě. Seznam oprávnění naleznete v tématu [Oprávnění partnerského vztahu virtuální sítě](virtual-network-manage-peering.md#permissions).
2. Vyberte **+ Vytvořit prostředek**, vyberte **Síť**a pak vyberte **Virtuální síť**.
3. Vyberte nebo zadejte následující příklady hodnot pro následující nastavení a pak vyberte **Vytvořit**:
    - **Jméno**: *myVnetA*
    - **Adresní prostor**: *10.0.0.0/16*
    - **Název podsítě**: *výchozí*
    - **Rozsah adres podsítě**: *10.0.0.0/24*
    - **Předplatné**: Vyberte předplatné A.
    - **Skupina prostředků**: Vyberte **Vytvořit nový** a zadejte *myResourceGroupA*
    - **Umístění**: *Východní USA*
4. Do pole **Hledat zdroje** v horní části portálu zadejte *myVnetA*. Vyberte **myVnetA,** když se zobrazí ve výsledcích hledání. 
5. Ze svislého seznamu možností na levé straně vyberte **ovládací prvek Přístup (IAM).**
6. V **části myVnetA - Access control (IAM)** vyberte **+ Přidat přiřazení role**.
7. V poli **Role** vyberte **Přispěvatel sítě.**
8. Do pole **Select** vyberte *UserB*nebo zadejte e-mailovou adresu UserB, kterou chcete vyhledat.
9. Vyberte **Uložit**.
10. V **části myVnetA - Access control (IAM)** vyberte **vlastnosti** ze svislého seznamu možností na levé straně. Zkopírujte **ID prostředku**, které se používá v pozdějším kroku. ID prostředku je podobné následujícímu `/subscriptions/<Subscription Id>/resourceGroups/myResourceGroupA/providers/Microsoft.Network/virtualNetworks/myVnetA`příkladu: .
11. Odhlaste se z portálu jako UserA a potom se přihlaste jako UserB.
12. Proveďte kroky 2-3, zadání nebo výběr následujících hodnot v kroku 3:

    - **Název**: *myVnetB*
    - **Adresní prostor**: *10.1.0.0/16*
    - **Název podsítě**: *výchozí*
    - **Rozsah adres podsítě**: *10.1.0.0/24*
    - **Předplatné**: Vyberte předplatné B.
    - **Skupina prostředků**: Vyberte **Vytvořit nový** a zadejte *myResourceGroupB.*
    - **Umístění**: *Východní USA*

13. Do pole **Hledat zdroje** v horní části portálu zadejte *myVnetB*. Vyberte **myVnetB,** když se objeví ve výsledcích hledání.
14. V **části myVnetB**vyberte **vlastnosti** ze svislého seznamu možností na levé straně. Zkopírujte **ID prostředku**, které se používá v pozdějším kroku. ID prostředku je podobné následujícímu `/subscriptions/<Subscription ID>/resourceGroups/myResourceGroupB/providers/Microsoft.ClassicNetwork/virtualNetworks/myVnetB`příkladu: .
15. Vyberte **řízení přístupu (IAM)** pod **myVnetB**a potom proveďte kroky 5-10 pro myVnetB a zadejte **UserA** v kroku 8.
16. Odhlaste se z portálu jako UserB a přihlaste se jako UserA.
17. Do pole **Hledat zdroje** v horní části portálu zadejte *myVnetA*. Vyberte **myVnetA,** když se zobrazí ve výsledcích hledání.
18. Vyberte **myVnetA**.
19. V části **NASTAVENÍ**vyberte **Peerings**.
20. V části **myVnetA - Peerings**vyberte **+ Přidat**
21. V části **Přidat partnerský vztah**zadejte nebo vyberte následující volby a pak vyberte **OK**:
     - **Název**: *myVnetAToMyVnetB*
     - **Model nasazení virtuální sítě**: Vyberte **Správce prostředků**.
     - **Znám ID svého zdroje**: Zaškrtněte toto políčko.
     - **ID prostředku:** Zadejte ID prostředku z kroku 14.
     - **Povolit přístup k virtuální síti:** Ujistěte se, že je vybrána možnost **Povoleno.**
    V tomto kurzu se nepoužívají žádná další nastavení. Informace o všech nastaveních partnerského vztahu najdete v části [Správa partnerských partnerských uživatelů virtuálních sítí](virtual-network-manage-peering.md#create-a-peering).
22. Vytvořený partnerský vztah se zobrazí jako krátké čekání po výběru **OK** v předchozím kroku. **Inicializováno** je uvedeno ve sloupci **STATUS PARTNERSkého VZTAHU** pro partnerský vztah **myVnetAToMyVnetB,** který jste vytvořili. Nahlíželi jste na myVnetA na myVnetB, ale nyní musíte nahlížet do myVnetA. Partnerský vztah musí být vytvořen v obou směrech, aby prostředky ve virtuálních sítích mohly vzájemně komunikovat.
23. Odhlaste se z portálu jako UserA a přihlaste se jako UserB.
24. Kompletní kroky 17-21 opět pro myVnetB. V kroku 21 pojmenujte partnerský *program myVnetBToMyVnetA*, vyberte *myVnetA* pro **virtuální síť**a zadejte ID z kroku 10 do pole **ID prostředku.**
25. Několik sekund po výběru **OK** k vytvoření partnerského vztahu pro myVnetB je partnerský vztah **myVnetBToMyVnetA,** který jste právě vytvořili, uveden ve **sloupci Připojeno** ve **sloupci Stav partnerského vztahu.**
26. Odhlaste se z portálu jako UserB a přihlaste se jako UserA.
27. Znovu proveďte kroky 17-19. **Stav peeringu** pro partnerský vztah **myVnetAToVNetB** je nyní také **připojen**. Partnerský vztah je úspěšně vytvořen poté, co uvidíte **Připojeno** ve **sloupci STAV PARTNERSkého VZTAHU** pro obě virtuální sítě v partnerské síti. Všechny prostředky Azure, které vytvoříte v obou virtuálních sítích, teď mohou komunikovat prostřednictvím svých IP adres. Pokud používáte výchozí překlad názvů Azure pro virtuální sítě, prostředky ve virtuálních sítích nejsou schopny přeložit názvy napříč virtuálními sítěmi. Pokud chcete přeložit názvy napříč virtuálními sítěmi v partnerské síti, musíte vytvořit vlastní server DNS. Přečtěte si, jak nastavit [překlad názvů pomocí vlastního serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
28. **Volitelné**: I když vytváření virtuálních počítačů není zahrnuto v tomto kurzu, můžete vytvořit virtuální počítač v každé virtuální síti a připojit se z jednoho virtuálního počítače do druhého, k ověření připojení.
29. **Volitelné**: Chcete-li odstranit prostředky, které vytvoříte v tomto kurzu, proveďte kroky v části [Odstranit prostředky](#delete-portal) tohoto článku.

## <a name="create-peering---azure-cli"></a><a name="cli"></a>Vytvoření partnerského vztahu – Azure CLI

Tento kurz používá různé účty pro každé předplatné. Pokud používáte účet, který má oprávnění k oběma předplatným, můžete použít stejný účet pro všechny kroky, přeskočit kroky pro odhlášení z Azure a odebrat řádky skriptu, které vytvářejí přiřazení rolí uživatele. Nahraďte UserA@azure.com a UserB@azure.com ve všech následujících skriptech uživatelská jména, která používáte pro UserA a UserB. 

Následující skripty:

- Vyžaduje Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete upgradovat, [přečtěte si informace o instalaci příkazového příkazového příkazu k webu Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Pracuje v bash shellu. Možnosti spuštění skriptů Azure CLI na klientovi s Windows najdete v tématu věnovaném [instalaci Azure CLI ve Windows](/cli/azure/install-azure-cli-windows).

Místo instalace příkazového příkazového příkazu a jeho závislostí můžete použít Prostředí Azure Cloud Shell. Služba Azure Cloud Shell je volně dostupné prostředí Bash, které můžete spustit přímo z portálu Azure Portal. Má předinstalované rozhraní Azure CLI, které je nakonfigurované pro použití s vaším účtem. Vyberte tlačítko **Try it** ve skriptu, který následuje, který vyvolá Cloud Shell, který můžete přihlásit ke svému účtu Azure.

1. Otevřete relaci rozhraní příkazového příkazu a `azure login` přihlaste se do Azure jako UserA pomocí příkazu. Účet, pomocí kterých se přihlásíte, musí mít potřebná oprávnění k vytvoření partnerského vztahu virtuální sítě. Seznam oprávnění naleznete v tématu [Oprávnění partnerského vztahu virtuální sítě](virtual-network-manage-peering.md#permissions).
2. Zkopírujte následující skript do textového editoru v počítači, nahraďte `<SubscriptionA-Id>` ID subscriptiona, zkopírujte upravený `Enter`skript, vložte jej do relace rozhraní příkazového příkazu a stiskněte klávesu . Pokud Id předplatného neznáte, zadejte `az account show` příkaz. Hodnota **id** ve výstupu je vaše Id předplatného.

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

3. Odhlaste se z Azure `az logout` jako UserA pomocí příkazu a pak se přihlaste do Azure jako UserB. Účet, pomocí kterých se přihlásíte, musí mít potřebná oprávnění k vytvoření partnerského vztahu virtuální sítě. Seznam oprávnění naleznete v tématu [Oprávnění partnerského vztahu virtuální sítě](virtual-network-manage-peering.md#permissions).
4. Vytvořte myVnetB. Zkopírujte obsah skriptu v kroku 2 do textového editoru v počítači. Nahraďte `<SubscriptionA-Id>` id SubscriptionB. Změňte 10.0.0.0/16 na 10.1.0.0/16, změňte všechny možnosti B a všechny bs na A. Zkopírujte `Enter`upravený skript, vložte jej do relace příkazového příkazu CLI a stiskněte klávesu .
5. Odhlaste se z Azure jako UserB a přihlaste se do Azure jako UserA.
6. Vytvořte partnerský vztah virtuální sítě z myVnetA do myVnetB. Zkopírujte následující obsah skriptu do textového editoru v počítači. Nahraďte `<SubscriptionB-Id>` id SubscriptionB. Chcete-li skript spustit, zkopírujte upravený skript, vložte jej do relace příkazového příkazu a stiskněte Enter.

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

7. Zobrazit peering stavu myVnetA.

    ```azurecli-interactive
    az network vnet peering list \
      --resource-group myResourceGroupA \
      --vnet-name myVnetA \
      --output table
    ```

    Stav je **Zahájeno**. Změní se na **Připojeno,** jakmile vytvoříte partnerský vztah na myVnetA z myVnetB.

8. Odhlaste se usera z Azure a přihlaste se do Azure jako UserB.
9. Vytvořte peering z myVnetB na myVnetA. Zkopírujte obsah skriptu v kroku 6 do textového editoru v počítači. Nahraďte `<SubscriptionB-Id>` id pro SubscriptionA a změňte všechny jako B a všechny Bs na A. Po provedení změn zkopírujte upravený skript, vložte jej do `Enter`relace příkazového příkazu a stiskněte klávesu .
10. Zobrazit peering stavu myVnetB. Zkopírujte obsah skriptu v kroku 7 do textového editoru v počítači. Změňte a na B pro názvy skupin prostředků a virtuální sítě, zkopírujte skript, `Enter`vložte upravený skript do relace příkazového příkazu a stiskněte klávesu . Stav partnerského vztahu je **Připojeno**. Peering stav myVnetA změní **na Připojeno** po vytvoření peering z myVnetB na myVnetA. Můžete se přihlásit UserA zpět do Azure a dokončit krok 7 znovu ověřit stav partnerského vztahu myVnetA. 

    > [!NOTE]
    > Partnerský vztah není vytvořen, dokud není stav partnerského vztahu **Připojeno** pro obě virtuální sítě.

11. **Volitelné**: I když vytváření virtuálních počítačů není zahrnuto v tomto kurzu, můžete vytvořit virtuální počítač v každé virtuální síti a připojit se z jednoho virtuálního počítače do druhého, k ověření připojení.
12. **Volitelné**: Chcete-li odstranit prostředky, které vytvoříte v tomto kurzu, proveďte kroky v [odstranění prostředků](#delete-cli) v tomto článku.

Všechny prostředky Azure, které vytvoříte v obou virtuálních sítích, teď mohou komunikovat prostřednictvím svých IP adres. Pokud používáte výchozí překlad názvů Azure pro virtuální sítě, prostředky ve virtuálních sítích nejsou schopny přeložit názvy napříč virtuálními sítěmi. Pokud chcete přeložit názvy napříč virtuálními sítěmi v partnerské síti, musíte vytvořit vlastní server DNS. Přečtěte si, jak nastavit [překlad názvů pomocí vlastního serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

## <a name="create-peering---powershell"></a><a name="powershell"></a>Vytvoření partnerského vztahu – PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Tento kurz používá různé účty pro každé předplatné. Pokud používáte účet, který má oprávnění k oběma předplatným, můžete použít stejný účet pro všechny kroky, přeskočit kroky pro odhlášení z Azure a odebrat řádky skriptu, které vytvářejí přiřazení rolí uživatele. Nahraďte UserA@azure.com a UserB@azure.com ve všech následujících skriptech uživatelská jména, která používáte pro UserA a UserB.

1. Zkontrolujte, zda máte Azure PowerShell verze 1.0.0 nebo vyšší. Můžete to provést spuštěním `Get-Module -Name Az` doporučujeme nainstalovat nejnovější verzi modulu PowerShell [Az](/powershell/azure/install-az-ps). Pokud s Azure PowerShellem začínáte, podívejte se na [Přehled Azure PowerShellu](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json). 
2. Spusťte relaci Prostředí PowerShell.
3. V PowerShellu se přihlaste do `Connect-AzAccount` Azure jako UserA zadáním příkazu. Účet, pomocí kterých se přihlásíte, musí mít potřebná oprávnění k vytvoření partnerského vztahu virtuální sítě. Seznam oprávnění naleznete v tématu [Oprávnění partnerského vztahu virtuální sítě](virtual-network-manage-peering.md#permissions).
4. Vytvoření skupiny prostředků a virtuální sítě A. Zkopírujte následující skript do textového editoru v počítači. Nahraďte `<SubscriptionA-Id>` id SubscriptionA. Pokud Id předplatného neznáte, zadejte `Get-AzSubscription` příkaz, který chcete zobrazit. Hodnota **ID** ve vráceném výstupu je Vaše ID předplatného. Chcete-li skript spustit, zkopírujte upravený skript, vložte `Enter`jej do prostředí PowerShell a stiskněte klávesu .

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

5. Odhlásit UserA z Azure a přihlásit UserB. Účet, pomocí kterých se přihlásíte, musí mít potřebná oprávnění k vytvoření partnerského vztahu virtuální sítě. Seznam oprávnění naleznete v tématu [Oprávnění partnerského vztahu virtuální sítě](virtual-network-manage-peering.md#permissions).
6. Zkopírujte obsah skriptu v kroku 4 do textového editoru v počítači. Nahraďte `<SubscriptionA-Id>` id předplatného B. Změna 10.0.0.0/16 na 10.1.0.0/16. Změňte všechny jako B a všechny Bs na A. Chcete-li skript spustit, zkopírujte upravený skript, vložte jej do prostředí PowerShell a stiskněte klávesu `Enter`.
7. Odhlásit UserB z Azure a přihlásit UserA.
8. Vytvořte partnerský vztah z myVnetA do myVnetB. Zkopírujte následující skript do textového editoru v počítači. Nahraďte `<SubscriptionB-Id>` id předplatného B. Chcete-li skript spustit, zkopírujte upravený skript, vložte `Enter`jej do prostředí PowerShell a stiskněte klávesu .

   ```powershell
   # Peer myVnetA to myVnetB.
   $vNetA=Get-AzVirtualNetwork -Name myVnetA -ResourceGroupName myResourceGroupA
   Add-AzVirtualNetworkPeering `
     -Name 'myVnetAToMyVnetB' `
     -VirtualNetwork $vNetA `
     -RemoteVirtualNetworkId "/subscriptions/<SubscriptionB-Id>/resourceGroups/myResourceGroupB/providers/Microsoft.Network/virtualNetworks/myVnetB"
   ```

9. Zobrazit peering stavu myVnetA.

    ```powershell
    Get-AzVirtualNetworkPeering `
      -ResourceGroupName myResourceGroupA `
      -VirtualNetworkName myVnetA `
      | Format-Table VirtualNetworkName, PeeringState
    ```

    Stav je **Zahájeno**. Změní se na **Připojeno,** jakmile nastavíte partnerský vztah na myVnetA z myVnetB.

10. Odhlásit UserA z Azure a přihlásit UserB.
11. Vytvořte peering z myVnetB na myVnetA. Zkopírujte obsah skriptu v kroku 8 do textového editoru v počítači. Nahraďte `<SubscriptionB-Id>` id předplatného A a změňte všechny jako B a všechny Bs na A. Chcete-li skript spustit, zkopírujte upravený skript, vložte `Enter`jej do prostředí PowerShell a stiskněte klávesu .
12. Zobrazit peering stavu myVnetB. Zkopírujte obsah skriptu v kroku 9 do textového editoru v počítači. Změňte A na B pro názvy skupin prostředků a virtuální sítě. Chcete-li skript spustit, vložte upravený skript `Enter`do prostředí PowerShell a stiskněte klávesu . Stav je **Připojeno**. Peering stav **myVnetA** změní **na Připojeno** po vytvoření peering z **myVnetB** na **myVnetA**. Můžete se přihlásit UserA zpět do Azure a dokončit krok 9 znovu ověřit stav partnerského vztahu myVnetA.

    > [!NOTE]
    > Partnerský vztah není vytvořen, dokud není stav partnerského vztahu **Připojeno** pro obě virtuální sítě.

    Všechny prostředky Azure, které vytvoříte v obou virtuálních sítích, teď mohou komunikovat prostřednictvím svých IP adres. Pokud používáte výchozí překlad názvů Azure pro virtuální sítě, prostředky ve virtuálních sítích nejsou schopny přeložit názvy napříč virtuálními sítěmi. Pokud chcete přeložit názvy napříč virtuálními sítěmi v partnerské síti, musíte vytvořit vlastní server DNS. Přečtěte si, jak nastavit [překlad názvů pomocí vlastního serveru DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

13. **Volitelné**: I když vytváření virtuálních počítačů není zahrnuto v tomto kurzu, můžete vytvořit virtuální počítač v každé virtuální síti a připojit se z jednoho virtuálního počítače do druhého, k ověření připojení.
14. **Volitelné**: Chcete-li odstranit prostředky, které vytvoříte v tomto kurzu, proveďte kroky v [odstranění prostředků](#delete-powershell) v tomto článku.

## <a name="create-peering---resource-manager-template"></a><a name="template"></a>Vytvořit partnerský vztah – šablona Správce prostředků

1. Chcete-li vytvořit virtuální síť a přiřadit příslušná [oprávnění](virtual-network-manage-peering.md#permissions), proveďte kroky v tomto článku v části [Portál](#portal), [Azure CLI](#cli)nebo [PowerShell.](#powershell)
2. Uložte následující text do souboru v místním počítači. Nahraďte `<subscription ID>` id předplatného UserA. Soubor můžete uložit například jako vnetpeeringA.json.

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

3. Přihlaste se k Azure jako UserA a nasadit šablonu pomocí [portálu](../azure-resource-manager/templates/deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template), [PowerShellu](../azure-resource-manager/templates/deploy-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template)nebo [rozhraní příkazového příkazu k Andazure](../azure-resource-manager/templates/deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-local-template). Zadejte název souboru, který jste uložili v kroku 2 do příkladu textu json.
4. Zkopírujte příklad json z kroku 2 do souboru v počítači a proveďte změny řádků, které začínají:
   - **název**: Změňte *myVnetA/myVnetAToMyVnetB* na *myVnetB/myVnetBToMyVnetA*.
   - **id**: `<subscription ID>` Nahraďte iD předplatného UserB a změňte *myVnetB* na *myVnetA*.
5. Znovu dokončete krok 3, který jste do Azure přihlášeni jako UserB.
6. **Volitelné**: I když vytváření virtuálních počítačů není zahrnuto v tomto kurzu, můžete vytvořit virtuální počítač v každé virtuální síti a připojit se z jednoho virtuálního počítače do druhého, k ověření připojení.
7. **Volitelné**: Chcete-li odstranit prostředky, které vytvoříte v tomto kurzu, proveďte kroky v části [Odstranit prostředky](#delete) tohoto článku pomocí portálu Azure, PowerShellu nebo příkazového příkazového příkazu k Azure.

## <a name="delete-resources"></a><a name="delete"></a>Odstranění prostředků
Po dokončení tohoto kurzu můžete chtít odstranit prostředky, které jste vytvořili v kurzu, takže vám nebudou účtovány poplatky za využití. Odstraněním skupiny prostředků také odstraníte všechny prostředky, které jsou ve skupině prostředků.

### <a name="azure-portal"></a><a name="delete-portal"></a>Portál Azure

1. Přihlaste se k portálu Azure jako UserA.
2. Do vyhledávacího pole portálu zadejte **myResourceGroupA**. Ve výsledcích hledání vyberte **myResourceGroupA**.
3. Vyberte **Odstranit**.
4. Chcete-li odstranění potvrdit, zadejte do pole **ZADEJTE NÁZEV SKUPINY PROSTŘEDKŮ** **položku myResourceGroupA**a vyberte příkaz **Odstranit**.
5. Odhlaste se z portálu jako UserA a přihlaste se jako UserB.
6. Proveďte kroky 2-4 pro myResourceGroupB.

### <a name="azure-cli"></a><a name="delete-cli"></a>Azure CLI

1. Přihlaste se do Azure jako UserA a spusťte následující příkaz:

   ```azurecli-interactive
   az group delete --name myResourceGroupA --yes
   ```

2. Odhlaste se z Azure jako UserA a přihlaste se jako UserB.
3. Spusťte následující příkaz:

   ```azurecli-interactive
   az group delete --name myResourceGroupB --yes
   ```

### <a name="powershell"></a><a name="delete-powershell"></a>PowerShell

1. Přihlaste se do Azure jako UserA a spusťte následující příkaz:

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupA -force
   ```

2. Odhlaste se z Azure jako UserA a přihlaste se jako UserB.
3. Spusťte následující příkaz:

   ```powershell
   Remove-AzResourceGroup -Name myResourceGroupB -force
   ```

## <a name="next-steps"></a>Další kroky

- Před vytvořením partnerského vztahu virtuální sítě pro produkční použití se důkladně seznamte s důležitými [omezeními a chováním partnerského vztahu](virtual-network-manage-peering.md#requirements-and-constraints) virtuální sítě.
- Informace o všech [nastaveních partnerského vztahu virtuální sítě](virtual-network-manage-peering.md#create-a-peering).
- Zjistěte, jak [vytvořit topologii sítě rozbočovače a paprsku](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) pomocí partnerského vztahu virtuální sítě.
