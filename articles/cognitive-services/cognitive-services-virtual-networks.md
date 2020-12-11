---
title: Virtuální sítě
titleSuffix: Azure Cognitive Services
description: Nakonfigurujte vícevrstvé zabezpečení sítě pro prostředky Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: aahi
ms.openlocfilehash: 3b6c2a5a50cedadd8818eae735df55b661e794ef
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97034016"
---
# <a name="configure-azure-cognitive-services-virtual-networks"></a>Konfigurace virtuálních sítí služeb Azure Cognitive Services

Azure Cognitive Services poskytuje vrstvený model zabezpečení. Tento model umožňuje zabezpečit účty služeb Cognitive Services pro konkrétní podmnožinu sítí. Když jsou nakonfigurovaná pravidla sítě, můžou k účtu přistupovat pouze aplikace, které žádají o data přes zadanou sadu sítí. Přístup k prostředkům můžete omezit pomocí filtrování požadavků. Povoluje se jenom požadavky pocházející ze zadaných IP adres, rozsahů IP adres nebo ze seznamu podsítí ve [virtuálních sítích Azure](../virtual-network/virtual-networks-overview.md).

Aplikace, která přistupuje k prostředku Cognitive Services, když jsou platná síťová pravidla, vyžaduje autorizaci. Autorizace se podporuje s přihlašovacími údaji služby [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) nebo s platným klíčem rozhraní API.

> [!IMPORTANT]
> Když zapnete pravidla brány firewall pro účet Cognitive Services, ve výchozím nastavení se zablokuje příchozí požadavky na data. Aby bylo možné podávat požadavky, je nutné splnit jednu z následujících podmínek:

> * Požadavek by měl pocházet ze služby provozované v rámci Azure Virtual Network (VNet) na seznamu povolených podsítí cílového Cognitive Services účtu. Koncový bod v žádostech pocházejících z virtuální sítě je potřeba nastavit jako [vlastní subdoménu](cognitive-services-custom-subdomains.md) účtu Cognitive Services.
> * Nebo požadavek by měl pocházet z povoleného seznamu IP adres.
>
> Blokované požadavky zahrnují ty z jiných služeb Azure, od Azure Portal, ze služeb protokolování a metriky atd.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Scénáře

Chcete-li zabezpečit prostředek Cognitive Services, je třeba nejprve nakonfigurovat pravidlo pro odepření přístupu k provozu ze všech sítí (včetně internetového provozu) ve výchozím nastavení. Pak byste měli nakonfigurovat pravidla, která udělí přístup k provozu z konkrétních virtuální sítě. Tato konfigurace umožňuje vytvořit zabezpečenou hranici sítě pro vaše aplikace. Můžete taky nakonfigurovat pravidla pro udělení přístupu k provozu z výběru rozsahů veřejných IP adres, které umožňují připojení z určitých internetových nebo místních klientů.

Síťová pravidla se vynutila na všech síťových protokolech do Azure Cognitive Services, včetně REST a WebSocket. Chcete-li získat přístup k datům pomocí nástrojů, jako jsou například konzole Azure test, je nutné nakonfigurovat explicitní Síťová pravidla. Můžete použít Síťová pravidla pro existující prostředky Cognitive Services, nebo při vytváření nových prostředků Cognitive Services. Po použití pravidel sítě se vynutily pro všechny požadavky.

## <a name="supported-regions-and-service-offerings"></a>Podporované oblasti a nabídky služeb

