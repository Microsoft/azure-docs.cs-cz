---
title: Plánování služby Azure Communication Services pro telefon a řešení SMS
titleSuffix: An Azure Communication Services concept document
description: Zjistěte, jak efektivně naplánovat používání telefonních čísel a telefonního subsystému.
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 10/05/2020
ms.topic: overview
ms.custom: references_regions
ms.service: azure-communication-services
ms.openlocfilehash: b25ac36bc0b424a9d6f76b37b532e52dc56e19e3
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92048215"
---
# <a name="plan-your-telephony-and-sms-solution"></a>Plánování telefonie a řešení pro SMS

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


Komunikační služby Azure umožňují používat telefonní čísla k provádění hlasových hovorů a posílání zpráv SMS pomocí veřejné telefonní sítě (PSTN). V tomto dokumentu zkontrolujeme typy telefonních čísel, plány a dostupnost oblastí pro plánování telefonního subsystému a řešení SMS pomocí komunikačních služeb.

[!INCLUDE [Emergency Calling Notice](../../includes/emergency-calling-notice-include.md)]


## <a name="phone-number-types-in-azure-communication-services"></a>Typy telefonních čísel v komunikačních službách Azure
 
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
| Toll-Free         | + 1 ( *kód*oblasti bez poplatků) XXX xx xx | USA                      | Volání (odchozí), SMS (příchozí/odchozí)| Přiřazení telefonních čísel k systémům IRV (Interactive Voice Response)/roboty, aplikacím SMS                                        |


## <a name="phone-number-plans-in-azure-communication-services"></a>Plány telefonního čísla v komunikačních službách Azure 

U většiny telefonních čísel vám umožníme nakonfigurovat sadu plánů "a la carte". Někteří vývojáři potřebují pouze plán odchozího volání; může se jednat o odchozí volání a odchozí plány SMS. Tyto plány je možné vybrat při zapůjčení telefonních čísel v rámci komunikačních služeb Azure.

Plány, které jsou k dispozici, závisí na zemi, ve které pracujete, v případu použití a na typu telefonního čísla, který jste vybrali. Tyto plány se v důsledku zákonných požadavků liší podle zemí. Komunikační služby Azure nabízí následující plány:

- **Jednosměrný odchozí SMS** Tento plán vám umožní posílat uživatelům zprávy SMS. Tento plán je vhodný pro scénáře, jako jsou oznámení a výstrahy se dvěma faktory ověřování. 
- **Obousměrný příchozí a odchozí SMS** Tento plán umožňuje odesílat a přijímat zprávy od uživatelů pomocí telefonních čísel. Tento plán je užitečný ve scénářích služeb zákazníkům.
- **Jednosměrné odchozí telefonní volání** Tento plán vám umožní volat uživatele a nakonfigurovat ID volajícího pro odchozí volání, která ukládá vaše služba. Tento plán je užitečný ve scénářích služeb zákazníkům a hlasových oznámení.

## <a name="countryregion-availability"></a>Dostupnost země nebo oblasti

Následující tabulka uvádí, kde můžete získat různé typy telefonních čísel spolu s příchozím a odchozím voláním a s využitím funkcí SMS přidružených k těmto typům telefonních čísel.

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
- [Toky volání](../call-flows.md)
- [Ceny](../pricing.md)
