---
title: Synchronizace protokolu LDAP v Ranger a Apache Ambari ve službě Azure HDInsight
description: Vyřešte synchronizaci LDAP v Ranger a Ambari a poskytněte obecné pokyny.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 99bd1ac156b12a5be7b8c5c17eb5b568b7070a25
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "77463216"
---
# <a name="ldap-sync-in-ranger-and-apache-ambari-in-azure-hdinsight"></a>Synchronizace protokolu LDAP v Ranger a Apache Ambari ve službě Azure HDInsight

Clustery HDInsight Balíček zabezpečení podniku (ESP) používají Ranger k autorizaci. Apache Ambari a Ranger synchronizuje uživatele i skupiny nezávisle a pracují trochu jinak. Tento článek má řešit synchronizaci LDAP v Ranger a Ambari.

## <a name="general-guidelines"></a>Obecné pokyny

* Vždy nasaďte clustery se skupinami.
* Místo změny skupinových filtrů v Ambari a Ranger se pokuste spravovat všechny tyto filtry ve službě Azure AD a použít vnořené skupiny k uvedení požadovaných uživatelů.
* Jakmile bude uživatel synchronizovaný, nebude odebrán ani v případě, že uživatel není součástí skupin.
* Pokud potřebujete přímo změnit filtry LDAP, použijte uživatelské rozhraní jako první, protože obsahuje nějaké ověření.

## <a name="users-are-synced-separately"></a>Uživatelé se synchronizují samostatně.

Ambari a Ranger nesdílí uživatelskou databázi, protože slouží dvěma různým účelům. Pokud uživatel potřebuje použít uživatelské rozhraní Ambari, musí být tento uživatel synchronizovaný na Ambari. Pokud uživatel není synchronizovaný s Ambari, uživatelské rozhraní nebo rozhraní API Ambari ho zamítne, ale ostatní části systému budou fungovat (budou chráněné pomocí Ranger nebo Správce prostředků a ne Ambari). Pokud chcete zahrnout uživatele do zásad Ranger, synchronizujte uživatele s Ranger.

Když se nasadí zabezpečený cluster, členové skupiny se budou synchronizovat přes Ambari i Ranger. 

## <a name="ambari-user-sync-and-configuration"></a>Synchronizace a konfigurace uživatelů Ambari

Z hlavních uzlů se úloha cron `/opt/startup_scripts/start_ambari_ldap_sync.py` spouští každou hodinu a naplánuje synchronizaci uživatelů. Úloha cron volá rozhraní Ambari REST API k provedení synchronizace. Skript odešle seznam uživatelů a skupin, které se mají synchronizovat (protože uživatelé nepatří do zadaných skupin, obě jsou zadány jednotlivě). Ambari synchronizuje sAMAccountName jako uživatelské jméno a všechny členy skupiny.

Protokoly by měly být v `/var/log/ambari-server/ambari-server.log` . Další informace najdete v tématu [Konfigurace úrovně protokolování Ambari](https://docs.cloudera.com/HDPDocuments/Ambari-latest/administering-ambari/content/amb_configure_ambari_logging_level.html).

V Data Lakech clusterech se k vytváření domovských složek pro synchronizované uživatele a jejich nastavení jako vlastníci domovských složek používá zavěšení vytvoření uživatele. Pokud uživatel není synchronizovaný tak, aby se Ambari správně, mohl by se uživateli při přístupu k přípravné a jiné dočasné složce setkat chyba.

### <a name="update-groups-to-be-synced-to-ambari"></a>Aktualizace skupin, které se mají synchronizovat s Ambari

Pokud ve službě Azure AD nemůžete spravovat členství ve skupinách, máte dvě možnosti:

* Proveďte jednu synchronizaci, jak je popsáno podrobněji v tématu [synchronizace uživatelů a skupin LDAP](https://docs.cloudera.com/HDPDocuments/HDP3/latest/ambari-authentication-ldap-ad/content/authe_ldapad_synchronizing_ldap_users_and_groups.html). Pokaždé, když se změní členství ve skupině, bude nutné provést synchronizaci znovu.

* Napíšete úlohu cron a [pravidelně volejte rozhraní Ambari API](https://community.cloudera.com/t5/Support-Questions/How-do-I-automate-the-Ambari-LDAP-sync/m-p/96634) s novými skupinami.

## <a name="ranger-user-sync-and-configuration"></a>Synchronizace a konfigurace uživatelů Ranger

Ranger má sestavený modul synchronizace, který se při synchronizaci uživatelů spouští každou hodinu. Nesdílí uživatelskou databázi s Ambari. HDInsight nakonfiguruje vyhledávací filtr tak, aby synchronizoval uživatele správce, sledovacího uživatele a členy skupiny zadané během vytváření clusteru. Členové skupiny se budou synchronizovat po cestě:

* Zakáže přírůstkovou synchronizaci.
* Povolit mapování skupin uživatelů na synchronizaci
* Zadejte vyhledávací filtr, který bude obsahovat členy přenositelných skupin.
* Synchronizace sAMAccountName pro uživatele a atribut Name pro skupiny.

### <a name="group-or-incremental-sync"></a>Skupina nebo přírůstková synchronizace

Ranger podporuje možnost synchronizace skupin, ale funguje jako průnik s uživatelským filtrem. Nejedná se o sjednocení mezi členstvím ve skupinách a filtrem uživatelů. Typický případ použití pro filtr synchronizace skupin v Ranger je filtr skupiny: (DN = clusteradmingroup), uživatelský filtr: (City = Seattle).

Přírůstková synchronizace funguje pouze pro uživatele, kteří jsou již synchronizováni (první čas). Přírůstkové neproběhne synchronizace nových uživatelů přidaných do skupin po počáteční synchronizaci.

### <a name="update-ranger-sync-filter"></a>Aktualizovat filtr Ranger Sync

Filtr protokolu LDAP najdete v uživatelském rozhraní Ambari v části Konfigurace Ranger uživatele – synchronizace. Existující filtr bude ve formuláři `(|(userPrincipalName=bob@contoso.com)(userPrincipalName=hdiwatchdog-core01@CONTOSO.ONMICROSOFT.COM)(memberOf:1.2.840.113556.1.4.1941:=CN=hadoopgroup,OU=AADDC Users,DC=contoso,DC=onmicrosoft,DC=com))` . Ujistěte se, že na konec přidáte predikát a otestujete filtr pomocí `net ads` příkazu Search nebo ldp.exe nebo podobného.

## <a name="ranger-user-sync-logs"></a>Protokoly synchronizace uživatelů Ranger

K synchronizaci uživatelů Ranger může dojít z některého z hlavních. Protokoly jsou v `/var/log/ranger/usersync/usersync.log` . Chcete-li zvýšit úroveň podrobností protokolů, proveďte následující kroky:

1. Přihlaste se k Ambari.
1. Přejít do konfiguračního oddílu Ranger.
1. Přejít na oddíl Advanced **usersync-log4j** .
1. Změňte na `log4j.rootLogger` `DEBUG` úroveň (po změně by měla vypadat podobně jako `log4j.rootLogger = DEBUG,logFile,FilterLog` ).
1. Uložte konfiguraci a restartuje Ranger.

## <a name="next-steps"></a>Další kroky

* [Problémy s ověřováním ve službě Azure HDInsight](./domain-joined-authentication-issues.md)
* [Synchronizace uživatelů Azure AD do clusteru služby HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
