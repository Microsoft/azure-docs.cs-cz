---
title: 'Kurz: výuka a nasazení modelu-Machine Learning na Azure IoT Edge'
description: V tomto kurzu vytvoříte model strojového učení pomocí Azure Machine Learning a pak zabalíte model jako image kontejneru, kterou můžete nasadit jako modul Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/24/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 16d5fe90be71f39d448e4c1ce10c0373f6bfc86c
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2021
ms.locfileid: "103463098"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Kurz: výuka a nasazení Azure Machine Learningho modelu

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

V tomto článku provedeme následující úlohy:

* Pomocí Azure Machine Learning Studio můžete naučit model strojového učení.
* Zabalit model trained jako image kontejneru.
* Nasaďte image kontejneru jako modul Azure IoT Edge.

Machine Learning Studio je základní blok, který se používá k experimentování, výuce a nasazování modelů strojového učení.

Kroky v tomto článku můžou obvykle provádět odborníci přes data.

V této části kurzu se dozvíte, jak:

> [!div class="checklist"]
> * Vytvářejte Jupyter poznámkové bloky v pracovním prostoru Azure Machine Learning a Naučte se model strojového učení.
> * Kontejnerizace se na školicí model strojového učení.
> * Vytvořte modul IoT Edge z kontejneru strojového učení s dodaným objektem.

## <a name="prerequisites"></a>Požadavky

Tento článek je součástí série, kde najdete kurz použití Machine Learning v IoT Edge. Každý článek v sérii vychází z práce v předchozím článku. Pokud jste dorazili přímo do tohoto článku, přečtěte si [první článek](tutorial-machine-learning-edge-01-intro.md) v řadě.

## <a name="set-up-azure-machine-learning"></a>Nastavit Azure Machine Learning

Používáme Machine Learning Studio k hostování dvou poznámkových bloků Jupyter a podpůrných souborů. Tady vytvoříme a nakonfigurujeme Machine Learning projekt. Pokud jste nepoužívali Jupyter nebo Machine Learning Studio, jsou zde dva úvodní dokumenty:

* **Jupyter notebook**: [práce s poznámkovým blokem Jupyter v Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support)
* **Azure Machine Learning**: [Začínáme s Azure Machine Learning v poznámkových blocích Jupyter](../machine-learning/tutorial-1st-experiment-sdk-setup.md)

> [!NOTE]
> Po nastavení služby se Machine Learning k nim dostanete z libovolného počítače. Během instalace byste měli použít vývojový virtuální počítač, který obsahuje všechny soubory, které budete potřebovat.

### <a name="install-azure-machine-learning-visual-studio-code-extension"></a>Nainstalovat rozšíření Azure Machine Learning Visual Studio Code

Visual Studio Code na vývojovém virtuálním počítači by měla mít tato rozšíření nainstalovaná. Pokud používáte jinou instanci, přeinstalujte rozšíření podle pokynů v tématu [nastavení rozšíření Visual Studio Code](../machine-learning/tutorial-setup-vscode-extension.md).

### <a name="create-an-azure-machine-learning-account"></a>Vytvoření účtu Azure Machine Learning

Pokud chcete zřídit prostředky a spouštět úlohy v Azure, přihlaste se pomocí svých přihlašovacích údajů k účtu Azure.

1. V Visual Studio Code otevřete paletu příkazů výběrem možnosti **Zobrazit**  >  **paleta příkazů** v řádku nabídek.

1. Zadáním příkazu na `Azure: Sign In` paletě příkazů spusťte proces přihlášení. Dokončete přihlášení podle pokynů.

1. Vytvořte instanci služby Machine Learning COMPUTE pro spuštění úlohy. Na paletě příkazů zadejte příkaz `Azure ML: Create Compute` .
1. Vyberte své předplatné Azure.
1. Vyberte **+ vytvořit novou službu Azure pracovní prostor ml** a zadejte název **turbofandemo**.
1. Vyberte skupinu prostředků, kterou jste pro tuto ukázku používali.
1. V pravém dolním rohu okna Visual Studio Code by se měl zobrazit průběh vytváření pracovního prostoru: **vytváření pracovního prostoru: turobofandemo**. Tento krok může trvat minutu nebo dvě.
1. Počkejte, až se pracovní prostor úspěšně vytvoří. Měl by se **vytvořit pracovní prostor Azure ml turbofandemo**.

### <a name="upload-jupyter-notebook-files"></a>Nahrání souborů Jupyter Notebook

Do nového pracovního prostoru Machine Learning nahrajeme ukázkové soubory poznámkových bloků.

1. Přejdete na ml.azure.com a přihlaste se.
1. Vyberte svůj adresář Microsoft, předplatné Azure a nově vytvořený Machine Learning pracovní prostor.

    :::image type="content" source="media/tutorial-machine-learning-edge-04-train-model/select-studio-workspace.png" alt-text="Snímek obrazovky zobrazující výběr pracovního prostoru Azure Machine Learning" :::

