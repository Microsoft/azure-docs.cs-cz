---
title: Auditování a vytváření sestav uživatele spolupráce B2B – Azure AD
description: Vlastnosti uživatele typu Host se dají konfigurovat v Azure Active Directory spolupráci B2B.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a805f643e5f93270fe4721f527073baa9a1e4c8d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87908761"
---
# <a name="auditing-and-reporting-a-b2b-collaboration-user"></a>Auditování a vytváření sestav uživatele spolupráce B2B
U uživatelů typu host máte k dispozici možnosti auditování podobně jako u členských uživatelů. 

## <a name="access-reviews"></a>Kontroly přístupu
Pomocí kontrol přístupu můžete pravidelně ověřovat, jestli uživatelé typu Host stále potřebují přístup k vašim prostředkům. Funkce kontroly **přístupu** je dostupná v **Azure Active Directory** v části kontroly přístupu **externích identit**  >  . Můžete také vyhledat "kontroly přístupu" ze **všech služeb** v Azure Portal. Pokud chcete zjistit, jak používat kontroly přístupu, přečtěte si téma [Správa přístupu hostů pomocí kontrol přístupu Azure AD](../governance/manage-guest-access-with-access-reviews.md).

## <a name="audit-logs"></a>Protokoly auditu

Protokoly auditu Azure AD poskytují záznamy o aktivitách systému a uživatelů, včetně aktivit iniciované uživateli typu Host. Chcete-li získat přístup k protokolům auditu, **Azure Active Directory** v části **monitorování** vyberte možnost **protokoly auditu**. Tady je příklad historie oznámení a uplatnění pro pozvání Sam Oogle:

![Snímek obrazovky s příkladem výstupu protokolu auditu](./media/auditing-and-reporting/audit-log.png)

K získání podrobností můžete podrobně jednotlivé události. Řekněme například, že se podíváme na podrobnosti o přijetí.

![Snímek obrazovky zobrazující výstup podrobností o aktivitě a ukázka](./media/auditing-and-reporting/activity-details.png)

Tyto protokoly můžete také exportovat z Azure AD a pomocí nástroje pro vytváření sestav podle svého výběru získat přizpůsobené sestavy.

### <a name="next-steps"></a>Další kroky

- [Vlastnosti uživatele spolupráce B2B](user-properties.md)

