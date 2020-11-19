---
title: Klientská a serverová architektura
titleSuffix: An Azure Communication Services concept document
description: Seznamte se s architekturou komunikačních služeb.
author: mikben
manager: mikben
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 8b63ce25d6839e8672027240099859dc7aad96c5
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886069"
---
# <a name="client-and-server-architecture"></a>Architektura klienta a serveru

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

<!--
> [!WARNING]
> This document is under construction and needs the following items to be addressed: 
> - Need to add security best practices for token management here
> - Reference docs:
> - https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/create-a-token-object
> - https://docs.microsoft.com/azure/aks/operator-best-practices-identity
> - https://docs.microsoft.com/cloud-app-security/api-tokens?view=gestures-1.0-->

Každá aplikace služby Azure Communication Services bude mít **klientské aplikace** , které používají **služby** , k usnadnění připojení mezi osobami. Tato stránka znázorňuje běžné prvky architektury v nejrůznějších scénářích.

## <a name="user-access-management"></a>Správa přístupu uživatelů

Klientské knihovny služby Azure Communication Services vyžadují `user access tokens` zabezpečený přístup k prostředkům komunikačních služeb. `User access tokens` by měla být vygenerována a spravována důvěryhodnou službou z důvodu citlivé povahy tokenu a připojovacího řetězce potřebného pro jejich vygenerování. Neúspěšné Správa přístupových tokenů může mít za následek další poplatky z důvodu zneužití prostředků. Důrazně se doporučuje využít důvěryhodnou službu pro správu uživatelů. Důvěryhodná služba vygeneruje tokeny a předá je zpátky klientovi pomocí správného šifrování. Ukázkový tok architektury najdete níže:

:::image type="content" source="../media/scenarios/archdiagram-access.png" alt-text="Diagram znázorňující architekturu tokenu uživatelského přístupu":::

Další informace najdete v článku [osvědčené postupy pro správu identit](../../security/fundamentals/identity-management-best-practices.md) .

## <a name="browser-communication"></a>Komunikace s prohlížečem

Klientské knihovny JavaScriptu pro komunikaci Azure umožňují webové aplikace s bohatou interakcí textu, hlasu a videa. Aplikace přímo spolupracuje s komunikačními službami Azure prostřednictvím klientské knihovny pro přístup k rovině dat a k doručování textu, hlasu a videa v reálném čase. Ukázkový tok architektury najdete níže:

:::image type="content" source="../media/scenarios/archdiagram-browser.png" alt-text="Diagram znázorňující architekturu prohlížeče pro komunikační služby.":::

## <a name="native-app-communication"></a>Komunikace nativní aplikace

Mnoho scénářů se nejlépe obsluhuje pomocí nativních aplikací. Komunikační služby Azure podporují komunikaci mezi aplikacemi a aplikacemi proti aplikacím.  Při sestavování nativního prostředí aplikace budou mít nabízená oznámení uživatelům přijímat volání i v případě, že aplikace není spuštěná. Služba Azure Communication Services usnadňuje integraci nabízených oznámení do Google Firebase, Apple Push Notification Service a nabízených oznámení Windows. Ukázkový tok architektury najdete níže:

:::image type="content" source="../media/scenarios/archdiagram-app.png" alt-text="Diagram znázorňující architekturu komunikačních služeb pro komunikaci nativní aplikace":::

## <a name="voice-and-sms-over-the-public-switched-telephony-network-pstn"></a>Hlasové a SMS přes síť PSTN (Public Switched Telephony Network)

Komunikace přes telefonní systém může výrazně zvýšit dosah vaší aplikace. Pro podporu hlasových scénářů a scénářů SMS vám komunikační služby Azure pomáhají [získat telefonní čísla](../quickstarts/telephony-sms/get-phone-number.md) přímo z Azure Portal nebo pomocí rozhraní REST API a klientských knihoven. Jakmile budou telefonní čísla získaná, dají se použít k oslovení zákazníků, kteří používají volání do veřejné sítě i SMS v obou scénářích příchozích i odchozích připojení. Ukázkový tok architektury najdete níže:

> [!Note]
> Během veřejné verze Preview jsou zřizování telefonních čísel USA dostupná zákazníkům s fakturačními adresami umístěnými v USA a Kanadě. 

:::image type="content" source="../media/scenarios/archdiagram-pstn.png" alt-text="Diagram znázorňující architekturu PSTN služby Communications Services":::

Další informace o řešeních PSTN a SMS najdete v tématu [Plánování sítě PSTN a řešení SMS](../concepts/telephony-sms/plan-solution.md) .

## <a name="humans-communicating-with-bots-and-other-services"></a>Lidé, kteří komunikují s roboty a dalšími službami

Komunikační služby Azure podporují komunikaci člověkem se systémem i přes textové a hlasové kanály se službami, které přímo přistupují k rovině dat služby Azure Communications. Například můžete mít odpověď na robota příchozí telefonní hovory nebo se zúčastnit webové konverzace. Komunikační služby Azure poskytují klientské knihovny, které umožňují tyto scénáře volání a chatování. Ukázkový tok architektury najdete níže:

:::image type="content" source="../media/scenarios/archdiagram-bot.png" alt-text="Diagram znázorňující architekturu služby Communications Services bot":::

## <a name="networking"></a>Sítě

Můžete chtít si vyměňovat libovolná data mezi uživateli, například synchronizovat sdílenou hybridní realitu nebo herní prostředí. Rovina dat v reálném čase, která se používá pro komunikaci textu, hlasu a videa, je k dispozici přímo dvěma způsoby:

- **Volání klientské knihovny** – zařízení ve volání mají přístup k rozhraním API pro odesílání a příjem dat prostřednictvím kanálu volání. Toto je nejjednodušší způsob, jak přidat datovou komunikaci do existující interakce.
- **STUN/zapněte** – služba Azure Communications umožňuje STUN a zpřístupňuje služby, které jsou pro vás k dispozici. Díky tomu můžete vytvořit vysoce přizpůsobenou přenosovou vrstvu nad těmito standardizovanými primitivy. Můžete vytvářet vlastní klienty vyhovující standardům nebo používat open source knihovny, jako je [WinRTC](https://github.com/microsoft/winrtc).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytváření tokenů přístupu uživatele](../quickstarts/access-tokens.md)

Další informace najdete v následujících článcích:

- Další informace o [ověřování](../concepts/authentication.md)
- Další informace o [řešeních PSTN a SMS](../concepts/telephony-sms/plan-solution.md)

- [Přidání chatu do aplikace](../quickstarts/chat/get-started.md)
- [Přidání hlasových hovorů do aplikace](../quickstarts/voice-video-calling/getting-started-with-calling.md)