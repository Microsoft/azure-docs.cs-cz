---
title: 'Kurz: Začínáme v poznámkových blocích Jupyter (Python)'
titleSuffix: Azure Machine Learning
description: Instalační program pro Jupyter Notebook kurzy.  Vytvořte Azure Machine Learning pracovní prostor, naklonujte poznámkové bloky Jupyter do pracovního prostoru a vytvořte výpočetní instanci, ve které spustíte poznámkové bloky.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 02/10/2020
ms.custom: devx-track-python
ms.openlocfilehash: de52013628f5d02bedcf72a99e0fad25cabe5d8f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90896863"
---
# <a name="tutorial-get-started-with-azure-machine-learning-in-jupyter-notebooks"></a>Kurz: Začínáme s Azure Machine Learning v poznámkových blocích Jupyter

V tomto kurzu provedete kroky, které vám pomohou začít s Azure Machine Learning pomocí poznámkových bloků Jupyter ve [spravované cloudové pracovní stanici (instanci COMPUTE)](concept-compute-instance.md). Tento kurz je prekurzorem všech dalších kurzů Jupyter Notebook.

V tomto kurzu jste:

> [!div class="checklist"]
> * Vytvořte [pracovní prostor Azure Machine Learning](concept-workspace.md) pro použití v dalších Jupyter Notebookch kurzech.
> * Naklonujte Poznámkový blok kurzů do složky v pracovním prostoru.
> * Vytvořte cloudovou instanci COMPUTE s nainstalovanou a předem nakonfigurovanou sadou Azure Machine Learning Python SDK.

Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree) dnes

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

Azure Machine Learning pracovní prostor je základní prostředek v cloudu, který používáte k experimentování, výuce a nasazování modelů strojového učení. Přijedná se o vaše předplatné Azure a skupinu prostředků k snadno spotřebovanému objektu ve službě.

Pracovní prostor můžete vytvořit prostřednictvím Azure Portal, webové konzoly pro správu prostředků Azure.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT]
> Poznamenejte si svůj **pracovní prostor** a **předplatné**. Budete je potřebovat, abyste se ujistili, že vytvoříte experiment na správném místě. 

## <a name="run-notebook-in-your-workspace"></a><a name="azure"></a>Spuštění poznámkového bloku v pracovním prostoru

Azure Machine Learning v pracovním prostoru obsahuje cloudový notebook pro instalaci bez předkonfigurovaného a předem nakonfigurovaného prostředí. Použijte [vlastní prostředí](tutorial-1st-experiment-sdk-setup-local.md) , pokud dáváte přednost kontrole prostředí, balíčků a závislostí.

 Postupujte podle tohoto videa nebo pomocí podrobných kroků naklonujte a spusťte Poznámkový blok kurzu z pracovního prostoru.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4mTUr]

### <a name="clone-a-notebook-folder"></a>Naklonování složky poznámkového bloku

Dokončili jste následující postup experimentování a spouštění v sadě Azure Machine Learning Studio, konsolidované rozhraní, které zahrnuje nástroje strojového učení, které slouží k provádění scénářů pro datové vědy v rámci všech úrovní dovedností.

1. Přihlaste se k [Azure Machine Learning Studiu](https://ml.azure.com/).

1. Vyberte své předplatné a pracovní prostor, který jste vytvořili.

1. Na levé straně vyberte **poznámkové bloky** .

1. V horní části vyberte kartu **ukázky** .

1. Otevřete složku **Python** .

1. Otevřete složku s číslem verze.  Toto číslo představuje aktuální vydání sady Python SDK.

1. Na pravé straně složky **kurzy** vyberte **"..."** a pak vyberte **klonovat**.

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/clone-tutorials.png" alt-text="Kopírovat složku kurzů":::

1. Seznam složek zobrazuje každého uživatele, který přistupuje k pracovnímu prostoru.  Vyberte složku, do které se má naklonovat složka s **kurzy**  .

### <a name="open-the-cloned-notebook"></a><a name="open"></a>Otevřít Klonovaný Poznámkový blok

1. Otevřete složku **kurzy** , která se právě zavřela v části **uživatelské soubory** .

    > [!IMPORTANT]
    > Poznámkové bloky můžete zobrazit ve složce **Samples** , ale nemůžete z nich spustit Poznámkový blok.  Pokud chcete spustit Poznámkový blok, ujistěte se, že jste v části **soubory uživatelů** otevřeli naklonované verze poznámkového bloku.
    
1. V části **kurzy/image-klasifikace-mnist ručně zapsaných-data** vyberte **kurz – 1. experiment-SDK-ipynb** .

    :::image type="content" source="media/tutorial-1st-experiment-sdk-setup/expand-user-folder.png" alt-text="Otevřít složku s kurzy":::

1. Na horním panelu vyberte výpočetní instanci, kterou chcete použít ke spuštění poznámkového bloku. Tyto virtuální počítače jsou předem nakonfigurované se všemi [potřebnými ke spuštění Azure Machine Learning](concept-compute-instance.md#contents).

1. Pokud se nenaleznou žádné virtuální počítače, vyberte **+ Přidat** a vytvořte virtuální počítač instance Compute. 

    1. Když vytváříte virtuální počítač, postupujte podle těchto pravidel:  
        + Název je povinný a nemůže být prázdný.
        + Název musí být jedinečný (rozlišuje velká a malá písmena) ve všech stávajících výpočetních instancích v oblasti Azure pracovní prostor/výpočetní instance. Když zvolený název není jedinečný, zobrazí se upozornění.
        + Platné znaky jsou velká a malá písmena, čísla (0 až 9) a spojovník (-).
        + Název musí mít délku 3 až 24 znaků.
        + Název by měl začínat písmenem (nejedná se o číslo nebo pomlčku).
        + Je-li použit spojovník znak, musí za pomlčkou následovat alespoň jedno písmeno. Příklad: test-, test-0, test-01 je neplatný, zatímco test-a0, test-0a jsou platné instance.

    1.  Z dostupných možností vyberte velikost virtuálního počítače. Pro kurzy je výchozí virtuální počítač dobrý volbou.

    1. Potom vyberte **Vytvořit**. Nastavení virtuálního počítače může trvat přibližně 5 minut.

1. Jakmile je virtuální počítač k dispozici, zobrazí se na horním panelu nástrojů.  Tento Poznámkový blok teď můžete spustit buď pomocí rutiny **Spustit vše** na panelu nástrojů, nebo pomocí **SHIFT + ENTER** v buňkách kódu poznámkového bloku.

Pokud máte vlastní widgety nebo dáváte přednost použití Jupyter/JupyterLab, vyberte rozevírací nabídku **Jupyter** úplně vpravo a potom vyberte **Jupyter** nebo **JupyterLab**. Otevře se nové okno prohlížeče.

## <a name="next-steps"></a>Další kroky

Teď, když máte nastavené vývojové prostředí, pokračujte na výuku modelu ve Jupyter Notebook:

> [!div class="nextstepaction"]
> [Kurz: analýza modelů klasifikace obrázků pomocí MNIST ručně zapsaných dat a scikit – učení](tutorial-train-models-with-aml.md)

<a name="stop-compute-instance"></a> Pokud teď neplánujete žádné jiné kurzy, měli byste zastavit virtuální počítač se serverem cloudového poznámkového bloku, pokud ho nepoužíváte ke snížení nákladů:

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]
