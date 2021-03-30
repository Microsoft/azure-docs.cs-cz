---
author: vhorne
ms.service: dns
ms.topic: include
ms.date: 11/25/2018
ms.author: victorh
ms.openlocfilehash: 9cc650cea17acb8d89933c819c4ca60e2c459d1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "67175638"
---
Záznamy SPF (Sender Policy Framework) se používají k určení, které e-mailové servery můžou posílat e-maily jménem názvu domény. Správná konfigurace záznamů SPF je důležitá, aby příjemci nemohli označit e-mail jako nevyžádaný.

Dokumenty RFC pro DNS původně zavedly nový typ záznamu SPF pro podporu tohoto scénáře. Aby bylo možné podporovat starší názvové servery, umožňují také použití typu záznamu TXT k určení záznamů SPF. Tato nejednoznačnost vedla k nejasnostem, což bylo vyřešeno [specifikací RFC 7208](http://tools.ietf.org/html/rfc7208#section-3.1). Uvádí, že záznamy SPF musí být vytvořeny pomocí typu záznamu TXT. Také uvádí, že typ záznamu SPF je zastaralý.

**Záznamy SPF jsou podporovány nástrojem Azure DNS a je nutné je vytvořit pomocí záznamu typu TXT.** Zastaralý typ záznamu SPF není podporován. Když [importujete soubor zóny DNS](../articles/dns/dns-import-export.md), všechny záznamy SPF, které používají typ záznamu SPF, se převedou na typ záznamu TXT.
