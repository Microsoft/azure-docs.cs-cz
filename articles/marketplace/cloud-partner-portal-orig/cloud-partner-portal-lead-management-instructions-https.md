---
title: Konfigurace správy zájemců pomocí koncového bodu HTTPS | Azure Marketplace
description: Přečtěte si, jak pomocí koncového bodu HTTP zpracovávat potenciální zákazníky Microsoft AppSource a Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: dsindona
ms.openlocfilehash: f56cc5aaad7d77ff8dc753115ef1becb08ddde73
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770186"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Konfigurace správy potenciálních zákazníků pomocí koncového bodu HTTPS

Ke zpracování potenciálních zákazníků Microsoft AppSource a Azure Marketplace můžete použít koncový bod HTTPS. Tito zájemci mohou být zapsáni do systému řízení vztahů se zákazníky (CRM) nebo odesláni jako e-mailové oznámení. Tento článek popisuje, jak pomocí služby automatizace [Microsoft Power Automate](https://powerapps.microsoft.com/automate-processes/) ke konfiguraci správy zájemců.

## <a name="create-a-flow-using-microsoft-power-automate"></a>Vytvoření toku pomocí Microsoft Power Automate

1. Otevřete webovou stránku [Power Automate.](https://flow.microsoft.com/) Vyberte **Přihlásit** se nebo **zvolte Registrace zdarma** a vytvořte si bezplatný účet Toku.

1. Přihlaste se a na řádku nabídek vyberte **Moje toky.**
    > [!div class="mx-imgBorder"]
    > ![Moje toky](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows.png)

1. V části **+ Nový**vyberte + Okamžité – **z prázdné**.
    > [!div class="mx-imgBorder"]
    > ![Vytvořit z prázdné](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank.png)

1. Pojmenujte tok a potom v části **Zvolte způsob spuštění tohoto toku**vyberte **Při přijetí požadavku HTTP**.

    > [!div class="mx-imgBorder"]
    > ![Vyberte aktivační událost přijatého požadavku HTTP.](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

1. Kliknutím na krok toku ho rozbalte.

    > [!div class="mx-imgBorder"]
    > ![Rozšíření kroku toku](./media/cloud-partner-portal-lead-management-instructions-https/expand-flow-step.png)

1. Ke konfiguraci **schématu JSON tělo požadavku**použijte jednu z následujících metod :

   - Zkopírujte [schéma JSON](#json-schema) na konci tohoto článku do textového pole **Schéma Text požadavku JSON.**
   - Vyberte **K vygenerování schématu použijte ukázkovou datovou část**. V **textovém poli Enter nebo paste ukázkové datové části JSON** vložte do [příkladu JSON](#json-example). Chcete-li vytvořit schéma, **vyberte Hotovo.**

   >[!Note]
   >V tomto okamžiku toku se můžete připojit k systému CRM nebo nakonfigurovat e-mailové oznámení.

### <a name="to-connect-to-a-crm-system"></a>Připojení k systému CRM

1. Vyberte **+ Nový krok**.
2. Vyberte si crm systém podle vašeho výběru s akcí k vytvoření nového záznamu. Následující snímek obrazovky ukazuje **Dynamics 365 - Vytvoření nového záznamu** jako příklad.

    ![Vytvoření nového záznamu](./media/cloud-partner-portal-lead-management-instructions-https/https-image009.png)

3. Zadejte **název organizace,** který je vstupy připojení pro konektor. V rozevíracím seznamu Název entity vyberte **Zájemce.** **Entity Name**

    ![Vybrat zájemce](./media/cloud-partner-portal-lead-management-instructions-https/https-image011.png)

4. Tok zobrazuje formulář pro poskytování informací o zájemcích. Položky ze vstupní žádosti můžete mapovat tak, že chcete přidat dynamický obsah. Následující snímek obrazovky zobrazuje **OfferTitle** jako příklad.

    ![Přidat dynamický obsah](./media/cloud-partner-portal-lead-management-instructions-https/https-image013.png)

5. Namapujte požadovaná pole a pak vyberte **Uložit,** chcete-li tok uložit.

6. V požadavku je vytvořena adresa URL HTTP POST. Zkopírujte tuto adresu URL a použijte ji jako koncový bod HTTPS.

    ![Adresa URL příspěvku HTTP](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

### <a name="to-set-up-email-notification"></a>Nastavení e-mailového oznámení

1. Vyberte **+ Nový krok**.
2. V části **Zvolit akci**vyberte **Akce**.
3. V části **Akce** zvolte **Odeslat e-mail**.

    ![Přidání akce e-mailu](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-add-email-action.png)

4. V **části Odeslat e-mail**nakonfigurujte následující povinná pole:

   - **Chcete-li** - Zadejte alespoň jednu platnou e-mailovou adresu.
   - **Předmět** – tok vám dává možnost přidat dynamický obsah, například **LeadSource** v následujícím snímání obrazovky.

     ![Přidání akce e-mailu pomocí dynamického obsahu](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-dynamic-content.png)

   - **Tělo** – ze seznamu Dynamický obsah přidejte požadované informace do textu e-mailu. Například Příjmení, Jméno, E-mail a Společnost.

   Po dokončení nastavení e-mailového oznámení bude vypadat jako příklad v následujícím zachycení obrazovky.

   ![Přidání akce e-mailu](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-action.png)

5. Chcete-li dokončit tok, vyberte **uložit.**

6. V požadavku je vytvořena adresa URL HTTP POST. Zkopírujte tuto adresu URL a použijte ji jako koncový bod HTTPS.

    ![Adresa URL příspěvku HTTP](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Konfigurace nabídky pro odesílání zájemců do koncového bodu HTTPS

Při konfiguraci informací o správě zájemců pro vaši nabídku vyberte **koncový bod HTTPS** pro cíl **zájemce** a vložte do adresy URL HTTP POST, kterou jste zkopírovali v předchozím kroku.  

![Přidat dynamický obsah](./media/cloud-partner-portal-lead-management-instructions-https/https-image017.png)

Když jsou vygenerováni zájemci, odešle společnost Microsoft zájemce do toku power automatu, který se směruje na nakonfigurovaný systém CRM nebo e-mailovou adresu.

## <a name="json-schema-and-example"></a>Schéma JSON a příklad

Testovací příklad JSON používá následující schéma:

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

Můžete zkopírovat a upravit následující příklad JSON použít jako test v toku.

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

Pokud jste tak ještě neučinili, nakonfigurujte [zájemce](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) zákazníků na portálu partnerů cloudu.
