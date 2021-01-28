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
ms.openlocfilehash: d8db9257ad6eed98b39cd2c9a52351f013453365
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98935224"
---
# <a name="tutorial-machine-learning-model-scoring-wizard-preview-for-dedicated-sql-pools"></a>Kurz: Průvodce bodování modelu Machine Learning (Preview) pro vyhrazené fondy SQL

Naučte se snadno rozšířit data ve vyhrazených fondech SQL pomocí prediktivních modelů strojového učení. Modely, které vaše datové specialisty vytvářejí, jsou teď snadno dostupné pro odborníky na data pro prediktivní analýzy. Data Professional v Azure synapse Analytics můžou jednoduše vybrat model z registru Azure Machine Learning modelu pro nasazení ve fondech Azure synapse SQL a spustit předpovědi pro rozšíření dat.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> - Naučte se model prediktivního strojového učení a zaregistrujte model v registru Azure Machine Learning modelu.
> - Pomocí Průvodce bodování SQL spusťte předpovědi ve vyhrazeném fondu SQL.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet před tím, než začnete](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

- [Pracovní prostor Azure synapse Analytics](../get-started-create-workspace.md) s účtem úložiště Azure Data Lake Storage Gen2 nakonfigurovaný jako výchozí úložiště. Musíte být *přispěvatelem dat objektů BLOB úložiště* Data Lake Storage Gen2 systému souborů, se kterým pracujete.
- Vyhrazený fond SQL ve vašem pracovním prostoru Azure synapse Analytics. Podrobnosti najdete v tématu [vytvoření vyhrazeného fondu SQL](../quickstart-create-sql-pool-studio.md).
- Azure Machine Learning propojená služba v pracovním prostoru Azure synapse Analytics. Podrobnosti najdete v tématu [vytvoření propojené služby Azure Machine Learning v Azure synapse](quickstart-integrate-azure-machine-learning.md).

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se na [Azure Portal](https://portal.azure.com/).

## <a name="train-a-model-in-azure-machine-learning"></a>Výuka modelu v Azure Machine Learning

Než začnete, ověřte, že je vaše verze skriptu sklearn 0.20.3.

Než spustíte všechny buňky v poznámkovém bloku, ověřte, že je spuštěná instance služby Compute.

![Snímek obrazovky, který zobrazuje ověření Azure Machine Learning Compute.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00b.png)

1. Přejít do pracovního prostoru Azure Machine Learning.

1. Stáhněte si [NYC s popisem taxislužby. ipynb](https://go.microsoft.com/fwlink/?linkid=2144301).

1. Otevřete pracovní prostor Azure Machine Learning v [Azure Machine Learning Studio](https://ml.azure.com).

1. Přejít na **poznámkové bloky**–  >  **nahrání souborů** Pak vyberte soubor **NYCs. ipynb s popisem taxislužby** , který jste stáhli a nahráli.
   ![Snímek obrazovky s tlačítkem pro nahrání souboru](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00a.png)

1. Po nahrání a otevření poznámkového bloku vyberte **Spustit všechny buňky**.

   Jedna z buněk může selhat a požádá vás o ověření v Azure. Sledujte ho ve výstupech buněk a ověřte ho v prohlížeči pomocí odkazu a zadání kódu. Pak znovu spusťte Poznámkový blok.

1. Poznámkový blok vytvoří model ONNX a zaregistruje ho pomocí MLflow. Pokud chcete ověřit, že je nový model správně zaregistrován, přejdete na **modely** .
   ![Snímek obrazovky, který zobrazuje model v registru.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00c.png)

1. Spuštění poznámkového bloku taky exportujte testovací data do souboru CSV. Stáhněte soubor CSV do místního systému. Později naimportujete soubor CSV do vyhrazeného fondu SQL a pomocí dat otestujete model.

   Soubor CSV se vytvoří ve stejné složce jako soubor poznámkového bloku. Pokud ho nevidíte hned, vyberte **aktualizovat** v Průzkumníkovi souborů.

   ![Snímek obrazovky, který zobrazuje soubor C S V.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00d.png)

## <a name="launch-predictions-with-the-sql-scoring-wizard"></a>Spuštění předpovědi pomocí Průvodce bodování SQL

1. Otevřete pracovní prostor Azure synapse pomocí synapse studia.

1. Přejít na **data**  >  **propojených**  >  **účtů úložiště**. Nahrajte `test_data.csv` do výchozího účtu úložiště.

   ![Snímek obrazovky zobrazující výběry pro nahrávání dat](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00a.png)

1. Přejít na **vývoj**  >  **skriptů SQL**. Vytvořte nový skript SQL, který se nahraje `test_data.csv` do vyhrazeného fondu SQL.

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

1. Přejít na   >  **pracovní prostor** dat. Otevřete Průvodce bodování SQL tak, že kliknete pravým tlačítkem na vyhrazenou tabulku fondu SQL. Vyberte **Machine Learning**  >  **obohacení s existujícím modelem**.

   > [!NOTE]
   > Možnost strojového učení se nezobrazí, pokud nemáte vytvořenou propojenou službu pro Azure Machine Learning. (Viz [předpoklady](#prerequisites) na začátku tohoto kurzu.)

   ![Snímek obrazovky, který zobrazuje možnost strojového učení.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00c.png)

1. V rozevíracím seznamu Vyberte propojený pracovní prostor Azure Machine Learning. Tento krok načte seznam modelů strojového učení z registru modelu vybraného Azure Machine Learning pracovního prostoru. V současné době se podporují jenom modely ONNX, takže v tomto kroku se zobrazí jenom modely ONNX.

1. Vyberte model, který jste právě proškolený, a pak vyberte **pokračovat**.

   ![Snímek obrazovky zobrazující výběr modelu Azure Machine Learning.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00d.png)

1. Namapujte sloupce tabulky na vstupy modelu a určete výstupy modelu. Pokud je model uložen ve formátu MLflow a je naplněný podpis modelu, mapování bude provedeno automaticky za vás pomocí logiky založené na podobnosti názvů. Rozhraní podporuje také ruční mapování.

   Vyberte **Pokračovat**.

   ![Snímek obrazovky, který ukazuje mapování tabulky na model.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00e.png)

1. Generovaný kód T-SQL je zabalen do uložené procedury. Důvodem je, že je potřeba zadat název uložené procedury. Binární soubor modelu, včetně metadat (verze, popis a další informace), bude fyzicky zkopírován z Azure Machine Learning do vyhrazené tabulky fondu SQL. Proto je třeba určit, na kterou tabulku se má model Uložit. 

   Můžete zvolit buď **existující tabulku** , nebo **vytvořit novou**. Až budete hotovi, vyberte **nasadit model + otevřít skript** a nasaďte model a vygenerujte prediktivní skript T-SQL.

   ![Snímek obrazovky zobrazující výběry pro vytvoření uložené procedury](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00f.png)

1. Po vygenerování skriptu vyberte **Spustit** pro spuštění bodování a získejte předpovědi.

   ![Snímek obrazovky, který ukazuje bodování a předpovědi.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00g.png)

## <a name="next-steps"></a>Další kroky

- [Rychlý Start: vytvoření nové propojené služby Azure Machine Learning v Azure synapse](quickstart-integrate-azure-machine-learning.md)
- [Funkce Machine Learning ve službě Azure synapse Analytics](what-is-machine-learning.md)
