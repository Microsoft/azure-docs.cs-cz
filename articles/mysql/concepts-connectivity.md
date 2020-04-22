---
title: Přechodné chyby připojení – Azure Database for MySQL
description: Zjistěte, jak zpracovat přechodné chyby připojení a efektivně se připojit k Azure Database for MySQL.
keywords: připojení mysql,připojovací řetězec,problémy s připojením,přechodná chyba,chyba připojení,efektivní připojení
author: jasonwhowell
ms.author: jasonh
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 4f9101b4108f5512ee9779f4633845b34fdfad5a
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767876"
---
# <a name="handle-transient-errors-and-connect-efficiently-to-azure-database-for-mysql"></a>Zpracování přechodných chyb a efektivní připojení k Azure Database for MySQL

Tento článek popisuje, jak zpracovat přechodné chyby a efektivně se připojit k Azure Database for MySQL.

## <a name="transient-errors"></a>Přechodné chyby

Přechodná chyba, označovaná také jako přechodná chyba, je chyba, která se vyřeší sama. Většina obvykle tyto chyby manifest jako připojení k databázovému serveru je vynechána. Také nelze otevřít nová připojení k serveru. K přechodným chybám může dojít například při selhání hardwaru nebo sítě. Dalším důvodem může být nová verze služby PaaS, která je právě zaváděna. Většina těchto událostí jsou automaticky zmírněny systémem za méně než 60 sekund. Osvědčeným postupem pro navrhování a vývoj aplikací v cloudu je očekávat přechodné chyby. Předpokládejme, že může dojít v libovolné součásti kdykoli a mít příslušnou logiku na místě pro zpracování těchto situací.

## <a name="handling-transient-errors"></a>Zpracování přechodných chyb

Přechodné chyby by měly být zpracovány pomocí logiky opakování. Situace, které je třeba vzít v úvahu:

* Při pokusu o otevření připojení dojde k chybě.
* Na straně serveru je přerušeno nečinné připojení. Při pokusu o vydání příkazu nelze provést
* Aktivní připojení, které právě provádí příkaz, je vynecháno.

První a druhý případ jsou poměrně přímočaré zvládnout. Zkuste připojení otevřít znovu. Když uspějete, přechodná chyba byla zmírněna systémem. Můžete znovu použít databázi Azure pro MySQL. Doporučujeme mít čeká před opakováním připojení. Pokud se počáteční opakování nezdaří, ustoupíte. Tímto způsobem může systém použít všechny prostředky, které jsou k dispozici k překonání chybové situace. Dobrý vzor následovat, je:

* Před prvním opakováním počkejte 5 sekund.
* Pro každý následující opakování zvýšit čekání exponenciálně, až 60 sekund.
* Nastavte maximální počet opakování, ve kterém okamžiku aplikace považuje operaci za neúspěšnou.

Pokud se nezdaří připojení s aktivní transakcí, je obtížnější správně zpracovat obnovení. Existují dva případy: Pokud transakce byla jen pro čtení v přírodě, je bezpečné znovu otevřít připojení a opakovat transakci. Pokud však transakce byla také zápis do databáze, je nutné určit, pokud transakce byla vrácena zpět, nebo pokud byla úspěšná před přechodnou chybu došlo. V takovém případě pravděpodobně jste potvrzení potvrzení neobdrželi z databázového serveru.

Jedním ze způsobů, jak toho dosáhnout, je generovat jedinečné ID na straně klienta, který se používá pro všechny opakování. Toto jedinečné ID předáte jako součást transakce serveru a uložíte ho do sloupce s jedinečným omezením. Tímto způsobem můžete bezpečně opakovat transakci. Bude úspěšná, pokud předchozí transakce byla vrácena zpět a klientem generované jedinečné ID ještě neexistuje v systému. Selhat označující duplicitní narušení klíče, pokud bylo jedinečné ID dříve uloženo, protože předchozí transakce byla úspěšně dokončena.

Když váš program komunikuje s Azure Database for MySQL prostřednictvím middlewaru jiného výrobce, zeptejte se dodavatele, zda middleware obsahuje logiku opakování pro přechodné chyby.

Ujistěte se, že test logiku opakování. Například zkuste spustit kód při škálování nahoru nebo dolů výpočetní prostředky databáze Azure pro server MySQL. Aplikace by měla zpracovat krátké prostoje, ke kterým došlo během této operace bez problémů.

## <a name="connect-efficiently-to-azure-database-for-mysql"></a>Efektivní připojení k Azure Database pro MySQL

Připojení databáze jsou omezený prostředek, takže efektivní využití sdružování připojení pro přístup k Azure Database for MySQL optimalizuje výkon. V následující části je vysvětleno, jak používat sdružování připojení nebo trvalá připojení k efektivnějšímu přístupu k Azure Database for MySQL.

## <a name="access-databases-by-using-connection-pooling-recommended"></a>Přístup k databázím pomocí sdružování připojení (doporučeno)

Správa připojení databáze může mít významný dopad na výkon aplikace jako celku. Chcete-li optimalizovat výkon aplikace, cílem by mělo být snížení počtu navázání připojení a čas pro navázání připojení v cestě kódu klíče. Důrazně doporučujeme používat sdružování připojení k databázi nebo trvalá připojení pro připojení k Azure Database for MySQL. Sdružování připojení k databázi zpracovává vytváření, správu a přidělování připojení k databázi. Pokud program požaduje připojení k databázi, upřednostňuje přidělení existujících nečinných databázových připojení, nikoli vytvoření nového připojení. Po dokončení programu pomocí připojení k databázi je připojení obnoveno v rámci přípravy na další použití, nikoli jednoduše ukončeno.

