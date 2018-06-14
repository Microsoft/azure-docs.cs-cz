---
title: Pomocí Azure DNS s jinými službami Azure | Microsoft Docs
description: Pochopení jak používat Azure DNS pro překlad názvu pro jinými službami Azure
services: dns
documentationcenter: na
author: KumudD
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
ms.author: kumud
ms.openlocfilehash: 6d052bc82c35aa3f2fdf5b5820e3901bd5c4080d
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2017
ms.locfileid: "25989839"
---
# <a name="how-azure-dns-works-with-other-azure-services"></a>Jak funguje Azure DNS s jinými službami Azure

Azure DNS je hostovanou službu DNS správu a název řešení. To umožňuje vytvoření veřejné názvy DNS pro ostatní aplikace a služby, které jste nasadili v Azure. Vytváření název služby Azure v vaše vlastní doména je jednoduché, přidávání záznamu správného typu služby.

* Pro dynamicky přidělené IP adresy musíte vytvořit záznam DNS CNAME, který mapuje název DNS, který Azure vytvořili pro vaši službu. Standardech DNS zabránit vám v použití záznam CNAME pro vrcholu zóny.
* Pro staticky přidělené IP adresy, můžete vytvořit záznam DNS A používat libovolný název, včetně *holé domény* název ve vrcholu zóny.

Následující tabulka popisuje typy podporované záznamů, které lze použít k různým službám Azure. Jak vidíte z této tabulky, podporuje Azure DNS pouze záznamy DNS pro internetový síťové prostředky. Azure DNS nelze použít pro překlad názvu interní, privátní adresy.

| Služba Azure | Síťové rozhraní | Popis |
| --- | --- | --- |
| Application Gateway |[Veřejná IP adresa front-endu](dns-custom-domain.md#public-ip-address) |Můžete vytvořit záznam CNAME nebo DNS A. |
| Load Balancer |[Veřejná IP adresa front-endu](dns-custom-domain.md#public-ip-address)  |Můžete vytvořit záznam CNAME nebo DNS A. Nástroj pro vyrovnávání zatížení může mít veřejnou IP adresu IPv6 adresu, která se dynamicky přiřadit. Proto musíte vytvořit záznam CNAME pro adresu IPv6. |
| Traffic Manager |Veřejný název |Můžete vytvořit pouze záznam CNAME, který mapuje název trafficmanager.net přiřazená k profilu Traffic Manageru. Další informace najdete v tématu [jak Traffic Manager funguje](../traffic-manager/traffic-manager-overview.md#traffic-manager-example). |
| Cloudové služby |[Veřejná IP adresa](dns-custom-domain.md#public-ip-address) |Pro staticky přidělené IP adresy můžete vytvořit záznam DNS A. Pro dynamicky přidělené IP adresy, musíte vytvořit záznam CNAME, který se mapuje *cloudapp.net* název.|
| App Service | [Externí IP](dns-custom-domain.md#app-service-web-apps) |Pro externí IP adresy můžete vytvořit záznam DNS A. V ostatních případech musíte vytvořit záznam CNAME, který mapuje název azurewebsites.net. Další informace najdete v tématu [mapování vlastního názvu domény do aplikace Azure](../app-service/app-service-web-tutorial-custom-domain.md) |
| Virtuální počítače správce prostředků |[Veřejná IP adresa](dns-custom-domain.md#public-ip-address) |Virtuální počítače správce prostředků může mít veřejné IP adresy. Virtuální počítač s veřejnou IP adresu. může být také za službou Vyrovnávání zatížení. Můžete vytvořit záznam CNAME nebo DNS A pro veřejné adresy. Tento název vlastního lze vynechat VIP adresa zákazníka ve vyrovnávání zátěže. |
| Klasické virtuální počítače |[Veřejná IP adresa](dns-custom-domain.md#public-ip-address) |Klasické virtuální počítače vytvořené pomocí prostředí PowerShell nebo rozhraní příkazového řádku se dá nakonfigurovat s dynamická nebo statická (vyhrazená) virtuální adresu. Vytvořením DNS CNAME nebo záznam, v uvedeném pořadí. |
