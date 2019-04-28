---
author: WenJason
ms.service: dns
ms.topic: include
origin.date: 11/25/2018
ms.date: 12/24/2018
ms.author: v-jay
ms.openlocfilehash: 9cc650cea17acb8d89933c819c4ca60e2c459d1c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60563332"
---
Odesílatel zásad framework (SPF) záznamy se používají k určení serverů, které e-mailu můžete odeslat e-mailu jménem názvu domény. Správná konfigurace záznamy SPF je důležité zabránit označení e-mailu jako nevyžádané příjemce.

Dokumenty RFC DNS původně zavedl nový typ záznamu SPF pro podporu tohoto scénáře. Pro podporu starších názvové servery, jsou také povoleny pomocí záznamu typu TXT, jak vytvořit záznamy SPF. Tuto nejednoznačnost vedla k záměně, který se vyřešil podle [RFC 7208](http://tools.ietf.org/html/rfc7208#section-3.1). Uvádí, že záznamy SPF musí být vytvořené pomocí záznamu typu TXT. Také uvádí, že typ záznamu SPF se už nepoužívá.

**Záznamy SPF se podporují v Azure DNS a musí být vytvořené pomocí záznamu typu TXT.** Zastaralý typ záznamu SPF se nepodporuje. Pokud jste [importovat soubor zóny DNS](../articles/dns/dns-import-export.md), všechny záznamy SPF, které používají typ záznamu SPF se převedou na záznamu typu TXT.
