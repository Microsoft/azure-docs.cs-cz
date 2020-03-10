---
title: Transformace pomocí Azure Databricks
description: Naučte se používat šablonu řešení k transformaci dat pomocí poznámkového bloku datacihly v Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/03/2020
ms.openlocfilehash: e771bc152ab50f907a8f2ad384e887c00d3f627a
ms.sourcegitcommit: e6bce4b30486cb19a6b415e8b8442dd688ad4f92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2020
ms.locfileid: "78933891"
---
# <a name="transformation-with-azure-databricks"></a>Transformace pomocí Azure Databricks

V tomto kurzu vytvoříte kompletní kanál obsahující **ověřování**, **kopírování**a aktivity **poznámkových bloků** v Data Factory.

-   Aktivita **ověření** se používá k zajištění, že zdrojová datová sada je připravená na využití pro příjem dat, než se aktivuje úloha kopírování a analýzy.

-   Aktivita **kopírování** zkopíruje zdrojový soubor nebo datovou sadu do úložiště jímky. Úložiště jímky je v poznámkovém bloku datacihly připojené jako DBFS, aby bylo možné datovou sadu přímo spotřebovat Sparkem.

-   Aktivita **poznámkového bloku datacihly** spustí Poznámkový blok datacihly, který transformuje datovou sadu a přidá ji do zpracované složky/SQL DW.

Chcete-li tuto šablonu ponechat jednoduchou, šablona nevytvoří plánovanou aktivační událost. V případě potřeby je můžete přidat.

![1](media/solution-template-Databricks-notebook/pipeline-example.png)

## <a name="prerequisites"></a>Předpoklady

1. Vytvořte **účet Blob Storage** a kontejner s názvem `sinkdata`, který se použije jako **jímka**. Poznamenejte si **název účtu úložiště**, **název kontejneru**a **přístupový klíč**, protože se na ně odkazuje později v šabloně.

2. Ujistěte se, že máte **pracovní prostor Azure Databricks** nebo vytvořte nový.

3. **Importujte Poznámkový blok pro transformaci**. 
    1. V Azure Databricks odkaz na následující snímky obrazovky pro import **transformačního** poznámkového bloku do pracovního prostoru datacihly. Nemusí být ve stejném umístění, jak je uvedeno níže, ale zapamatujte si cestu, kterou si zvolíte později.
   
       ![2](media/solution-template-Databricks-notebook/import-notebook.png)    
    
    1. Vyberte Importovat z: **Adresa URL**a do textového pole zadejte následující adresu URL:
    
       * `https://adflabstaging1.blob.core.windows.net/share/Transformations.html`
        
       ![3](media/solution-template-Databricks-notebook/import-from-url.png)    

4. Teď pojďme aktualizovat **Poznámkový** blok s informacemi o připojení úložiště. V importovaném poznámkovém bloku klikněte na **příkaz 5** (jak ukazuje následující fragment kódu) a nahraďte `<storage name>`a `<access key>` vlastními informacemi o připojení úložiště. Zajistěte, aby byl tento účet stejný účet úložiště, který jste vytvořili dříve, a obsahuje kontejner `sinkdata`.

    ```python
    # Supply storageName and accessKey values  
    storageName = "<storage name>"  
    accessKey = "<access key>"  

    try:  
      dbutils.fs.mount(  
        source = "wasbs://sinkdata\@"+storageName+".blob.core.windows.net/",  
        mount_point = "/mnt/Data Factorydata",  
        extra_configs = {"fs.azure.account.key."+storageName+".blob.core.windows.net": accessKey})  

    except Exception as e:  
      # The error message has a long stack track. This code tries to print just the relevant line indicating what failed.

    import re
    result = re.findall(r"\^\s\*Caused by:\s*\S+:\s\*(.*)\$", e.message, flags=re.MULTILINE)
    if result:
      print result[-1] \# Print only the relevant error message
    else:  
      print e \# Otherwise print the whole stack trace.  
    ```

5.  Vygenerujte **přístupový token datacihly** pro Data Factory pro přístup k datacihlům. **Uložte přístupový token** pro pozdější použití při vytváření propojené služby datacihly, což vypadá nějak takto: "dapi32db32cbb4w6eee18b7d87e45exxxxxx".

    ![4](media/solution-template-Databricks-notebook/user-setting.png)

    ![5](media/solution-template-Databricks-notebook/generate-new-token.png)

## <a name="how-to-use-this-template"></a>Jak používat tuto šablonu

