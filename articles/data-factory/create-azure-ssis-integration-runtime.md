---
title: Vytvoření Integration Runtime Azure-SSIS v Azure Data Factory | Microsoft Docs
description: Naučte se, jak vytvořit Azure-SSIS Integration Runtime v Azure Data Factory, abyste mohli nasadit a spouštět balíčky SSIS v Azure.
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
ms.openlocfilehash: 36cb4d306cd74dcde09fa55fc582a043bc324f65
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2019
ms.locfileid: "71010047"
---
# <a name="create-azure-ssis-integration-runtime-in-azure-data-factory"></a>Vytvoření Azure-SSIS Integration Runtime v Azure Data Factory

V tomto kurzu najdete postup, jak zřídit Azure-služba SSIS (SQL Server Integration Services) (SSIS) Integration Runtime (IR) v Azure Data Factory (ADF). Azure-SSIS IR podporuje spouštění balíčků nasazených do katalogu SSIS (SSISDB) hostovaných pomocí Azure SQL Database serveru/spravované instance (model nasazení projektu) a ty, které jsou nasazené do systémů souborů/sdílené složky/soubory Azure (model nasazení balíčku). Po zřízení Azure-SSIS IR pak můžete použít známé nástroje, jako je například nástroj SQL Server Data Tools (SSDT)/SQL Server Management Studio (SSMS) `dtinstall`a nástroje příkazového řádku, / `dtutil` / `dtexec`jako je například, na Nasaďte a spusťte balíčky v Azure.

Tento [kurz: Zřizování Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md) ukazuje, jak vytvořit Azure-SSIS IR prostřednictvím aplikace Azure Portal/ADF a volitelně použít Azure SQL Database Server/spravovanou instanci k hostování SSISDB. Tento článek se rozbalí v tomto kurzu a ukáže vám, jak provést následující akce:

- Volitelně můžete použít Azure SQL Database Server s koncovými body služby virtuální sítě/spravovanou instancí s privátním koncovým bodem pro hostování SSISDB. V takovém případě je nutné nakonfigurovat oprávnění a nastavení virtuální sítě pro Azure-SSIS IR pro připojení k virtuální síti.

- Volitelně můžete pomocí ověřování pomocí Azure Active Directory (AAD) se spravovanou identitou pro ADF připojit k Azure SQL Database serveru/spravované instanci. V takovém případě budete muset přidat spravovanou identitu svého ADF jako uživatel databáze s možností vytvoření SSISDB.

- Volitelně můžete připojit Azure-SSIS IR k virtuální síti nebo nakonfigurovat prostředí IR v místním prostředí jako proxy pro Azure-SSIS IR pro přístup k datům místně.

## <a name="overview"></a>Přehled

Tento článek ukazuje různé způsoby zřizování Azure-SSIS IR:

- [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Šablona Azure Resource Manager](#azure-resource-manager-template)

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Předplatné Azure**. Pokud ještě nemáte předplatné, můžete si vytvořit [bezplatný zkušební](https://azure.microsoft.com/pricing/free-trial/) účet.
- **Azure SQL Database Server/spravovaná instance (volitelné)** . Pokud ještě nemáte databázový server, vytvořte ho v Azure Portal před tím, než začnete. ADF pak na tomto databázovém serveru vytvoří SSISDB. Doporučujeme vytvořit databázový server ve stejné oblasti Azure jako prostředí Integration Runtime. Tato konfigurace umožňuje prostředí Integration runtime zapisovat protokoly spouštění do SSISDB bez přechodu do oblastí Azure. 
    - V závislosti na vybraném databázovém serveru je možné databázi SSISDB vytvořit vaším jménem jako jednoúčelovou databázi, součást elastického fondu nebo ve spravované instanci a zpřístupnit ji ve veřejné síti nebo prostřednictvím připojení k virtuální síti. Pokyny k výběru typu databázového serveru pro hostování SSISDB naleznete v tématu [Compare Azure SQL Database Single Database/elastický fond/Managed instance](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Pokud používáte Azure SQL Database Server s koncovými body služby virtuální sítě/spravovanou instancí s privátním koncovým bodem pro hostování SSISDB nebo vyžadujete přístup k místním datům, aniž byste museli konfigurovat místní prostředí IR, musíte se připojit k Azure-SSIS IR k virtuální síti, viz [připojení Azure-SSIS IR k virtuální síti](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).
    - Ujistěte se, že má databázový server povolené nastavení **Povolit přístup ke službám Azure**. Tato možnost se nevztahuje na použití serveru Azure SQL Database s koncovými body služby virtuální sítě/spravovanou instancí s privátním koncovým bodem pro hostování SSISDB. Další informace najdete v tématu [Zabezpečení databáze Azure SQL](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Pokud chcete toto nastavení povolit pomocí PowerShellu, přečtěte si článek [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).
    - Přidejte IP adresu klientského počítače nebo rozsah IP adres, který obsahuje IP adresu klientského počítače, do seznamu IP adres klienta v nastavení brány firewall pro databázový server. Další informace najdete v tématu [Pravidla brány firewall na úrovni serveru a databáze služby Azure SQL Database](../sql-database/sql-database-firewall-configure.md).
    - K databázovému serveru se můžete připojit pomocí ověřování SQL s přihlašovacími údaji správce serveru nebo pomocí ověřování Azure Active Directory (AAD) se spravovanou identitou pro ADF.  V takovém případě je potřeba přidat spravovanou identitu pro váš ADF do skupiny AAD s přístupovými oprávněními k databázovému serveru. Další informace najdete v tématu [Povolení ověřování AAD pro Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir).
    - Ověřte, že váš databázový server ještě nemá SSISDB. Zřizování Azure-SSIS IR nepodporuje používání existující SSISDB.
- **Azure Resource Manager virtuální síť (volitelné)** . Je nutné mít Azure Resource Manager virtuální síť, pokud je splněna alespoň jedna z následujících podmínek:
    - Hostuje se SSISDB na Azure SQL Database Server s koncovými body služby virtuální sítě/spravovanou instancí s privátním koncovým bodem.
    - Chcete se připojit k místním úložištím dat z balíčků SSIS běžících na vašem Azure-SSIS IR bez konfigurace místního prostředí IR.
- **Azure PowerShell (volitelné)** . Postupujte podle pokynů, [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/install-az-ps), pokud chcete ke zřízení Azure-SSIS IR spustit skript prostředí PowerShell.

### <a name="region-support"></a>Podpora oblastí

Seznam oblastí Azure, ve kterých jsou aktuálně k dispozici ADF a Azure-SSIS IR, najdete v článku o [dostupnosti ADF + SSIS IR v oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all).

### <a name="compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance"></a>Compare SQL Database izolovanou databázi nebo elastický fond a SQL Database spravovanou instanci.

V následující tabulce jsou porovnávány některé funkce Azure SQL Database serveru a spravované instance, protože souvisejí s Azure-SSIR IR:

| Funkce | izolovaná databáze/elastický fond| MI |
|---------|--------------|------------------|
| **Plánuje** | Agent SQL Server není k dispozici.<br/><br/>Viz [Naplánování spuštění balíčku v kanálu ADF](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity).| Agent spravované instance je k dispozici. |
| **Autentizace** | SSISDB můžete vytvořit pomocí uživatele databáze s omezením, který představuje libovolnou skupinu AAD se spravovanou identitou vašeho ADF jako členem role **db_owner** .<br/><br/>Další informace najdete [v tématu Povolení ověřování Azure AD při vytváření SSISDB na serveru Azure SQL Database](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | SSISDB můžete vytvořit s uživatelem databáze s omezením, který představuje spravovanou identitu svého ADF. <br/><br/>Další informace najdete [v tématu Povolení ověřování Azure AD při vytváření SSISDB v Azure SQL Database Managed instance](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance). |
| **Úroveň služeb** | Když vytvoříte Azure-SSIS IR s vaším serverem Azure SQL Database, můžete vybrat úroveň služby pro SSISDB. Existuje několik úrovní služeb. | Když vytvoříte Azure-SSIS IR s vaší spravovanou instancí, nemůžete vybrat úroveň služby pro SSISDB. Všechny databáze ve spravované instanci sdílejí stejný prostředek přidělený k této instanci. |
| **Virtuální síť** | Podporuje jenom Azure Resource Manager virtuální sítě pro připojení Azure-SSIS IR, pokud používáte Azure SQL Database Server s koncovými body služby virtuální sítě nebo vyžadují přístup k místním úložištím dat bez konfigurace místního prostředí IR. | Podporuje jenom Azure Resource Manager virtuální sítě pro připojení k Azure-SSIS IR. Virtuální síť je povinná, když nepovolíte veřejný koncový bod pro spravovanou instanci.<br/><br/>Pokud připojíte Azure-SSIS IR ke stejné virtuální síti jako vaše spravovaná instance, ujistěte se, že je váš Azure-SSIS IR v jiné podsíti než vaše spravovaná instance. Pokud se k Azure-SSIS IR připojíte do jiné virtuální sítě, než je vaše spravovaná instance, doporučujeme připojení virtuální sítě k virtuální síti pomocí partnerského vztahu virtuálních sítí nebo virtuální sítě. Viz téma [připojení aplikace k Azure SQL Database Managed instance](../sql-database/sql-database-managed-instance-connect-app.md). |
| **Distribuované transakce** | Podporováno prostřednictvím elastických transakcí. Transakce Microsoft DTC (Distributed Transaction Coordinator) (MSDTC) nejsou podporovány. Pokud vaše balíčky SSIS používají ke koordinaci distribuovaných transakcí službu MSDTC, zvažte migraci na elastické transakce pro Azure SQL Database. Další informace najdete v tématu [distribuované transakce v cloudových databázích](../sql-database/sql-database-elastic-transactions-overview.md). | Nepodporuje se. |
| | | |

## <a name="azure-portal"></a>portál Azure

V této části použijete k vytvoření prostředí Azure-SSIS IR Azure Portal specificky uživatelské rozhraní ADF (/App).

### <a name="create-a-data-factory"></a>Vytvoření datové továrny

Pokud chcete vytvořit ADF prostřednictvím Azure Portal, postupujte podle podrobných pokynů v tématu [Vytvoření ADF pomocí uživatelského rozhraní](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory) a vyberte **Připnout na řídicí panel** , abyste po jeho vytvoření povolili rychlý přístup. 

Po vytvoření ADF otevřete jeho stránku Přehled na Azure Portal a kliknutím na dlaždici pro **monitorování autora &** na samostatné kartě spusťte svou **úvodní** stránku, kde můžete pokračovat v vytváření Azure-SSIS IR.   

### <a name="provision-an-azure-ssis-integration-runtime"></a>Zřízení prostředí Azure SSIS Integration Runtime

1. Na stránce **Začínáme** klikněte na dlaždici **Konfigurovat SSIS Integration runtime** .

   ![Dlaždice Konfigurace prostředí SSIS Integration Runtime](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

2. Na stránce **Obecná nastavení** v okně **Instalace prostředí Integration Runtime** proveďte následující kroky:

   ![Obecná nastavení](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

    a. Jako **Název** zadejte název vašeho prostředí Integration Runtime.

    b. Jako **Popis** zadejte popis vašeho prostředí Integration Runtime.

    c. Jako **Umístění** vyberte umístění vašeho prostředí Integration Runtime. Zobrazí se pouze podporovaná umístění. Doporučujeme vybrat stejné umístění, jako má váš databázový server pro hostování databáze SSISDB.

    d. Jako **Velikost uzlu** vyberte velikost uzlu ve vašem clusteru Integration Runtime. Zobrazí se pouze podporované velikosti uzlů. Pokud chcete spouštět mnoho balíčků náročných na výpočetní výkon nebo paměť, vyberte velkou velikost uzlu (vertikálně navyšte kapacitu).

    e. Jako **Počet uzlů** vyberte počet uzlů ve vašem clusteru Integration Runtime. Zobrazí se pouze podporované počty uzlů. Pokud chcete paralelně spouštět mnoho balíčků, vyberte velký cluster s mnoha uzly (horizontálně navyšte kapacitu).

    f. V případě **edice nebo licence**vyberte SQL Server Edition/licence pro prostředí Integration Runtime: Standard nebo Enterprise. Vyberte Enterprise, pokud ve svém prostředí Integration Runtime chcete využít pokročilé nebo prémiové funkce.

    g. Pro možnost **Uložit peníze**vyberte možnost zvýhodněné hybridní využití Azure (AHB) pro prostředí Integration Runtime: Ano nebo ne. Vyberte Ano, pokud chcete použít vlastní licenci SQL Serveru se Software Assurance a využít tak úspory nákladů spojené s hybridním využitím.

    h. Klikněte na **Další**.

3. Na stránce **Nastavení SQL** proveďte následující kroky:

   ![Nastavení SQL](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

    a. V části **vytvořit katalog SSIS...** vyberte model nasazení, ve kterém se mají balíčky spouštět v Azure-SSIS IR: Model nasazení projektu, kde jsou balíčky nasazeny do SSISDB hostovaného databázovým serverem nebo modelem nasazení balíčku, kde jsou balíčky nasazeny do vašich systémů souborů/sdílených složek nebo souborů Azure. Pokud ho zkontrolujete, budete muset přenést svůj vlastní databázový server do hostitele SSISDB, který vytvoříme a spravujeme vaším jménem.
   
    b. Jako **Předplatné** vyberte předplatné Azure, které obsahuje váš databázový server pro hostování databáze SSISDB. 

    c. Jako **Umístění** vyberte umístění vašeho databázového serveru pro hostování databáze SSISDB. Doporučujeme vybrat stejné umístění, jako má vaše prostředí Integration Runtime. 

    d. Jako **Koncový bod databázového serveru katalogu** vyberte koncový bod vašeho databázového serveru pro hostování databáze SSISDB. V závislosti na vybraném databázovém serveru je možné databázi SSISDB vytvořit vaším jménem jako jednoúčelovou databázi, součást elastického fondu nebo ve spravované instanci a zpřístupnit ji ve veřejné síti nebo prostřednictvím připojení k virtuální síti. Pokyny k výběru typu databázového serveru pro hostování SSISDB naleznete v tématu [Compare Azure SQL Database Single Database/elastický fond/Managed instance](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). Pokud vyberete Azure SQL Database Server s použitím koncových bodů služby virtuální sítě/spravované instance s privátním koncovým bodem pro hostování SSISDB nebo vyžadujete přístup k místním datům, aniž byste museli nakonfigurovat prostředí IR v místním prostředí, musíte se připojit k Azure-SSIS IR k virtuální síti. najdete v tématu [připojení Azure-SSIS IR k virtuální síti](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

    e. V části **použít ověřování AAD...** vyberte metodu ověřování pro databázový server pro hostování SSISDB: Ověřování SQL nebo ověřování AAD se spravovanou identitou pro váš ADF. Pokud ho zkontrolujete, budete muset přidat spravovanou identitu pro svůj ADF do skupiny AAD s přístupovými oprávněními k vašemu databázovému serveru. Další informace najdete v tématu [Povolení ověřování AAD pro Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). 

    f. Jako **Uživatelské jméno správce** zadejte uživatelské jméno ověřování SQL pro váš databázový server pro hostování databáze SSISDB. 

    g. Jako **Heslo správce** zadejte heslo ověřování SQL pro váš databázový server pro hostování databáze SSISDB. 

    h. V poli **úroveň služby databáze katalogu**vyberte úroveň služby pro váš databázový server pro hostování SSISDB: Úroveň Basic/Standard/Premium nebo název elastického fondu. 

    i. Klikněte na **Test připojení**, a pokud proběhne úspěšně, klikněte na **Další**. 

4. Na stránce **Upřesnit nastavení** proveďte následující kroky:

    ![Upřesnit nastavení](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

    a. Jako **Maximální počet paralelních zpracování na uzel** vyberte maximální počet balíčků, které se můžou paralelně spustit v jednom uzlu ve vašem clusteru Integration Runtime. Zobrazí se pouze podporované počty balíčků. Vyberte nízké číslo, pokud chcete použít více než jedno jádro ke spuštění jednoho velkého nebo velmi vysokého balíčku, který je náročný na výpočetní výkon/náročné na paměť. Pokud chcete spustit jeden nebo více malých a méně náročných balíčků v jednom jádru, vyberte vysoké číslo.

    b. Jako **Identifikátor URI SAS kontejneru vlastního nastavení** volitelně zadejte identifikátor URI sdíleného přístupového podpisu (SAS) vašeho kontejneru Azure Storage Blob, ve kterém je uložený váš instalační skript a související soubory. Další informace najdete v tématu [Vlastní nastavení pro prostředí Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

5. V zaškrtávacím políčku **Vybrat virtuální síť...** vyberte, jestli se chcete připojit k prostředí Integration runtime k virtuální síti. Pokud používáte Azure SQL Database Server s koncovými body služby virtuální sítě/spravovanou instancí s privátním koncovým bodem pro hostování SSISDB nebo vyžadují přístup k místním datům, tj. máte místní zdroje dat/cíle v balíčcích SSIS, bez Konfigurace prostředí IR v místním prostředí – další informace najdete v tématu [připojení Azure-SSIS IR k virtuální síti](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Pokud je zaškrtnete, proveďte následující kroky:

   ![Rozšířená nastavení s využitím virtuální sítě](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

    a. V poli **předplatné**vyberte předplatné Azure, které má vaši virtuální síť.

    b. Pro **umístění**je vybrané stejné umístění prostředí Integration runtime.

    c. Jako **typ**vyberte typ virtuální sítě: Classic nebo Azure Resource Manager. Doporučujeme, abyste vybrali Azure Resource Manager virtuální síť, protože už brzy přestane být klasická virtuální síť.

    d. V poli **název virtuální**sítě vyberte název vaší virtuální sítě. Tato virtuální síť by měla být stejná jako ta, která se používá pro Azure SQL Database Server s koncovými body služby virtuální sítě/spravovanou instancí ve virtuální síti pro hostování SSISDB nebo připojení k vaší místní síti.

    e. Jako **název podsítě**vyberte název podsítě pro virtuální síť. Měla by to být jiná podsíť než ta, která se používá pro spravovanou instanci ve virtuální síti pro hostování SSISDB.

6. V nastavení v místním prostředí **...** vyberte, jestli chcete nakonfigurovat prostředí IR v místním prostředí jako proxy pro váš Azure-SSIS IR. Další informace najdete v tématu [nastavení prostředí IR pro místní hostování jako proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). Pokud je zaškrtnete, proveďte následující kroky:

   ![Rozšířená nastavení pomocí prostředí IR pro místní hostování](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

    a. Pro **Integration runtime**v místním prostředí vyberte pro Azure-SSIS IR existující místní prostředí IR jako proxy.

    b. Pro **propojenou službu úložiště s**ochranou úložiště vyberte existující propojenou službu Azure Blob Storage nebo vytvořte novou pro přípravu.

    c. Do pole **pracovní cesta**zadejte kontejner objektů BLOB ve vybrané službě Azure Blob Storage nebo ponechte prázdné, aby pro přípravu používala výchozí hodnotu.

7. Klikněte na **ověření virtuální** sítě a nebo **Další**. 

8. Na stránce **Souhrn** Zkontrolujte všechna nastavení zřizování, zajistěte záložku doporučené odkazy na dokumentaci a kliknutím na tlačítko **Dokončit** spusťte vytváření prostředí Integration runtime.

    > [!NOTE]
    > S výjimkou času vlastní instalace by tento proces měl být dokončen během 5 minut, ale může trvat přibližně 20-30 minut, než se Azure-SSIS IR připojuje k virtuální síti.
    >
    > Pokud používáte SSISDB, služba ADF se připojí k vašemu databázovému serveru a připraví SSISDB. Také nakonfiguruje oprávnění a nastavení vaší virtuální sítě, pokud je zadáte, a připojí vaše Azure-SSIS IR k virtuální síti.
    > 
    > Když zřizujete Azure-SSIS IR, nainstaluje se také Distribuovatelný balíček a sada Azure Feature Pack pro SSIS. Tyto komponenty poskytují kromě zdrojů dat, které už jsou podporované integrovanými součástmi, možnosti připojení k souborům Excelu a Accessu a různým zdrojům dat Azure. Můžete také nainstalovat další součásti, viz [vlastní nastavení pro Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

7. V okně **Připojení** v případě potřeby přepněte na **Prostředí Integration Runtime**. Kliknutím na **Aktualizovat** aktualizujte stav.

   ![Stav vytváření](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

8. Pomocí odkazů ve sloupci **Akce** můžete prostředí Integration runtime zastavit nebo spustit, upravit nebo odstranit. Pomocí posledního odkazu zobrazíte kód JSON pro prostředí Integration Runtime. Tlačítka pro úpravu a odstranění jsou aktivní, pouze když je prostředí IR zastavené.

   ![Azure SSIS IR – akce](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Prostředí Azure SSIS Integration Runtime na portálu

1. V uživatelském prostředí služby Azure Data Factory přepněte na kartu **Upravit**, klikněte na **Připojení** a pak přepněte zpět na kartu **Prostředí Integration Runtime**, kde se zobrazí existující prostředí Integration Runtime ve vaší datové továrně.

   ![Zobrazit existující finanční úřad](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

2. Kliknutím na **Nový** vytvořte nové prostředí Azure-SSIS IR.

   ![Prostředí Integration Runtime prostřednictvím nabídky](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

3. Pokud chcete vytvořit prostředí Azure-SSIS Integration Runtime, klikněte na **Nový**, jak je znázorněno na obrázku.

4. V okně Instalace prostředí Integration Runtime vyberte **Migrovat metodou „lift and shift“ existující balíčky služby SSIS ke spuštění v Azure** a pak klikněte na **Další**.

   ![Zadání typu prostředí Integration Runtime](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

5. Zbývající kroky k nastavení prostředí Azure-SSIS IR najdete v části [Zřízení prostředí Azure SSIS Integration Runtime](#provision-an-azure-ssis-integration-runtime).

## <a name="azure-powershell"></a>Azure PowerShell

V této části použijete Azure PowerShell k vytvoření prostředí Azure-SSIS IR.

### <a name="create-variables"></a>Vytvoření proměnných

Zkopírujte a vložte následující skript – zadejte hodnoty pro proměnné. 

```powershell
### Azure Data Factory information
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database server with virtual network service endpoints/Managed Instance with a private endpoint/on-premises data without configuring Self-Hosted IR, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints or a different subnet than the one used for your Managed Instance with a private endpoint

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.DNS prefix.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"
```

### <a name="sign-in-and-select-subscription"></a>Přihlášení a výběr předplatného

Přidejte následující kód pro přihlášení a vyberte své předplatné Azure:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database-server"></a>Ověření připojení k databázovému serveru

Přidejte následující skript, který ověří Azure SQL Database Server/spravovanou instanci.

```powershell
# Validate only if you use SSISDB and do not use VNet or AAD authentication
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

### <a name="configure-virtual-network"></a>Konfigurace virtuální sítě

Přidejte následující skript, který automaticky nakonfiguruje oprávnění a nastavení virtuální sítě, ke které se má vaše prostředí Azure SSIS Integration Runtime připojit.

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

Pokud vaše skupina prostředků už existuje, nekopírujte do skriptu tento kód. 

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

Pokud nepoužíváte SSISDB, můžete parametry CatalogServerEndpoint, CatalogPricingTier a CatalogAdminCredential vynechat.

Pokud nepoužíváte Azure SQL Database Server s koncovými body služby virtuální sítě/spravovanou instancí s privátním koncovým bodem pro hostování SSISDB nebo vyžadují přístup k místním datům, můžete parametry VNetId a Subnet vynechat nebo pro ně předávat prázdné hodnoty. Můžete je také vynechat, pokud nakonfigurujete místní prostředí IR jako proxy pro Azure-SSIS IR pro přístup k datům v místním prostředí. V opačném případě je nemůžete vynechat a musí předávat platné hodnoty z konfigurace vaší virtuální sítě. Další informace najdete v tématu [připojení Azure-SSIS IR k virtuální síti](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Pokud používáte spravovanou instanci pro hostování SSISDB, můžete vynechat parametr CatalogPricingTier nebo pro něj předat prázdnou hodnotu. Jinak ho nemůžete vynechat a musí předávat platnou hodnotu ze seznamu podporovaných cenových úrovní pro Azure SQL Database, viz [omezení prostředků SQL Database](../sql-database/sql-database-resource-limits.md).

Pokud pro připojení k databázovému serveru použijete ověřování pomocí Azure Active Directory (AAD) se spravovanou identitou, můžete parametr CatalogAdminCredential vynechat, ale musíte přidat spravovanou identitu svého ADF do skupiny AAD s přístupem. oprávnění k databázovému serveru najdete v tématu [Povolení ověřování AAD pro Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). V opačném případě ji nemůžete vynechat a musí předat platný objekt vytvořený z uživatelského jména a hesla správce serveru pro ověřování SQL.

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
       
# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add CatalogAdminCredential parameter if you do not use AAD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                   -DataFactoryName $DataFactoryName `
                                                   -Name $AzureSSISName `
                                                   -CatalogAdminCredential $serverCreds
    }
}

# Add SetupScriptContainerSasUri parameter if you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
```

### <a name="start-integration-runtime"></a>Spuštění prostředí Integration Runtime

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
> S výjimkou času vlastní instalace by tento proces měl být dokončen během 5 minut, ale může trvat přibližně 20-30 minut, než se Azure-SSIS IR připojuje k virtuální síti.
>
> Pokud používáte SSISDB, služba ADF se připojí k vašemu databázovému serveru a připraví SSISDB. Také nakonfiguruje oprávnění a nastavení vaší virtuální sítě, pokud je zadáte, a připojí vaše Azure-SSIS IR k virtuální síti.
> 
> Když zřizujete Azure-SSIS IR, nainstaluje se také Distribuovatelný balíček a sada Azure Feature Pack pro SSIS. Tyto komponenty poskytují kromě zdrojů dat, které už jsou podporované integrovanými součástmi, možnosti připojení k souborům Excelu a Accessu a různým zdrojům dat Azure. Můžete také nainstalovat další součásti, viz [vlastní nastavení pro Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="full-script"></a>Celý skript

Tady je úplný skript, který vytvoří prostředí Azure-SSIS Integration runtime.

```powershell
### Azure Data Factory information
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - Must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to (2 x number of cores) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database server with virtual network service endpoints/Managed Instance with a private endpoint/on-premises data without configuring Self-Hosted IR, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database server with virtual network service endpoints or a different subnet than the one used for your Managed Instance with a private endpoint

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or Managed Instance name.DNS prefix.database.windows.net or Managed Instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, please ensure that there is no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
# For the basic pricing tier, specify "Basic", not "B" - For standard/premium/elastic pool tiers, specify "S0", "S1", "S2", "S3", etc., see https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for Managed Instance]"

### Sign in and select subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to database server
# Validate only if you use SSISDB and do not use VNet or AAD authentication
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

### Configure virtual network
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

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add CatalogAdminCredential parameter if you do not use AAD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                   -DataFactoryName $DataFactoryName `
                                                   -Name $AzureSSISName `
                                                   -CatalogAdminCredential $serverCreds
    }
}

# Add SetupScriptContainerSasUri parameter when you use custom setup
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}

### Start integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru

V této části použijete šablonu Azure Resource Manager k vytvoření prostředí Azure-SSIS Integration runtime. Tady je ukázkový Návod:

1. Vytvořte soubor JSON s následující šablonou Azure Resource Manager. Nahraďte hodnoty v lomených závorkách (umístěte držitele) vlastními hodnotami.

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

2. Chcete-li nasadit šablonu Azure Resource Manager, spusťte příkaz New-AzResourceGroupDeployment, jak je znázorněno v následujícím příkladu, kde ADFTutorialResourceGroup je název vaší skupiny prostředků a ADFTutorialARM. JSON je soubor, který obsahuje definici JSON pro Data Factory a Azure-SSIS IR.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Tento příkaz vytvoří v něm datovou továrnu a Azure-SSIS IR, ale nespustí IR.

3. Pokud chcete spustit Azure-SSIS IR, spusťte příkaz Start-AzDataFactoryV2IntegrationRuntime:

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
                                                 -DataFactoryName "<Data Factory Name>" `
                                                 -Name "<Azure SSIS IR Name>" `
                                                 -Force
    ```

> [!NOTE]
> S výjimkou času vlastní instalace by tento proces měl být dokončen během 5 minut, ale může trvat přibližně 20-30 minut, než se Azure-SSIS IR připojuje k virtuální síti.
>
> Pokud používáte SSISDB, služba ADF se připojí k vašemu databázovému serveru a připraví SSISDB. Také nakonfiguruje oprávnění a nastavení vaší virtuální sítě, pokud je zadáte, a připojí vaše Azure-SSIS IR k virtuální síti.
> 
> Když zřizujete Azure-SSIS IR, nainstaluje se také Distribuovatelný balíček a sada Azure Feature Pack pro SSIS. Tyto komponenty poskytují kromě zdrojů dat, které už jsou podporované integrovanými součástmi, možnosti připojení k souborům Excelu a Accessu a různým zdrojům dat Azure. Můžete také nainstalovat další součásti, viz [vlastní nastavení pro Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="deploy-ssis-packages"></a>Nasazení balíčků SSIS

Pokud používáte SSISDB, můžete do něj nasadit balíčky a spouštět je v Azure-SSIS IR pomocí nástrojů SSDT/SSMS, které se připojují k vašemu databázovému serveru prostřednictvím koncového bodu serveru.  U Azure SQL Database serveru/spravované instance v nástroji s veřejným koncovým bodem nebo spravovanou instancí s veřejným koncovým bodem je `<server name>.database.windows.net` / / `<server name>.<dns prefix>.database.windows.net` `<server name>.public.<dns prefix>.database.windows.net,3342`formát koncového bodu serveru v uvedeném pořadí. Pokud SSISDB nepoužíváte, můžete balíčky nasadit do systémů souborů/sdílených složek nebo souborů Azure a spouštět je v `dtinstall` Azure-SSIS IR pomocí / `dtutil` / `dtexec` nástrojů příkazového řádku. Další informace najdete v tématu [nasazení balíčků SSIS](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). V obou případech můžete také spouštět nasazené balíčky na Azure-SSIS IR pomocí aktivity spustit balíček SSIS v kanálech ADF, viz téma [vyvolání spuštění balíčku SSIS jako aktivity první třídy ADF](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

## <a name="next-steps"></a>Další kroky

Viz také další témata Azure-SSIS IR v této dokumentaci:

- [Integration runtime Azure-SSIS](concepts-integration-runtime.md#azure-ssis-integration-runtime). Tento článek obsahuje obecné informace o prostředí Integration runtime, včetně Azure-SSIS IR.
- [Azure-SSIS IR monitorování](monitor-integration-runtime.md#azure-ssis-integration-runtime). V tomto článku se dozvíte, jak načíst a pochopit informace o Azure-SSIS IR.
- [Spravovat Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). V tomto článku se dozvíte, jak zastavit, spustit nebo odstranit Azure-SSIS IR – také se dozvíte, jak horizontální navýšení kapacity Azure-SSIS IR přidáním dalších uzlů.
- [Připojte Azure-SSIS IR k virtuální síti](join-azure-ssis-integration-runtime-virtual-network.md). Tento článek poskytuje informace o připojení Azure-SSIS IR k virtuální síti.