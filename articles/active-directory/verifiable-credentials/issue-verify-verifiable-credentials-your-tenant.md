---
title: Kurz – vystavení a ověření ověřitelných přihlašovacích údajů pomocí vašeho tenanta (Preview)
description: Změna ukázkového kódu ověřitelných přihlašovacích údajů pro práci s vaším klientem Azure
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.reviewer: ''
ms.openlocfilehash: 95d3ab19565ed04d9bf7d59ba7262d40b4971d34
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106169904"
---
# <a name="tutorial-issue-and-verify-verifiable-credentials-using-your-tenant-preview"></a>Kurz: vystavení a ověření ověřitelných přihlašovacích údajů pomocí vašeho tenanta (Preview)

> [!IMPORTANT]
> Azure Active Directory ověřitelných přihlašovacích údajů je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Teď, když máte vašeho tenanta Azure nastavenou službu ověřitelných přihlašovacích údajů, provedeme kroky potřebné k tomu, aby vaše Azure Active Directory (Azure AD) vydávala ověřitelná pověření pomocí ukázkové aplikace.

V tomto článku jste:

> [!div class="checklist"]
> * Registrace ukázkové aplikace ve službě Azure AD
> * Vytvořit pravidla a zobrazit soubor
> * Nahrávání pravidel a zobrazovaných souborů
> * Nastavte službu vystavitele ověřitelných přihlašovacích údajů, která se má použít Azure Key Vault
> * Aktualizujte vzorový kód s informacemi o vašem tenantovi.

Náš vzorový kód vyžaduje, aby se uživatelé před vydáním Poradce pro ověření přihlašovacích údajů vystavili pro poskytovatele identity, konkrétně Azure AD B2C. Před vyvoláním přihlašovacích údajů nemusíte všechny ověřované vystavitele přihlašovacích údajů ověřit.

Ověřování tokenů ID umožňuje uživatelům prokázat, kdo jsou před příjmem svých přihlašovacích údajů. Když se uživatel úspěšně přihlásí, vrátí zprostředkovatel identity token zabezpečení obsahující deklarace identity uživatele. Služba vystavitele pak tyto tokeny zabezpečení a jejich deklarace transformuje do ověřitelných přihlašovacích údajů. Ověřitelné přihlašovací údaje jsou podepsané vystavitelem.

Podporuje se libovolný poskytovatel identity, který podporuje protokol OpenID Connect. Příklady podporovaných zprostředkovatelů identity zahrnují [Azure Active Directory](../fundamentals/active-directory-whatis.md)a [Azure AD B2C](../../active-directory-b2c/overview.md). V tomto kurzu používáme AAD.

## <a name="prerequisites"></a>Požadavky

V tomto kurzu se předpokládá, že už jste dokončili kroky v [předchozím kurzu](enable-your-tenant-verifiable-credentials.md) a máte přístup k prostředí, které jste použili.

## <a name="register-an-app-to-enable-did-wallets-to-sign-in-users"></a>Zaregistrujte aplikaci, která má povolit, aby se uživatelé přihlásili.

Aby bylo možné vystavit ověřitelné přihlašovací údaje, je nutné aplikaci zaregistrovat, aby ověřovatel nebo jakékoli jiné ověřitelné kapesní přihlašovací údaje mohli přihlašovat uživatele.  

Registrace aplikace s názvem "aplikace kapesního VC" ve službě Azure AD a získání ID klienta.

1. Podle pokynů v části registrace aplikace v [Azure AD](../develop/quickstart-register-app.md) při registraci použijte níže uvedené hodnoty.

   - Název: "aplikace kapesního typu VC"
   - Podporované typy účtů: účty v tomto organizačním adresáři
   - Identifikátor URI pro přesměrování: vcclient://openid/

   ![Registrace aplikace](media/issue-verify-verifable-credentials-your-tenant/register-application.png)

2. Po registraci aplikace zapište ID aplikace (klienta). Tuto hodnotu budete potřebovat později.

   ![ID klienta aplikace](media/issue-verify-verifable-credentials-your-tenant/client-id.png)

