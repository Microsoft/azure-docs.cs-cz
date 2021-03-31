---
title: Azure Service Bus úrovně Premium a Standard
description: Tento článek popisuje úrovně Standard a Premium Azure Service Bus. Porovná tyto úrovně a poskytuje technické rozdíly.
ms.topic: conceptual
ms.date: 02/17/2021
ms.openlocfilehash: aa08a99009ef3d20e831e214ae5811059817d13c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104607547"
---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Úrovně zasílání zpráv Service Bus Premium a Standard

Zasílání zpráv Service Bus, které zahrnuje entity jako jsou fronty a témata, v sobě kombinuje funkce pro zasílání zpráv v rámci podniku s bohatou sémantikou publikování a odběru na úrovni cloudu. Zasílání zpráv Service Bus se používá jako páteřní prvek mnoha sofistikovaných cloudových řešení.

Úroveň *Premium* zasílání zpráv Service Bus řeší běžné požadavky zákazníků z pohledu rozsahu, výkonu a dostupnosti pro klíčové aplikace. Pro produkční scénáře se doporučuje úroveň Premium. Přestože mají tyto dvě úrovně skoro stejné sady funkcí, jsou určené pro použití v odlišných situacích.

V následující tabulce je zvýrazněno několik nejvýraznějších rozdílů.

