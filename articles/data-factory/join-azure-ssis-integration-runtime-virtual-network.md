---
title: Připojte se k prostředí Azure-SSIS IR k virtuální síti | Dokumentace Microsoftu
description: Zjistěte, jak připojit k prostředí Azure-SSIS integration runtime k virtuální síti Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: d89abfd0ec2ae5de8366a12bb38d9358aa8ab76d
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2018
ms.locfileid: "42055244"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Připojte se k prostředí Azure-SSIS integration runtime k virtuální síti
Připojte se k prostředí Azure-SSIS integration runtime (IR) ke službě Azure virtual network v následujících scénářích: 

- Z balíčků SSIS spuštěných v prostředí Azure SSIS Integration Runtime se chcete připojit k místním úložištím dat. 

- Jsou hostiteli databáze katalogu SQL Server Integration Services (SSIS) ve službě Azure SQL Database s virtuální sítě služby koncové body nebo spravované Instance (Preview). 

 Azure Data Factory umožňuje připojit k virtuální síti vytvořené prostřednictvím modelu nasazení classic nebo model nasazení Azure Resource Manageru prostředí Azure-SSIS integration runtime. 

## <a name="access-to-on-premises-data-stores"></a>Přístup k místním úložišti dat.
Pokud balíčků služby SSIS přistupovat k úložištím dat jediný veřejný cloud, není nutné připojení Azure-SSIS IR k virtuální síti. Pokud balíčků služby SSIS přistupovat k úložištím dat místní, musíte připojit Azure-SSIS IR k virtuální síti, který je připojený k místní síti. 

Tady je několik důležitých bodů, mějte na paměti: 

