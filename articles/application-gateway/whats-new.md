---
title: Co je nového v Azure Application Gateway
description: Seznamte se s novinkami v Azure Application Gateway, jako jsou například nejnovější poznámky k verzi, známé problémy, opravy chyb, zastaralé funkce a nadcházející změny.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 06/10/2020
ms.author: victorh
ms.openlocfilehash: a98a06dedf1d0ed9b92cc2028dfc208ff26df056
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86517616"
---
# <a name="whats-new-in-azure-application-gateway"></a>Co je nového v Azure Application Gateway?

Azure Application Gateway se průběžně aktualizuje. V tomto článku najdete informace o tom, jak se má aktualizovat pomocí nejnovějšího vývoje.

- Nejnovější verze
- Známé problémy
- Opravy chyb
- Zastaralé funkce

## <a name="new-features"></a>Nové funkce

|Funkce  |Popis  |Datum přidání  |
|---------|---------|---------|
| Názvy hostitelů se zástupnými znaky v posluchačích (Preview) | Nyní můžete definovat názvy hostitelů se zástupnými znaky v rámci naslouchacího procesu pro více webů a až 5 názvů hostitelů na naslouchací proces. Další informace najdete v tématech [názvy hostitelů se zástupnými znaky v naslouchacího procesu (Preview)](multiple-site-overview.md#wildcard-host-names-in-listener-preview) a [hostování více webů v Application Gateway pomocí Azure Portal](create-multiple-sites-portal.md) pro ste průvodce. | Červenec 2020 |
| Přepsání adresy URL (Preview) | Nyní můžete přepsat cestu adresy URL a parametry řetězce dotazu pomocí přepsání adresy URL. Další informace najdete v tématu přepis [hlaviček a adres URL protokolu HTTP](rewrite-http-headers-url.md) a další informace najdete [v tématu adresa URL pro přepis v Application Gateway pomocí Azure Portal](rewrite-url-portal.md) pro podrobného průvodce. | Červenec 2020 |
| Vlastní port pro sondy stavu | SKU Application Gateway v2 nyní poskytuje možnost poskytnout vlastní port v konfiguraci sondy stavu. Další informace najdete v tématu [Přehled stavu testu](application-gateway-probe-overview.md) . | Červenec 2020 |
| Application Gateway (AGIC) AKS Controller (verze Preview) |Řadič pro příchozí Application Gateway se teď dá nasadit jako nativní doplněk AKS v jednom řádku prostřednictvím Azure CLI. AKS doplněk umožňuje, aby se AGIC stala plně spravovaná služba a pořád běžela v clusteru AKS zákazníka. Další informace najdete v tématu [AGIC – rozdíly v doplňcích](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on). |Červen 2020 |
| Trasy definované uživatelem (UDR) ve verzi v2 (Preview) |Trasy definované uživatelem jsou nyní podporovány v některých scénářích u SKU Application Gateway v2. Další informace najdete v tématu [Přehled konfigurace Application Gateway](configuration-overview.md#user-defined-routes-supported-on-the-application-gateway-subnet). |Březen 2020 |
|Změny souborů cookie spřažení |Pokud je povoleno spřažení na základě souborů cookie, Application Gateway kromě existujícího souboru cookie ApplicationGatewayAffinity vloží jiný stejný soubor cookie s názvem *ApplicationGatewayAffinityCORS* . *ApplicationGatewayAffinityCORS* má přidaných dva další atributy (*SameSite = None; Secure*), aby se rychlé relace zachovaly i pro požadavky na více zdrojů. Další informace najdete v tématu [Application Gateway spřažení na základě souborů cookie](configuration-overview.md#cookie-based-affinity) . |Únor 2020 |
|Vylepšení testu paměti |Díky vylepšením vlastních sond ve službě Application Gateway v2 SKU jsme zjednodušili [konfiguraci testu paměti](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#create-probe-for-application-gateway-v2-sku), usnadnili jsme [testy stavu back-endu na vyžádání](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#test-backend-health-with-the-probe) a přidali jsme [Další diagnostické informace](https://docs.microsoft.com/azure/application-gateway/application-gateway-backend-health-troubleshooting#error-messages) , které vám pomůžou při řešení problémů se stavem back-endu.  |Říjen 2019 |
|Další metriky |Přidali jsme následující nové metriky, které vám pomůžou monitorovat SKU Application Gateway v2: [metriky související s časováním](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#timing-metrics), stav odpovědi back-endu, přijaté bajty, odeslané bajty, protokol TLS klienta a aktuální výpočetní jednotky. Podívejte se [na metriky podporované Application Gateway v2 SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-supported-by-application-gateway-v2-sku). |Srpen 2019 |
|Vlastní pravidla WAF |Application Gateway WAF_v2 teď podporuje vytváření vlastních pravidel. Viz [Application Gateway vlastní pravidla](custom-waf-rules-overview.md). |Červen 2019 |
|Automatické škálování, redundance zóny, statická VIP podpora GA |Obecná dostupnost pro SKU v2, která podporuje automatické škálování, redundanci zóny, zvýšení výkonu, statické VIP Key Vault, přepisování hlaviček. Viz [Application Gateway dokumentace automatického škálování](application-gateway-autoscaling-zone-redundant.md). |Duben 2019 |
|Integrace Key Vault |Application Gateway teď podporuje integraci s Key Vault (ve verzi Public Preview) pro certifikáty serveru, které jsou připojené k naslouchacím procesům s povoleným protokolem HTTPS. Přečtěte si téma [ukončení protokolu TLS s certifikáty Key Vault](key-vault-certs.md). |Duben 2019 |
|CRUD a Přepisy záhlaví     |Nyní můžete přepsat hlavičky HTTP. Další informace najdete v tématu [kurz: vytvoření služby Application Gateway a přepsání hlaviček protokolu HTTP](tutorial-http-header-rewrite-powershell.md) .|Prosinec 2018|
|WAF konfigurace a seznam vyloučení     |Přidali jsme další možnosti, které vám pomůžou nakonfigurovat WAF a omezit falešně pozitivní výsledky. Další informace najdete v tématu [omezení velikosti požadavků a seznamů vyloučení v bráně firewall webové aplikace](application-gateway-waf-configuration.md).|Prosinec 2018|
|Automatické škálování, redundance zóny, podpora statických virtuálních IP adres      |V případě SKU verze V2 existuje mnoho vylepšení, jako je automatické škálování, lepší výkon a další. Další informace najdete v tématu [co je Azure Application Gateway?](overview.md) .|Září 2018|
|Vyprázdnění připojení     |Vyprazdňování připojení umožňuje řádným odebráním členů ze svých back-end fondů. Další informace najdete v tématu [vyprazdňování připojení](features.md#connection-draining).|Září 2018|
|Vlastní chybové stránky     |Pomocí vlastních chybových stránek můžete vytvořit chybovou stránku v rámci formátu zbývajících webů. Pokud to chcete povolit, přečtěte si téma [vytvoření Application Gateway vlastní chybové stránky](custom-error.md).|Září 2018|
|Vylepšení metrik     |Můžete získat lepší přehled o stavu Application Gateway s rozšířenými metrikami. Pokud chcete na svém Application Gateway povolit metriky, přečtěte si téma [stav back-endu, diagnostické protokoly a metriky pro Application Gateway](application-gateway-diagnostics.md).|Červen 2018|

## <a name="next-steps"></a>Další kroky

Další informace o Azure Application Gateway najdete v tématu [co je azure Application Gateway?](overview.md)
