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
ms.openlocfilehash: 83cf8840ffb9d123efa88d4acc304c8af4db5bf8
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/10/2021
ms.locfileid: "100102457"
---
# <a name="quickstart-set-up-a-tenant"></a>Rychlý start: Nastavení tenanta

K sestavování aplikací, které používají Microsoft Identity Platform pro správu identit a přístupu, potřebujete přístup k *tenantovi* Azure Active Directory (Azure AD). Je v tenantovi Azure AD, který zaregistrujete a spravujete své aplikace, můžete nakonfigurovat jejich přístup k datům v Microsoft 365 a dalších webových rozhraních API a povolit funkce jako podmíněný přístup.

Tenant reprezentuje organizaci. Jedná se o vyhrazenou instanci služby Azure AD, kterou vývojář organizace nebo aplikace obdrží na začátku relace s Microsoftem. Tento vztah může začít s registrací do Azure, Microsoft Intune nebo Microsoft 365, například.

Každý tenant Azure AD je samostatný a oddělený od ostatních tenantů Azure AD. Má své vlastní reprezentace pracovních a školních identit, identit uživatelů (Pokud se jedná o klienta Azure AD B2C) a registraci aplikací. Registrace aplikace ve vašem tenantovi může umožňovat ověřování jenom z účtů v rámci vašeho tenanta nebo všech tenantů.

## <a name="prerequisites"></a>Požadavky

Účet Azure, který má aktivní předplatné. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="determining-the-environment-type"></a>Určení typu prostředí

Můžete vytvořit dva typy prostředí. Prostředí závisí výhradně na typech uživatelů, které vaše aplikace ověří. 

Tento rychlý Start řeší dva scénáře pro typ aplikace, kterou chcete sestavit:

* Pracovní a školní účty (Azure AD) nebo účty Microsoft (například Outlook.com a Live.com)
* Účty sociálních i místních (Azure AD B2C)

## <a name="work-and-school-accounts-or-personal-microsoft-accounts"></a>Pracovní a školní účty nebo účty Microsoft

Pokud chcete vytvořit prostředí pro pracovní a školní účty nebo osobní účty Microsoft, můžete použít stávajícího tenanta Azure AD nebo vytvořit nový.
### <a name="use-an-existing-azure-ad-tenant"></a>Použití existujícího tenanta Azure AD

Spousta vývojářů už má klienty prostřednictvím služeb nebo předplatných, které jsou svázané s klienty Azure AD, jako jsou Microsoft 365 nebo předplatná Azure.

Chcete-li zjistit tenanta:

1. Přihlaste se na <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>. Použijte účet, který budete používat ke správě aplikace.
1. Podívejte se do pravého horního rohu. Pokud máte tenanta, budete automaticky přihlášeni. Název tenanta se zobrazí přímo pod názvem vašeho účtu.
   * Když najedete myší na název účtu, zobrazí se vaše jméno, e-mailová adresa, adresář nebo ID tenanta (GUID) a doména.
   * Pokud je k vašemu účtu přidruženo více tenantů, můžete výběrem názvu vašeho účtu otevřít nabídku, ve které můžete mezi jednotlivými tenanty přepínat. Každý tenant má vlastní ID tenanta.

> [!TIP]
> Pokud chcete zjistit ID tenanta, můžete:
> * Pokud chcete získat ID adresáře nebo tenanta, najeďte myší na název účtu.
> * Vyhledejte a vyberte **Azure Active Directory**  >  **vlastnosti**  >  **ID tenanta** v Azure Portal.

Pokud nemáte k vašemu účtu přidružený tenant, zobrazí se pod názvem vašeho účtu identifikátor GUID. Dokud nevytvoříte tenanta Azure AD, nebudete moct provádět akce jako registrace aplikací.

### <a name="create-a-new-azure-ad-tenant"></a>Vytvoření nového tenanta Azure AD

Pokud ještě nemáte tenanta Azure AD nebo pokud chcete vytvořit nový pro vývoj, přečtěte si téma [Vytvoření nového tenanta ve službě Azure AD](../fundamentals/active-directory-access-create-new-tenant.md). Nebo použijte [prostředí pro vytváření adresářů](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) v Azure Portal. 

K vytvoření nového tenanta zadáte následující informace:

- **Název organizace**
- **Počáteční doména** – tato doména je součástí *. onmicrosoft.com. Doménu můžete přizpůsobit později.
- **Země nebo oblast**

> [!NOTE]
> Při pojmenování tenanta použijte alfanumerické znaky. Speciální znaky nejsou povoleny. Název nesmí být delší než 256 znaků.

## <a name="social-and-local-accounts"></a>Účty na sociálních sítích a místní účty

Pokud chcete začít sestavovat aplikace, které přihlásíte sociální a místní účty, vytvořte tenanta Azure AD B2C. Chcete-li začít, přečtěte si téma [Vytvoření klienta Azure AD B2C](../../active-directory-b2c/tutorial-create-tenant.md).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zaregistrujte aplikaci](quickstart-register-app.md) , která se má integrovat s platformou Microsoft identity.
