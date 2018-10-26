---
title: Použití Azure DNS s ostatními službami Azure | Dokumentace Microsoftu
description: Naučte se používat Azure DNS k překladu názvů u ostatních služeb Azure
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
ms.openlocfilehash: dcf209d2036d2686bea0b51380db3cd2473d04a6
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50094439"
---
# <a name="how-azure-dns-works-with-other-azure-services"></a>Jak funguje Azure DNS s ostatními službami Azure

Azure DNS je hostovaná služba DNS management a název řešení. Slouží k vytvoření veřejné názvy DNS pro ostatní aplikace a služby, které můžete nasadit v Azure. Vytvoření názvu pro služby Azure ve vaší vlastní doméně je jednoduché. Stačí přidat záznam nesprávného typu pro vaši službu.

* Pro dynamicky přidělené IP adresy můžete vytvořit záznam DNS CNAME, který se mapuje na název DNS, které Azure vytvořilo pro vaši službu. Standardy DNS by vám bránily v pomocí záznamu CNAME pro vrcholu zóny. Místo toho můžete záznamu o aliasu. Další informace najdete v tématu [kurz: záznamu aliasu pro odkazování na Azure veřejnou IP adresu nakonfigurujte](tutorial-alias-pip.md).
* Pro staticky přidělenou IP adresy, můžete vytvořit záznam DNS A pomocí libovolný název, který obsahuje *základní doména* název ve vrcholu zóny.

Následující tabulka uvádí typy podporovaných záznamů, které můžete použít pro různé služby Azure. Jak v tabulce jsou uvedeny, Azure DNS podporuje pouze záznamy DNS pro síťové prostředky přístupem k Internetu. Azure DNS nelze použít pro překlad adres interní, privátní.

| Služba Azure | Síťové rozhraní | Popis |
| --- | --- | --- |
| Azure Application Gateway |[Veřejná IP adresa front-endu](dns-custom-domain.md#public-ip-address) |Můžete vytvořit záznam DNS A nebo CNAME. |
| Nástroj pro vyrovnávání zatížení Azure |[Veřejná IP adresa front-endu](dns-custom-domain.md#public-ip-address) |Můžete vytvořit záznam DNS A nebo CNAME. Nástroj pro vyrovnávání zatížení může mít veřejnou IP adresu IPv6, která se dynamicky přiřadí. Vytvořte záznam CNAME pro adresu IPv6. |
| Azure Traffic Manager |Veřejný název |Můžete vytvořit záznam aliasu, který se mapuje na název trafficmanager.net přiřazené k vašemu profilu Traffic Manageru. Další informace najdete v tématu [kurz: Konfigurace záznamu o aliasu pro podporu vrcholu názvy domén s Traffic Managerem](tutorial-alias-tm.md). |
| Azure Cloud Services |[Veřejná IP adresa](dns-custom-domain.md#public-ip-address) |Pro staticky přidělenou IP adresy můžete vytvořit záznam DNS A. Pro dynamicky přidělené IP adresy, je nutné vytvořit záznam CNAME, který se mapuje *cloudapp.net* název.|
| Azure App Service | [Externí IP adresa](dns-custom-domain.md#app-service-web-apps) |Pro externí IP adresy můžete vytvořit záznam DNS A. V opačném případě musíte vytvořit záznam CNAME, který se mapuje na název azurewebsites.net. Další informace najdete v tématu [mapování vlastního názvu domény na aplikaci Azure](../app-service/app-service-web-tutorial-custom-domain.md). |
| Virtuální počítače Azure Resource Manageru |[Veřejná IP adresa](dns-custom-domain.md#public-ip-address) |Virtuální počítače Resource Manageru můžete mají veřejné IP adresy. Virtuální počítač s veřejnou IP adresu také může být za nástrojem pro vyrovnávání zatížení. Můžete vytvořit DNS A, CNAME nebo záznam aliasu pro veřejnou adresu. Obejít virtuální IP adresy na nástroj pro vyrovnávání zatížení můžete použít tento vlastní název. |
| Klasické virtuální počítače |[Veřejná IP adresa](dns-custom-domain.md#public-ip-address) |Klasické virtuální počítače vytvořené pomocí Powershellu nebo rozhraní příkazového řádku můžete mít nakonfigurovanou dynamická nebo statická (vyhrazená) virtuální adresu. Vytvořením DNS CNAME nebo záznam A, v uvedeném pořadí. |
