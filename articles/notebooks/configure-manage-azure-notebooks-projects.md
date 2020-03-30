---
title: Konfigurace a správa náhledu poznámkových bloků Azure
description: Zjistěte, jak spravovat metadata projektu, soubory projektu, prostředí projektu a kroky k nastavení prostřednictvím rozhraní Azure Notebooks a přímého přístupu k terminálu.
ms.topic: how-to
ms.date: 02/28/2020
ms.openlocfilehash: 1674effda2cb9bda45f49c91ca618225b0a75f0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280596"
---
# <a name="manage-and-configure-projects-in-azure-notebooks-preview"></a><a id="manage-and-configure-projects" />Správa a konfigurace projektů v náhledu poznámkových bloků Azure

Projekt v náhledu poznámkových bloků Azure je v podstatě konfigurace základního virtuálního počítače Linux, ve kterém běží poznámkové bloky Jupyter, spolu se složkou souborů a popisnými metadaty. 

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Řídicí panel projektu v poznámkových blocích Azure umožňuje spravovat soubory a jinak konfigurovat charakteristiky projektu:

- Výpočetní úroveň, na které projekt běží, což může být úroveň free nebo virtuální počítač Azure.
- Metadata projektu, která obsahuje název, popis, identifikátor, který se používá při sdílení projektu a zda je projekt veřejný nebo soukromý.
- Poznámkový blok projektu, data a další soubory, které spravujete jako jakýkoli jiný systém souborů.
- Prostředí projektu, které spravujete buď prostřednictvím spouštěcích skriptů, nebo přímo prostřednictvím terminálu.
- Protokoly, ke kterým přistupujete přes terminál.

> [!Note]
> Zde popsané funkce správy a konfigurace jsou k dispozici pouze vlastníkovi projektu, který projekt původně vytvořil. Projekt však můžete naklonovat na svůj vlastní účet, v takovém případě se stanete vlastníkem a můžete projekt nakonfigurovat podle potřeby.

Poznámkové bloky Azure spustí základní virtuální počítač při každém spuštění poznámkového bloku nebo jiného souboru. Server automaticky ukládá soubory a vypne se po 60 minutách nečinnosti. Server můžete také kdykoli zastavit pomocí příkazu **Vypnout** (klávesová zkratka: h).

## <a name="compute-tier"></a>Výpočetní úroveň

Ve výchozím nastavení se projekty spouštějí na úrovni **Free Compute,** která je omezena na 4 GB paměti a 1 GB dat, aby se zabránilo zneužití. Tato omezení můžete obejít a zvýšit výpočetní výkon pomocí jiného virtuálního počítače, který jste zřídit v předplatném Azure. Další informace naleznete v tématu [Jak používat virtuální počítače pro datové vědy](use-data-science-virtual-machine.md).

## <a name="edit-project-metadata"></a>Úprava metadat projektu

Na řídicím panelu projektu vyberte **Nastavení projektu**a vyberte kartu **Informace,** která obsahuje metadata projektu, jak je popsáno v následující tabulce. Metadata projektu můžete kdykoli změnit.

