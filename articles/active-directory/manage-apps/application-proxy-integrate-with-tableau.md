---
title: Proxy a tableau aplikace služby Azure Active Directory | Dokumenty společnosti Microsoft
description: Zjistěte, jak používat proxy aplikace Azure Active Directory (Azure AD) k poskytování vzdáleného přístupu pro nasazení tableau.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "65783852"
---
# <a name="azure-active-directory-application-proxy-and-tableau"></a>Proxy a tableau aplikace služby Azure Active Directory 

Proxy aplikace Azure Active Directory a Tableau se spojily, aby zajistily, že budete moci snadno použít proxy aplikace k poskytování vzdáleného přístupu pro nasazení Tableau. Tento článek vysvětluje, jak nakonfigurovat tento scénář.  

## <a name="prerequisites"></a>Požadavky 

Scénář v tomto článku předpokládá, že máte:

- [Tableau](https://onlinehelp.tableau.com/current/server/en-us/proxy.htm#azure) nakonfigurován. 

- Nainstalován [konektor proxy aplikace.](application-proxy-add-on-premises-application.md) 

 
## <a name="enabling-application-proxy-for-tableau"></a>Povolení proxy aplikace pro tableau 

Proxy aplikace podporuje Grant Flow OAuth 2.0, který je nutný pro správnou funkci Tableau. To znamená, že již nejsou nutné žádné zvláštní kroky potřebné k povolení této aplikace, kromě konfigurace pomocí následujících kroků publikování.


## <a name="publish-your-applications-in-azure"></a>Publikování aplikací v Azure 

Pokud chcete publikovat Tableau, musíte publikovat aplikaci na webu Azure Portal.

Pro:

- Podrobné pokyny k krokům 1-8 najdete v [tématu Publikování aplikací pomocí proxy aplikace Azure AD](application-proxy-add-on-premises-application.md). 
- Informace o tom, jak najít hodnoty Tableau pro pole Proxy aplikace, naleznete v dokumentaci tableau.  

**Jak publikovat aplikaci**: 


1. Přihlaste se k [portálu Azure](https://portal.azure.com) jako správce aplikací. 

2. Vyberte **azure active directory > podnikové aplikace**. 

3. V horní části nože vyberte **Přidat.** 

4. Vyberte **místní aplikaci**. 

5. Vyplňte požadovaná pole s informacemi o nové aplikaci. Pro nastavení použijte následující pokyny: 

    - **Interní adresa URL**: Tato aplikace by měla mít interní adresu URL, která je samotná adresa URL Tableau. Například, `https://adventure-works.tableau.com`. 

    - **Metoda předběžného ověřování:** Služba Azure Active Directory (doporučeno, ale není vyžadováno). 

6. V horní části nože vyberte **Přidat.** Aplikace je přidána a otevře se nabídka rychlého spuštění. 

7. V nabídce Rychlý start vyberte **Přiřadit uživatele k testování**a přidejte do aplikace alespoň jednoho uživatele. Ujistěte se, že tento testovací účet má přístup k místní aplikaci. 

8. Vyberte **Přiřadit,** chcete-li uložit přiřazení testovacího uživatele. 

9. (Nepovinné) Na stránce správa aplikací vyberte **Jednotné přihlašování**. Z rozevírací nabídky zvolte **Integrované ověřování systému Windows** a vyplňte požadovaná pole na základě konfigurace Tableau. Vyberte **Uložit**. 

 

## <a name="testing"></a>Testování 

Aplikace je nyní připravena k testování. Získejte přístup k externí adrese URL, kterou jste použili k publikování tableau, a přihlaste se jako uživatel přiřazený k oběma aplikacím.



## <a name="next-steps"></a>Další kroky

Další informace o proxy aplikací azure ad najdete v [tématu Jak zajistit zabezpečený vzdálený přístup k místním aplikacím](application-proxy.md).

