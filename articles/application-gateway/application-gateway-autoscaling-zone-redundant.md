---
title: Automatické škálování a zónově redundantní Application Gateway v Azure (Public Preview) | Dokumentace Microsoftu
description: Tento článek obsahuje informace o omezení velikosti pro požadavek webové aplikace brány firewall a seznamy vyloučení ve službě Application Gateway pomocí webu Azure portal.
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: victorh
ms.openlocfilehash: 5d051ed049964af708056e2963f04cb478c15906
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46951504"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-public-preview"></a>Automatické škálování a zónově redundantní služba Application Gateway (Public Preview)

Služba Application Gateway a Firewall webových aplikací (WAF) jsou teď dostupné ve veřejné verzi Preview za novou skladovou Položku, která nabízí vylepšení výkonu a přidává podporu pro důležité nové funkce jako podporu pro virtuální IP adresy statické, automatické škálování a redundanci zón. Existující funkce v rámci všeobecně dostupné skladové položky i nadále podporovat novou skladovou Položku s několika výjimkami, které jsou uvedené v části známé omezení. Nové SKU zahrnují následující vylepšení:

- **Automatické škálování**: Služba Application Gateway nebo WAF nasazení na základě automatického škálování SKU můžete vertikálně navyšovat nebo snižovat podle změna provoz vzory zátěže. Automatické škálování také eliminuje nutnost zvolit počet nasazení velikost nebo instance během zřizování. Proto skladová jednotka nabízí true elasticitu. V nové skladové položky můžete provozovat Application Gateway v pevnou kapacitu (automatické škálování, zakázáno) i v režimu s povoleným automatickým Škálováním. Režim pevné kapacity je užitečné pro scénáře s konzistentní a předvídatelné zatížení. Režim automatického škálování je výhodné v aplikacích, které se objeví velké odchylky v provozu aplikací.
   
   > [!NOTE]
   > Automatické škálování není aktuálně k dispozici pro WAF SKU. Konfigurace WAF s režimem pevnou kapacitu, namísto režim automatického škálování.
- **Zóna redundance**: nasazení služba Application Gateway nebo WAF může zahrnovat více zón dostupnosti, není potřeba zřizovat a pracovat s samostatných instancí Application Gateway v každé zóně pomocí Traffic Manageru. Můžete v jedné oblasti nebo více zónách ve které jsou nasazené instance aplikační brány, tedy zajistit, že zóna selhání odolnost proti chybám. Fond back-endu pro aplikace můžete podobně distribuované napříč zónami dostupnosti.
- **Vylepšení výkonu**: nabízí automatické škálování SKU až 5 X lepší výkon přesměrování zpracování SSL ve srovnání se obecně dostupné skladové položky.
- **Rychlejší nasazení a aktualizace** automatické škálování SKU poskytuje rychlejší nasazení a aktualizace čas obecně dostupné skladové položky.
- **Virtuální IP adresy statické**: Služba application gateway virtuální IP adresy výhradně nyní podporuje statického typu virtuálních IP adres. Tím se zajistí, že virtuální IP adresy přidružené k službě application gateway nemění ani po restartování počítače.

> [!IMPORTANT]
> Automatické škálování a zónově redundantní služba application gateway SKU je aktuálně ve verzi public preview. V této verzi preview je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se používat pro produkční úlohy. Některé funkce nemusí být podporované, nebo můžou mít omezené možnosti. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Podporované oblasti
Automatické škálování SKU je k dispozici v oblastech východní USA 2, USA – střed, západní USA 2, Francie – střed, západní Evropa a jihovýchodní Asie.

## <a name="pricing"></a>Ceny
Ve verzi preview se neúčtuje. Budou se účtovat prostředků než aplikační brány, jako je trezor klíčů, virtuální počítače atd. 

## <a name="known-issues-and-limitations"></a>Známé problémy a omezení

|Problém|Podrobnosti|
|--|--|
|Fakturace|Neexistuje žádné fakturace aktuálně.|
|Režim FIPS, protokol WebSocket|Ty nejsou aktuálně podporovány.|
|Režim pouze ILB|To není aktuálně podporováno. Veřejné a režim ILB společně se nepodporuje.|
|Automatické škálování brány firewall webových aplikací|WAF nepodporuje režim automatického škálování. Kapacitu dlouhodobého režimu je podporováno.|

## <a name="next-steps"></a>Další postup
- Další informace o [Application Gateway](overview.md).
- Další informace o [Brána Firewall služby Azure](../firewall/overview.md). 

