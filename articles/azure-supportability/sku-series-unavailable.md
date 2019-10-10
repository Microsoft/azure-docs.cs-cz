---
title: Řada SKU není dostupná | Microsoft Docs
description: Pro vybrané předplatné této oblasti nejsou některé řady SKU k dispozici.
services: Azure Supportability
author: stevendotwang
ms.service: azure-supportability
ms.topic: article
ms.date: 11/09/2017
ms.author: xingwan
ms.openlocfilehash: e317ae1ad88cf162f1d55a06d19e7b3b0b88ce60
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249045"
---
# <a name="region-or-sku-unavailable"></a>Oblast nebo SKU nejsou k dispozici
Tento článek popisuje, jak vyřešit problém předplatného Azure, který nemá přístup k oblasti nebo SKU virtuálního počítače.

## <a name="symptoms"></a>Příznaky

### <a name="when-deploying-a-virtual-machine-you-receive-one-of-the-following-error-messages"></a>Při nasazování virtuálního počítače se zobrazí jedna z následujících chybových zpráv:
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

### <a name="when-purchasing-reserved-virtual-machine-instances-you-receive-one-of-the-following-error-messages"></a>Při nákupu rezervovaných instancí virtuálních počítačů se zobrazí jedna z následujících chybových zpráv:

```
Message: Your subscription doesn’t support virtual machine reservation in <location>. Choose a 
different location. Supported locations are: <list of locations>  
```

```
Message: This size is currently unavailable in this location for this subscription
```

### <a name="when-creating-a-support-request-to-increase-compute-core-quota-a-region-or-a-sku-family-is-not-available-for-selection"></a>Při vytváření žádosti o podporu pro zvýšení kvóty výpočetního jádra není pro výběr k dispozici oblast nebo rodina SKU.

## <a name="solution"></a>Řešení
Nejdřív doporučujeme vzít v úvahu alternativní oblast nebo SKU, které vyhovují vašim obchodním potřebám. Pokud nemůžete najít vhodnou oblast nebo SKU, vytvořte [žádost o podporu](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) pro správu předplatného podle následujících kroků:


- Na stránce základy vyberte typ problému jako "Správa předplatného", vyberte předplatné a klikněte na další.

![Okno základy](./media/SKU-series-unavailable/BasicsSubMgmt.png)


-   Na stránce problém vyberte typ problému "ostatní obecné otázky".
- V části Podrobnosti:
  - Uveďte prosím, jestli se chystáte nasadit virtuální počítače nebo zakoupit rezervované instance virtuálních počítačů.
  - Zadejte prosím oblast, SKLADOVOU položku a počet instancí virtuálních počítačů, které plánujete nasadit nebo koupit.


![Problém](./media/SKU-series-unavailable/ProblemSubMgmt.png)

-   Zadejte svoje kontaktní údaje a klikněte na vytvořit.

![Kontaktní informace](./media/SKU-series-unavailable/ContactInformation.png)

## <a name="feedback"></a>Od
Vždycky jsme otevřeli názory a návrhy! Pošlete nám svoje [návrhy](https://feedback.azure.com/forums/266794-support-feedback). Kromě toho můžete s námi spolupracovat přes [Twitter](https://twitter.com/azuresupport) nebo [fóra MSDN](https://social.msdn.microsoft.com/Forums/azure).

## <a name="learn-more"></a>Další informace
[Nejčastější dotazy k podpoře Azure](https://azure.microsoft.com/support/faq)

