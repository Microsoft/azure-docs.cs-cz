---
title: Migrace místních Apache Hadoop clusterů Azure HDInsight – zabezpečení a osvědčené postupy DevOps
description: Zjistěte, zabezpečení a osvědčené postupy DevOps pro migrace místních Hadoop clusterů pro Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: 9489d6e8780a30c5c54ee307d6c45c4bc2eb0e5d
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50419278"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---security-and-devops-best-practices"></a>Migrace místních Apache Hadoop clusterů Azure HDInsight – zabezpečení a osvědčené postupy DevOps

Tento článek obsahuje doporučení pro zabezpečení a DevOps v Azure HDInsight systémy. To je součástí série, která poskytuje osvědčené postupy pro pomoc s migrací místních systémů Apache Hadoop do Azure HDInsight.

## <a name="use-the-enterprise-security-package-to-secure-and-govern-the-cluster"></a>Použijte k zabezpečení a řízení clusteru Enterprise Security Package

Balíček zabezpečení podniku (ESP) podporuje ověřování na základě služby Active Directory, podpora více uživatelů a řízení přístupu na základě rolí. S parametrem ESP zvolili je HDInsight cluster připojený k doméně služby Active Directory a správce rozlehlé sítě můžete nakonfigurovat řízení přístupu na základě role (RBAC) pro zabezpečení Hivu pomocí Apache Ranger. Správce může také auditovat přístup k datům zaměstnanci a jakýchkoli změn provedených v zásadách řízení přístupu.

ESP funkce jsou aktuálně ve verzi preview a jsou dostupné pouze pro následující typy clusteru: Apache Hadoop, Apache Spark, Apache HBase, Apache Kafka a Apache Interactive Query.

Použijte následující postup k nasazení clusteru HDInsight připojené k doméně:

- Nasazení služby Azure Active Directory (AAD) předáním názvu domény
- Nasazení služby Azure Active Directory Domain Services (AAD DS)
- Vytvoření požadované virtuální sítě a podsítě
- Nasazení virtuálního počítače ve virtuální síti pro správu adresářové služby AAD
- Virtuální počítač připojit k doméně
- Instalace služby AD a nástroje pro DNS
- Požádejte správce adresářové služby AAD vytvořte organizační jednotce (OU)
- Povolení protokolu LDAPS pro službu AAD DS
- Vytvořte účet služby v Azure Active Directory s delegovaná oprávnění číst a správu oprávnění k zápisu do organizační jednotky, tak, aby to možné. Tento účet služby lze připojit počítače k doméně a umístit objekty zabezpečení počítačů v rámci organizační jednotky. Můžete také vytvořit instanční objekty v rámci organizační jednotky, které zadáte během vytváření clusteru.

    > [!Note]
    > Účet služby nemusí být účet správce domény AD

- Nasazení clusteru HDInsight ESP tak, že nastavíte následující parametry:
    - **Název domény**: název domény, který je spojen s Azure AD DS.
    - **Uživatelské jméno domény**: účet služby v DS řadič domény spravované doméně služby Azure AD, kterou jste vytvořili v předchozí části, například: `hdiadmin@contoso.onmicrosoft.com`. Tento uživatel domény, bude správce tohoto clusteru HDInsight.
    - **Heslo domény**: heslo účtu služby.
    - **Organizační jednotka**: rozlišující název organizační jednotky, kterou chcete použít s clusterem HDInsight, třeba: `OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com`. Pokud této organizační jednotky buď neexistuje, pokusí se HDInsight cluster vytvořit organizační jednotku, pomocí oprávnění účtu služby.
    - **LDAPS URL**: například `ldaps://contoso.onmicrosoft.com:636`.
    - **Přístupová skupina uživatelů**: skupiny zabezpečení uživatelů chcete synchronizovat do clusteru, například: `HiveUsers`. Pokud chcete zadat víc skupin uživatelů, oddělte je středníkem (;). Tyto skupiny uplatněna musí existovat v adresáři před vytvořením clusteru ESP.

Další informace najdete v následujících článcích:

- [Úvod do zabezpečení Hadoop s clustery HDInsight připojené k doméně](../domain-joined/apache-domain-joined-introduction.md)
- [Plánování Azure clusterů Hadoop připojených k doméně v HDInsight](../domain-joined/apache-domain-joined-architecture.md)
- [Konfigurace clusteru HDInsight připojené k doméně pomocí Azure Active Directory Domain Services](../domain-joined/apache-domain-joined-configure-using-azure-adds.md)
- [Synchronizace uživatelů Azure Active Directory pro HDInsight cluster](../hdinsight-sync-aad-users-to-cluster.md)
- [Konfigurace zásad Hivu ve HDInsight připojených k doméně](../domain-joined/apache-domain-joined-run-hive.md)
- [Spustit Apache Oozie v doméně HDInsight Hadoop clusterů](../domain-joined/hdinsight-use-oozie-domain-joined-clusters.md)

