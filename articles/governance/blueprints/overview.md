---
title: Přehled Azure Blueprints
description: Pochopte, jak služba Azure Modrotiskys umožňuje vytvářet, definovat a nasazovat artefakty v prostředí Azure.
ms.date: 09/30/2020
ms.topic: overview
ms.openlocfilehash: 0dbf5ab54b694399c9d15cce84e8eca34a5d924e
ms.sourcegitcommit: b437bd3b9c9802ec6430d9f078c372c2a411f11f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91892723"
---
# <a name="what-is-azure-blueprints"></a>Co je Azure Blueprints?

Stejně jako technický nákres či podrobný plán (anglicky „blueprint“) umožňuje technikovi nebo architektovi načrtnout parametry návrhu projektu, služba Azure Blueprints umožňuje cloudovým architektům a centrálním oddělením IT definovat opakovatelnou sadu prostředků Azure, která implementuje a dodržuje standardy, vzory a požadavky organizace. Plány Azure umožňují vývojovým týmům rychle sestavit a vytvořit nová prostředí s důvěryhodným sestavou v souladu se sadou integrovaných komponent, jako jsou třeba síťové služby, a urychlit tak vývoj a doručování.

Podrobné plány představují deklarativní způsob, jak orchestrovat nasazení různých šablon prostředků a dalších artefaktů jako:

- Přiřazení rolí
- Přiřazení zásad
- Šablony Azure Resource Manager (šablony ARM)
- Skupiny prostředků

Služba Azure Blueprints využívá globálně distribuovanou službu [Azure Cosmos DB](../../cosmos-db/introduction.md). Objekty podrobného plánu se replikují do několika oblastí Azure. Tato replikace zajišťuje nízkou latenci, vysokou dostupnost a konzistentní přístup k vašim objektům podrobného plánu, bez ohledu na to, do jaké oblasti modrotisky Azure nasadí vaše prostředky.

## <a name="how-its-different-from-arm-templates"></a>Způsob, jakým se liší od šablon ARM

Účelem této služby je pomáhat s _nastavením prostředí_. Tato instalace se často skládá ze sady skupin prostředků, zásad, přiřazení rolí a nasazení šablon ARM. Podrobný plán je balíček pro každý z těchto typů _artefaktů_ a umožňuje vytváření a verzi balíčku, včetně využití kanálu průběžné integrace a průběžného doručování (CI/CD). Nakonec se každý přiřadí k předplatnému v rámci jedné operace, kterou je možné auditovat a sledovat.

Skoro všechno, co chcete zahrnout do nasazení v Azure modrotisky, se dá udělat pomocí šablony ARM. Šablona ARM je ale dokument, který v Azure neexistuje nativně – každý je uložený buď místně, nebo ve správě zdrojového kódu. Šablona se používá k nasazení jednoho nebo více prostředků Azure. Po jejich nasazení už ale neexistuje s šablonou žádné aktivní propojení ani relace.

V Azure modrotisky se zachová vztah mezi definicí podrobného plánu (co _by měl být_ nasazený) a přiřazení podrobného plánu (co _se_ nasadilo). Toto propojení podporuje vylepšené sledování a auditování nasazení. Azure modrotisky můžou také upgradovat několik předplatných, která se řídí stejným plánem.

Není nutné volit mezi šablonou ARM a podrobným plánem. Každý podrobný plán se může skládat z nuly nebo více _artefaktů_šablon ARM. Tato podpora znamená, že předchozí úsilí pro vývoj a údržbu knihoven šablon ARM je možné znovu použít v plánech Azure.

## <a name="how-its-different-from-azure-policy"></a>Odlišnosti od Azure Policy

Podrobný plán je balíček nebo kontejner pro sestavování sad standardů, vzorů a požadavků týkajících se implementace cloudových služeb, zabezpečení a návrhu Azure, který je možné opětovně používat a zajišťovat tak konzistenci a dodržování předpisů.

[Zásady](../policy/overview.md) jsou systémem výchozích povolení a explicitních zamítnutí, který se zaměřuje na vlastnosti prostředků během nasazení a pro už existující prostředky. Podporují zásady správného řízení cloudu tím, že ověřují, jestli prostředky v rámci předplatného odpovídají požadavkům a standardům.

Zahrnutí zásad do podrobného plánu umožňuje během jeho přiřazování vytvořit správný vzor nebo návrh. Zahrnutí zásad zajišťuje, že je možné provést jenom schválené nebo očekáváné změny prostředí, aby se zajistila ochrana trvalého dodržování záměru podrobného plánu.

V definici podrobného plánu může být zásada obsažena jako jeden z mnoha _artefaktů_ . Podrobné plány podporují také používání parametrů se zásadami a iniciativami.

## <a name="blueprint-definition"></a>Definice podrobného plánu

Podrobný plán se skládá z _artefaktů_. Plány Azure v současné době podporují následující zdroje jako artefakty:

