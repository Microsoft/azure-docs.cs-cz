---
title: Co je Azure Stack? | Dokumenty Microsoft
description: Zásobník Azure umožňuje spustit služby Azure ve vašem datovém centru.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/22/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: mvc
ms.openlocfilehash: 863c1ec562cd71af0df69ccc0547e16d02c7ee82
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="what-is-azure-stack"></a>Co je Azure Stack?

Zásobník Microsoft Azure je hybridní cloudové platformy, která umožňuje poskytovat služby Azure z vaší organizace datového centra.  Zásobník Azure umožňuje nové scénáře pro moderní aplikace v klíčových scénářů, jako je okraj a odpojených prostředích nebo konkrétní požadavky zabezpečení a dodržování předpisů na schůzku.  Azure zásobník je k dispozici v dvě možnosti nasazení podle svých potřeb.

## <a name="azure-stack-integrated-systems"></a>Integrované systémy pro službu Azure Stack
Azure zásobníku integrované systémy jsou nabízena prostřednictvím partnerství společnosti Microsoft a [hardwarovými partnery](https://azure.microsoft.com/overview/azure-stack/integrated-systems/), vytváření řešení, který nabízí cloudové pracovníky inovací vyvážit s jednoduchost ve správě.  Protože zásobník Azure je poskytován jako integrovaný systém hardwaru a softwaru, budete se moci správného množství flexibilitu a řízení, při přijetí stále inovací z cloudu.  Azure zásobníku integrované systémy rozsahu velikost z uzlů 4-12 a společně podporuje hardwaru partnera a společnosti Microsoft.  Použití Azure zásobníku integrované systémy, které umožňují nové scénáře pro produkční zatížení.    

## <a name="azure-stack-development-kit"></a>Vývojová sada pro Azure Stack
Microsoft [Azure zásobníku Development Kit (ASDK)](.\asdk\asdk-what-is.md) je jedním uzlem nasazení zásobník Azure, který můžete použít k vyhodnocení a další informace o Azure zásobníku.  Můžete také použít ASDK jako vývojářského prostředí, kde můžete vyvíjet pomocí rozhraní API a nástroje, které jsou konzistentní s Azure. ASDK není určena pro použití jako provozním prostředí.

ASDK má následující omezení:
* ASDK je přidružen jeden Azure Active Directory (Azure AD) nebo zprostředkovatele identity služby Active Directory Federation Services (AD FS). Můžete vytvořit více uživatelů v tomto adresáři a přiřadit předplatná jednotlivých uživatelů.
* Se všemi součástmi v jednom počítači nasazen nejsou omezené fyzické prostředky k dispozici pro klientské prostředky. Tato konfigurace není určen pro vyhodnocení škálování nebo výkonu.
* Scénáře pro sítě je omezen z důvodu požadavek na jednom nebo síťový adaptér hostitele.  

## <a name="next-steps"></a>Další postup
[Klíčové funkce a koncepty](azure-stack-key-features.md)

[Azure zásobník: Rozšíření Azure (pdf)](https://azure.microsoft.com/en-us/resources/azure-stack-an-extension-of-azure/)

