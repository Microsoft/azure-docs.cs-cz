---
title: 'Kurz: konfigurace Azure Active Directory pro vydávání ověřitelných přihlašovacích údajů (Preview)'
description: V tomto kurzu sestavíte prostředí potřebné k nasazení ověřitelných přihlašovacích údajů ve vašem tenantovi.
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: tutorial
ms.subservice: verifiable-credentials
ms.date: 03/31/2021
ms.author: barclayn
ms.reviewer: ''
ms.openlocfilehash: 08aaa49f73ed437e041ffb93dc9ef5be41e316ec
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106171887"
---
# <a name="tutorial-configure-your-azure-active-directory-to-issue-verifiable-credentials-preview"></a>Kurz: konfigurace Azure Active Directory pro vydávání ověřitelných přihlašovacích údajů (Preview)

V tomto kurzu sestavíme na práci provedené jako součást článku [Začínáme](get-started-verifiable-credentials.md) a nastavíme Azure Active Directory (Azure AD) s vlastním [decentralizovaným identifikátorem](https://www.microsoft.com/security/business/identity-access-management/decentralized-identity-blockchain?rtc=1#:~:text=Decentralized%20identity%20is%20a%20trust,protect%20privacy%20and%20secure%20transactions.) (). Decentralizovaný identifikátor používáme k vystavení ověřitelných přihlašovacích údajů pomocí ukázkové aplikace a svého vystavitele. v tomto kurzu ale pořád používáme ukázkového tenanta Azure B2C k ověřování.  V našem dalším kurzu budeme podniknout další kroky, abyste aplikaci nakonfigurovali pro práci s Azure AD.

> [!IMPORTANT]
> Azure Active Directory ověřitelných přihlašovacích údajů je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

V tomto článku:

> [!div class="checklist"]
> * Vytvoříte potřebné služby k připojení Azure AD k ověřitelným přihlašovacím údajům. 
> * Vytváříme vaše
> * Přizpůsobuje pravidla a zobrazované soubory.
> * Konfigurace ověřitelných přihlašovacích údajů ve službě Azure AD.


## <a name="prerequisites"></a>Požadavky

Než budete moct úspěšně dokončit tento kurz, musíte nejdřív:

- Dokončete [kroky Začínáme](get-started-verifiable-credentials.md).
- Mít účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Azure AD s [licencí](https://azure.microsoft.com/pricing/details/active-directory/)P2. Pokud ho nemáte, postupujte podle pokynů [pro vytvoření bezplatného vývojářského účtu](how-to-create-a-free-developer-account.md) .
- Instance [Azure Key Vault](../../key-vault/general/overview.md) , kde máte práva k vytváření klíčů a tajných kódů.

## <a name="azure-active-directory"></a>Azure Active Directory

Aby bylo možné začít, potřebujeme tenanta Azure AD. Pokud je u tenanta povolený ověřitelný přihlašovací údaj, je mu přiřazen decentralizovaný identifikátor (a) a je povolený pro vystavování ověřitelných přihlašovacích údajů u služby vystavitele. Jakékoli ověřitelné přihlašovací údaje, které vydáte, vystavuje váš tenant. Se používá také při ověřování ověřitelných přihlašovacích údajů.
Pokud jste právě vytvořili testovací předplatné Azure, nemusíte mít klienta naplněný uživatelskými účty, ale budete muset mít aspoň jeden účet uživatele testovacího účtu, abyste mohli dokončit pozdější kurzy.

## <a name="create-a-key-vault"></a>Vytvoření trezoru klíčů

Při práci s ověřitelnými přihlašovacími údaji máte úplnou kontrolu a správu kryptografických klíčů, které váš klient používá k digitálnímu podepisování ověřitelných přihlašovacích údajů. Aby bylo možné vystavit a ověřit přihlašovací údaje, je nutné poskytnout Azure AD s přístupem k vaší vlastní instanci Azure Key Vault.

1. V nabídce Azure Portal nebo na **domovské** stránce vyberte **vytvořit prostředek**.
2. Do vyhledávacího pole zadejte **Trezor klíčů**.
3. V seznamu výsledků zvolte **Key Vault**.
4. V části Key Vault zvolte **Vytvořit**.
5. V části **Vytvořit trezor klíčů** zadejte následující informace:
    - **Předplatné:** Zvolte předplatné.
    - V části **Skupina prostředků** vyberte **vytvořit novou** a zadejte název skupiny prostředků, jako je třeba **VC-Resource-Group**. Používáme stejný název skupiny prostředků v několika článcích.
    - **Název:** Je potřeba zadat jedinečný název. Používáme **pro dřevo-VC-KV** , takže tuto hodnotu nahraďte vlastním jedinečným názvem.
    - V rozevírací nabídce **Umístění** zvolte umístění.
    - U ostatních možností ponechte jejich výchozí hodnoty.
6. Po zadání výše uvedených informací vyberte **zásady přístupu** .

    ![Vytvoření stránky trezoru klíčů](media/enable-your-tenant-verifiable-credentials/create-key-vault.png)

7. Na obrazovce zásady přístupu klikněte na **Přidat zásady přístupu** .

    >[!NOTE]
    > Ve výchozím nastavení je účet, který vytváří Trezor klíčů, jediným z nich s přístupem. Služba ověřitelných přihlašovacích údajů potřebuje přístup k trezoru klíčů. Trezor klíčů musí mít zásady přístupu umožňující správcům **vytvářet klíče**, mít možnost **odstraňovat klíče** , pokud se odhlásíte, a **Přihlásit** se, aby se vytvořila vazba domény pro ověřitelné přihlašovací údaje. Pokud při testování používáte stejný účet, nezapomeňte změnit výchozí zásady, aby se přihlásila k účtu, který je **přihlášený** k výchozím oprávněním uděleným tvůrcům trezoru.

8. Pro správce uživatelů se ujistěte, že oddíl oprávnění klíče má povolené **Vytvoření**, **odstranění** a **podepsání** . Ve výchozím nastavení jsou vytváření a odstraňování již povolené a znaménko by mělo být jediné oprávnění ke klíči, které je třeba aktualizovat. 

    ![Key Vault oprávnění](media/enable-your-tenant-verifiable-credentials/keyvault-access.png)

9. Vyberte **Zkontrolovat a vytvořit**.
10. Vyberte **Vytvořit**.
11. Přejít do trezoru a poznamenejte si název trezoru a identifikátor URI

Poznamenejte si hodnoty dvou vlastností uvedených níže:

- **Název trezoru**: v tomto příkladu je název hodnoty **Woodgrove-VC-KV**. Tento název použijete pro další kroky.
- **Identifikátor URI trezoru**: v tomto příkladu je tato hodnota https://woodgrove-vc-kv.vault.azure.net/ . Aplikace, které používají váš trezor prostřednictvím REST API musí používat tento identifikátor URI.

>[!NOTE]
> Každá transakce trezoru klíčů má za následek dodatečné náklady na předplatné Azure. Další podrobnosti najdete na [stránce s cenami Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) .

>[!IMPORTANT]
> V rámci Azure Active Directory ověřitelných přihlašovacích údajů, se klíče a tajné klíče vytvořené ve vašem trezoru po vytvoření neupraví. Odstranění, zakázání nebo aktualizace klíčů a tajných kódů zruší platnost všech vydaných přihlašovacích údajů. Neměňte klíče ani tajné klíče ve verzi Preview.

## <a name="create-a-modified-rules-and-display-file"></a>Vytvoření upravených pravidel a zobrazovaného souboru

V této části používáme pravidla a zobrazení souborů z aplikace ukázkového vystavitele a mírně je upravíte, abyste vytvořili první ověřitelná pověření vašeho tenanta.

1. Zkopírujte jak pravidla, tak zobrazit soubory JSON do dočasné složky a přejmenujte je **MyFirstVC-display.jszapnuté** a **MyFirstVC-rules.jsv** uvedeném pořadí. Oba soubory můžete najít ve **vystaviteli \ issuer_config**

   ![Zobrazit soubory a pravidla jako součást adresáře ukázkových aplikací](media/enable-your-tenant-verifiable-credentials/sample-app-rules-display.png)

   ![zobrazení a soubory pravidel v dočasné složce](media/enable-your-tenant-verifiable-credentials/display-rules-files-temp.png)

2. Otevřete soubor MyFirstVC-rules.jsv editoru kódu. 

    ```json
         {
          "attestations": {
            "idTokens": [
              {
                "mapping": {
                  "firstName": { "claim": "given_name" },
                  "lastName": { "claim": "family_name" }
                },
                "configuration": "https://didplayground.b2clogin.com/didplayground.onmicrosoft.com/B2C_1_sisu/v2.0/.well-known/openid-configuration",
                "client_id": "8d5b446e-22b2-4e01-bb2e-9070f6b20c90",
                "redirect_uri": "vcclient://openid/"
              }
            ]
          },
          "validityInterval": 2592000,
          "vc": {
            "type": ["VerifiedCredentialExpert"]
          }
        }
      
    ```

Teď změňte pole typ na "MyFirstVC". 

  ```json
   "type": ["MyFirstVC"]
  
  ```

Uložte tuto změnu.

 >[!NOTE]
   > V tomto okamžiku tohoto kurzu neměníme **"konfiguraci"** ani **"client_id"** . Pořád používáme tenanta Microsoft B2C, který jsme použili v části [Začínáme](get-started-verifiable-credentials.md). V dalším kurzu budeme používat vaši službu Azure AD.

3. Otevřete soubor MyFirstVC-display.jsv editoru kódu.

   ```json
       {
          "default": {
           "locale": "en-US",
           "card": {
             "title": "Verified Credential Expert",
             "issuedBy": "Microsoft",
             "backgroundColor": "#000000",
             "textColor": "#ffffff",
             "logo": {
               "uri": "https://didcustomerplayground.blob.core.windows.net/public/VerifiedCredentialExpert_icon.png",
               "description": "Verified Credential Expert Logo"
             },
             "description": "Use your verified credential to prove to anyone that you know all about verifiable credentials."
           },
           "consent": {
             "title": "Do you want to get your Verified Credential?",
             "instructions": "Sign in with your account to get your card."
           },
           "claims": {
             "vc.credentialSubject.firstName": {
               "type": "String",
               "label": "First name"
             },
             "vc.credentialSubject.lastName": {
               "type": "String",
               "label": "Last name"
             }
           }
         }
      }
   ```

Umožňuje provést několik úprav, aby tato ověřitelná pověření byla viditelně odlišná od verze ukázkového kódu. 
    
```json
     "card": {
        "title": "My First VC",
        "issuedBy": "Your Issuer Name",
        "backgroundColor": "#ffffff",
        "textColor": "#000000",
```

Uložte tyto změny.
## <a name="create-a-storage-account"></a>Vytvoření účtu úložiště

Před vytvořením prvního ověřitelných přihlašovacích údajů musíme vytvořit kontejner Blob Storage, který může obsahovat soubory konfigurace a pravidel.

1. Pomocí níže uvedených možností vytvořte účet úložiště. Podrobný postup najdete v článku [Vytvoření účtu úložiště](../../storage/common/storage-account-create.md?tabs=azure-portal) .

   - **Předplatné:** Vyberte předplatné, které pro tyto kurzy používáme.
   - **Skupina prostředků:** Vyberte stejnou skupinu prostředků, kterou jsme použili v předchozích kurzech (**VC-Resource-Group**).
   - **Název:**  Jedinečný název.
   - **Umístění: USA –** východ.
   - **Výkon:** Standardní.
   - **Druh účtu:** Úložiště v2.
   - **Replikace:** Místně redundantní.
 
   ![Vytvoření nového účtu úložiště](media/enable-your-tenant-verifiable-credentials/create-storage-account.png)

2. Po vytvoření účtu úložiště musíme vytvořit kontejner. V části **BLOB Storage** vyberte **kontejnery** a vytvořte kontejner pomocí níže uvedených hodnot:

    - **Název:** kontejner VC
    - **Úroveň veřejného přístupu:** Privátní (bez anonymního přístupu)

   ![Vytvoření kontejneru](media/enable-your-tenant-verifiable-credentials/new-container.png)

3. Teď vyberte svůj nový kontejner a nahrajte jak nová pravidla, tak i zobrazované soubory **MyFirstVC-display.jsv** a **MyFirstVC-rules.js** , které jsme vytvořili dříve.

   ![Odeslat soubor pravidel](media/enable-your-tenant-verifiable-credentials/blob-storage-upload-rules-display-files.png)

## <a name="assign-blob-role"></a>Přiřadit roli objektu BLOB

Před vytvořením přihlašovacích údajů musíme nejdřív přihlášenému uživateli přiřadit správné přiřazení role, aby mohli přistupovat k souborům v objektu BLOB úložiště.

1. Přejděte do   >  **kontejneru** úložiště.
2. V nabídce na levé straně vyberte **Access Control (IAM)** .
3. Vyberte **přiřazení rolí**.
4. Vyberte **Přidat**.
5. V části **role** vyberte **čtečka dat objektů BLOB úložiště**.
6. V části **přiřadit přístup** vyberte možnost **uživatel, skupina nebo Princip služby**.
7. V části **Vybrat**: zvolte účet, který používáte k provedení těchto kroků.
8. Kliknutím na **Uložit** dokončete přiřazení role.


   ![Přidat přiřazení role](media/enable-your-tenant-verifiable-credentials/role-assignment.png)

  >[!IMPORTANT]
  >Ve výchozím nastavení získají tvůrci kontejneru přiřazenou roli **vlastníka** . Role **vlastníka** není dostačující. Váš účet potřebuje roli **čtečky dat objektů BLOB úložiště** . Další informace najdete [v části použití Azure Portal k přiřazení role Azure pro přístup k objektům blob a frontě](../../storage/common/storage-auth-aad-rbac-portal.md) .

## <a name="set-up-verifiable-credentials-preview"></a>Nastavení ověřitelných přihlašovacích údajů (Preview)

Nyní musíme provést poslední krok pro nastavení tenanta pro ověřitelné přihlašovací údaje.

1. Z Azure Portal vyhledejte **ověřitelné přihlašovací údaje**.
2. Vyberte **ověřitelné přihlašovací údaje (Preview)**.
3. Zvolit **Začínáme**
4. Musíme nastavit vaši organizaci a poskytnout název organizace, doménu a trezor klíčů. Pojďme se podívat na každou z hodnot.

      - **název organizace**: Tento název slouží jako odkaz na vaši firmu ve službě ověřitelných přihlašovacích údajů. Tato hodnota nepředstavuje zákazníka.
      - **Doména:** Zadaná doména se přidá do koncového bodu služby v dokumentu. [Microsoft Authenticator](../user-help/user-help-auth-app-download-install.md) a další peněženky využívají tyto informace k ověření, že je vaše [doména propojená s vaší doménou](how-to-dnsbind.md). Pokud se vám kapesní aplikace může ověřit, zobrazuje ověřený symbol. Pokud se peněženka nemůže ověřit, informuje uživatele o tom, že pověření vystavila organizace, kterou nebylo možné ověřit. Tato doména je svázána s něčím, že uživatel může o vaší firmě získat informace.
      - **Trezor klíčů:** Zadejte název Key Vault, který jsme vytvořili dříve.
 
   >[!IMPORTANT]
   > Doména nemůže být přesměrování, jinak nebyla a doména nemůže být propojena. Ujistěte se, že používáte https://www.domain.com formát.
    
5. Výběr možnosti **Uložit a vytvořit přihlašovací údaje**

    ![nastavení vaší organizační identity](media/enable-your-tenant-verifiable-credentials/save-create.png)

Blahopřejeme, váš tenant je teď povolený pro ověřitelný přihlašovací údaje.

## <a name="create-your-vc-in-the-portal"></a>Vytvoření VC na portálu

Předchozí krok si ponechá na stránce **vytvořit nové přihlašovací údaje** . 

   ![spuštění ověřitelných přihlašovacích údajů – začátek](media/enable-your-tenant-verifiable-credentials/create-credential-after-enable-did.png)

1. Do pole název přihlašovacích údajů přidejte název **MyFirstVC**. Tento název se používá na portálu k identifikaci ověřitelných přihlašovacích údajů a je zahrnutý jako součást kontraktu ověřitelných přihlašovacích údajů.
2. V části Zobrazit soubor vyberte možnost **Konfigurace zobrazení souboru** .
3. V části **účty úložiště** vyberte **woodgrovevcstorage**.
4. V seznamu dostupných kontejnerů vyberte **VC-Container**.
5. Vyberte **MyFirstVC-display.jsv** souboru, který jsme vytvořili dříve.
6. V části **vytvořit nové přihlašovací údaje** v části **soubor pravidel** vyberte **Konfigurovat soubor pravidel** .
7. V části **účty úložiště** vyberte **woodgrovecstorage**
8. Vyberte **VC-Container**.
9. Vybrat **MyFirstVC-rules.jsna**
10. Na obrazovce **vytvořit nové přihlašovací údaje** vyberte **vytvořit**.

   ![vytvořit nové přihlašovací údaje](media/enable-your-tenant-verifiable-credentials/create-my-first-vc.png)

### <a name="credential-url"></a>Přihlašovací adresa URL

Teď, když máte nové přihlašovací údaje, zkopírujte adresu URL přihlašovacích údajů.

   ![Adresa URL pro přihlašovací údaje problému](media/enable-your-tenant-verifiable-credentials/issue-credential-url.png)

>[!NOTE]
>Přihlašovací adresa URL je kombinací pravidel a zobrazovaných souborů. Jedná se o adresu URL, kterou ověřovatel vyhodnocuje před zobrazením požadavků na vystavení ověřených přihlašovacích údajů uživatelem.  

## <a name="update-the-sample-app"></a>Aktualizace ukázkové aplikace

Teď provedeme úpravy kódu vystavitele ukázkové aplikace a aktualizujeme ho pomocí ověřitelných přihlašovacích adres URL. Díky tomu můžete vystavovat ověřitelné přihlašovací údaje pomocí vašeho vlastního tenanta.

1. Přejít do složky, kam jste umístili soubory ukázkové aplikace.
2. Otevřete složku Vystavitel a pak otevřete app.js v Visual Studio Code.
3. Aktualizujte konstantu přihlašovacích údajů na adresu URL nového přihlašovacího údaje a nastavte credentialType konstantu na MyFirstVC a uložte soubor.

    ![obrázek Visual Studio Code znázorňující zvýrazněné relevantní oblasti](media/enable-your-tenant-verifiable-credentials/sample-app-vscode.png)

4. Otevřete příkazový řádek a otevřete složku Vystavitel.
5. Spusťte aktualizovanou aplikaci Node.

    ```terminal
    node app.js
    ```

6. Pomocí jiného příkazového řádku spusťte ngrok a nastavte adresu URL na 8081.

    ```terminal
    ngrok http 8081
    ```
    
    >[!IMPORTANT]
    > Můžete si také všimnout varování, že tato aplikace nebo web může být riskantní. V tuto chvíli se tato zpráva očekává, protože jsme ještě nepřipojili vaši doménu k vaší doméně. Pokud to chcete nakonfigurovat, postupujte podle pokynů pro [vázání DNS](how-to-dnsbind.md) .

    
7. Otevřete adresu URL protokolu HTTPS vygenerovanou ngrok.

    ![Koncové body předávání NGROK](media/enable-your-tenant-verifiable-credentials/ngrok-url-screen.png)

8. Zvolit **získání přihlašovacích údajů**
9. V okně ověřovatele Naskenujte kód QR.
10. V **této aplikaci nebo webu může být riziková** zpráva upozorňující na možnost **Upřesnit**.

  ![Počáteční upozornění](media/enable-your-tenant-verifiable-credentials/site-warning.png)

11. Na stránce rizikového upozornění vyberte možnost **pokračovat (nezabezpečené)**

  ![Druhé upozornění na vystavitele](media/enable-your-tenant-verifiable-credentials/site-warning-proceed.png)


12. Na obrazovce **Přidat přihlašovací údaje** si všimněte pár věcí: 
    1. V horní části obrazovky uvidíte červenou zprávu **neověřenou** .
    1. Přihlašovací údaje jsou upraveny na základě změn, které jsme provedli v zobrazení souboru.
    1. Možnost **přihlášení k účtu** odkazuje na **didplayground.b2clogin.com**.
    
   ![obrazovka Přidat přihlašovací údaje s upozorněním](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified.png)

13. Vyberte možnost **Přihlásit se ke svému účtu** a proveďte ověření pomocí přihlašovacích údajů, které jste zadali v [úvodním kurzu](get-started-verifiable-credentials.md).
14. Po úspěšném ověření se tlačítko **Přidat** už nešedá. Klikněte na tlačítko **Přidat**.

  ![Přidat obrazovku přihlašovacích údajů po ověření](media/enable-your-tenant-verifiable-credentials/add-credential-not-verified-authenticated.png)

Nyní jsme vydali ověřitelné přihlašovací údaje pomocí našeho tenanta, který vygeneruje náš VC a pořád k ověřování používá náš tenant B2C.

  ![VC vystavila vaše služba Azure AD a ověřená instancí Azure B2C](media/enable-your-tenant-verifiable-credentials/my-vc-b2c.png)


## <a name="test-verifying-the-vc-using-the-sample-app"></a>Test ověření VC pomocí ukázkové aplikace

Teď, když jsme vydali ověřitelné přihlašovací údaje z našeho vlastního tenanta, si ji pomocí naší ukázkové aplikace ověříme.

>[!IMPORTANT]
> Při testování použijte stejný e-mail a heslo, které jste použili [v článku Začínáme](get-started-verifiable-credentials.md) . Zatímco váš tenant vydává VC, vstup pochází z tokenu ID vydaného klientem Microsoft B2C. V tomto kurzu jsme přepnuli vystavení tokenu na vašeho tenanta.

1. Otevřete **Nastavení** v okně ověřitelné přihlašovací údaje v Azure Portal. Zkopírujte decentralizovaný identifikátor ().

   ![zkopírování](media/enable-your-tenant-verifiable-credentials/issuer-identifier.png)

2. Teď otevřete složku "verifierer" součásti ukázkových souborů aplikace. Musíme aktualizovat soubor app.js v ukázkovém kódu ověřovatele a provést následující změny:

    - **přihlašovací údaje**: Změna na adresu URL přihlašovacích údajů
    - **credentialType**: ' MyFirstVC '
    - **issuerDid**: Zkopírujte tuto hodnotu z Azure Portal>ověřitelných přihlašovacích údajů (Preview) >nastavení>decentralizovaný identifikátor ().
    
   ![aktualizuje konstantu issuerDid tak, aby odpovídala vašemu identifikátoru vystavitele.](media/enable-your-tenant-verifiable-credentials/editing-verifier.png)

3. Zastavení spouštění služby vystavitele ngrok.

    ```cmd
    control-c
    ```

4. Nyní spusťte ngrok s portem ověřovatele 8082.

    ```cmd
    ngrok http 8082
    ```

5. V jiném okně terminálu přejděte do aplikace Verifier a spusťte ji podobně jako při spuštění aplikace vystavitele.

    ```cmd
    cd ..
    cd verifier
    node app.js
    ```

6. Otevřete v prohlížeči adresu URL ngrok a naskenujte kód QR pomocí ověřovacích dat ve vašem mobilním zařízení.
7. Na svém mobilním zařízení vyberte na stránce **Nová žádost oprávnění** možnost **Povolení** .

    >[!NOTE]
    > Podpis tohoto VC je stále v Microsoft B2C. Ověřovatel byl stále z klienta ukázkové aplikace od společnosti Microsoft. Vzhledem k tomu, že společnost Microsoft byla propojená s doménou, kterou vlastníme, nevidíte během toku vystavování upozornění, jako bychom znamenali. Tato akce bude aktualizována v následující části.
    
   ![Nová žádost o oprávnění](media/enable-your-tenant-verifiable-credentials/new-permission-request.png)

8. Neúspěšně jste ověřili vaše přihlašovací údaje.

## <a name="next-steps"></a>Další kroky

Teď, když máte vzorový kód, který vystavuje VC z vašeho vystavitele, umožňuje pokračovat v další části, kde můžete použít vlastního zprostředkovatele identity k ověřování uživatelů, kteří se pokoušejí ověřit přihlašovací údaje, a používat k podepisování žádostí o prezentace.

> [!div class="nextstepaction"]
> [Kurz – vystavení a ověření ověřitelných přihlašovacích údajů pomocí vašeho tenanta](issue-verify-verifiable-credentials-your-tenant.md)


