---
title: Migrace místního poznámkového bloku Jupyter na Azure Notebooks
description: Jupyter Poznámkový blok můžete rychle přenést do Azure Notebooks z místního počítače nebo z webové adresy URL a pak ho sdílet pro spolupráci.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 2e935425-3923-4a33-89b2-0f2100b0c0c4
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 2151d5b04b0fe5d47e8e2ddfb750ac279b6f74fa
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970191"
---
# <a name="quickstart-migrate-a-local-jupyter-notebook"></a>Rychlý Start: migrace místního poznámkového bloku Jupyter

Poznámkové bloky Jupyter, které vytvoříte místně na svém počítači, jsou přístupné jenom pro vás. Soubory můžete sdílet různými způsoby, ale pak mají příjemci svou vlastní místní kopii poznámkového bloku a je obtížné začlenit jakékoli změny, které by mohly dělat. Poznámkové bloky můžete také ukládat do sdíleného online úložiště, jako je GitHub, ale přesto vyžaduje, aby každý spolupracovníka měla svou vlastní místní instalaci Jupyter se stejnou konfigurací jako vaše.

Díky migraci místních poznámkových bloků nebo notebooků založených na úložišti na Azure Notebooks je uložíte v cloudu, ze kterého je můžete snadno sdílet s vašimi spolupracovníky. Tito spolupracovníci potřebují jenom prohlížeč pro zobrazení a spuštění vašeho poznámkového bloku, a pokud se [přihlásí](quickstart-sign-in-azure-notebooks.md) , Azure Notebooks můžou dělat změny taky.

Tento rychlý Start ukazuje proces migrace poznámkového bloku z místního počítače nebo jiné adresy URL přístupného souboru. Postup migrace poznámkových bloků z úložiště GitHub najdete v tématu [rychlý Start: naklonování poznámkového bloku](quickstart-clone-jupyter-notebook.md).

## <a name="create-a-project-on-azure-notebooks"></a>Vytvořit projekt na Azure Notebooks

1. Přejít na [Azure Notebooks](https://notebooks.azure.com) a přihlásit se. (Podrobnosti najdete v tématu [rychlý Start – přihlášení k Azure Notebooks](quickstart-sign-in-azure-notebooks.md)).

1. Na stránce veřejný profil vyberte v horní části stránky **Moje projekty** :

    ![Odkaz Moje projekty v horní části okna prohlížeče](media/quickstarts/my-projects-link.png)

1. Na stránce **Moje projekty** vyberte **+ Nový projekt** (Klávesová zkratka: n); tlačítko se může zobrazit pouze jako **+** , pokud je okno prohlížeče úzké:

    ![Nový projekt na stránce Moje projekty](media/quickstarts/new-project-command.png)

1. V automaticky otevřeném okně **vytvořit nový projekt** zadejte odpovídající hodnoty pro Poznámkový blok, který migrujete v polích **název projektu** a **ID projektu** , zrušte zaškrtnutí možnosti pro **veřejný projekt** a **vytvořte Readme.MD**a pak Vyberte **vytvořit**.

## <a name="upload-the-local-notebook"></a>Nahrání místního poznámkového bloku

1. Na stránce projekt vyberte **Odeslat** (může se zobrazit jako šipka nahoru pouze v případě, že je okno prohlížeče malé) a pak vyberte 1. V místní nabídce, která se zobrazí, vyberte **z nabídky počítač** , pokud je váš Poznámkový blok umístěný v místním systému souborů nebo **z adresy URL** , pokud je váš Poznámkový blok online:

    ![Příkaz pro nahrání poznámkového bloku z adresy URL nebo místního počítače](media/quickstarts/upload-from-computer-url-command.png)

   (Pokud je váš Poznámkový blok v úložišti GitHubu, postupujte podle kroků v [rychlém startu: naklonujte Poznámkový blok](quickstart-clone-jupyter-notebook.md) místo.)

   - Pokud používáte **z počítače**, přetáhněte soubory *. ipynb* do automaticky otevíraného okna nebo vyberte **zvolit soubory**, vyhledejte a vyberte soubory, které chcete importovat. Pak vyberte **nahrát**. Nahrané soubory mají stejný název jako místní soubory. (Nemusíte nahrávat obsah žádné složky *. ipynb_checkpoints* .)

     ![Místní nabídka nahrát z počítače](media/quickstarts/upload-from-computer-popup.png)

   - Pokud používáte **z adresy URL**, zadejte zdrojovou adresu do pole **Adresa URL souboru** a název souboru, který chcete přiřadit k poznámkovému bloku v projektu v poli **název souboru** . Pak vyberte **nahrát**. Pokud máte více souborů s oddělenými adresami URL, použijte příkaz **+ Přidat soubor** pro kontrolu první adresy URL, kterou jste zadali, a potom v místní nabídce najdete nová pole pro jiný soubor.

     ![Místní nabídka nahrát z adresy URL](media/quickstarts/upload-from-url-popup.png)

1. Otevřete a spusťte nově nahraný Poznámkový blok a ověřte jeho obsah a operaci. Až budete hotovi, vyberte **soubor** > **zastavit a zavřít a** Zavřete Poznámkový blok.

1. Chcete-li sdílet odkaz na nahraný Poznámkový blok, klikněte pravým tlačítkem myši na soubor v projektu a vyberte příkaz **Kopírovat odkaz** (Klávesová zkratka: y) a vložte tento odkaz do příslušné zprávy. Alternativně můžete projekt sdílet jako celek pomocí ovládacího prvku pro **sdílení** na stránce projektu.

1. Chcete-li upravit jiné soubory než poznámkové bloky, klikněte pravým tlačítkem myši na soubor v projektu a vyberte možnost **Upravit soubor** (Klávesová zkratka: i). Výchozí akce, **Run** (Klávesová zkratka: r), zobrazuje pouze obsah souboru a neumožňuje úpravy.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: vytvoření Jupyter poznámkového bloku pro provádění lineární regrese](tutorial-create-run-jupyter-notebook.md)
