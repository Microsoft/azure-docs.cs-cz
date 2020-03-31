---
title: 'Úvodní příručka: Začínáme s Azure Sentinelem'
description: Azure Sentinel Quickstart – začínáme s Azure Sentinelem
services: sentinel
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: quickstart
ms.custom: mvc, fasttrack-edit
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 95dcc135593c566eb1319ed52df3df6c1ada6609
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80067687"
---
# <a name="quickstart-get-started-with-azure-sentinel"></a>Úvodní příručka: Začínáme s Azure Sentinelem




V tomto rychlém startu se dozvíte, jak rychle zobrazit a sledovat, co se děje ve vašem prostředí pomocí Azure Sentinelu. Po připojení zdrojů dat k Azure Sentinelu získáte okamžitou vizualizaci a analýzu dat, abyste věděli, co se děje ve všech připojených zdrojích dat. Azure Sentinel vám poskytuje sešity, které vám poskytují plný výkon nástrojů, které jsou už v Azure k dispozici, stejně jako tabulky a grafy, které jsou integrované tak, aby vám poskytovaly analýzy pro vaše protokoly a dotazy. Můžete buď použít předdefinované sešity, nebo vytvořit nový sešit snadno, od začátku nebo na základě existujícího sešitu. 

## <a name="get-visualization"></a>Získání vizualizace

Chcete-li vizualizovat a získat analýzu toho, co se děje ve vašem prostředí, nejprve se podívejte na řídicí panel s přehledem a získejte představu o stavu zabezpečení vaší organizace. Můžete kliknout na každý prvek těchto dlaždic přejít k podrobnostem nezpracovaná data, ze kterých jsou vytvořeny. Abychom vám pomohli snížit šum a minimalizovat počet výstrah, které je třeba zkontrolovat a prozkoumat, Azure Sentinel používá techniku fúze ke korelaci výstrah s incidenty. **incidenty** jsou skupiny souvisejících výstrah, které společně vytvářejí žalovatelný incident, který můžete vyšetřit a vyřešit.

- Na webu Azure Portal vyberte Azure Sentinel a pak vyberte pracovní prostor, který chcete monitorovat.

  ![Přehled služby Azure Sentinel](./media/qs-get-visibility/overview.png)

- Panel nástrojů v horní části vám řekne, kolik událostí jste za vybrané časové období získali, a porovná je s předchozími 24 hodinami. Panel nástrojů vám z těchto událostí sdělí výstrahy, které byly spuštěny (malé číslo představuje změnu za posledních 24 hodin) a pak vám řekne, pro tyto události, kolik jsou otevřené, probíhající a uzavřené. Zkontrolujte, zda nedošlo k dramatickému nárůstu nebo poklesu počtu událostí. Pokud dojde k poklesu, může to být, že připojení přestalo vykazovat na Azure Sentinel. Pokud dojde k nárůstu, mohlo se stát něco podezřelého. Zkontrolujte, zda máte nová upozornění.

   ![Nálevka Azure Sentinelu](./media/qs-get-visibility/funnel.png)

Hlavní část stránky s přehledem poskytuje přehled o stavu zabezpečení pracovního prostoru:

- **Události a výstrahy v průběhu času**: Uvádí počet událostí a kolik výstrah bylo vytvořeno z těchto událostí. Pokud vidíte špičku, která je neobvyklá, měli byste vidět upozornění na to - pokud je něco neobvyklého, kde je špička v událostech, ale nevidíte výstrahy, může to být důvod k obavám.

- **Potenciální škodlivé události**: Když je provoz zjištěn ze zdrojů, o kterých je známo, že jsou škodlivé, Azure Sentinel vás upozorní na mapě. Pokud vidíte oranžovou, jedná se o příchozí provoz: někdo se pokouší získat přístup k vaší organizaci ze známé škodlivé IP adresy. Pokud se zobrazí odchozí (červená) aktivita, znamená to, že data z vaší sítě jsou přenášena z vaší organizace na známou škodlivou IP adresu.

   ![Mapa Azure Sentinelu](./media/qs-get-visibility/map.png)


- **Nedávné incidenty**: Zobrazení nedávných incidentů, jejich závažnosti a počtu upozornění souvisejících s incidentem. Pokud vidíte jako náhlý vrchol v určitém typu výstrahy, může to znamenat, že je aktivní útok v současné době spuštěna. Například pokud máte náhlý vrchol 20 Pass-the-hash události z ochrany ATP v oblasti Azure, je možné, že někdo aktuálně pokouší zaútočit na vás.

