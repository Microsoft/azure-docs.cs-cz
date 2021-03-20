---
title: Chyby přechodného připojení – Azure Database for MySQL
description: Naučte se zpracovávat chyby s přechodným připojením a efektivně se připojit k Azure Database for MySQL.
keywords: připojení MySQL, připojovací řetězec, problémy s připojením, přechodná chyba, Chyba připojení, efektivní připojení
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 89673c14c38947dc5aeb91cacde1eb2755e84138
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94542605"
---
# <a name="handle-transient-errors-and-connect-efficiently-to-azure-database-for-mysql"></a>Zpracování přechodných chyb a efektivní připojení k Azure Database for MySQL

Tento článek popisuje, jak řešit přechodné chyby a efektivně se připojovat k Azure Database for MySQL.

## <a name="transient-errors"></a>Přechodné chyby

Přechodná chyba, označovaná také jako přechodná chyba, je chyba, která se vyřeší sám. Nejčastěji tyto chyby se manifestují jako připojení k databázovému serveru, který se vynechává. Nová připojení k serveru se taky nedají otevřít. K přechodným chybám může dojít například v případě, že dojde k selhání hardwaru nebo sítě. Dalším důvodem může být nová verze služby PaaS, která se zavádí. Většina těchto událostí je systémem automaticky snížena za méně než 60 sekund. Osvědčeným postupem pro navrhování a vývoj aplikací v cloudu je očekávat přechodné chyby. Předpokládejte, že se k nim může kdykoli docházet v každé komponentě a mít k dispozici odpovídající logiku pro zpracování těchto situací.

## <a name="handling-transient-errors"></a>Zpracování přechodných chyb

Přechodné chyby by se měly zpracovat pomocí logiky opakování. Situace, které je třeba vzít v úvahu:

* Při pokusu o otevření připojení dojde k chybě.
* Nečinné připojení je na straně serveru vyřazeno. Když se pokusíte o vydání příkazu, nejde ho spustit.
* Aktivní připojení, které aktuálně provádí příkaz, je vyřazeno.

První a druhý případ jsou poměrně přímo předávány na popisovač. Pokuste se znovu otevřít připojení. Po úspěšném provedení přechodné chyby systém sníží. Azure Database for MySQL můžete použít znovu. Doporučujeme, abyste počkali před opakováním pokusu o připojení. Pokud nedojde k selhání počátečních pokusů, je záložní. Tímto způsobem systém může použít všechny prostředky, které jsou k dispozici k překonání chybové situace. Dobrý vzor, který je potřeba provést:

* Počkejte 5 sekund před prvním opakováním.
* Pro každou z následujících možností zkuste zvětšit počkat exponenciálně až 60 sekund.
* Nastavte maximální počet opakovaných pokusů, na kterých by vaše aplikace pomohly operaci považovat za neúspěšnou.

V případě, že připojení k aktivní transakci dojde k chybě, je obtížné správně zpracovat obnovení. Existují dva případy: Pokud byla transakce určena jen pro čtení, je bezpečné znovu otevřít připojení a opakovat transakci. Pokud je však transakce také zapsána do databáze, je nutné určit, zda byla transakce vrácena zpět nebo zda byla úspěšná, než došlo k přechodné chybě. V takovém případě je možné, že jste neobdrželi potvrzení potvrzení z databázového serveru.

Jedním ze způsobů, jak to provést, je vygenerovat v klientovi jedinečné ID, které se používá pro všechny opakované pokusy. Toto jedinečné ID předáte jako součást transakce serveru a uložíte ji do sloupce s jedinečným omezením. Tímto způsobem lze transakci bezpečně opakovat. V případě, že předchozí transakce byla vrácena zpět a jedinečné ID generované klientem v systému ještě neexistuje, bude úspěšné. V případě, že se jedinečné ID dříve uložilo z důvodu úspěšného dokončení předchozí transakce, selže oznámení, že dojde k porušení duplicitního klíče.

Když váš program komunikuje s Azure Database for MySQL prostřednictvím middlewaru třetí strany, požádejte dodavatele, zda middleware obsahuje logiku opakování pro přechodné chyby.

Nezapomeňte otestovat logiku opakování. Například zkuste spustit kód při vertikálním navýšení nebo snížení kapacity výpočetních prostředků serveru Azure Database for MySQL. Vaše aplikace by měla zpracovávat krátké výpadky zjištěné během této operace bez jakýchkoli problémů.

## <a name="connect-efficiently-to-azure-database-for-mysql"></a>Efektivně se připojte k Azure Database for MySQL

