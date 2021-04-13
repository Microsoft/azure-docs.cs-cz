---
title: Sady SDK a rozhraní REST API pro komunikační služby Azure
titleSuffix: An Azure Communication Services concept document
description: Přečtěte si další informace o sadách Azure Communication Services SDK a rozhraní REST API.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/25/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: d71720b446baa8e2e5e15c407a69d9ff884004b2
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307686"
---
# <a name="sdks-and-rest-apis"></a>Sady SDK a rozhraní REST API

Funkce služby Azure Communication Services jsou koncepčně rozdělené do šesti oblastí. Většina oblastí má plně otevřené sady SDK, které jsou naprogramovány na publikovaná rozhraní REST API, která můžete použít přímo přes Internet. Volající sada SDK používá proprietární síťová rozhraní a je aktuálně zavřena. Ukázky a další technické podrobnosti pro sady SDK jsou publikované v [úložišti GitHub služby Azure Communication Services](https://github.com/Azure/communication).

## <a name="rest-apis"></a>Rozhraní REST API
Rozhraní API komunikačních služeb jsou zdokumentována spolu s dalšími rozhraními REST API Azure v [docs.Microsoft.com](/rest/api/azure/). V této dokumentaci se dozvíte, jak strukturovat zprávy HTTP a pokyny k používání služby post. Tato dokumentace je také k dispozici ve formátu Swagger na [GitHubu](https://github.com/Azure/azure-rest-api-specs).


## <a name="sdks"></a>Sady SDK

| Sestavení | Obory názvů| Protokoly | Možnosti |
|------------------------|-------------------------------------|---------------------------------|--------------------------------------------------------------------------------------------|
| Azure Resource Manager | Azure. ResourceManager. Communication | [REST](https://docs.microsoft.com/rest/api/communication/communicationservice)| Zřizování a Správa prostředků komunikačních služeb|
| Společné | Azure. Communication. Common| REST | Poskytuje základní typy pro jiné sady SDK. |
| Identita | Azure. Communication. identity| [REST](https://docs.microsoft.com/rest/api/communication/communicationidentity)| Správa uživatelů, přístupové tokeny|
| Telefonní čísla _(beta verze)_| Azure. Communication. PhoneNumbers| [REST](https://docs.microsoft.com/rest/api/communication/phonenumberadministration)| Získat a spravovat telefonní čísla |
| Chat | Azure. Communication. chat| [REST](https://docs.microsoft.com/rest/api/communication/) s proprietárními signály | Přidání textu založeného na textu v reálném čase do vašich aplikací |
| SMS| Azure. Communication. SMS | [REST](https://docs.microsoft.com/rest/api/communication/sms)| Odesílání a příjem zpráv SMS|
| Videohovory| Azure. Communication. Calling | Proprietární přenos | Použití hlasu, videa, sdílení obrazovky a dalších možností komunikace dat v reálném čase |

Sady SDK Azure Resource Manager, identity a SMS jsou zaměřené na integraci služeb a v mnoha případech dochází k problémům se zabezpečením při integraci těchto funkcí do aplikací koncových uživatelů. Společné a chatovací sady SDK jsou vhodné pro služby a klientské aplikace. Volající sada SDK je navržena pro klientské aplikace. Sada SDK zaměřená na scénáře služby je ve vývoji.


### <a name="languages-and-publishing-locations"></a>Jazyky a umístění pro publikování

Umístění pro publikování jednotlivých balíčků sady SDK jsou podrobněji popsána níže.

| Plošný           | JavaScript | .NET | Python | Java SE | iOS | Android | Jiné                          |
| -------------- | ---------- | ---- | ------ | ---- | -------------- | -------------- | ------------------------------ |
| Azure Resource Manager | -         | [NuGet](https://www.nuget.org/packages/Azure.ResourceManager.Communication)    |   [PyPi](https://pypi.org/project/azure-mgmt-communication/)    |  -  | -              | -  | [Přejít přes GitHub](https://github.com/Azure/azure-sdk-for-go/releases/tag/v46.3.0) |
| Společné         | [npm](https://www.npmjs.com/package/@azure/communication-common)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Common/)    | –      | [Maven](https://search.maven.org/search?q=a:azure-communication-common)   | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)            | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-common)             | -                              |
| Identita | [npm](https://www.npmjs.com/package/@azure/communication-identity)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Identity)    | [PyPi](https://pypi.org/project/azure-communication-identity/)      | [Maven](https://search.maven.org/search?q=a:azure-communication-identity)   | -              | -              | -                            |
| Telefonní čísla | [npm](https://www.npmjs.com/package/@azure/communication-phone-numbers)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.PhoneNumbers)    | [PyPi](https://pypi.org/project/azure-communication-phonenumbers/)      | [Maven](https://search.maven.org/search?q=a:azure-communication-phonenumbers)   | -              | -              | -                            |
| Chat           | [npm](https://www.npmjs.com/package/@azure/communication-chat)        | [NuGet](https://www.nuget.org/packages/Azure.Communication.Chat)     | [PyPi](https://pypi.org/project/azure-communication-chat/)     | [Maven](https://search.maven.org/search?q=a:azure-communication-chat)   | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)  | [Maven](https://search.maven.org/search?q=a:azure-communication-chat)   | -                              |
| SMS            | [npm](https://www.npmjs.com/package/@azure/communication-sms)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Sms)    | [PyPi](https://pypi.org/project/azure-communication-sms/)       | [Maven](https://search.maven.org/artifact/com.azure/azure-communication-sms)   | -              | -              | -                              |
| Videohovory        | [npm](https://www.npmjs.com/package/@azure/communication-calling)         | -      | -      | -     | [GitHub](https://github.com/Azure/Communication/releases)     | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-calling/)            | -                              |
| Referenční dokumentace     | [doc](https://azure.github.io/azure-sdk-for-js/communication.html)         | [doc](https://azure.github.io/azure-sdk-for-net/communication.html)      | -      | [doc](http://azure.github.io/azure-sdk-for-java/communication.html)     | [doc](/objectivec/communication-services/calling/)      | [doc](/java/api/com.azure.android.communication.calling)            | -                              |


## <a name="rest-api-throttles"></a>Omezení REST API
Určitá rozhraní REST API a odpovídající metody sady SDK mají omezení, která byste měli mít na vědomí. Překročení těchto omezení způsobí aktivaci  `429 - Too Many Requests` chybové odpovědi. Tato omezení se dají zvýšit prostřednictvím [žádosti na podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

| Rozhraní API                                                                                                                          | Omezení            |
|------------------------------------------------------------------------------------------------------------------------------|---------------------|
| [Všechna vyhledávání rozhraní API pro plán telefonního čísla](https://docs.microsoft.com/rest/api/communication/phonenumberadministration)         | 4 požadavky za den      |
| [Koupit plán telefonního čísla](https://docs.microsoft.com/rest/api/communication/phonenumberadministration/purchasesearch) | 1 nákup měsíčně  |
| [Odeslat SMS](https://docs.microsoft.com/rest/api/communication/sms/send)                                                       | 200 požadavků za minutu |


## <a name="sdk-platform-support-details"></a>Podrobnosti podpory platformy SDK

### <a name="ios-and-android"></a>iOS a Android 

- Služba Communications Services pro iOS sady SDK Target verze 13 + a Xcode 11 +.
- Android Java SDK cílové rozhraní Android API úrovně 21 + a Android Studio 4.0 +

### <a name="net"></a>.NET 

S výjimkou volání, cílové balíčky služby Communications .NET Standard 2,0, které podporují platformy uvedené níže.

Podpora prostřednictvím .NET Framework 4.6.1
- Windows 10, 8,1, 8 a 7
- Windows Server 2012 R2, 2012 a 2008 R2 SP1

Podpora prostřednictvím .NET Core 2,0:
- Windows 10 (1607 +), 7 SP1 +, 8,1
- Windows Server 2008 R2 SP1 +
- Max OS X 10.12 +
- Více verzí/distribucí pro Linux
- 10.0.16299 UWP (RS3), září 2017
- Unity 2018,1
- Mono 5,4
- Xamarin iOS 10,14
- Xamarin Mac 3,8

## <a name="api-stability-expectations"></a>Očekávání stability rozhraní API

> [!IMPORTANT]
> V této části najdete pokyny pro rozhraní REST API a sady SDK s označením **stabilní**. Rozhraní API označená předběžná verze, verze Preview nebo beta můžou být **beze** změny nebo zastaralá.

V budoucnu můžeme vyřadit verze sad SDK pro komunikaci a můžeme začlenit zásadní změny našich rozhraní REST API a vydaných sad SDK. Komunikační služby Azure budou *obecně* splňovat dvě zásady podpory pro vyřazení verzí služby:

- Před změnou kódu z důvodu změny rozhraní komunikačních služeb budete upozorněni alespoň na tři roky. Všechna dokumentované rozhraní REST API a rozhraní API sady SDK obecně využívají před vyřazením rozhraní alespoň tři roky upozornění.
- Před aktualizací sestavení sady SDK na nejnovější podverzi budete upozorněni alespoň na jeden rok. Tyto požadované aktualizace by neměly vyžadovat změny kódu, protože jsou ve stejné hlavní verzi. To platí zejména pro volající a konverzační knihovny, které mají komponenty v reálném čase, které často vyžadují aktualizace zabezpečení a výkonu. Důrazně doporučujeme, abyste si aktualizovali sady SDK komunikačních služeb.

### <a name="api-and-sdk-decommissioning-examples"></a>Příklady vyřazení rozhraní API a sady SDK z provozu

**V24i jste do své aplikace integraci verze REST API serveru SMS. Komunikační verze Azure v25.**

Před tím, než tato rozhraní API přestanou fungovat a budou nuceně aktualizovat na V25, zobrazí se tři roky. Tato aktualizace může vyžadovat změnu kódu.

**Do své aplikace jste zapnuli verzi v 2.02 volání sady SDK. Azure Communications releases v 2.05.**

Možná budete muset aktualizovat na verzi v 2.05 volání sady SDK do 12 měsíců od vydání verze v 2.05. Mělo by to být jednoduché nahrazení artefaktu bez nutnosti změny kódu, protože verze v 2.05 je v hlavní verzi v2 a nemá žádné závažné změny.

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících přehledech SDK:

- [Přehled volání sady SDK](../concepts/voice-video-calling/calling-sdk-features.md)
- [Přehled sady Chat SDK](../concepts/chat/sdk-features.md)
- [Přehled sady SMS SDK](../concepts/telephony-sms/sdk-features.md)

Začínáme s komunikačními službami Azure:

- [Vytvoření prostředků komunikace Azure](../quickstarts/create-communication-resource.md)
- Generovat [tokeny přístupu uživatele](../quickstarts/access-tokens.md)
