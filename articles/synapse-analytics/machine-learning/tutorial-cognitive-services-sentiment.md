---
title: 'Kurz: analýza mínění pomocí Cognitive Services'
description: Naučte se používat Cognitive Services k analýze mínění ve službě Azure synapse Analytics.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 08d5e53facce172c2287c2e341895f0ee38571f0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943696"
---
# <a name="tutorial-sentiment-analysis-with-cognitive-services-preview"></a>Kurz: analýza mínění pomocí Cognitive Services (Preview)

V tomto kurzu se naučíte, jak snadno rozšířit data ve službě Azure synapse Analytics pomocí [azure Cognitive Services](../../cognitive-services/index.yml). K provedení analýzy mínění použijete funkce [Analýza textu](../../cognitive-services/text-analytics/index.yml) . 

Uživatel ve službě Azure synapse může jednoduše vybrat tabulku obsahující textový sloupec, který se má rozšířit pomocí zabarvení. Tyto zabarvení můžou být kladné, záporné, smíšené nebo neutrální. Vrátí se také pravděpodobnost.

Tento kurz zahrnuje:

> [!div class="checklist"]
> - Postup získání datové sady pro tabulku Spark obsahující textový sloupec pro analýzu mínění
> - Použití Průvodce v prostředí Azure Synapse k rozšíření dat pomocí Analýza textu v Cognitive Services.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet před tím, než začnete](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

- [Pracovní prostor Azure synapse Analytics](../get-started-create-workspace.md) s účtem úložiště Azure Data Lake Storage Gen2 nakonfigurovaný jako výchozí úložiště. Musíte být *přispěvatelem dat objektů BLOB úložiště* Data Lake Storage Gen2 systému souborů, se kterým pracujete.
- V pracovním prostoru Azure synapse Analytics je fond Spark. Podrobnosti najdete v tématu [Vytvoření fondu Spark ve službě Azure synapse](../quickstart-create-sql-pool-studio.md).
- Kroky před konfigurací popsané v tomto kurzu [konfigurují Cognitive Services v Azure synapse](tutorial-configure-cognitive-services-synapse.md).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se na [Azure Portal](https://portal.azure.com/).

## <a name="create-a-spark-table"></a>Vytvoření tabulky Spark

Pro tento kurz budete potřebovat tabulku Spark.

1. Stáhněte soubor [FabrikamComments.csv](https://github.com/Kaiqb/KaiqbRepo0731190208/blob/master/CognitiveServices/TextAnalytics/FabrikamComments.csv) , který obsahuje datovou sadu pro text Analytics. 

1. Nahrajte soubor do svého účtu úložiště Azure synapse v Data Lake Storage Gen2.
  
   ![Snímek obrazovky zobrazující výběry pro nahrávání dat](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00a.png)

1. Vytvořte tabulku Spark ze souboru. csv tak, že kliknete pravým tlačítkem na soubor a vyberete **Nový Poznámkový blok**  >  **vytvořit tabulku Spark**.

   ![Snímek obrazovky zobrazující výběry pro vytvoření tabulky Spark](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00b.png)

1. Pojmenujte tabulku v buňce kódu a spusťte Poznámkový blok ve fondu Spark. Nezapomeňte nastavit `header=True` .

   ![Snímek obrazovky, který ukazuje spuštění poznámkového bloku](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00c.png)

   ```python
   %%pyspark
   df = spark.read.load('abfss://default@azuresynapsesa.dfs.core.windows.net/data/FabrikamComments.csv', format='csv'
   ## If a header exists, uncomment the line below
   , header=True
   )
   df.write.mode("overwrite").saveAsTable("default.YourTableName")
   ```

## <a name="open-the-cognitive-services-wizard"></a>Otevření Průvodce Cognitive Services

1. Klikněte pravým tlačítkem na tabulku Spark vytvořenou v předchozím postupu. Pro otevření průvodce vyberte **Machine Learning**  >  **obohacení s existujícím modelem** .

   ![Snímek obrazovky zobrazující výběry pro otevření Průvodce bodování](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00d.png)

2. Zobrazí se panel konfigurace a zobrazí se výzva k výběru Cognitive Servicesho modelu. Vyberte **Analýza textu-analýza mínění**.

   ![Snímek obrazovky zobrazující výběr Cognitive Servicesho modelu](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00e.png)

## <a name="provide-authentication-details"></a>Zadejte podrobnosti ověřování.

Aby bylo možné provést ověření pro Cognitive Services, musíte odkazovat na tajný klíč vašeho trezoru klíčů. Následující vstupy závisejí na [požadovaných krocích](tutorial-configure-cognitive-services-synapse.md) , které byste měli před tímto bodem dokončit.

- **Předplatné Azure**: vyberte předplatné Azure, do kterého patří Trezor klíčů.
- **Cognitive Services účet**: zadejte prostředek analýza textu, ke kterému se připojíte.
- **Azure Key Vault propojená služba**: jako součást požadovaných kroků jste vytvořili propojenou službu s vaším prostředkem analýza textu. Vyberte ho sem.
- **Název tajného** kódu: zadejte název tajného klíče do trezoru klíčů, který obsahuje klíč, který se má ověřit pro váš Cognitive Services prostředek.

![Snímek obrazovky zobrazující podrobnosti o ověřování pro Trezor klíčů](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00f.png)

## <a name="configure-sentiment-analysis"></a>Konfigurace analýzy mínění

Dále nakonfigurujte analýzu mínění. Vyberte následující podrobnosti:
- **Jazyk**: vyberte **angličtinu** jako jazyk textu, na kterém chcete provést analýzu mínění.
- **Textový sloupec**: vyberte možnost **Komentář (řetězec)** jako textový sloupec ve vaší datové sadě, který chcete analyzovat pro určení mínění.

Až skončíte, vyberte **otevřít poznámkový blok**. Tím se vygeneruje Poznámkový blok s PySpark kódem, který provede analýzu mínění pomocí Azure Cognitive Services.

![Snímek obrazovky zobrazující výběry pro konfiguraci analýzy mínění](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00g.png)

## <a name="run-the-notebook"></a>Spuštění poznámkového bloku

Poznámkový blok, který jste právě otevřeli, používá [knihovnu mmlspark](https://github.com/Azure/mmlspark) pro připojení k Cognitive Services. Azure Key Vault podrobnosti, které jste zadali, vám umožní bezpečně odkázat na tajné klíče z tohoto prostředí bez jejich odhalení.

Nyní můžete spouštět všechny buňky pro obohacení dat pomocí zabarvení. Vyberte **Spustit vše**. 

Zabarvení jsou vraceny jako **kladné**, **záporné**, **neutrální** nebo **smíšené**. Získáte také pravděpodobnost na mínění. [Přečtěte si další informace o mínění analýze v Cognitive Services](../../cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis.md).

![Snímek obrazovky zobrazující analýzu mínění](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00h.png)

## <a name="next-steps"></a>Další kroky
- [Kurz: detekce anomálií s využitím Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Kurz: bodování modelu Machine Learning ve vyhrazených fondech SQL Azure synapse](tutorial-sql-pool-model-scoring-wizard.md)
- [Možnosti strojového učení ve službě Azure Synapse Analytics](what-is-machine-learning.md)