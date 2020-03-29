---
title: Virtuální sítě
titleSuffix: Azure Cognitive Services
description: Nakonfigurujte zabezpečení sítě s vrstvami pro prostředky služeb Cognitive Services.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 0988c8154c63bb408493edf3243078e625c80d53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371218"
---
# <a name="configure-azure-cognitive-services-virtual-networks"></a>Konfigurace virtuálních sítí Azure Cognitive Services

Azure Cognitive Services poskytuje model zabezpečení s vrstvami. Tento model umožňuje zabezpečit účty služeb Cognitive Services do určité podmnožiny sítí. Při konfiguraci síťových pravidel mají k účtu přístup pouze aplikace požadující data přes zadanou sadu sítí. Přístup k prostředkům můžete omezit pomocí filtrování požadavků. Povolení pouze požadavků pocházejících ze zadaných IP adres, rozsahů IP adres nebo ze seznamu podsítí ve [virtuálních sítích Azure](../virtual-network/virtual-networks-overview.md). Pokud máte o tuto nabídku zájem, budete muset [požádat o přístup k náhledu](https://aka.ms/cog-svc-vnet-signup).

Aplikace, která přistupuje k prostředku služeb Cognitive Services, když jsou pravidla sítě v platnosti, vyžaduje autorizaci. Autorizace je podporovaná přihlašovacími údaji [služby Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) nebo s platným klíčem rozhraní API.

> [!IMPORTANT]
> Zapnutí mařit pravidla brány firewall pro váš účet Cognitive Services blokuje příchozí požadavky na data ve výchozím nastavení. Aby bylo možné žádosti prosadit, je třeba splnit jednu z těchto podmínek:
> * Požadavek by měl pocházet ze služby operující v rámci virtuální sítě Azure (VNet) v seznamu povolených podsítí cílového účtu služeb Cognitive Services. Koncový bod v požadavcích pocházejících z virtuální sítě musí být nastaven jako [vlastní subdoména](cognitive-services-custom-subdomains.md) vašeho účtu služeb Cognitive Services.
> * Nebo by žádost měla pocházet z povoleného seznamu ADRES IP.
>
> Mezi blokované požadavky patří požadavky z jiných služeb Azure, z portálu Azure, ze služeb protokolování a metrik a tak dále.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Scénáře

Chcete-li zabezpečit prostředek služeb Cognitive Services, měli byste nejprve nakonfigurovat pravidlo pro odepření přístupu k provozu ze všech sítí (včetně internetového provozu) ve výchozím nastavení. Potom byste měli nakonfigurovat pravidla, která udělují přístup k provozu z konkrétních virtuálních sítí. Tato konfigurace umožňuje vytvořit zabezpečené hranice sítě pro vaše aplikace. Můžete také nakonfigurovat pravidla pro udělení přístupu k provozu z vybraných rozsahů veřejných internetových IP adres a povolení připojení z konkrétních internetových nebo místních klientů.

Síťová pravidla se vynucují ve všech síťových protokolech služby Azure Cognitive Services, včetně REST a WebSocket. Chcete-li získat přístup k datům pomocí nástrojů, jako jsou testovací konzoly Azure, musí být nakonfigurována explicitní síťová pravidla. Síťová pravidla můžete použít pro existující prostředky služeb Cognitive Services nebo při vytváření nových prostředků služeb Cognitive Services. Jakmile jsou použita síťová pravidla, jsou vynucena pro všechny požadavky.

## <a name="supported-regions-and-service-offerings"></a>Podporované oblasti a nabídky služeb

Podpora virtuálních sítí pro služby Cognitive Services je omezena na oblasti *Střední USA EUAP*, *–střed USA – jih*, Usa – *východ*, Západní *USA 2*, Severní *Evropa*, Jižní Afrika *– sever*, Západní *Evropa*, *Střední Indie*, Austrálie – *východ*, *Západní USA*a Us *Gov Virginia* Azure. Pokud zde není uvedena nabídka služeb, nepodporuje virtuální sítě.

> [!div class="checklist"]
> * [Detektor anomálií](./anomaly-detector/index.yml)
> * [Počítačové vidění](./computer-vision/index.yml)
> * [Content Moderator](./content-moderator/index.yml)
> * [Custom Vision](./custom-vision-service/index.yml)
> * [Tvář](./face/index.yml)
> * [Rozpoznávání formulářů](./form-recognizer/index.yml)
> * [LUIS](./luis/index.yml)
> * [Personalizace](./personalizer/index.yml)
> * [Analýza textu](./text-analytics/index.yml)
> * [QnA Maker](./qnamaker/index.yml)

Podpora virtuálních sítí pro služby Cognitive Services je omezena na *oblasti EUAP – střední*USA – *jih*, usa *– východ*, USA – *západ 2*, *Globální*a US *Gov Virginia* Azure.
> [!div class="checklist"]
> * [Translator Text](./translator/index.yml)

## <a name="service-tags"></a>Značky služeb
Kromě podpory koncových bodů služby virtuální sítě pro výše uvedené služby podporuje služba Cognitive Services také značku služby pro konfiguraci pravidel odchozí sítě. Následující služby jsou zahrnuty v značku služby CognitiveServicesManagement.
> [!div class="checklist"]
> * [Detektor anomálií](./anomaly-detector/index.yml)
> * [Počítačové vidění](./computer-vision/index.yml)
> * [Content Moderator](./content-moderator/index.yml)
> * [Custom Vision](./custom-vision-service/index.yml)
> * [Tvář](./face/index.yml)
> * [Rozpoznávání formulářů](./form-recognizer/index.yml)
> * [LUIS](./luis/index.yml)
> * [Personalizace](./personalizer/index.yml)
> * [Analýza textu](./text-analytics/index.yml)
> * [QnA Maker](./qnamaker/index.yml)
> * [Translator Text](./translator/index.yml)
> * [Služba řeči](./speech-service/index.yml)

## <a name="change-the-default-network-access-rule"></a>Změna výchozího pravidla přístupu k síti

Ve výchozím nastavení prostředky služeb Cognitive Services přijímají připojení od klientů v libovolné síti. Chcete-li omezit přístup na vybrané sítě, musíte nejdřív změnit výchozí akci.

> [!WARNING]
> Provádění změn v pravidlech sítě může mít vliv na schopnost aplikací se připojit ke službám Azure Cognitive Services. Nastavení výchozího pravidla sítě **pro odepření** blokuje veškerý přístup k datům, pokud nejsou použita také určitá síťová pravidla, která **udělují** přístup. Než změníte výchozí pravidlo a odepřete přístup, nezapomeňte prostřednictvím pravidel sítě udělit přístup všem povoleným sítím. Pokud povolujete výpis IP adres pro místní síť, nezapomeňte přidat všechny možné odchozí veřejné IP adresy z místní sítě.

### <a name="managing-default-network-access-rules"></a>Správa výchozích pravidel síťového přístupu

Výchozí pravidla přístupu k síti pro prostředky služeb Cognitive Services můžete spravovat prostřednictvím portálu Azure, PowerShellu nebo nastavení námláčné nastavení Azure.

# <a name="azure-portal"></a>[Portál Azure](#tab/portal)

1. Přejděte na prostředek služeb Cognitive Services, který chcete zabezpečit.

1. Vyberte nabídku **SPRÁVA PROSTŘEDKŮ** nazvanou **Virtuální síť**.

   ![Možnost virtuální sítě](media/vnet/virtual-network-blade.png)

1. Chcete-li odepřít přístup ve výchozím nastavení, zvolte povolit přístup z **vybraných sítí**. S nastavením **Vybrané sítě** sám, bez doprovodu nakonfigurované **virtuální sítě** nebo **rozsahy adres** - veškerý přístup je účinně odepřen. Při odepření všech přístup, požadavky pokusu o využití prostředků služby Cognitive Services nejsou povoleny. Portál Azure, Azure PowerShell nebo Azure CLI se pořád dá použít ke konfiguraci prostředku služeb Cognitive Services.
1. Pokud chcete povolit přenos ze všech sítí, zvolte povolení přístupu ze **všech sítí**.

   ![Virtuální sítě popírají](media/vnet/virtual-network-deny.png)

1. Vyberte **Uložit**, aby se tyto změny použily.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Nainstalujte [Azure PowerShell](/powershell/azure/install-az-ps) a [přihlaste se](/powershell/azure/authenticate-azureps), nebo vyberte **Vyzkoušet**.

1. Zobrazení stavu výchozího pravidla pro prostředek služeb Cognitive Services.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).DefaultAction
    ```

1. Nastavte výchozí pravidlo pro odepření přístupu k síti ve výchozím nastavení.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Deny
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

1. Nastavte výchozí pravidlo, aby bylo ve výchozím nastavení povolováno připojení k síti.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Allow
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Nainstalujte [azure cli](/cli/azure/install-azure-cli) a [přihlaste se](/cli/azure/authenticate-azure-cli), nebo vyberte Try **it**.

1. Zobrazení stavu výchozího pravidla pro prostředek služeb Cognitive Services.

    ```azurecli-interactive
    az cognitiveservices account show \
        -g "myresourcegroup" -n "myaccount" \
        --query networkRuleSet.defaultAction
    ```

1. Nastavte výchozí pravidlo pro odepření přístupu k síti ve výchozím nastavení.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Deny
    ```

