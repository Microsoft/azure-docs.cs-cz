---
title: Přehled Azure Blueprints
description: Zjistěte, jak vám služba Azure Blueprints umožňuje vytvářet, definovat a nasazovat artefakty ve vašem prostředí Azure.
ms.date: 11/21/2019
ms.topic: overview
ms.openlocfilehash: 07d84d658d88e977cd73176861e5c5e080c02857
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "74321766"
---
# <a name="what-is-azure-blueprints"></a>Co je Azure Blueprints?

Stejně jako technický nákres či podrobný plán (anglicky „blueprint“) umožňuje technikovi nebo architektovi načrtnout parametry návrhu projektu, služba Azure Blueprints umožňuje cloudovým architektům a centrálním oddělením IT definovat opakovatelnou sadu prostředků Azure, která implementuje a dodržuje standardy, vzory a požadavky organizace. Azure Blueprints vývojářským týmům umožňuje rychle vytvářet nová prostředí s důvěrou, že je vytvářejí v souladu s předpisy organizace, a s využitím sady předdefinovaných komponent – třeba síťových – ke zrychlení vývoje a distribuce.

Podrobné plány představují deklarativní způsob, jak orchestrovat nasazení různých šablon prostředků a dalších artefaktů jako:

- Přiřazení rolí
- Přiřazení zásad
- Šablony Azure Resource Manageru
- Skupiny prostředků

Služba Azure Blueprints využívá globálně distribuovanou službu [Azure Cosmos DB](../../cosmos-db/introduction.md).
Objekty podrobného plánu se replikují do několika oblastí Azure. Tato replikace zajišťuje nízkou latenci, vysokou dostupnost a konzistentní přístup k objektům podrobného plánu bez ohledu na to, do které oblasti služba Blueprints nasazuje prostředky.

## <a name="how-its-different-from-resource-manager-templates"></a>V čem se liší od šablon Resource Manageru

Účelem této služby je pomáhat s _nastavením prostředí_. Toto nastavení často sestává ze sady skupin prostředků, zásad, přiřazení rolí a nasazení šablon Resource Manageru. Podrobný plán (anglicky „blueprint“) je balíček, který všechny tyto typy _artefaktů_ spojuje a umožní vám balíček sestavit a vytvořit jeho verzi – včetně prostřednictvím kanálu CI/CD. Nakonec se každý přiřadí k předplatnému v rámci jedné operace, kterou je možné auditovat a sledovat.

Téměř vše, co chcete zahrnout pro nasazení v Blueprints, se dá provést pomocí šablony Resource Manageru. Šablona Resource Manageru je ale dokument, který v Azure nativně neexistuje – každá je uložená místně nebo ve správě zdrojového kódu. Šablona se používá pro nasazení jednoho nebo více prostředků Azure, ale po nasazení těchto prostředků neexistuje s šablonou žádné aktivní propojení ani relace.

V případě Blueprints se relace mezi definicí podrobného plánu (_co se má_ nasadit) a přiřazením podrobného plánu (_co bylo_ nasazeno) zachová. Toto připojení podporuje vylepšené sledování a auditování nasazení. Blueprints dokáže také upgradovat najednou několik předplatných, která se řídí stejným podrobným plánem.

Není nutné volit mezi šablonou Resource Manageru a podrobným plánem. Každý podrobný plán se může skládat z nula nebo více _artefaktů_ šablon Resource Manageru. Tato podpora znamená, že v Blueprints je možné znovu využít předchozí snahy o vývoj a údržbu knihovny šablon Resource Manageru.

## <a name="how-its-different-from-azure-policy"></a>V čem se liší od Azure Policy

Podrobný plán je balíček nebo kontejner pro sestavování sad standardů, vzorů a požadavků týkajících se implementace cloudových služeb, zabezpečení a návrhu Azure, které je možné opětovně použít k udržení konzistence a dodržování předpisů.

[Zásady](../policy/overview.md) jsou systémem výchozích povolení a explicitních zamítnutí, který se zaměřuje na vlastnosti prostředků během nasazení a pro už existující prostředky. Podporují zásady správného řízení cloudu tím, že ověřují, jestli prostředky v rámci předplatného dodržují požadavky a standardy.

Zahrnutí zásad do podrobného plánu umožňuje vytvořit správný vzor nebo návrh při přiřazování podrobného plánu. Zahrnutí zásad zajišťuje, že je možné provést jenom schválené nebo očekáváné změny prostředí, aby se zajistila ochrana trvalého dodržování záměru podrobného plánu.

Zásady mohou být zahrnuty jako jeden z mnoha _artefaktů_ v definici podrobného plánu. Podrobné plány podporují také používání parametrů se zásadami a iniciativami.

## <a name="blueprint-definition"></a>Definice podrobného plánu