3. Vyberte tlačítko **koncové body** a zkopírujte identifikátor URI dokumentu metadat OpenID Connect. Tyto informace budete potřebovat pro další část. 

   ![koncové body vystavitele](media/issue-verify-verifable-credentials-your-tenant/application-endpoints.png)

## <a name="set-up-your-node-app-with-access-to-key-vault"></a>Nastavení aplikace pro uzly s přístupem k Key Vault

K ověření žádosti o vystavování přihlašovacích údajů uživatele web vystavitele používá vaše kryptografické klíče v Azure Key Vault. Pro přístup k Azure Key Vault potřebuje váš web ID klienta a tajný klíč klienta, které se dají použít k ověření Azure Key Vault.

1. Při prohlížení stránky s přehledem aplikace kapesního VC vyberte **certifikáty & tajných** kódů.
    ![certifikáty a tajné klíče](media/issue-verify-verifable-credentials-your-tenant/vc-wallet-app-certs-secrets.png)
1. V části **tajné klíče klienta** vyberte možnost **nový tajný klíč klienta** .
    1. Přidání popisu, jako je například "klientský tajný kód pro uzel VC"
    1. Platnost vyprší: v jednom roce.
  ![Tajný klíč aplikace s vypršením platnosti jednoho roku](media/issue-verify-verifable-credentials-your-tenant/add-client-secret.png)
1. Zkopírujte tajný klíč. Tyto informace potřebujete k aktualizaci ukázkové aplikace uzlu.

>[!WARNING]
> Je možné, že tajně zkopírujete tajný klíč. Tajný klíč je po tomto případě hash jednou. Nekopírujte ID. 

Po vytvoření aplikace a tajného klíče klienta ve službě Azure AD je potřeba udělit aplikaci potřebná oprávnění k provádění operací s vaším Key Vault. Tato změna oprávnění je nutná k tomu, aby web mohl přistupovat k privátním klíčům uloženým tam a používat je.

1. Přejít na Key Vault.
2. Vyberte Trezor klíčů, který pro tyto kurzy používáme.
3. Zvolit **zásady přístupu** na levém navigačním panelu
4. Vyberte **+ Přidat zásady přístupu**.
5. V části **oprávnění ke klíči** vyberte **získat** a **podepsat**.
6. Vyberte **objekt zabezpečení** a pomocí ID aplikace vyhledejte aplikaci, kterou jste zaregistrovali dříve. Vyberte ji.
7. Vyberte **Přidat**.
8. Klikněte na tlačítko **Uložit**.

Další informace o oprávněních Key Vault a řízení přístupu najdete v tématu [Příručka pro RBAC trezoru klíčů](../../key-vault/general/rbac-guide.md) .

![přiřazení oprávnění k trezoru klíčů](media/issue-verify-verifable-credentials-your-tenant/key-vault-permissions.png)
## <a name="make-changes-to-match-your-environment"></a>Provést změny, aby odpovídaly vašemu prostředí

Doposud jsme pracovali s naší ukázkovou aplikací. Aplikace používá [Azure Active Directory B2C](../../active-directory-b2c/overview.md) a teď jsme přepnuli na použití Azure AD, takže musíme udělat změny, které neodpovídají vašemu prostředí, ale také podporovat další deklarace, které se dřív nepoužily.

1. Zkopírujte níže soubor pravidel a uložte ho do **modified-expertRules.js**. 

    > [!NOTE]
    > **"obor": Profil OpenID** je součástí tohoto souboru pravidel a nebyl zahrnutý do souboru pravidel ukázkové aplikace. V další části se dozvíte, jak povolit volitelné deklarace identity ve vašem tenantovi Azure Active Directory. 
    
    ```json
    {
      "attestations": {
        "idTokens": [
          {
            "mapping": {
              "firstName": { "claim": "given_name" },
              "lastName": { "claim": "family_name" }
            },
            "configuration": "https://dIdPlayground.b2clogin.com/dIdPlayground.onmicrosoft.com/B2C_1_sisu/v2.0/.well-known/openid-configuration",
            "client_id": "8d5b446e-22b2-4e01-bb2e-9070f6b20c90",
            "redirect_uri": "vcclient://openid/",
             "scope": "openid profile"
          }
        ]
      },
      "validityInterval": 2592000,
      "vc": {
        "type": ["VerifiedCredentialExpert"]
      }
    }
    ```

