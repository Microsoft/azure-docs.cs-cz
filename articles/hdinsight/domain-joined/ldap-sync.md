---
title: Synchronizace protokolu LDAP v Ranger a Apache Ambari ve službě Azure HDInsight
description: Vyřešte synchronizaci LDAP v Ranger a Ambari a poskytněte obecné pokyny.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: fb9035b4d816c1af84b15e6865335aa1bdf86f5b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98933409"
---
# <a name="ldap-sync-in-ranger-and-apache-ambari-in-azure-hdinsight"></a>Synchronizace protokolu LDAP v Ranger a Apache Ambari ve službě Azure HDInsight

Clustery HDInsight Balíček zabezpečení podniku (ESP) používají Ranger k autorizaci. Apache Ambari a Ranger synchronizuje uživatele i skupiny nezávisle a pracují trochu jinak. Tento článek má řešit synchronizaci LDAP v Ranger a Ambari.

## <a name="general-guidelines"></a>Obecné pokyny

* Vždy nasaďte clustery s jednou nebo více skupinami.
* Pokud chcete v clusteru použít více skupin, ověřte, jestli má smysl aktualizovat členství ve skupině v Azure Active Directory (Azure AD).
* Pokud chcete změnit skupiny clusterů, můžete změnit filtry synchronizace pomocí Ambari.
* Všechny změny členství ve skupinách v Azure AD se v clusterech projeví v následných synchronizacích. Změny musí být nejprve synchronizovány do Azure AD Domain Services (Azure služba AD DS) a následně do clusterů.
* Clustery HDInsight používají Samba/winbind k navýšení členství ve skupině na uzlech clusteru.
* Členové skupiny jsou synchronizovaný průjezdně (všechny podskupiny a jejich členové) do Ambari i Ranger. 

## <a name="users-are-synced-separately"></a>Uživatelé se synchronizují samostatně.

 * Ambari a Ranger nesdílí uživatelskou databázi, protože slouží dvěma různým účelům. 
   * Pokud uživatel potřebuje použít uživatelské rozhraní Ambari, musí být tento uživatel synchronizovaný na Ambari. 
   * Pokud uživatel není synchronizovaný s Ambari, uživatelské rozhraní nebo rozhraní API Ambari ho odmítne, ale ostatní části systému budou fungovat (ty jsou chráněné pomocí Ranger nebo Správce prostředků a ne podle Ambari).
   * Aby bylo možné zahrnout uživatele nebo skupiny v zásadách Ranger, musí být objekty zabezpečení explicitně synchronizovány v Ranger.

## <a name="ambari-user-sync-and-configuration"></a>Synchronizace a konfigurace uživatelů Ambari

Z hlavních uzlů se úloha cron `/opt/startup_scripts/start_ambari_ldap_sync.py` spouští každou hodinu a naplánuje synchronizaci uživatelů. Úloha cron volá rozhraní Ambari REST API k provedení synchronizace. Skript odešle seznam uživatelů a skupin, které se mají synchronizovat (protože uživatelé nepatří do zadaných skupin, obě jsou zadány jednotlivě). Ambari synchronizuje sAMAccountName jako uživatelské jméno a všechny členy skupiny.

Protokoly by měly být v `/var/log/ambari-server/ambari-server.log` . Další informace najdete v tématu [Konfigurace úrovně protokolování Ambari](https://docs.cloudera.com/HDPDocuments/Ambari-latest/administering-ambari/content/amb_configure_ambari_logging_level.html).

V Data Lakech clusterech se k vytváření domovských složek pro synchronizované uživatele a jejich nastavení jako vlastníci domovských složek používá zavěšení vytvoření uživatele. Pokud uživatel není synchronizovaný, aby se Ambari správně, mohl by se uživateli při spouštění úloh setkat s chybami, protože Domovská složka nemusí být nastavená správně.

## <a name="ranger-user-sync-and-configuration"></a>Synchronizace a konfigurace uživatelů Ranger

Ranger má integrovaný synchronizační modul, který se každou hodinu spouští k synchronizaci uživatelů. Nesdílí uživatelskou databázi s Ambari. HDInsight nakonfiguruje vyhledávací filtr tak, aby synchronizoval uživatele správce, sledovacího uživatele a členy skupiny zadané během vytváření clusteru. Členové skupiny se budou synchronizovat po cestě:

1. Zakáže přírůstkovou synchronizaci.
1. Povolte mapování skupiny uživatelů na synchronizaci.
1. Zadejte vyhledávací filtr, který bude obsahovat členy přenositelných skupin.
1. Synchronizujte atribut sAMAccountName pro uživatele a atribut Name pro skupiny.

### <a name="group-or-incremental-sync"></a>Skupina nebo přírůstková synchronizace

Ranger podporuje možnost synchronizace skupin, ale funguje jako průnik s uživatelským filtrem, nikoli jako sjednocení mezi členstvím ve skupinách a filtrem uživatele. Typický případ použití pro filtr synchronizace skupin v Ranger je filtr skupiny: (DN = clusteradmingroup), uživatelský filtr: (City = Seattle).

Přírůstková synchronizace funguje pouze pro uživatele, kteří jsou již synchronizováni (první čas). Přírůstkové neproběhne synchronizace nových uživatelů přidaných do skupin po počáteční synchronizaci.

### <a name="update-ranger-sync-filter"></a>Aktualizovat filtr Ranger Sync

Filtr protokolu LDAP najdete v uživatelském rozhraní Ambari v části Konfigurace Ranger uživatele – synchronizace. Existující filtr bude ve formuláři `(|(userPrincipalName=bob@contoso.com)(userPrincipalName=hdiwatchdog-core01@CONTOSO.ONMICROSOFT.COM)(memberOf:1.2.840.113556.1.4.1941:=CN=hadoopgroup,OU=AADDC Users,DC=contoso,DC=onmicrosoft,DC=com))` . Ujistěte se, že na konec přidáte predikát a otestujete filtr pomocí `net ads` příkazu Search nebo ldp.exe nebo podobného.

## <a name="ranger-user-sync-logs"></a>Protokoly synchronizace uživatelů Ranger

K synchronizaci uživatelů Ranger může dojít z některého z hlavních. Protokoly jsou v `/var/log/ranger/usersync/usersync.log` . Chcete-li zvýšit úroveň podrobností protokolů, proveďte následující kroky:

1. Přihlaste se k Ambari.
1. Přejít do konfiguračního oddílu Ranger.
1. Přejít na oddíl Advanced **usersync-log4j** .
1. Změňte `log4j.rootLogger` na `DEBUG` úroveň. (Po jeho změně by měl vypadat jako `log4j.rootLogger = DEBUG,logFile,FilterLog` ).
1. Uložte konfiguraci a restartuje Ranger.

## <a name="known-issues-with-ranger-user-sync"></a>Známé problémy se synchronizací uživatelů Ranger
* Pokud má název skupiny znaky Unicode, synchronizace Ranger se nezdařila. Pokud uživatel patří do skupiny, která má mezinárodní znaky, Ranger synchronizuje členství v částečné skupině.
* Uživatelské jméno (sAMAccountName) a název skupiny (Name) musí být kratší než 20 znaků. Pokud je název skupiny delší, bude se uživateli při výpočtu oprávnění zacházet, jako by nepatří do skupiny.

## <a name="next-steps"></a>Další kroky

* [Problémy s ověřováním ve službě Azure HDInsight](./domain-joined-authentication-issues.md)
* [Synchronizace uživatelů Azure AD do clusteru služby HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
