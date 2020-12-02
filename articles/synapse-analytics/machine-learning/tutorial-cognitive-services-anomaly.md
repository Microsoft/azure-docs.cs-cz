---
title: 'Kurz: detekce anomálií pomocí Cognitive Services'
description: Kurz, jak využít Cognitive Services pro detekci anomálií v synapse
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 4052d6a0773aa27e0a378ee04975c7946f1ffbfe
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96468132"
---
# <a name="tutorial-anomaly-detection-with-cognitive-services-preview"></a>Kurz: detekce anomálií pomocí Cognitive Services (Preview)

V tomto kurzu se naučíte, jak snadno rozšířit data v Azure synapse pomocí [Cognitive Services](https://go.microsoft.com/fwlink/?linkid=2147492). K provedení detekce anomálií budeme používat [detektor anomálií](https://go.microsoft.com/fwlink/?linkid=2147493) . Uživatel ve službě Azure synapse může jednoduše vybrat tabulku, která bude obohacena na detekci anomálií.

Tento kurz zahrnuje:

> [!div class="checklist"]
> - Postup získání datové sady Sparku obsahující data časových řad
> - Využijte možnosti průvodce ve službě Azure Synapse k rozšíření dat pomocí služby pro rozpoznávání anomálií.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet před tím, než začnete](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

- [Pracovní prostor Azure synapse Analytics](../get-started-create-workspace.md) s účtem úložiště adls Gen2 nakonfigurovaný jako výchozí úložiště. Musíte být **přispěvatelem dat objektů BLOB úložiště** adls Gen2 systému souborů, se kterými pracujete.
- V pracovním prostoru Azure synapse Analytics je fond Spark. Podrobnosti najdete v tématu [Vytvoření fondu Spark ve službě Azure synapse](../quickstart-create-sql-pool-studio.md).
- Než budete moct použít tento kurz, musíte taky dokončit kroky před konfigurací popsanou v tomto kurzu. [Nakonfigurujte Cognitive Services v Azure synapse](tutorial-configure-cognitive-services-synapse.md).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k portálu [Azure Portal](https://portal.azure.com/).

## <a name="create-a-spark-table"></a>Vytvoření tabulky Spark

Pro tento kurz budete potřebovat tabulku Spark.

1. Stáhněte si následující soubor poznámkového bloku, který obsahuje kód pro vygenerování tabulky Spark: [prepare_anomaly_detector_data. ipynb](https://go.microsoft.com/fwlink/?linkid=2149577)

1. Nahrajte soubor do svého pracovního prostoru Azure synapse.
![Nahrát Poznámkový blok](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00a.png)

1. Otevřete soubor poznámkového bloku a vyberte možnost **spouštět všechny** buňky.
![Vytvořit tabulku Spark](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00b.png)

1. V výchozí databázi Spark by se teď měla zobrazit tabulka Spark s názvem **anomaly_detector_testing_data** .

## <a name="launch-cognitive-services-wizard"></a>Spustit Průvodce Cognitive Services

1. Klikněte pravým tlačítkem na tabulku Spark vytvořenou v předchozím kroku. Chcete-li spustit průvodce, vyberte možnost "Machine Learning-> rozšířit s existujícím modelem".
![Spustit Průvodce bodování](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00g.png)

2. Zobrazí se panel konfigurace a zobrazí se výzva k výběru Cognitive Servicesho modelu. Vyberte detektor anomálií.

![Spustit Průvodce vyhodnocování – krok 1](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00c.png)

## <a name="provide-authentication-details"></a>Zadejte podrobnosti ověřování.

Aby bylo možné provést ověření pro Cognitive Services, je nutné odkazovat na tajný kód pro použití v Key Vault. Níže uvedené vstupy jsou závislé na [nezbytných krocích](tutorial-configure-cognitive-services-synapse.md) , které byste měli před tímto krokem dokončit.

- **Předplatné Azure**: vyberte předplatné Azure, do kterého patří Trezor klíčů.
- **Účet Cognitive Services**: Jedná se o prostředek analýza textu, ke kterému se budete připojovat.
- **Azure Key Vault propojená služba**: jako součást nezbytných kroků jste vytvořili propojenou službu s vaším prostředkem analýza textu. Vyberte prosím tuto položku.
- **Název tajného** kódu: Jedná se o název tajného klíče v trezoru klíčů obsahující klíč, který se má ověřit pro váš Cognitive Services prostředek.

![Zadejte Azure Key Vault podrobnosti.](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00d.png)

## <a name="configure-anomaly-detection"></a>Konfigurovat detekci anomálií

Dále je nutné nakonfigurovat detekci anomálií. Zadejte následující podrobnosti:

- Členitost: frekvence vzorkování dat. Pokud má vaše data například hodnotu pro každou minutu, bude vaše členitost minutová. Zvolit **měsíčně** 

- Timestamp: sloupec představující čas řady. Zvolit **časové razítko** sloupce

- Hodnota časové řady: sloupec představující hodnotu řady v čase určeném sloupcem časového razítka. Zvolit **hodnotu** sloupce

- Seskupení: sloupec, který seskupí řadu. To znamená, že všechny řádky, které mají stejnou hodnotu v tomto sloupci, by měly tvořit jednu časovou řadu. Zvolit **skupinu** sloupců

Jakmile budete hotovi, vyberte **otevřít poznámkový blok**. Tím se vygeneruje Poznámkový blok s PySpark kódem, který provádí detekci anomálií pomocí Azure Cognitive Services.

![Konfigurace detektoru anomálií](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00e.png)

## <a name="open-notebook-and-run"></a>Otevřete Poznámkový blok a spusťte

Poznámkový blok, který jste právě otevřeli, používá [knihovnu mmlspark](https://github.com/Azure/mmlspark) k připojení ke službě rozpoznávání.

Informace o Azure Key Vault, které jste zadali, vám umožní bezpečně odkázat na tajné kódy z tohoto prostředí bez jejich odhalení.

Nyní můžete **spouštět všechny** buňky a provádět detekci anomálií. Přečtěte si další informace o [Cognitive Services detektoru anomálií](https://go.microsoft.com/fwlink/?linkid=2147493).

![Spustit detekci anomálií](media/tutorial-cognitive-services/tutorial-cognitive-services-anomaly-00f.png)

## <a name="next-steps"></a>Další kroky

- [Kurz: analýza mínění s využitím Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Kurz: bodování modelu Machine Learning ve vyhrazených fondech SQL Azure synapse](tutorial-sql-pool-model-scoring-wizard.md)
- [Funkce Machine Learning ve službě Azure synapse Analytics](what-is-machine-learning.md)