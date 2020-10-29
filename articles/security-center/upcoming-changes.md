---
title: Důležité změny přicházející do Azure Security Center
description: Nadcházející změny Azure Security Center, na které může být nutné vědět a pro které může být nutné naplánovat
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/26/2020
ms.author: memildin
ms.openlocfilehash: 549a95b0b2ffc2b2d2bf5670a961e0454683e33a
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2020
ms.locfileid: "93026713"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Důležité nadcházející změny Azure Security Center

> [!IMPORTANT]
> Informace na této stránce se vztahují k předběžným produktům nebo funkcím, které mohou být podstatně změněny předtím, než budou komerčně vydány. Společnost Microsoft neposkytuje žádné závazky ani záruky výslovně uvedené ani předpokládané, a to v souvislosti s informacemi, které jsou zde uvedeny.

Na této stránce se dozvíte o změnách, které jsou plánovány pro Security Center. Popisuje plánované změny produktu, které by mohly mít vliv na vaše zabezpečené skóre nebo pracovní postupy.

Pokud hledáte nejnovější poznámky k verzi, najdete je v [Azure Security Center novinky](release-notes.md).


## <a name="planned-changes"></a>Plánované změny

### <a name="recommendations-related-to-azure-security-benchmark-to-be-added-preview"></a>Doporučení týkající se srovnávacích testů zabezpečení Azure, která se mají přidat (Preview)

| Aspekt | Podrobnosti |
|---------|---------|
|Datum oznámení | 26. října 2020  |
|Datum pro tuto změnu  |  Listopadu 2020 |
|Dopad     | Potenciálně další doporučení ke kontrole.<br>Žádný bezprostřední dopad na doporučení pro zajištění skóre verze Preview nemá vliv na vaše zabezpečené skóre.<br>Žádný bezprostřední dopad na stav vašich prostředků – doporučení pro verzi Preview nevykreslují prostředek "není v pořádku".|
|  |  |

Srovnávací test zabezpečení Azure je Microsoftem vytvořená sada pokynů pro zabezpečení a osvědčené postupy na základě běžných architektur dodržování předpisů, které jsou specifické pro Azure. [Další informace o srovnávacím testu zabezpečení Azure](../security/benchmarks/introduction.md)

Následující 18 nových doporučení budou přidána do Security Center, aby se zvýšilo pokrytí srovnávacího testu.

Doporučení pro verzi Preview negenerují prostředek v pořádku a nejsou zahrnutá do výpočtů vašeho zabezpečeného skóre. Opravte je všude, kde je to možné, takže až do doby, kdy období Preview skončí, přispějete k vašemu skóre. Přečtěte si další informace o tom, jak na tato doporučení reagovat v tématu o [opravách doporučení v Azure Security Center](security-center-remediate-recommendations.md).

- Azure Backup by měly být povolené pro virtuální počítače
- Pro databázové servery MySQL by mělo být povoleno připojení SSL vynutilo.
- Pro databázové servery PostgreSQL by mělo být povoleno připojení SSL vynutilo.
- Pro Azure Database for MariaDB by měla být povolená geograficky redundantní záloha.
- Pro Azure Database for MySQL by měla být povolená geograficky redundantní záloha.
- Pro Azure Database for PostgreSQL by měla být povolená geograficky redundantní záloha.
- Java by se měla aktualizovat na nejnovější verzi vaší aplikace API
- Java by se měla aktualizovat na nejnovější verzi vaší aplikace Function App
- Java by se měla aktualizovat na nejnovější verzi vaší webové aplikace.
- PHP by se mělo aktualizovat na nejnovější verzi vaší aplikace API
- PHP by se měla aktualizovat na nejnovější verzi vaší webové aplikace.
- Privátní koncový bod by měl být povolený pro MariaDB servery.
- Pro servery MySQL by měl být povolen soukromý koncový bod.
- Privátní koncový bod by měl být povolený pro PostgreSQL servery.
- Python by se měl aktualizovat na nejnovější verzi vaší aplikace API
- Python by se měl aktualizovat na nejnovější verzi aplikace Function App.
- Python by se měl aktualizovat na nejnovější verzi vaší webové aplikace.
- Webové aplikace by měly požádat o certifikát SSL pro všechny příchozí požadavky

Související odkazy:

- [Další informace o testu zabezpečení Azure](../security/benchmarks/introduction.md)
- [Další informace o Azure API Apps](../app-service/app-service-web-tutorial-rest-api.md)
- [Další informace o aplikacích Azure Function App](../azure-functions/functions-overview.md)
- [Další informace o službě Azure Web Apps](../app-service/overview.md)
- [Další informace o Azure Database for MariaDB](../mariadb/overview.md)
- [Další informace o Azure Database for MySQL](../mysql/overview.md)
- [Další informace o Azure Database for PostgreSQL](../postgresql/overview.md)

## <a name="next-steps"></a>Další kroky

Všechny nedávné změny v produktu najdete v tématu [co je nového v Azure Security Center?](release-notes.md).