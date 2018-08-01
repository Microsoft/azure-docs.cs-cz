---
title: Proxy aplikací Azure Active Directory a tableau. představují | Dokumentace Microsoftu
description: Další informace o použití Proxy aplikace Azure Active Directory (Azure AD) pro zajištění vzdáleného přístupu pro vaše nasazení Tableau.  .
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 7cace1af527c1c7c80bf0e23f7a88aa9ac9f9d03
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365018"
---
# <a name="azure-active-directory-application-proxy-and-tableau"></a>Proxy aplikací Azure Active Directory a tableau. představují 

Azure Active Directory Application Proxy a tableau. představují uzavřeli partnerství s cílem zajistit, že můžete snadno provádět využití Proxy aplikace k zajištění vzdáleného přístupu pro vaše nasazení Tableau. Tento článek vysvětluje postup konfigurace tohoto scénáře.  

## <a name="prerequisites"></a>Požadavky 

Scénář v tomto článku se předpokládá, že máte:

- [Tableau](https://onlinehelp.tableau.com/current/server/en-us/proxy.htm#azure) nakonfigurované. 

- [Konektor Proxy aplikací](application-proxy-enable.md) nainstalované. 

 

## <a name="enabling-application-proxy-for-tableau"></a>Povolení Proxy aplikace u Tableau 

Pokud chcete použít Proxy aplikace pro Tableau, budete muset odeslat e-mailu [ aadapfeedback@microsoft.com ](mailto:aadapfeedback@microsoft.com) Chcete-li získat tento scénář povolena.
V e-mailu:

-   Povolení Proxy aplikace použít pro Tableau jako předmět
-   Obsahovat vaše ID tenanta v textu    

Až budete připravení k používání aplikace dostanete potvrzení. Dokončete požadované konfigurace při čekání na potvrzení.


 

## <a name="publish-your-applications-in-azure"></a>Publikování aplikací v Azure 

Pokud chcete publikovat Tableau, je třeba publikovat aplikaci na webu Azure Portal.

Pro:

- Podrobné pokyny z kroků 1-8, viz [publikování aplikací pomocí Proxy aplikací Azure AD](application-proxy-publish-azure-portal.md). 
- Informace o tom, jak najít tableau. představují hodnoty pro pole Proxy aplikací naleznete v dokumentaci Tableau.  

**K publikování aplikace**: 


1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) jako globální správce. 

2. Vyberte **Azure Active Directory > podnikové aplikace**. 

3. Vyberte **přidat** v horní části okna. 

4. Vyberte **On-premises application**. 

5. Vyplňte požadovaná pole s informacemi o novou aplikaci. Použijte následující pokyny pro nastavení: 

    - **Interní adresa URL**: Tato aplikace by měla mít interní adresa URL adresy URL Tableau samotné. Například, `https://adventure-works.tableau.com`. 

    - **Metoda předběžného ověřování služby**: Azure Active Directory (doporučené, ale nevyžaduje). 

6. Vyberte **přidat** v horní části okna. Přidá se vaše aplikace a otevře se nabídka rychlý start. 

7. V nabídce rychlý start vyberte **přiřadit uživatele pro testování**, a přidejte aspoň jednoho uživatele k aplikaci. Ujistěte se, že testovacího účtu má přístup k místní aplikaci. 

8. Vyberte **přiřadit** se uložit přiřazení uživatelských testu. 

9. (Volitelné) Na stránce Správa aplikací vyberte **jednotného přihlašování**. Zvolte **integrované ověřování Windows** z rozevírací nabídky a zadejte požadovaná pole na základě vaší konfigurace Tableau. Vyberte **Uložit**. 

 

## <a name="testing"></a>Testování 

Vaše aplikace je teď připravený k testování. Na externí adresu URL jste použili k publikování Tableau, a přihlaste se jako uživatel přiřazený na obě aplikace.



## <a name="next-steps"></a>Další postup

Další informace o Proxy aplikací Azure AD najdete v tématu [jak poskytnout zabezpečený vzdálený přístup k místním aplikacím](application-proxy.md).

