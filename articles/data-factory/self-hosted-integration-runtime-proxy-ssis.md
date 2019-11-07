---
title: Konfigurace místního prostředí Integration runtime jako proxy serveru pro SSIS v Azure Data Factory
description: Přečtěte si, jak nakonfigurovat Integration Runtime v místním prostředí jako proxy pro Azure-SSIS Integration Runtime.
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
ms.openlocfilehash: 178628db11b95fbd345e94111ebf15809da3fc35
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684295"
---
# <a name="configure-self-hosted-ir-as-a-proxy-for-azure-ssis-ir-in-adf"></a>Konfigurace místního prostředí IR jako proxy pro Azure-SSIS IR v ADF
Tento článek popisuje, jak spouštět balíčky služba SSIS (SQL Server Integration Services) (SSIS) v Azure-SSIS Integration Runtime (IR) v Azure Data Factory (ADF) s místním prostředím IR nakonfigurovaným jako proxy.  Tato funkce umožňuje přístup k datům místně bez [připojení Azure-SSIS IR k virtuální síti](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).  To je užitečné v případě, že vaše podniková síť má příliš složitou zásadu konfigurace/omezující zásady, která umožňuje vložit do ní Azure-SSIS IR.

Tato funkce rozdělí balíček obsahující úlohu toku dat s místním zdrojem dat do dvou pracovních úloh: první z nich spuštěná v místním prostředí IR nejprve přesune data z místního zdroje dat do pracovní oblasti v Azure Blob Storage, zatímco Druhá z nich spuštěná v Azure-SSIS IR pak přesune data z pracovní oblasti do určeného cíle dat.

Tato funkce také poskytuje další výhody a možnosti, protože umožňuje zřídit technologii IR v místním prostředí v oblastech, které ještě nejsou podporované nástrojem Azure-SSIS IR, povolit veřejnou statickou IP adresu místního prostředí IR v bráně firewall vašich zdrojů dat atd.

## <a name="prepare-self-hosted-ir"></a>Příprava prostředí IR pro místní hostování
Aby bylo možné tuto funkci používat, musíte nejdřív vytvořit ADF a zřídit Azure-SSIS IR pod ní, pokud jste to ještě neudělali, a to pomocí článku [jak zřídit Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) článek.

Potom budete muset zřídit svůj místní prostředí IR v rámci stejného ADF, kde se vaše Azure-SSIS IR zřídí, podle pokynů v článku [vytvoření prostředí IR s místním hostováním](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime) .

