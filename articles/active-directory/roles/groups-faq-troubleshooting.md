---
title: Řešení potíží s rolemi přiřazenými ke cloudové skupině Nejčastější dotazy – Azure Active Directory | Microsoft Docs
description: Seznamte se s některými běžnými dotazy a tipy pro řešení potíží při přiřazování rolí do skupin v Azure Active Directory.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/13/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 837750d7eeef9bc7a133a54b23a0c52c847364eb
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92375992"
---
# <a name="troubleshooting-roles-assigned-to-cloud-groups"></a>Řešení potíží s rolemi přiřazenými ke cloudovým skupinám

Tady jsou některé běžné dotazy a tipy pro řešení potíží při přiřazování rolí do skupin v Azure Active Directory (Azure AD).

**Otázka:** Jsem správcem skupin, ale nemůžem zobrazit **role Azure AD, které je možné přiřadit k** přepínači skupiny.

**A:** Pouze správci privilegovaných rolí nebo globální Správci mohou vytvořit skupinu, která bude mít nárok na přiřazení role. Pouze uživatelé v těchto rolích uvidí tento ovládací prvek.

**Otázka:** Kdo může upravit členství ve skupinách, které jsou přiřazené k rolím Azure AD?

**A:** Ve výchozím nastavení pouze správce privilegovaných rolí a globální správce spravují členství skupiny, která je přiřazena rolím, ale můžete delegovat správu skupin s možností přiřazení role přidáním vlastníků skupin.

**Otázka**: jsem správcem helpdesku v mé organizaci, ale nemohu aktualizovat heslo uživatele, který je čtečkou adresářů. Proč k tomu dochází?

Odpověď **: uživatel**může narazit na čtečku adresářů pomocí skupiny přiřazení role. Všechny členy a vlastníci skupin, které lze přiřadit k rolím, jsou chráněni. Přihlašovací údaje pro chráněného uživatele můžou resetovat jenom uživatelé v rolích správce privilegovaného ověřování nebo globální správce.

**Otázka:** Nemohu aktualizovat heslo uživatele. Nemají přiřazenou žádnou vyšší privilegovanou roli. Proč se to děje?

**A:** Uživatel může být vlastníkem skupiny přiřazení role. Chráníme vlastníky rolí a přiřazení skupin, abyste se vyhnuli zvýšení oprávnění. Příkladem může být, pokud je skupina Contoso_Security_Admins přiřazena k roli správce zabezpečení, kde Jan je vlastníkem skupiny a Alice je správcem hesel v organizaci. Pokud tato ochrana nebyla přítomna, mohl by Alice resetovat přihlašovací údaje Bobovi a převzít její identitu. Potom Alice může přidat do skupiny skupinu Contoso_Security_Admins, aby se stala správcem zabezpečení v organizaci. Pokud chcete zjistit, jestli je uživatel vlastníkem skupiny, získejte seznam vlastněných objektů daného uživatele a podívejte se, jestli má některá z těchto skupin isAssignableToRole nastavenou na hodnotu true. Pokud ano, pak je tento uživatel chráněný a chování je záměrné. Informace o tom, jak vlastní objekty získat, najdete v těchto dokumentech:

- [Get-AzureADUserOwnedObject](/powershell/module/azuread/get-azureaduserownedobject?view=azureadps-2.0)  
- [Seznam ownedObjects](/graph/api/user-list-ownedobjects?tabs=http&view=graph-rest-1.0)

**Otázka:** Můžu vytvořit kontrolu přístupu pro skupiny, které se dají přiřadit k rolím Azure AD (konkrétně skupiny s vlastností isAssignableToRole nastavenou na hodnotu true)?  

**A:** Ano, můžete. Pokud pracujete v nejnovější verzi kontroly přístupu, pak vaši kontroloři budou ve výchozím nastavení přesměrováni na můj přístup a pouze globální Správci mohou vytvářet kontroly přístupu u skupin s rolí přiřazení. Pokud však používáte starší verzi kontroly přístupu, pak jsou kontroloři přesměrováni na přístupový panel ve výchozím nastavení a globální správci i správci uživatelů mohou vytvářet kontroly přístupu u skupin s přiřazením rolí. Nové prostředí bude zavedeno pro všechny zákazníky 28. července 2020, ale pokud byste chtěli upgradovat dřív, vyžádejte si [v registraci přístupu požadavek na kontrolu přístupu k Azure AD – aktualizované možnosti revidujícího](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u).

**Otázka:** Můžu vytvořit balíček pro přístup a skupiny, které se v něm dají přiřadit k rolím Azure AD?

