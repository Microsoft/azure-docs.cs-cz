---
title: Přehled zásad Azure
description: Azure Policy je služba v Azure, pomocí které vytváříte, přiřazujete a spravujete definice zásad ve svém prostředí Azure.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/07/2018
ms.topic: overview
ms.service: azure-policy
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 3f14c547c072e012d44350706f08548208fbb544
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="what-is-azure-policy"></a>Co je Azure Policy?

Zásady správného řízení v oblasti IT vytvářejí jasný vztah mezi obchodními cíli a projekty IT. Dobré zásady správného řízení v oblasti IT zahrnují plánování iniciativ a nastavení priorit na strategické úrovni. Potýká se vaše společnost s velkým množstvím problémů v oblasti IT, které se zdánlivě nikdy nevyřeší? Implementace zásad vám pomůže tyto problémy lépe zvládnout a předcházet jim. Tuto implementaci zásad řeší Azure Policy.

Azure Policy je služba v Azure, pomocí které vytváříte, přiřazujete a spravujete definice zásad. Definice zásad u vašich prostředků vynucují různá pravidla a účinky, aby tyto prostředky i nadále odpovídaly vašim firemním standardům a smlouvám o úrovni služeb. Azure Policy provádí hodnocení vašich prostředků, mezi kterými vyhledává ty, které neodpovídají vašim definicím zásad. Například můžete mít zásadu pro povolení pouze určitého typu virtuálních počítačů. Další vyžaduje, aby všechny prostředky měly konkrétní značku. Tyto zásady se následně vyhodnocují při vytváření a aktualizaci prostředků.

> [!IMPORTANT]
> Vyhodnocování dodržování předpisů ve službě Azure Policy je teď dostupné pro všechna přiřazení bez ohledu na cenovou úroveň. Pokud vaše přiřazení nezobrazují data dodržování předpisů, ujistěte se, že je předplatné zaregistrované u poskytovatele prostředků Microsoft.PolicyInsights.

## <a name="how-is-it-different-from-rbac"></a>Čím se to liší od RBAC?

Mezi zásadami a řízením přístupu na základě role (RBAC) existuje několik klíčových rozdílů. RBAC se zaměřuje na akce uživatelů v různých oborech. Například můžete být přidáni do role přispěvatele pro skupinu prostředků v požadovaném oboru. Tato role vám umožňuje provádět změny této skupiny prostředků. Zásady se zaměřují na vlastnosti prostředků během nasazování a vlastnosti již existujících prostředků. Prostřednictvím zásad můžete například řídit, které typy prostředků je možné zřizovat. Nebo můžete omezit oblasti, ve kterých se prostředky můžou zřizovat. Na rozdíl od RBAC jsou zásady systémem, kdy je ve výchozím nastavení vše povoleno a explicitně se zamítá.

Pokud chcete používat zásady, musíte být ověřeni pomocí RBAC. Konkrétně váš účet potřebuje:

- Oprávnění `Microsoft.Authorization/policydefinitions/write` pro definování zásady.
- Oprávnění `Microsoft.Authorization/policyassignments/write` pro přiřazení zásady.
- Oprávnění `Microsoft.Authorization/policySetDefinitions/write` pro definování iniciativy.
- Oprávnění `Microsoft.Authorization/policyassignments/write` pro přiřazení iniciativy.

Tato oprávnění nejsou součástí role **Přispěvatel**.

## <a name="policy-definition"></a>Definice zásady

Každá definice zásady obsahuje podmínky, za kterých se vynucuje. Obsahuje také doprovodný účinek, který se provede při splnění podmínek.

Ve službě Azure Policy nabízíme několik integrovaných zásad, které máte k dispozici ve výchozím nastavení. Příklad:

- **Vyžadovat SQL Server 12.0:** Tato definice zásady obsahuje podmínky/pravidla pro zajištění, aby všechny servery SQL byly verze 12.0. Účinkem je zamítnutí všech serverů, které nesplňují tato kritéria.
- **Povolené skladové položky účtu úložiště:** Tato definice zásady obsahuje sadu podmínek/pravidel určujících, jestli se nasazovaný účet úložiště nachází v sadě velikostí skladových položek. Účinkem je zamítnutí všech serverů, které neodpovídají sadě definovaných velikostí skladových položek.
- **Povolený typ prostředku:** Tato definice zásady obsahuje sadu podmínek/pravidel určujících typy prostředků, které může vaše organizace nasazovat. Účinkem je zamítnutí všech prostředků, které se nenachází na tomto definovaném seznamu.
- **Povolená umístění:** Tato zásada umožňuje omezit umístění, která může vaše organizace zadat při nasazování prostředků. Účinkem je vynucení vašich požadavků na geografické dodržování předpisů.
- **Povolené skladové položky virtuálních počítačů:** Tato zásada umožňuje zadat sadu skladových položek virtuálních počítačů, které může vaše organizace nasazovat.
- **Použít značku a její výchozí hodnotu:** Tato zásada použije požadovanou značku a její výchozí hodnotu, pokud ji nezadá uživatel.
- **Vynucovat použití značky a její hodnoty:** Tato zásada vynucuje použití požadované značky a její hodnoty pro prostředek.
- **Nepovolené typy prostředků:** Tato zásada umožňuje zadat typy prostředků, které vaše organizace nemůže nasazovat.

