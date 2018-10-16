---
title: Azure Stack integrované systémy připojení modely | Dokumentace Microsoftu
description: Určení při rozhodování o několika uzly Azure Stack pro plánování nasazení.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 7509d00815f56dc46bd276ffc67c4c607c54070a
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2018
ms.locfileid: "49338892"
---
# <a name="azure-stack-integrated-systems-connection-models"></a>Azure Stack integrované systémy modely připojení
Pokud vás zajímá systémech pro Azure Stack integrované, musíte pochopit [důležité informace o integraci několika datacenter](azure-stack-datacenter-integration.md) pro nasazení Azure Stack určit, jak systém, se vejde do vašeho datového centra. Kromě toho bude nutné se rozhodnout, přesně jak můžete integrovat Azure Stack v hybridním cloudovém prostředí. Tento článek obsahuje přehled Tato hlavní rozhodnutí, včetně připojení k Azure, úložiště identit a fakturační model rozhodnutí.

Pokud se rozhodnete pro nákup integrovaného systému, pomůže dodavatele hardwaru, výrobce OEM (OEM), vás provedou velkou část procesu plánování podrobněji. Také provede nasazování ve skutečnosti.

## <a name="choose-an-azure-stack-deployment-connection-model"></a>Zvolte připojení modelu nasazení Azure Stack
Můžete nasadit Azure Stack buď připojené k Internetu (a do Azure) nebo odpojeno. Nejvíce výhod získat z Azure Stack, hybridní scénáře mezi Azure Stack a Azure, včetně byste k nasazení připojení k Azure. Tato volba určuje, jaké možnosti jsou k dispozici pro úložiště identit (Azure Active Directory nebo Active Directory Federation Services) a model fakturace (platit při použití na základě fakturace nebo založená na kapacitě fakturace) dle souhrnu v následující diagram a tabulka: 

![Azure Stack nasazení a fakturaci scénáře](media/azure-stack-connection-models/azure-stack-scenarios.png)  
  
> [!IMPORTANT]
> Toto je klíčová rozhodnutí bod! Výběr Active Directory Federation Services (AD FS) nebo Azure Active Directory (Azure AD) je jednorázový rozhodnutí, které je nutné provést v době nasazení. Nedá se změnit později bez nutnosti nového nasazení celého systému.  


|Možnosti|Připojení k Azure|Odpojení od Azure|
|-----|-----|-----|
|Azure AD|![Podporováno](media/azure-stack-connection-models/check.png)| |
|AD FS|![Podporováno](media/azure-stack-connection-models/check.png)|![Podporováno](media/azure-stack-connection-models/check.png)|
|Účtování podle využití|![Podporováno](media/azure-stack-connection-models/check.png)| |
|Účtování podle kapacity|![Podporováno](media/azure-stack-connection-models/check.png)|![Podporováno](media/azure-stack-connection-models/check.png)|
|Stáhnout balíčky aktualizací přímo do služby Azure Stack|![Podporováno](media/azure-stack-connection-models/check.png)|  |

Poté, co jste se rozhodli na model Azure připojení budou používat při nasazení Azure Stack, závislé na připojení a další rozhodnutí třeba identity úložiště a fakturace metodu. 

## <a name="next-steps"></a>Další postup

[Azure připojené rozhodnutí nasazení Azure Stack](azure-stack-connected-deployment.md)

[Azure odpojí rozhodnutí o nasazení služby Azure Stack](azure-stack-disconnected-deployment.md)
