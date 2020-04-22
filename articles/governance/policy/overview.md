---
title: Přehled zásad Azure
description: Azure Policy je služba v Azure, pomocí které vytváříte, přiřazujete a spravujete definice zásad ve svém prostředí Azure.
ms.date: 04/21/2020
ms.topic: overview
ms.openlocfilehash: 68005a9a07e7f081a646c75566a0be4046f078ad
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770588"
---
# <a name="what-is-azure-policy"></a>Co je Azure Policy?

Azure Policy pomáhá vynucovat organizační standardy a vyhodnocovat dodržování předpisů ve velkém měřítku. Prostřednictvím řídicího panelu dodržování předpisů poskytuje agregované zobrazení k vyhodnocení celkového stavu prostředí, s možností přejít k podrobnostem na prostředek, rozlišovací schopnost podle zásad. Pomáhá také přenést vaše prostředky do souladu s předpisy prostřednictvím hromadné nápravy stávajících prostředků a automatické nápravy nových prostředků.

Běžné případy použití zásad Azure zahrnují implementaci zásad správného řízení pro konzistenci prostředků, dodržování předpisů, zabezpečení, náklady a správu. Definice zásad pro tyto běžné případy použití jsou už dostupné ve vašem prostředí Azure jako integrované prostředí, které vám pomůžou snadno začít.

## <a name="overview"></a>Přehled

