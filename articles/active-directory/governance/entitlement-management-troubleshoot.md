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
ms.date: 05/30/2019
ms.author: rolyon
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: c2526ef10c3080dae1b32881a109a9436a0fd390
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66473820"
---
# <a name="troubleshoot-azure-ad-entitlement-management-preview"></a>Řešení potíží se správou oprávnění Azure AD (Preview)

> [!IMPORTANT]
> Správa nároků Azure Active Directory (Azure AD) je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tento článek popisuje některé položky, měli byste zkontrolovat při řešení potíží se správou oprávnění Azure Active Directory (Azure AD).

## <a name="checklist-for-entitlement-management-administration"></a>Kontrolní seznam pro oprávnění pro správu

* Pokud obdržíte zprávu o odepření při konfiguraci oprávnění řízení přístupu a jste globální správce, ujistěte se, že má váš adresář [licence Azure AD Premium P2 (nebo EMS E5)](entitlement-management-overview.md#license-requirements).  
* Je-li získat zprávu při vytváření nebo zobrazení přístup balíčků o odepření přístupu a jsou členy skupiny katalogu creator, je nutné vytvořit katalog před vytvořením první přístup k balíčku.

## <a name="checklist-for-adding-a-resource"></a>Kontrolní seznam pro přidání prostředku

* Pro aplikace prostředků, přístup k balíčku musí mít alespoň jednu roli prostředku, který je možné přiřadit. Role jsou definované vlastní aplikace a spravuje ve službě Azure AD. Všimněte si, že na webu Azure portal může také zobrazit instančních objektů pro služby, které nelze vybrat jako aplikace.  Zejména **Exchange Online** a **Sharepointu Online** jsou služby, ne aplikace, které mají role prostředků v adresáři, takže nemůže být součástí balíčku přístup.  Místo toho použijte licencování na základě skupiny vytvořit příslušnou licenci pro uživatele, kteří potřebují přístup k těmto službám.

* Pro skupinu prostředků, přístup k balíčku bude musí být schopen upravitelný ve službě Azure AD.  Skupiny, které pocházejí z místní služby Active Directory nelze přiřadit jako prostředky, protože jejich vlastník nebo atributy členu nelze změnit, ve službě Azure AD.  

* Jednotlivé dokumenty a Sharepointu Online knihovny dokumentů nelze přidat jako prostředky.  Místo toho vytvořte skupiny zabezpečení služby Azure AD, přístup k balíčku zahrnout skupiny a role lokality a v Sharepointu Online pomocí této skupiny pro řízení přístupu k dokumentu nebo knihovny dokumentů.

* Pokud existují uživatelé, které jsou už přiřazené k prostředku, který chcete spravovat s balíčkem aplikace přístup, ujistěte se, že uživatelé jsou přiřazeny k přístupu k balíčku s příslušnou zásadou. Například můžete chtít zahrnout skupiny, přístupu k balíčku, který už má uživatelů ve skupině. Pokud tito uživatelé ve skupině vyžadovat nadále přístup, musí mít příslušnou zásadu přístupu balíčků tak, aby o přístup ke skupině. Přístup k balíčku můžete přiřadit požádá uživatele o přístup k balíčku obsahující tento prostředek nebo přiřazením přímo k přístupu k balíčku. Další informace najdete v tématu [upravovat a spravovat existující balíček přístup](entitlement-management-access-package-edit.md).

## <a name="checklist-for-providing-external-users-access"></a>Kontrolní seznam pro poskytování přístupu externích uživatelů

* Pokud je B2B [seznamu povolených](../b2b/allow-deny-list.md), uživatelé, jejichž adresáře nejsou povoleny. nebude moct požádat o přístup.

* Ujistěte se, že neexistují žádné [zásady podmíněného přístupu](../conditional-access/require-managed-devices.md) , který by jinak znemožňovaly externí uživatelé si vyžádat přístup nebo nebudou moct používat aplikace v balíčcích přístup.

## <a name="checklist-for-request-issues"></a>Kontrolní seznam pro požadavek problémy

* Když chce uživatel požádat o přístup k balíčku přístup, ujistěte se, že budou používat **Moje portálu propojte** pro přístup k balíčku. Další informace najdete v tématu [kopírování Moje portálu propojte](entitlement-management-access-package-edit.md#copy-my-access-portal-link).

* Když se uživatel přihlásí k portálu Můj přístup k žádosti přístup k balíčku, ujistěte se, že ověřování pomocí účtu organizace. Účet organizace může být buď účet v adresáři zdroje nebo do adresáře, který je součástí některou ze zásad přístupu k balíčku. Pokud uživatelský účet není účet organizace, nebo adresář není obsažena v zásadách, uživatel neuvidí přístup k balíčku. Další informace najdete v tématu [žádat o přístup k balíčku přístup](entitlement-management-request-access.md).

* Pokud uživatel je blokován přihlášení do adresáře zdrojů, nebudou moct požádat o přístup na portálu Moje přístup. Předtím, než uživatel může požádat o přístup, musíte odebrat blok přihlášení z profilu uživatele. Zrušit blokování přihlášení, na webu Azure Portal, klikněte na tlačítko **Azure Active Directory**, klikněte na tlačítko **uživatelé**, klepněte na uživatele a potom klikněte na **profilu**. Upravit **nastavení** a u **zablokovat přihlášení** k **ne**. Další informace najdete v tématu [přidat nebo aktualizovat informace o profilu uživatele pomocí služby Azure Active Directory](../fundamentals/active-directory-users-profile-azure-portal.md).  Můžete také zkontrolovat, pokud uživatel byl zablokován z důvodu [zásad Identity Protection](../identity-protection/howto-unblock-user.md).

* Na portálu Moje přístup, pokud je uživatel žadatele a schvalovatele, neuvidí jejich žádost o přístup k balíčku na **schválení** stránky. Toto chování je záměrné – uživatel nemůže schválit požadavek na své vlastní. Ujistěte se, že přístup k balíčku požadují má další schvalovatele nakonfigurované v této zásadě. Další informace najdete v tématu [upravit existující zásadu](entitlement-management-access-package-edit.md#edit-an-existing-policy).

* Pokud nový externí uživatele, který nebyl dříve podepsaný ve vašem adresáři, obdrží balíček přístup, včetně webu Sharepointu Online, jejich přístup k balíčku zobrazí dodaným není plně až do svého účtu je zřízené v Sharepointu Online.

## <a name="next-steps"></a>Další postup

- [Zobrazení zpráv o tom, jak získávají uživatelé přístup ve správě nároku](entitlement-management-reports.md)
