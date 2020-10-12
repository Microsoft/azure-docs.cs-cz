---
title: Zabezpečení – Azure Database for MySQL
description: Přehled funkcí zabezpečení v Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 5caed64beb43cbb1e884a3dde6bc0052c617f83e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89378776"
---
# <a name="security-in-azure-database-for-mysql"></a>Zabezpečení ve službě Azure Database for MySQL

K ochraně dat na vašem serveru Azure Database for MySQL je k dispozici několik vrstev zabezpečení. Tento článek popisuje tyto možnosti zabezpečení.

## <a name="information-protection-and-encryption"></a>Ochrana informací a šifrování

### <a name="in-transit"></a>Při přenosu
Azure Database for MySQL zabezpečuje vaše data šifrováním přenášených dat pomocí Transport Layer Security. Šifrování (SSL/TLS) se ve výchozím nastavení vynutilo.

### <a name="at-rest"></a>V klidovém případě
Služba Azure Database for MySQL používá šifrovací modul ověřený 140-2 Standard FIPS pro šifrování úložiště neaktivních dat. Data včetně záloh se šifrují na disku, včetně dočasných souborů vytvořených při spouštění dotazů. Služba používá algoritmus AES 256-bit, který je součástí šifrování úložiště Azure, a klíče jsou spravované systémem. Šifrování úložiště je vždycky aktivní a není možné ho zakázat.


## <a name="network-security"></a>Zabezpečení sítě
Připojení k serveru Azure Database for MySQL jsou nejprve směrována prostřednictvím místní brány. Brána má veřejně přístupnou IP adresu, zatímco IP adresy serveru jsou chráněné. Další informace o bráně najdete v [článku architektura připojení](concepts-connectivity-architecture.md).  

Nově vytvořený Azure Database for MySQL server má bránu firewall, která blokuje všechna externí připojení. I když dosáhnou brány, není jim dovoleno se připojit k serveru. 

### <a name="ip-firewall-rules"></a>Pravidla brány firewall protokolu IP
Pravidla brány firewall protokolu IP udělují přístup k serverům na základě zdrojové IP adresy jednotlivých požadavků. Další informace najdete v tématu [Přehled pravidel brány firewall](concepts-firewall-rules.md) .

### <a name="virtual-network-firewall-rules"></a>Pravidla brány firewall virtuální sítě
Koncové body služby virtuální sítě prodlužují připojení k virtuální síti přes páteřní síť Azure. Pomocí pravidel virtuální sítě můžete povolit serveru Azure Database for MySQL, aby bylo možné povolit připojení z vybraných podsítí ve virtuální síti. Další informace najdete v tématu [Přehled koncového bodu služby virtuální sítě](concepts-data-access-and-security-vnet.md).

### <a name="private-ip"></a>Privátní IP adresa
Privátní odkaz vám umožní připojit se k vašemu Azure Database for MySQL v Azure prostřednictvím privátního koncového bodu. Azure Private Link v podstatě přináší služby Azure do vaší privátní virtuální sítě. K prostředkům PaaS je možné přistupovat přes privátní IP adresu stejně jako k jakýmkoli jiným prostředkům ve virtuální síti. Další informace najdete v tématu [Přehled privátních odkazů](concepts-data-access-security-private-link.md) .

## <a name="access-management"></a>Správa přístupu

Při vytváření Azure Database for MySQLho serveru poskytnete přihlašovací údaje pro uživatele správce. Tento správce se dá použít k vytvoření dalších uživatelů MySQL.


## <a name="threat-protection"></a>Ochrana před hrozbami

Můžete se rozhodnout pro [rozšířenou ochranu před internetovými útoky](concepts-data-access-and-security-threat-protection.md) , která detekuje neobvyklé aktivity indikující neobvyklé a potenciálně nebezpečné pokusy o přístup k serverům nebo jejich zneužití.

[Protokolování auditu](concepts-audit-logs.md) je k dispozici ke sledování aktivity ve vašich databázích. 


## <a name="next-steps"></a>Další kroky
- Povolit pravidla brány firewall pro [IP adresy](concepts-firewall-rules.md) nebo [virtuální sítě](concepts-data-access-and-security-vnet.md)
