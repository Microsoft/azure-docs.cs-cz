---
title: Nakonfigurovat zásady Apache Kafka v HDInsight s balíčkem Enterprise Security Package - Azure
description: Zjistěte, jak nakonfigurovat zásady Apache Ranger pro systém Kafka v Azure HDInsight s balíčkem zabezpečení podniku.
services: hdinsight
ms.service: hdinsight
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: tutorial
ms.date: 01/14/2019
ms.openlocfilehash: 381958b462b668447a6548029a1be616df7a3a4b
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55750218"
---
# <a name="tutorial-configure-apache-kafka-policies-in-hdinsight-with-enterprise-security-package-preview"></a>Kurz: Nakonfigurovat zásady Apache Kafka v HDInsight s balíčkem Enterprise Security Package (Preview)

Zjistěte, jak nakonfigurovat zásady Apache Rangeru pro clustery Apache Kafka Enterprise Security Package (ESP). ESP clustery jsou připojené k doméně, což uživatelům umožňuje ověření pomocí přihlašovacích údajů do domény. V tomto kurzu vytvoříte dvě zásady Ranger pro omezení přístupu k tématům `sales*` a `marketingspend`.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření uživatelů domén
> * Vytvoření zásad Ranger
> * Vytvoření témat v clusteru Kafka
> * Testování zásad Ranger

## <a name="before-you-begin"></a>Před zahájením

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).

* Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

* Vytvořte [cluster HDInsight Kafka s balíčkem zabezpečení podniku](apache-domain-joined-configure-using-azure-adds.md).

## <a name="connect-to-apache-ranger-admin-ui"></a>Připojení k uživatelskému rozhraní správce Apache Ranger

1. Z prohlížeče se připojte k uživatelskému rozhraní Ranger Admin pomocí adresy URL `https://<ClusterName>.azurehdinsight.net/Ranger/`. Nezapomeňte změnit `<ClusterName>` na název vašeho clusteru Kafka.

    > [!NOTE]  
    > Přihlašovací údaje k Rangeru nejsou stejné jako údaje ke clusteru Hadoop. Abyste zabránili prohlížeči v použití přihlašovacích údajů clusteru Hadoop uložených v mezipaměti, použijte pro připojení k uživatelskému rozhraní správce Ranger nové okno prohlížeče v režimu InPrivate.

2. Přihlaste se pomocí svých přihlašovacích údajů správce k Azure Active Directory (AD). Přihlašovací údaje správce k Azure AD nejsou stejné jako přihlašovací údaje ke clusteru HDInsight nebo přihlašovací údaje SSH k uzlu HDInsight Linux.

   ![Uživatelské rozhraní správce Apache Ranger](./media/apache-domain-joined-run-kafka/apache-ranger-admin-login.png)

## <a name="create-domain-users"></a>Vytvoření uživatelů domén

V tématu [vytvoření clusteru HDInsight s balíčkem zabezpečení podniku](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds) se naučíte vytvořit uživatele domény **sales_user** a **marketing_user**. V produkční scénáři uživatelé domény pocházejí z vašeho klienta služby Active Directory.

## <a name="create-ranger-policy"></a>Vytvoření zásady Ranger

Vytvoření zásady Ranger pro uživatele **sales_user** a **marketing_user**.

1. Otevřete **Uživatelské rozhraní správce Ranger**.

2. Klikněte na tlačítko **\<ClusterName>_kafka** pod **Kafka**. Může být uvedena jedna předem nakonfigurovaná zásada.

3. Klikněte na **Add New Policy** (Přidat novou zásadu) a pak zadejte následující hodnoty:

   |**Nastavení**  |**Navrhovaná hodnota**  |
   |---------|---------|
   |Název zásady  |  hdi sales* policy   |
   |Téma   |  sales* |
   |Vybrat uživatele  |  sales_user1 |
   |Oprávnění  | publish, consume, create (publikovat, využívat, vytvářet) |

   Následující zástupné znaky mohou být součástí názvu tématu:

   * * zastupuje nula nebo více výskytů libovolných znaků.
   * ? zastupuje jeden libovolný znak.

   ![Zásada vytvoření uživatelského rozhraní správce Apache Ranger](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy.png)   

   >[!NOTE]
   >Pokud uživatel domény v části **Select User** (Vybrat uživatele) není k dispozici, chvíli počkejte, než se Ranger synchronizuje s AAD.

4. Kliknutím na **Přidat** uložte zásadu.

5. Klikněte na **Add New Policy** (Přidat novou zásadu) a pak zadejte následující hodnoty:

   |**Nastavení**  |**Navrhovaná hodnota**  |
   |---------|---------|
   |Název zásady  |  hdi marketing policy   |
   |Téma   |  marketingspend |
   |Vybrat uživatele  |  marketing_user1 |
   |Oprávnění  | publish, consume, create (publikovat, využívat, vytvářet) |

   ![Zásada vytvoření uživatelského rozhraní správce Apache Ranger](./media/apache-domain-joined-run-kafka/apache-ranger-admin-create-policy-2.png)  

6. Kliknutím na **Přidat** uložte zásadu.

