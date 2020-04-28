---
title: 'Rychlý Start: zprovoznění v Azure Sentinel'
description: Pomocí tohoto dokumentu pro rychlý Start se dozvíte, jak shromažďovat data v Azure Sentinel.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: d4e7077c338586298c5e7f0805c3d47b08a7a108
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82184329"
---
# <a name="quickstart-on-board-azure-sentinel"></a>Rychlý Start: zprovoznění služby Azure Sentinel

V tomto rychlém startu se dozvíte, jak připojit k Azure Sentinel. 

Aby bylo možné připojit se k Azure Sentinel, musíte nejdřív povolit službu Azure Sentinel a pak připojit zdroje dat. Služba Azure Sentinel se dodává s řadou konektorů pro řešení Microsoftu, které jsou dostupné mimo kancelář, a poskytuje integraci v reálném čase, včetně řešení ochrany před internetovými útoky, Microsoft 365 zdrojů, včetně Office 365, Azure AD, Azure ATP a Microsoft Cloud App Security a dalších. Kromě toho jsou k dispozici Integrované konektory k širšímu ekosystému zabezpečení pro řešení jiných výrobců než Microsoftu. K propojení zdrojů dat s funkcí Sentinel Azure můžete použít také běžné formáty událostí, syslog nebo REST API.  

Po připojení zdrojů dat si můžete vybrat z Galerie odborných vytvořených sešitů, které jsou na základě vašich dat Surface Insights. Tyto sešity je možné snadno přizpůsobit podle svých potřeb.

>[!IMPORTANT] 
> Informace o účtovaných cenách při používání Azure Sentinel najdete v tématu [ceny služby Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/).
  

## <a name="global-prerequisites"></a>Globální požadavky

- Aktivní předplatné Azure, pokud ho ještě nemáte, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

- Log Analytics pracovní prostor. Naučte se, jak [vytvořit pracovní prostor Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md). Další informace o Log Analytics pracovních prostorech najdete v tématu [navrhování Azure Monitorch protokolů nasazení](../azure-monitor/platform/design-logs-deployment.md).

- Pokud chcete povolit Azure Sentinel, potřebujete oprávnění přispěvatele k předplatnému, ve kterém se nachází pracovní prostor Azure Sentinel. 
- Pokud chcete použít Azure Sentinel, potřebujete oprávnění Přispěvatel nebo čtenář ve skupině prostředků, do které pracovní prostor patří.
- K připojení konkrétních zdrojů dat se můžou potřebovat další oprávnění.
- Služba Azure Sentinel je placená služba. Informace o cenách najdete v tématu informace [o službě Azure Sentinel](https://go.microsoft.com/fwlink/?linkid=2104058).
 
## <a name="enable-azure-sentinel"></a>Povolit službu Azure Sentinel<a name="enable"></a>

1. Přihlaste se k portálu Azure. Ujistěte se, že je zaškrtnuté políčko předplatné, ve kterém je Azure Sentinel vytvořeno.

1. Vyhledejte a vyberte **Azure Sentinel**.

   ![search](./media/quickstart-onboard/search-product.png)

1. Vyberte **Přidat**.

1. Vyberte pracovní prostor, který chcete použít, nebo vytvořte nový. Službu Azure Sentinel můžete spustit ve více než jednom pracovním prostoru, ale data jsou izolovaná do jednoho pracovního prostoru.

   ![search](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - Výchozí pracovní prostory vytvořené nástrojem Azure Security Center se v seznamu nezobrazí. na ně nemůžete nainstalovat službu Azure Sentinel.
   > - U všech [oblastí GA Log Analytics](https://azure.microsoft.com/global-infrastructure/services/?products=monitor) s výjimkou oblastí Čína a Německo (svrchovan) může Azure Sentinel běžet v pracovních prostorech. Data generovaná službou Azure Sentinel (jako jsou incidenty, záložky a pravidla výstrah, která mohou obsahovat data zákaznická z těchto pracovních prostorů) se ukládají v Evropě (pro pracovní prostory v Evropě), v Austrálii (pro pracovní prostory v Austrálii) nebo v Východní USA (pro pracovní prostory v jiné oblasti).

1. Vyberte **Přidat Sentinel Azure**.
  

## <a name="connect-data-sources"></a>Připojení zdrojů dat

Služba Azure Sentinel vytvoří připojení ke službám a aplikacím pomocí připojení ke službě a předávání událostí a protokolů do služby Azure Sentinel. U počítačů a virtuálních počítačů můžete nainstalovat agenta Azure Sentinel, který shromáždí protokoly a předává je do Azure Sentinel. Pro brány firewall a proxy servery využívá Azure Sentinel Server Linux syslog. Agent se nainstaluje na něj a odkud agent shromáždí soubory protokolu a předá je do Azure Sentinel. 
 
1. Klikněte na **datové konektory**.
2. K dispozici je dlaždice pro každý zdroj dat, ke kterému se můžete připojit.<br>
Klikněte například na **Azure Active Directory**. Pokud tento zdroj dat připojíte, budete streamovat všechny protokoly z Azure AD do Azure Sentinel. Můžete vybrat typ protokolů, které jsou v síti WAN k dispozici, přihlášení a protokoly auditu. <br>
V dolní části poskytuje Azure Sentinel doporučení pro to, které sešity byste měli nainstalovat pro každý konektor, abyste mohli okamžitě získat zajímavé přehledy napříč vašimi daty. <br> Postupujte podle pokynů k instalaci nebo se [podívejte na příslušné Průvodce připojením](connect-data-sources.md) , kde najdete další informace. Informace o datových konektorech najdete v tématu [připojení služeb společnosti Microsoft](connect-data-sources.md).

Po připojení zdrojů dat začnou vaše data zasílat Stream do Azure Sentinel a je připravená, abyste mohli začít pracovat s. Protokoly můžete zobrazit ve [vestavěných řídicích panelech](quickstart-get-visibility.md) a začít sestavovat dotazy v Log Analytics a [Prozkoumat data](tutorial-investigate-cases.md).



## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se seznámili s připojením zdrojů dat ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- Streamování dat z [běžných zařízení formátu událostí](connect-common-event-format.md) do Azure Sentinel.
