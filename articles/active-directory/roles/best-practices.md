---
title: Osvědčené postupy pro role Azure AD – Azure Active Directory
description: Osvědčené postupy pro používání rolí Azure Active Directory.
services: active-directory
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: conceptual
ms.date: 03/28/2021
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 78f4642b8f9f1ede65766a0026940c0af0f01ec2
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111560"
---
# <a name="best-practices-for-azure-ad-roles"></a>Osvědčené postupy pro role Azure AD

Tento článek popisuje některé z osvědčených postupů pro použití Azure Active Directory řízení přístupu na základě rolí (Azure AD RBAC). Tyto osvědčené postupy se odvozují z našeho prostředí pomocí služby Azure AD RBAC a zkušeností zákazníků, jako je sami. Doporučujeme vám také přečíst si podrobné pokyny k zabezpečení při [zabezpečení privilegovaného přístupu pro hybridní a cloudová nasazení v Azure AD](security-planning.md).

## <a name="1-manage-to-least-privilege"></a>1. Správa s nejnižším oprávněním

Při plánování strategie řízení přístupu je osvědčeným postupem Správa s nejnižšími oprávněními. Nejnižší oprávnění znamená, že správcům udělíte přesně oprávnění, která potřebují ke své práci. Existují tři aspekty, které je potřeba vzít v úvahu při přiřazování role správcům: konkrétní sada oprávnění v rámci určitého oboru, po určitou dobu. Nepřiřazujte širší role v širších oborech, i když se zpočátku zdá být pohodlnější. Omezením rolí a oborů omezíte, jaké prostředky budou ohroženy, pokud dojde k ohrožení zabezpečení objektu zabezpečení. Služba Azure AD RBAC podporuje více než 65 [integrovaných rolí](permissions-reference.md). Existují role Azure AD, které slouží ke správě objektů adresáře, jako jsou uživatelé, skupiny a aplikace, a také ke správě Microsoft 365 služeb, jako je Exchange, SharePoint a Intune. Pokud chcete lépe porozumět integrovaným rolím Azure AD, přečtěte si téma [Principy rolí v Azure Active Directory](concept-understand-roles.md). Pokud není k dispozici předdefinovaná role, která by splňovala vaši potřebu, můžete vytvořit vlastní [role](custom-create.md).  
 
### <a name="finding-the-right-roles"></a>Hledání správných rolí

Pomocí těchto kroků vám pomůžete najít správnou roli.

1. V Azure Portal otevřete [role a správci](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) a zobrazte seznam rolí Azure AD.

1. Seznam rolí můžete zúžit pomocí filtru **služby** .

    ![Stránka role a správci ve službě Azure AD s otevřeným filtrem služby](./media/best-practices/roles-administrators.png)

