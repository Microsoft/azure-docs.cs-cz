---
title: Zabezpečení Azure SQL Edge
description: Další informace o zabezpečení ve službě Azure SQL Edge
keywords: SQL Edge, zabezpečení
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 56d5eb981aa02d9da83973d49e8df79fcd9c7e9c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95021010"
---
# <a name="securing-azure-sql-edge"></a>Zabezpečení Edge Azure SQL

Díky nárůstu přijetí IoT a hraničního zpracování napříč obory dochází ke zvýšení počtu zařízení a dat vygenerovaných z těchto zařízení. Zvýšený objem dat a počet koncových bodů zařízení přináší významnou výzvu z pohledu zabezpečení dat a zařízení. 

Azure SQL Edge nabízí několik funkcí a možností, díky kterým je poměrně snazší zabezpečit data IoT v rámci SQL Serverch databází. Azure SQL Edge se sestavuje pomocí stejného stroje SQL, který je součástí Microsoft SQL Server a Azure SQL a sdílí stejné možnosti zabezpečení, což usnadňuje rozšiřování stejných zásad a postupů zabezpečení z cloudu na hranici.

Stejně jako u Microsoft SQL Server a Azure SQL je možné zabezpečení nasazení Azure SQL Edge zobrazit jako řadu kroků, které se týkají čtyř oblastí: platformy, ověřování, objektů (včetně dat) a aplikací, které přistupují k systému. 

## <a name="platform-and-system-security"></a>Zabezpečení platformy a systému

Platforma pro Azure SQL Edge zahrnuje hostitele fyzického Docker, operační systém na hostiteli a síťové systémy připojující fyzické zařízení k aplikacím a klientům. 

Implementace zabezpečení platformy se spouští s udržováním neautorizovaných uživatelů mimo síť. Mezi Doporučené postupy patří, ale nejsou omezené na tyto:
- Implementují se pravidla brány firewall pro zajištění zásad zabezpečení organizace. 
- Ujistěte se, že v operačním systému na fyzickém zařízení jsou použité všechny nejnovější aktualizace zabezpečení. 
- Určení a omezení portů hostitele, které se používají pro Azure SQL Edge
- Ujistěte se, že se na všechny datové svazky, které hostují data Azure SQL Edge, aplikuje správné řízení přístupu. 

Další informace o síťových protokolech Azure SQL Edge a koncových bodech TDS najdete v těchto [síťových protokolech a koncových bodech TDS](/previous-versions/sql/sql-server-2008-r2/ms191220(v=sql.105)).

## <a name="authentication-and-authorization"></a>Ověřování a autorizace 

### <a name="authentication"></a>Authentication  
Ověřování je proces, který označuje, že uživatel vyžádá. Azure SQL Edge aktuálně podporuje jenom `SQL Authentication` mechanismus.

- *Ověřování SQL*:

    Ověřování SQL odkazuje na ověření uživatele při připojování k Edge SQL Azure pomocí uživatelského jména a hesla. Heslo pro přihlášení k SQL **SA** je potřeba zadat během nasazování SQL Edge. Potom může správce serveru vytvořit další přihlášení a uživatele SQL, která uživatelům umožňují připojit se pomocí uživatelského jména a hesla.

    Další informace o vytváření a správě přihlašovacích údajů a uživatelů v rámci SQL Edge najdete v tématu [Vytvoření přihlášení](/sql/relational-databases/security/authentication-access/create-a-login) a [Vytvoření uživatele databáze](/sql/relational-databases/security/authentication-access/create-a-database-user).

### <a name="authorization"></a>Autorizace   

Autorizace odkazuje na oprávnění přiřazená uživateli v rámci databáze ve službě Azure SQL Edge a určuje, co může uživatel dělat. Oprávnění jsou řízena přidáním uživatelských účtů do [databázových rolí](/sql/relational-databases/security/authentication-access/database-level-roles) a přiřazením oprávnění na úrovni databáze k těmto rolím nebo tím, že uživatel udělí určitá [oprávnění na úrovni objektu](/sql/relational-databases/security/permissions-database-engine). Další informace najdete v tématu [přihlášení a uživatelé](../azure-sql/database/logins-create-manage.md).

Osvědčeným postupem je v případě potřeby vytvořit vlastní role. Přidejte uživatele do role s nejnižšími oprávněními potřebnými k provedení jejich pracovní funkce. Nepřiřazujte oprávnění přímo uživatelům. Účet správce serveru je členem předdefinované role db_owner, která má rozsáhlá oprávnění a měla by být udělována pouze malým uživatelům s administrativními cly. Pro aplikace použijte příkaz [Spustit jako](/sql/t-sql/statements/execute-as-clause-transact-sql) a určete kontext spuštění volaného modulu nebo použijte [aplikační role](/sql/relational-databases/security/authentication-access/application-roles) s omezenými oprávněními. Tento postup zajišťuje, že aplikace, která se připojuje k databázi, má nejnižší oprávnění, která aplikace potřebuje. Tyto osvědčené postupy také podporují oddělení povinností.

## <a name="database-object-security"></a>Zabezpečení databázových objektů

