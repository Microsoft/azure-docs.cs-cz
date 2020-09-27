---
title: Zásady ochrany osobních údajů a zabezpečení dat pro konektory Google
description: Přečtěte si, jaký dopad má zásada zabezpečení a ochrany osobních údajů Google na konektorech Google, jako je například Gmail, v Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: conceptual
ms.date: 06/05/2020
ms.openlocfilehash: 2a5204be638f108b40e431b148c9cb97788c4a52
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2020
ms.locfileid: "91400753"
---
# <a name="data-security-and-privacy-policies-for-google-connectors-in-azure-logic-apps"></a>Zásady zabezpečení a ochrany osobních údajů pro konektory Google v Azure Logic Apps

Od **1. května 2020**můžou změny v důsledku [zabezpečení a zásad ochrany osobních údajů](https://www.blog.google/technology/safety-security/project-strobe/) v Google ovlivnit pracovní postupy aplikace logiky, které používají [konektor Gmail](/connectors/gmail/). Pokud vaše aplikace logiky používají konektor Gmail s účtem uživatele Gmail (e-mailová adresa, která končí @gmail.com @googlemail.com na nebo), vaše aplikace logiky můžou používat jenom konkrétní [triggery, akce a konektory schválené společností Google](#approved-connectors).

> [!NOTE]
> Pokud vaše aplikace logiky používají konektor Gmail s obchodním účtem G-Suite (e-mailová adresa s vlastní doménou), vaše aplikace logiky se nijak nedotýká a nebude mít k dispozici žádné omezení na používání konektoru gmail.

## <a name="affected-logic-apps"></a>Ovlivněné aplikace logiky

Pokud máte Logic Apps, které používají konektor Gmail, obdržíte e-mail s potenciálně ovlivněnými Logic Apps. Od 15. **června 2020**se ale všechny pracovní postupy, které nedodržují předpisy, zakážou. Můžete provést jednu z těchto akcí:

* Aktualizujte ovlivněné aplikace logiky podle [kroků v tomto tématu](#update-affected-workflows). Musíte vytvořit klientskou aplikaci Google, která poskytuje ID klienta a tajný kód klienta, který používáte k ověřování ve triggeru nebo akci gmail.

* Aktualizujte ovlivněné aplikace logiky tak, aby používaly jenom [konektory schválené pro Google](#approved-connectors) předtím, než znovu povolíte zakázané Logic Apps.

<a name="approved-connectors"></a>

## <a name="google-approved-connectors"></a>Konektory schválené pro Google

V rámci této zásady platí, že když použijete účet příjemce Gmail, můžete použít konektor Gmail jenom s konkrétními službami, které se můžou změnit. Naše technické týmy pokračují v práci s Google a přidává další služby do tohoto seznamu. V současnosti se tady nacházejí triggery, akce a konektory, které jsou v rámci služby Gmail standardně schválené a které můžete použít ve stejném pracovním postupu aplikace logiky pomocí konektoru Gmail, když použijete účet uživatele Gmail:

* Logic Apps integrovaných triggerů a akcí: Batch, Control, datové operace, datum a čas, plochý soubor, kapalina, požadavek, plán, proměnné a XML

  Integrované triggery a akce, které neschválily Google, například HTTP, Azure Functions, Azure Logic Apps a další, vytvářejí aplikaci logiky nekompatibilní s konektorem Gmail, protože aplikace může data odesílat nebo přijímat odkudkoli.

* Služby Google: Gmail, Kalendář Google, Kontakty Google, disk Google, tabulky Google a úkoly Google

* Schválené služby Microsoftu: Dynamics 365, Excel Online, Microsoft teams, Microsoft 365, OneDrive a SharePoint Online

* Konektory pro zdroje dat spravované zákazníkem: FTP, RSS, SFTP, SMTP a SQL Server

## <a name="non-compliant-examples"></a>Příklady neodpovídajícího typu

Tady je několik příkladů použití konektoru Gmail s integrovanými aktivačními událostmi a akcemi nebo spravovanými konektory, které neschválil Google:

* Tato aplikace logiky používá konektor Gmail s integrovaným triggerem protokolu HTTP:

  ![Aplikace logiky, které nedodržují předpisy – příklad 1](./media/connectors-google-data-security-privacy-policy/not-compliant-logic-app-1.png)
  
  Aplikace logiky používá také konektor kalendáře Google, který je schválen.

* Tato aplikace logiky používá konektor Gmail s konektorem Azure Blob Storage:

  ![Aplikace logiky, které nedodržují předpisy – příklad 2](./media/connectors-google-data-security-privacy-policy/not-compliant-logic-app-2.png)

* Tato aplikace logiky používá konektor Gmail s konektorem Twitteru:

  ![Aplikace logiky, které nedodržují předpisy – příklad 3](./media/connectors-google-data-security-privacy-policy/not-compliant-logic-app-3.png)

Nejnovější informace najdete v [technické referenční dokumentaci konektoru Gmail](/connectors/gmail/).

<a name="update-affected-workflows"></a>

## <a name="steps-for-affected-logic-apps"></a>Postup pro ovlivněné aplikace logiky

Pokud musíte použít konektor Gmail s uživatelským účtem Gmail a neschválenými konektory Google v aplikaci logiky, můžete vytvořit vlastní aplikaci Google pro osobní nebo interní použití ve vašem podniku. V tomto scénáři jsou zde popsané kroky vysoké úrovně, které je třeba provést:

1. Pomocí [konzoly rozhraní Google API](https://console.developers.google.com)vytvoříte klientskou aplikaci Google.

1. V konektoru Gmail použijte ID klienta a hodnoty tajného klíče klienta z vaší klientské aplikace Google.

Další informace najdete v [technické referenční dokumentaci konektoru Gmail](/connectors/gmail/#authentication-and-bring-your-own-application).

### <a name="create-google-client-app"></a>Vytvoření klientské aplikace Google

Chcete-li nastavit projekt pro klientskou aplikaci, použijte [Průvodce konzolou rozhraní Google API](https://console.developers.google.com/start/api?id=gmail&credential=client_key) a postupujte podle pokynů. Pokud chcete zobrazit podrobné pokyny, přečtěte si pokyny v [dokumentaci k technickým odkazům ke konektoru Gmail](/connectors/gmail/#authentication-and-bring-your-own-application).

Až budete hotovi, vaše obrazovka vypadá podobně jako v tomto příkladu, ale budete mít vlastní **ID klienta** a tajné hodnoty pro **klienta** , které později použijete ve své aplikaci logiky.

![ID klienta a tajný klíč klienta pro vaši klientskou aplikaci Google](./media/connectors-google-data-security-privacy-policy/google-api-console.png)

### <a name="use-client-app-settings-in-logic-app"></a>Použití nastavení klientské aplikace v aplikaci logiky

Pokud chcete použít ID klienta a tajný klíč klienta z vaší klientské aplikace Google ve vaší aktivační události nebo akci Gmail, postupujte podle následujících kroků:

1. V [Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v návrháři aplikace logiky.

1. Pokud přidáváte novou aktivační událost nebo akci Gmail a vytváříte zcela nové připojení, pokračujte k dalšímu kroku. V opačném případě v aktivační události nebo v akci Gmail vyberte **změnit připojení**  >  **Přidat nový**, například:

   ![Vyberte změnit připojení > přidat nový.](./media/connectors-google-data-security-privacy-policy/change-gmail-connection.png)

1. Zadejte informace o připojení, například:

   ![Zadání informací o připojení](./media/connectors-google-data-security-privacy-policy/authentication-type-bring-your-own.png)

   | Vlastnost | Hodnota | Popis |
   |----------|-------|-------------|
   | **Typ ověřování** | **Přineste si vlastní aplikaci** | Určuje, že budete používat vlastní klientskou aplikaci pro ověřování. |
   | **ID klienta** | <*ID klienta*> | ID klienta z klientské aplikace Google |
   | **Tajný kód klienta** | <*tajný kód klienta*> | Tajný kód klienta z klientské aplikace Google |
   ||||

1. Až budete hotovi, vyberte **Přihlásit**se.

   Zobrazí se stránka, která obsahuje klientskou aplikaci, kterou jste vytvořili. Pokud používáte účet příjemce Gmail, může se zobrazit stránka, na které se zobrazí vaše klientská aplikace Google, a zobrazí se výzva, abyste nejdřív povolili přístup k vašemu účtu Google.

   ![Zobrazit výzvu pro přístup k vašemu účtu Google](./media/connectors-google-data-security-privacy-policy/allow-access-authorized-domain.png)

1. V případě potřeby vyberte možnost **udělit**.

   Ve vaší aplikaci logiky teď můžete používat konektor Gmail bez omezení.

## <a name="next-steps"></a>Další kroky

Další informace o [konektoru Gmail](/connectors/gmail/)

