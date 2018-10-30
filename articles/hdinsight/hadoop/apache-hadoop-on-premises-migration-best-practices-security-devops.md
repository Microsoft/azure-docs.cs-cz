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
ms.openlocfilehash: 1a1cf731678ef7678b740020a4d61725f9a2b32a
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50221871"
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

- **Soukromým a chráněným datovým kanálem** (úrovně zabezpečení perimetru):
    - Hraniční úroveň zabezpečení je možné zajistit pomocí Azure Virtual Network, skupiny zabezpečení sítě a služby brány

- **Ověřování a autorizaci pro přístup k datům**
    - Vytvořte cluster Hdinsight připojený k doméně pomocí Azure Active Directory Domain Services. (Enterprise Security Package)
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

- Vytvoření nového clusteru HDI testu pomocí nejnovější dostupné verze HDI.
- Testování na novém clusteru, abyste měli jistotu, že úlohy a úlohy fungovat podle očekávání.
- Upravte úlohy nebo aplikace nebo procesy podle potřeby.
- Zazálohujte si všechna přechodné data uložená místně na uzlech clusteru.
- Odstranění existujícího clusteru.
- Vytvoření clusteru HDInsight nejnovějších ve stejné podsíti virtuální sítě pomocí stejného úložiště dat a meta výchozí jako předchozí clusteru.
- Importujte všechny přechodné data, která byla zálohována.
- Spuštění úlohy nebo pokračovat ve zpracování pomocí nového clusteru.

Další informace najdete v článku: [clusteru HDInsight Upgrade na novou verzi.](../hdinsight-upgrade-cluster.md)

## <a name="patch-cluster-operating-systems"></a>Opravy clusteru operačních systémů

Spravovaná služba, Hadoop HDInsight postará o opravy operačního systému virtuálních počítačů používané clustery HDInsight.

Další informace najdete v článku: [opravy operačního systému pro HDInsight](../hdinsight-os-patching.md)

## <a name="post-migration"></a>Po migraci

1. **Oprava aplikací** – využívejte iterativní proveďte potřebné změny pro úlohy, procesy a skripty
2. **Testovat** – opakované spuštění funkčnosti a výkonu testy
3. **Optimalizace** – řešit problémy s výkonem na základě výše uvedených výsledků testu a pak testování potvrďte vylepšení výkonu.

## <a name="appendix-gathering-details-to-prepare-for-a-migration"></a>Dodatek: shromáždění podrobností přípravy na migraci

Tato část obsahuje šablony dotazníky umožňující získat důležité informace o:

- V místním nasazení.
- Podrobnosti o projektu.
- Požadavky služby Azure.

### <a name="on-premises-deployment-questionnaire"></a>Dotazník pro místní nasazení

| **Dotaz** | **Příklad** | **Odpověď** |
|---|---|---|
|**Téma**: **prostředí**|||
|Typ clusteru distribuce|Hortonworks, Cloudera, MapR| |
|Verze clusteru distribuce|HDP 2.6.5 CDH – 5.7|
|Komponenty ekosystému velkých objemů dat|HDFS, Yarn, Hive, LLAP, Impala, Kudu, HBase, Spark, MapReduce, Kafka, Zookeeper, Solr, Sqoop, Oozie, Ranger, střední, Falcon, Zeppelin, R|
|Typy clusterů|Hadoop, Spark, nesrostlé Kafka, Storm, Solr|
|Počet clusterů|4|
|Číslo hlavní uzly|2|
|Počet uzlů pracovního procesu|100|
|Počet hraničních uzlů| 5|
|Celkové místo na disku|100 TB|
|Konfigurace hlavního uzlu|min nebo y, procesoru, disku atd.|
|Konfigurace datové uzly|min nebo y, procesoru, disku atd.|
|Hraniční uzly konfigurace|min nebo y, procesoru, disku atd.|
|HDFS šifrování?|Ano|
|Vysoká dostupnost|HA HDFS, Metastore HA|
|Zotavení po havárii / zálohování|Zálohování clusteru?|  
|Systémy, které jsou závislé na clusteru|SQL Server, Teradata, Power BI, MongoDB|
|Integrace třetích stran|Tableau GridGain Qubole, Informatica, Splunk|
|**Téma**: **zabezpečení**|||
|Zabezpečení perimetru|Brány firewall|
|Cluster ověřování a autorizace|Active Directory, Ambari, Cloudera správce, bez ověřování|
|Řízení přístupu HDFS|  Ruční, ssh uživatelů|
|Hive ověřování a autorizace|SENTRY, LDAP, Kerberos, Ranger AD|
|Auditování|Ambari, Cloudera Navigátor Ranger|
|Monitorování|Grafitová, shromážděná, statsd, Telegraf, InfluxDB|
|Zobrazení výstrah|Kapacitor Prometheus, služby Datadog|
|Doba uchování dat| 3 roky, 5 let.|
|Správce clusteru|Jeden správce, více správců|

