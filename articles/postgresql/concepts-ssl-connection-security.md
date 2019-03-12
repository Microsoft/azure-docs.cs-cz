---
title: Konfigurace připojení SSL ve službě Azure Database for PostgreSQL
description: Pokyny a informace o konfiguraci – Azure Database for PostgreSQL a přidružené aplikace pro správné použití připojení SSL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 5a0fc99052b18dc1fa837147aa914a473d27d832
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2019
ms.locfileid: "57730022"
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql"></a>Konfigurace připojení SSL ve službě Azure Database for PostgreSQL
Azure Database for PostgreSQL upřednostňuje připojení klientských aplikací do služby PostgreSQL pomocí vrstvy SSL (Secure Sockets). Díky vynucování připojení SSL mezi databázovým serverem a klientskými aplikacemi se šifruje datový proud mezi serverem a vaší aplikací, což pomáhá chránit před napadením útočníky, kteří se vydávají za prostředníky.

Ve výchozím nastavení je databázová služba PostgreSQL nakonfigurované vyžadování připojení SSL. Volitelně můžete zakázat, vyžadování SSL pro připojení k vaší databázové službě, pokud klientská aplikace nepodporuje připojení SSL. 

## <a name="enforcing-ssl-connections"></a>Vynucení připojení SSL
Pro všechny – Azure Database for PostgreSQL servery poskytované prostřednictvím webu Azure portal a rozhraní příkazového řádku je standardně povolená vynucení připojení SSL. 

Připojovací řetězce, které jsou předem definované v nastavení "Připojovací řetězce" v rámci vašeho serveru na webu Azure Portal, zahrnout požadované parametry pro běžných jazyků pro připojení k vašemu databázovému serveru pomocí protokolu SSL. Parametr SSL se liší v závislosti na konektoru, třeba "ssl = true" nebo "sslmode = vyžadují" nebo "sslmode = požadované" a další varianty konfigurací.

## <a name="configure-enforcement-of-ssl"></a>Nakonfigurovat vynucení SSL
Volitelně můžete zakázat vynucení připojení SSL. Microsoft Azure doporučuje vždy povolit **vynucování SSL připojení** nastavení pro zvýšení zabezpečení.

### <a name="using-the-azure-portal"></a>Použití webu Azure Portal
Navštivte web vašeho serveru Azure Database for PostgreSQL a klikněte na tlačítko **zabezpečení připojení**. Povolit nebo zakázat pomocí přepínacího tlačítka **vynucování SSL připojení** nastavení. Potom klikněte na **Uložit**. 

![Zabezpečení připojení – zakázat vynucování SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

Toto nastavení můžete ověřit zobrazením **přehled** stránku a podívejte se **stav vynucení SSL** indikátoru.

### <a name="using-azure-cli"></a>Použití Azure CLI
Můžete povolit nebo zakázat **vynucení ssl** pomocí parametru `Enabled` nebo `Disabled` hodnoty v uvedeném pořadí v Azure CLI.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Zkontrolujte vaše aplikace nebo rozhraní framework podporuje připojení SSL
Mnoho běžné systémy aplikací, které používají PostgreSQL pro databázi služby, jako je například Drupal a Django, nepovolujte SSL ve výchozím nastavení při instalaci. Povolení připojení SSL je třeba provést po dokončení instalace nebo prostřednictvím příkazů rozhraní příkazového řádku, které jsou specifické pro aplikaci. Pokud váš server PostgreSQL je vynucení připojení SSL a přidružené aplikace není správně nakonfigurována, aplikace nemusí podařit připojit ke svému databázovému serveru. Další informace o povolení připojení SSL v dokumentaci vaší aplikace.


## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Aplikace, které vyžadují ověření certifikátů pro připojení SSL
V některých případech může aplikace vyžadují soubor místní certifikát vygenerovaný z důvěryhodné certifikační autority (CA) soubor certifikátu (.cer) se navázat zabezpečené připojení. Prohlédněte si následující postup získání souboru .cer, dekódovat certifikát a jeho vazbu na aplikaci.

### <a name="download-the-certificate-file-from-the-certificate-authority-ca"></a>Stáhněte si soubor certifikátu od certifikátu autority (CA) 
Certifikát nutný pro komunikaci pomocí protokolu SSL s využitím Azure Database pro je umístěn PostgreSQL server [tady](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt). Stáhněte si certifikát, který místně.

### <a name="install-a-cert-decoder-on-your-machine"></a>Dekodér certifikát nainstalovat do počítače 
Můžete použít [OpenSSL](https://github.com/openssl/openssl) dekódovat certifikát, který vaše aplikace se navázat zabezpečené připojení k vašemu databázovému serveru potřebuje. Zjistěte, jak nainstalovat OpenSSL, najdete v článku [pokyny k instalaci OpenSSL](https://github.com/openssl/openssl/blob/master/INSTALL). 


### <a name="decode-your-certificate-file"></a>Dekódování váš soubor certifikátu
Stažený soubor kořenové certifikační Autority je v šifrovaném tvaru. Dekódovat certifikát, který používají OpenSSL. Chcete-li tak učinit, spusťte tento příkaz OpenSSL:

```
openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
```

### <a name="connecting-to-azure-database-for-postgresql-with-ssl-certificate-authentication"></a>Připojení ke službě Azure Database for PostgreSQL pomocí ověřování certifikátu SSL
Teď, když jste úspěšně dekódovat certifikát, je možné připojit se ke svému databázovému serveru bezpečně přes protokol SSL. Povolit ověření certifikátu serveru, certifikát musí být umístěn v souboru ~/.postgresql/root.crt domovského adresáře uživatele. (V Microsoft Windows soubor je s názvem % APPDATA%\postgresql\root.crt.). 

#### <a name="connect-using-psql"></a>Připojte se pomocí nástroje psql
Následující příklad ukazuje, jak úspěšně připojit k serveru PostgreSQL pomocí nástroje příkazového řádku psql. Použití `root.crt` soubor vytvořený a `sslmode=verify-ca` nebo `sslmode=verify-full` možnost.

Pomocí rozhraní příkazového řádku PostgreSQL, spusťte následující příkaz:
```bash
psql "sslmode=verify-ca sslrootcert=root.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=mylogin@mydemoserver"
```
V případě úspěchu se zobrazí následující výstup:
```bash
Password for user mylogin@mydemoserver:
psql (9.6.2)
WARNING: Console code page (437) differs from Windows code page (1252)
     8-bit characters might not work correctly. See psql reference
     page "Notes for Windows users" for details.
SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-SHA384, bits: 256, compression: off)
Type "help" for help.

postgres=>
```

## <a name="next-steps"></a>Další postup
Projděte si různé možnosti připojení k aplikaci následující [připojení knihoven pro službu Azure Database for PostgreSQL](concepts-connection-libraries.md).