Azure Policy vyhodnocuje prostředky v Azure porovnáním vlastností těchto prostředků s obchodními pravidly. Tato obchodní pravidla popsaná ve [formátu JSON](./concepts/definition-structure.md)se označují jako [definice zásad](#policy-definition). Pro zjednodušení správy lze seskupit několik obchodních pravidel a vytvořit [tak iniciativu zásad](#initiative-definition) (někdy označovanou jako _sada zásad)._ Po vytvoření obchodních pravidel se definice zásad nebo iniciativa [přiřadí](#assignments) k libovolnému oboru prostředků, které Azure podporuje, jako jsou [skupiny pro správu](../management-groups/overview.md), předplatná, [skupiny prostředků](../../azure-resource-manager/management/overview.md#resource-groups)nebo jednotlivé prostředky. Přiřazení se vztahuje na všechny zdroje v rámci [tohoto](../../azure-resource-manager/management/overview.md#understand-scope) přiřazení.
Podkopy mohou být v případě potřeby vyloučeny.

Azure Policy používá [formát JSON](./concepts/definition-structure.md) k vytvoření logiky, kterou hodnocení používá k určení, jestli je prostředek kompatibilní nebo ne. Definice zahrnují metadata a pravidlo zásad. Definované pravidlo může používat funkce, parametry, logické operátory, podmínky a [aliasy vlastností](./concepts/definition-structure.md#aliases) tak, aby přesně odpovídaly požadovanému scénáři. Pravidlo zásad určuje, které prostředky v rozsahu přiřazení budou vyhodnoceny.

### <a name="understand-evaluation-outcomes"></a>Porozumět výsledkům hodnocení

Zdroje jsou vyhodnocovány v určitých časech během životního cyklu zdrojů, životního cyklu přiřazení zásad a pro pravidelné průběžné hodnocení dodržování předpisů. Následují časy nebo události, které způsobují vyhodnocení prostředku:

- Prostředek je vytvořen, aktualizován nebo odstraněn v oboru s přiřazením zásad.
- Zásada nebo iniciativa je nově přiřazena k oboru.
- Zásada nebo iniciativa, která je již přiřazena k oboru, je aktualizována.
- Během standardního cyklu hodnocení shody, ke kterému dochází jednou za 24 hodin.

Podrobné informace o tom, kdy a jak probíhá hodnocení zásad, naleznete v [tématu Vyhodnocení aktivačních událostí](./how-to/get-compliance-data.md#evaluation-triggers).

### <a name="control-the-response-to-an-evaluation"></a>Kontrola odpovědi na hodnocení

Obchodní pravidla pro zpracování nekompatibilních prostředků se v jednotlivých organizacích značně liší. Příklady toho, jak organizace chce, aby platforma reagovala na prostředek bez stížnosti, zahrnují:

- Odepřít změnu prostředku
- Protokolovat změnu prostředku
- Změna zdroje před změnou
- Změna zdroje po změně
- Nasazení souvisejících kompatibilních prostředků

Azure Policy umožňuje každou z těchto obchodních odpovědí možné prostřednictvím použití [efektů](./concepts/effects.md). Účinky jsou nastaveny v části **pravidla zásad** y [definice zásad](./concepts/definition-structure.md).

### <a name="remediate-non-compliant-resources"></a>Oprava prostředků, které nevyhovují předpisům

Zatímco tyto efekty primárně ovlivňují prostředek při vytvoření nebo aktualizaci prostředku, zásady Azure také podporuje práci s existujícími nekompatibilními prostředky bez nutnosti měnit tento prostředek. Další informace o tom, jak stávající prostředky splňovat, naleznete [v tématu náprava prostředků](./how-to/remediate-resources.md).

### <a name="video-overview"></a>Přehled videí

Následující přehled služby Azure Policy se týká sestavení 2018. Snímcích nebo stahováním videa najdete v [článku Řízení prostředí Azure prostřednictvím Zásad Azure](https://channel9.msdn.com/events/Build/2018/THR2030) na kanálu 9.

> [!VIDEO https://www.youtube.com/embed/dxMaYF2GB7o]

## <a name="getting-started"></a>Začínáme

### <a name="azure-policy-and-rbac"></a>Zásady Azure a RBAC

Existuje několik klíčových rozdílů mezi zásady Azure a řízení přístupu na základě rolí (RBAC). Azure Policy vyhodnotí stav kontrolou vlastností prostředků, které jsou reprezentovány ve Správci prostředků a vlastností některých zprostředkovatelů prostředků. Zásady Azure neomezují akce (označované také jako _operace)._ Azure Policy zajišťuje, že stav prostředků je kompatibilní s vašimi obchodními pravidly bez obav o to, kdo provedl změnu nebo kdo má oprávnění ke změně.

RBAC se zaměřuje na správu [uživatelských akcí](../../role-based-access-control/resource-provider-operations.md) v různých oborech. Pokud je vyžadována kontrola akce, pak RBAC je správný nástroj pro použití. I v případě, že jednotlivec má přístup k provedení akce, pokud je výsledkem nekompatibilní prostředek, zásady Azure stále blokuje vytvořit nebo aktualizovat.

Kombinace RBAC a Azure Policy poskytují plnou kontrolu rozsahu v Azure.

### <a name="rbac-permissions-in-azure-policy"></a>Oprávnění RBAC ve službě Azure Policy

Služba Azure Policy má několik oprávnění, která se označují jako operace, ve dvou poskytovatelích prostředků:

- [Microsoft.Authorization](../../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft.PolicyInsights](../../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

Řada předdefinovaných rolí uděluje oprávnění k prostředkům Azure Policy. Role **přispěvatele zásad prostředků** zahrnuje většinu operací zásad Azure. **Vlastník** má plná práva. **Přispěvatel** i **Čtenář mají** přístup ke všem _přečtených_ operací zásad Azure. **Přispěvatel** může vyvolat nápravu prostředků, ale nemůže _vytvářet_ definice nebo přiřazení.

Pokud žádná z předdefinovaných rolí nemá požadovaná oprávnění, vytvořte [vlastní roli](../../role-based-access-control/custom-roles.md).

> [!NOTE]
> Spravovaná identita přiřazení zásad **deployIfNotExists** potřebuje dostatečná oprávnění k vytvoření nebo aktualizaci prostředků zahrnutých v šabloně. Další informace naleznete [v tématu Konfigurace definic zásad pro nápravu](./how-to/remediate-resources.md#configure-policy-definition).

### <a name="resources-covered-by-azure-policy"></a>Prostředky, na které se vztahuje zásady Azure

Azure Policy vyhodnotí všechny prostředky v Azure. Pro některé poskytovatele prostředků, jako je [konfigurace hosta](./concepts/guest-configuration.md), [Služba Azure Kubernetes](../../aks/intro-kubernetes.md)a [Azure Key Vault](../../key-vault/key-vault-overview.md), je hlubší integrace pro správu nastavení a objektů. Další informace naleznete v [tématu Režimy zprostředkovatele prostředků](./concepts/definition-structure.md).

### <a name="recommendations-for-managing-policies"></a>Doporučení pro správu zásad

Zde je několik tipů a tipů, které je třeba mít na paměti:

- Začněte s efektem auditování namísto efektu odepření ke sledování dopadu definice zásad na prostředky ve vašem prostředí. Pokud máte skripty již na místě pro automatické škálování aplikací, nastavení efektu odepření může bránit takové úlohy automatizace již na místě.

- Při vytváření definic a přiřazení zvažte organizační hierarchie. Doporučujeme vytvářet definice na vyšších úrovních, jako je například skupina pro správu nebo úroveň předplatného. Potom vytvořte přiřazení na další podřízené úrovni. Pokud vytvoříte definici ve skupině pro správu, přiřazení může být vymezeno až na předplatné nebo skupinu prostředků v rámci této skupiny pro správu.

- Doporučujeme vytvářet a přiřazovat definice iniciativ i pro jednu definici zásad.
  Například máte _zásadu_ definice zásady defa a vytvořit ji pod _iniciativou initiative initiativeDefC_. Pokud později vytvoříte jinou definici zásad pro _policyDefB_ s cíli _podobnými policyDefA_, můžete ji přidat v rámci _initiativeDefC_ a sledovat je společně.

- Jakmile vytvoříte přiřazení iniciativy, definice zásad přidané do iniciativy se také stanou součástí přiřazení těchto iniciativ.

- Při vyhodnocení přiřazení iniciativy jsou vyhodnoceny také všechny zásady v rámci iniciativy.
  Pokud potřebujete vyhodnotit zásadu jednotlivě, je lepší ji nezahrnout do iniciativy.

## <a name="azure-policy-objects"></a>Objekty zásad Azure

### <a name="policy-definition"></a>Definice zásady

Postup vytváření a implementace zásady v Azure Policy začíná vytvořením definice zásady. Každá definice zásad má podmínky, za kterých je vynucena. A má definovaný účinek, který se koná, pokud jsou splněny podmínky.

V Zásadách Azure nabízíme několik předdefinovaných zásad, které jsou ve výchozím nastavení dostupné. Příklad:

- **Povolené skladové položky účtu úložiště** (odepřít): Určuje, zda je nasazený účet úložiště v rámci sady velikostí skladových míst. Jeho účinkem je odepřít všechny účty úložiště, které nedodržují sadu definovaných velikostí skladových položk.
- **Povolený typ prostředku** (Odepřít): Definuje typy prostředků, které můžete nasadit. Jeho účinkem je odepřít všechny prostředky, které nejsou součástí tohoto definovaného seznamu.
- **Povolená umístění** (odepřít): Omezuje dostupná umístění pro nové prostředky. Účinkem je vynucení vašich požadavků na geografické dodržování předpisů.
- **Povolené soupoložky virtuálních strojů** (Odepřít): Určuje sadu soupoložk virtuálních strojů, které můžete nasadit.
- **Přidání značky k prostředkům** (Změnit): Použije požadovanou značku a její výchozí hodnotu, pokud není určena požadavkem na nasazení.
- **Připojit značku a její výchozí hodnotu** (Připojit): Vynucuje požadovanou značku a její hodnotu k prostředku.
- **Nepovolené typy prostředků** (Odepřít): Zabrání seznamu typů prostředků nasazených.

Chcete-li implementovat tyto definice zásad (předdefinované i vlastní definice), budete je muset přiřadit. Jakékoli z těchto zásad můžete přiřadit prostřednictvím webu Azure Portal, PowerShellu nebo Azure CLI.

Vyhodnocení zásad probíhá u několika různých akcí, jako je například přiřazení zásad nebo aktualizace zásad. Úplný seznam naleznete v [tématu Aktivační události vyhodnocení zásad](./how-to/get-compliance-data.md#evaluation-triggers).

Další informace o strukturách definic zásad najdete v článku [Struktura definic zásad](./concepts/definition-structure.md).

Parametry zásad pomáhají zjednodušit správu zásad tím, že snižují počet definic zásad, které musíte vytvářet. Parametry můžete definovat při vytváření definice zásady a tím ji více zobecnit. Následně můžete tuto definici zásady použít opakovaně pro různé scénáře. Provedete to předáváním různých hodnot při přiřazování této definice zásady. Například můžete pro každé předplatné zadat jednu sadu umístění.

Parametry jsou definovány při vytváření definice zásad. Při definování je parametru dán název a volitelně i hodnota. Pro zásadu můžete například definovat parametr s názvem _location_ (umístění). Následně mu můžete při přiřazování zásady předávat různé hodnoty, například _EastUS_ nebo _WestUS_.

Další informace o parametrech zásad naleznete v [tématu Definition structure - Parameters](./concepts/definition-structure.md#parameters).

### <a name="initiative-definition"></a>Definice iniciativy

Definice iniciativy je kolekce definic zásad, které jsou přizpůsobené pro dosažení jednoho zastřešujícího cíle. Definice iniciativ zjednodušují správu a přiřazování definic zásad. Toto zjednodušení spočívá v seskupování sad zásad do jedné položky. Mohli byste například vytvořit iniciativu s názvem **Povolení monitorování v Azure Security Center** s cílem monitorovat všechna dostupná doporučení zabezpečení v Azure Security Center.

> [!NOTE]
> Sada SDK, jako je azure cli a Azure PowerShell, používají vlastnosti a parametry s názvem **PolicySet** k odkazování na iniciativy.

V rámci této iniciativy byste měli například tyto definice zásad:

- **Monitorování nešifrované služby SQL Database v Security Center** – pro monitorování nešifrovaných databází a serverů SQL.
- **Monitorování slabých míst operačního systému v Centru zabezpečení** – pro monitorování serverů, které nesplňují nakonfigurovaný směrný plán.
- **Monitorování chybějící služby Endpoint Protection v Security Center** – pro monitorování serverů bez nainstalovaného agenta služby Endpoint Protection.

Podobně jako parametry zásad pomáhají parametry iniciativ zjednodušit správu iniciativ tím, že snižují redundanci. Parametry iniciativy jsou parametry používané definicemi zásad v rámci iniciativy.

Jako příklad může posloužit scénář, ve kterém máte definici iniciativy **initiativeC** s definicemi zásad **policyA** a **policyB**, z nichž každá očekává jiný typ parametru:

| Zásada | Název parametru |Typ parametru  |Poznámka |
|---|---|---|---|
| policyA | allowedLocations | pole  |Tento parametr jako hodnotu očekává seznam řetězců, protože typ parametru byl definovaný jako pole. |
| policyB | allowedSingleLocation |řetězec |Tento parametr jako hodnotu očekává jedno slovo, protože typ parametru byl definovaný jako řetězec. |

V tomto scénáři máte při definování parametrů iniciativy pro **initiativeC** tři možnosti:

- Použít parametry definic zásad v rámci této iniciativy: V tomto příkladu se _allowedLocations_ a _allowedSingleLocation_ stanou parametry iniciativy pro **initiativeC**.
- Zadat hodnoty do parametrů definic zásad v rámci této definice iniciativy. V tomto příkladu můžete poskytnout seznam umístění **policyA**'s parametr – **allowedLocations** and **policyB**'s parametr – **allowedSingleLocation**. Hodnoty můžete zadat také při přiřazování této iniciativy.
- Zadat seznam možností _hodnot_, které se můžou použít při přiřazování této iniciativy. Když přiřadíte tuto iniciativu, zděděné parametry z definic zásad v rámci této iniciativy můžou mít pouze hodnoty z tohoto zadaného seznamu.

Při vytváření hodnotových možností v definici iniciativy nelze během přiřazení iniciativy zadat jinou hodnotu, protože není součástí seznamu.

### <a name="assignments"></a>Přiřazení

Přiřazení je definice zásad y nebo iniciativa, která byla přiřazena k tomu, aby probíhala v rámci určitého oboru. Tento obor může být v rozsahu od [skupiny pro správu](../management-groups/overview.md) k jednotlivým prostředkům. Rozsah _termínu_ odkazuje na všechny prostředky, skupiny prostředků, předplatná nebo skupiny pro správu, které je přiřazena definice. Přiřazení jsou zděděna všemi podřízenými prostředky. Tento návrh znamená, že definice použitá pro skupinu prostředků je také použita pro prostředky v této skupině prostředků. Můžete však vyloučit podobor z přiřazení.

Například v oboru předplatného můžete přiřadit definici, která brání vytváření síťových prostředků. Můžete vyloučit skupinu prostředků v tomto předplatném, která je určena pro síťovou infrastrukturu. Potom udělíte přístup k této skupině síťových prostředků uživatelům, kterým důvěřujete při vytváření síťových prostředků.

V jiném příkladu můžete přiřadit definici seznamu povolených typů prostředků na úrovni skupiny pro správu. Pak přiřadíte více tolerantní zásady (povolení více typů prostředků) na podřízené skupiny pro správu nebo dokonce přímo na předplatná. Tento příklad by však nefungoval, protože Azure Policy je explicitní systém odepřít. Místo toho je třeba vyloučit podřízenou skupinu pro správu nebo předplatné z přiřazení na úrovni skupiny pro správu. Potom přiřaďte více tolerantní definici na podřízenou skupinu pro správu nebo úroveň předplatného. Pokud jakékoli přiřazení vede k odepření prostředku, je jediným způsobem, jak zdroj povolit, upravit odepření přiřazení.

Další informace o nastavení přiřazení prostřednictvím portálu najdete v [tématu Vytvoření přiřazení zásad k identifikaci nekompatibilních prostředků ve vašem prostředí Azure](assign-policy-portal.md). K dispozici jsou také kroky pro [PowerShell](assign-policy-powershell.md) a [Azure CLI](assign-policy-azurecli.md).

## <a name="maximum-count-of-azure-policy-objects"></a>Maximální počet objektů zásad Azure

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="next-steps"></a>Další kroky

Získali jste přehled o službě Azure Policy a některých klíčových konceptech. Tady je návrh dalších kroků:

- [Zkontrolujte strukturu definice zásad](./concepts/definition-structure.md).
- [Přiřaďte definici zásady pomocí portálu](./assign-policy-portal.md).
