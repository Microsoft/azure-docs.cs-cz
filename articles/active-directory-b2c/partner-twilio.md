---
title: Twilio ověřit aplikaci pomocí Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Naučte se integrovat ukázkovou online platební aplikaci do Azure AD B2C pomocí rozhraní API pro ověření Twilio. Splnění požadavků na transakce PSD2 (platební služby 2) prostřednictvím dynamického propojování a silného ověřování zákazníků.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 953653a758577ed3d48ca2d81403b4cb363ea294
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91259064"
---
# <a name="integrating-twilio-verify-app-with-azure-active-directory-b2c"></a>Integrace aplikace Twilio Verify s Azure Active Directory B2C

V tomto návodu se dozvíte, jak integrovat ukázkovou online platební aplikaci do Azure Active Directory B2C (Azure AD B2C) pomocí rozhraní API pro ověření Twilio. Pomocí Twilio ověření aplikace mohou zákazníci naplnit požadavky Azure AD B2C na transakce PSD2 (platební služby 2) prostřednictvím dynamického propojování a silného ověřování zákazníků.

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, budete potřebovat:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* [Tenant Azure AD B2C](tutorial-create-tenant.md) , který je propojený s vaším předplatným Azure.
* [Zkušební účet](https://www.twilio.com/try-twilio) na adrese Twilio.

## <a name="scenario-description"></a>Popis scénáře

Řešení Twilio tvoří následující komponenty:

- [Ukázková webová aplikace .NET PSD2](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/source-code/PSD2%20Demo%20App), která poskytuje možnost přihlásit se nebo zaregistrovat a provést fiktivní vysoce rizikové transakce.
- Azure AD B2C kombinované [přihlášení a zásady registrace](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/policy).
- Zásada Azure AD B2C integrovaná s Twilio ověřuje rozhraní API pomocí `id_token_hint` .
- Webová aplikace .NET, která hostuje `.well-known` koncový bod OpenIdConnect, aby povolovala ověřování `id_token_hint` .


    ![tok Twilio](media/partner-twilio/twilio-flow.png)

| Krok | Popis |
|------|------|
| 1     | Uživatel zahájí přihlášení nebo se zaregistruje do ukázkové aplikace PSD2. Uživatel je ověřený prostřednictvím kombinovaného přihlášení Azure AD B2C a zásady registrace. Do aplikace se vrátí token. Při registraci se telefonní číslo uživatele ověřuje pomocí serveru SMS nebo telefonu a zaznamenaného na svém Azure AD B2C účtu.     |
| 2     | Uživatel zahájí transakci s vysokým rizikem, jako je například přenos $50,00. Aktuální přístupový token uživatele se vyhodnocuje pro PolicyId, aby se zjistilo, jestli se uživatel už ověřený prostřednictvím vlastní zásady pro krokování.     |
| 3     | Aplikace zaznamenává hodnotu transakce a příjemce, $50,00 a Jan Novák a vygeneruje podepsaný token. Tento token se nazývá `id_token_hint` a obsahuje deklaraci identity `amount:$500, payee:john doe` . Se `id_token_hint` pošle spolu s požadavkem na vlastní zásadu Azure AD B2C, která je integrovaná s Twilio.     |
| 4     | Azure AD B2C ověří podpis id_token_hint kontrolou `/.well-known` koncového bodu aplikace OpenId Connect. Po ověření extrahuje deklarace z tohoto tokenu, zejména `amount` a `payee` . Uživateli se zobrazí stránka pro ověření svého mobilního telefonního čísla prostřednictvím zprávy SMS.     |
| 5     | Uživatel žádá o ověření svého telefonního čísla prostřednictvím zprávy SMS a Azure AD B2C provede požadavek REST API na ověření koncového bodu rozhraní API pro Twilio. Odesílá také transakce `amount` a `payee` jako součást procesu PSD2 k VYgenerování jednorázového hesla. Twilio pošle zprávu SMS zaregistrovanému telefonnímu číslu uživatele.     |
| 6     |  Uživatel zadá do zprávy SMS přijaté heslo a odešle ho do Azure AD B2C. Azure AD B2C vytvoří pomocí tohoto jednorázového hesla požadavek rozhraní API, aby ověřil, jestli je heslo pro ověřování správné. Nakonec se pro aplikaci vydá token s novým PolicyId, který značí, že uživatel pozměnil své ověřování.    |
|      |      |

## <a name="onboard-with-twilio"></a>Zprovoznění s Twilio

1. Získejte [zkušební účet](https://www.twilio.com/try-twilio) na adrese Twilio.

2. Zakupte si telefonní číslo na Twilio, jak je popsáno v [tomto článku](https://support.twilio.com/hc/articles/223135247-How-to-Search-for-and-Buy-a-Twilio-Phone-Number-from-Console) .

3. V konzole Twilio přejděte na [ověřit rozhraní API](https://www.twilio.com/console/verify/services) a podle [pokynů](https://www.twilio.com/docs/verify/verifying-transactions-psd2) vytvořte službu a povolte možnost PSD2.  

## <a name="configure-the-psd2-demo-app"></a>Konfigurace ukázkové aplikace PSD2

1. Otevřete řešení B2C-WebAPI-DotNet a nahraďte následující hodnoty vlastními hodnotami konkrétního tenanta v web.config:

    ```xml
   <add key="ida:Tenant" value="yourtenant.onmicrosoft.com" />
   <add key="ida:TenantId" value="d6f33888-0000-4c1f-9b50-1590f171fc70" />
   <add key="ida:ClientId" value="6bd98cc8-0000-446a-a05e-b5716ef2651b" />
   <add key="ida:ClientSecret" value="secret" />
   <add key="ida:AadInstance" value="https://yourtenant.b2clogin.com/tfp/{0}/{1}" />
   <add key="ida:RedirectUri" value="https://your hosted psd2 demo app url/" />
   ```

2. [Webová aplikace](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/source-code/PSD2%20Demo%20App) taky hostuje generátor pomocného tokenu ID a koncový bod metadat.
   - Vytvořte podpisový certifikát, jak je popsáno v tomto [ukázkovém popisu](https://github.com/azure-ad-b2c/samples/tree/master/policies/invite#creating-a-signing-certificate).
   - Aktualizujte následující řádky na základě vašeho certifikátu v web.config:
   
   ```xml
   <add key="ida:SigningCertThumbprint" value="4F39D6014818082CBB763E5BA5F230E545212E89" />
   <add key="ida:SigningCertAlgorithm" value="RS256" />
   ```

3. Nahrajte ukázkovou aplikaci pro poskytovatele hostingu dle vlastního výběru. Pokyny pro Azure App Service jsou uvedené v [tomto ukázkovém popisu](https://github.com/azure-ad-b2c/samples/tree/master/policies/invite#hosting-the-application-in-azure-app-service), včetně pokynů pro nahrání certifikátu.

4. Aktualizujte svou registraci aplikace Azure AD B2C přidáním adresy URL odpovědi, která odpovídá adrese URL, na které je aplikace hostovaná.

5. Otevřete [soubory zásad](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/policy) a nahraďte všechny výskyty  `contoso` názvem vašeho tenanta.

6. Vyhledejte Twilio REST API Technical Profile **Custom – SMS – zapsat**. Aktualizujte  `ServiceURL`   ho pomocí Twilio AccountSID a číslo od do zakoupeného telefonního čísla.

7. Najděte Twilio REST API Technical Profiles, **TwilioRestAPI-Verify-krok 1**   a **TwilioRestAPI-Verify-STEP2**a aktualizujte  `ServiceURL`   ho pomocí Twilio AccountSID.

## <a name="integrate-with-azure-ad-b2c"></a>Integrace s Azure AD B2C

Přidejte soubory zásad do Azure AD B2C:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.

2. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete filtr **adresář + předplatné** a zvolíte adresář, který obsahuje vašeho tenanta.

3. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.

4. Přejděte na **Azure AD B2C**  >  klíče zásad Azure AD B2C**identity Experience Framework**  >  **Policy Keys**.

5. Přidejte nový klíč s názvem **B2cRestTwilioClientId**. Vyberte možnost **ručně**a zadejte hodnotu Twilio AccountSID.

6. Přidejte nový klíč s názvem **B2cRestTwilioClientSecret**. Vyberte **ručně**a zadejte hodnotu OVĚŘOVACÍho tokenu Twilio.

7. Nahrajte všechny soubory zásad do svého tenanta.

8. Přizpůsobte řetězec v transformaci deklarací GenerateOTPMessageEnrol pro text SMS pro registraci.

## <a name="test-the-solution"></a>Testování řešení

* Přejděte do své aplikace a otestujte přihlašování, registraci a odeslání peněžních akcí.

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích:

- [Ukázky kódu pro integraci s Twilio](https://github.com/azure-ad-b2c/samples/tree/master/policies/twilio-mfa-psd2) najdete v GitHubu.  

- [Vlastní zásady v AAD B2C](custom-policy-overview.md)

- [Začínáme s vlastními zásadami v AAD B2C](custom-policy-get-started.md?tabs=applications)