Jakékoli z těchto zásad můžete přiřadit prostřednictvím webu Azure Portal, PowerShellu nebo Azure CLI. Po změně definice zásad se budou zásady opakovaně vyhodnocovat přibližně jednou za hodinu.

Další informace o strukturách definic zásad najdete v článku [Struktura definic zásad](policy-definition.md).

## <a name="policy-assignment"></a>Přiřazení zásad

Přiřazení zásady je definice zásady, která byla přiřazena, aby proběhla v rámci zadaného oboru. Tento obor může sahat od skupiny pro správu až po skupinu prostředků. Termín *obor* označuje všechny skupiny prostředků, předplatná nebo skupiny pro správu, ke kterým je definice zásady přiřazená. Všechny podřízené prostředky dědí přiřazení zásad. Takže pokud se použije zásada na skupinu prostředků, použije se na všechny prostředky v této skupině prostředků. Z přiřazení zásad však můžete vyloučit určitý podobor.

Například v oboru předplatného můžete přiřadit zásadu, která brání vytváření síťových prostředků. V rámci tohoto předplatného však vyloučíte jednu skupinu prostředků, která je určená pro síťovou infrastrukturu. Přístup k této skupině síťových prostředků udělíte uživatelům, kterým důvěřujete s vytvářením síťových prostředků.

V jiné situaci můžete například chtít přiřadit zásady seznamu povolených typů prostředků na úrovni skupiny správy. A potom přiřadit mnohem mírnější zásady, které povolují více typů prostředků, u podřízené skupiny správy nebo dokonce přímo u předplatných. To ale nebude fungovat, protože zásady představují systém, kdy se explicitně zamítá. Místo toho musíte podřízenou skupinu správy nebo předplatné vyloučit z přiřazení zásad na úrovni skupiny správy. Potom přiřadíte mnohem mírnější zásady na úrovni podřízené skupiny správy nebo předplatného. Stručně řečeno, pokud je výsledkem nějaké zásady zamítnutí prostředku, je jediným způsobem, jak prostředek povolit, změna zásad zamítnutí.

Další informace o nastavování definic a přiřazení zásad najdete v tématu [Vytvoření přiřazení zásady pro identifikaci neodpovídajících prostředků v prostředí Azure](assign-policy-definition.md).

## <a name="policy-parameters"></a>Parametry zásad

Parametry zásad pomáhají zjednodušit správu zásad tím, že snižují počet definic zásad, které musíte vytvářet. Parametry můžete definovat při vytváření definice zásady a tím ji více zobecnit. Následně můžete tuto definici zásady použít opakovaně pro různé scénáře. Provedete to předáváním různých hodnot při přiřazování této definice zásady. Například můžete pro každé předplatné zadat jednu sadu umístění.

Parametry se definují/vytvářejí při vytváření definice zásady. Při definování získá parametr název a volitelně i hodnotu. Pro zásadu můžete například definovat parametr s názvem *location* (umístění). Následně mu můžete při přiřazování zásady předávat různé hodnoty, například *EastUS* nebo *WestUS*.

