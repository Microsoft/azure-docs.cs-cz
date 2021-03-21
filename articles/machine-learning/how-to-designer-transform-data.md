---
title: Transformace dat v Návrháři
titleSuffix: Azure Machine Learning
description: Naučte se importovat a transformovat data v Návrháři Azure Machine Learning, abyste mohli vytvářet vlastní datové sady.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: peterclu
ms.author: peterlu
ms.date: 06/28/2020
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: 10904275d382c7f9dbf223ded78cab0adf14992a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101661048"
---
# <a name="transform-data-in-azure-machine-learning-designer"></a>Transformace dat v Návrháři Azure Machine Learning


V tomto článku se naučíte, jak transformovat a ukládat datové sady v Návrháři Azure Machine Learning, abyste mohli připravit vlastní data pro strojové učení.

K přípravě dvou datových sad použijete ukázkovou datovou sadu [binárních hodnot řazení pro dospělé](./samples-designer.md) : jednu datovou sadu, která obsahuje informace o sestavách dospělého jenom z USA, a další datovou sadu, která obsahuje sčítání informací od jiných než USA.

V tomto článku získáte informace o těchto tématech:

1. Transformujte datovou sadu pro přípravu na školení.
1. Vyexportujte výsledné datové sady do úložiště dat.
1. Zobrazit výsledky.

Tento postup je nezbytným předpokladem pro [reučení modelů návrháře](how-to-retrain-designer.md) . V tomto článku se naučíte, jak používat transformované datové sady ke výukám více modelů s parametry kanálu.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="transform-a-dataset"></a>Transformace datové sady

V této části se dozvíte, jak naimportovat ukázkovou datovou sadu a rozdělit data do datových sad US a non-US. Další informace o tom, jak importovat vlastní data do návrháře, najdete v tématu [Jak importovat data](how-to-designer-import-data.md).

### <a name="import-data"></a>Import dat

Pomocí následujících kroků importujte ukázkovou datovou sadu.

1. Přihlaste se k <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.Azure.com</a>a vyberte pracovní prostor, se kterým chcete pracovat.

1. Přejít na Návrhář. Vyberte **snadno použitelné moduly předbuildu** a vytvořte nový kanál.

1. Vyberte výchozí výpočetní cíl pro spuštění kanálu.

1. Vlevo od plátna kanálu je paleta datových sad a modulů. Vyberte **datové sady**. Pak se podívejte na oddíl **ukázek** .

1. Přetáhněte na plátno datovou sadu **binární klasifikace pro příjem z** odkládacích souborů.

1. Klikněte pravým tlačítkem na modul **pro příjem dat příjmů z dospělého** a vyberte **vizualizovat**  >  **výstup datové sady** .

1. Pomocí okna náhledu dat můžete prozkoumat datovou sadu. Poznamenejte si zvláštní podobu hodnot sloupců "nativní země".

### <a name="split-the-data"></a>Rozdělení dat

V této části použijete [modul rozdělit data](algorithm-module-reference/split-data.md) k identifikaci a rozdělení řádků, které obsahují "Spojené státy", ve sloupci "nativní země". 

1. V paletě modulu nalevo od plátna rozbalte část **transformace dat** a najděte modul **rozdělit data** .

1. Přetáhněte na plátno modul **rozdělit data** a přetáhněte modul pod modul DataSet.

1. Připojte modul DataSet k modulu **rozdělit data** .

1. Vyberte modul **rozdělit data** .

1. V podokně podrobností modulu napravo od plátna nastavte **rozdělovací režim** na **regulární výraz**.

1. Zadejte **regulární výraz**: `\"native-country" United-States` .

    Režim **regulárního výrazu** testuje jeden sloupec pro hodnotu. Další informace o modulu Split data najdete na [referenční stránce modulu souvisejícího algoritmu](algorithm-module-reference/split-data.md).

Váš kanál by měl vypadat takto:

:::image type="content" source="./media/how-to-designer-transform-data/split-data.png" alt-text="Snímek obrazovky ukazující, jak nakonfigurovat kanál a modul rozdělit data":::


## <a name="save-the-datasets"></a>Uložení datových sad

Teď, když je váš kanál nastavený pro rozdělení dat, je potřeba určit, kam se mají zachovat datové sady. V tomto příkladu použijte modul **exportovat data** a uložte datovou sadu do úložiště dat. Další informace o úložištích dat najdete v tématu [připojení ke službám Azure Storage](how-to-access-data.md) .

1. V paletě modulu nalevo od plátna rozbalte část **vstup a výstup dat** a vyhledejte modul **Export dat** .

1. Přetáhněte dva moduly **exportu dat** pod modul **rozdělit data** .

