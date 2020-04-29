---
title: Konfigurace správy potenciálních zákazníků pomocí koncového bodu HTTPS | Azure Marketplace
description: Naučte se používat koncový bod HTTP ke zpracování Microsoft AppSource a Azure Marketplace potenciálních zákazníků.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: dsindona
ms.openlocfilehash: f56cc5aaad7d77ff8dc753115ef1becb08ddde73
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770186"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Konfigurace správy potenciálních zákazníků pomocí koncového bodu HTTPS

Ke zpracování Microsoft AppSource a Azure Marketplace potenciálních zákazníků můžete použít koncový bod HTTPS. Tyto zájemce můžete zapsat do systému správy vztahů se zákazníky (CRM) nebo odesílat jako e-mailové oznámení. Tento článek popisuje, jak pomocí služby [Microsoft Power](https://powerapps.microsoft.com/automate-processes/) autoautomation Service nakonfigurovat správu potenciálních zákazníků.

## <a name="create-a-flow-using-microsoft-power-automate"></a>Vytvoření toku pomocí Microsoft Power automatizuje

1. Otevřete webovou stránku [Power automatizováno](https://flow.microsoft.com/) . Vyberte **Přihlásit** se nebo vyberte **zaregistrovat se zdarma** , abyste mohli vytvořit bezplatný účet toku.

1. Přihlaste se a v řádku nabídek vyberte **Moje toky** .
    > [!div class="mx-imgBorder"]
    > ![Moje toky](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows.png)

1. V části **+ Nový**vyberte **+ rychlá – od prázdná**.
    > [!div class="mx-imgBorder"]
    > ![Vytvořit z prázdné](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank.png)

1. Pojmenujte tok a potom v části **Zvolte, jak se má tento tok aktivovat**, vyberte, **kdy se přijme požadavek HTTP**.

    > [!div class="mx-imgBorder"]
    > ![Vyberte Trigger přijatý žádostí HTTP](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

1. Kliknutím na krok toku ho rozbalte.

    > [!div class="mx-imgBorder"]
    > ![Rozbalí krok Flow.](./media/cloud-partner-portal-lead-management-instructions-https/expand-flow-step.png)

1. Ke konfiguraci **schématu JSON těla žádosti**použijte jednu z následujících metod:

   - Zkopírujte [schéma JSON](#json-schema) na konci tohoto článku do textového pole **schématu JSON textu žádosti** .
   - Vyberte **K vygenerování schématu použijte ukázkovou datovou část**. Do textového pole **Zadejte nebo vložte ukázkovou datovou část JSON** vložte do [příkladu JSON](#json-example). Vyberte **Hotovo** a vytvořte schéma.

   >[!Note]
   >V tomto okamžiku toku se můžete připojit k systému CRM nebo nakonfigurovat e-mailové oznámení.

### <a name="to-connect-to-a-crm-system"></a>Připojení k systému CRM

1. Vyberte **+ Nový krok**.
2. Zvolte systém CRM podle vašeho výběru a akci pro vytvoření nového záznamu. Následující snímek obrazovky ukazuje **Dynamics 365 – vytvoří nový záznam** jako příklad.

    ![Vytvoření nového záznamu](./media/cloud-partner-portal-lead-management-instructions-https/https-image009.png)

3. Zadejte **název organizace** , který bude obsahovat vstupy připojení pro váš konektor. V rozevíracím seznamu **název entity** vyberte **zájemci** .

    ![Vybrat potenciální zákazníky](./media/cloud-partner-portal-lead-management-instructions-https/https-image011.png)

4. Flow zobrazuje formulář pro poskytování informací o potenciálních vedoucích. Položky ze vstupní žádosti můžete mapovat tak, že vyberete možnost Přidat dynamický obsah. Následující snímek obrazovky ukazuje **OfferTitle** jako příklad.

    ![Přidat dynamický obsah](./media/cloud-partner-portal-lead-management-instructions-https/https-image013.png)

5. Namapujte požadovaná pole a potom vyberte **Uložit** . tím tok uložíte.

6. V žádosti se vytvoří adresa URL POST protokolu HTTP. Zkopírujte tuto adresu URL a použijte ji jako koncový bod HTTPS.

    ![Adresa URL pro post HTTP](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

### <a name="to-set-up-email-notification"></a>Nastavení e-mailového oznámení

1. Vyberte **+ Nový krok**.
2. V části **zvolit akci**vyberte **Akce**.
3. V části **Akce** zvolte **Odeslat e-mail**.

    ![Přidat akci e-mailu](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-add-email-action.png)

4. V části **Odeslat e-mail**nakonfigurujte následující povinná pole:

   - Zadejte alespoň jednu platnou **e-** mailovou adresu.
   - Tok **předmětu** vám dává možnost Přidat dynamický obsah, jako je například **LeadSource** , na následujícím snímku obrazovky.

     ![Přidání e-mailové akce pomocí dynamického obsahu](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-dynamic-content.png)

   - **Tělo** – ze seznamu dynamického obsahu přidejte do textu e-mailu požadované informace. Například LastName, FirstName, E-mail a společnost.

   Až budete hotovi s nastavením e-mailového oznámení, bude vypadat jako v příkladu na následujícím snímku obrazovky.

   ![Přidat akci e-mailu](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-action.png)

5. Kliknutím na **Uložit** dokončete tok.

6. V žádosti se vytvoří adresa URL POST protokolu HTTP. Zkopírujte tuto adresu URL a použijte ji jako koncový bod HTTPS.

    ![Adresa URL pro post HTTP](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Konfigurace nabídky pro odeslání zájemců do koncového bodu HTTPS

Když nakonfigurujete informace o správě zájemců pro vaši nabídku, vyberte **koncový bod HTTPS** pro **cíl zájemce** a vložte adresu URL post protokolu HTTP, kterou jste zkopírovali v předchozím kroku.  

![Přidat dynamický obsah](./media/cloud-partner-portal-lead-management-instructions-https/https-image017.png)

Když vygenerujete zájemce, společnost Microsoft pošle do vašeho toku Power Automate, které se budou směrovat na systém nebo e-mailovou adresu, kterou jste nakonfigurovali.

## <a name="json-schema-and-example"></a>Schéma a příklad JSON

Příklad testu JSON používá následující schéma:

### <a name="json-schema"></a>Schéma JSON

``` json
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

Následující příklad JSON můžete zkopírovat a upravit tak, aby se ve vašem toku používal jako test.

### <a name="json-example"></a>Příklad JSON

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

## <a name="next-steps"></a>Další kroky

Pokud jste to ještě neudělali, nakonfigurujte [potenciální](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) zákazníky v portál partnerů cloudu.