Virtuální sítě (virtuální sítě) jsou podporované v [oblastech, kde jsou k dispozici Cognitive Services](https://azure.microsoft.com/global-infrastructure/services/). Cognitive Services podporuje značky služby pro konfiguraci pravidel sítě. Níže uvedené služby jsou součástí značky služby **CognitiveServicesManagement** .

> [!div class="checklist"]
> * Detektor anomálií
> * Počítačové zpracování obrazu
> * Content Moderator
> * Custom Vision
> * Tvář
> * Rozpoznávání formulářů
> * Asistivní čtečka
> * Language Understanding (LUIS)
> * Personalizace
> * Hlasové služby
> * Analýza textu
> * QnA Maker
> * Translator Text


> [!NOTE]
> Pokud používáte LUIS, značka **CognitiveServicesManagement** vám umožňuje používat jenom tuto službu pomocí sady SDK nebo REST API. Pokud chcete získat přístup k portálu LUIS z virtuální sítě a používat ho, budete muset použít následující značky:  
> * **Azureactivedirectory selhala**
> * **AzureFrontDoor. front-end**
> * **AzureResourceManager** 
> * **CognitiveServicesManagement**



## <a name="change-the-default-network-access-rule"></a>Změna výchozího pravidla přístupu k síti

Ve výchozím nastavení Cognitive Services prostředky přijímají připojení z klientů v jakékoli síti. Chcete-li omezit přístup na vybrané sítě, musíte nejdřív změnit výchozí akci.

> [!WARNING]
> Změny síťových pravidel můžou mít vliv na schopnost vaší aplikace připojit se k Azure Cognitive Services. Nastavením výchozího síťového pravidla na **Odepřít** znemožníte přístup k datům, pokud nejsou použita specifická Síťová pravidla, která **udělují** přístup. Než změníte výchozí pravidlo a odepřete přístup, nezapomeňte prostřednictvím pravidel sítě udělit přístup všem povoleným sítím. Pokud povolujete výpis IP adres pro místní síť, nezapomeňte přidat všechny možné odchozí veřejné IP adresy z místní sítě.

### <a name="managing-default-network-access-rules"></a>Správa výchozích pravidel síťového přístupu

Pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure můžete spravovat výchozí pravidla přístupu k síti pro Cognitive Services prostředky.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. Přejít na prostředek Cognitive Services, který chcete zabezpečit.

1. Vyberte nabídku **Správa prostředků** s názvem **virtuální síť**.

   ![Možnost virtuální sítě](media/vnet/virtual-network-blade.png)

1. Chcete-li odepřít přístup ve výchozím nastavení, vyberte možnost povolit přístup z **vybraných sítí**. Když jsou samostatně **Vybraná nastavení sítě** , která nejsou doprovázena nakonfigurovanými **virtuálními sítěmi** nebo **rozsahy adres** – veškerý přístup je efektivně odepřen. Po odepření přístupu se požadavky, které se pokoušejí o využívání prostředku Cognitive Services, nepovolují. Azure Portal, Azure PowerShell nebo, se ke konfiguraci Cognitive Services prostředků dají dál použít Azure CLI.
1. Pokud chcete povolit přenos ze všech sítí, zvolte povolení přístupu ze **všech sítí**.

   ![Virtuální sítě – zamítnutí](media/vnet/virtual-network-deny.png)

1. Vyberte **Uložit**, aby se tyto změny použily.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Nainstalujte [Azure PowerShell](/powershell/azure/install-az-ps) a [přihlaste](/powershell/azure/authenticate-azureps)se, nebo vyberte **vyzkoušet**.

1. Zobrazí stav výchozího pravidla pro prostředek Cognitive Services.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).DefaultAction
    ```

1. Nastavte výchozí pravidlo na odepřít přístup k síti ve výchozím nastavení.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Deny
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

1. Nastavte výchozí pravidlo tak, aby povolovalo přístup k síti ve výchozím nastavení.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Allow
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Nainstalujte rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) a [přihlaste](/cli/azure/authenticate-azure-cli)se nebo vyberte **vyzkoušet**.

1. Zobrazí stav výchozího pravidla pro prostředek Cognitive Services.

    ```azurecli-interactive
    az cognitiveservices account show \
        -g "myresourcegroup" -n "myaccount" \
        --query networkRuleSet.defaultAction
    ```

1. Nastavte výchozí pravidlo na odepřít přístup k síti ve výchozím nastavení.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Deny
    ```

