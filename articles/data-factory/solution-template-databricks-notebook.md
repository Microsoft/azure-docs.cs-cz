---
title: Transformace dat pomocí Databricks v Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak transformovat data pomocí poznámkového bloku Databricks v Azure Data Factory pomocí šablony řešení.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/10/2018
ms.author: abnarain
ms.reviewer: douglasl
ms.openlocfilehash: 3d7fd9b3e7ab8e5f193fd3b34a804b9ed0780d66
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56105684"
---
# <a name="transform-data-by-using-databricks-in-azure-data-factory"></a>Transformace dat pomocí Databricks v Azure Data Factory

V tomto kurzu vytvoříte začátku do konce profilace obsahující **vyhledávání**, **kopírování**, a **poznámkového bloku Databricks** aktivity ve službě Data Factory.

-   **Vyhledávání** nebo aktivita GetMetadata slouží k zajištění zdrojová datová sada je připravená pro příjem dat využití, než aktivujete úlohu kopírování a analýzy.

-   **Aktivita kopírování** zkopíruje zdrojový soubor / datovou sadu, která úložiště jímky. Úložiště jímky připojený jako DBFS do poznámkového bloku Databricks tak, aby datové sady mohou být přímo využívány službou Spark.

-   **Aktivity poznámkového bloku Databricks** aktivuje poznámkového bloku Databricks, který transformuje datovou sadu a přidá jej do složky zpracovaných / SQL data Warehouse.

Pro zjednodušení Tato šablona nevytvoří šablony naplánovanou aktivační událost. Můžete přidat, který v případě potřeby.

![1](media/solution-template-Databricks-notebook/Databricks-tutorial-image01.png)

## <a name="prerequisites"></a>Požadavky

1.  Vytvoření **účtu úložiště objektů blob** kontejner s názvem `sinkdata` má být použit jako **jímky**. Poznamenejte **název účtu úložiště**, **název kontejneru**, a **přístupový klíč**, protože se na ně odkazuje později v šabloně.

2.  Zkontrolujte, že máte **pracovního prostoru Azure Databricks** nebo vytvořte novou.

1.  **Import poznámkového bloku pro ETL**. Import níže transformace Poznámkový blok jupyter pracovního prostoru Databricks. (Nemusí být ve stejném umístění, jak je uvedeno níže, ale mějte na paměti cestu, která jste vybrali pro později.) Import poznámkového bloku z následující adresy URL tak, že zadáte tuto adresu URL do pole Adresa URL: `https://Data Factorylabstaging1.blob.core.windows.net/share/Transformations.html`. Vyberte **Import**.

    ![2](media/solution-template-Databricks-notebook/Databricks-tutorial-image02.png)

    ![3](media/solution-template-Databricks-notebook/Databricks-tutorial-image03.png)  

1.  Teď můžeme aktualizovat **transformace** Poznámkový blok s vaší **informace o připojení úložiště** (název a přístupový klíč). Přejděte na **příkaz 5** v importované Poznámkový blok výše, nahraďte ho hodnotou následující fragment kódu po nahrazení zvýrazněné hodnoty. Ujistěte se o stejný účet úložiště vytvořili tento účet a obsahuje `sinkdata` kontejneru.

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

1.  Generování **Databricks přístupový token** pro službu Data Factory pro přístup k Databricks. **Uložit přístupový token** pro pozdější použití při vytváření Databricks propojenou službu, která vypadá podobně jako "dapi32db32cbb4w6eee18b7d87e45exxxxxx.

    ![4](media/solution-template-Databricks-notebook/Databricks-tutorial-image04.png)

    ![5](media/solution-template-Databricks-notebook/Databricks-tutorial-image05.png)

## <a name="create-linked-services-and-datasets"></a>Vytvoření propojené služby a datové sady

