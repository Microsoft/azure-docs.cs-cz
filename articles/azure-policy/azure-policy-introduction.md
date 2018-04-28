---
title: Přehled služby Azure Policy | Microsoft Docs
description: Azure Policy je služba v Azure, pomocí které vytváříte, přiřazujete a spravujete definice zásad ve svém prostředí Azure.
services: azure-policy
keywords: ''
author: bandersmsft
ms.author: banders
ms.reviewer: nini
ms.date: 04/18/2018
ms.topic: overview
ms.service: azure-policy
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 9b948721c9190b38d0770fd38739d53d252f513d
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="what-is-azure-policy"></a>Co je Azure Policy?

Zásady správného řízení v oblasti IT vytvářejí jasný vztah mezi obchodními cíli a projekty IT. Dobré zásady správného řízení v oblasti IT zahrnují plánování iniciativ a nastavení priorit na strategické úrovni. Potýká se vaše společnost s velkým množstvím problémů v oblasti IT, které se zdánlivě nikdy nevyřeší? Implementace zásad vám pomůže tyto problémy lépe zvládnout a předcházet jim. Tuto implementaci zásad řeší Azure Policy.

Azure Policy je služba v Azure, pomocí které vytváříte, přiřazujete a spravujete definice zásad. Definice zásad u vašich prostředků vynucují různá pravidla a akce, aby tyto prostředky i nadále odpovídaly vašim firemním standardům a smlouvám o úrovni služeb. Azure Policy provádí hodnocení vašich prostředků, mezi kterými vyhledává ty, které neodpovídají vašim definicím zásad. Například můžete mít zásadu pro povolení pouze určitého typu virtuálních počítačů. Další vyžaduje, aby všechny prostředky měly konkrétní značku. Tyto zásady se následně vyhodnocují při vytváření a aktualizaci prostředků.

## <a name="how-is-it-different-from-rbac"></a>Čím se to liší od RBAC?

Mezi zásadami a řízením přístupu na základě role (RBAC) existuje několik klíčových rozdílů. RBAC se zaměřuje na akce uživatelů v různých oborech. Například můžete být přidáni do role přispěvatele pro skupinu prostředků v požadovaném oboru. Tato role vám umožňuje provádět změny této skupiny prostředků. Zásady se zaměřují na vlastnosti prostředků během nasazování a vlastnosti již existujících prostředků. Prostřednictvím zásad můžete například řídit, které typy prostředků je možné zřizovat. Nebo můžete omezit oblasti, ve kterých se prostředky můžou zřizovat. Na rozdíl od RBAC jsou zásady systémem, kdy je ve výchozím nastavení vše povoleno a explicitně se zamítá.

Pokud chcete používat zásady, musíte být ověřeni pomocí RBAC. Konkrétně váš účet potřebuje:

- Oprávnění `Microsoft.Authorization/policydefinitions/write` pro definování zásady.
- Oprávnění `Microsoft.Authorization/policyassignments/write` pro přiřazení zásady.
- Oprávnění `Microsoft.Authorization/policySetDefinitions/write` pro definování iniciativy.
- Oprávnění `Microsoft.Authorization/policyassignments/write` pro přiřazení iniciativy.


Tato oprávnění nejsou součástí role **Přispěvatel**.


## <a name="policy-definition"></a>Definice zásady

Každá definice zásady obsahuje podmínky, za kterých se vynucuje. Obsahuje také doprovodnou akci, která se provede při splnění podmínek.

Ve službě Azure Policy nabízíme několik integrovaných zásad, které máte k dispozici ve výchozím nastavení. Příklad:

- **Vyžadovat SQL Server 12.0:** Tato definice zásady obsahuje podmínky/pravidla pro zajištění, aby všechny servery SQL byly verze 12.0. Akcí je zamítnutí všech serverů, které nesplňují tato kritéria.
- **Povolené skladové položky účtu úložiště:** Tato definice zásady obsahuje sadu podmínek/pravidel určujících, jestli se nasazovaný účet úložiště nachází v sadě velikostí skladových položek. Akcí je zamítnutí všech serverů, které neodpovídají sadě definovaných velikostí skladových položek.
- **Povolený typ prostředku:** Tato definice zásady obsahuje sadu podmínek/pravidel určujících typy prostředků, které může vaše organizace nasazovat. Akcí je zamítnutí všech prostředků, které se nenachází na tomto definovaném seznamu.
- **Povolená umístění:** Tato zásada umožňuje omezit umístění, která může vaše organizace zadat při nasazování prostředků. Akcí je vynucení vašich požadavků na geografické dodržování předpisů.
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

