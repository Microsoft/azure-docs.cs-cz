---
title: Ověřování
titleSuffix: Azure Cognitive Services
description: 'Existují tři způsoby ověření požadavku na prostředek služby Azure Cognitive Services: klíč předplatného, token nosiče nebo předplatné s více službami. V tomto článku se dozvíte o jednotlivých metodách a o tom, jak podat žádost.'
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 11/22/2019
ms.author: erhopf
ms.openlocfilehash: 1c13c2cc4d4e562d3512de90338d874091dfeef6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74423942"
---
# <a name="authenticate-requests-to-azure-cognitive-services"></a>Ověřování požadavků na služby Azure Cognitive Services

Každý požadavek na službu Azure Cognitive Service musí obsahovat ověřovací hlavičku. Tato hlavička předá klíč předplatného nebo přístupový token, který se používá k ověření předplatného pro službu nebo skupinu služeb. V tomto článku se dozvíte o třech způsobech ověření požadavku a požadavcích pro každý z nich.

* [Ověření pomocí klíče předplatného jedné služby](#authenticate-with-a-single-service-subscription-key)
* [Ověření pomocí klíče předplatného s více službami](#authenticate-with-a-multi-service-subscription-key)
* [Ověření pomocí tokenu](#authenticate-with-an-authentication-token)
* [Ověření pomocí služby Azure Active Directory (AAD)](#authenticate-with-azure-active-directory)

## <a name="prerequisites"></a>Požadavky

Než podáte žádost, potřebujete účet Azure a předplatné Azure Cognitive Services. Pokud již účet máte, přejděte k další části. Pokud nemáte účet, máme průvodce, který vám pomůže nastavit během několika minut: [Vytvoření účtu služeb Cognitive Services pro Azure](cognitive-services-apis-create-account.md).

Klíč předplatného můžete získat z [portálu Azure](cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) po vytvoření účtu nebo aktivaci [bezplatné zkušební verze](https://azure.microsoft.com/try/cognitive-services/my-apis).

## <a name="authentication-headers"></a>Záhlaví ověřování

Pojďme rychle zkontrolovat záhlaví ověřování, které jsou k dispozici pro použití se službami Azure Cognitive Services.

| Hlavička | Popis |
|--------|-------------|
| Ocp-Apim-Subscription-Key | Tato hlavička slouží k ověření pomocí klíče předplatného pro konkrétní službu nebo klíč předplatného s více službami. |
| Ocp-Apim-Subscription-Region | Tato hlavička je vyžadována pouze při použití klíče předplatného více služeb s [rozhraním Translator Text API](./Translator/reference/v3-0-reference.md). Tato hlavička slouží k určení oblasti předplatného. |
| Autorizace | Tuto hlavičku použijte, pokud používáte ověřovací token. Kroky k provedení výměny tokenů jsou podrobně popsány v následujících částech. Uvedená hodnota je následující `Bearer <TOKEN>`formát: . |

## <a name="authenticate-with-a-single-service-subscription-key"></a>Ověření pomocí klíče předplatného jedné služby

První možností je ověření požadavku pomocí klíče předplatného pro konkrétní službu, jako je text překladače. Klíče jsou dostupné na webu Azure Portal pro každý prostředek, který jste vytvořili. Chcete-li použít klíč předplatného k ověření požadavku, `Ocp-Apim-Subscription-Key` musí být předán jako záhlaví.

Tyto ukázkové požadavky ukazuje, `Ocp-Apim-Subscription-Key` jak použít záhlaví. Nezapomeňte, že při použití této ukázky budete muset zahrnout platný klíč předplatného.

Toto je ukázkové volání rozhraní API pro vyhledávání na webu Bingu:
```cURL
curl -X GET 'https://api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Toto je ukázkové volání překladače text api:
```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

Následující video ukazuje použití klíče Cognitive Services.

## <a name="authenticate-with-a-multi-service-subscription-key"></a>Ověření pomocí klíče předplatného s více službami

>[!WARNING]
> V současné době tyto služby **nepodporují** víceservisní klíče: QnA Maker, hlasové služby, vlastní vidění a detektor anomálií.

Tato možnost také používá klíč předplatného k ověření požadavků. Hlavní rozdíl je, že klíč předplatného není vázána na konkrétní služby, spíše jeden klíč lze použít k ověření požadavků na více služeb Cognitive Services. Informace o místní dostupnosti, podporovaných funkcích a cenách najdete v [tématu Ceny služeb Cognitive Services.](https://azure.microsoft.com/pricing/details/cognitive-services/)

Klíč předplatného je k dispozici v `Ocp-Apim-Subscription-Key` každém požadavku jako záhlaví.

[![Ukázka klíče předplatného s více službami pro služby Cognitive Services](./media/index/single-key-demonstration-video.png)](https://www.youtube.com/watch?v=psHtA1p7Cas&feature=youtu.be)

### <a name="supported-regions"></a>Podporované oblasti

Při použití klíče předplatného více služeb k `api.cognitive.microsoft.com`vytvoření požadavku na , musíte zahrnout oblast v adrese URL. Například: `westus.api.cognitive.microsoft.com`.

Při použití klíče předplatného více služeb s překladačem textového `Ocp-Apim-Subscription-Region` rozhraní API, je nutné zadat oblast předplatného s hlavičkou.

Ověřování více služeb je podporováno v těchto oblastech:

| | | |
|-|-|-|
| `australiaeast` | `brazilsouth` | `canadacentral` |
| `centralindia` | `eastasia` | `eastus` |
| `japaneast` | `northeurope` | `southcentralus` |
| `southeastasia` | `uksouth` | `westcentralus` |
| `westeurope` | `westus` | `westus2` |


### <a name="sample-requests"></a>Ukázkové požadavky

Toto je ukázkové volání rozhraní API pro vyhledávání na webu Bingu:

```cURL
curl -X GET 'https://YOUR-REGION.api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Toto je ukázkové volání překladače text api:

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Ocp-Apim-Subscription-Region: YOUR_SUBSCRIPTION_REGION' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

## <a name="authenticate-with-an-authentication-token"></a>Ověření pomocí ověřovacího tokenu

Některé služby Azure Cognitive Services přijímají a v některých případech vyžadují ověřovací token. V současné době tyto služby podporují ověřovací tokeny:

* Rozhraní API pro překlad textu
* Hlasové služby: Rozhraní REST API pro převod řeči na text
* Hlasové služby: Rozhraní REST API pro převod textu na řeč

>[!NOTE]
> QnA Maker také používá hlavičku Autorizace, ale vyžaduje klíč koncového bodu. Další informace naleznete v tématu [QnA Maker: Získat odpověď ze znalostní báze](./qnamaker/quickstarts/get-answer-from-knowledge-base-using-url-tool.md).

>[!WARNING]
> Služby, které podporují ověřovací tokeny, se mohou v průběhu času měnit, před použitím této metody ověřování zkontrolujte odkaz na rozhraní API pro službu.

Klíče předplatného jedné služby i více služeb lze vyměnit za ověřovací tokeny. Ověřovací tokeny jsou platné po dobu 10 minut.

Ověřovací tokeny jsou zahrnuty `Authorization` v požadavku jako záhlaví. Zadaný token hodnota musí předcházet `Bearer`, například: `Bearer YOUR_AUTH_TOKEN`.

### <a name="sample-requests"></a>Ukázkové požadavky

Tato adresa URL slouží k výměně `https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken`klíče předplatného pro ověřovací token: .

```cURL
curl -v -X POST \
"https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
-H "Content-type: application/x-www-form-urlencoded" \
-H "Content-length: 0" \
-H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

Tyto oblasti s více službami podporují výměnu tokenů:

| | | |
|-|-|-|
| `australiaeast` | `brazilsouth` | `canadacentral` |
| `centralindia` | `eastasia` | `eastus` |
| `japaneast` | `northeurope` | `southcentralus` |
| `southeastasia` | `uksouth` | `westcentralus` |
| `westeurope` | `westus` | `westus2` |

Po získání ověřovacího tokenu ho budete muset předat v `Authorization` každém požadavku jako záhlaví. Toto je ukázkové volání překladače text api:

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Authorization: Bearer YOUR_AUTH_TOKEN' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

[!INCLUDE [](../../includes/cognitive-services-azure-active-directory-authentication.md)]

## <a name="see-also"></a>Viz také

* [Co je služba Cognitive Services?](welcome.md)
* [Ceny služeb Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/)
* [Vlastní subdomény](cognitive-services-custom-subdomains.md)
