---
title: Konfigurace a Správa Azure Notebooks ve verzi Preview
description: Naučte se spravovat metadata projektu, soubory projektu, prostředí projektu a kroky nastavení prostřednictvím uživatelského rozhraní Azure Notebooks a přímého přístupového terminálu.
ms.topic: how-to
ms.date: 02/28/2020
ms.custom: devx-track-python
ms.openlocfilehash: 7674c2151922d26e069b5cd285cb311d7a18fa98
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87852799"
---
# <a name="manage-and-configure-projects-in-azure-notebooks-preview"></a><a id="manage-and-configure-projects" /> Správa a konfigurace projektů v Azure Notebooks Preview

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Projekt ve verzi Preview v Azure Notebooks je v podstatě konfigurací základního virtuálního počítače se systémem Linux, ve kterém se spouští poznámkové bloky Jupyter spolu se složkou souborů a popisnými metadaty. 

Řídicí panel projekt v Azure Notebooks umožňuje spravovat soubory a jinak konfigurovat vlastnosti projektu:

- Výpočetní vrstva, na které se projekt spouští, což může být úroveň Free nebo virtuální počítač Azure.
- Metadata projektu, která obsahují název, popis, identifikátor, který se používá při sdílení projektu a zda je projekt veřejný nebo soukromý.
- Poznámkový blok, data a další soubory projektu, které můžete spravovat stejně jako jakýkoli jiný systém souborů.
- Prostředí projektu, které spravujete prostřednictvím spouštěcích skriptů nebo přímo prostřednictvím terminálu.
- Protokoly, ke kterým přistupujete prostřednictvím terminálu.

> [!Note]
> Níže popsané funkce pro správu a konfiguraci jsou k dispozici pouze pro vlastníka projektu, který původně vytvořil projekt. Projekt můžete ale klonovat na vlastní účet. v takovém případě se stanete vlastníkem a můžete projekt nakonfigurovat podle potřeby.

Azure Notebooks spustí základní virtuální počítač při každém spuštění poznámkového bloku nebo jiného souboru. Server automaticky ukládá soubory a ukončí se po 60 minutách nečinnosti. Server můžete také kdykoli zastavit pomocí příkazu pro **vypnutí** (Klávesová zkratka: h).

## <a name="compute-tier"></a>Úroveň výpočetních prostředků

Ve výchozím nastavení jsou projekty spouštěny na **bezplatné výpočetní** úrovni, což je omezeno na 4 GB paměti a 1 GB dat, aby nedocházelo k zneužití. Tato omezení můžete obejít a zvýšit výpočetní výkon pomocí jiného virtuálního počítače, který jste zřídili v rámci předplatného Azure. Další informace najdete v tématu [použití Virtual Machines pro datové vědy](use-data-science-virtual-machine.md).

## <a name="edit-project-metadata"></a>Upravit metadata projektu

Na řídicím panelu projekt vyberte **nastavení projektu**a pak vyberte kartu **informace** , která obsahuje metadata projektu, jak je popsáno v následující tabulce. Metadata projektu můžete kdykoli změnit.

