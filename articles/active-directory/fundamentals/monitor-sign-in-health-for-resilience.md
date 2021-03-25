---
title: Monitorovat stav přihlašování k aplikacím pro odolnost v Azure Active Directory
description: Vytvořte dotazy a oznámení, abyste mohli monitorovat stav přihlášení vašich aplikací.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47691ae404f65f04ace36485cb01fc5617d00a9a
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105031762"
---
# <a name="monitoring-application-sign-in-health-for-resilience"></a>Monitorování stavu přihlašování aplikací pro odolnost

Pro zvýšení odolnosti infrastruktury nastavte monitorování stavu přihlašování k aplikacím pro důležité aplikace, aby se zobrazila výstraha v případě, že dojde k dopadu na incident. Pro pomoc s tímto úsilím můžete nakonfigurovat výstrahy na základě sešitu stavu přihlášení. 

Tento sešit umožňuje správcům monitorovat žádosti o ověření pro aplikace ve vašem tenantovi. Nabízí tyto klíčové funkce:

* Nakonfigurujte sešit tak, aby sledoval všechny nebo jednotlivé aplikace s daty téměř v reálném čase.

* Nakonfigurujte výstrahy, které vás upozorní na změnu vzorů ověřování, abyste mohli prozkoumat a provést akci.

* Porovnejte trendy za určité období, například týden po týdnu, což je výchozí nastavení sešitu.

> [!NOTE]
> Chcete-li zobrazit všechny dostupné sešity a předpoklady jejich použití, přečtěte si téma [jak použít Azure monitor sešity pro sestavy](../reports-monitoring/howto-use-azure-monitor-workbooks.md).

Během události se může vyskytnout dvě věci:

* Počet přihlášení pro aplikaci může precipitously vyřadit, protože se uživatelé nemůžou přihlásit.

* Počet neúspěšných přihlášení se může zvýšit. 

Tento článek vás provede nastavením sešitu stavu přihlášení, který monitoruje přerušení přihlášení vašich uživatelů.

## <a name="prerequisites"></a>Požadavky

* Tenanta Azure AD.

* Uživatel s rolí globálního správce nebo správce zabezpečení pro tenanta Azure AD.

* Log Analytics pracovní prostor v předplatném Azure pro odesílání protokolů do protokolů Azure Monitor. 

   * Informace o tom, jak [vytvořit pracovní prostor Log Analytics](../../azure-monitor/logs/quick-create-workspace.md)

