---
title: Rychlý Start pro Azure Sentinel – Začínáme s Azure Sentinel Preview | Microsoft Docs
description: Rychlý Start pro Azure Sentinel – Začínáme s Sentinel Azure
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 5a4ae93c-d648-41fb-8fb8-96a025d2f73e
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/20/2019
ms.author: rkarlin
ms.openlocfilehash: c6434b4f5808c349d5e0ab04dafc5233f6ec63ac
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780472"
---
# <a name="quickstart-get-started-with-azure-sentinel-preview"></a>Rychlý start: Začínáme s Azure Sentinel Preview

> [!IMPORTANT]
> Služba Azure Sentinel je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


V tomto rychlém startu se dozvíte, jak rychle zobrazit a monitorovat, co se děje ve vašem prostředí pomocí Azure Sentinel. Po připojení zdrojů dat ke službě Azure Sentinel získáte okamžitou vizualizaci a analýzu dat, abyste mohli zjistit, co se děje napříč všemi připojenými zdroji dat. Pomocí služby Azure Sentinel získáte řídicí panely, které jsou už dostupné v Azure, a také tabulky a grafy, které jsou integrované pro poskytování analýz pro vaše protokoly a dotazy. Můžete buď použít předdefinované řídicí panely, nebo vytvořit nový řídicí panel snadno, od nuly nebo na základě existujícího řídicího panelu. 

## <a name="get-visualization"></a>Získat vizualizaci

Aby bylo možné vizualizovat a získat analýzu toho, co se děje ve vašem prostředí, nejprve se podívejte na řídicí panel přehled, abyste získali představu o stav zabezpečení vaší organizace. Můžete kliknout na jednotlivé prvky těchto dlaždic a přejít k podrobnostem o nezpracovaná data, ze kterých jsou vytvořena. K tomu, abyste snížili šum a minimalizovali počet výstrah, které je třeba zkontrolovat a prozkoumat, využívá Azure Sentinel způsob fúze ke korelaci upozornění na incidenty. **incidenty** jsou skupiny souvisejících výstrah, které společně vytvářejí incident s možnou činností, který můžete prozkoumat a vyřešit.

- V Azure Portal vyberte možnost Azure Sentinel a pak vyberte pracovní prostor, který chcete monitorovat.

  ![Přehled služby Azure Sentinel](./media/qs-get-visibility/overview.png)

- Na panelu nástrojů v horní části se dozvíte, kolik událostí jste ve vybraném časovém období, a porovná je s předchozími 24 hodinami. Panel nástrojů vás od těchto událostí upozorní, aktivované výstrahy (malé číslo představuje změnu za posledních 24 hodin) a pak vás upozorní na tyto události, počet otevřených, probíhajících a uzavřených. Zkontrolujte, že se nejedná o výrazné zvýšení ani pokles počtu událostí. Pokud dojde k přerušení, může to být tím, že připojení zastavilo hlášení do Azure Sentinel. Pokud dojde ke zvýšení, může dojít k nějaké podezřelé situaci. Podívejte se, jestli máte nové výstrahy.

   ![Filtr Sentinel Azure](./media/qs-get-visibility/funnel.png)

Hlavní část stránky s přehledem poskytuje rychlý přehled o stavu zabezpečení vašeho pracovního prostoru:

- **Události a výstrahy v průběhu času**: Vypíše počet událostí a kolik výstrah bylo z těchto událostí vytvořeno. Pokud se zobrazí špička, která je neobvyklá, měla by se vám zobrazit upozornění – pokud se vyskytnou špička v událostech, ale výstrahy nevidíte, může to způsobovat obavy.

- **Potenciální škodlivé události**: Když se zjistí provoz ze zdrojů, u kterých se ví, že jsou škodlivé, výstrahy Azure Sentinel na mapě. Pokud vidíte oranžová, jedná se o příchozí provoz: někdo se snaží o přístup k vaší organizaci ze známé škodlivé IP adresy. Pokud se zobrazí odchozí (červená) aktivita, znamená to, že data z vaší sítě se streamují z vaší organizace na známou škodlivou IP adresu.

   ![Mapování Sentinel Azure](./media/qs-get-visibility/map.png)


- **Nedávné incidenty**: Pokud si chcete zobrazit poslední incidenty, jejich závažnost a počet výstrah přidružených k incidentu. Pokud se u určitého typu výstrahy zobrazí jako náhlé špičky, může to znamenat, že aktuálně probíhá aktivní útok. Pokud máte například náhlou špičku 20 událostí pass-the-hash z Azure ATP, je možné, že se někdo aktuálně snaží o útok.

