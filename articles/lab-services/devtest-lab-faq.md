---
title: Nejčastější dotazy k Azure DevTest Labs | Dokumentace Microsoftu
description: Najděte odpovědi na běžné dotazy týkající se Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: afe83109-b89f-4f18-bddd-b8b4a30f11b4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2019
ms.author: spelluru
ms.openlocfilehash: a46d816c04d9f5629c2ee9538016d42c53f9a331
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244391"
---
# <a name="azure-devtest-labs-faq"></a>Nejčastější dotazy k Azure DevTest Labs
Získejte odpovědi na některé nejběžnější otázky o Azure DevTest Labs.

## <a name="blog-post"></a>Blogový příspěvek
Náš blog týmu DevTest Labs je vyřazený od 20. března 2019. 

### <a name="where-can-i-track-feature-updates-from-now-on"></a>Kde mohu od této chvíle sledovat aktualizace funkcí?
Od této chvíle jsme budete mít účtování aktualizace funkcí a informativní blogových příspěvků na blogu Azure a Azure aktualizuje. Příspěvky na blogu také odkaz na dokumentaci bez ohledu na to povinné.

Přihlaste se k odběru [blogu Azure DevTest Labs](https://azure.microsoft.com/blog/tag/azure-devtest-labs/) a [DevTest Labs Azure aktualizuje](https://azure.microsoft.com/updates/?product=devtest-lab) tak neustále informováni o nových funkcích ve službě DevTest Labs.

### <a name="what-happens-to-the-existing-blog-posts"></a>Co se stane v existující blogových příspěvcích?
Aktuálně pracujeme na migraci stávajících blogových příspěvků (s výjimkou aktualizace výpadek) na naše [dokumentace k DevTest Labs](devtest-lab-overview.md). Když v blogu MSDN je zastaralý, jeho přesměrováni na přehled dokumentace pro DevTest Labs. Po přesměrování, můžete vyhledat článek, který hledáte, v názvu "Filtrování podle". Jsme ještě nebyly migrovány všechny příspěvky, ale má počítat od konce tohoto měsíce. 


### <a name="where-do-i-see-outage-updates"></a>Kde se zobrazí aktualizace výpadku?
Jsme budete mít účtování výpadek aktualizací pomocí našich popisovač Twitteru od tohoto okamžiku a vyšší. Sledujte nás na Twitteru, chcete-li získat nejnovější informace o výpadcích a známé chyby.

### <a name="twitter"></a>Twitter
Naše popisovač Twitteru: [@azlabservices](https://twitter.com/azlabservices)

## <a name="general"></a>Obecné
### <a name="what-if-my-question-isnt-answered-here"></a>Co když můj dotaz zde nenalezl?
Pokud zde není uveden váš dotaz, dejte nám vědět, abychom mohli pomoct najít odpověď.

- Odešlete dotaz na konci tyto Nejčastější dotazy. Spolupracujte s týmem Azure Cache a ostatních členů komunity o tomto článku.
- K dosažení širší cílovou skupinu, odeslat dotaz na [fóra Azure DevTest Labs MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs). Spolupracujte s týmem Azure DevTest Labs a ostatní členové komunity.
- Pro žádosti o funkce odeslání žádosti a nápady, jak ho [Azure DevTest Labs User Voice](https://feedback.azure.com/forums/320373-azure-devtest-labs).

### <a name="what-is-a-microsoft-account"></a>Co je účet Microsoft?
Účet Microsoft je účet, který používáte pro téměř vše, co dělat s Microsoft zařízení a služeb. Jde e-mailovou adresu a heslo, které používáte pro přihlášení na Skype, Outlook.com, OneDrive, Windows phone, Azure a Xbox Live. Jeden účet znamená, že soubory, fotografie, kontakty a nastavení můžete sledovat na libovolném zařízení.
 
> [!NOTE]
> Účet Microsoft používá říkalo Windows Live ID.

### <a name="why-should-i-use-azure-devtest-labs"></a>Proč je vhodné použít Azure DevTest Labs?
Azure DevTest Labs můžete uložit týmu čas i peníze. Vývojáři mohou vytvářet vlastní prostředí pomocí několika různých základů. Také použitím artefakty kvůli rychlému nasazení a konfigurace aplikací. Pomocí vlastních imagí a vzorců, můžete uložit jako šablony virtuálních počítačů (VM) a snadno reprodukovat napříč týmem. DevTest Labs také nabízí několik Konfigurovatelné zásady tohoto testovacího prostředí, které správcům umožňuje omezit Mrhání prostředky a spravovat prostředí týmu. Tyto zásady patří automatické vypnutí, prahové hodnoty nákladů, maximální virtuálních počítačů na uživatele a maximální velikost virtuálního počítače. Podrobnější vysvětlení DevTest Labs, najdete v článku [přehled](devtest-lab-overview.md) nebo [úvodní video](https://channel9.msdn.com/Blogs/Azure/what-is-azure-devtest-labs).

### <a name="what-does-worry-free-self-service-mean"></a>Co znamená "bez obav samoobslužné služby"?
Bez obav samoobslužné funkce znamená, že vývojářům a testerům vytvářet vlastní prostředí podle potřeby. Správci mají zabezpečení vědomím, že DevTest Labs pomůže nastavit odpovídající přístup, minimalizovat ztráty a mít náklady pod kontrolou. Správci mohou určit, jaké velikosti virtuálních počítačů jsou povolené a maximální počet virtuálních počítačů, a virtuální počítače jsou při spuštění a vypnutí. DevTest Labs také umožňuje snadno monitorovat náklady a nastavení výstrah můžete zůstat vědět, jak se používají prostředky testovacího prostředí.

### <a name="how-can-i-use-devtest-labs"></a>Použití DevTest Labs
DevTest Labs je užitečná, když mají dev nebo testovací prostředí a chcete rychle reprodukovat nebo spravovat pomocí zásad hospodárnému.
Tady je několik scénářů, které naši zákazníci využívat DevTest Labs pro:

- Správa vývoje a testovacích prostředí na jednom místě. Používání zásad ke snížení nákladů a vytváření vlastních imagí sdílet sestavení napříč týmem.
- Vývoj aplikace s využitím vlastní Image pro uložení stavu disku v průběhu fází vývoje.
- Sledování nákladů ve vztahu k pokroku.
- Vytvoření velkokapacitního testovací prostředí pro testování assurance kvality.
- Použít artefakty a vzorce snadno nakonfigurovat a reprodukovat aplikace v různých prostředích.
- Distribuce virtuální počítače pro hackathony (vývoj nebo testování týmovém) a potom snadno zrušit jejich zřízení až událost skončí.

### <a name="how-am-i-billed-for-devtest-labs"></a>Jak se fakturují DevTest Labs?
DevTest Labs je bezplatná služba. Vytváření testovacích prostředí a konfiguraci zásad, šablon a artefaktů ve službě DevTest Labs je bezplatná. Platíte jenom za prostředky Azure používané v laboratořích, jako jsou virtuální počítače, účty úložiště a virtuální sítě. Další informace o nákladech prostředky testovacího prostředí naleznete v tématu [ceny Azure DevTest Labs](https://azure.microsoft.com/pricing/details/devtest-lab/).

## <a name="security"></a>Zabezpečení

### <a name="what-are-the-different-security-levels-in-devtest-labs"></a>Co jsou úrovně zabezpečení ve službě DevTest Labs?
Zabezpečení přístupu je určeno řízení přístupu na základě Role (RBAC). Informace o tom, jak funguje přístup, pomáhá další rozdíly mezi oprávnění, role a obor, jak jsou definovány pomocí RBAC.

- **Oprávnění**: Oprávnění je definované přístup k určité akce. Oprávnění může být například přístup pro čtení pro všechny virtuální počítače.
- **Role**: Role je sadu oprávnění, které mohou být seskupeny a přiřazená uživateli. Například uživatel s rolí vlastník předplatného má přístup ke všem prostředkům v rámci předplatného.
- **Obor**: Obor je úroveň v hierarchii k prostředku Azure. Obor může být například skupinu prostředků, jednoho testovacího prostředí nebo celé předplatné.

V rámci oboru DevTest Labs existují dva typy rolí, které definují uživatelská oprávnění:

- **Vlastník testovacího prostředí**: Vlastník testovacího prostředí má přístup ke všem prostředkům v testovacím prostředí. Vlastník testovacího prostředí můžete upravovat zásady, čtení a zápis pro všechny virtuální počítače, změnit virtuální síť a podobně.
- **Uživatele testovacího prostředí**: Uživatele testovacího prostředí můžete zobrazit všechny prostředky testovacího prostředí, jako jsou virtuální počítače, zásady a virtuální sítě. Uživatele testovacího prostředí. nelze však změnit zásady nebo všechny virtuální počítače, které byly vytvořené jinými uživateli.

Můžete také vytvořit vlastní role ve službě DevTest Labs. Zjistěte, jak vytvořit vlastní role ve službě DevTest Labs, najdete v článku [udělení uživatelských oprávnění na určitém laboratorním zásady](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Protože obory jsou hierarchické, když má uživatel oprávnění v určitém rozsahu, uživateli je udělen těchto oprávnění v každé nižší úrovni oboru v oboru. Například pokud uživatel je přiřazena role vlastníka předplatného, má uživatel přístup ke všem prostředkům v rámci předplatného. Tyto prostředky zahrnují virtuální počítače, virtuální sítě a testovací prostředí. Vlastník předplatného automaticky dědí role vlastník testovacího prostředí. Ale opak není true. Vlastník testovacího prostředí má přístup k testovacím prostředí, které je obor nižší než úroveň předplatného. Vlastník testovacího prostředí tak, neuvidí virtuálních počítačů, virtuálních sítí nebo jiné prostředky, které jsou mimo testovacího prostředí.

### <a name="how-do-i-define-role-based-access-control-for-my-devtest-labs-environments-to-ensure-that-it-can-govern-while-developerstest-can-do-their-work"></a>Jak definovat řízení přístupu na základě rolí pro moje DevTest Labs prostředí, abyste, který může IT řídit při vývojáři a testování můžete své práci?
Neexistuje široké vzor, ale podrobností závisí na vaší organizaci.

Centrální IT by měl vlastní nezbytné minimum a umožňuje týmům projektu a aplikace potřebné stupeň ovládacího prvku. Obvykle to znamená, že tento centrální IT vlastníkem předplatného a zpracovává základní IT funkcemi, jako je například konfigurace sítí. Sada **vlastníky** předplatného musí být malé. Tyto vlastníky můžete nominujte další vlastníky, když je potřeba, nebo použít zásady na úrovni předplatného, například "žádná veřejná IP adresa".

Může existovat podmnožinu uživatelů, které vyžadují přístup v rámci předplatného, jako je například podpora úrovně 1 nebo 2. V takovém případě vám doporučujeme, abyste udělili těmto uživatelům **Přispěvatel** přístup tak, aby nelze spravovat prostředky, ale poskytnout uživatelský přístup nebo úpravě zásad.

DevTest Labs prostředků musí být vlastněn vlastníky, kteří jsou blízko aplikace project/team. Je to proto, porozumět jejich požadavkům pro počítače a požadovaný software. Ve většině organizací vlastníka tohoto prostředku DevTest Labs je obvykle vedoucí projektu nebo vývoje. Tohoto vlastníka mohou spravovat uživatele a zásad v testovacím prostředí a můžete spravovat všechny virtuální počítače v prostředí DevTest Labs.

Členové týmu projektu/aplikace měla být přidána do **DevTest Labs uživatelé** role. Tito uživatelé můžou vytvářet virtuální počítače (v řádku pomocí testovacího prostředí a zásad na úrovni předplatného). Mohou také spravovat vlastní virtuální počítače. Jejich nemůže spravovat virtuální počítače, které patří jiným uživatelům.

Další informace najdete v tématu [Základní kostra Azure enterprise – zásady správného řízení dokumentaci předplatná](/azure/architecture/cloud-adoption/appendix/azure-scaffold).


### <a name="how-do-i-create-a-role-to-allow-users-to-do-a-specific-task"></a>Jak vytvořit role umožňuje uživatelům provádět určitý úkol?
Komplexní článek o tom, jak vytvořit vlastní role a přiřadit oprávnění k roli najdete v části [udělení uživatelských oprávnění na určitém laboratorním zásady](devtest-lab-grant-user-permissions-to-specific-lab-policies.md). Tady je příklad skriptu, který vytvoří roli **Advanced uživatel služby DevTest Labs**, který má oprávnění ke spuštění a zastavení všech virtuálních počítačů v testovacím prostředí:


```powershell
$policyRoleDef = Get-AzRoleDefinition "DevTest Labs User"
$policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*')
$policyRoleDef.Id = $null
$policyRoleDef.Name = "DevTest Labs Advanced User"
$policyRoleDef.IsCustom = $true
$policyRoleDef.AssignableScopes.Clear()
$policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>")
$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action")
$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action")
$policyRoleDef = New-AzRoleDefinition -Role $policyRoleDef  
```

### <a name="how-can-an-organization-ensure-corporate-security-policies-are-in-place"></a>Jak můžou organizace Ujistěte se, že firemním zásadám zabezpečení jsou na místě?
Organizace mohou dosáhnout ji provedením následujících akcí:

- Vývoj a publikování zásad zabezpečení. Zásady zabývá se výhodami pravidla podmínky použití spojené s na pomocí softwaru, cloudovým prostředím. Také definuje, co jasně porušuje zásady.
- Vyvíjejte vlastní image, vlastních artefaktů a proces nasazení, která umožňuje orchestraci v rámci sféry zabezpečení, která je definována s active directory. Tento přístup vynucuje hranic firmy a nastaví společnou sadu ovládacích prvků prostředí. Tyto ovládací prvky pro prostředí Vývojář můžete zvážit při vyvíjejí a postupujte podle zabezpečené vývojového cyklu jako součást svých celkový proces. Cílem je také poskytuje prostředí, který není příliš omezující této může bránit vývoje, ale přiměřené sadu ovládacích prvků. Zásady skupiny na organizační jednotku (OU), který obsahuje testovací prostředí virtuálních počítačů může být podmnožinou zásady celkový skupiny, které se nacházejí v produkčním prostředí. Alternativně je možné další sadu chcete všechny zjištěné riziko omezit správně.

### <a name="how-can-an-organization-ensure-data-integrity-to-ensure-that-remoting-developers-cant-remove-code-or-introduce-malware-or-unapproved-software"></a>Jak můžou organizace zajištění integrity dat k zajištění, že vývojáři vzdálené komunikace nelze odebrat kód nebo zavést malware nebo neschválených software?
Existuje několik úrovní kontroly ke zmírnění hrozeb od externí konzultanty, smluvní pracovníci ani interní zaměstnance, kteří jsou vzdálené komunikace v ke spolupráci ve službě DevTest Labs.

Jak bylo uvedeno dříve, první krok musí mít zásady podmínky použití zpracovávány a definice, která bude jasně uvádět důsledky, když někdo porušuje zásady.

První vrstva ovládacích prvků pro vzdálený přístup, je použít zásady vzdáleného přístupu prostřednictvím připojení VPN, který není připojený přímo k testovacím prostředí.

Druhá vrstva ovládacích prvků, je použít sadu objektů zásad skupiny, které brání kopírování a vkládání přes vzdálenou plochu. Nepovolí odchozí z prostředí, jako je například FTP a služby protokolu RDP z prostředí může implementovat zásady sítě. Uživatelsky definované směrování může vynutit veškerý síťový provoz Azure zpět do místního, ale směrování nejde účet pro všechny adresy URL, které může povolit nahrávání dat, není-li řídit prostřednictvím proxy serveru k prohledání obsahu a relací. Veřejné IP adresy může být v rámci virtuální sítě podporuje DevTest Labs k přemostění externího síťového prostředku, aby s omezeným přístupem.

Nakonec stejný typ omezení musí být nastavení použilo pro organizace, které by účet pro všechny možné metody vyměnitelného média nebo externí adresy URL, které by mohl přijmout příspěvek obsahu. Poraďte se s profesionální ke kontrole a implementovat zásadu zabezpečení zabezpečení. Další doporučení, najdete v tématu [Microsoft Security Kybernetických](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs).

## <a name="lab-configuration"></a>Konfigurace testovacího prostředí

### <a name="how-do-i-create-a-lab-from-a-resource-manager-template"></a>Jak vytvořit testovací prostředí ze šablony Resource Manageru?
Nabízíme [úložišti GitHub šablon Azure Resource Manageru prostředí](https://azure.microsoft.com/resources/templates/101-dtl-create-lab) , kterou můžete nasadit jako – nebo ho upravit k vytváření vlastních šablon pro vaše testovací prostředí. Každá šablona má odkaz na prostředí nasadit, protože je ve svém vlastním předplatném Azure. Nebo můžete přizpůsobit šablonu a [nasazení pomocí Powershellu nebo rozhraní příkazového řádku Azure](../azure-resource-manager/resource-group-template-deploy.md).


### <a name="can-i-have-all-virtual-machines-to-be-created-in-a-common-resource-group-instead-having-each-machine-in-its-own-resource-group"></a>Může mít všechny virtuální počítače vytvořené v běžných skupinu prostředků, namísto nutnosti každý počítač ve vlastní skupině prostředků? 
Ano, jako vlastník testovacího prostředí, můžete buď nechat lab zpracování přidělení skupiny prostředků pro vás nebo všechny virtuální počítače vytvořené ve skupině prostředků běžné, že zadáte. 

Scénář skupiny pro samostatný prostředek:
-   DevTest Labs vytvoří novou skupinu prostředků pro každý veřejného a privátního IP virtuálního počítače, které můžete aktivovat
-   DevTest Labs vytvoří skupinu prostředků pro sdílené počítače IP, které patří do stejné velikosti.

Běžný scénář skupiny prostředků:
-   Všechny virtuální počítače jsou spuštěné v běžných skupinu prostředků, kterou zadáte. Další informace [přidělení skupiny prostředků pro testovací prostředí](https://aka.ms/RGControl). 

### <a name="how-do-i-maintain-a-naming-convention-across-my-devtest-labs-environment"></a>Jak spravovat zásady vytváření názvů Moje prostředí DevTest Labs?
Můžete rozšířit stávající konvence pojmenování enterprise na provoz Azure a zajištění konzistentní napříč prostředí DevTest Labs. Při nasazování DevTest Labs, doporučujeme, abyste měli určité výchozí zásady. Tyto zásady centrální skriptů a šablon JSON vynucovat konzistenci nasazujete. Pojmenování zásady je možné implementovat pomocí Azure zásady na úrovni předplatného. Ukázky JSON pro Azure Policy najdete v tématu [ukázek Azure Policy](../governance/policy/samples/index.md).

### <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>Kolik testovacích prostředí můžete vytvořit pod stejné předplatné?
Není k dispozici konkrétní limitu počtu testovacích prostředí, které je možné vytvořit jedno předplatné. Objem prostředků používá jedno předplatné je však omezená. Informace o [omezení a kvót pro předplatná Azure](../azure-subscription-service-limits.md) a [tom, jak tyto limity zvýšit](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).


### <a name="how-many-vms-can-i-create-per-lab"></a>Kolik virtuálních počítačů můžete vytvořit počet v testovacím prostředí
Neexistuje žádná konkrétní omezení počtu virtuálních počítačů, které je možné vytvořit počet v testovacím prostředí. Prostředky (počet jader virtuálního počítače, veřejné IP adresy a tak dále), které se používají jsou však omezená na jedno předplatné. Informace o [omezení a kvót pro předplatná Azure](../azure-subscription-service-limits.md) a [tom, jak tyto limity zvýšit](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

### <a name="how-do-i-determine-the-ratio-of-users-per-lab-and-the-overall-number-of-labs-that-are-needed-across-an-organization"></a>Jak zjistím, poměr uživatelé testovacího prostředí a celkového počtu testovacích prostředí, které jsou potřeba v rámci celé organizace?
Doporučujeme vám, že jsou přidruženy stejném testovacího prostředí obchodním jednotkám a vývojářské skupiny, které jsou přidruženy stejném projektu vývoje. Umožňuje stejné typy zásad, obrázky a vypnutí zásad u obou skupin.

Můžete také zvažte geografické hranice. Vývojáři v USA – sever – východ (USA) může například použít testovací prostředí zřízené ve východní USA 2. A mohou vývojáři v Dallasu, Texas a Liberci přesměrovány na prostředek v USA střed – jih. Při spolupráci s externí třetích stran může být přiřazen do testovacího prostředí, která není používána umožňuje interním vývojářům.

Můžete také použít testovací prostředí pro konkrétní projekt v rámci projektů Azure DevOps. Pak můžete použít zabezpečení prostřednictvím zadané skupiny Azure Active Directory, který umožňuje přístup k oběma sadu prostředků. Virtuální síť přiřazeny k testovacím prostředí může být jiné hranice pro konsolidaci uživatelů.

### <a name="how-can-we-prevent-the-deletion-of-resources-within-a-lab"></a>Jak jsme zabránili odstranění prostředků v rámci testovacího prostředí?
Doporučujeme nastavit příslušná oprávnění na úrovni prostředí tak, aby jenom Autorizovaní uživatelé můžete odstranit prostředky nebo změnit zásady testovacího prostředí. Vývojáři by měl být umístěn v rámci **DevTest Labs uživatelé** skupiny. Vedoucí vývojář nebo zájemce infrastruktury by měla být **DevTest Labs vlastníka**. Doporučujeme, že abyste měli jenom dva vlastníků testovacího prostředí. Tato zásada rozšiřuje na úložiště kódu, aby se zabránilo poškození. Uživatelé testovacího prostředí mají práva k používání prostředků, ale nelze aktualizovat zásady testovacího prostředí. Přečtěte si článek, který obsahuje seznam rolí a oprávnění, která má každý předdefinované skupiny v rámci testovacího prostředí: [Přidat vlastníky a uživatele ve službě Azure DevTest Labs](devtest-lab-add-devtest-user.md).

### <a name="how-do-i-share-a-direct-link-to-my-lab"></a>Jak se dá sdílet přímý odkaz na Moje testovací prostředí?

1. V [webu Azure portal](https://portal.azure.com), přejděte do testovacího prostředí.
2. Kopírovat **adresu URL testovacího prostředí** z prohlížeče a potom ho sdílet s uživateli vašeho testovacího prostředí.

> [!NOTE]
> Pokud uživatele testovacího prostředí je to externí uživatel, kdo má účet Microsoft, ale který není členem instance Active Directory vaší organizace, uživatel může se zobrazit chybová zpráva při pokusu o přístup k sdílený odkaz. Pokud externí uživateli se zobrazí chybová zpráva, požádejte uživatele, aby nejdřív vyberte jeho jméno v pravém horním rohu webu Azure portal. Pak v sekci adresáře v nabídce, může uživatel vybrat adresáře, kde testovacím prostředí existuje.

## <a name="virtual-machines"></a>Virtuální počítače

### <a name="why-cant-i-see-vms-on-the-virtual-machines-page-that-i-see-in-devtest-labs"></a>Proč se nezobrazují virtuální počítače na stránce virtuální počítače, které se zobrazuje ve službě DevTest Labs?
Při vytváření virtuálního počítače ve službě DevTest Labs, že zadané oprávnění pro přístup k tomuto virtuálnímu počítači. Můžete zobrazit na stránce labs a na virtuálním počítači **virtuálních počítačů** stránky. Uživatelé s **DevTest Labs vlastníka** role může zobrazit všechny virtuální počítače, které byly vytvořeny v testovacím prostředí cvičení **všechny virtuální počítače** stránky. Nicméně uživatelé, kteří mají **uživatel služby DevTest Labs** roli nejsou automaticky udělit oprávnění ke čtení na prostředky virtuálních počítačů, které jste vytvořili jiným uživatelům. Ano, tyto virtuální počítače nejsou zobrazeny na **virtuálních počítačů** stránky.


### <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>Jak můžu vytvořit víc virtuálních počítačů ze stejné šablony najednou?
Máte dvě možnosti, souběžně vytvoření více virtuálních počítačů ze stejné šablony:

- Můžete použít [rozšíření Azure DevOps úkolů](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
- Je možné [generovat šablony Resource Manageru](devtest-lab-add-vm.md#save-azure-resource-manager-template) při vytváření virtuálního počítače, a [nasazení šablony Resource Manageru z prostředí Windows PowerShell](../azure-resource-manager/resource-group-template-deploy.md).
- Můžete také zadat více než jednu instanci počítače má být vytvořen při vytváření virtuálního počítače. Další informace o vytvoření více instancí virtuálních počítačů najdete v dokumentu na [vytváří se testovací virtuální počítač](devtest-lab-add-vm.md).

### <a name="how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab"></a>Jak můžu přesunout svoje existující virtuální počítače Azure do Moje testovací prostředí DevTest Labs?
Kopírování existujících virtuálních počítačů k DevTest Labs:

1.  Kopírování souboru virtuálního pevného disku existujícího virtuálního počítače pomocí [skript prostředí Windows PowerShell](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/Scripts/CopyVirtualMachines/CopyAzVHDFromVMToLab.ps1).
2.  Vytvořte [vlastní image](devtest-lab-create-template.md) uvnitř testovacího prostředí DevTest Labs.
3.  Vytvoření virtuálního počítače v testovacím prostředí z vlastní image.

### <a name="can-i-attach-multiple-disks-to-my-vms"></a>Můžete připojit několik disků do virtuálních počítačů?

Ano, můžete připojit několik disků k vašim virtuálním počítačům.

### <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>Pokud chcete použít bitovou kopii operačního systému Windows pro testování, je nutné koupit předplatné MSDN?
Použití imagí klienta operačního systému Windows (Windows 7 nebo novější) pro vývoj a testování v Azure, proveďte jednu z následujících kroků:

- [Koupit předplatné MSDN](https://www.visualstudio.com/products/how-to-buy-vs).
- Pokud máte smlouvu Enterprise Agreement, vytvořte předplatné Azure s [nabídka Enterprise pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0148p).

Další informace o kreditech Azure pro každou nabídku MSDN najdete v tématu [Azure měsíční kredit pro předplatitele sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).


### <a name="how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>Automatizace procesu odstranění všech virtuálních počítačů v Moje testovací prostředí
Jako vlastník testovacího prostředí můžete odstranit virtuální počítače ve svém testovacím prostředí na webu Azure Portal. Také můžete odstranit všechny virtuální počítače ve vaší laboratoři pomocí skriptu prostředí PowerShell. V následujícím příkladu v části **hodnoty změnit** komentář, upravte hodnoty parametrů. ID předplatného, labResourceGroup a labName hodnoty můžete načíst z podokna testovacího prostředí na webu Azure Portal.

```powershell
# Delete all the VMs in a lab.

# Values to change:
$subscriptionId = "<Enter Azure subscription ID here>"
$labResourceGroup = "<Enter lab's resource group here>"
$labName = "<Enter lab name here>"

# Sign in to your Azure account.
Connect-AzAccount

# Select the Azure subscription that has the lab. This step is optional
# if you have only one subscription.
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab that has the VMs that you want to delete.
$lab = Get-AzResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Get the VMs from that lab.
$labVMs = Get-AzResource | Where-Object {
          $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
          $_.Name -like "$($lab.Name)/*"}

# Delete the VMs.
foreach($labVM in $labVMs)
{
    Remove-AzResource -ResourceId $labVM.ResourceId -Force
}
```

## <a name="environments"></a>Prostředí 

### <a name="how-can-i-use-resource-manager-templates-in-my-devtest-labs-environment"></a>Jak lze pomocí šablon Resource Manageru v mého prostředí DevTest Labs?
Nasadit své šablony Resource Manageru prostředí v DevTest Labs pomocí kroků uvedených v [funkce prostředí v DevTest Labs](devtest-lab-test-env.md) článku. V podstatě, zkontrolujte své šablony Resource Manageru do úložiště Git (úložiště Azure nebo GitHub) a přidejte [soukromé úložiště k šablonám](devtest-lab-test-env.md) testovací prostředí. Tento scénář nemusejí být užitečné, pokud používáte DevTest Labs hostovat vývojové počítače, ale může být užitečné, pokud vytváříte pracovní prostředí, které je zástupce produkčního prostředí.

Je také vhodné poznamenat, že počet virtuálních počítačů podle testovacího prostředí nebo možnost uživatelů pouze omezí počet počítačů, které jsou vytvořené nativně v samotné testovací prostředí a ne všechny prostředí (šablon Resource Manageru).

## <a name="custom-images"></a>Vlastní image

### <a name="how-can-i-set-up-an-easily-repeatable-process-to-bring-my-custom-organizational-images-into-a-devtest-labs-environment"></a>Jak nastavím snadno opakovatelné procesu uvést Moje vlastní organizační Image v prostředí DevTest Labs?
Najdete v tomto [videa na objekt pro vytváření bitové kopie vzoru](https://sec.ch9.ms/ch9/8e8a/9ea0b8d4-b803-4f23-bca4-4808d9368e8a/dtlimagefactory_mid.mp4). Tento scénář je pokročilý scénář a skripty k dispozici jsou pouze ukázkové skripty. Pokud jsou vyžadovány žádné změny, musíte pro správu a údržbu skripty používané ve vašem prostředí.

Podrobné informace o vytvoření objektu pro vytváření image, najdete v části [vytvořte objekt pro vytváření vlastní image ve službě Azure DevTest Labs](image-factory-create.md). 

### <a name="what-is-the-difference-between-a-custom-image-and-a-formula"></a>Jaký je rozdíl mezi vlastní image a vzorce?
Vlastní image je spravované image. Vzorec je bitovou kopii, která můžete nakonfigurovat další nastavení a poté uložit a reprodukovat. Vlastní image může být vhodnější, pokud chcete rychle vytvořit několik prostředí s využitím stejné základní, neměnné image. Vzorec může být lepší, pokud chcete reprodukovat konfigurace virtuálního počítače nejnovější pomůcky, v rámci virtuální sítě nebo podsítě, nebo jako virtuální počítač určité velikosti. Podrobnější vysvětlení najdete v tématu [porovnání vlastních imagí a vzorců ve službě DevTest Labs](devtest-lab-comparing-vm-base-image-types.md).

### <a name="when-should-i-use-a-formula-vs-custom-image"></a>Kdy použít vzorec vs. vlastní image?
Obvykle rozhodujícím faktorem v tomto scénáři je nákladů a znovu použít. Pokud máte scénáře, kde mnoho uživatelů/testovacích prostředí vyžadují image s velkým množstvím softwaru nad základní image, může snížit náklady tím, že vytvoříte vlastní image. To znamená, že po vytvoření image. Snižuje čas nastavení virtuálního počítače a náklady, protože virtuální počítač spuštěn, když dojde k nastavení.

Dodatečný faktor si je ale frekvence změny vašeho softwarového balíčku. Pokud spustíte denní sestavení a vyžadovat, aby software na virtuálních počítačích uživatelů, zvažte použití vzorec namísto vlastní image.

Podrobnější vysvětlení najdete v tématu [porovnání vlastních imagí a vzorců](devtest-lab-comparing-vm-base-image-types.md) ve službě DevTest Labs.

### <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>Automatizace procesu nahrávání souborů virtuálního pevného disku k vytváření vlastních imagí

Automatizace nahrávání souborů virtuálního pevného disku k vytváření vlastních imagí, máte dvě možnosti:

- Použití [AzCopy](../storage/common/storage-use-azcopy-v10.md) zkopírovat nebo nahrajte soubory virtuálního pevného disku do účtu úložiště, který je spojen s testovacím prostředí.
- Použití [Průzkumníka služby Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md). Průzkumník služby Storage je samostatná aplikace, která běží na Windows, OS X a Linux.

Chcete-li najít cílový účet úložiště, který je spojen s testovacího prostředí:

1.  Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2.  V nabídce vlevo vyberte **skupiny prostředků**.
3.  Vyhledejte a vyberte skupinu prostředků, který je spojen s testovacího prostředí.
4.  V části **přehled**, vyberte jeden z účtů úložiště.
5.  Vyberte **Objekty blob**.
6.  Vyhledejte nahrávání v seznamu. Pokud žádný neexistuje, vraťte se ke kroku 4 a zkuste použít jiný účet úložiště.
7.  Použití **URL** jako cíl v příkazu AzCopy.

Kdy použít image Azure Marketplace vs. vlastní organizační vlastní image?

### <a name="when-should-i-use-an-azure-marketplace-image-vs-my-own-custom-organizational-image"></a>Kdy použít image Azure Marketplace vs. vlastní organizační vlastní image?
Pokud nemáte konkrétní otázky nebo požadavků organizace by měla sloužit ve výchozím nastavení Azure Marketplace. Mezi běžné příklady patří;

- Nastavení složitý software, který vyžaduje, aby aplikace byly zahrnuty základní image.
- Instalace a nastavení aplikace může trvat mnoho hodin, které nejsou efektivní využití výpočetního času má být přidán do Image Azure Marketplace.
- Vývojářům a testerům rychle vyžadují přístup k virtuálnímu počítači a chcete minimalizovat čas instalace nového virtuálního počítače.
- Dodržování předpisů nebo právní podmínky (třeba zásady zabezpečení), které musí být splněné všechny počítače.
- Použití vlastní Image by neměly být zahrnuté lehce. Přinášejí další složitost, protože nyní musíte spravovat soubory virtuálního pevného disku pro tyto podpůrné základní Image. Také je potřeba pravidelně oprava tyto základní Image s aktualizacemi softwaru. Tyto aktualizace zahrnují nové aktualizace operačního systému (OS) a všechny aktualizace nebo změny konfigurace, které jsou potřebné pro balíček softwaru, samotného.

## <a name="artifacts"></a>Artefakty

### <a name="what-are-artifacts"></a>Co se o artefakty?
Artefakty jsou přizpůsobitelné prvky, které vám umožní nasazovat nejnovější části nebo nasazení nástroje pro vývojáře k virtuálnímu počítači. Při vytváření virtuálního počítače, připojte k vašemu virtuálnímu počítači artefakty. Po zřízení virtuálního počítače artefakty nasazení a konfigurace virtuálního počítače. Jsou k dispozici v různých artefaktů existující naše [veřejného úložiště GitHub](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts). Můžete také [vytváření vlastních artefaktů](devtest-lab-artifact-author.md).

### <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>Při vytváření virtuálního počítače se nezdařilo tento artefakt. Jak ji můžu vyřešit?
Zjistěte, jak získat protokoly neúspěšných artefaktu, najdete v článku [Diagnostika selhání artefaktů ve službě DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md).

### <a name="when-should-an-organization-use-a-public-artifact-repository-vs-private-artifact-repository-in-devtest-labs"></a>Kdy organizace použít veřejné úložiště artefaktů a úložiště privátního artefaktů ve službě DevTest Labs?
[Veřejné úložiště artefaktů](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) obsahuje počáteční sadu softwarové balíčky, které se běžně používají. Pomáhá s rychlým nasazením bez nutnosti investovat čas pro reprodukci běžné vývojářské nástroje a moduly. Je možné nasadit vlastní soukromé úložiště. Můžete použít veřejné a soukromé úložiště paralelně. Můžete také zakázat veřejného úložiště. Kritéria, která je nasadit do privátního úložiště by měl vycházet následující dotazy a důležité informace:

- Má organizace potřeba, aby podnikové licencovaného softwaru jako součást své nabídky DevTest Labs? Pokud odpovíte Ano, pak by měly být vytvořeny v privátním úložišti.
- Organizace vývoj vlastní software, který obsahuje konkrétní operací, který je vyžadován jako součást celkového během procesu zřizování? Pokud odpovíte Ano, by měly být nasazeny v privátním úložišti.
- Pokud zásady zásad správného řízení organizace vyžaduje izolace a externí úložiště nejsou pod správou konfigurace s přímým přístupem v organizaci, by měly být nasazeny úložiště privátního artefaktů. Jako součást tohoto procesu je zkopírovat počáteční kopie veřejného úložiště a integrovat se na soukromé úložiště. Potom veřejného úložiště je možné zakázat tak, aby nikdo v rámci organizace můžete už přístup. Tento přístup všichni uživatelé v organizaci měli pouze jediné úložiště, které má schválený organizací vynutí a minimalizovat odchylky od konfigurace.


### <a name="should-an-organization-plan-for-a-single-repository-or-allow-multiple-repositories"></a>Organizace by měly plánování jednoho úložiště nebo povolit více úložišť?
Jako součást celkového zásad správného řízení vaší organizace a strategie správy konfigurace doporučujeme použít centralizované úložiště. Při použití více úložišť mohou být sila nespravované software čase. S centrálním úložišti můžete využívat více týmů artefakty z tohoto úložiště pro své projekty. Vynucuje normalizace, zabezpečení, snadnou správu a odstraňuje duplicity úsilí. Jako součást centralizovaného tyto akce jsou doporučené postupy pro dlouhodobé správy a udržitelnosti:

- Úložiště Azure přidružení stejného tenanta služby Azure Active Directory, která je pro ověřování a autorizace pomocí předplatného Azure.
- Vytvořte skupinu s názvem `All DevTest Labs Developers` ve službě Azure Active Directory, který je centrálně spravovat. Kterýkoli vývojář, který přispívá k rozvoji artefaktů by měl umístit do této skupiny.
- Stejnou skupinu Azure Active Directory je možné poskytnout přístup k úložišti Azure úložišť a testovací prostředí.
- V úložišti Azure, větvení nebo větvení by měla sloužit k úložišti samostatné v vývoj z primární produkční úložiště. Obsah je pouze přidán do hlavní větve s žádostí o přijetí změn po přezkoumání kódu správné. Jakmile kód revidující změnu schválí, hlavní vývojář, který zodpovídá za údržbu v hlavní větvi, sloučí aktualizovaný kód.

## <a name="cicd-integration"></a>Integrace CI/CD

### <a name="does-devtest-labs-integrate-with-my-cicd-toolchain"></a>Integrovat DevTest Labs se Moje sadou nástrojů CI/CD?
Pokud používáte Azure DevOps, můžete použít [rozšíření DevTest Labs úloh](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) automatizace kanálu pro vydávání verzí ve službě DevTest Labs. Úlohy, které vám pomůžou s touto příponou patří:

- Vytvoření a nasazení virtuálního počítače automaticky. Také můžete nakonfigurovat virtuální počítač s nejnovější sestavení pomocí úloh Azure File Copy nebo prostředí PowerShell Azure DevOps služby.
- Automaticky zachytávat stav virtuálního počítače po testování pro reprodukci chyby na stejný virtuální počítač pro další zkoumání.
- Pokud už je nepotřebujete, odstraňte virtuální počítač na konci procesu vydávání verzí.

Následující příspěvky nabídka pokyny a informace o použití rozšíření Azure DevOps služby:

- [DevTest Labs a rozšíření Azure DevOps](integrate-environments-devops-pipeline.md)
- [Nasazení nového virtuálního počítače v existující testovací prostředí DevTest Labs ze služeb Azure DevOps](https://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
- [Pomocí nástroje Azure DevOps služby release management pro průběžné nasazení na DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)

Pro další kontinuální integrace (CI) / průběžné doručování (CD) sady nástrojů, které můžete dosáhnout stejné scénáře podle nasazení [šablon Azure Resource Manageru](https://azure.microsoft.com/resources/templates/) pomocí [rutin prostředí Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) a [Sady .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/). Můžete také použít [rozhraní REST API pro DevTest Labs](https://aka.ms/dtlrestapis) integrovat sady nástrojů.

## <a name="networking"></a>Sítě

### <a name="when-should-i-create-a-new-virtual-network-for-my-devtest-labs-environment-vs-using-an-existing-virtual-network"></a>Kdy je vhodné vytvořit novou virtuální síť pro Moje prostředí DevTest Labs oproti použití existující virtuální sítě?
Pokud vaše virtuální počítače potřebují komunikovat s stávající infrastruktury, zvažte použití existující virtuální sítě v prostředí DevTest Labs. Pokud používáte ExpressRoute, můžete chtít minimalizovat množství virtuálních sítí / podsítí tak, aby váš adresní prostor IP adres, který získá přiřazen pro použití v předplatných nejsou fragment. 

Zvažte použití vzoru partnerského vztahu virtuálních sítí tady ([centra s paprsky modelu](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)) příliš. Tento přístup umožňuje komunikaci virtuálních sítí/podsítí napříč předplatnými. Každé prostředí DevTest Labs, jinak může mít svůj vlastní virtuální sítě. 

Existují [omezení](../azure-subscription-service-limits.md) na počet virtuálních sítí na jedno předplatné. Výchozí doba je 50, i když tento limit lze zvýšit na 100.

### <a name="when-should-i-use-a-shared-ip-vs-public-ip-vs-private-ip"></a>Kdy použít se sdílené IP Adresou a veřejnou IP adresu a privátní IP adresa?
 
Pokud používáte síť VPN site-to-site nebo Expressroute, zvažte použití privátních IP adres tak, že vaše počítače jsou přístupné prostřednictvím vaší interní síti a nejsou přístupné prostřednictvím veřejného Internetu.

> [!NOTE]
> Vlastníků testovacího prostředí můžete změnit tyto zásady podsítě k zajištění, že nikdo omylem vytvoří veřejné IP adresy pro své virtuální počítače. Vlastník předplatného byste vytvořit zásady předplatného brání vytváření veřejné IP adresy.

Při použití sdílené veřejné IP adresy, virtuální počítače v testovacím prostředí, sdílet veřejnou IP adresu. Tento přístup může být užitečné, když budete chtít zabránit porušení omezení pro veřejné IP adresy pro daný odběr.

### <a name="how-do-i-ensure-that-development-and-test-virtual-machines-are-unable-to-reach-the-public-internet-are-there-any-recommended-patterns-to-set-up-network-configuration"></a>Jak zajistit, že vývojové a testovací virtuální počítače se nám kontaktovat veřejného Internetu? Existují všechny doporučené způsoby nastavení konfigurace sítě?

Ano. Existují dva aspekty ke zvážení – příchozí a odchozí provoz.

- **Příchozí provoz** – Pokud virtuální počítač nemá veřejnou IP adresu, pak nelze získat přístup k Internetu. Běžným přístupem je zajistit, že je nastavena zásady na úrovni předplatného, tak, že žádný uživatel můžete vytvořit veřejnou IP adresu.
- **Odchozí provoz** – Pokud chcete zabránit virtuální počítače přímo přístup k veřejným Internetem a vynucení provozu prostřednictvím podnikové brány firewall, pak může směrovat provoz místních přes expressroute nebo VPN, pomocí vynucené směrování.

> [!NOTE]
> Pokud máte proxy server, která blokuje provoz bez nastavení proxy serveru, nezapomeňte přidat výjimky pro účet úložiště artefaktu testovacího prostředí.

Můžete také použít skupiny zabezpečení sítě pro virtuální počítače nebo podsítí. Tento krok přidává další vrstvu ochrany pro povolení / blokování provozu.

## <a name="troubleshooting"></a>Řešení potíží

### <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>Není Proč moje stávající virtuální sítě ukládání správně?
Jednou z možností je, že váš název virtuální sítě obsahuje období. Pokud ano, zkuste odebrat období nebo jejich náhradu za pomlčky. Poté se pokuste znovu uložit virtuální síť.

### <a name="why-do-i-get-a-parent-resource-not-found-error-when-i-provision-a-vm-from-powershell"></a>Proč se při zřizování virtuálního počítače z prostředí PowerShell získat chybu "Nadřazený prostředek nebyl nalezen"?
Pokud jeden prostředek je nadřazený objekt na jiný prostředek, musí existovat nadřazený prostředek vytvořit podřízený prostředek. Pokud se nadřazený prostředek neexistuje, zobrazí **ParentResourceNotFound** zprávy. Pokud nezadáte závislost na nadřazený prostředek, podřízený prostředek může být nasazena nadřazeného objektu.

Virtuální počítače jsou podřízené prostředky v rámci testovacího prostředí ve skupině prostředků. Při použití šablon Resource Manageru k nasazení virtuálních počítačů pomocí Powershellu, název skupiny prostředků, které jsou k dispozici v skriptu prostředí PowerShell by měl být název skupiny prostředků testovacího prostředí. Další informace najdete v tématu [řešení potíží s běžnými chybami nasazení Azure](../azure-resource-manager/resource-manager-common-deployment-errors.md).

### <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>Kde najdu Další informace o chybě, pokud se nezdaří nasazení virtuálního počítače?
Chyby nasazení virtuálního počítače zachyceny v protokolech aktivit. Testovací prostředí můžete najít protokoly aktivit virtuálního počítače v části **protokoly auditu** nebo **Diagnostika virtuálního počítače** v nabídce prostředků na stránce testovacího prostředí virtuálních počítačů (se zobrazí po výběru virtuálního počítače z seznam virtuálních počítačů).

V některých případech nasazení dojde k chybě před zahájením nasazení virtuálního počítače. Příkladem je při překročení limitu předplatného pro prostředek, který byl vytvořen s virtuálním Počítačem. Podrobnosti o chybě v tomto případě jsou zachyceny v protokolech aktivit úrovni testovacího prostředí. Protokoly aktivit jsou umístěné v dolní části **konfigurace a zásad** nastavení. Další informace o používání aktivit protokoly v Azure, najdete v článku [zobrazení protokolů aktivit pro auditování akcí u prostředků](../azure-resource-manager/resource-group-audit.md).