| Nastavení | Popis |
| --- | --- |
| Název projektu | Popisný název pro váš projekt, který poznámkové bloky Azure používá pro účely zobrazení. Například "Hello World in Python". |
| ID projektu | Vlastní identifikátor, který se stane součástí adresy URL, kterou používáte ke sdílení projektu. Toto ID může používat pouze písmena, čísla a pomlčky, je omezeno na 30 znaků a nemůže být [vyhrazeným ID projektu](create-clone-jupyter-notebooks.md#reserved-project-ids). Pokud si nejste jisti, co použít, je běžnou konvencí použití malé verze názvu projektu, kde jsou mezery převedeny na pomlčky, například "my-notebook-project" (zkráceno v případě potřeby tak, aby odpovídalo limitu délky). |
| Veřejný projekt | Pokud je nastavena, umožňuje komukoli s odkazem přístup k projektu. Při vytváření soukromého projektu zrušte zaškrtnutí této možnosti. |
| Skrýt klony | Pokud je nastavena, ostatní uživatelé nemohou zobrazit seznam klonů, které byly provedeny pro tento projekt. Skrytí klonů je užitečné pro projekty, které jsou sdíleny s mnoha lidmi, kteří nejsou součástí stejné organizace, například při použití poznámkového bloku pro výuku kurzu. |

> [!Important]
>
> Změna ID projektu zruší platnost všech odkazů na projekt, který jste mohli sdílet dříve.

## <a name="manage-project-files"></a>Správa souborů projektu

Řídicí panel projektu zobrazuje obsah systému složek projektu. Ke správě těchto souborů můžete použít různé příkazy.

### <a name="create-new-files-and-folders"></a>Vytváření nových souborů a složek

Příkaz **+ Nový** (klávesová zkratka: n) vytváří nové soubory nebo složky. Při použití příkazu nejprve vyberte typ položky, kterou chcete vytvořit:

| Typ položky | Popis | Chování příkazu |
| --- | --- | --- |
| **Poznámkový blok** | Jupyter notebook | Zobrazí místní okno, ve kterém zadáte název a jazyk poznámkového bloku. |
| **Složka** | Podsložka | Vytvoří pole pro úpravy v seznamu souborů projektu, do kterého zadáte název složky. |
| **Prázdný soubor** | Soubor, do kterého můžete uložit jakýkoli obsah, jako je text, data atd. | Vytvoří pole pro úpravy v seznamu souborů projektu, do kterého zadáte název souboru. |
| **Markdown** | Soubor Markdown. | Vytvoří pole pro úpravy v seznamu souborů projektu, do kterého zadáte název souboru. |

### <a name="upload-files"></a>Nahrání souborů

Příkaz **Nahrát** nabízí dvě možnosti importu dat z jiných umístění: **Z adresy URL** a Z **počítače**. Další informace najdete [v tématu Práce s datovými soubory v projektech Poznámkového bloku Azure](work-with-project-data-files.md).

### <a name="select-file-specific-commands"></a>Výběr příkazů specifických pro daný soubor

Každá položka v seznamu souborů projektu poskytuje příkazy prostřednictvím kontextové nabídky po kliknutí pravým tlačítkem myši:

![Příkazy v kontextové nabídce souboru](media/project-file-commands.png)

| Příkaz | Klávesová zkratka | Akce |
| --- | --- | --- |
| Spusťte | r (nebo klikněte) | Spustí soubor poznámkového bloku. Pro zobrazení jsou otevřeny další typy souborů.  |
| Kopírovat odkaz | y | Zkopíruje odkaz na soubor do schránky. |
| Běh v Laboratoři Jupyter | J | Běží notebook v JupyterLab, což je více developer-orientované rozhraní, než Jupyter obvykle poskytuje. |
| Preview | p | Otevře náhled souboru ve formátu HTML; u poznámkových bloků je náhled vykreslení poznámkového bloku jen pro čtení. Další informace naleznete v části [Náhled.](#preview) |
| Upravit soubor | Mohu | Otevře soubor pro úpravy. |
| Stáhnout | d | Stáhne soubor ZIP, který obsahuje soubor nebo obsah složky. |
| Přejmenovat | a | Zobrazí výzvu k zadání nového názvu souboru nebo složky. |
| Odstranění | x | Zobrazí výzvu k potvrzení a poté soubor trvale odebere z projektu. Odstranění nelze vrátit zpět. |
| Přesunout | m | Přesune soubor do jiné složky ve stejném projektu. |

#### <a name="preview"></a>Preview

Náhled souboru nebo poznámkového bloku je zobrazení obsahu jen pro čtení. spuštění buněk poznámkového bloku je zakázáno. Náhled se zobrazí každému, kdo má odkaz na soubor nebo poznámkový blok, ale není přihlášen do poznámkových bloků Azure. Po přihlášení může uživatel poznámkový blok naklonovat na svůj vlastní účet nebo si poznámkový blok stáhnout do místního počítače.

Stránka náhledu podporuje několik příkazů panelu nástrojů s klávesovými zkratkami:

| Příkaz | Klávesová zkratka | Akce |
| --- | --- | --- |
| Sdílet | s | Zobrazí vyskakovací okno sdílení, ze kterého můžete získat odkaz, sdílet se sociálními médii, získat HTML pro vkládání a odeslat e-mail. |
| Klonování | c  | Naklonujte zápisník na svůj účet. |
| Spusťte | r | Pokud to máte povoleno, spustí poznámkový blok. |
| Stáhnout | d | Stáhne kopii poznámkového bloku. |

## <a name="configure-the-project-environment"></a>Konfigurace prostředí projektu

Prostředí základního virtuálního počítače, ve kterém jsou vaše poznámkové bloky spuštěny, lze nakonfigurovat třemi způsoby:

- Zahrnutí jednorázového inicializačního skriptu
- Použití nastavení prostředí projektu k určení kroků nastavení
- Přístup k virtuálnímu počítači prostřednictvím terminálu.

Všechny formy konfigurace projektu se použijí při každém spuštění virtuálního počítače a tím ovlivní všechny poznámkové bloky v rámci projektu.

### <a name="one-time-initialization-script"></a>Jednorázový inicializační skript

První poznámkové bloky Azure vytvoří server pro projekt, hledá soubor v projektu s názvem *aznbsetup.sh*. Pokud je tento soubor k dispozici, poznámkové bloky Azure ho spustí. Výstup skriptu je uložen ve složce projektu jako *.aznbsetup.log*.

### <a name="environment-setup-steps"></a>Kroky nastavení prostředí

Nastavení prostředí projektu můžete použít k vytvoření jednotlivých kroků, které konfigurují prostředí.

Na řídicím panelu projektu vyberte **Nastavení projektu**a pak vyberte kartu **Prostředí,** do které přidáte, odeberete a upravíte kroky nastavení projektu:

![Místní okno Nastavení projektu s vybranou kartou Prostředí](media/project-settings-environment-steps.png)

Chcete-li přidat krok, nejprve vyberte **+ Přidat**, pak vyberte typ kroku v rozevíracím seznamu **Operace:**

![Volič operace pro nový krok nastavení prostředí](media/project-settings-environment-details.png)

Informace, které pak promítnete, závisí na typu operace, kterou jste zvolili:

- **Requirements.txt**: V druhém rozevíracím seznamu vyberte soubor *requirements.txt,* který je již v projektu. Pak vyberte verzi Pythonu ze třetího rozevíracího seznamu, který se zobrazí. Pomocí souboru *requirements.txt* se poznámkové bloky Azure spouštějí `pip install -r` se souborem *requirements.txt* při spuštění notebooku. Není nutné explicitně instalovat balíčky z v rámci samotného poznámkového bloku.

- **Skript prostředí**: V druhém rozevíracím seznamu vyberte v projektu skript bash shellu (obvykle soubor s příponou *.sh),* který obsahuje všechny příkazy, které chcete spustit pro inicializaci prostředí.

- **Environment.yml**: V druhém rozevíracím seznamu vyberte soubor *environments.yml* pro projekty Pythonu pomocí prostředí conda.

   > [!WARNING]
   > Vzhledem k tomu, že se jedná o službu `Environment.yml` preview ve vývoji, je aktuálně známý problém, kde se nastavení nepoužije na váš projekt podle očekávání. Projekt a poznámkové bloky Jupyter v rámci nenačítají zadaný soubor prostředí v současné době.

Po přidání kroků vyberte **Uložit**.

### <a name="use-the-terminal"></a>Použití terminálu

Na řídicím panelu projektu příkaz **Terminál** otevře terminál Linux, který vám umožní přímý přístup k serveru. V rámci terminálu můžete stahovat data, upravovat nebo spravovat soubory, kontrolovat procesy a dokonce používat nástroje jako vi a nano.

> [!Note]
> Pokud máte spouštěcí skripty v prostředí projektu, otevření terminálu může zobrazit zprávu oznamující, že instalace stále probíhá.

V terminálu můžete vydávat libovolné standardní příkazy Linuxu. Můžete také `ls` použít v domovské složce zobrazit různá prostředí, které existují ve virtuálním počítači, jako je například *anaconda2_501*, *anaconda3_420*, *anaconda3_501*, *IfSharp*a *R*, spolu s složku *projektu,* který obsahuje projekt:

![Projektový terminál v poznámkových blocích Azure](media/project-terminal.png)

Chcete-li ovlivnit určité prostředí, nejprve změňte adresáře do této složky prostředí.

Pro prostředí Pythonu můžete `pip` najít `conda` a v *bin* složce každého prostředí. Můžete také použít vestavěné aliasy pro prostředí:

```bash
# Anaconda 2 5.3.0/Python 2.7: python27
python27 -m pip install <package>

# Anaconda 3 4.2.0/Python 3.5: python35
python35 -m pip install <package>

# Anaconda 3 5.3.0/Python 3.6: python36
python36 -m pip install <package>
```

Změny provedené na serveru platí pouze pro aktuální relaci, s výjimkou souborů a složek, které vytvoříte v samotné složce *projektu.* Například úpravy souboru ve složce projektu je trvalé `pip install` mezi relacemi, ale balíčky s nejsou.

> [!Note]
> Pokud `python` používáte `python3`nebo , vyvoláte systémem nainstalované verze Pythonu, které se nepoužívají pro poznámkové bloky. Nemáte oprávnění pro operace, `pip install` jako je buď, takže nezapomeňte použít aliasy specifické pro verzi.

## <a name="access-notebook-logs"></a>Přístup k protokolům poznámkových bloků

Pokud narazíte na problémy při spuštění poznámkového bloku, výstup z Jupyter je uložen ve složce s názvem *.nb.log*. K těmto protokolům můžete přistupovat pomocí příkazu **Terminál** nebo řídicího panelu projektu.

Obvykle, když používáte Jupyter místně, můžete jej spustit z okna terminálu. Okno terminálu zobrazuje výstup, například stav jádra.

Chcete-li zobrazit protokoly, použijte v terminálu následující příkaz:

```bash
cat .nb.log
```

Příkaz můžete použít také z buňky kódu v poznámkovém bloku Pythonu:

```bash
!cat .nb.log
```

## <a name="next-steps"></a>Další kroky

- [Postup: Práce s datovými soubory projektu](work-with-project-data-files.md)
- [Přístup ke cloudovým datům v poznámkovém bloku](access-data-resources-jupyter-notebooks.md)
