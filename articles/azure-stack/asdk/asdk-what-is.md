---
title: Úvod k Azure Stack Development Kit (ASDK) | Dokumentace Microsoftu
description: Popisuje, co je ASDK a běžné případy použití za vaše rozhodnutí vyzkoušet Microsoft Azure Stack.
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
ms.topic: overview
ms.custom: mvc
ms.date: 10/25/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 62eccba1b876ca8272dacbbd97a1d28c8b796081
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50138306"
---
# <a name="what-is-the-azure-stack-development-kit"></a>Co je Azure Stack Development Kit?
[Microsoft Azure Stack integrované systémy](.\.\azure-stack-poc.md) rozsah od 4 až 16 uzlů a společně podporuje hardwarových partnerů a Microsoft. Použijte integrované systémy Azure Stack umožňuje nové scénáře pro vaše produkční úlohy. Pokud je pro vás, operátorovi služby Azure Stack, který spravuje integrovaných systémů infrastruktury a nabízí služby, přečtěte si naše [dokumentace operátora k](https://docs.microsoft.com/azure/azure-stack).

Azure Stack Development Kit (ASDK) je jedním uzlem nasazení služby Azure Stack, který můžete stáhnout a použít **zdarma**. Ve virtuálních počítačích spuštěn v počítači serveru jednoho hostitele, které musí splňovat nebo překračovat jsou nainstalovány všechny součásti ASDK [minimální požadavky na hardware](asdk-deploy-considerations.md#hardware). Je určená ASDK a poskytuje prostředí, ve kterém můžete vyhodnotit Azure Stack a vývoj moderních aplikací pomocí rozhraní API a nástrojů konzistentní s Azure v *-li se o neprodukční* prostředí. 

> [!IMPORTANT]
> ASDK není určen k použití nebo podporované v produkčním prostředí.

Protože všechny součásti ASDK se nasadí do jediného vývojového kit hostitelském počítači a k dispozici omezené fyzické prostředky. U ASDK nasazení virtuální počítače infrastruktury Azure stacku a tenanta virtuálních počítačů existovat vedle sebe na stejném serveru. Tato konfigurace není určena pro zkušební škálování a výkon.

ASDK je navržené pro poskytování Azure konzistentní hybridní cloudové prostředí pro:
- **Správci** (operátoři Azure stacku). ASDK je skvělým zdrojem k vyhodnocení a další informace o dostupných služeb Azure Stack.
- **Vývojáři**. ASDK slouží k vývoji hybridní nebo moderních aplikací v místním (vývojových/testovacích prostředí). Tato možnost nabízí opakovatelnosti vývojové prostředí před, nebo spolu s nasazení v produkčním prostředí Azure Stack. 

Podívejte se na toto krátké video získat další informace o ASDK:

> [!VIDEO https://www.youtube.com/embed/dbVWDrl00MM]


## <a name="asdk-and-multi-node-azure-stack-differences"></a>Rozdíly v Azure stacku ASDK a více uzly
Jednouzlový ASDK nasazení se liší od nasazení Azure Stack na víc uzlů v několika důležitých směrech –, které byste měli vědět.

|Popis|ASDK|Azure Stack s několika uzly|
|-----|-----|-----|
|**Škálování**|Na počítači jedním uzlem serveru jsou nainstalovány všechny součásti.|Může být v rozsahu od 4 až 16 uzlů.|
|**Odolnost**|Konfigurace s jedním uzlem nebude poskytovat vysokou dostupnost|[Zajistit vysokou dostupnost](.\.\azure-stack-key-features.md#high-availability-for-azure-stack) funkce jsou podporovány.|
|**Sítě**|ASDK používá virtuální počítač s názvem AzS-BGPNAT01 směrovat veškerý přenos v síti ASDK. Neexistují žádné požadavky na další přepínače.|Virtuální počítač AzS-BGPNAT01 neexistuje v nasazení na víc uzlů. Složitější [síťová infrastruktura směrování](.\.\azure-stack-network.md#network-infrastructure) je nezbytné včetně Top-Of-Rack (TOR), řadiče pro správu základní desky (BMC) a přepínače ohraničení (síti datového centra).|
|**Proces opravy a aktualizace**|Pokud chcete přesunout na novou verzi ASDK, je nutné znovu nasadit ASDK na hostitelském počítači development kit.|[Opravy a aktualizace](.\.\azure-stack-updates.md) proces používá k aktualizaci nainstalované verze služby Azure Stack.|
|**Podpora**|Fóra MSDN Azure Stack. Podpora Microsoft zákaznický servis a podporu šablon stylů CSS je *není* k dispozici pro neprodukční prostředí.|[Fóra MSDN Azure Stack](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStack) a podporu celé šablon stylů CSS.|
| | |

## <a name="learn-about-available-services"></a>Další informace o dostupných služeb
Jako operátor Azure stacku je potřeba vědět, služby, které můžete zpřístupnit uživatelům. Azure Stack podporuje podmnožinu služeb Azure a seznam podporovaných služeb budou i nadále v průběhu času vyvíjejí.

### <a name="foundational-services"></a>Základní služby
Ve výchozím nastavení, služby Azure Stack zahrnuje následující "základní služby" při nasazování ASDK:
- Compute
- Úložiště
- Sítě
- Key Vault

Pomocí těchto základních služeb můžou nabízet Infrastructure-as--Service (IaaS) uživatelům s minimální konfigurací.

### <a name="additional-services"></a>Další služby
V současné době jsou podporovány následující další služby Platform-as-a-Service (PaaS):
- App Service
- Azure Functions
- Databáze SQL a MySQL

> [!NOTE]
> Tyto služby vyžadují další konfiguraci, předtím, než je můžete zpřístupnit uživatelům a nejsou k dispozici ve výchozím nastavení při instalaci ASDK.

## <a name="service-roadmap"></a>Plán služby
Azure Stack bude pokračovat a přidat podporu pro další služby Azure. Další informace o co se chystá pomocí služby Azure Stack, najdete v článku [plán služby Azure Stack](https://azure.microsoft.com/roadmap/?tag=azure-stack). 


## <a name="next-steps"></a>Další postup
Abyste mohli začít, vaše rozhodnutí vyzkoušet Azure Stack, musíte si nejdřív [stáhnout nejnovější ASDK](asdk-download.md) a příprava hostitelském počítači ASDK. Po přípravě hostitele development kit, můžete nainstalovat ASDK a přihlásit na portály správce a uživatele chcete začít používat Azure Stack.