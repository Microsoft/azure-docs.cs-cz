---
title: SSL – databáze Azure pro PostgreSQL – jeden server
description: Pokyny a informace o konfiguraci připojení SSL pro Azure Database for PostgreSQL – jeden server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 303da4dcb68a79e69254f6610afc0003bf0aa22c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476996"
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql---single-server"></a>Konfigurace připojení SSL v databázi Azure pro PostgreSQL – jeden server

Azure Database for PostgreSQL upřednostňuje připojení klientských aplikací ke službě PostgreSQL pomocí ssl (Secure Sockets Layer). Vynucení připojení SSL mezi databázovým serverem a klientskými aplikacemi pomáhá chránit před útoky man-in-the-middle šifrováním datového proudu mezi serverem a aplikací.

Ve výchozím nastavení je databázová služba PostgreSQL nakonfigurována tak, aby vyžadovala připojení SSL. Pokud klientská aplikace nepodporuje připojení SSL, můžete zakázat vyžadování ssl.

## <a name="enforcing-ssl-connections"></a>Vynucení připojení SSL

Pro všechny servery Azure Database for PostgreSQL zřízené prostřednictvím portálu Azure a rozhraní příkazového příkazu CLI je vynucení připojení SSL ve výchozím nastavení povolené. 

Podobně připojovací řetězce, které jsou předdefinované v nastavení "Připojovací řetězce" pod serverem na webu Azure Portal zahrnují požadované parametry pro běžné jazyky pro připojení k databázovému serveru pomocí protokolu SSL. Parametr SSL se liší v závislosti na konektoru, například "ssl=true" nebo "sslmode=require" nebo "sslmode=required" a dalších variantách.

## <a name="configure-enforcement-of-ssl"></a>Konfigurace vynucení protokolu SSL

Volitelně můžete zakázat vynucení připojení SSL. Microsoft Azure doporučuje vždy povolit **vynutit nastavení připojení SSL** pro rozšířené zabezpečení.

### <a name="using-the-azure-portal"></a>Použití webu Azure Portal

Navštivte server Azure Database for PostgreSQL a klikněte na **Zabezpečení připojení**. Pomocí přepínacího tlačítka můžete povolit nebo zakázat nastavení **vynucení připojení SSL.** Potom klepněte na tlačítko **Uložit**.

![Zabezpečení připojení – zakázat vynucení protokolu SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

Nastavení můžete potvrdit zobrazením stránky **Přehled** a zobrazí se indikátor **vynucení ssl.**

### <a name="using-azure-cli"></a>Použití Azure CLI

Můžete povolit nebo zakázat **ssl vynucování** parametr pomocí `Enabled` nebo `Disabled` hodnoty v příslušných v Azure CLI.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Ujistěte se, že vaše aplikace nebo architektura podporuje připojení SSL

Některé aplikační architektury, které používají PostgreSQL pro své databázové služby nepovolují SSL ve výchozím nastavení během instalace. Pokud váš server PostgreSQL vynucuje připojení SSL, ale aplikace není nakonfigurována pro protokol SSL, může se v aplikaci nepodaří připojit k databázovému serveru. Informace o povolení připojení SSL naleznete v dokumentaci k aplikaci.

## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Aplikace, které vyžadují ověření certifikátu pro připojení SSL

V některých případech aplikace vyžadují místní soubor certifikátu generovaný z důvěryhodného souboru certifikátu certifikační autority (.cer) pro bezpečné připojení. Certifikát pro připojení k azure databázi pro postgresql server je umístěn na adrese https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem. Stáhněte soubor certifikátu a uložte jej do preferovaného umístění.

### <a name="connect-using-psql"></a>Připojení pomocí psql

Následující příklad ukazuje, jak se připojit k serveru PostgreSQL pomocí nástroje příkazového řádku pSQL. Pomocí `sslmode=verify-full` nastavení připojovacího řetězce vynucujte ověření certifikátu SSL. Předajte cestu k `sslrootcert` místnímu souboru certifikátu parametru.

Následující příkaz je příkladem připojovacího řetězce psql:

```shell
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> Zkontrolujte, zda `sslrootcert` hodnota předaná odpovídá cestě k souboru uloženého certifikátu.

## <a name="next-steps"></a>Další kroky

Projděte si různé možnosti připojení aplikací v [knihovnách připojení pro Azure Database for PostgreSQL](concepts-connection-libraries.md).
