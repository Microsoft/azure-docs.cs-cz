---
title: Zabezpečení v databázi Azure pro PostgreSQL – jeden server
description: Přehled funkcí zabezpečení v Azure Database for PostgreSQL – single server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: 02dc9e1ad9ee46b1a400e44b6ef737e70571a17a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972589"
---
# <a name="security-in-azure-database-for-postgresql---single-server"></a>Zabezpečení v databázi Azure pro PostgreSQL – jeden server

Existuje několik vrstev zabezpečení, které jsou k dispozici k ochraně dat na azure database pro postgresql server. Tento článek popisuje tyto možnosti zabezpečení.

## <a name="information-protection-and-encryption"></a>Ochrana informací a šifrování

### <a name="in-transit"></a>V tranzitu
Azure Database for PostgreSQL zabezpečuje vaše data šifrováním dat při přenosu pomocí zabezpečení transportní vrstvy. Šifrování (SSL/TLS) je vynuceno ve výchozím nastavení.

### <a name="at-rest"></a>V klidu
Služba Azure Database for PostgreSQL používá kryptografický modul fips 140-2 pro šifrování dat v klidovém stavu. Data, včetně záloh, jsou šifrována na disku, s výjimkou dočasných souborů vytvořených při spouštění dotazů. Služba používá 256bitovou šifru AES, která je součástí šifrování úložiště Azure, a klíče jsou spravované systémem. Šifrování úložiště je vždycky aktivní a není možné ho zakázat.


## <a name="network-security"></a>Zabezpečení sítě
Připojení k databázi Azure pro postgreSQL server jsou nejprve směrovány přes místní bránu. Brána má veřejně přístupnou IP adresu, zatímco IP adresy serveru jsou chráněny. Další informace o bráně naleznete v [článku architektury připojení](concepts-connectivity-architecture.md).  

Nově vytvořená databáze Azure pro postgreSQL server má bránu firewall, která blokuje všechna externí připojení. Přestože se dostanou k bráně, není jim povoleno připojit se k serveru. 

### <a name="ip-firewall-rules"></a>Pravidla brány firewall IP
Pravidla brány firewall IP udělují přístup k serverům na základě původní adresy IP každého požadavku. Další informace najdete v [přehledu pravidel brány firewall.](concepts-firewall-rules.md)

### <a name="virtual-network-firewall-rules"></a>Pravidla brány firewall virtuální sítě
Koncové body virtuální síťové služby rozšiřují připojení virtuální sítě přes páteřní síť Azure. Pomocí pravidel virtuální sítě můžete povolit Azure Database for PostgreSQL server povolit připojení z vybraných podsítí ve virtuální síti. Další informace naleznete v přehledu [koncového bodu služby virtuální sítě](concepts-data-access-and-security-vnet.md).

### <a name="private-ip"></a>Privátní IP adresa
Private Link umožňuje připojení k databázi Azure pro postgreSQL jeden server v Azure přes privátní koncový bod. Azure Private Link v podstatě přináší služby Azure uvnitř vaší privátní virtuální sítě (VNet). K prostředkům PaaS lze přistupovat pomocí privátní IP adresy, stejně jako jakýkoli jiný prostředek ve virtuální síti. Další informace naleznete v přehledu [soukromých odkazů](concepts-data-access-and-security-private-link.md)


## <a name="access-management"></a>Správa přístupu

Při vytváření azure databáze pro postgresql server, zadáte pověření pro roli správce. Tuto roli správce lze použít k vytvoření [dalších rolí PostgreSQL](https://www.postgresql.org/docs/current/user-manag.html).

K serveru se můžete připojit také pomocí [ověřování služby Azure Active Directory (AAD).](concepts-aad-authentication.md)


## <a name="threat-protection"></a>Ochrana před hrozbami

Můžete se přihlásit k [rozšířené ochraně před hrozbami,](concepts-data-access-and-security-threat-protection.md) která detekuje neobvyklé aktivity indikující neobvyklé a potenciálně škodlivé pokusy o přístup k serverům nebo jejich zneužití.

[Protokolování auditu](concepts-audit.md) je k dispozici ke sledování aktivity ve vašich databázích. 


## <a name="next-steps"></a>Další kroky
- Povolení pravidel brány firewall pro [IP adresy](concepts-firewall-rules.md) nebo [virtuální sítě](concepts-data-access-and-security-vnet.md)
- Informace o [ověřování Azure Active Directory](concepts-aad-authentication.md) v Azure Database for PostgreSQL
