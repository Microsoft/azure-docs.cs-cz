---
title: Role a oprávnění pro službu Azure Data Factory
description: Popisuje role a oprávnění potřebné k vytvoření datových továren a pro práci s podřízenými prostředky.
ms.date: 11/5/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: d143992317c77c6fc3137527bea485a98c046daa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75969229"
---
# <a name="roles-and-permissions-for-azure-data-factory"></a>Role a oprávnění pro službu Azure Data Factory

Tento článek popisuje role potřebné k vytvoření a správě prostředků Azure Data Factory a oprávnění udělená těmito rolemi.

## <a name="roles-and-requirements"></a>Role a požadavky

Pro vytvoření instancí služby Data Factory musí být uživatelský účet, který použijete pro přihlášení k Azure, členem role *přispěvatel* nebo *vlastník* nebo *správcem* předplatného Azure. Pokud chcete zobrazit oprávnění, která máte v předplatném, na webu Azure Portal v pravém horním rohu vyberte své uživatelské jméno a potom **Oprávnění**. Pokud máte přístup k několika předplatným, vyberte odpovídající předplatné. 

Při vytváření a správě podřízených prostředků pro službu Data Factory, včetně datových sad, propojených služeb, kanálů, triggerů a prostředí Integration Runtime, platí následující požadavky:
- Pokud chcete vytvářet a spravovat podřízené prostředky na webu Azure Portal, je potřeba, abyste patřili do role **Přispěvatel Data Factory** na úrovni skupiny prostředků nebo vyšší.
- Pro vytváření a správu podřízených prostředků pomocí PowerShellu nebo sady SDK na úrovni prostředku nebo vyšší je dostatečná role **Přispěvatel**.

Ukázku pokynů pro přidání uživatele do role najdete v článku věnovaném [přidávání rolí](../cost-management-billing/manage/add-change-subscription-administrator.md).

## <a name="set-up-permissions"></a>Nastavení oprávnění

Po vytvoření data factory, můžete chtít, aby ostatní uživatelé pracovat s factory dat. Chcete-li tento přístup poskytnout ostatním uživatelům, je třeba je přidat do předdefinované role **přispěvatele datové továrny** ve skupině prostředků, která obsahuje datovou továrnu.

### <a name="scope-of-the-data-factory-contributor-role"></a>Rozsah role přispěvatele datové továrny

Členství v roli **Přispěvatel datové továrny** umožňuje uživatelům dělat následující věci:
- Vytvářejte, upravujte a odstraňujte datové továrny a podřízené prostředky, včetně datových sad, propojených služeb, kanálů, aktivačních událostí a integračních runů.
- Nasazení šablon Správce prostředků Nasazení Správce prostředků je metoda nasazení používaná společností Data Factory na webu Azure Portal.
- Spravujte výstrahy Přehledů aplikací pro datové továrny.
- Vytvořte lístky podpory.

Další informace o této roli naleznete v [tématu Role přispěvatele datové továrny](../role-based-access-control/built-in-roles.md#data-factory-contributor).

### <a name="resource-manager-template-deployment"></a>Nasazení šablony Správce prostředků

Role **přispěvatele datové továrny** na úrovni skupiny prostředků nebo na vyšší úrovni umožňuje uživatelům nasadit šablony Správce prostředků. V důsledku toho mohou členové role používat šablony Správce prostředků k nasazení datových továren i jejich podřízených prostředků, včetně datových sad, propojených služeb, kanálů, aktivačních událostí a zaběhu integrace. Členství v této roli však neumožňuje uživateli vytvářet jiné prostředky.

Oprávnění pro Azure Repos a GitHub jsou nezávislá na oprávněních Data Factory. V důsledku toho může uživatel s oprávněními k opětovnému prodeji, který je pouze členem role Reader, upravovat podřízené prostředky datové továrny a posuzovat změny úložiště, ale nemůže tyto změny publikovat.

> [!IMPORTANT]
> Nasazení šablony Správce prostředků s rolí **Přispěvatel datové továrny** nezvyšuje vaše oprávnění. Pokud například nasadíte šablonu, která vytvoří virtuální počítač Azure, a nemáte oprávnění k vytváření virtuálních počítačů, nasazení se nezdaří s chybou autorizace.

### <a name="custom-scenarios-and-custom-roles"></a>Vlastní scénáře a vlastní role

Někdy může být nutné udělit různé úrovně přístupu pro různé uživatele datové továrny. Například:
- Možná budete potřebovat skupinu, kde uživatelé mají oprávnění pouze pro konkrétní datové továrny.
- Nebo můžete potřebovat skupinu, kde uživatelé mohou sledovat pouze datové továrny (nebo továrny), ale nelze ji upravit.

Těchto vlastních scénářů můžete dosáhnout vytvořením vlastních rolí a přiřazením uživatelů k těmto rolím. Další informace o vlastních rolích najdete [v tématu Vlastní role v Azure](..//role-based-access-control/custom-roles.md).

Zde je několik příkladů, které ukazují, co můžete dosáhnout s vlastnírole:

- Uchvátíte uživatele, aby vytvořil, upravil nebo odstranil libovolnou továrnu dat ve skupině prostředků z webu Azure Portal.

  Přiřaďte uživateli roli **integrovaného přispěvatele data factory** na úrovni skupiny prostředků. Pokud chcete povolit přístup k libovolné datové továrny v předplatném, přiřaďte roli na úrovni předplatného.

- Uchvátí uživatele a sleduje továrnu dat, ale neupravuje ji ani ji neměň.

  Přiřaďte uživateli roli předdefinované **čtečky** na prostředku datové továrny.

- Uchvátíte uživatele, aby na webu Azure Portal upravil jednu datovou továrnu.

  Tento scénář vyžaduje dvě přiřazení rolí.

  1. Přiřaďte předdefinovanou roli **přispěvatele** na úrovni datové továrny.
  2. Vytvořte vlastní roli s oprávněním **Microsoft.Resources/deployments/**. Přiřaďte tuto vlastní roli uživateli na úrovni skupiny prostředků.

- Uchvátit uživatele pouze otestovat připojení v propojené službě

    Vytvořte vlastní roli role s oprávněními pro následující akce: **Microsoft.DataFactory/factories/getFeatureValue/read** a **Microsoft.DataFactory/factories/getDataPlaneAccess/read**. Přiřaďte tuto vlastní roli na prostředek datové továrny pro uživatele.

- Uchvátí uživatele aktualizovat datové továrny z Prostředí PowerShell nebo SDK, ale ne na webu Azure Portal.

  Přiřaďte uživateli roli předdefinovaného **přispěvatele** k prostředku datové továrny. Tato role umožňuje uživateli zobrazit prostředky na webu Azure Portal, ale uživatel nemá přístup k tlačítka **Publikovat** a **publikovat vše.**

## <a name="next-steps"></a>Další kroky

- Další informace o rolích v Azure – [principy definic rolí](../role-based-access-control/role-definitions.md)

- Další informace o roli **přispěvatele datové továrny** – [role Přispěvatel datové továrny](../role-based-access-control/built-in-roles.md#data-factory-contributor).