|Prostředek  | Možnosti hierarchie| Description  |
|---------|---------|---------|
|Skupiny prostředků | Předplatné | Vytvořte novou skupinu prostředků pro použití jinými artefakty v rámci podrobného plánu.  Tyto zástupné skupiny prostředků umožňují organizovat prostředky přesně tak, jak chcete, aby byly strukturované a poskytovaly omezení oboru pro zahrnuté artefakty a přiřazení rolí a šablony ARM. |
|Šablona ARM | Předplatné, skupina prostředků | Šablony, včetně vnořených a propojených šablon, slouží k vytváření složitých prostředí. Příklady prostředí: farma SharePointu, konfigurace stavu Azure Automation nebo pracovní prostor služby Log Analytics. |
|Přiřazení zásad | Předplatné, skupina prostředků | Umožňuje přiřazení zásady nebo iniciativy k předplatnému, ke kterému je podrobný plán přiřazený. Zásada nebo iniciativa musí být v rozsahu umístění definice podrobného plánu. Pokud zásady nebo iniciativa obsahuje parametry, tyto parametry se přiřadí při vytvoření podrobného plánu nebo během přiřazení podrobného plánu. |
|Přiřazení role | Předplatné, skupina prostředků | Přidejte existujícího uživatele nebo skupinu k předdefinované roli, aby se zajistilo, že k vašim prostředkům budou mít vždy správný přístup správní lidé. Přiřazení rolí se dá definovat pro celé předplatné nebo vnořit do konkrétní skupiny prostředků, která je součástí podrobného plánu. |

### <a name="blueprint-definition-locations"></a>Umístění definic podrobného plánu

Při vytváření definice podrobného plánu definujete, kam se podrobný plán uloží. Plány lze uložit do [skupiny pro správu](../management-groups/overview.md) nebo předplatného, ke kterému máte přístup **přispěvatele** . Pokud se jedná o skupinu pro správu, je plán k dispozici pro přiřazení k libovolnému podřízenému předplatnému této skupiny pro správu.

### <a name="blueprint-parameters"></a>Parametry podrobného plánu

Podrobné plány můžou předat parametry buď zásad nebo iniciativou, nebo šabloně ARM. Když se do podrobného plánu přidá kterýkoli z _artefaktů_, může se autor rozhodnout, jestli zadá definovanou hodnotu pro každé přiřazení podrobného plánu, nebo umožní, aby jednotlivá přiřazení podrobného plánu zadala hodnotu v době přiřazení. Tato flexibilita umožňuje definovat předem určenou hodnotu pro všechna použití podrobného plánu, nebo umožnit, aby se o tom rozhodlo v době přiřazení.

> [!NOTE]
> Podrobný plán může mít vlastní parametry, ale ty momentálně můžou být vytvořené jenom v případě, že je podrobný plán vygenerovaný z rozhraní REST API a ne prostřednictvím portálu.

Další informace najdete v [parametrech podrobného plánu](./concepts/parameters.md).

### <a name="blueprint-publishing"></a>Publikování podrobného plánu

Při prvním vytvoření podrobného plánu se přepokládá, že je v režimu **konceptu**. Když je připravený k přiřazení, musí být **Publikovaný**. Publikování vyžaduje definování řetězce **Verze** (písmena, číslice a spojovníky s maximální délkou 20 znaků) spolu s volitelnými **Poznámkami ke změnám**. **Verze** ho odlišuje od budoucích změn stejného podrobného plánu a umožňuje přiřazovat jednotlivé verze. Tato správa verzí také znamená, že různé **verze** stejného podrobného plánu je možné přiřadit ke stejnému předplatnému. Pokud jsou v podrobném plánu provedeny další změny, **publikovaná** 
 **verze** stále existuje, stejně jako **nepublikované změny**. Po dokončení změn se aktualizovaný podrobný plán **publikuje** pomocí nové a jedinečné **verze** a je teď možné ho taky přiřadit.

## <a name="blueprint-assignment"></a>Přiřazení podrobného plánu

Každá **publikovaná** **verze** podrobného plánu může být přiřazena (s maximální délkou 90 znaků) do existující skupiny pro správu nebo předplatného. Na portálu bude jako výchozí **verze** podrobného plánu ta, která se **publikovala** jako poslední. Pokud existují parametry artefaktů nebo parametry podrobného plánu, pak jsou parametry definovány během procesu přiřazení.

> [!NOTE]
> Přiřazení definice podrobného plánu ke skupině pro správu znamená, že objekt přiřazení existuje ve skupině pro správu. Nasazení artefaktů stále cílí na předplatné. Chcete-li provést přiřazení skupiny pro správu, je nutné použít [REST API vytvořit nebo aktualizovat](/rest/api/blueprints/assignments/createorupdate) a text žádosti musí obsahovat hodnotu pro `properties.scope` Definování cílového předplatného.

## <a name="permissions-in-azure-blueprints"></a>Oprávnění v Azure Blueprints

