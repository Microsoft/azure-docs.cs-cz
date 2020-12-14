---
title: Řešení běžných chyb – Azure Database for MySQL
description: Přečtěte si, jak řešit běžné chyby migrace, ke kterým dochází uživatelům, kteří jsou noví ve službě Azure Database for MySQL.
author: savjani
ms.service: mysql
ms.author: pariks
ms.custom: mvc
ms.topic: overview
ms.date: 8/20/2020
ms.openlocfilehash: 8f735ecd4f8b79b4f5bd0c95d0bfb9f280d93833
ms.sourcegitcommit: ea17e3a6219f0f01330cf7610e54f033a394b459
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2020
ms.locfileid: "97387339"
---
# <a name="common-errors"></a>Běžné chyby

Azure Database for MySQL je plně spravovaná služba, která využívá komunitu verze MySQL. Prostředí MySQL v prostředí spravované služby se může lišit od spuštění MySQL ve vašem vlastním prostředí. V tomto článku se zobrazí některé běžné chyby, se kterými se uživatelé můžou setkat při prvním pokusu o migraci nebo vývoji služby Azure Database for MySQL.

## <a name="errors-due-to-lack-of-super-privilege-and-dba-role"></a>Chyby z důvodu nedostatku role SUPER Privilege a DBA

Role SUPER Privilege a DBA není ve službě podporovaná. V důsledku toho může dojít k některým běžným chybám uvedeným níže:

#### <a name="error-1419-you-do-not-have-the-super-privilege-and-binary-logging-is-enabled-you-might-want-to-use-the-less-safe-log_bin_trust_function_creators-variable"></a>Chyba 1419: nemáte oprávnění SUPERUŽIVATELE a binární protokolování je povolené ( *možná* budete chtít použít méně bezpečné log_bin_trust_function_creators proměnné).

K této chybě může dojít při vytváření funkce, triggeru níže nebo importu schématu. Příkazy DDL, jako je funkce vytvořit nebo aktivační událost pro vytvoření, jsou zapsány do binárního protokolu, takže je může spustit sekundární replika. Vlákno SQL repliky má úplná oprávnění, která je možné zneužít ke zvýšení oprávnění. Aby bylo možné chránit před tímto nebezpečím u serverů s povoleným binárním protokolováním, modul MySQL vyžaduje, aby tvůrci uložených funkcí měli oprávnění SUPER, a to i v případě, že je nutné mít oprávnění k běžnému vytvoření RUTINy. 

```sql
CREATE FUNCTION f1(i INT)
RETURNS INT
DETERMINISTIC
READS SQL DATA
BEGIN
  RETURN i;
END;
```

**Řešení**: Pokud chcete chybu vyřešit, nastavte log_bin_trust_function_creators na 1 z okna [parametry serveru](howto-server-parameters.md) na portálu, spusťte příkazy DDL nebo importujte schéma pro vytvoření požadovaných objektů a vraťte zpět log_bin_trust_function_creators parametr na předchozí hodnotu po vytvoření.

#### <a name="error-1227-42000-at-line-101-access-denied-you-need-at-least-one-of-the-super-privileges-for-this-operation-operation-failed-with-exitcode-1"></a>Chyba 1227 (42000) na řádku 101: přístup byl odepřen; pro tuto operaci potřebujete (aspoň jedno z) oprávnění SUPER. Operace selhala s ExitCode 1.

Výše uvedená chyba může nastat při importu souboru s výpisem paměti nebo vytvoření procedury, která obsahuje [zpřesnění](https://dev.mysql.com/doc/refman/5.7/en/create-procedure.html). 

**Řešení**: Chcete-li vyřešit tuto chybu, uživatel s právy pro správu může udělit oprávnění k vytvoření nebo spuštění procedur spuštěním příkazu Grant jako v následujících příkladech:

```sql
GRANT CREATE ROUTINE ON mydb.* TO 'someuser'@'somehost';
GRANT EXECUTE ON PROCEDURE mydb.myproc TO 'someuser'@'somehost';
```
Alternativně můžete změnit podrobnější hodnotu pomocí jména uživatele správce, který spouští proces importu, jak je znázorněno níže.

```sql
DELIMITER;;
/*!50003 CREATE*/ /*!50017 DEFINER=`root`@`127.0.0.1`*/ /*!50003
DELIMITER;;

/* Modified to */

DELIMITER ;;
/*!50003 CREATE*/ /*!50017 DEFINER=`AdminUserName`@`ServerName`*/ /*!50003
DELIMITER ;
```
#### <a name="error-1227-42000-at-line-295-access-denied-you-need-at-least-one-of-the-super-or-set_user_id-privileges-for-this-operation"></a>Chyba 1227 (42000) na řádku 295: přístup byl odepřen; potřebujete (aspoň jedno z) oprávnění SUPER nebo SET_USER_ID pro tuto operaci.

K výše uvedené chybě může dojít při provádění příkazu CREATE VIEW s příkazy DEFINe v rámci importu souboru s výpisem paměti nebo spuštěním skriptu. Azure Database for MySQL pro žádného uživatele nepovoluje oprávnění SUPER nebo SET_USER_ID. 

**Řešení**: 
* Pokud je to možné, použijte uživatele definující, aby spustil zobrazení vytvořit. Je možné, že existuje mnoho zobrazení s různými podrobnějšími oprávněními, takže to nemusí být proveditelné.  NEBO
* Upravte soubor s výpisem paměti nebo vytvořte skript pro zobrazení a odeberte příkaz DEFINe = ze souboru s výpisem paměti nebo 
* Upravte soubor s výpisem paměti nebo vytvořte skript pro zobrazení a nahraďte hodnoty definované uživatelem s oprávněními správce, které provádí import nebo spuštění souboru skriptu.

> [!Tip] 
> Pomocí příkazu SED nebo Perl upravte soubor s výpisem paměti nebo skript SQL, který nahradí příkaz DEFINe =.

## <a name="next-steps"></a>Další kroky
Pokud jste nenašli odpověď, kterou jste hledali, zvažte následující:
- Vystavte svůj dotaz na [Microsoft Q&stránku s dotazy](/answers/topics/azure-database-mysql.html) nebo [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql).
- Odešlete e-mail do Azure Database for MySQL týmu [ @Ask Azure DB pro MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com). Tato e-mailová adresa není alias technické podpory.
- Obraťte se na podporu Azure, [zasoubor lístku z Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Pokud řešíte problém s účtem, podejte prostřednictvím webu Azure Portal [žádost o podporu](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
- Pokud nám chcete sdělit svůj názor nebo požádat o nové funkce, využijte nástroj [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).
