---
title: Jak vytvořit nabídku aplikace Azure na komerčním webu Marketplace
description: Naučte se, jak vytvořit novou nabídku aplikací Azure pro výpis nebo prodej v Azure Marketplace nebo prostřednictvím programu Cloud Solution Provider (CSP) pomocí komerčního portálu pro Marketplace v partnerském centru Microsoftu.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 3cc5e5114b435965eee4aa096e5898538b0a56e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94370076"
---
# <a name="how-to-create-an-azure-application-offer-in-the-commercial-marketplace"></a>Jak vytvořit nabídku aplikace Azure na komerčním webu Marketplace

Jako vydavatel komerčního tržiště můžete vytvořit nabídku aplikací Azure, aby potenciální zákazníci mohli koupit vaše řešení. Tento článek vysvětluje proces vytvoření nabídky aplikací Azure pro komerční tržiště Microsoftu.

Pokud jste to ještě neudělali, přečtěte si téma [plánování nabídky aplikací Azure pro komerční tržiště](plan-azure-application-offer.md). Poskytne vám prostředky a pomůže vám shromáždit informace a prostředky, které budete potřebovat při vytváření vaší nabídky.

## <a name="create-a-new-offer"></a>Vytvoření nové nabídky

1. Přihlaste se k [partnerskému centru](https://partner.microsoft.com/dashboard/home).

1. V navigační nabídce vlevo vyberte **obchodní Marketplace**  >  **Přehled**.

1. Na stránce Přehled vyberte **+ Nová nabídka**  >  **aplikace Azure**.

    ![Znázorňuje levou navigační nabídku.](./media/create-new-azure-app-offer/new-offer-azure-app.png)

1. V dialogovém okně **Nová nabídka** zadejte **ID nabídky**. Toto je jedinečný identifikátor každé nabídky ve vašem účtu. Toto ID se zobrazí v adrese URL seznamu komerčního tržiště a v případě potřeby šablon Azure Resource Manager. Pokud například zadáte do tohoto pole test-nabídka-1, bude webová adresa nabídky `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .

     * Každá nabídka ve vašem účtu musí mít jedinečné ID nabídky.
     * Použijte při tom jenom malá písmena a číslice. Může zahrnovat pomlčky a podtržítka, ale ne mezery a je omezen na 50 znaků.
     * ID nabídky nelze změnit po výběru možnost **vytvořit**.

1. Zadejte **alias nabídky**. Toto je název, který se používá pro nabídku v partnerském centru.

     * Tento název je viditelný jenom v partnerském centru a liší se od názvu nabídky a dalších hodnot, které se zákazníkům zobrazují.
     * Po zvolení možnost **vytvořit** se alias nabídky nedá změnit.

1. Pokud chcete vygenerovat nabídku a pokračovat, vyberte  **vytvořit**.

## <a name="configure-your-azure-application-offer-setup-details"></a>Konfigurovat podrobnosti o nastavení nabídek aplikací Azure

Na kartě **nastavení nabídky** v části **Podrobnosti o nastavení** vyberte, jestli se má nakonfigurovat testovací jednotka. Svůj systém pro správu vztahů se zákazníky (CRM) se připojíte i s vaší nabídkou komerčního tržiště.

### <a name="enable-a-test-drive-optional"></a>Povolit testovací jednotku (volitelné)

Testovací jednotka představuje skvělý způsob, jak předprezentovat vaši nabídku potenciálním zákazníkům tím, že jim poskytne přístup k předkonfigurovanému prostředí po dobu určitého počtu hodin. Nabídka testovacích jednotek má za následek vyšší míru převodu a generuje vysoce kvalifikované potenciální zákazníky. Další informace o testovacích jednotkách najdete v tématu [testovací jednotka](plan-azure-application-offer.md#test-drive).

#### <a name="to-enable-a-test-drive"></a>Povolení testovací jednotky

- V části **testovací jednotka** zaškrtněte políčko **Povolit testovací jednotku** .

### <a name="customer-lead-management"></a>Správa zákaznických zájemců

Připojte svůj systém pro řízení vztahů se zákazníky (CRM) pomocí komerční nabídky na webu Marketplace, abyste mohli dostávat kontaktní údaje zákazníka, když zákazník vyjádří zájem nebo nasadí váš produkt.

#### <a name="to-configure-the-connection-details-in-partner-center"></a>Konfigurace podrobností o připojení v partnerském centru

1. V části **zájemci na zákazníky** vyberte odkaz **připojit** .
1. V dialogovém okně **Podrobnosti připojení** vyberte v seznamu cíl zájemce.
1. Vyplňte pole, která se zobrazí. Podrobný postup najdete v následujících článcích:

   - [Konfigurace nabídky pro odesílání zájemců do tabulky Azure](partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [Konfigurace nabídky pro odeslání zájemců do dynamics 365 Customer Engagement](partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (dřív Dynamics CRM Online)
   - [Konfigurace nabídky k odeslání potenciálních zákazníků do koncového bodu HTTPS](partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [Konfigurace nabídky pro odesílání zájemců do markety](partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [Konfigurace nabídky pro odesílání zájemců do Salesforce](partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. Pokud chcete ověřit zadanou konfiguraci, vyberte odkaz pro **ověření** (Pokud je k dispozici).
1. Pokud chcete dialogové okno zavřít, vyberte **připojit**.
1. Před pokračováním na další kartu vyberte **Uložit koncept** : vlastnosti.

> [!NOTE]
> Ujistěte se, že připojení k cíli zájemce zůstane aktuální, takže nepřijdete o žádné zájemce. Nezapomeňte aktualizovat tato připojení vždy, když se něco změní.

## <a name="next-steps"></a>Další kroky

- [Jak nakonfigurovat vlastnosti nabídky aplikací Azure](create-new-azure-apps-offer-properties.md)
