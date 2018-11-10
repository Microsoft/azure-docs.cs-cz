---
title: Výkon Linuxových aplikací v Log Analytics shromažďovat | Dokumentace Microsoftu
description: Tento článek obsahuje podrobnosti pro konfiguraci agenta Log Analytics pro Linux ke shromažďování čítačů výkonu pro MySQL a serveru Apache HTTP Server.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 21f030f692065357f5ab8d23aa6940fecfdeefc1
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51004862"
---
# <a name="collect-performance-counters-for-linux-applications-in-log-analytics"></a>Shromáždit čítače výkonu pro Linuxové aplikace ve službě Log Analytics 
[!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]
Tento článek obsahuje podrobné informace pro konfiguraci [agenta Log Analytics pro Linux](https://github.com/Microsoft/OMS-Agent-for-Linux) získat čítače výkonu pro určité aplikace.  Aplikací obsažených v tomto článku jsou:  

- [MySQL](#MySQL)
- [Serveru Apache HTTP Server](#apache-http-server)

## <a name="mysql"></a>MySQL
Pokud se MySQL Server nebo MariaDB serveru při instalaci agenta Log Analytics zjistí na počítači, se automaticky nainstaluje poskytovatele pro MySQL Server monitorování výkonu. Tento zprostředkovatel připojení k místnímu serveru MySQL/MariaDB vystavit statistiky výkonu. Přihlašovací údaje uživatele MySQL je třeba nastavit tak, aby zprostředkovatel má přístup k serveru MySQL.

### <a name="configure-mysql-credentials"></a>Nakonfigurujte přihlašovací údaje MySQL
Zprostředkovatel MySQL OMI vyžaduje předkonfigurované uživatele MySQL a nainstalovat klientské knihovny MySQL aby bylo možné dotazovat výkonu a informace o stavu z MySQL instance.  Tyto přihlašovací údaje jsou uložené v souboru ověřování, která je uložena na agenta pro Linux.  Ověřovacího souboru Určuje, jaké vazby adresu a port instanci MySQL naslouchá a jaké přihlašovací údaje použít ke shromažďování metrik.  

Během instalace agenta Log Analytics pro Linux MySQL OMI zprostředkovatele vyhledá MySQL my.cnf konfigurační soubory (výchozí umístění) pro vazbu adresy a portu a částečně nastavit ověřovacího souboru MySQL OMI.

MySQL ověřovacího souboru je uložené v `/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth`.


### <a name="authentication-file-format"></a>Formát souboru ověřování
Má tento formát pro ověření souboru MySQL OMI

    [Port]=[Bind-Address], [username], [Base64 encoded Password]
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    AutoUpdate=[true|false]

Položky v souboru ověřování jsou popsané v následující tabulce.

| Vlastnost | Popis |
|:--|:--|
| Port | Představuje aktuální port, který naslouchá instanci MySQL. Port 0 určuje, že se používají následující vlastnosti pro výchozí instanci. |
| Adresy vazby| Aktuální vazby MySQL-adresu. |
| uživatelské jméno| Uživatel MySQL lze použít k monitorování instance serveru MySQL. |
| Heslo s kódováním base64| Heslo uživatele MySQL monitorování kódovaný jako Base64. |
| Automatickou aktualizaci| Určuje, jestli se má prohledat znovu na změny v souboru my.cnf a přepsat soubor MySQL OMI ověřování při upgradu infrastruktury OMI poskytovatele MySQL. |

### <a name="default-instance"></a>Výchozí instance
Ověřovacího souboru MySQL OMI můžete definovat výchozí instance a číslo portu provádět správu více instancí MySQL na jednom hostiteli systému Linux jednodušší.  Výchozí instance je označen instance s portem 0. Všechny další instance zdědí vlastnosti nastavit z výchozí instance, pokud jsou specifikování různých hodnot. Například pokud se přidá instanci MySQL naslouchá na portu "3308", vazba adresu výchozí instance, uživatelské jméno a heslo kódovanou jako Base64 se použije sledovat instanci naslouchá na 3308 a zkuste to. Pokud instanci na 3308 je vázán na jinou adresu a používá stejnou dvojici uživatelského jména a hesla MySQL je potřeba jenom adresy vazby a ostatní vlastnosti se budou dědit.

Následující tabulka obsahuje příklady nastavení instance 

| Popis | File |
|:--|:--|
| Výchozí instanci a instanci s portem 3308. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=, ,`<br>`AutoUpdate=true` |
| Výchozí instanci a instanci s portem 3308 a jiné uživatelské jméno a heslo. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=127.0.1.1, myuser2,cGluaGVhZA==`<br>`AutoUpdate=true` |


### <a name="mysql-omi-authentication-file-program"></a>MySQL OMI ověřování souboru programu
Součástí instalace zprostředkovatele MySQL OMI je program ověřování souboru MySQL OMI, který slouží k úpravě MySQL OMI ověřovacího souboru. Soubor programu ověřování najdete v následujícím umístění.

    /opt/microsoft/mysql-cimprov/bin/mycimprovauth

> [!NOTE]
> Soubor s přihlašovacími údaji musí být čitelný omsagent účtem. Spuštění příkazu mycimprovauth jako omsgent se doporučuje.

Následující tabulka obsahuje podrobnosti o syntaxi pro používání mycimprovauth.

| Operace | Příklad: | Popis
|:--|:--|:--|
| automatickou aktualizaci *true nebo false* | false mycimprovauth automatických aktualizací | Určuje, jestli se automaticky aktualizují ověřovacího souboru nastaví na restartování nebo aktualizovat. |
| výchozí *uživatelské jméno vázání adresy a hesla* | mycimprovauth výchozí 127.0.0.1 kořenové heslo | Nastaví výchozí instanci MySQL OMI ověřovacího souboru.<br>Do pole hesla by měly být zadány ve formátu prostého textu – hesla ověřovacího souboru MySQL OMI bude kódování Base 64. |
| Odstranit *výchozí nebo port_num* | mycimprovauth 3308 | Odstraní zadanou instanci buď výchozí nebo číslo portu. |
| Nápověda | mycimprov nápovědy | Vytiskne seznam příkazů použít. |
| Tisk | mycimprov tisku | Vytiskne snadno čitelný MySQL OMI ověřovacího souboru. |
| Aktualizovat port_num *uživatelské jméno vázání adresy a hesla* | mycimprov aktualizace 3307 127.0.0.1 kořenové heslo | Aktualizuje zadaný instanci nebo přidá instanci, pokud neexistuje. |

Příkazy v následujícím příkladu definovat výchozí uživatelský účet pro MySQL server v místním hostiteli.  Do pole hesla by měly být zadány ve formátu prostého textu – hesla ověřovacího souboru MySQL OMI bude kódování Base 64

    sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>'
    sudo /opt/omi/bin/service_control restart

### <a name="database-permissions-required-for-mysql-performance-counters"></a>Oprávnění databáze požadovaná pro čítače výkonu MySQL
Uživatel MySQL vyžaduje přístup k následující dotazy ke shromažďování dat výkonu serveru MySQL. 

    SHOW GLOBAL STATUS;
    SHOW GLOBAL VARIABLES:


Uživatel MySQL také vyžaduje přístup SELECT k následující výchozí tabulky.

- INFORMATION_SCHEMA
- MySQL. 

Tato oprávnění lze udělit spuštěním následujících příkazů udělení.

    GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
    GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;


> [!NOTE]
> Udělení oprávnění k MySQL monitorování uživatel muselo uživatel musí mít oprávnění "GRANT option", jakož i udělením oprávnění.

### <a name="define-performance-counters"></a>Definování čítače výkonu

Jakmile nakonfigurujete agenta Log Analytics pro Linux pro odesílání dat do Log Analytics, je nutné nakonfigurovat čítačů výkonu k získání.  Použijte postup uvedený v [Windows a Linuxem zdroje dat výkonu do Log Analytics](log-analytics-data-sources-windows-events.md) s čítače v následující tabulce.

| Název objektu | Název počítadla |
|:--|:--|
| Databáze MySQL | Místo na disku v bajtech |
| Databáze MySQL | Tabulky |
| MySQL Server | Přerušené připojení protokolu Pct |
| MySQL Server | Připojení pomocí protokolu Pct |
| MySQL Server | Využití místa na disku v bajtech |
| MySQL Server | Procentní hodnota kontroly celou tabulku |
| MySQL Server | Fond vyrovnávacích pamětí InnoDB přístupů protokolu Pct |
| MySQL Server | Procentní hodnota použití fondu vyrovnávací paměti InnoDB |
| MySQL Server | Procentní hodnota použití fondu vyrovnávací paměti InnoDB |
| MySQL Server | Procentní hodnota přístupů do mezipaměti klíče |
| MySQL Server | Klíče mezipaměti použití protokolu Pct |
| MySQL Server | Procentní hodnota klíče mezipaměti zápisu |
| MySQL Server | Dotaz protokolu Pct přístupů do mezipaměti |
| MySQL Server | Procentní hodnota vyřadí mezipaměti dotazů |
| MySQL Server | Dotaz mezipaměti použití protokolu Pct |
| MySQL Server | Procentní hodnota přístupů do mezipaměti tabulky |
| MySQL Server | Tabulka mezipaměti použití protokolu Pct |
| MySQL Server | Pct spor zámku tabulky |

## <a name="apache-http-server"></a>Apache HTTP Server 
Pokud se při instalaci sady omsagent serveru Apache HTTP Server zjistí na počítači, se automaticky nainstaluje zprostředkovatele pro serveru Apache HTTP Server monitorování výkonu. Tento zprostředkovatel spoléhá na modul Apache, který musí být načten do serveru Apache HTTP Server za účelem přístupu k data o výkonu. Modul lze načíst pomocí následujícího příkazu:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

Uvolnění modulu Sledování Apache, spusťte následující příkaz:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```

### <a name="define-performance-counters"></a>Definování čítače výkonu

Jakmile nakonfigurujete agenta Log Analytics pro Linux pro odesílání dat do Log Analytics, je nutné nakonfigurovat čítačů výkonu k získání.  Použijte postup uvedený v [Windows a Linuxem zdroje dat výkonu do Log Analytics](log-analytics-data-sources-windows-events.md) s čítače v následující tabulce.

| Název objektu | Název počítadla |
|:--|:--|
| Apache HTTP Server | Vytížených pracovních procesů |
| Apache HTTP Server | Nečinných pracovních procesů |
| Apache HTTP Server | Procentní hodnota vytížených pracovních procesů |
| Apache HTTP Server | Celkové procento CPU |
| Apache virtuální hostitel | Chyby za minutu - klient |
| Apache virtuální hostitel | Chyby za minutu - Server |
| Apache virtuální hostitel | KB na žádost |
| Apache virtuální hostitel | KB požadavků za sekundu |
| Apache virtuální hostitel | Počet požadavků za sekundu |



## <a name="next-steps"></a>Další postup
* [Shromažďování čítačů výkonu](log-analytics-data-sources-performance-counters.md) z agentů Linuxu.
* Další informace o [prohledávání protokolů](log-analytics-queries.md) analyzovat data shromážděná ze zdrojů dat a jejich řešení. 
