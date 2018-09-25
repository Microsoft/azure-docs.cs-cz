---
title: Webová aplikace brány firewall žádost o velikosti omezení a seznamy vyloučení ve službě Azure Application Gateway – Azure portal | Dokumentace Microsoftu
description: Tento článek obsahuje informace o omezení velikosti pro požadavek webové aplikace brány firewall a seznamy vyloučení konfigurace ve službě Application Gateway pomocí webu Azure portal.
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: victorh
ms.openlocfilehash: 995e003422d5a94fe57174dc9733c870e4e003aa
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46965489"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists-public-preview"></a>Omezení velikosti pro požadavek webové aplikace brány firewall a vyloučení uvádí (Public Preview)

Firewall webových aplikací (WAF) Azure Application Gateway chrání webové aplikace. Tento článek popisuje WAF žádost o velikosti omezení a vyloučení jsou uvedeny konfigurace.

> [!IMPORTANT]
> Konfigurace omezení velikosti požadavek WAF a seznamy vyloučení je aktuálně ve verzi public preview. V této verzi preview je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se používat pro produkční úlohy. Některé funkce nemusí být podporované, nebo můžou mít omezené možnosti. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="waf-request-size-limits"></a>Omezení velikosti žádostí o WAF
Firewall webových aplikací umožňuje uživatelům konfigurovat omezení velikosti požadavku v rámci dolní a horní hranice. K dispozici jsou následující dvě velikost omezení konfigurace:
- Pole velikost textu maximální požadavek určen ve znalostní báze a ovládací prvky, které celkový limit velikosti žádosti o vyloučení všechny soubory nahraje. Toto pole musí být v rozsahu 1 KB minimální do maximální hodnoty 128 KB. Výchozí hodnota pro velikost textu požadavku je 128 KB.
- Limit pole pro uložení souborů se uvádí v MB a řídí maximální velikost povolenou nahrávání. Toto pole může mít minimální hodnotu 1 MB a maximálně 500 MB. Výchozí hodnota pro limitu pro nahrávání souborů je 100 MB.

WAF také nabízí Konfigurovatelný ovladače k zapnutí nebo vypnutí kontroly těla požadavku. Ve výchozím nastavení povolení kontroly těla požadavku. Pokud kontrola tělo žádosti je vypnutý, WAF nevyhodnocuje obsah zprávy HTTP. V takových případech WAF nadále vynucovat pravidla firewallu webových aplikací na záhlaví, soubory cookie a identifikátor URI. Pokud kontrola tělo žádosti je vypnutý, maximální žádost subjektu velikost pole se nedá použít a nelze nastavit. Vypnutím kontroly tělo požadavku umožňuje zprávy větší než 128 KB k odeslání do WAF. Tělo zprávy není však zkontroloval ohrožení zabezpečení.

## <a name="waf-exclusion-lists"></a>Seznamy vyloučení WAF

Seznamy vyloučení WAF povolit uživatelům vynechání určité atributy žádosti ze zkušební verze WAF. Běžným příkladem je že vložen tokeny, které se používají pro ověřování nebo pole s heslem služby Active Directory. Tyto atributy jsou náchylné k obsahovat speciální znaky, které můžou aktivovat falešně pozitivní z pravidla firewallu webových aplikací. Po přidání atributu do seznamu vyloučení WAF se nepořídí v úvahu žádným pravidlem nakonfigurovaná a aktivní WAF. V oboru jsou globální seznamy vyloučení.
Hlavičky žádosti, souborů cookie požadavku nebo argumenty řetězce dotazu žádosti můžete přidat na seznamy vyloučení WAF. Můžete zadat přesná požádat o záhlaví, soubor cookie nebo shodu atributů řetězce dotazu, nebo můžete volitelně zadat částečné shody. Tady jsou operátory porovnání podporovaných kritéria: 
- **Se rovná**: Tento operátor se používá pro přesnou shodu. Jako příklad pro výběr záhlaví s názvem **bearerToken**"** použití rovná se operátor s selektor nastavit jako **bearerToken**. 
- **Začíná**: Tento operátor odpovídá všechna pole, která začínají hodnotou zadaný selektor. 
- **Končí**: Tento operátor odpovídá všechna pole žádosti, které končí hodnotu zadaný selektor. 
- **Obsahuje**: Tento operátor odpovídá všechna pole žádosti, které obsahují hodnotu zadaný selektor.

Ve všech případech vyhledávání nejsou rozlišována velká a malá písmena a regulárního výrazu nejsou povoleny jako selektorů.

## <a name="next-steps"></a>Další postup

Po dokončení konfigurace nastavení WAF se dozvíte, jak si chcete zobrazit protokoly WAF. Další informace najdete v tématu [diagnostice služby Application Gateway](application-gateway-diagnostics.md#diagnostic-logging).