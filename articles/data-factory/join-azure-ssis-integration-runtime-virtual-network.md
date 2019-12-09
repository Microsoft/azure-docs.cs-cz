---
title: Připojení prostředí Azure-SSIS Integration runtime k virtuální síti
description: Naučte se připojit prostředí Azure-SSIS Integration runtime k virtuální síti Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/15/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: anandsub
ms.openlocfilehash: 77019d6a99e41bb5fb9233aa95836bd4bc8dd877
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926890"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Připojení prostředí Azure-SSIS Integration runtime k virtuální síti
Při použití služba SSIS (SQL Server Integration Services) (SSIS) v Azure Data Factory byste se měli připojit k Azure-SSIS Integration runtime (IR) k virtuální síti Azure v následujících scénářích: 

- Chcete se připojit k místním úložištím dat z balíčků SSIS, které běží na vašem Azure-SSIS IR bez konfigurace nebo správy prostředí IR v místním prostředí jako proxy serveru. 

- Chcete se připojit k prostředkům služby Azure, které jsou podporované pomocí koncových bodů služby virtuální sítě z balíčků SSIS, které běží na vašem Azure-SSIS IR.

- Hostuje se databáze katalogu SSIS (SSISDB) v Azure SQL Database s koncovými body služby virtuální sítě nebo se spravovanou instancí ve virtuální síti. 

Data Factory umožňuje připojit se k Azure-SSIS IR k virtuální síti vytvořené prostřednictvím modelu nasazení Classic nebo modelu nasazení Azure Resource Manager. 

> [!IMPORTANT]
> Klasická virtuální síť se už nepoužívá, proto místo ní použijte Azure Resource Manager virtuální síť.  Pokud už používáte klasický virtuální síť, přepněte co nejdříve do Azure Resource Manager virtuální sítě.

## <a name="access-to-on-premises-data-stores"></a>Přístup k místním úložištím dat
Pokud vaše SSIS balíčky přistupují k místním úložištím dat, můžete se Azure-SSIS IR připojit k virtuální síti, která je připojená k místní síti. Nebo můžete nakonfigurovat nebo spravovat technologii IR v místním prostředí jako proxy pro váš Azure-SSIS IR. Další informace najdete v tématu [Konfigurace prostředí IR pro místní hostování jako proxy serveru pro Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). 

Při připojování k vašemu Azure-SSIS IR k virtuální síti si pamatujte tyto důležité body: 

