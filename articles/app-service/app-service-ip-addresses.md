---
title: IP adresy v Azure App Service | Microsoft Docs
description: Popisuje, jak příchozí a odchozí IP adresy se používají v App Service a jak se mají najít informace o je pro vaši aplikaci.
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
ms.openlocfilehash: 906a5d511615c57b6ff807ac240a838c63917e66
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
ms.locfileid: "31789942"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Příchozí a odchozí IP adres ve službě Azure App Service

[Aplikační služba Azure](app-service-web-overview.md) je víceklientské služby, s výjimkou [prostředí App Service](environment/intro.md). Aplikace, které nejsou ve službě App Service environment (není v [izolované vrstvy](https://azure.microsoft.com/pricing/details/app-service/)) sdílené složky síťovou infrastrukturu s jinými aplikacemi. Příchozí a odchozí IP adresy aplikace v důsledku toho se může lišit a můžete změnit i v některých situacích. 

[Prostředí App Service](environment/intro.md) použít vyhrazenou síť infrastruktury, proto aplikace běžící v App Service environment statické, vyhrazených IP adres pro příchozí a odchozí připojení.

## <a name="when-inbound-ip-changes"></a>Když se změní příchozí IP

Bez ohledu na počet instancí upraveným každá aplikace má příchozí jednu IP adresu. Příchozí IP adresa může změnit, když provedete jednu z následujících akcí:

- Odstraňte aplikaci a znovu ji vytvořte v jiné skupině prostředků.
- Odstranit poslední aplikace ve skupině prostředků _a_ kombinace oblasti a vytvořte je znovu.
- Odstranit stávající vazbu SSL, například při obnovení certifikátu (najdete v části [obnovení certifikátů](app-service-web-tutorial-custom-ssl.md#renew-certificates)).

## <a name="get-static-inbound-ip"></a>Získat statickou IP adresu příchozí

Někdy potřebovat vyhrazený statickou IP adresu pro vaši aplikaci. Chcete-li získat příchozí statickou IP adresu, musíte nakonfigurovat [vazba založená na IP](app-service-web-tutorial-custom-ssl.md#bind-your-ssl-certificate). Pokud nepotřebujete ve skutečnosti funkce SSL k zabezpečení vaší aplikace, můžete dokonce nahrát certifikát podepsaný svým držitelem pro tuto vazbu. V vazbu SSL založenou na protokolu IP vázán certifikát na IP adresu, samostatně, tak služby App Service zřizuje statických IP adres Chcete-li dojít. 

## <a name="when-outbound-ips-change"></a>Když změnit odchozí IP adresy

Bez ohledu na počet instancí upraveným každá aplikace má se stanoveným počtem odchozí IP adres v daném okamžiku. Jakékoli odchozí připojení z aplikace služby App Service, například za účelem databázi back-end, používá jednu z odchozí IP adresy jako původní IP adresu. Nelze vědět předem IP adresu, která instance dané aplikace používat k provádění odchozí připojení, takže služby back-end musíte otevřít jeho bránu firewall, aby všechny odchozí IP adresy vaší aplikace.

Sada odchozí IP adresy pro vaše změny aplikace při škálování aplikace mezi nižší úrovně (**základní**, **standardní**, a **Premium**) a  **Premium V2** vrstvy.

Sada všech možných odchozí IP adres vaše aplikace používá, bez ohledu na to, cenové úrovně, tak, že vyhledá můžete najít `possibleOutboundIPAddresses` vlastnost. V tématu [najít odchozí IP adresy](#find-outbound-ips).

## <a name="find-outbound-ips"></a>Najít odchozí IP adresy

Chcete-li najít odchozí IP adresy, které jsou aktuálně používané vaší aplikace na portálu Azure, klikněte na tlačítko **vlastnosti** v levém navigačním panelu vaší aplikace. 

Stejné informace můžete najít spuštěním následujícího příkazu [cloudové prostředí](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

Najít všechny možné odchozí IP adresy pro vaši aplikaci, bez ohledu na to, cenové úrovně, spusťte následující příkaz [cloudové prostředí](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

## <a name="next-steps"></a>Další postup

Zjistěte, jak omezit příchozí přenosy tak, že zdrojové IP adresy.

> [!div class="nextstepaction"]
> [Omezení statických IP adres](app-service-ip-addresses.md)
