---
title: Aplikace Proxy aplikací trvá příliš dlouho načíst | Microsoft Docs
description: Řešení potíží s výkonem načítání stránky s Azure AD Application Proxy
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 517f78f811d57e06b4c6e6f0fad24d3b0f96b6e3
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="an-application-proxy-application-takes-too-long-to-load"></a>Aplikace Proxy aplikací trvá příliš dlouho zatížení

Tento článek pomáhá pochopit, proč Azure AD Application Proxy aplikace může trvat dlouhou dobu spouštění. Také vysvětluje, jak tento problém můžete vyřešit.

## <a name="overview"></a>Přehled
I když vaše aplikace pracují, budou mít dlouhé latence. Může být vylepšení topologie sítě, které můžete použít ke zlepšení rychlost. Vyhodnocení topologiemi, najdete v článku [sítě aspekty dokumentu](application-proxy-network-topology-considerations.md).

Kromě topologie sítě aktuálně nejsou žádné další doporučení pro optimalizaci výkonu. Jako Proxy aplikace, které rozšíří služby může pocházet do datového centra, který je fyzicky blíž. Těsné blízkosti mohou pomoci s latencí. Seznam datových center Azure najdete v tématu [latence zkušební stránku](http://www.azurespeed.com/Azure/Latency). 

Datových centrech s Proxy aplikace služby lze najít pomocí [nástroj pro testování porty konektor](https://aadap-portcheck.connectorporttest.msappproxy.net/). 

## <a name="feedback-on-application-proxy-data-center-locations"></a>Zpětná vazba týkající se umístění Proxy aplikací datových center 
Může být Azure datových center, které není ještě zahrnovat Proxy aplikace, ale vedlo ke zlepšování skvělé latence pro vás. Odeslání dat center umístění pro aadapfeedback@microsoft.com. Společnost Microsoft používá vaše zpětná vazba pro rozšíření plány.

Společnost Microsoft pracuje na další možnosti pro zlepšení latence. Jakmile tato vylepšení jsou k dispozici, budou aktualizovány v dokumentaci.

## <a name="next-steps"></a>Další postup
[Práce s existující místní proxy servery](application-proxy-working-with-proxy-servers.md)
