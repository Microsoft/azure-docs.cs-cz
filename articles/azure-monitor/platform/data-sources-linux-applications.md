---
title: Shromážděte výkon linuxových aplikací ve Službě Azure Monitor | Dokumenty společnosti Microsoft
description: Tento článek obsahuje podrobnosti pro konfiguraci agenta Log Analytics pro Linux pro shromažďování čítačů výkonu pro MySQL a Apache HTTP Server.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/04/2017
ms.openlocfilehash: 2fd148dbb85a4fd60fe63d4fb73128bf92dea1d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670555"
---
# <a name="collect-performance-counters-for-linux-applications-in-azure-monitor"></a>Shromažďování čítačů výkonu pro linuxové aplikace v Azure Monitoru 
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]
Tento článek obsahuje podrobnosti pro konfiguraci [agenta Log Analytics pro Linux](https://github.com/Microsoft/OMS-Agent-for-Linux) shromažďovat čítače výkonu pro konkrétní aplikace do Azure Monitoru.  Aplikace obsažené v tomto článku jsou:  

- [Mysql](#mysql)
- [Apache HTTP Server](#apache-http-server)

## <a name="mysql"></a>MySQL
Pokud mysql server nebo MariaDB Server je zjištěna v počítači při instalaci agenta Log Analytics, bude automaticky nainstalován poskytovatel sledování výkonu pro MySQL Server. Tento zprostředkovatel se připojí k místnímu serveru MySQL/MariaDB, aby odhalil statistiky výkonu. Přihlašovací údaje uživatele MySQL musí být nakonfigurovány tak, aby poskytovatel měl přístup k Serveru MySQL.

### <a name="configure-mysql-credentials"></a>Konfigurace přihlašovacích údajů MySQL
Poskytovatel MySQL OMI vyžaduje předkonfigurovaného uživatele MySQL a nainstalované klientské knihovny MySQL, aby mohl z instance MySQL zadat dotaz na informace o výkonu a stavu.  Tato pověření jsou uložena v ověřovacím souboru, který je uložen v agentovi Linuxu.  Ověřovací soubor určuje, co bind adresu a port MySQL instance naslouchá a jaká pověření použít ke shromažďování metrik.  

Při instalaci agenta Log Analytics pro Linux poskytovatel MySQL OMI naskenuje konfigurační soubory MySQL my.cnf (výchozí umístění) pro vázací adresu a port a částečně nastaví ověřovací soubor MySQL OMI.

Ověřovací soubor MySQL je `/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth`uložen na adrese .


### <a name="authentication-file-format"></a>Formát ověřovacího souboru
Následuje formát ověřovacího souboru MySQL OMI.

    [Port]=[Bind-Address], [username], [Base64 encoded Password]
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    AutoUpdate=[true|false]

Položky v ověřovacím souboru jsou popsány v následující tabulce.

| Vlastnost | Popis |
|:--|:--|
| Port | Představuje aktuální port, na který instance MySQL naslouchá. Port 0 určuje, že následující vlastnosti se používají pro výchozí instanci. |
| Adresa vazby| Aktuální MySQL bind-adresa. |
| uživatelské jméno| Uživatel MySQL používá ke sledování instance serveru MySQL. |
| Base64 kódované heslo| Heslo uživatele monitorování MySQL zakódované v Base64. |
| Autoupdate| Určuje, zda má být změna v souboru my.cnf znovu prohledána a při upgradu zprostředkovatele MySQL OMI přepsán a přepsán autentizační soubor MySQL OMI. |

### <a name="default-instance"></a>Výchozí instance
Ověřovací soubor MySQL OMI může definovat výchozí instanci a číslo portu, aby byla správa více instancí MySQL na jednom hostiteli Linuxu jednodušší.  Výchozí instance je označena instancí s portem 0. Všechny další instance zdědí vlastnosti nastavené z výchozí instance, pokud neurčují různé hodnoty. Například pokud mysql instance naslouchání na portu '3308' je přidán, výchozí instance je bind-adresa, uživatelské jméno a Base64 kódované heslo se použije k pokusu a sledování instance naslouchání na 3308. Pokud instance na 3308 je vázánna na jinou adresu a používá stejné MySQL uživatelské jméno a heslo dvojice je potřeba pouze bind-adresa a ostatní vlastnosti budou zděděny.

Následující tabulka obsahuje ukázkové nastavení instance 

| Popis | File |
|:--|:--|
| Výchozí instance a instance s portem 3308. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=, ,`<br>`AutoUpdate=true` |
| Výchozí instance a instance s portem 3308 a jiným uživatelským jménem a heslem. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=127.0.1.1, myuser2,cGluaGVhZA==`<br>`AutoUpdate=true` |


### <a name="mysql-omi-authentication-file-program"></a>MySQL OMI ověřovací soubor ový program
Součástí instalace poskytovatele MySQL OMI je mysql OMI autentizační souborový program, který lze použít k úpravě souboru MySQL OMI Authentication. Program ověřovacího souboru lze nalézt v následujícím umístění.

    /opt/microsoft/mysql-cimprov/bin/mycimprovauth

> [!NOTE]
> Soubor pověření musí být čitelný pomocí účtu omsagent. Doporučujese spustit příkaz mycimprovauth jako omsgent.

V následující tabulce jsou uvedeny podrobnosti o syntaxi pro použití mycimprovauth.

| Operace | Příklad | Popis
|:--|:--|:--|
| automatická aktualizace *false nebo true* | mycimprovauth automatická aktualizace false | Nastaví, zda bude ověřovací soubor automaticky aktualizován při restartování nebo aktualizaci. |
| výchozí *heslo uživatelského jména s adresou vazby* | mycimprovauth výchozí 127.0.0.1 kořen pwd | Nastaví výchozí instanci v ověřovacím souboru MySQL OMI.<br>Heslo pole by mělo být zadáno ve formátu prostého textu - heslo v ověřovacím souboru MySQL OMI bude zakódováno na základně 64. |
| odstranit *výchozí nebo port_num* | mycimprovauth 3308 | Odstraní zadanou instanci buď ve výchozím nastavení, nebo podle čísla portu. |
| Nápověda | mycimprov pomoc | Vytiskne seznam příkazů, které mají být používány. |
| Tisk | mycimprov tisk | Vytiskne snadno čitelný ověřovací soubor MySQL OMI. |
| aktualizovat port_num *heslo uživatelského jména s vazbou a adresou* | mycimprov aktualizace 3307 127.0.0.1 kořen pwd | Aktualizuje zadanou instanci nebo přidá instanci, pokud neexistuje. |

Následující ukázkové příkazy definují výchozí uživatelský účet pro server MySQL na localhost.  Heslo pole by mělo být zadáno ve formátu prostého textu - heslo v ověřovacím souboru MySQL OMI bude zakódováno na základně 64

    sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>'
    sudo /opt/omi/bin/service_control restart

### <a name="database-permissions-required-for-mysql-performance-counters"></a>Pro čítače výkonu MySQL jsou vyžadována oprávnění databáze
Uživatel MySQL vyžaduje přístup k následujícím dotazům ke shromažďování údajů o výkonu serveru MySQL Server. 

    SHOW GLOBAL STATUS;
    SHOW GLOBAL VARIABLES:


Uživatel MySQL také vyžaduje přístup SELECT k následujícím výchozím tabulkám.

- Information_schema
- Mysql. 

Tato oprávnění lze udělit spuštěním následujících příkazů udělení.

    GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
    GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;


> [!NOTE]
> Chcete-li udělit oprávnění uživateli monitorování MySQL, musí mít udělující uživatel oprávnění "MOŽNOST GRANT" a také udělené oprávnění.

### <a name="define-performance-counters"></a>Definování čítačů výkonu

Jakmile nakonfigurujete agenta Log Analytics pro Linux pro odesílání dat do Azure Monitoru, musíte nakonfigurovat čítače výkonu, které chcete shromažďovat.  Použijte postup ve [Windows a Linux u zdrojů dat výkonu v Azure Monitoru](data-sources-performance-counters.md) s čítači v následující tabulce.

| Název objektu | Název čítače |
|:--|:--|
| Databáze MySQL | Místo na disku v bajtech |
| Databáze MySQL | Tabulky |
| MySQL Server | Přerušené připojení Pct |
| MySQL Server | Připojení Použití Pct |
| MySQL Server | Využití místa na disku v bajtech |
| MySQL Server | Úplná tabulka Prohledávat Pct |
| MySQL Server | Přístup do pct fondu vyrovnávací paměti InnoDB |
| MySQL Server | Fond vyrovnávací paměti InnoDB používá pct |
| MySQL Server | Fond vyrovnávací paměti InnoDB používá pct |
| MySQL Server | Přístup do mezipaměti klíčů Pct |
| MySQL Server | Použití mezipaměti klíčů Pct |
| MySQL Server | Key Cache Zápis Pct |
| MySQL Server | Přístup pct mezipaměti dotazu |
| MySQL Server | Mezipaměť dotazů Prořezává pct |
| MySQL Server | Cache dotazu Použití Pct |
| MySQL Server | Přístup do mezipaměti tabulky Pct |
| MySQL Server | Použití mezipaměti tabulky Pct |
| MySQL Server | Konflikty zámků tabulky Pct |

## <a name="apache-http-server"></a>Apache HTTP Server 
Pokud je v počítači při instalaci sady omsagent zjištěn server Apache HTTP Server, bude automaticky nainstalován poskytovatel sledování výkonu pro server Apache HTTP. Tento zprostředkovatel spoléhá na modul Apache, který musí být načten do apache HTTP serveru, aby bylo možné získat přístup k datům o výkonu. Modul lze načíst následujícím příkazem:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

Chcete-li uvolnit monitorovací modul Apache, spusťte následující příkaz:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```

### <a name="define-performance-counters"></a>Definování čítačů výkonu

Jakmile nakonfigurujete agenta Log Analytics pro Linux pro odesílání dat do Azure Monitoru, musíte nakonfigurovat čítače výkonu, které chcete shromažďovat.  Použijte postup ve [Windows a Linux u zdrojů dat výkonu v Azure Monitoru](data-sources-performance-counters.md) s čítači v následující tabulce.

| Název objektu | Název čítače |
|:--|:--|
| Apache HTTP Server | Zaneprázdnění pracovníci |
| Apache HTTP Server | Nečinné pracovníky |
| Apache HTTP Server | Pct Zaneprázdnění pracovníci |
| Apache HTTP Server | Celkový procesor Pct |
| Virtuální hostitel Apache | Chyby za minutu – klient |
| Virtuální hostitel Apache | Chyby za minutu – server |
| Virtuální hostitel Apache | KB na požadavek |
| Virtuální hostitel Apache | Požadavky KB za sekundu |
| Virtuální hostitel Apache | Požadavky za sekundu |



## <a name="next-steps"></a>Další kroky
* [Shromážděte čítače výkonu](data-sources-performance-counters.md) od agentů Linuxu.
* Přečtěte si o [dotazech protokolu](../log-query/log-query-overview.md) k analýze dat shromážděných ze zdrojů dat a řešení. 
