---
title: Přehled zásad Azure
description: Azure Policy je služba v Azure, pomocí které vytváříte, přiřazujete a spravujete definice zásad ve svém prostředí Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/06/2018
ms.topic: overview
ms.service: azure-policy
ms.openlocfilehash: 46d78ca9f82017e1a11642af6e5dcdc68e239c8a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498281"
---
# <a name="overview-of-the-azure-policy-service"></a>Přehled služby Azure Policy

Zásady správného řízení ověří, že vaše organizace může dosáhnout svých cílů, a to Díky efektivnímu a efektivnímu využití IT. Tato nutnost splňuje požadavky vytvořením jasnosti mezi obchodními cíli a projekty IT.

Potýká se vaše společnost s velkým množstvím problémů v oblasti IT, které se zdánlivě nikdy nevyřeší?
Dobré zásady správného řízení v oblasti IT zahrnují plánování iniciativ a nastavení priorit na strategické úrovni, která pomáhají překonávat problémy a předcházet jim. Tato strategická potřeba je tam, kde Azure Policy přichází.

Azure Policy je služba v Azure, která slouží k vytváření, přiřazování a správě zásad. Tyto zásady vynucují na vašich prostředcích různá pravidla a účinky, aby tyto prostředky zůstaly kompatibilní s vašimi firemními standardy a smlouvami o úrovni služeb. Azure Policy tuto potřebu splňuje, protože vyhodnocuje prostředky pro nedodržování předpisů pomocí přiřazených zásad. Například můžete mít zásadu, která ve vašem prostředí povoluje jenom určitou skladovou položku virtuálních počítačů. Po implementaci této zásady se vyhodnotí nové a stávající prostředky pro dodržování předpisů. U správného typu zásad lze stávající prostředky uvést do dodržování předpisů. Později v této dokumentaci budeme podrobnější informace o tom, jak vytvářet a implementovat zásady pomocí Azure Policy.

> [!IMPORTANT]
> Vyhodnocování dodržování předpisů ve službě Azure Policy je teď dostupné pro všechna přiřazení bez ohledu na cenovou úroveň. Pokud vaše přiřazení nezobrazují data dodržování předpisů, ujistěte se, že je předplatné zaregistrované u poskytovatele prostředků Microsoft.PolicyInsights.

