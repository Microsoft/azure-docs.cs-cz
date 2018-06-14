---
title: Řada SKU není k dispozici | Microsoft Docs
description: Některé řada SKU nejsou k dispozici pro vybrané předplatné pro tuto oblast.
services: Azure Supportability
documentationcenter: ''
author: stevendotwang
manager: rajatk
editor: ''
ms.service: azure-supportability
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: xingwan
ms.openlocfilehash: 62964d0c5d75168226a35b25e5c256a1b57f3f81
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2017
ms.locfileid: "24870574"
---
# <a name="region-or-sku-unavailable"></a>Oblast nebo SKU, které jsou k dispozici
Tento článek popisuje, jak k vyřešení problému předplatné Azure, které nemají přístup k oblasti nebo SKU virtuálních počítačů.

## <a name="symptoms"></a>Příznaky

### <a name="when-deploying-a-virtual-machine-you-receive-one-of-the-following-error-messages"></a>Při nasazení virtuálního počítače, se zobrazí některá z následujících chybových zpráv:
```
Code: SkuNotAvailable
Message: The requested size for resource '<resource>' is currently not available in location 
'<location>' zones '<zone>' for subscription '<subscriptionID>'. Please try another size or 
deploy to a different location or zones. See https://aka.ms/azureskunotavailable for details.
```

```
Message: Your subscription doesn’t support virtual machine creation in <location>. Choose a 
different location. Supported locations are <list of locations>
```

```
Code: NotAvailableForSubscription
Message: This size is currently unavailable in this location for this subscription
```

### <a name="when-purchasing-reserved-virtual-machine-instances-you-receive-one-of-the-following-error-messages"></a>Při zakoupení vyhrazenou instancí virtuálního počítače, se zobrazí některá z následujících chybových zpráv:

```
Message: Your subscription doesn’t support virtual machine reservation in <location>. Choose a 
different location. Supported locations are: <list of locations>  
```

```
Message: This size is currently unavailable in this location for this subscription
```

### <a name="when-creating-a-support-request-to-increase-compute-core-quota-a-region-or-a-sku-family-is-not-available-for-selection"></a>Při vytváření žádosti o podporu o navýšení kvóty základní výpočetní, oblasti nebo řadu SKU není k dispozici pro výběr.

## <a name="solution"></a>Řešení
Doporučujeme nejprve uvažujete alternativní oblasti nebo skladová položka, která vyhovuje vašim obchodním potřebám. Pokud nemůžete najít vhodný oblasti nebo SKU, vytvořte "Správa předplatného" [žádost o podporu](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) následujících kroků:


- Na stránce základy vyberte typ problému jako "Správa předplatného", vyberte odběr a klikněte na tlačítko Další.

![Okno Základy](./media/SKU-series-unavailable/BasicsSubMgmt.png)


-   Na stránce problém vyberte typ problému jako "Ostatní obecné otázky".
- V části Podrobnosti:
  - Označte prosím, pokud se díváte nasazení virtuálních počítačů nebo nákupu vyhrazenou instancí virtuálního počítače
  - Zadejte oblast, SKU a počet instancí virtuálního počítače, které chcete nasadit k vyzkoušení nebo nákupu


![Problém](./media/SKU-series-unavailable/ProblemSubMgmt.png)

-   Zadejte svoje kontaktní údaje a klikněte na tlačítko "Vytvořit".

![Kontaktní informace](./media/SKU-series-unavailable/ContactInformation.png)

## <a name="feedback"></a>Váš názor
Snažíme se vždy otevřený a názory a návrhy! Pošlete nám vaše [návrhy](https://feedback.azure.com/forums/266794-support-feedback). Kromě toho můžete použít u nás prostřednictvím [Twitter](https://twitter.com/azuresupport) nebo [fórech MSDN](https://social.msdn.microsoft.com/Forums/azure).

## <a name="learn-more"></a>Další informace
[Nejčastější dotazy týkající se podpory Azure](https://azure.microsoft.com/support/faq)

