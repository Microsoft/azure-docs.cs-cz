---
title: Trénování a nasazení modelu – Machine Learning v Azure IoT Edge | Dokumentace Microsoftu
description: Trénování modelu strojového učení pomocí Azure Machine Learning a pak balíček modelu jako image kontejneru, který je možné nasadit jako modul služby Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: af91dc09f8ed68d7bd4f2378c13d99eb67c52e9e
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155645"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Kurz: Trénování a nasazení modelu služby Azure Machine Learning

> [!NOTE]
> Tento článek je součástí série kurz o používání Azure Machine learningu na hraničních zařízeních IoT. Pokud jste jste dostali přímo v tomto článku, doporučujeme vám začneme [nejprve článek](tutorial-machine-learning-edge-01-intro.md) v řadě nejlepších výsledků.

V tomto článku používáme poznámkových bloků Azure nejprve pomocí Azure Machine Learning model strojového učení a pak balíček tohoto modelu jako image kontejneru, který je možné nasadit jako modul služby Azure IoT Edge. Poznámkových bloků Azure využívat Azure Machine Learning službu pracovního prostoru, což je základní blok použít k experimentování, trénovat a nasazovat modely machine learningu.

Aktivity v této části kurzu se rozdělit mezi dva poznámkové bloky.

* **01-turbofan\_regression.ipynb:** Tento poznámkový blok vás provede kroky pro trénování a publikování modelu pomocí Azure Machine Learning. Potřebný postup se širokém smyslu:

  1. Stáhnout, přípravě a prozkoumejte trénovacích dat
  2. Použití pracovního prostoru služby k vytvoření a spuštění experimentu služby machine learning
  3. Vyhodnocení výsledků modelu z experimentu
  4. Nejlepší model publikujte do pracovního prostoru služby

* **02-turbofan\_deploy\_model.ipynb:** Tento poznámkový blok má model vytvořený v předchozích Poznámkový blok a použije ho k vytvoření image kontejneru, která je připravená k nasazení do zařízení s Azure IoT Edge.

  1. Vytváření hodnoticí skript pro model
  2. Vytvoření a publikování image
  3. Nasazení bitové kopie jako webové služby na instanci kontejneru Azure
  4. Použijte webovou službu pro ověření modelu a image fungují podle očekávání

Kroky v tomto článku můžou být obvykle provádí odborníci přes data.

## <a name="set-up-azure-notebooks"></a>Nastavit poznámkových bloků Azure

Používáme poznámkových bloků Azure k hostování dva poznámkové bloky Jupyter a podpůrné soubory. Zde vytvoříme a konfigurace projektu poznámkových bloků Azure. Pokud jste ještě nepoužívali, a/nebo Azure poznámkových bloků Jupyter, tady je několik dokumentů úvodní:

* **Rychlý start:** [Vytvářejte a sdílejte poznámkového bloku](../notebooks/quickstart-create-share-jupyter-notebook.md)
* **Kurz:** [Vytvoření a spuštění poznámkového bloku Jupyter s využitím Pythonu](../notebooks/tutorial-create-run-jupyter-notebook.md)

Jako s virtuálním počítačem pro vývojáře dříve, pomocí poznámkových bloků Azure zajišťuje konzistentní prostředí pro výkon.

> [!NOTE]
> Jakmile je nastavená, službě Azure Notebooks je přístupný z libovolného počítače. Během instalace měli byste použít Vývojový virtuální počítač, který obsahuje všechny soubory, které budete potřebovat.

### <a name="create-an-azure-notebooks-account"></a>Vytvoření účtu služby Azure poznámkové bloky

Účty Azure poznámkového bloku jsou nezávislá na předplatná Azure. Použití poznámkových bloků Azure, budete muset vytvořit účet.

