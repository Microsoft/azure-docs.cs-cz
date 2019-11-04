---
title: 'Zabezpečení: migrace místních Apache Hadoop do Azure HDInsight'
description: Seznamte se s osvědčenými postupy zabezpečení a DevOps pro migraci místních clusterů Hadoop do Azure HDInsight.
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: 1cebe425e323eefda6e26b0f32ddeda0118a70d1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494978"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---security-and-devops-best-practices"></a>Migrace místních Apache Hadoop clusterů do Azure HDInsight – osvědčené postupy zabezpečení a DevOps

Tento článek obsahuje doporučení pro zabezpečení a DevOps v systémech Azure HDInsight. Je součástí série, která poskytuje osvědčené postupy, které vám pomůžou s migrací místních Apache Hadoop systémů do Azure HDInsight.

## <a name="secure-and-govern-cluster-with-enterprise-security-package"></a>Zabezpečený a řídící cluster pomocí Balíček zabezpečení podniku

Balíček zabezpečení podniku (ESP) podporuje ověřování založené na službě Active Directory, podporu více uživatelů a řízení přístupu na základě rolí. Když jste zvolili možnost ESP, cluster HDInsight se připojí k doméně služby Active Directory a správce podniku může nakonfigurovat řízení přístupu na základě role (RBAC) pro Apache Hive zabezpečení pomocí Apache Ranger. Správce může také Auditovat přístup k datům podle zaměstnanců a jakékoli změny provedené v zásadách řízení přístupu.

Protokol ESP je k dispozici na následujících typech clusterů: Apache Hadoop, Apache Spark, Apache Hbas, Apache Kafka a interaktivní dotaz (LLAP podregistru). 

K nasazení clusteru HDInsight připojeného k doméně použijte následující postup:

- Nasaďte Azure Active Directory (AAD) předáním názvu domény.
- Nasazení Azure Active Directory Domain Services (AAD DS).
- Vytvořte požadované Virtual Network a podsíť.
- Nasazení virtuálního počítače v Virtual Network pro správu AAD DS.
- Připojte virtuální počítač k doméně.
- Nainstalujte nástroje AD a DNS.
- Správce AAD DS vytvoří organizační jednotku (OU).
- Povolte LDAPs pro AAD DS.
- V Azure Active Directory vytvořte účet služby s delegovaným čtením & oprávnění zapisovat správce k organizační jednotce, aby mohla. Tento účet služby pak může připojit počítače k doméně a umístit objekty zabezpečení počítače v rámci organizační jednotky. Může také vytvořit instanční objekty v rámci organizační jednotky, kterou zadáte při vytváření clusteru.


    > [!Note]
    > Účet služby nemusí být účtem správce domény služby AD.


- Nasaďte cluster HDInsight ESP nastavením následujících parametrů:
    - **Název domény**: název domény, který je přidružený k Azure služba AD DS.
    - **Doména uživatelské jméno**: účet služby v doméně spravované službou Azure služba AD DS řadiče domény, kterou jste vytvořili v předchozí části, například: `hdiadmin@contoso.onmicrosoft.com`. Tento uživatel domény bude správcem tohoto clusteru HDInsight.
    - **Heslo domény**: heslo účtu služby.
    - **Organizační jednotka**: rozlišující název organizační jednotky, kterou chcete používat s clusterem HDInsight, například: `OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com`. Pokud tato organizační jednotka neexistuje, pokusí se cluster HDInsight vytvořit organizační jednotku s použitím oprávnění účtu služby.
    - **Adresa URL LDAPS**: například `ldaps://contoso.onmicrosoft.com:636`.
    - **Přístup ke skupině uživatelů**: skupiny zabezpečení, jejichž uživatelé chcete synchronizovat s clusterem, například: `HiveUsers`. Pokud chcete zadat více skupin uživatelů, oddělte je středníkem ";". Aby bylo možné cluster ESP vytvořit, musí se skupiny v adresáři vyskytovat.

Další informace najdete v následujících článcích:

- [Úvod do zabezpečení Apache Hadoop s využitím clusterů HDInsight připojených k doméně](../domain-joined/hdinsight-security-overview.md)

