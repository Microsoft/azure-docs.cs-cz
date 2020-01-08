---
title: Migrace místního poznámkového bloku Jupyter do verze Preview Azure Notebooks
description: Jupyter Poznámkový blok můžete rychle přenést do Azure Notebooks Preview z místního počítače nebo z webové adresy URL a pak ho sdílet pro spolupráci.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 9e5270c59a64f9510f9108bbe4d00b922178888c
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75647046"
---
# <a name="quickstart-migrate-a-local-jupyter-notebook-in-azure-notebooks-preview"></a>Rychlý Start: migrace místního poznámkového bloku Jupyter v Azure Notebooks Preview

Poznámkové bloky Jupyter, které vytvoříte místně na vlastním počítači jsou dostupné pouze pro vás. Můžete sdílet soubory prostřednictvím různých prostředků, ale pak příjemci mít své vlastní místní kopii Poznámkový blok a je těžké za vás, abyste zapracovali všechny změny, které můžou zvolit. Můžete také ukládat poznámkových bloků v sdílené úložiště online, jako je například GitHub, ale to stále vyžaduje, aby měl každý spolupracovníka vlastní místní instalace aplikace Jupyter se stejnou konfigurací se nenachází žádný.

Pomocí migrace vašich místních nebo na základě úložiště poznámkových bloků do poznámkových bloků Azure, uložit je v cloudu, ze kterého můžete okamžitě je sdílet s vaší spolupracovníky. Tyto spolupracovníci potřebujete jenom prohlížeč pro zobrazení a spuštění Poznámkový blok a pokud jsou [přihlášení](quickstart-sign-in-azure-notebooks.md) do poznámkových bloků Azure, můžete také provádět změny.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Tento rychlý start popisuje proces migrace z místního počítače nebo jinou adresu URL přístupná soubor poznámkového bloku. K migraci poznámkových bloků z úložiště GitHub, najdete v článku [rychlý start: klonování Poznámkový blok](quickstart-clone-jupyter-notebook.md).

## <a name="create-a-project-on-azure-notebooks"></a>Vytvoření projektu v poznámkových blocích Azure

1. Přejděte na [poznámkových bloků Azure](https://notebooks.azure.com) a přihlaste se. (Podrobnosti najdete v tématu [rychlý start – přihlášení k Azure Notebooks](quickstart-sign-in-azure-notebooks.md)).

1. Na stránce svého veřejného profilu vyberte **projekty** v horní části stránky:

    ![Moje projekty odkaz horní části okna prohlížeče](media/quickstarts/my-projects-link.png)

1. Na **projekty** stránce **+ nový projekt** (Klávesová zkratka: n); na tlačítko se může objevit pouze jako **+** li úzké okno prohlížeče:

    ![Nový projekt – příkaz na stránce Mé projekty](media/quickstarts/new-project-command.png)

1. V **vytvořit nový projekt** automaticky otevírané okno, které se zobrazí, zadejte odpovídající hodnoty Poznámkový blok jupyter v migrujete **název projektu** a **ID projektu** pole, zrušte možnosti pro **veřejného projektu** a **vytvořit README.md**a pak vyberte **vytvořit**.

## <a name="upload-the-local-notebook"></a>Nahrání místního poznámkového bloku

1. Na stránce projektu vyberte **nahrát** (což se může zobrazit jako nahoru šipka pouze pokud okno prohlížeče je malé), pak vyberte 1. V místní nabídce, která se zobrazí, vyberte **z počítače** Pokud poznámkový blok se nachází na vašem místním systému souborů, nebo **z adresy URL** Pokud online se nachází v poznámkovém bloku:

    ![Příkaz pro nahrání poznámkového bloku z adresy URL nebo místní počítač](media/quickstarts/upload-from-computer-url-command.png)

   (Znovu, pokud je v úložišti GitHub, postupujte podle kroků [rychlý start: klonování Poznámkový blok](quickstart-clone-jupyter-notebook.md) místo.)

   - Pokud používáte **z počítače**, přetažením vaše *.ipynb* soubory do automaticky otevíraného okna, nebo vyberte **vybrat soubory**, vyhledejte a vyberte soubory, které chcete importovat. Potom vyberte **nahrát**. Nahrané soubory jsou uvedeny stejný název jako místní soubory. (Není nutné k nahrání obsahu jakékoli *.ipynb_checkpoints* složek.)

     ![Nahrát z počítače automaticky otevíraného okna](media/quickstarts/upload-from-computer-popup.png)

   - Pokud používáte **z adresy URL**, zadejte adresu zdroje **adresa URL souboru** pole a název souboru přiřadit do poznámkového bloku v projektu v **název souboru** pole. Potom vyberte **nahrát**. Pokud máte víc souborů s samostatné adresy URL, použijte **+ přidat soubor** příkaz a zkontrolujte první adresa URL, které jste zadali, po jejímž uplynutí automaticky otevírané okno obsahuje nové pole pro jiný soubor.

     ![Nahrát z adresy URL automaticky otevíraného okna](media/quickstarts/upload-from-url-popup.png)

1. Otevřete a spusťte nově nahraných Poznámkový blok a ověřte jeho obsah a operace. Jakmile budete hotovi, vyberte **souboru** > **zastavení a zavřít** zavřete poznámkový blok.

1. Sdílet odkaz na vaše nahrané Poznámkový blok, klikněte pravým tlačítkem na soubor v projektu a vyberte **Kopírovat odkaz** (Klávesová zkratka: y), vložte tento odkaz do odpovídající zprávu. Alternativně můžete sdílet projekt jako celek za použití **sdílet** ovládací prvek na stránce projektu.

1. Chcete-li upravit soubory jiné než poznámkových bloků, klikněte pravým tlačítkem na soubor v projektu a vyberte **upravit soubor** (Klávesová zkratka: Mohu). Výchozí akce **spustit** (Klávesová zkratka: r), pouze zobrazuje obsah souboru a neumožňuje úpravy.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: vytvoření spustit Poznámkový blok Jupyter provedete lineární regrese](tutorial-create-run-jupyter-notebook.md)
