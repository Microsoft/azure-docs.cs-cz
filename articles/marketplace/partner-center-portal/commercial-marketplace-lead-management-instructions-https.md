---
title: Koncový bod HTTPS | Azure Marketplace
description: Konfigurace správy zájemců pro koncový bod HTTPS
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: evansma
ms.openlocfilehash: 83efb9cfd1ee7464a334ebc4064dbfaa20ab30de
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73812294"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Konfigurace správy potenciálních zákazníků pomocí koncového bodu HTTPS

Pokud se v partnerském centru pro příjem Azure Marketplace a AppSourcech potenciálních zákazníků nepodporovaný systém pro správu vztahů se zákazníky (CRM), můžete tyto zájemce zpracovat pomocí koncového bodu HTTPS v MS Flow. S koncovým bodem HTTPS je možné tyto zájemce odeslat jako e-mailové oznámení nebo je můžete zapsat do systému správy vztahů se zákazníky (CRM), který podporuje MS Flow. Pokyny v tomto článku vás seznámí se základním procesem vytvoření nového toku pomocí Microsoft Flow, který vygeneruje adresu URL POST HTTP, kterou zadáte do portálu pro publikování pro pole **Adresa URL koncového bodu HTTPS > https** . Obsahuje také pokyny, jak otestovat tok pomocí nástroje s názvem " [Poster](https://www.getpostman.com/downloads/) ", který lze najít online.

## <a name="create-a-flow-using-microsoft-flow"></a>Vytvoření toku pomocí Microsoft Flow

