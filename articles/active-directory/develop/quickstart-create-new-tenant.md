---
title: Vytvoření tenanta Azure Active Directory
description: Zjistěte, jak vytvořit tenanta Azure AD pro použití při registraci a vytváření aplikací.
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
ms.openlocfilehash: 0e2247e94b20846f19c2ed26c96a5dc53972e770
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80883809"
---
# <a name="quickstart-set-up-a-tenant"></a>Úvodní příručka: Nastavení klienta

Microsoft Identity Platform umožňuje vývojářům vytvářet aplikace určené pro širokou škálu vlastních prostředí a identit Microsoft 365. Abyste mohli začít používat platformu identit Microsoftu, budete potřebovat přístup k prostředí, nazývanému také tenant azure ad, které může registrovat a spravovat aplikace, mít přístup k datům Microsoftu 365 a nasadit vlastní omezení podmíněného přístupu a tenanta.

Tenant je reprezentace organizace. Jedná se o vyhrazenou instanci Azure AD, kterou organizace nebo vývojář aplikací obdrží při vytvoření partnerství s Microsoftem, například registrací do Azure, Microsoft Intune nebo Microsoft 365.

Každý tenant Azure AD je odlišný a oddělený od ostatních tenantů Azure AD a používá vlastní reprezentaci pracovních a školních identit, identit zákazníků (pokud se jedná o tenanta Azure AD B2C) a registrací aplikací. Registrace aplikace uvnitř vašeho tenanta může umožňovat ověřování z účtů pouze v rámci vašeho tenanta nebo ve všech tenantech.

## <a name="determining-environment-type"></a>Určení typu prostředí

Existují dva typy prostředí, které můžete vytvořit. Určení potřebného typu prostředí vychází pouze z typu uživatelů, které bude vaše aplikace ověřovat.

* Pracovní a školní účty (účty Azure AD) nebo účty Microsoft (například outlook.com a live.com)
* Účty na sociálních sítích a místní účty (Azure AD B2C)

Tento rychlý start je rozdělený na dva scénáře v závislosti na typu aplikace, kterou chcete vytvořit. Pokud potřebujete další pomoc s určením typu identity, přečtěte si [informace o platformě Microsoft Identity Platform](about-microsoft-identity-platform.md).

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
> * Nebo na webu Azure Portal vyberte **Azure Active Directory > Vlastnosti > ID adresáře**.

Pokud existujícího tenanta přidruženého k účtu nemáte, zobrazí se pod názvem vašeho účtu GUID a než dokončíte postup v následující části, nebudete moct provádět akce, jako jsou třeba registrace aplikací.

### <a name="create-a-new-azure-ad-tenant"></a>Vytvoření nového tenanta Azure AD

Pokud ještě nemáte klienta Azure AD nebo chcete vytvořit nový pro vývoj, podívejte se na [rychlý start](../fundamentals/active-directory-access-create-new-tenant.md) nebo jednoduše postupujte podle prostředí [vytváření adresářů](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory). Při vytváření nového tenanta budete muset zadat následující informace:

- **Název organizace**
- **Počáteční doména** – bude to část domény *.onmicrosoft.com. Doménu můžete dále přizpůsobit později.
- **Země nebo oblast**

> [!NOTE]
> Při pojmenování klienta použijte alfanumerické znaky. Speciální znaky nejsou povoleny. Název nesmí přesáhnout 256 znaků.

## <a name="social-and-local-accounts"></a>Účty na sociálních sítích a místní účty

Pokud chcete začít vytvářet aplikace s přihlašováním účtů na sociálních sítích a místních účtů, budete muset vytvořit tenanta Azure AD B2C. Pokud chcete začít, postupujte podle pokynů k [vytvoření tenanta Azure AD B2C](../../active-directory-b2c/tutorial-create-tenant.md).

## <a name="next-steps"></a>Další kroky

* [Zaregistrujte aplikaci](quickstart-register-app.md) a integrujte ji s platformou microsoftu pro identity. 
* Naučte se [základy ověřování](authentication-scenarios.md).
* Podrobnosti o vztahu mezi předplatnými a klientem Azure AD najdete v tématu [Jak jsou předplatná Azure přidružená k Azure Active Directory.](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