Nakonec budete muset stáhnout a nainstalovat nejnovější verzi prostředí IR v místním prostředí a také další ovladače a modul runtime na místním počítači nebo virtuálním počítači Azure (VM) následujícím způsobem:
- Stáhněte si a nainstalujte nejnovější verzi prostředí [IR v místním prostředí.](https://www.microsoft.com/download/details.aspx?id=39717)
- Pokud používáte konektory OLEDB ve svých balíčcích, Stáhněte a nainstalujte příslušné ovladače OLEDB do stejného počítače, ve kterém je nainstalovaný modul IR pro místní hostování, pokud jste to ještě neudělali.  Pokud používáte starší verzi ovladače OLEDB pro SQL Server (SQLNCLI), můžete si stáhnout verzi 64 z [tohoto místa](https://www.microsoft.com/download/details.aspx?id=50402).  Pokud používáte nejnovější verzi ovladače OLEDB pro SQL Server (MSOLEDBSQL), můžete si stáhnout verzi 64 z [tohoto místa](https://www.microsoft.com/download/details.aspx?id=56730).  Pokud používáte ovladače OLEDB pro jiné databázové systémy, jako je PostgreSQL, MySQL, Oracle atd., můžete si stáhnout 64 verzi ze svých příslušných webů.
- Pokud jste to ještě neudělali, Stáhněte a nainstalujte modul runtime Visual C++ (VC) na stejném počítači, kde je nainstalováno prostředí IR pro místní hostování.  64 verzi si můžete stáhnout [tady](https://www.microsoft.com/download/details.aspx?id=40784).

## <a name="prepare-azure-blob-storage-linked-service-for-staging"></a>Příprava propojené služby Azure Blob Storage pro přípravu
Vytvořte propojenou službu Azure Blob Storage v rámci stejného ADF, kde jste nastavili Azure-SSIS IR, pokud jste to ještě neudělali, podle článku [jak vytvořit propojenou službu ADF](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service) .  Ověřte prosím následující:
- Pro **úložiště dat** je vybraná možnost **Azure Blob Storage** .
- **AutoResolveIntegrationRuntime** je vybraný pro **připojení prostřednictvím prostředí Integration runtime** .
- Pro **metodu ověřování** je vybraný buď **klíč účtu**/**identifikátor URI SAS**/**instanční objekt** .

![Příprava propojené služby Azure Blob Storage pro přípravu](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-azure-ssis-ir-with-self-hosted-ir-as-a-proxy"></a>Konfigurace Azure-SSIS IR s využitím místního hostitele IR jako proxy
Když jste připravili vaše místní prostředí IR a propojenou službu Azure Blob Storage pro přípravu, můžete teď nakonfigurovat nové nebo existující Azure-SSIS IR pomocí prostředí IR pro místní hostování jako proxy na portálu ADF nebo v aplikaci.  Pokud je vaše stávající Azure-SSIS IR spuštěná, zastavte ji prosím a pak ji znovu spusťte.

Na stránce **Upřesnit nastavení** zaškrtněte políčko **nastavit Integration runtime v místním prostředí jako proxy pro Azure-SSIS Integration runtime** zaškrtávací políčko, vyberte místní prostředí IR a propojenou službu Azure Blob Storage pro přípravu a určete objekt BLOB. název kontejneru pro **pracovní cestu** , pokud chcete.

![Konfigurace Azure-SSIS IR s využitím místního hostitele IR jako proxy](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-settings-ssisir.png)

## <a name="enable-ssis-packages-to-connect-by-proxy"></a>Povolit SSIS balíčky pro připojení pomocí proxy
Pomocí nejnovější SSDT s rozšířením projektů SSIS pro Visual Studio, které se dá stáhnout [odsud nebo jako](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) samostatný instalační program, který se dá stáhnout z [tohoto místa](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer), můžete najít novou vlastnost **ConnectByProxy** , která se přidala v OLEDB/ Správce připojení plochých souborů.  

Když navrhujete nové balíčky obsahující úlohy toku dat se zdroji souborů OLEDB/Flat pro přístup k databázím a souborům v místním prostředí, můžete tuto vlastnost povolit nastavením na **hodnotu true** na panelu Vlastnosti příslušných správců připojení.

![Povolit vlastnost ConnectByProxy](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

Tuto vlastnost můžete také povolit při spouštění existujících balíčků, aniž byste je museli ručně změnit o jednu.  K dispozici jsou dvě možnosti:
- Otevřete, znovu sestavíte a znovu nasadíte projekt obsahující tyto balíčky s nejnovějším SSDT pro spuštění na vašem Azure-SSIS IR: vlastnost se dá povolit tak, že ji nastavíte na **true** pro příslušné Správce připojení, které se zobrazí v **připojení. Karta manažeři** v automaticky otevíraném okně pro spuštění balíčku při spouštění balíčků z SSMS.

  ![Povolit ConnectByProxy Vlastnost2](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  Vlastnost se dá povolit taky tak, že ji nastavíte na **true** pro příslušné Správce připojení, které se zobrazí na kartě **Správci připojení** v [aktivitě SSIS balíčku](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) pro spuštění balíčků v kanálech ADF.
  
  ![Povolit ConnectByProxy property3](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- Opětovné nasazení projektu obsahujícího tyto balíčky ke spuštění v SSIS IR: vlastnost se pak může povolit zadáním cesty vlastností, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`a nastavením na **hodnotu true** jako přepsání vlastnosti na kartě **Upřesnit** v místním okně spustit balíček. při spouštění balíčků z SSMS.

  ![Povolit ConnectByProxy property4](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  Vlastnost může být také povolena tím, že poskytuje cestu k vlastnostem, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`a nastavení na **hodnotu true** jako přepsání vlastnosti na kartě **přepsání vlastností** [aktivity balíčku](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) Overrides při spuštění balíčků v kanálech ADF.
  
  ![Povolit ConnectByProxy property5](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>Ladění první a druhé pracovní úlohy
V místním prostředí IR můžete najít protokoly modulu runtime ve složce `C:\ProgramData\SSISTelemetry` a protokoly spouštění pro první pracovní úkoly v `C:\ProgramData\SSISTelemetry\ExecutionLog` složce.  Protokoly spouštění druhých pracovních úkolů najdete v SSISDB nebo zadaných cestách protokolování, v závislosti na tom, jestli vaše balíčky ukládáte do SSISDB nebo souborů/sdílených složek/souborů Azure (v uvedeném pořadí).  Jedinečné identifikátory první pracovní úlohy můžete také najít v protokolech spouštění druhé pracovní úlohy, např. 

![Jedinečné ID první pracovní úlohy](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="billing-for-the-first-and-second-staging-tasks"></a>Faktura za první a druhý pracovní úkol
První pracovní úkoly spuštěné v místním prostředí IR se budou účtovat samostatně stejným způsobem jako všechny aktivity přesunu dat spuštěné v místním prostředí IR se účtují tak, jak je uvedeno v článku o [cenách datového kanálu ADF](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) .

Druhý pracovní úkol, který běží na vašem Azure-SSIS IR, se nebude účtovat samostatně, ale běžící Azure-SSIS IR bude účtována tak, jak je uvedeno v článku [Azure-SSIS IR ceny](https://azure.microsoft.com/pricing/details/data-factory/ssis/) .

## <a name="current-limitations"></a>Aktuální omezení

- V současné době jsou podporovány pouze správci připojení souborů OLEDB a plochý soubor a zdroje OLEDB/ploché soubory. 
- V současné době jsou podporovány pouze propojené služby Azure Blob Storage nakonfigurované s **klíčem účtu**/**identifikátor URI SAS**/ověřování **instančního objektu** .
- V současné době je podporována pouze místní prostředí IR zřízené v rámci stejného ADF, kde je zajištěna vaše Azure-SSIS IR.
- Použití parametrů nebo proměnných SSIS ve vlastnostech zdrojů OLEDB/plochých souborů a správců připojení se nepodporuje.

## <a name="next-steps"></a>Další kroky
Po nakonfigurování místního prostředí IR jako proxy serveru pro Azure-SSIS IR můžete nasadit a spustit balíčky pro přístup k datům v místním prostředí, jak spouštět aktivity balíčků SSIS v kanálech ADF, v tématu [spouštění balíčků SSIS jako provádění aktivit balíčku SSIS v kanálech ADF. ](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).