---
title: Azure Active Directory PoC Playbook Úvod | Microsoft Docs
description: Prozkoumejte a rychle implementovat scénáře identita a správa přístupu
services: active-directory
keywords: Azure active directory, scénářem, testování konceptu, PoC
documentationcenter: ''
author: dstefanMSFT
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: dstefan
ms.openlocfilehash: 80b7ba9507b85621e7b0623362774edc0c8cc729
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2017
ms.locfileid: "26601205"
---
# <a name="azure-active-directory-proof-of-concept-playbook-introduction"></a>Ověření služby Azure Active Directory koncept Playbook: Úvod

Tento článek obsahuje pokyny a prozkoumejte různé Azure AD možnosti testování konceptu (PoC). Předpokládanou cílovou skupinou tohoto dokumentu je Identity architektům, odborníci v oblasti IT a integrátory systémů

## <a name="how-to-use-this-playbook"></a>Jak používat tento Playbook

1. Použití [motiv](active-directory-playbook-ingredients.md#theme) části a vyberte vlastnost zájmu na základě potřeb.  
2. Rozsah PoC výběrem scénáře, které jsou zarovnané s obchodním cílům. Kratší tím lépe. Doporučujeme, abyste ho jako krátký a stručným nejblíže k vyjádření hodnota zúčastněným stranám a současně minimalizujete její složitost si uvědomí, je to.  
3. Použití [implementace](active-directory-playbook-implementation.md) části, abyste pochopili scénáře a co by se rozumí pro vaše prostředí. V každém scénáři jsme popisují, jak ho nastavit (takzvaný [stavební bloky](active-directory-playbook-building-blocks.md)) a jak se orientovat scénáře. 
4. Každý stavebním blokem vysvětluje požadavky potřebné, jakož i Přibližná doba na dokončení. To vám může pomoci během procesu plánování. 
5. Podle toho, 1 – 3 výše, zadejte [prostředí](active-directory-playbook-ingredients.md#environment) do kterého chcete provést. Doporučujeme usilovat o provozním prostředí podívat, funkční prostředí pro uživatele. 
6. Pokud s konfliktní požadavky, použijte tato matice užitečné kompromis 
   * Motiv na střed zobrazení hodnoty  
   * Plynulost připravit, nastavení a spuštění scénáře 
   * Minimální doba provádění cílové scénáře 
   * Jako blízko produkční jako vhodný v rámci vaší omezení 

>[!NOTE]
> V tomto článku zobrazí se některé produkty, které jsou uvedené jako příklady ke zvýšení pohodlí a aplikací konkrétní třetí strany. Azure AD podporuje tisíce aplikace v našem [galerii aplikací](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) , které můžete použít podle konkrétních potřeb a prostředí. 



[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]