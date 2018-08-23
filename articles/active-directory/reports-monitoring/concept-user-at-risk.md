---
title: Sestava zabezpečení Uživatelé označení příznakem rizika na portálu Azure Active Directory | Dokumentace Microsoftu
description: Přečtěte si o sestavě zabezpečení Uživatelé označení příznakem rizika na portálu Azure Active Directory
services: active-directory
author: priyamohanram
manager: mtillman
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/14/2017
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 030774716e1af4a7d6817d64ae66ded2bcaf4081
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2018
ms.locfileid: "41920670"
---
# <a name="users-flagged-for-risk-security-report-in-the-azure-active-directory-portal"></a>Sestava zabezpečení Uživatelé označení příznakem rizika na portálu Azure Active Directory

Sestavy zabezpečení v Azure Active Directory (Azure AD) umožňují získat přehled o pravděpodobnosti ohrožení uživatelských účtů ve vašem prostředí. 

Azure Active Directory detekuje podezřelé akce, které souvisejí s vašimi uživatelskými účty. Pro každou zjištěnou akci se vytvoří záznam nazvaný *riziková událost*. Další informace najdete v tématu věnovaném [rizikovým událostem služby Azure Active Directory](concept-risk-events.md). 

Zjištěné rizikové události se použijí k výpočtu těchto údajů:

- **Riziková přihlášení** –Rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu. Další informace najdete v tématu popisujícím [riziková přihlášení](../identity-protection/overview.md#risky-sign-ins). 

- **Uživatelé označení příznakem rizika** – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený. Další informace najdete v tématu popisujícím [uživatele označené příznakem rizika](../identity-protection/overview.md#users-flagged-for-risk).  

Na webu Azure Portal najdete sestavy zabezpečení v okně **Azure Active Directory** v části **Zabezpečení**.  

![Riziková přihlášení](./media/concept-user-at-risk/10.png)



## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>Jaká licence Azure AD je potřeba pro přístup k sestavě zabezpečení?  

Sestavy uživatelů označených příznakem rizika nabízí všechny edice Azure Active Directory.  
Úroveň podrobností sestav se však mezi jednotlivými edicemi liší: 

- **Edice Azure Active Directory Free a Basic** již nabízí seznam uživatelů označených příznakem rizika. 

- Edice **Azure Active Directory Premium 1** tento model rozšiřuje tím, že umožňuje také prozkoumávat některé ze základních rizikových událostí, které byly v každé sestavě rozpoznány. 

- Edice **Azure Active Directory Premium 2** poskytuje nejpodrobnější informace o všech základních rizikových událostech a umožňuje konfigurovat zásady zabezpečení, které automaticky reagují na nakonfigurované úrovně rizika.



## <a name="azure-active-directory-free-and-basic-edition"></a>Edice Free a Basic služby Azure Active Directory

Edice Free a Basic služby Azure Active Directory poskytuje sestavu uživatelů označených příznakem rizika, která obsahuje seznam uživatelských účtů, které můžou být ohrožené. 


![Riziková přihlášení](./media/concept-user-at-risk/03.png)

Kliknutím na uživatele otevřete okno se souvisejícími uživatelskými daty.
U ohrožených uživatelů můžete zkontrolovat historii jejich přihlášení a v případě potřeby resetovat heslo.

![Riziková přihlášení](./media/concept-user-at-risk/46.png)


V tomto dialogovém okně máte možnost:

- Stáhnout sestavu

- Vyhledávat uživatele

![Riziková přihlášení](./media/concept-user-at-risk/16.png)


## <a name="azure-active-directory-premium-editions"></a>Edice Premium služby Azure Active Directory

Sestava uživatelů označených příznakem rizika v edicích Premium služby Azure Active Directory vám nabízí:

- [Seznam uživatelských účtů](../identity-protection/overview.md#users-flagged-for-risk), které mohly být napadeny nebo vyzrazeny 

- Agregované informace o [typech rizikových událostí](concept-risk-events.md), které byly zjištěné

- Možnost stažení sestavy

- Možnost konfigurace [zásad odstraňování rizik uživatelů](../identity-protection/overview.md#user-risk-security-policy)  


![Riziková přihlášení](./media/concept-user-at-risk/71.png)

Po výběru uživatele získáte podrobné zobrazení sestavy pro tohoto uživatele, které vám umožňuje:

- Otevřít zobrazení Všechna přihlášení

- Resetovat heslo uživatele

- Zavřít všechny události

- Vyšetřit rizikové události oznámené pro uživatele 


![Riziková přihlášení](./media/concept-user-at-risk/324.png)


Pokud chcete vyšetřit rizikovou událost, vyberte některou ze seznamu a otevře se okno **Podrobnosti** pro danou rizikovou událost. V okně **Podrobnosti** můžete zvolit [ruční zavření rizikové události](../identity-protection/overview.md#closing-risk-events-manually) nebo reaktivaci ručně zavřené rizikové události. 


![Riziková přihlášení](./media/concept-user-at-risk/325.png)



## <a name="next-steps"></a>Další kroky

- Další informace o Azure Active Directory Identity Protection najdete v tématu [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