1. Přečtěte si dokumentaci k [předdefinovaným rolím Azure AD](permissions-reference.md) . Oprávnění přidružená k jednotlivým rolím jsou uvedena dohromady pro lepší čitelnost. Chcete-li pochopit strukturu a význam oprávnění role, přečtěte si téma [jak pochopit oprávnění role](permissions-reference.md#how-to-understand-role-permissions).

1. Projděte si dokumentaci k [nejmenším privilegovaným rolím podle úkolu](delegate-by-task.md) .

## <a name="2-use-privileged-identity-management-to-grant-just-in-time-access"></a>2. pomocí Privileged Identity Management udělte přístup za běhu.

Jedním ze zásad, které mají nejnižší oprávnění, je udělit přístup jenom za konkrétní časový úsek. [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) umožňuje správcům udělit přístup za běhu. Microsoft doporučuje povolit PIM ve službě Azure AD. Pomocí PIM může být uživateli vytvořen oprávněný člen role Azure AD. Pak může aktivovat své role pro omezený časový rámec pokaždé, když je potřebuje použít. Privilegovaný přístup se po vypršení časového období automaticky odebere. [Nastavení PIM](../privileged-identity-management/pim-how-to-change-default-settings.md) můžete také nakonfigurovat tak, aby vyžadovalo schválení nebo příjem e-mailů s oznámením, když někdo aktivuje přiřazení role. Oznámení poskytují upozornění, když se noví uživatelé přidají do vysoce privilegovaných rolí. 

## <a name="3-turn-on-multi-factor-authentication-for-all-your-administrator-accounts"></a>3. zapněte službu Multi-Factor Authentication pro všechny účty správců.

[Na základě našich studií](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)je váš účet 99,9% méně pravděpodobný, pokud použijete službu Multi-Factor Authentication (MFA). 
 
Vícefaktorové ověřování můžete povolit pro role Azure AD pomocí dvou metod:
- [Nastavení role](../privileged-identity-management/pim-how-to-change-default-settings.md) v Privileged Identity Management
- [Podmíněný přístup](../conditional-access/howto-conditional-access-policy-admin-mfa.md)

## <a name="4-configure-recurring-access-reviews-to-revoke-unneeded-permissions-over-time"></a>4. Konfigurace kontroly opakovaného přístupu pro odvolání nepotřebných oprávnění v průběhu času

Kontroly přístupu umožňují organizacím pravidelně kontrolovat přístup správce, aby měli jistotu, že budou mít přístup jenom přípravní uživatelé. Pravidelný audit správců je zásadní z následujících důvodů:
- Škodlivý objekt actor může ohrozit účet.
- Lidé přesouvá týmy v rámci společnosti. Pokud neexistuje žádné auditování, můžou amass zbytečný přístup v průběhu času.
 
Informace o kontrolách přístupu pro role najdete [v tématu Vytvoření kontroly přístupu rolí Azure AD v PIM](../privileged-identity-management/pim-how-to-start-security-review.md). Informace o kontrolách přístupu ke skupinám, které jsou přiřazenými rolemi, najdete v tématu [Vytvoření kontroly přístupu skupin a aplikací v recenzích přístupu Azure AD](../governance/create-access-review.md).

## <a name="5-limit-the-number-of-global-administrators-to-less-than-5"></a>5. omezte počet globálních správců na méně než 5

Osvědčeným postupem je, že společnost Microsoft doporučuje přiřadit roli globálního správce k **méně než pěti** lidem ve vaší organizaci. Globální správci uchovávají klíče do království a jsou ve vašem nejlepším zájmu pro zachování nízkého prostoru pro útoky. Jak bylo uvedeno dříve, všechny tyto účty by měly být chráněny službou Multi-Factor Authentication.

Když se uživatel přihlásí ke cloudové službě Microsoftu, vytvoří se ve výchozím nastavení tenant Azure AD a uživatel se stane členem role globální správci. Uživatelé, kteří mají přiřazenou roli globálního správce, můžou číst a upravovat všechna nastavení správy ve vaší organizaci Azure AD. S několika výjimkami můžou globální správci také číst a upravovat všechna nastavení konfigurace ve vaší organizaci Microsoft 365. Globální správci mají taky možnost zvýšit svůj přístup pro čtení dat.

Společnost Microsoft doporučuje, abyste zachovali dva účty pro oddělitelné sklo, které jsou trvale přiřazeny k roli globálního správce. Ujistěte se, že tyto účty nevyžadují stejný mechanismus Multi-Factor Authentication jako běžné účty pro správu, jak je popsáno v tématu [Správa účtů pro nouzový přístup ve službě Azure AD](../roles/security-emergency-access.md). 

## <a name="6-use-groups-for-azure-ad-role-assignments-and-delegate-the-role-assignment"></a>6. použijte skupiny pro přiřazení rolí Azure AD a předelegovat přiřazení role.

Pokud máte externí systém zásad správného řízení, který využívá skupiny, měli byste zvážit přiřazování rolí do skupin Azure AD místo jednotlivých uživatelů. Můžete také spravovat role a přiřadit skupiny v PIM, aby se zajistilo, že v těchto privilegovaných skupinách nejsou žádní stálí vlastníci nebo členové. Další informace najdete v tématu [Možnosti správy pro privilegovaný přístup skupiny Azure AD](../privileged-identity-management/groups-features.md).

Můžete přiřadit vlastníka ke skupinám, které umožňují přiřazení rolí. Tento vlastník rozhoduje o tom, kdo je ve skupině přidaný nebo odebraný, takže se nepřímo rozhodne, kdo získá přiřazení role. Tímto způsobem může správce globálních správců nebo privilegovaných rolí delegovat správu rolí na základě jednotlivých rolí pomocí skupin. Další informace najdete v tématu [použití skupin cloudu ke správě přiřazení rolí v Azure Active Directory](groups-concept.md).

## <a name="7-activate-multiple-roles-at-once-using-privileged-access-groups"></a>7. pomocí privilegovaných skupin přístupu aktivujte víc rolí najednou.

Může to být případ, že jednotlivec má pro role Azure AD prostřednictvím PIM pět nebo šest oprávněných přiřazení. Budou muset aktivovat jednotlivé role jednotlivě, což může snížit produktivitu. V horším případě jsou k dispozici také desítky nebo stovky prostředků Azure, což tyto problémy snižuje.
 
V takovém případě byste měli použít [přístupové skupiny s oprávněním](../privileged-identity-management/groups-features.md). Vytvořte skupinu privilegovaný přístup a udělte jí trvalý přístup k více rolím (Azure AD a/nebo Azure). Nastavit tohoto uživatele jako oprávněného člena nebo vlastníka této skupiny. Jenom s jednou aktivací budou mít přístup ke všem připojeným prostředkům.

![Diagram privilegovaného přístupu, který ukazuje aktivaci více rolí najednou](./media/best-practices/privileged-access-group.png)

## <a name="8-use-cloud-native-accounts-for-azure-ad-roles"></a>8. použití cloudových nativních účtů pro role Azure AD

Vyhněte se použití místních synchronizovaných účtů pro přiřazení rolí Azure AD. Pokud dojde k ohrožení bezpečnosti vašeho místního účtu, může to ohrozit i vaše prostředky Azure AD.

## <a name="next-steps"></a>Další kroky

- [Zabezpečení privilegovaného přístupu pro hybridní a cloudová nasazení v Azure AD](security-planning.md)