1. Nastavte výchozí pravidlo tak, aby povolovalo přístup k síti ve výchozím nastavení.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Allow
    ```

**_

## <a name="grant-access-from-a-virtual-network"></a>Udělení přístupu z virtuální sítě

Cognitive Services prostředky můžete nakonfigurovat tak, aby povolovaly přístup pouze ze specifických podsítí. Povolené podsítě můžou patřit do virtuální sítě ve stejném předplatném nebo v jiném předplatném, včetně předplatných, která patří do jiného tenanta Azure Active Directory.

Povolte [koncový bod služby](../virtual-network/virtual-network-service-endpoints-overview.md) pro Azure Cognitive Services v rámci virtuální sítě. Koncový bod služby směruje provoz z virtuální sítě prostřednictvím optimální cesty ke službě Azure Cognitive Services. Identity podsítě a virtuální sítě se také odesílají s každým požadavkem. Správci potom můžou nakonfigurovat Síťová pravidla pro prostředek Cognitive Services, který povoluje příjem požadavků ze specifických podsítí ve virtuální síti. Klienti s uděleným přístupem přes tato Síťová pravidla musí nadále splňovat požadavky na autorizaci prostředku Cognitive Services pro přístup k datům.

Každý prostředek Cognitive Services podporuje až 100 pravidel virtuální sítě, která se můžou kombinovat s [pravidly sítě IP](#grant-access-from-an-internet-ip-range).

### <a name="required-permissions"></a>Požadovaná oprávnění

Chcete-li použít pravidlo virtuální sítě na prostředek Cognitive Services, musí mít uživatel příslušná oprávnění pro přidávané podsítě. Požadovaná oprávnění je výchozí role _Contributor * nebo role *přispěvatele Cognitive Services* . Pro vlastní definice rolí je možné přidat také požadovaná oprávnění.

Cognitive Services prostředku a povolený přístup k virtuálním sítím může být v různých předplatných, včetně předplatných, která jsou součástí jiného tenanta Azure AD.

> [!NOTE]
> Konfigurace pravidel, která udělují přístup k podsítím ve virtuálních sítích, které jsou součástí jiného tenanta Azure Active Directory, se v současné době podporují jenom prostřednictvím PowerShellu, CLI a rozhraní REST API. Taková pravidla nelze konfigurovat prostřednictvím Azure Portal, i když je lze zobrazit na portálu.

### <a name="managing-virtual-network-rules"></a>Správa pravidel virtuální sítě

Pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure můžete spravovat pravidla virtuální sítě pro prostředky Cognitive Services.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. Přejít na prostředek Cognitive Services, který chcete zabezpečit.

1. Vyberte nabídku **Správa prostředků** s názvem **virtuální síť**.

1. Ověřte, že jste vybrali povolení přístupu z **vybraných sítí**.

1. Chcete-li udělit přístup k virtuální síti s existujícím pravidlem sítě, vyberte v části **virtuální sítě** možnost **Přidat existující virtuální síť**.

   ![Přidat existující virtuální síť](media/vnet/virtual-network-add-existing.png)

1. Vyberte možnosti **virtuální sítě** a **podsítě** a pak vyberte **Povolit**.

   ![Přidat existující podrobnosti virtuální sítě](media/vnet/virtual-network-add-existing-details.png)

1. Pokud chcete vytvořit novou virtuální síť a udělit jí přístup, vyberte **Přidat novou virtuální síť**.

   ![Přidat novou virtuální síť](media/vnet/virtual-network-add-new.png)

1. Zadejte informace potřebné k vytvoření nové virtuální sítě a pak vyberte **vytvořit**.

   ![Vytvořit virtuální síť](media/vnet/virtual-network-create.png)

    > [!NOTE]
    > Pokud koncový bod služby pro Azure Cognitive Services nebyl dřív nakonfigurovaný pro vybranou virtuální síť a podsítě, můžete ho nakonfigurovat jako součást této operace.
    >
    > V současné době se při vytváření pravidel zobrazují jenom virtuální sítě patřící do stejného Azure Active Directory tenanta. Pokud chcete udělit přístup k podsíti ve virtuální síti patřící jinému tenantovi, použijte PowerShell, rozhraní příkazového řádku nebo rozhraní REST API.

1. Chcete-li odebrat virtuální síť nebo pravidlo podsítě, vyberte **...** a otevřete tak kontextovou nabídku pro virtuální síť nebo podsíť a vyberte možnost **Odebrat**.

   ![Odebrat virtuální síť](media/vnet/virtual-network-remove.png)

1. Vyberte **Uložit**, aby se tyto změny použily.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Nainstalujte [Azure PowerShell](/powershell/azure/install-az-ps) a [přihlaste](/powershell/azure/authenticate-azureps)se, nebo vyberte **vyzkoušet**.

1. Vypíše pravidla virtuální sítě.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).VirtualNetworkRules
    ```

