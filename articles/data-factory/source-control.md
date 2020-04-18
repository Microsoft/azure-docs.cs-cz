---
title: Správa zdrojového kódu
description: Přečtěte si, jak nakonfigurovat správou zdrojového kódu v Azure Data Factory
services: data-factory
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.reviewer: ''
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/09/2019
ms.openlocfilehash: 3007865c15ceb03b104282c29179ec59a8196b38
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604589"
---
# <a name="source-control-in-azure-data-factory"></a>Správa zdrojového kódu ve Službě Azure Data Factory
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Uživatelské rozhraní Azure Data Factory (UX) má dvě možnosti, které jsou k dispozici pro vizuální vytváření:

- Autor přímo se službou Data Factory
- Vytváření s integrací Azure Repos Git nebo GitHub

> [!NOTE]
> Ve službě Azure Government Cloud se podporuje jenom vytváření přímo se službou Data Factory.

## <a name="author-directly-with-the-data-factory-service"></a>Autor přímo se službou Data Factory

Při vytváření přímo se službou Data Factory je jediným způsobem, jak uložit změny, tlačítko **Publikovat vše.** Po kliknutí se všechny provedené změny publikují přímo do služby Data Factory. 

![Režim publikování](media/author-visually/data-factory-publish.png)

Vytváření přímo se službou Data Factory má následující omezení:

- Služba Data Factory neobsahuje úložiště pro ukládání entit JSON pro vaše změny.
- Služba Data Factory není optimalizovaná pro spolupráci nebo správu verzí.

> [!NOTE]
> Při vytváření přímo se službou Data Factory je v uživatelském prostředí Azure Data Factory zakázáno, když je nakonfigurované úložiště Git. Změny lze provést přímo do služby prostřednictvím prostředí PowerShell nebo SDK.

## <a name="author-with-azure-repos-git-integration"></a>Vytváření s využitím integrace Gitu s Azure Repos

Vizuální vytváření s azure repos git integrace podporuje slučování zdrojového kódu a spolupráci pro práci na kanálech datové továrny. Toto datové úložiště můžete přidružit k úložišti organizace Azure Repos Git pro správu zdrojového kódu, spolupráci, správu verzí a tak dále. Jedna organizace Azure Repos Git může mít více úložišť, ale úložiště Azure Repos Git lze přidružit jenom k jedné datové továrně. Pokud nemáte organizaci nebo úložiště Azure Repos, vytvořte prostředky podle [těchto pokynů.](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)

> [!NOTE]
> Skripty a datové soubory můžete ukládat do úložiště Azure Repos Git. Je však nutné nahrát soubory ručně do služby Azure Storage. Kanál datové továrny automaticky nenahrává skript nebo datové soubory uložené v úložišti Azure Repos Git do úložiště Azure.

### <a name="configure-an-azure-repos-git-repository-with-azure-data-factory"></a>Konfigurace úložiště Azure Repos Git pomocí Azure Data Factory

Úložiště Azure Repos Git můžete nakonfigurovat pomocí datové továrny pomocí dvou metod.

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>Metoda konfigurace 1: Domovská stránka Azure Data Factory

Na domovské stránce Azure Data Factory vyberte **Nastavit úložiště kódu**.