* Protokoly služby Azure AD integrované s protokoly Azure Monitor

   * Naučte se [integrovat protokoly přihlášení do služby Azure AD pomocí Azure monitorového streamu.](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

## <a name="configure-the-app-sign-in-health-workbook"></a>Konfigurace sešitu pro přihlášení ke stavu aplikace 

Chcete-li získat přístup k sešitům, otevřete **Azure Portal**, vyberte položku **Azure Active Directory** a pak vyberte možnost **sešity**.

V části použití, podmíněný přístup a řešení potíží uvidíte sešity. V části využití se zobrazí sešit pro přihlášení do aplikace.

Když použijete sešit, může se zobrazit v části nedávno změněné sešity.

![Snímek obrazovky znázorňující galerii sešitů v Azure Portal](./media/monitor-sign-in-health-for-resilience/sign-in-health-workbook.png)


Sešit stavu přihlášení aplikace umožňuje vizualizovat, co se děje s vašimi přihlášeními. 

Ve výchozím nastavení sešit nabízí dva grafy. Tyto grafy porovnávají, co se děje s vašimi aplikacemi, a to oproti stejné době před týden. Modré čáry jsou aktuální a oranžové řádky jsou předchozí týden.

![Snímek obrazovky znázorňující grafy pro přihlášení ke stavu](./media/monitor-sign-in-health-for-resilience/sign-in-health-graphs.png)

**První graf je hodinové použití (počet úspěšných uživatelů)**. Porovnání aktuálního počtu úspěšných uživatelů až po typickou dobu použití vám pomůže umístit odložení na místo, které může vyžadovat šetření. Míra úspěšnosti vyřazení může přispět k detekci problémů s výkonem a využitím, které nezpůsobí selhání. Například pokud uživatelé nebudou mít přístup k vaší aplikaci, aby se pokusili o přihlášení, nedošlo k žádným chybám, a to jenom při použití. Ukázkový dotaz pro tato data najdete v následující části.

**Druhý graf je hodinová míra selhání**. Špička v míře selhání může znamenat problém s ověřovacími mechanismy. Frekvence selhání se dá změřit jenom v případě, že se uživatelé můžou pokusit ověřit. Pokud uživatelé nemůžou získat přístup k pokusu, neobjeví se chyba.

Můžete nakonfigurovat výstrahu, která upozorní konkrétní skupinu, když míra využití nebo selhání překročí zadanou prahovou hodnotu. Ukázkový dotaz pro tato data najdete v následující části.

## <a name="configure-the-query-and-alerts"></a>Konfigurace dotazů a upozornění

Vytvoříte pravidla upozornění v Azure Monitor a v pravidelných intervalech můžete automaticky spouštět uložené dotazy nebo vlastní prohledávání protokolů.

Pomocí následujících pokynů můžete vytvořit e-mailová upozornění založená na dotazech, které se projeví v grafech. Ukázkové skripty níže odešlou e-mailové oznámení, když

* úspěšnost použití se od jedné hodiny před dvěma dny vynechá 90%, jako v grafu hodinového využití v předchozí části. 

* frekvence selhání se zvyšuje o 90% od stejné hodiny před dvěma dny, jako v grafu četnosti chyb v předchozí části. 

 Pokud chcete nakonfigurovat základní dotaz a nastavit upozornění, proveďte následující kroky. Vzorový dotaz použijete jako základ pro vaši konfiguraci. Na konci této části se zobrazí vysvětlení struktury dotazu.

Další informace o tom, jak vytvářet, zobrazovat a spravovat výstrahy protokolu pomocí Azure Monitor najdete v tématu [Správa výstrah protokolu](../../azure-monitor/alerts/alerts-log.md).

1. V sešitu vyberte **Upravit** a pak vyberte **ikonu dotazu** hned nad pravou stranu grafu.   

   [![Snímek obrazovky s upraveným sešitem](./media/monitor-sign-in-health-for-resilience/edit-workbook.png)](./media/monitor-sign-in-health-for-resilience/edit-workbook.png)

   Otevře se protokol dotazu.

   [![Snímek obrazovky se zobrazeným protokolem dotazů](./media/monitor-sign-in-health-for-resilience/query-log.png)](./media/monitor-sign-in-health-for-resilience/query-log.png)
‎

2. Zkopírujte jeden z ukázkových skriptů pro nový dotaz Kusto.  
   * [Dotaz Kusto na zvýšení míry selhání](#kusto-query-for-increase-in-failure-rate)
   * [Dotaz Kusto pro použití při odtažení](#kusto-query-for-drop-in-usage)

3. Vložte dotaz do okna a vyberte **Spustit**. Ujistěte se, že je na obrázku níže zobrazená dokončená zpráva a že se zobrazí zpráva s výsledky pod ní.

   [![Snímek obrazovky znázorňující výsledky spuštění dotazu.](./media/monitor-sign-in-health-for-resilience/run-query.png)](./media/monitor-sign-in-health-for-resilience/run-query.png)

4. Zvýrazněte dotaz a vyberte + **nové pravidlo výstrahy**. 
 
   [![Snímek obrazovky znázorňující obrazovku nového pravidla výstrahy](./media/monitor-sign-in-health-for-resilience/new-alert-rule.png)](./media/monitor-sign-in-health-for-resilience/new-alert-rule.png)


5. Konfigurace podmínek upozornění. V části podmínka vyberte odkaz **vždy, když je průměrné hledání vlastního protokolu větší než počet definovaných logických protokolů**. V podokně Konfigurace logiky signálu přejděte do logiky výstrah.

   [![Snímek obrazovky se zobrazením konfigurace výstrah](./media/monitor-sign-in-health-for-resilience/configure-alerts.png)](./media/monitor-sign-in-health-for-resilience/configure-alerts.png)
 
   * **Prahová hodnota**: 0. Tato hodnota bude upozorňovat na jakékoli výsledky.

   * **Zkušební období (v minutách)**: 2880. Tato hodnota vypadá po hodinách.

   * **Frekvence (v minutách)**: 60. Tato hodnota nastaví zkušební období na jednu hodinu pro předchozí hodinu.

   * Vyberte **Hotovo**.

6. V části **Akce** nakonfigurujte tato nastavení:  

    [![Snímek obrazovky znázorňující stránku vytvořit pravidlo výstrahy](./media/monitor-sign-in-health-for-resilience/create-alert-rule.png)](./media/monitor-sign-in-health-for-resilience/create-alert-rule.png)

   * V části **Akce** zvolte **možnost vybrat skupinu akcí** a přidejte skupinu, které chcete upozornit na výstrahy.

   * V části **přizpůsobit akce** vyberte **e-mailové výstrahy**.

   * Přidejte **řádek předmětu**.

7. V části **Podrobnosti pravidla výstrahy** nakonfigurujte tato nastavení:

   * Přidejte popisný název a popis.

   * Vyberte **skupinu prostředků** , do které chcete přidat výstrahu.

   * Vyberte výchozí **závažnost** výstrahy.

   * Po **Vytvoření vyberte Povolit pravidlo výstrahy** , pokud chcete hned hned začít, jinak vyberte **potlačit výstrahy**.

8. Vyberte **Vytvořit pravidlo upozornění**.

9. Vyberte **Save (Uložit**), zadejte název dotazu a **uložte ho jako dotaz s kategorií výstrahy**. Pak vyberte **Uložit** znovu.  

   [![Snímek obrazovky se zobrazeným tlačítkem uložit dotaz](./media/monitor-sign-in-health-for-resilience/save-query.png)](./media/monitor-sign-in-health-for-resilience/save-query.png)

### <a name="refine-your-queries-and-alerts"></a>Upřesnění dotazů a upozornění

Upravte dotazy a výstrahy pro maximální efektivitu.

* Nezapomeňte otestovat výstrahy.

* Upravte citlivost a četnost výstrah, abyste získali důležitá oznámení. Správci mohou být desenzibilizující na výstrahy, pokud získají příliš mnoho a nemůžou přijít o něco důležitého. 

* Zajistěte, aby se e-mail, ze kterého se přidávají upozornění v e-mailových klientech správce, přidal do seznamu povolené odesílatelé. V opačném případě možná nebudete dostávat oznámení z důvodu filtru spamu v e-mailovém klientovi. 

* Dotaz na výstrahy v Azure Monitor může zahrnout jenom výsledky z posledních 48 hodin. [Toto je aktuální omezení podle návrhu](https://github.com/MicrosoftDocs/azure-docs/issues/22637).

## <a name="sample-scripts"></a>Ukázkové skripty

### <a name="kusto-query-for-increase-in-failure-rate"></a>Dotaz Kusto na zvýšení míry selhání

   Poměr v dolní části lze podle potřeby upravit a představuje procentuální změnu v provozu za poslední hodinu ve srovnání se stejným časem včera. 0,5 znamená, že v provozu je 50% rozdíl.

```kusto

let today = SigninLogs

| where TimeGenerated > ago(1h) // Query failure rate in the last hour
 
| project TimeGenerated, UserPrincipalName, AppDisplayName, status = case(Status.errorCode == "0", "success", "failure")

// Optionally filter by a specific application

//| where AppDisplayName == **APP NAME**

| summarize success = countif(status == "success"), failure = countif(status == "failure") by bin(TimeGenerated, 1h) // hourly failure rate

| project TimeGenerated, failureRate = (failure * 1.0) / ((failure + success) * 1.0)

| sort by TimeGenerated desc

| serialize rowNumber = row_number();

let yesterday = SigninLogs

| where TimeGenerated between((ago(1h) - totimespan(1d))..(now() - totimespan(1d))) // Query failure rate at the same time yesterday

| project TimeGenerated, UserPrincipalName, AppDisplayName, status = case(Status.errorCode == "0", "success", "failure")

// Optionally filter by a specific application

//| where AppDisplayName == **APP NAME**

| summarize success = countif(status == "success"), failure = countif(status == "failure") by bin(TimeGenerated, 1h) // hourly failure rate at same time yesterday

| project TimeGenerated, failureRateYesterday = (failure * 1.0) / ((failure + success) * 1.0)

| sort by TimeGenerated desc

| serialize rowNumber = row_number();
today
| join (yesterday) on rowNumber // join data from same time today and yesterday

| project TimeGenerated, failureRate, failureRateYesterday

// Set threshold to be the percent difference in failure rate in the last hour as compared to the same time yesterday

| where abs(failureRate - failureRateYesterday) > 0.5

```

### <a name="kusto-query-for-drop-in-usage"></a>Dotaz Kusto pro použití při odtažení

V následujícím dotazu porovnáváme provoz za poslední hodinu do stejné doby včera.
Vylučujeme sobotu, neděli a pondělí, protože se očekává v těchto dnech, kdy by existovala velká variabilita provozu ve stejnou dobu jako předchozí den. 

Poměr v dolní části lze podle potřeby upravit a představuje procentuální změnu v provozu za poslední hodinu ve srovnání se stejným časem včera. 0,5 znamená, že v provozu je 50% rozdíl.

*Tyto hodnoty byste měli upravit tak, aby vyhovovaly vašemu modelu obchodní operace*.

```Kusto
 let today = SigninLogs // Query traffic in the last hour

| where TimeGenerated > ago(1h)

| project TimeGenerated, AppDisplayName, UserPrincipalName

// Optionally filter by AppDisplayName to scope query to a single application

//| where AppDisplayName contains "Office 365 Exchange Online"

| summarize users = dcount(UserPrincipalName) by bin(TimeGenerated, 1hr) // Count distinct users in the last hour

| sort by TimeGenerated desc

| serialize rn = row_number();

let yesterday = SigninLogs // Query traffic at the same hour yesterday

| where TimeGenerated between((ago(1h) - totimespan(1d))..(now() - totimespan(1d))) // Count distinct users in the same hour yesterday

| project TimeGenerated, AppDisplayName, UserPrincipalName

// Optionally filter by AppDisplayName to scope query to a single application

//| where AppDisplayName contains "Office 365 Exchange Online"

| summarize usersYesterday = dcount(UserPrincipalName) by bin(TimeGenerated, 1hr)

| sort by TimeGenerated desc

| serialize rn = row_number();

today
| join // Join data from today and yesterday together
(
yesterday
)
on rn

// Calculate the difference in number of users in the last hour compared to the same time yesterday

| project TimeGenerated, users, usersYesterday, difference = abs(users - usersYesterday), max = max_of(users, usersYesterday)

 extend ratio = (difference * 1.0) / max // Ratio is the percent difference in traffic in the last hour as compared to the same time yesterday

// Day variable is the number of days since the previous Sunday. Optionally ignore results on Sat, Sun, and Mon because large variability in traffic is expected.

| extend day = dayofweek(now())

| where day != time(6.00:00:00) // exclude Sat

| where day != time(0.00:00:00) // exclude Sun

| where day != time(1.00:00:00) // exclude Mon

| where ratio > 0.7 // Threshold percent difference in sign-in traffic as compared to same hour yesterday

```

## <a name="create-processes-to-manage-alerts"></a>Vytváření procesů pro správu výstrah

Po nastavení dotazu a výstrah vytvořte obchodní procesy pro správu výstrah.

* Kdo bude monitorovat sešit a kdy?

* Když se vygeneruje výstraha, která se prošetří?

* Jaké jsou požadavky na komunikaci? Kdo bude tuto komunikaci vytvářet a kdo je obdrží?

* Pokud dojde k výpadku, jaké obchodní procesy je potřeba aktivovat?

## <a name="next-steps"></a>Další kroky

[Další informace o sešitech](../reports-monitoring/howto-use-azure-monitor-workbooks.md)
