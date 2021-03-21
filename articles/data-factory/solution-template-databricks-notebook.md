---
title: Transformace se službou Azure Databricks
description: Naučte se používat šablonu řešení k transformaci dat pomocí poznámkového bloku datacihly v Azure Data Factory.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/27/2020
ms.openlocfilehash: ee663423071458605f37f07293693dbc91f592bb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100362110"
---
# <a name="transformation-with-azure-databricks"></a>Transformace se službou Azure Databricks

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

V tomto kurzu vytvoříte kompletní kanál obsahující **ověřování**, **kopírování dat** a aktivity **poznámkových bloků** v Azure Data Factory.

- **Ověření** zajišťuje, že vaše zdrojová datová sada je připravená na využití pro příjem dat před aktivací úlohy kopírování a analýzy.

- **Kopírování dat** duplikuje zdrojovou datovou sadu do úložiště jímky, které je připojené jako DBFS do poznámkového bloku Azure Databricks. Tímto způsobem lze datovou sadu přímo spotřebovat pomocí Sparku.

- **Poznámkový blok** spustí Poznámkový blok datacihly, který transformuje datovou sadu. Také přidá datovou sadu do zpracované složky nebo Azure Azure synapse Analytics.

V případě jednoduchosti šablona v tomto kurzu nevytvoří plánovanou aktivační událost. V případě potřeby ho můžete přidat.

![Diagram kanálu](media/solution-template-Databricks-notebook/pipeline-example.png)

## <a name="prerequisites"></a>Předpoklady

- Účet úložiště objektů BLOB v Azure s kontejnerem, který se má `sinkdata` použít jako jímka.

  Poznamenejte si název účtu úložiště, název kontejneru a přístupový klíč. Tyto hodnoty budete potřebovat později v šabloně.

- Pracovní prostor Azure Databricks.

## <a name="import-a-notebook-for-transformation"></a>Import poznámkového bloku pro transformaci

Import **transformačního** poznámkového bloku do pracovního prostoru datacihly:

1. Přihlaste se ke svému pracovnímu prostoru Azure Databricks a pak vyberte **importovat**.
       ![Příkaz nabídky pro import pracovního prostoru ](media/solution-template-Databricks-notebook/import-notebook.png) vaše cesta k pracovnímu prostoru může být jiný než zobrazený, ale zapamatujte si ho později.
1. Vyberte **importovat z: adresa URL**. Do textového pole zadejte `https://adflabstaging1.blob.core.windows.net/share/Transformations.html` .

   ![Výběry pro import poznámkového bloku](media/solution-template-Databricks-notebook/import-from-url.png)

1. Teď pojďme aktualizovat **Poznámkový** blok s informacemi o připojení úložiště.

   V importovaném poznámkovém bloku přejdete na **příkaz 5** , jak je znázorněno v následujícím fragmentu kódu.

   - Nahraďte `<storage name>` a `<access key>` vlastními informacemi o připojení úložiště.
   - Použijte účet úložiště s `sinkdata` kontejnerem.

    ```python
    # Supply storageName and accessKey values  
    storageName = "<storage name>"  
    accessKey = "<access key>"  

    try:  
      dbutils.fs.mount(  
        source = "wasbs://sinkdata\@"+storageName+".blob.core.windows.net/",  
        mount_point = "/mnt/Data Factorydata",  
        extra_configs = {"fs.azure.account.key."+storageName+".blob.core.windows.net": accessKey})  

    except Exception as e:  
      # The error message has a long stack track. This code tries to print just the relevant line indicating what failed.

    import re
    result = re.findall(r"\^\s\*Caused by:\s*\S+:\s\*(.*)\$", e.message, flags=re.MULTILINE)
    if result:
      print result[-1] \# Print only the relevant error message
    else:  
      print e \# Otherwise print the whole stack trace.  
    ```

1. Vygenerujte **přístupový token datacihly** pro Data Factory pro přístup k datacihlům.
   1. V pracovním prostoru datacihly vyberte ikonu profilu uživatele v pravém horním rohu.
   1. Vyberte **nastavení uživatele**.
    ![Příkaz nabídky pro uživatelská nastavení](media/solution-template-Databricks-notebook/user-setting.png)
   1. Na kartě **přístupové tokeny** vyberte **generovat nový token** .
   1. Vyberte **Generovat**.

    ![Tlačítko generovat](media/solution-template-Databricks-notebook/generate-new-token.png)

   *Uložte přístupový token* pro pozdější použití při vytváření propojené služby datacihly. Přístupový token vypadá nějak takto `dapi32db32cbb4w6eee18b7d87e45exxxxxx` .

## <a name="how-to-use-this-template"></a>Jak používat tuto šablonu

