---
title: Auditování a vytváření sestav uživatele spolupráce B2B – Azure AD
description: Vlastnosti uživatele typu Host se dají konfigurovat v Azure Active Directory spolupráci B2B.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "74273301"
---
# <a name="auditing-and-reporting-a-b2b-collaboration-user"></a>Auditování a vytváření sestav uživatele spolupráce B2B
U uživatelů typu host máte k dispozici možnosti auditování podobně jako u členských uživatelů. 

## <a name="access-reviews"></a>Kontroly přístupu
Pomocí kontrol přístupu můžete pravidelně ověřovat, jestli uživatelé typu Host stále potřebují přístup k vašim prostředkům. Funkce kontroly **přístupu** je dostupná v **Azure Active Directory** v části **Správa** > **vztahů organizace**. (Můžete také vyhledat "kontroly přístupu" ze **všech služeb** v Azure Portal.) Pokud chcete zjistit, jak používat kontroly přístupu, přečtěte si téma [Správa přístupu hostů pomocí kontrol přístupu Azure AD](../governance/manage-guest-access-with-access-reviews.md).

## <a name="audit-logs"></a>Protokoly auditu

Protokoly auditu Azure AD poskytují záznamy o aktivitách systému a uživatelů, včetně aktivit iniciované uživateli typu Host. Chcete-li získat přístup k protokolům auditu, **Azure Active Directory**v části **monitorování**vyberte možnost **protokoly auditu**. Tady je příklad historie oznámení a uplatnění pro pozvání Sam Oogle:

![Snímek obrazovky s příkladem výstupu protokolu auditu](./media/auditing-and-reporting/audit-log.png)

K získání podrobností můžete podrobně jednotlivé události. Řekněme například, že se podíváme na podrobnosti o přijetí.

![Snímek obrazovky zobrazující výstup podrobností o aktivitě a ukázka](./media/auditing-and-reporting/activity-details.png)

Tyto protokoly můžete také exportovat z Azure AD a pomocí nástroje pro vytváření sestav podle svého výběru získat přizpůsobené sestavy.

### <a name="next-steps"></a>Další kroky

- [Vlastnosti uživatele spolupráce B2B](user-properties.md)

