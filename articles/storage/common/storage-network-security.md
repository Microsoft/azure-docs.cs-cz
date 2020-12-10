---
title: Konfigurace Azure Storage bran firewall a virtuálních sítí | Microsoft Docs
description: Nakonfigurujte vícevrstvé zabezpečení sítě pro svůj účet úložiště pomocí Azure Storage bran firewall a Azure Virtual Network.
services: storage
author: santoshc
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: tamram
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: c2074402225c8134329e00003e4fbdfd95b5dba6
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96938388"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Konfigurace bran firewall Azure Storage a virtuálních sítí

Azure Storage zajišťuje vrstvený model zabezpečení. Tento model umožňuje zabezpečit a řídit úroveň přístupu k vašim účtům úložiště, které vyžadují vaše aplikace a podniková prostředí, v závislosti na typu a podmnožině použitých sítí. Při konfiguraci síťových pravidel mají přístup k účtu úložiště jenom aplikace požadující data přes zadanou sadu sítí. Přístup k účtu úložiště můžete omezit na požadavky pocházející ze zadaných IP adres, rozsahů IP adres nebo ze seznamu podsítí v Azure Virtual Network (virtuální síť).

Účty úložiště mají veřejný koncový bod, který je přístupný prostřednictvím Internetu. [Pro svůj účet úložiště můžete také vytvořit privátní koncové body](storage-private-endpoints.md), které přiřadí privátní IP adresu z vaší virtuální sítě k účtu úložiště a zabezpečuje veškerý provoz mezi vaší virtuální sítí a účtem úložiště prostřednictvím privátního propojení. Brána firewall služby Azure Storage poskytuje přístup k řízení přístupu pro veřejný koncový bod vašeho účtu úložiště. Bránu firewall můžete použít také k blokování veškerého přístupu prostřednictvím veřejného koncového bodu při používání privátních koncových bodů. Vaše konfigurace brány firewall pro úložiště také umožňuje, aby bylo možné bezpečně přistupovat k účtu úložiště pomocí výběru důvěryhodných služeb platformy Azure.

Aplikace, která přistupuje k účtu úložiště v případě, že jsou platná síťová pravidla, vyžaduje pro požadavek správnou autorizaci. Autorizace se podporuje s přihlašovacími údaji služby Azure Active Directory (Azure AD) pro objekty BLOB a front, s platným klíčem pro přístup k účtu nebo s tokenem SAS.

