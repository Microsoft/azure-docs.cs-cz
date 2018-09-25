---
title: Použití služby Azure Kubernetes s využitím Kafka v HDInsight
description: Další informace o použití systému Kafka v HDInsight z imagí kontejnerů, které jsou hostované ve službě Azure Kubernetes Service (AKS).
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: 9170a771b50af4a031d747df547152beda52d01f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46998300"
---
# <a name="use-azure-kubernetes-service-with-kafka-on-hdinsight"></a>Použití služby Azure Kubernetes s využitím Kafka v HDInsight

Zjistěte, jak používat Azure Kubernetes Service (AKS) s využitím Kafka v clusteru HDInsight. Kroky v tomto dokumentu používají aplikace Node.js hostované ve službě AKS k ověření připojení s využitím systému Kafka. Tato aplikace používá [kafka uzel](https://www.npmjs.com/package/kafka-node) balíček ke komunikaci s využitím systému Kafka. Používá [Socket.io](https://socket.io/) pro zasílání zpráv mezi klientský prohlížeč a back-end hostovaný ve službě AKS řízené událostmi.

[Apache Kafka](https://kafka.apache.org) je open source distribuovaná streamovací platforma, kterou lze použít k vytváření aplikací a kanálů pro streamování dat v reálném čase. Azure Kubernetes Service spravuje vaše hostované prostředí Kubernetes a umožňuje rychlé a snadné nasazování kontejnerizovaných aplikací. Pomocí služby Azure Virtual Network, propojíte tyto dvě služby.

> [!NOTE]
> Účelem tohoto dokumentu je na kroky potřebné k povolení služby Azure Kubernetes Service ke komunikaci s využitím Kafka v HDInsight. V příkladu, samotného je jenom základní klientskou Kafka prokázat, že konfigurace funguje.

## <a name="prerequisites"></a>Požadavky

* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* Předplatné Azure

Tento dokument předpokládá, že máte zkušenosti s vytvářením a používání následujících služeb Azure:

* Kafka v HDInsightu
* Azure Kubernetes Service
* Virtuální sítě Azure

Tento dokument předpokládá také, že je třeba projít [kurz služby Azure Kubernetes Service](../../aks/tutorial-kubernetes-prepare-app.md). Tento kurz vytvoří služby kontejneru, vytvoří cluster Kubernetes, registr kontejnerů a nakonfiguruje `kubectl` nástroj.

## <a name="architecture"></a>Architektura

### <a name="network-topology"></a>Síťová topologie

HDInsight a AKS pomocí služby Azure Virtual Network jako kontejner pro výpočetní prostředky. K umožnění komunikace mezi HDInsight a AKS, je nutné povolit komunikaci mezi své sítě. Kroky v tomto dokumentu používají partnerské vztahy virtuálních sítí k sítím. Další připojení, například ze sítě VPN, mělo by také fungovat. Další informace o vytvoření partnerského vztahu, najdete v článku [partnerský vztah virtuálních sítí](../../virtual-network/virtual-network-peering-overview.md) dokumentu.


Následující diagram znázorňuje topologii sítě, v tomto dokumentu:

![HDInsight v jedné virtuální sítě, AKS v jiném a sítí připojenou pomocí partnerského vztahu](./media/apache-kafka-azure-container-services/kafka-aks-architecture.png)

> [!IMPORTANT]
> Mezi partnerské sítě, takže používat IP adresy není povolen překlad názvů. Ve výchozím nastavení je nakonfigurovaná Kafka v HDInsight k vrácení názvů hostitelů, místo IP adresy, pokud se klienti připojují. Upravit kroky v tomto dokumentu Kafka použít IP adresu místo toho inzerování.

## <a name="create-an-azure-kubernetes-service-aks"></a>Vytvoření Azure Kubernetes Service (AKS)

Pokud již nemáte AKS cluster, použijte jednu z následujících dokumentech se naučíte vytvořit:

* [Nasaďte cluster Azure Kubernetes Service (AKS) – portál](../../aks/kubernetes-walkthrough-portal.md)
* [Nasaďte cluster Azure Kubernetes Service (AKS) – rozhraní příkazového řádku](../../aks/kubernetes-walkthrough.md)

> [!NOTE]
> AKS vytvoří virtuální síť během instalace. Tato síť je v partnerském vztahu vytvořeném pro HDInsight v další části.

## <a name="configure-virtual-network-peering"></a>Nakonfigurujte partnerský vztah virtuální sítě

1. Z [webu Azure portal](https://portal.azure.com)vyberte __skupiny prostředků__a pak vyhledejte skupinu prostředků obsahující virtuální sítě pro váš cluster AKS. Název skupiny prostředků je `MC_<resourcegroup>_<akscluster>_<location>`. `resourcegroup` a `akscluster` položky jsou název skupiny prostředků, vytvoří se cluster a název clusteru. `location` Je umístění, vytvořený v clusteru.

2. Ve skupině prostředků, vyberte __virtuální síť__ prostředků.

3. Vyberte __adresní prostor__. Poznámka: uvedené adresní prostor.

4. Chcete-li vytvořit virtuální síť pro HDInsight, vyberte __+ vytvořit prostředek__, __sítě__a potom __virtuální síť__.

    > [!IMPORTANT]
    > Při zadávání hodnot pro nové virtuální sítě, je nutné použít adresní prostor, který se nepřekrývá identifikátoru použitému síť clusteru AKS.

    Použijte stejný __umístění__ pro virtuální sítě, který jste použili pro AKS cluster.

    Počkejte na vytvoření virtuální sítě než přejdete k dalšímu kroku.

5. Pokud chcete nakonfigurovat partnerský vztah mezi HDInsight sítě a sítě clusteru AKS, vyberte virtuální síť a pak vyberte __partnerské vztahy__. Vyberte __+ přidat__ a k naplnění formuláře použijte následující hodnoty:

    * __Název__: Zadejte jedinečný název pro tuto konfiguraci partnerského vztahu.
    * __Virtuální sítě__: Toto pole použít k výběru virtuální sítě v případě **clusteru AKS**.

    Nechte ve všech polích výchozí hodnoty a pak vyberte __OK__ konfigurace partnerského vztahu.

6. Pokud chcete nakonfigurovat partnerský vztah mezi sítě clusteru AKS a síť HDInsight, vyberte __virtuální sítě clusteru AKS__a pak vyberte __partnerské vztahy__. Vyberte __+ přidat__ a k naplnění formuláře použijte následující hodnoty:

    * __Název__: Zadejte jedinečný název pro tuto konfiguraci partnerského vztahu.
    * __Virtuální sítě__: Toto pole použít k výběru virtuální sítě v případě __clusteru HDInsight__.

    Nechte ve všech polích výchozí hodnoty a pak vyberte __OK__ konfigurace partnerského vztahu.

## <a name="install-kafka-on-hdinsight"></a>Instalace systému Kafka v HDInsight

Při vytváření Kafka v clusteru HDInsight, musíte připojit virtuální síť vytvořili dříve pro HDInsight. Další informace týkající se vytvoření clusteru Kafka, najdete v článku [vytvoření clusteru Kafka](apache-kafka-get-started.md) dokumentu.

> [!IMPORTANT]
> Při vytváření clusteru, je nutné použít __upřesňující nastavení__ připojení k virtuální síti, kterou jste vytvořili pro HDInsight.

## <a name="configure-kafka-ip-advertising"></a>Konfigurace IP reklamy Kafka

Použijte následující postup ke konfiguraci Kafka inzerovat IP adres místo názvů domén:

1. Pomocí webového prohlížeče, přejděte na https://CLUSTERNAME.azurehdinsight.net. Nahraďte __CLUSTERNAME__ s názvem Kafka v clusteru HDInsight.

    Po zobrazení výzvy použijte HTTPS uživatelské jméno a heslo pro cluster. Webové uživatelské rozhraní Ambari pro cluster se zobrazí.

2. Chcete-li zobrazit informace o Kafka, vyberte __Kafka__ ze seznamu na levé straně.

    ![Zvýrazněný seznamu služeb s využitím Kafka](./media/apache-kafka-azure-container-services/select-kafka-service.png)

3. Chcete-li zobrazit informace o konfiguraci Kafka, vyberte __Configs__ z nahoře uprostřed.

    ![Konfigurace odkazů pro systém Kafka](./media/apache-kafka-azure-container-services/select-kafka-config.png)

4. Najít __kafka env__ konfigurace, zadejte `kafka-env` v __filtr__ pole v pravém horním rohu.

    ![Konfigurace Kafka, kafka env](./media/apache-kafka-azure-container-services/search-for-kafka-env.png)

5. Ke konfiguraci Kafka inzerovat IP adresy, přidejte následující text k dolní části __kafka env šablony__ pole:

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Pokud chcete nakonfigurovat rozhraní, které naslouchá Kafka, zadejte `listeners` v __filtr__ pole v pravém horním rohu.

7. Ke konfiguraci Kafka tak, aby naslouchala na všech síťových rozhraních, změňte hodnotu v __naslouchacích procesů__ pole `PLAINTEXT://0.0.0.0:9092`.

8. Chcete-li uložit změny konfigurace, použijte __Uložit__ tlačítko. Zadejte textovou zprávu s popisem změny. Vyberte __OK__ po změny se uložily.

    ![Konfigurace tlačítko Uložit](./media/apache-kafka-azure-container-services/save-button.png)

9. Chcete-li zabránit chybám při restartování Kafka, použijte __akce služby__ tlačítko a vyberte __zapnout v režimu údržby__. Klepněte na tlačítko OK pro dokončení této operace.

    ![Akce služby s zapnout zvýrazněnou údržby](./media/apache-kafka-azure-container-services/turn-on-maintenance-mode.png)

10. Chcete-li restartovat Kafka, použijte __restartovat__ tlačítko a vyberte __restartujte všechny ovlivněné__. Potvrďte restartování a pak použít __OK__ tlačítka po dokončení operace.

    ![Restartujte zvýrazněným tlačítkem s restartem vliv](./media/apache-kafka-azure-container-services/restart-button.png)

11. Chcete-li zakázat režim údržby, použijte __akce služby__ tlačítko a vyberte __zapnout vypnout režim údržby__. Vyberte **OK** k dokončení této operace.

## <a name="test-the-configuration"></a>Otestujte konfiguraci

V tomto okamžiku Kafka a Azure Kubernetes Service jsou v komunikaci prostřednictvím partnerských virtuálních sítích. K otestování tohoto připojení, použijte následující kroky:

1. Vytvoří téma Kafka, který používá testovací aplikace. Informace o vytváření témat Kafka, najdete v článku [vytvoření clusteru Kafka](apache-kafka-get-started.md) dokumentu.

2. Stáhněte si ukázková aplikace z [ https://github.com/Blackmist/Kafka-AKS-Test ](https://github.com/Blackmist/Kafka-AKS-Test).

3. Upravit `index.js` soubor a změňte následující řádky:

    * `var topic = 'mytopic'`: Nahradit `mytopic` s názvem tématu Kafka používaný touto aplikací.
    * `var brokerHost = '176.16.0.13:9092`: Nahradit `176.16.0.13` s interní IP adresa z jednoho z hostitelů zprostředkovatele pro váš cluster.

        Interní IP adresa zprostředkovatele hostitele (workernodes) v clusteru, najdete v tématu [rozhraní Ambari REST API](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-internal-ip-address-of-cluster-nodes) dokumentu. Vyberte IP adresu jedné z položek, kde začíná název domény `wn`.

4. Z příkazového řádku ve `src` adresáře, nainstalujte závislosti a sestavte image pro nasazení pomocí Dockeru:

    ```bash
    docker build -t kafka-aks-test .
    ```

    > [!NOTE]
    > Balíčky požadované aplikace se změnami do úložiště, takže není potřeba použít `npm` nástroj k instalaci.

5. Přihlaste se k vaší Azure Container Registry (ACR) a název loginServer zjistíte následujícím způsobem:

    ```bash
    az acr login --name <acrName>
    az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
    ```

    > [!NOTE]
    > Pokud neznáte název Azure Container Registry nebo nejste obeznámeni s pomocí Azure CLI pro práci s Azure Kubernetes Service, najdete v článku [kurzech AKS](../../aks/tutorial-kubernetes-prepare-app.md).

6. Označit místní `kafka-aks-test` image názvem loginServer záznamu ACR. Přidejte také `:v1` za účelem k označení verze image:

    ```bash
    docker tag kafka-aks-test <acrLoginServer>/kafka-aks-test:v1
    ```

7. Nahrání image do registru:

    ```bash
    docker push <acrLoginServer>/kafka-aks-test:v1
    ```
    Tato operace trvá několik minut.

8. Upravit soubor manifestu Kubernetes (`kafka-aks-test.yaml`) a nahraďte `microsoft` název loginServer služby ACR získali v kroku 4.

9. Použijte následující příkaz k nasazení nastavení aplikace z manifestu:

    ```bash
    kubectl create -f kafka-aks-test.yaml
    ```

10. Použijte následující příkaz a sledujte pro `EXTERNAL-IP` aplikace:

    ```bash
    kubectl get service kafka-aks-test --watch
    ```

    Jakmile je externí IP adresa se přiřadí, použijte __CTRL + C__ ukončíte hodinek

11. Otevřete webový prohlížeč a zadejte externí IP adresu pro službu. Dostanete na stránku podobně jako na následujícím obrázku:

    ![Snímek webové stránky](./media/apache-kafka-azure-container-services/test-web-page.png)

12. Zadejte text do pole a pak vyberte __odeslat__ tlačítko. Data se odesílají do systému Kafka. Příjemce Kafka v aplikaci přečte zprávu a přidá jej do __zprávy z Kafka__ oddílu.

    > [!WARNING]
    > Může se zobrazit několik kopií zprávu. Tento problém obvykle se stane, když aktualizujete prohlížeč po připojení nebo otevírat víc připojení prohlížeče do aplikace.

## <a name="next-steps"></a>Další postup

Následující odkazy popisují používání Apache Kafka ve službě HDInsight:

* [Začínáme s Kafka ve službě HDInsight](apache-kafka-get-started.md)

* [Vytvoření repliky Kafka ve službě HDInsight pomocí MirrorMakeru](apache-kafka-mirroring.md)

* [Použití Apache Stormu se systémem Kafka ve službě HDInsight](../hdinsight-apache-storm-with-kafka.md)

* [Použití Apache Sparku se systémem Kafka ve službě HDInsight](../hdinsight-apache-spark-with-kafka.md)

* [Připojení k systému Kafka přes virtuální síť Azure](apache-kafka-connect-vpn-gateway.md)
