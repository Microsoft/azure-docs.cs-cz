---
title: Role a oprávnění pro službu Azure Data Factory | Dokumentace Microsoftu
description: Popisuje role a oprávnění potřebná k vytvoření datové továrny a pro práci s podřízené prostředky.
author: douglaslMS
ms.author: douglasl
manager: craigg
ms.date: 11/5/2018
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
ms.tgt_pltfrm: na
ms.openlocfilehash: ee35c7ae1b75c6fb4d656927d7e6e4384cd76836
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56003937"
---
# <a name="roles-and-permissions-for-azure-data-factory"></a>Role a oprávnění pro službu Azure Data Factory

Tento článek popisuje role potřebné k vytváření a správě prostředků Azure Data Factory a oprávnění udělená tyto role.

## <a name="roles-and-requirements"></a>Role a požadavky

Pro vytvoření instancí služby Data Factory musí být uživatelský účet, který použijete pro přihlášení k Azure, členem role *přispěvatel* nebo *vlastník* nebo *správcem* předplatného Azure. Pokud chcete zobrazit oprávnění, která máte v předplatném, na webu Azure Portal v pravém horním rohu vyberte své uživatelské jméno a potom **Oprávnění**. Pokud máte přístup k několika předplatným, vyberte odpovídající předplatné. 

Při vytváření a správě podřízených prostředků pro službu Data Factory, včetně datových sad, propojených služeb, kanálů, triggerů a prostředí Integration Runtime, platí následující požadavky:
- Pokud chcete vytvářet a spravovat podřízené prostředky na webu Azure Portal, je potřeba, abyste patřili do role **Přispěvatel Data Factory** na úrovni skupiny prostředků nebo vyšší.
- Pro vytváření a správu podřízených prostředků pomocí PowerShellu nebo sady SDK na úrovni prostředku nebo vyšší je dostatečná role **Přispěvatel**.

Ukázku pokynů pro přidání uživatele do role najdete v článku věnovaném [přidávání rolí](../billing/billing-add-change-azure-subscription-administrator.md).

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

Oprávnění pro úložiště Azure a Githubu nejsou oprávnění datové továrny. V důsledku toho uživatel s oprávněními úložiště, který je pouze členové role Čtenář můžete upravit podřízené prostředky Data Factory a potvrzení změn úložišti, ale tyto změny nelze publikovat.

> [!IMPORTANT]
> Nasazení šablony Resource Manageru s **Přispěvatel Data Factory** role nezvýší vaše oprávnění. Například pokud nasadit šablonu, která vytvoří virtuální počítač Azure, a nemáte oprávnění k vytvoření virtuálních počítačů, nasazení se nezdaří s chybou autorizace.

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
  2. Vytvořit vlastní roli s oprávněním **Microsoft.Resources/deployments/**. Tuto vlastní roli přiřadíte uživatele na úrovni skupiny prostředků.

- Umožní uživateli aktualizovat objekt pro vytváření dat z Powershellu nebo sady SDK, ale není na portálu Azure portal.

  Přiřadíte integrovanou **Přispěvatel** role na objekt pro vytváření zdrojů dat pro uživatele. Tato role umožňuje uživateli zobrazit prostředky na webu Azure Portal, ale uživatel nemůže přejít **publikovat** a **Publikovat vše** tlačítka.

## <a name="next-steps"></a>Další postup

- Další informace o rolích v Azure – [pochopit definice rolí](../role-based-access-control/role-definitions.md)

- Další informace o **Přispěvatel Data Factory** role - [role Přispěvatel Data Factory](../role-based-access-control/built-in-roles.md#data-factory-contributor).
