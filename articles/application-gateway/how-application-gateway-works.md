---
title: Jak funguje Aplikační brána
description: Tento článek poskytuje informace o tom, jak brána Application Gateway přijímá příchozí požadavky a směruje je do back-endu.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: absha
ms.openlocfilehash: d2055bf812c3dc986a907d4358fa0e74e8af20fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104599659"
---
# <a name="how-an-application-gateway-works"></a>Jak funguje Aplikační brána

Tento článek vysvětluje, jak Aplikační brána přijímá příchozí požadavky a směruje je do back-endu.

![Jak Aplikační brána přijímá požadavek](./media/how-application-gateway-works/how-application-gateway-works.png)

## <a name="how-an-application-gateway-accepts-a-request"></a>Jak Aplikační brána přijímá požadavek

1. Předtím, než klient pošle požadavek službě Application Gateway, vyřeší název domény aplikační brány pomocí serveru DNS (Domain Name System). Azure řídí položku DNS, protože všechny aplikační brány jsou v doméně azure.com.

2. Azure DNS vrátí IP adresu klientovi, což je IP adresa front-endu služby Application Gateway.

3. Aplikační brána přijímá příchozí provoz na jednom nebo více posluchačích. Naslouchací proces je logická entita, která kontroluje požadavky na připojení. U připojení od klientů k aplikační bráně je nakonfigurována IP adresa front-end, protokol a číslo portu.

4. Pokud je brána firewall webových aplikací (WAF) používána, brána Application Gateway kontroluje hlavičky požadavků a text, pokud jsou k dispozici, proti pravidlům WAF. Tato akce určuje, jestli je požadavek platným požadavkem nebo bezpečnostní hrozbou. Pokud je požadavek platný, bude směrován do back-endu. Pokud je požadavek neplatný a WAF je v režimu prevence, je zablokovaná jako bezpečnostní hrozba. Pokud je v režimu detekce, požadavek se vyhodnocuje a protokoluje, ale pořád se přepošle na back-end Server.

Službu Azure Application Gateway lze použít jako interní nástroj pro vyrovnávání zatížení aplikace nebo jako internetový nástroj pro vyrovnávání zatížení. Internetová služba brány používá veřejné IP adresy. Název DNS internetové brány s přístupem k Internetu je veřejně přístupný k veřejné IP adrese. V důsledku toho mohou internetové brány s přístupem k Internetu směrovat požadavky klientů z Internetu.

Interní aplikační brány používají jenom soukromé IP adresy. Pokud používáte vlastní [zónu nebo privátní DNS](../dns/private-dns-overview.md), měl by být název domény interně přeložitelný na soukromou IP adresu Application Gateway. Interní nástroje pro vyrovnávání zatížení proto můžou směrovat požadavky od klientů jenom na přístup k virtuální síti pro službu Application Gateway.

## <a name="how-an-application-gateway-routes-a-request"></a>Jak Aplikační brána směruje požadavek

Pokud je požadavek platný a neblokuje ho služba WAF, služba Application Gateway vyhodnotí pravidlo směrování požadavků, které je přidružené k naslouchacímu procesu. Tato akce určuje, který back-end fond má směrovat požadavek.

Na základě pravidla směrování požadavků určí brána Application Gateway, jestli má směrovat všechny požadavky na naslouchací proces do konkrétního back-endu, směrovat požadavky na různé back-endové fondy na základě cesty URL nebo přesměrovat požadavky na jiný port nebo externí Web.
>[!NOTE]
>Pravidla se zpracovávají v pořadí, v jakém jsou uvedena na portálu pro SKU verze V1. 

Když Aplikační brána vybere back-end fond, odešle požadavek na jeden ze serverů back-end ve fondu (y. y. y. y). Stav serveru je určen sondou stavu. Pokud back-end fond obsahuje několik serverů, používá Aplikační brána k směrování požadavků mezi zdravými servery algoritmus kruhového dotazování. Toto zatížení vyrovnává požadavky na serverech.

Jakmile Aplikační brána určí back-end Server, otevře novou relaci TCP se back-end serverem na základě nastavení HTTP. Nastavení HTTP určuje protokol, port a další nastavení související s směrováním, která jsou nutná k navázání nové relace s back-end serverem.

Port a protokol použitý v nastavení HTTP určují, jestli je přenos dat mezi aplikační bránou a back-end servery šifrovaný (takže se provádí komplexní protokol TLS) nebo není šifrovaný.

Když Aplikační brána pošle původní požadavek na back-end Server, dodrží veškerou vlastní konfiguraci vytvořenou v nastavení HTTP, které souvisí s přepsáním názvu hostitele, cesty a protokolu. Tato akce udržuje spřažení relace na základě souborů cookie, vyprazdňování připojení, výběr názvů hostitelů z back-endu a tak dále.

 >[!NOTE]
>Pokud back-end fond:
> - **Je veřejný koncový bod**, používá služba Application Gateway svou veřejnou IP adresu front-end k dosažení serveru. Pokud není k dispozici veřejná IP adresa front-endu, je pro odchozí externí připojení přiřazena jedna.
> - **Obsahuje interně přeložitelný plně kvalifikovaný název domény nebo soukromou IP adresu**, služba Application Gateway směruje požadavek na back-end Server pomocí privátních IP adres instance.
> - **Obsahuje externí koncový bod nebo externě přeložitelný plně kvalifikovaný název domény**, protože služba Application Gateway směruje požadavek na back-end Server pomocí veřejné IP adresy front-endu. Překlad DNS je založený na soukromé zóně DNS nebo na vlastním serveru DNS, pokud je nakonfigurovaný, nebo používá výchozí DNS poskytovanou systémem Azure. Pokud není k dispozici veřejná IP adresa front-endu, je pro odchozí externí připojení přiřazena jedna.

### <a name="modifications-to-the-request"></a>Úpravy žádosti

Aplikační brána vloží čtyři další hlavičky do všech požadavků předtím, než přepošle požadavky do back-endu. Tato záhlaví jsou předávána x-pro, x předávaného-za, x předávaných portů a x-Original-Host. Formát pro hlavičku s přesměrováním na ose x je čárkami oddělený seznam IP adres: port.

Platné hodnoty pro předané x-a jsou proto HTTP nebo HTTPS. Port předaný přes X Určuje port, na který požadavek dostal Aplikační bránu. Hlavička X-originál-Host obsahuje původní hlavičku hostitele, se kterou byl požadavek přijat. Tato hlavička je užitečná v integraci webů Azure, kde se před směrováním provozu do back-endu změnila hlavička příchozího hostitele. Pokud je spřažení relace povolené jako možnost, přidá soubor cookie spřažení spravovaný bránou.

Aplikační bránu můžete nakonfigurovat tak, aby upravila hlavičky požadavků a odpovědí a adresu URL pomocí přepsání [hlaviček protokolu HTTP a adresy URL](rewrite-http-headers-url.md) nebo upravili cestu identifikátoru URI pomocí nastavení přepsat cestu. Pokud to ale není nakonfigurované, všechny příchozí požadavky se zaúčtují proxy serverem do back-endu.

## <a name="next-steps"></a>Další kroky

[Další informace o součástech služby Application Gateway](application-gateway-components.md)
