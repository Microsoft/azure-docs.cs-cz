---
title: Vytvoření prostředí Azure-SSIS Integration runtime v Azure Data Factory
description: Naučte se vytvořit prostředí Azure-SSIS Integration runtime v Azure Data Factory, abyste mohli nasadit a spustit balíčky SSIS v Azure.
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
ms.openlocfilehash: af6e6346616255cfb153d59df7031c6d0d4710da
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84117894"
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Vytvoření prostředí Azure-SSIS Integration runtime v Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Tento článek popisuje kroky pro zřízení prostředí Integration runtime (SSIS) Azure-služba SSIS (SQL Server Integration Services) (IR) v Azure Data Factory. Azure-SSIS IR podporuje:

- Spouštění balíčků nasazených do katalogu SSIS (SSISDB) na Azure SQL Database nebo spravované instanci SQL (model nasazení projektu).
- Spouštění balíčků nasazených do systémů souborů, sdílených složek nebo souborů Azure (model nasazení balíčku). 

Po zřízení Azure-SSIS IR můžete pomocí známých nástrojů nasadit a spustit balíčky v Azure. Mezi tyto nástroje patří nástroje SQL Server Data Tools (SSDT), SQL Server Management Studio (SSMS) a nástroje příkazového řádku `dtinstall` , jako jsou, `dtutil` a `dtexec` .

V kurzu [zřizování Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md) se dozvíte, jak vytvořit Azure-SSIS IR prostřednictvím Azure Portal nebo Data Factory aplikace. Tento kurz také ukazuje, jak volitelně použít SQL Database nebo spravovanou instanci SQL k hostování SSISDB. Tento článek se rozbalí v tomto kurzu a popisuje, jak provádět tyto volitelné úlohy:

- Pro hostování SSISDB použijte SQL Database s pravidly brány firewall IP/koncovými body služby virtuální sítě nebo s použitím spravované instance SQL s privátním koncovým bodem. V takovém případě musíte nakonfigurovat oprávnění a nastavení virtuální sítě pro Azure-SSIS IR pro připojení k virtuální síti.

- K připojení k SQL Database nebo spravované instanci SQL použijte ověřování pomocí Azure Active Directory (Azure AD) se spravovanou identitou vaší datové továrny. Jako požadavek musíte přidat spravovanou identitu pro vaši datovou továrnu jako uživatele databáze, který může vytvořit instanci SSISDB.

- Připojte se k vašemu Azure-SSIS IR k virtuální síti nebo nakonfigurujte jako proxy v místním prostředí technologii IR jako proxy, aby Azure-SSIS IR přístup k datům v místním prostředí.

