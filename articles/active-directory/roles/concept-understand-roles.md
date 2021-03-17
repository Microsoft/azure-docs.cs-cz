---
title: Principy Azure Active Directory konceptů rolí
description: Naučte se pochopit Azure Active Directory předdefinované a vlastní role s oborem prostředků v Azure Active Directory.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 11/20/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71291458eec0aec13542d3e0dfaf04a96f391a23
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/15/2021
ms.locfileid: "103466112"
---
# <a name="understand-roles-in-azure-active-directory"></a>Pochopení rolí v Azure Active Directory

K dispozici jsou přibližně 60 Azure Active Directory (Azure AD) předdefinovaných rolí, což jsou role s pevnou sadou oprávnění role. Aby bylo možné doplnit předdefinované role, Azure AD podporuje také vlastní role. Pomocí vlastních rolí vyberte oprávnění role, která chcete. Můžete ho například vytvořit pro správu konkrétních prostředků služby Azure AD, jako jsou aplikace nebo instanční objekty.

Tento článek vysvětluje, co jsou role Azure AD a jak se dají použít.

## <a name="how-azure-ad-roles-are-different-from-other-microsoft-365-roles"></a>Jak se role Azure AD liší od jiných rolí Microsoft 365

V Microsoft 365 existuje celá řada různých služeb, jako je například Azure AD a Intune. Některé z těchto služeb mají své vlastní systémy řízení přístupu založené na rolích; určen

- Azure AD
- Výměna
- Intune
- Security Center
- Centrum dodržování předpisů
- Microsoft Cloud App Security
- Obchod

Jiné služby, jako jsou týmy, SharePoint a spravovaná plocha, nemají samostatné systémy pro řízení přístupu založené na rolích. Používají role Azure AD pro přístup správce. Azure má svůj vlastní systém řízení přístupu založený na rolích pro prostředky Azure, jako jsou virtuální počítače, a tento systém není stejný jako role Azure AD.

