---
title: Jediné a víceklientské aplikace v Azure AD
titleSuffix: Microsoft identity platform
description: Přečtěte si o funkcích a rozdílech mezi aplikacemi pro jednoho tenanta a víceklientské aplikace v Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/24/2020
ms.author: ryanwi
ms.reviewer: justhu
ms.custom: aaddev
ms.openlocfilehash: 0868d87d977b15a552b04d5dbd6d19de6931f0ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91395902"
---
# <a name="tenancy-in-azure-active-directory"></a>Architektura tenantů v Azure Active Directory

Azure Active Directory (Azure AD) uspořádá objekty, jako jsou uživatelé a aplikace, do skupin označovaných jako *klienti*. Klienti umožňují správcům nastavit zásady pro uživatele v rámci organizace a aplikace, které organizace vlastní, aby splnila své zásady zabezpečení a provozu. 

## <a name="who-can-sign-in-to-your-app"></a>Kdo se může přihlásit ke svojí aplikaci?

Když se přihlásí k vývoji aplikací, můžou si vývojáři zvolit, aby svou aplikaci během registrace aplikace v [Azure Portal](https://portal.azure.com)nakonfigurovali buď jako jeden tenant, nebo pro více tenantů.
* Aplikace pro jednoho tenanta jsou k dispozici pouze v tenantovi, které byly zaregistrovány, označované také jako jejich domácí tenant.
* Víceklientské aplikace jsou k dispozici uživatelům v jejich domovském tenantovi i v dalších klientech.

V Azure Portal můžete svou aplikaci nakonfigurovat tak, aby byla jedním nebo více klienty, nastavením cílové skupiny podle následujících pokynů.

| Cílová skupina | Jeden nebo více tenantů | Kdo se může přihlásit | 
|----------|--------| ---------|
| Účty pouze v tomto adresáři | Jeden tenant | Vaše aplikace nebo rozhraní API můžou používat všechny účty uživatelů a hostů v adresáři.<br>*Tuto možnost použijte, pokud je cílová skupina vaší organizace interní.* |
| Účty v jakémkoli adresáři služby Azure AD | Vícetenantové | Vaše aplikace nebo rozhraní API můžou používat všichni uživatelé a hosté s pracovním nebo školním účtem od Microsoftu. To zahrnuje školy a firmy, které používají Microsoft 365.<br>*Tuto možnost použijte, pokud vaše cílová skupina představuje obchodní nebo vzdělávací zákazníky.* |
| Účty v jakémkoli adresáři služby Azure AD a osobních účtech Microsoft (například Skype, Xbox, Outlook.com) | Vícetenantové | Vaši aplikaci nebo rozhraní API můžou používat všichni uživatelé s pracovními nebo školními nebo osobními účet Microsoft. Zahrnuje školy a firmy, které používají Microsoft 365 a také osobní účty, které slouží k přihlašování ke službám, jako jsou Xbox a Skype.<br>*Tuto možnost použijte, chcete-li cílit na nejširší sadu účtů Microsoft.* | 

## <a name="best-practices-for-multi-tenant-apps"></a>Osvědčené postupy pro víceklientské aplikace

Vytváření skvělých aplikací s více klienty může být náročné kvůli počtu různých zásad, které správci IT můžou ve svých klientech nastavit. Pokud se rozhodnete sestavit aplikaci pro více tenantů, postupujte podle těchto osvědčených postupů:

* Otestujte svoji aplikaci v tenantovi, která má nakonfigurované [zásady podmíněného přístupu](../azuread-dev/conditional-access-dev-guide.md).
* Postupujte podle principu minimálního přístupu uživatelů, abyste měli jistotu, že vaše aplikace žádá jenom o oprávnění, která skutečně potřebují. 
* Poskytněte vhodné názvy a popisy pro všechna oprávnění, která vystavíte v rámci vaší aplikace. To pomáhá uživatelům a správcům zjistit, na co se dohodli při pokusu o použití rozhraní API vaší aplikace. Další informace najdete v části osvědčené postupy v [Průvodci oprávněními](v2-permissions-and-consent.md).

## <a name="next-steps"></a>Další kroky

* [Jak převést aplikaci na více tenantů](howto-convert-app-to-be-multi-tenant.md)