### <a name="project-details-questionnaire"></a>Dotazník podrobností projektu

|**Dotaz**|**Příklad**|**Odpověď**|
|---|---|---|
|**Téma**: **úloh a četnost**|||
|Úlohy MapReduce|10 úlohy--dvakrát denně||
|Úlohy Hive|100 úloh – každou hodinu||
|Sparkových úloh služby batch|50 úlohy--každých 15 minut||
|Úlohy Spark Streaming|5 úloh – každé 3 minuty||
|Strukturované streamování úlohy|5 úlohy--každou minutu||
|Úlohy trénování modelů ML|2 úlohy--jednou za týden||
|Programovací jazyky|Python, Scala, Java||
|Skriptování|Prostředí Pythonu||
|**Téma**: **dat**|||
|Zdroje dat|Ploché soubory Json, Kafka, relační databázový systém||
|Orchestrace dat|Pracovní postupy Oozie, vzduchu||
|V paměti pro vyhledávání|Apache ke konferenci Ignite, redis Cache||
|Cíle dat|HDFS, relační databázový systém, Kafka, MPP ||
|**Téma**: **metadat**|||
|Typ databáze Hive|MySQL, Postgres||
|Ne. z metaúložiště Hive|2||
|Ne. tabulek Hive|100||
|Ne. zásad Ranger|20||
|Ne. Oozie pracovních postupů|100||
|**Téma**: **škálování**|||
|Objem dat, včetně replikace|100 TB||
|Denní objem příjmu|50 GB||
|Míry růstu dat|10 % za rok||
|Míra růstu uzlů clusteru|% 5, ročně
|**Téma**: **clusteru využití**|||
|Průměrné využití procesoru % využití|60%||
|Průměrná paměť % využití|75 %||
|Využité místo na disku|75 %||
|Průměrná sítě % využití|25 %
|**Téma**: **zaměstnanců**|||
|Ne. správců|2||
|Ne. vývojářů|10||
|Ne. koncových uživatelů|100||
|Dovednosti|Hadoop, Spark||
|Ne. z dostupných prostředků pro účely migrace|2||
|**Téma**: **omezení**|||
|Aktuální omezení|Latence je vysoká.||
|Aktuální problémy|Problém souběžnosti||

### <a name="azure-requirements-questionnaire"></a>Dotazník požadavky pro Azure

|**Téma**: **infrastruktury** |||
|---|---|---|
|**Dotaz**|**Příklad**|**Odpověď**|
| Preferované oblasti|USA – východ||
|Upřednostňované sítě VNet?|Ano||
|HA / DR potřeby?|Ano||
|Integrace s jinými cloudovými službami?|ADF, služby cosmos DB||
|**Téma**: **přesun dat**  |||
|Předvolby počátečním načtení|DistCp, Data box, ADF, WANDisco||
|Rozdílová data přenosu|DistCp, AzCopy||
|Průběžné přírůstkové datové přenosy|DistCp, Sqoop||
|**Téma**: **Monitoring a Alerting** |||
|Použití Azure Monitoring a Alerting integrace Vs monitorování třetích stran|Použití Azure Monitoring a Alerting||
|**Téma**: **předvolby pro zabezpečení** |||
|Soukromým a chráněným datovým kanálem?|Ano||
|Cluster připojeno k doméně (ESPP)?|     Ano||
|On-Premises synchronizace AD do cloudu?|     Ano||
|Ne. Synchronizace uživatelů AD?|          100||
|OK pro synchronizaci hesel do cloudu?|    Ano||
|Jenom cloudoví uživatelé?|                 Ano||
|Potřeba vícefaktorové ověřování?|                       Ne|| 
|Požadavky na ověření dat?|  Ano||
|Řízení přístupu na základě rolí?|        Ano||
|Auditování potřeba?|                  Ano||
|Šifrování dat v klidovém stavu?|          Ano||
|Šifrování dat při přenosu?|       Ano||
|**Téma**: **předvolby Re – architektura** |||
|Jeden cluster vs specifické typy clusterů|Specifické typy clusterů||
|Společně umísťovat úložiště Vs vzdálené úložiště?|Vzdálené úložiště||
|Protože data uložená vzdáleně menší velikost clusteru?|Menší velikost clusteru||
|Použití více clusterů menší než jednoho velkého clusteru?|Použití několika menších clusterů||
|Používat vzdálený metastore?|Ano||
|Sdílet metaúložiště mezi různými clustery?|Ano||
|Dekonstruovat úlohy?|Nahraďte úloh Hive Sparkových úloh||
|Pro Orchestrace dat pomocí ADF?|Ne||
|HDI vs HDP na IaaS?|HDI||

## <a name="next-steps"></a>Další postup

- Další informace o [HDInsight 4.0](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-introduction)