## <a name="create-topics-in-a-kafka-cluster-with-esp"></a>Vytvoření témat v clusteru Kafka pomocí ESP

K vytvoření dvou tématech `salesevents` a `marketingspend`:

1. Navažte připojení SSH ke clusteru použitím následujícího příkazu:

   ```bash
   ssh DOMAINADMIN@CLUSTERNAME-ssh.azurehdinsight.net
   ```

   Nahraďte `DOMAINADMIN` s uživatele s rolí správce pro cluster nakonfigurovaná v průběhu [vytvoření clusteru](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds#create-a-hdinsight-cluster-with-esp)a nahraďte `CLUSTERNAME` s názvem vašeho clusteru. Pokud se zobrazí výzva, zadejte heslo pro uživatelský účet správce. Další informace o použití `SSH` se službou HDInsight najdete v tématu [Použití SSH se službou HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix).

2. Pomocí následujících příkazů uložte název clusteru do proměnné a nainstalujte nástroj pro parsování JSON `jq`. Po zobrazení výzvy zadejte název clusteru Kafka.

   ```bash
   sudo apt -y install jq
   read -p 'Enter your Kafka cluster name:' CLUSTERNAME
   ```

3. Chcete-li získat hostiteli zprostředkovatele Kafka použijte následující příkazy. Po zobrazení výzvy zadejte heslo účtu správce clusteru.

   ```bash
   export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
   ```

   > [!Note]  
   > Než budete pokračovat, možná budete muset nastavit své vývojové prostředí, pokud jste to ještě neudělali. Budete potřebovat komponenty, jako jsou sada Java JDK, Apache Maven a klient SSH s scp. Další podrobnosti najdete v tématu [instalační pokyny](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer).
   
1. Stáhněte si [příklady pro producenta a konzumenta Apache Kafka připojené k doméně](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/DomainJoined-Producer-Consumer).

1. Postupujte podle kroků 2 a 3 v části **sestavovat a nasazovat v příkladu** v [kurzu: Použití Apache Kafka Producer and Consumer API](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-producer-consumer-api#build-and-deploy-the-example)

1. Spusťte následující příkazy:

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create salesevents $KAFKABROKERS
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create marketingspend $KAFKABROKERS
   ```

## <a name="test-the-ranger-policies"></a>Testování zásad Ranger

Na základě zásad Ranger nakonfigurovaná, **sales_user** produktu/využívají tématu `salesevents` , ale ne tématu `marketingspend`. Naopak **marketing_user** produktu/využívají tématu `marketingspend` , ale ne tématu `salesevents`.

1. Otevřete nové připojení SSH ke clusteru. Pomocí následujícího příkazu se přihlaste jako uživatel **sales_user1**:

   ```bash
   ssh sales_user1@CLUSTERNAME-ssh.azurehdinsight.net
   ```

2. Spusťte následující příkaz:

   ```bash
   export KAFKA_OPTS="-Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf"
   ```

3. Nastavte následující proměnné prostředí pomocí názvů zprostředkovatele v předchozí části:

   ```bash
   export KAFKABROKERS=<brokerlist>:9092
   ```

   Příklad: `export KAFKABROKERS=wn0-khdicl.contoso.com:9092,wn1-khdicl.contoso.com:9092`

4. Postupujte podle kroku 3 v části **sestavovat a nasazovat v příkladu** v [kurzu: Použití Apache Kafka Producer and Consumer API](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-producer-consumer-api#build-and-deploy-the-example) zajistit, aby `kafka-producer-consumer.jar` dostupná je i **sales_user**.

5. Ověřte, že **sales_user1** může vytvořit téma `salesevents` spuštěním následujícího příkazu:

   ```bash
   java -jar kafka-producer-consumer.jar producer salesevents $KAFKABROKERS
   ```

6. Spusťte následující příkaz k využívání z tématu `salesevents`:

   ```bash
   java -jar kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   Ověřte, že můžete ke čtení zpráv.

7. Ověřte, že **sales_user1** nesmí vytvořit téma `marketingspend` spuštěním následujícího ve stejném ssh okno:

   ```bash
   java -jar kafka-producer-consumer.jar producer marketingspend $KAFKABROKERS
   ```

   Dojde k chybě autorizace, kterou můžete ignorovat.

8. Všimněte si, že **marketing_user1** moci spotřebovat z tématu `salesevents`.

   Opakujte kroky 1 – 4 výše, ale tentokrát jako **marketing_user1**.

   Spusťte následující příkaz k využívání z tématu `salesevents`:

   ```bash
   java -jar kafka-producer-consumer.jar consumer salesevents $KAFKABROKERS
   ```

   Předchozí zprávy nejsou zobrazeny.

9. Zobrazte události přístupu k auditu v uživatelském rozhraní Ranger.

   ![Audit zásad uživatelského rozhraní Ranger](./media/apache-domain-joined-run-kafka/apache-ranger-admin-audit.png)

## <a name="next-steps"></a>Další postup

* [Přineste si vlastní klíč k Apache Kafka](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok)
* [Úvod do Apache Hadoop zabezpečení s balíčkem Enterprise Security Package](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-introduction)
