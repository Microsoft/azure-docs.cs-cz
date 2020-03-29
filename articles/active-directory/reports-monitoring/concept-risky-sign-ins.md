---
title: Zpráva o rizikových přihlášeních na portálu | Dokumenty společnosti Microsoft
description: Informace o sestavách rizikových přihlášení na portálu Azure Active Directory
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 7728fcd7-3dd5-4b99-a0e4-949c69788c0f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/04/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: b77486064139895799ac5a48327377154f75da6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273844"
---
# <a name="risky-sign-ins-report-in-the-azure-active-directory-portal"></a>Sestavy rizikových přihlášení na portálu Azure Active Directory

Azure Active Directory (Azure AD) detekuje podezřelé akce, které souvisejí s vašimi uživatelskými účty. Pro každou zjištěnou akci je vytvořen záznam nazývaný **zjišťování rizik.** Další podrobnosti najdete [v tématu Azure AD detekce rizik](concept-risk-events.md). 

K sestavám zabezpečení z [portálu Azure](https://portal.azure.com) se dostanete tak, že vyberete okno **Azure Active Directory** a pak přejdete do části **Zabezpečení.** 

Existují dvě různé zprávy o zabezpečení, které jsou vypočteny na základě detekce rizik:

- **Riziková přihlášení** – Rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu.

- **Uživatelé označení příznakem rizika** – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený. 

![Riziková přihlášení](./media/concept-risky-sign-ins/10.png)

Informace o konfiguraci zásad, které tato zjišťování rizik aktivují, naleznete [v tématu Konfigurace zásad rizik pro uživatele](../identity-protection/howto-user-risk-policy.md).  

## <a name="who-can-access-the-risky-sign-ins-report"></a>Kdo má přístup k přehledu rizikových přihlášení?

Rizikové sestavy přihlášení jsou k dispozici uživatelům v následujících rolích:

- Správce zabezpečení
- Globální správce
- Čtečka zabezpečení

Informace o tom, jak přiřadit role pro správu uživateli ve službě Azure Active Directory, najdete v tématu [Zobrazení a přiřazení rolí správce ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-manage-roles-portal).

## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>Jaká licence Azure AD je potřeba pro přístup k sestavě zabezpečení?  

Všechny edice Azure AD poskytují rizikové sestavy přihlášení. Úroveň podrobností sestav se však mezi jednotlivými edicemi liší: 

- V **edici Azure Active Directory Free**získáte seznam rizikových přihlášení. 

- Kromě toho **azure Active Directory Premium 1** vydání umožňuje prozkoumat některé základní zjišťování rizik, které byly zjištěny pro každou sestavu. 

- **Edice Azure Active Directory Premium 2** poskytuje nejpodrobnější informace o všech základních zjišťovánírizik a také umožňuje konfigurovat zásady zabezpečení, které automaticky reagují na nakonfigurované úrovně rizika.

## <a name="risky-sign-ins-report-for-azure-ad-free-edition"></a>Sestava rizikových přihlášení pro bezplatnou edici Azure AD

Azure AD free edition vám poskytne seznam rizikových přihlášení, které byly zjištěny pro vaše uživatele. Každý záznam obsahuje následující atributy:

- **Uživatel** - jméno uživatele, který byl použit během operace přihlášení.
- **IP** – IP adresa zařízení, které bylo použito k připojení ke službě Azure Active Directory.
- **Umístění** – umístění používané pro připojení ke službě Azure Active Directory. Jedná se o nejlepší úsilí aproximace na základě trasování, data registru, reverzní vyhledávání a další informace.
- **Čas přihlášení** – Čas, kdy k přihlášení došlo
- **Stav** – Stav přihlášení

![Riziková přihlášení](./media/concept-risky-sign-ins/01.png)

Na základě vašeho šetření rizikovépřihlášení, můžete poskytnout zpětnou vazbu na Azure AD provedením následujících akcí:

- Vyřešit
- Označit jako falešně pozitivní
- Ignorovat
- Znovu aktivovat

![Riziková přihlášení](./media/concept-risky-sign-ins/21.png)

Tato sestava také poskytuje možnost:

- Prohledávat prostředky
- Stáhnout data sestavy

![Riziková přihlášení](./media/concept-risky-sign-ins/93.png)

## <a name="risky-sign-ins-report-for-azure-ad-premium-editions"></a>Sestava rizikových přihlášení pro edice Azure AD premium

Přehled rizikových přihlášení v edicích Azure AD premium poskytuje:

- Souhrnné informace o [typech detekce rizik,](concept-risk-events.md) které byly zjištěny. V **edici Azure AD Premium P1**se zjišťování, na která se licence nevztahuje, zobrazí jako přihlášení ke zjišťování **rizika s dalším zjištěným rizikem**. S **edicí Azure AD Premium P2**získáte nejpodrobnější informace o všech základních detekcích.

- Možnost stažení sestavy

![Riziková přihlášení](./media/concept-risky-sign-ins/456.png)

Když vyberete detekci rizik, získáte podrobné zobrazení sestavy pro toto zjišťování rizik, které vám umožní:

- Výběr možnosti konfigurace [zásad odstraňování rizik uživatelů](../identity-protection/howto-user-risk-policy.md)  

- Zkontrolujte časovou osu detekce pro detekci rizik  

- Zkontrolujte seznam uživatelů, u kterých byla zjištěna tato detekce rizik.

- Ručně zastavte detekci rizik. 

![Riziková přihlášení](./media/concept-risky-sign-ins/457.png)

> [!IMPORTANT]
> V některých případě můžete najít detekci rizik bez odpovídající přihlášení v [sestavě přihlášení](concept-sign-ins.md). Je to proto, že ochrana identity vyhodnocuje riziko pro **interaktivní** i **neinteraktivní** přihlášení, zatímco sestava přihlášení zobrazuje pouze interaktivní přihlášení.

Po výběru uživatele získáte podrobné zobrazení sestavy pro tohoto uživatele, které vám umožňuje:

- Otevřít zobrazení Všechna přihlášení

- Resetovat heslo uživatele

- Zavřít všechny události

- Prozkoumejte hlášené detekce rizik pro uživatele. 

![Riziková přihlášení](./media/concept-risky-sign-ins/324.png)

Chcete-li prozkoumat detekci rizik, vyberte jednu ze seznamu.  
Tím se otevře okno **Podrobnosti** pro tuto detekci rizik. V okně **Podrobnosti** máte možnost buď ručně uzavřít detekci rizik, nebo znovu aktivovat ručně uzavřenou detekci rizik. 

![Riziková přihlášení](./media/concept-risky-sign-ins/325.png)

## <a name="next-steps"></a>Další kroky

- [Jak nakonfigurovat zásady rizika pro uživatele](../identity-protection/howto-user-risk-policy.md)
- [Jak nakonfigurovat zásadu nápravy rizik](../identity-protection/howto-user-risk-policy.md)
- [Typy detekce rizik](concept-risk-events.md)
