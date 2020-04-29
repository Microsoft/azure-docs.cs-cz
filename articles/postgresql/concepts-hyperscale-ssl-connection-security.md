---
title: TLS – Citus (TLS-Scale) – Azure Database for PostgreSQL
description: Pokyny a informace pro konfiguraci Azure Database for PostgreSQL – Citus () a přidružených aplikací pro správné používání připojení TLS.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 8a691b6d3f28800ab1cb38a72f124e950d485084
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80422341"
---
# <a name="configure-tls-in-azure-database-for-postgresql---hyperscale-citus"></a>Konfigurace TLS v Azure Database for PostgreSQL – Citus (škálování)
Klientská aplikace připojení k uzlu koordinátora Citus (Transport Layer Security) vyžaduje protokol TLS (Transport Layer Security), dříve označovaný jako SSL (Secure Sockets Layer) (SSL). Vynucování připojení TLS mezi vaším databázovým serverem a klientskými aplikacemi pomáhá chránit před útoky typu "prostředník-in-the-middle" tím, že šifruje datový proud mezi serverem a vaší aplikací.

## <a name="enforcing-tls-connections"></a>Vynucování připojení TLS
Pro všechny Azure Database for PostgreSQL servery zřízené prostřednictvím Azure Portal je vynucování připojení TLS ve výchozím nastavení povolené. 

Připojovací řetězce, které jsou předem definované v nastavení "připojovací řetězce" na serveru v Azure Portal obsahují také požadované parametry pro společné jazyky pro připojení k databázovému serveru pomocí protokolu TLS. Parametr TLS se liší v závislosti na konektoru, například "SSL = true" nebo "sslmode = vyžadovat" nebo "sslmode = Required" a další variace.

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Ujistěte se, že vaše aplikace nebo architektura podporuje připojení TLS.
Některé aplikační architektury, které používají PostgreSQL pro své databázové služby, během instalace standardně nepovolují protokol TLS. Pokud váš server PostgreSQL vynutil připojení TLS, ale aplikace není nakonfigurovaná pro TLS, aplikace se nemusí podařit připojit k vašemu databázovému serveru. Informace o tom, jak povolit připojení TLS, najdete v dokumentaci k vaší aplikaci.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>Aplikace, které vyžadují ověření certifikátu pro připojení TLS
V některých případech aplikace vyžadují místní soubor certifikátu generovaný ze souboru certifikátu důvěryhodné certifikační autority (. cer) pro zabezpečené připojení. Certifikát pro připojení k Azure Database for PostgreSQL – Citus (škálování) se nachází na adrese https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem. Stáhněte si soubor certifikátu a uložte ho do svého upřednostňovaného umístění.

### <a name="connect-using-psql"></a>Připojení pomocí psql
Následující příklad ukazuje, jak se připojit k uzlu koordinátoru Citus (psql) pomocí nástroje příkazového řádku. Pomocí nastavení `sslmode=verify-full` připojovacího řetězce vyvynuťte ověřování certifikátu TLS. Předat do `sslrootcert` parametru cestu k místnímu souboru certifikátu.

Níže je uveden příklad připojovacího řetězce psql:
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> Potvrďte, že hodnota předaná, aby `sslrootcert` odpovídala cestě k souboru pro certifikát, který jste uložili.

## <a name="next-steps"></a>Další kroky
Zvyšte zabezpečení pomocí [pravidel brány firewall v Azure Database for PostgreSQL – Citus (škálování)](concepts-hyperscale-firewall-rules.md).
