---
title: Automatické škálování a zónově redundantní Application Gateway v Azure (Public Preview)
description: Tento článek představuje SKU v2 aplikace Azure, který obsahuje funkce automatického škálování a zónově redundantní.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 1/31/2019
ms.author: victorh
ms.openlocfilehash: d680634231b90e5d1cc036f43b96e05d66899e34
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55487043"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-public-preview"></a>Automatické škálování a zónově redundantní služba Application Gateway (Public Preview)

Služba Application Gateway a Firewall webových aplikací (WAF) jsou teď dostupné ve verzi Public Preview v nové skladové jednotce v2, která nabízí vylepšení výkonu a přidává podporu pro důležité nové funkce jako podporu pro virtuální IP adresy statické, automatické škálování a redundanci zón. Existující funkce v rámci všeobecně dostupné skladové položky i dál podporovaná v nové SKU v2 s několika výjimkami, které jsou uvedené v části známé omezení. Nové SKU v2 zahrnují následující vylepšení:

- **Automatické škálování**: Služba Application Gateway nebo WAF nasazení na základě automatického škálování SKU můžete vertikálně navýšit kapacitu nebo dolů podle aktuálních vzory zatížení provozu. Automatické škálování také eliminuje nutnost zvolit během zřizování velikost nasazení nebo počet instancí. Proto skladová jednotka nabízí true elasticitu. V nové skladové položky můžete provozovat Application Gateway v pevnou kapacitu (automatické škálování, zakázané) a v režimu s povoleným automatickým Škálováním. Režim pevné kapacity je užitečné pro scénáře s konzistentní a předvídatelné zatížení. Režim automatického škálování je výhodné v aplikacích, které se zobrazují velké odchylky v provozu aplikací.

   > [!NOTE]
   > Automatické škálování není aktuálně k dispozici pro WAF SKU. Konfigurace WAF s režimem pevnou kapacitu, namísto režim automatického škálování.
- **Zóna redundance**: Nasazení služby Application Gateway nebo WAF může zahrnovat více zón dostupnosti, není potřeba zřizovat a typu číselník samostatných instancí služby Application Gateway v každé zóně pomocí Traffic Manageru. Můžete v jedné oblasti nebo více zónách ve které jsou nasazené instance aplikační brány, tedy zajistit, že zóna selhání odolnost proti chybám. Fond back-endu pro aplikace můžete podobně distribuované napříč zónami dostupnosti.
- **Vylepšení výkonu**: Automatické škálování, které skladová jednotka nabízí až 5 X lepší SSL snižování zátěže výkon ve srovnání se obecně dostupné skladové položky.
- **Rychlejší nasazení a aktualizace** automatické škálování SKU poskytuje rychlejší nasazení a aktualizace čas obecně dostupné skladové položky.
- **Virtuální IP adresy statické**: Virtuální IP adresu brány aplikace nyní podporuje statický typ VIP výhradně. Tím se zajistí, že se virtuální IP adresy přidružené k službě Application Gateway nezmění ani po restartování počítače.

> [!IMPORTANT]
> Skladová položka automaticky škálované a zónově redundantní Application Gateway je aktuálně ve verzi Public Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Podporované oblasti

Automatické škálování SKU je k dispozici v následujících oblastech: eastus2, westus2, westeurope, southeastasia, centralus, francecentral, eastus, japaneast, northeurope, southcentralus, ukwest, northcentralus, westus, eastus, centralus, japanwest.

## <a name="pricing"></a>Ceny

Ve verzi preview se neúčtuje. Bude vám Účtovaná prostředků než aplikační brány, jako je trezor klíčů, virtuální počítače atd.

## <a name="known-issues-and-limitations"></a>Známé problémy a omezení

|Problém|Podrobnosti|
|--|--|
|Certifikát pro ověřování|Nepodporuje se.<br>Další informace najdete v tématu [přehled koncového šifrování protokolu SSL pomocí Application Gateway](ssl-overview.md#end-to-end-ssl-with-the-v2-sku).|
|Kombinování Standard_v2 a standardní Application Gateway ve stejné podsíti|Nepodporuje se.<br>Kromě toho pokud je povolené automatické škálování, jednu podsíť může mít pouze jednu bránu aplikace.|
|Uživatelem definovanou trasou (UDR) na podsítě Application Gateway.|Nepodporuje se|
|Skupina NSG pro rozsah portu pro příchozí spojení| -65200 až 65535. pro Standard_v2 SKU<br>-65503 až 65534 pro standardní SKU.<br>Další informace najdete v tématu [nejčastější dotazy k](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet).|
|Protokoly výkonu v diagnostice Azure|Nepodporuje se.<br>Metriky Azure je třeba použít.|
|Fakturace|Neexistuje žádné fakturace aktuálně.|
|Režim FIPS, protokol WebSocket|Ty nejsou aktuálně podporovány.|
|Režim pouze ILB|To není aktuálně podporováno. Veřejné a režim ILB společně se nepodporuje.|
|Automatické škálování brány firewall webových aplikací|WAF nepodporuje režim automatického škálování. Kapacitu dlouhodobého režimu je podporováno.|
|Integrace sledování sítě|Není podporováno ve verzi Public Preview.|

## <a name="next-steps"></a>Další postup
- [Vytvoření automatickým Škálováním, zóna redundantní služba application gateway s vyhrazenou virtuální IP adresu pomocí Azure Powershellu](tutorial-autoscale-ps.md)
- Další informace o [Application Gateway](overview.md).
- Další informace o [Brána Firewall služby Azure](../firewall/overview.md).