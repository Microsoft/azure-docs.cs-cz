---
title: Vytváření a klonování Jupyter poznámkových bloků – Azure Notebooks Preview
description: Projekty Azure Notebooks ve verzi Preview spravují kolekci poznámkových bloků a souvisejících souborů, které můžete vytvořit nebo klonovat z jiného zdroje.
ms.topic: how-to
ms.date: 02/25/2019
ms.openlocfilehash: 4a51f9b12ca24d16858b41357627ff26d233357f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86181488"
---
# <a name="create-and-clone-projects-in-azure-notebooks-preview"></a>Vytváření a klonování projektů v Azure Notebooks Preview

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Azure Notebooks uspořádá Jupyter poznámkové bloky a související soubory do logických skupin označovaných jako *projekty*. Nejprve vytvoříte projekt jako kontejner a pak vytvoříte nebo naklonujte jeden nebo několik poznámkových bloků v rámci složky vedle jiných souborů projektu. (Tento proces je znázorněn v tomto [kurzu](tutorial-create-run-jupyter-notebook.md).)

Projekt také uchovává metadata a další nastavení konfigurace, která mají vliv na server, na kterém se poznámkové bloky spouštějí, včetně kroků vlastního nastavení a instalace balíčku. Další informace najdete v tématu [Správa a konfigurace projektů](configure-manage-azure-notebooks-projects.md).

## <a name="use-the-my-projects-dashboard"></a>Použití řídicího panelu Moje projekty

Na řídicím panelu **Moje projekty** v `https://notebooks.azure.com/<userID>/projects` je místo, kde můžete zobrazit, spravovat a vytvářet projekty:

[![Řídicí panel Moje projekty v Azure Notebooks](media/my-projects-dashboard.png)](media/my-projects-dashboard.png#lightbox)

To, co můžete na řídicím panelu udělat, závisí na tom, jestli jste přihlášení pomocí účtu, který vlastní ID uživatele:

| Příkaz | K dispozici pro | Description |
| --- | --- | --- |
| **Spustit** | Vlastník | Spustí projektový Server a otevře složku projektu v Jupyter. (Častěji přejděte do složky projektu a pak z ní začněte Poznámkový blok.) |
| **Stáhnout** | Kdokoliv | Stáhne kopii vybraného projektu jako soubor ZIP. |
| **Sdílení** | Kdokoliv | Zobrazí místní nabídku sdílení, pomocí které můžete získat adresu URL vybraného projektu, sdílet je se sociálními médii, poslat e-mail s adresou URL a získat kód HTML nebo Markdownu pro přístup pomocí znaku "Poznámkový blok" (viz část [získání znaku ke spuštění](#obtain-a-launch-badge)) s adresou URL. |
| **Odstranit** | Vlastník | Odstraní vybraný projekt. Tato operace se nedá vrátit zpět. |
| **Terminál** | Vlastník | Spustí projektový Server a pak otevře nové okno prohlížeče s terminálem bash pro tento server. |
| **+ Nový projekt** | Vlastník | Vytvoří nový projekt. Viz [Vytvoření nového projektu](#create-a-new-project). |
| **Nahrát úložiště GitHub** | Vlastník | Importuje projekt z GitHubu. [Naimportujte projekt z GitHubu](#import-a-project-from-github). |
| **Klonování** | Kdokoliv | Zkopíruje vybraný projekt na vlastní účet. Vyzve vás k přihlášení, pokud ještě není. Viz [klonování projektu](#clone-a-project). |

### <a name="obtain-a-launch-badge"></a>Získání znaku ke spuštění

Když použijete příkaz **share** a vyberete kartu **Vložit** , můžete zkopírovat kód HTML nebo Markdownu, který vytvoří znak "spustit Poznámkový blok":

![Spustit odpalovací znak poznámkového bloku](https://notebooks.azure.com/launch.png)

Pokud nemáte Azure Notebooks projekt, můžete vytvořit odkaz, který klonuje z GitHubu přímo pomocí následujících šablon, přičemž nahradíte příslušné uživatelské jméno a názvy úložišť:

```html
<a href="https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>"><img src="https://notebooks.azure.com/launch.png" /></a>
```

```markdown
[![Azure Notebooks](https://notebooks.azure.com/launch.png)](https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>)
```

## <a name="create-a-new-project"></a>Vytvoření nového projektu

Při použití příkazu **+ Nový projekt** Azure Notebooks zobrazí místní nabídku **vytvořit nový projekt** . V tomto překryvném okně zadejte následující informace a pak vyberte **vytvořit**:

| Pole | Description |
| --- | --- |
| Název projektu | Popisný název projektu, který Azure Notebooks používá pro účely zobrazení. Například "projekt na Poznámkový blok". |
| ID projektu | Vlastní identifikátor, který se stal součástí adresy URL, kterou používáte pro sdílení projektu (formulář je `https://notebooks.azure.com/<user_id>/projects/<project_id>` ). Toto ID může obsahovat pouze písmena, číslice a spojovníky, které jsou omezeny na 30 znaků a nesmí se jednat o [REZERVOVANÉ ID projektu](#reserved-project-ids). Pokud si nejste jistí, co použít, obvyklá konvence je použití malé verze názvu projektu, kde jsou mezery převedeny na spojovníky, například "My-Poznámkový projekt" (zkráceně, pokud je to nutné pro přizpůsobení limitu délky). |
| Public | Pokud je nastaveno, umožňuje komukoli s odkazem přístup k projektu. Při vytváření privátního projektu zrušte zaškrtnutí tohoto políčka. |
| Inicializovat tento projekt pomocí souboru READme | Pokud je nastaveno, vytvoří výchozí soubor *Readme.MD* v projektu. Soubor *Readme.MD* je místo, kde v případě potřeby poskytnete dokumentaci pro svůj projekt. |

### <a name="reserved-project-ids"></a>ID rezervovaných projektů

Následující vyhrazená slova nelze použít samostatně jako ID projektu. Tato vyhrazená slova lze však použít jako součást delšího ID projektu.

- o systému
- account
- správa
- api
- blog
- třída
- obsah
- dashboard
- okno
- dotaz
- Nápověda
- html
- Domovská stránka
- import
- knihovna
- správa
- new
- poznámkový blok
- poznámkové bloky
- formátu
- preview
- stanov
- profil
- search
- status
- podpora
- test

Pokud se pokusíte použít jedno z těchto slov jako ID projektu, místní nabídky pro **Vytvoření nového projektu** a **projektu** označují, že "ID knihovny je vyhrazený identifikátor".

Vzhledem k tomu, že ID projektu je také součástí adresy URL projektu, může software blokování služby AD blokovat použití určitých klíčových slov, jako je například "inzerát". V takových případech použijte jiné slovo v ID projektu.

## <a name="import-a-project-from-github"></a>Import projektu z GitHubu

Můžete snadno importovat celé veřejné úložiště GitHub jako projekt, včetně všech datových a *Readme.MD* souborů. Použijte příkaz **nahrát úložiště GitHub** , v překryvném okně zadejte následující podrobnosti a pak vyberte **importovat**:

| Pole | Description |
| --- | --- |
| Úložiště GitHub | Název zdrojového úložiště v github.com. Pokud například chcete klonovat Jupyter poznámkové bloky pro Azure Cognitive Services v [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks) , zadejte "Microsoft/vnímání-Services-poznámkové bloky".  |
| Rekurzivní klonování | Úložiště GitHub můžou obsahovat několik podřízených úložišť. Tuto možnost nastavte, pokud chcete klonovat nadřazené úložiště a všechny jeho podřízené objekty. Vzhledem k tomu, že je možné, že úložiště má mnoho podřízených objektů, nechte tuto možnost nevyjasná, pokud víte, že ji nepotřebujete. |
| Název projektu | Popisný název projektu, který Azure Notebooks používá pro účely zobrazení. |
| ID projektu | Vlastní identifikátor, který se stal součástí adresy URL, kterou používáte pro sdílení projektu (formulář je `https://notebooks.azure.com/<user_id>/projects/<project_id>` ). Toto ID může obsahovat pouze písmena, číslice a spojovníky, které jsou omezeny na 30 znaků a nesmí se jednat o [REZERVOVANÉ ID projektu](#reserved-project-ids). Pokud si nejste jistí, co použít, obvyklá konvence je použití malé verze názvu projektu, kde jsou mezery převedeny na spojovníky, například "My-Poznámkový projekt" (zkráceně, pokud je to nutné pro přizpůsobení limitu délky). |
| Public | Pokud je nastaveno, umožňuje komukoli s odkazem přístup k projektu. Při vytváření privátního projektu zrušte zaškrtnutí tohoto políčka. |

Import úložiště z GitHubu také importuje historii. Pomocí standardních příkazů git z terminálu můžete potvrdit nové změny, přijímat změny z GitHubu a tak dále.

## <a name="clone-a-project"></a>Klonovat projekt

Klonování vytvoří kopii existujícího projektu ve vlastním účtu, kde můžete následně spustit a upravit kterýkoli Poznámkový blok nebo jiný soubor v projektu. Klonování můžete použít také k vytvoření kopií vlastních projektů, ve kterých provedete experimenty nebo jiné práce bez narušení původního projektu.

Naklonování projektu:

1. Na řídicím panelu **Moje projekty** klikněte pravým tlačítkem na požadovaný projekt a vyberte **klonovat** (Klávesová zkratka: c).

    ![Příkaz klonování v kontextové nabídce projektu](media/clone-command.png)

1. V místní nabídce **klonovaného projektu** zadejte název a ID klonu a určete, zda je klon veřejný. Tato nastavení jsou stejná jako u [nového projektu](#create-a-new-project).

    ![Automaticky otevírané okno klonovat projekt](media/clone-project.png)

1. Po výběru tlačítka **klonování** Azure Notebooks přejít přímo na kopii.

## <a name="next-steps"></a>Další kroky

- [Prozkoumat ukázkové poznámkové bloky](azure-notebooks-samples.md)
- [Postupy: konfigurace a správa projektů](configure-manage-azure-notebooks-projects.md)
- [Postupy: Instalace balíčků z poznámkového bloku](install-packages-jupyter-notebook.md)
- [Postupy: zobrazení prezentace](present-jupyter-notebooks-slideshow.md)
- [Postupy: práce s datovými soubory](work-with-project-data-files.md)
- [Postupy: přístup k datovým prostředkům](access-data-resources-jupyter-notebooks.md)
- [Postupy: použití Azure Machine Learning](use-machine-learning-services-jupyter-notebooks.md)
