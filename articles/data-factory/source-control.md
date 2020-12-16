---
title: Správa zdrojového kódu
description: Informace o tom, jak nakonfigurovat správu zdrojového kódu v Azure Data Factory
services: data-factory
ms.service: data-factory
ms.workload: data-services
author: dcstwh
ms.author: weetok
manager: anandsub
ms.reviewer: ''
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/02/2020
ms.openlocfilehash: e839b8a22da50ce172043f57a4467219a1771175
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584197"
---
# <a name="source-control-in-azure-data-factory"></a>Správa zdrojového kódu v Azure Data Factory
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Ve výchozím nastavení Azure Data Factory autoři uživatelského rozhraní (UX) přímo na službu Data Factory. Toto prostředí má následující omezení:

- Služba Data Factory nezahrnuje úložiště pro ukládání entit JSON pro vaše změny. Jediným způsobem, jak změny uložit, je prostřednictvím tlačítka **publikovat vše** a všechny změny se publikují přímo ve službě Data Factory.
- Služba Data Factory není optimalizovaná pro spolupráci a správu verzí.

Aby se zajistilo lepší prostředí pro vytváření, Azure Data Factory vám umožní nakonfigurovat úložiště Git buď pomocí Azure Repos, nebo GitHubu. Git je systém správy verzí, který umožňuje snazší sledování změn a spolupráci. V tomto článku se dozvíte, jak nakonfigurovat a pracovat v úložišti Git společně s důrazem na osvědčené postupy a Průvodce odstraňováním potíží.

> [!NOTE]
> Integrace Git Azure Data Factory je k dispozici pouze pro GitHub Enterprise v cloudu Azure Government.

Další informace o tom, jak se Azure Data Factory integruje s Git, najdete v níže uvedeném videu kurzu na 15 minutách:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4GNKv]

## <a name="advantages-of-git-integration"></a>Výhody integrace Gitu

Níže je uveden seznam některých výhod integrace Gitu do prostředí pro vytváření obsahu:

-   **Správa zdrojového kódu:** Vzhledem k tomu, že se vaše úlohy datové továrny stanou rozhodující, byste měli svůj objekt pro vytváření integrovat do Gitu a využít přitom několik výhod správy zdrojového kódu, jako jsou tyto:
    -   Schopnost sledovat nebo auditovat změny.
    -   Možnost vrátit změny, které zavedly chyby.
-   **Částečné uložení:** Při vytváření ve službě Data Factory nemůžete uložit změny jako koncept a všechny publikace musí předat ověření Data Factory. Bez ohledu na to, jestli vaše kanály nejsou dokončené, nebo pokud nechcete přijít o změny, pokud dojde k chybě počítače, integrace Git umožňuje přírůstkové změny prostředků služby Data Factory bez ohledu na stav, ve kterém se nacházejí. Konfigurace úložiště Git vám umožní uložit změny, takže budete moct publikovat jenom v případě, že jste své změny sami otestovali.
-   **Spolupráce a řízení:** Pokud máte více členů týmu přispívajících ke stejné továrně, můžete chtít, aby vaše ostatními týmu vzájemně spolupracovaly prostřednictvím procesu revize kódu. Můžete také nastavit továrnu tak, že ne každý přispěvatel má stejné oprávnění. Někteří členové týmu můžou povolit změny jenom přes Git a jenom někteří lidé v týmu můžou publikovat změny v továrně.
-   **Lepší CI/CD:**  Pokud nasazujete do více prostředí s [nepřetržitým procesem doručování](continuous-integration-deployment.md), integrace Git usnadňuje určité akce. Mezi tyto akce patří:
    -   Nakonfigurujte svůj kanál pro vydávání verzí tak, aby se automaticky aktivoval, jakmile se ve vašem vývojovém objektu pro vývoj provádí nějaké změny.
    -   Přizpůsobení vlastností ve vaší továrně, které jsou k dispozici jako parametry v šabloně Správce prostředků. Může být užitečné zachovat jenom požadovanou sadu vlastností jako parametry a mít vše dalšího pevně kódovaného kódu.
