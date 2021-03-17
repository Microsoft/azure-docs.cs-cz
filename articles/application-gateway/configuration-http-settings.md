---
title: Konfigurace nastavení HTTP pro Azure Application Gateway
description: Tento článek popisuje, jak nakonfigurovat nastavení protokolu HTTP v Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: c0c939a6a8323dfdfafddb46ccdb7d7ef3dd2f2c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89652813"
---
# <a name="application-gateway-http-settings-configuration"></a>Konfigurace nastavení protokolu HTTP Application Gateway

Služba Application Gateway směruje provoz na back-endové servery pomocí konfigurace, kterou tady zadáte. Po vytvoření nastavení HTTP je potřeba ho přidružit k jednomu nebo více pravidlům směrování požadavků.

## <a name="cookie-based-affinity"></a>Spřažení na základě souborů cookie.

Azure Application Gateway používá soubory cookie spravované bránou k údržbě uživatelských relací. Když uživatel odešle první požadavek na Application Gateway, nastaví soubor cookie spřažení v odpovědi s hodnotou hash, která obsahuje podrobnosti o relaci, takže následné požadavky na soubor cookie spřažení budou směrovány na stejný back-end Server pro zachování vytrvalost. 

Tato funkce je užitečná, když chcete zachovat relaci uživatele na stejném serveru a když je stav relace uložen místně na serveru pro relaci uživatele. Pokud aplikace nemůže zpracovat spřažení na základě souborů cookie, nemůžete tuto funkci použít. Pokud ho chcete použít, ujistěte se, že klienti podporují soubory cookie.

