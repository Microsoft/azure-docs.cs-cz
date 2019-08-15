---
title: Sestava zabezpečení Uživatelé označení příznakem rizika na portálu Azure Active Directory | Dokumentace Microsoftu
description: Přečtěte si o sestavě zabezpečení Uživatelé označení příznakem rizika na portálu Azure Active Directory
services: active-directory
author: cawrites
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/17/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 950d0dc2f4a1a88e0c317b3398c7492cc6c52e2a
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989844"
---
# <a name="users-flagged-for-risk-report-in-the-azure-portal"></a>Sestava uživatelů označených příznakem rizika v Azure Portal

Azure Active Directory (Azure AD) detekuje podezřelé akce týkající se vašich uživatelských účtů. Pro každou zjištěnou akci se vytvoří záznam s názvem [riziková událost](concept-risk-events.md) .

K sestavám zabezpečení můžete přistupovat z [Azure Portal](https://portal.azure.com) tak, že vyberete okno **Azure Active Directory** a pak přejdete do části **zabezpečení** . 

Zjištěné rizikové události se použijí k výpočtu těchto údajů:

- **Riziková přihlášení** –Rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu. 

- **Uživatelé označení příznakem rizika** – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený. 

Informace o tom, jak nakonfigurovat zásady, které aktivují tyto rizikové události, najdete v tématu [Postup konfigurace zásad rizik uživatelů](../identity-protection/howto-user-risk-policy.md). 

![Riziková přihlášení](./media/concept-user-at-risk/10.png)


## <a name="what-azure-ad-license-do-you-need-to-access-the-users-at-risk-report"></a>Jaká licence Azure AD potřebujete pro přístup k sestavě rizik uživatelů?  

Sestavy uživatelů označených příznakem rizika nabízí všechny edice Azure Active Directory. Úroveň podrobností sestav se však mezi jednotlivými edicemi liší: 

- V **edicích Azure Active Directory Free a Basic**se zobrazí seznam uživatelů označených příznakem rizika. 

- Kromě toho edice **Azure Active Directory Premium 1** umožňuje prozkoumávat některé ze základních rizikových událostí, které byly zjištěny pro každou sestavu. 

- Edice **Azure Active Directory Premium 2** poskytuje nejpodrobnější informace o všech základních rizikových událostech a umožňuje také konfigurovat zásady zabezpečení, které automaticky reagují na nakonfigurované úrovně rizika.


## <a name="users-at-risk-report-for-azure-ad-free-and-basic-editions"></a>Sestava rizik uživatelů pro edice Free a Basic pro Azure AD

Zpráva uživatelé označení příznakem rizika v edicích Free a Basic služby Azure AD nabízí seznam uživatelských účtů, které mohou být ohroženy. 

![Riziková přihlášení](./media/concept-user-at-risk/03.png)

Když se vybere uživatel, zobrazí se přihlašovací údaje. U ohrožených uživatelů můžete zkontrolovat historii jejich přihlášení a v případě potřeby resetovat heslo.

V tomto dialogovém okně máte možnost:

- Stáhnout sestavu
- Hledat uživatele

    ![Riziková přihlášení](./media/concept-user-at-risk/16.png)

Pro podrobnější informace potřebujete prémiovou licenci.

## <a name="users-at-risk-report-for-azure-ad-premium-editions"></a>Sestava rizik uživatelů pro edice Azure AD Premium

Sestava uživatelé označení příznakem rizika v edicích Azure AD Premium vám poskytne:

- Seznam uživatelských účtů, které by mohly být ohrožené 

- Agregované informace o [typech rizikových událostí](concept-risk-events.md), které byly zjištěné

- Možnost stažení sestavy

- Možnost konfigurace [zásad odstraňování rizik uživatelů](../identity-protection/howto-user-risk-policy.md)  

![Riziková přihlášení](./media/concept-user-at-risk/71.png)

Po výběru uživatele získáte podrobné zobrazení sestavy pro tohoto uživatele, které vám umožňuje:

- Otevřít zobrazení Všechna přihlášení

- Resetovat heslo uživatele

- Zavřít všechny události

- Vyšetřit rizikové události oznámené pro uživatele 

![Riziková přihlášení](./media/concept-user-at-risk/324.png)

Pokud chcete vyšetřit rizikovou událost, vyberte některou ze seznamu a otevře se okno **Podrobnosti** pro danou rizikovou událost. V okně **Podrobnosti** můžete buď ručně zavřít rizikovou událost, nebo znovu aktivovat ručně zavřenou rizikovou událost. 

![Riziková přihlášení](./media/concept-user-at-risk/325.png)


## <a name="next-steps"></a>Další postup

- [Jak nakonfigurovat zásady rizik uživatelů](../identity-protection/howto-user-risk-policy.md)
- [Jak nakonfigurovat zásady pro nápravu rizik](../identity-protection/howto-user-risk-policy.md)
- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)