| Premium | Standard |
| --- | --- |
| Vysoká propustnost |Variabilní propustnost |
| Předvídatelný výkon |Variabilní latence |
| Pevné ceny |Variabilní průběžná cena  |
| Možnost vertikálně navýšit a snížit kapacitu |– |
| Velikost zprávy je až 1 MB. Toto omezení může být vyvoláno v budoucnu. Nejnovější důležité aktualizace této služby najdete v tématu [zasílání zpráv na blogu Azure](https://techcommunity.microsoft.com/t5/messaging-on-azure/bg-p/MessagingonAzureBlog). |Velikost zprávy do 256 kB |

**Zasílání zpráv Service Bus Premium** zajišťuje izolaci prostředků na úrovni CPU a paměti, takže každá úloha zákazníka běží izolovaně. Kontejner prostředků se nazývá *jednotka zasílání zpráv*. Každému prémiovému obor názvů se přiřadí aspoň jedna jednotka zasílání zpráv. Pro každý obor názvů Service Bus Premium si můžete koupit 1, 2, 4, 8 nebo 16 jednotek zasílání zpráv. Jedna úloha nebo entita může zabírat několik jednotek zasílání zpráv a počet jednotek zasílání zpráv se dá změnit na. Výsledkem je předvídatelný a opakovatelný výkon vašeho řešení postaveného na Service Bus.

Vedle toho, že je tento výkon předvídatelnější, je také rychlejší. Se zasíláním zpráv na úrovni Premium je výkon ve špičce mnohem vyšší než na úrovni Standard.

## <a name="premium-messaging-technical-differences"></a>Technické rozdíly zasílání zpráv na úrovni Premium

V následujících částech je uvedeno několik rozdílů mezi úrovněmi zasílání zpráv Premium a Standard.

### <a name="partitioned-queues-and-topics"></a>Dělené fronty a témata

Dělené fronty a témata nejsou v zasílání zpráv na úrovni Premium podporované. Další informace o dělení najdete v oddílu [Dělené fronty a témata](service-bus-partitioning.md).

### <a name="express-entities"></a>Expresní entity

Protože zasílání zpráv na úrovni Premium běží v izolovaném prostředí, nejsou expresní entity v oborech názvů úrovně Premium podporované. Entita Express uchovává zprávu v paměti dočasně předtím, než ji zapíše do trvalého úložiště. Pokud máte kód spuštěný v rámci standardního zasílání zpráv a chcete ho přenést na úroveň Premium, zajistěte, aby byla funkce Express entity zakázaná.

## <a name="premium-messaging-resource-usage"></a>Využívání prostředků pro zasílání zpráv na úrovni Premium
Obecně platí, že jakákoli operace s entitou může způsobit využití procesoru a paměti. Tady jsou některé z těchto operací: 

- Operace správy, například operace CRUD (vytváření, načítání, aktualizace a odstraňování) ve frontách, tématech a předplatných.
- Běhové operace (odesílání a příjem zpráv)
- Monitorování operací a výstrah

Další využití procesoru a paměti se navíc neúčtují. U úrovně zasílání zpráv na úrovni Premium má jednotka pro zprávy jednu cenu.

Využití CPU a paměti se sleduje a zobrazí se vám z následujících důvodů: 

- Zajištění transparentnosti interních systémů
- Pochopení kapacity zakoupených prostředků.
- Plánování kapacity, které vám pomůže se rozhodnout o horizontální navýšení nebo snížení kapacity.

## <a name="messaging-unit---how-many-are-needed"></a>Jednotka zasílání zpráv – kolik je potřeba?

Při zřizování oboru názvů Azure Service Bus Premium je nutné zadat počet přidělených jednotek zasílání zpráv. Tyto jednotky pro zasílání zpráv jsou vyhrazené prostředky, které jsou přiděleny oboru názvů.

Počet jednotek zasílání zpráv přidělených oboru názvů Service Bus Premium se dá **dynamicky upravovat** na faktoru změny (zvýšení nebo snížení) v úlohách.

Při rozhodování o počtu jednotek zasílání zpráv pro vaši architekturu je potřeba vzít v úvahu několik faktorů:

- Začněte s ***1 nebo 2 jednotkami pro zasílání zpráv*** přidělenou vašemu oboru názvů.
- Prostudujte metriky využití CPU v rámci [metrik využití prostředků](service-bus-metrics-azure-monitor.md#resource-usage-metrics) pro váš obor názvů.
    - Pokud je využití procesoru ***nižší než 20%** _, může být možné _ *_škálovat × snížit_* počet jednotek zasílání zpráv, které jsou přiděleny vašemu oboru názvů.
    - Pokud je využití procesoru ***vyšší než 70%** _, bude mít vaše aplikace za to, že bude vertikální *_navýšení_* kapacity × počet jednotek zasílání zpráv, které jsou přiděleny vašemu oboru názvů.

Informace o tom, jak nakonfigurovat obor názvů Service Bus pro automatické škálování (zvýšení nebo snížení počtu jednotek pro zasílání zpráv), najdete v tématu [Automatické aktualizace jednotek zasílání zpráv](automate-update-messaging-units.md).

> [!NOTE]
> **Škálování** prostředků přidělených oboru názvů může být buď přepnuto, nebo reaktivní.
>
>  * **Nemožnost: Pokud** je očekáváno další zatížení (z důvodu sezónnost nebo trendů), můžete pokračovat v přidělování více jednotek zasílání zpráv do oboru názvů před dosažením zatížení.
>
>  * **Reactive**: Pokud se při studiu metrik využití prostředků identifikují další úlohy, dají se k oboru názvů přidělit další prostředky, které budou zahrnovat rostoucí poptávku.
>
> Měřiče fakturace pro Service Bus jsou každou hodinu. V případě horizontálního navýšení kapacity platíte jenom za další prostředky za hodiny, které se používaly.
>

## <a name="get-started-with-premium-messaging"></a>Začínáme se zasíláním zpráv na úrovni Premium

Využití zasílání zpráv na úrovni Premium je jednoduché a je podobné standardnímu zasílání zpráv. Začněte [vytvořením oboru názvů](service-bus-create-namespace-portal.md) na webu [Azure Portal](https://portal.azure.com). Ujistěte se, že jste v části **cenová úroveň** vybrali **Premium** . Kliknutím na **Zobrazit všechny podrobnosti o cenách** zobrazíte další informace o jednotlivých úrovních.

![create-premium-namespace][create-premium-namespace]

Můžete také vytvářet [obory názvů Premium pomocí šablon Azure Resource Manageru](https://azure.microsoft.com/resources/templates/101-servicebus-pn-ar/).

## <a name="next-steps"></a>Další kroky

Pokud se o zasílání zpráv Service Bus chcete dozvědět více, podívejte se na následující odkazy:

- [Automaticky aktualizovat jednotky zasílání zpráv](automate-update-messaging-units.md).
- [Představení zasílání zpráv Azure Service Bus úrovně Premium (příspěvek na blogu)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
- [Představení zasílání zpráv Azure Service Bus úrovně Premium (channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)

<!--Image references-->

[create-premium-namespace]: ./media/service-bus-premium-messaging/select-premium-tier.png
