---
title: Připojení k virtuální síti Azure SSIS integrace runtime | Microsoft Docs
description: Zjistěte, jak propojit runtime integrace Azure SSIS virtuální sítě Azure.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: douglasl
ms.openlocfilehash: b998b47cdc65be91f62543369f5c3f18e4f270c4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34619639"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Připojení modulu runtime integrace Azure SSIS k virtuální síti.
Vaše Azure SSIS integrace modulu runtime (IR) připojení k virtuální síť Azure v následujících scénářích: 

- Jsou hostiteli databáze katalogu integrační služby SSIS (SQL Server) na Azure SQL Database spravované Instance (Preview) ve virtuální síti.
- Z balíčků SSIS spuštěných v prostředí Azure SSIS Integration Runtime se chcete připojit k místním úložištím dat.

 Azure Data Factory verze 2 (Preview) umožňuje připojení k vaší runtime integrace Azure SSIS k virtuální síti vytvořené pomocí modelu nasazení classic nebo modelu nasazení Azure Resource Manager. 

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verze 1 služby Data Factory, který je v obecně dostupnosti (GA), najdete v článku [dokumentace verze 1 pro vytváření dat](v1/data-factory-introduction.md).

## <a name="access-to-on-premises-data-stores"></a>Přístup k místním úložištím dat.
Pokud balíčky SSIS přistupovat k úložištím dat pouze veřejného cloudu, nemusíte IR Azure SSIS připojit k virtuální síti. Pokud balíčky SSIS přístup k místním úložištím dat, musíte IR Azure SSIS připojit k virtuální síti, která je připojena k místní síti. 

Tady je několik bodů důležité si uvědomit: 

