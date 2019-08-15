---
title: Při Chybějící data ve stažených protokolech aktivit Azure Active Directory | Microsoft Docs
description: Nabízí řešení pro chybějící data ve stažených protokolech aktivity služby Azure Active Directory.
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: f120c1b86efe94f4ff6316e6116b9049582b07e9
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68987987"
---
# <a name="i-cant-find-all-the-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>Nemohu najít všechna data ve stažených protokolech aktivit Azure Active Directory

## <a name="symptoms"></a>Příznaky

Ve stažených protokolech aktivity (auditu nebo přihlášení) se nezobrazují žádné záznamy pro zvolený čas. Proč? 

 ![Vytváření sestav](./media/troubleshoot-missing-data-download/01.png)
 
## <a name="cause"></a>Příčina

Když si stáhnete protokoly aktivit v Azure Portal, omezíme měřítko na 250 000 záznamů seřazených podle nejnovějšího prvního. 

## <a name="resolution"></a>Řešení

Můžete využít [rozhraní API pro vytváření sestav Azure AD](concept-reporting-api.md), abyste načetli až milion záznamů v libovolném časovém okamžiku.

## <a name="next-steps"></a>Další postup

* [Nejčastější dotazy k Azure Active Directorym sestavám](reports-faq.md)

