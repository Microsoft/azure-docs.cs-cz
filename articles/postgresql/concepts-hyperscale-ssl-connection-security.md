---
title: SSL – hyperškálování (Citus) – databáze Azure pro PostgreSQL
description: Pokyny a informace ke konfiguraci Azure Database pro PostgreSQL – Hyperscale (Citus) a přidružené aplikace správně používat připojení SSL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 3e4ef5d2d6db3a3d4f8923f47079f2484639a751
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973981"
---
# <a name="configure-ssl-in-azure-database-for-postgresql---hyperscale-citus"></a>Konfigurace protokolu SSL v databázi Azure pro PostgreSQL – hyperškálování (Citus)
Připojení klientských aplikací k uzlu koordinátora Hyperscale (Citus) vyžadují ssl (Secure Sockets Layer). Vynucení připojení SSL mezi databázovým serverem a klientskými aplikacemi pomáhá chránit před útoky man-in-the-middle šifrováním datového proudu mezi serverem a aplikací.

## <a name="enforcing-ssl-connections"></a>Vynucení připojení SSL
Pro všechny servery Azure Database for PostgreSQL zřízené prostřednictvím portálu Azure je vynucení připojení SSL ve výchozím nastavení povolené. 

Podobně připojovací řetězce, které jsou předdefinované v nastavení "Připojovací řetězce" pod serverem na webu Azure Portal zahrnují požadované parametry pro běžné jazyky pro připojení k databázovému serveru pomocí protokolu SSL. Parametr SSL se liší v závislosti na konektoru, například "ssl=true" nebo "sslmode=require" nebo "sslmode=required" a dalších variantách.

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Ujistěte se, že vaše aplikace nebo architektura podporuje připojení SSL
Některé aplikační architektury, které používají PostgreSQL pro své databázové služby nepovolují SSL ve výchozím nastavení během instalace. Pokud váš server PostgreSQL vynucuje připojení SSL, ale aplikace není nakonfigurována pro protokol SSL, může se v aplikaci nepodaří připojit k databázovému serveru. Informace o povolení připojení SSL naleznete v dokumentaci k aplikaci.

## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Aplikace, které vyžadují ověření certifikátu pro připojení SSL
V některých případech aplikace vyžadují místní soubor certifikátu generovaný z důvěryhodného souboru certifikátu certifikační autority (.cer) pro bezpečné připojení. Certifikát pro připojení k databázi Azure pro PostgreSQL – Hyperscale https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem(Citus) se nachází na adrese . Stáhněte soubor certifikátu a uložte jej do preferovaného umístění.

### <a name="connect-using-psql"></a>Připojení pomocí psql
Následující příklad ukazuje, jak se připojit k uzlu koordinátora Hyperscale (Citus) pomocí nástroje příkazového řádku psql. Pomocí `sslmode=verify-full` nastavení připojovacího řetězce vynucujte ověření certifikátu SSL. Předajte cestu k `sslrootcert` místnímu souboru certifikátu parametru.

Níže je uveden příklad připojovacího řetězce psql:
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> Zkontrolujte, zda `sslrootcert` hodnota předaná odpovídá cestě k souboru uloženého certifikátu.

## <a name="next-steps"></a>Další kroky
Zvyšte zabezpečení pomocí [pravidel brány firewall v Azure Database for PostgreSQL – Hyperscale (Citus).](concepts-hyperscale-firewall-rules.md)
