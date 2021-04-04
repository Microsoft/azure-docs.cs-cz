---
title: Postup vytvoření nabídky konzultační služby na komerčním webu Microsoft Marketplace
description: Naučte se, jak vytvořit novou nabídku konzultační služby pro Microsoft AppSource nebo Azure Marketplace pomocí programu komerčního tržiště v partnerském centru Microsoftu.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 10/27/2020
ms.openlocfilehash: e9a0b2fe883fa46010fda74c58908128d05919e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92754378"
---
# <a name="how-to-create-a-consulting-service-offer-in-the-commercial-marketplace"></a>Jak vytvořit nabídku konzultační služby na komerčním webu Marketplace

Tento článek vysvětluje, jak vytvořit konzultační nabídku služeb pro komerční tržiště Microsoftu pomocí partnerského centra. 

## <a name="before-you-begin"></a>Než začnete

Pokud chcete publikovat konzultační nabídku služeb, musíte splnit určité požadavky na způsobilost, abyste mohli Ukázat odbornosti v poli. Pokud jste to ještě neudělali, přečtěte si téma [Naplánování nabídky konzultační služby pro komerční tržiště](./plan-consulting-service-offer.md). Popisuje požadavky na konzultační službu a assety, které budete potřebovat při vytváření nabídky s partnerským centrem.

## <a name="create-a-new-consulting-service-offer"></a>Vytvořit novou nabídku konzultační služby

1. Přihlaste se k [partnerskému centru](https://partner.microsoft.com/dashboard/home).
2.  V navigační nabídce vlevo vyberte **obchodní Marketplace**  >  **Přehled**.
3.  Na kartě Přehled vyberte **+ Nová nabídka**  >  **konzultační služba**.

    ![Znázorňuje levou navigační nabídku.](./media/new-offer-consulting-service.png)

4. V dialogovém okně **Nová nabídka** zadejte **ID nabídky**. Toto ID se zobrazuje v adrese URL výpisu komerčního tržiště. Pokud například zadáte do tohoto pole test-nabídka-1, bude webová adresa nabídky `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .

    * Každá nabídka ve vašem účtu musí mít jedinečné ID nabídky.
    * Použijte při tom jenom malá písmena a číslice. ID nabídky může zahrnovat pomlčky a podtržítka, ale ne mezery a je omezeno na 50 znaků.
    * ID nabídky nelze změnit po výběru možnost **vytvořit**.

5. Zadejte **alias nabídky**. Toto je název, který se používá pro nabídku v partnerském centru. Není viditelný v online obchodech a liší se od názvu nabídky zobrazeného pro zákazníky.
6. Pokud chcete vygenerovat nabídku a pokračovat, vyberte **vytvořit**.

## <a name="configure-lead-management"></a>Konfigurace správy potenciálních zákazníků

Připojte svůj systém pro řízení vztahů se zákazníky (CRM) pomocí komerční nabídky na webu Marketplace, abyste mohli dostávat kontaktní údaje zákazníka, když zákazník vyjádří zájem o konzultační službu. Toto připojení můžete kdykoli změnit během nebo po vytvoření nabídky. Podrobné pokyny najdete v [zákaznických zákaznících z nabídky komerčních webů na webu Marketplace](./partner-center-portal/commercial-marketplace-get-customer-leads.md).

Konfigurace správy zájemců v partnerském centru:

1.  V partnerském centru, navštivte kartu **nastavení nabídky** .
2.  V části **zájemci na zákazníky** vyberte odkaz **připojit** .
3.  V dialogovém okně **Podrobnosti připojení** vyberte v seznamu cíl zájemce.
4.  Vyplňte pole, která se zobrazí. Podrobný postup najdete v následujících článcích:

    * [Konfigurace nabídky pro odesílání zájemců do tabulky Azure](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
    * [Konfigurace nabídky pro odeslání zájemců do dynamics 365 Customer Engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (dřív Dynamics CRM Online)
    * [Konfigurace nabídky k odeslání potenciálních zákazníků do koncového bodu HTTPS](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
    * [Konfigurace nabídky pro odesílání zájemců do markety](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
    * [Konfigurace nabídky pro odesílání zájemců do Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

5.  Pokud chcete ověřit zadanou konfiguraci, vyberte **odkaz ověřit**.
6.  Po nakonfigurování podrobností připojení vyberte **připojit**.
7.  Vyberte **Uložit koncept**.

Po odeslání nabídky pro publikování v partnerském centru ověříme připojení a pošleme vám testovacího vedoucího. Když si nabídku předcházíte, než začnete fungovat, otestujte své připojení k vašemu zájemce tím, že se pokusíte nabídku koupit v prostředí verze Preview.

> [!TIP]
> Ujistěte se, že připojení k cíli realizace zůstane aktualizované, takže nepřijdete o žádné zájemce.

## <a name="next-steps"></a>Další kroky

* [Postup konfigurace vlastností nabídek konzultačních služeb](./create-consulting-service-offer-properties.md)