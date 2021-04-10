---
title: Služba detekce anomálií pro měření podle objemu dat – Microsoft Azure Marketplace
description: Popisuje způsob, jakým funguje detekce anomálií, když se odesílají oznámení a co s nimi dělat, a možnosti podpory.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.author: mingshen
author: mingshen-ms
ms.date: 06/10/2020
ms.openlocfilehash: ac3e07c67ca82c2960de1c4341a714b33751bfc1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101092395"
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

V partnerském centru můžete vyhodnocovat, spravovat a potvrdit anomálie. Další informace najdete v tématu [Správa anomálií s měřenými fakturami v partnerském centru](../anomaly-detection.md).

Aby se zajistilo, že se vašim zákazníkům neúčtují měřené využití, měli byste prozkoumat, jestli jsou zjištěné anomálie reálné problémy. Pokud ano, můžete potvrdit nesprávné použití v partnerském centru.

Doporučujeme, abyste potvrdili, jestli jsou zjištěné anomálie normálním využitím. Tím se vylepšit data anomálií, která vám poskytneme. Pokud anomálie představuje potenciálně vysoké finanční riziko, můžeme vás kontaktovat, abychom potvrdili použití.

## <a name="when-and-how-to-get-support"></a>Kdy a jak získat podporu

Pokud nám jste poslali nesprávné měřené využití, které mohlo nebo bude mít za následek navýšení na zákazníky, nebudeme zákazníkovi účtovat poplatky za použití v rámci hlášeného využití nebo je platíte za toto využití. Za ztrátu příjmů způsobenou nahlášením nižšího využití ponesete odpovědnost vy.

Pokud platí jeden z následujících případů, můžete upravit množství využití v partnerském centru, které bude mít za následek změnu refundace nebo fakturace pro vaše zákazníky:

- Potvrdili se, že jedna z anomálií, které jsme našli, je skutečný problém a nesprávné použití by vedlo k tomu, že se zákazník dobíjí.
- Zjistili jste, že nám jste poslali nesprávné použití, a nesprávné použití by vedlo k tomu, že se zákazník dobíjí.

Chcete-li odeslat lístek podpory týkající se anomálií měřených faktur:

1. Přihlaste se do [partnerského centra](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) pomocí svého pracovního účtu.
1. V nabídce v pravém horním rohu stránky vyberte ikonu **Podpora** . Podokno **pomoc a podpora** se zobrazí na pravé straně stránky.
1. Pokud chcete získat nápovědu k komerčnímu tržišti, vyberte **komerční tržiště**.
   ![Znázorňuje podokno podpory.](../media/support/commercial-marketplace-support-pane.png)
1. V poli **Shrnutí problému** zadejte **komerční web Marketplace > účtované podle objemu**.
1. V poli **typ problému** vyberte jednu z následujících možností:
    - **Komerční tržiště > účtované podle objemu > nesprávné použití odesílané pro nabídky aplikací Azure**
    - **Komerční tržiště > účtované podle objemu > nesprávné použití odeslané pro nabídku SaaS**
1. V části **Další krok** vyberte **Zkontrolovat řešení**.
1. Pokud chcete odeslat lístek podpory, přečtěte si Doporučené dokumenty, pokud nějaké máte, nebo vyberte **Podrobnosti o problému** .

Další možnosti podpory vydavatelů najdete [v tématu Podpora programu komerčního tržiště v partnerském centru](../support.md).

## <a name="next-steps"></a>Další kroky

- Seznamte se s [rozhraním API služby pro měření na webu Marketplace](marketplace-metering-service-apis.md).
- [Správa anomálií s měřením fakturace v partnerském centru](../anomaly-detection.md)
