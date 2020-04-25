---
title: Microsoft Commercial Marketplace – Správa potenciálních zákazníků pomocí protokolu HTTPS
description: Konfigurace vedoucího komerčního tržiště Microsoftu pro koncový bod HTTPS
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 1c3337e970fdbb22cb1ed88f105d5e7798a68f74
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133733"
---
# <a name="configure-lead-management-by-using-an-https-endpoint"></a>Konfigurace správy potenciálních zákazníků pomocí koncového bodu HTTPS

>[!NOTE]
>Konektor Power automat používaný v těchto pokynech vyžaduje placené předplatné k automatizaci. Než budete postupovat podle pokynů v tomto článku, ujistěte se, že tento účet máte.

Pokud se v partnerském centru nepodporuje Podniková správa vztahů se zákazníky (CRM) pro příjem Microsoft AppSource a Azure Marketplace potenciálních zákazníků, můžete tyto zájemce zpracovat pomocí koncového bodu HTTPS v Power automatu. Pomocí koncového bodu HTTPS je možné tyto zájemce odeslat jako e-mailové oznámení nebo je můžete zapsat do systému CRM, který podporuje Power automat. Pokyny v tomto článku vás seznámí se základním procesem vytvoření nového toku pomocí Power Automate, který vygeneruje adresu URL post protokolu HTTP, kterou zadáte na portál pro publikování pro pole > **Adresa URL koncového bodu** **správy potenciálních zákazníků**. K dispozici jsou také pokyny, jak testovat tok pomocí nástroje označovaného jako " [Poster](https://www.getpostman.com/downloads/)", který je k dispozici online.

## <a name="create-a-flow-by-using-power-automate"></a>Vytvoření toku pomocí Power automatizuje