1. Povolte koncový bod služby pro Azure Cognitive Services v existující virtuální síti a podsíti.

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
    > Pokud chcete přidat síťové pravidlo pro podsíť ve virtuální síti, která patří do jiného tenanta Azure AD, použijte plně kvalifikovaný parametr **VirtualNetworkResourceId** ve formátu "/Subscriptions/Subscription-ID/resourceGroups/resourceGroup-Name/Providers/Microsoft.Network/virtualNetworks/vNet-Name/subnets/Subnet-Name".

1. Odeberte pravidlo sítě pro virtuální síť a podsíť.

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

1. Nainstalujte rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) a [přihlaste](/cli/azure/authenticate-azure-cli)se nebo vyberte **vyzkoušet**.

1. Vypíše pravidla virtuální sítě.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" \
        --query virtualNetworkRules
    ```

1. Povolte koncový bod služby pro Azure Cognitive Services v existující virtuální síti a podsíti.

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
    > Pokud chcete přidat pravidlo pro podsíť ve virtuální síti, která patří do jiného tenanta Azure AD, použijte plně kvalifikované ID podsítě ve formátu "/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name".
    > 
    > Pomocí parametru **Subscription** můžete načíst ID podsítě pro virtuální síť patřící jinému Tenantovi služby Azure AD.

1. Odeberte pravidlo sítě pro virtuální síť a podsíť.

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule removal
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```

**_

