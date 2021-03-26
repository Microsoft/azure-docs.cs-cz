---
title: Použití spravované instance Azure SQL s Azure-služba SSIS (SQL Server Integration Services) (SSIS) v Azure Data Factory
description: Naučte se používat spravovanou instanci Azure SQL s služba SSIS (SQL Server Integration Services) (SSIS) v Azure Data Factory.
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.topic: conceptual
ms.date: 4/15/2020
ms.openlocfilehash: 190def0e6e2f77d330d2307753dc9e9d53c55dd7
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105564135"
---
# <a name="use-azure-sql-managed-instance-with-sql-server-integration-services-ssis-in-azure-data-factory"></a>Použití spravované instance Azure SQL s služba SSIS (SQL Server Integration Services) (SSIS) v Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Nyní můžete přesunout projekty služba SSIS (SQL Server Integration Services) (SSIS), balíčky a úlohy do cloudu Azure. Nasaďte, spouštějte a spravujte projekty a balíčky SSIS na Azure SQL Database nebo spravované instanci SQL pomocí známých nástrojů, jako je například SQL Server Management Studio (SSMS). Tento článek popisuje následující konkrétní oblasti při použití spravované instance Azure SQL s Azure-SSIS Integration runtime (IR):

- [Zřízení Azure-SSIS IR pomocí katalogu SSIS (SSISDB) hostovaného službou Azure SQL Managed instance](#provision-azure-ssis-ir-with-ssisdb-hosted-by-azure-sql-managed-instance)
- [Spouštění balíčků SSIS pomocí úlohy agenta Managed instance Azure SQL](how-to-invoke-ssis-package-managed-instance-agent.md)
- [Vyčistit protokoly SSISDB pomocí úlohy agenta spravované instance Azure SQL](#clean-up-ssisdb-logs)
- [Azure-SSIS IR převzetí služeb při selhání pomocí spravované instance Azure SQL](configure-bcdr-azure-ssis-integration-runtime.md)
- [Migrace místních úloh SSIS do SSIS v ADF pomocí spravované instance Azure SQL jako cíle úloh databáze](scenario-ssis-migration-overview.md#azure-sql-managed-instance-as-database-workload-destination)

## <a name="provision-azure-ssis-ir-with-ssisdb-hosted-by-azure-sql-managed-instance"></a>Zřízení Azure-SSIS IR s SSISDB hostovaným pomocí spravované instance Azure SQL

### <a name="prerequisites"></a>Požadavky

1. Při volbě Azure Active Directory ověřování [povolit Azure Active Directory (Azure AD) na spravované instanci SQL Azure](enable-aad-authentication-azure-ssis-ir.md#configure-azure-ad-authentication-for-azure-sql-managed-instance)

1. Vyberte, jak se má připojit spravovaná instance SQL, přes soukromý koncový bod nebo přes Veřejný koncový bod:

    - Přes soukromý koncový bod (upřednostňovaný)

        1. Vyberte virtuální síť, ke které se má Azure-SSIS IR připojit:
            - Uvnitř stejné virtuální sítě jako spravovaná instance s **jinou podsítí**.
            - Uvnitř jiné virtuální sítě, než je spravovaná instance, prostřednictvím partnerského vztahu virtuální sítě (která je omezená na stejnou oblast z důvodu omezení globálních partnerských vztahů) nebo připojení virtuální sítě k virtuální síti.

            Další informace o připojení ke spravovaným instancím SQL najdete v tématu [připojení aplikace ke spravované instanci Azure SQL](../azure-sql/managed-instance/connect-application-instance.md).

        1. [Nakonfigurujte virtuální síť](#configure-virtual-network).

    - Přes Veřejný koncový bod

        Spravované instance Azure SQL můžou poskytovat připojení přes [veřejné koncové body](../azure-sql/managed-instance/public-endpoint-configure.md). Aby bylo možné povolit přenosy mezi spravovanou instancí SQL a Azure-SSIS IR, musí splňovat požadavky na příchozí a odchozí spojení:

        - Když Azure-SSIS IR není uvnitř virtuální sítě (upřednostňovaná)

            **Příchozí požadavek spravované instance SQL**, aby povoloval příchozí provoz z Azure-SSIS IR.

            | Transportní protokol | Zdroj | Rozsah zdrojových portů | Cíl | Rozsah cílových portů |
            |---|---|---|---|---|
            |TCP|Značka cloudové služby Azure|*|VirtualNetwork|3342|

            Další informace najdete v tématu [povolení provozu veřejného koncového bodu ve skupině zabezpečení sítě](../azure-sql/managed-instance/public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group).

        - při Azure-SSIS IR v rámci virtuální sítě

            Pokud je spravovaná instance SQL v oblasti, kterou Azure-SSIS IR nepodporuje, je Azure-SSIS IR uvnitř virtuální sítě bez partnerského vztahu virtuálních sítí z důvodu omezení globálního partnerského vztahu virtuální sítě. V tomto scénáři **Azure-SSIS IR v rámci virtuální sítě** připojí SPRAVOVANOU instanci SQL **přes Veřejný koncový bod**. Pomocí pravidel pro skupinu zabezpečení sítě (NSG) povolte provoz mezi spravovanou instancí SQL a Azure-SSIS IR:

            1. **Příchozí požadavek spravované instance SQL**, aby povoloval příchozí provoz z Azure-SSIS IR.

                | Transportní protokol | Zdroj | Rozsah zdrojových portů | Cíl |Rozsah cílových portů |
                |---|---|---|---|---|
                |TCP|Statická IP adresa Azure-SSIS IR <br> Podrobnosti najdete v tématu věnovaném využití [vlastní veřejné IP adresy pro Azure-SSIS IR](join-azure-ssis-integration-runtime-virtual-network.md#publicIP).|*|VirtualNetwork|3342|

             1. **Odchozí požadavek Azure-SSIS IR**, který umožňuje odchozí přenosy do spravované instance SQL

                | Transportní protokol | Zdroj | Rozsah zdrojových portů | Cíl |Rozsah cílových portů |
                |---|---|---|---|---|
                |TCP|VirtualNetwork|*|[IP adresa veřejného koncového bodu spravované instance SQL](../azure-sql/managed-instance/management-endpoint-find-ip-address.md)|3342|

### <a name="configure-virtual-network"></a>Konfigurace virtuální sítě

1. **Oprávnění uživatele**. Uživatel, který vytváří Azure-SSIS IR, musí mít [přiřazení role](../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-for-a-user-at-a-scope) aspoň u prostředku Azure Data Factory s jednou z následujících možností:

    - Použijte integrovanou roli Přispěvatel sítě. Tato role se dodává s oprávněním _Microsoft. \* Network/_ , které má mnohem větší rozsah, než je nutné.
    - Vytvořte vlastní roli, která bude obsahovat jenom potřebná oprávnění _Microsoft. Network/virtualNetworks/ \* /Join/Action_ . Pokud chcete pro Azure-SSIS IR při připojení k virtuální síti Azure Resource Manager použít i vlastní veřejné IP adresy, v roli taky uveďte oprávnění _Microsoft. Network/publicIPAddresses/*/JOIN/Action_ .

1. **Virtuální síť:**

    1. Ujistěte se, že skupina prostředků virtuální sítě může vytvářet a odstraňovat určité síťové prostředky Azure.

        Azure-SSIS IR musí vytvořit určité síťové prostředky ve stejné skupině prostředků jako virtuální síť. Mezi tyto prostředky patří:
        - Nástroj pro vyrovnávání zatížení Azure s názvem *\<Guid> -azurebatch-cloudserviceloadbalancer*
        - Skupina zabezpečení sítě s názvem * \<Guid> -azurebatch-cloudservicenetworksecuritygroup
        - Veřejná IP adresa Azure s názvem-azurebatch-cloudservicepublicip

        Tyto prostředky budou vytvořeny při spuštění Azure-SSIS IR. Po zastavení Azure-SSIS IR se odstraní. Chcete-li zabránit zablokování Azure-SSIS IR zastavovat, nepoužívejte tyto síťové prostředky v jiných prostředcích.

    1. Ujistěte se, že nemáte [Zámek prostředků](../azure-resource-manager/management/lock-resources.md) u skupiny prostředků nebo předplatného, ke kterému virtuální síť patří. Pokud nakonfigurujete zámek jen pro čtení nebo odstranění, začnete Azure-SSIS IR a zastavuje se, nebo přestane reagovat.

    1. Ujistěte se, že nemáte zásadu Azure, která znemožňuje vytvoření následujících prostředků v rámci skupiny prostředků nebo předplatného, ke kterému patří virtuální síť:
        - Microsoft. Network/LoadBalancers
        - Microsoft. Network/NetworkSecurityGroups

    1. Povolí provoz na základě pravidla skupiny zabezpečení sítě (NSG), aby bylo možné provoz mezi spravovanou instancí SQL a Azure-SSIS IR a provozem, který vyžaduje Azure-SSIS IR.
        1. **Příchozí požadavek spravované instance SQL**, aby povoloval příchozí provoz z Azure-SSIS IR.

            | Transportní protokol | Zdroj | Rozsah zdrojových portů | Cíl | Rozsah cílových portů | Komentáře |
            |---|---|---|---|---|---|
            |TCP|VirtualNetwork|*|VirtualNetwork|1433, 11000-11999|Pokud je vaše zásada připojení SQL Database serveru nastavená na **proxy** místo **přesměrování**, je potřeba jenom port 1433.|

        1. **Odchozí požadavek Azure-SSIS IR**, který umožňuje odchozí přenosy do spravované instance SQL a další provoz, který vyžaduje Azure-SSIS IR

        | Transportní protokol | Zdroj | Rozsah zdrojových portů | Cíl | Rozsah cílových portů | Komentáře |
        |---|---|---|---|---|---|
        | TCP | VirtualNetwork | * | VirtualNetwork | 1433, 11000-11999 |Povolí odchozí provoz do spravované instance SQL. Pokud je zásada připojení nastavená na **proxy** místo **přesměrování**, je potřeba jenom port 1433. |
        | TCP | VirtualNetwork | * | AzureCloud | 443 | Uzly vašeho Azure-SSIS IR ve virtuální síti používají tento port pro přístup ke službám Azure, jako je například Azure Storage a Azure Event Hubs. |
        | TCP | VirtualNetwork | * | Internet | 80 | Volitelné Uzly vašeho Azure-SSIS IR ve virtuální síti používají tento port ke stažení seznamu odvolaných certifikátů z Internetu. Pokud zablokujete tento provoz, může dojít ke snížení výkonu při spuštění prostředí IR a ke ztrátě možností použití certifikátu pro kontrolu seznamu odvolaných certifikátů. Pokud chcete dále zúžit cíl na určité plně kvalifikované názvy domény, přečtěte si téma [použití Azure ExpressRoute nebo trasy definované uživatelem (udr)](./join-azure-ssis-integration-runtime-virtual-network.md#route).|
        | TCP | VirtualNetwork | * | Storage | 445 | Volitelné Toto pravidlo se vyžaduje jenom v případě, že chcete spustit balíček SSIS uložený ve službě soubory Azure. |
        |||||||

        1. **Příchozí požadavek Azure-SSIS IR**, který umožní provoz potřebný Azure-SSIS IR.

        | Transportní protokol | Zdroj | Rozsah zdrojových portů | Cíl | Rozsah cílových portů | Komentáře |
        |---|---|---|---|---|---|
        | TCP | BatchNodeManagement | * | VirtualNetwork | 29876, 29877 (Pokud se připojíte k IR k virtuální síti Správce prostředků) <br/><br/>10100, 20100, 30100 (Pokud se připojíte k IR k klasické virtuální síti)| Služba Data Factory tyto porty používá ke komunikaci s uzly vašeho Azure-SSIS IR ve virtuální síti. <br/><br/> Bez ohledu na to, jestli vytvoříte NSG na úrovni podsítě, Data Factory vždycky nakonfiguruje NSG na úrovni síťových rozhraní (nic) připojených k virtuálním počítačům, které hostují Azure-SSIS IR. Pouze příchozí provoz z Data Factory IP adres na zadaných portech je povolený pro NSG na úrovni síťových adaptérů. I když tyto porty otevřete pro internetový provoz na úrovni podsítě, provoz z IP adres, které nejsou Data Factory IP adresy, se zablokuje na úrovni síťové karty. |
        | TCP | CorpNetSaw | * | VirtualNetwork | 3389 | Volitelné Toto pravidlo se vyžaduje jenom v případě, že se zákazník požádá o otevření pro pokročilé řešení problémů. po vyřešení problému ho můžete hned zavřít. Značka služby **CorpNetSaw** povoluje použití vzdálené plochy jenom pro pracovní stanice s zabezpečeným přístupem v podnikové síti Microsoftu. A tento tag služby nejde vybrat z portálu a je dostupný jenom přes Azure PowerShell nebo Azure CLI. <br/><br/> Na úrovni síťové karty je ve výchozím nastavení otevřený port 3389 a my vám umožní řídit port 3389 na úrovni podsítě NSG, mezitím Azure-SSIS IR ve výchozím nastavení v pravidle brány Windows Firewall u každého uzlu infračerveného přenosu nepovolený port 3389 odchozí. |
        |||||||

    1. Další informace najdete v tématu [Konfigurace virtuální sítě](join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration) .
        - Pokud přenesete vlastní veřejné IP adresy pro Azure-SSIS IR
        - Pokud používáte vlastní server DNS (Domain Name System)
        - Pokud používáte Azure ExpressRoute nebo trasu definovanou uživatelem (UDR)
        - Pokud používáte vlastní Azure-SSIS IR

### <a name="provision-azure-ssis-integration-runtime"></a>Zřídit Azure-SSIS Integration Runtime

1. Vyberte privátní koncový bod spravované instance SQL nebo veřejný koncový bod.

    Při [zřizování Azure-SSIS IR](create-azure-ssis-integration-runtime.md#provision-an-azure-ssis-integration-runtime) v aplikaci Azure Portal/ADF, na stránce nastavení SQL použijte **privátní koncový bod** spravované instance SQL nebo **veřejný koncový bod** při vytváření katalogu SSIS (SSISDB).

    Název hostitele veřejného koncového bodu se nachází ve formátu <mi_name>. Public. <dns_zone>. database.windows.net a port používaný pro připojení je 3342.  

    ![Snímek obrazovky ukazuje instalační program prostředí Integration runtime s vybraným katalogem pro vytváření s s/s a serverem katalogu databáze.](./media/how-to-use-sql-managed-instance-with-ir/catalog-public-endpoint.png)

1. V případě použití vyberte ověřování Azure AD.

    ![Katalog – veřejný koncový bod](./media/how-to-use-sql-managed-instance-with-ir/catalog-aad.png)

    Další informace o tom, jak povolit ověřování Azure AD, najdete v tématu [Povolení Azure AD na spravované instanci SQL Azure](enable-aad-authentication-azure-ssis-ir.md#configure-azure-ad-authentication-for-azure-sql-managed-instance).

1. Když se použije, připojte se k virtuální síti Azure-SSIS IR.

    Na stránce Upřesnit nastavení vyberte Virtual Network a podsíť, ke které se chcete připojit.
    
    Když je ve stejné virtuální síti jako SQL Managed instance, vyberte **jinou podsíť** , než je spravovaná instance SQL. 

    Další informace o tom, jak připojit Azure-SSIS IR k virtuální síti, najdete v tématu [připojení prostředí Azure-SSIS Integration runtime k virtuální síti](join-azure-ssis-integration-runtime-virtual-network.md).

    ![Snímek obrazovky ukazuje upřesňující nastavení nastavení modulu runtime integrace, kde můžete vybrat virtuální síť, ke které se má modul runtime připojit.](./media/how-to-use-sql-managed-instance-with-ir/join-virtual-network.png)

Další informace o tom, jak vytvořit Azure-SSIS IR, najdete [v tématu Vytvoření prostředí Azure-SSIS Integration runtime v Azure Data Factory](create-azure-ssis-integration-runtime.md#provision-an-azure-ssis-integration-runtime).

## <a name="clean-up-ssisdb-logs"></a>Vyčistit protokoly SSISDB

Zásady uchovávání protokolů SSISDB jsou definované pomocí níže uvedených vlastností v [Catalog.catalog_properties](/sql/integration-services/system-views/catalog-catalog-properties-ssisdb-database):

- OPERATION_CLEANUP_ENABLED

    Pokud je hodnota TRUE, z katalogu se odstraní podrobnosti operace a zprávy o provozu starší než RETENTION_WINDOW (dny). Pokud je hodnota FALSE, všechny podrobnosti o operaci a zprávy o operacích budou uloženy v katalogu. Poznámka: úloha SQL Server provede vyčištění operace.

- RETENTION_WINDOW

    Počet dní, po které se v katalogu ukládají podrobnosti operace a zprávy o operacích. Pokud je hodnota-1, okno uchování je nekonečné. Poznámka: Pokud není potřeba žádné vyčištění, nastavte OPERATION_CLEANUP_ENABLED na hodnotu FALSE (NEPRAVDA).

Chcete-li odebrat protokoly SSISDB, které jsou mimo interval uchovávání nastavené správcem, můžete spustit uloženou proceduru `[internal].[cleanup_server_retention_window_exclusive]` . Volitelně můžete naplánovat spuštění úlohy agenta spravované instance SQL a aktivovat uloženou proceduru.

## <a name="next-steps"></a>Další kroky

- [Spouštění balíčků SSIS pomocí úlohy agenta Managed instance Azure SQL](how-to-invoke-ssis-package-managed-instance-agent.md)
- [Nastavení provozní kontinuity a zotavení po havárii (BCDR)](configure-bcdr-azure-ssis-integration-runtime.md)
- [Migrace místních SSIS úloh do SSIS v ADF](scenario-ssis-migration-overview.md)