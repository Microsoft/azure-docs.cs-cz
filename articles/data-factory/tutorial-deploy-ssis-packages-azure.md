---
title: Zřízení prostředí Azure-SSIS Integration runtime
description: Zjistěte, jak zřídit prostředí Azure-SSIS Integration Runtime ve službě Azure Data Factory, abyste v Azure mohli nasazovat a spouštět balíčky SSIS.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: tutorial
ms.date: 09/15/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 14871aa790bd94dbd23dea30c8a229eac7945c75
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683561"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Zřízení prostředí Azure-SSIS Integration runtime v Azure Data Factory

V tomto kurzu najdete postup, jak pomocí Azure Portal zřídit prostředí Azure-služba SSIS (SQL Server Integration Services) (IR) Integration runtime (SSIS) v Azure Data Factory. Azure-SSIS IR podporuje:

- Spuštění balíčků nasazených do katalogu SSIS (SSISDB), které jsou hostovány serverem Azure SQL Database nebo spravované instance (model nasazení projektu).
- Spouštění balíčků nasazených do systémů souborů, sdílených složek nebo souborů Azure (model nasazení balíčku). 

Po zřízení Azure-SSIS IR můžete pomocí známých nástrojů nasadit a spustit balíčky v Azure. Mezi tyto nástroje patří SQL Server Data Tools, SQL Server Management Studio a nástroje příkazového řádku, jako jsou `dtinstall`, `dtutil`a `dtexec`.

