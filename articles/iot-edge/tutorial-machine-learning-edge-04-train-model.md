---
title: 'Kurz: Trénování a nasazování modelu – Machine Learning na Azure IoT Edge'
description: V tomto kurzu budete trénovat model strojového učení pomocí Azure Machine Learning a pak balíček modelu jako image kontejneru, který lze nasadit jako modul Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/24/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 57630b789233dd23e61398f445b434e4ba08b48e
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2020
ms.locfileid: "80236032"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Kurz: Trénování a nasazování modelu Azure Machine Learning

> [!NOTE]
> Tento článek je součástí řady pro kurz o používání Azure Machine Learning na IoT Edge. Pokud jste k tomuto článku dorazili přímo, doporučujeme vám začít s [prvním článkem](tutorial-machine-learning-edge-01-intro.md) v sérii pro dosažení nejlepších výsledků.

V tomto článku děláme následující úkoly:

* K trénování modelu strojového učení použijte poznámkové bloky Azure.
* Balíček trénovaný model jako image kontejneru.
* Nasaďte image kontejneru jako modul Azure IoT Edge.

Poznámkové bloky Azure využívají pracovní prostor Azure Machine Learning, základní blok používaný k experimentování, trénování a nasazování modelů strojového učení.

Kroky v tomto článku může být obvykle provádí vědci dat.

## <a name="set-up-azure-notebooks"></a>Nastavení poznámkových bloků Azure

Poznámkové bloky Azure používáme k hostování dvou poznámkových bloků Jupyter a podpůrných souborů. Tady vytvoříme a nakonfigurujeme projekt poznámkových bloků Azure. Pokud jste nepoužili Jupyter a/nebo Poznámkové bloky Azure, tady je několik úvodních dokumentů:

* **Úvodní příručka:** [Vytvoření a sdílení poznámkového bloku](../notebooks/quickstart-create-share-jupyter-notebook.md)
* **Kurz:** [Vytvoření a spuštění poznámkového bloku Jupyter s Pythonem](../notebooks/tutorial-create-run-jupyter-notebook.md)

Používání poznámkových bloků Azure zajišťuje konzistentní prostředí pro cvičení.

> [!NOTE]
> Po nastavení je služba Poznámkových bloků Azure přístupná z libovolného počítače. Během instalace byste měli použít vývojový virtuální počítač, který má všechny soubory, které budete potřebovat.

### <a name="create-an-azure-notebooks-account"></a>Vytvoření účtu poznámkových bloků Azure

Pokud chcete používat poznámkové bloky Azure, musíte si vytvořit účet. Účty poznámkových bloků Azure jsou nezávislé na předplatných Azure.