V tomto článku se dozvíte, jak zřídit Azure-SSIS IR pomocí šablony Azure Portal, Azure PowerShell a Azure Resource Manager.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Předplatné Azure**. Pokud ještě nemáte předplatné, můžete si vytvořit [bezplatný zkušební](https://azure.microsoft.com/pricing/free-trial/) účet.

- **SQL Database nebo spravovaná instance SQL (volitelné)**. Pokud ještě nemáte server v SQL Database nebo instanci spravované instance SQL, vytvořte ho v Azure Portal předtím, než začnete. Data Factory pak vytvoří instanci SSISDB pro tento server nebo instanci. 

  Doporučujeme vytvořit server nebo spravovanou instanci ve stejné oblasti Azure jako modul runtime integrace. Tato konfigurace umožňuje prostředí Integration runtime zapisovat protokoly spouštění do SSISDB bez přechodu do oblastí Azure.

  Pamatujte na tyto body:

  - V závislosti na vybraném produktu může být instance SSISDB vytvořená vaším jménem v SQL Database jako jedna databáze nebo jako součást elastického fondu nebo ve spravované instanci SQL. Může být přístupný ve veřejné síti nebo prostřednictvím připojení k virtuální síti. Pokyny k výběru vhodného databázového produktu pro hostování SSISDB naleznete v tématu [Compare SQL Database a SQL Managed instance](#comparison-of-sql-database-and-sql-managed-instance). 
  
    Pokud používáte SQL Database s pravidly brány firewall protokolu IP/koncovými body služby virtuální sítě nebo se službou SQL Managed instance s privátním koncovým bodem hostitele SSISDB, nebo pokud potřebujete přístup k místním datům bez konfigurace místního prostředí IR, musíte se připojit k vaší Azure-SSIS IR k virtuální síti. Další informace najdete v tématu [připojení Azure-SSIS IR k virtuální síti](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

  - Pro SQL Database nakonfigurujte pravidla brány firewall protokolu IP/koncové body služby virtuální sítě nebo spravovanou instanci SQL s privátním koncovým bodem na hostování SSISDB. Další informace najdete v tématu [Zabezpečení databáze Azure SQL](../azure-sql/database/secure-database-tutorial.md#create-firewall-rules). Pokud chcete toto nastavení povolit pomocí PowerShellu, přečtěte si článek [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).

  - Přidejte IP adresu klientského počítače nebo rozsah IP adres, který obsahuje IP adresu klientského počítače, do seznamu IP adres klienta v nastavení brány firewall pro databázový server. Další informace najdete v tématu [pravidla brány firewall na úrovni serveru a databáze](../azure-sql/database/firewall-configure.md).

  - K databázovému serveru se můžete připojit pomocí ověřování SQL s přihlašovacími údaji správce serveru nebo pomocí ověřování Azure AD se spravovanou identitou pro vaši datovou továrnu. V takovém případě je potřeba přidat spravovanou identitu pro vaši datovou továrnu do skupiny Azure AD s přístupovými oprávněními k databázovému serveru. Další informace najdete v tématu [Povolení ověřování Azure AD pro Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir).

  - Ověřte, že váš databázový server ještě nemá instanci SSISDB. Zřizování Azure-SSIS IR nepodporuje použití existující instance SSISDB.

- **Azure Resource Manager virtuální síť (volitelné)**. Je nutné mít Azure Resource Manager virtuální síť, pokud je splněna alespoň jedna z následujících podmínek:
  - Hostuje se SSISDB na SQL Database s pravidly brány firewall protokolu IP/koncovými body služby virtuální sítě nebo spravovanou instancí SQL s privátním koncovým bodem.
  - Chcete se připojit k místním úložištím dat z balíčků SSIS běžících na vašem Azure-SSIS IR bez konfigurace prostředí IR v místním prostředí.

- **Azure PowerShell (volitelné)**. Postupujte podle pokynů v tématu [instalace a konfigurace Azure PowerShell](/powershell/azure/install-az-ps), pokud chcete ke zřízení Azure-SSIS IR spustit skript prostředí PowerShell.

### <a name="regional-support"></a>Místní podpora

Seznam oblastí Azure, ve kterých jsou k dispozici Data Factory a Azure-SSIS IR, Data Factory najdete v tématu [SSIS a dostupnost infračerveného přenosu v oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all).

### <a name="comparison-of-sql-database-and-sql-managed-instance"></a>Porovnání SQL Database a spravované instance SQL

Následující tabulka porovnává některé funkce SQL Database a SQL Managed instance, protože se vztahují k Azure-SSIR IR:

| Funkce | SQL Database | Spravovaná instance SQL |
|---------|--------------|------------------|
| **Plánování** | Agent SQL Server není k dispozici.<br/><br/>Viz [Naplánování spuštění balíčku ve data Factoryovém kanálu](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity).| Agent spravované instance SQL je k dispozici. |
| **Authentication** | Můžete vytvořit instanci SSISDB s uživatelem databáze s omezením, který představuje libovolnou skupinu Azure AD se spravovanou identitou vaší datové továrny jako členem v roli **db_owner** .<br/><br/>Další informace najdete v tématu [Povolení ověřování Azure AD pro vytvoření instance SSISDB na SQL Database](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | Můžete vytvořit instanci SSISDB s databází s omezením uživatele, která představuje spravovanou identitu vaší datové továrny. <br/><br/>Viz [Povolení ověřování Azure AD pro vytvoření instance SSISDB ve spravované instanci SQL](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-sql-managed-instance). |
| **Úroveň služby** | Při vytváření Azure-SSIS IR s SQL Database můžete vybrat úroveň služby pro SSISDB. Existuje několik úrovní služeb. | Když vytváříte Azure-SSIS IR pomocí spravované instance SQL, nemůžete vybrat úroveň služby pro SSISDB. Všechny databáze ve spravované instanci SQL sdílejí stejný prostředek přidělený k této instanci. |
| **Virtuální síť** | Pokud používáte SQL Database s pravidly firewallu protokolu IP nebo koncovými body služby virtuální sítě, váš Azure-SSIS IR se může připojit k virtuální síti Azure Resource Manager. | Váš Azure-SSIS IR se může připojit k virtuální síti Azure Resource Manager, pokud použijete spravovanou instanci SQL s privátním koncovým bodem. Virtuální síť se vyžaduje, když nepovolíte veřejný koncový bod pro spravovanou instanci.<br/><br/>Pokud připojíte Azure-SSIS IR ke stejné virtuální síti jako vaše spravovaná instance, ujistěte se, že je Azure-SSIS IR v jiné podsíti než vaše spravovaná instance. Pokud připojíte Azure-SSIS IR k jiné virtuální síti ze spravované instance, doporučujeme buď vytvoření partnerského vztahu virtuální sítě, nebo připojení typu síť-síť. Viz [připojení aplikace k spravované instanci SQL](../azure-sql/managed-instance/connect-application-instance.md). |
| **Distribuované transakce** | Tato funkce je podporovaná elastickými transakcemi. Transakce Microsoft DTC (Distributed Transaction Coordinator) (MSDTC) nejsou podporovány. Pokud vaše balíčky SSIS používají ke koordinaci distribuovaných transakcí službu MSDTC, zvažte migraci na elastické transakce pro Azure SQL Database. Další informace najdete v tématu [distribuované transakce v cloudových databázích](../azure-sql/database/elastic-transactions-overview.md). | Není podporováno. |
| | | |

## <a name="use-the-azure-portal-to-create-an-integration-runtime"></a>Použití Azure Portal k vytvoření prostředí Integration runtime

V této části použijete Azure Portal, konkrétně Data Factory uživatelské rozhraní (UI) nebo aplikace, k vytvoření Azure-SSIS IR.

### <a name="create-a-data-factory"></a>Vytvoření datové továrny

Pokud chcete vytvořit datovou továrnu pomocí Azure Portal, postupujte podle podrobných pokynů v tématu [Vytvoření datové továrny prostřednictvím uživatelského rozhraní](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory). Když to uděláte, vyberte **Připnout na řídicí panel** , abyste po jeho vytvoření povolili rychlý přístup. 

Po vytvoření objektu pro vytváření dat otevřete jeho stránku Přehled v Azure Portal. Výběrem dlaždice **autora & monitorování** otevřete stránku **Začínáme** na samostatné kartě. Zde můžete pokračovat v vytváření Azure-SSIS IR.   

### <a name="provision-an-azure-ssis-integration-runtime"></a>Zřízení prostředí Azure-SSIS Integration Runtime

1. Na stránce **Začínáme** vyberte dlaždici **Konfigurace prostředí SSIS Integration Runtime**.

   ![Dlaždice Konfigurace prostředí SSIS Integration Runtime](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

2. V části **Obecné nastavení** na panelu **Nastavení Integration runtime** proveďte následující kroky.

   ![Obecná nastavení](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. Jako **Název** zadejte název vašeho prostředí Integration Runtime.
   2. Jako **Popis** zadejte popis vašeho prostředí Integration Runtime.
   3. Jako **Umístění** vyberte umístění vašeho prostředí Integration Runtime. Zobrazí se pouze podporovaná umístění. Doporučujeme vybrat stejné umístění, jako má váš databázový server pro hostování databáze SSISDB.
   4. V poli **Velikost uzlu**vyberte velikost uzlu v clusteru Integration runtime. Zobrazí se pouze podporované velikosti uzlů. Pokud chcete spustit mnoho balíčků náročných na výpočetní výkon nebo paměť s náročnou na paměť, vyberte velikost velkého uzlu (horizontální navýšení kapacity).
   5. Jako **Počet uzlů** vyberte počet uzlů ve vašem clusteru Integration Runtime. Zobrazí se pouze podporované počty uzlů. Vyberte velký cluster s mnoha uzly (horizontální navýšení kapacity), pokud chcete souběžně spouštět mnoho balíčků.
   6. V části **edice/licence**vyberte edici SQL Server pro modul runtime integrace: Standard nebo Enterprise. Vyberte možnost Enterprise, pokud chcete používat pokročilé funkce v prostředí Integration runtime.
   7. Pro **Uložit peníze**vyberte možnost zvýhodněné hybridní využití Azure pro prostředí Integration Runtime: **Ano** nebo **ne**. Vyberte **Ano** , pokud chcete využít vlastní licenci SQL Server se Software Assurance, abyste využili úspory nákladů s využitím hybridního použití.
   8. Vyberte **Další**.

3. V části **nastavení SQL** proveďte následující kroky.

   ![Nastavení SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   1. Zaškrtnutím políčka **vytvořit katalog SSIS (SSISDB) pomocí spravované Instance SQL Database/SQL uložte své projekty/balíčky/prostředí/spuštění protokolů** , abyste vybrali produkt Azure SQL, který se má v Azure-SSIS IR spustit. Zvolíte buď model nasazení projektu, kde jsou balíčky nasazeny do SSISDB hostovaného SQL Database nebo spravované instance SQL, nebo model nasazení balíčku, ve kterém jsou balíčky nasazeny do systémů souborů, sdílených souborů nebo souborů Azure. 
    
    Pokud zaškrtnete políčko, budete muset použít vlastní SQL Database nebo spravovanou instanci SQL k hostování instance SSISDB, kterou vytvoříme a spravujeme vaším jménem.
   
    1. Pro **předplatné**vyberte předplatné Azure, které má vaši SQL Database nebo SPRAVOVANOU instanci SQL pro hostování SSISDB. 

    1. V poli **umístění**vyberte umístění vaší SQL Database nebo spravované instance SQL. Doporučujeme vybrat stejné umístění, jako má vaše prostředí Integration Runtime. 

    1. V poli **koncový bod databázového serveru katalogu**vyberte koncový bod vaší SQL Database nebo spravované instance SQL. 
    
       Na základě vybrané SQL Database nebo spravované instance SQL může být instance SSISDB vytvořená vaším jménem jako jediná databáze nebo jako součást elastického fondu v SQL Database nebo jako instance databáze ve spravované instanci SQL. Může být přístupný ve veřejné síti nebo prostřednictvím připojení k virtuální síti. Pokyny k výběru produktu pro hostování SSISDB naleznete v tématu [Compare Azure SQL Database a SQL Managed instance](#comparison-of-sql-database-and-sql-managed-instance).
       Pokud vyberete SQL Database s pravidly brány firewall protokolu IP/koncovými body služby virtuální sítě nebo se službou SQL Managed instance s privátním koncovým bodem hostitele SSISDB, nebo pokud budete vyžadovat přístup k místním datům bez konfigurace místního prostředí IR, musíte se připojit k vaší Azure-SSIS IR k virtuální síti. Další informace najdete v tématu [připojení Azure-SSIS IR k virtuální síti](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

    1. Zaškrtněte políčko pro výběr metody ověřování pro databázový server pro hostování SSISDB **použít ověřování AAD se spravovaným identitou pro váš server ADF** . Zvolíte ověřování SQL nebo ověřování Azure AD se spravovanou identitou pro vaši datovou továrnu. 
    
        Pokud políčko zaškrtnete, budete muset přidat spravovanou identitu pro vaši datovou továrnu do skupiny Azure AD s přístupovými oprávněními k vašemu databázovému serveru. Další informace najdete v tématu [Povolení ověřování Azure AD pro Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). 

    1. Jako **uživatelské jméno správce**zadejte uživatelské jméno pro ověřování SQL vašeho databázového serveru pro hostování SSISDB. 

    1. Jako **heslo správce**zadejte heslo pro ověřování SQL vašeho databázového serveru pro hostování SSISDB. 

    1. Pro **úroveň služby databáze katalogu**vyberte úroveň služby pro váš databázový server pro hostování SSISDB. Vyberte úroveň Basic, Standard nebo Premium nebo vyberte název elastického fondu. 

      1. Vyberte **Test připojení**. Pokud je test úspěšný, vyberte **Další**. 

4. V části **Upřesnit nastavení** proveďte následující kroky.

   ![Upřesnit nastavení](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. Pro **maximální paralelní spouštění na uzel**vyberte maximální počet balíčků, které mají být spuštěny souběžně na jeden uzel v clusteru Integration runtime. Zobrazí se pouze podporované počty balíčků. Vyberte nízké číslo, pokud chcete použít více než jedno jádro pro spuštění jednoho velkého balíčku, který je náročné na výpočetní výkon nebo paměť. Vyberte vysoké číslo, pokud chcete spustit jeden nebo více malých balíčků v jednom jádru.

   1. Zaškrtněte políčko **přizpůsobit Azure-SSIS Integration runtime pomocí dalších systémových konfigurací/instalací součástí** a zvolte, zda chcete do Azure-SSIS IR přidat vlastní nastavení Standard/Express. Další informace najdete v tématu [vlastní nastavení pro Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

      Pokud zaškrtnete políčko, proveďte následující kroky.

      ![Rozšířená nastavení s vlastními nastaveními](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)
   
      1. Pro **vlastní nastavení identifikátoru URI SAS kontejneru**zadejte identifikátor URI SAS kontejneru, kam ukládáte skripty a přidružené soubory pro standardní vlastní nastavení.

      1. Pro **expresní vlastní instalaci**vyberte **Nový** , aby se otevřel panel **Přidat expresní vlastní nastavení** , a pak vyberte libovolné typy v rozevírací nabídce **typ rychlé vlastní instalace** , třeba **příkaz run cmdkey**, **přidejte proměnnou prostředí**, **Nainstalujte licencovanou komponentu**atd.

        Pokud vyberete možnost **instalovat licencovaný typ součásti** , pak můžete vybrat všechny integrované komponenty z našich partnerů ISV v rozevírací nabídce **název součásti** a v případě potřeby zadat licenční kód produktu, který jste si z nich koupili v poli **licenční klíč** .
  
        Vaše přidané expresní vlastní nastavení se zobrazí v části **Upřesnit nastavení** . Pokud je chcete odebrat, můžete zaškrtnout políčka a pak vybrat **Odstranit**.

   1. Vyberte **virtuální síť, ke které se má Azure-SSIS Integration runtime připojit, povolte ADF, aby se vytvořily určité síťové prostředky, a volitelně můžete použít vlastní statické veřejné IP adresy,** abyste si zvolili, jestli se chcete připojit ke službě Integration runtime k virtuální síti. 

    Tuto možnost vyberte, pokud používáte SQL Database s pravidly brány firewall protokolu IP/koncovými body služby virtuální sítě nebo se službou SQL Managed instance s privátním koncovým bodem hostitele SSISDB, nebo pokud požadujete přístup k místním datům (to znamená, že máte místní zdroje dat nebo cíle v balíčcích SSIS), aniž byste museli nakonfigurovat prostředí IR v místním prostředí. Další informace najdete v tématu [připojení Azure-SSIS IR k virtuální síti](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

    Pokud zaškrtnete políčko, proveďte následující kroky.

    ![Rozšířené nastavení virtuální sítě](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

    1. V poli **předplatné**vyberte předplatné Azure, které má vaši virtuální síť.

    1. Pro **umístění**je vybrané stejné umístění prostředí Integration runtime.

    1. Jako **typ**vyberte typ virtuální sítě: classic nebo Azure Resource Manager. Doporučujeme vybrat Azure Resource Manager virtuální síť, protože klasické virtuální sítě budou brzy zastaralé.

    1. V poli **název virtuální**sítě vyberte název vaší virtuální sítě. Měl by být stejný jako použitý pro váš SQL Database s koncovými body služby virtuální sítě nebo vaší spravovanou instancí SQL s privátním koncovým bodem pro hostování SSISDB. Nebo by měl být stejný jako připojený k vaší místní síti. V opačném případě může být libovolná virtuální síť, která bude obsahovat vlastní statické veřejné IP adresy pro Azure-SSIS IR.

    1. Jako **název podsítě**vyberte název podsítě pro virtuální síť. Měla by být stejná jako ta, která se používá pro váš SQL Database s koncovými body služby virtuální sítě pro hostování SSISDB. Nebo by měla být jiná podsíť než ta, která se používá pro vaši spravovanou instanci SQL s privátním koncovým bodem pro hostování SSISDB. V opačném případě může to být jakákoli podsíť, která přináší vlastní statické veřejné IP adresy pro Azure-SSIS IR.

    1. Zaškrtněte políčko **přenést statické veřejné IP adresy pro Azure-SSIS Integration runtime** a zvolte, jestli chcete použít vlastní statické veřejné IP adresy pro Azure-SSIS IR, abyste je mohli v bráně firewall pro zdroje dat zapnout.

        Pokud zaškrtnete políčko, proveďte následující kroky.

        1. U **první statické veřejné IP adresy**vyberte první STATICKOU veřejnou IP adresu, která splňuje požadavky pro váš Azure-SSIS IR. Pokud žádné nemáte, klikněte na **vytvořit nový** odkaz pro vytvoření statických veřejných IP adres na Azure Portal a pak klikněte na tlačítko Aktualizovat, abyste je mohli vybrat.
        2. U **druhé statické veřejné IP adresy**vyberte druhou STATICKOU veřejnou IP adresu, která splňuje požadavky pro váš Azure-SSIS IR. Pokud žádné nemáte, klikněte na **vytvořit nový** odkaz pro vytvoření statických veřejných IP adres na Azure Portal a pak klikněte na tlačítko Aktualizovat, abyste je mohli vybrat.

   1. Zaškrtněte políčko **nastavit Integration runtime pro místní hostování jako proxy pro Azure-SSIS Integration runtime** zaškrtávací políčko pro výběr, zda chcete pro své Azure-SSIS IR nakonfigurovat prostředí IR v místním prostředí jako proxy. Další informace najdete v tématu [nastavení místního hostitele IR jako proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). 

      Pokud zaškrtnete políčko, proveďte následující kroky.

      ![Rozšířená nastavení s místním prostředím IR](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

      1. Pro **Integration runtime**v místním prostředí vyberte pro Azure-SSIS IR existující místní prostředí IR jako proxy.

      1. V případě **pracovní služby s**ochranou úložiště vyberte existující propojenou službu Azure Blob Storage nebo vytvořte novou pro přípravu.

      1. Do pole **pracovní cesta**zadejte kontejner objektů BLOB ve vybraném účtu služby Azure Blob Storage nebo ho nechte prázdný, aby se pro přípravu použil výchozí.

   1. Vyberte pokračovat v **ověřování virtuální**sítě  >  **Continue**. 

1. V části **Souhrn** Zkontrolujte všechna nastavení zřizování, záložku doporučené odkazy na dokumentaci a vyberte **Dokončit** a spusťte tak vytváření prostředí Integration runtime.

   > [!NOTE]
   > S výjimkou času vlastní instalace by tento proces měl skončit do 5 minut. Může ale trvat 20-30 minut, než se Azure-SSIS IR připojit k virtuální síti.
   >
   > Pokud používáte SSISDB, Služba Data Factory se připojí k vašemu databázovému serveru, aby připravil SSISDB. Také nakonfiguruje oprávnění a nastavení vaší virtuální sítě, pokud je zadáte, a připojí vaše Azure-SSIS IR k virtuální síti.
   > 
   > Když zřizujete Azure-SSIS IR, nainstaluje se také Distribuovatelný balíček a sada Azure Feature Pack pro SSIS. Tyto komponenty poskytují připojení k excelovým souborům, přístup k souborům a různým zdrojům dat Azure a navíc ke zdrojům dat, které jsou již podporovány v rámci integrovaných komponent. Informace o dalších součástech, které můžete nainstalovat, najdete v tématu [vlastní nastavení pro Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

1. V okně **Připojení** v případě potřeby přepněte na **Prostředí Integration Runtime**. Seznam můžete aktualizovat kliknutím na **Aktualizovat**.

   ![Stav vytváření](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Pomocí odkazů ve sloupci **Akce** můžete prostředí Integration Runtime zastavit nebo spustit, upravit nebo odstranit. Pomocí posledního odkazu zobrazíte kód JSON pro prostředí Integration Runtime. Tlačítka pro úpravu a odstranění jsou aktivní, pouze když je prostředí IR zastavené.

   ![SSIS možnosti infračerveného přenosu v Azure](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Prostředí Azure SSIS Integration Runtime na portálu

1. V uživatelském rozhraní Azure Data Factory přepněte na kartu **Upravit** a vyberte **připojení**. Pak přejděte na kartu **prostředí Integration runtime** , kde se zobrazí existující prostředí Integration runtime ve vaší datové továrně.

   ![Zobrazení existujících IR](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Pokud chcete vytvořit nový Azure-SSIS IR, vyberte **Nový** .

   ![Prostředí Integration Runtime prostřednictvím nabídky](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. Na panelu **nastavení Integration runtime** vyberte dlaždici **stávající balíčky SSIS, které se mají spustit v Azure** , a pak vyberte **Další**.

   ![Zadání typu prostředí Integration Runtime](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Zbývající kroky nastavení Azure-SSIS IR najdete v části [zřízení prostředí Azure SSIS Integration runtime](#provision-an-azure-ssis-integration-runtime) .

## <a name="use-azure-powershell-to-create-an-integration-runtime"></a>Použití Azure PowerShell k vytvoření prostředí Integration runtime

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
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use SQL Database with IP firewall rules/virtual network service endpoints or SQL Managed Instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for SQL Database with virtual network service endpoints, or a different subnet from the one used for SQL Managed Instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"

### SSISDB info
$SSISDBServerEndpoint = "[your logical server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for SQL Database or leave it empty for SQL Managed Instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 
```

### <a name="sign-in-and-select-a-subscription"></a>Přihlášení a výběr předplatného

Přidejte následující skript pro přihlášení a výběr vašeho předplatného Azure.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection"></a>Ověření připojení

Přidejte následující skript, který ověří připojení k SQL Database nebo spravované instanci SQL.

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
                Write-Warning "Cannot connect, exception: $_";
                Write-Warning "Please make sure the server or instance you specified has already been created. Do you want to proceed? [Y/N]"
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

Přidejte následující skript, který automaticky nakonfiguruje oprávnění a nastavení virtuální sítě pro prostředí Azure-SSIS Integration runtime pro připojení.

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

Vytvořte [skupinu prostředků Azure](../azure-resource-manager/management/overview.md) pomocí příkazu [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky jako skupina.

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

Spuštěním následujících příkazů vytvořte prostředí Azure-SSIS Integration runtime, které spouští balíčky SSIS v Azure.

Pokud SSISDB nepoužíváte, můžete vynechat `CatalogServerEndpoint` `CatalogPricingTier` parametry, a `CatalogAdminCredential` .

Pokud nepoužíváte SQL Database s pravidly brány firewall protokolu IP/koncovými body služby virtuální sítě nebo se službou SQL Managed instance s privátním koncovým bodem pro hostování SSISDB nebo vyžadujete přístup k místním datům, můžete vynechat `VNetId` `Subnet` parametry a nebo pro ně předat prázdné hodnoty. Můžete je také vynechat, pokud nakonfigurujete místní prostředí IR jako proxy pro Azure-SSIS IR pro přístup k datům v místním prostředí. V opačném případě je nemůžete vynechat a musí předávat platné hodnoty z konfigurace vaší virtuální sítě. Další informace najdete v tématu [připojení Azure-SSIS IR k virtuální síti](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Použijete-li ke hostování SSISDB spravovanou instanci SQL, můžete parametr vynechat `CatalogPricingTier` nebo pro něj předat prázdnou hodnotu. V opačném případě ho nemůžete vynechat a musí předávat platnou hodnotu ze seznamu podporovaných cenových úrovní pro Azure SQL Database. Další informace najdete v tématu [SQL Database omezení prostředků](../sql-database/sql-database-resource-limits.md).

Pokud pro připojení k databázovému serveru použijete ověřování Azure AD se spravovanou identitou, můžete tento `CatalogAdminCredential` parametr vynechat. Je ale nutné přidat spravovanou identitu pro vaši datovou továrnu do skupiny Azure AD s přístupovými oprávněními k databázovému serveru. Další informace najdete v tématu [Povolení ověřování Azure AD pro Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). V opačném případě ji nemůžete vynechat a musí předat platný objekt vytvořený z uživatelského jména a hesla správce serveru pro ověřování SQL.

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

### <a name="start-the-integration-runtime"></a>Spuštění prostředí Integration runtime

Spuštěním následujících příkazů spusťte prostředí Azure-SSIS Integration runtime.

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
> S výjimkou času vlastní instalace by tento proces měl skončit do 5 minut. Může ale trvat 20-30 minut, než se Azure-SSIS IR připojit k virtuální síti.
>
> Pokud používáte SSISDB, Služba Data Factory se připojí k vašemu databázovému serveru, aby připravil SSISDB. Také nakonfiguruje oprávnění a nastavení vaší virtuální sítě, pokud je zadáte, a připojí vaše Azure-SSIS IR k virtuální síti.
> 
> Když zřizujete Azure-SSIS IR, nainstaluje se také Distribuovatelný balíček a sada Azure Feature Pack pro SSIS. Tyto komponenty poskytují připojení k excelovým souborům, přístup k souborům a různým zdrojům dat Azure a navíc ke zdrojům dat, které jsou již podporovány v rámci integrovaných komponent. Informace o dalších součástech, které můžete nainstalovat, najdete v tématu [vlastní nastavení pro Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="full-script"></a>Celý skript

Tady je celý skript, který vytvoří prostředí Azure-SSIS Integration runtime.

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
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use SQL Database with IP firewall rules/virtual network service endpoints or SQL Managed Instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for SQL Database with virtual network service endpoints, or a different subnet from the one used for SQL Managed Instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"

### SSISDB info
$SSISDBServerEndpoint = "[your logical server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for SQL Database or leave it empty for SQL Managed Instance]"

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
                Write-Warning "Cannot connect, exception: $_";
                Write-Warning "Please make sure the server or instance you specified has already been created. Do you want to proceed? [Y/N]"
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

## <a name="use-an-azure-resource-manager-template-to-create-an-integration-runtime"></a>Použití šablony Azure Resource Manager k vytvoření prostředí Integration runtime

V této části použijete šablonu Azure Resource Manager k vytvoření prostředí Azure-SSIS Integration runtime. Tady je ukázkový Návod:

1. Vytvořte soubor JSON s následující šablonou Azure Resource Manager. Nahraďte hodnoty v lomených závorkách (zástupné symboly) vlastními hodnotami.

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
                                "catalogServerEndpoint": "<server name>.database.windows.net",
                                "catalogAdminUserName": "<server admin username>",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<server admin password>"
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

2. Chcete-li nasadit šablonu Azure Resource Manager, spusťte `New-AzResourceGroupDeployment` příkaz, jak je znázorněno v následujícím příkladu. V tomto příkladu `ADFTutorialResourceGroup` je název vaší skupiny prostředků. `ADFTutorialARM.json`je soubor, který obsahuje definici JSON pro datovou továrnu a Azure-SSIS IR.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Tento příkaz vytvoří datovou továrnu a Azure-SSIS IR v ní, ale nespustí IR.

3. Chcete-li spustit Azure-SSIS IR, spusťte `Start-AzDataFactoryV2IntegrationRuntime` příkaz:

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
        -DataFactoryName "<Data Factory Name>" `
        -Name "<Azure SSIS IR Name>" `
        -Force
    ```

> [!NOTE]
> S výjimkou času vlastní instalace by tento proces měl skončit do 5 minut. Může ale trvat 20-30 minut, než se Azure-SSIS IR připojit k virtuální síti.
>
> Pokud používáte SSISDB, Služba Data Factory se připojí k vašemu databázovému serveru, aby připravil SSISDB. Také nakonfiguruje oprávnění a nastavení vaší virtuální sítě, pokud je zadáte, a připojí vaše Azure-SSIS IR k virtuální síti.
> 
> Když zřizujete Azure-SSIS IR, nainstaluje se také Distribuovatelný balíček a sada Azure Feature Pack pro SSIS. Tyto komponenty poskytují připojení k excelovým souborům, přístup k souborům a různým zdrojům dat Azure a navíc ke zdrojům dat, které jsou již podporovány v rámci integrovaných komponent. Informace o dalších součástech, které můžete nainstalovat, najdete v tématu [vlastní nastavení pro Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="deploy-ssis-packages"></a>Nasazení balíčků SSIS

Pokud používáte SSISDB, můžete do něj nasadit balíčky a spouštět je v Azure-SSIS IR pomocí nástrojů SQL Server Data Tools (SSDT) nebo SQL Server Management Studio (SSMS). Tyto nástroje se připojují k vašemu databázovému serveru prostřednictvím koncového bodu serveru: 

- Pro SQL Database je formát koncového bodu serveru `<server name>.database.windows.net` .
- Pro spravovanou instanci SQL s privátním koncovým bodem je formát koncového bodu serveru `<server name>.<dns prefix>.database.windows.net` .
- Pro spravovanou instanci SQL s veřejným koncovým bodem je formát koncového bodu serveru `<server name>.public.<dns prefix>.database.windows.net,3342` . 

Pokud SSISDB nepoužíváte, můžete balíčky nasadit do systémů souborů, sdílených složek nebo souborů Azure a spouštět je v Azure-SSIS IR pomocí `dtinstall` `dtutil` `dtexec` nástrojů příkazového řádku, a. Další informace najdete v tématu [nasazení balíčků SSIS](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). 

V obou případech můžete také spouštět nasazené balíčky na Azure-SSIS IR pomocí aktivity spustit balíček SSIS v Data Factorych kanálech. Další informace najdete v tématu [vyvolání spuštění balíčku SSIS jako aktivity první třídy Data Factory](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

## <a name="next-steps"></a>Další kroky

Další témata Azure-SSIS IR v této dokumentaci:

- [Prostředí Azure-SSIS Integration runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Tento článek obsahuje obecné informace o prostředí Integration runtime, včetně Azure-SSIS IR.
- [Monitorování Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). V tomto článku se dozvíte, jak načíst a pochopit informace o Azure-SSIS IR.
- [Správa Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). V tomto článku se dozvíte, jak zastavit, spustit nebo odstranit Azure-SSIS IR. Ukazuje také způsob horizontálního navýšení kapacity Azure-SSIS IR přidáním dalších uzlů.
- [Nasazení, spuštění a monitorování balíčků SSIS v Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Připojení k SSISDB v Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Připojení k místním zdrojům dat s využitím ověřování systému Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 
- [Plánování spouštění balíčků v Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)