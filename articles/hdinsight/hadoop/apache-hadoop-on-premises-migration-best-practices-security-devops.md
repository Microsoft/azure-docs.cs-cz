---
title: 'Zabezpečení: Migrace místního Apache Hadoop do Azure HDInsight'
description: Seznamte se s doporučenými postupy zabezpečení a devops pro migraci místních clusterů Hadoop do Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/19/2019
ms.openlocfilehash: 4ceefcbbbb53e3ae13f8ced930ae8417fb00965f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75974409"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---security-and-devops-best-practices"></a>Migrace místních clusterů Apache Hadoop do Azure HDInsight – doporučené postupy zabezpečení a DevOps

Tento článek obsahuje doporučení pro zabezpečení a DevOps v systémech Azure HDInsight. Je součástí řady, která poskytuje osvědčené postupy, které vám pomohou s migrací místních systémů Apache Hadoop do Azure HDInsight.

## <a name="secure-and-govern-cluster-with-enterprise-security-package"></a>Zabezpečení a správa clusteru pomocí balíčku enterprise security package

Balíček ESP (Enterprise Security Package) podporuje ověřování založené na službě Active Directory, podporu více uživatelů a řízení přístupu na základě rolí. S vybranou možností ESP je cluster HDInsight připojen k doméně služby Active Directory a správce rozlehlé sítě může nakonfigurovat řízení přístupu na základě rolí (RBAC) pro zabezpečení Apache Hive pomocí Apache Ranger. Správce může také auditovat přístup zaměstnanců k datům a všechny změny provedené v zásadách řízení přístupu.

ESP je k dispozici na následujících typech clusterů: Apache Hadoop, Apache Spark, Apache HBase, Apache Kafka a Interaktivní dotaz (Hive LLAP).

Pomocí následujících kroků nasadit cluster HDInsight přilehlý k doméně:

- Nasazení služby Azure Active Directory (AAD) předáním názvu domény.
- Nasazení služby Azure Active Directory Domain Services (AAD DS).
- Vytvořte požadovanou virtuální síť a podsíť.
- Nasazení virtuálního počítače ve virtuální síti ke správě Služby AAD DS.
- Připojte virtuální ho k doméně.
- Nainstalujte nástroje AD a DNS.
- Chcete, aby správce služby AAD DS vytvořil organizační jednotku (OU).
- Povolte protokol LDAPS pro systém AAD DS.
- Vytvořte účet služby ve službě Azure Active Directory s delegovanými & oprávnění správce zápisu do hlavní účetní hodnoty, aby mohla. Tento účet služby pak můžete připojit počítače k doméně a umístit objekty počítače v rámci ou. Může také vytvořit instanční objekty v rámci ou, které zadáte při vytváření clusteru.

    > [!Note]
    > Účet služby nemusí být účet správce domény služby AD.

- Nasazení clusteru HDInsight ESP nastavením následujících parametrů:

    |Parametr |Popis |
    |---|---|
    |Název domény|Název domény, který je přidružený k Azure AD DS.|
    |Uživatelské jméno domény|Účet služby v doméně spravované službou Azure AD DS DC, `hdiadmin@contoso.onmicrosoft.com`kterou jste vytvořili v předchozí části, například: . Tento uživatel domény bude správcem tohoto clusteru HDInsight.|
    |Heslo domény|Heslo účtu služby.|
    |Organizační jednotka|Rozlišující název ou, kterou chcete použít v clusteru `OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com`HDInsight, například: . Pokud tato nejetá neexistuje, pokusí se cluster HDInsight vytvořit ou pomocí oprávnění účtu služby.|
    |LDAPS URL|například `ldaps://contoso.onmicrosoft.com:636`.|
    |Přístup ke skupině uživatelů|Skupiny zabezpečení, jejichž uživatele chcete synchronizovat se `HiveUsers`clusterem, například: . Chcete-li zadat více skupin uživatelů, oddělte je středníkem ';'. Skupiny musí existovat v adresáři před vytvořením clusteru ESP.|

Další informace najdete v těchto článcích:

- [Úvod do zabezpečení Apache Hadoop s clustery HDInsight přilehlými k doméně](../domain-joined/hdinsight-security-overview.md)
- [Plánování clusterů Apache Hadoop přilehlých k doméně Azure ve hdinsightu](../domain-joined/apache-domain-joined-architecture.md)
- [Konfigurace clusteru HDInsight přilehlého k doméně pomocí služby Azure Active Directory Domain Services](../domain-joined/apache-domain-joined-configure-using-azure-adds.md)
- [Synchronizace uživatelů Azure Active Directory do clusteru HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
- [Konfigurace zásad Apache Hive v hdinsightu přilehlém k doméně](../domain-joined/apache-domain-joined-run-hive.md)
- [Spusťte Apache Oozie v clusterech HDInsight Hadoop přilehlých k doméně](../domain-joined/hdinsight-use-oozie-domain-joined-clusters.md)

## <a name="implement-end-to-end-enterprise-security"></a>Implementace zabezpečení rozlehlé sítě od konce

Zabezpečení podniku od konce do konce lze dosáhnout pomocí následujících ovládacích prvků:

**Privátní a chráněný datový kanál (zabezpečení na úrovni obvodu)**
    - Zabezpečení na úrovni obvodu lze dosáhnout prostřednictvím virtuálních sítí Azure, skupin zabezpečení sítě a služby Gateway.

**Ověřování a autorizace pro přístup k datům**
    - Vytvořte cluster HDInsight spojený s doménou pomocí služby Azure Active Directory Domain Services. (Balíček enterprise security).
    - Pomocí aplikace Ambari můžete uživatelům služby AD poskytovat přístup k prostředkům clusteru založený na rolích.
    - Pomocí Apache Ranger nastavte zásady řízení přístupu pro Hive na úrovni tabulky / sloupce / řádku.
    - Přístup SSH ke clusteru lze omezit pouze na správce.

**Auditování**
    - Zobrazte a oznamte veškerý přístup k prostředkům a datům clusteru HDInsight.
    - Zobrazení a hlášení všech změn zásad řízení přístupu.

**Šifrování**
    - Transparentní šifrování na straně serveru pomocí klíčů spravovaných společností Microsoft nebo klíčů spravovaných zákazníky.
    - Šifrování v tranzitu pomocí šifrování na straně klienta, https a TLS.

Další informace najdete v těchto článcích:

- [Přehled virtuálních sítí Azure](../../virtual-network/virtual-networks-overview.md)
- [Přehled skupin zabezpečení sítě Azure](../../virtual-network/security-overview.md)
- [Partnerský vztah virtuální sítě Azure](../../virtual-network/virtual-network-peering-overview.md)
- [Průvodce zabezpečením azure úložiště](../../storage/blobs/security-recommendations.md)
- [Šifrování služby Azure Storage Service v klidovém stavu](../../storage/common/storage-service-encryption.md)

## <a name="use-monitoring--alerting"></a>Použití výstrahy & monitorování

Další informace naleznete v článku:

[Přehled služby Azure Monitor](../../azure-monitor/overview.md)

## <a name="upgrade-clusters"></a>Upgrade clusterů

Pravidelně upgradujte na nejnovější verzi HDInsight, abyste využili nejnovější funkce. Následující kroky lze použít k upgradu clusteru na nejnovější verzi:

1. Vytvořte nový cluster TEST HDInsight pomocí nejnovější dostupné verze HDInsight.
1. Otestujte na novém clusteru a ujistěte se, že úlohy a úlohy fungují podle očekávání.
1. Podle potřeby upravte úlohy nebo aplikace nebo úlohy.
1. Zálohujte všechna přechodná data uložená místně v uzlech clusteru.
1. Odstraňte existující cluster.
1. Vytvořte cluster nejnovější verze HDInsight ve stejné podsíti virtuální sítě pomocí stejných výchozích dat a meta úložiště jako předchozí cluster.
1. Importujte všechna přechodná data, která byla zálohována.
1. Spusťte úlohy/pokračujte ve zpracování pomocí nového clusteru.

Další informace naleznete v [článku: Upgrade clusteru HDInsight na novou verzi](../hdinsight-upgrade-cluster.md).

## <a name="patch-cluster-operating-systems"></a>Operační systémy clusteru patch

Jako spravovaná služba Hadoop se HDInsight stará o opravy operačního systému virtuálních mích používaných clustery HDInsight.

Další informace naleznete v [článku: Opravy operačního systému pro HDInsight](../hdinsight-os-patching.md).

## <a name="post-migration"></a>Po migraci

1. **Nápravné aplikace** – Iterativně provést nezbytné změny úloh, procesů a skriptů.
2. **Provádět testy** - Iterativně spustit funkční a testy výkonu.
3. **Optimalizovat** – řešení všech problémů s výkonem na základě výše uvedených výsledků testu a potom znovu otestovat potvrzení zlepšení výkonu.

## <a name="next-steps"></a>Další kroky

Přečtěte si více o [HDInsight 4.0](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-introduction).