- Pokud k vaší místní síti není připojená žádná virtuální síť, vytvořte nejdřív [Azure Resource Manager virtuální síť](../virtual-network/quick-create-portal.md#create-a-virtual-network) , pro kterou Azure-SSIS IR připojit. Pak nakonfigurujte [připojení brány VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) typu Site-to-site nebo [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) připojení z této virtuální sítě k místní síti. 

- Pokud je už virtuální síť Azure Resource Manager připojená k vaší místní síti ve stejném umístění jako vaše Azure-SSIS IR, můžete k této virtuální síti připojit IR. 

- Pokud je klasická virtuální síť již připojená k vaší místní síti v jiném umístění, než je vaše Azure-SSIS IR, můžete pro připojení Azure-SSIS IR vytvořit [Azure Resource Manager virtuální síť](../virtual-network/quick-create-portal.md#create-a-virtual-network) . Pak nakonfigurujte připojení [k virtuální síti typu Classic-to-Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) . 
 
- Pokud je už virtuální síť Azure Resource Manager připojená k vaší místní síti v jiném umístění, než je vaše Azure-SSIS IR, můžete nejdřív vytvořit [Azure Resource Manager virtuální síť](../virtual-network/quick-create-portal.md##create-a-virtual-network) , pro kterou Azure-SSIS IR se chcete připojit. Pak nakonfigurujte připojení k virtuální síti Azure Resource Manager-to-Azure Resource Manager. 

## <a name="access-to-azure-services"></a>Přístup ke službám Azure
Pokud vaše balíčky SSIS přistupují k prostředkům služby Azure podporovaným [koncovými body služby virtuální sítě](../virtual-network/virtual-network-service-endpoints-overview.md) a chcete zabezpečit tyto prostředky na Azure-SSIS IR, můžete se připojit k Azure-SSIS IR k podsíti virtuální sítě nakonfigurované pomocí koncových bodů služby virtuální sítě. Mezitím přidejte pravidlo virtuální sítě do prostředků služby Azure, aby bylo možné přístup ze stejné podsítě.

## <a name="hosting-the-ssis-catalog-in-sql-database"></a>Hostování katalogu SSIS v SQL Database
Pokud hostete svůj SSISový katalog v Azure SQL Database s koncovými body služby virtuální sítě, ujistěte se, že jste připojení Azure-SSIS IR ke stejné virtuální síti a podsíti.

Pokud chcete připojit Azure-SSIS IR ke stejné virtuální síti jako spravovaná instance, ujistěte se, že Azure-SSIS IR je v jiné podsíti než spravovaná instance. Pokud chcete připojit svůj Azure-SSIS IR k jiné virtuální síti, než je spravovaná instance, doporučujeme buď partnerský vztah virtuální sítě (která je omezená na stejnou oblast), nebo připojení virtuální sítě k virtuální síti. Další informace najdete v tématu [připojení aplikace k Azure SQL Database Managed instance](../sql-database/sql-database-managed-instance-connect-app.md).

Ve všech případech se virtuální síť dá nasadit jenom prostřednictvím modelu nasazení Azure Resource Manager.

Následující části obsahují další podrobnosti. 

## <a name="virtual-network-configuration"></a>Konfigurace virtuální sítě

Nastavte si virtuální síť tak, aby splňovala tyto požadavky: 

-   Ujistěte se, že `Microsoft.Batch` je registrovaný poskytovatel v rámci předplatného podsítě virtuální sítě, která hostuje Azure-SSIS IR. Pokud používáte klasickou virtuální síť, připojte se taky `MicrosoftAzureBatch` k roli Přispěvatel klasického virtuálního počítače pro tuto virtuální síť. 

-   Ujistěte se, že máte požadovaná oprávnění. Další informace najdete v tématu [Nastavení oprávnění](#perms).

-   Vyberte správnou podsíť pro hostování Azure-SSIS IR. Další informace najdete v tématu [Výběr podsítě](#subnet). 

-   Pokud ve virtuální síti používáte vlastní server DNS (Domain Name System), přečtěte si téma [nastavení serveru DNS](#dns_server). 

-   Pokud v podsíti použijete skupinu zabezpečení sítě (NSG), přečtěte si téma [Nastavení NSG](#nsg). 

-   Pokud používáte Azure ExpressRoute nebo trasu definovanou uživatelem (UDR), přečtěte si téma [použití Azure ExpressRoute nebo udr](#route). 

-   Ujistěte se, že skupina prostředků virtuální sítě může vytvářet a odstraňovat určité síťové prostředky Azure. Další informace najdete v tématu [Nastavení skupiny prostředků](#resource-group). 

-   Pokud přizpůsobíte Azure-SSIS IR, jak je popsáno v tématu [vlastní nastavení pro Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup), Azure-SSIS IR uzly budou mít privátní IP adresy z předdefinovaného rozsahu 172.16.0.0 až 172.31.255.255. Proto se ujistěte, že se rozsahy privátních IP adres ve vašich virtuálních nebo místních sítích nekolidují s tímto rozsahem.

Tento diagram zobrazuje požadovaná připojení pro vaše Azure-SSIS IR:

![Prostředí Azure-SSIS IR](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="perms"></a>Nastavení oprávnění

Uživatel, který vytváří Azure-SSIS IR, musí mít následující oprávnění:

- Pokud se připojujete k SSIS IR k virtuální síti Azure Resource Manager, máte dvě možnosti:

  - Použijte integrovanou roli Přispěvatel sítě. Tato role se dodává s oprávněním _Microsoft. Network/\*_ , která má mnohem větší rozsah, než je nutné.

  - Vytvořte vlastní roli, která bude obsahovat pouze nezbytná oprávnění _Microsoft. Network/virtualNetworks/\*/JOIN/Action_ . 

- Pokud se připojujete k SSIS IR k klasické virtuální síti, doporučujeme použít integrovanou roli přispěvatele klasického virtuálního počítače. V opačném případě musíte definovat vlastní roli, která zahrnuje oprávnění k připojení k virtuální síti.

### <a name="subnet"></a>Vyberte podsíť.

Při volbě podsítě: 

-   Nevybírejte GatewaySubnet pro nasazení Azure-SSIS IR. Je vyhrazený pro brány virtuální sítě. 

-   Ujistěte se, že vybraná podsíť má dostatek dostupného adresního prostoru, aby bylo možné Azure-SSIS IR použít. Nechte dostupné IP adresy aspoň po dobu nejméně dvojnásobku čísla uzlu IR. Azure rezervuje některé IP adresy v každé podsíti. Tyto adresy se nedají použít. První a poslední IP adresa podsítí jsou vyhrazené pro shodu protokolu a pro služby Azure se používají tři další adresy. Další informace najdete v tématu [jakákoli omezení používání IP adres v těchto podsítích](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) . 

-   Nepoužívejte podsíť, která je exkluzivně zabraná jinými službami Azure (například SQL Database spravované instance, App Service atd.). 

### <a name="dns_server"></a>Nastavení serveru DNS 
Pokud potřebujete ve virtuální síti připojené k vašemu Azure-SSIS IR používat vlastní server DNS, ujistěte se, že dokáže přeložit globální názvy hostitelů Azure (například objekt blob Azure Storage s názvem `<your storage account>.blob.core.windows.net`). 

Doporučujeme následující postup: 

-   Nakonfigurujte vlastní DNS pro přeposílání požadavků na Azure DNS. Nevyřešené záznamy DNS můžete přesměrovat na IP adresu rekurzivních překladačů Azure (168.63.129.16) na vlastním serveru DNS. 

-   Nastavte vlastní DNS jako primární server DNS pro virtuální síť. Nastavte Azure DNS jako sekundární server DNS. Zaregistrujte IP adresu rekurzivního překladače Azure (168.63.129.16) jako sekundárního serveru DNS pro případ, že váš vlastní server DNS není dostupný. 

Další informace najdete v tématu [překlad názvů, který používá vlastní server DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). 

### <a name="nsg"></a>Nastavení NSG
Pokud potřebujete implementovat NSG pro podsíť, kterou používá vaše Azure-SSIS IR, povolte příchozí a odchozí provoz prostřednictvím následujících portů: 

| Směr | Transportní protokol | Zdroj | Rozsah zdrojových portů | Cíl | Rozsah cílových portů | Komentáře |
|---|---|---|---|---|---|---|
| Příchozí | TCP | BatchNodeManagement | * | VirtualNetwork | 29876, 29877 (Pokud se připojíte k IR k virtuální síti Správce prostředků) <br/><br/>10100, 20100, 30100 (Pokud se připojíte k IR k klasické virtuální síti)| Služba Data Factory tyto porty používá ke komunikaci s uzly vašeho Azure-SSIS IR ve virtuální síti. <br/><br/> Bez ohledu na to, jestli vytvoříte NSG na úrovni podsítě, Data Factory vždycky nakonfiguruje NSG na úrovni síťových rozhraní (nic) připojených k virtuálním počítačům, které hostují Azure-SSIS IR. Pouze příchozí provoz z Data Factory IP adres na zadaných portech je povolený pro NSG na úrovni síťových adaptérů. I když tyto porty otevřete pro internetový provoz na úrovni podsítě, provoz z IP adres, které nejsou Data Factory IP adresy, se zablokuje na úrovni síťové karty. |
| Odchozí | TCP | VirtualNetwork | * | AzureCloud | 443 | Uzly vašeho Azure-SSIS IR ve virtuální síti používají tento port pro přístup ke službám Azure, jako je například Azure Storage a Azure Event Hubs. |
| Odchozí | TCP | VirtualNetwork | * | Internet | 80 | Uzly vašeho Azure-SSIS IR ve virtuální síti používají tento port ke stažení seznamu odvolaných certifikátů z Internetu. |
| Odchozí | TCP | VirtualNetwork | * | Sql | 1433, 11000-11999 | Uzly Azure-SSIS IR ve virtuální síti používají tyto porty k přístupu k SSISDB hostovanému vaším SQL Database serverem. Pokud je vaše zásada připojení SQL Database serveru nastavená na **proxy** místo **přesměrování**, je potřeba jenom port 1433. Toto odchozí pravidlo zabezpečení se nevztahuje na SSISDB, které hostuje vaše spravovaná instance ve virtuální síti. |
||||||||

### <a name="route"></a>Použití Azure ExpressRoute nebo UDR
Když připojíte okruh [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) k infrastruktuře virtuální sítě a rozšíříte místní síť do Azure, obvyklá konfigurace používá vynucené tunelové propojení (inzerování trasy protokolu BGP, 0.0.0.0/0, virtuální síti). Toto tunelové propojení vynutí odchozí internetový provoz z toku virtuální sítě do místní síťové zařízení pro kontrolu a protokolování. 
 
Případně můžete definovat [udr](../virtual-network/virtual-networks-udr-overview.md) pro vynucení odchozího internetového provozu z podsítě, která hostuje Azure-SSIS IR do jiné podsítě, která hostuje síťové virtuální zařízení (síťové virtuální zařízení) jako bránu firewall nebo Azure firewall pro kontrolu a protokolování. 

V obou případech přenosová trasa přeruší požadované příchozí připojení ze závislých Azure Data Factory služeb (konkrétně Azure Batch služby pro správu) na Azure-SSIS IR ve virtuální síti. Pokud tomu chcete předejít, definujte jednu nebo více udr v podsíti, která obsahuje Azure-SSIS IR. 

Můžete použít trasu 0.0.0.0/0 s typem dalšího segmentu směrování jako **Internet** v podsíti, která hostuje Azure-SSIS IR ve scénáři Azure ExpressRoute. Můžete také upravit existující trasu 0.0.0.0/0 z typu dalšího segmentu směrování jako **virtuální zařízení** na **Internet** ve scénáři síťové virtuální zařízení.

![Přidat trasu](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

Pokud máte obavy o ztrátu možnosti kontroly odchozího internetového provozu z této podsítě, můžete definovat konkrétní udr pro směrování provozu pouze mezi Azure Batch služby správy a Azure-SSIS IR s typem dalšího segmentu směrování jako **Internet**.

Pokud se například Azure-SSIS IR nachází v `UK South`, měli byste získat seznam rozsahů IP adres služby `BatchNodeManagement.UKSouth` z [odkazu ke stažení](https://www.microsoft.com/en-us/download/details.aspx?id=56519) nebo prostřednictvím [rozhraní API pro zjišťování značek](https://aka.ms/discoveryapi)služby. Pak použijte následující udr trasy rozsahu IP adres s typem dalšího segmentu směrování jako **Internet**.

![Nastavení UDR Azure Batch](media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png)

> [!NOTE]
> U tohoto přístupu se vyskytnou další náklady na údržbu. Pravidelně kontrolujte rozsah IP adres a přidejte do svého UDR nové rozsahy IP adres, abyste se vyhnuli přerušení Azure-SSIS IR. Doporučujeme, abyste provedli kontrolu rozsahu IP adres měsíčně, protože když se nová IP adresa objeví ve značce služby, bude trvat další měsíc. 

### <a name="resource-group"></a>Nastavení skupiny prostředků
Azure-SSIS IR musí vytvořit určité síťové prostředky ve stejné skupině prostředků jako virtuální síť. Mezi tyto prostředky patří:
   -   Nástroj pro vyrovnávání zatížení Azure s názvem *\<Guid >-azurebatch-cloudserviceloadbalancer*.
   -   Veřejná IP adresa Azure s názvem *\<Guid >-azurebatch-cloudservicepublicip*.
   -   Skupina zabezpečení práce v síti s názvem *\<Guid >-azurebatch-cloudservicenetworksecuritygroup*. 

Tyto prostředky budou vytvořeny při spuštění infračerveného signálu. Budou odstraněny po zastavení IR. Chcete-li zabránit zablokování INFRAČERVENého zastavení, neprovádějte opakované použití těchto síťových prostředků v jiných prostředcích. 

Ujistěte se, že nemáte zámek prostředků u skupiny prostředků nebo předplatného, ke kterému virtuální síť patří. Pokud nakonfigurujete zámek jen pro čtení nebo zámek proti odstranění, spuštění a zastavení infračerveného signálu se nemusí zdařit, jinak může infračervený přenos přestat reagovat. 

Ujistěte se, že nemáte zásadu Azure, která znemožňuje vytvoření následujících prostředků v rámci skupiny prostředků nebo předplatného, ke kterému patří virtuální síť: 
   -   Microsoft. Network/LoadBalancers 
   -   Microsoft.Network/NetworkSecurityGroups 
   -   Microsoft.Network/PublicIPAddresses 

### <a name="faq"></a>DOTAZ

- Jak můžu chránit veřejnou IP adresu zveřejněnou v Azure-SSIS IR pro příchozí připojení? Je možné odebrat veřejnou IP adresu?
 
    V současné době se při připojení Azure-SSIS IR k virtuální síti automaticky vytvoří veřejná IP adresa. K dispozici je NSG na úrovni síťové karty, aby bylo možné pouze příchozí připojení k Azure-SSIS IR Azure Batch služby pro správu. Pro příchozí ochranu můžete zadat také NSG na úrovni podsítě.

    Pokud nechcete, aby se veřejná IP adresa nastavila, zvažte [konfiguraci místního prostředí IR jako proxy pro Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis) místo virtuální sítě, pokud to platí pro váš scénář.
 
- Můžu přidat statickou IP adresu Azure-SSIS IR do seznamu povolených adres brány firewall pro daný zdroj dat?
 
    - Pokud je váš zdroj dat místní, po připojení virtuální sítě k místní síti a připojení Azure-SSIS IR k podsíti virtuální sítě můžete přidat rozsah IP adres této podsítě do seznamu povolených.
    - Pokud je zdrojem dat služba Azure, která je podporována s koncovým bodem služby virtuální sítě, můžete nakonfigurovat bod služby virtuální sítě ve vaší virtuální síti a připojit Azure-SSIS IR k této podsíti virtuální sítě. Pak můžete přístup povolen pomocí pravidla virtuální sítě služeb Azure namísto rozsahu IP adres.
    - Pokud je zdrojem dat jiný druh cloudového zdroje dat, můžete použít UDR ke směrování odchozího provozu z Azure-SSIS IR do síťové virtuální zařízení nebo pro Azure Firewall pomocí statické veřejné IP adresy. Do seznamu povolených adres můžete přidat veřejnou IP adresu síťové virtuální zařízení nebo Azure Firewall.
    - Pokud předchozí odpovědi nevyhovují vašim potřebám, zvažte poskytnutí přístupu ke zdroji dat [nakonfigurováním místního prostředí IR jako proxy serveru pro Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). Pak můžete přidat IP adresu počítače, který je hostitelem místního prostředí IR, do seznamu povolených místo připojení Azure-SSIS IR k virtuální síti.

## <a name="azure-portal-data-factory-ui"></a>Azure Portal (uživatelské rozhraní Data Factory)
V této části se dozvíte, jak připojit existující Azure-SSIS IR k virtuální síti (Classic nebo Azure Resource Manager) pomocí uživatelského rozhraní Azure Portal a Data Factory. 

Předtím, než se připojíte k vašemu Azure-SSIS IR k virtuální síti, musíte správně nakonfigurovat virtuální síť. Postupujte podle kroků v části, které se vztahují k vašemu typu virtuální sítě (Classic nebo Azure Resource Manager). Pak postupujte podle kroků v třetí části a připojte se k Azure-SSIS IR k virtuální síti. 

### <a name="configure-an-azure-resource-manager-virtual-network"></a>Konfigurace Azure Resource Manager virtuální sítě

Použijte portál ke konfiguraci Azure Resource Manager virtuální sítě před tím, než se pokusíte připojit k Azure-SSIS IR.

1. Spusťte Microsoft Edge nebo Google Chrome. V současné době pouze tyto webové prohlížeče podporují rozhraní Data Factory. 

1. Přihlaste se na web [Azure Portal](https://portal.azure.com). 

1. Vyberte **Další služby**. Vyfiltrujte a vyberte **virtuální sítě**. 

1. Vyfiltrujte a vyberte svou virtuální síť v seznamu. 

1. Na stránce **virtuální síť** vyberte **vlastnosti**. 

1. Pro zkopírování ID prostředku pro virtuální síť do schránky vyberte tlačítko Kopírovat pro **ID prostředku** . Uloží ID ze schránky ve OneNotu nebo v souboru. 

1. V nabídce vlevo vyberte **podsítě**. Ujistěte se, že počet dostupných adres je větší než uzly ve vašem Azure-SSIS IR. 

1. Ověřte, jestli je poskytovatel Azure Batch zaregistrovaný v předplatném Azure, které má virtuální síť. Nebo zaregistrujte poskytovatele Azure Batch. Pokud už máte účet Azure Batch v předplatném, předplatné se zaregistruje pro Azure Batch. (Pokud vytvoříte Azure-SSIS IR na portálu Data Factory, poskytovatel Azure Batch se automaticky zaregistruje.) 

   a. V Azure Portal v nabídce vlevo vyberte **předplatná**. 

   b. Vyberte své předplatné. 

   c. Na levé straně vyberte **poskytovatelé prostředků**a potvrďte, že **Microsoft. Batch** je registrovaný poskytovatel. 

   ![Potvrzení stavu "zaregistrován"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Pokud v seznamu nevidíte **Microsoft. Batch** a zaregistrujete ho, vytvořte ve svém předplatném [prázdný účet Azure Batch](../batch/batch-account-create-portal.md) . Můžete ho odstranit později. 

### <a name="configure-a-classic-virtual-network"></a>Konfigurace klasické virtuální sítě
Než se pokusíte připojit k Azure-SSIS IR, použijte portál ke konfiguraci klasické virtuální sítě. 

1. Spusťte Microsoft Edge nebo Google Chrome. V současné době pouze tyto webové prohlížeče podporují rozhraní Data Factory. 

1. Přihlaste se na web [Azure Portal](https://portal.azure.com). 

1. Vyberte **Další služby**. Vyfiltrujte a vyberte **virtuální sítě (klasické)** . 

1. Vyfiltrujte a vyberte svou virtuální síť v seznamu. 

1. Na stránce **virtuální síť (Classic)** vyberte **vlastnosti**. 

   ![ID prostředku klasické virtuální sítě](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. Pro zkopírování ID prostředku pro klasickou síť do schránky vyberte tlačítko Kopírovat pro **ID prostředku** . Uloží ID ze schránky ve OneNotu nebo v souboru. 

1. V nabídce vlevo vyberte **podsítě**. Ujistěte se, že počet dostupných adres je větší než uzly ve vašem Azure-SSIS IR. 

   ![Počet dostupných adres ve virtuální síti](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. Připojte se **MicrosoftAzureBatch** k roli **Přispěvatel klasických virtuálních počítačů** pro virtuální síť. 

    a. V nabídce vlevo vyberte **řízení přístupu (IAM)** a vyberte kartu **přiřazení rolí** . 

    ![Tlačítka řízení přístupu a přidat](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

    b. Vyberte **přidat přiřazení role**.

    c. Na stránce **Přidat přiřazení role** vyberte pro **role**možnost **Přispěvatel klasických virtuálních počítačů**. Do pole **Vybrat** vložte **ddbf3205-c6bd-46ae-8127-60eb93363864**a potom v seznamu výsledků hledání vyberte **Microsoft Azure Batch** . 

    ![Výsledky hledání na stránce přidat přiřazení role](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

    d. Vyberte **Uložit** a uložte nastavení a zavřete stránku. 

    ![Uložit nastavení přístupu](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

    e. Ověřte, že se v seznamu přispěvatelů zobrazuje **Microsoft Azure Batch** . 

    ![Potvrdit přístup k Azure Batch](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. Ověřte, jestli je poskytovatel Azure Batch zaregistrovaný v předplatném Azure, které má virtuální síť. Nebo zaregistrujte poskytovatele Azure Batch. Pokud už máte účet Azure Batch v předplatném, předplatné se zaregistruje pro Azure Batch. (Pokud vytvoříte Azure-SSIS IR na portálu Data Factory, poskytovatel Azure Batch se automaticky zaregistruje.) 

   a. V Azure Portal v nabídce vlevo vyberte **předplatná**. 

   b. Vyberte své předplatné. 

   c. Na levé straně vyberte **poskytovatelé prostředků**a potvrďte, že **Microsoft. Batch** je registrovaný poskytovatel. 

   ![Potvrzení stavu "zaregistrován"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Pokud v seznamu nevidíte **Microsoft. Batch** a zaregistrujete ho, vytvořte ve svém předplatném [prázdný účet Azure Batch](../batch/batch-account-create-portal.md) . Můžete ho odstranit později. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Připojit Azure-SSIS IR k virtuální síti

Po nakonfigurování Azure Resource Manager virtuální sítě nebo klasické virtuální sítě se můžete připojit k Azure-SSIS IR k virtuální síti:

1. Spusťte Microsoft Edge nebo Google Chrome. V současné době pouze tyto webové prohlížeče podporují rozhraní Data Factory. 

1. V [Azure Portal](https://portal.azure.com)v nabídce vlevo vyberte **Datové továrny**. Pokud v nabídce nevidíte **Datové továrny** , vyberte **Další služby**a potom v části **Intelligence + Analytics** vyberte **Datové továrny**. 

   ![Seznam datových továren](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Vyberte datovou továrnu pomocí Azure-SSIS IR v seznamu. Zobrazí se Domovská stránka pro datovou továrnu. Vyberte dlaždici **autora & nasazení** . Na samostatné kartě se zobrazí uživatelské rozhraní Data Factory. 

   ![Domovská stránka datové továrny](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. V uživatelském rozhraní Data Factory přepněte na kartu **Upravit** , vyberte **připojení**a přepněte na kartu **prostředí Integration runtime** . 

   ![Karta prostředí Integration runtime](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Pokud je váš Azure-SSIS IR spuštěný, vyberte v seznamu **Integration runtime** ve sloupci **Akce** tlačítko **zastavit** pro vaši Azure-SSIS IR. Nemůžete upravovat IR, dokud ho nezastavíte. 

   ![Zastavit IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. V seznamu **prostředí Integration runtime** vyberte ve sloupci **Akce** tlačítko **Upravit** pro Azure-SSIS IR. 

   ![Úprava prostředí Integration runtime](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. Na panelu **nastavení Integration runtime** se na stránce **Obecné nastavení** a **nastavení SQL** přejděte tak, že vyberete tlačítko **Další** . 

1. Na stránce **Upřesnit nastavení** : 

   a. Zaškrtněte políčko vedle **výběru virtuální**sítě. 

   b. Jako **předplatné**vyberte své předplatné Azure. V rámci předplatného můžete vybrat existující virtuální síť. 
  
   c. Jako **název virtuální**sítě vyberte svou virtuální síť. 

   d. V poli **název podsítě**vyberte podsíť ve virtuální síti. 

   e. Pokud chcete konfigurovat nebo spravovat technologii IR v místním prostředí jako proxy pro váš Azure-SSIS IR, zaškrtněte políčko **nastavit samoobslužné hostování** . Další informace najdete v tématu [Konfigurace prostředí IR pro místní hostování jako proxy serveru pro Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis).

   f. Vyberte tlačítko **ověřování virtuální** sítě. Pokud je ověření úspěšné, klikněte na tlačítko **Další** . 

   ![Upřesňující nastavení pro nastavení infračerveného přenosu](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)

1. Na stránce **Souhrn** Zkontrolujte všechna nastavení Azure-SSIS IR. Pak vyberte tlačítko **aktualizovat** .

1. Spusťte Azure-SSIS IR výběrem tlačítka **Start** ve sloupci **akce** pro vaši Azure-SSIS IR. Spuštění Azure-SSIS IR, který se připojuje k virtuální síti, trvá přibližně 20 až 30 minut. 

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

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
Pokud chcete nakonfigurovat Azure-SSIS IR připojení k virtuální síti, spusťte příkaz `Set-AzDataFactoryV2IntegrationRuntime`: 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Type Managed `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
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
- [Kurz: nasazení balíčků SSIS do Azure](tutorial-create-azure-ssis-runtime-portal.md). V tomto kurzu najdete podrobné pokyny k vytvoření Azure-SSIS IR. Pro hostování katalogu SSIS používá Azure SQL Database. 
- [Vytvořte Azure-SSIS IR](create-azure-ssis-integration-runtime.md). Tento článek se rozbalí v tomto kurzu. Poskytuje pokyny k použití Azure SQL Database s koncovými body služby virtuální sítě nebo se spravovanou instancí ve virtuální síti pro hostování katalogu SSIS. Ukazuje, jak připojit Azure-SSIS IR k virtuální síti. 
- [Monitorování Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). V tomto článku se dozvíte, jak získat informace o Azure-SSIS IR. Poskytuje popisy stavu pro vrácené informace. 
- [Správa Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). V tomto článku se dozvíte, jak zastavit, spustit nebo odstranit Azure-SSIS IR. Také ukazuje, jak horizontální navýšení kapacity Azure-SSIS IR přidáním uzlů.
