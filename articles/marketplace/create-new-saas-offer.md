---
title: Jak vytvořit nabídku SaaS na komerčním webu Microsoft Marketplace
description: Naučte se, jak vytvořit novou nabídku software jako službu (SaaS) pro výpis nebo prodej v Microsoft AppSource, Azure Marketplace nebo prostřednictvím programu poskytovatel cloudových řešení (CSP) pomocí komerčního portálu pro Marketplace v partnerském centru Microsoftu.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 03/19/2021
ms.openlocfilehash: f652858f22e26eb7167a0cc7a2cfb8ef4b600ace
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105047296"
---
# <a name="how-to-create-a-saas-offer-in-the-commercial-marketplace"></a>Jak vytvořit nabídku SaaS na komerčním webu Marketplace

Jako vydavatel komerčního tržiště můžete vytvořit nabídku software jako služba (SaaS), aby potenciální zákazníci mohli koupit vaše technické řešení založené na SaaS. Tento článek vysvětluje proces vytvoření SaaS nabídky pro komerční tržiště Microsoftu.

## <a name="before-you-begin"></a>Než začnete

Pokud jste to ještě neudělali, přečtěte si téma [plánování nabídky SaaS pro komerční web Marketplace](plan-saas-offer.md). Vysvětlete technické požadavky na vaši aplikaci SaaS a informace a materiály, které budete potřebovat při vytváření vaší nabídky. Pokud neplánujete publikovat jednoduchý seznam (možnost **kontakt mi** ) na komerčním webu Marketplace, musí vaše aplikace SaaS splňovat technické požadavky na ověřování.

> [!IMPORTANT]
> Doporučujeme vytvořit samostatnou nabídku pro vývoj/testování (DEV) a samostatnou produkci (PROD). Tento článek popisuje, jak vytvořit nabídku výr. Podrobnosti o vytvoření nabídky pro vývoj najdete v tématu [Vytvoření nabídky pro vývoj a testování](create-saas-dev-test-offer.md).

## <a name="create-a-new-saas-offer"></a>Vytvoření nové nabídky SaaS