- **Anomálie zdroje dat**: Analytici dat společnosti Microsoft vytvořili modely, které neustále vyhledávají data z vašich zdrojů dat pro anomálie. Pokud nejsou žádné anomálie, nic se nezobrazí. Pokud jsou zjištěny anomálie, měli byste se do nich ponořit, abyste zjistili, co se stalo. Klikněte například na špičku v Azure Activity. Kliknutím na **graf** zobrazíte, kdy došlo k nárůstu špičky, a poté filtrujte aktivity, ke kterým došlo během tohoto časového období, abyste zjistili, co špičku způsobilo.

   ![Mapa Azure Sentinelu](./media/qs-get-visibility/anomolies.png)

## <a name="use-built-in-workbooks"></a>Použití předdefinovaných sešitů<a name="dashboards"></a>

Integrované sešity poskytují integrovaná data z připojených zdrojů dat, která vám umožní hlouběji se ponořit do událostí generovaných v těchto službách. Integrované sešity zahrnují Azure AD, události aktivit Azure a místní, což mohou být data ze serverů Windows, z výstrah první strany, od všech třetích stran, včetně protokolů přenosů brány firewall, Office 365 a nezabezpečených protokolů založených na Windows Události. Sešity jsou založené na sešitech Azure Monitor, které vám poskytnou vylepšenou přizpůsobitelnost a flexibilitu při navrhování vlastního sešitu. Další informace naleznete v tématu [Sešity](../azure-monitor/app/usage-workbooks.md).

1. V části **Nastavení**vyberte **Sešity**. V části **Nainstalovaný**můžete zobrazit všechny nainstalované sešity. V části **Vše**můžete zobrazit celou galerii vestavěných sešitů, které jsou k dispozici pro instalaci. 
2. Vyhledejte konkrétní sešit, abyste viděli celý seznam a popis toho, co jednotlivé nabídky nabízejí. 
3. Za předpokladu, že používáte Azure AD, abyste mohli začít pracovat s Azure Sentinelem, doporučujeme nainstalovat alespoň následující sešity:
   - **Azure AD**: Použijte jednu nebo obě následující:
       - **Přihlášení Azure AD** analyzuje přihlášení v průběhu času zjistit, pokud existují anomálie. Tyto sešity poskytují neúspěšné přihlášení podle aplikací, zařízení a umístění, takže si můžete všimnout, na první pohled, pokud se stane něco neobvyklého. Věnujte pozornost několika neúspěšným přihlášením. 
       - **Protokoly auditu Azure AD** analyzuje aktivity správce, jako jsou změny v uživatelích (přidat, odebrat atd.), vytváření skupin a úpravy.  

   - Přidejte sešit pro bránu firewall. Přidejte například sešit Palo Alto. Sešit analyzuje provoz brány firewall a poskytuje vám korelace mezi daty brány firewall a událostmi hrozeb a upozorňuje na podezřelé události napříč entitami. Sešity vám poskytnou informace o trendech v návštěvnosti a umožní vám přejít k podrobnostem a filtrovat výsledky. 

      ![Pal Alto palubní deska](./media/qs-get-visibility/palo-alto-week-query.png)


