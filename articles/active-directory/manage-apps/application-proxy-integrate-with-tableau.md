---
title: Proxy aplikací služby Azure Active Directory a Tableau | Microsoft Docs
description: Naučte se používat proxy aplikace Azure Active Directory (Azure AD) k poskytnutí vzdáleného přístupu k nasazení Tableau.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1aa99e7e71ad78a62c1a9da303b2ecc8347ebeb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "65783852"
---
# <a name="azure-active-directory-application-proxy-and-tableau"></a>Proxy aplikací služby Azure Active Directory a Tableau 

Proxy aplikací služby Azure Active Directory a Tableau byly partnerem, aby bylo zajištěno, že budete moci snadno používat proxy aplikací k poskytování vzdáleného přístupu k nasazení Tableau. Tento článek vysvětluje, jak tento scénář nakonfigurovat.  

## <a name="prerequisites"></a>Požadavky 

Scénář v tomto článku předpokládá, že máte následující:

- [Tableau](https://onlinehelp.tableau.com/current/server/en-us/proxy.htm#azure) nakonfigurovaný. 

- Je nainstalovaný [konektor proxy aplikace](application-proxy-add-on-premises-application.md) . 

 
## <a name="enabling-application-proxy-for-tableau"></a>Povolení proxy aplikací pro Tableau 

Proxy aplikací podporuje tok udělení OAuth 2,0, který je nutný ke správnému fungování Tableau. To znamená, že neexistují žádné zvláštní kroky nutné k povolení této aplikace, kromě jejich konfigurace, podle následujících kroků publikování.


## <a name="publish-your-applications-in-azure"></a>Publikování aplikací v Azure 

K publikování Tableau je potřeba publikovat aplikaci na webu Azure Portal.

For

- Podrobné pokyny k krokům 1-8 najdete v tématu [publikování aplikací pomocí Azure proxy aplikací služby AD](application-proxy-add-on-premises-application.md). 
- Informace o tom, jak najít hodnoty Tableau pro pole proxy aplikací, najdete v dokumentaci k Tableau.  

**Publikování aplikace**: 


1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako správce aplikace. 

2. Vyberte **Azure Active Directory > podnikových aplikací**. 

3. V horní části okna vyberte **Přidat** . 

4. Vyberte **místní aplikaci**. 

5. Vyplňte požadovaná pole informacemi o vaší nové aplikaci. Pro nastavení použijte následující pokyny: 

    - **Interní adresa URL**: Tato aplikace by měla mít interní adresu URL, která je Tableau adresou URL. Například, `https://adventure-works.tableau.com`. 

    - **Metoda**předběžného ověřování: Azure Active Directory (doporučeno, ale není nutné). 

6. V horní části okna vyberte **Přidat** . Vaše aplikace se přidá a otevře se nabídka rychlý Start. 

7. V nabídce rychlý Start vyberte **přiřadit uživatele pro testování**a přidejte alespoň jednoho uživatele do aplikace. Ujistěte se, že tento testovací účet má přístup k místní aplikaci. 

8. Vyberte **přiřadit** a uložte přiřazení testovacího uživatele. 

9. Volitelné Na stránce Správa aplikací vyberte **jednotné přihlašování**. V rozevírací nabídce vyberte **integrované ověřování systému Windows** a vyplňte požadovaná pole na základě konfigurace Tableau. Vyberte **Uložit**. 

 

## <a name="testing"></a>Testování 

Vaše aplikace je teď připravená k testování. Přístup k externí adrese URL, kterou jste použili k publikování Tableau, a přihlaste se jako uživatel přiřazený k oběma aplikacím.



## <a name="next-steps"></a>Další kroky

Další informace o službě Azure Proxy aplikací služby AD najdete v tématu [jak poskytnout zabezpečený vzdálený přístup k místním aplikacím](application-proxy.md).

