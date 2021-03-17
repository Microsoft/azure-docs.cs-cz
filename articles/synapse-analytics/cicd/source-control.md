---
title: Správa zdrojového kódu v synapse studiu
description: Informace o tom, jak nakonfigurovat správu zdrojového kódu v Azure synapse studiu
services: synapse-analytics
author: liud
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: 3564609d869bef090f0a3db5e6040ba0f5ad80b0
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98796956"
---
# <a name="source-control-in-azure-synapse-studio"></a>Správa zdrojového kódu v Azure synapse studiu

Ve výchozím nastavení autoři Azure synapse Studio přímo na službu synapse. Pokud potřebujete spolupráci pomocí Gitu pro správu zdrojového kódu, synapse Studio vám umožní přidružit svůj pracovní prostor k úložišti Git, Azure DevOps nebo GitHubu. 

V tomto článku se dozvíte, jak nakonfigurovat a pracovat v pracovním prostoru synapse s povoleným úložištěm Git. Také zvýrazníme některé osvědčené postupy a Průvodce odstraňováním potíží.

> [!NOTE]
> Integrace Git Azure synapse Studio není dostupná v cloudu Azure Government.

## <a name="configure-git-repository-in-synapse-studio"></a>Konfigurace úložiště Git v synapse studiu 

Po spuštění synapse studia můžete v pracovním prostoru nakonfigurovat úložiště Git. Pracovní prostor synapse Studio může být současně přidružen pouze k jednomu úložišti Git. 

### <a name="configuration-method-1-global-bar"></a>Konfigurační Metoda 1: globální pruh

V globálním panelu synapse Studio vyberte rozevírací nabídku **synapse Live** a pak vyberte **nastavit úložiště kódu**.

![Konfigurace nastavení úložiště kódu z vytváření obsahu](media/configure-repo-1.png)

### <a name="configuration-method-2-manage-hub"></a>Konfigurační Metoda 2: Správa centra

Přejdete do centra pro správu synapse studia. V části **Správa zdrojového kódu** vyberte **Konfigurace Gitu** . Pokud nemáte připojené žádné úložiště, klikněte na **Konfigurovat**.

![Konfigurace nastavení úložiště kódu z centra pro správu](media/configure-repo-2.png)

> [!NOTE]
> Uživatelé, kteří mají oprávnění k pracovnímu prostoru přispěvatel, vlastník nebo vyšší úrovně, můžou konfigurovat, upravovat nastavení a odpojit úložiště Git v Azure synapse studiu. 

V pracovním prostoru můžete připojit buď úložiště Git Azure DevOps, nebo GitHub.

## <a name="connect-with-azure-devops-git"></a>Připojení ke službě Azure DevOps Git 

Pracovní prostor synapse můžete přidružit k úložišti Azure DevOps pro správu zdrojového kódu, spolupráci, správu verzí a tak dále. Pokud nemáte úložiště Azure DevOps, při vytváření prostředků úložiště postupujte podle [těchto pokynů](/azure/devops/organizations/accounts/create-organization-msa-or-work-student) .

### <a name="azure-devops-git-repository-settings"></a>Nastavení úložiště Git Azure DevOps

Při připojování k úložišti Git nejdřív vyberte typ úložiště jako Azure DevOps Git a pak v rozevíracím seznamu vyberte jeden tenant Azure AD a klikněte na **pokračovat**.

![Konfigurace nastavení úložiště kódu](media/connect-with-azuredevops-repo-selected.png)

V podokně Konfigurace se zobrazí následující nastavení Azure DevOps git:

| Nastavení | Popis | Hodnota |
|:--- |:--- |:--- |
| **Typ úložiště** | Typ úložiště kódu Azure Repos.<br/> | Azure DevOps Git nebo GitHub |
| **Azure Active Directory** | Název vašeho tenanta Azure AD. | `<your tenant name>` |
| **Účet Azure DevOps** | Název vaší Azure Repos organizace Název vaší Azure Repos organizace najdete na adrese `https://{organization name}.visualstudio.com` . K vašemu [Azure Repos organizaci](https://www.visualstudio.com/team-services/git/) se můžete přihlásit, abyste měli přístup k profilu sady Visual Studio a viděli vaše úložiště a projekty. | `<your organization name>` |
| **Názevprojektu** | Název projektu Azure Repos. Název projektu Azure Repos můžete najít na adrese `https://{organization name}.visualstudio.com/{project name}` . | `<your Azure Repos project name>` |
| **Úložiště** | Váš Azure Repos název úložiště kódu. Azure Repos projekty obsahují úložiště Git pro správu zdrojového kódu při zvětšování projektu. Můžete vytvořit nové úložiště nebo použít existující úložiště, které už je v projektu. | `<your Azure Repos code repository name>` |
| **Větev pro spolupráci** | Vaše větev Azure Repos spolupráci, která se používá k publikování. Ve výchozím nastavení je to `master` . Toto nastavení změňte pro případ, že chcete publikovat prostředky z jiné větve. Můžete vybrat existující větve nebo vytvořit nové. | `<your collaboration branch name>` |
| **Kořenová složka** | Kořenová složka ve větvi Azure Repos spolupráci. | `<your root folder name>` |
| **Import existujících prostředků do úložiště** | Určuje, jestli se mají importovat stávající prostředky z synapse studia do úložiště Git Azure Repos. Zaškrtněte políčko pro import prostředků pracovního prostoru (s výjimkou fondů) do přidruženého úložiště Git ve formátu JSON. Tato akce exportuje jednotlivé prostředky jednotlivě. Pokud toto políčko není zaškrtnuté, existující prostředky se neimportují. | Zaškrtnuto (výchozí) |
| **Importovat prostředek do této větve** | Vyberte, do které větve se importují prostředky (skript SQL, Poznámkový blok, definice úlohy Spark, datová sada, tok dat atd.). 

Pomocí odkazu úložiště můžete také rychle odkazovat na úložiště Git, se kterým se chcete připojit. 

### <a name="use-a-different-azure-active-directory-tenant"></a>Použít jiného tenanta Azure Active Directory

Azure Repos úložiště Git se může nacházet v jiném tenantovi Azure Active Directory. Pokud chcete zadat jiného tenanta Azure AD, musíte mít oprávnění správce pro předplatné Azure, které používáte. Další informace najdete v tématu [Změna správce předplatného](../../cost-management-billing/manage/add-change-subscription-administrator.md#assign-a-subscription-administrator) .

> [!IMPORTANT]
> Pokud se chcete připojit k jinému Azure Active Directory, přihlášený uživatel musí být součástí služby Active Directory. 

### <a name="use-your-personal-microsoft-account"></a>Použití osobních účet Microsoft

Pokud chcete použít osobní účet Microsoft pro integraci Gitu, můžete své osobní úložiště Azure propojit se službou Active Directory vaší organizace.

1. Přidejte osobní účet Microsoft do služby Active Directory vaší organizace jako hosta. Další informace najdete v tématu [přidání Azure Active Directory uživatelů spolupráce B2B v Azure Portal](../../active-directory/external-identities/add-users-administrator.md).

2. Přihlaste se k Azure Portal pomocí osobního účet Microsoft. Pak přejděte do služby Active Directory vaší organizace.

3. Přejděte do části Azure DevOps, kde se teď zobrazuje vaše osobní úložiště. Vyberte úložiště a připojte se ke službě Active Directory.

Po provedení těchto kroků konfigurace bude osobní úložiště k dispozici při nastavení integrace Gitu v nástroji synapse Studio.

Další informace o připojení Azure Repos ke službě Active Directory vaší organizace najdete v tématu věnovaném [připojení organizace k Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad).

## <a name="connect-with-github"></a>Připojení k GitHubu 

 Pracovní prostor můžete přidružit k úložišti GitHubu pro správu zdrojového kódu, spolupráci, správu verzí. Pokud nemáte účet GitHub nebo úložiště, vytvořte prostředky podle [těchto pokynů](https://github.com/join) .

Integrace GitHubu s synapse Studio podporuje veřejné GitHub (tj [https://github.com](https://github.com) .) i GitHub Enterprise. Můžete použít veřejné i soukromé úložiště GitHub, pokud máte oprávnění ke čtení a zápisu do úložiště v GitHubu.

### <a name="github-settings"></a>Nastavení GitHubu

Při připojování k úložišti Git nejdřív vyberte typ úložiště jako GitHub a pak zadejte účet GitHub nebo adresu URL serveru GitHub Enterprise, pokud používáte GitHub Enterprise Server, a klikněte na **pokračovat**.

![Nastavení úložiště GitHub](media/connect-with-github-repo-1.png)

V podokně Konfigurace se zobrazí následující nastavení úložiště GitHub:

| **Nastavení** | **Popis**  | **Hodnota**  |
|:--- |:--- |:--- |
| **Typ úložiště** | Typ úložiště kódu Azure Repos. | GitHub |
| **Použití GitHubu Enterprise** | Zaškrtávací políčko pro výběr GitHubu Enterprise | nevybráno (výchozí) |
| **Adresa URL GitHubu Enterprise** | Kořenová adresa URL pro GitHub Enterprise (musí být HTTPS pro místní server GitHub Enterprise). Příklad: `https://github.mydomain.com`. Požadováno jenom v případě, že je vybraná **možnost použít GitHub Enterprise** | `<your GitHub enterprise url>` |                                                           
| **Účet GitHub** | Název vašeho účtu GitHubu. Tento název najdete v názvu https: \/ /GitHub.com/{account}/{repository Name}. Když přejdete na tuto stránku, zobrazí se výzva k zadání přihlašovacích údajů GitHubu OAuth do svého účtu GitHubu. | `<your GitHub account name>` |
| **Název úložiště**  | Název vašeho úložiště kódu GitHubu Účty GitHubu obsahují úložiště Git pro správu zdrojového kódu. Můžete vytvořit nové úložiště nebo použít existující úložiště, které už máte ve svém účtu. | `<your repository name>` |
| **Větev pro spolupráci** | Vaše větev pro spolupráci GitHubu, která se používá k publikování. Ve výchozím nastavení se jedná o hlavní server. Toto nastavení změňte pro případ, že chcete publikovat prostředky z jiné větve. | `<your collaboration branch>` |
| **Kořenová složka** | Kořenová složka ve větvi spolupráce na GitHubu. |`<your root folder name>` |
| **Import existujících prostředků do úložiště** | Určuje, jestli se mají importovat stávající prostředky z synapse studia do úložiště Git. Zaškrtněte políčko pro import prostředků pracovního prostoru (s výjimkou fondů) do přidruženého úložiště Git ve formátu JSON. Tato akce exportuje jednotlivé prostředky jednotlivě. Pokud toto políčko není zaškrtnuté, existující prostředky se neimportují. | Vybráno (výchozí) |
| **Importovat prostředek do této větve** | Vyberte, kterou větev se importují prostředky (skript SQL, Poznámkový blok, definice úlohy Spark, datová sada, tok dat atd.). 

### <a name="github-organizations"></a>Organizace GitHubu

Připojení k organizaci GitHubu vyžaduje, aby organizace udělila oprávnění k synapse studiu. Uživatel s oprávněními správce v organizaci musí provádět níže uvedené kroky.

#### <a name="connecting-to-github-for-the-first-time"></a>Připojení k GitHubu poprvé

Pokud se připojujete k GitHubu z synapse studia poprvé, postupujte podle těchto kroků a připojte se k organizaci GitHubu.

1. V podokně Konfigurace Gitu zadejte název organizace do pole *účet GitHub* . Zobrazí se výzva k přihlášení do GitHubu. 

1. Přihlaste se pomocí přihlašovacích údajů uživatele.

1. Zobrazí se výzva k autorizaci synapse jako aplikace s názvem *Azure synapse*. Na této obrazovce se zobrazí možnost udělit oprávnění pro Synapse k přístupu k organizaci. Pokud nevidíte možnost udělit oprávnění, požádejte správce, aby oprávnění ručně udělil prostřednictvím GitHubu.

Po provedení těchto kroků se váš pracovní prostor bude moci připojit k veřejným i privátním úložištím v rámci vaší organizace. Pokud se nemůžete připojit, zkuste vyprázdnit mezipaměť prohlížeče a zkusit to znovu.

#### <a name="already-connected-to-github-using-a-personal-account"></a>Už jste připojení k GitHubu pomocí osobního účtu.

Pokud jste se už připojili k GitHubu a udělili jste mu oprávnění k přístupu k osobnímu účtu, pomocí následujících kroků udělíte oprávnění k organizaci.

1. Přejít na GitHub a otevřít **Nastavení**.

    ![Otevřít nastavení GitHubu](media/github-settings.png)

1. Vyberte **aplikace**. Na kartě **autorizované aplikace OAuth** byste měli vidět *Azure synapse*.

    ![Autorizovat aplikace OAuth](media/authorize-app.png)

1. Vyberte *Azure synapse* a udělte přístup k vaší organizaci.

    ![Udělit oprávnění organizace](media/grant-organization-permission.png)

Po dokončení těchto kroků se váš pracovní prostor bude moci připojit k veřejným i privátním úložištím v rámci vaší organizace.

## <a name="version-control"></a>Správa verzí

Systémy správy verzí (označované také jako _Správa zdrojového_ kódu) umožňují vývojářům spolupracovat na kódu a sledovat změny. Správa zdrojového kódu je důležitým nástrojem pro projekty s více vývojáři.

### <a name="creating-feature-branches"></a>Vytváření větví funkcí

Každé úložiště Git, které je přidruženo k nástroji synapse Studio, má větev pro spolupráci. ( `main` nebo `master` je výchozí branou pro spolupráci). Uživatelé můžou také vytvářet větve funkcí kliknutím na **+ Nová větev** v rozevíracím seznamu větev. Jakmile se zobrazí podokno nová větev, zadejte název vaší větve funkce.

![Vytvořit novou větev](media/create-new-branch.png)

Až budete připraveni sloučit změny z větve funkcí do vaší větve pro spolupráci, klikněte na rozevírací seznam větev a vyberte **vytvořit žádost o získání dat**. Tato akce převezme poskytovatele Git, kde můžete vyvolávat žádosti o přijetí změn, provádět revize kódu a sloučit změny ve vaší větvi pro spolupráci. Do služby synapse se povoluje jenom publikování z vaší větve pro spolupráci. 

![Vytvořit novou žádost o získání dat](media/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Konfigurovat nastavení publikování

Ve výchozím nastavení aplikace synapse Studio generuje šablony pracovního prostoru a ukládá je do větve s názvem `workspace_publish` . Chcete-li nakonfigurovat vlastní větev publikování, přidejte `publish_config.json` soubor do kořenové složky ve větvi pro spolupráci. Při publikování synapse Studio přečte tento soubor, vyhledá pole `publishBranch` a uloží soubory šablony pracovního prostoru do zadaného umístění. Pokud větev neexistuje, synapse Studio je automaticky vytvoří. A příklad toho, jak tento soubor vypadá:

```json
{
    "publishBranch": "workspace_publish"
}
```

Azure synapse Studio může mít najednou jenom jednu větev publikování. Když zadáte novou větev publikování, předchozí větev publikování by se neodstranila. Pokud chcete odebrat předchozí větev publikování, odstraňte ji ručně.


### <a name="publish-code-changes"></a>Publikování změn kódu

Po sloučení změn do větve pro spolupráci klikněte na **publikovat** a ručně publikujte změny kódu ve větvi spolupráce do služby synapse.

![Publikování změn](media/gitmode-publish.png)

Otevře se boční podokno, kde ověříte, že je větev publikování a nedokončené změny správná. Po ověření změn potvrďte publikování kliknutím na tlačítko **OK** .

![Potvrďte správnou větev publikování.](media/publish-change.png)

> [!IMPORTANT]
> Větev pro spolupráci není zástupcem toho, co je ve službě nasazené. Změny ve větvi spolupráce *musí* být publikovány ručně.

## <a name="switch-to-a-different-git-repository"></a>Přepnout na jiné úložiště Git

Pokud chcete přepnout na jiné úložiště Git, přejděte na stránku konfigurace Git v centru správy pod správou **zdrojových kódů**. Vyberte **Odpojit**. 

![Ikona Git](media/remove-repository.png)

Zadejte název pracovního prostoru a kliknutím na tlačítko **Odpojit** odeberte úložiště Git přidružené k vašemu pracovnímu prostoru.

Po odebrání přidružení s aktuálním úložištěm můžete nakonfigurovat nastavení Gitu tak, aby používalo jiné úložiště, a pak importovat stávající prostředky do nového úložiště.

> [!IMPORTANT]
> Odebráním konfigurace git z pracovního prostoru nedojde k odstranění všeho z úložiště. Pracovní prostor synapse bude obsahovat všechny publikované prostředky. Můžete pokračovat v úpravách pracovního prostoru přímo na službu.

## <a name="best-practices-for-git-integration"></a>Osvědčené postupy pro integraci Git

-   **Oprávnění**. Až budete mít úložiště Git připojené k vašemu pracovnímu prostoru, kdokoli, kdo má přístup k úložišti Git s libovolnou rolí ve vašem pracovním prostoru, bude moct aktualizovat artefakty, jako je skript SQL, Poznámkový blok, definice úlohy Spark, datová sada, tok dat a kanál v režimu Git. Obvykle nechcete, aby každý člen týmu měl oprávnění aktualizovat pracovní prostor. Udělte jenom oprávnění úložiště Git jenom pro autory artefaktů v pracovním prostoru synapse. 
-   **Spolupráci**. Doporučuje se nepovoluje přímá vrácení se změnami do větve pro spolupráci. Toto omezení může zabránit chybám, protože každé vrácení se změnami projde procesem revize žádosti o přijetí změn popsaným v [tématu vytváření větví funkcí](source-control.md#creating-feature-branches).
-   **Synapse živý režim**. Po publikování v režimu Git se všechny změny projeví v režimu synapse Live. V režimu synapse Live je publikování zakázané. A můžete zobrazit a spustit artefakty v živém režimu, pokud jste mu udělili správné oprávnění. 
-   **Upravit artefakty v studiu** Synapse Studio je jediné místo, kde můžete povolit správu zdrojového kódu pracovního prostoru a synchronizovat změny automaticky v Gitu. Jakékoli změny prostřednictvím sady SDK, PowerShell nebudou synchronizovány do Gitu. V případě, že je povolený Git, doporučujeme v studiu vždycky upravovat artefakt.

## <a name="troubleshooting-git-integration"></a>Řešení potíží s integrací Gitu

### <a name="access-to-git-mode"></a>Přístup k režimu Git 

Pokud vám bylo udělené oprávnění k úložišti Git GitHubu, které je propojené s vaším pracovním prostorem, ale nemůžete získat přístup k režimu git: 

1. Vymažte mezipaměť a aktualizujte stránku. 

1. Přihlaste se k účtu GitHub.

### <a name="stale-publish-branch"></a>Zastaralá větev publikování

Pokud větev publikování není synchronizovaná s branou pro spolupráci a obsahuje zastaralé prostředky navzdory nedávnému publikování, zkuste provést následující kroky:

1. Odebrat aktuální úložiště Git

1. Překonfigurujte Git se stejnými nastaveními, ale ujistěte se, že je zaškrtnuté políčko **importovat stávající prostředky do úložiště** a vybrat stejnou větev.  

1. Vytvořte žádost o přijetí změn, aby se změny sloučily do větve spolupráce. 

## <a name="unsupported-features"></a>Nepodporované funkce

- Synapse Studio nepovoluje vybírání určitých položek potvrzení nebo selektivního publikování prostředků. 
- Synapse Studio nepodporuje přizpůsobení zprávy potvrzení.
- V případě návrhu se akce Odstranit v studiu dokončí přímo do Gitu.

## <a name="next-steps"></a>Další kroky

* Chcete-li implementovat průběžnou integraci a nasazování, přečtěte si téma [průběžná integrace a doručování (CI/CD)](continuous-integration-deployment.md).