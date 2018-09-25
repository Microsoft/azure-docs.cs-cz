---
title: Konfigurace výkonu prostředí Azure-SSIS Integration runtime | Dokumentace Microsoftu
description: Další informace o konfiguraci vlastností prostředí Azure-SSIS Integration Runtime pro vysoký výkon
services: data-factory
ms.date: 01/10/2018
ms.topic: conceptual
ms.service: data-factory
ms.workload: data-services
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 2592c81947f48c10891fe920647612d5c30af64f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989071"
---
# <a name="configure-the-azure-ssis-integration-runtime-for-high-performance"></a>Konfigurace prostředí Azure-SSIS Integration Runtime pro vysoký výkon

Tento článek popisuje, jak nakonfigurovat Azure-SSIS Integration Runtime (IR) pro vysoce výkonné. Prostředí Azure-SSIS IR umožňuje nasazení a spouštění balíčků SQL Server Integration Services (SSIS) v Azure. Další informace o Azure-SSIS IR najdete v tématu [prostředí Integration runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime) článku. Informace o nasazování a spouštění balíčků služby SSIS v Azure najdete v tématu [Lift and shift úloh SQL Server Integration Services do cloudu](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

> [!IMPORTANT]
> Tento článek obsahuje výsledky výkonu a připomínek interní testování provádí členové vývojového týmu prostředí SSIS. Vaše výsledky se mohou lišit. Proveďte vlastní testování před další změny nastavení konfigurace, které ovlivňují náklady a výkon.

## <a name="properties-to-configure"></a>Vlastnosti konfigurace

Následující část skript konfigurace jsou uvedeny vlastnosti, které můžete nakonfigurovat při vytváření prostředí Azure-SSIS Integration Runtime. Celý skript prostředí PowerShell a popis najdete v tématu [balíčků nasazení SQL Server Integration Services do Azure](tutorial-deploy-ssis-packages-azure-powershell.md).

```powershell
$SubscriptionName = "<Azure subscription name>"
$ResourceGroupName = "<Azure resource group name>"
# Data factory name. Must be globally unique
$DataFactoryName = "<Data factory name>" 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "<Specify a name for your Azure-SSIS IR>"
$AzureSSISDescription = "<Specify description for your Azure-SSIS IR"
# Only EastUS, NorthEurope, and WestEurope are supported.
$AzureSSISLocation = "EastUS" 
# Only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_D3_v2"
# Only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "<Azure SQL server name>.database.windows.net"
$SSISDBServerAdminUserName = "<Azure SQL server - user name>"
$SSISDBServerAdminPassword = "<Azure SQL server - user password>"
# Remove the SSISDBPricingTier variable if you are using Azure SQL Database Managed Instance
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "<pricing tier of your Azure SQL server. Examples: Basic, S0, S1, S2, S3, etc.>"
```

## <a name="azuressislocation"></a>AzureSSISLocation
**AzureSSISLocation** je umístění pro pracovníka uzel integration runtime. Pracovní uzel udržuje stálé připojení k databázi katalogu služby SSIS (SSISDB) ve službě Azure SQL database. Nastavte **AzureSSISLocation** do stejného umístění jako databáze SQL serveru, který je hostitelem databáze SSISDB, což umožňuje prostředí integration runtime jako efektivní práce.

## <a name="azuressisnodesize"></a>AzureSSISNodeSize
Data Factory, včetně Azure-SSIS IR podporuje následující možnosti:
-   Standardní\_A4\_v2
-   Standardní\_A8\_v2
-   Standardní\_D1\_v2
-   Standardní\_D2\_v2
-   Standardní\_D3\_v2
-   Standardní\_D4\_v2.

V neoficiální interní testování pomocí služby SSIS technický tým, řady D series pravděpodobně vhodnější pro spouštění balíčku služby SSIS než řady A series.

-   Výkon a cenu poměr řady D series je vyšší než řady A series.
-   Propustnost pro řady D series je vyšší než řada A za stejnou cenu.

### <a name="configure-for-execution-speed"></a>Konfigurace pro rychlost provádění
Pokud nemáte mnoho balíčky ke spuštění a chcete, aby balíčky pro rychlé spouštění, použijte informace v následující tabulce vybrat typ virtuálního počítače, který je vhodný pro váš scénář.

Tato data představuje jeden balíček provádění na jeden pracovního uzlu. Balíček načte 10 milionů záznamů s názvem prvního a posledního sloupce z Azure Blob Storage vygeneruje úplný název sloupce a zapíše záznamy, které mají příjmení delší než 20 znaků do Azure Blob Storage.

![Rychlost provádění balíčků služby SSIS Integration Runtime](media/configure-azure-ssis-integration-runtime-performance/ssisir-execution-speed.png)

### <a name="configure-for-overall-throughput"></a>Konfigurace pro celkovou propustnost

Pokud máte velké balíčky ke spuštění a vám nejvíce jde o celkovou propustnost, vyberte typ virtuálního počítače, který je vhodný pro váš scénář pomocí informací v následující tabulce.

![Maximální celkovou propustnost služby SSIS Integration Runtime](media/configure-azure-ssis-integration-runtime-performance/ssisir-overall-throughput.png)

## <a name="azuressisnodenumber"></a>AzureSSISNodeNumber

**AzureSSISNodeNumber** upraví škálovatelnost prostředí integration runtime. Propustnost modulu runtime integrace je přímo úměrný **AzureSSISNodeNumber**. Nastavte **AzureSSISNodeNumber** malou hodnotu při prvním sledovat propustnost prostředí integration runtime a pak upravte hodnotu pro váš scénář. Změna konfigurace počet uzlů pracovního procesu, naleznete v tématu [správě prostředí Azure-SSIS integration runtime](manage-azure-ssis-integration-runtime.md).

## <a name="azuressismaxparallelexecutionspernode"></a>AzureSSISMaxParallelExecutionsPerNode

Pokud už používáte výkonné pracovního uzlu ke spouštění balíčků, zvýšení **AzureSSISMaxParallelExecutionsPerNode** může zvýšit celkovou propustnost prostředí integration runtime. 1 – 4 paralelních úloh na uzlu Standard_D1_v2 uzly, jsou podporovány. Pro všechny ostatní typy uzlů se podporují 1-8 paralelních úloh na uzlu.
Chcete-li odhadnout příslušnou hodnotu na základě nákladů balíčku a následujících konfigurací pracovních uzlů. Další informace najdete v tématu [velikostí virtuálních počítačů pro obecné účely](../virtual-machines/windows/sizes-general.md).

| Velikost             | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Maximální propustnost dočasného úložiště: IOPS / čtení v MB/s / zápis v MB/s | Maximální propustnost datových disků: IOPS | Max. počet síťových karet / Očekávaný výkon sítě (Mb/s) |
|------------------|------|-------------|------------------------|------------------------------------------------------------|-----------------------------------|------------------------------------------------|
| Standardní\_D1\_v2 | 1    | 3,5         | 50                     | 3000 / 46 / 23                                             | 2 / 2×500                         | 2 / 750                                        |
| Standardní\_D2\_v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 4 / 4×500                         | 2 / 1 500                                       |
| Standardní\_D3\_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 8 / 8×500                         | 4 / 3 000                                       |
| Standardní\_D4\_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 16 / 16×500                       | 8 / 6 000                                       |
| Standardní\_A4\_v2 | 4    | 8           | 40                     | 4000 / 80 / 40                                             | 8 / 8×500                         | 4 / 1 000                                       |
| Standardní\_A8\_v2 | 8    | 16          | 80                     | 8000 / 160 / 80                                            | 16 / 16×500                       | 8 / 2 000                                       |

Tady jsou pokyny pro nastavení správné hodnoty pro **AzureSSISMaxParallelExecutionsPerNode** vlastnost: 

1. Nastavte malou hodnotu při prvním.
2. Zvýšit o malou část ke kontrole, jestli je lepší celkovou propustnost.
3. Zastavte, pokud celková propustnost nedosáhne maximální hodnoty zvýšit hodnotu.

## <a name="ssisdbpricingtier"></a>SSISDBPricingTier

**SSISDBPricingTier** je cenovou úroveň pro databázi katalogu služby SSIS (SSISDB) ve službě Azure SQL database. Toto nastavení má vliv na maximální počet pracovních procesů v instanci reakcí na Incidenty, rychlost do fronty spouštění balíčku a rychlost pro načtení protokolu spuštění.

-   Pokud vám nezáleží na rychlost spouštění balíčku fronty a pro načtení protokolu spuštění, můžete databázi nejnižší cenovou úroveň. Základní ceny za Azure SQL Database podporuje 8 pracovních procesů v instanci modulu runtime integrace.

-   Zvolte databázi výkonnější než základní, pokud počet pracovních procesů je víc než 8, nebo počet jader je více než 50. Jinak databáze se stane kritickým bodem instance modulu runtime integrace a je celkový výkon negativně ovlivněn.

Můžete také upravit databázi cenovou úroveň na základě [jednotky transakcí databáze](../sql-database/sql-database-what-is-a-dtu.md) (DTU) informace o využití na portálu Azure portal.

## <a name="design-for-high-performance"></a>Návrh pro vysoký výkon
Navrhování balíčku SSIS v Azure se liší od návrhu balíčku pro místní spuštění. Místo kombinování více nezávislých úloh, které ve stejném balíčku, oddělte je do několika balíčků pro efektivnější spouštění v prostředí Azure-SSIS IR. Spouštění balíčků pro každý balíček, vytvořte tak, aby si uživatelé nebudou muset čekat na jiných na dokončení. Tento přístup těží z prostředí Azure-SSIS integration runtime škálovatelnost a zlepšuje celkovou propustnost.

## <a name="next-steps"></a>Další postup
Další informace o Azure-SSIS Integration Runtime. Zobrazit [prostředí Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime).
