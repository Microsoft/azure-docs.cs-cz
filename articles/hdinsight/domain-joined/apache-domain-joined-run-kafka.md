---
title: Konfigurace zásad Kafka ve službě HDInsight s balíčkem zabezpečení podniku - Azure
description: Zjistěte, jak nakonfigurovat zásady Apache Ranger pro systém Kafka v Azure HDInsight s balíčkem zabezpečení podniku.
services: hdinsight
ms.service: hdinsight
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: tutorial
ms.date: 09/24/2018
ms.openlocfilehash: 1a8f04f39568816252175fc9e0893f1ab3e2cdc6
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47224813"
---
# <a name="tutorial-configure-kafka-policies-in-hdinsight-with-enterprise-security-package-preview"></a>Kurz: Konfigurace zásad Kafka ve službě HDInsight s balíčkem zabezpečení podniku (Preview)

Zjistěte, jak nakonfigurovat zásady Apache Ranger pro clustery Kafka ESP (balíček zabezpečení podniku). ESP clustery jsou připojené k doméně, což uživatelům umožňuje ověření pomocí přihlašovacích údajů do domény. V tomto kurzu vytvoříte dvě zásady Ranger pro omezení přístupu k tématům `sales*` a `marketingspend`.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření uživatelů domén
> * Vytvoření zásad Ranger
> * Vytvoření témat v clusteru Kafka
> * Testování zásad Ranger

## <a name="before-you-begin"></a>Než začnete

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).

* Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

* Vytvořte [cluster HDInsight Kafka s balíčkem zabezpečení podniku](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-run-hive).

## <a name="connect-to-apache-ranger-admin-ui"></a>Připojení k uživatelskému rozhraní správce Apache Ranger

1. Z prohlížeče se připojte k uživatelskému rozhraní Ranger Admin pomocí adresy URL `https://<ClusterName>.azurehdinsight.net/Ranger/`. Nezapomeňte změnit `<ClusterName>` na název vašeho clusteru Kafka.

    > [!NOTE] 
    > Přihlašovací údaje k Rangeru nejsou stejné jako údaje ke clusteru Hadoop. Abyste zabránili prohlížeči v použití přihlašovacích údajů clusteru Hadoop uložených v mezipaměti, použijte pro připojení k uživatelskému rozhraní správce Ranger nové okno prohlížeče v režimu InPrivate.

2. Přihlaste se pomocí svých přihlašovacích údajů správce k Azure Active Directory (AD). Přihlašovací údaje správce k Azure AD nejsou stejné jako přihlašovací údaje ke clusteru HDInsight nebo přihlašovací údaje SSH k uzlu HDInsight Linux.

   ![Uživatelské rozhraní správce Apache Ranger](./media/apache-domain-joined-run-kafka/apache-ranger-admin-login.png)

## <a name="create-domain-users"></a>Vytvoření uživatelů domén

