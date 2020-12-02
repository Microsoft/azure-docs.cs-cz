---
title: 'Postupy: Změna typů účtů podporovaných aplikací | Azure'
titleSuffix: Microsoft identity platform
description: V tomto postupu nakonfigurujete aplikaci registrovanou s platformou Microsoft identity na změnu toho, kdo nebo jaké účty mají přístup k aplikaci.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/15/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: marsma, aragra, lenalepa, sureshja
ms.openlocfilehash: 94a7f4d9ce1471aa1dd6aef3165562a2abc02816
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96453267"
---
# <a name="how-to-modify-the-accounts-supported-by-an-application"></a>Postup úpravy účtů podporovaných aplikací

Když zaregistrujete aplikaci s platformou Microsoft identity, zadali jste, kteří typy účtů k nim mají přístup. Můžete například zadat účty pouze ve vaší organizaci, což je jedna aplikace pro *jednoho tenanta* . Nebo jste mohli zadat účty v jakékoli organizaci (včetně vašeho), což je *víceklientské* aplikace.

V následujících částech se dozvíte, jak změnit registraci vaší aplikace v Azure Portal, abyste změnili, kdo nebo jaké typy účtů mají přístup k aplikaci.

## <a name="prerequisites"></a>Předpoklady

* [Aplikace zaregistrovaná v Tenantovi Azure AD](quickstart-register-app.md)

## <a name="change-the-application-registration-to-support-different-accounts"></a>Změna registrace aplikace pro podporu různých účtů

Pokud chcete zadat jiné nastavení pro typy účtů podporované při registraci existující aplikace:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
1. Máte-li přístup k více klientům, použijte filtr **adresář + odběr** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: v horní nabídce a vyberte klienta, ve kterém chcete aplikaci zaregistrovat.
1. Vyhledejte a vyberte **Azure Active Directory**.
1. V části **Spravovat** vyberte **Registrace aplikací** a pak vyberte svou aplikaci.
1. Nyní určete, kdo může používat aplikaci, někdy označovanou jako *cílová skupina pro přihlášení*.

    | Podporované typy účtu | Popis |
    |-------------------------|-------------|
    | **Účty jen v tomto organizačním adresáři** | Tuto možnost vyberte, pokud vytváříte aplikaci pro použití jenom pro uživatele (nebo hosty) ve *vašem* tenantovi.<br><br>Je často označována jako *obchodní* aplikace (LOB), což je **jediná klientská** aplikace na platformě Microsoft identity. |
    | **Účty v libovolném organizačním adresáři** | Tuto možnost vyberte, pokud chcete, aby se vaše aplikace mohla používat pro uživatele v *jakémkoli* tenantovi služby Azure AD. Tato možnost je vhodná, pokud například vytváříte aplikaci typu software jako služba (SaaS), kterou máte v úmyslu poskytnout více organizacím.<br><br>Tato aplikace se označuje jako **víceklientská** aplikace na platformě Microsoft identity. |
1. Vyberte **Uložit**.

### <a name="why-changing-to-multi-tenant-can-fail"></a>Proč se změna na více tenantů může zdařit

Přepnutí registrace aplikace z jednoho na více tenantů může někdy selhat z důvodu kolizí názvů identifikátoru URI ID aplikace (identifikátor URI ID aplikace). Identifikátor URI ID aplikace je příklad `https://contoso.onmicrosoft.com/myapp` .

Identifikátor URI ID aplikace je jedním ze způsobů, kterými se může aplikace ve zprávách protokolu identifikovat. V případě aplikace s jedním klientem musí být identifikátor URI ID aplikace jedinečný jenom v rámci tohoto tenanta. Pro víceklientské aplikace musí být globálně jedinečné, aby služba Azure AD mohla najít aplikaci napříč všemi klienty. Globální jedinečnost se vynutila tím, že se vyžaduje, aby název hostitele identifikátoru URI ID aplikace odpovídal jednomu z [ověřených domén vydavatelů](howto-configure-publisher-domain.md)v TENANTOVI Azure AD.

Například pokud je název vašeho tenanta *contoso.onmicrosoft.com*, pak `https://contoso.onmicrosoft.com/myapp` je platný identifikátor URI ID aplikace. Pokud má váš tenant ověřenou doménu *contoso.com*, bude také platný identifikátor URI ID aplikace `https://contoso.com/myapp` . Pokud identifikátor URI ID aplikace nedodržuje druhý vzor, `https://contoso.com/myapp` převod registrace aplikace na více tenantů se nezdařil.

Další informace o konfiguraci ověřené domény vydavatele najdete v tématu [Konfigurace ověřené domény](howto-configure-publisher-domain.md).

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o požadavcích na [Převod aplikace z jednoho na více tenantů](howto-convert-app-to-be-multi-tenant.md).
