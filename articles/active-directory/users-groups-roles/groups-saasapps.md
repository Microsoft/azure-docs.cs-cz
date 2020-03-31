---
title: Správa přístupu k aplikacím SaaS – Azure AD | Dokumenty společnosti Microsoft
description: Jak používat skupiny ve službě Azure Active Directory k přiřazení přístupu k aplikacím SaaS, které jsou integrované s Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51375f057543c86fe021822eb9722ffd1be16804
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74026860"
---
# <a name="using-a-group-to-manage-access-to-saas-applications"></a>Použití skupiny ke správě přístupu k aplikacím SaaS

Pomocí Služby Azure Active Directory (Azure AD) s licenčním plánem Azure AD Premium můžete pomocí skupin přiřadit přístup k aplikaci SaaS, která je integrovaná s Azure AD. Chcete-li například přiřadit marketingovému oddělení přístup k použití pěti různých aplikací SaaS, můžete vytvořit skupinu, která obsahuje uživatele v marketingovém oddělení, a pak tuto skupinu přiřadit těmto pěti aplikacím SaaS, které jsou potřebné marketingového oddělení. Tímto způsobem můžete ušetřit čas tím, že spravuje členství v marketingovém oddělení na jednom místě. Uživatelé jsou pak přiřazeni k aplikaci, když jsou přidáni jako členové marketingové skupiny a jejich přiřazení jsou odebrána z aplikace, když jsou odebrány z marketingové skupiny. Tuto funkci lze použít se stovkami aplikací, které můžete přidat z galerie aplikací Azure AD.

> [!IMPORTANT]
> Tuto funkci můžete použít až po spuštění zkušební verze Azure AD Premium nebo zakoupení matného tarifu Azure AD Premium.
> Přiřazení založené na skupině je podporováno pouze pro skupiny zabezpečení.
> Vnořené členství ve skupinách momentálně není podporované v případě přiřazování k aplikacím podle skupiny.

## <a name="to-assign-access-for-a-user-or-group-to-a-saas-application"></a>Přiřazení přístupu uživatele nebo skupiny k aplikaci SaaS

1. V [Centru pro správu Azure AD](https://aad.portal.azure.com)vyberte **podnikové aplikace**.
2. Vyberte aplikaci, kterou jste přidali v Galerii aplikací, a otevřete ji.
3. Vyberte **Uživatelé a skupiny**a pak vyberte **Přidat uživatele**.
4. V **části Přidat přiřazení**vyberte Možnost **Uživatelé a skupiny,** chcete-li otevřít seznam **výběru Uživatelé a skupiny.**
6. Vyberte libovolný počet skupin nebo uživatelů a kliknutím nebo klepnutím na **Vybrat** je přidejte do seznamu **Přidat přiřazení.** V této fázi můžete také přiřadit roli uživateli.
7. Vyberte **Přiřadit,** chcete-li přiřadit uživatele nebo skupiny k vybrané podnikové aplikaci.

## <a name="next-steps"></a>Další kroky
Následující články poskytují další informace o službě Azure Active Directory.

* [Správa přístupu k prostředkům pomocí skupin služby Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Správa aplikací ve službě Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Rutiny Azure Active Directory pro konfiguraci nastavení skupiny](groups-settings-cmdlets.md)
* [Představení služby Azure Active Directory](../fundamentals/active-directory-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
