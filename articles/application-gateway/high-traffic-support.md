---
title: Podpora objemu Application Gateway vysokého provozu
description: Tento článek poskytuje pokyny ke konfiguraci Azure Application Gateway v podpoře scénářů velkého objemu síťových přenosů.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: caya
ms.openlocfilehash: d8940d791920daca6ef0af186a4bb5e17009637b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100586111"
---
# <a name="application-gateway-high-traffic-support"></a>Podpora vysokého provozu služby Application Gateway

>[!NOTE]
> Tento článek popisuje několik navrhovaných pokynů, které vám pomůžou nastavit Application Gateway pro zpracování dalších přenosů dat v případě vysokého objemu provozu, ke kterému může dojít. Prahové hodnoty výstrah jsou čistě návrhy a obecné v podstatě. Uživatelé můžou určit prahové hodnoty výstrah na základě jejich zatížení a očekávání využití.

Pomocí Application Gateway s firewallem webových aplikací (WAF) můžete škálovatelný a zabezpečený způsob správy provozu do webových aplikací.

Je důležité škálovat Application Gateway v závislosti na provozu a bitové kopii vyrovnávací paměti, abyste se připravili na jakýkoliv nárůst provozu nebo špičky a minimalizovali dopad, který může mít vaše technologie QoS. Následující návrhy vám pomůžou nastavit Application Gateway s využitím WAF pro zpracování dalšího provozu.