Podrobný plán se skládá z _artefaktů_. Podrobné plány aktuálně jako artefakty podporují tyto prostředky:

|Prostředek  | Možnosti hierarchie| Popis  |
|---------|---------|---------|
|Skupiny prostředků | Předplatné | Vytvořte novou skupinu prostředků pro použití jinými artefakty v rámci podrobného plánu.  Tyto zástupné skupiny prostředků vám umožní uspořádat prostředky přesně tak, jak je chcete mít strukturované, a poskytují omezovač oboru pro zahrnuté artefakty zásad a přiřazení rolí a šablony Azure Resource Manageru. |
|Šablona Azure Resource Manageru | Předplatné, skupina prostředků | Šablony, včetně vnořených a propojených šablon, se používají k komponování složitých prostředí. Příklady prostředí: farma SharePointu, konfigurace stavu Azure Automation nebo pracovní prostor služby Log Analytics. |
|Přiřazení zásad | Předplatné, skupina prostředků | Umožňuje přiřazení zásady nebo iniciativy k předplatnému, ke kterému je podrobný plán přiřazený. Zásady nebo iniciativy musí být v rámci umístění definice podrobného plánu. Pokud zásady nebo iniciativa obsahuje parametry, tyto parametry se přiřadí při vytvoření podrobného plánu nebo během přiřazení podrobného plánu. |
|Přiřazení role | Předplatné, skupina prostředků | Přidejte existujícího uživatele nebo skupinu k předdefinované roli, aby se zajistilo, že k vašim prostředkům budou mít vždy správný přístup správní lidé. Přiřazení rolí se dá definovat pro celé předplatné nebo vnořit do konkrétní skupiny prostředků, která je součástí podrobného plánu. |

### <a name="blueprint-definition-locations"></a>Umístění definic podrobného plánu

Při vytváření definice podrobného plánu definujete, kam se podrobný plán uloží. Podrobné plány lze uložit do [skupiny pro správu](../management-groups/overview.md) nebo předplatného, ke kterému máte **přístup přispěvatele.** Pokud je umístění skupinou pro správu, podrobný plán je k dispozici pro přiřazení libovolného podřízeného předplatného této skupiny pro správu.

### <a name="blueprint-parameters"></a>Parametry podrobného plánu

Podrobné plány můžou předávat parametry zásadám/iniciativě nebo šabloně Azure Resource Manageru.
Když se do podrobného plánu přidá kterýkoli z _artefaktů_, může se autor rozhodnout, jestli zadá definovanou hodnotu pro každé přiřazení podrobného plánu, nebo umožní, aby jednotlivá přiřazení podrobného plánu zadala hodnotu v době přiřazení.
Tato flexibilita umožňuje definovat předem určenou hodnotu pro všechna použití podrobného plánu, nebo umožnit, aby se o tom rozhodlo v době přiřazení.

> [!NOTE]
> Podrobný plán může mít vlastní parametry, ale ty momentálně můžou být vytvořené jenom v případě, že je podrobný plán vygenerovaný z rozhraní REST API a ne prostřednictvím portálu.

Další informace najdete v [parametrech podrobného plánu](./concepts/parameters.md).

### <a name="blueprint-publishing"></a>Publikování podrobného plánu

Při prvním vytvoření podrobného plánu se přepokládá, že je v režimu **konceptu**. Když je připravený k přiřazení, musí být **Publikovaný**. Publikování vyžaduje definování řetězce **Verze** (písmena, číslice a spojovníky s maximální délkou 20 znaků) spolu s volitelnými **Poznámkami ke změnám**. **Verze** ho odlišuje od budoucích změn stejného podrobného plánu a umožňuje přiřazovat jednotlivé verze. Tato správa verzí také znamená, že různé **verze** stejného podrobného plánu je možné přiřadit ke stejnému předplatnému. Při další změny jsou provedeny v podrobném plánu, **publikovaná**
**verze** stále existuje, stejně jako **nepublikované změny**. Po dokončení změn se aktualizovaný podrobný plán **publikuje** pomocí nové a jedinečné **verze** a je teď možné ho taky přiřadit.

## <a name="blueprint-assignment"></a>Přiřazení podrobného plánu

Každá **publikovaná** **verze** podrobného plánu může být přiřazena (s maximální délkou názvu 90 znaků) k existujícímu předplatnému. Na portálu bude jako výchozí **verze** podrobného plánu ta, která se **publikovala** jako poslední. Pokud existují parametry artefaktu (nebo parametry podrobného plánu), pak se parametry definují během přiřazení.

## <a name="permissions-in-azure-blueprints"></a>Oprávnění v Azure Blueprints

