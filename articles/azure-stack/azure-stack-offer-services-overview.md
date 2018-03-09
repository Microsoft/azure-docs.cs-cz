---
title: "Nabídka služeb v zásobníku Azure | Microsoft Docs"
description: "Jako operátor cloudu můžete nabízet služby pro vaše uživatele."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 5b117a9b174f5d2419ff596cc90436e4b9d21645
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="overview-of-offering-services-in-azure-stack"></a>Přehled nabídky služeb v Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

[Microsoft Azure zásobníku](azure-stack-poc.md) je hybridní Cloudová platforma, která umožňuje poskytovat služby z vašeho datového centra. Jako poskytovatel služby můžete nabízet služby vašim klientům. V rámci obchodní nebo státní úřad můžete nabídnout místních služeb zaměstnancům. Služby, které může poskytnout zahrnovat, ale nejsou omezeny na:

- Platforma jako služba (PaaS) služby, například aplikační služby, aplikace API, funkce rozhraní API, SQL, databáze MySQL.

Dokonce můžete kombinovat služeb a integrovat sestavení komplexní řešení pro různé uživatele.

Chcete-li tyto služby poskytovat uživatelům, musíte vytvořit [plánům, nabídky a kvóty](azure-stack-plan-offer-quota-overview.md). Uživatelé mohou potom přihlásit k vaší nabídky k použití služby.

## <a name="plan-your-service-offers"></a>Plánování vaší nabídky služeb

Pokud už plánujete vaší nabídky, uvědomte si následující body:

**Nabídkách zkušebních verzí**: můžete nabídkách zkušebních verzí přilákat noví uživatelé, kteří můžete poté upgradovat na další služby. Chcete-li vytvořit zkušební nabídku, vytvořit malá [základního plánu](azure-stack-plan-offer-quota-overview.md#base-plan) s plán volitelné rozšíření větší.

**Plánování kapacity**: je možné, že se zajímá uživatele, kteří získat velké množství prostředků a zdokonaleným systému pro všechny uživatele. Chcete-li vám pomůže zvýšit výkon, můžete [nakonfigurovat kvóty plánu](azure-stack-plan-offer-quota-overview.md#plans) využití zakončení.

**Delegovaná zprostředkovatelé**: ostatním uživatelům můžete udělit možnost vytvoření nabídky ve vašem prostředí. Například pokud jste poskytovatel služeb, můžete [delegovat](azure-stack-delegated-provider.md) tato schopnost váš prodejce. Nebo, pokud jste v organizaci, můžete delegovat na ostatní rozdělení pobočky.

## <a name="next-steps"></a>Další postup
[Vytvořit nabídku v Azure zásobníku](azure-stack-create-offer.md)

