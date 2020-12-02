---
title: 'Kurz: školení modelů strojového učení pomocí AutoML'
description: Kurz pro výuku modelu strojového učení ve službě Azure synapse pomocí AutoML
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 11/20/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: 8dd99b60a548e3c392bbe468ddde484081e6eb8b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96468138"
---
# <a name="tutorial-code-free-machine-learning-model-training-in-azure-synapse-with-automl-preview"></a>Kurz: kód bezplatné školení modelů ve službě Machine Learning v Azure synapse s AutoML (Preview)

Naučte se, jak snadno rozšířit data v tabulkách Spark pomocí nových modelů strojového učení, které [v Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml)zadáváte pomocí AutoML.  Uživatel v synapse může jednoduše vybrat tabulku Spark v pracovním prostoru Azure synapse, která se použije jako školicí sada pro sestavování modelů strojového učení v prostředí s bezplatným kódem.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> - Naučte modely strojového učení s využitím bezplatného kódu v Azure synapse studiu, které používá automatizované ML v Azure ML. Typ modelu, který se naučíte, závisí na problému, který se snažíte vyřešit.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet před tím, než začnete](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

- [Pracovní prostor synapse Analytics](../get-started-create-workspace.md) s účtem úložiště adls Gen2 nakonfigurovaný jako výchozí úložiště. Musíte být **přispěvatelem dat objektů BLOB úložiště** adls Gen2 systému souborů, se kterými pracujete.
- V pracovním prostoru Azure synapse Analytics je fond Spark. Podrobnosti najdete v tématu [Vytvoření fondu Spark ve službě Azure synapse](../quickstart-create-sql-pool-studio.md).
- Azure Machine Learning propojená služba v pracovním prostoru Azure synapse Analytics. Podrobnosti najdete v tématu [vytvoření propojené služby Azure Machine Learning v Azure synapse](quickstart-integrate-azure-machine-learning.md).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k portálu [Azure Portal](https://portal.azure.com/).

## <a name="create-a-spark-table-for-training-dataset"></a>Vytvoření tabulky Spark pro datovou sadu školení

Pro tento kurz budete potřebovat tabulku Spark. V následujícím poznámkovém bloku se vytvoří tabulka Spark.

1. Stažení poznámkového bloku [Create-Spark-Table-NYCTaxi-data. ipynb](https://go.microsoft.com/fwlink/?linkid=2149229)

1. Naimportujte Poznámkový blok do Azure synapse studia.
![Importovat Poznámkový blok](media/tutorial-automl-wizard/tutorial-automl-wizard-00a.png)

1. Vyberte fond Spark, který chcete použít, a klikněte na `Run all` . Spustit tento poznámkový blok získá data New York taxislužby z otevřené datové sady a uloží se do výchozí databáze Spark.
![Spustit vše](media/tutorial-automl-wizard/tutorial-automl-wizard-00b.png)

1. Po dokončení poznámkového bloku se v rámci výchozí databáze Spark vytvoří nová tabulka Spark. Přejít do centra dat a vyhledat tabulku s názvem `nyc_taxi` .
![Tabulka Spark](media/tutorial-automl-wizard/tutorial-automl-wizard-00c.png)

## <a name="launch-automl-wizard-to-train-a-model"></a>Spuštění Průvodce AutoML pro výuku modelu

Klikněte pravým tlačítkem na tabulku Spark vytvořenou v předchozím kroku. Výběrem možnosti Machine Learning-> rozšířit s novým modelem otevřete průvodce.
![Spustit Průvodce AutoML](media/tutorial-automl-wizard/tutorial-automl-wizard-00d.png)

Zobrazí se panel konfigurace a budete požádáni o zadání podrobností o konfiguraci pro vytvoření AutoML experimentu v Azure Machine Learning. Toto spuštění bude mít za následek vytvoření více modelů a nejlepší model z úspěšného spuštění bude zaregistrován v registru modelu Azure ML:

![Konfigurace běhu krok 1](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00a.png)

- **Pracovní prostor Azure ml**: pracovní prostor Azure ml je nutný k vytvoření AutoML experimentu. Pracovní prostor Azure synapse je také potřeba propojit s pracovním prostorem Azure ML pomocí [propojené služby](quickstart-integrate-azure-machine-learning.md). Až budete mít všechny předpoklady, můžete zadat pracovní prostor Azure ML, který chcete použít pro tento AutoML běh.

- **Název experimentu**: zadejte název experimentu. Když odešlete AutoML běh, zadáte název experimentu. Informace o běhu jsou uloženy v rámci tohoto experimentu v pracovním prostoru Azure ML. V tomto prostředí se ve výchozím nastavení vytvoří nový experiment a vygeneruje se navrhovaný název, ale můžete taky zadat název existujícího experimentu.

- **Nejlepší model**: zadejte název nejlepšího modelu z AutoML spuštění. Nejlepší model bude mít tento název a uloží se do registru modelu Azure ML automaticky po tomto spuštění. AutoML spuštění vytvoří mnoho modelů strojového učení. V závislosti na primární metrikě, kterou vyberete v pozdějším kroku, se tyto modely dají porovnat a můžete vybrat nejlepší model.

- **Cílový sloupec**: Jedná se o to, aby byl model vyškolený pro předpověď. Vyberte sloupec, který chcete předpovědět.

- **Fond Spark**: fond Spark, který chcete použít pro AutoML experiment. Výpočty se spustí ve vámi určeném fondu.

- **Podrobnosti konfigurace Spark**: Kromě fondu Spark máte také možnost zadat podrobnosti o konfiguraci relace.

V tomto kurzu vybereme číselný sloupec `fareAmount` jako cílový sloupec.

Klikněte na pokračovat.

## <a name="choose-task-type"></a>Zvolit typ úlohy

Vyberte typ modelu strojového učení pro experiment na základě otázky, na kterou se pokoušíte odpovědět. Vzhledem k tomu, že jsme vybrali `fareAmount` jako cílový sloupec a jedná se o číselnou hodnotu, vybereme *regresi*.

Klikněte na pokračovat a proveďte konfiguraci dalších nastavení.

![Výběr typu úlohy](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00b.png)

## <a name="additional-configurations"></a>Další konfigurace

Pokud vyberete možnost *klasifikace* nebo *regresní* typ, další konfigurace:

- **Primární metrika**: metrika používaná k měření, jak dobře model pracuje. Toto je metrika, která se bude používat k porovnání různých modelů vytvořených v AutoML spuštění a určení toho, který model se nejlépe udělal.

- **Čas úlohy školení (hodiny)**: maximální množství času (v hodinách), ve kterém se má experiment spouštět a naučit modely. Všimněte si, že můžete také zadat hodnoty menší než 1. Příklad: `0.5`.

- **Maximální počet souběžných iterací**: představuje maximální počet iterací, které se spustí paralelně.

- **Kompatibilita modelu ONNX**: Pokud je tato možnost povolená, modely vyškolené nástrojem AutoML se převedou na formát ONNX. To je důležité zejména v případě, že chcete model použít pro bodování v synapse fondech SQL Azure.

Tato nastavení mají všechny výchozí hodnoty, které můžete přizpůsobit.
![Další konfigurace](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00c.png)

> Všimněte si, že FF vyberete "Časová prognózování", vyžaduje se více konfigurací. Prognózy také nepodporuje kompatibilitu modelů ONNX.

Po dokončení všech požadovaných konfigurací můžete spustit AutoML spustit.

Existují dva způsoby, jak spustit AutoML běh v Azure Azure synapse. V rámci bezplatného kódu se můžete rozhodnout vytvořit přímo **Spustit** . Pokud dáváte přednost kódu, můžete vybrat **otevřít v poznámkovém bloku**, který vám umožní zobrazit kód, který vytvoří běh a spustí Poznámkový blok.

### <a name="create-run-directly"></a>Vytvořit přímo spustit

Kliknutím na spustit běh spusťte AutoML spustit přímo. Bude se jednat o oznámení, které indikuje spuštění AutoML.

Po úspěšném spuštění AutoML se zobrazí další úspěšné oznámení. Můžete také kliknout na tlačítko oznámení a ověřit stav odeslání do běhu.
Azure ML kliknutím na odkaz v úspěšném oznámení.
![Úspěšné oznámení](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00d.png)

### <a name="create-run-with-notebook"></a>Vytvořit běh pomocí poznámkového bloku

Vyberte *otevřít v poznámkovém bloku* pro vygenerování poznámkového bloku. Kliknutím na *Spustit vše* spusťte Poznámkový blok.
Získáte tak možnost přidat do AutoML spuštění další nastavení.

![Otevřít poznámkový blok](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00e.png)

Po úspěšném odeslání z poznámkového bloku bude odkaz na experiment spuštěný v pracovním prostoru Azure ML ve výstupu poznámkového bloku. Kliknutím na odkaz můžete monitorovat AutoML běh v Azure ML.
![Spustit Poznámkový blok vše ](media/tutorial-automl-wizard/tutorial-automl-wizard-configure-run-00f.png) )

## <a name="next-steps"></a>Další kroky

- [Kurz: bodování modelu Machine Learning ve vyhrazených fondech SQL ve službě Azure synapse](tutorial-sql-pool-model-scoring-wizard.md).
- [Rychlý Start: vytvoření nové propojené služby Azure Machine Learning v Azure synapse](quickstart-integrate-azure-machine-learning.md)
- [Funkce Machine Learning ve službě Azure synapse Analytics](what-is-machine-learning.md)