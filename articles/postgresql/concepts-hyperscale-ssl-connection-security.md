---
title: TLS (Transport Layer Security) – Citus (Transport Layer Security) – Azure Database for PostgreSQL
description: Pokyny a informace pro konfiguraci Azure Database for PostgreSQL – Citus () a přidružených aplikací pro správné používání připojení TLS.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/16/2020
ms.openlocfilehash: 659f86a107e4b08db4ec5195635ea32d2260d677
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87071452"
---
# <a name="configure-tls-in-azure-database-for-postgresql---hyperscale-citus"></a>Konfigurace TLS v Azure Database for PostgreSQL – Citus (škálování)
Uzel koordinátora Citus () vyžaduje, aby se klientské aplikace připojovaly pomocí protokolu TLS (Transport Layer Security). Vynucování TLS mezi databázovým serverem a klientskými aplikacemi pomáhá zajistit důvěrnost dat při přenosu. Další nastavení ověřování, která jsou popsaná níže, jsou také chráněná před útoky typu "prostředník-in-the-middle".

## <a name="enforcing-tls-connections"></a>Vynucování připojení TLS
Aplikace používají připojovací řetězec k identifikaci cílové databáze a nastavení připojení. Různí klienti vyžadují různá nastavení. Pokud chcete zobrazit seznam připojovacích řetězců používaných společnými klienty, přečtěte si část **připojovací řetězce** pro skupinu serverů v Azure Portal.

Parametry TLS `ssl` a `sslmode` liší se v závislosti na možnostech konektoru, například `ssl=true` nebo `sslmode=require` nebo `sslmode=required` .

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Ujistěte se, že vaše aplikace nebo architektura podporuje připojení TLS.
Některé aplikační architektury nepovolují TLS ve výchozím nastavení pro připojení PostgreSQL. Bez zabezpečeného připojení ale aplikace se nemůže připojit k uzlu koordinátora Citus (The scaleed). Informace o tom, jak povolit připojení TLS, najdete v dokumentaci k vaší aplikaci.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>Aplikace, které vyžadují ověření certifikátu pro připojení TLS
V některých případech aplikace vyžadují místní soubor certifikátu generovaný ze souboru certifikátu důvěryhodné certifikační autority (. cer) pro zabezpečené připojení. Certifikát pro připojení k Azure Database for PostgreSQL – Citus (škálování) se nachází na adrese https://cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem . Stáhněte si soubor certifikátu a uložte ho do svého upřednostňovaného umístění.

> [!NOTE]
>
> Chcete-li zkontrolovat pravost certifikátu, můžete ověřit otisk otisku SHA-256 pomocí nástroje příkazového řádku OpenSSL:
>
> ```sh
> openssl x509 -in DigiCertGlobalRootCA.crt.pem -noout -sha256 -fingerprint
>
> # should output:
> # 43:48:A0:E9:44:4C:78:CB:26:5E:05:8D:5E:89:44:B4:D8:4F:96:62:BD:26:DB:25:7F:89:34:A4:43:C7:01:61
> ```

### <a name="connect-using-psql"></a>Připojení pomocí psql
Následující příklad ukazuje, jak se připojit k uzlu koordinátoru Citus (psql) pomocí nástroje příkazového řádku. Pomocí `sslmode=verify-full` nastavení připojovacího řetězce vyvynuťte ověřování certifikátu TLS. Předat do parametru cestu k místnímu souboru certifikátu `sslrootcert` .

Níže je uveden příklad připojovacího řetězce psql:
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> Potvrďte, že hodnota předaná, aby `sslrootcert` odpovídala cestě k souboru pro certifikát, který jste uložili.

## <a name="next-steps"></a>Další kroky
Zvyšte zabezpečení pomocí [pravidel brány firewall v Azure Database for PostgreSQL – Citus (škálování)](concepts-hyperscale-firewall-rules.md).
