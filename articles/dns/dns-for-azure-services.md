---
title: Používání Azure DNS s ostatními službami Azure | Dokumentace Microsoftu
description: Pochopení způsobu, jak používat Azure DNS přeložit název pro ostatní služby Azure
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
editor: ''
tags: azure dns
ms.assetid: e9b5eb94-7984-4640-9930-564bb9e82b78
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 09/21/2016
ms.author: victorh
ms.openlocfilehash: 5d9886e16aa1921963f3d91d0fbd4da9287f7e54
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989137"
---
# <a name="how-azure-dns-works-with-other-azure-services"></a>Jak funguje Azure DNS s ostatními službami Azure

Azure DNS je hostovaná služba DNS management a název řešení. To umožňuje vytvoření veřejné názvy DNS pro ostatní aplikace a služby, které jste nasadili v Azure. Vytvoření názvu pro služby Azure ve vaší vlastní doméně je stejně jednoduché jako přidání záznamu nesprávného typu pro vaši službu.

* Pro dynamicky přidělené IP adresy můžete vytvořit záznam DNS CNAME, který se mapuje na název DNS, které Azure vytvořilo pro vaši službu. Standardy DNS by vám bránily v pomocí záznamu CNAME pro vrcholu zóny, ale místo toho použít záznamu o aliasu. Další informace najdete v tématu [kurz: záznamu aliasu pro odkazování na Azure veřejnou IP adresu nakonfigurujte](tutorial-alias-pip.md).
* Pro staticky přidělenou IP adresy, můžete vytvořit záznam DNS A používat libovolný název, včetně *základní doména* název ve vrcholu zóny.

Následující tabulka uvádí typy podporovaných záznamů, které lze použít pro různé služby Azure. Jak je vidět z této tabulky, podporuje Azure DNS pouze záznamy DNS pro síťové prostředky přístupem k Internetu. Azure DNS nelze použít pro překlad adres interní, privátní.

| Služba Azure | Síťové rozhraní | Popis |
| --- | --- | --- |
| Application Gateway |[Veřejná IP adresa front-endu](dns-custom-domain.md#public-ip-address) |Můžete vytvořit záznam DNS A nebo CNAME. |
| Load Balancer |[Veřejná IP adresa front-endu](dns-custom-domain.md#public-ip-address)  |Můžete vytvořit záznam DNS A nebo CNAME. Nástroj pro vyrovnávání zatížení může mít veřejnou IP adresu protokolu IPv6 adresu, která se dynamicky přiřadí. Proto musíte vytvořit záznam CNAME pro adresu IPv6. |
| Traffic Manager |Veřejný název |Můžete vytvořit záznam aliasu, který se mapuje na název trafficmanager.net přiřazené k vašemu profilu Traffic Manageru. Další informace najdete v tématu [kurz: Konfigurace záznamu o aliasu pro podporu vrcholu názvy domén s Traffic Managerem](tutorial-alias-tm.md). |
| Cloudová služba |[Veřejná IP adresa](dns-custom-domain.md#public-ip-address) |Pro staticky přidělenou IP adresy můžete vytvořit záznam DNS A. Pro dynamicky přidělené IP adresy, je nutné vytvořit záznam CNAME, který se mapuje *cloudapp.net* název.|
| App Service | [Externí IP adresa](dns-custom-domain.md#app-service-web-apps) |Pro externí IP adresy můžete vytvořit záznam DNS A. V opačném případě musíte vytvořit záznam CNAME, který se mapuje na název azurewebsites.net. Další informace najdete v tématu [mapování vlastního názvu domény na aplikaci Azure](../app-service/app-service-web-tutorial-custom-domain.md) |
| Virtuální počítače Resource Manageru |[Veřejná IP adresa](dns-custom-domain.md#public-ip-address) |Virtuální počítače Resource Manageru můžete mít veřejné IP adresy. Virtuální počítač s použitím veřejné IP adresy mohou být také za nástrojem pro vyrovnávání zatížení. Můžete vytvořit DNS A, CNAME nebo záznam aliasu pro veřejnou adresu. Tento vlastní název je možné obejít virtuální IP adresy na nástroj pro vyrovnávání zatížení. |
| Klasické virtuální počítače |[Veřejná IP adresa](dns-custom-domain.md#public-ip-address) |Klasické virtuální počítače vytvořené pomocí Powershellu nebo rozhraní příkazového řádku se dá nakonfigurovat s dynamickou nebo statickou (vyhrazené) virtuální adresu. Vytvořením DNS CNAME nebo záznam, v uvedeném pořadí. |
