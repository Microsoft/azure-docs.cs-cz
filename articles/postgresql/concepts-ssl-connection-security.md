---
title: Konfigurace připojení SSL ve službě Azure Database for PostgreSQL
description: Pokyny a informace o konfiguraci – Azure Database for PostgreSQL a přidružené aplikace pro správné použití připojení SSL.
services: postgresql
author: JasonMAnderson
ms.author: janders
editor: jasonwhowell
manager: kfile
ms.service: postgresql
ms.custom: ''
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: cc8bd25b58cf898169a4d84154f7f3f81966bb92
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49985790"
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

### <a name="download-and-install-openssl-on-your-machine"></a>Stáhněte a nainstalujte OpenSSL na vašem počítači 
Dekódovat certifikát, který pro vaši aplikaci se navázat zabezpečené připojení k vašemu databázovému serveru, musíte nainstalovat OpenSSL, v místním počítači.

#### <a name="for-linux-os-x-or-unix"></a>Pro Linux, OS X nebo Unix
Knihovny OpenSSL jsou k dispozici ve zdrojovém kódu přímo z [OpenSSL Software Foundation](https://www.openssl.org). Postupujte podle následujících pokynů vás provede kroky potřebnými k instalaci OpenSSL na svůj počítač s Linuxem. Tento článek používá příkazy pro práci na Ubuntu 12.04 známé a vyšší.

Otevřete relaci Terminálové a stáhněte OpenSSL.
```bash
wget http://www.openssl.org/source/openssl-1.1.0e.tar.gz
``` 
Extrahujte soubory ze staženého balíčku.
```bash
tar -xvzf openssl-1.1.0e.tar.gz
```
Zadejte adresář, ve kterém byly soubory extrahovány. Ve výchozím nastavení měla by být následujícím způsobem.

```bash
cd openssl-1.1.0e
```
Spuštěním následujícího příkazu nakonfigurujte OpenSSL. Pokud chcete soubory ve složce liší od /usr/local/openssl, ujistěte se, že chcete-li změnit následující podle potřeby.

```bash
./config --prefix=/usr/local/openssl --openssldir=/usr/local/openssl
```
Teď, když OpenSSL je správně nakonfigurovaný, budete muset zkompilovat jej k převodu vašeho certifikátu. Chcete-li zkompilovat, spusťte následující příkaz:

```bash
make
```
Po dokončení sestavení jste připraveni nainstalovat OpenSSL, jako spustitelný soubor spuštěním následujícího příkazu:
```bash
make install
```
Pokud chcete potvrdit, že jste úspěšně nainstalovat OpenSSL ve vašem systému, spusťte následující příkaz a zkontrolujte, že získáte stejný výstup.

```bash
/usr/local/openssl/bin/openssl version
```
V případě úspěšného ověření by se zobrazit následující zpráva.
```bash
OpenSSL 1.1.0e 7 Apr 2014
```

#### <a name="for-windows"></a>Pro Windows
Instalace OpenSSL na počítače s Windows můžete udělat následujícími způsoby:
1. **(Doporučeno)**  Pomocí integrované funkce Bash pro Windows ve Windows 10 a novější, je ve výchozím nastavení nainstalované OpenSSL. Pokyny o tom, jak povolit funkci Bash pro Windows ve Windows 10 najdete [tady](https://msdn.microsoft.com/commandline/wsl/install_guide).
2. Prostřednictvím stahování aplikace typu Win32/64 poskytovaných komunitou. Když OpenSSL Software Foundation neposkytuje ani neschvaluje žádné konkrétní instalačních programů Windows, poskytují seznam k dispozici instalační programy [tady](https://wiki.openssl.org/index.php/Binaries).

### <a name="decode-your-certificate-file"></a>Dekódování váš soubor certifikátu
Stažený soubor kořenové certifikační Autority je v šifrovaném tvaru. Dekódovat certifikát, který používají OpenSSL. Chcete-li tak učinit, spusťte tento příkaz OpenSSL:

```dos
openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
```

### <a name="connecting-to-azure-database-for-postgresql-with-ssl-certificate-authentication"></a>Připojení ke službě Azure Database for PostgreSQL pomocí ověřování certifikátu SSL
Teď, když jste úspěšně dekódovat certifikát, je možné připojit se ke svému databázovému serveru bezpečně přes protokol SSL. Povolit ověření certifikátu serveru, certifikát musí být umístěn v souboru ~/.postgresql/root.crt domovského adresáře uživatele. (V Microsoft Windows soubor je s názvem % APPDATA%\postgresql\root.crt.). Následující část obsahuje pokyny pro připojení k Azure Database for PostgreSQL.

#### <a name="using-psql-command-line-utility"></a>Pomocí příkazového řádku psql
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

#### <a name="using-pgadmin-gui-tool"></a>Pomocí nástroje pgAdmin grafického uživatelského rozhraní
Konfigurace nástroje pgAdmin 4 se navázat zabezpečené připojení přes protokol SSL vyžaduje vám umožní nastavit `SSL mode = Verify-CA` nebo `SSL mode = Verify-Full` následujícím způsobem:

![Snímek obrazovky režimu SSL nástroje pgAdmin - connection - vyžadují](./media/concepts-ssl-connection-security/2-pgadmin-ssl.png)

## <a name="next-steps"></a>Další postup
Projděte si různé možnosti připojení k aplikaci následující [připojení knihoven pro službu Azure Database for PostgreSQL](concepts-connection-libraries.md).
