---
title: Transformace dat pomocí Hivu ve službě Azure Virtual Network | Dokumentace Microsoftu
description: Tento kurz obsahuje podrobné pokyny pro transformaci dat pomocí aktivity Hivu v Azure Data Factory.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/04/2018
ms.author: douglasl
ms.openlocfilehash: e9ec338e04c456a9a91d51a7a4741e4760f1b3c9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="transform-data-in-azure-virtual-network-using-hive-activity-in-azure-data-factory"></a>Transformace dat ve službě Azure Virtual Network pomocí aktivity Hivu v Azure Data Factory
V tomto kurzu pomocí webu Azure Portal vytvoříte kanál Data Factory, který transformuje data pomocí aktivity Hivu v clusteru HDInsight ve službě Azure Virtual Network. V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Vytvoření datové továrny 
> * Vytvoření místního prostředí Integration Runtime
> * Vytvoření propojených služeb Azure Storage a Azure HDInsight
> * Vytvoření kanálu s aktivitou Hivu
> * Aktivace spuštění kanálu
> * Monitorování spuštění kanálu 
> * Ověření výstupu

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), prostudujte si [dokumentaci služby Data Factory verze 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky
- **Účet služby Azure Storage**. Vytvoříte skript Hivu a uložíte ho do úložiště Azure. Výstup ze skriptu Hivu je uložený v tomto účtu úložiště. V této ukázce clusteru HDInsight používá tento účet služby Azure Storage jako primární úložiště. 
- **Virtuální síť Azure**. Pokud nemáte virtuální síť Azure, vytvořte ji pomocí [těchto pokynů](../virtual-network/quick-create-portal.md). V této ukázce je HDInsight ve službě Azure Virtual Network. Tady je ukázka konfigurace služby Azure Virtual Network. 

    ![Vytvoření virtuální sítě](media/tutorial-transform-data-using-hive-in-vnet-portal/create-virtual-network.png)
- **Cluster HDInsight**. Vytvořte cluster HDInsight a připojte ho k virtuální síti, kterou jste vytvořili v předchozím kroku, a na základě informací v článku věnovaném [rozšíření Azure HDInsightu s využitím služby Azure Virtual Network](../hdinsight/hdinsight-extend-hadoop-virtual-network.md). Tady je ukázka konfigurace HDInsightu ve virtuální síti. 

    ![HDInsight ve virtuální síti](media/tutorial-transform-data-using-hive-in-vnet-portal/hdinsight-virtual-network-settings.png)
- **Azure PowerShell**. Postupujte podle pokynů v tématu [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/install-azurerm-ps).
- **Virtuální počítač**. Vytvořte virtuální počítač Azure a připojte ho do stejné virtuální sítě, která obsahuje váš cluster HDInsight. Podrobnosti najdete v tématu věnovaném [postupu při vytváření virtuálních počítačů](../virtual-network/quick-create-portal.md#create-virtual-machines). 

### <a name="upload-hive-script-to-your-blob-storage-account"></a>Uložení skriptu Hivu do vašeho účtu služby Blob Storage

1. Vytvořte soubor SQL Hivu s názvem **hivescript.hql** a s následujícím obsahem:

   ```sql
   DROP TABLE IF EXISTS HiveSampleOut; 
   CREATE EXTERNAL TABLE HiveSampleOut (clientid string, market string, devicemodel string, state string)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' 
   STORED AS TEXTFILE LOCATION '${hiveconf:Output}';

   INSERT OVERWRITE TABLE HiveSampleOut
   Select 
       clientid,
       market,
       devicemodel,
       state
   FROM hivesampletable
   ```
2. Ve službě Azure Blob Storage, vytvořte kontejner **adftutorial**, pokud ještě neexistuje.
3. Vytvořte složku s názvem **hivescripts**.
4. Uložte soubor **hivescript.hql** do podsložky **hivescripts**.

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. Spusťte webový prohlížeč **Microsoft Edge** nebo **Google Chrome**. Uživatelské rozhraní služby Data Factory podporují v současnosti jenom webové prohlížeče Microsoft Edge a Google Chrome.
1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com/).    
2. V nabídce vlevo klikněte na **Nový**, klikněte na **Data + analýzy** a pak na **Data Factory**. 
   
   ![Nový -> Objekt pro vytváření dat](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-data-factory-menu.png)
3. Na stránce **Nová datová továrna** jako **název** zadejte **ADFTutorialHiveFactory**. 
      
     ![Stránka Nová datová továrna](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-azure-data-factory.png)
 
   Název objektu pro vytváření dat Azure musí být **globálně jedinečný**. Pokud se zobrazí následující chyba, změňte název datové továrny (například na vaše_jméno_MyAzureSsisDataFactory) a zkuste to znovu. Pravidla pojmenování artefaktů služby Data Factory najdete v článku [Data Factory – pravidla pojmenování](naming-rules.md).
  
       `Data factory name “MyAzureSsisDataFactory” is not available`
3. Vyberte své **předplatné** Azure, ve kterém chcete vytvořit datovou továrnu. 
4. Pro **Skupinu prostředků** proveďte jeden z následujících kroků:
     
      - Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků. 
      - Vyberte **Vytvořit novou** a zadejte název skupiny prostředků.   
         
      Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-overview.md).  
