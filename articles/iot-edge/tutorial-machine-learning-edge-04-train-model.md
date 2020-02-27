---
title: 'Kurz: výuka a nasazení modelu-Machine Learning na Azure IoT Edge'
description: V tomto kurzu vytvoříte model strojového učení pomocí Azure Machine Learning a pak zabalíte model jako image kontejneru, která se dá nasadit jako Azure IoT Edge modul.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 2/10/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a5c754373ba9437c631e62acbb5d6d246db4c862
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650754"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Kurz: výuka a nasazení Azure Machine Learningho modelu

> [!NOTE]
> Tento článek je součástí série, kde najdete kurz použití Azure Machine Learning v IoT Edge. Pokud jste dorazili přímo do tohoto článku, doporučujeme začít s [prvním článkem](tutorial-machine-learning-edge-01-intro.md) řady, abyste dosáhli nejlepších výsledků.

V tomto článku provedeme následující úlohy:

* Pomocí Azure Notebooks můžete naučit model strojového učení.
* Zabalit model trained jako image kontejneru.
* Nasaďte image kontejneru jako modul Azure IoT Edge.

Azure Notebooks využít pracovní prostor Azure Machine Learning, což je základní blok, který se používá k experimentování, výuce a nasazování modelů strojového učení.

Kroky v tomto článku můžou obvykle provádět odborníci přes data.

## <a name="set-up-azure-notebooks"></a>Nastavit Azure Notebooks

Používáme Azure Notebooks k hostování dvou poznámkových bloků Jupyter a podpůrných souborů. Tady vytvoříme a nakonfigurujeme Azure Notebooks projekt. Pokud jste nepoužívali Jupyter a/nebo Azure Notebooks, je zde několik úvodních dokumentů:

* **Rychlý Start:** [Vytvoření a sdílení poznámkového bloku](../notebooks/quickstart-create-share-jupyter-notebook.md)
* **Kurz:** [Vytvoření a spuštění poznámkového bloku Jupyter pomocí Pythonu](../notebooks/tutorial-create-run-jupyter-notebook.md)

Použití poznámkových bloků Azure zajišťuje konzistentní prostředí pro cvičení.

> [!NOTE]
> Po nastavení se k Azure Notebooks službě dostanete z libovolného počítače. Během instalace byste měli použít vývojový virtuální počítač, který bude mít všechny soubory, které budete potřebovat.

### <a name="create-an-azure-notebooks-account"></a>Vytvoření účtu Azure Notebooks

Chcete-li použít Azure Notebooks, je nutné vytvořit účet. Účty poznámkového bloku Azure jsou nezávislé na předplatných Azure.

