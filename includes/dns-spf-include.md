---
author: vhorne
ms.service: dns
ms.topic: include
ms.date: 11/25/2018
ms.author: victorh
ms.openlocfilehash: 9cc650cea17acb8d89933c819c4ca60e2c459d1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175638"
---
Záznamy rámce zásad odesílatele (SPF) se používají k určení, které e-mailové servery mohou odesílat e-maily jménem názvu domény. Správná konfigurace záznamů SPF je důležitá, aby příjemci neoznačovali váš e-mail jako nevyžádanou poštu.

Dokumenty RFC DNS původně zavedly nový typ záznamu SPF pro podporu tohoto scénáře. Pro podporu starších názvových serverů také povolili použití typu záznamu TXT k určení záznamů SPF. Tato nejednoznačnost vedla ke zmatku, který byl vyřešen [RFC 7208](http://tools.ietf.org/html/rfc7208#section-3.1). Uvádí, že záznamy SPF musí být vytvořeny pomocí typu záznamu TXT. Také uvádí, že typ záznamu SPF je zastaralé.

**Záznamy SPF jsou podporované službou Azure DNS a musí být vytvořeny pomocí typu záznamu TXT.** Zastaralý typ záznamu SPF není podporován. Při [importu souboru zóny DNS](../articles/dns/dns-import-export.md)budou všechny záznamy SPF, které používají typ záznamu SPF, převedeny na typ záznamu TXT.
