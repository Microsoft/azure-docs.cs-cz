---
title: 'Řešení potíží: Chybějící data ve stažených protokolech aktivity služby Azure Active Directory | Dokumentace Microsoftu'
description: Nabízí řešení pro chybějící data ve stažených protokolech aktivity služby Azure Active Directory.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: ea7ef4eb75cc7ffdf951e8d8799e898bfc31948b
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54818196"
---
# <a name="i-cant-find-all-the-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>Nemůžu najít všechna data v protokolech aktivit Azure Active Directory, které jsem si stáhli

## <a name="symptoms"></a>Příznaky

Ve stažených protokolech aktivity (auditu nebo přihlášení) se nezobrazují žádné záznamy pro zvolený čas. Proč? 

 ![Vytváření sestav](./media/troubleshoot-missing-data-download/01.png)
 
## <a name="cause"></a>Příčina

Když si stáhnete protokoly aktivity na webu Azure Portal, Omezujeme rozsah na 5000 záznamů řazených od nejnovější je první. 

## <a name="resolution"></a>Řešení

Můžete využít [rozhraní API pro vytváření sestav Azure AD](concept-reporting-api.md), abyste načetli až milion záznamů v libovolném časovém okamžiku. Náš doporučený postup je [spuštění skriptu na základě plánu](tutorial-signin-logs-download-script.md) , která volá rozhraní API pro generování sestav k načtení záznamy přírůstkově v časovém intervalu času (například denně nebo týdně). 

## <a name="next-steps"></a>Další postup

* [Nejčastější dotazy týkající se sestav Azure Active Directory](reports-faq.md)

