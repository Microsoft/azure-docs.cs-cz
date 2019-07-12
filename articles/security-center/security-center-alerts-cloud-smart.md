---
title: Inteligentní korelací výstrah ve službě Azure Security Center (incidenty) v cloudu | Dokumentace Microsoftu
description: Toto téma vysvětluje, jak jsou fusion používá cloud inteligentní korelací výstrah pro generování incidentů zabezpečení ve službě Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: e9d5a771-bfbe-458c-9a9b-a10ece895ec1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: 2ab4dab8cb7729b0c2ca023f22066f7b5d166a02
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571774"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Inteligentní korelací výstrah ve službě Azure Security Center (incidenty) v cloudu

Security Center průběžně analyzuje hybridními cloudovými úlohami upozornit vás na škodlivou aktivitu pomocí pokročilých analýz a inteligentních analýz hrozeb.

Šířka hrozeb pokrytí roste a potřebné ke zjišťování i nejslabším označení dojde k ohrožení bezpečnosti zvyšuje, může být náročné analytikům zabezpečení posoudit jiné upozornění a identifikovat skutečnému útoku. Security Center pomáhá analytici zvládnout výstrah reorganizovaly a diagnostice útoky, když k nim dojde, podle korelace různých výstrah a s nízkou spolehlivostí signály na incidenty zabezpečení.

Fusion je technologie a analytické back-end, který používá Security Center incidentů, umožňuje korelovat různé výstrahy a kontextové signály společně. Fusion funguje, zobrazením různým signálům hlášené pro předplatné napříč prostředky a vyhledávání běžně se vyskytujícím vzorů, které zobrazí průběh útoku nebo signály s sdílené kontextové informace, která určuje postup jednotné odpověď by měla být provést pro ně.

Fusion analytics zkombinovat znalosti domény zabezpečení s využitím AI k analýze upozornění, jakmile nastanou zjišťování nových vzorů útoků. 

Security Center bude využívat MITRE útoku matice pro přidružení k jejich vnímaná záměr výstrahy pomáhají formalizujte znalosti domény zabezpečení. Kromě toho pomocí informací shromážděných pro každý krok útoku, Security Center můžete vyloučit aktivitu, která se zdá být kroky útoku, ale není.  

Protože útoků se často dochází v různých tenantech, Security Center můžete kombinovat algoritmy AI k analýze sekvence útoku, které jsou hlášeny v každém předplatném identifikovat jako běžně se vyskytujícím výstrah vzory namísto pouze se mimochodem spojené s jednotlivými pro jiné účely.

Během vyšetřování incidentu analytici často potřebují další kontext k dosažení závěru ohledně povahy hrozby a jak ji zmírnit. Například i když je zjištěna síťová anomálie, bez nutnosti porozumět, co se děje v síti a s ohledem na cílový prostředek je obtížné pochopit, jaká opatření je třeba provést. Abychom pomohli, bezpečnostní incident může obsahovat artefakty, související události a informace. Další informace, které jsou k dispozici pro bezpečnostní incidenty v oblasti se liší v závislosti na typu ohrožení zjištěného a konfiguraci vašeho prostředí. 

![Podrobnosti o incidentu zabezpečení](./media/security-center-alerts-cloud-smart/security-incident.png)

Pro lepší pochopení bezpečnostních incidentů, naleznete v tématu [jak k řešení bezpečnostních incidentů v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-incident).