<!--
Next link should point to new Concept page for Parameters
-->
Další informace o parametrech zásad najdete v tématu [Přehled zásad prostředků – Parametry](policy-definition.md#parameters).

## <a name="initiative-definition"></a>Definice iniciativy
Definice iniciativy je kolekce definic zásad, které jsou přizpůsobené pro dosažení jednoho zastřešujícího cíle. Definice iniciativ zjednodušují správu a přiřazování definic zásad. Toto zjednodušení spočívá v seskupování sad zásad do jedné položky. Mohli byste například vytvořit iniciativu s názvem **Povolení monitorování v Azure Security Center** s cílem monitorovat všechna dostupná doporučení zabezpečení v Azure Security Center.

V rámci této iniciativy byste měli například tyto definice zásad:

1. **Monitorování nešifrované služby SQL Database v Security Center** – pro monitorování nešifrovaných databází a serverů SQL.
2. **Monitorování ohrožení zabezpečení operačního systému v Security Center** – pro monitorování serverů, které nevyhovují nakonfigurovaným standardním hodnotám.
3. **Monitorování chybějící služby Endpoint Protection v Security Center** – pro monitorování serverů bez nainstalovaného agenta služby Endpoint Protection.

<!--
For more information about initiative definitions, see Initiative Definitions.+ (instead of linking to this, link out to Concept page on Initiative Definitions)
-->

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

1. Použít parametry definic zásad v rámci této iniciativy: V tomto příkladu se *allowedLocations* a *allowedSingleLocation* stanou parametry iniciativy pro **initiativeC**.
2. Zadat hodnoty do parametrů definic zásad v rámci této definice iniciativy. V tomto příkladu můžete zadat seznam umístění do **parametru allowedLocations zásady policyA** a **parametru allowedSingleLocation zásady policyB**.
Hodnoty můžete zadat také při přiřazování této iniciativy.
3. Zadat seznam možností *hodnot*, které se můžou použít při přiřazování této iniciativy. Když přiřadíte tuto iniciativu, zděděné parametry z definic zásad v rámci této iniciativy můžou mít pouze hodnoty z tohoto zadaného seznamu.

V definici iniciativy můžete například vytvořit seznam možností hodnot, který obsahuje oblasti *EastUS*, *WestUS*, *CentralUS* a *WestEurope*. Pokud to uděláte, během přiřazování iniciativy nemůžete zadat jinou hodnotu, například oblast *Southeast Asia* (Jihovýchodní Asie), protože není na seznamu.

## <a name="recommendations-for-managing-policies"></a>Doporučení pro správu zásad

Při vytváření a správě definic a přiřazení zásad existuje několik ukazatelů, kterými byste se měli řídit:

- Pokud ve svém prostředí vytváříte definice zásad, doporučujeme místo efektu zamítnutí začít efektem auditu, abyste mohli sledovat dopad definic zásad na prostředky ve vašem prostředí. Pokud již máte skripty pro automatické vertikální navyšování kapacity vašich aplikací, nastavení efektu zamítnutí může bránit těmto již existujícím úlohám automatizace.
- Při vytváření definic a přiřazení je důležité mít na paměti organizační hierarchie. Doporučujeme vytvářet definice na vyšší úrovni, například na úrovni skupiny pro správu nebo předplatného, a přiřazovat je na další podřízené úrovni. Pokud například vytvoříte definici zásady na úrovni skupiny pro správu, oblast přiřazení zásady této definice můžete snížit na úroveň předplatného v rámci této skupiny pro správu.
- Pro lepší pochopení stavu dodržování předpisů vašeho prostředí doporučujeme používat cenovou úroveň Standard. Další informace o našich cenových modelech a o tom, co jednotlivé modely nabízejí, najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/azure-policy).
- Doporučujeme vždy místo definic zásad používat definice iniciativ, a to i v případě, že plánujete používat jenom jednu zásadu. Pokud máte například definici zásady *policyDefA* a vytvoříte ji v rámci definice iniciativy *initiativeDefC*, v případě, že se později rozhodnete vytvořit další definici zásady pro *policyDefB* s podobnými cíli, jako má zásada *policyDefA*, můžete ji přidat do iniciativy *initiativeDefC* a obě definice zásad tak lépe sledovat.

   Mějte na paměti, že jakmile z definice iniciativy vytvoříte přiřazení iniciativy, všechny nové definice zásad přidané do této definice iniciativy se automaticky zahrnou do přiřazení iniciativy v rámci této definice iniciativy. Pokud však nová definice zásady zavádí nový parametr, musíte aktualizovat definici a přiřazení iniciativy upravením definice nebo přiřazení iniciativy.

## <a name="next-steps"></a>Další kroky

Když teď máte přehled o službě Azure Policy a některých klíčových konceptech, které zavádíme, tady jsou další navrhované kroky:

- [Přiřazení definice zásady](./assign-policy-definition.md)
- [Přiřazení definice zásady pomocí Azure CLI](./assign-policy-definition-cli.md)
- [Přiřazení definice zásady pomocí PowerShellu](./assign-policy-definition-ps.md)
