---
title: Proxy server Apache Kafka REST – Azure HDInsight
description: Zjistěte, jak provádět operace Apache Kafka pomocí proxy Kafka REST na Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: d99a3b803b80dc41990a63e647d3ba928deb31af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198901"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Interakce s clustery Apache Kafka v Azure HDInsight pomocí proxy REST

Kafka REST Proxy umožňuje interakci s clusterem Kafka prostřednictvím rozhraní REST API přes HTTP. To znamená, že vaši klienti Kafka mohou být mimo vaši virtuální síť. Kromě toho klienti mohou provádět jednoduchá volání HTTP pro odesílání a přijímání zpráv do clusteru Kafka namísto spoléhání se na knihovny Kafka. Tento kurz vám ukáže, jak vytvořit cluster Kafka s podporou serveru PROXY REST a poskytnout ukázkový kód, který ukazuje, jak volat na proxy server REST.

## <a name="rest-api-reference"></a>REST API – referenční informace

Úplné specifikace operací podporovaných rozhraním Kafka REST API naleznete v [tématu ODKAZ NA ROZHRANÍ API rozhraní PROXY HDInsight Kafka REST](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

## <a name="background"></a>Pozadí

![Architektura proxy Kafka REST](./media/rest-proxy/rest-proxy-architecture.png)

Úplné specifikace operací podporovaných rozhraním API naleznete v tématu [Apache Kafka REST Proxy API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

### <a name="rest-proxy-endpoint"></a>Koncový bod serveru REST proxy

Vytvoření clusteru HDInsight Kafka s proxy serverem REST vytvoří nový veřejný koncový bod pro váš cluster, který najdete v clusteru HDInsight "Vlastnosti" na webu Azure Portal.

### <a name="security"></a>Zabezpečení

Přístup k proxy serveru Kafka REST se spravuje pomocí skupin zabezpečení služby Azure Active Directory. Při vytváření clusteru Kafka s povoleným proxy serverem REST poskytnete skupinu zabezpečení Azure Active Directory, která by měla mít přístup ke koncovému bodu REST. Klienti Kafka (aplikace), které potřebují přístup k proxy serveru REST, by měli být do této skupiny zaregistrováni vlastníkem skupiny. Vlastník skupiny to může udělat prostřednictvím portálu nebo přes Powershell.

Před provedením požadavků na koncový bod proxy REST by měla klientská aplikace získat token OAuth k ověření členství v pravé skupině zabezpečení. Níže naleznete [ukázku klientské aplikace,](#client-application-sample) která ukazuje, jak získat token OAuth. Jakmile má klientská aplikace token OAuth, musí předat tento token v požadavku HTTP na proxy server REST.

> [!NOTE]  
> Další informace o skupinách zabezpečení AAD najdete v tématu [Správa přístupu k aplikacím a prostředkům pomocí skupin Služby Azure Active Directory](../../active-directory/fundamentals/active-directory-manage-groups.md). Další informace o tom, jak tokeny OAuth fungují, najdete [v tématu Autorizace přístupu k webovým aplikacím Azure Active Directory pomocí toku udělení kódu OAuth 2.0](../../active-directory/develop/v1-protocols-oauth-code.md).

## <a name="prerequisites"></a>Požadavky

1. Zaregistrovat aplikaci s Azure AD. Klientské aplikace, které zapisujete pro interakci s proxy kafka REST bude používat id této aplikace a tajný klíč k ověření azure.
1. Vytvořte skupinu zabezpečení Azure AD a přidejte aplikaci, kterou jste zaregistrovali ve službě Azure AD, do skupiny zabezpečení. Tato skupina zabezpečení bude použita k řízení, které aplikace mohou pracovat s proxy serverem REST. Další informace o vytváření skupin Azure AD najdete [v tématu Vytvoření základní skupiny a přidání členů pomocí služby Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Vytvoření clusteru Kafka s povoleným proxy serverem REST

1. Během pracovního postupu vytváření clusteru Kafka zaškrtněte na kartě Zabezpečení + sítě možnost "Povolit proxy server Kafka REST".

     ![Povolení proxy serveru Kafka REST a výběr skupiny zabezpečení](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. Klepněte na **tlačítko Vybrat skupinu zabezpečení**. Ze seznamu skupin zabezpečení vyberte skupinu zabezpečení, ke které chcete mít přístup k serveru proxy REST. Pomocí vyhledávacího pole můžete najít příslušnou skupinu zabezpečení. Klikněte dole na tlačítko **Vybrat.**

     ![Povolení proxy serveru Kafka REST a výběr skupiny zabezpečení](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. Dokončete zbývající kroky k vytvoření clusteru, jak je popsáno v [aplikaci Create Apache Kafka clusteru v Azure HDInsight pomocí portálu Azure Portal](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started).

1. Po vytvoření clusteru přejděte do vlastností clusteru a zaznamenejte adresu URL proxy Kafka REST.

     ![zobrazit adresu URL serveru proxy REST](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>Ukázka klientské aplikace

Níže uvedený kód pythonu můžete použít k interakci s proxy rest v clusteru Kafka. Chcete-li použít ukázku kódu, postupujte takto:

1. Uložte ukázkový kód do počítače s nainstalovaným Pythonem.
1. Nainstalujte požadované závislosti pythonu spuštěním `pip3 install adal` a `pip install msrestazure`.
1. Upravte část kódu *Konfigurace těchto vlastností* a aktualizace následujících vlastností pro vaše prostředí:
    1.  *ID klienta* – tenant Azure, kde je vaše předplatné.
    1.  *ID klienta* – ID aplikace, kterou jste zaregistrovali ve skupině zabezpečení.
    1.  *Tajný klíč klienta* – tajný klíč pro aplikaci, kterou jste zaregistrovali ve skupině zabezpečení
    1.  *Kafkarest_endpoint* – tuto hodnotu získáte z karty "vlastnosti" v přehledu clusteru, jak je popsáno v [části nasazení](#create-a-kafka-cluster-with-rest-proxy-enabled). Mělo by být v následujícím formátu –`https://<clustername>-kafkarest.azurehdinsight.net`
3. Z příkazového řádku spusťte soubor pythonu spuštěním`python <filename.py>`

Tento kód provádí následující akce:

1. Načte token OAuth z Azure AD
1. Ukazuje, jak podat žádost na proxy Kafka REST

Další informace o získání tokenů OAuth v pythonu naleznete v [tématu Python AuthenticationContext class](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python). Může se zobrazit zpoždění, zatímco témata, která nejsou vytvořena nebo odstraněna prostřednictvím proxy Kafka REST, se zde projeví. Toto zpoždění je způsobeno aktualizací mezipaměti.

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

## <a name="next-steps"></a>Další kroky

* [Referenční dokumenty proxy ROZHRANÍ KAFKA REST](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