Databázová připojení jsou omezeným prostředkem, takže efektivní využití sdružování připojení pro přístup k Azure Database for MySQL optimalizuje výkon. Následující část vysvětluje, jak používat sdružování připojení nebo trvalá připojení k efektivnějšímu přístupu Azure Database for MySQL.

## <a name="access-databases-by-using-connection-pooling-recommended"></a>Přístup k databázím pomocí sdružování připojení (doporučeno)

Správa připojení k databázi může mít významný dopad na výkon aplikace jako celek. Pro optimalizaci výkonu aplikace by měl být cílem snížit počet navázání připojení a čas pro vytvoření připojení v cestách kódu klíče. Pro připojení k Azure Database for MySQL důrazně doporučujeme použít sdružování připojení databáze nebo trvalá připojení. Sdružování připojení databáze zajišťuje vytváření, správu a přidělování databázových připojení. Když program požádá o připojení k databázi, upřednostní přidělení stávajících nečinných připojení databáze místo vytvoření nového připojení. Po dokončení programu pomocí databázového připojení se připojení obnoví v příprava pro další použití, místo abyste ho jednoduše zavřeli.

Pro lepší ilustraci Tento článek obsahuje [ukázku kódu](./sample-scripts-java-connection-pooling.md) , který jako příklad používá Java. Další informace najdete v tématu [Apache Common DBCP](https://commons.apache.org/proper/commons-dbcp/).

> [!NOTE]
> Server po nějakou dobu nakonfiguruje mechanismus časového limitu, aby uzavřel připojení, které bylo v nečinném stavu, aby bylo možné prostředky uvolnit. Nezapomeňte nastavit ověřovací systém, aby se zajistila efektivita trvalých připojení při jejich používání. Další informace najdete v tématu [Konfigurace ověřovacích systémů na straně klienta, aby se zajistila efektivita trvalých připojení](concepts-connectivity.md#configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections).

## <a name="access-databases-by-using-persistent-connections-recommended"></a>Přístup k databázím pomocí trvalých připojení (doporučeno)

Koncept trvalých připojení je podobný jako sdružování připojení. Nahrazení krátkých připojení pomocí trvalých připojení vyžaduje pouze drobné změny kódu, ale má zásadní vliv na zlepšení výkonu v řadě typických aplikačních scénářů.

## <a name="access-databases-by-using-wait-and-retry-mechanism-with-short-connections"></a>Přístup k databázím pomocí mechanismu čekání a opakování s krátkými připojeními

Pokud máte omezení prostředků, důrazně doporučujeme, abyste pro přístup k databázím použili fondy databází nebo trvalá připojení. Pokud vaše aplikace používá krátká připojení a dochází k selhání připojení při přístupu k hornímu limitu počtu souběžných připojení, můžete zkusit počkat a znovu použít mechanismus. Můžete nastavit vhodný čekací čas s kratší čekací dobou po prvním pokusu. Potom můžete zkusit počkat na události víckrát.

## <a name="configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections"></a>Konfigurace ověřovacích mechanismů v klientech pro ověření efektivity trvalých připojení

Server po nějakou dobu nakonfiguruje mechanismus časového limitu, aby uzavřel připojení, které je ve stavu nečinnosti, aby bylo možné prostředky uvolnit. Když klient znovu přistupuje k databázi, je stejný jako vytvoření nové žádosti o připojení mezi klientem a serverem. Aby se zajistila efektivita připojení během procesu jejich použití, nakonfigurujte ověřovací mechanismus na klientovi. Jak je znázorněno v následujícím příkladu, můžete ke konfiguraci tohoto mechanismu ověřování použít sdružování připojení Tomcat JDBC.

Po nastavení parametru TestOnBorrow, když je k dispozici nový požadavek, fond připojení automaticky ověří efektivitu všech dostupných nečinných připojení. Pokud je takové připojení účinné, přímo vráceno jinak fond připojení odvolá připojení. Fond připojení potom vytvoří nové efektivní připojení a vrátí jej. Tento proces zajišťuje efektivní použití databáze. 

Informace o konkrétních nastaveních najdete v článku [Úvod do fondu připojení JDBC oficiálního dokumentu](https://tomcat.apache.org/tomcat-7.0-doc/jdbc-pool.html#Common_Attributes). Většinou je potřeba nastavit následující tři parametry: TestOnBorrow (nastavte na true), ValidationQuery (nastavte na vybrat 1) a ValidationQueryTimeout (nastavte na 1). Konkrétní vzorový kód je uveden níže:

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
