---
title: Nástroj data pro přírůstkové kopírování nových a aktualizovaných souborů
description: Vytvořte datovou továrnu Azure a pak pomocí nástroje Kopírování dat postupně načtěte nové soubory založené na LastModifiedDate.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 1/24/2019
ms.openlocfilehash: 5c20196bd243d025d58f7cc08e015e1e0038e178
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74217792"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Přírůstkové kopírování nových a změněných souborů na základě LastModifiedDate pomocí nástroje Kopírování dat

V tomto kurzu použijete Azure Portal k vytvoření datové továrny. Pak použijete nástroj Kopírování dat k vytvoření kanálu, který postupně kopíruje jenom nové a změněné soubory, a to na základě jejich **LastModifiedDate** ze služby Azure Blob Storage do úložiště objektů BLOB v Azure.

Díky tomu ADF bude kontrolovat všechny soubory ze zdrojového úložiště, použít filtr souborů podle jejich LastModifiedDate a kopírovat nový a aktualizovaný soubor pouze od posledního času do cílového úložiště.  Počítejte s tím, že pokud povolíte ADF velké množství souborů, ale kopírujete jenom několik souborů do cíle, očekává se i delší doba, vzhledem k tomu, že kontrola souborů spotřebovává čas.   

> [!NOTE]
> Pokud se službou Azure Data Factory začínáte, přečtěte si téma [Seznámení se službou Azure Data Factory](introduction.md).

V tomto kurzu budete provádět následující úlohy:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření kanálu pomocí nástroje pro kopírování dat
> * Monitorování spuštění aktivit a kanálu

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure:** Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* **Účet úložiště Azure**: jako _zdroj_ dat a úložiště dat _jímky_ použijte úložiště objektů BLOB. Pokud účet úložiště Azure nemáte, přečtěte si pokyny v tématu [Vytvoření účtu úložiště](../storage/common/storage-quickstart-create-account.md).

### <a name="create-two-containers-in-blob-storage"></a>Vytvoření dvou kontejnerů v úložišti objektů BLOB

Provedením těchto kroků Připravte úložiště objektů BLOB pro tento kurz.

