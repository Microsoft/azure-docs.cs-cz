---
title: Nejčastější dotazy k Azure DevTest Labs | Microsoft Docs
description: Tento článek obsahuje odpovědi na některé nejčastější dotazy týkající se Azure DevTest Labs.
ms.topic: article
ms.date: 07/17/2020
ms.openlocfilehash: 1cbea3628d6c8c1b43766140d201ce46964a60b5
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92328381"
---
# <a name="azure-devtest-labs-faq"></a>Nejčastější dotazy k Azure DevTest Labs
Získejte odpovědi na některé z nejběžnějších otázek o Azure DevTest Labs.

## <a name="blog-post"></a>Příspěvek na blogu
Náš blog týmu DevTest Labs byl vyřazen od 20. března 2019. 

### <a name="where-can-i-track-feature-updates-from-now-on"></a>Kde můžu sledovat aktualizace funkcí od teď?
Od této chvíle budeme publikovat aktualizace funkcí a informativní blogové příspěvky na blogu Azure a na aktualizace Azure. Tyto blogové příspěvky budou také odkazovat na naši dokumentaci, kdykoli je to potřeba.

Přihlaste se k odběru [blogu Azure pro DevTest Labs](https://azure.microsoft.com/blog/tag/azure-devtest-labs/) a [aktualizace DevTest Labs Azure](https://azure.microsoft.com/updates/?product=devtest-lab) , abyste měli přehled o nových funkcích v DevTest Labs.

### <a name="what-happens-to-the-existing-blog-posts"></a>Co se stane s existujícími příspěvky na blogu?
V současnosti pracujeme na migraci stávajících příspěvků na blogu (s výjimkou aktualizací výpadků) do naší [dokumentace k DevTest Labs](devtest-lab-overview.md). Pokud je blog MSDN zastaralý, bude přesměrován na přehled dokumentace pro DevTest Labs. Po přesměrování můžete vyhledat článek, který hledáte v názvu Filter by. Zatím jsme nemigrovali všechny příspěvky, ale měli byste to udělat na konci tohoto měsíce. 


### <a name="where-do-i-see-outage-updates"></a>Kde se zobrazují aktualizace výpadku?
Pomocí naší služby Twitter vám budeme účtovat aktualizace výpadků, a to od teď až dál. Sledujte nás na Twitteru a získejte nejnovější aktualizace pro výpadky a známé chyby.

### <a name="twitter"></a>Twitter
Váš popisovač Twitteru: [@azlabservices](https://twitter.com/azlabservices)

## <a name="general"></a>Obecné
### <a name="what-if-my-question-isnt-answered-here"></a>Co když tady není odpověď na moji otázku?
Pokud tady uvedený dotaz není, sdělte nám, abychom vám pomohli najít odpověď.

- Na konci těchto nejčastějších dotazů si vystavte otázku.
- Pokud chcete oslovit širší cílovou skupinu, pošlete dotaz na [stránku s dotazem na Microsoft Q&Azure DevTest Labs](/answers/topics/azure-devtestlabs.html). Zapojte se do týmu Azure DevTest Labs a dalších členů komunity.
- V případě žádostí o funkce odešlete žádosti a nápady [Azure DevTest Labs uživatelského hlasu](https://feedback.azure.com/forums/320373-azure-devtest-labs).

### <a name="what-is-a-microsoft-account"></a>Co je účet Microsoft?
Účet Microsoft je účet, který použijete pro téměř všechno, co děláte se zařízeními a službami Microsoftu. Jedná se o e-mailovou adresu a heslo, které používáte k přihlášení ke službám Skype, Outlook.com, OneDrive, Windows Phone, Azure a Xbox Live. Jeden účet znamená, že vaše soubory, fotky, kontakty a nastavení vás můžou sledovat na jakémkoli zařízení.

> [!NOTE]
> Účet Microsoft, který se používá pro volání Windows Live ID.

### <a name="why-should-i-use-azure-devtest-labs"></a>Proč mám použít Azure DevTest Labs?
Azure DevTest Labs může váš tým ušetřit čas i peníze. Vývojáři mohou vytvářet vlastní prostředí pomocí několika různých základů. Můžou také použít artefakty k rychlému nasazování a konfiguraci aplikací. Pomocí vlastních imagí a vzorců můžete virtuální počítače ukládat jako šablony a snadno je reprodukována v rámci týmu. DevTest Labs také nabízí několik konfigurovatelných zásad, které mohou správci testovacího prostředí využít ke snížení odpadu a správě prostředí týmu. Mezi tyto zásady patří automatické vypnutí, prahová hodnota nákladů, maximální počet virtuálních počítačů na uživatele a maximální velikost virtuálního počítače. Podrobnější vysvětlení DevTest Labs najdete v tématu [Přehled](devtest-lab-overview.md) nebo [úvodní video](https://channel9.msdn.com/Blogs/Azure/what-is-azure-devtest-labs).

### <a name="what-does-worry-free-self-service-mean"></a>Co znamená "Samoobslužná služba bez obav"?
Samoobslužná služba bez obav znamená, že vývojáři a testeri si podle potřeby vytvářejí vlastní prostředí. Správci mají jistotu, že DevTest Labs vám pomůžou nastavit příslušný přístup a minimalizovat náklady na odpad a kontrolu. Správci mohou určit, jaké velikosti virtuálních počítačů jsou povoleny, maximální počet virtuálních počítačů a spuštění a vypnutí virtuálních počítačů. DevTest Labs také usnadňuje monitorování nákladů a nastavování výstrah, které vám pomůžou udržet informace o tom, jak se používají prostředky testovacího prostředí.

### <a name="how-can-i-use-devtest-labs"></a>Jak můžu používat DevTest Labs?
DevTest Labs je užitečné kdykoli, když požadujete vývojové nebo testovací prostředí a chcete je rychle reprodukováni, nebo je můžete spravovat pomocí zásad úspory nákladů.
Tady je několik scénářů, které naši zákazníci používají DevTest Labs pro:

- Spravujte prostředí pro vývoj a testování na jednom místě. Pomocí zásad můžete snížit náklady a vytvořit vlastní image pro sdílení sestavení v celém týmu.
- Vývoj aplikace pomocí vlastních imagí k uložení stavu disku v průběhu fází vývoje.
- Sledujte náklady ve vztahu k průběhu.
- Vytváření hromadných testovacích prostředí pro testování kvality zabezpečování.
- Použití artefaktů a vzorců umožňuje snadnou konfiguraci a reprodukování aplikace v různých prostředích.
- Distribuujte virtuální počítače pro hackathony (Týmová vývoj nebo testování práce) a pak je snadno oddělte, až událost skončí.

### <a name="how-am-i-billed-for-devtest-labs"></a>Jak se fakturuje DevTest Labs?
DevTest Labs je bezplatná služba. Vytváření Labs a konfigurace zásad, šablon a artefaktů v DevTest Labs je zdarma. Platíte jenom za prostředky Azure používané v laboratořích, jako jsou virtuální počítače, účty úložiště a virtuální sítě. Další informace o nákladech na prostředky testovacího prostředí najdete v tématu [Azure DevTest Labs ceny](https://azure.microsoft.com/pricing/details/devtest-lab/).

## <a name="security"></a>Zabezpečení

### <a name="what-are-the-different-security-levels-in-devtest-labs"></a>Jaké jsou různé úrovně zabezpečení v DevTest Labs?
Přístup k zabezpečení závisí na řízení přístupu na základě role Azure (Azure RBAC). Pokud se chcete dozvědět, jak Access funguje, pomůže se naučit rozdíly mezi oprávněními, rolí a oborem definovaným v Azure RBAC.

- **Oprávnění**: oprávnění je definovaný přístup k určité akci. Oprávnění může být například přístup pro čtení ke všem virtuálním počítačům.
- **Role**: role je sada oprávnění, která se dají seskupit a přiřadit k uživateli. Například uživatel s rolí vlastníka předplatného má přístup ke všem prostředkům v rámci předplatného.
- **Obor**: obor je úroveň v rámci hierarchie prostředku Azure. Oborem může být například skupina prostředků, jedna laboratoř nebo celé předplatné.

V rámci rozsahu DevTest Labs existují dva typy rolí, které definují oprávnění uživatele:

- **Vlastník testovacího prostředí**: vlastník testovacího prostředí má přístup ke všem prostředkům v testovacím prostředí. Vlastník testovacího prostředí může měnit zásady, číst a zapisovat na všechny virtuální počítače, měnit virtuální síť a tak dále.
- **Uživatel testovacího prostředí**: uživatel testovacího prostředí může zobrazit všechny prostředky testovacího prostředí, jako jsou virtuální počítače, zásady a virtuální sítě. Uživatel testovacího prostředí ale nemůže měnit zásady nebo žádné virtuální počítače, které vytvořili jiní uživatelé.

Můžete také vytvořit vlastní role v DevTest Labs. Informace o tom, jak vytvářet vlastní role v DevTest Labs, najdete v tématu [udělení uživatelských oprávnění konkrétním zásadám testovacího prostředí](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Vzhledem k tomu, že obory jsou hierarchické, pokud má uživatel oprávnění v určitém oboru, uživateli se tato oprávnění automaticky udělí v každém oboru nižší úrovně v oboru. Pokud je například uživateli přiřazena role vlastníka předplatného, má uživatel přístup ke všem prostředkům v rámci předplatného. Mezi tyto prostředky patří virtuální počítače, virtuální sítě a laboratoře. Vlastník předplatného automaticky zdědí roli vlastníka testovacího prostředí. Opak však není pravda. Vlastník testovacího prostředí má přístup k testovacímu prostředí, což je nižší obor než úroveň předplatného. Vlastník testovacího prostředí proto nemůže zobrazit virtuální počítače, virtuální sítě ani žádné další prostředky, které jsou mimo testovací prostředí.

### <a name="how-do-i-define-azure-role-based-access-control-for-my-devtest-labs-environments-to-ensure-that-it-can-govern-while-developerstest-can-do-their-work"></a>Návody pro prostředí DevTest Labs definovat řízení přístupu na základě role v Azure, aby se zajistilo, že se bude moct řídit i když vývojáři/testy můžou dělat svou práci?
Existuje velký model, ale podrobnosti závisí na vaší organizaci.

Centrální IT oddělení by mělo mít pouze to, co je potřeba, a umožnit týmům projektu a aplikace, aby měli potřebnou úroveň řízení. Obvykle to znamená, že je ústřední IT vlastníkem předplatného a zpracovává základní funkce IT, jako jsou třeba síťové konfigurace. Sada **vlastníků** předplatného musí být malá. Tito vlastníci můžou jmenovat další vlastníky, pokud je potřeba, nebo použít zásady na úrovni předplatného, například bez veřejné IP adresy.

Může existovat podmnožina uživatelů, kteří vyžadují přístup v rámci předplatného, jako je Tier1 nebo podpora vrstvy 2. V takovém případě doporučujeme těmto uživatelům udělit přístup **přispěvatelům** , aby mohli spravovat prostředky, ale neposkytovali přístup k uživatelům nebo neupravují zásady.

Prostředek DevTest Labs by měl vlastnit vlastníci, kteří jsou blízko týmu projektu nebo aplikace. Je to proto, že rozumí jejich požadavkům na počítače a požadovaný software. Ve většině organizací je vlastníkem tohoto prostředku DevTest Labs obvykle vedoucí projektu nebo vývoje. Tento vlastník může spravovat uživatele a zásady v rámci testovacího prostředí a může spravovat všechny virtuální počítače v prostředí DevTest Labs.

Členové týmu projektu nebo aplikace by měli být přidáni do role **uživatele DevTest Labs** . Tito uživatelé můžou vytvářet virtuální počítače (v souladu s zásadami testovacího prostředí a na úrovni předplatného). Můžou taky spravovat svoje vlastní virtuální počítače. Nemůžou spravovat virtuální počítače, které patří jiným uživatelům.

Další informace najdete v tématu základní informace o [službě Azure Enterprise lešení – dokumentace zásad správného řízení předplatného](/azure/architecture/cloud-adoption/appendix/azure-scaffold).


### <a name="how-do-i-create-a-role-to-allow-users-to-do-a-specific-task"></a>Návody vytvořit roli, která uživatelům umožní provést konkrétní úkol?
Komplexní článek o vytváření vlastních rolí a přiřazování oprávnění k roli najdete v tématu [udělení uživatelských oprávnění konkrétním zásadám testovacího prostředí](devtest-lab-grant-user-permissions-to-specific-lab-policies.md). Tady je příklad skriptu, který vytváří **pokročilého uživatele role DevTest Labs**, který má oprávnění ke spouštění a zastavování všech virtuálních počítačů v testovacím prostředí:


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

### <a name="how-can-an-organization-ensure-corporate-security-policies-are-in-place"></a>Jak může organizace zajistit, aby byly zásady zabezpečení společnosti zavedeny?
Organizace ji může dosáhnout provedením následujících akcí:

- Vývoj a publikování komplexních zásad zabezpečení. Zásady překloubují pravidla přijatelného použití související s používáním softwaru, cloudových prostředků. Definuje také, co jasně narušuje zásady.
- Vývoj vlastní image, vlastních artefaktů a procesu nasazení, který umožňuje orchestraci v rámci sféry zabezpečení, která je definovaná ve službě Active Directory. Tento přístup vynutil hranici společnosti a nastavuje společnou sadu ovládacích prvků pro životní prostředí. Tyto ovládací prvky pro prostředí může vývojář při vývoji brát v úvahu a v rámci celého procesu dodržujte životní cyklus bezpečného vývoje. Cílem je také poskytnout prostředí, které není příliš omezující, což může bránit vývoji, ale přiměřenou sadu ovládacích prvků. Zásady skupiny v organizační jednotce (OU), která obsahuje virtuální počítače testovacího prostředí, můžou být podmnožinou celkových zásad skupiny, které se nacházejí v produkčním prostředí. Alternativně může být další sada, která bude správně zmírnit všechna zjištěná rizika.

### <a name="how-can-an-organization-ensure-data-integrity-to-ensure-that-remoting-developers-cant-remove-code-or-introduce-malware-or-unapproved-software"></a>Jak může organizace zajistit integritu dat, aby vývojáři vzdálené komunikace nemohli odebrat kód nebo zavést malware nebo neschválený software?
Existuje několik vrstev řízení pro zmírnění hrozby od externích konzultantů, dodavatelů nebo zaměstnanců, kteří pracují v DevTest Labs při spolupráci.

Jak bylo uvedeno dříve, musí mít první krok v konceptu přijatelné zásady použití, které budou jasně vypsány a definovány tak, aby jasné, které důsledky porušují nějaké zásady.

První vrstvou ovládacích prvků pro vzdálený přístup je použití zásady vzdáleného přístupu prostřednictvím připojení VPN, které není přímo připojené k testovacímu prostředí.

Druhá vrstva ovládacích prvků je použít sadu objektů zásad skupiny, které zabraňují kopírování a vkládání prostřednictvím vzdálené plochy. Je možné implementovat zásady sítě, aby nepovolovaly odchozí služby z prostředí, jako jsou služby FTP a RDP, mimo prostředí. Uživatelem definované směrování může vynutit všechny síťové přenosy v Azure zpátky do místního prostředí, ale směrování nemohlo pro všechny adresy URL, které by mohly umožňovat nahrávání dat, nastavovat přenosy dat, pokud se neřídí proxy serverem pro skenování obsahu a relací. Veřejné IP adresy se můžou omezit v rámci virtuální sítě, které podporují DevTest Labs, aby nepovolovaly přemostění prostředků externí sítě.

V konečném případě je třeba v rámci organizace použít stejný typ omezení, který by měl mít za všechny možné metody vyměnitelných médií nebo externích adres URL, které by mohly přijmout obsah. Pokud chcete zkontrolovat a implementovat zásady zabezpečení, obraťte se na svého odborníka na zabezpečení. Další doporučení najdete v tématu [Microsoft Internet Security](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs).

## <a name="lab-configuration"></a>Konfigurace testovacího prostředí

### <a name="how-do-i-create-a-lab-from-a-resource-manager-template"></a>Návody vytvořit testovací prostředí ze šablony Správce prostředků?
Nabízíme [úložiště GitHubu šablon testovacího Azure Resource Manager prostředí](https://azure.microsoft.com/resources/templates/101-dtl-create-lab) , které můžete nasadit tak, jak jsou, nebo upravit a vytvořit vlastní šablony pro cvičení. Každá šablona obsahuje odkaz na nasazení testovacího prostředí, protože je ve vašem vlastním předplatném Azure. Nebo můžete šablonu přizpůsobit a [nasadit pomocí PowerShellu nebo Azure CLI](../azure-resource-manager/templates/deploy-powershell.md).


### <a name="can-i-have-all-virtual-machines-to-be-created-in-a-common-resource-group-instead-having-each-machine-in-its-own-resource-group"></a>Můžu se všechny virtuální počítače vytvořit ve společné skupině prostředků, takže každý počítač má vlastní skupinu prostředků?
Ano, jako vlastník testovacího prostředí můžete buď nechat testovací prostředí zpracovat přidělení skupiny prostředků, nebo nechat všechny virtuální počítače vytvořené v rámci společné skupiny prostředků, kterou zadáte.

Scénář samostatné skupiny prostředků:
-   DevTest Labs vytvoří novou skupinu prostředků pro každý virtuální počítač s veřejnými a soukromými IP adresami, který jste vytočíi.
-   DevTest Labs vytvoří skupinu prostředků pro sdílené IP počítače, které patří do stejné velikosti.

Scénář běžných skupin prostředků:
-   Všechny virtuální počítače se nastavují do společné skupiny prostředků, kterou zadáte. Přečtěte si další informace [o přidělování skupin prostředků pro testovací prostředí](./resource-group-control.md).

### <a name="how-do-i-maintain-a-naming-convention-across-my-devtest-labs-environment"></a>Návody udržovat zásady vytváření názvů napříč prostředím DevTest Labs?
Je možné, že budete chtít stávající podnikové zásady vytváření názvů v rámci Azure zvýšit a zajistit jejich konzistenci v prostředí DevTest Labs. Při nasazování DevTest Labs doporučujeme, abyste měli konkrétní počáteční zásady. Tyto zásady nasadíte pomocí centrálního skriptu a šablon JSON pro vymáhání konzistence. Zásady pojmenování se dají implementovat prostřednictvím zásad Azure použitých na úrovni předplatného. Ukázky JSON pro Azure Policy najdete v tématu [Azure Policy Samples](../governance/policy/samples/index.md).

### <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>Kolik laboratoří mohu vytvořit v rámci stejného předplatného?
Počet laboratoří, které se dají vytvořit pro každé předplatné, nemá konkrétní limit. Množství prostředků použitých v rámci předplatného je ale omezené. Můžete si přečíst o [omezeních a kvótách pro předplatná Azure](../azure-resource-manager/management/azure-subscription-service-limits.md) a [o tom, jak tato omezení zvýšit](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).


### <a name="how-many-vms-can-i-create-per-lab"></a>Kolik virtuálních počítačů je možné vytvořit pro testovací prostředí?
Počet virtuálních počítačů, které se dají vytvořit pro každé testovací prostředí, není nijak omezený. Prostředky (jádra virtuálních počítačů, veřejné IP adresy atd.) se ale u každého předplatného omezí. Můžete si přečíst o [omezeních a kvótách pro předplatná Azure](../azure-resource-manager/management/azure-subscription-service-limits.md) a [o tom, jak tato omezení zvýšit](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

### <a name="how-do-i-determine-the-ratio-of-users-per-lab-and-the-overall-number-of-labs-that-are-needed-across-an-organization"></a>Návody určit poměr uživatelů na testovací prostředí a celkový počet laboratorních cvičení potřebných v rámci organizace?
Doporučujeme, aby obchodní jednotky a skupiny pro vývoj, které jsou přidruženy ke stejnému vývojovému projektu, byly přidruženy ke stejnému testovacímu prostředí. Umožňuje použít pro obě skupiny stejné typy zásad, obrázků a zásad vypnutí.

Možná budete muset zvážit i geografické hranice. Například vývojáři v oblasti USA – východ USA (US) můžou používat testovací prostředí zřízené ve východní USA 2. A vývojáři v Dallasu, Texas a Denver můžou Coloradu směrovat na používání prostředků v USA (střed) – jih. Pokud se jedná o spolupráci s externí třetí stranou, mohla by být přiřazena testovacímu prostředí, které nepoužívá interní vývojáři.

Testovací prostředí můžete použít také pro konkrétní projekt v rámci Azure DevOps Projects. Pak můžete zabezpečení použít prostřednictvím zadané skupiny Azure Active Directory, která umožňuje přístup k oběma prostředkům. Pro konsolidaci uživatelů může být virtuální síť přiřazená k testovacímu prostředí jinou hranicí.

### <a name="how-can-we-prevent-the-deletion-of-resources-within-a-lab"></a>Jak můžeme zabránit odstranění prostředků v testovacím prostředí?
Doporučujeme, abyste na úrovni testovacího prostředí nastavili správná oprávnění, aby mohli odstraňovat prostředky nebo měnit zásady testovacího prostředí jenom autorizovaní uživatelé. Vývojáři by měli být uvedení do skupiny **uživatelů DevTest Labs** . Vedoucí vývojář nebo vedoucí infrastruktury by měli být **vlastníkem DevTest Labs**. Doporučujeme, abyste měli pouze dva vlastníky testovacího prostředí. Tato zásada rozšiřuje do úložiště kódu, aby nedocházelo k poškození. Uživatelé testovacího prostředí mají práva k používání prostředků, ale nemůžou aktualizovat zásady testovacího prostředí. Přečtěte si následující článek, který obsahuje informace o rolích a oprávněních, které mají jednotlivé předdefinované skupiny v rámci testovacího prostředí: [Přidání vlastníků a uživatelů v Azure DevTest Labs](devtest-lab-add-devtest-user.md).

### <a name="how-do-i-share-a-direct-link-to-my-lab"></a>Návody sdílet přímý odkaz na můj testovací prostředí?

1. V [Azure Portal](https://portal.azure.com)přejdete do testovacího prostředí.
2. Zkopírujte **adresu URL testovacího prostředí** z prohlížeče a potom ho sdílejte s uživateli testovacího prostředí.

> [!NOTE]
> Pokud je uživatel testovacího prostředí externí uživatel, který má účet Microsoft, ale není členem instance Active Directory vaší organizace, může se uživateli při pokusu o přístup ke sdílenému odkazu zobrazit chybová zpráva. Pokud externí uživatel uvidí chybovou zprávu, požádejte uživatele, aby nejdřív vybral jméno v pravém horním rohu Azure Portal. V části adresář v nabídce pak může uživatel vybrat adresář, ve kterém testovací prostředí existuje.

## <a name="virtual-machines"></a>Virtuální počítače

### <a name="why-cant-i-see-vms-on-the-virtual-machines-page-that-i-see-in-devtest-labs"></a>Proč se na stránce Virtual Machines, kterou vidím v DevTest Labs, nezobrazily virtuální počítače?
Při vytváření virtuálního počítače v DevTest Labs máte oprávnění pro přístup k tomuto virtuálnímu počítači. Virtuální počítač můžete zobrazit na stránce Labs a na stránce **Virtual Machines** . Uživatelé přiřazení k roli **vlastníka DevTest Labs** můžou zobrazit všechny virtuální počítače, které byly vytvořené v testovacím prostředí, na stránce **všechny Virtual Machines** testovacího prostředí. Uživatelům, kteří mají roli **uživatele DevTest Labs** , ale nemají automaticky udělený přístup pro čtení k prostředkům virtuálního počítače, které vytvořili jiní uživatelé. Tyto virtuální počítače proto nejsou zobrazené na stránce **Virtual Machines** .


### <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>Návody vytvořit více virtuálních počítačů ze stejné šablony najednou?
Existují dvě možnosti pro současné vytvoření více virtuálních počítačů ze stejné šablony:

- Můžete použít [rozšíření úlohy Azure DevOps](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
- Když vytváříte virtuální počítač, můžete [vygenerovat šablonu správce prostředků](devtest-lab-add-vm.md#save-azure-resource-manager-template) a [nasadit šablonu správce prostředků z Windows PowerShellu](../azure-resource-manager/templates/deploy-powershell.md).
- Můžete také zadat více než jednu instanci počítače, který se má vytvořit během vytváření virtuálního počítače. Další informace o vytváření více instancí virtuálních počítačů najdete v dokumentu o [Vytvoření virtuálního počítače testovacího prostředí](devtest-lab-add-vm.md).

### <a name="how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab"></a>Návody přesunout stávající virtuální počítače Azure do testovacího prostředí DevTest Labs?
Zkopírování stávajících virtuálních počítačů do DevTest Labs:

1.  Zkopírujte soubor VHD existujícího virtuálního počítače pomocí [skriptu Windows PowerShellu](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/Scripts/CopyVirtualMachines/CopyAzVHDFromVMToLab.ps1).
2.  Vytvořte [vlastní image](devtest-lab-create-template.md) v testovacím prostředí DevTest Labs.
3.  Vytvořte virtuální počítač v testovacím prostředí z vlastní image.

### <a name="can-i-attach-multiple-disks-to-my-vms"></a>Můžu k virtuálním počítačům připojit více disků?

Ano, k virtuálním počítačům můžete připojit několik disků.

### <a name="are-gen-2-images-supported-by-devtest-labs"></a>Jsou pro DevTest Labs podporované image Gen 2?
Ano. Služba DevTest Labs podporuje [Image Gen 2](../virtual-machines/generation-2.md). Pokud jsou však pro Image k dispozici obě verze 1. generace a 2. DevTest Labs při vytváření virtuálního počítače zobrazuje pouze verzi 1. generace. Obrázek se zobrazí, pokud je k dispozici pouze obecná verze 2. 

### <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>Pokud chci pro svoje testování použít bitovou kopii operačního systému Windows, musím si koupit předplatné MSDN?
Pokud chcete pro vývoj nebo testování v Azure použít image operačního systému Windows Client (Windows 7 nebo novější), proveďte jeden z následujících kroků:

- [Kupte si předplatné MSDN](https://www.visualstudio.com/products/how-to-buy-vs).
- Pokud máte smlouva Enterprise, vytvořte si předplatné Azure s [nabídkou Enterprise pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0148p).

Další informace o kreditech Azure pro jednotlivé nabídky MSDN najdete v tématu [měsíční kredit Azure pro předplatitele sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).


### <a name="how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>Návody automatizujete proces odstraňování všech virtuálních počítačů v mém testovacím prostředí?
Jako vlastník testovacího prostředí můžete virtuální počítače z testovacího prostředí odstranit v Azure Portal. Všechny virtuální počítače v testovacím prostředí můžete také odstranit pomocí skriptu PowerShellu. V následujícím příkladu v části **hodnoty pro změnu** komentáře upravte hodnoty parametrů. `subscriptionId`Hodnoty, a můžete načíst `labResourceGroup` `labName` z podokna testovacího prostředí v Azure Portal.

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

### <a name="how-can-i-use-resource-manager-templates-in-my-devtest-labs-environment"></a>Jak můžu použít šablony Správce prostředků v prostředí DevTest Labs?
Šablony Správce prostředků můžete nasadit do prostředí DevTest Labs pomocí kroků uvedených v tématu [funkce prostředí v článku DevTest Labs](devtest-lab-test-env.md) . V podstatě zkontrolujete šablony Správce prostředků do úložiště Git (buď Azure Repos nebo GitHub), a přidejte do testovacího prostředí [privátní úložiště pro vaše šablony](devtest-lab-test-env.md) . Tento scénář nemusí být užitečný, pokud používáte DevTest Labs k hostování vývojových počítačů, ale může být užitečný, pokud vytváříte pracovní prostředí, které je reprezentativní pro produkci.

Také je potřeba poznamenat, že počet virtuálních počítačů na testovací prostředí nebo na uživatele omezuje jenom počet počítačů nativně vytvořených v testovacím prostředí, a ne žádné prostředí (Správce prostředků šablony).

## <a name="custom-images"></a>Vlastní image

### <a name="how-can-i-set-up-an-easily-repeatable-process-to-bring-my-custom-organizational-images-into-a-devtest-labs-environment"></a>Jak můžu snadno opakovat a přenést vlastní organizační image do prostředí DevTest Labs?
Podívejte se [na toto video ve vzoru pro vytváření imagí](https://sec.ch9.ms/ch9/8e8a/9ea0b8d4-b803-4f23-bca4-4808d9368e8a/dtlimagefactory_mid.mp4). Tento scénář je pokročilý scénář a poskytují se jenom ukázkové skripty. Pokud se vyžadují nějaké změny, budete muset spravovat a udržovat skripty používané ve vašem prostředí.

Podrobné informace o vytvoření objektu pro vytváření imagí najdete [v tématu Vytvoření vlastní továrny imagí v Azure DevTest Labs](image-factory-create.md).

### <a name="what-is-the-difference-between-a-custom-image-and-a-formula"></a>Jaký je rozdíl mezi vlastní obrázkou a vzorcem?
Vlastní image je spravovaná bitová kopie. Vzorec je obrázek, který můžete konfigurovat s dalšími nastaveními a pak ho uložit a znovu vytvořit. Vlastní image může být vhodnější, pokud chcete rychle vytvořit několik prostředí pomocí stejné základní, neproměnlivé image. Vzorec může být lepší, pokud chcete znovu vytvořit konfiguraci virtuálního počítače s využitím nejnovějších bitů, jako součást virtuální sítě nebo podsítě, nebo jako virtuální počítač určité velikosti. Podrobnější vysvětlení najdete v tématu [porovnání vlastních imagí a vzorců v DevTest Labs](devtest-lab-comparing-vm-base-image-types.md).

### <a name="when-should-i-use-a-formula-vs-custom-image"></a>Kdy mám použít vzorec vs. vlastní image?
Obvykle je rozhodující faktor v tomto scénáři náklady a znovu použít. Pokud máte scénář, ve kterém mnoho uživatelů/cvičení vyžaduje image s velkým množstvím softwaru nad základní imagí, pak můžete snížit náklady vytvořením vlastní image. Znamená to, že se image vytvoří jednou. Zkracuje dobu nastavení virtuálního počítače a náklady vzniklé v důsledku toho, že dojde k instalaci na virtuálním počítači.

Dodatečný faktor k poznámení je ale frekvence změn balíčku softwaru. Pokud spouštíte každodenní sestavení a požadujete, aby byl software na virtuálních počítačích vašich uživatelů, zvažte použití vzorce místo vlastní image.

Podrobnější vysvětlení najdete v tématu [porovnání vlastních imagí a vzorců](devtest-lab-comparing-vm-base-image-types.md) v DevTest Labs.

### <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>Návody automatizaci procesu nahrávání souborů VHD k vytváření vlastních imagí?

K automatickému nahrávání souborů VHD pro vytváření vlastních imagí máte dvě možnosti:

- Pomocí [AzCopy](../storage/common/storage-use-azcopy-v10.md) můžete kopírovat nebo nahrávat soubory VHD do účtu úložiště, který je přidružený k testovacímu prostředí.
- Použijte [Průzkumník služby Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md). Průzkumník služby Storage je samostatná aplikace, která běží na Windows, OS X a Linux.

Pokud chcete najít cílový účet úložiště, který je přidružený k vašemu testovacímu prostředí:

1.  Přihlaste se na [Azure Portal](https://portal.azure.com).
2.  V nabídce vlevo vyberte **skupiny prostředků**.
3.  Vyhledejte a vyberte skupinu prostředků, která je přidružená k vašemu testovacímu prostředí.
4.  V části **Přehled**vyberte jeden z účtů úložiště.
5.  Vyberte **objekty blob**.
6.  Vyhledejte v seznamu nahrávání. Pokud žádný neexistuje, vraťte se ke kroku 4 a zkuste použít jiný účet úložiště.
7.  Použijte **adresu URL** jako cíl v příkazu AzCopy.

Kdy mám použít Azure Marketplace image vs. vlastní organizační image?

### <a name="when-should-i-use-an-azure-marketplace-image-vs-my-own-custom-organizational-image"></a>Kdy mám použít Azure Marketplace image vs. vlastní organizační image?
Azure Marketplace by se měla používat ve výchozím nastavení, pokud nemáte konkrétní obavy nebo organizační požadavky. Mezi běžné příklady patří:

- Komplexní instalace softwaru, která vyžaduje, aby aplikace byla součástí základní image.
- Instalace a nastavení aplikace může trvat mnoho hodin, což není efektivní využití výpočetního času k přidání na Azure Marketplace image.
- Vývojáři a testeri vyžadují rychlý přístup k virtuálnímu počítači a chtějí minimalizovat dobu nastavení nového virtuálního počítače.
- Podmínky dodržování předpisů nebo regulativní podmínky (například zásady zabezpečení), které musí být na místě pro všechny počítače.
- Použití vlastních imagí by nemělo být považováno za lehce. Představují velmi složitost, protože teď musíte spravovat soubory VHD pro základní základní image. Také je potřeba rutinním pomocí aktualizací softwaru rutiny opravit. Tyto aktualizace zahrnují nové aktualizace operačního systému (OS) a všechny aktualizace nebo změny konfigurace, které jsou potřeba pro samotný softwarový balíček.

## <a name="artifacts"></a>Artifacts

### <a name="what-are-artifacts"></a>Co jsou artefakty?
Artefakty jsou přizpůsobitelné prvky, které můžete použít k nasazení nejnovějších bitů nebo nasazení vývojových nástrojů do virtuálního počítače. Když vytváříte virtuální počítač, připojte ke svému VIRTUÁLNÍmu počítači artefakty. Po zřízení virtuálního počítače nasadí artefakty a nakonfigurují váš virtuální počítač. V našem [veřejném úložišti GitHubu](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)jsou k dispozici různé již existující artefakty. Můžete také [vytvářet vlastní artefakty](devtest-lab-artifact-author.md).

### <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>Během vytváření virtuálního počítače selhal můj artefakt. Návody je řešit?
Informace o tom, jak získat protokoly pro artefakt, který selhal, najdete v tématu [postup diagnostiky selhání artefaktů v DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md).

### <a name="when-should-an-organization-use-a-public-artifact-repository-vs-private-artifact-repository-in-devtest-labs"></a>Kdy by organizace měla v DevTest Labs použít veřejné úložiště artefaktů a soukromé úložiště artefaktů?
[Veřejné úložiště artefaktů](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) poskytuje počáteční sadu softwarových balíčků, které se nejčastěji používají. Pomáhá s rychlým nasazením, aniž by bylo nutné investovat čas k reprodukování běžných vývojářských nástrojů a doplňků. Můžete si vybrat, že nasadíte vlastní privátní úložiště. Veřejné a privátní úložiště můžete používat paralelně. Můžete se také rozhodnout zakázat veřejné úložiště. Kritéria pro nasazení privátního úložiště by měla být založená na následujících otázkách a ohledech:

- Má organizace požadavky na podnikový licencovaný software jako součást nabídky DevTest Labs? Pokud je odpověď ano, je třeba vytvořit privátní úložiště.
- Vyvíjí organizace vlastní software, který poskytuje konkrétní operace, která je vyžadována jako součást celkového procesu zřizování? Pokud je odpověď ano, měli byste nasadit privátní úložiště.
- Pokud zásady správného řízení organizace vyžadují izolaci a externí úložiště nejsou pod správou přímých konfigurací v organizaci, měla by být nasazené soukromé úložiště artefaktů. V rámci tohoto procesu je možné zkopírovat a integrovat počáteční kopii veřejného úložiště s privátním úložištěm. Veřejné úložiště pak můžete zakázat, aby k němu nikdo jiný nemohly získat přístup. Tento přístup vynutí, aby všichni uživatelé v organizaci měli jenom jedno úložiště schválené organizací a minimalizovali snížení konfigurace.


### <a name="should-an-organization-plan-for-a-single-repository-or-allow-multiple-repositories"></a>Měl by se použít plán organizace pro jedno úložiště nebo povolení více úložišť?
V rámci celkové strategie správy zásad správného řízení a konfigurace vaší organizace doporučujeme, abyste používali centralizované úložiště. Když použijete více úložišť, můžou se v průběhu času stát siloy nespravovaného softwaru. S centrálním úložištěm může více týmů využívat artefakty z tohoto úložiště pro své projekty. Vynutil standardizaci, zabezpečení, snadné řízení a eliminuje duplicity úsilí. V rámci centralizované správy jsou následující akce Doporučené postupy pro dlouhodobou správu a udržitelnost:

- Přidružte Azure Repos ke stejnému Azure Active Directory tenanta, kterého předplatné Azure používá k ověřování a autorizaci.
- Vytvořte skupinu s názvem `All DevTest Labs Developers` v Azure Active Directory centrálně spravovaná. Do této skupiny by měl být umístěn jakýkoli vývojář, který přispívá ke vývojům artefaktů.
- Stejnou skupinu Azure Active Directory lze použít k poskytnutí přístupu k úložišti Azure Repos a testovacímu prostředí.
- V Azure Repos by se měly použít větvení nebo rozvětvení do samostatného cloudového úložiště z primárního provozního úložiště. Obsah se přidá do hlavní větve s žádostí o přijetí změn po správné revizi kódu. Jakmile recenzent kódu schválí změnu, vývojář zájemce, který zodpovídá za údržbu hlavní větve, sloučí aktualizovaný kód.

## <a name="cicd-integration"></a>Integrace CI/CD

### <a name="does-devtest-labs-integrate-with-my-cicd-toolchain"></a>Integruje se DevTest Labs s CI/CD sada nástrojů?
Pokud používáte Azure DevOps, můžete k automatizaci kanálu vydání v DevTest Labs použít [rozšíření úlohy DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) . Mezi úlohy, které můžete s tímto rozšířením dělat, patří:

- Vytvořte a nasaďte virtuální počítač automaticky. Virtuální počítač můžete nakonfigurovat také pomocí nejnovějšího buildu pomocí úloh Azure File Copy nebo PowerShellu Azure DevOps Services.
- Po otestování automaticky Zachyťte stav virtuálního počítače, aby se reprodukovala chyba na stejném virtuálním počítači pro další šetření.
- Odstraňte virtuální počítač na konci kanálu vydání, pokud už ho nepotřebujete.

Následující blogové příspěvky obsahují doprovodné materiály a informace o používání rozšíření Azure DevOps Services:

- [DevTest Labs a rozšíření Azure DevOps](integrate-environments-devops-pipeline.md)
- [Nasazení nového virtuálního počítače v existujícím testovacím prostředí DevTest Labs z Azure DevOps Services](https://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
- [Používání služby Azure DevOps Services Release Management pro kontinuální nasazování do DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)

Pro jiné průběžné integrace (CI)/continuous delivery (CD) sady nástrojů můžete dosáhnout stejných scénářů nasazením [Azure Resource Manager šablon](https://azure.microsoft.com/resources/templates/) pomocí [rutin Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) a sady [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/). Pro integraci s sada nástrojů můžete také použít [rozhraní REST API pro DevTest Labs](https://aka.ms/dtlrestapis) .

## <a name="networking"></a>Sítě

### <a name="when-should-i-create-a-new-virtual-network-for-my-devtest-labs-environment-vs-using-an-existing-virtual-network"></a>Kdy je vhodné vytvořit novou virtuální síť pro prostředí DevTest Labs a použít stávající virtuální síť?
Pokud vaše virtuální počítače potřebují pracovat se stávající infrastrukturou, zvažte použití existující virtuální sítě v prostředí DevTest Labs. Pokud používáte ExpressRoute, možná budete chtít minimalizovat počet virtuálních sítí nebo podsítí, abyste nefragmentoval adresní prostor IP adres, který se přiřadí k použití v předplatných.

Zvažte možnost použít také vzor partnerského vztahu virtuální sítě ([hvězdicové model](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)). Tento přístup umožňuje komunikaci mezi virtuálními sítěmi a podsítí v rámci předplatných. V opačném případě by každé prostředí DevTest Labs mohlo mít vlastní virtuální síť.

Existují [omezení](../azure-resource-manager/management/azure-subscription-service-limits.md) počtu virtuálních sítí na jedno předplatné. Výchozí hodnota je 50, i když tento limit lze vystoupl na 100.

### <a name="when-should-i-use-a-shared-ip-vs-public-ip-vs-private-ip"></a>Kdy mám použít sdílenou IP adresu a veřejnou IP adresu a soukromou IP adresu?

Pokud používáte síť Site-to-Site VPN nebo Express Route, zvažte použití privátních IP adres, aby vaše počítače byly přístupné prostřednictvím interní sítě a nepřístupné přes veřejný Internet.

> [!NOTE]
> Vlastníci testovacího prostředí můžou tyto zásady podsítě změnit, aby se zajistilo, že nikdo nechtěně nevytvoří veřejné IP adresy pro své virtuální počítače Vlastník předplatného by měl vytvořit zásady předplatného, které brání v vytváření veřejných IP adres.

Při použití sdílených veřejných IP adres sdílí virtuální počítače v testovacím prostředí veřejnou IP adresu. Tento přístup může být užitečný v případě, že se potřebujete vyhnout porušení limitů u veřejných IP adres daného předplatného.

### <a name="how-do-i-ensure-that-development-and-test-virtual-machines-are-unable-to-reach-the-public-internet-are-there-any-recommended-patterns-to-set-up-network-configuration"></a>Návody zajistěte, aby vývojové a testovací virtuální počítače nedokázaly získat přístup k veřejnému Internetu? Existují nějaké Doporučené vzory pro nastavení konfigurace sítě?

Ano. K dispozici jsou dvě aspekty, které byste měli zvážit – příchozí a odchozí provoz.

- **Příchozí provoz** – Pokud virtuální počítač nemá veřejnou IP adresu, nepůjde k němu získat Internet. Běžným přístupem je zajistit, že jsou nastavené zásady na úrovni předplatného, takže žádný uživatel nemůže vytvořit veřejnou IP adresu.
- **Odchozí provoz** – Pokud chcete zabránit tomu, aby virtuální počítače přistupovaly k veřejnému Internetu a vynutily provoz přes podnikovou bránu firewall, můžete směrovat provoz místně přes Express Route nebo VPN pomocí vynuceného směrování.

> [!NOTE]
> Pokud máte proxy server, která blokuje provoz bez nastavení proxy serveru, nezapomeňte přidat výjimky do účtu úložiště artefaktu testovacího prostředí.

Skupiny zabezpečení sítě můžete použít také pro virtuální počítače nebo podsítě. Tento krok přidá další vrstvu ochrany pro povolení/blokování provozu.

## <a name="troubleshooting"></a>Řešení potíží

### <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>Proč se moje existující virtuální síť neukládá správně?
Jednou z možností je, že název virtuální sítě obsahuje tečky. Pokud ano, zkuste odebrat období nebo je nahraďte pomlčkami. Pak zkuste znovu uložit virtuální síť.

### <a name="why-do-i-get-a-parent-resource-not-found-error-when-i-provision-a-vm-from-powershell"></a>Proč se při zřizování virtuálního počítače z PowerShellu zobrazí chyba "nadřazený prostředek nebyl nalezen"?
Pokud je jeden prostředek nadřazený jinému prostředku, musí před vytvořením podřízeného prostředku existovat nadřazený prostředek. Pokud nadřazený prostředek neexistuje, zobrazí se zpráva **ParentResourceNotFound** . Pokud nezadáte závislost na nadřazeném prostředku, může se podřízený prostředek nasadit před nadřazeným objektem.

Virtuální počítače jsou podřízené prostředky v rámci testovacího prostředí ve skupině prostředků. Když použijete šablony Správce prostředků k nasazení virtuálních počítačů pomocí prostředí PowerShell, název skupiny prostředků zadaný ve skriptu PowerShellu by měl být název skupiny prostředků testovacího prostředí. Další informace najdete v tématu [řešení běžných chyb při nasazení Azure](../azure-resource-manager/templates/common-deployment-errors.md).

### <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>Kde najdu Další informace o chybě, pokud se nasazení virtuálního počítače nepodaří?
Chyby nasazení virtuálních počítačů jsou zachyceny v protokolech aktivit. Protokoly aktivit virtuálních počítačů v testovacím prostředí najdete v části **protokoly auditu** nebo **Diagnostika virtuálního počítače** v nabídce prostředků na stránce virtuálního počítače testovacího prostředí (stránka se zobrazí po výběru virtuálního počítače ze seznamu Moje virtuální počítače).

V některých případech dojde k chybě nasazení před zahájením nasazení virtuálního počítače. Příkladem je překročení limitu předplatného pro prostředek, který byl vytvořen s virtuálním počítačem. V tomto případě jsou podrobnosti o chybě zachyceny v protokolech aktivit na úrovni testovacího prostředí. Protokoly aktivit se nacházejí v dolní části nastavení **Konfigurace a zásady** . Další informace o používání protokolů aktivit v Azure najdete v tématu [zobrazení protokolů aktivit pro audit akcí u prostředků](../azure-resource-manager/management/view-activity-logs.md).

### <a name="why-do-i-get-location-is-not-available-for-resource-type-error-when-trying-to-create-a-lab"></a>Proč se při pokusu o vytvoření testovacího prostředí zobrazí chyba "umístění není k dispozici pro typ prostředku"?
Při pokusu o vytvoření testovacího prostředí se může zobrazit chybová zpráva podobná následující:

```
The provided location 'australiacentral' is not available for resource type 'Microsoft.KeyVault/vaults'. List of available regions for the resource type is 'northcentralus,eastus,northeurope,westeurope,eastasia,southeastasia,eastus2,centralus,southcentralus,westus,japaneast,japanwest,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia,canadacentral,canadaeast,uksouth,ukwest,westcentralus,westus2,koreacentral,koreasouth,francecentral,southafricanorth
```

Tuto chybu můžete vyřešit provedením jednoho z následujících kroků:

#### <a name="option-1"></a>Možnost 1
Ověřte dostupnost typu prostředku v oblastech Azure na stránce [produkty k dispozici podle oblasti](https://azure.microsoft.com/global-infrastructure/services/) . Pokud typ prostředku není v určité oblasti dostupný, DevTest Labs nepodporuje vytvoření testovacího prostředí v této oblasti. Při vytváření testovacího prostředí vyberte jinou oblast.

#### <a name="option-2"></a>Možnost 2
Pokud je ve vaší oblasti dostupný typ prostředku, ověřte, jestli je ve vašem předplatném zaregistrovaný. Lze ji provést na úrovni vlastníka předplatného, jak je uvedeno v [tomto článku](../azure-resource-manager/management/resource-providers-and-types.md).