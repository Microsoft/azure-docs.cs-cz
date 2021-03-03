---
title: Začínáme s pokročilým vývojem Azure Percept v cloudu
description: Začněte s pokročilým vývojem v cloudu prostřednictvím poznámkových bloků Jupyter a Azure Machine Learning
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: 408040ea68273a29ed9be87b60bd0cc6da736a05
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101664574"
---
# <a name="getting-started-with-advanced-development-in-the-cloud-via-jupyter-notebooks-and-azure-machine-learning"></a>Začínáme s pokročilým vývojem v cloudu prostřednictvím poznámkových bloků Jupyter a Azure Machine Learning

Tento článek vás provede procesem nastavení pracovního prostoru Azure Machine Learning, nahrání předkonfigurovaného ukázkového Jupyter Notebook do pracovního prostoru, vytvoření výpočetní instance a spuštění buněk poznámkového bloku v pracovním prostoru.

[Poznámkový blok](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) provádí přenosové učení pomocí předem připraveného modelu TensorFlow (MobileNetSSDV2Lite) na AzureML v Pythonu s vlastní datovou sadou pro detekci bowls.

Poznámkový blok ukazuje, jak začít z [díky Coco](https://cocodataset.org/#home), vyfiltrovat ho až na třídu zájmu (Bowls) a pak ho převést do příslušného formátu. Alternativně můžete použít nástroj pro označování open source [VoTT 2](https://github.com/microsoft/VoTT) k vytváření a popiskům ohraničovacích rámečků ve formátu Pascal VOC.

Po přeškolení modelu pro vlastní datovou sadu můžete model nasadit do Azure Percept DK pomocí metody s dvojitou metodou aktualizace modulu.

Inferencing modelu můžete ověřit zobrazením živého datového proudu RTSP z Azure Percept Vision SoM. Přeškolení a nasazení modelu se provádí v rámci poznámkového bloku v cloudu.

## <a name="prerequisites"></a>Požadavky

- [Předplatné Azure Machine Learning](https://azure.microsoft.com/free/services/machine-learning/)
- [Azure Percept DK s připojením služby Azure Percept Vision SoM](./overview-azure-percept-dk.md)
- [Prostředí Azure PERCEPT DK na zprovoznění](./quickstart-percept-dk-set-up.md) se dokončilo.

## <a name="download-azure-percept-github-repository"></a>Stáhnout úložiště GitHub Azure Percept

1. Přejít do [úložiště GitHub Azure Percept](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview).

1. Naklonujte úložiště nebo Stáhněte soubor ZIP. V horní části obrazovky klikněte na stáhnout **kód**  ->  **zip**.

    :::image type="content" source="./media/advanced-development-cloud/download-zip.png" alt-text="Obrazovka pro stažení GitHubu":::

## <a name="set-up-azure-machine-learning-portal-and-notebook"></a>Nastavení Azure Machine Learningového portálu a poznámkového bloku

1. Přejít na [Azure Machine Learning portál](https://ml.azure.com).

1. V rozevíracích nabídkách vyberte svůj adresář, předplatné Azure a Machine Learning pracovní prostor a klikněte na **Začínáme**.

    :::image type="content" source="./media/advanced-development-cloud/machine-learning-portal-get-started.png" alt-text="Obrazovka Začínáme s Azure ML.":::

    Pokud ještě nemáte pracovní prostor Azure Machine Learning, klikněte na **vytvořit nový pracovní prostor**. Na nové kartě prohlížeče udělejte toto:

    1. Vyberte své předplatné Azure.
    1. Vyberte skupinu prostředků.
    1. Zadejte název pracovního prostoru.
    1. Vyberte oblast.
    1. Vyberte edici vašeho pracovního prostoru.
    1. Klikněte na **Zkontrolovat a vytvořit**.
    1. Na další stránce zkontrolujte výběr a klikněte na **vytvořit**.

        :::image type="content" source="./media/advanced-development-cloud/workspace-review-and-create.png" alt-text="Obrazovka pro vytvoření pracovního prostoru v Azure ML":::

    Počkejte prosím několik minut na vytvoření pracovního prostoru. Po vytvoření pracovního prostoru se zobrazí zelená zaškrtnutí vedle vašich prostředků a **nasazení se dokončí** v horní části stránky s přehledem Machine Learning Services.

    :::image type="content" source="./media/advanced-development-cloud/workspace-creation-complete-inline.png" alt-text="Potvrzení vytvoření pracovního prostoru" lightbox= "./media/advanced-development-cloud/workspace-creation-complete.png":::

    Po vytvoření pracovního prostoru se vraťte na kartu portál Machine Learning a klikněte na **Začínáme**.

1. Na domovské stránce pracovního prostoru Machine Learning klikněte v levém podokně na **poznámkové bloky** .

    :::image type="content" source="./media/advanced-development-cloud/notebook.png" alt-text="Domovská stránka Azure ML.":::

1. Na kartě **Moje soubory** klikněte na svislou šipku pro nahrání souboru. ipynb.

    :::image type="content" source="./media/advanced-development-cloud/upload-files.png" alt-text="Domovská stránka – zvýraznění ikony pro nahrání souboru":::

1. Přejděte na adresu a vyberte [soubor Transferlearningusing_SSDLiteV2 model. ipynb](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) z místní kopie úložiště GitHub Azure Percept. Klikněte na **Otevřít**. V okně **nahrát soubory** zaškrtněte políčko vedle **obsahu důvěry z tohoto souboru** a klikněte na **nahrát**.

    :::image type="content" source="./media/advanced-development-cloud/select-file.png" alt-text="Obrazovka pro výběr souboru.":::

1. V pravém horním panelu nabídek ověřte stav služby **COMPUTE** . Pokud se nenaleznou žádné výpočty, klikněte na **+** ikonu a vytvořte novou výpočetní kapacitu.

    :::image type="content" source="./media/advanced-development-cloud/no-computes-found-inline.png" alt-text="Stav výpočtů se zvýrazněnou možností + ikona" lightbox= "./media/advanced-development-cloud/no-computes-found.png":::

1. V okně **Nová instance COMPUTE** zadejte **výpočetní název**, zvolte **typ virtuálního počítače** a vyberte **Velikost virtuálního počítače**. Klikněte na **Vytvořit**.

    :::image type="content" source="./media/advanced-development-cloud/new-compute-instance.png" alt-text="Obrazovka pro vytvoření nové instance COMPUTE":::

    Po kliknutí na **vytvořit** se ve stavu služby **COMPUTE** zobrazí modrý **\<your compute name> kruh a vytvoří** se.

    :::image type="content" source="./media/advanced-development-cloud/compute-creating.png" alt-text="Stav výpočtů při vytváření výpočetního prostředí stále probíhá.":::

    Po dokončení výpočetního prostředí se ve vašem stavu služby **COMPUTE** zobrazí zelený kroužek a **\<your compute name> spuštěný** .

    :::image type="content" source="./media/advanced-development-cloud/compute-running.png" alt-text="Výpočetní stav ukazující vytváření výpočetních prostředků je dokončený.":::

1. Jakmile je výpočetní prostředí spuštěné, vyberte v rozevírací nabídce vedle ikony možnost **Python 3,6-AzureML** **+** .

## <a name="working-with-the-notebook"></a>Práce s poznámkovým blokem

Teď jste připraveni spustit Poznámkový blok, abyste mohli naučit svůj vlastní detektor Bowl a nasazovat ho do svého devkitu. Nezapomeňte spustit každou buňku poznámkového bloku jednotlivě, protože některé buňky vyžadují vstupní parametry před spuštěním skriptu. Buňky, které vyžadují vstupní parametry, mohou být upraveny přímo v poznámkovém bloku. Chcete-li spustit buňku, klikněte na ikonu Přehrát na levé straně buňky:

:::image type="content" source="./media/advanced-development-cloud/run-cell-inline.png" alt-text="Ikona buňky zvýraznění poznámkového bloku" lightbox= "./media/advanced-development-cloud/run-cell.png":::

## <a name="next-steps"></a>Další kroky

Další ukázkové poznámkové bloky pro Azure Machine Learning služby najdete na tomto [úložišti](https://github.com/Azure/MachineLearningNotebooks/tree/2aa7c53b0ce84e67565d77e484987714fdaed36e/how-to-use-azureml) .
