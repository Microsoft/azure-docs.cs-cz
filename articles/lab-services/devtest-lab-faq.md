---
title: Nejčastější dotazy k laboratořím Azure DevTest Labs | Dokumenty společnosti Microsoft
description: Tento článek obsahuje odpovědi na některé často kladené otázky (FAQ) o Azure DevTest Labs.
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
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: de99e9b1e4adceaf08beaf8ad3b5ea114b31a586
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270781"
---
# <a name="azure-devtest-labs-faq"></a>Nejčastější dotazy k Azure DevTest Labs
Získejte odpovědi na některé nejčastější dotazy týkající se azure devtest labs.

## <a name="blog-post"></a>Blogu
Náš blog DevTest Labs Team byl vyřazen od 20 Března 2019. 

### <a name="where-can-i-track-feature-updates-from-now-on"></a>Kde mohu od nynějška sledovat aktualizace funkcí?
Od této chvíle budeme zveřejňovat aktualizace funkcí a informativní blogové příspěvky na blogu Azure a aktualizacích Azure. Tyto blogové příspěvky budou také odkazovat na naši dokumentaci všude tam, kde je to požadováno.

Přihlaste se k odběru aktualizací [DevTest Labs Azure blog](https://azure.microsoft.com/blog/tag/azure-devtest-labs/) a [DevTest Labs Azure,](https://azure.microsoft.com/updates/?product=devtest-lab) abyste byli informováni o nových funkcích v DevTest Labs.

### <a name="what-happens-to-the-existing-blog-posts"></a>Co se stane se stávajícími příspěvky blogu?
V současné době pracujeme na migraci stávajících blogových příspěvků (s výjimkou aktualizací výpadku) do naší [dokumentace DevTest Labs](devtest-lab-overview.md). Když je blog MSDN zastaralá, bude přesměrován na přehled dokumentace pro DevTest Labs. Po přesměrování můžete vyhledat článek, který hledáte, v názvu Filtrovat podle. Ještě jsme nemigrovali všechny příspěvky, ale mělo by to být hotové do konce tohoto měsíce. 


### <a name="where-do-i-see-outage-updates"></a>Kde se zobrazují aktualizace výpadku?
Budeme vysílání výpadku aktualizace pomocí našeho Twitter rukojeť od nynějška. Sledujte nás na Twitteru a získejte nejnovější informace o výpadcích a známých chybách.

### <a name="twitter"></a>Twitter
Naše twitterová rukojeť:[@azlabservices](https://twitter.com/azlabservices)

## <a name="general"></a>Obecné
### <a name="what-if-my-question-isnt-answered-here"></a>Co když moje otázka není zodpovězena tady?
Pokud zde vaše otázka není uvedena, dejte nám vědět, abychom vám pomohli najít odpověď.

- Zveřejněte dotaz na konci tohoto faq.
- Chcete-li oslovit širší okruh uživatelů, pošlete dotaz na [fóru MSDN služby Azure DevTest Labs](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs). Zapojte se do týmu Azure DevTest Labs a dalších členů komunity.
- V případě žádostí o funkce odešlete své požadavky a nápady do [služby Azure DevTest Labs User Voice](https://feedback.azure.com/forums/320373-azure-devtest-labs).

### <a name="what-is-a-microsoft-account"></a>Co je účet Microsoft?
Účet Microsoft je účet, který používáte téměř ke všemu, co děláte se zařízeními a službami Microsoft. Jedná se o e-mailovou adresu a heslo, které používáte k přihlášení ke Skypu, Outlook.com, OneDrivu, Windows phone, Azure a Xbox Live. Jeden účet znamená, že vaše soubory, fotky, kontakty a nastavení vás mohou sledovat na libovolném zařízení.

> [!NOTE]
> Účet Microsoft se dříve nazýval Windows Live ID.

### <a name="why-should-i-use-azure-devtest-labs"></a>Proč bych měl používat Azure DevTest Labs?
Azure DevTest Labs můžou vašemu týmu ušetřit čas i peníze. Vývojáři mohou vytvářet vlastní prostředí pomocí několika různých základů. Mohou také použít artefakty k rychlému nasazení a konfiguraci aplikací. Pomocí vlastních irekcí a vzorců můžete virtuální počítače (VM) uložit jako šablony a snadno je reprodukovat v celém týmu. DevTest Labs také nabízí několik konfigurovatelných zásad, které mohou správci testovacího prostředí použít ke snížení množství odpadu a správě prostředí týmu. Tyto zásady zahrnují automatické vypnutí, prahovou hodnotu nákladů, maximální počet virtuálních počítače na uživatele a maximální velikost virtuálních počítače. Podrobnější vysvětlení devTest Labs najdete v [přehledu](devtest-lab-overview.md) nebo [úvodním videu](https://channel9.msdn.com/Blogs/Azure/what-is-azure-devtest-labs).

### <a name="what-does-worry-free-self-service-mean"></a>Co znamená "bezstarostná samoobsluha"?
Bezstarostná samoobsluha znamená, že vývojáři a testeři vytvářejí vlastní prostředí podle potřeby. Správci mají jistotu, že DevTest Labs může pomoci nastavit odpovídající přístup, minimalizovat odpad a kontrolovat náklady. Správci můžou určit, které velikosti virtuálních počítače jsou povolené, maximální počet virtuálních počítače a když se virtuální počítače schytnou a vypínají. DevTest Labs také usnadňuje sledování nákladů a nastavení výstrah, které vám pomohou zůstat vědomi toho, jak jsou využívány laboratorní prostředky.

### <a name="how-can-i-use-devtest-labs"></a>Jak mohu používat DevTest Labs?
DevTest Labs je užitečné kdykoli vyžadované dev nebo testovací prostředí a chcete reprodukovat rychle, nebo spravovat pomocí zásad úspory nákladů.
Tady jsou některé scénáře, pro které naši zákazníci používají DevTest Labs:

- Spravujte dev a testovací prostředí na jednom místě. Pomocí zásad můžete snížit náklady a vytvořit vlastní image pro sdílení sestavení v rámci týmu.
- Vytvořte aplikaci pomocí vlastních bitových kopií k uložení stavu disku v průběhu fází vývoje.
- Sledovat náklady ve vztahu k průběhu.
- Vytvořte prostředí pro hromadné testování pro testování zajištění kvality.
- Pomocí artefaktů a vzorců můžete snadno konfigurovat a reprodukovat aplikaci v různých prostředích.
- Distribuujte virtuální počítače pro hackathony (kolaborativní dev nebo testovací práce) a pak je snadno odpojit po ukončení události.

### <a name="how-am-i-billed-for-devtest-labs"></a>Jak se mi účtují DevTest Labs?
DevTest Labs je bezplatná služba. Vytváření testovacích prostředí a konfigurace zásad, šablon a artefaktů v devTest Labs je zdarma. Platíte jenom za prostředky Azure používané ve vašich testovacích prostředích, jako jsou virtuální počítače, účty úložiště a virtuální sítě. Další informace o nákladech na prostředky testovacího prostředí najdete v [tématu Azure DevTest Labs pricing](https://azure.microsoft.com/pricing/details/devtest-lab/).

## <a name="security"></a>Zabezpečení

### <a name="what-are-the-different-security-levels-in-devtest-labs"></a>Jaké jsou různé úrovně zabezpečení v DevTest Labs?
Přístup k zabezpečení je určen řízením přístupu na základě rolí (RBAC). Chcete-li zjistit, jak funguje přístup, pomáhá zjistit rozdíly mezi oprávnění, role a oboru, jak je definováno RBAC.

- **Oprávnění**: Oprávnění je definovaný přístup k určité akci. Oprávnění může být například přístup pro čtení pro všechny virtuální aplikace.
- **Role**: Role je sada oprávnění, která lze seskupit a přiřadit uživateli. Například uživatel s rolí vlastníka předplatného má přístup ke všem prostředkům v rámci předplatného.
- **Obor**: Obor je úroveň v rámci hierarchie prostředku Azure. Obor může být například skupina prostředků, jedno testovací prostředí nebo celé předplatné.

V rámci devTest Labs existují dva typy rolí, které definují uživatelská oprávnění:

- **Vlastník testovacího prostředí**: Vlastník testovacího prostředí má přístup ke všem prostředkům v testovacím prostředí. Vlastník testovacího prostředí můžete upravit zásady, číst a zapisovat do libovolného virtuálního počítače, změnit virtuální síť a tak dále.
- **Uživatel testovacího prostředí**: Uživatel testovacího prostředí může zobrazit všechny prostředky testovacího prostředí, jako jsou virtuální počítače, zásady a virtuální sítě. Uživatel testovacího prostředí však nemůže změnit zásady ani žádné virtuální uživatele, které byly vytvořeny jinými uživateli.

Můžete také vytvořit vlastní role v DevTest Labs. Informace o tom, jak vytvořit vlastní role v devtest labs, najdete v článku [Udělení uživatelských oprávnění pro konkrétní zásady testovacího prostředí](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Vzhledem k tomu, že obory jsou hierarchické, pokud má uživatel oprávnění v určitém oboru, je uživateli automaticky udělena tato oprávnění v každém oboru nižší úrovně v oboru. Například pokud je uživateli přiřazena role vlastníka předplatného, uživatel má přístup ke všem prostředkům v předplatném. Tyto prostředky zahrnují virtuální počítače, virtuální sítě a testovací prostředí. Vlastník předplatného automaticky zdědí roli vlastníka testovacího prostředí. Opak však není pravdou. Vlastník testovacího prostředí má přístup k testovacímu prostředí, což je nižší obor než úroveň předplatného. Takže vlastník testovacího prostředí nemůže zobrazit virtuální počítače, virtuální sítě nebo jiné prostředky, které jsou mimo testovací prostředí.

### <a name="how-do-i-define-role-based-access-control-for-my-devtest-labs-environments-to-ensure-that-it-can-govern-while-developerstest-can-do-their-work"></a>Jak lze definovat řízení přístupu na základě rolí pro prostředí DevTest Labs, abych zajistil, že IT oddělení může řídit, zatímco vývojáři/test můžou dělat svou práci?
Existuje široký vzorec, nicméně detail závisí na vaší organizaci.

Centrální IT by mělo vlastnit pouze to, co je nezbytné, a umožnit týmům projektu a aplikacím mít potřebnou úroveň kontroly. Obvykle to znamená, že centrální IT vlastní předplatné a zpracovává základní funkce IT, jako jsou konfigurace sítě. Sada **vlastníků** pro předplatné by měla být malá. Tito vlastníci mohou nominovat další vlastníky, když je to potřeba, nebo použít zásady na úrovni předplatného, například "Žádná veřejná IP adresa".

Může existovat podmnožina uživatelů, kteří vyžadují přístup přes předplatné, jako je například podpora Tier1 nebo Tier 2. V takovém případě doporučujeme poskytnout těmto uživatelům přístup **přispěvatele,** aby mohli spravovat prostředky, ale neposkytovat přístup uživatelů nebo upravovat zásady.

Prostředek DevTest Labs by měl být vlastněn vlastníky, kteří jsou blízko týmu projektu nebo aplikace. Je to proto, že chápou jejich požadavky na stroje a požadovaný software. Ve většině organizací je vlastníkem tohoto prostředku DevTest Labs obvykle vedoucí projektu nebo vývoje. Tento vlastník může spravovat uživatele a zásady v testovacím prostředí a spravovat všechny virtuální uživatele v prostředí DevTest Labs.

Členové týmu projektu/aplikace by měli být přidáni do role **Uživatelé devTest Labs.** Tito uživatelé mohou vytvářet virtuální počítače (v souladu s testovacím prostředím a zásadami na úrovni předplatného). Mohou také spravovat své vlastní virtuální počítače. Nemohou spravovat virtuální počítače, které patří jiným uživatelům.

Další informace najdete v [tématu Azure enterprise scaffold – prescriptive subscription governance documentation](/azure/architecture/cloud-adoption/appendix/azure-scaffold).


### <a name="how-do-i-create-a-role-to-allow-users-to-do-a-specific-task"></a>Jak vytvořím roli, která uživatelům umožní provést určitý úkol?
Úplný článek o vytváření vlastních rolí a přiřazení oprávnění k roli naleznete v tématu [Udělení uživatelských oprávnění konkrétním zásadám testovacího prostředí](devtest-lab-grant-user-permissions-to-specific-lab-policies.md). Tady je příklad skriptu, který vytvoří roli **DevTest Labs Advanced User**, která má oprávnění ke spuštění a zastavení všech virtuálních uživatelů v testovacím prostředí:


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

### <a name="how-can-an-organization-ensure-corporate-security-policies-are-in-place"></a>Jak může organizace zajistit, aby byly zavedeny podnikové zásady zabezpečení?
Organizace toho může dosáhnout následujícím způsobem:

- Vypracování a publikování komplexní bezpečnostní politiky. Zásady formulují pravidla přijatelného použití spojená s pomocí softwaru, cloudových prostředků. Definuje také to, co jasně porušuje zásady.
- Vytvořte vlastní bitovou kopii, vlastní artefakty a proces nasazení, který umožňuje orchestraci v rámci sféry zabezpečení, která je definována pomocí služby Active Directory. Tento přístup vynucuje hranice podniku a nastavuje společnou sadu ovládacích prvků prostředí. Tyto ovládací prvky proti prostředí, které může vývojář zvážit při vývoji a sledování zabezpečeného životního cyklu vývoje jako součást jejich celkového procesu. Cílem je také poskytnout prostředí, které není příliš omezující, které může bránit rozvoji, ale rozumný soubor ovládacích prvků. Zásady skupiny v organizační jednotce (OU), která obsahuje virtuální počítače testovacího prostředí, mohou být podmnožinou celkových zásad skupiny, které se nacházejí v produkčním prostředí. Alternativně mohou být další sadou, která řádně zmírní všechna zjištěná rizika.

### <a name="how-can-an-organization-ensure-data-integrity-to-ensure-that-remoting-developers-cant-remove-code-or-introduce-malware-or-unapproved-software"></a>Jak může organizace zajistit integritu dat, aby zajistila, že vývojáři vzdálené komunikace nebudou moci odebrat kód nebo zavést malware nebo neschválený software?
Existuje několik vrstev kontroly ke zmírnění hrozby od externích konzultantů, dodavatelů nebo zaměstnanců, kteří jsou vzdálené ke spolupráci v DevTest Labs.

Jak již bylo uvedeno dříve, první krok musí mít přijatelné použití politiky vypracovány a definovány, že jasně nastiňuje důsledky, když někdo poruší politiku.

První vrstva ovládacích prvků pro vzdálený přístup je použít zásady vzdáleného přístupu prostřednictvím připojení VPN, které není přímo připojeno k testovacímu prostředí.

Druhá vrstva ovládacích prvků je použít sadu objektů zásad skupiny, které brání kopírování a vkládání přes vzdálenou plochu. Síťová politika by mohla být implementována tak, aby neumožňovala odchozí msty z prostředí, jako jsou služby FTP a RDP, mimo prostředí. Uživatelem definované směrování může vynutit veškerý síťový provoz Azure zpět do místního prostředí, ale směrování nemůže odpovídat všem adresám URL, které by mohly povolit nahrávání dat, pokud nejsou řízeny prostřednictvím proxy serveru pro skenování obsahu a relací. Veřejné IP adresy by mohly být omezeny v rámci virtuální sítě podporující DevTest Labs neumožňuje přemostění externího síťového prostředku.

Nakonec je třeba použít stejný typ omezení v celé organizaci, což by odpovídalo všem možným metodám vyměnitelných médií nebo externích adres URL, které by mohly přijmout příspěvek obsahu. Poraďte se se svým bezpečnostním odborníkem a zkontrolujte a implementujte zásady zabezpečení. Další doporučení naleznete v tématu [Microsoft Cyber Security](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs).

## <a name="lab-configuration"></a>Konfigurace laboratoře

### <a name="how-do-i-create-a-lab-from-a-resource-manager-template"></a>Jak vytvořím testovací prostředí ze šablony Správce prostředků?
Nabízíme [úložiště GitHub šablon Azure Resource Manager v testovacím prostředí,](https://azure.microsoft.com/resources/templates/101-dtl-create-lab) které můžete nasadit tak, jak jsou, nebo upravit tak, aby vytvářely vlastní šablony pro vaše testovací prostředí. Každá šablona má odkaz na nasazení testovacího prostředí, protože je ve vašem vlastním předplatném Azure. Nebo můžete přizpůsobit šablonu a [nasadit pomocí PowerShellu nebo Azure CLI](../azure-resource-manager/templates/deploy-powershell.md).


### <a name="can-i-have-all-virtual-machines-to-be-created-in-a-common-resource-group-instead-having-each-machine-in-its-own-resource-group"></a>Můžu mít všechny virtuální počítače, které mají být vytvořeny ve společné skupině prostředků místo toho, aby každý počítač ve své vlastní skupině prostředků?
Ano, jako vlastník testovacího prostředí můžete buď nechat testovací prostředí zpracovat přidělení skupiny prostředků za vás, nebo mít všechny virtuální počítače vytvořené ve společné skupině prostředků, kterou zadáte.

Scénář samostatné skupiny prostředků:
-   DevTest Labs vytvoří novou skupinu prostředků pro každý veřejný/privátní virtuální počítač IP, který roztáčíte
-   DevTest Labs vytvoří skupinu prostředků pro sdílené počítače IP, které patří do stejné velikosti.

Běžný scénář skupiny prostředků:
-   Všechny virtuální počítače se stočejí do společné skupiny prostředků, kterou zadáte. Další [přidělení skupiny prostředků pro testovací prostředí](https://aka.ms/RGControl).

### <a name="how-do-i-maintain-a-naming-convention-across-my-devtest-labs-environment"></a>Jak lze udržovat konvence pojmenování v prostředí DevTest Labs?
Můžete chtít rozšířit aktuální konvence pojmenování rozlehlé sítě na operace Azure a učinit je konzistentní v prostředí DevTest Labs. Při nasazování DevTest Labs doporučujeme, abyste měli konkrétní zásady spuštění. Tyto zásady nasadíte pomocí centrálního skriptu a šablon JSON, abyste vynutili konzistenci. Zásady pojmenování lze implementovat prostřednictvím zásad Azure používaných na úrovni předplatného. Ukázky JSON pro zásady Azure najdete v [tématu ukázky zásad Azure](../governance/policy/samples/index.md).

### <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>Kolik testovacích prostředí mohu vytvořit v rámci stejného předplatného?
Neexistuje konkrétní limit na počet testovacích prostředí, které lze vytvořit na jedno předplatné. Množství prostředků použitých na jedno předplatné je však omezené. Můžete si přečíst o [omezení a kvóty pro předplatná Azure](../azure-resource-manager/management/azure-subscription-service-limits.md) a jak zvýšit tato [omezení](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).


### <a name="how-many-vms-can-i-create-per-lab"></a>Kolik virtuálních virtuálních můžu vytvořit na testovací prostředí?
Neexistuje žádné konkrétní omezení počtu virtuálních počítačů, které lze vytvořit na testovací prostředí. Prostředky (jádra virtuálních počítačů, veřejné IP adresy a tak dále), které se používají, jsou však omezené na jedno předplatné. Můžete si přečíst o [omezení a kvóty pro předplatná Azure](../azure-resource-manager/management/azure-subscription-service-limits.md) a jak zvýšit tato [omezení](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

### <a name="how-do-i-determine-the-ratio-of-users-per-lab-and-the-overall-number-of-labs-that-are-needed-across-an-organization"></a>Jak zjistím poměr uživatelů na testovací prostředí a celkový počet testovacích prostředí, které jsou potřeba v rámci organizace?
Doporučujeme, aby organizační jednotky a vývojové skupiny, které jsou přidruženy ke stejnému vývojovému projektu, byly přidruženy ke stejnému testovacímu prostředí. Umožňuje stejné typy zásad, bitových kopií a zásad vypnutí, které mají být použity pro obě skupiny.

Možná budete také muset zvážit zeměpisné hranice. Například vývojáři v severovýchodní Spojené státy (USA) mohou používat testovací prostředí zřízené v usa – východ. A vývojáři v Dallasu, Texasu a Denveru v Coloradu mohou být nasměrováni k použití zdroje v americkém south centralu. Pokud existuje společné úsilí s externí třetí stranou, mohou být přiřazeny k testovacímu prostředí, které interní vývojáři nepoužívají.

Můžete také použít testovací prostředí pro konkrétní projekt v rámci projektů Azure DevOps. Potom použijete zabezpečení prostřednictvím zadané skupiny Služby Azure Active Directory, která umožňuje přístup k oběma množině prostředků. Virtuální síť přiřazená testovacímu prostředí může být další hranicí pro konsolidaci uživatelů.

### <a name="how-can-we-prevent-the-deletion-of-resources-within-a-lab"></a>Jak můžeme zabránit odstranění prostředků v rámci testovacího prostředí?
Doporučujeme nastavit správná oprávnění na úrovni testovacího prostředí tak, aby pouze oprávnění uživatelé mohli odstranit prostředky nebo změnit zásady testovacího prostředí. Vývojáři by měly být umístěny v rámci **skupiny DevTest Labs Users.** Vedoucím vývojářem nebo vedoucím infrastruktury by měl být **vlastník devTest Labs**. Doporučujeme, abyste měli pouze dva vlastníky laboratoře. Tato zásada sahá směrem k úložišti kódu, aby se zabránilo poškození. Uživatelé testovacího prostředí mají práva k použití prostředků, ale nemohou aktualizovat zásady testovacího prostředí. Podívejte se na následující článek, který uvádí role a práva, které má každá předdefinovaná skupina v rámci testovacího prostředí: [Přidání vlastníků a uživatelů v laboratořích Azure DevTest .](devtest-lab-add-devtest-user.md)

### <a name="how-do-i-share-a-direct-link-to-my-lab"></a>Jak mohu sdílet přímý odkaz na svou laboratoř?

1. Na [webu Azure Portal](https://portal.azure.com)přejděte do testovacího prostředí.
2. Zkopírujte **adresu URL testovacího prostředí** z prohlížeče a pak ji sdílejte s uživateli testovacího prostředí.

> [!NOTE]
> Pokud je uživatel testovacího prostředí externím uživatelem, který má účet Microsoft, ale není členem instance služby Active Directory vaší organizace, může se při pokusu o přístup ke sdílenému odkazu zobrazit chybová zpráva. Pokud se externímu uživateli zobrazí chybová zpráva, požádejte ho, aby nejprve vyberte své jméno v pravém horním rohu portálu Azure. Potom v části Adresář v nabídce může uživatel vybrat adresář, ve kterém testovací prostředí existuje.

## <a name="virtual-machines"></a>Virtual Machines

### <a name="why-cant-i-see-vms-on-the-virtual-machines-page-that-i-see-in-devtest-labs"></a>Proč nevidím virtuální počítače na stránce Virtuální počítače, které vidím v DevTest Labs?
Když vytvoříte virtuální hod v DevTest Labs, budete mít oprávnění k přístupu k tomuto virtuálnímu jevu. Virtuální počítač můžete zobrazit na stránce testovacího prostředí i na stránce **Virtuální počítače.** Uživatelé přiřazení k roli **vlastníka devTest Labs** můžou zobrazit všechny virtuální počítače, které byly vytvořeny v testovacím prostředí na stránce **Všechny virtuální počítače** testovacího prostředí. Uživatelům, kteří mají roli **Uživatele devTest Labs** však není automaticky udělen přístup pro čtení k prostředkům virtuálního počítačů, které vytvořili jiní uživatelé. Takže tyto virtuální počítače se nezobrazují na stránce **virtuální počítače.**


### <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>Jak vytvořím více virtuálních počítačů ze stejné šablony najednou?
Máte dvě možnosti pro současné vytvoření více virtuálních počítačů ze stejné šablony:

- Můžete použít [rozšíření Azure DevOps Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
- [Šablonu Správce prostředků](devtest-lab-add-vm.md#save-azure-resource-manager-template) můžete generovat při vytváření virtuálního počítače a [nasadit šablonu Správce prostředků z prostředí Windows PowerShell](../azure-resource-manager/templates/deploy-powershell.md).
- Můžete také určit více než jednu instanci počítače, který má být vytvořen během vytváření virtuálního počítače. Další informace o vytváření více instancí virtuálních počítačů najdete v dokumentu o [vytvoření testovacího virtuálního počítače](devtest-lab-add-vm.md).

### <a name="how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab"></a>Jak můžu přesunout svoje stávající virtuální počítače Azure do testovacího prostředí DevTest Labs?
Kopírování stávajících virtuálních počítačů do laboratoří DevTest:

1.  Zkopírujte soubor VHD existujícího virtuálního počítače pomocí [skriptu prostředí Windows PowerShell](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/Scripts/CopyVirtualMachines/CopyAzVHDFromVMToLab.ps1).
2.  Vytvořte [vlastní bitovou kopii](devtest-lab-create-template.md) v testovacím prostředí DevTest Labs.
3.  Vytvořte virtuální počítač v testovacím prostředí z vlastní image.

### <a name="can-i-attach-multiple-disks-to-my-vms"></a>Můžu k virtuálním počítačům připojit víc disků?

Ano, k virtuálním počítačům můžete připojit více disků.

### <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>Pokud chci k testování použít bitovou kopii operačního systému Windows, musím si zakoupit předplatné MSDN?
Pokud chcete pro vývoj nebo testování v Azure používat bitové kopie operačního systému Windows klienta (Windows 7 nebo novější) pro vývoj nebo testování, udělejte jeden z následujících kroků:

- [Zakoupení předplatného MSDN](https://www.visualstudio.com/products/how-to-buy-vs).
- Pokud máte smlouvu Enterprise, vytvořte předplatné Azure s [nabídkou Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p).

Další informace o kreditech Azure pro každou nabídku MSDN najdete [v tématu Měsíční kredit Azure pro předplatitele Visual Studia](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).


### <a name="how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>Jak zautomatizovat proces odstranění všech virtuálních měn v testovacím prostředí?
Jako vlastník testovacího prostředí můžete odstranit virtuální počítače z testovacího prostředí na webu Azure Portal. Můžete také odstranit všechny virtuální počítače v testovacím prostředí pomocí skriptu PowerShell. V následujícím příkladu pod **hodnotami pro změnu** komentáře upravte hodnoty parametrů. Hodnoty subscriptionId, labResourceGroup a labName můžete načíst z podokna testovacího prostředí na webu Azure Portal.

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

### <a name="how-can-i-use-resource-manager-templates-in-my-devtest-labs-environment"></a>Jak lze používat šablony Správce prostředků v prostředí DevTest Labs?
Nasadíte šablony Správce prostředků do prostředí DevTest Labs pomocí kroků uvedených v funkci Prostředí v článku [DevTest Labs.](devtest-lab-test-env.md) V podstatě zkontrolujete šablony Správce prostředků do úložiště Git (azure úložiště nebo GitHub) a přidáte [privátní úložiště pro šablony](devtest-lab-test-env.md) do testovacího prostředí. Tento scénář nemusí být užitečné, pokud používáte DevTest Labs k hostování vývojových počítačů, ale může být užitečné, pokud vytváříte pracovní prostředí, které je reprezentativní pro produkční prostředí.

Je také třeba poznamenat, že počet virtuálních počítačů na testovací prostředí nebo na uživatele možnost omezuje pouze počet počítačů nativně vytvořených v samotném testovacím prostředí, a nikoli v žádném prostředí (šablony Resource Manageru).

## <a name="custom-images"></a>Vlastní image

### <a name="how-can-i-set-up-an-easily-repeatable-process-to-bring-my-custom-organizational-images-into-a-devtest-labs-environment"></a>Jak lze nastavit snadno opakovatelný proces, který přenese mé vlastní organizační obrázky do prostředí DevTest Labs?
Podívejte se na toto [video na vzoru Image Factory](https://sec.ch9.ms/ch9/8e8a/9ea0b8d4-b803-4f23-bca4-4808d9368e8a/dtlimagefactory_mid.mp4). Tento scénář je pokročilý scénář a poskytované skripty jsou pouze ukázkové skripty. Pokud jsou požadovány nějaké změny, je třeba spravovat a udržovat skripty používané ve vašem prostředí.

Podrobné informace o vytvoření továrny bitových bitových zjištění najdete [v tématu Vytvoření vlastní továrny bitových bitů v laboratořích Azure DevTest .](image-factory-create.md)

### <a name="what-is-the-difference-between-a-custom-image-and-a-formula"></a>Jaký je rozdíl mezi vlastním obrázkem a vzorcem?
Vlastní bitová kopie je spravovaná bitová kopie. Vzorec je obrázek, který můžete nakonfigurovat s dalšími nastaveními a potom uložit a reprodukovat. Vlastní obrázek může být vhodnější, pokud chcete rychle vytvořit několik prostředí pomocí stejného základního, neměnného obrázku. Vzorec může být lepší, pokud chcete reprodukovat konfiguraci virtuálního počítače s nejnovějšíbity, jako součást virtuální sítě nebo podsítě nebo jako virtuální počítač určité velikosti. Podrobnější vysvětlení najdete v tématu [Porovnání vlastních obrázků a vzorců v devTest Labs](devtest-lab-comparing-vm-base-image-types.md).

### <a name="when-should-i-use-a-formula-vs-custom-image"></a>Kdy mám použít vzorec vs. vlastní obrázek?
Rozhodujícím faktorem v tomto scénáři je obvykle náklady a opětovné použití. Pokud máte scénář, kdy mnoho uživatelů nebo testovacích prostředí vyžaduje bitovou kopii s velkým množstvím softwaru nad základní bitovou kopii, můžete snížit náklady vytvořením vlastní bitové kopie. To znamená, že obraz je vytvořen jednou. Snižuje dobu nastavení virtuálního počítače a náklady vzniklé v důsledku spuštění virtuálního počítače při instalaci.

Dalším faktorem, který je třeba poznamenat, je však četnost změn softwarového balíčku. Pokud spustíte denní sestavení a budete vyžadovat, aby tento software byl na virtuálních počítačích vašich uživatelů, zvažte použití vzorce namísto vlastní bitové kopie.

Podrobnější vysvětlení najdete v tématu [Porovnání vlastních obrázků a vzorců](devtest-lab-comparing-vm-base-image-types.md) v devTest Labs.

### <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>Jak lze automatizovat proces nahrávání souborů VHD a vytvářet vlastní obrázky?

Chcete-li automatizovat nahrávání souborů VHD a vytvářet vlastní obrázky, máte dvě možnosti:

- Pomocí [aplikace AzCopy](../storage/common/storage-use-azcopy-v10.md) zkopírujte nebo nahrajte soubory Virtuálního pevného disku do účtu úložiště, který je přidružen k testovacímu prostředí.
- Použijte [Průzkumníka úložiště Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md). Storage Explorer je samostatná aplikace, která běží na Windows, OS X a Linux.

Jak najít účet cílového úložiště, který je přidružený k vašemu testovacímu prostředí:

1.  Přihlaste se k [portálu Azure](https://portal.azure.com).
2.  V levé nabídce vyberte **položku Skupiny prostředků**.
3.  Najděte a vyberte skupinu prostředků, která je přidružená k testovacímu prostředí.
4.  V části **Přehled**vyberte jeden z účtů úložiště.
5.  Vyberte **objekty BLOB**.
6.  Vyhledejte obrázky v seznamu. Pokud žádný neexistuje, vraťte se ke kroku 4 a zkuste jiný účet úložiště.
7.  Použijte **adresu URL** jako cíl v příkazu AzCopy.

Kdy mám použít image Azure Marketplace vs. vlastní image organizace?

### <a name="when-should-i-use-an-azure-marketplace-image-vs-my-own-custom-organizational-image"></a>Kdy mám použít image Azure Marketplace vs. vlastní image organizace?
Azure Marketplace by se měl používat ve výchozím nastavení, pokud nemáte konkrétní obavy nebo organizační požadavky. Některé běžné příklady zahrnují;

- Komplexní nastavení softwaru, které vyžaduje, aby aplikace byla zahrnuta jako součást základní bitové kopie.
- Instalace a nastavení aplikace může trvat mnoho hodin, což není efektivní využití výpočetního času, které mají být přidány na image Azure Marketplace.
- Vývojáři a testeři vyžadují rychlý přístup k virtuálnímu počítači a chtějí minimalizovat dobu nastavení nového virtuálního počítače.
- Dodržování předpisů nebo regulační podmínky (například zásady zabezpečení), které musí být zavedeny pro všechny počítače.
- Použití vlastních obrázků by nemělo být považováno za na lehkou váhu. Zavádějí mimořádnou složitost, protože nyní musíte spravovat soubory VHD pro tyto základní bitové kopie. Je také nutné pravidelně opravovat tyto základní bitové kopie aktualizacemi softwaru. Tyto aktualizace zahrnují nové aktualizace operačního systému (OS) a všechny aktualizace nebo změny konfigurace potřebné pro samotný softwarový balíček.

## <a name="artifacts"></a>Artefakty

### <a name="what-are-artifacts"></a>Co jsou artefakty?
Artefakty jsou přizpůsobitelné prvky, které můžete použít k nasazení nejnovějších bitů nebo nasazení dev nástrojů do virtuálního počítače. Při vytváření virtuálního počítače připojte artefakty k virtuálnímu počítači. Po zřízení virtuálního počítače artefakty nasadit a nakonfigurovat virtuální počítač. Různé již existující artefakty jsou k dispozici v našem [veřejném úložišti GitHub](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts). Můžete také [vytvářet vlastní artefakty](devtest-lab-artifact-author.md).

### <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>Můj artefakt se nezdařil během vytváření virtuálního řezu. Jak se s ním vyřešuji?
Informace o tom, jak získat protokoly pro neúspěšný artefakt, naleznete [v tématu Diagnostika selhání artefaktů v devTest Labs](devtest-lab-troubleshoot-artifact-failure.md).

### <a name="when-should-an-organization-use-a-public-artifact-repository-vs-private-artifact-repository-in-devtest-labs"></a>Kdy by měla organizace používat veřejné úložiště artefaktů vs. soukromé úložiště artefaktů v devTest Labs?
[Veřejné úložiště artefaktů](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) poskytuje počáteční sadu softwarových balíčků, které se nejčastěji používají. Pomáhá s rychlým nasazením, aniž by bylo nutné investovat čas na reprodukci běžných vývojářských nástrojů a doplňků. Můžete nasadit vlastní soukromé úložiště. Můžete použít veřejné a soukromé úložiště paralelně. Můžete také zakázat veřejné úložiště. Kritéria pro nasazení soukromého úložiště by měla být řízena následujícími otázkami a aspekty:

- Má organizace požadavek mít firemní licencovaný software jako součást své nabídky DevTest Labs? Pokud je odpověď ano, pak by měl být vytvořen soukromý úložiště.
- Vyvíjí organizace vlastní software, který poskytuje konkrétní operaci, která je vyžadována jako součást celkového procesu zřizování? Pokud je odpověď ano, pak by měl být nasazen soukromý repozitář.
- Pokud zásady zásad správného řízení organizace vyžaduje izolaci a externí úložiště nejsou pod přímou správou konfigurace organizace, soukromé úložiště artefaktů by měla být nasazena. V rámci tohoto procesu lze zkopírovat a integrovat první kopii veřejného úložiště se soukromým úložištěm. Veřejné úložiště pak může být zakázáno, aby k němu už nikdo v rámci organizace neměl přístup. Tento přístup vynutí, aby všichni uživatelé v rámci organizace měli pouze jedno úložiště schválené organizací a minimalizovalo posun konfigurace.


### <a name="should-an-organization-plan-for-a-single-repository-or-allow-multiple-repositories"></a>Má organizace plánovat jedno úložiště nebo povolit více úložišť?
Jako součást celkové strategie správy zásad správného řízení a konfigurace vaší organizace doporučujeme použít centralizované úložiště. Při použití více úložišť se mohou v průběhu času stát sily nespravovaného softwaru. S centrální úložiště více týmů můžete využívat artefakty z tohoto úložiště pro své projekty. Vynucuje standardizaci, zabezpečení, snadnou správu a eliminuje zdvojování úsilí. V rámci centralizace jsou doporučovány následující postupy pro dlouhodobé řízení a udržitelnost:

- Přidružte azure úložiště ke stejnému tenantovi Azure Active Directory, který předplatné Azure používá pro ověřování a autorizaci.
- Vytvořte skupinu s názvem `All DevTest Labs Developers` ve službě Azure Active Directory, která je centrálně spravovaná. Každý vývojář, který přispívá k vývoji artefaktů by měl být umístěn v této skupině.
- Stejná skupina Azure Active Directory slouží k poskytování přístupu k úložišti Azure Repos a do testovacího prostředí.
- V Azure Repos větvení nebo rozvětvení by měl být použit k samostatné úložiště ve vývoji z úložiště primární produkční ho. Obsah se do hlavní větve přidává pouze s žádostí o přijetí vyžádat po řádné kontrole kódu. Jakmile recenzent kódu změnu schválí, vývojář hlavního zákazníka, který je zodpovědný za údržbu hlavní větve, sloučí aktualizovaný kód.

## <a name="cicd-integration"></a>Integrace CI/CD

### <a name="does-devtest-labs-integrate-with-my-cicd-toolchain"></a>Integruje se DevTest Labs s mým řetězcem nástrojů CI/CD?
Pokud používáte Azure DevOps, můžete použít [devTest Labs úkoly rozšíření](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) k automatizaci kanálu vydání v DevTest Labs. Některé z úkolů, které můžete dělat s tímto rozšířením patří:

- Vytvořte a nasaďte virtuální hospo- Virtuální počítač můžete také nakonfigurovat s nejnovějším sestavením pomocí úloh Azure File Copy nebo PowerShell Azure DevOps Services.
- Automaticky zachytit stav virtuálního počítače po testování reprodukovat chybu na stejném virtuálním počítači pro další šetření.
- Odstraňte virtuální virtuální ho na konci kanálu vydání, když už není potřeba.

Následující příspěvky blogu nabízejí pokyny a informace o použití rozšíření Služby Azure DevOps:

- [DevTest Labs a rozšíření Azure DevOps](integrate-environments-devops-pipeline.md)
- [Nasazení nového virtuálního počítače v existující laboratoři DevTest Labs ze služby Azure DevOps Services](https://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
- [Použití správy verzí služeb Azure DevOps Services pro průběžné nasazení do testovacích prostředí DevTest](https://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)

Pro další řetězce nástrojů průběžné integrace (CI)/průběžné doručování (CD) můžete dosáhnout stejných scénářů nasazením [šablon Azure Resource Manager](https://azure.microsoft.com/resources/templates/) pomocí rutin Azure [PowerShell](../azure-resource-manager/templates/deploy-powershell.md) a [sad .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/). Můžete také použít [REST API pro DevTest Labs](https://aka.ms/dtlrestapis) integrovat s toolchain.

## <a name="networking"></a>Síťové služby

### <a name="when-should-i-create-a-new-virtual-network-for-my-devtest-labs-environment-vs-using-an-existing-virtual-network"></a>Kdy mám vytvořit novou virtuální síť pro prostředí DevTest Labs vs. pomocí existující virtuální sítě?
Pokud vaše virtuální počítače potřebují komunikovat s existující infrastrukturou, zvažte použití existující virtuální sítě v prostředí DevTest Labs. Pokud používáte ExpressRoute, můžete minimalizovat množství virtuálních sítí / podsítí tak, aby fragmentovat ip adresní prostor, který získá přiřazeny pro použití v odběrech.

Zvažte použití vzoru partnerského vztahu virtuální sítě zde[(model Hub-Spoke)](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)příliš. Tento přístup umožňuje komunikaci vnetu/podsítě mezi předplatnými. V opačném případě může mít každé prostředí DevTest Labs vlastní virtuální síť.

Počet virtuálních sítí na jedno předplatné jsou [omezení.](../azure-resource-manager/management/azure-subscription-service-limits.md) Výchozí částka je 50, i když tento limit lze zvýšit na 100.

### <a name="when-should-i-use-a-shared-ip-vs-public-ip-vs-private-ip"></a>Kdy mám použít sdílenou IP adresu vs. veřejnou IP adresu vs. soukromou IP adresu?

Pokud používáte síť VPN nebo Express Route mezi webovými servery, zvažte použití privátních IP služeb tak, aby vaše počítače byly přístupné prostřednictvím interní sítě a nepřístupné prostřednictvím veřejného internetu.

> [!NOTE]
> Vlastníci testovacího prostředí můžete změnit tuto zásadu podsítě zajistit, že nikdo omylem vytvoří veřejné IP adresy pro své virtuální chod. Vlastník předplatného by měl vytvořit zásady předplatného, které zabrání vytváření veřejných IP služeb.

Při použití sdílených veřejných IP adres virtuální počítače v testovacím prostředí sdílejí veřejnou IP adresu. Tento přístup může být užitečný, když potřebujete zabránit porušení limitů na veřejné IP adresy pro dané předplatné.

### <a name="how-do-i-ensure-that-development-and-test-virtual-machines-are-unable-to-reach-the-public-internet-are-there-any-recommended-patterns-to-set-up-network-configuration"></a>Jak zajistím, aby se vývojové a testovací virtuální počítače nemohly dostat k veřejnému internetu? Existují nějaké doporučené vzory pro nastavení konfigurace sítě?

Ano. Existují dva aspekty, které je třeba zvážit – příchozí a odchozí provoz.

- **Příchozí provoz** – Pokud virtuální počítač nemá veřejnou IP adresu, nemůže se k němu dostat přes internet. Běžným přístupem je zajistit, aby byla nastavena zásada na úrovni předplatného, takže žádný uživatel nemůže vytvořit veřejnou IP adresu.
- **Odchozí provoz** – Pokud chcete zabránit virtuálním počítačům v přímém přístupu k veřejnému internetu a vynutit provoz prostřednictvím podnikové brány firewall, můžete směrovat provoz místně přes expresní trasu nebo SÍŤ VPN pomocí vynuceného směrování.

> [!NOTE]
> Pokud máte proxy server, který blokuje provoz bez nastavení proxy serveru, nezapomeňte přidat výjimky do účtu úložiště artefaktů testovacího prostředí.

Skupiny zabezpečení sítě můžete také použít pro virtuální počítače nebo podsítě. Tento krok přidá další vrstvu ochrany povolit / blokovat provoz.

## <a name="troubleshooting"></a>Řešení potíží

### <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>Proč se moje stávající virtuální síť neukládá správně?
Jednou z možností je, že název virtuální sítě obsahuje tečky. Pokud ano, zkuste období odebrat nebo je nahradit spojovníky. Potom zkuste znovu uložit virtuální síť.

### <a name="why-do-i-get-a-parent-resource-not-found-error-when-i-provision-a-vm-from-powershell"></a>Proč se při zřizování virtuálního rozhraní z Prostředí PowerShell zobrazuje chyba "Nadřazený prostředek nebyl nalezen"?
Pokud je jeden prostředek nadřazený k jinému prostředku, musí nadřazený prostředek existovat před vytvořením podřízeného prostředku. Pokud nadřazený prostředek neexistuje, zobrazí se zpráva **ParentResourceNotFound.** Pokud nezadáte závislost na nadřazeném prostředku, podřízený prostředek může být nasazen před nadřazeným.

Virtuální virtuální chody jsou podřízené prostředky v rámci testovacího prostředí ve skupině prostředků. Při použití šablon Správce prostředků k nasazení virtuálních počítače pomocí prostředí PowerShell, název skupiny prostředků zadaný ve skriptu Prostředí PowerShell by měl být název skupiny prostředků testovacího prostředí. Další informace naleznete [v tématu Poradce při potížích s běžnými chybami nasazení Azure](../azure-resource-manager/templates/common-deployment-errors.md).

### <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>Kde najdu další informace o chybě, pokud se nasazení virtuálního počítače nezdaří?
Chyby nasazení virtuálních počítače jsou zachyceny v protokolech aktivit. Protokoly aktivit virtuálních počítačů testovacího prostředí najdete v části **Protokoly auditování** nebo **Diagnostika virtuálního počítače** v nabídce prostředků na stránce virtuálního počítače testovacího prostředí (stránka se zobrazí po výběru virtuálního počítače ze seznamu Moje virtuální počítače).

Někdy dojde k chybě nasazení před zahájením nasazení virtuálního počítače. Příkladem je, když je překročen limit předplatného pro prostředek, který byl vytvořen pomocí virtuálního soudu. V tomto případě jsou podrobnosti o chybě zachyceny v protokolech aktivit na úrovni testovacího prostředí. Protokoly aktivit jsou umístěny v dolní části nastavení **konfigurace a zásad.** Další informace o používání protokolů aktivit v Azure najdete v [tématu Zobrazení protokolů aktivit k auditování akcí na prostředky](../azure-resource-manager/management/view-activity-logs.md).

### <a name="why-do-i-get-location-is-not-available-for-resource-type-error-when-trying-to-create-a-lab"></a>Proč se při pokusu o vytvoření testovacího prostředí zobrazuje chyba "Umístění není k dispozici pro typ prostředku"?
Při pokusu o vytvoření testovacího prostředí se může zobrazit chybová zpráva podobná následující:

```
The provided location 'australiacentral' is not available for resource type 'Microsoft.KeyVault/vaults'. List of available regions for the resource type is 'northcentralus,eastus,northeurope,westeurope,eastasia,southeastasia,eastus2,centralus,southcentralus,westus,japaneast,japanwest,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia,canadacentral,canadaeast,uksouth,ukwest,westcentralus,westus2,koreacentral,koreasouth,francecentral,southafricanorth
```

Tuto chybu můžete vyřešit jedním z následujících kroků:

#### <a name="option-1"></a>Možnost 1
Zkontrolujte dostupnost typu prostředku v oblastech Azure na stránce [Produkty dostupné podle oblastí.](https://azure.microsoft.com/global-infrastructure/services/) Pokud typ prostředku není k dispozici v určité oblasti, DevTest Labs nepodporuje vytvoření testovacího prostředí v této oblasti. Při vytváření testovacího prostředí vyberte jinou oblast.

#### <a name="option-2"></a>2. možnost
Pokud je typ prostředku dostupný ve vaší oblasti, zkontrolujte, jestli je registrovaný s vaším předplatným. To lze provést na úrovni vlastníka předplatného, jak je znázorněno v [tomto článku](../azure-resource-manager/management/resource-providers-and-types.md).
