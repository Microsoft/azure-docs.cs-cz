---
title: Strategie přístupu k datům
description: Azure Data Factory teď podporuje rozsahy statických IP adres.
services: data-factory
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/28/2020
ms.openlocfilehash: a4d8d7eaed40b876adecb82f339be4a4c434325f
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91616852"
---
# <a name="data-access-strategies"></a>Strategie přístupu k datům

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Zásadním cílem zabezpečení organizace je chránit svá úložiště dat před náhodným přístupem přes Internet, může se jednat o místní nebo cloudové nebo SaaS úložiště dat. 

Cloudové úložiště dat obvykle řídí přístup pomocí následujících mechanismů:
* Privátní odkaz z Virtual Network na zdroje dat s povoleným soukromým koncovým bodem
* Pravidla brány firewall, která omezují připojení podle IP adresy
* Mechanismus ověřování, který vyžaduje, aby uživatelé prokáže svoji identitu
* Autorizační mechanismy, které omezují uživatelům konkrétní akce a data

> [!TIP]
> Díky [zavedení rozsahu statických IP adres](https://docs.microsoft.com/azure/data-factory/azure-integration-runtime-ip-addresses)teď můžete u konkrétní oblasti Azure Integration runtime povolit seznam ROZSAHů IP adres, abyste se ujistili, že není nutné povolit všechny IP adresy Azure v cloudových úložištích dat. Tímto způsobem můžete omezit IP adresy, které mají povolený přístup k úložištím dat.

> [!NOTE] 
> Rozsahy IP adres jsou blokované pro Azure Integration Runtime a aktuálně se používají jenom pro přesun dat, kanály a externí aktivity. Tok dat a Azure Integration Runtime, které umožňují spravovaným Virtual Network nyní tyto rozsahy IP adres nepoužívají. 

To by mělo fungovat v mnoha scénářích a máme na to, že je žádoucí jedinečnou statickou IP adresu na prostředí Integration runtime, ale to by nebylo možné pomocí Azure Integration Runtime aktuálně, což je bez serveru. V případě potřeby můžete kdykoli nastavit Integration Runtime v místním prostředí a použít k němu statickou IP adresu. 

## <a name="data-access-strategies-through-azure-data-factory"></a>Strategie přístupu k datům prostřednictvím Azure Data Factory

* **[Soukromý odkaz](https://docs.microsoft.com/azure/private-link/private-link-overview)** – můžete vytvořit Azure Integration runtime v rámci Azure Data Factory spravované Virtual Network a použije se privátní koncové body pro zabezpečené připojení k podporovaným úložištím dat. Provoz mezi spravovanými Virtual Network a zdroji dat migruje páteřní síť společnosti Microsoft a nezveřejňuje veřejnou síť.
* **[Trusted Service](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)** -Azure Storage (Blob, adls Gen2) podporuje konfiguraci brány firewall, která umožňuje pro zabezpečený přístup k účtu úložiště vybrat důvěryhodné služby platformy Azure. Trusted Services vynutily ověřování spravované identity, které zajišťuje, aby se žádná jiná továrna dat nemohla připojit k tomuto úložišti, pokud to nepovažuje za použití spravované identity. Další podrobnosti najdete v **[tomto blogu](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993)**. To je proto extrémně zabezpečené a doporučuje se. 
* **Jedinečná statická IP adresa** – budete muset nastavit prostředí Integration runtime v místním prostředí, abyste získali statickou IP adresu pro Data Factory konektory. Tento mechanismus zajišťuje, že můžete blokovat přístup ze všech ostatních IP adres. 
* **[Rozsah statických IP](https://docs.microsoft.com/azure/data-factory/azure-integration-runtime-ip-addresses)** adres – můžete použít IP adresy Azure Integration runtime, abyste je mohli zobrazit v úložišti (například S3, Salesforce atd.). V takovém případě omezuje IP adresy, které se můžou připojit k úložištím dat, ale také spoléhá na pravidla ověřování a autorizace.
* **[Tag služby](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)** – značka služby představuje skupinu předpon IP adres z dané služby Azure (například Azure Data Factory). Společnost Microsoft spravuje předpony adres, které jsou zahrnuté ve značce služby, a automaticky aktualizuje značku služby, protože se mění adresy. tím se minimalizuje složitost častých aktualizací pravidel zabezpečení sítě. To je užitečné, když filtrujete přístup k datům v úložištích hostovaných IaaS dat v Virtual Network.
* **Povolení služeb Azure** – některé služby umožňují, aby se k němu připojovaly všechny služby Azure v případě, že zvolíte tuto možnost. 

Další informace o podporovaných mechanismech zabezpečení sítě v úložištích dat v Azure Integration Runtime a v místním prostředí Integration Runtime najdete níže v části dvě tabulky.  
* **Azure Integration Runtime**

    | Úložiště dat                  | Podporované mechanismy zabezpečení sítě v úložištích dat | Private Link     | Důvěryhodná služba     | Rozsah statických IP adres | Značky služeb | Povolení služeb Azure |
    |------------------------------|-------------------------------------------------------------|---------------------|-----------------|--------------|----------------------|-----------------|
    | Úložiště dat Azure PaaS       | Azure Cosmos DB                                     | Ano              | -                   | Ano             | -            | Ano                  |
    |                              | Průzkumník dat Azure                                 | -                | -                   | Ano*            | Ano*         | -                    |
    |                              | Azure Data Lake Gen1                                | -                | -                   | Ano             | -            | Ano                  |
    |                              | Azure Database for MariaDB, MySQL, PostgreSQL       | -                | -                   | Ano             | -            | Ano                  |
    |                              | Azure File Storage                                  | Ano              | -                   | Ano             | -            | .                    |
    |                              | Azure Storage (objekt blob, ADLS Gen2)                     | Ano              | Ano (pouze ověřování MSI) | Ano             | -            | .                    |
    |                              | Azure SQL DB, Azure synapse Analytics), SQL ml  | Ano (jenom Azure SQL DB/DW)        | -                   | Ano             | -            | Ano                  |
    |                              | Azure Key Vault (pro načtení tajných klíčů/připojovacího řetězce) | ano      | Ano                 | Ano             | -            | -                    |
    | Jiná úložiště dat PaaS/SaaS | AWS S3, SalesForce, Google Cloud Storage atd.    | -                | -                   | Ano             | -            | -                    |
    | LaaS Azure                   | SQL Server, Oracle atd.                          | -                | -                   | Ano             | Ano          | -                    |
    | Místní laaS              | SQL Server, Oracle atd.                          | -                | -                   | Ano             | -            | -                    |
    
    **Dá se použít jenom v případě, že je virtuální síť ve službě Azure Průzkumník dat vložená a rozsah IP adres se dá použít na NSG/bráně firewall.* 

* **Integration Runtime v místním prostředí (ve virtuální síti/místním prostředí)**
    
    | Úložiště dat                  | Podporované mechanismy zabezpečení sítě v úložištích dat         | Statická IP adresa | Důvěryhodné služby  |
    |--------------------------------|---------------------------------------------------------------|-----------|---------------------|
    | Úložiště dat Azure PaaS       | Azure Cosmos DB                                               | Ano       | -                   |
    |                                | Průzkumník dat Azure                                           | -         | -                   |
    |                                | Azure Data Lake Gen1                                          | Ano       | -                   |
    |                                | Azure Database for MariaDB, MySQL, PostgreSQL               | Ano       | -                   |
    |                                | Azure File Storage                                            | Ano       | -                   |
    |                                | Azure Storage (blog, ADLS Gen2)                             | Ano       | Ano (pouze ověřování MSI) |
    |                                | Azure SQL DB, Azure synapse Analytics), SQL ml          | Ano       | -                   |
    |                                | Azure Key Vault (pro načtení tajných klíčů/připojovacího řetězce) | Ano       | Ano                 |
    | Jiná úložiště dat PaaS/SaaS | AWS S3, SalesForce, Google Cloud Storage atd.              | Ano       | -                   |
    | LaaS Azure                     | SQL Server, Oracle atd.                                  | Ano       | -                   |
    | Místní laaS              | SQL Server, Oracle atd.                                  | Ano       | -                   |    

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících souvisejících článcích:
* [Podporované zdroje dat](https://docs.microsoft.com/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)
* [Azure Key Vault důvěryhodné služby](https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints#trusted-services)
* [Azure Storage důvěryhodné služby Microsoftu](https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services)
* [Spravovaná identita pro Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-service-identity)
