---
title: Příchozí/odchozí IP adresy – Azure App Service | Dokumentace Microsoftu
description: Popisuje, jakým způsobem příchozí a odchozí IP adresy se používají ve službě App Service a tom, jak najít informace o je pro vaši aplikaci.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: e5315efbfb94bec5ef810078788623dd105dcf34
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53731008"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Příchozí a odchozí IP adresy ve službě Azure App Service

[Azure App Service](overview.md) je víceklientská služba, s výjimkou [App Service Environment](environment/intro.md). Aplikace, které nejsou ve službě App Service environment (není v [izolované úroveň](https://azure.microsoft.com/pricing/details/app-service/)) sdílené složky síťovou infrastrukturu s dalšími aplikacemi. Příchozí a odchozí IP adresy aplikace v důsledku toho se může lišit a můžete změnit i v některých situacích. 

[App Service Environment](environment/intro.md) používat vyhrazené síťové infrastruktury, takže aplikace spuštěné ve službě App Service environment statické, vyhrazené IP adresy pro příchozí a odchozí připojení.

## <a name="when-inbound-ip-changes"></a>Když se změní příchozí IP

Bez ohledu na počet škálovaných instancí přičemž každá aplikace má jednu příchozí IP adresu. Příchozí IP adresa se může změnit při provádění jednoho z následujících akcí:

- Odstraňte aplikaci a znovu ho vytvořte v jiné skupině prostředků.
- Odstranit poslední aplikace ve skupině prostředků _a_ kombinaci oblasti a znovu ho vytvořte.
- Odstranit existující vazby SSL, například během obnovení certifikátu (najdete v článku [prodloužit platnost certifikátů](app-service-web-tutorial-custom-ssl.md#renew-certificates)).

## <a name="get-static-inbound-ip"></a>Získat statickou IP adresu pro příchozí

V některých případech může být vhodné vyhrazené statickou IP adresu pro vaši aplikaci. Chcete-li získat příchozí statickou IP adresu, musíte nakonfigurovat [vazby SSL založené na protokolu IP](app-service-web-tutorial-custom-ssl.md#bind-your-ssl-certificate). Pokud doopravdy nepotřebujete funkce SSL k zabezpečení aplikace, můžete dokonce odeslat certifikát podepsaný svým držitelem pro tuto vazbu. V vazby SSL založené na protokolu IP, certifikát vázán na IP adresu, takže služby App Service ustanovení statických IP adres pro to uděláme. 

## <a name="when-outbound-ips-change"></a>Když se změní odchozí IP adresy

Bez ohledu na počet škálovaných instancí přičemž každá aplikace má stanovený počet odchozích IP adres v daném okamžiku. Všechny odchozí připojení z aplikace služby App Service, třeba k back-end databáze, používá jednu z odchozí IP adresy jako IP adresa původu. Nemůže vědět předem která IP adresa instance dané aplikace bude používat pro odchozí připojení, tak back-end služby musíte otevřít jeho bránu firewall, aby všechny odchozí IP adresy vaší aplikace.

Sadu odchozích IP adres pro změny aplikace při škálování aplikace mezi vrstvami nižší (**základní**, **standardní**, a **Premium**) a  **Premium V2** vrstvy.

Můžete najít sadu všechny možné odchozí IP adresy vaší aplikace můžete použít, bez ohledu na cenové úrovně, tím, že hledají `possibleOutboundIPAddresses` vlastnost. Zobrazit [najít odchozí IP adresy](#find-outbound-ips).

## <a name="find-outbound-ips"></a>Najít odchozí IP adresy

Chcete-li zjistit odchozí IP adresy, které aktuálně používají ve vaší aplikaci na webu Azure Portal, klikněte na tlačítko **vlastnosti** v levém navigačním panelu vaší aplikace. 

Tyto informace můžete najít spuštěním následujícího příkazu v [Cloud Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

K vyhledání všech možných odchozí IP adresy pro vaši aplikaci, bez ohledu na cenové úrovně, spusťte následující příkaz [Cloud Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

## <a name="next-steps"></a>Další postup

Zjistěte, jak omezit příchozí provoz tak, že zdrojové IP adresy.

> [!div class="nextstepaction"]
> [Omezení statických IP adres](app-service-ip-restrictions.md)