Chcete-li použít plány, musíte mít udělená oprávnění prostřednictvím [řízení přístupu na základě role Azure (RBAC)](../../role-based-access-control/overview.md). Chcete-li číst nebo zobrazit podrobný plán v Azure Portal, váš účet musí mít oprávnění ke čtení oboru, ve kterém je umístěna definice podrobného plánu.

Abyste mohli podrobné plány vytvářet, váš účet potřebuje tato oprávnění:

- `Microsoft.Blueprint/blueprints/write` – vytvořit definici podrobného plánu
- `Microsoft.Blueprint/blueprints/artifacts/write` – vytvořit artefakty v definici podrobného plánu
- `Microsoft.Blueprint/blueprints/versions/write` – publikovat podrobný plán

Pokud chcete podrobné plány odstranit, váš účet potřebuje tato oprávnění:

- `Microsoft.Blueprint/blueprints/delete`
- `Microsoft.Blueprint/blueprints/artifacts/delete`
- `Microsoft.Blueprint/blueprints/versions/delete`

> [!NOTE]
> Oprávnění definice podrobného plánu musí být udělená nebo zděděná ve skupině pro správu nebo v oboru předplatného, kde je uložená.

Pokud chcete podrobný plán přiřadit nebo zrušit jeho přiřazení, váš účet potřebuje tato oprávnění:

- `Microsoft.Blueprint/blueprintAssignments/write` – přiřadit podrobný plán
- `Microsoft.Blueprint/blueprintAssignments/delete` – zrušit přiřazení podrobného plánu

> [!NOTE]
> Protože se přiřazení podrobného plánu vytvářejí v předplatném, musí se oprávnění pro přiřazení a zrušení přiřazení podrobného plánu udělit v oboru předplatného nebo do oboru předplatného zdědit.

K dispozici jsou následující předdefinované role:

|Role Azure | Description |
|-|-|
|[Vlastník](../../role-based-access-control/built-in-roles.md#owner) | Kromě dalších oprávnění zahrnuje všechna Azure Blueprint související oprávnění. |
|[Přispěvatel](../../role-based-access-control/built-in-roles.md#contributor) | Kromě dalších oprávnění může vytvořit a odstranit definice podrobného plánu, ale nemá oprávnění k přiřazení podrobného plánu. |
|[Přispěvatel podrobného plánu](../../role-based-access-control/built-in-roles.md#blueprint-contributor) | Může spravovat definice podrobného plánu, ale nepřiřazovat je. |
|[Operátor podrobného plánu](../../role-based-access-control/built-in-roles.md#blueprint-operator) | Může přiřadit existující publikované modrotisky, ale nemůže vytvářet nové definice podrobného plánu. Přiřazení podrobného plánu funguje pouze v případě, že je přiřazení provedeno pomocí uživatelem přiřazené spravované identity. |

Pokud tyto předdefinované role nevyhovují vašim požadavkům na zabezpečení, zvažte vytvoření [vlastní role](../../role-based-access-control/custom-roles.md).

> [!NOTE]
> Pokud používáte spravovanou identitu přiřazenou systémem, musí instanční objekt pro plány Azure pro povolení nasazení vyžadovat roli **vlastníka** v přiřazeném předplatném. Pokud používáte portál, tato role se pro nasazení uděluje a ruší automaticky. Pokud používáte rozhraní REST API, tato role se musí udělit ručně, ale po dokončení nasazení se zruší automaticky. Při použití spravované identity přiřazené uživatelem potřebuje jenom uživatel, který vytvořil přiřazení podrobného plánu `Microsoft.Blueprint/blueprintAssignments/write` , který je zahrnutý v předdefinovaných rolích **vlastník** a podrobný **plán** .

## <a name="naming-limits"></a>Omezení pojmenování

Pro určitá pole existují následující omezení:

|Objekt|Pole|Povolené znaky|Max. Délka|
|-|-|-|-|
|Podrobného plánu|Name|písmena, číslice, spojovníky a tečky|48|
|Podrobného plánu|Verze|písmena, číslice, spojovníky a tečky|20|
|Přiřazení podrobného plánu|Name|písmena, číslice, spojovníky a tečky|90|
|Artefakt podrobného plánu|Name|písmena, číslice, spojovníky a tečky|48|

## <a name="video-overview"></a>Přehled videí

Následující přehled Azure modrotisky je od Azure pátek. Pro stažení videa přejděte na [Azure pátek – Přehled plánů Azure](https://channel9.msdn.com/Shows/Azure-Friday/An-overview-of-Azure-Blueprints) na webu Channel 9.

> [!VIDEO https://www.youtube.com/embed/cQ9D-d6KkMY]

## <a name="next-steps"></a>Další kroky

- [Vytvořit podrobný plán – portál](./create-blueprint-portal.md)
- [Vytvořit podrobný plán – PowerShell](./create-blueprint-powershell.md)
- [Vytvořit podrobný plán – REST API](./create-blueprint-rest-api.md).