- **Anomálie zdroje dat**: Analytiky dat Microsoftu vytvořily modely, které neustále vyhledávají data ze zdrojů dat, a to kvůli anomáliím. Pokud neexistují žádné anomálie, nic se nezobrazí. Pokud se zjistí anomálie, měli byste je podrobněovat, abyste zjistili, co se stalo. Například klikněte na špička v aktivitě Azure. Kliknutím na **graf** můžete zobrazit, kdy špička proběhla, a potom filtrovat aktivity, ke kterým došlo během tohoto časového období, abyste viděli, co způsobilo špičku.

   ![Mapování Sentinel Azure](./media/qs-get-visibility/anomolies.png)

## Použití vestavěných řídicích panelů<a name="dashboards"></a>

Vestavěné řídicí panely poskytují integrovaná data z vašich připojených zdrojů dat a umožňují tak důkladné podrobně události vygenerované v těchto službách. Vestavěné řídicí panely zahrnují Azure ID, události aktivit Azure a místní, což může být data z událostí systému Windows ze serverů, výstrahy od první strany, od jakékoli třetí strany, včetně protokolů přenosů firewallu, Office 365 a nezabezpečených protokolů založených na systému Windows. událost.

1. V části **Nastavení**vyberte **řídicí panely**. Včásti nainstalováno uvidíte všechny nainstalované řídicí panely. V části **vše** můžete zobrazit celou galerii vestavěných řídicích panelů, které jsou k dispozici pro instalaci. 
2. Pokud chcete zobrazit celý seznam a popis jednotlivých nabídek, vyhledejte konkrétní řídicí panel. 
3. Za předpokladu, že používáte Azure AD, můžete začít pracovat se službou Azure Sentinel. doporučujeme nainstalovat alespoň následující řídicí panely:
   - **Azure AD**: Použijte buď jednu z těchto možností, nebo obě z těchto možností:
       - **Přihlášení Azure AD** analyzuje přihlášení v průběhu času, aby bylo možné zjistit, jestli existují anomálie. Tento řídicí panel poskytuje aplikacím, zařízením a umístěním neúspěšné přihlášení, takže můžete na první pohled všimnout, pokud se nestane něco neobvyklého. Věnujte pozornost několika neúspěšným přihlášením. 
       - **Protokoly auditu Azure AD** analyzují aktivity správců, například změny uživatelů (přidávání, odebírání atd.), vytváření skupin a změny.  

   - Přidejte řídicí panel pro bránu firewall. Přidejte například řídicí panel Palo Alto. Řídicí panel analyzuje provoz brány firewall a poskytuje korelace mezi daty brány firewall a událostmi hrozeb a zvýrazňuje podezřelé události napříč entitami. Řídicí panely poskytují informace o trendech v provozu a umožňují přechod k podrobnostem a filtrování výsledků. 

      ![Řídicí panel PAL Alto](./media/qs-get-visibility/palo-alto-week-query.png)


Řídicí panely můžete přizpůsobit úpravou tlačítka ![](./media/qs-get-visibility/edit-query-button.png)hlavní dotaz. Kliknutím na ![tlačítko tlačítka](./media/qs-get-visibility/go-to-la-button.png) můžete přejít na Log Analytics a [Upravit dotaz tam](../azure-monitor/log-query/get-started-portal.md). můžete také vybrat tři tečky (...) a vybrat **přizpůsobit data dlaždice**, což vám umožní upravit hlavní filtr času nebo odebrat konkrétní dlaždice z řídicího panelu.

Další informace o práci s dotazy najdete v tématu [kurz: Vizuální data v Log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md)

### <a name="add-a-new-tile"></a>Přidat novou dlaždici

Pokud chcete přidat novou dlaždici, můžete ji přidat na existující řídicí panel, který vytvoříte, nebo na integrovaný řídicí panel Azure Sentinel. 
1. V Log Analytics vytvořte dlaždici pomocí pokynů uvedených v [kurzu: Vizuální data v Log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md). 
2. Po vytvoření dlaždice vyberte v části **připnout**řídicí panel, ve kterém se má dlaždice zobrazit.

## <a name="create-new-dashboards"></a>Vytvoření nových řídicích panelů
Nový řídicí panel můžete vytvořit úplně od začátku nebo použít vestavěný řídicí panel jako základ pro váš nový řídicí panel.

