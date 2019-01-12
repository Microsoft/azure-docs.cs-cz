---
title: Sestavy rizikových přihlášení na portálu Azure Active Directory | Dokumentace Microsoftu
description: Informace o sestavách rizikových přihlášení na portálu Azure Active Directory
services: active-directory
author: priyamohanram
manager: mtillman
ms.assetid: 7728fcd7-3dd5-4b99-a0e4-949c69788c0f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 8e92288883a7779130e3b7f7a8433b61f76aa18c
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2019
ms.locfileid: "54244711"
---
# <a name="risky-sign-ins-report-in-the-azure-active-directory-portal"></a>Sestavy rizikových přihlášení na portálu Azure Active Directory

Azure Active Directory (Azure AD) detekuje podezřelé akce, které souvisejí s vašimi uživatelskými účty. Pro každou zjištěnou akci, vytvoří záznam nazvaný **riziková událost** se vytvoří. Další podrobnosti najdete v tématu [rizikových událostí služby Azure AD](concept-risk-events.md). 

Přístupné sestavy zabezpečení z [webu Azure portal](https://portal.azure.com) tak, že vyberete **Azure Active Directory** okna a pak přejdete do **zabezpečení** oddílu. 

Existují dvě sestavy zabezpečení, které se počítají podle rizikových událostí:

- **Riziková přihlášení** –Rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu.

- **Uživatelé označení příznakem rizika** – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený. 

![Riziková přihlášení](./media/concept-risky-sign-ins/10.png)

Zjistěte, jak nakonfigurovat zásady, které aktivují tyto rizikové události, najdete v článku [jak nakonfigurovat zásady rizik uživatelů](../identity-protection/howto-user-risk-policy.md).  

## <a name="who-can-access-the-risky-sign-ins-report"></a>Kdo má přístup k sestavě rizikových přihlášení?

Sestavy rizikových přihlášení jsou k dispozici uživatelům v následujících rolí:

- Správce zabezpečení
- Globální správce
- Čtecí zařízení pro zabezpečení

Zjistěte, jak přiřadit správní role pro uživatele v Azure Active Directory, najdete v článku [zobrazení a přiřazení rolí správce ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-manage-roles-portal).

## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>Jaká licence Azure AD je potřeba pro přístup k sestavě zabezpečení?  

Všechny edice Azure AD poskytují sestavy rizikových přihlášení. Úroveň podrobností sestav se však mezi jednotlivými edicemi liší: 

- V **edice Azure Active Directory Free a Basic**, získejte seznam rizikových přihlášení. 

- Kromě toho **Azure Active Directory Premium 1** edition umožňuje prozkoumávat některé ze základních rizikových událostech, které byly zjištěny pro každou sestavu. 

- Edice **Azure Active Directory Premium 2** poskytuje nejpodrobnější informace o všech základních rizikových událostech a umožňuje také konfigurovat zásady zabezpečení, které automaticky reagují na nakonfigurované úrovně rizika.

## <a name="risky-sign-ins-report-for-azure-ad-free-and-basic-edition"></a>Sestavy rizikových přihlášení pro službu Azure AD free a basic edition

Edice free a basic služby Azure AD poskytují seznam rizikových přihlášení, které byly zjištěny pro vaše uživatele. Každý záznam obsahuje následující atributy:

- **Uživatel** – Jméno uživatele použité během přihlašovací operace
- **IP** – IP adresa zařízení použitá pro připojení k Azure Active Directory
- **Umístění** – Umístění použité pro připojení k Azure Active Directory
- **Čas přihlášení** – Čas, kdy k přihlášení došlo
- **Stav** – Stav přihlášení

![Riziková přihlášení](./media/concept-risky-sign-ins/01.png)

Založené na šetření rizikových přihlášení, můžete poskytnout zpětnou vazbu ke službě Azure AD pomocí následujících kroků:

- Vyřešit
- Označit jako falešně pozitivní
- Ignorovat
- Znovu aktivovat

![Riziková přihlášení](./media/concept-risky-sign-ins/21.png)

Tato sestava také nabízí možnost:

- Prohledávat prostředky
- Stáhnout data sestavy

![Riziková přihlášení](./media/concept-risky-sign-ins/93.png)

## <a name="risky-sign-ins-report-for-azure-ad-premium-editions"></a>Sestavy rizikových přihlášení pro edice premium služby Azure AD

Sestavy rizikových přihlášení v edicích premium služby Azure AD vám nabízí:

- Agregované informace o [typech rizikových událostí](concept-risk-events.md) , který byl zjištěn. S **edice Azure AD Premium P1**, detekce, které nejsou pokryty všemi licence se zobrazí jako rizikovou událost **přihlášení s dalšími riziky zjistil**. S **edice Azure AD Premium P2**, získáte nejpodrobnější informace o základní všechna nalezení.

- Možnost stažení sestavy

![Riziková přihlášení](./media/concept-risky-sign-ins/456.png)

Při výběru rizikové události získáte zobrazení podrobné sestavy pro tuto rizikovou událost, které umožňuje následující akce:

- Výběr možnosti konfigurace [zásad odstraňování rizik uživatelů](../identity-protection/howto-user-risk-policy.md)  

- Kontrola časové osy zjištění pro rizikové události  

- Kontrola seznamu uživatelů, pro které byla riziková událost zjištěná

- Ruční zavření rizikové události. 

![Riziková přihlášení](./media/concept-risky-sign-ins/457.png)

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
- [Jak nakonfigurovat zásady odstraňování rizik](../identity-protection/howto-user-risk-policy.md)
- [Typy rizikových událostí](concept-risk-events.md)
