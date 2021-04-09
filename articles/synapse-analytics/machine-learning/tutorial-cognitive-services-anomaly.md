---
title: 'Kurz: detekce anomálií pomocí Cognitive Services'
description: Naučte se používat Cognitive Services pro detekci anomálií v Azure synapse Analytics.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: c54300bf37f6f4526c525b1502d902e5f4336ed7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943509"
---
# <a name="tutorial-anomaly-detection-with-cognitive-services-preview"></a>Kurz: detekce anomálií pomocí Cognitive Services (Preview)

V tomto kurzu se naučíte, jak snadno rozšířit data ve službě Azure synapse Analytics pomocí [azure Cognitive Services](../../cognitive-services/index.yml). Ke zjištění anomálií se používá [detektor anomálií](../../cognitive-services/anomaly-detector/index.yml) . Uživatel ve službě Azure synapse může jednoduše vybrat tabulku, která bude obohacena na detekci anomálií.

Tento kurz zahrnuje:

> [!div class="checklist"]
> - Postup získání datové sady pro tabulku Spark, která obsahuje data časových řad.
> - Využijte zkušenosti s průvodcem v Azure Synapse k obohacení dat pomocí detektoru anomálií v Cognitive Services.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet před tím, než začnete](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

- [Pracovní prostor Azure synapse Analytics](../get-started-create-workspace.md) s účtem úložiště Azure Data Lake Storage Gen2 nakonfigurovaný jako výchozí úložiště. Musíte být *přispěvatelem dat objektů BLOB úložiště* Data Lake Storage Gen2 systému souborů, se kterým pracujete.
- V pracovním prostoru Azure synapse Analytics je fond Spark. Podrobnosti najdete v tématu [Vytvoření fondu Spark ve službě Azure synapse](../quickstart-create-sql-pool-studio.md).
- Dokončení kroků před konfigurací v kurzu [konfigurace Cognitive Services v Azure synapse](tutorial-configure-cognitive-services-synapse.md) .

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se na [Azure Portal](https://portal.azure.com/).

## <a name="create-a-spark-table"></a>Vytvoření tabulky Spark

Pro tento kurz potřebujete tabulku Spark.

1. Stáhněte si následující soubor poznámkového bloku, který obsahuje kód pro vygenerování tabulky Spark: [prepare_anomaly_detector_data. ipynb](https://go.microsoft.com/fwlink/?linkid=2149577).

1. Nahrajte soubor do svého pracovního prostoru Azure synapse.

   ![Snímek obrazovky zobrazující výběry pro nahrání poznámkového bloku](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00a.png)

1. Otevřete soubor poznámkového bloku a výběrem možnost **Spustit vše** spusťte všechny buňky.

   ![Snímek obrazovky zobrazující výběry pro vytvoření tabulky Spark](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00b.png)

1. V výchozí databázi Spark by se teď měla zobrazit tabulka Spark s názvem **anomaly_detector_testing_data** .

## <a name="open-the-cognitive-services-wizard"></a>Otevření Průvodce Cognitive Services

1. Klikněte pravým tlačítkem na tabulku Spark vytvořenou v předchozím kroku. Pro otevření průvodce vyberte **Machine Learning**  >  **obohacení s existujícím modelem** .

   ![Snímek obrazovky zobrazující výběry pro otevření Průvodce bodování](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00g.png)

2. Zobrazí se panel konfigurace a zobrazí se výzva k výběru Cognitive Servicesho modelu. Vyberte **detektor anomálií**.

   ![Snímek obrazovky znázorňující výběr detektoru anomálií jako modelu.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00c.png)

## <a name="provide-authentication-details"></a>Zadejte podrobnosti ověřování.

Pokud chcete ověřit, že se Cognitive Services, musíte na svůj Trezor klíčů odkazovat na tajný kód. Následující vstupy závisí na krocích splnění [požadavků](tutorial-configure-cognitive-services-synapse.md) , které byste měli před tímto bodem dokončit.

- **Předplatné Azure**: vyberte předplatné Azure, do kterého patří Trezor klíčů.
- **Cognitive Services účet**: zadejte prostředek analýza textu, ke kterému se připojíte.
- **Azure Key Vault propojená služba**: jako součást požadovaných kroků jste vytvořili propojenou službu s vaším prostředkem analýza textu. Vyberte ho sem.
- **Název tajného** kódu: zadejte název tajného klíče do trezoru klíčů, který obsahuje klíč, který se má ověřit pro váš Cognitive Services prostředek.

![Snímek obrazovky zobrazující podrobnosti o ověřování pro Trezor klíčů](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00d.png)

## <a name="configure-anomaly-detector"></a>Konfigurace detektoru anomálií

Pro konfiguraci detektoru anomálií zadejte následující podrobnosti:

- **Členitost**: rychlost vzorkování vašich dat. Vyberte **měsíčně**. 

- **Sloupec časového razítka**: sloupec, který představuje čas řady. Vyberte **časové razítko (řetězec)**.

- **Časové řady hodnota sloupce**: sloupec, který představuje hodnotu řady v čase určeném sloupcem časového razítka. Vyberte **hodnotu (Double)**.

- **Sloupec seskupení**: sloupec, který seskupí řadu. To znamená, že všechny řádky, které mají stejnou hodnotu v tomto sloupci, by měly tvořit jednu časovou řadu. Vyberte možnost **Skupina (řetězec)**.

Až skončíte, vyberte **otevřít poznámkový blok**. Tím se vygeneruje Poznámkový blok s PySpark kódem, který používá Azure Cognitive Services k detekci anomálií.

![Snímek obrazovky zobrazující podrobnosti o konfiguraci pro detektor anomálií](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00e.png)

## <a name="run-the-notebook"></a>Spuštění poznámkového bloku

Poznámkový blok, který jste právě otevřeli, používá [knihovnu mmlspark](https://github.com/Azure/mmlspark) pro připojení k Cognitive Services. Azure Key Vault podrobnosti, které jste zadali, vám umožní bezpečně odkázat na tajné klíče z tohoto prostředí bez jejich odhalení.

Nyní můžete spouštět všechny buňky a provádět detekci anomálií. Vyberte **Spustit vše**. [Přečtěte si další informace o detekci anomálií v Cognitive Services](../../cognitive-services/anomaly-detector/index.yml).

![Snímek obrazovky, který ukazuje detekci anomálií.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00f.png)

## <a name="next-steps"></a>Další kroky

- [Kurz: analýza mínění s využitím Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Kurz: bodování modelu Machine Learning ve vyhrazených fondech SQL Azure synapse](tutorial-sql-pool-model-scoring-wizard.md)
- [Možnosti strojového učení ve službě Azure Synapse Analytics](what-is-machine-learning.md)
