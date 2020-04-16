---
title: Připojení prostředí Azure-SSIS Integration Runtime k virtuální síti
description: Zjistěte, jak se připojit k runtime integrace Azure-SSIS do virtuální sítě Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/01/2020
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: cf13dbe17738ca1ae658c73bb0092a219b4823d1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415905"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Připojení prostředí Azure-SSIS Integration Runtime k virtuální síti

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Při použití služby SQL Server Integration Services (SSIS) v Azure Data Factory byste měli připojit runtime integrace Azure-SSIS (IR) do virtuální sítě Azure v následujících scénářích:

- Chcete se připojit k místním úložištím dat z balíčků SSIS, které běží na zařízení Azure-SSIS IR bez konfigurace nebo správy samoobslužné infračervené hodu jako proxy serveru. 

- Chcete hostovat databázi katalogu SSIS (SSISDB) v databázi Azure SQL s koncovými body brány firewall IP nebo koncovými body služby virtuální sítě nebo spravovanou instanci s privátním koncovým bodem. 

- Chcete se připojit k prostředkům Azure nakonfigurovaným koncovými body služby virtuální sítě z balíčků SSIS, které běží na vašem zařízení Azure-SSIS IR.

- Chcete se připojit k úložištím dat nebo prostředkům nakonfigurovaným pomocí pravidel brány firewall IP z balíčků SSIS, které běží na zařízení Azure-SSIS IR.

Data Factory umožňuje připojit azure-SSIS IR do virtuální sítě vytvořené prostřednictvím klasického modelu nasazení nebo modelu nasazení Azure Resource Manager.

> [!IMPORTANT]
> Klasická virtuální síť se zastaralá, proto místo toho použijte virtuální síť Azure Resource Manageru.  Pokud už používáte klasickou virtuální síť, přepněte do virtuální sítě Azure Resource Manager co nejdříve.

[Konfigurace modulu runtime integrace Azure-SQL Server Integration Services (SSIS) pro připojení k virtuální síti](tutorial-deploy-ssis-virtual-network.md) kurz ukazuje minimální kroky prostřednictvím portálu Azure. Tento článek rozšiřuje kurz a popisuje všechny volitelné úkoly:

- Pokud používáte virtuální síť (klasickou).
- Pokud si přinesete vlastní veřejné IP adresy pro Azure-SSIS IR.
- Pokud používáte vlastní server DNS (Domain Name System).
- Pokud v podsíti používáte skupinu zabezpečení sítě (NSG).
- Pokud používáte Azure ExpressRoute nebo uživatelem definovanou trasu (UDR).
- Pokud používáte vlastní Azure-SSIS IR.
- Pokud používáte azure shell zřizování.

## <a name="access-to-on-premises-data-stores"></a>Přístup k místním úložištím dat

Pokud vaše balíčky SSIS přistupují k místním úložištím dat, můžete připojit azure-SSIS IR k virtuální síti, která je připojená k místní síti. Nebo můžete nakonfigurovat a spravovat samoobslužné infračervené ovládání jako proxy pro azure-SSIS IR. Další informace naleznete [v tématu Konfigurace infračerveného serveru s vlastním hostitelem jako proxy serveru pro službu Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). 

Když připojíte azure-SSIS IR k virtuální síti, nezapomeňte na tyto důležité body: 