- Pokud není žádná existující virtuální sítě připojené k vaší místní síti, musíte nejprve vytvořit [virtuální sítí Azure Resource Manageru](../virtual-network/quick-create-portal.md#create-a-virtual-network) nebo [klasickou virtuální síť](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) pro vaše prostředí Azure-SSIS integration Runtime připojit. Potom nakonfigurujte site-to-site [připojení brány VPN typu](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) nebo [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) připojení z této virtuální sítě k místní síti. 

- Pokud existuje stávající Azure Resource Manageru nebo klasické virtuální sítě připojené k vaší místní síti ve stejném umístění jako prostředí Azure-SSIS IR, musíte připojit prostředí IR k virtuální síti. 

- Pokud je existující klasické virtuální sítě připojené k vaší místní síti v jiném umístění z prostředí Azure-SSIS IR, můžete nejprve vytvořit [klasickou virtuální síť](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) pro prostředí Azure SSIS IR pro připojení. Potom nakonfigurujte [classic klasické virtuální síti](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md) připojení. Nebo můžete vytvořit [virtuální sítí Azure Resource Manageru](../virtual-network/quick-create-portal.md#create-a-virtual-network) pro vaše prostředí Azure-SSIS integration runtime připojit. Nakonfigurujte [virtuální síť modelu classic na Azure Resource Manageru](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) připojení. 
 
- Pokud je virtuální síť připojená k vaší místní síti v jiném umístění z prostředí Azure-SSIS IR stávající Azure Resource Manageru, je nejprve vytvořit [virtuální sítí Azure Resource Manageru](../virtual-network/quick-create-portal.md##create-a-virtual-network) pro Azure-SSIS Prostředí IR pro připojení. Potom nakonfigurujte připojení virtuální sítě k Azure Resource Manageru do Azure Resource Manageru. Nebo můžete vytvořit [klasickou virtuální síť](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) pro prostředí Azure SSIS IR pro připojení. Potom nakonfigurujte [virtuální síť modelu classic na Azure Resource Manageru](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) připojení. 

## <a name="host-the-ssis-catalog-database-in-azure-sql-database-with-virtual-network-service-endpointsmanaged-instance-preview"></a>Hostovat databázi katalogu služby SSIS v Azure SQL Database s virtuální sítě služby koncové body nebo spravované Instance (Preview)
Pokud katalog služby SSIS je hostovaný ve službě Azure SQL Database s koncové body služeb virtuální sítě nebo spravované Instance (Preview), můžete připojit k prostředí Azure-SSIS IR do: 

- Stejné virtuální síti 
- Jiné virtuální sítě, který má připojení k síťovým názvem, který se používá pro službu Azure SQL Database s virtuální sítě služby koncové body nebo spravované Instance (Preview) 

Když se do programu Azure-SSIS IR do stejné virtuální síti jako Managed Instance, ujistěte se, že Azure-SSIS IR je v jiné podsíti, než Managed Instance. Když se do programu Azure-SSIS IR k jiné virtuální sítě, než Managed Instance, doporučujeme, abyste partnerský vztah virtuální sítě (což je omezený na stejné oblasti) nebo virtuální sítě pro připojení k virtuální síti. Zobrazit [vaši aplikaci do Azure SQL Database Managed Instance připojit](../sql-database/sql-database-managed-instance-connect-app.md).

Virtuální sítě můžou být nasazené prostřednictvím modelu nasazení classic nebo model nasazení Azure Resource Manageru.

Následující oddíly poskytují další podrobnosti. 

## <a name="requirements-for-virtual-network-configuration"></a>Požadavky na konfiguraci virtuální sítě
-   Ujistěte se, že `Microsoft.Batch` je registrovaný poskytovatel v rámci předplatného vaší podsítě virtuální sítě, který je hostitelem prostředí Azure-SSIS IR. Pokud použijete klasické virtuální síti připojit také `MicrosoftAzureBatch` role Přispěvatel virtuálních počítačů modelu Classic pro tuto virtuální síť. 

-   Vyberte správnou podsíť k hostování prostředí Azure-SSIS IR. Zobrazit [vyberte podsíť](#subnet). 

-   Pokud používáte vlastní server služby DNS (Domain Name) ve virtuální síti, přečtěte si téma [Domain Name Services server](#dns_server). 

-   Pokud používáte skupiny zabezpečení sítě (NSG) v podsíti, přečtěte si téma [skupina zabezpečení sítě](#nsg) 

-   Pokud používáte Azure Express Route nebo konfigurace uživatelem definovanou trasou (UDR), přečtěte si téma [pomocí Azure ExpressRoute nebo uživatelem definovaná trasa](#route). 

-   Ujistěte se, že skupiny prostředků ve virtuální síti můžete vytvářet a odstraňovat určitých prostředků sítě Azure. Zobrazit [požadavky pro skupinu prostředků](#resource-group). 

### <a name="subnet"></a> Vyberte podsíť
-   Nesmí být zvolen GatewaySubnet pro nasazení prostředí Azure-SSIS Integration Runtime, protože je vyhrazený pro brány virtuální sítě. 

-   Zajistěte, aby podsíť, ve které jste vybrali dostatečný k dispozici adresní prostor pro prostředí Azure-SSIS IR používat. Nechte aspoň 2 * číslo uzlu reakcí na Incidenty v dostupných IP adres. Některé IP adresy v rámci každé podsítě vyhrazuje Azure a tyto adresy nelze použít. První a poslední IP adresy podsítí jsou vyhrazené pro udržování souladu s protokoly, spolu s tři další adresy používané pro služby Azure. Další informace najdete v tématu [existují nějaká omezení týkající se použití IP adresy v rámci těchto podsítí?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets). 

-   Nepoužívejte podsítě, která je výhradně obsazena dalšími službami Azure (například SQL Database Managed Instance (Preview), služby App Service, atd.). 

### <a name="dns_server"></a> Server služby název domény 
Pokud je potřeba použít vlastní server služby DNS (Domain Name) ve virtuální síti připojí pomocí prostředí Azure-SSIS integration runtime, ujistěte se, že překlad názvů hostitelů veřejný Azure (třeba název služby Azure Storage blob, `<your storage account>.blob.core.windows.net`). 

Doporučujeme následující kroky: 

-   Konfigurace vlastního DNS ke směrování žádostí na službu Azure DNS. Můžete dál nevyřešené záznamy DNS na IP adresu rekurzivní překladače Azure (168.63.129.16) na serveru DNS. 

-   Nastavení vlastního DNS jako primární a DNS Azure jako sekundární virtuální sítě. Zaregistrujte se v případě, že DNS server není k dispozici IP adresu rekurzivního překladače Azure (168.63.129.16) jako sekundární server DNS. 

Další informace najdete v tématu [překlad názvů, který používá vlastní server DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). 

### <a name="nsg"></a> Skupina zabezpečení sítě
Pokud potřebujete implementovat skupinu zabezpečení sítě (NSG) pro podsíť používá prostředí Azure-SSIS integration runtime, povolený příchozí/odchozí provoz přes následující porty: 

| Směr | Přenosový protokol | Zdroj | Rozsah zdrojových portů | Cíl | Rozsah cílových portů | Komentáře |
|---|---|---|---|---|---|---|
| Příchozí | TCP | Internet | * | VirtualNetwork | 29876, 29877 (když se do programu prostředí IR k virtuální síti Azure Resource Manageru) <br/><br/>10100, 20100, 30100 (když se do programu prostředí IR k virtuální síti classic)| Služba Data Factory používá následující porty pro komunikaci s uzly prostředí Azure-SSIS integration runtime ve virtuální síti. <br/><br/> Jestli NSG úrovni podsítě vytvořit, nebo Ne, služby Data Factory vždy nakonfiguruje skupiny NSG na úrovni karty síťového rozhraní (NIC) připojených k virtuálním počítačům, které jsou hostiteli Azure-SSIS IR. V této úrovni síťové karty, skupiny zabezpečení sítě je povolen pouze příchozí provoz z Data Factory IP adresy zadané porty. I v případě, že otevřete tyto porty pro přenosy z Internetu na úrovni podsítě, jsou blokovány přenosy z IP adresy, které nejsou Data Factory IP adresy na úrovni síťové karty. |
| Odchozí | TCP | VirtualNetwork | * | Internet | 443 | Uzly vašeho prostředí Azure-SSIS integration runtime ve službě virtual network používat tento port pro přístup ke službám Azure, jako je například Azure Storage a Azure Event Hubs. |
| Odchozí | TCP | VirtualNetwork | * | Internet nebo Sql | 1433, 11000 11999, 14000 14999 | Uzly prostředí Azure-SSIS integration runtime používá virtuální síť, tyto porty pro přístup k SSISDB hostuje váš server Azure SQL Database – tento účel neplatí pro databází SSISDB hostovanou ve spravované Instance (Preview). |
||||||||

### <a name="route"></a> Pomocí Azure ExpressRoute nebo uživatelem definované trasy
Můžete se připojit [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) okruh k vaší infrastruktuře virtuální sítě můžete rozšířit místní síť do Azure. 

Běžnou konfigurací je použití vynuceného tunelování (Inzerovat trasy protokolu BGP, 0.0.0.0/0 k virtuální síti) který vynutí odchozího internetového provozu z toku virtuální síť k místní síti zařízení pro účely zkoumání a protokolování. Tento tok provozu přestane fungovat připojení mezi Azure-SSIS IR ve virtuální síti s závislé služby Azure Data Factory. Řešením je definují jednu (nebo více) [trasy definované uživatelem (udr)](../virtual-network/virtual-networks-udr-overview.md) na podsíť, která obsahuje prostředí Azure-SSIS IR. Definuje trasu UDR trasy konkrétní podsítě, které se uplatní místo trasy protokolu BGP. 

Nebo můžete definovat trasy definované uživatelem (udr) k vynucení odchozího internetového provozu z podsítě, který je hostitelem prostředí Azure-SSIS IR k jiné podsíti, který je hostitelem virtuální síťové zařízení, jako je brána firewall nebo hostitele hraniční sítě pro účely zkoumání a protokolování. 

V obou případech platí trasy 0.0.0.0/0 s typem dalšího segmentu směrování jako **Internet** v podsíti, který je hostitelem prostředí Azure-SSIS IR, aby komunikace mezi službou Data Factory a Azure-SSIS IR je úspěšné. 

![Přidání trasy](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

Pokud máte obavy o ztrátu schopnost kontrolovat odchozího internetového provozu z dané podsítě, můžete také přidat pravidlo skupiny zabezpečení sítě na podsíť, kterou chcete omezit odchozí cíle [Azure datových center IP adresy](https://www.microsoft.com/download/details.aspx?id=41653). 

Zobrazit [tento skript Powershellu](https://gallery.technet.microsoft.com/scriptcenter/Adds-Azure-Datacenter-IP-dbeebe0c) příklad. Budete muset spustit skript každý týden k udržování aktuálnosti seznamu IP adres Azure data center. 

### <a name="resource-group"></a> Požadavky pro skupinu prostředků
-   Prostředí Azure-SSIS IR je potřeba vytvořit určitým síťovým prostředkům ve stejné skupině prostředků jako virtuální síť. Tyto prostředky zahrnují následující:
    -   Azure load balancer, s názvem  *<Guid>- azurebatch cloudserviceloadbalancer*.
    -   Azure veřejnou IP adresu, s názvem  *<Guid>- azurebatch cloudservicepublicip*.
    -   Skupinu zabezpečení sítě práce s názvem  *<Guid>- azurebatch cloudservicenetworksecuritygroup*. 

-   Ujistěte se, že nemáte žádný zámek prostředků na skupinu prostředků nebo předplatného, ke které patří virtuální síť. Pokud nakonfigurujete zámek jen pro čtení nebo zámek proti odstranění, spouštění a zastavování prostředí IR může selhat nebo přestane reagovat. 

-   Ujistěte se, že není nutné zásady služby Azure, které brání následující prostředky vytváří v rámci skupiny prostředků nebo předplatného, ke které patří virtuální síť: 
    -   Microsoft.Network/LoadBalancers 
    -   Microsoft.Network/NetworkSecurityGroups 
    -   Microsoft.Network/PublicIPAddresses 

## <a name="azure-portal-data-factory-ui"></a>Azure portal (uživatelské rozhraní služby Data Factory)
V této části se dozvíte, jak propojit existující modulu runtime Azure-SSIS k virtuální síti (classic nebo Azure Resource Manageru) pomocí webu Azure portal a uživatelské rozhraní služby Data Factory. Nejprve budete muset před příchodem do prostředí Azure-SSIS IR k němu správně nakonfigurovat ve virtuální síti. Projděte si jeden z následujících dvou oddílů na základě typu virtuální síť (classic nebo Azure Resource Manager). Potom pokračujte v třetí části připojit Azure-SSIS IR k virtuální síti. 

### <a name="use-the-portal-to-configure-an-azure-resource-manager-virtual-network"></a>Konfigurace virtuální sítě Azure Resource Manageru pomocí portálu
Musíte nakonfigurovat virtuální sítě, než bude možné připojit Azure-SSIS IR k němu. 

1. Spuštění Microsoft Edge nebo Google Chrome. Uživatelské rozhraní služby Data Factory v současné době se podporuje jenom v těchto webových prohlížečů. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 

1. Vyberte **další služby**. Filtrovat a vybrat **virtuální sítě**. 

1. Filtrovat a v seznamu vyberte virtuální síť. 

1. Na **virtuální síť** stránce **vlastnosti**. 

1. Klikněte na tlačítko kopírování pro **ID prostředku** zkopírovat ID prostředku virtuální sítě do schránky. Uložte si ID ze schránky do Onenotu nebo soubor. 

1. Vyberte **podsítě** v nabídce vlevo. Ujistěte se, že počet **dostupných adres** je větší než uzly v prostředí Azure-SSIS integration runtime. 

1. Ověřte, že zprostředkovatel služby Azure Batch je zaregistrovaný v rámci předplatného Azure, která obsahuje virtuální síť. Nebo zaregistrujte zprostředkovatele služby Azure Batch. Pokud již máte účet Azure Batch v rámci vašeho předplatného, vaše předplatné je zaregistrovaný pro službu Azure Batch. (Pokud na portálu služby Data Factory vytvořit prostředí Azure-SSIS IR, zprostředkovatele služby Azure Batch se automaticky registruje za vás.) 

   a. Na webu Azure portal, vyberte **předplatná** v nabídce vlevo. 

   b. Vyberte své předplatné. 

   c. Vyberte **poskytovatelů prostředků** na levé straně a ujistěte se, že **Microsoft.Batch** je registrovaný poskytovatel. 

   ![Potvrzení stavu "Registrováno"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Pokud nevidíte **Microsoft.Batch** v seznamu k registraci, [vytvořit účet Azure Batch prázdný](../batch/batch-account-create-portal.md) ve vašem předplatném. Můžete ho odstranit později. 

### <a name="use-the-portal-to-configure-a-classic-virtual-network"></a>Použití portálu ke konfiguraci klasickou virtuální síť
Musíte nakonfigurovat virtuální sítě, než bude možné připojit Azure-SSIS IR k němu. 

1. Spuštění Microsoft Edge nebo Google Chrome. Uživatelské rozhraní služby Data Factory v současné době je podporována pouze pro tyto webové prohlížeče. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 

1. Vyberte **další služby**. Filtrovat a vybrat **virtuální sítě (klasické)**. 

1. Filtrovat a v seznamu vyberte virtuální síť. 

1. Na **virtuální síť (klasická)** stránce **vlastnosti**. 

   ![ID prostředku klasické virtuální sítě](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. Klikněte na tlačítko kopírování pro **ID prostředku** ID prostředku pro klasickou síť zkopírovat do schránky. Uložte si ID ze schránky do Onenotu nebo soubor. 

1. Vyberte **podsítě** v nabídce vlevo. Ujistěte se, že počet **dostupných adres** je větší než uzly v prostředí Azure-SSIS integration runtime. 

   ![Počet dostupných adres ve virtuální síti](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. Připojte se k **MicrosoftAzureBatch** k **Přispěvatel virtuálních počítačů modelu Classic** role pro virtuální síť. 

    a. Vyberte **řízení přístupu (IAM)** v levé nabídce a vyberte **přidat** na panelu nástrojů. 

    !["Řízení přístupu" a "Přidání" tlačítka](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

    b. Na **přidat oprávnění** stránce **Přispěvatel virtuálních počítačů modelu Classic** pro **Role**. Vložit **ddbf3205-c6bd-46ae-8127-60eb93363864** v **vyberte** a potom vyberte **Microsoft Azure Batch** ze seznamu výsledků hledání. 

    ![Na stránce "Přidání oprávnění" výsledky hledání](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

    c. Vyberte **Uložit** nastavení uložte a zavřete stránku. 

    ![Uložit nastavení přístupu](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

    d. Zkontrolujte, jestli se **Microsoft Azure Batch** v seznamu přispěvatelé. 

    ![Ověření přístupu služby Azure Batch](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. Ověřte, že zprostředkovatel služby Azure Batch je zaregistrovaný v rámci předplatného Azure, která obsahuje virtuální síť. Nebo zaregistrujte zprostředkovatele služby Azure Batch. Pokud již máte účet Azure Batch v rámci vašeho předplatného, vaše předplatné je zaregistrovaný pro službu Azure Batch. (Pokud na portálu služby Data Factory vytvořit prostředí Azure-SSIS IR, zprostředkovatele služby Azure Batch se automaticky registruje za vás.) 

   a. Na webu Azure portal, vyberte **předplatná** v nabídce vlevo. 

   b. Vyberte své předplatné. 

   c. Vyberte **poskytovatelů prostředků** na levé straně a ujistěte se, že **Microsoft.Batch** je registrovaný poskytovatel. 

   ![Potvrzení stavu "Registrováno"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Pokud nevidíte **Microsoft.Batch** v seznamu k registraci, [vytvořit účet Azure Batch prázdný](../batch/batch-account-create-portal.md) ve vašem předplatném. Můžete ho odstranit později. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Připojení Azure-SSIS IR k virtuální síti
1. Spuštění Microsoft Edge nebo Google Chrome. Uživatelské rozhraní služby Data Factory v současné době se podporuje jenom v těchto webových prohlížečů. 

1. V [webu Azure portal](https://portal.azure.com)vyberte **datových továren** v nabídce vlevo. Pokud nevidíte **datových továren** v nabídce vyberte **další služby**a pak zaškrtněte **datových továren** v **INTELIGENCE a analýza**oddílu. 

   ![Seznam objektů pro vytváření dat](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. V seznamu vyberte své datové továrny pomocí prostředí Azure-SSIS integration runtime. Zobrazí domovská stránka datové továrny. Vyberte **vytvořit a nasadit** dlaždici. Zobrazí na samostatné kartě uživatelské rozhraní služby Data Factory. 

   ![Domovská stránka datové továrny](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. V Uživatelském rozhraní Data Factory přepněte na **upravit** kartu, vyberte možnost **připojení**a přepnout **prostředí Integration runtime** kartu. 

   ![Karta "Integration runtime"](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Pokud prostředí Azure-SSIS IR je spuštěn v seznamu modulu runtime integrace, vyberte **Zastavit** tlačítko **akce** sloupec pro vaše prostředí Azure-SSIS IR. Prostředí IR nelze upravit, dokud jej nezastavíte. 

   ![Zastavit prostředí IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. Vyberte v seznamu modulu runtime integrace **upravit** tlačítko **akce** sloupec pro vaše prostředí Azure-SSIS IR. 

   ![Upravit prostředí integration runtime](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. Na **obecné nastavení** stránku **instalace prostředí Integration Runtime** okně **Další**. 

   ![Obecná nastavení pro nastavení prostředí IR](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-general-settings.png)

1. Na **nastavení SQL** stránky, zadejte heslo správce a vyberte **Další**. 

   ![Nastavení systému SQL Server pro instalace prostředí IR](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-sql-settings.png)

1. Na **Upřesnit nastavení** stránce, proveďte následující akce: 

   a. Zaškrtněte políčko pro **vybrat virtuální síť pro vaše prostředí Azure-SSIS Integration Runtime připojit a povolit službám Azure konfigurovat oprávnění a nastavení virtuální sítě**. 

   b. Pro **typ**vyberte, jestli se virtuální síť je klasickou virtuální síť nebo virtuální síť Azure Resource Manageru. 

   c. Pro **název virtuální sítě**, vyberte virtuální síť. 

   d. Pro **název podsítě**, vyberte podsíť ve virtuální síti. 

   e. Klikněte na tlačítko **ověření virtuální sítě** a v případě úspěchu, klikněte na tlačítko **aktualizace**. 

   ![Pokročilé nastavení instalace prostředí IR](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)

1. Nyní můžete spustit prostředí IR pomocí **Start** tlačítko **akce** sloupec pro vaše prostředí Azure-SSIS IR. Trvá přibližně 20 až 30 minut na spuštění Azure-SSIS IR. 

## <a name="azure-powershell"></a>Azure PowerShell

### <a name="configure-a-virtual-network"></a>Konfigurace virtuální sítě
Musíte nakonfigurovat virtuální sítě, než bude možné připojit Azure-SSIS IR k němu. Automaticky konfigurovat oprávnění a nastavení virtuální sítě pro prostředí Azure-SSIS integration runtime k virtuální síti připojit, přidejte následující skript:

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Vytvořte prostředí Azure-SSIS IR a připojte ho k virtuální síti
Můžete vytvořit prostředí Azure-SSIS IR a připojte ho k virtuální síti ve stejnou dobu. Celý skript a pokyny najdete v tématu [vytvořit prostředí Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md#azure-powershell).

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Připojte se k existující Azure-SSIS IR k virtuální síti
Skript v [vytvořit prostředí Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md) článku se dozvíte, jak vytvořit prostředí Azure-SSIS IR a připojte ho k virtuální síti ve stejném skriptu. Pokud máte existující prostředí Azure-SSIS IR, proveďte následující kroky pro připojení k virtuální síti: 
1. Zastavit prostředí Azure-SSIS IR. 
1. Konfigurace prostředí Azure-SSIS IR k virtuální síti připojit. 
1. Spuštění prostředí Azure-SSIS IR. 

### <a name="define-the-variables"></a>Definování proměnné
```powershell
$ResourceGroupName = "<your Azure resource group name>"
$DataFactoryName = "<your Data Factory name>" 
$AzureSSISName = "<your Azure-SSIS IR name>"
# Specify the information about your classic or Azure Resource Manager virtual network.
$VnetId = "<your Azure virtual network resource ID>"
$SubnetName = "<the name of subnet in your virtual network>"
```

### <a name="stop-the-azure-ssis-ir"></a>Zastavit prostředí Azure-SSIS IR
Prostředí Azure-SSIS integration runtime zastavte, než bude možné připojit k virtuální síti. Tento příkaz uvolní všechny jeho uzly a zastaví fakturaci:

```powershell
Stop-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Nakonfigurujte nastavení virtuální sítě pro Azure-SSIS IR pro připojení
```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="configure-the-azure-ssis-ir"></a>Konfigurace prostředí Azure-SSIS IR
Pokud chcete nakonfigurovat prostředí Azure-SSIS IR k virtuální síti připojit, spusťte `Set-AzureRmDataFactoryV2IntegrationRuntime` příkaz: 

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName `
                                           -Type Managed `
                                           -VnetId $VnetId `
                                           -Subnet $SubnetName
```

### <a name="start-the-azure-ssis-ir"></a>Spuštění prostředí Azure-SSIS IR
Pokud chcete spustit prostředí Azure-SSIS integration runtime, spusťte následující příkaz: 

```powershell
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```

Tento příkaz trvá 20 až 30 minut na dokončení.

## <a name="next-steps"></a>Další postup
Další informace o modulu runtime Azure-SSIS najdete v následujících tématech: 
- [Prostředí Azure-SSIS integration runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Tento článek obsahuje koncepční informace o prostředí integration runtime, včetně Azure-SSIS IR. 
- [Kurz: Nasazení balíčků SSIS do Azure](tutorial-create-azure-ssis-runtime-portal.md) Tento článek obsahuje podrobné pokyny pro vytvoření Azure-SSIS IR. Azure SQL Database používá k hostování katalogu SSIS. 
- [Vytvoření prostředí Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md). Tento článek dál navazuje na tento kurz a obsahuje pokyny k používání Azure SQL Database s virtuální sítě služby koncové body nebo spravované Instance (Preview) k hostování katalogu SSIS a připojení IR k virtuální síti. 
- [Monitorování Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Tento článek ukazuje, jak načíst informace o Azure-SSIS IR a popisy stavů ve vrácených informacích. 
- [Správa Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). Tento článek ukazuje, jak zastavit, spustit nebo odebrat Azure-SSIS IR. Je také ukazuje, jak horizontální navýšení kapacity Azure-SSIS IR přidáním uzlů. 
