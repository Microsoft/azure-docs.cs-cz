---
title: 'Řešení potíží: Chybějící data ve stažených protokolech aktivity služby Azure Active Directory | Dokumentace Microsoftu'
description: Nabízí řešení pro chybějící data ve stažených protokolech aktivity služby Azure Active Directory.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 01/15/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 9138da42eeb87e45b86be10aff67792ee6de09b4
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2018
ms.locfileid: "42061294"
---
# <a name="i-cant-find-any-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>Nemůžu najít žádná data v protokolech aktivit Azure Active Directory, které jsem si stáhli


## <a name="symptoms"></a>Příznaky

Ve stažených protokolech aktivity (auditu nebo přihlášení) se nezobrazují žádné záznamy pro zvolený čas. Proč? 

 ![Vytváření sestav](./media/troubleshoot-missing-data-download/01.png)
 

## <a name="cause"></a>Příčina

Když si stáhnete protokoly aktivity na webu Azure Portal, Omezujeme rozsah na 5000 záznamů řazených od nejnovější je první. 

## <a name="resolution"></a>Řešení

Můžete využít [rozhraní API pro vytváření sestav Azure AD](concept-reporting-api.md), abyste načetli až milion záznamů v libovolném časovém okamžiku. Náš doporučený postup je pro spuštění skriptu na základě plánu, který volá rozhraní API pro generování sestav k načtení záznamy přírůstkově v časovém intervalu času (například denně nebo týdně).

## <a name="next-steps"></a>Další postup
Přečtěte si [nejčastější dotazy ke generování sestav v Azure Active Directory](reports-faq.md).

