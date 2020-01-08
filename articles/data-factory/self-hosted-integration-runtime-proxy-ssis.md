---
title: Konfigurace místního prostředí Integration runtime jako proxy pro SSIS
description: Přečtěte si, jak nakonfigurovat Integration Runtime v místním prostředí jako proxy pro Azure-SSIS Integration Runtime.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 12/23/2019
ms.openlocfilehash: 8754b2ef9727b5526cc9bcf756085f2f05f6a398
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552096"
---
# <a name="configure-self-hosted-ir-as-a-proxy-for-azure-ssis-ir-in-adf"></a>Konfigurace místního prostředí IR jako proxy pro Azure-SSIS IR v ADF

Tento článek popisuje, jak spouštět balíčky služba SSIS (SQL Server Integration Services) (SSIS) v Azure-SSIS Integration Runtime (IR) v Azure Data Factory (ADF) s místním prostředím IR nakonfigurovaným jako proxy.  Tato funkce umožňuje přístup k datům místně bez [připojení Azure-SSIS IR k virtuální síti](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).  To je užitečné v případě, že vaše podniková síť má příliš složitou zásadu konfigurace/omezující zásady, která umožňuje vložit do ní Azure-SSIS IR.

Tato funkce rozdělí balíček obsahující úlohu toku dat s místním zdrojem dat do dvou pracovních úloh: první z nich spuštěná v místním prostředí IR nejprve přesune data z místního zdroje dat do pracovní oblasti v Azure Blob Storage, zatímco Druhá z nich spuštěná v Azure-SSIS IR pak přesune data z pracovní oblasti do určeného cíle dat.

Tato funkce také poskytuje další výhody a možnosti, protože umožňuje zřídit technologii IR v místním prostředí v oblastech, které ještě nejsou podporované nástrojem Azure-SSIS IR, povolit veřejnou statickou IP adresu místního prostředí IR v bráně firewall vašich zdrojů dat atd.

## <a name="prepare-self-hosted-ir"></a>Příprava prostředí IR pro místní hostování

Aby bylo možné tuto funkci používat, musíte nejdřív vytvořit ADF a zřídit Azure-SSIS IR pod ní, pokud jste to ještě neudělali, a to pomocí článku [jak zřídit Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) článek.

Potom budete muset zřídit svůj místní prostředí IR v rámci stejného ADF, kde se vaše Azure-SSIS IR zřídí, podle pokynů v článku [vytvoření prostředí IR s místním hostováním](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime) .

