---
title: Připojení prostředí Azure-SSIS Integration runtime k virtuální síti | Microsoft Docs
description: Naučte se připojit prostředí Azure-SSIS Integration runtime k virtuální síti Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/15/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 3a1e272fa332c0bf0ee4e5ececa3edd83aec1d46
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543129"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Připojení prostředí Azure-SSIS Integration runtime k virtuální síti
Pokud používáte služba SSIS (SQL Server Integration Services) (SSIS) v Azure Data Factory (ADF), měli byste se připojit ke službě Azure-SSIS Integration Runtime (IR) do virtuální sítě Azure v následujících scénářích: 

- Chcete se připojit k místním úložištím dat z balíčků SSIS běžících na Azure-SSIS IR bez konfigurace/správy prostředí IR v místním prostředí jako proxy serveru. 

- Chcete se připojit k prostředkům služby Azure, které jsou podporované pomocí koncových bodů služby virtuální sítě z balíčků SSIS běžících ve vašem Azure-SSIS IR.

- Hostuje se databáze katalogu SSIS (SSISDB) v Azure SQL Database s koncovými body služby virtuální sítě/spravovanou instancí ve virtuální síti. 

ADF umožňuje připojit se k Azure-SSIS IR k virtuální síti vytvořené prostřednictvím modelu nasazení Classic nebo modelu nasazení Azure Resource Manager. 

> [!IMPORTANT]
> Klasická virtuální síť je aktuálně zastaralá, proto místo ní použijte Azure Resource Manager virtuální síť.  Pokud už používáte klasickou virtuální síť, přepněte prosím na použití Azure Resource Manager virtuální sítě co nejdřív.

## <a name="access-to-on-premises-data-stores"></a>Přístup k místním úložištím dat
Pokud vaše SSIS balíčky přistupují k místním úložištím dat, můžete se připojit ke službě Azure-SSIS IR k virtuální síti, která je připojená k místní síti, nebo nakonfigurovat nebo spravovat místní prostředí IR jako proxy pro Azure-SSIS IR. Další informace najdete v tématu Konfigurace prostředí IR pro místní [hostování jako proxy pro Azure-SSIS IR –](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis) článek Když se připojíte k Azure-SSIS IR k virtuální síti, je potřeba si uvědomit několik důležitých bodů: 

