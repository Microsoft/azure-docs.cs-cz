---
title: Vytvoření prostředí Azure-SSIS Integration runtime v Azure Data Factory
description: Naučte se vytvořit prostředí Azure-SSIS Integration runtime v Azure Data Factory, abyste mohli nasadit a spustit balíčky SSIS v Azure.
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/22/2021
author: swinarko
ms.author: sawinark
ms.openlocfilehash: 4b26abe1d1340e4e8c5f034fad72f612f0b246a2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101739401"
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Vytvoření prostředí Azure-SSIS Integration runtime v Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Tento článek popisuje kroky pro zřízení prostředí Integration runtime (SSIS) Azure-služba SSIS (SQL Server Integration Services) (IR) v Azure Data Factory (ADF). Azure-SSIS IR podporuje:

- Spouštění balíčků nasazených do katalogu SSIS (SSISDB) Azure SQL Database hostovaných serverem/spravovanou instancí (model nasazení projektu)
- Spouštění balíčků nasazených do systému souborů, souborů Azure nebo databáze služby SQL Server Database (MSDB) hostované službou Azure SQL Managed instance (model nasazení balíčku)

Po zřízení Azure-SSIS IR můžete pomocí známých nástrojů nasadit a spustit balíčky v Azure. Tyto nástroje jsou již povoleny v Azure a zahrnují nástroje SQL Server Data Tools (SSDT), SQL Server Management Studio (SSMS) a nástroje příkazového řádku, jako je [dtutil](/sql/integration-services/dtutil-utility) a [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md).

V kurzu [zřizování Azure-SSIS IR](./tutorial-deploy-ssis-packages-azure.md) se dozvíte, jak vytvořit Azure-SSIS IR prostřednictvím Azure Portal nebo Data Factory aplikace. Tento kurz také ukazuje, jak volitelně použít Azure SQL Database Server nebo spravovanou instanci k hostování SSISDB. Tento článek se rozbalí v tomto kurzu a popisuje, jak provádět tyto volitelné úlohy:

- Pro hostování SSISDB použijte Azure SQL Database Server s pravidly brány firewall protokolu IP/koncovými body služby virtuální sítě nebo spravovanou instancí s privátním koncovým bodem. V takovém případě musíte nakonfigurovat oprávnění a nastavení virtuální sítě pro Azure-SSIS IR pro připojení k virtuální síti.

- K připojení k serveru Azure SQL Database nebo spravované instanci použijte ověřování pomocí Azure Active Directory (Azure AD) se spravovanou identitou vaší datové továrny. Jako požadavek musíte přidat spravovanou identitu pro vaši datovou továrnu jako uživatele databáze, který může vytvořit instanci SSISDB.

- Připojte se k vašemu Azure-SSIS IR k virtuální síti nebo nakonfigurujte jako proxy v místním prostředí technologii IR jako proxy, aby Azure-SSIS IR přístup k datům v místním prostředí.

