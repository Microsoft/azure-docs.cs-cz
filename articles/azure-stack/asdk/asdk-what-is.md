---
title: "Úvod do Azure zásobníku Development Kit (ASDK) | Microsoft Docs"
description: "Popisuje, co je ASDK a běžné případy použití pro vyhodnocení zásobníku Microsoft Azure."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 5a268a29c7a767084049bf56270aa8bc9d2ccc3f
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="what-is-the-azure-stack-development-kit"></a>Co je Azure zásobníku Development Kit?
[Microsoft Azure zásobníku integrované systémy](.\.\azure-stack-poc.md) rozsahu velikost z uzlů 4-12 a společně podporuje hardwaru partnera a společnosti Microsoft. Použití Azure zásobníku integrované systémy, které umožňují nové scénáře pro produkční zatížení. Pokud jste Azure zásobníku subjektu, který spravuje infrastrukturu integrované systémy a nabízí služby, přečtěte si naše [operátor dokumentaci](https://docs.microsoft.com/azure/azure-stack).

Azure zásobníku Development Kit (ASDK) je jedním uzlem nasazení zásobník Azure, který můžete stáhnout a použít **zdarma**. Ve virtuálních počítačích, které musí splňují nebo překračují spuštěná na počítači serveru jednom hostiteli jsou nainstalovány všechny součásti ASDK [minimální požadavky na hardware](asdk-deploy-considerations.md#hardware). ASDK je určen a poskytuje prostředí, ve kterém můžete vyhodnotit Azure zásobníku a vývoj moderních aplikací pomocí rozhraní API a nástrojů konzistentní s Azure v rámci *mimo produkční* prostředí. 

> [!IMPORTANT]
> ASDK není určen k použití nebo v produkčním prostředí podporované.

Protože všechny součásti ASDK se nasadí do jednoho development kit hostitelský počítač a nejsou omezena fyzické prostředky k dispozici. U nasazení ASDK infrastruktury Azure zásobníku virtuálních počítačů i klienta virtuálních počítačů nacházet na stejném serveru. Tato konfigurace není určen pro vyhodnocení škálování nebo výkonu.

ASDK určená k poskytování Azure konzistentní hybridní cloudové prostředí pro:
- **Správci** (operátory Azure zásobníku). ASDK je skvělým zdrojem k vyhodnocení a další informace o dostupných službách Azure zásobníku.
- **Vývojáři**. ASDK slouží k vývoji hybridní nebo moderní aplikace místně (vývojářů a testovací prostředí). To nabízí opakovatelnosti vývojového prostředí před nebo souběžně s Azure zásobníku nasazení v produkčním prostředí. 

Podívejte se na následující krátké video Další informace o ASDK:

> [!VIDEO https://www.youtube.com/embed/dbVWDrl00MM]


## <a name="asdk-and-multi-node-azure-stack-differences"></a>Rozdíly v Azure zásobníku ASDK a více uzly
Jeden uzel ASDK nasazení se liší od nasazení na víc uzlů Azure zásobníku několik důležitých způsoby, které byste měli vědět.

|Popis|ASDK|Zásobník Azure několika uzly|
|-----|-----|-----|
|**Škálování**|Všechny součásti jsou nainstalovány na počítači jedním uzlem serveru.|Může být v rozsahu velikost z uzlů 4-12.|
|**Odolnost proti**|Jeden uzel konfigurace nebude poskytovat vysokou dostupnost|[Vysoké dostupnosti probíhá](.\.\azure-stack-key-features.md#high-availability-for-azure-stack) jsou podporované možnosti.|
|**Sítě**|ASDK používá virtuální počítač s názvem AzS BGPNAT01 směrovat veškerý přenos v síti ASDK. Neexistují žádné další přepínače požadavky.|Virtuální počítač AzS BGPNAT01 neexistuje v nasazení na víc uzlů. Složitější [síťové infrastruktuře směrování](.\.\azure-stack-network.md#network-infrastructure) je nutné včetně Top-Of-Rack (TOR), řadiče pro správu základní desky (BMC) a přepínače ohraničení (síť datového centra).|
|**Proces opravy a aktualizace**|Chcete-li přesunout na novou verzi ASDK, je nutné znovu nasadit ASDK na hostitelském počítači development kit.|[Opravy a aktualizace](.\.\azure-stack-updates.md) proces použít k aktualizaci nainstalovaná verze zásobník Azure.|
|**Podpora**|Fórum MSDN Azure zásobníku. Podpora Microsoft zákaznický servis a podporu (CSS) je *není* k dispozici pro nevýrobní prostředí.|[Fórum MSDN Azure zásobníku](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStack) a podporu úplné šablon stylů CSS.|
| | |

## <a name="learn-about-available-services"></a>Další informace o dostupných služeb
Jako operátor zásobník Azure potřebujete vědět, které služby můžete zpřístupnit uživatelům. Azure zásobníku podporuje podmnožinu služeb Azure a seznam služeb podporovaných bude nadále průběhu času.

### <a name="foundational-services"></a>Základní služby
Ve výchozím nastavení, Azure Stack zahrnuje následující "základní služby" při nasazení ASDK:
- Compute
- Úložiště
- Sítě
- Key Vault

S těmito službami základní nabízejí uživatelům s minimální konfigurací infrastruktury jako služba (IaaS).

### <a name="additional-services"></a>Další služby
V současné době jsou podporovány následující další služby platformy jako služba (PaaS):
- App Service
- Azure Functions
- Databáze SQL a MySQL

> [!NOTE]
> Tyto služby vyžadovat další konfiguraci před jste je zpřístupnit uživatelům a nejsou k dispozici ve výchozím nastavení při instalaci ASDK.

## <a name="service-roadmap"></a>Přehled služby
Přidání podpory pro další služby Azure bude Azure zásobníku. Další informace o co Připravujeme další zásobníkem Azure najdete v tématu [zásobník Azure plán](https://azure.microsoft.com/roadmap/?tag=azure-stack). 


## <a name="next-steps"></a>Další postup
Abyste mohli začít, vyhodnocení zásobník Azure, je nutné připravit hostitele development kit počítač serveru a potom [nainstalovat ASDK](asdk-deploy.md). Potom můžete přihlásit k portálů správce a uživatele a začít používat Azure zásobníku.