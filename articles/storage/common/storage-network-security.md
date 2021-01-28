---
title: Konfigurace Azure Storage bran firewall a virtuálních sítí | Microsoft Docs
description: Nakonfigurujte vícevrstvé zabezpečení sítě pro svůj účet úložiště pomocí Azure Storage bran firewall a Azure Virtual Network.
services: storage
author: santoshc
ms.service: storage
ms.topic: how-to
ms.date: 01/27/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 5a1ad898b745bbb49421c1bc0b5a9b2e5c8ec0f6
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98985988"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Konfigurace bran firewall Azure Storage a virtuálních sítí

Azure Storage poskytuje vrstvený model zabezpečení. Tento model vám umožní zabezpečit a řídit úroveň přístupu k účtům úložiště, které vaše aplikace a podniková prostředí vyžadují, na základě typu a podmnožiny sítí nebo prostředků. Při konfiguraci síťových pravidel mají přístup k účtu úložiště jenom aplikace požadující data přes zadanou sadu sítí nebo prostřednictvím zadané sady prostředků Azure. Přístup k účtu úložiště můžete omezit na požadavky pocházející ze zadaných IP adres, rozsahů IP adres, podsítí v Azure Virtual Network (VNet) nebo instancí prostředků některých služeb Azure.

Účty úložiště mají veřejný koncový bod, který je přístupný prostřednictvím Internetu. [Pro svůj účet úložiště můžete také vytvořit privátní koncové body](storage-private-endpoints.md), které přiřadí privátní IP adresu z vaší virtuální sítě k účtu úložiště a zabezpečuje veškerý provoz mezi vaší virtuální sítí a účtem úložiště prostřednictvím privátního propojení. Brána firewall služby Azure Storage poskytuje řízení přístupu pro veřejný koncový bod vašeho účtu úložiště. Bránu firewall můžete použít také k blokování veškerého přístupu prostřednictvím veřejného koncového bodu při používání privátních koncových bodů. Vaše konfigurace brány firewall pro úložiště také umožňuje, aby bylo možné bezpečně přistupovat k účtu úložiště pomocí výběru důvěryhodných služeb platformy Azure.

Aplikace, která přistupuje k účtu úložiště v případě, že jsou platná síťová pravidla, vyžaduje pro požadavek správnou autorizaci. Autorizace se podporuje s přihlašovacími údaji služby Azure Active Directory (Azure AD) pro objekty BLOB a front, s platným klíčem pro přístup k účtu nebo s tokenem SAS.

> [!IMPORTANT]
> Zapnutím pravidel brány firewall pro váš účet úložiště se ve výchozím nastavení zablokuje příchozí požadavky na data, pokud žádosti pocházejí ze služby v rámci Azure Virtual Network (VNet) nebo z povolených veřejných IP adres. Blokované požadavky zahrnují ty z jiných služeb Azure, od Azure Portal, ze služeb protokolování a metriky atd.
>
> Přístup ke službám Azure, které provozují v rámci virtuální sítě, můžete udělit povolením provozu z podsítě hostující instanci služby. Pomocí mechanismu výjimek popsaných níže můžete také povolit omezený počet scénářů. Pokud chcete získat přístup k datům z účtu úložiště prostřednictvím Azure Portal, musíte být na počítači v rámci důvěryhodné hranice (buď IP nebo VNet), kterou jste nastavili.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Scénáře

Chcete-li zabezpečit svůj účet úložiště, je třeba nejprve nakonfigurovat pravidlo pro odepření přístupu k provozu ze všech sítí (včetně internetového provozu) ve výchozím nastavení ve veřejném koncovém bodu. Pak byste měli nakonfigurovat pravidla, která udělí přístup k provozu z konkrétních virtuální sítě. Můžete taky nakonfigurovat pravidla pro udělení přístupu k provozu z vybraných rozsahů veřejných IP adres, které umožňují připojení z určitých internetových nebo místních klientů. Tato konfigurace umožňuje vytvořit zabezpečenou hranici sítě pro vaše aplikace.

Můžete zkombinovat pravidla brány firewall, která umožňují přístup z konkrétních virtuálních sítí a z rozsahů veřejných IP adres ve stejném účtu úložiště. Pravidla brány firewall pro úložiště se dají použít u existujících účtů úložiště nebo při vytváření nových účtů úložiště.

Pravidla brány firewall pro úložiště se vztahují na veřejný koncový bod účtu úložiště. Nepotřebujete žádná pravidla přístupu k bránám firewall, aby bylo možné povolit přenosy privátních koncových bodů účtu úložiště. Proces schvalování privátního koncového bodu uděluje implicitní přístup k provozu z podsítě, která je hostitelem privátního koncového bodu.

Síťová pravidla jsou vynutila ve všech síťových protokolech pro Azure Storage, včetně REST a protokolu SMB. Chcete-li získat přístup k datům pomocí nástrojů, jako jsou Azure Portal, Průzkumník služby Storage a AZCopy, musí být nakonfigurována explicitní Síťová pravidla.

