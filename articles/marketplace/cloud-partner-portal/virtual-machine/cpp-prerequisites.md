---
title: Požadavky virtuálního počítače pro Microsoft Azure | Dokumentace Microsoftu
description: Seznam požadovaných součástí pro publikování nabídky virtuálních počítačů na webu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 03/13/2019
ms.author: pbutlerm
ms.openlocfilehash: 5c26fd3d6c2593217aef8588e2a58377a86412ae
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "58010848"
---
# <a name="virtual-machine-prerequisites"></a>Požadavky virtuálního počítače

Tento článek uvádí i technické a podnikové požadavky, které je nutné splnit před publikováním nabídky virtuálních počítačů na [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/).  Pokud jste tak již neučinili, přečtěte si [Průvodce publikování nabídky virtuálních počítačů](../../marketplace-virtual-machines.md).


## <a name="technical-requirements"></a>Technické požadavky

Technické požadavky pro publikování řešení virtuálních počítačů (VM) jsou jednoduché:

- Musíte mít aktivní účet Azure. Pokud nemáte, můžete si zaregistrovat na [webu Microsoft Azure](https://azure.microsoft.com).  
- Musíte mít prostředí nakonfigurované pro podporu vývoje aplikací pro Windows nebo virtuální počítač s Linuxem.  Další informace najdete v tématu webu s dokumentací přidružený virtuální počítač:
    - [Dokumentace k virtuálním počítačům s Linuxem](https://docs.microsoft.com/azure/virtual-machines/linux/)
    - [Dokumentace k virtuálním počítačům pro Windows](https://docs.microsoft.com/azure/virtual-machines/windows/)


## <a name="business-requirements"></a>Obchodní požadavky

Obchodní požadavky patří procesní, smluvní a právní závazky: 

<!-- TD: Aren't most of these business requirements common to all AMP offerings?  If yes, then move to higher level, perhaps to the AMP section "Become a Cloud Marketplace Publisher" -->
<!-- TD: Need references for remaining docs/business reqs!-->

- Musí být registrovaný vydavatele na webu Marketplace cloudu.  Pokud jste se ještě nezaregistrovali, postupujte podle kroků v článku [stát vydavatelem cloudové Tržiště](../../become-publisher.md).

    > [!NOTE]
    > Používejte stejné registrace účtu Microsoft Developer Center pro přihlášení do [portál partnerů cloudu](https://cloudpartner.azure.com).
    > Pro svoje nabídky na webu Azure Marketplace byste měli mít jenom jeden účet Microsoft. Neměl by být specifické pro jednotlivé služby nebo nabídky.
    
- Vaše společnost (nebo její pobočka) se musí nacházet v prodeji z zemi podporovanými webem Azure Marketplace.  Aktuální seznam těchto zemí najdete v tématu [zásadách pro účast na Microsoft Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
- Váš produkt musí mít licenci způsobem, který je kompatibilní s modely fakturace podporovanými webem Azure Marketplace.  Další informace najdete v tématu [možnosti fakturace na webu Azure Marketplace](https://docs.microsoft.com/azure/marketplace/billing-options-azure-marketplace). 
- Zodpovídáte za zpřístupnění technické podpory pro zákazníky přiměřené způsobem. Tato podpora může být zdarma, placená nebo prostřednictvím komunity přístupy.
- Zodpovídáte za licencování váš software a všechny závislosti softwaru třetích stran.
- Je nutné zadat obsah, který splňuje kritéria pro vaší nabídky na webu Azure Marketplace a na webu Azure Portal. <!-- TD: Meaning/links? -->
- Musíte souhlasit s podmínkami [zásadách pro účast na Microsoft Azure Marketplace](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) a smlouvě s vydavatelem.
- Musíte dodržovat [Microsoft Azure podmínky používání webu](https://azure.microsoft.com/support/legal/website-terms-of-use/), [prohlášení o ochraně osobních údajů Microsoft](https://privacy.microsoft.com/privacystatement), a [smlouvou Microsoft Azure Certified Program](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).


## <a name="next-steps"></a>Další postup

Po splnění těchto požadavků můžete [vytvoření nabídky virtuálních počítačů](./cpp-create-offer.md).
