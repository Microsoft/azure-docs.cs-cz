---
title: 'Řešení potíží: Chybějící data v protokolech aktivity služby Azure Active Directory | Dokumentace Microsoftu'
description: Obsahuje seznam různých dostupných sestav pro Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 7cbe4337-bb77-4ee0-b254-3e368be06db7
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 01/15/2018
ms.author: rolyon
ms.reviewer: dhanyahk
ms.openlocfilehash: 1568a7c508dbbb42143d8badf39833af90fcc376
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34589538"
---
# <a name="i-cant-find-some-actions-that-i-performed-in-the-azure-active-directory-activity-log"></a>Nemůžu v protokolu aktivity Azure Active Directory najít některé provedené akce


## <a name="symptoms"></a>Příznaky

Provedl jsem nějaké akce na webu Azure Portal a očekával jsem pro tyto akce zobrazení protokolu auditu v okně `Activity logs > Audit Logs`, ale nemůžu je najít.

 ![Vytváření sestav](./media/active-directory-reporting-troubleshoot-missing-audit-data/01.png)
 

## <a name="cause"></a>Příčina

Akce se v protokolu auditu aktivity nezobrazí okamžitě. Zobrazení operací v protokolech auditu na portálu může od jejich provedení trvat 15 minut až jednu hodinu.

## <a name="resolution"></a>Řešení

Počkejte 15 minut až hodinu a pak se podívejte, jestli se akce v protokolu zobrazily. Pokud je stále nevidíte, vytvořte prosím lístek podpory a my se na to podíváme.


## <a name="next-steps"></a>Další kroky
Přečtěte si [nejčastější dotazy ke generování sestav v Azure Active Directory](active-directory-reporting-faq.md).

