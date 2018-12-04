---
title: Migrovat místní aplikace Jupyter notebook poznámkových bloků Azure | Dokumentace Microsoftu
description: Rychle převést Poznámkový blok Jupyter do poznámkových bloků Azure z místního počítače nebo adresu URL webu, a její následné sdílení pro spolupráci.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 2e935425-3923-4a33-89b2-0f2100b0c0c4
ms.service: notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: bac6834a8ffb107c344086d1d3c28990f32a4760
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52855772"
---
# <a name="quickstart-migrate-a-local-jupyter-notebook"></a>Rychlý start: Migrovat místní aplikace Jupyter notebook

Poznámkové bloky Jupyter, které vytvoříte místně na vlastním počítači jsou dostupné pouze pro vás. Můžete sdílet soubory prostřednictvím různých prostředků, ale pak příjemci mít své vlastní místní kopii Poznámkový blok a je těžké za vás, abyste zapracovali všechny změny, které můžou zvolit. Můžete také ukládat poznámkových bloků v sdílené úložiště online, jako je například GitHub, ale to stále vyžaduje, aby měl každý spolupracovníka vlastní místní instalace aplikace Jupyter se stejnou konfigurací se nenachází žádný.

Pomocí migrace vašich místních nebo na základě úložiště poznámkových bloků do poznámkových bloků Azure, uložit je v cloudu, ze kterého můžete okamžitě je sdílet s vaší spolupracovníky. Tyto spolupracovníci potřebujete jenom prohlížeč pro zobrazení a spuštění Poznámkový blok a pokud jsou [přihlášení](quickstart-sign-in-azure-notebooks.md) do poznámkových bloků Azure, můžete také provádět změny.

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

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Kurz: vytvoření spustit Poznámkový blok Jupyter provedete lineární regrese](tutorial-create-run-jupyter-notebook.md)
