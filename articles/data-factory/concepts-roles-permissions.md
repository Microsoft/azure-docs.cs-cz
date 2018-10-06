---
title: Role a oprávnění pro službu Azure Data Factory | Dokumentace Microsoftu
description: Popisuje role a oprávnění potřebná k vytvoření datové továrny a pro práci s podřízené prostředky.
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.date: 10-04/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.openlocfilehash: cd6e28298b773f466ea317b7217af7709802aa12
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48817496"
---
# <a name="roles-and-permissions-for-azure-data-factory"></a>Role a oprávnění pro službu Azure Data Factory

Tento článek popisuje role potřebné k vytváření a správě prostředků Azure Data Factory a oprávnění udělená tyto role.

## <a name="roles-and-requirements"></a>Role a požadavky

Pro vytvoření instancí služby Data Factory musí být uživatelský účet, který použijete pro přihlášení k Azure, členem role *přispěvatel* nebo *vlastník* nebo *správcem* předplatného Azure. Chcete-li zobrazit oprávnění, která máte v rámci předplatného na webu Azure Portal, vyberte své uživatelské jméno v pravém horním rohu a pak vyberte **oprávnění**. Pokud máte přístup k několika předplatným, vyberte odpovídající předplatné. 

Vytvoření a správě podřízených prostředků pro službu Data Factory – včetně datových sad, propojených služeb, kanály, aktivační události a prostředí integration runtime – tyto požadavky platí:
- Vytvoření a správě podřízených prostředků na webu Azure Portal, musíte patřit do **Přispěvatel Data Factory** role na úrovni skupiny prostředků nebo novější.
- Vytvoření a správě podřízených prostředků pomocí Powershellu nebo sady SDK **Přispěvatel** role na úrovni prostředků nebo novější je dostačující.

Ukázka pokyny o tom, jak přidat uživatele k roli najdete v tématu [přidání rolí](../billing/billing-add-change-azure-subscription-administrator.md) článku.

## <a name="set-up-permissions"></a>Nastavení oprávnění

Po vytvoření datové továrny můžete chtít umožní ostatním uživatelům práci s datovou továrnou. Pokud chcete dát přístup ostatním uživatelům, je nutné je přidat do integrovaného **Přispěvatel Data Factory** role pro skupinu prostředků, která obsahuje datovou továrnou.

### <a name="scope-of-the-data-factory-contributor-role"></a>Obor role Přispěvatel Data Factory

Členství ve skupině **Přispěvatel Data Factory** rolí umožňuje uživatelům provádět následující akce:
- Vytvořit, upravit a odstranit objekty pro vytváření dat a podřízené prostředky, včetně datových sad, propojených služeb, kanály, aktivační události a prostředí integration runtime.
- Nasazení šablon Resource Manageru. Nasazení podle modelu Resource Manager je metoda nasazení použít se službou Data Factory na webu Azure Portal.
- Správa výstrah App Insights pro službu data factory.
- Vytvořte lístky podpory.

Další informace o této role, naleznete v tématu [role Přispěvatel Data Factory](../role-based-access-control/built-in-roles.md#data-factory-contributor).

### <a name="resource-manager-template-deployment"></a>Nasazení šablony Resource Manageru

**Přispěvatel Data Factory** role na úrovni skupiny prostředků nebo vyšší, umožňuje uživatelům nasadit šablon Resource Manageru. Členové role proto můžete použít šablony Resource Manageru k nasazování datové továrny a jejich podřízené prostředky, včetně datových sad, propojených služeb, kanály, aktivační události a prostředí integration runtime. Členství v této roli uživatele vytvořit další prostředky, ale neumožňuje.

### <a name="custom-scenarios-and-custom-roles"></a>Vlastní scénáře a vlastní role

V některých případech budete muset udělit různé úrovně přístupu pro uživatele různé datové továrny. Příklad:
- Budete potřebovat skupinu, ve kterém uživatelé mají oprávnění na konkrétní datové továrny.
- Nebo možná bude nutné skupinu, kde uživatelé můžete sledovat pouze služby data factory (nebo objekty pro vytváření) ale nelze změnit.

Tyto vlastní scénáře můžete dosáhnout vytvořením vlastních rolí a přiřazování uživatelů k rolím. Další informace o vlastních rolích najdete v tématu [vlastní role v Azure](..//role-based-access-control/custom-roles.md).

Tady je pár příkladů, které ukazují, můžete dosáhnout s vlastními rolemi:

- Umožňují uživateli vytvořit, upravit nebo odstranit všechny služby data factory v skupiny prostředků na webu Azure Portal.

  Přiřadíte integrovanou **Přispěvatel Data Factory** role na úrovni skupiny prostředků pro daného uživatele. Pokud chcete povolit přístup k objektu pro vytváření všech dat v rámci předplatného, přiřazení role na úrovni předplatného.

- Umožní zobrazení uživatele (čtení) a monitorovat objekt pro vytváření dat, ale nikoli upravovat nebo ho změnit.

  Přiřadíte integrovanou **čtečky** role na objekt pro vytváření zdrojů dat pro uživatele.

- Umožní uživateli upravit jednu datovou továrnu na webu Azure Portal.

  Tento scénář vyžaduje dvě přiřazení rolí.

  1. Přiřadíte integrovanou **Přispěvatel** na objekt pro vytváření dat na úrovni role.
  2. Vytvořit vlastní roli s oprávněním * Microsoft.Resources/deployments/**. Tuto vlastní roli přiřadíte uživatele na úrovni skupiny prostředků.

- Umožní uživateli aktualizovat objekt pro vytváření dat z Powershellu nebo sady SDK, ale není na portálu Azure portal.

  Přiřadíte integrovanou **Přispěvatel** role na objekt pro vytváření zdrojů dat pro uživatele. Tato role umožňuje uživateli zobrazit prostředky na webu Azure Portal, ale uživatel nemůže přejít **publikovat** a **Publikovat vše** tlačítka.

## <a name="next-steps"></a>Další postup

- Další informace o rolích v Azure – [pochopit definice rolí](../role-based-access-control/role-definitions.md)

- Další informace o **Přispěvatel Data Factory** role - [role Přispěvatel Data Factory](../role-based-access-control/built-in-roles.md#data-factory-contributor).