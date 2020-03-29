---
title: Oblast nebo skladové položky řady není k dispozici – Azure
description: Některé řady skladových položk nejsou k dispozici pro vybrané předplatné pro tuto oblast, což může vyžadovat požadavek na podporu správy předplatného.
services: Azure Supportability
author: stevendotwang
ms.service: azure-supportability
ms.topic: article
ms.date: 01/27/2020
ms.author: xingwan
ms.openlocfilehash: b0f0762ded6804c0b0d90a19223c082f0fb8fd49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843620"
---
# <a name="region-or-sku-unavailable"></a>Oblast nebo skladová položka není k dispozici

Tento článek popisuje, jak vyřešit problém předplatného Azure, které nemají přístup k oblasti nebo skladové jednotce virtuálního počítače.

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

Při nákupu rezervovaných instancí virtuálních strojů se zobrazí jedna z následujících chybových zpráv:

```
Message: Your subscription doesn’t support virtual machine reservation in <location>. Choose a 
different location. Supported locations are: <list of locations>  
```

```
Message: This size is currently unavailable in this location for this subscription
```

Při vytváření žádosti o podporu pro zvýšení kvóty výpočetního jádra není pro výběr k dispozici oblast nebo rodina skladových míst.

## <a name="solution"></a>Řešení

Nejprve doporučujeme zvážit alternativní oblast nebo skladovou položku, která splňuje vaše obchodní potřeby.

Pokud nemůžete najít vhodnou oblast nebo skladovou položku, vytvořte [žádost o podporu pro](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) **správu předplatného** podle následujících kroků:

1. V nabídce [Portál Azure](https://portal.azure.com) vyberte **Nápověda + podpora**. Pak vyberte **Nový požadavek na podporu**.

1. V **části Základy**vyberte v části **Typ problému** **položku Správa předplatného**.

1. Vyberte **předplatné** a zadejte stručný popis v **souhrnu**.

   ![Karta Základy nové žádosti o podporu](./media/SKU-series-unavailable/support-request-basics.png)

1. V **poučném typu**zvolte **Vybrat typ problému**.

1. V **případě typu Vybrat problém**zvolte možnost, **například Nelze získat přístup k předplatnému nebo prostředku** > **Můj problém není uveden výše**. Vyberte **Uložit**.

   ![Určení problému pro požadavek](./media/SKU-series-unavailable/support-request-select-problem-type.png)

1. Vyberte **další: Řešení** pro zkoumání možných řešení. V případě potřeby vyberte **další: Podrobnosti** pokračovat.

1. Zadejte jakékoli další informace, které můžete poskytnout, spolu s kontaktními údaji.

1. Vyberte **Zkontrolovat a vytvořit**. Po ověření informací vytvořte požadavek výběrem příkazu **Vytvořit.**

## <a name="send-us-your-suggestions"></a>Pošlete nám své návrhy

Jsme vždy otevřeni zpětné vazbě a návrhům! Pošlete nám své [návrhy](https://feedback.azure.com/forums/266794-support-feedback). Kromě toho se můžete zapojit s námi na [Twitter](https://twitter.com/azuresupport) nebo [MSDN fóra](https://social.msdn.microsoft.com/Forums/azure).

## <a name="learn-more"></a>Další informace

[Nejčastější dotazy k podpoře Azure](https://azure.microsoft.com/support/faq)
