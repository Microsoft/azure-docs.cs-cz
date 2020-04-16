---
title: Transformace se službou Azure Databricks
description: Zjistěte, jak pomocí šablony řešení transformovat data pomocí poznámkového bloku Databricks v Azure Data Factory.
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
ms.openlocfilehash: 65b89a13637f5a4e1712995a6ac58d88b4421806
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414852"
---
# <a name="transformation-with-azure-databricks"></a>Transformace se službou Azure Databricks

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

V tomto kurzu vytvoříte kanál od konce, který obsahuje **aktivity ověření**, **kopírování dat**a **poznámkového bloku** v Azure Data Factory.

- **Ověření** zajistí, že zdrojová datová sada je připravena pro příjem dat před aktivací úlohy kopírování a analýzy.

- **Kopírování dat** duplikuje zdrojovou datovou sadu do úložiště jímky, které se namontuje jako DBFS v poznámkovém bloku Azure Databricks. Tímto způsobem může být datová sada přímo spotřebována Spark.

- **Poznámkový blok** aktivuje poznámkový blok Databricks, který transformuje datovou sadu. Také přidá datovou sadu do zpracované složky nebo Azure SQL Data Warehouse.

Pro jednoduchost šablona v tomto kurzu nevytvoří naplánovanou aktivační událost. V případě potřeby jej můžete přidat.

![Diagram potrubí](media/solution-template-Databricks-notebook/pipeline-example.png)

## <a name="prerequisites"></a>Požadavky

- Účet úložiště objektů blob Azure `sinkdata` s kontejnerem volá pro použití jako jímky.

  Poznamenejte si název účtu úložiště, název kontejneru a přístupový klíč. Tyto hodnoty budete potřebovat později v šabloně.

- Pracovní prostor Azure Databricks.

## <a name="import-a-notebook-for-transformation"></a>Import poznámkového bloku pro transformaci

Import **poznámkového** bloku Transformace do pracovního prostoru Databricks:

1. Přihlaste se k pracovnímu prostoru Azure Databricks a pak vyberte **Importovat**.
       ![Příkaz Nabídky pro import](media/solution-template-Databricks-notebook/import-notebook.png) pracovního prostoru: Cesta pracovního prostoru se může lišit od zobrazené cesty, ale nezapomeňte ji na později.
1. Vyberte **Importovat z: URL**. Do textového pole `https://adflabstaging1.blob.core.windows.net/share/Transformations.html`zadejte .

   ![Výběry pro import poznámkového bloku](media/solution-template-Databricks-notebook/import-from-url.png)

1. Nyní pojďme aktualizovat **transformace** poznámkový blok s informacemi o připojení úložiště.

   V importovaném poznámkovém bloku přejděte na **příkaz 5,** jak je znázorněno na následujícím fragmentu kódu.

   - Nahraďte `<storage name>`a `<access key>` zadejte vlastní informace o připojení úložiště.
   - Použijte účet úložiště `sinkdata` s kontejnerem.

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

1. Vygenerujte **přístupový token Databricks** pro Data Factory pro přístup k Databricks.
   1. V pracovním prostoru Databricks vyberte ikonu uživatelského profilu v pravém horním části.
   1. Vyberte **nastavení uživatele**.
    ![Příkaz Nabídky pro uživatelská nastavení](media/solution-template-Databricks-notebook/user-setting.png)
   1. Na kartě **Přístupové tokeny** vyberte **Generovat nový token.**
   1. Vyberte **Generovat**.

    ![Tlačítko "Generovat"](media/solution-template-Databricks-notebook/generate-new-token.png)

   *Uložte přístupový token* pro pozdější použití při vytváření propojené služby Databricks. Přístupový token vypadá `dapi32db32cbb4w6eee18b7d87e45exxxxxx`podobně jako .

## <a name="how-to-use-this-template"></a>Jak používat tuto šablonu

