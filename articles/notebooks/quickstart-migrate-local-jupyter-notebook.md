---
title: Migrace místního poznámkového bloku Jupyter do náhledu poznámkových bloků Azure
description: Rychlý přenos poznámkového bloku Jupyter do náhledu poznámkových bloků Azure z místního počítače nebo webové adresy URL a jeho sdílení za účelem spolupráce.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: baf05d7adb1340d712ff0fc87436d5bbac51bc8f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77064321"
---
# <a name="quickstart-migrate-a-local-jupyter-notebook-in-azure-notebooks-preview"></a>Úvodní příručka: Migrace místního poznámkového bloku Jupyter ve verzi Azure Notebooks Preview

V tomto rychlém startu migrujete poznámkový blok Jupyter z místního počítače nebo jinou adresu URL přístupného souboru do poznámkových bloků Azure. 

Jupyter notebooky na vašem počítači jsou přístupné pouze pro vás. Můžete sdílet soubory, ale příjemci pak mají své vlastní místní kopie poznámkového bloku a je obtížné začlenit jejich změny. I když ukládáte poznámkové bloky ve sdíleném online úložišti, jako je GitHub, každý spolupracovník musí mít místní instalaci Jupyterna nakonfigurovanou jako vy.

Migrací místních poznámkových bloků nebo poznámkových bloků založených na úložišti do poznámkových bloků Azure je můžete okamžitě sdílet se svými spolupracovníky, kteří k zobrazení a spouštění poznámkových bloků potřebují jenom prohlížeč. Pokud se přihlásí k poznámkovým blokům Azure, můžou taky provádět změny.

## <a name="prerequisites"></a>Požadavky

- [Jupyter notebook](https://jupyter-notebook.readthedocs.io) na místním počítači nebo na jiné přístupné url souboru. 

## <a name="create-a-project-on-azure-notebooks"></a>Vytvoření projektu v poznámkových blocích Azure

Tento rychlý start ukazuje migraci poznámkového bloku z místního počítače nebo jiné adresy URL přístupného souboru. Informace o migraci poznámkových bloků z úložiště GitHub najdete v [tématu Úvodní příručka: Klonování poznámkového bloku](quickstart-clone-jupyter-notebook.md).

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

1. Přejděte na [Poznámkové bloky Azure](https://notebooks.azure.com) a přihlaste se. (Podrobnosti najdete [v tématu Úvodní příručka – přihlášení k poznámkovým blokům Azure).](quickstart-sign-in-azure-notebooks.md)

1. Na stránce veřejného profilu vyberte **moje projekty** v horní části stránky:

    ![Odkaz Moje projekty v horní části okna prohlížeče](media/quickstarts/my-projects-link.png)

1. Na stránce **Moje projekty** vyberte **Nový projekt** (klávesová zkratka: n). Tlačítko se může **+** zobrazit pouze tak, jako by bylo okno prohlížeče úzké:

    ![Příkaz Nový projekt na stránce Moje projekty](media/quickstarts/new-project-command.png)

1. Ve vyskakovacím okně **Vytvořit nový projekt,** které se zobrazí, zadejte příslušné hodnoty pro poznámkový blok, který migrujete, do polí **Název projektu** a **ID projektu** zrušte zaškrtnutí možností pro Veřejný **projekt** a **Vytvořte README.md**a pak vyberte **Vytvořit**.

## <a name="upload-the-local-notebook"></a>Nahrání místního poznámkového bloku

1. Na stránce projektu vyberte **Nahrát** (která se může zobrazit jako šipka nahoru pouze v případě, že je okno prohlížeče malé), pak vyberte 1. Ve vyskakovacím zobrazení vyberte **Z počítače,** pokud je váš poznámkový blok umístěný v místním systému souborů, nebo **z adresy URL,** pokud je poznámkový blok umístěn online:

    ![Příkaz k nahrání poznámkového bloku z adresy URL nebo místního počítače](media/quickstarts/upload-from-computer-url-command.png)

   Pokud je váš poznámkový blok v úložišti GitHub, postupujte podle pokynů na [úvodním panelu: Místo toho klonujte poznámkový blok.](quickstart-clone-jupyter-notebook.md)

   - Pokud **používáte z počítače**, přetáhněte soubory *.ipynb* do místního vyskakovacího období nebo vyberte **Zvolit soubory**, pak vyhledejte a vyberte soubory, které chcete importovat. Potom vyberte **Nahrát**. Nahrané soubory mají stejný název jako místní soubory. Není nutné nahrávat obsah žádné složky *.ipynb_checkpoints.*

     ![Nahrát z místního blokování počítače](media/quickstarts/upload-from-computer-popup.png)

   - Pokud **používáte adresu URL from**, zadejte do pole **Adresa URL souboru** zdrojovou adresu a název souboru, který chcete přiřadit poznámkovému bloku v projektu, do pole **Název souboru.** Potom vyberte **Nahrát**. Pokud máte více souborů se samostatnými adresami URL, zkontrolujte pomocí **příkazu Přidat soubor** první zadanou adresu URL, po které vyskakovací okno poskytuje nová pole pro jiný soubor.

     ![Nahrát z místního vyskakovacího místa url](media/quickstarts/upload-from-url-popup.png)

1. Otevřete a spusťte nově nahraný poznámkový blok a ověřte jeho obsah a provoz. Až budete hotovi, vyberte **Zastavení souborů** > **a zavřete** poznámkový blok.

1. Chcete-li sdílet odkaz na nahraný poznámkový blok, klikněte pravým tlačítkem myši na soubor v projektu a vyberte **příkaz Kopírovat odkaz** (klávesová zkratka: y) a vložte tento odkaz do příslušné zprávy. Alternativně můžete sdílet projekt jako celek pomocí ovládacího prvku **Sdílet** na stránce projektu.

1. Pokud chcete upravovat jiné soubory než poznámkové bloky, klikněte pravým tlačítkem myši na soubor v projektu a vyberte **Upravit soubor** (klávesová zkratka: i). Výchozí akce **Spustit** (klávesová zkratka: r) zobrazuje pouze obsah souboru a neumožňuje úpravy.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: Vytvoření a spuštění poznámkového bloku Jupyter pro lineární regresi](tutorial-create-run-jupyter-notebook.md)
