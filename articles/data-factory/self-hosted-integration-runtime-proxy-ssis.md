---
title: Konfigurace místního prostředí Integration runtime jako proxy pro SSIS v Azure Data Factory | Microsoft Docs
description: Přečtěte si, jak nakonfigurovat Integration Runtime pro místní hostování jako proxy pro Azure-SSIS Integration Runtime.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 2f65303cd5cda50a95c3563422c059b985ecf28a
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2019
ms.locfileid: "68737560"
---
# <a name="configure-self-hosted-ir-as-a-proxy-for-azure-ssis-ir-in-adf"></a>Konfigurace místního prostředí IR jako proxy pro Azure-SSIS IR v ADF
Tento článek popisuje, jak spouštět balíčky služba SSIS (SQL Server Integration Services) (SSIS) v Azure-SSIS Integration Runtime (IR) v Azure Data Factory (ADF) s místním prostředím IR nakonfigurovaným jako proxy.  Tato funkce umožňuje přístup k datům místně bez [připojení Azure-SSIS IR k virtuální síti](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).  To je užitečné v případě, že vaše podniková síť má vysoce složitou konfiguraci/omezující zásadu pro vložení Azure-SSIS IR do tohoto prostředí.

Tato funkce rozdělí balíček obsahující úlohu toku dat s místním zdrojem dat do dvou pracovních úloh: první z nich spuštěná v místním prostředí IR nejprve přesune data z místního zdroje dat do pracovní oblasti v Azure Blob Storage, zatímco Druhá z nich spuštěná v Azure-SSIS IR pak přesune data z pracovní oblasti do určeného cíle dat.

Tato funkce také poskytuje další výhody a možnosti, protože umožňuje zřídit technologii IR v místním prostředí v oblastech, které ještě nejsou podporované službou Azure-SSIS IR, a umožnit tak veřejnou statickou IP adresu místního prostředí IR v bráně firewall vašich zdrojů dat atd.

## <a name="prepare-self-hosted-ir"></a>Příprava prostředí IR pro místní hostování
Aby bylo možné tuto funkci používat, musíte nejdřív vytvořit ADF a zřídit Azure-SSIS IR, pokud jste to ještě neudělali, a to podle pokynů v článku [jak zřídit Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) .

Pak budete muset zřídit svůj místní prostředí IR v rámci stejného ADF, kde se Azure-SSIS IR zřídí, pomocí následujícího postupu v článku Vytvoření místního prostředí [IR](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime) .

