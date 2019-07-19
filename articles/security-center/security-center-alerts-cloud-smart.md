---
title: Korelace cloudových inteligentních výstrah v Azure Security Center (incidenty) | Microsoft Docs
description: Toto téma vysvětluje, jak Fusion používá relaci cloudových inteligentních výstrah ke generování incidentů zabezpečení v Azure Security Center.
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
ms.author: v-mohabe
ms.openlocfilehash: 7ba2cf14d9fac100f44a1ef23997b27ba062bee0
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295863"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Korelace cloudových inteligentních výstrah v Azure Security Center (incidenty)

Security Center průběžně analyzuje úlohy hybridního cloudu pomocí pokročilých analýz a analýzy hrozeb, které vás upozorňují na škodlivou aktivitu.

Vzhledem k tomu, že se rozsah hrozeb zvyšuje a nutnost detekovat ještě mírné označení napadení, může být pro analytiky zabezpečení obtížné určit různé výstrahy a identifikovat skutečný útok. Security Center pomáhá analytikům vypořádat se únavou a diagnostikou útoků při jejich výskytu tím, že koreluje různé výstrahy a signály s nízkou přesností na incidenty zabezpečení.

Fúze je technologický a analytický back-end, který splňuje Security Center incidenty, a umožňuje tak korelaci různých výstrah a kontextových signálů společně. Fúze funguje tak, že se podíváme na různé signály nahlášené na základě předplatného napříč prostředky a vyhledáme základní vzory, které zobrazují průběh útoku nebo signály se sdílenými kontexty informací, které určují jednotnou odpověď. vybere se pro ně.

Analytics Analytics kombinuje znalostní bázi domény zabezpečení se systémem AI a analyzuje výstrahy a zjišťuje nové vzory útoků, když k nim dojde. 

Security Center využívá matrici útoků MITRE k přidružení výstrah k jejich vnímanému záměru a pomáhá znalostní bázi formalizovat zabezpečení domény. Kromě toho můžete pomocí informací shromážděných pro každý krok útoku Security Center vyfiltrovat aktivitu, která se jeví jako kroky útoku, ale ne.  

Vzhledem k tomu, že k útokům často dochází v různých klientech, Security Center můžou kombinovat algoritmy AI a analyzovat tak sekvence útoků, které jsou nahlášeny v každém předplatném, aby je identifikovaly jako rozšířené vzory výstrah, a ne jenom incidenty přidružené jiná.

V průběhu vyšetřování incidentu často potřebují analytikům přístup k závěru o povaze hrozby a o tom, jak ho zmírnit. Například, i když je zjištěna anomálie sítě, bez pochopení, co se děje v síti nebo s ohledem na cílový prostředek, je obtížné pochopit, jaké akce mají být další. Pro usnadnění může incident zabezpečení zahrnovat artefakty, související události a informace. Další informace, které jsou k dispozici pro incidenty zabezpečení, se liší v závislosti na typu zjištěné hrozby a konfiguraci vašeho prostředí. 

![Podrobnosti o incidentu zabezpečení](./media/security-center-alerts-cloud-smart/security-incident.png)

Chcete-li lépe porozumět incidentům zabezpečení, přečtěte si téma [jak řešit incidenty zabezpečení v Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-incident).

