---
title: Předpoklady virtuálního počítače pro Microsoft Azure | Azure Marketplace
description: Seznam požadavků potřebných k publikování nabídky virtuálních počítačů na Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: dsindona
ms.openlocfilehash: 24c560aff9e8ba7ca0858fdb7fa11a59301c2360
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80277612"
---
# <a name="virtual-machine-prerequisites"></a>Předpoklady virtuálního počítače

Tento článek uvádí technické i obchodní požadavky, které musíte splnit před publikováním nabídky virtuálních počítačů na [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/).  Pokud jste tak ještě neučinili, přečtěte si [Průvodce publikováním nabídek virtuálních strojů](../../marketplace-virtual-machines.md).


## <a name="technical-requirements"></a>Technické požadavky

Technické předpoklady pro publikování řešení virtuálního počítače (VM) jsou jednoduché:

- Musíte mít aktivní účet Azure. Pokud ho nemáte, můžete se zaregistrovat na [webu Microsoft Azure](https://azure.microsoft.com).  
- Musíte mít prostředí nakonfigurované pro podporu vývoje virtuálních zařízení windows nebo linux.  Další informace najdete v tématu přidružený web dokumentace virtuálních aplikací:
    - [Dokumentace k virtuálním počítačům s Linuxem](https://docs.microsoft.com/azure/virtual-machines/linux/)
    - [Dokumentace k virtuálním počítačům s Windows](https://docs.microsoft.com/azure/virtual-machines/windows/)


## <a name="business-requirements"></a>Obchodní požadavky

Obchodní požadavky zahrnují procesní, smluvní a právní povinnosti: 

<!-- TD: Aren't most of these business requirements common to all AMP offerings?  If yes, then move to higher level, perhaps to the AMP section "Become a Cloud Marketplace Publisher" -->
<!-- TD: Need references for remaining docs/business reqs!-->

- Musíte být registrovaným vydavatelem Cloud Marketplace.  Pokud ještě nejste zaregistrováni, postupujte podle pokynů v článku [Staňte se vydavatelem cloudového webu](https://docs.microsoft.com/azure/marketplace/become-publisher)Marketplace .

    > [!NOTE]
    > K přihlášení na [portál cloudových partnerů](https://cloudpartner.azure.com)byste měli použít stejný registrační účet Centra pro vývojáře Microsoft Developer Center .
    > Pro nabídky Azure Marketplace byste měli mít jenom jeden účet Microsoft. Neměla by být specifická pro jednotlivé služby nebo nabídky.
    
- Vaše společnost (nebo její dceřiná společnost) musí být umístěna v prodeji ze země nebo oblasti podporované Azure Marketplace.  Aktuální seznam těchto zemí nebo oblastí najdete v tématu [Zásady účasti na webu Microsoft Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
- Váš produkt musí být licencován způsobem, který je kompatibilní s fakturačními modely podporovanými Azure Marketplace.  Další informace najdete [v tématu Možnosti fakturace na Webu Azure Marketplace](https://docs.microsoft.com/azure/marketplace/billing-options-azure-marketplace). 
- Jste zodpovědní za zpřístupnění technické podpory zákazníkům komerčně přiměřeným způsobem. Tato podpora může být bezplatná, placená nebo prostřednictvím komunitních přístupů.
- Nesete odpovědnost za licencování softwaru a jakýchkoli závislostí na softwaru třetích stran.
- Musíte poskytnout obsah, který splňuje kritéria pro vaši nabídku, která mají být uvedeny na Azure Marketplace a na webu Azure Portal. <!-- TD: Meaning/links? -->
- Musíte souhlasit s podmínkami [zásad účasti](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) na webu Microsoft Azure Marketplace a smlouvy s vydavatelem.
- Musíte dodržovat podmínky používání webu Microsoft Azure , [Prohlášení o zásadách ochrany osobních údajů společnosti Microsoft](https://privacy.microsoft.com/privacystatement)a [smlouvu o certifikovaných programech Microsoft Azure](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/). [Microsoft Azure Website Terms of Use](https://azure.microsoft.com/support/legal/website-terms-of-use/)


## <a name="next-steps"></a>Další kroky

Po splnění těchto požadavků můžete [vytvořit nabídku virtuálního počítače](./cpp-create-offer.md).
