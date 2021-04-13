---
title: 'ML Studio (Classic): migrace na sadu Azure Machine Learning-opětovné sestavení sady dat'
description: Opětovné sestavení datových sad Studio (Classic) v Návrháři Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 02/04/2021
ms.openlocfilehash: 9604fc9d862d94ba5e566753d2186d7d28aa37ee
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308842"
---
# <a name="migrate-a-studio-classic-dataset-to-azure-machine-learning"></a>Migrace datové sady studia (Classic) do Azure Machine Learning

V tomto článku se dozvíte, jak migrovat datovou sadu studia (Classic) na Azure Machine Learning. Další informace o migraci z studia (Classic) najdete v [článku Přehled migrace](migrate-overview.md).

Máte tři možnosti, jak datovou sadu migrovat na Azure Machine Learning. Přečtěte si jednotlivé části, abyste zjistili, která možnost je pro váš scénář nejvhodnější.


|Kde jsou data? | Možnost migrace  |
|---------|---------|
|V studiu (Classic)     |  Možnost 1: [Stáhněte datovou sadu z studia (Classic) a nahrajte ji do Azure Machine Learning](#download-the-dataset-from-studio-classic).      |
|Cloudové úložiště     | Možnost 2: [Zaregistrujte datovou sadu ze zdroje cloudu](#import-data-from-cloud-sources). <br><br>  Možnost 3: [k získání dat z cloudového zdroje použijte modul import dat](#import-data-from-cloud-sources).        |

> [!NOTE]
> Azure Machine Learning také podporuje [pracovní postupy](../how-to-create-register-datasets.md) pro vytváření a správu datových sad jako první. 

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Pracovní prostor služby Azure Machine Learning. [Vytvořte pracovní prostor Azure Machine Learning](../how-to-manage-workspace.md#create-a-workspace).
- Datová sada studia (Classic), která se má migrovat.


## <a name="download-the-dataset-from-studio-classic"></a>Stáhnout datovou sadu z studia (Classic)

Nejjednodušší způsob, jak migrovat datovou sadu studia (Classic) na Azure Machine Learning, je stáhnout datovou sadu a zaregistrovat ji v Azure Machine Learning. Tím se vytvoří nová kopie datové sady a nahraje se do úložiště dat Azure Machine Learning.

Následující typy datových sad Studio (Classic) si můžete stáhnout přímo.

* Prostý text (. txt)
* Hodnoty oddělené čárkami (CSV) s hlavičkou (. csv) nebo bez (.nh.csv)
* Hodnoty oddělené tabulátory (TSV) s hlavičkou (. TSV) nebo bez (. NH. TSV)
* Excelový soubor
* Soubor zip (. zip)

Přímé stažení datových sad:
1. Přejdete do pracovního prostoru studia (Classic) ( [https://studio.azureml.net](https://studio.azureml.net) ).
1. V levém navigačním panelu vyberte kartu **datové sady** .
1. Vyberte datovou sadu, kterou chcete stáhnout.
1. V dolním panelu akcí vyberte **Stáhnout**.

    ![Snímek obrazovky, který ukazuje, jak stáhnout datovou sadu v nástroji Studio (Classic)](./media/migrate-register-dataset/download-dataset.png)

U následujících typů dat je nutné ke stažení datových sad použít modul **převést do sdíleného svazku clusteru** .

* Data SVMLight (. SVMLight) 
* Data ve formátu souboru relací atributů (ARFF) (. arff) 
* Objekt R nebo soubor pracovního prostoru (. RData
* Typ datové sady (. data). Typ datové sady je interní datový typ studia (klasický) pro výstup modulu.

Chcete-li převést datovou sadu na sdílený svazek clusteru a stáhnout výsledky:

1. Přejdete do pracovního prostoru studia (Classic) ( [https://studio.azureml.net](https://studio.azureml.net) ).
1. Vytvořte nový experiment.
1. Přetáhněte datovou sadu, kterou chcete stáhnout, na plátno.
1. Přidejte modul **převést do sdíleného svazku clusteru** .
1. Připojte ke vstupnímu portu vaší datové sady **převod na** výstupní port CSV.
1. Spusťte experiment.
1. Klikněte pravým tlačítkem na modul **převést do sdíleného svazku clusteru** .
1. Vyberte možnost stáhnout **datovou sadu výsledků**  >  .

    ![Snímek obrazovky ukazující, jak nastavit kanál pro převod na sdílený svazek clusteru](./media/migrate-register-dataset/csv-download-dataset.png)

### <a name="upload-your-dataset-to-azure-machine-learning"></a>Nahrajte datovou sadu do Azure Machine Learning

Po stažení datového souboru můžete datovou sadu zaregistrovat v Azure Machine Learning:

1. Přejít na Azure Machine Learning Studio ([ml.Azure.com](https://ml.azure.com)).
1. V levém navigačním podokně vyberte kartu **datové sady** .
1. Vyberte **vytvořit datovou sadu**  >  **z místních souborů**.
    ![Snímek obrazovky s kartou datových sad a tlačítkem pro vytvoření místního souboru](./media/migrate-register-dataset/register-dataset.png)
1. Zadejte název a popis.
1. Jako **Typ datové sady** vyberte **tabelární**.

    > [!NOTE]
    > Soubory ZIP můžete odeslat také jako datové sady. Pokud chcete nahrát soubor ZIP, vyberte **soubor** pro **Typ datové sady**.

1. **Pro úložiště dat a výběr souborů** vyberte úložiště dat, do kterého chcete uložit soubor datové sady.

    Ve výchozím nastavení Azure Machine Learning ukládá datovou sadu do výchozího pracovního prostoru SAS. Další informace o úložištích dat najdete v tématu [připojení ke službám úložiště](../how-to-access-data.md).

1. Nastavte nastavení analýzy dat a schéma pro datovou sadu. Potvrďte nastavení.

## <a name="import-data-from-cloud-sources"></a>Import dat z cloudových zdrojů

Pokud vaše data jsou již ve službě cloudového úložiště a chcete uchovávat data v jejím nativním umístění. Můžete použít kteroukoli z následujících možností:

|Metoda ingestování|Description|
|---| --- |
|Registrace Azure Machine Learning datové sady|Ingestování dat z místních a online zdrojů dat (objekt blob, ADLS Gen1, ADLS Gen2, sdílení souborů, databáze SQL). <br><br>Vytvoří odkaz na zdroj dat, který je laxně vytvářená vyhodnocen za běhu. Tuto možnost použijte, pokud opakovaně přistupujete k této datové sadě a chcete povolit pokročilé funkce dat, jako je například Správa verzí dat a monitorování.
|Importovat data modul|Ingestování dat z online zdrojů dat (objekt blob, ADLS Gen1, ADLS Gen2, sdílení souborů, databáze SQL). <br><br> Datová sada je importována pouze do aktuálního spuštění kanálu návrháře.


>[!Note]
> Uživatelé nástroje Studio (Classic) by měli mít na paměti, že následující zdroje cloudu nejsou nativně podporované v Azure Machine Learning:
> - Dotaz na podregistr
> - Tabulka Azure
> - Azure Cosmos DB
> - Místní SQL Database
>
> Doporučujeme, aby uživatelé migrovali svá data do podporovaných služeb úložiště pomocí Azure Data Factory.  

### <a name="register-an-azure-machine-learning-dataset"></a>Registrace Azure Machine Learning datové sady

Pomocí následujících kroků zaregistrujete datovou sadu pro Azure Machine Learning z cloudové služby: 

1. [Vytvořte úložiště dat](../how-to-connect-data-ui.md#create-datastores), které propojí službu cloudového úložiště s vaším pracovním prostorem Azure Machine Learning. 

1. [Zaregistrujte datovou sadu](../how-to-connect-data-ui.md#create-datasets). Pokud migrujete datovou sadu studia (Classic), vyberte nastavení **tabulková** datová sada.

Když datovou sadu zaregistrujete v Azure Machine Learning, můžete ji použít v Návrháři:
 
1. Vytvoří novou koncept kanálu návrháře.
1. V paletě modulů vlevo rozbalte část **datové sady** .
1. Přetáhněte registrovanou datovou sadu na plátno. 

### <a name="use-the-import-data-module"></a>Použití modulu import dat

K importu dat přímo do kanálu návrháře použijte následující postup:

1. [Vytvořte úložiště dat](https://github.com/MicrosoftDocs/azure-docs-pr/blob/master/articles/machine-learning/how-to-connect-data-ui.md#create-datastores), které propojí službu cloudového úložiště s vaším pracovním prostorem Azure Machine Learning. 

Po vytvoření úložiště dat můžete k ingestování dat použít modul [**importovat data**](../algorithm-module-reference/import-data.md) v Návrháři:

1. Vytvoří novou koncept kanálu návrháře.
1. V paletě modulu vlevo Najděte modul **Import dat** a přetáhněte ho na plátno.
1. Vyberte modul **Import dat** a pomocí nastavení v pravém panelu nakonfigurujte zdroj dat.

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak migrovat datovou sadu studia (Classic) na Azure Machine Learning. Dalším krokem je [opětovné sestavení školicího kanálu studia (Classic)](migrate-rebuild-experiment.md).


Podívejte se na další články v řadě migrace pro Studio (Classic):

1. [Přehled migrace](migrate-overview.md)
1. **Migrujte datové sady**.
1. [Znovu sestavte školicí kanál studia (Classic)](migrate-rebuild-experiment.md).
1. [Opětovné sestavení webové služby studia (Classic)](migrate-rebuild-web-service.md).
1. [Integrujte webovou službu Azure Machine Learning s klientskými aplikacemi](migrate-rebuild-integrate-with-client-app.md).
1. [Migrace spouštěného skriptu R](migrate-execute-r-script.md)