2. Otevřete soubor a nahraďte **client_id** a **konfigurační** hodnoty dvěma hodnotami, které jsme zkopírovali v předchozí části.

    ![zvýrazňování dvou hodnot, které je třeba upravit v rámci tohoto kroku](media/issue-verify-verifable-credentials-your-tenant/rules-file.png)

  **Konfigurace** hodnoty je identifikátor URI dokumentu metadat OpenID Connect.

  Vzhledem k tomu, že vzorový kód používá Azure Active Directory B2C a používáme Azure Active Directory, musíme přidat volitelné deklarace identity přes obory, aby se tyto deklarace zahrnuly do ověřovacího pověření, aby je bylo možné zapsat do ověřitelných přihlašovacích údajů. 

3. Zpět v Azure Portal otevřete Azure Active Directory.
4. Vyberte **Registrace aplikací**.
5. Otevřete aplikaci Peněženka VC, kterou jsme vytvořili dříve.
6. Vyberte možnost **Konfigurace tokenu**.
7. Vybrat **+ přidat volitelnou deklaraci identity**

     ![v části Konfigurace tokenu přidejte volitelnou deklaraci identity.](media/issue-verify-verifable-credentials-your-tenant/token-configuration.png)

8. Z **typu tokenu** vyberte **ID** a ze seznamu dostupných deklarací vyberte **given_name** a **family_name**

     ![Přidat volitelné deklarace](media/issue-verify-verifable-credentials-your-tenant/add-optional-claim.png)

9. Stiskněte **Přidat**.
10. Pokud obdržíte upozornění na oprávnění, jak je uvedeno níže, zaškrtněte políčko a vyberte **Přidat**.

     ![Přidání oprávnění pro volitelné deklarace identity](media/issue-verify-verifable-credentials-your-tenant/add-optional-claim-permissions.png)

Když se teď uživatel zobrazí s přihlašovacími údaji, aby si mohl vystavit ověřitelné přihlašovací údaje, aplikace kapesního VC ví, že zahrne konkrétní deklarace identity přes parametr scope, který se má zapsat do ověřitelných přihlašovacích údajů.

## <a name="create-new-vc-with-this-rules-file-and-the-old-display-file"></a>Vytvořit nový VC pomocí tohoto souboru pravidel a starého souboru zobrazení

1. Nahrání nového souboru pravidel do našeho kontejneru
1. Na stránce ověřitelné přihlašovací údaje vytvořte nové přihlašovací údaje s názvem **modifiedCredentialExpert** s použitím starého souboru zobrazení a nového souboru pravidel (**modified-credentialExpert.jszapnuté**).
1. Po dokončení procesu vytváření přihlašovacích údajů na stránce **Přehled** zkopírujte **adresu URL pro přihlašovací údaje problému** a uložte ji, protože ji budeme potřebovat v další části.

## <a name="before-we-continue"></a>Než budeme pokračovat

Aby bylo možné provést potřebné změny kódu, musíme vložit několik hodnot dohromady. Tyto hodnoty použijeme v další části, aby vzorový kód používal vaše vlastní klíče uložené ve vašem trezoru. Zatím by měly být připravené následující hodnoty.

- **Identifikátor URI kontraktu** od přihlašovacích údajů, které jsme právě vytvořili (adresa URL pro vydávání přihlašovacích údajů)
- **ID klienta aplikace** Máme to, když jsme zaregistrovali aplikaci Node.
- **Tajný kód klienta** Dříve jsme tuto verzi vytvořili, když jsme aplikaci udělili přístup k trezoru klíčů.

