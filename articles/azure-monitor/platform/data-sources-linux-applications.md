---
title: Shromažďovat výkon aplikací pro Linux v Azure Monitor | Microsoft Docs
description: Tento článek poskytuje podrobné informace o konfiguraci Log Analytics agenta pro Linux ke shromáždění čítačů výkonu pro MySQL a Server HTTP s Apache.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/04/2017
ms.openlocfilehash: 2fd148dbb85a4fd60fe63d4fb73128bf92dea1d8
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670555"
---
# <a name="collect-performance-counters-for-linux-applications-in-azure-monitor"></a>Shromažďování čítačů výkonu pro aplikace pro Linux v Azure Monitor 
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]
Tento článek poskytuje podrobné informace o konfiguraci [Log Analytics agenta pro Linux](https://github.com/Microsoft/OMS-Agent-for-Linux) ke shromažďování čítačů výkonu pro konkrétní aplikace do Azure monitor.  Mezi aplikace, které jsou součástí tohoto článku, patří:  

- [MySQL](#mysql)
- [Server Apache HTTP](#apache-http-server)

## <a name="mysql"></a>MySQL
Pokud se v počítači při instalaci agenta Log Analytics zjistí server MySQL nebo server MariaDB, automaticky se nainstaluje poskytovatel monitorování výkonu pro MySQL server. Tento zprostředkovatel se připojí k místnímu serveru MySQL/MariaDB a zveřejní statistiku výkonu. Přihlašovací údaje uživatele MySQL musí být nakonfigurovány tak, aby poskytovatel mohl získat přístup k serveru MySQL.

### <a name="configure-mysql-credentials"></a>Konfigurace přihlašovacích údajů MySQL
Poskytovatel MySQL OMI vyžaduje předem nakonfigurovanýho uživatele MySQL a nainstalovaná klientské knihovny MySQL, aby se dotazoval na informace o výkonu a stavu z instance MySQL.  Tyto přihlašovací údaje jsou uložené v ověřovacím souboru, který je uložený v agentovi Linux.  Ověřovací soubor určuje, která adresa BIND a port má instance MySQL naslouchat a jaké přihlašovací údaje se mají použít ke shromáždění metrik.  

Během instalace agenta Log Analytics pro Linux bude poskytovatel MySQL OMI kontrolovat konfigurační soubory MySQL my. CNF (výchozí umístění) pro adresu BIND a port a částečně nastavili soubor s ověřováním MySQL OMI.

Ověřovací soubor MySQL je uložený na `/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth`.


### <a name="authentication-file-format"></a>Formát ověřovacího souboru
Toto je formát souboru OMI pro ověřování MySQL.

    [Port]=[Bind-Address], [username], [Base64 encoded Password]
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    AutoUpdate=[true|false]

Položky v ověřovacím souboru jsou popsány v následující tabulce.

| Vlastnost | Popis |
|:--|:--|
| Port | Představuje aktuální port, na kterém naslouchá instance MySQL. Port 0 určuje, zda jsou následující vlastnosti použity pro výchozí instanci. |
| Adresa vazby| Aktuální adresa BIND pro MySQL. |
| uživatelské jméno| Uživatel MySQL, který slouží k monitorování instance serveru MySQL. |
| Heslo kódované v kódování Base64| Heslo uživatele pro monitorování MySQL kódovaného ve formátu base64. |
| AutoUpdate| Určuje, jestli se mají znovu vyhledat změny v souboru my. CNF a přepsat ověřovací soubor MySQL OMI při upgradu poskytovatele MySQL OMI. |

### <a name="default-instance"></a>Výchozí instance
Soubor s ověřováním MySQL OMI může definovat výchozí instanci a číslo portu, aby bylo snazší spravovat více instancí MySQL na jednom hostiteli systému Linux.  Výchozí instance je označena instancí s portem 0. Všechny další instance zdědí vlastnosti nastavené z výchozí instance, pokud nezadávají jiné hodnoty. Pokud se například přidá instance MySQL, která naslouchá na portu 3308, použije se k pokusu o monitorování instance, která naslouchá na 3308, a použije se výchozí adresa BIND, uživatelské jméno a heslo kódovaná pomocí kódování Base64. Pokud je instance na 3308 vázána na jinou adresu a používá stejné dvojice uživatelského jména a hesla MySQL, je vyžadována pouze adresa vazby a ostatní vlastnosti budou děděny.

Následující tabulka obsahuje příklad nastavení instance. 

| Popis | Soubor |
|:--|:--|
| Výchozí instance a instance s portem 3308. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=, ,`<br>`AutoUpdate=true` |
| Výchozí instance a instance s portem 3308 a jiným uživatelským jménem a heslem. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=127.0.1.1, myuser2,cGluaGVhZA==`<br>`AutoUpdate=true` |


### <a name="mysql-omi-authentication-file-program"></a>Program pro ověřování MySQL OMI
Součástí instalace poskytovatele MySQL OMI je programový soubor MySQL OMI Authentication, který se dá použít k úpravě souboru MySQL OMI Authentication. Ověřovací soubor aplikace najdete v následujícím umístění.

    /opt/microsoft/mysql-cimprov/bin/mycimprovauth

> [!NOTE]
> Účet omsagent musí číst soubor s přihlašovacími údaji. Spouští se příkaz mycimprovauth, protože se doporučuje omsgent.

Následující tabulka uvádí podrobnosti o syntaxi pro použití mycimprovauth.

| Operace | Příklad | Popis
|:--|:--|:--|
| AutoUpdate *false nebo true* | mycimprovauth AutoUpdate – NEPRAVDA | Nastaví, jestli se má soubor ověření automaticky aktualizovat při restartování nebo aktualizaci. |
| výchozí *přihlašovací heslo pro adresu BIND* | mycimprovauth výchozí adresa 127.0.0.1 root PWD | Nastaví výchozí instanci v souboru OMI pro ověřování MySQL.<br>Pole heslo by mělo být zadáno v prostém textu – heslo v souboru OMI pro ověřování MySQL bude obsahovat kódování Base 64. |
| Odstranit *výchozí nebo port_num* | mycimprovauth 3308 | Odstraní zadanou instanci buď ve výchozím nastavení, nebo podle čísla portu. |
| Nápověda | mycimprov – Help | Vytiskne seznam příkazů, které se mají použít. |
| Tisk | mycimprov tisk | Vytiskne snadno čitelný soubor OMI pro ověřování MySQL. |
| aktualizovat port_num *heslo uživatelského jména pro adresu BIND* | mycimprov aktualizace 3307 127.0.0.1 root PWD | Aktualizuje zadanou instanci nebo přidá instanci, pokud neexistuje. |

Následující příklady příkazů definují výchozí uživatelský účet pro server MySQL na localhost.  Pole heslo by mělo být zadáno jako prostý text – heslo v souboru OMI pro ověřování MySQL bude obsahovat kódování Base 64

    sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>'
    sudo /opt/omi/bin/service_control restart

### <a name="database-permissions-required-for-mysql-performance-counters"></a>Oprávnění databáze požadovaná pro čítače výkonu MySQL
Uživatel MySQL vyžaduje přístup k následujícím dotazům pro shromáždění dat o výkonu serveru MySQL. 

    SHOW GLOBAL STATUS;
    SHOW GLOBAL VARIABLES:


Uživatel MySQL také vyžaduje, abyste vybrali přístup k následujícím výchozím tabulkám.

- information_schema
- MySQL. 

Tato oprávnění se dají udělit spuštěním následujících příkazů pro udělení.

    GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
    GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;


> [!NOTE]
> Chcete-li udělit oprávnění uživateli pro monitorování MySQL, musí mít uživatel grant oprávnění pro udělení oprávnění a také pro udělená oprávnění.

### <a name="define-performance-counters"></a>Definovat čítače výkonu

Jakmile nakonfigurujete Log Analytics agenta pro Linux, aby odesílal data do Azure Monitor, musíte nakonfigurovat čítače výkonu, které se mají shromažďovat.  Použijte postup v části [zdroje dat o výkonu systému Windows a Linux v Azure monitor](data-sources-performance-counters.md) s čítači v následující tabulce.

| Název objektu | Název čítače |
|:--|:--|
| MySQL Database | Místo na disku v bajtech |
| MySQL Database | Tabulky |
| Server MySQL | Procento přerušení připojení |
| Server MySQL | Připojení pomocí protokolu PCT |
| Server MySQL | Využití místa na disku v bajtech |
| Server MySQL | Úplná kontrola tabulky PCT |
| Server MySQL | InnoDB procento přístupů fondu vyrovnávací paměti |
| Server MySQL | InnoDB fondu vyrovnávací paměti použít PCT |
| Server MySQL | InnoDB fondu vyrovnávací paměti použít PCT |
| Server MySQL | Procento přístupů do mezipaměti klíčů |
| Server MySQL | Použití protokolu PCT pro mezipaměť klíčů |
| Server MySQL | Protokol PCT zápisu do mezipaměti klíčů |
| Server MySQL | Procento přístupů do mezipaměti dotazů |
| Server MySQL | Mezipaměť dotazů vyřadí PCT |
| Server MySQL | Použití protokolu PCT v mezipaměti dotazů |
| Server MySQL | Procento přístupů do mezipaměti tabulky |
| Server MySQL | Mezipaměť tabulky používá protokol PCT |
| Server MySQL | Procento kolizí zámku tabulky |

## <a name="apache-http-server"></a>Apache HTTP Server 
Pokud se v počítači při instalaci balíčku omsagent zjistí server Apache HTTP, automaticky se nainstaluje poskytovatel monitorování výkonu pro server Apache HTTP. Tento zprostředkovatel spoléhá na modul Apache, který musí být načten do serveru protokolu HTTP Apache, aby bylo možné získat přístup k datům o výkonu. Modul lze načíst pomocí následujícího příkazu:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

Chcete-li uvolnit modul monitorování Apache, spusťte následující příkaz:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```

### <a name="define-performance-counters"></a>Definovat čítače výkonu

Jakmile nakonfigurujete Log Analytics agenta pro Linux, aby odesílal data do Azure Monitor, musíte nakonfigurovat čítače výkonu, které se mají shromažďovat.  Použijte postup v části [zdroje dat o výkonu systému Windows a Linux v Azure monitor](data-sources-performance-counters.md) s čítači v následující tabulce.

| Název objektu | Název čítače |
|:--|:--|
| Apache HTTP Server | Zaneprázdněné pracovní procesy |
| Apache HTTP Server | Nečinné pracovní procesy |
| Apache HTTP Server | Pracovní procesy s hodnotou PCT |
| Apache HTTP Server | Celkový procesor protokolu PCT |
| Apache Virtual Host | Chyby za minutu – klient |
| Apache Virtual Host | Chyby za minutu – Server |
| Apache Virtual Host | Počet KB na požadavek |
| Apache Virtual Host | Počet požadavků v KILOBAJTech za sekundu |
| Apache Virtual Host | Počet požadavků za sekundu |



## <a name="next-steps"></a>Další kroky
* [Shromáždí čítače výkonu](data-sources-performance-counters.md) od agentů systému Linux.
* Přečtěte si o [dotazech protokolů](../log-query/log-query-overview.md) , které analyzují data shromážděná ze zdrojů dat a řešení. 
