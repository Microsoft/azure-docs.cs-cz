---
title: Výhody migrace na rozhraní Media Services API V3
description: V tomto článku jsou uvedené výhody migrace z Media Services V2 na v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 70f64813546c66c0f9e3533e09de192315f75600
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955067"
---
# <a name="step-1---understand-the-benefits-of-migrating-to-media-services-api-v3"></a>Krok 1 – pochopení výhod migrace na rozhraní Media Services API V3

![logo Průvodce migrací](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![kroky migrace 2](./media/migration-guide/steps-1.svg)

Doporučujeme začít používat rozhraní API verze 2020-05-01 Azure Media Services V3 nyní, abyste získali výhody, protože nové funkce, funkce a optimalizace výkonu jsou dostupné pouze v aktuálním rozhraní API v3.

Verzi rozhraní API můžete změnit na portálu, nejnovější sady SDK, nejnovější rozhraní příkazového řádku a REST API se správným řetězcem verze.

Byla zjištěna významná vylepšení Media Services se systémem v3.  

## <a name="benefits-of-media-services-v3"></a>Výhody Media Services V3

| **V3 – funkce** | **Výhoda** |
| --- | --- |
| **Azure Portal** | |
| Aktualizace Azure Portal | Azure Portal byla aktualizována, aby zahrnovala správu entit rozhraní V3 API. Umožňuje zákazníkům používat portál ke spouštění živého streamování, odesílání transformačních úloh v3, správu zásad ochrany obsahu, koncových bodů streamování, získání přístupu k rozhraní API, správě propojených účtů úložiště a provádění úloh monitorování. |
| **Účty a úložiště** | |
| Řízení přístupu na základě role v Azure (RBAC) | Zákazníci teď můžou definovat vlastní role a řídit přístup ke každé entitě v rozhraní API Media Services ARM. To pomáhá řídit přístup k prostředkům pomocí účtů AAD. |
| Spravované identity | Spravované identity eliminují potřebu vývojářů spravovat přihlašovací údaje tím, že poskytuje identitu pro prostředek Azure ve službě Azure AD. [Tady](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)najdete podrobnosti o spravovaných identitách. |
| Podpora privátních odkazů | Zákazníci budou mít přístup k koncovým bodům Media Services pro doručování klíčů, LiveEvents a starají prostřednictvím PrivateEndpoint ve své virtuální síti. |
| [Customer-spravovaných Key](concept-use-customer-managed-keys-byok.md) nebo Přineste si vlastní klíč (BYOK) support | Zákazníci můžou data v účtu Media Services šifrovat pomocí klíče ve svých Azure Key Vault. |
| **Aktiva** | |
| Asset může mít několik [lokátorů streamování](streaming-locators-concept.md) s různými nastaveními [dynamického balení](dynamic-packaging-overview.md) a dynamického šifrování. | U každého assetu je povolený limit datových lokátorů 100. Zákazníci můžou do assetu ukládat jednu kopii mediálního obsahu, ale můžou sdílet různé adresy URL streamování s různými zásadami streamování nebo zásadami ochrany obsahu, které jsou založené na cílové cílové skupině.
| **Zpracování úlohy** ||
| V3 zavádí koncepci [transformací](transforms-jobs-concept.md)   pro zpracování úloh založených na souborech. | Transformaci lze použít k vytvoření opakovaně použitelných konfigurací, k vytvoření Azure Resource Manager šablon a k izolaci nastavení zpracování mezi několika zákazníky nebo klienty. |
| Pro zpracování úloh založených na souborech můžete jako vstup použít adresu URL HTTP (S). | Nemusíte mít obsah, který je už uložený v Azure, ani nemusíte vytvářet vstupní prostředky. |
| **Živé události** ||
| Živé události služby Premium 1080p | Nová skladová SKU události umožňuje zákazníkům získat kódování v cloudu s využitím výstupu až 1080p v rámci řešení. |
| Nová podpora živého streamování s [nízkou latencí](live-event-latency.md) pro živé události | To umožňuje uživatelům sledovat živé události blíž k reálnému času, než by toto nastavení mělo povoleno. |
| Live Event Preview podporuje [dynamické balení](dynamic-packaging-overview.md)   a dynamické šifrování. | To umožňuje ochranu obsahu ve verzi Preview a nepřerušované a HLS balení. |
| Živé výstupy nahrazují programy | Živý výstup je jednodušší použít než entita programu v rozhraních API v2. |
| Vylepšení aplikace RTMP ingestování pro živé události je vylepšená a podporuje více kodérů. | Zvyšuje stabilitu a poskytuje flexibilitu zdrojového kodéru. |
| Živé události můžou streamovat nepřetržitě. | Můžete hostovat živou událost a zajistit, aby se vaše cílová skupina vykonávala delší období. |
| Živý přepis při dynamických událostech | Live přepis umožňuje zákazníkům automaticky přepisovat mluvený jazyk na text v reálném čase během živého vysílání událostí. Tím se výrazně zlepšuje přístupnost živých událostí. |
| [Úsporný režim](live-events-outputs-concept.md#standby-mode) u živých událostí | Živé události, které jsou v pohotovostním stavu, jsou méně nákladné než spouštění živých událostí. Díky tomu mohou zákazníci udržovat sadu živých událostí, které jsou připravené k zahájení během několika sekund, a to za nižší náklady než udržování sady spuštěných živých událostí. Snížené ceny za pohotovostní živé události začnou platit v únoru 2021 pro většinu oblastí a zbylé další kroky v dubnu 2021.
|**Ochrana obsahu** ||
| [Ochrana obsahu](content-key-policy-concept.md)   podporuje víc klíčových funkcí. | Zákazníci teď můžou ve svých lokátorech streamování používat víc šifrovacích klíčů obsahu. |
| **Monitorování** | |
| Podpora oznámení [Azure EventGrid](reacting-to-media-services-events.md) | Oznámení EventGrid jsou ve větším množství funkcí. Existuje více typů oznámení, širší podpora SDK pro příjem oznámení ve vaší vlastní aplikaci a další stávající služby Azure, které mohou fungovat jako obslužné rutiny událostí. |
| [Podpora a Integrace Azure Monitor v Azure Portal](monitor-events-portal-how-to.md) | To zákazníkům umožňuje vizualizovat Media Services využití kvót účtu, statistiky streamování v reálném čase a statistiky ingestování a archivace pro živé události. Zákazníci teď můžou nastavovat upozornění a provádět nezbytné akce na základě dat metrik v reálném čase. |

## <a name="next-steps"></a>Další kroky

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
