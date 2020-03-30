---
title: Zabezpečení – databáze Azure pro MariaDB
description: Přehled funkcí zabezpečení v Azure Database pro MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 8f41fe1005e96b428337bc73b9d468962a079596
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527822"
---
# <a name="security-in-azure-database-for-mariadb"></a>Zabezpečení ve službě Azure Database for MariaDB

Existuje několik vrstev zabezpečení, které jsou k dispozici k ochraně dat v databázi Azure pro server MariaDB. Tento článek popisuje tyto možnosti zabezpečení.

## <a name="information-protection-and-encryption"></a>Ochrana informací a šifrování

### <a name="in-transit"></a>V tranzitu
Azure Database for MariaDB zabezpečuje vaše data šifrováním dat při přenosu pomocí zabezpečení transportní vrstvy. Šifrování (SSL/TLS) je vynuceno ve výchozím nastavení.

### <a name="at-rest"></a>V klidu
Služba Azure Database for MariaDB používá kryptografický modul ověřený fips 140-2 pro šifrování dat v klidovém stavu. Data, včetně záloh, jsou šifrována na disku, s výjimkou dočasných souborů vytvořených při spouštění dotazů. Služba používá 256bitovou šifru AES, která je součástí šifrování úložiště Azure, a klíče jsou spravované systémem. Šifrování úložiště je vždycky aktivní a není možné ho zakázat.


## <a name="network-security"></a>Zabezpečení sítě
Připojení k databázi Azure pro server MariaDB jsou nejprve směrovány prostřednictvím místní brány. Brána má veřejně přístupnou IP adresu, zatímco IP adresy serveru jsou chráněny. Další informace o bráně naleznete v [článku architektury připojení](concepts-connectivity-architecture.md).  

Nově vytvořená databáze Azure pro server MariaDB má bránu firewall, která blokuje všechna externí připojení. Přestože se dostanou k bráně, není jim povoleno připojit se k serveru. 

### <a name="ip-firewall-rules"></a>Pravidla brány firewall IP
Pravidla brány firewall IP udělují přístup k serverům na základě původní adresy IP každého požadavku. Další informace najdete v [přehledu pravidel brány firewall.](concepts-firewall-rules.md)

### <a name="virtual-network-firewall-rules"></a>Pravidla brány firewall virtuální sítě
Koncové body virtuální síťové služby rozšiřují připojení virtuální sítě přes páteřní síť Azure. Pomocí pravidel virtuální sítě můžete povolit azure databáze pro mariadb server povolit připojení z vybraných podsítí ve virtuální síti. Další informace naleznete v přehledu [koncového bodu služby virtuální sítě](concepts-data-access-security-vnet.md).


## <a name="access-management"></a>Správa přístupu

Při vytváření databáze Azure pro server MariaDB zadáte pověření pro uživatele správce. Tento správce lze použít k vytvoření dalších uživatelů MariaDB.


## <a name="threat-protection"></a>Ochrana před hrozbami

Můžete se přihlásit k [rozšířené ochraně před hrozbami,](concepts-data-access-and-security-threat-protection.md) která detekuje neobvyklé aktivity indikující neobvyklé a potenciálně škodlivé pokusy o přístup k serverům nebo jejich zneužití.

[Protokolování auditu](concepts-audit-logs.md) je k dispozici ke sledování aktivity ve vašich databázích. 


## <a name="next-steps"></a>Další kroky
- Povolení pravidel brány firewall pro [IP adresy](concepts-firewall-rules.md) nebo [virtuální sítě](concepts-data-access-security-vnet.md)