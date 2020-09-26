---
title: Klientské knihovny a rozhraní REST API pro komunikační služby Azure
titleSuffix: An Azure Communication Services concept document
description: Přečtěte si další informace o knihovnách klienta služby Azure Communication Services a rozhraní REST API.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/18/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 2d5637be547b28c231ef757b5dd4355692dab88f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318819"
---
# <a name="client-libraries-and-rest-apis"></a>Klientské knihovny a rozhraní REST API

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Funkce služby Azure Communication Services jsou koncepčně rozdělené do šesti oblastí. Některé oblasti mají plně Open Source klientské knihovny. Volající Klientská knihovna používá proprietární síťová rozhraní a je aktuálně uzavřená a knihovna konverzace obsahuje uzavřenou závislost na zdroji. Odkazy na všechny sady SDK a ukázky se udržují v [úložišti GitHub služby Azure Communication Services](https://github.com/Azure/communication).

## <a name="client-libraries"></a>Klientské knihovny

| Sestavení               | Protokoly             |Otevřít vs. uzavřený zdroj| Obory názvů                          | Možnosti                                                      |
| ---------------------- | --------------------- | ---|-------------------------- | --------------------------------------------------------------------------- |
| Azure Resource Manager | REST | Otevřít            | Azure. ResourceManager. Communication | Zřizování a Správa prostředků komunikačních služeb             |
| Společné                 | REST | Otevřít               | Azure. Communication. Common          | Poskytuje základní typy pro jiné klientské knihovny. |
| Správa         | REST | Otevřít               | Azure. Communications. Administration  | Spravujte uživatele, přístupové tokeny a telefonní čísla, přidělte STUN a zapněte servery. |
| Chat                   | REST s proprietárními signály | Otevřít s zavřeným balíčkem zdrojového signálu    | Azure. Communication. chat            | Přidání textu založeného na textu v reálném čase do vašich aplikací  |
| SMS                    | REST | Otevřít              | Azure. Communication. SMS             | Odesílání a příjem zpráv SMS |
| Videohovory                | Proprietární přenos | Uzavřená |Azure. Communication. Calling         | Využití hlasu, videa, sdílení obrazovky a dalších možností komunikace dat v reálném čase          |

### <a name="client-library-language-support"></a>Podpora jazyka klientské knihovny

Pokyny k dostupnosti a časové osy pro jednotlivé balíčky klientských knihoven jsou podrobně popsané níže. [Průvodce Azure](https://azure.microsoft.com/updates/) obsahuje další informace o nadcházejících funkcích.

| Oblast           | JavaScript | .NET | Python | Java | SWIFT nebo obj-C | Java (Android) | Další                          |
| -------------- | ---------- | ---- | ------ | ---- | -------------- | -------------- | ------------------------------ |
| Azure Resource Manager | ✔️         | ✔️    | ✔️      | -    | -              | *Zatím nepodporováno*  | Rozhraní příkazového řádku Azure *ještě není podporované* |
| Společné         | ✔️         | ✔️    | -      | ✔️   | ✔️            | ✔️             | -                              |
| Správa | ✔️         | ✔️    | ✔️      | ✔️   | -              | -              | Rozhraní příkazového řádku                            |
| Chat           | ✔️         | ✔️    | ✔️      | ✔️   | *Zatím nepodporováno*  | *Zatím nepodporováno*  | -                              |
| SMS            | ✔️         | ✔️    | ✔️      | ✔️   | -              | -              | -                              |
| Videohovory        | ✔️         | -      | -      | -     | (Obj-C) ✔️     | ✔️            | -                              |

### <a name="client-library-public-repository-support"></a>Podpora veřejného úložiště klientské knihovny

Komunikační služby publikují sestavené knihovny v několika veřejných úložištích.

| Jazyk       | Optimalizováno pro...                       | Balení |
| -------------- | ------------------------------------ | --------- |
| .NET           | Různé platformy                       | NuGet     |
| Python         | Servery se systémem Windows & Linux              | PyPI      |
| Java (J2EE)    | JVM na serverech se systémem Windows nebo Linux      | Maven     |
| Java (Android) | Klientské aplikace pro Android          | Maven     |
| JavaScript     | Klientské aplikace a uzel prohlížeče | NPM       |

## <a name="rest-apis"></a>Rozhraní REST API

Rozhraní API komunikačních služeb jsou zdokumentována spolu s dalšími rozhraními REST API Azure v [docs.Microsoft.com](https://docs.microsoft.com/rest/api/azure/). V této dokumentaci se dozvíte, jak strukturovat zprávy HTTP a pokyny k používání služby post. Tato dokumentace je také k dispozici ve formátu Swagger na [GitHubu](https://github.com/Azure/azure-rest-api-specs).

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

## <a name="api-stability-expectations"></a>Očekávání stability rozhraní API 

> [!IMPORTANT]
> V této části najdete pokyny pro rozhraní REST API a klientské knihovny označené jako **stabilní**. Rozhraní API označená předběžná verze, verze Preview nebo beta můžou být **beze**změny nebo zastaralá. Služby Azure Communication Services jsou momentálně ve **verzi Public Preview**a rozhraní API jsou označená jako.

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
