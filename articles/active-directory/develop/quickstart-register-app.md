---
title: 'Úvodní příručka: Registrace aplikací pomocí platformy microsoft identit | Azure'
description: V tomto rychlém startu se dozvíte, jak přidat a zaregistrovat aplikaci s platformou identit microsoftu.
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
ms.openlocfilehash: 1625b48d86eebaf5d8fcd4c100d89b83716ba459
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79408364"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>Úvodní příručka: Registrace aplikace s platformou microsoftu pro identity

V tomto rychlém startu zaregistrujete aplikaci pomocí prostředí **registrace aplikací** na webu Azure Portal. 

Vaše aplikace je integrovaná s platformou identit Microsoftu tím, že ji zaregistrujete u klienta Azure Active Directory. Podnikoví vývojáři a poskytovatelé softwaru jako služby (SaaS) mohou vyvíjet komerční cloudové služby nebo obchodní aplikace, které lze integrovat s platformou identit Microsoftu. Integrace poskytuje zabezpečené přihlášení a autorizaci pro tyto služby.

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Tentant Azure AD](quickstart-create-new-tenant.md).

## <a name="register-a-new-application-using-the-azure-portal"></a>Registrace nové aplikace pomocí portálu Azure Portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. Pokud váš účet umožňuje přístup k více než jednomu tenantovi, vyberte svůj účet v pravém horním rohu. Nastavte relaci portálu na tenanta Azure AD, který chcete.
1. Vyhledejte a vyberte **Azure Active Directory**. V části **Spravovat** vyberte **Registrace aplikací**.
1. Vyberte **možnost Nová registrace**.
1. Do **pole Registrovat aplikaci**zadejte smysluplný název aplikace, který se zobrazí uživatelům.
1. Určete, kdo může aplikaci používat, a to následovně:

    | Podporované typy účtu | Popis |
    |-------------------------|-------------|
    | **Účty jen v tomto organizačním adresáři** | Tuto možnost vyberte, pokud vytváříte obchodní aplikaci. Tato možnost není k dispozici, pokud aplikaci neregistrujete v adresáři.<br><br>Tato možnost se mapuje pouze na účty Azure AD s jedním tenantem.<br><br>Tato možnost je výchozí, pokud neregistrujete aplikaci mimo adresář. V případech, kdy je aplikace zaregistrovaná mimo adresář, jsou výchozí možností účty Azure AD s více tenanty a osobní účty Microsoft. |
    | **Účty v libovolném organizačním adresáři** | Tuto možnost vyberte, pokud chcete cílit na všechny zákazníky z řad firem a vzdělávacích institucí.<br><br>Tato možnost se mapuje pouze na účty Azure AD s více tenanty.<br><br>Pokud jste aplikaci zaregistrovali jako jenom jednoklientskou službu Azure AD, můžete ji aktualizovat tak, aby byla víceklientská azure a a zpět na jednoho tenanta prostřednictvím stránky **Ověřování.** |
    | **Účty v libovolném organizačním adresáři a osobní účty Microsoft** | Tuto možnost vyberte, pokud chcete cílit na co nejširší okruh zákazníků.<br><br>Tato možnost se mapuje na účty Azure AD s více tenanty a osobní účty Microsoft.<br><br>Pokud jste aplikaci zaregistrovali jako víceklientské a osobní účty Microsoft Azure AD, nemůžete toto nastavení v uživatelském rozhraní změnit. Místo toho musíte ke změně podporovaných typů účtu použít editor manifestu aplikace. |

1. V části **Přesměrování identifikátoru URI (volitelné)** vyberte typ vytvářené aplikace: **Web** nebo Veřejný **klient (mobilní & plochy).** Potom zadejte identifikátor URI přesměrování nebo adresu URL odpovědi pro vaši aplikaci.

    * V případě webových aplikací zadejte základní adresu URL vaší aplikace. Například `https://localhost:31544` může být adresa URL pro webovou aplikaci spuštěnou na místním počítači. Uživatelé by se pomocí této adresy URL přihlašovali k webové klientské aplikaci.
    * V případě veřejných klientských aplikací zadejte identifikátor URI, který Azure AD použije k vrácení odpovědí týkajících se tokenu. Zadejte konkrétní hodnotu pro vaši aplikaci, například `myapp://auth`.

    Příklady pro webové aplikace nebo nativní aplikace naleznete v rychlých startech na [platformě identit společnosti Microsoft](https://docs.microsoft.com/azure/active-directory/develop).

1. Až budete hotovi, vyberte **Zaregistrovat**.

    ![Zobrazuje obrazovku pro registraci nové aplikace na webu Azure Portal.](./media/quickstart-add-azure-ad-app-preview/new-app-registration.png)

Azure AD přiřadí jedinečnou aplikaci nebo id klienta vaší aplikaci. Portál otevře stránku **Přehled** vaší aplikace. Chcete-li do aplikace přidat funkce, můžete vybrat další možnosti konfigurace, včetně brandingu, certifikátů a tajných kódů, oprávnění rozhraní API a dalších.

![Příklad nově registrované stránky s přehledem aplikací](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png)

## <a name="next-steps"></a>Další kroky

* Přístup k webovým rozhraním API naleznete v [tématu Úvodní příručka: Konfigurace klientské aplikace pro přístup k webovým rozhraním API](quickstart-configure-app-access-web-apis.md)

* Informace o oprávněních najdete [v tématu Oprávnění a souhlas v koncovém bodě platformy identit microsoftu](v2-permissions-and-consent.md).

* Informace o zveřejnění webových rozhraní API naleznete [v tématu Úvodní příručka: Konfigurace aplikace pro vystavení webových rozhraní API](quickstart-configure-app-expose-web-apis.md).

* Informace o správě podporovaných účtů naleznete [v tématu Úvodní příručka: Úprava účtů podporovaných aplikací](quickstart-modify-supported-accounts.md).

* Pokud chcete vytvořit aplikaci a přidat funkce, přečtěte si rychlé starty na [platformě microsoft identity](https://docs.microsoft.com/azure/active-directory/develop).

* Další informace o dvou objektech Azure AD, které představují zaregistrovanou aplikaci, a vztahu mezi nimi, najdete v článku o [objektech aplikací a instančních objektech](app-objects-and-service-principals.md).

* Další informace o pravidlech pro branding, které byste měli použít při vývoji aplikací, najdete v [tématu Pokyny pro používání softwaru pro aplikace](howto-add-branding-in-azure-ad-apps.md).
