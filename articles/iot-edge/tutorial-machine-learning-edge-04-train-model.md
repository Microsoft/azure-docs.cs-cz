---
title: 'Kurz: výuka a nasazení modelu-Machine Learning na Azure IoT Edge'
description: V tomto kurzu vytvoříte model strojového učení pomocí Azure Machine Learning a pak zabalíte model jako image kontejneru, která se dá nasadit jako Azure IoT Edge modul.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/24/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 2cc96db88d9a2aec02de5e2fc4ed18b445972e7b
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121126"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Kurz: výuka a nasazení Azure Machine Learningho modelu

V tomto článku provedeme následující úlohy:

* Pomocí Azure Machine Learning Studio můžete naučit model strojového učení.
* Zabalit model trained jako image kontejneru.
* Nasaďte image kontejneru jako modul Azure IoT Edge.

Azure Machine Learning Studio je základní blok, který slouží k experimentování, výuce a nasazování modelů strojového učení.

Kroky v tomto článku můžou obvykle provádět odborníci přes data.

V této části kurzu se dozvíte, jak:

> [!div class="checklist"]
> * Vytvářejte Jupyter poznámkové bloky v pracovní prostor Azure Machine Learning, abyste mohli naučit model strojového učení.
> * Kontejnerizace se na školicí model strojového učení.
> * Vytvořte modul Azure IoT Edge z kontejneru strojového učení s dodaným objektem.

## <a name="prerequisites"></a>Požadavky

Tento článek je součástí série, kde najdete kurz použití Azure Machine Learning v IoT Edge. Každý článek v sérii vychází z práce v předchozím článku. Pokud jste dorazili přímo do tohoto článku, přejděte na [první článek](tutorial-machine-learning-edge-01-intro.md) v řadě.

## <a name="set-up-azure-machine-learning"></a>Nastavit Azure Machine Learning 

Používáme Azure Machine Learning Studio k hostování dvou poznámkových bloků Jupyter a podpůrných souborů. Tady vytvoříme a nakonfigurujeme Azure Machine Learning projekt. Pokud jste nepoužívali Jupyter a/nebo Azure Machine Learning Studio, je zde několik úvodních dokumentů:

* **Jupyter poznámkové bloky:** [práce s poznámkovými bloky Jupyter v Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support)
* **Azure Machine Learning:** [Začínáme s Azure Machine Learning v poznámkových blocích Jupyter](../machine-learning/tutorial-1st-experiment-sdk-setup.md)


> [!NOTE]
> Po nastavení se k Azure Machine Learning službě dostanete z libovolného počítače. Během instalace byste měli použít vývojový virtuální počítač, který bude mít všechny soubory, které budete potřebovat.

### <a name="install-azure-machine-learning-visual-studio-code-extension"></a>Nainstalovat rozšíření Azure Machine Learning Visual Studio Code
VS Code na vývojovém virtuálním počítači by měla mít tato rozšíření nainstalovaná. Pokud používáte jinou instanci, přeinstalujte prosím rozšíření, jak je popsáno [zde.](../machine-learning/tutorial-setup-vscode-extension.md)

### <a name="create-an-azure-machine-learning-account"></a>Vytvoření účtu Azure Machine Learning  
Aby bylo možné zřídit prostředky a spouštět úlohy v Azure, musíte se přihlásit pomocí přihlašovacích údajů k účtu Azure.

1. V Visual Studio Code otevřete paletu příkazů výběrem možnosti **Zobrazit**  >  **paleta příkazů** v řádku nabídek. 

1. Zadejte příkaz `Azure: Sign In` do palety příkazů pro spuštění procesu přihlášení. Dokončete přihlášení podle pokynů. 

1. Vytvořte instanci Azure Výpočetní prostředky služby ML pro spuštění úlohy. Pomocí příkazové palety zadejte příkaz `Azure ML: Create Compute` . 
1. Vyberte své předplatné Azure.
1. Vyberte **+ vytvořit nový pracovní prostor Azure ml** a zadejte název `turbofandemo` .
1. Vyberte skupinu prostředků, kterou jste pro tuto ukázku používali.
1. V pravém dolním rohu okna VS Code byste měli být schopní zobrazit průběh vytváření pracovního prostoru: **vytváření pracovního prostoru: turobofandemo** (může to trvat minutu nebo dvě). 
1. Počkejte prosím, než se pracovní prostor úspěšně vytvoří. Měl by se **vytvořit pracovní prostor Azure ml turbofandemo**.


### <a name="upload-jupyter-notebook-files"></a>Nahrání souborů Jupyter Notebook

Do nového pracovního prostoru Azure ML odešleme ukázkové soubory poznámkových bloků.

