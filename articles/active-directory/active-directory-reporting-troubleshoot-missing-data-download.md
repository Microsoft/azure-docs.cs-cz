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
ms.component: compliance-reports
ms.date: 01/15/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: d0638404ec6f5b6d13aa207ef54913c1bd3ecc1a
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232331"
---
# <a name="i-cant-find-any-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>Nelze najít žádná data v protokolech aktivita služby Azure Active Directory, který byl stažen


## <a name="symptoms"></a>Příznaky

Ve stažených protokolech aktivity (auditu nebo přihlášení) se nezobrazují žádné záznamy pro zvolený čas. Proč? 

 ![Vytváření sestav](./media/active-directory-reporting-troubleshoot-missing-data-download/01.png)
 

## <a name="cause"></a>Příčina

Když si stáhnete protokoly aktivity na portálu Azure, je omezená měřítka 5000 záznamy, seřazené podle nejnovější první. 

## <a name="resolution"></a>Řešení

Můžete využít [rozhraní API pro vytváření sestav Azure AD](active-directory-reporting-api-getting-started.md), abyste načetli až milion záznamů v libovolném časovém okamžiku. Naše doporučený postup je spuštění skriptu na základě plánu, který volá rozhraní API pro vytváření sestav k načtení záznamů v přírůstkové způsobem za období (například denně nebo týdně).

## <a name="next-steps"></a>Další postup
Přečtěte si [nejčastější dotazy ke generování sestav v Azure Active Directory](active-directory-reporting-faq.md).

