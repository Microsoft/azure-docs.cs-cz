---
title: Úkoly pro vedoucí týmu v týmu vědeckého zpracování týmových dat
description: Podrobný návod pro úlohy týmu vedoucí týmu vědeckého zpracování týmových dat
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: df7d2278487c1b098615a14562c498b9187c56eb
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000024"
---
# <a name="tasks-for-the-team-lead-on-a-team-data-science-process-team"></a>Úlohy týmu vedoucích k týmovému zpracování týmových dat

Tento článek popisuje úkoly, které *tým vedoucí* dokončí pro svůj tým pro datové vědy. Cílem zájemce týmu je vytvořit týmovou spolupráci, která se bude standardizovat na [vědecký proces týmových dat](overview.md) (TDSP). TDSP je navržený tak, aby pomohla zdokonalit spolupráci a týmovou výuku. 

TDSP je agilní a iterativní metodologie pro datové vědy, která umožňuje efektivně dodávat prediktivní Analytická řešení a inteligentní aplikace. Proces zpracovává i osvědčené postupy a struktury od společnosti Microsoft a odvětví.  Cílem je úspěšná implementace iniciativ v oblasti datových věd a plně se doplněním výhod svých analytických programů. Přehled rolí personálu a přidružených úloh pro tým pro datové vědy, který se na TDSPe standardizace, najdete v tématu [role procesů a úlohy vědeckého zpracování dat týmu](roles-tasks.md).

Vedoucí týmu spravuje tým, který se skládá z několika vědců v oblasti datových věd v podniku. V závislosti na velikosti a struktuře datové vědy a vedoucí [skupiny](group-manager-tasks.md) a vedoucího týmu může být stejná osoba nebo by mohla delegovat jejich úkoly na náhrady. Ale nemění se samotné úkoly. 

Následující diagram znázorňuje pracovní postup pro úkoly, které tým vedoucí dokončí k nastavení týmového prostředí:

![Pracovní postup úkolu zájemce týmu](./media/team-lead-tasks/team-leads-1-creating-teams.png)

1. Vytvořte **týmový projekt** v organizaci skupiny ve službě Azure DevOps. 
  
1. Přejmenujte výchozí týmové úložiště na **TeamUtilities**.
  
1. Vytvořte nové úložiště **TeamTemplate** v týmovém projektu. 
  
1. Importujte obsah úložišť **GroupUtilities** a **GroupProjectTemplate** skupiny do úložišť **TeamUtilities** a **TeamTemplate** . 
  
1. Nastavte **řízení zabezpečení** přidáním členů týmu a konfigurací jejich oprávnění.
  
1. V případě potřeby vytvořte zdroje dat a analýzy týmu:
   - Přidejte nástroje pro konkrétní tým do úložiště **TeamUtilities** . 
   - Vytvořte službu **Azure File Storage** pro ukládání datových assetů, které mohou být užitečné pro celý tým. 
   - Připojte službu Azure File Storage k vedoucímu týmu **Data Science Virtual Machine** (DSVM) a přidejte do ní datové assety.

Následující kurz vás provede podrobnými kroky.

> [!NOTE] 
> Tento článek používá Azure DevOps a DSVM k nastavení týmového prostředí TDSP, protože to je způsob implementace TDSP v Microsoftu. Pokud váš tým používá jiné hostování kódu nebo vývojové platformy, úkoly vedoucí týmu jsou stejné, ale způsob jejich dokončení může být jiný.

## <a name="prerequisites"></a>Požadavky

V tomto kurzu se předpokládá, že [správce skupiny](group-manager-tasks.md)nastavil následující prostředky a oprávnění:

- **Organizace** Azure DevOps pro vaši datovou jednotku
- Úložiště **GroupProjectTemplate** a **GroupUtilities** , která se naplní obsahem úložišť **PROJECTTEMPLATE** a **nástrojů** týmu Microsoftu TDSP
- Oprávnění pro váš účet organizace k vytváření projektů a úložišť pro váš tým

Abyste mohli klonovat úložiště a upravovat svůj obsah na vašem místním počítači nebo v DSVM nebo nastavit službu Azure File Storage a připojit ji k DSVM, budete potřebovat následující:

- Předplatné Azure.
- V počítači je nainstalovaný Git. Pokud používáte DSVM, Git se předem nainstaluje. V opačném případě si přečtěte článek věnované [platformám a nástrojům](platforms-and-tools.md#appendix).
- Pokud chcete použít DSVM, Windows nebo Linux DSVM vytvořeného a nakonfigurovaného v Azure. Další informace a pokyny najdete v dokumentaci k [Data Science Virtual Machine](../data-science-virtual-machine/index.yml).
- Pro Windows DSVM se na vašem počítači nainstaluje [Správce přihlašovacích údajů Git (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) . V souboru *Readme.MD* se posuňte dolů k části **Stažení a instalace** a vyberte **nejnovější instalační program**. Stáhněte instalační program *. exe* z instalační stránky a spusťte ho. 
- Pro Linux DSVM se v DSVM nastavil veřejný klíč SSH a přidal se do Azure DevOps. Další informace a pokyny najdete v části **vytvoření veřejného klíče SSH** v [příloze platformy a nástroje](platforms-and-tools.md#appendix). 

## <a name="create-a-team-project-and-repositories"></a>Vytvoření týmového projektu a úložišť

V této části vytvoříte v organizaci Azure DevOps ve vaší skupině následující zdroje:

- Projekt **myTeam** v Azure DevOps
- Úložiště **TeamTemplate**
- Úložiště **TeamUtilities**

Názvy zadané pro úložiště a adresáře v tomto kurzu předpokládají, že chcete vytvořit samostatný projekt pro vlastní tým v rámci větší organizace pro vědu pro datové vědy. Celá skupina ale může zvolit práci v rámci jednoho projektu vytvořeného správcem skupiny nebo správcem organizace. Všechny týmy pro datové vědy pak vytvoří úložiště v rámci tohoto jediného projektu. Tento scénář může být platný pro:
- Malá skupina pro datové vědy, která nemá více týmů pro datové vědy. 
- Širší skupina pro datové vědy s více týmy pro datové vědy, které si ale přeje optimalizovat spolupráci s aktivitami, jako je plánování sprintů na úrovni skupiny. 

Pokud se týmy rozhodnou, že mají svá vlastní úložiště v rámci jedné skupiny projektů, vedoucí týmu by měl vytvořit úložiště s názvy, jako je *\<TeamName> Šablona* a *\<TeamName> nástroje*. Např.: *TeamATemplate* a *TeamAUtilities*. 

V každém případě vedoucí týmu potřebuje nechat své členy týmu, aby věděli, které šablony a úložiště nástrojů se mají nastavit a klonovat. Vedoucí projektu by měli postupovat podle [úkolů vedoucího týmu pro datové vědy](project-lead-tasks.md) k vytváření úložišť projektů, ať už v samostatných projektech nebo v jednom projektu. 

### <a name="create-the-myteam-project"></a>Vytvoření projektu MyTeam

Vytvoření samostatného projektu pro váš tým:

1. Ve webovém prohlížeči přejdete na domovskou stránku organizace Azure DevOps, na adrese URL *https: \/ / \<server name> / \<organization name>* a vyberte **Nový projekt**. 
   
   ![Vybrat nový projekt](./media/team-lead-tasks/team-leads-2-create-new-team.png)
   
1. V dialogovém okně **vytvořit projekt** zadejte do pole **název projektu** název svého týmu, například *myTeam*, a pak vyberte **Upřesnit**. 
   
1. V části **Správa verzí** vyberte **Git** a v části **proces pracovní položky** vyberte **agilní**. Potom vyberte **Vytvořit**. 
   
   ![Vytvoření projektu](./media/team-lead-tasks/team-leads-3-create-new-team-2.png)
   
Otevře se stránka s **přehledem** týmového projektu s adresou URL stránky *https: \/ / \<server name> / \<organization name> / \<team name>*.

### <a name="rename-the-myteam-default-repository-to-teamutilities"></a>Přejmenujte výchozí úložiště MyTeam na TeamUtilities

1. Na stránce **Souhrn** projektu **myTeam** v části **jakou službu chcete začít?** vyberte možnost **úložišť**. 
   
   ![Výběr úložišť](./media/team-lead-tasks/team-leads-6-rename-team-project-repo.png)
   
1. Na stránce úložiště **myTeam** vyberte úložiště **myTeam** v horní části stránky a potom v rozevíracím seznamu vyberte **Spravovat** úložiště. 
   
   ![Výběr možnosti spravovat úložiště](./media/team-lead-tasks/team-leads-7-rename-team-project-repo-2.png)
1. Na stránce **nastavení projektu** vyberte **...** vedle úložiště **myTeam** a pak vyberte **Přejmenovat úložiště**. 
   
   ![Vybrat přejmenovat úložiště](./media/team-lead-tasks/team-leads-8-rename-team-project-repo-3.png)
   
1. V místní nabídce **přejmenování úložiště myTeam** zadejte *TeamUtilities* a pak vyberte **Přejmenovat**. 

### <a name="create-the-teamtemplate-repository"></a>Vytvoření úložiště TeamTemplate

1. Na stránce **nastavení projektu** vyberte **nové úložiště.** 
   
   ![Vybrat nové úložiště](./media/team-lead-tasks/team-leads-9-create-team-utilities.png)
   
   Nebo v levém navigačním panelu na stránce **Souhrn** projektu **myTeam** **Vyberte úložiště** , v horní části stránky vyberte úložiště a v rozevíracím seznamu vyberte **nové úložiště** .
   
1. V dialogovém okně **vytvořit nové úložiště** se ujistěte, že je v části **typ** zaškrtnuté políčko **Git** . Do **pole název úložiště** zadejte *TeamTemplate* a pak vyberte **vytvořit**.
   
   ![Vytvořit úložiště](./media/team-lead-tasks/team-leads-10-create-team-utilities-2.png)
   
1. Potvrďte, že na stránce nastavení projektu vidíte dvě úložiště **TeamUtilities** a **TeamTemplate** . 
   
   ![Dvě týmové úložiště](./media/team-lead-tasks/team-leads-11-two-repo-in-team.png)

### <a name="import-the-contents-of-the-group-common-repositories"></a>Import obsahu společných úložišť skupin

Pokud chcete naplnit svá úložiště v týmu obsahem společných úložišť skupiny, která nastavuje váš správce skupin:

1. Na domovské stránce projektu **myTeam** v levém navigačním **panelu vyberte úložiště** . Pokud se zobrazí zpráva, že se nenašla Šablona **myTeam** , vyberte odkaz v **opačném případě, přejděte k výchozímu úložišti TeamTemplate.** 
   
   Otevře se výchozí úložiště **TeamTemplate** . 
   
1. Na stránce **TeamTemplate je prázdná** vyberte **importovat**. 
   
   ![Vybrat Import](./media/team-lead-tasks/import-repo.png)
   
1. V dialogovém okně **importovat úložiště Git** jako **typ zdroje** vyberte **Git** a v části **Adresa URL klonu** zadejte adresu URL pro úložiště šablon společné šablony. Adresa URL je *https: \/ / \<server name> / \<organization name> /_git/ \<repository name>*. Příklad: *https: \/ /dev.Azure.com/DataScienceUnit/GroupCommon/_git/GroupProjectTemplate*. 
   
1. Vyberte **Importovat**. Obsah úložiště šablony skupiny se importuje do vašeho týmového úložiště šablony. 
   
   ![Importovat společné úložiště šablon skupin](./media/team-lead-tasks/import-repo-2.png)
   
1. V horní části stránky **úložišť** projektu rozbalte rozevírací seznam a vyberte úložiště **TeamUtilities** .
   
1. Opakujte proces importu pro import obsahu společného úložiště nástrojů pro vaše skupiny, například *GroupUtilities*, do úložiště **TeamUtilities** . 
   
Každé ze dvou úložišť týmu teď obsahuje soubory z příslušného společného úložiště skupiny. 

### <a name="customize-the-contents-of-the-team-repositories"></a>Přizpůsobení obsahu úložišť týmu

Pokud chcete přizpůsobit obsah úložišť týmu tak, aby splňovala konkrétní potřeby vašeho týmu, můžete to udělat hned teď. Můžete upravovat soubory, měnit adresářovou strukturu nebo přidávat soubory a složky.

Postup úpravy, nahrávání nebo vytváření souborů nebo složek přímo v Azure DevOps:

1. Na stránce **Souhrn** projektu **myTeam** **Vyberte úložiště**. 
   
1. V horní části stránky vyberte úložiště, které chcete upravit.

1. V adresářové struktuře úložiště přejděte do složky nebo souboru, který chcete změnit. 
   
   - Chcete-li vytvořit nové složky nebo soubory, vyberte šipku vedle tlačítka **Nový**. 
     
     ![Vytvořit nový soubor](./media/team-lead-tasks/new-file.png)
     
   - Chcete-li odeslat soubory, vyberte možnost **nahrát** soubory. 
     
     ![Nahrání souborů](./media/team-lead-tasks/upload-files.png)
     
   - Pokud chcete upravit existující soubory, přejděte k souboru a pak vyberte **Upravit**. 
     
     ![Upravit soubor](./media/team-lead-tasks/edit-file.png)
     
1. Po přidání nebo úpravě souborů vyberte **Potvrdit**.
   
   ![Potvrdit změny](./media/team-lead-tasks/commit.png)

Pokud chcete pracovat s úložišti na místním počítači nebo DSVM, nejdřív zkopírujte nebo *naklonujte* úložiště do místního počítače a pak proveďte potvrzení a vložení změn do sdílených úložišť týmu. 

Klonování úložišť:

1. Na stránce **Souhrn** projektu **myTeam** **Vyberte úložiště a v** horní části stránky vyberte úložiště, které chcete klonovat.
   
1. Na stránce úložiště vyberte **klonovat** v pravém horním rohu.
   
1. V dialogu **úložiště klonování** v části **příkazový řádek** vyberte **https** pro připojení HTTP nebo **SSH** pro připojení SSH a zkopírujte adresu URL klonování do schránky.
   
   ![Kopírovat adresu URL klonování](./media/team-lead-tasks/clone.png)
   
1. Na místním počítači vytvořte následující adresáře:
   
   - Pro Windows: **C:\GitRepos\MyTeam**
   - Pro Linux **$Home/gitrepos/myTeam** 
   
1. Přejděte do adresáře, který jste vytvořili.
   
1. V Gitu bash spusťte příkaz `git clone <clone URL>` , kde \<clone URL> je adresa URL, kterou jste zkopírovali z dialogového okna **klonování** .
   
   Například pomocí jednoho z následujících příkazů naklonujte úložiště **TeamUtilities** do adresáře *myTeam* na místním počítači. 
   
   **Připojení HTTPS:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **Připojení SSH:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```

Po provedení jakýchkoli změn v místním klonu úložiště proveďte potvrzení a vložení změn do sdílených úložišť týmu. 

Spusťte následující příkazy Git bash z místního adresáře **GitRepos\MyTeam\TeamTemplate** nebo **GitRepos\MyTeam\TeamUtilities** .

```bash
git add .
git commit -m "push from local"
git push
```

> [!NOTE]
> Pokud se jedná o první potvrzení do úložiště Git, možná budete muset nakonfigurovat globální parametry *User.Name* a *User. email* před spuštěním `git commit` příkazu. Spusťte následující dva příkazy:
> 
> `git config --global user.name <your name>`
> 
> `git config --global user.email <your email address>`
> 
> Pokud potvrdíte několik úložišť Git, použijte stejný název a e-mailovou adresu pro všechny z nich. Použití stejného jména a e-mailové adresy je vhodné při vytváření Power BI řídicích panelů ke sledování aktivit Git ve více úložištích.

## <a name="add-team-members-and-configure-permissions"></a>Přidat členy týmu a nakonfigurovat oprávnění

Chcete-li přidat členy do týmu:

1. V Azure DevOps na domovské stránce projektu **myTeam** v levém navigačním panelu vyberte **nastavení projektu** . 
   
1. V **nastavení projektu** vlevo navigace vyberte **týmy** a pak na stránce **týmy** vyberte **tým myTeam**. 
   
   ![Konfigurace týmů](./media/team-lead-tasks/teams.png)
   
1. Na stránce **týmový profil** vyberte **Přidat**.
   
   ![Přidat do MyTeam týmu](./media/team-lead-tasks/add-to-team.png)
   
1. V dialogovém okně **Přidat uživatele a skupiny** vyhledejte a vyberte členy, které chcete přidat do skupiny, a pak vyberte **Uložit změny**. 
   
   ![Přidat uživatele a skupiny](./media/team-lead-tasks/add-users.png)
   

Konfigurace oprávnění pro členy týmu:

1. V **nastavení projektu** vlevo navigace vyberte **oprávnění**. 
   
1. Na stránce **oprávnění** vyberte skupinu, do které chcete přidat členy. 
   
1. Na stránce pro tuto skupinu vyberte **členy** a pak vyberte **Přidat**. 
   
1. V místní nabídce **pozvat členy** vyhledejte a vyberte členy, které chcete přidat do skupiny, a pak vyberte **Uložit**. 
   
   ![Udělení oprávnění členům](./media/team-lead-tasks/grant-permissions.png)

## <a name="create-team-data-and-analytics-resources"></a>Vytvoření týmových dat a prostředků analýzy

Tento krok je nepovinný, ale v celém týmu sdílíme data a analytické prostředky s výhodami výkonu a nákladů. Členové týmu mohou spouštět své projekty na sdílených prostředcích, ukládat je do rozpočtů a efektivněji spolupracovat. Můžete vytvořit službu Azure File Storage a připojit ji k DSVM ke sdílení se členy týmu. 

Informace o sdílení dalších prostředků s týmem, například Azure HDInsight Spark clustery, najdete v tématu [platformy a nástroje](platforms-and-tools.md). Toto téma obsahuje pokyny z pohledu na data vědy o výběru prostředků, které jsou vhodné pro vaše potřeby, odkazů na stránky produktů a dalších relevantních a užitečných kurzů.

>[!NOTE]
> Aby nedošlo k přenosu dat mezi datovými centry, což může být pomalé a nákladné, ujistěte se, že je vaše skupina prostředků Azure, účet úložiště a DSVM všechna hostovaná ve stejné oblasti Azure. 

### <a name="create-azure-file-storage"></a>Vytvoření služby Azure File Storage

1. Spusťte následující skript, který vytvoří službu Azure File Storage pro datové prostředky, které jsou užitečné pro celý tým. Skript vás vyzve k zadání informací o předplatném Azure, takže musí začít. 

   - Na počítači s Windows spusťte skript z příkazového řádku PowerShellu:
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.ps1" -outfile "CreateFileShare.ps1"
     .\CreateFileShare.ps1
     ```
     
   - Na počítači se systémem Linux spusťte skript z prostředí Linux:
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.sh"
     bash CreateFileShare.sh
     ```
   
1. Po zobrazení výzvy se přihlaste ke svému účtu Microsoft Azure a vyberte předplatné, které chcete použít.
   
1. Vyberte účet úložiště, který se má použít, nebo vytvořte nové v rámci vybraného předplatného. Pro název služby Azure File Storage můžete použít malá písmena, číslice a spojovníky.
   
1. Pokud chcete usnadnit připojení a sdílení úložiště, stiskněte ENTER nebo ENTER *Y* a uložte informace o službě Azure File Storage do textového souboru v aktuálním adresáři. Můžete vrátit tento textový soubor do úložiště **TeamTemplate** , v ideálním případě v **Docs\DataDictionaries**, aby k němu měli přístup všechny projekty ve vašem týmu. K připojení úložiště Azure File Storage k Azure DSVM v další části budete potřebovat taky informace o souboru. 
   
### <a name="mount-azure-file-storage-on-your-local-machine-or-dsvm"></a>Připojení úložiště souborů Azure na místním počítači nebo DSVM

1. K připojení služby Azure File Storage k místnímu počítači nebo DSVM použijte následující skript.
   
   - Na počítači s Windows spusťte skript z příkazového řádku PowerShellu:
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.ps1" -outfile "AttachFileShare.ps1"
     .\AttachFileShare.ps1
     ```
     
   - Na počítači se systémem Linux spusťte skript z prostředí Linux:
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.sh"
     bash AttachFileShare.sh
     ```
   
1. Pokud chcete pokračovat, pokud jste v předchozím kroku uložili soubor s informacemi o službě Azure File Storage, stiskněte ENTER nebo ENTER *Y* . Zadejte úplnou cestu a název souboru, který jste vytvořili. 
   
   Pokud nemáte soubor s informacemi o službě Azure File Storage, zadejte *n* a podle pokynů zadejte své předplatné, účet Azure Storage a informace o službě Azure File Storage.
   
1. Zadejte název místního nebo TDSP jednotky pro připojení sdílené složky. Na obrazovce se zobrazí seznam existujících názvů jednotek. Zadejte název jednotky, který ještě neexistuje.
   
1. Zkontrolujte, jestli je nová jednotka a úložiště úspěšně připojené k vašemu počítači.

## <a name="next-steps"></a>Další kroky

Zde jsou odkazy na podrobné popisy dalších rolí a úloh, které jsou definovány v rámci vědeckého zpracování týmových dat:

- [Úlohy správce skupin pro tým pro datové vědy](group-manager-tasks.md)
- [Úkoly vedoucího projektu pro tým pro datové vědy](project-lead-tasks.md)
- [Jednotlivé úkoly přispěvatele projektu pro tým pro datové vědy](project-ic-tasks.md)