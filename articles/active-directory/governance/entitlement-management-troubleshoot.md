---
title: Řešení potíží se správou nároků Azure AD (Preview) – Azure Active Directory
description: Přečtěte si o některých položkách, které byste měli ověřit, abyste vám pomohli vyřešit Azure Active Directory správu nároků (Preview).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/30/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea979731c27a8d332102c3215e80510994f2ab3f
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430234"
---
# <a name="troubleshoot-azure-ad-entitlement-management-preview"></a>Řešení potíží se správou nároků Azure AD (Preview)

> [!IMPORTANT]
> Správa opravňujících k Azure Active Directory (Azure AD) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tento článek popisuje některé položky, které byste měli ověřit, abyste mohli řešit problémy se správou oprávnění Azure Active Directory (Azure AD).

## <a name="checklist-for-entitlement-management-administration"></a>Kontrolní seznam pro správu správy nároků

* Pokud se při konfiguraci správy opravňují zobrazí zpráva o odepření přístupu a Vy jste globálním správcem, ujistěte se, že má váš adresář [Azure AD Premium licenci P2 (nebo EMS E5)](entitlement-management-overview.md#license-requirements).  
* Pokud se při vytváření nebo zobrazování balíčků pro přístup zobrazí zpráva o odepření přístupu a jste členem skupiny Creator Creator, musíte vytvořit katalog před vytvořením prvního přístupového balíčku.

## <a name="checklist-for-adding-a-resource"></a>Kontrolní seznam pro přidání prostředku

* Aby aplikace mohla být prostředkem v přístupovém balíčku, musí mít alespoň jednu roli prostředku, kterou je možné přiřadit. Role jsou definované aplikací a jsou spravované ve službě Azure AD. Všimněte si, že Azure Portal může také zobrazit instanční objekty pro služby, které nelze vybrat jako aplikace.  Konkrétně **Exchange Online** a **SharePoint Online** jsou služby, ne aplikace, které mají v adresáři role prostředků, takže je nejde zahrnout do balíčku pro přístup.  Místo toho použijte licencování na základě skupin k navázání vhodné licence pro uživatele, který potřebuje přístup k těmto službám.

* Aby skupina mohla být prostředkem v přístupovém balíčku, musí být schopna být upravitelná v Azure AD.  Skupiny, které pocházejí z místní služby Active Directory, se nedají přiřadit jako prostředky, protože jejich vlastník nebo atributy členů nejde změnit v Azure AD.   Skupiny, které pocházejí z Exchange Online jako distribuční skupiny, se nedají ve službě Azure AD upravovat ani v nich. 

* Knihovny dokumentů SharePointu Online a jednotlivé dokumenty nelze přidat jako prostředky.  Místo toho vytvořte skupinu zabezpečení Azure AD, zahrňte tuto skupinu a roli lokality do balíčku pro přístup a v SharePointu Online použijte tuto skupinu k řízení přístupu k knihovně dokumentů nebo dokumentu.

* Pokud jsou uživatelé, kteří už byli přiřazeni k prostředku, který chcete spravovat pomocí balíčku pro přístup, ujistěte se, že jsou uživatelé přiřazeni k balíčku přístupu s odpovídajícími zásadami. Například můžete chtít zahrnout skupinu do balíčku pro přístup, který již má uživatele ve skupině. Pokud tito uživatelé ve skupině potřebují pokračovat v přístupu, musí mít odpovídající zásady pro přístupové balíčky, aby neztratily přístup ke skupině. Balíček pro přístup můžete přiřadit tak, že požádáte uživatele, aby si vyžádali balíček pro přístup, který tento prostředek obsahuje, nebo ho přímo přiřadí do balíčku pro přístup. Další informace najdete v tématu [Nastavení žádostí o změnu a schvalování balíčku pro přístup](entitlement-management-access-package-request-policy.md).

## <a name="checklist-for-providing-external-users-access"></a>Kontrolní seznam pro poskytování přístupu externích uživatelů

* Pokud existuje [seznam povolených](../b2b/allow-deny-list.md)B2B, uživatelé, jejichž adresáře nejsou povolené, nebudou moct požádat o přístup.

* Zajistěte, aby nedocházelo k žádným [zásadám podmíněného přístupu](../conditional-access/require-managed-devices.md) , které by externím uživatelům zabránily v požadování přístupu nebo používání aplikací v balíčcích přístupu.

## <a name="checklist-for-request-issues"></a>Kontrolní seznam pro žádosti o problémy

* Když chce uživatel požádat o přístup k balíčku přístupu, ujistěte se, že používají **odkaz Můj portál přístupu** pro balíček přístupu. Další informace najdete v tématu [sdílení odkazu pro vyžádání balíčku přístupu](entitlement-management-access-package-settings.md).  Pokud **myAccess.Microsoft.com**navštíví externí uživatel, zobrazí se jim balíčky pro přístup k nim ve své vlastní organizaci.

* Když se uživatel, který ještě není ve vašem adresáři, přihlásí na portál My Access, aby si vyžádal balíček pro přístup, ujistěte se, že se ověřuje pomocí účtu organizace. Účet organizace může být buď účet v adresáři prostředků, nebo v adresáři, který je zahrnutý v jedné ze zásad balíčku přístupu. Pokud účet uživatele není účet organizace nebo pokud se v zásadě nezahrne adresář, ve kterém se ověřuje, uživatel neuvidí balíček pro přístup. Další informace najdete v tématu [vyžádání přístupu k balíčku přístupu](entitlement-management-request-access.md).

* Pokud se uživateli zablokuje přihlášení k adresáři prostředků, nebude moct požádat o přístup na portálu pro přístup. Než bude moct uživatel požádat o přístup, musíte z profilu uživatele odebrat blok přihlášení. Chcete-li odebrat blok přihlášení, klikněte v Azure Portal na **Azure Active Directory**, klikněte na **Uživatelé**, klikněte na uživatele a pak klikněte na **profil**. Upravte oddíl **Nastavení** a změňte **zablokovat přihlášení** na **ne**. Další informace najdete v tématu [Přidání nebo aktualizace informací o profilu uživatele pomocí Azure Active Directory](../fundamentals/active-directory-users-profile-azure-portal.md).  Můžete taky zjistit, jestli se uživatel zablokoval kvůli [zásadám ochrany identity](../identity-protection/howto-unblock-user.md).

* Pokud je uživatel v portálu pro přístup žadatelem i schvalovatelem, nezobrazí se na stránce **schválení** žádost o přístup k balíčku pro přístup. Toto chování je záměrné – uživatel nemůže schvalovat svoji vlastní žádost. Ujistěte se, že přístupový balíček, který požaduje, má pro tuto zásadu nakonfigurované další schvalovatele. Další informace najdete v tématu [Nastavení žádostí o změnu a schvalování balíčku pro přístup](entitlement-management-access-package-request-policy.md).

* Pokud nový externí uživatel, který se dřív nepodepsal v adresáři, obdrží balíček pro přístup, včetně webu SharePointu Online, zobrazí se jeho balíček pro přístup jako neúplně doručené, dokud se jejich účet nezřídí v SharePointu Online.

## <a name="next-steps"></a>Další kroky

- [Zobrazit zprávy o tom, jak uživatelé získali přístup ve správě nároků](entitlement-management-reports.md)
- [Řízení přístupu pro externí uživatele](entitlement-management-external-users.md)
