---
title: Transformace dat pomocí datacihlů
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
ms.date: 12/10/2018
ms.openlocfilehash: d21bab2f358e8ae9460bff2305957ed901c70926
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927750"
---
# <a name="transform-data-by-using-databricks-in-azure-data-factory"></a>Transformace dat pomocí datacihlů v Azure Data Factory

V tomto kurzu vytvoříte kompletní kanál obsahující aktivity **hledání**, **kopírování**a **poznámkového bloku datacihly** v Data Factory.

-   Aktivita **Lookup** nebo GetMetadata se používá k zajištění, že zdrojová datová sada je připravená na využití pro příjem dat, než se aktivuje úloha kopírování a analýzy.

-   **Aktivita kopírování** zkopíruje zdrojový soubor nebo datovou sadu do úložiště jímky. Úložiště jímky je v poznámkovém bloku datacihly připojené jako DBFS, aby bylo možné datovou sadu přímo spotřebovat Sparkem.

-   **Aktivita poznámkového bloku datacihly** spustí Poznámkový blok datacihly, který transformuje datovou sadu a přidá ji do zpracované složky/SQL DW.

Chcete-li tuto šablonu ponechat jednoduchou, šablona nevytvoří plánovanou aktivační událost. V případě potřeby je můžete přidat.

![1\. místo](media/solution-template-Databricks-notebook/Databricks-tutorial-image01.png)

## <a name="prerequisites"></a>Předpoklady

1.  Vytvořte **účet Blob Storage** a kontejner s názvem `sinkdata`, který se použije jako **jímka**. Poznamenejte si **název účtu úložiště**, **název kontejneru**a **přístupový klíč**, protože se na ně odkazuje později v šabloně.

2.  Ujistěte se, že máte **pracovní prostor Azure Databricks** nebo vytvořte nový.

1.  **Importujte Poznámkový blok pro ETL**. Níže uvedený transformační Poznámkový blok importujte do pracovního prostoru datacihly. (Nemusí být ve stejném umístění jako níže, ale zapamatujte si cestu, kterou si zvolíte později.) Naimportujte Poznámkový blok z následující adresy URL zadáním této adresy URL do pole Adresa URL: `https://adflabstaging1.blob.core.windows.net/share/Transformations.html`. Vyberte **Import** (Importovat).

    ![2](media/solution-template-Databricks-notebook/Databricks-tutorial-image02.png)

    ![3](media/solution-template-Databricks-notebook/Databricks-tutorial-image03.png)  

1.  Teď aktualizujeme Poznámkový **blok s** vašimi **informacemi o připojení úložiště** (název a přístupový klíč). V importovaném poznámkovém bloku přejít na **příkaz 5** a po nahrazení zvýrazněných hodnot ho nahraďte následujícím fragmentem kódu. Zajistěte, aby byl tento účet stejný účet úložiště, který jste vytvořili dříve, a obsahuje kontejner `sinkdata`.

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

1.  Vygenerujte **přístupový token datacihly** pro Data Factory pro přístup k datacihlům. **Uložte přístupový token** pro pozdější použití při vytváření propojené služby datacihly, který vypadá nějak takto: "dapi32db32cbb4w6eee18b7d87e45exxxxxx".

    ![4](media/solution-template-Databricks-notebook/Databricks-tutorial-image04.png)

    ![5](media/solution-template-Databricks-notebook/Databricks-tutorial-image05.png)

## <a name="create-linked-services-and-datasets"></a>Vytvoření propojených služeb a datových sad

