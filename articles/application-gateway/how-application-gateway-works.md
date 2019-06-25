---
title: Fungování služby application gateway
description: Tento článek obsahuje informace o fungování služby application gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: a16421182f533f5aa2ad4bcc2e58e910cc7e8ca6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64702410"
---
# <a name="how-an-application-gateway-works"></a>Fungování služby application gateway

Tento článek vysvětluje, jak službu application gateway přijímá příchozí požadavky a směruje je do back-endu.

![Jak služby application gateway přijímá žádosti o](./media/how-application-gateway-works/how-application-gateway-works.png)

## <a name="how-an-application-gateway-accepts-a-request"></a>Jak služby application gateway přijímá žádosti o

1. Předtím, než klient odešle požadavek do služby application gateway, překládá název domény služby application gateway s využitím serveru systému DNS (Domain Name). Azure určuje položku DNS, vzhledem k tomu, že jsou všechny brány application Gateway v doméně azure.com.

2. Azure DNS vrátí IP adresu klienta, což je IP adresa front-endu služby application gateway.

3. Application gateway přijímá příchozí provoz na jeden nebo více naslouchacích procesů. Naslouchací proces je logická entita, která kontroluje pro žádosti o připojení. Má nakonfigurovanou IP adresu front-endu, protokol a číslo portu pro připojení klientů ke službě application gateway.

4. Pokud firewall webových aplikací (WAF) se používá, application gateway kontroluje hlavičky požadavku a text, pokud jsou k dispozici pro pravidla firewallu webových aplikací. Tuto akci Určuje, zda je požadavek platným požadavkem nebo ohrožení zabezpečení. Pokud je požadavek platný, přesměruje ho na back-endu. Pokud daný požadavek není platný, je blokovaná jako bezpečnostní hrozbu.

Azure Application Gateway je možné jako nástroj pro vyrovnávání zatížení interní aplikace nebo jako Vyrovnávání zatížení aplikace přístupem k Internetu. Službu application gateway přístupem k Internetu používá veřejné IP adresy. Název DNS služby application gateway přístupem k Internetu je veřejně přeložitelného jeho veřejné IP adresy. Internetové služby application Gateway v důsledku toho může směrovat požadavky klienta na Internetu.

Interní aplikační brány použít pouze privátní IP adresy. Název DNS pro interní aplikační brány je veřejně přeložitelného na jeho privátní IP adresu. Interní Vyrovnávání zatížení proto pouze směrovat požadavky od klientů s přístupem k virtuální síti pro službu application gateway.

Internetové i interní aplikační brány směrovat požadavky do back-end serverů pomocí privátních IP adres. Back-end serverech není nutné veřejné IP adresy pro příjem požadavků z interní nebo služby application gateway přístupem k Internetu.

## <a name="how-an-application-gateway-routes-a-request"></a>Jak směruje žádost o služby application gateway

Pokud je požadavek platný, nebo brány WAF se používá, se vyhodnotí jako pravidlo směrování požadavku, který je spojen s naslouchací proces application gateway. Tuto akci Určuje, které back-endovém fondu směrovat žádosti.

Pravidla se zpracovávají v pořadí, ve kterém jsou uvedeny na portálu. Pravidlo směrování žádostí podle, application gateway Určuje, zda směrování všech žádostí na straně posluchače na konkrétní back-endový fond, směrování požadavků na různé back-endových fondů na základě cest URL nebo přesměrovat požadavky do jiný port nebo externí web.

Když službu application gateway vybere back-endový fond, odešle požadavek na jednu v dobrém stavu back-end serverů ve fondu (y.y.y.y). Stav serveru je určen podle sondu stavu. Pokud back-endový fond obsahuje několik serverů, application gateway používá algoritmus kruhové dotazování směrovat požadavky mezi servery v pořádku. Rozloží požadavky na serverech.

Po application gateway určuje back-end serveru, otevře se nová relace TCP s back-end serveru, na základě nastavení HTTP. Nastavení HTTP určují protokol, port a další nastavení související s směrování, které jsou nutné k vytvoření nové relace s back-end serveru.

Port a protokol použitý v nastavení HTTP určení, zda přenos dat mezi servery brány a back-endu aplikace zašifrují (tedy provádění-kompletního protokolu SSL), nebo nešifrovaná.

Když službu application gateway odešle původní požadavek na back-end serveru, respektuje všechny vlastní konfigurace z nastavení HTTP související s přepsání název hostitele, cestu a protokolu. Tato akce udržuje spřažení relace na základě souborů cookie, připojení vyprázdnění, název hostitele výběr z back-end a tak dále.

Interní aplikační brány se používá pouze privátní IP adresy. Název DNS pro interní aplikační brány je možné přeložit na jeho privátní IP adresu. Interní Vyrovnávání zatížení v důsledku toho může směrovat pouze požadavky od klientů s přístupem k virtuální síti pro službu application gateway.

 >[!NOTE]
 >Obě brány přístupem k Internetu a interní aplikace směrovat požadavky do back-end serverů pomocí privátních IP adres. Tato akce se stane, když váš prostředek back-endový fond obsahuje privátní IP adresy, konfigurace síťové karty virtuálního počítače nebo interně přeložitelnou adresu. Pokud back-endového fondu:
> - **Je veřejný koncový bod**, application gateway používá veřejnou IP adresu jeho front-endu k dosažení serveru. Pokud není k dispozici veřejnou IP adresu front-endu, jeden je přiřazen pro odchozí připojení k externí síti.
> - **Obsahuje interně přeložitelný plně kvalifikovaný název domény nebo privátní IP adresu**, aplikační brány s použitím jeho privátní IP adresy instance přesměruje požadavek na back-end serveru.
> - **Obsahuje externí koncový bod nebo externě přeložitelný plně kvalifikovaný název domény**, aplikační brány s použitím jeho veřejné IP adresy front-endu přesměruje požadavek na back-end serveru. Překlad názvů DNS je založen na privátní zóny DNS nebo vlastního serveru DNS, pokud nakonfigurované nebo použije výchozí DNS poskytnutých platformou Azure. Pokud není k dispozici veřejnou IP adresu front-endu, jeden je přiřazen pro odchozí připojení k externí síti.

### <a name="modifications-to-the-request"></a>Úpravy na požadavek

Službu application gateway vloží čtyři dodatečné hlavičky pro všechny požadavky, předtím, než ji předá požadavek back-endu. Tyto hlavičky jsou x předané pro, x-forwarded-proto, x předané portu a x původního hostitele. Formát pro x předané – pro hlavičky je čárkou oddělený seznam IP: port.

Platné hodnoty pro x-forwarded-proto jsou HTTP nebo HTTPS. X předané portu Určuje port, který žádost-li dosáhnout application gateway. Hlavička X-původního hostitele obsahuje hlavičku původního hostitele, pomocí kterého Přišla žádost. Této hlavičky je užitečné v integrace webu Azure, ve kterém se upraví hlavičku hostitele příchozího před provoz směruje do back-endu. Pokud spřažení relace je povolená možnost, pak přidá do souboru cookie spravovaných bránou spřažení.

Můžete nakonfigurovat aplikační brány pro úpravy hlaviček pomocí [hlavičky protokolu HTTP přepsat](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) nebo pro úpravu cesty identifikátoru URI pomocí nastavení specifikátor override cesty ke. Pokud je tak nakonfigurovaný, všechny příchozí požadavky ale směrovány přes proxy server back-endu.

## <a name="next-steps"></a>Další postup

[Další informace o součástech aplikace brány](application-gateway-components.md)
