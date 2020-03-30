---
title: Konfigurace správy zájemců pro službu Salesforce | Azure Marketplace
description: Nakonfigurujte správu potenciálních zákazníků salesforce pro zákazníky Azure Marketplace.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: dsindona
ms.openlocfilehash: 1720026b4beff941b02a60cd1c755a043d66bdb5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281489"
---
# <a name="configure-lead-management-for-salesforce"></a>Konfigurace správy zájemců pro službu Salesforce

Tento článek popisuje, jak nastavit systém Salesforce pro zpracování prodejních zájemců z nabídky marketplace.

## <a name="set-up-your-salesforce-system"></a>Nastavení systému Salesforce

1. Přihlaste se ke sazím Salesforce.
2. Pokud používáte prostředí osvětlení Salesforce.
    1. Vyberte **Nastavení** z domovské stránky Služby Salesforce.
    ![Nastavení salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. Na stránce Instalace přejděte levým navigačním programem na **platformu Nástroje >Nastavení funkcí >Marketing->Web-to-Lead**.
    ![Web společnosti Salesforce vedoucí](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

3. Pokud používáte prostředí Salesforce Classic:
    1. Vyberte **Nastavení** z domovské stránky Služby Salesforce.
    ![Klasické nastavení Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. Na stránce Nastavení přejděte přes navigaci vlevo na **build ->Customize->zájemci >web-to-lead**.
    ![Salesforce klasický web-to-lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

Zbývající pokyny jsou stejné bez ohledu na to, jaké zkušenosti se službou Salesforce používáte.

4. Na **stránce Nastavení web-zájemce**vyberte tlačítko **Vytvořit formulář z webu na zájemce.**
5. V **nastavení Web-to-Lead**vyberte **možnost Vytvořit formulář Web-to-Lead**.
    ![Salesforce - Nastavení web-to-lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

6. Ve **formuláři Vytvořit web-zájemce**zkontrolujte, zda `the Include reCAPTCHA in HTML` není zaškrtnuto nastavení, a vyberte **generovat**. 
    ![Salesforce - Vytvoření formuláře web-to-lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

7. Zobrazí se text HTML. Vyhledejte text "oid" a zkopírujte **hodnotu oid** z textu HTML (pouze text mezi uvozovkami) a uložte jej. Tuto hodnotu vložíte do pole **Identifikátor organizace** na portálu pro publikování.
    ![Salesforce - Vytvoření formuláře web-to-lead](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

8. Vybrané **dokončeno**.

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>Konfigurace nabídky pro odesílání zájemců do služby Salesforce

Až budete připraveni nakonfigurovat informace o správě zájemců pro vaši nabídku na portálu pro publikování, postupujte podle následujících kroků:

1. Přejděte na stránku **Nastavení nabídky** pro vaši nabídku.
1. V části Správa zájemců vyberte **Připojit.**
    ![Správa potenciálních zákazníků - Připojení](./media/commercial-marketplace-lead-management-instructions-salesforce/lead-management-connect.png)

1. V rozbalovacím okně Podrobnosti o připojení vyberte **salesforce** pro **cíl zájemce** a vložte do formuláře `oid` od webu k zájemce, který jste vytvořili, a to podle předchozích kroků do pole identifikátor **organizace.**

1. Vyberte **Uložit**. 

    >[!Note]
    >Před přijetím zájemců o nabídku je nutné dokončit konfiguraci zbývající nabídky a publikovat ji.

    ![Podrobnosti o připojení – výběr cíle zájemce](./media/commercial-marketplace-lead-management-instructions-salesforce/choose-lead-destination.png)

    ![Podrobnosti o připojení – výběr cíle zájemce](./media/commercial-marketplace-lead-management-instructions-salesforce/connection-details.png)
