---
title: Zabezpečení v Azure Database for PostgreSQL – jeden server
description: Přehled funkcí zabezpečení v Azure Database for PostgreSQL-jednom serveru.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: b95e02046b2f05dd89ec8fce5da438380a8894e9
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89375784"
---
# <a name="security-in-azure-database-for-postgresql---single-server"></a>Zabezpečení v Azure Database for PostgreSQL – jeden server

K ochraně dat na vašem serveru Azure Database for PostgreSQL je k dispozici několik vrstev zabezpečení. Tento článek popisuje tyto možnosti zabezpečení.

## <a name="information-protection-and-encryption"></a>Ochrana informací a šifrování

### <a name="in-transit"></a>Při přenosu
Azure Database for PostgreSQL zabezpečuje vaše data šifrováním přenášených dat pomocí Transport Layer Security. Šifrování (SSL/TLS) se ve výchozím nastavení vynutilo.

### <a name="at-rest"></a>V klidovém případě
Služba Azure Database for PostgreSQL používá šifrovací modul ověřený 140-2 Standard FIPS pro šifrování úložiště neaktivních dat. Data včetně záloh se šifrují na disku, včetně dočasných souborů vytvořených při spouštění dotazů. Služba používá algoritmus AES 256-bit, který je součástí šifrování úložiště Azure, a klíče jsou spravované systémem. Šifrování úložiště je vždycky aktivní a není možné ho zakázat.


## <a name="network-security"></a>Zabezpečení sítě
Připojení k serveru Azure Database for PostgreSQL jsou nejprve směrována prostřednictvím místní brány. Brána má veřejně přístupnou IP adresu, zatímco IP adresy serveru jsou chráněné. Další informace o bráně najdete v [článku architektura připojení](concepts-connectivity-architecture.md).  

Nově vytvořený Azure Database for PostgreSQL Server má bránu firewall, která blokuje všechna externí připojení. I když dosáhnou brány, není jim dovoleno se připojit k serveru. 

### <a name="ip-firewall-rules"></a>Pravidla brány firewall protokolu IP
Pravidla brány firewall protokolu IP udělují přístup k serverům na základě zdrojové IP adresy jednotlivých požadavků. Další informace najdete v tématu [Přehled pravidel brány firewall](concepts-firewall-rules.md) .

### <a name="virtual-network-firewall-rules"></a>Pravidla brány firewall virtuální sítě
Koncové body služby virtuální sítě prodlužují připojení k virtuální síti přes páteřní síť Azure. Pomocí pravidel virtuální sítě můžete povolit serveru Azure Database for PostgreSQL, aby bylo možné povolit připojení z vybraných podsítí ve virtuální síti. Další informace najdete v tématu [Přehled koncového bodu služby virtuální sítě](concepts-data-access-and-security-vnet.md).

### <a name="private-ip"></a>Privátní IP adresa
Privátní odkaz vám umožní připojit se k vašemu Azure Database for PostgreSQLmu jednomu serveru v Azure prostřednictvím privátního koncového bodu. Privátní propojení Azure v podstatě přináší služby Azure do privátního Virtual Network (VNet). K prostředkům PaaS se dá dostat pomocí privátní IP adresy stejně jako u jakéhokoli jiného prostředku ve virtuální síti. Další informace najdete v tématu [Přehled privátních odkazů](concepts-data-access-and-security-private-link.md) .


## <a name="access-management"></a>Správa přístupu

Při vytváření Azure Database for PostgreSQL serveru poskytujete přihlašovací údaje pro roli správce. Tato role správce se dá použít k vytvoření dalších [rolí PostgreSQL](https://www.postgresql.org/docs/current/user-manag.html).

K serveru se můžete připojit taky pomocí [ověřování Azure Active Directory (AAD)](concepts-aad-authentication.md).


## <a name="threat-protection"></a>Ochrana před hrozbami

Můžete se rozhodnout pro [rozšířenou ochranu před internetovými útoky](concepts-data-access-and-security-threat-protection.md) , která detekuje neobvyklé aktivity indikující neobvyklé a potenciálně nebezpečné pokusy o přístup k serverům nebo jejich zneužití.

[Protokolování auditu](concepts-audit.md) je k dispozici ke sledování aktivity ve vašich databázích. 


## <a name="next-steps"></a>Další kroky
- Povolit pravidla brány firewall pro [IP adresy](concepts-firewall-rules.md) nebo [virtuální sítě](concepts-data-access-and-security-vnet.md)
- Další informace o [ověřování Azure Active Directory](concepts-aad-authentication.md) v Azure Database for PostgreSQL
