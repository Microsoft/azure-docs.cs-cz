---
title: Typy telefonních čísel v komunikačních službách Azure
titleSuffix: An Azure Communication Services concept document
description: Naučte se efektivně používat různé typy telefonních čísel pro SMS a telefonní číslo.
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 10/05/2020
ms.topic: overview
ms.custom: references_regions
ms.service: azure-communication-services
ms.openlocfilehash: b7d780ce6362e34c801b4da25332bff90f0b7515
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101659269"
---
# <a name="phone-number-types-in-azure-communication-services"></a>Typy telefonních čísel v komunikačních službách Azure

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Komunikační služby Azure umožňují používat telefonní čísla k provádění hlasových hovorů a posílání zpráv SMS pomocí veřejné telefonní sítě (PSTN). V tomto dokumentu si probereme typ telefonního čísla, možnosti konfigurace a dostupnost oblasti pro plánování telefonního subsystému a řešení SMS pomocí komunikačních služeb.

## <a name="number-types-and-features"></a>Typy a funkce čísel
Komunikační služby nabízí dva typy telefonních čísel: **místní** a **bez poplatků**.

### <a name="local-numbers"></a>Místní čísla
Místní (geografické) čísla jsou telefonní čísla tvořená 10 číslicemi, která se skládají z kódů místních oblastí v USA. Například `+1 (206) XXX-XXXX` je místní číslo s kódem oblasti `206` . Tento kód oblasti je přiřazen města Seattle. Tato telefonní čísla obecně používají jednotlivci a místní firmy. Služba Azure Communication Services nabízí místní čísla v USA. Tato čísla lze použít k uskutečnění telefonních hovorů, ale ne k odesílání zpráv SMS.

### <a name="toll-free-numbers"></a>Bezplatné telefonní číslo
Bezplatné telefonní číslo je 10 číslic telefonního čísla s jedinečnými kódy oblastí, které je možné volat z libovolného telefonního čísla zdarma. Například `+1 (800) XXX-XXXX` je číslo bezplatné linky v oblasti Severní Amerika. Tato telefonní čísla se obecně používají pro účely služeb zákazníkům. Služba Azure Communication Services nabízí bezplatné telefonní údaje ve Spojených státech. Tato čísla lze použít k umístění telefonních hovorů a k zasílání zpráv SMS. Čísla s bezplatnými čísly nemůžou používat lidé a dají se jim přiřadit jenom aplikace.

#### <a name="choosing-a-phone-number-type"></a>Výběr typu telefonního čísla

Pokud bude vaše telefonní číslo používáno aplikací (například při volání nebo posílání zpráv jménem vaší služby), můžete vybrat číslo bezplatné linky nebo místní (geografické) číslo. Pokud vaše aplikace posílá zprávy SMS nebo volají volání, můžete vybrat bezplatné telefonní číslo.

Pokud vaše telefonní číslo používá osoba (například uživatel vaší volající aplikace), musí se použít místní (geografické) telefonní číslo.

Následující tabulka shrnuje tyto typy telefonních čísel:

| Typ telefonního čísla | Příklad                              | Dostupnost země    | Možnost telefonního čísla |Běžný případ použití                                                                                                     |
| ----------------- | ------------------------------------ | ----------------------- | ------------------------|------------------------------------------------------------------------------------------------------------------- |
| Místní (geografické)        | + 1 (místní směrové číslo) XXX XX XX  | USA                      | Volání (odchozí) | Přiřazení telefonních čísel uživatelům ve vašich aplikacích  |
| Toll-Free         | + 1 ( *kód* oblasti bez poplatků) XXX xx xx | USA                      | Volání (odchozí), SMS (příchozí/odchozí)| Přiřazení telefonních čísel k systémům IRV (Interactive Voice Response)/roboty, aplikacím SMS                                        |


### <a name="phone-number-features-in-azure-communication-services"></a>Funkce telefonního čísla v komunikačních službách Azure

[!INCLUDE [Emergency Calling Notice](../../includes/emergency-calling-notice-include.md)]

U většiny telefonních čísel vám umožníme nakonfigurovat sadu funkcí "a la carte". Tyto funkce se dají vybrat při zapůjčení telefonních čísel v rámci komunikačních služeb Azure.

Funkce, které jsou vám k dispozici, závisí na zemi, ve které pracujete, v případu použití a na typu telefonního čísla, který jste vybrali. Tyto funkce se v jednotlivých zemích liší podle zákonných požadavků. Služba Azure Communication Services nabízí následující funkce telefonního čísla:

- **Jednosměrný odchozí SMS** Tato možnost umožňuje odesílat uživatelům zprávy SMS. To může být užitečné v případě oznámení a scénářů se dvěma faktory ověřování.
- **Obousměrný příchozí a odchozí SMS** Tato možnost umožňuje odesílat a přijímat zprávy od uživatelů pomocí telefonních čísel. To může být užitečné ve scénářích služeb zákazníkům.
- **Jednosměrné odchozí telefonní volání** Tato možnost umožňuje volat uživatele a nakonfigurovat ID volajícího pro odchozí volání, která jsou umístěna ve vaší službě. To může být užitečné ve scénářích služeb zákazníkům a hlasových oznámení.

## <a name="countryregion-availability"></a>Dostupnost země nebo oblasti

Následující tabulka uvádí, kde můžete získat různé typy telefonních čísel spolu s příchozím a odchozím voláním a funkcemi SMS, které jsou přidružené k těmto typům telefonních čísel.

|Typ čísla| Získat čísla v | Volat na                                        | Přijmout volání z                                    |Odeslat zprávy do       | Přijímat zprávy z |
|-----------| ------------------ | ---------------------------------------------------  |-------------------------------------------------------|-----------------------|--------|
| Místní (geografické)  | USA                 | USA, Kanada, Spojené království, Německo, Francie,. + Další *| USA, Kanada, Spojené království, Německo, Francie,. + Další * |Není k dispozici| Není k dispozici |
| Toll-Free | USA                 | USA                                                   | USA                                                    |USA                | USA |

* Další podrobnosti o cílech volání a cenách najdete na [stránce s cenami](../pricing.md).

## <a name="azure-subscriptions-eligibility"></a>Způsobilost k předplatným Azure

Pokud chcete získat telefonní číslo, musíte se přihlásit k placenému předplatnému Azure. Telefonní čísla nejdou získat na zkušebních účtech ani na bezplatné kredity Azure.

V současné době je dostupnost telefonního čísla omezená na předplatná Azure, která mají fakturační adresu v USA.

## <a name="next-steps"></a>Další kroky

### <a name="quickstarts"></a>Rychlé starty

- [Získat telefonní číslo](../../quickstarts/telephony-sms/get-phone-number.md)
- [Umístit volání](../../quickstarts/voice-video-calling/calling-client-samples.md)
- [Odeslání zprávy SMS](../../quickstarts/telephony-sms/send.md)

### <a name="conceptual-documentation"></a>Koncepční dokumentace

- [Koncepce hlasových hovorů a videohovorů](../voice-video-calling/about-call-types.md)
- [Koncepce telefonního subsystému](./telephony-concept.md)
- [Toky volání](../call-flows.md)
- [Ceny](../pricing.md)
