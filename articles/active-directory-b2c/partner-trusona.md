---
title: Trusona a Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Naučte se, jak přidat Trusona jako zprostředkovatele identity na Azure AD B2C, aby se povolilo ověřování s heslem.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 99368da8b4018e93ad537e4722ffefd476e61291
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "99573683"
---
# <a name="integrating-trusona-with-azure-active-directory-b2c"></a>Integrace Trusona s Azure Active Directory B2C

Trusona je poskytovatel nezávislého výrobce softwaru (ISV), který pomáhá zabezpečit přihlášení tím, že umožňuje ověřování bez hesla, vícefaktorové ověřování a kontrolu digitální licence. V tomto článku se dozvíte, jak přidat Trusona jako poskytovatele identity v Azure AD B2C pro povolení ověřování bez hesla.

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, budete potřebovat:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* [Tenant Azure AD B2C](tutorial-create-tenant.md) , který je propojený s vaším předplatným Azure.
* [Zkušební účet](https://www.trusona.com/) na adrese Trusona

## <a name="scenario-description"></a>Popis scénáře

V tomto scénáři Trusona funguje jako zprostředkovatel identity pro Azure AD B2C pro povolení ověřování bez hesla. Řešení tvoří následující součásti:

* Azure AD B2C kombinované přihlášení a zásady registrace
* Trusona se přidal do Azure AD B2C jako zprostředkovatel identity.
* Aplikace Trusona ke stažení

![Diagram architektury Trusona](media/partner-trusona/trusona-architecture-diagram.png)

| Krok | Description |
|------|------|
|1     | Uživatel se pokusí přihlásit k aplikaci nebo se k ní zaregistrovat. Uživatel je ověřený prostřednictvím Azure AD B2C zásady registrace a přihlašování. Při registraci se používá dříve ověřená e-mailová adresa uživatele z aplikace Trusona.     |
|2     | Azure B2C přesměruje uživatele na zprostředkovatele identity Trusona OpenID Connect (OIDC) pomocí implicitního toku.     |
|3     | U přihlašovacích údajů založených na stolních počítačích Trusona zobrazí jedinečný, bezstavový, animovaný a dynamický kód QR pro kontrolu pomocí aplikace Trusona. Pro mobilní přihlášení používá Trusona k otevření aplikace Trusona "přímý odkaz". Tyto dvě metody se používají pro zařízení a nakonec pro zjišťování uživatelů.     |
|4     | Uživatel vyhledá zobrazený kód QR pomocí aplikace Trusona.     |
|5     | Účet uživatele najdete v cloudové službě Trusona a připravuje se ověřování.     |
|6     | Cloudová služba Trusona vydá uživateli výzvu k ověření prostřednictvím nabízeného oznámení odeslaného do aplikace Trusona:<br>a. Uživateli se zobrazí výzva s výzvou k ověření. <br> b. Uživatel se rozhodne přijmout nebo odmítnout výzvu. <br> c. Uživatel se vyzve k potvrzení a podepsání výzvy pomocí zabezpečení operačního systému (například biometrika, heslo, PIN nebo vzoru) a podepsání a podepsání výzvy soukromým klíčem v prostředí zabezpečeného enklávy/důvěryhodného spuštění. <br> d. Aplikace Trusona generuje dynamickou datovou část anti-Play na základě parametrů ověřování v reálném čase. <br> e. Celá odpověď je podepsána (za sekundu) pomocí privátního klíče v prostředí zabezpečeného enklávy/důvěryhodného spuštění a vrátila se do cloudové služby Trusona pro ověření.      |
|7     |  Cloudová služba Trusona přesměruje uživatele zpět na inicializační aplikaci pomocí id_token. Azure AD B2C ověří id_token pomocí konfigurace publikovaného OpenIDu Trusona, jak je nakonfigurované během instalace zprostředkovatele identity.    |
|  |  |

## <a name="onboard-with-trusona"></a>Zprovoznění s Trusona

1. Vyplňte [formulář](https://www.trusona.com/) pro vytvoření účtu Trusona a začněte.

2. Stáhněte si mobilní aplikaci Trusona z App Storu. Nainstalujte aplikaci a zaregistrujte svůj e-mail.

3. Ověřte e-maily prostřednictvím zabezpečeného "Magic" linku odesílaného softwarem.  

4. Přejít na [řídicí panel vývojáře Trusona](https://dashboard.trusona.com) pro samoobslužné služby.

5. Vyberte možnost **jsem připraveno** a ověříte si aplikaci Trusona.

6. V levém navigačním panelu vyberte **integrace OIDC**.

7. Vyberte **vytvořit integraci OpenID Connect**.

8. Zadejte **název** podle svého výběru a v **poli Hostitel přesměrování klienta** použijte dříve zadané informace o doméně (například contoso).  

   > [!NOTE]
   > Jako hostitel přesměrování klienta se použije počáteční název domény Azure Active Directory.

9. Postupujte podle pokynů v [příručce pro integraci Trusona](https://docs.trusona.com/integrations/aad-b2c-integration/). Po zobrazení výzvy použijte počáteční název domény (například contoso), na který se odkazuje v předchozím kroku.  

## <a name="integrate-with-azure-ad-b2c"></a>Integrace s Azure AD B2C

### <a name="add-a-new-identity-provider"></a>Přidat nového zprostředkovatele identity

> [!NOTE]
> Pokud ho ještě nemáte, [Vytvořte klienta Azure AD B2C](tutorial-create-tenant.md) , který je propojený s vaším předplatným Azure.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.

2. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete filtr **adresář + předplatné** a zvolíte adresář, který obsahuje vašeho tenanta.

3. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.

4. Přejděte na **řídicí panel**  >  **Azure Active Directory B2C**  >  **poskytovatelé identit**.

3. Vyberte **Zprostředkovatelé identity**.

4. Vyberte **Přidat**.

### <a name="configure-an-identity-provider"></a>Konfigurace zprostředkovatele identity  

1. Vyberte **typ zprostředkovatele identity**  >  **OpenID připojit (Preview)**.

2. Vyplňte formulář a nastavte zprostředkovatele identity:  

   | Vlastnost | Hodnota  |
   | :--- | :--- |
   | Adresa URL metadat | `https://gateway.trusona.net/oidc/.well-known/openid-configuration`|
   | ID klienta | Pošle vám e-mail z Trusona |
   | Obor | E-mail s profilem OpenID |
   | Typ odpovědi | Id_token |
   | Režim odezvy  | Form_post |

3. Vyberte **OK**.  

4. Vyberte **mapovat deklarace identity zprostředkovatele identity**.  

5. Vyplňte formulář pro mapování zprostředkovatele identity:

   | Vlastnost | Hodnota  |
   | :--- | :--- |
   | UserID | Sub  |
   | Zobrazované jméno | zdívek |
   | Křestní jméno | given_name |
   | příjmení | Family_name |
   | Režim odezvy | e-mail |

6. Výběrem **OK** dokončete instalaci pro nového poskytovatele identity OIDC.

### <a name="create-a-user-flow-policy"></a>Vytvoření zásady toku uživatele

Teď byste měli vidět Trusona jako **nového poskytovatele identity OpenID Connect** uvedeného v rámci vašich poskytovatelů identity B2C.

1. Ve vašem tenantovi Azure AD B2C v části **zásady** vyberte **toky uživatelů**.

1. Vyberte **Nový tok uživatele**.

1. Vyberte **zaregistrovat, přihlaste** se, vyberte verzi a pak vyberte **vytvořit**.

1. Zadejte **název** zásady.

1. V části **Zprostředkovatelé identit** vyberte svého nově vytvořeného **poskytovatele identity Trusona**.

   > [!NOTE]
   > Vzhledem k tomu, že Trusona je v podstatě Multi-Factor, je nejlepší službu Multi-Factor Authentication ponechat zakázanou.

1. Vyberte **Vytvořit**.

1. V části **atributy a deklarace identity uživatele** vyberte **Zobrazit další**. Ve formuláři vyberte alespoň jeden atribut, který jste zadali při nastavení poskytovatele identity v předchozí části.

1. Vyberte **OK**.  

### <a name="test-the-policy"></a>Testování zásad

1. Vyberte nově vytvořenou zásadu.

2. Vyberte **Spustit tok uživatele**.

3. Do formuláře zadejte adresu URL pro odpovědi.

4. Vyberte **Spustit tok uživatele**. Měli byste se přesměrovat na bránu OIDC Trusona. V bráně Trusona zkontrolujte zobrazený zabezpečený kód QR pomocí aplikace Trusona nebo vlastní aplikace s využitím sady Trusona Mobile SDK.

5. Po kontrole zabezpečeného kódu QR byste měli být přesměrováni na adresu URL odpovědi, kterou jste definovali v kroku 3.

## <a name="next-steps"></a>Další kroky  

Další informace najdete v následujících článcích:

- [Vlastní zásady v Azure AD B2C](custom-policy-overview.md)

- [Začínáme s vlastními zásadami v AAD B2C](custom-policy-get-started.md?tabs=applications)