1. Otevřete webovou stránku [Power automatizováno](https://flow.microsoft.com/) . Vyberte **Sign in** (Přihlásit se). Pokud ještě nemáte účet, vyberte **zaregistrovat se zdarma** a vytvořte si bezplatný účet Power Automatizujte.

1. Přihlaste se a v nabídce vyberte **Moje toky** .

1. Vyberte **+ automatizovaná – z prázdné**.

    ![Moje toky + automatizovaná – od prázdná](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

1. V okně **sestavit automatizovaný tok** vyberte **Přeskočit**. 

    ![Tlačítko pro přeskočení okna pro automatické sestavování toku](./media/commercial-marketplace-lead-management-instructions-https/build-automated-flow.png)

1. Do pole **vyhledávací konektory a triggery** zadejte **požadavek** na vyhledání konektoru požadavků.
1. V části **triggery**vyberte, **kdy se přijme požadavek HTTP**. 

    ![Nabídka triggery](./media/commercial-marketplace-lead-management-instructions-https/request-connector.png)

1. V okně **když se přijme požadavek HTTP** , zkopírujte a vložte následující schéma JSON do textového pole **schématu JSON textu žádosti** . Toto schéma používá společnost Microsoft k tomu, aby obsahovalo vaše údaje o vašich potenciálních datech.

    ![Textové pole pro schéma JSON textu žádosti](./media/commercial-marketplace-lead-management-instructions-https/https-request-received.png)

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

>[!NOTE]
>V této chvíli můžete v konfiguraci vybrat možnost připojení k systému CRM nebo nakonfigurovat e-mailové oznámení. Postupujte podle dalších pokynů na základě vašeho výběru.

### <a name="connect-to-a-crm-system"></a>Připojení k systému CRM

1. Vyberte **+ Nový krok**.
1. Zvolte systém CRM podle vašeho výběru, a to tak, že ho vyhledáte, kde říkají **spojnice a akce hledání**. Na kartě **Akce** vyberte tuto akci pro vytvoření nového záznamu. Následující obrazovka ukazuje jako příklad **Vytvoření nového záznamu (Dynamics 365)** .

    ![Vytvoření nového záznamu](./media/commercial-marketplace-lead-management-instructions-https/create-new-record.png)

1. Zadejte **název organizace** , který je přidružený k systému CRM. V rozevíracím seznamu **název entity** vyberte **zájemci** .

    ![Vybrat potenciální zákazníky](./media/commercial-marketplace-lead-management-instructions-https/select-leads.png)

1. Power automatizuje zobrazuje formulář pro poskytování informací o potenciálních vedoucích. Položky ze vstupní žádosti můžete mapovat tak, že vyberete možnost Přidat dynamický obsah. Na následující obrazovce vidíte **OfferTitle** jako příklad.

    ![Přidat dynamický obsah](./media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png)

1. Namapujte pole, která chcete, a pak vyberte **Uložit** . tím se váš tok uloží. Vytvoří se adresa URL POST protokolu HTTP a je dostupná v okně **při přijetí požadavku HTTP** . Zkopírujte tuto adresu URL pomocí ovládacího prvku kopírování, který je umístěn napravo od adresy URL HTTP POST. Použití ovládacího prvku kopírování je důležité, takže nemůžete přijít o žádnou část celé adresy URL. Tuto adresu URL uložte, protože ji budete potřebovat při konfiguraci správy potenciálních zákazníků na portálu pro publikování.

    ![Při přijetí požadavku HTTP](./media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png)

### <a name="set-up-email-notification"></a>Nastavení e-mailového oznámení

1. Teď, když jste dokončili schéma JSON, vyberte **+ Nový krok**.
1. V části **zvolit akci**vyberte **Akce**.
1. Na kartě **Akce** vyberte **Odeslat e-mail (Office 365 Outlook)**.

    >[!NOTE]
    >Pokud chcete použít jiného poskytovatele e-mailu, vyhledejte a vyberte **Odeslat e-mailové oznámení (e-mail)** jako akci.

    ![Přidat akci e-mailu](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

1. V okně **Odeslat e-mail** nakonfigurujte následující povinná pole:

   - **Do**: Zadejte alespoň jednu platnou e-mailovou adresu, kam budou odesílány zájemci.
   - **Předmět**: Power Automate vám dává možnost Přidat dynamický obsah, jako je například **LeadSource** , na následující obrazovce. Začněte zadáním názvu pole. Pak v automaticky otevíraném okně vyberte seznam pro výběr dynamického obsahu. 

        >[!NOTE] 
        > Při přidávání názvů polí můžete každý název sledovat dvojtečkou (:) a pak vyberte **ENTER** a vytvořte nový řádek. Po přidání názvů polí můžete přidat každý přidružený parametr ze seznamu dynamického výběru.

        ![Přidání e-mailové akce pomocí dynamického obsahu](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **Tělo**: ze seznamu pro výběr dynamického obsahu přidejte do textu e-mailu požadované informace. Použijte například LastName, FirstName, E-mail a společnost. Až budete s nastavením e-mailového oznámení hotovi, vypadá to jako na příkladu na následující obrazovce.


       ![Příklad e-mailových oznámení](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

1. Kliknutím na **Uložit** dokončete tok. Vytvoří se adresa URL POST protokolu HTTP a je dostupná v okně **při přijetí požadavku HTTP** . Zkopírujte tuto adresu URL pomocí ovládacího prvku kopírování, který je umístěn napravo od adresy URL HTTP POST. Použití tohoto ovládacího prvku je důležité, takže nemůžete přijít o žádnou část celé adresy URL. Tuto adresu URL uložte, protože ji budete potřebovat při konfiguraci správy potenciálních zákazníků na portálu pro publikování.

   ![ADRESA URL PRO POST HTTP](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>Testování

Můžete otestovat, že vše funguje podle očekávání, pomocí nástroje označovaného jako " [Poster](https://app.getpostman.com/app/download/win64)", který lze stáhnout online. Tento nástroj je k dispozici pro Windows. 

1. Spusťte příkaz post a vyberte **Nový** > **požadavek** a nastavte si testovací nástroj. 

   ![Požadavek na nastavení testovacího nástroje](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

1. Vyplňte formulář **Uložit žádost** a pak ho uložte do složky, kterou jste vytvořili.

   ![Formulář pro uložení žádosti](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

1. V rozevíracím seznamu vyberte **Odeslat** . 

   ![Test toku](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

1. Vložte adresu URL POST protokolu HTTP z toku, který jste vytvořili v části Power Automate, kde uvádí **adresu URL žádosti**.

   ![Vložit adresu URL pro POST HTTP](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

1. Vraťte se k [Power](https://flow.microsoft.com/)automatu. V řádku nabídek Power Automate můžete vyhledat tok, který jste vytvořili pro odesílání zájemců, a to tak, že kliknete na **Moje toky** . Vyberte tři tečky vedle názvu toku a zobrazte další možnosti a vyberte **Upravit**.


1. V pravém horním rohu vyberte **test** , vyberte možnost **provede akci aktivace**a pak vyberte **test**. V horní části obrazovky uvidíte indikaci, že byl test spuštěn.

   ![Provedem možnost Akce triggeru](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

1. Vraťte se do aplikace pro odesílání a vyberte **Odeslat**.

   ![Tlačítko Odeslat](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

1. Vraťte se ke svému toku a podívejte se na výsledek. Pokud vše funguje podle očekávání, zobrazí se zpráva, která indikuje, že tok byl úspěšný.

   ![Výsledky kontroly](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

1. Měli byste mít také přijatý e-mail. Prohlédněte si e-mailové doručené pošty. 

    >[!NOTE] 
    >Pokud nevidíte e-mail od testu, zkontrolujte nevyžádanou poštu a složky Nevyžádaná pošta. Na následující obrazovce si všimnete pouze popisků polí, které jste přidali při konfiguraci e-mailového oznámení. Pokud se jednalo o skutečného zájemce vygenerovaného vaší nabídkou, zobrazí se vám také aktuální informace z kontaktu zájemce v textu a na řádku předmětu.

   ![E-mail přijat](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Konfigurace nabídky pro odeslání zájemců do koncového bodu HTTPS

Až budete připraveni ke konfiguraci informací o správě zájemců pro vaši nabídku na portálu pro publikování, postupujte podle těchto kroků.

1. Pro vaši nabídku přejdete na stránku **nastavení nabídek** .
1. V části **Správa zájemců** vyberte **připojit** .
1. V místním okně **Podrobnosti připojení** vyberte pro **cíl zájemce** **koncový bod HTTPS** . Vložte adresu URL POST protokolu HTTP z toku, který jste vytvořili, pomocí předchozích kroků do pole **Adresa URL koncového bodu https** .
1. V části **kontaktní e-mail**zadejte e-mailové adresy pro lidi ve vaší společnosti, kteří by měli dostávat e-mailová oznámení při přijetí nového zájemce. Více e-mailů můžete zadat tak, že je oddělíte středníkem.
1. Vyberte **OK**.

Chcete-li se ujistit, že jste úspěšně připojeni k cíli zájemce, klikněte na tlačítko **ověřit** . V případě úspěchu budete mít vedoucího testu v cíli realizace.

>[!NOTE] 
>Musíte dokončit konfiguraci zbytku nabídky a publikovat ji předtím, než budete moct získat zájemce pro tuto nabídku.

Po vygenerování zájemců pošle Microsoft do tohoto toku zájemce. Zájemci se budou směrovat do systému CRM nebo e-mailové adresy, kterou jste nakonfigurovali.

![Tlačítko pro řízení připojení zájemce](./media/commercial-marketplace-lead-management-instructions-https/lead-management-connect.png)

![Cíl zájemce podrobnosti o připojení](./media/commercial-marketplace-lead-management-instructions-https/connection-details.png)

![Kontaktní e-mail s podrobnostmi připojení](./media/commercial-marketplace-lead-management-instructions-https/https-connection-details.png)