1. Pokud chcete vytvořit nový řídicí panel od začátku, vyberte **řídicí panely** a potom **+ nový řídicí panel**.
2. Vyberte předplatné, ve kterém se řídicí panel vytvoří, a sdělte mu popisný název. Každý řídicí panel je prostředek Azure, podobně jako kterýkoli jiný, a k definování a omezení přístupu k nim můžete přiřadit role (RBAC). 
3. Pokud ho chcete povolit, aby se zobrazoval ve vašich řídicích panelech, abyste mohli vizualizace připnout na, budete ho muset sdílet. Klikněte na **sdílet** a pak na **Spravovat uživatele**. 
 
1. Použijte přiřazení **přístupových oprávnění** a **rolí** stejně jako u všech ostatních prostředků Azure. Další informace najdete v tématu [sdílení řídicích panelů Azure pomocí RBAC](../azure-portal/azure-portal-dashboard-share-access.md).


## <a name="new-dashboard-examples"></a>Nové příklady řídicích panelů

Následující vzorový dotaz vám umožní porovnat trendy provozu v různých týdnech. Můžete snadno přepnout daného dodavatele zařízení a zdroje dat, na kterém jste dotaz spustili. V tomto příkladu se používá SecurityEvent ze systému Windows, takže ho můžete přepnout na AzureActivity nebo CommonSecurityLog na kterékoli jiné brány firewall.

     |where DeviceVendor = = "Palo Alto Networks":
      // week over week query
      SecurityEvent
      | where TimeGenerated > ago(14d)
      | summarize count() by bin(TimeGenerated, 1d)
      | extend Week = iff(TimeGenerated>ago(7d), "This Week", "Last Week"), TimeGenerated = iff(TimeGenerated>ago(7d), TimeGenerated, TimeGenerated + 7d)


Možná budete chtít vytvořit dotaz, který zahrnuje data z více zdrojů. Můžete vytvořit dotaz, který bude Azure Active Directory protokoly auditu pro nové uživatele, které jste právě vytvořili, a pak zkontrolovat protokoly Azure, abyste viděli, jestli uživatel začal provádět změny přiřazení role během 24 hodin od vytvoření. Tato podezřelá aktivita by se zobrazila na tomto řídicím panelu:

    AuditLogs
    | where OperationName == "Add user"
    | project AddedTime = TimeGenerated, user = tostring(TargetResources[0].userPrincipalName)
    | join (AzureActivity
    | where OperationName == "Create role assignment"
    | project OperationName, RoleAssignmentTime = TimeGenerated, user = Caller) on user
    | project-away user1

Můžete vytvořit různé řídicí panely na základě role osoby, která si vyhledává data a co hledají. Můžete například vytvořit řídicí panel pro správce sítě, který obsahuje data brány firewall. Můžete také vytvořit řídicí panely na základě toho, jak často je chcete zobrazit, zda existují věci, které chcete kontrolovat denně, a další položky, které chcete kontrolovat jednou za hodinu, například můžete chtít v každé hodiny podívat se na vaše přihlášení ke službě Azure AD a vyhledat anomálii. jednomu. 

## <a name="create-new-detections"></a>Vytvořit nové detekce

Vygenerujte zjišťování [zdrojů dat, ke kterým jste se připojili ke službě Azure Sentinel,](connect-data-sources.md) a prozkoumejte hrozby ve vaší organizaci.

Když vytváříte nové zjišťování, využijte integrované detekce vytvořené odborníky na zabezpečení Microsoftu, které jsou přizpůsobené zdrojům dat, které jste připojili.

1. [Ve komunitě GitHubu](https://github.com/Azure/Azure-Sentinel/tree/master/Detections) přejít do složky **detekce** a vyberte příslušné složky.
   ![relevantní složky](./media/qs-get-visibility/detection-folders.png)
 
3.  Přejít na kartu **Analytics** a vyberte **Přidat**.
   ![vytvořit pravidlo v Log Analytics](./media/qs-get-visibility/query-params.png)

3.  Zkopírujte všechny parametry do pravidla a klikněte na **vytvořit**.
   ![vytvořit pravidlo výstrahy](./media/qs-get-visibility/create-alert-rule.png)

 
## <a name="next-steps"></a>Další postup
V tomto rychlém startu jste zjistili, jak začít používat Azure Sentinel. Pokračujte v tomto kurzu, [](tutorial-detect-threats.md)kde zjistíte hrozby.
> [!div class="nextstepaction"]
> [Detekuje hrozby](tutorial-detect-threats.md) pro automatizaci reakcí na hrozby.

