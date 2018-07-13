---
title: 'Kurz: Vytvoření kanálu pomocí průvodce kopírováním | Dokumentace Microsoftu'
description: V tomto kurzu vytvoříte kanál služby Azure Data Factory s aktivitou kopírování pomocí průvodce kopírováním podporovaného službou Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: b87afb8e-53b7-4e1b-905b-0343dd096198
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: c0823fa839df98fa81f42bfbb3f7cd2bec6d7c1a
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38582532"
---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-data-factory-copy-wizard"></a>Kurz: Vytvoření kanálu s aktivitou kopírování pomocí průvodce kopírováním služby Data Factory.
> [!div class="op_single_selector"]
> * [Přehled a požadavky](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Průvodce kopírováním](data-factory-copy-data-wizard-tutorial.md)
> * [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Šablona Azure Resource Manageru](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi této služby, podívejte se na [kurz o aktivitě kopírování](../quickstart-create-data-factory-dot-net.md). 


V tomto kurzu se dozvíte, jak používat **Průvodce kopírováním** ke zkopírování dat z úložiště objektů blob v Azure do databáze Azure SQL. 

**Průvodce kopírováním**  Azure Data Factory vám umožní rychle vytvořit datové kanály, které kopírují data z podporovaných zdrojů úložišť dat do podporovaných cílů úložišť dat. Proto doporučujeme použít průvodce jako první krok k vytvoření ukázkového kanálu pro svůj scénář pohybu dat. Seznam úložišť dat podporovaných jako zdroje a cíle najdete v tématu [podporovaná úložiště dat](data-factory-data-movement-activities.md#supported-data-stores-and-formats).  

Tento návod ukazuje, jak vytvořit objekt pro vytváření dat Azure, spustit Průvodce kopírováním a projít posloupností kroků poskytnutí podrobností o vašem scénáři příjmu/pohybu dat. Po dokončení kroků v průvodci se automaticky vytvoří kanál s aktivitou kopírování pro kopírování dat z úložiště Azure Blob Storage do Azure SQL Database. Další informace o aktivitě kopírování najdete v tématu [Aktivity pohybu dat](data-factory-data-movement-activities.md).

## <a name="prerequisites"></a>Požadavky
Než se pustíte do tohoto kurzu, dokončete požadované kroky uvedené v článku [Přehled kurzu](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="create-data-factory"></a>Vytvoření objektu pro vytváření dat
V tomto kroku vytvoříte pomocí webu Azure Portal objekt pro vytváření dat Azure s názvem **ADFTutorialDataFactory**.

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
2. V nabídce v levém horním rohu klikněte na **Vytvořit prostředek**, pak na **Data a analýzy** a pak klikněte na **Data Factory**. 
   
   ![Nový -> Objekt pro vytváření dat](./media/data-factory-copy-data-wizard-tutorial/new-data-factory-menu.png)
2. V okně **Nový objekt pro vytváření dat**:
   
   1. Do pole **Název** zadejte **ADFTutorialDataFactory**.
       Název objektu pro vytváření dat Azure musí být globálně jedinečný. Pokud se zobrazí chyba: `Data factory name “ADFTutorialDataFactory” is not available`, změňte název datové továrny (třeba na váš_název_ADFTutorialDataFactoryDDMMYYYY) a zkuste to znovu. V tématu [Objekty pro vytváření dat – pravidla pojmenování](data-factory-naming-rules.md) najdete pravidla pojmenování artefaktů služby Data Factory.  
      
       ![Název objektu pro vytváření dat není k dispozici](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-not-available.png)    
   2. Vyberte své **předplatné** Azure.
   3. Pro skupinu prostředků proveďte jeden z následujících kroků: 
      
      - Vyberte možnost **Použít existující** a vyberte existující skupinu prostředků.
      - Vyberte možnost **Vytvořit nový** a zadejte název pro skupinu prostředků.
          
        Některé kroky v tomto kurzu vychází z předpokladu, že pro skupinu prostředků použijete název **ADFTutorialResourceGroup**. Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../../azure-resource-manager/resource-group-overview.md).
   4. Vyberte **umístění** pro příslušný objekt pro vytváření dat.
   5. Zaškrtněte políčko **Připnout na řídicí panel** v dolní části okna.  
   6. Klikněte na možnost **Vytvořit**.
      
       ![Okno Nový objekt pro vytváření dat](media/data-factory-copy-data-wizard-tutorial/new-data-factory-blade.png)            
3. Po vytvoření se zobrazí okno **Objekt pro vytváření dat**, jak je znázorněno na následujícím obrázku:
   
   ![Domovská stránka objektu pro vytváření dat](./media/data-factory-copy-data-wizard-tutorial/getstarted-data-factory-home-page.png)

## <a name="launch-copy-wizard"></a>Spuštění průvodce kopírováním
1. V okně Data Factory klikněte na **Kopírovat data**. Spustí se **průvodce kopírováním**. 
   
   > [!NOTE]
   > Pokud zjistíte, že se webový prohlížeč zasekl ve fázi „Autorizace…“, zakažte / zrušte zaškrtnutí políčka **Block third party cookies and site data** (Blokovat data souborů cookie a webů třetích stran) v nastavení prohlížeče nebo nechte toto nastavení povolené a vytvořte výjimku pro **login.microsoftonline.com**. Potom zkuste průvodce znovu spustit.
2. Na stránce **Vlastnosti**:
   
   1. Jako **Název úlohy** zadejte **CopyFromBlobToAzureSql**.
   2. Zadejte **popis** (volitelné).
   3. Změňte položky **Datum a čas zahájení** a **Datum a čas ukončení** tak, aby datum ukončení bylo nastaveno na dnešek a datum zahájení bylo o pět dnů dříve.  
   4. Klikněte na **Další**.  
      
      ![Nástroj pro kopírování – stránka Vlastnosti](./media/data-factory-copy-data-wizard-tutorial/copy-tool-properties-page.png) 
3. Na stránce **Source data store** (Zdrojové úložiště dat) klikněte na dlaždici **Azure Blob Storage**. Tato stránka slouží k zadání zdrojového úložiště dat pro úlohu kopírování. 
   
    ![Nástroj pro kopírování – stránka zdrojového úložiště dat](./media/data-factory-copy-data-wizard-tutorial/copy-tool-source-data-store-page.png)
4. Na stránce **Specify the Azure Blob storage account** (Zadejte účet Azure Blob Storage):
   
   1. Do pole **Název propojené služby** zadejte **AzureStorageLinkedService**.
   2. Ujistěte se, že je pro položku **Metoda výběru účtu** vybrána možnost **Z předplatných Azure**.
   3. Vyberte své **předplatné** Azure.  
   4. V seznamu účtů úložiště Azure dostupných ve zvoleném předplatném vyberte požadovaný **účet úložiště Azure**. Také si můžete zvolit, že chcete zadat nastavení účtu úložiště ručně. Stačí u položky **Account selection method** (Metoda výběru účtu) vybrat možnost **Enter manually** (Zadat ručně) a potom kliknout na **Další**. 
      
      ![Nástroj pro kopírování – zadání účtu Azure Blob Storage](./media/data-factory-copy-data-wizard-tutorial/copy-tool-specify-azure-blob-storage-account.png)
5. Na stránce **Choose the input file or folder** (Zvolte vstupní soubor nebo složku):
   
   1. Klikněte dvakrát na **adftutorial** (složka).
   2. Vyberte soubor **emp.txt** a klikněte na **Vybrat**.
      
      ![Nástroj pro kopírování – volba vstupního souboru nebo složky](./media/data-factory-copy-data-wizard-tutorial/copy-tool-choose-input-file-or-folder.png)
6. Na stránce **Zvolte vstupní soubor nebo složku** klikněte na **Další**. Nezaškrtávejte políčko **Binární kopie**. 
   
    ![Nástroj pro kopírování – volba vstupního souboru nebo složky](./media/data-factory-copy-data-wizard-tutorial/chose-input-file-folder.png) 
7. Na stránce **Nastavení formátu souboru** jsou uvedeny oddělovače a schéma, které je automaticky zjištěno průvodcem při analýze souboru. Můžete také zadat oddělovače ručně, pokud chcete, aby Průvodce kopírováním zastavil automatické zjišťování, nebo pokud je chcete přepsat. Po zkontrolování oddělovačů a náhledu dat klikněte na **Další**. 
   
    ![Nástroj pro kopírování – nastavení formátu souboru](./media/data-factory-copy-data-wizard-tutorial/copy-tool-file-format-settings.png)  
8. Na stránce Cílové úložiště dat vyberte možnost **Azure SQL Database** a potom klikněte na **Další**.
   
    ![Nástroj pro kopírování – volba cílového úložiště](./media/data-factory-copy-data-wizard-tutorial/choose-destination-store.png)
9. Na stránce **Specify the Azure SQL database** (Určete databázi SQL Azure):
   
   1. Do pole **Název připojení** zadejte hodnotu **AzureSqlLinkedService**.
   2. Ujistěte se, že je pro položku **Metoda výběru serveru/databáze** vybrána možnost **Z předplatných Azure**.
   3. Vyberte své **předplatné** Azure.  
   4. Vyberte možnosti v polích **Název serveru** a **Databáze**.
   5. Zadejte **Uživatelské jméno** a **Heslo**.
   6. Klikněte na **Další**.  
      
      ![Nástroj pro kopírování – určení Azure SQL Database](./media/data-factory-copy-data-wizard-tutorial/specify-azure-sql-database.png)
10. Na stránce **Mapování tabulek** vyberte v rozevíracím seznamu poli **Cíl** možnost **emp** a potom klikněte na **šipku dolů** (volitelné). Tím zobrazíte schéma a náhled dat.
    
     ![Nástroj pro kopírování – mapování tabulek](./media/data-factory-copy-data-wizard-tutorial/copy-tool-table-mapping-page.png) 
11. Na stránce **Schema mapping** (Mapování schématu) klikněte na tlačítko **Další**.
    
    ![Nástroj pro kopírování – mapování schématu](./media/data-factory-copy-data-wizard-tutorial/schema-mapping-page.png)
12. Na stránce **Nastavení výkonu** klikněte na **Další**. 
    
    ![Nástroj pro kopírování – nastavení výkonu](./media/data-factory-copy-data-wizard-tutorial/performance-settings.png)
13. Na stránce **Souhrn** zkontrolujte informace a klikněte na **Dokončit**. Průvodce v objektu pro vytváření dat (ze kterého jste průvodce kopírováním spustili) vytvoří dvě propojené služby, dvě datové sady (vstupní a výstupní) a jeden kanál. 
    
    ![Nástroj pro kopírování – nastavení výkonu](./media/data-factory-copy-data-wizard-tutorial/summary-page.png)

## <a name="launch-monitor-and-manage-application"></a>Spuštění aplikace pro monitorování a správu
1. Na stránce **Nasazení** klikněte na následující odkaz: `Click here to monitor copy pipeline`.
   
   ![Nástroj pro kopírování – nasazení bylo úspěšné](./media/data-factory-copy-data-wizard-tutorial/copy-tool-deployment-succeeded.png)  
2. Monitorovací aplikace se spouští na samostatné kartě ve webovém prohlížeči.   
   
   ![Monitorovací aplikace](./media/data-factory-copy-data-wizard-tutorial/monitoring-app.png)   
3. Kliknutím na tlačítko **Aktualizovat** v seznamu **OKNA AKTIVITY** ve spodní části zobrazíte nejnovější stav hodinových řezů. Uvidíte pět oken aktivity, každé pro jeden den mezi počátečním a koncovým časem pro kanál. Seznam se automaticky neobnovuje, takže možná budete muset několikrát kliknout na tlačítko Aktualizovat, než uvidíte všechna okna aktivity ve stavu Připraveno. 
4. Vyberte ze seznamu okno aktivity. Jeho podrobnosti si zobrazíte napravo v **Průzkumníku okna aktivity**.

    ![Podrobnosti o okně aktivity](media/data-factory-copy-data-wizard-tutorial/activity-window-details.png)    

    Všimněte si, že data 11., 12., 13., 14. a 15. jsou zelená, což znamená, že denní výstup řezů byl u těchto dnů už vytvořen. Toto barevné kódování můžete také vidět u kanálu a výstupu datové sady v zobrazení diagramu. Všimněte si, že v předchozím kroku byly už vytvořeny dva řezy, jeden řez se právě zpracovává a ty další dva na zpracování čekají (podle barevného kódování). 

    Další informace o používání této aplikace najdete v článku [Monitorování a správa kanálu pomocí monitorovací aplikace](data-factory-monitor-manage-app.md).

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste v operaci kopírování použili úložiště objektů blob jako zdrojové úložiště dat a databázi Azure SQL jako cílové úložiště dat. Následující tabulka obsahuje seznam úložišť dat podporovaných jako zdroje a cíle aktivitou kopírování: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Podrobnosti o polích nebo vlastnostech, které vidíte v průvodci kopírováním pro úložiště dat získáte po kliknutí na odkaz úložiště dat v tabulce. 