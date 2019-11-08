---
title: Konfigurace správy potenciálních zákazníků ve Marketě | Azure Marketplace
description: Nakonfigurujte řízení správy pro zákazníky na webu Marketo pro zákazníky Azure Marketplace.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: evansma
ms.openlocfilehash: 8d13e8c3aeabf6d3fdea80ffddbae47b80adc139
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73812138"
---
# <a name="configure-lead-management-in-marketo"></a>Konfigurace správy potenciálních zákazníků ve Marketě

Tento článek popisuje, jak nastavit svůj systém Marketo pro CRM na zpracování potenciálních zákazníků z nabídky na webu Marketplace.

## <a name="set-up-your-marketo-crm-system"></a>Nastavení systému Marketo CRM

1. Přihlaste se k Marketo.
2. Vyberte **návrh Studio**.
    ![Market Design Studio](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

3.  Vyberte **nový formulář**.
    nový formulář ![Market](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

4.  Vyplňte požadovaná pole v novém formuláři a pak vyberte **vytvořit**.
    ![Marketo vytvořit nový formulář](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

5.  V poli Podrobnosti o poli vyberte **Dokončit**.
    ![formulář dokončení Market](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

6.  Schvalte a zavřete.

7. Na kartě *MarketplaceLeadBacked* vyberte **Vložit kód**. 

    ![Kód pro vložení](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

8. Kód pro vložení do markety zobrazí kód podobný následujícímu příkladu.

    ```
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

9. Zkopírujte hodnoty níže uvedených polí zobrazených ve formuláři kód pro vložení. Tyto hodnoty použijete ke konfiguraci nabídky pro příjem zájemců v dalším kroku. Následující příklad slouží jako vodítko pro získání potřebných ID z příkladu kódu pro vložení do Marketo.

    - ID serveru = **ys12**
    - Munchkin ID = **123-PQR-789**
    - ID formuláře = **1179**

    **Další způsob, jak tyto hodnoty zjistit**

    - ID serveru najdete v adrese URL vaší instance služby Marketo, například "`serverID.marketo.com`".
    - Munching ID vašeho předplatného, a to tak, že v poli > pro ID účtu Munchkin Munchkin na REST API a z první části vaší subdomény hostitele Marketo: `https://{Munchkin ID}.mktorest.com`.
    - ID formuláře je ID formuláře pro vložení kódu, který jste vytvořili v kroku 7 ke směrování potenciálních zákazníků z našeho webu Marketplace.

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>Konfigurace nabídky pro odesílání zájemců do markety

Až budete připraveni ke konfiguraci informací o správě zájemce pro vaši nabídku na portálu pro publikování, postupujte podle následujících kroků: 

1. Přejděte na stránku **nastavení nabídky** pro vaši nabídku.
1. V části Správa zájemců vyberte **připojit** . 

    ![Správa vedoucích – připojení](./media/commercial-marketplace-lead-management-instructions-marketo/lead-management-connect.png)

1. V místním okně Podrobnosti připojení vyberte **Marketo** pro cíl zájemce.

    ![Zvolit cíl zájemce](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

4. Zadejte **ID serveru**, **ID účtu Munching**a **ID formuláře**.

    >[!Note]
    >Musíte dokončit konfiguraci zbytku nabídky a publikovat ji předtím, než budete moct získat zájemce pro tuto nabídku. 