1. Nastavte výchozí pravidlo, aby bylo ve výchozím nastavení povolováno připojení k síti.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Allow
    ```

***

## <a name="grant-access-from-a-virtual-network"></a>Udělení přístupu z virtuální sítě

Prostředky služeb Cognitive Services můžete nakonfigurovat tak, aby umožňovaly přístup pouze z určitých podsítí. Povolené podsítě mohou patřit do virtuální sítě ve stejném předplatném nebo v jiném předplatném, včetně předplatných patřících jinému tenantovi služby Azure Active Directory.

Povolte [koncový bod služby](../virtual-network/virtual-network-service-endpoints-overview.md) pro azure kognitivní služby v rámci virtuální sítě. Koncový bod služby směruje provoz z virtuální sítě přes optimální cestu ke službě Azure Cognitive Services. Identity podsítě a virtuální sítě jsou také přenášeny s každým požadavkem. Správci pak mohou nakonfigurovat síťová pravidla pro prostředek služeb Cognitive Services, která umožňují přijímat požadavky z konkrétních podsítí ve virtuální síti. Klienti, kterým byl udělen přístup prostřednictvím těchto síťových pravidel, musí i nadále splňovat požadavky na autorizaci prostředku služeb Cognitive Services pro přístup k datům.

Každý prostředek služeb Cognitive Services podporuje až 100 pravidel virtuální sítě, která mohou být kombinována s [pravidly sítě IP](#grant-access-from-an-internet-ip-range).

### <a name="required-permissions"></a>Požadovaná oprávnění

Chcete-li použít pravidlo virtuální sítě pro prostředek služeb Cognitive Services, musí mít uživatel příslušná oprávnění pro přidávané podsítě. Požadované oprávnění je výchozí role *přispěvatele* nebo role *přispěvatele služeb Cognitive Services.* Požadovaná oprávnění lze také přidat do vlastních definic rolí.

Prostředek služeb Cognitive Services a udělený přístup virtuálních sítí může být v různých předplatných, včetně předplatných, které jsou součástí jiného klienta Azure AD.

> [!NOTE]
> Konfigurace pravidel, která udělují přístup k podsítím ve virtuálních sítích, které jsou součástí jiného klienta Služby Azure Active Directory, je aktuálně podporována jenom prostřednictvím rozhraní API powershellu, příkazového příkazu k příkazu CLI a REST. Tato pravidla nelze nakonfigurovat prostřednictvím portálu Azure, i když mohou být zobrazeny na portálu.

### <a name="managing-virtual-network-rules"></a>Správa pravidel virtuální sítě

Pravidla virtuálních sítí pro prostředky služeb Cognitive Services můžete spravovat prostřednictvím portálu Azure, PowerShellu nebo nastavení námpou v eli Azure.

# <a name="azure-portal"></a>[Portál Azure](#tab/portal)

1. Přejděte na prostředek služeb Cognitive Services, který chcete zabezpečit.

1. Vyberte nabídku **SPRÁVA PROSTŘEDKŮ** nazvanou **Virtuální síť**.

1. Zkontrolujte, zda jste zvolili povolení **přístupu**z vybraných sítí .

1. Pokud chcete udělit přístup k virtuální síti s existujícím pravidlem sítě, vyberte v části **Virtuální sítě**možnost Přidat existující **virtuální síť**.

   ![Přidání existující virtuální sítě](media/vnet/virtual-network-add-existing.png)

1. Vyberte možnosti **Virtuální sítě** a **podsítě** a pak vyberte **Povolit**.

   ![Přidání existujících podrobností virtuální sítě](media/vnet/virtual-network-add-existing-details.png)

1. Pokud chcete vytvořit novou virtuální síť a udělit jí přístup, vyberte **Přidat novou virtuální síť**.

   ![Přidání nové virtuální sítě](media/vnet/virtual-network-add-new.png)

1. Zadejte informace potřebné k vytvoření nové virtuální sítě a pak vyberte **Vytvořit**.

   ![Vytvořit virtuální síť](media/vnet/virtual-network-create.png)

    > [!NOTE]
    > Pokud koncový bod služby pro služby Azure Cognitive Services nebyl dříve nakonfigurovaný pro vybranou virtuální síť a podsítě, můžete ji nakonfigurovat jako součást této operace.
    >
    > V současné době se při vytváření pravidel zobrazí pro výběr pouze virtuální sítě patřící do stejného klienta služby Azure Active Directory. Chcete-li udělit přístup k podsíti ve virtuální síti patřící jinému tenantovi, použijte rozhraní API powershellu, rozhraní CLI nebo REST.

1. Chcete-li odebrat pravidlo virtuální sítě nebo podsítě, vyberte **...** a otevřete místní nabídku pro virtuální síť nebo podsíť a vyberte **Odebrat**.

   ![Odebrání virtuální sítě](media/vnet/virtual-network-remove.png)

1. Vyberte **Uložit**, aby se tyto změny použily.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Nainstalujte [Azure PowerShell](/powershell/azure/install-az-ps) a [přihlaste se](/powershell/azure/authenticate-azureps), nebo vyberte **Vyzkoušet**.

1. Seznam pravidel virtuální sítě.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).VirtualNetworkRules
    ```