Předtím, než můžeme udělat změny v naší ukázkové aplikaci, je potřeba, abyste se dostali k několika dalším hodnotám. Pojďme se teď dostat!

### <a name="verifiable-credentials-settings"></a>Nastavení ověřitelných přihlašovacích údajů

1. Přejděte na stránku ověřitelné přihlašovací údaje a vyberte **Nastavení**.  
1. Zkopírujte následující hodnoty:

    - Identifikátor tenanta 
    - Identifikátor vystavitele (vaše)
    - Trezor klíčů (URI)

1. Pod identifikátorem podpisového klíče je identifikátor URI, ale potřebujeme jenom jeho část. Zkopírujte z části, která říká **issuerSigningKeyION** , zvýrazněný červeným obdélníkem na obrázku níže.

   ![identifikátor klíče pro přihlášení](media/issue-verify-verifable-credentials-your-tenant/issuer-signing-key-ion.png)

### <a name="did-document"></a>Dokument DID 

1. Otevřete [Průzkumníka sítě pro DIF Ion](https://identity.foundation/ion/explorer/) .

2. Vložte do vyhledávacího panelu.

4. Z formátované odpovědi Najděte oddíl s názvem **verificationMethod**
5. V části "verificationMethod" zkopírujte ID a označte ho jako kvSigningKeyId.
    
    ```json=
    "verificationMethod": [
          {
            "id": "#sig_25e48331",
    ```

Teď máme všechno, co potřebujeme, abychom provedli změny v našem ukázkovém kódu.

- **Vystavitel:** app.js aktualizovat konstantní přihlašovací údaje pomocí identifikátoru URI nové smlouvy
- **Verifier:** app.js aktualizovat issuerDid pomocí vašeho identifikátoru vystavitele
- **Vystavitel a Ověřovač** aktualizují didconfig.jss následujícími hodnotami:


```json=
{
    "azTenantId": "Your tenant ID",
    "azClientId": "Your client ID",
    "azClientSecret": "Your client secret",
    "kvVaultUri": "your keyvault uri",
    "kvSigningKeyId": "The verificationMethod ID from your DID Document",
    "kvRemoteSigningKeyId" : "The snippet of the issuerSigningKeyION we copied ",
    "did": "Your DID"
}
```

>[!IMPORTANT]
>Toto je ukázková aplikace a obvykle byste neměli aplikaci nikdy přímo předávat tajným klíčům.


Teď máte všechno, co je potřeba k vystavení a ověření vašich vlastních ověřitelných přihlašovacích údajů ze svého tenanta Azure Active Directory s vlastními klíči. 

## <a name="issue-and-verify-the-vc"></a>Vystavení a ověření VC

Použijte stejný postup jako v předchozím kurzu pro vydání ověřitelných přihlašovacích údajů a ověření ve vaší aplikaci. Po úspěšném dokončení procesu ověřování teď můžete pokračovat v učení o ověřitelných přihlašovacích údajích.

1. Otevřete příkazový řádek a otevřete složku Vystavitel.
2. Spusťte aktualizovanou aplikaci Node.

    ```terminal
    node app.js
    ```

3. Pomocí jiného příkazového řádku spusťte ngrok a nastavte adresu URL na 8081.

    ```terminal
    ngrok http 8081
    ```
    
    >[!IMPORTANT]
    > Můžete si také všimnout varování, že tato aplikace nebo web může být riskantní. V tuto chvíli se tato zpráva očekává, protože jsme ještě nepřipojili vaši doménu k vaší doméně. Pokud to chcete nakonfigurovat, postupujte podle pokynů pro [vázání DNS](how-to-dnsbind.md) .

    
4. Otevřete adresu URL protokolu HTTPS vygenerovanou ngrok.

    ![Koncové body předávání NGROK](media/enable-your-tenant-verifiable-credentials/ngrok-url-screen.png)

5. Zvolit **získání přihlašovacích údajů**
6. V okně ověřovatele Naskenujte kód QR.
7. V **této aplikaci nebo webu může být riziková** zpráva upozorňující na možnost **Upřesnit**.

  ![Počáteční upozornění](media/enable-your-tenant-verifiable-credentials/site-warning.png)

8. Na stránce rizikového upozornění vyberte možnost **pokračovat (nezabezpečené)**

  ![Druhé upozornění na vystavitele](media/enable-your-tenant-verifiable-credentials/site-warning-proceed.png)


9. Na obrazovce **Přidat přihlašovací údaje** si všimněte pár věcí: 
    1. V horní části obrazovky uvidíte červenou zprávu **neověřenou** .
    1. Přihlašovací údaje jsou upraveny na základě změn, které jsme provedli v zobrazení souboru.
    1. Možnost **přihlášení k účtu** se odkazuje na přihlašovací stránku Azure AD.
    
   ![obrazovka Přidat přihlašovací údaje s upozorněním](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified.png)

10. Vyberte možnost **Přihlásit se ke svému účtu** a proveďte ověření pomocí uživatele v TENANTOVI Azure AD.
11. Po úspěšném ověření se tlačítko **Přidat** už nešedá. Klikněte na tlačítko **Přidat**.

  ![Přidat obrazovku přihlašovacích údajů po ověření](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified-authenticated.png)

Nyní jsme vydali ověřitelné přihlašovací údaje pomocí našeho tenanta, který vygeneruje náš VC a pořád k ověřování používá náš tenant B2C.

  ![VC vystavila vaše služba Azure AD a ověřená instancí Azure B2C](media/enable-your-tenant-verifiable-credentials/my-vc-b2c.png)


## <a name="test-verifying-the-vc-using-the-sample-app"></a>Test ověření VC pomocí ukázkové aplikace

Teď, když jsme vydali ověřitelné přihlašovací údaje z našeho vlastního tenanta s deklaracemi z Azure AD, si ji ověříte pomocí naší ukázkové aplikace.

1. Zastavení spouštění služby vystavitele ngrok.

    ```cmd
    control-c
    ```

2. Nyní spusťte ngrok s portem ověřovatele 8082.

    ```cmd
    ngrok http 8082
    ```

3. V jiném okně terminálu přejděte do aplikace Verifier a spusťte ji podobně jako při spuštění aplikace vystavitele.

    ```cmd
    cd ..
    cd verifier
    node app.js
    ```

4. Otevřete v prohlížeči adresu URL ngrok a naskenujte kód QR pomocí ověřovacích dat ve vašem mobilním zařízení.
5. Na svém mobilním zařízení vyberte na stránce **Nová žádost oprávnění** možnost **Povolení** .

   >[!IMPORTANT]
    > Vzhledem k tomu, že ukázková aplikace také používá k podepsání žádosti o prezentaci, se zobrazí upozornění, že tato aplikace nebo web může být riskantní. V tuto chvíli se tato zpráva očekává, protože jsme ještě nepřipojili vaši doménu k vaší doméně. Pokud to chcete nakonfigurovat, postupujte podle pokynů pro [vázání DNS](how-to-dnsbind.md) .
    
   ![Nová žádost o oprávnění](media/enable-your-tenant-verifiable-credentials/new-permission-request.png)

8. Neúspěšně jste ověřili vaše přihlašovací údaje a web by měl zobrazovat vaše jméno a příjmení z uživatelského účtu služby Azure AD. 

Nyní jste dokončili kurz a je oficiálně ověřený odborník na přihlašovací údaje. Vaše ukázková aplikace používá vaše služby pro vydávání i ověřování a zároveň zapisuje deklarace do ověřitelných přihlašovacích údajů z Azure AD. 

## <a name="next-steps"></a>Další kroky

- Naučte se vytvářet [vlastní přihlašovací údaje](credential-design.md) .
- [Příklady](issuer-openid.md) komunikace služby vystavitele