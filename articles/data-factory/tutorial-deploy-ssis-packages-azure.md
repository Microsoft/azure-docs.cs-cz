---
title: Zřízení prostředí Azure-SSIS Integration runtime
description: Zjistěte, jak zřídit prostředí Azure-SSIS Integration Runtime ve službě Azure Data Factory, abyste v Azure mohli nasazovat a spouštět balíčky SSIS.
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 02/22/2021
author: swinarko
ms.author: sawinark
ms.openlocfilehash: 7c439d71806d2deba508ce35131f21ebfbd7a3ec
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740405"
---
# <a name="provision-the-azure-ssis-integration-runtime-in-azure-data-factory"></a>Zřízení prostředí Azure-SSIS Integration runtime v Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

V tomto kurzu najdete postup, jak pomocí Azure Portal zřídit prostředí Azure-služba SSIS (SQL Server Integration Services) (IR) Integration runtime (SSIS) v Azure Data Factory (ADF). Azure-SSIS IR podporuje:

- Spouštění balíčků nasazených do katalogu SSIS (SSISDB) Azure SQL Database hostovaných serverem/spravovanou instancí (model nasazení projektu)
- Spouštění balíčků nasazených do systému souborů, souborů Azure nebo databáze služby SQL Server Database (MSDB) hostované službou Azure SQL Managed instance (model nasazení balíčku)

Po zřízení Azure-SSIS IR můžete pomocí známých nástrojů nasadit a spustit balíčky v Azure. Tyto nástroje jsou již povoleny v Azure a zahrnují nástroje SQL Server Data Tools (SSDT), SQL Server Management Studio (SSMS) a nástroje příkazového řádku, jako je [dtutil](/sql/integration-services/dtutil-utility) a [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md).

Informace o konceptu prostředí Azure-SSIS IR najdete v [přehledu prostředí Azure-SSIS Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime).

V tomto kurzu dokončíte následující kroky:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Zřízení prostředí Azure-SSIS Integration Runtime

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Předplatné Azure**. Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/).

