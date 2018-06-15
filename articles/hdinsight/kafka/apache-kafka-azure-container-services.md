---
title: Pomocí služby Azure Kubernetes Kafka v HDInsight | Microsoft Docs
description: Další informace o použití Kafka v HDInsight z bitové kopie kontejneru hostované v Azure Kubernetes služby (AKS).
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/07/2018
ms.author: larryfr
ms.openlocfilehash: f54039a0e702aa3c789363969120e000760f6ef5
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
ms.locfileid: "33885957"
---
# <a name="use-azure-kubernetes-service-with-kafka-on-hdinsight"></a>Pomocí služby Azure Kubernetes Kafka v HDInsight

Naučte se používat Azure Kubernetes služby (AKS) s Kafka na clusteru HDInsight. Kroky v tomto dokumentu používají k ověření připojení s Kafka hostované v AKS aplikace Node.js. Tato aplikace používá [kafka uzlu](https://www.npmjs.com/package/kafka-node) balíček ke komunikaci s Kafka. Používá [Socket.io](https://socket.io/) pro událost řízené zasílání zpráv mezi prohlížeče klienta a hostované ve AKS back-end.

[Apache Kafka](https://kafka.apache.org) je open source distribuovaná streamovací platforma, kterou lze použít k vytváření aplikací a kanálů pro streamování dat v reálném čase. Služba Azure Kubernetes spravuje hostovaného prostředí Kubernetes a umožňuje snadno a rychle nasadit kontejnerizované aplikace. Pomocí virtuální síť Azure, můžete připojit dvě služby.

> [!NOTE]
> Účelem tohoto dokumentu je na kroky potřebné k povolení služby Azure Kubernetes ke komunikaci s Kafka v HDInsight. Příkladem samotné je právě základní klientskou Kafka prokázat, že konfigurace funguje.

## <a name="prerequisites"></a>Požadavky

* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* Předplatné Azure

Tento dokument předpokládá, že jste obeznámeni s vytváření a používání následujících služeb Azure:

* Kafka v HDInsightu
* Služba Azure Kubernetes
* Virtuální sítě Azure

Tento dokument předpokládá také je třeba projít [kurz služby Azure Kubernetes](../../aks/tutorial-kubernetes-prepare-app.md). V tomto kurzu vytvoří kontejner služby, vytvoří cluster Kubernetes kontejneru registru a nakonfiguruje `kubectl` nástroj.

## <a name="architecture"></a>Architektura

### <a name="network-topology"></a>Síťová topologie

HDInsight a AKS použít virtuální síť Azure jako kontejner pro výpočetní prostředky. Pokud chcete povolit komunikaci mezi HDInsight a AKS, je nutné povolit komunikaci mezi sítí. Kroky v tomto dokumentu používají partnerský vztah virtuální síť k sítím. Další připojení, jako je například VPN, by taky měly fungovat. Další informace o vytvoření partnerského vztahu, najdete v článku [partnerský vztah virtuální sítě](../../virtual-network/virtual-network-peering-overview.md) dokumentu.


Následující diagram znázorňuje topologii sítě použitou v tomto dokumentu:

![HDInsight v jednu virtuální síť, AKS v jiném a sítě připojené pomocí partnerského vztahu](./media/apache-kafka-azure-container-services/kafka-aks-architecture.png)

> [!IMPORTANT]
> Rozlišení názvů není povoleno mezi peered sítí, takže adresování IP se používá. Ve výchozím nastavení je vrátit názvy hostitelů místo IP adresy, pokud se klienti připojují nakonfigurovaná Kafka v HDInsight. Kafka používat IP upravit kroky v tomto dokumentu místo toho inzerování.

## <a name="create-an-azure-kubernetes-service-aks"></a>Vytvoření služby Azure Kubernetes (AKS)

Pokud již nemáte AKS clusteru, použijte jednu z následujících dokumentech se dozvíte, jak chcete vytvořit:

* [Nasazení clusteru Azure Kubernetes služby (AKS) - portálu](../../aks/kubernetes-walkthrough-portal.md)
* [Nasazení clusteru Azure Kubernetes služby (AKS) - rozhraní příkazového řádku](../../aks/kubernetes-walkthrough.md)

> [!NOTE]
> AKS vytvoří virtuální síť během instalace. Tato síť je peered jako byla vytvořena pro HDInsight v další části.

## <a name="configure-virtual-network-peering"></a>Nakonfigurujte partnerský vztah virtuální sítě

1. Z [portál Azure](https://portal.azure.com), vyberte __skupiny prostředků__a pak najděte skupinu prostředků, která obsahuje virtuální síť pro váš cluster AKS. Název skupiny prostředků se `MC_<resourcegroup>_<akscluster>_<location>`. `resourcegroup` a `akscluster` položky jsou název skupinu prostředků, kterou jste vytvořili v clusteru a název clusteru. `location` Je umístění, vytvořený v clusteru.

2. Ve skupině prostředků, vyberte __virtuální síť__ prostředků.

3. Vyberte __adresní prostor__. Poznamenejte si uvedené adresní prostor.

4. Chcete-li vytvořit virtuální síť pro HDInsight, vyberte __+ vytvořit prostředek__, __sítě__a potom __virtuální síť__.

    > [!IMPORTANT]
    > Při zadávání hodnot pro nové virtuální sítě, je nutné použít adresní prostor, který se nepřekrývá používají AKS síť s clustery.

    Použijte stejný __umístění__ pro virtuální sítě, který jste použili pro AKS cluster.

    Vyčkejte, než přejdete k dalšímu kroku byl vytvořen virtuální sítě.

5. Chcete-li nakonfigurovat partnerský vztah mezi HDInsight a AKS síť s clustery, vyberte virtuální síť a pak vyberte __partnerských vztahů__. Vyberte __+ přidat__ a použijte následující hodnoty k vyplnění formuláře:

    * __Název__: Zadejte jedinečný název pro tuto konfiguraci partnerského vztahu.
    * __Virtuální síť__: Toto pole slouží k výběru virtuální sítě **AKS clusteru**.

    Ponechte všechna pole na výchozí hodnotu a pak vyberte __OK__ konfigurace partnerského vztahu.

6. K nastavení partnerského vztahu mezi síť s clustery AKS a sítí, HDInsight, vyberte __AKS clusteru virtuální sítě__a potom vyberte __partnerských vztahů__. Vyberte __+ přidat__ a použijte následující hodnoty k vyplnění formuláře:

    * __Název__: Zadejte jedinečný název pro tuto konfiguraci partnerského vztahu.
    * __Virtuální síť__: Toto pole slouží k výběru virtuální sítě __clusteru HDInsight__.

    Ponechte všechna pole na výchozí hodnotu a pak vyberte __OK__ konfigurace partnerského vztahu.

## <a name="install-kafka-on-hdinsight"></a>Nainstalujte Kafka v HDInsight

Při vytváření Kafka v clusteru HDInsight, musíte se připojit k virtuální síti vytvořený pro HDInsight. Další informace týkající se vytvoření clusteru Kafka, najdete v článku [vytvoření clusteru s podporou Kafka](apache-kafka-get-started.md) dokumentu.

> [!IMPORTANT]
> Při vytváření clusteru, je nutné použít __upřesňující nastavení__ připojovat k virtuální síti, kterou jste vytvořili pro HDInsight.

## <a name="configure-kafka-ip-advertising"></a>Konfigurace Kafka IP inzerování

Pomocí následujících kroků nakonfigurujte Kafka inzerovat IP adresy místo názvy domén:

1. Pomocí webového prohlížeče, přejděte na https://CLUSTERNAME.azurehdinsight.net. Nahraďte __CLUSTERNAME__ s názvem Kafka na clusteru HDInsight.

    Pokud budete vyzváni, použijte HTTPS uživatelské jméno a heslo pro cluster. Webové uživatelské rozhraní Ambari pro cluster se zobrazí.

2. Chcete-li zobrazit informace o Kafka, vyberte __Kafka__ ze seznamu na levé straně.

    ![Seznam služeb s Kafka zvýrazněná](./media/apache-kafka-azure-container-services/select-kafka-service.png)

3. Chcete-li zobrazit informace o konfiguraci Kafka, vyberte __konfigurací__ nejvyšší uprostřed.

    ![Konfigurací odkazy pro Kafka](./media/apache-kafka-azure-container-services/select-kafka-config.png)

4. Najít __kafka env__ konfigurace, zadejte `kafka-env` v __filtru__ pole v pravém horním rohu.

    ![Konfigurace Kafka, pro kafka env](./media/apache-kafka-azure-container-services/search-for-kafka-env.png)

5. Ke konfiguraci Kafka inzerovat IP adres, přidejte následující text k dolní části __kafka. env šablony__ pole:

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

6. Pokud chcete konfigurovat rozhraní, které Kafka naslouchá na, zadejte `listeners` v __filtru__ pole v pravém horním rohu.

7. Ke konfiguraci Kafka naslouchat na všech síťových rozhraní, změňte hodnotu v __naslouchací procesy__ do `PLAINTEXT://0.0.0.0:9092`.

8. Chcete-li uložit změny konfigurace, použijte __Uložit__ tlačítko. Zadejte textovou zprávu s popisem změny. Vyberte __OK__ po změny byly uloženy.

    ![Konfigurace tlačítko Uložit](./media/apache-kafka-azure-container-services/save-button.png)

9. Chcete-li zabránit chybám při restartování Kafka, použijte __služby akce__ tlačítko a vyberte __zapnout v režimu údržby__. Klepněte na tlačítko OK pro dokončení této operace.

    ![Akce služby s zapněte zvýrazněná údržby](./media/apache-kafka-azure-container-services/turn-on-maintenance-mode.png)

10. Chcete-li restartovat Kafka, použijte __restartujte__ tlačítko a vyberte __restartujte všechny vliv__. Potvrďte restartování a potom pomocí __OK__ tlačítko po dokončení operace.

    ![Restartujte tlačítkem s restartem vliv](./media/apache-kafka-azure-container-services/restart-button.png)

11. Zakázat režim údržby, použijte __služby akce__ tlačítko a vyberte __zapnout vypnout režimu údržby__. Vyberte **OK** pro dokončení této operace.

## <a name="test-the-configuration"></a>Otestujte konfiguraci

V tomto okamžiku Kafka a Azure Kubernetes služby jsou v komunikaci prostřednictvím peered virtuální sítě. K otestování tohoto připojení, použijte následující kroky:

1. Vytvořte téma Kafka, které používá testovací aplikace. Informace o vytváření Kafka témata najdete v tématu [vytvoření clusteru s podporou Kafka](apache-kafka-get-started.md) dokumentu.

2. Stáhněte si ukázková aplikace z [ https://github.com/Blackmist/Kafka-AKS-Test ](https://github.com/Blackmist/Kafka-AKS-Test).

3. Upravit `index.js` soubor a změňte následující řádky:

    * `var topic = 'mytopic'`: Nahraďte `mytopic` s názvem Kafka tématu používaný touto aplikací.
    * `var brokerHost = '176.16.0.13:9092`: Nahraďte `176.16.0.13` s interní IP adresu z jednoho z hostitelů zprostředkovatele pro váš cluster.

        Interní IP adresu zprostředkovatele hostitelů (workernodes) v clusteru, najdete v tématu [Ambari REST API](../hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-internal-ip-address-of-cluster-nodes) dokumentu. Vyberte IP adresu jedné z položek, kde název domény začíná `wn`.

4. Z příkazového řádku v `src` adresáře, nainstalujte závislosti a Docker použít k vytvoření image pro nasazení:

    ```bash
    docker build -t kafka-aks-test .
    ```

    > [!NOTE]
    > Balíčky požadované touto aplikací jsou zaškrtnutá políčka do úložiště, takže nemusíte používat `npm` nástroj k instalaci.

5. Přihlaste se do registru kontejner Azure (ACR) a najít název loginServer:

    ```bash
    az acr login --name <acrName>
    az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
    ```

    > [!NOTE]
    > Pokud neznáte název registru kontejner Azure nebo jsou obeznámeni s pomocí rozhraní příkazového řádku Azure pro práci s Kubernetes službu Azure najdete v článku [AKS kurzy](../../aks/tutorial-kubernetes-prepare-app.md).

6. Označit místní `kafka-aks-test` bitovou kopii s loginServer z ACR. Také přidat `:v1` se označuje verzi obrázku:

    ```bash
    docker tag kafka-aks-test <acrLoginServer>/kafka-aks-test:v1
    ```

7. Push bitovou kopii do registru:

    ```bash
    docker push <acrLoginServer>/kafka-aks-test:v1
    ```
    Tato operace trvá několik minut na dokončení.

8. Upravte soubor manifestu Kubernetes (`kafka-aks-test.yaml`) a nahraďte `microsoft` název ACR loginServer získali v kroku 4.

9. Použijte následující příkaz k nasazení nastavení aplikace z manifestu:

    ```bash
    kubectl create -f kafka-aks-test.yaml
    ```

10. Použijte následující příkaz potřeba sledovat u `EXTERNAL-IP` aplikace:

    ```bash
    kubectl get service kafka-aks-test --watch
    ```

    Jakmile je přiřazen externí IP adresu, použijte __kombinaci kláves CTRL + C__ ukončíte hodinek

11. Otevřete webový prohlížeč a zadejte externí IP adresu pro službu. Přijedete do stránka podobná na následujícím obrázku:

    ![Obrázek webové stránky](./media/apache-kafka-azure-container-services/test-web-page.png)

12. Zadejte text do pole a pak vyberte __odeslat__ tlačítko. Data je odeslána do Kafka. Pak přečte zprávu Kafka příjemce v aplikaci a přidává ji k __zprávy z Kafka__ části.

    > [!WARNING]
    > Může se zobrazit více kopií zprávu. Tento problém obvykle se stane, když aktualizujte webový prohlížeč po připojení nebo otevírat víc připojení prohlížeče do aplikace.

## <a name="next-steps"></a>Další postup

Následující odkazy popisují používání Apache Kafka ve službě HDInsight:

* [Začínáme s Kafka ve službě HDInsight](apache-kafka-get-started.md)

* [Vytvoření repliky Kafka ve službě HDInsight pomocí MirrorMakeru](apache-kafka-mirroring.md)

* [Použití Apache Stormu se systémem Kafka ve službě HDInsight](../hdinsight-apache-storm-with-kafka.md)

* [Použití Apache Sparku se systémem Kafka ve službě HDInsight](../hdinsight-apache-spark-with-kafka.md)

* [Připojení k systému Kafka přes virtuální síť Azure](apache-kafka-connect-vpn-gateway.md)
