---
title: Co je Azure Stack? | Dokumenty Microsoft
description: Zásobník Azure vám umožní spustit služby Azure ve vašem datovém centru.
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
ms.date: 06/04/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.custom: mvc
ms.openlocfilehash: 848df59b01cc0c03b9a5f3dae2644d469c8651bb
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234882"
---
# <a name="what-is-azure-stack"></a>Co je Azure Stack?

Zásobník Microsoft Azure je hybridní cloudové platformy, která umožňuje poskytovat služby Azure ve vašem datovém centru. Tato platforma je navržen pro podporu měnící podnikové požadavky. Zásobník Azure můžete povolit nové scénáře pro moderní aplikace, jako je například okraj a odpojených prostředích, nebo splňovat požadavky na zabezpečení a dodržování předpisů.

Azure zásobník je k dispozici v dvě možnosti nasazení podle svých potřeb.

## <a name="azure-stack-integrated-systems"></a>Integrované systémy pro službu Azure Stack
Azure zásobníku integrované systémy jsou nabízena prostřednictvím partnerství společnosti Microsoft a [hardwarovými partnery](https://azure.microsoft.com/overview/azure-stack/integrated-systems/), vytváření řešení, které nabízí cloudové pracovníky inovace a výpočetních zjednodušení správy. Protože zásobník Azure je poskytován jako systém integrované hardwaru a softwaru, máte flexibilitu a řízení, které potřebujete, spolu se schopností inovacemi. Zajistěte z cloudu. Azure zásobníku integrované systémy rozsahu velikost z uzlů 4-12 a společně podporuje hardwaru partnera a společnosti Microsoft.  Použití Azure zásobníku integrované systémy a vytvářet nové scénáře nasazení nového řešení pro produkční zatížení.

## <a name="azure-stack-development-kit"></a>Vývojová sada pro Azure Stack

Microsoft [Azure zásobníku Development Kit (ASDK)](.\asdk\asdk-what-is.md) je jedním uzlem nasazení zásobník Azure, který můžete použít k vyhodnocení a další informace o Azure zásobníku.  Můžete také použít ASDK jako vývojářského prostředí pro vývoj aplikací pomocí rozhraní API a nástrojů, který je v souladu s Azure.

>[!Note]
>ASDK není určena pro použití jako provozním prostředí.

ASDK má následující omezení:

* ASDK je přidružen jeden Azure Active Directory (Azure AD) nebo zprostředkovatele identity služby Active Directory Federation Services (AD FS). Můžete vytvořit více uživatelů v tomto adresáři a přiřadit předplatná jednotlivých uživatelů.
* Protože Azure zásobníku součásti jsou nasazeny na jeden hostitelský počítač, existují omezené fyzické prostředky k dispozici pro klientské prostředky. Tato konfigurace není určeno k vyhodnocení škálování nebo výkonu.
* Scénáře pro sítě je omezen z důvodu jeden hostitel a síťový adaptér požadavky na nasazení.

## <a name="next-steps"></a>Další postup

- [Klíčové funkce a koncepty](azure-stack-key-features.md)
- [Azure zásobník: Rozšíření Azure (pdf)](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)
