---
title: Rychlý start spuštění poznámkového bloku v cloudu
titleSuffix: Azure Machine Learning service
description: Začínáme se službou Azure Machine Learning. Použijte server spravované poznámkového bloku v cloudu k vyzkoušení vašeho pracovního prostoru.  Váš pracovní prostor se základní blok v cloudu, který použijete k experimentovat, trénovat a nasazovat modely machine learningu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
author: sdgilley
ms.author: sgilley
ms.date: 03/21/2019
ms.custom: seodec18
ms.openlocfilehash: 761ecefa768914e0c27f46d37050743e4f1f60ff
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2019
ms.locfileid: "58362436"
---
# <a name="quickstart-use-a-cloud-based-notebook-server-to-get-started-with-azure-machine-learning"></a>Rychlý start: Začínáme s Azure Machine Learning pomocí serveru založené na cloudu poznámkového bloku

V tomto článku se pomocí poznámkových bloků Azure spustit kód, který je přihlášený služby Azure Machine Learning [pracovní prostor](concept-azure-machine-learning-architecture.md). Váš pracovní prostor se základní blok v cloudu, který použijete k experimentovat, trénovat a nasazovat modely strojového učení pomocí Machine Learning. 

V tomto rychlém startu se používají cloudové prostředky a nevyžaduje se žádná instalace. Místo toho použít vlastní prostředí, najdete v článku [rychlý start: Začínáme s Azure Machine Learning pomocí serveru Poznámkový blok](quickstart-run-local-notebook.md).  
 
V tomto rychlém startu jste pomocí následujících kroků:

* Připojení k vašemu pracovnímu prostoru s Pythonem v poznámkovém bloku Jupyter. Poznámkový blok obsahuje kód pro odhad číslo pí a protokoly chyb v jednotlivých iteracích. 
* Zobrazte zaznamenané chybové hodnoty v pracovním prostoru.