- **Server Azure SQL Database (volitelné)**. Pokud ještě nemáte databázový server, vytvořte si ho na webu Azure Portal před tím, než začnete. Data Factory pak na tomto databázovém serveru vytvoří instanci SSISDB. 

  Doporučujeme vytvořit databázový server ve stejné oblasti Azure jako prostředí Integration Runtime. Tato konfigurace umožňuje prostředí Integration runtime zapisovat protokoly spouštění do SSISDB bez přechodu do oblastí Azure.

  Pamatujte na tyto body:

  - V závislosti na vybraném databázovém serveru může být instance SSISDB vytvořená vaším jménem jako jediná databáze, jako součást elastického fondu nebo ve spravované instanci. Může být přístupný ve veřejné síti nebo prostřednictvím připojení k virtuální síti. Pokyny k výběru typu databázového serveru pro hostování SSISDB naleznete v tématu [Compare SQL Database a SQL Managed instance](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance). 
  
    Pokud používáte server Azure SQL Database s pravidly brány firewall protokolu IP nebo koncovými body služby virtuální sítě nebo spravovanou instancí s privátním koncovým bodem hostitele SSISDB, nebo pokud budete vyžadovat přístup k místním datům bez konfigurace místního prostředí IR, musíte se připojit k vaší Azure-SSIS IR k virtuální síti. Další informace najdete v tématu [vytvoření Azure-SSIS IR ve virtuální síti](./create-azure-ssis-integration-runtime.md).

  - Potvrďte, že nastavení **Povolit přístup ke službám Azure** je pro databázový server povolené. Toto nastavení se nedá použít, když použijete server Azure SQL Database s pravidly brány firewall protokolu IP nebo koncovými body služby virtuální sítě nebo spravovanou instancí s privátním koncovým bodem pro hostování SSISDB. Další informace najdete v tématu [zabezpečená Azure SQL Database](../azure-sql/database/secure-database-tutorial.md#create-firewall-rules). Pokud chcete toto nastavení povolit pomocí PowerShellu, přečtěte si článek [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).

  - Přidejte IP adresu klientského počítače nebo rozsah IP adres, který obsahuje IP adresu klientského počítače, do seznamu IP adres klienta v nastavení brány firewall pro databázový server. Další informace najdete v tématu [Pravidla brány firewall na úrovni serveru a databáze služby Azure SQL Database](../azure-sql/database/firewall-configure.md).

  - K databázovému serveru se můžete připojit pomocí ověřování SQL s přihlašovacími údaji správce serveru nebo pomocí ověřování Azure AD se spravovanou identitou pro vaši datovou továrnu. V takovém případě je potřeba přidat spravovanou identitu pro vaši datovou továrnu do skupiny Azure AD s přístupovými oprávněními k databázovému serveru. Další informace najdete v tématu [vytvoření Azure-SSIS IR s ověřováním Azure AD](./create-azure-ssis-integration-runtime.md).

  - Ověřte, že váš databázový server ještě nemá instanci SSISDB. Zřizování Azure-SSIS IR nepodporuje použití existující instance SSISDB.

> [!NOTE]
> Seznam oblastí Azure, ve kterých jsou aktuálně k dispozici Data Factory a Azure-SSIS IR, najdete v části [Data Factory a SSIS IR dostupnost v oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all). 

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

Pokud chcete vytvořit datovou továrnu pomocí Azure Portal, postupujte podle podrobných pokynů v tématu [Vytvoření datové továrny prostřednictvím uživatelského rozhraní](./quickstart-create-data-factory-portal.md#create-a-data-factory). Když to uděláte, vyberte **Připnout na řídicí panel** , abyste po jeho vytvoření povolili rychlý přístup. 

Po vytvoření objektu pro vytváření dat otevřete jeho stránku Přehled v Azure Portal. Výběrem dlaždice **autora & monitorování** otevřete stránku **Začínáme** na samostatné kartě. Zde můžete pokračovat v vytváření Azure-SSIS IR.

## <a name="create-an-azure-ssis-integration-runtime"></a>Vytvoření prostředí Azure-SSIS Integration Runtime

### <a name="from-the-data-factory-overview"></a>Z přehledu Data Factory

1. Na stránce **Začínáme** vyberte dlaždici **Konfigurace prostředí SSIS Integration Runtime**. 

   ![Dlaždice Konfigurace prostředí SSIS Integration Runtime](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Zbývající kroky nastavení prostředí Azure-SSIS IR najdete v části [Zřízení prostředí Azure-SSIS Integration Runtime](#provision-an-azure-ssis-integration-runtime). 

### <a name="from-the-authoring-ui"></a>Z uživatelského rozhraní pro vytváření obsahu

1. V uživatelském rozhraní Azure Data Factory přepněte na kartu **Upravit** a vyberte **připojení**. Pak přejděte na kartu **prostředí Integration runtime** , kde se zobrazí existující prostředí Integration runtime ve vaší datové továrně. 

   ![Výběr možností za účelem zobrazení stávajících prostředí IR](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Vyberte **Nový** , chcete-li vytvořit Azure-SSIS IR a otevřete podokno **instalace prostředí Integration runtime** . 

   ![Prostředí Integration Runtime prostřednictvím nabídky](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. V podokně **instalace prostředí Integration runtime** vyberte dlaždici **převedené a posunutí existující SSIS balíčky, které se mají spustit v Azure** , a pak vyberte **Další**.

   ![Zadání typu prostředí Integration Runtime](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. Zbývající kroky nastavení prostředí Azure-SSIS IR najdete v části [Zřízení prostředí Azure-SSIS Integration Runtime](#provision-an-azure-ssis-integration-runtime). 

## <a name="provision-an-azure-ssis-integration-runtime"></a>Zřízení prostředí Azure-SSIS Integration Runtime

Podokno **instalace prostředí Integration runtime** má tři stránky, na kterých jste úspěšně nakonfigurovali obecné, nasazení a pokročilá nastavení.

### <a name="general-settings-page"></a>Stránka Obecné nastavení

Na stránce **Obecné nastavení** v podokně **instalace prostředí Integration runtime** proveďte následující kroky. 

   ![Obecná nastavení](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. Jako **Název** zadejte název vašeho prostředí Integration Runtime. 

   1. Jako **Popis** zadejte popis vašeho prostředí Integration Runtime. 

   1. Jako **Umístění** vyberte umístění vašeho prostředí Integration Runtime. Zobrazí se pouze podporovaná umístění. Doporučujeme vybrat stejné umístění, jako má váš databázový server pro hostování databáze SSISDB. 

   1. Jako **Velikost uzlu** vyberte velikost uzlu ve vašem clusteru Integration Runtime. Zobrazí se pouze podporované velikosti uzlů. Pokud chcete spustit mnoho balíčků náročných na výpočetní výkon nebo paměť s náročnou na paměť, vyberte velikost velkého uzlu (horizontální navýšení kapacity). 

   1. Jako **Počet uzlů** vyberte počet uzlů ve vašem clusteru Integration Runtime. Zobrazí se pouze podporované počty uzlů. Vyberte velký cluster s mnoha uzly (horizontální navýšení kapacity), pokud chcete souběžně spouštět mnoho balíčků. 

   1. V části **edice/licence** vyberte edici SQL Server pro modul runtime integrace: Standard nebo Enterprise. Vyberte možnost Enterprise, pokud chcete používat pokročilé funkce v prostředí Integration runtime. 

   1. Pro **Uložit peníze** vyberte možnost zvýhodněné hybridní využití Azure pro prostředí Integration Runtime: **Ano** nebo **ne**. Vyberte **Ano** , pokud chcete využít vlastní licenci SQL Server se Software Assurance, abyste využili úspory nákladů s využitím hybridního použití. 

   1. Vyberte **Další**. 

### <a name="deployment-settings-page"></a>Stránka nastavení nasazení

Na stránce **nastavení nasazení** v podokně **instalace prostředí Integration runtime** máte možnost vytvářet SSISDB a nebo Azure-SSIS IR úložiště balíčků.

#### <a name="creating-ssisdb"></a>Vytváření SSISDB

Pokud chcete nasadit balíčky do SSISDB (model nasazení projektu), vyberte na stránce **nastavení nasazení** v podokně **instalace prostředí Integration runtime** možnost **vytvořit katalog SSIS (SSISDB), která je hostována Azure SQL Database serverem nebo spravovanou instancí, a zaškrtněte políčko pro uložení projektů, balíčků/prostředí/spuštění protokolů** . Případně, pokud chcete balíčky nasadit do systému souborů, souborů Azure nebo databáze služby SQL Server Database (MSDB), která je hostovaná pomocí spravované instance Azure SQL (model nasazení balíčku), nemusíte vytvářet SSISDB ani políčko.

Bez ohledu na váš model nasazení, pokud chcete použít agenta SQL Server hostovaného službou Azure SQL Managed instance za účelem orchestrace nebo naplánování spuštění balíčku, je povolená SSISDB, takže zaškrtněte políčko. Další informace najdete v tématu [plánování spouštění balíčků SSIS prostřednictvím agenta spravované instance Azure SQL](./how-to-invoke-ssis-package-managed-instance-agent.md).
   
Pokud zaškrtnete toto políčko, proveďte následující kroky, abyste mohli přenést svůj vlastní databázový server do hostitele SSISDB, který vytvoříme a spravujeme vaším jménem.

   ![Nastavení nasazení pro SSISDB](./media/tutorial-create-azure-ssis-runtime-portal/deployment-settings.png)
   
   1. Jako **Předplatné** vyberte předplatné Azure, které obsahuje váš databázový server pro hostování databáze SSISDB. 

   1. Jako **Umístění** vyberte umístění vašeho databázového serveru pro hostování databáze SSISDB. Doporučujeme vybrat stejné umístění, jako má vaše prostředí Integration Runtime.

   1. Jako **Koncový bod databázového serveru katalogu** vyberte koncový bod vašeho databázového serveru pro hostování databáze SSISDB. 
   
      V závislosti na vybraném databázovém serveru může být instance SSISDB vytvořená vaším jménem jako jediná databáze, jako součást elastického fondu nebo ve spravované instanci. Může být přístupný ve veřejné síti nebo prostřednictvím připojení k virtuální síti. Pokyny k výběru typu databázového serveru pro hostování SSISDB naleznete v tématu [Compare SQL Database a SQL Managed instance](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance).   

      Pokud vyberete Azure SQL Database Server s pravidly brány firewall protokolu IP/koncovými body služby virtuální sítě nebo spravovanou instancí s privátním koncovým bodem hostitele SSISDB, nebo pokud budete vyžadovat přístup k místním datům bez konfigurace místního prostředí IR, budete se muset připojit k Azure-SSIS IR k virtuální síti. Další informace najdete v tématu [vytvoření Azure-SSIS IR ve virtuální síti](./create-azure-ssis-integration-runtime.md).

   1. Zaškrtněte políčko pro výběr metody ověřování pro databázový server pro hostování SSISDB **použít ověřování Azure AD se spravovaným identitou pro váš** databázový server. Zvolíte ověřování SQL nebo ověřování Azure AD se spravovanou identitou pro vaši datovou továrnu.

      Pokud políčko zaškrtnete, budete muset přidat spravovanou identitu pro vaši datovou továrnu do skupiny Azure AD s přístupovými oprávněními k vašemu databázovému serveru. Další informace najdete v tématu [vytvoření Azure-SSIS IR s ověřováním Azure AD](./create-azure-ssis-integration-runtime.md).
   
   1. Jako **uživatelské jméno správce** zadejte uživatelské jméno pro ověřování SQL vašeho databázového serveru pro hostování SSISDB. 

   1. Jako **heslo správce** zadejte heslo pro ověřování SQL vašeho databázového serveru pro hostování SSISDB. 

   1. Zaškrtněte políčko **použít dvojici duálního Azure-SSIS Integration runtime úsporného režimu s převzetím služeb při selhání SSISDB** , abyste NAKONFIGUROVALI SSIS infračervený přenos s duálním pohotovostním režimem, který spolupracuje se skupinou s převzetím služeb při selhání Azure SQL Database/spravované instance pro zajištění kontinuity podnikových
   
      Pokud zaškrtnete políčko, zadejte název pro identifikaci páru primárních a sekundárních úřadů Azure-SSIS v textovém poli **název páru se dvěma pohotovostními** pomocným polem. Při vytváření primárního a sekundárního finančního úřadu Azure-SSIS je potřeba zadat stejný název páru.

      Další informace najdete v tématu [konfigurace Azure-SSIS IR pro BCDR](./configure-bcdr-azure-ssis-integration-runtime.md).

   1. Pro **úroveň služby databáze katalogu** vyberte úroveň služby pro váš databázový server pro hostování SSISDB. Vyberte úroveň Basic, Standard nebo Premium nebo vyberte název elastického fondu.

Vyberte možnost **Testovat připojení** , pokud je to možné, a pokud je to úspěšné, vyberte **Další**.

#### <a name="creating-azure-ssis-ir-package-stores"></a>Vytváření Azure-SSIS IRch úložišť balíčků

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

### <a name="advanced-settings-page"></a>Stránka Upřesnit nastavení

Na stránce **Upřesnit nastavení** v podokně **instalace prostředí Integration runtime** proveďte následující kroky. 

   ![Rozšířená nastavení](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. Pro **maximální paralelní spouštění na uzel** vyberte maximální počet balíčků, které mají být spuštěny souběžně na jeden uzel v clusteru Integration runtime. Zobrazí se pouze podporované počty balíčků. Vyberte nízké číslo, pokud chcete použít více než jedno jádro pro spuštění jednoho velkého balíčku, který je náročné na výpočetní výkon nebo paměť. Vyberte vysoké číslo, pokud chcete spustit jeden nebo více malých balíčků v jednom jádru. 

   1. Zaškrtněte políčko **přizpůsobit Azure-SSIS Integration runtime pomocí dalších systémových konfigurací/instalací součástí** a zvolte, zda chcete do Azure-SSIS IR přidat vlastní nastavení Standard/Express. Další informace najdete v tématu [vlastní nastavení pro Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md).
   
   1. Vyberte **virtuální síť, ve které se má Azure-SSIS Integration runtime připojit, povolte ADF, aby se vytvořily určité síťové prostředky, a volitelně zaškrtněte políčko vlastní statické veřejné IP adresy,** abyste si zvolili, jestli se chcete připojit k Azure-SSIS IR k virtuální síti.

      Tuto možnost vyberte, pokud používáte server Azure SQL Database s pravidly brány firewall protokolu IP/koncovými body služby virtuální sítě nebo spravovanou instancí s privátním koncovým bodem hostitele SSISDB, nebo pokud požadujete přístup k místním datům bez konfigurace místního prostředí IR. Další informace najdete v tématu [vytvoření Azure-SSIS IR ve virtuální síti](./create-azure-ssis-integration-runtime.md). 
   
   1. Zaškrtněte políčko **nastavit Self-Hosted Integration runtime jako proxy pro Azure-SSIS Integration runtime** zaškrtávací políčko pro výběr, zda chcete pro Azure-SSIS IR nakonfigurovat jako proxy místní prostředí IR jako proxy. Další informace najdete v tématu [nastavení místního hostitele IR jako proxy](./self-hosted-integration-runtime-proxy-ssis.md).   

   1. Vyberte **Pokračovat**. 

Na stránce **Souhrn** v podokně **instalace prostředí Integration runtime** Zkontrolujte všechna nastavení zřizování, zajistěte záložku doporučené odkazy na dokumentaci a výběrem možnosti **Dokončit** spusťte vytváření prostředí Integration runtime. 

   > [!NOTE]
   > S výjimkou času vlastní instalace by tento proces měl skončit do 5 minut.
   >
   > Pokud používáte SSISDB, Služba Data Factory se připojí k vašemu databázovému serveru, aby připravil SSISDB. 
   > 
   > Když zřizujete Azure-SSIS IR, nainstaluje se také Distribuovatelný balíček a sada Azure Feature Pack pro SSIS. Tyto komponenty poskytují připojení k excelovým souborům, přístup k souborům a různým zdrojům dat Azure a navíc ke zdrojům dat, které jsou již podporovány v rámci integrovaných komponent. Další informace o předdefinovaných/předinstalovaných součástech najdete v tématu [vestavěné/předinstalované komponenty na Azure-SSIS IR](./built-in-preinstalled-components-ssis-integration-runtime.md). Další informace o dalších součástech, které můžete nainstalovat, najdete v tématu [vlastní nastavení pro Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="connections-pane"></a>Podokno připojení

V podokně **připojení** v části **Spravovat** centrum přepněte na stránku **prostředí Integration runtime** a vyberte **aktualizovat**. 

   ![Podokno připojení](./media/tutorial-create-azure-ssis-runtime-portal/connections-pane.png)

   Azure-SSIS IR můžete upravit nebo překonfigurovat tak, že vyberete jeho název. Můžete také vybrat relevantní tlačítka pro monitorování, spouštění, zastavování a odstraňování Azure-SSIS IR, automatické vygenerování kanálu ADF s aktivitou spustit balíček SSIS ke spuštění v Azure-SSIS IR a zobrazení kódu JSON nebo datové části vašeho Azure-SSIS IR.  Úpravy a odstranění Azure-SSIS IR lze provést pouze v případě, že je zastaveno.

## <a name="deploy-ssis-packages"></a>Nasazení balíčků SSIS

Pokud používáte SSISDB, můžete do něj nasadit balíčky a spouštět je v Azure-SSIS IR pomocí nástrojů SSDT nebo SSMS s podporou Azure. Tyto nástroje se připojují k vašemu databázovému serveru prostřednictvím koncového bodu serveru: 

- Pro server Azure SQL Database je formát koncového bodu serveru `<server name>.database.windows.net` .
- Pro spravovanou instanci s privátním koncovým bodem je formát koncového bodu serveru `<server name>.<dns prefix>.database.windows.net` .
- Pro spravovanou instanci s veřejným koncovým bodem je formát koncového bodu serveru `<server name>.public.<dns prefix>.database.windows.net,3342` . 

Pokud SSISDB nepoužíváte, můžete své balíčky nasadit do systému souborů, souborů Azure nebo MSDB hostovaného vaší spravovanou instancí Azure SQL a spouštět je v Azure-SSIS IR pomocí nástrojů příkazového řádku [dtutil](/sql/integration-services/dtutil-utility) a [AzureDTExec](./how-to-invoke-ssis-package-azure-enabled-dtexec.md) . 

Další informace najdete v tématu [nasazení projektů a balíčků SSIS](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages).

V obou případech můžete také spouštět nasazené balíčky na Azure-SSIS IR pomocí aktivity spustit balíček SSIS v Data Factorych kanálech. Další informace najdete v tématu [vyvolání spuštění balíčku SSIS jako aktivity první třídy Data Factory](./how-to-invoke-ssis-package-ssis-activity.md).

Viz také následující dokumentace k SSIS: 

- [Nasazení, spuštění a monitorování balíčků SSIS v Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial) 
- [Připojení k SSISDB v Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database) 
- [Plánování spouštění balíčků v Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages) 
- [Připojení k místním zdrojům dat s využitím ověřování systému Windows](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="next-steps"></a>Další kroky

Informace o přizpůsobení prostředí Azure-SSIS Integration Runtime najdete v následujícím článku: 

> [!div class="nextstepaction"]
> [Přizpůsobení Azure-SSIS IR](./how-to-configure-azure-ssis-ir-custom-setup.md)