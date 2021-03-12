---
title: Vytvořte nabídku virtuálního počítače na Azure Marketplace.
description: Naučte se, jak vytvořit nabídku virtuálních počítačů na komerčním webu Microsoft Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 10/20/2020
ms.openlocfilehash: 5c01b1965c38811e78909f27ca4ab65614bc7f0e
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "102629972"
---
# <a name="how-to-create-a-virtual-machine-offer-on-azure-marketplace"></a>Postup vytvoření nabídky virtuálního počítače na Azure Marketplace

Tento článek popisuje, jak vytvořit nabídku virtuálních počítačů Azure pro [Azure Marketplace](https://azuremarketplace.microsoft.com/). Řeší virtuální počítače založené na Windows i Linux, které obsahují operační systém, virtuální pevný disk (VHD) a až 16 datových disků.

Než začnete, [Vytvořte si účet pro komerční tržiště v partnerském centru](partner-center-portal/create-account.md). Ujistěte se, že je váš účet zaregistrovaný v programu komerčního tržiště.

## <a name="before-you-begin"></a>Než začnete

Pokud jste to ještě neudělali, přečtěte si téma [plánování nabídky virtuálních počítačů](marketplace-virtual-machines.md). Vysvětlete technické požadavky na váš virtuální počítač a seznam informací a prostředků, které budete potřebovat při vytváření vaší nabídky.

## <a name="create-a-new-offer"></a>Vytvoření nové nabídky

1. Přihlaste se k [partnerskému centru](https://partner.microsoft.com/dashboard/home).
2. V levém podokně vyberte přehled **komerčního tržiště**  >  .
3. Na stránce **Přehled** vyberte **+ Nová nabídka**  >  **virtuální počítač Azure**.

    ![Snímek obrazovky s možnostmi levého podokna v nabídce a tlačítkem Nová nabídka](./media/create-vm/new-offer-azure-virtual-machine.png)

> [!NOTE]
> Po publikování vaší nabídky se všechny úpravy, které provedete v partnerském centru, zobrazí Azure Marketplace až po opětovném publikování nabídky. Nezapomeňte vždy znovu publikovat nabídku po provedení změn.

Zadejte **ID nabídky**. Toto je jedinečný identifikátor každé nabídky ve vašem účtu.

- Toto ID je viditelné pro zákazníky na webové adrese pro nabídku Azure Marketplace a v Azure PowerShell a v Azure CLI, pokud je to možné.
- Použijte při tom jenom malá písmena a číslice. ID může zahrnovat pomlčky a podtržítka, ale ne mezery a je omezeno na 50 znaků. Pokud například zadáte **příkaz Test-nabídky-1**, Webová adresa nabídky bude `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- ID nabídky nelze změnit po výběru možnost **vytvořit**.

Zadejte **alias nabídky**. Alias nabídky je název, který se používá pro nabídku v partnerském centru.

- Tento název se nepoužívá na Azure Marketplace. Liší se od názvu nabídky a dalších hodnot, které se zákazníkům zobrazují.

Vyberte **vytvořit** pro vygenerování nabídky a pokračování. Partnerské centrum otevře stránku **nastavení nabídky** .

## <a name="enable-a-test-drive-optional"></a>Povolit testovací jednotku (volitelné)

Testovací jednotka představuje skvělý způsob, jak předprezentovat vaši nabídku potenciálním zákazníkům tím, že jim poskytne přístup k předkonfigurovanému prostředí po dobu určitého počtu hodin. Nabídka testovacích jednotek má za následek vyšší míru převodu a generuje vysoce kvalifikované potenciální zákazníky. Další informace o testovacích jednotkách najdete v tématu [co je testovací jednotka?](./what-is-test-drive.md).

> [!TIP]
> Testovací jednotka se liší od bezplatné zkušební verze. Můžete nabízet buď testovací, bezplatnou zkušební verzi, nebo obojí. Poskytují zákazníkům s vaším řešením pevně stanovené časové období. Testovací jednotka ale taky obsahuje praktickou a samoobslužnou prohlídku klíčových funkcí vašeho produktu a výhod, které se provedou ve scénáři implementace reálného světa.

Chcete-li povolit testovací jednotku, zaškrtněte políčko **Povolit testovací jednotku** . Testovací jednotku budete konfigurovat později. V případě testovacích jednotek se vyžaduje konfigurace CRM (viz další oddíl).

## <a name="configure-customer-leads-management"></a>Konfigurace správy zájemců zákazníků

Když publikujete nabídku na komerční tržišti s partnerským centrem, připojte ji k systému pro správu vztahů se zákazníky (CRM). To vám umožní získat kontaktní údaje zákazníka, jakmile někdo vyjádří zájem nebo použije váš produkt. Pokud chcete povolit testovací jednotku (viz předchozí část), vyžaduje se připojení k CRM. V opačném případě je připojení k CRM volitelné.

1. V části **zájemci na zákazníky** vyberte odkaz **připojit** .
1. V dialogovém okně **Podrobnosti připojení** vyberte cíl zájemce.
1. Vyplňte pole, která se zobrazí. Podrobný postup najdete v následujících článcích:

   - [Konfigurace nabídky pro odesílání zájemců do tabulky Azure](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [Konfigurace nabídky pro odeslání zájemců do dynamics 365 Customer Engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement) (dřív Dynamics CRM Online)
   - [Konfigurace nabídky k odeslání potenciálních zákazníků do koncového bodu HTTPS](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [Konfigurace nabídky pro odesílání zájemců do markety](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [Konfigurace nabídky pro odesílání zájemců do Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. Pokud chcete ověřit zadanou konfiguraci, vyberte odkaz **ověřit** .
1. Vyberte **Connect** (Připojit).

Než budete pokračovat na další kartu v nabídce vlevo na **Další, vyberte** **Uložit koncept** .

## <a name="next-steps"></a>Další kroky

- [Postup konfigurace vlastností nabídky virtuálního počítače](azure-vm-create-properties.md)
- [Osvědčené postupy nabídky](gtm-offer-listing-best-practices.md)