- Pokud neexistuje žádná virtuální síť připojená k vaší místní síti, vytvořte nejprve [Azure Resource Manager virtuální síť](../virtual-network/quick-create-portal.md#create-a-virtual-network) nebo [klasický virtuální síť](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) pro prostředí Azure-SSIS Integration runtime pro připojení. Pak nakonfigurujte [připojení brány VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) typu Site-to-site nebo připojení [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) z této virtuální sítě k místní síti. 

- Pokud existuje existující Azure Resource Manager nebo klasická virtuální síť připojená k vaší místní síti ve stejném umístění jako Azure-SSIS IR, můžete k této virtuální síti připojit IR. 

- Pokud máte existující klasickou virtuální síť připojenou k místní síti v jiném umístění než Azure-SSIS IR, můžete nejdřív vytvořit [klasickou virtuální síť](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) pro Azure-SSIS IR, abyste se mohli připojit. Pak nakonfigurujte připojení k [virtuální síti Classic-to-Classic](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md) . Nebo můžete vytvořit [virtuální síť Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) pro prostředí Azure-SSIS Integration runtime pro připojení. Pak nakonfigurujte připojení [k virtuální síti typu Classic-to-Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) . 
 
- Pokud je existující virtuální síť Azure Resource Manager připojená k vaší místní síti v jiném umístění než Azure-SSIS IR, můžete nejdřív vytvořit [Azure Resource Manager virtuální síť](../virtual-network/quick-create-portal.md##create-a-virtual-network) pro Azure-SSIS IR, abyste se mohli připojit. Pak nakonfigurujte připojení k virtuální síti Azure Resource Manager-to-Azure Resource Manager. Nebo můžete vytvořit [klasickou virtuální síť](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) pro Azure-SSIS IR, abyste se mohli připojit. Pak nakonfigurujte připojení k [virtuální síti typu Classic-to-Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) . 

## <a name="access-to-azure-services-with-virtual-network-service-endpoints"></a>Přístup ke službám Azure pomocí koncových bodů služby virtuální sítě
Pokud vaše balíčky SSIS přistupují k prostředkům služby Azure, které jsou podporované u [koncových bodů služby virtuální sítě](../virtual-network/virtual-network-service-endpoints-overview.md) a chcete tyto prostředky zabezpečit pomocí Azure-SSIS IR, můžete se připojit ke svému prostředí Azure-SSIS IR k podsíti virtuální sítě nakonfigurované s virtuální sítí. koncový bod služby a mezitím přidávají pravidlo virtuální sítě do prostředku služby Azure, aby povoloval přístup ze stejné podsítě.

## <a name="host-the-ssis-catalog-database-in-azure-sql-database-with-virtual-network-service-endpointsmanaged-instance"></a>Hostování databáze katalogu SSIS v Azure SQL Database pomocí koncových bodů služby virtuální sítě/spravované instance
Pokud hostete svůj SSIS katalog v Azure SQL Database s koncovými body služby virtuální sítě, nezapomeňte ke stejné virtuální síti a podsíti připojit Azure-SSIS IR.

Pokud připojíte Azure-SSIS IR ke stejné virtuální síti jako spravovaná instance, ujistěte se, že je Azure-SSIS IR v jiné podsíti než spravovaná instance. Pokud se připojíte ke službě Azure-SSIS IR do jiné virtuální sítě, než je spravovaná instance, doporučujeme buď partnerský vztah virtuální sítě (která je omezená na stejnou oblast), nebo virtuální síť na připojení k virtuální síti. Viz téma [připojení aplikace k Azure SQL Database Managed instance](../sql-database/sql-database-managed-instance-connect-app.md).

Ve všech případech se virtuální síť dá nasadit jenom prostřednictvím modelu nasazení Azure Resource Manager.

Následující části obsahují další podrobnosti. 

## <a name="requirements-for-virtual-network-configuration"></a>Požadavky na konfiguraci virtuální sítě
-   Ujistěte se, `Microsoft.Batch` že je registrovaný poskytovatel v rámci předplatného podsítě virtuální sítě, která je hostitelem Azure-SSIS IR. Pokud používáte klasickou virtuální síť, připojte `MicrosoftAzureBatch` se také k roli Přispěvatel klasických virtuálních počítačů pro tuto virtuální síť. 

-   Ujistěte se, že máte požadovaná oprávnění. Viz [požadovaná oprávnění](#perms).

-   Vyberte správnou podsíť, která bude hostovat Azure-SSIS IR. Viz [Vyberte podsíť](#subnet). 

-   Pokud používáte vlastní server DNS (Domain Name Services) ve virtuální síti, přečtěte si téma [server Domain Name Services](#dns_server). 

-   Pokud v podsíti používáte skupinu zabezpečení sítě (NSG), přečtěte si téma [Skupina zabezpečení sítě](#nsg) . 

-   Pokud používáte službu Azure Express Route nebo konfiguraci trasy definované uživatelem (UDR), přečtěte si téma [použití Azure ExpressRoute nebo trasy definované uživatelem](#route). 

-   Ujistěte se, že skupina prostředků virtuální sítě může vytvářet a odstraňovat určité síťové prostředky Azure. Viz [požadavky na skupinu prostředků](#resource-group). 

-   Pokud přizpůsobíte Azure-SSIS IR, jak je popsáno v článku [vlastní nastavení pro Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup) , uzly IR Azure-SSIS budou přiděleny privátní IP adresy z předdefinovaného rozsahu 172.16.0.0-172.31.255.255, takže se ujistěte, že privátní IP adresa rozsahy adres vašich virtuálních nebo místních sítí nekolidují s tímto rozsahem.

Tady je diagram znázorňující požadovaná připojení pro Azure-SSIS IR:

![Prostředí Azure-SSIS IR](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="perms"></a>Požadovaná oprávnění

Uživatel, který vytváří Integration Runtime Azure-SSIS, musí mít následující oprávnění:

- Pokud se připojujete k SSIS IR k virtuální síti Azure Resource Manager, máte dvě možnosti:

  - Použijte integrovanou roli *Přispěvatel sítě* . Tato role se dodává s oprávněním _Microsoft.\* Network/_ , které má mnohem větší rozsah, než je nutné.

  - Vytvořte vlastní roli, která bude obsahovat jenom potřebná oprávnění _Microsoft. Network/virtualNetworks\*//JOIN/Action_ . 

- Pokud se připojujete k SSIS IR k klasické virtuální síti, doporučujeme použít integrovanou roli *přispěvatele klasického virtuálního počítače* . V opačném případě musíte definovat vlastní roli, která zahrnuje oprávnění k připojení k virtuální síti.

### <a name="subnet"></a>Vyberte podsíť.
-   Nevybírejte GatewaySubnet pro nasazení Azure-SSIS Integration Runtime, protože je vyhrazený pro brány virtuální sítě. 

-   Ujistěte se, že vybraná podsíť má dostatečný dostupný adresní prostor pro Azure-SSIS IR, který se má použít. V dostupných IP adresách ponechte alespoň 2 * číslo uzlu IR. Azure rezervuje některé IP adresy v rámci každé podsítě a tyto adresy se nedají použít. První a poslední IP adresa podsítí jsou vyhrazené pro shodu protokolu a tři další adresy, které se používají pro služby Azure. Další informace najdete v tématu [jakákoli omezení používání IP adres v těchto](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)podsítích. 

-   Nepoužívejte podsíť, která je exkluzivně zabraná jinými službami Azure (například SQL Database spravované instance, App Service atd.). 

### <a name="dns_server"></a>Server služby DNS 
Pokud potřebujete používat vlastní server DNS (Domain Name Services) ve virtuální síti připojené pomocí prostředí Azure-SSIS Integration runtime, ujistěte se, že dokáže přeložit globální názvy hostitelů Azure (například název objektu blob Azure Storage, `<your storage account>.blob.core.windows.net`). 

Doporučujeme následující postup: 

-   Nakonfigurujte vlastní DNS pro přeposílání požadavků na Azure DNS. Nevyřešené záznamy DNS můžete přesměrovat na IP adresu rekurzivních překladačů Azure (168.63.129.16) na vlastním serveru DNS. 

-   Nastavte vlastní DNS jako primární a Azure DNS jako sekundární pro virtuální síť. Zaregistrujte IP adresu rekurzivního překladače (168.63.129.16) Azure jako sekundárního serveru DNS pro případ, že váš vlastní server DNS není dostupný. 

Další informace najdete v tématu [překlad názvů, který používá váš vlastní server DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). 

### <a name="nsg"></a>Skupina zabezpečení sítě
Pokud potřebujete implementovat skupinu zabezpečení sítě (NSG) pro podsíť, kterou používá prostředí Azure-SSIS Integration runtime, povolte příchozí a odchozí provoz prostřednictvím následujících portů: 

| Direction | Transportní protokol | Source | Rozsah zdrojových portů | Cíl | Rozsah cílových portů | Komentáře |
|---|---|---|---|---|---|---|
| Příchozí | TCP | BatchNodeManagement | * | VirtualNetwork | 29876, 29877 (Pokud se připojíte k IR k virtuální síti Azure Resource Manager) <br/><br/>10100, 20100, 30100 (Pokud se připojíte k IR k klasické virtuální síti)| Služba Data Factory tyto porty používá ke komunikaci s uzly prostředí Azure-SSIS Integration runtime ve virtuální síti. <br/><br/> Bez ohledu na to, jestli vytvoříte NSG na úrovni podsítě, nebo ne, Data Factory vždycky nakonfigurovat NSG na úrovni síťových rozhraní (nic) připojených k virtuálním počítačům, které hostují Azure-SSIS IR. Pouze příchozí provoz z Data Factory IP adres na zadaných portech je povolený pro NSG na úrovni síťových adaptérů. I když tyto porty otevřete pro internetový provoz na úrovni podsítě, provoz z IP adres, které nejsou Data Factory IP adresy, se zablokuje na úrovni síťové karty. |
| Odchozí | TCP | VirtualNetwork | * | AzureCloud | 443 | V uzlech prostředí Azure-SSIS Integration runtime ve virtuální síti se tento port používá pro přístup ke službám Azure, jako jsou Azure Storage a Azure Event Hubs. |
| Odchozí | TCP | VirtualNetwork | * | Internet | 80 | Uzly prostředí Azure-SSIS Integration runtime ve virtuální síti používají tento port ke stažení seznamu odvolaných certifikátů z Internetu. |
| Odchozí | TCP | VirtualNetwork | * | Sql | 1433, 11000-11999 | Uzly prostředí Azure-SSIS Integration runtime ve virtuální síti tyto porty používají pro přístup k SSISDB hostované serverem Azure SQL Database. Pokud je vaše zásada připojení Azure SQL Database serveru nastavená na **proxy** místo **přesměrování**, je potřeba jenom port 1433. Toto odchozí pravidlo zabezpečení se nevztahuje na SSISDB hostované vaší spravovanou instancí ve virtuální síti. |
||||||||

### <a name="route"></a>Použití Azure ExpressRoute nebo trasy definované uživatelem
Když připojíte okruh [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) k infrastruktuře virtuální sítě a rozšíříte svou místní síť do Azure, je běžnou konfigurací použití vynuceného tunelového propojení (inzerování trasy protokolu BGP, 0.0.0.0/0 virtuální sítě), která nutí odchozí internetový provoz z toku virtuální sítě do místní síťové zařízení pro kontrolu a protokolování. 
 
Případně můžete definovat [uživatelsky definované trasy (udr)](../virtual-network/virtual-networks-udr-overview.md) pro vynucení odchozího internetového provozu z podsítě, která hostuje Azure-SSIS IR do jiné podsítě, která hostuje zařízení Virtual Network (síťové virtuální zařízení) jako bránu firewall nebo Azure firewall pro kontrolu a protokolování.
 
V obou případech přenosová trasa přeruší požadované příchozí připojení ze závislých služeb Azure Data Factory Services (konkrétně služby Azure Batch Management) do Azure-SSIS IR ve virtuální síti. 
 
Řešením je definování jedné (nebo více) uživatelem definovaných tras (udr) v podsíti, která obsahuje Azure-SSIS IR. 

- Můžete použít trasu 0.0.0.0/0 s typem dalšího segmentu směrování jako **Internet** v podsíti, která hostuje Azure-SSIS IR ve scénáři Azure ExpressRoute, nebo upravit stávající trasu 0.0.0.0/0 z typu dalšího segmentu směrování jako **virtuálního zařízení** na **Internet** v síťové virtuální zařízení. případě.

![Přidat trasu](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)
- Pokud máte obavy o ztrátu možnosti kontroly odchozího internetového provozu z této podsítě. Můžete definovat konkrétní udr jenom pro směrování provozu mezi Azure Batch Management Services a Azure-SSIS IR s typem dalšího segmentu směrování jako **Internet**.
Například Pokud je v Azure-SSIS IR umístěný na `UK South`, budete muset získat seznam `BatchNodeManagement.UKSouth` rozsahů IP adres z značky služby [odkaz ke stažení](https://www.microsoft.com/en-us/download/details.aspx?id=56519) nebo přes [rozhraní API pro zjišťování značek](https://aka.ms/discoveryapi)služby. Pak použijte níže udr související trasy rozsahu IP adres s typem dalšího segmentu směrování jako **Internet**.

![Nastavení AzureBatch UDR](media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png)
> [!NOTE]
> U tohoto přístupu se účtují další náklady na údržbu, které potřebujete k pravidelné kontrole rozsahu IP adres a přidání nového rozsahu IP adres do UDR, abyste se vyhnuli přerušení Azure-SSIS IR. Pokud se nová IP adresa objeví v označení služby, bude trvat další měsíc, než se nová IP adresa projeví. Proto se doporučuje kontrolovat rozsah IP adres měsíčně. 

### <a name="resource-group"></a>Požadavky na skupinu prostředků
-   Prostředí Azure-SSIS IR potřebuje vytvořit určité síťové prostředky ve stejné skupině prostředků jako virtuální síť. Mezi tyto prostředky patří následující:
    -   Nástroj pro vyrovnávání zatížení Azure s názvem  *\<GUID >-azurebatch-cloudserviceloadbalancer*.
    -   Veřejná IP adresa Azure s názvem  *\<GUID >-azurebatch-cloudservicepublicip*.
    -   Skupina zabezpečení práce v síti s názvem  *\<GUID >-azurebatch-cloudservicenetworksecuritygroup*. 

    Tyto prostředky budou vytvořeny při spuštění a odstranění INFRAČERVENého signálu při zastavení. Nepoužívejte je prosím znovu v dalších prostředcích, jinak by to blokovalo zastavení IR. 

-   Ujistěte se, že nemáte žádný zámek prostředků v rámci skupiny prostředků nebo předplatného, ke kterému virtuální síť patří. Pokud nakonfigurujete zámek jen pro čtení nebo zámek proti odstranění, spouštění a zastavování IR může selhat nebo přestat reagovat. 

-   Ujistěte se, že nemáte zásadu Azure, která zabraňuje vytváření těchto prostředků v rámci skupiny prostředků nebo předplatného, ke kterému patří virtuální síť: 
    -   Microsoft. Network/LoadBalancers 
    -   Microsoft.Network/NetworkSecurityGroups 
    -   Microsoft.Network/PublicIPAddresses 

### <a name="faq"></a>DOTAZ

- Jak chráníme veřejnou IP adresu zveřejněnou v Azure-SSIS IR pro příchozí připojení? A je možné odebrat veřejnou IP adresu?
 
    V současné době se veřejná IP adresa vytvoří automaticky, až se Azure-SSIS IR připojí VNet. NSG má na úrovni NIC možnost Povolit jenom službám pro správu Azure Batch příchozí připojení k Azure-SSIS IR a taky můžete zadat i NSG úrovně podsítě pro příchozí ochranu.

    Pokud nechcete, aby se veřejná IP adresa vystavila, můžete v případě použití ve vašem scénáři zvážit způsob [Konfigurace prostředí IR jako proxy pro Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis) .
 
- Je k dispozici statická IP adresa Azure-SSIS IR, kterou je možné uvést do seznamu povolených bran firewall pro přístup ke zdroji dat?
 
    - Pokud je váš zdroj dat místní, po připojení virtuální sítě k místní síti a připojení k Azure-SSIS IR do této podsítě virtuální sítě můžete rozsah IP adres této podsítě umístit do seznamu povolených.
    - Pokud je zdrojem dat služba Azure, která je podporována koncovým bodem služby Virtual Network Service, můžete na virtuální síti připojit bod služby Virtual Network a připojit se k Azure-SSIS IR do této podsítě virtuální sítě, pak budete moci použít pravidlo virtuální sítě. služeb Azure namísto rozsahu IP adres, který umožňuje přístup.
    - Pokud je zdrojem dat jiný cloudový zdroj dat, můžete použít UDR ke směrování odchozího provozu z Azure-SSIS IR na síťové virtuální zařízení nebo Azure firewall se statickou veřejnou IP adresou, takže můžete do seznamu povolených adres umístit tuto veřejnou IP adresu síťové virtuální zařízení nebo Azure firewallu.
    - Pokud žádný z výše nevyhovuje vašemu požadavku, můžete vyhodnotit, jestli máte přístup ke zdroji dat, pomocí možnosti konfigurovat v místním [prostředí IR jako proxy pro Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis), a pak můžete IP adresu počítače, který hostuje v místním prostředí IR do seznamu povolených umístění, místo připojení k Azure-SSIS IR do virtuální sítě.

## <a name="azure-portal-data-factory-ui"></a>Azure Portal (uživatelské rozhraní Data Factory)
V této části se dozvíte, jak připojit stávající modul runtime Azure-SSIS k virtuální síti (Classic nebo Azure Resource Manager) pomocí uživatelského rozhraní Azure Portal a Data Factory. Nejdřív je potřeba nakonfigurovat virtuální síť odpovídajícím způsobem, než se k ní připojíte k Azure-SSIS IR. Projděte jednu z následujících dvou částí na základě typu vaší virtuální sítě (Classic nebo Azure Resource Manager). Pak pokračujte třetí částí a připojte se k Azure-SSIS IR k virtuální síti. 

### <a name="use-the-portal-to-configure-an-azure-resource-manager-virtual-network"></a>Použití portálu ke konfiguraci Azure Resource Manager virtuální sítě
Abyste se mohli připojit k Azure-SSIS IR, musíte nakonfigurovat virtuální síť. 

1. Spusťte Microsoft Edge nebo Google Chrome. V současné době je uživatelské rozhraní Data Factory podporováno pouze v těchto webových prohlížečích. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 

1. Vyberte **Další služby**. Vyfiltrujte a vyberte **virtuální sítě**. 

1. Vyfiltrujte a vyberte svou virtuální síť v seznamu. 

1. Na stránce **virtuální síť** vyberte **vlastnosti**. 

1. Pro zkopírování ID prostředku pro virtuální síť do schránky vyberte tlačítko Kopírovat pro **ID prostředku** . Uloží ID ze schránky ve OneNotu nebo v souboru. 

1. V nabídce vlevo vyberte **podsítě** . Ujistěte se, že počet **dostupných adres** je větší než uzly v prostředí Azure-SSIS Integration runtime. 

1. Ověřte, jestli je poskytovatel Azure Batch zaregistrovaný v předplatném Azure, které má virtuální síť. Případně Zaregistrujte poskytovatele Azure Batch. Pokud již máte účet Azure Batch v předplatném, je vaše předplatné zaregistrované pro Azure Batch. (Pokud vytvoříte Azure-SSIS IR na portálu Data Factory, poskytovatel Azure Batch se automaticky zaregistruje.) 

   a. V Azure Portal v nabídce vlevo vyberte odběry. 

   b. Vyberte své předplatné. 

   c. Na levé straně vyberte **poskytovatele prostředků** a ověřte, že je **Microsoft. Batch** registrovaným poskytovatelem. 

   ![Potvrzení stavu "zaregistrován"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Pokud v seznamu nevidíte **Microsoft. Batch** a zaregistrujete ho, vytvořte ve svém předplatném [prázdný účet Azure Batch](../batch/batch-account-create-portal.md) . Můžete ho odstranit později. 

### <a name="use-the-portal-to-configure-a-classic-virtual-network"></a>Konfigurace klasické virtuální sítě pomocí portálu
Abyste se mohli připojit k Azure-SSIS IR, musíte nakonfigurovat virtuální síť. 

1. Spusťte Microsoft Edge nebo Google Chrome. V současné době je uživatelské rozhraní Data Factory podporováno pouze v těchto webových prohlížečích. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 

1. Vyberte **Další služby**. Vyfiltrujte a vyberte **virtuální sítě (klasické)** . 

1. Vyfiltrujte a vyberte svou virtuální síť v seznamu. 

1. Na stránce **virtuální síť (Classic)** vyberte **vlastnosti**. 

   ![ID prostředku klasické virtuální sítě](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. Pro zkopírování ID prostředku pro klasickou síť do schránky vyberte tlačítko Kopírovat pro **ID prostředku** . Uloží ID ze schránky ve OneNotu nebo v souboru. 

1. V nabídce vlevo vyberte **podsítě** . Ujistěte se, že počet **dostupných adres** je větší než uzly v prostředí Azure-SSIS Integration runtime. 

   ![Počet dostupných adres ve virtuální síti](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. Připojte se **MicrosoftAzureBatch** k roli **Přispěvatel klasických virtuálních počítačů** pro virtuální síť. 

    a. V nabídce vlevo vyberte **řízení přístupu (IAM)** a vyberte kartu **přiřazení rolí** . 

    ![Tlačítka řízení přístupu a přidat](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

    b. Vyberte **přidat přiřazení role**.

    c. Na stránce **Přidat přiřazení role** vyberte **Přispěvatel klasických virtuálních počítačů** pro **roli**. Do pole **Vybrat** vložte **ddbf3205-c6bd-46ae-8127-60eb93363864** a v seznamu výsledků hledání vyberte **Microsoft Azure Batch** . 

    ![Výsledky hledání na stránce přidat přiřazení role](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

    d. Výběrem možnosti **Uložit** uložte nastavení a zavřete stránku. 

    ![Uložit nastavení přístupu](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

    e. Ověřte, že se v seznamu přispěvatelů zobrazuje **Microsoft Azure Batch** . 

    ![Potvrdit přístup k Azure Batch](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. Ověřte, jestli je poskytovatel Azure Batch zaregistrovaný v předplatném Azure, které má virtuální síť. Případně Zaregistrujte poskytovatele Azure Batch. Pokud již máte účet Azure Batch v předplatném, je vaše předplatné zaregistrované pro Azure Batch. (Pokud vytvoříte Azure-SSIS IR na portálu Data Factory, poskytovatel Azure Batch se automaticky zaregistruje.) 

   a. V Azure Portal v nabídce vlevo vyberte odběry. 

   b. Vyberte své předplatné. 

   c. Na levé straně vyberte **poskytovatele prostředků** a ověřte, že je **Microsoft. Batch** registrovaným poskytovatelem. 

   ![Potvrzení stavu "zaregistrován"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Pokud v seznamu nevidíte **Microsoft. Batch** a zaregistrujete ho, vytvořte ve svém předplatném [prázdný účet Azure Batch](../batch/batch-account-create-portal.md) . Můžete ho odstranit později. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Připojení Azure-SSIS IR k virtuální síti
1. Spusťte Microsoft Edge nebo Google Chrome. V současné době je uživatelské rozhraní Data Factory podporováno pouze v těchto webových prohlížečích. 

1. V [Azure Portal](https://portal.azure.com)v nabídce vlevo vyberte **Datové továrny** . Pokud v nabídce nevidíte **Datové továrny** , vyberte **Další služby**a vyberte **Datové továrny** v části **Intelligence + Analytics** . 

   ![Seznam datových továren](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. V seznamu vyberte datovou továrnu pomocí prostředí Azure-SSIS Integration runtime. Zobrazí se Domovská stránka pro datovou továrnu. Vyberte dlaždici **autora & nasazení** . Na samostatné kartě se zobrazí uživatelské rozhraní Data Factory. 

   ![Domovská stránka datové továrny](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. V uživatelském rozhraní Data Factory přepněte na kartu **Upravit** , vyberte **připojení**a přepněte na kartu **prostředí Integration runtime** . 

   ![Karta prostředí Integration runtime](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Pokud je prostředí Azure-SSIS IR spuštěné, vyberte v seznamu prostředí Integration runtime tlačítko **zastavit** ve sloupci **Akce** pro Azure-SSIS IR. IR nelze upravovat, dokud ji nezastavíte. 

   ![Zastavit IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. V seznamu prostředí Integration runtime vyberte ve sloupci **Akce** pro Azure-SSIS IR tlačítko **Upravit** . 

   ![Úprava prostředí Integration runtime](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. Na panelu **nastavení Integration runtime** přejděte na stránce **Obecné nastavení** a **nastavení SQL** kliknutím na tlačítko **Další** . 

1. Na stránce **Upřesnit nastavení** proveďte následující akce: 

   a. Zaškrtněte políčko **Vybrat virtuální síť...** . 

   b. V poli **předplatné**vyberte své předplatné Azure, ve kterém můžete vybrat existující virtuální síť. 
  
   c. Jako **název virtuální**sítě vyberte svou virtuální síť. 

   d. V poli **název podsítě**vyberte podsíť ve virtuální síti. 

   e. Pokud chcete pro Azure-SSIS IR nakonfigurovat i místní prostředí IR jako proxy, zaškrtněte políčko nastavit v místním prostředí **...** a podívejte se na článek [Konfigurace místního prostředí IR jako proxy pro Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis) .

   f. Klikněte na tlačítko **ověření virtuální** sítě a pokud je to úspěšné, klikněte na tlačítko **Další** . 

   ![Upřesňující nastavení pro nastavení infračerveného přenosu](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)

1. Na stránce **Souhrn** Zkontrolujte všechna nastavení pro Azure-SSIS IR a klikněte na tlačítko **aktualizovat** .

1. Nyní můžete spustit prostředí Azure-SSIS IR kliknutím na tlačítko **Start** ve sloupci **Akce** pro Azure-SSIS IR. Spuštění prostředí Azure-SSIS IR, které se připojuje k virtuální síti, trvá přibližně 20 až 30 minut. 

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="configure-a-virtual-network"></a>Konfigurace virtuální sítě
Virtuální síť musíte nakonfigurovat předtím, než se k ní budete moct připojit k Azure-SSIS IR. Pokud chcete automaticky nakonfigurovat oprávnění/nastavení virtuální sítě pro prostředí Azure-SSIS Integration runtime pro připojení k virtuální síti, přidejte následující skript:

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

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Vytvoření prostředí Azure-SSIS IR a jeho připojení k virtuální síti
Můžete vytvořit Azure-SSIS IR a připojit ho k virtuální síti ve stejnou dobu. Úplný skript a pokyny najdete v tématu [vytvoření prostředí Azure-SSIS Integration runtime](create-azure-ssis-integration-runtime.md#azure-powershell).

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Připojení existujícího prostředí Azure-SSIS IR k virtuální síti
Skript v článku [vytvoření prostředí Azure-SSIS Integration runtime](create-azure-ssis-integration-runtime.md) ukazuje, jak vytvořit Azure-SSIS IR a připojit ho k virtuální síti ve stejném skriptu. Pokud máte existující prostředí Azure-SSIS IR, připojte ho k virtuální síti pomocí následujících kroků: 
1. Zastavte Azure-SSIS IR. 
1. Nakonfigurujte Azure-SSIS IR pro připojení k virtuální síti. 
1. Spusťte Azure-SSIS IR. 

### <a name="define-the-variables"></a>Definovat proměnné
```powershell
$ResourceGroupName = "<your Azure resource group name>"
$DataFactoryName = "<your Data Factory name>" 
$AzureSSISName = "<your Azure-SSIS IR name>"
# Specify the information about your classic or Azure Resource Manager virtual network.
$VnetId = "<your Azure virtual network resource ID>"
$SubnetName = "<the name of subnet in your virtual network>"
```

### <a name="stop-the-azure-ssis-ir"></a>Zastavení Azure-SSIS IR
Před tím, než se můžete připojit k virtuální síti, zastavte prostředí Azure-SSIS Integration runtime. Tento příkaz uvolní všechny jeho uzly a zastaví fakturaci:

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Konfigurace nastavení virtuální sítě pro Azure-SSIS IR pro připojení
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

### <a name="configure-the-azure-ssis-ir"></a>Konfigurace Azure-SSIS IR
Pokud chcete nakonfigurovat prostředí Azure-SSIS Integration runtime pro připojení k virtuální síti, `Set-AzDataFactoryV2IntegrationRuntime` spusťte příkaz: 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Type Managed `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
```

### <a name="start-the-azure-ssis-ir"></a>Spuštění Azure – SSIS IR
Pokud chcete spustit prostředí Azure-SSIS Integration runtime, spusťte následující příkaz: 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```

Dokončení tohoto příkazu trvá 20 až 30 minut.

## <a name="next-steps"></a>Další postup
Další informace o prostředí Azure-SSIS Integration runtime najdete v následujících tématech: 
- [Prostředí Azure-SSIS Integration runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Tento článek obsahuje koncepční informace o modulu Integration runtime obecně, včetně Azure-SSIS IR. 
- [Kurz: Nasazení balíčků SSIS do Azure](tutorial-create-azure-ssis-runtime-portal.md) V tomto kurzu najdete podrobné pokyny k vytvoření prostředí Azure-SSIS IR. Pro hostování katalogu SSIS používá Azure SQL Database. 
- [Vytvoření prostředí Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md). Tento článek se rozbalí do kurzu a poskytne vám pokyny k použití Azure SQL Database s koncovými body služby virtuální sítě/spravovanou instancí ve virtuální síti pro hostování katalogu SSIS a připojení k Azure-SSIS IR k virtuální síti. 
- [Monitorování Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). V tomto článku se dozvíte, jak načíst informace o prostředí Azure-SSIS IR a které obsahuje popisy stavů ve vrácených informacích. 
- [Správa Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). V tomto článku se dozvíte, jak zastavit, spustit nebo odstranit Azure-SSIS IR. Také se dozvíte, jak škálovat Azure-SSIS IR přidáním uzlů.
