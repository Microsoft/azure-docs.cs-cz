---
title: 'Rychlý Start: vytvoření tenanta Azure Active Directory'
titleSuffix: Microsoft identity platform
description: V tomto rychlém startu se dozvíte, jak vytvořit tenanta Azure Active Directory pro použití při vývoji aplikací, které používají platformu Microsoft identity pro ověřování a autorizaci.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: quickstart
ms.date: 03/12/2020
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: f43cb80a148f5dadb409f506f237cb1210a2000a
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97933995"
---
# <a name="quickstart-set-up-a-tenant"></a>Rychlý start: Nastavení tenanta

Microsoft Identity Platform umožňuje vývojářům vytvářet aplikace určené pro širokou škálu vlastních prostředí a identit Microsoft 365. Pokud chcete začít používat Microsoft Identity Platform, budete potřebovat přístup k prostředí, které se označuje taky jako tenant služby Azure AD, který může registrovat a spravovat aplikace, mít přístup k Microsoft 365 dat a nasazovat vlastní podmíněný přístup a omezení klientů.

Klient představuje organizaci. Jedná se o vyhrazenou instanci Azure AD, kterou organizace nebo vývojář aplikací obdrží při vytvoření partnerství s Microsoftem, například registrací do Azure, Microsoft Intune nebo Microsoft 365.

Každý tenant Azure AD je odlišný a oddělený od ostatních tenantů Azure AD a používá vlastní reprezentaci pracovních a školních identit, identit zákazníků (pokud se jedná o tenanta Azure AD B2C) a registrací aplikací. Registrace aplikace uvnitř vašeho tenanta může umožňovat ověřování z účtů pouze v rámci vašeho tenanta nebo ve všech tenantech.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="determining-environment-type"></a>Určení typu prostředí

Existují dva typy prostředí, které můžete vytvořit. Určení potřebného typu prostředí vychází pouze z typu uživatelů, které bude vaše aplikace ověřovat.

* Pracovní a školní účty (účty Azure AD) nebo účty Microsoft (například outlook.com a live.com)
* Účty na sociálních sítích a místní účty (Azure AD B2C)

Tento rychlý start je rozdělený na dva scénáře v závislosti na typu aplikace, kterou chcete vytvořit.

## <a name="work-and-school-accounts-or-personal-microsoft-accounts"></a>Pracovní a školní účty nebo účty Microsoft

### <a name="use-an-existing-tenant"></a>Použití existujícího tenanta

Řada vývojářů už má tenanty vytvořené prostřednictvím služeb nebo předplatných vázaných na tenanty Azure AD, jako jsou předplatná Microsoft 365 nebo Azure.

1. Pokud chcete zkontrolovat, jestli máte tenanta, přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí účtu, který chcete použít ke správě vaší aplikace.
1. Zkontrolujte pravý horní roh. Pokud tenanta máte, budete k němu automaticky přihlášeni a název tenanta se zobrazí přímo pod názvem vašeho účtu.
   * Najeďte myší na název vašeho účtu v pravém horním rohu webu Azure Portal, zobrazí se vaše jméno, e-mail, ID adresáře nebo tenanta (GUID) a vaše doména.
   * Pokud je k vašemu účtu přidruženo více tenantů, můžete výběrem názvu vašeho účtu otevřít nabídku, ve které můžete mezi jednotlivými tenanty přepínat. Každý tenant má vlastní ID tenanta.

> [!TIP]
> Pokud potřebujete zjistit ID tenanta, můžete to provést následovně:
> * Najeďte myší na název vašeho účtu, aby se zobrazilo ID adresáře nebo tenanta.
> * Vyhledat a vybrat **Azure Active Directory > vlastnosti > ID tenanta** v Azure Portal

Pokud existujícího tenanta přidruženého k účtu nemáte, zobrazí se pod názvem vašeho účtu GUID a než dokončíte postup v následující části, nebudete moct provádět akce, jako jsou třeba registrace aplikací.

### <a name="create-a-new-azure-ad-tenant"></a>Vytvoření nového tenanta Azure AD

Pokud ještě nemáte tenanta Azure AD nebo chcete vytvořit nový pro vývoj, Projděte si [rychlý Start](../fundamentals/active-directory-access-create-new-tenant.md) nebo jednoduše postupujte podle [možnosti vytvoření adresáře](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory). Při vytváření nového tenanta budete muset zadat následující informace:

- **Název organizace**
- **Počáteční doména** – bude to část domény *.onmicrosoft.com. Doménu můžete dále přizpůsobit později.
- **Země nebo oblast**

> [!NOTE]
> Při pojmenování tenanta použijte alfanumerické znaky. Speciální znaky nejsou povoleny. Název nesmí být delší než 256 znaků.

## <a name="social-and-local-accounts"></a>Účty na sociálních sítích a místní účty

Pokud chcete začít vytvářet aplikace s přihlašováním účtů na sociálních sítích a místních účtů, budete muset vytvořit tenanta Azure AD B2C. Pokud chcete začít, postupujte podle pokynů k [vytvoření tenanta Azure AD B2C](../../active-directory-b2c/tutorial-create-tenant.md).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zaregistrujte aplikaci](quickstart-register-app.md) , která se má integrovat s platformou Microsoft identity.
