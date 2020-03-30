---
title: Úkoly pro vedoucího týmu v týmu team data science process team
description: Podrobný návod úkolů pro vedoucího týmu v týmu zpracování dat
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d099d7c233c3f4b5e65bfdb7d4b875a0e4098499
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864277"
---
# <a name="tasks-for-the-team-lead-on-a-team-data-science-process-team"></a>Úkoly pro vedoucího týmu v týmu team data science process

Tento článek popisuje úkoly, které *tým ový vedoucí* dokončí pro svůj tým datové vědy. Cílem vedoucího týmu je vytvořit týmové prostředí pro spolupráci, které standardizuje [proces vědecké vědy o týmových datech](overview.md) (TDSP). TDSP je navržen tak, aby pomohl zlepšit spolupráci a týmové učení. 

TDSP je agilní, iterativní metodika datové vědy, která efektivně poskytuje prediktivní analytická řešení a inteligentní aplikace. Proces destiluje osvědčené postupy a struktury od společnosti Microsoft a průmyslu.  Cílem je úspěšná implementace iniciativ v oblasti datových věd a plné využití výhod jejich analytických programů. Přehled rolí personálu a přidružených úkolů pro tým pro datové vědy standardizující na tdsp, najdete v [tématu Role a úkoly procesu týmových dat](roles-tasks.md).

Vedoucí týmu spravuje tým složený z několika datových vědců v jednotce datové vědy podniku. V závislosti na velikosti a struktuře jednotky datové vědy může být [vedoucí skupiny](group-manager-tasks.md) a vedoucí týmu stejná osoba nebo mohou delegovat své úkoly na náhradníky. Ale samotné úkoly se nemění. 

Následující diagram znázorňuje pracovní postup pro úkoly, které vedoucí týmu dokončí, aby nastavil týmové prostředí:

![Pracovní postup úkolu vedoucího týmu](./media/team-lead-tasks/team-leads-1-creating-teams.png)

1. Vytvořte **týmový projekt** v organizaci skupiny v Azure DevOps. 
  
1. Přejmenujte výchozí týmové úložiště na **TeamUtilities**.
  
1. Vytvořte nové úložiště **TeamTemplate** v týmovém projektu. 
  
1. Importujte obsah úložišť **GroupUtilities** a **GroupProjectTemplate** skupiny do úložišť **TeamUtilities** a **TeamTemplate.** 
  
1. Nastavte **ovládací prvek zabezpečení** přidáním členů týmu a konfigurací jejich oprávnění.
  
1. V případě potřeby vytvořte týmová data a analytické prostředky:
   - Přidejte nástroje specifické pro tým do úložiště **TeamUtilities.** 
   - Vytvořte **úložiště souborů Azure** pro ukládání datových prostředků, které mohou být užitečné pro celý tým. 
   - Připojte úložiště souborů Azure do **virtuálního počítače datové vědy** (DSVM) vedoucího týmu a přidejte do něj datové prostředky.

Následující kurz podrobně provede kroky.

> [!NOTE] 
> Tento článek používá Azure DevOps a DSVM k nastavení týmového prostředí TDSP, protože to je, jak implementovat TDSP u Microsoftu. Pokud váš tým používá jiné kód hosting nebo vývojové platformy, vedoucí tým úkoly jsou stejné, ale způsob jejich dokončení se může lišit.

## <a name="prerequisites"></a>Požadavky

Tento kurz předpokládá, že [správce skupiny](group-manager-tasks.md)nastavil následující prostředky a oprávnění :

- **Organizace** Azure DevOps pro vaši datovou jednotku
- Repozitáře **GroupProjectTemplate** a **GroupUtilities,** naplněná obsahem úložišť **ProjectTemplate** a **Utilities** týmu Microsoft TDSP
- Oprávnění k vytvoření projektů a úložišť pro váš tým pro váš účet organizace

Abyste mohli klonovat úložiště a upravovat jejich obsah v místním počítači nebo DSVM nebo nastavit úložiště souborů Azure a připojit ho k vašemu DSVM, potřebujete následující:

