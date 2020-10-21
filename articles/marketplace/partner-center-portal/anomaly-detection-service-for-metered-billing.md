---
title: Služba detekce anomálií pro měření podle objemu dat – Microsoft Azure Marketplace
description: Popisuje způsob, jakým funguje detekce anomálií, když se odesílají oznámení a co s nimi dělat, a možnosti podpory.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.author: mingshen
author: mingshen-ms
ms.date: 06/10/2020
ms.openlocfilehash: 909b818c60932f5b1d6071ea1ccf0ee4c76173b4
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92280528"
---
# <a name="anomaly-detection-service-for-metered-billing"></a>Služba detekce anomálií pro fakturaci na základě měření

[Služba měření na webu Marketplace](marketplace-metering-service-apis-faq.md) umožňuje vytvářet nabídky v programu komerčního tržiště, které se účtují podle nestandardních jednotek. S měřením fakturace odesíláte do Microsoftu události využití vašeho zákazníka a na základě tohoto využití připravujeme fakturaci.

Nesprávná data o využití můžou pocházet z nejrůznějších příčin, jako jsou chyby, chybné konfigurace při sledování spotřeby nebo podvod. Nesprávná data o použití by způsobila nesprávné poplatky za zákazníky a případné spory.

Abychom mohli zmírnit riziko, služba detekce anomálií aplikuje algoritmy strojového učení k určení normálního chování při vyúčtování, analýze měření využívání a zjišťování anomálií s minimálním zásahem uživatele.

Zobrazí se oznámení, že při využití účtovaného podle objemu dat se zjistila anomálie. Díky tomu máte možnost prozkoumat a informovat nás, pokud se potvrdí, že došlo k reálnému problému, a v tom případě je možné provést akce, které budou proaktivně řešit potíže s fakturací zákazníků.

Kromě náhlého nárůstu, poklesu a trendu u využívání měřených fakturačních hodnot používá náš model také účty pro sezónní účinky. Vzhledem k tomu, že se účtované měřené údaje sdělují prostřednictvím dat nadlimitního využití, náš model také dokáže řádně zpracovat dlouhá období chybějících dat.

Následují příklady výsledků detekce anomálií. Očekávaný rozsah se zobrazuje jako žlutý pruh. Přijatelné využití účtované podle objemu se v pásmu zobrazuje jako zelené hvězdičky. Využití fakturace mimo pásmo se zobrazuje jako červená tečka.  

Anomálie zjištěné mimo předvídatelný trend:

![Znázorňuje anomálie zjištěné mimo předvídatelný trend.](media/anomaly-1.png)

Anomálie zjištěné mimo opakovaný cyklický trend:

![Znázorňuje anomálie zjištěné mimo opakující se cyklický trend.](media/anomaly-2.png)

Anomálie zjištěné ve stoupajících trendech:

![Znázorňuje anomálie zjištěné ve stoupajících trendech.](media/anomaly-3.png)

## <a name="how-anomaly-detection-service-works"></a>Jak funguje služba pro detekci anomálií

Detekce anomálií je povolena automaticky pro všechna využívání měřených faktur. Při odesílání událostí používání společnosti Microsoft vytvoří služba detekce anomálií model očekávaných hodnot na základě dat o využití v minulosti. Tento model běží týdně.

Funkce detekce anomálií na úrovni jednotlivých měřičů a zákazníků. To znamená, že každý měřič s každým zákazníkem bude mít vyškolený model založený na vzorci použití tohoto měřiče v minulosti.

Model funguje tak, že se generují opakované intervaly spolehlivosti. Prognóza časové řady je zobecněný aditivní model skládající se z části předpovědi trendu a sezónnost část. Vzhledem k tomu, že model je formulován jako regresní úkol, může řádným způsobem zpracovávat dlouhou dobu chybějících dat. Pokud pozorování spadá mimo předpokládané intervaly spolehlivosti, znamená to, že pozorování nelze vysvětlit na základě historických vzorců měřených faktur, a proto může být anomálií.

## <a name="anomaly-detection-notification"></a>Oznámení o detekci anomálií

Každý týden vydáme e-mailová oznámení o detekci anomálií. Zahrnuje všechny anomálie zjištěné v týdnu pro všechny měřiče a zákazníky. Tento e-mail se pošle **technickému** a **technickému** kontaktu, který jste získali při vytváření nabídky.

Očekává se, že budete zkoumat, jestli jsou zjištěné anomálie reálné, a pokud ano, požádejte Microsoft, aby nahlásil nesprávné použití (viz část podpora níže).

Pokud ověříte, že zjištěné anomálie jsou normálním využitím, nemusíte nic dělat. Pokud však anomálie představuje potenciálně vysoké finanční riziko, můžeme vás kontaktovat, abychom potvrdili použití.  

## <a name="when-and-how-to-get-support"></a>Kdy a jak získat podporu

Pokud jste společnosti Microsoft poslali nesprávné používání a tato akce vznikla, nebo bude mít za následek, že je zákazníkem účtováno jako podplatně, nevystaví se zákazníkovi žádné poplatky za použití, nebo za toto použití platíte. Za ztrátu příjmů způsobenou nahlášením nižšího využití ponesete odpovědnost vy.

Pokud se vás týká některý z následujících případů, můžete vytvořit lístek podpory s žádostí o refundaci nebo úpravu fakturace pro vaše zákazníky:

- Potvrdili se, že jedna z anomálií, které jsme našli, je skutečný problém a nesprávné použití **overcharge** by vedlo k přečerpání zákazníka.
- Zjistili jste, že nám jste poslali nesprávné použití, a nesprávné použití by vedlo **k** přečerpání zákazníka.
- Chtěli byste požádat o refundaci za poplatek za využití účtované fakturace zákazníka.

Odeslání lístku:

1. Přejít na stránku podpory. V **poli řekněte nám o vašem problému**zadejte "nesprávné použití".
2. V tématech podpory v rozevíracím seznamu výsledků hledání vyberte jednu z následujících možností:
    - **Komerční tržiště**  >  **Měřené fakturace**  >  Bylo **Odesláno nesprávné použití pro nabídku aplikací Azure**nebo
    - **Komerční tržiště**  >  **Měřené fakturace**  >  **Nesprávné použití odeslané pro nabídku SaaS**
3. V části **Další krok**vyberte tlačítko **revidovat řešení** , které se má směrovat, abyste se přihlásili do partnerského centra pro odeslání lístku podpory.

Další možnosti podpory vydavatelů najdete [v tématu Podpora programu komerčního tržiště v partnerském centru](../support.md).

## <a name="next-step"></a>Další krok

- Seznamte se s [rozhraním API služby pro měření na webu Marketplace](marketplace-metering-service-apis.md).
