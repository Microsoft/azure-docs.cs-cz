---
title: Konfigurace brány firewall úložiště Azure a virtuální sítě | Microsoft Docs
description: Konfigurace zabezpečení sítě vrstev pro váš účet úložiště.
services: storage
documentationcenter: ''
author: cbrooksmsft
manager: cbrooks
editor: cbrooks
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 10/25/2017
ms.author: cbrooks
ms.openlocfilehash: 52d904e7a7e8e5d520d2abd799ef0ae7e99b9894
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Konfigurace brány firewall úložiště Azure a virtuální sítě
Úložiště Azure poskytuje vrstvený model zabezpečení umožňuje zabezpečit účtů úložiště na konkrétní sadu povolených sítí.  Pokud jsou nakonfigurovaná pravidla pro sítě, můžete jenom aplikace z povolených sítí přístup k účtu úložiště.  Při volání z povolených sítě, aplikace dál vyžadují správné autorizace (platný přístupový klíč nebo tokenu SAS) pro přístup k účtu úložiště.

> [!IMPORTANT]
> Zapnutí pravidla brány Firewall pro váš účet úložiště bude blokovat přístup k příchozí požadavky pro data, včetně z jiných služeb systému Azure.  To zahrnuje pomocí portálu, zápis protokoly atd.  Pro zúčastněných služby můžete znovu povolit funkce prostřednictvím [výjimky](#Exceptions) části níže.  Pro přístup k portálu potřebovali byste to udělat z počítače v důvěryhodných hranici (IP adresu nebo virtuální sítě), který jste nastavili.
>

## <a name="scenarios"></a>Scénáře
Účty úložiště lze nakonfigurovat tak, aby odepřel přístup k provozu od všech sítí (včetně přenosy z Internetu) ve výchozím nastavení.  Provoz z konkrétní Azure virtuální sítě, což umožňuje vytvořit hranici zabezpečení sítě pro vaše aplikace můžete udělit přístup.  Také lze udělit přístup k veřejnému Internetu rozsahy IP adres, povolení připojení z konkrétní klientů internet nebo místní.

Na všech síťových protokolů do úložiště Azure, včetně REST a protokolu SMB se vynucují pravidla sítě.  Přístup k datům z nástrojů, například portálu Azure Storage Explorer a AZCopy vyžadují explicitní sítě pravidla udělení přístupu při pravidla sítě jsou v platnosti.

Pravidla pro sítě lze použít pro existující účty úložiště, nebo můžete použít při vytváření nových účtů úložiště.

Po síti pravidla se používají, se vynucuje pro všechny požadavky.  Tokeny SAS, která udělují přístup na konkrétní IP adresu službu sloužit k **limit** přístup držitele token se ale není udělit nové přístup mimo nakonfigurovaná síťová pravidla. 

Přenosy disku virtuálního počítače (včetně připojení a odpojení operace a vstupně-výstupních operací na disku) je **není** vliv na pravidla sítě.  Pravidla pro sítě je chráněn REST přístup k objekty BLOB stránky.

Klasické účty úložiště **nepodporují** podporu brány firewall a virtuální sítě.

Zálohování a obnovení virtuálních počítačů pomocí nespravované disky v účtech úložiště použít pravidla pro sítě je podporována prostřednictvím vytváření výjimku, jak je uvedeno v [výjimky](/storage/common/storage-network-security#exceptions) tohoto článku.  Výjimky brány firewall se nedají použít s spravované disky, jako jsou již spravovány nástrojem Azure.

## <a name="change-the-default-network-access-rule"></a>Změňte výchozí pravidlo přístupu k síti
Ve výchozím nastavení účty úložiště přijmout připojení z klientů v síti.  Omezit přístup k vybrané sítě, musíte nejdřív změnit výchozí akci.

> [!WARNING]
> Provádění změn pravidla sítě může mít vliv na vaše aplikace umožňuje připojit k úložišti Azure.  Nastavení výchozí pravidlo sítě na **Odepřít** blokován přístup k datům, pokud pravidla pro konkrétní sítě *udělení* přístup se také použijí.  Ujistěte se, že udělit přístup k žádným povolené sítím pomocí pravidel sítě než změníte výchozí pravidlo odepřít přístup.
>

#### <a name="azure-portal"></a>Azure Portal
1. Přejděte na účet úložiště, které chcete zabezpečit.  

2. Klikněte v nabídce nastavení, která je volána **brány firewall a virtuální sítě**.
3. Odepřít přístup ve výchozím nastavení, zvolte povolení přístupu z "Vybrané sítě".  Pokud chcete povolit přenosy ze všech sítí, zvolte povolení přístupu z "Všechny sítě".
4. Klikněte na tlačítko *Uložit* proveďte změny.

#### <a name="powershell"></a>PowerShell
1. Nainstalujte si nejnovější verzi [prostředí Azure PowerShell](/powershell/azure/install-azurerm-ps) a [přihlášení](/powershell/azure/authenticate-azureps).

2. Zobrazení stavu výchozí pravidlo pro účet úložiště.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet  -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
``` 

3. Nastavte výchozí pravidlo tak, aby odepřel přístup k síti ve výchozím nastavení.  
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
```    

4. Nastavte výchozí pravidlo, které ve výchozím nastavení Povolit přístup k síti.
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
```    

#### <a name="cliv2"></a>CLIv2
1. [Instalace rozhraní příkazového řádku Azure 2.0](/cli/azure/install-azure-cli) a [přihlášení](/cli/azure/authenticate-azure-cli).
2. Zobrazení stavu výchozí pravidlo pro účet úložiště.
```azurecli
az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
```

3. Nastavte výchozí pravidlo tak, aby odepřel přístup k síti ve výchozím nastavení.  
```azurecli
az storage account update --name "mystorageaccount" --resource-group "myresourcegroup" --default-action Deny
```

4. Nastavte výchozí pravidlo, které ve výchozím nastavení Povolit přístup k síti.
```azurecli
az storage account update --name "mystorageaccount" --resource-group "myresourcegroup" --default-action Allow
```

## <a name="grant-access-from-a-virtual-network"></a>Udělení přístupu z virtuální sítě
Účty úložiště můžete nakonfigurovat tak, aby přístup pouze z konkrétní virtuálních sítí Azure. 

Povolením [koncový bod služby](/azure/virtual-network/virtual-network-service-endpoints-overview) pro Azure Storage v rámci virtuální sítě, je provoz zajištěn optimální trasy ke službě Azure Storage. Identity virtuální síť a podsíť, se přenáší taky s každou žádostí.  Správci mohou následně nakonfigurovat pravidla sítě pro účet úložiště, které chcete povolit žádosti o přijetí z konkrétních podsítí ve virtuální síti.  Klienti udělit přístup přes tato pravidla sítě musí i dál splňují požadavky na ověření účtu úložiště pro přístup k datům.

Každý účet úložiště může podporovat až 100 pravidla virtuální sítě, které mohou být kombinovány s [pravidla sítě IP](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Dostupné oblasti virtuální sítě
Obecně platí koncové body služby fungovat mezi virtuálními sítěmi a instance služby ve stejné oblasti Azure.  Když se použije koncové body služby Azure Storage, je tento obor rozšířit, aby obsahovaly [spárované oblast](/azure/best-practices-availability-paired-regions).  To umožňuje kontinuitu během regionální převzetí služeb při selhání a také seemless přístup k instancím úložiště (RA-GRS) geo-reduntant jen pro čtení.  Pravidla sítě, která udělují přístup z virtuální sítě na účet úložiště také udělit přístup k jakékoli instanci RA-GRS.

Při plánování pro zotavení po havárii během výpadku místní, by měl zřídit virtuální sítě v oblasti spárované předem. Koncové body služby pro Azure Storage by měla být povolená a pravidla sítě udělení přístupu z tyto alternativní virtuální sítě má být použita na účty geograficky redundantní úložiště.

> [!NOTE]
> Koncové body služby nevztahují na provoz mimo oblast dvojice určené oblasti a virtuální sítě.  Udělení přístupu z virtuální sítě k účtům úložiště pravidla sítě lze použít pouze pro virtuální sítě v primární oblasti účtu úložiště nebo v oblasti určené spárované.
>

### <a name="required-permissions"></a>Požadovaná oprávnění
Chcete-li použít pravidlo pro virtuální sítě na účet úložiště, uživatel musí mít oprávnění k *připojit ke službě Service k podsíti* pro podsítě, který chcete přidat.  Toto oprávnění je součástí *Přispěvatel účet úložiště* předdefinovaná role a mohou být přidány do definice vlastních rolí.

Účet úložiště a virtuální sítě udělen přístup **může** být v různých předplatných, ale tyto odběry musí být součástí stejné klienta Azure Active Directory.

### <a name="managing-virtual-network-rules"></a>Položka spravovat pravidla virtuální sítě
Pravidla pro virtuální sítě pro účty úložiště je možné spravovat prostřednictvím portálu Azure, PowerShell nebo CLIv2.

#### <a name="azure-portal"></a>Azure Portal
1. Přejděte na účet úložiště, které chcete zabezpečit.  
2. Klikněte v nabídce nastavení, která je volána **brány firewall a virtuální sítě**.
3. Ujistěte se, že jste se rozhodli povolit přístup ze 'vybrané sítě.
4. Chcete-li udělit přístup k virtuální síti s nové pravidlo sítě, v části "Virtuální sítě", klikněte na tlačítko "Přidat existující" a vyberte existující virtuální sítě a podsítě, potom klikněte na *přidat*.  Chcete-li vytvořit novou virtuální síť a jí udělit přístup, klikněte na tlačítko *přidat nový*, poskytovat informace potřebné k vytvoření nové virtuální sítě a potom klikněte na *vytvořit*.

> [!NOTE]
> Pokud koncový bod služby pro Azure Storage nebyl dříve nakonfigurován pro vybrané virtuální sítě a podsítě, můžete nakonfigurovat jako součást této operace.
>

5. Odebrat virtuální síť nebo podsíť, pravidlo, klikněte na tlačítko "..." otevřete kontextu nabídku pro virtuální síť nebo podsíť a klikněte na "Odebrat".
6. Klikněte na tlačítko *Uložit* proveďte změny.

#### <a name="powershell"></a>PowerShell
1. Nainstalujte si nejnovější verzi [prostředí Azure PowerShell](/powershell/azure/install-azurerm-ps) a [přihlášení](/powershell/azure/authenticate-azureps).
2. Seznam pravidel virtuální síť
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
```

3. Povolení koncového bodu služby pro Azure Storage na existující virtuální síť a podsíť
```PowerShell
Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"  -AddressPrefix "10.1.1.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzureRmVirtualNetwork
```

4. Přidáte pravidlo pro sítě pro virtuální síť a podsíť.  
```PowerShell
$subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
Add-AzureRmStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id   
```    

5. Odeberte pravidlo sítě pro virtuální síť a podsíť.  
```PowerShell
$subnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mysubnet"
Remove-AzureRmStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id   
```    

> [!IMPORTANT]
> Nezapomeňte [nastavit výchozí pravidlo](#change-the-default-network-access-rule) odepřít, nebo pravidla sítě nebude mít žádný vliv.
>

#### <a name="cliv2"></a>CLIv2
1. [Instalace rozhraní příkazového řádku Azure 2.0](/cli/azure/install-azure-cli) a [přihlášení](/cli/azure/authenticate-azure-cli).
2. Seznam pravidel virtuální síť
```azurecli
az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
```

2. Povolení koncového bodu služby pro Azure Storage na existující virtuální síť a podsíť
```azurecli
az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
```

3. Přidáte pravidlo pro sítě pro virtuální síť a podsíť.  
```azurecli
subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
```

4. Odeberte pravidlo sítě pro virtuální síť a podsíť. 
```azurecli
subnetid=$(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
```

> [!IMPORTANT]
> Nezapomeňte [nastavit výchozí pravidlo](#change-the-default-network-access-rule) odepřít, nebo pravidla sítě nebude mít žádný vliv.
>

## <a name="grant-access-from-an-internet-ip-range"></a>Udělení přístupu z Internetu rozsah IP adres
Účty úložiště může být nakonfigurována pro povolení přístupu z konkrétní veřejného Internetu rozsahy IP adres.  Tato konfigurace umožňuje konkrétní internetové služby a místní sítě chcete povolit přístup při obecné přenosy z Internetu je blokovaný.

Povolené rozsahy adres Internetu lze zadat pomocí [notaci CIDR](https://tools.ietf.org/html/rfc4632) ve tvaru *16.17.18.0/24* nebo jako jednotlivé IP adresy jako *16.17.18.19* .

> [!NOTE]
> Rozsahy malé adres pomocí "/ 31" nebo "/ 32" předpona velikosti nejsou podporovány.  Tyto rozsahy musí být nakonfigurovaný pomocí jednotlivých pravidel IP adresu.
>

Pravidla pro sítě IP je povoleno pouze pro **veřejného Internetu** IP adresy.  Rozsahy IP adres vyhrazené pro privátní sítě (podle definice v dokumentu RFC 1918) nejsou povoleny v pravidlech IP.  Privátní sítě zahrnují adresy, které začínají *10.\** , *172.16.\** , a *192.168.\** .

V tuto chvíli jsou podporovány pouze adresy IPV4.

Každý účet úložiště může podporovat až 100 pravidla sítě IP, které mohou být kombinovány s [pravidla virtuální sítě](#grant-access-from-a-virtual-network)

### <a name="configuring-access-from-on-premises-networks"></a>Konfigurace přístupu z místní sítě
Aby bylo možné udělit přístup k vašemu účtu úložiště s pravidlo sítě IP z vaší místní sítě, musíte určit internetové IP adresy používané vaší sítě.  Požádejte o pomoc správce sítě.

Pokud vaše síť připojená k síti Azure pomocí [ExpressRoute](/azure/expressroute/expressroute-introduction), každý okruh je nakonfigurován s dvě veřejné IP adresy v Microsoft Edge používané k připojení ke službě Microsoft Services jako Azure Storage pomocí [Veřejný partnerský vztah azure](/azure/expressroute/expressroute-circuit-peerings#expressroute-routing-domains).  Povolit komunikaci z okruhu do služby Azure Storage, je nutné vytvořit pravidla pro sítě IP pro veřejné IP adresy vaší okruhů.  Aby bylo možné najít okruh ExpressRoute veřejné IP adresy, [otevřete lístek podpory s ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) prostřednictvím portálu Azure.


### <a name="managing-ip-network-rules"></a>Správa pravidel sítě IP
Pravidla pro účty úložiště pro sítě IP je možné spravovat prostřednictvím portálu Azure, PowerShell nebo CLIv2.

#### <a name="azure-portal"></a>Azure Portal
1. Přejděte na účet úložiště, které chcete zabezpečit.  
2. Klikněte v nabídce nastavení, která je volána **brány firewall a virtuální sítě**.
3. Ujistěte se, že jste se rozhodli povolit přístup ze 'vybrané sítě.
4. Udělit přístup k Internetu rozsah IP adres, zadejte IP adresu nebo rozsah adres (ve formátu CIDR) v rámci brány Firewall, rozsahy adres.
5. Chcete-li odebrat pravidlo síť IP, klikněte na tlačítko "..." otevřete kontextu nabídku pro pravidlo a klikněte na "Odebrat".
6. Klikněte na tlačítko *Uložit* proveďte změny.

#### <a name="powershell"></a>PowerShell
1. Nainstalujte si nejnovější verzi [prostředí Azure PowerShell](/powershell/azure/install-azurerm-ps) a [přihlášení](/powershell/azure/authenticate-azureps).
2. Zobrazí seznam pravidel sítě IP.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
```

3. Přidáte pravidlo pro sítě pro jednotlivé IP adresy.  
```PowerShell
Add-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19" 
``` 

4. Přidáte pravidlo pro sítě pro rozsah IP adres.  
```PowerShell
Add-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24" 
```    

5. Odeberte pravidlo sítě pro jednotlivé IP adresy. 
```PowerShell
Remove-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"  
```

6. Odeberte pravidlo sítě pro rozsah IP adres.  
```PowerShell
Remove-AzureRMStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"  
```    

> [!IMPORTANT]
> Nezapomeňte [nastavit výchozí pravidlo](#change-the-default-network-access-rule) odepřít, nebo pravidla sítě nebude mít žádný vliv.
>

#### <a name="cliv2"></a>CLIv2
1. [Instalace rozhraní příkazového řádku Azure 2.0](/cli/azure/install-azure-cli) a [přihlášení](/cli/azure/authenticate-azure-cli).
2. Seznam pravidel sítě IP
```azurecli
az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
```

3. Přidáte pravidlo pro sítě pro jednotlivé IP adresy.
```azurecli
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
```

4. Přidáte pravidlo pro sítě pro rozsah IP adres.  
```azurecli
az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
```

5. Odeberte pravidlo sítě pro jednotlivé IP adresy.  
```azurecli
az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
```

6. Odeberte pravidlo sítě pro rozsah IP adres.  
```azurecli
az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
```

> [!IMPORTANT]
> Nezapomeňte [nastavit výchozí pravidlo](#change-the-default-network-access-rule) odepřít, nebo pravidla sítě nebude mít žádný vliv.
>

## <a name="exceptions"></a>Výjimky
Zatímco pravidla sítě můžete povolit konfigurace zabezpečení sítě pro většinu scénářů, jsou případy, kde musí být udělena výjimky k zajištění plné funkčnosti.  Účty úložiště můžete nakonfigurovat výjimky pro služby Microsoft důvěryhodné a pro přístup k datům analytics úložiště.

### <a name="trusted-microsoft-services"></a>Důvěryhodné služby společnosti Microsoft
Některé služby společnosti Microsoft, které pracují s účty úložiště pracovat ze sítě, které nelze udělit přístup pomocí sítě pravidla. 

Pokud chcete povolit tento typ služby fungovat tak, jak má, můžete povolit sadu na obešla pravidla sítě důvěryhodné služby společnosti Microsoft. Tyto služby pak bude používat silné ověřování pro přístup k účtu úložiště.

Pokud výjimky "Důvěryhodné služby Microsoft" je povoleno, tyto služby (při registraci v rámci vašeho předplatného) mají udělen přístup k účtu úložiště:

|Služba|Název zprostředkovatele prostředků|Účel|
|:------|:---------------------|:------|
|Azure Backup|Microsoft.Backup|Proveďte zálohování a obnovení nespravované disků v virtuální počítače IAAS. (není potřeba pro spravované disky). [Další informace](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup).|
|Azure DevTest Labs|Microsoft.DevTestLab|Vytváření a artefaktů instalace vlastní image.  [Další informace](https://docs.microsoft.com/azure/devtest-lab/devtest-lab-overview).|
|Azure Event Grid|Microsoft.EventGrid|Povolte publikování událostí úložiště objektů Blob.  [Další informace](https://docs.microsoft.com/azure/event-grid/overview).|
|Azure Event Hubs|Microsoft.EventHub|Archivní data s zaznamenat centra událostí.  [Další informace](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview).|
|Sítě Azure|Microsoft.Networking|Ukládat a analyzovat protokoly síťových přenosů.  [Další informace](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview).|
||||

### <a name="storage-analytics-data-access"></a>Přístup k datům analytics úložiště
V některých případech není zapotřebí z mimo hranici sítě přístup ke čtení diagnostické protokoly a metriky.  Výjimky z pravidel sítě lze udělit povolit přístup pro čtení do úložiště účet soubory protokolů, metrik tabulky nebo obojí. [Další informace o práci s analytika úložiště.](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>Správa výjimek
Pravidlo výjimky sítě je možné spravovat prostřednictvím portálu Azure, PowerShell nebo rozhraní příkazového řádku Azure v2.

#### <a name="azure-portal"></a>Azure Portal
1. Přejděte na účet úložiště, které chcete zabezpečit.  
2. Klikněte v nabídce nastavení, která je volána **brány firewall a virtuální sítě**.
3. Ujistěte se, že jste se rozhodli povolit přístup ze 'vybrané sítě.
4. V části výjimky vyberte výjimky, které chcete udělit.
5. Klikněte na tlačítko *Uložit* proveďte změny.

#### <a name="powershell"></a>PowerShell
1. Nainstalujte si nejnovější verzi [prostředí Azure PowerShell](/powershell/azure/install-azurerm-ps) a [přihlášení](/powershell/azure/authenticate-azureps).
2. Zobrazte výjimky pravidel sítě účet úložiště.
```PowerShell
(Get-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
```

3. Nakonfigurujte výjimky pravidla síť účtu úložiště.
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount"  -Bypass AzureServices,Metrics,Logging
```

4. Odeberte výjimky pravidla síť účtu úložiště.
```PowerShell
Update-AzureRmStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount"  -Bypass None
```

> [!IMPORTANT]
> Nezapomeňte [nastavit výchozí pravidlo](#change-the-default-network-access-rule) odepřít, nebo odebrání výjimky nebude mít žádný vliv.
>

#### <a name="cliv2"></a>CLIv2
1. [Instalace rozhraní příkazového řádku Azure 2.0](/cli/azure/install-azure-cli) a [přihlášení](/cli/azure/authenticate-azure-cli).
2. Zobrazte výjimky pravidel sítě účet úložiště.
```azurecli
az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
```

3. Nakonfigurujte výjimky pravidla síť účtu úložiště.
```azurecli
az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
```

4. Odeberte výjimky pravidla síť účtu úložiště.
```azurecli
az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
```

> [!IMPORTANT]
> Nezapomeňte [nastavit výchozí pravidlo](#change-the-default-network-access-rule) odepřít, nebo odebrání výjimky nebude mít žádný vliv.
>

## <a name="next-steps"></a>Další postup
Další informace o koncových bodů služby sítě Azure v [koncové body služby](/azure/virtual-network/virtual-network-service-endpoints-overview).

Dig hlouběji do Azure Storage zabezpečení v [Průvodce zabezpečením úložiště Azure](storage-security-guide.md).