> [!IMPORTANT]
> Zapnutím pravidel brány firewall pro váš účet úložiště se ve výchozím nastavení zablokuje příchozí požadavky na data, pokud žádosti pocházejí ze služby v rámci Azure Virtual Network (VNet) nebo z povolených veřejných IP adres. Blokované požadavky zahrnují ty z jiných služeb Azure, od Azure Portal, ze služeb protokolování a metriky atd.
>
> Přístup ke službám Azure, které provozují v rámci virtuální sítě, můžete udělit povolením provozu z podsítě hostující instanci služby. Pomocí mechanismu [výjimek](#exceptions) popsaných níže můžete také povolit omezený počet scénářů. Pokud chcete získat přístup k datům z účtu úložiště prostřednictvím Azure Portal, musíte být na počítači v rámci důvěryhodné hranice (buď IP nebo VNet), kterou jste nastavili.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Scénáře

Chcete-li zabezpečit svůj účet úložiště, je třeba nejprve nakonfigurovat pravidlo pro odepření přístupu k provozu ze všech sítí (včetně internetového provozu) ve výchozím nastavení ve veřejném koncovém bodu. Pak byste měli nakonfigurovat pravidla, která udělí přístup k provozu z konkrétních virtuální sítě. Můžete taky nakonfigurovat pravidla pro udělení přístupu k provozu z výběru rozsahů veřejných IP adres, které umožňují připojení z určitých internetových nebo místních klientů. Tato konfigurace umožňuje vytvořit zabezpečenou hranici sítě pro vaše aplikace.

Můžete zkombinovat pravidla brány firewall, která umožňují přístup z konkrétních virtuálních sítí a z rozsahů veřejných IP adres ve stejném účtu úložiště. Pravidla brány firewall pro úložiště se dají použít u existujících účtů úložiště nebo při vytváření nových účtů úložiště.

Pravidla brány firewall pro úložiště se vztahují na veřejný koncový bod účtu úložiště. Nepotřebujete žádná pravidla přístupu k bránám firewall, aby bylo možné povolit přenosy privátních koncových bodů účtu úložiště. Proces schvalování privátního koncového bodu uděluje implicitní přístup k provozu z podsítě, která je hostitelem privátního koncového bodu.

Síťová pravidla jsou vynutila ve všech síťových protokolech do služby Azure Storage, včetně REST a protokolu SMB. Chcete-li získat přístup k datům pomocí nástrojů, jako jsou Azure Portal, Průzkumník služby Storage a AZCopy, musí být nakonfigurována explicitní Síťová pravidla.

Po použití pravidel sítě se vynutily pro všechny požadavky. Tokeny SAS, které udělují přístup k určité IP adrese, slouží k omezení přístupu držitele tokenu, ale neudělují se novému přístupu nad nakonfigurovaná Síťová pravidla.

Síťový provoz virtuálního počítače (včetně operací připojení a odpojení a vstupně-výstupní operace disku) nejsou ovlivněná síťovými pravidly. Přístup REST k objektům blob stránky je chráněn síťovými pravidly.

Klasické účty úložiště nepodporují brány firewall a virtuální sítě.

Nespravované disky můžete použít v účtech úložiště s pravidly sítě použitými pro zálohování a obnovení virtuálních počítačů, a to vytvořením výjimky. Tento postup je popsán v části [výjimky](#exceptions) v tomto článku. Výjimky brány firewall se nevztahují na spravované disky, protože už jsou spravované přes Azure.

## <a name="change-the-default-network-access-rule"></a>Změna výchozího pravidla přístupu k síti

Účty úložiště ve výchozím nastavení přijímají připojení z klientů v jakékoli síti. Chcete-li omezit přístup na vybrané sítě, musíte nejdřív změnit výchozí akci.

> [!WARNING]
> Změny pravidel sítě mohou mít vliv na schopnost vaší aplikace připojit se k Azure Storage. Nastavením výchozího síťového pravidla na **Odepřít** znemožníte přístup k datům, pokud nejsou použita specifická Síťová pravidla, která **udělují** přístup. Než změníte výchozí pravidlo a odepřete přístup, nezapomeňte prostřednictvím pravidel sítě udělit přístup všem povoleným sítím.

### <a name="managing-default-network-access-rules"></a>Správa výchozích pravidel síťového přístupu

Pomocí Azure Portal, PowerShellu nebo CLIv2 můžete spravovat výchozí pravidla přístupu k síti pro účty úložiště.

#### <a name="azure-portal"></a>portál Azure

1. Přejděte do účtu úložiště, který chcete zabezpečit.

1. Klikněte na nabídku nastavení s názvem **sítě**.

1. Chcete-li odepřít přístup ve výchozím nastavení, vyberte možnost povolit přístup z **vybraných sítí**. Pokud chcete povolit přenos ze všech sítí, zvolte povolení přístupu ze **všech sítí**.

1. Kliknutím na **Uložit** použijte změny.

#### <a name="powershell"></a>PowerShell

1. Nainstalujte [Azure PowerShell](/powershell/azure/install-Az-ps) a [přihlaste](/powershell/azure/authenticate-azureps)se.

1. Zobrazí stav výchozího pravidla pro účet úložiště.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

1. Nastavte výchozí pravidlo na odepřít přístup k síti ve výchozím nastavení.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

1. Nastavte výchozí pravidlo tak, aby povolovalo přístup k síti ve výchozím nastavení.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="cliv2"></a>CLIv2

1. Nainstalujte rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) a [přihlaste](/cli/azure/authenticate-azure-cli)se.

1. Zobrazí stav výchozího pravidla pro účet úložiště.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

1. Nastavte výchozí pravidlo na odepřít přístup k síti ve výchozím nastavení.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

1. Nastavte výchozí pravidlo tak, aby povolovalo přístup k síti ve výchozím nastavení.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```

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

#### <a name="azure-portal"></a>portál Azure

1. Přejděte do účtu úložiště, který chcete zabezpečit.

1. Klikněte na nabídku nastavení s názvem **sítě**.

1. Ověřte, že jste vybrali povolení přístupu z **vybraných sítí**.

1. Chcete-li udělit přístup k virtuální síti pomocí nového síťového pravidla, klikněte v části **virtuální sítě** na položku **Přidat existující virtuální síť**, vyberte možnost **virtuální sítě** a **podsítě** a pak klikněte na tlačítko **Přidat**. Pokud chcete vytvořit novou virtuální síť a udělit jí přístup, klikněte na **Přidat novou virtuální síť**. Zadejte informace potřebné k vytvoření nové virtuální sítě a potom klikněte na tlačítko **vytvořit**.

    > [!NOTE]
    > Pokud koncový bod služby pro Azure Storage ještě nebyl nakonfigurovaný pro vybranou virtuální síť a podsítě, můžete ho nakonfigurovat jako součást této operace.
    >
    > V současné době se při vytváření pravidel zobrazují jenom virtuální sítě patřící do stejného Azure Active Directory tenanta. Pokud chcete udělit přístup k podsíti ve virtuální síti patřící jinému tenantovi, použijte PowerShell, rozhraní příkazového řádku nebo rozhraní REST API.

1. Pokud chcete odebrat pravidlo virtuální sítě nebo podsítě, klikněte na **...** a otevřete místní nabídku pro virtuální síť nebo podsíť a klikněte na **Odebrat**.

1. Kliknutím na **Uložit** použijte změny.

#### <a name="powershell"></a>PowerShell

1. Nainstalujte [Azure PowerShell](/powershell/azure/install-Az-ps) a [přihlaste](/powershell/azure/authenticate-azureps)se.

1. Vypíše pravidla virtuální sítě.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

1. Povolte koncový bod služby pro Azure Storage v existující virtuální síti a podsíti.

    ```powershell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

1. Přidejte síťové pravidlo pro virtuální síť a podsíť.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

    > [!TIP]
    > Pokud chcete přidat síťové pravidlo pro podsíť ve virtuální síti, která patří do jiného tenanta Azure AD, použijte plně kvalifikovaný parametr **VirtualNetworkResourceId** ve formátu "/Subscriptions/Subscription-ID/resourceGroups/resourceGroup-Name/Providers/Microsoft.Network/virtualNetworks/vNet-Name/subnets/Subnet-Name".

1. Odeberte pravidlo sítě pro virtuální síť a podsíť.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> Ujistěte se, že jste [výchozí pravidlo nastavili](#change-the-default-network-access-rule) na **Odepřít**, nebo že Síťová pravidla nemají žádný vliv.

#### <a name="cliv2"></a>CLIv2

1. Nainstalujte rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) a [přihlaste](/cli/azure/authenticate-azure-cli)se.

1. Vypíše pravidla virtuální sítě.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

1. Povolte koncový bod služby pro Azure Storage v existující virtuální síti a podsíti.

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

1. Přidejte síťové pravidlo pro virtuální síť a podsíť.

    ```azurecli
    subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

    > [!TIP]
    > Pokud chcete přidat pravidlo pro podsíť ve virtuální síti, která patří do jiného tenanta Azure AD, použijte plně kvalifikované ID podsítě ve formátu "/Subscriptions/ \<subscription-ID\> /ResourceGroups/ \<resourceGroup-Name\> /providers/Microsoft.Network/virtualNetworks/ \<vNet-name\> /subnets/ \<subnet-name\> ".
    >
    > Pomocí parametru **Subscription** můžete načíst ID podsítě pro virtuální síť patřící jinému Tenantovi služby Azure AD.

1. Odeberte pravidlo sítě pro virtuální síť a podsíť.

    ```azurecli
    subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> Ujistěte se, že jste [výchozí pravidlo nastavili](#change-the-default-network-access-rule) na **Odepřít**, nebo že Síťová pravidla nemají žádný vliv.

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

#### <a name="azure-portal"></a>portál Azure

1. Přejděte do účtu úložiště, který chcete zabezpečit.

1. Klikněte na nabídku nastavení s názvem **sítě**.

1. Ověřte, že jste vybrali povolení přístupu z **vybraných sítí**.

1. Pokud chcete udělit přístup k rozsahu IP adres Internetu, zadejte v části   >  **Rozsah adres** firewallu IP adresu nebo rozsah adres (ve formátu CIDR).

1. Chcete-li odebrat pravidlo sítě protokolu IP, klikněte na ikonu koše vedle rozsahu adres.

1. Kliknutím na **Uložit** použijte změny.

#### <a name="powershell"></a>PowerShell

1. Nainstalujte [Azure PowerShell](/powershell/azure/install-Az-ps) a [přihlaste](/powershell/azure/authenticate-azureps)se.

1. Vypíše pravidla sítě protokolu IP.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

1. Přidejte síťové pravidlo pro jednotlivé IP adresy.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Přidejte síťové pravidlo pro rozsah IP adres.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

1. Odeberte pravidlo sítě pro jednotlivé IP adresy.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Odebere síťové pravidlo pro rozsah IP adres.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Ujistěte se, že jste [výchozí pravidlo nastavili](#change-the-default-network-access-rule) na **Odepřít**, nebo že Síťová pravidla nemají žádný vliv.

#### <a name="cliv2"></a>CLIv2

1. Nainstalujte rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) a [přihlaste](/cli/azure/authenticate-azure-cli)se.

1. Vypíše pravidla sítě protokolu IP.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

1. Přidejte síťové pravidlo pro jednotlivé IP adresy.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Přidejte síťové pravidlo pro rozsah IP adres.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

1. Odeberte pravidlo sítě pro jednotlivé IP adresy.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Odebere síťové pravidlo pro rozsah IP adres.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Ujistěte se, že jste [výchozí pravidlo nastavili](#change-the-default-network-access-rule) na **Odepřít**, nebo že Síťová pravidla nemají žádný vliv.

## <a name="exceptions"></a>Výjimky

Síťová pravidla usnadňují vytvoření zabezpečeného prostředí pro připojení mezi vašimi aplikacemi a Vašimi daty pro většinu scénářů. Některé aplikace jsou ale závislé na službách Azure, které se nedají jedinečně izolovat prostřednictvím pravidel virtuální sítě nebo IP adres. Takové služby ale musí být udělené do úložiště, aby se povolily úplné funkce aplikace. V takových situacích můžete použít možnost **_povoluje důvěryhodné služby společnosti Microsoft..._* _ nastavení, které umožní těmto službám přístup k vašim datům, protokolům nebo analýzám.

### <a name="trusted-microsoft-services"></a>Důvěryhodné služby Microsoftu

Některé služby společnosti Microsoft pracují z sítí, které nelze zahrnout do síťových pravidel. Podmnožině takových důvěryhodných služeb Microsoftu získáte přístup k účtu úložiště a přitom zachováte Síťová pravidla pro ostatní aplikace. Tyto důvěryhodné služby pak budou používat silné ověřování pro zabezpečené připojení k vašemu účtu úložiště. Povolili jsme dva režimy důvěryhodného přístupu pro služby Microsoftu.

- Prostředky některých služeb, _ * Pokud jsou zaregistrované ve vašem předplatném * *, mají přístup k vašemu účtu úložiště **ve stejném předplatném** pro vybrané operace, jako je například zápis protokolů nebo zálohování.
- Prostředkům některých služeb lze udělit explicitní přístup k účtu úložiště tím, že **přiřadíte roli Azure** ke spravované identitě přiřazené systémem.


Pokud povolíte nastavení **Povolit důvěryhodné služby společnosti Microsoft...** , prostředky následujících služeb, které jsou zaregistrované ve stejném předplatném jako účet úložiště, budou mít přístup k omezené sadě operací, jak je popsáno níže:

| Služba                  | Název poskytovatele prostředků     | Povolené operace                 |
|:------------------------ |:-------------------------- |:---------------------------------- |
| Azure Backup             | Microsoft. RecoveryServices | Spusťte zálohování a obnovujte nespravované disky ve virtuálních počítačích IAAS. (není vyžadováno pro Managed Disks). [Přečtěte si další informace](../../backup/backup-overview.md). |
| Azure Data Box           | Microsoft. DataBox          | Umožňuje importovat data do Azure pomocí Data Box. [Přečtěte si další informace](../../databox/data-box-overview.md). |
| Azure DevTest Labs       | Microsoft. DevTestLab       | Vytvoření vlastní image a instalace artefaktů. [Přečtěte si další informace](../../devtest-labs/devtest-lab-overview.md). |
| Azure Event Grid         | Microsoft. EventGrid        | Povolte publikování událostí Blob Storage a umožněte Event Grid publikování do front úložiště. Přečtěte si informace o [událostech služby Blob Storage](../../event-grid/overview.md#event-sources) a [publikování do front](../../event-grid/event-handlers.md). |
| Azure Event Hubs         | Microsoft. EventHub         | Archivujte data pomocí Event Hubsho zachycení. [Další informace](../../event-hubs/event-hubs-capture-overview.md) |
| Synchronizace souborů Azure          | Microsoft. StorageSync      | Umožňuje transformovat souborový server Prem na mezipaměť pro sdílené složky Azure. Povoluje se synchronizace více webů, rychlé zotavení po havárii a zálohování na straně cloudu. [Další informace](../files/storage-sync-files-planning.md) |
| Azure HDInsight          | Microsoft. HDInsight        | Zřídí počáteční obsah výchozího systému souborů pro nový cluster HDInsight. [Přečtěte si další informace](../../hdinsight/hdinsight-hadoop-use-blob-storage.md). |
| Export pro import do Azure      | Microsoft. ImportExport     | Umožňuje importovat data Azure Storage nebo exportovat data z Azure Storage pomocí služby Azure Storage import/export. [Přečtěte si další informace](./storage-import-export-service.md).  |
| Azure Monitor            | Microsoft. Insights         | Povoluje zápis dat monitorování do zabezpečeného účtu úložiště, včetně protokolů prostředků, Azure Active Directory protokolů pro přihlášení a auditu a protokolů Microsoft Intune. [Přečtěte si další informace](../../azure-monitor/platform/roles-permissions-security.md). |
| Sítě Azure         | Microsoft.Network          | Ukládejte a analyzujte protokoly síťového provozu, včetně služeb Network Watcher a Analýza provozu. [Přečtěte si další informace](../../network-watcher/network-watcher-nsg-flow-logging-overview.md). |
| Azure Site Recovery      | Microsoft. SiteRecovery     | Povolení replikace pro zotavení po havárii virtuálních počítačů Azure s IaaS při použití mezipaměti, zdrojového nebo cílového účtu úložiště podporujícího bránu firewall  [Přečtěte si další informace](../../site-recovery/azure-to-azure-tutorial-enable-replication.md). |

Nastavení **Povolit důvěryhodné služby společnosti Microsoft...** umožňuje také konkrétní instanci níže uvedených služeb pro přístup k účtu úložiště, pokud explicitně [přiřadíte roli Azure](storage-auth-aad.md#assign-azure-roles-for-access-rights) k [spravované identitě přiřazené systémem](../../active-directory/managed-identities-azure-resources/overview.md) pro danou instanci prostředku. V takovém případě rozsah přístupu pro instanci odpovídá roli Azure přiřazené spravované identitě.

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


### <a name="storage-analytics-data-access"></a>Přístup k datům Analytics Storage

V některých případech je přístup ke čtení protokolů prostředku a metriky vyžadován mimo hranici sítě. Když konfigurujete přístup důvěryhodných služeb k účtu úložiště, můžete pro soubory protokolů, tabulky metrik nebo obojí dovolit přístup pro čtení. [Přečtěte si další informace o práci s analýzou úložiště.](./storage-analytics.md)

### <a name="managing-exceptions"></a>Správa výjimek

Výjimky síťového pravidla můžete spravovat pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure CLI v2.

#### <a name="azure-portal"></a>portál Azure

1. Přejděte do účtu úložiště, který chcete zabezpečit.

1. Klikněte na nabídku nastavení s názvem **sítě**.

1. Ověřte, že jste vybrali povolení přístupu z **vybraných sítí**.

1. V části **výjimky** vyberte výjimky, které chcete udělit.

1. Kliknutím na **Uložit** použijte změny.

#### <a name="powershell"></a>PowerShell

1. Nainstalujte [Azure PowerShell](/powershell/azure/install-Az-ps) a [přihlaste](/powershell/azure/authenticate-azureps)se.

1. Zobrazí výjimky pro síťová pravidla účtu úložiště.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

1. Nakonfigurujte výjimky na Síťová pravidla účtu úložiště.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

1. Odeberte výjimky pro síťová pravidla účtu úložiště.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> Nezapomeňte [nastavit výchozí pravidlo](#change-the-default-network-access-rule) na **Odepřít**, nebo odebrání výjimek nebude mít žádný vliv.

#### <a name="cliv2"></a>CLIv2

1. Nainstalujte rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) a [přihlaste](/cli/azure/authenticate-azure-cli)se.

1. Zobrazí výjimky pro síťová pravidla účtu úložiště.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

1. Nakonfigurujte výjimky na Síťová pravidla účtu úložiště.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

1. Odeberte výjimky pro síťová pravidla účtu úložiště.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> Nezapomeňte [nastavit výchozí pravidlo](#change-the-default-network-access-rule) na **Odepřít**, nebo odebrání výjimek nebude mít žádný vliv.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o koncových bodech síťových služeb Azure v [koncových bodech služby](../../virtual-network/virtual-network-service-endpoints-overview.md).

Dig hlouběji do zabezpečení Azure Storage v [Azure Storage příručce zabezpečení](../blobs/security-recommendations.md).
