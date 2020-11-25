---
title: Připojení prostředí Azure-SSIS Integration Runtime k virtuální síti
description: Naučte se připojit prostředí Azure-SSIS Integration runtime k virtuální síti Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/02/2020
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: e73126cfc54294a7b9d54ff62c406d5e686ac470
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95982704"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Připojení prostředí Azure-SSIS Integration Runtime k virtuální síti

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Při použití služba SSIS (SQL Server Integration Services) (SSIS) v Azure Data Factory byste se měli připojit k Azure-SSIS Integration runtime (IR) k virtuální síti Azure v následujících scénářích:

- Chcete se připojit k místním úložištím dat z balíčků SSIS, které běží na vašich Azure-SSIS IR, aniž byste museli konfigurovat nebo spravovat místní prostředí IR jako proxy. 

- Chcete hostovat službu SSIS Catalog Database (SSISDB) v Azure SQL Database s pravidly brány firewall protokolu IP/koncovými body služby virtuální sítě nebo se službou SQL Managed instance s privátním koncovým bodem. 

- Chcete se připojit k prostředkům Azure nakonfigurovaným pomocí koncových bodů služby virtuální sítě z balíčků SSIS, které běží na vašem Azure-SSIS IR.

- Chcete se připojit k úložištím nebo zdrojům dat nakonfigurovaným pomocí pravidel brány firewall protokolu IP z balíčků SSIS, které běží na vašem Azure-SSIS IR.

Data Factory umožňuje připojit se k Azure-SSIS IR k virtuální síti vytvořené prostřednictvím modelu nasazení Classic nebo modelu nasazení Azure Resource Manager.

> [!IMPORTANT]
> Klasická virtuální síť se už nepoužívá, proto místo ní použijte Azure Resource Manager virtuální síť.  Pokud už používáte klasický virtuální síť, přepněte co nejdříve do Azure Resource Manager virtuální sítě.

Kurz [Konfigurace prostředí Azure-služba SSIS (SQL Server Integration Services) (SSIS) Integration runtime (IR) pro připojení k virtuální síti](tutorial-deploy-ssis-virtual-network.md) zobrazuje minimální kroky prostřednictvím Azure Portal. Tento článek se rozbalí v tomto kurzu a popisuje všechny volitelné úlohy:

- Pokud používáte virtuální síť (Classic).
- Pokud přenesete vlastní veřejné IP adresy pro Azure-SSIS IR.
- Pokud používáte vlastní server DNS (Domain Name System).
- Použijete-li v podsíti skupinu zabezpečení sítě (NSG).
- Pokud používáte Azure ExpressRoute nebo trasu definovanou uživatelem (UDR).
- Pokud používáte vlastní Azure-SSIS IR.
- Pokud používáte zřizování Azure PowerShellu.

## <a name="access-to-on-premises-data-stores"></a>Přístup k místním úložištím dat

Pokud vaše SSIS balíčky přistupují k místním úložištím dat, můžete se Azure-SSIS IR připojit k virtuální síti, která je připojená k místní síti. Případně můžete pro Azure-SSIS IR nakonfigurovat a spravovat místní prostředí IR jako proxy. Další informace najdete v tématu [Konfigurace prostředí IR pro místní hostování jako proxy serveru pro Azure-SSIS IR](./self-hosted-integration-runtime-proxy-ssis.md). 

Při připojování k vašemu Azure-SSIS IR k virtuální síti si pamatujte tyto důležité body: 

