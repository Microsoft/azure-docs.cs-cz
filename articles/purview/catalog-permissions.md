---
title: Oprávnění katalogu (Preview)
description: Tento článek poskytuje přehled o tom, jak nakonfigurovat Role-Based Access Control (RBAC) v Azure dosah.
author: yarong
ms.author: yarong
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: b351be1e7212dc9923f701599dd951a73254afe0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98610365"
---
# <a name="role-based-access-control-in-azure-purviews-data-plane"></a>Řízení přístupu na základě role v rovině dat Azure dosah

Tento článek popisuje, jak se v [rovině dat](../azure-resource-manager/management/control-plane-and-data-plane.md#data-plane)dosah Azure implementuje Role-Based Access Control (RBAC).

> [!IMPORTANT]
> Objektu zabezpečení, který vytvořil účet dosah, se automaticky dostanou všechna oprávnění roviny dat bez ohledu na to, k jakým rolím roviny dat můžou nebo nemusí být. Aby každý jiný uživatel mohl dělat cokoli v Azure dosah, musí být aspoň v jedné z předem definovaných rolí roviny dat.

## <a name="azure-purviews-pre-defined-data-plane-roles"></a>Předem definované role roviny dat v Azure dosah

Azure dosah definuje sadu předem definovaných rolí rovin dat, které se dají použít k určení toho, kdo má přístup k čemu v Azure dosah. Jedná se o následující role:

* **Role čtecího modulu dat dosah** – má přístup k portálu dosah a může číst veškerý obsah v Azure dosah s výjimkou prověřování vazeb.
* **Role dosah data kurátor** – má přístup k portálu dosah a může číst veškerý obsah v Azure dosah s výjimkou prověřování vazeb, může upravit informace o prostředcích, upravit definice klasifikace a podmínky glosáře a může použít klasifikace a pojmy glosáře k assetům.
* **Role správce zdroje dat dosah** – nemá přístup k portálu dosah (uživatel musí být také v rolích data Reader nebo data kurátor) a může spravovat všechny aspekty skenování dat do Azure dosah, ale nemá přístup pro čtení nebo zápis k obsahu v Azure dosah, a to nad rámec těch, které se týkají kontroly.

## <a name="understanding-how-to-use-azure-purviews-data-plane-roles"></a>Princip použití rolí roviny dat v Azure dosah

Při vytvoření účtu Azure dosah se tvůrce zpracuje, jako by byl v rolích dosah data kurátor a dosah správce zdrojů dat. Tvůrce účtu však není k těmto rolím přiřazený v úložišti rolí. Azure Purview rozpozná, že je objekt zabezpečení tvůrcem účtu, a v závislosti na jeho identitě na něj rozšíří příslušné možnosti.

Všichni ostatní uživatelé můžou účet Azure Purview používat pouze v případě, že mají alespoň jednu z těchto rolí. To znamená, že když se vytvoří účet Azure dosah, nikdo jiný než tvůrce má k účtu přístup nebo použije jeho rozhraní API, dokud se nevloží do jedné nebo více předchozích definovaných rolí.

Upozorňujeme, že role správce zdrojů dat dosah má dva podporované scénáře. První scénář je pro uživatele, kteří už jsou dosah čtenáři dat nebo data dosah Curators, které potřebují také schopnost vytvářet kontroly. Uživatelé musí být ve dvou rolích, alespoň v jednom z dosah data Reader nebo dosah data kurátor a také je umístit do role správce zdrojů dat dosah.

Druhý scénář pro správce zdroje dat dosah je pro programové procesy, jako jsou instanční objekty, které potřebují být schopné nastavit a monitorovat kontroly, ale neměly by mít přístup k žádným datům katalogu.

Tento scénář je možné implementovat tak, že umístíte instanční objekt do role správce zdrojů dat dosah, aniž byste museli být uvedeni v žádné jiné ze dvou rolí. Objekt zabezpečení nemá přístup k portálu dosah, ale to je o.k.. protože jde o programový objekt zabezpečení a komunikuje jenom přes rozhraní API.

## <a name="putting-users-into-roles"></a>Vkládání uživatelů do rolí

Takže první pořadí podnikání po vytvoření účtu Azure dosah slouží k přiřazení osob do těchto rolí.

Přiřazení role se spravuje přes službu [RBAC Azure](../role-based-access-control/overview.md).

Role uživatelů můžou přiřadit jenom dvě předdefinované role řídicích rovin v Azure, které jsou buď vlastníci, nebo správci přístupu uživatelů. Takže pokud chcete, aby se lidé do těchto rolí pro Azure dosah nastavili, musí buď najít někoho, kdo je vlastníkem nebo správcem přístupu uživatelů, nebo se stát jedním oneself.

### <a name="an-example-of-assigning-someone-to-a-role"></a>Příklad přiřazení uživatele k roli

1. Přejděte na https://portal.azure.com účet Azure dosah a přejděte na něj.
1. Na levé straně klikněte na řízení přístupu (IAM).
1. Pak postupujte podle obecných pokynů uvedených [zde](../role-based-access-control/quickstart-assign-role-user-portal.md#create-a-resource-group) .

## <a name="role-definitions-and-actions"></a>Definice rolí a akce

Role je definovaná jako kolekce akcí. Další informace o tom, jak jsou role definované, najdete [tady](../role-based-access-control/role-definitions.md) . [Další informace najdete](../role-based-access-control/built-in-roles.md) v tématu Definice rolí pro role Azure dosah.

## <a name="getting-added-to-a-data-plane-role-in-an-azure-purview-account"></a>Přidání do role roviny dat v účtu Azure dosah

Pokud chcete mít přístup k účtu Azure dosah, abyste mohli použít jeho Studio nebo zavolat jeho rozhraní API, musíte přidat do role roviny dat Azure dosah. Jedinými lidmi, kteří to mohou udělat, jsou vlastníci nebo správci přístupu uživatelů na účet Azure dosah. U většiny uživatelů je dalším krokem najít místního správce, který vám může pomáhat najít správné lidi, kteří vám můžou udělit přístup.

Pro uživatele, kteří mají přístup k firemním [Azure Portal](https://portal.azure.com) můžou vyhledat konkrétní účet Azure dosah, ke kterému se chtějí připojit, kliknout na kartu řízení přístupu (IAM) a zjistit, kdo jsou vlastníci nebo správci přístupu uživatele (UAAs). Upozorňujeme však, že v některých případech mohou být Azure Active Directory skupiny nebo instanční objekty použity jako vlastníci nebo UAAs. v takovém případě nemusí být možné je kontaktovat přímo. Místo toho musí najít správce, který vám může pomáhat.

## <a name="who-should-be-assigned-to-what-role"></a>Kdo má být přiřazen k co roli?

|Scénář uživatele|Příslušné role|
|-------------|-----------------|
|Potřebuji jen najít assety, nechci nic upravovat.|Role čtecího modulu dat dosah|
|Potřebuji upravovat informace o prostředcích, vkládat do nich klasifikace, přidružit je k položkám glosáře atd.|Role dosah data kurátor|
|Potřebuji upravit Glosář nebo nastavit nové definice klasifikace|Role dosah data kurátor|
|Instanční objekt aplikace musí nabízet data do služby Azure dosah.|Role dosah data kurátor|
|Potřebuji nastavovat kontroly přes dosah Studio|Role správce zdroje dat dosah plus nejméně jedna role dosah data Reader nebo role dosah data kurátor|
|Potřebuji aktivovat instanční objekt nebo jinou programovou identitu, abyste mohli nastavit a monitorovat kontroly v Azure dosah bez povolení programové identity pro přístup k informacím katalogu. |Role správce zdroje dat dosah|
|Potřebuji umístit uživatele do rolí v Azure dosah | Vlastník nebo správce přístupu uživatelů |

Další informace o tom, jak do role přidat objekt zabezpečení, najdete v tématu [rychlý Start: vytvoření účtu Azure dosah](create-catalog-portal.md) .

## <a name="next-steps"></a>Další kroky

* [Přehledy dat](concept-insights.md)
