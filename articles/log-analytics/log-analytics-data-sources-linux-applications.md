---
title: Shromažďování výkonu aplikace Linux v OMS Log Analytics | Microsoft Docs
description: Tento článek obsahuje podrobné informace pro konfiguraci agenta OMS pro Linux ke shromažďování čítače výkonu pro MySQL a serveru Apache HTTP Server.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: magoedte
ms.openlocfilehash: 04ea6f728e59ec8b47e54fe45e1adc6cbbfb85ff
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="collect-performance-counters-for-linux-applications-in-log-analytics"></a>Shromáždit čítače výkonu pro Linux aplikace v analýzy protokolů 
Tento článek obsahuje podrobné informace pro konfiguraci [OMS agenta pro Linux](https://github.com/Microsoft/OMS-Agent-for-Linux) ke shromažďování čítače výkonu pro konkrétní aplikace.  Aplikace, zahrnuté v tomto článku jsou:  

- [MySQL](#MySQL)
- [Apache HTTP Server](#apache-http-server)

## <a name="mysql"></a>MySQL
Pokud MySQL nebo MariaDB serveru je zjištěn v počítači, pokud je nainstalován OMS agent, se automaticky nainstaluje zprostředkovatele pro Server databáze MySQL monitorování výkonu. Tento zprostředkovatel připojí k místnímu serveru MySQL nebo MariaDB vystavit statistiku výkonu. Přihlašovací údaje uživatele MySQL musí nakonfigurovat tak, aby zprostředkovatel můžete přístup k serveru databáze MySQL.

### <a name="configure-mysql-credentials"></a>Konfigurovat přihlašovací údaje databáze MySQL
Zprostředkovatel MySQL OMI vyžaduje předkonfigurované uživatele MySQL a nainstalovat MySQL klientské knihovny k dotazování na výkon a informace o stavu z instance databáze MySQL.  Tyto přihlašovací údaje jsou uložené v souboru ověřování, který je uložený na agenta systému Linux.  Soubor ověřování určuje, jaké vazby adresu a port instance databáze MySQL naslouchá na a jaké přihlašovací údaje se má použít ke shromažďování metrik.  

Během instalace agenta OMS pro Linux MySQL OMI zprostředkovatele vyhledá MySQL my.cnf konfigurační soubory (výchozí umístění) pro vazbu adresy a portu a částečně nastavit MySQL OMI soubor pro ověření.

MySQL ověřování je uložen v `/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth`.


### <a name="authentication-file-format"></a>Formát souboru ověřování
Toto je formát pro ověření souboru MySQL OMI

    [Port]=[Bind-Address], [username], [Base64 encoded Password]
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    AutoUpdate=[true|false]

Položky v souboru ověřování jsou popsané v následující tabulce.

| Vlastnost | Popis |
|:--|:--|
| Port | Představuje aktuální port, který naslouchá instance databáze MySQL na. Port 0 určuje, že jsou pro výchozí instanci používat následující vlastnosti. |
| Vazby – adresy| Adresa-aktuální MySQL vazby. |
| uživatelské jméno| Uživatel MySQL lze použít k monitorování instance serveru MySQL. |
| Kódováním base64, pomocí hesla| Heslo uživatele monitorování MySQL kódovaný jako Base64. |
| AutoUpdate| Určuje, jestli se znovu zkontrolujte změny v souboru my.cnf a při upgraduje se zprostředkovatel OMI MySQL přepsat soubor MySQL OMI ověřování. |

### <a name="default-instance"></a>Výchozí instance
Soubor MySQL OMI ověřování můžete definovat výchozí instance a číslo portu provádět správu více instancí databáze MySQL na jednom hostiteli systému Linux jednodušší.  Instance s portem 0 je označený jako výchozí instanci. Všechny další instance zdědí vlastnosti nastavit z výchozí instanci, pokud určí různé hodnoty. Například pokud je přidána instance databáze MySQL naslouchání na portu '3308', vazby adresu výchozí instance, uživatelské jméno a heslo kódováním Base64 použije sledovat instance naslouchá na 3308 a zkuste to. Pokud instanci na 3308 je vázána na jinou adresu a používá stejný MySQL dvojici uživatelské jméno a heslo je potřeba jenom adresy vazby a zdědí vlastnosti.

Následující tabulka obsahuje příklad nastavení instance 

| Popis | File |
|:--|:--|
| Výchozí instance a instanci s portem 3308. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=, ,`<br>`AutoUpdate=true` |
| Výchozí instance a instanci s portem 3308 a jiné uživatelské jméno a heslo. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=127.0.1.1, myuser2,cGluaGVhZA==`<br>`AutoUpdate=true` |


### <a name="mysql-omi-authentication-file-program"></a>MySQL OMI ověřování souboru programu
Součástí instalace zprostředkovatele MySQL OMI je MySQL OMI soubor programu ověřování, který můžete použít k úpravě souboru MySQL OMI ověřování. Soubor programu ověřování najdete v následujícím umístění.

    /opt/microsoft/mysql-cimprov/bin/mycimprovauth

> [!NOTE]
> Soubor s přihlašovacími údaji musí být účet omsagent. Doporučuje se spuštěním příkazu mycimprovauth jako omsgent.

Následující tabulka obsahuje podrobnosti o syntaxi použití mycimprovauth.

| Operace | Příklad: | Popis
|:--|:--|:--|
| pomocí funkce Automatické aktualizace *false\|true* | false mycimprovauth automatických aktualizací | Nastaví, jestli se automaticky aktualizuje soubor ověřování na restartovat nebo aktualizovat. |
| výchozí *vazby adresu uživatelské jméno, heslo* | mycimprovauth výchozí 127.0.0.1 kořenové pwd | Nastaví výchozí instance databáze MySQL OMI soubor pro ověření.<br>Pole hesla by měly být zadány ve formátu prostého textu – heslo v souboru MySQL OMI ověřování bude kódováním Base 64. |
| Odstranit *výchozí\|port_num* | mycimprovauth 3308 | Odstraní zadanou instanci buď výchozí nebo číslo portu. |
| nápověda | mycimprov nápovědy | Vytiskne seznam příkazů použít. |
| Tisk | mycimprov tisku | Vytiskne snadno MySQL OMI číst soubor pro ověření. |
| Aktualizovat port_num *vazby adresu uživatelské jméno, heslo* | mycimprov aktualizace 3307 127.0.0.1 kořenové pwd | Aktualizuje zadaný instanci nebo přidá instanci, pokud neexistuje. |

Příkazy v následujícím příkladu definovat výchozí uživatelského účtu pro server databáze MySQL na localhost.  Pole hesla by měly být zadány ve formátu prostého textu – heslo v souboru MySQL OMI ověřování bude kódováním Base 64

    sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>'
    sudo /opt/omi/bin/service_control restart

### <a name="database-permissions-required-for-mysql-performance-counters"></a>Databáze oprávnění vyžadovaná pro čítače výkonu databáze MySQL
Uživatel MySQL vyžaduje přístup k následující dotazy ke shromažďování dat výkonu serveru MySQL. 

    SHOW GLOBAL STATUS;
    SHOW GLOBAL VARIABLES:


Uživatel MySQL taky vyžaduje vyberte přístup k následující výchozí tabulky.

- information_schema
- mysql. 

Tato oprávnění lze udělit spuštěním následujících příkazů grant.

    GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
    GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;


> [!NOTE]
> Udělte oprávnění MySQL monitorování uživatel poskytující uživatel musí mít oprávnění k 'GRANT option, a také udělení oprávnění.

### <a name="define-performance-counters"></a>Definování čítače výkonu

Po dokončení konfigurace agenta OMS pro Linux k odesílání dat k analýze protokolů, je nutné nakonfigurovat shromažďování čítačů výkonu.  Pomocí postupu v [Windows a Linux zdroje dat výkonu v analýzy protokolů](log-analytics-data-sources-windows-events.md) s čítači v následující tabulce.

| Název objektu | Název čítače |
|:--|:--|
| Databáze MySQL | Místo na disku v bajtech |
| Databáze MySQL | Tabulky |
| MySQL Server | Přerušené připojení Pct |
| MySQL Server | Připojení pomocí protokolu Pct |
| MySQL Server | Využití místa na disku v bajtech |
| MySQL Server | Tabulka úplné prohledávání Pct |
| MySQL Server | Fondu vyrovnávací paměti InnoDB dosáhl Pct |
| MySQL Server | Vyrovnávací paměť InnoDB fondu použití protokolu Pct |
| MySQL Server | Vyrovnávací paměť InnoDB fondu použití protokolu Pct |
| MySQL Server | Protokol Pct požadavků klíče mezipaměti |
| MySQL Server | Klíče mezipaměti použití protokolu Pct |
| MySQL Server | Protokol Pct zápisu klíče mezipaměti |
| MySQL Server | Pct přístupů do mezipaměti dotazu |
| MySQL Server | Dotaz mezipaměti vyřadí Pct |
| MySQL Server | Dotaz mezipaměti použití protokolu Pct |
| MySQL Server | Protokol Pct vstupů do mezipaměti tabulky |
| MySQL Server | Tabulka mezipaměti použití protokolu Pct |
| MySQL Server | Tabulka zámku kolizí Pct |

## <a name="apache-http-server"></a>Apache HTTP Server 
Pokud serveru Apache HTTP Server je v počítači nalezen, když je nainstalovaná sada omsagent, se automaticky nainstaluje zprostředkovatele pro serveru Apache HTTP Server monitorování výkonu. Tento zprostředkovatel spoléhá na modul Apache, který je nutné načíst do serveru Apache HTTP Server, aby měli přístup na údaje o výkonu. Modul můžete načíst pomocí následujícího příkazu:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

Unload modulu Sledování Apache, spusťte následující příkaz:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```

### <a name="define-performance-counters"></a>Definování čítače výkonu

Po dokončení konfigurace agenta OMS pro Linux k odesílání dat k analýze protokolů, je nutné nakonfigurovat shromažďování čítačů výkonu.  Pomocí postupu v [Windows a Linux zdroje dat výkonu v analýzy protokolů](log-analytics-data-sources-windows-events.md) s čítači v následující tabulce.

| Název objektu | Název čítače |
|:--|:--|
| Apache HTTP Server | Vytížených pracovních procesů |
| Apache HTTP Server | Nečinných pracovních procesů |
| Apache HTTP Server | Protokol PCT vytížených pracovních procesů |
| Apache HTTP Server | Celkový počet Pct procesoru |
| Apache Virtual Host | Chyby za minutu - klienta |
| Apache Virtual Host | Chyby za minutu - Server |
| Apache Virtual Host | KB každý požadavek |
| Apache Virtual Host | Žádosti o KB za sekundu |
| Apache Virtual Host | Počet požadavků za sekundu |



## <a name="next-steps"></a>Další postup
* [Shromažďování čítačů výkonu](log-analytics-data-sources-performance-counters.md) od agentů systému Linux.
* Další informace o [protokolu hledání](log-analytics-log-searches.md) analyzovat data shromážděná ze zdrojů dat a řešení. 