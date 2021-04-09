---
title: 'Kurz: výuka modelu pomocí automatizovaného strojového učení'
description: Kurz o výuce modelu Machine Learning bez kódu ve službě Azure synapse Analytics.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: aaf0aab2ef600b269b9b47182aeb096ca13c7a87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943525"
---
# <a name="tutorial-train-a-machine-learning-model-without-code"></a>Kurz: výuka modelu strojového učení bez kódu

Data můžete v tabulkách Spark rozšířit o nové modely strojového učení, které pomocí [automatizovaného strojového učení](../../machine-learning/concept-automated-ml.md)naučíte. V Azure synapse Analytics můžete vybrat tabulku Spark v pracovním prostoru, která se použije jako školicí sada pro sestavování modelů strojového učení, a můžete to udělat v prostředí bez kódu.

V tomto kurzu se naučíte naučit modely strojového učení pomocí prostředí bez kódu v synapse studiu. Synapse Studio je funkce služby Azure synapse Analytics. 

Místo ručního kódování prostředí budete používat automatické strojové učení v Azure Machine Learning. Typ modelu, který se naučíte, závisí na problému, který se snažíte vyřešit.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet před tím, než začnete](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

- [Pracovní prostor analýzy Azure synapse](../get-started-create-workspace.md). Ujistěte se, že má účet úložiště Azure Data Lake Storage Gen2 nakonfigurovaný jako výchozí úložiště. U Data Lake Storage Gen2 systému souborů, se kterými pracujete, se ujistěte, že jste *přispěvatelem dat objektů BLOB úložiště*.
- Fond Apache Spark ve vašem pracovním prostoru Azure synapse Analytics. Podrobnosti najdete v tématu [rychlý Start: vytvoření vyhrazeného fondu SQL pomocí synapse studia](../quickstart-create-sql-pool-studio.md).
- Propojená služba Azure Machine Learning v pracovním prostoru Azure synapse Analytics. Podrobnosti najdete v tématu [rychlý Start: vytvoření nové propojené služby Azure Machine Learning v Azure synapse Analytics](quickstart-integrate-azure-machine-learning.md).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se na [Azure Portal](https://portal.azure.com/).

## <a name="create-a-spark-table-for-the-training-dataset"></a>Vytvoření tabulky Spark pro datovou sadu školení

Pro tento kurz potřebujete tabulku Spark. Následující Poznámkový blok ho vytvoří:

1. Stáhněte si Poznámkový blok [Create-Spark-Table-NYCTaxi-data. ipynb](https://go.microsoft.com/fwlink/?linkid=2149229).

1. Naimportujte Poznámkový blok do synapse studia.
![Snímek obrazovky Azure synapse Analytics s zvýrazněnou možností importu](media/tutorial-automl-wizard/tutorial-automl-wizard-00a.png)

1. Vyberte fond Spark, který chcete použít, a pak vyberte **Spustit vše**. Tento krok získá data o New Yorku taxislužby z otevřené datové sady a uloží je do vaší výchozí databáze Spark.
![Snímek obrazovky Azure synapse Analytics se zvýrazněnou možností spustit vše a Spark Database.](media/tutorial-automl-wizard/tutorial-automl-wizard-00b.png)

1. Po dokončení poznámkového bloku se v rámci výchozí databáze Spark zobrazí nová tabulka Spark. Z **dat** vyhledejte tabulku s názvem **nyc_taxi**.
![Snímek karty Azure synapse Analytics data s zvýrazněnou novou tabulkou](media/tutorial-automl-wizard/tutorial-automl-wizard-00c.png)

## <a name="open-the-automated-machine-learning-wizard"></a>Otevření Průvodce automatickým strojovým učením

Otevření Průvodce:

1. Klikněte pravým tlačítkem na tabulku Spark, kterou jste vytvořili v předchozím kroku. Pak vyberte **Machine Learning**  >  **obohacení s novým modelem**.
![Snímek obrazovky tabulky Spark se zvýrazněnou možností Machine Learning a obohacení novým modelem](media/tutorial-automl-wizard/tutorial-automl-wizard-00d.png)

1. Zadejte podrobnosti o konfiguraci pro vytvoření automatizovaného experimentu Machine Learningu v Azure Machine Learning. Tato rutina spustí více modelů. Nejlepší model z úspěšného spuštění je zaregistrován v registru Azure Machine Learningho modelu.

   ![Snímek obrazovky specifikace konfigurace pro školení modelu Machine Learning.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00a.png)

    - **Azure Machine Learning pracovní prostor**: pro vytvoření automatizovaného spuštění experimentu ve strojovém učení se vyžaduje Azure Machine Learning pracovní prostor. Pracovní prostor analýzy Azure synapse je také potřeba propojit s pracovním prostorem Azure Machine Learning pomocí [propojené služby](quickstart-integrate-azure-machine-learning.md). Po splnění všech požadovaných součástí můžete zadat Azure Machine Learning pracovní prostor, který chcete použít pro toto automatizované spuštění.

    - **Název experimentu**: zadejte název experimentu. Když odesíláte automatizované spuštění strojového učení, zadáte název experimentu. Informace pro běh jsou uloženy v rámci tohoto experimentu v pracovním prostoru Azure Machine Learning. Toto prostředí ve výchozím nastavení vytvoří nový experiment a vygeneruje navrhovaný název, ale můžete také zadat název existujícího experimentu.

    - **Nejlepší název modelu**: zadejte název nejlepšího modelu z automatizovaného spuštění. Nejlepším modelem je tento název a uložený v registru Azure Machine Learning modelu automaticky po tomto spuštění. Automatizované spuštění strojového učení vytváří mnoho modelů strojového učení. V závislosti na primární metrikě, kterou jste vybrali v pozdějším kroku, se tyto modely dají porovnat a můžete vybrat nejlepší model.

    - **Cílový sloupec**: Jedná se o to, aby byl model vyškolený pro předpověď. Vyberte sloupec, který chcete předpovědět. (V tomto kurzu vybereme číselný sloupec `fareAmount` jako cílový sloupec.)

    - **Fond Spark**: Určete fond Spark, který chcete použít pro automatizovaný experiment. Výpočty se spouštějí ve fondu, který zadáte.

    - **Podrobnosti konfigurace Spark**: Kromě fondu Spark máte možnost zadat podrobnosti o konfiguraci relace.

1. Vyberte **Pokračovat**.

## <a name="choose-a-task-type"></a>Zvolit typ úlohy

Vyberte typ modelu strojového učení pro experiment na základě otázky, na kterou se snažíte odpovědět. Vzhledem k tomu, že `fareAmount` je cílový sloupec a jedná se o číselnou hodnotu, doporučujeme zde vybrat **regresi** . Potom vyberte **Pokračovat**.

![Snímek obrazovky obohacený novým modelem se zvýrazněnou regresí](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00b.png)

## <a name="additional-configurations"></a>Další konfigurace

Pokud jste v předchozí části vybrali možnost **regrese** nebo **klasifikace** jako typ modelu, jsou k dispozici následující konfigurace:

- **Primární metrika**: zadejte metriku, která měří, jak dobře model pracuje. Tuto metriku použijete k porovnání různých modelů vytvořených v rámci automatizovaného spuštění a určení toho, který model nejlépe využíval.

- **Čas úlohy školení (hodiny)**: Určete maximální dobu v hodinách, po kterou se má experiment spouštět a naučit modely. Všimněte si, že můžete také zadat hodnoty menší než 1 (například **0,5**).

- **Maximální počet souběžných iterací**: Vyberte maximální počet iterací, které běží paralelně.

- **Kompatibilita modelů ONNX**: Pokud povolíte tuto možnost, budou se modely školicí v rámci automatizovaného strojového učení převádět do formátu ONNX. To je důležité zejména v případě, že chcete model použít pro vyhodnocování v rámci fondů SQL Azure synapse Analytics.

Tato nastavení mají všechny výchozí hodnoty, které můžete přizpůsobit.
![Snímek obrazovky s dalšími konfiguracemi pro konfiguraci regresního modelu.](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00c.png)

Po dokončení všech požadovaných konfigurací můžete spustit automatizovaný běh. Můžete zvolit **vytvořit běh**, který spustí přímo, bez kódu. Případně, pokud dáváte přednost kódu, můžete vybrat **otevřít v poznámkovém bloku**. Tato možnost umožňuje zobrazit kód, který vytvoří běh, a pak spustit Poznámkový blok.

>[!NOTE]
>Pokud jste v předchozí části vybrali **prognózu časových řad** jako typ modelu, je nutné provést další konfigurace. Prognózy také nepodporují kompatibilitu modelu ONNX.

### <a name="create-a-run-directly"></a>Vytvořit přímo spustit

Pokud chcete spustit automatický běh machine learningu přímo, vyberte **Spustit spustit**. Zobrazí se oznámení, které indikuje, že spuštění probíhá. Pak se zobrazí další oznámení, které indikuje úspěch. Můžete také zjistit stav v Azure Machine Learning tím, že vyberete odkaz v oznámení.
![Snímek obrazovky s úspěšným oznámením](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00d.png)

### <a name="create-a-run-with-a-notebook"></a>Vytvoření spuštění pomocí poznámkového bloku

Pokud chcete vygenerovat Poznámkový blok, vyberte **otevřít v poznámkovém bloku**. Pak vyberte **Spustit vše**. Získáte tak možnost Přidat nastavení do automatizovaného spuštění strojového učení.

![Snímek obrazovky s poznámkovým blokem se zvýrazněnou možností spustit vše](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00e.png)

Po úspěšném odeslání se zobrazí odkaz na spuštění experimentu v pracovním prostoru Azure Machine Learning ve výstupu poznámkového bloku. Vyberte odkaz pro monitorování automatizovaného běhu v Azure Machine Learning.
![Snímek obrazovky Azure synapse Analytics s zvýrazněným odkazem. ](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00f.png) )

## <a name="next-steps"></a>Další kroky

- [Kurz: Průvodce bodování modelu Machine Learning (Preview) pro vyhrazené fondy SQL](tutorial-sql-pool-model-scoring-wizard.md)
- [Rychlý Start: vytvoření nové propojené služby Azure Machine Learning v Azure synapse Analytics](quickstart-integrate-azure-machine-learning.md)
- [Možnosti strojového učení ve službě Azure synapse Analytics](what-is-machine-learning.md)