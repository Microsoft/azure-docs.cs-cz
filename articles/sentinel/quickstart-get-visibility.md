---
title: Sentinel rychlý start Azure – Začínáme s náhledem Sentinelu Azure | Dokumentace Microsoftu
description: Sentinel rychlý start Azure – Začínáme s Azure Sentinelu
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 5a4ae93c-d648-41fb-8fb8-96a025d2f73e
ms.service: sentinel
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/20/2019
ms.author: rkarlin
ms.openlocfilehash: 07839c6372e0048048b62fbfedd58bfa9b9942f5
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65207519"
---
# <a name="quickstart-get-started-with-azure-sentinel-preview"></a>Rychlý start: Začínáme s Azure Sentinelu ve verzi Preview

> [!IMPORTANT]
> Azure Sentinel je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


V tomto rychlém startu se dozvíte, jak rychle bude moct zobrazovat a monitorovat, co se děje v prostředí pomocí ověřovacích Azure. Po připojení zdroje dat do Azure Sentinelu, získáte okamžitý vizualizace a analýzy dat, abyste věděli, co se děje pro všechny připojené datové zdroje. Azure Sentinel poskytuje řídicí panely, které vám poskytnou výkon nástroje, které jsou aktuálně k dispozici v Azure a jak tabulky a grafy, které jsou součástí poskytují analýzy protokolů a dotazy. Můžete použít integrované řídicí panely nebo vytvořit nový řídicí panel, od začátku nebo na základě na existující řídicí panel. 

## <a name="get-visualization"></a>Získat vizualizace

Vizualizace a získat analýzy, co se děje ve vašem prostředí, nejprve, podívejte se na přehled řídicího panelu, kde získáte představu o stavu zabezpečení vaší organizace. Můžete kliknout na každý prvek z těchto dlaždic pro procházení nezpracovaná data, ze které se vytvářejí. Můžete omezit informační šum a minimalizovat počet výstrah, které budete muset zkontrolovat a zjistit, Sentinelu Azure používá techniku fusion ke korelaci výstrahy do případů. **Případy** jsou skupiny souvisejících výstrah, které společně vytvářejí užitečné incident, který můžete prozkoumat a vyřešit.

- Na webu Azure Portal vyberte Sentinelu Azure a pak vyberte pracovní prostor, který chcete monitorovat.

  ![Přehled služby Azure Sentinel](./media/qs-get-visibility/overview.png)

- Panelu nástrojů v horní části se dozvíte, jak mnoho událostí, které jste získali za vybrané časové období a jejím srovnání s předchozím 24 hodinám. Panelu nástrojů sděluje, z těchto událostí, výstrah, které se aktivuje (malé číslo představuje změny za posledních 24 hodin), a pak vám sdělí pro události, kolik byly otevřené, probíhající a uzavřený. Zaškrtněte, pokud chcete zobrazit, že není výrazné zvýšení nebo pokles počtu událostí. Pokud existuje pokles, je možné, že připojení přestaly se generovat sestavy na Sentinelu Azure. Pokud dojde navýšení, něco podezřelého mohlo dojít. Zkontrolujte, jestli máte nové výstrahy.

   ![Azure Trychtýř Sentinel](./media/qs-get-visibility/funnel.png)

Hlavní stránka s přehledem poskytuje přehled o na první pohled na stav zabezpečení vašeho pracovního prostoru:

- **Události a upozornění v průběhu času**: Obsahuje počet událostí a počet výstrah vytvořených z těchto událostí. Pokud se zobrazí prudký nárůst, který neobvyklá, že pokud existuje něco neobvyklého kdy nedojde k prudkému události, ale nevidíte oznámení, měli byste vidět výstrahy pro něj –, může být příčinou znepokojení.

- **Potenciální škodlivých událostech**: Při zjištění provozu ze zdroje, které jsou známé jako škodlivý, Sentinelu Azure vás upozorní na mapě. Pokud se zobrazí oranžově, je příchozí provoz: někdo pokouší o přístup k vaší organizaci ze známé škodlivé IP adresy. Pokud se aktivita odchozí (červená), znamená to, streamování dat z vaší sítě mimo vaši organizaci známé škodlivé IP adresy.

   ![Azure mapa Sentinel](./media/qs-get-visibility/map.png)


- **Nedávné případy**: Chcete-li zobrazit nedávné případy, závažnosti a počet výstrah, které jsou přidružené k tento případ. Pokud se zobrazí jako i s náhlými špičku v konkrétním typu výstrahy, může to znamenat, že je aktivní útoku aktuálně běží. Například pokud máte i s náhlými ve špičce 20 událostí Pass-the-hash z ochrany ATP v programu Azure je možné, že se někdo aktuálně snaží útoku je.