V tomto článku se dozvíte, jak zřídit Azure-SSIS IR pomocí šablony Azure Portal, Azure PowerShell a Azure Resource Manager.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Předplatné Azure**. Pokud ještě nemáte předplatné, můžete si vytvořit [bezplatný zkušební](https://azure.microsoft.com/pricing/free-trial/) účet.

- **Azure SQL Database Server nebo spravovaná instance SQL (volitelné)**. Pokud ještě nemáte databázový server nebo spravovanou instanci, vytvořte si ho v Azure Portal před tím, než začnete začít. Data Factory pak na tomto databázovém serveru vytvoří instanci SSISDB. 

  Doporučujeme vytvořit databázový server nebo spravovanou instanci ve stejné oblasti Azure jako modul Integration runtime. Tato konfigurace umožňuje prostředí Integration runtime zapisovat protokoly spouštění do SSISDB bez přechodu do oblastí Azure.

  Pamatujte na tyto body:

  - Instance SSISDB se dá vytvořit vaším jménem jako jediná databáze, jako součást elastického fondu nebo ve spravované instanci. Může být přístupný ve veřejné síti nebo prostřednictvím připojení k virtuální síti. Pokyny k výběru mezi SQL Database a SQL Managed instance pro hostování SSISDB naleznete v části [porovnání SQL Database a SQL Managed instance](#comparison-of-sql-database-and-sql-managed-instance) v tomto článku. 
  
    Pokud používáte server Azure SQL Database s pravidly brány firewall protokolu IP/koncovými body služby virtuální sítě nebo se službou SQL Managed instance s privátním koncovým bodem hostitele SSISDB, nebo pokud požadujete přístup k místním datům bez konfigurace místního prostředí IR, musíte se připojit k vaší Azure-SSIS IR k virtuální síti. Další informace najdete v tématu [připojení Azure-SSIS IR k virtuální síti](./join-azure-ssis-integration-runtime-virtual-network.md).

  - Potvrďte, že nastavení **Povolit přístup ke službám Azure** je pro databázový server povolené. Toto nastavení se nedá použít, když použijete server Azure SQL Database s pravidly firewallu protokolu IP/koncovými body služby virtuální sítě nebo spravovanou instancí SQL s privátním koncovým bodem pro hostování SSISDB. Další informace najdete v tématu [zabezpečená Azure SQL Database](../azure-sql/database/secure-database-tutorial.md#create-firewall-rules). Pokud chcete toto nastavení povolit pomocí PowerShellu, přečtěte si článek [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).

  - Přidejte IP adresu klientského počítače nebo rozsah IP adres, který obsahuje IP adresu klientského počítače, do seznamu IP adres klienta v nastavení brány firewall pro databázový server. Další informace najdete v tématu [Pravidla brány firewall na úrovni serveru a databáze služby Azure SQL Database](../azure-sql/database/firewall-configure.md).

  - K databázovému serveru se můžete připojit pomocí ověřování SQL s přihlašovacími údaji správce serveru nebo pomocí ověřování Azure AD se spravovanou identitou pro vaši datovou továrnu. V takovém případě je potřeba přidat spravovanou identitu pro vaši datovou továrnu do skupiny Azure AD s přístupovými oprávněními k databázovému serveru. Další informace najdete v tématu [Povolení ověřování Azure AD pro Azure-SSIS IR](./enable-aad-authentication-azure-ssis-ir.md).

  - Ověřte, že váš databázový server ještě nemá instanci SSISDB. Zřizování Azure-SSIS IR nepodporuje použití existující instance SSISDB.

- **Azure Resource Manager virtuální síť (volitelné)**. Je nutné mít Azure Resource Manager virtuální síť, pokud je splněna alespoň jedna z následujících podmínek:

  - SSISDB je hostitelem na serveru Azure SQL Database s pravidly brány firewall protokolu IP/koncovými body služby virtuální sítě nebo spravované instance s privátním koncovým bodem.

  - Chcete se připojit k místním úložištím dat z balíčků SSIS běžících na vašem Azure-SSIS IR bez konfigurace prostředí IR v místním prostředí.

- **Azure PowerShell (volitelné)**. Postupujte podle pokynů v tématu [instalace a konfigurace Azure PowerShell](/powershell/azure/install-az-ps), pokud chcete ke zřízení Azure-SSIS IR spustit skript prostředí PowerShell.

### <a name="regional-support"></a>Místní podpora

Seznam oblastí Azure, ve kterých jsou k dispozici Data Factory a Azure-SSIS IR, Data Factory najdete v tématu [SSIS a dostupnost infračerveného přenosu v oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all).

### <a name="comparison-of-sql-database-and-sql-managed-instance"></a>Porovnání SQL Database a spravované instance SQL

Následující tabulka porovnává některé funkce serveru Azure SQL Database a SQL Managed instance, protože se vztahují k Azure-SSIR IR:

| Funkce | SQL Database| Spravovaná instance SQL |
|---------|--------------|------------------|
| **Plánování** | Agent SQL Server není k dispozici.<br/><br/>Viz [Naplánování spuštění balíčku ve data Factoryovém kanálu](/sql/integration-services/lift-shift/ssis-azure-schedule-packages#activity).| Agent spravované instance je k dispozici. |
| **Authentication** | Můžete vytvořit instanci SSISDB s uživatelem databáze s omezením, který představuje libovolnou skupinu Azure AD se spravovanou identitou vaší datové továrny jako členem v roli **db_owner** .<br/><br/>Další informace najdete [v tématu Povolení ověřování Azure AD při vytváření SSISDB na serveru Azure SQL Database](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | Můžete vytvořit instanci SSISDB s databází s omezením uživatele, která představuje spravovanou identitu vaší datové továrny. <br/><br/>Viz [Povolení ověřování Azure AD k vytvoření SSISDB ve spravované instanci Azure SQL](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-sql-managed-instance). |
| **Úroveň služby** | Když vytvoříte Azure-SSIS IR se serverem Azure SQL Database, můžete vybrat úroveň služby pro SSISDB. Existuje několik úrovní služeb. | Když vytvoříte Azure-SSIS IR s vaší spravovanou instancí, nemůžete vybrat úroveň služby pro SSISDB. Všechny databáze ve spravované instanci sdílejí stejný prostředek přidělený k této instanci. |
| **Virtuální síť** | Pokud používáte Azure SQL Database Server s pravidly brány firewall nebo koncovými body služby virtuální sítě, váš Azure-SSIS IR se může připojit k virtuální síti Azure Resource Manager. | Váš Azure-SSIS IR se může připojit k virtuální síti Azure Resource Manager, pokud použijete spravovanou instanci s privátním koncovým bodem. Virtuální síť se vyžaduje, když nepovolíte veřejný koncový bod pro spravovanou instanci.<br/><br/>Pokud připojíte Azure-SSIS IR ke stejné virtuální síti jako vaše spravovaná instance, ujistěte se, že je Azure-SSIS IR v jiné podsíti než vaše spravovaná instance. Pokud připojíte Azure-SSIS IR k jiné virtuální síti ze spravované instance, doporučujeme buď vytvoření partnerského vztahu virtuální sítě, nebo připojení typu síť-síť. Viz téma [připojení aplikace k spravované instanci Azure SQL Database](../azure-sql/managed-instance/connect-application-instance.md). |
| **Distribuované transakce** | Tato funkce je podporovaná elastickými transakcemi. Transakce Microsoft DTC (Distributed Transaction Coordinator) (MSDTC) nejsou podporovány. Pokud vaše balíčky SSIS používají ke koordinaci distribuovaných transakcí službu MSDTC, zvažte migraci na elastické transakce pro Azure SQL Database. Další informace najdete v tématu [distribuované transakce v cloudových databázích](../azure-sql/database/elastic-transactions-overview.md). | Nepodporováno |
| | | |

## <a name="use-the-azure-portal-to-create-an-integration-runtime"></a>Použití Azure Portal k vytvoření prostředí Integration runtime

V této části použijete Azure Portal, konkrétně Data Factory uživatelské rozhraní (UI) nebo aplikace, k vytvoření Azure-SSIS IR.

### <a name="create-a-data-factory"></a>Vytvoření datové továrny

Pokud chcete vytvořit datovou továrnu pomocí Azure Portal, postupujte podle podrobných pokynů v tématu [Vytvoření datové továrny prostřednictvím uživatelského rozhraní](./quickstart-create-data-factory-portal.md#create-a-data-factory). Když to uděláte, vyberte **Připnout na řídicí panel** , abyste po jeho vytvoření povolili rychlý přístup. 

Po vytvoření objektu pro vytváření dat otevřete jeho stránku Přehled v Azure Portal. Výběrem dlaždice **autora & monitorování** otevřete stránku **Začínáme** na samostatné kartě. Zde můžete pokračovat v vytváření Azure-SSIS IR.   

### <a name="provision-an-azure-ssis-integration-runtime"></a>Zřízení prostředí Azure-SSIS Integration Runtime

Na stránce **Začínáme** vyberte dlaždici **konfigurovat Integration runtime SSIS** a otevřete tak podokno **instalace prostředí Integration runtime** .

   ![Dlaždice Konfigurace prostředí SSIS Integration Runtime](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

   Podokno **instalace prostředí Integration runtime** má tři stránky, na kterých jste úspěšně nakonfigurovali obecné, nasazení a pokročilá nastavení.

#### <a name="general-settings-page"></a>Stránka Obecné nastavení

Na stránce **Obecné nastavení** v podokně **instalace prostředí Integration runtime** proveďte následující kroky.

   ![Obecná nastavení](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. Jako **Název** zadejte název vašeho prostředí Integration Runtime.

   2. Jako **Popis** zadejte popis vašeho prostředí Integration Runtime.

   3. Jako **Umístění** vyberte umístění vašeho prostředí Integration Runtime. Zobrazí se pouze podporovaná umístění. Doporučujeme vybrat stejné umístění, jako má váš databázový server pro hostování databáze SSISDB.

   4. V poli **Velikost uzlu** vyberte velikost uzlu v clusteru Integration runtime. Zobrazí se pouze podporované velikosti uzlů. Pokud chcete spustit mnoho balíčků náročných na výpočetní výkon nebo paměť s náročnou na paměť, vyberte velikost velkého uzlu (horizontální navýšení kapacity).
   > [!NOTE]
   > Pokud požadujete [izolaci výpočtů](../azure-government/azure-secure-isolation-guidance.md#compute-isolation), vyberte prosím velikost uzlu **Standard_E64i_v3** . Tato velikost uzlu představuje izolované virtuální počítače, které využívají svého celého fyzického hostitele, a poskytuje potřebnou úroveň izolace, kterou vyžadují určité úlohy, jako je USA – ministerstvo IL5 (US level of obrany).
   
   5. Jako **Počet uzlů** vyberte počet uzlů ve vašem clusteru Integration Runtime. Zobrazí se pouze podporované počty uzlů. Vyberte velký cluster s mnoha uzly (horizontální navýšení kapacity), pokud chcete souběžně spouštět mnoho balíčků.

   6. V části **edice/licence** vyberte edici SQL Server pro modul runtime integrace: Standard nebo Enterprise. Vyberte možnost Enterprise, pokud chcete používat pokročilé funkce v prostředí Integration runtime.

   7. Pro **Uložit peníze** vyberte možnost zvýhodněné hybridní využití Azure pro prostředí Integration Runtime: **Ano** nebo **ne**. Vyberte **Ano** , pokud chcete využít vlastní licenci SQL Server se Software Assurance, abyste využili úspory nákladů s využitím hybridního použití.

   8. Vyberte **Další**.

#### <a name="deployment-settings-page"></a>Stránka nastavení nasazení

Na stránce **nastavení nasazení** v podokně **instalace prostředí Integration runtime** máte možnost vytvářet SSISDB a nebo Azure-SSIS IR úložiště balíčků.

##### <a name="creating-ssisdb"></a>Vytváření SSISDB

Pokud chcete nasadit balíčky do SSISDB (model nasazení projektu), vyberte na stránce **nastavení nasazení** v podokně **instalace prostředí Integration runtime** možnost **vytvořit katalog SSIS (SSISDB), která je hostována Azure SQL Database serverem nebo spravovanou instancí, a zaškrtněte políčko pro uložení projektů, balíčků/prostředí/spuštění protokolů** . Případně, pokud chcete balíčky nasadit do systému souborů, souborů Azure nebo databáze služby SQL Server Database (MSDB), která je hostovaná pomocí spravované instance Azure SQL (model nasazení balíčku), nemusíte vytvářet SSISDB ani políčko.

Bez ohledu na váš model nasazení, pokud chcete použít agenta SQL Server hostovaného službou Azure SQL Managed instance za účelem orchestrace nebo naplánování spuštění balíčku, je povolená SSISDB, takže zaškrtněte políčko. Další informace najdete v tématu [plánování spouštění balíčků SSIS prostřednictvím agenta spravované instance Azure SQL](./how-to-invoke-ssis-package-managed-instance-agent.md).
   
Pokud zaškrtnete toto políčko, proveďte následující kroky, abyste mohli přenést svůj vlastní databázový server do hostitele SSISDB, který vytvoříme a spravujeme vaším jménem.

   ![Nastavení nasazení pro SSISDB](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings.png)
   
   1. Jako **Předplatné** vyberte předplatné Azure, které obsahuje váš databázový server pro hostování databáze SSISDB. 

   1. Jako **Umístění** vyberte umístění vašeho databázového serveru pro hostování databáze SSISDB. Doporučujeme vybrat stejné umístění, jako má vaše prostředí Integration Runtime.

   1. Jako **Koncový bod databázového serveru katalogu** vyberte koncový bod vašeho databázového serveru pro hostování databáze SSISDB. 
   
      V závislosti na vybraném databázovém serveru může být instance SSISDB vytvořená vaším jménem jako jediná databáze, jako součást elastického fondu nebo ve spravované instanci. Může být přístupný ve veřejné síti nebo prostřednictvím připojení k virtuální síti. Pokyny k výběru typu databázového serveru pro hostování SSISDB naleznete v tématu [Compare SQL Database a SQL Managed instance](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance).   

      Pokud vyberete Azure SQL Database Server s pravidly brány firewall protokolu IP/koncovými body služby virtuální sítě nebo spravovanou instancí s privátním koncovým bodem hostitele SSISDB, nebo pokud budete vyžadovat přístup k místním datům bez konfigurace místního prostředí IR, budete se muset připojit k Azure-SSIS IR k virtuální síti. Další informace najdete v tématu [připojení Azure-SSIS IR k virtuální síti](./join-azure-ssis-integration-runtime-virtual-network.md).

   1. Zaškrtněte políčko pro výběr metody ověřování pro databázový server pro hostování SSISDB **použít ověřování Azure AD se spravovaným identitou pro váš** databázový server. Zvolíte ověřování SQL nebo ověřování Azure AD se spravovanou identitou pro vaši datovou továrnu.

      Pokud políčko zaškrtnete, budete muset přidat spravovanou identitu pro vaši datovou továrnu do skupiny Azure AD s přístupovými oprávněními k vašemu databázovému serveru. Další informace najdete v tématu [Povolení ověřování Azure AD pro Azure-SSIS IR](./enable-aad-authentication-azure-ssis-ir.md).
   
   1. Jako **uživatelské jméno správce** zadejte uživatelské jméno pro ověřování SQL vašeho databázového serveru pro hostování SSISDB. 

   1. Jako **heslo správce** zadejte heslo pro ověřování SQL vašeho databázového serveru pro hostování SSISDB. 

   1. Zaškrtněte políčko **použít dvojici duálního Azure-SSIS Integration runtime úsporného režimu s převzetím služeb při selhání SSISDB** , abyste NAKONFIGUROVALI SSIS infračervený přenos s duálním pohotovostním režimem, který spolupracuje se skupinou s převzetím služeb při selhání Azure SQL Database/spravované instance pro zajištění kontinuity podnikových
   
      Pokud zaškrtnete políčko, zadejte název pro identifikaci páru primárních a sekundárních úřadů Azure-SSIS v textovém poli **název páru se dvěma pohotovostními** pomocným polem. Při vytváření primárního a sekundárního finančního úřadu Azure-SSIS je potřeba zadat stejný název páru.

      Další informace najdete v tématu [konfigurace Azure-SSIS IR pro BCDR](./configure-bcdr-azure-ssis-integration-runtime.md).

   1. Pro **úroveň služby databáze katalogu** vyberte úroveň služby pro váš databázový server pro hostování SSISDB. Vyberte úroveň Basic, Standard nebo Premium nebo vyberte název elastického fondu.

Vyberte možnost **Testovat připojení** , pokud je to možné, a pokud je to úspěšné, vyberte **Další**.

> [!NOTE]
   > Pokud používáte Azure SQL Database Server k hostování SSISDB, budou se data ve výchozím nastavení ukládat v geograficky redundantním úložišti pro zálohy. Pokud nechcete, aby byla vaše data replikována v jiných oblastech, postupujte podle pokynů ke [konfiguraci redundance úložiště zálohování pomocí PowerShellu](../azure-sql/database/automated-backups-overview.md?tabs=single-database#configure-backup-storage-redundancy-by-using-powershell).
   
##### <a name="creating-azure-ssis-ir-package-stores"></a>Vytváření Azure-SSIS IRch úložišť balíčků

Pokud chcete spravovat balíčky nasazené do MSDB, systém souborů nebo soubory Azure (model nasazení balíčku) pomocí Azure-SSIS IRch úložišť balíčků, na stránce **nastavení nasazení** v podokně nastavení **prostředí Integration runtime** vyberte **úložiště balíčků pro správu balíčků, které jsou nasazeny do systému souborů/soubory Azure SQL Server/databáze aplikace (msdb) hostované službou Azure SQL Managed instance** .
   
Azure-SSIS IR úložiště balíčků umožňuje importovat/exportovat, odstraňovat/spouštět balíčky a monitorovat nebo zastavovat balíčky přes SSMS podobně jako [starší verze úložiště balíčků SSIS](/sql/integration-services/service/package-management-ssis-service). Další informace najdete v tématu [Správa balíčků SSIS pomocí Azure-SSIS IRch balíčků](./azure-ssis-integration-runtime-package-store.md).
   
Pokud toto políčko zaškrtnete, můžete do svého Azure-SSIS IR přidat několik úložišť balíčků, a to tak, že vyberete **Nový**. Naopak jeden obchod s balíčky může sdílet několik SSISch úřadů Azure-.

![Nastavení nasazení pro MSDB/File System/soubory Azure](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings2.png)

V podokně **Přidat úložiště balíčků** proveďte následující kroky.
   
   1. Do pole **název úložiště balíčků** zadejte název úložiště balíčků. 

   1. Pro **propojenou službu úložiště balíčků** vyberte existující propojenou službu, která uchovává informace o přístupu pro systém souborů/soubory Azure/spravovanou instanci Azure SQL, kde jsou balíčky nasazené, nebo vytvořte novou výběrem možnosti **Nový**. V podokně **Nová propojená služba** proveďte následující kroky.
   
      > [!NOTE]
      > Pro přístup k souborům Azure můžete použít buď službu **azure File Storage** , nebo propojené služby **systému souborů** . Pokud používáte propojenou službu **Azure File Storage** , Azure-SSIS IR úložiště balíčků pro teď podporuje jenom **základní** metodu ověřování (ne **klíč účtu** ani **SAS URI**). Pokud chcete pro propojenou službu **Azure File Storage** použít **základní** ověřování, můžete `?feature.upgradeAzureFileStorage=false` se připojit k adrese URL portálu ADF v prohlížeči. Alternativně můžete místo toho použít propojenou službu **systému souborů** k přístupu k souborům Azure. 

      ![Nastavení nasazení pro propojené služby](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings-linked-service.png)

      1. Do pole **název** zadejte název propojené služby. 
         
      1. Jako **Popis** zadejte popis propojené služby. 
         
      1. Jako **typ** vyberte **Azure File Storage**, **spravovaná instance Azure SQL** nebo **systém souborů**.

      1. Připojení můžete ignorovat **prostřednictvím prostředí Integration runtime**, protože k získání informací o přístupu k úložištím balíčků vždycky používáme váš Azure-SSIS IR.

      1. Pokud vyberete **File Storage Azure**, proveďte následující kroky. 

         1. Pro **metodu výběru účtu** vyberte možnost **z předplatného Azure** nebo **Zadejte ručně**.
         
         1. Pokud vyberete možnost **z předplatného Azure**, vyberte příslušné **předplatné Azure**, **název účtu úložiště** a **sdílenou složku**.
            
         1. Pokud vyberete **zadat ručně**, zadejte `\\<storage account name>.file.core.windows.net\<file share name>` pro možnost **hostitel**, `Azure\<storage account name>` **uživatelské jméno** a `<storage account key>` **heslo** nebo vyberte **Azure Key Vault** , kde je uložený jako tajný kód.

      1. Pokud vyberete **spravovanou instanci Azure SQL**, proveďte následující kroky. 

         1. Vyberte **připojovací řetězec** , který chcete zadat ručně, nebo **Azure Key Vault** , kde je uložený jako tajný kód.
         
         1. Pokud vyberete **připojovací řetězec**, proveďte následující kroky. 

            1. Pro **plně kvalifikovaný název domény** zadejte `<server name>.<dns prefix>.database.windows.net` nebo `<server name>.public.<dns prefix>.database.windows.net,3342` jako privátní nebo veřejný koncový bod spravované instance SQL Azure v uvedeném pořadí. Pokud zadáte privátní koncový bod, **testovací připojení** se nedá použít, protože uživatelské rozhraní ADF k němu nemůže získat přístup.

            1. Jako **název databáze** zadejte `msdb` .
               
            1. Jako **typ ověřování** vyberte **ověřování SQL**, **spravovaná identita** nebo **instanční objekt**.

            1. Pokud vyberete **ověřování SQL**, zadejte příslušné **uživatelské jméno** a **heslo** nebo vyberte **Azure Key Vault** , kde se uloží jako tajný kód.

            1. Pokud vyberete **spravovaná identita**, udělte vašemu spravovanému přístupu přes správu ADF přístup k vaší spravované instanci SQL Azure.

            1. Pokud vyberete **instanční objekt**, zadejte příslušné **ID instančního objektu** a **klíč instančního objektu** nebo vyberte **Azure Key Vault** , kde je uložený jako tajný kód.

      1. Pokud vyberete **systém souborů**, zadejte cestu UNC ke složce, ve které jsou balíčky nasazeny pro **hostitele**, a také příslušné **uživatelské jméno** a **heslo** nebo vyberte **Azure Key Vault** , kde jsou uloženy jako tajné.

      1. Vyberte možnost **Testovat připojení** , pokud je to možné, a pokud je to úspěšné, vyberte **vytvořit**.

   1. Vaše přidaná úložiště balíčků se zobrazí na stránce **nastavení nasazení** . Pokud je chcete odebrat, zaškrtněte příslušná políčka a potom vyberte **Odstranit**.

Vyberte možnost **Testovat připojení** , pokud je to možné, a pokud je to úspěšné, vyberte **Další**.

#### <a name="advanced-settings-page"></a>Stránka Upřesnit nastavení

Na stránce **Upřesnit nastavení** v podokně **instalace prostředí Integration runtime** proveďte následující kroky.

   ![Rozšířená nastavení](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. Pro **maximální paralelní spouštění na uzel** vyberte maximální počet balíčků, které mají být spuštěny souběžně na jeden uzel v clusteru Integration runtime. Zobrazí se pouze podporované počty balíčků. Vyberte nízké číslo, pokud chcete použít více než jedno jádro pro spuštění jednoho velkého balíčku, který je náročné na výpočetní výkon nebo paměť. Vyberte vysoké číslo, pokud chcete spustit jeden nebo více malých balíčků v jednom jádru.

   1. Zaškrtněte políčko **přizpůsobit Azure-SSIS Integration runtime pomocí dalších systémových konfigurací/instalací součástí** a zvolte, zda chcete do Azure-SSIS IR přidat vlastní nastavení Standard/Express. Další informace najdete v tématu [vlastní nastavení pro Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md).

      Pokud zaškrtnete políčko, proveďte následující kroky.

      ![Rozšířená nastavení s vlastními nastaveními](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)
   
      1. Pro **vlastní nastavení identifikátoru URI SAS kontejneru** zadejte identifikátor URI SAS kontejneru, kam ukládáte skripty a přidružené soubory pro standardní vlastní nastavení.

      1. Pro **expresní vlastní instalaci** vyberte **Nový** , aby se otevřel panel **Přidat expresní vlastní nastavení** , a pak vyberte libovolné typy v rozevírací nabídce **typ rychlé vlastní instalace** , třeba **příkaz run cmdkey**, **přidejte proměnnou prostředí**, **Nainstalujte licencovanou komponentu** atd.

         Pokud vyberete možnost **instalovat licencovaný typ součásti** , pak můžete vybrat všechny integrované komponenty z našich partnerů ISV v rozevírací nabídce **název součásti** a v případě potřeby zadat licenční klíč produktu/Odeslat soubor s licencí k produktu, který jste si z nich koupili, do  / pole **licenční** kód licenčního klíče.
  
         Přidaná expresní vlastní nastavení se zobrazí na stránce **Upřesnit nastavení** . Pokud je chcete odebrat, můžete zaškrtnout políčka a pak vybrat **Odstranit**.

   1. Vyberte **virtuální síť, ke které se má Azure-SSIS Integration runtime připojit, povolte ADF, aby se vytvořily určité síťové prostředky, a volitelně můžete použít vlastní statické veřejné IP adresy,** abyste si zvolili, jestli se chcete připojit ke službě Integration runtime k virtuální síti. 

      Tuto možnost vyberte, pokud používáte server Azure SQL Database s pravidly brány firewall protokolu IP nebo koncovými body služby virtuální sítě nebo spravovanou instancí s privátním koncovým bodem hostitele SSISDB, nebo pokud požadujete přístup k místním datům (to znamená, že máte místní zdroje dat nebo cíle v balíčcích SSIS), aniž byste museli nakonfigurovat prostředí IR v místním prostředí. Další informace najdete v tématu [připojení Azure-SSIS IR k virtuální síti](./join-azure-ssis-integration-runtime-virtual-network.md). 

      Pokud zaškrtnete políčko, proveďte následující kroky.

      ![Rozšířené nastavení virtuální sítě](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

      1. V poli **předplatné** vyberte předplatné Azure, které má vaši virtuální síť.

      1. Pro **umístění** je vybrané stejné umístění prostředí Integration runtime.

      1. Jako **typ** vyberte typ virtuální sítě: classic nebo Azure Resource Manager. Doporučujeme vybrat Azure Resource Manager virtuální síť, protože klasické virtuální sítě budou brzy zastaralé.

      1. V poli **název virtuální** sítě vyberte název vaší virtuální sítě. Měl by být stejný jako použitý pro váš Azure SQL Database Server s koncovými body služby virtuální sítě nebo spravovanou instancí s privátním koncovým bodem pro hostování SSISDB. Nebo by měl být stejný jako připojený k vaší místní síti. V opačném případě může být libovolná virtuální síť, která bude obsahovat vlastní statické veřejné IP adresy pro Azure-SSIS IR.

      1. Jako **název podsítě** vyberte název podsítě pro virtuální síť. Měla by být stejná jako ta, která se používá pro váš Azure SQL Database Server s koncovými body služby virtuální sítě pro hostování SSISDB. Nebo by měla být jiná podsíť než ta, která se používá pro vaši spravovanou instanci s privátním koncovým bodem pro hostování SSISDB. V opačném případě může to být jakákoli podsíť, která přináší vlastní statické veřejné IP adresy pro Azure-SSIS IR.

      1. Zaškrtněte políčko **přenést statické veřejné IP adresy pro Azure-SSIS Integration runtime** a zvolte, jestli chcete použít vlastní statické veřejné IP adresy pro Azure-SSIS IR, abyste je mohli v bráně firewall pro zdroje dat zapnout.

         Pokud zaškrtnete políčko, proveďte následující kroky.

         1. U **první statické veřejné IP adresy** vyberte první STATICKOU veřejnou IP adresu, která splňuje požadavky pro váš Azure-SSIS IR. Pokud žádné nemáte, klikněte na **vytvořit nový** odkaz pro vytvoření statických veřejných IP adres na Azure Portal a pak klikněte na tlačítko Aktualizovat, abyste je mohli vybrat.
      
         1. U **druhé statické veřejné IP adresy** vyberte druhou STATICKOU veřejnou IP adresu, která splňuje požadavky pro váš Azure-SSIS IR. Pokud žádné nemáte, klikněte na **vytvořit nový** odkaz pro vytvoření statických veřejných IP adres na Azure Portal a pak klikněte na tlačítko Aktualizovat, abyste je mohli vybrat.

   1. Zaškrtněte políčko **nastavit Self-Hosted Integration runtime jako proxy pro Azure-SSIS Integration runtime** zaškrtávací políčko pro výběr, zda chcete pro Azure-SSIS IR nakonfigurovat jako proxy místní prostředí IR jako proxy. Další informace najdete v tématu [nastavení místního hostitele IR jako proxy](./self-hosted-integration-runtime-proxy-ssis.md). 

      Pokud zaškrtnete políčko, proveďte následující kroky.

      ![Rozšířená nastavení s místním prostředím IR](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

      1. Pro **Integration runtime** v místním prostředí vyberte pro Azure-SSIS IR existující místní prostředí IR jako proxy.

      1. V případě **pracovní služby s** ochranou úložiště vyberte existující propojenou službu Azure Blob Storage nebo vytvořte novou pro přípravu.

      1. Do pole **pracovní cesta** zadejte kontejner objektů BLOB ve vybraném účtu služby Azure Blob Storage nebo ho nechte prázdný, aby se pro přípravu použil výchozí.

   1. Vyberte pokračovat v **ověřování virtuální** sítě  >  . 

V části **Souhrn** Zkontrolujte všechna nastavení zřizování, záložku doporučené odkazy na dokumentaci a vyberte **Dokončit** a spusťte tak vytváření prostředí Integration runtime.

   > [!NOTE]
   > S výjimkou času vlastní instalace by tento proces měl skončit do 5 minut. Může ale trvat 20-30 minut, než se Azure-SSIS IR připojit k virtuální síti.
   >
   > Pokud používáte SSISDB, Služba Data Factory se připojí k vašemu databázovému serveru, aby připravil SSISDB. Také nakonfiguruje oprávnění a nastavení vaší virtuální sítě, pokud je zadáte, a připojí vaše Azure-SSIS IR k virtuální síti.
   > 
   > Když zřizujete Azure-SSIS IR, nainstaluje se také Distribuovatelný balíček a sada Azure Feature Pack pro SSIS. Tyto komponenty poskytují připojení k excelovým souborům, přístup k souborům a různým zdrojům dat Azure a navíc ke zdrojům dat, které jsou již podporovány v rámci integrovaných komponent. Další informace o předdefinovaných/předinstalovaných součástech najdete v tématu [vestavěné/předinstalované komponenty na Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md). Další informace o dalších součástech, které můžete nainstalovat, najdete v tématu [vlastní nastavení pro Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md).

#### <a name="connections-pane"></a>Podokno připojení

V podokně **připojení** v části **Spravovat** centrum přepněte na stránku **prostředí Integration runtime** a vyberte **aktualizovat**. 

   ![Podokno připojení](./media/tutorial-create-azure-ssis-runtime-portal/connections-pane.png)

   Azure-SSIS IR můžete upravit nebo překonfigurovat tak, že vyberete jeho název. Můžete také vybrat relevantní tlačítka pro monitorování, spouštění, zastavování a odstraňování Azure-SSIS IR, automatické vygenerování kanálu ADF s aktivitou spustit balíček SSIS ke spuštění v Azure-SSIS IR a zobrazení kódu JSON nebo datové části vašeho Azure-SSIS IR.  Úpravy a odstranění Azure-SSIS IR lze provést pouze v případě, že je zastaveno.

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Prostředí Azure SSIS Integration Runtime na portálu

1. V uživatelském rozhraní Azure Data Factory přepněte na kartu **Upravit** a vyberte **připojení**. Pak přejděte na kartu **prostředí Integration runtime** , kde se zobrazí existující prostředí Integration runtime ve vaší datové továrně.

   ![Zobrazení existujících IR](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Vyberte **Nový** , pokud chcete vytvořit nový Azure-SSIS IR a otevřete podokno **instalace prostředí Integration runtime** .

   ![Prostředí Integration Runtime prostřednictvím nabídky](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. V podokně **instalace prostředí Integration runtime** vyberte dlaždici **převedené a posunutí existující SSIS balíčky, které se mají spustit v Azure** , a pak vyberte **Další**.

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
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|InstallAzurePowerShell|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS|AecorSoft.IntegrationService|CData.Standard|CData.Extended or leave it empty]" # OPTIONAL to configure an express custom setup without script
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

Přidejte následující skript pro přihlášení a výběr vašeho předplatného Azure.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database-server"></a>Ověření připojení k databázovému serveru

Přidejte následující skript, který ověří Azure SQL Database Server nebo spravovanou instanci.

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

Pokud nepoužíváte server Azure SQL Database s pravidly brány firewall protokolu IP/koncovými body služby virtuální sítě nebo spravovanou instancí s privátním koncovým bodem pro hostování SSISDB nebo vyžadujete přístup k místním datům, můžete `VNetId` vynechat `Subnet` parametry a nebo pro ně předat prázdné hodnoty. Můžete je také vynechat, pokud nakonfigurujete místní prostředí IR jako proxy pro Azure-SSIS IR pro přístup k datům v místním prostředí. V opačném případě je nemůžete vynechat a musí předávat platné hodnoty z konfigurace vaší virtuální sítě. Další informace najdete v tématu [připojení Azure-SSIS IR k virtuální síti](./join-azure-ssis-integration-runtime-virtual-network.md).

Pokud používáte spravovanou instanci pro hostování SSISDB, můžete `CatalogPricingTier` parametr vynechat nebo pro něj předat prázdnou hodnotu. V opačném případě ho nemůžete vynechat a musí předávat platnou hodnotu ze seznamu podporovaných cenových úrovní pro Azure SQL Database. Další informace najdete v tématu [SQL Database omezení prostředků](../azure-sql/database/resource-limits-logical-server.md).

Pokud pro připojení k databázovému serveru použijete ověřování Azure AD se spravovanou identitou, můžete tento `CatalogAdminCredential` parametr vynechat. Je ale nutné přidat spravovanou identitu pro vaši datovou továrnu do skupiny Azure AD s přístupovými oprávněními k databázovému serveru. Další informace najdete v tématu [Povolení ověřování Azure AD pro Azure-SSIS IR](./enable-aad-authentication-azure-ssis-ir.md). V opačném případě ji nemůžete vynechat a musí předat platný objekt vytvořený z uživatelského jména a hesla správce serveru pro ověřování SQL.

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
    if($ExpressCustomSetup -eq "InstallAzurePowerShell")
    {
        $moduleVersion = "YourAzModuleVersion"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.AzPowerShellSetup($moduleVersion)
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
    if($ExpressCustomSetup -eq "AecorSoft.IntegrationService")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Standard")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Extended")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
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
> Když zřizujete Azure-SSIS IR, nainstaluje se také Distribuovatelný balíček a sada Azure Feature Pack pro SSIS. Tyto komponenty poskytují připojení k excelovým souborům, přístup k souborům a různým zdrojům dat Azure a navíc ke zdrojům dat, které jsou již podporovány v rámci integrovaných komponent. Další informace o předdefinovaných/předinstalovaných součástech najdete v tématu [vestavěné/předinstalované komponenty na Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md). Další informace o dalších součástech, které můžete nainstalovat, najdete v tématu [vlastní nastavení pro Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md).

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
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|InstallAzurePowerShell|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS|AecorSoft.IntegrationService|CData.Standard|CData.Extended or leave it empty]" # OPTIONAL to configure an express custom setup without script
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
    if($ExpressCustomSetup -eq "InstallAzurePowerShell")
    {
        $moduleVersion = "YourAzModuleVersion"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.AzPowerShellSetup($moduleVersion)
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
    if($ExpressCustomSetup -eq "AecorSoft.IntegrationService")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Standard")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "CData.Extended")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
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

2. Chcete-li nasadit šablonu Azure Resource Manager, spusťte `New-AzResourceGroupDeployment` příkaz, jak je znázorněno v následujícím příkladu. V tomto příkladu `ADFTutorialResourceGroup` je název vaší skupiny prostředků. `ADFTutorialARM.json` je soubor, který obsahuje definici JSON pro datovou továrnu a Azure-SSIS IR.

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
> Když zřizujete Azure-SSIS IR, nainstaluje se také Distribuovatelný balíček a sada Azure Feature Pack pro SSIS. Tyto komponenty poskytují připojení k excelovým souborům, přístup k souborům a různým zdrojům dat Azure a navíc ke zdrojům dat, které jsou již podporovány v rámci integrovaných komponent. Další informace o předdefinovaných/předinstalovaných součástech najdete v tématu [vestavěné/předinstalované komponenty na Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md). Další informace o dalších součástech, které můžete nainstalovat, najdete v tématu [vlastní nastavení pro Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="deploy-ssis-packages"></a>Nasazení balíčků SSIS

Pokud používáte SSISDB, můžete do něj nasadit balíčky a spouštět je v Azure-SSIS IR pomocí nástrojů SSDT nebo SSMS s podporou Azure. Tyto nástroje se připojují k vašemu databázovému serveru prostřednictvím koncového bodu serveru: 

- Pro server Azure SQL Database je formát koncového bodu serveru `<server name>.database.windows.net` .
- Pro spravovanou instanci s privátním koncovým bodem je formát koncového bodu serveru `<server name>.<dns prefix>.database.windows.net` .
- Pro spravovanou instanci s veřejným koncovým bodem je formát koncového bodu serveru `<server name>.public.<dns prefix>.database.windows.net,3342` . 

Pokud SSISDB nepoužíváte, můžete své balíčky nasadit do systému souborů, souborů Azure nebo MSDB hostovaného vaší spravovanou instancí Azure SQL a spouštět je v Azure-SSIS IR pomocí nástrojů příkazového řádku [dtutil](/sql/integration-services/dtutil-utility) a [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md) . 

Další informace najdete v tématu [nasazení projektů a balíčků SSIS](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages).

V obou případech můžete také spouštět nasazené balíčky na Azure-SSIS IR pomocí aktivity spustit balíček SSIS v Data Factorych kanálech. Další informace najdete v tématu [vyvolání spuštění balíčku SSIS jako aktivity první třídy Data Factory](./how-to-invoke-ssis-package-ssis-activity.md).

## <a name="next-steps"></a>Další kroky

Další témata Azure-SSIS IR v této dokumentaci:

- [Prostředí Azure-SSIS Integration runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Tento článek obsahuje obecné informace o prostředí Integration runtime, včetně Azure-SSIS IR.
- [Monitorování Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). V tomto článku se dozvíte, jak načíst a pochopit informace o Azure-SSIS IR.
- [Správa Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). V tomto článku se dozvíte, jak zastavit, spustit nebo odstranit Azure-SSIS IR. Ukazuje také způsob horizontálního navýšení kapacity Azure-SSIS IR přidáním dalších uzlů.
- [Nasazení, spuštění a monitorování balíčků SSIS v Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Připojení k SSISDB v Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Připojení k místním zdrojům dat s využitím ověřování systému Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 
- [Plánování spouštění balíčků v Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)