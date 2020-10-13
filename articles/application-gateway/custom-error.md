---
title: Vytvořit vlastní chybové stránky pro Azure Application Gateway
description: V tomto článku se dozvíte, jak vytvořit Application Gateway vlastní chybové stránky. U vlastní chybové stránky můžete použít vlastní branding a rozložení.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/16/2019
ms.author: victorh
ms.openlocfilehash: ff3e9db4dcfe0bedc348323dbbddd1e66124fc5d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91360153"
---
# <a name="create-application-gateway-custom-error-pages"></a>Vytvořit Application Gateway vlastní chybové stránky

Služba Application Gateway vám umožní vytvořit vlastní chybové stránky místo zobrazení výchozích chybových stránek. U vlastní chybové stránky můžete použít vlastní branding a rozložení.

Můžete například definovat vlastní stránku údržby, pokud vaše webová aplikace není dostupná. Nebo můžete vytvořit neoprávněnou stránku přístupu, pokud se do webové aplikace pošle škodlivá žádost.

Vlastní chybové stránky jsou podporovány v následujících dvou scénářích:

- **Stránka údržby** – Tato vlastní chybová stránka se odesílá místo stránky 502 špatné brány. Zobrazuje se, když Application Gateway nemá žádný back-end ke směrování provozu do. Například při plánované údržbě nebo v případě nepředvídatelného problému, který má vliv na přístup k back-endu fondu.
- **Stránka neautorizovaný přístup** – Tato vlastní chybová stránka se odesílá místo neautorizovaného přístupového stránky 403. Zobrazuje se, když Application Gateway WAF detekuje škodlivý provoz a zablokuje ho.

Pokud dojde k chybě ze serverů back-end, pak je předána beze změny zpět volajícímu. Nezobrazuje se vlastní chybová stránka. Application Gateway může zobrazit vlastní chybovou stránku, pokud se žádost nemůže připojit k back-endu.

Vlastní chybové stránky lze definovat na globální úrovni a na úrovni naslouchacího procesu:

- **Globální úroveň** – chybová stránka se vztahuje na provoz všech webových aplikací nasazených v této aplikační bráně.
- **Úroveň naslouchacího procesu** – chybová stránka se aplikuje na provoz přijatý v tomto naslouchacího procesu.
- **Obojí** – vlastní chybová stránka definovaná na úrovni naslouchacího procesu přepíše jednu sadu na globální úrovni.

Pokud chcete vytvořit vlastní chybovou stránku, musíte mít:

- Stavový kód odpovědi HTTP.
- příslušné umístění chybové stránky 
- veřejně přístupný objekt BLOB služby Azure Storage pro dané umístění.
- typ rozšíření *. htm nebo *. html. 

Velikost chybové stránky musí být menší než 1 MB. Pokud jsou na chybové stránce připojené obrázky, musí být na vlastní chybové stránce buď veřejně přístupné absolutní adresy URL nebo obrázky kódované v kódování Base64. Relativní odkazy s obrázky ve stejném umístění objektu BLOB se v tuto chvíli nepodporují. 

Po zadání chybové stránky ji služba Application Gateway stáhne z umístění objektu BLOB úložiště a uloží ji do mezipaměti místní služby Application Gateway. Chybová stránka se pak obsluhuje přímo z aplikační brány. Pokud chcete upravit existující vlastní chybovou stránku, musíte v konfiguraci služby Application Gateway nasměrovat na jiné umístění objektu BLOB. Aplikační brána pravidelně nekontroluje umístění objektu blob, aby načetla nové verze.

## <a name="portal-configuration"></a>Konfigurace portálu

1. Na portálu přejděte na Application Gateway a vyberte Application Gateway.

    ![Snímek obrazovky se zobrazí stránka s přehledem pro aplikační bránu.](media/custom-error/ag-overview.png)
2. Klikněte na **naslouchací procesy** a přejděte na konkrétní naslouchací proces, kde chcete zadat chybovou stránku.

    ![Naslouchací procesy Application Gateway](media/custom-error/ag-listener.png)
3. Nakonfigurujte vlastní chybovou stránku pro chybu 403 WAF nebo stránku údržby 502 na úrovni naslouchacího procesu.

    > [!NOTE]
    > Vytváření vlastních chybových stránek na globální úrovni z Azure Portal aktuálně není podporováno.

4. Zadejte veřejně dostupnou adresu URL objektu BLOB pro daný stavový kód chyby a klikněte na **Uložit**. Application Gateway je teď nakonfigurovaný s vlastní chybovou stránkou.

   ![Kódy chyb Application Gateway](media/custom-error/ag-error-codes.png)

## <a name="azure-powershell-configuration"></a>Konfigurace Azure PowerShellu

Pomocí Azure PowerShell můžete nakonfigurovat vlastní chybovou stránku. Například globální vlastní chybová stránka:

```powershell
$appgw   = Get-AzApplicationGateway -Name <app-gateway-name> -ResourceGroupName <resource-group-name>

$updatedgateway = Add-AzApplicationGatewayCustomError -ApplicationGateway $appgw -StatusCode HttpStatus502 -CustomErrorPageUrl "http://<website-url>"
```

Nebo na stránce s chybou úrovně naslouchacího procesu:

```powershell
$appgw   = Get-AzApplicationGateway -Name <app-gateway-name> -ResourceGroupName <resource-group-name>

$listener01 = Get-AzApplicationGatewayHttpListener -Name <listener-name> -ApplicationGateway $appgw

$updatedlistener = Add-AzApplicationGatewayHttpListenerCustomError -HttpListener $listener01 -StatusCode HttpStatus502 -CustomErrorPageUrl "http://<website-url>"
```

Další informace najdete v tématech [Add-AzApplicationGatewayCustomError](https://docs.microsoft.com/powershell/module/az.network/add-azapplicationgatewaycustomerror?view=azps-1.2.0) a [Add-AzApplicationGatewayHttpListenerCustomError](https://docs.microsoft.com/powershell/module/az.network/add-azapplicationgatewayhttplistenercustomerror?view=azps-1.3.0).

## <a name="next-steps"></a>Další kroky

Informace o diagnostice Application Gateway najdete v tématu [stav back-endu, diagnostické protokoly a metriky pro Application Gateway](application-gateway-diagnostics.md).