Nakonec budete muset stáhnout a nainstalovat nejnovější verzi prostředí IR v místním prostředí a také další ovladače a modul runtime na místním počítači nebo virtuálním počítači Azure (VM) následujícím způsobem:
- Stáhněte si a nainstalujte nejnovější verzi prostředí IR v místním prostředí (webhost) z [tohoto místa](https://www.microsoft.com/download/details.aspx?id=39717).
- Pokud používáte konektory OLEDB ve svých balíčcích, Stáhněte a nainstalujte příslušné ovladače OLEDB do stejného počítače, ve kterém je nainstalováno prostředí IR v místním prostředí, pokud jste to ještě neudělali.  Pokud používáte starší verzi ovladače OLEDB pro SQL Server (SQLNCLI), můžete si stáhnout verzi 64 z [tohoto místa](https://www.microsoft.com/download/details.aspx?id=50402).  Pokud používáte nejnovější verzi ovladače OLEDB pro SQL Server (MSOLEDBSQL), můžete si stáhnout verzi 64 z [tohoto místa](https://www.microsoft.com/download/details.aspx?id=56730).  Pokud používáte ovladače OLEDB pro jiné databázové systémy, jako je PostgreSQL, MySQL, Oracle atd., můžete si stáhnout 64 verzi ze svých příslušných webů.
- Pokud jste to ještě C++ neudělali, Stáhněte a nainstalujte Visual (VC) runtime na stejném počítači, kde je nainstalováno prostředí IR pro místní hostování.  64 verzi si můžete stáhnout [tady](https://www.microsoft.com/download/details.aspx?id=40784).

## <a name="prepare-azure-blob-storage-linked-service-for-staging"></a>Příprava propojené služby Azure Blob Storage pro přípravu

Pokud jste to ještě neudělali, vytvořte propojenou službu Azure Blob Storage v rámci stejného ADF, na které jste svoji Azure-SSIS IR zřídili (Pokud jste to ještě neudělali) podle článku [jak vytvořit propojenou službu ADF](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service) .  Ověřte prosím následující:
- Pro **úložiště dat** je vybraná možnost **Azure Blob Storage** .
- **AutoResolveIntegrationRuntime** je vybraný pro **připojení prostřednictvím prostředí Integration runtime** .
- Pro **metodu ověřování** je vybraný buď **klíč účtu**/**identifikátor URI SAS**/**instanční objekt** .

>[!TIP]
>Když je vybraný **instanční objekt** , udělte aspoň **roli Přispěvatel dat objektu BLOB úložiště**. Další informace najdete v tématu [konektor Azure Blob Storage](connector-azure-blob-storage.md#linked-service-properties).

![Příprava propojené služby Azure Blob Storage pro přípravu](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-azure-ssis-ir-with-self-hosted-ir-as-a-proxy"></a>Konfigurace Azure-SSIS IR s využitím místního hostitele IR jako proxy

Když jste připravili vaše místní prostředí IR a propojenou službu Azure Blob Storage pro přípravu, můžete teď nakonfigurovat nové nebo existující Azure-SSIS IR pomocí prostředí IR pro místní hostování jako proxy na portálu ADF nebo v aplikaci.  Pokud je spuštěný existující Azure-SSIS IR, před tím, než to uděláte, ho zastavte a pak ho znovu spusťte.

1. V instalačním panelu prostředí Integration runtime se v sekcích **Obecné nastavení** a **nastavení SQL** pokračujte výběrem tlačítka **Další** . 

1. V části **Upřesnit nastavení** :

   1. Zaškrtněte políčko **nastavit Integration runtime pro místní hostování jako proxy pro Azure-SSIS Integration runtime** zaškrtávací políčko. 

   1. Pro **Integration runtime**v místním prostředí vyberte pro Azure-SSIS IR existující místní prostředí IR jako proxy.

   1. V případě **pracovní služby s**ochranou úložiště vyberte existující propojenou službu Azure Blob Storage nebo vytvořte novou pro přípravu.

   1. Do pole **pracovní cesta**zadejte kontejner objektů BLOB ve vybraném účtu služby Azure Blob Storage nebo ho nechte prázdný, aby se pro přípravu použil výchozí.

   1. Vyberte **Pokračovat**.

   ![Rozšířená nastavení s místním prostředím IR](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

## <a name="enable-ssis-packages-to-connect-by-proxy"></a>Povolit SSIS balíčky pro připojení pomocí proxy

Pomocí nejnovější SSDT s rozšířením projektů SSIS pro Visual Studio, které se dá stáhnout [odsud nebo jako](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) samostatný instalační program, který se dá stáhnout z [tohoto místa](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer), můžete najít novou vlastnost **ConnectByProxy** , která se přidala do Správce připojení OLEDB/plochých souborů.  

Když navrhujete nové balíčky obsahující úlohy toku dat se zdroji souborů OLEDB/Flat pro přístup k databázím a souborům v místním prostředí, můžete tuto vlastnost povolit nastavením na **hodnotu true** na panelu Vlastnosti příslušných správců připojení.

![Povolit vlastnost ConnectByProxy](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

Tuto vlastnost můžete také povolit při spouštění existujících balíčků, aniž byste je museli ručně změnit o jednu.  Existují dvě možnosti:
- Otevřete, znovu sestavíte a znovu nasadíte projekt obsahující tyto balíčky s nejnovějším SSDT pro spuštění ve vašem Azure-SSIS IR: vlastnost lze povolit nastavením na **hodnotu true** pro příslušné Správce připojení, který se zobrazí na kartě **Správci připojení** v místním okně spustit balíček při spouštění balíčků z SSMS.

  ![Povolit ConnectByProxy Vlastnost2](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  Vlastnost se dá povolit taky tak, že ji nastavíte na **true** pro příslušné Správce připojení, které se zobrazí na kartě **Správci připojení** v [aktivitě SSIS balíčku](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) pro spuštění balíčků v kanálech ADF.
  
  ![Povolit ConnectByProxy property3](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- Opětovné nasazení projektu obsahujícího tyto balíčky ke spuštění v SSIS IR: vlastnost se pak dá povolit tak, že poskytne cestu k vlastnosti, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`a nastaví ji na **true** jako přepsání vlastnosti na kartě **Upřesnit** v místním okně spustit balíček při spouštění balíčků z SSMS.

  ![Povolit ConnectByProxy property4](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  Vlastnost může být také povolena tím, že poskytuje cestu k vlastnostem, `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`a nastavení na **hodnotu true** jako přepsání vlastnosti na kartě **přepsání vlastností** [aktivity balíčku](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) Overrides při spuštění balíčků v kanálech ADF.
  
  ![Povolit ConnectByProxy property5](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>Ladění první a druhé pracovní úlohy

V místním prostředí IR můžete najít protokoly modulu runtime ve složce `C:\ProgramData\SSISTelemetry` a protokoly spouštění pro první pracovní úkoly v `C:\ProgramData\SSISTelemetry\ExecutionLog` složce.  Protokoly spouštění druhých pracovních úkolů najdete v SSISDB nebo zadaných cestách protokolování, v závislosti na tom, jestli vaše balíčky ukládáte do SSISDB nebo souborů/sdílených složek/souborů Azure (v uvedeném pořadí).  Jedinečné identifikátory první pracovní úlohy můžete také najít v protokolech spouštění druhé pracovní úlohy, např. 

![Jedinečné ID první pracovní úlohy](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="using-windows-authentication-in-staging-tasks"></a>Použití ověřování systému Windows v pracovních úlohách

Pokud vaše pracovní úkoly v místním prostředí IR vyžadují ověřování systému Windows, je třeba [nakonfigurovat balíčky SSIS tak, aby používaly stejné ověřování systému Windows](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth?view=sql-server-ver15). Vaše pracovní úkoly budou vyvolány s účtem služby IR v místním prostředí (ve výchozím nastavení`NT SERVICE\DIAHostService`) a vaše úložiště dat budou k dispozici s účtem ověřování systému Windows. Oba účty vyžadují, aby jim byly přiřazeny určité zásady zabezpečení. Proto v místním počítači IR otevřete `Local Security Policy` -> `Local Policies` -> `User Rights Assignment` a proveďte následující kroky.
- Přiřaďte **kvóty pro úpravu paměti pro proces** a **nahraďte zásady tokenu na úrovni procesu** na účet služby IR pro místní hostování. Tato funkce by měla být automaticky provedena při instalaci místního prostředí IR s výchozím účtem služby. Pokud používáte jiný účet služby, přiřaďte k němu stejné zásady.
- Přiřaďte k účtu ověřování systému Windows zásadu **přihlášení jako služby** .

## <a name="billing-for-the-first-and-second-staging-tasks"></a>Faktura za první a druhý pracovní úkol

První pracovní úkoly spuštěné v místním prostředí IR se budou účtovat samostatně stejným způsobem jako všechny aktivity přesunu dat spuštěné v místním prostředí IR se účtují tak, jak je uvedeno v článku o [cenách datového kanálu ADF](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) .

Druhý pracovní úkol, který běží na vašem Azure-SSIS IR, se nebude účtovat samostatně, ale běžící Azure-SSIS IR bude účtována tak, jak je uvedeno v článku [Azure-SSIS IR ceny](https://azure.microsoft.com/pricing/details/data-factory/ssis/) .

## <a name="current-limitations"></a>Aktuální omezení

- V současné době jsou podporovány pouze úlohy toku dat se správci připojení souborů ODBC/OLEDB/Flat File a ODBC/OLEDB/ploché soubory. 
- V současné době jsou podporovány pouze propojené služby Azure Blob Storage nakonfigurované s **klíčem účtu**/**identifikátor URI SAS**/ověřování **instančního objektu** .

## <a name="next-steps"></a>Další kroky

Po nakonfigurování místního prostředí IR jako proxy pro váš Azure-SSIS IR můžete nasadit a spustit balíčky pro přístup k datům v místním prostředí, jak spouštět aktivity balíčků SSIS v kanálech ADF, v tématu [spouštění balíčků SSIS jako provádění aktivit balíčku SSIS v kanálech ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).