Po použití pravidel sítě se vynutily pro všechny požadavky. Tokeny SAS, které udělují přístup k určité IP adrese, slouží k omezení přístupu držitele tokenu, ale neudělují se novému přístupu nad nakonfigurovaná Síťová pravidla.

Síťový provoz virtuálního počítače (včetně operací připojení a odpojení a vstupně-výstupní operace disku) nejsou ovlivněná síťovými pravidly. Přístup REST k objektům blob stránky je chráněn síťovými pravidly.

Klasické účty úložiště nepodporují brány firewall a virtuální sítě.

Nespravované disky můžete použít v účtech úložiště s pravidly sítě použitými pro zálohování a obnovení virtuálních počítačů, a to vytvořením výjimky. Tento postup je popsán v části [Správa výjimek](#manage-exceptions) v tomto článku. Výjimky brány firewall se nevztahují na spravované disky, protože už jsou spravované přes Azure.

## <a name="change-the-default-network-access-rule"></a>Změna výchozího pravidla přístupu k síti

Účty úložiště ve výchozím nastavení přijímají připojení z klientů v jakékoli síti. Chcete-li omezit přístup na vybrané sítě, musíte nejdřív změnit výchozí akci.

> [!WARNING]
> Změny pravidel sítě mohou mít vliv na schopnost vaší aplikace připojit se k Azure Storage. Nastavením výchozího síťového pravidla na **Odepřít** znemožníte přístup k datům, pokud nejsou použita specifická Síťová pravidla, která **udělují** přístup. Než změníte výchozí pravidlo a odepřete přístup, nezapomeňte prostřednictvím pravidel sítě udělit přístup všem povoleným sítím.

### <a name="managing-default-network-access-rules"></a>Správa výchozích pravidel síťového přístupu

Pomocí Azure Portal, PowerShellu nebo CLIv2 můžete spravovat výchozí pravidla přístupu k síti pro účty úložiště.

#### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Přejděte do účtu úložiště, který chcete zabezpečit.

2. Vyberte položku v nabídce nastavení s názvem **síť**.

3. Chcete-li odepřít přístup ve výchozím nastavení, vyberte možnost povolit přístup z **vybraných sítí**. Pokud chcete povolit přenos ze všech sítí, zvolte povolení přístupu ze **všech sítí**.

4. Vyberte **Uložit**, aby se tyto změny použily.

<a id="powershell"></a>

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Nainstalujte [Azure PowerShell](/powershell/azure/install-Az-ps) a [přihlaste](/powershell/azure/authenticate-azureps)se.

2. Zobrazí stav výchozího pravidla pro účet úložiště.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

3. Nastavte výchozí pravidlo na odepřít přístup k síti ve výchozím nastavení.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

4. Nastavte výchozí pravidlo tak, aby povolovalo přístup k síti ve výchozím nastavení.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Nainstalujte rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) a [přihlaste](/cli/azure/authenticate-azure-cli)se.

2. Zobrazí stav výchozího pravidla pro účet úložiště.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

3. Nastavte výchozí pravidlo na odepřít přístup k síti ve výchozím nastavení.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