1. Přejít na **transformaci pomocí šablony Azure Databricks** a vytvořit nové propojené služby pro následující připojení.

   ![Nastavení připojení](media/solution-template-Databricks-notebook/connections-preview.png)

    - Připojení ke zdrojovému **objektu BLOB** – pro přístup ke zdrojovým datům.

       Pro toto cvičení můžete použít veřejné úložiště objektů blob, které obsahuje zdrojové soubory. Odkaz na následující snímek obrazovky pro konfiguraci. Použijte následující **adresu URL SAS** pro připojení ke zdrojovému úložišti (přístup jen pro čtení):

       `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        ![Výběry pro metodu ověřování a adresu URL SAS](media/solution-template-Databricks-notebook/source-blob-connection.png)

    - **Připojení k cílovému objektu BLOB** – pro uložení kopírovaných dat

       V okně **Nová propojená služba** vyberte objekt BLOB úložiště jímky.

       ![Objekt BLOB úložiště jímky jako nová propojená služba](media/solution-template-Databricks-notebook/destination-blob-connection.png)

    - **Azure Databricks** – připojení ke clusteru datacihly.

        Vytvořte propojenou službu datacihly pomocí přístupového klíče, který jste předtím vygenerovali. Můžete zvolit *interaktivní cluster* , pokud ho máte. V tomto příkladu se používá možnost **nový cluster úloh** .

        ![Výběry pro připojení ke clusteru](media/solution-template-Databricks-notebook/databricks-connection.png)

1. Vyberte **Použít tuto šablonu**. Zobrazí se vytvořený kanál.

    ![Vytvoření kanálu](media/solution-template-Databricks-notebook/new-pipeline.png)

## <a name="pipeline-introduction-and-configuration"></a>Úvod a konfigurace kanálu

V novém kanálu je většina nastavení nakonfigurovaná automaticky s výchozími hodnotami. Zkontrolujte konfigurace kanálu a proveďte potřebné změny.

1. V **příznaku dostupnosti** aktivity **ověření** ověřte, zda je hodnota zdrojové **datové sady** nastavena na hodnotu `SourceAvailabilityDataset` , kterou jste vytvořili dříve.

   ![Hodnota zdrojové datové sady](media/solution-template-Databricks-notebook/validation-settings.png)

1. V souboru s aktivitou **kopírování dat** **do objektu BLOB** se podívejte na karty **zdroje** a **jímky** . V případě potřeby změňte nastavení.

   - **Karta zdroje zdrojového kódu** ![](media/solution-template-Databricks-notebook/copy-source-settings.png)

   - Karta jímky na kartě **jímky** ![](media/solution-template-Databricks-notebook/copy-sink-settings.png)

1. V **transformaci** aktivity **poznámkového bloku** zkontrolujte a podle potřeby aktualizujte cesty a nastavení.

   **Propojená služba datacihly** by měla být předem vyplněná hodnotou z předchozího kroku, jak je znázorněno v následujícím příkladu: ![ naplněná hodnota pro propojenou službu datacihly](media/solution-template-Databricks-notebook/notebook-activity.png)

   Postup kontroly nastavení **poznámkového bloku** :
  
    1. Vyberte kartu **Nastavení** . V poli **cesta k poznámkovému bloku** ověřte, zda je výchozí cesta správná. Možná budete muset vyhledat a zvolit správnou cestu k poznámkovému bloku.

       ![Cesta k poznámkovému bloku](media/solution-template-Databricks-notebook/notebook-settings.png)

    1. Rozbalte selektor **základních parametrů** a ověřte, že parametry se shodují s tím, co je znázorněno na následujícím snímku obrazovky. Tyto parametry jsou předány do poznámkového bloku datacihly z Data Factory.

       ![Základní parametry](media/solution-template-Databricks-notebook/base-parameters.png)

1. Ověřte, že **parametry kanálu** odpovídají čemu uvedené na následujícím snímku obrazovky: ![ parametry kanálu](media/solution-template-Databricks-notebook/pipeline-parameters.png)

1. Připojte se k vašim datovým sadám.

    >[!NOTE]
    >V níže uvedených datových sadách se cesta k souboru automaticky zadala v šabloně. Pokud potřebujete nějaké změny, nezapomeňte zadat cestu pro **kontejner** i **adresář** v případě jakékoli chyby připojení.

   - **SourceAvailabilityDataset** – Chcete-li ověřit, zda jsou zdrojová data k dispozici.

     ![Výběry propojené služby a cesty k souboru pro SourceAvailabilityDataset](media/solution-template-Databricks-notebook/source-availability-dataset.png)

   - **SourceFilesDataset** – pro přístup ke zdrojovým datům.

       ![Výběry propojené služby a cesty k souboru pro SourceFilesDataset](media/solution-template-Databricks-notebook/source-file-dataset.png)

   - **DestinationFilesDataset** – kopírování dat do cílového umístění jímky. Použijte následující hodnoty:

     - **Propojená služba**  -  `sinkBlob_LS` vytvořená v předchozím kroku.

     - **Cesta k souboru**  -  `sinkdata/staged_sink` .

       ![Výběry propojené služby a cesty k souboru pro DestinationFilesDataset](media/solution-template-Databricks-notebook/destination-dataset.png)

1. Vyberte **ladit** a spusťte kanál. Podrobnější protokoly Spark najdete v protokolech s odkazy na datacihly.

    ![Odkaz na protokoly datacihly z výstupu](media/solution-template-Databricks-notebook/pipeline-run-output.png)

    Datový soubor můžete také ověřit pomocí Průzkumník služby Azure Storage.

    > [!NOTE]
    > V případě korelace s Data Factory spuštění kanálu připojí tento příklad ID spuštění kanálu z objektu pro vytváření dat do výstupní složky. To pomáhá sledovat soubory generované jednotlivými spuštěními.
    > ![ID spuštění kanálu připojení](media/solution-template-Databricks-notebook/verify-data-files.png)

## <a name="next-steps"></a>Další kroky

- [Úvod do služby Azure Data Factory](introduction.md)