1. Vytvořte kontejner s názvem **source**. K provedení této úlohy můžete použít různé nástroje, například [Průzkumník služby Azure Storage](https://storageexplorer.com/).

2. Vytvořte kontejner s názvem **Destination**. 

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. V nabídce vlevo vyberte **vytvořit prostředek** > **data a analýzy** > **Data Factory**: 
   
   ![Výběr datové továrny v podokně Nový](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Do pole **Název** na stránce **Nová datová továrna** zadejte **ADFTutorialDataFactory**. 
 
   Název datové továrny musí být _globálně jedinečný_. Možná se zobrazí následující chybová zpráva:
   
   ![Nová datová továrna – chybová zpráva](./media/doc-common-process/name-not-available-error.png)

   Pokud se zobrazí chybová zpráva týkající se hodnoty názvu, zadejte jiný název datové továrny. Použijte například název _**vaše_jméno**_ **ADFTutorialDataFactory**. Pravidla pojmenování artefaktů služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](naming-rules.md).
3. Vyberte **předplatné** Azure, ve kterém vytvoříte novou datovou továrnu. 
4. U položky **Skupina prostředků** proveďte jeden z následujících kroků:
     
    * Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků.

    * Vyberte **Vytvořit novou** a zadejte název skupiny prostředků. 
         
    Informace o skupinách prostředků najdete v tématu [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-overview.md).

5. V části **verze**vyberte **v2**.
6. V části **Umístění** vyberte umístění datové továrny. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat (například Azure Storage a SQL Database) a výpočetní prostředí (například Azure HDInsight), které vaše Datová továrna používá, můžou být v jiných umístěních a oblastech.
7. Zaškrtněte **Připnout na řídicí panel**. 
8. Vyberte **Vytvořit**.
9. Na řídicím panelu se podívejte na dlaždici **nasazení Data Factory** , kde vidíte stav procesu.

    ![Nasazování dlaždice Data Factory](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. Po vytvoření se zobrazí domovská stránka **Datová továrna**.
   
    ![Domovská stránka objektu pro vytváření dat](./media/doc-common-process/data-factory-home-page.png)
11. Pokud chcete na samostatné kartě otevřít Azure Data Factory uživatelské rozhraní (UI), vyberte dlaždici **autora & monitorování** . 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Vytvoření kanálu pomocí nástroje pro kopírování dat

1. Na stránce **Začínáme** vyberte název **kopírování dat** pro otevření nástroje kopírování dat. 

   ![Dlaždice nástroje pro kopírování dat](./media/doc-common-process/get-started-page.png)
   
2. Na stránce **vlastnosti** proveďte následující kroky:

    a. V části **název úlohy**zadejte **DeltaCopyFromBlobPipeline**.

    b. V části **úkol tempo** nebo **plán úlohy**vyberte možnost **spouštět pravidelně podle plánu**.

    c. V části **typ triggeru**vyberte **okno bubnu**.
    
    d. V části **opakování**zadejte **15 minut**. 
    
    e. Vyberte **Next** (Další). 
    
    Uživatelské rozhraní služby Data Factory vytvoří kanál se zadaným názvem úlohy. 

    ![Stránka Vlastnosti](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)
    
3. Na stránce **Source data store** (Zdrojové úložiště dat) proveďte následující kroky:

    a. Chcete-li přidat připojení, vyberte **+ vytvořit nové připojení**.
    
    ![Stránka Zdrojové úložiště dat](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page.png)

    b. Z Galerie vyberte **Azure Blob Storage** a pak vyberte **pokračovat**.
    
    ![Stránka Zdrojové úložiště dat](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. Na stránce **Nová propojená služba** vyberte v seznamu **název účtu úložiště** svůj účet úložiště a pak vyberte **Dokončit**.
    
    ![Stránka Zdrojové úložiště dat](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d. Vyberte nově vytvořenou propojenou službu a pak vyberte **Další**. 
    
   ![Stránka Zdrojové úložiště dat](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. Na stránce **Choose the input file or folder** (Zvolit vstupní soubor nebo složku) proveďte následující kroky:
    
    a. Procházejte a vyberte **zdrojovou** složku a pak vyberte **zvolit**.
    
    ![Zvolte vstupní soubor nebo složku](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)
    
    b. V části **chování načítání souborů**vyberte **přírůstkové načtení: LastModifiedDate**.
    
    ![Zvolte vstupní soubor nebo složku](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-loading-behavior.png)
    
    c. Zaškrtněte **binární kopii** a vyberte **Další**.
    
     ![Zvolte vstupní soubor nebo složku](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)
     
5. Na stránce **cílové úložiště dat** vyberte **AzureBlobStorage**. Toto je stejný účet úložiště jako zdrojové úložiště dat. Pak vyberte **Další**.

    ![Stránka Cílové úložiště dat](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/destination-data-store-page-select-linkedservice.png)
    
6. Na stránce **Choose the output file or folder** (Zvolit výstupní soubor nebo složku) proveďte následující kroky:
    
    a. Vyhledejte a vyberte **cílovou** složku a pak vyberte **zvolit**.
    
    ![Zvolte výstupní soubor nebo složku](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)
    
    b. Vyberte **Next** (Další).
    
     ![Zvolte výstupní soubor nebo složku](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/click-next-after-output-folder.png)
    
7. Na stránce **Settings** (Nastavení) vyberte **Next** (Další). 

    ![Stránka Nastavení](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/settings-page.png)
    
8. Na stránce **Souhrn** zkontrolujte nastavení a pak vyberte **Další**.

    ![Stránka souhrnu](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)
    
9. Na stránce **Nasazení** vyberte **Monitorovat** a začněte monitorovat kanál (úlohu).

    ![Stránka Nasazení](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)
    
10. Všimněte si, že je vlevo automaticky vybraná karta **Monitorování**. Sloupec **Akce** obsahuje odkazy na zobrazení podrobností o spuštění aktivit a na opětovné spuštění kanálu. Vyberte **aktualizovat** a aktualizujte seznam a vyberte odkaz **Zobrazit spuštění aktivit** ve sloupci **Akce** . 

    ![Aktualizovat seznam a vybrat zobrazení spuštění aktivit](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. Kanál obsahuje pouze jednu aktivitu (aktivita kopírování), takže se zobrazí pouze jedna položka. Podrobnosti o operaci kopírování zobrazíte výběrem odkazu **Podrobnosti** (ikona brýlí) ve sloupci **Akce**. 

    ![Aktivita kopírování je v kanálu.](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)
    
    Vzhledem k tomu, že ve **zdrojovém** kontejneru účtu BLOB Storage není žádný soubor, nebude se do **cílového** kontejneru v účtu BLOB Storage kopírovat žádný soubor.
    
    ![Žádný soubor ve zdrojovém kontejneru nebo cílovém kontejneru](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)
    
12. Vytvořte prázdný textový soubor a pojmenujte ho **Soubor1. txt**. Nahrajte tento textový soubor do **zdrojového** kontejneru v účtu úložiště. K provedení těchto úloh můžete použít různé nástroje, například [Průzkumníka služby Azure Storage](https://storageexplorer.com/).   

    ![Vytvoření souborů Soubor1. txt a nahrání do zdrojového kontejneru](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)
    
13. Pokud se chcete vrátit do zobrazení **spuštění kanálu** , vyberte **všechna spuštění kanálu**a počkejte na automatické spuštění stejného kanálu.  

    ![Vybrat všechna spuštění kanálu](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. Vyberte **Zobrazit spuštění aktivit** pro druhý běh kanálu po jeho zobrazení. Podrobnosti si pak Projděte stejným způsobem jako u prvního spuštění kanálu.  

    ![Vyberte Zobrazit spuštění aktivit a zkontrolovat podrobnosti.](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs5.png)

    Uvidíte, že se ze **zdrojového** kontejneru zkopíroval jeden soubor (Soubor1. txt) do **cílového** kontejneru vašeho účtu služby Blob Storage.
    
    ![Soubor1. txt se zkopíroval ze zdrojového kontejneru do cílového kontejneru.](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)
    
15. Vytvořte další prázdný textový soubor a pojmenujte ho **Soubor2. txt**. Nahrajte tento textový soubor do **zdrojového** kontejneru v účtu služby Blob Storage.   
    
16. Opakujte kroky 13 a 14 pro tento druhý textový soubor. Uvidíte, že se ze **zdrojového** kontejneru zkopíroval jenom nový soubor (Soubor2. txt) do **cílového** kontejneru vašeho účtu úložiště v dalším spuštění kanálu.  
    
    ![Soubor2. txt byl zkopírován ze zdrojového kontejneru do cílového kontejneru.](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs7.png)

    Můžete to také ověřit pomocí [Průzkumník služby Azure Storage](https://storageexplorer.com/) pro skenování souborů.
    
    ![Kontrolovat soubory pomocí Průzkumník služby Azure Storage](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>Další kroky
Pokud se chcete dozvědět víc o transformaci dat pomocí Apache Spark clusteru v Azure, přejděte k následujícímu kurzu:

> [!div class="nextstepaction"]
>[Transformace dat v cloudu pomocí Apache Sparkho clusteru](tutorial-transform-data-spark-portal.md)