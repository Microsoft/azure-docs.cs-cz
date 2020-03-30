---
title: Incidenty Azure Security Center – inteligentní korelace výstrah
description: Toto téma vysvětluje, jak fusion používá cloud inteligentní výstrahy korelace ke generování incidentů zabezpečení v Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e9d5a771-bfbe-458c-9a9b-a10ece895ec1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/02/2019
ms.author: memildin
ms.openlocfilehash: b26f0bab073ce248ca23bb8a815fa3e293ddba51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73686490"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Korelace inteligentních výstrah cloudu v Azure Security Center (incidenty)

Azure Security Center průběžně analyzuje hybridní cloudové úlohy pomocí pokročilých analýz a analýzy hrozeb, které vás upozorní na škodlivou aktivitu.

Šíře pokrytí hrozbami roste. Potřeba odhalit i sebemenší kompromis je důležitá a pro bezpečnostní analytiky může být náročné, aby tísni různé výstrahy a identifikovali skutečný útok. Security Center pomáhá analytikům vyrovnat se s touto únavou výstrahy. Pomáhá diagnostikovat útoky, jakmile k nim dojde, tím, že koreluje různé výstrahy a signály s nízkou věrností do bezpečnostních incidentů.

Fusion analytics je technologie a analytické back-end, který pohání incidenty Security Center, což umožňuje korelovat různé výstrahy a kontextové signály dohromady. Fusion se dívá na různé signály hlášené na předplatné napříč prostředky. Fusion vyhledá vzory, které odhalují průběh útoku nebo signály se sdílenými kontextovými informacemi, což znamená, že byste pro ně měli použít jednotný postup odezvy.

Fusion analytics kombinuje znalosti domény zabezpečení s umělou ai pro analýzu výstrah a objevování nových vzorů útoků, jakmile k nim dojde. 

Security Center využívá MITRE Attack Matrix k přidružení výstrah k jejich vnímaným záměrům a pomáhá formalizovat znalosti domény zabezpečení. Kromě toho pomocí informací shromážděných pro každý krok útoku může Centrum zabezpečení vyloučit aktivitu, která se jeví jako kroky útoku, ale ve skutečnosti není.

Vzhledem k tomu, že útoky často dochází mezi různými tenanty, Security Center můžete kombinovat algoritmy AI analyzovat útoky sekvence, které jsou hlášeny na každé předplatné. Tato technika identifikuje sekvence útoku jako převládající vzory výstrah, místo toho, aby byly jen vedlejší.

Během vyšetřování incidentu analytici často potřebují další kontext, aby dospěli k verdiktu o povaze hrozby a o tom, jak ji zmírnit. Například i když je zjištěna anomálie sítě, aniž byste pochopili, co dalšího se děje v síti nebo s ohledem na cílový prostředek, je obtížné pochopit, jaké akce je třeba provést dále. Chcete-li pomoci, incident zabezpečení může zahrnovat artefakty, související události a informace. Další informace, které jsou k dispozici pro bezpečnostní incidenty, se liší v závislosti na typu zjištěné hrozby a konfiguraci vašeho prostředí. 

![Snímek obrazovky se zprávou o zjištěném incidentu zabezpečení](./media/security-center-alerts-cloud-smart/security-incident.png)

Pokud chcete lépe porozumět incidentům zabezpečení, přečtěte si informace [o tom, jak zpracovat incidenty zabezpečení v Centru zabezpečení Azure](https://docs.microsoft.com/azure/security-center/security-center-incident).

