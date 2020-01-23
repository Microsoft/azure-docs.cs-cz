---
title: Konfigurace virtuálních sítí a bran firewall Azure Storage | Dokumentace Microsoftu
description: Konfigurace zabezpečení vrstvami sítě pro váš účet úložiště.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: tamram
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 2c3b329aa767fbe9795c90ca236008210576fe12
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514727"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Konfigurace virtuálních sítí a bran firewall Azure Storage

Azure Storage poskytuje vrstvený model zabezpečení. Tento model vám umožní zabezpečit a řídit úroveň přístupu k účtům úložiště, které vaše aplikace a podniková prostředí vyžadují, na základě typu a podmnožiny používaných sítí. Při konfiguraci síťových pravidel mají přístup k účtu úložiště jenom aplikace požadující data přes zadanou sadu sítí. Přístup k účtu úložiště můžete omezit na požadavky pocházející ze zadaných IP adres, rozsahů IP adres nebo ze seznamu podsítí v Azure Virtual Network (virtuální síť).

Účty úložiště mají veřejný koncový bod, který je přístupný prostřednictvím Internetu. [Pro svůj účet úložiště můžete také vytvořit privátní koncové body](storage-private-endpoints.md), které přiřadí privátní IP adresu z vaší virtuální sítě k účtu úložiště a zabezpečuje veškerý provoz mezi vaší virtuální sítí a účtem úložiště prostřednictvím privátního propojení. Brána firewall služby Azure Storage poskytuje přístup k řízení přístupu pro veřejný koncový bod vašeho účtu úložiště. Bránu firewall můžete použít také k blokování veškerého přístupu prostřednictvím veřejného koncového bodu při používání privátních koncových bodů. Vaše konfigurace brány firewall pro úložiště také umožňuje, aby bylo možné bezpečně přistupovat k účtu úložiště pomocí výběru důvěryhodných služeb platformy Azure.

Aplikace, která přistupuje k účtu úložiště v případě, že jsou platná síťová pravidla, vyžaduje pro požadavek správnou autorizaci. Autorizace se podporuje s přihlašovacími údaji služby Azure Active Directory (Azure AD) pro objekty BLOB a front, s platným klíčem pro přístup k účtu nebo s tokenem SAS.

