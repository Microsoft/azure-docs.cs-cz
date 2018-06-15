---
title: Ochrana aplikací v Azure Security Center | Microsoft Docs
description: Tento dokument adresy doporučení v Azure Security Center, které vám pomohou ochránit vaše aplikace Azure a zůstat souladu se zásadami zabezpečení.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: b5fc7a9e-24b1-415f-b3b5-62a53f5dd424
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/01/2016
ms.author: terrylan
ms.openlocfilehash: b5bc02082fa8c2681aa67910729870921fec329d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23866265"
---
# <a name="protecting-your-applications-in-azure-security-center"></a>Ochrana aplikací v Azure Security Center
Azure Security Center analyzuje stav zabezpečení vašich prostředků Azure. Když Security Center identifikuje potenciální ohrožení zabezpečení, vytvoří se doporučení, která vás provede procesem konfigurace potřebných kontrol.  Doporučení platí pro typy prostředků Azure: virtuální počítače (VM), sítě, SQL a aplikace.

Tento článek se zaměřuje na doporučení, které se vztahují k aplikacím.  Aplikace center doporučení ohledně nasazení brány firewall webových aplikací.  Použijte v následující tabulce vám pomohou pochopit doporučení k dispozici aplikace a co každé z nich dělá Pokud použijete ho jako odkaz.

## <a name="available-application-recommendations"></a>Doporučení k dispozici aplikace
| Doporučení | Popis |
| --- | --- |
| [Přidání brány firewall webových aplikací](security-center-add-web-application-firewall.md) |Doporučuje, která můžete nasadit brány firewall webových aplikací (firewall webových aplikací) pro koncových bodů webové. Doporučení firewall webových aplikací je zobrazený pro všechny veřejné přístupných IP adresy (IP úrovni Instance nebo IP skupinu s vyrovnáváním zatížení), skupinu zabezpečení sítě spojenou s otevřete příchozí webovými porty (80,443).</br></br>Security Center doporučuje zřízení firewall webových aplikací, které pomáhají bránit proti útokům na cílení na vaše webové aplikace na virtuální počítače a služby App Service Environment. Je aplikaci služby prostředí (App Service Environment) [Premium](https://azure.microsoft.com/pricing/details/app-service/) služby možnost plánu služby Azure App Service, která poskytuje plně izolovaném a vyhrazeném prostředí pro zabezpečené spouštění aplikací Azure App Service. Další informace o App Service Environment, najdete v článku [dokumentace k aplikaci služby prostředí](../app-service/environment/intro.md).</br></br>Přidáním těchto aplikací na vaše stávající nasazení firewall webových aplikací můžete chránit několika webových aplikací ve službě Security Center. |
| [Finalizace ochrany aplikací](security-center-add-web-application-firewall.md#finalize-application-protection) |K dokončení konfigurace firewall webových aplikací, musí být přenos přesměruje do zařízení firewall webových aplikací. Následující toto doporučení dokončení změny potřebné instalační. |

## <a name="see-also"></a>Viz také
Další informace o doporučení, které se vztahují na jiné typy prostředků Azure, naleznete v následujících tématech:

* [Ochrana virtuálních počítačů v Azure Security Center](security-center-virtual-machine-recommendations.md)
* [Ochrana sítě v Azure Security Center](security-center-network-recommendations.md)
* [Ochrana služby Azure SQL v Azure Security Center](security-center-sql-service-recommendations.md)

Pokud se o službě Security Center chcete dozvědět víc, pročtěte si tato témata:

* [Nastavení zásad zabezpečení v Azure Security Center](security-center-policies.md) – Zjistěte, jak konfigurovat zásady zabezpečení pro svá předplatná Azure a skupiny prostředků.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md) – Přečtěte si nejčastější dotazy k používání této služby.