1. Až se přihlásíte k pracovnímu prostoru Machine Learning, přejděte do části **poznámkové bloky** pomocí nabídky na levé straně.
1. Vyberte kartu **Moje soubory** .

1. Vyberte **nahrát** (ikona šipky nahoru).

1. Přejít na **C:\source\IoTEdgeAndMlSample\AzureNotebooks**. Vyberte všechny soubory v seznamu a vyberte **otevřít**.

1. Zaškrtněte políčko **důvěřovat obsahu těchto souborů** .

1. Vyberte **Odeslat** a začněte nahrávat. Potom vyberte **Hotovo** po dokončení procesu.

### <a name="jupyter-notebook-files"></a>Soubory Jupyter Notebook

Pojďme se podívat na soubory, které jste nahráli do pracovního prostoru Machine Learning. Aktivity v této části kurzu jsou rozloženy mezi dva soubory poznámkového bloku, které používají několik podpůrných souborů.

* **01 – turbofan \_ regrese. ipynb**: Tento Poznámkový blok používá pracovní prostor Machine Learning k vytvoření a spuštění experimentu machine learningu. Poznámkový blok v podstatě provede následující kroky:

  1. Stáhne data z Azure Storage účtu, který vygenerovalo předaný svazek zařízení.
  1. Prozkoumá a připraví data a pak data použije ke školení modelu třídění.
  1. Vyhodnotí model z experimentu pomocí testovací datové sady (testovací \_FD003.txt).
  1. Publikuje nejlepší model třídění do pracovního prostoru Machine Learning.

* **02 – turbofan \_ nasazení \_ modelu. ipynb**: Tento Poznámkový blok získá model vytvořený v předchozím poznámkovém bloku a použije ho k vytvoření image kontejneru, která je připravená k nasazení do zařízení IoT Edge. Poznámkový blok provede následující kroky:

  1. Vytvoří skript bodování pro model.
  1. Vytvoří Image kontejneru pomocí modelu klasifikátoru, který byl uložen v pracovním prostoru Machine Learning.
  1. Nasadí Image jako webovou službu v Azure Container Instances.
  1. Používá webovou službu k ověření modelu a image funguje podle očekávání. Ověřená bitová kopie se nasadí do našeho zařízení IoT Edge v části [Vytvoření a nasazení vlastních IoT Edge modulů](tutorial-machine-learning-edge-06-custom-modules.md) v tomto kurzu.

* **Testovací \_FD003.txt**: Tento soubor obsahuje data, která budeme používat jako naši sadu testů, když Ověřujeme naše vyškolená třídění. Rozhodli jsme se používat testovací data, jak je k dispozici pro původní soutěž, jako naše testovací sada pro zjednodušení.
* **RUL \_FD003.txt**: Tento soubor obsahuje zbývající dobu života (RUL) pro poslední cyklus každého zařízení v testovacím \_FD003.txt souboru. Podrobné vysvětlení dat najdete v tématu readme.txt a šíření poškození Modeling.pdf souborů v C: \\ source \\ IoTEdgeAndMlSample \\ data \\ Turbofan.
* **Utils.py**: Tento soubor obsahuje sadu funkcí nástrojů Pythonu pro práci s daty. První Poznámkový blok obsahuje podrobné vysvětlení funkcí.
* **Readme.MD**: Tento soubor Readme popisuje použití poznámkových bloků.

## <a name="run-the-jupyter-notebooks"></a>Spuštění poznámkových bloků Jupyter

Teď, když je pracovní prostor vytvořený, můžete spustit poznámkové bloky.

1. Na stránce **Moje soubory** vyberte **01-turbofan \_ regrese. ipynb**.

    :::image type="content" source="media/tutorial-machine-learning-edge-04-train-model/select-turbofan-notebook.png" alt-text="Snímek obrazovky, který zobrazuje výběr prvního poznámkového bloku ke spuštění.":::

1. Pokud je Poznámkový blok uvedený jako **nedůvěryhodný**, vyberte v pravém horním rohu poznámkového bloku **nedůvěryhodnou** pomůcku. Jakmile se zobrazí dialogové okno, vyberte možnost **důvěřovat**.

1. Pro dosažení nejlepších výsledků si přečtěte dokumentaci pro každou buňku a spusťte ji samostatně. Na panelu nástrojů vyberte **Spustit** . Později zjistíte, že je vhodné spustit více buněk. Můžete debrát v úvahu aktualizace a upozornění na zastarání.

    Když je buňka spuštěná, zobrazí se hvězdička v hranatých závorkách ([ \* ]). Po dokončení operace buňky je hvězdička nahrazena číslem a může se zobrazit relevantní výstup. Buňky v poznámkovém bloku se postupně sestavují a v jednu chvíli může běžet jenom jedna buňka.

    V nabídce **buňka** můžete také použít možnosti spuštění. Vyberte **CTRL + ENTER** , aby se buňka spustila, a vyberte **SHIFT + ENTER** , aby se buňka spustila a přechod na další buňku.

    > [!TIP]
    > V případě konzistentních operací s buňkami nepoužívejte stejný Poznámkový blok z více karet v prohlížeči.

