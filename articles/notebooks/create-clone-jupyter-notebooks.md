---
title: Vytváření a klonování Jupyter poznámkových bloků – Azure Notebooks Preview
description: Projekty Azure Notebooks ve verzi Preview spravují kolekci poznámkových bloků a souvisejících souborů, které můžete vytvořit nebo klonovat z jiného zdroje.
ms.topic: how-to
ms.date: 02/25/2019
ms.openlocfilehash: b29ff336c09a3bbf05a57c8a3a503b1875b76e54
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78360602"
---
# <a name="create-and-clone-projects-in-azure-notebooks-preview"></a>Vytváření a klonování projektů v Azure Notebooks Preview

Azure Notebooks uspořádá Jupyter poznámkové bloky a související soubory do logických skupin označovaných jako *projekty*. Jako kontejner, nejprve vytvořte projekt pak vytvořit nebo klonovat jeden nebo více poznámkových bloků v rámci složky společně s další soubory projektu. (Tento proces je znázorněn v tomto [kurzu](tutorial-create-run-jupyter-notebook.md).)

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Projekt také udržuje metadat a dalších nastavení konfigurace, které mají vliv na server, na které poznámkových bloků spuštění, včetně pokynů k vlastní instalaci a instalace balíčku. Další informace najdete v tématu [Správa a konfigurace projektů](configure-manage-azure-notebooks-projects.md).

## <a name="use-the-my-projects-dashboard"></a>Pomocí řídicího panelu projekty

Na řídicím panelu **Moje projekty** na `https://notebooks.azure.com/<userID>/projects` je místo, kde můžete zobrazit, spravovat a vytvářet projekty:

