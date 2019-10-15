---
title: Vytvoření prostředí Azure-SSIS Integration runtime v Azure Data Factory | Microsoft Docs
description: Naučte se vytvořit prostředí Azure-SSIS Integration runtime v Azure Data Factory, abyste mohli nasadit a spustit balíčky SSIS v Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/15/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: ddc91a3317d362f6b56e486556f2edf6cdb85131
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326698"
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Vytvoření prostředí Azure-SSIS Integration runtime v Azure Data Factory

Tento článek popisuje kroky pro zřízení prostředí Integration runtime (SSIS) Azure-služba SSIS (SQL Server Integration Services) (IR) v Azure Data Factory. Azure-SSIS IR podporuje:

- Spuštění balíčků nasazených do katalogu SSIS (SSISDB), které jsou hostovány serverem Azure SQL Database nebo spravované instance (model nasazení projektu).
- Spouštění balíčků nasazených do systémů souborů, sdílených složek nebo souborů Azure (model nasazení balíčku). 

Po zřízení Azure-SSIS IR můžete pomocí známých nástrojů nasadit a spustit balíčky v Azure. Mezi tyto nástroje patří SQL Server Data Tools, SQL Server Management Studio a nástroje příkazového řádku, jako je `dtinstall`, `dtutil` a `dtexec`.

V kurzu [zřizování Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md) se dozvíte, jak vytvořit Azure-SSIS IR prostřednictvím Azure Portal nebo Data Factory aplikace. Tento kurz také ukazuje, jak volitelně použít Azure SQL Database Server nebo spravovanou instanci k hostování SSISDB. Tento článek se rozbalí v tomto kurzu a popisuje, jak provádět tyto volitelné úlohy:

- Pro hostování SSISDB použijte Azure SQL Database Server s koncovými body služby virtuální sítě nebo spravovanou instancí s privátním koncovým bodem. V takovém případě musíte nakonfigurovat oprávnění a nastavení virtuální sítě pro Azure-SSIS IR pro připojení k virtuální síti.

- K připojení k serveru Azure SQL Database nebo spravované instanci použijte ověřování pomocí Azure Active Directory (Azure AD) se spravovanou identitou vaší datové továrny. Jako požadavek musíte přidat spravovanou identitu pro vaši datovou továrnu jako uživatele databáze, který může vytvořit instanci SSISDB.

- Připojte se k vašemu Azure-SSIS IR k virtuální síti nebo nakonfigurujte prostředí IR v místním prostředí jako proxy pro Azure-SSIS IR pro přístup k místním datům.