[!INCLUDE [service-provider-management-toolkit](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-is-it-different-from-rbac"></a>Čím se to liší od RBAC?

Mezi Azure Policy a řízením přístupu na základě role (RBAC) existuje několik klíčových rozdílů. RBAC se zaměřuje na akce uživatelů v různých oborech. Můžete být přidáni do role přispěvatele pro skupinu prostředků, což vám umožní provádět změny v této skupině prostředků. Azure Policy se zaměřuje na vlastnosti prostředku během nasazování a už existující prostředky. Azure Policy řídí vlastnosti, jako jsou typy nebo umístění prostředků. Na rozdíl od RBAC je Azure Policy výchozím systémem povolení a explicitním odepřením.

### <a name="rbac-permissions-in-azure-policy"></a>Oprávnění RBAC ve službě Azure Policy

Služba Azure Policy má několik oprávnění, která se označují jako operace, ve dvou poskytovatelích prostředků:

- [Microsoft.Authorization](../../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft.PolicyInsights](../../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

Řada předdefinovaných rolí uděluje oprávnění k prostředkům Azure Policy. Role **Přispěvatel zásad prostředků (Preview)** zahrnuje většinu operací Azure Policy. **Vlastník** má plná práva. **Přispěvatel** i **Čtenář** můžou použít všechny operace čtení Azure Policy, ale **Přispěvatel** může taky aktivovat nápravu.

Pokud žádná z předdefinovaných rolí nemá požadovaná oprávnění, vytvořte [vlastní roli](../../role-based-access-control/custom-roles.md).

## <a name="policy-definition"></a>Definice zásady

Postup vytváření a implementace zásady v Azure Policy začíná vytvořením definice zásady. Každá definice zásady obsahuje podmínky, za kterých se vynutilo. A má definovaný účinek, který se provede, pokud jsou splněny podmínky.

V Azure Policy nabízíme několik předdefinovaných zásad, které jsou ve výchozím nastavení dostupné. Například:

- **Povolené skladové položky účtu úložiště**: Určuje, jestli se nasazený účet úložiště nachází v rámci sady velikostí SKU. Jeho účinkem je odmítnutí všech účtů úložiště, které nedodržují sadu definovaných velikostí SKU.
- **Povolený typ prostředku**: definuje typy prostředků, které můžete nasadit. Jeho účelem je Odepřít všechny prostředky, které nejsou součástí tohoto definovaného seznamu.
- **Povolená umístění**: omezuje dostupná umístění pro nové prostředky. Účinkem je vynucení vašich požadavků na geografické dodržování předpisů.
- **Povolené SKU virtuálních počítačů**: Určuje sadu SKU virtuálních počítačů, které můžete nasadit.
- **Přidat značku k prostředkům**: použije povinnou značku a její výchozí hodnotu, pokud není zadána v žádosti o nasazení.
- **Vysazení značky a její hodnoty**: vynutila požadovanou značku a její hodnotu pro prostředek.
- **Nepovolené typy prostředků**: zabrání v nasazení seznamu typů prostředků.

Pokud chcete implementovat tyto definice zásad (předdefinované i vlastní definice), budete je muset přiřadit. Jakékoli z těchto zásad můžete přiřadit prostřednictvím webu Azure Portal, PowerShellu nebo Azure CLI.

Vyhodnocení zásad probíhá s několika různými akcemi, jako jsou přiřazení zásad nebo aktualizace zásad. Úplný seznam najdete v tématu [triggery vyhodnocení zásad](./how-to/get-compliance-data.md#evaluation-triggers).

Další informace o strukturách definic zásad najdete v článku [Struktura definic zásad](./concepts/definition-structure.md).

## <a name="policy-assignment"></a>Přiřazení zásad

Přiřazení zásady je definice zásady, která byla přiřazena, aby proběhla v rámci zadaného oboru. Tento obor může sahat od [skupiny pro správu](../management-groups/overview.md) až po skupinu prostředků. Termín *obor* označuje všechny skupiny prostředků, předplatná nebo skupiny pro správu, ke kterým je definice zásady přiřazená. Všechny podřízené prostředky dědí přiřazení zásad. Tento návrh znamená, že se k prostředkům v této skupině prostředků použije taky zásada použitá pro skupinu prostředků. Z přiřazení zásad však můžete vyloučit určitý podobor.

Například v oboru předplatného můžete přiřadit zásadu, která brání vytváření síťových prostředků. V tomto předplatném byste mohli vyloučit skupinu prostředků, která je určená pro síťovou infrastrukturu. Pak udělíte přístup k této skupině síťových prostředků uživatelům, kterým důvěřujete vytváření síťových prostředků.

V jiném příkladu můžete chtít přiřadit zásady seznamu povolených typů prostředků na úrovni skupiny pro správu. A potom přiřadit mnohem mírnější zásady, které povolují více typů prostředků, u podřízené skupiny správy nebo dokonce přímo u předplatných. To ale nebude fungovat, protože zásady představují systém, kdy se explicitně zamítá. Místo toho musíte podřízenou skupinu správy nebo předplatné vyloučit z přiřazení zásad na úrovni skupiny správy. Potom přiřadíte mnohem mírnější zásady na úrovni podřízené skupiny správy nebo předplatného. Pokud má kterákoli zásada za následek odepření prostředku, pak jediným způsobem, jak prostředek povolit, je upravit zásady odepření.

Další informace o nastavování definic a přiřazení zásad prostřednictvím portálu najdete v tématu [Vytvoření přiřazení zásady pro identifikaci neodpovídajících prostředků v prostředí Azure](assign-policy-portal.md). K dispozici jsou také kroky pro [PowerShell](assign-policy-powershell.md) a [Azure CLI](assign-policy-azurecli.md).

## <a name="policy-parameters"></a>Parametry zásad

Parametry zásad pomáhají zjednodušit správu zásad tím, že snižují počet definic zásad, které musíte vytvářet. Parametry můžete definovat při vytváření definice zásady a tím ji více zobecnit. Následně můžete tuto definici zásady použít opakovaně pro různé scénáře. Provedete to předáváním různých hodnot při přiřazování této definice zásady. Například můžete pro každé předplatné zadat jednu sadu umístění.

Parametry jsou definovány při vytváření definice zásady. Je-li definován parametr, je mu přidělen název a volitelně je zadána hodnota. Pro zásadu můžete například definovat parametr s názvem *location* (umístění). Následně mu můžete při přiřazování zásady předávat různé hodnoty, například *EastUS* nebo *WestUS*.

Další informace o parametrech zásad najdete v tématu [Struktura definice – parametry](./concepts/definition-structure.md#parameters).

## <a name="initiative-definition"></a>Definice iniciativy

Definice iniciativy je kolekce definic zásad, které jsou přizpůsobené pro dosažení jednoho zastřešujícího cíle. Definice iniciativ zjednodušují správu a přiřazování definic zásad. Toto zjednodušení spočívá v seskupování sad zásad do jedné položky. Mohli byste například vytvořit iniciativu s názvem **Povolení monitorování v Azure Security Center** s cílem monitorovat všechna dostupná doporučení zabezpečení v Azure Security Center.

V rámci této iniciativy byste měli například tyto definice zásad:

- **Monitorování nešifrované služby SQL Database v Security Center** – pro monitorování nešifrovaných databází a serverů SQL.
- **Monitorování ohrožení zabezpečení operačního systému v Security Center** – pro monitorovací servery, které nevyhovují nakonfigurovanému směrnému plánu.
- **Monitorování chybějící služby Endpoint Protection v Security Center** – pro monitorování serverů bez nainstalovaného agenta služby Endpoint Protection.

## <a name="initiative-assignment"></a>Přiřazení iniciativy

Podobně jako přiřazení zásady je přiřazení iniciativy definicí iniciativy přiřazenou ke konkrétnímu oboru. Přiřazení iniciativ omezují potřebu vytváření několika definic iniciativ pro každý obor. Tento obor může také sahat od skupiny pro správu až po skupinu prostředků.

Každou iniciativu lze přiřadit k různým oborům. Jednu iniciativu lze přiřadit k **předplatnému** i k **subscriptionB**.

## <a name="initiative-parameters"></a>Parametry iniciativ

Podobně jako parametry zásad pomáhají parametry iniciativ zjednodušit správu iniciativ tím, že snižují redundanci. Parametry iniciativy jsou parametry používané definicemi zásad v rámci iniciativy.

Jako příklad může posloužit scénář, ve kterém máte definici iniciativy **initiativeC** s definicemi zásad **policyA** a **policyB**, z nichž každá očekává jiný typ parametru:

| Zásady | Název parametru |Typ parametru  |Poznámka |
|---|---|---|---|
| policyA | allowedLocations | pole  |Tento parametr jako hodnotu očekává seznam řetězců, protože typ parametru byl definovaný jako pole. |
| policyB | allowedSingleLocation |string |Tento parametr jako hodnotu očekává jedno slovo, protože typ parametru byl definovaný jako řetězec. |

V tomto scénáři máte při definování parametrů iniciativy pro **initiativeC** tři možnosti:

- Použít parametry definic zásad v rámci této iniciativy: V tomto příkladu se *allowedLocations* a *allowedSingleLocation* stanou parametry iniciativy pro **initiativeC**.
- Zadat hodnoty do parametrů definic zásad v rámci této definice iniciativy. V tomto příkladu můžete zadat seznam umístění pro **parametr zásad – allowedLocations** a **PolicyB parametr – allowedSingleLocation**. Hodnoty můžete zadat také při přiřazování této iniciativy.
- Zadat seznam možností *hodnot*, které se můžou použít při přiřazování této iniciativy. Když přiřadíte tuto iniciativu, zděděné parametry z definic zásad v rámci této iniciativy můžou mít pouze hodnoty z tohoto zadaného seznamu.

Při vytváření možností hodnot v definici iniciativy nemůžete zadat jinou hodnotu během přiřazení iniciativy, protože není součástí seznamu.

## <a name="maximum-count-of-azure-policy-objects"></a>Maximální počet Azure Policy objektů

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="recommendations-for-managing-policies"></a>Doporučení pro správu zásad

Tady je několik ukazatelů a tipů, které byste měli mít na paměti:

- Zahajte s použitím efektu auditu místo zakazování, abyste mohli sledovat dopad definice zásad na prostředky ve vašem prostředí. Pokud již máte skripty pro automatické škálování vašich aplikací, může nastavení efektu odepření bránit na to, aby tyto úlohy Automation byly již zavedeny.

- Při vytváření definic a přiřazení Vezměte v úvahu organizační hierarchie. Doporučujeme vytvořit definice na vyšších úrovních, například na úrovni skupiny pro správu nebo předplatného. Pak vytvořte přiřazení na další podřízené úrovni. Pokud vytvoříte definici ve skupině pro správu, může být přiřazení vymezeno níže v rámci předplatného nebo skupiny prostředků v této skupině pro správu.

- Definice iniciativ doporučujeme vytvářet a přiřazovat i pro jednu definici zásad.
Například máte definici zásad *policyDefA* a vytvoříte ji v části iniciativa definice *initiativeDefC*. Pokud později vytvoříte další definici zásad pro *policyDefB* s cíli podobnými *policyDefA*, můžete ho přidat pod *initiativeDefC* a sledovat společně.

- Po vytvoření přiřazení iniciativy se definice zásad přidané do iniciativy stanou také součástí těchto přiřazení iniciativ.

- Když se vyhodnotí přiřazení iniciativy, vyhodnotí se taky všechny zásady v iniciativě. Pokud potřebujete zásadu vyhodnotit jednotlivě, je lepší ji v iniciativě Nezahrnovat.

## <a name="video-overview"></a>Video – přehled

Následující přehled služby Azure Policy se týká sestavení 2018. Pokud chcete stáhnout snímky nebo video, [Projděte si téma Správa prostředí Azure prostřednictvím Azure Policy](https://channel9.msdn.com/events/Build/2018/THR2030) na webu Channel 9.

> [!VIDEO https://www.youtube.com/embed/dxMaYF2GB7o]

## <a name="next-steps"></a>Další kroky

Získali jste přehled o službě Azure Policy a některých klíčových konceptech. Tady je návrh dalších kroků:

- [Přiřaďte definici zásady pomocí portálu](assign-policy-portal.md).
- [Přiřaďte definici zásady pomocí Azure CLI](assign-policy-azurecli.md).
- [Přiřaďte definici zásady pomocí PowerShellu](assign-policy-powershell.md).
- Seznamte se s tím, co skupina pro správu [organizuje vaše prostředky pomocí skupin pro správu Azure](..//management-groups/overview.md).
- V Azure Policy na Channel 9 [se řídí prostředí Azure](https://channel9.msdn.com/events/Build/2018/THR2030) .
