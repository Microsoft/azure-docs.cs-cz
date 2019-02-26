---
title: Azure Application Gateway funguje
description: Tento článek obsahuje informace o tom, k funguje Application Gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: 06206ececcb1a51da402c4232f19801793c1cd4a
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2019
ms.locfileid: "56807333"
---
# <a name="how-application-gateway-works"></a>Jak funguje služba Application Gateway

Služba Application Gateway je cloud určený pro kontroler doručování aplikací vrstvy 7 (HTTP) Vyrovnávání zatížení, které umožňují spravovat webový provoz v rámci vašich serverů. Kromě toho také poskytuje funkce Firewall webových aplikací (WAF), poskytující centralizovanou ochranu před běžnými typy útoků a chyby webové služby.

Pokud klient odešle požadavek HTTP ke službě Application Gateway, slouží jako reverzní proxy server a předává provoz do back-end serverů, na základě vaší konfigurace. Kromě toho Application Gateway také sledovat stav své back-end serverů a zajistí, že směruje přenos jenom do back-endů v pořádku.

![how-application-gateway-works](.\media\how-application-gateway-works\how-application-gateway-works.png)

## <a name="how-request-is-accepted"></a>Jak je přijat požadavek na

Předtím, než klient odešle žádost pro vaši bránu Application Gateway, překládá název domény Application Gateway pomocí serveru systému DNS (Domain Name). Záznam DNS se řídí tím Azure, protože aplikační brány se v doméně azure.com. Azure DNS vrátí IP adresu klienta, který je *front-endové IP adresy* služby Application Gateway. Application Gateway přijímá příchozí provoz na jeden nebo více *naslouchacích procesů*. Naslouchací proces je proces, který zkontroluje požadavky na připojení. Má nakonfigurovanou IP adresu přední stěnou, protokol a číslo portu pro připojení klientů ke službě Application Gateway. Pokud je povolené WAF, služba Application Gateway kontroluje hlavičky požadavku a text (pokud existuje) proti *pravidla firewallu webových aplikací* k určení, zda požadavek je žádost platná – v takovém případě bude probíhat jeho směrování do back-end – nebo ohrožení zabezpečení v požadavek na takovém se zablokuje.  

## <a name="how-request-is-routed"></a>Jak se směruje žádosti

Pokud se najde platný požadavek (nebo pokud není povolená WAF), *požádat o pravidlo směrování* přidružené *naslouchací proces* je vyhodnocován pro určení *back-endový fond* do což je požadavek bude směrovat. Pravidla se zpracovávají v pořadí, v jakém jsou uvedena na portálu. Na základě *požádat o pravidlo směrování* konfigurace aplikační brány rozhodne, zda chcete směrovat všechny požadavky na straně posluchače na konkrétní back-endový fond nebo směrovat na různé back-endové fondy v závislosti na cestě adresy URL nebo na *přesměrování požadavků* na jiný port nebo externí web

Jednou *back-endu* *fondu* byla vybrána, služba Application Gateway odešle požadavek na některý back-end Server nakonfigurované ve fondu, který je v pořádku (y.y.y.y). Stav serveru závisí *sondu stavu*. Pokud back-endový fond obsahuje více než jeden server, služba Application Gateway pomocí algoritmu kruhové dotazování směrovat požadavky mezi servery v pořádku, proto požadavky na servery Vyrovnávání zatížení.

Po zjištění back-end serveru Application Gateway otevře novou relaci protokolu TCP s back-end serveru, v závislosti na konfiguraci v *nastavení HTTP*. *Nastavení HTTP* je komponenta, která určuje protokol, port a další směrování související s nastavení, které jsou požadovány pro vytvoření nové relace s back-end serveru. Port a protokol použitý v nastavení HTTP určit, jestli je přenos dat mezi Application Gateway a back-end serverů šifrovaná, tedy provádění kompletního protokolu SSL, nebo bez šifrování. Při odesílání původní požadavek back-end server, služba Application Gateway respektuje všechny vlastní konfigurace z nastavení HTTP související s přepsání název hostitele, cestu, protokol; Údržba spřažení relace na základě souborů cookie, vyprázdnění, připojení vybrat název hostitele z back-endu, atd.

Application gateway také vloží tyto tři výchozí HTTP * hlavičky: x předané pro x-forwarded-proto a x předané port do žádosti předané back-endu.

Jakmile back-end server zpracuje žádost a odešle odpověď HTTP s obsahem stránky ke službě Application Gateway, Application Gateway předá odpověď klientovi, kde se zobrazí na stránce v prohlížeči.

## <a name="application-load-balancing-type"></a>Typ služby Vyrovnávání zatížení aplikace

Application Gateway můžete použít jako nástroj pro vyrovnávání zatížení interní aplikace nebo Vyrovnávání zatížení aplikace přístupem k Internetu. Application Gateway přístupem k Internetu je veřejné IP adresy. Název DNS služby Application Gateway přístupem k Internetu je veřejně přeložitelného jeho veřejné IP adresy. Internetové služby Application Gateway proto může směrovat požadavky od klientů přes Internet.

Interní aplikační brány má pouze privátní IP adresu. Název DNS pro interní aplikační brány je interně přeložit na jeho privátní IP adresu. Proto interní služby load balancer můžete pouze směrovat požadavky od klientů s přístupem k virtuální síti pro službu Application Gateway.

Všimněte si, že internetové i interní aplikační brány směrovat požadavky do back-end serverů pomocí privátních IP adres. Pokud váš prostředek back-endový fond obsahuje privátní IP adresy, konfigurace síťové karty virtuálního počítače nebo interně přeložitelnou adresu a back-endový fond je veřejný koncový bod, služba Application Gateway používá veřejnou IP adresu jeho front-endu k dosažení serveru. Pokud jste nezřídili veřejné IP adresy front-endu, jeden je přiřazen pro odchozí připojení k externí síti.

## <a name="next-steps"></a>Další postup

Po získání informací o tom, jak funguje služba Application Gateway, přejděte na [komponenty Gateway aplikace](application-gateway-components.md) pochopit jeho součástí další podrobnosti.
