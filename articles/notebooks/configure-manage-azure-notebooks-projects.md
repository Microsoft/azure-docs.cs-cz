---
title: Konfigurovat a spravovat projekty Azure poznámkového bloku
description: Jak spravovat metadat projektu, soubory projektu, projektu prostředí a kroky instalace přes uživatelské rozhraní služby Azure poznámkových bloků a přímý přístup terminálu.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 35dd6ff1-a14a-4a2e-b173-6d8467de3e89
ms.service: notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2019
ms.author: kraigb
ms.openlocfilehash: 31cbe2e62582ae810d165ddef5db6a20c52ff050
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54847539"
---
# <a name="manage-and-configure-projects"></a>Správa a konfigurace projektů

Projekt v poznámkových bloků Azure je v podstatě konfiguraci základní virtuální počítač s Linuxem ve kterém je spuštěný poznámkové bloky Jupyter, spolu s složku souborů a popisnými metadaty. Řídicí panel projekt v poznámkových bloků Azure umožňuje spravovat soubory a jinak konfigurovat vlastnosti projektu:

- Úrovně výpočetních na kterém běží projekt, který může být na úrovni free nebo virtuálním počítači Azure.
- Projekt metadat, která obsahuje název, popis, identifikátor, který se používá při sdílení projektu a zda je projekt veřejné nebo soukromé.
- Poznámkový blok v projektu, dat a další soubory, které můžete spravovat jako jakéhokoli souborové systému.
- Prostředí projektu, které lze spravovat prostřednictvím spouštění skriptů nebo přímo přes terminál.
- Protokoly, které přistupujete prostřednictvím terminálu.

> [!Note]
> Správa a konfigurace funkcí je zde popsáno, jsou k dispozici pouze vlastník projektu, který původně vytvořil projektu. Klonování projektu z do vlastního účtu může, ale v takovém případě stát vlastníkem a můžete podle potřeby nakonfigurujte projekt.

Poznámkových bloků Azure spustí základní virtuální počítač se při každém spuštění poznámkového bloku nebo jiného souboru. Server automaticky ukládá soubory a ukončí po 60 minutách nečinnosti. Taky můžete zastavit server kdykoli s **vypnutí** příkazu (Klávesová zkratka: h).

## <a name="compute-tier"></a>Služba COMPUTE úrovně

**Spustit** rozevíracího seznamu na řídicím panelu Projekt slouží k výběru výpočetní úroveň, na kterém běží projektu. Ve výchozím nastavení, spouštět projekty **bezplatné Compute** úroveň, která je omezena na 4 GB paměti a 1 GB dat, aby se zabránilo zneužití:

![COMPUTE úrovně rozevíracího seznamu na řídicím panelu Projekt](media/project-compute-tier-list.png)

Tato omezení můžete obejít a použít jiný virtuální počítač, který zřídíte v předplatném Azure. Jupyter je také nutné nainstalovat na tomto virtuálním počítači. Image virtuálního počítače pro datové vědy jsou vhodná rozhodnutí, protože patří mezi ně Jupyter ve výchozím nastavení.

Můžete připojit k jakékoli vhodně nakonfigurovaných virtuálních počítačů Azure pomocí **přímé Compute** možnost v rozevíracím seznamu. Výběrem této možnosti vás vyzve k zadání názvu (Chcete-li zobrazit v seznamu), IP adresu Virtuálního počítače a port (obvykle 8000, výchozí port, na které naslouchá JupyterHub) a přihlašovací údaje virtuálního počítače:

![Výzva ke shromažďování informací serveru pro možnost přímého Compute](media/project-compute-tier-direct.png)

Pokud jsou splněny následující podmínky, rozevírací seznam také zobrazuje [dat virtuálního počítače VĚDY](/azure/machine-learning/data-science-virtual-machine) instancí. (Pokud nejsou splněny některé z těchto podmínek, můžete se připojit k datové VĚDY použít možnost Compute s přímým přístupem a zadat hodnoty získané z portálu Azure portal.)