1. Přejděte do [poznámkových bloků Azure](https://notebooks.azure.com).

1. V pravém horním rohu stránky klikněte na **Přihlásit** se.

1. Přihlaste se pomocí svého pracovního nebo školního účtu (Azure Active Directory) nebo svého osobního účtu (účet Microsoft).

1. Pokud jste Azure Notebooks ještě nepoužili, budete vyzváni k udělení přístupu pro aplikaci Azure Notebooks.

1. Vytvořte ID uživatele pro Azure Notebooks.

### <a name="upload-jupyter-notebook-files"></a>Nahrání souborů Jupyter poznámkového bloku

Do nového projektu Azure Notebooks budeme nahrávat ukázkové soubory poznámkových bloků.

1. Na stránce uživatele nového účtu v horním řádku nabídek vyberte **Moje projekty** .

1. V dialogovém okně **vytvořit nový projekt** zadejte **název projektu** , který také automaticky vytvoří **ID projektu**.

1. Ponechte **veřejné** a **soubor Readme** nezaškrtnuté, protože není potřeba, aby projekt byl veřejný nebo měl soubor Readme.

1. Vyberte **Vytvořit**.

1. Vyberte **nahrát** (ikona šipky nahoru) a zvolte **z počítače**.

1. Vyberte možnost **zvolit soubory**.

1. Přejděte na **C:\source\IoTEdgeAndMlSample\AzureNotebooks**. Vyberte všechny soubory v seznamu a klikněte na **otevřít**.

1. Vyberte **nahrát** a začněte nahrávat a potom vyberte **Hotovo** po dokončení procesu.

### <a name="azure-notebook-files"></a>Soubory notebooků Azure

Pojďme se podívat na soubory, které jste nahráli do projektu Azure Notebooks. Aktivity v této části kurzu jsou rozloženy mezi dva soubory poznámkového bloku, které používají několik podpůrných souborů.

* **01 – turbofan\_regrese. ipynb:** Tento Poznámkový blok používá pracovní prostor služby Machine Learning k vytvoření a spuštění experimentu machine learningu. Poznámkový blok v podstatě provede následující kroky:

  1. Stáhne data z Azure Storage účtu, který vygenerovalo předaný svazek zařízení.
  1. Prozkoumá a připraví data a pak data použije ke školení modelu třídění.
  1. Vyhodnoťte model z experimentu pomocí testovací datové sady (test\_FD003. txt).
  1. Publikuje nejlepší model třídění v pracovním prostoru služby Machine Learning.

* **02 – turbofan\_nasazení\_modelu. ipynb:** Tento Poznámkový blok získá model vytvořený v předchozím poznámkovém bloku a použije ho k vytvoření image kontejneru, která je připravená k nasazení do zařízení Azure IoT Edge. Poznámkový blok provede následující kroky:

  1. Vytvoří skript bodování pro model.
  1. Vytvoří Image kontejneru pomocí modelu klasifikátoru, který byl uložen v pracovním prostoru služby Machine Learning.
  1. Nasadí Image jako webovou službu ve službě Azure Container instance.
  1. Používá webovou službu k ověření modelu a image funguje podle očekávání. Ověřená bitová kopie se nasadí do našeho zařízení IoT Edge v části [Vytvoření a nasazení vlastních IoT Edge modulů](tutorial-machine-learning-edge-06-custom-modules.md) v tomto kurzu.

* **Test\_FD003. txt:** Tento soubor obsahuje data, která budeme používat jako naši sadu testů při ověřování našeho učeného třídění. Rozhodli jsme se používat testovací data, jak je k dispozici pro původní soutěž, jako naše testovací sada pro zjednodušení.

* **RUL\_FD003. txt:** Tento soubor obsahuje RUL pro poslední cyklus každého zařízení v souboru Test\_FD003. txt. Podrobné vysvětlení dat najdete v souboru Readme. txt a v článku C:\\source\\IoTEdgeAndMlSample\\data\\Turbofan.

* **Utils.py:** Obsahuje sadu funkcí nástrojů Pythonu pro práci s daty. První Poznámkový blok obsahuje podrobné vysvětlení funkcí.

* **Readme.MD:** Soubor Readme popisující použití poznámkových bloků.  

## <a name="run-azure-notebooks"></a>Spustit Azure Notebooks

Teď, když je projekt vytvořený, můžete spustit poznámkové bloky. 

1. Na stránce projektu vyberte **01-turbofan\_regrese. ipynb**.

    ![Vyberte první Poznámkový blok, který chcete spustit.](media/tutorial-machine-learning-edge-04-train-model/select-turbofan-regression-notebook.png)

1. Pokud se zobrazí výzva, zvolte jádro Python 3,6 z dialogového okna a vyberte **nastavit jádro**.

1. Pokud je Poznámkový blok uvedený jako **nedůvěryhodný**, klikněte v pravém horním rohu poznámkového bloku na widget **nedůvěryhodná** . Až se dialogové okno objeví, vyberte **důvěřovat**.

1. V poznámkovém bloku se posuňte dolů na buňku, která následuje za pokyny pro **Nastavení globálních vlastností** a která začíná kódem `AZURE_SUBSCRIPTION_ID =` a vyplní hodnoty pro vaše předplatné Azure, nastavení a prostředky.

    ![Nastavení globálních vlastností v poznámkovém bloku](media/tutorial-machine-learning-edge-04-train-model/set-global-properties.png)

1. Tuto buňku spustíte výběrem možnosti **Spustit** na panelu nástrojů.

    Když je buňka spuštěná, zobrazí se hvězdička mezi hranatými závorkami ([\*]). Po dokončení operace buňky je hvězdička nahrazena číslem a může se zobrazit relevantní výstup. Buňky v poznámkovém bloku se sestavují sekvenčně a v jednu chvíli může běžet jenom jedna.

    Postupujte podle pokynů v poznámkovém bloku. Můžete také použít možnosti spuštění z nabídky **buňka** `Ctrl` + `Enter` ke spuštění buňky a `Shift` + `Enter` ke spuštění buňky a přechodu na další buňku.

    > [!TIP]
    > V případě konzistentních operací s buňkami nepoužívejte stejný Poznámkový blok z více karet v prohlížeči.

1. Posuňte se dolů k buňce, která následuje po textu přehledu **vytvořit pracovní prostor** a tuto buňku spusťte. V výstupu buňky vyhledejte odkaz, který vám dává pokyn přihlásit se a ověřit. 

    ![Výzva k přihlášení pro ověřování zařízení](media/tutorial-machine-learning-edge-04-train-model/sign-in-prompt.png)

    Otevřete odkaz a zadejte zadaný kód. Tato procedura pro přihlášení ověřuje Poznámkový blok Jupyter pro přístup k prostředkům Azure pomocí rozhraní příkazového řádku Microsoft Azure pro různé platformy.  

    ![Potvrzení ověření aplikace na zařízení](media/tutorial-machine-learning-edge-04-train-model/cross-platform-cli.png)

1. V tomto okamžiku můžete spustit zbývající část buněk. Je optimální spouštět všechny buňky tak, aby se kód v buňkách spouštěl sekvenčně. V nabídce **buňka** vyberte **Spustit vše** . Posuňte se zpátky přes Poznámkový blok a Prohlédněte si, jak se operace s buňkami dokončily.

    V části **Prozkoumat data** si můžete prohlédnout buňky v podčásti **senzory čtení a RUL** , které vykreslují scatterplots měření senzorů.

    ![Scatterplots čtení snímačů](media/tutorial-machine-learning-edge-04-train-model/sensor-readings.png)

1. Uložte Poznámkový blok a vraťte se na stránku projektu kliknutím na název projektu v pravém horním rohu poznámkového bloku nebo v prohlížeči zpátky.

1. Otevřete **02-turbofan\_nasazení\_modelu. ipynb** a zopakováním kroků v tomto postupu spusťte druhý Poznámkový blok.

1. Uložte Poznámkový blok a vraťte se na stránku projektu kliknutím na název projektu v pravém horním rohu poznámkového bloku nebo v prohlížeči zpátky.

### <a name="verify-success"></a>Ověřit úspěch

Chcete-li ověřit, zda byly poznámkové bloky úspěšně dokončeny, ověřte, zda bylo vytvořeno několik položek.

1. Na stránce Azure Notebooks projektu vyberte možnost **Zobrazit skryté položky** tak, aby se zobrazily názvy položek začínající tečkou.

1. Ověřte, že byly vytvořeny následující soubory:

    | File | Popis |
    | --- | --- |
    | ./aml_config/.AzureML/config.JSON | Konfigurační soubor, který se používá k vytvoření pracovní prostor Azure Machine Learning. |
    | ./aml_config/model_config. JSON | Konfigurační soubor, který budeme potřebovat k nasazení modelu v pracovním prostoru **turbofanDemo** Machine Learning v Azure. |
    | MyENV. yml| Poskytuje informace o závislostech pro nasazený Machine Learning model.|

1. Ověřte v Azure Portal, že ve vaší skupině prostředků existuje pracovní prostor služby **turboFanDemo** Machine Learning.

### <a name="debugging"></a>Ladění

Do poznámkového bloku můžete odkrýt příkazy Pythonu pro ladění, hlavně příkaz `print()`. Pokud vidíte proměnné nebo objekty, které nejsou definovány, spusťte buňky, kde jsou nejprve deklarovány nebo vytvořeny instance.

## <a name="next-steps"></a>Další kroky

V tomto článku jsme použili dva poznámkové bloky Jupyter běžící v Azure Notebooks k tomu, abyste mohli využít data ze zařízení turbofan ke školení zbývajícího možného života (RUL) klasifikátoru, k vytvoření image kontejneru a k nasazení a otestování Image jako webu se rvice.

Pokračujte dalším článkem a vytvořte zařízení IoT Edge.

> [!div class="nextstepaction"]
> [Konfigurace zařízení IoT Edge](tutorial-machine-learning-edge-05-configure-edge-device.md)
