---
title: Proxy aplikací Azure Active Directory a tableau. představují | Dokumentace Microsoftu
description: Další informace o použití Proxy aplikace Azure Active Directory (Azure AD) pro zajištění vzdáleného přístupu pro vaše nasazení Tableau.
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: celested
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e154a53dc32aea0b3fbe093526546b080b02ceff
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56188456"
---
# <a name="azure-active-directory-application-proxy-and-tableau"></a>Proxy aplikací Azure Active Directory a tableau. představují 

Azure Active Directory Application Proxy a tableau. představují uzavřeli partnerství s cílem zajistit, že můžete snadno provádět využití Proxy aplikace k zajištění vzdáleného přístupu pro vaše nasazení Tableau. Tento článek vysvětluje postup konfigurace tohoto scénáře.  

## <a name="prerequisites"></a>Požadavky 

Scénář v tomto článku se předpokládá, že máte:

- [Tableau](https://onlinehelp.tableau.com/current/server/en-us/proxy.htm#azure) nakonfigurované. 

- [Konektor Proxy aplikací](application-proxy-add-on-premises-application.md) nainstalované. 

 
## <a name="enabling-application-proxy-for-tableau"></a>Povolení Proxy aplikace u Tableau 

Proxy aplikací podporuje OAuth 2.0 tok udělování, které jsou požadovány pro Tableau fungovala správně. To znamená, že již neexistují žádné speciální kroky potřebné k povolení této aplikace, než je konfigurace podle pokynů pro publikování níže.


## <a name="publish-your-applications-in-azure"></a>Publikování aplikací v Azure 

Pokud chcete publikovat Tableau, je třeba publikovat aplikaci na webu Azure Portal.

Pro:

- Podrobné pokyny z kroků 1-8, viz [publikování aplikací pomocí Proxy aplikací Azure AD](application-proxy-add-on-premises-application.md). 
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

