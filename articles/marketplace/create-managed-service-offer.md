---
title: Jak vytvořit nabídku spravované služby na komerčním webu Microsoft Marketplace
description: Naučte se, jak vytvořit novou nabídku spravované služby pro Azure Marketplace pomocí programu komerčního obchodu na webu Microsoft Partner Center.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 12/23/2020
ms.openlocfilehash: 46a9c9d953a2311d83b5fd18b83727d6765734fa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97918162"
---
# <a name="how-to-create-a-managed-service-offer-for-the-commercial-marketplace"></a>Jak vytvořit nabídku spravované služby pro komerční tržiště

Tento článek vysvětluje, jak vytvořit nabídku spravované služby pro komerční tržiště Microsoftu pomocí partnerského centra.

Pokud chcete publikovat nabídku spravované služby, musíte mít na cloudové platformě vytvořenou zlatou nebo stříbrné kompetence Microsoftu. Pokud jste to ještě neudělali, přečtěte si téma [plánování nabídky spravované služby pro komerční tržišti](./plan-managed-service-offer.md). Při vytváření nabídky v partnerském centru vám pomůže připravit assety, které potřebujete.

## <a name="create-a-new-offer"></a>Vytvoření nové nabídky

1. Přihlaste se k [partnerskému centru](https://partner.microsoft.com/dashboard/home).
2. V navigační nabídce vlevo vyberte **obchodní Marketplace**  >  **Přehled**.
3. Na kartě Přehled vyberte **+ Nová nabídka**  >  **spravovaná služba**.

:::image type="content" source="./media/new-offer-managed-service.png" alt-text="Znázorňuje levou navigační nabídku.":::

4. V dialogovém okně **Nová nabídka** zadejte **ID nabídky**. Toto je jedinečný identifikátor každé nabídky ve vašem účtu. Toto ID se zobrazí v adrese URL seznamu komerčního tržiště a v případě potřeby šablon Azure Resource Manager. Pokud například zadáte do tohoto pole test-nabídka-1, bude webová adresa nabídky `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .

    * Každá nabídka ve vašem účtu musí mít jedinečné ID nabídky.
    * Použijte při tom jenom malá písmena a číslice. Může zahrnovat pomlčky a podtržítka, ale ne mezery a je omezen na 50 znaků.
    * ID nabídky nelze změnit po výběru možnost **vytvořit**.

5. Zadejte **alias nabídky**. Toto je název, který se používá pro nabídku v partnerském centru. Není viditelný v online obchodech a liší se od názvu nabídky zobrazeného pro zákazníky.
6. Pokud chcete vygenerovat nabídku a pokračovat, vyberte **vytvořit**.

## <a name="configure-lead-management"></a>Konfigurace správy potenciálních zákazníků

Připojte svůj systém pro řízení vztahů se zákazníky (CRM) pomocí komerční nabídky na webu Marketplace, abyste mohli dostávat kontaktní údaje zákazníka, když zákazník vyjádří zájem o konzultační službu. Toto připojení můžete kdykoli změnit během nebo po vytvoření nabídky. Podrobné pokyny najdete v [zákaznických zákaznících z nabídky komerčních webů na webu Marketplace](./partner-center-portal/commercial-marketplace-get-customer-leads.md).

Konfigurace správy zájemců v partnerském centru:

1. V partnerském centru, navštivte kartu **nastavení nabídky** .
2. V části **zájemci na zákazníky** vyberte odkaz **připojit** .
3. V dialogovém okně **Podrobnosti připojení** vyberte v seznamu cíl zájemce.
4. Vyplňte pole, která se zobrazí. Podrobný postup najdete v následujících článcích:

    * [Konfigurace nabídky pro odesílání zájemců do tabulky Azure](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
    * [Konfigurace nabídky pro odeslání zájemců do dynamics 365 Customer Engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (dřív Dynamics CRM Online)
    * [Konfigurace nabídky k odeslání potenciálních zákazníků do koncového bodu HTTPS](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
    * [Konfigurace nabídky pro odesílání zájemců do markety](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
    * [Konfigurace nabídky pro odesílání zájemců do Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

5. Pokud chcete ověřit zadanou konfiguraci, vyberte **odkaz ověřit**.
6. Po nakonfigurování podrobností připojení vyberte **připojit**.
7. Vyberte **Uložit koncept**.

Po odeslání nabídky pro publikování v partnerském centru ověříme připojení a pošleme vám testovacího vedoucího. Když si nabídku předcházíte, než začnete fungovat, otestujte své připojení k vašemu zájemce tím, že se pokusíte nabídku koupit v prostředí verze Preview.

> [!TIP]
> Ujistěte se, že připojení k cíli realizace zůstane aktualizované, takže nepřijdete o žádné zájemce.

## <a name="configure-offer-properties"></a>Konfigurovat vlastnosti nabídky

Na stránce vlastnosti vaší nabídky v partnerském centru definujete kategorie použitelné pro vaši nabídku a právní smlouvy. Tyto informace zajistí, že se spravovaná služba na online obchodě zobrazí správně a nabídne se správné sadě zákazníků.

### <a name="select-a-category"></a>Vybrat kategorii

V části **kategorie** vyberte aspoň jednu a až pět kategorií, aby se vaše nabídka mohla seskupovat do odpovídajících oblastí pro hledání v obchodě na webu Marketplace.

### <a name="provide-terms-and-conditions"></a>Zadání podmínek a ujednání

V části **právní** zadejte podmínky a ujednání pro tuto nabídku. Před použitím této nabídky se zákazníci budou muset přijmout. Můžete taky zadat adresu URL, kde se můžou vaše podmínky a ujednání najít.

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="next-step"></a>Další krok

* [Konfigurace seznamu nabídek spravovaných služeb](./create-managed-service-offer-listing.md)