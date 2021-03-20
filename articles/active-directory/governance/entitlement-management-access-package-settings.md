---
title: Odkaz pro sdílení pro vyžádání balíčku přístupu ve správě nároků Azure AD – Azure Active Directory
description: Naučte se sdílet odkaz pro vyžádání balíčku přístupu v Azure Active Directory správě nároků.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32973d063c36a45eee79db0b4da4e7419cb70013
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87798542"
---
# <a name="share-link-to-request-an-access-package-in-azure-ad-entitlement-management"></a>Odkaz pro sdílení pro vyžádání balíčku přístupu ve správě nároků Azure AD

Většina uživatelů v adresáři se může přihlásit k portálu pro přístup a automaticky uvidí seznam balíčků pro přístup, které si můžou vyžádat. U externích partnerských uživatelů, kteří ještě nejsou ve vašem adresáři, je ale budete muset poslat odkaz, který může použít k vyžádání přístupového balíčku. 

Pokud je katalog pro přístupový balíček [povolený pro externí uživatele](entitlement-management-catalog-create.md) a máte [zásadu pro adresář externího uživatele](entitlement-management-access-package-request-policy.md), může externí uživatel požádat o přístup k balíčku s odkazem na portál pro přístup.

## <a name="share-link-to-request-an-access-package"></a>Sdílet odkaz pro vyžádání balíčku pro přístup

**Požadovaná role:** Globální správce, Správce uživatelů, vlastník katalogu nebo správce balíčků přístupu

1. V Azure Portal klikněte na **Azure Active Directory** a pak klikněte na zásady **správného řízení identity**.

1. V nabídce vlevo klikněte na **přístup k balíčkům** a otevřete balíček pro přístup.

1. Na stránce Přehled zkopírujte **odkaz Můj portál přístupu**.

    ![Přehled přístupu k balíčku – odkaz na portál pro přístup](./media/entitlement-management-shared/my-access-portal-link.png)

    Je důležité, abyste při odesílání e-mailu na interní obchodní partner zkopírovali celý odkaz Můj portál pro přístup. Tím se zajistí, že partner získá přístup k portálu vašeho adresáře, aby mohl učinit svůj požadavek. Odkaz začíná na `myaccess` , obsahuje pomocný parametr adresáře a končí ID balíčku přístupu.  (Pro státní správu USA bude doména na portálu pro správu přístupu `myaccess.microsoft.us` .)

    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. Odešlete e-mail nebo pošlete odkaz na vašeho externího obchodního partnera. Můžou sdílet odkaz s uživateli, aby si vyžádali balíček pro přístup.

## <a name="next-steps"></a>Další kroky

- [Vyžádat přístup k balíčku přístupu](entitlement-management-request-access.md)
- [Schválení nebo zamítnutí žádostí o přístup](entitlement-management-request-approve.md)