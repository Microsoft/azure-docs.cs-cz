---
title: Používání Azure DNS s jinými službami Azure
description: V tomto studijním programu můžete začít s používáním Azure DNS k překladu názvů pro jiné služby Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937240"
---
# <a name="how-azure-dns-works-with-other-azure-services"></a>Jak Azure DNS funguje s jinými službami Azure

Azure DNS je hostovaná služba správy DNS a překladu názvů. Můžete ji použít k vytvoření veřejných názvů DNS pro jiné aplikace a služby, které nasadíte v Azure. Vytvoření názvu služby Azure ve vaší vlastní doméně je jednoduché. Stačí přidat záznam správného typu pro vaši službu.

* U dynamicky přidělěných IP adres můžete vytvořit záznam CNAME DNS, který se mapuje na název DNS, který Azure vytvořil pro vaši službu. Standardy DNS vám brání v použití záznamu CNAME pro vrchol zóny. Místo toho můžete použít záznam aliasu. Další informace [najdete v tématu Výuka: Konfigurace záznamu aliasu, který bude odkazovat na veřejnou IP adresu Azure](tutorial-alias-pip.md).
* Pro staticky přidělené IP adresy můžete vytvořit záznam DNS A pomocí libovolného názvu, který obsahuje nahý název *domény* na vrcholu zóny.

V následující tabulce jsou uvedeny podporované typy záznamů, které můžete použít pro různé služby Azure. Jak ukazuje tabulka, Azure DNS podporuje jenom záznamy DNS pro síťové prostředky orientované na Internet. Azure DNS nelze použít pro překlad názvů interních privátních adres.

| Služba Azure | Síťové rozhraní | Popis |
| --- | --- | --- |
| Azure Application Gateway |[Veřejná IP adresa front-endu](dns-custom-domain.md#public-ip-address) |Můžete vytvořit záznam DNS A nebo CNAME. |
| Azure Load Balancer |[Veřejná IP adresa front-endu](dns-custom-domain.md#public-ip-address) |Můžete vytvořit záznam DNS A nebo CNAME. Správce zatížení může mít dynamicky přiřazenou veřejnou IP adresu IPv6. Vytvořte záznam CNAME pro adresu IPv6. |
| Azure Traffic Manager |Veřejné jméno |Můžete vytvořit záznam aliasu, který se mapuje na trafficmanager.net název přiřazený profilu traffic manageru. Další informace naleznete [v tématu Výuka: Konfigurace záznamu aliasu pro podporu názvů domén vrcholů pomocí Traffic Manageru](tutorial-alias-tm.md). |
| Azure Cloud Services |[Veřejná IP adresa](dns-custom-domain.md#public-ip-address) |Pro staticky přidělené adresy IP můžete vytvořit záznam DNS A. Pro dynamicky přidělené ADRESY IP je nutné vytvořit záznam CNAME, který se mapuje na *název cloudapp.net.*|
| Azure App Service | [Externí IP adresa](dns-custom-domain.md#app-service-web-apps) |Pro externí IP adresy můžete vytvořit záznam DNS A. V opačném případě je nutné vytvořit záznam CNAME, který se mapuje na název azurewebsites.net. Další informace najdete [v tématu Mapování názvu vlastní domény na aplikaci Azure](../app-service/app-service-web-tutorial-custom-domain.md). |
| Virtuální počítače Azure Správce prostředků |[Veřejná IP adresa](dns-custom-domain.md#public-ip-address) |Virtuální virtuální ms správce prostředků může mít veřejné IP adresy. Virtuální ho virtuálního počítačů s veřejnou IP adresou může být také za vyrovnáváním zatížení. Pro veřejnou adresu můžete vytvořit záznam DNS A, CNAME nebo alias. Tento vlastní název můžete použít k obejití virtuální ip v navyčitadlo zatížení. |
| Klasické virtuální počítače |[Veřejná IP adresa](dns-custom-domain.md#public-ip-address) |Klasické virtuální počítače vytvořené pomocí prostředí PowerShell nebo CLI lze nakonfigurovat s dynamickou nebo statickou (vyhrazenou) virtuální adresou. Můžete vytvořit záznam DNS CNAME nebo A. |