1. Přejděte na [poznámkové bloky Azure](https://notebooks.azure.com).

1. V pravém horním rohu stránky klikněte na **Přihlásit.**

1. Přihlaste se pomocí pracovního nebo školního účtu (Azure Active Directory) nebo osobního účtu (účet Microsoft).

1. Pokud jste poznámkové bloky Azure ještě nepoužívali, budete vyzváni k udělení přístupu pro aplikaci Poznámkové bloky Azure.

1. Vytvořte ID uživatele pro poznámkové bloky Azure.

### <a name="upload-jupyter-notebook-files"></a>Nahrání souborů poznámkového bloku Jupyter

Nahrajeme ukázkové soubory poznámkového bloku do nového projektu poznámkových bloků Azure.

1. Na uživatelské stránce nového účtu vyberte v horním řádku nabídek položku **Moje projekty.**

1. Přidejte nový projekt výběrem **+** tlačítka.

1. V dialogovém okně **Vytvořit nový projekt** zadejte název **projektu**. 

1. Ponechat **veřejné** a **README** nezaškrtnuté, protože není nutné, aby projekt byl veřejný nebo měl readme.

1. Vyberte **Vytvořit**.

1. Vyberte **Nahrát** (ikona šipky nahoru) a zvolte **Z počítače**.

1. Vyberte **Vybrat soubory**.

1. Přejděte na **c:\source\IoTEdgeAndMlSample\AzureNotebooks**. Vyberte všechny soubory v seznamu a klepněte na **tlačítko Otevřít**.

1. Zaškrtněte **políčko Důvěřovat obsahu těchto souborů.**

1. Chcete-li začít nahrávat, vyberte **Nahrát** a po dokončení procesu vyberte **Hotovo.**

### <a name="azure-notebook-files"></a>Soubory poznámkového bloku Azure

Podívejme se na soubory, které jste nahráli do projektu poznámkových bloků Azure. Aktivity v této části kurzu se rozprostírají ve dvou souborech poznámkového bloku, které používají několik podpůrných souborů.

* **01-turbodmychadlo\_regression.ipynb:** Tento poznámkový blok používá pracovní prostor služby Machine Learning k vytvoření a spuštění experimentu strojového učení. V podstatě poznámkový blok provádí následující kroky:

  1. Stáhne data z účtu Azure Storage, který byl vygenerován pomocí využití zařízení.
  1. Prozkoumá a připraví data a potom data použije k trénování modelu třídění.
  1. Vyhodnoťte model z experimentu pomocí testovací datové sady (Test\_FD003.txt).
  1. Publikuje model nejlepší třídění do pracovního prostoru služby Machine Learning.

* **02-turbodmychadlo\_nasadit\_model.ipynb:** Tento poznámkový blok vezme model vytvořený v předchozím poznámkovém bloku a použije ho k vytvoření image kontejneru připravené k nasazení na zařízení Azure IoT Edge. Poznámkový blok provádí následující kroky:

  1. Vytvoří bodovací skript pro model.
  1. Vytvoří image kontejneru pomocí modelu třídění, který byl uložen v pracovním prostoru služby Machine Learning.
  1. Nasadí image jako webovou službu v instanci kontejneru Azure.
  1. Používá webovou službu k ověření modelu a image práce podle očekávání. Ověřená image se nasadí do našeho zařízení IoT Edge v části tohoto kurzu [Vytvořit a nasadit vlastní moduly IoT Edge.](tutorial-machine-learning-edge-06-custom-modules.md)

* **Test\_FD003.txt:** Tento soubor obsahuje data, která použijeme jako testovací sadu při ověřování našeho trénovaného třídění. Rozhodli jsme se použít testovací data, jak je stanoveno pro původní soutěž, jako naši testovací sadu pro její jednoduchost.

* **RUL\_FD003.txt:** Tento soubor obsahuje zbývající životnost (RUL) pro poslední cyklus každého zařízení v souboru Test\_FD003.txt. Podrobné vysvětlení dat\\naleznete v\\\\\\souborech readme.txt a Damage.

* **Utils.py:** Obsahuje sadu funkcí nástroje Pythonpro práci s daty. První poznámkový blok obsahuje podrobné vysvětlení funkcí.

* **README.md:** Readme popisující použití poznámkových bloků.  

## <a name="run-azure-notebooks"></a>Spuštění poznámkových bloků Azure

Teď, když je projekt vytvořen, můžete spustit poznámkové bloky. 

1. Na stránce projektu vyberte **01-turbofan\_regression.ipynb**.

    ![Výběr prvního poznámkového bloku ke spuštění](media/tutorial-machine-learning-edge-04-train-model/select-turbofan-regression-notebook.png)

1. Pokud je poznámkový blok uveden jako **Nedůvěryhodný**, klikněte v pravém horním rohu poznámkového bloku na widget **Nedůvěryhodný.** Až se dialogové okno objeví, vyberte **Důvěřovat**.

1. Nejlepších výsledků dosáhnete, přečtěte si dokumentaci pro každou buňku a spusťte ji jednotlivě. Na panelu nástrojů vyberte **Spustit.** Později zjistíte, že je vhodné spustit více buněk. Můžete ignorovat upozornění na upgrade a vyřazení.

    Když je buňka spuštěna, zobrazuje hvězdičku mezi hranatými závorkami ([\*]). Po dokončení operace buňky je hvězdička nahrazena číslem a může se zobrazit příslušný výstup. Buňky v poznámkovém bloku sestavení postupně a pouze jeden může být spuštěn v době.

    Můžete také použít možnosti spuštění `Ctrl`  +  `Enter` z nabídky `Shift`  +  `Enter` **Buňka,** spustit buňku a spustit buňku a převést na další buňku.

    > [!TIP]
    > U konzistentních operací s buňkami se vyhněte spuštění stejného poznámkového bloku z více karet v prohlížeči.

1. Do buňky, která následuje za pokyny **pro globální vlastnosti** set, napište hodnoty pro předplatné Azure, nastavení a prostředky. Tak projej celu.

    ![Nastavení globálních vlastností v poznámkovém bloku](media/tutorial-machine-learning-edge-04-train-model/set-global-properties.png)

1. V buňce před poslední do **podrobnosti pracovního prostoru**vyhledejte po spuštění odkaz, který vás instruuje k přihlášení k ověření:

    ![Výzva k přihlášení k ověření zařízení](media/tutorial-machine-learning-edge-04-train-model/sign-in-prompt.png)

    Otevřete odkaz a zadejte zadaný kód. Tento postup přihlášení ověřuje poznámkový blok Jupyter pro přístup k prostředkům Azure pomocí rozhraní Microsoft Azure Cross-Platform Command Line Interface Interface.  

    ![Ověření aplikace při potvrzení zařízení](media/tutorial-machine-learning-edge-04-train-model/cross-platform-cli.png)

1. V buňce, která předchází **Prozkoumat výsledky**, zkopírujte hodnotu z ID spuštění a vložte ji pro ID spuštění v buňce, která následuje **Reconstitute spustit**.

   ![Kopírování ID spuštění mezi buňkami](media/tutorial-machine-learning-edge-04-train-model/automl-id.png)

1. Spusťte zbývající buňky v poznámkovém bloku.

1. Uložte poznámkový blok a vraťte se na stránku projektu.

1. Otevřete **02-turbofan\_nasadit\_model.ipynb** a spustit každou buňku. K ověření v buňce, která následuje **Konfigurace pracovního prostoru**, se budete muset přihlásit.

1. Uložte poznámkový blok a vraťte se na stránku projektu.

### <a name="verify-success"></a>Ověření úspěšnosti

Chcete-li ověřit, zda byly poznámkové bloky úspěšně dokončeny, ověřte, zda bylo vytvořeno několik položek.

1. Na stránce projektu Poznámkových bloků Azure vyberte **Zobrazit skryté položky,** aby se zobrazily názvy položek začínající tečkou.

1. Ověřte, zda byly vytvořeny následující soubory:

    | File | Popis |
    | --- | --- |
    | ./aml_config/.azureml/config.json | Konfigurační soubor použitý k vytvoření pracovního prostoru Azure Machine Learning. |
    | ./aml_config/model_config.json | Konfigurační soubor, který budeme muset nasadit model v pracovním prostoru **turbofanDemo** Machine Learning v Azure. |
    | myenv.yml| Obsahuje informace o závislostech pro nasazený model Machine Learning.|

1. Ověřte, že byly vytvořeny následující prostředky Azure. Názvy některých prostředků jsou připojeny s náhodnými znaky.

    | Prostředek Azure | Name (Název) |
    | --- | --- |
    | Pracovní prostor strojového učení | turborfanDemo |
    | Container Registry | turbodmychadlodemoxxxxxxxx |
    | Přehledy aplikací | turbofaninsightxxxxxxxx |
    | Key Vault | turbofankeyvaultbxxxxxxxx |
    | Úložiště | turbofanstoragexxxxxxxxx |

### <a name="debugging"></a>Ladění

Příkazy Pythonu můžete vložit do poznámkového bloku `print()` pro ladění, například příkaz pro zobrazení hodnot. Pokud se zobrazí proměnné nebo objekty, které nejsou definovány, spusťte buňky, kde jsou nejprve deklarovány nebo instanci.

Možná budete muset odstranit dříve vytvořené soubory a prostředky Azure, pokud potřebujete znovu poznámkové bloky.

## <a name="next-steps"></a>Další kroky

V tomto článku jsme použili dva poznámkové bloky Jupyter spuštěné v poznámkových blocích Azure k použití dat ze zařízení turbodmychadla k trénování klasifikátoru zbývající životnosti (RUL), k uložení klasifikátoru jako modelu, k vytvoření image kontejneru a k nasazení a testování bitové kopie jako webové Služby.

Pokračujte k dalšímu článku a vytvořte zařízení IoT Edge.

> [!div class="nextstepaction"]
> [Konfigurace zařízení IoT Edge](tutorial-machine-learning-edge-05-configure-edge-device.md)
