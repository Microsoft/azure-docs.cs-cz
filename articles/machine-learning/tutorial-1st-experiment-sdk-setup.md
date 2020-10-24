---
title: 'Kurz: Začínáme v poznámkových blocích Jupyter (Python)'
titleSuffix: Azure Machine Learning
description: Instalační program pro Jupyter Notebook kurzy. Vytvořte Azure Machine Learning pracovní prostor, naklonujte poznámkové bloky Jupyter do pracovního prostoru a vytvořte výpočetní instanci, ve které spustíte poznámkové bloky.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 02/10/2020
ms.custom: devx-track-python
ms.openlocfilehash: 5e9fd3b3d2d5bceb766651c2758f956b9ef105e0
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494956"
---
# <a name="tutorial-get-started-with-azure-machine-learning-in-jupyter-notebooks"></a>Kurz: Začínáme s Azure Machine Learning v poznámkových blocích Jupyter

V tomto kurzu dokončíte kroky, které vám pomohou začít s Azure Machine Learning pomocí poznámkových bloků Jupyter na [spravované cloudové pracovní stanici (instance COMPUTE)](concept-compute-instance.md). Tento kurz je prekurzorem všech dalších kurzů Jupyter Notebook.

V tomto kurzu jste:

> [!div class="checklist"]
> * Vytvořte [Azure Machine Learning pracovní prostor](concept-workspace.md) pro použití v dalších Jupyter Notebookch kurzech.
> * Naklonujte Poznámkový blok kurzů do složky v pracovním prostoru.
> * Vytvořte cloudovou instanci COMPUTE s nainstalovanou a předem nakonfigurovanou sadou Azure Machine Learning Python SDK.

Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

Azure Machine Learning pracovní prostor je základní prostředek v cloudu, který používáte k experimentování, výuce a nasazování modelů strojového učení. Přijedná se o vaše předplatné Azure a skupinu prostředků k snadno spotřebovanému objektu ve službě.

Pokud již máte pracovní prostor Azure Machine Learning, přeskočte k [naklonování složky poznámkového bloku](#clone) .  

Pracovní prostor můžete vytvořit prostřednictvím Azure Portal, webové konzoly pro správu prostředků Azure.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT]
> Poznamenejte si svůj *pracovní prostor* a *předplatné*. Tyto informace budete potřebovat, abyste se ujistili, že vytvoříte experiment na správném místě.

## <a name="run-a-notebook-in-your-workspace"></a><a name="azure"></a>Spuštění poznámkového bloku v pracovním prostoru

Azure Machine Learning v pracovním prostoru obsahuje cloudový notebook pro prostředí bez instalace a předkonfigurované prostředí. Použijte [vlastní prostředí](tutorial-1st-experiment-sdk-setup-local.md) , pokud dáváte přednost kontrole prostředí, balíčků a závislostí.

 Sledujte spolu s tímto videem nebo použijte podrobný postup k klonování a spuštění poznámkového bloku kurzu z pracovního prostoru.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4mTUr]

### <a name="clone-a-notebook-folder"></a><a name="clone"></a> Naklonování složky poznámkového bloku

Dokončili jste následující postup nastavení experimentu a spustíte kroky v Azure Machine Learning Studiu. Toto konsolidované rozhraní zahrnuje nástroje strojového učení, které slouží k provádění scénářů pro datové vědy u všech úrovní dovedností.

1. Přihlaste se k [Azure Machine Learning Studiu](https://ml.azure.com/).

1. Vyberte své předplatné a pracovní prostor, který jste vytvořili.

1. Na levé straně vyberte **poznámkové bloky**.

1. V horní části vyberte kartu **ukázky** .

1. Otevřete složku **Python** .

1. Otevřete složku s číslem verze. Toto číslo představuje aktuální vydání sady Python SDK.

1. Klikněte na tlačítko **...** napravo od složky **kurzy** a pak vyberte **klonovat**.

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png" alt-text="Snímek obrazovky, který zobrazuje složku s pokyny pro klonování.":::

1. Seznam složek zobrazuje každého uživatele, který přistupuje k pracovnímu prostoru. Vyberte složku, do které se má naklonovat složka s **kurzy**  .

### <a name="open-the-cloned-notebook"></a><a name="open"></a>Otevřít Klonovaný Poznámkový blok

1. Otevřete složku **kurzy** , která byla zavřena do části **uživatelské soubory** .

    > [!IMPORTANT]
    > Poznámkové bloky můžete zobrazit ve složce **Samples** , ale nemůžete z nich spustit Poznámkový blok. Pokud chcete spustit Poznámkový blok, ujistěte se, že jste v části **soubory uživatelů** otevřeli naklonované verze poznámkového bloku.
    
1. V části **kurzy/image-klasifikace-mnist ručně zapsaných-data** vyberte **kurz – 1. experiment-SDK-ipynb** .

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png" alt-text="Snímek obrazovky, který zobrazuje složku s pokyny pro klonování.":::

1. Na horním panelu vyberte výpočetní instanci, kterou chcete použít ke spuštění poznámkového bloku. U těchto virtuálních počítačů je předem nakonfigurované [všechno, co potřebujete ke spuštění Azure Machine Learning](concept-compute-instance.md#contents).

1. Pokud se nenaleznou žádné virtuální počítače, vyberte **+ Přidat** a vytvořte virtuální počítač instance Compute.

    1. Když vytváříte virtuální počítač, postupujte podle těchto pravidel:
 
        + Název je povinný a pole nesmí být prázdné.
        + Název musí být jedinečný (rozlišuje velká a malá písmena) napříč všemi existujícími výpočetními instancemi v oblasti Azure pracovního prostoru nebo instance služby Compute. Když název, který vyberete, není jedinečný, zobrazí se upozornění.
        + Platné znaky jsou velká a malá písmena, číslice 0 až 9 a pomlčkový znak (-).
        + Název musí mít délku 3 až 24 znaků.
        + Název by měl začínat písmenem, nejedná se o číslo ani znak pomlčky.
        + Je-li použit pomlčkový znak, musí za pomlčkou následovat alespoň jedno písmeno. Například test-, test-0, test-01 je neplatný, zatímco test-a0, test-0a jsou platné instance.

    1. Z dostupných možností vyberte velikost virtuálního počítače. Pro kurzy je výchozí virtuální počítač dobrý volbou.

    1. Potom vyberte **Vytvořit**. Nastavení virtuálního počítače může trvat přibližně pět minut.

1. Když je virtuální počítač k dispozici, zobrazí se na horním panelu nástrojů. Nyní můžete spustit Poznámkový blok buď pomocí rutiny **Spustit vše** na panelu nástrojů, nebo **stisknutím SHIFT + ENTER** v buňkách kódu poznámkového bloku.

Pokud máte vlastní widgety nebo dáváte přednost použití Jupyter nebo JupyterLab, vyberte v rozevíracím seznamu **Jupyter** úplně vpravo. Pak vyberte **Jupyter** nebo **JupyterLab**. Otevře se nové okno prohlížeče.

## <a name="next-steps"></a>Další kroky

Teď, když máte nastavené vývojové prostředí, pokračujte na výuku modelu ve Jupyter Notebook.

> [!div class="nextstepaction"]
> [Kurz: analýza modelů klasifikace obrázků pomocí MNIST ručně zapsaných dat a scikit – učení](tutorial-train-models-with-aml.md)

<a name="stop-compute-instance"></a> Pokud teď žádné jiné kurzy neplánujete, zastavte virtuální počítač se serverem cloudového poznámkového bloku, pokud ho nepoužíváte ke snížení nákladů.

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]
