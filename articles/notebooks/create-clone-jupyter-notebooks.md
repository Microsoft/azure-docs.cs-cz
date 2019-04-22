---
title: Vytvoření a klonování poznámkové bloky Jupyter v Azure
description: Projekty Azure poznámkových bloků spravovat kolekci poznámkových bloků a související soubory, které můžete vytvořit nový nebo naklonovat z jiného zdroje.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 9b6a49e2-1d71-4c0b-9e5d-16e059427e38
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2019
ms.author: kraigb
ms.openlocfilehash: 0ee0c7162e26b875c74796b6d5379b414981e2d5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59282317"
---
# <a name="create-and-clone-projects"></a>Vytváření a klonování projektů

Poznámkových bloků Azure slouží k uspořádání vašich poznámkových bloků Jupyter a související soubory do logických skupin, které volá *projekty*. Jako kontejner, nejprve vytvořte projekt pak vytvořit nebo klonovat jeden nebo více poznámkových bloků v rámci složky společně s další soubory projektu. (Tento proces je patrné [kurzu](tutorial-create-run-jupyter-notebook.md).)

Projekt také udržuje metadat a dalších nastavení konfigurace, které mají vliv na server, na které poznámkových bloků spuštění, včetně pokynů k vlastní instalaci a instalace balíčku. Další informace najdete v tématu [spravovat a konfigurovat projekty](configure-manage-azure-notebooks-projects.md).

## <a name="use-the-my-projects-dashboard"></a>Pomocí řídicího panelu projekty

Vaše **projekty** řídicí panel na `https://notebooks.azure.com/<userID>/projects` zobrazovat, spravovat, a vytvářet projekty:

[![Můj řídicí panel projektů v poznámkových bloků Azure](media/my-projects-dashboard.png)](media/my-projects-dashboard.png#lightbox)

Co můžete dělat na řídicím panelu, závisí na tom, jestli jste přihlášení pomocí účtu, který vlastní ID uživatele:

| Příkaz | Dostupnost | Popis |
| --- | --- | --- |
| **Spuštění** | Vlastník | Spustí server project server a otevře složku projektu v Jupyter. (Častěji, můžete nejprve přejděte do složky projektu a pak spustit Poznámkový blok z něj.) |
| **Stáhnout** | Všem uživatelům | Stáhne kopii vybraného projektu jako soubor ZIP. |
| **Sdílet** | Všem uživatelům | Zobrazí sdílení překryvné okno, pomocí kterého můžete získat adresu URL do vybraného projektu, sdílení na sociálních médií, odeslat e-mail s adresou URL a získat kód HTML a Markdown pro s odznáčkem "spuštění poznámkového bloku" (Zobrazit [získat Odznáček spuštění](#obtain-a-launch-badge)) s adresou URL. |
| **Odstranění** | Vlastník | Odstraní vybraný projekt. Tato operace se nedá vrátit zpět. |
| **Terminálu** | Vlastník | Spustí server project server a pak otevře nové okno prohlížeče pomocí prostředí bash, terminálu pro tento server. |
| **+ Nový projekt** | Vlastník | Vytvoří nový projekt. Zobrazit [vytvořte nový projekt](#create-a-new-project). |
| **Nahrát úložiště GitHub** | Vlastník | Importuje projektu z Githubu. [Import projektu z Githubu](#import-a-project-from-github). |
| **Klonování** | Všem uživatelům | Zkopíruje vybraný projekt do vlastního účtu. Zobrazí výzvu k přihlášení Nepřihlášený. Zobrazit [Naklonujte projekt](#clone-a-project). |

### <a name="obtain-a-launch-badge"></a>Získání spuštění oznámení "BADGE"

Při použití **sdílenou složku** příkazů a vyberte **vložení** kartu, můžete zkopírovat kód HTML a Markdown, který vytvoří Odznáček "spuštění poznámkového bloku":

![Spusťte Poznámkový blok oznámení "BADGE"](https://notebooks.azure.com/launch.png)

Pokud projekt poznámkových bloků Azure nemáte, můžete vytvořit odkaz, který duplicity z Githubu přímo pomocí následující šablony, kde nahradíte odpovídající uživatelského jména a názvy úložišť:

```html
<a href="https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>"><img src="https://notebooks.azure.com/launch.png" /></a>
```

```markdown
[![Azure Notebooks](https://notebooks.azure.com/launch.png)](https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>)
```

## <a name="create-a-new-project"></a>Vytvoření nového projektu

Při použití **+ nový projekt** příkaz, zobrazí poznámkových bloků Azure **vytvořit nový projekt** automaticky otevíraného okna. V toto automaticky otevírané okno, zadejte následující informace a pak vyberte **vytvořit**:

| Pole | Popis |
| --- | --- |
| Název projektu | Popisný název pro váš projekt, který poznámkových bloků Azure používá pro účely zobrazení. Například "Můj Notebook projekt". |
| ID projektu | Vlastní identifikátor, který se stane součástí adresy URL, které umožňují sdílet projekt (je formulář `https://notebooks.azure.com/<user_id>/projects/<project_id>`). Toto ID můžete použít jenom písmena, číslice a spojovníky, je omezená na 30 znaků a nesmí být [rezervované ID projektu](#reserved-project-ids). Pokud si nejste jisti, co se má použít, je běžné konvence používat malá písmena verzi název vašeho projektu, kde jsou mezery převedena na rozdělovníků, například "my-poznámkového bloku – projekt" (zkráceno, aby odpovídala délkový limit). |
| Public | Pokud nastavíte, může prakticky kdokoli s odkazem na přístup k projektu. Při vytváření projektu privátní, zrušte zaškrtnutí tohoto políčka. |
| Inicializovat tento projekt pomocí souboru README | Pokud nastavení, vytvoří výchozí *README.md* soubor v projektu. A *README.md* soubor je, kde poskytuje dokumentaci pro váš projekt, v případě potřeby. |

### <a name="reserved-project-ids"></a>Rezervované ID projektu

Následující vyhrazená slova nelze použít samy o sobě jako ID projektu. Tato vyhrazená slova lze však použít jako součást delšího ID projektu.

| | | | | | |
| --- | --- | --- | --- | --- | --- |
| o produktu | account | Správa | rozhraní api | blog | Classroom |
| content | řídicí panel | Prozkoumejte službu | Nejčastější dotazy | Nápověda | html |
| Domovská složka | Import | Knihovna | Správa | novinka | Poznámkový blok |
| Poznámkové bloky | pdf | preview | Ceny | profil | hledat |
| status | podpora | test | | | |

Pokud se pokusíte použít jednu z těchto slov jako ID projektu, **vytvořit nový projekt** a **nastavení projektu** automaticky otevíraná okna označení "id knihovny je rezervovaný identifikátor."

Protože ID projektu je také součástí adresy URL projektu, ad blokování automaticky otevíraných oken softwaru může blokovat použití určitých klíčových slov, jako je například "inzerování." V takovém případě použijte různá slova v ID projektu.

## <a name="import-a-project-from-github"></a>Import projektu z Githubu

Celý veřejného úložiště GitHub můžete snadno importovat jako projekt, včetně všech dat a *README.md* soubory. Použití **nahrát úložiště GitHub se vzorovými** příkazu zadejte následující podrobnosti v místní nabídce, a pak vyberte **Import**:

| Pole | Popis |
| --- | --- |
| Úložiště GitHub | Název zdrojové úložiště na webu github.com. Například chcete naklonovat poznámkové bloky Jupyter pro Azure Cognitive Services v [ https://github.com/Microsoft/cognitive-services-notebooks ](https://github.com/Microsoft/cognitive-services-notebooks), zadejte "Microsoft/cognitive-services notebooky".  |
| Rekurzivně klonování | Úložiště GitHub může obsahovat více podřízených úložištích. Tuto možnost nastavte, pokud chcete klonovat nadřazeným úložištěm a všechny jeho podřízené objekty. Vzhledem k tomu je možné u úložiště mít mnoho podřízených objektů, ponechejte tuto možnost zrušte Pokud si nejste jisti, že ji budete potřebovat. |
| Název projektu | Popisný název pro váš projekt, který poznámkových bloků Azure používá pro účely zobrazení. |
| ID projektu | Vlastní identifikátor, který se stane součástí adresy URL, které umožňují sdílet projekt (je formulář `https://notebooks.azure.com/<user_id>/projects/<project_id>`). Toto ID můžete použít jenom písmena, číslice a spojovníky, je omezená na 30 znaků a nesmí být [rezervované ID projektu](#reserved-project-ids). Pokud si nejste jisti, co se má použít, je běžné konvence používat malá písmena verzi název vašeho projektu, kde jsou mezery převedena na rozdělovníků, například "my-poznámkového bloku – projekt" (zkráceno, aby odpovídala délkový limit). |
| Public | Pokud nastavíte, může prakticky kdokoli s odkazem na přístup k projektu. Při vytváření projektu privátní, zrušte zaškrtnutí tohoto políčka. |

Import úložiště z Githubu, naimportuje se také jeho historie. Můžete použít standardní příkazy Gitu z terminálu potvrďte nové změny vložit změny z Githubu a tak dále.

## <a name="clone-a-project"></a>Naklonujte projekt

Klonování vytvoří kopii existujícího projektu do vlastního účtu, kde budete moct zprovoznit službu a změnit libovolné poznámkového bloku nebo jiného souboru v projektu. Taky můžete klonování vytvářet kopie vašich vlastních projektů, ve kterých uděláte experimentů nebo jiné práce bez narušení původní projekt.

Naklonujte projekt:

1. Na **projekty** řídicí panel, klikněte pravým tlačítkem na požadovaný projekt a vyberte **klonování** (Klávesová zkratka: c).

    ![Klonovací příkaz v místní nabídce projektu](media/clone-command.png)

1. V **klonování projektu** automaticky otevírané okno, zadejte název a ID pro klonování a určete, jestli je veřejný klonu. Tato nastavení jsou stejné jako v případě [nový projekt](#create-a-new-project).

    ![Klonování projektu automaticky otevíraného okna](media/clone-project.png)

1. Po výběru **klonování** tlačítko, poznámkových bloků Azure přejde přímo na kopii.

## <a name="next-steps"></a>Další postup

- [Prozkoumejte ukázkové poznámkové bloky](azure-notebooks-samples.md)
- [Postup: Konfigurace a správa projektů](configure-manage-azure-notebooks-projects.md)
- [Postup: Instalace balíčků z v rámci poznámkového bloku](install-packages-jupyter-notebook.md)
- [Postup: K dispozici prezentace](present-jupyter-notebooks-slideshow.md)
- [Postup: Práce s datovými soubory](work-with-project-data-files.md)
- [Postup: Přístup k prostředkům data](access-data-resources-jupyter-notebooks.md)
- [Postup: Použití Azure Machine Learning Services](use-machine-learning-services-jupyter-notebooks.md)