Další informace o parametrech zásad najdete v tématu [Přehled zásad prostředků – Parametry](policy-definition.md#parameters).

## <a name="initiative-definition"></a>Definice iniciativy

Definice iniciativy je kolekce definic zásad, které jsou přizpůsobené pro dosažení jednoho zastřešujícího cíle. Definice iniciativ zjednodušují správu a přiřazování definic zásad. Toto zjednodušení spočívá v seskupování sad zásad do jedné položky. Mohli byste například vytvořit iniciativu s názvem **Povolení monitorování v Azure Security Center** s cílem monitorovat všechna dostupná doporučení zabezpečení v Azure Security Center.

V rámci této iniciativy byste měli například tyto definice zásad:

- **Monitorování nešifrované služby SQL Database v Security Center** – pro monitorování nešifrovaných databází a serverů SQL.
- **Monitorování ohrožení zabezpečení operačního systému v Security Center** – pro monitorování serverů, které nevyhovují nakonfigurovaným standardním hodnotám.
- **Monitorování chybějící služby Endpoint Protection v Security Center** – pro monitorování serverů bez nainstalovaného agenta služby Endpoint Protection.

## <a name="initiative-assignment"></a>Přiřazení iniciativy

Podobně jako přiřazení zásady je přiřazení iniciativy definicí iniciativy přiřazenou ke konkrétnímu oboru. Přiřazení iniciativ omezují potřebu vytváření několika definic iniciativ pro každý obor. Tento obor může také sahat od skupiny pro správu až po skupinu prostředků.

Iniciativu **Povolení monitorování v Azure Security Center** z předchozího příkladu je možné přiřadit k různým oborům. Jedno přiřazení se může přiřadit například k předplatnému **subscriptionA**. Další se může přiřadit k předplatnému **subscriptionB**.

## <a name="initiative-parameters"></a>Parametry iniciativ

Podobně jako parametry zásad pomáhají parametry iniciativ zjednodušit správu iniciativ tím, že snižují redundanci. Parametry iniciativ jsou v podstatě seznamem parametrů používaných definicemi zásad v rámci příslušné iniciativy.

Vezměte si například scénář, kdy máte definici iniciativy **initiativeC** se dvěma definicemi zásad. Každá definice zásady má definovaný jeden parametr:

| Zásada | Název parametru |Typ parametru  |Poznámka |
|---|---|---|---|
| policyA | allowedLocations | pole  |Tento parametr jako hodnotu očekává seznam řetězců, protože typ parametru byl definovaný jako pole. |
| policyB | allowedSingleLocation |řetězec |Tento parametr jako hodnotu očekává jedno slovo, protože typ parametru byl definovaný jako řetězec. |

V tomto scénáři máte při definování parametrů iniciativy pro **initiativeC** tři možnosti:

- Použít parametry definic zásad v rámci této iniciativy: V tomto příkladu se *allowedLocations* a *allowedSingleLocation* stanou parametry iniciativy pro **initiativeC**.
- Zadat hodnoty do parametrů definic zásad v rámci této definice iniciativy. V tomto příkladu můžete zadat seznam umístění do **parametru allowedLocations zásady policyA** a **parametru allowedSingleLocation zásady policyB**. Hodnoty můžete zadat také při přiřazování této iniciativy.
- Zadat seznam možností *hodnot*, které se můžou použít při přiřazování této iniciativy. Když přiřadíte tuto iniciativu, zděděné parametry z definic zásad v rámci této iniciativy můžou mít pouze hodnoty z tohoto zadaného seznamu.

V definici iniciativy můžete například vytvořit seznam možností hodnot, který obsahuje oblasti *EastUS*, *WestUS*, *CentralUS* a *WestEurope*. Pokud to uděláte, během přiřazování iniciativy nemůžete zadat jinou hodnotu, například oblast *Southeast Asia* (Jihovýchodní Asie), protože není na seznamu.

## <a name="recommendations-for-managing-policies"></a>Doporučení pro správu zásad

Při vytváření a správě definic a přiřazení zásad existuje několik ukazatelů, kterými byste se měli řídit:

- Pokud ve svém prostředí vytváříte definice zásad, doporučujeme místo efektu zamítnutí začít efektem auditu, abyste mohli sledovat dopad definic zásad na prostředky ve vašem prostředí. Pokud již máte skripty pro automatické vertikální navyšování kapacity vašich aplikací, nastavení efektu zamítnutí může bránit těmto již existujícím úlohám automatizace.
- Při vytváření definic a přiřazení je důležité mít na paměti organizační hierarchie. Doporučujeme vytvářet definice na vyšší úrovni, například na úrovni skupiny pro správu nebo předplatného, a přiřazovat je na další podřízené úrovni. Pokud například vytvoříte definici zásady na úrovni skupiny pro správu, oblast přiřazení zásady této definice můžete snížit na úroveň předplatného v rámci této skupiny pro správu.
- Doporučujeme vždy místo definic zásad používat definice iniciativ, a to i v případě, že plánujete používat jenom jednu zásadu. Pokud máte například definici zásady *policyDefA* a vytvoříte ji v rámci definice iniciativy *initiativeDefC*, v případě, že se později rozhodnete vytvořit další definici zásady pro *policyDefB* s podobnými cíli, jako má zásada *policyDefA*, můžete ji přidat do iniciativy *initiativeDefC* a obě definice zásad tak lépe sledovat.

   Mějte na paměti, že jakmile z definice iniciativy vytvoříte přiřazení iniciativy, všechny nové definice zásad přidané do této definice iniciativy se automaticky zahrnou do přiřazení iniciativy v rámci této definice iniciativy. Pokud však nová definice zásady zavádí nový parametr, musíte aktualizovat definici a přiřazení iniciativy upravením definice nebo přiřazení iniciativy.

   Mějte na paměti, že jakmile se aktivuje přiřazení iniciativy, aktivují se také všechny zásady v rámci této iniciativy. Pokud však potřebujete provést zásadu jednotlivě, je lepší ji nezahrnovat do iniciativy.

## <a name="next-steps"></a>Další kroky

Když teď máte přehled o službě Azure Policy a některých klíčových konceptech, které zavádíme, tady jsou další navrhované kroky:

- [Přiřazení definice zásady](assign-policy-definition.md)
- [Přiřazení definice zásady pomocí Azure CLI](assign-policy-definition-cli.md)
- [Přiřazení definice zásady pomocí PowerShellu](assign-policy-definition-ps.md)