- Předplatné Azure.
- Git nainstalovaný na vašem počítači. Pokud používáte DSVM, Git je předinstalovaný. V opačném případě se podívejte do [dodatku k platformám a nástrojům](platforms-and-tools.md#appendix).
- Pokud chcete použít DSVM, Windows nebo Linux DSVM vytvořené a nakonfigurované v Azure. Další informace a pokyny naleznete v [dokumentaci k virtuálním strojům pro datové vědy](/azure/machine-learning/data-science-virtual-machine/).
- Pro systém Windows DSVM je v počítači nainstalován [Správce přihlašovacích údajů (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) gitu. V *souboru README.md* přejděte dolů do části **Stáhnout a nainstalovat** a vyberte nejnovější instalační **program**. Stáhněte instalační *program .exe* ze stránky instalačního programu a spusťte jej. 
- Pro Linux DSVM, SSH veřejný klíč nastavit na vašem DSVM a přidány do Azure DevOps. Další informace a pokyny naleznete v části **Vytvoření veřejného klíče SSH** v [dodatku platformy a nástroje](platforms-and-tools.md#appendix). 

## <a name="create-a-team-project-and-repositories"></a>Vytvoření týmového projektu a úložišť

V této části vytvoříte následující prostředky v organizaci Azure DevOps vaší skupiny:

- Projekt **MyTeam** v Azure DevOps
- Úložiště **TeamTemplate**
- Repozitář **TeamUtilities**

Názvy zadané pro úložiště a adresáře v tomto kurzu předpokládají, že chcete vytvořit samostatný projekt pro svůj vlastní tým v rámci větší organizace datové vědy. Celá skupina se však může rozhodnout pracovat v rámci jednoho projektu vytvořeného správcem skupiny nebo správcem organizace. Potom všechny týmy datové vědy vytvořit úložiště v rámci tohoto jediného projektu. Tento scénář může být platný pro:
- Malá skupina datové vědy, která nemá více týmů datové vědy. 
- Větší skupina datových věd s více týmy datových věd, které přesto chtějí optimalizovat spolupráci mezi týmy s aktivitami, jako je plánování sprintu na úrovni skupiny. 

Pokud se týmy rozhodnou mít úložiště specifická pro daný tým v rámci projektu jedné skupiny, vedoucí týmů by měli vytvořit úložiště s názvy jako * \<TeamName>Template* a * \<TeamName>Utilities*. Například: *TeamATemplate* a *TeamAUtilities*. 

V každém případě je třeba, aby vedoucí týmu dali členům týmu vědět, které šablony a úložiště nástrojů mají být nastaveny a klonovány. Vedoucí projektu by měli sledovat [úkoly vedoucího projektu pro tým pro datovou vědu](project-lead-tasks.md) a vytvářet úložiště projektů, ať už v rámci samostatných projektů nebo v jednom projektu. 

### <a name="create-the-myteam-project"></a>Vytvoření projektu MyTeam

Vytvoření samostatného projektu pro váš tým:

1. Ve webovém prohlížeči přejděte na domovskou stránku organizace Azure DevOps vaší skupiny na adrese URL *https:\//\<název serveru>/\<název organizace>* a vyberte Nový **projekt**. 
   
   ![Vybrat nový projekt](./media/team-lead-tasks/team-leads-2-create-new-team.png)
   
1. V dialogovém okně **Vytvořit projekt** zadejte název týmu, například *MyTeam*, v části **Název projektu**a pak vyberte **Upřesnit**. 
   
1. V části **Správa verzí**vyberte **Git**a v části Proces **pracovní položky**vyberte **Agile**. Pak vyberte **Vytvořit**. 
   
   ![Vytvoření projektu](./media/team-lead-tasks/team-leads-3-create-new-team-2.png)
   
Otevře se stránka **Souhrn týmového** projektu s url stránkou *\//\<https: název serveru\<>/ název organizace>/\<název týmu>*.

### <a name="rename-the-myteam-default-repository-to-teamutilities"></a>Přejmenování výchozího úložiště MyTeam na TeamUtilities

1. Na **Repos**stránce **Souhrn** projektu **MyTeam** v části **Jakou službu chcete začít?** 
   
   ![Vybrat repos](./media/team-lead-tasks/team-leads-6-rename-team-project-repo.png)
   
1. Na stránce úložiště **MyTeam** vyberte úložiště **MyTeam** v horní části stránky a pak v rozevíracím seznamu vyberte **Spravovat úložiště.** 
   
   ![Vybrat Spravovat repozitáře](./media/team-lead-tasks/team-leads-7-rename-team-project-repo-2.png)
1. Na stránce **Nastavení projektu** vyberte **...** vedle úložiště **MyTeam** a pak vyberte **Přejmenovat úložiště**. 
   
   ![Vybrat Přejmenovat úložiště](./media/team-lead-tasks/team-leads-8-rename-team-project-repo-3.png)
   
1. V **přejmenovat vyskakovací** okno úložiště MyTeam zadejte *TeamUtilities*a pak vyberte **Přejmenovat**. 

### <a name="create-the-teamtemplate-repository"></a>Vytvoření úložiště TeamTemplate

1. Na stránce **Nastavení projektu** vyberte **Nové úložiště.** 
   
   ![Vybrat nové úložiště](./media/team-lead-tasks/team-leads-9-create-team-utilities.png)
   
   Nebo vyberte **Úložiště** z levé navigace na stránce **Souhrn** projektu **MyTeam,** vyberte úložiště v horní části stránky a pak v rozevíracím seznamu vyberte **Nové úložiště.**
   
1. V **dialogovém** okně Vytvořit nové úložiště zkontrolujte, jestli je vybraná volba **Git** v části **Typ**. Pod názvem **úložiště**zadejte *TeamTemplate* a pak vyberte **Vytvořit**.
   
   ![Vytvořit úložiště](./media/team-lead-tasks/team-leads-10-create-team-utilities-2.png)
   
1. Potvrďte, že můžete vidět dvě úložiště **TeamUtilities** a **TeamTemplate** na stránce nastavení projektu. 
   
   ![Dvě týmová úložiště](./media/team-lead-tasks/team-leads-11-two-repo-in-team.png)

### <a name="import-the-contents-of-the-group-common-repositories"></a>Import obsahu společných úložišť skupiny

Chcete-li naplnit týmová úložiště obsahem společných úložišť skupiny nastavených správcem skupiny:

1. Na domovské stránce projektu **MyTeam** vyberte v levé navigaci **možnost Repos.** Pokud se zobrazí zpráva, že **šablona MyTeam** nebyla nalezena, vyberte odkaz v **části Jinak přejděte do výchozího úložiště TeamTemplate.** 
   
   Otevře se výchozí úložiště **TeamTemplate.** 
   
1. Na **stránce TeamTemplate je prázdná** stránka vyberte **Importovat**. 
   
   ![Vybrat import](./media/team-lead-tasks/import-repo.png)
   
1. V **dialogovém** okně Importovat úložiště Git vyberte **Git** jako **typ zdroje**a zadejte adresu URL pro společné úložiště šablon skupiny v části Adresa **URL klonování**. Adresa URL je *\//\<https:\<název serveru>/\<název organizace>/_git/název repozitáře>*. Například: *\/https: /dev.azure.com/DataScienceUnit/GroupCommon/_git/GroupProjectTemplate*. 
   
1. Vyberte **Importovat**. Obsah úložiště šablon skupiny se importuje do úložiště šablon týmu. 
   
   ![Importovat společné úložiště šablon skupiny](./media/team-lead-tasks/import-repo-2.png)
   
1. V horní části stránky **repos** projektu rozbalte a vyberte úložiště **TeamUtilities.**
   
1. Opakujte proces importu a importujte obsah úložiště běžných nástrojů skupiny, například *GroupUtilities*, do úložiště **TeamUtilities.** 
   
Každý z vašich dvou týmových úložišť nyní obsahuje soubory z odpovídajícího společného úložiště skupiny. 

### <a name="customize-the-contents-of-the-team-repositories"></a>Přizpůsobení obsahu týmových úložišť

Pokud chcete přizpůsobit obsah týmových úložišť tak, aby vyhovoval specifickým potřebám vašeho týmu, můžete to udělat nyní. Můžete upravovat soubory, měnit strukturu adresářů nebo přidávat soubory a složky.

Úprava, nahrávání nebo vytváření souborů nebo složek přímo v Azure DevOps:

1. Na stránce **Souhrn** projektu **MyTeam** vyberte **Možnost Repos**. 
   
1. V horní části stránky vyberte úložiště, které chcete přizpůsobit.

1. Ve struktuře adresářů úložiště přejděte do složky nebo souboru, který chcete změnit. 
   
   - Chcete-li vytvořit nové složky nebo soubory, vyberte šipku vedle položky **Nový**. 
     
     ![Vytvořit nový soubor](./media/team-lead-tasks/new-file.png)
     
   - Chcete-li nahrát soubory, vyberte **Nahrát soubory**. 
     
     ![Nahrání souborů](./media/team-lead-tasks/upload-files.png)
     
   - Chcete-li upravit existující soubory, přejděte do souboru a vyberte **upravit**. 
     
     ![Úprava souboru](./media/team-lead-tasks/edit-file.png)
     
1. Po přidání nebo úpravě souborů vyberte **Potvrdit**.
   
   ![Potvrzení změn](./media/team-lead-tasks/commit.png)

Chcete-li pracovat s úložišti v místním počítači nebo DSVM, nejprve zkopírujte nebo *naklonujte* úložiště do místního počítače a poté potvrdíte a posunete změny do sdílených týmových úložišť, 

Chcete-li klonovat úložiště:

1. Na stránce **Souhrn** projektu **MyTeam** vyberte **Úložiště**a v horní části stránky vyberte úložiště, které chcete klonovat.
   
1. Na stránce repo vyberte **Klonování** vpravo nahoře.
   
1. V dialogovém okně **Úložiště klonování** vyberte v části **Příkazový řádek** **příkazový** řádek pro připojení HTTP protokol HTTPS nebo **SSH** pro připojení SSH a zkopírujte adresu URL klonu do schránky.
   
   ![Kopírovat klonovat adresu URL](./media/team-lead-tasks/clone.png)
   
1. V místním počítači vytvořte následující adresáře:
   
   - Pro Windows: **C:\GitRepos\MyTeam**
   - Pro Linux, **$home/GitRepos/MyTeam** 
   
1. Změňte adresář, který jste vytvořili.
   
1. V Git Bash spusťte příkaz `git clone <clone URL>`, kde \<klonURL> je adresa URL zkopírovaná z dialogového okna **Klonování.**
   
   Pomocí jednoho z následujících příkazů můžete naklonovat úložiště **TeamUtilities** do adresáře *MyTeam* v místním počítači. 
   
   **Připojení HTTPS:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **Připojení SSH:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```

Po provedení jakékoli změny, které chcete v místním klonu úložiště, potvrdí a posune změny do sdílených týmových úložišť. 

Spusťte následující příkazy Git Bash z místního adresáře **GitRepos\MyTeam\TeamTemplate** nebo **GitRepos\MyTeam\TeamUtilities.**

```bash
git add .
git commit -m "push from local"
git push
```

> [!NOTE]
> Pokud je to poprvé, co se potvrdíte do úložiště Git, budete muset před `git commit` spuštěním příkazu nakonfigurovat globální parametry *user.name* a *user.email.* Spusťte následující dva příkazy:
> 
> `git config --global user.name <your name>`
> 
> `git config --global user.email <your email address>`
> 
> Pokud se zavazujete k několika úložištím Git, použijte pro všechny stejné jméno a e-mailovou adresu. Použití stejného jména a e-mailové adresy je vhodné při vytváření řídicích panelů Power BI ke sledování aktivit Gitu ve více repozitářích.

## <a name="add-team-members-and-configure-permissions"></a>Přidání členů týmu a konfigurace oprávnění

Přidání členů do týmu:

1. V Azure DevOps na domovské stránce projektu **MyTeam** vyberte **nastavení projektu** z levé navigace. 
   
1. V levém **navigačním panelu Nastavení projektu** vyberte **Týmy**a potom na stránce **Týmy** vyberte **tým MyTeam**. 
   
   ![Konfigurace týmů](./media/team-lead-tasks/teams.png)
   
1. Na stránce **Profil týmu** vyberte **Přidat**.
   
   ![Přidat do týmu MyTeam](./media/team-lead-tasks/add-to-team.png)
   
1. V dialogovém okně **Přidat uživatele a skupiny** vyhledejte a vyberte členy, které chcete přidat do skupiny, a pak vyberte **Uložit změny**. 
   
   ![Přidání uživatelů a skupin](./media/team-lead-tasks/add-users.png)
   

Konfigurace oprávnění pro členy týmu:

1. V levém navigačním **panelu Nastavení projektu** vyberte **oprávnění**. 
   
1. Na stránce **Oprávnění** vyberte skupinu, do které chcete členy přidat. 
   
1. Na stránce pro tuto skupinu vyberte **Členové**a pak vyberte **Přidat**. 
   
1. Ve vyskakovacím blokování **členů** vyhledání a výběru členů, které chcete přidat do skupiny, a pak vyberte **Uložit**. 
   
   ![Udělit oprávnění členům](./media/team-lead-tasks/grant-permissions.png)

## <a name="create-team-data-and-analytics-resources"></a>Vytváření týmových dat a analytických prostředků

Tento krok je volitelný, ale sdílení dat a analytických prostředků s celým týmem má výhody výkonu a nákladů. Členové týmu mohou provádět své projekty na sdílených zdrojích, ušetřit na rozpočtech a efektivněji spolupracovat. Můžete vytvořit úložiště souborů Azure a připojit jej na Vašem DSVM sdílet se členy týmu. 

Informace o sdílení dalších prostředků s týmem, jako jsou clustery Azure HDInsight Spark, najdete [v tématu Platformy a nástroje](platforms-and-tools.md). Toto téma poskytuje pokyny z hlediska datové vědy o výběru prostředků, které jsou vhodné pro vaše potřeby, a odkazy na stránky produktů a další relevantní a užitečné kurzy.

>[!NOTE]
> Pokud se vyhnete přenosu dat mezi datovými centry, což může být pomalé a nákladné, ujistěte se, že vaše skupina prostředků Azure, účet úložiště a DSVM jsou hostované ve stejné oblasti Azure. 

### <a name="create-azure-file-storage"></a>Vytvoření úložiště souborů Azure

1. Spusťte následující skript a vytvořte úložiště souborů Azure pro datové prostředky, které jsou užitečné pro celý tým. Skript vás vyzve k zadání informací o předplatném Azure, takže mějte je připravené k zadání. 

   - Na počítači se systémem Windows spusťte skript z příkazového řádku prostředí PowerShell:
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.ps1" -outfile "CreateFileShare.ps1"
     .\CreateFileShare.ps1
     ```
     
   - Na linuxovém počítači spusťte skript z linuxového prostředí:
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.sh"
     bash CreateFileShare.sh
     ```
   
1. Po zobrazení výzvy se přihlaste ke svému účtu Microsoft Azure a vyberte předplatné, které chcete použít.
   
1. Vyberte účet úložiště, který chcete použít, nebo si v rámci vybraného předplatného vytvořte nový. Pro název úložiště souborů Azure můžete použít malá písmena, čísla a pomlčky.
   
1. Chcete-li usnadnit připojení a sdílení úložiště, uložte stisknutím klávesy Enter nebo *Zadejte Y* informace o úložišti souborů Azure do textového souboru v aktuálním adresáři. Tento textový soubor můžete zkontrolovat do úložiště **TeamTemplate,** ideálně v části **Docs\DataDictionaries**, aby k němu měly přístup všechny projekty ve vašem týmu. V další části taky potřebujete informace o souborech k připojení úložiště souborů Azure do zařízení Azure DSVM. 
   
### <a name="mount-azure-file-storage-on-your-local-machine-or-dsvm"></a>Připojení úložiště souborů Azure na místním počítači nebo DSVM

1. Chcete-li připojit úložiště souborů Azure do místního počítače nebo DSVM, použijte následující skript.
   
   - Na počítači se systémem Windows spusťte skript z příkazového řádku prostředí PowerShell:
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.ps1" -outfile "AttachFileShare.ps1"
     .\AttachFileShare.ps1
     ```
     
   - Na linuxovém počítači spusťte skript z linuxového prostředí:
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.sh"
     bash AttachFileShare.sh
     ```
   
1. Pokud jste v předchozím kroku uložili informační soubor úložiště souborů Azure, pokračujte stisknutím klávesy Enter nebo *zadejte Y.* Zadejte úplnou cestu a název souboru, který jste vytvořili. 
   
   Pokud nemáte informační soubor úložiště souborů Azure, zadejte *n*a podle pokynů zadejte předplatné, účet úložiště Azure a informace o úložišti souborů Azure.
   
1. Zadejte název místní jednotky nebo jednotky TDSP, na kterou chcete sdílenou složku připojit. Na obrazovce se zobrazí seznam existujících názvů jednotek. Zadejte název jednotky, která ještě neexistuje.
   
1. Zkontrolujte, zda je nová jednotka a úložiště úspěšně připojeny k počítači.

## <a name="next-steps"></a>Další kroky

Zde jsou odkazy na podrobný popis dalších rolí a úkolů definovaných procesem vědecké vědy o týmových datech:

- [Úlohy správce skupiny pro tým datových věd](group-manager-tasks.md)
- [Úkoly vedoucího projektu pro tým datové vědy](project-lead-tasks.md)
- [Projekt ové úkoly jednotlivých přispěvatelů pro tým pro datové vědy](project-ic-tasks.md)