1. Přejděte do [poznámkových bloků Azure](http://notebooks.azure.com).

2. Klikněte na tlačítko **Sign In** v horním rohu, v pravém horním rohu stránky.

3. Přihlaste se pomocí pracovního nebo školního účtu (Azure Active Directory) nebo váš osobní účet (Microsoft Account).

4. Pokud jste ještě nepoužívali poznámkových bloků Azure před, se výzva k udělení přístupu pro aplikaci poznámkových bloků Azure.

5. Vytvořte ID uživatele pro poznámkových bloků Azure.

### <a name="upload-jupyter-notebooks-files"></a>Nahrání souborů poznámkové bloky Jupyter

V tomto kroku vytvoříme nový projekt poznámkových bloků Azure a do něj nahrát soubory. Konkrétně se nám nahrát soubory:

* **01-turbofan\_regression.ipynb**: Soubor poznámkového bloku Jupyter, který vás provede procesem stahování dat vygenerovaných vašimi harness zařízení z účtu úložiště Azure; zkoumání a příprava dat pro trénování třídění; trénování modelu; testování data s využitím testovací datové sady v testu nalezen\_FD003.txt souboru; a nakonec ukládání třídění modelu v pracovním prostoru služby Machine Learning.

* **02-turbofan\_deploy\_model.ipynb:** Poznámkový blok Jupyter, který vás provede procesem pomocí modelu třídění uloží v pracovním prostoru služby Machine Learning k vytvoření image kontejneru. Po vytvoření image byl očekáván procházení Poznámkový blok, který vás provede procesem nasazení bitové kopie jako webovou službu tak, aby je bylo možné ověřit funguje jako. Ověřená image se nasadí do našich hraniční zařízení v [vytvořit a nasadit vlastní moduly IoT Edge](tutorial-machine-learning-edge-06-custom-modules.md) část tohoto kurzu.

* **Test\_FD003.txt:** Tento soubor obsahuje data, které používáme jako naše test nastavení při ověřování naše trénovaného třídění. Jsme se rozhodli použít testovací data, jak je uvedeno pro původní soutěže jako naše testovací nastavení pro zjednodušení tento příklad.

* **Zbývající doby životnosti\_FD003.txt:** Tento soubor obsahuje zbývající doby životnosti pro poslední cyklus každé zařízení v testu\_FD003.txt souboru. Najdete v článku **readme.txt** a **poškození šíření Modeling.pdf** soubory v jednotce C:\\zdroj\\IoTEdgeAndMlSample\\data\\Turbofan pro podrobné vysvětlení data.

* **Utils.py:** Obsahuje sadu funkcí nástroje Pythonu pro práci s daty. První Poznámkový blok obsahuje podrobné vysvětlení funkcí.

* **README.md:** Soubor Readme s popisem použití poznámkových bloků.

Vytvořte nový projekt a nahrajte soubory v poznámkovém bloku.

1. Vyberte **projekty** v horní nabídce.

1. Vyberte **+ nový projekt**. Zadejte název a identifikátor. Není nutné pro projekt být veřejné nebo souboru readme.

1. Vyberte **nahrát** a zvolte **z počítače**.

1. Vyberte **vybrat soubory**.

1. Přejděte do **C:\source\IoTEdgeAndMlSample\AzureNotebooks**. Vyberte všechny soubory a klikněte na tlačítko **otevřít**.

1. Vyberte **nahrát** k nahrání a pak vyberte **provádí** po dokončení procesu.

## <a name="run-azure-notebooks"></a>Spouštění poznámkových bloků Azure

Teď, když se vytvoří projekt, spusťte **01 turbofan\_regression.ipynb** poznámkového bloku.

1. Na stránce projektu turbofan vyberte **01 turbofan\_regression.ipynb**.

    ![Vyberte první Poznámkový blok jupyter spustit](media/tutorial-machine-learning-edge-04-train-model/select-turbofan-regression-notebook.png)

2. Pokud se zobrazí výzva, zvolte jádra Python 3.6 z dialogového okna a vyberte **nastavit jádra**.

3. Pokud je hodnota uvedena jako poznámkového bloku **není důvěryhodný.** , klikněte na **není důvěryhodný.** widgetu v horní části napravo od poznámkového bloku. Když dialogové okno se zobrazí, vyberte **důvěřovat**.

4. Postupujte podle pokynů v poznámkovém bloku.

    * `Ctrl + Enter` Spustí buňky.
    * `Shift + Enter` Spustí buňky a přejde na další buňku.
    * Při spuštění buňky má hvězdičky mezi hranaté závorky, jako je třeba **[\*]** . Když je dokončená, hvězdička se nahradí s číslem a může být relevantní výstup se níže zobrazí. Od buňky často sestavení na práci předchozích balíčcích, můžete současně spustit pouze jednu buňku.

5. Po dokončení spuštění **01 turbofan\_regression.ipynb** Poznámkový blok, vraťte se na stránce projektu.

6. Otevřít **02 turbofan\_nasazení\_model.ipynb** a opakujte kroky v této části druhý Poznámkový blok spustit.

## <a name="next-steps"></a>Další postup

V tomto článku jsme použili dva poznámkové bloky Jupyter používané poznámkových bloků Azure s využitím dat ze zařízení turbofan trénování zbývající dobu životnosti (RUL) třídění, uložte třídění jako model, k vytvoření image kontejneru a k nasazení a otestujte image jako web se lužby.

Pokračujte k dalšímu článku k vytvoření zařízení IoT Edge.

> [!div class="nextstepaction"]
> [Konfigurace zařízení IoT Edge](tutorial-machine-learning-edge-05-configure-edge-device.md)
