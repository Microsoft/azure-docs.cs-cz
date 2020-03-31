---
title: Vytvoření vlastních chybových stránek Brány aplikace Azure
description: Tento článek ukazuje, jak vytvořit vlastní chybové stránky brány aplikace. U vlastní chybové stránky můžete použít vlastní značky a rozložení.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: victorh
ms.openlocfilehash: ff11f686287498fe12b31d15a630178bb12035ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74129863"
---
# <a name="create-application-gateway-custom-error-pages"></a>Vytvořit vlastní chybové stránky brány aplikace

Služba Application Gateway vám umožní vytvořit vlastní chybové stránky místo zobrazení výchozích chybových stránek. U vlastní chybové stránky můžete použít vlastní značky a rozložení.

Můžete například definovat vlastní stránku údržby, pokud vaše webová aplikace není dostupná. Nebo můžete vytvořit stránku neoprávněného přístupu, pokud je do webové aplikace odeslán škodlivý požadavek.

Vlastní chybové stránky jsou podporovány v následujících dvou scénářích:

- **Stránka údržby** – tato vlastní chybová stránka je odeslána namísto stránky chybné brány 502. Je zobrazena, když aplikační brána nemá žádný back-end pro směrování provozu. Například když je naplánovaná údržba nebo když nepředvídaný problém ovlivňuje přístup k back-endového fondu.
- **Neoprávněný přístupová stránka** – Tato vlastní chybová stránka je odeslána namísto stránky 403 neoprávněných přístupů. Zobrazuje se, když WAF aplikační brány detekuje škodlivý provoz a blokuje jej.

Pokud chyba pochází z back-endových serverů, pak je předána spolu nezměněné zpět volajícímu. Vlastní chybová stránka se nezobrazí. Aplikační brána může zobrazit vlastní chybovou stránku, když požadavek nemůže dosáhnout back-endu.

Vlastní chybové stránky lze definovat na globální úrovni a na úrovni posluchače:

- **Globální úroveň** – chybová stránka se vztahuje na provoz pro všechny webové aplikace nasazené v této aplikační bráně.
- **Úroveň naslouchací procesu** - chybová stránka je použita pro přenosy přijaté na tomto naslouchací proces.
- **Oba** - vlastní chybová stránka definovaná na úrovni posluchače přepíše jednu sadu na globální úrovni.

Chcete-li vytvořit vlastní chybovou stránku, musíte mít:

- stavový kód odpovědi HTTP.
- umístění chybové stránky. 
- veřejně přístupný objekt blob úložiště Azure pro umístění.
- typu rozšíření *.htm nebo *.html. 

Velikost chybové stránky musí být menší než 1 MB. Pokud jsou na chybové stránce propojeny obrázky, musí se jednat o veřejně přístupné absolutní adresy URL nebo vložená zakódovaná bitová kopie base64 na vlastní stránce chyby. Relativní odkazy s obrázky ve stejném umístění objektu blob nejsou aktuálně podporovány. 

Po zadání chybové stránky ji aplikační brána stáhne z umístění objektu blob úložiště a uloží ji do mezipaměti místní brány aplikace. Pak se chybová stránka zobrazí přímo z aplikační brány. Chcete-li upravit existující vlastní chybovou stránku, musíte v konfiguraci aplikační brány přejděte na jiné umístění objektů blob. Aplikační brána pravidelně nekontroluje umístění objektů blob, aby načítala nové verze.

## <a name="portal-configuration"></a>Konfigurace portálu

1. Přejděte na portálu do brány aplikace a zvolte aplikační bránu.

    ![ag-přehled](media/custom-error/ag-overview.png)
2. Klikněte na **Naslouchací procesy** a přejděte na konkrétní naslouchací proces, kde chcete zadat chybovou stránku.

    ![Naslouchací procesy aplikační brány](media/custom-error/ag-listener.png)
3. Nakonfigurujte vlastní chybovou stránku pro chybu 403 WAF nebo stránku údržby 502 na úrovni posluchače.

    > [!NOTE]
    > Vytváření vlastních chybových stránek globální úrovně z portálu Azure momentálně není podporované.

4. Zadejte veřejně přístupnou adresu URL objektu blob pro daný stavový kód chyby a klepněte na tlačítko **Uložit**. Aplikační brána je nyní nakonfigurována s vlastní chybovou stránkou.

   ![Chybové kódy brány aplikace](media/custom-error/ag-error-codes.png)

## <a name="azure-powershell-configuration"></a>Konfigurace Azure PowerShellu

Azure PowerShell můžete použít ke konfiguraci vlastní chybové stránky. Například globální vlastní chybová stránka:

`$updatedgateway = Add-AzApplicationGatewayCustomError -ApplicationGateway $appgw -StatusCode HttpStatus502 -CustomErrorPageUrl $customError502Url`

Nebo chybová stránka na úrovni posluchače:

`$updatedlistener = Add-AzApplicationGatewayHttpListenerCustomError -HttpListener $listener01 -StatusCode HttpStatus502 -CustomErrorPageUrl $customError502Url`

Další informace naleznete v [tématech Add-AzApplicationGatewayCustomError](https://docs.microsoft.com/powershell/module/az.network/add-azapplicationgatewaycustomerror?view=azps-1.2.0) a [Add-AzApplicationGatewayHttpListenerCustomError](https://docs.microsoft.com/powershell/module/az.network/add-azapplicationgatewayhttplistenercustomerror?view=azps-1.3.0).

## <a name="next-steps"></a>Další kroky

Informace o diagnostice brány aplikace naleznete v [tématu Back-end health, diagnostic kázně a metriky pro aplikační bránu](application-gateway-diagnostics.md).