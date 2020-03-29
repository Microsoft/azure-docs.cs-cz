---
title: Odkaz na sdílení pro vyžádání balíčku přístupu ve správě nároků Azure AD – Azure Active Directory
description: Přečtěte si, jak sdílet odkaz a požádat o přístupový balíček ve správě nároků služby Azure Active Directory.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea90032b1f0cfe598ffdb3d35448a996f3111036
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968761"
---
# <a name="share-link-to-request-an-access-package-in-azure-ad-entitlement-management"></a>Odkaz na sdílení pro vyžádání balíčku přístupu ve správě oprávnění Azure AD

Většina uživatelů ve vašem adresáři se může přihlásit k portálu Můj přístup a automaticky zobrazit seznam přístupových balíčků, které mohou požadovat. Pro externí uživatele obchodního partnera, kteří ještě nejsou ve vašem adresáři, jim však budete muset odeslat odkaz, který mohou použít k vyžádání přístupového balíčku. 

Dokud je katalog pro přístupový balíček [povolen pro externí uživatele](entitlement-management-catalog-create.md) a máte [zásady pro adresář externího uživatele](entitlement-management-access-package-request-policy.md), může externí uživatel použít odkaz Portál přístupu k vyžádání přístupového balíčku.

## <a name="share-link-to-request-an-access-package"></a>Odkaz na sdílení pro vyžádání přístupového balíčku

**Předpokladová role:** Globální správce, správce uživatele, vlastník katalogu nebo správce balíčků aplikace Access

1. Na webu Azure Portal klikněte na **Azure Active Directory** a potom klikněte na **Identity Governance**.

1. V levé nabídce klikněte na **accessové balíčky** a potom otevřete přístupový balíček.

1. Na stránce Přehled zkopírujte **odkaz Můj přístup ový portál**.

    ![Přehled přístupových balíčků – odkaz na portál Můj Přístup](./media/entitlement-management-shared/my-access-portal-link.png)

    Je důležité, abyste při odesílání internímu obchodnímu partnerovi zkopírovali celý odkaz na portál Můj přístup. Tím zajistíte, že partner získá přístup k portálu vašeho adresáře, aby mohl požádat. Odkaz začíná `myaccess`aplikací , obsahuje nápovědu k adresáři a končí ID přístupového balíčku.  (Pro vládu USA bude `myaccess.microsoft.us`doména v odkazu Portál můj přístup .)

    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. Pošlete e-mail nebo pošlete odkaz externímu obchodnímu partnerovi. Mohou sdílet odkaz se svými uživateli a požádat o přístupový balíček.

## <a name="next-steps"></a>Další kroky

- [Požádat o přístup k přístupovému balíčku](entitlement-management-request-access.md)
- [Schválení nebo zamítnutí žádostí o přístup](entitlement-management-request-approve.md)