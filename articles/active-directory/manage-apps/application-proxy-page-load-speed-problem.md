---
title: Načtení aplikace proxy aplikací trvá příliš dlouho a nemůže se načíst | Microsoft Docs
description: Řešení potíží s výkonem stránky při načítání Proxy aplikací služby AD Azure
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: bff9873742d5d660f563eebcb58ff9bcca940d24
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/14/2020
ms.locfileid: "84760167"
---
# <a name="an-application-proxy-application-takes-too-long-to-load"></a>Načtení aplikace proxy aplikací trvá příliš dlouho.

Tento článek vám pomůže pochopit, proč se zatížení aplikace Proxy aplikací služby AD Azure může trvat dlouhou dobu. Vysvětluje také, co můžete udělat k vyřešení tohoto problému.

## <a name="overview"></a>Přehled
I když vaše aplikace fungují, můžou vyskytnout dlouhou latenci. Může se jednat o vylepšení síťové topologie, která můžete využít ke zlepšení rychlosti. Hodnocení různých topologií najdete v [dokumentu o požadavcích na síť](application-proxy-network-topology.md).

Kromě síťové topologie neexistují v současnosti žádná další doporučení k vyladění výkonu. Vzhledem k tomu, že se služba proxy aplikací rozšíří, může přijít do datového centra, které je fyzicky přiblížné. Užší blízkost může pomáhat s latencí. Seznam datových center Azure najdete na [stránce testování latence](http://www.azurespeed.com/Azure/Latency). 

Datová centra s aplikační proxy službou můžete najít pomocí [Nástroje pro testování portů konektoru](https://aadap-portcheck.connectorporttest.msappproxy.net/). 

## <a name="feedback-on-application-proxy-data-center-locations"></a>Zpětná vazba na umístění datového centra proxy aplikací 
Možná existují datová centra Azure, která ještě neobsahují proxy aplikací, ale mají za následek lepší vylepšení latence. Odešle umístění datového centra do aadapfeedback@microsoft.com . Microsoft používá vaši zpětnou vazbu k plánům rozšíření.

Microsoft pracuje na dalších možnostech zlepšování latence. Jakmile budou tato vylepšení k dispozici, bude dokumentace aktualizována.

## <a name="next-steps"></a>Další kroky
[Práce se stávajícími místními proxy servery](application-proxy-configure-connectors-with-proxy-servers.md)
