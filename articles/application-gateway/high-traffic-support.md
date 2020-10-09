---
title: Podpora objemu Application Gateway vysokého provozu
description: Tento článek poskytuje pokyny ke konfiguraci Azure Application Gateway v podpoře scénářů velkého objemu síťových přenosů.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: caya
ms.openlocfilehash: b96720ead2c7b7bc942efca32a8510f57c2dbcad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85250244"
---
# <a name="application-gateway-high-traffic-support"></a>Podpora vysokého provozu služby Application Gateway

>[!NOTE]
> Tento článek popisuje několik navrhovaných pokynů, které vám pomůžou nastavit Application Gateway pro zpracování dalšího provozu z důvodu vysokého objemu provozu, ke kterému může dojít v důsledku krize COVID-19.

Pomocí Application Gateway s firewallem webových aplikací (WAF) můžete škálovatelný a zabezpečený způsob správy provozu do webových aplikací.

Následující návrhy vám pomůžou nastavit Application Gateway s využitím WAF pro zpracování dalšího provozu.

## <a name="use-the-v2-sku-over-v1-for-its-autoscaling-capabilities-and-performance-benefits"></a>Pro své možnosti automatického škálování a výhody výkonu použijte SKU verze V2 přes v1.
SKU verze 2 nabízí automatické škálování, aby bylo zajištěno, že se vaše Application Gateway může vertikálně škálovat při zvyšování provozu. Nabízí taky další významné výkonnostní výhody, jako je pětinásobné lepší výkon při snižování zátěže TLS, rychlejší nasazení a časy aktualizace, redundance zóny a další v porovnání s v1. Další informace najdete v [dokumentaci ke v2](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant). 

## <a name="set-maximum-instance-count-to-the-maximum-possible-125"></a>Nastavit maximální počet instancí na maximální možnou hodnotu (125)
 
Za předpokladu, že máte SKU Application Gateway v2, nastavení maximálního počtu instancí na maximální možnou hodnotu 125 umožňuje Application Gateway horizontální navýšení kapacity v případě potřeby. To umožňuje, aby mohla zpracovávat možné zvýšení provozu do vašich aplikací. Budou se vám účtovat jenom jednotky kapacity (kapacitní jednotky), které použijete.  

## <a name="set-your-minimum-instance-count-based-on-your-average-cu-usage"></a>Nastavte minimální počet instancí na základě průměrného využití v množství CU

Za předpokladu, že máte SKU Application Gateway v2, bude automatické škálování trvat šest až sedm minut. S vyšším minimálním počtem instancí může Application Gateway lépe zpracovávat přenosy při zvýšení zatížení, protože špička v provozu nevyžaduje operaci automatického škálování.  

## <a name="alert-if-a-certain-metric-surpasses-75-of-average-cu-utilization"></a>Výstraha, pokud určitá metrika překročí 75% průměrného využití hodnoty CU 
Podrobné vysvětlení našich metrik a dalších návodů najdete v [dokumentaci k Application Gatewaym metrikám](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-visualization) . 

### <a name="example-setting-up-an-alert-on-75-of-average-cu-usage"></a>Příklad: nastavení výstrahy na 75% průměrného množství CU využití

V tomto příkladu se dozvíte, jak použít Azure Portal k nastavení upozornění, když je dosaženo 75% průměrného využití. 
1. Přejděte na **Application Gateway**.
2. Na levém panelu vyberte na kartě **monitorování** možnost **metriky** . 
3. Přidejte metriku pro **průměrné aktuální výpočetní jednotky**. 
![Nastavení metriky WAF](./media/application-gateway-covid-guidelines/waf-setup-metrics.png)
4. Pokud jste nastavili minimální počet instancí, který bude odpovídat průměrnému využití, pokračujte a nastavte výstrahu, když se vaše minimální instance 75% využije. Pokud je například průměrné využití 10 kapacitní jednotky, nastavte upozornění na 7,5 kapacitní jednotky. Tato výstraha vás upozorní, pokud se využití zvyšuje a poskytuje čas na odpověď. Pokud si myslíte, že se tento provoz bude zabývat, budete moct upozornit na to, že se provoz může zvýšit. 
![Nastavení upozornění WAF](./media/application-gateway-covid-guidelines/waf-setup-monitoring-alert.png)

> [!NOTE]
> V závislosti na tom, jakým způsobem chcete mít k dispozici možnosti provozu, můžete nastavit, aby se tato výstraha nastavila na nižší nebo vyšší procento využití.

## <a name="set-up-waf-with-geofiltering-and-bot-protection-to-stop-attacks"></a>Nastavení WAF s využitím pro infiltrování a ochranu robota k zastavení útoků
Pokud chcete před aplikací použít další vrstvu zabezpečení, použijte Application Gateway WAF_v2 SKU pro funkce WAF. SKLADOVOU položku v2 můžete nakonfigurovat tak, aby povolovala přístup pouze k vašim aplikacím z dané země nebo oblasti nebo zemí/oblastí. Nastavili jste vlastní pravidlo WAF k explicitnímu povolení nebo blokování provozu na základě geografického umístění. Další informace najdete v tématu věnovaném [infiltrování vlastních pravidel](https://docs.microsoft.com/azure/web-application-firewall/ag/geomatch-custom-rules) a [způsobu konfigurace vlastních pravidel pro Application Gateway WAF_v2 SKU prostřednictvím PowerShellu](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules).

Povolí ochranu robotů blokující známé chybné roboty. To by mělo snížit objem provozu, který se bude připravovat do vaší aplikace. Další informace najdete v tématu [ochrana robota s pokyny k instalaci](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules).

## <a name="turn-on-diagnostics-on-application-gateway-and-waf"></a>Zapnutí diagnostiky na Application Gateway a WAF

Diagnostické protokoly umožňují zobrazit protokoly brány firewall, protokoly výkonu a protokoly přístupu. Pomocí těchto protokolů v Azure můžete spravovat a řešit potíže s aplikačními bránami. Další informace najdete v naší [dokumentaci k diagnostice](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#diagnostic-logging). 

## <a name="set-up-an-tls-policy-for-extra-security"></a>Nastavení zásad TLS pro další zabezpečení
Ujistěte se, že používáte nejnovější verzi zásad TLS ([AppGwSslPolicy20170401S](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview#appgwsslpolicy20170401s)). Tím se vynutí TLS 1,2 a silnější šifry. Další informace najdete v tématu [Konfigurace verzí zásad TLS a šifrovacích sad přes PowerShell](https://docs.microsoft.com/azure/application-gateway/application-gateway-configure-ssl-policy-powershell).