-   **Lepší výkon:** Průměrná továrna s integrací Git se za víc než jedním vytvářením ve službě Data Factory načítá víckrát. Zlepšení výkonu je způsobeno tím, že se prostředky stahují prostřednictvím Gitu.

> [!NOTE]
> Při konfiguraci úložiště Git je v uživatelském prostředí Azure Data Factory zakázáno vytváření obsahu přímo pomocí služby Data Factory. Změny provedené prostřednictvím PowerShellu nebo sady SDK se publikují přímo ve službě Data Factory a nezadávají se do Gitu.

## <a name="connect-to-a-git-repository"></a>Připojení k úložišti Git

Existují čtyři různé způsoby, jak připojit úložiště Git k datové továrně pro Azure Repos i GitHub. Po připojení k úložišti Git můžete zobrazit a spravovat konfiguraci v [centru pro správu](author-management-hub.md) v části **Konfigurace Gitu** v části Správa **zdrojového kódu** .

### <a name="configuration-method-1-home-page"></a>Konfigurační Metoda 1: Domovská stránka

Na domovské stránce Azure Data Factory vyberte **nastavit úložiště kódu**.

![Konfigurace úložiště kódu z domovské stránky](media/author-visually/configure-repo.png)

### <a name="configuration-method-2-authoring-canvas"></a>Konfigurační Metoda 2: plátno pro vytváření obsahu

Na plátně pro vytváření Azure Data Factory UX vyberte **Data Factory** rozevírací nabídku a pak vyberte **nastavit úložiště kódu**.

![Konfigurace nastavení úložiště kódu z vytváření obsahu](media/author-visually/configure-repo-2.png)

### <a name="configuration-method-3-management-hub"></a>Konfigurační Metoda 3: Centrum správy

V uživatelském prostředí ADF se můžete dostat do centra správy. V části **Správa zdrojového kódu** vyberte **Konfigurace Gitu** . Pokud nemáte připojené žádné úložiště, klikněte na **nastavit úložiště kódu**.

![Konfigurace nastavení úložiště kódu z centra pro správu](media/author-visually/configure-repo-3.png)

### <a name="configuration-method-4-during-factory-creation"></a>Konfigurační Metoda 4: během vytváření továrny

Při vytváření nové datové továrny v Azure Portal můžete na kartě **Konfigurace Git** nakonfigurovat informace o úložišti Git.

> [!NOTE]
> Při konfiguraci Gitu na webu Azure Portal musí být nastavení, jako je název projektu a název úložiště, ručně zadáno jako součást rozevíracího seznamu.

![Konfigurace nastavení úložiště kódu z webu Azure Portal](media/author-visually/configure-repo-4.png)

## <a name="author-with-azure-repos-git-integration"></a>Vytváření s využitím integrace Gitu s Azure Repos

Vytváření vizuálního obsahu pomocí Azure Repos integrace Gitu podporuje správu zdrojového kódu a spolupráci pro práci na kanálech služby Data Factory. Datovou továrnu můžete přidružit k Azure Repos úložiště organizace Git pro správu zdrojového kódu, spolupráci, správu verzí atd. Jediná Azure Repos organizace Git může mít víc úložišť, ale Azure Repos úložiště Git se dá přidružit jenom k jednomu objektu pro vytváření dat. Pokud nemáte Azure Repos organizaci nebo úložiště, vytvořte prostředky podle [těchto pokynů](/azure/devops/organizations/accounts/create-organization-msa-or-work-student) .

> [!NOTE]
> Skripty a datové soubory můžete ukládat do Azure Repos úložiště Git. Soubory však budete muset odeslat ručně, aby bylo možné Azure Storage. Kanál služby Data Factory automaticky neodesílá skript nebo datové soubory uložené v úložišti Git Azure Repos do Azure Storage.

### <a name="azure-repos-settings"></a>Nastavení Azure Repos

![Konfigurace nastavení úložiště kódu](media/author-visually/repo-settings.png)

