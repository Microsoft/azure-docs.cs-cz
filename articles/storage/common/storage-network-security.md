---
title: Konfigurace virtuálních sítí a bran firewall Azure Storage | Dokumentace Microsoftu
description: Konfigurace zabezpečení vrstvami sítě pro váš účet úložiště.
services: storage
author: cbrooksmsft
ms.service: storage
ms.topic: article
ms.date: 10/25/2017
ms.author: cbrooks
ms.component: common
ms.openlocfilehash: 9eaaaaa4cc9be661cdc2ffde2b634e062c95a404
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39523253"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Konfigurace virtuálních sítí a bran firewall Azure Storage
Azure Storage poskytuje vrstvený model zabezpečení umožňuje zabezpečit vaše účty úložiště s konkrétní sadou povolených sítí.  Když jsou nakonfigurovaná pravidla sítě, můžete pouze aplikace z povolených sítí přístup k účtu úložiště.  Při volání z povolené sítě, aplikace dál vyžadují správnou autorizaci (platný přístupový klíč nebo SAS token) pro přístup k účtu úložiště.

> [!IMPORTANT]
> Zapnutí pravidla brány Firewall pro váš účet úložiště bude blokovat přístup na příchozí požadavky pro data, včetně od ostatních služeb Azure.  To zahrnuje pomocí portálu pro zápis protokolů, atd.  Pro zúčastněné služby můžete znovu povolit funkce prostřednictvím [výjimky](#Exceptions) níže v části.  Pro přístup k portálu je třeba provést z počítače v rámci důvěryhodné hranice (IP nebo virtuální síť), který jste nastavili.
>

## <a name="scenarios"></a>Scénáře
Účty úložiště je možné nakonfigurovat k odepření přístupu k provozu ze všech sítí (včetně přenosů z Internetu) ve výchozím nastavení.  Přístup lze udělit práva provoz z konkrétní Azure virtuálních sítí, což umožňuje vytvořit zabezpečené ohraničení sítě pro vaše aplikace.  Můžete také umožnit přístup k veřejnému Internetu rozsahy IP adres, povolení připojení z konkrétní Internetu nebo místních klientů.

Na všech síťových protokolů do úložiště Azure, včetně REST a protokolu SMB se vynucují pravidla sítě.  Přístup k vašim datům z nástrojů, jako je na webu Azure portal, Průzkumníku služby Storage a AZCopy vyžadují explicitní pravidla udělení přístupu při pravidla sítě jsou v platnost.

Pravidla sítě můžete použít pro existující účty úložiště, nebo lze použít při vytváření nových účtů úložiště.

Po použití pravidla sítě jsou vynucují se pro všechny požadavky.  Tokeny SAS, které udělují přístup ke konkrétní IP adresu služby slouží k **limit** access token držitele, ale proveďte není udělit nový přístup mimo síť nakonfigurovaná pravidla. 

Diskové přenosy virtuálního počítače (včetně připojení a odpojení operace a vstupně-výstupních operací na disku) je **není** ovlivněny pravidel sítě.  Přístup REST k objektům BLOB stránky je chráněn pravidel sítě.

Klasické účty úložiště **nejsou** podporu brány firewall a virtuální sítě.

Prostřednictvím vytváření výjimku, jak je uvedeno v se podporuje zálohování a obnovení virtuálních počítačů pomocí nespravované disky v účtech úložiště s pravidla sítě použitá [výjimky](/storage/common/storage-network-security#exceptions) části tohoto článku.  Výjimky brány firewall se nedají použít se službou Managed Disks, jako jsou již spravovány nástrojem Azure.

## <a name="change-the-default-network-access-rule"></a>Změnit výchozí pravidlo přístupu sítě
Účty úložiště ve výchozím nastavení, přijímat připojení z klientů v síti.  Chcete-li omezit přístup k vybrané sítě, musíte nejdřív změnit výchozí akci.

> [!WARNING]
> Provádění změn pravidel sítě může mít vliv na vaše aplikace možnost připojení k Azure Storage.  Nastavit výchozí pravidlo sítě **Odepřít** blokuje všechna přistupovat k datům, není-li pravidla konkrétní sítě *udělení* přístupu jsou použita také.  Je potřeba udělit přístup k žádné povolené sítě pomocí pravidel pro sítě, než změníte výchozí pravidlo pro odepření přístupu.
>

#### <a name="azure-portal"></a>portál Azure
1. Přejděte do účtu úložiště, kterou chcete zabezpečit.  

2. Klikněte v nabídce nastavení nazývá **virtuální sítí a bran firewall**.
3. Ve výchozím nastavení odepření přístupu, zvolte povolení přístupu z "Vybraných sítí".  Povolit přenos ze všech sítí, zvolte povolení přístupu z "Všechny sítě".
4. Klikněte na tlačítko *Uložit* změny.

#### <a name="powershell"></a>PowerShell
1. Nainstalujte nejnovější [prostředí Azure PowerShell](/powershell/azure/install-azurerm-ps) a [přihlášení](/powershell/azure/authenticate-azureps).

2. Zobrazí stav výchozí pravidlo pro účet úložiště.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet  -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
``` 

3. Nastavte výchozí pravidlo pro odepření přístupu k síti ve výchozím nastavení.  
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
```    

4. Nastavte výchozí pravidlo, kterým povolíte přístup k síti ve výchozím nastavení.
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
```    

#### <a name="cliv2"></a>CLIv2
1. [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli) a [přihlášení](/cli/azure/authenticate-azure-cli).
2. Zobrazí stav výchozí pravidlo pro účet úložiště.
```azurecli
az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
```

3. Nastavte výchozí pravidlo pro odepření přístupu k síti ve výchozím nastavení.  
```azurecli
az storage account update --name "mystorageaccount" --resource-group "myresourcegroup" --default-action Deny
```

4. Nastavte výchozí pravidlo, kterým povolíte přístup k síti ve výchozím nastavení.
```azurecli
az storage account update --name "mystorageaccount" --resource-group "myresourcegroup" --default-action Allow
```

## <a name="grant-access-from-a-virtual-network"></a>Udělení přístupu z virtuální sítě
Účty úložiště můžete nakonfigurována pro povolení přístupu jenom z konkrétních virtuálních sítí Azure. 

Tím, že [koncový bod služby](/azure/virtual-network/virtual-network-service-endpoints-overview) pro službu Azure Storage v rámci virtuální sítě, provoz je zajištěn optimální trasa ke službě Azure Storage. Identity virtuální síť a podsíť také přenášejí spolu s každou žádostí.  Správci mohou následně nakonfigurovat pravidla sítě pro účet úložiště, které umožňují požadavků bude moct přijmout z konkrétních podsítí ve virtuální síti.  Klienti udělen přístup přes tyto pravidel sítě musí i dál splňuje požadavky na povolení účtu úložiště pro přístup k datům.

Každý účet úložiště může podporovat až 100 pravidel virtuální sítě, které mohou být spojeny s [pravidel sítě IP](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Dostupné oblasti virtuální sítě
Obecně platí koncové body služby práci mezi virtuálními sítěmi a instance služby ve stejné oblasti Azure.  Když koncové body služby se používají s Azure Storage, je tento obor rozšířen, aby zahrnoval [spárované oblasti](/azure/best-practices-availability-paired-regions).  To umožňuje kontinuity podnikových procesů během regionální převzetí služeb při selhání a také bezproblémový přístup k instancím jen pro čtení (RA-GRS) geograficky redundantní úložiště.  Pravidla sítě, které udělují přístup z virtuální sítě do účtu úložiště také udělit přístup na jakoukoli instanci RA-GRS.

Při plánování zotavení po havárii během oblastního výpadku, měli byste zřídit virtuální sítě v párované oblasti předem. By měl být povolené koncové body služby pro službu Azure Storage a udělení přístupu z těchto alternativních virtuálních sítí pravidel sítě bude použito pro účty geograficky redundantní úložiště.

> [!NOTE]
> Koncové body služby se nevztahují na přenosy mimo oblast virtuální sítě a pár určené oblasti.  Udělení přístupu z virtuální sítě k účtům úložiště pravidel sítě lze použít pouze pro virtuální sítě v primární oblasti účtu úložiště nebo v určené spárované oblasti.
>

### <a name="required-permissions"></a>Požadovaná oprávnění
Pokud chcete použít pravidlo virtuální sítě pro účet úložiště, uživatel musí mít oprávnění k *připojit ke službě Service k podsíti* pro přidávané podsítě.  Toto oprávnění je součástí *Přispěvatel účtů úložiště* předdefinovanou roli a může být přidán do vlastní definice rolí.

Účet úložiště a virtuální sítí udělen přístup **může** v různých předplatných, ale tato předplatná musí být součástí stejného tenanta služby Azure Active Directory.

### <a name="managing-virtual-network-rules"></a>Správa pravidel virtuální sítě
Pravidla virtuální sítě pro účty úložiště jde spravovat pomocí webu Azure portal, Powershellu nebo CLIv2.

#### <a name="azure-portal"></a>portál Azure
1. Přejděte do účtu úložiště, kterou chcete zabezpečit.  
2. Klikněte v nabídce nastavení nazývá **virtuální sítí a bran firewall**.
3. Ujistěte se, že rozhodnete povolit přístup z "Vybraných sítí".
4. Chcete-li udělit přístup k virtuální síti se nové pravidlo sítě, v části "Virtuální sítě", klikněte na tlačítko "Přidat existující" a vyberte existující virtuální sítě a podsítě, pak klikněte na tlačítko *přidat*.  Pokud chcete vytvořit novou virtuální síť a jí udělit přístup, klikněte na tlačítko *přidat nový*, zadejte informace potřebné k vytvoření nové virtuální sítě a potom klikněte na *vytvořit*.

> [!NOTE]
> Pokud koncový bod služby pro službu Azure Storage nebyl dříve nakonfigurován pro vybrané virtuální sítě a podsítě, můžete nakonfigurovat jako součást této operace.
>

5. Odebrat pravidlo virtuální sítě nebo podsítě, klikněte na tlačítko "..." otevřete místní nabídku pro virtuální síť nebo podsíť a klikněte na "Remove".
6. Klikněte na tlačítko *Uložit* změny.

#### <a name="powershell"></a>PowerShell
1. Nainstalujte nejnovější [prostředí Azure PowerShell](/powershell/azure/install-azurerm-ps) a [přihlášení](/powershell/azure/authenticate-azureps).
2. Seznam pravidel virtuální sítě
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
```

3. Povolit koncový bod služby pro službu Azure Storage na existující virtuální sítě a podsítě
```PowerShell
Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"  -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzureRmVirtualNetwork
```

4. Přidáte pravidlo pro sítě pro virtuální síť a podsíť.  
```PowerShell
$subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
Add-AzureRmStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id   
```    

5. Odeberte pravidlo pro sítě pro virtuální síť a podsíť.  
```PowerShell
$subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
Remove-AzureRmStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id   
```    

> [!IMPORTANT]
> Nezapomeňte [nastavte výchozí pravidlo](#change-the-default-network-access-rule) na odepřít, nebo pravidel sítě nebude mít žádný efekt.
>

#### <a name="cliv2"></a>CLIv2
1. [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli) a [přihlášení](/cli/azure/authenticate-azure-cli).
2. Seznam pravidel virtuální sítě
```azurecli
az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
```

2. Povolit koncový bod služby pro službu Azure Storage na existující virtuální sítě a podsítě
```azurecli
az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
```

3. Přidáte pravidlo pro sítě pro virtuální síť a podsíť.  
```azurecli
subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
```

4. Odeberte pravidlo pro sítě pro virtuální síť a podsíť. 
```azurecli
subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
```

> [!IMPORTANT]
> Nezapomeňte [nastavte výchozí pravidlo](#change-the-default-network-access-rule) na odepřít, nebo pravidel sítě nebude mít žádný efekt.
>

## <a name="grant-access-from-an-internet-ip-range"></a>Udělení přístupu z Internetu rozsah IP adres
Účty úložiště můžete nakonfigurována pro povolení přístupu z veřejného Internetu konkrétní rozsahy IP adres.  Tato konfigurace umožňuje konkrétní internetové služby a s místními sítěmi, které má být udělen přístup, když obecné internetový provoz je blokován.

Povolené rozsahy adres sítě internet se dá zadat pomocí [notaci CIDR](https://tools.ietf.org/html/rfc4632) ve formě *16.17.18.0/24* nebo jako jednotlivé IP adresy jako *16.17.18.19* .

> [!NOTE]
> Rozsahy malé adres pomocí "/ 31" nebo "/ 32" předpony velikosti se nepodporují.  Tyto rozsahy musí být nakonfigurovaný pomocí jednotlivých pravidel IP adres.
>

Pravidla síťových IP jsou povolené jenom pro **veřejného Internetu** IP adresy.  Rozsahy IP adres vyhrazené pro privátní sítě (jak jsou definovány v dokumentu RFC 1918) nejsou povoleny v pravidlech IP.  Privátní sítě zahrnují adresy začínající hodnotami *10.\** , *172.16.\** , a *192.168.\** .

V současnosti jsou podporované jenom adresy IPV4.

Každý účet úložiště může podporovat až 100 IP pravidla sítě, které mohou být spojeny s [pravidel virtuální sítě](#grant-access-from-a-virtual-network)

### <a name="configuring-access-from-on-premises-networks"></a>Konfigurace přístupu z místní sítě
Aby bylo možné udělit přístup z vaší místní sítě k vašemu účtu úložiště s pravidlem síť IP, je nutné určit internetové IP adresy používané ve vaší síti.  Požádejte o pomoc svého správce sítě.

Pokud vaše síť připojená k síti Azure pomocí [ExpressRoute](/azure/expressroute/expressroute-introduction), každý okruh se nakonfigurují dvě veřejné IP adresy v Microsoft Edge, které se používají pro připojení k Microsoft Services jako je Azure Storage pomocí [Veřejného partnerského vztahu azure](/azure/expressroute/expressroute-circuit-peerings#expressroute-routing-domains).  Povolit komunikaci z váš okruh do služby Azure Storage, musíte vytvořit pravidla sítě protokolu IP pro veřejné IP adresy vaší obvody.  Aby bylo možné najít váš okruh ExpressRoute veřejné IP adresy [otevřete lístek podpory pro ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) prostřednictvím webu Azure portal.


### <a name="managing-ip-network-rules"></a>Správa pravidel sítě IP
Pravidla sítě protokolu IP pro účty úložiště je možné spravovat prostřednictvím webu Azure portal, Powershellu nebo CLIv2.

#### <a name="azure-portal"></a>portál Azure
1. Přejděte do účtu úložiště, kterou chcete zabezpečit.  
2. Klikněte v nabídce nastavení nazývá **virtuální sítí a bran firewall**.
3. Ujistěte se, že rozhodnete povolit přístup z "Vybraných sítí".
4. Udělit přístup k Internetu rozsah IP adres, zadejte IP adresu nebo rozsah adres (ve formátu CIDR) v rámci brány Firewall, rozsahy adres.
5. Chcete-li odebrat pravidlo protokolu IP sítě, klikněte na tlačítko "..." otevřete místní nabídku pro pravidlo a klikněte na "Remove".
6. Klikněte na tlačítko *Uložit* změny.

#### <a name="powershell"></a>PowerShell
1. Nainstalujte nejnovější [prostředí Azure PowerShell](/powershell/azure/install-azurerm-ps) a [přihlášení](/powershell/azure/authenticate-azureps).
2. Seznam pravidel sítě protokolu IP.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
```

3. Přidáte pravidlo pro sítě pro jednotlivé IP adresy.  
```PowerShell
Add-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19" 
``` 

4. Přidáte pravidlo pro rozsah IP adres pro sítě.  
```PowerShell
Add-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24" 
```    

5. Odeberte pravidlo pro sítě pro jednotlivé IP adresy. 
```PowerShell
Remove-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"  
```

6. Odeberte pravidlo pro rozsah IP adres pro sítě.  
```PowerShell
Remove-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"  
```    

> [!IMPORTANT]
> Nezapomeňte [nastavte výchozí pravidlo](#change-the-default-network-access-rule) na odepřít, nebo pravidel sítě nebude mít žádný efekt.
>

#### <a name="cliv2"></a>CLIv2
1. [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli) a [přihlášení](/cli/azure/authenticate-azure-cli).
2. Seznam pravidel sítě IP
```azurecli
az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
```

3. Přidáte pravidlo pro sítě pro jednotlivé IP adresy.
```azurecli
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
```

4. Přidáte pravidlo pro rozsah IP adres pro sítě.  
```azurecli
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
```

5. Odeberte pravidlo pro sítě pro jednotlivé IP adresy.  
```azurecli
az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
```

6. Odeberte pravidlo pro rozsah IP adres pro sítě.  
```azurecli
az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
```

> [!IMPORTANT]
> Nezapomeňte [nastavte výchozí pravidlo](#change-the-default-network-access-rule) na odepřít, nebo pravidel sítě nebude mít žádný efekt.
>

## <a name="exceptions"></a>Výjimky
Zatímco pravidel sítě můžete povolit zabezpečené síťové konfigurace pro většinu scénářů, existují případy, ve kterém musí být výjimky udělena pro svou plnou funkčnost.  Účty úložiště můžete nakonfigurovat výjimky pro důvěryhodného služby a pro přístup k datům Storage analytics.

### <a name="trusted-microsoft-services"></a>Důvěryhodné služby Microsoftu
Některé služby společnosti Microsoft, které pracují s účty úložiště provoz ze sítě, které nelze udělit přístup pomocí pravidel sítě. 

Pokud chcete povolit tento typ služby fungují tak, jak má, je možné povolit sadu důvěryhodným službám Microsoftu obejít pravidel sítě. Tyto služby pak bude používat silné ověřování pro přístup k účtu úložiště.

Když je povolená výjimka "Důvěryhodné služby Microsoftu", jsou tyto služby (při registraci v rámci vašeho předplatného) udělen přístup k účtu úložiště:

|Služba|Název poskytovatele prostředků|Účel|
|:------|:---------------------|:------|
|Azure Backup|Microsoft.Backup|Provedení zálohování a obnovení nespravované disky ve virtuálních počítačích IAAS. (není vyžadované pro spravované disky). [Další informace](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup).|
|Azure DevTest Labs|Microsoft.DevTestLab|Vytváření a artefaktů instalace vlastní image.  [Další informace](https://docs.microsoft.com/azure/devtest-lab/devtest-lab-overview).|
|Azure Event Grid|Microsoft.EventGrid|Povolte publikování událostí v úložišti objektů Blob.  [Další informace](https://docs.microsoft.com/azure/event-grid/overview).|
|Azure Event Hubs|Microsoft.EventHub|Archivace dat s Event Hubs Capture.  [Další informace](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview).|
|Sítě Azure|Microsoft.Networking|Store a analyzovat protokoly přenosů sítě.  [Další informace](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview).|
||||

### <a name="storage-analytics-data-access"></a>Přístup k datům Storage analytics
V některých případech je zapotřebí ve směru z mimo hranice sítě přístup ke čtení diagnostické protokoly a metriky.  Výjimky z pravidel sítě lze udělit práva povolit přístup pro čtení na úložiště účtu soubory protokolů, metriky tabulek nebo obojí. [Další informace o práci s analytika úložiště.](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>Správa výjimek
Pravidlo výjimky sítě je možné spravovat prostřednictvím webu Azure portal, Powershellu nebo Azure CLI verze 2.

#### <a name="azure-portal"></a>portál Azure
1. Přejděte do účtu úložiště, kterou chcete zabezpečit.  
2. Klikněte v nabídce nastavení nazývá **virtuální sítí a bran firewall**.
3. Ujistěte se, že rozhodnete povolit přístup z "Vybraných sítí".
4. V části výjimky vyberte výjimky, které chcete udělit.
5. Klikněte na tlačítko *Uložit* změny.

#### <a name="powershell"></a>PowerShell
1. Nainstalujte nejnovější [prostředí Azure PowerShell](/powershell/azure/install-azurerm-ps) a [přihlášení](/powershell/azure/authenticate-azureps).
2. Zobrazte výjimky pravidel sítě účtu úložiště.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
```

3. Nakonfigurujte výjimky, čímž pravidel sítě účtu úložiště.
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount"  -Bypass AzureServices,Metrics,Logging
```

4. Odeberte výjimky z pravidel sítě účtu úložiště.
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount"  -Bypass None
```

> [!IMPORTANT]
> Nezapomeňte [nastavte výchozí pravidlo](#change-the-default-network-access-rule) na odepřít, nebo odebrání výjimky nebude mít žádný efekt.
>

#### <a name="cliv2"></a>CLIv2
1. [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli) a [přihlášení](/cli/azure/authenticate-azure-cli).
2. Zobrazte výjimky pravidel sítě účtu úložiště.
```azurecli
az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
```

3. Nakonfigurujte výjimky, čímž pravidel sítě účtu úložiště.
```azurecli
az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
```

4. Odeberte výjimky z pravidel sítě účtu úložiště.
```azurecli
az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
```

> [!IMPORTANT]
> Nezapomeňte [nastavte výchozí pravidlo](#change-the-default-network-access-rule) na odepřít, nebo odebrání výjimky nebude mít žádný efekt.
>

## <a name="next-steps"></a>Další postup
Další informace o koncových bodech služeb sítě Azure v [koncové body služby](/azure/virtual-network/virtual-network-service-endpoints-overview).

Dostaňte hlouběji do zabezpečení služby Azure Storage v [Průvodci zabezpečením Azure Storage](storage-security-guide.md).