- Pokud k vaší místní síti není připojená žádná virtuální síť, vytvořte nejdřív [Azure Resource Manager virtuální síť](../virtual-network/quick-create-portal.md#create-a-virtual-network) , pro kterou Azure-SSIS IR připojit. Pak nakonfigurujte [připojení brány VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) typu Site-to-site nebo [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) připojení z této virtuální sítě k místní síti. 

- Pokud je už virtuální síť Azure Resource Manager připojená k vaší místní síti ve stejném umístění jako vaše Azure-SSIS IR, můžete k této virtuální síti připojit IR. 

- Pokud je klasická virtuální síť již připojená k vaší místní síti v jiném umístění, než je vaše Azure-SSIS IR, můžete pro připojení Azure-SSIS IR vytvořit [Azure Resource Manager virtuální síť](../virtual-network/quick-create-portal.md#create-a-virtual-network) . Pak nakonfigurujte připojení [k virtuální síti typu Classic-to-Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) . 
 
- Pokud je už virtuální síť Azure Resource Manager připojená k vaší místní síti v jiném umístění, než je vaše Azure-SSIS IR, můžete nejdřív vytvořit [Azure Resource Manager virtuální síť](../virtual-network/quick-create-portal.md#create-a-virtual-network) , pro kterou Azure-SSIS IR se chcete připojit. Pak nakonfigurujte připojení k virtuální síti Azure Resource Manager-to-Azure Resource Manager. 

## <a name="hosting-the-ssis-catalog-in-sql-database"></a>Hostování katalogu SSIS v SQL Database

Pokud svůj katalog SSIS hostujte v Azure SQL Database pomocí koncových bodů služby virtuální sítě, ujistěte se, že jste připojení Azure-SSIS IR ke stejné virtuální síti a podsíti.

Pokud svůj katalog SSIS Hostujte ve spravované instanci SQL pomocí privátního koncového bodu, nezapomeňte připojit Azure-SSIS IR ke stejné virtuální síti, ale v jiné podsíti, než je spravovaná instance. Pokud chcete připojit Azure-SSIS IR k jiné virtuální síti než SQL Managed instance, doporučujeme buď partnerský vztah virtuální sítě (která je omezená na stejnou oblast), nebo připojení virtuální sítě k virtuální síti. Další informace najdete v tématu [připojení aplikace ke spravované instanci Azure SQL](../azure-sql/managed-instance/connect-application-instance.md).

## <a name="access-to-azure-services"></a>Přístup ke službám Azure

Pokud vaše balíčky SSIS přistupují k prostředkům Azure, které podporují [koncové body služby virtuální sítě](../virtual-network/virtual-network-service-endpoints-overview.md) a chcete zabezpečit přístup k těmto prostředkům z Azure-SSIS IR, můžete se připojit k Azure-SSIS IR k podsíti virtuální sítě nakonfigurované pro koncové body služby virtuální sítě a pak přidat pravidlo virtuální sítě k relevantním prostředkům Azure, aby bylo možné přístup ze stejné podsítě.

## <a name="access-to-data-sources-protected-by-ip-firewall-rule"></a>Přístup ke zdrojům dat chráněným pravidlem brány firewall protokolu IP

Pokud vaše SSIS balíčky přistupují k úložištím nebo prostředkům, které umožňují pouze konkrétní statické veřejné IP adresy, a chcete zabezpečit přístup k těmto prostředkům z Azure-SSIS IR, můžete použít vlastní [veřejné IP adresy](../virtual-network/virtual-network-public-ip-address.md) pro Azure-SSIS IR při připojení k virtuální síti a pak přidat pravidlo brány firewall protokolu IP k příslušným prostředkům, aby bylo možné přístup z těchto IP adres.

Ve všech případech se virtuální síť dá nasadit jenom prostřednictvím modelu nasazení Azure Resource Manager.

Následující části obsahují další podrobnosti. 

## <a name="virtual-network-configuration"></a>Konfigurace virtuální sítě

Nastavte si virtuální síť tak, aby splňovala tyto požadavky: 

- Ujistěte se, že `Microsoft.Batch` je registrovaný poskytovatel v rámci předplatného podsítě virtuální sítě, která je hostitelem Azure-SSIS IR. Pokud používáte klasickou virtuální síť, připojte se také `MicrosoftAzureBatch` k roli Přispěvatel klasických virtuálních počítačů pro tuto virtuální síť. 

- Ujistěte se, že máte požadovaná oprávnění. Další informace najdete v tématu [Nastavení oprávnění](#perms).

- Vyberte správnou podsíť pro hostování Azure-SSIS IR. Další informace najdete v tématu [Výběr podsítě](#subnet). 

- Pokud přenesete vlastní veřejné IP adresy pro Azure-SSIS IR, přečtěte si téma [Výběr statických veřejných IP adres](#publicIP) .

- Pokud ve virtuální síti používáte vlastní server DNS (Domain Name System), přečtěte si téma [nastavení serveru DNS](#dns_server). 

- Pokud v podsíti použijete skupinu zabezpečení sítě (NSG), přečtěte si téma [Nastavení NSG](#nsg). 

- Pokud používáte Azure ExpressRoute nebo trasu definovanou uživatelem (UDR), přečtěte si téma [použití Azure ExpressRoute nebo udr](#route). 

- Pokud přenesete vlastní veřejné IP adresy, ujistěte se, že skupina prostředků virtuální sítě (nebo skupina prostředků veřejné IP adresy) může vytvořit a odstranit určité síťové prostředky Azure. Další informace najdete v tématu [Nastavení skupiny prostředků](#resource-group). 

- Pokud přizpůsobíte Azure-SSIS IR, jak je popsáno v tématu [vlastní nastavení pro Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md), náš interní proces pro správu uzlů bude využívat privátní IP adresy z předdefinovaného rozsahu 172.16.0.0 až 172.31.255.255. V důsledku toho se ujistěte, že rozsahy privátních IP adres ve vašich virtuálních nebo místních sítích nebudou v tomto rozsahu kolidovat.

Tento diagram zobrazuje požadovaná připojení pro vaše Azure-SSIS IR:

![Diagram, který zobrazuje požadovaná připojení pro vaši Azure-SSIS IR.](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="set-up-permissions"></a><a name="perms"></a> Nastavení oprávnění

Uživatel, který vytváří Azure-SSIS IR, musí mít následující oprávnění:

- Pokud se připojujete k SSIS IR k virtuální síti Azure Resource Manager, máte dvě možnosti:

  - Použijte integrovanou roli Přispěvatel sítě. Tato role se dodává s oprávněním _Microsoft. \* Network/_ , které má mnohem větší rozsah, než je nutné.

  - Vytvořte vlastní roli, která bude obsahovat jenom potřebná oprávnění _Microsoft. Network/virtualNetworks/ \* /Join/Action_ . Pokud chcete pro Azure-SSIS IR při připojení k virtuální síti Azure Resource Manager využít vlastní veřejné IP adresy, uveďte také v roli oprávnění _Microsoft. Network/publicIPAddresses/*/JOIN/Action_ .

- Pokud se připojujete k SSIS IR k klasické virtuální síti, doporučujeme použít integrovanou roli přispěvatele klasického virtuálního počítače. V opačném případě musíte definovat vlastní roli, která zahrnuje oprávnění k připojení k virtuální síti.

### <a name="select-the-subnet"></a><a name="subnet"></a> Vyberte podsíť.

Při volbě podsítě: 

- Nevybírejte GatewaySubnet pro nasazení Azure-SSIS IR. Je vyhrazený pro brány virtuální sítě. 

- Ujistěte se, že vybraná podsíť má dostatek dostupného adresního prostoru, aby bylo možné Azure-SSIS IR použít. Nechte dostupné IP adresy aspoň po dobu nejméně dvojnásobku čísla uzlu IR. Azure rezervuje některé IP adresy v každé podsíti. Tyto adresy se nedají použít. První a poslední IP adresa podsítí jsou vyhrazené pro shodu protokolu a pro služby Azure se používají tři další adresy. Další informace najdete v tématu [jakákoli omezení používání IP adres v těchto podsítích](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) . 

- Nepoužívejte podsíť, která je exkluzivně zabraná jinými službami Azure (například SQL Database spravované instance SQL, App Service atd.). 

### <a name="select-the-static-public-ip-addresses"></a><a name="publicIP"></a>Vyberte statické veřejné IP adresy.

Pokud chcete použít vlastní statické veřejné IP adresy pro Azure-SSIS IR při připojení k virtuální síti, ujistěte se, že splňují následující požadavky:

- Měla by se zadat přesně dvě nepoužitá ta, která ještě nejsou přidružená k ostatním prostředkům Azure. Ta, která se používá, se použije při pravidelném upgradu vašeho Azure-SSIS IR. Upozorňujeme, že jedna veřejná IP adresa se nedá sdílet mezi aktivním úřadem Azure-SSIS.

- Měly by být oba staticky typu Standard. Další podrobnosti najdete v [části SKU veřejné IP adresy](../virtual-network/public-ip-addresses.md#sku) .

- Měly by mít název DNS. Pokud jste nezadali název DNS při jeho vytváření, můžete tak učinit na Azure Portal.

![Prostředí Azure-SSIS IR](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

- A virtuální síť by měly být v rámci stejného předplatného a ve stejné oblasti.

### <a name="set-up-the-dns-server"></a><a name="dns_server"></a> Nastavení serveru DNS 
Pokud potřebujete použít vlastní server DNS ve virtuální síti připojené pomocí Azure-SSIS IR k překladu názvu privátního hostitele, ujistěte se, že dokáže přeložit i globální názvy hostitelů Azure (například objekt blob Azure Storage s názvem `<your storage account>.blob.core.windows.net` ). 

Jeden doporučený postup je následující: 

-   Nakonfigurujte vlastní DNS pro přeposílání požadavků na Azure DNS. Nevyřešené záznamy DNS můžete přesměrovat na IP adresu rekurzivních překladačů Azure (168.63.129.16) na vlastním serveru DNS. 

Další informace najdete v tématu [překlad názvů, který používá vlastní server DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). 

> [!NOTE]
> Jako název privátního hostitele použijte plně kvalifikovaný název domény (FQDN) (například použijte `<your_private_server>.contoso.com` místo `<your_private_server>` ). Alternativně můžete použít standardní vlastní instalaci v Azure-SSIS IR k automatickému připojení vlastní přípony DNS (například `contoso.com` k libovolnému nekvalifikovanému názvu domény s názvem bez přípony) a převeďte ho na plně kvalifikovaný název domény, než ho použijete v dotazech DNS. Další informace najdete v tématu [standardní ukázky instalačních vlastních nastavení](./how-to-configure-azure-ssis-ir-custom-setup.md#standard-custom-setup-samples). 

### <a name="set-up-an-nsg"></a><a name="nsg"></a> Nastavení NSG
Pokud potřebujete implementovat NSG pro podsíť, kterou používá vaše Azure-SSIS IR, povolte příchozí a odchozí provoz prostřednictvím následujících portů: 

-   **Příchozí požadavek Azure-SSIS IR**

| Směr | Transportní protokol | Zdroj | Rozsah zdrojových portů | Cíl | Rozsah cílových portů | Komentáře |
|---|---|---|---|---|---|---|
| Příchozí | TCP | BatchNodeManagement | * | VirtualNetwork | 29876, 29877 (Pokud se připojíte k IR k virtuální síti Správce prostředků) <br/><br/>10100, 20100, 30100 (Pokud se připojíte k IR k klasické virtuální síti)| Služba Data Factory tyto porty používá ke komunikaci s uzly vašeho Azure-SSIS IR ve virtuální síti. <br/><br/> Bez ohledu na to, jestli vytvoříte NSG na úrovni podsítě, Data Factory vždycky nakonfiguruje NSG na úrovni síťových rozhraní (nic) připojených k virtuálním počítačům, které hostují Azure-SSIS IR. Pouze příchozí provoz z Data Factory IP adres na zadaných portech je povolený pro NSG na úrovni síťových adaptérů. I když tyto porty otevřete pro internetový provoz na úrovni podsítě, provoz z IP adres, které nejsou Data Factory IP adresy, se zablokuje na úrovni síťové karty. |
| Příchozí | TCP | CorpNetSaw | * | VirtualNetwork | 3389 | Volitelné Toto pravidlo se vyžaduje jenom v případě, že je zákazníkem podpory Microsoftu požádat o řešení potíží a dá se hned po vyřešení potíží zavřít. Značka služby **CorpNetSaw** povoluje použití vzdálené plochy jenom pro pracovní stanice s zabezpečeným přístupem v podnikové síti Microsoftu. A tento tag služby nejde vybrat z portálu a je dostupný jenom přes Azure PowerShell nebo Azure CLI. <br/><br/> Na úrovni síťové karty je ve výchozím nastavení otevřený port 3389 a my vám umožní řídit port 3389 na úrovni podsítě NSG, mezitím Azure-SSIS IR ve výchozím nastavení v pravidle brány Windows Firewall u každého uzlu infračerveného přenosu nepovolený port 3389 odchozí. |
||||||||

-   **Odchozí požadavek Azure-SSIS IR**

| Směr | Transportní protokol | Zdroj | Rozsah zdrojových portů | Cíl | Rozsah cílových portů | Komentáře |
|---|---|---|---|---|---|---|
| Odchozí | TCP | VirtualNetwork | * | AzureCloud | 443 | Uzly vašeho Azure-SSIS IR ve virtuální síti používají tento port pro přístup ke službám Azure, jako je například Azure Storage a Azure Event Hubs. |
| Odchozí | TCP | VirtualNetwork | * | Internet | 80 | Volitelné Uzly vašeho Azure-SSIS IR ve virtuální síti používají tento port ke stažení seznamu odvolaných certifikátů z Internetu. Pokud zablokujete tento provoz, může dojít ke snížení výkonu při spuštění prostředí IR a ke ztrátě možností použití certifikátu pro kontrolu seznamu odvolaných certifikátů. Pokud chcete cíl dále zúžit na určité plně kvalifikované názvy domény, přečtěte si téma **použití Azure ExpressRoute nebo udr** .|
| Odchozí | TCP | VirtualNetwork | * | Sql | 1433, 11000-11999 | Volitelné Toto pravidlo se vyžaduje jenom v případě, že uzly Azure-SSIS IR ve virtuální síti přístup k SSISDB hostovanému vaším serverem. Pokud je zásada připojení k serveru nastavená na **proxy** místo **přesměrování**, je potřeba jenom port 1433. <br/><br/> Toto odchozí pravidlo zabezpečení se nevztahuje na SSISDB, které hostuje vaše spravovaná instance SQL ve virtuální síti nebo SQL Database nakonfigurovaná pomocí privátního koncového bodu. |
| Odchozí | TCP | VirtualNetwork | * | VirtualNetwork | 1433, 11000-11999 | Volitelné Toto pravidlo se vyžaduje jenom v případě, že uzly vaší Azure-SSIS IR ve virtuální síti přistupují k SSISDB hostovanému vaší spravovanou instancí SQL ve virtuální síti nebo SQL Database nakonfigurované pomocí privátního koncového bodu. Pokud je zásada připojení k serveru nastavená na **proxy** místo **přesměrování**, je potřeba jenom port 1433. |
| Odchozí | TCP | VirtualNetwork | * | Storage | 445 | Volitelné Toto pravidlo se vyžaduje jenom v případě, že chcete spustit balíček SSIS uložený ve službě soubory Azure. |
||||||||

### <a name="use-azure-expressroute-or-udr"></a><a name="route"></a> Použití Azure ExpressRoute nebo UDR
Pokud chcete zkontrolovat odchozí provoz z Azure-SSIS IR, můžete směrovat provoz iniciované z Azure-SSIS IR na místní zařízení brány firewall prostřednictvím tunelového připojení [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) Force (inzerování trasy protokolu BGP, 0.0.0.0/0, virtuální sítě) nebo síťového virtuálního zařízení (síťové virtuální zařízení) jako brány firewall nebo [Azure firewall](../firewall/index.yml) prostřednictvím [udr](../virtual-network/virtual-networks-udr-overview.md). 

![Scénář síťové virtuální zařízení pro Azure-SSIS IR](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir-nva.png)

Chcete-li udělat celý scénář, musíte provést následující kroky.
   -   Příchozí provoz mezi Azure Batch Management Services a Azure-SSIS IR se nedá směrovat přes zařízení brány firewall.
   -   Zařízení brány firewall musí umožňovat odchozí provoz vyžadovaný Azure-SSIS IR.

Příchozí provoz mezi Azure Batch Management Services a Azure-SSIS IR není možné směrovat do zařízení brány firewall. v důsledku problému s asymetrickým směrováním dojde k přerušení provozu. Trasy musí být definovány pro příchozí provoz, aby přenos mohl odpovídat stejnému způsobu, jakým přišel. Můžete definovat konkrétní udr ke směrování provozu mezi Azure Batch služby správy a Azure-SSIS IR s typem dalšího segmentu směrování jako **Internet**.

Pokud se například vaše Azure-SSIS IR nachází v `UK South` a chcete kontrolovat odchozí přenosy prostřednictvím Azure firewall, měli byste nejprve získat seznam rozsahů IP adres služby `BatchNodeManagement.UKSouth` z [odkazu ke stažení z rozsahu IP adres značek služby](https://www.microsoft.com/download/details.aspx?id=56519) nebo prostřednictvím [rozhraní API pro zjišťování značek služby](../virtual-network/service-tags-overview.md#service-tags-on-premises). Pak použijte následující udr trasy rozsahu IP adres s typem dalšího segmentu směrování jako **Internet** společně s trasou 0.0.0.0/0 s typem dalšího segmentu směrování jako **virtuální zařízení**.

![Nastavení UDR Azure Batch](media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png)

> [!NOTE]
> U tohoto přístupu se vyskytnou další náklady na údržbu. Pravidelně kontrolujte rozsah IP adres a přidejte do svého UDR nové rozsahy IP adres, abyste se vyhnuli přerušení Azure-SSIS IR. Doporučujeme, abyste provedli kontrolu rozsahu IP adres měsíčně, protože když se nová IP adresa objeví ve značce služby, bude trvat další měsíc. 

Aby bylo nastavení pravidel UDR snazší, můžete spuštěním následujícího skriptu PowerShellu přidat pravidla UDR pro Azure Batch služby pro správu:
```powershell
$Location = "[location of your Azure-SSIS IR]"
$RouteTableResourceGroupName = "[name of Azure resource group that contains your Route Table]"
$RouteTableResourceName = "[resource name of your Azure Route Table ]"
$RouteTable = Get-AzRouteTable -ResourceGroupName $RouteTableResourceGroupName -Name $RouteTableResourceName
$ServiceTags = Get-AzNetworkServiceTag -Location $Location
$BatchServiceTagName = "BatchNodeManagement." + $Location
$UdrRulePrefixForBatch = $BatchServiceTagName
if ($ServiceTags -ne $null)
{
    $BatchIPRanges = $ServiceTags.Values | Where-Object { $_.Name -ieq $BatchServiceTagName }
    if ($BatchIPRanges -ne $null)
    {
        Write-Host "Start to add rule for your route table..."
        for ($i = 0; $i -lt $BatchIPRanges.Properties.AddressPrefixes.Count; $i++)
        {
            $UdrRuleName = "$($UdrRulePrefixForBatch)_$($i)"
            Add-AzRouteConfig -Name $UdrRuleName `
                -AddressPrefix $BatchIPRanges.Properties.AddressPrefixes[$i] `
                -NextHopType "Internet" `
                -RouteTable $RouteTable `
                | Out-Null
            Write-Host "Add rule $UdrRuleName to your route table..."
        }
        Set-AzRouteTable -RouteTable $RouteTable
    }
}
else
{
    Write-Host "Failed to fetch service tags, please confirm that your Location is valid."
}
```

Aby bylo možné u zařízení s bránou firewall povolený odchozí provoz, je nutné v odchozích pravidlech NSG povolené odchozí přenosy pod porty stejné jako požadavek.
-   Port 443 s cílem jako Azure Cloud Services.

    Pokud používáte Azure Firewall, můžete zadat síťové pravidlo s označením služby AzureCloud. Pro bránu firewall ostatních typů můžete buď jednoduše zapnout cíl jako vše pro port 443, nebo můžete v závislosti na typu vašeho prostředí Azure povolovat tyto plně kvalifikované názvy domény:

    | Prostředí Azure | Koncové body                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Veřejný partnerský vztah Azure      | <ul><li><b>Azure Data Factory (Správa)</b><ul><li>\*. frontend.clouddatahub.net</li></ul></li><li><b>Azure Storage (Správa)</b><ul><li>\*.blob.core.windows.net</li><li>\*. table.core.windows.net</li></ul></li><li><b>Azure Container Registry (vlastní nastavení)</b><ul><li>\*. azurecr.io</li></ul></li><li><b>Centrum událostí (protokolování)</b><ul><li>\*. servicebus.windows.net</li></ul></li><li><b>Služba protokolování společnosti Microsoft (interní použití)</b><ul><li>gcs.prod.monitoring.core.windows.net</li><li>prod.warmpath.msftcloudes.com</li><li>azurewatsonanalysis-prod.core.windows.net</li></ul></li></ul> |
    | Azure Government  | <ul><li><b>Azure Data Factory (Správa)</b><ul><li>\*. frontend.datamovement.azure.us</li></ul></li><li><b>Azure Storage (Správa)</b><ul><li>\*. blob.core.usgovcloudapi.net</li><li>\*. table.core.usgovcloudapi.net</li></ul></li><li><b>Azure Container Registry (vlastní nastavení)</b><ul><li>\*. azurecr.us</li></ul></li><li><b>Centrum událostí (protokolování)</b><ul><li>\*. servicebus.usgovcloudapi.net</li></ul></li><li><b>Služba protokolování společnosti Microsoft (interní použití)</b><ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>azurewatsonanalysis.usgovcloudapp.net</li></ul></li></ul> |
    | Azure (Čína) 21Vianet     | <ul><li><b>Azure Data Factory (Správa)</b><ul><li>\*. frontend.datamovement.azure.cn</li></ul></li><li><b>Azure Storage (Správa)</b><ul><li>\*. blob.core.chinacloudapi.cn</li><li>\*. table.core.chinacloudapi.cn</li></ul></li><li><b>Azure Container Registry (vlastní nastavení)</b><ul><li>\*. azurecr.cn</li></ul></li><li><b>Centrum událostí (protokolování)</b><ul><li>\*. servicebus.chinacloudapi.cn</li></ul></li><li><b>Služba protokolování společnosti Microsoft (interní použití)</b><ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>azurewatsonanalysis.chinacloudapp.cn</li></ul></li></ul> |

    U plně kvalifikovaných názvů domén Azure Storage Azure Container Registry a centra událostí můžete také povolit pro virtuální síť tyto koncové body služby, aby síťový provoz do těchto koncových bodů procházel přes páteřní síť Azure místo přesměrování na zařízení brány firewall:
    -  Microsoft.Storage
    -  Microsoft. ContainerRegistry
    -  Microsoft. EventHub


-   Port 80 s cílovým umístěním jako servery pro stažení seznamu CRL.

    V Azure-SSIS IR případě, že se použijete jako seznam odvolaných certifikátů (seznam odvolaných certifikátů), budete mít povolený následující počet plně kvalifikovaných názvů domén:
    -  crl.microsoft.com:80
    -  mscrl.microsoft.com:80
    -  crl3.digicert.com:80
    -  crl4.digicert.com:80
    -  ocsp.digicert.com:80
    -  cacerts.digicert.com:80
    
    Pokud používáte certifikáty s jiným seznamem CRL, je vhodné je zahrnout také. Tuto možnost si můžete přečíst, abyste lépe pochopili [seznam odvolaných certifikátů](https://social.technet.microsoft.com/wiki/contents/articles/2303.understanding-access-to-microsoft-certificate-revocation-list.aspx).

    Pokud tento provoz zakážete, může dojít ke snížení výkonu při spuštění Azure-SSIS IR a ztratí se možnost kontrolovat seznam odvolaných certifikátů pro použití certifikátu, který se nedoporučuje z hlediska zabezpečení.

-   Port 1433, 11000-11999 s cílovým umístěním Azure SQL Database (vyžaduje se pouze v případě, že uzly vaší Azure-SSIS IR ve virtuální síti přistupují k SSISDB hostovanému vaším serverem).

    Pokud používáte Azure Firewall, můžete zadat síťové pravidlo se značkou služby Azure SQL, jinak můžete v zařízení brány firewall nastavit cíl jako konkrétní adresu URL Azure SQL.

-   Port 445 s cílovým umístěním Azure Storage (vyžaduje se pouze při spuštění balíčku SSIS uloženého ve službě soubory Azure).

    Pokud používáte Azure Firewall, můžete zadat síťové pravidlo s označením služby úložiště, jinak můžete v zařízení brány firewall nastavit cíl jako konkrétní adresu URL služby Azure File Storage.

> [!NOTE]
> Pokud pro Azure SQL a službu Storage nakonfigurujete koncové body služby Virtual Network ve vaší podsíti, přenosy mezi Azure-SSIS IR a Azure SQL ve stejné oblasti \ Azure Storage ve stejné oblasti nebo spárované oblasti budou směrovány do Microsoft Azure páteřní sítě přímo místo zařízení brány firewall.

Pokud nepotřebujete mít možnost kontrolovat odchozí provoz Azure-SSIS IR, můžete jednoduše použít směrování a vynutit veškerý provoz na typ dalšího segmentu směrování **Internet**:

-   V případě scénáře Azure ExpressRoute můžete použít trasu 0.0.0.0/0 s typem dalšího segmentu směrování jako **Internet** v podsíti, která hostuje Azure-SSIS IR. 
-   Ve scénáři síťové virtuální zařízení můžete upravit existující trasu 0.0.0.0/0 použitou v podsíti, která hostuje Azure-SSIS IR z dalšího typu segmentu směrování jako **virtuální zařízení** na **Internet**.

![Přidat trasu](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

> [!NOTE]
> Zadání trasy s typem dalšího segmentu směrování **Internet** neznamená, že veškerý provoz bude přecházet přes Internet. Pokud je cílová adresa pro jednu ze služeb Azure, Azure směruje provoz přímo do služby přes páteřní síť Azure místo směrování provozu na Internet.

### <a name="set-up-the-resource-group"></a><a name="resource-group"></a> Nastavení skupiny prostředků

Azure-SSIS IR musí vytvořit určité síťové prostředky ve stejné skupině prostředků jako virtuální síť. Mezi tyto prostředky patří:
- Nástroj pro vyrovnávání zatížení Azure s názvem *\<Guid> -azurebatch-cloudserviceloadbalancer*.
- Veřejná IP adresa Azure s názvem *\<Guid> -azurebatch-cloudservicepublicip*.
- Skupina zabezpečení práce v síti s názvem *\<Guid> -azurebatch-cloudservicenetworksecuritygroup*. 

> [!NOTE]
> Teď můžete využít vlastní statické veřejné IP adresy pro Azure-SSIS IR. V tomto scénáři vytvoříme jenom skupinu zabezpečení pro vyrovnávání zatížení Azure a skupinu zabezpečení sítě ve stejné skupině prostředků jako vaše statické veřejné IP adresy místo virtuální sítě.

Tyto prostředky budou vytvořeny při spuštění Azure-SSIS IR. Po zastavení Azure-SSIS IR se odstraní. Pokud přenesete vlastní statické veřejné IP adresy pro Azure-SSIS IR, vaše vlastní statické veřejné IP adresy se po zastavení Azure-SSIS IR neodstraní. Chcete-li zabránit zablokování Azure-SSIS IR zastavovat, nepoužívejte tyto síťové prostředky v jiných prostředcích.

Ujistěte se, že nemáte zámek prostředků v rámci skupiny prostředků nebo předplatného, ke kterému patří virtuální síť nebo vaše statické veřejné IP adresy. Pokud nakonfigurujete zámek jen pro čtení nebo odstranění, začnete Azure-SSIS IR a zastavuje se, nebo přestane reagovat.

Ujistěte se, že nemáte přiřazení Azure Policy, které brání vytvoření následujících prostředků v rámci skupiny prostředků nebo předplatného, ke které patří virtuální síť/vaše statické veřejné IP adresy: 
- Microsoft. Network/LoadBalancers 
- Microsoft. Network/NetworkSecurityGroups 
- Microsoft. Network/PublicIPAddresses 

Ujistěte se, že je kvóta prostředků vašeho předplatného dostatečná pro výše uvedené tři síťové prostředky. Konkrétně pro každý Azure-SSIS IR vytvořený ve virtuální síti musíte pro každý z výše uvedených tří síťových prostředků rezervovat dvě bezplatné kvóty. Při pravidelném upgradu vašeho Azure-SSIS IR se použije jedna kvóta navíc.

### <a name="faq"></a><a name="faq"></a> DOTAZ

- Jak můžu chránit veřejnou IP adresu zveřejněnou na mém Azure-SSIS IR pro příchozí připojení? Je možné odebrat veřejnou IP adresu?
 
  V současné době se při Azure-SSIS IR připojení k virtuální síti automaticky vytvoří veřejná IP adresa. K dispozici je NSG na úrovni síťové karty, aby bylo možné pouze příchozí připojení k vašemu Azure-SSIS IR Azure Batch služby pro správu. Pro příchozí ochranu můžete zadat také NSG na úrovni podsítě.

  Pokud nechcete, aby se žádná veřejná IP adresa nastavila, zvažte možnost [nakonfigurovat pro svůj Azure-SSIS IR místní prostředí IR jako proxy](./self-hosted-integration-runtime-proxy-ssis.md) místo připojení Azure-SSIS IR k virtuální síti, pokud to platí pro váš scénář.
 
- Můžu přidat veřejnou IP adresu mých Azure-SSIS IR do seznamu povolených adres brány firewall pro moje zdroje dat?

  Teď můžete využít vlastní statické veřejné IP adresy pro Azure-SSIS IR. V takovém případě můžete přidat IP adresy do seznamu povolených bran firewall pro vaše zdroje dat. K zabezpečení přístupu k datům z Azure-SSIS IR v závislosti na vašem scénáři můžete také zvážit další možnosti:

  - Pokud je váš zdroj dat místní, po připojení virtuální sítě k místní síti a připojení Azure-SSIS IR k podsíti virtuální sítě můžete přidat rozsah privátních IP adres této podsítě do seznamu povolených adres brány firewall pro váš zdroj dat.
  - Pokud je zdrojem dat služba Azure, která podporuje koncové body služby virtuální sítě, můžete v podsíti virtuální sítě nakonfigurovat koncový bod služby virtuální sítě a připojit Azure-SSIS IR k této podsíti. Pak můžete přidat pravidlo virtuální sítě s touto podsítí do brány firewall pro zdroj dat.
  - Pokud je váš zdroj dat cloudovou službou mimo Azure, můžete použít UDR ke směrování odchozího provozu z vaší Azure-SSIS IR do síťové virtuální zařízení/Azure Firewall prostřednictvím statické veřejné IP adresy. Pak můžete přidat statickou veřejnou IP adresu vašeho síťové virtuální zařízení nebo Azure Firewall do seznamu povolených adres brány firewall pro váš zdroj dat.
  - Pokud žádná z výše uvedených možností nevyhovuje vašim potřebám, zvažte [konfiguraci místního prostředí IR jako proxy serveru pro váš Azure-SSIS IR](./self-hosted-integration-runtime-proxy-ssis.md). Pak můžete přidat statickou veřejnou IP adresu počítače, který je hostitelem místního prostředí IR, do seznamu povolených v bráně firewall pro váš zdroj dat.

- Proč potřebuji zadat dvě statické veřejné adresy, pokud chci Azure-SSIS IR vlastní?

  Azure-SSIS IR se pravidelně aktualizuje v pravidelných intervalech. Během upgradu se vytvoří nové uzly a staré se odstraní. Aby ale nedocházelo k výpadkům, staré uzly se neodstraní, dokud nebudou nové připravené. Vaše první statická veřejná IP adresa, kterou používají staré uzly, proto nebude možné vydat hned a k vytvoření nových uzlů potřebujeme vaši druhou statickou veřejnou IP adresu.

- Mám vlastní statické veřejné IP adresy pro Azure-SSIS IR, ale proč stále nemá přístup ke zdrojům dat?

  - Ověřte, že se obě statické veřejné IP adresy přidaly do seznamu povolených adres brány firewall pro vaše zdroje dat. Pokaždé, když je upgrade Azure-SSIS IR, statická veřejná IP adresa se přepíná mezi dvěma dodanými. Pokud přidáte do seznamu povolených aplikací jenom jeden z nich, bude po svém upgradu přerušený přístup k datům pro váš Azure-SSIS IR.
  - Pokud je zdrojem dat služba Azure, zkontrolujte prosím, jestli jste ji nakonfigurovali pomocí koncových bodů služby virtuální sítě. V takovém případě se přenos z Azure-SSIS IR do vašeho zdroje dat přepne na použití privátních IP adres spravovaných službami Azure a přidáním vlastních statických veřejných IP adres do seznamu povolených adres brány firewall pro váš zdroj dat se neprojeví.

## <a name="azure-portal-data-factory-ui"></a>Azure Portal (uživatelské rozhraní Data Factory)

V této části se dozvíte, jak připojit existující Azure-SSIS IR k virtuální síti (Classic nebo Azure Resource Manager) pomocí uživatelského rozhraní Azure Portal a Data Factory. 

Předtím, než se připojíte k vašemu Azure-SSIS IR k virtuální síti, musíte správně nakonfigurovat virtuální síť. Postupujte podle kroků v části, které se vztahují k vašemu typu virtuální sítě (Classic nebo Azure Resource Manager). Pak postupujte podle kroků v třetí části a připojte se k Azure-SSIS IR k virtuální síti. 

### <a name="configure-an-azure-resource-manager-virtual-network"></a>Konfigurace Azure Resource Manager virtuální sítě

Použijte portál ke konfiguraci Azure Resource Manager virtuální sítě před tím, než se pokusíte připojit k Azure-SSIS IR.

1. Spusťte Microsoft Edge nebo Google Chrome. V současné době pouze tyto webové prohlížeče podporují rozhraní Data Factory. 

1. Přihlaste se na [Azure Portal](https://portal.azure.com). 

1. Vyberte **Další služby**. Vyfiltrujte a vyberte **virtuální sítě**. 

1. Vyfiltrujte a vyberte svou virtuální síť v seznamu. 

1. Na stránce **virtuální síť** vyberte **vlastnosti**. 

1. Pro zkopírování ID prostředku pro virtuální síť do schránky vyberte tlačítko Kopírovat pro **ID prostředku** . Uloží ID ze schránky ve OneNotu nebo v souboru. 

1. V nabídce vlevo vyberte **podsítě**. Ujistěte se, že počet dostupných adres je větší než uzly ve vašem Azure-SSIS IR. 

1. Ověřte, jestli je poskytovatel Azure Batch zaregistrovaný v předplatném Azure, které má virtuální síť. Nebo zaregistrujte poskytovatele Azure Batch. Pokud už máte účet Azure Batch v předplatném, předplatné se zaregistruje pro Azure Batch. (Pokud vytvoříte Azure-SSIS IR na portálu Data Factory, poskytovatel Azure Batch se automaticky zaregistruje.) 

   1. V Azure Portal v nabídce vlevo vyberte **předplatná**. 

   1. Vyberte své předplatné. 

   1. Na levé straně vyberte **poskytovatelé prostředků** a potvrďte, že **Microsoft.Batch** je registrovaný poskytovatel. 

   ![Potvrzení stavu "zaregistrován"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Pokud v seznamu nevidíte **Microsoft.Batch** a zaregistrujete ho, vytvořte ve svém předplatném [prázdný Azure Batch účet](../batch/batch-account-create-portal.md) . Můžete ho odstranit později. 

### <a name="configure-a-classic-virtual-network"></a>Konfigurace klasické virtuální sítě

Než se pokusíte připojit k Azure-SSIS IR, použijte portál ke konfiguraci klasické virtuální sítě. 

1. Spusťte Microsoft Edge nebo Google Chrome. V současné době pouze tyto webové prohlížeče podporují rozhraní Data Factory. 

1. Přihlaste se na [Azure Portal](https://portal.azure.com). 

1. Vyberte **Další služby**. Vyfiltrujte a vyberte **virtuální sítě (klasické)**. 

1. Vyfiltrujte a vyberte svou virtuální síť v seznamu. 

1. Na stránce **virtuální síť (Classic)** vyberte **vlastnosti**. 

   ![ID prostředku klasické virtuální sítě](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. Pro zkopírování ID prostředku pro klasickou síť do schránky vyberte tlačítko Kopírovat pro **ID prostředku** . Uloží ID ze schránky ve OneNotu nebo v souboru. 

1. V nabídce vlevo vyberte **podsítě**. Ujistěte se, že počet dostupných adres je větší než uzly ve vašem Azure-SSIS IR. 

   ![Počet dostupných adres ve virtuální síti](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. Připojte se **MicrosoftAzureBatch** k roli **Přispěvatel klasických virtuálních počítačů** pro virtuální síť. 

   1. V nabídce vlevo vyberte **řízení přístupu (IAM)** a vyberte kartu **přiřazení rolí** . 

       ![Tlačítka řízení přístupu a přidat](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

   1. Vyberte **Přidat přiřazení role**.

   1. Na stránce **Přidat přiřazení role** vyberte pro **role** možnost **Přispěvatel klasických virtuálních počítačů**. Do pole **Vybrat** vložte **ddbf3205-c6bd-46ae-8127-60eb93363864** a potom v seznamu výsledků hledání vyberte **Microsoft Azure Batch** . 

       ![Výsledky hledání na stránce přidat přiřazení role](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

   1. Vyberte **Uložit** a uložte nastavení a zavřete stránku. 

       ![Uložit nastavení přístupu](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

   1. Ověřte, že se v seznamu přispěvatelů zobrazuje **Microsoft Azure Batch** . 

       ![Potvrdit přístup k Azure Batch](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. Ověřte, jestli je poskytovatel Azure Batch zaregistrovaný v předplatném Azure, které má virtuální síť. Nebo zaregistrujte poskytovatele Azure Batch. Pokud už máte účet Azure Batch v předplatném, předplatné se zaregistruje pro Azure Batch. (Pokud vytvoříte Azure-SSIS IR na portálu Data Factory, poskytovatel Azure Batch se automaticky zaregistruje.) 

   1. V Azure Portal v nabídce vlevo vyberte **předplatná**. 

   1. Vyberte své předplatné. 

   1. Na levé straně vyberte **poskytovatelé prostředků** a potvrďte, že **Microsoft.Batch** je registrovaný poskytovatel. 

   ![Potvrzení stavu "zaregistrován"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Pokud v seznamu nevidíte **Microsoft.Batch** a zaregistrujete ho, vytvořte ve svém předplatném [prázdný Azure Batch účet](../batch/batch-account-create-portal.md) . Můžete ho odstranit později. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Připojit Azure-SSIS IR k virtuální síti

Po nakonfigurování Azure Resource Manager virtuální sítě nebo klasické virtuální sítě se můžete připojit k Azure-SSIS IR k virtuální síti:

1. Spusťte Microsoft Edge nebo Google Chrome. V současné době pouze tyto webové prohlížeče podporují rozhraní Data Factory. 

1. V [Azure Portal](https://portal.azure.com)v nabídce vlevo vyberte **Datové továrny**. Pokud v nabídce nevidíte **Datové továrny** , vyberte **Další služby** a potom v části **Intelligence + Analytics** vyberte **Datové továrny**. 

   ![Seznam datových továren](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Vyberte datovou továrnu pomocí Azure-SSIS IR v seznamu. Zobrazí se Domovská stránka pro datovou továrnu. Vyberte dlaždici **Author & monitor** . Na samostatné kartě se zobrazí uživatelské rozhraní Data Factory. 

   ![Domovská stránka objektu pro vytváření dat](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. V uživatelském rozhraní Data Factory přepněte na kartu **Upravit** , vyberte **připojení** a přepněte na kartu **prostředí Integration runtime** . 

   ![Karta prostředí Integration runtime](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Pokud je váš Azure-SSIS IR spuštěný, vyberte v seznamu **Integration runtime** ve sloupci **Akce** tlačítko **zastavit** pro vaši Azure-SSIS IR. Azure-SSIS IR dokud ho nezastavíte, nemůžete ho upravovat. 

   ![Zastavit IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. V seznamu **prostředí Integration runtime** vyberte ve sloupci **Akce** tlačítko **Upravit** pro Azure-SSIS IR. 

   ![Úprava prostředí Integration runtime](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. V instalačním panelu prostředí Integration runtime se v sekcích **Obecné nastavení** a **nastavení SQL** pokračujte výběrem tlačítka **Další** . 

1. V části **Upřesnit nastavení** : 

   1. Vyberte **virtuální síť, ke které se má Azure-SSIS Integration runtime připojit, umožněte ADF vytvořit určité síťové prostředky a volitelně uveďte vlastní statické veřejné IP adresy** . 

   1. V poli **předplatné** vyberte předplatné Azure, které má vaši virtuální síť.

   1. Pro **umístění** je vybrané stejné umístění prostředí Integration runtime.

   1. Jako **typ** vyberte typ virtuální sítě: classic nebo Azure Resource Manager. Doporučujeme vybrat Azure Resource Manager virtuální síť, protože klasické virtuální sítě budou brzy zastaralé.

   1. V poli **název virtuální** sítě vyberte název vaší virtuální sítě. Měla by být stejná jako ta, která se používá pro SQL Database s koncovými body služby virtuální sítě nebo spravovanou instancí SQL s privátním koncovým bodem pro hostování SSISDB. Nebo by měl být stejný jako připojený k vaší místní síti. V opačném případě může být libovolná virtuální síť, která bude obsahovat vlastní statické veřejné IP adresy pro Azure-SSIS IR.

   1. Jako **název podsítě** vyberte název podsítě pro virtuální síť. Měla by být stejná jako ta, která se používá pro SQL Database s koncovými body služby virtuální sítě pro hostování SSISDB. Nebo by měla být jiná podsíť než ta, která se používá pro spravovanou instanci SQL s privátním koncovým bodem pro hostování SSISDB. V opačném případě může to být jakákoli podsíť, která přináší vlastní statické veřejné IP adresy pro Azure-SSIS IR.

   1. Zaškrtněte políčko **přenést statické veřejné IP adresy pro Azure-SSIS Integration runtime** a zvolte, jestli chcete použít vlastní statické veřejné IP adresy pro Azure-SSIS IR, abyste je mohli v bráně firewall pro zdroje dat zapnout.

      Pokud zaškrtnete políčko, proveďte následující kroky.

      1. U **první statické veřejné IP adresy** vyberte první STATICKOU veřejnou IP adresu, která [splňuje požadavky](#publicIP) pro váš Azure-SSIS IR. Pokud žádné nemáte, klikněte na **vytvořit nový** odkaz pro vytvoření statických veřejných IP adres na Azure Portal a pak klikněte na tlačítko Aktualizovat, abyste je mohli vybrat.
      
      1. U **druhé statické veřejné IP adresy** vyberte druhou STATICKOU veřejnou IP adresu, která [splňuje požadavky](#publicIP) pro váš Azure-SSIS IR. Pokud žádné nemáte, klikněte na **vytvořit nový** odkaz pro vytvoření statických veřejných IP adres na Azure Portal a pak klikněte na tlačítko Aktualizovat, abyste je mohli vybrat.

   1. Vyberte **ověření virtuální** sítě. Pokud je ověření úspěšné, vyberte **pokračovat**. 

   ![Rozšířené nastavení virtuální sítě](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. V části **Souhrn** Zkontrolujte všechna nastavení Azure-SSIS IR. Pak vyberte **aktualizovat**.

1. Spusťte Azure-SSIS IR výběrem tlačítka **Start** ve sloupci **akce** pro vaši Azure-SSIS IR. Spuštění Azure-SSIS IR, který se připojuje k virtuální síti, trvá přibližně 20 až 30 minut. 

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="define-the-variables"></a>Definovat proměnné

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use SQL Database with IP firewall rules/virtual network service endpoints or SQL Managed Instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for SQL Database with virtual network service endpoints, or a different subnet from the one used for SQL Managed Instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"
```

### <a name="configure-a-virtual-network"></a>Konfigurace virtuální sítě

Předtím, než budete moci připojit Azure-SSIS IR k virtuální síti, je nutné nakonfigurovat virtuální síť. Pokud chcete automaticky konfigurovat oprávnění a nastavení virtuální sítě pro Azure-SSIS IR pro připojení k virtuální síti, přidejte následující skript:

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Vytvoření Azure-SSIS IR a připojení k virtuální síti

Můžete vytvořit Azure-SSIS IR a připojit se k virtuální síti ve stejnou dobu. Úplný skript a pokyny najdete v tématu [vytvoření Azure-SSIS IR](create-azure-ssis-integration-runtime.md#use-azure-powershell-to-create-an-integration-runtime).

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Připojit existující Azure-SSIS IR k virtuální síti

V článku [vytvoření Azure-SSIS IR](create-azure-ssis-integration-runtime.md) se dozvíte, jak vytvořit Azure-SSIS IR a připojit ho k virtuální síti ve stejném skriptu. Pokud už máte Azure-SSIS IR, připojte se k virtuální síti pomocí těchto kroků: 
1. Zastavte Azure-SSIS IR. 
1. Nakonfigurujte Azure-SSIS IR pro připojení k virtuální síti. 
1. Spusťte Azure-SSIS IR. 

### <a name="stop-the-azure-ssis-ir"></a>Zastavení Azure-SSIS IR

Musíte zastavit Azure-SSIS IR, abyste se mohli připojit k virtuální síti. Tento příkaz uvolní všechny jeho uzly a zastaví fakturaci:

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Konfigurovat nastavení virtuální sítě pro Azure-SSIS IR pro připojení

Pokud chcete nakonfigurovat nastavení pro virtuální síť, ke které se bude Azure-SSIS připojit, použijte tento skript: 

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="configure-the-azure-ssis-ir"></a>Nakonfigurovat Azure-SSIS IR

Pokud chcete připojit svůj Azure-SSIS IR k virtuální síti, spusťte `Set-AzDataFactoryV2IntegrationRuntime` příkaz: 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -VnetId $VnetId `
    -Subnet $SubnetName

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}
```

### <a name="start-the-azure-ssis-ir"></a>Spusťte Azure-SSIS IR

Chcete-li spustit Azure-SSIS IR, spusťte následující příkaz: 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

Dokončení tohoto příkazu trvá 20 až 30 minut.

## <a name="next-steps"></a>Další kroky

Další informace o Azure-SSIS IR najdete v následujících článcích: 
- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). Tento článek obsahuje obecné koncepční informace o finančním úřadu, včetně Azure-SSIS IR. 
- [Kurz: nasazení balíčků SSIS do Azure](./tutorial-deploy-ssis-packages-azure.md). V tomto kurzu najdete podrobné pokyny k vytvoření Azure-SSIS IR. Pro hostování katalogu SSIS používá Azure SQL Database. 
- [Vytvořte Azure-SSIS IR](create-azure-ssis-integration-runtime.md). Tento článek se rozbalí v tomto kurzu. Poskytuje pokyny, jak Azure SQL Database s použitím koncových bodů služby virtuální sítě nebo spravované instance SQL ve virtuální síti pro hostování katalogu SSIS. Ukazuje, jak připojit Azure-SSIS IR k virtuální síti. 
- [Monitorování Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). V tomto článku se dozvíte, jak získat informace o Azure-SSIS IR. Poskytuje popisy stavu pro vrácené informace. 
- [Správa Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). V tomto článku se dozvíte, jak zastavit, spustit nebo odstranit Azure-SSIS IR. Také ukazuje, jak horizontální navýšení kapacity Azure-SSIS IR přidáním uzlů.
