---
title: 'Rychlý start: Začínáme se službou Azure Sentinel'
description: V tomto rychlém startu se dozvíte, jak rychle zobrazit a monitorovat, co se děje ve vašem prostředí pomocí funkce Azure Sentinel. 
services: sentinel
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: quickstart
ms.custom: mvc, fasttrack-edit
ms.date: 09/16/2020
ms.author: yelevin
ms.openlocfilehash: fe2a632901b32993d46e723ab73fce045bfa9f84
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "90889129"
---
# <a name="quickstart-get-started-with-azure-sentinel"></a>Rychlý start: Začínáme se službou Azure Sentinel




V tomto rychlém startu se dozvíte, jak rychle zobrazit a monitorovat, co se děje ve vašem prostředí pomocí funkce Azure Sentinel. Po připojení zdrojů dat ke službě Azure Sentinel získáte okamžitou vizualizaci a analýzu dat, abyste mohli zjistit, co se děje napříč všemi připojenými zdroji dat. Azure Sentinel vám poskytuje sešity, které vám poskytnou kompletní možnosti nástrojů, které jsou už dostupné v Azure, a také tabulky a grafy, které jsou integrované pro poskytování analýz pro vaše protokoly a dotazy. Můžete buď použít předdefinované sešity nebo vytvořit nový sešit snadno, od nuly nebo na základě existujícího sešitu. 

## <a name="get-visualization"></a>Získat vizualizaci

Aby bylo možné vizualizovat a získat analýzu toho, co se děje ve vašem prostředí, nejprve se podívejte na řídicí panel přehled, abyste získali představu o stav zabezpečení vaší organizace. Můžete kliknout na jednotlivé prvky těchto dlaždic a přejít k podrobnostem o nezpracovaná data, ze kterých jsou vytvořena. K tomu, abyste snížili šum a minimalizovali počet výstrah, které je třeba zkontrolovat a prozkoumat, využívá Azure Sentinel způsob fúze ke korelaci upozornění na incidenty. **incidenty** jsou skupiny souvisejících výstrah, které společně vytvářejí incident s možnou činností, který můžete prozkoumat a vyřešit.

- V Azure Portal vyberte možnost Azure Sentinel a pak vyberte pracovní prostor, který chcete monitorovat.

  ![Přehled služby Azure Sentinel](./media/qs-get-visibility/overview.png)

- Na panelu nástrojů v horní části se dozvíte, kolik událostí jste ve vybraném časovém období, a porovná je s předchozími 24 hodinami. Panel nástrojů vás od těchto událostí upozorní, aktivované výstrahy (malé číslo představuje změnu za posledních 24 hodin) a pak vás upozorní na tyto události, počet otevřených, probíhajících a uzavřených. Zkontrolujte, že se nejedná o výrazné zvýšení ani pokles počtu událostí. Pokud dojde k přerušení, může to být tím, že připojení zastavilo hlášení do Azure Sentinel. Pokud dojde ke zvýšení, může dojít k nějaké podezřelé situaci. Podívejte se, jestli máte nové výstrahy.

   ![Čítače Azure Sentinel](./media/qs-get-visibility/funnel.png)

Hlavní část stránky s přehledem poskytuje rychlý přehled o stavu zabezpečení vašeho pracovního prostoru:

- **Události a výstrahy v průběhu času**: vypíše počet událostí a kolik výstrah bylo z těchto událostí vytvořeno. Pokud se zobrazí špička, která je neobvyklá, měla by se vám zobrazit upozornění – pokud se vyskytnou špička v událostech, ale výstrahy nevidíte, může to způsobovat obavy.

- **Potenciální škodlivé události**: když se zjistí provoz ze zdrojů, u kterých se ví, že jsou škodlivé, Azure Sentinel vás na mapě upozorní. Pokud vidíte oranžová, jedná se o příchozí provoz: někdo se snaží o přístup k vaší organizaci ze známé škodlivé IP adresy. Pokud se zobrazí odchozí (červená) aktivita, znamená to, že data z vaší sítě se streamují z vaší organizace na známou škodlivou IP adresu.

   ![Mapa škodlivého provozu](./media/qs-get-visibility/map.png)

- **Nedávné incidenty**: Pokud si chcete zobrazit poslední incidenty, jejich závažnost a počet výstrah přidružených k incidentu. Pokud se u určitého typu výstrahy zobrazí jako náhlé špičky, může to znamenat, že aktuálně probíhá aktivní útok. Například pokud máte náhlé špičky 20 událostí pass-the-hash od Microsoft Defenderu po identitu (dřív ATP. Azure ATP), je možné, že se někdo momentálně snaží o útok.