1. Do buňky, která následuje za pokyny pro **Nastavení globálních vlastností** , zadejte hodnoty pro vaše předplatné Azure, nastavení a prostředky. Pak buňku spusťte.

    ![Snímek obrazovky, který zobrazuje nastavení globálních vlastností v poznámkovém bloku](media/tutorial-machine-learning-edge-04-train-model/set-global-properties.png)

1. V případě, že se v buňce předchozí zobrazí **Podrobnosti o pracovním prostoru**, vyhledejte odkaz, který vám dává pokyn přihlásit se k ověřování.

    ![Snímek obrazovky, který zobrazuje výzvu k přihlášení k ověření zařízení.](media/tutorial-machine-learning-edge-04-train-model/sign-in-prompt.png)

    Otevřete odkaz a zadejte zadaný kód. Tato procedura pro přihlášení ověřuje Poznámkový blok Jupyter pro přístup k prostředkům Azure pomocí rozhraní příkazového řádku Microsoft Azure pro různé platformy.

    ![Snímek obrazovky, který ukazuje ověřování aplikace při potvrzení zařízení.](media/tutorial-machine-learning-edge-04-train-model/cross-platform-cli.png)

1. Do buňky, která předchází **výsledků prozkoumat**, zkopírujte hodnotu z ID běhu a vložte ji pro ID běhu do buňky, která následuje za **běhu**.

   ![Snímek obrazovky, který ukazuje kopírování ID běhu mezi buňkami.](media/tutorial-machine-learning-edge-04-train-model/automl-id.png)

1. Spustí zbývající buňky v poznámkovém bloku.

1. Uložte si Poznámkový blok a vraťte se na stránku projektu.

1. Otevřete **02-turbofan \_ nasazení \_ model. ipynb** a spusťte každou buňku. K ověření musíte se přihlásit v buňce, která následuje po **konfiguraci pracovního prostoru**.

1. Uložte si Poznámkový blok a vraťte se na stránku projektu.

### <a name="verify-success"></a>Ověřit úspěch

Chcete-li ověřit, zda byly poznámkové bloky úspěšně dokončeny, ověřte, zda bylo vytvořeno několik položek.

1. Na kartě **Moje soubory** ve Machine Learning poznámkovém bloku vyberte **aktualizovat**.

1. Ověřte, zda byly vytvořeny následující soubory.

    | Soubor | Description |
    | --- | --- |
    | ./aml_config/.AzureML/config.jsna | Konfigurační soubor, který se používá k vytvoření pracovního prostoru Machine Learning. |
    | ./aml_config/model_config.jsna | Konfigurační soubor, který budeme potřebovat k nasazení modelu v pracovním prostoru **turbofanDemo** Machine Learning v Azure. |
    | MyENV. yml| Poskytuje informace o závislostech pro nasazený Machine Learning model.|

1. Ověřte, že byly vytvořeny následující prostředky Azure. Některé názvy prostředků se připojují s náhodnými znaky.

    | Prostředek Azure | Name |
    | --- | --- |
    | Pracovní prostor služby Azure Machine Learning | turborfanDemo |
    | Azure Container Registry | turbofandemoxxxxxxxx |
    | Application Insights | turbofaninsightxxxxxxxx |
    | Azure Key Vault | turbofankeyvaultbxxxxxxxx |
    | Azure Storage | turbofanstoragexxxxxxxxx |

### <a name="debugging"></a>Ladění

Do poznámkového bloku můžete vkládat příkazy Pythonu pro ladění, jako je například `print()` příkaz pro zobrazení hodnot. Pokud vidíte proměnné nebo objekty, které nejsou definovány, spusťte buňky, ve kterých jsou nejprve deklarovány nebo vytvořeny instance.

Pokud potřebujete opakovat poznámkové bloky, možná budete muset odstranit dříve vytvořené soubory a prostředky Azure.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Tento kurz je součástí sady, kde každý článek sestaví na práci, která se provádí v předchozích verzích. Počkejte na vyčištění všech prostředků, dokud nedokončíte finální kurz.

## <a name="next-steps"></a>Další kroky

V tomto článku jsme použili dva poznámkové bloky Jupyter spuštěné v Machine Learning Studio k použití dat ze zařízení turbofan na:

- Výuka RUL klasifikátoru.
- Uložte klasifikátor jako model.
- Vytvořte image kontejneru.
- Nasaďte a otestujte Image jako webovou službu.

Pokračujte dalším článkem a vytvořte zařízení IoT Edge.

> [!div class="nextstepaction"]
> [Konfigurace zařízení IoT Edge](tutorial-machine-learning-edge-05-configure-edge-device.md)