1.  V uživatelském rozhraní Data Factory vytvořte nové **propojené služby** tak, že přejdete na *připojení propojené služby + nové* .

    1.  **Zdroj** – pro přístup ke zdrojovým datům. Pro tuto ukázku můžete použít veřejné úložiště objektů blob, které obsahuje zdrojové soubory.

        Vyberte **BLOB Storage**pro připojení ke zdrojovému úložišti použijte níže uvedený **identifikátor URI SAS** (přístup jen pro čtení).

        `https://storagewithdata.blob.core.windows.net/?sv=2017-11-09&ss=b&srt=sco&sp=rl&se=2019-12-31T21:40:53Z&st=2018-10-24T13:40:53Z&spr=https&sig=K8nRio7c4xMLnUV0wWVAmqr5H4P3JDwBaG9HCevI7kU%3D`

        ![6](media/solution-template-Databricks-notebook/Databricks-tutorial-image06.png)

    1.  **Jímka** – pro kopírování dat do.

        V propojené službě jímky vyberte úložiště vytvořené v požadavku 1.

        ![7](media/solution-template-Databricks-notebook/Databricks-tutorial-image07.png)

    1.  **Datacihly** – pro připojení ke clusteru datacihly

        Vytvořte propojenou službu datacihly pomocí přístupového klíče vygenerovaného v požadavku 2. c. Máte-li *interaktivní cluster*, můžete jej vybrat. (V tomto příkladu se používá možnost *nový cluster úloh* .)

        ![8](media/solution-template-Databricks-notebook/Databricks-tutorial-image08.png)

2.  Vytvoření **datových sad**

    1.  Vytvořte **sourceAvailability_Dataset** a ověřte, jestli jsou zdrojová data dostupná.

    ![9](media/solution-template-Databricks-notebook/Databricks-tutorial-image09.png)

    1.  **Zdrojová datová sada –** pro kopírování zdrojových dat (pomocí binární kopie)

    ![10](media/solution-template-Databricks-notebook/Databricks-tutorial-image10.png)

    1.  **Datová sada jímky** – pro kopírování do jímky/cílového umístění

        1.  Propojená služba – vyberte ' sinkBlob_LS ' vytvořenou v 1. b

        2.  Cesta k souboru-' sinkdata/staged_sink '

        ![11](media/solution-template-Databricks-notebook/Databricks-tutorial-image11.png)

## <a name="create-activities"></a>Vytváření aktivit

1.  Vytvořit aktivitu vyhledávání –**příznak dostupnosti**pro provedení kontroly dostupnosti zdroje (lze použít vyhledávání nebo GetMetadata). Vyberte možnost ' sourceAvailability_Dataset ' vytvořenou ve 2. a.

    ![12](media/solution-template-Databricks-notebook/Databricks-tutorial-image12.png)

1.  Vytvořte aktivitu kopírování**File-to-BLOB**pro kopírování datové sady ze zdroje do jímky. V tomto případě jsou data binární soubor. Odkaz na následující snímky obrazovky pro konfiguraci zdrojového kódu a jímky v aktivitě kopírování

    ![13](media/solution-template-Databricks-notebook/Databricks-tutorial-image13.png)

    ![14](media/solution-template-Databricks-notebook/Databricks-tutorial-image14.png)

1.  Definovat **parametry kanálu**

    ![15](media/solution-template-Databricks-notebook/Databricks-tutorial-image15.png)

1.  Vytvoření **aktivity datacihly**

    Vyberte propojenou službu vytvořenou v předchozím kroku.

    ![16](media/solution-template-Databricks-notebook/Databricks-tutorial-image16.png)

    Nakonfigurujte **Nastavení**. Vytvořte **základní parametry** , jak je znázorněno na snímku obrazovky a vytvořte parametry, které mají být předány do poznámkového bloku datacihly z Data Factory. Vyhledejte a **Vyberte** **správnou cestu poznámkového bloku** nahranou v **požadovaném umístění 2**.

    ![17](media/solution-template-Databricks-notebook/Databricks-tutorial-image17.png)

1.  **Spusťte kanál**. Podrobnější protokoly Spark najdete v protokolech pro odkazy na datacihly.

    ![18](media/solution-template-Databricks-notebook/Databricks-tutorial-image18.png)

    Datový soubor můžete také ověřit pomocí Průzkumníka služby Storage. (Pro korelaci s Data Factory spuštění kanálu tento příklad připojí ID spuštění kanálu z objektu pro vytváření dat do výstupní složky. Tímto způsobem můžete sledovat zpět soubory generované při každém spuštění.)

![19](media/solution-template-Databricks-notebook/Databricks-tutorial-image19.png)

## <a name="next-steps"></a>Další kroky

- [Seznámení se službou Azure Data Factory](introduction.md)
