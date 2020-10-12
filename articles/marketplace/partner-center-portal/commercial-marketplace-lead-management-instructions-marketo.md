---
title: Správa zájemců ve Marketě – komerční tržiště Microsoftu
description: Naučte se používat systém CRM Marketo ke správě potenciálních zákazníků z Microsoft AppSource a Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 03/30/2020
ms.openlocfilehash: be1f21f927b01d66e19dc5e97b38e5c35e6664cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86120258"
---
# <a name="use-marketo-to-manage-commercial-marketplace-leads"></a>Použití Marketo ke správě potenciálních zákazníků z obchodu na webu Marketplace

Tento článek popisuje, jak nastavit systém Marketo pro CRM na zpracování potenciálních zákazníků z nabídek v Microsoft AppSource a Azure Marketplace.

## <a name="set-up-your-marketo-crm-system"></a>Nastavení systému Marketo CRM

1. Přihlaste se k Marketo.

1. Vyberte **návrh Studio**.

    ![Marketo návrh Studio](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

1.  Vyberte **nový formulář**.

    ![Nový formulář Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

1.  Vyplňte požadovaná pole v dialogovém okně **nový formulář** a pak vyberte **vytvořit**.

    ![Vytvoření nového formuláře Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

1.  Na stránce **Podrobnosti pole** vyberte **Dokončit**.

    ![Formulář pro dokončení Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

1.  Schvalte a zavřete.

1. Na kartě **MarketplaceLeadBackend** vyberte **Vložit kód**. 

    ![Kód pro vložení Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

1. Kód pro vložení do markety zobrazí kód podobný následujícímu příkladu.

    ```html
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

1. Zkopírujte hodnoty pro následující pole zobrazená ve formuláři kód pro vložení. Tyto hodnoty použijete ke konfiguraci nabídky pro příjem zájemců v dalším kroku. Následující příklad slouží jako vodítko pro získání potřebných ID z příkladu kódu pro vložení do Marketo.

    - ID serveru = **ys12**
    - Munchkin ID = **123-PQR-789**
    - ID formuláře = **1179**

    Další způsob, jak zjistit tyto hodnoty:

    - ID serveru najdete v adrese URL vaší instance Marketo, například `serverID.marketo.com` .
    - Pořiďte si Munchkin ID vašeho předplatného tak **Admin**  >  , že v poli **ID účtu Munchkin** zobrazíte nabídku správce**Munchkin** , nebo z první části vaší REST API domény hostitele Marketo: `https://{Munchkin ID}.mktorest.com` .
    - ID formuláře je ID formuláře pro vložení kódu, který jste vytvořili v kroku 7 ke směrování potenciálních zákazníků z webu Marketplace.

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>Konfigurace nabídky pro odesílání zájemců do markety

Až budete připraveni ke konfiguraci informací o správě zájemců pro vaši nabídku na portálu pro publikování, postupujte podle těchto kroků. 

1. Přihlaste se k [partnerskému centru](https://partner.microsoft.com/dashboard/home).

1. Vyberte svou nabídku a pak navštivte kartu **nastavení nabídky** .

1. V části **zájemci pro zákazníky** vyberte **připojit**.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-marketo/customer-leads.png" alt-text="Potenciální zákazníci":::

1. V místním okně **Podrobnosti připojení** vyberte **Marketo** pro **cíl zájemce**.

    ![Zvolit cíl zájemce](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

1. Zadejte **ID serveru**, **ID účtu Munchkin**a **ID formuláře**.

    > [!NOTE]
    > Musíte dokončit konfiguraci zbytku nabídky a publikovat ji předtím, než budete moct získat zájemce pro tuto nabídku. 

1. V části **kontaktní e-mail**zadejte e-mailové adresy pro lidi ve vaší společnosti, kteří by měli dostávat e-mailová oznámení při přijetí nového zájemce. Více e-mailových adres můžete zadat tak, že je oddělíte středníkem.

1. Vyberte **OK**.

   Abyste se ujistili, že jste se úspěšně připojili k cíli zájemce, vyberte **ověřit**. V případě úspěchu budete mít vedoucího testu v cíli realizace.

   ![Místní okno s podrobnostmi připojení](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-connection-details.png)
