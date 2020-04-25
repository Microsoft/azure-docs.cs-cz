---
title: Konfigurace správy zájemců pro Salesforce | Azure Marketplace
description: Nakonfigurujte správu zájemců na Salesforce pro zákazníky Azure Marketplace.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: e0fbb09370e198772b4fc485b3c0fe8a56da4226
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133635"
---
# <a name="configure-lead-management-for-salesforce"></a>Konfigurace správy zájemců pro Salesforce

Tento článek popisuje, jak nastavit systém Salesforce pro zpracování zájemců z komerční nabídky na webu Marketplace.

> [!NOTE]
> Azure Marketplace nepodporuje předem vyplněné seznamy, jako je například seznam hodnot pro pole **země** . Než budete pokračovat, ujistěte se, že nejsou nastavené žádné seznamy. Případně můžete pro příjem zájemců nakonfigurovat [koncový bod HTTPS](./commercial-marketplace-lead-management-instructions-https.md) nebo [tabulku Azure](./commercial-marketplace-lead-management-instructions-azure-table.md) .

## <a name="set-up-your-salesforce-system"></a>Nastavení systému Salesforce

1. Přihlaste se k Salesforce.
1. Pokud používáte možnosti osvětlení Salesforce:
    1. Na domovské stránce Salesforce vyberte **Nastavení** .

       ![Nastavení Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. Na stránce **instalace** přejdete na **nástroje** > platformy**Nastavení** > funkce**Marketing** > **Web-to-zájemce**.

        ![Web Salesforce – zájemce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

1. Pokud používáte rozhraní Salesforce Classic:

    1. Na domovské stránce Salesforce vyberte **Nastavení** .

       ![Nastavení služby Salesforce Classic](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. Na stránce **Nastavení** vyberte **sestavit** > **přizpůsobit** > **zájemce** > **Web-to-zájemce**.

        ![Klasický Web-to-vedoucí pro Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

Ostatní pokyny jsou stejné bez ohledu na to, jaké prostředí Salesforce používáte.

1. Na stránce **Nastavení webu na základě potenciálního zákazníka** vyberte tlačítko **vytvořit formulář na webu pro** pořízení.
1. Při **instalaci z webu na potenciálního zákazníka**vyberte **vytvořit webový formulář pro**vyřízení.

    ![Nastavení webu Salesforce Web-to-zájemce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

1. V části `Include reCAPTCHA in HTML` **vytvořit webový formulář pro vyřízení**se ujistěte, že je nastavení vymazáno a vyberte **Generovat**.

    ![Podokno formuláře pro vytváření webů a zájemců Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

1. Zobrazí se text HTML. Vyhledejte text "OID" a zkopírujte **hodnotu "OID"** z textu HTML (jenom text mezi uvozovkami) a uložte ho. Tuto hodnotu vložíte do pole **identifikátor organizace** na portálu pro publikování.

    ![Salesforce – vytvoření webového formuláře, který zobrazuje hodnotu "OID" kódu HTML](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

1. Vyberte **dokončeno**.

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>Konfigurace nabídky pro odesílání zájemců do Salesforce

Až budete připraveni ke konfiguraci informací o správě zájemců pro vaši nabídku na portálu pro publikování, postupujte podle těchto kroků.

1. Pro vaši nabídku přejdete na stránku **nastavení nabídek** .
1. V části **Správa zájemců** vyberte **připojit** .

    ![Tlačítko připojit k části Správa zájemců](./media/commercial-marketplace-lead-management-instructions-salesforce/lead-management-connect.png)

1. V automaticky otevíraném okně **Podrobnosti připojení** vyberte **Salesforce** pro **cíl zájemce** a vložte `oid` hodnotu z formuláře z webu do zájemce, který jste vytvořili v poli **identifikátor organizace** .

1. V části **kontaktní e-mail**zadejte e-mailové adresy pro lidi ve vaší společnosti, kteří by měli dostávat e-mailová oznámení při přijetí nového zájemce. Více e-mailů můžete zadat tak, že je oddělíte středníkem.

1. Vyberte **OK**.

Chcete-li se ujistit, že jste úspěšně připojeni k cíli zájemce, klikněte na tlačítko **ověřit** . V případě úspěchu budete mít vedoucího testu v cíli realizace.

>[!NOTE]
>Musíte dokončit konfiguraci zbytku nabídky a publikovat ji předtím, než budete moct získat zájemce pro tuto nabídku.

![Místní okno s podrobnostmi připojení zvolit cíl zájemce](./media/commercial-marketplace-lead-management-instructions-salesforce/choose-lead-destination.png)

![Místní okno s podrobnostmi o připojení ověření e-mailového pole kontaktu](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-connection-details.png)
