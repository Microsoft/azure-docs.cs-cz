---
title: TLS-Azure Database for PostgreSQL – jeden server
description: Pokyny a informace o tom, jak nakonfigurovat připojení TLS pro Azure Database for PostgreSQL pro jeden server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: d0482e5205b97b5c57c41e0ba98fb9ca819e5d5f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82141749"
---
# <a name="configure-tls-connectivity-in-azure-database-for-postgresql---single-server"></a>Konfigurace připojení TLS v Azure Database for PostgreSQL – jeden server

Azure Database for PostgreSQL upřednostňuje připojení klientských aplikací ke službě PostgreSQL pomocí protokolu TLS (Transport Layer Security), dříve označovaného jako SSL (Secure Sockets Layer) (SSL). Vynucování připojení TLS mezi vaším databázovým serverem a klientskými aplikacemi pomáhá chránit před útoky typu "prostředník-in-the-middle" tím, že šifruje datový proud mezi serverem a vaší aplikací.

Ve výchozím nastavení je databázová služba PostgreSQL nakonfigurovaná tak, aby vyžadovala připojení TLS. Pokud klientská aplikace nepodporuje připojení TLS, můžete zakázat vyžadování TLS.

## <a name="enforcing-tls-connections"></a>Vynucování připojení TLS

Pro všechny Azure Database for PostgreSQL servery zřízené prostřednictvím Azure Portal a CLI je vynucování připojení TLS ve výchozím nastavení povolené. 

Připojovací řetězce, které jsou předem definované v nastavení "připojovací řetězce" na serveru v Azure Portal obsahují také požadované parametry pro společné jazyky pro připojení k databázovému serveru pomocí protokolu TLS. Parametr TLS se liší v závislosti na konektoru, například "SSL = true" nebo "sslmode = vyžadovat" nebo "sslmode = Required" a další variace.

## <a name="configure-enforcement-of-tls"></a>Konfigurace vynucení TLS

Volitelně můžete zakázat vynucování připojení TLS. Microsoft Azure doporučuje vždycky povolit nastavení **připojení SSL vynutilo** pro zvýšené zabezpečení.

### <a name="using-the-azure-portal"></a>Použití webu Azure Portal

Přejděte na server Azure Database for PostgreSQL a klikněte na **zabezpečení připojení**. Pomocí přepínacího tlačítka povolíte nebo zakážete nastavení **Vynutilí připojení SSL** . Pak klikněte na **Uložit**.

![Zabezpečení připojení – zakázat vymáhání TLS/SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

Nastavení můžete potvrdit zobrazením stránky **Přehled** , kde se zobrazí indikátor **stavu vystavení SSL** .

### <a name="using-azure-cli"></a>Použití Azure CLI

Můžete povolit nebo zakázat parametr **SSL-Enforcement** pomocí `Enabled` hodnot nebo `Disabled` v rozhraní příkazového řádku Azure CLI.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Ujistěte se, že vaše aplikace nebo architektura podporuje připojení TLS.

Některé aplikační architektury, které používají PostgreSQL pro své databázové služby, během instalace standardně nepovolují protokol TLS. Pokud váš server PostgreSQL vynutil připojení TLS, ale aplikace není nakonfigurovaná pro TLS, aplikace se nemusí podařit připojit k vašemu databázovému serveru. Informace o tom, jak povolit připojení TLS, najdete v dokumentaci k vaší aplikaci.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>Aplikace, které vyžadují ověření certifikátu pro připojení TLS

V některých případech aplikace vyžadují místní soubor certifikátu generovaný ze souboru certifikátu důvěryhodné certifikační autority (. cer) pro zabezpečené připojení. Certifikát pro připojení k serveru Azure Database for PostgreSQL se nachází na adrese https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem. Stáhněte si soubor certifikátu a uložte ho do svého upřednostňovaného umístění.

### <a name="connect-using-psql"></a>Připojení pomocí psql

Následující příklad ukazuje, jak se připojit k serveru PostgreSQL pomocí nástroje příkazového řádku psql. Použijte nastavení `sslmode=verify-full` připojovacího řetězce pro vymáhání ověření certifikátu TLS/SSL. Předat do `sslrootcert` parametru cestu k místnímu souboru certifikátu.

Následující příkaz je příkladem připojovacího řetězce psql:

```shell
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> Potvrďte, že hodnota předaná, aby `sslrootcert` odpovídala cestě k souboru pro certifikát, který jste uložili.

## <a name="next-steps"></a>Další kroky

Přečtěte si různé možnosti připojení aplikací v [knihovně připojení pro Azure Database for PostgreSQL](concepts-connection-libraries.md).
