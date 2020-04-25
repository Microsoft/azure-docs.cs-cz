---
title: Konfigurace správy potenciálních zákazníků ve Marketě | Azure Marketplace
description: Nakonfigurujte správu zájemců pro služby Marketo pro Azure Marketplace zákazníky.
author: qianw211
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: b142e0ab1aaa242157e207ceecf958be51bb1721
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133658"
---
# <a name="configure-lead-management-in-marketo"></a>Konfigurace správy potenciálních zákazníků ve Marketě

Tento článek popisuje, jak nastavit systém Marketo pro CRM pro zpracování zájemců z komerční nabídky na webu Marketplace.

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

    - ID serveru najdete v adrese URL vaší instance Marketo, například `serverID.marketo.com`.
    - Pořiďte si Munchkin ID vašeho předplatného tak, že v poli **ID účtu Munchkin** zobrazíte nabídku **správce** > **Munchkin** , nebo z první části vaší REST API domény hostitele Marketo `https://{Munchkin ID}.mktorest.com`:.
    - ID formuláře je ID formuláře pro vložení kódu, který jste vytvořili v kroku 7 ke směrování potenciálních zákazníků z webu Marketplace.

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>Konfigurace nabídky pro odesílání zájemců do markety

Až budete připraveni ke konfiguraci informací o správě zájemců pro vaši nabídku na portálu pro publikování, postupujte podle těchto kroků. 

1. Pro vaši nabídku přejdete na stránku **nastavení nabídek** .

1. V části **Správa zájemců** vyberte **připojit** . 

    ![Tlačítko připojit k části Správa zájemců](./media/commercial-marketplace-lead-management-instructions-marketo/lead-management-connect.png)

1. V místním okně **Podrobnosti připojení** vyberte **Marketo** pro **cíl zájemce**.

    ![Zvolit cíl zájemce](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

1. Zadejte **ID serveru**, **ID účtu Munchkin**a **ID formuláře**.

    > [!NOTE]
    > Musíte dokončit konfiguraci zbytku nabídky a publikovat ji předtím, než budete moct získat zájemce pro tuto nabídku. 

1. V části **kontaktní e-mail**zadejte e-mailové adresy pro lidi ve vaší společnosti, kteří by měli dostávat e-mailová oznámení při přijetí nového zájemce. Více e-mailových adres můžete zadat tak, že je oddělíte středníkem.

1. Vyberte **OK**.

   Chcete-li se ujistit, že jste úspěšně připojeni k cíli zájemce, klikněte na tlačítko **ověřit** . V případě úspěchu budete mít vedoucího testu v cíli realizace.

   ![Místní okno s podrobnostmi připojení](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-connection-details.png)
