---
title: Vytváření a klonování poznámkových bloků Jupyter – náhled poznámkových bloků Azure
description: Projekty Azure Notebooks Preview spravují kolekci poznámkových bloků a souvisejících souborů, které můžete vytvořit nové nebo klonovat z jiného zdroje.
ms.topic: how-to
ms.date: 02/25/2019
ms.openlocfilehash: b29ff336c09a3bbf05a57c8a3a503b1875b76e54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280570"
---
# <a name="create-and-clone-projects-in-azure-notebooks-preview"></a>Vytváření a klonování projektů v náhledu poznámkových bloků Azure

Poznámkové bloky Azure uspořádají poznámkové bloky AUpYTER a související soubory do logických skupin *nazývaných projekty*. Projekt nejprve vytvoříte jako kontejner a potom vytvoříte nebo naklonujete jeden nebo více poznámkových bloků ve složce vedle jiných souborů projektu. (Tento proces je demonstrován v [kurzu](tutorial-create-run-jupyter-notebook.md).)

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Projekt také udržuje metadata a další nastavení konfigurace, která ovlivňují server, na kterém jsou spuštěny poznámkové bloky, včetně vlastních kroků instalace a instalace balíčku. Další informace naleznete v [tématu Správa a konfigurace projektů](configure-manage-azure-notebooks-projects.md).

## <a name="use-the-my-projects-dashboard"></a>Použití řídicího panelu Moje projekty

Řídicí panel Moje `https://notebooks.azure.com/<userID>/projects` **projekty** je místem, kde můžete zobrazit, spravovat a vytvářet projekty:

[![Řídicí panel Moje projekty v poznámkových blocích Azure](media/my-projects-dashboard.png)](media/my-projects-dashboard.png#lightbox)

Co můžete na řídicím panelu dělat, závisí na tom, jestli jste přihlášení pomocí účtu, který vlastní ID uživatele:

| Příkaz | K dispozici | Popis |
| --- | --- | --- |
| **Spustit** | Vlastník | Spustí projektový server a otevře složku projektu v jupyteru. (Častěji nejprve přejdete do složky projektu a pak odtud spustíte poznámkový blok.) |
| **Stáhnout** | Kdokoliv | Stáhne kopii vybraného projektu jako soubor ZIP. |
| **Sdílení** | Kdokoliv | Zobrazí vyskakovací okno sdílení, pomocí kterého můžete získat adresu URL vybraného projektu, sdílet na sociálních sítích, odeslat e-mail s adresou URL a získat kód HTML nebo Markdown s odznakem "launch notebook" (viz [získání odpalovacího odznaku)](#obtain-a-launch-badge)s adresou URL. |
| **Odstranit** | Vlastník | Odstraní vybraný projekt. Tato operace se nedá vrátit zpět. |
| **Terminálové** | Vlastník | Spustí projektový server a poté otevře nové okno prohlížeče s terminálem bash pro tento server. |
| **+ Nový projekt** | Vlastník | Vytvoří nový projekt. Viz [Vytvoření nového projektu](#create-a-new-project). |
| **Nahrání úložiště GitHub** | Vlastník | Importuje projekt z GitHubu. [Importprojektu z GitHubu](#import-a-project-from-github). |
| **Klonování** | Kdokoliv | Zkopíruje vybraný projekt do svého vlastního účtu. Zobrazí výzvu k přihlášení, pokud již nejste. Viz [Klonování projektu](#clone-a-project). |

### <a name="obtain-a-launch-badge"></a>Získání odpalovacího odznaku

Když použijete příkaz **Sdílet** a vyberete kartu **Vložit,** můžete zkopírovat kód HTML nebo Markdown, který vytvoří odznak "spustit poznámkový blok":

![Spuštění odznaku poznámkového bloku](https://notebooks.azure.com/launch.png)

Pokud nemáte projekt poznámkových bloků Azure, můžete vytvořit odkaz, který klonuje z GitHubu přímo pomocí následujících šablon a nahrazuje příslušné uživatelské jméno a názvy úložišť:

```html
<a href="https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>"><img src="https://notebooks.azure.com/launch.png" /></a>
```

```markdown
[![Azure Notebooks](https://notebooks.azure.com/launch.png)](https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>)
```

## <a name="create-a-new-project"></a>Vytvoření nového projektu

Když použijete příkaz **+ Nový projekt,** poznámkové bloky Azure zobrazí místní okno **Vytvořit nový projekt.** V tomto vyskakovacím okno zadejte následující informace a pak vyberte **Vytvořit**:

| Pole | Popis |
| --- | --- |
| Název projektu | Popisný název pro váš projekt, který poznámkové bloky Azure používá pro účely zobrazení. Například "Projekt poznámkového bloku". |
| ID projektu | Vlastní identifikátor, který se stane součástí adresy URL, kterou `https://notebooks.azure.com/<user_id>/projects/<project_id>`používáte ke sdílení projektu (formulář je ). Toto ID může používat pouze písmena, čísla a pomlčky, je omezeno na 30 znaků a nemůže být [vyhrazeným ID projektu](#reserved-project-ids). Pokud si nejste jisti, co použít, je běžnou konvencí použití malé verze názvu projektu, kde jsou mezery převedeny na pomlčky, například "my-notebook-project" (zkráceno v případě potřeby tak, aby odpovídalo limitu délky). |
| Public | Pokud je nastavena, umožňuje komukoli s odkazem přístup k projektu. Při vytváření soukromého projektu zrušte zaškrtnutí této možnosti. |
| Inicializovat tento projekt pomocí readme | Pokud je nastavena, vytvoří výchozí *README.md* soubor v projektu. Soubor *README.md* je místo, kde můžete v případě potřeby poskytnout dokumentaci k projektu. |

### <a name="reserved-project-ids"></a>Vyhrazená ID projektu

Následující vyhrazená slova nemohou být použita sami jako ID projektu. Tato vyhrazená slova však lze použít jako součást delší id projektu.

| | | | | | |
| --- | --- | --- | --- | --- | --- |
| o systému | account | správa | rozhraní api | blog | třída |
| content | řídicí panel | Prozkoumat | Faq | Nápověda | html |
| Domovská stránka | import | knihovna | správa | new | poznámkový blok |
| poznámkové bloky | Pdf | Náhled | Ceny | profil | search |
| status | podpora | test | | | |

Pokud se pokusíte použít jedno z těchto slov jako ID projektu, vyskakovací okno **Vytvořit nový projekt** a nastavení **projektu** označují, že ID knihovny je vyhrazený identifikátor.

Vzhledem k tomu, že ID projektu je také součástí adresy URL projektu, může software pro blokování reklam blokovat používání určitých klíčových slov, například reklamy. V takových případech použijte jiné slovo v ID projektu.

## <a name="import-a-project-from-github"></a>Import projektu z GitHubu

Můžete snadno importovat celé veřejné úložiště GitHub jako projekt, včetně všech dat a *README.md* souborů. Pomocí příkazu **Nahrát úložiště GitHub** uveďte ve vyskakovacím centru následující podrobnosti a vyberte **Importovat**:

| Pole | Popis |
| --- | --- |
| Úložiště GitHub | Název zdrojového úložiště na github.com. Chcete-li například naklonovat poznámkové bloky Jupyter pro služby Azure Cognitive Services na adrese [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks), zadejte "Microsoft/cognitive-services-notebooks".  |
| Klonovat rekurzivně | Úložiště GitHub umějí obsahovat více podřízených úložišť. Tuto možnost nastavte, pokud chcete klonovat nadřazené úložiště a všechny jeho podřízené objekty. Vzhledem k tomu, že úložiště může mít mnoho dětí, ponechejte tuto možnost vychlazenou, pokud nevíte, že ji potřebujete. |
| Název projektu | Popisný název pro váš projekt, který poznámkové bloky Azure používá pro účely zobrazení. |
| ID projektu | Vlastní identifikátor, který se stane součástí adresy URL, kterou `https://notebooks.azure.com/<user_id>/projects/<project_id>`používáte ke sdílení projektu (formulář je ). Toto ID může používat pouze písmena, čísla a pomlčky, je omezeno na 30 znaků a nemůže být [vyhrazeným ID projektu](#reserved-project-ids). Pokud si nejste jisti, co použít, je běžnou konvencí použití malé verze názvu projektu, kde jsou mezery převedeny na pomlčky, například "my-notebook-project" (zkráceno v případě potřeby tak, aby odpovídalo limitu délky). |
| Public | Pokud je nastavena, umožňuje komukoli s odkazem přístup k projektu. Při vytváření soukromého projektu zrušte zaškrtnutí této možnosti. |

Import úložiště z GitHubu také importuje jeho historii. Standardní příkazy Git z terminálu můžete použít k potvrzení nových změn, vyžádat změny z GitHubu a tak dále.

## <a name="clone-a-project"></a>Klonovat projekt

Klonování vytvoří kopii existujícího projektu ve vašem vlastním účtu, kde pak můžete spustit a upravit libovolný poznámkový blok nebo jiný soubor v projektu. Klonování můžete také použít k pořízení kopií vlastních projektů, ve kterých experimentujete nebo pracujete, aniž byste narušili původní projekt.

Chcete-li klonovat projekt:

1. Na řídicím panelu **Moje projekty** klikněte pravým tlačítkem myši na požadovaný projekt a vyberte **Clone** (klávesová zkratka: c).

    ![Příkaz Klonovat v kontextové nabídce projektu](media/clone-command.png)

1. V pop-up **klonování projektu** zadejte název a ID pro klon a zadejte, zda klon je veřejné. Tato nastavení jsou stejná jako u [nového projektu](#create-a-new-project).

    ![Pop-up projektu Klonování](media/clone-project.png)

1. Po výběru **tlačítka Klonování** poznámkové bloky Azure přejde přímo na kopii.

## <a name="next-steps"></a>Další kroky

- [Prozkoumejte ukázkové poznámkové bloky](azure-notebooks-samples.md)
- [Postup: Konfigurace a správa projektů](configure-manage-azure-notebooks-projects.md)
- [Postup: Instalace balíčků z poznámkového bloku](install-packages-jupyter-notebook.md)
- [Postup: Prezentace prezentace](present-jupyter-notebooks-slideshow.md)
- [Postup: Práce s datovými soubory](work-with-project-data-files.md)
- [Postup: Přístup k datovým prostředkům](access-data-resources-jupyter-notebooks.md)
- [Postup: Použití Azure Machine Learning](use-machine-learning-services-jupyter-notebooks.md)
