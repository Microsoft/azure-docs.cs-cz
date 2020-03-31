---
title: Přehled zásad Azure
description: Azure Policy je služba v Azure, pomocí které vytváříte, přiřazujete a spravujete definice zásad ve svém prostředí Azure.
ms.date: 11/25/2019
ms.topic: overview
ms.custom: fasttrack-edit
ms.openlocfilehash: e886f37a8d7f1395b5c831e81e600ecc6e2dd20f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241525"
---
# <a name="what-is-azure-policy"></a>Co je Azure Policy?

Zásady správného řízení ověřují, že vaše organizace může dosáhnout svých cílů prostřednictvím efektivního a efektivního využití IT. Naplňuje tuto potřebu tím, že vytváří jasnost mezi obchodními cíli a IT projekty.

Potýká se vaše společnost s velkým množstvím problémů v oblasti IT, které se zdánlivě nikdy nevyřeší? Dobré zásady správného řízení v oblasti IT zahrnují plánování iniciativ a nastavení priorit na strategické úrovni, která pomáhají překonávat problémy a předcházet jim. Tato strategická potřeba je místo, kde Azure Policy přichází.

Azure Policy je služba v Azure, která slouží k vytváření, přiřazování a správě zásad. Tyto zásady vynucují na vašich prostředcích různá pravidla a účinky, aby tyto prostředky zůstaly kompatibilní s vašimi firemními standardy a smlouvami o úrovni služeb. Služba Azure Policy splňuje tuto potřebu vyhodnocením vašich prostředků z hlediska nedodržování přiřazených zásad. Všechna data uložená zásadami Azure jsou šifrovaná v klidovém stavu.

Například můžete mít zásadu, která ve vašem prostředí povoluje jenom určitou skladovou položku virtuálních počítačů. Po implementaci této zásady se vyhodnotí, jestli nové a stávajících prostředky vyhovují. Se správným typem zásad je možné zajistit, aby stávající prostředky vyhovovaly. Později v této dokumentaci si projdeme další podrobnosti o tom, jak vytvořit a implementovat zásady pomocí Zásad Azure.

> [!IMPORTANT]
> Vyhodnocování dodržování předpisů ve službě Azure Policy je teď dostupné pro všechna přiřazení bez ohledu na cenovou úroveň. Pokud vaše přiřazení nezobrazují data dodržování předpisů, ujistěte se, že je předplatné zaregistrované u poskytovatele prostředků Microsoft.PolicyInsights.

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-is-it-different-from-rbac"></a>Čím se to liší od RBAC?

Existuje několik klíčových rozdílů mezi zásady Azure a řízení přístupu na základě rolí (RBAC). RBAC se zaměřuje na akce uživatelů v různých oborech. Můžete být přidáni do role přispěvatele pro skupinu prostředků, což vám umožní provádět změny v této skupině prostředků. Azure Policy se zaměřuje na vlastnosti prostředků během nasazení a pro již existující prostředky. Služba Azure Policy řídí vlastnosti, jako jsou typy nebo umístění prostředků. Na rozdíl od RBAC zásady Azure je výchozí povolit a explicitní odepřít systém.

### <a name="rbac-permissions-in-azure-policy"></a>Oprávnění RBAC ve službě Azure Policy

Služba Azure Policy má několik oprávnění, která se označují jako operace, ve dvou poskytovatelích prostředků:

