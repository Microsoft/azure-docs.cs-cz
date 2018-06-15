---
title: Sestavy rizikových přihlášení na portálu Azure Active Directory | Dokumentace Microsoftu
description: Informace o sestavách rizikových přihlášení na portálu Azure Active Directory
services: active-directory
author: rolyon
manager: mtillman
ms.assetid: 7728fcd7-3dd5-4b99-a0e4-949c69788c0f
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 11/14/2017
ms.author: rolyon
ms.reviewer: dhanyahk
ms.openlocfilehash: 16b0f9fd240f393c23bc33ac0c9f5d024c3cb42e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34589419"
---
# <a name="risky-sign-ins-report-in-the-azure-active-directory-portal"></a>Sestavy rizikových přihlášení na portálu Azure Active Directory

Sestavy zabezpečení v Azure Active Directory (Azure AD) umožňují získat přehled o pravděpodobnosti ohrožení uživatelských účtů ve vašem prostředí. 

Azure AD detekuje podezřelé akce, které souvisejí s vašimi uživatelskými účty. Pro každou zjištěnou akci se vytvoří záznam nazvaný *riziková událost*. Další podrobnosti najdete v tématu věnovaném [rizikovým událostem služby Azure Active Directory](active-directory-identity-protection-risk-events.md). 

Zjištěné rizikové události se použijí k výpočtu těchto údajů:

- **Riziková přihlášení** – Rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu. Další podrobnosti najdete v tématu [Riziková přihlášení](active-directory-identityprotection.md#risky-sign-ins). 

- **Uživatelé označení příznakem rizika** – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený. Další podrobnosti najdete v tématu [Uživatelé označení příznakem rizika](active-directory-identityprotection.md#users-flagged-for-risk).  

Na webu [Azure Portal](https://portal.azure.com) najdete sestavy zabezpečení v okně **Azure Active Directory** v části **Zabezpečení**. 

![Riziková přihlášení](./media/active-directory-reporting-security-risky-sign-ins/10.png)


## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>Jaká licence Azure AD je potřeba pro přístup k sestavě zabezpečení?  

Sestavy rizikových přihlášení nabízí všechny edice Azure Active Directory.  
Úroveň podrobností sestav se však mezi jednotlivými edicemi liší: 

- **Edice Azure Active Directory Free a Basic** již nabízí seznam rizikových přihlášení. 

- Edice **Azure Active Directory Premium 1** tento model rozšiřuje tím, že umožňuje také prozkoumávat některé ze základních rizikových událostí, které byly v každé sestavě rozpoznány. 

- Edice **Azure Active Directory Premium 2** poskytuje nejpodrobnější informace o všech základních rizikových událostech a umožňuje také konfigurovat zásady zabezpečení, které automaticky reagují na nakonfigurované úrovně rizika.



## <a name="azure-active-directory-free-and-basic-edition"></a>Edice Free a Basic služby Azure Active Directory

Edice Free a Basic služby Azure Active Directory poskytují seznam rizikových přihlášení, která byla zjištěná pro vaše uživatele. Tato sestava uvádí:

- **Uživatel** – Jméno uživatele použité během přihlašovací operace
- **IP** – IP adresa zařízení použitá pro připojení k Azure Active Directory
- **Umístění** – Umístění použité pro připojení k Azure Active Directory
- **Čas přihlášení** – Čas, kdy k přihlášení došlo
- **Stav** – Stav přihlášení


![Riziková přihlášení](./media/active-directory-reporting-security-risky-sign-ins/01.png)

Na základě prošetření rizikového přihlášení můžete službě Azure Active Directory poskytnout zpětnou vazbu ve formě následujících akcí:

- Vyřešit
- Označit jako falešně pozitivní
- Ignorovat
- Znovu aktivovat

![Riziková přihlášení](./media/active-directory-reporting-security-risky-sign-ins/21.png)

Další informace najdete v tématu věnovaném [ručnímu zavření rizikových událostí](active-directory-identityprotection.md#closing-risk-events-manually).

V tomto dialogovém okně máte možnost:

- Prohledávat prostředky
- Stáhnout data sestavy


![Riziková přihlášení](./media/active-directory-reporting-security-risky-sign-ins/93.png)


## <a name="azure-active-directory-premium-editions"></a>Edice Premium služby Azure Active Directory

Sestavy rizikových přihlášení v edicích Premium služby Azure Active Directory vám nabízí:

- Agregované informace o [typech rizikových událostí](active-directory-identity-protection-risk-events.md), které byly zjištěné

- Možnost stažení sestavy


![Riziková přihlášení](./media/active-directory-reporting-security-risky-sign-ins/456.png)


Při výběru rizikové události získáte zobrazení podrobné sestavy pro tuto rizikovou událost, které umožňuje následující akce:

- Výběr možnosti konfigurace [zásad odstraňování rizik uživatelů](active-directory-identityprotection.md#user-risk-security-policy)  

- Kontrola časové osy zjištění pro rizikové události  

- Kontrola seznamu uživatelů, pro které byla riziková událost zjištěná

- [Ruční zavření rizikových událostí](active-directory-identityprotection.md#closing-risk-events-manually) nebo reaktivace ručně zavřené rizikové události 


![Riziková přihlášení](./media/active-directory-reporting-security-risky-sign-ins/457.png)

Po výběru uživatele získáte podrobné zobrazení sestavy pro tohoto uživatele, které vám umožňuje:

- Otevřít zobrazení Všechna přihlášení

- Resetovat heslo uživatele

- Zavřít všechny události

- Vyšetřit rizikové události oznámené pro uživatele 


![Riziková přihlášení](./media/active-directory-reporting-security-risky-sign-ins/324.png)


Pokud chcete vyšetřovat rizikovou událost, vyberte ji ze seznamu.  
Tím otevřete okno **Podrobnosti** pro tuto rizikovou událost. V okně **Podrobnosti** můžete zvolit [ruční zavření rizikové události](active-directory-identityprotection.md#closing-risk-events-manually) nebo reaktivaci ručně zavřené rizikové události. 


![Riziková přihlášení](./media/active-directory-reporting-security-risky-sign-ins/325.png)





## <a name="next-steps"></a>Další kroky

- Další informace o Azure Active Directory Identity Protection najdete v tématu [Azure Active Directory Identity Protection](active-directory-identityprotection.md).

