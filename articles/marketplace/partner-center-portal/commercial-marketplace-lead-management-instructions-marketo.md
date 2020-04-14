---
title: Konfigurace správy potenciálních zákazníků v Marketu | Azure Marketplace
description: Konfigurace správy potenciálních zákazníků pro zákazníky marketu pro Azure Marketplace.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 04eae529efcf9509d8cd43a7629bb0b76b593cd0
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81252426"
---
# <a name="configure-lead-management-in-marketo"></a>Konfigurace správy potenciálních zákazníků v Marketu

Tento článek popisuje, jak nastavit systém Marketo CRM pro zpracování prodejních zájemců z nabídky na trhu.

## <a name="set-up-your-marketo-crm-system"></a>Nastavení systému Marketo CRM

1. Přihlaste se do Marketo.
2. Vyberte **návrhstudia**.
    ![Marketo Design Studio](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

3.  Vyberte **Nový formulář**.
    ![Marketo nová forma](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

4.  Vyplňte požadovaná pole v novém formuláři a pak vyberte **Vytvořit**.
    ![Marketo vytvořit novou formu](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

5.  V části Podrobnosti pole vyberte **Dokončit**.
    ![Marketo dokončovací formulář](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

6.  Schválit a zavřít.

7. Na kartě *MarketplaceLeadBacked* vyberte **Vložit kód**. 

    ![Kód pro vložení](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

8. Kód pro vložení Marketo zobrazí kód podobný následujícímu příkladu.

    ```
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

9. Zkopírujte hodnoty pro níže uvedená pole zobrazená ve formuláři Kód vložení. Tyto hodnoty použijete ke konfiguraci nabídky pro příjem zájemců v dalším kroku. Použijte další příklad jako vodítko pro získání Ids, které potřebujete z Marketo Vložit kód příkladu.

    - ID serveru = **ys12**
    - Munchkin ID = **123-PQR-789**
    - ID formuláře = **1179**

    **Dalším způsobem, jak zjistit tyto hodnoty**

    - ID serveru se nachází v adrese URL instance Marketo, například "`serverID.marketo.com`".
    - Získejte své předplatné Munching ID tím, že půjdete do admin>Munchkin menu v "Munchkin ID účtu" pole, nebo z první části vašeho Marketo REST API hostitelské subdomény: `https://{Munchkin ID}.mktorest.com`.
    - ID formuláře je ID formuláře Vložit kód, který jste vytvořili v kroku 7 k směrování zájemců z našeho trhu.

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>Nakonfigurujte svou nabídku pro odesílání potenciálních zákazníků do Marketo

Až budete připraveni nakonfigurovat informace o správě zájemců pro vaši nabídku na portálu pro publikování, postupujte podle následujících kroků: 

1. Přejděte na stránku **Nastavení nabídky** pro vaši nabídku.
1. V části Správa zájemců vyberte **Připojit.** 

    ![Správa potenciálních zákazníků - Připojení](./media/commercial-marketplace-lead-management-instructions-marketo/lead-management-connect.png)

1. V rozbalovacím okně Podrobnosti o připojení vyberte **položku Marketo** pro cíl zájemce.

    ![Výběr cíle zájemce](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

4. Zadejte **ID serveru**, **ID účtu munching**a **ID formuláře**.

    >[!Note]
    >Před přijetím zájemců o nabídku je nutné dokončit konfiguraci zbývající nabídky a publikovat ji. 

5. **Kontaktní e-mail** – Poskytněte e-maily lidem ve vaší společnosti, kteří by měli dostávat e-mailová oznámení při přijetí nového zájemce. Můžete poskytnout více e-mailů tím, že je oddělíte středníkem.
6. Vyberte **OK**.

Chcete-li se ujistit, že jste se úspěšně připojili k cíli zájemce, klikněte na tlačítko ověřit. Pokud bude úspěšná, budete mít testovacího zájemce v cíli zájemce.

![Podrobnosti připojení](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-connection-details.png)