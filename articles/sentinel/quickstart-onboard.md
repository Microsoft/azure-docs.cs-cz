---
title: 'Rychlý Start: zprovoznění v Azure Sentinel'
description: V tomto rychlém startu se dozvíte, jak nejdřív povolit Sentinel a pak připojit zdroje dat do zprovoznění Azure Sentinel.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: quickstart
ms.date: 10/14/2020
ms.custom: references_regions
ms.openlocfilehash: 21e0cfd56a37304d1ec333f8713894504282541f
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660657"
---
# <a name="quickstart-on-board-azure-sentinel"></a>Rychlý start: Zprovoznění služby Azure Sentinel

V tomto rychlém startu se dozvíte, jak připojit k Azure Sentinel. 

Aby bylo možné připojit se k Azure Sentinel, musíte nejdřív povolit službu Azure Sentinel a pak připojit zdroje dat. Služba Azure Sentinel je dodávána s řadou konektorů pro řešení Microsoftu, která je dostupná předem a poskytuje integraci v reálném čase, včetně řešení Microsoft 365 Defender (dříve Microsoft Threat Protection), Microsoft 365ch zdrojů (včetně Office 365), Azure AD, Microsoft Defenderu pro identitu (dřív ATP. Azure ATP), Microsoft Cloud App Security, výstrah v programu Azure Defender z Azure Security Center a další. Kromě toho jsou k dispozici Integrované konektory k širšímu ekosystému zabezpečení pro řešení jiných výrobců než Microsoftu. Pomocí protokolu CEF (Common Event Format), syslog nebo REST-API můžete také připojit zdroje dat k Azure Sentinel. 

Po připojení zdrojů dat si můžete vybrat z Galerie odborných vytvořených sešitů, které jsou na základě vašich dat Surface Insights. Tyto sešity je možné snadno přizpůsobit podle svých potřeb.

>[!IMPORTANT] 
> Informace o účtovaných cenách při používání Azure Sentinel najdete v tématu [ceny služby Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/).

## <a name="global-prerequisites"></a>Globální požadavky

- Aktivní předplatné Azure, pokud ho ještě nemáte, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

- Log Analytics pracovní prostor. Naučte se, jak [vytvořit pracovní prostor Log Analytics](../azure-monitor/learn/quick-create-workspace.md). Další informace o Log Analytics pracovních prostorech najdete v tématu [navrhování Azure Monitorch protokolů nasazení](../azure-monitor/platform/design-logs-deployment.md).