4. Jako **verzi** vyberte **V2 (Preview)**.
5. Vyberte **umístění** pro objekt pro vytváření dat. V seznamu se zobrazí pouze podporovaná umístění pro vytváření datových továren.
6. Zaškrtněte **Připnout na řídicí panel**.     
7. Klikněte na možnost **Vytvořit**.
8. Na řídicím panelu vidíte následující dlaždice se statusem: **Nasazování datové továrny**. 

    ![nasazování dlaždice datové továrny](media/tutorial-transform-data-using-hive-in-vnet-portal/deploying-data-factory.png)
9. Po vytvoření se zobrazí stránka **Datová továrna**, jak je znázorněno na obrázku.
   
   ![Domovská stránka objektu pro vytváření dat](./media/tutorial-transform-data-using-hive-in-vnet-portal/data-factory-home-page.png)
10. Kliknutím na dlaždici **Vytvořit a monitorovat** otevřete na samostatné kartě uživatelské rozhraní služby Data Factory.
11. Na stránce **Začínáme** přepněte na levém panelu na kartu **Upravit**, jak je znázorněno na následujícím obrázku: 

   ![Karta Upravit](./media/tutorial-transform-data-using-hive-in-vnet-portal/get-started-page.png)

## <a name="create-a-self-hosted-integration-runtime"></a>Vytvoření místního prostředí Integration Runtime
Vzhledem k tomu, že cluster Hadoop je ve virtuální síti, musíte do stejné virtuální sítě nainstalovat místní prostředí Integration Runtime. V této části vytvoříte nový virtuální počítač, připojíte ho do stejné virtuální sítě a nainstalujete na něm místní prostředí IR. Místní prostředí IR umožňuje službě Data Factory odesílat požadavky na zpracování do výpočetní služby, jako je HDInsight, ve virtuální síti. Umožňuje také přesun dat mezi úložištěm dat ve virtuální síti a Azure. Místní prostředí IR použijete i v případě, že je úložiště dat nebo výpočetní služba v místním prostředí. 

1. V uživatelském rozhraní služby Azure Data Factory klikněte v dolní části okna na **Připojení**, přepněte na kartu **Prostředí Integration Runtime** a klikněte na tlačítko **+ Nový** na panelu nástrojů. 

   ![Nabídka Nové prostředí Integration Runtime](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-integration-runtime-menu.png)
2. V okně **Instalace prostředí Integration Runtime** vyberte možnost **Provést přesun dat a odeslání aktivit do externích výpočetních prostředí** a klikněte na **Další**. 

   ![Výběr možnosti Provést přesun dat a odeslání aktivit](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-perform-data-movement-compute-option.png)
3. Vyberte **Privátní síť** a klikněte na **Další**.
    
   ![Výběr privátní sítě](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-private-network.png)
4. Jako **Název** zadejte **MySelfHostedIR** a klikněte na **Další**. 

   ![Zadání názvu prostředí Integration Runtime](./media/tutorial-transform-data-using-hive-in-vnet-portal/integration-runtime-name.png) 
