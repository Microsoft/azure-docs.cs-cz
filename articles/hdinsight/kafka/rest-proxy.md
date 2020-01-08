---
title: Apache Kafka REST proxy – Azure HDInsight
description: Naučte se provádět operace Apache Kafka pomocí proxy serveru REST Kafka ve službě Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: a64d03ebe7c8bbb4cfa9c7bd63a678892250373d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75482865"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Interakce s Apache Kafka clustery ve službě Azure HDInsight pomocí proxy REST

Proxy Kafka REST umožňuje komunikovat s clusterem Kafka prostřednictvím REST API přes protokol HTTP. To znamená, že klienti Kafka můžou být mimo virtuální síť. Kromě toho můžou klienti provádět jednoduché volání HTTP pro posílání a přijímání zpráv do clusteru Kafka a nemusí se spoléhat na Kafka knihovny.  

## <a name="background"></a>Pozadí

### <a name="architecture"></a>Architektura

Bez proxy REST musí být klienti Kafka ve stejné virtuální síti jako cluster Kafka nebo virtuální síť s partnerským vztahem. Proxy REST umožňuje připojit producenty dat nebo spotřebitele, kteří se nacházejí kdekoli. Nasazení serveru REST proxy vytvoří nový veřejný koncový bod pro váš cluster, který můžete najít v nastavení portálu.

Úplnou specifikaci operací podporovaných rozhraním API najdete v tématu [Apache Kafka proxy REST API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

### <a name="security"></a>Zabezpečení

Přístup k proxy Kafka REST se spravuje pomocí skupin zabezpečení Azure Active Directory. Další informace najdete v tématu [Správa přístupu k aplikacím a prostředkům pomocí skupin Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups).

Při vytváření clusteru Kafka se zapnutým proxy serverem REST zadáte skupinu zabezpečení AAD, která by měla mít přístup ke koncovému bodu REST. Kafka klienti (aplikace), kteří potřebují přístup k proxy REST, by měli být k této skupině zaregistrovaní vlastníkem skupiny. Vlastník skupiny to může provést prostřednictvím portálu nebo pomocí PowerShellu.

Před odesláním žádostí na koncový bod proxy REST by klientská aplikace měla získat token OAuth pro ověření členství ve správné skupině zabezpečení. Další informace o tom, jak tokeny OAuth fungují, najdete v tématu [autorizace přístupu k Azure Active Directory webových aplikací pomocí toku udělení kódu oauth 2,0](../../active-directory/develop/v1-protocols-oauth-code.md). Příklad Načtení tokenu OAuth v Pythonu najdete v tématu [ukázková klientská aplikace](#client-application-sample) .

Jakmile má klientská aplikace token OAuth, musí tento token předat v žádosti HTTP provedené proxy REST.

## <a name="prerequisites"></a>Požadavky

1. Zaregistrovat aplikaci s Azure AD. Klientské aplikace, které napíšete pro interakci s proxy Kafka REST, budou k ověření v Azure používat ID a tajný kód této aplikace.
1. Vytvořte skupinu zabezpečení Azure AD a přidejte aplikaci, kterou jste zaregistrovali ve službě Azure AD, do skupiny zabezpečení. Tato skupina zabezpečení se použije k určení, které aplikace můžou pracovat s proxy REST. Další informace o vytváření skupin Azure AD najdete v tématu [Vytvoření základní skupiny a přidání členů pomocí Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Vytvoření clusteru Kafka s povolenou službou REST proxy

1. V pracovním postupu vytváření clusteru Kafka na kartě zabezpečení + sítě zaškrtněte možnost Povolit proxy REST Kafka.

     ![Povolit proxy REST Kafka a vybrat skupinu zabezpečení](./media/apache-kafka-rest-proxy/apache-kafka-rest-proxy-enable.png)

1. Klikněte na **Vybrat skupinu zabezpečení**. V seznamu skupin zabezpečení vyberte skupinu zabezpečení, kterou chcete mít přístup k proxy REST. Pomocí vyhledávacího pole můžete najít příslušnou skupinu zabezpečení. Klikněte na tlačítko **Vybrat** v dolní části.

     ![Povolit proxy REST Kafka a vybrat skupinu zabezpečení](./media/apache-kafka-rest-proxy/apache-kafka-rest-proxy-select-security-group.png)

1. Dokončete zbývající kroky k vytvoření clusteru, jak je popsáno v tématu [Vytvoření clusteru Apache Kafka ve službě Azure HDInsight pomocí Azure Portal](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started).

1. Po vytvoření clusteru přejděte do vlastností clusteru a zaznamenejte adresu URL proxy serveru Kafka REST.

     ![Zobrazit adresu URL proxy serveru REST](./media/apache-kafka-rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>Ukázka klientské aplikace

K interakci s proxy REST v clusteru Kafka můžete použít následující kód Pythonu. Tento kód provede následující:

1. Načte token OAuth z Azure AD.
1. Vytvoří zadané téma.
1. Odesílá zprávy do tohoto tématu.
1. Využívá zprávy z tohoto tématu.

Další informace o získání tokenů OAuth v Pythonu najdete v tématu [Třída Python AuthenticationContext](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python). Může se zobrazit zpoždění, pokud se některá témata, která nejsou vytvořená nebo Odstraněná pomocí proxy REST Kafka, odrážejí. Tato prodleva je způsobena aktualizací mezipaměti.

```python
#Required python packages
#pip3 install adal
#pip install msrestazure

import adal
from msrestazure.azure_active_directory import AdalAuthentication
from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD
import requests

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

#getting token
login_endpoint = AZURE_PUBLIC_CLOUD.endpoints.active_directory
resource = "https://hib.azurehdinsight.net"
context = adal.AuthenticationContext(login_endpoint + '/' + tenant_id)

token = context.acquire_token_with_client_credentials(
    resource,
    client_id,
    client_secret)

accessToken = 'Bearer ' + token['accessToken']

print(accessToken)

# relative url
getstatus = "/v1/metadata/topics"
request_url = kafkarest_endpoint + getstatus

# sending get request and saving the response as response object
response = requests.get(request_url, headers={'Authorization': accessToken})
print(response.content)
```

Chcete-li použít ukázku kódu, postupujte podle následujících kroků:

1. Uložte vzorový kód do počítače s nainstalovaným Pythonem.
1. Požadované závislosti v Pythonu Nainstalujte spuštěním `pip3 install adal` a `pip install msrestazure`.
1. Upravte kód a aktualizujte následující vlastnosti svého prostředí:
    1.  *ID tenanta* – tenant Azure, ve kterém je vaše předplatné.
    1.  *ID klienta* – ID aplikace, kterou jste zaregistrovali ve skupině zabezpečení.
    1.  *Tajný kód klienta* – tajný kód pro aplikaci, kterou jste zaregistrovali ve skupině zabezpečení
    1.  *Kafkarest_endpoint* – získat tuto hodnotu z karty Properties (vlastnosti) v přehledu clusteru, jak je popsáno v [části nasazení](#create-a-kafka-cluster-with-rest-proxy-enabled). Měl by být v následujícím formátu – `https://<clustername>-kafkarest.azurehdinsight.net`
3. Z příkazového řádku spusťte soubor Python spuštěním `python <filename.py>`

## <a name="next-steps"></a>Další kroky

* [Referenční dokumenty k rozhraní API proxy Kafka REST](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
* [Kurz: použití rozhraní API pro Apache Kafka výrobce a příjemce](apache-kafka-producer-consumer-api.md)