- **Anomálie zdrojů dat**: analytiky dat Microsoftu vytvořily modely, které neustále vyhledávají data ze zdrojů dat, a to kvůli anomáliím. Pokud neexistují žádné anomálie, nic se nezobrazí. Pokud se zjistí anomálie, měli byste je podrobněovat, abyste zjistili, co se stalo. Například klikněte na špička v aktivitě Azure. Kliknutím na **graf** můžete zobrazit, kdy špička proběhla, a potom filtrovat aktivity, ke kterým došlo během tohoto časového období, abyste viděli, co způsobilo špičku.

   ![Zdroje dat neobvyklé](./media/qs-get-visibility/anomolies.png)

## <a name="use-built-in-workbooks"></a>Použití vestavěných sešitů<a name="dashboards"></a>

Předdefinované sešity poskytují integrovaná data z připojených zdrojů dat a umožňují vám tak podrobně události vygenerované v těchto službách. Předdefinované sešity zahrnují Azure AD, události aktivit Azure a místní, což může být data z událostí systému Windows ze serverů, výstrahy od první strany, od jakékoli třetí strany, včetně protokolů přenosů z brány firewall, sady Office 365 a nezabezpečené protokoly založené na událostech systému Windows. Sešity jsou založené na Azure Monitor sešity, které vám poskytnou rozšířenou možnost úprav a flexibility při navrhování vlastního sešitu. Další informace najdete v tématu [sešity](../azure-monitor/platform/workbooks-overview.md).

1. V části **Nastavení**vyberte **sešity**. V části **nainstalováno**uvidíte všechny nainstalované sešity. V části **vše**můžete zobrazit celou galerii vestavěných sešitů, které jsou k dispozici pro instalaci. 
2. Vyhledáním konkrétního sešitu zobrazíte celý seznam a popis toho, co jednotlivé nabídky nabízí. 
3. Za předpokladu, že používáte Azure AD, můžete začít pracovat se službou Azure Sentinel, doporučujeme nainstalovat alespoň následující sešity:
   - **Azure AD**: použijte jednu nebo obě z následujících možností:
       - **Přihlášení Azure AD** analyzuje přihlášení v průběhu času, aby bylo možné zjistit, jestli existují anomálie. Tyto sešity poskytují neúspěšné přihlášení aplikací, zařízení a umístění, takže můžete na první pohled všimnout, že se něco nestane. Věnujte pozornost několika neúspěšným přihlášením. 
       - **Protokoly auditu Azure AD** analyzují aktivity správců, například změny uživatelů (přidávání, odebírání atd.), vytváření skupin a změny.  

   - Přidejte sešit pro bránu firewall. Přidejte například sešit Palo Alto. Sešit analyzuje provoz brány firewall a poskytuje korelace mezi daty brány firewall a událostmi hrozeb a zvýrazňuje podezřelé události napříč entitami. V sešitech získáte informace o trendech v provozu a umožňují přejít k podrobnostem a filtrovat výsledky. 

      ![Řídicí panel Palo Alto](./media/qs-get-visibility/palo-alto-week-query.png)


Sešity můžete přizpůsobit úpravou ![ tlačítka pro úpravy hlavního dotazu dotazu ](./media/qs-get-visibility/edit-query-button.png) . Kliknutím na tlačítko Log Analytics tlačítko ![ ](./media/qs-get-visibility/go-to-la-button.png) přejdete na Log Analytics a [upravíte dotaz tam](../azure-monitor/log-query/get-started-portal.md)a můžete vybrat tři tečky (...) a vybrat **přizpůsobit data dlaždice**, což umožňuje upravit hlavní filtr času nebo odebrat konkrétní dlaždice ze sešitu.

Další informace o práci s dotazy najdete v tématu [kurz: vizuální data v Log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md)

### <a name="add-a-new-tile"></a>Přidat novou dlaždici

Pokud chcete přidat novou dlaždici, můžete ji přidat do existujícího sešitu, který vytvoříte, nebo do předdefinovaného sešitu ověřovacího protokolu Azure. 
1. V Log Analytics vytvořte dlaždici pomocí pokynů v tématu [kurz: vizuální data v Log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md). 
2. Po vytvoření dlaždice vyberte v části **připnout**sešit, ve kterém se má dlaždice zobrazit.