![Konfigurace úložiště kódu Azure Repos](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Metoda konfigurace 2: Uživatelské virtuální plátno
Na vývojovém plátně Azure Data Factory UX vyberte rozevírací nabídku **Data Factory** a pak vyberte Nastavit **úložiště kódu**.

![Konfigurace nastavení úložiště kódu pro vytváření uživatelského prostředí](media/author-visually/configure-repo-2.png)

Obě metody otevřou konfigurační podokno nastavení úložiště.

![Konfigurace nastavení úložiště kódu](media/author-visually/repo-settings.png)

V konfiguračním podokně se zobrazí následující nastavení úložiště kódů Azure:

| Nastavení | Popis | Hodnota |
|:--- |:--- |:--- |
| **Typ úložiště** | Typ úložiště kódu Azure Repos.<br/> | Azure DevOps Git nebo GitHub |
| **Azure Active Directory** | Název klienta Azure AD. | `<your tenant name>` |
| **Organizace azure repos** | Název organizace Azure Repos. Název organizace Azure Repos můžete `https://{organization name}.visualstudio.com`najít na adrese . Můžete [se přihlásit do organizace Azure Repos,](https://www.visualstudio.com/team-services/git/) abyste měli přístup k profilu visual studia a viděli vaše úložiště a projekty. | `<your organization name>` |
| **Projectname** | Název projektu Azure Repos. Název projektu Azure Repos můžete `https://{organization name}.visualstudio.com/{project name}`najít na adrese . | `<your Azure Repos project name>` |
| **Název úložiště** | Název úložiště kódu Azure Repos. Projekty Azure Repos obsahují úložiště Git pro správu zdrojového kódu podle růstu projektu. Můžete vytvořit nové úložiště nebo použít existující úložiště, které je již ve vašem projektu. | `<your Azure Repos code repository name>` |
| **Větev spolupráce** | Vaše azure repos spolupráce větev, která se používá pro publikování. Ve výchozím nastavení `master`je to . Změňte toto nastavení v případě, že chcete publikovat prostředky z jiné větve. | `<your collaboration branch name>` |
| **Kořenová složka** | Kořenová složka ve větvi spolupráce Azure Repos. | `<your root folder name>` |
| **Import existujících prostředků data factory do úložiště** | Určuje, zda se mají importovat existující prostředky datové továrny z **plátna pro vytváření uživatelského** rozhraní do úložiště Azure Repos Git. Vyberte pole pro import prostředků datové továrny do přidruženého úložiště Git ve formátu JSON. Tato akce exportuje každý prostředek jednotlivě (to znamená, že propojené služby a datové sady jsou exportovány do samostatných jsoňů). Pokud toto pole není zaškrtnuto, existující prostředky se neimportují. | Vybráno (výchozí) |
| **Větev pro import prostředku do** | Určuje, do které větve se importují prostředky datové továrny (kanály, datové sady, propojené služby atd.). Prostředky můžete importovat do jedné z následujících větví: a. Spolupráce b. Vytvořit nové c. Použít existující |  |

> [!NOTE]
> Pokud používáte Microsoft Edge a nevidíte žádné hodnoty v rozevíracím seznamu účtu Azure DevOps, přidejte https://*.visualstudio.com do seznamu důvěryhodných serverů.

### <a name="use-a-different-azure-active-directory-tenant"></a>Použití jiného klienta Služby Azure Active Directory

Úložiště Git v Azure Repos můžete vytvořit v jiném tenantovi Azure Active Directory. Pokud chcete zadat jiného tenanta Azure AD, musíte mít oprávnění správce pro předplatné Azure, které používáte.

### <a name="use-your-personal-microsoft-account"></a>Používání osobního účtu Microsoft

Pokud chcete pro integraci Gitu použít osobní účet Microsoft, můžete propojit svůj osobní Azure Repo se službou Active Directory vaší organizace.

1. Přidejte svůj osobní účet Microsoft do služby Active Directory vaší organizace jako hosta. Další informace najdete v tématu [Přidání uživatelů spolupráce Služby Azure Active Directory B2B na webu Azure Portal](../active-directory/b2b/add-users-administrator.md).

2. Přihlaste se na portál Azure pomocí svého osobního účtu Microsoft. Poté přepněte do služby Active Directory vaší organizace.

3. Přejděte do části Azure DevOps, kde teď vidíte své osobní repo. Vyberte úložiště a spojte se se službou Active Directory.

Po těchto konfiguračních krocích je vaše osobní úložiště k dispozici, když nastavíte integraci Gitu v uzdu Factory.

Další informace o připojení azure repos do služby Active Directory vaší organizace najdete v [tématu Připojení organizace Azure DevOps k Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad).

## <a name="author-with-github-integration"></a>Vytváření s využitím integrace GitHubu

Vizuální vytváření s integrací GitHub podporuje slučování zdrojů a spolupráci pro práci na kanálech datové továrny. Toto datové úložiště můžete přidružit k úložišti účtů GitHub pro správu zdrojového kódu, spolupráci, správu verzí. Jeden účet GitHub může mít více úložišť, ale úložiště GitHub u nás může být přidruženo jenom k jedné datové továrně. Pokud nemáte účet GitHub nebo úložiště, vytvořte si prostředky podle [těchto pokynů.](https://github.com/join) 

Integrace GitHubu s Data Factory podporuje jak [https://github.com](https://github.com)veřejný GitHub (to znamená, ) tak GitHub Enterprise. S Tourezy Dat můžete používat veřejná i soukromá úložiště GitHubu, pokud máte oprávnění k čtení a zápisu do úložiště v GitHubu.

Chcete-li nakonfigurovat úložiště GitHub, musíte mít oprávnění správce pro předplatné Azure, které používáte.

Pro devítiminutový úvod a ukázku této funkce se podívejte na následující video:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Azure-Data-Factory-visual-tools-now-integrated-with-GitHub/player]

### <a name="configure-a-github-repository-with-azure-data-factory"></a>Konfigurace úložiště GitHub u Azure Data Factory

Úložiště GitHub můžete nakonfigurovat pomocí datové továrny pomocí dvou metod.

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>Metoda konfigurace 1: Domovská stránka Azure Data Factory

Na domovské stránce Azure Data Factory vyberte **Nastavit úložiště kódu**.

![Konfigurace úložiště kódu Azure Repos](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Metoda konfigurace 2: Uživatelské virtuální plátno

Na vývojovém plátně Azure Data Factory UX vyberte rozevírací nabídku **Data Factory** a pak vyberte Nastavit **úložiště kódu**.

![Konfigurace nastavení úložiště kódu pro vytváření uživatelského prostředí](media/author-visually/configure-repo-2.png)

Obě metody otevřou konfigurační podokno nastavení úložiště.

![Nastavení úložiště GitHub](media/author-visually/github-integration-image2.png)

V konfiguračním podokně se zobrazí následující nastavení úložiště GitHub:

| **Nastavení** | **Popis**  | **Hodnota**  |
|:--- |:--- |:--- |
| **Typ úložiště** | Typ úložiště kódu Azure Repos. | GitHubu |
| **Použití GitHub u Enterprise** | Zaškrtávací políčko pro výběr GitHub Enterprise | nevybrané (výchozí) |
| **Adresa URL githubu enterprise** | Kořenová adresa URL GitHub Enterprise (musí být HTTPS pro místní server GitHub Enterprise). Například: https://github.mydomain.com. Povinné pouze v případě, že je vybrána možnost **Použít GitHub Enterprise.** | `<your GitHub enterprise url>` |                                                           
| **Účet GitHub** | Název účtu GitHub. Tento název naleznete v\/https: /github.com/{název účtu}/{název úložiště}. Přechod na tuto stránku vás vyzve k zadání přihlašovacích údajů GitHub OAuth do vašeho účtu GitHub. | `<your GitHub account name>` |
| **Název úložiště**  | Název úložiště kódu GitHub. Účty GitHubu obsahují úložiště Git pro správu zdrojového kódu. Můžete vytvořit nové úložiště nebo použít existující úložiště, které je již ve vašem účtu. | `<your repository name>` |
| **Větev spolupráce** | Vaše větev spolupráce GitHub, která se používá pro publikování. Ve výchozím nastavení je to master. Změňte toto nastavení v případě, že chcete publikovat prostředky z jiné větve. | `<your collaboration branch>` |
| **Kořenová složka** | Kořenová složka ve větvi spolupráce GitHubu. |`<your root folder name>` |
| **Import existujících prostředků data factory do úložiště** | Určuje, zda se mají importovat existující prostředky datové továrny z vývojového plátna uživatelského rozhraní do úložiště GitHub. Vyberte pole pro import prostředků datové továrny do přidruženého úložiště Git ve formátu JSON. Tato akce exportuje každý prostředek jednotlivě (to znamená, že propojené služby a datové sady jsou exportovány do samostatných jsoňů). Pokud toto pole není zaškrtnuto, existující prostředky se neimportují. | Vybráno (výchozí) |
| **Větev pro import prostředku do** | Určuje, do které větve se importují prostředky datové továrny (kanály, datové sady, propojené služby atd.). Prostředky můžete importovat do jedné z následujících větví: a. Spolupráce b. Vytvořit nové c. Použít existující |  |

### <a name="known-github-limitations"></a>Známá omezení GitHubu

- Skripty a datové soubory můžete ukládat do úložiště GitHub. Je však nutné nahrát soubory ručně do služby Azure Storage. Kanál datové továrny automaticky nenahrává skript nebo datové soubory uložené v úložišti GitHub do úložiště Azure.

- GitHub Enterprise s verzí starší než 2.14.0 nefunguje v prohlížeči Microsoft Edge.

- Integrace GitHubu s nástroji pro vytváření vizuálních center Datové továrny funguje pouze v obecně dostupné verzi Data Factory.

- Maximálně 1 000 entit na typ prostředku (například kanály a datové sady) lze načíst z jedné větve GitHub. Pokud je tohoto limitu dosaženo, doporučujeme rozdělit prostředky do samostatných továren. Azure DevOps Git nemá toto omezení.

## <a name="switch-to-a-different-git-repo"></a>Přepnutí na jiné úložiště Git

Pokud chcete přepnout na jiné úložiště Git, klikněte na ikonu **Nastavení rychlého potočení Gitu** v pravém horním rohu stránky Přehled datové továrny. Pokud ikonu nevidíte, zrušte místní mezipaměť prohlížeče. Vyberte ikonu, chcete-li odebrat přidružení k aktuálnímu opětovnému poklidu.

![Ikona Gitu](media/author-visually/remove-repo.png)

Po otevření podokna Nastavení úložiště vyberte **Odebrat Git**. Zadejte název datové továrny a kliknutím na **potvrdit** odeberte úložiště Git přidružené k vaší továrně dat.

![Odebrání přidružení k aktuálnímu úložišti Git](media/author-visually/remove-repo2.png)

Po odebrání přidružení k aktuálnímu úložišti můžete nakonfigurovat nastavení Gitu tak, aby používalo jiné úložiště, a pak importovat existující prostředky data factory do nového úložiště. 

## <a name="version-control"></a>Správa verzí

Systémy správy verzí (označované také jako _správa zdrojového kódu_) umožňují vývojářům spolupracovat na kódu a sledovat změny, které jsou provedeny v základu kódu. Směřování zdrojového kódu je základním nástrojem pro projekty s více vývojáři.

### <a name="creating-feature-branches"></a>Vytváření větví prvků

Každé úložiště Azure Repos Git, které je přidruženo k datové továrně, má větev spolupráce. (`master` je výchozí větví spolupráce). Uživatelé mohou také vytvářet větve funkcí kliknutím na **+ Nová větev** v rozevíracím čísle větve. Po zobrazení nového podokna větve zadejte název větve funkcí.

![Vytvoření nové větve](media/author-visually/new-branch.png)

Až budete připraveni sloučit změny z větve funkcí do větve spolupráce, klikněte na rozevírací větev a vyberte **Vytvořit žádost o přijetí změn**. Tato akce vás přenese na Azure Repos Git, kde můžete vyvolat žádosti o přijetí změn, provést revize kódu a sloučit změny do větve spolupráce. (`master` je výchozí). Můžete publikovat pouze do služby Data Factory z větve spolupráce. 

![Vytvoření nové žádosti o přijetí vzbuzování](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Konfigurace nastavení publikování

Chcete-li nakonfigurovat větev publikování – tedy větev, `publish_config.json` ve které jsou uloženy šablony Správce prostředků – přidejte soubor do kořenové složky ve větvi spolupráce. Data Factory přečte tento soubor, vyhledá pole `publishBranch`a vytvoří novou větev (pokud ještě neexistuje) s poskytnutou hodnotou. Potom uloží všechny šablony Správce prostředků do zadaného umístění. Příklad:

```json
{
    "publishBranch": "factory/adf_publish"
}
```

Když zadáte novou větev publikování, Data Factory neodstraní předchozí publikovat větev. Pokud chcete předchozí publikovat větev odebrat, odstraňte ji ručně.

> [!NOTE]
> Data Factory pouze `publish_config.json` přečte soubor při načtení zvýroby. Pokud již máte na portálu načtenou továrnu, aktualizujte prohlížeč, aby se změny projevily.

### <a name="publish-code-changes"></a>Publikovat změny kódu

Po sloučení změn do větve`master` spolupráce (je výchozí) klepněte na **tlačítko Publikovat** a ručně publikujte změny kódu v hlavní větvi do služby Data Factory.

![Publikování změn ve službě Data Factory](media/author-visually/publish-changes.png)

Boční podokno se otevře tam, kde potvrdíte, že větev publikování a čekající změny jsou správné. Po ověření změn potvrďte publikování klepnutím na tlačítko **OK.**

![Potvrzení správné větve publikování](media/author-visually/configure-publish-branch.png)

> [!IMPORTANT]
> Hlavní větev není reprezentativní pro to, co je nasazeno ve službě Data Factory. Hlavní větev *musí* být publikována ručně do služby Data Factory.

## <a name="advantages-of-git-integration"></a>Výhody integrace Gitu

-   **Řízení zdrojového kódu**. Vzhledem k tomu, že vaše úlohy datové továrny se stanou klíčovými, měli byste integrovat svou továrnu s Gitem, abyste využili několik výhod správy zdrojového kódu, jako je následující:
    -   Schopnost sledovat/auditovat změny.
    -   Schopnost vrátit změny, které zavedly chyby.
-   **Částečné uložení**. Jak budete dělat spoustu změn ve vaší továrně, uvědomíte si, že v běžném režimu LIVE, nemůžete uložit změny jako návrh, protože nejste připraveni, nebo nechcete přijít o změny v případě, že váš počítač havaruje. Díky integraci Gitu můžete pokračovat v postupném ukládání změn a publikovat do továrny pouze v případě, že jste připraveni. Git funguje jako pracovní místo pro vaši práci, dokud neotestujete změny na vaši spokojenost.
-   **Spolupráce a řízení**. Pokud máte více členů týmu, kteří se účastní stejné továrny, můžete nechat své spoluhráče vzájemně spolupracovat prostřednictvím procesu kontroly kódu. Můžete také nastavit tovární nastavení tak, aby ne každý přispěvatel do výroby má oprávnění k nasazení do výroby. Členům týmu může být povoleno provádět změny prostřednictvím gitu, ale pouze někteří lidé v týmu mohou "publikovat" změny v továrně.
-   **Zobrazení rozdílů**. V režimu Git uvidíte pěkný rozdíl užitečné hodu, který se chystá publikovat do továrny. Tento rozdíl zobrazuje všechny prostředky/entity, které byly změněny nebo přidány/odstraněny od posledního publikování do továrny. Na základě tohoto rozdílu můžete buď pokračovat v publikování, nebo se vrátit a zkontrolovat změny a vrátit se později.
-   **Lepší CI /CD**. Pokud používáte režim Git, můžete nakonfigurovat kanál vydání tak, aby se automaticky aktivoval, jakmile budou provedeny změny v továrně pro zpracování. Můžete také přizpůsobit vlastnosti ve vaší továrně, které jsou k dispozici jako parametry v šabloně Správce prostředků. Může být užitečné zachovat pouze požadovanou sadu vlastností jako parametry a mít vše ostatní pevně zakódované.
-   **Lepší výkon**. Průměrná továrna se v režimu Git načte desetkrát rychleji než v běžném režimu LIVE, protože prostředky se stahují přes Git.

## <a name="best-practices-for-git-integration"></a>Doporučené postupy pro integraci Gitu

### <a name="permissions"></a>Oprávnění

Obvykle nechcete, aby každý člen týmu mít oprávnění k aktualizaci továrny. Doporučujeme následující nastavení oprávnění:

*   Všichni členové týmu by měli mít oprávnění ke čtení datové továrny.
*   Do továrny by měla být povolena pouze vybraná sada osob. Chcete-li tak učinit, musí mít roli **přispěvatele data factory** na skupinu prostředků, ve které se továrna nachází. Další informace o oprávněních najdete v [tématu Role a oprávnění pro Azure Data Factory](concepts-roles-permissions.md).
   
Doporučujeme nepovolit přímé vrácení se změnami do větve spolupráce. Toto omezení může pomoci zabránit chybám, protože každé vrácení se změnami projde procesem kontroly žádosti o přijetí uživatelům popsaným v [části Vytváření větví funkcí](source-control.md#creating-feature-branches).

### <a name="using-passwords-from-azure-key-vault"></a>Používání hesel z trezoru klíčů Azure

Doporučujeme použít Azure Key Vault k ukládání všech připojovacích řetězců nebo hesel pro propojené služby Data Factory. Z bezpečnostních důvodů žádné takové tajné informace v Gitu neukládáme, takže všechny změny propojených služeb se okamžitě publikují ve službě Azure Data Factory.

Použití trezoru klíčů také usnadňuje průběžnou integraci a nasazování, protože během nasazení šablony Správce prostředků nebudete muset tyto tajné klíče poskytovat.

## <a name="troubleshooting-git-integration"></a>Poradce při potížích s integrací Gitu

### <a name="stale-publish-branch"></a>Zatuchlá publikovat větev

Pokud je větev publikování nesynchronizovaná s hlavní větví a obsahuje zastaralé prostředky i přes nedávné publikování, vyzkoušejte následující kroky:

1. Odebrání aktuálního úložiště Git
1. Překonfigurujte Git se stejným nastavením, ale ujistěte se, že je **vybraná možnost Importovat existující prostředky Data Factory do úložiště** a zvolte Nová **větev.**
1. Vytvoření žádosti o přijetí změn ke sloučení změn do větve spolupráce 

Níže jsou uvedeny některé příklady situací, které mohou způsobit zastaralé publikovat větev:
- Uživatel má více větví. V jedné větvi funkcí vymazali propojenou službu, která není přidružena k AKV (nepropojené služby AKV jsou publikovány okamžitě bez ohledu na to, zda jsou v Gitu nebo ne) a nikdy nesloučili větev funkcí do spolupráce brnach.
- Uživatel upravil datovou továrnu pomocí sady SDK nebo Prostředí PowerShell
- Uživatel přesunul všechny prostředky do nové větve a pokusil se je poprvé publikovat. Propojené služby by měly být vytvořeny ručně při importu prostředků.
- Uživatel nahraje službu propojenou s akv nebo integrační runtime JSON ručně. Odkazují na tento prostředek z jiného prostředku, jako je například datová sada, propojená služba nebo kanál. Propojená služba bez AKV vytvořená prostřednictvím uživatelského uživatelského nastavení je publikována okamžitě, protože pověření musí být zašifrována. Pokud nahrajete datovou sadu odkazující na tuto propojenou službu a pokusíte se ji publikovat, uživatelské prostředí ji povolí, protože existuje v prostředí git. Bude odmítnuta v době publikování, protože ve službě datové továrny neexistuje.

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
Vyberte **Zpětná vazba,** která chcete komentovat funkce nebo upozornit společnost Microsoft na problémy s nástrojem:

![Váš názor](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Další kroky

* Další informace o monitorování a správě kanálů najdete v tématu [Programové sledování a správa kanálů](monitor-programmatically.md).
* Implementovat průběžnou integraci a nasazování najdete [v tématu Průběžná integrace a doručování (CI/CD) v Azure Data Factory](continuous-integration-deployment.md).
