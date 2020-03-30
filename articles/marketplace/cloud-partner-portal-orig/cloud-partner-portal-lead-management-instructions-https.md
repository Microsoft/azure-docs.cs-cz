---
title: Koncový bod HTTPS | Azure Marketplace
description: Nakonfigurujte správu zájemců pro koncový bod HTTPS.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/24/2018
ms.author: dsindona
ms.openlocfilehash: cb6ef173e97a7c2bbd7d7cad5e5074b1f2d0f066
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288593"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Konfigurace správy potenciálních zákazníků pomocí koncového bodu HTTPS

Ke zpracování potenciálních zákazníků Azure Marketplace a AppSource můžete použít koncový bod HTTPS. Tito zájemci mohou být zapsáni do systému řízení vztahů se zákazníky (CRM) nebo odesláni jako e-mailové oznámení. Tento článek popisuje, jak nakonfigurovat správu zájemců pomocí služby automatizace [Microsoft Flow.](https://powerapps.microsoft.com/automate-processes/)

## <a name="create-a-flow-using-microsoft-flow"></a>Vytvoření toku pomocí Microsoft Flow

1. Otevřete webovou stránku [Flow.](https://flow.microsoft.com/) Vyberte **Přihlásit** se nebo **zvolte Registrace zdarma** a vytvořte si bezplatný účet Toku.

2. Přihlaste se a na řádku nabídek vyberte **Moje toky.**

    ![Moje toky](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows.png)

3. Vyberte **+ Vytvořit z prázdné**.

    ![Vytvořit z prázdné](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank.png)

4. Vyberte **Vytvořit z prázdné**.

    ![Vytvořit z prázdné](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank2.png)

5. Do pole Hledat konektory a aktivační události zadejte "request" pro **vyhledání** konektoru Požadavku.
6. V části **Triggers**vyberte **Při přijetí požadavku HTTP**. 

    ![Vyberte aktivační událost přijatého požadavku HTTP.](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

7. Ke konfiguraci **schématu JSON tělo požadavku**použijte jeden z následujících kroků :

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

Při generování zájemců odesílá společnost Microsoft zájemce na tok, který se směruje na nakonfigurovaný systém CRM nebo e-mailovou adresu.

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

Můžete zkopírovat a upravit následující příklad JSON použít jako test v MS Flow.

### <a name="json-example"></a>Příklad JSON

```json
{
"OfferTitle": "Test Microsoft",
"LeadSource": "Test run through MS Flow",
"UserDetails": {
"Company": "Contoso",
"Country": "USA",
"Email": "someone@contoso.com",
"FirstName": "Some",
"LastName": "One",
"Phone": "16175555555",
"Title": "Esquire"
}
}
```

## <a name="next-steps"></a>Další kroky

Pokud jste tak ještě neučinili, nakonfigurujte [zájemce](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) zákazníků na portálu partnerů cloudu.