## <a name="create-new-workbooks"></a>Vytváření nových sešitů
Můžete vytvořit nový sešit úplně od začátku nebo použít vestavěný sešit jako základ pro nový sešit.

1. Chcete-li vytvořit nový sešit od začátku, vyberte **sešity** a potom **+ nový sešit**.
2. Vyberte předplatné, ve kterém se sešit vytvoří, a sdělte mu popisný název. Každý sešit je prostředek Azure, podobně jako jakýkoli jiný, a k definování a omezení přístupu k nim můžete přiřadit role pro IT (RBAC). 
3. Pokud ho chcete povolit zobrazení v sešitech a připnout vizualizace na, budete ho muset sdílet. Klikněte na **sdílet** a pak na **Spravovat uživatele**. 
 
1. Použijte přiřazení **přístupových oprávnění** a **rolí** stejně jako u všech ostatních prostředků Azure. Další informace najdete v tématu [sdílení sešitů Azure pomocí RBAC](../azure-portal/azure-portal-dashboard-share-access.md).


## <a name="new-workbook-examples"></a>Příklady nových sešitů

Následující vzorový dotaz vám umožní porovnat trendy provozu v různých týdnech. Můžete snadno přepnout daného dodavatele zařízení a zdroje dat, na kterém jste dotaz spustili. V tomto příkladu se používá SecurityEvent ze systému Windows, takže ho můžete přepnout na AzureActivity nebo CommonSecurityLog na kterékoli jiné brány firewall.

```console
 |where DeviceVendor == "Palo Alto Networks":
  // week over week query
  SecurityEvent
  | where TimeGenerated > ago(14d)
  | summarize count() by bin(TimeGenerated, 1d)
  | extend Week = iff(TimeGenerated>ago(7d), "This Week", "Last Week"), TimeGenerated = iff(TimeGenerated>ago(7d), TimeGenerated, TimeGenerated + 7d)
```

Možná budete chtít vytvořit dotaz, který zahrnuje data z více zdrojů. Můžete vytvořit dotaz, který bude Azure Active Directory protokoly auditu pro nové uživatele, které jste právě vytvořili, a pak zkontrolovat protokoly Azure, abyste viděli, jestli uživatel začal provádět změny přiřazení role během 24 hodin od vytvoření. Tato podezřelá aktivita by se zobrazila na tomto řídicím panelu:

```console
AuditLogs
| where OperationName == "Add user"
| project AddedTime = TimeGenerated, user = tostring(TargetResources[0].userPrincipalName)
| join (AzureActivity
| where OperationName == "Create role assignment"
| project OperationName, RoleAssignmentTime = TimeGenerated, user = Caller) on user
| project-away user1
```

Můžete vytvářet různé sešity na základě role osoby, která si hledá data a co hledají. Můžete například vytvořit sešit pro správce sítě, který obsahuje data brány firewall. Můžete také vytvořit sešity na základě toho, jak často je chcete zobrazit, zda existují věci, které chcete kontrolovat denně, a další položky, které chcete kontrolovat jednou za hodinu, například můžete chtít v každé hodiny podívat se na přihlášení ke službě Azure AD a vyhledat anomálie. 

## <a name="create-new-detections"></a>Vytvořit nové detekce

Vygenerujte zjišťování [zdrojů dat, ke kterým jste se připojili ke službě Azure Sentinel,](connect-data-sources.md) a prozkoumejte hrozby ve vaší organizaci.

Když vytváříte nové zjišťování, využijte integrované detekce vytvořené odborníky na zabezpečení Microsoftu, které jsou přizpůsobené zdrojům dat, které jste připojili.

Pokud chcete zobrazit všechna připravená zjišťování, pokračujte na **analýzy** a pak na **šablony pravidel**. Tato karta obsahuje všechna předdefinovaná pravidla Azure Sentinel.

   ![Použití vestavěných detekcí k nalezení hrozeb pomocí Sentinel Azure](media/tutorial-detect-built-in/view-oob-detections.png)

Další informace o tom, jak získat připravená zjišťování, najdete v tématu [kurz: získání integrovaných analýz](tutorial-detect-threats-built-in.md).
 
## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste zjistili, jak začít používat Azure Sentinel. Pokračujte v tomto kurzu, kde [zjistíte hrozby](tutorial-detect-threats-built-in.md).
> [!div class="nextstepaction"]
> [Vytvářejte vlastní pravidla detekce hrozeb](tutorial-detect-threats-custom.md) pro automatizaci reakcí na hrozby.