1. Povolte koncový bod služby pro služby Azure Cognitive Services v existující virtuální síti a podsíti.

    ```azurepowershell-interactive
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" `
        -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" `
        -AddressPrefix "10.0.0.0/24" `
        -ServiceEndpoint "Microsoft.CognitiveServices" | Set-AzVirtualNetwork
    ```

1. Přidejte síťové pravidlo pro virtuální síť a podsíť.

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

    > [!TIP]
    > Chcete-li přidat síťové pravidlo pro podsíť ve virtuální síti patřící do jiného klienta Služby Azure AD, použijte plně kvalifikovaný parametr **VirtualNetworkResourceId** ve formuláři "/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name".

1. Odeberte síťové pravidlo pro virtuální síť a podsíť.

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Nainstalujte [azure cli](/cli/azure/install-azure-cli) a [přihlaste se](/cli/azure/authenticate-azure-cli), nebo vyberte Try **it**.

1. Seznam pravidel virtuální sítě.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" \
        --query virtualNetworkRules
    ```

1. Povolte koncový bod služby pro služby Azure Cognitive Services v existující virtuální síti a podsíti.

    ```azurecli-interactive
    az network vnet subnet update -g "myresourcegroup" -n "mysubnet" \
    --vnet-name "myvnet" --service-endpoints "Microsoft.CognitiveServices"
    ```

1. Přidejte síťové pravidlo pro virtuální síť a podsíť.

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule addition
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```

    > [!TIP]
    > Chcete-li přidat pravidlo pro podsíť ve virtuální síti patřící jinému tenantovi Služby Azure AD, použijte plně kvalifikované ID podsítě ve formuláři "/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name".
    > 
    > Parametr **předplatného** můžete použít k načtení ID podsítě pro virtuální síť patřící do jiného klienta Azure AD.