- Pokud není k vaší místní síti připojena žádná virtuální síť, nejprve vytvořte [virtuální síť Azure Resource Manager,](../virtual-network/quick-create-portal.md#create-a-virtual-network) ke které se připojí zařízení Azure-SSIS IR. Potom nakonfigurujte [připojení brány VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) mezi lokalitami nebo připojení [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) z této virtuální sítě do místní sítě. 

- Pokud virtuální síť Azure Resource Manager je už připojenka k místní síti ve stejném umístění jako vaše Azure-SSIS IR, můžete připojit infračervený přenos k této virtuální síti. 

- Pokud je klasická virtuální síť už připojená k vaší místní síti v jiném umístění než vaše zařízení Azure-SSIS IR, můžete vytvořit [virtuální síť Azure Resource Manager,](../virtual-network/quick-create-portal.md#create-a-virtual-network) ke které se může připojit vaše infračervená zioven Azure-SSIS. Pak nakonfigurujte připojení [virtuální sítě správce prostředků klasického Azure.](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) 
 
- Pokud je virtuální síť Azure Resource Manageru už připojená k vaší místní síti v jiném umístění než vaše zařízení Azure-SSIS IR, můžete nejprve vytvořit [virtuální síť Azure Resource Manager,](../virtual-network/quick-create-portal.md#create-a-virtual-network) ke které se připojí vaše zařízení Azure-SSIS IR. Pak nakonfigurujte připojení virtuální sítě Azure Resource Manager-to-Azure. 

## <a name="hosting-the-ssis-catalog-in-sql-database"></a>Hostování katalogu SSIS v databázi SQL

Pokud hostujete katalog SSIS v databázi Azure SQL s koncovými body služby virtuální sítě, ujistěte se, že připojíte zařízení Azure-SSIS IR ke stejné virtuální síti a podsíti.

Pokud hostujete katalog SSIS ve spravované instanci s privátním koncovým bodem, ujistěte se, že připojíte zařízení Azure-SSIS IR ke stejné virtuální síti, ale v jiné podsíti než spravovaná instance. Chcete-li připojit infračervený přenos Azure-SSIS k jiné virtuální síti než spravovaná instance, doporučujeme buď partnerský vztah virtuální sítě (který je omezen na stejnou oblast), nebo připojení z virtuální sítě do virtuální sítě. Další informace najdete [v tématu Připojení aplikace ke spravované instanci Azure SQL Database](../sql-database/sql-database-managed-instance-connect-app.md).

## <a name="access-to-azure-services"></a>Přístup ke službám Azure

Pokud vaše balíčky SSIS přistupují k prostředkům Azure, které podporují [koncové body služby virtuální sítě,](../virtual-network/virtual-network-service-endpoints-overview.md) a chcete zabezpečit přístup k těmto prostředkům z azure-SSIS IR, můžete připojit azure-SSIS IR k podsíti virtuální sítě nakonfigurované pro koncové body virtuální sítě a pak přidat pravidlo virtuální sítě k příslušným prostředkům Azure, aby byl umožněn přístup ze stejné podsítě.

## <a name="access-to-data-sources-protected-by-ip-firewall-rule"></a>Přístup ke zdrojům dat chráněným pravidlem brány firewall IP

Pokud váš SSIS balíčky přístup úložiště dat nebo prostředky, které umožňují pouze konkrétní statické veřejné IP adresy a chcete zabezpečit přístup k těmto prostředkům z Azure-SSIS IR, můžete přinést vlastní [veřejné IP adresy](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address) pro Azure-SSIS IR při připojení k virtuální síti a pak přidat pravidlo brány firewall IP k příslušným prostředkům povolit přístup z těchto IP adres.

Ve všech případech lze virtuální síť nasadit jenom prostřednictvím modelu nasazení Azure Resource Manager.

V následujících částech jsou uvedeny další podrobnosti. 

## <a name="virtual-network-configuration"></a>Konfigurace virtuální sítě

Nastavte virtuální síť tak, aby splňovala tyto požadavky: 

- Ujistěte `Microsoft.Batch` se, že je registrovaným poskytovatelem v rámci předplatného podsítě virtuální sítě, která je hostitelem Azure-SSIS IR. Pokud používáte klasickou virtuální `MicrosoftAzureBatch` síť, připojte se také k roli přispěvatele klasického virtuálního počítače pro tuto virtuální síť. 

- Ujistěte se, že máte požadovaná oprávnění. Další informace naleznete v tématu [Nastavení oprávnění](#perms).

- Vyberte správnou podsíť pro hostování infračerveného počítače Azure-SSIS. Další informace naleznete [v tématu Výběr podsítě](#subnet). 

- Pokud si přivedete vlastní veřejné IP adresy pro zařízení Azure-SSIS IR, přečtěte si informace [o výběru statických veřejných IP adres.](#publicIP)

- Pokud ve virtuální síti používáte vlastní server DNS (Domain Name System), přečtěte si informace [o nastavení serveru DNS](#dns_server). 

- Pokud v podsíti používáte skupinu zabezpečení sítě (NSG), přečtěte si informace [o nastavení skupiny síťových připojení](#nsg). 

- Pokud používáte Azure ExpressRoute nebo uživatelem definovanou trasu (UDR), přečtěte si informace [o použití Azure ExpressRoute nebo UDR](#route). 

- Ujistěte se, že skupina prostředků virtuální sítě (nebo skupina prostředků veřejných IP adres, pokud přinesete vlastní veřejné IP adresy) může vytvářet a odstraňovat určité síťové prostředky Azure. Další informace naleznete [v tématu Nastavení skupiny prostředků](#resource-group). 

- Pokud indiuru Azure-SSIS inivíš, jak je popsáno v [části Vlastní nastavení pro Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup), vaše infračervené uzly Azure-SSIS získají privátní IP adresy z předdefinovaného rozsahu 172.16.0.0 na 172.31.255.255. Takže se ujistěte, že rozsahy privátních IP adres virtuálních nebo místních sítí nejsou kolidována s tímto rozsahem.

Tento diagram znázorňuje požadovaná připojení pro infračervený přenos Azure-SSIS:

![Prostředí Azure-SSIS IR](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="set-up-permissions"></a><a name="perms"></a>Nastavení oprávnění

Uživatel, který vytvoří indistrua Služby Azure-SSIS, musí mít následující oprávnění:

- Pokud připojujete infračervený přenos SSIS k virtuální síti Azure Resource Manager, máte dvě možnosti:

  - Použijte předdefinovanou roli přispěvatele sítě. Tato role je dodávána s oprávněním _Microsoft.Network/,\* _ které má mnohem větší rozsah, než je nutné.

  - Vytvořte vlastní roli, která obsahuje pouze potřebná oprávnění _Microsoft.Network/virtualNetworks/\*/join/action._ Pokud také chcete přenést vlastní veřejné IP adresy pro Azure-SSIS IR při jeho připojení k virtuální síti Azure Resource Manager, zahrňte do role také oprávnění _Microsoft.Network/publicIPAddresses/*/join/action._

- Pokud se připojujete k prostředí SSIS IR ke klasické virtuální síti, doporučujeme použít integrovanou roli přispěvatele klasického virtuálního počítače. V opačném případě budete muset definovat vlastní roli, která zahrnuje oprávnění k připojení k virtuální síti.

### <a name="select-the-subnet"></a><a name="subnet"></a>Výběr podsítě

Při výběru podsítě: 

- Nevybírejte GatewaySubnet pro nasazení infračerveného počítače Azure-SSIS. Je určen pro brány virtuální sítě. 

- Ujistěte se, že vybraná podsíť má dostatek dostupného adresního prostoru pro použití infračerveného prostoru Azure-SSIS. Ponechte dostupné IP adresy alespoň pro dvojnásobné číslo infračerveného uzlu. Azure si vyhrazuje některé IP adresy v rámci každé podsítě. Tyto adresy nelze použít. První a poslední IP adresy podsítí jsou vyhrazeny pro shodu protokolu a pro služby Azure se používají další tři adresy. Další informace naleznete v tématu [Existují nějaká omezení pro používání IP adres v těchto podsítích?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) 

- Nepoužívejte podsíť, která je výhradně obsazena jinými službami Azure (například instance spravované databází SQL, služba App Service a tak dále). 

### <a name="select-the-static-public-ip-addresses"></a><a name="publicIP"></a>Výběr statických veřejných IP adres

Pokud chcete přinést vlastní statické veřejné IP adresy pro Azure-SSIS IR při připojení k virtuální síti, ujistěte se, že splňují následující požadavky:

- Měly by být k dispozici přesně dva nepoužívané prostředky, které ještě nejsou přidružené k jiným prostředkům Azure. Další se použije při pravidelném upgradu zařízení Azure-SSIS IR. Všimněte si, že jednu veřejnou IP adresu nelze sdílet mezi aktivními irs Azure-SSIS.

- Oba by měly být statické standardního typu. Další podrobnosti naleznete [v části SKU veřejné IP adresy.](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#sku)

- Oba by měli mít název DNS. Pokud jste při jejich vytváření nezadali název DNS, můžete tak učinit na webu Azure Portal.

![Prostředí Azure-SSIS IR](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

- Oni a virtuální síť by měla být pod stejným předplatným a ve stejné oblasti.

### <a name="set-up-the-dns-server"></a><a name="dns_server"></a>Nastavení serveru DNS 
Pokud potřebujete použít vlastní DNS server ve virtuální síti, ke které se připojí zařízení Azure-SSIS IR, abyste vyřešili název privátního hostitele, ujistěte se, že může také přeložit globální názvy hostitelů Azure (například objekt blob Azure Storage s názvem). `<your storage account>.blob.core.windows.net` 

Jeden doporučený přístup je níže: 

-   Nakonfigurujte vlastní DNS pro předávání požadavků na Azure DNS. Nevyřešené záznamy DNS můžete předávat na IP adresu rekurzivních překladačů Azure (168.63.129.16) na vlastním serveru DNS. 

Další informace naleznete v [tématu Překlad názvů, který používá vlastní server DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). 

> [!NOTE]
> Pro název soukromého `<your_private_server>.contoso.com` `<your_private_server>`hostitele použijte plně kvalifikovaný název domény (FQDN), například použití místo , protože azure-SSIS IR automaticky nepřipojí vlastní příponu DNS.

### <a name="set-up-an-nsg"></a><a name="nsg"></a>Nastavení souboru nSG
Pokud potřebujete implementovat skupinu zabezpečení sítě pro podsíť používanou infračerveným přenosem Azure-SSIS, povolte příchozí a odchozí provoz prostřednictvím následujících portů: 

-   **Příchozí požadavek na infračervený přenos Azure-SSIS**

| Směr | Transportní protokol | Zdroj | Rozsah zdrojových portů | Cíl | Rozsah cílových portů | Komentáře |
|---|---|---|---|---|---|---|
| Příchozí | TCP | Správa batchnode | * | VirtualNetwork | 29876, 29877 (pokud připojíte infračervený přenos do virtuální sítě Resource Manageru) <br/><br/>10100, 20100, 30100 (pokud se připojíte k IR do klasické virtuální sítě)| Služba Data Factory používá tyto porty ke komunikaci s uzly zařízení Azure-SSIS IR ve virtuální síti. <br/><br/> Bez ohledu na to, zda vytvoříte skupinu zabezpečení sítě na úrovni podsítě, data factory vždy konfiguruje skupinu zabezpečení sítě na úrovni karet síťového rozhraní připojené k virtuálním počítačům, které hostují zařízení Azure-SSIS IR. Pouze příchozí provoz z IP adres datové továrny na určených portech je povolen tímto souborem zabezpečení sítě nsg na úrovni nic. I když tyto porty otevřete pro internetový provoz na úrovni podsítě, provoz z IP adres, které nejsou IP adresy Factory, je blokován na úrovni síťové kouta. |
| Příchozí | TCP | CorpNetSaw | * | VirtualNetwork | 3389 | (Nepovinné) Toto pravidlo je vyžadováno pouze v případě, že podporovatel společnosti Microsoft požádá zákazníka, aby otevřel řešení potíží pro pokročilé, a může být uzavřeno ihned po řešení potíží. Značka služby **CorpNetSaw** umožňuje používat vzdálenou plochu pouze zabezpečeným přístupovým pracovním stanicím v podnikové síti společnosti Microsoft. A tuto značku služby nelze vybrat z portálu a je k dispozici jenom prostřednictvím Azure PowerShell nebo Azure CLI. <br/><br/> Na úrovni síťové sítě NSG je port 3389 ve výchozím nastavení otevřený a umožňujeme řídit port 3389 na úrovni sítě NSG, zatímco Azure-SSIS IR má ve výchozím nastavení nepovolený port 3389 odchozí v pravidle brány firewall systému Windows na každém uzlu INFralin pro ochranu. |
||||||||

-   **Odchozí požadavek na infračervený přenos Azure-SSIS**

| Směr | Transportní protokol | Zdroj | Rozsah zdrojových portů | Cíl | Rozsah cílových portů | Komentáře |
|---|---|---|---|---|---|---|
| Odchozí | TCP | VirtualNetwork | * | AzureCloud | 443 | Uzly indicí Azure-SSIS ve virtuální síti používají tento port pro přístup ke službám Azure, jako je Azure Storage a Azure Event Hubs. |
| Odchozí | TCP | VirtualNetwork | * | Internet | 80 | (Nepovinné) Uzly zařízení Azure-SSIS IR ve virtuální síti používají tento port ke stažení seznamu odvolaných certifikátů z Internetu. Pokud tento provoz zablokujete, může dojít ke snížení výkonu při spuštění infračerveného přenosu a ke ztrátě možnosti kontroly seznamu odvolaných certifikátů pro použití certifikátu. Pokud chcete dále zúžit cíl na určité reqdneny, přečtěte **si část Použití Azure ExpressRoute nebo UDR.**|
| Odchozí | TCP | VirtualNetwork | * | Sql | 1433, 11000-11999 | (Nepovinné) Toto pravidlo je vyžadováno pouze v případě, že uzly zařízení Azure-SSIS IR ve virtuální síti přistupují k databázi SSISDB hostované serverem SQL Database. Pokud je zásada připojení serveru SQL Database server nastavena na **proxy** místo **přesměrování**, je potřeba pouze port 1433. <br/><br/> Toto pravidlo odchozího zabezpečení se nevztahuje na SSISDB hostovanou spravovanou instancí ve virtuální síti nebo na databázovém serveru Azure nakonfigurovaném privátním koncovým bodem. |
| Odchozí | TCP | VirtualNetwork | * | VirtualNetwork | 1433, 11000-11999 | (Nepovinné) Toto pravidlo je vyžadováno jenom v případě, že uzly vašeho Zařízení pro infračervený přenos Azure-SSIS iniody ve virtuální síti přistupují k správě SSISDB hostované spravovanou instancí ve virtuální síti nebo na databázovém serveru Azure nakonfigurovaném privátním koncovým bodem. Pokud je zásada připojení serveru SQL Database server nastavena na **proxy** místo **přesměrování**, je potřeba pouze port 1433. |
| Odchozí | TCP | VirtualNetwork | * | Storage | 445 | (Nepovinné) Toto pravidlo je vyžadováno jenom v případě, že chcete spustit balíček SSIS uložený v souborech Azure. |
||||||||

### <a name="use-azure-expressroute-or-udr"></a><a name="route"></a>Použití Azure ExpressRoute nebo UDR
Pokud chcete kontrolovat odchozí provoz z Azure-SSIS IR, můžete směrovat provoz iniciovaný z Azure-SSIS IR do místního zařízení brány firewall prostřednictvím vynuceného tunelového propojení [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) (inzerování trasy Protokolu BGP, 0.0.0.0/0, do virtuální sítě) nebo do síťového virtuálního zařízení (NVA) jako brány firewall nebo [brány firewall Azure firewall](https://docs.microsoft.com/azure/firewall/) přes [UDR](../virtual-network/virtual-networks-udr-overview.md). 

![Scénář NVA pro Azure-SSIS IR](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir-nva.png)

Musíte udělat níže, aby celý scénář fungoval
   -   Příchozí provoz mezi službami Azure Batch management a Azure-SSIS IR nelze směrovat přes zařízení brány firewall.
   -   Zařízení brány firewall musí umožňovat odchozí provoz vyžadované službou Azure-SSIS IR.

Příchozí provoz mezi službami azure batch management a Azure-SSIS Ir nelze směrovat do zařízení brány firewall jinak provoz bude přerušeno z důvodu problému asymetrické směrování. Trasy musí být definovány pro příchozí provoz, aby provoz mohl odpovědět zpět stejným způsobem, jakým přišel. Můžete definovat konkrétní UDR pro směrování provozu mezi službami Azure Batch management a Azure-SSIS IR s dalším typem směrování jako **Internet**.

Pokud je například inzivodazure-SSIS `UK South` umístěn a chcete zkontrolovat odchozí provoz prostřednictvím brány Azure Firewall, nejprve byste získali seznam rozsahů IP adres značky `BatchNodeManagement.UKSouth` služby z odkazu ke stažení rozsahu IP značek [služby](https://www.microsoft.com/download/details.aspx?id=56519) nebo prostřednictvím rozhraní API pro zjišťování [výrobních značek](https://aka.ms/discoveryapi). Potom použijte následující UDR souvisejících tras rozsahu IP s dalším typem směrování jako **Internet** spolu s trasou 0.0.0.0/0 s dalším typem směrování jako **virtuální zařízení**.

![Nastavení Azure Batch UDR](media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png)

> [!NOTE]
> Tento přístup účtuje další náklady na údržbu. Pravidelně kontrolujte rozsah IP adres a přidávejte do udr nové rozsahy IP adres, abyste zabránili přerušení infračerveného zařízení Azure-SSIS. Doporučujeme kontrolovat rozsah IP měsíčně, protože když se nová IP adresa objeví v servisním štítku, ip bude trvat další měsíc. 

Chcete-li usnadnit nastavení pravidel UDR, můžete spustit následující skript Powershellu a přidat pravidla UDR pro služby Azure Batch management:
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

Aby zařízení brány firewall povolilo odchozí provoz, je třeba povolit odchozí do pod portů, stejně jako požadavek v pravidlech odchozích služeb nsg.
-   Port 443 s cílem jako služby Azure Cloud.

    Pokud používáte Azure Firewall, můžete zadat síťové pravidlo pomocí azurecloudové výrobní značky. Pro bránu firewall jiných typů můžete buď jednoduše povolit cíl jako vše pro port 443, nebo povolit pod fqdnena na základě typu vašeho prostředí Azure:

    | Prostředí Azure | Koncové body                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Veřejný partnerský vztah Azure      | <ul><li><b>Azure Data Factory (správa)</b><ul><li>\*.frontend.clouddatahub.net</li></ul></li><li><b>Azure Storage (správa)</b><ul><li>\*.blob.core.windows.net</li><li>\*.table.core.windows.net</li></ul></li><li><b>Registr kontejnerů Azure (vlastní nastavení)</b><ul><li>\*.azurecr.io</li></ul></li><li><b>Centrum událostí (protokolování)</b><ul><li>\*.servicebus.windows.net</li></ul></li><li><b>Služba protokolování společnosti Microsoft (interní použití)</b><ul><li>gcs.prod.monitoring.core.windows.net</li><li>prod.warmpath.msftcloudes.com</li><li>azurewatsonanalysis-prod.core.windows.net</li></ul></li></ul> |
    | Azure Government  | <ul><li><b>Azure Data Factory (správa)</b><ul><li>\*.frontend.datamovement.azure.us</li></ul></li><li><b>Azure Storage (správa)</b><ul><li>\*.blob.core.usgovcloudapi.net</li><li>\*.table.core.usgovcloudapi.net</li></ul></li><li><b>Registr kontejnerů Azure (vlastní nastavení)</b><ul><li>\*.azurecr.us</li></ul></li><li><b>Centrum událostí (protokolování)</b><ul><li>\*.servicebus.usgovcloudapi.net</li></ul></li><li><b>Služba protokolování společnosti Microsoft (interní použití)</b><ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>azurewatsonanalysis.usgovcloudapp.net</li></ul></li></ul> |
    | Azure China 21Vianet     | <ul><li><b>Azure Data Factory (správa)</b><ul><li>\*.frontend.datamovement.azure.cn</li></ul></li><li><b>Azure Storage (správa)</b><ul><li>\*.blob.core.chinacloudapi.cn</li><li>\*.table.core.chinacloudapi.cn</li></ul></li><li><b>Registr kontejnerů Azure (vlastní nastavení)</b><ul><li>\*.azurecr.cn</li></ul></li><li><b>Centrum událostí (protokolování)</b><ul><li>\*.servicebus.chinacloudapi.cn</li></ul></li><li><b>Služba protokolování společnosti Microsoft (interní použití)</b><ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>azurewatsonanalysis.chinacloudapp.cn</li></ul></li></ul> |

    Pokud jde o hlavní názvy qdn azure storage, Azure Container Registry a Event Hub, můžete také povolit následující koncové body služby pro vaši virtuální síť, aby síťový provoz do těchto koncových bodů procházel páteřní sítí Azure namísto směrování do vašeho zařízení brány firewall:
    -  Microsoft.Storage
    -  Microsoft.ContainerRegistry
    -  Microsoft.EventHub


-   Port 80 s cílem jako crl download stránky.

    Níže uvedené sítě souborů FQDN, které se používají jako seznam odvolaných certifikátů (seznam odvolaných certifikátů) služby CRL (Seznam odvolaných certifikátů) pro účely správy infračerveného systému Azure-SSIS:
    -  crl.microsoft.com:80
    -  mscrl.microsoft.com:80
    -  crl3.digicert.com:80
    -  crl4.digicert.com:80
    -  ocsp.digicert.com:80
    -  cacerts.digicert.com:80
    
    Pokud používáte certifikáty s jiným seznamem CRL, doporučujeme je zahrnout také. Můžete si přečíst, abyste se dozvěděli více o [seznamu odvolaných certifikátů](https://social.technet.microsoft.com/wiki/contents/articles/2303.understanding-access-to-microsoft-certificate-revocation-list.aspx).

    Pokud tento provoz zakážete, může dojít ke snížení výkonu při spuštění služby Azure-SSIS IR a ke ztrátě možnosti kontroly seznamu odvolaných certifikátů pro použití certifikátu, který se z hlediska zabezpečení nedoporučuje.

-   Port 1433, 11000-11999 s cílem jako Azure SQL (jenom v případě, že uzly vašeho Zařízení pro zjišťování inzidu Azure-SSIS in ve virtuální síti přistupují k SSISDB hostovanému vaším databázovým serverem SQL).

    Pokud používáte Azure Firewall, můžete zadat síťové pravidlo s Azure SQL Service Tag, jinak můžete povolit cíl jako konkrétní azure sql url v zařízení firewall.

-   Port 445 s cílem jako Azure Storage (jenom při spuštění balíčku SSIS uloženého v Azure Files).

    Pokud používáte Azure Firewall, můžete zadat síťové pravidlo s service tag úložiště, jinak můžete povolit cíl jako konkrétní azure soubor úložiště URL v zařízení firewall.

> [!NOTE]
> Pokud pro Azure SQL a Storage nakonfigurujete koncové body služby Virtuální síť ve své podsíti, bude přenos mezi Azure-SSIS IR a Azure SQL ve stejné oblasti \ Azure Storage ve stejné oblasti nebo ve stejné oblasti nebo ve spárované oblasti směrován přímo do páteřní sítě Microsoft Azure místo zařízení brány firewall.

Pokud nepotřebujete možnost kontroly odchozího provozu služby Azure-SSIS IR, můžete jednoduše použít trasu a vynutit veškerý provoz na další **internet**typu směrování :

-   Ve scénáři Azure ExpressRoute můžete použít trasu 0.0.0.0/0 s dalším typem směrování jako **Internet** v podsíti, která hostuje Azure-SSIS IR. 
-   Ve scénáři síťového virtuálního zařízení můžete upravit existující trasu 0.0.0.0/0 použitou v podsíti, která hostuje infračervený přenos Azure-SSIS, z dalšího typu směrování jako **virtuální zařízení** na **Internet**.

![Přidání trasy](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

> [!NOTE]
> Určení trasy s dalším typem směrování **v Internetu** neznamená, že veškerý provoz bude přecházet přes Internet. Dokud je cílová adresa pro jednu ze služeb Azure, Azure směruje provoz přímo do služby přes páteřní síť Azure, místo směrování provozu na Internet.

### <a name="set-up-the-resource-group"></a><a name="resource-group"></a>Nastavení skupiny prostředků

Azure-SSIS Ir potřebuje vytvořit určité síťové prostředky ve stejné skupině prostředků jako virtuální síť. Mezi tyto zdroje patří:
- Nástroj pro vyrovnávání zatížení Azure s názvem * \<Guid>-azurebatch-cloudserviceloadbalancer*.
- Veřejná IP adresa Azure s názvem * \<Guid>-azurebatch-cloudservicepublicip*.
- Skupina zabezpečení práce v síti s názvem * \<Guid>-azurebatch-cloudservicenetworksecuritygroup*. 

> [!NOTE]
> Nyní můžete přinést vlastní statické veřejné IP adresy pro Azure-SSIS IR. V tomto scénáři vytvoříme jenom nástroj pro vyrovnávání zatížení Azure a skupinu zabezpečení sítě ve stejné skupině prostředků jako statické veřejné IP adresy namísto virtuální sítě.

Tyto prostředky se vytvoří při spuštění infračerveného zařízení Azure-SSIS. Budou odstraněny, když se zastaví vaše infračervená infračervená infračervená zručná zařízení Azure-SSIS. Pokud si přinesete vlastní statické veřejné IP adresy pro Azure-SSIS IR, vaše vlastní statické veřejné IP adresy se neodstraní, když se vaše infračervená infračervená infračervená zručná zařízení Azure-SSIS zastaví. Chcete-li zabránit blokování azure-SSIS IR zastavení, nepoužívejte znovu tyto síťové prostředky v jiných prostředků.

Ujistěte se, že nemáte zámek prostředků na skupinu prostředků nebo odběr, do kterého patří virtuální síť/statické veřejné IP adresy. Pokud nakonfigurujete zámek jen pro čtení nebo odstranění, spuštění a zastavení zařízení Azure-SSIS IR se nezdaří nebo přestane reagovat.

Ujistěte se, že nemáte zásady Azure, které brání vytváření následujících prostředků v rámci skupiny prostředků/předplatného, do které patří virtuální síť/vaše statické veřejné IP adresy: 
- Microsoft.Network/LoadBalancers 
- Microsoft.Network/NetworkSecurityGroups 
- Adresy Microsoft.Network/PublicIPAddresses 

Ujistěte se, že kvóta prostředků vašeho předplatného je dostatečná pro výše uvedené tři síťové prostředky. Konkrétně pro každý Azure-SSIS IR vytvořené ve virtuální síti, je třeba rezervovat dvě volné kvóty pro každý z výše uvedených tří síťových prostředků. Kvóta navíc se použije při pravidelném upgradu zařízení Azure-SSIS IR.

### <a name="faq"></a><a name="faq"></a>Faq

- Jak můžu chránit veřejnou IP adresu vystavenou na izoru Azure-SSIS pro příchozí připojení? Je možné odstranit veřejnou IP adresu?
 
  Právě teď se veřejná IP adresa automaticky vytvoří, když se vaše zařízení Azure-SSIS IR připojí k virtuální síti. Máme nsg na úrovni nic, který umožňuje pouze služby správy Azure Batch pro příchozí připojení k infračervenému zařízení Azure-SSIS. Můžete také určit skupinu nsg na úrovni podsítě pro ochranu příchozích.

  Pokud nechcete, aby byla vystavena žádná veřejná IP adresa, [zvažte konfiguraci infračerveného serveru hostovaného na vlastním účtu jako proxy serveru pro zařízení Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis) namísto připojení zařízení Azure-SSIS IR k virtuální síti, pokud to platí pro váš scénář.
 
- Můžu přidat veřejnou IP adresu id služby Azure-SSIS IR do seznamu povolených dat brány firewall pro mé zdroje dat?

  Nyní můžete přinést vlastní statické veřejné IP adresy pro Azure-SSIS IR. V takovém případě můžete přidat ip adresy do seznamu povolených adres brány firewall pro zdroje dat. Můžete také zvážit další možnosti níže pro zabezpečení přístupu k datům z Azure-SSIS IR v závislosti na vašem scénáři:

  - Pokud je váš zdroj dat místní, po připojení virtuální sítě k místní síti a připojení zařízení Azure-SSIS IR k podsíti virtuální sítě můžete přidat rozsah privátních IP adres této podsítě do seznamu povolených dat brány firewall pro váš zdroj dat.
  - Pokud je váš zdroj dat služba Azure, která podporuje koncové body služby virtuální sítě, můžete nakonfigurovat koncový bod služby virtuální sítě v podsíti virtuální sítě a připojit infračervený přenos Azure-SSIS k této podsíti. Potom můžete přidat pravidlo virtuální sítě s tímto podsítí do brány firewall pro váš zdroj dat.
  - Pokud je vaším zdrojem dat cloudová služba mimo Azure, můžete pomocí UDR směrovat odchozí provoz z zařízení Azure-SSIS IR do brány firewall pro připojení k Internetu nebo Azure firewall prostřednictvím statické veřejné IP adresy. Potom můžete přidat statickou veřejnou IP adresu brány Firewall/Azure firewall do seznamu povolených bran firewall pro váš zdroj dat.
  - Pokud žádná z výše uvedených možností nevyhovuje vašim potřebám, [zvažte konfiguraci infračerveného serveru s vlastním hostitelem jako proxy serveru pro zařízení Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). Potom můžete přidat statickou veřejnou IP adresu počítače, který hostuje vaše vlastní hostované infračervené ovládání, do seznamu povolených dat brány firewall pro váš zdroj dat.

- Proč je nutné zadat dvě statické veřejné adresy, pokud chci přinést vlastní pro Azure-SSIS IR?

  Azure-SSIS IR se pravidelně aktualizuje automaticky. Během upgradu se vytvoří nové uzly a staré budou odstraněny. Chcete-li se však vyhnout prostojům, staré uzly nebudou odstraněny, dokud nebudou nové připraveny. Proto vaše první statická veřejná IP adresa používaná starými uzly nemůže být uvolněna okamžitě a potřebujeme druhou statickou veřejnou IP adresu k vytvoření nových uzlů.

- Přinesl(a) jsem vlastní statické veřejné IP adresy pro Azure-SSIS IR, ale proč stále nemá přístup k mým zdrojům dat?

  - Zkontrolujte, zda jsou obě statické veřejné IP adresy přidány do seznamu povolených adres brány firewall pro zdroje dat. Pokaždé, když je inovovaná infračervená počítač Azure-SSIS upgradována, jeho statická veřejná IP adresa se přepíná mezi dvěma vámi, které jste přinesli. Pokud přidáte pouze jeden z nich do seznamu povolených, přístup k datům pro azure-SSIS IR bude přerušena po jeho upgradu.
  - Pokud je váš zdroj dat služba Azure, zkontrolujte, jestli jste ji nakonfigurovali pomocí koncových bodů služby virtuální sítě. V takovém případě se provoz z Azure-SSIS IR do zdroje dat přepne na použití privátních IP adres spravovaných službami Azure a přidání matné statické veřejné IP adresy do seznamu povolených adres brány firewall pro váš zdroj dat se neprojeví.

## <a name="azure-portal-data-factory-ui"></a>Portál Azure (ui datové továrny)

Tato část ukazuje, jak připojit existující Azure-SSIS IR do virtuální sítě (klasické nebo Azure Resource Manager) pomocí portálu Azure a datové továrny ui. 

Před připojením azure-SSIS IR k virtuální síti, je třeba správně nakonfigurovat virtuální síť. Postupujte podle pokynů v části, která se vztahuje k typu virtuální sítě (klasické nebo Azure Resource Manager). Potom podle kroků ve třetí části připojte azure-SSIS IR k virtuální síti. 

### <a name="configure-an-azure-resource-manager-virtual-network"></a>Konfigurace virtuální sítě Azure Resource Manageru

Na portálu nakonfigurujte virtuální síť Azure Resource Manageru, než se k ní pokusíte připojit infračervený přenos Azure-SSIS.

1. Spusťte Microsoft Edge nebo Google Chrome. V současné době pouze tyto webové prohlížeče podporují uživatelské nastavení datové továrny. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 

1. Vyberte **Další služby**. Filtrovat a vybrat **virtuální sítě**. 

1. Filtrujte a vyberte virtuální síť v seznamu. 

1. Na stránce **Virtuální síť** vyberte **Vlastnosti**. 

1. Vyberte tlačítko kopírovat pro **ID prostředku,** chcete-li zkopírovat ID prostředku pro virtuální síť do schránky. Uložte ID ze schránky ve OneNotu nebo v souboru. 

1. V levé nabídce vyberte **Podsítě**. Ujistěte se, že počet dostupných adres je větší než uzly v zařízení Azure-SSIS IR. 

1. Ověřte, že poskytovatel Azure Batch je registrovaný v předplatném Azure, které má virtuální síť. Nebo zaregistrujte zprostředkovatele Azure Batch. Pokud už máte účet Azure Batch ve vašem předplatném, vaše předplatné je registrované pro Azure Batch. (Pokud vytvoříte Indiovní počítač Azure-SSIS na portálu Data Factory, poskytovatel Azure Batch se automaticky zaregistruje za vás.) 

   1. Na webu Azure Portal v yberte v levé nabídce **Předplatná**. 

   1. Vyberte své předplatné. 

   1. Na levé straně vyberte **zprostředkovatele prostředků**a potvrďte, že **Microsoft.Batch** je registrovaným poskytovatelem. 

   ![Potvrzení stavu "Registrovaní"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Pokud v seznamu nevidíte **Microsoft.Batch,** zaregistrujte ho, vytvořte ve svém [předplatném prázdný účet Azure Batch.](../batch/batch-account-create-portal.md) Později jej můžete odstranit. 

### <a name="configure-a-classic-virtual-network"></a>Konfigurace klasické virtuální sítě

Než se pokusíte připojit infračervený přenos Azure-SSIS, nakonfigurujte klasickou virtuální síť pomocí portálu. 

1. Spusťte Microsoft Edge nebo Google Chrome. V současné době pouze tyto webové prohlížeče podporují uživatelské nastavení datové továrny. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 

1. Vyberte **Další služby**. Filtrovat a vybrat **virtuální sítě (klasické)**. 

1. Filtrujte a vyberte virtuální síť v seznamu. 

1. Na stránce **Virtuální síť (klasická)** vyberte **Vlastnosti**. 

   ![ID klasického prostředku virtuální sítě](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. Vyberte tlačítko kopírovat pro **ID prostředku,** chcete-li zkopírovat ID prostředku pro klasickou síť do schránky. Uložte ID ze schránky ve OneNotu nebo v souboru. 

1. V levé nabídce vyberte **Podsítě**. Ujistěte se, že počet dostupných adres je větší než uzly v zařízení Azure-SSIS IR. 

   ![Počet dostupných adres ve virtuální síti](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. Připojte **MicrosoftAzureBatch** k roli **přispěvatele klasického virtuálního počítače** pro virtuální síť. 

   1. V levé nabídce vyberte **Ovládací prvek přístupu (IAM)** a vyberte kartu **Přiřazení rolí.** 

       ![Tlačítka "Řízení přístupu" a "Přidat"](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

   1. Vyberte **Přidat přiřazení role**.

   1. Na stránce **Přidat přiřazení role** vyberte v části **Role** **položku Přispěvatel klasického virtuálního počítače**. Do pole **Select** vložte **ddbf3205-c6bd-46ae-8127-60eb93363864**a ze seznamu výsledků hledání vyberte **Microsoft Azure Batch.** 

       ![Výsledky hledání na stránce Přidat přiřazení role](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

   1. Vyberte **Uložit,** chcete-li uložit nastavení a zavřít stránku. 

       ![Uložení nastavení přístupu](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

   1. Potvrďte, že se v seznamu přispěvatelů zobrazuje **Microsoft Azure Batch.** 

       ![Potvrzení přístupu azure batch](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. Ověřte, že poskytovatel Azure Batch je registrovaný v předplatném Azure, které má virtuální síť. Nebo zaregistrujte zprostředkovatele Azure Batch. Pokud už máte účet Azure Batch ve vašem předplatném, vaše předplatné je registrované pro Azure Batch. (Pokud vytvoříte Indiovní počítač Azure-SSIS na portálu Data Factory, poskytovatel Azure Batch se automaticky zaregistruje za vás.) 

   1. Na webu Azure Portal v yberte v levé nabídce **Předplatná**. 

   1. Vyberte své předplatné. 

   1. Na levé straně vyberte **zprostředkovatele prostředků**a potvrďte, že **Microsoft.Batch** je registrovaným poskytovatelem. 

   ![Potvrzení stavu "Registrovaní"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Pokud v seznamu nevidíte **Microsoft.Batch,** zaregistrujte ho, vytvořte ve svém [předplatném prázdný účet Azure Batch.](../batch/batch-account-create-portal.md) Později jej můžete odstranit. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Připojení infračerveného zařízení Azure-SSIS k virtuální síti

Po konfiguraci virtuální sítě Azure Resource Manager nebo klasické virtuální sítě se můžete k virtuální síti připojit k infračervenému zařízení Azure-SSIS:

1. Spusťte Microsoft Edge nebo Google Chrome. V současné době pouze tyto webové prohlížeče podporují uživatelské nastavení datové továrny. 

1. Na [portálu Azure](https://portal.azure.com)v yberte v levé nabídce **položku Datové továrny**. Pokud v nabídce nevidíte **továrny dat,** vyberte **Další služby**a pak v části **INTELLIGENCE + ANALYTICS** vyberte **Datové továrny**. 

   ![Seznam datových továren](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Vyberte svou datovou továrnu s Azure-SSIS IR v seznamu. Zobrazí se domovská stránka datové továrny. Vyberte dlaždici **Author & Monitor.** Uvidíte udata factory uj. 

   ![Domovská stránka objektu pro vytváření dat](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. V uzdu Factory dat přepněte na kartu **Úpravy,** vyberte **Připojení**a přepněte na kartu **Integrační runtimes.** 

   ![Karta "Integrační běhové zaběhu"](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Pokud je vaše infračervená infračervená infračervená induiční služba Azure-SSIS spuštěná, vyberte v seznamu **Infikované běhy** integrace ve sloupci **Akce** tlačítko **Stop** pro infračervený přenos Azure-SSIS. Azure-SSIS IR nemůžete upravovat, dokud ho nezastavíte. 

   ![Zastavit infračervený přenos](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. V seznamu **Integrační běhové časy** ve sloupci **Akce** vyberte tlačítko **Upravit** pro infračervený přenos Azure-SSIS. 

   ![Úprava prostředí runtime integrace](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. Na panelu nastavení modulu runtime integrace přejděte v částech **Obecné nastavení** a **Nastavení SQL** výběrem tlačítka **Další.** 

1. V části **Upřesnit nastavení:** 

   1. Zaškrtněte **políčko Vybrat virtuální síť pro prostředí Integrace Azure-SSIS, chcete-li se připojit, povolte adf k vytvoření určitých síťových prostředků a volitelně přenést vlastní statické veřejné IP adresy.** 

   1. V **části Předplatné**vyberte předplatné Azure, které má vaši virtuální síť.

   1. V **části Umístění**je vybráno stejné umístění integračního běhu.

   1. V **části Typ**vyberte typ virtuální sítě: klasický nebo Správce prostředků Azure. Doporučujeme vybrat virtuální síť Azure Resource Manager, protože klasické virtuální sítě se brzy zastaralá.

   1. V **části Název virtuální sítě**vyberte název virtuální sítě. Měl by to být stejný, který se používá pro váš server Azure SQL Database s koncovými body služby virtuální sítě nebo spravovanou instancí s privátním koncovým bodem pro hostování SSISDB. Nebo by to mělo být stejné připojení k místní síti. V opačném případě může být libovolná virtuální síť, která přinese vlastní statické veřejné IP adresy pro Azure-SSIS IR.

   1. V **části Název podsítě**vyberte název podsítě pro virtuální síť. Měl by být stejný jako ten, který se používá pro váš server Azure SQL Database s koncovými body služby virtuální sítě pro hostování SSISDB. Nebo by měla být jiná podsíť než ta, která se používá pro vaši spravovanou instanci s privátním koncovým bodem pro hostování SSISDB. V opačném případě může být libovolná podsíť pro přenos vlastních statických veřejných IP adres pro Azure-SSIS IR.

   1. Zaškrtněte políčko **Bring static public IP addresses for your Azure-SSIS Integration Runtime** a vyberte, jestli chcete přenést vlastní statické veřejné IP adresy pro Azure-SSIS IR, abyste je mohli povolit na bráně firewall pro vaše zdroje dat.

      Pokud toto políčko zaškrtnete, proveďte následující kroky.

      1. Pro **první statickou veřejnou IP adresu**vyberte první statickou veřejnou IP adresu, která splňuje [požadavky](#publicIP) na zařízení Azure-SSIS IR. Pokud žádné nemáte, klikněte na **Vytvořit nový** odkaz, abyste vytvořili statické veřejné IP adresy na webu Azure Portal, a potom klikněte na tlačítko aktualizovat tady, abyste je mohli vybrat.
      
      1. Pro **druhou statickou veřejnou IP adresu**vyberte druhou statickou veřejnou IP adresu, která splňuje [požadavky](#publicIP) na infračervený přenos Azure-SSIS. Pokud žádné nemáte, klikněte na **Vytvořit nový** odkaz, abyste vytvořili statické veřejné IP adresy na webu Azure Portal, a potom klikněte na tlačítko aktualizovat tady, abyste je mohli vybrat.

   1. Vyberte **ověření virtuální sítě**. Pokud je ověření úspěšné, vyberte **pokračovat**. 

   ![Rozšířené nastavení virtuální sítě](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. V části **Souhrn** zkontrolujte všechna nastavení pro azure-SSIS IR. Pak vyberte **Aktualizovat**.

1. Spusťte infračervený přenos Azure-SSIS tak, že vyberete tlačítko **Start** ve **sloupci Akce** pro infračervený přenos Azure-SSIS. Spuštění zařízení Azure-SSIS IR, které se připojuje k virtuální síti, trvá přibližně 20 až 30 minut. 

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="define-the-variables"></a>Definování proměnných

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"
```

### <a name="configure-a-virtual-network"></a>Konfigurace virtuální sítě

Než se budete moci připojit k indiodu Azure-SSIS ir do virtuální sítě, je potřeba nakonfigurovat virtuální síť. Pokud chcete automaticky nakonfigurovat oprávnění a nastavení virtuální sítě pro zařízení Azure-SSIS IR pro připojení k virtuální síti, přidejte následující skript:

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

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Vytvoření infračerveného zařízení Azure-SSIS a připojení k virtuální síti

Můžete vytvořit Azure-SSIS IR a připojit ji k virtuální síti ve stejnou dobu. Úplný skript a pokyny naleznete v [tématu Vytvoření indičního počítače Azure-SSIS IR](create-azure-ssis-integration-runtime.md#use-azure-powershell-to-create-an-integration-runtime).

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Připojení existující infračervené ho počítače Azure-SSIS k virtuální síti

Vytvoření [Azure-SSIS IR](create-azure-ssis-integration-runtime.md) článek ukazuje, jak vytvořit Azure-SSIS IR a připojit ji k virtuální síti ve stejném skriptu. Pokud už máte Azure-SSIS IR, připojte se k virtuální síti následujícím postupem: 
1. Zastavte infračervený přenos služby Azure-SSIS. 
1. Nakonfigurujte infračervený přenos Azure-SSIS tak, aby se připojil k virtuální síti. 
1. Spusťte infračervený přenos Azure-SSIS. 

### <a name="stop-the-azure-ssis-ir"></a>Zastavení infračerveného počítače Azure-SSIS

Před připojením k virtuální síti je nutné id inzivodAzure-SSIS zastavit. Tento příkaz uvolní všechny jeho uzly a zastaví fakturaci:

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Konfigurace nastavení virtuální sítě pro zařízení Azure-SSIS IR pro připojení

Chcete-li nakonfigurovat nastavení pro virtuální síť, ke které se připojí Azure-SSIS, použijte tento skript: 

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

### <a name="configure-the-azure-ssis-ir"></a>Konfigurace infračerveného serveru Azure-SSIS

Pokud chcete připojit infračervený přenos Azure-SSIS k virtuální síti, spusťte `Set-AzDataFactoryV2IntegrationRuntime` příkaz: 

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

### <a name="start-the-azure-ssis-ir"></a>Spuštění infračerveného počítače Azure-SSIS

Chcete-li spustit infračervený přenos Azure-SSIS, spusťte následující příkaz: 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

Dokončení tohoto příkazu trvá 20 až 30 minut.

## <a name="next-steps"></a>Další kroky

Další informace o azure-SSIS IR najdete v následujících článcích: 
- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). Tento článek obsahuje obecné rámcové informace o iRs, včetně Azure-SSIS IR. 
- [Kurz: Nasazení balíčků SSIS do Azure](tutorial-create-azure-ssis-runtime-portal.md). Tento kurz obsahuje podrobné pokyny k vytvoření zařízení Azure-SSIS IR. Používá Azure SQL Database k hostování katalogu SSIS. 
- [Vytvořte infračervený přenos Azure-SSIS](create-azure-ssis-integration-runtime.md). Tento článek rozšiřuje kurz. Obsahuje pokyny k použití Azure SQL Database s koncovými body služby virtuální sítě nebo spravované instance ve virtuální síti k hostování katalogu SSIS. Ukazuje, jak připojit azure-SSIS IR do virtuální sítě. 
- [Monitorování Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Tento článek ukazuje, jak získat informace o azure-SSIS IR. Poskytuje popisy stavu vrácených informací. 
- [Správa Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). V tomto článku se zobrazí postup zastavení, spuštění nebo odstranění infračerveného počítače Azure-SSIS. Také ukazuje, jak škálovat vaše Azure-SSIS IR přidáním uzlů.