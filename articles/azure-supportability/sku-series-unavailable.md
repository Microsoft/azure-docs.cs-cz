---
title: Řady SKU není k dispozici | Dokumentace Microsoftu
description: Některé řady SKU nejsou pro vybrané předplatné v této oblasti.
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
ms.openlocfilehash: a57899e36a6716a6fd59cb018119c225b7396c0d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60649086"
---
# <a name="region-or-sku-unavailable"></a>Oblast nebo SKU není k dispozici
Tento článek popisuje, jak vyřešit problém předplatné Azure nemáte přístup k oblasti nebo skladovou Položku virtuálního počítače.

## <a name="symptoms"></a>Příznaky

### <a name="when-deploying-a-virtual-machine-you-receive-one-of-the-following-error-messages"></a>Při nasazování virtuálního počítače, se zobrazit jedna z následujících chybových zpráv:
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

### <a name="when-purchasing-reserved-virtual-machine-instances-you-receive-one-of-the-following-error-messages"></a>Při nákupu rezervované instance virtuálních počítačů, se zobrazit jedna z následujících chybových zpráv:

```
Message: Your subscription doesn’t support virtual machine reservation in <location>. Choose a 
different location. Supported locations are: <list of locations>  
```

```
Message: This size is currently unavailable in this location for this subscription
```

### <a name="when-creating-a-support-request-to-increase-compute-core-quota-a-region-or-a-sku-family-is-not-available-for-selection"></a>Při vytváření žádosti o podporu o navýšení kvóty pro jádra výpočetní prostředky, oblasti nebo rodině SKU není k dispozici pro výběr.

## <a name="solution"></a>Řešení
Nejdřív doporučujeme považovat alternativní oblast nebo skladovou Položku, která vyhovuje vašim obchodním potřebám. Pokud nemůžete najít vhodné oblasti nebo SKU, vytvořte "Správa předplatného" [žádost o podporu](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) následujících kroků:


- Na stránce základy vyberte typ problému jako "Správa předplatného", vyberte odběr a klikněte na tlačítko Další.

![Okno Základy](./media/SKU-series-unavailable/BasicsSubMgmt.png)


-   Na stránce problému vyberte typ problému jako "Ostatní obecné dotazy".
- V části Podrobnosti:
  - Uveďte, pokud chcete nasadit virtuální počítače nebo nákupu rezervované instance virtuálních počítačů
  - Zadejte prosím oblast, SKU a počet instancí virtuálních počítačů, které plánujete nasadit nebo nákupu


![Problém](./media/SKU-series-unavailable/ProblemSubMgmt.png)

-   Zadejte svoje kontaktní údaje a klikněte na možnost "Vytvořit".

![Kontaktní informace](./media/SKU-series-unavailable/ContactInformation.png)

## <a name="feedback"></a>Váš názor
Máme vždy otevřít na názory a návrhy! Pošlete nám vaše [návrhy](https://feedback.azure.com/forums/266794-support-feedback). Kromě toho můžete zapojit s námi prostřednictvím [Twitter](https://twitter.com/azuresupport) nebo [DISKUZNÍ fóra MSDN](https://social.msdn.microsoft.com/Forums/azure).

## <a name="learn-more"></a>Další informace
[Nejčastější dotazy k podpoře Azure](https://azure.microsoft.com/support/faq)

