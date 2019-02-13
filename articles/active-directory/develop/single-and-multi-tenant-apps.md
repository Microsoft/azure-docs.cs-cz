---
title: Jednotné a víceklientské aplikace v Azure Active Directory
description: Další informace o funkcích a rozdíly mezi aplikacemi pro jednoho tenanta a více tenantů ve službě Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: justhu
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57ec1939dd1f5445583200776e489f7596f572f0
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56178443"
---
# <a name="tenancy-in-azure-active-directory"></a>Architektura v Azure Active Directory

Azure Active Directory (Azure AD) slouží k uspořádání objektů, jako jsou uživatelé a aplikace do skupin volá *tenantů*. Tenanti umožňují správcům nastavit zásady pro uživatele v rámci organizace a aplikace, které organizace je vlastníkem ke splnění jejich zabezpečení a zkontrolovala zásady. 

## <a name="who-can-sign-in-to-your-app"></a>Kdo se může přihlásit do aplikace?

Při rozhodování o vývoj aplikací, mohou zvolit bude jedním tenantem nebo více tenanty při registraci aplikace ve své aplikaci nakonfigurovat tak, [webu Azure portal](https://portal.azure.com).
* Aplikace s jedním tenantem jsou dostupné jenom v tenantovi, které jste zaregistrovali v rámci, označované také jako jejich domovské tenanta.
* Aplikace s více tenanty jsou dostupné pro uživatele v jeho domovském tenantovi a jiných tenantů.

Na webu Azure Portal můžete nakonfigurovat aplikaci tak, aby se jedním tenantem nebo více tenanty nastavením cílovou skupinu.

| Cílová skupina | Jeden nebo více-tenant | Kdo se může přihlásit | 
|----------|--------| ---------|
| V tomto adresáři pouze účty | Jeden tenant | Vaši aplikaci nebo rozhraní API můžou používat všechny účty uživatelů a hostů ve vašem adresáři.<br>*Tuto možnost použijte, pokud vaší cílovou skupinou probíhá interně ve vaší organizaci.* |
| Účty v libovolném adresáři Azure AD | Víc klientů | Všech uživatelů a hostů pomocí pracovního nebo školního účtu společnosti Microsoft můžete použít, vaše aplikace nebo rozhraní API. To zahrnuje školy a podniky, které používají Office 365.<br>*Tuto možnost použijte, pokud vaší cílovou skupinou obchodní nebo vzdělávací zákazníků.* |
| Účty v jakékoli adresáře služby Azure AD a osobní účty Microsoft (jako je Skype, Xbox, Outlook.com) | Víc klientů | Všichni uživatelé s pracovní nebo školní nebo osobní účet Microsoft můžete použít, vaše aplikace nebo rozhraní API. Zahrnuje školy a podniky, které používají Office 365, jakož i osobní účty, které se používají k přihlášení ke službám, jako jsou Xbox a Skype.<br>*Tuto možnost použijte k cílení nejširší sadu účtů Microsoft.* | 

## <a name="best-practices-for-multi-tenant-apps"></a>Osvědčené postupy pro aplikace s více tenanty

Vytvářet skvělé aplikace s více tenanty může být náročné z důvodu počet různých zásad, které správci IT můžou nastavit v svým klientům. Pokud se rozhodnete k sestavení aplikace s více tenanty, dodržujte tyto doporučené postupy zabezpečení:

* Otestujte aplikaci v tenantovi, který byl nakonfigurován [zásady podmíněného přístupu](conditional-access-dev-guide.md).
* Postupujte podle principu nejnižší přístup uživatelů k zajištění, že vaše aplikace požaduje pouze oprávnění, které skutečně potřebuje. Vyhněte se požaduje oprávnění, které vyžadují souhlas správce, protože to může zabránit uživatelům v získání vaší aplikace v některých organizacích. 
* Zadejte odpovídající názvy a popisy pro veškerá potřebná oprávnění je vystavit v rámci vaší aplikace. Díky tomu mohou uživatelé a správci vědět, co, souhlasíte s při pokusu o použití rozhraní API vaší aplikace. Další informace naleznete v části osvědčené postupy v [oprávnění průvodce](v1-permissions-and-consent.md).

## <a name="next-steps"></a>Další postup

* [Jak převést aplikaci na více tenantů](howto-convert-app-to-be-multi-tenant.md)