Když říkáte samostatný systém řízení přístupu založený na rolích. znamená to, že existuje jiné úložiště dat, kde jsou uloženy definice rolí a přiřazení rolí. Podobně je k dispozici jiný bod rozhodování o zásadách, ve kterém se kontroly přístupu vyskytují. Další informace najdete v tématu [role pro Microsoft 365 služby ve službě Azure AD](m365-workload-docs.md) a [klasické role Správce předplatného, role Azure a role Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="why-some-azure-ad-roles-are-for-other-services"></a>Proč některé role Azure AD jsou pro další služby

Microsoft 365 má řadu systémů pro řízení přístupu založených na rolích, které se v průběhu času vyvíjely nezávisle, a to z vlastního portálu služby. Aby bylo vhodné spravovat identitu napříč Microsoft 365 z portálu Azure AD, Přidali jsme některé předdefinované role specifické pro službu, z nichž každý uděluje přístup pro správu ke službě Microsoft 365. Příkladem tohoto přidání je role správce Exchange ve službě Azure AD. Tato role je rovnocenná [skupině rolí správy organizace](/exchange/organization-management-exchange-2013-help) v systému řízení přístupu na základě role Exchange a může spravovat všechny aspekty Exchange. Podobně jsme přidali roli správce Intune, správce týmů, správce SharePointu atd. Role specifické pro službu jsou jedna kategorie předdefinovaných rolí Azure AD v následující části.

## <a name="categories-of-azure-ad-roles"></a>Kategorie rolí Azure AD

Předdefinované role Azure AD se liší v umístění, kde je lze použít, které spadají do následujících tří hlavních kategorií.

- **Role specifické pro Azure AD**: tyto role udělují oprávnění ke správě prostředků jenom v rámci Azure AD. Například správce uživatele, Správce aplikací, skupiny správců udělí oprávnění ke správě prostředků, které žijí v Azure AD.
- **Role specifické pro službu**: pro hlavní Microsoft 365 služby (mimo Azure AD) jsme vytvořili role specifické pro službu, které udělují oprávnění ke správě všech funkcí v rámci služby.  Role správce serveru Exchange, Správce služby Intune, správce SharePointu a týmy můžou například spravovat funkce s příslušnými službami. Správce Exchange může spravovat poštovní schránky, správce Intune může spravovat zásady zařízení, Správce služby SharePoint může spravovat kolekce webů, správce týmů může spravovat kvality volání a tak dále.
- **Role mezi** službami: existují nějaké role zahrnující služby. Máme dvě globální role – Globální správce a globální čtenář. Tyto dvě role dodržují všechny Microsoft 365 služby. K dispozici jsou také některé role související se zabezpečením, jako je správce zabezpečení a čtecí modul zabezpečení, který uděluje přístup přes více služeb zabezpečení v rámci Microsoft 365. Pokud například používáte role správce zabezpečení ve službě Azure AD, můžete spravovat Microsoft 365 Security Center, antivirovou ochranu v programu Microsoft Defender a Microsoft Cloud App Security. Podobně platí, že v roli správce dodržování předpisů můžete spravovat nastavení související s dodržováním předpisů v Microsoft 365 centrum dodržování předpisů, Exchange a tak dále.

![Tři kategorie předdefinovaných rolí Azure AD](./media/concept-understand-roles/role-overlap-diagram.png)

Následující tabulka se nabízí jako pomůcka pro porozumění těmto kategoriím rolí. Kategorie jsou pojmenovány libovolně a nemají za cíl zahrnovat jiné funkce nad rámec [oprávnění zdokumentované role](permissions-reference.md).

Kategorie | Role
---- | ----
Role specifické pro Azure AD | Správce aplikace<br>Vývojář aplikace<br>Správce ověřování<br>B2C IEF, správce sady klíčů<br>Správce zásad IEF B2C<br>Správce cloudové aplikace<br>Správce cloudového zařízení<br>Správce podmíněného přístupu<br>Správci služeb<br>Čtečky adresářů<br>Účty synchronizace adresářů<br>Zapisovače adresářů<br>Správce toku externího ID uživatele<br>Správce atributů toku uživatele externího ID<br>Správce externích zprostředkovatelů identity<br>Správce skupin<br>Pozvánka hosta<br>Správce helpdesku<br>Správce hybridní identity<br>Správce licencí<br>Podpora partnerů Tier1<br>Podpora partnerů 2<br>Správce hesel<br>Správce privilegovaného ověřování<br>Správce privilegovaných rolí<br>Čtečka sestav<br>Správce účtu uživatele
Role mezi službami | Globální správce<br>Správce dodržování předpisů<br>Správce dat dodržování předpisů<br>Globální čtenář<br>Správce zabezpečení<br>Operátor zabezpečení<br>Čtenář zabezpečení<br>Správce služby Service support
Role specifické pro službu | Správce Azure DevOps<br>Správce Azure Information Protection<br>Správce fakturace<br>Správce služby CRM<br>Schvalovatel přístupu k bezpečnostnímu modulu zákazníka<br>Správce Desktop Analytics<br>Správce služby Exchange<br>Správce Insights<br>Vedoucí firmy pro Insights<br>Správce služby Intune<br>Správce Kaizala<br>Správce služby Lync<br>Čtenář ochrany osobních údajů centra zpráv<br>Čtenář centra zpráv<br>Moderní uživatel pro obchodování<br>Správce sítě<br>Správce aplikací Office<br>Správce služby Power BI<br>Správce Power Platform<br>Správce tiskárny<br>Technik tiskárny<br>Správce hledání<br>Editor hledání<br>Správce služby SharePoint<br>Správce komunikace týmů<br>Týmy Communications support inženýr<br>Týmy Communications support specialisty<br>Správci zařízení Teams<br>Správce služby Teams

## <a name="next-steps"></a>Další kroky

- [Přehled řízení přístupu na základě role Azure AD](custom-overview.md)
- Vytvoření přiřazení rolí pomocí [Azure Portal, Azure AD PowerShellu a Graph API](custom-create.md)
- [Zobrazení seznamu přiřazení rolí](view-assignments.md)
