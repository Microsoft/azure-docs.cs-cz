---
title: Vytvořit vlastní chybové stránky Azure Application Gateway
description: V tomto článku se dozvíte, jak vytvořit Application Gateway vlastní chybové stránky.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 2/14/2019
ms.author: victorh
ms.openlocfilehash: abfe33ff679bef125d9bf5b78e1790a1a4c64863
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56301600"
---
# <a name="create-application-gateway-custom-error-pages"></a>Vytvořit Application Gateway vlastní chybové stránky

Služba Application Gateway vám umožní vytvořit vlastní chybové stránky místo zobrazení výchozích chybových stránek. U vlastní chybové stránky můžete použít vlastní značky a rozložení.

Například můžete definovat vlastní stránce Údržba, jestli vaše webová aplikace není dostupný. Nebo můžete vytvořit stránku před neoprávněným přístupem, pokud škodlivý požadavek je odeslán do webové aplikace.

Pro následující dva scénáře jsou podporovány vlastní chybové stránky:

- **Stránce údržba** – je tato vlastní chybová stránka odešle se místo stránky 502 – Chybná brána. Zobrazí se při Application Gateway nemá žádné back-end směrovat provoz. Například, když má naplánované údržby nebo když nepředpokládaného problému efekty přístup k back-endového fondu.
- **Stránka před neoprávněným přístupem** – je tato vlastní chybová stránka odešle se místo stránky 403 – neoprávněný přístup. Zobrazí se při waf služby Application Gateway zjišťuje škodlivý provoz a blokuje se.

Pokud chyba pochází z back-end serverů, pak je předána spolu bez úprav back volající. Vlastní chybová stránka se nezobrazí. Služba Application gateway můžou zobrazovat vlastní chybovou stránku, když požadavek nemá přístup na back-endu.

Vlastní chybové stránky dají definovat na globální úrovni a úrovni naslouchací proces:

- **Globální úrovni** – chybová stránka vztahuje na provoz pro všechny webové aplikace nasazené v této službě application gateway.
- **Naslouchací proces úroveň** – chybové stránky se použijí na provoz přijatá v naslouchacím procesem.
- **Obě** – vlastní chybovou stránku definované na úrovni naslouchací proces přepíše nastavené na globální úrovni.

Chcete-li vytvořit vlastní chybovou stránku, musíte mít:

- Stavový kód odpovědi HTTP.
- odpovídající umístění pro chybovou stránku. 
- veřejně přístupné služby Azure storage blob pro umístění.
- typ rozšíření *.htm nebo *.html. 

Velikost chybové stránky musí být menší než 1 MB. Pokud jsou bitové kopie v chybovou stránku, musí být veřejně přístupná absolutní adresy URL nebo image vložené kódovanou jako base64 ve vlastní chybovou stránku. Relativní odkazy s obrázky ve stejném umístění objektu blob se momentálně nepodporují. 

Po zadání chybovou stránku application gateway stáhne z úložiště objektů blob a uloží jej do mezipaměti brány místní aplikace. Potom chybovou stránku obsluhuje přímo z aplikační brány. Pokud chcete upravit existující vlastní chybovou stránku, musí odkazovat na umístění různých objektů blob v konfiguraci application gateway. Application gateway nemá pravidelně ji kontrolujte, umístění objektu blob k načtení nové verze.

## <a name="portal-configuration"></a>Konfigurace portálu

1. Přejděte k Application Gateway na portálu a zvolte službu application gateway.

    ![ag-overview](media/custom-error/ag-overview.png)
2. Klikněte na tlačítko **naslouchacích procesů** a přejděte do příslušného naslouchacího procesu, ve které chcete zadat chybovou stránku.

    ![Naslouchací procesy Application Gateway](media/custom-error/ag-listener.png)
3. Konfigurace vlastní chybové stránky pro chybu 403 WAF nebo stránku 502 údržby na úrovni naslouchacího procesu.

    > [!NOTE]
    > Vytvoření globální úrovni vlastní chybové stránky na webu Azure Portal se aktuálně nepodporuje.

4. Zadejte adresu URL veřejně přístupná objektů blob pro dané chybě. stavový kód a klikněte na tlačítko **Uložit**. Application Gateway je nyní nakonfigurován s vlastní chybovou stránku.

   ![Kódy chyb aplikace brány](media/custom-error/ag-error-codes.png)

## <a name="azure-powershell-configuration"></a>Konfigurace Azure PowerShellu

Prostředí Azure PowerShell můžete použít ke konfiguraci vlastní chybové stránky. Například globální vlastní chybové stránky:

`$updatedgateway = Add-AzApplicationGatewayCustomError -ApplicationGateway $appgw -StatusCode HttpStatus502 -CustomErrorPageUrl $customError502Url`

Nebo na úrovni chybovou stránku naslouchací proces:

`$updatedlistener = Add-AzApplicationGatewayHttpListenerCustomError -HttpListener $listener01 -StatusCode HttpStatus502 -CustomErrorPageUrl $customError502Url`

Další informace najdete v tématu [přidat AzApplicationGatewayCustomError](https://docs.microsoft.com/powershell/module/az.network/add-azapplicationgatewaycustomerror?view=azps-1.2.0) a [přidat AzApplicationGatewayHttpListenerCustomError](https://docs.microsoft.com/powershell/module/az.network/add-azapplicationgatewayhttplistenercustomerror?view=azps-1.3.0).

## <a name="next-steps"></a>Další postup

Informace o diagnostice služby Application Gateway najdete v tématu [stav Back endu, diagnostické protokoly a metriky pro službu Application Gateway](application-gateway-diagnostics.md).