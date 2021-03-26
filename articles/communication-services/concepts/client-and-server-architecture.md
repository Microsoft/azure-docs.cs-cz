---
title: Klientská a serverová architektura
titleSuffix: An Azure Communication Services concept document
description: Seznamte se s architekturou komunikačních služeb.
author: mikben
manager: mikben
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: f52d907c36c7900d5f5eada3ca4a7328e42f9729
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110844"
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

Sady SDK pro služby Azure Communication Services vyžadují `user access tokens` zabezpečený přístup k prostředkům komunikačních služeb. `User access tokens` by měla být vygenerována a spravována důvěryhodnou službou z důvodu citlivé povahy tokenu a připojovacího řetězce potřebného pro jejich vygenerování. Neúspěšné Správa přístupových tokenů může mít za následek další poplatky z důvodu zneužití prostředků. Důrazně se doporučuje využít důvěryhodnou službu pro správu uživatelů. Důvěryhodná služba vygeneruje tokeny a předá je zpátky klientovi pomocí správného šifrování. Ukázkový tok architektury najdete níže:

:::image type="content" source="../media/scenarios/archdiagram-access.png" alt-text="Diagram znázorňující architekturu tokenu uživatelského přístupu":::

Další informace najdete v článku [osvědčené postupy pro správu identit](../../security/fundamentals/identity-management-best-practices.md) .

## <a name="browser-communication"></a>Komunikace s prohlížečem

Sady SDK komunikace v Azure umožňují webové aplikace s bohatou interakcí textu, hlasu a videa. Aplikace přímo spolupracuje s komunikačními službami Azure prostřednictvím sady SDK pro přístup k rovině dat a k doručování textu, hlasové a video komunikace v reálném čase. Ukázkový tok architektury najdete níže:

:::image type="content" source="../media/scenarios/archdiagram-browser.png" alt-text="Diagram znázorňující architekturu prohlížeče pro komunikační služby.":::

## <a name="native-app-communication"></a>Komunikace nativní aplikace

Mnoho scénářů se nejlépe obsluhuje pomocí nativních aplikací. Komunikační služby Azure podporují komunikaci mezi aplikacemi a aplikacemi proti aplikacím.  Při sestavování nativního prostředí aplikace budou mít nabízená oznámení uživatelům přijímat volání i v případě, že aplikace není spuštěná. Služba Azure Communication Services usnadňuje integraci nabízených oznámení do Google Firebase, Apple Push Notification Service a nabízených oznámení Windows. Ukázkový tok architektury najdete níže:

:::image type="content" source="../media/scenarios/archdiagram-app.png" alt-text="Diagram znázorňující architekturu komunikačních služeb pro komunikaci nativní aplikace":::

## <a name="voice-and-sms-over-the-public-switched-telephony-network-pstn"></a>Hlasové a SMS přes síť PSTN (Public Switched Telephony Network)

Komunikace přes telefonní systém může výrazně zvýšit dosah vaší aplikace. Pro podporu hlasových scénářů a scénářů SMS vám komunikační služby Azure pomáhají [získat telefonní čísla](../quickstarts/telephony-sms/get-phone-number.md) přímo z Azure Portal nebo pomocí rozhraní REST API a sad SDK. Jakmile budou telefonní čísla získaná, dají se použít k oslovení zákazníků, kteří používají volání do veřejné sítě i SMS v obou scénářích příchozích i odchozích připojení. Ukázkový tok architektury najdete níže:

> [!Note]
> Během veřejné verze Preview jsou zřizování telefonních čísel USA dostupná zákazníkům s fakturačními adresami umístěnými v USA a Kanadě.

:::image type="content" source="../media/scenarios/archdiagram-pstn.png" alt-text="Diagram znázorňující architekturu PSTN služby Communications Services":::

Další informace o telefonních číslech PSTN najdete v tématu [typy telefonních](../concepts/telephony-sms/plan-solution.md) čísel.

## <a name="humans-communicating-with-bots-and-other-services"></a>Lidé, kteří komunikují s roboty a dalšími službami

Komunikační služby Azure podporují komunikaci člověkem se systémem i přes textové a hlasové kanály se službami, které přímo přistupují k rovině dat služby Azure Communications. Například můžete mít odpověď na robota příchozí telefonní hovory nebo se zúčastnit webové konverzace. Komunikační služby Azure poskytují sady SDK, které umožňují tyto scénáře volání a chatování. Ukázkový tok architektury najdete níže:

:::image type="content" source="../media/scenarios/archdiagram-bot.png" alt-text="Diagram znázorňující architekturu služby Communications Services bot":::

## <a name="networking"></a>Sítě

Můžete chtít si vyměňovat libovolná data mezi uživateli, například synchronizovat sdílenou hybridní realitu nebo herní prostředí. Rovina dat v reálném čase, která se používá pro komunikaci textu, hlasu a videa, je k dispozici přímo dvěma způsoby:

- **Volání sady SDK** – zařízení ve volání mají přístup k rozhraním API pro odesílání a příjem dat prostřednictvím kanálu volání. Toto je nejjednodušší způsob, jak přidat datovou komunikaci do existující interakce.
- **STUN/zapněte** – služba Azure Communications umožňuje STUN a zpřístupňuje služby, které jsou pro vás k dispozici. Díky tomu můžete vytvořit vysoce přizpůsobenou přenosovou vrstvu nad těmito standardizovanými primitivy. Můžete vytvářet vlastní klienty vyhovující standardům nebo používat open source knihovny, jako je [WinRTC](https://github.com/microsoft/winrtc).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytváření tokenů přístupu uživatele](../quickstarts/access-tokens.md)

Další informace najdete v následujících článcích:

- Další informace o [ověřování](../concepts/authentication.md)
- Další informace o [typech telefonních čísel](../concepts/telephony-sms/plan-solution.md)

- [Přidání chatu do aplikace](../quickstarts/chat/get-started.md)
- [Přidání hlasových hovorů do aplikace](../quickstarts/voice-video-calling/getting-started-with-calling.md)