Sešity můžete přizpůsobit úpravou hlavního](./media/qs-get-visibility/edit-query-button.png)tlačítka dotazu ![. Klepnutím na tlačítko ![](./media/qs-get-visibility/go-to-la-button.png) přejdete do [služby Log Analytics a upravte dotaz a](../azure-monitor/log-query/get-started-portal.md)můžete vybrat tři tečky (...) a vybrat **možnost Přizpůsobit data dlaždic**, která umožňuje upravit hlavní časový filtr, nebo odebrat konkrétní dlaždice ze sešitu.

Další informace o práci s dotazy najdete v [tématu Výuka: Vizuální data v Log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md)

### <a name="add-a-new-tile"></a>Přidání nové dlaždice

Pokud chcete přidat novou dlaždici, můžete ji přidat do existujícího sešitu, buď ten, který vytvoříte, nebo do integrovaného sešitu Azure Sentinelu. 
1. V Log Analytics vytvořte dlaždici podle pokynů v [kurzu: Vizuální data v Log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md). 
2. Po vytvoření dlaždice vyberte v části **Připnout**sešit, ve kterém se má dlaždice zobrazit.

## <a name="create-new-workbooks"></a>Vytvoření nových sešitů
Můžete vytvořit nový sešit od začátku nebo použít předdefinovaný sešit jako základ pro nový sešit.

1. Pokud chcete vytvořit nový sešit od začátku, vyberte **Sešity** a potom **+Nový sešit**.
2. Vyberte předplatné, ve které se sešit vytvoří, a pojmenujte jej popisným názvem. Každý sešit je prostředek Azure jako každý jiný a můžete mu přiřadit role (RBAC) k definování a omezení, kdo má přístup. 
3. Chcete-li, aby se vizualizace zobrazovaly v sešitech, musíte je sdílet. Klikněte na **Sdílet** a potom **na Spravovat uživatele**. 
 
1. Použijte **zkontrolovat přístup** a **přiřazení rolí,** jako byste to udělali pro jakýkoli jiný prostředek Azure. Další informace najdete [v tématu Sdílení sešitů Azure pomocí RBAC](../azure-portal/azure-portal-dashboard-share-access.md).


## <a name="new-workbook-examples"></a>Příklady nových sešitů

Následující ukázkový dotaz umožňuje porovnat trendy provozu v týdnech. Můžete snadno přepnout, který dodavatel zařízení a zdroj dat spustíte dotaz. Tento příklad používá SecurityEvent ze systému Windows, můžete přepnout spustit na AzureActivity nebo CommonSecurityLog na jakékoli jiné brány firewall.

     |where DeviceVendor == "Palo Alto Networks":
      // week over week query
      SecurityEvent
      | where TimeGenerated > ago(14d)
      | summarize count() by bin(TimeGenerated, 1d)
      | extend Week = iff(TimeGenerated>ago(7d), "This Week", "Last Week"), TimeGenerated = iff(TimeGenerated>ago(7d), TimeGenerated, TimeGenerated + 7d)


Můžete chtít vytvořit dotaz, který obsahuje data z více zdrojů. Můžete vytvořit dotaz, který se podívá na protokoly auditu služby Azure Active Directory pro nové uživatele, kteří byli právě vytvořeni, a potom zkontroluje vaše protokoly Azure, abyste zjistili, jestli uživatel začal provádět změny přiřazení rolí do 24 hodin od vytvoření. Tato podezřelá aktivita by se objevila na tomto řídicím panelu:

    AuditLogs
    | where OperationName == "Add user"
    | project AddedTime = TimeGenerated, user = tostring(TargetResources[0].userPrincipalName)
    | join (AzureActivity
    | where OperationName == "Create role assignment"
    | project OperationName, RoleAssignmentTime = TimeGenerated, user = Caller) on user
    | project-away user1

Můžete vytvářet různé sešity na základě role osoby, která se dívá na data a na to, co hledá. Můžete například vytvořit sešit pro správce sítě, který obsahuje data brány firewall. Sešity můžete také vytvářet podle toho, jak často se na ně chcete dívat, jestli chcete kontrolovat věci denně a další položky, které chcete zkontrolovat jednou za hodinu, například můžete se každou hodinu podívat na přihlášení azure ad, abyste mohli hledat anomálie. . 

## <a name="create-new-detections"></a>Vytváření nových detekcí

Generujte zjišťování [na zdrojích dat, které jste připojili k Azure Sentinelu,](connect-data-sources.md) abyste prozkoumali hrozby ve vaší organizaci.

Při vytváření nové detekce využijte integrované detekce vytvořené výzkumníky zabezpečení společnosti Microsoft, které jsou přizpůsobeny zdrojům dat, které jste připojili.

Chcete-li zobrazit všechna zjišťování před výběrem, přejděte na **službu Analytics** a potom na **šablony pravidel**. Tato karta obsahuje všechna předdefinovaná pravidla Azure Sentinelu.

   ![Hledání hrozeb pomocí Azure Sentinelu pomocí integrovaných detekčních detekcí](media/tutorial-detect-built-in/view-oob-detections.png)

Další informace o získání out-of-the-box detekce, naleznete [v tématu kurz: Získání vestavěné analýzy](tutorial-detect-threats-built-in.md).
 
## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste se naučili, jak začít používat Azure Sentinel. Pokračujte v [kurzu, jak zjistit hrozby](tutorial-detect-threats-built-in.md).
> [!div class="nextstepaction"]
> [Vytvořte vlastní pravidla detekce hrozeb,](tutorial-detect-threats-custom.md) která automatizují vaše reakce na hrozby.