| Nastavení | Popis |
| --- | --- |
| Název projektu | Popisný název projektu, který Azure Notebooks používá pro účely zobrazení. Například "Hello World v Pythonu". |
| ID projektu | Vlastní identifikátor, který se stal součástí adresy URL, kterou používáte pro sdílení projektu. Toto ID může obsahovat pouze písmena, číslice a spojovníky, které jsou omezeny na 30 znaků a nesmí se jednat o [REZERVOVANÉ ID projektu](create-clone-jupyter-notebooks.md#reserved-project-ids). Pokud si nejste jistí, co použít, obvyklá konvence je použití malé verze názvu projektu, kde jsou mezery převedeny na spojovníky, například "My-Poznámkový projekt" (zkráceně, pokud je to nutné pro přizpůsobení limitu délky). |
| Veřejný projekt | Pokud je nastaveno, umožňuje komukoli s odkazem přístup k projektu. Při vytváření privátního projektu zrušte zaškrtnutí tohoto políčka. |
| Skrýt klony | Pokud je nastaveno, ostatní uživatelé neuvidí seznam klonů, které byly vytvořeny pro tento projekt. Skrývání klonů je užitečné pro projekty, které jsou sdíleny s mnoha lidmi, kteří nejsou součástí stejné organizace, například při použití poznámkového bloku pro výuku třídy. |

> [!Important]
>
> Změna ID projektu zruší platnost všech odkazů na projekt, které jste mohli dříve sdílet.

## <a name="manage-project-files"></a>Spravovat soubory projektu

Řídicí panel projekt zobrazuje obsah systému složek projektu. Ke správě těchto souborů můžete použít různé příkazy.

### <a name="create-new-files-and-folders"></a>Vytvořit nové soubory a složky

Příkaz **+ New** (Klávesová zkratka: n) vytvoří nové soubory nebo složky. Při použití příkazu nejdřív vyberte typ položky, kterou chcete vytvořit:

| Typ položky | Description | Chování příkazu |
| --- | --- | --- |
| **Poznámkový blok** | Jupyter Poznámkový blok | Zobrazí místní nabídku, ve které zadáte název souboru a jazyk poznámkového bloku. |
| **Složka** | Podsložka | Vytvoří pole pro úpravy v seznamu souborů projektu, do kterého zadáte název složky. |
| **Prázdný soubor** | Soubor, do kterého můžete ukládat obsah, jako je text, data atd. | Vytvoří pole pro úpravy v seznamu souborů projektu, do kterého zadáte název souboru. |
| **Markdown** | Markdownu soubor. | Vytvoří pole pro úpravy v seznamu souborů projektu, do kterého zadáte název souboru. |

### <a name="upload-files"></a>Nahrání souborů

Příkaz **nahrát** nabízí dvě možnosti, jak importovat data z jiných umístění: **z adresy URL** a **z počítače**. Další informace najdete v tématu [práce s datovými soubory v projektech Azure notebook](work-with-project-data-files.md).

### <a name="select-file-specific-commands"></a>Výběr příkazů specifických pro soubor

Jednotlivé položky v seznamu souborů projektu poskytují příkazy prostřednictvím místní nabídky po kliknutí pravým tlačítkem myši:

![Příkazy v místní nabídce souboru](media/project-file-commands.png)

| Příkaz | Klávesová zkratka | Akce |
| --- | --- | --- |
| Spustit | r (nebo klikněte na) | Spustí soubor poznámkového bloku. Další typy souborů jsou otevřeny k zobrazení.  |
| Kopírovat odkaz | y | Zkopíruje odkaz na soubor do schránky. |
| Spuštění v Jupyter Lab | j | Spustí Poznámkový blok v JupyterLab, což je více než Jupyter normálně poskytované rozhraní orientované na vývojáře. |
| Preview | p | Otevře náhled souboru HTML. pro poznámkové bloky je náhled vykreslování poznámkového bloku jen pro čtení. Další informace najdete v části [Preview](#preview) . |
| Upravit soubor | Mohu | Otevře soubor pro úpravy. |
| Stáhnout | d | Stáhne soubor zip, který obsahuje soubor nebo obsah složky. |
| přejmenování | pro | Zobrazí výzvu k zadání nového názvu souboru nebo složky. |
| Odstranit | x | Vyzve k potvrzení a pak trvale odstraní soubor z projektu. Odstranění nelze vrátit zpět. |
| Přesunout | m | Přesune soubor do jiné složky ve stejném projektu. |

#### <a name="preview"></a>Preview

Náhled souboru nebo poznámkového bloku je zobrazení obsahu jen pro čtení. spuštěné buňky poznámkového bloku jsou zakázané. Verze Preview je zobrazená komukoli, který má odkaz na soubor nebo Poznámkový blok, ale přihlásil se k Azure Notebooks. Jakmile se uživatel přihlásí, může Poznámkový blok klonovat na svůj vlastní účet nebo si ho může stáhnout do svého místního počítače.

Stránka Preview podporuje několik příkazů panelu nástrojů s klávesovými zkratkami:

| Příkaz | Klávesová zkratka | Akce |
| --- | --- | --- |
| Sdílení | s | Zobrazí místní nabídku sdílení, ze které můžete získat odkaz, sdílet na sociálních médiích, získat kód HTML pro vložení a poslat e-mail. |
| Klonování | c  | Naklonujte Poznámkový blok na svůj účet. |
| Spustit | r | Spustí Poznámkový blok, pokud je povolený. |
| Stáhnout | d | Stáhne kopii poznámkového bloku. |

## <a name="configure-the-project-environment"></a>Konfigurace prostředí projektu

Existují tři způsoby, jak nakonfigurovat prostředí základního virtuálního počítače, ve kterém jsou poznámkové bloky spuštěny:

- Zahrnutí jednorázového inicializačního skriptu
- Nastavení prostředí projektu použijte k zadání kroků instalace
- Přístup k virtuálnímu počítači pomocí terminálu.

Všechny formy konfigurace projektu jsou aplikovány při každém spuštění virtuálního počítače, a tak ovlivní všechny poznámkové bloky v rámci projektu.

### <a name="one-time-initialization-script"></a>Jednorázový skript pro inicializaci

První Azure Notebooks vytvoří server pro projekt, vyhledá soubor v projektu s názvem *aznbsetup.sh*. Pokud je tento soubor k dispozici, Azure Notebooks ho spustí. Výstup skriptu je uložen ve složce projektu jako *. aznbsetup. log*.

### <a name="environment-setup-steps"></a>Kroky pro nastavení prostředí

Nastavení prostředí projektu můžete použít k vytvoření jednotlivých kroků, které konfigurují prostředí.

Na řídicím panelu projekt vyberte možnost **nastavení projektu**, pak vyberte kartu **prostředí** , ve které chcete přidat, odebrat a změnit instalační kroky pro projekt:

![Místní nabídka nastavení projektu s vybranou kartou prostředí](media/project-settings-environment-steps.png)

Pokud chcete přidat krok, nejdřív vyberte **+ Přidat**a pak v rozevíracím seznamu **operace** vyberte typ kroku:

![Výběr operace pro nový krok nastavení prostředí](media/project-settings-environment-details.png)

Informace, které pak provedete, závisí na zvoleném typu operace:

- **Requirements.txt**: v druhém rozevíracím seznamu vyberte *requirements.txt* soubor, který je již v projektu. Pak vyberte verzi Pythonu z třetího rozevíracího seznamu, který se zobrazí. Když použijete soubor *requirements.txt* , Azure Notebooks `pip install -r` při spuštění serveru poznámkového bloku se spustí se souborem *requirements.txt* . Nemusíte explicitně instalovat balíčky přímo do samotného poznámkového bloku.

- **Skript prostředí**: v druhém rozevíracím seznamu Vyberte skript prostředí bash shell (obvykle soubor s příponou *. sh* ), který obsahuje příkazy, které chcete spustit k inicializaci prostředí.

- **Environment. yml**: v druhém rozevíracím seznamu vyberte soubor Environments *. yml* pro projekty Pythonu s použitím prostředí conda.

   > [!WARNING]
   > Vzhledem k tomu, že se jedná o službu ve verzi Preview, se v současnosti jedná o známý problém, kdy se `Environment.yml` nastavení pro váš projekt nepoužije podle očekávání. Projekt a poznámkové bloky Jupyter v v současné době nenačte zadaný soubor prostředí.

Až skončíte s přidáváním kroků, vyberte **Uložit**.

### <a name="use-the-terminal"></a>Použití terminálu

Na řídicím panelu projekt otevře příkaz **terminálu** terminál pro Linux, který vám poskytne přímý přístup k serveru. V rámci terminálu můžete stahovat data, upravovat a spravovat soubory, kontrolovat procesy a dokonce používat nástroje jako VI a nano.

> [!Note]
> Pokud máte spouštěcí skripty v prostředí projektu, otevření terminálu může zobrazit zprávu oznamující, že instalace stále probíhá.

V terminálu můžete vydat všechny standardní příkazy systému Linux. Můžete také použít `ls` v domovské složce k zobrazení různých prostředí, která existují na virtuálním počítači, například *anaconda2_501*, *anaconda3_420*, *anaconda3_501*, *IfSharp*a *R*, spolu se složkou *projektu* , který obsahuje projekt:

![Terminál projektu v Azure Notebooks](media/project-terminal.png)

Chcete-li ovlivnit konkrétní prostředí, změňte nejprve adresáře do této složky prostředí.

V prostředích Pythonu můžete najít `pip` a `conda` ve složce *bin* každého prostředí. Pro prostředí můžete také použít předdefinované aliasy:

```bash
# Anaconda 2 5.3.0/Python 2.7: python27
python27 -m pip install <package>

# Anaconda 3 4.2.0/Python 3.5: python35
python35 -m pip install <package>

# Anaconda 3 5.3.0/Python 3.6: python36
python36 -m pip install <package>
```

Změny provedené na serveru platí pouze pro aktuální relaci s výjimkou souborů a složek, které vytvoříte ve složce *projektu* samotné. Například úprava souboru ve složce projektu je trvalá mezi relacemi, ale balíčky s nejsou `pip install` .

> [!Note]
> Použijete `python` -li nebo `python3` , vyvoláte systémem nainstalované verze Pythonu, které se nepoužívají pro poznámkové bloky. Nemáte oprávnění pro operace, jako je `pip install` například, nezapomeňte použít aliasy specifické pro danou verzi.

## <a name="access-notebook-logs"></a>Přístup k protokolům poznámkových bloků

Pokud při spuštění poznámkového bloku narazíte na problémy, uloží se výstup z Jupyter do složky s názvem *. NB. log*. K těmto protokolům můžete přistupovat pomocí příkazu **terminálu** nebo řídicího panelu projekt.

Obvykle když spouštíte Jupyter místně, je možné, že jste ho spustili z okna terminálu. V okně terminálu se zobrazí výstup, jako je například stav jádra.

Pokud chcete zobrazit protokoly, použijte následující příkaz v terminálu:

```bash
cat .nb.log
```

Příkaz můžete použít také z buňky kódu v poznámkovém bloku Python:

```bash
!cat .nb.log
```

## <a name="next-steps"></a>Další kroky

- [Postupy: práce s datovými soubory projektu](work-with-project-data-files.md)
- [Přístup k datům v cloudu v poznámkovém bloku](access-data-resources-jupyter-notebooks.md)