1. Přejděte na ml.azure.com a přihlaste se.
1. Vyberte svůj adresář Microsoft, předplatné Azure a nově vytvořený pracovní prostor Azure ML.

    :::image type="content" source="media/tutorial-machine-learning-edge-04-train-model/select-studio-workspace.png" alt-text="Vyberte pracovní prostor Azure ML." :::

1. Po přihlášení k pracovnímu prostoru Azure ML přejděte do části **poznámkové bloky** pomocí levé strany nabídky.
1. Vyberte kartu **Moje soubory** .

1. Vyberte **nahrát** (ikona šipky nahoru). 


1. Přejděte na **C:\source\IoTEdgeAndMlSample\AzureNotebooks**. Vyberte všechny soubory v seznamu a klikněte na **otevřít**.

1. Zaškrtněte políčko **důvěřovat obsahu těchto souborů** .

1. Vyberte **nahrát** a začněte nahrávat a potom vyberte **Hotovo** po dokončení procesu.

### <a name="jupyter-notebook-files"></a>Soubory Jupyter Notebook

Pojďme se podívat na soubory, které jste nahráli do pracovního prostoru Azure ML. Aktivity v této části kurzu jsou rozloženy mezi dva soubory poznámkového bloku, které používají několik podpůrných souborů.

* **01 – turbofan \_ regrese. ipynb:** tento poznámkový blok používá pracovní prostor služby Machine Learning k vytvoření a spuštění experimentu machine learningu. Poznámkový blok v podstatě provede následující kroky:

  1. Stáhne data z Azure Storage účtu, který vygenerovalo předaný svazek zařízení.
  1. Prozkoumá a připraví data a pak data použije ke školení modelu třídění.
  1. Vyhodnoťte model z experimentu pomocí testovací datové sady (testovací \_FD003.txt).
  1. Publikuje nejlepší model třídění v pracovním prostoru služby Machine Learning.

* **02 – turbofan \_ nasazení \_ modelu. ipynb:** tento poznámkový blok získá model vytvořený v předchozím poznámkovém bloku a použije ho k vytvoření image kontejneru, která je připravená k nasazení do zařízení Azure IoT Edge. Poznámkový blok provede následující kroky:

  1. Vytvoří skript bodování pro model.
  1. Vytvoří Image kontejneru pomocí modelu klasifikátoru, který byl uložen v pracovním prostoru služby Machine Learning.
  1. Nasadí Image jako webovou službu ve službě Azure Container instance.
  1. Používá webovou službu k ověření modelu a image funguje podle očekávání. Ověřená bitová kopie se nasadí do našeho zařízení IoT Edge v části [Vytvoření a nasazení vlastních IoT Edge modulů](tutorial-machine-learning-edge-06-custom-modules.md) v tomto kurzu.

* **Testovací \_FD003.txt:** tento soubor obsahuje data, která budeme používat jako naši sadu testů při ověřování našeho vyučeného třídění. Rozhodli jsme se používat testovací data, jak je k dispozici pro původní soutěž, jako naše testovací sada pro zjednodušení.

* **RUL \_FD003.txt:** tento soubor obsahuje zbývající dobu života (RUL) pro poslední cyklus každého zařízení v testovacím \_FD003.txt souboru. Podrobné vysvětlení dat najdete v tématu readme.txt a šíření poškození Modeling.pdf souborů ve službě C: \\ source \\ IoTEdgeAndMlSample \\ data \\ Turbofan.

* **Utils.py:** Obsahuje sadu funkcí nástrojů Pythonu pro práci s daty. První Poznámkový blok obsahuje podrobné vysvětlení funkcí.

* **Readme.MD:** Soubor Readme popisující použití poznámkových bloků.  

## <a name="run-jupyter-notebooks"></a>Spuštění poznámkových bloků Jupyter

Teď, když je pracovní prostor vytvořený, můžete spustit poznámkové bloky. 

1. Na stránce **Moje soubory** vyberte **01-turbofan \_ regrese. ipynb**.

    :::image type="content" source="media/tutorial-machine-learning-edge-04-train-model/select-turbofan-notebook.png" alt-text="Vyberte první Poznámkový blok, který chcete spustit. ":::

1. Pokud je Poznámkový blok uvedený jako **nedůvěryhodný**, klikněte v pravém horním rohu poznámkového bloku na widget **nedůvěryhodná** . Až se dialogové okno objeví, vyberte **důvěřovat**.

1. Pro dosažení nejlepších výsledků si přečtěte dokumentaci pro každou buňku a spusťte ji samostatně. Na panelu nástrojů vyberte **Spustit** . Později zjistíte, že pro spuštění více buněk bude vhodné. Můžete debrát v úvahu aktualizace a upozornění na zastarání.

    Když je buňka spuštěná, zobrazí se hvězdička v hranatých závorkách ([ \* ]). Po dokončení operace buňky je hvězdička nahrazena číslem a může se zobrazit relevantní výstup. Buňky v poznámkovém bloku se sestavují sekvenčně a v jednu chvíli může běžet jenom jedna.

    Můžete také použít možnosti spuštění z nabídky **buňka** , `Ctrl`  +  `Enter` Spustit buňku a `Shift`  +  `Enter` Spustit buňku a přejít na další buňku.

    > [!TIP]
    > V případě konzistentních operací s buňkami nepoužívejte stejný Poznámkový blok z více karet v prohlížeči.

