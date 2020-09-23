---
title: Plánování služby Azure Communication Services pro telefon a řešení SMS
titleSuffix: An Azure Communication Services concept document
description: Zjistěte, jak efektivně naplánovat používání telefonních čísel a telefonního subsystému.
author: stkozak
manager: rampras
services: azure-communication-services
ms.author: stkozak
ms.date: 06/23/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 39f88ab8b735438f60d8e20513ea5cbda43d41ee
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90946854"
---
# <a name="plan-your-telephony-and-sms-solution"></a>Plánování telefonního subsystému a řešení SMS

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Tento dokument popisuje různé plány a číselné typy telefonního subsystému, které nabízí komunikační služby Azure. Provedeme vás prostřednictvím rozhodovacích toků, které vám pomůžou vybrat poskytovatele hlasových služeb, typy telefonních čísel, plány a možnosti, které jsou dostupné prostřednictvím komunikačních služeb.

## <a name="about-phone-numbers-in-azure-communications-services"></a>O telefonních číslech v komunikačních službách Azure

Komunikační služby Azure umožňují používat telefonní čísla k umísťování a přijímání telefonních hovorů a zpráv SMS. Tato telefonní čísla se dají použít ke konfiguraci ID volajícího na odchozích voláních, která jsou do vaší služby.
  
Pokud nemáte stávající telefonní číslo pro import do řešení komunikačních služeb, nejjednodušší způsob, jak začít, je získat nové telefonní číslo z komunikačních služeb Azure během několika minut.

Pokud máte existující telefonní číslo, které chcete dál používat ve vašem řešení (například 1 – 800 – společnost), můžete telefonní číslo přenést z existujícího poskytovatele na komunikační služby.

Následující diagram vám pomůže procházet dostupné možnosti:

:::image type="content" source="../media/plan-solution/decision-tree-basic.png" alt-text="Diagram znázorňující, jak učinit rozhodnutí týkající se vašich telefonních čísel.":::

Teď si projdeme typy a možnosti telefonního čísla, které jsou k dispozici prostřednictvím komunikačních služeb. 

## <a name="microsoft-direct-offer-of-phone-numbers-and-capabilities"></a>Microsoft Direct nabízí telefonní čísla a možnosti

Komunikační služby Azure poskytují vývojářům vynikající flexibilitu. Na většině telefonních čísel vám umožníme nakonfigurovat sadu plánů "a la carte". Někteří vývojáři potřebují pouze vstupní plán volání; může se jednat o příchozí volání a odchozí plány SMS. Tyto plány je možné vybrat při zapůjčení nebo portování telefonních čísel v rámci komunikačních služeb.

Dostupné plány závisí na typu země a telefonní číslo, ve kterém pracujete. Následující diagram představuje postup rozhodování: dostupné plány závisí na typu země a telefonní číslo, na kterém pracujete.

<!-- Tami/team have rejected this multiple times despite updates, says it needs to be higher res - need to work with her to get approval for this image. Commenting out to move our staging forward. :::image type="content" source="../../media/example-decision-flow.png" alt-text="Example for the decision flow"::: -->

Než budete moct vybrat typ telefonní číslo, Podívejme se na plán mezinárodního telefonního číslování.

### <a name="optional-reading-international-public-telecommunication-numbering-plan-e164"></a>Volitelné čtení. Plán mezinárodní veřejné telekomunikace pro číslování (E. 164)

> [!NOTE]
> Tyto informace doporučujeme zkontrolovat i v případě, že jste obeznámeni s plánem telefonního číslování E. 164, abyste lépe pochopili typy a možnosti, které nabízí přímá nabídka služby Azure Communication Services.

Plán mezinárodní veřejné telekomunikace pro číslování je definovaný v doporučení ITU (International Telecommunication Union) E. 164. Čísla vyhovujících číslům jsou omezená na maximálně 15 číslic.

Telefonní číslo se skládá z

