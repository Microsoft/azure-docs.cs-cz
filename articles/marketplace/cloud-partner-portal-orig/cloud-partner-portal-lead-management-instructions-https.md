---
title: Koncový bod HTTPS | Dokumentace Microsoftu
description: Konfigurace správy potenciálních zákazníků pro protokol Https.
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
ms.date: 09/17/2018
ms.author: pbutlerm
ms.openlocfilehash: fd13a7281c7e8702fd199364261ebcd458db0555
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809186"
---
# <a name="configure-lead-management-using-an-https-endpoint"></a>Konfigurace správy potenciálních zákazníků pomocí koncového bodu HTTPS

Koncový bod HTTPS můžete použít ke zpracování Azure Marketplace a AppSource potenciálních zákazníků, které se dají zapisovat do systému CRM. Tento článek popisuje, jak nakonfigurovat pomocí služby Microsoft Flow automatizace Správa potenciálních zákazníků.


## <a name="create-a-flow-using-microsoft-flow"></a>Vytvoření toku pomocí Microsoft Flow

1.  Otevřít [tok](https://flow.microsoft.com/) webové stránky. Vyberte **přihlášení** nebo vyberte **bezplatná registrace** vytvořte si bezplatný účet toku.

2.  Přihlaste se a vyberte **Moje toky** na řádku nabídek.

    ![Moje toky](./media/cloud-partner-portal-lead-management-instructions-https/image001.png)

3.  Vyberte **vytvořit z prázdné**.

    ![Vytvořit z prázdné](./media/cloud-partner-portal-lead-management-instructions-https/image003.png)


4.  Vyberte **žádostí a odpovědí** konektoru a pak vyhledejte pro trigger požadavku. 

    ![Vytvořit z prázdné](./media/cloud-partner-portal-lead-management-instructions-https/image005.png)

5. Vyberte **žádosti** aktivační události.
    ![Aktivační událost požadavek](./media/cloud-partner-portal-lead-management-instructions-https/image007.png)


6.  Kopírovat **příklad JSON** na konci tohoto článku do **požádat o schéma JSON hlavní části**.

7.  Přidat nový krok a zvolte systém CRM podle vašeho výběru s akcí k vytvoření nového záznamu. Na další obrazovce zachycení ukazuje **Dynamics 365 – vytvořit nový záznam** jako příklad.

    ![Vytvořit nový záznam](./media/cloud-partner-portal-lead-management-instructions-https/image009.png)

8.  Zadejte vstupy připojení pro váš konektor, vyberte **vede** entity.

    ![Výběr potenciálních zákazníků](./media/cloud-partner-portal-lead-management-instructions-https/image011.png)

9.  Toky zobrazí formulář pro poskytování informací o zájemcích. Kliknutím na Přidat dynamický obsah můžete namapovat položky z vstupní požadavek.

    ![Přidat dynamický obsah](./media/cloud-partner-portal-lead-management-instructions-https/image013.png)

10.  Mapování polí a potom vybrat **Uložit** a uložte tok.

11. V požadavku se vytvoří adresu URL protokolu HTTP POST. Zkopírujte tuto adresu URL a použijte jako koncový bod HTTPS.

    ![Adresa URL protokolu HTTP Post](./media/cloud-partner-portal-lead-management-instructions-https/image015.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Konfiguraci nabídky k odeslání potenciálních zákazníků na koncový bod HTTPS

Když konfigurujete informací o zájemcích management pro vaši nabídku, vyberte **koncový bod HTTPS** vést cíl a vložte adresu URL protokolu HTTP POST, který jste zkopírovali v předchozím kroku.  

![Přidat dynamický obsah](./media/cloud-partner-portal-lead-management-instructions-https/image017.png)

Při generování potenciálních zákazníků, Microsoft vám pošle potenciálních zákazníků pro tok, který směrované do systému CRM, které jste nakonfigurovali.


## <a name="json-example"></a>Příklad JSON

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
