---
title: Konfigurace bran a virtuálních sítí služby Azure Storage | Dokumenty společnosti Microsoft
description: Nakonfigurujte zabezpečení sítě s vrstvami pro váš účet úložiště.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: tamram
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 7120ba2cf71c9af5373b830d04d0b67952922887
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113509"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Konfigurace bran azure storage a virtuálních sítí

Azure Storage poskytuje vrstvený model zabezpečení. Tento model umožňuje zabezpečit a řídit úroveň přístupu k účtům úložiště, které vaše aplikace a podniková prostředí vyžadují, na základě typu a podmnožiny používaných sítí. Při konfiguraci síťových pravidel mají k účtu úložiště přístup pouze aplikace požadující data přes zadanou sadu sítí. Přístup k účtu úložiště můžete omezit na požadavky pocházející ze zadaných IP adres, rozsahů IP adres nebo ze seznamu podsítí ve virtuální síti Azure (Virtuální síť).

Účty úložiště mají veřejný koncový bod, který je přístupný přes Internet. Můžete také vytvořit [soukromé koncové body pro váš účet úložiště](storage-private-endpoints.md), který přiřadí privátní IP adresu z vaší virtuální sítě k účtu úložiště a zabezpečí veškerý provoz mezi vaší virtuální sítí a účtem úložiště přes privátní odkaz. Brána firewall úložiště Azure poskytuje přístup k řízení přístupu pro veřejný koncový bod vašeho účtu úložiště. Bránu firewall můžete také použít k zablokování veškerého přístupu prostřednictvím veřejného koncového bodu při použití privátní koncové body. Konfigurace brány firewall úložiště také umožňuje vybrat důvěryhodné služby platformy Azure pro bezpečný přístup k účtu úložiště.

Aplikace, která přistupuje k účtu úložiště, když jsou pravidla sítě v platnosti, stále vyžaduje řádnou autorizaci žádosti. Autorizace je podporovaná s přihlašovacími údaji služby Azure Active Directory (Azure AD) pro objekty BLOB a fronty s platným klíčem pro přístup k účtu nebo tokenem SAS.