Úplný seznam metrik nabízených nástrojem Application Gateway najdete v [dokumentaci k metrikám](./application-gateway-metrics.md) . Informace o tom, jak nastavit výstrahy pro metriky, najdete v tématu [vizualizace metriky](./application-gateway-metrics.md#metrics-visualization) v Azure Portal a v [dokumentaci ke službě Azure monitor](../azure-monitor/alerts/alerts-metric.md) .

## <a name="scaling-for-application-gateway-v1-sku-standardwaf-sku"></a>Škálování pro Application Gateway verze V1 SKU (standardní/WAF SKU)

### <a name="set-your-instance-count-based-on-your-peak-cpu-usage"></a>Nastavte počet instancí na základě špičkového využití procesoru.
Pokud používáte bránu SKU V1, budete mít možnost nastavit Application Gateway až 32 instancí pro škálování. Prohlédněte si využití CPU Application Gateway v minulosti za jeden měsíc pro všechny špičky nad 80%, je k dispozici jako metrika, kterou můžete monitorovat. Doporučujeme, abyste nastavili počet instancí podle vašeho špičkového využití a od 10% do 20% dodatečné vyrovnávací paměti pro všechny špičky provozu.

:::image type="content" source="./media/application-gateway-covid-guidelines/v1-cpu-utilization-inline.png" alt-text="Metriky využití procesoru v1" lightbox="./media/application-gateway-covid-guidelines/v1-cpu-utilization-exp.png":::

### <a name="use-the-v2-sku-over-v1-for-its-autoscaling-capabilities-and-performance-benefits"></a>Pro své možnosti automatického škálování a výhody výkonu použijte SKU verze V2 přes v1.
SKU verze 2 nabízí automatické škálování, aby bylo zajištěno, že se vaše Application Gateway může vertikálně škálovat při zvyšování provozu. Nabízí taky další významné výkonnostní výhody, jako je pětinásobné lepší výkon při snižování zátěže TLS, rychlejší nasazení a časy aktualizace, redundance zóny a další v porovnání s v1. Další informace najdete v dokumentaci k verzi [v2](./application-gateway-autoscaling-zone-redundant.md) a v [dokumentaci k migraci](./migrate-v1-v2.md) V1 na v2 se dozvíte, jak migrovat stávající brány SKU V1 na verzi v2 SKU. 

## <a name="autoscaling-for-application-gateway-v2-sku-standard_v2waf_v2-sku"></a>Automatické škálování pro skladovou položku služby Application Gateway v2 (Standard_v2 nebo WAF_v2)

### <a name="set-maximum-instance-count-to-the-maximum-possible-125"></a>Nastavit maximální počet instancí na maximální možnou hodnotu (125)
 
V případě SKU Application Gateway v2 je nastavení maximální počet instancí na maximální možnou hodnotu 125 umožňuje Application Gateway horizontálního navýšení kapacity v případě potřeby. To umožňuje, aby mohla zpracovávat možné zvýšení provozu do vašich aplikací. Budou se vám účtovat jenom jednotky kapacity (kapacitní jednotky), které použijete. 

Zkontrolujte velikost podsítě a dostupný počet IP adres v podsíti a nastavte maximální počet instancí na základě těchto hodnot. Pokud vaše podsíť nemá dost místa pro vyžádání, bude nutné znovu vytvořit bránu ve stejné nebo jiné podsíti, která má dostatečnou kapacitu. 

:::image type="content" source="./media/application-gateway-covid-guidelines/v2-autoscaling-max-instances-inline.png" alt-text="V2 konfigurace automatického škálování" lightbox="./media/application-gateway-covid-guidelines/v2-autoscaling-max-instances-exp.png":::

### <a name="set-your-minimum-instance-count-based-on-your-average-compute-unit-usage"></a>Nastavte minimální počet instancí na základě průměrného využití výpočetních jednotek.

U SKU Application Gateway v2 má automatické škálování trvat šest až sedm minut, než se horizontální navýšení kapacity a zřídí další sada instancí připravených k přenosu provozu. Až pak dojde k krátké špičkě v provozu, vaše existující instance brány můžou dosáhnout zátěže a to může způsobit neočekávanou latenci nebo ztrátu provozu. 

Doporučuje se nastavit minimální počet instancí na optimální úroveň. Pokud například požadujete, aby se instance 50 pro zpracování provozu při vrcholu zátěže, pak nastavení minimálního počtu 25 až 30 je dobré nápad místo na <10, takže i v případě, že dojde k krátkým nárůstům provozu, Application Gateway by je bylo možné zpracovat a poskytnout dostatek času na reakci a projevení.

Podívejte se na metriku výpočetní jednotky za uplynulý měsíc. Metrika výpočetních jednotek je reprezentace využití procesoru vaší brány a na základě špičky vydělené 10 můžete nastavit minimální počet požadovaných instancí. Všimněte si, že 1 instance služby Application Gateway může zpracovat minimálně 10 výpočetních jednotek.

:::image type="content" source="./media/application-gateway-covid-guidelines/compute-unit-metrics-inline.png" alt-text="Verze V2 – metriky výpočetní jednotky" lightbox="./media/application-gateway-covid-guidelines/compute-unit-metrics-exp.png":::

## <a name="manual-scaling-for-application-gateway-v2-sku-standard_v2waf_v2"></a>Ruční škálování pro SKU Application Gateway v2 (Standard_v2/WAF_v2)

### <a name="set-your-instance-count-based-on-your-peak-compute-unit-usage"></a>Nastavte počet instancí na základě využití ve špičce výpočetní jednotky. 

Na rozdíl od automatického škálování se v ručním škálování musíte ručně nastavit počet instancí aplikační brány na základě požadavků na provoz. Doporučujeme, abyste nastavili počet instancí podle vašeho špičkového využití a od 10% do 20% dodatečné vyrovnávací paměti pro všechny špičky provozu. Pokud například váš provoz vyžaduje 50 instancí ve špičce, zřídí 55 až 60 instancí, které budou zpracovávat neočekávané špičky provozu, ke kterým může dojít.

Podívejte se na metriku výpočetní jednotky za uplynulý měsíc. Metrika výpočetních jednotek je reprezentace využití procesoru vaší brány a na základě špičky vydělené 10. můžete nastavit počet požadovaných instancí, protože 1 instance aplikační brány může zpracovávat minimálně 10 výpočetních jednotek.

## <a name="monitoring-and-alerting"></a>Monitorování a upozorňování

Chcete-li dostávat oznámení o jakémkoli provozu nebo anomáliích využití, můžete nastavit výstrahy pro určité metriky. Úplný seznam metrik nabízených nástrojem Application Gateway najdete v [dokumentaci k metrikám](./application-gateway-metrics.md) . Informace o tom, jak nastavit výstrahy pro metriky, najdete v tématu [vizualizace metriky](./application-gateway-metrics.md#metrics-visualization) v Azure Portal a v [dokumentaci ke službě Azure monitor](../azure-monitor/alerts/alerts-metric.md) .

## <a name="alerts-for-application-gateway-v1-sku-standardwaf"></a>Výstrahy pro Application Gateway v1 SKU (Standard/WAF)

### <a name="alert-if-average-cpu-utilization-crosses-80"></a>Výstraha v případě, že průměrné využití procesoru překračuje 80%

Za normálních podmínek by využití procesorů nemělo pravidelně překračovat 90 %, protože to může způsobit latenci na webech hostovaných za službou Application Gateway a narušení klientského prostředí. Můžete nepřímo řídit nebo zdokonalovat využití procesoru změnou konfigurace Application Gateway zvýšením počtu instancí nebo přesunutím na větší velikost SKU nebo provedením obou. Nastavte výstrahu, pokud metrika využití procesoru překročí 80% průměr.

### <a name="alert-if-unhealthy-host-count-crosses-threshold"></a>Výstraha, pokud je prahová hodnota počtu hostitelů v pořádku překročena

Tato metrika indikuje počet back-end serverů, které Aplikační brána nemůže úspěšně sondovat. Tím zachytíte problémy, které se instance aplikační brány nemůžou připojit k back-endu. Výstraha, pokud toto číslo překročí 20% kapacitu back-endu. Například Pokud máte ve svém back-end fondu 30 back-end serverů, nastavte výstrahu v případě, že počet hostitelů, který není v pořádku, překročí 6.

### <a name="alert-if-response-status-4xx-5xx-crosses-threshold"></a>Výstraha, pokud stav odpovědi (4xx, 5xx) překračuje prahovou hodnotu 

Vytvořit výstrahu, když Application Gateway stav odpovědi je 4xx nebo 5xx. Kvůli přechodným problémům by mohlo dojít k příležitostnému 4xx nebo 5xx reakci. Bránu v produkčním prostředí byste měli sledovat, pokud chcete určit statickou prahovou hodnotu nebo pro výstrahu použít dynamickou prahovou hodnotu.

### <a name="alert-if-failed-requests-crosses-threshold"></a>Výstraha, pokud neúspěšné požadavky překračují prahovou hodnotu 

Vytvořit výstrahu v případě, že metrika neúspěšných požadavků překračuje prahovou hodnotu Bránu v produkčním prostředí byste měli sledovat, pokud chcete určit statickou prahovou hodnotu nebo pro výstrahu použít dynamickou prahovou hodnotu.

### <a name="example-setting-up-an-alert-for-more-than-100-failed-requests-in-the-last-5-minutes"></a>Příklad: nastavení výstrahy pro více než 100 neúspěšných žádostí za posledních 5 minut

Tento příklad ukazuje, jak použít Azure Portal k nastavení výstrahy v případě, že počet neúspěšných žádostí za posledních 5 minut je větší než 100.
1. Přejděte na **Application Gateway**.
2. Na levém panelu vyberte na kartě **monitorování** možnost **metriky** . 
3. Přidejte metriku pro **neúspěšné žádosti**.
4. Klikněte na **nové pravidlo výstrahy** a definujte podmínku a akce.
5. Kliknutím na **vytvořit pravidlo výstrahy** vytvoříte a povolíte výstrahu.

:::image type="content" source="./media/application-gateway-covid-guidelines/create-alerts-inline.png" alt-text="V2 vytvořit výstrahy" lightbox="./media/application-gateway-covid-guidelines/create-alerts-exp.png":::

## <a name="alerts-for-application-gateway-v2-sku-standard_v2waf_v2"></a>Výstrahy pro SKU Application Gateway v2 (Standard_v2/WAF_v2)

### <a name="alert-if-compute-unit-utilization-crosses-75-of-average-usage"></a>Výstraha, pokud využití výpočetní jednotky překračuje 75% průměrného využití 

Výpočetní jednotka je míra využití výpočetní kapacity vašich Application Gateway. Podívejte se na průměrné využití výpočetních jednotek za poslední měsíc a nastavte upozornění, pokud to 75% z nich. Pokud například průměrné využití je 10 výpočetních jednotek, nastavte upozornění na 7,5 kapacitní jednotky. Tato výstraha vás upozorní, pokud se využití zvyšuje a poskytuje čas na odpověď. Pokud si myslíte, že se tento provoz bude zabývat, budete moct upozornit na to, že se provoz může zvýšit. Podle potřeby horizontální navýšení kapacity můžete provést podle výše uvedených návrhů škálování.

### <a name="example-setting-up-an-alert-on-75-of-average-cu-usage"></a>Příklad: nastavení výstrahy na 75% průměrného množství CU využití

V tomto příkladu se dozvíte, jak použít Azure Portal k nastavení upozornění, když je dosaženo 75% průměrného využití. 
1. Přejděte na **Application Gateway**.
2. Na levém panelu vyberte na kartě **monitorování** možnost **metriky** . 
3. Přidejte metriku pro **průměrné aktuální výpočetní jednotky**. 
4. Pokud jste nastavili minimální počet instancí, který bude odpovídat průměrnému využití, pokračujte a nastavte výstrahu, když se vaše minimální instance 75% využije. Pokud je například průměrné využití 10 kapacitní jednotky, nastavte upozornění na 7,5 kapacitní jednotky. Tato výstraha vás upozorní, pokud se využití zvyšuje a poskytuje čas na odpověď. Pokud si myslíte, že se tento provoz bude zabývat, budete moct upozornit na to, že se provoz může zvýšit. 

:::image type="content" source="./media/application-gateway-covid-guidelines/compute-unit-alert-inline.png" alt-text="V2 výstrahy výpočetních jednotek v2" lightbox="./media/application-gateway-covid-guidelines/compute-unit-alert-exp.png":::

> [!NOTE]
> V závislosti na tom, jakým způsobem chcete mít k dispozici možnosti provozu, můžete nastavit, aby se tato výstraha nastavila na nižší nebo vyšší procento využití.

### <a name="alert-if-capacity-unit-utilization-crosses-75-of-peak-usage"></a>Výstraha, pokud využití kapacity jednotek překračuje 75% využití ve špičce 

Jednotky kapacity představuje celkové využití brány z hlediska propustnosti, výpočtů a počtu připojení. Prohlédněte si maximální využití kapacitní jednotky za poslední měsíc a nastavte upozornění, pokud to 75% z nich. Pokud je například maximální využití 100 jednotek kapacity, nastavte upozornění na 75 kapacitní jednotky. Podle potřeby horizontální navýšení kapacity proveďte podle výše uvedených dvou návrhů.

### <a name="alert-if-unhealthy-host-count-crosses-threshold"></a>Výstraha, pokud je prahová hodnota počtu hostitelů v pořádku překročena 

Tato metrika indikuje počet back-end serverů, které Aplikační brána nemůže úspěšně sondovat. Tím zachytíte problémy, které se instance aplikační brány nemůžou připojit k back-endu. Výstraha, pokud toto číslo překročí 20% kapacitu back-endu. Například Pokud máte ve svém back-end fondu 30 back-end serverů, nastavte výstrahu v případě, že počet hostitelů, který není v pořádku, překročí 6.

### <a name="alert-if-response-status-4xx-5xx-crosses-threshold"></a>Výstraha, pokud stav odpovědi (4xx, 5xx) překračuje prahovou hodnotu 

Vytvořit výstrahu, když Application Gateway stav odpovědi je 4xx nebo 5xx. Kvůli přechodným problémům by mohlo dojít k příležitostnému 4xx nebo 5xx reakci. Bránu v produkčním prostředí byste měli sledovat, pokud chcete určit statickou prahovou hodnotu nebo pro výstrahu použít dynamickou prahovou hodnotu.

### <a name="alert-if-failed-requests-crosses-threshold"></a>Výstraha, pokud neúspěšné požadavky překračují prahovou hodnotu 

Vytvořit výstrahu v případě, že metrika neúspěšných požadavků překračuje prahovou hodnotu Bránu v produkčním prostředí byste měli sledovat, pokud chcete určit statickou prahovou hodnotu nebo pro výstrahu použít dynamickou prahovou hodnotu.

### <a name="alert-if-backend-last-byte-response-time-crosses-threshold"></a>Výstraha, pokud doba odezvy back-endu uplynula při překročení prahové hodnoty 

Tato metrika indikuje časový interval mezi zahájením navázání připojení k back-endu serveru a přijetím posledního bajtu těla odpovědi. Vytvoří výstrahu, pokud je latence odezvy back-endu větší než určitá prahová hodnota od obvyklého. Například nastavte, aby se zobrazila výstraha v případě, že se latence odezvy na back-end zvyšuje o více než 30% z běžné hodnoty.

### <a name="alert-if-application-gateway-total-time-crosses-threshold"></a>Výstraha, pokud Application Gateway celkový čas překročení prahové hodnoty

Toto je interval od času, kdy Application Gateway obdrží první bajt požadavku HTTP na čas, kdy byl klientovi odeslán poslední bajt odpovědi. By měl vytvořit výstrahu, pokud je latence odezvy back-endu větší než určitá prahová hodnota od obvyklého. Například může nastavit, aby se zobrazila výstraha v případě, že celková latence roste o více než 30% z běžné hodnoty.

## <a name="set-up-waf-with-geo-filtering-and-bot-protection-to-stop-attacks"></a>Nastavení WAF pomocí geografického filtrování a ochrany robotů k zastavení útoků
Pokud chcete před aplikací použít další vrstvu zabezpečení, použijte Application Gateway WAF_v2 SKU pro funkce WAF. SKLADOVOU položku v2 můžete nakonfigurovat tak, aby povolovala přístup pouze k vašim aplikacím z dané země nebo oblasti nebo zemí/oblastí. Nastavíte vlastní pravidlo WAF k explicitnímu povolení nebo blokování provozu na základě geografického umístění. Další informace najdete v tématech [vlastní pravidla geografického filtrování](../web-application-firewall/ag/geomatch-custom-rules.md) a [Postup konfigurace vlastních pravidel pro Application Gateway WAF_v2 SKU prostřednictvím PowerShellu](../web-application-firewall/ag/configure-waf-custom-rules.md).

Povolí ochranu robotů blokující známé chybné roboty. To by mělo snížit objem provozu, který se bude připravovat do vaší aplikace. Další informace najdete v tématu [ochrana robota s pokyny k instalaci](../web-application-firewall/ag/configure-waf-custom-rules.md).

## <a name="turn-on-diagnostics-on-application-gateway-and-waf"></a>Zapnutí diagnostiky na Application Gateway a WAF

Diagnostické protokoly umožňují zobrazit protokoly brány firewall, protokoly výkonu a protokoly přístupu. Pomocí těchto protokolů v Azure můžete spravovat a řešit potíže s aplikačními bránami. Další informace najdete v naší [dokumentaci k diagnostice](./application-gateway-diagnostics.md#diagnostic-logging). 

## <a name="set-up-an-tls-policy-for-extra-security"></a>Nastavení zásad TLS pro další zabezpečení
Ujistěte se, že používáte nejnovější verzi zásad TLS ([AppGwSslPolicy20170401S](./application-gateway-ssl-policy-overview.md#appgwsslpolicy20170401s)). Tím se vynutí TLS 1,2 a silnější šifry. Další informace najdete v tématu [Konfigurace verzí zásad TLS a šifrovacích sad přes PowerShell](./application-gateway-configure-ssl-policy-powershell.md).