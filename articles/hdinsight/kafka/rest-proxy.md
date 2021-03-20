---
title: Apache Kafka REST proxy – Azure HDInsight
description: Naučte se, jak provádět operace s Apache Kafka pomocí Kafka REST proxy v Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: has-adal-ref, devx-track-python
ms.date: 04/03/2020
ms.openlocfilehash: a9a007d33226c508e193368b08b189001bf53401
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944073"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Interakce s Apache Kafka clustery ve službě Azure HDInsight pomocí proxy REST

Kafka REST proxy umožňuje komunikovat s clusterem Kafka prostřednictvím REST API přes HTTPS. Tato akce znamená, že klienti Kafka můžou být mimo vaši virtuální síť. Klienti můžou provádět jednoduché zabezpečené volání HTTPS do clusteru Kafka a nemusíte přitom spoléhat na Kafka knihovny. Tento článek vám ukáže, jak vytvořit cluster Kafka s povoleným proxy serverem REST. Obsahuje také vzorový kód, který ukazuje, jak volat proxy REST.

## <a name="rest-api-reference"></a>REST API – referenční informace

Informace o operacích podporovaných Kafka REST API najdete v tématu [Reference k rozhraní API HDInsight Kafka REST](/rest/api/hdinsight-kafka-rest-proxy).

## <a name="background"></a>Pozadí

![Návrh proxy serveru REST Kafka](./media/rest-proxy/rest-proxy-architecture.png)

Úplnou specifikaci operací podporovaných rozhraním API najdete v tématu [rozhraní API služby REST proxy serveru Apache Kafka](/rest/api/hdinsight-kafka-rest-proxy).

### <a name="rest-proxy-endpoint"></a>Koncový bod proxy REST

Vytvoření clusteru HDInsight Kafka pomocí proxy REST vytvoří nový veřejný koncový bod pro váš cluster, který můžete najít ve **vlastnostech** clusteru HDInsight na Azure Portal.

### <a name="security"></a>Zabezpečení

Přístup k proxy Kafka REST se spravuje pomocí skupin zabezpečení Azure Active Directory. Při vytváření clusteru Kafka zadejte skupinu zabezpečení Azure AD s přístupem ke koncovému bodu REST. Kafka klienti, kteří potřebují přístup k proxy REST, by měli být k této skupině zaregistrovaní vlastníkem skupiny. Vlastník skupiny se může zaregistrovat prostřednictvím portálu nebo pomocí PowerShellu.