- Pokud není žádná existující virtuální sítě připojený k síti na pracovišti, nejprve vytvořit [virtuální sítě Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) nebo [klasickou virtuální síť](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) pro svoji integraci Azure SSIS modul runtime pro připojení. Potom nakonfigurujte site-to-site [připojení brány sítě VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) nebo [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) připojení z této virtuální sítě k síti na pracovišti.
- Pokud jde o existující Azure Resource Manager nebo classic virtuální sítě, které jsou připojené k místní síti ve stejném umístění jako vaše IR Azure SSIS, můžete IR připojení k virtuální síti.
- Pokud je existující klasické virtuální sítě připojený k síti na pracovišti v jiném umístění z vaší IR Azure SSIS, je nejprve vytvořit [klasickou virtuální síť](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) pro vaši Azure SSIS IR pro připojení. Potom nakonfigurujte [classic klasické virtuální síti](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md) připojení. Nebo můžete vytvořit [virtuální sítě Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) pro vaše integrace Azure SSIS modulu runtime pro připojení. Nakonfigurujte [virtuální sítě classic Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) připojení.
- Pokud je virtuální sítě připojený k síti na pracovišti v jiném umístění z vaší služby SSIS Azure IR existující správce prostředků Azure, je nejprve vytvořit [virtuální sítě Azure Resource Manager](../virtual-network/quick-create-portal.md##create-a-virtual-network) pro Azure-SSIS Pro připojení reakcí na Incidenty. Potom nakonfigurujte připojení k virtuální síti Azure Resource Manager Azure Resource Manager. Nebo můžete vytvořit [klasickou virtuální síť](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) pro vaši Azure SSIS IR pro připojení. Potom nakonfigurujte [virtuální sítě classic Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) připojení.

## <a name="host-the-ssis-catalog-database-on-azure-sql-database-managed-instance"></a>Hostovat databázi SSIS Catalog na Azure spravované Instance databáze SQL 
Pokud katalog služby SSIS je hostována v spravované Instance SQL Database (Preview) ve virtuální síti, můžete připojit Azure SSIS Reakcí na:

- Stejné virtuální síti.
- Jinou virtuální síť, která má do síťového připojení s takový, který má spravované Instance SQL Database (Preview). 

Virtuální sítě můžou být nasazené prostřednictvím modelu nasazení classic nebo modelu nasazení Azure Resource Manager. Pokud se chystáte připojit k Azure SSIS IR *stejné virtuální síti* má spravované Instance SQL Database (Preview), zajistěte, aby IR Azure SSIS v *jiné podsíti* z takový, který má SQL Spravované Instance databáze (Preview).   

Další podrobnosti naleznete v následujících částech.

## <a name="requirements-for-virtual-network-configuration"></a>Požadavky pro konfiguraci virtuální sítě

-   Ujistěte se, že `Microsoft.Batch` je registrovaný poskytovatel v rámci předplatného podsítě vaší virtuální sítě, který hostuje infračerveného signálu Azure SSIS. Pokud používáte klasické virtuální sítě, také připojit `MicrosoftAzureBatch` k roli Přispěvatel Classic virtuálních počítačů pro tuto virtuální síť.

-   Vyberte správnou podsíť k hostování infračerveného signálu Azure SSIS. V tématu [vyberte podsíť](#subnet).

-   Pokud používáte vlastní server služby DNS (Domain Name) ve virtuální síti, najdete v části [Domain Name Services server](#dns_server).

-   Pokud používáte skupiny zabezpečení sítě (NSG) v podsíti, přečtěte si téma [skupinu zabezpečení sítě](#nsg)

-   Pokud používáte Azure Express trasy nebo konfigurace trasy definované uživatele (UDR), přečtěte si téma [použití Azure ExpressRoute nebo trasu definovaná uživatelem](#route).

-   Zajistěte, aby skupina prostředků virtuální sítě můžete vytvářet a odstraňovat určité Azure síťovým prostředkům. V tématu [požadavky pro skupinu prostředků](#resource-group).

### <a name="subnet"></a> Vyberte podsíť
-   Nevybírejte GatewaySubnet pro nasazení modulu Runtime integrace Azure SSIS, protože je vyhrazen pro brány virtuální sítě.

-   Zajistěte, aby podsíť, kterou vyberete dostatek prostoru adres k dispozici pro Azure SSIS IR používat. Nechte aspoň 2 * číslo uzlu reakcí na Incidenty v dostupných IP adres. Azure si vyhrazuje některé IP adresy v rámci každé podsítě a tyto adresy nelze použít. První a poslední IP adresy podsítě jsou vyhrazené pro protokol shoda, společně s tři další adresy používané pro služby Azure. Další informace najdete v tématu [existují nějaká omezení na pomocí IP adresy v rámci těchto podsítí?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets).

-   Nepoužívejte podsíť, která je výhradně obsazena jinými službami Azure (například databáze spravované Instance systému SQL služby App Service).

### <a name="dns_server"></a> Server služby název domény 
Pokud budete muset použít vlastní server služby DNS (Domain Name) ve virtuální síti připojí pomocí vaší runtime integrace Azure SSIS, ujistěte se, překlad názvů veřejný Azure hostitele (například pomocí názvu úložiště Azure blob, `<your storage account>.blob.core.windows.net`).

Doporučujeme následující kroky:

-   Nakonfigurujte vlastní DNS pro předávání požadavků do Azure DNS. Na serveru DNS, může předat nerozpoznané záznamy DNS na IP adresu překladače rekurzivní Azure (168.63.129.16).

-   Nastavte vlastní DNS jako primární a Azure DNS jako sekundární sítě vnet. Zaregistrujte Azure rekurzivní překladače (168.63.129.16) na IP adresu jako sekundární server DNS v případě, že DNS server není k dispozici.

Další informace najdete v tématu [překlad, který používá DNS server](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

### <a name="nsg"></a> Skupina zabezpečení sítě
Pokud potřebujete implementovat skupinu zabezpečení sítě (NSG) ve virtuální síti připojí pomocí vaší runtime integrace Azure SSIS, povolte příchozí nebo odchozí provoz prostřednictvím následující porty:

| Směr | Přenosový protokol | Zdroj | Rozsah zdrojový Port | Cíl | Rozsah cílový Port | Komentáře |
|---|---|---|---|---|---|---|
| Příchozí | TCP | Internet | * | VirtualNetwork | 29876, 29877 (Pokud Reakcí připojíte k virtuální síti Azure Resource Manager) <br/><br/>10100, 20100, 30100 (Pokud se připojíte k klasickou virtuální síť IR)| Služba Data Factory používá tyto porty pro komunikaci s uzly, které vaše integrace modulu runtime Azure SSIS ve virtuální síti. <br/><br/> Jestli jste určili skupinu NSG, nebo Ne, Data Factory vždy nakonfiguruje skupinu NSG na úrovni karty síťového rozhraní (NIC) připojených k virtuálním počítačům, které jsou hostiteli infračerveného signálu Azure SSIS. Je povolen pouze příchozí provoz z dat objektu pro vytváření IP adres. I v případě, že otevřete tyto porty pro provoz sítě Internet, je blokován provoz z IP adresy, které nejsou adresy IP objekt pro vytváření dat na úrovni síťový adaptér. |
| Odchozí | TCP | VirtualNetwork | * | Internet | 443 | Tento port použít uzly vaší runtime integrace Azure SSIS ve virtuální síti přístupu ke službám Azure, například Azure Storage a Azure Event Hubs. |
| Odchozí | TCP | VirtualNetwork | * | Internet nebo jazyka Sql | 1433, 11000 11999, 14000 14999 | Uzly vaše integrace modulu runtime Azure SSIS ve virtuální síti používat tyto porty pro přístup k SSISDB hostované serverem Azure SQL Database. (Tento účel není použitelná pro SSISDB hostované spravované Instance SQL Database (Preview).) |
||||||||

### <a name="route"></a> Použití Azure ExpressRoute nebo trasy definované uživatelem

Můžete připojit [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) okruhu vaší infrastruktury virtuální sítě k rozšíření místní sítě do Azure. 

Obvyklé konfigurace se má používat vynucené tunelování (Inzerovat trasy protokolu BGP, 0.0.0.0/0 do virtuální sítě) který vynutí odchozí internetové přenosy z toku virtuální sítě do místní síťové zařízení pro kontrolu a protokolování. Tento tok provozu dělí připojení mezi Azure SSIS IR ve virtuální síti s závislé služby Azure Data Factory. Řešení je definovat jeden (nebo více) [trasy definované uživatelem (udr)](../virtual-network/virtual-networks-udr-overview.md) na podsíť, která obsahuje infračerveného signálu Azure SSIS. UDR definuje tras konkrétní podsítě, které jsou dodržení místo trasy protokolu BGP.

Nebo můžete definovat trasy definované uživatelem (udr) Chcete-li vynutit odchozí internetové přenosy z podsítě, který hostuje IR Azure SSIS k jiné podsíti, který hostuje virtuální síťové zařízení, jako je brána firewall nebo hostitel hraniční sítě pro kontrolu a protokolování.

V obou případech použít trasu 0.0.0.0/0 s typ dalšího směrování jako **Internet** v podsíti, který hostuje IR Azure SSIS, tak, aby mohlo být úspěšné komunikaci mezi služba Data Factory a je Reakcí Azure SSIS. 

![Přidejte trasu](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

Pokud máte obavy o ztráty možnosti Zkontrolujte odchozí internetové přenosy od této podsíti, můžete také přidat pravidlo NSG na podsítě, který omezit odchozí cíle k [Azure datového centra IP adresy](https://www.microsoft.com/download/details.aspx?id=41653).

V tématu [tento skript prostředí PowerShell](https://gallery.technet.microsoft.com/scriptcenter/Adds-Azure-Datacenter-IP-dbeebe0c) příklad. Budete muset spustit skript každý týden k zachování aktualizovaného stavu seznamu IP adres center dat Azure.

### <a name="resource-group"></a> Požadavky pro skupinu prostředků
IR Azure SSIS potřebuje k vytvoření určité síťové prostředky v rámci stejné skupině prostředků jako virtuální síť, včetně Vyrovnávání zatížení Azure, Azure veřejnou IP adresu a pracovní skupina zabezpečení sítě.

-   Ujistěte se, že nemáte žádné Zámek prostředků ve skupině prostředků nebo předplatného, do které patří virtuální sítě. Pokud nakonfigurujete zámek jen pro čtení nebo odstranění zámku, spuštění a zastavení IR může selhat nebo zablokuje.

-   Ujistěte se, že nemáte zásady služby Azure, který zabraňuje v následujících zdrojích informací se vytvořil v rámci skupiny prostředků nebo předplatného, do které patří virtuální sítě:
    -   Microsoft.Network/LoadBalancers
    -   Microsoft.Network/NetworkSecurityGroups
    -   Microsoft.Network/PublicIPAddresses

## <a name="azure-portal-data-factory-ui"></a>Portál Azure (objekt pro vytváření dat uživatelského rozhraní)
V této části se dozvíte, jak připojit existující runtime Azure SSIS k virtuální síti (classic nebo Azure Resource Manager) pomocí portálu Azure a objekt pro vytváření dat uživatelského rozhraní. Nejdřív musíte vhodně nakonfigurujte virtuální síť před připojením k němu vaše Azure SSIS reakcí na Incidenty. Přejděte prostřednictvím jednoho z následujících dvou částech na základě typu virtuální sítě (classic nebo Azure Resource Manager). Potom pokračujte třetí oddílu, který má připojení k vaší Azure SSIS Reakcí do virtuální sítě. 

### <a name="use-the-portal-to-configure-an-azure-resource-manager-virtual-network"></a>Nakonfigurujte virtuální síť Azure Resource Manager pomocí portálu
Musíte nakonfigurovat virtuální síť, než bude možné připojit Azure SSIS Reakcí na ni.

1. Spusťte Microsoft Edge nebo Google Chrome. Rozhraní objektu pro vytváření dat je v současné době podporováno pouze v těchto webových prohlížečů.
2. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
3. Vyberte **další služby**. Filtrovat a vyberte **virtuální sítě**.
4. Filtrovat a v seznamu vyberte virtuální síť. 
5. Na **virtuální síť** vyberte **vlastnosti**. 
6. Vyberte tlačítko Kopírovat, pokud pro **ID prostředku** ID prostředku pro virtuální síť zkopírovat do schránky. Uložte ID ze schránky v OneNote nebo souboru.
7. Vyberte **podsítě** v levé nabídce. Ujistěte se, že počet **dostupné adresy** je větší než uzly ve vaší runtime integrace Azure SSIS.
8. Ověřte, že zprostředkovatele Azure Batch je zaregistrován v rámci předplatného Azure, který má ve virtuální síti. Nebo zaregistrujte zprostředkovatele Azure Batch. Pokud již máte účet Azure Batch v rámci vašeho předplatného, je zaregistrován předplatného pro Azure Batch. (Pokud vytvoříte Azure SSIS Reakcí na portálu služby Data Factory, zprostředkovatele Azure Batch se automaticky registruje za vás.)

   a. Na portálu Azure, vyberte **odběry** v levé nabídce.

   b. Vyberte své předplatné. 
   
   c. Vyberte **zprostředkovatelé prostředků** na levé straně a ověřte, že **Microsoft.Batch** je registrovaný poskytovatel.     
      ![Potvrzení stavu "Registrovaná"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Pokud nevidíte **Microsoft.Batch** v seznamu, a zaregistrujte ho, [vytvořit prázdný účet Azure Batch](../batch/batch-account-create-portal.md) ve vašem předplatném. Chcete-li odstranit později.

### <a name="use-the-portal-to-configure-a-classic-virtual-network"></a>Použití portálu ke konfiguraci klasickou virtuální síť
Musíte nakonfigurovat virtuální síť, než bude možné připojit Azure SSIS Reakcí na ni.

1. Spusťte Microsoft Edge nebo Google Chrome. Rozhraní objektu pro vytváření dat je v současné době podporována pouze pro tyto webové prohlížeče.
2. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
3. Vyberte **další služby**. Filtrovat a vyberte **virtuální sítě (klasické)**.
4. Filtrovat a v seznamu vyberte virtuální síť. 
5. Na **virtuální sítě (klasické)** vyberte **vlastnosti**. 

    ![ID prostředku klasickou virtuální síť](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)
5. Vyberte tlačítko Kopírovat, pokud pro **ID prostředku** ID prostředku pro klasické síťové zkopírovat do schránky. Uložte ID ze schránky v OneNote nebo souboru.
6. Vyberte **podsítě** v levé nabídce. Ujistěte se, že počet **dostupné adresy** je větší než uzly ve vaší runtime integrace Azure SSIS.

    ![Počet dostupných adres ve virtuální síti](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)
7. Připojení k **MicrosoftAzureBatch** k **Classic Přispěvatel virtuálních počítačů** role pro virtuální síť.

    a. Vyberte **přístup k ovládacímu prvku (IAM)** v levé nabídce a vyberte **přidat** na panelu nástrojů. 
       !["Řízení přístupu" a "Přidat" tlačítka](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

    b. Na **přidat oprávnění** vyberte **Classic Přispěvatel virtuálních počítačů** pro **Role**. Vložení **ddbf3205-c6bd-46ae-8127-60eb93363864** v **vyberte** a pak vyberte **Microsoft Azure Batch** ze seznamu výsledků hledání.   
       ![Výsledky hledání na stránce "Přidat oprávnění"](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

    c. Vyberte **Uložit** uložte nastavení a zavřete stránku.  
       ![Uložit nastavení přístupu](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

    d. Zkontrolujte, jestli **Microsoft Azure Batch** v seznamu přispěvatele.  
       ![Potvrďte volbu přístup Azure Batch](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

5. Ověřte, že zprostředkovatele Azure Batch je zaregistrován v rámci předplatného Azure, který má ve virtuální síti. Nebo zaregistrujte zprostředkovatele Azure Batch. Pokud již máte účet Azure Batch v rámci vašeho předplatného, je zaregistrován předplatného pro Azure Batch. (Pokud vytvoříte Azure SSIS Reakcí na portálu služby Data Factory, zprostředkovatele Azure Batch se automaticky registruje za vás.)

   a. Na portálu Azure, vyberte **odběry** v levé nabídce.

   b. Vyberte své předplatné.

   c. Vyberte **zprostředkovatelé prostředků** na levé straně a ověřte, že **Microsoft.Batch** je registrovaný poskytovatel.     
      ![Potvrzení stavu "Registrovaná"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Pokud nevidíte **Microsoft.Batch** v seznamu, a zaregistrujte ho, [vytvořit prázdný účet Azure Batch](../batch/batch-account-create-portal.md) ve vašem předplatném. Chcete-li odstranit později. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Připojení k virtuální síti Azure SSIS IR

1. Spusťte Microsoft Edge nebo Google Chrome. Rozhraní objektu pro vytváření dat je v současné době podporováno pouze v těchto webových prohlížečů.
2. V [portál Azure](https://portal.azure.com), vyberte **datové továrny** v levé nabídce. Pokud nevidíte **datové továrny** v nabídce vyberte **další služby**a vyberte položku **datové továrny** v **INTELLIGENCE + analýzy**části. 
    
   ![Seznam objektů pro vytváření dat](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)
2. V seznamu vyberete datovou továrnu s modulem runtime integrace Azure SSIS. Zobrazí domovská stránka objektu pro vytváření dat. Vyberte **vytvořit a nasadit** dlaždici. Rozhraní objektu pro vytváření dat zobrazí na samostatné kartě. 

   ![Domovská stránka objektu pro vytváření dat](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)
3. V uživatelském rozhraní objektu pro vytváření dat přepnout **upravit** vyberte **připojení**a přepněte do **integrační moduly runtime** karta. 

   ![Karta "Integrační moduly runtime"](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)
4. Pokud vaše Azure SSIS IR běží, v seznamu integrace modulu runtime, vyberte **Zastavit** v tlačítko **akce** sloupec pro vaši Azure SSIS infračerveného signálu. IR nelze upravit, dokud jej nezastavíte. 

   ![Zastavit reakcí na Incidenty](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)
1. Vyberte v seznamu runtime integrace **upravit** v tlačítko **akce** sloupec pro vaši Azure SSIS infračerveného signálu.

   ![Upravit integrace modulu runtime](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)
5. Na **obecné nastavení** stránky **nastavení integrace modulu Runtime** vyberte **Další**. 

   ![Obecná nastavení pro instalaci reakcí na Incidenty](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-general-settings.png)
6. Na **nastavení SQL** , zadejte heslo správce a vyberte **Další**.

   ![Nastavení systému SQL Server pro instalace reakcí na Incidenty](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-sql-settings.png)
7. Na **Upřesnit nastavení** proveďte následující akce: 

   a. Zaškrtněte políčko pro **vyberte virtuální síť pro vaše Runtime integrace Azure SSIS připojit a povolit službám Azure ke konfiguraci oprávnění nebo nastavení sítě VNet**.

   b. Pro **typu**, určete, zda je virtuální sítě klasickou virtuální síť nebo k virtuální síti Azure Resource Manager. 

   c. Pro **název virtuální sítě**, vyberte virtuální síť.

   d. Pro **název podsítě**, vyberte podsíť ve virtuální síti.

   e. Vyberte **aktualizace**. 

   ![Rozšířená nastavení pro instalaci reakcí na Incidenty](media/join-azure-ssis-integration-runtime-virtual-network/ir-setup-advanced-settings.png)
8. Teď můžete spustit IR pomocí **spustit** tlačítka na **akce** sloupec pro vaši Azure SSIS infračerveného signálu. Jak dlouho trvá přibližně 20 minut před spuštěním Azure SSIS infračerveného signálu. 


## <a name="azure-powershell"></a>Azure PowerShell

### <a name="configure-a-virtual-network"></a>Konfigurace virtuální sítě
Musíte nakonfigurovat virtuální síť, než bude možné připojit Azure SSIS Reakcí na ni. Chcete-li automaticky nakonfigurovat oprávnění nebo nastavení virtuální sítě pro vaše runtime integrace Azure SSIS připojovat k virtuální síti, přidejte následující skript:

```powershell
# Register to the Azure Batch resource provider
# Make sure to run this script against the subscription to which the VNet belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
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

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Vytvoření služby Azure SSIS IR a připojení k virtuální síti.
Můžete vytvořit Azure SSIS reakcí na Incidenty a připojení k virtuální síti současně. Dokončení skriptu a pokyny najdete v tématu [vytvoření modulu runtime integrace Azure SSIS](create-azure-ssis-integration-runtime.md#azure-powershell).

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Připojení existující IR Azure SSIS k virtuální síti.
Skript u [vytvoření modulu runtime integrace Azure SSIS](create-azure-ssis-integration-runtime.md) článek ukazuje, jak vytvořit Azure SSIS reakcí na Incidenty a připojení k virtuální síti ve stejném skriptu. Pokud máte existující IR Azure SSIS, proveďte následující kroky pro připojení k virtuální síti: 

1. Zastavit infračerveného signálu Azure SSIS.
2. Konfigurace Azure SSIS IR připojovat k virtuální síti. 
3. Spustit infračerveného signálu Azure SSIS. 

### <a name="define-the-variables"></a>Definování proměnné

```powershell
$ResourceGroupName = "<Azure resource group name>"
$DataFactoryName = "<Data factory name>" 
$AzureSSISName = "<Specify Azure-SSIS IR name>"
## These two parameters apply if you are using a virtual network and Azure SQL Database Managed Instance (Preview) 
# Specify information about your classic or Azure Resource Manager virtual network.
$VnetId = "<Name of your Azure virtual network>"
$SubnetName = "<Name of the subnet in the virtual network>"
```

### <a name="stop-the-azure-ssis-ir"></a>Zastavit IR Azure SSIS
Modul runtime integrace Azure SSIS zastavte, než bude možné připojit k virtuální síti. Tento příkaz uvolní všechny jeho uzly a zastaví fakturace:

```powershell
Stop-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force 
```
### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Nakonfigurujte nastavení virtuální sítě pro Azure SSIS IR pro připojení

```powershell
# Register to the Azure Batch resource provider
# Make sure to run this script against the subscription to which the VNet belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName "MicrosoftAzureBatch").Id
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

### <a name="configure-the-azure-ssis-ir"></a>Konfigurace Azure SSIS IR
Pokud chcete konfigurovat modul runtime integrace Azure SSIS připojovat k virtuální síti, spusťte `Set-AzureRmDataFactoryV2IntegrationRuntime` příkaz: 

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -VnetId $VnetId `
                                            -Subnet $SubnetName
```

### <a name="start-the-azure-ssis-ir"></a>Spustit IR Azure SSIS
Pokud chcete spustit modul runtime integrace Azure SSIS, spusťte následující příkaz: 

```powershell
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

```
Tento příkaz má 20 až 30 minut na dokončení.

## <a name="next-steps"></a>Další postup
Další informace o běhu Azure SSIS najdete v následujících tématech: 

- [Modul runtime integrace Azure SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). Tento článek obsahuje koncepční informace o integraci runtimes obecně platí, včetně infračerveného signálu Azure SSIS. 
- [Kurz: Nasazení balíčků SSIS do Azure](tutorial-create-azure-ssis-runtime-portal.md) Tento článek obsahuje podrobné pokyny k vytvoření služby Azure SSIS infračerveného signálu. Azure SQL database používá k hostování katalogu služby SSIS. 
- [Vytvoření prostředí Azure-SSIS Integration Runtime](create-azure-ssis-integration-runtime.md). Tento článek rozšíří na kurz a poskytuje pokyny o používání Azure SQL Database spravované Instance (Preview) a připojení k virtuální síti reakcí na Incidenty. 
- [Monitorování Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Tento článek ukazuje, jak načíst informace o Azure-SSIS IR a popisy stavů ve vrácených informacích. 
- [Správa Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). Tento článek ukazuje, jak zastavit, spustit nebo odebrat Azure-SSIS IR. Je také ukazuje, jak chcete škálovat vaše IR Azure SSIS tak, že přidáte uzly. 