Informace o konceptu prostředí Azure-SSIS IR najdete v [přehledu prostředí Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime).

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Zřízení prostředí Azure-SSIS Integration Runtime

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Předplatné Azure**. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
- **Server Azure SQL Database (volitelné)** . Pokud ještě nemáte databázový server, vytvořte si ho na webu Azure Portal před tím, než začnete. Data Factory pak na tomto databázovém serveru vytvoří instanci SSISDB. 

  Doporučujeme vytvořit databázový server ve stejné oblasti Azure jako prostředí Integration Runtime. Tato konfigurace umožňuje prostředí Integration runtime zapisovat protokoly spouštění do SSISDB bez přechodu do oblastí Azure.

  Pamatujte na tyto body:

  - V závislosti na vybraném databázovém serveru může být instance SSISDB vytvořená vaším jménem jako jediná databáze, jako součást elastického fondu nebo ve spravované instanci. Může být přístupný ve veřejné síti nebo prostřednictvím připojení k virtuální síti. Pokyny k výběru typu databázového serveru pro hostování SSISDB najdete v tématu [porovnání Azure SQL Database izolované databáze, elastického fondu a spravované instance](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance). 
  
    Pokud používáte server Azure SQL Database s koncovými body služby virtuální sítě nebo spravovanou instancí s privátním koncovým bodem pro hostování SSISDB, nebo pokud budete vyžadovat přístup k místním datům bez konfigurace místního prostředí IR, musíte se připojit k Azure-SSIS IR k virtuálnímu počítači. sítě. Další informace najdete v tématu [vytvoření Azure-SSIS IR ve virtuální síti](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
  - Ujistěte se, že má databázový server povolené nastavení **Povolit přístup ke službám Azure**. Toto nastavení se nedá použít, pokud používáte server Azure SQL Database s koncovými body služby virtuální sítě nebo spravovanou instancí s privátním koncovým bodem pro hostování SSISDB. Další informace najdete v tématu [Zabezpečení databáze Azure SQL](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Pokud chcete toto nastavení povolit pomocí PowerShellu, přečtěte si článek [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).
  - Přidejte IP adresu klientského počítače nebo rozsah IP adres, který obsahuje IP adresu klientského počítače, do seznamu IP adres klienta v nastavení brány firewall pro databázový server. Další informace najdete v tématu [Pravidla brány firewall na úrovni serveru a databáze služby Azure SQL Database](../sql-database/sql-database-firewall-configure.md).
  - K databázovému serveru se můžete připojit pomocí ověřování SQL s přihlašovacími údaji správce serveru nebo pomocí ověřování Azure AD se spravovanou identitou pro vaši datovou továrnu. V takovém případě je potřeba přidat spravovanou identitu pro vaši datovou továrnu do skupiny Azure AD s přístupovými oprávněními k databázovému serveru. Další informace najdete v tématu [vytvoření Azure-SSIS IR s ověřováním Azure AD](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
  - Ověřte, že váš databázový server ještě nemá instanci SSISDB. Zřizování Azure-SSIS IR nepodporuje použití existující instance SSISDB.


> [!NOTE]
> Seznam oblastí Azure, ve kterých jsou aktuálně k dispozici Data Factory a Azure-SSIS IR, najdete v části [Data Factory a SSIS IR dostupnost v oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

Pokud chcete vytvořit datovou továrnu pomocí Azure Portal, postupujte podle podrobných pokynů v tématu [Vytvoření datové továrny prostřednictvím uživatelského rozhraní](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory). Když to uděláte, vyberte **Připnout na řídicí panel** , abyste po jeho vytvoření povolili rychlý přístup. 

Po vytvoření objektu pro vytváření dat otevřete jeho stránku Přehled v Azure Portal. Výběrem dlaždice **autora & monitorování** otevřete stránku **Začínáme** na samostatné kartě. Zde můžete pokračovat v vytváření Azure-SSIS IR.

## <a name="create-an-azure-ssis-integration-runtime"></a>Vytvoření prostředí Azure-SSIS Integration Runtime

### <a name="from-the-data-factory-overview"></a>Z přehledu Data Factory

1. Na stránce **Začínáme** vyberte dlaždici **Konfigurace prostředí SSIS Integration Runtime**. 

   ![Dlaždice Konfigurace prostředí SSIS Integration Runtime](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Zbývající kroky nastavení prostředí Azure-SSIS IR najdete v části [Zřízení prostředí Azure-SSIS Integration Runtime](#provision-an-azure-ssis-integration-runtime). 

### <a name="from-the-authoring-ui"></a>Z uživatelského rozhraní pro vytváření obsahu

1. V uživatelském rozhraní Azure Data Factory přepněte na kartu **Upravit** a vyberte **připojení**. Pak přejděte na kartu **prostředí Integration runtime** , kde se zobrazí existující prostředí Integration runtime ve vaší datové továrně. 

   ![Výběr možností za účelem zobrazení stávajících prostředí IR](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Výběrem možnosti **Nové** vytvořte nové prostředí Azure-SSIS IR. 

   ![Prostředí Integration Runtime prostřednictvím nabídky](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. V okně **Instalace prostředí Integration Runtime** vyberte možnost **Migrovat metodou „lift and shift“ existující balíčky služby SSIS ke spuštění v Azure** a pak vyberte tlačítko **Další**. 

   ![Zadání typu prostředí Integration Runtime](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Zbývající kroky nastavení prostředí Azure-SSIS IR najdete v části [Zřízení prostředí Azure-SSIS Integration Runtime](#provision-an-azure-ssis-integration-runtime). 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Zřízení prostředí Azure-SSIS Integration Runtime

1. Na stránce **Obecné nastavení** **Integration runtime instalaci**proveďte následující kroky. 

   ![Obecná nastavení](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   a. Jako **Název** zadejte název vašeho prostředí Integration Runtime. 

   b. Jako **Popis** zadejte popis vašeho prostředí Integration Runtime. 

   c. Jako **Umístění** vyberte umístění vašeho prostředí Integration Runtime. Zobrazí se pouze podporovaná umístění. Doporučujeme vybrat stejné umístění, jako má váš databázový server pro hostování databáze SSISDB. 

   d. Jako **Velikost uzlu** vyberte velikost uzlu ve vašem clusteru Integration Runtime. Zobrazí se pouze podporované velikosti uzlů. Pokud chcete spustit mnoho balíčků náročných na výpočetní výkon nebo paměť s náročnou na paměť, vyberte velikost velkého uzlu (horizontální navýšení kapacity). 

   e. Jako **Počet uzlů** vyberte počet uzlů ve vašem clusteru Integration Runtime. Zobrazí se pouze podporované počty uzlů. Vyberte velký cluster s mnoha uzly (horizontální navýšení kapacity), pokud chcete souběžně spouštět mnoho balíčků. 

   f. V části **edice/licence**vyberte edici SQL Server pro modul runtime integrace: Standard nebo Enterprise. Vyberte možnost Enterprise, pokud chcete používat pokročilé funkce v prostředí Integration runtime. 

   g. Pro **Uložit peníze**vyberte možnost zvýhodněné hybridní využití Azure pro prostředí Integration Runtime: **Ano** nebo **ne**. Vyberte **Ano** , pokud chcete využít vlastní licenci SQL Server se Software Assurance, abyste využili úspory nákladů s využitím hybridního použití. 

   h. Vyberte **Next** (Další). 

1. Na stránce **nastavení SQL** proveďte následující kroky. 

   ![Nastavení SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. Zaškrtněte políčko **vytvořit katalog SSIS...** a zvolte model nasazení pro balíčky, které se mají spustit na vašem Azure-SSIS IR. Zvolíte buď model nasazení projektu, kde jsou balíčky nasazeny do SSISDB hostovaného databázovým serverem, nebo model nasazení balíčku, ve kterém jsou balíčky nasazeny do systémů souborů, sdílených souborů nebo souborů Azure.
   
   Pokud zaškrtnete toto políčko, budete muset přenést svůj vlastní databázový server do hostitele SSISDB, který vytvoříme a spravujeme vaším jménem.
   
   b. Jako **Předplatné** vyberte předplatné Azure, které obsahuje váš databázový server pro hostování databáze SSISDB. 

   c. Jako **Umístění** vyberte umístění vašeho databázového serveru pro hostování databáze SSISDB. Doporučujeme vybrat stejné umístění, jako má vaše prostředí Integration Runtime.

   d. Jako **Koncový bod databázového serveru katalogu** vyberte koncový bod vašeho databázového serveru pro hostování databáze SSISDB. 
   
   V závislosti na vybraném databázovém serveru může být instance SSISDB vytvořená vaším jménem jako jediná databáze, jako součást elastického fondu nebo ve spravované instanci. Může být přístupný ve veřejné síti nebo prostřednictvím připojení k virtuální síti. Pokyny k výběru typu databázového serveru pro hostování SSISDB najdete v tématu [porovnání Azure SQL Database izolované databáze, elastického fondu a spravované instance](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance).   

   Pokud vyberete server Azure SQL Database s použitím koncových bodů služby virtuální sítě nebo spravované instance s privátním koncovým bodem pro hostování SSISDB, nebo pokud budete vyžadovat přístup k místním datům, aniž byste museli nakonfigurovat prostředí IR v místním prostředí, musíte se připojit k Azure-SSIS IR virtuální síť. Další informace najdete v tématu [vytvoření Azure-SSIS IR ve virtuální síti](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

   e. Zaškrtněte políčko pro výběr metody ověřování pro databázový server pro hostování SSISDB **použít ověřování AAD se spravovaným identitou pro váš server ADF** . Zvolíte ověřování SQL nebo ověřování Azure AD se spravovanou identitou pro vaši datovou továrnu.

   Pokud políčko zaškrtnete, budete muset přidat spravovanou identitu pro vaši datovou továrnu do skupiny Azure AD s přístupovými oprávněními k vašemu databázovému serveru. Další informace najdete v tématu [vytvoření Azure-SSIS IR s ověřováním Azure AD](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
   
   f. Jako **uživatelské jméno správce**zadejte uživatelské jméno pro ověřování SQL vašeho databázového serveru pro hostování SSISDB. 

   g. Jako **heslo správce**zadejte heslo pro ověřování SQL vašeho databázového serveru pro hostování SSISDB. 

   h. Pro **úroveň služby databáze katalogu**vyberte úroveň služby pro váš databázový server pro hostování SSISDB. Vyberte úroveň Basic, Standard nebo Premium nebo vyberte název elastického fondu.

   i. Vyberte **Test připojení**. Pokud je test úspěšný, vyberte **Další**. 

1. Na stránce **Upřesnit nastavení** proveďte následující kroky. 

   ![Upřesnit nastavení](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   a. Pro **maximální paralelní spouštění na uzel**vyberte maximální počet balíčků, které mají být spuštěny souběžně na jeden uzel v clusteru Integration runtime. Zobrazí se pouze podporované počty balíčků. Vyberte nízké číslo, pokud chcete použít více než jedno jádro pro spuštění jednoho velkého balíčku, který je náročné na výpočetní výkon nebo paměť. Vyberte vysoké číslo, pokud chcete spustit jeden nebo více malých balíčků v jednom jádru. 

   b. Pro **vlastní nastavení identifikátoru URI SAS kontejneru**můžete volitelně zadat identifikátor URI (URI) kontejneru úložiště objektů BLOB v Azure, ve kterém jsou uložené instalační skript a jeho přidružené soubory. Další informace najdete v tématu [vlastní nastavení pro Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).
   
   c. Vyberte virtuální síť, ve **které se má Azure-SSIS Integration runtime připojit, a povolte pomocí služby ADF vytvořit určité síťové prostředky** , abyste si zvolili, jestli se chcete připojit k prostředí Integration runtime k virtuální síti.

   Tuto možnost vyberte, pokud používáte server Azure SQL Database s koncovými body služby virtuální sítě nebo spravovanou instancí s privátním koncovým bodem pro hostování SSISDB nebo pokud požadujete přístup k místním datům bez konfigurace místního prostředí IR. Další informace najdete v tématu [vytvoření Azure-SSIS IR ve virtuální síti](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 
   
   d. Zaškrtněte políčko **nastavit Integration runtime v místním prostředí jako proxy pro Azure-SSIS Integration runtime** zaškrtávací políčko pro výběr, zda chcete pro Azure-SSIS IR nakonfigurovat prostředí IR v místním prostředí jako proxy. Další informace najdete v tématu [nastavení prostředí IR pro místní hostování jako proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis).   

1. Vyberte **Next** (Další). 

1. Na stránce **Souhrn** Zkontrolujte všechna nastavení zřizování, zajistěte záložku doporučené odkazy na dokumentaci a výběrem možnosti **Dokončit** spusťte vytváření prostředí Integration runtime. 

   > [!NOTE]
   > S výjimkou času vlastní instalace by tento proces měl skončit do 5 minut.
   >
   > Pokud používáte SSISDB, Služba Data Factory se připojí k vašemu databázovému serveru, aby připravil SSISDB. 
   > 
   > Když zřizujete Azure-SSIS IR, nainstaluje se také Distribuovatelný balíček a sada Azure Feature Pack pro SSIS. Tyto komponenty poskytují připojení k excelovým souborům, přístup k souborům a různým zdrojům dat Azure a navíc ke zdrojům dat, které jsou již podporovány v rámci integrovaných komponent. Informace o dalších součástech, které můžete nainstalovat, najdete v tématu [vlastní nastavení pro Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

1. V okně **Připojení** v případě potřeby přepněte na **Prostředí Integration Runtime**. Seznam můžete aktualizovat kliknutím na **Aktualizovat**. 

   ![Stav vytváření s tlačítkem Aktualizovat](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Pomocí odkazů ve sloupci **Akce** můžete prostředí Integration Runtime zastavit nebo spustit, upravit nebo odstranit. Pomocí posledního odkazu zobrazíte kód JSON pro prostředí Integration Runtime. Tlačítka pro úpravu a odstranění jsou aktivní, pouze když je prostředí IR zastavené. 

   ![Odkazy ve sloupci Akce](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png) 

## <a name="deploy-ssis-packages"></a>Nasazení balíčků SSIS

Pokud používáte SSISDB, můžete do něj nasadit balíčky a spouštět je na Azure-SSIS IR pomocí nástrojů SQL Server Data Tools nebo SQL Server Management Studio Tools. Tyto nástroje se připojují k vašemu databázovému serveru prostřednictvím koncového bodu serveru: 

- Pro Azure SQL Database Server s privátním koncovým bodem je formát koncového bodu serveru `<server name>.database.windows.net`.
- Pro spravovanou instanci s privátním koncovým bodem je `<server name>.<dns prefix>.database.windows.net`formát koncového bodu serveru.
- Pro spravovanou instanci s veřejným koncovým bodem je `<server name>.public.<dns prefix>.database.windows.net,3342`formát koncového bodu serveru. 

Pokud SSISDB nepoužíváte, můžete balíčky nasadit do systémů souborů, sdílených souborů nebo souborů Azure. Pak je můžete spustit na Azure-SSIS IR pomocí nástrojů příkazového řádku `dtinstall`, `dtutil`a `dtexec`. Další informace najdete v tématu [nasazení balíčků SSIS](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). 

V obou případech můžete na Azure-SSIS IR spustit i nasazené balíčky pomocí aktivity spustit balíček SSIS v Data Factorych kanálech. Další informace najdete v tématu [vyvolání spuštění balíčku SSIS jako aktivity první třídy Data Factory](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

Viz také následující dokumentace k SSIS: 

- [Nasazení, spuštění a monitorování balíčků SSIS v Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Připojení k SSISDB v Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Plánování spouštění balíčků v Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [Připojení k místním zdrojům dat s využitím ověřování systému Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Další kroky

Informace o přizpůsobení prostředí Azure-SSIS Integration Runtime najdete v následujícím článku: 

> [!div class="nextstepaction"]
> [Přizpůsobení Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)