- Pokud chcete povolit Azure Sentinel, potřebujete oprávnění přispěvatele k předplatnému, ve kterém se nachází pracovní prostor Azure Sentinel. 
- Pokud chcete použít Azure Sentinel, potřebujete oprávnění Přispěvatel nebo čtenář ve skupině prostředků, do které pracovní prostor patří.
- K připojení konkrétních zdrojů dat se můžou potřebovat další oprávnění.
- Azure Sentinel je placená služba. Informace o cenách najdete v tématu informace [o službě Azure Sentinel](https://go.microsoft.com/fwlink/?linkid=2104058).

### <a name="geographical-availability-and-data-residency"></a>Zeměpisná dostupnost a zasídlí dat

- U všech [oblastí GA Log Analytics](https://azure.microsoft.com/global-infrastructure/services/?products=monitor) s výjimkou oblastí Čína a Německo (svrchovan) může Azure Sentinel běžet v pracovních prostorech. 

- Data generovaná službou Azure Sentinel, jako jsou incidenty, záložky a analytická pravidla, můžou obsahovat určitá zákaznická data, která jsou zdrojem zákaznických pracovních Log Analytics. Tato data generovaná službou Azure Sentinel se ukládají v geograficky uvedeném v následující tabulce, podle geografického umístění, ve kterém se nachází pracovní prostor:

    | Geografické pracovní prostory | Geografická data generovaná službou Azure Sentinel |
    | --- | --- |
    | USA<br>Indie<br>Brazílie<br>Afrika<br>Jižní Korea | USA |
    | Evropa<br>Francie<br>Švýcarsko | Evropa |
    | Austrálie | Austrálie |
    | Spojené království | Spojené království |
    | Kanada | Kanada |
    | Japonsko | Japonsko |
    |

## <a name="enable-azure-sentinel"></a>Povolit službu Azure Sentinel <a name="enable"></a>

1. Přihlaste se k webu Azure Portal. Ujistěte se, že je zaškrtnuté políčko předplatné, ve kterém je Azure Sentinel vytvořeno.

1. Vyhledejte a vyberte **Azure Sentinel**.

   ![Hledání služeb](./media/quickstart-onboard/search-product.png)

1. Vyberte možnost **Přidat**.

1. Vyberte pracovní prostor, který chcete použít, nebo vytvořte nový. Službu Azure Sentinel můžete spustit ve více než jednom pracovním prostoru, ale data jsou izolovaná do jednoho pracovního prostoru.

   ![Zvolit pracovní prostor](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - Výchozí pracovní prostory vytvořené nástrojem Azure Security Center se v seznamu nezobrazí. na ně nemůžete nainstalovat službu Azure Sentinel.
   >

   >[!IMPORTANT]
   >
   > - Po nasazení v pracovním prostoru Azure Sentinel v **současné době nepodporuje** přesun tohoto pracovního prostoru do jiných skupin prostředků nebo předplatných. 
   >
   >   Pokud jste pracovní prostor již přesunuli, zakažte v rámci **analýz** všechna aktivní pravidla a po pěti minutách je znovu povolte. Tato činnost by se měla projevit ve většině případů, ale při opakování iterace není podporovaná a je prováděná na vlastním riziku.

1. Vyberte **Přidat Sentinel Azure**.

## <a name="connect-data-sources"></a>Připojení zdrojů dat

Služba Azure Sentinel ingestuje data ze služeb a aplikací připojením ke službě a předáváním událostí a protokolů do služby Azure Sentinel. U fyzických a virtuálních počítačů můžete nainstalovat agenta Log Analytics, který shromáždí protokoly a předává je do Azure Sentinel. U bran firewall a proxy serverů Azure Sentinel nainstaluje agenta Log Analytics na server Linux syslog, ze kterého Agent shromáždí soubory protokolů a předá je službě Azure Sentinel. 
 
1. V hlavní nabídce vyberte **datové konektory**. Tím se otevře Galerie datových konektorů.

1. Galerie je seznam všech zdrojů dat, které se můžete připojit. Vyberte zdroj dat a pak tlačítko **otevřít stránku konektoru** .

1. Stránka konektor zobrazuje pokyny ke konfiguraci konektoru a případné další pokyny, které mohou být nezbytné.<br>
Pokud například vyberete zdroj dat **Azure Active Directory** , který umožňuje streamování protokolů z Azure AD do Azure Sentinel, můžete vybrat typ protokolů, které jsou v síti WAN, a protokoly pro přihlášení a protokoly auditu. <br> Postupujte podle pokynů k instalaci nebo se [podívejte na příslušné Průvodce připojením](connect-data-sources.md) , kde najdete další informace. Informace o datových konektorech najdete v tématu [připojení služeb společnosti Microsoft](connect-data-sources.md).

1. Karta **Další kroky** na stránce konektor zobrazuje relevantní předdefinované sešity, Ukázkové dotazy a šablony analytického pravidla, které doprovázejí datový konektor. Můžete je použít tak, jak jsou, nebo si je upravit – buď můžete okamžitě získat zajímavé přehledy napříč daty. <br>

Po připojení zdrojů dat začnou vaše data zasílat Stream do Azure Sentinel a je připravená, abyste mohli začít pracovat s. Protokoly můžete zobrazit v [vestavěných sešitech](quickstart-get-visibility.md) a začít sestavovat dotazy v Log Analytics a [Prozkoumat data](tutorial-investigate-cases.md).

## <a name="next-steps"></a>Další kroky
V tomto dokumentu jste se seznámili s připojováním a připojením zdrojů dat ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- Streamování dat z [běžných zařízení formátu událostí](connect-common-event-format.md) do Azure Sentinel.