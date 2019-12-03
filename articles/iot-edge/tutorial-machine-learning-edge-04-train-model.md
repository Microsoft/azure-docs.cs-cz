---
title: 'Kurz: výuka a nasazení modelu-Machine Learning na Azure IoT Edge'
description: V tomto kurzu vytvoříte model strojového učení pomocí Azure Machine Learning a pak zabalíte model jako image kontejneru, která se dá nasadit jako Azure IoT Edge modul.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9c0613d319c0c82fa61d75ed016d68d38dfcea8d
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74701827"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Kurz: výuka a nasazení Azure Machine Learningho modelu

> [!NOTE]
> Tento článek je součástí série, kde najdete kurz použití Azure Machine Learning v IoT Edge. Pokud jste dorazili přímo do tohoto článku, doporučujeme začít s [prvním článkem](tutorial-machine-learning-edge-01-intro.md) řady, abyste dosáhli nejlepších výsledků.

V tomto článku používáme Azure Notebooks nejprve ke studiu modelu strojového učení pomocí Azure Machine Learning a pak tento model zabalíte jako image kontejneru, která se dá nasadit jako modul Azure IoT Edge. Azure Notebooks využít pracovní prostor Azure Machine Learning, což je základní blok, který se používá k experimentování, výuce a nasazování modelů strojového učení.

Aktivity v této části kurzu se rozdělují mezi dva poznámkové bloky.

* **01 – turbofan\_regrese. ipynb:** Tento Poznámkový blok vás provede kroky ke výukě a publikování modelu pomocí Azure Machine Learning. V podstatě se jedná o následující kroky:

  1. Stažení, příprava a prozkoumání školicích dat
  2. Použití pracovního prostoru služby k vytvoření a spuštění experimentu machine learningu
  3. Vyhodnocení výsledků modelu z experimentu
  4. Publikování nejlepšího modelu do pracovního prostoru služby

* **02 – turbofan\_nasazení\_modelu. ipynb:** Tento Poznámkový blok získá model vytvořený v předchozím poznámkovém bloku a použije ho k vytvoření image kontejneru, která je připravená k nasazení do zařízení Azure IoT Edge.

  1. Vytvoření vyhodnocovacího skriptu pro model
  2. Vytvoření a publikování image
  3. Nasazení image jako webové služby ve službě Azure Container instance
  4. Použití webové služby k ověření modelu a image funguje podle očekávání

Kroky v tomto článku můžou obvykle provádět odborníci přes data.

## <a name="set-up-azure-notebooks"></a>Nastavit Azure Notebooks

Používáme Azure Notebooks k hostování dvou poznámkových bloků Jupyter a podpůrných souborů. Tady vytvoříme a nakonfigurujeme Azure Notebooks projekt. Pokud jste nepoužívali Jupyter a/nebo Azure Notebooks, je zde několik úvodních dokumentů:

* **Rychlý Start:** [Vytvoření a sdílení poznámkového bloku](../notebooks/quickstart-create-share-jupyter-notebook.md)
* **Kurz:** [Vytvoření a spuštění poznámkového bloku Jupyter pomocí Pythonu](../notebooks/tutorial-create-run-jupyter-notebook.md)

Stejně jako u virtuálního počítače pro vývojáře se pomocí poznámkových bloků Azure zajišťuje konzistentní prostředí pro toto cvičení.

> [!NOTE]
> Po nastavení se k Azure Notebooks službě dostanete z libovolného počítače. Během instalace byste měli použít vývojový virtuální počítač, který bude mít všechny soubory, které budete potřebovat.

### <a name="create-an-azure-notebooks-account"></a>Vytvoření účtu Azure Notebooks

Účty poznámkového bloku Azure jsou nezávislé na předplatných Azure. Chcete-li použít Azure Notebooks, je nutné vytvořit účet.