- [Microsoft.Authorization](../../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft.PolicyInsights](../../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

Řada předdefinovaných rolí uděluje oprávnění k prostředkům Azure Policy. Role **přispěvatele zásad prostředků** zahrnuje většinu operací zásad Azure. **Vlastník** má plná práva. Přispěvatel i **Čtenář můžou** používat všechny přečtené operace zásad Azure, ale **přispěvatel** může taky aktivovat nápravu. **Contributor**

Pokud žádná z předdefinovaných rolí nemá požadovaná oprávnění, vytvořte [vlastní roli](../../role-based-access-control/custom-roles.md).

## <a name="policy-definition"></a>Definice zásady

Postup vytváření a implementace zásady v Azure Policy začíná vytvořením definice zásady. Každá definice zásad má podmínky, za kterých je vynucena. A má definovaný účinek, který se koná, pokud jsou splněny podmínky.

V Zásadách Azure nabízíme několik předdefinovaných zásad, které jsou ve výchozím nastavení dostupné. Například:

- **Povolené skladové položky účtu úložiště**: Určuje, zda je nasazený účet úložiště v rámci sady velikostí skladových položk. Jeho účinkem je odepřít všechny účty úložiště, které nedodržují sadu definovaných velikostí skladových položk.
- **Povolený typ prostředku**: Definuje typy prostředků, které můžete nasadit. Jeho účinkem je odepřít všechny prostředky, které nejsou součástí tohoto definovaného seznamu.
- **Povolená umístění**: Omezuje dostupná umístění pro nové zdroje. Účinkem je vynucení vašich požadavků na geografické dodržování předpisů.
- **Povolené souaplikace virtuálních strojů**: Určuje sadu sous virtuálních počítačů, které můžete nasadit.
- **Přidání značky k prostředkům**: Použije požadovanou značku a její výchozí hodnotu, pokud není určena požadavkem na nasazení.
- **Vynutit značku a její hodnotu**: Vynucuje požadovanou značku a její hodnotu prostředku.
- **Nepovolené typy prostředků**: Zabrání nasazení seznamu typů prostředků.

Chcete-li implementovat tyto definice zásad (předdefinované i vlastní definice), budete je muset přiřadit. Jakékoli z těchto zásad můžete přiřadit prostřednictvím webu Azure Portal, PowerShellu nebo Azure CLI.

Vyhodnocení zásad probíhá u několika různých akcí, jako je například přiřazení zásad nebo aktualizace zásad. Úplný seznam naleznete v [tématu Aktivační události vyhodnocení zásad](./how-to/get-compliance-data.md#evaluation-triggers).

Další informace o strukturách definic zásad najdete v článku [Struktura definic zásad](./concepts/definition-structure.md).

## <a name="policy-assignment"></a>Přiřazení zásad

Přiřazení zásady je definice zásady, která byla přiřazena, aby proběhla v rámci zadaného oboru. Tento obor může být v rozsahu od [skupiny pro správu](../management-groups/overview.md) k jednotlivým prostředkům. Rozsah *termínu* odkazuje na všechny prostředky, skupiny prostředků, předplatná nebo skupiny pro správu, které je přiřazena definice zásady. Všechny podřízené prostředky dědí přiřazení zásad. Tento návrh znamená, že zásada použitá pro skupinu prostředků je použita také pro prostředky v této skupině prostředků. Z přiřazení zásad však můžete vyloučit určitý podobor.

Například v oboru předplatného můžete přiřadit zásadu, která brání vytváření síťových prostředků. Můžete vyloučit skupinu prostředků v tomto předplatném, která je určena pro síťovou infrastrukturu. Potom udělíte přístup k této skupině síťových prostředků uživatelům, kterým důvěřujete při vytváření síťových prostředků.

V jiném příkladu můžete přiřadit zásadu seznamu povolených typů prostředků na úrovni skupiny pro správu. A potom přiřadit mnohem mírnější zásady, které povolují více typů prostředků, u podřízené skupiny správy nebo dokonce přímo u předplatných. To ale nebude fungovat, protože zásady představují systém, kdy se explicitně zamítá. Místo toho musíte podřízenou skupinu správy nebo předplatné vyloučit z přiřazení zásad na úrovni skupiny správy. Potom přiřadíte mnohem mírnější zásady na úrovni podřízené skupiny správy nebo předplatného. Pokud některé zásady má za následek prostředek získávání odepřen, pak jediný způsob, jak povolit prostředek je změnit zásady odepření.

Další informace o nastavování definic a přiřazení zásad prostřednictvím portálu najdete v tématu [Vytvoření přiřazení zásady pro identifikaci neodpovídajících prostředků v prostředí Azure](assign-policy-portal.md). K dispozici jsou také kroky pro [PowerShell](assign-policy-powershell.md) a [Azure CLI](assign-policy-azurecli.md).

## <a name="policy-parameters"></a>Parametry zásad

Parametry zásad pomáhají zjednodušit správu zásad tím, že snižují počet definic zásad, které musíte vytvářet. Parametry můžete definovat při vytváření definice zásady a tím ji více zobecnit. Následně můžete tuto definici zásady použít opakovaně pro různé scénáře. Provedete to předáváním různých hodnot při přiřazování této definice zásady. Například můžete pro každé předplatné zadat jednu sadu umístění.

Parametry jsou definovány při vytváření definice zásad. Při definování je parametru dán název a volitelně i hodnota. Pro zásadu můžete například definovat parametr s názvem *location* (umístění). Následně mu můžete při přiřazování zásady předávat různé hodnoty, například *EastUS* nebo *WestUS*.

Další informace o parametrech zásad naleznete v [tématu Definition structure - Parameters](./concepts/definition-structure.md#parameters).

## <a name="initiative-definition"></a>Definice iniciativy

Definice iniciativy je kolekce definic zásad, které jsou přizpůsobené pro dosažení jednoho zastřešujícího cíle. Definice iniciativ zjednodušují správu a přiřazování definic zásad. Toto zjednodušení spočívá v seskupování sad zásad do jedné položky. Mohli byste například vytvořit iniciativu s názvem **Povolení monitorování v Azure Security Center** s cílem monitorovat všechna dostupná doporučení zabezpečení v Azure Security Center.

> [!NOTE]
> Sada SDK, jako je azure cli a Azure PowerShell, používají vlastnosti a parametry s názvem **PolicySet** k odkazování na iniciativy.

V rámci této iniciativy byste měli například tyto definice zásad:

- **Monitorování nešifrované služby SQL Database v Security Center** – pro monitorování nešifrovaných databází a serverů SQL.
- **Monitorování slabých míst operačního systému v Centru zabezpečení** – pro monitorování serverů, které nesplňují nakonfigurovaný směrný plán.
- **Monitorování chybějící služby Endpoint Protection v Security Center** – pro monitorování serverů bez nainstalovaného agenta služby Endpoint Protection.

## <a name="initiative-assignment"></a>Přiřazení iniciativy

Podobně jako přiřazení zásady je přiřazení iniciativy definicí iniciativy přiřazenou ke konkrétnímu oboru. Přiřazení iniciativ omezují potřebu vytváření několika definic iniciativ pro každý obor. Tento obor může také v rozsahu od skupiny pro správu k jednotlivým prostředkům.

Každá iniciativa je přiřaditelná do různých oborů. Jedna iniciativa může být přiřazena k **předplatnémuA** a **subscriptionB**.

## <a name="initiative-parameters"></a>Parametry iniciativ

Podobně jako parametry zásad pomáhají parametry iniciativ zjednodušit správu iniciativ tím, že snižují redundanci. Parametry iniciativy jsou parametry používané definicemi zásad v rámci iniciativy.

Jako příklad může posloužit scénář, ve kterém máte definici iniciativy **initiativeC** s definicemi zásad **policyA** a **policyB**, z nichž každá očekává jiný typ parametru:

| Zásada | Název parametru |Typ parametru  |Poznámka |
|---|---|---|---|
| policyA | allowedLocations | pole  |Tento parametr jako hodnotu očekává seznam řetězců, protože typ parametru byl definovaný jako pole. |
| policyB | allowedSingleLocation |řetězec |Tento parametr jako hodnotu očekává jedno slovo, protože typ parametru byl definovaný jako řetězec. |

V tomto scénáři máte při definování parametrů iniciativy pro **initiativeC** tři možnosti:

- Použít parametry definic zásad v rámci této iniciativy: V tomto příkladu se *allowedLocations* a *allowedSingleLocation* stanou parametry iniciativy pro **initiativeC**.
- Zadat hodnoty do parametrů definic zásad v rámci této definice iniciativy. V tomto příkladu můžete poskytnout seznam umístění **policyA parametr – allowedLocations** a **policyB parametr – allowedSingleLocation**. Hodnoty můžete zadat také při přiřazování této iniciativy.
- Zadat seznam možností *hodnot*, které se můžou použít při přiřazování této iniciativy. Když přiřadíte tuto iniciativu, zděděné parametry z definic zásad v rámci této iniciativy můžou mít pouze hodnoty z tohoto zadaného seznamu.

Při vytváření hodnotových možností v definici iniciativy nelze během přiřazení iniciativy zadat jinou hodnotu, protože není součástí seznamu.

## <a name="maximum-count-of-azure-policy-objects"></a>Maximální počet objektů zásad Azure

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="recommendations-for-managing-policies"></a>Doporučení pro správu zásad

Zde je několik tipů a tipů, které je třeba mít na paměti:

- Začněte s efektem auditování namísto efektu odepření ke sledování dopadu definice zásad na prostředky ve vašem prostředí. Pokud máte skripty již na místě pro automatické škálování aplikací, nastavení efektu odepření může bránit takové úlohy automatizace již na místě.

- Při vytváření definic a přiřazení zvažte organizační hierarchie. Doporučujeme vytvářet definice na vyšších úrovních, jako je například skupina pro správu nebo úroveň předplatného. Potom vytvořte přiřazení na další podřízené úrovni. Pokud vytvoříte definici ve skupině pro správu, přiřazení může být vymezeno až na předplatné nebo skupinu prostředků v rámci této skupiny pro správu.

- Doporučujeme vytvářet a přiřazovat definice iniciativ i pro jednu definici zásad.
  Například máte *zásadu* definice zásady defa a vytvořit ji pod *iniciativou initiative initiativeDefC*. Pokud později vytvoříte jinou definici zásad pro *policyDefB* s cíli *podobnými policyDefA*, můžete ji přidat v rámci *initiativeDefC* a sledovat je společně.

- Jakmile vytvoříte přiřazení iniciativy, definice zásad přidané do iniciativy se také stanou součástí přiřazení těchto iniciativ.

- Při vyhodnocení přiřazení iniciativy jsou vyhodnoceny také všechny zásady v rámci iniciativy.
  Pokud potřebujete vyhodnotit zásadu jednotlivě, je lepší ji nezahrnout do iniciativy.

## <a name="video-overview"></a>Přehled videí

Následující přehled služby Azure Policy se týká sestavení 2018. Snímcích nebo stahováním videa najdete v [článku Řízení prostředí Azure prostřednictvím Zásad Azure](https://channel9.msdn.com/events/Build/2018/THR2030) na kanálu 9.

> [!VIDEO https://www.youtube.com/embed/dxMaYF2GB7o]

## <a name="next-steps"></a>Další kroky

Získali jste přehled o službě Azure Policy a některých klíčových konceptech. Tady je návrh dalších kroků:

- [Přiřaďte definici zásady pomocí portálu](./assign-policy-portal.md).
- [Přiřaďte definici zásad pomocí příkazového příkazu k příkazu Azure](./assign-policy-azurecli.md).
- [Přiřaďte definici zásad pomocí prostředí PowerShell](./assign-policy-powershell.md).