-   Předpona "+"
-   Mezinárodní předpona vytáčení nebo kód země/oblasti (jedna, dvě nebo tři číslice) 
-   *(Volitelné)* Národní cílový kód nebo plán číslování, který se běžně označuje jako kód oblasti. Délka tohoto kódu závisí na zemi. V USA se jedná o tři číslice. V Austrálii a na Novém Zélandu je to jedna číslice. Německo, Japonsko, Mexiko a některé jiné země mají proměnlivé délky kódů oblastí. Například v Německu může být kód oblasti kdekoli od dvou po pět číslic, zatímco v Japonsku může být kdekoli od jednoho až pěti číslic.
-   Číslo předplatitele

> [!NOTE]
> Výše uvedená klasifikace zcela nevyhovuje standardu ITU E. 164 a je určena k poskytnutí zjednodušeného popisu. Například číslo předplatitele je rozdělené do standardu. Pokud vás zajímá informace o mezinárodním číslovacím plánu hlubší, je [standard ITU E. 164 standardně](https://www.itu.int/rec/T-REC-E.164) vynikajícím místem, kde začít.  

Tady je několik příkladů, které vám pomůžou lépe pochopit plán číslování:

Místní telefonní číslo v USA:

:::image type="content" source="../media/plan-solution/regional-us.png" alt-text="Příklad místního telefonního čísla v USA":::

Místní telefonní číslo v Kanadě:

:::image type="content" source="../media/plan-solution/regional-canada.png" alt-text="Příklad místního telefonního čísla v Kanadě":::

Bezplatné číslo v Severní Amerika oblasti:

:::image type="content" source="../media/plan-solution/tollfree-us.png" alt-text="Příklad bezplatného čísla v Severní Amerika":::

Číslo mobilního telefonu v ČR:

:::image type="content" source="../media/plan-solution/mobile-uk.png" alt-text="Příklad mobilního čísla v ČR":::

Teď si probereme konkrétní typy telefonních čísel dostupných v komunikačních službách Azure.

## <a name="phone-number-types-in-azure-communication-services"></a>Typy telefonních čísel v komunikačních službách Azure

Společnost Microsoft nabízí pro krátké kódy a mobilní čísla v zemích, kde je to možné, i plány bez poplatků.

Následující tabulka shrnuje tyto typy telefonních čísel: 

| Typ telefonního čísla | Příklad                              | Dostupnost země    | Běžný případ použití                                                                                                     |
| ----------------- | ------------------------------------ | ----------------------- | ------------------------------------------------------------------------------------------------------------------- |
| Regionální          | + 1 (kód geografické oblasti) XXX XX XX  | USA, Kanada, Portoriko | Přiřazení telefonních čísel uživatelům v aplikacích nebo přiřazení interaktivních hlasových odpovědí (IRV)/roboty |
| Bezplatná linka         | + 1 ( *kód*oblasti bez poplatků) XXX xx xx | USA, Kanada, Portoriko | Přiřazení k systémům interaktivních hlasových odpovědí (IRV)/roboty, aplikacím SMS                                        |

## <a name="plans"></a>Plány 

Pojďme se podívat na možnosti, které můžete pro svoje telefonní čísla povolit. Tyto možnosti se v různých zemích liší podle zákonných požadavků. Komunikační služby Azure nabízí následující možnosti:

- **Jednosměrný odchozí server SMS, který**je užitečný pro scénáře oznámení a dvojúrovňové ověřování.
- **Obousměrný příchozí a odchozí SMS**, předdefinovaný balíček, který umožňuje, aby se SMS odesílal a přijímal jako součást jednoho plánu.
- **Volání veřejné telefonní sítě**: můžete vybrat příchozí volání a použít ID volajícího k umístění odchozích volání.

## <a name="next-steps"></a>Další kroky

### <a name="quickstarts"></a>Rychlé starty

- [Získat telefonní číslo](../../quickstarts/telephony-sms/get-phone-number.md)
- [Umístit volání](../../quickstarts/voice-video-calling/calling-client-samples.md)
- [Odeslání zprávy SMS](../../quickstarts/telephony-sms/send.md)

### <a name="conceptual-documentation"></a>Koncepční dokumentace

- [Koncepce hlasu a videa](../voice-video-calling/about-call-types.md)
- [Toky volání a toky SMS](../call-flows.md)
- [Ceny](../pricing.md)