[![řídicí panel projekty v Azure Notebooks](media/my-projects-dashboard.png)](media/my-projects-dashboard.png#lightbox)

Co můžete dělat na řídicím panelu, závisí na tom, jestli jste přihlášení pomocí účtu, který vlastní ID uživatele:

| Příkaz | Dostupnost | Popis |
| --- | --- | --- |
| **Spouštěl** | Vlastník | Spustí server project server a otevře složku projektu v Jupyter. (Častěji, můžete nejprve přejděte do složky projektu a pak spustit Poznámkový blok z něj.) |
| **Stáhnout** | Všem uživatelům | Stáhne kopii vybraného projektu jako soubor ZIP. |
| **Sdílení** | Všem uživatelům | Zobrazí místní nabídku sdílení, pomocí které můžete získat adresu URL vybraného projektu, sdílet je se sociálními médii, poslat e-mail s adresou URL a získat kód HTML nebo Markdownu pro přístup pomocí znaku "Poznámkový blok" (viz část [získání znaku ke spuštění](#obtain-a-launch-badge)) s adresou URL. |
| **Odstranění** | Vlastník | Odstraní vybraný projekt. Tato operace se nedá vrátit zpět. |
| **Terminálovému** | Vlastník | Spustí server project server a pak otevře nové okno prohlížeče pomocí prostředí bash, terminálu pro tento server. |
| **+ Nový projekt** | Vlastník | Vytvoří nový projekt. Viz [Vytvoření nového projektu](#create-a-new-project). |
| **Nahrát úložiště GitHub** | Vlastník | Importuje projektu z Githubu. [Naimportujte projekt z GitHubu](#import-a-project-from-github). |
| **Klonovat** | Všem uživatelům | Zkopíruje vybraný projekt do vlastního účtu. Zobrazí výzvu k přihlášení Nepřihlášený. Viz [klonování projektu](#clone-a-project). |

### <a name="obtain-a-launch-badge"></a>Získání spuštění oznámení "BADGE"

Když použijete příkaz **share** a vyberete kartu **Vložit** , můžete zkopírovat kód HTML nebo Markdownu, který vytvoří znak "spustit Poznámkový blok":

![Spusťte Poznámkový blok oznámení "BADGE"](https://notebooks.azure.com/launch.png)

Pokud projekt poznámkových bloků Azure nemáte, můžete vytvořit odkaz, který duplicity z Githubu přímo pomocí následující šablony, kde nahradíte odpovídající uživatelského jména a názvy úložišť:

```html
<a href="https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>"><img src="https://notebooks.azure.com/launch.png" /></a>
```

```markdown
[![Azure Notebooks](https://notebooks.azure.com/launch.png)](https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>)
```

## <a name="create-a-new-project"></a>Vytvoření nového projektu

Při použití příkazu **+ Nový projekt** Azure Notebooks zobrazí místní nabídku **vytvořit nový projekt** . V tomto překryvném okně zadejte následující informace a pak vyberte **vytvořit**:

| Pole | Popis |
| --- | --- |
| Název projektu | Popisný název pro váš projekt, který poznámkových bloků Azure používá pro účely zobrazení. Například "projekt na Poznámkový blok". |
| ID projektu | Vlastní identifikátor, který se stal součástí adresy URL, kterou používáte pro sdílení projektu (formulář je `https://notebooks.azure.com/<user_id>/projects/<project_id>`). Toto ID může obsahovat pouze písmena, číslice a spojovníky, které jsou omezeny na 30 znaků a nesmí se jednat o [REZERVOVANÉ ID projektu](#reserved-project-ids). Pokud si nejste jisti, co se má použít, je běžné konvence používat malá písmena verzi název vašeho projektu, kde jsou mezery převedena na rozdělovníků, například "my-poznámkového bloku – projekt" (zkráceno, aby odpovídala délkový limit). |
| Public | Pokud nastavíte, může prakticky kdokoli s odkazem na přístup k projektu. Při vytváření projektu privátní, zrušte zaškrtnutí tohoto políčka. |
| Inicializovat tento projekt pomocí souboru README | Pokud je nastaveno, vytvoří výchozí soubor *Readme.MD* v projektu. Soubor *Readme.MD* je místo, kde v případě potřeby poskytnete dokumentaci pro svůj projekt. |

### <a name="reserved-project-ids"></a>ID rezervovaných projektů

Následující vyhrazená slova nelze použít samostatně jako ID projektu. Tato vyhrazená slova lze však použít jako součást delšího ID projektu.

| | | | | | |
| --- | --- | --- | --- | --- | --- |
| o produktu | account | správa | api | webový | učeben |
| content | řídicí panel | okno | dotaz | Nápověda | html |
| Domů | importovat | Knihovna | management | nových | poznámkového bloku |
| poznámkové bloky | pdf | preview | ceny | profile | search |
| status | podpora | test | | | |

Pokud se pokusíte použít jedno z těchto slov jako ID projektu, místní nabídky pro **Vytvoření nového projektu** a **projektu** označují, že "ID knihovny je vyhrazený identifikátor".

Vzhledem k tomu, že ID projektu je také součástí adresy URL projektu, může software blokování služby AD blokovat použití určitých klíčových slov, jako je například "inzerát". V takových případech použijte jiné slovo v ID projektu.

## <a name="import-a-project-from-github"></a>Import projektu z Githubu

Můžete snadno importovat celé veřejné úložiště GitHub jako projekt, včetně všech datových a *Readme.MD* souborů. Použijte příkaz **nahrát úložiště GitHub** , v překryvném okně zadejte následující podrobnosti a pak vyberte **importovat**:

| Pole | Popis |
| --- | --- |
| Úložiště GitHub | Název zdrojové úložiště na webu github.com. Pokud například chcete klonovat Jupyter poznámkové bloky pro Azure Cognitive Services v [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks), zadejte "Microsoft/vnímání-Services-poznámkové bloky".  |
| Rekurzivně klonování | Úložiště GitHub může obsahovat více podřízených úložištích. Tuto možnost nastavte, pokud chcete klonovat nadřazeným úložištěm a všechny jeho podřízené objekty. Vzhledem k tomu je možné u úložiště mít mnoho podřízených objektů, ponechejte tuto možnost zrušte Pokud si nejste jisti, že ji budete potřebovat. |
| Název projektu | Popisný název pro váš projekt, který poznámkových bloků Azure používá pro účely zobrazení. |
| ID projektu | Vlastní identifikátor, který se stal součástí adresy URL, kterou používáte pro sdílení projektu (formulář je `https://notebooks.azure.com/<user_id>/projects/<project_id>`). Toto ID může obsahovat pouze písmena, číslice a spojovníky, které jsou omezeny na 30 znaků a nesmí se jednat o [REZERVOVANÉ ID projektu](#reserved-project-ids). Pokud si nejste jisti, co se má použít, je běžné konvence používat malá písmena verzi název vašeho projektu, kde jsou mezery převedena na rozdělovníků, například "my-poznámkového bloku – projekt" (zkráceno, aby odpovídala délkový limit). |
| Public | Pokud nastavíte, může prakticky kdokoli s odkazem na přístup k projektu. Při vytváření projektu privátní, zrušte zaškrtnutí tohoto políčka. |

Import úložiště z Githubu, naimportuje se také jeho historie. Můžete použít standardní příkazy Gitu z terminálu potvrďte nové změny vložit změny z Githubu a tak dále.

## <a name="clone-a-project"></a>Naklonujte projekt

Klonování vytvoří kopii existujícího projektu do vlastního účtu, kde budete moct zprovoznit službu a změnit libovolné poznámkového bloku nebo jiného souboru v projektu. Taky můžete klonování vytvářet kopie vašich vlastních projektů, ve kterých uděláte experimentů nebo jiné práce bez narušení původní projekt.

Naklonujte projekt:

1. Na řídicím panelu **Moje projekty** klikněte pravým tlačítkem na požadovaný projekt a vyberte **klonovat** (Klávesová zkratka: c).

    ![Klonovací příkaz v místní nabídce projektu](media/clone-command.png)

1. V místní nabídce **klonovaného projektu** zadejte název a ID klonu a určete, zda je klon veřejný. Tato nastavení jsou stejná jako u [nového projektu](#create-a-new-project).

    ![Klonování projektu automaticky otevíraného okna](media/clone-project.png)

1. Po výběru tlačítka **klonování** Azure Notebooks přejít přímo na kopii.

## <a name="next-steps"></a>Další kroky

- [Prozkoumat ukázkové poznámkové bloky](azure-notebooks-samples.md)
- [Postupy: konfigurace a správa projektů](configure-manage-azure-notebooks-projects.md)
- [Postupy: Instalace balíčků z poznámkového bloku](install-packages-jupyter-notebook.md)
- [Postupy: zobrazení prezentace](present-jupyter-notebooks-slideshow.md)
- [Postupy: práce s datovými soubory](work-with-project-data-files.md)
- [Postupy: přístup k datovým prostředkům](access-data-resources-jupyter-notebooks.md)
- [Postupy: použití Azure Machine Learning](use-machine-learning-services-jupyter-notebooks.md)