V případě požadavků koncových bodů proxy REST by klientské aplikace měly získat token OAuth. Token slouží k ověření členství ve skupině zabezpečení. Níže najdete [ukázku klientské aplikace](#client-application-sample) , která ukazuje, jak získat token OAuth. Klientská aplikace předá token OAuth v požadavku HTTPS proxy REST.

> [!NOTE]
> Další informace o skupinách zabezpečení AAD najdete v tématu [Správa přístupu k aplikacím a prostředkům pomocí skupin Azure Active Directory](../../active-directory/fundamentals/active-directory-manage-groups.md). Další informace o tom, jak tokeny OAuth fungují, najdete v tématu [autorizace přístupu k Azure Active Directory webových aplikací pomocí toku udělení kódu oauth 2,0](../../active-directory/azuread-dev/v1-protocols-oauth-code.md).

## <a name="kafka-rest-proxy-with-network-security-groups"></a>Kafka REST proxy se skupinami zabezpečení sítě
Pokud přenesete vlastní virtuální síť a řízení síťového provozu pomocí skupin zabezpečení sítě, povolte kromě portu 443 **příchozí** provoz na portu **9400** . Tím se zajistí, že proxy server Kafka REST je dosažitelný.

## <a name="prerequisites"></a>Předpoklady

1. Zaregistrovat aplikaci s Azure AD. Klientské aplikace, které napíšete pro interakci s proxy Kafka REST, budou k ověření v Azure používat ID a tajný kód této aplikace.

1. Vytvořte skupinu zabezpečení Azure AD. Přidejte aplikaci, kterou jste zaregistrovali ve službě Azure AD, do skupiny zabezpečení jako **člena** skupiny. Tato skupina zabezpečení se použije k určení, které aplikace můžou pracovat s proxy REST. Další informace o vytváření skupin Azure AD najdete v tématu [Vytvoření základní skupiny a přidání členů pomocí Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

    Ověřte, že je skupina typu **zabezpečení**.
    ![Skupina zabezpečení](./media/rest-proxy/rest-proxy-group.png)

    Ověřte, že je aplikace členem skupiny.
    ![Ověřit členství](./media/rest-proxy/rest-proxy-membergroup.png)

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Vytvoření clusteru Kafka s povolenou službou REST proxy

Následující postup používá Azure Portal. Příklad použití rozhraní příkazového řádku Azure najdete v tématu [vytvoření Apache Kafkaho clusteru REST proxy pomocí Azure CLI](tutorial-cli-rest-proxy.md).

1. V pracovním postupu vytváření clusteru Kafka na kartě **zabezpečení + sítě** zaškrtněte možnost **Povolit proxy REST Kafka** .

     ![Snímek obrazovky s vybraným zabezpečením a sítí zobrazí stránku vytvořit cluster s informacemi o clusteru v D.](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. Klikněte na **Vybrat skupinu zabezpečení**. V seznamu skupin zabezpečení vyberte skupinu zabezpečení, kterou chcete mít přístup k proxy REST. Pomocí vyhledávacího pole můžete najít příslušnou skupinu zabezpečení. Klikněte na tlačítko **Vybrat** v dolní části.

     ![Snímek obrazovky s možností výběru skupiny zabezpečení zobrazí stránku vytvořit cluster s příponou H D Insight.](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. Dokončete zbývající kroky k vytvoření clusteru, jak je popsáno v tématu [Vytvoření clusteru Apache Kafka ve službě Azure HDInsight pomocí Azure Portal](./apache-kafka-get-started.md).

1. Po vytvoření clusteru přejděte do vlastností clusteru a zaznamenejte adresu URL proxy serveru Kafka REST.

     ![Zobrazit adresu URL proxy serveru REST](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>Ukázka klientské aplikace

K interakci s proxy REST v clusteru Kafka můžete použít následující kód Pythonu. Chcete-li použít ukázku kódu, postupujte podle následujících kroků:

1. Uložte vzorový kód do počítače s nainstalovaným Pythonem.
1. Pomocí příkazu nainstalujte požadované závislosti Pythonu `pip3 install msal` .
1. Upravte část kód **konfigurace těchto vlastností** a aktualizujte následující vlastnosti svého prostředí:

    |Vlastnost |Popis |
    |---|---|
    |ID tenanta|Tenant Azure, ve kterém je vaše předplatné.|
    |ID klienta|ID aplikace, kterou jste zaregistrovali ve skupině zabezpečení.|
    |Tajný klíč klienta|Tajný klíč pro aplikaci, kterou jste zaregistrovali ve skupině zabezpečení.|
    |Kafkarest_endpoint|Tuto hodnotu získáte na kartě **vlastnosti** v přehledu clusteru, jak je popsáno v [části nasazení](#create-a-kafka-cluster-with-rest-proxy-enabled). Měl by být v následujícím formátu – `https://<clustername>-kafkarest.azurehdinsight.net`|

1. Z příkazového řádku spusťte soubor Python spuštěním příkazu. `sudo python3 <filename.py>`

Tento kód provede následující akci:

1. Načte token OAuth z Azure AD.
1. Ukazuje, jak vytvořit požadavek na Kafka proxy REST.

Další informace o získání tokenů OAuth v Pythonu najdete v tématu [Třída Python AuthenticationContext](/python/api/adal/adal.authentication_context.authenticationcontext). V takovém případě se může zobrazit prodleva `topics` , která není vytvořena ani smazána prostřednictvím proxy Kafka REST. Tato prodleva je způsobená obnovením mezipaměti. Pole **hodnota** rozhraní API pro producenta bylo vylepšeno. Nyní přijímá objekty JSON a všechny serializované formuláře.

```python
#Required python packages
#pip3 install msal

import json
import msal
import random
import requests
import string
import sys
import time

def get_random_string():
    letters = string.ascii_letters
    random_string = ''.join(random.choice(letters) for i in range(7))

    return random_string


#--------------------------Configure these properties-------------------------------#
# Tenant ID for your Azure Subscription
tenant_id = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Application Id
client_id = 'XYZABCDE-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Credentials
client_secret = 'password'
# kafka rest proxy -endpoint
kafkarest_endpoint = "https://<clustername>-kafkarest.azurehdinsight.net"
#--------------------------Configure these properties-------------------------------#

# Get access token
# Scope
scope = 'https://hib.azurehdinsight.net/.default'
#Authority
authority = 'https://login.microsoftonline.com/' + tenant_id

app = msal.ConfidentialClientApplication(
    client_id , client_secret, authority,
    #cache - For details on how look at this example: https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py
)

# The pattern to acquire a token looks like this.
result = None
result = app.acquire_token_for_client(scopes=[scope])
accessToken = result['access_token']
verify_https = True
request_timeout = 10

# Print access token
print("Access token: " + accessToken)

# API format
api_version = 'v1'
api_format = kafkarest_endpoint + '/{api_version}/{rest_api}'
get_topic_api = 'metadata/topics'
topic_api_format = 'topics/{topic_name}'
producer_api_format = 'producer/topics/{topic_name}'
consumer_api_format = 'consumer/topics/{topic_name}/partitions/{partition_id}/offsets/{offset}?count={count}'  # by default count = 1

# Request header
headers = {
    'Authorization': 'Bearer ' + accessToken,
    'Content-type': 'application/json'          # set Content-type to 'application/json'
}

# New topic
new_topic = 'hello_topic_' + get_random_string()
print("Topic " + new_topic + " is going to be used for demo.")

topics = []

# Create a  new topic
# Example of topic config
topic_config = {
    "partition_count": 1,
    "replication_factor": 1,
    "topic_properties": {
        "retention.ms": 604800000,
        "min.insync.replicas": "1"
    }
}

create_topic_url = api_format.format(api_version=api_version, rest_api=topic_api_format.format(topic_name=new_topic))
response = requests.put(create_topic_url, headers=headers, json=topic_config, timeout=request_timeout, verify=verify_https)
print(response.content)

if response.ok:
    while new_topic not in topics:
        print("The new topic " + new_topic + " is not visible yet. sleep 30 seconds...")
        time.sleep(30)
        # List Topic
        get_topic_url = api_format.format(api_version=api_version, rest_api=get_topic_api)

        response = requests.get(get_topic_url, headers={'Authorization': 'Bearer ' + accessToken}, timeout=request_timeout, verify=verify_https)
        topic_list = response.json()
        topics = topic_list.get("topics", [])
else:
    print("Topic " + new_topic + " was created. Exit.")
    sys.exit(1)

# Produce messages to new_topic
# Example payload of Producer REST API
payload_json = {
    "records": [
        {
            "key": "key1",
            "value": "**********"         # A string                              
        },
        {
            "partition": 0,
            "value": 5                    # An integer
        },
        {
            "value": 3.14                 # A floating number
        },
        {
            "value": {                    # A JSON object
                "id": 1,
                "name": "HDInsight Kafka REST proxy"
            }
        },
        {
            "value": [                    # A list of JSON objects
                {
                    "id": 1,
                    "name": "HDInsight Kafka REST proxy 1"
                },
                {
                    "id": 2,
                    "name": "HDInsight Kafka REST proxy 2"
                },
                {
                    "id": 3,
                    "name": "HDInsight Kafka REST proxy 3"
                }
            ]
        },
        {
            "value": {                  # A nested JSON object
                "group id": 1,
                "HDI Kafka REST": {
                    "id": 1,
                    "name": "HDInsight Kafka REST proxy 1"
                },
                "HDI Kafka REST server info": {
                    "id": 1,
                    "name": "HDInsight Kafka REST proxy 1",
                    "servers": [
                        {
                            "server id": 1,
                            "server name": "HDInsight Kafka REST proxy server 1"
                        },
                        {
                            "server id": 2,
                            "server name": "HDInsight Kafka REST proxy server 2"
                        },
                        {
                            "server id": 3,
                            "server name": "HDInsight Kafka REST proxy server 3"
                        }
                    ]
                }
            }
        }
    ]
}

print("Payloads in a Producer request: \n", payload_json)
producer_url = api_format.format(api_version=api_version, rest_api=producer_api_format.format(topic_name=new_topic))
response = requests.post(producer_url, headers=headers, json=payload_json, timeout=request_timeout, verify=verify_https)
print(response.content)

# Consume messages from the topic
partition_id = 0
offset = 0
count = 2

while True:
    consumer_url = api_format.format(api_version=api_version, rest_api=consumer_api_format.format(topic_name=new_topic, partition_id=partition_id, offset=offset, count=count))
    print("Consuming " + str(count) + " messages from offset " + str(offset))

    response = requests.get(consumer_url, headers=headers, timeout=request_timeout, verify=verify_https)

    if response.ok:
        messages = response.json()
        print("Consumed messages: \n" + json.dumps(messages, indent=2))
        next_offset = response.headers.get("NextOffset")
        if offset == next_offset or not messages.get("records", []):
            print("Consumer caught up with producer. Exit for now...")
            break

        offset = next_offset

    else:
        print("Error " + str(response.status_code))
        break
        
# List partitions
get_partitions_url = api_format.format(api_version=api_version, rest_api=partitions_api_format.format(topic_name=new_topic))
print("Fetching partitions from  " + get_partitions_url)

response = requests.get(get_partitions_url, headers={'Authorization': 'Bearer ' + accessToken}, timeout=request_timeout, verify=verify_https)
partition_list = response.json()
print("Partition list: \n" + json.dumps(partition_list, indent=2))

# List a partition
get_partition_url = api_format.format(api_version=api_version, rest_api=partition_api_format.format(topic_name=new_topic, partition_id=partition_id))
print("Fetching metadata of a partition from  " + get_partition_url)

response = requests.get(get_partition_url, headers={'Authorization': 'Bearer ' + accessToken}, timeout=request_timeout, verify=verify_https)
partition = response.json()
print("Partition metadata: \n" + json.dumps(partition, indent=2))

```

Níže najdete další ukázku, jak získat token z Azure pro proxy REST pomocí příkazu složeného. **Všimněte si, že pro `scope=https://hib.azurehdinsight.net/.default` získání tokenu potřebujeme zadaný.**

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&scope=https://hib.azurehdinsight.net/.default' 'https://login.microsoftonline.com/<tenantid>/oauth2/v2.0/token'
```

## <a name="next-steps"></a>Další kroky

* [Referenční dokumenty k rozhraní API proxy Kafka REST](/rest/api/hdinsight-kafka-rest-proxy/)
