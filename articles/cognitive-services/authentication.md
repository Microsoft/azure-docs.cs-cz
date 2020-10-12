---
title: Authentication
titleSuffix: Azure Cognitive Services
description: 'Existují tři způsoby, jak ověřit požadavek na prostředek služby Azure Cognitive Services: klíč předplatného, nosný token nebo předplatné s více službami. V tomto článku se dozvíte o jednotlivých metodách a o tom, jak vytvořit žádost.'
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 11/22/2019
ms.author: erhopf
ms.openlocfilehash: 4fab0be90e6941d1a6b8f137ae574223b0d7a9d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86232742"
---
# <a name="authenticate-requests-to-azure-cognitive-services"></a>Ověřování požadavků do Azure Cognitive Services

Každý požadavek na službu rozpoznávání Azure musí obsahovat hlavičku ověřování. Tato hlavička se předává na klíč předplatného nebo přístupového tokenu, který se používá k ověření předplatného služby nebo skupiny služeb. V tomto článku se dozvíte o třech způsobech ověření žádosti a požadavků na jednotlivé služby.

* Ověřování pomocí klíče předplatného s [jednou službou](#authenticate-with-a-single-service-subscription-key) nebo [více službami](#authenticate-with-a-multi-service-subscription-key)
* Ověřování pomocí [tokenu](#authenticate-with-an-authentication-token)
* Ověřování pomocí [Azure Active Directory (AAD)](#authenticate-with-azure-active-directory)

## <a name="prerequisites"></a>Požadavky

Před vytvořením žádosti potřebujete účet Azure a předplatné Azure Cognitive Services. Pokud již účet máte, přejděte k další části a přejděte k dalšímu oddílu. Pokud účet nemáte, máme v něm průvodce, který vám pomůže nastavit [účet Cognitive Services pro Azure](cognitive-services-apis-create-account.md).

Klíč předplatného můžete získat z [Azure Portal](cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) po [Vytvoření účtu](https://azure.microsoft.com/free/cognitive-services/).

## <a name="authentication-headers"></a>Ověřovací hlavičky

Pojďme rychle zkontrolovat hlavičky ověřování, které jsou k dispozici pro použití s Azure Cognitive Services.

| Záhlaví | Description |
|--------|-------------|
| Ocp-Apim-Subscription-Key | Tuto hlavičku použijte k ověření pomocí klíče předplatného pro konkrétní službu nebo klíč předplatného s více službami. |
| OCP – APIM – předplatné – oblast | Tato hlavička se vyžaduje jenom v případě, že se ke [službě Translator](./Translator/reference/v3-0-reference.md)používá klíč předplatného s více službami. Pomocí této hlavičky můžete zadat oblast předplatného. |
| Autorizace | Tuto hlavičku použijte v případě, že používáte ověřovací token. Postup pro výměnu tokenu je podrobně popsán v následujících částech. Zadaná hodnota je následující: `Bearer <TOKEN>` . |

## <a name="authenticate-with-a-single-service-subscription-key"></a>Ověřování pomocí klíče předplatného s jednou službou

První možností je ověřit požadavek pomocí klíče předplatného pro konkrétní službu, jako je například Translator. Klíče jsou k dispozici v Azure Portal pro každý prostředek, který jste vytvořili. Chcete-li k ověření požadavku použít klíč předplatného, musí být předán společně s `Ocp-Apim-Subscription-Key` hlavičkou.

Tyto ukázkové požadavky demonstrují, jak používat `Ocp-Apim-Subscription-Key` hlavičku. Mějte na paměti, že při použití této ukázky budete muset zahrnout platný klíč předplatného.

Toto je ukázkové volání rozhraní API Bingu pro vyhledávání na webu:
```cURL
curl -X GET 'https://api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Toto je ukázkové volání služby Translator:
```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

Následující video demonstruje použití Cognitive Servicesho klíče.

## <a name="authenticate-with-a-multi-service-subscription-key"></a>Ověřování pomocí klíče předplatného s více službami

>[!WARNING]
> V tuto chvíli tyto služby nepodporují klíče s **více službami:** QnA maker, Speech services, Custom Vision a detektor anomálií.

Tato možnost také používá k ověření požadavků klíč předplatného. Hlavní rozdíl spočívá v tom, že klíč předplatného není vázaný na konkrétní službu, ale jeden klíč lze použít k ověření požadavků na více Cognitive Services. Informace o místní dostupnosti, podporovaných funkcích a cenách najdete v tématu [Cognitive Services ceny](https://azure.microsoft.com/pricing/details/cognitive-services/) .

Klíč předplatného je k dispozici v každé žádosti jako `Ocp-Apim-Subscription-Key` záhlaví.

[![Ukázka klíče předplatného pro více služeb pro Cognitive Services](./media/index/single-key-demonstration-video.png)](https://www.youtube.com/watch?v=psHtA1p7Cas&feature=youtu.be)

### <a name="supported-regions"></a>Podporované oblasti

Při použití klíče předplatného s více službami k vytvoření požadavku na `api.cognitive.microsoft.com` , musíte zahrnout oblast do adresy URL. Například: `westus.api.cognitive.microsoft.com`.

Při použití klíče předplatného s více službami se službou Translator musíte zadat oblast předplatného s `Ocp-Apim-Subscription-Region` hlavičkou.

Ověřování více službami je podporované v těchto oblastech:

- `australiaeast`
- `brazilsouth`
- `canadacentral`
- `centralindia`
- `eastasia`
- `eastus`
- `japaneast`
- `northeurope`
- `southcentralus`
- `southeastasia`
- `uksouth`
- `westcentralus`
- `westeurope`
- `westus`
- `westus2`

### <a name="sample-requests"></a>Ukázkové požadavky

Toto je ukázkové volání rozhraní API Bingu pro vyhledávání na webu:

```cURL
curl -X GET 'https://YOUR-REGION.api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Toto je ukázkové volání služby Translator:

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Ocp-Apim-Subscription-Region: YOUR_SUBSCRIPTION_REGION' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

## <a name="authenticate-with-an-authentication-token"></a>Ověřování pomocí ověřovacího tokenu

Některé služby Azure Cognitive Services akceptují a v některých případech vyžadují ověřovací token. V současné době tyto služby podporují ověřovací tokeny:

* Rozhraní API pro překlad textu
* Speech Services: Převod řeči na text REST API
* Speech Services: převod textu na řeč REST API

>[!NOTE]
> QnA Maker používá taky autorizační hlavičku, ale vyžaduje klíč koncového bodu. Další informace najdete v tématu [QnA maker: získání odpovědi ze znalostní báze Knowledge Base](./qnamaker/quickstarts/get-answer-from-knowledge-base-using-url-tool.md).

>[!WARNING]
> Služby, které podporují tokeny ověřování, se můžou v průběhu času měnit, před použitím této metody ověřování prosím zkontrolujte Reference k rozhraní API pro službu.

U ověřovacích tokenů se dá vyměňovat jedna služba i klíč předplatného s více službami. Ověřovací tokeny jsou platné po dobu 10 minut.

Tokeny ověřování jsou součástí žádosti jako `Authorization` záhlaví. Zadaná hodnota tokenu musí předcházet, například `Bearer` : `Bearer YOUR_AUTH_TOKEN` .

### <a name="sample-requests"></a>Ukázkové požadavky

Pomocí této adresy URL můžete vyměňovat klíč předplatného pro ověřovací token: `https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken` .

```cURL
curl -v -X POST \
"https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
-H "Content-type: application/x-www-form-urlencoded" \
-H "Content-length: 0" \
-H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

Tyto oblasti s více službami podporují výměnu tokenů:

- `australiaeast`
- `brazilsouth`
- `canadacentral`
- `centralindia`
- `eastasia`
- `eastus`
- `japaneast`
- `northeurope`
- `southcentralus`
- `southeastasia`
- `uksouth`
- `westcentralus`
- `westeurope`
- `westus`
- `westus2`

Po získání ověřovacího tokenu ho budete muset předat do každého požadavku jako `Authorization` hlavičku. Toto je ukázkové volání služby Translator:

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