Pokud chcete použít podrobné plány, musíte mít udělená oprávnění prostřednictvím [řízení přístupu na základě role](../../role-based-access-control/overview.md). Abyste mohli podrobné plány vytvářet, váš účet potřebuje tato oprávnění:

- `Microsoft.Blueprint/blueprints/write` – vytvořit definici podrobného plánu
- `Microsoft.Blueprint/blueprints/artifacts/write` – vytvořit artefakty v definici podrobného plánu
- `Microsoft.Blueprint/blueprints/versions/write` – publikovat podrobný plán

Pokud chcete podrobné plány odstranit, váš účet potřebuje tato oprávnění:

- `Microsoft.Blueprint/blueprints/delete`
- `Microsoft.Blueprint/blueprints/artifacts/delete`
- `Microsoft.Blueprint/blueprints/versions/delete`

> [!NOTE]
> Oprávnění definice podrobného plánu musí být udělena nebo zděděna ve skupině pro správu nebo oboru předplatného, kde je uložena.

Pokud chcete podrobný plán přiřadit nebo zrušit jeho přiřazení, váš účet potřebuje tato oprávnění:

- `Microsoft.Blueprint/blueprintAssignments/write` – přiřadit podrobný plán
- `Microsoft.Blueprint/blueprintAssignments/delete` – zrušit přiřazení podrobného plánu

> [!NOTE]
> Protože se přiřazení podrobného plánu vytvářejí v předplatném, musí se oprávnění pro přiřazení a zrušení přiřazení podrobného plánu udělit v oboru předplatného nebo do oboru předplatného zdědit.

K dispozici jsou následující předdefinované role:

|RBAC Role | Popis |
|-|-|
|[Vlastník](../../role-based-access-control/built-in-roles.md#owner) | Kromě dalších oprávnění zahrnuje všechna oprávnění související s Azure Blueprintem. |
|[Přispěvatel](../../role-based-access-control/built-in-roles.md#contributor) | Kromě jiných oprávnění můžete vytvářet a odstraňovat definice podrobného plánu, ale nemá oprávnění k přiřazení podrobného plánu. |
|[Přispěvatel podrobného plánu](../../role-based-access-control/built-in-roles.md#blueprint-contributor) | Můžete spravovat definice podrobného plánu, ale ne přiřazovat je. |
|[Operátor plánu](../../role-based-access-control/built-in-roles.md#blueprint-operator) | Můžete přiřadit existující publikované podrobné plány, ale nelze vytvořit nové definice podrobného plánu. Podrobné přiřazení funguje pouze v případě, že přiřazení se provádí s uživatelem přiřazenou spravovanou identitou. |

Pokud tyto předdefinované role nevyhovují vašim požadavkům na zabezpečení, zvažte vytvoření [vlastní role](../../role-based-access-control/custom-roles.md).

> [!NOTE]
> Pokud používáte spravovanou identitu přiřazenou systémem, instanční objekt služby pro azure plány vyžaduje roli **vlastníka** v přiřazeném předplatném, aby bylo možné nasazení. Pokud používáte portál, tato role se pro nasazení uděluje a ruší automaticky. Pokud používáte rozhraní REST API, tato role se musí udělit ručně, ale po dokončení nasazení se zruší automaticky. Pokud používáte uživatelem přiřazenou spravovanou identitu, `Microsoft.Blueprint/blueprintAssignments/write` pouze uživatel, který vytváří přiřazení podrobného plánu, potřebuje oprávnění, které je zahrnuto v předdefinovaných rolích **Vlastník** a **Operátor podrobného plánu.**

## <a name="naming-limits"></a>Limity pojmenování

Pro určitá pole existují následující omezení:

|Objekt|Pole|Povolené znaky|Max. Délka|
|-|-|-|-|
|Blueprint|Name (Název)|písmena, čísla, pomlčky a tečky|48|
|Blueprint|Version|písmena, čísla, pomlčky a tečky|20|
|Přiřazení podrobného plánu|Name (Název)|písmena, čísla, pomlčky a tečky|90|
|Artefakt podrobného plánu|Name (Název)|písmena, čísla, pomlčky a tečky|48|

## <a name="video-overview"></a>Přehled videí

Následující přehled azure blueprints je z Azure pátek. Pro stažení videa navštivte [Azure Fridays – přehled plánů Azure](https://channel9.msdn.com/Shows/Azure-Friday/An-overview-of-Azure-Blueprints) na kanálu 9.

> [!VIDEO https://www.youtube.com/embed/cQ9D-d6KkMY]

## <a name="next-steps"></a>Další kroky

- [Vytvořte podrobný plán – portál](./create-blueprint-portal.md).
- [Vytvořte podrobný plán - PowerShell](./create-blueprint-powershell.md).
- [Vytvořte podrobný plán – rozhraní REST API](./create-blueprint-rest-api.md).