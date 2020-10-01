---
title: Azure Security Center incidenty – inteligentní korelace výstrah
description: Toto téma vysvětluje, jak Fusion používá relaci cloudových inteligentních výstrah ke generování incidentů zabezpečení v Azure Security Center.
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
ms.openlocfilehash: 742d7122c60b383e8ab7aa73f73b11b47843a9d5
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91613814"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Korelace cloudových inteligentních výstrah v Azure Security Center (incidenty)

Azure Security Center průběžně analyzuje hybridní cloudové úlohy pomocí pokročilých analýz a analýzy hrozeb, které vás upozorňují na škodlivou aktivitu.

Šířka pokrytí hrozbami roste. Nutnost detekovat i mírné ohrožení zabezpečení je důležité a může být náročné na to, aby analytici zabezpečení mohli určit různé výstrahy a identifikovat skutečný útok. Security Center pomáhá analytikům vypořádat se s tímto neúnavou výstrahou. Pomáhá diagnostikovat útoky při jejich výskytu tím, že koreluje různé výstrahy a signály s nízkou věrností do incidentů zabezpečení.

Fusion Analytics je technologický a analytický back-end, který splňuje Security Center incidenty a umožňuje tak korelaci různých výstrah a kontextových signálů. Fúze vychází z různých signálů hlášených u předplatného napříč prostředky. Fusion vyhledá vzory, které odhalí průběh útoku nebo signály se sdílenými kontexty informací, což značí, že byste pro ně měli použít jednotný postup odezvy.

Analytics Analytics kombinuje znalostní bázi zabezpečení domény se systémem AI a analyzuje výstrahy a zjišťuje nové vzory útoků, když k nim dojde. 

Security Center využívá matrici útoků MITRE k přidružení výstrah k jejich vnímanému záměru a pomáhá znalostní bázi formalizovat zabezpečení domény. Kromě toho můžete pomocí informací shromážděných pro každý krok útoku Security Center vyfiltrovat aktivitu, která se jeví jako kroky útoku, ale ve skutečnosti ne.

Vzhledem k tomu, že k útokům často dochází v různých klientech, Security Center mohou kombinovat algoritmy AI a analyzovat tak sekvence útoků, které jsou hlášeny v každém předplatném Tato technika identifikuje posloupnosti útoků jako vzorové výstrahy, místo toho, aby se k sobě navzájemně nepřidružil.

V průběhu vyšetřování incidentu často potřebují analytikům přístup k závěru o povaze hrozby a o tom, jak ho zmírnit. Například, i když je zjištěna anomálie sítě, bez pochopení, co se děje v síti nebo s ohledem na cílový prostředek, je obtížné pochopit, jaké akce mají být další. Pro usnadnění může incident zabezpečení zahrnovat artefakty, související události a informace. Další informace, které jsou k dispozici pro incidenty zabezpečení, se liší v závislosti na typu zjištěné hrozby a konfiguraci vašeho prostředí. 

> [!TIP]
> Seznam výstrah zabezpečení, které mohou být vytvořeny analýzou Fusion, najdete v [referenční tabulce výstrah](alerts-reference.md#alerts-fusion).

:::image type="content" source="./media/security-center-alerts-cloud-smart/security-incident.png" alt-text="Snímek obrazovky s zjištěnou událostí bezpečnostního incidentu":::


Pokud chcete lépe porozumět incidentům zabezpečení, přečtěte si téma [Správa incidentů zabezpečení v Azure Security Center](security-center-incident.md).