**A:** Ano, můžete. Globální správce a Správce uživatelů mají v balíčku pro přístup oprávnění k uvedení jakékoli skupiny. U globálního správce se nic nemění, ale oprávnění role Správce uživatelů se mírně mění. Chcete-li do balíčku pro přístup umístit skupinu s přiřazením role, musíte být správcem uživatele a také vlastníkem skupiny role-přiřazení. Tady je celá tabulka, která ukazuje, kdo může vytvořit balíček přístupu ve správě licencí Enterprise:

Role adresáře Azure AD | Role správy oprávnění | Může přidat skupinu zabezpečení.\* | Může přidat skupinu Microsoft 365.\* | Může přidat aplikaci. | Může přidat web SharePointu Online.
----------------------- | --------------------------- | ----------------------- | ------------------------- | ----------- |  -----------------------------
Globální správce | neuvedeno | ✔️ | ✔️ | ✔️  | ✔️
Správce uživatelů  | neuvedeno  | ✔️  | ✔️  | ✔️
Správce Intune | Vlastník katalogu | ✔️  | ✔️  | &nbsp;  | &nbsp;
Správce Exchange  | Vlastník katalogu  | &nbsp; | ✔️  | &nbsp;  | &nbsp;
Správce služby Teams | Vlastník katalogu  | &nbsp; | ✔️  | &nbsp;  | &nbsp;
Správce SharePointu | Vlastník katalogu | &nbsp; | ✔️  | &nbsp;  | ✔️ 
Správce aplikace | Vlastník katalogu  | &nbsp;  | &nbsp; | ✔️  | &nbsp;
Správce cloudové aplikace | Vlastník katalogu  | &nbsp;  | &nbsp; | ✔️  | &nbsp;
Uživatel | Vlastník katalogu | Pouze v případě, že vlastník skupiny | Pouze v případě, že vlastník skupiny | Jenom v případě, že vlastník aplikace  | &nbsp;

\*Skupina není přiřazena role; To znamená, že isAssignableToRole = false. Pokud má skupina přiřazenou roli, musí mít osoba, která vytvořila balíček pro přístup, taky vlastníka skupiny s přiřazením rolí.

**Otázka:** V části přiřazené role nemůžu najít možnost Remove Assignment (odebrat přiřazení). Návody odstranit přiřazení role uživateli?

**A:** Tato odpověď se vztahuje pouze na organizace Azure AD Premium P1.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a otevřete **Azure Active Directory**.
1. Vyberte uživatelé a otevřete profil uživatele.
1. Vyberte **přiřazené role**.
1. Vyberte ikonu ozubeného kolečka. Otevře se podokno, které může tyto informace poskytnout. Vedle přímých přiřazení je tlačítko Odebrat. Chcete-li odebrat přiřazení nepřímých rolí, odeberte uživatele ze skupiny, které byla přiřazena role.

**Otázka:** Návody se zobrazí všechny skupiny, které jsou přiřadit roli?

**A:** Použijte následující postup:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a otevřete **Azure Active Directory**.
1. Vyberte **skupiny**  >  **všechny skupiny**.
1. Vyberte **Přidat filtry**.
1. Filtrovat podle **přiřazení role**

**Otázka:** Návody víte, která role je přímo nebo nepřímo přiřazená k objektu zabezpečení?

**A:** Použijte následující postup:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a otevřete **Azure Active Directory**.
1. Vyberte uživatelé a otevřete profil uživatele.
1. Vyberte **přiřazené role**a potom:

    - V Azure AD Premium licencovaných organizacích P1: vyberte ikonu ozubeného kolečka. Otevře se podokno, které může tyto informace poskytnout.
    - V Azure AD Premium licencovaných organizacích: ve sloupci **členství** najdete přímo a zděděné informace o licencích.

**Otázka:** Proč vynutili vytvoření nové cloudové skupiny pro přiřazení k roli?  

**A:** Pokud přiřadíte existující skupinu k roli, stávající vlastník skupiny může do této skupiny přidat další členy, aniž by Tito noví členové měli roli. Vzhledem k tomu, že skupiny s přiřazením rolí jsou výkonné, zadáváme spoustu omezení, která je chrání. Nechcete, aby se změny skupiny překvapivé na osobu, která spravuje skupinu.

## <a name="next-steps"></a>Další kroky

- [Použití skupin cloudů ke správě přiřazení rolí](groups-concept.md)
- [Vytvoření skupiny s možností přiřazení rolí](groups-create-eligible.md)