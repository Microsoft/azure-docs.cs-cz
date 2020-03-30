---
title: Synchronizace LDAP v Rangeru a Apache Ambari v Azure HDInsight
description: Adresuujte synchronizaci LDAP v Rangeru a Ambari a poskytněte obecné pokyny.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 99bd1ac156b12a5be7b8c5c17eb5b568b7070a25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77463216"
---
# <a name="ldap-sync-in-ranger-and-apache-ambari-in-azure-hdinsight"></a>Synchronizace LDAP v Rangeru a Apache Ambari v Azure HDInsight

Clustery balíčků HDInsight Enterprise Security Package (ESP) používají k autorizaci ranger. Apache Ambari a Ranger synchronizují uživatele i skupiny nezávisle a pracují trochu jinak. Tento článek je určen k řešení synchronizace LDAP v Ranger a Ambari.

## <a name="general-guidelines"></a>Obecné pokyny

* Vždy nasazujte clustery se skupinami.
* Namísto změny filtrů skupin v Ambari a Ranger, zkuste spravovat všechny tyto ve službě Azure AD a pomocí vnořených skupin, aby v požadované uživatele.
* Po synchronizaci uživatele není odebrán, i když uživatel není součástí skupin.
* Pokud potřebujete změnit filtry LDAP přímo, použijte nejprve ui, protože obsahuje některá ověření.

## <a name="users-are-synced-separately"></a>Uživatelé jsou synchronizováni samostatně

Ambari a Ranger nesdílejí databázi uživatelů, protože slouží dvěma různým účelům. Pokud uživatel potřebuje použít uživatelské rozhraní Ambari, pak uživatel musí být synchronizováns Ambari. Pokud uživatel není synchronizován s Ambari, Ambari UI / API odmítne, ale ostatní části systému bude fungovat (ty jsou střeženy Ranger nebo Resource Manager a ne Ambari). Pokud chcete zahrnout uživatele do zásadranger, pak synchronizovat uživatele ranger.

Při nasazení zabezpečeného clusteru jsou členové skupiny synchronizováni přechodně (všechny podskupiny a jejich členové) s Ambari i Ranger. 

## <a name="ambari-user-sync-and-configuration"></a>Synchronizace a konfigurace uživatelů Ambari

Z hlavníu uzly, `/opt/startup_scripts/start_ambari_ldap_sync.py`cron úlohy, , je spuštěn každou hodinu naplánovat synchronizaci uživatele. Úloha cron volá ambari klid ová api k provedení synchronizace. Skript odešle seznam uživatelů a skupin k synchronizaci (protože uživatelé nemusí patřit do zadaných skupin, oba jsou určeny jednotlivě). Ambari synchronizuje sAMAccountName jako uživatelské jméno a všechny členy skupiny, přechodně.

Protokoly by měly být v `/var/log/ambari-server/ambari-server.log`. Další informace naleznete v [tématu Configure Ambari logging level](https://docs.cloudera.com/HDPDocuments/Ambari-latest/administering-ambari/content/amb_configure_ambari_logging_level.html).

V clusterech Data Lake se k vytvoření domovských složek pro synchronizované uživatele používá zavěšení uživatelů, kteří jsou nastaveni jako vlastníci domácích složek. Pokud uživatel není synchronizován s Ambari správně, pak uživatel může čelit selhání v přístupu k pracovní a další dočasné složky.

### <a name="update-groups-to-be-synced-to-ambari"></a>Aktualizovat skupiny, které mají být synchronizovány s Ambari

Pokud nemůžete spravovat členství ve skupinách ve službě Azure AD, máte dvě možnosti:

* Proveďte jednorázovou synchronizaci, jak je popsáno ve větší míře na [synchronizaci uživatelů a skupin LDAP](https://docs.cloudera.com/HDPDocuments/HDP3/latest/ambari-authentication-ldap-ad/content/authe_ldapad_synchronizing_ldap_users_and_groups.html). Kdykoli se změní členství ve skupině, budete muset tuto synchronizaci provést znovu.

* Napište úlohu cron, pravidelně volejte [Rozhraní API Ambari](https://community.cloudera.com/t5/Support-Questions/How-do-I-automate-the-Ambari-LDAP-sync/m-p/96634) s novými skupinami.

## <a name="ranger-user-sync-and-configuration"></a>Synchronizace a konfigurace uživatele rangeru

Ranger má vestavěný synchronizační modul, který běží každou hodinu pro synchronizaci uživatelů. Nesdílí uživatelskou databázi s Ambari. HDInsight konfiguruje vyhledávací filtr pro synchronizaci uživatele správce, uživatele hlídacího psa a členů skupiny zadané během vytváření clusteru. Členové skupiny budou synchronizováni přechodně:

* Zakažte přírůstkovou synchronizaci.
* Povolit mapování synchronizace skupin uživatelů.
* Zadejte vyhledávací filtr, který má být zahrnut mezi členy přenosité skupiny.
* Synchronizace sAMAccountName pro uživatele a atribut name pro skupiny.

### <a name="group-or-incremental-sync"></a>Seskupení nebo přírůstková synchronizace

Ranger podporuje možnost synchronizace skupiny, ale funguje jako průsečík s uživatelským filtrem. Není sjednocení mezi členstvíve skupiny a uživatelský filtr. Typický případ použití pro filtr synchronizace skupiny v Ranger je - filtr skupiny: (dn=clusteradmingroup), uživatelský filtr: (město = seattle).

Přírůstková synchronizace funguje pouze pro uživatele, kteří jsou již synchronizováni (poprvé). Přírůstkové nebude synchronizovat žádné nové uživatele přidané do skupin po počáteční synchronizaci.

### <a name="update-ranger-sync-filter"></a>Aktualizovat filtr synchronizace rangeru

Filtr LDAP naleznete v uživatelském rozhraní Ambari v části Konfigurace synchronizace uživatelů rangeru. Existující filtr bude ve `(|(userPrincipalName=bob@contoso.com)(userPrincipalName=hdiwatchdog-core01@CONTOSO.ONMICROSOFT.COM)(memberOf:1.2.840.113556.1.4.1941:=CN=hadoopgroup,OU=AADDC Users,DC=contoso,DC=onmicrosoft,DC=com))`formuláři . Ujistěte se, že přidáte predikát na `net ads` konci a otestujte filtr pomocí příkazu search nebo ldp.exe nebo něčeho podobného.

## <a name="ranger-user-sync-logs"></a>Protokoly synchronizace uživatelů rangerů

Synchronizace uživatelů rangerů může dojít z jednoho z headnodes. Protokoly jsou `/var/log/ranger/usersync/usersync.log`v . Chcete-li zvýšit podrobnost protokolů, postupujte takto:

1. Přihlaste se do Ambari.
1. Přejděte do konfigurační části Ranger.
1. Přejděte do části Advanced **usersync-log4j.**
1. Změňte `log4j.rootLogger` `DEBUG` úroveň na úroveň (Po `log4j.rootLogger = DEBUG,logFile,FilterLog`změně by měla vypadat).
1. Uložte konfiguraci a restartujte ranger.

## <a name="next-steps"></a>Další kroky

* [Problémy s ověřováním v Azure HDInsightu](./domain-joined-authentication-issues.md)
* [Synchronizace uživatelů Azure AD do clusteru služby HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