4. Nastavte výchozí pravidlo tak, aby povolovalo přístup k síti ve výchozím nastavení.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```
---

## <a name="grant-access-from-a-virtual-network"></a>Udělení přístupu z virtuální sítě

Účty úložiště můžete nakonfigurovat tak, aby povolovaly přístup jenom z konkrétních podsítí. Povolené podsítě můžou patřit do virtuální sítě ve stejném předplatném nebo v jiném předplatném, včetně předplatných, která patří do jiného tenanta Azure Active Directory.

Povolte [koncový bod služby](../../virtual-network/virtual-network-service-endpoints-overview.md) pro Azure Storage v rámci virtuální sítě. Koncový bod služby směruje provoz z virtuální sítě prostřednictvím optimální cesty ke službě Azure Storage. Identity podsítě a virtuální sítě se také odesílají s každým požadavkem. Správci potom můžou nakonfigurovat Síťová pravidla pro účet úložiště, který povoluje příjem požadavků z konkrétních podsítí ve virtuální síti. Klienti s uděleným přístupem přes tato Síťová pravidla musí nadále splňovat požadavky na autorizaci účtu úložiště pro přístup k datům.

Každý účet úložiště podporuje až 200 pravidel virtuální sítě, která se můžou kombinovat s [pravidly sítě IP](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Dostupné oblasti virtuální sítě

Obecně platí, že koncové body služby fungují mezi virtuálními sítěmi a instancemi služby ve stejné oblasti Azure. Při použití koncových bodů služby s Azure Storage se tento rozsah zvětšuje tak, aby zahrnoval [spárovánou oblast](../../best-practices-availability-paired-regions.md). Koncové body služby umožňují kontinuitu v rámci regionálního převzetí služeb při selhání a přístup k instancím geograficky redundantního úložiště jen pro čtení (RA-GRS). Síťová pravidla, která udělují přístup z virtuální sítě k účtu úložiště, taky udělují přístup k libovolné instanci RA-GRS.

Při plánování zotavení po havárii během regionálního výpadku byste měli vytvořit virtuální sítě v párové oblasti předem. Povolte koncové body služby pro Azure Storage se síťovými pravidly udělujících přístup z těchto alternativních virtuálních sítí. Tato pravidla pak použijte pro vaše geograficky redundantní účty úložiště.

> [!NOTE]
> Koncové body služby se nevztahují na provoz mimo oblast virtuální sítě a vymezené dvojice oblastí. Můžete použít jenom Síťová pravidla udělující přístup z virtuálních sítí k účtům úložiště v primární oblasti účtu úložiště nebo v určené spárované oblasti.

### <a name="required-permissions"></a>Požadovaná oprávnění

Aby bylo možné použít pravidlo virtuální sítě pro účet úložiště, musí mít uživatel příslušná oprávnění pro přidávané podsítě. Potřebná oprávnění se *připojují ke službě do podsítě* a jsou součástí předdefinované role *přispěvatele účtu úložiště* . Dá se taky přidat k definicím vlastních rolí.

Účet úložiště a udělený přístup k virtuálním sítím můžou být v různých předplatných, včetně předplatných, která jsou součástí jiného tenanta Azure AD.

> [!NOTE]
> Konfigurace pravidel, která udělují přístup k podsítím ve virtuálních sítích, které jsou součástí jiného tenanta Azure Active Directory, se v současné době podporují jenom prostřednictvím PowerShellu, CLI a rozhraní REST API. Taková pravidla nelze konfigurovat prostřednictvím Azure Portal, i když je lze zobrazit na portálu.

### <a name="managing-virtual-network-rules"></a>Správa pravidel virtuální sítě

Pravidla virtuální sítě pro účty úložiště můžete spravovat prostřednictvím Azure Portal, PowerShellu nebo CLIv2.

#### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Přejděte do účtu úložiště, který chcete zabezpečit.

2. Vyberte položku v nabídce nastavení s názvem **síť**.

3. Ověřte, že jste vybrali povolení přístupu z **vybraných sítí**.

4. Chcete-li udělit přístup k virtuální síti pomocí nového pravidla sítě, vyberte v části **virtuální sítě** možnost **Přidat existující virtuální síť**, vyberte možnost **virtuální sítě** a **podsítě** a pak vyberte **Přidat**. Pokud chcete vytvořit novou virtuální síť a udělit jí přístup, vyberte **Přidat novou virtuální síť**. Zadejte informace potřebné k vytvoření nové virtuální sítě a pak vyberte **vytvořit**.

    > [!NOTE]
    > Pokud koncový bod služby pro Azure Storage ještě nebyl nakonfigurovaný pro vybranou virtuální síť a podsítě, můžete ho nakonfigurovat jako součást této operace.
    >
    > V současné době se při vytváření pravidel zobrazují jenom virtuální sítě patřící do stejného Azure Active Directory tenanta. Pokud chcete udělit přístup k podsíti ve virtuální síti patřící jinému tenantovi, použijte PowerShell, rozhraní příkazového řádku nebo rozhraní REST API.

5. Chcete-li odebrat virtuální síť nebo pravidlo podsítě, vyberte **...** a otevřete tak kontextovou nabídku pro virtuální síť nebo podsíť a vyberte možnost **Odebrat**.

6. Pokud chcete změny použít, vyberte **Uložit** .

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Nainstalujte [Azure PowerShell](/powershell/azure/install-Az-ps) a [přihlaste](/powershell/azure/authenticate-azureps)se.

2. Vypíše pravidla virtuální sítě.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

3. Povolte koncový bod služby pro Azure Storage v existující virtuální síti a podsíti.

    ```powershell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

4. Přidejte síťové pravidlo pro virtuální síť a podsíť.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

    > [!TIP]
    > Pokud chcete přidat síťové pravidlo pro podsíť ve virtuální síti, která patří do jiného tenanta Azure AD, použijte plně kvalifikovaný parametr **VirtualNetworkResourceId** ve formátu "/Subscriptions/Subscription-ID/resourceGroups/resourceGroup-Name/Providers/Microsoft.Network/virtualNetworks/vNet-Name/subnets/Subnet-Name".

5. Odeberte pravidlo sítě pro virtuální síť a podsíť.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> Ujistěte se, že jste [výchozí pravidlo nastavili](#change-the-default-network-access-rule) na **Odepřít**, nebo že Síťová pravidla nemají žádný vliv.

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Nainstalujte rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) a [přihlaste](/cli/azure/authenticate-azure-cli)se.

2. Vypíše pravidla virtuální sítě.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

3. Povolte koncový bod služby pro Azure Storage v existující virtuální síti a podsíti.

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

