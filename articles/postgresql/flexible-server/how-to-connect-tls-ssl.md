---
title: Šifrované připojení pomocí TLS/SSL v Azure Database for PostgreSQL-flexibilním serveru
description: Pokyny a informace o tom, jak se připojit pomocí protokolu TLS/SSL v Azure Database for PostgreSQL flexibilním serveru.
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 0ba04ff14e62cd411515a765dc95ef7a3e72b709
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90935928"
---
# <a name="encrypted-connectivity-using-transport-layer-security-in-azure-database-for-postgresql---flexible-server"></a>Šifrované připojení pomocí Transport Layer Security v Azure Database for PostgreSQL flexibilním serveru

> [!IMPORTANT]
> Azure Database for PostgreSQL – flexibilní Server je ve verzi Preview.

Azure Database for PostgreSQL – flexibilní Server podporuje připojení klientských aplikací ke službě PostgreSQL pomocí protokolu TLS (Transport Layer Security), dříve označovaného jako SSL (Secure Sockets Layer) (SSL). TLS je průmyslový standardní protokol, který zajišťuje šifrovaná síťová připojení mezi databázovým serverem a klientskými aplikacemi, což vám umožní dodržovat požadavky na dodržování předpisů.

Azure Database for PostgreSQL – flexibilní Server podporuje šifrovaná připojení pomocí protokolu TLS 1.2 + a všechna příchozí připojení s TLS 1,0 a TLS 1,1 budou odepřená. Pro všechny flexibilní servery, které jsou vynucená pro připojení TLS, je povolený a protokol TLS/SSL nemůžete pro připojení k flexibilnímu serveru zakázat.

## <a name="applications-that-require-certificate-verification-for-tlsssl-connectivity"></a>Aplikace, které vyžadují ověření certifikátu pro připojení TLS/SSL
V některých případech aplikace vyžadují k zabezpečenému připojení soubor místního certifikátu generovaný ze souboru certifikátu důvěryhodné certifikační autority (CA). Azure Database for PostgreSQL – flexibilní Server používá *globální kořenovou certifikační autoritu DigiCert*. Stáhněte si tento certifikát potřebný ke komunikaci přes SSL z [globální kořenové certifikační autority DigiCert](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) a uložte soubor certifikátu do svého upřednostňovaného umístění. Tento kurz například používá `c:\ssl` .


### <a name="connect-using-psql"></a>Připojení pomocí psql
Pokud jste vytvořili flexibilní Server s *privátním přístupem (Integration VNET)*, budete se muset připojit k serveru z prostředku ve stejné virtuální síti jako váš server. Můžete vytvořit virtuální počítač a přidat ho do virtuální sítě vytvořené pomocí flexibilního serveru.

Pokud jste vytvořili flexibilní Server s *veřejným přístupem (povolenými IP adresami)*, můžete přidat místní IP adresu do seznamu pravidel brány firewall na serveru.

Následující příklad ukazuje, jak se připojit k serveru pomocí rozhraní příkazového řádku psql. Použijte `sslmode=verify-full` nastavení připojovacího řetězce pro vymáhání ověření certifikátu TLS/SSL. Předat do parametru cestu k místnímu souboru certifikátu `sslrootcert` .

```bash
 psql "sslmode=verify-full sslrootcert=c:\ssl\DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=postgres user=myadmin"
```
> [!Note]
> Potvrďte, že hodnota předaná, aby `sslrootcert` odpovídala cestě k souboru pro certifikát, který jste uložili.

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Ujistěte se, že vaše aplikace nebo architektura podporuje připojení TLS.

Některé aplikační architektury, které používají PostgreSQL pro své databázové služby, během instalace standardně nepovolují protokol TLS. Server PostgreSQL vynutil připojení TLS, ale pokud aplikace není nakonfigurovaná pro TLS, aplikace se nemusí podařit připojit k vašemu databázovému serveru. Informace o tom, jak povolit připojení TLS, najdete v dokumentaci k vaší aplikaci.

## <a name="next-steps"></a>Další kroky
- [Vytvářejte a spravujte Azure Database for PostgreSQLově flexibilní serverovou serverovou síť pomocí Azure CLI](./how-to-manage-virtual-network-cli.md).
- Další informace o [sítích v Azure Database for PostgreSQL-flexibilním serveru](./concepts-networking.md)
- Další informace o [Azure Database for PostgreSQL – flexibilní pravidla brány firewall serveru](./concepts-networking.md#public-access-allowed-ip-addresses)