1.  Vytvořit nový **propojené služby** v uživatelském rozhraní Data Factory na webu *připojení propojených služeb + nová*

    1.  **Zdroj** – pro přístup k datům. Můžete použít veřejné službě blob storage, který obsahuje zdrojové soubory pro tuto ukázku.

        Vyberte **úložiště objektů Blob**, použijte níže **identifikátor URI SAS** pro připojení k úložišti zdroje (přístup jen pro čtení).

        `https://storagewithdata.blob.core.windows.net/?sv=2017-11-09&ss=b&srt=sco&sp=rl&se=2019-12-31T21:40:53Z&st=2018-10-24T13:40:53Z&spr=https&sig=K8nRio7c4xMLnUV0wWVAmqr5H4P3JDwBaG9HCevI7kU%3D`

        ![6](media/solution-template-Databricks-notebook/Databricks-tutorial-image06.png)

    1.  **Jímka** – pro kopírování dat do aplikace.

        Vyberte úložiště vytvořený v předpokladu 1, v jímky propojené služby.

        ![7](media/solution-template-Databricks-notebook/Databricks-tutorial-image07.png)

    1.  **Databricks** – pro připojení ke clusteru Databricks

        Vytvoření propojené služby Databricks pomocí přístupového klíče vygenerované v 2.c požadovaných součástí. Pokud máte *interaktivní clusteru*, můžete vybrat, který. (V tomto příkladu *nového clusteru úloh* možnost.)

        ![8](media/solution-template-Databricks-notebook/Databricks-tutorial-image08.png)

2.  Vytvoření **datové sady**

    1.  Vytvoření **"sourceAvailability_Dataset"** ke kontrole, pokud se zdrojová data nejsou k dispozici

    ![9](media/solution-template-Databricks-notebook/Databricks-tutorial-image09.png)

    1.  **Zdrojová datová sada –** kopírovat zdroje dat (pomocí binární kopie)

    ![10](media/solution-template-Databricks-notebook/Databricks-tutorial-image10.png)

    1.  **Datová sada jímky** – pro kopírování do dřezu a cílové umístění

        1.  Propojená služba – vyberte vytvořený v 1.b sinkBlob_LS

        2.  Cesta k souboru - ' sinkdata/staged_sink.

        ![11](media/solution-template-Databricks-notebook/Databricks-tutorial-image11.png)

## <a name="create-activities"></a>Vytvoření aktivity

1.  Vytvořit aktivitu vyhledávání "**dostupnost příznak**" provádět kontrola dostupnosti zdroj (můžete použít vyhledávání nebo GetMetadata). Vyberte vytvořený v 2.a sourceAvailability_Dataset.

    ![12](media/solution-template-Databricks-notebook/Databricks-tutorial-image12.png)

1.  Vytvořte aktivitu kopírování "**soubor blob**" Kopírovat datovou sadu ze zdroje do jímky. Data v tomto případě je binární soubor. Odkaz následující snímky obrazovky pro konfigurace zdroje a jímky v aktivitě kopírování.

    ![13](media/solution-template-Databricks-notebook/Databricks-tutorial-image13.png)

    ![14](media/solution-template-Databricks-notebook/Databricks-tutorial-image14.png)

1.  Definování **parametry kanálu**

    ![15](media/solution-template-Databricks-notebook/Databricks-tutorial-image15.png)

1.  Vytvoření **aktivity Databricks**

    Vyberte propojenou službu vytvořili v předchozím kroku.

    ![16](media/solution-template-Databricks-notebook/Databricks-tutorial-image16.png)

    Konfigurace **nastavení**. Vytvoření **základní parametry** jak je znázorněno na snímku obrazovky a vytvořit parametry předávané do poznámkového bloku Databricks ze služby Data Factory. Procházet a **vyberte** **cestu k poznámkovému bloku správné** nahrané v **požadavků požadavek 2**.

    ![17](media/solution-template-Databricks-notebook/Databricks-tutorial-image17.png)

1.  **Spuštění kanálu**. Odkaz na protokoly Databricks najdete podrobnější protokoly Spark.

    ![18](media/solution-template-Databricks-notebook/Databricks-tutorial-image18.png)

    Můžete si taky ověřit datový soubor pomocí Průzkumníka služby storage. (Pro korelaci s spuštění kanálu služby Data Factory, v tomto příkladu přidá ID spuštění ze služby data factory do výstupní složky kanálu. Tímto způsobem můžete sledovat, vrátit soubory vygenerované prostřednictvím jednotlivých spuštění.)

![19](media/solution-template-Databricks-notebook/Databricks-tutorial-image19.png)

## <a name="next-steps"></a>Další postup

- [Úvod do Azure Data Factory](introduction.md)
