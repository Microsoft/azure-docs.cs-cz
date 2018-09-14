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
ms.date: 11/14/2017
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 3fab0648a2ad0e0a4c193d37875913dd27bf9af4
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45574266"
---
# <a name="risky-sign-ins-report-in-the-azure-active-directory-portal"></a>Sestavy rizikových přihlášení na portálu Azure Active Directory

Sestavy zabezpečení v Azure Active Directory (Azure AD) umožňují získat přehled o pravděpodobnosti ohrožení uživatelských účtů ve vašem prostředí. 

Azure AD detekuje podezřelé akce, které souvisejí s vašimi uživatelskými účty. Pro každou zjištěnou akci se vytvoří záznam nazvaný *riziková událost*. Další podrobnosti najdete v tématu věnovaném [rizikovým událostem služby Azure Active Directory](concept-risk-events.md). 

Zjištěné rizikové události se použijí k výpočtu těchto údajů:

- **Riziková přihlášení** –Rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu. Další podrobnosti najdete v tématu [jak nakonfigurovat zásady rizik přihlašování](../identity-protection/howto-sign-in-risk-policy.md). 

- **Uživatelé označení příznakem rizika** – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený. Další podrobnosti najdete v tématu [jak nakonfigurovat zásady rizik uživatelů](../identity-protection/howto-user-risk-policy.md).  

Na webu [Azure Portal](https://portal.azure.com) najdete sestavy zabezpečení v okně **Azure Active Directory** v části **Zabezpečení**. 

![Riziková přihlášení](./media/concept-risky-sign-ins/10.png)


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


![Riziková přihlášení](./media/concept-risky-sign-ins/01.png)

Na základě prošetření rizikového přihlášení můžete službě Azure Active Directory poskytnout zpětnou vazbu ve formě následujících akcí:

- Vyřešit
- Označit jako falešně pozitivní
- Ignorovat
- Znovu aktivovat

![Riziková přihlášení](./media/concept-risky-sign-ins/21.png)



V tomto dialogovém okně máte možnost:

- Prohledávat prostředky
- Stáhnout data sestavy


![Riziková přihlášení](./media/concept-risky-sign-ins/93.png)


## <a name="azure-active-directory-premium-editions"></a>Edice Premium služby Azure Active Directory

Sestavy rizikových přihlášení v edicích Premium služby Azure Active Directory vám nabízí:

- Agregované informace o [typech rizikových událostí](concept-risk-events.md), které byly zjištěné

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
Tím otevřete okno **Podrobnosti** pro tuto rizikovou událost. Na **podrobnosti** okně máte možnost buď ruční zavření rizikové události a znovu aktivovat ručně zavřené rizikové události. 


![Riziková přihlášení](./media/concept-risky-sign-ins/325.png)





## <a name="next-steps"></a>Další postup

- Další informace o Azure Active Directory Identity Protection najdete v tématu [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

