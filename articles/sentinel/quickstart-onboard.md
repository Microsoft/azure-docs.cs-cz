---
title: Začlenění ve verzi Preview Azure Sentinel | Dokumentace Microsoftu
description: Zjistěte, jak shromažďovat data v Azure Sentinelu.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/27/2019
ms.author: rkarlin
ms.openlocfilehash: 891f9fbd26b53b392ac84ed9d420b58558cd20c2
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258423"
---
# <a name="on-board-azure-sentinel-preview"></a>Ve verzi Preview připojit Azure Sentinel

> [!IMPORTANT]
> Azure Sentinel je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

V tomto rychlém startu se dozvíte, jak začlenit Sentinelu Azure. 

Do vlaku Sentinelu Azure musíte nejprve povolit Sentinelu Azure a připojte vaše zdroje. Azure Sentinel se dodává s celou řadou konektorů pro Microsoft solutions, k dispozici z pole a poskytuje v reálném čase, integrace, včetně řešení ochrany před internetovými útoky Microsoft, Microsoft 365 zdrojů, včetně Office 365, Azure AD, Azure ATP a Microsoft Cloud App Security a další. Kromě toho jsou integrované konektory do širšího ekosystému zabezpečení pro řešení jiného subjektu než Microsoft. Můžete také použít běžný formát události Syslog nebo rozhraní REST API pro připojení zdroje dat pomocí Azure Sentinelu.  

Po připojení zdroje dat, vyberte si z Galerie odborně řídicí panely, které přehledy na základě vašich dat. Tyto řídicí panely můžete snadno přizpůsobit svým potřebám.


## <a name="global-prerequisites"></a>Globální požadavky

- Aktivní předplatné Azure, pokud ho nemáte, vytvořte [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) předtím, než začnete.

- Pracovní prostor log Analytics. Zjistěte, jak [vytvořit pracovní prostor Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md)

-  Povolit Sentinelu Azure, potřebujete oprávnění přispěvatele pro předplatné, ve kterém se nachází pracovní prostor Sentinelu Azure. 
- Použití ověřovacích Azure, potřebujete oprávnění Přispěvatel nebo Čtenář, který pracovní prostor patří do skupiny prostředků
- Může být potřeba další oprávnění pro připojení konkrétní zdroje dat
 
## Povolit Azure Sentinel <a name="enable"></a>

1. Přejděte na web Azure Portal.
2. Ujistěte se, že je vybrané předplatné, ve kterém se vytvoří Sentinelu Azure. 
3. Hledání Azure Sentinel. 
   ![search](./media/quickstart-onboard/search-product.png)

1. Klikněte na tlačítko **+ přidat**.
1. Vyberte pracovní prostor, který chcete použít nebo vytvořit nový. Můžete spustit ověřovací Azure na více než jednomu pracovnímu prostoru, ale data budou izolovaná do jednoho pracovního prostoru.

   ![search](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - **Umístění pracovního prostoru** je důležité pochopit, že všechna data můžete Streamovat do ověřovacích Azure je uložen v zeměpisné oblasti pracovního prostoru, který jste vybrali.  
   > - Výchozí pracovních prostorů vytvořených službou Azure Security Center se nezobrazí v seznamu. Azure Sentinelu nelze nainstalovat na ně.
   > - Azure Sentinel můžete spustit na pracovní prostory, které jsou nasazené v některém z následujících oblastí:  Austrálie – jihovýchod, Kanada – střed, střed Indie, východní USA, Východ USA 2 EUAP (testovací), Japonsko – východ, Asie – jihovýchod, Velká Británie – Jih, západní Evropa, západní USA 2.

6. Klikněte na tlačítko **přidat Azure Sentinel**.
  

## <a name="connect-data-sources"></a>Připojení zdrojů dat

Azure Sentinel vytvoří připojení ke službám a aplikace připojuje ke službě a jejich předávání událostí a protokolů Sentinelu Azure. Pro počítače a virtuální počítače můžete nainstalovat agenta, který shromažďuje protokoly a předává je na Azure Sentinelu Sentinelu Azure. Brány firewall a proxy servery využívá Azure Sentinelu serveru protokolu Syslog v Linuxu. Je na něm nainstalován agent a ze které agent shromažďuje protokolu, souborů a předává je na Azure Sentinelu. 
 
1. Klikněte na tlačítko **shromažďování dat**.
2. Je dlaždice pro každý zdroj dat, které se můžete připojit.<br>
Klikněte například na **Azure Active Directory**. Pokud propojíte tento zdroj dat, streamování všechny protokoly z Azure AD do Azure Sentinelu. Můžete vybrat typ protokolů, které pokud chcete získat – protokoly přihlášení a/nebo protokoly auditu. <br>
V dolní části Sentinelu Azure poskytuje doporučení, pro které řídicí panely byste měli nainstalovat pro každý konektor je můžete okamžitě získat zajímavé přehledy napříč vašimi daty. <br> Postupujte podle pokynů k instalaci nebo [naleznete v Průvodci příslušné připojení](connect-data-sources.md) Další informace. Informace o datových konektorů najdete v tématu [připojení služby](connect-data-sources.md).

Za data, ke které jsou připojené zdroje vaše data spustí Streamovat do ověřovacích Azure a je připraven k zahájení práce s. Můžete zobrazit v protokolech [integrované řídicí panely](quickstart-get-visibility.md) a začněte vytvářet dotazy v Log Analytics pro [prozkoumat data](tutorial-investigate-cases.md).



## <a name="next-steps"></a>Další postup
V tomto dokumentu jste se dozvěděli o připojení zdroje dat k Sentinelu Azure. Další informace o Azure Sentinelu, naleznete v následujících článcích:
- Zjistěte, jak [umožňuje získat přehled vaše data a potenciální hrozby](quickstart-get-visibility.md).
- Začínáme [detekuje hrozby s využitím Azure Sentinelu](tutorial-detect-threats.md).
- Stream data z [běžný formát chyba zařízení](connect-common-event-format.md) do ověřovacích Azure.
