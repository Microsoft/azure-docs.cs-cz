---
title: Vytvoření runtime integrace Azure-SSIS v Azure Data Factory
description: Zjistěte, jak vytvořit runtime integrace Azure-SSIS v Azure Data Factory, abyste mohli nasadit a spouštět balíčky SSIS v Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/27/2020
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: 18555fbffbc48594793163894c010998094b3b59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336227"
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Vytvoření runtime integrace Azure-SSIS v Azure Data Factory

Tento článek obsahuje kroky pro zřizování modulu runtime integrace Azure-SQL Server Integration Services (SSIS) (IR) v Azure Data Factory. Azure-SSIS IR podporuje:

- Spouštění balíčků nasazených do katalogu SSIS (SSISDB) hostovaného serverem Azure SQL Database nebo spravovanou instancí (model nasazení projektu).
- Spouštění balíčků nasazených do systémů souborů, sdílených složek nebo souborů Azure (model nasazení balíčků). 

Po zřízení indičního zařízení Azure-SSIS můžete k nasazení a spuštění balíčků v Azure použít známé nástroje. Mezi tyto nástroje patří nástroje SQL Server Data Tools (SSDT), SQL `dtinstall`Server `dtutil`Management `dtexec`Studio (SSMS) a nástroje příkazového řádku jako , , a .

[Zřizování Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md) kurz ukazuje, jak vytvořit Azure-SSIS IR prostřednictvím portálu Azure nebo data factory aplikace. Kurz také ukazuje, jak volitelně použít server Azure SQL Database nebo spravovanou instanci k hostování SSISDB. Tento článek rozšiřuje kurz a popisuje, jak provést tyto volitelné úkoly:

- K hostování SSISDB použijte server Azure SQL Database s pravidly brány firewall IP nebo koncovými body služby virtuální sítě nebo spravovanou instanci s privátním koncovým bodem. Jako předpoklad je potřeba nakonfigurovat oprávnění a nastavení virtuální sítě pro azure-SSIS IR pro připojení k virtuální síti.

- Použijte ověřování Azure Active Directory (Azure AD) se spravovanou identitou pro vaši datovou továrnu k připojení k serveru Azure SQL Database nebo spravované instanci. Jako předpoklad je třeba přidat spravovanou identitu pro vaši datovou továrnu jako uživatele databáze, který může vytvořit instanci SSISDB.

- Připojte svůj Azure-SSIS IR do virtuální sítě nebo nakonfigurujte infračervený přenos hostovaný s vlastním hostitelem jako proxy pro zařízení Azure-SSIS IR pro přístup k datům místně.

