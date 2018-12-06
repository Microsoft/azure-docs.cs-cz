---
title: Přehled Azure Blueprints
description: Azure Blueprints je služba v Azure, která se používá k vytváření, definování a nasazování artefaktů ve vašem prostředí Azure.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/05/2018
ms.topic: overview
ms.service: blueprints
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: f1ebbc10109563b771c5417a0449efec12138526
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52967687"
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

Zásady je možné zahrnout jako jeden z mnoha _artefaktů_ v definici podrobných plánů. Podrobné plány podporují také používání parametrů se zásadami a iniciativami.

## <a name="blueprint-definition"></a>Definice podrobného plánu

Podrobný plán se skládá z _artefaktů_. Podrobné plány aktuálně jako artefakty podporují tyto prostředky:

|Prostředek  | Možnosti hierarchie| Popis  |
|---------|---------|---------|
|Skupiny prostředků     | Předplatné | Vytvořte novou skupinu prostředků pro použití jinými artefakty v rámci podrobného plánu.  Tyto zástupné skupiny prostředků vám umožní uspořádat prostředky přesně tak, jak je chcete mít strukturované, a poskytují omezovač oboru pro zahrnuté artefakty zásad a přiřazení rolí a šablony Azure Resource Manageru.         |
|Šablona Azure Resource Manageru      | Předplatné, skupina prostředků | Šablony slouží k vytváření složitých prostředí. Příklady prostředí: farma SharePointu, konfigurace stavu Azure Automation nebo pracovní prostor Log Analytics. |
|Přiřazení zásad     | Předplatné, skupina prostředků | Umožňuje přiřazení zásady nebo iniciativy k předplatnému, ke kterému je podrobný plán přiřazený. Zásady nebo iniciativa musí být v rámci oboru podrobného plánu (ve skupině pro správu podrobného plánu nebo pod ní). Pokud zásady nebo iniciativa obsahuje parametry, tyto parametry se přiřadí při vytvoření podrobného plánu nebo během přiřazení podrobného plánu.       |
|Přiřazení role   | Předplatné, skupina prostředků | Přidejte existujícího uživatele nebo skupinu k předdefinované roli, aby se zajistilo, že k vašim prostředkům budou mít vždy správný přístup správní lidé. Přiřazení rolí se dá definovat pro celé předplatné nebo vnořit do konkrétní skupiny prostředků, která je součástí podrobného plánu. |

### <a name="blueprints-and-management-groups"></a>Podrobné plány a skupiny pro správu

Při vytváření definice podrobného plánu definujete, kam se podrobný plán uloží. V současnosti se podrobné plány můžou uložit jednom do [skupiny pro správu](../management-groups/overview.md), ke které máte přístup jako **Přispěvatel**. Podrobný plán je k dispozici pro přiřazení ke všem podřízeným předplatným této skupiny pro správu.

> [!IMPORTANT]
> Pokud nemáte přístup k žádné skupině pro správu nebo nemáte žádné skupiny pro správu nakonfigurované, při načtení seznamu definic podrobných plánů se ukáže, že žádná není k dispozici, a při kliknutí na **Obor** se otevře okno s upozorněním o načítání skupin pro správu. Pokud to chcete vyřešit, ujistěte se, že součástí [skupiny pro správu](../management-groups/overview.md) je předplatné, ke kterému máte náležitý přístup.

### <a name="blueprint-parameters"></a>Parametry podrobného plánu

Podrobné plány můžou předávat parametry zásadám/iniciativě nebo šabloně Azure Resource Manageru.
Když se do podrobného plánu přidá kterýkoli z _artefaktů_, může se autor rozhodnout, jestli zadá definovanou hodnotu pro každé přiřazení podrobného plánu, nebo umožní, aby jednotlivá přiřazení podrobného plánu zadala hodnotu v době přiřazení. Tato flexibilita umožňuje definovat předem určenou hodnotu pro všechna použití podrobného plánu, nebo umožnit, aby se o tom rozhodlo v době přiřazení.

> [!NOTE]
> Podrobný plán může mít vlastní parametry, ale ty momentálně můžou být vytvořené jenom v případě, že je podrobný plán vygenerovaný z rozhraní REST API a ne prostřednictvím portálu.

Další informace najdete v [parametrech podrobného plánu](./concepts/parameters.md).

### <a name="blueprint-publishing"></a>Publikování podrobného plánu

Při prvním vytvoření podrobného plánu se přepokládá, že je v režimu **konceptu**. Když je připravený k přiřazení, musí být **Publikovaný**. Publikování vyžaduje definování řetězce **Verze** (písmena, číslice a spojovníky s maximální délkou 20 znaků) spolu s volitelnými **Poznámkami ke změnám**. **Verze** ho odlišuje od budoucích změn stejného podrobného plánu a umožňuje přiřazovat jednotlivé verze. Tato správa verzí také znamená, že různé **verze** stejného podrobného plánu je možné přiřadit ke stejnému předplatnému. Při provedení dalších změn podrobného plánu kromě **Nepublikovaných změn** stále existuje **publikovaná** **verze**. Po dokončení změn se aktualizovaný podrobný plán **publikuje** pomocí nové a jedinečné **verze** a je teď možné ho taky přiřadit.

## <a name="blueprint-assignment"></a>Přiřazení podrobného plánu

Každá **publikovaná** **verze** podrobného plánu se může přiřadit k existujícímu předplatnému. Na portálu bude jako výchozí **verze** podrobného plánu ta, která se **publikovala** jako poslední. Pokud existují parametry artefaktu (nebo parametry podrobného plánu), pak se parametry definují během přiřazení.

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
> Protože se definice podrobného plánu vytvářejí ve skupině pro správu, musí se oprávnění definice podrobného plánu udělit v oboru skupiny pro správu nebo do oboru skupiny pro správu zdědit.

Pokud chcete podrobný plán přiřadit nebo zrušit jeho přiřazení, váš účet potřebuje tato oprávnění:

- `Microsoft.Blueprint/blueprintAssignments/write` – přiřadit podrobný plán
- `Microsoft.Blueprint/blueprintAssignments/delete` – zrušit přiřazení podrobného plánu

> [!NOTE]
> Protože se přiřazení podrobného plánu vytvářejí v předplatném, musí se oprávnění pro přiřazení a zrušení přiřazení podrobného plánu udělit v oboru předplatného nebo do oboru předplatného zdědit.

Všechny výše uvedené oprávnění jsou součástí **vlastníka** role. **Přispěvatel** role má vytvořit podrobný plán a odstranit podrobný plán oprávnění, ale nemá oprávnění k přiřazení podrobného plánu. Pokud tyto předdefinované role nevyhovují vašim požadavkům na zabezpečení, zvažte vytvoření [vlastní role](../../role-based-access-control/custom-roles.md).

> [!NOTE]
> K povolení nasazení vyžaduje instanční objekt pro Azure Blueprints u přiřazeného předplatného roli **Vlastník**. Pokud používáte portál, tato role se pro nasazení uděluje a ruší automaticky. Pokud používáte rozhraní REST API, tato role se musí udělit ručně, ale po dokončení nasazení se zruší automaticky.

## <a name="next-steps"></a>Další postup

- [Vytvoření podrobného plánu – portál](create-blueprint-portal.md)
- [Vytvoření podrobného plánu – REST API](create-blueprint-rest-api.md)
