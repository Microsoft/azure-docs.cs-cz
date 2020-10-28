---
title: Konfigurace výkonu pro Azure-SSIS Integration Runtime
description: Naučte se konfigurovat vlastnosti Azure-SSIS Integration Runtime pro vysoký výkon.
services: data-factory
ms.date: 01/10/2018
ms.topic: conceptual
ms.service: data-factory
ms.workload: data-services
author: swinarko
ms.author: sawinark
ms.reviewer: ''
manager: anandsub
ms.openlocfilehash: f0fcd61230d68d7b26017237e2b7e0465fcb1f07
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92635316"
---
# <a name="configure-the-azure-ssis-integration-runtime-for-high-performance"></a>Konfigurace Azure-SSIS Integration Runtime pro vysoký výkon

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]


Tento článek popisuje, jak nakonfigurovat Azure-SSIS Integration Runtime (IR) pro vysoký výkon. Azure-SSIS IR umožňuje nasadit a spouštět balíčky služba SSIS (SQL Server Integration Services) (SSIS) v Azure. Další informace o Azure-SSIS IR najdete v článku věnovaném [prostředí Integration runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime) . Informace o nasazování a spouštění balíčků SSIS v Azure najdete v tématu [zátěž a shift služba SSIS (SQL Server Integration Services) úloh do cloudu](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

> [!IMPORTANT]
> Tento článek obsahuje výsledky výkonu a pozorování z interního testování, které provádí členové vývojového týmu SSIS. Vaše výsledky se můžou lišit. Proveďte vlastní testování před dokončením nastavení konfigurace, které ovlivňuje náklady i výkon.

## <a name="properties-to-configure"></a>Vlastnosti ke konfiguraci

Následující část konfiguračního skriptu zobrazuje vlastnosti, které můžete konfigurovat při vytváření Azure-SSIS Integration Runtime. Úplný skript a Popis prostředí PowerShell najdete v tématu [nasazení balíčků služba SSIS (SQL Server Integration Services) do Azure](tutorial-deploy-ssis-packages-azure-powershell.md).

```powershell
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your existing SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to max(2 x number of cores, 8) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/SQL Managed Instance/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database with virtual network service endpoints or a different subnet than the one used for your SQL Managed Instance

### SSISDB info
$SSISDBServerEndpoint = "[your server name or managed instance name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for SQL Managed Instance]"
```

## <a name="azuressislocation"></a>AzureSSISLocation
**AzureSSISLocation** je umístění pracovního uzlu modulu runtime integrace. Pracovní uzel udržuje konstantní připojení k databázi katalogu SSIS (SSISDB) v Azure SQL Database. Nastavte **AzureSSISLocation** na stejné umístění jako [logický SQL Server](../azure-sql/database/logical-servers.md) , který je hostitelem SSISDB, což umožňuje prostředí Integration runtime co nejefektivněji fungovat co nejúčinnější.

## <a name="azuressisnodesize"></a>AzureSSISNodeSize
Data Factory, včetně Azure-SSIS IR, podporují následující možnosti:
-   \_A4 \_ v2 úrovně Standard
-   \_A8 \_ v2 úrovně Standard
-   \_D1 \_ v2 úrovně Standard
-   \_D2 \_ v2 úrovně Standard
-   \_D3 \_ v2 úrovně Standard
-   \_D4 \_ v2 úrovně Standard
-   \_D2 \_ V3 úrovně Standard
-   \_D4 \_ V3 úrovně Standard
-   \_D8 \_ V3 úrovně Standard
-   \_D16 \_ V3 úrovně Standard
-   \_D32 \_ V3 úrovně Standard
-   \_D64 \_ V3 úrovně Standard
-   Standard \_ E2 \_ V3
-   \_E4 \_ V3 úrovně Standard
-   \_E8 \_ V3 úrovně Standard
-   \_E16 \_ V3 úrovně Standard
-   \_E32 \_ V3 úrovně Standard
-   \_E64 \_ V3 úrovně Standard

V neoficiálním testování interního týmu SSIS technický tým je řada D vhodná pro spuštění balíčku SSIS než řada A.

-   Poměr výkonu a ceny řady D je vyšší než řada a a poměr výkonu a ceny řady v3 je vyšší než série v2.
-   Propustnost pro řady D je vyšší než série za stejnou cenu a propustnost pro řadu v3 je vyšší než série v2 za stejnou cenu.
-   Uzly řady v2 Azure-SSIS IR nejsou vhodné pro vlastní instalaci, proto prosím použijte místo toho uzly řady v3. Pokud už používáte uzly řady v2, přepněte prosím na použití uzlů řady V3 co nejdříve.
-   Řada E má paměťově optimalizované velikosti virtuálních počítačů, které poskytují vyšší poměr paměti na procesor než jiné počítače. Pokud váš balíček vyžaduje hodně paměti, můžete zvážit výběr možnosti virtuální počítač E-series.

### <a name="configure-for-execution-speed"></a>Konfigurace pro rychlost spuštění
Pokud nemáte mnoho balíčků ke spuštění a chcete, aby se balíčky spouštěly rychle, použijte informace v následujícím grafu pro výběr typu virtuálního počítače vhodného pro váš scénář.

Tato data představují jedno spuštění balíčku v jednom pracovním uzlu. Balíček načte 3 000 000 záznamů se sloupci křestní jméno a příjmení z Azure Blob Storage, vygeneruje sloupec s úplným názvem a zapíše záznamy, které mají úplný název delší než 20 znaků, do Azure Blob Storage.

Osa y je počet balíčků, které dokončily provádění během jedné hodiny. Všimněte si, že se jedná pouze o výsledek testu jednoho balíčku využívajícího paměť. Pokud chcete zjistit propustnost balíčku, doporučujeme provést test sami.

![SSIS Integration Runtime – rychlost spuštění balíčku](media/configure-azure-ssis-integration-runtime-performance/ssisir-execution-speedV2.png)

### <a name="configure-for-overall-throughput"></a>Konfigurace pro celkovou propustnost

Pokud máte spoustu balíčků, které je třeba spustit, a Vy se zajímáte o celkové propustnosti, použijte informace v následujícím grafu k výběru typu virtuálního počítače vhodného pro váš scénář.

Osa y je počet balíčků, které dokončily provádění během jedné hodiny. Všimněte si, že se jedná pouze o výsledek testu jednoho balíčku využívajícího paměť. Pokud chcete zjistit propustnost balíčku, doporučujeme provést test sami.

![SSIS Integration Runtime maximální celkovou propustnost](media/configure-azure-ssis-integration-runtime-performance/ssisir-overall-throughputV2.png)

## <a name="azuressisnodenumber"></a>AzureSSISNodeNumber

**AzureSSISNodeNumber** upravuje škálovatelnost prostředí Integration runtime. Propustnost prostředí Integration runtime je úměrná **AzureSSISNodeNumber** . Nastavte nejprve **AzureSSISNodeNumber** na malou hodnotu, Sledujte propustnost prostředí Integration runtime a pak upravte hodnotu pro váš scénář. Informace o překonfigurování počtu pracovních uzlů najdete v tématu [Správa prostředí Azure-SSIS Integration runtime](manage-azure-ssis-integration-runtime.md).

## <a name="azuressismaxparallelexecutionspernode"></a>AzureSSISMaxParallelExecutionsPerNode

Když už používáte výkonný pracovní uzel ke spouštění balíčků, zvýšení **AzureSSISMaxParallelExecutionsPerNode** může zvýšit celkovou propustnost prostředí Integration runtime. Pokud chcete zvýšit maximální hodnotu, je potřeba použít Azure PowerShell k aktualizaci **AzureSSISMaxParallelExecutionsPerNode** . Odpovídající hodnotu můžete odhadnout na základě nákladů na váš balíček a následujících konfigurací pro pracovní uzly. Další informace najdete v tématu [velikosti virtuálních počítačů pro obecné účely](../virtual-machines/sizes-general.md).

| Velikost             | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Maximální propustnost dočasného úložiště: IOPS / čtení v MB/s / zápis v MB/s | Maximální propustnost datových disků: IOPS | Max. počet síťových karet / Očekávaný výkon sítě (Mb/s) |
|------------------|------|-------------|------------------------|------------------------------------------------------------|-----------------------------------|------------------------------------------------|
| \_D1 \_ v2 úrovně Standard | 1    | 3,5         | 50                     | 3000 / 46 / 23                                             | 2 / 2×500                         | 2 / 750                                        |
| \_D2 \_ v2 úrovně Standard | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 4 / 4×500                         | 2 / 1 500                                       |
| \_D3 \_ v2 úrovně Standard | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 8 / 8×500                         | 4 / 3 000                                       |
| \_D4 \_ v2 úrovně Standard | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 16 / 16×500                       | 8 / 6 000                                       |
| \_A4 \_ v2 úrovně Standard | 4    | 8           | 40                     | 4000 / 80 / 40                                             | 8 / 8×500                         | 4 / 1 000                                       |
| \_A8 \_ v2 úrovně Standard | 8    | 16          | 80                     | 8000 / 160 / 80                                            | 16 / 16×500                       | 8 / 2 000                                       |
| \_D2 \_ V3 úrovně Standard | 2    | 8           | 50                     | 3000 / 46 / 23                                             | 4/6x500                         | 2 / 1 000                                       |
| \_D4 \_ V3 úrovně Standard | 4    | 16          | 100                    | 6000 / 93 / 46                                             | 8/12x500                        | 2 / 2 000                                       |
| \_D8 \_ V3 úrovně Standard | 8    | 32          | 200                    | 12000 / 187 / 93                                           | 16/24x500                       | 4 / 4000                                       |
| \_D16 \_ V3 úrovně Standard| 16   | 64          | 400                    | 24000 / 375 / 187                                          | 32/48x500                        | 8 / 8000                                       |
| \_D32 \_ V3 úrovně Standard| 32   | 128         | 800                    | 48000 / 750 / 375                                          | 32/96x500                       | 8 / 16 000                                      |
| \_D64 \_ V3 úrovně Standard| 64   | 256         | 1600                   | 96000/1000/500                                         | 32/192x500                      | 8 / 30000                                      |
| Standard \_ E2 \_ V3 | 2    | 16          | 50                     | 3000 / 46 / 23                                             | 4/6x500                         | 2 / 1 000                                       |
| \_E4 \_ V3 úrovně Standard | 4    | 32          | 100                    | 6000 / 93 / 46                                             | 8/12x500                        | 2 / 2 000                                       |
| \_E8 \_ V3 úrovně Standard | 8    | 64          | 200                    | 12000 / 187 / 93                                           | 16/24x500                       | 4 / 4000                                       |
| \_E16 \_ V3 úrovně Standard| 16   | 128         | 400                    | 24000 / 375 / 187                                          | 32/48x500                       | 8 / 8000                                       |
| \_E32 \_ V3 úrovně Standard| 32   | 256         | 800                    | 48000 / 750 / 375                                          | 32/96x500                       | 8 / 16 000                                      |
| \_E64 \_ V3 úrovně Standard| 64   | 432         | 1600                   | 96000/1000/500                                         | 32/192x500                      | 8 / 30000                                      |

Tady jsou pokyny pro nastavení správné hodnoty pro vlastnost **AzureSSISMaxParallelExecutionsPerNode** : 

1. Nastavte nejprve na malou hodnotu.
2. Zvyšte ho o malou část, abyste zkontrolovali, jestli se zvýšila celková propustnost.
3. Zastavit zvýšení hodnoty, když celková propustnost dosáhne maximální hodnoty.

## <a name="ssisdbpricingtier"></a>SSISDBPricingTier

**SSISDBPricingTier** je cenová úroveň pro databázi katalogu SSIS (SSISDB) na v Azure SQL Database. Toto nastavení má vliv na maximální počet pracovních procesů v instanci IR, rychlost pro zařazení běhu balíčku do fronty a rychlost načtení protokolu spuštění.

-   Pokud nezáleží na tom, jak zrychlit spuštění balíčku a načíst protokol spuštění, můžete zvolit nejnižší cenovou úroveň databáze. Azure SQL Database se základními cenami podporuje 8 pracovních procesů v instanci prostředí Integration runtime.

-   Pokud je počet pracovních procesů větší než 8 nebo je počet jader větší než 50, vyberte výkonnější databázi, než je základní. V opačném případě se databáze stal kritickým bodem instance prostředí Integration runtime a celkový výkon je negativně ovlivněn.

-   Vyberte výkonnější databázi, jako je například S3, pokud je úroveň protokolování nastavená na Verbose. V souladu s neoficiálním testováním interních testů může cenová úroveň S3 podporovat provádění balíčků SSIS se dvěma uzly, 128 paralelních počtů a podrobné úrovně protokolování.

Cenovou úroveň databáze můžete také upravit na základě informací o využití [jednotek databázové transakce](../azure-sql/database/service-tiers-dtu.md) (DTU) dostupných na Azure Portal.

## <a name="design-for-high-performance"></a>Návrh pro vysoký výkon
Návrh balíčku SSIS, který se má spustit v Azure, se liší od návrhu balíčku pro místní spuštění. Místo kombinace více nezávislých úloh ve stejném balíčku je můžete oddělit do několika balíčků a zefektivnit tak provádění Azure-SSIS IR. Vytvořte spuštění balíčku pro každý balíček, aby nemuseli čekat na jeho dokončení. Tento přístup přináší výhody škálovatelnosti prostředí Azure-SSIS Integration runtime a zlepšuje celkovou propustnost.

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o Azure-SSIS Integration Runtime. Viz [Azure-SSIS Integration runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime).