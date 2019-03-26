---
title: 'Řešení potíží: Chybějící data ve stažených protokolech aktivity služby Azure Active Directory | Dokumentace Microsoftu'
description: Nabízí řešení pro chybějící data ve stažených protokolech aktivity služby Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
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
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2200a9c75b371ed72ffefe6900367e698101e0fe
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2019
ms.locfileid: "58434836"
---
# <a name="i-cant-find-all-the-data-in-the-azure-active-directory-activity-logs-i-downloaded"></a>Nemůžu najít všechna data v protokolech aktivit Azure Active Directory, které jsem si stáhli

## <a name="symptoms"></a>Příznaky

Ve stažených protokolech aktivity (auditu nebo přihlášení) se nezobrazují žádné záznamy pro zvolený čas. Proč? 

 ![Vytváření sestav](./media/troubleshoot-missing-data-download/01.png)
 
## <a name="cause"></a>Příčina

Když si stáhnete protokoly aktivity na webu Azure Portal, Omezujeme rozsah na 250 000 záznamů řazených od nejnovější je první. 

## <a name="resolution"></a>Řešení

Můžete využít [rozhraní API pro vytváření sestav Azure AD](concept-reporting-api.md), abyste načetli až milion záznamů v libovolném časovém okamžiku.

## <a name="next-steps"></a>Další postup

* [Nejčastější dotazy týkající se sestav Azure Active Directory](reports-faq.md)