- Jste se přihlásili do poznámkových bloků Azure pomocí účtu, který používá Azure Active Directory (AAD), jako je například účet společnosti.
- Váš účet připojený k předplatnému Azure.
- Máte jeden nebo více virtuálních počítačů v tomto předplatném s alespoň čtečky přístup, použijte virtuální počítač pro datové vědy pro Linux (Ubuntu) image.

![Instance data Science virtuálních počítačů v rozevíracím seznamu v řídicím panelu Projekt](media/project-compute-tier-dsvm.png)

Při výběru DSVM instance poznámkových bloků Azure může výzvu pro konkrétní počítač pověření použitá při vytváření virtuálního počítače.

Pokud chcete vytvořit novou instanci DSVM, postupujte podle pokynů [vytvoření virtuálního počítače s Ubuntu Data Science](/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro). Můžete *musí* použít **virtuální počítač pro datové vědy pro Linux (Ubuntu)** image, protože poznámkových bloků Azure nezobrazí datové, které používají Image Windows nebo CentOS.

## <a name="edit-project-metadata"></a>Úprava metadat projektu

Na řídicím panelu Projekt, vyberte **nastavení projektu**a pak **informace** karta, který obsahuje metadata projektu, jak je popsáno v následující tabulce. Metadata projektu můžete změnit kdykoli.

| Nastavení | Popis |
| --- | --- |
| Název projektu | Popisný název pro váš projekt, který poznámkových bloků Azure používá pro účely zobrazení. Například "Hello World v Pythonu". |
| ID projektu | Vlastní identifikátor, který se stane součástí adresy URL, které umožňují sdílet projekt (je formulář `https://notebooks.azure.com/<user_id>/projects/<project_id>`). Toto ID můžete použít jenom písmena, číslice a pomlčky a je omezená na 30 znaků. Pokud si nejste jisti, co se má použít, je běžné konvence, používat malá písmena verzi název vašeho projektu, kde mezery jsou převedena na spojovníky, například "My Project Name" změní na "my-project-name". |
| Veřejného projektu | Pokud nastavíte, může prakticky kdokoli s odkazem na přístup k projektu. Při vytváření projektu privátní, zrušte zaškrtnutí tohoto políčka. |
| Skrýt klony | Pokud je nastaveno, ostatní uživatelé neuvidí seznamu duplicity, které mají za cíl pro tento projekt. Skrytí duplicit je užitečné pro projekty, které jsou sdíleny s mnoha lidmi, kteří nejsou součástí stejné organizaci, jako například při použití poznámkového bloku pro vyučují třídu. |

> [!Important]
>
> Změna ID projektu zruší platnost odkazy na projekt, který jste pravděpodobně dříve sdíleli.

## <a name="manage-project-files"></a>Spravovat soubory projektu

Řídicí panel Projekt zobrazuje obsah složky systému projektu. Můžete použít různé příkazy ke správě těchto souborů.

### <a name="create-new-files-and-folders"></a>Vytvoření nových souborů a složek

**+ Nová** příkazu (Klávesová zkratka: n) vytvoří nové soubory nebo složky. Při použití příkazu, nejprve vyberte typ položky vytvořit:

| Typ položky | Popis | Chování příkazu |
| --- | --- | --- |
| **Poznámkový blok** | Poznámkový blok Jupyter | Zobrazí automaticky otevírané okno, ve kterém můžete zadat název souboru a jazyk poznámkového bloku. |
| **Složka** | Podsložky | Vytvoří pole v seznamu souboru projektu, ve kterém zadáte název složky. |
| **Prázdný soubor** | Soubor, do kterého můžete ukládat jakýkoli obsah, jako je text, data atd. | Vytvoří pole v seznamu souboru projektu, ve kterém zadáte název souboru. |
| **Markdown** | Soubor Markdown. | Vytvoří pole v seznamu souboru projektu, ve kterém zadáte název souboru. |

### <a name="upload-files"></a>Nahrání souborů

**Nahrát** příkaz poskytuje dvě možnosti, jak importovat data z jiných míst: **Z adresy URL** a **z počítače**. Další informace najdete v tématu [práce s datovými soubory v projektech poznámkového bloku Azure](work-with-project-data-files.md).

### <a name="select-file-specific-commands"></a>Vyberte soubor konkrétní příkazy

