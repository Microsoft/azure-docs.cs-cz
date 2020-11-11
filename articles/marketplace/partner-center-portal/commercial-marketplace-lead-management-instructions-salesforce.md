---
title: Správa zájemců v Salesforce – Microsoft Commercial Marketplace
description: Naučte se používat Salesforce ke konfiguraci zájemců pro Microsoft AppSource a Azure Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 03/30/2020
ms.openlocfilehash: 73caf848ab5c6f8e973469066ce4612a075a52f5
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/11/2020
ms.locfileid: "94489314"
---
# <a name="configure-lead-management-for-salesforce"></a>Konfigurace správy zájemců pro Salesforce

Tento článek popisuje, jak nastavit systém Salesforce pro zpracování potenciálních zákazníků z nabídek v Microsoft AppSource a Azure Marketplace.

> [!NOTE]
> Azure Marketplace nepodporuje předem vyplněné seznamy, jako je například seznam hodnot pro pole **země** . Než budete pokračovat, ujistěte se, že nejsou nastavené žádné seznamy. Případně můžete pro příjem zájemců nakonfigurovat [koncový bod HTTPS](./commercial-marketplace-lead-management-instructions-https.md) nebo [tabulku Azure](./commercial-marketplace-lead-management-instructions-azure-table.md) .

## <a name="set-up-your-salesforce-system"></a>Nastavení systému Salesforce

1. Přihlaste se k Salesforce.
1. Přejděte k nastavení **webu-vedoucí** . 
    
    Pokud používáte možnosti osvětlení Salesforce
    1. Na domovské stránce Salesforce vyberte **Nastavení** .

       ![Nastavení Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. Na stránce **instalace** přejdete na **nástroje platformy**  >  **Nastavení funkce**  >  **Marketing**  >  **Web-to-zájemce**.

        ![Web Salesforce – zájemce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

    Pokud používáte rozhraní Salesforce Classic:

    1. Na domovské stránce Salesforce vyberte **Nastavení** .

       ![Nastavení služby Salesforce Classic](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. Na stránce **Nastavení** vyberte **sestavit**  >  **přizpůsobit**  >  **zájemce**  >  **Web-to-zájemce**.

        ![Klasický Web-to-vedoucí pro Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

   Zbývající kroky jsou pro obě prostředí Salesforce stejné.

1. Na stránce **Nastavení webu na základě potenciálního zákazníka** vyberte tlačítko **vytvořit formulář na webu pro** pořízení.
1. Při **instalaci z webu na potenciálního zákazníka** vyberte **vytvořit webový formulář pro** vyřízení.

    ![Nastavení webu Salesforce Web-to-zájemce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

1. V části **vytvořit webový formulář pro vyřízení** se ujistěte, že `Include reCAPTCHA in HTML` je nastavení vymazáno a vyberte **Generovat**.

    ![Podokno formuláře pro vytváření webů a zájemců Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

1. Zobrazí se text HTML. Vyhledejte text "OID" a zkopírujte **hodnotu "OID"** z textu HTML (jenom text mezi uvozovkami) a uložte ho. Tuto hodnotu vložíte do pole **identifikátor organizace** na portálu pro publikování.

    ![Salesforce – vytvoření webového formuláře, který zobrazuje hodnotu "OID" kódu HTML](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

1. Vyberte **dokončeno**.

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>Konfigurace nabídky pro odesílání zájemců do Salesforce

Až budete připraveni ke konfiguraci informací o správě zájemců pro vaši nabídku na portálu pro publikování, postupujte podle těchto kroků.

1. Přihlaste se k [partnerskému centru](https://partner.microsoft.com/dashboard/home).

1. Vyberte svou nabídku a pak navštivte kartu **nastavení nabídky** .

1. V části **zájemci pro zákazníky** vyberte **připojit**.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-salesforce/customer-leads.png" alt-text="Potenciální zákazníci":::

1. V automaticky otevíraném okně **Podrobnosti připojení** vyberte **Salesforce** pro **cíl zájemce** a vložte `oid` hodnotu z formuláře z webu do zájemce, který jste vytvořili v poli **identifikátor organizace** .

    ![Místní okno s podrobnostmi o připojení ověření e-mailového pole kontaktu](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-connection-details.png)

1. V části **kontaktní e-mail** zadejte e-mailové adresy pro lidi ve vaší společnosti, kteří by měli dostávat e-mailová oznámení při přijetí nového zájemce. Více e-mailů můžete zadat tak, že je oddělíte středníkem.

1. Vyberte **OK**.

Abyste se ujistili, že jste se úspěšně připojili k cíli zájemce, vyberte **ověřit**. V případě úspěchu budete mít vedoucího testu v cíli realizace.

>[!NOTE]
>Musíte dokončit konfiguraci zbytku nabídky a publikovat ji předtím, než budete moct získat zájemce pro tuto nabídku.
