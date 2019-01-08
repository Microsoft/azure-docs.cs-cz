---
title: Koncový bod HTTPS | Dokumentace Microsoftu
description: Konfigurace správy potenciálních zákazníků pro koncový bod HTTPS.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/24/2018
ms.author: pbutlerm
ms.openlocfilehash: 0d14b2eeb90bd360b71b315d7b4f4459f17f5672
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54073757"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Konfigurace správy potenciálních zákazníků pomocí koncového bodu HTTPS

Koncový bod HTTPS můžete použít ke zpracování Azure Marketplace a AppSource vede. Těchto potenciálních zákazníků je možné zapisovat na, které zapisují do systému řízení vztahů se zákazníky (CRM) nebo posílat jako e-mailové oznámení. Tento článek popisuje postup konfigurace zájemce správě pomocí [Microsoft Flow](https://powerapps.microsoft.com/automate-processes/) služby automation.

## <a name="create-a-flow-using-microsoft-flow"></a>Vytvoření toku pomocí Microsoft Flow

1. Otevřít [tok](https://flow.microsoft.com/) webové stránky. Vyberte **přihlášení** nebo vyberte **bezplatná registrace** vytvořte si bezplatný účet toku.

2. Přihlaste se a vyberte **Moje toky** na řádku nabídek.

    ![Moje toky](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows.png)

3. Vyberte **+ vytvořit z prázdné**.

    ![Vytvořit z prázdné](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank.png)

4. Vyberte **vytvořit z prázdné**.

    ![Vytvořit z prázdné](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-create-fromblank2.png)

5. V **prohledat konektory a triggery** zadejte "request" najít požadovaný konektor požadavku.
6. V části **triggery**vyberte **přijetí požadavku HTTP když**. 

    ![Výběr triggeru HTTP byla přijata žádost o](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

7. Použijte jednu z následujících kroků konfigurace **požádat o schéma JSON hlavní části**:

   - Kopírovat [schématu JSON](#JSON-schema) na konci tohoto článku do **požádat o schéma JSON hlavní části** textového pole.
   - Vyberte **K vygenerování schématu použijte ukázkovou datovou část**. V **zadejte nebo vložte ukázkovou datovou část JSON** textového pole, vložte [příklad JSON](#JSON-example). Vyberte **provádí** tvorby schématu.

   >[!Note]
   >V tomto okamžiku v toku můžete připojit k systému CRM nebo konfigurovat e-mailové oznámení.

### <a name="to-connect-to-a-crm-system"></a>Pro připojení k systému CRM

1. Vyberte **+ nový krok**.
2. Zvolte systém CRM podle vašeho výběru s akcí k vytvoření nového záznamu. Na následující obrazovce zachycení ukazuje **Dynamics 365 – vytvořit nový záznam** jako příklad.

    ![Vytvořit nový záznam](./media/cloud-partner-portal-lead-management-instructions-https/https-image009.png)

3. Zadejte **název organizace** , který je vstupy propojení pro váš konektor. Vyberte **vede** z **název Entity** rozevíracího seznamu.

    ![Výběr potenciálních zákazníků](./media/cloud-partner-portal-lead-management-instructions-https/https-image011.png)

4. Tok zobrazí formulář pro poskytování informací o zájemcích. Kliknutím na Přidat dynamický obsah můžete namapovat položky z vstupní požadavek. Na následující obrazovce zachycení ukazuje **LeadTitle** jako příklad.

    ![Přidat dynamický obsah](./media/cloud-partner-portal-lead-management-instructions-https/https-image013.png)

5. Mapování polí a potom vybrat **Uložit** a uložte tok.

6. V požadavku se vytvoří adresu URL protokolu HTTP POST. Zkopírujte tuto adresu URL a použijte jako koncový bod HTTPS.

    ![Adresa URL protokolu HTTP Post](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

### <a name="to-set-up-email-notification"></a>Nastavení e-mailové oznámení

1. Vyberte **+ nový krok**.
2. V části **zvolte akci**vyberte **akce**.
3. V části **Akce** zvolte **Odeslat e-mail**.

    ![Přidání akce e-mailu](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-add-email-action.png)

4. V **odeslat e-mailu**, vyplňte následující požadované pole:

   - **K** – zadejte alespoň jednu platnou e-mailovou adresu.
   - **Předmět** -Flow nabízí možnost Přidat dynamický obsah, jako je třeba **LeadSource** na následujícím snímku obrazovky.

    ![Přidejte akci e-mailu s použitím dynamický obsah](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-dynamic-content.png)

   - **Tělo** – ze seznamu dynamického obsahu, přidejte informace, které mají v textu e-mailu. Například LastName, jméno, e-mailu, a společnosti.

   Jakmile budete hotovi, nastavení e-mailové oznámení, bude vypadat jako příklad na následujícím snímku obrazovky.

   ![Přidání akce e-mailu](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-configure-email-action.png)

5. Vyberte **Uložit** dokončete svůj tok.
6. V požadavku se vytvoří adresu URL protokolu HTTP POST. Zkopírujte tuto adresu URL a použijte jako koncový bod HTTPS.

    ![Adresa URL protokolu HTTP Post](./media/cloud-partner-portal-lead-management-instructions-https/https-myflows-get-post-url.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Konfiguraci nabídky k odeslání potenciálních zákazníků na koncový bod HTTPS

Když konfigurujete informací o zájemcích management pro vaši nabídku, vyberte **koncový bod HTTPS** pro **vést cílové** a vložte adresu URL protokolu HTTP POST, jste si zkopírovali v předchozím kroku.  

![Přidat dynamický obsah](./media/cloud-partner-portal-lead-management-instructions-https/https-image017.png)

Při generování potenciálních zákazníků, Microsoft pošle potenciálních zákazníků pro tok, který směrované do CRM systému nebo e-mailovou adresu, kterou jste nakonfigurovali.

## <a name="json-schema-and-example"></a>Schéma JSON a příklad

Příklad testovací JSON používá následující schéma:

### <a name="json-schema"></a>Schéma JSON

``` json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
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

Můžete kopírovat a upravit podle následujícího příkladu JSON lze použít jako test v MS Flowu.

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

## <a name="next-steps"></a>Další postup

Pokud jste tak již neučinili, nakonfigurovat odběratele [vede](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads) v portál partnerů cloudu.
