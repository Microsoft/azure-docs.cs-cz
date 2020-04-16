---
title: Zřízení runtime integrace Azure-SSIS
description: Zjistěte, jak zřídit prostředí Azure-SSIS Integration Runtime ve službě Azure Data Factory, abyste v Azure mohli nasazovat a spouštět balíčky SSIS.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 12/23/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: 91962b578f9620384af86d25a33ba35830abb285
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418622"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Zřízení runtime integrace Azure-SSIS v Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Tento kurz obsahuje postup pro použití portálu Azure k zřízení modulu runtime integrace Azure-SQL Server Integration Services (SSIS) (IR) v Azure Data Factory. Azure-SSIS IR podporuje:

- Spouštění balíčků nasazených do katalogu SSIS (SSISDB) hostovaného serverem Azure SQL Database nebo spravovanou instancí (model nasazení projektu).
- Spouštění balíčků nasazených do systémů souborů, sdílených složek nebo souborů Azure (model nasazení balíčků). 

Po zřízení indičního zařízení Azure-SSIS můžete k nasazení a spuštění balíčků v Azure použít známé nástroje. Mezi tyto nástroje patří nástroje SQL Server Data Tools (SSDT), SQL `dtinstall`Server `dtutil`Management `dtexec`Studio (SSMS) a nástroje příkazového řádku jako , , a .