## <a name="implement-end-to-end-enterprise-security-management"></a>Implementace správy komplexní podnikové zabezpečení

Konec koncové podnikového zabezpečení lze dosáhnout pomocí následujících ovládacích prvků:

- **Soukromým a chráněným datovým kanálem (úrovně zabezpečení perimetru)**
    - Hraniční úroveň zabezpečení je možné zajistit pomocí Azure Virtual Network, skupiny zabezpečení sítě a služby brány

- **Ověřování a autorizaci pro přístup k datům**
    - Vytvoření clusteru HDInsight připojené k doméně pomocí Azure Active Directory Domain Services. (Enterprise Security Package)
    - Ambari použijte k poskytnutí přístupu na základě rolí k prostředkům clusteru pro uživatele AD
    - Pomocí Apache Ranger nastavil zásady řízení přístupu pro Hive na tabulce / sloupce / úrovně řádků.
    - Přístup přes SSH ke clusteru může být omezena pouze na správce.

- **Auditování**
    - Zobrazení a sestavy o všech přístupech k prostředkům clusteru HDInsight a data.
    - Zobrazení a sestavy o všech změnách v zásadách řízení přístupu

- **Šifrování**
    - Transparentní šifrování na straně serveru pomocí klíčů spravovaných microsoftem nebo klíče spravované zákazníkem.
    - Při šifrování přenosu pomocí šifrování na straně klienta, https a TLS

Další informace najdete v následujících článcích:

- [Přehled služby Azure Virtual Networks](../../virtual-network/virtual-networks-overview.md)
- [Přehled skupin zabezpečení sítě Azure](../../virtual-network/security-overview.md)
- [Partnerský vztah Azure Virtual Network](../../virtual-network/virtual-network-peering-overview.md)
- [Průvodci zabezpečením Azure storage](../../storage/common/storage-security-guide.md)
- [Šifrování služby Azure Storage v klidovém stavu](../../storage/common/storage-service-encryption.md)

## <a name="use-monitoring--alerting"></a>Použití monitoring a alerting

Další informace najdete v článku:

[Přehled služby Azure Monitor](../../azure-monitor/overview.md)

## <a name="upgrade-clusters"></a>Upgradovat clustery

Pravidelně upgradujte na nejnovější verzi HDInsight, abyste mohli využívat nejnovější funkce. Následující postup můžete použít k upgradu clusteru na nejnovější verzi:

1. Vytvořte nový cluster HDInsight testu pomocí nejnovější dostupné verze HDInsight.
1. Testování na novém clusteru, abyste měli jistotu, že úlohy a úlohy fungovat podle očekávání.
1. Upravte úlohy nebo aplikace nebo procesy podle potřeby.
1. Zazálohujte si všechna přechodné data uložená místně na uzlech clusteru.
1. Odstranění existujícího clusteru.
1. Vytvoření clusteru HDInsight nejnovějších ve stejné podsíti virtuální sítě pomocí stejného úložiště dat a meta výchozí jako předchozí clusteru.
1. Importujte všechny přechodné data, která byla zálohována.
1. Spuštění úlohy nebo pokračovat ve zpracování pomocí nového clusteru.

Další informace najdete v článku: [clusteru HDInsight Upgrade na novou verzi.](../hdinsight-upgrade-cluster.md)

## <a name="patch-cluster-operating-systems"></a>Opravy clusteru operačních systémů

Spravovaná služba, Hadoop HDInsight postará o opravy operačního systému virtuálních počítačů používané clustery HDInsight.

Další informace najdete v článku: [opravy operačního systému pro HDInsight](../hdinsight-os-patching.md)

## <a name="post-migration"></a>Po migraci

1. **Oprava aplikace** – opakované úlohy, procesy a skripty proveďte potřebné změny.
2. **Testovat** – opakované spuštění funkčnosti a výkonu testy.
3. **Optimalizace** – řešit problémy s výkonem na základě výše uvedených výsledků testu a pak testování potvrďte vylepšení výkonu.

## <a name="next-steps"></a>Další postup

- Další informace o [HDInsight 4.0](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-introduction)