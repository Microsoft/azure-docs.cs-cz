---
title: Koncový bod HTTPS | Azure Marketplace
description: Nakonfigurujte správu zájemců pro koncový bod HTTPS.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: dsindona
ms.openlocfilehash: 6a0131cf94759fc529a52ea33d5392a60c5ef30c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281591"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Konfigurace správy potenciálních zákazníků pomocí koncového bodu HTTPS

Pokud váš systém správy vztahů se zákazníky (CRM) není explicitně podporován v Centru partnerů pro příjem zájemců Azure Marketplace a AppSource, můžete k zpracování těchto zájemců použít koncový bod HTTPS v MS Flow. S koncovým bodem HTTPS mohou být tito zájemci odesláni jako e-mailové oznámení nebo mohou být zapsáni do systému řízení vztahů se zákazníky (CRM) podporovaného ms flow. Pokyny v tomto článku vás provedou základním procesem k vytvoření nového toku pomocí microsoft flow, který vygeneruje adresu URL HTTP POST, kterou zadáte do portálu pro publikování pro pole URL adresy URL služby Správa potenciálních zákazníků > **HTTPS.** Také jsou zahrnuty pokyny, jak můžete vyzkoušet svůj tok pomocí nástroje s názvem [Pošťák,](https://www.getpostman.com/downloads/) který lze nalézt na internetu.

## <a name="create-a-flow-using-microsoft-flow"></a>Vytvoření toku pomocí Microsoft Flow

1. Otevřete webovou stránku [Flow.](https://flow.microsoft.com/) Vyberte **Přihlásit**se , nebo pokud ještě nemáte účet, vyberte **Zaregistrovat se zdarma** a vytvořte si bezplatný účet Flow.

2. Přihlaste se a na řádku nabídek vyberte **Moje toky.**

3. Vyberte **+Automatizovat - z prázdné**.

    ![Moje toky + Automatizovaná - z prázdného místa](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

4. V okně *Sestavení automatického toku* vyberte **přeskočit**. 

    ![Vytváření automatizovaného toku – přeskočit](./media/commercial-marketplace-lead-management-instructions-https/build-automated-flow.png)

5. Do pole Hledat konektory a aktivační události zadejte "request" pro **vyhledání** konektoru Požadavku.
6. V části *Triggers*vyberte **Při přijetí požadavku HTTP**. 

    ![konektor požadavku - aktivační události](./media/commercial-marketplace-lead-management-instructions-https/request-connector.png)

7. Při přijetí *požadavku HTTP okno* zkopírujte a vložte schéma JSON níže do textového pole Text požadavku **JSON schéma.** Toto schéma používá společnost Microsoft k obsahují data zájemce.

    ![konektor požadavku - aktivační události](./media/commercial-marketplace-lead-management-instructions-https/https-request-received.png)

    **Schéma JSON**

    ```JSON
    {
      "$schema": "https://json-schema.org/draft-04/schema#",
      "definitions": {},
      "id": "http://example.com/example.json",
      "properties": {
        "ActionCode": {
          "id": "/properties/ActionCode",
          "type": "string"
        },
        "OfferTitle": {
          "id": "/properties/OfferTitle",
          "type": "string"
        },
        "LeadSource": {
          "id": "/properties/LeadSource",
          "type": "string"
        },
        "UserDetails": {
          "id": "/properties/UserDetails",
          "properties": {
            "Company": {
              "id": "/properties/UserDetails/properties/Company",
              "type": "string"
            },
            "Country": {
              "id": "/properties/UserDetails/properties/Country",
              "type": "string"
            },
            "Email": {
              "id": "/properties/UserDetails/properties/Email",
              "type": "string"
            },
            "FirstName": {
              "id": "/properties/UserDetails/properties/FirstName",
              "type": "string"
            },
            "LastName": {
              "id": "/properties/UserDetails/properties/LastName",
              "type": "string"
            },
            "Phone": {
              "id": "/properties/UserDetails/properties/Phone",
              "type": "string"
            },
            "Title": {
              "id": "/properties/UserDetails/properties/Title",
              "type": "string"
            }
          },
          "type": "object"
        }
      },
      "type": "object"
    }
    ```

>[!Note]
>V tomto okamžiku v konfiguraci můžete vybrat buď připojení k systému CRM nebo konfiguraci e-mailové oznámení. Postupujte podle zbývajících pokynů na základě vašeho výběru.

### <a name="to-connect-to-a-crm-system"></a>Připojení k systému CRM

1. Vyberte **+ Nový krok**.
2. Vyberte systém CRM podle vašeho výběru vyhledáním na něm tam, kde je uvedeno *Hledat konektory a akce*, a vyberte jej v části *Akce* s akcí pro vytvoření nového záznamu. Následující snímek obrazovky ukazuje **Dynamics 365 - Vytvoření** nového záznamu jako příklad.

    ![Vytvoření nového záznamu](./media/commercial-marketplace-lead-management-instructions-https/create-new-record.png)

3. Zadejte **název organizace** přidružený k systému CRM. V rozevíracím seznamu Název entity vyberte **Zájemce.** **Entity Name**

    ![Vybrat zájemce](./media/commercial-marketplace-lead-management-instructions-https/select-leads.png)

4. Tok zobrazuje formulář pro poskytování informací o zájemcích. Položky ze vstupní žádosti můžete mapovat tak, že přidáte dynamický obsah. Následující snímek obrazovky zobrazuje **OfferTitle** jako příklad.

    ![Přidat dynamický obsah](./media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png)

5. Namapujte požadovaná pole a pak vyberte **Uložit,** chcete-li tok uložit. Adresa URL HTTP POST je vytvořena a je přístupná v okně *Při přijetí požadavku HTTP.* Zkopírujte tuto adresu URL pomocí ovládacího prvku copy, který je umístěn vpravo od adresy URL HTTP POST - to je důležité, abyste omylem nezmeškali žádnou část celé adresy URL. Uložte tuto adresu URL podle potřeby při konfiguraci správy zájemců na portálu pro publikování.

    ![Při přijetí požadavku HTTP.](./media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png)

### <a name="to-set-up-email-notification"></a>Nastavení e-mailového oznámení

1. Nyní, když jste dokončili schéma JSON, vyberte **+ Nový krok**.
2. V části **Zvolit akci**vyberte **Akce**.
3. V části **Akce**vyberte **Odeslat e-mail (Office 365 Outlook).**

    >[!Note]
    >Pokud chcete použít jiného poskytovatele e-mailu, vyhledejte a místo toho jako akci vyberte *Odeslat e-mailové oznámení (E-mail).*

    ![Přidání akce e-mailu](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

4. V okně **Odeslat e-mail** nakonfigurujte následující povinná pole:

   - **Chcete-li** zadat alespoň jednu platnou e-mailovou adresu, kam budou zájemci odesláni.
   - **Předmět** – tok vám dává možnost přidat dynamický obsah, například **LeadSource** v následujícím snímání obrazovky. Začněte zadáním názvu pole a kliknutím na seznam výběrů dynamický obsah v automaticky otevíraném okně. 

        >[!Note] 
        > Při přidávání názvů polí můžete každý z nich sledovat pomocí klávesy " a potom zadat a vytvořit nový řádek. Jakmile přidáte názvy polí, můžete přidat každý přidružený parametr z dynamického seznamu vyskladnění.

        ![Přidání akce e-mailu pomocí dynamického obsahu](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **Tělo** – ze seznamu výběru dynamického obsahu přidejte požadované informace do textu e-mailu. Například Příjmení, Jméno, E-mail a Společnost. <br> <br> Po dokončení nastavení e-mailového oznámení bude vypadat jako příklad v následujícím zachycení obrazovky.


       ![Přidání akce e-mailu](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

5. Chcete-li dokončit tok, vyberte **uložit.** Adresa URL HTTP POST je vytvořena a je přístupná v okně *Při přijetí požadavku HTTP.* Zkopírujte tuto adresu URL pomocí ovládacího prvku copy, který je umístěn vpravo od adresy URL HTTP POST - to je důležité, abyste omylem nezmeškali žádnou část celé adresy URL. Uložte tuto adresu URL podle potřeby při konfiguraci správy zájemců na portálu pro publikování.

   ![HTTP POST URL ](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>Testování

Můžete otestovat, že vše funguje podle očekávání pomocí následujících kroků pomocí nástroje s názvem [Postman](https://app.getpostman.com/app/download/win64), který lze stáhnout online. To je k dispozici pro Windows. 

1. Spusťte Pošťák a vyberte **Nový** > **požadavek** pro nastavení testovacího nástroje. 

   ![Žádost o nastavení testovacího nástroje](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

2. Vyplňte formulář *Uložit požadavek* a pak **uložte** do složky, kterou jste vytvořili.

   ![Uložení požadavku](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

3. V rozevíracím seznamu vyberte **POST.** 

   ![Otestujte můj tok](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

4. Vložte adresu URL HTTP POST z toku, který jste vytvořili v MS Flow, kde je uvedeno *Enter request URL*.

   ![Vložení adresy URL HTTP POST](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

5. Vraťte se do [flow](https://flow.microsoft.com/) a najděte tok, který jste vytvořili pro odesílání zájemců, a přejděte na **můj toky** z panelu nabídek Tok.  Vyberte 3 tečky vedle názvu toku a vyberte **Upravit**.

   ![Moje toky - Upravit](./media/commercial-marketplace-lead-management-instructions-https/my-flows-edit.png)

6. V pravém horním rohu vyberte **Testovat,** vyberte možnost "Prováděj akci aktivační události" a pak vyberte **Testovat**. V horní části obrazovky se zobrazí údaj, který označuje, že test byl zahájen

   ![Zkušební průtok - aktivační událost](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

7. Vraťte se do aplikace Postman a vyberte **Odeslat** vedle místa, kam jste vložili adresu URL HTTPS.

   ![Otestovat můj tok - Odeslat](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

8. Vraťte se do svého toku a zkontrolujte výsledek. Pokud vše funguje podle očekávání, zobrazí se zpráva oznamující, že byla úspěšná.

   ![Tok – kontrola výsledků](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

9. Měli jste také obdržet e-mail. Zkontrolujte e-mailovou schránku. 

    >[!Note] 
    >Pokud nevidíte e-mail z testu, zkontrolujte složky se spamem a nevyžádanou poštou. Níže si všimnete pouze pole štítky, které jste přidali při konfiguraci e-mailové oznámení. Pokud by se jednalo o skutečného zájemce generovaného z vaší nabídky, viděli byste také skutečné informace z kontaktu zájemce v těle a v řádku Předmět.

   ![Přijatý e-mail](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Konfigurace nabídky pro odesílání zájemců do koncového bodu HTTPS

Až budete připraveni nakonfigurovat informace o správě zájemců pro vaši nabídku na portálu pro publikování, postupujte podle následujících kroků:

1. Přejděte na stránku **Nastavení nabídky** pro vaši nabídku.
2. V části Správa zájemců vyberte **Připojit.**
3. V rozbalovacím okně Podrobnosti o připojení vyberte **koncový bod HTTPS** pro cíl **zájemce** a vložte do adresy URL HTTP POST z toku, který jste vytvořili podle předchozích kroků do pole **URL koncového bodu HTTPS.**
4. Vyberte **Uložit**. 

>[!Note] 
>Před přijetím zájemců o nabídku je nutné dokončit konfiguraci zbývající nabídky a publikovat ji.

Při generování zájemců odesílá společnost Microsoft zájemce na tok, který se směruje na nakonfigurovaný systém CRM nebo e-mailovou adresu.

![Vedení řízení - připojit](./media/commercial-marketplace-lead-management-instructions-https/lead-management-connect.png)

![Podrobnosti připojení](./media/commercial-marketplace-lead-management-instructions-https/connection-details.png)

![Podrobnosti připojení](./media/commercial-marketplace-lead-management-instructions-https/connection-details-1.png)

