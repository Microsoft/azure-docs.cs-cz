---
title: Použití Azure DNS s dalšími službami Azure
description: V této cestě výukového kurzu se naučíte, jak používat Azure DNS k překladu názvů jiných služeb Azure.
services: dns
documentationcenter: na
author: rohinkoul
manager: kumudD
tags: azure dns
ms.assetid: e9b5eb94-7984-4640-9930-564bb9e82b78
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 09/21/2016
ms.author: rohink
ms.openlocfilehash: fa2c1ced6405c967ca33562d6215b304b8507e5a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "76937240"
---
# <a name="how-azure-dns-works-with-other-azure-services"></a>Jak Azure DNS funguje s ostatními službami Azure

Azure DNS je hostovaná služba DNS pro správu a překlad názvů. Můžete ho použít k vytvoření veřejných názvů DNS pro jiné aplikace a služby, které nasadíte v Azure. Vytvoření názvu pro službu Azure ve vlastní doméně je jednoduché. Stačí přidat záznam správného typu pro vaši službu.

* Pro dynamicky přidělené IP adresy můžete vytvořit záznam DNS CNAME, který se mapuje na název DNS, který vytvořila služba Azure pro vaši službu. Standardy DNS zabraňují použití záznamu CNAME pro vrchol zóny. Místo toho můžete použít záznam aliasu. Další informace najdete v tématu [kurz: Konfigurace záznamu aliasu pro odkaz na veřejnou IP adresu Azure](tutorial-alias-pip.md).
* Pro staticky přidělené IP adresy můžete vytvořit záznam DNS A pomocí libovolného názvu, který zahrnuje název *domény* ve vrcholu zóny.

Následující tabulka popisuje podporované typy záznamů, které můžete použít pro různé služby Azure. Jak ukazuje tabulka, Azure DNS podporuje pouze záznamy DNS pro síťové prostředky internetové sítě. Azure DNS nelze použít k překladu názvů interních privátních adres.

| Služba Azure | Síťové rozhraní | Popis |
| --- | --- | --- |
| Azure Application Gateway |[Veřejná IP adresa front-endu](dns-custom-domain.md#public-ip-address) |Můžete vytvořit záznam DNS A nebo CNAME. |
| Azure Load Balancer |[Veřejná IP adresa front-endu](dns-custom-domain.md#public-ip-address) |Můžete vytvořit záznam DNS A nebo CNAME. Load Balancer může mít dynamicky přiřazenou veřejnou IP adresu IPv6. Vytvoří záznam CNAME pro adresu IPv6. |
| Azure Traffic Manager |Veřejný název |Můžete vytvořit záznam aliasu, který se mapuje na trafficmanager.net název přiřazený k vašemu profilu Traffic Manager. Další informace najdete v tématu [kurz: Konfigurace záznamu aliasu pro podporu názvů vrcholových domén pomocí Traffic Manager](tutorial-alias-tm.md). |
| Azure Cloud Services |[Veřejná IP adresa](dns-custom-domain.md#public-ip-address) |Pro staticky přidělené IP adresy můžete vytvořit záznam DNS A. Pro dynamicky přidělené IP adresy musíte vytvořit záznam CNAME, který se mapuje na název *cloudapp.NET* .|
| Azure App Service | [Externí IP adresa](dns-custom-domain.md#app-service-web-apps) |Pro externí IP adresy můžete vytvořit záznam DNS A. V opačném případě je nutné vytvořit záznam CNAME, který se mapuje na název azurewebsites.net. Další informace najdete v tématu [Mapování vlastního názvu domény na aplikaci Azure](../app-service/app-service-web-tutorial-custom-domain.md). |
| Azure Resource Manager virtuálních počítačů |[Veřejná IP adresa](dns-custom-domain.md#public-ip-address) |Virtuální počítače s Správce prostředků můžou mít veřejné IP adresy. Virtuální počítač s veřejnou IP adresou může být taky za nástrojem pro vyrovnávání zatížení. Pro veřejnou adresu můžete vytvořit záznam DNS A, CNAME nebo alias. Tento vlastní název můžete použít k obejít virtuální IP adresy v nástroji pro vyrovnávání zatížení. |
| Klasické virtuální počítače |[Veřejná IP adresa](dns-custom-domain.md#public-ip-address) |U klasických virtuálních počítačů vytvořených pomocí PowerShellu nebo rozhraní příkazového řádku se dá nakonfigurovat dynamická nebo statická (rezervovaná) virtuální adresa. Můžete vytvořit záznam DNS CNAME nebo záznam v uvedeném pořadí. |
