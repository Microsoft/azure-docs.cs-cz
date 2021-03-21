---
title: Správa zájemců pomocí koncového bodu HTTPS – komerční Marketplace Microsoftu
description: Naučte se používat Power automat a koncový bod HTTPS ke správě potenciálních zákazníků z Microsoft AppSource a Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 03/30/2020
ms.openlocfilehash: 5bea2cf256e30bd896957bbee0e0ad824057a569
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98247178"
---
# <a name="use-an-https-endpoint-to-manage-commercial-marketplace-leads"></a>Použití koncového bodu HTTPS ke správě potenciálních zákazníků z obchodu na webu Marketplace

Pokud se v partnerském centru nepodporuje Podniková správa vztahů se zákazníky (CRM) pro příjem Microsoft AppSource a Azure Marketplace potenciálních zákazníků, můžete tyto zájemce zpracovat pomocí koncového bodu HTTPS v [Power](https://powerapps.microsoft.com/automate-processes/) automatu. S koncovým bodem HTTPS můžete komerční zájemce na webu Marketplace poslat jako e-mailové oznámení nebo je můžete zapsat do systému CRM, který podporuje Power automat.

Tento článek vysvětluje, jak vytvořit nový tok v Power automatu pro generování adresy URL POST HTTP, kterou budete používat ke konfiguraci zájemců v partnerském centru. Obsahuje také kroky pro otestování toku s využitím [post](https://www.getpostman.com/downloads/).

>[!NOTE]
>Konektor Power automat používaný v těchto pokynech vyžaduje placené předplatné k automatizaci. Před konfigurací tohoto toku se ujistěte, že tento účet máte.

## <a name="create-a-flow-by-using-power-automate"></a>Vytvoření toku pomocí Power automatizuje

1. Otevřete webovou stránku [Power automatizováno](https://flow.microsoft.com/) . Vyberte **Sign in** (Přihlásit se). Pokud ještě nemáte účet, vyberte **zaregistrovat se zdarma** a vytvořte si bezplatný účet Power Automatizujte.

1. Přihlaste se a v nabídce vyberte **Moje toky** .

    ![Přihlášení mých toků](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

1. V části **+ Nový** vyberte **+ rychlá – od prázdná**.

    ![Moje toky + automatizovaná – od prázdná](./media/commercial-marketplace-lead-management-instructions-https/https-myflows-create-fromblank.png)

1. Pojmenujte tok a potom v části **Zvolte, jak se má tento tok aktivovat**, vyberte, **kdy se přijme požadavek HTTP**.

    ![Tlačítko pro přeskočení okna pro automatické sestavování toku](./media/commercial-marketplace-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

1. Kliknutím na krok toku ho rozbalte.

    ![Rozbalí krok Flow.](./media/commercial-marketplace-lead-management-instructions-https/expand-flow-step.png)

1. Ke konfiguraci **schématu JSON těla žádosti** použijte jednu z následujících metod:

    - Zkopírujte schéma JSON do textového pole **schématu JSON textu žádosti** .
    - Vyberte **K vygenerování schématu použijte ukázkovou datovou část**. Do textového pole **Zadejte nebo vložte ukázkovou datovou část JSON** vložte do příkladu JSON. Vyberte **Hotovo** a vytvořte schéma.

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
        "Description": {
          "id": "/properties/Description",
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

    **Příklad JSON**
    
    ```json
    {
      "UserDetails": {
        "FirstName": "Some",
        "LastName": "One",
        "Email": "someone@contoso.com",
        "Phone": "16175555555",
        "Country": "USA",
        "Company": "Contoso",
        "Title": "Esquire"
     },
      "LeadSource": "AzureMarketplace",
      "ActionCode": "INS",
      "OfferTitle": "Test Microsoft",
      "Description": "Test run through Power Automate"
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
1. V části **zvolit akci** vyberte **Akce**.
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


       ![Příklad e-mailového oznámení](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

1. Kliknutím na **Uložit** dokončete tok. Vytvoří se adresa URL POST protokolu HTTP a je dostupná v okně **při přijetí požadavku HTTP** . Zkopírujte tuto adresu URL pomocí ovládacího prvku kopírování, který je umístěn napravo od adresy URL HTTP POST. Použití tohoto ovládacího prvku je důležité, takže nemůžete přijít o žádnou část celé adresy URL. Tuto adresu URL uložte, protože ji budete potřebovat při konfiguraci správy potenciálních zákazníků na portálu pro publikování.

   ![ADRESA URL PRO POST HTTP](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>Testování

Konfiguraci můžete otestovat pomocí [post](https://app.getpostman.com/app/download/win64). Pro Windows je k dispozici online stažení příspěvku. 

1. Spusťte příkaz post a vyberte **Nový**  >  **požadavek** a nastavte si testovací nástroj. 

   ![Požadavek na nastavení testovacího nástroje](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

1. Vyplňte formulář **Uložit žádost** a pak ho uložte do složky, kterou jste vytvořili.

   ![Formulář pro uložení žádosti](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

1. V rozevíracím seznamu vyberte **Odeslat** . 

   ![Test toku](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

1. Vložte adresu URL POST protokolu HTTP z toku, který jste vytvořili v části Power Automate, kde uvádí **adresu URL žádosti**.

   ![Vložit adresu URL pro POST HTTP](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

1. Vraťte se k [Power](https://flow.microsoft.com/)automatu. V řádku nabídek Power Automate můžete vyhledat tok, který jste vytvořili pro odesílání zájemců, a to tak, že kliknete na **Moje toky** . Vyberte tři tečky vedle názvu toku a zobrazte další možnosti a vyberte **Upravit**.


1. V pravém horním rohu vyberte **test** , vyberte možnost **provede akci aktivace** a pak vyberte **test**. V horní části obrazovky uvidíte indikaci, že byl test spuštěn.

   ![Provedem možnost Akce triggeru](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

1. Vraťte se do aplikace pro odesílání a vyberte **Odeslat**.

   ![Tlačítko Odeslat](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

1. Vraťte se ke svému toku a podívejte se na výsledek. Pokud vše funguje podle očekávání, zobrazí se zpráva, která indikuje, že tok byl úspěšný.

   ![Výsledky kontroly](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

1. Měli byste mít také přijatý e-mail. Prohlédněte si e-mailové doručené pošty. 

    >[!NOTE] 
    >Pokud nevidíte e-mail od testu, zkontrolujte nevyžádanou poštu a složky Nevyžádaná pošta. Na následující obrazovce si všimnete pouze popisků polí, které jste přidali při konfiguraci e-mailového oznámení. Pokud se jednalo o skutečného zájemce vygenerovaného vaší nabídkou, zobrazí se vám také aktuální informace z kontaktu zájemce v textu a na řádku předmětu.

   ![Doručený e-mail](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Konfigurace nabídky pro odeslání zájemců do koncového bodu HTTPS

Až budete připraveni ke konfiguraci informací o správě zájemců pro vaši nabídku na portálu pro publikování, postupujte podle těchto kroků.

1. Přihlaste se k [partnerskému centru](https://partner.microsoft.com/dashboard/home).

1. Vyberte svou nabídku a pak navštivte kartu **nastavení nabídky** .

1. V části **zájemci pro zákazníky** vyberte **připojit**.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-https/customer-leads.png" alt-text="Potenciální zákazníci":::

1. V místním okně **Podrobnosti připojení** vyberte pro **cíl zájemce** **koncový bod HTTPS** . Vložte adresu URL POST protokolu HTTP z toku, který jste vytvořili, pomocí předchozích kroků do pole **Adresa URL koncového bodu https** .
    ![Kontaktní e-mail s podrobnostmi připojení](./media/commercial-marketplace-lead-management-instructions-https/https-connection-details.png)

1. V části **kontaktní e-mail** zadejte e-mailové adresy pro lidi ve vaší společnosti, kteří by měli dostávat e-mailová oznámení při přijetí nového zájemce. Více e-mailů můžete zadat tak, že je oddělíte středníkem.

1. Vyberte **OK**.

Chcete-li se ujistit, že jste úspěšně připojeni k cíli zájemce, klikněte na tlačítko **ověřit** . V případě úspěchu budete mít vedoucího testu v cíli realizace.

>[!NOTE] 
>Musíte dokončit konfiguraci zbytku nabídky a publikovat ji předtím, než budete moct získat zájemce pro tuto nabídku.

Po vygenerování zájemců pošle Microsoft do tohoto toku zájemce. Zájemci se budou směrovat do systému CRM nebo e-mailové adresy, kterou jste nakonfigurovali.