1. Přejděte do [poznámkových bloků Azure](https://notebooks.azure.com).

2. V pravém horním rohu stránky klikněte na **Přihlásit** .

3. Přihlaste se pomocí svého pracovního nebo školního účtu (Azure Active Directory) nebo svého osobního účtu (účet Microsoft).

4. Pokud jste Azure Notebooks ještě nepoužili, budete vyzváni k udělení přístupu pro aplikaci Azure Notebooks.

5. Vytvořte ID uživatele pro Azure Notebooks.

### <a name="upload-jupyter-notebooks-files"></a>Nahrání souborů Jupyter poznámkových bloků

V tomto kroku vytvoříme nový projekt Azure Notebooks a do něj nahrajeme soubory. Konkrétně soubory, které odesíláme:

* **01 – turbofan\_regrese. ipynb**: soubor poznámkového bloku Jupyter, který vás provede procesem stahování dat vygenerovaných ze zařízení z účtu služby Azure Storage. zkoumání a Příprava dat pro školení třídění; školení modelu; testování dat pomocí testovací datové sady nalezené v souboru Test\_FD003. txt; a nakonec uložte model klasifikátoru v pracovním prostoru služby Machine Learning.

* **02 – turbofan\_nasazení\_modelu. ipynb:** Jupyter Poznámkový blok, který vás provede procesem použití modelu klasifikátoru uloženého v pracovním prostoru služby Machine Learning k vytvoření image kontejneru. Když se image vytvoří, Poznámkový blok vás provede procesem nasazení image jako webové služby, abyste mohli ověřit, jestli funguje podle očekávání. Ověřená bitová kopie se nasadí do našeho zařízení IoT Edge v části [Vytvoření a nasazení vlastních IoT Edge modulů](tutorial-machine-learning-edge-06-custom-modules.md) v tomto kurzu.

* **Test\_FD003. txt:** Tento soubor obsahuje data, která budeme používat jako naši sadu testů při ověřování našeho učeného třídění. Rozhodli jste se použít testovací data, která jsou k dispozici pro původní soutěž jako naši sadu testů pro jednoduchost příkladu.

* **RUL\_FD003. txt:** Tento soubor obsahuje RUL pro poslední cyklus každého zařízení v souboru Test\_FD003. txt. Podrobné vysvětlení dat najdete v **souboru Readme. txt** **a v článku** C:\\source\\IoTEdgeAndMlSample\\data\\Turbofan.

* **Utils.py:** Obsahuje sadu funkcí nástrojů Pythonu pro práci s daty. První Poznámkový blok obsahuje podrobné vysvětlení funkcí.

* **Readme.MD:** Soubor Readme popisující použití poznámkových bloků.

Vytvořte nový projekt a nahrajte soubory do poznámkového bloku.

1. V horním řádku nabídek vyberte **Moje projekty** .

1. Vyberte **+ Nový projekt**. Zadejte název a ID. Není nutné, aby projekt byl veřejný nebo měl soubor Readme.

1. Vyberte **nahrát** a zvolte **z počítače**.

1. Vyberte možnost **zvolit soubory**.

1. Přejděte na **C:\source\IoTEdgeAndMlSample\AzureNotebooks**. Vyberte všechny soubory a klikněte na **otevřít**.

1. Vyberte **nahrát** a začněte nahrávat a potom vyberte **Hotovo** po dokončení procesu.

## <a name="run-azure-notebooks"></a>Spustit Azure Notebooks

Teď, když se projekt vytvoří, spusťte Poznámkový blok **01-turbofan\_regrese ipynb** .

1. Na stránce projektu turbofan vyberte **01-turbofan\_regrese. ipynb**.

    ![Vyberte první Poznámkový blok, který chcete spustit.](media/tutorial-machine-learning-edge-04-train-model/select-turbofan-regression-notebook.png)

2. Pokud se zobrazí výzva, zvolte jádro Python 3,6 z dialogového okna a vyberte **nastavit jádro**.

3. Pokud je Poznámkový blok uvedený jako **nedůvěryhodný**, klikněte v pravém horním rohu poznámkového bloku na widget **nedůvěryhodná** . Až se dialogové okno objeví, vyberte **důvěřovat**.

4. Postupujte podle pokynů v poznámkovém bloku.

    * `Ctrl + Enter` spustí buňku.
    * `Shift + Enter` spustí buňku a přejde na další buňku.
    * Když je buňka spuštěná, obsahuje hvězdičku mezi hranatými závorkami, jako je například **[\*]** . Po dokončení bude hvězdička nahrazena číslem a příslušný výstup může být uveden níže. Vzhledem k tomu, že se buňky často sestavují na práci na předchozích verzích, může běžet současně pouze jedna buňka.

5. Po dokončení práce s poznámkovým blokem **01-turbofan\_regrese ipynb** se vraťte na stránku projektu.

6. Otevřete **02-turbofan\_nasazení\_modelu. ipynb** a zopakováním kroků v této části spusťte druhý Poznámkový blok.

## <a name="next-steps"></a>Další kroky

V tomto článku jsme použili dva poznámkové bloky Jupyter běžící v Azure Notebooks k tomu, abyste mohli využít data ze zařízení turbofan ke školení zbývajícího možného života (RUL) klasifikátoru, k vytvoření image kontejneru a k nasazení a otestování Image jako webu se rvice.

Pokračujte dalším článkem a vytvořte zařízení IoT Edge.

> [!div class="nextstepaction"]
> [Konfigurace zařízení IoT Edge](tutorial-machine-learning-edge-05-configure-edge-device.md)