Tento článek ukazuje, jak zřídit Azure-SSIS IR pomocí portálu Azure, Azure PowerShell a šablony Azure Resource Manager.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Předplatné Azure**. Pokud ještě nemáte předplatné, můžete si vytvořit [bezplatný zkušební](https://azure.microsoft.com/pricing/free-trial/) účet.

- **Server Azure SQL Database nebo spravovaná instance (volitelná).** Pokud ještě nemáte databázový server, vytvořte si ho na webu Azure Portal před tím, než začnete. Data Factory zase vytvoří instanci SSISDB na tomto databázovém serveru. 

  Doporučujeme vytvořit databázový server ve stejné oblasti Azure jako prostředí Integration Runtime. Tato konfigurace umožňuje protokoly spuštění spuštění za běhu integrace do SSISDB bez křížení oblastí Azure.

  Mějte na paměti tyto body:

  - Na základě vybraného databázového serveru může být instance SSISDB vytvořena vaším jménem jako jedna databáze, jako součást elastického fondu nebo ve spravované instanci. Může být přístupná ve veřejné síti nebo připojením k virtuální síti. Pokyny při výběru typu databázového serveru pro hostování SSISDB najdete v [tématu Porovnání jedné databáze Azure SQL Database, elastického fondu a spravované instance](#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance) části v tomto článku. 
  
    Pokud používáte server Azure SQL Database s koncovými body brány firewall IP nebo koncové body virtuální síťové služby nebo spravovanou instanci s privátním koncovým bodem k hostování SSISDB nebo pokud požadujete přístup k místním datům bez konfigurace samoobslužné infračervené kontroly, musíte připojit infračervený přenos Azure-SSIS k virtuální síti. Další informace najdete [v tématu Připojení azure-SSIS IR k virtuální síti](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

  - Zkontrolujte, zda je pro databázový server povoleno **povolit přístup ke službám Azure.** Toto nastavení se nepoužije, pokud k hostování SSISDB používáte server Azure SQL Database s pravidly brány firewall IP nebo koncovými body služby virtuální sítě nebo spravovanou instanci s privátním koncovým bodem. Další informace najdete v tématu [Zabezpečení databáze Azure SQL](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Chcete-li toto nastavení povolit pomocí prostředí PowerShell, přečtěte si [téma New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).

  - Přidejte adresu IP klientského počítače nebo rozsah adres IP, která obsahuje adresu IP klientského počítače, do seznamu adres IP klienta v nastavení brány firewall pro databázový server. Další informace najdete v tématu [Pravidla brány firewall na úrovni serveru a databáze služby Azure SQL Database](../sql-database/sql-database-firewall-configure.md).

  - K databázovému serveru se můžete připojit pomocí ověřování SQL s přihlašovacími údaji správce serveru nebo pomocí ověřování Azure AD se spravovanou identitou pro vaši datovou továrnu. Pro druhé je třeba přidat spravovanou identitu pro vaši datovou továrnu do skupiny Azure AD s přístupovými oprávněními k databázovému serveru. Další informace najdete [v tématu Povolení ověřování Azure AD pro Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir).

  - Zkontrolujte, zda databázový server již nemá instanci SSISDB. Zřizování Azure-SSIS IR nepodporuje použití existující instance SSISDB.

- **Virtuální síť Azure Resource Manager (volitelně).** Pokud platí alespoň jedna z následujících podmínek, musíte mít virtuální síť Azure Resource Manageru:
  - Hostujete SSISDB na serveru Azure SQL Database s koncovými body brány firewall IP nebo koncovými body služby virtuální sítě nebo spravovanou instancí s privátním koncovým bodem.
  - Chcete se připojit k místním úložištím dat z balíčků SSIS spuštěných na zařízení Azure-SSIS IR bez konfigurace infračerveného počítače s vlastním hostitelem.

- **Azure PowerShell (volitelné)**. Postupujte podle pokynů v [části Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/install-az-ps), pokud chcete spustit skript PowerShellu pro zřízení indičního zařízení Azure-SSIS.

### <a name="regional-support"></a>Místní podpora

Seznam oblastí Azure, ve kterých jsou data factory a Azure-SSIS Ir k dispozici, najdete v [tématu Data Factory a SSIS Ir dostupnost podle oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all).

### <a name="comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance"></a>Porovnání jedné databáze SQL, elastického fondu a spravované instance

Následující tabulka porovnává některé funkce serveru Azure SQL Database a spravované instance, které se vztahují k Azure-SSIR IR:

| Funkce | Jedna databáze/elastický fond| Spravovaná instance |
|---------|--------------|------------------|
| **Plánování** | Agent serveru SQL Server není k dispozici.<br/><br/>Viz [Naplánování spuštění balíčku v kanálu datové továrny](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity).| Agent spravované instance je k dispozici. |
| **Ověřování** | Můžete vytvořit instanci SSISDB s uživatelem obsažené databáze, který představuje libovolnou skupinu Azure AD se spravovanou identitou vaší datové továrny jako člen v **roli db_owner.**<br/><br/>V [tématu Povolení ověřování Azure AD k vytvoření instance SSISDB na databázovém serveru Azure SQL](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | Můžete vytvořit instanci SSISDB s uživatelem obsažené databáze, který představuje spravovanou identitu vaší datové továrny. <br/><br/>V [tématu Povolení ověřování Azure AD k vytvoření instance SSISDB ve spravované instanci Azure SQL Database](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance). |
| **Úroveň služeb** | Když vytvoříte infračervený přenos Azure-SSIS s vaším serverem Azure SQL Database, můžete vybrat úroveň služby pro SSISDB. Existuje více úrovní služby. | Když vytvoříte Indiční reziduum Azure-SSIS se spravovanou instancí, nemůžete vybrat úroveň služeb pro SSISDB. Všechny databáze ve spravované instanci sdílejí stejný prostředek přidělený této instanci. |
| **Virtuální síť** | Vaše Indiční služba Azure-SSIS IR se může připojit k virtuální síti Azure Resource Manager, pokud používáte server Azure SQL Database s koncovými body brány firewall IP nebo koncových bodů virtuální síťové služby. | Vaše Azure-SSIS IR se může připojit k virtuální síti Azure Resource Manager, pokud používáte spravovanou instanci s privátním koncovým bodem. Virtuální síť je vyžadována, pokud nepovolíte veřejný koncový bod pro spravovanou instanci.<br/><br/>Pokud připojíte infračervený přenos Azure-SSIS do stejné virtuální sítě jako spravovaná instance, ujistěte se, že vaše zařízení Azure-SSIS IR je v jiné podsíti než vaše spravovaná instance. Pokud připojíte azure-SSIS IR do jiné virtuální sítě než spravované instance, doporučujeme buď partnerský vztah virtuální sítě nebo připojení k síti k síti. Viz [Připojení aplikace ke spravované instanci Azure SQL Database](../sql-database/sql-database-managed-instance-connect-app.md). |
| **Distribuované transakce** | Tato funkce je podporována prostřednictvím elastických transakcí. Transakce koordinátora distribuovaných transakcí (MSDTC) společnosti Microsoft nejsou podporovány. Pokud vaše balíčky SSIS používají msdtc ke koordinaci distribuovaných transakcí, zvažte migraci na elastické transakce pro Azure SQL Database. Další informace naleznete [v tématu Distribuované transakce napříč cloudovými databázemi](../sql-database/sql-database-elastic-transactions-overview.md). | Není podporováno. |
| | | |

## <a name="use-the-azure-portal-to-create-an-integration-runtime"></a>Vytvoření integračního běhu pomocí portálu Azure

V této části použijete portál Azure, konkrétně uživatelské rozhraní datové továrny (UI) nebo aplikaci, k vytvoření zařízení Azure-SSIS IR.

### <a name="create-a-data-factory"></a>Vytvoření datové továrny

Pokud chcete vytvořit svou datovou továrnu na webu Azure Portal, postupujte podle podrobných pokynů v části [Vytvoření datové továrny prostřednictvím ui](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory). Vyberte **Připnout na řídicí panel,** abyste po svém vytvoření povolili rychlý přístup. 

Po vytvoření datové továrny otevřete jeho stránku s přehledem na webu Azure Portal. Vyberte dlaždici **& monitor u autora** a otevřete stránku **Let's Started** na samostatné kartě. Zde můžete pokračovat ve vytváření azure-SSIS IR.   

### <a name="provision-an-azure-ssis-integration-runtime"></a>Zřízení prostředí Azure-SSIS Integration Runtime

1. Na stránce **Začínáme** vyberte dlaždici **Konfigurace prostředí SSIS Integration Runtime**.

   ![Dlaždice Konfigurace prostředí SSIS Integration Runtime](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. V části **Obecné nastavení** panelu Instalace **prostředí integrace** proveďte následující kroky.

   ![Obecná nastavení](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. Jako **Název** zadejte název vašeho prostředí Integration Runtime.

   1. Jako **Popis** zadejte popis vašeho prostředí Integration Runtime.

   1. Jako **Umístění** vyberte umístění vašeho prostředí Integration Runtime. Zobrazí se pouze podporovaná umístění. Doporučujeme vybrat stejné umístění, jako má váš databázový server pro hostování databáze SSISDB.

   1. V části **Velikost uzlu**vyberte velikost uzlu v integračním runtime clusteru. Zobrazí se pouze podporované velikosti uzlů. Pokud chcete spustit mnoho balíčků náročných na výpočetní prostředky nebo na paměť, vyberte velikost velkého uzlu (navýšit kapacitu).

   1. Jako **Počet uzlů** vyberte počet uzlů ve vašem clusteru Integration Runtime. Zobrazí se pouze podporované počty uzlů. Vyberte velký cluster s mnoha uzly (horizontální navýšení kapacity), pokud chcete spustit mnoho balíčků paralelně.

   1. V **případě edice/licence**vyberte edici SQL Serveru pro modul runtime integrace: Standard nebo Enterprise. Pokud chcete v integračním běhu používat pokročilé funkce, vyberte Enterprise.

   1. V **části Ušetřete peníze**vyberte možnost Hybridní výhoda Azure pro prostředí integrace: **Ano** nebo **Ne**. Pokud chcete přinést vlastní licenci SQL Serveru s programem Software Assurance, vyberte **ano,** abyste měli prospěch z úspor nákladů při hybridním použití.

   1. Vyberte **další**.

1. V části **Nastavení SQL** proveďte následující kroky.

   ![Nastavení SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   1. Zaškrtněte políčko **Vytvořit katalog SSIS (SSISDB) hostovaný serverem Azure SQL Database/spravovanou instanci a uložte projekty/balíčky/prostředí/protokoly spuštění** a vyberte model nasazení pro balíčky, které se mají spouštět na zařízení Azure-SSIS IR. Zvolíte buď model nasazení projektu, kde jsou balíčky nasazeny do SSISDB hostované databázovým serverem, nebo model nasazení balíčku, kde se balíčky nasazují do systémů souborů, sdílených složek nebo souborů Azure. 
    
      Pokud zaškrtnete toto políčko, budete muset přinést vlastní databázový server, který bude hostovat instanci SSISDB, kterou vytvoříme a spravujeme vaším jménem.
   
      1. Jako **Předplatné** vyberte předplatné Azure, které obsahuje váš databázový server pro hostování databáze SSISDB. 

      1. Jako **Umístění** vyberte umístění vašeho databázového serveru pro hostování databáze SSISDB. Doporučujeme vybrat stejné umístění, jako má vaše prostředí Integration Runtime. 

      1. Jako **Koncový bod databázového serveru katalogu** vyberte koncový bod vašeho databázového serveru pro hostování databáze SSISDB. 
    
         Na základě vybraného databázového serveru může být instance SSISDB vytvořena vaším jménem jako jedna databáze, jako součást elastického fondu nebo ve spravované instanci. Může být přístupná ve veřejné síti nebo připojením k virtuální síti. Pokyny při výběru typu databázového serveru pro hostování SSISDB najdete v [tématu Porovnání jedné databáze Azure SQL Database, elastického fondu a spravované instance](#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance) části v tomto článku. 
    
         Pokud vyberete server Azure SQL Database s pravidly brány FIREWALL IP nebo koncovými body virtuální síťové služby nebo spravovanou instanci s privátním koncovým bodem pro hostování SSISDB, nebo pokud požadujete přístup k místním datům bez konfigurace samoobslužné infračervené kontroly, musíte připojit infračervený přenos Azure-SSIS k virtuální síti. Další informace najdete [v tématu Připojení azure-SSIS IR k virtuální síti](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

      1. Zaškrtněte **políčko Použít ověřování AAD se spravovanou identitou pro adf** a zvolte metodu ověřování pro databázový server pro hostování SSISDB. Zvolíte ověřování SQL nebo ověřování Azure AD se spravovanou identitou pro vaši datovou továrnu. 
    
         Pokud zaškrtnete políčko, budete muset přidat spravovanou identitu pro vaši datovou továrnu do skupiny Azure AD s přístupovými oprávněními k databázovému serveru. Další informace najdete [v tématu Povolení ověřování Azure AD pro Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). 

      1. Do **pole Admin Username**zadejte uživatelské jméno pro ověřování SQL databázového serveru pro hostování sisisdb. 

      1. V **případě hesla správce**zadejte heslo pro ověřování SQL pro databázový server, které bude hostovat sisisdb. 

      1. Pro **úroveň databázové služby katalogu**vyberte úroveň služby pro databázový server, která bude hostovat SSISDB. Vyberte úroveň Basic, Standard nebo Premium nebo vyberte název elastického fondu. 

      1. Vyberte **možnost Testovat připojení**. Pokud je test úspěšný, vyberte **další**. 

1. V části **Upřesnit nastavení** proveďte následující kroky.

   ![Upřesnit nastavení](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. Pro **maximální paralelní spuštění na uzel**vyberte maximální počet balíčků, které mají být spuštěny souběžně na uzel v clusteru integračního běhu. Zobrazí se pouze podporované počty balíčků. Vyberte nízké číslo, pokud chcete použít více než jedno jádro ke spuštění jednoho velkého balíčku, který je náročný na výpočetní prostředky nebo na paměť. Pokud chcete spustit jeden nebo více malých balíčků v jednom jádru, vyberte vysoké číslo.

   1. Zaškrtněte políčko **Přizpůsobit runtime integrace Azure-SSIS s dalšími konfiguracemi/instalacemi součástí systému** a zvolte, jestli chcete do zařízení Azure-SSIS IR přidat standardní/expresní vlastní nastavení. Další informace naleznete [v tématu Vlastní nastavení pro Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

      Pokud toto políčko zaškrtnete, proveďte následující kroky.

      ![Upřesňující nastavení s vlastními nastaveními](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)
   
      1. Do **pole Vlastní instalační kontejner SAS URI**zadejte identifikátor URI SAS kontejneru, do kterého ukládáte skripty a přidružené soubory pro standardní vlastní nastavení.

      1. V **části Expresní vlastní nastavení**vyberte **Nový,** chcete-li otevřít panel **Přidat expresní vlastní nastavení** a potom v rozevírací nabídce Express custom setup **type,** například **Spustit příkaz cmdkey**, **Přidat proměnnou prostředí**, Nainstalovat **licencovanou komponentu**atd.

         Pokud vyberete typ **licencované součásti Instalovat,** můžete v rozevírací nabídce **Název součásti** vybrat všechny integrované součásti od našich partnerů pro vlastní dodavatele softwaru a v případě potřeby zadat licenční klíč produktu, který jste od nich zakoupili, do pole **Licenční klíč.**
  
         Přidaná expresní vlastní nastavení se zobrazí v části **Upřesnit nastavení.** Chcete-li je odebrat, můžete zaškrtnout jejich políčka a potom vybrat **možnost Odstranit**.

   1. Zaškrtněte políčko **Vybrat virtuální síť pro prostředí Runtime integrace Azure-SSIS, chcete-li se připojit, povolte adf k vytvoření určitých síťových prostředků a volitelně přineste vlastní statické veřejné IP adresy** a zvolte, jestli chcete připojit integrační runtime do virtuální sítě. 

      Vyberte ji, pokud používáte server Azure SQL Database s koncovými body brány firewall IP nebo koncové body služby virtuální sítě nebo spravovanou instanci s privátním koncovým bodem k hostování SSISDB, nebo pokud požadujete přístup k místním datům (to znamená, že máte místní zdroje dat nebo cíle v balíčcích SSIS) bez konfigurace samoobslužné infračerveného zařízení. Další informace najdete [v tématu Připojení azure-SSIS IR k virtuální síti](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

      Pokud toto políčko zaškrtnete, proveďte následující kroky.

      ![Rozšířené nastavení virtuální sítě](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

      1. V **části Předplatné**vyberte předplatné Azure, které má vaši virtuální síť.

      1. V **části Umístění**je vybráno stejné umístění integračního běhu.

      1. V **části Typ**vyberte typ virtuální sítě: klasický nebo Správce prostředků Azure. Doporučujeme vybrat virtuální síť Azure Resource Manager, protože klasické virtuální sítě se brzy zastaralá.

      1. V **části Název virtuální sítě**vyberte název virtuální sítě. Měl by to být stejný, který se používá pro váš server Azure SQL Database s koncovými body služby virtuální sítě nebo spravovanou instancí s privátním koncovým bodem pro hostování SSISDB. Nebo by to mělo být stejné připojení k místní síti. V opačném případě může být libovolná virtuální síť, která přinese vlastní statické veřejné IP adresy pro Azure-SSIS IR.

      1. V **části Název podsítě**vyberte název podsítě pro virtuální síť. Měl by být stejný jako ten, který se používá pro váš server Azure SQL Database s koncovými body služby virtuální sítě pro hostování SSISDB. Nebo by měla být jiná podsíť než ta, která se používá pro vaši spravovanou instanci s privátním koncovým bodem pro hostování SSISDB. V opačném případě může být libovolná podsíť pro přenos vlastních statických veřejných IP adres pro Azure-SSIS IR.

      1. Zaškrtněte políčko **Bring static public IP addresses for your Azure-SSIS Integration Runtime** a vyberte, jestli chcete přenést vlastní statické veřejné IP adresy pro Azure-SSIS IR, abyste je mohli povolit na bráně firewall pro vaše zdroje dat.

         Pokud toto políčko zaškrtnete, proveďte následující kroky.

         1. Pro **první statickou veřejnou IP adresu**vyberte první statickou veřejnou IP adresu, která splňuje požadavky na zařízení Azure-SSIS IR. Pokud žádné nemáte, klikněte na **Vytvořit nový** odkaz, abyste vytvořili statické veřejné IP adresy na webu Azure Portal, a potom klikněte na tlačítko aktualizovat tady, abyste je mohli vybrat.
      
         1. Pro **druhou statickou veřejnou IP adresu**vyberte druhou statickou veřejnou IP adresu, která splňuje požadavky na infračervený přenos Azure-SSIS. Pokud žádné nemáte, klikněte na **Vytvořit nový** odkaz, abyste vytvořili statické veřejné IP adresy na webu Azure Portal, a potom klikněte na tlačítko aktualizovat tady, abyste je mohli vybrat.

   1. Zaškrtněte **políčko Nastavit runtime integrace s vlastním hostitelem jako proxy pro prostředí Runtime integrace Azure-SSIS** a zvolte, jestli chcete nakonfigurovat infračervený přenos hostovaný na vlastním základě jako proxy pro zařízení Azure-SSIS IR. Další informace naleznete [v tématu Nastavení infračerveného serveru s vlastním hostitelem jako proxy serveru](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). 

      Pokud toto políčko zaškrtnete, proveďte následující kroky.

      ![Pokročilá nastavení s infračerveným přenosem s vlastním hostitelem](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

      1. V **případě prostředí Runtime integrace s vlastním hostitelem**vyberte existující infračervený přenos hostovaný na vlastním základě jako proxy server pro azure-SSIS IR.

      1. Pro **propojenou službu pracovního úložiště**vyberte stávající propojenou službu úložiště Azure blob nebo vytvořte novou pro pracovní.

      1. Pro **pracovní cestu**zadejte kontejner objektů blob ve vybraném účtu úložiště objektů blob Azure nebo ho ponechte prázdné, aby se použil oteplit výchozí kontejner pro pracovní.

   1. Vyberte **pokračovat ve ověření** > virtuální**sítě**. 

1. V části **Souhrn** zkontrolujte všechna nastavení zřizování, přidejte do záložek doporučené odkazy na dokumentaci a vyberte **Dokončit** a spusťte vytváření integračního běhu.

   > [!NOTE]
   > S výjimkou vlastní čas nastavení, tento proces by měl být dokončen do 5 minut. Ale může trvat 20 až 30 minut pro Azure-SSIS IR připojit k virtuální síti.
   >
   > Pokud používáte SSISDB, služba Data Factory se připojí k databázovému serveru k přípravě SSISDB. Také nakonfiguruje oprávnění a nastavení pro vaši virtuální síť, pokud je zadána, a připojí vaše Azure-SSIS IR do virtuální sítě.
   > 
   > Když zřídíte Azure-SSIS IR, nainstalované jsou taky Access Redistributable a Azure Feature Pack pro SSIS. Tyto součásti poskytují připojení k souborům aplikace Excel, souborům aplikace Access a různým zdrojům dat Azure, kromě zdrojů dat, které integrované součásti již podporují. Informace o dalších součástech, které můžete nainstalovat, naleznete [v tématu Vlastní nastavení pro Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

1. V okně **Připojení** v případě potřeby přepněte na **Prostředí Integration Runtime**. Seznam můžete aktualizovat kliknutím na **Aktualizovat**.

   ![Stav vytváření](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Pomocí odkazů ve sloupci **Akce** můžete prostředí Integration Runtime zastavit nebo spustit, upravit nebo odstranit. Pomocí posledního odkazu zobrazíte kód JSON pro prostředí Integration Runtime. Tlačítka pro úpravu a odstranění jsou aktivní, pouze když je prostředí IR zastavené.

   ![Akce infračerveného infračerveného počítače Azure SSIS](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Prostředí Azure SSIS Integration Runtime na portálu

1. V uzu Azure Data Factory přepněte na kartu **Úpravy** a vyberte **Připojení**. Pak přepněte na kartu **Integrační runtimes** a zobrazte existující integrační runtimes v datové továrně.

   ![Zobrazení existujících IR](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Chcete-li vytvořit novou infračervený přenos Azure-SSIS, vyberte **možnost Nový.**

   ![Prostředí Integration Runtime prostřednictvím nabídky](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. V panelu **Nastavení prostředí Integration Runtime** vyberte **existující balíčky SSIS, které chcete provést v dlaždici Azure, a** pak vyberte **Další**.

   ![Zadání typu prostředí Integration Runtime](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Zbývající kroky k nastavení Azure-SSIS IR najdete v části [Zřízení runtime integrace Azure SSIS.](#provision-an-azure-ssis-integration-runtime)

## <a name="use-azure-powershell-to-create-an-integration-runtime"></a>Vytvoření integračního běhu pomocí Azure PowerShellu

V této části použijete Azure PowerShell k vytvoření Azure-SSIS IR.

### <a name="create-variables"></a>Vytvoření proměnných

Zkopírujte a vložte následující skript. Zadejte hodnoty pro proměnné. 

```powershell
### Azure Data Factory info
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime info - This is a Data Factory compute resource for running SSIS packages.
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, whereas Enterprise lets you use advanced features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, whereas BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported. For other nodes, up to (2 x number of cores) are currently supported.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS or leave it empty]" # OPTIONAL to configure an express custom setup without script
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 
```

### <a name="sign-in-and-select-a-subscription"></a>Přihlášení a výběr předplatného

Přidejte následující skript, abyste se přihlásili a vyberte předplatné Azure.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database-server"></a>Ověření připojení k databázovému serveru

Přidejte následující skript k ověření serveru Azure SQL Database nebo spravované instance.

```powershell
# Validate only if you use SSISDB and you don't use virtual network or Azure AD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
        {
            $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword
            $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
            Try
            {
                $sqlConnection.Open();
            }
            Catch [System.Data.SqlClient.SqlException]
            {
                Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
                Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
                $yn = Read-Host
                if(!($yn -ieq "Y"))
                {
                    Return;
                }
            }
        }
    }
}
```

### <a name="configure-the-virtual-network"></a>Konfigurace virtuální sítě

Přidejte následující skript pro automatickou konfiguraci oprávnění a nastavení virtuální sítě pro váš runtime integrace Azure-SSIS pro připojení.

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte [skupinu prostředků Azure](../azure-resource-manager/management/overview.md) pomocí příkazu [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky jako skupina.

Pokud vaše skupina prostředků už existuje, nekopírujte tento kód do vašeho skriptu. 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>Vytvoření datové továrny

Spuštěním následujícího příkazu vytvořte datovou továrnu.

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>Vytvoření prostředí Integration Runtime

Spusťte následující příkazy a vytvořte runtime integrace Azure-SSIS, který spouští balíčky SSIS v Azure.

Pokud nepoužíváte SSISDB, můžete vynechat `CatalogServerEndpoint`, `CatalogPricingTier`a `CatalogAdminCredential` parametry.

Pokud nepoužíváte server Azure SQL Database s koncovými body brány firewall IP nebo koncové body služby virtuální sítě nebo spravovanou instanci s `VNetId` privátním koncovým bodem k hostování SSISDB nebo vyžadujete přístup k místním datům, můžete vynechat parametry a `Subnet` nebo pro ně předat prázdné hodnoty. Můžete je také vynechat, pokud nakonfigurujete infračervený přenos s vlastním hostitelem jako proxy server pro zařízení Azure-SSIS IR pro přístup k datům v místním prostředí. V opačném případě je nemůžete vynechat a musíte předat platné hodnoty z konfigurace virtuální sítě. Další informace najdete [v tématu Připojení azure-SSIS IR k virtuální síti](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Pokud používáte spravovanou instanci k hostování SSISDB, můžete `CatalogPricingTier` parametr vynechat nebo pro něj předat prázdnou hodnotu. V opačném případě jej nemůžete vynechat a musíte předat platnou hodnotu ze seznamu podporovaných cenových úrovní pro Azure SQL Database. Další informace naleznete v tématu [omezení prostředků databáze SQL](../sql-database/sql-database-resource-limits.md).

Pokud používáte ověřování Azure AD se spravovanou identitou pro vaši datovou továrnu pro připojení k databázovému serveru, můžete `CatalogAdminCredential` parametr vynechat. Ale musíte přidat spravovanou identitu pro vaši datovou továrnu do skupiny Azure AD s přístupovými oprávněními k databázovému serveru. Další informace najdete [v tématu Povolení ověřování Azure AD pro Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). V opačném případě jej nelze vynechat a musí předat platný objekt vytvořený z uživatelského jména a hesla správce serveru pro ověřování SQL.

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Description $AzureSSISDescription `
    -Type Managed `
    -Location $AzureSSISLocation `
    -NodeSize $AzureSSISNodeSize `
    -NodeCount $AzureSSISNodeNumber `
    -Edition $AzureSSISEdition `
    -LicenseType $AzureSSISLicenseType `
    -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
    -VnetId $VnetId `
    -Subnet $SubnetName
       
# Add the CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -CatalogServerEndpoint $SSISDBServerEndpoint `
        -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add the CatalogAdminCredential parameter if you don't use Azure AD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -CatalogAdminCredential $serverCreds
    }
}

# Add custom setup parameters if you use standard/express custom setups
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
if(![string]::IsNullOrEmpty($ExpressCustomSetup))
{
    if($ExpressCustomSetup -eq "RunCmdkey")
    {
        $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
        $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
        $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
    }
    if($ExpressCustomSetup -eq "SetEnvironmentVariable")
    {
        $variableName = "YourVariableName"
        $variableValue = "YourVariableValue"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
    }
    if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
    {
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    if($ExpressCustomSetup -eq "Theobald.XtractIS")
    {
        $jsonData = Get-Content -Raw -Path YourLicenseFile.json
        $jsonData = $jsonData -replace '\s',''
        $jsonData = $jsonData.replace('"','\"')
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    # Create an array of one or more express custom setups
    $setups = New-Object System.Collections.ArrayList
    $setups.Add($setup)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -ExpressCustomSetup $setups
}

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}
```

### <a name="start-the-integration-runtime"></a>Spuštění integračního běhu

Spusťte následující příkazy a spusťte runtime integrace Azure-SSIS.

```powershell
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

> [!NOTE]
> S výjimkou vlastní čas nastavení, tento proces by měl být dokončen do 5 minut. Ale může trvat 20 až 30 minut pro Azure-SSIS IR připojit k virtuální síti.
>
> Pokud používáte SSISDB, služba Data Factory se připojí k databázovému serveru k přípravě SSISDB. Také nakonfiguruje oprávnění a nastavení pro vaši virtuální síť, pokud je zadána, a připojí vaše Azure-SSIS IR do virtuální sítě.
> 
> Když zřídíte Azure-SSIS IR, nainstalované jsou taky Access Redistributable a Azure Feature Pack pro SSIS. Tyto součásti poskytují připojení k souborům aplikace Excel, souborům aplikace Access a různým zdrojům dat Azure, kromě zdrojů dat, které integrované součásti již podporují. Informace o dalších součástech, které můžete nainstalovat, naleznete [v tématu Vlastní nastavení pro Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="full-script"></a>Celý skript

Tady je úplný skript, který vytvoří runtime integrace Azure-SSIS.

```powershell
### Azure Data Factory info
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime info - This is a Data Factory compute resource for running SSIS packages.
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, whereas Enterprise lets you use advanced features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, whereas BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from the Azure Hybrid Benefit option
# For a Standard_D1_v2 node, up to four parallel executions per node are supported. For other nodes, up to (2 x number of cores) are currently supported.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS or leave it empty]" # OPTIONAL to configure an express custom setup without script
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

### Sign in and select a subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to the database server
# Validate only if you use SSISDB and don't use a virtual network or Azure AD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
        {
            $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword
            $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
            Try
            {
                $sqlConnection.Open();
            }
            Catch [System.Data.SqlClient.SqlException]
            {
                Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
                Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
                $yn = Read-Host
                if(!($yn -ieq "Y"))
                {
                    Return;
                }
            }
        }
    }
}

### Configure a virtual network
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}

### Create a data factory
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $DataFactoryName

### Create an integration runtime
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Description $AzureSSISDescription `
    -Type Managed `
    -Location $AzureSSISLocation `
    -NodeSize $AzureSSISNodeSize `
    -NodeCount $AzureSSISNodeNumber `
    -Edition $AzureSSISEdition `
    -LicenseType $AzureSSISLicenseType `
    -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
    -VnetId $VnetId `
    -Subnet $SubnetName
       
# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -CatalogServerEndpoint $SSISDBServerEndpoint `
        -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add the CatalogAdminCredential parameter if you don't use Azure AD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -CatalogAdminCredential $serverCreds
    }
}

# Add custom setup parameters if you use standard/express custom setups
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
if(![string]::IsNullOrEmpty($ExpressCustomSetup))
{
    if($ExpressCustomSetup -eq "RunCmdkey")
    {
        $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
        $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
        $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
    }
    if($ExpressCustomSetup -eq "SetEnvironmentVariable")
    {
        $variableName = "YourVariableName"
        $variableValue = "YourVariableValue"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
    }
    if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
    {
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    if($ExpressCustomSetup -eq "Theobald.XtractIS")
    {
        $jsonData = Get-Content -Raw -Path YourLicenseFile.json
        $jsonData = $jsonData -replace '\s',''
        $jsonData = $jsonData.replace('"','\"')
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    # Create an array of one or more express custom setups
    $setups = New-Object System.Collections.ArrayList
    $setups.Add($setup)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -ExpressCustomSetup $setups
}

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}

### Start the integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="use-an-azure-resource-manager-template-to-create-an-integration-runtime"></a>Vytvoření integračního běhu pomocí šablony Azure Resource Manageru

V této části použijete šablonu Azure Resource Manager k vytvoření runtime integrace Azure-SSIS. Tady je ukázkový návod:

1. Vytvořte soubor JSON s následující šablonou Správce prostředků Azure. Nahraďte hodnoty v úhlových závorkách (zástupné symboly) vlastními hodnotami.

    ```json
    {
        "contentVersion": "1.0.0.0",
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2018-06-01",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for your Azure-SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2018-06-01",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D8_v3",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 8
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL Database server name>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL Database server admin username>",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Database server admin password>"
                                },
                                "catalogPricingTier": "Basic"
                            }
                        }
                    }
                }
            }]
        }]
    }
    ```

2. Chcete-li nasadit šablonu `New-AzResourceGroupDeployment` Azure Resource Manager, spusťte příkaz, jak je znázorněno v následujícím příkladu. V příkladu `ADFTutorialResourceGroup` je název skupiny prostředků. `ADFTutorialARM.json`je soubor, který obsahuje definici JSON pro vaši datovou továrnu a azure-SSIS IR.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Tento příkaz vytvoří vaši továrnu dat a Azure-SSIS Ir v něm, ale nespustí infračervený přenos.

3. Chcete-li inindovou infračervený `Start-AzDataFactoryV2IntegrationRuntime` přenos Azure-SSIS spustit, spusťte příkaz:

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
        -DataFactoryName "<Data Factory Name>" `
        -Name "<Azure SSIS IR Name>" `
        -Force
    ```

> [!NOTE]
> S výjimkou vlastní čas nastavení, tento proces by měl být dokončen do 5 minut. Ale může trvat 20 až 30 minut pro Azure-SSIS IR připojit k virtuální síti.
>
> Pokud používáte SSISDB, služba Data Factory se připojí k databázovému serveru k přípravě SSISDB. Také nakonfiguruje oprávnění a nastavení pro vaši virtuální síť, pokud je zadána, a připojí vaše Azure-SSIS IR do virtuální sítě.
> 
> Když zřídíte Azure-SSIS IR, nainstalované jsou taky Access Redistributable a Azure Feature Pack pro SSIS. Tyto součásti poskytují připojení k souborům aplikace Excel, souborům aplikace Access a různým zdrojům dat Azure, kromě zdrojů dat, které integrované součásti již podporují. Informace o dalších součástech, které můžete nainstalovat, naleznete [v tématu Vlastní nastavení pro Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="deploy-ssis-packages"></a>Nasazení balíčků SSIS

Pokud používáte SSISDB, můžete nasadit balíčky do něj a spustit je na Azure-SSIS IR pomocí SQL Server Datové nástroje (SSDT) nebo SQL Server Management Studio (SSMS) nástroje. Tyto nástroje se připojují k databázovému serveru prostřednictvím koncového bodu serveru: 

- Pro server Azure SQL Database je `<server name>.database.windows.net`formát koncového bodu serveru .
- Pro spravovanou instanci s privátním koncovým bodem je `<server name>.<dns prefix>.database.windows.net`formát koncového bodu serveru .
- Pro spravovanou instanci s veřejným koncovým bodem je `<server name>.public.<dns prefix>.database.windows.net,3342`formát koncového bodu serveru . 

Pokud nepoužíváte SSISDB, můžete nasadit balíčky do systémů souborů, sdílených složek nebo souborů Azure a `dtinstall`spustit `dtutil`je `dtexec` na azure-SSIS IR pomocí nástrojů , a příkazového řádku. Další informace naleznete v [tématu Deploy SSIS packages](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). 

V obou případech můžete také spustit nasazené balíčky na Azure-SSIS IR pomocí spustit aktivitu balíčku SSIS v kanálech datové továrny. Další informace naleznete [v tématu Vyvolání spuštění balíčku SSIS jako prvotřídní aktivity data factory](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

## <a name="next-steps"></a>Další kroky

Další témata infračerveného systému Azure-SSIS naleznete v této dokumentaci:

- [Doba runtime integrace Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). Tento článek obsahuje informace o integraci runtimes obecně, včetně Azure-SSIS IR.
- [Monitorování Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Tento článek ukazuje, jak načíst a pochopit informace o azure-SSIS IR.
- [Správa Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). V tomto článku se zobrazí postup zastavení, spuštění nebo odstranění infračerveného počítače Azure-SSIS. Také ukazuje, jak škálovat vaše Azure-SSIS IR přidáním dalšíu uzly.
- [Nasazení, spouštění a monitorování balíčků SSIS v Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Připojení k SSISDB v Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Připojení k místním zdrojům dat s využitím ověřování systému Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 
- [Plánování spuštění balíčků v Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)