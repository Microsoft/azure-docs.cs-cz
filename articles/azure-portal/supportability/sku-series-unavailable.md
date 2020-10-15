---
title: Řady oblastí nebo SKU nejsou dostupné – Azure
description: Některé řady SKU nejsou pro vybrané předplatné v této oblasti k dispozici, což může vyžadovat požadavek na podporu správy předplatných.
services: Azure Supportability
author: stevendotwang
ms.service: azure-supportability
ms.topic: troubleshooting
ms.date: 01/27/2020
ms.author: xingwan
ms.openlocfilehash: fccde344a06d39bd4bb8e3178444ec20677e2f50
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078506"
---
# <a name="region-or-sku-unavailable"></a>Oblast nebo skladová položka není k dispozici

Tento článek popisuje, jak vyřešit problém předplatného Azure, který nemá přístup k oblasti nebo SKU virtuálního počítače.

## <a name="symptoms"></a>Příznaky

Při nasazování virtuálního počítače se zobrazí jedna z následujících chybových zpráv:

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

Při nákupu rezervovaných instancí virtuálních počítačů se zobrazí jedna z následujících chybových zpráv:

```
Message: Your subscription doesn’t support virtual machine reservation in <location>. Choose a 
different location. Supported locations are: <list of locations>  
```

```
Message: This size is currently unavailable in this location for this subscription
```

Při vytváření žádosti o podporu pro zvýšení kvóty výpočetního jádra není dostupná oblast nebo rodina SKU pro výběr.

## <a name="solution"></a>Řešení

Nejdřív doporučujeme vzít v úvahu alternativní oblast nebo SKU, které vyhovují vašim obchodním potřebám.

Pokud nemůžete najít vhodnou oblast nebo SKU, vytvořte [žádost o podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) **správy předplatného** podle těchto kroků:

1. V nabídce [Azure Portal](https://portal.azure.com) vyberte možnost **help + podpora**. Pak vyberte **Nová žádost o podporu**.

1. V **základních**informacích pro **typ problému**vyberte **Správa předplatných**.

1. Vyberte **předplatné** a v **souhrnu**zadejte stručný popis.

   ![Karta základy nové žádosti o podporu](./media/SKU-series-unavailable/support-request-basics.png)

1. Jako **typ problému**zvolte **Vybrat typ problému**.

1. Pro **možnost vybrat typ problému**zvolte možnost, například **nemůžete získat přístup k předplatnému nebo prostředek**  >  **můj problém není uveden výše**. Vyberte **Uložit**.

   ![Zadejte problém pro požadavek.](./media/SKU-series-unavailable/support-request-select-problem-type.png)

1. Vyberte **Další: řešení** a prozkoumejte možná řešení. V případě potřeby vyberte **Další: podrobnosti** a pokračujte.

1. Zadejte další informace, které můžete zadat, spolu s kontaktními informacemi.

1. Vyberte **Zkontrolovat a vytvořit**. Po ověření vašich informací vyberte **vytvořit** a vytvořte žádost.

## <a name="send-us-your-suggestions"></a>Pošlete nám svoje návrhy

Vždycky jsme si otevřeli názory a návrhy! Pošlete nám svoje [návrhy](https://feedback.azure.com/forums/266794-support-feedback). Kromě toho můžete s námi spolupracovat na [Twitteru](https://twitter.com/azuresupport) nebo na [stránce s dotazem na Microsoft Q&](/answers/products/azure).

## <a name="learn-more"></a>Další informace

[Nejčastější dotazy k podpoře Azure](https://azure.microsoft.com/support/faq)