1. Odeberte síťové pravidlo pro virtuální síť a podsíť.

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule removal
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```
***

> [!IMPORTANT]
> Nezapomeňte [nastavit výchozí pravidlo](#change-the-default-network-access-rule) **pro odepření**nebo síťová pravidla nemají žádný vliv.

## <a name="grant-access-from-an-internet-ip-range"></a>Udělení přístupu z internetového rozsahu IP adres

Můžete nakonfigurovat prostředky služeb Cognitive Services tak, aby umožňovaly přístup z konkrétních rozsahů veřejných internetových IP adres. Tato konfigurace poskytuje přístup ke konkrétním službám a místním sítím, což účinně blokuje obecný internetový provoz.

Poskytněte povolené rozsahy internetových adres `16.17.18.0/24` pomocí [cidr notace](https://tools.ietf.org/html/rfc4632) ve formuláři nebo jako jednotlivé IP adresy jako `16.17.18.19`.

   > [!Tip]
   > Malé rozsahy adres používající velikosti předpony "/31" nebo "/32" nejsou podporovány. Tyto rozsahy by měly být konfigurovány pomocí jednotlivých pravidel IP adres.

Pravidla sítě IP jsou povolena pouze pro **veřejné internetové** IP adresy. Rozsahy IP adres vyhrazené pro privátní sítě (podle definice v [RFC 1918)](https://tools.ietf.org/html/rfc1918#section-3)nejsou v pravidlech IP povoleny. Privátní sítě `10.*`zahrnují `172.16.*`  -  `172.31.*`adresy, které začínají na , , a `192.168.*`.

   > [!NOTE]
   > Pravidla sítě IP nemají žádný vliv na požadavky pocházející ze stejné oblasti Azure jako prostředek služeb Cognitive Services. Použití [pravidla virtuální sítě](#grant-access-from-a-virtual-network) povolit požadavky stejné oblasti.

V současné době jsou podporovány pouze adresy IPV4. Každý prostředek služeb Cognitive Services podporuje až 100 pravidel sítě IP, která mohou být kombinována s [pravidly virtuální sítě](#grant-access-from-a-virtual-network).

### <a name="configuring-access-from-on-premises-networks"></a>Konfigurace přístupu z místních sítí

Chcete-li udělit přístup z místních sítí k prostředku služeb Cognitive Services pomocí pravidla sítě IP, je nutné identifikovat ip adresy používané v síti. Požádejte o pomoc správce sítě.

Pokud používáte [ExpressRoute](../expressroute/expressroute-introduction.md) místně pro veřejný partnerský vztah nebo partnerský vztah Microsoftu, budete muset identifikovat IP adresy NAT. Pro veřejný partnerský vztah každý okruh ExpressRoute ve výchozím nastavení používá dvě IP adresy NAT. Každý se použije pro provoz služeb Azure, když provoz vstoupí do páteřní sítě Microsoft Azure. Pro partnerský vztah společnosti Microsoft jsou používané IP adresy NAT buď poskytnuty zákazníkem, nebo jsou poskytovány poskytovatelem služeb. Pokud chcete povolit přístup k prostředkům služby, musíte tyto veřejné IP adresy povolit v nastavení IP adresy brány firewall prostředku. Pokud chcete zjistit IP adresy veřejného partnerského okruhu ExpressRoute, [otevřete lístek podpory pro ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) na webu Azure Portal. Další informace o [překladu adres (NAT) pro veřejné partnerské vztahy a partnerské vztahy s Microsoftem v ExpressRoute.](../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>Správa pravidel sítě IP

Pravidla sítě IP pro prostředky služeb Cognitive Services můžete spravovat prostřednictvím portálu Azure, PowerShellu nebo nastavení námpou v eli Azure.

# <a name="azure-portal"></a>[Portál Azure](#tab/portal)

1. Přejděte na prostředek služeb Cognitive Services, který chcete zabezpečit.

1. Vyberte nabídku **SPRÁVA PROSTŘEDKŮ** nazvanou **Virtuální síť**.

1. Zkontrolujte, zda jste zvolili povolení **přístupu**z vybraných sítí .

1. Chcete-li udělit přístup k internetovému rozsahu IP adres, zadejte ip adresu nebo rozsah adres (ve [formátu CIDR)](https://tools.ietf.org/html/rfc4632)v části**Rozsah adres**brány **firewall** > . Jsou přijímány pouze platné veřejné ip adresy (nevyhrazené).

   ![Přidat rozsah IP adres](media/vnet/virtual-network-add-ip-range.png)

1. Chcete-li odebrat pravidlo sítě <span class="docon docon-delete x-hidden-focus"></span> IP, vyberte ikonu koše vedle rozsahu adres.

   ![Odstranit rozsah IP adres](media/vnet/virtual-network-delete-ip-range.png)

1. Vyberte **Uložit**, aby se tyto změny použily.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Nainstalujte [Azure PowerShell](/powershell/azure/install-az-ps) a [přihlaste se](/powershell/azure/authenticate-azureps), nebo vyberte **Vyzkoušet**.

1. Seznam pravidel sítě IP.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).IPRules
    ```

1. Přidejte síťové pravidlo pro jednotlivé adresy IP.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Přidejte síťové pravidlo pro rozsah adres IP.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Odeberte síťové pravidlo pro jednotlivé adresy IP.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Odeberte síťové pravidlo pro rozsah adres IP.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Nainstalujte [azure cli](/cli/azure/install-azure-cli) a [přihlaste se](/cli/azure/authenticate-azure-cli), nebo vyberte Try **it**.

1. Seznam pravidel sítě IP.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" --query ipRules
    ```

1. Přidejte síťové pravidlo pro jednotlivé adresy IP.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. Přidejte síťové pravidlo pro rozsah adres IP.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

1. Odeberte síťové pravidlo pro jednotlivé adresy IP.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. Odeberte síťové pravidlo pro rozsah adres IP.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

***

> [!IMPORTANT]
> Nezapomeňte [nastavit výchozí pravidlo](#change-the-default-network-access-rule) **pro odepření**nebo síťová pravidla nemají žádný vliv.

## <a name="next-steps"></a>Další kroky

* Prozkoumejte různé [služby Azure Cognitive Services](welcome.md)
* Další informace o [koncových bodech služby Azure Virtual Network Service](../virtual-network/virtual-network-service-endpoints-overview.md)