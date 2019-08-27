---
title: Zprovoznění v Azure Sentinel Preview | Microsoft Docs
description: Naučte se shromažďovat data v Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: rkarlin
ms.openlocfilehash: b609dc70c45941ec1132c7cdf614cf9bec8119ff
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/26/2019
ms.locfileid: "70019023"
---
# <a name="on-board-azure-sentinel-preview"></a>On-Board Azure Sentinel Preview

> [!IMPORTANT]
> Služba Azure Sentinel je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

V tomto rychlém startu se dozvíte, jak se připojit ke službě Azure Sentinel. 

Aby bylo možné připojit se k Azure Sentinel, musíte nejdřív povolit službu Azure Sentinel a pak připojit zdroje dat. Služba Azure Sentinel je dodávána s řadou konektorů pro řešení Microsoftu, která je dostupná předem a poskytuje integraci v reálném čase, včetně řešení ochrany před internetovými útoky, Microsoft 365ch zdrojů, včetně Office 365, Azure AD, Azure ATP a Microsoft Cloud App Security a další. Kromě toho jsou k dispozici Integrované konektory k širšímu ekosystému zabezpečení pro řešení jiných výrobců než Microsoftu. K propojení zdrojů dat s funkcí Sentinel Azure můžete použít také běžné formáty událostí, syslog nebo REST API.  

Po připojení zdrojů dat si můžete vybrat z Galerie expertů vytvořených řídicích panelů, které jsou na základě vašich dat Surface Insights. Tyto řídicí panely je možné snadno přizpůsobit podle svých potřeb.


## <a name="global-prerequisites"></a>Globální požadavky

- Aktivní předplatné Azure, pokud ho ještě nemáte, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

- Log Analytics pracovní prostor. Informace o tom, jak [vytvořit pracovní prostor Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md)

-  Pokud chcete povolit Azure Sentinel, potřebujete oprávnění přispěvatele k předplatnému, ve kterém se nachází pracovní prostor Azure Sentinel. 
- Pokud chcete použít Azure Sentinel, potřebujete oprávnění Přispěvatel nebo čtenář ve skupině prostředků, do které pracovní prostor patří.
- Pro připojení konkrétních zdrojů dat můžou být potřeba další oprávnění.
 
## Povolit službu Azure Sentinel<a name="enable"></a>

1. Přejít do Azure Portal.
2. Ujistěte se, že je zaškrtnuté předplatné, ve kterém je Azure Sentinel vytvořeno. 
3. Vyhledejte Sentinel Azure. 
   ![search](./media/quickstart-onboard/search-product.png)

1. Klikněte na **+ Přidat**.
1. Vyberte pracovní prostor, který chcete použít, nebo vytvořte nový. Službu Azure Sentinel můžete spustit ve více než jednom pracovním prostoru, ale data jsou izolovaná do jednoho pracovního prostoru.

   ![search](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - Výchozí pracovní prostory vytvořené nástrojem Azure Security Center se v seznamu nezobrazí. na ně nemůžete nainstalovat službu Azure Sentinel.
   > - V pracovních prostorech, které jsou nasazené v některé z těchto oblastí, můžete spustit Azure Sentinel: Austrálie – východ, Austrálie – jihovýchod, Kanada – střed, Střed Indie, Východní USA, Východní USA 2 EUAP (Kanárské), Japonsko – východ, jihovýchodní Asie, Velká Británie – jih, Západní Evropa, Západní USA 2, Středozápadní USA, Francie – střed, Korea – střed, Severní Evropa, Východní USA 2, východní Asie, Západní USA, Střed USA, Střed USA – jih.


6. Klikněte na **Přidat Sentinel Azure**.
  

## <a name="connect-data-sources"></a>Připojení zdrojů dat

Služba Azure Sentinel vytvoří připojení ke službám a aplikacím pomocí připojení ke službě a předávání událostí a protokolů do služby Azure Sentinel. U počítačů a virtuálních počítačů můžete nainstalovat agenta Azure Sentinel, který shromáždí protokoly a předává je do Azure Sentinel. Pro brány firewall a proxy servery využívá Azure Sentinel Server Linux syslog. Agent se nainstaluje na něj a odkud agent shromáždí soubory protokolu a předá je do Azure Sentinel. 
 
1. Klikněte na **shromažďování dat**.
2. K dispozici je dlaždice pro každý zdroj dat, ke kterému se můžete připojit.<br>
Klikněte například na **Azure Active Directory**. Pokud tento zdroj dat připojíte, budete streamovat všechny protokoly z Azure AD do Azure Sentinel. Můžete vybrat typ protokolů, které jsou v síti WAN k dispozici, přihlášení a protokoly auditu. <br>
V dolní části poskytuje Azure Sentinel doporučení pro řídicí panely, které byste měli nainstalovat pro každý konektor, abyste mohli okamžitě získat zajímavé přehledy napříč daty. <br> Postupujte podle pokynů k instalaci nebo se [podívejte na příslušné Průvodce připojením](connect-data-sources.md) , kde najdete další informace. Informace o datových konektorech najdete v tématu [připojení služeb společnosti Microsoft](connect-data-sources.md).

Po připojení zdrojů dat začnou vaše data zasílat Stream do Azure Sentinel a je připravená, abyste mohli začít pracovat s. Protokoly můžete zobrazit ve [vestavěných řídicích panelech](quickstart-get-visibility.md) a začít sestavovat dotazy v Log Analytics a [Prozkoumat data](tutorial-investigate-cases.md).



## <a name="next-steps"></a>Další postup
V tomto dokumentu jste se seznámili s připojením zdrojů dat ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats.md).
- Streamuje data z [běžných chybových formátů](connect-common-event-format.md) do Azure Sentinel.
