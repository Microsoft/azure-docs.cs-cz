---
title: Klientské knihovny a rozhraní REST API pro komunikační služby Azure
titleSuffix: An Azure Communication Services concept document
description: Přečtěte si další informace o knihovnách klienta služby Azure Communication Services a rozhraní REST API.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 01a5f4a947f0b89b5881eddb3c743b9a9b184b19
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103495636"
---
# <a name="client-libraries-and-rest-apis"></a>Klientské knihovny a rozhraní REST API

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]


Funkce služby Azure Communication Services jsou koncepčně rozdělené do šesti oblastí. Některé oblasti mají plně Open Source klientské knihovny. Volající Klientská knihovna používá proprietární síťová rozhraní a je aktuálně uzavřená a knihovna konverzace obsahuje uzavřenou závislost na zdroji. Ukázky a další technické podrobnosti pro klientské knihovny jsou publikované v [úložišti GitHub služby Azure Communication Services](https://github.com/Azure/communication).

## <a name="client-libraries"></a>Klientské knihovny

| Sestavení               | Protokoly             |Otevřít vs. uzavřený zdroj| Obory názvů                          | Možnosti                                                      |
| ---------------------- | --------------------- | ---|-------------------------- | --------------------------------------------------------------------------- |
| Azure Resource Manager | REST | Otevřít            | Azure. ResourceManager. Communication | Zřizování a Správa prostředků komunikačních služeb             |
| Společné                 | REST | Otevřít               | Azure. Communication. Common          | Poskytuje základní typy pro jiné klientské knihovny. |
| Identita         | REST | Otevřít               | Azure. Communication. identity  | Správa uživatelů, přístupové tokeny |
| Telefonní čísla         | REST | Otevřít               | Azure. Communication. PhoneNumbers  | Správa telefonních čísel |
| Chat                   | REST s proprietárními signály | Otevřít pomocí zavřeného balíčku zdrojového signálu    | Azure. Communication. chat            | Přidání textu založeného na textu v reálném čase do vašich aplikací  |
| SMS                    | REST | Otevřít              | Azure. Communication. SMS             | Odesílání a příjem zpráv SMS |
| Videohovory                | Proprietární přenos | Uzavřeno |Azure. Communication. Calling         | Využití hlasu, videa, sdílení obrazovky a dalších možností komunikace dat v reálném čase          |

Všimněte si, že klientské knihovny Azure Resource Manager, identity a serveru SMS jsou zaměřené na integraci služeb a v mnoha případech dochází k problémům se zabezpečením při integraci těchto funkcí do aplikací koncových uživatelů. Společné a chatovací klientské knihovny jsou vhodné pro služby a klientské aplikace. Volající Klientská knihovna je navržena pro klientské aplikace. Klientská knihovna zaměřená na scénáře služby je ve vývoji.

### <a name="languages-and-publishing-locations"></a>Jazyky a umístění pro publikování

Umístění pro publikování pro jednotlivé balíčky klientských knihoven jsou podrobně popsány níže.

| Plošný           | JavaScript | .NET | Python | Java SE | iOS | Android | Jiné                          |
| -------------- | ---------- | ---- | ------ | ---- | -------------- | -------------- | ------------------------------ |
| Azure Resource Manager | -         | [NuGet](https://www.nuget.org/packages/Azure.ResourceManager.Communication)    |   [PyPi](https://pypi.org/project/azure-mgmt-communication/)    |  -  | -              | -  | [Přejít přes GitHub](https://github.com/Azure/azure-sdk-for-go/releases/tag/v46.3.0) |
| Společné         | [npm](https://www.npmjs.com/package/@azure/communication-common)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Common/)    | –      | [Maven](https://search.maven.org/search?q=a:azure-communication-common)   | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)            | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-common)             | -                              |
| Identita | [npm](https://www.npmjs.com/package/@azure/communication-identity)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Identity)    | [PyPi](https://pypi.org/project/azure-communication-identity/)      | [Maven](https://search.maven.org/search?q=a:azure-communication-identity)   | -              | -              | -                            |
| Telefonní čísla | [npm](https://www.npmjs.com/package/@azure/communication-phone-numbers)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.PhoneNumbers)    | [PyPi](https://pypi.org/project/azure-communication-phonenumbers/)      | [Maven](https://search.maven.org/search?q=a:azure-communication-phonenumbers)   | -              | -              | -                            |
| Chat           | [npm](https://www.npmjs.com/package/@azure/communication-chat)        | [NuGet](https://www.nuget.org/packages/Azure.Communication.Chat)     | [PyPi](https://pypi.org/project/azure-communication-chat/)     | [Maven](https://search.maven.org/search?q=a:azure-communication-chat)   | [GitHub](https://github.com/Azure/azure-sdk-for-ios/releases)  | [Maven](https://search.maven.org/search?q=a:azure-communication-chat)   | -                              |
| SMS            | [npm](https://www.npmjs.com/package/@azure/communication-sms)         | [NuGet](https://www.nuget.org/packages/Azure.Communication.Sms)    | [PyPi](https://pypi.org/project/azure-communication-sms/)       | [Maven](https://search.maven.org/artifact/com.azure/azure-communication-sms)   | -              | -              | -                              |
| Videohovory        | [npm](https://www.npmjs.com/package/@azure/communication-calling)         | -      | -      | -     | [GitHub](https://github.com/Azure/Communication/releases)     | [Maven](https://search.maven.org/artifact/com.azure.android/azure-communication-calling/)            | -                              |
| Referenční dokumentace     | [doc](https://azure.github.io/azure-sdk-for-js/communication.html)         | [doc](https://azure.github.io/azure-sdk-for-net/communication.html)      | -      | [doc](http://azure.github.io/azure-sdk-for-java/communication.html)     | [doc](/objectivec/communication-services/calling/)      | [doc](/java/api/com.azure.communication.calling)            | -                              |

## <a name="rest-apis"></a>Rozhraní REST API

Rozhraní API komunikačních služeb jsou zdokumentována spolu s dalšími rozhraními REST API Azure v [docs.Microsoft.com](/rest/api/azure/). V této dokumentaci se dozvíte, jak strukturovat zprávy HTTP a pokyny k používání služby post. Tato dokumentace je také k dispozici ve formátu Swagger na [GitHubu](https://github.com/Azure/azure-rest-api-specs).

## <a name="additional-support-details"></a>Další podrobnosti o podpoře

### <a name="ios-and-android-support-details"></a>Podrobnosti o podpoře pro iOS a Android

- Služba Communications Client Librarys pro iOS má cílovou verzi verze 13 + a Xcode 11.
- Klientské knihovny pro Android Java – úroveň rozhraní Android API 21 + a Android Studio 4.0 +

### <a name="net-support-details"></a>Podrobnosti o podpoře .NET

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

## <a name="calling-client-library-timeouts"></a>Volání klientských časových limitů klientské knihovny

Pro komunikační služby, které volají klientské knihovny, platí následující časový limit:

| Akce           | Časový limit v sekundách |
| -------------- | ---------- |
| Znovu připojit/odebrat účastníka | 120 |
| Přidání nebo odebrání nové modální metody ze volání (spuštění/zastavení videa nebo sdílení obrazovky) | 40 |
| Časový limit operace přenosu volání | 60 |
| časový limit pro zahájení volání 1:1 | 85 |
| Časový limit pro vytvoření volání skupiny | 85 |
| Časový limit pro vytvoření volání veřejné telefonní sítě | 115 |
| Zvýšit úroveň volání 1:1 na časový limit volání skupiny | 115 |


## <a name="api-stability-expectations"></a>Očekávání stability rozhraní API

> [!IMPORTANT]
> V této části najdete pokyny pro rozhraní REST API a klientské knihovny označené jako **stabilní**. Rozhraní API označená předběžná verze, verze Preview nebo beta můžou být **beze** změny nebo zastaralá.

V budoucnu můžeme vyřadit verze klientských knihoven komunikačních služeb a můžeme začlenit zásadní změny v našich rozhraních REST API a vydané klientské knihovny. Komunikační služby Azure budou *obecně* splňovat dvě zásady podpory pro vyřazení verzí služby:

- Před změnou kódu z důvodu změny rozhraní komunikačních služeb budete upozorněni alespoň na tři roky. Všechna dokumentované rozhraní REST API a rozhraní API klientské knihovny mají při vyřazení rozhraní do provozu aspoň tři roky upozornění.
- Před aktualizací sestavení klientské knihovny na nejnovější podverzi budete upozorněni alespoň na jeden rok. Tyto požadované aktualizace by neměly vyžadovat změny kódu, protože jsou ve stejné hlavní verzi. To platí zejména pro volající a konverzační knihovny, které mají komponenty v reálném čase, které často vyžadují aktualizace zabezpečení a výkonu. Důrazně doporučujeme, abyste si aktualizovali klientské knihovny komunikačních služeb.

### <a name="api-and-client-library-decommissioning-examples"></a>Příklady vyřazení rozhraní API a klientské knihovny z provozu

**V24i jste do své aplikace integraci verze REST API serveru SMS. Komunikační verze Azure v25.**

Před tím, než tato rozhraní API přestanou fungovat a budou nuceně aktualizovat na V25, zobrazí se upozornění 3 roky. Tato aktualizace může vyžadovat změnu kódu.

**Do své aplikace jste zapnuli verzi v 2.02 volání klientské knihovny. Azure Communications releases v 2.05.**

Možná budete muset aktualizovat na verzi v 2.05 volání klientské knihovny do 12 měsíců od vydání verze v 2.05. Mělo by to být jednoduché nahrazení artefaktu bez nutnosti změny kódu, protože verze v 2.05 je v hlavní verzi v2 a nemá žádné závažné změny.

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících přehledech klientské knihovny:

- [Přehled volání klientské knihovny](../concepts/voice-video-calling/calling-sdk-features.md)
- [Přehled klientské knihovny pro chat](../concepts/chat/sdk-features.md)
- [Přehled klientské knihovny SMS](../concepts/telephony-sms/sdk-features.md)

Začínáme s komunikačními službami Azure:

- [Vytvoření prostředků komunikace Azure](../quickstarts/create-communication-resource.md)
- Generovat [tokeny přístupu uživatele](../quickstarts/access-tokens.md)