Informace o konceptu prostředí Azure-SSIS IR najdete v [přehledu prostředí Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime).

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Zřízení prostředí Azure-SSIS Integration Runtime

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Předplatné Azure**. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete.
- **Databázový server Azure SQL (volitelný)**. Pokud ještě nemáte databázový server, vytvořte si ho na webu Azure Portal před tím, než začnete. Data Factory zase vytvoří instanci SSISDB na tomto databázovém serveru. 

  Doporučujeme vytvořit databázový server ve stejné oblasti Azure jako prostředí Integration Runtime. Tato konfigurace umožňuje protokoly spuštění spuštění za běhu integrace do SSISDB bez křížení oblastí Azure.

  Mějte na paměti tyto body:

  - Na základě vybraného databázového serveru může být instance SSISDB vytvořena vaším jménem jako jedna databáze, jako součást elastického fondu nebo ve spravované instanci. Může být přístupná ve veřejné síti nebo připojením k virtuální síti. Pokyny při výběru typu databázového serveru pro hostování SSISDB najdete v [tématu Porovnání jedné databáze Azure SQL Database, elastického fondu a spravované instance](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance). 
  
    Pokud používáte server Azure SQL Database s koncovými body brány firewall IP nebo koncové body virtuální síťové služby nebo spravovanou instanci s privátním koncovým bodem k hostování SSISDB nebo pokud požadujete přístup k místním datům bez konfigurace samoobslužné infračervené kontroly, musíte připojit infračervený přenos Azure-SSIS k virtuální síti. Další informace najdete [v tématu Vytvoření Azure-SSIS IR ve virtuální síti](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

  - Zkontrolujte, zda je pro databázový server povoleno **povolit přístup ke službám Azure.** Toto nastavení se nepoužije, pokud k hostování SSISDB používáte server Azure SQL Database s pravidly brány firewall IP nebo koncovými body služby virtuální sítě nebo spravovanou instanci s privátním koncovým bodem. Další informace najdete v tématu [Zabezpečení databáze Azure SQL](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Chcete-li toto nastavení povolit pomocí prostředí PowerShell, přečtěte si [téma New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).

  - Přidejte adresu IP klientského počítače nebo rozsah adres IP, která obsahuje adresu IP klientského počítače, do seznamu adres IP klienta v nastavení brány firewall pro databázový server. Další informace najdete v tématu [Pravidla brány firewall na úrovni serveru a databáze služby Azure SQL Database](../sql-database/sql-database-firewall-configure.md).

  - K databázovému serveru se můžete připojit pomocí ověřování SQL s přihlašovacími údaji správce serveru nebo pomocí ověřování Azure AD se spravovanou identitou pro vaši datovou továrnu. Pro druhé je třeba přidat spravovanou identitu pro vaši datovou továrnu do skupiny Azure AD s přístupovými oprávněními k databázovému serveru. Další informace najdete [v tématu Vytvoření Azure-SSIS IR s ověřováním Azure AD](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

  - Zkontrolujte, zda databázový server již nemá instanci SSISDB. Zřizování Azure-SSIS IR nepodporuje použití existující instance SSISDB.


> [!NOTE]
> Seznam oblastí Azure, ve kterých jsou data factory a Azure-SSIS Ir momentálně k dispozici, najdete v [tématu Data Factory a SSIS Ir dostupnost podle oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

Pokud chcete vytvořit svou datovou továrnu na webu Azure Portal, postupujte podle podrobných pokynů v části [Vytvoření datové továrny prostřednictvím ui](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory). Vyberte **Připnout na řídicí panel,** abyste po svém vytvoření povolili rychlý přístup. 

Po vytvoření datové továrny otevřete jeho stránku s přehledem na webu Azure Portal. Výběrem dlaždice **Autor & monitor** otevřete stránku **Začínáme** na samostatné kartě. Zde můžete pokračovat ve vytváření azure-SSIS IR.

## <a name="create-an-azure-ssis-integration-runtime"></a>Vytvoření prostředí Azure-SSIS Integration Runtime

### <a name="from-the-data-factory-overview"></a>Z přehledu Data Factory

1. Na stránce **Začínáme** vyberte dlaždici **Konfigurace prostředí SSIS Integration Runtime**. 

   ![Dlaždice Konfigurace prostředí SSIS Integration Runtime](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Zbývající kroky nastavení prostředí Azure-SSIS IR najdete v části [Zřízení prostředí Azure-SSIS Integration Runtime](#provision-an-azure-ssis-integration-runtime). 

### <a name="from-the-authoring-ui"></a>Z vývojového ui

1. V uzu Azure Data Factory přepněte na kartu **Úpravy** a vyberte **Připojení**. Pak přepněte na kartu **Integrační runtimes** a zobrazte existující integrační runtimes v datové továrně. 

   ![Výběr možností za účelem zobrazení stávajících prostředí IR](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Výběrem možnosti **Nové** vytvořte nové prostředí Azure-SSIS IR. 

   ![Prostředí Integration Runtime prostřednictvím nabídky](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. V panelu **Nastavení prostředí Integration Runtime** vyberte **existující balíčky SSIS, které chcete provést v dlaždici Azure, a** pak vyberte **Další**. 

   ![Zadání typu prostředí Integration Runtime](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Zbývající kroky nastavení prostředí Azure-SSIS IR najdete v části [Zřízení prostředí Azure-SSIS Integration Runtime](#provision-an-azure-ssis-integration-runtime). 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Zřízení prostředí Azure-SSIS Integration Runtime

1. V části **Obecné nastavení** panelu Instalace **prostředí integrace** proveďte následující kroky. 

   ![Obecná nastavení](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. Jako **Název** zadejte název vašeho prostředí Integration Runtime. 

   1. Jako **Popis** zadejte popis vašeho prostředí Integration Runtime. 

   1. Jako **Umístění** vyberte umístění vašeho prostředí Integration Runtime. Zobrazí se pouze podporovaná umístění. Doporučujeme vybrat stejné umístění, jako má váš databázový server pro hostování databáze SSISDB. 

   1. Jako **Velikost uzlu** vyberte velikost uzlu ve vašem clusteru Integration Runtime. Zobrazí se pouze podporované velikosti uzlů. Pokud chcete spustit mnoho balíčků náročných na výpočetní prostředky nebo na paměť, vyberte velikost velkého uzlu (navýšit kapacitu). 

   1. Jako **Počet uzlů** vyberte počet uzlů ve vašem clusteru Integration Runtime. Zobrazí se pouze podporované počty uzlů. Vyberte velký cluster s mnoha uzly (horizontální navýšení kapacity), pokud chcete spustit mnoho balíčků paralelně. 

   1. V **případě edice/licence**vyberte edici SQL Serveru pro modul runtime integrace: Standard nebo Enterprise. Pokud chcete v integračním běhu používat pokročilé funkce, vyberte Enterprise. 

   1. V **části Ušetřete peníze**vyberte možnost Hybridní výhoda Azure pro prostředí integrace: **Ano** nebo **Ne**. Pokud chcete přinést vlastní licenci SQL Serveru s programem Software Assurance, vyberte **ano,** abyste měli prospěch z úspor nákladů při hybridním použití. 

   1. Vyberte **Další**. 

1. V části **Nastavení SQL** proveďte následující kroky. 

   ![Nastavení SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   1. Zaškrtněte políčko **Vytvořit katalog SSIS (SSISDB) hostovaný serverem Azure SQL Database/spravovanou instanci a uložte projekty/balíčky/prostředí/protokoly spuštění** a vyberte model nasazení pro balíčky, které se mají spouštět na zařízení Azure-SSIS IR. Zvolíte buď model nasazení projektu, kde jsou balíčky nasazeny do SSISDB hostované databázovým serverem, nebo model nasazení balíčku, kde se balíčky nasazují do systémů souborů, sdílených složek nebo souborů Azure.
   
      Pokud zaškrtnete políčko, budete muset přinést svůj vlastní databázový server pro hostování SSISDB, který vytvoříme a spravujeme vaším jménem.
   
      1. Jako **Předplatné** vyberte předplatné Azure, které obsahuje váš databázový server pro hostování databáze SSISDB. 

      1. Jako **Umístění** vyberte umístění vašeho databázového serveru pro hostování databáze SSISDB. Doporučujeme vybrat stejné umístění, jako má vaše prostředí Integration Runtime.

      1. Jako **Koncový bod databázového serveru katalogu** vyberte koncový bod vašeho databázového serveru pro hostování databáze SSISDB. 
   
         Na základě vybraného databázového serveru může být instance SSISDB vytvořena vaším jménem jako jedna databáze, jako součást elastického fondu nebo ve spravované instanci. Může být přístupná ve veřejné síti nebo připojením k virtuální síti. Pokyny při výběru typu databázového serveru pro hostování SSISDB najdete v [tématu Porovnání jedné databáze Azure SQL Database, elastického fondu a spravované instance](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance).   

         Pokud vyberete server Azure SQL Database s pravidly brány FIREWALL IP nebo koncovými body virtuální síťové služby nebo spravovanou instanci s privátním koncovým bodem pro hostování SSISDB, nebo pokud požadujete přístup k místním datům bez konfigurace samoobslužné infračervené kontroly, musíte připojit infračervený přenos Azure-SSIS k virtuální síti. Další informace najdete [v tématu Vytvoření Azure-SSIS IR ve virtuální síti](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

      1. Zaškrtněte **políčko Použít ověřování AAD se spravovanou identitou pro adf** a zvolte metodu ověřování pro databázový server pro hostování SSISDB. Zvolíte ověřování SQL nebo ověřování Azure AD se spravovanou identitou pro vaši datovou továrnu.

         Pokud zaškrtnete políčko, budete muset přidat spravovanou identitu pro vaši datovou továrnu do skupiny Azure AD s přístupovými oprávněními k databázovému serveru. Další informace najdete [v tématu Vytvoření Azure-SSIS IR s ověřováním Azure AD](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).
   
      1. Do **pole Admin Username**zadejte uživatelské jméno pro ověřování SQL databázového serveru pro hostování sisisdb. 

      1. V **případě hesla správce**zadejte heslo pro ověřování SQL pro databázový server, které bude hostovat sisisdb. 

      1. Pro **úroveň databázové služby katalogu**vyberte úroveň služby pro databázový server, která bude hostovat SSISDB. Vyberte úroveň Basic, Standard nebo Premium nebo vyberte název elastického fondu.

      1. Vyberte **možnost Testovat připojení**. Pokud je test úspěšný, vyberte **další**. 

1. V části **Upřesnit nastavení** proveďte následující kroky. 

   ![Upřesnit nastavení](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. Pro **maximální paralelní spuštění na uzel**vyberte maximální počet balíčků, které mají být spuštěny souběžně na uzel v clusteru integračního běhu. Zobrazí se pouze podporované počty balíčků. Vyberte nízké číslo, pokud chcete použít více než jedno jádro ke spuštění jednoho velkého balíčku, který je náročný na výpočetní prostředky nebo na paměť. Pokud chcete spustit jeden nebo více malých balíčků v jednom jádru, vyberte vysoké číslo. 

   1. Zaškrtněte políčko **Přizpůsobit runtime integrace Azure-SSIS s dalšími konfiguracemi/instalacemi součástí systému** a zvolte, jestli chcete do zařízení Azure-SSIS IR přidat standardní/expresní vlastní nastavení. Další informace naleznete [v tématu Vlastní nastavení pro Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).
   
   1. Zaškrtněte políčko **Vybrat virtuální síť pro prostředí Integrace Azure-SSIS, chcete-li se připojit, povolte službě ADF vytvářet určité síťové prostředky a volitelně přenesete vlastní statické veřejné IP adresy** a zvolte, jestli chcete připojit infračervený přenos Azure-SSIS k virtuální síti.

      Vyberte ji, pokud používáte server Azure SQL Database s koncovými body brány firewall IP nebo koncovými body služby virtuální sítě nebo spravovanou instanci s privátním koncovým bodem k hostování SSISDB, nebo pokud požadujete přístup k místním datům bez konfigurace samoobslužné infračervené kontroly. Další informace najdete [v tématu Vytvoření Azure-SSIS IR ve virtuální síti](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). 
   
   1. Zaškrtněte **políčko Nastavit runtime integrace s vlastním hostitelem jako proxy pro prostředí Runtime integrace Azure-SSIS** a zvolte, jestli chcete nakonfigurovat infračervený přenos hostovaný na vlastním základě jako proxy pro zařízení Azure-SSIS IR. Další informace naleznete [v tématu Nastavení infračerveného serveru s vlastním hostitelem jako proxy serveru](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis).   

   1. Vyberte **Pokračovat**. 

1. V části **Souhrn** zkontrolujte všechna nastavení zřizování, přidejte do záložek doporučené odkazy na dokumentaci a vyberte **Dokončit** a spusťte vytváření integračního běhu. 

   > [!NOTE]
   > S výjimkou vlastní čas nastavení, tento proces by měl být dokončen do 5 minut.
   >
   > Pokud používáte SSISDB, služba Data Factory se připojí k databázovému serveru k přípravě SSISDB. 
   > 
   > Když zřídíte Azure-SSIS IR, nainstalované jsou taky Access Redistributable a Azure Feature Pack pro SSIS. Tyto součásti poskytují připojení k souborům aplikace Excel, souborům aplikace Access a různým zdrojům dat Azure, kromě zdrojů dat, které integrované součásti již podporují. Informace o dalších součástech, které můžete nainstalovat, naleznete [v tématu Vlastní nastavení pro Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

1. V okně **Připojení** v případě potřeby přepněte na **Prostředí Integration Runtime**. Seznam můžete aktualizovat kliknutím na **Aktualizovat**. 

   ![Stav vytváření s tlačítkem Aktualizovat](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Pomocí odkazů ve sloupci **Akce** můžete prostředí Integration Runtime zastavit nebo spustit, upravit nebo odstranit. Pomocí posledního odkazu zobrazíte kód JSON pro prostředí Integration Runtime. Tlačítka pro úpravu a odstranění jsou aktivní, pouze když je prostředí IR zastavené. 

   ![Odkazy ve sloupci Akce](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png) 

## <a name="deploy-ssis-packages"></a>Nasazení balíčků SSIS

Pokud používáte SSISDB, můžete nasadit balíčky do něj a spustit je na Azure-SSIS IR pomocí SQL Server Datové nástroje (SSDT) nebo SQL Server Management Studio (SSMS) nástroje. Tyto nástroje se připojují k databázovému serveru prostřednictvím koncového bodu serveru: 

- Pro server Azure SQL Database je `<server name>.database.windows.net`formát koncového bodu serveru .
- Pro spravovanou instanci s privátním koncovým bodem je `<server name>.<dns prefix>.database.windows.net`formát koncového bodu serveru .
- Pro spravovanou instanci s veřejným koncovým bodem je `<server name>.public.<dns prefix>.database.windows.net,3342`formát koncového bodu serveru . 

Pokud nepoužíváte SSISDB, můžete nasadit balíčky do systémů souborů, sdílených složek nebo souborů Azure a `dtinstall`spustit `dtutil`je `dtexec` na Azure-SSIS IR pomocí nástrojů , a příkazového řádku. Další informace naleznete v [tématu Deploy SSIS packages](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). 

V obou případech můžete také spustit nasazené balíčky na Azure-SSIS IR pomocí spustit aktivitu balíčku SSIS v kanálech datové továrny. Další informace naleznete [v tématu Vyvolání spuštění balíčku SSIS jako prvotřídní aktivity data factory](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

Viz také následující dokumentace SSIS: 

- [Nasazení, spouštění a monitorování balíčků SSIS v Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Připojení k SSISDB v Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Plánování spuštění balíčků v Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [Připojení k místním zdrojům dat s využitím ověřování systému Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Další kroky

Informace o přizpůsobení prostředí Azure-SSIS Integration Runtime najdete v následujícím článku: 

> [!div class="nextstepaction"]
> [Přizpůsobení infračerveného systému Azure-SSIS](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)