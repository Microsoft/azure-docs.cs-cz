---
title: Přehled zásad Azure
description: Azure Policy je služba v Azure, pomocí které vytváříte, přiřazujete a spravujete definice zásad ve svém prostředí Azure.
ms.date: 01/14/2021
ms.topic: overview
ms.openlocfilehash: df430586af2e701ec2881f6ea760095fd2ca79d0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98220724"
---
# <a name="what-is-azure-policy"></a>Co je Azure Policy?

Služba Azure Policy pomáhá vynutit standardy organizace a vyhodnotit dodržování předpisů s podporou škálování. Prostřednictvím řídicího panelu pro dodržování předpisů poskytuje agregované zobrazení pro vyhodnocení celkového stavu prostředí s možností přechodu k podrobnostem podle prostředků a členitosti podle zásad. Také pomáhá zajistit, aby byly prostředky v souladu s hromadnou nápravou pro stávající prostředky a automatickou nápravu pro nové prostředky.

Běžné případy použití pro Azure Policy zahrnují implementaci zásad správného řízení pro konzistenci prostředků, dodržování legislativních předpisů, zabezpečení, náklady a správu. Definice zásad pro tyto běžné případy použití jsou už v prostředí Azure dostupné jako předdefinované, které vám pomůžou začít.

Všechna Azure Policyová data a objekty jsou v klidovém stavu šifrované. Další informace najdete v tématu [šifrování dat Azure v klidovém umístění](../../security/fundamentals/encryption-atrest.md).

## <a name="overview"></a>Přehled

