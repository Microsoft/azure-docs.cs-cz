---
title: Migrace místního poznámkového bloku Jupyter do verze Preview Azure Notebooks
description: Jupyter Poznámkový blok můžete rychle přenést do Azure Notebooks Preview z místního počítače nebo z webové adresy URL a pak ho sdílet pro spolupráci.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 9c961ad67260cee2809e0bba1e79f2c709183dea
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "85832113"
---
# <a name="quickstart-migrate-a-local-jupyter-notebook-in-azure-notebooks-preview"></a>Rychlý Start: migrace místního poznámkového bloku Jupyter v Azure Notebooks Preview

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

V tomto rychlém startu migrujete Poznámkový blok Jupyter z místního počítače nebo jiné adresy URL přístupového souboru na Azure Notebooks. 

Jupyter poznámkové bloky na vašem počítači jsou přístupné jenom pro vás. Soubory můžete sdílet, ale příjemci pak mají své vlastní místní kopie poznámkového bloku a je obtížné tyto změny začlenit. I když poznámkové bloky ukládáte do sdíleného online úložiště, jako je GitHub, každý spolupracovníka musí mít nakonfigurovanou místní instalaci Jupyter jako vaše.

Migrací místních poznámkových bloků nebo notebooků založených na úložišti na Azure Notebooks je můžete okamžitě sdílet se spolupracovníky, kteří potřebují jenom prohlížeč k zobrazení a spuštění vašich poznámkových bloků. Pokud se přihlásí k Azure Notebooks, můžou dělat změny i vy.

## <a name="prerequisites"></a>Předpoklady

- [Jupyter Poznámkový blok](https://jupyter-notebook.readthedocs.io) na místním počítači nebo v jiné adrese URL souboru k dispozici. 

## <a name="create-a-project-on-azure-notebooks"></a>Vytvořit projekt na Azure Notebooks

V tomto rychlém startu se dozvíte, jak migrovat Poznámkový blok z místního počítače nebo jiného přístupného souboru. Postup migrace poznámkových bloků z úložiště GitHub najdete v tématu [rychlý Start: naklonování poznámkového bloku](quickstart-clone-jupyter-notebook.md).

1. Přejít na [Azure Notebooks](https://notebooks.azure.com) a přihlásit se. (Podrobnosti najdete v tématu [rychlý Start – přihlášení k Azure Notebooks](quickstart-sign-in-azure-notebooks.md)).

1. Na stránce veřejný profil vyberte v horní části stránky **Moje projekty** :

    ![Odkaz Moje projekty v horní části okna prohlížeče](media/quickstarts/my-projects-link.png)

1. Na stránce **Moje projekty** vyberte **Nový projekt** (Klávesová zkratka: n). Tlačítko se může zobrazit jenom v **+** případě, že je okno prohlížeče úzké:

    ![Nový projekt na stránce Moje projekty](media/quickstarts/new-project-command.png)

1. V automaticky otevřeném okně **vytvořit nový projekt** zadejte odpovídající hodnoty pro Poznámkový blok, který migrujete v polích **název projektu** a **ID projektu** , zrušte zaškrtnutí možnosti pro **veřejný projekt** a **vytvořte Readme.MD**a pak vyberte **vytvořit**.

## <a name="upload-the-local-notebook"></a>Nahrání místního poznámkového bloku

1. Na stránce projekt vyberte **Odeslat** (může se zobrazit jako šipka nahoru pouze v případě, že je okno prohlížeče malé) a pak vyberte 1. V místní nabídce, která se zobrazí, vyberte **z nabídky počítač** , pokud je váš Poznámkový blok umístěný v místním systému souborů nebo **z adresy URL** , pokud je váš Poznámkový blok online:

    ![Příkaz pro nahrání poznámkového bloku z adresy URL nebo místního počítače](media/quickstarts/upload-from-computer-url-command.png)

   Pokud je váš Poznámkový blok v úložišti GitHubu, postupujte podle kroků v [rychlém startu: naklonujte Poznámkový blok](quickstart-clone-jupyter-notebook.md) .

   - Pokud používáte **z počítače**, přetáhněte soubory *. ipynb* do automaticky otevíraného okna nebo vyberte **zvolit soubory**, vyhledejte a vyberte soubory, které chcete importovat. Potom vyberte **Nahrát**. Nahrané soubory mají stejný název jako místní soubory. Nemusíte nahrávat obsah žádné *.ipynb_checkpoints* složky.

     ![Místní nabídka nahrát z počítače](media/quickstarts/upload-from-computer-popup.png)

   - Pokud používáte **z adresy URL**, zadejte zdrojovou adresu do pole **Adresa URL souboru** a název souboru, který chcete přiřadit k poznámkovému bloku v projektu v poli **název souboru** . Potom vyberte **Nahrát**. Pokud máte více souborů s oddělenými adresami URL, použijte příkaz **Přidat soubor** pro kontrolu první adresy URL, kterou jste zadali, a potom v místní nabídce najdete nová pole pro jiný soubor.

     ![Místní nabídka nahrát z adresy URL](media/quickstarts/upload-from-url-popup.png)

1. Otevřete a spusťte nově nahraný Poznámkový blok a ověřte jeho obsah a operaci. Až budete hotovi, vyberte **soubor**  >  **zastavit a zavřít** pro zavření poznámkového bloku.

1. Chcete-li sdílet odkaz na nahraný Poznámkový blok, klikněte pravým tlačítkem myši na soubor v projektu a vyberte příkaz **Kopírovat odkaz** (Klávesová zkratka: y) a vložte tento odkaz do příslušné zprávy. Alternativně můžete projekt sdílet jako celek pomocí ovládacího prvku pro **sdílení** na stránce projektu.

1. Chcete-li upravit jiné soubory než poznámkové bloky, klikněte pravým tlačítkem myši na soubor v projektu a vyberte možnost **Upravit soubor** (Klávesová zkratka: i). Výchozí akce, **Run** (Klávesová zkratka: r), zobrazuje pouze obsah souboru a neumožňuje úpravy.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: vytvoření a spuštění poznámkového bloku Jupyter pro lineární regresi](tutorial-create-run-jupyter-notebook.md)
