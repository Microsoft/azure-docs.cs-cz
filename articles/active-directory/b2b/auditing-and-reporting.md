---
title: Auditování a vytváření sestav uživatele služby Azure Active Directory s B2B spolupráce | Dokumentace Microsoftu
description: Vlastnosti uživatele typu Host se dají konfigurovat v spolupráce Azure Active Directory s B2B
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.openlocfilehash: 06622c093ca90b3873365e6c93c40fc7221a6398
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2019
ms.locfileid: "55075189"
---
# <a name="auditing-and-reporting-a-b2b-collaboration-user"></a>Auditování a vytváření sestav uživatele spolupráce B2B
Uživatelé typu Host máte podobné možnosti auditování s využitím členské uživatele. 

## <a name="access-reviews"></a>Kontroly přístupu
Kontroly přístupu můžete pravidelně ověřovat, jestli uživatelé typu Host stále potřebují přístup k vašim prostředkům. **Kontrol přístupu** funkce je dostupná v **Azure Active Directory** pod **spravovat** > **organizační vztahy**. ("Kontroly přístupu" můžete vyhledat taky z **všechny služby** na webu Azure Portal.) Další informace o použití kontroly přístupu, najdete v článku [kontroly přístupu hostů spravovat pomocí služby Azure AD access](../governance/manage-guest-access-with-access-reviews.md).

## <a name="audit-logs"></a>Protokoly auditu

Auditování Azure AD protokoly poskytuje záznamy systémových a uživatelských aktivit, včetně aktivit iniciovaných uživatele typu Host. Pro přístup k protokolům auditu v **Azure Active Directory**v části **monitorování**vyberte **protokoly auditu**. Tady je příklad historie pozvánku a uplatnění pozvaného Sam Oogle:

![protokol auditu](./media/auditing-and-reporting/audit-log.png)

Můžete věnovat každá z těchto událostí, který obsahuje podrobné informace. Například Pojďme se podívat na podrobnosti přijetí.

![Podrobnosti o aktivitě](./media/auditing-and-reporting/activity-details.png)

Můžete také exportovat tyto protokoly z Azure AD a získání přizpůsobených sestav pomocí vytváření sestav nástroje podle vašeho výběru.

### <a name="next-steps"></a>Další postup

- [Vlastnosti uživatele spolupráce B2B](user-properties.md)

