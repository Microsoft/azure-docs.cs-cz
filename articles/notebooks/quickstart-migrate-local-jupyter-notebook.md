---
title: Migrovat místní aplikace Jupyter notebook poznámkových bloků Azure
description: Rychle převést Poznámkový blok Jupyter do poznámkových bloků Azure z místního počítače nebo adresu URL webu, a její následné sdílení pro spolupráci.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 3b7d5aab05923fb4356e0d45aaf21a77076a870a
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277470"
---
# <a name="quickstart-migrate-a-local-jupyter-notebook"></a>Rychlý start: Migrovat místní aplikace Jupyter notebook

Poznámkové bloky Jupyter, které vytvoříte místně na vlastním počítači jsou dostupné pouze pro vás. Můžete sdílet soubory prostřednictvím různých prostředků, ale pak příjemci mít své vlastní místní kopii Poznámkový blok a je těžké za vás, abyste zapracovali všechny změny, které můžou zvolit. Můžete také ukládat poznámkových bloků v sdílené úložiště online, jako je například GitHub, ale to stále vyžaduje, aby měl každý spolupracovníka vlastní místní instalace aplikace Jupyter se stejnou konfigurací se nenachází žádný.

Pomocí migrace vašich místních nebo na základě úložiště poznámkových bloků do poznámkových bloků Azure, uložit je v cloudu, ze kterého můžete okamžitě je sdílet s vaší spolupracovníky. Tito spolupracovníci potřebují jenom prohlížeč pro zobrazení a spuštění vašeho poznámkového bloku, a pokud se [přihlásí](quickstart-sign-in-azure-notebooks.md) , Azure Notebooks můžou dělat změny taky.

Tento rychlý start popisuje proces migrace z místního počítače nebo jinou adresu URL přístupná soubor poznámkového bloku. Postup migrace poznámkových bloků z úložiště GitHub najdete v tématu [rychlý Start: naklonování poznámkového bloku](quickstart-clone-jupyter-notebook.md).

## <a name="create-a-project-on-azure-notebooks"></a>Vytvoření projektu v poznámkových blocích Azure

1. Přejít na [Azure Notebooks](https://notebooks.azure.com) a přihlásit se. (Podrobnosti najdete v tématu [rychlý Start – přihlášení k Azure Notebooks](quickstart-sign-in-azure-notebooks.md)).

1. Na stránce veřejný profil vyberte v horní části stránky **Moje projekty** :

    ![Moje projekty odkaz horní části okna prohlížeče](media/quickstarts/my-projects-link.png)

1. Na stránce **Moje projekty** vyberte **+ Nový projekt** (Klávesová zkratka: n); tlačítko se může zobrazit jenom jako **+** , pokud je okno prohlížeče úzké:

    ![Nový projekt – příkaz na stránce Mé projekty](media/quickstarts/new-project-command.png)

1. V automaticky otevřeném okně **vytvořit nový projekt** zadejte odpovídající hodnoty pro Poznámkový blok, který migrujete v polích **název projektu** a **ID projektu** , zrušte zaškrtnutí možnosti pro **veřejný projekt** a **vytvořte Readme.MD**a pak vyberte **vytvořit**.

## <a name="upload-the-local-notebook"></a>Nahrání místního poznámkového bloku

1. Na stránce projekt vyberte **Odeslat** (může se zobrazit jako šipka nahoru pouze v případě, že je okno prohlížeče malé) a pak vyberte 1. V místní nabídce, která se zobrazí, vyberte **z nabídky počítač** , pokud je váš Poznámkový blok umístěný v místním systému souborů nebo **z adresy URL** , pokud je váš Poznámkový blok online:

    ![Příkaz pro nahrání poznámkového bloku z adresy URL nebo místní počítač](media/quickstarts/upload-from-computer-url-command.png)

   (Pokud je váš Poznámkový blok v úložišti GitHubu, postupujte podle kroků v [rychlém startu: naklonujte Poznámkový blok](quickstart-clone-jupyter-notebook.md) místo.)

   - Pokud používáte **z počítače**, přetáhněte soubory *. ipynb* do automaticky otevíraného okna nebo vyberte **zvolit soubory**, vyhledejte a vyberte soubory, které chcete importovat. Pak vyberte **nahrát**. Nahrané soubory jsou uvedeny stejný název jako místní soubory. (Nemusíte nahrávat obsah žádné složky *. ipynb_checkpoints* .)

     ![Nahrát z počítače automaticky otevíraného okna](media/quickstarts/upload-from-computer-popup.png)

   - Pokud používáte **z adresy URL**, zadejte zdrojovou adresu do pole **Adresa URL souboru** a název souboru, který chcete přiřadit k poznámkovému bloku v projektu v poli **název souboru** . Pak vyberte **nahrát**. Pokud máte více souborů s oddělenými adresami URL, použijte příkaz **+ Přidat soubor** pro kontrolu první adresy URL, kterou jste zadali, a potom v místní nabídce najdete nová pole pro jiný soubor.

     ![Nahrát z adresy URL automaticky otevíraného okna](media/quickstarts/upload-from-url-popup.png)

1. Otevřete a spusťte nově nahraných Poznámkový blok a ověřte jeho obsah a operace. Až budete hotovi, vyberte **soubor** > **zastavit a zavřít** pro zavření poznámkového bloku.

1. Chcete-li sdílet odkaz na nahraný Poznámkový blok, klikněte pravým tlačítkem myši na soubor v projektu a vyberte příkaz **Kopírovat odkaz** (Klávesová zkratka: y) a vložte tento odkaz do příslušné zprávy. Alternativně můžete projekt sdílet jako celek pomocí ovládacího prvku pro **sdílení** na stránce projektu.

1. Chcete-li upravit jiné soubory než poznámkové bloky, klikněte pravým tlačítkem myši na soubor v projektu a vyberte možnost **Upravit soubor** (Klávesová zkratka: i). Výchozí akce, **Run** (Klávesová zkratka: r), zobrazuje pouze obsah souboru a neumožňuje úpravy.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: vytvoření Jupyter poznámkového bloku pro provádění lineární regrese](tutorial-create-run-jupyter-notebook.md)
