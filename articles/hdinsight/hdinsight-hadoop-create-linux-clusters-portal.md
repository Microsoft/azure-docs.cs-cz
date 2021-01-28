---
title: Vytváření clusterů Apache Hadoop pomocí webového prohlížeče, Azure HDInsight
description: Naučte se vytvářet Apache Hadoop, Apache HBA, Apache Storm nebo Apache Spark clustery v HDInsight. Použijte webový prohlížeč a Azure Portal.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 08/06/2020
ms.openlocfilehash: c68d342cf21d69fa97ba3d5171ba596662fd845f
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945820"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>Vytváření clusterů se systémem Linux v HDInsight pomocí Azure Portal

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure Portal je webový nástroj pro správu služeb a prostředků hostovaných v cloudu Microsoft Azure. V tomto článku se dozvíte, jak vytvořit clustery Azure HDInsight se systémem Linux pomocí portálu. Další podrobnosti jsou k dispozici v [vytváření clusterů HDInsight](./hdinsight-hadoop-provision-linux-clusters.md).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Azure Portal zpřístupňuje většinu vlastností clusteru. Pomocí šablon Azure Resource Manager můžete skrýt mnoho podrobností. Další informace najdete v tématu [Vytvoření clusterů Apache Hadoop ve službě HDInsight pomocí šablon Správce prostředků](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-clusters"></a>Vytváření clusterů

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. V horní nabídce vyberte **+ vytvořit prostředek**.

    ![Vytvoření nového clusteru v Azure Portal](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-create-resource.png "Vytvoření nového clusteru v Azure Portal")

1. Vyberte **Analytics**  >  **Azure HDInsight** a přejdete na stránku **vytvořit cluster HDInsight** .

## <a name="basics"></a>Základy

![Základy vytváření clusterů HDInsight](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-basics.png "Vytvoření nového clusteru v Azure Portal")

Na kartě **základy** zadejte následující informace:

|Vlastnost |Popis |
|---|---|
|Předplatné|V rozevíracím seznamu vyberte předplatné Azure, které se používá pro cluster.|
|Skupina prostředků|V rozevíracím seznamu vyberte existující skupinu prostředků nebo vyberte **vytvořit novou**.|
|Název clusteru|Zadejte globálně jedinečný název.|
|Region (Oblast)|V rozevíracím seznamu vyberte oblast, ve které se cluster vytvoří.|
|Typ clusteru|Kliknutím na **Vybrat typ clusteru** otevřete seznam. V seznamu vyberte požadovaný typ clusteru. Clustery HDInsight přicházejí v různých typech. Odpovídají zátěži nebo technologii, pro kterou je cluster laděn. Neexistuje žádná podporovaná metoda pro vytvoření clusteru, který kombinuje více typů.|
|Verze|V rozevíracím seznamu vyberte **verzi**. Pokud si nejste jisti, co si můžete vybrat, použijte výchozí verzi. Další informace najdete v článku [Verze clusterů HDInsight](hdinsight-component-versioning.md).|
|Uživatelské jméno přihlášení clusteru|Zadejte uživatelské jméno, výchozí nastavení je **admin**.|
|Heslo přihlášení clusteru|Zadejte heslo.|
|Potvrzení hesla pro přihlášení ke clusteru|Zadejte znovu heslo.|
|Uživatelské jméno Secure Shell (SSH)|Zadejte uživatelské jméno, výchozí hodnota je **sshuser**|
|Použít heslo přihlášení clusteru pro SSH|Pokud chcete stejné heslo SSH jako heslo správce, které jste zadali dříve, zaškrtněte políčko **použít heslo přihlášení clusteru pro SSH** . Pokud ne, zadejte buď **heslo** , nebo **veřejný klíč** k ověření uživatele SSH. Veřejný klíč je přístup, který doporučujeme. Kliknutím na **tlačítko vybrat** v dolní části uložte konfiguraci přihlašovacích údajů.  Další informace najdete v tématu [připojení ke službě HDInsight (Apache Hadoop) pomocí SSH](hdinsight-hadoop-linux-use-ssh-unix.md).|

Vyberte **Další: >>úložiště** pro přechod na další kartu.

## <a name="storage"></a>Storage

> [!WARNING] 
> Od 15. června 2020 nebudou moct zákazníci vytvořit nový instanční objekt pomocí služby HDInsight. Viz téma [Vytvoření instančního objektu a certifikátů](../active-directory/develop/howto-create-service-principal-portal.md) pomocí Azure Active Directory.

![Vytvoření úložiště clusteru HDInsight](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-storage.png "Vytvoření nového clusteru v Azure Portal – úložiště")

### <a name="primary-storage"></a>Primární úložiště

Z rozevíracího seznamu pro **typ primárního úložiště** vyberte výchozí typ úložiště. Pozdější pole, která se mají dokončit, se budou lišit v závislosti na vašem výběru. Pro **Azure Storage**:

1. Pro **metodu výběru** vyberte buď **možnost vybrat ze seznamu**, nebo **použijte přístupový klíč**.
    * V **seznamu vybrat ze** vyberte svůj **primární účet úložiště** z rozevíracího seznamu nebo vyberte **vytvořit novou**.
    * V případě **použití přístupového klíče** zadejte **název svého účtu úložiště**. Pak zadejte **přístupový klíč**.

1. Pro **kontejner** přijměte výchozí hodnotu nebo zadejte novou.

### <a name="additional-azure-storage"></a>Další Azure Storage

Volitelné: vyberte **přidat Azure Storage** pro další úložiště clusteru. Použití dalšího účtu úložiště v jiné oblasti, než je cluster HDInsight, se nepodporuje.

### <a name="metastore-settings"></a>Nastavení metastore

Volitelné: Zadejte existující SQL Database pro ukládání Apache Hive, Apache Oozie a a Apache Ambari metadata mimo cluster. Azure SQL Database, který se používá pro metastore, musí umožňovat připojení k ostatním službám Azure, včetně Azure HDInsight. Při vytváření metastore Nejmenujte databázi s pomlčkami nebo pomlčkami. Tyto znaky můžou způsobit selhání procesu vytváření clusteru.

> [!IMPORTANT]
> Pro obrazce clusterů, které podporují metaúložiště, výchozí metastore poskytuje Azure SQL Database s **limitem DTU úrovně Basic 5 (není aktualizovatelný)**. Vhodné pro účely základního testování. U rozsáhlých nebo produkčních úloh doporučujeme migrovat na externí metastore.

Vyberte **Další: zabezpečení + síťové >>** , abyste mohli přejít na další kartu.

## <a name="security--networking"></a>Zabezpečení a sítě

![Vytvoření sítě zabezpečení clusteru HDInsight](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-security-networking.png "Vytvoření sítě zabezpečení clusteru HDInsight")

Na kartě **zabezpečení + sítě** zadejte následující informace:

|Vlastnost |Popis |
|---|---|
|Balíček podnikového zabezpečení|Volitelné: zaškrtněte políčko pro použití **balíček zabezpečení podniku**. Další informace najdete v tématu [Konfigurace clusteru HDInsight s balíček zabezpečení podniku pomocí Azure Active Directory Domain Services](./domain-joined/apache-domain-joined-configure-using-azure-adds.md).|
|TLS|Volitelné: v rozevíracím seznamu vyberte verzi TLS. Další informace najdete v tématu [zabezpečení transportní vrstvy](./transport-layer-security.md).|
|Virtuální síť|Volitelné: v rozevíracím seznamu vyberte existující virtuální síť a podsíť. Informace najdete v tématu [Plánování nasazení virtuální sítě pro clustery Azure HDInsight](hdinsight-plan-virtual-network-deployment.md). Článek zahrnuje konkrétní požadavky na konfiguraci pro virtuální síť.|
|Nastavení šifrování disku|Volitelné: Pokud chcete použít šifrování, zaškrtněte políčko. Další informace najdete v tématu [šifrování klíčového disku spravovaného zákazníkem](./disk-encryption.md).|
|Proxy REST Kafka|Toto nastavení je k dispozici pouze pro typ clusteru Kafka. Další informace najdete v tématu [použití proxy REST](./kafka/rest-proxy.md).|
|Identita|Volitelné: v rozevíracím seznamu vyberte existující identitu služby přiřazenou uživatelem. Další informace najdete v tématu [spravované identity ve službě Azure HDInsight](./hdinsight-managed-identities.md).|

Vyberte **Další: Konfigurace + cenové >>** pro přechod na další kartu.

## <a name="configuration--pricing"></a>Konfigurace a ceny

![Vytvoření konfigurace clusteru HDInsight](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-configuration.png "Karta konfigurace a ceny")

Na kartě **Konfigurace + ceny** zadejte následující informace:

|Vlastnost |Popis |
|---|---|
|+ Přidat aplikaci|Volitelné: Vyberte libovolné aplikace, které chcete. Společnost Microsoft, nezávislé výrobce softwaru (ISV) nebo tyto aplikace můžete vyvíjet. Další informace najdete v tématu [instalace aplikací během vytváření clusteru](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).|
|Velikost uzlu|Volitelné: vyberte uzel jiné velikosti.|
|Počet uzlů|Volitelné: zadejte počet uzlů pro zadaný typ uzlu. Pokud plánujete více než 32 pracovních uzlů, vyberte velikost hlavního uzlu s alespoň osmi jádry a 14 GB paměti RAM. Naplánujte uzly buď při vytváření clusteru, nebo pomocí škálování clusteru po jeho vytvoření.|
|Povolit automatické škálování|Volitelné: Pokud chcete funkci povolit, zaškrtněte políčko. Další informace najdete v tématu [Automatické škálování clusterů Azure HDInsight](./hdinsight-autoscale-clusters.md).|
|+ Přidat akci skriptu|Volitelné: Tato možnost funguje, pokud chcete použít vlastní skript k přizpůsobení clusteru, protože se vytváří cluster. Další informace o akcích skriptu najdete v tématu [Přizpůsobení clusterů HDInsight se systémem Linux pomocí akcí skriptů](hdinsight-hadoop-customize-cluster-linux.md).|

Vyberte **zkontrolovat + vytvořit >>** a ověřte konfiguraci clusteru a přejděte na finální kartu.

## <a name="review--create"></a>Kontrola a vytvoření

![Shrnutí clusteru pro vytvoření HDInsight](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-review-create-hadoop.png "Zadat počet uzlů clusteru")

Zkontrolujte nastavení. Vyberte **Vytvořit**. Cluster se vytvoří.

Vytvoření clusteru nějakou dobu trvá, obvykle přibližně 20 minut. Sledujte **oznámení** pro kontrolu procesu zřizování.

## <a name="post-creation"></a>Vytvoření příspěvku

Po dokončení procesu vytváření vyberte **Přejít k prostředku** z oznámení o **úspěšném nasazení** . V okně clusteru jsou uvedeny následující informace.

![HDI Azure Portal clusteru – Přehled](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "Vlastnosti clusteru")

Některé ikony v okně jsou vysvětleny takto:

|Vlastnost | Popis |
|---|---|
|Přehled|Poskytuje všechny základní informace o clusteru. Příklady jsou název, skupina prostředků, do které patří, umístění, operační systém a adresa URL řídicího panelu clusteru.|
|Řídicí panely clusteru|Vás směruje na portál Ambari přidružený ke clusteru.|
|SSH + přihlášení k clusteru|Poskytuje informace potřebné pro přístup ke clusteru pomocí SSH.|
|Odstranit|Odstraní cluster HDInsight.|

## <a name="delete-the-cluster"></a>Odstranění clusteru

Další informace najdete v tématu [odstranění clusteru HDInsight pomocí prohlížeče, PowerShellu nebo rozhraní příkazového řádku Azure](./hdinsight-delete-cluster.md).

## <a name="troubleshoot"></a>Řešení potíží

Pokud narazíte na problémy s vytvářením clusterů HDInsight, podívejte se na [požadavky na řízení přístupu](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Další kroky

Úspěšně jste vytvořili cluster HDInsight. Nyní se naučíte pracovat s clusterem.

* [Použití Apache Hive se službou HDInsight](hadoop/hdinsight-use-hive.md)
* [Začínáme s Apache HBA v HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Přizpůsobení clusterů HDInsight se systémem Linux pomocí akcí skriptů](hdinsight-hadoop-customize-cluster-linux.md)