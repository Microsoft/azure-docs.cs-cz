---
title: Vytvářejte clustery Apache Hadoop pomocí webového prohlížeče Azure HDInsight
description: Naučte se vytvářet clustery Apache Hadoop, Apache HBase, Apache Storm nebo Apache Spark na HDInsightu. Použijte webový prohlížeč a portál Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/13/2020
ms.openlocfilehash: 88392e4e61c28d4fe821c8176242246d10e52b23
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770900"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>Vytváření clusterů založených na Linuxu ve službě HDInsight pomocí portálu Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Portál Azure je webový nástroj pro správu služeb a prostředků hostovaných v cloudu Microsoft Azure. V tomto článku se dozvíte, jak vytvořit clustery Azure HDInsight založené na Linuxu pomocí portálu. Další podrobnosti jsou k dispozici [v clusterech Create HDInsight](./hdinsight-hadoop-provision-linux-clusters.md).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Portál Azure zveřejňuje většinu vlastností clusteru. Pomocí šablon Azure Resource Manager můžete skrýt mnoho podrobností. Další informace najdete [v tématu Vytváření clusterů Apache Hadoop v HDInsight pomocí šablon Správce prostředků](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="create-clusters"></a>Vytváření clusterů

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. V horní nabídce vyberte **+ Vytvořit prostředek**.

    ![Vytvoření nového clusteru na webu Azure Portal](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-create-resource.png "Vytvoření nového clusteru na webu Azure Portal")

1. Vyberte **Analytics** > **Azure HDInsight** a přejděte na stránku **vytvořit cluster HDInsight.**

## <a name="basics"></a>Základy

![HDInsight vytváří základy clusteru](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-basics.png "Vytvoření nového clusteru na webu Azure Portal")

Na kartě **Základy** zadejte následující informace:

|Vlastnost |Popis |
|---|---|
|Předplatné|V rozevíracím seznamu vyberte předplatné Azure, které se používá pro cluster.|
|Skupina prostředků|V rozevíracím seznamu vyberte existující skupinu prostředků nebo vyberte **Vytvořit nový**.|
|Název clusteru|Zadejte globálně jedinečný název.|
|Region (Oblast)|V rozevíracím seznamu vyberte oblast, ve které je cluster vytvořen.|
|Typ clusteru|Kliknutím na **Vybrat typ clusteru** otevřete seznam. Ze seznamu vyberte hledaný typ clusteru. Clustery HDInsight se domýšlet v různých typech. Odpovídají zatížení nebo technologii, pro kterou je cluster naladěn. Neexistuje žádná podporovaná metoda k vytvoření clusteru, který kombinuje více typů.|
|Version|V rozevíracím seznamu vyberte **verzi**. Pokud nevíte, co si vybrat, použijte výchozí verzi. Další informace najdete v článku [Verze clusterů HDInsight](hdinsight-component-versioning.md).|
|Uživatelské jméno přihlášení clusteru|Zadejte uživatelské jméno, výchozí je **admin**.|
|Heslo přihlášení clusteru|Zadejte heslo.|
|Potvrzení přihlašovacího hesla clusteru|Znovu zadejte heslo|
|Uživatelské jméno Secure Shell (SSH)|Zadejte uživatelské jméno, výchozí je **sshuser**|
|Použití přihlašovacího hesla clusteru pro SSH|Pokud chcete stejné heslo SSH jako heslo správce, které jste zadali dříve, zaškrtněte políčko **Použít přihlašovací heslo clusteru pro SSH.** Pokud ne, zadejte **heslo** nebo **veřejný klíč** k ověření uživatele SSH. Veřejný klíč je přístup, který doporučujeme. Zvolte **Vybrat** dole, chcete-li uložit konfiguraci pověření.  Další informace naleznete [v tématu Připojení k HDInsight (Apache Hadoop) pomocí SSH](hdinsight-hadoop-linux-use-ssh-unix.md).|

Vyberte **Další: Úložiště >>,** abyste postoupili na další kartu.

## <a name="storage"></a>Storage

