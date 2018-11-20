---
title: Konfigurace virtuálních sítí a bran firewall Azure Storage | Dokumentace Microsoftu
description: Konfigurace zabezpečení vrstvami sítě pro váš účet úložiště.
services: storage
author: cbrooksmsft
ms.service: storage
ms.topic: article
ms.date: 10/30/2018
ms.author: cbrooks
ms.component: common
ms.openlocfilehash: f9f38b11aa333eed9c5f524cc5c6c9e727fede15
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2018
ms.locfileid: "52164893"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Konfigurace virtuálních sítí a bran firewall Azure Storage

Azure Storage poskytuje vrstvený model zabezpečení. Tento model umožňuje zabezpečit vaše účty úložiště s konkrétní sadou podporované sítí. Když jsou nakonfigurovaná pravidla sítě, pouze aplikace vyžadující data z více než zadaných sítí můžou k účtu úložiště.

Aplikace, který přistupuje k účtu úložiště, když pravidla sítě jsou aktivní, vyžaduje řádné autorizace v požadavku. Autorizace je podporováno pomocí přihlašovacích údajů Azure Active Directory (AD) (pro objekty BLOB a fronty) (preview), platným účtem přístupový klíč nebo SAS token.

> [!IMPORTANT]
> Ve výchozím nastavení, zapnutí pravidla brány firewall pro váš účet úložiště blokuje příchozí požadavky na data, není-li, požadavky pocházejí z služba, která je zpracovávána v rámci služby Azure Virtual Network (VNet). Požadavky, které jsou blokovány mezi patří zařízení se od ostatních služeb Azure z webu Azure portal, protokolování a metrik služby a tak dále.
>
> Můžete udělit přístup ke službám Azure, které pracují z v rámci virtuální sítě tím, že podsíť bude instance služby. Povolit omezený počet scénářů prostřednictvím [výjimky](#exceptions) mechanismus je popsáno v následující části. Pro přístup k webu Azure portal, musíte být na počítači v rámci důvěryhodné hranice (IP nebo virtuální síť), které jste nastavili.

## <a name="scenarios"></a>Scénáře

Ve výchozím nastavení konfigurace účtů úložiště k odepření přístupu k provozu ze všech sítí (včetně přenosů z Internetu). Pak udělují přístup na provoz z konkrétní virtuálních sítí. Tato konfigurace umožňuje vytvořit zabezpečené ohraničení sítě pro vaše aplikace. Můžete také udělit přístup k veřejnému Internetu rozsahy IP adres, povolení připojení z konkrétní Internetu nebo místních klientů.

Na všech síťových protokolů do úložiště Azure, včetně REST a protokolu SMB se vynucují pravidla sítě. Pro přístup k datům pomocí nástrojů, jako je Azure portal, Průzkumníka služby Storage a AZCopy, se vyžadují explicitní pravidla.

Pravidla sítě můžete použít pro existující účty úložiště nebo vytvořit nové účty úložiště.

Po použití pravidla sítě jsou, se vynutí pro všechny požadavky. Tokeny SAS, které udělují přístup na konkrétní IP adresu slouží k omezení přístupu k tokenu držitele, ale není udělovat nový přístup mimo síť nakonfigurovaná pravidla.

Diskové přenosy virtuálního počítače (včetně připojení a odpojení operace a vstupně-výstupních operací disku) nemá vliv pravidla sítě. Přístup REST k objektům BLOB stránky je chráněn pravidel sítě.

Klasických účtů úložiště nepodporují virtuální sítí a bran firewall.

Nespravované disky v účtech úložiště můžete použít s pravidly sítě použít pro zálohování a obnovení virtuálních počítačů tím, že vytvoříte výjimku. Tento proces je popsána v [výjimky](#exceptions) části tohoto článku. Výjimky brány firewall nejsou použitelné se spravovanými disky se už spravuje Azure.

## <a name="change-the-default-network-access-rule"></a>Změnit výchozí pravidlo přístupu sítě

Účty úložiště ve výchozím nastavení, přijímat připojení z klientů v síti. Chcete-li omezit přístup k vybrané sítě, musíte nejdřív změnit výchozí akci.

> [!WARNING]
> Provádění změn pravidel sítě může mít vliv na vaše aplikace možnost připojení k Azure Storage. Nastavit výchozí pravidlo sítě **Odepřít** blokuje všechna přistupovat k datům, není-li konkrétní síťové pravidla pro **udělit** přístupu jsou použita také. Je potřeba udělit přístup k žádné povolené sítě pomocí pravidel pro sítě, než změníte výchozí pravidlo pro odepření přístupu.

### <a name="managing-default-network-access-rules"></a>Správa výchozího pravidla přístupu k síti

Můžete spravovat výchozí pravidla přístupu k síti pro účty úložiště na webu Azure portal, Powershellu nebo CLIv2.

#### <a name="azure-portal"></a>portál Azure

1. Přejděte na účet úložiště, kterou chcete zabezpečit.

1. Klikněte v nabídce nastavení nazývá **virtuální sítí a bran firewall**.

1. Ve výchozím nastavení odepření přístupu, zvolit povolení přístupu z **vybrané sítě**. Povolit přenos ze všech sítí, zvolit povolení přístupu z **všechny sítě**.

1. Klikněte na tlačítko **Uložit** změny.

#### <a name="powershell"></a>PowerShell

1. Nainstalujte [prostředí Azure PowerShell](/powershell/azure/install-azurerm-ps) a [přihlášení](/powershell/azure/authenticate-azureps).

1. Zobrazí stav výchozí pravidlo pro účet úložiště.

    ```PowerShell
    (Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

1. Nastavte výchozí pravidlo pro odepření přístupu k síti ve výchozím nastavení.

    ```PowerShell
    Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

1. Nastavte výchozí pravidlo, kterým povolíte přístup k síti ve výchozím nastavení.

    ```PowerShell
    Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
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

Můžete nakonfigurovat účty úložiště pro povolení přístupu jenom z konkrétních virtuálních sítí.

Povolit [koncový bod služby](/azure/virtual-network/virtual-network-service-endpoints-overview) pro službu Azure Storage v rámci virtuální sítě. Tento koncový bod poskytuje optimální směrování provozu do služby Azure Storage. Identity virtuální síť a podsíť také přenášejí spolu s každou žádostí. Správci pak můžete nakonfigurovat pravidla sítě pro účet úložiště, které umožňují požadavků bude moct přijmout z konkrétních podsítí ve virtuální síti. Klienti udělen přístup přes tyto pravidel sítě musí i dál splňuje požadavky na povolení účtu úložiště pro přístup k datům.

Každý účet úložiště podporuje až 100 pravidel virtuální sítě, které mohou být spojeny s [pravidel sítě IP](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Oblasti k dispozici virtuální sítě

Obecně platí koncové body služby práci mezi virtuálními sítěmi a instance služby ve stejné oblasti Azure. Při používání koncových bodů služby se službou Azure Storage, tento obor roste zahrnout [spárované oblasti](/azure/best-practices-availability-paired-regions). Koncové body služby povolit kontinuity podnikových procesů během regionální převzetí služeb při selhání a přístup k instancím jen pro čtení (RA-GRS) geograficky redundantní úložiště. Pravidla sítě, které udělují přístup z virtuální sítě do účtu úložiště také udělit přístup na jakoukoli instanci RA-GRS.

Při plánování zotavení po havárii během oblastního výpadku, měli byste vytvořit virtuální sítě předem v párované oblasti. Povolení koncových bodů služby pro službu Azure Storage, pomocí pravidel sítě udělení přístupu z těchto alternativních virtuálních sítí. Pak použijte tato pravidla pro účty geograficky redundantní úložiště.

> [!NOTE]
> Koncové body služby se nevztahují na přenosy mimo oblast virtuální sítě a pár určené oblasti. Lze použít pouze pravidel sítě udělení přístupu z virtuální sítě do účtů úložiště v primární oblasti účtu úložiště nebo v určené spárované oblasti.

### <a name="required-permissions"></a>Požadovaná oprávnění

Použít pravidlo virtuální sítě pro účet úložiště, musí mít uživatel příslušná oprávnění pro přidávané podsítě. Je nutná oprávnění *připojit ke službě Service k podsíti* a je součástí *Přispěvatel účtů úložiště* předdefinovaná role. Mohou být přidány také do vlastní definice rolí.

Účet úložiště a virtuální sítí udělen přístup může být v různých předplatných, ale tato předplatná musí být součástí stejného tenanta služby Azure AD.

### <a name="managing-virtual-network-rules"></a>Správa pravidel virtuální sítě

Můžete spravovat pravidla virtuální sítě pro účty úložiště pomocí webu Azure portal, Powershellu nebo CLIv2.

#### <a name="azure-portal"></a>portál Azure

1. Přejděte na účet úložiště, kterou chcete zabezpečit.

1. Klikněte v nabídce nastavení nazývá **virtuální sítí a bran firewall**.

1. Zkontrolujte, jestli jste vybrali k povolení přístupu z **vybrané sítě**.

1. Udělit přístup k virtuální síti s nové pravidlo sítě v části **virtuální sítě**, klikněte na tlačítko **přidat existující virtuální síť**vyberte **virtuální sítě** a **Podsítě** možnosti a pak klikněte na tlačítko **přidat**. Pokud chcete vytvořit novou virtuální síť a jí udělit přístup, klikněte na tlačítko **přidat novou virtuální síť**. Zadejte informace potřebné k vytvoření nové virtuální sítě a potom klikněte na **vytvořit**.

    > [!NOTE]
    > Pokud koncový bod služby pro službu Azure Storage se dříve nakonfigurované pro vybranou virtuální síť a podsítě, musíte ho nakonfigurovat jako součást této operace.

1. Pokud chcete odstranit virtuální síť nebo podsítě pravidlo, klikněte na tlačítko **...**  otevřete kontextovou nabídku pro virtuální síť nebo podsíť, a klikněte na **odebrat**.

1. Klikněte na tlačítko **Uložit** změny.

#### <a name="powershell"></a>PowerShell

1. Nainstalujte [prostředí Azure PowerShell](/powershell/azure/install-azurerm-ps) a [přihlášení](/powershell/azure/authenticate-azureps).

1. Seznam pravidel virtuální sítě.

    ```PowerShell
    (Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

1. Povolte koncový bod služby pro službu Azure Storage na existující virtuální síť a podsíť.

    ```PowerShell
    Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzureRmVirtualNetwork
    ```

1. Přidáte pravidlo pro sítě pro virtuální síť a podsíť.

    ```PowerShell
    $subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzureRmStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

1. Odeberte pravidlo pro sítě pro virtuální síť a podsíť.

    ```PowerShell
    $subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzureRmStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
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

Pravidla síťových IP jsou povolené jenom pro **veřejného Internetu** IP adresy. IP adresy rozsahů, které jsou vyhrazené pro privátní sítě (jak je definováno v [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)) nejsou povoleny v pravidlech IP. Privátní sítě zahrnují adresy začínající hodnotami _maska 10.*_, _172.16. *_ - _172.31. *_, a _192.168. *_.

   > [!NOTE]
   > Pravidla protokolu IP sítě nemají žádný vliv na požadavků pocházejících ze stejné oblasti Azure jako účet úložiště. Použití [pravidla virtuální sítě](#grant-access-from-a-virtual-network) požadavky stejné oblasti.

V současnosti jsou podporované jenom adresy IPV4.

Každý účet úložiště podporuje až 100 IP sítě pravidla, která mohou být spojeny s [pravidla virtuální sítě](#grant-access-from-a-virtual-network).

### <a name="configuring-access-from-on-premises-networks"></a>Konfigurace přístupu z místní sítě

Pokud chcete udělit přístup z vaší místní sítě k vašemu účtu úložiště s pravidlem síť IP, je nutné určit internetové IP adresy používané ve vaší síti. Požádejte o pomoc svého správce sítě.

Můžete použít [ExpressRoute](/azure/expressroute/expressroute-introduction) k připojení sítě k síti Azure. Každý okruh tady, se nakonfigurují dvě veřejné IP adresy. Lze nalézt v Microsoft Edge a použít [veřejné partnerské vztahy Azure](/azure/expressroute/expressroute-circuit-peerings#expressroute-routing-domains) pro připojení k Microsoft Services jako je Azure Storage. Umožňující navázat komunikaci s Azure Storage, vytvořte pravidla sítě protokolu IP pro veřejné IP adresy vaší obvody. Najít váš okruh ExpressRoute veřejné IP adresy [otevřete lístek podpory pro ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) prostřednictvím webu Azure portal.

### <a name="managing-ip-network-rules"></a>Správa pravidel sítě IP

Můžete spravovat pravidla sítě protokolu IP pro účty úložiště pomocí webu Azure portal, Powershellu nebo CLIv2.

#### <a name="azure-portal"></a>portál Azure

1. Přejděte na účet úložiště, kterou chcete zabezpečit.

1. Klikněte v nabídce nastavení nazývá **virtuální sítí a bran firewall**.

1. Zkontrolujte, jestli jste vybrali k povolení přístupu z **vybrané sítě**.

1. Udělit přístup k Internetu rozsah IP adres, zadejte IP adresu nebo rozsah adres (ve formátu CIDR) v části **brány Firewall** > **rozsah adres**.

1. Pokud chcete odebrat pravidlo protokolu IP sítě, klikněte na ikonu koše vedle rozsah adres.

1. Klikněte na tlačítko **Uložit** změny.

#### <a name="powershell"></a>PowerShell

1. Nainstalujte [prostředí Azure PowerShell](/powershell/azure/install-azurerm-ps) a [přihlášení](/powershell/azure/authenticate-azureps).

1. Seznam pravidel sítě protokolu IP.

    ```PowerShell
    (Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

1. Přidáte pravidlo pro sítě pro jednotlivé IP adresy.

    ```PowerShell
    Add-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Přidáte pravidlo pro rozsah IP adres pro sítě.

    ```PowerShell
    Add-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

1. Odeberte pravidlo pro sítě pro jednotlivé IP adresy.

    ```PowerShell
    Remove-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Odeberte pravidlo pro rozsah IP adres pro sítě.

    ```PowerShell
    Remove-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
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

Pravidla sítě můžete povolit zabezpečené síťové konfigurace pro většinu scénářů. Existují však některé případů, kdy výjimky musí být uděleno pro svou plnou funkčnost. Účty úložiště můžete nakonfigurovat výjimky pro důvěryhodné služby Microsoftu a pro přístup k datům storage analytics.

### <a name="trusted-microsoft-services"></a>Důvěryhodné služby Microsoftu

Některé služby společnosti Microsoft, které pracují s účty úložiště provoz ze sítě, které nelze udělit přístup pomocí pravidel sítě.

Abychom tento typ služby práce tak, jak má, umožňují sadu důvěryhodným službám Microsoftu obejít pravidel sítě. Tyto služby pak bude používat silné ověřování pro přístup k účtu úložiště.

Pokud povolíte **Povolit důvěryhodné služby Microsoftu...**  výjimky, tyto služby (při registraci v rámci vašeho předplatného), je udělen přístup k účtu úložiště:

|Služba|Název poskytovatele prostředků|Účel|
|:------|:---------------------|:------|
|Azure Backup|Microsoft.Backup|Spuštění zálohování a obnovení nespravované disky ve virtuálních počítačích IAAS. (není vyžadované pro spravované disky). [Další informace](/azure/backup/backup-introduction-to-azure-backup).|
|Azure DevTest Labs|Microsoft.DevTestLab|Vytváření a artefaktů instalace vlastní image. [Další informace](/azure/devtest-lab/devtest-lab-overview).|
|Azure Event Grid|Microsoft.EventGrid|Povolte publikování událostí v úložišti objektů Blob. [Další informace](/azure/event-grid/overview).|
|Azure Event Hubs|Microsoft.EventHub|Archivace dat s Event Hubs Capture. [Další informace](/azure/event-hubs/event-hubs-capture-overview).|
|Sítě Azure|Microsoft.Networking|Store a analyzovat protokoly přenosů sítě. [Další informace](/azure/network-watcher/network-watcher-packet-capture-overview).|
|Azure Monitor|Microsoft.Insights|Umožňuje zápis dat do účtu úložiště zabezpečené monitorování [Další](/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security#monitoring-and-secured-Azure-storage-and-networks).|
|

### <a name="storage-analytics-data-access"></a>Přístup k datům Storage analytics

V některých případech je zapotřebí ve směru z mimo hranice sítě přístup ke čtení diagnostické protokoly a metriky. Můžete udělit výjimky pravidel sítě umožňující přístup pro čtení na soubory protokolů pro účet úložiště, metriky tabulek nebo obojí. [Další informace o práci s analytika úložiště.](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>Správa výjimek

Můžete spravovat pravidla výjimky sítě prostřednictvím webu Azure portal, Powershellu nebo Azure CLI verze 2.

#### <a name="azure-portal"></a>portál Azure

1. Přejděte na účet úložiště, kterou chcete zabezpečit.

1. Klikněte v nabídce nastavení nazývá **virtuální sítí a bran firewall**.

1. Zkontrolujte, jestli jste vybrali k povolení přístupu z **vybrané sítě**.

1. V části **výjimky**, vyberte výjimky, kterým chcete přiřadit.

1. Klikněte na tlačítko **Uložit** změny.

#### <a name="powershell"></a>PowerShell

1. Nainstalujte [prostředí Azure PowerShell](/powershell/azure/install-azurerm-ps) a [přihlášení](/powershell/azure/authenticate-azureps).

1. Zobrazte výjimky pravidel sítě účtu úložiště.

    ```PowerShell
    (Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

1. Nakonfigurujte výjimky, čímž pravidel sítě účtu úložiště.

    ```PowerShell
    Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

1. Odeberte výjimky z pravidel sítě účtu úložiště.

    ```PowerShell
    Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
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

## <a name="next-steps"></a>Další postup

Další informace o koncových bodech služeb Azure Network v [koncové body služby](/azure/virtual-network/virtual-network-service-endpoints-overview).

Dostaňte hlouběji do zabezpečení služby Azure Storage v [Průvodci zabezpečením Azure Storage](storage-security-guide.md).
