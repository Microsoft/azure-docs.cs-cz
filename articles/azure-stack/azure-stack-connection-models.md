---
title: Azure zásobníku integrované systémy připojení modely | Microsoft Docs
description: Určení plánování rozhodnutí pro více uzly Azure zásobníku nasazení.
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
ms.date: 01/31/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: e6c94ef1172ea6380a94d5907c24069ed8c48ff5
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2018
ms.locfileid: "29118784"
---
# <a name="azure-stack-integrated-systems-connection-models"></a>Azure zásobníku integrované systémy připojení modely
Pokud byste chtěli v systému Azure zásobníku integrované, budete potřebovat zjistit [důležité informace o integraci několik datacenter](azure-stack-datacenter-integration.md) pro nasazení Azure zásobníku k určení, jak v systému se vejde do datového centra. Kromě toho budete muset rozhodnout, přesně jak bude integrace Azure zásobníku do prostředí hybridního cloudu. Tento článek obsahuje přehled těchto hlavní rozhodnutí, včetně připojení k Azure, úložiště identit a fakturace rozhodnutí týkající se modelu.

Pokud se rozhodnete koupit integrovaný systém, pomáhá dodavatele hardwaru, výrobce (OEM), vás provede velkou část procesu plánování podrobněji. Také provede skutečné nasazení.

## <a name="choose-an-azure-stack-deployment-connection-model"></a>Zvolte připojení model nasazení Azure zásobníku
Můžete nasadit Azure zásobníku buď připojené k Internetu (a do Azure) nebo odpojena. K získání využívat výhod z zásobník Azure, včetně hybridní scénáře mezi zásobník Azure a Azure, by chcete nasadit připojení k Azure. Tato volba definuje, které možnosti jsou k dispozici pro úložiště identit (Azure Active Directory nebo Active Directory Federation Services) a fakturační model (platit jako vám na základě použití fakturace nebo na základě kapacity fakturace) popsaná v následující obrázek a tabulka: 

![Azure zásobníku nasazení a fakturace scénáře](media/azure-stack-connection-models/azure-stack-scenarios.png)  
  
> [!IMPORTANT]
> Toto je bod klíčová rozhodnutí! Jednorázové rozhodnutí, které je nutné provést v době nasazení je výběr Active Directory Federation Services (AD FS) nebo Azure Active Directory (Azure AD). Nedá se změnit později bez opětovného nasazení celého systému.  


|Možnosti|Připojení k Azure|Odpojení od Azure|
|-----|-----|-----|
|Azure AD|![Podporováno](media/azure-stack-connection-models/check.png)| |
|AD FS|![Podporováno](media/azure-stack-connection-models/check.png)|![Podporováno](media/azure-stack-connection-models/check.png)|
|Na základě spotřeby fakturace|![Podporováno](media/azure-stack-connection-models/check.png)| |
|Na základě kapacity fakturace|![Podporováno](media/azure-stack-connection-models/check.png)|![Podporováno](media/azure-stack-connection-models/check.png)|
|Stáhnout balíky aktualizací přímo do Azure zásobníku|![Podporováno](media/azure-stack-connection-models/check.png)|  |

Poté, co jste se rozhodli na modelu připojení Azure má být použit pro nasazení Azure zásobníku, musí být provedeny závislé na připojení, další rozhodnutí pro úložiště identit a fakturace metodu. 

## <a name="next-steps"></a>Další postup

[Azure rozhodnutí o nasazení Azure zásobníku připojené](azure-stack-connected-deployment.md)

[Azure odpojen rozhodnutí o nasazení Azure zásobníku](azure-stack-disconnected-deployment.md)