V tomto článku se dozvíte, jak zřídit Azure-SSIS IR pomocí šablony Azure Portal, Azure PowerShell a Azure Resource Manager.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Předplatné Azure**. Pokud ještě nemáte předplatné, můžete si vytvořit [bezplatný zkušební](https://azure.microsoft.com/pricing/free-trial/) účet.
- **Azure SQL Database Server nebo spravovaná instance (volitelné)** . Pokud ještě nemáte databázový server, vytvořte si ho na webu Azure Portal před tím, než začnete. Data Factory pak na tomto databázovém serveru vytvoří instanci SSISDB. 

  Doporučujeme vytvořit databázový server ve stejné oblasti Azure jako prostředí Integration Runtime. Tato konfigurace umožňuje prostředí Integration runtime zapisovat protokoly spouštění do SSISDB bez přechodu do oblastí Azure.

  Pamatujte na tyto body:

  - V závislosti na vybraném databázovém serveru může být instance SSISDB vytvořená vaším jménem jako jediná databáze, jako součást elastického fondu nebo ve spravované instanci. Může být přístupný ve veřejné síti nebo prostřednictvím připojení k virtuální síti. Pokyny k výběru typu databázového serveru pro hostování SSISDB naleznete v části [porovnání Azure SQL Database izolované databáze, elastického fondu a spravované instance](#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance) v tomto článku. 
  
    Pokud používáte server Azure SQL Database s koncovými body služby virtuální sítě nebo spravovanou instancí s privátním koncovým bodem pro hostování SSISDB, nebo pokud budete vyžadovat přístup k místním datům bez konfigurace místního prostředí IR, musíte se připojit k Azure-SSIS IR k virtuálnímu počítači. sítě. Další informace najdete v tématu [připojení Azure-SSIS IR k virtuální síti](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).
  - Ujistěte se, že má databázový server povolené nastavení **Povolit přístup ke službám Azure**. Toto nastavení se nedá použít, pokud používáte server Azure SQL Database s koncovými body služby virtuální sítě nebo spravovanou instancí s privátním koncovým bodem pro hostování SSISDB. Další informace najdete v tématu [Zabezpečení databáze Azure SQL](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Pokud chcete toto nastavení povolit pomocí PowerShellu, přečtěte si článek [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).
  - Přidejte IP adresu klientského počítače nebo rozsah IP adres, který obsahuje IP adresu klientského počítače, do seznamu IP adres klienta v nastavení brány firewall pro databázový server. Další informace najdete v tématu [Pravidla brány firewall na úrovni serveru a databáze služby Azure SQL Database](../sql-database/sql-database-firewall-configure.md).
  - K databázovému serveru se můžete připojit pomocí ověřování SQL s přihlašovacími údaji správce serveru nebo pomocí ověřování Azure AD se spravovanou identitou pro vaši datovou továrnu. V takovém případě je potřeba přidat spravovanou identitu pro vaši datovou továrnu do skupiny Azure AD s přístupovými oprávněními k databázovému serveru. Další informace najdete v tématu [Povolení ověřování Azure AD pro Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir).
  - Ověřte, že váš databázový server ještě nemá instanci SSISDB. Zřizování Azure-SSIS IR nepodporuje použití existující instance SSISDB.
- **Azure Resource Manager virtuální síť (volitelné)** . Je nutné mít Azure Resource Manager virtuální síť, pokud je splněna alespoň jedna z následujících podmínek:
    - SSISDB se hostuje na serveru Azure SQL Database s použitím koncových bodů služby virtuální sítě nebo spravované instance s privátním koncovým bodem.
    - Chcete se připojit k místním úložištím dat z balíčků SSIS běžících na vašem Azure-SSIS IR bez konfigurace prostředí IR v místním prostředí.
- **Azure PowerShell (volitelné)** . Postupujte podle pokynů v tématu [instalace a konfigurace Azure PowerShell](/powershell/azure/install-az-ps), pokud chcete ke zřízení Azure-SSIS IR spustit skript prostředí PowerShell.

### <a name="regional-support"></a>Místní podpora

Seznam oblastí Azure, ve kterých jsou k dispozici Data Factory a Azure-SSIS IR, Data Factory najdete v tématu [SSIS a dostupnost infračerveného přenosu v oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all).

### <a name="comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance"></a>Porovnání SQL Database jedné databáze, elastického fondu a spravované instance

V následující tabulce jsou porovnávány některé funkce serveru Azure SQL Database a spravované instance, protože souvisejí s Azure-SSIR IR:

| Funkce | Izolovaná databáze/elastický fond| Spravovaná instance |
|---------|--------------|------------------|
| **Plánuje** | Agent SQL Server není k dispozici.<br/><br/>Viz [Naplánování spuštění balíčku ve data Factoryovém kanálu](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity).| Agent spravované instance je k dispozici. |
| **Ověřování** | Můžete vytvořit instanci SSISDB s databází s omezením uživatele, která představuje libovolnou skupinu Azure AD se spravovanou identitou vaší datové továrny jako členem v roli **db_owner** .<br/><br/>Další informace najdete v tématu [Povolení ověřování Azure AD pro vytvoření instance SSISDB na serveru Azure SQL Database](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | Můžete vytvořit instanci SSISDB s databází s omezením uživatele, která představuje spravovanou identitu vaší datové továrny. <br/><br/>Další informace najdete [v tématu Povolení ověřování Azure AD pro vytvoření instance SSISDB ve spravované instanci Azure SQL Database](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance). |
| **Úroveň služeb** | Když vytvoříte Azure-SSIS IR se serverem Azure SQL Database, můžete vybrat úroveň služby pro SSISDB. Existuje několik úrovní služeb. | Když vytvoříte Azure-SSIS IR s vaší spravovanou instancí, nemůžete vybrat úroveň služby pro SSISDB. Všechny databáze ve spravované instanci sdílejí stejný prostředek přidělený k této instanci. |
| **Virtuální síť** | Vaše Azure-SSIS IR se můžou připojit k Azure Resource Manager virtuálním sítím jenom v případě, že používáte Azure SQL Database Server s koncovými body služby virtuální sítě, nebo pokud potřebujete přístup k místním úložištím dat bez konfigurace místního prostředí IR. | Váš Azure-SSIS IR se může připojit pouze k virtuálním sítím Azure Resource Manager. Virtuální síť se vyžaduje, když nepovolíte veřejný koncový bod pro spravovanou instanci.<br/><br/>Pokud připojíte Azure-SSIS IR ke stejné virtuální síti jako vaše spravovaná instance, ujistěte se, že je Azure-SSIS IR v jiné podsíti než vaše spravovaná instance. Pokud připojíte Azure-SSIS IR k jiné virtuální síti ze spravované instance, doporučujeme buď vytvoření partnerského vztahu virtuální sítě, nebo připojení typu síť-síť. Viz téma [připojení aplikace k spravované instanci Azure SQL Database](../sql-database/sql-database-managed-instance-connect-app.md). |
| **Distribuované transakce** | Tato funkce je podporovaná elastickými transakcemi. Transakce Microsoft DTC (Distributed Transaction Coordinator) (MSDTC) nejsou podporovány. Pokud vaše balíčky SSIS používají ke koordinaci distribuovaných transakcí službu MSDTC, zvažte migraci na elastické transakce pro Azure SQL Database. Další informace najdete v tématu [distribuované transakce v cloudových databázích](../sql-database/sql-database-elastic-transactions-overview.md). | Nepodporuje se. |
| | | |

## <a name="use-the-azure-portal-to-create-an-integration-runtime"></a>Použití Azure Portal k vytvoření prostředí Integration runtime

V této části použijete Azure Portal, konkrétně Data Factory uživatelské rozhraní (UI) nebo aplikace, k vytvoření Azure-SSIS IR.

### <a name="create-a-data-factory"></a>Vytvoření datové továrny

Pokud chcete vytvořit datovou továrnu pomocí Azure Portal, postupujte podle podrobných pokynů v tématu [Vytvoření datové továrny prostřednictvím uživatelského rozhraní](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory). Když to uděláte, vyberte **Připnout na řídicí panel** , abyste po jeho vytvoření povolili rychlý přístup. 

Po vytvoření objektu pro vytváření dat otevřete jeho stránku Přehled v Azure Portal. Výběrem dlaždice **autora & monitorování** otevřete stránku **Začínáme** na samostatné kartě. Zde můžete pokračovat v vytváření Azure-SSIS IR.   

### <a name="provision-an-azure-ssis-integration-runtime"></a>Zřízení prostředí Azure-SSIS Integration Runtime

1. Na stránce **Začínáme** vyberte dlaždici **Konfigurace prostředí SSIS Integration Runtime**.

   ![Dlaždice Konfigurace prostředí SSIS Integration Runtime](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

2. Na stránce **Obecné nastavení** **Integration runtime instalaci**proveďte následující kroky.

   ![Obecná nastavení](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    a. Jako **Název** zadejte název vašeho prostředí Integration Runtime.

    b. Jako **Popis** zadejte popis vašeho prostředí Integration Runtime.

    c. Jako **Umístění** vyberte umístění vašeho prostředí Integration Runtime. Zobrazí se pouze podporovaná umístění. Doporučujeme vybrat stejné umístění, jako má váš databázový server pro hostování databáze SSISDB.

    d. V poli **Velikost uzlu**vyberte velikost uzlu v clusteru Integration runtime. Zobrazí se pouze podporované velikosti uzlů. Pokud chcete spustit mnoho balíčků náročných na výpočetní výkon nebo paměť s náročnou na paměť, vyberte velikost velkého uzlu (horizontální navýšení kapacity).

    e. Jako **Počet uzlů** vyberte počet uzlů ve vašem clusteru Integration Runtime. Zobrazí se pouze podporované počty uzlů. Vyberte velký cluster s mnoha uzly (horizontální navýšení kapacity), pokud chcete souběžně spouštět mnoho balíčků.

    f. V části **edice/licence**vyberte edici SQL Server pro modul runtime integrace: Standard nebo Enterprise. Vyberte možnost Enterprise, pokud chcete používat pokročilé funkce v prostředí Integration runtime.

    g. Pro **Uložit peníze**vyberte možnost zvýhodněné hybridní využití Azure pro prostředí Integration Runtime: **Ano** nebo **ne**. Vyberte **Ano** , pokud chcete využít vlastní licenci SQL Server se Software Assurance, abyste využili úspory nákladů s využitím hybridního použití.

    h. Vyberte **Další**.

3. Na stránce **nastavení SQL** proveďte následující kroky.

   ![Nastavení SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   a. Zaškrtněte políčko **vytvořit katalog SSIS...** a zvolte model nasazení pro balíčky, které se mají spustit na vašem Azure-SSIS IR. Zvolíte buď model nasazení projektu, kde jsou balíčky nasazeny do SSISDB hostovaného databázovým serverem, nebo model nasazení balíčku, ve kterém jsou balíčky nasazeny do systémů souborů, sdílených souborů nebo souborů Azure. 
    
   Pokud zaškrtnete toto políčko, budete muset převést svůj vlastní databázový server na hostování instance SSISDB, kterou vytvoříme a spravujeme vaším jménem.
   
   b. Jako **Předplatné** vyberte předplatné Azure, které obsahuje váš databázový server pro hostování databáze SSISDB. 

   c. Jako **Umístění** vyberte umístění vašeho databázového serveru pro hostování databáze SSISDB. Doporučujeme vybrat stejné umístění, jako má vaše prostředí Integration Runtime. 

   d. Jako **Koncový bod databázového serveru katalogu** vyberte koncový bod vašeho databázového serveru pro hostování databáze SSISDB. 
    
   V závislosti na vybraném databázovém serveru může být instance SSISDB vytvořená vaším jménem jako jediná databáze, jako součást elastického fondu nebo ve spravované instanci. Může být přístupný ve veřejné síti nebo prostřednictvím připojení k virtuální síti. Pokyny k výběru typu databázového serveru pro hostování SSISDB naleznete v části [porovnání Azure SQL Database izolované databáze, elastického fondu a spravované instance](#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance) v tomto článku. 
    
   Pokud vyberete server Azure SQL Database s použitím koncových bodů služby virtuální sítě nebo spravované instance s privátním koncovým bodem pro hostování SSISDB, nebo pokud budete vyžadovat přístup k místním datům, aniž byste museli nakonfigurovat prostředí IR v místním prostředí, musíte se připojit k Azure-SSIS IR virtuální síť. Další informace najdete v tématu [připojení Azure-SSIS IR k virtuální síti](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

   e. Zaškrtněte políčko pro výběr metody ověřování pro databázový server pro hostování SSISDB **použít ověřování AAD se spravovaným identitou pro váš server ADF** . Zvolíte ověřování SQL nebo ověřování Azure AD se spravovanou identitou pro vaši datovou továrnu. 
    
   Pokud políčko zaškrtnete, budete muset přidat spravovanou identitu pro vaši datovou továrnu do skupiny Azure AD s přístupovými oprávněními k vašemu databázovému serveru. Další informace najdete v tématu [Povolení ověřování Azure AD pro Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). 

   f. Jako **uživatelské jméno správce**zadejte uživatelské jméno pro ověřování SQL vašeho databázového serveru pro hostování SSISDB. 

   g. Jako **heslo správce**zadejte heslo pro ověřování SQL vašeho databázového serveru pro hostování SSISDB. 

   h. Pro **úroveň služby databáze katalogu**vyberte úroveň služby pro váš databázový server pro hostování SSISDB. Vyberte úroveň Basic, Standard nebo Premium nebo vyberte název elastického fondu. 

   i. Vyberte **Test připojení**. Pokud je test úspěšný, vyberte **Další**. 

4. Na stránce **Upřesnit nastavení** proveďte následující kroky.

   ![Upřesnit nastavení](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   a. Pro **maximální paralelní spouštění na uzel**vyberte maximální počet balíčků, které mají být spuštěny souběžně na jeden uzel v clusteru Integration runtime. Zobrazí se pouze podporované počty balíčků. Vyberte nízké číslo, pokud chcete použít více než jedno jádro pro spuštění jednoho velkého balíčku, který je náročné na výpočetní výkon nebo paměť. Vyberte vysoké číslo, pokud chcete spustit jeden nebo více malých balíčků v jednom jádru.

   b. Pro **vlastní nastavení identifikátoru URI SAS kontejneru**můžete volitelně zadat identifikátor URI (URI) kontejneru úložiště objektů BLOB v Azure, ve kterém jsou uložené instalační skript a jeho přidružené soubory. Další informace najdete v tématu [vlastní nastavení pro Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

5. Vyberte virtuální síť, ve **které se má Azure-SSIS Integration runtime připojit, a povolte pomocí služby ADF vytvořit určité síťové prostředky** , abyste si zvolili, jestli se chcete připojit k prostředí Integration runtime k virtuální síti. 

   Tuto možnost vyberte, pokud používáte server Azure SQL Database s koncovými body služby virtuální sítě nebo spravovanou instancí s privátním koncovým bodem pro hostování SSISDB nebo pokud potřebujete přístup k místním datům. (To znamená, že máte místní zdroje dat nebo cíle v balíčcích SSIS bez konfigurace místního prostředí IR.) Další informace najdete v tématu [připojení Azure-SSIS IR k virtuální síti](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

   Pokud zaškrtnete políčko, proveďte následující kroky.

   ![Rozšířené nastavení virtuální sítě](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

   a. V poli **předplatné**vyberte předplatné Azure, které má vaši virtuální síť.

   b. Pro **umístění**je vybrané stejné umístění prostředí Integration runtime.

   c. Jako **typ**vyberte typ virtuální sítě: classic nebo Azure Resource Manager. Doporučujeme vybrat Azure Resource Manager virtuální síť, protože klasické virtuální sítě budou brzy zastaralé.

   d. V poli **název virtuální**sítě vyberte název vaší virtuální sítě. Tato virtuální síť by měla být stejná jako ta, která se používá pro Azure SQL Database Server s koncovými body služby virtuální sítě nebo spravovanou instancí ve virtuální síti pro hostování SSISDB. Nebo tato virtuální síť by měla být stejná jako ta, která je připojená k vaší místní síti.

   e. Jako **název podsítě**vyberte název podsítě pro virtuální síť. Mělo by se jednat o jinou podsíť než tu, která se používá pro spravovanou instanci ve virtuální síti pro hostování SSISDB.

6. Zaškrtněte políčko **nastavit Integration runtime v místním prostředí jako proxy pro Azure-SSIS Integration runtime** zaškrtávací políčko pro výběr, zda chcete pro Azure-SSIS IR nakonfigurovat prostředí IR v místním prostředí jako proxy. Další informace najdete v tématu [nastavení prostředí IR pro místní hostování jako proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). 

   Pokud zaškrtnete políčko, proveďte následující kroky.

   ![Rozšířená nastavení s místním prostředím IR](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

   a. Pro **Integration runtime**v místním prostředí vyberte jako proxy pro Azure-SSIS IR existující prostředí IR v místním prostředí.

   b. V případě **propojené služby úložiště s dalšími službami**vyberte svou stávající propojenou službu Azure Blob Storage. Nebo vytvořte novou pro přípravu.

   c. V nastavení **pracovní cesta**Určete kontejner objektů BLOB ve vybraném účtu úložiště Azure Blob. Případně ponechte prázdné pro použití výchozího nastavení pro přípravu.

7. Vyberte **ověřování VNet** > **Další**. 

8. Na stránce **Souhrn** Zkontrolujte všechna nastavení zřizování, zajistěte záložku doporučené odkazy na dokumentaci a výběrem možnosti **Dokončit** spusťte vytváření prostředí Integration runtime.

    > [!NOTE]
    > S výjimkou času vlastní instalace by tento proces měl skončit do 5 minut. Může ale trvat 20-30 minut, než se Azure-SSIS IR připojit k virtuální síti.
    >
    > Pokud používáte SSISDB, Služba Data Factory se připojí k vašemu databázovému serveru, aby připravil SSISDB. Také nakonfiguruje oprávnění a nastavení vaší virtuální sítě, pokud je zadáte, a připojí vaše Azure-SSIS IR k virtuální síti.
    > 
    > Když zřizujete Azure-SSIS IR, nainstaluje se také Distribuovatelný balíček a sada Azure Feature Pack pro SSIS. Tyto komponenty poskytují připojení k excelovým souborům, přístup k souborům a různým zdrojům dat Azure a navíc ke zdrojům dat, které jsou již podporovány v rámci integrovaných komponent. Informace o dalších součástech, které můžete nainstalovat, najdete v tématu [vlastní nastavení pro Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

7. V okně **Připojení** v případě potřeby přepněte na **Prostředí Integration Runtime**. Seznam můžete aktualizovat kliknutím na **Aktualizovat**.

   ![Stav vytváření](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

8. Pomocí odkazů ve sloupci **Akce** můžete prostředí Integration Runtime zastavit nebo spustit, upravit nebo odstranit. Pomocí posledního odkazu zobrazíte kód JSON pro prostředí Integration Runtime. Tlačítka pro úpravu a odstranění jsou aktivní, pouze když je prostředí IR zastavené.

   ![SSIS možnosti infračerveného přenosu v Azure](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Prostředí Azure SSIS Integration Runtime na portálu

1. V uživatelském rozhraní Azure Data Factory přepněte na kartu **Upravit** a vyberte **připojení**. Pak přejděte na kartu **prostředí Integration runtime** , kde se zobrazí existující prostředí Integration runtime ve vaší datové továrně.

   ![Zobrazit existující finanční úřad](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

2. Pokud chcete vytvořit nový Azure-SSIS IR, vyberte **Nový** .

   ![Prostředí Integration Runtime prostřednictvím nabídky](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

4. V okně **Instalace prostředí Integration Runtime** vyberte možnost **Migrovat metodou „lift and shift“ existující balíčky služby SSIS ke spuštění v Azure** a pak vyberte tlačítko **Další**.

   ![Zadání typu prostředí Integration Runtime](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

5. Zbývající kroky nastavení Azure-SSIS IR najdete v části [zřízení prostředí Azure SSIS Integration runtime](#provision-an-azure-ssis-integration-runtime) .

## <a name="use-azure-powershell-to-create-an-integration-runtime"></a>Použití Azure PowerShell k vytvoření prostředí Integration runtime

V této části použijete Azure PowerShell k vytvoření Azure-SSIS IR.

### <a name="create-variables"></a>Vytvoření proměnných

Zkopírujte a vložte následující skript. Zadejte hodnoty pro proměnné. 

```powershell
### Azure Data Factory information
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages.
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
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide the SAS URI of the blob container where your custom setup script and its associated files are stored
# Virtual network info: classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with virtual network service endpoints or a managed instance with a private endpoint, or if you require on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"
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

Vytvořte [skupinu prostředků Azure](../azure-resource-manager/resource-group-overview.md) pomocí příkazu [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky jako skupina.

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

Pokud SSISDB nepoužíváte, můžete parametry `CatalogServerEndpoint`, `CatalogPricingTier` a `CatalogAdminCredential` vynechat.

Pokud nepoužíváte server Azure SQL Database s koncovými body služby virtuální sítě nebo spravovanou instancí s privátním koncovým bodem pro hostování SSISDB, nebo pokud požadujete přístup k místním datům, můžete vynechat parametry `VNetId` a `Subnet` nebo předat prázdné hodnoty pro ihned. Můžete je také vynechat, pokud nakonfigurujete místní prostředí IR jako proxy pro Azure-SSIS IR pro přístup k datům v místním prostředí. V opačném případě je nemůžete vynechat a musí předávat platné hodnoty z konfigurace vaší virtuální sítě. Další informace najdete v tématu [připojení Azure-SSIS IR k virtuální síti](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Pokud používáte spravovanou instanci pro hostování SSISDB, můžete vynechat parametr `CatalogPricingTier` nebo pro něj předat prázdnou hodnotu. V opačném případě ho nemůžete vynechat a musí předávat platnou hodnotu ze seznamu podporovaných cenových úrovní pro Azure SQL Database. Další informace najdete v tématu [SQL Database omezení prostředků](../sql-database/sql-database-resource-limits.md).

Pokud pro připojení k databázovému serveru použijete ověřování Azure AD se spravovanou identitou, můžete parametr `CatalogAdminCredential` vynechat. Je ale nutné přidat spravovanou identitu pro vaši datovou továrnu do skupiny Azure AD s přístupovými oprávněními k databázovému serveru. Další informace najdete v tématu [Povolení ověřování Azure AD pro Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). V opačném případě ji nemůžete vynechat a musí předat platný objekt vytvořený z uživatelského jména a hesla správce serveru pro ověřování SQL.

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

# Add the SetupScriptContainerSasUri parameter if you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
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
### Azure Data Factory information
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages.
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
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide the SAS URI of the blob container where your custom setup script and its associated files are stored
# Virtual network info: classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with virtual network service endpoints or a managed instance with a private endpoint, or if you require on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"

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

# Add the SetupScriptContainerSasUri parameter when you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
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

2. Chcete-li nasadit šablonu Azure Resource Manager, spusťte příkaz `New-AzResourceGroupDeployment`, jak je znázorněno v následujícím příkladu. V tomto příkladu je `ADFTutorialResourceGroup` název vaší skupiny prostředků. `ADFTutorialARM.json` je soubor, který obsahuje definici JSON pro datovou továrnu a Azure-SSIS IR.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Tento příkaz vytvoří datovou továrnu a Azure-SSIS IR v ní, ale nespustí IR.

3. Pokud chcete spustit Azure-SSIS IR, spusťte příkaz `Start-AzDataFactoryV2IntegrationRuntime`:

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

Pokud používáte SSISDB, můžete do něj nasadit balíčky a spouštět je na Azure-SSIS IR pomocí nástrojů SQL Server Data Tools nebo SQL Server Management Studio Tools. Tyto nástroje se připojují k vašemu databázovému serveru prostřednictvím koncového bodu serveru: 

- Pro Azure SQL Database Server s privátním koncovým bodem je formát koncového bodu serveru `<server name>.database.windows.net`.
- U spravované instance s privátním koncovým bodem je formát koncového bodu serveru `<server name>.<dns prefix>.database.windows.net`.
- Pro spravovanou instanci s veřejným koncovým bodem je formát koncového bodu serveru `<server name>.public.<dns prefix>.database.windows.net,3342`. 

Pokud SSISDB nepoužíváte, můžete balíčky nasadit do systémů souborů, sdílených souborů nebo souborů Azure. Pak je můžete spustit na Azure-SSIS IR pomocí nástrojů příkazového řádku `dtinstall`, `dtutil` a `dtexec`. Další informace najdete v tématu [nasazení balíčků SSIS](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). 

V obou případech můžete na Azure-SSIS IR spustit i nasazené balíčky pomocí aktivity spustit balíček SSIS v Data Factorych kanálech. Další informace najdete v tématu [vyvolání spuštění balíčku SSIS jako aktivity první třídy Data Factory](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

## <a name="next-steps"></a>Další kroky

Další témata Azure-SSIS IR v této dokumentaci:

- [Prostředí Azure-SSIS Integration runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Tento článek obsahuje obecné informace o prostředí Integration runtime, včetně Azure-SSIS IR.
- [Monitorování Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). V tomto článku se dozvíte, jak načíst a pochopit informace o Azure-SSIS IR.
- [Správa Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). V tomto článku se dozvíte, jak zastavit, spustit nebo odstranit Azure-SSIS IR. Ukazuje také způsob horizontálního navýšení kapacity Azure-SSIS IR přidáním dalších uzlů.
- [Připojení Azure-SSIS IR k virtuální síti](join-azure-ssis-integration-runtime-virtual-network.md). Tento článek poskytuje informace o připojení Azure-SSIS IR k virtuální síti.