Pokud nemáte předplatné Azure, vytvořte si bezplatný účet, před zahájením. Zkuste [bezplatné nebo placené verzi aplikace služby Azure Machine Learning](https://aka.ms/AMLFree) ještě dnes.

## <a name="prerequisite"></a>Požadavek

1. [Vytvoření pracovního prostoru Azure Machine Learning](setup-create-workspace.md#portal) pokud ho nemáte.

1. Otevření pracovního prostoru v [webu Azure portal](https://portal.azure.com/).  V tématu Jak [najít váš pracovní prostor](how-to-manage-workspace.md#view).

## <a name="use-your-workspace"></a>Použití vašeho pracovního prostoru

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2F9Ad]



Zjistěte, jak pracovní prostor vám pomůže spravovat vaše skriptů strojového učení. V této části provedete následující kroky:

* Otevřete poznámkový blok ve službě Azure Notebooks.
* Spustíte kód, který vytvoří zaprotokolované hodnoty.
* Zobrazíte zaprotokolované hodnoty ve vašem pracovním prostoru.

Tento příklad ukazuje, jak vám pracovní prostor může pomoct udržovat si přehled o informacích generovaných ve skriptu. 

### <a name="open-a-notebook"></a>Otevření poznámkového bloku 

[Azure Notebooks](https://notebooks.azure.com) poskytuje bezplatná Cloudová platforma pro poznámkové bloky Jupyter, které je předem nakonfigurován s všechno, co potřebujete ke spuštění Machine Learning. Z pracovního prostoru můžete spustit tuto platformu, abyste mohli začít používat váš pracovní prostor služby Azure Machine Learning.

1. Na stránce Přehled pracovního prostoru, vyberte **získat začít Azure poznámkových bloků** vyzkoušet prvního experimentu v Azure poznámkových bloků.  Poznámkových bloků Azure je samostatná služba, která umožňuje spouštět poznámkové bloky Jupyter zdarma v cloudu.  Pokud použijete tento odkaz na službu, informace o tom, jak se připojit k vašemu pracovnímu prostoru se přidají do knihovny, kterou jste vytvořili v poznámkových bloků Azure.

   ![Prozkoumejte pracovního prostoru](./media/quickstart-run-cloud-notebook/explore-aml.png)

1. Přihlaste se k Azure poznámkových bloků.  Ujistěte se, že se že přihlásíte pomocí stejného účtu, který jste použili pro přihlášení na webu Azure portal. Vaše organizace může před přihlášením vyžadovat [souhlas správce](https://notebooks.azure.com/help/signing-up/work-or-school-account/admin-consent).

1. Po přihlášení se otevře nová karta a zobrazí se výzva `Clone Library`. Klonování tuto knihovnu načte sadu poznámkových bloků a jiných souborů do účtu poznámkových bloků Azure.  Tyto soubory můžete prozkoumat možnosti služby Azure Machine Learning.

1. Zrušte zaškrtnutí políčka **veřejné** tak, aby informace o pracovním prostoru není sdílet s ostatními.

1. Vyberte **klonování**.

   ![Klonovat do knihovny](./media/quickstart-run-cloud-notebook/clone.png)

1. Pokud se zobrazí, že v zastaveném stavu projektu, klikněte na **zdarma v počítačích** používat server zdarma poznámkového bloku.

    ![Spuštění projektu na bezplatnými výpočetními funkcemi](./media/quickstart-run-cloud-notebook/run-project.png)

### <a name="run-the-notebook"></a>Spuštění poznámkového bloku

V seznamu souborů pro tento projekt, se zobrazí `config.json` souboru. Tento konfigurační soubor obsahuje informace o pracovním prostoru, kterou jste vytvořili na webu Azure Portal.  Tento soubor umožňuje váš kód k připojení a přidání informací do pracovního prostoru.

1. Vyberte **01.run experiment.ipynb** otevřete Poznámkový blok.

1. Oblasti stav zjistíte Počkejte, až jádro byla spuštěna.  Zpráva zmizí, jakmile je jádro připraveno.

    ![Vyčkat, než jádra spuštění](./media/quickstart-run-cloud-notebook/wait-for-kernel.png)

1. Po spuštění jádra spuštění jednotlivé buňky v čase pomocí **Shift + Enter**. Nebo vyberte **buňky** > **spustit všechny** celý poznámkový blok spustit. Když se zobrazí hvězdička, __*__, vedle buňky, je stále spuštěna buňku. Po dokončení kódu v této buňce se zobrazí číslo. 

1. Postupujte podle pokynů v poznámkovém bloku k ověření vašeho předplatného Azure.

Po dokončení spuštění všechny buňky v poznámkovém bloku, zaznamenané hodnoty lze zobrazit v pracovním prostoru.

## <a name="view-logged-values"></a>Zobrazení zaprotokolovaných hodnot

1. Výstup `run` buňka obsahuje odkaz zpět na webu Azure portal pro zobrazení výsledků testu ve vašem pracovním prostoru. 

    ![Zobrazení experimentů](./media/quickstart-run-cloud-notebook/view-exp.png)

1. Klikněte na tlačítko **odkaz na webu Azure portal** zobrazíte informace o daném spuštění ve vašem pracovním prostoru.  Tento odkaz otevře pracovní prostor na webu Azure Portal.

1. Vykreslení zaznamenané hodnoty, které se zobrazí automaticky vytvořené v pracovním prostoru. Při každém zaprotokolování více hodnot se stejným parametrem názvu se pro vás automaticky vygeneruje diagram.

   ![Zobrazení historie](./media/quickstart-run-cloud-notebook/web-results.png)

Protože kód přibližné PI použije náhodné hodnoty, se zobrazí vaše vykreslení různé hodnoty.  

## <a name="clean-up-resources"></a>Vyčištění prostředků 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Můžete také zachovat skupinu prostředků, ale odstranění jednoho pracovního prostoru. Zobrazení vlastností pracovního prostoru a vyberte **odstranit**.

## <a name="next-steps"></a>Další postup

Vytvořili jste prostředky potřebné k experimentování s modely a jejich nasazení. Také jste spustili kód v poznámkovém bloku. Kromě toho jste prozkoumali historii spuštění z tohoto kódu ve vašem pracovním prostoru v cloudu.

Podrobný pracovní postup prostředí postupujte podle kurzů strojového učení a trénování a nasadit model:  

> [!div class="nextstepaction"]
> [Kurz: Trénování modelu klasifikace obrázků](tutorial-train-models-with-aml.md)
