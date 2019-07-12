---
title: Vytvoření tenanta služby Azure pro aplikaci s více tenanty
description: Pokyny pro nezávislé dodavatele softwaru na integraci se službou Azure Active Directory
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69cc625500af60a753ad8e7db0363954088f3307
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659577"
---
# <a name="create-an-azure-tenant-for-a-multi-tenant-application"></a>Vytvoření tenanta služby Azure pro aplikaci s více tenanty  

Pro poskytnutí přístupu k vaší aplikace s více tenanty musí vytvořit tenanta služby Azure Active Directory pro registraci aplikace a povolit federaci identit vašich zákazníků. Zobrazit [výběr správné federace protokol pro vaše aplikace s více tenanty](isv-choose-multi-tenant-federation.md). Tento tenant vám umožní testovat aplikace a federace v prostředí, které se podobá prostředí zákazníků Azure AD.

## <a name="costs-of-hosting-a-multi-tenant-application"></a>Náklady na hostování aplikace s více tenanty

Azure Active Directory je k dispozici ve třech skladových položek, Free, Basic a Premium. [Zobrazit podrobné porovnání funkcí](https://azure.microsoft.com/pricing/details/active-directory/).

Můžete zdarma vytvořit vaše předplatné Azure a Azure active directory a používat základní funkce.

## <a name="create-your-tenant"></a>Vytvoření vašeho tenanta

1. Vytvoření vašeho Tenanta. Zobrazit [nastavit vývojové prostředí](../develop/quickstart-create-new-tenant.md).

2. Povolit a otestovat přístup jednotné přihlašování do vaší aplikace

   a. **U aplikací Oath OIDC**, [registrace vaší aplikace](../develop/quickstart-register-app.md) jako aplikaci s více tenanty. Vyberte si účty v jakékoli organizace adresáři a osobní účty možnost Microsoft typy podporované účtů

   b. **Pro aplikace a WS-Fed založené na SAML**, můžete [založené na SAML nakonfigurovat jednotné přihlašování](configure-single-sign-on-non-gallery-applications.md) aplikace pomocí obecného SAML šablony ve službě Azure AD.

Můžete také [převod jednoho tenanta aplikaci pro více tenantů](../develop/howto-convert-app-to-be-multi-tenant.md) v případě potřeby.

## <a name="next-steps"></a>Další kroky

[Integrace jednotného přihlašování v aplikaci](isv-sso-content.md)