Každá položka v seznamu souborů v projektu poskytuje příkazů pomocí místní nabídky klikněte pravým tlačítkem na:

![Příkazy v místní nabídce Soubor](media/project-file-commands.png)

| Příkaz | Klávesová zkratka | Akce |
| --- | --- | --- |
| Spusťte | r (nebo klikněte na tlačítko) | Spustí soubor poznámkového bloku. Jiné typy souborů jsou otevřené pro zobrazení.  |
| Kopírovat odkaz | Y | Odkaz se zkopíruje do souboru do schránky. |
| Spustit v testovacím prostředí Jupyter | j | Spuštění poznámkového bloku v JupyterLab, což je více vývojářů objektově orientovaného rozhraní než Jupyter obvykle poskytuje. |
| Preview | p | Otevře se v HTML verzi preview souboru. verze preview pro poznámkové bloky, je jen pro čtení vykreslování poznámkového bloku. Další informace najdete v tématu [ve verzi Preview](#preview) oddílu. |
| Upravte soubor | Můžu | Otevře soubor pro úpravy. |
| Ke stažení | d | Stáhne soubor zip, který obsahuje soubor nebo obsah složky. |
| Přejmenovat | a | Výzvy k zadání nového názvu souboru nebo složky. |
| Odstranění | x | Zobrazí výzvu k potvrzení a pak soubor trvale odebere z projektu. Odstranění se nedá vrátit zpět. |
| Přesunout | min. | Přesune soubor do jiné složky ve stejném projektu. |

#### <a name="preview"></a>Preview

Náhled souboru nebo Poznámkový blok je zobrazení jen pro čtení obsahu; spuštění poznámkového bloku buněk je zakázaná. Ve verzi preview se zobrazí všem uživatelům, který obsahuje odkaz na soubor nebo Poznámkový blok ale neuzavřela do poznámkových bloků Azure. Po přihlášení uživatele můžete naklonovat ho na svůj vlastní účet, nebo ho můžou stahovat do místního počítače.

Na stránce ve verzi preview podporuje několik nástrojů příkazů s klávesovými zkratkami:

| Příkaz | Klávesová zkratka | Akce |
| --- | --- | --- |
| Sdílení | s | Zobrazí sdílení překryvné okno, ze kterého lze získat odkaz, sdílení na sociálních médií, získat HTML pro vkládání a odeslat e-mailu. |
| Klon | c  | Naklonujte ho na svůj účet. |
| Spusťte | r | Pokud máte oprávnění k tomu, spustí Poznámkový blok. |
| Ke stažení | d | Stáhne kopii poznámkového bloku. |

## <a name="configure-the-project-environment"></a>Konfigurace prostředí projektu

Existují tři způsoby, jak nakonfigurovat prostředí základní virtuální počítač, ve kterém je spuštěný vašich poznámkových bloků:

- Zahrnují skript jednorázová inicializace
- Nastavení prostředí projektu použijte k určení pokynů k instalaci
- Přístup k virtuálnímu počítači pomocí terminálu.

Všechny formy projektové konfigurace se použijí pokaždé, když se virtuální počítač se spustí a proto má vliv na všech poznámkových bloků v rámci projektu.

### <a name="one-time-initialization-script"></a>Jednorázová inicializace skriptu

Vytvoří poznámkových bloků Azure první server pro projekt, hledá soubor v projektu volá *aznbsetup.sh*. Pokud tento soubor je k dispozici, poznámkových bloků Azure ho spustí. Výstup skriptu je uložen ve složce projektu jako *. aznbsetup.log*.

### <a name="environment-setup-steps"></a>Kroky nastavení prostředí

Nastavení prostředí projektu slouží k vytvoření jednotlivé kroky, které nakonfigurují prostředí.

Na řídicím panelu Projekt, vyberte **nastavení projektu**a pak **prostředí** kartu, ve kterém přidat, odebrat a upravit kroky nastavení projektu:

![Místní nastavení projektu s vybraná karta prostředí](media/project-settings-environment-steps.png)

Chcete-li přidat, vyberte nejdřív **+ přidat**, vyberte odpovídající typ v **operace** rozevíracího seznamu:

![Selektor operace pro nový krok nastavení prostředí](media/project-settings-environment-details.png)

Informace, které pak projektu závisí na typu operace, kterou jste zvolili:

- **Soubor Requirements.txt**: Ve druhém seznamu, rozevíracího seznamu vyberte *souboru requirements.txt* soubor, který je již v projektu. Vyberte ze seznamu třetí rozevíracího seznamu, který se zobrazí verze Pythonu. Pomocí *souboru requirements.txt* souborů, spuštění poznámkových bloků Azure `pip install -r` s *souboru requirements.txt* souboru při spuštění serveru poznámkového bloku. Není nutné explicitně instalace balíčků z v rámci samotného poznámkového bloku.

- **Skript prostředí**: Ve druhém seznamu, rozevíracího seznamu vyberte skript prostředí bash v projektu (obvykle soubor s *.sh* rozšíření), která obsahuje všechny příkazy, které chcete spustit inicializace prostředí.

- **Environment.yml**: Ve druhém seznamu, rozevíracího seznamu vyberte *environments.yml* souborů pro projekty v Pythonu pomocí prostředí conda.

Až dokončíte přidávání kroků, vyberte **Uložit**.

### <a name="use-the-terminal"></a>Použití terminálu

Na řídicím panelu Projekt **terminálu** příkaz otevře terminálu Linuxu, která poskytuje přímý přístup k serveru. V terminálu můžete stáhnout data, upravit nebo spravovat soubory, zkontrolujte procesy a dokonce i pomocí nástrojů, jako je editoru vi nebo nano.

> [!Note]
> Pokud máte spouštění skriptů v prostředí vašeho projektu, otevřete terminál může zobrazit zpráva oznamující, že tento instalační program je stále probíhá.

Můžete použít všechny standardní Linuxové příkazy, v terminálu. Můžete také použít `ls` v domovskou složku zobrazíte různá prostředí, které existují na virtuálním počítači, jako například *anaconda2_501*, *anaconda3_420*, *anaconda3_501*, *IfSharp*, a *R*, spolu s *projektu* složku obsahující projekt:

![Projekt terminálu v poznámkových bloků Azure](media/project-terminal.png)

Pokud chcete mít vliv na konkrétní prostředí, změňte adresáře nejprve do této složky prostředí.

Prostředí Pythonu můžete najít `pip` a `conda` v *bin* složky každého prostředí. Můžete také použít předdefinované aliasy prostředí:

```bash
# Anaconda 2 5.3.0/Python 2.7: python27
python27 -m pip install <package>

# Anaconda 3 4.2.0/Python 3.5: python35
python35 -m pip install <package>

# Anaconda 3 5.3.0/Python 3.6: python36
python36 -m pip install <package>
```

Změny provedené na server použít pouze s aktuální relací, s výjimkou souborů a složek, které vytvoříte v *projektu* samotné složce. Například úpravě souboru ve složce projektu je mezi relacemi trvalé, ale balíčků s `pip install` nejsou.

> [!Note]
> Pokud používáte `python` nebo `python3`, vyvoláte systému nainstalovaná verze jazyka Python, který se používá pro poznámkové bloky. Nemáte oprávnění. pro operace, jako jsou `pip install` buď, takže nezapomeňte použít konkrétní verzi aliasy.

## <a name="access-notebook-logs"></a>Přístup k protokolům poznámkového bloku

Pokud narazíte na problémy při spuštění poznámkového bloku, ve složce s názvem se ukládá výstup z Jupyter *. nb.log*. Mohou přistupovat k těmto protokolům prostřednictvím **terminálu** příkaz nebo řídicí panel Projekt.

Většinou když používáte Jupyter místně může po spuštění v okně terminálu. V okně terminálu zobrazí výstup, jako je stav jádra.

Pokud chcete zobrazit protokoly, použijte následující příkaz, v terminálu:

```bash
cat .nb.log
```

Můžete také použít příkaz z buňky kódu do poznámkového bloku Python:

```bash
!cat .nb.log
```

## <a name="next-steps"></a>Další postup

- [Postup: Práce s datovými soubory projektu](work-with-project-data-files.md)
- [Přístup k datům cloudu v poznámkovém bloku](access-data-resources-jupyter-notebooks.md)