Pro lepší ilustraci tento článek obsahuje [ukázkový kód,](./sample-scripts-java-connection-pooling.md) který používá java jako příklad. Další informace naleznete v [tématu Apache common DBCP](https://commons.apache.org/proper/commons-dbcp/).

> [!NOTE]
> Server nakonfiguruje mechanismus časového limitu pro ukončení připojení, které bylo po určitou dobu v nečinnosti, aby uvolnil oprostředek. Nezapomeňte nastavit ověřovací systém, abyste zajistili účinnost trvalých připojení při jejich používání. Další informace naleznete v [tématu Konfigurace ověřovacích systémů na straně klienta, abyste zajistili účinnost trvalých připojení](concepts-connectivity.md#configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections).

## <a name="access-databases-by-using-persistent-connections-recommended"></a>Přístup k databázím pomocí trvalých připojení (doporučeno)

Koncept trvalé připojení je podobný jako sdružování připojení. Nahrazení krátkých připojení trvalými připojeními vyžaduje pouze drobné změny kódu, ale má zásadní vliv z hlediska zlepšení výkonu v mnoha typických scénářích aplikace.

## <a name="access-databases-by-using-wait-and-retry-mechanism-with-short-connections"></a>Přístup k databázím pomocí mechanismu čekání a opakování s krátkými připojeními

Pokud máte omezení prostředků, důrazně doporučujeme použít sdružování databází nebo trvalá připojení pro přístup k databázím. Pokud vaše aplikace používat krátká připojení a dojde k selhání připojení při přiblížení k horní limit na počet souběžných připojení, můžete zkusit čekat a opakovat mechanismus. Můžete nastavit vhodnou čekací dobu s kratší čekací dobou po prvním pokusu. Poté můžete zkusit čekat na události vícekrát.

## <a name="configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections"></a>Konfigurace ověřovacích mechanismů v klientech pro potvrzení účinnosti trvalých připojení

Server nakonfiguruje mechanismus časového limitu pro ukončení připojení, které bylo nějakou dobu v nečinném stavu, aby se uvolnily prostředky. Když klient znovu přistupuje k databázi, je ekvivalentní k vytvoření nového požadavku na připojení mezi klientem a serverem. Chcete-li zajistit účinnost připojení během procesu jejich používání, nakonfigurujte ověřovací mechanismus na straně klienta. Jak je znázorněno v následujícím příkladu, můžete použít sdružování připojení Tomcat JDBC ke konfiguraci tohoto ověřovacího mechanismu.

Nastavením Parametr TestOnBorrow, když je nový požadavek, fond připojení automaticky ověří účinnost všech dostupných nečinných připojení. Pokud je takové připojení účinné, jeho přímo vrácené jinak fond připojení zruší připojení. Fond připojení pak vytvoří nové efektivní připojení a vrátí jej. Tento proces zajišťuje, že databáze je přístupná efektivně. 

Informace o konkrétních nastaveních naleznete v [oficiálním úvodním dokumentu fondu připojení JDBC](https://tomcat.apache.org/tomcat-7.0-doc/jdbc-pool.html#Common_Attributes). Je třeba hlavně nastavit následující tři parametry: TestOnBorrow (nastaveno na hodnotu true), ValidationQuery (nastaveno na SELECT 1) a ValidationQueryTimeout (nastaveno na 1). Specifický ukázkový kód je uveden níže:

```java
public class SimpleTestOnBorrowExample {
      public static void main(String[] args) throws Exception {
          PoolProperties p = new PoolProperties();
          p.setUrl("jdbc:mysql://localhost:3306/mysql");
          p.setDriverClassName("com.mysql.jdbc.Driver");
          p.setUsername("root");
          p.setPassword("password");
            // The indication of whether objects will be validated by the idle object evictor (if any). 
            // If an object fails to validate, it will be dropped from the pool. 
            // NOTE - for a true value to have any effect, the validationQuery or validatorClassName parameter must be set to a non-null string. 
          p.setTestOnBorrow(true); 

            // The SQL query that will be used to validate connections from this pool before returning them to the caller.
            // If specified, this query does not have to return any data, it just can't throw a SQLException.
          p.setValidationQuery("SELECT 1");

            // The timeout in seconds before a connection validation queries fail. 
            // This works by calling java.sql.Statement.setQueryTimeout(seconds) on the statement that executes the validationQuery. 
            // The pool itself doesn't timeout the query, it is still up to the JDBC driver to enforce query timeouts. 
            // A value less than or equal to zero will disable this feature.
          p.setValidationQueryTimeout(1);
            // set other useful pool properties.
          DataSource datasource = new DataSource();
          datasource.setPoolProperties(p);

          Connection con = null;
          try {
            con = datasource.getConnection();
            // execute your query here
          } finally {
            if (con!=null) try {con.close();}catch (Exception ignore) {}
          }
      }
  }
```

## <a name="next-steps"></a>Další kroky

* [Řešení potíží s připojením ke službě Azure Database for MySQL](howto-troubleshoot-common-connection-issues.md)
