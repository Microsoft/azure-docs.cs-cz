---
title: Konfigurace správy zájemců pro Salesforce | Azure Marketplace
description: Nakonfigurujte správu zájemců na Salesforce pro zákazníky Azure Marketplace.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: evansma
ms.openlocfilehash: d941a7902dd374a931147bdaa8f015c46268ee8a
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69901984"
---
# <a name="configure-lead-management-for-salesforce"></a>Konfigurace správy zájemců pro Salesforce

Tento článek popisuje, jak nastavit systém Salesforce pro zpracování potenciálních zákazníků z vaší nabídky na webu Marketplace.

## <a name="set-up-your-salesforce-system"></a>Nastavení systému Salesforce

1. Přihlaste se k Salesforce.
2. Pokud používáte osvětlovací prostředí Salesforce.
    1. Na domovské stránce Salesforce vyberte **Nastavení** .
    ![Nastavení Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. Na stránce nastavení přejděte přes levou navigaci k **nástrojům platforem – > nastavení funkcí – > marketingu > Web-to-zájemce**.
    ![Web Salesforce – zájemce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

3. Pokud používáte rozhraní Salesforce Classic:
    1. Na domovské stránce Salesforce vyberte **Nastavení** .
    ![Nastavení služby Salesforce Classic](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. Na stránce nastavení přejděte pomocí levé navigace k sestavování **-> přizpůsobení-> potenciální zákazníky – > z webu na potenciálního zákazníka**.
    ![Klasický Web-to-vedoucí pro Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

Ostatní pokyny jsou stejné bez ohledu na to, jaké prostředí Salesforce používáte.

4. Na **stránce nastavení webu na základě potenciálního zákazníka**vyberte tlačítko **vytvořit formulář na webu pro** pořízení.
5. Při **instalaci z webu na potenciálního zákazníka**vyberte **vytvořit webový formulář pro**vyřízení.
    ![Salesforce – instalace z webu na potenciálního zákazníka](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

6. Na **formuláři vytvořit web do zájemce**se ujistěte, že `the Include reCAPTCHA in HTML` nastavení není zaškrtnuté a vyberte **Generovat**. 
    ![Salesforce – vytvoření formuláře z webu na potenciálního zákazníka](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

7. Zobrazí se text HTML. Vyhledejte text "OID" a zkopírujte **hodnotu OID** z textu HTML (jenom text mezi uvozovkami) a uložte ho. Tuto hodnotu vložíte do pole **identifikátor organizace** na portálu pro publikování.
    ![Salesforce – vytvoření formuláře z webu na potenciálního zákazníka](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

8. Vybráno **dokončeno**.

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>Konfigurace nabídky pro odesílání zájemců do Salesforce

Až budete připraveni ke konfiguraci informací o správě zájemce pro vaši nabídku na portálu pro publikování, postupujte podle následujících kroků:

1. Přejděte na stránku **nastavení nabídky** pro vaši nabídku.
1. V části Správa zájemců vyberte **připojit** .
    ![Správa vedoucích – připojení](./media/commercial-marketplace-lead-management-instructions-salesforce/lead-management-connect.png)

1. V automaticky otevíraném okně s podrobnostmi o připojení vyberte **Salesforce** pro **cíl zájemce** a `oid` vložte ho do pole od webového-vedoucího formuláře, který jste vytvořili pomocí předchozích kroků v poli **identifikátor organizace** .

1. Vyberte **Uložit**. 

    >[!Note]
    >Musíte dokončit konfiguraci zbytku nabídky a publikovat ji předtím, než budete moct získat zájemce pro tuto nabídku.

    ![Podrobnosti o připojení – volba cíle zájemce](./media/commercial-marketplace-lead-management-instructions-salesforce/choose-lead-destination.png)

    ![Podrobnosti o připojení – volba cíle zájemce](./media/commercial-marketplace-lead-management-instructions-salesforce/connection-details.png)