V tématu [vytvoření clusteru HDInsight s balíčkem zabezpečení podniku](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds#create-a-domain-joined-hdinsight-cluster) se naučíte vytvořit uživatele domény **sales_user** a **marketing_user**. V produkční scénáři uživatelé domény pocházejí z vašeho klienta služby Active Directory.

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

Abyste vytvořili dvě témata **salesevents** a **marketingspend**:

1. Navažte připojení SSH ke clusteru použitím následujícího příkazu:

   ```bash
   ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net
   ```

   Místo `SSHUSER` použijte jméno uživatele SSH pro váš cluster a místo `CLUSTERNAME` zadejte název clusteru. Pokud se zobrazí výzva, zadejte heslo uživatelského účtu SSH. Další informace o použití `scp` se službou HDInsight najdete v tématu [Použití SSH se službou HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix).

   V produkčním scénáři mohou uživatelé domény nakonfigurovaní během vytváření clusteru získat přístup do clusteru přes SSH.

2. Pomocí následujících příkazů uložte název clusteru do proměnné a nainstalujte nástroj pro parsování JSON `jq`. Po zobrazení výzvy zadejte název clusteru Kafka.

   ```bash
   sudo apt -y install jq
   read -p 'Enter your Kafka cluster name:' CLUSTERNAME
   ```

3. K získání hostitelů zprostředkovatelů Kafka a hostitelů Zookeeper použijte následující příkazy. Po zobrazení výzvy zadejte heslo účtu správce clusteru.

   ```bash
   export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`; \
   
   export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
   ```

4. Spusťte následující příkazy: 

   ```bash
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create salesevents $KAFKABROKERS
   
   java -jar -Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf kafka-producer-consumer.jar create marketingspend $KAFKABROKERS
   ```

   >[!NOTE] 
   >Pouze vlastník procesu služby Kafka, jako je například root, může zapisovat do z-uzlů Zookeeperu `/config/topics`. Zásady Ranger nejsou vynucovány, pokud uživatel bez oprávnění vytvoří téma. Je to proto, že skript `kafka-topics.sh` při tvorbě tématu komunikuje přímo s Zookeeperem. Položky jsou přidány do uzlů Zookeeperu, zatímco sledovací procesy na straně zprostředkovatele monitorují a tvoří témata odpovídajícím způsobem. Autorizaci není možné provést pomocí plug-inu Ranger a výše uvedený příkaz je spuštěn pomocí `sudo` prostřednictvím zprostředkovatele Kafka.


## <a name="test-the-ranger-policies"></a>Testování zásad Ranger

Na základě nakonfigurovaných zásad Ranger, mohou uživatelé **sales_user** tvořit nebo využívat téma **salesevents**, ale ne téma **marketingspend**. Naopak uživatel **marketing_user** může tvořit nebo využívat téma **marketingspend**, ale ne téma **salesevents**.

1. Otevřete nové připojení SSH ke clusteru. Pomocí následujícího příkazu se přihlaste jako uživatel **sales_user1**:

   ```bash
   ssh sales_user1@CLUSTERNAME-ssh.azurehdinsight.net
   ```

2. Spusťte následující příkaz:

   ```bash
   export KAFKA_OPTS="-Djava.security.auth.login.config=/usr/hdp/current/kafka-broker/config/kafka_client_jaas.conf"
   ```

3. Pomocí názvů zprostředkovatele a Zookeepera z předchozí části nastavte následující proměnné prostředí:

   ```bash
   export KAFKABROKERS=<brokerlist>:9092 
   ```

   Příklad: `export KAFKABROKERS=wn0-khdicl.contoso.com:9092,wn1-khdicl.contoso.com:9092`

   ```bash
   export KAFKAZKHOSTS=<zklist>:2181
   ```

   Příklad: `export KAFKAZKHOSTS=zk1-khdicl.contoso.com:2181,zk2-khdicl.contoso.com:2181`

4. Ověřte, že **sales_user1** může tvořit v tématu **salesevents**.
   
   Spuštěním následujícího příkazu spusťte využití konzoly pro téma **salesevents**:

   ```bash
   /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic salesevents --security-protocol SASL_PLAINTEXT
   ```

   Poté zadejte několik zpráv v konzole. Stisknutím kláves **Ctrl + C** ukončíte konzoly výrobce.

5. Spuštěním následujícího příkazu umožníte využívání tématu **salesevents**:

   ```bash
   /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --topic salesevents --security-protocol PLAINTEXTSASL --from-beginning
   ```
 
6. Ověřte, že se zobrazují zprávy, které jste zadali v předchozím kroku, a že **sales_user1** nesmí tvořit v tématu **marketingspend**.

   Ze stejného okna SSH jako dříve spusťte následující příkaz, kterým je možné tvořit v tématu **marketingspend**:

   ```bash
   /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic marketingspend --security-protocol SASL_PLAINTEXT
   ```

   Dojde k chybě autorizace, kterou můžete ignorovat. 

7. Všimněte si, že **marketing_user1** nemůže využívat téma **salesevents**.

   Zopakujte kroky 1 až 3 výše, ale tentokrát jako **marketing_user1**.

   Spuštěním následujícího příkazu umožníte využívání tématu **salesevents**:

   ```bash
   /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --zookeeper $KAFKAZKHOSTS --topic marketingspend --security-protocol PLAINTEXTSASL --from-beginning
   ```

   Předchozí zprávy nejsou zobrazeny.

8. Zobrazte události přístupu k auditu v uživatelském rozhraní Ranger.

   ![Audit zásad uživatelského rozhraní Ranger](./media/apache-domain-joined-run-kafka/apache-ranger-admin-audit.png)

## <a name="next-steps"></a>Další kroky

* [Používání vlastního klíče v systému Kafka](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok)
* [Úvod do zabezpečení Hadoop s balíčkem zabezpečení podniku](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-introduction)
