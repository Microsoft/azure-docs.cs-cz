---
title: Monitorování stavu instancí App Service
description: Naučte se monitorovat stav instancí App Service pomocí kontroly stavu.
keywords: Azure App Service, Webová aplikace, kontrola stavu, směrování provozu, v dobrém stavu, cesta, monitorování,
author: msangapu-msft
ms.topic: article
ms.date: 12/03/2020
ms.author: msangapu
ms.openlocfilehash: 7d6f9564328f81b71c62a4243c5f4cc209a29d8f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101714472"
---
# <a name="monitor-app-service-instances-using-health-check"></a>Monitorování instancí App Service pomocí kontroly stavu

![Selhání kontroly stavu][2]

V tomto článku se pomocí Azure Portal ke sledování instancí App Service používá kontrolu stavu. Při kontrole stavu se zvyšuje dostupnost vaší aplikace odebráním špatných instancí. [Plán App Service](./overview-hosting-plans.md) je třeba škálovat na dvě nebo více instancí, aby bylo možné použít kontrolu stavu. Cesta pro kontrolu stavu by měla kontrolovat kritické součásti aplikace. Například pokud vaše aplikace závisí na databázi a systému zasílání zpráv, koncový bod kontroly stavu by se měl k těmto součástem připojit. Pokud se aplikace nemůže připojit ke kritické součásti, měla by tato cesta vracet kód odpovědi 500 na úrovni, který indikuje, že aplikace není v pořádku.

## <a name="what-app-service-does-with-health-checks"></a>Co App Service s kontrolami stavu

- Když při zadání cesty k vaší aplikaci zkontroluje stav, otestuje tato cesta na všech instancích vaší App Service aplikace v intervalu 1 minut.
- Pokud instance nereaguje na stavový kód mezi 200-299 (včetně) po dvou nebo více požadavcích, nebo nereaguje na odpověď na příkaz, systém zjistí, že není v pořádku, a odebere ho.
- Po odebrání bude test stavu nadále testovat, zda není instance poškozena. Pokud bude nadále odpovídat neúspěšně, App Service restartuje příslušný virtuální počítač v úsilí, aby se instance vrátila do stavu v pořádku.
- Pokud instance zůstane po jednu hodinu v nesprávném stavu, bude nahrazena novou instancí.
- Při vertikálním navýšení nebo zmenšování App Service nástroj příkazového řádku otestuje cestu kontroly stavu, aby bylo zajištěno, že budou nové instance připravené.

> [!NOTE]
> Kontrolu stavu nedodržuje 302 přesměrování. V jednom z nich bude za hodinu nahrazena maximálně jedna instance, a to s maximálním počtem tří instancí za den a App Service.
>

## <a name="enable-health-check"></a>Povolit kontrolu stavu

![Navigace v oblasti kontroly stavu na webu Azure Portal][3]

- Pokud chcete povolit kontrolu stavu, přejděte na Azure Portal a vyberte svou aplikaci App Service.
- V části **monitorování** vyberte možnost **Kontrola stavu**.
- Vyberte **Povolit** a zadejte platnou cestu adresy URL v aplikaci, například `/health` nebo `/api/health` .
- Klikněte na **Uložit**.

> [!CAUTION]
> Změny konfigurace kontroly stavu restartujte aplikaci. Abychom minimalizovali dopad na produkční aplikace, doporučujeme [nakonfigurovat pracovní sloty](deploy-staging-slots.md) a odkládací prostředí v produkčním prostředí.
>

### <a name="configuration"></a>Konfigurace

Kromě konfigurace možností kontroly stavu můžete také nakonfigurovat následující [nastavení aplikace](configure-common.md):

| Název nastavení aplikace | Povolené hodnoty | Popis |
|-|-|-|
|`WEBSITE_HEALTHCHECK_MAXPINGFAILURES` | 2 - 10 | Maximální počet selhání nástroje test. Například pokud je nastavena na `2` , vaše instance budou odebrány po `2` neúspěšných příkazech otestuje. Při vertikálním navýšení nebo zmenšování App Service nástroj příkazového řádku otestuje cestu kontroly stavu, aby bylo zajištěno, že budou nové instance připravené. |
|`WEBSITE_HEALTHCHECK_MAXUNHEALTYWORKERPERCENT` | 0 - 100 | Aby nedocházelo k nenáročným instancím v pořádku, nebudou vyloučeny žádné více než polovinu instancí. Pokud je například plán App Service škálovat na čtyři instance a tři nejsou v pořádku, bude vyloučeno nejvíce dvou. Ostatní dvě instance (v pořádku a jedna není v pořádku) budou i nadále přijímat požadavky. V nejhorším případě, kdy nejsou všechny instance v pořádku, se nevylučují žádné. Chcete-li toto chování přepsat, nastavte nastavení aplikace na hodnotu mezi `0` a `100` . Vyšší hodnota znamená, že se odeberou víc instancí, které nejsou v pořádku (výchozí hodnota je 50). |

#### <a name="authentication-and-security"></a>Ověřování a zabezpečení

Kontroly stavu se integrují s funkcemi ověřování a autorizace App Service. Pokud jsou tyto funkce zabezpečení povoleny, nejsou vyžadovány žádné další nastavení. Pokud ale používáte vlastní ověřovací systém, musí cesta pro kontrolu stavu umožňovat anonymní přístup. Pokud je lokalita pouze HTTP **s** povolenou, žádost o kontrolu stavu bude odeslána prostřednictvím protokolu HTTP **S**.

Velké podniky pro vývoj v podniku často potřebují splňovat požadavky na zabezpečení pro vystavená rozhraní API. K zabezpečení koncového bodu kontroly stavu byste nejdřív měli používat funkce, jako jsou [omezení IP adresy](app-service-ip-restrictions.md#set-an-ip-address-based-rule), [klientské certifikáty](app-service-ip-restrictions.md#set-an-ip-address-based-rule)nebo Virtual Network, které omezují přístup k aplikacím. Koncový bod kontroly stavu můžete zabezpečit tím, že budete vyžadovat, aby `User-Agent` odpovídaly příchozímu požadavku `ReadyForRequest/1.0` . User-Agent nemůže být zfalšovaný, protože žádost již byla zabezpečena předchozími funkcemi zabezpečení.

## <a name="monitoring"></a>Sledování

Po poskytnutí cesty pro kontrolu stavu vaší aplikace můžete monitorovat stav svého webu pomocí Azure Monitor. V okně pro **kontrolu stavu** na portálu klikněte na **metriky** na horním panelu nástrojů. Otevře se nové okno, kde můžete zobrazit historický stav webu a vytvořit nové pravidlo výstrahy. Další informace o monitorování vašich lokalit najdete v [příručce k Azure monitor](web-sites-monitor.md).

## <a name="next-steps"></a>Další kroky
- [Vytvoření upozornění protokolu aktivit pro monitorování všech operací modulu automatického škálování v předplatném](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Vytvoření upozornění protokolu aktivit pro monitorování všech neúspěšných operací automatického škálování na více instancích ve vašem předplatném](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

[1]: ./media/app-service-monitor-instances-health-check/health-check-success-diagram.png
[2]: ./media/app-service-monitor-instances-health-check/health-check-failure-diagram.png
[3]: ./media/app-service-monitor-instances-health-check/azure-portal-navigation-health-check.png