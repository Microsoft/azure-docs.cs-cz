---
title: itsme OpenID Connect s Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Přečtěte si, jak integrovat ověřování Azure AD B2C pomocí itsme OIDC pomocí zásad uživatelského toku client_secret. itsme je aplikace s digitálním ID. Umožňuje bezpečné přihlášení bez čtenářů karty, hesla, dvojúrovňového ověřování a více kódů PIN.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ba7875caa6a1db7638bfeafcfea1efa7b2462152
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87489511"
---
# <a name="configure-itsme-openid-connect-oidc-with-azure-active-directory-b2c"></a>Konfigurace itsme OpenID Connect (OIDC) pomocí Azure Active Directory B2C

Aplikace itsme Digital ID vám umožní bezpečně se přihlásit bez čtecího zařízení, hesla, dvojúrovňového ověřování nebo více kódů PIN. Aplikace itsme poskytuje silné ověřování zákazníků s ověřenou identitou. V tomto článku se dozvíte, jak integrovat ověřování Azure AD B2C pomocí itsme OpenID Connect (OIDC) pomocí zásad toku uživatele tajného kódu klienta.

## <a name="prerequisites"></a>Předpoklady

Abyste mohli začít, budete potřebovat:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* [Tenant Azure AD B2C](tutorial-create-tenant.md) , který je propojený s vaším předplatným Azure.
* ID klienta, označované také jako partnerský kód poskytovaný službou itsme.
* Váš kód služby poskytovaný službou itsme.
* Váš tajný klíč klienta pro účet itsme.

## <a name="scenario-description"></a>Popis scénáře

![Diagram architektury itsme](media/partner-itsme/itsme-architecture-diagram.png)

<!--
Please clarify step 1 in the description below - we don't have steps in this tutorial for "adapting in the Azure AD B2C Custom Policy- User Journeys" - should this be added somewhere?
-->

| Krok | Description |
|------|------|
|1     | Na svém webu nebo v aplikaci zahrňte **Přihlaste se pomocí tlačítka itsme** , a to tak, že se Přizpůsobte v toku Azure AD B2C uživatele. Tok interakce se spustí, když uživatel klikne na toto tlačítko.  |
|2     | Azure AD B2C spustí tok OpenID Connect odesláním žádosti o autorizaci do rozhraní API tajného kódu klienta itsme. K dispozici je také koncový bod známého/OpenID-Configuration obsahující informace o koncových bodech.  |
|3     | Prostředí itsme přesměruje uživatele na stránku itsme identifikovat sebe a umožní uživateli vyplnit své telefonní číslo.  |
|4     | Prostředí itsme obdrží telefonní číslo od uživatele a ověří správnost.  |
|5     | Pokud telefonní číslo patří k aktivnímu itsme uživateli, vytvoří se pro aplikaci itsme akce.  |
|6     | Uživatel otevře aplikaci itsme, zkontroluje požadavek a potvrdí akci.  |
|7     |  Aplikace informuje prostředí itsme o tom, že akce byla potvrzena. |
|8     |  Prostředí itsme vrací autorizační kód OAuth pro Azure AD B2C. |
|9     |  Pomocí autorizačního kódu Azure AD B2C provede požadavek tokenu. |
| 10 | Prostředí itsme zkontroluje požadavek na token, a pokud je stále platné, vrátí přístupový token OAuth a token ID obsahující požadované informace o uživateli. |
| 11 | Nakonec se uživatel přesměruje na adresu URL pro přesměrování jako ověřený uživatel.  |
|   |   |

## <a name="onboard-with-itsme"></a>Zprovoznění s itsme

1. Pokud chcete vytvořit účet s itsme, přejděte na adresu itsme na [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace).

2. Aktivujte si účet itsme odesláním e-mailu na adresu onboarding@itsme.be . Obdržíte kód **partnera** a **kód služby** , který budete potřebovat pro instalaci B2C.

3. Po aktivaci účtu partnera itsme obdržíte e-mail s jediným odkazem na **tajný klíč klienta**.

