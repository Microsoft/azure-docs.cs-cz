---
title: Podpora vysokého objemu provozu aplikační brány
description: Tento článek obsahuje pokyny ke konfiguraci Brány aplikací Azure pro podporu scénářů s vysokým objemem síťového provozu.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: caya
ms.openlocfilehash: e3f8445f68ec959ce1bb0d1ba4029807bd25907a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257423"
---
# <a name="application-gateway-high-traffic-support"></a>Podpora vysokého provozu aplikační brány 

Tento článek popisuje několik doporučených pokynů, které vám pomohou nastavit aplikační bránu pro zpracování dalšího provozu kvůli scénářům s vysokým objemem provozu, jako je například situace COVID-19. Pomocí brány aplikace s bránou webových aplikací (WEB Application Firewall) můžete škálovatelný a bezpečný způsob správy provozu webových aplikací. 

Následující návrhy vám pomohou nastavit aplikační bránu s WAF pro zpracování dalšího provozu. 

## <a name="use-the-v2-sku-over-v1-for-its-autoscaling-capabilities-and-performance-benefits"></a>Použití sku v2 přes v1 pro jeho možnosti automatického škálování a výhody výkonu
Skladová položka v2 nabízí automatické škálování, aby bylo zajištěno, že vaše aplikační brána může vertikálně navýšit kapacitu s nárůstem provozu. Nabízí také další významné výhody výkonu, jako je například 5x lepší výkon tls snížení zátěže, rychlejší nasazení a aktualizace časy, redundance zóny a další ve srovnání s v1. Další informace naleznete v naší [dokumentaci v2](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant). 

## <a name="set-maximum-instance-count-to-the-maximum-possible-125"></a>Nastavte maximální počet instancí na maximální možnou hodnotu (125)
 
Za předpokladu, že máte aplikační bránu v2 skladovou položku, nastavení maximálnípočet instancí na maximální možnou hodnotu 125 umožňuje aplikační brány horizontální navýšení kapacity podle potřeby. To umožňuje zpracovat možný nárůst provozu na vaše aplikace. Bude vám účtována pouze kapacita jednotek (CU), které používáte.  

## <a name="set-your-minimum-instance-count-based-on-your-average-cu-usage"></a>Nastavte minimální počet instancí na základě průměrného využití CU

Za předpokladu, že máte aplikační bránu v2 Skladové položky, automatické škálování trvá šest až sedm minut horizontálního navýšení kapacity. S vyšší minimální počet instancí, aplikační brána může lépe zpracovat provoz při zvýšení zatížení, protože špička v provozu nevyžaduje operaci automatického škálování.  

## <a name="alert-if-a-certain-metric-surpasses-75-of-average-cu-utilization"></a>Výstraha, pokud určitá metrika překročí 75 % průměrného využití CU 
Podrobné vysvětlení našich metrik a dalších návodů najdete v [dokumentaci k metrikám aplikační brány.](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-visualization) 

### <a name="example-setting-up-an-alert-on-75-of-average-cu-usage"></a>Příklad: Nastavení výstrahy na 75 % průměrné hospo- využití CU

Tento příklad ukazuje, jak pomocí portálu Azure nastavit výstrahu, když je dosaženo 75 % průměrného využití CU. 
1. Přejděte do **brány aplikace**.
2. Na levém panelu vyberte **metriky** na kartě **Monitorování.** 
3. Přidejte metriku pro **průměrné aktuální výpočetní jednotky**. 
![Nastavení metriky WAF](./media/application-gateway-covid-guidelines/waf-setup-metrics.png)
4. Pokud jste nastavili minimální počet instancí jako průměrné využití CU, pokračujte a nastavte upozornění, když se používá 75 % minimálních instancí. Pokud je například průměrné využití 10 CU, nastavte výstrahu na 7,5 CU. To vás upozorní, pokud se zvyšuje využití a dává vám čas reagovat. Můžete zvýšit minimum, pokud si myslíte, že tento provoz bude udržován, aby vás upozornil, že provoz může být rostoucí. 
![Nastavení výstrahy WAF](./media/application-gateway-covid-guidelines/waf-setup-monitoring-alert.png)

> [!NOTE]
> Výstrahu můžete nastavit tak, aby se vyskytovala v nižším nebo vyšším procentu využití CU v závislosti na tom, jak citlivá má být na potenciální špičky provozu.

## <a name="set-up-waf-with-geofiltering-and-bot-protection-to-stop-attacks"></a>Nastavení WAF s geofiltrováním a ochranou botů pro zastavení útoků
Pokud chcete další vrstvu zabezpečení před vaší aplikací, použijte možnosti brány aplikace WAF_v2 pro waf. SKU v2 můžete nakonfigurovat tak, aby umožňovala přístup k vašim aplikacím pouze z dané země nebo zemí. Nastavíte vlastní pravidlo WAF explicitně povolit nebo blokovat provoz na základě geolokace. Další informace naleznete v [tématu vlastní pravidla filtrování geografické konfigurace](https://docs.microsoft.com/azure/web-application-firewall/ag/geomatch-custom-rules) a [jak nakonfigurovat vlastní pravidla na WAF_v2 skladové položky brány aplikace prostřednictvím prostředí PowerShell](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules).

Povolte ochranu botů a blokujte známé špatné roboty. To by mělo snížit množství provozu dostat do aplikace. Další informace naleznete v [tématu ochrana bot s pokyny k nastavení](https://docs.microsoft.com/azure/web-application-firewall/ag/configure-waf-custom-rules).

## <a name="turn-on-diagnostics-on-application-gateway-and-waf"></a>Zapnutí diagnostiky na Application Gateway a WAF

Diagnostické protokoly umožňují zobrazit protokoly brány firewall, protokoly výkonu a protokoly přístupu. Tyto protokoly v Azure můžete použít ke správě a odstraňování problémů s aplikačními bránami. Další informace naleznete v naší [diagnostické dokumentaci](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#diagnostic-logging). 

## <a name="set-up-an-tls-policy-for-extra-security"></a>Nastavení zásad TLS pro další zabezpečení
Ujistěte se, že používáte nejnovější verzi zásad TLS[(AppGwSslPolicy20170401S).](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview#appgwsslpolicy20170401s) To vynucuje TLS 1.2 a silnější šifry. Další informace naleznete [v tématu konfigurace verzí zásad TLS a šifrovacích sad prostřednictvím prostředí PowerShell](https://docs.microsoft.com/azure/application-gateway/application-gateway-configure-ssl-policy-powershell).