Objekty zabezpečení jsou jednotlivci, skupiny a procesy s uděleným přístupem k SQL Edge. "Zabezpečit" jsou servery, databáze a objekty, které databáze obsahuje. Každá z nich má sadu oprávnění, která se dají nakonfigurovat tak, aby se snížila plocha. Následující tabulka obsahuje informace o objektech zabezpečení a zabezpečit.

|Pro informace o|Seznamte se s |  
|---------------------------|---------|  
|Uživatelé, role a procesy serveru a databáze|[Databázový stroj objektů zabezpečení](/sql/relational-databases/security/authentication-access/principals-database-engine)|  
|Zabezpečení serveru a databázových objektů|[Migrovány zabezpečitelné objekty](/sql/relational-databases/security/securables)|
| &nbsp; | &nbsp; |

### <a name="encryption-and-certificates"></a>Šifrování a certifikáty  
 
Šifrování neřeší problémy řízení přístupu. Vylepšuje zabezpečení ale omezuje ztrátu dat, a to i ve vzácných případech, kdy se ovládací prvky přístupu nepoužívají. Pokud je například počítač hostitele databáze nesprávně nakonfigurovaný a uživatel se zlými úmysly získá citlivá data, jako jsou třeba čísla kreditních karet, mohou být odcizené informace zbytečné, pokud jsou zašifrované. Následující tabulka obsahuje další informace o šifrování ve službě Azure SQL Edge.  
  
|Pro informace o|Seznamte se s |  
|---------------------------|---------|  
|Implementace zabezpečených připojení|[Šifrování připojení](/sql/linux/sql-server-linux-encrypted-connections)|  
|Funkce šifrování|[Kryptografické funkce &#40;Transact-SQL&#41;](/sql/t-sql/functions/cryptographic-functions-transact-sql)|
|Šifrování dat v klidovém umístění|[Transparentní šifrování dat](/sql/relational-databases/security/encryption/transparent-data-encryption)|
|Always Encrypted|[Funkce Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)|
| &nbsp; | &nbsp; |

> [!NOTE]
> Omezení zabezpečení popsaná pro [SQL Server on Linux](/sql/linux/sql-server-linux-security-overview) platí i pro Azure SQL Edge. 


> [!NOTE]
> Azure SQL Edge nezahrnuje nástroj MSSQL-conf. Všechny konfigurace, včetně konfigurace související s šifrováním, se musí provádět prostřednictvím [souboru MSSQL. conf](configure.md#configure-by-using-an-mssqlconf-file) nebo [proměnných prostředí](configure.md#configure-by-using-environment-variables). 


Podobně jako u Azure SQL a Microsoft SQL Server poskytuje Azure SQL Edge stejný mechanismus pro vytváření a používání certifikátů ke zvýšení zabezpečení objektů a připojení. Další informace najdete v tématu [Vytvoření certifikátu (Transact-SQL)](/sql/t-sql/statements/create-certificate-transact-sql).


## <a name="application-security"></a>Zabezpečení aplikací

### <a name="client-programs"></a>Klientské programy

Mezi osvědčené postupy zabezpečení Azure SQL Edge patří vytváření zabezpečených klientských aplikací. Další informace o tom, jak lépe zabezpečit klientské aplikace v síťové vrstvě, najdete v tématu [Konfigurace sítě klienta](/sql/database-engine/configure-windows/client-network-configuration).

### <a name="security-catalog-views-and-functions"></a>Zobrazení a funkce katalogu zabezpečení  
Informace o zabezpečení jsou zpřístupněny v několika zobrazeních a funkcích, které jsou optimalizovány pro výkon a nástroj. Následující tabulka obsahuje informace o zobrazení a funkcích zabezpečení v Azure SQL Edge.  
  
|Funkce a zobrazení|Odkazy|  
|---------------------------|---------|  
|Zobrazení katalogu zabezpečení, které vrací informace o oprávněních na úrovni databáze a na úrovni serveru, objektech zabezpečení, rolích atd. K dispozici jsou také Katalogová zobrazení, která poskytují informace o šifrovacích klíčích, certifikátech a přihlašovacích údajích.|[Zobrazení katalogu zabezpečení &#40;Transact-SQL&#41;](/sql/relational-databases/system-catalog-views/security-catalog-views-transact-sql)|  
|Funkce zabezpečení, které vracejí informace o aktuálním uživateli, oprávněních a schématech.|[Funkce zabezpečení &#40;Transact-SQL&#41;](/sql/t-sql/functions/security-functions-transact-sql)|  
|Zobrazení dynamické správy zabezpečení|[Zobrazení a funkce dynamické správy související se zabezpečením &#40;Transact-SQL&#41;](/sql/relational-databases/system-dynamic-management-views/security-related-dynamic-management-views-and-functions-transact-sql)|  
| &nbsp; | &nbsp; |

### <a name="auditing"></a>Auditování 

Azure SQL Edge nabízí stejné mechanismy auditování jako SQL Server. Další informace najdete v tématu [SQL Server Audit (databázový stroj)](/sql/relational-databases/security/auditing/sql-server-audit-database-engine).


## <a name="next-steps"></a>Další kroky

- [Začínáme s funkcemi zabezpečení](/sql/linux/sql-server-linux-security-get-started)
- [Spuštění Azure SQL Edge jako nerootový uživatel](configure.md#run-azure-sql-edge-as-non-root-user)
- [Azure Security Center pro IoT](../defender-for-iot/overview.md)