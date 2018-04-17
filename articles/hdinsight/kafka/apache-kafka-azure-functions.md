---
title: Používat Azure Functions k odesílání dat do Kafka v HDInsight | Microsoft Docs
description: Naučte se používat funkci Azure k zápisu dat do Kafka v HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/09/2018
ms.author: larryfr
ms.openlocfilehash: 6c462f9fe5e152c82be1a2b8643ee35d260a90f6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="use-kafka-on-hdinsight-from-an-azure-function-app"></a>Použít Kafka v HDInsight z aplikace Azure – funkce

Zjistěte, jak vytvořit aplikaci funkce Azure, který odesílá data do Kafka v HDInsight.

[Azure Functions](https://docs.microsoft.com/azure/azure-functions/) je bez serveru výpočetní služba. Umožňuje spustit kód na vyžádání bez nutnosti explicitně zřizovat nebo spravovat infrastrukturu.

[Apache Kafka](https://kafka.apache.org) je open source distribuovaná streamovací platforma, kterou lze použít k vytváření aplikací a kanálů pro streamování dat v reálném čase. Kafka také poskytuje funkci pro zprostředkování zpráv podobnou frontě zpráv, ve které můžete publikovat pojmenované datové proudy a přihlásit se k jejich odběru. Kafka ve službě HDInsight představuje spravovanou, vysoce škálovatelnou a vysoce dostupnou službu v cloudu Microsoft Azure.

Kafka v HDInsight neposkytuje rozhraní API na veřejného Internetu. K publikování nebo využívat data ze Kafka, je nutné se připojit ke clusteru Kafka pomocí virtuální síti Azure. Azure Functions nabízí pohodlný způsob, jak vytvořit veřejný koncový bod, který by vložil data do Kafka v HDInsight prostřednictvím brány virtuální sítě.

> [!NOTE]
> Účelem tohoto dokumentu je na kroky potřebné k povolení funkce Azure ke komunikaci s Kafka v HDInsight. Příkladem samotné je právě základní producent Kafka prokázat, že konfigurace funguje.

## <a name="prerequisites"></a>Požadavky

* Aplikace Azure funkce. Další informace najdete v tématu [vytvoření první funkce](../../azure-functions/functions-create-first-azure-function.md) dokumentaci.

* Virtuální síť Azure. Pro práci s Azure Functions, virtuální síť musí používat jednu z následujících rozsahů IP:

    * 10.0.0.0-10.255.255.255
    * 172.16.0.0-172.31.255.255
    * 192.168.0.0-192.168.255.255

    Další informace najdete v tématu [aplikace integrovat Azure Virtual Network](../../app-service/web-sites-integrate-with-vnet.md) dokumentu.

* Kafka na clusteru HDInsight. Informace o vytváření Kafka na clusteru HDInsight naleznete v tématu [vytvoření clusteru s podporou Kafka](apache-kafka-get-started.md) dokumentu.

    > [!IMPORTANT]
    > Při konfiguraci clusteru, je nutné použít __upřesňující nastavení__ krok a vybrat virtuální síť Azure a podsíť, která používá HDInsight. Vyberte virtuální síť a podsíť vytvořená v předchozím kroku.

    Další informace o Kafka a virtuální sítě najdete v tématu [připojit k Kafka přes virtuální síť](apache-kafka-connect-vpn-gateway.md) dokumentu.

## <a name="architecture"></a>Architektura

Kafka v HDInsight je obsažený ve virtuální síti Azure. Azure Functions může komunikovat s virtuální sítě pomocí brány Point-to-Site. Na následujícím obrázku je diagram Tato topologie sítě:

![Architektura Azure Functions připojení k HDInsight](./media/apache-kafka-azure-functions/kafka-azure-functions.png)

## <a name="configure-kafka"></a>Konfigurace Kafka

Informace v této části připraví Kafka clusteru tak, aby přijímal data z funkce Azure. Pokrývá akcí následující konfigurace:

* Inzerování IP
* Shromažďování Kafka zprostředkovatele IP adresy
* Vytváření Kafka téma

### <a name="configure-kafka-for-ip-advertising"></a>Konfigurace Kafka pro inzerování IP

Ve výchozím nastavení Zookeeper vrátí název domény zprostředkovatelé Kafka klientům. Tato konfigurace bez DNS server, nebude fungovat jako klient (Azure Functions) nelze překlad názvů pro virtuální síť. Pro tuto konfiguraci použijte ke konfiguraci Kafka inzerovat IP adresy namísto názvů domény následující kroky:

1. Pomocí webového prohlížeče, přejděte na https://CLUSTERNAME.azurehdinsight.net. Nahraďte __CLUSTERNAME__ s názvem Kafka na clusteru HDInsight.

    Pokud budete vyzváni, použijte HTTPS uživatelské jméno a heslo pro cluster. Webové uživatelské rozhraní Ambari pro cluster se zobrazí.

2. Chcete-li zobrazit informace o Kafka, vyberte __Kafka__ ze seznamu na levé straně.

    ![Seznam služeb s Kafka zvýrazněná](./media/apache-kafka-azure-functions/select-kafka-service.png)

3. Chcete-li zobrazit informace o konfiguraci Kafka, vyberte __konfigurací__ nejvyšší uprostřed.

    ![Konfigurací odkazy pro Kafka](./media/apache-kafka-azure-functions/select-kafka-config.png)

4. Najít __kafka env__ konfigurace, zadejte `kafka-env` v __filtru__ pole v pravém horním rohu.

    ![Konfigurace Kafka, pro kafka env](./media/apache-kafka-azure-functions/search-for-kafka-env.png)

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

    ![Konfigurace tlačítko Uložit](./media/apache-kafka-azure-functions/save-button.png)

9. Chcete-li zabránit chybám při restartování Kafka, použijte __služby akce__ tlačítko a vyberte __zapnout v režimu údržby__. Klepněte na tlačítko OK pro dokončení této operace.

    ![Akce služby s zapněte zvýrazněná údržby](./media/apache-kafka-azure-functions/turn-on-maintenance-mode.png)

10. Chcete-li restartovat Kafka, použijte __restartujte__ tlačítko a vyberte __restartujte všechny vliv__. Potvrďte restartování a potom pomocí __OK__ tlačítko po dokončení operace.

    ![Restartujte tlačítkem s restartem vliv](./media/apache-kafka-azure-functions/restart-button.png)

11. Zakázat režim údržby, použijte __služby akce__ tlačítko a vyberte __zapnout vypnout režimu údržby__. Vyberte **OK** pro dokončení této operace.

### <a name="get-the-kafka-broker-ip-address"></a>Získat IP adresu zprostředkovatele Kafka

K načtení plně kvalifikovaný název domény (FQDN) a IP adresy z uzlů v clusteru Kafka, použijte jednu z následujících metod:

```powershell
$resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

$clusterNICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

$nodes = @()
foreach($nic in $clusterNICs) {
    $node = new-object System.Object
    $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
    $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
    $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
    $nodes += $node
}
$nodes | sort-object Type
```

```azurecli
az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
```

Tento příkaz předpokládá, že `<resourcegroupname>` je název skupiny prostředků Azure, která obsahuje virtuální síť.

V seznamu názvů vrátil vyberte IP adresu workernode. Interní plně kvalifikovaný název domény uzlu začíná písmena `wn`. Tato IP adresa se používá funkce ke komunikaci s Kafka.

### <a name="create-a-kafka-topic"></a>Vytvoří téma Kafka

Kafka ukládá data v __témata__. Před odesláním dat Kafka z funkce Azure, musíte vytvořit funkci.

Chcete-li vytvořit téma, postupujte podle kroků v [vytvoření clusteru s podporou Kafka](apache-kafka-get-started.md) dokumentu.

## <a name="create-a-function-that-sends-to-kafka"></a>Vytvoří funkci, která odesílá do Kafka

> [!NOTE]
> Postup v této části vytvořit funkce JavaScript, který používá [kafka uzlu](https://www.npmjs.com/package/kafka-node) balíček k publikování dat Kafka:

1. Vytvořte novou __WebHook + API__ fungovat a vyberte __JavaScript__ jako jazyk. Další informace o vytvoření nové funkce, najdete v článku [vytvoření první funkce](../../azure-functions/functions-create-first-azure-function.md#create-function) dokumentu.

2. Použijte následující kód jako obsah funkce:

    ```javascript
    var kafka = require('kafka-node');

    // The topic and a Kafka broker host for your HDInsight cluster
    var topic = 'test';
    var brokerHost = '10.1.0.7:9092';
    // Create the client
    var client= new kafka.KafkaClient({kafkaHost: brokerHost});

    module.exports = function (context, req) {
        // Create the producer
        var producer = new kafka.Producer(client, {requireAcks: 1});

        context.log('JavaScript HTTP trigger function processed a request.');

        if (req.query.name || (req.body && req.body.name)) {
            var name = req.query.name || req.body.name
            context.res = {
                // status: 200, /* Defaults to 200 */
                body: "Hello " + name
            };
            // Create the payload, using the name as the body
            var payloads = [
                    { topic: topic, messages: [name]}
            ];
            context.log("calling producer.send");
            // Send the data to Kafka
            producer.send(payloads, function(err, data) {
                if(err) {
                    context.log(err);
                } else {
                    context.log(data);
                }
            });
        }
        else {
            context.res = {
                status: 400,
                body: "Please pass a name on the query string or in the request body"
            };
        }
        context.done();
    };
    ```

    Nahraďte `'test'` s názvem Kafka tématu, které jste vytvořili v clusteru HDInsight.

    Nahraďte `10.1.0.7` s IP adresou, který jste získali dříve. Ponechte `:9092` hodnotu. 9092 je port, který Kafka naslouchá na.

    Použití __Uložit__ tlačítko a uložte změny.

    ![Editor s tlačítko Uložit](./media/apache-kafka-azure-functions/function-editor.png)

3. Napravo od editoru funkce, vyberte __zobrazit soubory__. Vyberte __+ přidat__ a přidejte nový soubor s názvem `package.json`. Tento soubor se používá k určení závislost na `kafka-node` balíčku.

    ![Přidání souboru](./media/apache-kafka-azure-functions/add-files.png)

4. Chcete-li upravit nový soubor, vyberte `package.json` z __zobrazit soubory__ seznamu. Použijte následující text jako obsah souboru:

    ```json
    {
    "name": "kafkatest",
    "version": "1.0.0",
    "description": "",
    "main": "index.js",
    "author": "",
    "license": "ISC",
    "dependencies": {
            "kafka-node": "^2.4.1"
        }
    }
    ```

    Použití __Uložit__ tlačítko a uložte změny.

5. K instalaci `kafka-node` balíčku, použijte _uzel verze a balíček správy_ části [Příručka pro vývojáře Azure funkce JavaScript](../../azure-functions/functions-reference-node.md#node-version-and-package-management).

    > [!NOTE]
    > Při instalaci balíčku uzlem kafka, může se zobrazit několik chyb. Tyto chyby můžete bezpečně ignorovat.

## <a name="run-the-function"></a>Spuštění funkce

Napravo od editoru funkce, vyberte __Test__. Ponechejte výchozí nastavení pro test a potom vyberte __spustit__. Test běží, předá `name` parametr funkce. Tento parametr obsahuje hodnotu `Azure`, které funkce vloží do Kafka.

![Snímek obrazovky testovací dialogové okno](./media/apache-kafka-azure-functions/function-test-dialog.png)

Chcete-li zobrazit informace při spuštění testu protokolována funkce, vyberte __protokoly__ v dolní části stránky. Spusťte test znovu generovat informace o protokolu.

![Příklad výstupu protokolu – funkce](./media/apache-kafka-azure-functions/function-log.png)

## <a name="verify-the-data-is-in-kafka"></a>Ověřte, že data jsou v Kafka

Pokud chcete ověřit, že byly přijaty data v tomto tématu Kafka, použijte informace v _vytvořit a využívat záznamy_ části [vytvoření clusteru s podporou Kafka](apache-kafka-get-started.md#produce-and-consume-records) dokumentu. `kafka-console-consumer` Čte data z tématu a zobrazí seznam zpráv, které jsou uloženy v tomto tématu.

## <a name="next-steps"></a>Další postup

Následující odkazy popisují používání Apache Kafka ve službě HDInsight:

* [Začínáme s Kafka ve službě HDInsight](apache-kafka-get-started.md)

* [Vytvoření repliky Kafka ve službě HDInsight pomocí MirrorMakeru](apache-kafka-mirroring.md)

* [Použití Apache Stormu se systémem Kafka ve službě HDInsight](../hdinsight-apache-storm-with-kafka.md)

* [Použití Apache Sparku se systémem Kafka ve službě HDInsight](../hdinsight-apache-spark-with-kafka.md)

* [Připojení k systému Kafka přes virtuální síť Azure](apache-kafka-connect-vpn-gateway.md)
