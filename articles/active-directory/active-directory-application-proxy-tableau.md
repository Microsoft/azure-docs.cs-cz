---
title: Proxy aplikace služby Azure Active Directory a Tableau | Microsoft Docs
description: Další informace o použití Proxy aplikace služby Azure Active Directory (Azure AD) k zajištění vzdáleného přístupu pro vaše nasazení Tableau.  .
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 7fa8654e413ac337994d35afdce7bd3478127f64
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="azure-active-directory-application-proxy-and-tableau"></a>Proxy aplikace služby Azure Active Directory a Tableau 

Azure Proxy aplikace Active Directory a Tableau společně Ujistěte se, že budete moci snadno poskytnout vzdálený přístup pro vaše nasazení Tableau pomocí Proxy aplikace. Tento článek vysvětluje postup konfigurace tento scénář.  

## <a name="prerequisites"></a>Požadavky 

Tento scénář v tomto článku předpokládá, že máte:

- [Tableau](https://onlinehelp.tableau.com/current/server/en-us/proxy.htm#azure) nakonfigurované. 

- [Konektor Proxy aplikace](manage-apps/application-proxy-enable.md) nainstalována. 

 

## <a name="enabling-application-proxy-for-tableau"></a>Povolení Proxy aplikace u Tableau 

Pokud chcete použít pro Tableau Proxy aplikace, budete muset odeslat e-mail na [ aadapfeedback@microsoft.com ](mailto:aadapfeedback@microsoft.com) získat tento scénář povolena.
V e-mailu:

-   Povolení Proxy aplikace použít pro Tableau jako předmět
-   Zahrnout vaše ID klienta v textu    

Až budete připraveni k používání aplikace získáte potvrzení. Obvykle to trvá asi týden. Můžete však dokončit konfiguraci při čekání potvrzení.


 

## <a name="publish-your-applications-in-azure"></a>Publikování aplikací v Azure 

Pokud chcete publikovat Tableau, musíte publikovat aplikaci na portálu Azure.

Pro:

- Podrobné pokyny ke kroky 1-8, viz [publikování aplikací pomocí proxy aplikace služby Azure AD](manage-apps/application-proxy-publish-azure-portal.md). 
- Informace o tom, jak najít Tableau hodnot pro pole Proxy aplikace naleznete v dokumentaci Tableau.  

**K publikování aplikace**: 


1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) jako globální správce. 

2. Vyberte **Azure Active Directory > podnikové aplikace, které**. 

3. Vyberte **přidat** v horní části okna. 

4. Vyberte **místní aplikace**. 

5. Vyplňte požadovaná pole s informacemi o nové aplikace. Použijte následující pokyny pro nastavení: 

    - **Interní adresa URL**: Tato aplikace musí mít interní adresu URL, která je adresa URL Tableau sám sebe. Například, `https://adventure-works.tableau.com`. 

    - **Metoda předběžného ověřování**: Azure Active Directory (doporučená, ale nevyžaduje). 

6. Vyberte **přidat** v horní části okna. Přidání vaší aplikace a otevře se v nabídce rychlý start. 

7. V nabídce rychlý start, vyberte **přiřadit uživatele pro testování**, a přidejte alespoň jednoho uživatele k aplikaci. Ujistěte se, že tento testovací účet má přístup k aplikaci místní. 

8. Vyberte **přiřadit** uložit přiřazení uživatelských testu. 

9. (Volitelné) Na stránce Správa aplikací vyberte **jednotného přihlašování**. Zvolte **integrované ověřování systému Windows** z rozevírací nabídky a výplně se požadovaná pole v závislosti na vaší konfiguraci Tableau. Vyberte **Uložit**. 

 

## <a name="testing"></a>Testování 

Aplikace je nyní připraven k testování. Přístup k externí adresu URL můžete použít k publikování Tableau, a přihlaste se jako uživatel přiřazený k obě aplikace.



## <a name="next-steps"></a>Další postup

Další informace o proxy aplikace služby Azure AD najdete v tématu [jak poskytnout zabezpečený vzdálený přístup k místním aplikacím](manage-apps/application-proxy.md).

