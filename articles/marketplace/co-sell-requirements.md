---
title: Požadavky na společný prodej | Azure Marketplace
description: Seznamte se s požadavky, které nabídka na komerčním tržišti společnosti Microsoft musí splňovat, aby bylo možné využít motivovaní stav připravený pro spoluprodejní nebo společný prodej.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.date: 2/24/2021
ms.openlocfilehash: 9fb86b3d4a46a70d71aa0c3fe9fd1b4a1ea6f298
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102120126"
---
# <a name="co-sell-requirements"></a>Požadavky na společný prodej

Tento článek popisuje požadavky na různé úrovně stavu společného prodeje. Nejnovější seznam typů nabídek, které podporují společný prodej, najdete v tématu  [Konfigurace společného prodeje pro nabídku komerčního tržiště](commercial-marketplace-co-sell.md). Přehled společného prodeje najdete v tématu [společný prodej s prodejními týmy a partnery společnosti Microsoft](marketplace-co-sell.md).

V této tabulce jsou uvedené všechny možné stavy spoluprodeji:

| Status | Komentář |
| ------------ | ------------- |
| Není připravený společný prodej | Nebyly splněny minimální [požadavky na stav připravenosti na společný prodej](#requirements-for-co-sell-ready-status) . |
| Připravený společný prodej | Splnění všech [požadavků na stav připravenosti na společný prodej](#requirements-for-co-sell-ready-status) bylo dosaženo. |
| Azure IP – společný prodej motivovaní | Kromě [těchto dalších požadavků](#requirements-for-ip-co-sell-incentivized-status)se splnily požadavky na společný prodej. |
| Aplikace pro výrobce ISV Connect Premium  | Tento stav platí pro nabídky Dynamics 365 a Power Apps a označuje, že byly splněny všechny [požadavky pro tento stav](#requirements-for-biz-apps-isv-connect-premium-incentive-status) . |
|||

## <a name="requirements-for-co-sell-ready-status"></a>Požadavky na společný prodej – stav připravenosti

Aby bylo možné dosáhnout stavu připraveného pro společný prodej, je nutné splnit následující požadavky:

**Všichni partneři**:

- Mít [v partnerském centru ID MPN a aktivní účet komerčního tržiště](./partner-center-portal/create-account.md).
- Ujistěte se, že máte v partnerském centru kompletní [obchodní profil](/partner-center/create-a-marketing-profile.md) . Jako kvalifikovaný partner Microsoftu vám váš obchodní profil pomůže prezentovat vaši firmu zákazníkům, kteří hledají vaše jedinečná řešení a odbornosti, aby vyhledali své obchodní potřeby. výsledkem jsou [referenčníky](/partner-center/referrals.md).
- Dokončete **společný prodej pomocí karty Microsoft** a publikujte nabídku na komerčním webu Marketplace.
- Zadejte prodejní kontakt pro každý společný prodej s nárokem na geografickou oblast a požadované náklady na vyúčtování.

**Partneři služeb**:

- V případě nabídek typu _řešení služby_ musíte mít aktivní Gold Competency v jakékoli oblasti kompetence.
 
**Business Applications nezávislý výrobce softwaru**:

- Dynamics 365 Customer Engagement & PowerApps a Dynamics 365 finance & Ops (s výjimkou Dynamics 365 Business Central) a PowerApps řešení vyžadují registraci ISV Connect.

### <a name="complete-the-co-sell-with-microsoft-tab"></a>Dokončete společný prodej pomocí karty Microsoftu.

Při publikování nebo aktualizaci vaší nabídky Poskytněte všechny požadované informace na kartě **společný prodej s Microsoftem** , jak je podrobně popsáno v části [Konfigurace společného prodeje pro nabídku komerčního tržiště](commercial-marketplace-co-sell.md). To zahrnuje následující dokumenty:

- Řešení/nabídka – jeden – pager
- Balíček řešení nebo nabídky pro rozteč

Poskytujeme šablony, které vám pomůžou tyto dokumenty vytvořit. Další informace o požadovaných a volitelných informacích o společném prodeji na kartě Microsoft najdete v tématu [Konfigurace společného prodeje pro nabídku komerčního tržiště](commercial-marketplace-co-sell.md).

### <a name="publish-your-offer-live"></a>Publikování vaší nabídky živě

Aby bylo možné získat nárok na stav připraveného pro společný prodej, musí být vaše nabídka nebo řešení publikována živě na nejméně jednom z online obchodů pro komerční tržiště: Azure Marketplace nebo Microsoft AppSource. Informace o publikování nabídek na komerčním webu Marketplace najdete v tématu [Průvodce publikováním podle typu nabídky](publisher-guide-by-offer-type.md). Pokud jste nabídku ještě nepublikovali na komerčním webu Marketplace, ujistěte se, že máte [účet komerčního tržiště](./partner-center-portal/create-account.md).

## <a name="requirements-for-ip-co-sell-incentivized-status"></a>Požadavky na společný prodej motivovaní stav

Motivovaní stav společného prodeje Azure IP se vztahuje na následující typy nabídek:

- Azure Application
- Kontejner Azure
- Virtuální počítač Azure
- Modul IoT Edge
- Software jako služba (SaaS)

Po dosažení stavu připravenosti na společný prodej jsou k dispozici tři další požadavky pro zajištění motivovaní stavu společného prodeje Azure IP:

Požadavek 1 – dosažení následujících možností:

- Na _úrovni organizace_ vygenerujte za uplynutí 12 měsíců minimální prahovou hodnotu výnosů Azure spotřebovaných za $100 000 USD. To se dá získat kombinací řešení Azure. Pokud je nabídka transakční na komerčním webu Marketplace, můžete tento požadavek splnit tím, že splníte prahovou hodnotu fakturovaného výnosu $100 000 USD.

Požadavek 2 – předání technického ověření Microsoftu pro řešení založené na Azure:
- Technické ověření musí potvrdit, že více než 50% infrastruktury vaší nabídky používá opakovaně používaný kód IP v Azure. Upozorňujeme, že virtuální počítače Azure a řešení Azure pro aplikace na komerčním tržišti budou standardně splňovat tento požadavek.

Požadavek 3 – poskytnutí referenčního diagramu architektury:
- Nahrajte diagram referenční architektury se svými společnými dokumenty v partnerském centru pro kontrolu. Pokyny k vytvoření tohoto diagramu najdete v tématu [diagram referenční architektury](reference-architecture-diagram.md). Informace o nahrávání diagramu najdete v tématu věnovaném [konfiguraci společného prodeje pro nabídku komerčního tržiště](commercial-marketplace-co-sell.md).

## <a name="requirements-for-biz-apps-isv-connect-premium-incentive-status"></a>Požadavky na aplikace pro výrobce ISV Connect status Premium

Tento stav platí pro řešení založená na protokolu IP, aplikace a služby postavené na Dynamics 365 nebo v aplikacích Power.

Nemusíte dosahovat stav připravenosti na společný prodej (uvedený výše), abyste mohli využívat aplikace \ prodejce ISV Connect Standard motivační. Ale jakmile aplikace dosáhne stavu připraveného pro společný prodej, může se zvážit pro aplikace \ prodejce ISV připojit stav Premium motivační na základě posledních 12 měsíců podílu výnosů a společného prodeje výsledků nebo prahových hodnot.

Požadavek – musíte mít aktivní registraci na úrovni Premium [ISV Connect](business-applications-isv-program.md) .

## <a name="next-steps"></a>Další kroky

- [Konfigurace společného prodeje pro komerční nabídku na webu Marketplace](commercial-marketplace-co-sell.md)