Nakonec budete muset stáhnout a nainstalovat nejnovější verzi prostředí IR v místním prostředí a také další ovladače a modul runtime na místním počítači nebo virtuálním počítači Azure (VM) následujícím způsobem:
- Stáhněte si a nainstalujte nejnovější verzi prostředí IR v místním prostředí. [](https://www.microsoft.com/download/details.aspx?id=39717)
- Pokud používáte konektory OLEDB ve svých balíčcích, Stáhněte a nainstalujte příslušné ovladače OLEDB do stejného počítače, ve kterém je nainstalovaný modul IR pro místní hostování, pokud jste to ještě neudělali.  Pokud používáte starší verzi ovladače OLEDB pro SQL Server (SQLNCLI), můžete si stáhnout verzi 64 z [tohoto místa](https://www.microsoft.com/download/details.aspx?id=50402).  Pokud používáte nejnovější verzi ovladače OLEDB pro SQL Server (MSOLEDBSQL), můžete si stáhnout verzi 64 z [tohoto místa](https://www.microsoft.com/download/details.aspx?id=56730).  Pokud používáte ovladače OLEDB pro jiné databázové systémy, jako je PostgreSQL, MySQL, Oracle atd., můžete si stáhnout 64 verzi ze svých příslušných webů.
- Pokud jste to ještě neudělali, Stáhněte a nainstalujte modul runtime Visual C++ (VC) na stejném počítači, kde je nainstalováno prostředí IR pro místní hostování.  64 verzi si můžete stáhnout [tady](https://www.microsoft.com/download/details.aspx?id=40784).

## <a name="prepare-azure-blob-storage-linked-service-for-staging"></a>Příprava propojené služby Azure Blob Storage pro přípravu
Vytvořte propojenou službu Azure Blob Storage v rámci stejného ADF, ve kterém je Azure-SSIS IR zřízená, pokud jste to ještě neudělali, podle článku [jak vytvořit propojenou službu ADF](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service) .  Ověřte prosím následující:
- Pro **úložiště dat** je vybraná možnost **Azure Blob Storage** .
- **AutoResolveIntegrationRuntime** je vybraný pro **připojení prostřednictvím prostředí Integration runtime** .
- Pro **metodu ověřování** je vybraný buď klíčový**objekt** **SAS identifikátoru URI**/ **klíče**/účtu.

![Příprava propojené služby Azure Blob Storage pro přípravu](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-azure-ssis-ir-with-self-hosted-ir-as-a-proxy"></a>Konfigurace Azure-SSIS IR pomocí místního prostředí IR jako proxy
Připravili jste místní prostředí IR a propojenou službu Azure Blob Storage pro přípravu, teď můžete nakonfigurovat svůj nový nebo stávající Azure-SSIS IR pomocí prostředí IR pro místní hostování jako proxy na portálu ADF nebo v aplikaci.  Pokud je spuštěný váš stávající Azure-SSIS IR, zastavte ho prosím ještě před tím, než to uděláte, a pak ho znovu spusťte.

Na stránce **Upřesnit nastavení** zaškrtněte políčko **nastavit Integration runtime v místním prostředí jako proxy pro Azure-SSIS Integration runtime** , vyberte své místní prostředí IR a propojenou službu Azure Blob Storage pro přípravu a určete objekt BLOB. název kontejneru pro **pracovní cestu** , pokud chcete.

![Konfigurace Azure-SSIS IR pomocí místního prostředí IR jako proxy](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-settings-ssisir.png)

## <a name="enable-ssis-packages-to-connect-by-proxy"></a>Povolit SSIS balíčky pro připojení pomocí proxy
Pomocí nejnovější SSDT s rozšířením projektů SSIS pro Visual Studio, které se dá stáhnout [](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) odsud nebo jako samostatný instalační program, který se dá stáhnout z [tohoto místa](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer), můžete najít novou vlastnost **ConnectByProxy** , která se přidala v OLEDB/ Správce připojení plochých souborů.  

Když navrhujete nové balíčky obsahující úlohy toku dat se zdroji souborů OLEDB/Flat pro přístup k databázím a souborům v místním prostředí, můžete tuto vlastnost povolit nastavením na **hodnotu true** na panelu Vlastnosti příslušných správců připojení.

![Povolit vlastnost ConnectByProxy](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

Tuto vlastnost můžete také povolit při spouštění existujících balíčků, aniž byste je museli ručně změnit o jednu.  K dispozici jsou dvě možnosti:
- Otevřete, znovu sestavíte a znovu nasadíte projekt, který obsahuje tyto balíčky, s nejnovějším SSDT pro spuštění v Azure-SSIS IR: Vlastnost může být povolena nastavením **hodnoty true** pro příslušné Správce připojení, který se zobrazí na kartě **Správci připojení** v místním okně spustit balíček při spuštění balíčků z SSMS.

  ![Povolit ConnectByProxy Vlastnost2](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  Vlastnost se dá povolit taky tak, že ji nastavíte na **true** pro příslušné Správce připojení, které se zobrazí na kartě **Správci připojení** v [aktivitě SSIS balíčku](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) pro spuštění balíčků v kanálech ADF.
  
  ![Povolit ConnectByProxy property3](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- Opětovné nasazení projektu obsahujícího tyto balíčky ke spuštění v SSIS IR: Vlastnost se pak dá povolit tak `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`, že poskytne cestu k vlastnosti a nastaví ji na **true** jako přepsání vlastnosti na kartě **Upřesnit** v automaticky otevíraném okně spustit balíček při spouštění balíčků z SSMS.

  ![Povolit ConnectByProxy property4](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  Vlastnost může být také povolena tím, že poskytuje `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`cestu k její vlastnosti a nastaví ji na **hodnotu true** jako přepsání vlastnosti na kartě **přepsání vlastností** [aktivity SSIS Package](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) při spouštění balíčků v kanálech ADF.
  
  ![Povolit ConnectByProxy property5](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>Ladění první a druhé pracovní úlohy
V místním prostředí IR můžete najít protokoly modulu runtime ve `C:\ProgramData\SSISTelemetry` složce a protokoly spuštění první pracovní úlohy ve `C:\ProgramData\SSISTelemetry\ExecutionLog` složce.  Protokoly spouštění druhých pracovních úkolů najdete v SSISDB nebo zadaných cestách protokolování, v závislosti na tom, jestli vaše balíčky ukládáte do SSISDB nebo souborů/sdílených složek/souborů Azure (v uvedeném pořadí).  Jedinečné identifikátory první pracovní úlohy můžete také najít v protokolech spouštění druhé pracovní úlohy, např. 

![Jedinečné ID první pracovní úlohy](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="billing-for-the-first-and-second-staging-tasks"></a>Faktura za první a druhý pracovní úkol
První pracovní úkoly spuštěné v místním prostředí IR se budou účtovat samostatně stejným způsobem jako všechny aktivity přesunu dat spuštěné v místním prostředí IR se účtují tak, jak je uvedeno v článku o [cenách datového kanálu ADF](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) .

Druhý pracovní úkol běžící na vašem Azure-SSIS IR se nebude účtovat samostatně, ale běžící Azure-SSIS IR se bude účtovat tak, jak je uvedeno v článku o [cenách Azure-SSIS IR](https://azure.microsoft.com/pricing/details/data-factory/ssis/) .

## <a name="current-limitations"></a>Aktuální omezení
- V současné době jsou podporovány pouze správci připojení souborů OLEDB a plochý soubor a zdroje OLEDB/ploché soubory.
- V současné době jsou podporovány pouze propojené služby Azure Blob Storage nakonfigurované s **klíčem**/účtu**SAS identifikátoru**/zabezpečení SAS.
- V tuto chvíli se podporuje jenom prostředí IR s vlastním hostováním v rámci stejného ADF, kde se zřídí Azure-SSIS IR.

## <a name="next-steps"></a>Další postup
Po nakonfigurování místního prostředí IR jako proxy pro Azure-SSIS IR můžete nasadit a spustit balíčky pro přístup k datům v místním prostředí, jako jsou aktivity balíčku SSIS v kanálech ADF, najdete v tématu [spouštění balíčků SSIS jako provádění aktivit balíčků SSIS v kanálech ADF. ](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).