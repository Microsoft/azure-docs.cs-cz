---
title: 'Úvodní příručka: Na palubě v Azure Sentinelu'
description: Zjistěte, jak shromažďovat data v Azure Sentinelu podle tohoto dokumentu Rychlého startu.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: quickstart
ms.date: 12/05/2019
ms.openlocfilehash: bcb6cd7393419fb8fc75decb00a8f4165d15668c
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025267"
---
# <a name="quickstart-on-board-azure-sentinel"></a>Úvodní příručka: Na palubě Azure Sentinelu

V tomto rychlém startu se dozvíte, jak na palubě Azure Sentinel. 

Chcete-li napalubě Azure Sentinel, musíte nejprve povolit Azure Sentinel a pak připojit zdroje dat. Azure Sentinel je dodáván s řadou konektorů pro řešení Microsoft, které jsou k dispozici iz v boxu a poskytují integraci v reálném čase, včetně řešení Microsoft Threat Protection, zdrojů Microsoft 365, včetně Office 365, Azure AD, Ochrany ATP v Azure a Zabezpečení aplikací Microsoft Cloud a další. Kromě toho existují integrované konektory pro širší ekosystém zabezpečení pro řešení jiných společností než Microsoft. Můžete také použít běžný formát událostí, Syslog nebo REST-API pro připojení zdrojů dat s Azure Sentinel.  

Po připojení zdrojů dat si vyberte z galerie odborně vytvořených sešitů, které na základě vašich dat vypracují přehledy. Tyto sešity lze snadno přizpůsobit vašim potřebám.

>[!IMPORTANT] 
> Informace o poplatcích vzniklých při používání Azure Sentinelu najdete v [tématu ceny Azure Sentinelu](https://azure.microsoft.com/pricing/details/azure-sentinel/).
  

## <a name="global-prerequisites"></a>Globální předpoklady

- Aktivní předplatné Azure, pokud ho nemáte, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

- Pracovní prostor Log Analytics. Přečtěte si, jak [vytvořit pracovní prostor Analýzy protokolů](../log-analytics/log-analytics-quick-create-workspace.md). Další informace o pracovních prostorech Log Analytics najdete v [tématu Návrh nasazení protokolů azure monitoru](../azure-monitor/platform/design-logs-deployment.md).

- Chcete-li povolit Azure Sentinel, potřebujete oprávnění přispěvatele k předplatnému, ve kterém se nachází pracovní prostor Azure Sentinelu. 
- Chcete-li používat Azure Sentinel, potřebujete buď přispěvatele nebo čtenáře oprávnění na skupinu prostředků, které pracovní prostor patří.
- K připojení konkrétních zdrojů dat mohou být potřebná další oprávnění.
- Azure Sentinel je placená služba. Informace o cenách najdete [v tématu O Azure Sentinelu](https://go.microsoft.com/fwlink/?linkid=2104058).
 
## <a name="enable-azure-sentinel"></a>Povolení Azure Sentinelu<a name="enable"></a>

1. Přihlaste se k portálu Azure. Ujistěte se, že je vybráno předplatné, ve kterém se azure sentinel uvázne.

1. Vyhledejte a vyberte **Azure Sentinel**.

   ![search](./media/quickstart-onboard/search-product.png)

1. Vyberte **Přidat**.

1. Vyberte pracovní prostor, který chcete použít, nebo vytvořte nový. Azure Sentinel můžete spustit ve více než jednom pracovním prostoru, ale data jsou izolovaná do jednoho pracovního prostoru.

   ![search](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - Výchozí pracovní prostory vytvořené službou Azure Security Center se v seznamu nezobrazí. Azure Sentinel na ně nemůžete nainstalovat.
   > - Azure Sentinel můžete spustit na pracovních prostorech v libovolné oblasti GA log analytics s výjimkou oblasti [Číny,](https://azure.microsoft.com/global-infrastructure/services/?products=monitor) Německa a Azure governmentu. Data generovaná službou Azure Sentinel (například incidenty, záložky a pravidla výstrah, která mohou obsahovat některá zákaznická data ztěchto pracovních prostorů) se ukládají buď v západní Evropě (pro pracovní prostory umístěné v Evropě) nebo ve východní USA (pro všechny pracovní prostory založené na USA a také v jakékoli jiné oblasti kromě Evropy).

1. Vyberte **Přidat Azure Sentinel**.
  

## <a name="connect-data-sources"></a>Připojení zdrojů dat

Azure Sentinel vytvoří připojení ke službám a aplikacím připojením ke službě a předáváním událostí a protokolů do Azure Sentinelu. Pro počítače a virtuální počítače můžete nainstalovat agenta Azure Sentinelu, který shromažďuje protokoly a předává je do Azure Sentinelu. Pro brány firewall a proxy servery Azure využívá server Linux Syslog. Agent je nainstalovaný na něm a ze kterého agent shromažďuje soubory protokolu a předává je do Azure Sentinelu. 
 
1. Klepněte na **položku Datové konektory**.
2. Pro každý zdroj dat, který můžete připojit, je k dispozici dlaždice.<br>
Klepněte například na **položku Azure Active Directory**. Pokud připojíte tento zdroj dat, můžete streamovat všechny protokoly z Azure AD do Azure Sentinelu. Můžete vybrat, jaký typ protokolů chcete získat – protokoly přihlášení a/nebo protokoly auditu. <br>
V dolní části Azure Sentinel poskytuje doporučení, pro které sešity byste měli nainstalovat pro každý konektor, takže můžete okamžitě získat zajímavé přehledy napříč daty. <br> Další informace naleznete v návodu k instalaci nebo [v příslušnépříručce](connect-data-sources.md) pro připojení. Informace o datových konektorech naleznete v [tématu Connect Microsoft Services](connect-data-sources.md).

Po připojení zdrojů dat se vaše data začnou streamovat do Azure Sentinelu a je připravená začít pracovat. Můžete zobrazit protokoly v [předdefinovaných řídicích panelech](quickstart-get-visibility.md) a začít vytvářet dotazy v Log Analytics [prozkoumat data](tutorial-investigate-cases.md).



## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se dozvěděli o připojení zdrojů dat k Azure Sentinelu. Další informace o Azure Sentinelu najdete v následujících článcích:
- Přečtěte [si, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začínáme [s detekcí hrozeb pomocí Azure Sentinelu](tutorial-detect-threats-built-in.md).
- Streamujte data ze [zařízení Common Event Format](connect-common-event-format.md) do Azure Sentinelu.