- [Plánování clusterů Apache Hadoop připojených k doméně Azure v HDInsight](../domain-joined/apache-domain-joined-architecture.md)
- [Konfigurace clusteru HDInsight připojeného k doméně pomocí Azure Active Directory Domain Services](../domain-joined/apache-domain-joined-configure-using-azure-adds.md)
- [Synchronizace Azure Active Directory uživatelů s clusterem HDInsight](../hdinsight-sync-aad-users-to-cluster.md)
- [Konfigurace zásad Apache Hive v HDInsight připojené k doméně](../domain-joined/apache-domain-joined-run-hive.md)
- [Spuštění Apache Oozie v clusterech HDInsight Hadoop připojených k doméně](../domain-joined/hdinsight-use-oozie-domain-joined-clusters.md)

## <a name="implement-end-to-end-enterprise-security"></a>Implementace koncového podnikového zabezpečení

Koncová podniková zabezpečení je možné dosáhnout pomocí následujících ovládacích prvků:

- **Privátní a chráněný datový kanál (zabezpečení na úrovni hraničních)**
    - Zabezpečení na úrovni hraničního okruhu lze dosáhnout pomocí virtuálních sítí Azure, skupin zabezpečení sítě a služby brány.

- **Ověřování a autorizace pro přístup k datům**
    - Vytvořte cluster HDInsight připojený k doméně pomocí Azure Active Directory Domain Services. (Balíček zabezpečení podniku).
    - Pomocí Ambari můžete zajistit přístup k prostředkům clusteru pro uživatele služby AD na základě rolí.
    - Použijte Apache Ranger k nastavení zásad řízení přístupu pro podregistr na úrovni tabulky nebo sloupce nebo řádku.
    - Přístup SSH ke clusteru lze omezit pouze na správce.

- **Auditování**
    - Zobrazit a ohlásit veškerý přístup k prostředkům a datům clusteru HDInsight.
    - Zobrazit a ohlásit všechny změny v zásadách řízení přístupu.

- **Šifrování**
    - Transparentní šifrování na straně serveru pomocí klíčů spravovaných Microsoftem nebo klíčů spravovaných zákazníkem.
    - Při přenosu šifrování pomocí šifrování na straně klienta, https a TLS.

Další informace najdete v následujících článcích:

- [Přehled služby Azure Virtual Networks](../../virtual-network/virtual-networks-overview.md)
- [Přehled skupin zabezpečení sítě Azure](../../virtual-network/security-overview.md)
- [Partnerský vztah Azure Virtual Network](../../virtual-network/virtual-network-peering-overview.md)
- [Průvodce zabezpečením Azure Storage](../../storage/common/storage-security-guide.md)
- [Šifrování služby Azure Storage v klidovém provozu](../../storage/common/storage-service-encryption.md)

## <a name="use-monitoring--alerting"></a>Použití monitorování & upozorňování

Další informace najdete v článku:

[Přehled služby Azure Monitor](../../azure-monitor/overview.md)

## <a name="upgrade-clusters"></a>Upgradovat clustery

Pravidelně inovujte na nejnovější verzi HDInsight, abyste mohli využívat nejnovější funkce. Pomocí následujících kroků můžete upgradovat cluster na nejnovější verzi:

1. Vytvořte nový testovací cluster HDInsight pomocí nejnovější dostupné verze HDInsight.
1. Otestujte nový cluster a ujistěte se, že úlohy a úlohy fungují podle očekávání.
1. Podle potřeby upravte úlohy nebo aplikace nebo úlohy.
1. Zálohujte všechna přechodná data uložená místně na uzlech clusteru.
1. Odstraňte existující cluster.
1. Vytvořte cluster nejnovější verze HDInsight ve stejné podsíti virtuální sítě s použitím stejných výchozích dat a meta úložiště jako v předchozím clusteru.
1. Importujte všechna přechodná data, která byla zálohována.
1. Spustí úlohy/pokračovat ve zpracování pomocí nového clusteru.

Další informace naleznete v článku: [upgrade clusteru HDInsight na novou verzi](../hdinsight-upgrade-cluster.md).

## <a name="patch-cluster-operating-systems"></a>Oprava operačních systémů clusteru

V rámci spravované služby Hadoop se HDInsight postará o opravu operačního systému virtuálních počítačů používaných clustery HDInsight.

Další informace naleznete v článku: [opravy operačního systému pro HDInsight](../hdinsight-os-patching.md).

## <a name="post-migration"></a>Po migraci

1. **Opravit aplikace** – iterativní provedení potřebných změn v úlohách, procesech a skriptech.
2. **Provádět testy** – opakované spuštění testů funkčního a funkčního výkonu.
3. **Optimalizujte** problémy s výkonem na základě výše uvedených výsledků testů a pak proveďte znovu test, abyste potvrdili zvýšení výkonu.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [HDInsight 4,0](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-introduction).