1.  Přejít na **transformaci pomocí šablony Azure Databricks** . Vytvořte nové propojené služby pro následující připojení. 
    
    ![Nastavení připojení](media/solution-template-Databricks-notebook/connections-preview.png)

    1.  **Připojení zdrojového objektu BLOB** – pro přístup ke zdrojovým datům. 
        
        Pro tuto ukázku můžete použít veřejné úložiště objektů blob, které obsahuje zdrojové soubory. Odkaz na následující snímek obrazovky pro konfiguraci. Použijte níže uvedenou **adresu URL SAS** pro připojení ke zdrojovému úložišti (přístup jen pro čtení): 
        * `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        ![6](media/solution-template-Databricks-notebook/source-blob-connection.png)

    1.  **Připojení k cílovému objektu BLOB** – pro kopírování dat do. 
        
        V propojené službě jímky vyberte úložiště vytvořené v **předpokladu** 1.

        ![7](media/solution-template-Databricks-notebook/destination-blob-connection.png)

    1.  **Azure Databricks** – pro připojení ke clusteru datacihly.

        Vytvořte propojenou službu datacihly pomocí přístupového klíče vygenerovaného v **požadavku** 2. c. Máte-li *interaktivní cluster*, můžete jej vybrat. (V tomto příkladu se používá možnost *nový cluster úloh* .)

        ![8](media/solution-template-Databricks-notebook/databricks-connection.png)

1. Vyberte **použít tuto šablonu**a zobrazí se vytvořený kanál, jak je znázorněno níže:
    
    ![Vytvoření kanálu](media/solution-template-Databricks-notebook/new-pipeline.png)   

## <a name="pipeline-introduction-and-configuration"></a>Úvod a konfigurace kanálu

V nově vytvořeném kanálu je většina nastavení nakonfigurovaná automaticky s výchozími hodnotami. Prohlédněte si konfigurace a aktualizujte tam, kde je to potřeba, podle vlastních nastavení. Podrobnosti můžete zobrazit níže v části pokyny a snímky obrazovky pro referenci.

1.  Pro kontrolu dostupnosti zdroje je vytvořen **příznak dostupnosti** aktivity ověření. *SourceAvailabilityDataset* vytvořený v předchozím kroku je vybraný jako datová sada.

    ![12](media/solution-template-Databricks-notebook/validation-settings.png)

1.  Soubor aktivity kopírování **na objekt BLOB** se vytvoří pro kopírování datové sady ze zdroje do jímky. Odkaz na následující snímky obrazovky pro konfiguraci zdrojového kódu a jímky v aktivitě kopírování

    ![13](media/solution-template-Databricks-notebook/copy-source-settings.png)

    ![14](media/solution-template-Databricks-notebook/copy-sink-settings.png)

1.  Vytvoří se **transformace** aktivity poznámkového bloku a vybraná propojená služba vytvořená v předchozím kroku.
    ![16](media/solution-template-Databricks-notebook/notebook-activity.png)

     1. Vyberte kartu **Nastavení** . V případě *cesty poznámkového bloku*Šablona definuje cestu ve výchozím nastavení. Možná budete muset vyhledat a vybrat správnou cestu poznámkového bloku nahranou v **požadovaném** umístění 2. 

         ![17](media/solution-template-Databricks-notebook/notebook-settings.png)
    
     1. Podívejte se na *základní parametry* vytvořené, jak je znázorněno na snímku obrazovky. Budou předány do poznámkového bloku datacihly z Data Factory. 

         ![Základní parametry](media/solution-template-Databricks-notebook/base-parameters.png)

1.  **Parametry kanálu** jsou definovány níže.

    ![15](media/solution-template-Databricks-notebook/pipeline-parameters.png)

1. Nastavení datových sad.
    1.  **SourceAvailabilityDataset** je vytvořen pro kontrolu, zda jsou zdrojová data k dispozici.

        ![9](media/solution-template-Databricks-notebook/source-availability-dataset.png)

    1.  **SourceFilesDataset** – pro kopírování zdrojových dat.

        ![10](media/solution-template-Databricks-notebook/source-file-dataset.png)

    1.  **DestinationFilesDataset** – pro kopírování do jímky/cílového umístění.

        1.  Propojená služba – *sinkBlob_LS* vytvořená v předchozím kroku.

        2.  Cesta k souboru – *sinkdata/staged_sink*.

            ![11](media/solution-template-Databricks-notebook/destination-dataset.png)


1.  Vyberte **ladit** a spusťte kanál. Podrobnější protokoly Spark najdete v protokolech pro odkazy na datacihly.

    ![18](media/solution-template-Databricks-notebook/pipeline-run-output.png)

    Datový soubor můžete také ověřit pomocí Průzkumníka služby Storage. (Pro korelaci s Data Factory spuštění kanálu tento příklad připojí ID spuštění kanálu z objektu pro vytváření dat do výstupní složky. Tímto způsobem můžete sledovat zpět soubory generované při každém spuštění.)

    ![19](media/solution-template-Databricks-notebook/verify-data-files.png)

## <a name="next-steps"></a>Další kroky

- [Úvod do Azure Data Factory](introduction.md)