![HDInsight vytvořit úložiště clusteru](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-storage.png "Vytvoření nového clusteru na portálu Azure – úložiště")

### <a name="primary-storage"></a>Primární úložiště

V rozevíracím seznamu pro **typ primárního úložiště**vyberte výchozí typ úložiště. Následující pole, která chcete vyplnit, se budou lišit v závislosti na vašem výběru. Pro **úložiště Azure:**

1. V **případě metody Výběr**zvolte možnost Vybrat ze **seznamu**nebo **Použít přístupový klíč**.
    * V **části Vybrat ze seznamu**vyberte účet **primárního úložiště** v rozevíracím seznamu nebo vyberte Vytvořit **nový**.
    * V **položte možnost Použít přístupový klíč**a zadejte **název účtu úložiště**. Poté zadejte **přístupový klíč**.

1. V **části Kontejner**přijměte výchozí hodnotu nebo zadejte novou.

### <a name="additional-azure-storage"></a>Další úložiště Azure

Volitelné: Vyberte **Přidat Azure Storage** pro další úložiště clusteru. Použití dalšího účtu úložiště v jiné oblasti, než je cluster HDInsight, není podporováno.

### <a name="metastore-settings"></a>Nastavení metaúložiště

Volitelné: Zadejte existující databázi SQL pro uložení metadat Apache Hive, Apache Oozie a Apache Ambari mimo cluster. Azure SQL Database, která se používá pro metastore musí umožňovat připojení k jiným službám Azure, včetně Azure HDInsight. Při vytváření metaúložiště nepojmenovávejte databázi pomlčkami nebo spojovníky. Tyto znaky mohou způsobit selhání procesu vytváření clusteru.

Vyberte **Další: Zabezpečení + síťové >>** pro postup na další kartu.

## <a name="security--networking"></a>Bezpečnost + vytváření sítí

![HDInsight vytvořit síť zabezpečení clusteru](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-security-networking.png "HDInsight vytvořit síť zabezpečení clusteru")

Na kartě **Zabezpečení + sítě** zadejte následující informace:

|Vlastnost |Popis |
|---|---|
|Balíček zabezpečení organizace|Volitelné: Zaškrtnutím políčka použijete **balíček Enterprise Security Package**. Další informace naleznete [v tématu Konfigurace clusteru HDInsight s balíčkem podnikového zabezpečení pomocí služby Azure Active Directory Domain Services](./domain-joined/apache-domain-joined-configure-using-azure-adds.md).|
|TLS|Volitelné: V rozevíracím seznamu vyberte verzi TLS. Další informace naleznete v tématu [Transport Layer Security](./transport-layer-security.md).|
|Virtuální síť|Volitelné: V rozevíracím seznamu vyberte existující virtuální síť a podsíť. Další informace najdete [v tématu Plánování nasazení virtuální sítě pro clustery Azure HDInsight](hdinsight-plan-virtual-network-deployment.md). Článek obsahuje specifické požadavky na konfiguraci pro virtuální síť.|
|Nastavení šifrování disku|Volitelné: Zaškrtněte políčko, chcete-li použít šifrování. Další informace naleznete v [tématu Customer-managed key disk encryption](./disk-encryption.md).|
|Proxy REST Kafka|Toto nastavení je k dispozici pouze pro cluster typu Kafka. Další informace naleznete [v tématu Použití serveru REST](./kafka/rest-proxy.md).|
|Identita|Volitelné: V rozevíracím seznamu vyberte existující identitu služby přiřazenou uživateli. Další informace najdete [v tématu Spravované identity v Azure HDInsight](./hdinsight-managed-identities.md).|

Vyberte **Další: Konfigurace + >>ceny,** abyste postoupili na další kartu.

## <a name="configuration--pricing"></a>Konfigurace + ceny

![HDInsight vytvořit konfiguraci clusteru](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-configuration.png "Karta Konfigurace a ceny")

Na kartě **Konfigurace + ceny** zadejte následující informace:

|Vlastnost |Popis |
|---|---|
|+ Přidat aplikaci|Volitelné: Vyberte všechny aplikace, které chcete. společnosti Microsoft, nezávislých dodavatelů softwaru (ISV), nebo můžete tyto aplikace vyvíjet. Další informace naleznete v [tématu Instalace aplikací během vytváření clusteru](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).|
|Velikost uzlu|Volitelné: Vyberte uzel jiné velikosti.|
|Počet uzlů|Volitelné: Zadejte počet uzlů pro zadaný typ uzlu. Pokud plánujete více než 32 pracovních uzlů, vyberte velikost hlavního uzlu s alespoň osmi jádry a 14 GB paměti RAM. Naplánujte uzly buď při vytváření clusteru, nebo změnou velikosti clusteru po vytvoření.|
|Povolení automatického škálování|Volitelné: Zaškrtnutím políčka povolíte funkci. Další informace najdete [v tématu Automatické škálování clusterů Azure HDInsight](./hdinsight-autoscale-clusters.md).|
|+ Přidat akci skriptu|Volitelné: Tato možnost funguje, pokud chcete k přizpůsobení clusteru použít vlastní skript při vytváření clusteru. Další informace o akcích skriptů najdete [v tématu Přizpůsobení clusterů HDInsight založených na Linuxu pomocí akcí skriptů](hdinsight-hadoop-customize-cluster-linux.md).|

Vyberte **Zkontrolovat + vytvořit >>** k ověření konfigurace clusteru a přejděte na poslední kartu.

## <a name="review--create"></a>Recenze + vytvořit

![HDInsight vytvořit souhrn clusteru](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-review-create-hadoop.png "Určení počtu uzlů clusteru")

Zkontrolujte nastavení. Vyberte **Vytvořit**. Cluster se vytvoří.

Vytvoření clusteru nějakou dobu trvá, obvykle přibližně 20 minut. Sledování **oznámení** ke kontrole procesu zřizování.

## <a name="post-creation"></a>Vytvoření příspěvku

Po dokončení procesu vytváření vyberte **přejít na prostředek** z oznámení **o úspěšném nasazení.** Okno clusteru poskytuje následující informace.

![Přehled portálového clusteru HDI Azure](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "Vlastnosti clusteru")

Některé ikony v okně jsou vysvětleny takto:

|Vlastnost | Popis |
|---|---|
|Přehled|Poskytuje všechny základní informace o clusteru. Příklady jsou název, skupina prostředků, do které patří, umístění, operační systém a adresa URL řídicího panelu clusteru.|
|Řídicí panely clusteru|Přesměruje vás na portál Ambari přidružený ke clusteru.|
|SSH + Přihlášení do clusteru|Obsahuje informace potřebné pro přístup ke clusteru pomocí SSH.|
|Odstranit|Odstraní cluster HDInsight.|

## <a name="customize-clusters"></a>Přizpůsobení clusterů

* [Přizpůsobení clusterů HDInsight pomocí Bootstrapu](hdinsight-hadoop-customize-cluster-bootstrap.md)
* [Přizpůsobení clusterů HDInsight založených na Linuxu pomocí akcí skriptů](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="delete-the-cluster"></a>Odstranění clusteru

Viz [Odstranění clusteru HDInsight pomocí prohlížeče, PowerShellu nebo rozhraní příkazového příkazového příkazu Konto Azure](./hdinsight-delete-cluster.md).

## <a name="troubleshoot"></a>Řešení potíží

Pokud narazíte na problémy s vytvářením clusterů HDInsight, podívejte se na [požadavky na řízení přístupu](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Další kroky

Úspěšně jste vytvořili cluster HDInsight. Teď se dozvíte, jak pracovat s clusterem.

* [Použití Apache Hive s HDInsight](hadoop/hdinsight-use-hive.md)
* [Začínáme s Apache HBase na HDInsightu](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Vývoj java topologie pro Apache Storm na HDInsight](storm/apache-storm-develop-java-topology.md)
* [Vytvoření samostatné aplikace pomocí scaly](spark/apache-spark-create-standalone-application.md)
