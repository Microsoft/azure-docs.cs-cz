---
title: Auditování a vykazování uživatele spolupráce B2B – Azure AD
description: Vlastnosti uživatele typu Host lze konfigurovat ve spolupráci služby Azure Active Directory B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74d2473d37dcf0d435ab61763e72a64111845956
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74273301"
---
# <a name="auditing-and-reporting-a-b2b-collaboration-user"></a>Auditování a vykazování uživatele spolupráce B2B
U uživatelů typu Host máte možnosti auditování podobné jako u členských uživatelů. 

## <a name="access-reviews"></a>Kontroly přístupu
Pomocí kontrol přístupu můžete pravidelně ověřovat, zda uživatelé typu Host stále potřebují přístup k vašim prostředkům. Funkce **Kontroly aplikace Access** je dostupná ve službě Azure Active **Directory** v části **Správa** > **vztahů organizace**. (Můžete také vyhledat "recenze přístupu" ze **všech služeb** na webu Azure Portal.) Informace o tom, jak používat kontroly přístupu, najdete v [tématu Správa přístupu hosta pomocí kontrol přístupu k Azure AD](../governance/manage-guest-access-with-access-reviews.md).

## <a name="audit-logs"></a>Protokoly auditu

Protokoly auditu Azure AD poskytují záznamy o systémových a uživatelských aktivitách, včetně aktivit iniciovaných uživateli typu Host. Chcete-li získat přístup k protokolům auditování, **vyberte**v části Monitorování v části **Sledování** **protokoly auditování**. Zde je příklad pozvání a vykoupení historie pozvaného Sam Oogle:

![Snímek obrazovky s ukázkou výstupu protokolu auditu](./media/auditing-and-reporting/audit-log.png)

Můžete se ponořit do každé z těchto událostí, abyste získali podrobnosti. Podívejme se například na podrobnosti o přijetí.

![Snímek obrazovky s podrobnostmi o aktivitě a příklad výstupu podrobností o aktivitě](./media/auditing-and-reporting/activity-details.png)

Tyto protokoly můžete také exportovat z Azure AD a pomocí nástroje pro vytváření sestav podle vašeho výběru získat vlastní sestavy.

### <a name="next-steps"></a>Další kroky

- [Vlastnosti uživatele spolupráce B2B](user-properties.md)

