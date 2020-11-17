---
title: Použití skupiny pro správu přístupu k SaaS aplikacím – Azure AD | Microsoft Docs
description: Postup použití skupin v Azure Active Directory k přiřazení přístupu k SaaS aplikacím, které jsou integrovány s Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: enterprise-users
ms.topic: how-to
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58d5cc64b21207efbdefdafe020248212eed7efe
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94650559"
---
# <a name="using-a-group-to-manage-access-to-saas-applications"></a>Použití skupiny ke správě přístupu k aplikacím SaaS

Pomocí služby Azure Active Directory (Azure AD) s licenčním plánem Azure AD Premium můžete pomocí skupin přiřadit přístup k aplikaci SaaS, která je integrovaná se službou Azure AD. Pokud například chcete přiřadit přístup k marketingovému oddělení, aby používalo pět různých aplikací SaaS, můžete vytvořit skupinu, která obsahuje uživatele v marketingovém oddělení, a pak tuto skupinu přiřadit k těmto pěti aplikacím SaaS, které jsou potřeba pro marketingové oddělení. Tímto způsobem můžete ušetřit čas tím, že budete spravovat členství v marketingovém oddělení na jednom místě. Uživatelé pak budou přiřazeni k aplikaci, když budou přidáni jako členové skupiny marketingu a jejich přiřazení se z aplikace odeberou, když jsou odebrána z marketingové skupiny. Tato funkce se dá použít se stovkami aplikací, které můžete přidat z Galerie aplikací Azure AD.

> [!IMPORTANT]
> Tuto funkci můžete použít až po spuštění zkušebního tarifu Azure AD Premium nebo při nákupu Azure AD Premium.
> Přiřazování na základě skupin je podporováno pouze pro skupiny zabezpečení.
> Vnořené členství ve skupinách momentálně není podporované v případě přiřazování k aplikacím podle skupiny.

## <a name="to-assign-access-for-a-user-or-group-to-a-saas-application"></a>Přiřazení přístupu uživateli nebo skupině k aplikaci SaaS

1. V [centru pro správu Azure AD](https://aad.portal.azure.com)vyberte **podnikové aplikace**.
2. Vyberte aplikaci, kterou jste přidali z Galerie aplikací, a otevřete ji.
3. Vyberte **Uživatelé a skupiny** a pak vyberte **Přidat uživatele**.
4. V části **Přidat přiřazení** vyberte **Uživatelé a skupiny** a otevřete seznam **uživatelů a skupin** .
6. Vyberte tolik skupin nebo uživatelů, kolik chcete, a pak klikněte nebo klepněte na **Vybrat** a přidejte je do seznamu **Přidat přiřazení** . Uživateli v této fázi můžete také přiřadit roli.
7. Vyberte **přiřadit** a přiřaďte uživatele nebo skupiny k vybrané podnikové aplikaci.

## <a name="next-steps"></a>Další kroky
Následující články poskytují další informace o službě Azure Active Directory.

* [Správa přístupu k prostředkům pomocí skupin služby Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Správa aplikací ve službě Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Rutiny Azure Active Directory pro konfiguraci nastavení skupiny](../enterprise-users/groups-settings-cmdlets.md)
* [Co je Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
