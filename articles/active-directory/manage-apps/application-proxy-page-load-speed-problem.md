---
title: Načtení aplikace Proxy aplikace trvá příliš dlouho | Dokumenty společnosti Microsoft
description: Poradce při potížích s výkonem načítání stránky pomocí proxy aplikace Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ac1182d719d7c90129115e1fadf94f4f86a28e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "65782653"
---
# <a name="an-application-proxy-application-takes-too-long-to-load"></a>Načtení aplikace Proxy aplikace trvá příliš dlouho.

Tento článek vám pomůže pochopit, proč aplikace proxy aplikace Azure AD může trvat dlouhou dobu načíst. Vysvětluje také, co můžete udělat pro vyřešení tohoto problému.

## <a name="overview"></a>Přehled
I když vaše aplikace pracují, mohou zaznamenat dlouhou latenci. Mohou existovat vylepšení topologie sítě, které můžete provést pro zlepšení rychlosti. Hodnocení různých topologií naleznete v [dokumentu o aspektech sítě](application-proxy-network-topology.md).

Kromě topologie sítě v současné době neexistují žádná další doporučení pro ladění výkonu. Jak se služba Proxy aplikace rozšiřuje, může se dostat do datového centra, které je fyzicky blíže. Bližší blízkost může pomoci s latencí. Seznam datových center Azure najdete na [stránce testu latence](http://www.azurespeed.com/Azure/Latency). 

Datová centra se službou Proxy aplikace lze nalézt pomocí [nástroje Test Connector Ports](https://aadap-portcheck.connectorporttest.msappproxy.net/). 

## <a name="feedback-on-application-proxy-data-center-locations"></a>Zpětná vazba k umístění mno žití o aplikačním proxy centru 
Mohou existovat datová centra Azure, která ještě neobsahují proxy aplikace, ale vedla by k velkému zlepšení latence. Odešlete umístění aadapfeedback@microsoft.comdatového centra do aplikace . Společnost Microsoft používá vaši zpětnou vazbu pro plány rozšíření.

Společnost Microsoft pracuje na dalších funkcích ke zlepšení latence. Jakmile budou tato vylepšení k dispozici, bude dokumentace aktualizována.

## <a name="next-steps"></a>Další kroky
[Práce s existujícími místními proxy servery](application-proxy-configure-connectors-with-proxy-servers.md)