4. Přidejte síťové pravidlo pro virtuální síť a podsíť.

    ```azurecli
    subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

    > [!TIP]
    > Pokud chcete přidat pravidlo pro podsíť ve virtuální síti, která patří do jiného tenanta Azure AD, použijte plně kvalifikované ID podsítě ve formátu "/Subscriptions/ \<subscription-ID\> /ResourceGroups/ \<resourceGroup-Name\> /providers/Microsoft.Network/virtualNetworks/ \<vNet-name\> /subnets/ \<subnet-name\> ".
    >
    > Pomocí parametru **Subscription** můžete načíst ID podsítě pro virtuální síť patřící jinému Tenantovi služby Azure AD.

5. Odeberte pravidlo sítě pro virtuální síť a podsíť.

    ```azurecli
    subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> Ujistěte se, že jste [výchozí pravidlo nastavili](#change-the-default-network-access-rule) na **Odepřít**, nebo že Síťová pravidla nemají žádný vliv.

---

## <a name="grant-access-from-an-internet-ip-range"></a>Udělení přístupu z rozsahu internetových IP adres

Účty úložiště můžete nakonfigurovat tak, aby povolovaly přístup z určitých rozsahů veřejných internetových IP adres. Tato konfigurace uděluje přístup ke konkrétním internetovým službám a místním sítím a blokuje obecný internetový provoz.

Zadejte povolené rozsahy internetových adres pomocí [zápisu CIDR](https://tools.ietf.org/html/rfc4632) ve formě *16.17.18.0/24* nebo jako jednotlivé IP adresy, jako je *16.17.18.19*.

   > [!NOTE]
   > Malé rozsahy adres používající velikosti předpony "/31" nebo "/32" se nepodporují. Tyto rozsahy by měly být nakonfigurované pomocí jednotlivých pravidel IP adres.

Pravidla sítě IP jsou povolená jenom pro **veřejné internetové** IP adresy. Rozsahy IP adres rezervované pro privátní sítě (definované v [dokumentu RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)) nejsou povolené v pravidlech protokolu IP. Soukromé sítě obsahují adresy, které začínají na _10. *_, _172,16. *_  -  _172,31. *_ a _192,168. *_.

   > [!NOTE]
   > Pravidla sítě IP neovlivňují požadavky pocházející ze stejné oblasti Azure jako účet úložiště. Použijte [pravidla virtuální sítě](#grant-access-from-a-virtual-network) a povolte tak požadavky stejné oblasti.

  > [!NOTE]
  > Služby nasazené ve stejné oblasti jako účet úložiště používají privátní IP adresy Azure ke komunikaci. Proto nemůžete omezit přístup ke konkrétním službám Azure na základě jejich veřejného odchozího rozsahu IP adres.

Pro konfiguraci pravidel brány firewall úložiště se podporují jenom IPV4 adresy.

Každý účet úložiště podporuje až 200 pravidel sítě IP.

### <a name="configuring-access-from-on-premises-networks"></a>Konfigurace přístupu z místních sítí

Pokud chcete udělit přístup z místních sítí k vašemu účtu úložiště pomocí pravidla sítě protokolu IP, musíte identifikovat internetové IP adresy, které používá vaše síť. Požádejte o nápovědu správce sítě.

Pokud používáte [ExpressRoute](../../expressroute/expressroute-introduction.md) z vašich místních partnerských vztahů nebo partnerských vztahů Microsoftu, budete muset určit IP adresy NAT, které se používají. Ve veřejných partnerských vztazích každý okruh ExpressRoute automaticky využívá dvě IP adresy pro překlad adres (NAT), které se používají k provozu služeb Azure při vstupu do páteřní sítě Microsoft Azure. V případě partnerského vztahu Microsoftu se používají IP adresy NAT buď poskytované zákazníkem, nebo poskytovatelem služeb. Pokud chcete povolit přístup k prostředkům služby, musíte tyto veřejné IP adresy povolit v nastavení IP adresy brány firewall prostředku. Pokud chcete zjistit IP adresy veřejného partnerského okruhu ExpressRoute, [otevřete lístek podpory pro ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) na webu Azure Portal. Další informace o [překladu adres (NAT) pro veřejné partnerské vztahy a partnerské vztahy s Microsoftem v ExpressRoute.](../../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>Správa pravidel sítě IP

Pravidla sítě IP pro účty úložiště můžete spravovat pomocí Azure Portal, PowerShellu nebo CLIv2.

#### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Přejděte do účtu úložiště, který chcete zabezpečit.

2. Vyberte položku v nabídce nastavení s názvem **síť**.

3. Ověřte, že jste vybrali povolení přístupu z **vybraných sítí**.

4. Pokud chcete udělit přístup k rozsahu IP adres Internetu, zadejte v části   >  **Rozsah adres** firewallu IP adresu nebo rozsah adres (ve formátu CIDR).

5. Chcete-li odebrat pravidlo sítě protokolu IP, vyberte ikonu odpadkového koše vedle rozsahu adres.

6. Vyberte **Uložit**, aby se tyto změny použily.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Nainstalujte [Azure PowerShell](/powershell/azure/install-Az-ps) a [přihlaste](/powershell/azure/authenticate-azureps)se.

2. Vypíše pravidla sítě protokolu IP.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

3. Přidejte síťové pravidlo pro jednotlivé IP adresy.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

4. Přidejte síťové pravidlo pro rozsah IP adres.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

5. Odeberte pravidlo sítě pro jednotlivé IP adresy.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

6. Odebere síťové pravidlo pro rozsah IP adres.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Ujistěte se, že jste [výchozí pravidlo nastavili](#change-the-default-network-access-rule) na **Odepřít**, nebo že Síťová pravidla nemají žádný vliv.

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Nainstalujte rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) a [přihlaste](/cli/azure/authenticate-azure-cli)se.

1. Vypíše pravidla sítě protokolu IP.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

2. Přidejte síťové pravidlo pro jednotlivé IP adresy.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

3. Přidejte síťové pravidlo pro rozsah IP adres.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

4. Odeberte pravidlo sítě pro jednotlivé IP adresy.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

5. Odebere síťové pravidlo pro rozsah IP adres.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Ujistěte se, že jste [výchozí pravidlo nastavili](#change-the-default-network-access-rule) na **Odepřít**, nebo že Síťová pravidla nemají žádný vliv.

---

<a id="grant-access-specific-instances"></a>

## <a name="grant-access-from-azure-resource-instances-preview"></a>Udělení přístupu z Azure Resource Instances (Preview)

V některých případech může být aplikace závislá na prostředcích Azure, které se nedají izolovat přes virtuální síť nebo pravidlo IP adresy. Pořád ale chcete zabezpečit a omezit přístup k účtu úložiště jenom na prostředky Azure vaší aplikace. Účty úložiště můžete nakonfigurovat tak, aby povolovaly přístup ke konkrétním instancím prostředků některých služeb Azure tím, že se vytvoří pravidlo instance prostředku. 

Typy operací, které může instance prostředků provádět u dat účtu úložiště, se určují pomocí [přiřazení rolí Azure](storage-auth-aad.md#assign-azure-roles-for-access-rights) instance prostředku. Instance prostředků musí být ze stejného tenanta jako váš účet úložiště, ale můžou patřit do libovolného předplatného v tenantovi.

Seznam podporovaných služeb Azure se zobrazí v části [důvěryhodný přístup na základě spravované identity přiřazené systémem](#trusted-access-system-assigned-managed-identity) v tomto článku.

> [!NOTE]
> Tato funkce je ve verzi Public Preview a je dostupná ve všech oblastech veřejného cloudu. 

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Můžete přidat nebo odebrat pravidla sítě prostředků v Azure Portal.

1. Začněte tím, že se přihlásíte k [Azure Portal](https://portal.azure.com/) .

2. Vyhledejte svůj účet úložiště a zobrazte přehled účtu.

3. Vyberte **sítě** , aby se zobrazila stránka konfigurace pro síťové služby.

4. V rozevíracím seznamu **typ prostředku** vyberte typ prostředku vaší instance prostředku. 

5. V rozevíracím seznamu **název instance** vyberte instanci prostředku. Můžete se také rozhodnout zahrnout všechny instance prostředků do aktivního tenanta, předplatného nebo skupiny prostředků.

6. Vyberte **Uložit**, aby se tyto změny použily. Instance prostředků se zobrazí v části **instance prostředků** na stránce nastavení sítě. 

Chcete-li odebrat instanci prostředku, vyberte ikonu odstranění ( :::image type="icon" source="media/storage-network-security/delete-icon.png"::: ) vedle instance prostředku.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

K přidání nebo odebrání pravidel sítě prostředků můžete použít příkazy prostředí PowerShell.

> [!IMPORTANT]
> Ujistěte se, že jste [výchozí pravidlo nastavili](#change-the-default-network-access-rule) na **Odepřít**, nebo že Síťová pravidla nemají žádný vliv.

#### <a name="install-the-preview-module"></a>Instalace modulu Preview

Nainstalujte nejnovější verzi modulu PowershellGet. Pak zavřete a znovu otevřete konzolu PowerShellu.

```powershell
install-Module PowerShellGet –Repository PSGallery –Force  
```

Nainstalujte modul **AZ. Storage** Preview.

```powershell
Install-Module Az.Storage -Repository PsGallery -RequiredVersion 3.0.1-preview -AllowClobber -AllowPrerelease -Force 
```

Další informace o tom, jak nainstalovat moduly PowerShellu, najdete v tématu [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) .

#### <a name="grant-access"></a>Udělení přístupu

Přidejte síťové pravidlo, které uděluje přístup z instance prostředku.

```powershell
$resourceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Add-AzStorageAccountNetworkRule -ResourceGroupName $resourceGroupName -Name $accountName -TenantId $tenantId -ResourceId $resourceId

```

Určete více instancí prostředků najednou úpravou sady pravidel sítě.

```powershell
$resourceId1 = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$resourceId2 = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/mySQLServer"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Update-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName -ResourceAccessRule (@{ResourceId=$resourceId1;TenantId=$tenantId},@{ResourceId=$resourceId2;TenantId=$tenantId}) 
```

#### <a name="remove-access"></a>Odebrání přístupu

Odebere síťové pravidlo, které uděluje přístup z instance prostředku.

```powershell
$resourceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory"
$tenantId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Remove-AzStorageAccountNetworkRule -ResourceGroupName $resourceGroupName -Name $accountName -TenantId $tenantId -ResourceId $resourceId  
```

Odeberte všechna síťová pravidla, která udělují přístup z instancí prostředků.

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

Update-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName -ResourceAccessRule @()  
```

#### <a name="view-a-list-of-allowed-resource-instances"></a>Zobrazit seznam povolených instancí prostředků

Zobrazte úplný seznam instancí prostředků, kterým byl udělen přístup k účtu úložiště.

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mystorageaccount"

$rule = Get-AzStorageAccountNetworkRuleSet -ResourceGroupName $resourceGroupName -Name $accountName
$rule.ResourceAccessRules 
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pomocí příkazů rozhraní příkazového řádku Azure můžete přidat nebo odebrat pravidla sítě prostředků.

#### <a name="install-the-preview-extension"></a>Instalace rozšíření verze Preview

1. Otevřete [Azure Cloud Shell](../../cloud-shell/overview.md)nebo pokud máte rozhraní příkazového řádku Azure místně [nainstalované](/cli/azure/install-azure-cli) , otevřete konzolovou aplikaci, například Windows PowerShell.

2. Pak ověřte, že verze rozhraní příkazového řádku Azure, kterou jste nainstalovali, je `2.13.0` nebo vyšší, a to pomocí následujícího příkazu.

   ```azurecli
   az --version
   ```

   Pokud je vaše verze rozhraní příkazového řádku Azure nižší než `2.13.0` , nainstalujte novější verzi. Viz [instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

3. Zadejte následující příkaz pro instalaci rozšíření Preview.

   ```azurecli
   az extension add -n storage-preview
   ```

#### <a name="grant-access"></a>Udělení přístupu

Přidejte síťové pravidlo, které uděluje přístup z instance prostředku.

```azurecli
az storage account network-rule add \
    --resource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Synapse/workspaces/testworkspace \
    --tenant-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    -g myResourceGroup \
    --account-name mystorageaccount
```

#### <a name="remove-access"></a>Odebrání přístupu

Odebere síťové pravidlo, které uděluje přístup z instance prostředku.

```azurecli
az storage account network-rule remove \
    --resource-id /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Synapse/workspaces/testworkspace \
    --tenant-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    -g myResourceGroup \
    --account-name mystorageaccount
```

#### <a name="view-a-list-of-allowed-resource-instances"></a>Zobrazit seznam povolených instancí prostředků

Zobrazte úplný seznam instancí prostředků, kterým byl udělen přístup k účtu úložiště.

```azurecli
az storage account network-rule list \
    -g myResourceGroup \
    --account-name mystorageaccount
```

---

<a id="exceptions"></a>
<a id="trusted-microsoft-services"></a>

## <a name="grant-access-to-azure-services"></a>Udělení přístupu ke službám Azure 

Některé služby Azure fungují ze sítí, které není možné zahrnout do svých síťových pravidel. K účtu úložiště můžete udělit podmnožinu takových důvěryhodných služeb Azure a přitom zachovat Síťová pravidla pro ostatní aplikace. Tyto důvěryhodné služby pak budou používat silné ověřování pro zabezpečené připojení k vašemu účtu úložiště. 

Vytvořením výjimky pro síťové pravidlo můžete udělit přístup k důvěryhodným službám Azure. Podrobné pokyny najdete v části [Správa výjimek](#manage-exceptions) v tomto článku. 

Když udělíte přístup k důvěryhodným službám Azure, udělíte jim následující typy přístupu:

- Důvěryhodný přístup pro vybrané operace k prostředkům, které jsou zaregistrované ve vašem předplatném
- Důvěryhodný přístup k prostředkům na základě spravované identity přiřazené systémem.

<a id="trusted-access-resources-in-subscription"></a>

### <a name="trusted-access-for-resources-registered-in-your-subscription"></a>Důvěryhodný přístup k prostředkům zaregistrovaným ve vašem předplatném

Prostředky některých služeb, **Pokud jsou zaregistrované ve vašem předplatném**, mají přístup k vašemu účtu úložiště **ve stejném předplatném** pro vybrané operace, jako je například zápis protokolů nebo zálohování.  V následující tabulce jsou popsány jednotlivé služby a operace, které jsou povoleny. 

| Služba                  | Název poskytovatele prostředků     | Povolené operace                 |
|:------------------------ |:-------------------------- |:---------------------------------- |
| Azure Backup             | Microsoft. RecoveryServices | Spusťte zálohování a obnovujte nespravované disky ve virtuálních počítačích IAAS. (není vyžadováno pro Managed Disks). [Přečtěte si další informace](../../backup/backup-overview.md). |
| Azure Data Box           | Microsoft. DataBox          | Umožňuje importovat data do Azure pomocí Data Box. [Přečtěte si další informace](../../databox/data-box-overview.md). |
| Azure DevTest Labs       | Microsoft. DevTestLab       | Vytvoření vlastní image a instalace artefaktů. [Přečtěte si další informace](../../devtest-labs/devtest-lab-overview.md). |
| Azure Event Grid         | Microsoft. EventGrid        | Povolte publikování událostí Blob Storage a umožněte Event Grid publikování do front úložiště. Přečtěte si informace o [událostech služby Blob Storage](../../event-grid/overview.md#event-sources) a [publikování do front](../../event-grid/event-handlers.md). |
| Azure Event Hubs         | Microsoft. EventHub         | Archivujte data pomocí Event Hubsho zachycení. [Další informace](../../event-hubs/event-hubs-capture-overview.md) |
| Synchronizace souborů Azure          | Microsoft. StorageSync      | Umožňuje transformovat souborový server Prem na mezipaměť pro sdílené složky Azure. Povoluje se synchronizace více webů, rychlé zotavení po havárii a zálohování na straně cloudu. [Další informace](../files/storage-sync-files-planning.md) |
| Azure HDInsight          | Microsoft. HDInsight        | Zřídí počáteční obsah výchozího systému souborů pro nový cluster HDInsight. [Přečtěte si další informace](../../hdinsight/hdinsight-hadoop-use-blob-storage.md). |
| Export pro import do Azure      | Microsoft. ImportExport     | Umožňuje importovat data Azure Storage nebo exportovat data z Azure Storage pomocí služby Azure Storage import/export. [Přečtěte si další informace](../../import-export/storage-import-export-service.md).  |
| Azure Monitor            | Microsoft. Insights         | Povoluje zápis dat monitorování do zabezpečeného účtu úložiště, včetně protokolů prostředků, Azure Active Directory protokolů pro přihlášení a auditu a protokolů Microsoft Intune. [Přečtěte si další informace](../../azure-monitor/platform/roles-permissions-security.md). |
| Sítě Azure         | Microsoft.Network          | Ukládejte a analyzujte protokoly síťového provozu, včetně služeb Network Watcher a Analýza provozu. [Přečtěte si další informace](../../network-watcher/network-watcher-nsg-flow-logging-overview.md). |
| Azure Site Recovery      | Microsoft. SiteRecovery     | Povolení replikace pro zotavení po havárii virtuálních počítačů Azure s IaaS při použití mezipaměti, zdrojového nebo cílového účtu úložiště podporujícího bránu firewall  [Přečtěte si další informace](../../site-recovery/azure-to-azure-tutorial-enable-replication.md). |

<a id="trusted-access-system-assigned-managed-identity"></a>

### <a name="trusted-access-based-on-system-assigned-managed-identity"></a>Důvěryhodný přístup na základě spravované identity přiřazené systémem

V následující tabulce jsou uvedeny služby, které mohou mít přístup k datům účtu úložiště, pokud se instance těchto služeb dostanou příslušným oprávněním. Pokud chcete udělit oprávnění, musíte explicitně [přiřadit roli Azure](storage-auth-aad.md#assign-azure-roles-for-access-rights) pro [spravovanou identitu přiřazenou systémem](../../active-directory/managed-identities-azure-resources/overview.md) pro každou instanci prostředku. V takovém případě rozsah přístupu pro instanci odpovídá roli Azure přiřazené spravované identitě. 

> [!TIP]
> Doporučeným způsobem, jak udělit přístup ke konkrétním prostředkům, je použít pravidla instance prostředků. Pokud chcete udělit přístup ke konkrétním instancím prostředků, přečtěte si část [udělení přístupu z Azure Resource Instances (Preview)](#grant-access-specific-instances) v tomto článku.


| Služba                        | Název poskytovatele prostředků                 | Účel            |
| :----------------------------- | :------------------------------------- | :----------------- |
| Azure API Management           | Microsoft.ApiManagement/service        | Umožňuje službě API Management přístup k účtům úložiště za bránou firewall pomocí zásad. [Přečtěte si další informace](../../api-management/api-management-authentication-policies.md#use-managed-identity-in-send-request-policy). |
| Azure Cognitive Search         | Microsoft. Search/searchServices        | Umožňuje službám Kognitivní hledání získat přístup k účtům úložiště pro indexování, zpracování a dotazování. |
| Azure Cognitive Services       | Microsoft. CognitiveService             | Povolí Cognitive Services k přístupu k účtům úložiště. |
| Úlohy Azure Container Registry | Microsoft. ContainerRegistry/Registry | ACR úlohy mají přístup k účtům úložiště při vytváření imagí kontejneru. |
| Azure Data Factory             | Microsoft. DataFactory/továrny        | Umožňuje přístup k účtům úložiště pomocí modulu runtime ADF. |
| Azure Data Share               | Microsoft. datashare/Accounts           | Umožňuje přístup k účtům úložiště prostřednictvím sdílení dat. |
| Azure IoT Hub                  | Microsoft. Devices/IotHubs              | Umožňuje zapsat data z služby IoT Hub do úložiště objektů BLOB. [Další informace](../../iot-hub/virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing) |
| Azure Logic Apps               | Microsoft. Logic/Workflows              | Povoluje Logic Apps přístup k účtům úložiště. [Přečtěte si další informace](../../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity). |
| Služba Azure Machine Learning | Microsoft.MachineLearningServices      | Autorizované pracovní prostory Azure Machine Learning zapisují výstup, modely a protokoly do úložiště objektů BLOB a čtou data z experimentu. [Přečtěte si další informace](../../machine-learning/how-to-network-security-overview.md#secure-the-workspace-and-associated-resources). | 
| Azure Synapse Analytics       | Microsoft.Sql                          | Umožňuje importovat a exportovat data z konkrétních databází SQL pomocí příkazu COPY nebo základny. [Přečtěte si další informace](../../azure-sql/database/vnet-service-endpoint-rule-overview.md). |
| Azure SQL Database       | Microsoft.Sql                          | Umožňuje [importovat](/sql/t-sql/statements/bulk-insert-transact-sql#f-importing-data-from-a-file-in-azure-blob-storage) data z účtů úložiště a [zapisovat](../../azure-sql/database/audit-write-storage-account-behind-vnet-firewall.md) data auditu do účtů úložiště za bránou firewall. |
| Azure Stream Analytics         | Microsoft. StreamAnalytics             | Umožňuje zapsat data z úlohy streamování do úložiště objektů BLOB. [Přečtěte si další informace](../../stream-analytics/blob-output-managed-identity.md). |
| Azure Synapse Analytics        | Microsoft. synapse/pracovní prostory          | Umožňuje přístup k datům v Azure Storage z Azure synapse Analytics. |

## <a name="grant-access-to-storage-analytics"></a>Udělení přístupu k analýze úložiště

V některých případech je přístup ke čtení protokolů prostředku a metriky vyžadován mimo hranici sítě. Při konfiguraci přístupu důvěryhodných služeb k účtu úložiště můžete povolení přístupu pro čtení pro soubory protokolů, tabulky metrik nebo obojí vytvořit tak, že vytvoříte výjimku síťového pravidla. Podrobné pokyny najdete níže v části **Manage Exceptions** . Další informace o práci s analýzou úložiště najdete v tématu [použití analýzy Azure Storage ke shromažďování dat o protokolech a metrikách](./storage-analytics.md). 

<a id="manage-exceptions"></a>

## <a name="manage-exceptions"></a>Správa výjimek

Výjimky síťového pravidla můžete spravovat pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure CLI v2.

#### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Přejděte do účtu úložiště, který chcete zabezpečit.

2. Vyberte položku v nabídce nastavení s názvem **síť**.

3. Ověřte, že jste vybrali povolení přístupu z **vybraných sítí**.

4. V části **výjimky** vyberte výjimky, které chcete udělit.

5. Vyberte **Uložit**, aby se tyto změny použily.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Nainstalujte [Azure PowerShell](/powershell/azure/install-Az-ps) a [přihlaste](/powershell/azure/authenticate-azureps)se.

2. Zobrazí výjimky pro síťová pravidla účtu úložiště.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

3. Nakonfigurujte výjimky na Síťová pravidla účtu úložiště.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

4. Odeberte výjimky pro síťová pravidla účtu úložiště.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> Nezapomeňte [nastavit výchozí pravidlo](#change-the-default-network-access-rule) na **Odepřít**, nebo odebrání výjimek nebude mít žádný vliv.

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Nainstalujte rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) a [přihlaste](/cli/azure/authenticate-azure-cli)se.

2. Zobrazí výjimky pro síťová pravidla účtu úložiště.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

3. Nakonfigurujte výjimky na Síťová pravidla účtu úložiště.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

4. Odeberte výjimky pro síťová pravidla účtu úložiště.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> Nezapomeňte [nastavit výchozí pravidlo](#change-the-default-network-access-rule) na **Odepřít**, nebo odebrání výjimek nebude mít žádný vliv.

---

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o koncových bodech síťových služeb Azure v [koncových bodech služby](../../virtual-network/virtual-network-service-endpoints-overview.md).

Dig hlouběji do zabezpečení Azure Storage v [Azure Storage příručce zabezpečení](../blobs/security-recommendations.md).