1. Do buňky, která následuje za pokyny pro **Nastavení globálních vlastností** , zapište hodnoty pro vaše předplatné Azure, nastavení a prostředky. Pak buňku spusťte.

    ![Nastavení globálních vlastností v poznámkovém bloku](media/tutorial-machine-learning-edge-04-train-model/set-global-properties.png)

1. V případě, že se v buňce předchozí zobrazí **Podrobnosti o pracovním prostoru**, vyhledejte odkaz, který vám dává pokyn přihlásit se k ověřování:

    ![Výzva k přihlášení pro ověřování zařízení](media/tutorial-machine-learning-edge-04-train-model/sign-in-prompt.png)

    Otevřete odkaz a zadejte zadaný kód. Tato procedura pro přihlášení ověřuje Poznámkový blok Jupyter pro přístup k prostředkům Azure pomocí rozhraní příkazového řádku Microsoft Azure pro různé platformy.  

    ![Potvrzení ověření aplikace na zařízení](media/tutorial-machine-learning-edge-04-train-model/cross-platform-cli.png)

1. Do buňky, která předchází **výsledků prozkoumat**, zkopírujte hodnotu z ID běhu a vložte ji pro ID běhu do buňky, která následuje za **běhu**.

   ![Kopírovat ID běhu mezi buňkami](media/tutorial-machine-learning-edge-04-train-model/automl-id.png)

1. Spustí zbývající buňky v poznámkovém bloku.

1. Uložte Poznámkový blok a vraťte se na stránku projektu.

1. Otevřete **02-turbofan \_ nasazení \_ model. ipynb** a spusťte každou buňku. K ověření se budete muset přihlásit v buňce, která následuje po **konfiguraci pracovního prostoru**.

1. Uložte Poznámkový blok a vraťte se na stránku projektu.

### <a name="verify-success"></a>Ověřit úspěch

Chcete-li ověřit, zda byly poznámkové bloky úspěšně dokončeny, ověřte, zda bylo vytvořeno několik položek.

1. Na kartě **Moje soubory** ve vašich poznámkových blocích Azure ml vyberte **aktualizovat**.

1. Ověřte, že byly vytvořeny následující soubory:

    | Soubor | Description |
    | --- | --- |
    | ./aml_config/.AzureML/config.jsna | Konfigurační soubor, který se používá k vytvoření pracovní prostor Azure Machine Learning. |
    | ./aml_config/model_config.jsna | Konfigurační soubor, který budeme potřebovat k nasazení modelu v pracovním prostoru **turbofanDemo** Machine Learning v Azure. |
    | MyENV. yml| Poskytuje informace o závislostech pro nasazený Machine Learning model.|

1. Ověřte, že byly vytvořeny následující prostředky Azure. Některé názvy prostředků se připojují s náhodnými znaky.

    | Prostředek Azure | Name |
    | --- | --- |
    | Pracovní prostor Machine Learning | turborfanDemo |
    | Container Registry | turbofandemoxxxxxxxx |
    | Application Insights | turbofaninsightxxxxxxxx |
    | Key Vault | turbofankeyvaultbxxxxxxxx |
    | Storage | turbofanstoragexxxxxxxxx |

### <a name="debugging"></a>Ladění

Do poznámkového bloku můžete vkládat příkazy Pythonu pro ladění, jako je například `print()` příkaz pro zobrazení hodnot. Pokud vidíte proměnné nebo objekty, které nejsou definovány, spusťte buňky, kde jsou nejprve deklarovány nebo vytvořeny instance.

Pokud potřebujete opakovat poznámkové bloky, možná budete muset odstranit dříve vytvořené soubory a prostředky Azure.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Tento kurz je součástí sady, kde každý článek sestaví na práci, která se provádí v předchozích verzích. Počkejte prosím na vyčištění všech prostředků, dokud nedokončíte finální kurz.

## <a name="next-steps"></a>Další kroky

V tomto článku jsme použili dva poznámkové bloky Jupyter běžící v Azure ML Studio k tomu, abyste mohli využít data ze zařízení turbofan ke školení zbývajícího životního (RUL) klasifikátoru, k uložení klasifikátoru jako modelu, k vytvoření image kontejneru a k nasazení a otestování Image jako webové služby.

Pokračujte dalším článkem a vytvořte zařízení IoT Edge.

> [!div class="nextstepaction"]
> [Konfigurace zařízení IoT Edge](tutorial-machine-learning-edge-05-configure-edge-device.md)