1. Otevřete webovou stránku [Flow](https://flow.microsoft.com/) . Vyberte **Přihlásit**se nebo pokud ještě nemáte účet, vyberte **zaregistrovat** se a vytvořte bezplatný účet toku.

2. Přihlaste se a v řádku nabídek vyberte **Moje toky** .

3. Vyberte **+ automatizovaná – z prázdné**.

    ![Moje toky + automatizovaná – od prázdná](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

4. V okně *sestavit automatizovaný tok* vyberte **Přeskočit**. 

    ![Sestavit automatizovaný tok – přeskočit](./media/commercial-marketplace-lead-management-instructions-https/build-automated-flow.png)

5. V poli **vyhledávací konektory a triggery** zadejte "Request" a najděte konektor požadavků.
6. V části *triggery*vyberte, **kdy se přijme požadavek HTTP**. 

    ![konektor požadavku – triggery](./media/commercial-marketplace-lead-management-instructions-https/request-connector.png)

7. V okně *při přijetí požadavku HTTP* zkopírujte a vložte níže schéma JSON do textového pole **schématu JSON pro tělo požadavku** . Toto schéma používá společnost Microsoft k tomu, aby obsahovalo vaše údaje o vašich potenciálních datech.

    ![konektor požadavku – triggery](./media/commercial-marketplace-lead-management-instructions-https/https-request-received.png)

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
>V tomto okamžiku v konfiguraci můžete vybrat buď připojení k systému CRM, nebo konfiguraci e-mailového oznámení. Postupujte podle dalších pokynů na základě vašeho výběru.

### <a name="to-connect-to-a-crm-system"></a>Připojení k systému CRM

1. Vyberte **+ Nový krok**.
2. Zvolený systém CRM zvolte tak, že ho vyhledáte tak, že uvedete *vyhledávací konektory a akce*a vyberete ho v části *Akce* s akcí vytvořit nový záznam. Následující snímek obrazovky ukazuje **Dynamics 365 – vytvoří** nový záznam jako příklad.

    ![Vytvořit nový záznam](./media/commercial-marketplace-lead-management-instructions-https/create-new-record.png)

3. Zadejte **název organizace** , který je přidružený k systému CRM. V rozevíracím seznamu **název entity** vyberte **zájemci** .

    ![Vybrat potenciální zákazníky](./media/commercial-marketplace-lead-management-instructions-https/select-leads.png)

4. Flow zobrazuje formulář pro poskytování informací o potenciálních vedoucích. Položky ze vstupní žádosti můžete mapovat tak, že vyberete možnost Přidat dynamický obsah. Následující snímek obrazovky ukazuje **OfferTitle** jako příklad.

    ![Přidat dynamický obsah](./media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png)

5. Namapujte požadovaná pole a potom vyberte **Uložit** . tím tok uložíte. Vytvoří se adresa URL POST protokolu HTTP a je dostupná v okně *při přijetí požadavku HTTP* . Zkopírujte tuto adresu URL pomocí ovládacího prvku kopírování, který se nachází napravo od adresy URL POST protokolu HTTP – to je důležité, aby nedošlo k omylům při nevracení žádné části celé adresy URL. Tuto adresu URL uložte, protože ji budete potřebovat, když konfigurujete správu potenciálních zákazníků na portálu pro publikování.

    ![Při přijetí požadavku HTTP.](./media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png)

### <a name="to-set-up-email-notification"></a>Nastavení e-mailového oznámení

1. Teď, když jste dokončili schéma JSON, vyberte **+ Nový krok**.
2. V části **zvolit akci**vyberte **Akce**.
3. V části **Akce**vyberte **Odeslat e-mail (Office 365 Outlook)** .

    >[!Note]
    >Pokud chcete použít jiné hledání poskytovatele e-mailu a místo toho vybrat *Odeslat e-mailové oznámení (e-mail)* .

    ![Přidat akci e-mailu](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

4. V okně **Odeslat e-mail** nakonfigurujte následující povinná pole:

   - Zadejte alespoň jednu platnou **e-** mailovou adresu, kam budou odesílány zájemci.
   - Tok **předmětu** vám dává možnost Přidat dynamický obsah, jako je například **LeadSource** , na následujícím snímku obrazovky. Začněte zadáním názvu pole a kliknutím na seznam pro výběr dynamického obsahu v místním okně. 

        >[!Note] 
        > Při přidávání názvů polí můžete sledovat jednotlivé řádky pomocí ":" a potom zadat k vytvoření nového řádku. Jakmile budete mít přidané názvy polí, můžete přidat každý přidružený parametr ze seznamu dynamického výběru.

        ![Přidání e-mailové akce pomocí dynamického obsahu](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **Tělo** – ze seznamu pro výběr dynamického obsahu přidejte do textu e-mailu požadované informace. Například LastName, FirstName, E-mail a společnost. <br> <br> Až budete hotovi s nastavením e-mailového oznámení, bude vypadat jako v příkladu na následujícím snímku obrazovky.


       ![Přidat akci e-mailu](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

5. Kliknutím na **Uložit** dokončete tok. Vytvoří se adresa URL POST protokolu HTTP a je dostupná v okně *při přijetí požadavku HTTP* . Zkopírujte tuto adresu URL pomocí ovládacího prvku kopírování, který se nachází napravo od adresy URL POST protokolu HTTP – to je důležité, aby nedošlo k omylům při nevracení žádné části celé adresy URL. Tuto adresu URL uložte, protože ji budete potřebovat, když konfigurujete správu potenciálních zákazníků na portálu pro publikování.

   ![ADRESA URL PRO POST HTTP ](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>Testování

Podle následujících kroků můžete testovat, že vše funguje podle očekávání, a to pomocí nástroje označovaného jako " [Poster](https://app.getpostman.com/app/download/win64)", který je možné stáhnout online. Toto je k dispozici pro Windows. 

1. Spusťte příkaz post a vyberte **nový** > **žádost** o nastavení nástroje test Tool. 

   ![Požadavek na nastavení testovacího nástroje](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

2. Vyplňte formulář *Uložit požadavek* a pak ho **uložte** do složky, kterou jste vytvořili.

   ![Uložit žádost](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

3. V rozevíracím seznamu vyberte **Odeslat** . 

   ![Test toku](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

4. Vložte adresu URL POST protokolu HTTP z toku, který jste vytvořili v MS flow, kde říká *zadání adresy URL žádosti*.

   ![Vložit adresu URL pro POST HTTP](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

5. Vraťte se ke službě [Flow](https://flow.microsoft.com/) a najděte tok, který jste vytvořili pro odeslání zájemců, na panelu nabídek flow ( **Moje toky** ).  Vyberte 3 tečky vedle názvu toku a vyberte **Upravit**.

   ![Moje toky – upravit](./media/commercial-marketplace-lead-management-instructions-https/my-flows-edit.png)

6. V pravém horním rohu vyberte **test** a pak vyberte spustit akci aktivace a pak vybrat **test**. V horní části obrazovky se zobrazí údaj s oznámením, že se test spustil.

   ![Test Flow – aktivační událost](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

7. Vraťte se do aplikace pro odesílání a vyberte **Odeslat** vedle místa, kam jste vložili adresu URL https.

   ![Test mého toku – odeslání](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

8. Vraťte se ke svému toku a podívejte se na výsledek. Pokud vše funguje podle očekávání, zobrazí se zpráva oznamující, že byla úspěšná.

   ![Výsledky kontroly toku](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

9. Měli byste mít také přijatý e-mail. Prohlédněte si e-mailové doručené pošty. 

    >[!Note] 
    >Pokud nevidíte e-mail od testu, zkontrolujte nevyžádanou poštu a složky Nevyžádaná pošta. Níže si všimnete, že při konfiguraci e-mailového oznámení se zobrazí jenom popisky polí, které jste přidali. Pokud se jednalo o skutečného zájemce vygenerovaného vaší nabídkou, zobrazí se vám také aktuální informace z kontaktu zájemce v textu a na řádku předmětu.

   ![E-mail přijat](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Konfigurace nabídky pro odeslání zájemců do koncového bodu HTTPS

Až budete připraveni ke konfiguraci informací o správě zájemce pro vaši nabídku na portálu pro publikování, postupujte podle následujících kroků:

1. Přejděte na stránku **nastavení nabídky** pro vaši nabídku.
2. V části Správa zájemců vyberte **připojit** .
3. V automaticky otevíraném okně s podrobnostmi o připojení vyberte pro **cíl zájemce** **koncový bod HTTPS** a vložte adresu URL post protokolu HTTP z toku, který jste vytvořili pomocí předchozího postupu, do pole **Adresa URL koncového bodu https** .
4. Vyberte **Uložit**. 

>[!Note] 
>Musíte dokončit konfiguraci zbytku nabídky a publikovat ji předtím, než budete moct získat zájemce pro tuto nabídku.

Po vygenerování zájemců pošle Microsoft do toku zájemce, který se směruje na systém nebo e-mailovou adresu, kterou jste nakonfigurovali.

![Správa vedoucích – připojení](./media/commercial-marketplace-lead-management-instructions-https/lead-management-connect.png)

![Podrobnosti připojení](./media/commercial-marketplace-lead-management-instructions-https/connection-details.png)

![Podrobnosti připojení](./media/commercial-marketplace-lead-management-instructions-https/connection-details-1.png)