[Aktualizace v80](https://chromiumdash.appspot.com/schedule) [prohlížeče Chromu](https://www.chromium.org/Home) provedla mandát, kde se soubory cookie protokolu HTTP bez atributu [SameSite](https://tools.ietf.org/id/draft-ietf-httpbis-rfc6265bis-03.html#rfc.section.5.3.7) musí považovat za SameSite = Lax. V případě požadavků CORS (sdílení prostředků mezi zdroji) platí, že pokud se soubor cookie má odeslat v kontextu třetí strany, musí používat *SameSite = None; Zabezpečte* atributy a měly by se odesílat jenom přes HTTPS. V opačném případě se ve scénáři protokolu HTTP v prohlížeči neodesílají soubory cookie v kontextu třetí strany. Cílem této aktualizace z Chrome je zvýšit zabezpečení a vyhnout se útokům prostřednictvím CSRF (pro falšování požadavků mezi lokalitami). 

Aby byla tato změna podporovaná, počínaje února 17 2020 se Application Gateway (všechny typy SKU) vloží další soubor cookie s názvem *ApplicationGatewayAffinityCORS* společně s existujícím souborem cookie *ApplicationGatewayAffinity* . K souboru cookie *ApplicationGatewayAffinityCORS* jsou přidané dva další atributy (*"SameSite = None; Secure "*), aby se relace vždy zachovala i pro požadavky na více zdrojů.

Všimněte si, že výchozí název souboru cookie spřažení je *ApplicationGatewayAffinity* a můžete ho změnit. V případě, že používáte vlastní název souboru cookie spřažení, je jako přípona přidána další soubor cookie s CORS. Například *CustomCookieNameCORS*.

> [!NOTE]
> Pokud je nastaven atribut *SameSite = None* , je povinný, že soubor cookie obsahuje také příznak *zabezpečení* a musí být odeslán prostřednictvím protokolu HTTPS.  Pokud je v CORS vyžadováno spřažení relací, je nutné migrovat úlohy do HTTPS. Tady najdete informace o přesměrování zpracování TLS a kompletní Application Gateway protokolu TLS – [Přehled](ssl-overview.md), [Konfigurace aplikační brány s ukončením TLS pomocí Azure Portal](create-ssl-portal.md), [konfigurace kompletního TLS pomocí Application Gateway s portálem](end-to-end-ssl-portal.md).

## <a name="connection-draining"></a>Vyprázdnění připojení

Vyprazdňování připojení pomáhá řádně odebrat členy fondu back-end během plánovaných aktualizací služby. Toto nastavení můžete použít pro všechny členy fondu back-end tím, že v nastavení HTTP povolíte vyprazdňování připojení. Zajišťuje, aby všechny odregistrované instance back-end fondu nadále udržovaly stávající připojení a poskytovaly žádosti o konfigurovatelný časový limit a nedostaly žádné nové žádosti nebo připojení. Jedinou výjimkou jsou požadavky vázané na zrušení registrace instancí z důvodu spřažení relace spravované bránou a budou nadále předány do odregistrování instancí. Vyprazdňování připojení se vztahuje na instance back-endu, které jsou explicitně odebrány z fondu back-end.

## <a name="protocol"></a>Protokol

Application Gateway podporuje HTTP i HTTPS pro požadavky směrování na back-endové servery. Pokud zvolíte protokol HTTP, přenosy na back-endové servery budou nešifrované. Pokud nešifrovaná komunikace není přijatelná, vyberte HTTPS.

Toto nastavení kombinované s protokolem HTTPS v naslouchací službě podporuje [kompletní protokol TLS](ssl-overview.md). Díky tomu můžete bezpečně přenášet citlivá data zašifrovaná do back-endu. Každý back-end Server ve fondu back-end, který má zapnutý koncový protokol TLS, musí být nakonfigurovaný s certifikátem, aby umožňoval zabezpečenou komunikaci.

## <a name="port"></a>Port

Toto nastavení určuje port, ve kterém back-endové servery naslouchají provozu z aplikační brány. Můžete nakonfigurovat porty v rozsahu od 1 do 65535.

## <a name="request-timeout"></a>Časový limit žádosti

Toto nastavení je počet sekund, po které brána Application Gateway čeká, až obdrží odpověď z back-endového serveru.

## <a name="override-back-end-path"></a>Přepsat back-end cestu

Toto nastavení umožňuje nakonfigurovat volitelnou vlastní cestu přesměrování, která se má použít, když se požadavek přesměruje do back-endu. Všechny části příchozí cesty, které se shodují s vlastní cestou v poli **přepište cestu k back-endu** , se zkopírují do přesměrované cesty. Následující tabulka ukazuje, jak tato funkce funguje:

- Když je nastavení HTTP připojené k základnímu pravidlu pro směrování požadavků:

  | Původní požadavek  | Přepsat back-end cestu | Požadavek předaný do back-endu |
  | ----------------- | --------------------- | ---------------------------- |
  | domovské            | /override musí obsahovat            | /override/home/              |
  | /home/secondhome/ | /override musí obsahovat            | /override/home/secondhome/   |

- Pokud je nastavení HTTP připojeno k pravidlu směrování požadavků založené na cestách:

  | Původní požadavek           | Pravidlo cesty       | Přepsat back-end cestu | Požadavek předaný do back-endu |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | pathrule      | /override musí obsahovat            | /override/home/              |
  | /pathrule/home/secondhome/ | pathrule      | /override musí obsahovat            | /override/home/secondhome/   |
  | domovské                     | pathrule      | /override musí obsahovat            | /override/home/              |
  | /home/secondhome/          | pathrule      | /override musí obsahovat            | /override/home/secondhome/   |
  | /pathrule/home/            | /pathrule/home* | /override musí obsahovat            | /override musí obsahovat                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | /override musí obsahovat            | /override/secondhome/        |
  | pathrule                 | pathrule      | /override musí obsahovat            | /override musí obsahovat                   |

## <a name="use-for-app-service"></a>Použít pro App Service

Jedná se o zástupce pouze uživatelského rozhraní, který vybere dvě požadovaná nastavení pro back-end Azure App Service. Umožňuje **Vybrat název hostitele z back-endové adresy**a vytvoří nový vlastní test, pokud ho ještě nemáte. (Další informace naleznete v části [výběr názvu hostitele z nastavení back-endové adresy](#pick-host-name-from-back-end-address)v tomto článku.) Vytvoří se nový test paměti a hlavička testu se vybere z adresy člena, který je back-end.

## <a name="use-custom-probe"></a>Použít vlastní test paměti

Toto nastavení přidruží [vlastní test](application-gateway-probe-overview.md#custom-health-probe) s nastavením http. K nastavení HTTP můžete přidružit jenom jeden vlastní test paměti. Pokud nechcete explicitně přidružit vlastní test, použije se k monitorování stavu back-endu [výchozí test](application-gateway-probe-overview.md#default-health-probe-settings) . Doporučujeme vytvořit vlastní test pro lepší kontrolu nad monitorováním stavu back-endu.

> [!NOTE]
> Vlastní test nemonitoruje stav fondu back-end, pokud není odpovídající nastavení HTTP explicitně přidruženo k naslouchacímu procesu.

## <a name="pick-host-name-from-back-end-address"></a>Vybrat název hostitele z back-endové adresy

Tato funkce dynamicky nastaví hlavičku *hostitele* v požadavku na název hostitele back-end fondu. Používá IP adresu nebo plně kvalifikovaný název domény.

Tato funkce pomáhá v případě, že se název domény back-endu liší od názvu DNS aplikační brány a back-end spoléhá na konkrétní hlavičku hostitele, aby se přeložila na správný koncový bod.

Příkladem případu jsou služby pro více tenantů jako back-end. App Service je víceklientské služby, které používají sdílený prostor s jednou IP adresou. Služba App Service je proto dostupná jenom prostřednictvím názvů hostitelů, které jsou nakonfigurované v nastavení vlastní domény.

Ve výchozím nastavení je název vlastní domény *example.azurewebsites.NET*. Pokud chcete ke službě App Service přistupovat pomocí služby Application Gateway prostřednictvím názvu hostitele, který není explicitně zaregistrovaný ve službě App Service nebo prostřednictvím plně kvalifikovaného názvu domény služby Application Gateway, přepište název hostitele v původní žádosti na název hostitele App Service. Provedete to tak, že povolíte nastavení **Vybrat název hostitele z back-endu adresy** .

U vlastní domény, jejíž existující vlastní název DNS je namapovaný na službu App Service, nemusíte toto nastavení Povolit.

> [!NOTE]
> Toto nastavení není vyžadováno pro App Service Environment, což je vyhrazené nasazení.

## <a name="host-name-override"></a>Přepsání názvu hostitele

Tato funkce nahrazuje hlavičku *hostitele* v příchozím požadavku na aplikační bráně zadaným názvem hostitele.

Pokud je například v nastavení **název hostitele** zadán parametr *www.contoso.com* , původní požadavek * `https://appgw.eastus.cloudapp.azure.com/path1` se změní na * `https://www.contoso.com/path1` při přeposílání požadavku na back-end Server.

## <a name="next-steps"></a>Další kroky

- [Seznamte se s back-end fondem.](configuration-overview.md#back-end-pool)