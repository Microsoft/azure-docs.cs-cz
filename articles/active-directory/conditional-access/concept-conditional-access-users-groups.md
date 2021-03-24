---
title: Uživatelé a skupiny v zásadách podmíněného přístupu – Azure Active Directory
description: Kdo jsou uživatelé a skupiny v zásadách podmíněného přístupu Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e1f4c7272c3db3b1e4cd834a621b66f519c6f69
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952809"
---
# <a name="conditional-access-users-and-groups"></a>Podmíněný přístup: uživatelé a skupiny

Zásady podmíněného přístupu musí zahrnovat přiřazení uživatele jako jeden ze signálů v rámci procesu rozhodování. Uživatele můžete zahrnout nebo vyloučit ze zásad podmíněného přístupu. Azure Active Directory vyhodnotí všechny zásady a zajistí splnění všech požadavků před udělením přístupu uživateli. 

> [!VIDEO https://www.youtube.com/embed/5DsW1hB3Jqs]

## <a name="include-users"></a>Zahrnutí uživatelů

Tento seznam uživatelů obvykle zahrnuje všechny uživatele, kterým organizace cílí v zásadách podmíněného přístupu. 

Při vytváření zásad podmíněného přístupu jsou k dispozici následující možnosti:

- Žádné
   - Nejsou vybráni žádní uživatelé.
- Všichni uživatelé
   - Všichni uživatelé, kteří existují v adresáři včetně hostů v B2B.
- Výběr uživatelů a skupin
   - Všichni uživatelé typu Host a externí uživatelé
      - Tento výběr zahrnuje všechny hosty a externí uživatele B2B včetně všech uživatelů s `user type` atributem nastaveným na `guest` . Tento výběr platí také pro všechny přihlášené externí uživatele z jiné organizace, jako je například poskytovatel Cloud Solution Provider (CSP). 
   - Role adresáře
      - Umožňuje správcům vybrat konkrétní integrované role adresáře Azure AD, které se používají k určení přiřazení zásad. Organizace můžou například vytvořit přísnější zásadu pro uživatele, kteří mají přiřazenou roli globálního správce. Jiné typy rolí nejsou podporované, včetně rolí pro správu v oboru jednotky a vlastní role.
   - Uživatelé a skupiny
      - Umožňuje zaměřit se na konkrétní skupiny uživatelů. Organizace můžou například vybrat skupinu, která obsahuje všechny členy oddělení lidských zdrojů, když je jako cloudová aplikace vybraná aplikace pro HR. Skupina může být libovolný typ skupiny v Azure AD, včetně dynamických nebo přiřazených skupin zabezpečení a distribuce. Zásady se použijí pro vnořené uživatele a skupiny.

> [!IMPORTANT]
> Když vybíráte, kteří uživatelé a skupiny jsou součástí zásad podmíněného přístupu, je limit počtu jednotlivých uživatelů, kteří se dají přidat přímo do zásad podmíněného přístupu. Pokud je potřeba přidat velké množství individuálních uživatelů, kteří jsou přidaní přímo do zásad podmíněného přístupu, doporučujeme umístit uživatele do skupiny a místo toho přiřadit skupinu k zásadám podmíněného přístupu.

> [!WARNING]
> Pokud jsou uživatelé nebo skupiny členy více než 2048 skupin, jejich přístup může být blokovaný. Toto omezení platí pro přímé i vnořené členství ve skupinách.

> [!WARNING]
> Zásady podmíněného přístupu nepodporují uživatelům, kteří mají přiřazenou roli adresáře v [oboru pro jednotky pro správu](../roles/admin-units-assign-roles.md) nebo role adresáře přímo na objekt, jako je například prostřednictvím [vlastních rolí](../roles/custom-create.md).

## <a name="exclude-users"></a>Vyloučení uživatelů

Pokud organizace zahrnují a vylučují uživatele nebo skupinu, uživatel nebo skupina je ze zásad vyloučená, protože akce vyloučení Přepisuje zahrnutí v zásadě. Vyloučení se běžně používají pro nouzový přístup nebo pro účty pro rozbití. Další informace o účtech pro nouzový přístup a o tom, proč jsou důležité, najdete v následujících článcích: 

* [Správa účtů pro nouzový přístup v Azure AD](../roles/security-emergency-access.md)
* [Vytvoření odolné strategie správy řízení přístupu pomocí Azure Active Directory](../authentication/concept-resilient-controls.md)

Při vytváření zásad podmíněného přístupu je k dispozici možnost vyloučit následující možnosti.

- Všichni uživatelé typu Host a externí uživatelé
   - Tento výběr zahrnuje všechny hosty a externí uživatele B2B včetně všech uživatelů s `user type` atributem nastaveným na `guest` . Tento výběr platí také pro všechny přihlášené externí uživatele z jiné organizace, jako je například poskytovatel Cloud Solution Provider (CSP). 
- Role adresáře
   - Umožňuje správcům vybrat konkrétní role adresáře Azure AD, které se používají k určení přiřazení. Organizace můžou například vytvořit přísnější zásadu pro uživatele, kteří mají přiřazenou roli globálního správce.
- Uživatelé a skupiny
   - Umožňuje zaměřit se na konkrétní skupiny uživatelů. Organizace můžou například vybrat skupinu, která obsahuje všechny členy oddělení lidských zdrojů, když je jako cloudová aplikace vybraná aplikace pro HR. Skupina může být libovolný typ skupiny v Azure AD, včetně dynamických nebo přiřazených skupin zabezpečení a distribuce.

### <a name="preventing-administrator-lockout"></a>Prevence uzamčení správce

Pokud chcete zabránit tomu, aby správce při vytváření zásad použitých pro **všechny uživatele** a **všechny aplikace** uzamknul svůj adresář, zobrazí se následující upozornění.

> Nezamykat! Doporučujeme, abyste nejdřív použili zásadu pro malou skupinu uživatelů, abyste ověřili, že se chová podle očekávání. Doporučujeme také, abyste z těchto zásad vyloučili alespoň jednoho správce. Tím zajistíte, že budete mít stále přístup a budete moct aktualizovat zásady, pokud je potřeba změnit. Přečtěte si prosím ovlivněné uživatele a aplikace.

Ve výchozím nastavení zásada nabídne možnost vyloučení aktuálního uživatele ze zásad, ale toto nastavení může přepsat správce, jak je znázorněno na následujícím obrázku. 

![Upozornění, neprovádějte zámek!](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups-lockout-warning.png)

Pokud se vám při[nedostatku Azure Portal zamknete, co dělat?](troubleshoot-conditional-access.md#what-to-do-if-you-are-locked-out-of-the-azure-portal)

## <a name="next-steps"></a>Další kroky

- [Podmíněný přístup: cloudové aplikace nebo akce](concept-conditional-access-cloud-apps.md)

- [Společné zásady podmíněného přístupu](concept-conditional-access-policy-common.md)
