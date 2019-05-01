---
title: Řešení potíží se správou oprávnění Azure AD (Preview) – Azure Active Directory
description: Přečtěte si o některé položky, že měli byste zkontrolovat při řešení potíží se správou oprávnění Azure Active Directory (Preview).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/25/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ca7955186d6c6fb1dff2acd6a2d5badd1cedaef
ms.sourcegitcommit: 9ad75f83bbf0fc4623b7995794f33bbf823b31c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/26/2019
ms.locfileid: "64541507"
---
# <a name="troubleshoot-azure-ad-entitlement-management-preview"></a>Řešení potíží se správou oprávnění Azure AD (Preview)

> [!IMPORTANT]
> Správa nároků Azure Active Directory (Azure AD) je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tento článek popisuje některé položky, měli byste zkontrolovat při řešení potíží se správou oprávnění Azure Active Directory (Azure AD).

## <a name="checklist-for-entitlement-management-administration"></a>Kontrolní seznam pro oprávnění pro správu

* Pokud obdržíte zprávu o odepření při konfiguraci oprávnění řízení přístupu a jste globální správce, ujistěte se, že má váš adresář [licence Azure AD Premium P2 (nebo EMS E5)](entitlement-management-overview.md#prerequisites).  
* Je-li získat zprávu při vytváření nebo zobrazení přístup balíčků o odepření přístupu a jsou členy skupiny katalogu creator, je nutné vytvořit katalog před vytvořením první přístup k balíčku.

## <a name="checklist-for-adding-a-resource"></a>Kontrolní seznam pro přidání prostředku

* Pokud existují uživatelé, které jsou už přiřazené k prostředku, který chcete spravovat s balíčkem aplikace přístup, ujistěte se, že uživatelé jsou přiřazeny k přístupu k balíčku s příslušnou zásadou. Například můžete chtít zahrnout skupiny, přístupu k balíčku, který už má uživatelů ve skupině. Pokud tito uživatelé ve skupině vyžadovat nadále přístup, musí mít příslušnou zásadu přístupu balíčků tak, aby o přístup ke skupině. Přístup k balíčku můžete přiřadit požádá uživatele o přístup k balíčku obsahující tento prostředek nebo přiřazením přímo k přístupu k balíčku. Další informace najdete v tématu [upravovat a spravovat existující balíček přístup](entitlement-management-access-package-edit.md).

## <a name="checklist-for-request-issues"></a>Kontrolní seznam pro požadavek problémy

* Když chce uživatel požádat o přístup k balíčku přístup, ujistěte se, že budou používat **Moje portálu propojte** pro přístup k balíčku. Další informace najdete v tématu [kopírování Moje portálu propojte](entitlement-management-access-package-edit.md#copy-my-access-portal-link).

* Když se uživatel přihlásí k portálu Můj přístup k žádosti přístup k balíčku, ujistěte se, že ověřování pomocí účtu organizace. Účet organizace může být buď účet v adresáři zdroje nebo do adresáře, který je součástí některou ze zásad přístupu k balíčku. Pokud uživatelský účet není účet organizace, nebo adresář není obsažena v zásadách, uživatel neuvidí přístup k balíčku. Další informace najdete v tématu [žádat o přístup k balíčku přístup](entitlement-management-request-access.md).

* Pokud uživatel je blokován přihlášení do adresáře zdrojů, nebudou moct požádat o přístup na portálu Moje přístup. Předtím, než uživatel může požádat o přístup, musíte odebrat blok přihlášení z profilu uživatele. Zrušit blokování přihlášení, na webu Azure Portal, klikněte na tlačítko **Azure Active Directory**, klikněte na tlačítko **uživatelé**, klepněte na uživatele a potom klikněte na **profilu**. Upravit **nastavení** a u **zablokovat přihlášení** k **ne**. Další informace najdete v tématu [přidat nebo aktualizovat informace o profilu uživatele pomocí služby Azure Active Directory](../fundamentals/active-directory-users-profile-azure-portal.md)

* Na portálu Moje přístup, pokud je uživatel žadatele a schvalovatele, neuvidí jejich žádost o přístup k balíčku na **schválení** stránky. Toto chování je záměrné – uživatel nemůže schválit požadavek na své vlastní. Ujistěte se, že přístup k balíčku požadují má další schvalovatele nakonfigurované v této zásadě. Další informace najdete v tématu [upravit existující zásadu](entitlement-management-access-package-edit.md#edit-an-existing-policy).

* Pokud nový externí uživatele, který nebyl dříve podepsaný ve vašem adresáři, obdrží balíček přístup, včetně webu Sharepointu Online, jejich přístup k balíčku zobrazí dodaným není plně až do svého účtu je zřízené v Sharepointu Online.

## <a name="next-steps"></a>Další postup

- [Zobrazení zpráv o tom, jak získávají uživatelé přístup ve správě nároku](entitlement-management-reports.md)
