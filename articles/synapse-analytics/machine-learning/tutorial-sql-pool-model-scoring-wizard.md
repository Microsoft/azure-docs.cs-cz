---
title: 'Kurz: Průvodce bodování modelu Machine Learning pro vyhrazené fondy SQL'
description: Kurz týkající se použití Průvodce bodování modelu Machine Learning k rozšíření dat ve vyhrazených fondech SQL.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 09/25/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: f5c5edc067b3f7b525fd129462c48ca50fdafc8f
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93314040"
---
# <a name="tutorial-machine-learning-model-scoring-wizard-for-dedicated-sql-pools"></a>Kurz: Průvodce bodování modelu Machine Learning pro vyhrazené fondy SQL

Naučte se snadno rozšířit data ve vyhrazených fondech SQL pomocí prediktivních modelů strojového učení.  Modely, které vaše datové specialisty vytvářejí, jsou teď snadno dostupné pro odborníky na data pro prediktivní analýzy. Data Professional v synapse můžete jednoduše vybrat model z registru Azure Machine Learning modelu pro nasazení v synapse fondech SQL a spustit předpovědi pro rozšíření dat.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> - Výuka prediktivního modelu strojového učení a registrace modelu v registru Azure Machine Learningho modelu
> - Použití Průvodce bodování SQL ke spuštění předpovědi ve vyhrazeném fondu SQL

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet před tím, než začnete](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

- [Pracovní prostor synapse Analytics](../get-started-create-workspace.md) s účtem úložiště adls Gen2 nakonfigurovaný jako výchozí úložiště. Musíte být **přispěvatelem dat objektů BLOB úložiště** adls Gen2 systému souborů, se kterými pracujete.
- Vyhrazený fond SQL ve vašem pracovním prostoru synapse Analytics. Podrobnosti najdete v tématu [vytvoření vyhrazeného fondu SQL](../quickstart-create-sql-pool-studio.md).
- V pracovním prostoru synapse Analytics Azure Machine Learning propojenou službu. Podrobnosti najdete v tématu [vytvoření propojené služby Azure Machine Learning v synapse](quickstart-integrate-azure-machine-learning.md).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k portálu [Azure Portal](https://portal.azure.com/).

## <a name="train-a-model-in-azure-machine-learning"></a>Výuka modelu v Azure Machine Learning

Než začnete, ověřte, že je vaše verze **skriptu sklearn** 0.20.3.

Před spuštěním všech buněk v poznámkovém bloku ověřte, zda je instance služby COMPUTE spuštěna.

![Ověřit výpočetní AML](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00b.png)

1. Přejděte do pracovního prostoru Azure Machine Learning.

1. Stáhněte si [NYC s popisem taxislužby. ipynb](https://go.microsoft.com/fwlink/?linkid=2144301).

1. V [Azure Machine Learning Studiu](https://ml.azure.com)spusťte Azure Machine Learning pracovní prostor.

1. Přejděte na **poznámkové bloky** a klikněte na **Odeslat soubory**. Vyberte "předpověď NYC taxislužby tipy. ipynb", které jste stáhli a nahráli.
   ![Nahrání souboru](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00a.png)

1. Po nahrání a otevření poznámkového bloku klikněte na **Spustit všechny buňky**.

   Jedna z buněk může selhat a požádá vás o ověření v Azure. Sledujte to ve výstupech buněk a ověřte ho v prohlížeči pomocí odkazu a zadání kódu. Pak znovu spusťte Poznámkový blok.

1. Poznámkový blok vytvoří model ONNX a zaregistruje ho pomocí MLFlow. Pokud chcete zjistit, jestli je nový model správně zaregistrovaný, otevřete **model** .
   ![Model v registru](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00c.png)

1. Spuštění poznámkového bloku taky exportujte testovací data do souboru CSV. Stáhněte soubor CSV do místního systému. Později naimportujete soubor CSV do vyhrazeného fondu SQL a pomocí dat otestujete model.

   Soubor CSV se vytvoří ve stejné složce jako soubor poznámkového bloku. Pokud ho nevidíte hned, klikněte na aktualizovat v Průzkumníkovi souborů.

   ![Soubor CSV](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00d.png)

## <a name="launch-predictions-with-sql-scoring-wizard"></a>Spuštění předpovědi s průvodcem bodování SQL

1. Otevřete pracovní prostor synapse pomocí synapse studia.

1. Přejděte na **data**  ->  **propojených**  ->  **účtů úložiště**. Nahrajte `test_data.csv` do výchozího účtu úložiště.

   ![Nahrání dat](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00a.png)

1. Přejít na **vývoj**  ->  **skriptů SQL**. Vytvořte nový skript SQL, který se nahraje `test_data.csv` do vyhrazeného fondu SQL.

   > [!NOTE]
   > Před spuštěním aktualizujte adresu URL souboru na tomto skriptu.

   ```SQL
   IF NOT EXISTS (SELECT * FROM sys.objects WHERE NAME = 'nyc_taxi' AND TYPE = 'U')
   CREATE TABLE dbo.nyc_taxi
   (
       tipped int,
       fareAmount float,
       paymentType int,
       passengerCount int,
       tripDistance float,
       tripTimeSecs bigint,
       pickupTimeBin nvarchar(30)
   )
   WITH
   (
       DISTRIBUTION = ROUND_ROBIN,
       CLUSTERED COLUMNSTORE INDEX
   )
   GO
   
   COPY INTO dbo.nyc_taxi
   (tipped 1, fareAmount 2, paymentType 3, passengerCount 4, tripDistance 5, tripTimeSecs 6, pickupTimeBin 7)
   FROM '<URL to linked storage account>/test_data.csv'
   WITH
   (
       FILE_TYPE = 'CSV',
       ROWTERMINATOR='0x0A',
       FIELDQUOTE = '"',
       FIELDTERMINATOR = ',',
       FIRSTROW = 2
   )
   GO
   
   SELECT TOP 100 * FROM nyc_taxi
   GO
   ```

   ![Načíst data do vyhrazeného fondu SQL](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00b.png)

1. Přejít na **Data**  ->  **pracovní prostor** dat. Otevřete Průvodce bodování SQL tak, že kliknete pravým tlačítkem na vyhrazenou tabulku fondu SQL. Vyberte **Machine Learning**  ->  **obohacení s existujícím modelem**.

   > [!NOTE]
   > Možnost strojového učení se nezobrazí, pokud nemáte vytvořenou propojenou službu pro Azure Machine Learning (viz **požadavky** na začátku tohoto kurzu).

   ![Možnost Machine Learning](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00c.png)

1. V rozevíracím seznamu Vyberte propojený pracovní prostor Azure Machine Learning. Tím se načte seznam modelů strojového učení z registru modelu vybraného Azure Machine Learning pracovního prostoru. V současné době se podporují jenom modely ONNX, takže se zobrazí jenom modely ONNX.

1. Vyberte model, který jste právě proučeni, a klikněte na **pokračovat**.

   ![Vybrat model Azure Machine Learning](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00d.png)

1. Dále namapujte sloupce tabulky na vstupy modelu a určete výstupy modelu. Pokud je model uložen ve formátu MLFlow a je naplněný podpis modelu, mapování bude provedeno automaticky za použití logiky založené na podobnosti názvů. Rozhraní podporuje také ruční mapování.

   Klikněte na **Pokračovat**.

   ![Mapování tabulky na model](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00e.png)

1. Generovaný kód T-SQL je zabalen do uložené procedury. K tomu je potřeba zadat název uložené procedury. Binární soubor modelu včetně metadat (verze, popis atd.) bude fyzicky zkopírován z Azure Machine Learning do vyhrazené tabulky fondu SQL. Proto je třeba určit, na kterou tabulku se má model Uložit. Můžete zvolit možnost použít existující tabulku nebo vytvořit novou tabulku. Po dokončení klikněte na **nasadit model + otevřít Editor** a nasaďte model a vygenerujte prediktivní skript T-SQL.

   ![Vytvořit proceduru](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00f.png)

1. Po vygenerování skriptu klikněte na run (spustit) a spusťte bodování a získejte předpovědi.

   ![Spustit předpovědi](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00g.png)

## <a name="next-steps"></a>Další kroky

- [Rychlý Start: vytvoření nové propojené služby Azure Machine Learning v synapse](quickstart-integrate-azure-machine-learning.md)
- [Funkce Machine Learning ve službě Azure synapse Analytics (verze Preview pro pracovní prostory)](what-is-machine-learning.md)