> [!IMPORTANT]
> Zapnutí pravidel brány firewall pro váš účet úložiště blokuje příchozí požadavky na data ve výchozím nastavení, pokud požadavky pocházejí ze služby pracující v rámci virtuální sítě (VNet) Azure nebo z povolených veřejných IP adres. Mezi blokované požadavky patří požadavky z jiných služeb Azure, z portálu Azure, ze služeb protokolování a metrik a tak dále.
>
> Přístup ke službám Azure, které fungují v rámci virtuální sítě, můžete udělit povolením provozu z podsítě hostující instanci služby. Můžete také povolit omezený počet scénářů prostřednictvím mechanismu [výjimek](#exceptions) popsaného níže. Chcete-li získat přístup k datům z účtu úložiště prostřednictvím portálu Azure, musíte být v počítači v rámci důvěryhodné hranice (ip nebo virtuální sítě), kterou jste nastavili.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Scénáře

Chcete-li zabezpečit účet úložiště, měli byste nejprve nakonfigurovat pravidlo pro odepření přístupu k provozu ze všech sítí (včetně internetového provozu) ve veřejném koncovém bodu ve výchozím nastavení. Potom byste měli nakonfigurovat pravidla, která udělují přístup k provozu z konkrétních virtuálních sítí. Můžete také nakonfigurovat pravidla pro udělení přístupu k provozu z vybraných rozsahů veřejných internetových IP adres a povolení připojení z konkrétních internetových nebo místních klientů. Tato konfigurace umožňuje vytvořit zabezpečené hranice sítě pro vaše aplikace.

Můžete kombinovat pravidla brány firewall, která umožňují přístup z konkrétních virtuálních sítí a z oblastí veřejných IP adres na stejném účtu úložiště. Pravidla brány firewall úložiště lze použít pro existující účty úložiště nebo při vytváření nových účtů úložiště.

Pravidla brány firewall úložiště platí pro veřejný koncový bod účtu úložiště. K povolení přenosu soukromých koncových bodů účtu úložiště nepotřebujete žádná pravidla přístupu brány firewall. Proces schvalování vytvoření privátního koncového bodu uděluje implicitní přístup k provozu z podsítě, která hostuje soukromý koncový bod.

Síťová pravidla se vynucují ve všech síťových protokolech do úložiště Azure, včetně REST a SMB. Chcete-li získat přístup k datům pomocí nástrojů, jako je portál Azure, Průzkumník úložiště a AZCopy, musí být nakonfigurována explicitní síťová pravidla.

Jakmile jsou použita síťová pravidla, jsou vynucena pro všechny požadavky. Tokeny SAS, které udělují přístup k určité adrese IP, slouží k omezení přístupu držitele tokenu, ale neudělují nový přístup nad rámec nakonfigurovaných síťových pravidel.

Provoz na disku virtuálního počítače (včetně operací připojení a odpojení a vodicího zařízení disku) není ovlivněn síťovými pravidly. Přístup REST k objektům BLOB stránky je chráněn síťovými pravidly.

Klasické účty úložiště nepodporují brány firewall a virtuální sítě.

Nespravované disky můžete použít v účtech úložiště se síťovými pravidly použitými pro zálohování a obnovení virtuálních počítačů vytvořením výjimky. Tento proces je popsán v části [Výjimky](#exceptions) tohoto článku. Výjimky brány firewall se nedají použít na spravované disky, protože už jsou spravované Azure.

## <a name="change-the-default-network-access-rule"></a>Změna výchozího pravidla přístupu k síti

Účty úložiště ve výchozím nastavení přijímají připojení z klientů v jakékoli síti. Chcete-li omezit přístup na vybrané sítě, musíte nejdřív změnit výchozí akci.

> [!WARNING]
> Změny pravidel sítě mohou mít vliv na schopnost vaší aplikace připojit se k Azure Storage. Nastavení výchozího pravidla sítě **pro odepření** blokuje veškerý přístup k datům, pokud nejsou použita také určitá síťová pravidla, která **udělují** přístup. Než změníte výchozí pravidlo a odepřete přístup, nezapomeňte prostřednictvím pravidel sítě udělit přístup všem povoleným sítím.

### <a name="managing-default-network-access-rules"></a>Správa výchozích pravidel síťového přístupu

Výchozí pravidla přístupu k síti pro účty úložiště můžete spravovat prostřednictvím portálu Azure, PowerShellu nebo CLIv2.

#### <a name="azure-portal"></a>portál Azure

1. Přejděte do účtu úložiště, který chcete zabezpečit.

1. Klikněte na nabídku nastavení s názvem **Firewally a virtuální sítě**.

1. Chcete-li odepřít přístup ve výchozím nastavení, zvolte povolit přístup z **vybraných sítí**. Pokud chcete povolit přenos ze všech sítí, zvolte povolení přístupu ze **všech sítí**.

1. Kliknutím na **Uložit** použijte změny.

#### <a name="powershell"></a>PowerShell

1. Nainstalujte [Azure PowerShell](/powershell/azure/install-Az-ps) a [přihlaste se](/powershell/azure/authenticate-azureps).

1. Zobrazte stav výchozího pravidla pro účet úložiště.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

1. Nastavte výchozí pravidlo pro odepření přístupu k síti ve výchozím nastavení.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

1. Nastavte výchozí pravidlo, aby bylo ve výchozím nastavení povolováno připojení k síti.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="cliv2"></a>CLIv2

1. Nainstalujte [příkazové příkazové příkazové příkazy k Azure](/cli/azure/install-azure-cli) a [přihlaste se](/cli/azure/authenticate-azure-cli).

1. Zobrazte stav výchozího pravidla pro účet úložiště.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

1. Nastavte výchozí pravidlo pro odepření přístupu k síti ve výchozím nastavení.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

1. Nastavte výchozí pravidlo, aby bylo ve výchozím nastavení povolováno připojení k síti.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```

## <a name="grant-access-from-a-virtual-network"></a>Udělení přístupu z virtuální sítě

Účty úložiště můžete nakonfigurovat tak, aby umožňovaly přístup pouze z určitých podsítí. Povolené podsítě mohou patřit do virtuální sítě ve stejném předplatném nebo ve stejném předplatném, včetně předplatných patřících jinému tenantovi služby Azure Active Directory.

Povolte [koncový bod služby](/azure/virtual-network/virtual-network-service-endpoints-overview) pro Azure Storage v rámci virtuální sítě. Koncový bod služby směruje provoz z virtuální sítě přes optimální cestu ke službě Azure Storage. Identity podsítě a virtuální sítě jsou také přenášeny s každým požadavkem. Správci pak mohou nakonfigurovat síťová pravidla pro účet úložiště, která umožňují přijímat požadavky z konkrétních podsítí ve virtuální síti. Klienti, kterým byl udělen přístup prostřednictvím těchto síťových pravidel, musí i nadále splňovat požadavky na autorizaci účtu úložiště pro přístup k datům.

Každý účet úložiště podporuje až 100 pravidel virtuální sítě, která mohou být kombinována s [pravidly sítě IP](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Dostupné oblasti virtuální sítě

Koncové body služby obecně fungují mezi virtuálními sítěmi a instancemi služeb ve stejné oblasti Azure. Při použití koncových bodů služby s Azure Storage se tento obor zvětšuje tak, aby zahrnoval [spárovanou oblast](/azure/best-practices-availability-paired-regions). Koncové body služby umožňují kontinuitu během regionálního převzetí služeb při selhání a přístup k geograficky redundantním upočovacím datům (RA-GRS) jen pro čtení. Síťová pravidla, která udělují přístup z virtuální sítě k účtu úložiště, také udělují přístup k libovolné instanci RA-GRS.

Při plánování zotavení po havárii během místnívýprodej, měli byste vytvořit virtuální sítě ve spárované oblasti předem. Povolte koncové body služby pro Azure Storage, přičemž síťová pravidla udělují přístup z těchto alternativních virtuálních sítí. Pak tato pravidla použijte na účty geograficky redundantního úložiště.

> [!NOTE]
> Koncové body služby se nevztahují na provoz mimo oblast virtuální sítě a dvojice určených oblastí. Síťová pravidla udělující přístup z virtuálních sítí lze použít pouze k účtům úložiště v primární oblasti účtu úložiště nebo v určené spárované oblasti.

### <a name="required-permissions"></a>Požadovaná oprávnění

Chcete-li použít pravidlo virtuální sítě pro účet úložiště, musí mít uživatel příslušná oprávnění pro přidávané podsítě. Potřebné oprávnění je *Připojit se ke službě do podsítě* a je součástí předdefinované role *přispěvatele účtu úložiště.* Lze jej také přidat do vlastních definic rolí.

Účet úložiště a virtuální sítě udělený přístup může být v různých předplatných, včetně předplatných, které jsou součástí jiného klienta Azure AD.

> [!NOTE]
> Konfigurace pravidel, která udělují přístup k podsítím ve virtuálních sítích, které jsou součástí jiného klienta Služby Azure Active Directory, je aktuálně podporována jenom prostřednictvím rozhraní API powershellu, příkazového příkazu k příkazu CLI a REST. Tato pravidla nelze nakonfigurovat prostřednictvím portálu Azure, i když mohou být zobrazeny na portálu.

### <a name="managing-virtual-network-rules"></a>Správa pravidel virtuální sítě

Pravidla virtuální sítě pro účty úložiště můžete spravovat prostřednictvím portálu Azure, PowerShellu nebo CLIv2.

#### <a name="azure-portal"></a>portál Azure

1. Přejděte do účtu úložiště, který chcete zabezpečit.

1. Klikněte na nabídku nastavení s názvem **Firewally a virtuální sítě**.

1. Zkontrolujte, zda jste zvolili povolení **přístupu**z vybraných sítí .

1. Pokud chcete udělit přístup k virtuální síti s novým síťovým pravidlem, klikněte v části **Virtuální sítě**na Přidat existující **virtuální síť**, vyberte Možnosti Virtuální **sítě** a **Podsítě** a potom klikněte na **Přidat**. Pokud chcete vytvořit novou virtuální síť a udělit jí přístup, klikněte na **Přidat novou virtuální síť**. Zadejte informace potřebné k vytvoření nové virtuální sítě a klepněte na tlačítko **Vytvořit**.

    > [!NOTE]
    > Pokud koncový bod služby pro Azure Storage nebyl dříve nakonfigurovaný pro vybranou virtuální síť a podsítě, můžete ji nakonfigurovat jako součást této operace.
    >
    > V současné době se při vytváření pravidel zobrazí pro výběr pouze virtuální sítě patřící do stejného klienta služby Azure Active Directory. Chcete-li udělit přístup k podsíti ve virtuální síti patřící jinému tenantovi, použijte rozhraní API powershellu, rozhraní CLI nebo REST.

1. Chcete-li odebrat pravidlo virtuální sítě nebo podsítě, klepnutím na tlačítko **...** otevřete místní nabídku pro virtuální síť nebo podsíť a klepněte na tlačítko **Odebrat**.

1. Kliknutím na **Uložit** použijte změny.

#### <a name="powershell"></a>PowerShell

1. Nainstalujte [Azure PowerShell](/powershell/azure/install-Az-ps) a [přihlaste se](/powershell/azure/authenticate-azureps).

1. Seznam pravidel virtuální sítě.

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
    > Chcete-li přidat síťové pravidlo pro podsíť ve virtuální síti patřící do jiného klienta Služby Azure AD, použijte plně kvalifikovaný parametr **VirtualNetworkResourceId** ve formuláři "/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name".

1. Odeberte síťové pravidlo pro virtuální síť a podsíť.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> Nezapomeňte [nastavit výchozí pravidlo](#change-the-default-network-access-rule) **pro odepření**nebo síťová pravidla nemají žádný vliv.

#### <a name="cliv2"></a>CLIv2

1. Nainstalujte [příkazové příkazové příkazové příkazy k Azure](/cli/azure/install-azure-cli) a [přihlaste se](/cli/azure/authenticate-azure-cli).

1. Seznam pravidel virtuální sítě.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

1. Povolte koncový bod služby pro Azure Storage v existující virtuální síti a podsíti.

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

1. Přidejte síťové pravidlo pro virtuální síť a podsíť.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

    > [!TIP]
    > Chcete-li přidat pravidlo pro podsíť ve virtuální síti patřící jinému tenantovi Služby Azure AD, použijte\<plně kvalifikované\>ID\<podsítě ve\>formuláři "/subscriptions/\<subscription-ID\>/resourceGroups/\<resourceGroup-Name /providers/Microsoft.Network/virtualNetworks/ vNet-name /podnets/ podnet-name\>".
    >
    > Parametr **předplatného** můžete použít k načtení ID podsítě pro virtuální síť patřící do jiného klienta Azure AD.

1. Odeberte síťové pravidlo pro virtuální síť a podsíť.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> Nezapomeňte [nastavit výchozí pravidlo](#change-the-default-network-access-rule) **pro odepření**nebo síťová pravidla nemají žádný vliv.

## <a name="grant-access-from-an-internet-ip-range"></a>Udělení přístupu z internetového rozsahu IP adres

Účty úložiště můžete nakonfigurovat tak, aby umožňovaly přístup z konkrétních rozsahů ip adres sítě Internet. Tato konfigurace poskytuje přístup ke konkrétním internetovým službám a místním sítím a blokuje obecný internetový provoz.

Poskytněte povolené rozsahy internetových adres pomocí [zápisu CIDR](https://tools.ietf.org/html/rfc4632) ve tvaru *16.17.18.0/24* nebo jako jednotlivé IP adresy jako *16.17.18.19*.

   > [!NOTE]
   > Malé rozsahy adres používající velikosti předpony "/31" nebo "/32" nejsou podporovány. Tyto rozsahy by měly být konfigurovány pomocí jednotlivých pravidel IP adres.

Pravidla sítě IP jsou povolena pouze pro **veřejné internetové** IP adresy. Rozsahy IP adres vyhrazené pro privátní sítě (podle definice v [RFC 1918)](https://tools.ietf.org/html/rfc1918#section-3)nejsou v pravidlech IP povoleny. Soukromé sítě zahrnují adresy začínající _10.*_, _172.16.*_ - _172.31.*_ a _192.168.*_.

   > [!NOTE]
   > Pravidla sítě IP nemají žádný vliv na požadavky pocházející ze stejné oblasti Azure jako účet úložiště. Použití [pravidla virtuální sítě](#grant-access-from-a-virtual-network) povolit požadavky stejné oblasti.

  > [!NOTE]
  > Služby nasazené ve stejné oblasti jako účet úložiště používají pro komunikaci privátní IP adresy Azure. Proto nelze omezit přístup ke konkrétním službám Azure na základě jejich rozsahu veřejných odchozích IP adres.

Pro konfiguraci pravidel brány firewall úložiště jsou podporovány pouze adresy IPV4.

Každý účet úložiště podporuje až 100 pravidel sítě IP.

### <a name="configuring-access-from-on-premises-networks"></a>Konfigurace přístupu z místních sítí

Chcete-li udělit přístup z místních sítí k účtu úložiště pomocí pravidla sítě IP, je nutné identifikovat ip adresy používané vaší sítí. Požádejte o pomoc správce sítě.

Pokud používáte [ExpressRoute](/azure/expressroute/expressroute-introduction) z vašich prostor, pro veřejný partnerský vztah nebo partnerský vztah Microsoftu, budete muset identifikovat IP adresy NAT, které se používají. Ve veřejných partnerských vztazích každý okruh ExpressRoute automaticky využívá dvě IP adresy pro překlad adres (NAT), které se používají k provozu služeb Azure při vstupu do páteřní sítě Microsoft Azure. Pro partnerský vztah společnosti Microsoft jsou použité IP adresy NAT buď poskytnuty zákazníkem, nebo jsou poskytovány poskytovatelem služeb. Pokud chcete povolit přístup k prostředkům služby, musíte tyto veřejné IP adresy povolit v nastavení IP adresy brány firewall prostředku. Pokud chcete zjistit IP adresy veřejného partnerského okruhu ExpressRoute, [otevřete lístek podpory pro ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) na webu Azure Portal. Další informace o [překladu adres (NAT) pro veřejné partnerské vztahy a partnerské vztahy s Microsoftem v ExpressRoute.](/azure/expressroute/expressroute-nat#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>Správa pravidel sítě IP

Pravidla sítě IP pro účty úložiště můžete spravovat prostřednictvím portálu Azure, PowerShellu nebo CLIv2.

#### <a name="azure-portal"></a>portál Azure

1. Přejděte do účtu úložiště, který chcete zabezpečit.

1. Klikněte na nabídku nastavení s názvem **Firewally a virtuální sítě**.

1. Zkontrolujte, zda jste zvolili povolení **přístupu**z vybraných sítí .

1. Chcete-li udělit přístup k rozsahu IP adres internetu, zadejte ip adresu nebo rozsah adres (ve formátu CIDR) v části**Rozsah adres**brány **firewall** > .

1. Chcete-li odebrat pravidlo sítě IP, klepněte na ikonu koše vedle rozsahu adres.

1. Kliknutím na **Uložit** použijte změny.

#### <a name="powershell"></a>PowerShell

1. Nainstalujte [Azure PowerShell](/powershell/azure/install-Az-ps) a [přihlaste se](/powershell/azure/authenticate-azureps).

1. Seznam pravidel sítě IP.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

1. Přidejte síťové pravidlo pro jednotlivé adresy IP.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Přidejte síťové pravidlo pro rozsah adres IP.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

1. Odeberte síťové pravidlo pro jednotlivé adresy IP.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Odeberte síťové pravidlo pro rozsah adres IP.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Nezapomeňte [nastavit výchozí pravidlo](#change-the-default-network-access-rule) **pro odepření**nebo síťová pravidla nemají žádný vliv.

#### <a name="cliv2"></a>CLIv2

1. Nainstalujte [příkazové příkazové příkazové příkazy k Azure](/cli/azure/install-azure-cli) a [přihlaste se](/cli/azure/authenticate-azure-cli).

1. Seznam pravidel sítě IP.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

1. Přidejte síťové pravidlo pro jednotlivé adresy IP.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Přidejte síťové pravidlo pro rozsah adres IP.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

1. Odeberte síťové pravidlo pro jednotlivé adresy IP.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Odeberte síťové pravidlo pro rozsah adres IP.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Nezapomeňte [nastavit výchozí pravidlo](#change-the-default-network-access-rule) **pro odepření**nebo síťová pravidla nemají žádný vliv.

## <a name="exceptions"></a>Výjimky

Pravidla sítě pomáhají vytvořit zabezpečené prostředí pro připojení mezi aplikacemi a daty pro většinu scénářů. Některé aplikace však závisí na službách Azure, které nelze jednoznačně izolovat prostřednictvím pravidel virtuální sítě nebo IP adres. Ale tyto služby musí být poskytnuty do úložiště povolit plnou funkčnost aplikace. V takových situacích můžete použít nastavení ***Povolit důvěryhodným službám společnosti Microsoft...*** a povolit těmto službám přístup k vašim datům, protokolům nebo analýzám.

### <a name="trusted-microsoft-services"></a>Důvěryhodné služby společnosti Microsoft

Některé služby společnosti Microsoft pracují ze sítí, které nelze zahrnout do pravidel sítě. Podmnožinu těchto důvěryhodných služeb společnosti Microsoft můžete udělit přístup k účtu úložiště při zachování síťových pravidel pro jiné aplikace. Tyto důvěryhodné služby pak budou používat silné ověřování pro bezpečné připojení k účtu úložiště. Povolili jsme dva režimy důvěryhodného přístupu ke službám společnosti Microsoft.

- Prostředky některých služeb, **pokud jsou registrovány ve vašem předplatném**, mají přístup k vašemu účtu úložiště **ve stejném předplatném** pro vybrané operace, jako je zápis protokolů nebo zálohování.
- Prostředky některých služeb může být udělen explicitní přístup k účtu úložiště **přiřazením role RBAC** k jeho systémem přiřazené spravované identity.


Pokud povolíte nastavení **Povolit důvěryhodné služby společnosti Microsoft...** , budou prostředky následujících služeb registrovaných ve stejném předplatném jako váš účet úložiště udělen přístup pro omezenou sadu operací, jak je popsáno:

| Služba                  | Název poskytovatele prostředků     | Operace povoleny                 |
|:------------------------ |:-------------------------- |:---------------------------------- |
| Azure Backup             | Služby Microsoft.RecoveryServices | Spouštět zálohy a obnovení nespravovaných disků ve virtuálních počítačích IAAS. (není vyžadováno pro spravované disky). [Další informace](/azure/backup/backup-introduction-to-azure-backup). |
| Azure Data Box           | Microsoft.DataBox          | Umožňuje import dat do Azure pomocí datové schránky. [Další informace](/azure/databox/data-box-overview). |
| Azure DevTest Labs       | Microsoft.DevTestLab       | Vytvoření vlastní image a instalace artefaktů. [Další informace](/azure/devtest-lab/devtest-lab-overview). |
| Azure Event Grid         | Microsoft.EventGrid        | Povolte publikování událostí úložiště objektů blob a povolte publikování event gridu do front úložiště. Informace o [událostech úložiště objektů blob](/azure/event-grid/event-sources) a [publikování do front](/azure/event-grid/event-handlers). |
| Azure Event Hubs         | Microsoft.EventHub         | Archivujte data pomocí funkce Capture centra událostí. [Další informace](/azure/event-hubs/event-hubs-capture-overview). |
| Synchronizace souborů Azure          | Microsoft.StorageSync      | Umožňuje transformovat souborový server on-prem na mezipaměť pro sdílené složky Azure. Umožňuje synchronizaci ve více lokalitách, rychlé zotavení po havárii a zálohování na straně cloudu. [Další informace](../files/storage-sync-files-planning.md) |
| Azure HDInsight          | Microsoft.HDInsight        | Zřídit počáteční obsah výchozího systému souborů pro nový cluster HDInsight. [Další informace](/azure/hdinsight/hdinsight-hadoop-use-blob-storage). |
| Export importu Azure      | Microsoft.ImportExport     | Umožňuje import dat do Azure a export dat z Azure pomocí služby Import/Export. [Další informace](/azure/storage/common/storage-import-export-service).  |
| Azure Monitor            | Microsoft.Insights         | Umožňuje zápis dat monitorování do zabezpečeného účtu úložiště, včetně protokolů diagnostiky prostředků, protokolů přihlášení a auditu služby Azure Active Directory a protokolů Microsoft Intune. [Další informace](/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security). |
| Azure Networking         | Microsoft.Network          | Uklápěte a analyzujte protokoly síťového provozu. [Další informace](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview). |
| Azure Site Recovery      | Microsoft.SiteRecovery     | Povolte replikaci pro zotavení po havárii virtuálních počítačů Azure IaaS při použití účtů mezipaměti, zdrojového nebo cílového úložiště s povolenou bránou firewall.  [Další informace](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication). |

Povolit **důvěryhodné služby společnosti Microsoft...** nastavení také umožňuje konkrétní instanci níže uvedených služeb pro přístup k účtu úložiště, pokud explicitně [přiřadit roli RBAC](storage-auth-aad.md#assign-rbac-roles-for-access-rights) [systémově přiřazené spravované identity](../../active-directory/managed-identities-azure-resources/overview.md) pro tuto instanci prostředku. V tomto případě rozsah přístupu pro instanci odpovídá roli RBAC přiřazené spravované identitě.

| Služba                        | Název poskytovatele prostředků                 | Účel            |
| :----------------------------- | :------------------------------------- | :----------------- |
| Azure Cognitive Search         | Microsoft.Search/searchServices        | Umožňuje službám cognitive search přístup k účtům úložiště pro indexování, zpracování a dotazování. |
| Úlohy Azure Container Registry | Microsoft.ContainerRegistry/registry | Úlohy ACR mají přístup k účtům úložiště při vytváření iimages kontejnerů. |
| Azure Data Factory             | Microsoft.DataFactory/továrny        | Umožňuje přístup k účtům úložiště prostřednictvím runtime ADF. |
| Azure Data Share               | Microsoft.DataShare/účty           | Umožňuje přístup k účtům úložiště prostřednictvím sdílení dat. |
| Azure Logic Apps               | Microsoft.Logic/pracovní postupy              | Umožňuje aplikacím logiky přístup k účtům úložiště. [Další informace](/azure/logic-apps/create-managed-service-identity#authenticate-access-with-managed-identity). |
| Služba Azure Machine Learning | Microsoft.MachineLearningServices      | Autorizované pracovní prostory Azure Machine Learning zapisují experimentální výstup, modely a protokoly do úložiště objektů Blob a načtou data. [Další informace](/azure/machine-learning/service/how-to-enable-virtual-network#use-a-storage-account-for-your-workspace). | 
| Azure SQL Data Warehouse       | Microsoft.Sql                          | Umožňuje import a export dat z konkrétních instancí databáze SQL pomocí polybase. [Další informace](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview). |
| Azure Stream Analytics         | Microsoft.StreamAnalytics             | Umožňuje zápis dat z úlohy streamování do úložiště objektů Blob. Tato funkce je aktuálně ve verzi Preview. [Další informace](/azure/stream-analytics/blob-output-managed-identity). |
| Azure Synapse Analytics        | Microsoft.Synapse/pracovní prostory          | Umožňuje přístup k datům ve službě Azure Storage ze služby Synapse Analytics. |


### <a name="storage-analytics-data-access"></a>Přístup k datům analýzy úložiště

V některých případech je vyžadován přístup ke čtení diagnostických protokolů a metrik y mimo hranice sítě. Při konfiguraci přístupu důvěryhodných služeb k účtu úložiště můžete povolit přístup pro čtení pro soubory protokolu, tabulky metrik nebo obojí. [Přečtěte si další informace o práci s analýzou úložišť.](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>Správa výjimek

Výjimky síťových pravidel můžete spravovat prostřednictvím portálu Azure, PowerShellu nebo Azure CLI v2.

#### <a name="azure-portal"></a>portál Azure

1. Přejděte do účtu úložiště, který chcete zabezpečit.

1. Klikněte na nabídku nastavení s názvem **Firewally a virtuální sítě**.

1. Zkontrolujte, zda jste zvolili povolení **přístupu**z vybraných sítí .

1. V **části Výjimky**vyberte výjimky, které chcete udělit.

1. Kliknutím na **Uložit** použijte změny.

#### <a name="powershell"></a>PowerShell

1. Nainstalujte [Azure PowerShell](/powershell/azure/install-Az-ps) a [přihlaste se](/powershell/azure/authenticate-azureps).

1. Zobrazí výjimky pro pravidla sítě účtu úložiště.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

1. Nakonfigurujte výjimky z pravidel sítě účtu úložiště.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

1. Odeberte výjimky z pravidel sítě účtu úložiště.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> Nezapomeňte [nastavit výchozí pravidlo](#change-the-default-network-access-rule) **pro odepření**nebo odebrání výjimek nemá žádný vliv.

#### <a name="cliv2"></a>CLIv2

1. Nainstalujte [příkazové příkazové příkazové příkazy k Azure](/cli/azure/install-azure-cli) a [přihlaste se](/cli/azure/authenticate-azure-cli).

1. Zobrazí výjimky pro pravidla sítě účtu úložiště.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

1. Nakonfigurujte výjimky z pravidel sítě účtu úložiště.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

1. Odeberte výjimky z pravidel sítě účtu úložiště.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> Nezapomeňte [nastavit výchozí pravidlo](#change-the-default-network-access-rule) **pro odepření**nebo odebrání výjimek nemá žádný vliv.

## <a name="next-steps"></a>Další kroky

Další informace o koncových bodech služby Azure Network v [koncových bodech služby](/azure/virtual-network/virtual-network-service-endpoints-overview).

Hlouběji se promítejte do zabezpečení Azure Storage v [průvodci zabezpečením Azure Storage](../blobs/security-recommendations.md).
