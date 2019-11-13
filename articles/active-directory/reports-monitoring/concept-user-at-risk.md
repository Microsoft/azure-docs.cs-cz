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
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014456"
---
# <a name="users-flagged-for-risk-report-in-the-azure-portal"></a>Sestava uživatelů označených příznakem rizika v Azure Portal

Azure Active Directory (Azure AD) detekuje podezřelé akce týkající se vašich uživatelských účtů. Pro každou zjištěnou akci se vytvoří záznam s názvem [zjišťování rizik](concept-risk-events.md) .

K sestavám zabezpečení můžete přistupovat z [Azure Portal](https://portal.azure.com) tak, že vyberete okno **Azure Active Directory** a pak přejdete do části **zabezpečení** . 

Pro výpočet se používají zjištěná zjištění rizik:

- **Riziková přihlášení** –Rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu. 

- **Uživatelé označení příznakem rizika** – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený. 

Informace o tom, jak nakonfigurovat zásady, které aktivují tyto detekce rizik, najdete v tématu [Postup konfigurace zásad rizik uživatelů](../identity-protection/howto-user-risk-policy.md). 

![Riziková přihlášení](./media/concept-user-at-risk/10.png)


## <a name="what-azure-ad-license-do-you-need-to-access-the-users-at-risk-report"></a>Jaká licence Azure AD potřebujete pro přístup k sestavě rizik uživatelů?  

Sestavy uživatelů označených příznakem rizika nabízí všechny edice Azure Active Directory. Úroveň podrobností sestav se však mezi jednotlivými edicemi liší: 

- V **edicích Azure Active Directory Free a Basic**se zobrazí seznam uživatelů označených příznakem rizika. 

- Kromě toho edice **Azure Active Directory Premium 1** umožňuje prozkoumávat některé z základních detekcí rizik zjištěných pro jednotlivé sestavy. 

- Edice **Azure Active Directory Premium 2** poskytuje nejpodrobnější informace o všech základních detekcích rizik a umožňuje také konfigurovat zásady zabezpečení, které automaticky reagují na nakonfigurované úrovně rizika.


## <a name="users-at-risk-report-for-azure-ad-free-and-basic-editions"></a>Sestava rizik uživatelů pro edice Free a Basic pro Azure AD

Zpráva uživatelé označení příznakem rizika v edicích Free a Basic služby Azure AD nabízí seznam uživatelských účtů, které mohou být ohroženy. 

![Riziková přihlášení](./media/concept-user-at-risk/03.png)

Když se vybere uživatel, zobrazí se přihlašovací údaje. U ohrožených uživatelů můžete zkontrolovat historii jejich přihlášení a v případě potřeby resetovat heslo.

V tomto dialogovém okně máte možnost:

- Stáhnout sestavu
- Vyhledávat uživatele

    ![Riziková přihlášení](./media/concept-user-at-risk/16.png)

Pro podrobnější informace potřebujete prémiovou licenci.

## <a name="users-at-risk-report-for-azure-ad-premium-editions"></a>Sestava rizik uživatelů pro edice Azure AD Premium

Sestava uživatelé označení příznakem rizika v edicích Azure AD Premium vám poskytne:

- Seznam uživatelských účtů, které by mohly být ohrožené 

- Agregované informace o zjištěných [typech detekce rizik](concept-risk-events.md)

- Možnost stažení sestavy

- Možnost konfigurace [zásad odstraňování rizik uživatelů](../identity-protection/howto-user-risk-policy.md)  

![Riziková přihlášení](./media/concept-user-at-risk/71.png)

Po výběru uživatele získáte podrobné zobrazení sestavy pro tohoto uživatele, které vám umožňuje:

- Otevřít zobrazení Všechna přihlášení

- Resetovat heslo uživatele

- Zavřít všechny události

- Prozkoumat zjištěná zjištění rizik pro uživatele. 

![Riziková přihlášení](./media/concept-user-at-risk/324.png)

Pokud chcete zjistit detekci rizik, vyberte ji ze seznamu a otevřete okno **Podrobnosti** pro toto zjišťování rizik. V okně **podrobností** máte možnost buď ručně uzavřít detekci rizik, nebo znovu aktivovat ručně uzavřenou detekci rizik. 

![Riziková přihlášení](./media/concept-user-at-risk/325.png)


## <a name="next-steps"></a>Další kroky

- [Jak nakonfigurovat zásady rizik uživatelů](../identity-protection/howto-user-risk-policy.md)
- [Jak nakonfigurovat zásady pro nápravu rizik](../identity-protection/howto-user-risk-policy.md)
- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)

