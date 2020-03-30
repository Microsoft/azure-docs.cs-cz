---
title: Vytvoření tenanta Azure pro víceklientské aplikace
description: Pokyny pro nezávislé dodavatele softwaru pro integraci s Azure Active Directory
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
ms.openlocfilehash: 637adba89445e6974e83486f0641576225ccd268
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70812610"
---
# <a name="create-an-azure-tenant-for-a-multi-tenant-application"></a>Vytvoření tenanta Azure pro víceklientské aplikace  

Chcete-li poskytnout přístup k aplikaci s více klienty, musíte vytvořit tenanta služby Azure Active Directory, který aplikaci zaregistruje a povolí federaci identit zákazníka. Viz [Výběr správného federačního protokolu pro víceklientské aplikace](isv-choose-multi-tenant-federation.md). Tento tenant vám umožní otestovat vaši aplikaci a federaci v prostředí, které je podobné vašim zákazníkům prostředí Azure AD.

## <a name="costs-of-hosting-a-multi-tenant-application"></a>Náklady na hostování víceklientské aplikace

Azure Active Directory je k dispozici ve více edicích. [Podívejte se na podrobné porovnání funkcí](https://azure.microsoft.com/pricing/details/active-directory/).

Můžete si vytvořit předplatné Azure a aktivní adresář Azure zdarma a používat základní funkce.

## <a name="create-your-tenant"></a>Vytvoření tenanta

1. Vytvořte klienta. Viz [Nastavení prostředí pro dev](../develop/quickstart-create-new-tenant.md).

2. Povolení a testování jednotného přihlášení k vaší aplikaci,

   a. **Pro aplikace OIDC nebo Oath** [zaregistrujte aplikaci](../develop/quickstart-register-app.md) jako víceklientské aplikace. V podporovaných typech účtů vyberte možnost Účty v libovolném organizačním adresáři a osobních účtech Microsoft.

   b. **Pro saml- a WS-Fed-založené aplikace** [, můžete nakonfigurovat SAML založené jednotné přihlašování](configure-single-sign-on-non-gallery-applications.md) aplikace pomocí obecné šablony SAML v Azure AD.

V případě potřeby můžete také [převést aplikaci s jedním tenantem na více klienta.](../develop/howto-convert-app-to-be-multi-tenant.md)

## <a name="next-steps"></a>Další kroky

[Integrace přihlašovat do aplikace](isv-sso-content.md)
