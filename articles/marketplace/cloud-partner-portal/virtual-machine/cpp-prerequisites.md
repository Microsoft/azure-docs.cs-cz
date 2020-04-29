---
title: Požadavky na virtuální počítač pro Microsoft Azure | Azure Marketplace
description: Seznam požadavků vyžadovaných k publikování nabídky virtuálních počítačů do Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: dsindona
ms.openlocfilehash: 24c560aff9e8ba7ca0858fdb7fa11a59301c2360
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80277612"
---
# <a name="virtual-machine-prerequisites"></a>Požadavky na virtuální počítač

Tento článek obsahuje seznam požadavků na technickou i obchodní požadavky, které je potřeba splnit, než bude možné publikovat nabídku virtuálních počítačů do [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/).  Pokud jste to ještě neudělali, přečtěte si [příručku k publikování nabídky virtuálních počítačů](../../marketplace-virtual-machines.md).


## <a name="technical-requirements"></a>Technické požadavky

Technické předpoklady pro publikování řešení virtuálních počítačů jsou jednoduché:

- Musíte mít aktivní účet Azure. Pokud ho nemáte, můžete se zaregistrovat na [Microsoft Azure lokalitě](https://azure.microsoft.com).  
- Aby bylo možné podporovat vývoj virtuálních počítačů se systémem Windows nebo Linux, je nutné mít prostředí nakonfigurované.  Další informace najdete na stránce dokumentace k souvisejícímu virtuálnímu počítači:
    - [Dokumentace k virtuálním počítačům s Linuxem](https://docs.microsoft.com/azure/virtual-machines/linux/)
    - [Dokumentace k virtuálním počítačům s Windows](https://docs.microsoft.com/azure/virtual-machines/windows/)


## <a name="business-requirements"></a>Podnikové požadavky

Mezi obchodní požadavky patří procedurální, smluvní a právní závazky: 

<!-- TD: Aren't most of these business requirements common to all AMP offerings?  If yes, then move to higher level, perhaps to the AMP section "Become a Cloud Marketplace Publisher" -->
<!-- TD: Need references for remaining docs/business reqs!-->

- Musíte být registrovaný Vydavatel cloudového webu Marketplace.  Pokud ještě nejste registrováni, postupujte podle kroků v článku [Staňte se vydavatelem cloudového Marketplace](https://docs.microsoft.com/azure/marketplace/become-publisher).

    > [!NOTE]
    > K přihlášení do [portál partnerů cloudu](https://cloudpartner.azure.com)byste měli použít stejný registrační účet Microsoft Developer Center.
    > Pro nabídky Azure Marketplace byste měli mít jenom jeden účet Microsoft. Neměla by být specifická pro jednotlivé služby nebo nabídky.
    
- Vaše společnost (nebo její dceřiná společnost) se musí nacházet v zemi nebo oblasti pro prodej od země, kterou Azure Marketplace podporuje.  Aktuální seznam těchto zemí nebo oblastí najdete v tématu [Microsoft Azure Marketplace zásady pro účast](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
- Váš produkt musí být licencován způsobem, který je kompatibilní s modely fakturace podporovanými Azure Marketplace.  Další informace najdete v tématu [Možnosti fakturace v Azure Marketplace](https://docs.microsoft.com/azure/marketplace/billing-options-azure-marketplace). 
- Zodpovídáte za zajištění dostupnosti technické podpory pro zákazníky v obchodním přiměřeném způsobem. Tato podpora může být bezplatná, placená nebo prostřednictvím komunitních přístupů.
- Zodpovídáte za licencování softwaru a všech závislostí softwaru třetích stran.
- Je nutné poskytnout obsah, který splňuje kritéria pro vaši nabídku, aby byla uvedena na Azure Marketplace a Azure Portal. <!-- TD: Meaning/links? -->
- Musíte souhlasit s podmínkami [zásad Microsoft Azure Marketplace pro účast](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) a smlouvy vydavatele.
- Musíte dodržovat [podmínky použití Microsoft Azure webu](https://azure.microsoft.com/support/legal/website-terms-of-use/), [prohlášení o zásadách ochrany osobních údajů společnosti Microsoft](https://privacy.microsoft.com/privacystatement)a [Microsoft Azure certifikovaných programů](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).


## <a name="next-steps"></a>Další kroky

Po splnění těchto požadavků můžete [vytvořit nabídku virtuálních počítačů](./cpp-create-offer.md).
