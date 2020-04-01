---
title: TLS – hyperškálování (Citus) – databáze Azure pro PostgreSQL
description: Pokyny a informace ke konfiguraci Azure Database pro PostgreSQL – Hyperscale (Citus) a přidružené aplikace správně používat připojení TLS.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 8a691b6d3f28800ab1cb38a72f124e950d485084
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422341"
---
# <a name="configure-tls-in-azure-database-for-postgresql---hyperscale-citus"></a>Konfigurace TLS v databázi Azure pro PostgreSQL – hyperškálování (Citus)
Připojení klientských aplikací k uzlu koordinátora Hyperscale (Citus) vyžadují zabezpečení transportní vrstvy (TLS), dříve známé jako SSL (Secure Sockets Layer). Vynucení připojení TLS mezi databázovým serverem a klientskými aplikacemi pomáhá chránit před útoky man-in-the-middle šifrováním datového proudu mezi serverem a aplikací.

## <a name="enforcing-tls-connections"></a>Vynucení připojení TLS
Pro všechny servery Azure Database for PostgreSQL zřízené prostřednictvím portálu Azure je ve výchozím nastavení povoleno vynucení připojení TLS. 

Podobně připojovací řetězce, které jsou předdefinované v nastavení "Připojovací řetězce" pod serverem na webu Azure Portal zahrnují požadované parametry pro běžné jazyky pro připojení k databázovému serveru pomocí TLS. Parametr TLS se liší v závislosti na konektoru, například "ssl=true" nebo "sslmode=require" nebo "sslmode=required" a dalších variantách.

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Ujistěte se, že vaše aplikace nebo architektura podporuje připojení TLS
Některé aplikační architektury, které používají PostgreSQL pro své databázové služby neumožňují TLS ve výchozím nastavení během instalace. Pokud váš server PostgreSQL vynucuje připojení TLS, ale aplikace není nakonfigurována pro TLS, může se v aplikaci nepodaří připojit k databázovému serveru. Informace o povolení připojení TLS naleznete v dokumentaci k aplikaci.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>Aplikace, které vyžadují ověření certifikátu pro připojení TLS
V některých případech aplikace vyžadují místní soubor certifikátu generovaný z důvěryhodného souboru certifikátu certifikační autority (.cer) pro bezpečné připojení. Certifikát pro připojení k databázi Azure pro PostgreSQL – Hyperscale https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem(Citus) se nachází na adrese . Stáhněte soubor certifikátu a uložte jej do preferovaného umístění.

### <a name="connect-using-psql"></a>Připojení pomocí psql
Následující příklad ukazuje, jak se připojit k uzlu koordinátora Hyperscale (Citus) pomocí nástroje příkazového řádku psql. Pomocí `sslmode=verify-full` nastavení připojovacího řetězce vynucujte ověření certifikátu TLS. Předajte cestu k `sslrootcert` místnímu souboru certifikátu parametru.

Níže je uveden příklad připojovacího řetězce psql:
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> Zkontrolujte, zda `sslrootcert` hodnota předaná odpovídá cestě k souboru uloženého certifikátu.

## <a name="next-steps"></a>Další kroky
Zvyšte zabezpečení pomocí [pravidel brány firewall v Azure Database for PostgreSQL – Hyperscale (Citus).](concepts-hyperscale-firewall-rules.md)
