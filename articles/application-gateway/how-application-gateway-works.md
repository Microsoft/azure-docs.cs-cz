---
title: Jak funguje aplikační brána
description: Tento článek obsahuje informace o tom, jak brána aplikace přijímá příchozí požadavky a směruje je do back-endu.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: absha
ms.openlocfilehash: 84a7bdfb9f8f7c741140cbe2086149dff90db211
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132970"
---
# <a name="how-an-application-gateway-works"></a>Jak funguje aplikační brána

Tento článek vysvětluje, jak brána aplikace přijímá příchozí požadavky a směruje je do back-endu.

![Jak aplikační brána přijímá požadavek](./media/how-application-gateway-works/how-application-gateway-works.png)

## <a name="how-an-application-gateway-accepts-a-request"></a>Jak aplikační brána přijímá požadavek

1. Před odesláním požadavku na aplikační bránu klienta překládá název domény aplikační brány pomocí serveru DNS (Domain Name System). Azure řídí položku DNS, protože všechny aplikační brány jsou v azure.com doméně.

2. Azure DNS vrátí IP adresu klientovi, což je front-endIP adresa aplikační brány.

3. Aplikační brána přijímá příchozí provoz na jeden nebo více naslouchacích procesy. Naslouchací proces je logická entita, která kontroluje požadavky na připojení. Je nakonfigurován s front-endovou IP adresou, protokolem a číslem portu pro připojení klientů k aplikační bráně.

4. Pokud je brána firewall webové aplikace (WAF) používána, brána aplikace zkontroluje hlavičky požadavku a tělo, pokud je k dispozici, proti pravidlům WAF. Tato akce určuje, zda je požadavek platný požadavek nebo ohrožení zabezpečení. Pokud je požadavek platný, je směrován do back-endu. Pokud požadavek není platný a WAF je v režimu prevence, je blokován jako bezpečnostní hrozba. Pokud je v režimu zjišťování, požadavek je vyhodnocena a zaznamenána, ale stále předána back-endový server.

Azure Application Gateway se dá použít jako interní vyrovnávání zatížení aplikace nebo jako internetový aplikační vyrovnávání zatížení. Internetová aplikační brána používá veřejné IP adresy. Název DNS internetové aplikační brány je veřejně řešitelný na veřejnou IP adresu. V důsledku toho mohou internetové aplikační brány směrovat požadavky klientů na internet.

Interní aplikační brány používají pouze privátní IP adresy. Pokud používáte vlastní nebo [soukromou zónu DNS](https://docs.microsoft.com/azure/dns/private-dns-overview), název domény by měl být vnitřně řešitelný na privátní IP adresu aplikační brány. Proto interní nástroje pro vyrovnávání zatížení můžete směrovat pouze požadavky od klientů s přístupem k virtuální síti pro aplikační bránu.

## <a name="how-an-application-gateway-routes-a-request"></a>Způsob, jakým aplikační brána směruje požadavek

Pokud je požadavek platný a není blokován waf, brána aplikace vyhodnotí pravidlo směrování požadavku, který je přidružen k naslouchací proces. Tato akce určuje, do kterého back-endového fondu má být požadavek směrován.

Na základě pravidla směrování požadavku určuje aplikační brána, zda mají být směrovány všechny požadavky na naslouchací proces do konkrétního back-endového fondu, směrovat požadavky do různých back-endových fondů na základě cesty URL nebo přesměrovat požadavky na jiný port nebo externí síť.
>[!NOTE]
>Pravidla jsou zpracována v pořadí, v jakém jsou uvedena na portálu pro skladovou položku v1. 

Když brána aplikace vybere back-endový fond, odešle požadavek na jeden z vpořádku back-endové servery ve fondu (y.y.y.y). Stav serveru je určen sondou stavu. Pokud back-endový fond obsahuje více serverů, aplikace brána používá algoritmus kruhového dotazování ke směrování požadavků mezi servery v pořádku. Toto vyrovnává zatížení požadavky na serverech.

Poté, co brána aplikace určí back-endový server, otevře novou relaci TCP s back-endovým serverem založeným na nastavení protokolu HTTP. Nastavení protokolu HTTP určují nastavení protokolu, portu a dalších nastavení souvisejících se směrováním, která jsou nutná k vytvoření nové relace s back-endovým serverem.

Port a protokol použitý v nastavení protokolu HTTP určují, zda je provoz mezi aplikační bránou a servery back-end šifrován (tedy provedení montovny TLS od konce) nebo zda není zašifrován.

Když aplikační brána odešle původní požadavek na back-endový server, respektuje všechny vlastní konfigurace provedené v nastavení PROTOKOLU HTTP související s přepsáním názvu hostitele, cesty a protokolu. Tato akce udržuje spřažení relací na základě souborů cookie, vyprázdnění připojení, výběr názvu hostitele z back-endu a tak dále.

 >[!NOTE]
>Pokud back-end fondu:
> - **Je veřejný koncový bod**, brána aplikace používá jeho front-end veřejné IP k dosažení serveru. Pokud neexistuje front-endová veřejná IP adresa, je přiřazena pro odchozí externí připojení.
> - **Obsahuje interně řešitelný reqdn nebo privátní IP adresu**, aplikační brána směruje požadavek na back-endový server pomocí jeho instance privátní IP adresy.
> - **Obsahuje externí koncový bod nebo externě řešitelný reqdn**, aplikační brána směruje požadavek na back-endový server pomocí jeho front-endu veřejné IP adresy. Překlad DNS je založen na privátní zóně DNS nebo vlastním serveru DNS, pokud je nakonfigurovaný nebo používá výchozí DNS poskytovanou Azure. Pokud neexistuje front-endová veřejná IP adresa, je přiřazena pro odchozí externí připojení.

### <a name="modifications-to-the-request"></a>Změny žádosti

Aplikační brána vloží čtyři další hlavičky pro všechny požadavky před předáním požadavků back-endu. Tyto hlavičky jsou x-forwarded-for, x-forwarded-proto, x-forwarded-port a x-original-host. Formát hlavičky x-forwarded-for je seznam IP:port oddělený čárkami.

Platné hodnoty pro x-forwarded-proto jsou HTTP nebo HTTPS. X-forwarded-port určuje port, kde požadavek dosáhl aplikační brány. Hlavička x-original-host obsahuje původní hlavičku hostitele, se kterou byl požadavek doručen. Tato hlavička je užitečná v integraci webu Azure, kde se virtuální hlavička příchozího hostitele změní před směrováním provozu do back-endu. Pokud je jako možnost povolena spřažení relace, přidá soubor cookie spřažení spravované bránou.

Aplikační brána můžete nakonfigurovat tak, aby upravovala záhlaví pomocí [přepsání hlaviček HTTP](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) nebo pomocí nastavení přepsání cesty. Pokud k tomu však není nakonfigurováno, všechny příchozí požadavky jsou zálohovány do back-endu.

## <a name="next-steps"></a>Další kroky

[Informace o součástech aplikační brány](application-gateway-components.md)