- **Zdroje dat anomálie**: Microsoftu pro datové analytiky vytvořené modely, které neustále hledat data ze zdrojů dat pro anomálie. Pokud nejsou k dispozici anomálie, nezobrazí se. Anomálie se zjišťují, měli byste podrobné informace o nich chcete zobrazit, co se stalo. Klikněte například na nárůst aktivit Azure. Můžete kliknout na **grafu** uvidí, když se stalo zásobníku a vyfiltrujte pro aktivity, ke kterým došlo během tohoto časového období zobrazíte, co způsobilo zásobníku.

   ![Azure mapa Sentinel](./media/qs-get-visibility/anomolies.png)

## Pomocí předdefinovaných řídicích panelů<a name="dashboards"></a>

Integrované řídicí panely poskytují integrované data z vašich připojených zdrojů dat umožňuje hloubkové podrobně události generované v těchto služeb. Integrované řídicí panely zahrnují Azure ID, události aktivit v Azure a v místním prostředí, které mohou být data z události Windows ze serverů, z první strany výstrah, z jakékoli třetí strany, včetně protokolů přenosů brány firewall, Office 365 a nezabezpečené protokoly založené na Windows události.

1. V části **nastavení**vyberte **řídicí panely**. V části **nainstalováno**, zobrazí se všechny řídicí panely nainstalované. V části **všechny** můžete zobrazit celou galerii integrované řídicí panely, které jsou k dispozici pro instalaci. 
2. Hledat na konkrétním řídicím panelu zobrazíte celý seznam a popis co každá nabízí. 
3. Za předpokladu, že jste pomocí Azure AD, zprovoznit a s Azure Sentinelu, doporučujeme vám nainstalovat minimálně následující řídicí panely:
   - **Azure AD**: Použijte jednu nebo obě z následujících akcí:
       - **Azure AD přihlášení** analyzuje přihlášení v čase, abyste viděli, jestli jsou anomálie. Tento řídicí panel poskytuje neúspěšné přihlášení pomocí aplikací, zařízení a umístění, takže můžete zjistíte, na první pohled je-li se stane něco neobvyklého. Věnujte pozornost několik neúspěšných přihlášení. 
       - **Protokoly auditu Azure AD** analyzuje aktivity správy, jako jsou například změny uživatelů (přidání, odebrání, atd.), vytvoření skupiny a úpravy.  

   - Přidání řídicího panelu ke své bráně firewall. Například přidáte řídicí panel Palo Alto. Řídicí panel analyzuje provoz brány firewall, vám poskytnou korelace mezi událostmi hrozeb a data vaší brány firewall a zvýrazní podezřelé události napříč entitami. Řídicí panely vám poskytne informace o trendech v provozu a umožňuje procházet a filtrovat výsledky. 

      ![PAL Alto řídicí panel](./media/qs-get-visibility/palo-alto-week-query.png)


Můžete přizpůsobit řídicí panely, buď tak, že upravíte hlavního dotazu ![tlačítko](./media/qs-get-visibility/edit-query-button.png). Kliknete na tlačítko ![tlačítko](./media/qs-get-visibility/go-to-la-button.png) přejdete na [Log Analytics k úpravě dotazu existuje](../azure-monitor/log-query/get-started-portal.md), a můžete vybrat na tři tečky (...) a vyberte **přizpůsobit data dlaždice**, což vám umožní Upravit filtr času hlavní nebo odebrat konkrétní dlaždice na řídicím panelu.

Další informace o práci s dotazy, naleznete v tématu [kurzu: Vizuálních dat v Log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md)

### <a name="add-a-new-tile"></a>Přidat novou dlaždici

Pokud chcete přidat novou dlaždici, můžete ho přidat na existující řídicí panel, ten, který vytvoříte nebo Azure Sentinelu integrovaný řídicí panel. 
1. V Log Analytics, vytvoření dlaždice s pomocí pokynů uvedených v [kurzu: Vizuálních dat v Log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md). 
2. Po vytvoření dlaždice, v části **Pin**, vyberte řídicí panel, ve kterém chcete dlaždici se zobrazí.

## <a name="create-new-dashboards"></a>Vytvořit nové řídicí panely
Můžete vytvořit nový řídicí panel úplně od začátku nebo použít integrovaný řídicí panel jako základ pro váš nový řídicí panel.