Azure Policy vyhodnocuje prostředky v Azure porovnáním vlastností těchto prostředků s obchodními pravidly. Tato obchodní pravidla, která jsou popsaná ve [formátu JSON](./concepts/definition-structure.md), se označují jako [definice zásad](#policy-definition). Pro zjednodušení správy lze seskupit několik obchodních pravidel, aby bylo možné vytvořit [iniciativu zásad](#initiative-definition) (někdy se mu říká _policySet_). Po založení obchodních pravidel se definice nebo podnět zásady [přiřadí](#assignments) k jakémukoli oboru prostředků, které Azure podporuje, jako jsou [skupiny pro správu](../management-groups/overview.md), předplatná, [skupiny prostředků](../../azure-resource-manager/management/overview.md#resource-groups)nebo jednotlivé prostředky. Přiřazení se vztahuje na všechny prostředky v rámci [rozsahu správce prostředků](../../azure-resource-manager/management/overview.md#understand-scope) tohoto přiřazení. Podobory lze v případě potřeby vyloučit. Další informace najdete v tématu věnovaném [oboru v Azure Policy](./concepts/scope.md).

Azure Policy používá [formát JSON](./concepts/definition-structure.md) k vytvoření logiky, kterou vyzkoušení používá k určení, jestli je prostředek kompatibilní, nebo ne. Definice zahrnují metadata a pravidlo zásad. Definované pravidlo může používat funkce, parametry, logické operátory, podmínky a [aliasy](./concepts/definition-structure.md#aliases) vlastností, aby odpovídaly přesně požadovanému scénáři. Pravidlo zásad určuje, které prostředky v oboru přiřazení se vyhodnotí.

### <a name="understand-evaluation-outcomes"></a>Vysvětlení výsledků hodnocení

Prostředky se vyhodnocují v určitých časech během životního cyklu prostředků, životního cyklu přiřazení zásad a pravidelného průběžného vyhodnocení dodržování předpisů. Níže jsou uvedené časy nebo události, které způsobují vyhodnocování prostředku:

- Prostředek se vytvoří, aktualizuje nebo odstraní v oboru s přiřazením zásady.
- Zásada nebo iniciativa je nově přiřazena k oboru.
- Zásada nebo iniciativa, která je již přiřazena k oboru, je aktualizována.
- Během standardního cyklu hodnocení dodržování předpisů, ke kterému dochází každých 24 hodin.

Podrobné informace o tom, kdy a jak probíhá vyhodnocení zásad, najdete v tématu [triggery vyhodnocení](./how-to/get-compliance-data.md#evaluation-triggers).

### <a name="control-the-response-to-an-evaluation"></a>Řízení odpovědi na vyhodnocení

Obchodní pravidla pro zpracování nevyhovujících prostředků se mezi organizacemi značně liší. Příklady, jak organizace chce, aby platforma reagovala na nekompatibilní prostředek, zahrnuje:

- Odepřít změnu prostředku
- Zaznamenat změnu prostředku
- Změna prostředku před změnou
- Úprava prostředku po změně
- Nasazení souvisejících odpovídajících prostředků

Azure Policy zpřístupňuje každou z těchto obchodních odpovědí prostřednictvím použití [efektů](./concepts/effects.md). Účinky se nastavují v části **pravidla zásad** v [definici zásady](./concepts/definition-structure.md).

### <a name="remediate-non-compliant-resources"></a>Oprava prostředků, které nevyhovují předpisům

I když tyto efekty primárně ovlivňují prostředek při vytvoření nebo aktualizaci prostředku, Azure Policy také podporuje práci s existujícími nekompatibilními prostředky, aniž by bylo potřeba měnit tento prostředek. Další informace o tom, jak existující prostředky vyhovují, najdete v tématu [Oprava Resources](./how-to/remediate-resources.md).

### <a name="video-overview"></a>Přehled videí

Následující přehled služby Azure Policy se týká sestavení 2018. Pokud chcete stáhnout snímky nebo video, [Projděte si téma Správa prostředí Azure prostřednictvím Azure Policy](https://channel9.msdn.com/events/Build/2018/THR2030) na webu Channel 9.

> [!VIDEO https://www.youtube.com/embed/dxMaYF2GB7o]

## <a name="getting-started"></a>Začínáme

### <a name="azure-policy-and-azure-rbac"></a>Azure Policy a Azure RBAC

Mezi Azure Policy a řízením přístupu na základě role Azure (Azure RBAC) existuje několik klíčových rozdílů. Azure Policy vyhodnocuje stav kontrolou vlastností u prostředků, které jsou zastoupeny v Správce prostředků a vlastnostech některých poskytovatelů prostředků. Azure Policy neomezuje akce (označují se také jako _operace_). Azure Policy zajistí, aby byl stav prostředku v souladu s vašimi obchodními pravidly bez obav, kdo provedl změnu nebo kdo má oprávnění provést změnu.

Azure RBAC se zaměřuje na správu uživatelských [akcí](../../role-based-access-control/resource-provider-operations.md) v různých oborech. Pokud je potřeba řídit akci, je to správný nástroj pro použití Azure RBAC. I v případě, že má jednotlivec přístup k provedení určité akce, pokud je výsledkem nekompatibilní prostředek, Azure Policy stále zablokovat vytvoření nebo aktualizaci.

Kombinace Azure RBAC a Azure Policy poskytuje kompletní řízení oboru v Azure.

### <a name="azure-rbac-permissions-in-azure-policy"></a>Oprávnění Azure RBAC v Azure Policy

Služba Azure Policy má několik oprávnění, která se označují jako operace, ve dvou poskytovatelích prostředků:

- [Microsoft.Authorization](../../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft. PolicyInsights](../../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

Řada předdefinovaných rolí uděluje oprávnění k prostředkům Azure Policy. Role **Přispěvatel zásad prostředků** zahrnuje většinu operací Azure Policy. **Vlastník** má plná práva. Všichni **přispěvatelé** a **čtenáři** mají přístup ke všem operacím _čtení_ Azure Policy. **Přispěvatel** může aktivovat nápravu prostředků, ale nemůže _vytvořit_ definice nebo přiřazení. **Správce přístupu uživatele** je nezbytný k udělení oprávnění ke spravované identitě v **deployIfNotExists** nebo k **úpravám** potřebných přiřazení.

Pokud žádná z předdefinovaných rolí nemá požadovaná oprávnění, vytvořte [vlastní roli](../../role-based-access-control/custom-roles.md).

> [!NOTE]
> Spravovaná identita přiřazení zásady **deployIfNotExists** nebo **Modify** potřebuje dostatečná oprávnění k vytvoření nebo aktualizaci prostředků zacílené. Další informace najdete v tématu [Konfigurace definic zásad pro nápravu](./how-to/remediate-resources.md#configure-policy-definition).

### <a name="resources-covered-by-azure-policy"></a>Prostředky, na které se vztahuje Azure Policy

Azure Policy vyhodnocuje všechny prostředky Azure na úrovni předplatného, včetně prostředků s podporou ARC. Pro určité poskytovatele prostředků, jako je například [Konfigurace hostů](./concepts/guest-configuration.md), [Služba Azure Kubernetes](../../aks/intro-kubernetes.md)a [Azure Key Vault](../../key-vault/general/overview.md), je k dispozici hlubší integrace pro správu nastavení a objektů. Další informace najdete v tématu [režimy poskytovatele prostředků](./concepts/definition-structure.md).

### <a name="recommendations-for-managing-policies"></a>Doporučení pro správu zásad

Tady je několik ukazatelů a tipů, které byste měli mít na paměti:

- Zahajte s použitím efektu auditu místo zakazování, abyste mohli sledovat dopad definice zásad na prostředky ve vašem prostředí. Pokud již máte skripty pro automatické škálování vašich aplikací, může nastavení efektu odepření bránit na to, aby tyto úlohy Automation byly již zavedeny.

- Při vytváření definic a přiřazení Vezměte v úvahu organizační hierarchie. Doporučujeme vytvořit definice na vyšších úrovních, například na úrovni skupiny pro správu nebo předplatného. Pak vytvořte přiřazení na další podřízené úrovni. Pokud vytvoříte definici ve skupině pro správu, může být přiřazení vymezeno níže v rámci předplatného nebo skupiny prostředků v této skupině pro správu.

- Definice iniciativ doporučujeme vytvářet a přiřazovat i pro jednu definici zásad.
  Například máte definici zásad _policyDefA_ a vytvoříte ji v části iniciativa definice _initiativeDefC_. Pokud později vytvoříte další definici zásad pro _policyDefB_ s cíli podobnými _policyDefA_, můžete ho přidat pod _initiativeDefC_ a sledovat společně.

- Jakmile vytvoříte přiřazení iniciativy, definice zásad přidané k iniciativě se také stanou součástí přiřazení v této iniciativě.

- Když se vyhodnotí přiřazení iniciativy, vyhodnotí se taky všechny zásady v iniciativě.
  Pokud potřebujete zásadu vyhodnotit jednotlivě, je lepší ji v iniciativě Nezahrnovat.

## <a name="azure-policy-objects"></a>Azure Policy objekty

### <a name="policy-definition"></a>Definice zásady

Postup vytváření a implementace zásady v Azure Policy začíná vytvořením definice zásady. Každá definice zásady se vynucuje za určitých podmínek. A má definovaný účinek, který se provede, pokud jsou splněny podmínky.

V Azure Policy nabízíme několik předdefinovaných zásad, které jsou ve výchozím nastavení dostupné. Například:

- **Povolené skladové položky účtu úložiště** (odepřít): Určuje, jestli se nasazený účet úložiště nachází v rámci sady velikostí SKU. Jeho účinkem je odmítnutí všech účtů úložiště, které nedodržují sadu definovaných velikostí SKU.
- **Povolený typ prostředku** (odepřít): definuje typy prostředků, které můžete nasadit. Jeho účelem je Odepřít všechny prostředky, které nejsou součástí tohoto definovaného seznamu.
- **Povolená umístění** (odepřít): omezuje dostupná umístění pro nové prostředky. Účinkem je vynucení vašich požadavků na geografické dodržování předpisů.
- **Povolené SKU virtuálních počítačů** (odepřít): Určuje sadu SKU virtuálních počítačů, které můžete nasadit.
- **Přidat značku do prostředků** (Upravit): použije požadovanou značku a její výchozí hodnotu, pokud není zadána v žádosti o nasazení.
- **Nepovolené typy prostředků** (odepřít): zabrání v nasazení seznamu typů prostředků.

Pokud chcete implementovat tyto definice zásad (předdefinované i vlastní definice), budete je muset přiřadit. Jakékoli z těchto zásad můžeme přiřadit prostřednictvím webu Azure Portal, PowerShellu nebo Azure CLI.

Vyhodnocení zásad probíhá s několika různými akcemi, jako jsou přiřazení zásad nebo aktualizace zásad. Úplný seznam najdete v tématu [triggery vyhodnocení zásad](./how-to/get-compliance-data.md#evaluation-triggers).

Další informace o strukturách definic zásad najdete v článku [Struktura definic zásad](./concepts/definition-structure.md).

Parametry zásad pomáhají zjednodušit správu zásad tím, že snižují počet definic zásad, které musíte vytvářet. Parametry můžete definovat při vytváření definice zásady a tím ji více zobecnit. Následně můžete tuto definici zásady použít opakovaně pro různé scénáře. Provedete to předáváním různých hodnot při přiřazování této definice zásady. Například můžete pro každé předplatné zadat jednu sadu umístění.

Parametry jsou definovány při vytváření definice zásady. Při definování dostane parametr název a volitelně i hodnotu. Pro zásadu můžete například definovat parametr s názvem _location_ (umístění). Následně mu můžete při přiřazování zásady předávat různé hodnoty, například _EastUS_ nebo _WestUS_.

Další informace o parametrech zásad najdete v tématu [Struktura definice – parametry](./concepts/definition-structure.md#parameters).

### <a name="initiative-definition"></a>Definice iniciativy

Definice iniciativy je kolekce definic zásad, které jsou přizpůsobené pro dosažení jednoho zastřešujícího cíle. Definice iniciativ zjednodušují správu a přiřazování definic zásad. Toto zjednodušení spočívá v seskupování sad zásad do jedné položky. Mohli byste například vytvořit iniciativu s názvem **Povolení monitorování v Azure Security Center** s cílem monitorovat všechna dostupná doporučení zabezpečení v Azure Security Center.

> [!NOTE]
> Sada SDK, jako je Azure CLI a Azure PowerShell, používá k odkazování na iniciativy vlastnosti a parametry s názvem **PolicySet** .

V rámci této iniciativy byste měli například tyto definice zásad:

- **Monitorování nešifrované služby SQL Database v Security Center** – pro monitorování nešifrovaných databází a serverů SQL.
- **Monitorování ohrožení zabezpečení operačního systému v Security Center** – pro monitorovací servery, které nevyhovují nakonfigurovanému směrnému plánu.
- **Monitorování chybějící služby Endpoint Protection v Security Center** – pro monitorování serverů bez nainstalovaného agenta služby Endpoint Protection.

Podobně jako parametry zásad pomáhají parametry iniciativ zjednodušit správu iniciativ tím, že snižují redundanci. Parametry iniciativy jsou parametry používané definicemi zásad v rámci iniciativy.

Jako příklad může posloužit scénář, ve kterém máte definici iniciativy **initiativeC** s definicemi zásad **policyA** a **policyB**, z nichž každá očekává jiný typ parametru:

| Zásady | Název parametru |Typ parametru  |Poznámka |
|---|---|---|---|
| policyA | allowedLocations | array  |Tento parametr jako hodnotu očekává seznam řetězců, protože typ parametru byl definovaný jako pole. |
| policyB | allowedSingleLocation |řetězec |Tento parametr jako hodnotu očekává jedno slovo, protože typ parametru byl definovaný jako řetězec. |

V tomto scénáři máte při definování parametrů iniciativy pro **initiativeC** tři možnosti:

- Použít parametry definic zásad v rámci této iniciativy: V tomto příkladu se _allowedLocations_ a _allowedSingleLocation_ stanou parametry iniciativy pro **initiativeC**.
- Zadat hodnoty do parametrů definic zásad v rámci této definice iniciativy. V tomto příkladu můžete zadat seznam umístění pro parametr **zásad**– **allowedLocations** a **policyB** parametr – **allowedSingleLocation**. Hodnoty můžete zadat také při přiřazování této iniciativy.
- Zadat seznam možností _hodnot_, které se můžou použít při přiřazování této iniciativy. Když přiřadíte tuto iniciativu, zděděné parametry z definic zásad v rámci této iniciativy můžou mít pouze hodnoty z tohoto zadaného seznamu.

Při vytváření možností hodnot v definici iniciativy nemůžete zadat jinou hodnotu během přiřazení iniciativy, protože není součástí seznamu.

Další informace o strukturách definic iniciativ, přezkoumání [struktury definice iniciativy](./concepts/initiative-definition-structure.md).

### <a name="assignments"></a>Přiřazení

Přiřazení je definice zásady nebo iniciativa, která se má provést v rámci určitého oboru. Tento rozsah může být v rozsahu od [skupiny pro správu](../management-groups/overview.md) k individuálnímu prostředku. Pojem _oboru_ odkazuje na všechny prostředky, skupiny prostředků, odběry nebo skupiny pro správu, ke kterým je definice přiřazena. Přiřazení jsou děděna všemi podřízenými prostředky. Tento návrh znamená, že definice použitá pro skupinu prostředků se používá taky u prostředků v této skupině prostředků. Z přiřazení však můžete podobor vyloučit.

Například v oboru předplatného můžete přiřadit definici, která zabraňuje vytváření síťových prostředků. V tomto předplatném byste mohli vyloučit skupinu prostředků, která je určená pro síťovou infrastrukturu. Pak udělíte přístup k této skupině síťových prostředků uživatelům, kterým důvěřujete vytváření síťových prostředků.

V jiném příkladu můžete chtít přiřadit definici seznamu povolených typů prostředků na úrovni skupiny pro správu. Pak přiřadíte více opravňující zásady (povolení více typů prostředků) pro podřízenou skupinu pro správu nebo dokonce přímo na předplatných. Tento příklad ale nefunguje, protože Azure Policy je explicitní systém odmítnutí. Místo toho je potřeba vyřadit podřízenou skupinu pro správu nebo předplatné z přiřazení na úrovni skupiny pro správu. Pak přiřaďte přísnější definici na úrovni podřízené skupiny pro správu nebo předplatného. Pokud některý z přiřazení povede k odepření prostředku, pak jediným způsobem, jak prostředek povolit, je změnit přiřazení odmítnutí.

Další informace o nastavení přiřazení prostřednictvím portálu najdete v tématu [vytvoření přiřazení zásady pro identifikaci prostředků, které nedodržují předpisy, v prostředí Azure](./assign-policy-portal.md). K dispozici jsou také kroky pro [PowerShell](./assign-policy-powershell.md) a [Azure CLI](./assign-policy-azurecli.md). Informace o struktuře přiřazení najdete v tématu [Struktura přiřazení](./concepts/assignment-structure.md).

## <a name="maximum-count-of-azure-policy-objects"></a>Maximální počet Azure Policy objektů

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="next-steps"></a>Další kroky

Získali jste přehled o službě Azure Policy a některých klíčových konceptech. Tady je návrh dalších kroků:

- [Zkontrolujte strukturu definic zásad](./concepts/definition-structure.md).
- [Přiřaďte definici zásady pomocí portálu](./assign-policy-portal.md).
