---
title: Proxy server Apache Kafka REST – Azure HDInsight
description: Naučte se, jak dělat operace Apache Kafka pomocí proxy Kafka REST na Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: 265e15713f8159e370ef22a197ffe931200a88f7
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758999"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Interakce s clustery Apache Kafka v Azure HDInsight pomocí proxy REST

Kafka REST Proxy umožňuje interakci s clusterem Kafka prostřednictvím rozhraní REST API přes HTTP. Tato akce znamená, že vaši klienti Kafka může být mimo vaši virtuální síť. Klienti mohou provádět jednoduchá volání HTTP do clusteru Kafka, místo spoléhání se na knihovny Kafka. Tento článek vám ukáže, jak vytvořit cluster Kafka s podporou serveru PROXY REST. Také poskytuje ukázkový kód, který ukazuje, jak volat na proxy REST.

## <a name="rest-api-reference"></a>REST API – referenční informace

Operace podporované rozhraním API Kafka REST naleznete v [tématu ODKAZ NA ROZHRANÍ PROXY HDInsight Kafka REST .](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy)

## <a name="background"></a>Pozadí

![Kafka REST proxy design](./media/rest-proxy/rest-proxy-architecture.png)

Úplné specifikace operací podporovaných rozhraním API naleznete v tématu [Apache Kafka REST Proxy API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

### <a name="rest-proxy-endpoint"></a>Koncový bod serveru REST proxy

Vytvoření clusteru HDInsight Kafka s proxy serverem REST vytvoří nový veřejný koncový bod pro váš cluster, který najdete ve **vlastnostech** clusteru HDInsight na webu Azure Portal.

### <a name="security"></a>Zabezpečení

Přístup k proxy serveru Kafka REST se spravuje pomocí skupin zabezpečení služby Azure Active Directory. Při vytváření clusteru Kafka, zadejte skupině zabezpečení Azure AD přístup ke koncovému bodu REST. Klienti Kafka, kteří potřebují přístup k proxy serveru REST, by měli být do této skupiny zaregistrováni vlastníkem skupiny. Vlastník skupiny se může zaregistrovat prostřednictvím portálu nebo přes Prostředí PowerShell.

Pro požadavky koncového bodu proxy REST by klientské aplikace měly získat token OAuth. Token se používá k ověření členství ve skupině zabezpečení. Najděte [ukázku klientské aplikace](#client-application-sample) níže, která ukazuje, jak získat token OAuth. Klientská aplikace předá token OAuth v požadavku HTTP proxy REST.

> [!NOTE]  
> Další informace o skupinách zabezpečení AAD najdete v tématu [Správa přístupu k aplikacím a prostředkům pomocí skupin Služby Azure Active Directory](../../active-directory/fundamentals/active-directory-manage-groups.md). Další informace o tom, jak tokeny OAuth fungují, najdete [v tématu Autorizace přístupu k webovým aplikacím Azure Active Directory pomocí toku udělení kódu OAuth 2.0](../../active-directory/develop/v1-protocols-oauth-code.md).

## <a name="prerequisites"></a>Požadavky

1. Zaregistrovat aplikaci s Azure AD. Klientské aplikace, které zapisujete pro interakci s proxy kafka REST bude používat id této aplikace a tajný klíč k ověření azure.

1. Vytvořte skupinu zabezpečení Azure AD. Přidejte aplikaci, kterou jste zaregistrovali ve službě Azure AD, do skupiny zabezpečení jako **člena** skupiny. Tato skupina zabezpečení bude použita k řízení, které aplikace mohou pracovat s proxy serverem REST. Další informace o vytváření skupin Azure AD najdete [v tématu Vytvoření základní skupiny a přidání členů pomocí služby Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

    Ověření skupiny je typu **Zabezpečení**.
    ![Skupina zabezpečení](./media/rest-proxy/rest-proxy-group.png)

    Ověřte, zda je tato aplikace členem skupiny.
    ![Zkontrolovat členství](./media/rest-proxy/rest-proxy-membergroup.png)

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Vytvoření clusteru Kafka s povoleným proxy serverem REST

1. Během pracovního postupu vytváření clusteru Kafka zaškrtněte na kartě **Zabezpečení + sítě** možnost **Povolit proxy server Kafka REST.**

     ![Povolení proxy serveru Kafka REST a výběr skupiny zabezpečení](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. Klepněte na **tlačítko Vybrat skupinu zabezpečení**. Ze seznamu skupin zabezpečení vyberte skupinu zabezpečení, ke které chcete mít přístup k serveru proxy REST. Pomocí vyhledávacího pole můžete najít příslušnou skupinu zabezpečení. Klikněte dole na tlačítko **Vybrat.**

     ![Povolení proxy serveru Kafka REST a výběr skupiny zabezpečení](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. Dokončete zbývající kroky k vytvoření clusteru, jak je popsáno v [aplikaci Create Apache Kafka clusteru v Azure HDInsight pomocí portálu Azure Portal](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started).

1. Po vytvoření clusteru přejděte do vlastností clusteru a zaznamenejte adresu URL proxy Kafka REST.

     ![zobrazit adresu URL serveru proxy REST](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>Ukázka klientské aplikace

Níže uvedený kód pythonu můžete použít k interakci s proxy rest v clusteru Kafka. Chcete-li použít ukázku kódu, postupujte takto:

1. Uložte ukázkový kód do počítače s nainstalovaným Pythonem.
1. Nainstalujte požadované závislosti pythonu spuštěním `pip3 install msal`.
1. Upravte část kódu **Konfigurace těchto vlastností** a aktualizace následujících vlastností pro vaše prostředí:

    |Vlastnost |Popis |
    |---|---|
    |ID tenanta|Tenant Azure, kde je vaše předplatné.|
    |ID klienta|ID aplikace, kterou jste zaregistrovali ve skupině zabezpečení.|
    |Tajný klíč klienta|Tajný klíč pro aplikaci, kterou jste zaregistrovali ve skupině zabezpečení.|
    |Kafkarest_endpoint|Tuto hodnotu získáte z karty **Vlastnosti** v přehledu clusteru, jak je popsáno v [části nasazení](#create-a-kafka-cluster-with-rest-proxy-enabled). Mělo by být v následujícím formátu –`https://<clustername>-kafkarest.azurehdinsight.net`|

1. Z příkazového řádku spusťte soubor pythonu spuštěním`sudo python3 <filename.py>`

Tento kód provádí následující akci:

1. Načte token OAuth z Azure AD.
1. Ukazuje, jak podat žádost na proxy Kafka REST.

Další informace o získání tokenů OAuth v pythonu naleznete v [tématu Python AuthenticationContext class](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python). Může se zobrazit `topics` zpoždění, zatímco nejsou vytvořeny nebo odstraněny prostřednictvím proxy Kafka REST se projeví tam. Toto zpoždění je z důvodu aktualizace mezipaměti.

```python
#Required python packages
#pip3 install msal

import msal

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

# Scope
scope = 'https://hib.azurehdinsight.net/.default'
#Authority
authority = 'https://login.microsoftonline.com/' + tenant_id

# Create a preferably long-lived app instance which maintains a token cache.
app = msal.ConfidentialClientApplication(
    client_id , client_secret, authority,
    #cache - For details on how look at this example: https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py
    )

# The pattern to acquire a token looks like this.
result = None

result = app.acquire_token_for_client(scopes=[scope])

print(result)
accessToken = result['access_token']

# relative url
getstatus = "/v1/metadata/topics"
request_url = kafkarest_endpoint + getstatus

# sending get request and saving the response as response object
response = requests.get(request_url, headers={'Authorization': accessToken})
print(response.content)
```

Níže najdete další ukázku, jak získat token z proxy serveru Azure for REST pomocí příkazu curl. **Všimněte si, `scope=https://hib.azurehdinsight.net/.default` že potřebujeme zadaný při získávání tokenu.**

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&scope=https://hib.azurehdinsight.net/.default' 'https://login.microsoftonline.com/<tenantid>/oauth2/v2.0/token'
```

## <a name="next-steps"></a>Další kroky

* [Referenční dokumenty proxy ROZHRANÍ KAFKA REST](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
