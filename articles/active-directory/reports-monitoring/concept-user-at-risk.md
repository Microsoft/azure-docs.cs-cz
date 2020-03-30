---
title: Sestava zabezpečení Uživatelé označení příznakem rizika na portálu Azure Active Directory | Dokumentace Microsoftu
description: Přečtěte si o sestavě zabezpečení Uživatelé označení příznakem rizika na portálu Azure Active Directory
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/17/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 894d8dfb7f870ec4a2a11f1d75ee0376b25d8c7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74014456"
---
# <a name="users-flagged-for-risk-report-in-the-azure-portal"></a>Uživatelé označeni pro sestavu rizik na webu Azure Portal

Azure Active Directory (Azure AD) detekuje podezřelé akce související s vašimi uživatelskými účty. Pro každou zjištěnou akci je vytvořen záznam nazývaný [zjišťování rizik.](concept-risk-events.md)

K sestavám zabezpečení z [portálu Azure](https://portal.azure.com) se dostanete tak, že vyberete okno **Azure Active Directory** a pak přejdete do části **Zabezpečení.** 

Detekované detekce rizik se používají k výpočtu:

- **Riziková přihlášení** – Rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu. 

- **Uživatelé označení příznakem rizika** – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený. 

Informace o konfiguraci zásad, které tato zjišťování rizik aktivují, naleznete [v tématu Konfigurace zásad rizik pro uživatele](../identity-protection/howto-user-risk-policy.md). 

![Riziková přihlášení](./media/concept-user-at-risk/10.png)


## <a name="what-azure-ad-license-do-you-need-to-access-the-users-at-risk-report"></a>Jakou licenci Azure AD potřebujete pro přístup k ohlášení ohrožených uživatelů?  

Sestavy uživatelů označených příznakem rizika nabízí všechny edice Azure Active Directory. Úroveň podrobností sestav se však mezi jednotlivými edicemi liší: 

- V **edicích Azure Active Directory Free a Basic**získáte seznam uživatelů označených příznakem riziko. 

- Kromě toho **azure Active Directory Premium 1** vydání umožňuje prozkoumat některé základní zjišťování rizik, které byly zjištěny pro každou sestavu. 

- **Edice Azure Active Directory Premium 2** poskytuje nejpodrobnější informace o všech základních zjišťovánírizik a také umožňuje konfigurovat zásady zabezpečení, které automaticky reagují na nakonfigurované úrovně rizika.


## <a name="users-at-risk-report-for-azure-ad-free-and-basic-editions"></a>Sestava ohrožených uživatelů pro azure a dlaze zdarma a základní edice

Uživatelé označeny pro zprávu o rizicích ve službě Azure AD zdarma a základní edice poskytuje seznam uživatelských účtů, které mohou být ohroženy. 

![Riziková přihlášení](./media/concept-user-at-risk/03.png)

Výběr uživatele poskytuje přihlašovací informace. U ohrožených uživatelů můžete zkontrolovat historii jejich přihlášení a v případě potřeby resetovat heslo.

V tomto dialogovém okně máte možnost:

- Stáhnout sestavu
- Vyhledávat uživatele

    ![Riziková přihlášení](./media/concept-user-at-risk/16.png)

Pro podrobnější informace potřebujete prémiovou licenci.

## <a name="users-at-risk-report-for-azure-ad-premium-editions"></a>Sestava ohrožených uživatelů pro edice Azure AD premium

Uživatelé označeni pro sestavu rizik v edicích Azure AD premium vám poskytuje:

- Seznam uživatelských účtů, které by mohly být ohrožené 

- Souhrnné informace o [typech detekce rizik,](concept-risk-events.md) které byly zjištěny

- Možnost stažení sestavy

- Výběr možnosti konfigurace [zásad odstraňování rizik uživatelů](../identity-protection/howto-user-risk-policy.md)  

![Riziková přihlášení](./media/concept-user-at-risk/71.png)

Po výběru uživatele získáte podrobné zobrazení sestavy pro tohoto uživatele, které vám umožňuje:

- Otevřít zobrazení Všechna přihlášení

- Resetovat heslo uživatele

- Zavřít všechny události

- Prozkoumejte hlášené detekce rizik pro uživatele. 

![Riziková přihlášení](./media/concept-user-at-risk/324.png)

Chcete-li prozkoumat detekci rizik, vyberte jednu ze seznamu a otevřete okno **Podrobnosti** pro tuto detekci rizik. V okně **Podrobnosti** máte možnost buď ručně uzavřít detekci rizik, nebo znovu aktivovat ručně uzavřenou detekci rizik. 

![Riziková přihlášení](./media/concept-user-at-risk/325.png)


## <a name="next-steps"></a>Další kroky

- [Jak nakonfigurovat zásady rizika pro uživatele](../identity-protection/howto-user-risk-policy.md)
- [Jak nakonfigurovat zásadu nápravy rizik](../identity-protection/howto-user-risk-policy.md)
- [Ochrana identit služby Azure Active Directory](../active-directory-identityprotection.md)

