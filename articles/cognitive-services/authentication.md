---
title: Authentication
titleSuffix: Cognitive Services - Azure
description: 'Existují tři způsoby, jak ověřit požadavek na prostředek Azure Cognitive Services: klíč předplatného, nosný token nebo víc služeb předplatného. V tomto článku se dozvíte o jednotlivých metod a jak vytvořit žádost.'
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: erhopf
ms.openlocfilehash: 8adff94a4cb536c3d8dbf7382b8acb69184030b5
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53325843"
---
# <a name="authenticate-requests-to-azure-cognitive-services"></a>Ověření požadavků ve službě Azure Cognitive Services

Každý požadavek do služby Azure Cognitive musí obsahovat hlavičku ověřování. Této hlavičky předá klíč předplatného nebo přístupový token, který se používá k ověření vašeho předplatného služby nebo skupiny služeb. V tomto článku se dozvíte o tři způsoby, jak ověřit požadavek a požadavky pro každý.

* [Ověření s klíčem jednoúčelovou předplatného](#authenticate-with-a-single-service-subscription-key)
* [Ověření s klíčem víc služeb předplatného](#authenticate-with-a-multi-service-subscription-key)
* [Ověřování pomocí tokenu](#authenticate-with-an-authentication-token)

## <a name="authentication-headers"></a>Ověřovací hlavičky

Pojďme ověřovací hlavičky, které jsou k dispozici pro použití se službou Azure Cognitive Services rychle.

| Hlavička | Popis |
|--------|-------------|
| OCP-Apim-Subscription-Key | Tuto hlavičku používají k ověření pomocí klíče předplatného pro konkrétní službu nebo klíč víc služeb předplatného. Pokud používáte víc služeb předplatného klíč, oblast pro vaše předplatné musí být uvedena jako `Ocp-Apim-Subscription-Region` záhlaví. |
| OCP-Apim předplatné – oblasti | Tato hlavička se pouze při použití klíče víc služeb předplatného se vyžaduje [Translator Text API](./Translator/reference/v3-0-reference.md). Tuto hlavičku používají k určení oblasti předplatného. |
| Autorizace | Tuto hlavičku používají, pokud používáte ověřovací token. V následujících oddílech jsou podrobně popsané kroky nutné k provedení výměny tokenu. Zadaná hodnota následující formát: `Bearer <TOKEN>`. |

## <a name="authenticate-with-a-single-service-subscription-key"></a>Ověření s klíčem jednoúčelovou předplatného

První možností je žádost o ověření pomocí klíče předplatného pro konkrétní službu, jako je Translator Text. Klíče jsou k dispozici na webu Azure Portal pro každý prostředek, který jste vytvořili. Žádost o ověření pomocí klíče předplatného, je nutné předají se jako `Ocp-Apim-Subscription-Key` záhlaví.

Tyto požadavky na ukázky, ukazuje, jak používat `Ocp-Apim-Subscription-Key` záhlaví. Mějte na paměti, při použití této ukázky, budete muset zahrnovat klíč platné předplatné.

Toto je ukázka volání rozhraní API webové vyhledávání Bingu:
```cURL
curl -X GET 'https://api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Toto je ukázka volání rozhraní Translator Text API:
```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

## <a name="authenticate-with-a-multi-service-subscription-key"></a>Ověření s klíčem víc služeb předplatného

>[!WARNING]
> V tuto chvíli tyto služby **není** podporovat víc služeb klíče: Nástroj QnA Maker, hlasových služeb a Custom Vision.

Tato možnost také používá k ověřování žádostí zasílaných klíč předplatného. Hlavní rozdíl je, že klíč předplatného se neváže ke konkrétní službě, jeden klíč můžete místo toho použije k ověření žádosti pro více služeb Cognitive Services. Zobrazit [ceny služeb Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/) informace o místní dostupnosti, podporované funkce a ceny.

Klíč předplatného je součástí každého požadavku, jako `Ocp-Apim-Subscription-Key` záhlaví.

### <a name="supported-regions"></a>Podporované oblasti

Při použití klíče víc služeb předplatného na vytvořit žádost o `api.cognitive.microsoft.com`, musí obsahovat oblast v adrese URL. Například: `westus.api.cognitive.microsoft.com`.

Pokud používáte víc služeb předplatného klíč s rozhraním Translator Text API, je nutné zadat oblast předplatné s `Ocp-Apim-Subscription-Region` záhlaví.

Ověřování víc služeb se podporuje v těchto oblastech:

| | | |
|-|-|-|
| `australiaeast` | `brazilsouth` | `canadacentral` |
| `centralindia` | `eastasia` | `eastus` |
| `japaneast` | `northeurope` | `southcentralus` |
| `southeastasia` | `uksouth` | `westcentralus` |
| `westeurope` | `westus` | `westus2` |


### <a name="sample-requests"></a>Požadavky na ukázky

Toto je ukázka volání rozhraní API webové vyhledávání Bingu:

```cURL
curl -X GET 'https://YOUR-REGION.api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Toto je ukázka volání rozhraní Translator Text API:

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Ocp-Apim-Subscription-Region: YOUR_SUBSCRIPTION_REGION' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

## <a name="authenticate-with-an-authentication-token"></a>Ověřování pomocí tokenu ověření

Několik služeb Azure Cognitive Services přijmout a v některých případech vyžadovat, ověřovací token. Tyto služby v současné době podporují ověřování tokenů:

* Rozhraní API pro překlad textu
* Hlasové služby: Rozhraní REST Speech to text API
* Hlasové služby: Převod textu na řeč REST API

>[!NOTE]
> Nástroj QnA Maker také používá hlavičku autorizace, ale vyžaduje klíč rozhraní koncového bodu. Další informace najdete v tématu [QnA Maker: Získání odpovědí ze znalostní báze](./qnamaker/quickstarts/get-answer-from-kb-using-curl.md).

>[!WARNING]
> Služby, které podporují ověřování tokenů může v průběhu času měnit, prosím odkaz rozhraní API pro kontrolu služby před použitím této metody ověřování.

Obě jedné služby a klíče víc služeb předplatného může probíhat pro ověřování tokenů. Tokeny ověřování jsou platné po dobu 10 minut.

Tokeny ověřování jsou součástí žádost jako `Authorization` záhlaví. Poskytnutá hodnota tokenu musí předcházet párový příkaz `Bearer`, například: `Bearer YOUR_AUTH_TOKEN`.

### <a name="sample-requests"></a>Požadavky na ukázky

Pomocí této adresy URL pro výměnu klíč předplatného jednoúčelovou pro ověřovací token: `https://api.cognitive.microsoft.com/sts/v1.0/issueToken`.

```cURL
curl -v -X POST \
"https://api.cognitive.microsoft.com/sts/v1.0/issueToken" \
-H "Content-type: application/x-www-form-urlencoded" \
-H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

Při použití klíče předplatného víc služeb, musíte použít oblast určitého koncového bodu pro výměnu tokenů. Pomocí této adresy URL pro výměnu klíč víc služeb předplatného pro ověřovací token: `https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken`.

Tyto víc služeb regiony podporují průběhu výměny tokenů:

| | | |
|-|-|-|
| `australiaeast` | `brazilsouth` | `canadacentral` |
| `centralindia` | `eastasia` | `eastus` |
| `japaneast` | `northeurope` | `southcentralus` |
| `southeastasia` | `uksouth` | `westcentralus` |
| `westeurope` | `westus` | `westus2` |

```cURL
curl -v -X POST \
"https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
-H "Content-type: application/x-www-form-urlencoded" \
-H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

Po získání ověřovacího tokenu, je potřeba předat v každé žádosti o jako `Authorization` záhlaví. Toto je ukázka volání rozhraní Translator Text API:

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Authorization: Bearer YOUR_AUTH_TOKEN' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

## <a name="see-also"></a>Další informace najdete v tématech

* [Co je služba Cognitive Services?](welcome.md)
* [Ceny služeb cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/)
* [Vytvoření účtu](cognitive-services-apis-create-account.md)
