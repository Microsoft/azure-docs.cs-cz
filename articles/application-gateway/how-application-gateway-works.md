---
title: Azure Application Gateway funguje
description: Tento článek obsahuje informace o tom, k funguje Application Gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: d37114fda7f442a5fa077c8dde9fd8aec3ac4378
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2019
ms.locfileid: "57317097"
---
# <a name="how-application-gateway-works"></a>Jak funguje služba Application Gateway

Tento článek vysvětluje, jak službu application gateway přijímá příchozí požadavky a směruje je do back-endu.

![how-application-gateway-works](.\media\how-application-gateway-works\how-application-gateway-works.png)

## <a name="how-request-is-accepted"></a>Jak je přijat požadavek na

Předtím, než klient odešle žádost pro vaši bránu application gateway, překládá název domény application gateway pomocí serveru systému DNS (Domain Name). Záznam DNS se řídí tím Azure, protože aplikační brány se v doméně azure.com. Azure DNS vrátí IP adresu klienta, který je *front-endové IP adresy* služby Application Gateway. Application gateway přijímá příchozí provoz na jeden nebo více *naslouchacích procesů*. Naslouchací proces je logická entita, která kontroluje pro žádosti o připojení. Má nakonfigurovanou IP adresu přední stěnou, protokol a číslo portu pro připojení klientů ke službě application gateway. Pokud je povolený Firewall webových aplikací (WAF), služba Application Gateway kontroluje hlavičky požadavku a text (pokud existuje) proti *pravidla firewallu webových aplikací* k určení, zda požadavek je žádost platná – v takovém případě bude směrovat do back-end – nebo bezpečnostní hrozbu, ve kterém se zablokuje případ požadavku.  

Služba Application gateway může sloužit jako nástroj pro vyrovnávání zatížení interní aplikace nebo Vyrovnávání zatížení aplikace přístupem k Internetu. Přístupem k Internetu application gateway poskytuje veřejné IP adresy. Název DNS služby application gateway přístupem k Internetu je veřejně přeložitelného jeho veřejné IP adresy. Internetové služby application Gateway proto může směrovat požadavky od klientů přes Internet. Interní aplikační brány mají pouze privátní IP adresu. Název DNS pro interní aplikační brány je veřejně přeložitelného na jeho privátní IP adresu. Interní služby load balancer proto pouze směrovat požadavky od klientů s přístupem k síti pro službu application gateway. Internetové i interní aplikační brány směrovat požadavky do back-end serverů pomocí privátních IP adres. Back-end serverů, proto není nutné veřejné IP adresy pro příjem požadavků z interní nebo služby Application Gateway přístupem k Internetu.

## <a name="how-request-is-routed"></a>Jak se směruje žádosti

Pokud se najde platný požadavek (nebo pokud není povolená WAF), *požádat o pravidlo směrování* přidružené *naslouchací proces* je vyhodnocován pro určení *back-endový fond* do což je požadavek bude směrovat. Pravidla se zpracovávají v pořadí, v jakém jsou uvedena na portálu. Na základě *požádat o pravidlo směrování* konfigurace aplikační brány rozhodne, zda chcete směrovat všechny požadavky na straně posluchače na konkrétní back-endový fond nebo směrovat na různé back-endové fondy v závislosti na cestě adresy URL nebo na *přesměrování požadavků* na jiný port nebo externí web

Jednou *back-endu* *fondu* byla vybrána, služba application gateway odešle požadavek na některý back-end Server nakonfigurované ve fondu, který je v pořádku (y.y.y.y). Stav serveru závisí *sondu stavu*. Pokud back-endový fond obsahuje více než jeden server, služba application gateway pomocí algoritmu kruhové dotazování směrovat požadavky mezi servery v pořádku, proto požadavky na servery Vyrovnávání zatížení.

Po back-end serveru Aplikační brána otevře novou relaci protokolu TCP s back-end serveru, v závislosti na konfiguraci v *nastavení HTTP*. *Nastavení HTTP* je komponenta, která určuje protokol, port a další směrování související s nastavení, které jsou požadovány pro vytvoření nové relace s back-end serveru. Port a protokol použitý v nastavení HTTP určit, jestli je přenos dat mezi servery brány a back-endu aplikace šifrovaná, tedy provádění kompletního protokolu SSL, nebo bez šifrování. Při odesílání původní požadavek back-end server, služba application gateway respektuje všechny vlastní konfigurace z nastavení HTTP související s přepsání název hostitele, cestu, protokol; Údržba spřažení relace na základě souborů cookie, vyprázdnění, připojení vybrat název hostitele z back-endu, atd.

Interní aplikační brány má pouze privátní IP adresu. Název DNS pro interní aplikační brány je interně přeložit na jeho privátní IP adresu. Proto interní služby load balancer můžete pouze směrovat požadavky od klientů s přístupem k virtuální síti pro službu Application Gateway.

Všimněte si, že internetové i interní aplikační brány směrovat požadavky do back-end serverů pomocí privátních IP adres. Pokud váš prostředek back-endový fond obsahuje privátní IP adresy, konfigurace síťové karty virtuálního počítače nebo interně přeložitelnou adresu a back-endový fond je veřejný koncový bod, služba Application Gateway používá veřejnou IP adresu jeho front-endu k dosažení serveru. Pokud jste nezřídili veřejné IP adresy front-endu, jeden je přiřazen pro odchozí připojení k externí síti.


## <a name="next-steps"></a>Další postup

Po získání informací o tom, jak funguje služba application gateway, naleznete v tématu [komponenty gateway aplikace](application-gateway-components.md) pochopit jeho součástí další podrobnosti.