> [!IMPORTANT]
> Zapnutím pravidel brány firewall pro váš účet úložiště se ve výchozím nastavení zablokuje příchozí požadavky na data, pokud žádosti pocházejí ze služby v rámci Azure Virtual Network (VNet) nebo z povolených veřejných IP adres. Požadavky, které jsou blokovány mezi patří zařízení se od ostatních služeb Azure z webu Azure portal, protokolování a metrik služby a tak dále.
>
> Přístup ke službám Azure, které provozují v rámci virtuální sítě, můžete udělit povolením provozu z podsítě hostující instanci služby. Pomocí mechanismu [výjimek](#exceptions) popsaných níže můžete také povolit omezený počet scénářů. Pokud chcete získat přístup k datům z účtu úložiště prostřednictvím Azure Portal, musíte být na počítači v rámci důvěryhodné hranice (buď IP nebo VNet), kterou jste nastavili.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Scénáře

Chcete-li zabezpečit svůj účet úložiště, je třeba nejprve nakonfigurovat pravidlo pro odepření přístupu k provozu ze všech sítí (včetně internetového provozu) ve výchozím nastavení ve veřejném koncovém bodu. Pak byste měli nakonfigurovat pravidla, která udělí přístup k provozu z konkrétních virtuální sítě. Můžete taky nakonfigurovat pravidla pro udělení přístupu k provozu z výběru rozsahů veřejných IP adres, které umožňují připojení z určitých internetových nebo místních klientů. Tato konfigurace umožňuje vytvořit zabezpečené ohraničení sítě pro vaše aplikace.

Můžete zkombinovat pravidla brány firewall, která umožňují přístup z konkrétních virtuálních sítí a z rozsahů veřejných IP adres ve stejném účtu úložiště. Pravidla brány firewall pro úložiště se dají použít u existujících účtů úložiště nebo při vytváření nových účtů úložiště.

Pravidla brány firewall pro úložiště se vztahují na veřejný koncový bod účtu úložiště. Nepotřebujete žádná pravidla přístupu k bránám firewall, aby bylo možné povolit přenosy privátních koncových bodů účtu úložiště. Proces schvalování privátního koncového bodu uděluje implicitní přístup k provozu z podsítě, která je hostitelem privátního koncového bodu.

Na všech síťových protokolů do úložiště Azure, včetně REST a protokolu SMB se vynucují pravidla sítě. Chcete-li získat přístup k datům pomocí nástrojů, jako jsou Azure Portal, Průzkumník služby Storage a AZCopy, musí být nakonfigurována explicitní Síťová pravidla.

Po použití pravidla sítě jsou, se vynutí pro všechny požadavky. Tokeny SAS, které udělují přístup na konkrétní IP adresu slouží k omezení přístupu k tokenu držitele, ale není udělovat nový přístup mimo síť nakonfigurovaná pravidla.

Diskové přenosy virtuálního počítače (včetně připojení a odpojení operace a vstupně-výstupních operací disku) nemá vliv pravidla sítě. Přístup REST k objektům BLOB stránky je chráněn pravidel sítě.

Klasických účtů úložiště nepodporují virtuální sítí a bran firewall.

Nespravované disky v účtech úložiště můžete použít s pravidly sítě použít pro zálohování a obnovení virtuálních počítačů tím, že vytvoříte výjimku. Tento proces je popsána v [výjimky](#exceptions) části tohoto článku. Výjimky brány firewall nejsou použitelné se spravovanými disky se už spravuje Azure.

## <a name="change-the-default-network-access-rule"></a>Změnit výchozí pravidlo přístupu sítě

Účty úložiště ve výchozím nastavení, přijímat připojení z klientů v síti. Chcete-li omezit přístup k vybrané sítě, musíte nejdřív změnit výchozí akci.

> [!WARNING]
> Provádění změn pravidel sítě může mít vliv na vaše aplikace možnost připojení k Azure Storage. Nastavením výchozího síťového pravidla na **Odepřít** znemožníte přístup k datům, pokud nejsou použita specifická Síťová pravidla, která **udělují** přístup. Je potřeba udělit přístup k žádné povolené sítě pomocí pravidel pro sítě, než změníte výchozí pravidlo pro odepření přístupu.

### <a name="managing-default-network-access-rules"></a>Správa výchozího pravidla přístupu k síti

Můžete spravovat výchozí pravidla přístupu k síti pro účty úložiště na webu Azure portal, Powershellu nebo CLIv2.

#### <a name="azure-portal"></a>Portál Azure

1. Přejděte na účet úložiště, kterou chcete zabezpečit.

1. Klikněte v nabídce nastavení nazývá **virtuální sítí a bran firewall**.

1. Ve výchozím nastavení odepření přístupu, zvolit povolení přístupu z **vybrané sítě**. Povolit přenos ze všech sítí, zvolit povolení přístupu z **všechny sítě**.

1. Klikněte na tlačítko **Uložit** změny.

#### <a name="powershell"></a>PowerShell

1. Nainstalujte [prostředí Azure PowerShell](/powershell/azure/install-Az-ps) a [přihlášení](/powershell/azure/authenticate-azureps).

1. Zobrazí stav výchozí pravidlo pro účet úložiště.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

1. Nastavte výchozí pravidlo pro odepření přístupu k síti ve výchozím nastavení.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

1. Nastavte výchozí pravidlo, kterým povolíte přístup k síti ve výchozím nastavení.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="cliv2"></a>CLIv2

1. Nainstalujte [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli) a [přihlášení](/cli/azure/authenticate-azure-cli).

1. Zobrazí stav výchozí pravidlo pro účet úložiště.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

1. Nastavte výchozí pravidlo pro odepření přístupu k síti ve výchozím nastavení.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

1. Nastavte výchozí pravidlo, kterým povolíte přístup k síti ve výchozím nastavení.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```

## <a name="grant-access-from-a-virtual-network"></a>Udělení přístupu z virtuální sítě

Účty úložiště můžete nakonfigurovat tak, aby povolovaly přístup jenom z konkrétních podsítí. Povolené podsítě můžou patřit do virtuální sítě ve stejném předplatném nebo v jiném předplatném, včetně předplatných, která patří do jiného tenanta Azure Active Directory.

Povolit [koncový bod služby](/azure/virtual-network/virtual-network-service-endpoints-overview) pro službu Azure Storage v rámci virtuální sítě. Koncový bod služby směruje provoz z virtuální sítě prostřednictvím optimální cesty ke službě Azure Storage. Identity podsítě a virtuální sítě se také odesílají s každým požadavkem. Správci potom můžou nakonfigurovat Síťová pravidla pro účet úložiště, který povoluje příjem požadavků z konkrétních podsítí ve virtuální síti. Klienti udělen přístup přes tyto pravidel sítě musí i dál splňuje požadavky na povolení účtu úložiště pro přístup k datům.

Každý účet úložiště podporuje až 100 pravidel virtuální sítě, které mohou být spojeny s [pravidel sítě IP](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Oblasti k dispozici virtuální sítě

Obecně platí koncové body služby práci mezi virtuálními sítěmi a instance služby ve stejné oblasti Azure. Při používání koncových bodů služby se službou Azure Storage, tento obor roste zahrnout [spárované oblasti](/azure/best-practices-availability-paired-regions). Koncové body služby povolit kontinuity podnikových procesů během regionální převzetí služeb při selhání a přístup k instancím jen pro čtení (RA-GRS) geograficky redundantní úložiště. Pravidla sítě, které udělují přístup z virtuální sítě do účtu úložiště také udělit přístup na jakoukoli instanci RA-GRS.

Při plánování zotavení po havárii během oblastního výpadku, měli byste vytvořit virtuální sítě předem v párované oblasti. Povolení koncových bodů služby pro službu Azure Storage, pomocí pravidel sítě udělení přístupu z těchto alternativních virtuálních sítí. Pak použijte tato pravidla pro účty geograficky redundantní úložiště.

> [!NOTE]
> Koncové body služby se nevztahují na přenosy mimo oblast virtuální sítě a pár určené oblasti. Lze použít pouze pravidel sítě udělení přístupu z virtuální sítě do účtů úložiště v primární oblasti účtu úložiště nebo v určené spárované oblasti.

### <a name="required-permissions"></a>Požadovaná oprávnění

Použít pravidlo virtuální sítě pro účet úložiště, musí mít uživatel příslušná oprávnění pro přidávané podsítě. Je nutná oprávnění *připojit ke službě Service k podsíti* a je součástí *Přispěvatel účtů úložiště* předdefinovaná role. Mohou být přidány také do vlastní definice rolí.

Účet úložiště a udělený přístup k virtuálním sítím můžou být v různých předplatných, včetně předplatných, která jsou součástí jiného tenanta Azure AD.

> [!NOTE]
> Konfigurace pravidel, která udělují přístup k podsítím ve virtuálních sítích, které jsou součástí jiného tenanta Azure Active Directory, se v současné době podporují jenom prostřednictvím PowerShellu, CLI a rozhraní REST API. Taková pravidla nelze konfigurovat prostřednictvím Azure Portal, i když je lze zobrazit na portálu.

### <a name="managing-virtual-network-rules"></a>Správa pravidel virtuální sítě

Můžete spravovat pravidla virtuální sítě pro účty úložiště pomocí webu Azure portal, Powershellu nebo CLIv2.

#### <a name="azure-portal"></a>Portál Azure

1. Přejděte na účet úložiště, kterou chcete zabezpečit.

1. Klikněte v nabídce nastavení nazývá **virtuální sítí a bran firewall**.

1. Zkontrolujte, jestli jste vybrali k povolení přístupu z **vybrané sítě**.

1. Udělit přístup k virtuální síti s nové pravidlo sítě v části **virtuální sítě**, klikněte na tlačítko **přidat existující virtuální síť**vyberte **virtuální sítě** a **Podsítě** možnosti a pak klikněte na tlačítko **přidat**. Pokud chcete vytvořit novou virtuální síť a jí udělit přístup, klikněte na tlačítko **přidat novou virtuální síť**. Zadejte informace potřebné k vytvoření nové virtuální sítě a potom klikněte na **vytvořit**.

    > [!NOTE]
    > Pokud koncový bod služby pro službu Azure Storage se dříve nakonfigurované pro vybranou virtuální síť a podsítě, musíte ho nakonfigurovat jako součást této operace.
    >
    > V současné době se při vytváření pravidel zobrazují jenom virtuální sítě patřící do stejného Azure Active Directory tenanta. Pokud chcete udělit přístup k podsíti ve virtuální síti patřící jinému tenantovi, použijte PowerShell, rozhraní příkazového řádku nebo rozhraní REST API.

1. Pokud chcete odstranit virtuální síť nebo podsítě pravidlo, klikněte na tlačítko **...**  otevřete kontextovou nabídku pro virtuální síť nebo podsíť, a klikněte na **odebrat**.

1. Klikněte na tlačítko **Uložit** změny.

#### <a name="powershell"></a>PowerShell

1. Nainstalujte [prostředí Azure PowerShell](/powershell/azure/install-Az-ps) a [přihlášení](/powershell/azure/authenticate-azureps).

1. Seznam pravidel virtuální sítě.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

1. Povolte koncový bod služby pro službu Azure Storage na existující virtuální síť a podsíť.

    ```powershell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

1. Přidáte pravidlo pro sítě pro virtuální síť a podsíť.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

    > [!TIP]
    > Pokud chcete přidat síťové pravidlo pro podsíť ve virtuální síti, která patří do jiného tenanta Azure AD, použijte plně kvalifikovaný parametr **VirtualNetworkResourceId** ve formátu "/Subscriptions/Subscription-ID/resourceGroups/resourceGroup-Name/Providers/Microsoft.Network/virtualNetworks/vNet-Name/subnets/Subnet-Name".

1. Odeberte pravidlo pro sítě pro virtuální síť a podsíť.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> Nezapomeňte [nastavte výchozí pravidlo](#change-the-default-network-access-rule) k **Odepřít**, nebo pravidel sítě nemají žádný vliv.

#### <a name="cliv2"></a>CLIv2

1. Nainstalujte [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli) a [přihlášení](/cli/azure/authenticate-azure-cli).

1. Seznam pravidel virtuální sítě.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

1. Povolte koncový bod služby pro službu Azure Storage na existující virtuální síť a podsíť.

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

1. Přidáte pravidlo pro sítě pro virtuální síť a podsíť.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

    > [!TIP]
    > Pokud chcete přidat pravidlo pro podsíť ve virtuální síti, která patří do jiného tenanta Azure AD, použijte plně kvalifikované ID podsítě ve formátu "/Subscriptions/\<Subscription-ID\>/resourceGroups/\<\>/providers/Microsoft.Network/virtualNetworks/\<vNet-Name\>/subnets/\<podsíť-název\>".
    >
    > Pomocí parametru **Subscription** můžete načíst ID podsítě pro virtuální síť patřící jinému Tenantovi služby Azure AD.

1. Odeberte pravidlo pro sítě pro virtuální síť a podsíť.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> Nezapomeňte [nastavte výchozí pravidlo](#change-the-default-network-access-rule) k **Odepřít**, nebo pravidel sítě nemají žádný vliv.

## <a name="grant-access-from-an-internet-ip-range"></a>Udělení přístupu z Internetu rozsah IP adres

Můžete nakonfigurovat účty úložiště pro povolení přístupu z veřejného Internetu konkrétní rozsahy IP adres. Tato konfigurace uděluje přístup ke konkrétní internetové služby a s místními sítěmi a blokuje obecné přenosy z Internetu.

Zadejte rozsahy adres pomocí povolených internet [notaci CIDR](https://tools.ietf.org/html/rfc4632) ve formě *16.17.18.0/24* nebo jako jednotlivé IP adresy jako *16.17.18.19*.

   > [!NOTE]
   > Rozsahy malé adres pomocí "/ 31" nebo "/ 32" předpony velikosti se nepodporují. Tyto rozsahy musí být nakonfigurovaný pomocí jednotlivých pravidel IP adres.

Pravidla síťových IP jsou povolené jenom pro **veřejného Internetu** IP adresy. IP adresy rozsahů, které jsou vyhrazené pro privátní sítě (jak je definováno v [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)) nejsou povoleny v pravidlech IP. Privátní sítě zahrnují adresy začínající hodnotami _maska 10.*_ , _172.16. *_  - _172.31. *_ , a _192.168. *_ .

   > [!NOTE]
   > Pravidla protokolu IP sítě nemají žádný vliv na požadavků pocházejících ze stejné oblasti Azure jako účet úložiště. Použití [pravidla virtuální sítě](#grant-access-from-a-virtual-network) požadavky stejné oblasti.

  > [!NOTE]
  > Služby nasazené ve stejné oblasti jako účet úložiště používají privátní IP adresy Azure ke komunikaci. Proto nemůžete omezit přístup ke konkrétním službám Azure na základě jejich veřejného rozsahu příchozích IP adres.

Pro konfiguraci pravidel brány firewall úložiště se podporují jenom IPV4 adresy.

Každý účet úložiště podporuje až 100 pravidel sítě IP.

### <a name="configuring-access-from-on-premises-networks"></a>Konfigurace přístupu z místní sítě

Pokud chcete udělit přístup z vaší místní sítě k vašemu účtu úložiště s pravidlem síť IP, je nutné určit internetové IP adresy používané ve vaší síti. Požádejte o pomoc svého správce sítě.

Pokud používáte [ExpressRoute](/azure/expressroute/expressroute-introduction) z vašich místních partnerských vztahů nebo partnerských vztahů Microsoftu, budete muset určit IP adresy NAT, které se používají. Ve veřejných partnerských vztazích každý okruh ExpressRoute automaticky využívá dvě IP adresy pro překlad adres (NAT), které se používají k provozu služeb Azure při vstupu do páteřní sítě Microsoft Azure. V případě partnerského vztahu Microsoftu se používají IP adresy NAT buď poskytované zákazníkem, nebo poskytovatelem služeb. Pokud chcete povolit přístup k prostředkům služby, musíte tyto veřejné IP adresy povolit v nastavení IP adresy brány firewall prostředku. Pokud chcete zjistit IP adresy veřejného partnerského okruhu ExpressRoute, [otevřete lístek podpory pro ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) na webu Azure Portal. Další informace o [překladu adres (NAT) pro veřejné partnerské vztahy a partnerské vztahy s Microsoftem v ExpressRoute.](/azure/expressroute/expressroute-nat#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>Správa pravidel sítě IP

Můžete spravovat pravidla sítě protokolu IP pro účty úložiště pomocí webu Azure portal, Powershellu nebo CLIv2.

#### <a name="azure-portal"></a>Portál Azure

1. Přejděte na účet úložiště, kterou chcete zabezpečit.

1. Klikněte v nabídce nastavení nazývá **virtuální sítí a bran firewall**.

1. Zkontrolujte, jestli jste vybrali k povolení přístupu z **vybrané sítě**.

1. Udělit přístup k Internetu rozsah IP adres, zadejte IP adresu nebo rozsah adres (ve formátu CIDR) v části **brány Firewall** > **rozsah adres**.

1. Pokud chcete odebrat pravidlo protokolu IP sítě, klikněte na ikonu koše vedle rozsah adres.

1. Klikněte na tlačítko **Uložit** změny.

#### <a name="powershell"></a>PowerShell

1. Nainstalujte [prostředí Azure PowerShell](/powershell/azure/install-Az-ps) a [přihlášení](/powershell/azure/authenticate-azureps).

1. Seznam pravidel sítě protokolu IP.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

1. Přidáte pravidlo pro sítě pro jednotlivé IP adresy.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Přidáte pravidlo pro rozsah IP adres pro sítě.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

1. Odeberte pravidlo pro sítě pro jednotlivé IP adresy.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Odeberte pravidlo pro rozsah IP adres pro sítě.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Nezapomeňte [nastavte výchozí pravidlo](#change-the-default-network-access-rule) k **Odepřít**, nebo pravidel sítě nemají žádný vliv.

#### <a name="cliv2"></a>CLIv2

1. Nainstalujte [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli) a [přihlášení](/cli/azure/authenticate-azure-cli).

1. Seznam pravidel sítě protokolu IP.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

1. Přidáte pravidlo pro sítě pro jednotlivé IP adresy.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Přidáte pravidlo pro rozsah IP adres pro sítě.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

1. Odeberte pravidlo pro sítě pro jednotlivé IP adresy.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Odeberte pravidlo pro rozsah IP adres pro sítě.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Nezapomeňte [nastavte výchozí pravidlo](#change-the-default-network-access-rule) k **Odepřít**, nebo pravidel sítě nemají žádný vliv.

## <a name="exceptions"></a>Výjimky

Síťová pravidla usnadňují vytvoření zabezpečeného prostředí pro připojení mezi vašimi aplikacemi a Vašimi daty pro většinu scénářů. Některé aplikace jsou ale závislé na službách Azure, které se nedají jedinečně izolovat prostřednictvím pravidel virtuální sítě nebo IP adres. Takové služby ale musí být udělené do úložiště, aby se povolily úplné funkce aplikace. V takových situacích můžete použít nastavení ***Povolit důvěryhodné služby společnosti Microsoft...*** , aby tyto služby mohly přistupovat k vašim datům, protokolům a analýzám.

### <a name="trusted-microsoft-services"></a>Důvěryhodné služby Microsoftu

Některé služby společnosti Microsoft pracují z sítí, které nelze zahrnout do síťových pravidel. Podmnožině takových důvěryhodných služeb Microsoftu získáte přístup k účtu úložiště a přitom zachováte Síťová pravidla pro ostatní aplikace. Tyto důvěryhodné služby pak budou používat silné ověřování pro zabezpečené připojení k vašemu účtu úložiště. Povolili jsme dva režimy důvěryhodného přístupu pro služby Microsoftu.

- Prostředky některých služeb, **Pokud jsou zaregistrované ve vašem předplatném**, mají přístup k vašemu účtu úložiště **ve stejném předplatném** pro vybrané operace, jako je například zápis protokolů nebo zálohování.
- Prostředkům některých služeb lze udělit explicitní přístup k vašemu účtu úložiště **přiřazením role RBAC** ke své spravované identitě přiřazené systémem.


Pokud povolíte nastavení **Povolit důvěryhodné služby společnosti Microsoft...** , prostředky následujících služeb, které jsou zaregistrované ve stejném předplatném jako účet úložiště, budou mít přístup k omezené sadě operací, jak je popsáno níže:

| Služba                  | Název poskytovatele prostředků     | Povolené operace                 |
|:------------------------ |:-------------------------- |:---------------------------------- |
| Azure Backup             | Microsoft.RecoveryServices | Spuštění zálohování a obnovení nespravované disky ve virtuálních počítačích IAAS. (není vyžadované pro spravované disky). [Další informace](/azure/backup/backup-introduction-to-azure-backup). |
| Azure Data Box           | Microsoft.DataBox          | Umožňuje importovat data do Azure pomocí Data Box. [Další informace](/azure/databox/data-box-overview). |
| Testovací a vývojová prostředí Azure       | Microsoft.DevTestLab       | Vytváření a artefaktů instalace vlastní image. [Další informace](/azure/devtest-lab/devtest-lab-overview). |
| Azure Event Grid         | Microsoft.EventGrid        | Povolit publikování událostí v úložišti objektů Blob a umožňují Event Grid k publikování do fronty úložiště. Další informace o [události služby blob storage](/azure/event-grid/event-sources) a [publikování do front](/azure/event-grid/event-handlers). |
| Azure Event Hubs         | Microsoft.EventHub         | Archivace dat s Event Hubs Capture. [Další informace](/azure/event-hubs/event-hubs-capture-overview). |
| Synchronizace souborů Azure          | Microsoft.StorageSync      | Umožňuje transformovat souborový server Prem na mezipaměť pro sdílené složky Azure. Povoluje se synchronizace více webů, rychlé zotavení po havárii a zálohování na straně cloudu. [Další informace](../files/storage-sync-files-planning.md) |
| Azure HDInsight          | Microsoft.HDInsight        | Zřídí počáteční obsah výchozího systému souborů pro nový cluster HDInsight. [Další informace](/azure/hdinsight/hdinsight-hadoop-use-blob-storage). |
| Export pro import do Azure      | Microsoft.ImportExport     | Umožňuje importovat data do Azure a exportovat data z Azure pomocí služby pro import a export. [Další informace](/azure/storage/common/storage-import-export-service).  |
| Azure Monitor            | Microsoft.Insights         | Umožňuje zápis dat monitorování do zabezpečeného účtu úložiště, včetně protokolů diagnostiky prostředků, Azure Active Directory protokolů pro přihlášení a auditu a protokolů Microsoft Intune. [Další informace](/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security). |
| Sítě Azure         | Microsoft.Network          | Store a analyzovat protokoly přenosů sítě. [Další informace](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview). |
| Azure Site Recovery      | Microsoft.SiteRecovery     | Povolení replikace pro zotavení po havárii virtuálních počítačů Azure s IaaS při použití mezipaměti, zdrojového nebo cílového účtu úložiště podporujícího bránu firewall  [Další informace](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication). |

Nastavení **Povolit důvěryhodné služby společnosti Microsoft...** umožňuje také konkrétní instanci níže uvedených služeb pro přístup k účtu úložiště, pokud explicitně [přiřadíte roli RBAC](storage-auth-aad.md#assign-rbac-roles-for-access-rights) k [spravované identitě přiřazené systémem](../../active-directory/managed-identities-azure-resources/overview.md) pro danou instanci prostředku. V takovém případě rozsah přístupu pro instanci odpovídá roli RBAC přiřazené spravované identitě.

| Služba                        | Název poskytovatele prostředků          | Účel            |
| :----------------------------- | :------------------------------------- | :---------- |
| Úlohy Azure Container Registry | Microsoft.ContainerRegistry/registries | ACR úlohy mají přístup k účtům úložiště při vytváření imagí kontejneru. |
| Azure Data Factory             | Microsoft. DataFactory/továrny        | Umožňuje přístup k účtům úložiště pomocí modulu runtime ADF. |
| Azure Logic Apps               | Microsoft.Logic/workflows              | Povoluje Logic Apps přístup k účtům úložiště. [Další informace](../../logic-apps/create-managed-service-identity.md#authenticate-access-with-managed-identity). |
| Azure Machine Learning | Microsoft.MachineLearningServices      | Autorizované pracovní prostory Azure Machine Learning zapisují výstup, modely a protokoly do úložiště objektů BLOB experiment. [Další informace](/azure/machine-learning/service/how-to-enable-virtual-network#use-a-storage-account-for-your-workspace). |
| Azure SQL Data Warehouse       | Microsoft.Sql                          | Umožňuje importovat a exportovat data z konkrétních instancí SQL Database pomocí základu. [Další informace](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview). |
| Azure Stream Analytics         | Microsoft.StreamAnalytics             | Umožňuje zapsat data z úlohy streamování do úložiště objektů BLOB. Tato funkce je aktuálně ve verzi Preview. [Další informace](/azure/stream-analytics/blob-output-managed-identity). |
| Azure Synapse Analytics        | Microsoft. synapse/pracovní prostory          | Umožňuje přístup k datům v Azure Storage z synapse Analytics. |


### <a name="storage-analytics-data-access"></a>Přístup k datům Storage analytics

V některých případech je zapotřebí ve směru z mimo hranice sítě přístup ke čtení diagnostické protokoly a metriky. Když konfigurujete přístup důvěryhodných služeb k účtu úložiště, můžete pro soubory protokolů, tabulky metrik nebo obojí dovolit přístup pro čtení. [Další informace o práci s analytika úložiště.](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>Správa výjimek

Můžete spravovat pravidla výjimky sítě prostřednictvím webu Azure portal, Powershellu nebo Azure CLI verze 2.

#### <a name="azure-portal"></a>Portál Azure

1. Přejděte na účet úložiště, kterou chcete zabezpečit.

1. Klikněte v nabídce nastavení nazývá **virtuální sítí a bran firewall**.

1. Zkontrolujte, jestli jste vybrali k povolení přístupu z **vybrané sítě**.

1. V části **výjimky**, vyberte výjimky, kterým chcete přiřadit.

1. Klikněte na tlačítko **Uložit** změny.

#### <a name="powershell"></a>PowerShell

1. Nainstalujte [prostředí Azure PowerShell](/powershell/azure/install-Az-ps) a [přihlášení](/powershell/azure/authenticate-azureps).

1. Zobrazte výjimky pravidel sítě účtu úložiště.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

1. Nakonfigurujte výjimky, čímž pravidel sítě účtu úložiště.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

1. Odeberte výjimky z pravidel sítě účtu úložiště.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> Nezapomeňte [nastavte výchozí pravidlo](#change-the-default-network-access-rule) k **Odepřít**, nebo odebrání výjimky nemají žádný vliv.

#### <a name="cliv2"></a>CLIv2

1. Nainstalujte [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli) a [přihlášení](/cli/azure/authenticate-azure-cli).

1. Zobrazte výjimky pravidel sítě účtu úložiště.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

1. Nakonfigurujte výjimky, čímž pravidel sítě účtu úložiště.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

1. Odeberte výjimky z pravidel sítě účtu úložiště.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> Nezapomeňte [nastavte výchozí pravidlo](#change-the-default-network-access-rule) k **Odepřít**, nebo odebrání výjimky nemají žádný vliv.

## <a name="next-steps"></a>Další kroky

Další informace o koncových bodech služeb Azure Network v [koncové body služby](/azure/virtual-network/virtual-network-service-endpoints-overview).

Dostaňte hlouběji do zabezpečení služby Azure Storage v [Průvodci zabezpečením Azure Storage](../blobs/security-recommendations.md).