1. Přejděte na šablonu **Transformace pomocí Azure Databricks** a vytvořte nové propojené služby pro následující připojení.

   ![Nastavení připojení](media/solution-template-Databricks-notebook/connections-preview.png)

    - **Zdroj ové připojení k objektu blob** – pro přístup ke zdrojovým datům.

       Pro toto cvičení můžete použít veřejné úložiště objektů blob, které obsahuje zdrojové soubory. Odkaz na následující snímek obrazovky pro konfiguraci. Pomocí následující **adresy URL SAS** se připojte ke zdrojovému úložišti (přístup jen pro čtení):

       `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        ![Výběry pro metodu ověřování a adresu URL SAS](media/solution-template-Databricks-notebook/source-blob-connection.png)

    - **Cílové připojení k objektu blob** – pro uložení zkopírovaných dat.

       V okně **Nová propojená služba** vyberte objekt blob úložiště jímky.

       ![Objekt blob úložiště jímky jako nová propojená služba](media/solution-template-Databricks-notebook/destination-blob-connection.png)

    - **Azure Databricks** – pro připojení ke clusteru Databricks.

        Vytvořte službu propojenou s datovými cihlami pomocí přístupového klíče, který jste dříve vygenerovali. Můžete se rozhodnout vybrat *interaktivní cluster,* pokud ho máte. Tento příklad používá možnost **Nový cluster úloh.**

        ![Výběry pro připojení ke clusteru](media/solution-template-Databricks-notebook/databricks-connection.png)

1. Vyberte **Použít tuto šablonu**. Zobrazí se vytvořený kanál.

    ![Vytvoření kanálu](media/solution-template-Databricks-notebook/new-pipeline.png)

## <a name="pipeline-introduction-and-configuration"></a>Úvod a konfigurace kanálu

V novém kanálu je většina nastavení konfigurována automaticky s výchozími hodnotami. Zkontrolujte konfigurace kanálu a proveďte potřebné změny.

1. V **příznaku Dostupnost**aktivity **ověření** ověřte, zda je hodnota **zdrojové datové sady** nastavena na `SourceAvailabilityDataset` hodnotu, kterou jste vytvořili dříve.

   ![Hodnota zdrojové datové sady](media/solution-template-Databricks-notebook/validation-settings.png)

1. V části Kopírovat soubor mezi **daty** z oblasti **objektů blob**zkontrolujte karty **Zdroj** a **Jímka.** V případě potřeby změňte nastavení.

   - **Karta** ![Zdroj Zdroj Zdroj](media/solution-template-Databricks-notebook/copy-source-settings.png)

   - Karta ![ **Jímka dřezu**](media/solution-template-Databricks-notebook/copy-sink-settings.png)

1. V **transformaci aktivity poznámkového bloku** zkontrolujte a aktualizujte cesty a nastavení podle potřeby. **Transformation**

   **Propojená služba Databricks** by měla být předem vyplněna hodnotou ![z předchozího kroku, jak je znázorněno: Vyplněná hodnota propojené služby Databricks](media/solution-template-Databricks-notebook/notebook-activity.png)

   Kontrola nastavení **poznámkového bloku:**
  
    1. Vyberte kartu **Nastavení.** V **případě cesty poznámkového bloku**ověřte, zda je výchozí cesta správná. Možná budete muset procházet a zvolit správnou cestu poznámkového bloku.

       ![Cesta k poznámkovému bloku](media/solution-template-Databricks-notebook/notebook-settings.png)

    1. Rozbalte volič **základních parametrů** a ověřte, zda parametry odpovídají tomu, co je zobrazeno na následujícím snímku obrazovky. Tyto parametry jsou předány databricks notebook z data factory.

       ![Základní parametry](media/solution-template-Databricks-notebook/base-parameters.png)

1. Ověřte, zda **parametry kanálu** odpovídají tomu, co je zobrazeno na následujícím snímku obrazovky: ![Parametry kanálu](media/solution-template-Databricks-notebook/pipeline-parameters.png)

1. Připojte se k datovým souborům.

   - **SourceAvailabilityDataset** - zkontrolujte, zda jsou k dispozici zdrojová data.

     ![Výběry pro propojenou službu a cestu k souboru pro sourceavailabilitydataset](media/solution-template-Databricks-notebook/source-availability-dataset.png)

   - **SourceFilesDataset** - pro přístup ke zdrojovým datům.

       ![Výběry pro propojenou službu a cestu k souboru pro SourceFilesDataset](media/solution-template-Databricks-notebook/source-file-dataset.png)

   - **DestinationFilesDataset** - zkopírovat data do cílového umístění jímky. Použijte následující hodnoty:

     - **Propojená služba** - `sinkBlob_LS`vytvořená v předchozím kroku.

     - **Cesta k souboru** - `sinkdata/staged_sink`.

       ![Výběry pro propojenou službu a cestu k souboru pro DestinationFilesDataset](media/solution-template-Databricks-notebook/destination-dataset.png)

1. Chcete-li kanál spustit, vyberte **možnost Ladění.** Můžete najít odkaz na Databricks protokoly pro podrobnější Protokoly Spark.

    ![Propojení s protokoly Databricks z výstupu](media/solution-template-Databricks-notebook/pipeline-run-output.png)

    Datový soubor můžete také ověřit pomocí Průzkumníka úložiště Azure.

    > [!NOTE]
    > Pro korelaci s spuštěním kanálu Data Factory tento příklad připojí ID spuštění kanálu z továrny dat do výstupní složky. To pomáhá sledovat soubory generované každým spuštěním.
    > ![ID spuštění připojeného kanálu](media/solution-template-Databricks-notebook/verify-data-files.png)

## <a name="next-steps"></a>Další kroky

- [Seznámení se službou Azure Data Factory](introduction.md)
