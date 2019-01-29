---
title: Sestava zabezpečení Uživatelé označení příznakem rizika na portálu Azure Active Directory | Dokumentace Microsoftu
description: Přečtěte si o sestavě zabezpečení Uživatelé označení příznakem rizika na portálu Azure Active Directory
services: active-directory
author: priyamohanram
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/17/2019
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 8f9a4b95feadad873bb87cb9071dd98f597d839a
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55193994"
---
# <a name="users-flagged-for-risk-report-in-the-azure-portal"></a>Uživatelé označení příznakem rizika sestavy na webu Azure Portal

Azure Active Directory (Azure AD) detekuje podezřelé akce související s vašimi uživatelskými účty. Pro každou zjištěnou akci, vytvoří záznam nazvaný [riziková událost](concept-risk-events.md) se vytvoří.

Přístupné sestavy zabezpečení z [webu Azure portal](https://portal.azure.com) tak, že vyberete **Azure Active Directory** okna a pak přejdete do **zabezpečení** oddílu. 

Zjištěné rizikové události se použijí k výpočtu těchto údajů:

- **Riziková přihlášení** –Rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu. 

- **Uživatelé označení příznakem rizika** – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený. 

Zjistěte, jak nakonfigurovat zásady, které aktivují tyto rizikové události, najdete v článku [jak nakonfigurovat zásady rizik uživatelů](../identity-protection/howto-user-risk-policy.md). 

![Riziková přihlášení](./media/concept-user-at-risk/10.png)


## <a name="what-azure-ad-license-do-you-need-to-access-the-users-at-risk-report"></a>Jaká licence Azure AD je potřeba pro přístup k sestava ohrožených uživatelů?  

Sestavy uživatelů označených příznakem rizika nabízí všechny edice Azure Active Directory. Úroveň podrobností sestav se však mezi jednotlivými edicemi liší: 

- V **edice Azure Active Directory Free a Basic**, získat seznam uživatelů označených příznakem rizika. 

- Kromě toho **Azure Active Directory Premium 1** edition umožňuje prozkoumávat některé ze základních rizikových událostech, které byly zjištěny pro každou sestavu. 

- Edice **Azure Active Directory Premium 2** poskytuje nejpodrobnější informace o všech základních rizikových událostech a umožňuje také konfigurovat zásady zabezpečení, které automaticky reagují na nakonfigurované úrovně rizika.


## <a name="users-at-risk-report-for-azure-ad-free-and-basic-editions"></a>Sestava ohrožených pro edice free a basic služby Azure AD uživatelů

Uživatelé označení příznakem rizika sestavy ve službě Azure AD free a basic edice poskytuje seznam uživatelských účtů, které možná ohrožené. 

![Riziková přihlášení](./media/concept-user-at-risk/03.png)

Kliknutím na uživatele poskytuje přihlašovací údaje. U ohrožených uživatelů můžete zkontrolovat historii jejich přihlášení a v případě potřeby resetovat heslo.

V tomto dialogovém okně máte možnost:

- Stáhnout sestavu
- Vyhledávat uživatele

    ![Riziková přihlášení](./media/concept-user-at-risk/16.png)

Podrobnější informace potřebujete licenci úrovně premium.

## <a name="users-at-risk-report-for-azure-ad-premium-editions"></a>Sestava ohrožených pro edice premium služby Azure AD uživatelů

Uživatelé označení příznakem rizika sestavy v edicích premium služby Azure AD vám nabízí:

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
- [Jak nakonfigurovat zásady odstraňování rizik](../identity-protection/howto-user-risk-policy.md)
- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)

