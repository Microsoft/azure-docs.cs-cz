---
title: 'Rychlý Start: Registrace aplikací pomocí Microsoft Identity Platform | Azure'
description: V tomto rychlém startu se dozvíte, jak přidat a zaregistrovat aplikaci na platformě Microsoft identity.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 03/12/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 79983678d13b810a521a00ba2c1978de92a5029f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81309505"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>Rychlý Start: registrace aplikace s platformou Microsoft identity

V tomto rychlém startu zaregistrujete aplikaci pomocí **registrace aplikacího** prostředí v Azure Portal. 

Vaše aplikace se integruje s platformou Microsoft identity tak, že ji zaregistrujete pomocí klienta Azure Active Directory. Podnikoví vývojáři a poskytovatelé softwaru jako služby (SaaS) můžou vyvíjet komerční cloudové služby nebo obchodní aplikace, které je možné integrovat s platformou Microsoft identity. Integrace poskytuje pro tyto služby zabezpečené přihlašování a autorizaci.

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Tenant služby Azure AD](quickstart-create-new-tenant.md).

## <a name="register-a-new-application-using-the-azure-portal"></a>Registrace nové aplikace pomocí portálu Azure Portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. Pokud vám váš účet poskytne přístup k více než jednomu klientovi, vyberte svůj účet v pravém horním rohu. Nastavte relaci portálu na klienta služby Azure AD, kterého chcete.
1. Vyhledejte a vyberte **Azure Active Directory**. V části **Spravovat** vyberte **Registrace aplikací**.
1. Vyberte **Nová registrace**.
1. V **registrování aplikace**zadejte smysluplný název aplikace, který se zobrazí uživatelům.
1. Určete, kdo může používat aplikaci následujícím způsobem:

    | Podporované typy účtu | Popis |
    |-------------------------|-------------|
    | **Účty jen v tomto organizačním adresáři** | Tuto možnost vyberte, pokud vytváříte obchodní aplikaci. Tato možnost není dostupná, pokud neprovádíte registraci aplikace v adresáři.<br><br>Tato možnost se mapuje pouze na účty Azure AD s jedním tenantem.<br><br>Tato možnost je výchozí, pokud neprovádíte registraci aplikace mimo adresář. V případech, kdy je aplikace zaregistrovaná mimo adresář, jsou výchozí možností účty Azure AD s více tenanty a osobní účty Microsoft. |
    | **Účty v libovolném organizačním adresáři** | Tuto možnost vyberte, pokud chcete cílit na všechny zákazníky z řad firem a vzdělávacích institucí.<br><br>Tato možnost se mapuje pouze na účty Azure AD s více tenanty.<br><br>Pokud jste aplikaci zaregistrovali jako jenom pro jednoho tenanta Azure AD, můžete ji aktualizovat na Azure AD multi-tenant a zpátky na jeden tenant prostřednictvím **ověřovací** stránky. |
    | **Účty v libovolném organizačním adresáři a osobní účty Microsoft** | Tuto možnost vyberte, pokud chcete cílit na co nejširší okruh zákazníků.<br><br>Tato možnost se mapuje na účty Azure AD s více tenanty a osobní účty Microsoft.<br><br>Pokud jste aplikaci zaregistrovali jako víceklientské a osobní účty Microsoft Azure AD, nemůžete toto nastavení změnit v uživatelském rozhraní. Místo toho musíte ke změně podporovaných typů účtu použít editor manifestu aplikace. |

1. V části **identifikátor URI přesměrování (volitelné)** vyberte typ aplikace, kterou vytváříte: **Web** nebo **veřejný klient (mobilní & Desktop)**. Pak zadejte identifikátor URI pro přesměrování nebo adresu URL odpovědi pro vaši aplikaci.

    * V případě webových aplikací zadejte základní adresu URL vaší aplikace. Například `https://localhost:31544` může být adresa URL pro webovou aplikaci spuštěnou na místním počítači. Uživatelé by se pomocí této adresy URL přihlašovali k webové klientské aplikaci.
    * V případě veřejných klientských aplikací zadejte identifikátor URI, který Azure AD použije k vrácení odpovědí týkajících se tokenu. Zadejte konkrétní hodnotu pro vaši aplikaci, například `myapp://auth`.

    Příklady webových aplikací nebo nativních aplikací najdete v tématu rychlé starty v [Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop).

1. Až budete hotovi, vyberte **Zaregistrovat**.

    ![Zobrazuje obrazovku pro registraci nové aplikace v Azure Portal](./media/quickstart-add-azure-ad-app-preview/new-app-registration.png)

Azure AD přiřadí vaší aplikaci jedinečnou aplikaci nebo klienta s ID. Portál otevře stránku s **přehledem** vaší aplikace. Pokud chcete do aplikace přidat funkce, můžete vybrat další možnosti konfigurace, včetně brandingu, certifikátů a tajných klíčů, oprávnění k rozhraní API a dalších.

![Příklad stránky s přehledem nově registrované aplikace](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png)

## <a name="next-steps"></a>Další kroky

* Přístup k webovým rozhraním API najdete v tématu [rychlý Start: Konfigurace klientské aplikace pro přístup k webovým rozhraním API](quickstart-configure-app-access-web-apis.md)

* Další informace o oprávněních najdete [v tématu oprávnění a souhlas v koncovém bodě Microsoft Identity Platform](v2-permissions-and-consent.md).

* Informace o vystavení webových rozhraní API najdete v tématu [rychlý Start: Konfigurace aplikace pro vystavování webových rozhraní API](quickstart-configure-app-expose-web-apis.md).

* Informace o správě podporovaných účtů najdete v tématu [rychlý Start: Změna účtů podporovaných aplikací](quickstart-modify-supported-accounts.md).

* Pokud chcete vytvořit aplikaci a přidat funkce, podívejte se na rychlé starty v [Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop).

* Další informace o dvou objektech Azure AD, které představují zaregistrovanou aplikaci, a vztahu mezi nimi, najdete v článku o [objektech aplikací a instančních objektech](app-objects-and-service-principals.md).

* Další informace o pokynech pro značky, které byste měli použít při vývoji aplikací, najdete v tématu [pokyny k vytváření značek pro aplikace](howto-add-branding-in-azure-ad-apps.md).
