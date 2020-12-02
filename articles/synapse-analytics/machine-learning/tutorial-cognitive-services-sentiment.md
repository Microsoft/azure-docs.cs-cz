---
title: 'Kurz: analýza mínění pomocí Cognitive Services'
description: Kurz, jak využít Cognitive Services pro analýzu mínění v synapse
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 1b407cbee5218149f794ab125ac058e32b422558
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96468366"
---
# <a name="tutorial-sentiment-analysis-with-cognitive-services-preview"></a>Kurz: analýza mínění pomocí Cognitive Services (Preview)

V tomto kurzu se naučíte, jak snadno rozšířit data v Azure synapse pomocí [Cognitive Services](https://go.microsoft.com/fwlink/?linkid=2147492). K provedení analýzy mínění použijeme funkce [Analýza textu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) . Uživatel ve službě Azure synapse může jednoduše vybrat tabulku obsahující textový sloupec, který se má rozšířit pomocí zabarvení. Tyto zabarvení mohou být kladné, záporné, smíšené nebo neutrální a bude vrácena i pravděpodobnost.

Tento kurz zahrnuje:

> [!div class="checklist"]
> - Postup získání datové sady Sparku obsahující textový sloupec pro analýzu mínění
> - Využijte možnosti průvodce v Azure Synapse k rozšíření dat pomocí Analýza textu Cognitive Services.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet před tím, než začnete](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

- [Pracovní prostor Azure synapse Analytics](../get-started-create-workspace.md) s účtem úložiště adls Gen2 nakonfigurovaný jako výchozí úložiště. Musíte být **přispěvatelem dat objektů BLOB úložiště** adls Gen2 systému souborů, se kterými pracujete.
- V pracovním prostoru Azure synapse Analytics je fond Spark. Podrobnosti najdete v tématu [Vytvoření fondu Spark ve službě Azure synapse](../quickstart-create-sql-pool-studio.md).
- Než budete moct použít tento kurz, musíte taky dokončit kroky před konfigurací popsanou v tomto kurzu. [Nakonfigurujte Cognitive Services v Azure synapse](tutorial-configure-cognitive-services-synapse.md).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k portálu [Azure Portal](https://portal.azure.com/).

## <a name="create-a-spark-table"></a>Vytvoření tabulky Spark

Pro tento kurz budete potřebovat tabulku Spark.

1. Stáhněte si následující soubor CSV obsahující datovou sadu pro analýzu textu: [FabrikamComments.csv](https://github.com/Kaiqb/KaiqbRepo0731190208/blob/master/CognitiveServices/TextAnalytics/FabrikamComments.csv)

1. Nahrajte soubor do svého účtu úložiště Azure synapse ADLSGen2.
![Nahrání dat](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00a.png)

1. Vytvořte tabulku Spark ze souboru. csv tak, že kliknete pravým tlačítkem na soubor a vyberete **Nový Poznámkový blok – > vytvořit tabulku Spark**.
![Vytvořit tabulku Spark](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00b.png)

1. Pojmenujte tabulku v buňce kódu a spusťte Poznámkový blok ve fondu Spark. Nezapomeňte nastavit "Header = true".
![Spustit Poznámkový blok](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00c.png)

```python
%%pyspark
df = spark.read.load('abfss://default@azuresynapsesa.dfs.core.windows.net/data/FabrikamComments.csv', format='csv'
## If header exists uncomment line below
, header=True
)
df.write.mode("overwrite").saveAsTable("default.YourTableName")
```

## <a name="launch-cognitive-services-wizard"></a>Spustit Průvodce Cognitive Services

1. Klikněte pravým tlačítkem na tabulku Spark vytvořenou v předchozím kroku. Chcete-li spustit průvodce, vyberte možnost "Machine Learning-> rozšířit s existujícím modelem".
![Spustit Průvodce bodování](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00d.png)

2. Zobrazí se panel konfigurace a zobrazí se výzva k výběru Cognitive Servicesho modelu. Vyberte analýza textu-Analýza mínění.

![Spustit Průvodce vyhodnocování – krok 1](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00e.png)

## <a name="provide-authentication-details"></a>Zadejte podrobnosti ověřování.

Aby bylo možné provést ověření pro Cognitive Services, je nutné odkazovat na tajný kód pro použití v Key Vault. Níže uvedené vstupy jsou závislé na [nezbytných krocích](tutorial-configure-cognitive-services-synapse.md) , které byste měli před tímto krokem dokončit.

- **Předplatné Azure**: vyberte předplatné Azure, do kterého patří Trezor klíčů.
- **Účet Cognitive Services**: Jedná se o prostředek analýza textu, ke kterému se budete připojovat.
- **Azure Key Vault propojená služba**: jako součást nezbytných kroků jste vytvořili propojenou službu s vaším prostředkem analýza textu. Vyberte prosím tuto položku.
- **Název tajného** kódu: Jedná se o název tajného klíče v trezoru klíčů obsahující klíč, který se má ověřit pro váš Cognitive Services prostředek.

![Zadejte Azure Key Vault podrobnosti.](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00f.png)

## <a name="configure-sentiment-analysis"></a>Konfigurace Analýza mínění

Dále je nutné nakonfigurovat analýzu mínění. Vyberte prosím následující podrobnosti:
- **Jazyk**: Vyberte jazyk textu, na kterém chcete provést analýzu mínění. Vyberte možnost **EN**.
- **Textový sloupec**: Jedná se o textový sloupec ve vaší datové sadě, který chcete analyzovat pro určení mínění. Vyberte **Komentář** k sloupci tabulky.

Až budete hotovi, klikněte na **otevřít poznámkový blok**. Tím se vygeneruje Poznámkový blok s PySpark kódem, který provede analýzu mínění pomocí Azure Cognitive Services.

![Konfigurace Analýza mínění](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00g.png)

## <a name="open-notebook-and-run"></a>Otevřete Poznámkový blok a spusťte

Poznámkový blok, který jste právě otevřeli, používá [knihovnu mmlspark](https://github.com/Azure/mmlspark) k připojení ke službě rozpoznávání.

Informace o Azure Key Vault, které jste zadali, vám umožní bezpečně odkázat na tajné kódy z tohoto prostředí bez jejich odhalení.

Nyní můžete **spouštět všechny** buňky pro obohacení dat pomocí zabarvení. Zabarvení bude vrácen jako kladný/záporný/neutrální/smíšený a získáte pravděpodobnost pro mínění. Přečtěte si další informace o [Cognitive Services mínění analýze](https://go.microsoft.com/fwlink/?linkid=2147792).

![Spustit Analýza mínění](media/tutorial-cognitive-services/tutorial-cognitive-services-sentiment-00h.png)

## <a name="next-steps"></a>Další kroky
- [Kurz: detekce anomálií s využitím Azure Cognitive Services](tutorial-cognitive-services-sentiment.md)
- [Kurz: bodování modelu Machine Learning ve vyhrazených fondech SQL Azure synapse](tutorial-sql-pool-model-scoring-wizard.md)
- [Funkce Machine Learning v Azure Azure synapse Analytics](what-is-machine-learning.md)