5. Kliknutím na tlačítko kopírování zkopírujte **ověřovací klíč** pro prostředí Integration Runtime a uložte ho. Nechte okno otevřené. Pomocí tohoto klíče zaregistrujete prostředí IR nainstalovaný na virtuálním počítači. 

   ![Zkopírování ověřovacího klíče](./media/tutorial-transform-data-using-hive-in-vnet-portal/copy-key.png)

### <a name="install-ir-on-a-virtual-machine"></a>Instalace prostředí IR na virtuálním počítači

1. Ve virtuálním počítači Azure stáhněte [modul runtime integrace v místním prostředí](https://www.microsoft.com/download/details.aspx?id=39717). Použijte **ověřovací klíč** získaný v předchozím kroku a toto místní prostředí Integration Runtime ručně zaregistrujte. 

    ![Registrace prostředí Integration Runtime](media/tutorial-transform-data-using-hive-in-vnet-portal/register-integration-runtime.png)

2. Po úspěšném zaregistrování místního prostředí Integration Runtime se zobrazí následující zpráva. 
   
    ![Úspěšně zaregistrováno](media/tutorial-transform-data-using-hive-in-vnet-portal/registered-successfully.png)
3. Klikněte na **Spustit Správce konfigurace**. Jakmile se uzel připojí ke cloudové službě, zobrazí se následující stránka: 
   
    ![Uzel je připojen](media/tutorial-transform-data-using-hive-in-vnet-portal/node-is-connected.png)

### <a name="self-hosted-ir-in-the-azure-data-factory-ui"></a>Místní prostředí IR v uživatelském rozhraní služby Azure Data Factory

1. V **uživatelském rozhraní služby Azure Data Factory** by se měl zobrazit název virtuálního počítače v místním prostředí a jeho stav.

   ![Existující uzly v místním prostředí](./media/tutorial-transform-data-using-hive-in-vnet-portal/existing-self-hosted-nodes.png)
2. Kliknutím na **Dokončit** zavřete okno **Instalace prostředí Integration Runtime**. Místním prostředí Integration Runtime se zobrazí v seznamu prostředí Integration Runtime.

   ![Místní prostředí IR v seznamu](./media/tutorial-transform-data-using-hive-in-vnet-portal/self-hosted-ir-in-list.png)


## <a name="create-linked-services"></a>Vytvoření propojených služeb

V této části vytvoříte a nasadíte dvě propojené služby:
- **Propojená služba Azure Storage**, která propojí účet služby Azure Storage s datovou továrnou. Toto úložiště používá cluster HDInsight jako primární. V tomto případě použijete tento účet služby Azure Storage k uložení skriptu Hivu a výstupu tohoto skriptu.
- **Propojená služba HDInsight**. Azure Data Factory odešle skript Hivu tomuto clusteru HDInsight ke spuštění.

### <a name="create-azure-storage-linked-service"></a>Vytvoření propojené služby Azure Storage

1. Přepněte na kartu **Propojené služby** a klikněte na **Nová**.

   ![Tlačítko Nová propojená služba](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-linked-service.png)    
2. V okně **Nová propojená služba** vyberte **Azure Blob Storage** a klikněte na **Pokračovat**. 

   ![Výběr služby Azure Blob Storage](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-azure-storage.png)
3. V okně **Nová propojená služba** proveďte následující kroky:

    1. Jako **Název** zadejte **AzureStorageLinkedService**.
    2. V části **Připojit prostřednictvím prostředí Integration Runtime** zadejte **MySelfHostedIR**.
    3. Jako **Název účtu úložiště** vyberte svůj účet úložiště Azure. 
    4. Pokud chcete otestovat připojení k účtu úložiště, klikněte na **Test připojení**.
    5. Klikněte na **Uložit**.
   
        ![Zadání účtu služby Azure Blob Storage](./media/tutorial-transform-data-using-hive-in-vnet-portal/specify-azure-storage-account.png)

### <a name="create-hdinsight-linked-service"></a>Vytvoření propojené služby HDInsight

1. Znovu klikněte na **Nová** a vytvořte další propojenou službu. 
    
   ![Tlačítko Nová propojená služba](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-linked-service.png)    
2. Přepněte na kartu **Výpočetní prostředí**, vyberte **Azure HDInsight** a klikněte na **Pokračovat**.

    ![Výběr služby Azure HDInsight](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-hdinsight.png)
3. V okně **Nová propojená služba** proveďte následující kroky:

    1. Jako **Název** zadejte **AzureHDInsightLinkedService**.
    2. Vyberte **Použít vlastní službu HDInsight**. 
    3. Jako **Cluster HDInsight** vyberte váš cluster HDInsight. 
    4. Zadejte **uživatelské jméno** pro cluster HDInsight.
    5. Zadejte **heslo** pro tohoto uživatele. 
    
        ![Nastavení služby Azure HDInsight](./media/tutorial-transform-data-using-hive-in-vnet-portal/specify-azure-hdinsight.png)

V tomto článku se předpokládá, že máte ke clusteru přístup přes internet. To znamená, že se ke clusteru můžete připojit třeba na `https://clustername.azurehdinsight.net`. Tato adresa se používá veřejnou brány, která není dostupná, pokud jste k omezení přístupu z internetu použili skupiny zabezpečení sítě (NSG) nebo uživatelem definované trasy (UDR). Aby datová továrna mohla odesílat úlohy do clusteru HDInsight ve službě Azure Virtual Network, musíte ji nakonfigurovat tak, aby tuto adresu URL bylo možné přeložit na privátní IP adresu brány, kterou používá HDInsight.

1. Na webu Azure Portal otevřete službu Virtual Network, ve které je HDInsight. Otevřete síťové rozhraní s názvem začínajícím textem `nic-gateway-0`. Poznamenejte si jeho privátní IP adresu. Příklad: 10.6.0.15. 
2. Pokud Azure Virtual Network má server DNS, aktualizujte záznam DNS tak, aby se adresa URL clusteru HDInsight `https://<clustername>.azurehdinsight.net` dala přeložit na `10.6.0.15`. Pokud ve službě Azure Virtual Network nemáte server DNS, můžete to dočasně obejít tak, že upravíte soubor hostitelů (C:\Windows\System32\drivers\etc) všech virtuálních počítačů, které se registrovaly jako uzly místního prostředí Integration Runtime, a to přidáním položky jako je tato: 

    `10.6.0.15 myHDIClusterName.azurehdinsight.net`

## <a name="create-a-pipeline"></a>Vytvoření kanálu 
V tomto kroku pomocí aktivity Hivu vytvoříte nový kanál. Tato aktivity spustí skript Hivu, který vrátí data z ukázkové tabulky a uloží je do cesty, které jste definovali.

Je třeba počítat s následujícím:

- Parametr **scriptPath** odkazuje na cestu ke skriptu Hivu v účtu Azure Storage, který jste použili pro MyStorageLinkedService. V této cestě se rozlišují velká a malá písmena.
- **Output** je argument použitý ve skriptu Hivu. Při zadávání odkazu na existující složku ve službě Azure Storage použijte formát `wasb://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/`. V této cestě se rozlišují velká a malá písmena. 

1. V uživatelském rozhraní služby Data Factory klikněte na symbol **+ (plus)** v levém podokně a pak klikněte na **Kanál**. 

    ![Nabídka Nový kanál](./media/tutorial-transform-data-using-hive-in-vnet-portal/new-pipeline-menu.png)
2. Na panelu nástrojů **Aktivity** rozbalte **HDInsight** a přetáhněte aktivitu **Hivu** na plochu návrháře kanálu. 

    ![Přetažení aktivity Hivu](./media/tutorial-transform-data-using-hive-in-vnet-portal/drag-drop-hive-activity.png)
3. V okně Vlastnosti přepněte na kartu **Cluster HDInsight** a jako **Propojená služba HDInsight** vyberte **AzureHDInsightLinkedService**.

    ![Výběr propojené služby HDInsight](./media/tutorial-transform-data-using-hive-in-vnet-portal/select-hdinsight-linked-service.png)
4. Přepněte na kartu **Skripty** a proveďte následující kroky: 

    1. Jako **Propojená služba se skriptem** zadejte **AzureStorageLinkedService**. 
    2. V části **Cesta k souboru** klikněte na **Procházet úložiště**. 
 
        ![Procházet úložiště](./media/tutorial-transform-data-using-hive-in-vnet-portal/browse-storage-hive-script.png)
    3. V okně **Zvolte soubor nebo složku** přejděte do složky **hivescripts** kontejneru **adftutorial**, vyberte soubor **hivescript.hql** a klikněte na **Dokončit**.  
        
        ![Zvolte soubor nebo složku](./media/tutorial-transform-data-using-hive-in-vnet-portal/choose-file-folder.png) 
    4. Ověřte, že se jako **Cesta k souboru** zobrazí **adftutorial/hivescripts/hivescript.hql**.

        ![Nastavení skriptu](./media/tutorial-transform-data-using-hive-in-vnet-portal/confirm-hive-script-settings.png)
    5. Na kartě **Skript** rozbalte část **Upřesnit**. 
    6. V části **Parametry** klikněte na **Automaticky vyplnit ze skriptu**. 
    7. Zadejte hodnotu parametru **Output** (Výstup) v následujícím formátu: `wasb://<Blob Container>@<StorageAccount>.blob.core.windows.net/outputfolder/`. Například: `wasb://adftutorial@mystorageaccount.blob.core.windows.net/outputfolder/`.
 
        ![Argumenty skriptu](./media/tutorial-transform-data-using-hive-in-vnet-portal/script-arguments.png)
1. Pokud chcete publikovat artefakty do služby Data Factory, klikněte na **Publikovat**.

    ![Publikování](./media/tutorial-transform-data-using-hive-in-vnet-portal/publish.png)

## <a name="trigger-a-pipeline-run"></a>Aktivace spuštění kanálu

1. Nejprve kanál ověřte kliknutím na tlačítko **Ověřit** na panelu nástrojů. Zavřete okno **Výstup ověření kanálu** kliknutím na **šipky doprava (>>)**. 

    ![Ověření kanálu](./media/tutorial-transform-data-using-hive-in-vnet-portal/validate-pipeline.png) 
2. Pokud chcete aktivovat spuštění kanálu, klikněte na Aktivační událost na panelu nástrojů a pak klikněte na Aktivovat. 

    ![Aktivovat](./media/tutorial-transform-data-using-hive-in-vnet-portal/trigger-now-menu.png)

## <a name="monitor-the-pipeline-run"></a>Monitorování spuštění kanálu

1. Vlevo přepněte na kartu **Monitorování**. V seznamu **Spuštění kanálu** se zobrazí spuštění kanálu. 

    ![Monitorování spuštění kanálu](./media/tutorial-transform-data-using-hive-in-vnet-portal/monitor-pipeline-runs.png)
2. Pokud chcete seznam aktualizovat, klikněte na **Aktualizovat**.
4. Pokud chcete zobrazit spuštění aktivit související se spuštěním kanálu, klikněte na **Zobrazit spuštění aktivit** ve sloupci **Akce**. Další odkazy na akce slouží k zastavení nebo opětovnému spuštění kanálu. 

    ![Zobrazení spuštění aktivit](./media/tutorial-transform-data-using-hive-in-vnet-portal/view-activity-runs-link.png)
5. Zobrazí se pouze jedno spuštění aktivit, protože kanál obsahuje pouze jednu aktivitu typu **HDInsightHive**. Pokud chcete přepnout zpět na předchozí zobrazení, klikněte na odkaz **Kanály** v horní části.

    ![Spuštění aktivit](./media/tutorial-transform-data-using-hive-in-vnet-portal/view-activity-runs.png)
6. Ověřte, že složka **outputfolder** kontejneru **adftutorial** obsahuje výstupní soubor. 

    ![Výstupní soubor](./media/tutorial-transform-data-using-hive-in-vnet-portal/output-file.png)

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste provedli následující kroky: 

> [!div class="checklist"]
> * Vytvoření datové továrny 
> * Vytvoření místního prostředí Integration Runtime
> * Vytvoření propojených služeb Azure Storage a Azure HDInsight
> * Vytvoření kanálu s aktivitou Hivu
> * Aktivace spuštění kanálu
> * Monitorování spuštění kanálu 
> * Ověření výstupu

Pokud se chcete dozvědět víc o transformaci dat pomocí clusteru Spark v Azure, přejděte k následujícímu kurzu:

> [!div class="nextstepaction"]
>[Větvení a řetězení tok řízení Data Factory](tutorial-control-flow-portal.md)