4. Dokončete konfiguraci podle pokynů na adrese [itsme](https://business.itsme.be/en) .

## <a name="integrate-with-azure-ad-b2c"></a>Integrace s Azure AD B2C

### <a name="set-up-a-new-identity-provider-in-azure-ad-b2c"></a>Nastavení nového zprostředkovatele identity v Azure AD B2C

> [!NOTE]
> Pokud ho ještě nemáte, [Vytvořte klienta Azure AD B2C](tutorial-create-tenant.md) , který je propojený s vaším předplatným Azure.

1. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta. V horní nabídce vyberte filtr **adresář + odběr** a zvolte adresář, který obsahuje vašeho tenanta Azure AD B2C.

2. V části **služby Azure** vyberte možnost **Azure AD B2C** (nebo vyberte **Další služby** a vyhledejte *Azure AD B2C* pomocí vyhledávacího pole **všechny služby** ).

3. Vyberte **Zprostředkovatelé identity** a potom vyberte **Nový poskytovatel OpenID Connect**.

4. Vyplňte formulář pomocí následujících informací:

   |Vlastnost | Hodnota |
   |------------ |------- |
   | Název | itsme |
   | Adresa URL metadat | `https://oidc.<environment>.itsme.services/clientsecret-oidc/csapi/v0.1/.well-known/openid-configuration` <br>kde `<environment>` je buď `e2e` (testovací prostředí) nebo `prd` (produkční)  |
   | ClientID     | **ID klienta**, označované také jako **partnerský kód**  |
   | Tajný klíč klienta | Vaše **client_secret** |
   | Obor  | Služba OpenID: e-mailový profil YOURSERVICECODE [telefon] [adresa]  |
   |Typ odpovědi | kód |
   |Režim odezvy | query |
   |Pomocný parametr domény | *Tohle můžete nechat prázdné.* |
   |UserID | jednotk |
   |Zobrazovaný název | name |
   |Křestní jméno | given_name |
   |příjmení | family_name |
   |E-mail | e-mail|

5. Vyberte **Uložit**.

### <a name="configure-a-user-flow"></a>Konfigurace toku uživatele

1. Ve vašem tenantovi Azure AD B2C v části **zásady** vyberte **toky uživatelů**.

2. Vyberte **Nový tok uživatele**.

3. Vyberte **zaregistrovat, přihlaste** se, vyberte verzi a pak vyberte **vytvořit**.

4. Zadejte **název**.

5. V části **Zprostředkovatelé identit** vyberte **itsme**.

6. Vyberte **Vytvořit**.

7. Vyberte název toku uživatele a otevřete nově vytvořeného toku uživatele.

8. Vyberte **vlastnosti** a upravte následující hodnoty:

   * Změňte **životnost tokenu ID & pro přístup (minuty)** na **5**.
   * **Doba životnosti posuvných oken aktualizačního tokenu** se nedá nastavit na **vypršení platnosti**.

### <a name="register-an-application"></a>Registrace aplikace

1. V tenantovi B2C v části **Spravovat** vyberte **Registrace aplikací**  >  **Nová registrace**.

2. Zadejte **název** aplikace a zadejte **identifikátor URI pro přesměrování**. Pro účely testování zadejte `https://jwt.ms` .

3. Ujistěte se, že je ověřování Multi-Factor Authentication **zakázané**.

4. Vyberte **Zaregistrovat**.

   a. Pro účely testování vyberte možnost **ověřování** a v části **implicitní udělení udělte** zaškrtněte políčka **přístupové tokeny** a **tokeny ID** .  

   b. Vyberte **Uložit**.

## <a name="test-the-user-flow"></a>Testování toku uživatele

1. V tenantovi B2C v části **zásady** vyberte **toky uživatelů**.

2. Vyberte dříve vytvořený tok uživatele.

3. Vyberte **Spustit tok uživatele**.

   a. **Aplikace**: *Vyberte registrovanou aplikaci* .

   b. **Adresa URL odpovědi**: *Vyberte adresu URL pro přesměrování* .

4. Zobrazí se stránka itsme **identifikovat sebe** .  

5. Zadejte číslo mobilního telefonu a vyberte **Odeslat**.

6. Potvrďte akci v aplikaci itsme.

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích:

* [Vlastní zásady v Azure AD B2C](custom-policy-overview.md)

* [Začínáme s vlastními zásadami v Azure AD B2C](custom-policy-get-started.md?tabs=applications)