1. Přihlaste se k [partnerskému centru](https://partner.microsoft.com/dashboard/home).
1. V navigační nabídce vlevo vyberte **obchodní Marketplace**  >  **Přehled**.
1. Na kartě **Přehled** vyberte **+ Nový**  >  **software nabídky jako službu**.

   :::image type="content" source="media/new-offer-saas.png" alt-text="Ukazuje navigační nabídku vlevo a seznam nových nabídek.":::

1. V dialogovém okně **Nová nabídka** zadejte **ID nabídky**. Toto ID se zobrazí v adrese URL seznamu komerčního tržiště a v případě potřeby šablon Azure Resource Manager. Pokud například zadáte do tohoto pole **test-nabídka-1** , bude webová adresa nabídky `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
   + Každá nabídka ve vašem účtu musí mít jedinečné ID nabídky.
   + Použijte při tom jenom malá písmena a číslice. Může zahrnovat pomlčky a podtržítka, ale ne mezery a je omezen na 50 znaků.
   + ID nabídky nelze změnit po výběru možnost **vytvořit**.

1. Zadejte **alias nabídky**. Toto je název, který se používá pro nabídku v partnerském centru.

   + Tento název není viditelný na komerčním webu Marketplace a liší se od názvu nabídky a dalších hodnot zobrazených zákazníkům.
   + Po zvolení možnost **vytvořit** se alias nabídky nedá změnit.
1. Pokud chcete vygenerovat nabídku a pokračovat, vyberte **vytvořit**.

## <a name="configure-your-saas-offer-setup-details"></a>Nakonfigurovat podrobnosti o nastavení nabídky SaaS

Na kartě **nastavení nabídky** v části **Podrobnosti o nastavení** zvolíte, jestli se má vaše nabídka prodávat prostřednictvím Microsoftu, nebo spravovat vaše transakce nezávisle. Nabídky prodávané prostřednictvím Microsoftu se označují jako _transakční nabídky_, což znamená, že Microsoft usnadňuje výměnu peněz za softwarovou licenci jménem vydavatele. Další informace o těchto možnostech najdete v tématu [Možnosti výpisu](plan-saas-offer.md#listing-options) a [Určení možnosti publikování](determine-your-listing-type.md).

1. Pokud chcete prodávat přes Microsoft a s námi usnadnit transakce, vyberte **Ano**. Pokračujte [v povolování testovacích jednotek](#enable-a-test-drive-optional).

1. Pokud chcete zobrazit seznam nabídek prostřednictvím komerčního tržiště a zpracovávat nezávisle, vyberte možnost **ne** a pak proveďte jednu z následujících akcí:
   + Pokud chcete poskytnout bezplatné předplatné pro vaši nabídku, vyberte **získat hned (zdarma)**. Potom do pole **Adresa URL nabídky** , která se zobrazí, zadejte adresu URL (počínaje *http* nebo *https*), kde zákazníci mohou získat zkušební verzi prostřednictvím [ověřování jedním kliknutím pomocí Azure Active Directory (Azure AD)](azure-ad-saas.md). Například, `https://contoso.com/saas-app`.
   + Pokud chcete poskytnout 30denní bezplatnou zkušební verzi, vyberte možnost **bezplatná** zkušební verze a potom v zobrazeném poli **Adresa URL zkušební verze** zadejte adresu URL (počínaje *http* nebo *https*), kde můžou zákazníci získat přístup k bezplatné zkušební verzi prostřednictvím [ověřování jedním kliknutím pomocí Azure Active Directory (Azure AD)](azure-ad-saas.md). Například, `https://contoso.com/trial/saas-app`.
   + Pokud chcete, aby se potenciální zákazníci mohli koupit nabídku, vyberte **kontaktovat mě**.

### <a name="enable-a-test-drive-optional"></a>Povolit testovací jednotku (volitelné)

Testovací jednotka představuje skvělý způsob, jak předprezentovat vaši nabídku potenciálním zákazníkům tím, že jim poskytne přístup k předkonfigurovanému prostředí po dobu určitého počtu hodin. Nabídka testovacích jednotek má za následek vyšší míru převodu a generuje vysoce kvalifikované potenciální zákazníky. Další informace o testovacích jednotkách najdete v tématu [co je testovací jednotka?](./what-is-test-drive.md).

> [!TIP]
> Testovací jednotka se liší od bezplatné zkušební verze. Můžete nabízet buď testovací, bezplatnou zkušební verzi, nebo obojí. Poskytují zákazníkům s vaším řešením pevně stanovené časové období. Testovací jednotka ale taky obsahuje praktickou a samoobslužnou prohlídku klíčových funkcí vašeho produktu a výhod, které se provedou ve scénáři implementace reálného světa.

**Povolení testovací jednotky**
1.  V části **testovací jednotka** zaškrtněte políčko **Povolit testovací jednotku** .
1.  V seznamu, který se zobrazí, vyberte typ testovacího disku.

### <a name="configure-lead-management"></a>Konfigurace správy potenciálních zákazníků

Připojte svůj systém pro řízení vztahů se zákazníky (CRM) pomocí komerční nabídky na webu Marketplace, abyste mohli dostávat kontaktní údaje zákazníka, když zákazník vyjádří zájem nebo nasadí váš produkt. Toto připojení můžete kdykoli změnit během nebo po vytvoření nabídky.

> [!NOTE]
> Pokud chcete nabídku prodávat přes společnost Microsoft nebo jste vybrali možnost **kontakt mi** , musíte nakonfigurovat správu potenciálních zákazníků. Podrobné pokyny najdete v [zákaznických zákaznících z nabídky komerčních webů na webu Marketplace](partner-center-portal/commercial-marketplace-get-customer-leads.md).

#### <a name="to-configure-the-connection-details-in-partner-center"></a>Konfigurace podrobností o připojení v partnerském centru

1.  V části **zájemci na zákazníky** vyberte odkaz **připojit** .
1. V dialogovém okně **Podrobnosti připojení** vyberte v seznamu cíl zájemce.
1. Vyplňte pole, která se zobrazí. Podrobný postup najdete v následujících článcích:

   - [Konfigurace nabídky pro odesílání zájemců do tabulky Azure](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [Konfigurace nabídky pro odeslání zájemců do dynamics 365 Customer Engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (dřív Dynamics CRM Online)
   - [Konfigurace nabídky k odeslání potenciálních zákazníků do koncového bodu HTTPS](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [Konfigurace nabídky pro odesílání zájemců do markety](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [Konfigurace nabídky pro odesílání zájemců do Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. Pokud chcete ověřit zadanou konfiguraci, vyberte odkaz **ověřit** .
1. Pokud chcete dialogové okno zavřít, vyberte **OK**.

## <a name="next-steps"></a>Další kroky

- [Jak nakonfigurovat vlastnosti nabídky SaaS](create-new-saas-offer-properties.md)