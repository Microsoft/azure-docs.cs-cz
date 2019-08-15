---
title: Sestavy rizikových přihlášení na portálu Azure Active Directory | Dokumentace Microsoftu
description: Informace o sestavách rizikových přihlášení na portálu Azure Active Directory
services: active-directory
author: cawrites
manager: daveba
ms.assetid: 7728fcd7-3dd5-4b99-a0e4-949c69788c0f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 629e7bf8ee7100b5927483ecfd4efa1d9223c151
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989927"
---
# <a name="risky-sign-ins-report-in-the-azure-active-directory-portal"></a>Sestavy rizikových přihlášení na portálu Azure Active Directory

Azure Active Directory (Azure AD) detekuje podezřelé akce, které souvisejí s vašimi uživatelskými účty. Pro každou zjištěnou akci se vytvoří záznam s názvem **riziková událost** . Další podrobnosti najdete v tématu věnovaném [rizikovým událostem Azure AD](concept-risk-events.md). 

K sestavám zabezpečení můžete přistupovat z [Azure Portal](https://portal.azure.com) tak, že vyberete okno **Azure Active Directory** a pak přejdete do části **zabezpečení** . 

K dispozici jsou dvě různé sestavy zabezpečení, které se počítají na základě rizikových událostí:

- **Riziková přihlášení** –Rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu.

- **Uživatelé označení příznakem rizika** – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený. 

![Riziková přihlášení](./media/concept-risky-sign-ins/10.png)

Informace o tom, jak nakonfigurovat zásady, které aktivují tyto rizikové události, najdete v tématu [Postup konfigurace zásad rizik uživatelů](../identity-protection/howto-user-risk-policy.md).  

## <a name="who-can-access-the-risky-sign-ins-report"></a>Kdo má přístup k sestavě rizikových přihlášení?

Sestavy rizikových přihlášení jsou k dispozici uživatelům v následujících rolích:

- Správce zabezpečení
- Globální správce
- Čtenář zabezpečení

Další informace o přiřazování rolí pro správu uživateli v Azure Active Directory najdete v tématu [zobrazení a přiřazení rolí správce v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-manage-roles-portal).

## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>Jaká licence Azure AD je potřeba pro přístup k sestavě zabezpečení?  

Všechny edice služby Azure AD poskytují zprávy o rizikových přihlášeních. Úroveň podrobností sestav se však mezi jednotlivými edicemi liší: 

- V **edicích Azure Active Directory Free a Basic**získáte seznam rizikových přihlášení. 

- Kromě toho edice **Azure Active Directory Premium 1** umožňuje prozkoumávat některé ze základních rizikových událostí, které byly zjištěny pro každou sestavu. 

- Edice **Azure Active Directory Premium 2** poskytuje nejpodrobnější informace o všech základních rizikových událostech a umožňuje také konfigurovat zásady zabezpečení, které automaticky reagují na nakonfigurované úrovně rizika.

## <a name="risky-sign-ins-report-for-azure-ad-free-and-basic-edition"></a>Sestava rizikových přihlášení pro edici Azure AD Free a Basic

Edice Free a Basic služby Azure AD poskytují seznam rizikových přihlášení zjištěných pro vaše uživatele. Každý záznam obsahuje následující atributy:

- **Uživatel** – jméno uživatele, které bylo použito během operace přihlášení.
- **IP** – IP adresa zařízení, které se použilo k připojení k Azure Active Directory.
- **Umístění** – umístění používané pro připojení k Azure Active Directory. Jedná se o nejlepší odhad úsilí na základě trasování, dat registru, zpětného vyhledávání a dalších informací.
- **Čas přihlášení** – Čas, kdy k přihlášení došlo
- **Stav** – Stav přihlášení

![Riziková přihlášení](./media/concept-risky-sign-ins/01.png)

Na základě vašeho šetření rizikového přihlášení můžete službě Azure AD poskytnout zpětnou vazbu provedením následujících akcí:

- Vyřešit
- Označit jako falešně pozitivní
- Ignorovat
- Znovu aktivovat

![Riziková přihlášení](./media/concept-risky-sign-ins/21.png)

Tato sestava také nabízí možnost:

- Hledat prostředky
- Stáhnout data sestavy

![Riziková přihlášení](./media/concept-risky-sign-ins/93.png)

## <a name="risky-sign-ins-report-for-azure-ad-premium-editions"></a>Sestava rizikových přihlášení pro edice Azure AD Premium

Sestava rizikových přihlášení v edicích Azure AD Premium vám nabízí:

- Agregované informace o [typech rizikových událostí](concept-risk-events.md) , které byly zjištěny. U **edice Azure AD Premium P1**se detekce, která není pokrytá vaší licencí, zobrazuje jako přihlašování rizikových událostí **s zjištěným dodatečným rizikem**. V případě **Azure AD Premiumho P2 Edition**získáte nejpodrobnější informace o všech základních detekcích.

- Možnost stažení sestavy

![Riziková přihlášení](./media/concept-risky-sign-ins/456.png)

Při výběru rizikové události získáte zobrazení podrobné sestavy pro tuto rizikovou událost, které umožňuje následující akce:

- Výběr možnosti konfigurace [zásad odstraňování rizik uživatelů](../identity-protection/howto-user-risk-policy.md)  

- Kontrola časové osy zjištění pro rizikové události  

- Kontrola seznamu uživatelů, pro které byla riziková událost zjištěná

- Ručně uzavřete rizikové události. 

![Riziková přihlášení](./media/concept-risky-sign-ins/457.png)

> [!IMPORTANT]
> V některých případech můžete v [sestavě přihlášení](concept-sign-ins.md)najít rizikové události bez odpovídající položky přihlášení. Je to proto, že Identity Protection vyhodnocuje riziko pro **interaktivní** i neinteraktivní přihlášení, zatímco sestava přihlášení zobrazuje jenom interaktivní přihlášení.

Po výběru uživatele získáte podrobné zobrazení sestavy pro tohoto uživatele, které vám umožňuje:

- Otevřít zobrazení Všechna přihlášení

- Resetovat heslo uživatele

- Zavřít všechny události

- Vyšetřit rizikové události oznámené pro uživatele 

![Riziková přihlášení](./media/concept-risky-sign-ins/324.png)

Pokud chcete vyšetřovat rizikovou událost, vyberte ji ze seznamu.  
Tím otevřete okno **Podrobnosti** pro tuto rizikovou událost. V okně **Podrobnosti** můžete buď ručně zavřít rizikovou událost, nebo znovu aktivovat ručně zavřenou rizikovou událost. 

![Riziková přihlášení](./media/concept-risky-sign-ins/325.png)

## <a name="next-steps"></a>Další postup

- [Jak nakonfigurovat zásady rizik uživatelů](../identity-protection/howto-user-risk-policy.md)
- [Jak nakonfigurovat zásady pro nápravu rizik](../identity-protection/howto-user-risk-policy.md)
- [Typy rizikových událostí](concept-risk-events.md)