> [!IMPORTANT]
> Ujistěte se, že jste [výchozí pravidlo nastavili](#change-the-default-network-access-rule) na _ * odepřít * * nebo že Síťová pravidla nemají žádný vliv.

## <a name="grant-access-from-an-internet-ip-range"></a>Udělení přístupu z rozsahu internetových IP adres

Cognitive Services prostředků můžete nakonfigurovat tak, aby povolovaly přístup z určitých rozsahů veřejných internetových IP adres. Tato konfigurace uděluje přístup ke konkrétním službám a místním sítím a efektivně tak blokuje obecný internetový provoz.

Zadejte povolené rozsahy internetových adres pomocí [zápisu CIDR](https://tools.ietf.org/html/rfc4632) ve formuláři `16.17.18.0/24` nebo jako jednotlivé IP adresy jako `16.17.18.19` .

   > [!Tip]
   > Malé rozsahy adres používající velikosti předpony "/31" nebo "/32" se nepodporují. Tyto rozsahy by měly být nakonfigurované pomocí jednotlivých pravidel IP adres.

Pravidla sítě IP jsou povolená jenom pro **veřejné internetové** IP adresy. Rozsahy IP adres rezervované pro privátní sítě (definované v [dokumentu RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)) nejsou povolené v pravidlech protokolu IP. Soukromé sítě obsahují adresy, které začínají `10.*` na, `172.16.*`  -  `172.31.*` a `192.168.*` .

   > [!NOTE]
   > Pravidla sítě IP neovlivňují požadavky pocházející ze stejné oblasti Azure jako prostředek Cognitive Services. Použijte [pravidla virtuální sítě](#grant-access-from-a-virtual-network) a povolte tak požadavky stejné oblasti.

V tuto chvíli se podporují jenom IPV4 adresy. Každý prostředek Cognitive Services podporuje až 100 pravidel sítě IP, která se můžou kombinovat s [pravidly virtuální sítě](#grant-access-from-a-virtual-network).

### <a name="configuring-access-from-on-premises-networks"></a>Konfigurace přístupu z místních sítí

Pokud chcete udělit přístup z vašich místních sítí k vašemu Cognitive Services prostředkům pomocí pravidla sítě IP, musíte identifikovat internetové IP adresy, které používá vaše síť. Požádejte o nápovědu správce sítě.

Pokud používáte místní [ExpressRoute](../expressroute/expressroute-introduction.md) pro veřejné partnerské vztahy nebo partnerské vztahy Microsoftu, budete muset identifikovat IP adresy překladu adres (NAT). U veřejných partnerských vztahů každý okruh ExpressRoute ve výchozím nastavení používá dvě IP adresy NAT. Každá z nich se použije na provoz služeb Azure, když přenos vstoupí do sítě Microsoft Azure páteřní síť. V případě partnerského vztahu Microsoftu se používají IP adresy NAT buď poskytnuté zákazníkem, nebo poskytovatel služeb. Pokud chcete povolit přístup k prostředkům služby, musíte tyto veřejné IP adresy povolit v nastavení IP adresy brány firewall prostředku. Pokud chcete zjistit IP adresy veřejného partnerského okruhu ExpressRoute, [otevřete lístek podpory pro ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) na webu Azure Portal. Další informace o [překladu adres (NAT) pro veřejné partnerské vztahy a partnerské vztahy s Microsoftem v ExpressRoute.](../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>Správa pravidel sítě IP

Pravidla sítě IP pro Cognitive Services prostředky můžete spravovat pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. Přejít na prostředek Cognitive Services, který chcete zabezpečit.

1. Vyberte nabídku **Správa prostředků** s názvem **virtuální síť**.

1. Ověřte, že jste vybrali povolení přístupu z **vybraných sítí**.

1. Pokud chcete udělit přístup k rozsahu IP adres Internetu, zadejte v části rozsah adres **firewallu** IP adresu nebo rozsah adres (ve [formátu CIDR](https://tools.ietf.org/html/rfc4632))  >  . Jsou přijímány pouze platné veřejné IP adresy (nerezervované).

   ![Přidat rozsah IP adres](media/vnet/virtual-network-add-ip-range.png)

1. Chcete-li odebrat pravidlo sítě protokolu IP, vyberte ikonu odpadkového koše <span class="docon docon-delete x-hidden-focus"></span> vedle rozsahu adres.

   ![Odstranit rozsah IP adres](media/vnet/virtual-network-delete-ip-range.png)

1. Vyberte **Uložit**, aby se tyto změny použily.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Nainstalujte [Azure PowerShell](/powershell/azure/install-az-ps) a [přihlaste](/powershell/azure/authenticate-azureps)se, nebo vyberte **vyzkoušet**.

1. Vypíše pravidla sítě protokolu IP.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).IPRules
    ```

1. Přidejte síťové pravidlo pro jednotlivé IP adresy.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Přidejte síťové pravidlo pro rozsah IP adres.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Odeberte pravidlo sítě pro jednotlivé IP adresy.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Odebere síťové pravidlo pro rozsah IP adres.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Nainstalujte rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) a [přihlaste](/cli/azure/authenticate-azure-cli)se nebo vyberte **vyzkoušet**.

1. Vypíše pravidla sítě protokolu IP.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" --query ipRules
    ```

1. Přidejte síťové pravidlo pro jednotlivé IP adresy.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. Přidejte síťové pravidlo pro rozsah IP adres.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

1. Odeberte pravidlo sítě pro jednotlivé IP adresy.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. Odebere síťové pravidlo pro rozsah IP adres.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

**_

> [!IMPORTANT]
> Ujistěte se, že jste [výchozí pravidlo nastavili](#change-the-default-network-access-rule) na _ * odepřít * * nebo že Síťová pravidla nemají žádný vliv.

## <a name="use-private-endpoints"></a>Použití privátních koncových bodů

Pro prostředky Cognitive Services můžete použít [soukromé koncové body](../private-link/private-endpoint-overview.md) , které umožní klientům ve virtuální síti (VNET) zabezpečený přístup k datům prostřednictvím [privátního propojení](../private-link/private-link-overview.md). Privátní koncový bod používá IP adresu z adresního prostoru virtuální sítě pro prostředek Cognitive Services. Síťový provoz mezi klienty ve virtuální síti a prostředkem projde virtuální síť a privátní odkaz na páteřní síti Microsoftu a odstraní tak expozici veřejného Internetu.

Soukromé koncové body pro Cognitive Services prostředky vám umožní:

* Zabezpečte Cognitive Services prostředek tak, že nakonfigurujete bránu firewall tak, aby blokovala všechna připojení na veřejném koncovém bodu služby Cognitive Services.
* Zvyšte zabezpečení virtuální sítě tím, že vám umožníte blokovat exfiltrace dat z virtuální sítě.
* Bezpečně se připojte k Cognitive Services prostředkům z místních sítí, které se připojují k virtuální síti pomocí [sítě VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) nebo [ExpressRoutes](../expressroute/expressroute-locations.md) s privátním partnerským vztahem.

### <a name="conceptual-overview"></a>Základní přehled

Privátní koncový bod je speciální síťové rozhraní pro prostředek Azure ve vaší [virtuální](../virtual-network/virtual-networks-overview.md)síti. Vytvoření privátního koncového bodu pro prostředek Cognitive Services zajišťuje zabezpečené připojení mezi klienty ve vaší virtuální síti a vaším prostředkem. Privátnímu koncovému bodu je přiřazena IP adresa z rozsahu IP adres vaší virtuální sítě. Připojení mezi soukromým koncovým bodem a službou Cognitive Services používá zabezpečený privátní odkaz.

Aplikace ve virtuální síti se můžou bez problémů připojit ke službě přes soukromý koncový bod pomocí stejných připojovacích řetězců a mechanismů autorizace, které by jinak používaly. Výjimkou jsou hlasové služby, které vyžadují samostatný koncový bod. Viz část [soukromých koncových bodů se službami Speech](#private-endpoints-with-the-speech-services). Soukromé koncové body lze použít se všemi protokoly podporovanými prostředkem Cognitive Services, včetně REST.

V podsítích, které používají [koncové body služby](../virtual-network/virtual-network-service-endpoints-overview.md), lze vytvořit privátní koncové body. Klienti v podsíti se mohou připojit k jednomu Cognitive Services prostředku pomocí privátního koncového bodu a při použití koncových bodů služby k přístupu k ostatním.

Když vytvoříte privátní koncový bod pro prostředek Cognitive Services ve vaší virtuální síti, pošle se žádost o souhlas ke schválení vlastníkem prostředku Cognitive Services. Pokud uživatel žádající o vytvoření privátního koncového bodu je zároveň vlastníkem prostředku, je tato žádost o souhlas automaticky schválena.

Cognitive Services vlastníci prostředků mohou spravovat žádosti o souhlas a soukromé koncové body prostřednictvím karty *privátní koncové body* pro prostředek Cognitive Services v [Azure Portal](https://portal.azure.com).

### <a name="private-endpoints"></a>Soukromé koncové body

Při vytváření privátního koncového bodu je nutné zadat prostředek Cognitive Services, ke kterému se připojuje. Další informace o vytváření privátního koncového bodu najdete v těchto tématech:

* [Vytvoření privátního koncového bodu pomocí centra privátního spojení v Azure Portal](../private-link/create-private-endpoint-portal.md)
* [Vytvoření privátního koncového bodu pomocí Azure CLI](../private-link/create-private-endpoint-cli.md)
* [Vytvoření privátního koncového bodu pomocí Azure PowerShell](../private-link/create-private-endpoint-powershell.md)

### <a name="connecting-to-private-endpoints"></a>Připojování k soukromým koncovým bodům

Klienti ve virtuální síti s použitím privátního koncového bodu by měli použít stejný připojovací řetězec pro prostředek Cognitive Services jako klienti připojující se ke veřejnému koncovému bodu. Výjimkou jsou hlasové služby, které vyžadují samostatný koncový bod. Viz část [soukromých koncových bodů se službami Speech](#private-endpoints-with-the-speech-services). Pro automatické směrování připojení z virtuální sítě do Cognitive Services prostředku prostřednictvím privátního propojení spoléháme na překlad DNS. 

Ve výchozím nastavení vytvoříme [privátní ZÓNU DNS](../dns/private-dns-overview.md) připojenou k virtuální síti s nezbytnými aktualizacemi pro privátní koncové body. Pokud ale používáte vlastní server DNS, možná budete muset provést další změny v konfiguraci DNS. Oddíl o [změnách DNS](#dns-changes-for-private-endpoints) níže popisuje aktualizace vyžadované pro soukromé koncové body.

### <a name="private-endpoints-with-the-speech-services"></a>Soukromé koncové body se službami Speech

Podívejte [se na téma používání služby Speech Services s privátními koncovými body poskytované privátním odkazem Azure](Speech-Service/speech-services-private-link.md).

### <a name="dns-changes-for-private-endpoints"></a>Změny DNS u privátních koncových bodů

Při vytváření privátního koncového bodu se záznam prostředku CNAME DNS pro prostředek Cognitive Services aktualizuje na alias v subdoméně s předponou '*privatelink*'. Ve výchozím nastavení vytvoříme také [privátní ZÓNU DNS](../dns/private-dns-overview.md), která odpovídá subdoméně *privatelink*, a záznamy prostředků DNS pro privátní koncové body.

Při překladu adresy URL koncového bodu mimo virtuální síť s privátním koncovým bodem se přeloží na veřejný koncový bod prostředku Cognitive Services. Při vyřešení z virtuální sítě hostující soukromý koncový bod adresa URL koncového bodu se přeloží na IP adresu privátního koncového bodu.

Tento přístup umožňuje přístup k prostředku Cognitive Services pomocí stejného připojovacího řetězce pro klienty ve virtuální síti hostující privátní koncové body a klienty mimo virtuální síť.

Pokud ve vaší síti používáte vlastní server DNS, klienti musí být schopni přeložit plně kvalifikovaný název domény (FQDN) pro koncový bod prostředku Cognitive Services na IP adresu privátního koncového bodu. Nakonfigurujte server DNS tak, aby delegoval subdoménu privátního propojení s privátní zónou DNS pro virtuální síť.

> [!TIP]
> Pokud používáte vlastní nebo místní server DNS, měli byste server DNS nakonfigurovat tak, aby přeložil Cognitive Services název prostředku v subdoméně ' privatelink ' na IP adresu privátního koncového bodu. Můžete to udělat tak, že delegujete subdoménu privatelink do privátní zóny DNS virtuální sítě nebo nakonfigurujete zónu DNS na serveru DNS a přidáte záznamy DNS a.

Další informace o konfiguraci vlastního serveru DNS pro podporu privátních koncových bodů najdete v následujících článcích:

* [Překlad názvů pro prostředky ve virtuálních sítích Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
* [Konfigurace DNS pro privátní koncové body](../private-link/private-endpoint-overview.md#dns-configuration)

### <a name="pricing"></a>Ceny

Podrobnosti o cenách najdete v tématu [ceny za privátní propojení Azure](https://azure.microsoft.com/pricing/details/private-link).

## <a name="next-steps"></a>Další kroky

* Prozkoumejte různé [Cognitive Services Azure](./what-are-cognitive-services.md)
* Další informace o [koncových bodech služby Azure Virtual Network](../virtual-network/virtual-network-service-endpoints-overview.md)