1. Chcete-li vytvořit zcela nový řídicí panel, vyberte **řídicí panely** a potom **+ nový řídicí panel**.
2. Vyberte předplatné, které se vytvoří řídicí panel v a přiřaďte jí popisný název. Každý řídicí panel je prostředek Azure stejně jako jakýkoli jiný, a můžete ji můžete přiřadit role (RBAC) k definování a omezení, kdo má přístup k. 
3. Se objeví v řídicích panelech a připnout vizualizace na povolit, je nutné ho sdílet. Klikněte na tlačítko **sdílenou složku** a potom **spravovat uživatele**. 
 
1. Použití **zkontrolovat přístup** a **přiřazení rolí** stejně jako pro libovolné prostředky Azure. Další informace najdete v tématu [řídicích panelů sdílenou složku Azure pomocí RBAC](../azure-portal/azure-portal-dashboard-share-access.md).


## <a name="new-dashboard-examples"></a>Příklady nový řídicí panel

Následující ukázkový dotaz můžete porovnat trendy provoz napříč týdnů. Můžete snadno přepínat, spusťte dotaz na zdroj dat a dodavatele zařízení. Tento příklad používá SecurityEvent z Windows, můžete přepnout jeho spuštění v AzureActivity nebo CommonSecurityLog na jakoukoli jinou bránu firewall.

     |where DeviceVendor = = "Palo Alto Networks":
      // week over week query
      SecurityEvent
      | where TimeGenerated > ago(14d)
      | summarize count() by bin(TimeGenerated, 1d)
      | extend Week = iff(TimeGenerated>ago(7d), "This Week", "Last Week"), TimeGenerated = iff(TimeGenerated>ago(7d), TimeGenerated, TimeGenerated + 7d)


Můžete chtít vytvořit dotaz, který zahrnuje data z více zdrojů. Můžete vytvořit dotaz, který se zabývá protokolů auditu Azure Active Directory pro nové uživatele, které byly vytvořeny pouze a kontroly protokolů Azure zobrazit, pokud uživatel spustí provádění změn přiřazení rolí během 24 hodin od vytvoření. Podezřelé aktivity se zobrazují na tomto řídicím panelu:

    AuditLogs
    | where OperationName == "Add user"
    | project AddedTime = TimeGenerated, user = tostring(TargetResources[0].userPrincipalName)
    | join (AzureActivity
    | where OperationName == "Create role assignment"
    | project OperationName, RoleAssignmentTime = TimeGenerated, user = Caller) on user
    | project-away user1

Můžete vytvořit na základě role osoby prohlížení dat a co hledají různých řídicích panelech. Můžete například vytvořit řídicí panel pro správce sítě, která obsahuje data brány firewall. Můžete také vytvořit řídicí panely založené na tom, jak často chcete podívejte se na ně, zda věcí, které chcete zkontrolovat každý den a jiné položky, které chcete zkontrolovat jednou za hodinu, například můžete chtít podívat na přihlášení Azure AD za hodinu pro hledání anomali ES. 

## <a name="create-new-detections"></a>Vytvořit nové detekce

Generovat detekce [zdroje dat, které jste se připojili k Azure Sentinelu](connect-data-sources.md) k prošetření hrozeb ve vaší organizaci.

Při vytváření nové detekce, využijte integrované detekce vytvořený zabezpečení odborníky společnosti Microsoft, které jsou přizpůsobené ke zdrojům dat, ke kterému jste připojeni.

1. [V komunitě GitHub](https://github.com/Azure/Azure-Sentinel/tree/master/Detections) přejděte **detekce** složky a vyberte příslušné složky.
   ![příslušné složky](./media/qs-get-visibility/detection-folders.png)
 
3.  Přejděte **Analytics** kartě a vyberte **přidat**.
   ![Vytvoření pravidla v Log Analytics](./media/qs-get-visibility/query-params.png)

3.  Zkopírujte všechny parametry do pravidla a klikněte na tlačítko **vytvořit**.
   ![Vytvořit pravidlo upozornění](./media/qs-get-visibility/create-alert-rule.png)

 
## <a name="next-steps"></a>Další postup
V tomto rychlém startu jste zjistili, jak chcete začít používat Azure Sentinelu. Dál najdete v tomto kurzu [jak detekovat hrozby](tutorial-detect-threats.md).
> [!div class="nextstepaction"]
> [Detekce hrozeb](tutorial-detect-threats.md) k automatizaci vaše odpovědi na ně.

