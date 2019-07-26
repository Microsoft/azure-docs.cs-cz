---
title: 'Rychlý start: Spuštění poznámkového bloku v cloudu'
titleSuffix: Azure Machine Learning service
description: V tomto kurzu se dozvíte, jak začít s Azure Machine Learning služby a použít spravovaný server poznámkového bloku v cloudu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
author: sdgilley
ms.author: sgilley
ms.date: 05/14/2019
ms.custom: seodec18
ms.openlocfilehash: 1124bb17abb9340b442d8d6075551ffe0dc681f7
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68371058"
---
# <a name="quickstart-use-a-cloud-based-notebook-server-to-get-started-with-azure-machine-learning"></a>Rychlý start: Začněte s Azure Machine Learning pomocí cloudového serveru poznámkového bloku.

V tomto rychlém startu se naučíte, jak začít používat službu Azure Machine Learning pomocí spravovaného serveru poznámkového bloku v cloudu. Není nutná žádná instalace. Pokud chcete sadu SDK místo toho nainstalovat do vlastního prostředí Pythonu, přečtěte [si téma rychlý Start: Začněte s Azure Machine Learning](quickstart-run-local-notebook.md)pomocí vlastního serveru poznámkového bloku.

V tomto rychlém startu se dozvíte, jak používat [pracovní prostor služby Azure Machine Learning](concept-azure-machine-learning-architecture.md) k udržení přehledu o experimentech v rámci strojového učení (ml). Provedete to tak, že vytvoříte [virtuální počítač Poznámkový blok (Preview)](how-to-configure-environment.md#notebookvm): zabezpečenou cloudovou pracovní stanici Azure, která poskytuje Jupyter notebook, JupyterLab a plně připravené prostředí ml. Pak na tomto virtuálním počítači spustíte Poznámkový blok Pythonu, který protokoluje hodnoty do pracovního prostoru.

Provedete to provedením následujících akcí:

* Vytvořte pracovní prostor.
* Vytvořte virtuální počítač s poznámkovým blokem ve vašem pracovním prostoru.
* Otevřete webové rozhraní Jupyter.
* Otevřete Poznámkový blok, který obsahuje kód pro odhad pí a zaprotokoluje chyby v každé iteraci.
* Spusťte Poznámkový blok.
* Zobrazit hodnoty chyb protokolu v pracovním prostoru. Následující příklad ukazuje, jak pracovní prostor pomáhá sledovat informace vygenerované ve skriptu.

Pokud ještě nemáte předplatné Azure, vytvořte si bezplatný účet před tím, než začnete. Vyzkoušejte [bezplatnou nebo placená verzi služby Azure Machine Learning](https://aka.ms/AMLFree).

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

Pokud máte pracovní prostor služby Azure Machine Learning, přejděte k [Další části](#create-notebook). V opačném případě vytvořte nyní.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="create-notebook"></a>Vytvoření virtuálního počítače s poznámkovým blokem

 V pracovním prostoru vytvořte cloudový prostředek, abyste mohli začít používat Jupyter poznámkové bloky. Tento prostředek je cloudová platforma, která je předem nakonfigurované s vše, co potřebujete ke spuštění služby Azure Machine Learning.

1. Otevřete pracovní prostor v [Azure Portal](https://portal.azure.com/). Pokud si nejste jistí, jak váš pracovní prostor najít na portálu, přečtěte si téma Jak [Zobrazit pracovní prostor](how-to-manage-workspace.md#view).

1. Na stránce pracovního prostoru na levé straně vyberte **virtuální počítače poznámkového bloku** .

1. Vyberte **+ Nová** a vytvořte virtuální počítač poznámkového bloku.  

     ![Výběr nového virtuálního počítače](./media/quickstart-run-cloud-notebook/add-workstation.png)

1. Zadejte název vašeho virtuálního počítače. Potom vyberte **Vytvořit**.

    > [!NOTE]
    > Název vašeho virtuálního počítače poznámkového bloku musí mít 2 až 16 znaků. Platné znaky jsou písmena, číslice a spojovníky. Název musí být v rámci vašeho předplatného Azure jedinečný.

    ![Vytvoření nového virtuálního počítače](media/quickstart-run-cloud-notebook/create-new-workstation.png)

1. Počkejte přibližně 4 až 5 minut, než se stav změní na **spuštěno**.


## <a name="open-the-jupyter-web-interface"></a>Otevřete webové rozhraní Jupyter

Po spuštění virtuálního počítače pomocí části **virtuální počítače poznámkového bloku** otevřete webové rozhraní Jupyter.

1. Vyberte **Jupyter** ve sloupci **identifikátor URI** pro váš virtuální počítač.  

    ![Spuštění serveru Jupyter notebook](./media/quickstart-run-cloud-notebook/start-server.png)

    Odkaz spustí svůj server poznámkového bloku a otevře webovou stránku Jupyter poznámkového bloku na nové kartě prohlížeče.  Tento odkaz bude fungovat jenom pro osobu, která virtuální počítač vytvořila.  Každý uživatel pracovního prostoru musí vytvořit svůj vlastní virtuální počítač.

1. Na webové stránce poznámkového bloku Jupyter je název nejvyšší složky vaše uživatelské jméno. Vyberte tuto složku.

    > [!TIP]
    > Tato složka se nachází na [kontejneru úložiště](concept-workspace.md#resources) ve vašem pracovním prostoru, nikoli na samotném virtuálním počítači poznámkového bloku.  Virtuální počítač poznámkového bloku můžete odstranit a pořád zachovat veškerou práci.  Když později vytvoříte nový virtuální počítač s poznámkovým blokem, načte se tato složka.  Pokud svůj pracovní prostor sdílíte s ostatními, uvidí vaši složku a zobrazí se jejich. 

1. Název složky ukázek zahrnuje číslo verze (například**Samples-1.0.33.1**). Vyberte složku ukázky.

1. Vyberte složku pro **rychlé** zprovoznění.

## <a name="run-the-notebook"></a>Spuštění poznámkového bloku

Spusťte Poznámkový blok, který odhadne PI a zapíše chybu do vašeho pracovního prostoru.

1. Pro otevření poznámkového bloku vyberte **01. Run-experiment. ipynb** .

1. Pokud se zobrazí výstraha "jádro nebylo nalezeno", vyberte jádro **Python 3,6-AzureML** (přibližně uprostřed seznamu) a nastavte jádro.

1. Vyberte první buňku kódu a pak vyberte **Spustit**.

    > [!NOTE]
    > Buňky kódu mají hranaté závorky. Pokud jsou hranaté závorky prázdné ( __[]__ ), kód nebyl spuštěn. I když je kód spuštěný, zobrazí se hvězdička ( __[*]__ ). Po dokončení kódu se zobrazí číslo **[1]** .  Číslo oznamuje pořadí, ve kterém byly buňky spuštěny.
    >
    > Pokud chcete spustit buňku, použijte **SHIFT + ENTER** jako zástupce.

    ![Spustit první buňku kódu](media/quickstart-run-cloud-notebook/cell1.png)

1. Spusťte druhou buňku kódu. Pokud se zobrazí pokyny k ověření, zkopírujte kód a použijte odkaz pro přihlášení. Po přihlášení se v prohlížeči zapamatuje toto nastavení.  

    ![Ověřit](media/quickstart-run-cloud-notebook/authenticate.png)

1. Po úspěšném spuštění buňky kódu se zobrazí číslo buňky __[2]__ . Pokud se přihlásíte, zobrazí se zpráva o úspěšném stavu ověření.   Pokud se nemusíte přihlásit, zobrazí se pro tuto buňku žádný výstup. Zobrazí se pouze číslo, které ukazuje, že buňka byla úspěšně spuštěna.

    ![Zpráva o úspěchu](media/quickstart-run-cloud-notebook/success.png)

1. Spustí zbytek buněk kódu. Po dokončení každé buňky se zobrazí číslo jeho buňky. Pouze poslední buňka zobrazí všechny ostatní výstupy.  

    V buňce `run.log` největší kód se zobrazí na více místech. Každá `run.log` z nich přidá svou hodnotu do vašeho pracovního prostoru.

## <a name="view-logged-values"></a>Zobrazení zaprotokolovaných hodnot

1. Výstup z `run` buňky obsahuje odkaz zpátky na Azure Portal, kde můžete zobrazit výsledky experimentů ve vašem pracovním prostoru.

    ![Zobrazení experimentů](./media/quickstart-run-cloud-notebook/view-exp.png)

1. Pokud chcete zobrazit informace o spuštění ve vašem pracovním prostoru, vyberte **odkaz na Azure Portal** . Tento odkaz otevře pracovní prostor v Azure Portal.

1. Vykreslení zaznamenaných hodnot, které vidíte, se v pracovním prostoru automaticky vytvoří. Při každém zaprotokolování více hodnot se stejným parametrem názvu se pro vás automaticky vygeneruje diagram. Zde naleznete příklad:

   ![Zobrazení historie](./media/quickstart-run-cloud-notebook/web-results.png)

Vzhledem k tomu, že kód pro aproximaci hodnoty PI používá náhodné hodnoty, vaše parcely mohou vypadat jinak.  

## <a name="clean-up-resources"></a>Vyčištění prostředků

### <a name="stop-the-notebook-vm"></a>Zastavení virtuálního počítače poznámkového bloku

Zastavte virtuální počítač poznámkového bloku, pokud ho nepoužíváte ke snížení nákladů.  

1. V pracovním prostoru vyberte **virtuální počítače poznámkového bloku**.

   ![Zastavení serveru VM](./media/quickstart-run-cloud-notebook/stop-server.png)

1. V seznamu vyberte virtuální počítač.

1. Vyberte **zastavit**.

1. Až budete chtít znovu použít server, vyberte **Spustit**.

### <a name="delete-everything"></a>Odstranit vše

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Můžete také zachovat skupinu prostředků, ale odstranit jeden pracovní prostor. Zobrazte vlastnosti pracovního prostoru a vyberte **Odstranit**.

## <a name="next-steps"></a>Další postup

Po dokončení těchto úloh přejdete na webovou stránku Jupyter Notebook. Ve složce pro **rychlý Start** otevřete a spusťte Poznámkový blok **02. deploy-Web-Service. ipynb** , kde se dozvíte, jak nasadit webovou službu.

Pokud chcete do prostředí Jupyter nainstalovat další balíčky Pythonu, použijte tento kód v poznámkovém bloku:

```
!source activate py36 && pip install <packagename>
```

Na webové stránce Jupyter Notebook můžete procházet dalšími poznámkami ve složce Samples, kde najdete další informace o službě Azure Machine Learning.

Podrobné prostředí pro práci s pracovními postupy najdete v Machine Learning výukových kurzů pro výuku a nasazení modelu:  

> [!div class="nextstepaction"]
> [Kurz: Výuka modelu klasifikace obrázků](tutorial-train-models-with-aml.md)