1. Připojte každý výstupní port modulu **rozdělených dat** k jinému modulu **exportu dat** .

    Váš kanál by měl vypadat přibližně takto:

    ![Snímek obrazovky ukazující, jak propojit moduly exportu dat](media/how-to-designer-transform-data/export-data-pipeline.png).

1. Vyberte modul **exportovat data** , který je připojen k portu nejvíce *vlevo* modulu **rozdělit data** .

    Pořadí výstupních portů pro modul **rozdělit data** První výstupní port obsahuje řádky, ve kterých je regulární výraz pravdivý. V takovém případě první port obsahuje řádky pro příjem na bázi USA a druhý port obsahuje řádky pro příjem nezaložených na USA.

1. V podokně podrobností modulu napravo od plátna nastavte následující možnosti:
    
    **Typ úložiště dat**: Azure Blob Storage

    **Úložiště dat**: Vyberte existující úložiště dat, nebo vyberte nové úložiště dat, abyste ho mohli teď vytvořit.

    **Cesta**: `/data/us-income`

    **Formát souboru**: CSV

    > [!NOTE]
    > V tomto článku se předpokládá, že máte přístup k úložišti dat registrovanému v aktuálním pracovním prostoru Azure Machine Learning. Pokyny, jak nastavit úložiště dat, najdete v tématu [připojení ke službám Azure Storage](how-to-connect-data-ui.md#create-datastores).

    Pokud úložiště dat nemáte, můžete si ho vytvořit teď. V tomto článku se například uloží datové sady do výchozího účtu služby Blob Storage přidruženého k pracovnímu prostoru. Datové sady se uloží do `azureml` kontejneru v nové složce s názvem `data` .

1.  Vyberte modul **exportovat data** připojený k *pravému* portu modulu **rozdělit data** .

1. V podokně podrobností modulu napravo od plátna nastavte následující možnosti:
    
    **Typ úložiště dat**: Azure Blob Storage

    **Úložiště dat**: vyberte stejné úložiště dat jako výše.

    **Cesta**: `/data/non-us-income`

    **Formát souboru**: CSV

1. Potvrďte, že je v modulu **Export data** připojené k levému portu **rozdělených dat** **cesta** `/data/us-income` .

1. Potvrďte, že je v modulu **Export dat** připojen ke správnému portu **cesta** `/data/non-us-income` .

    Váš kanál a nastavení by měly vypadat takto:
    
    ![Snímek obrazovky ukazující, jak nakonfigurovat moduly exportu dat](media/how-to-designer-transform-data/us-income-export-data.png).

### <a name="submit-the-run"></a>Odeslat běh

Teď, když je váš kanál nastavený tak, aby data rozdělil a exportovali, odešlete běh kanálu.

1. V horní části plátna vyberte **Odeslat**.

1. V dialogovém okně **nastavit spuštění kanálu** vyberte **vytvořit novou** a vytvořte experiment.

    Experimenty logicky seskupují související spuštění kanálu. Pokud tento kanál spustíte v budoucnu, měli byste použít stejný experiment pro účely protokolování a sledování.

1. Zadejte popisný název experimentu, například "dělené sčítání-data".

1. Vyberte **Odeslat**.

## <a name="view-results"></a>Zobrazení výsledků

Po dokončení kanálu můžete výsledky zobrazit tak, že přejdete do úložiště objektů BLOB v Azure Portal. Můžete také zobrazit zprostředkující výsledky modulu **rozdělit data** a ověřit tak, že vaše data byla správně rozdělena.

1. Vyberte modul **rozdělit data** .

1. V podokně podrobností modulu napravo od plátna vyberte **výstupy + protokoly**. 

1. Vyberte ikonu vizualizace ikona ![ vizualizace ](media/how-to-designer-transform-data/visualize-icon.png) vedle **výsledků DataSet1.**. 

1. Ověřte, že sloupec "nativní země" obsahuje pouze hodnotu "Spojené státy".

1. Vyberte ikonu vizualizace ikona ![ vizualizace ](media/how-to-designer-transform-data/visualize-icon.png) vedle **výsledků Dataset2**. 

1. Ověřte, že sloupec "nativní země" neobsahuje hodnotu "Spojené státy".

## <a name="clean-up-resources"></a>Vyčištění prostředků

Tuto část přeskočte, pokud chcete pokračovat s částí 2 tohoto postupu při [přeškolování modelů pomocí návrháře Azure Machine Learning](how-to-retrain-designer.md).

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak transformovat datovou sadu a uložit ji do registrovaného úložiště dat.

Přejděte k další části této série postupů s [převýukou modelů pomocí Azure Machine Learning designeru](how-to-retrain-designer.md) , abyste mohli využít transformované datové sady a parametry kanálu k výuce modelů strojového učení.