Podokno konfigurace zobrazuje následující Azure Repos nastavení úložiště kódu:

| Nastavení | Popis | Hodnota |
|:--- |:--- |:--- |
| **Typ úložiště** | Typ úložiště kódu Azure Repos.<br/> | Azure DevOps Git nebo GitHub |
| **Azure Active Directory** | Název vašeho tenanta Azure AD. | `<your tenant name>` |
| **Azure Repos organizace** | Název vaší Azure Repos organizace Název vaší Azure Repos organizace najdete na adrese `https://{organization name}.visualstudio.com` . K vašemu [Azure Repos organizaci](https://www.visualstudio.com/team-services/git/) se můžete přihlásit, abyste měli přístup k profilu sady Visual Studio a viděli vaše úložiště a projekty. | `<your organization name>` |
| **Názevprojektu** | Název projektu Azure Repos. Název projektu Azure Repos můžete najít na adrese `https://{organization name}.visualstudio.com/{project name}` . | `<your Azure Repos project name>` |
| **Úložiště** | Váš Azure Repos název úložiště kódu. Azure Repos projekty obsahují úložiště Git pro správu zdrojového kódu při zvětšování projektu. Můžete vytvořit nové úložiště nebo použít existující úložiště, které už je v projektu. | `<your Azure Repos code repository name>` |
| **Větev pro spolupráci** | Vaše větev Azure Repos spolupráci, která se používá k publikování. Ve výchozím nastavení je to `main` . Toto nastavení změňte pro případ, že chcete publikovat prostředky z jiné větve. | `<your collaboration branch name>` |
| **Kořenová složka** | Kořenová složka ve větvi Azure Repos spolupráci. | `<your root folder name>` |
| **Importovat stávající prostředky Data Factory do úložiště** | Určuje, jestli se mají importovat stávající prostředky služby Data Factory z **plátna pro vytváření** uživatelského rozhraní, do úložiště Git Azure Repos. Zaškrtněte políčko pro import prostředků datové továrny do přidruženého úložiště Git ve formátu JSON. Tato akce exportuje jednotlivé prostředky jednotlivě (tj. propojené služby a datové sady jsou exportovány do samostatných JSON). Pokud toto políčko není zaškrtnuté, existující prostředky se neimportují. | Vybráno (výchozí) |
| **Vytvořit větev pro import prostředku** | Určuje, do které větve se importují prostředky služby Data Factory (kanály, datové sady, propojené služby atd.). Prostředky můžete importovat do jedné z následujících větví: a. Spolupráci b. Vytvořte novou c. Použít existující |  |

> [!NOTE]
> Pokud používáte Microsoft Edge a v rozevíracím seznamu účtu Azure DevOps nevidíte žádné hodnoty, přidejte do seznamu důvěryhodných webů protokol https://*. VisualStudio. com.

### <a name="use-a-different-azure-active-directory-tenant"></a>Použít jiného tenanta Azure Active Directory

Azure Repos úložiště Git se může nacházet v jiném tenantovi Azure Active Directory. Pokud chcete zadat jiného tenanta Azure AD, musíte mít oprávnění správce pro předplatné Azure, které používáte. Další informace najdete v tématu [Změna správce předplatného](../cost-management-billing/manage/add-change-subscription-administrator.md#to-assign-a-user-as-an-administrator) .

> [!IMPORTANT]
> Pokud se chcete připojit k jinému Azure Active Directory, přihlášený uživatel musí být součástí služby Active Directory. 

### <a name="use-your-personal-microsoft-account"></a>Použití osobních účet Microsoft

Pokud chcete použít osobní účet Microsoft pro integraci Gitu, můžete své osobní úložiště Azure propojit se službou Active Directory vaší organizace.

1. Přidejte osobní účet Microsoft do služby Active Directory vaší organizace jako hosta. Další informace najdete v tématu [přidání Azure Active Directory uživatelů spolupráce B2B v Azure Portal](../active-directory/external-identities/add-users-administrator.md).

2. Přihlaste se k Azure Portal pomocí osobního účet Microsoft. Pak přejděte do služby Active Directory vaší organizace.

3. Přejděte do části Azure DevOps, kde se teď zobrazuje vaše osobní úložiště. Vyberte úložiště a připojte se ke službě Active Directory.

Po provedení těchto kroků konfigurace bude osobní úložiště k dispozici při nastavení integrace Gitu v uživatelském rozhraní Data Factory.

Další informace o připojení Azure Repos ke službě Active Directory vaší organizace najdete v tématu věnovaném [připojení organizace Azure DevOps k Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad).

## <a name="author-with-github-integration"></a>Vytváření s využitím integrace GitHubu

Vytváření vizuálů pomocí integrace GitHubu podporuje správu zdrojového kódu a spolupráci pro práci na kanálech služby Data Factory. Datovou továrnu můžete přidružit k úložišti účtů GitHub pro správu zdrojového kódu, spolupráci, správu verzí. Jeden účet GitHub může mít několik úložišť, ale úložiště GitHubu je možné přidružit pouze k jednomu objektu pro vytváření dat. Pokud nemáte účet GitHub nebo úložiště, vytvořte prostředky podle [těchto pokynů](https://github.com/join) .

Integrace GitHubu s Data Factory podporuje veřejné GitHub (tj [https://github.com](https://github.com) .) i GitHub Enterprise. Pokud máte oprávnění ke čtení a zápisu do úložiště v GitHubu, můžete použít veřejné i soukromé úložiště GitHub s Data Factory.

Abyste mohli nakonfigurovat úložiště GitHubu, musíte mít oprávnění správce pro předplatné Azure, které používáte.

### <a name="github-settings"></a>Nastavení GitHubu

![Nastavení úložiště GitHub](media/author-visually/github-integration-image2.png)

V podokně Konfigurace se zobrazí následující nastavení úložiště GitHub:

| **Nastavení** | **Popis**  | **Hodnota**  |
|:--- |:--- |:--- |
| **Typ úložiště** | Typ úložiště kódu Azure Repos. | GitHubu |
| **Použití GitHubu Enterprise** | Zaškrtávací políčko pro výběr GitHubu Enterprise | nevybráno (výchozí) |
| **Adresa URL GitHubu Enterprise** | Kořenová adresa URL pro GitHub Enterprise (musí být HTTPS pro místní server GitHub Enterprise). Například: `https://github.mydomain.com`. Požadováno jenom v případě, že je vybraná **možnost použít GitHub Enterprise** | `<your GitHub enterprise url>` |                                                           
| **Účet GitHub** | Název vašeho účtu GitHubu. Tento název najdete v názvu https: \/ /GitHub.com/{account}/{repository Name}. Když přejdete na tuto stránku, zobrazí se výzva k zadání přihlašovacích údajů GitHubu OAuth do svého účtu GitHubu. | `<your GitHub account name>` |
| **Název úložiště**  | Název vašeho úložiště kódu GitHubu Účty GitHubu obsahují úložiště Git pro správu zdrojového kódu. Můžete vytvořit nové úložiště nebo použít existující úložiště, které už máte ve svém účtu. | `<your repository name>` |
| **Větev pro spolupráci** | Vaše větev pro spolupráci GitHubu, která se používá k publikování. Ve výchozím nastavení je to hlavní. Toto nastavení změňte pro případ, že chcete publikovat prostředky z jiné větve. | `<your collaboration branch>` |
| **Kořenová složka** | Kořenová složka ve větvi spolupráce na GitHubu. |`<your root folder name>` |
| **Importovat stávající prostředky Data Factory do úložiště** | Určuje, jestli se mají importovat stávající prostředky služby Data Factory z plátna pro vytváření uživatelského rozhraní do úložiště GitHubu. Zaškrtněte políčko pro import prostředků datové továrny do přidruženého úložiště Git ve formátu JSON. Tato akce exportuje jednotlivé prostředky jednotlivě (tj. propojené služby a datové sady jsou exportovány do samostatných JSON). Pokud toto políčko není zaškrtnuté, existující prostředky se neimportují. | Vybráno (výchozí) |
| **Vytvořit větev pro import prostředku** | Určuje, do které větve se importují prostředky služby Data Factory (kanály, datové sady, propojené služby atd.). Prostředky můžete importovat do jedné z následujících větví: a. Spolupráci b. Vytvořte novou c. Použít existující |  |

### <a name="github-organizations"></a>Organizace GitHubu

Připojení k organizaci GitHubu vyžaduje, aby organizace udělila oprávnění Azure Data Factory. Uživatel s oprávněními správce v organizaci musí provést následující kroky, aby se data Factory mohla připojit.

#### <a name="connecting-to-github-for-the-first-time-in-azure-data-factory"></a>Připojení k GitHubu poprvé v Azure Data Factory

Pokud se k GitHubu připojujete poprvé z Azure Data Factory, postupujte podle těchto kroků a připojte se k organizaci GitHubu.

1. V podokně Konfigurace Gitu zadejte název organizace do pole *účet GitHub* . Zobrazí se výzva k přihlášení do GitHubu. 
1. Přihlaste se pomocí přihlašovacích údajů uživatele.
1. Zobrazí se výzva k autorizaci Azure Data Factory jako aplikace s názvem *AzureDataFactory*. Na této obrazovce se zobrazí možnost udělit oprávnění ADF pro přístup k organizaci. Pokud nevidíte možnost udělit oprávnění, požádejte správce, aby oprávnění ručně udělil prostřednictvím GitHubu.

Po provedení těchto kroků se továrna bude moci připojit k veřejným i privátním úložištím v rámci vaší organizace. Pokud se nemůžete připojit, zkuste vyprázdnit mezipaměť prohlížeče a zkusit to znovu.

#### <a name="already-connected-to-github-using-a-personal-account"></a>Už jste připojení k GitHubu pomocí osobního účtu.

Pokud jste se už připojili k GitHubu a udělili jste mu oprávnění k přístupu k osobnímu účtu, pomocí následujících kroků udělíte oprávnění k organizaci. 

1. Přejít na GitHub a otevřít **Nastavení**.

    ![Otevřít nastavení GitHubu](media/author-visually/github-settings.png)

1. Vyberte **aplikace**. Na kartě **autorizované aplikace OAuth** by se měla zobrazit *AzureDataFactory*.

    ![Vybrat aplikace OAuth](media/author-visually/github-organization-select-application.png)

1. Vyberte aplikaci a udělte aplikaci přístup k vaší organizaci.

    ![Udělení přístupu](media/author-visually/github-organization-grant.png)

Po provedení těchto kroků se továrna bude moci připojit k veřejným i privátním úložištím v rámci vaší organizace. 

### <a name="known-github-limitations"></a>Známá omezení GitHubu

- Skripty a datové soubory můžete ukládat do úložiště GitHub. Soubory však budete muset odeslat ručně, aby bylo možné Azure Storage. Kanál Data Factory neodesílá automaticky do Azure Storage skript nebo datové soubory uložené v úložišti GitHubu.

- GitHub Enterprise s verzí starší než 2.14.0 nefunguje v prohlížeči Microsoft Edge.

- Integrace GitHubu s nástroji Data Factoryho vizuálního vytváření funguje jenom v všeobecně dostupné verzi Data Factory.


- Z jedné větve GitHubu lze načíst maximálně 1 000 entit na typ prostředku (například kanály a datové sady). Pokud je dosaženo tohoto limitu, navrhne se rozdělení prostředků do samostatných továrn. Pro Azure DevOps Git toto omezení neplatí.

## <a name="version-control"></a>Správa verzí

Systémy správy verzí (označované také jako _Správa zdrojového_ kódu) umožňují vývojářům spolupracovat na kódu a sledovat změny, které jsou provedeny v základu kódu. Správa zdrojového kódu je důležitým nástrojem pro projekty s více vývojáři.

### <a name="creating-feature-branches"></a>Vytváření větví funkcí

Každé Azure Repos úložiště Git, které je přidružené k datové továrně, má větev pro spolupráci. ( `main` ) je výchozí větev pro spolupráci). Uživatelé můžou také vytvářet větve funkcí kliknutím na **+ Nová větev** v rozevíracím seznamu větev. Jakmile se zobrazí podokno nová větev, zadejte název vaší větve funkce.

![Vytvořit novou větev](media/author-visually/new-branch.png)

Až budete připraveni sloučit změny z větve funkcí do vaší větve pro spolupráci, klikněte na rozevírací seznam větev a vyberte **vytvořit žádost o získání dat**. Tato akce vás provede Azure Repos Git, kde můžete vyvolávat žádosti o přijetí změn, provádět revize kódu a sloučit změny ve větvi pro spolupráci. ( `main` výchozí). Do služby Data Factory se povoluje pouze publikování z vaší větve pro spolupráci. 

![Vytvořit novou žádost o získání dat](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Konfigurovat nastavení publikování

Ve výchozím nastavení služba Data Factory generuje šablony Správce prostředků publikovaných objektů pro vytváření a ukládá je do větve s názvem `adf_publish` . Chcete-li nakonfigurovat vlastní větev publikování, přidejte `publish_config.json` soubor do kořenové složky ve větvi pro spolupráci. Při publikování tento soubor přečte ADF, vyhledá pole `publishBranch` a uloží všechny správce prostředků šablony do zadaného umístění. Pokud větev neexistuje, vytvoří se data Factory automaticky. A příklad toho, jak tento soubor vypadá:

```json
{
    "publishBranch": "factory/adf_publish"
}
```

Azure Data Factory může mít současně jen jednu větev publikování. Když zadáte novou větev publikování, Data Factory neodstraní předchozí větev publikování. Pokud chcete odebrat předchozí větev publikování, odstraňte ji ručně.

> [!NOTE]
> `publish_config.json`Při načtení objektu pro vytváření načte data Factory jenom soubor. Pokud už máte v portálu načtenou továrnu, aktualizujte prohlížeč, aby se změny projevily.

### <a name="publish-code-changes"></a>Publikování změn kódu

Po sloučení změn ve větvi pro spolupráci ( `main` výchozí nastavení) klikněte na **publikovat** a ručně publikujte změny kódu v hlavní větvi služby Data Factory.

![Publikování změn ve službě Data Factory](media/author-visually/publish-changes.png)

Otevře se boční podokno, kde ověříte, že je větev publikování a nedokončené změny správná. Po ověření změn potvrďte publikování kliknutím na tlačítko **OK** .

![Potvrďte správnou větev publikování.](media/author-visually/configure-publish-branch.png)

> [!IMPORTANT]
> Hlavní větev nepředstavuje zástupce toho, co je ve službě Data Factory nasazené. Hlavní větev *musí* být publikovaná ručně ve službě Data Factory.

## <a name="best-practices-for-git-integration"></a>Osvědčené postupy pro integraci Git

### <a name="permissions"></a>Oprávnění

Obvykle nechcete, aby každý člen týmu měl oprávnění k aktualizaci Data Factory. Doporučuje se následující nastavení oprávnění:

*   Všichni členové týmu by měli mít oprávnění ke čtení Data Factory.
*   Pro publikování do Data Factory by mělo být povoleno pouze vybrat sadu osob. Aby to bylo možné, musí mít roli **přispěvatel Data Factory** ve **skupině prostředků** , která obsahuje data Factory. Další informace o oprávněních najdete v tématu [role a oprávnění pro Azure Data Factory](concepts-roles-permissions.md).

Doporučuje se nepovoluje přímá vrácení se změnami do větve pro spolupráci. Toto omezení může zabránit chybám, protože každé vrácení se změnami projde procesem revize žádosti o přijetí změn popsaným v [tématu vytváření větví funkcí](source-control.md#creating-feature-branches).

### <a name="using-passwords-from-azure-key-vault"></a>Používání hesel z Azure Key Vault

Doporučuje se použít Azure Key Vault k uložení připojovacích řetězců nebo hesel nebo ověřování spravované identity pro Data Factory propojených služeb. Z bezpečnostních důvodů Data Factory neukládá tajné klíče do Gitu. Jakékoli změny propojených služeb obsahujících tajné klíče, jako jsou hesla, se okamžitě publikují do služby Azure Data Factory.

Použití Key Vault nebo ověřování MSI také usnadňuje průběžnou integraci a nasazování, protože nebudete muset zadávat tyto tajné klíče během nasazování šablon Správce prostředků.

## <a name="troubleshooting-git-integration"></a>Řešení potíží s integrací Gitu

### <a name="stale-publish-branch"></a>Zastaralá větev publikování

Pokud větev publikování není synchronizovaná s hlavní větví a obsahuje zastaralé prostředky navzdory nedávnému publikování, zkuste provést následující kroky:

1. Odebrat aktuální úložiště Git
1. Překonfigurujte Git se stejnými nastaveními, ale ujistěte se, že je vybraná možnost **importovat existující data Factory prostředky do úložiště** , a zvolit **nové větve** .
1. Vytvořte žádost o přijetí změn, aby se změny sloučily do větve spolupráce. 

Níže jsou uvedeny některé příklady situací, které mohou způsobit zastaralou větev publikování:
- Uživatel má více větví. V jedné větvi funkce odstranili propojenou službu, která není integrace přidružená (neintegrace propojené služby se publikují hned bez ohledu na to, jestli jsou v Gitu nebo ne), a nikdy nesloučí větev funkcí do větve pro spolupráci.
- Uživatel změnil datovou továrnu pomocí sady SDK nebo PowerShellu.
- Uživatel přesunul všechny prostředky do nové větve a pokusil se o publikování poprvé. Propojené služby by se měly vytvářet ručně při importu prostředků.
- Uživatel nahraje propojenou službu bez integrace nebo Integration Runtime JSON ručně. Odkazují na tento prostředek z jiného prostředku, jako je datová sada, propojená služba nebo kanál. Propojená služba, která není integrace vytvořená prostřednictvím uživatelského rozhraní, se publikuje hned, protože přihlašovací údaje musí být šifrované. Pokud nahrajete datovou sadu odkazující na tuto propojenou službu a pokusíte se ji publikovat, bude ji uživatelské prostředí umožňovat, protože existuje v prostředí Git. Bude odmítnuta v době publikování, protože ve službě Data Factory neexistuje.

## <a name="switch-to-a-different-git-repository"></a>Přepnout na jiné úložiště Git

Pokud chcete přepnout na jiné úložiště Git, přejděte na stránku konfigurace Git v centru správy pod správou **zdrojových kódů**. Vyberte **Odpojit**. 

![Ikona Git](media/author-visually/remove-repository.png)

Zadejte název datové továrny a kliknutím na **Potvrdit** odeberte úložiště Git přidružené k vaší datové továrně.

![Odebere přidružení k aktuálnímu úložišti Git.](media/author-visually/remove-repository-2.png)

Po odebrání přidružení s aktuálním úložištěm můžete nakonfigurovat nastavení Gitu tak, aby používalo jiné úložiště, a pak do nového úložiště importovat existující Data Factory prostředky.

> [!IMPORTANT]
> Odebráním konfigurace git z datové továrny se neodstraňují cokoli z úložiště. Objekt pro vytváření bude obsahovat všechny publikované prostředky. Můžete pokračovat v úpravě továrny přímo na službu.

## <a name="next-steps"></a>Další kroky

* Další informace o monitorování a správě kanálů najdete v tématu [monitorování a Správa kanálů prostřednictvím kódu programu](monitor-programmatically.md).
* Chcete-li implementovat průběžnou integraci a nasazování, přečtěte si téma [průběžná integrace a doručování (CI/CD) v Azure Data Factory](continuous-integration-deployment.md).
