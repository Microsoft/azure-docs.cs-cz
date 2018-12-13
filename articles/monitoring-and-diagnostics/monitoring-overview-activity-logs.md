---
title: Přehled protokolu aktivit Azure
description: Zjistěte, co je protokol aktivit Azure a jak ho použít k pochopení události, ke kterým dochází v rámci vašeho předplatného Azure.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: johnkem
ms.component: activitylog
ms.openlocfilehash: 46b00e102cbf5e981ac7036de65232e869dc9651
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53272693"
---
# <a name="monitor-subscription-activity-with-the-azure-activity-log"></a>Monitorování aktivit předplatného s protokolem aktivit Azure

**Protokolu aktivit Azure** je předplatné protokol, který poskytuje podrobné informace o události na úrovni předplatného, ke kterým došlo v Azure. To zahrnuje celou řadu dat z Azure Resource Manageru provozních dat k aktualizacím na události služby Service Health. Protokol aktivit se dřív označovalo jako "Protokolů auditu" nebo "Provozní protokoly," od události administrativní kategorie sestavy rovina řízení pro vaše předplatná. Použití protokolu aktivit, můžete určit "co, kdo a kdy" veškerých operací (PUT, POST, DELETE) provedených ve prostředků ve vašem předplatném zápisu. Můžete také zjištění stavu operace a další relevantní vlastnosti. Protokol aktivit neobsahuje operace čtení (GET) ani operace pro prostředky, které používají Classic nebo model "RDFE".

![Vs protokoly aktivit jiné typy protokolů ](./media/monitoring-overview-activity-logs/Activity_Log_vs_other_logs_v5.png)

Obrázek 1: Vs protokoly aktivit jiné typy protokolů

Protokol aktivit se liší od [diagnostické protokoly](monitoring-overview-of-diagnostic-logs.md). Protokoly aktivit poskytují data o operacích v prostředku z vnějšku ("rovině řízení"). Diagnostické protokoly jsou emitovány prostředek a poskytnout informace o fungování tohoto prostředku ("rovině dat").

> [!WARNING]
> Protokol aktivit Azure je primárně určen pro aktivity, ke kterým dochází v Azure Resource Manageru. Sledovat prostředky pomocí modelu Classic/RDFE. Některé typy prostředků Classic mají poskytovatele prostředků proxy serveru v Azure Resource Manageru (například Microsoft.ClassicCompute). Pokud pracujete s typem prostředku klasické prostřednictvím Azure Resource Manageru pomocí těchto poskytovatelů prostředků proxy serveru, operace se zobrazí v protokolu aktivit. Pokud pracujete s typem prostředku klasické mimo proxy služby Azure Resource Manageru, vaše akce se zaznamenávají jenom v operaci protokolu. Protokol operace můžete procházet v samostatných oddílech portálu.
>
>

Události můžete načíst z váš protokol aktivit pomocí webu Azure portal, rozhraní příkazového řádku, rutin prostředí PowerShell a rozhraní REST API služby Azure Monitor.

> [!NOTE]
> [Novější upozornění](monitoring-overview-alerts.md) nabídnout vylepšené prostředí při vytváření a správa aktivit pravidel upozornění protokolů.  [Další informace](../azure-monitor/platform/alerts-activity-log.md).


## <a name="categories-in-the-activity-log"></a>Kategorie v protokolu aktivit
Protokol aktivit obsahuje několik kategorií data. Úplné podrobnosti o schémat z těchto kategorií [najdete v článku](monitoring-activity-log-schema.md). Mezi ně patří:
* **Pro správu** – Tato kategorie obsahuje záznam všech vytvoření, aktualizace, odstranění a akce operace provést prostřednictvím Resource Manageru. Typy událostí, zobrazí se v této kategorii příklady "vytvořit virtuální počítač" a "odstranit skupinu zabezpečení sítě" každé akce, které uživatele nebo aplikace pomocí Resource Manageru je modelovaná jako operace na konkrétní typ prostředku. Pokud je typ operace zápisu, Delete nebo akce, záznamy o zahájení a úspěchu nebo selhání této operace se zaznamenávají do administrativní kategorie. Administrativní kategorie také zahrnuje všechny změny na řízení přístupu na základě rolí v rámci předplatného.
* **Stav služby** – Tato kategorie obsahuje záznam všechny incidenty health service, ke kterým došlo v Azure. Je například typ události, kterou byste viděli v této kategorii "SQL Azure v oblasti východní USA má výpadek." Události služby Service health dostaneme v pěti typy: Požaduje se akce, obnovení s asistencí, Incident, údržby, informace nebo zabezpečení a zobrazí pouze v případě, že máte prostředek v rámci předplatného, který bude mít vliv na událost.
* **Služba Resource Health** – Tato kategorie obsahuje záznam všechny události stavu prostředků, ke kterým došlo u vašich prostředků Azure. Je například typ události, kterou byste viděli v této kategorii "Virtuální počítač stav změní na není k dispozici." Události stavu prostředků může představovat jeden z čtyř stavů stavu: K dispozici, není k dispozici, snížení a neznámý. Kromě toho události stavu prostředků můžete zařadit iniciovanou platformy nebo uživatele.
* **Upozornění** – Tato kategorie obsahuje záznam všech aktivací upozornění v Azure. Je například typ události, kterou byste viděli v této kategorii "% využití procesoru na myVM je už více než 80 posledních 5 minut." Výstrahy koncept mají různé systémy pro Azure – můžete definovat pravidla s nějakým a když podmínky odpovídají tímto pravidlem, dostanete oznámení. Pokaždé, když typ podporovaných Azure výstrahy "aktivuje," nebo ke generování oznámení o splnění podmínek, záznamy o aktivaci se nasdílejí také do této kategorie protokolu aktivit.
* **Automatické škálování** – Tato kategorie obsahuje záznam žádné události vztahující se k operaci modul automatického škálování na základě jakékoli nastavení automatického škálování jste definovali v rámci vašeho předplatného. Je například typ události, kterou byste viděli v této kategorii "Automatického vertikálního navýšení kapacity akce se nezdařila." Použití automatického škálování, můžete automaticky škálovat na více systémů nebo škálování počtu instancí v podporovaných prostředků typu na základě času dne a/nebo zatížení (metriky) dat s využitím nastavení automatického škálování. Pokud jsou splněny podmínky škálování směrem nahoru nebo dolů, spuštění a úspěšné nebo neúspěšné událostí se zaznamenávají v této kategorii.
* **Doporučení** – Tato kategorie obsahuje události doporučení Azure advisoru.
* **Zabezpečení** – Tato kategorie obsahuje záznam všech výstrah generovaných v Azure Security Center. Je například typ události, kterou byste viděli v této kategorii "podezřelou dvojitou příponou souboru provést."
* **Zásady** – Tato kategorie neobsahuje žádné události; je vyhrazená pro budoucí použití. 

## <a name="event-schema-per-category"></a>Schéma událostí podle jednotlivých kategorií
[Najdete v článku o schéma událostí protokolu aktivit podle jednotlivých kategorií.](monitoring-activity-log-schema.md)

## <a name="what-you-can-do-with-the-activity-log"></a>Co můžete dělat s protokolu aktivit
Tady jsou některé věci, které vám pomůžou s protokolu činnosti:

![Protokol aktivit v Azure](./media/monitoring-overview-activity-logs/Activity_Log_Overview_v3.png)


* Dotaz a zobrazit je v **webu Azure portal**.
* [Vytvořte výstrahu pro událost protokolu aktivit.](../azure-monitor/platform/activity-log-alerts.md)
* [Stream umožňuje **centra událostí** ](monitoring-stream-activity-logs-event-hubs.md) za účelem ingestování datových vlastní analýzy řešení, jako je například Power BI nebo služby třetích stran.
* Analyzovat pomocí Power BI [ **balíček obsahu Power BI**](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
* [Uložit ho. tím **účtu úložiště** pro archivaci nebo ruční kontrolu](monitoring-archive-activity-log.md). Můžete určit pomocí uchování (ve dnech) **profilu protokolu**.
* Zjistit pomocí rutiny Powershellu, rozhraní příkazového řádku nebo rozhraní REST API.

## <a name="query-the-activity-log-in-the-azure-portal"></a>Dotaz protokolu aktivit na webu Azure Portal
Na webu Azure portal můžete zobrazit protokol aktivit na několika místech:
* **Protokolu aktivit** , který se dostanete tak, že protokol aktivit v rámci **všechny služby** v levém navigačním podokně.
* **Monitorování** se zobrazí ve výchozím nastavení v levém navigačním podokně. Protokol aktivit je jeden oddíl služby Azure Monitor.
* Většina **prostředky**, například v okně Konfigurace virtuálního počítače. Protokol aktivit je část věnovanou většina oken prostředků a po kliknutí automaticky vyfiltruje události, které mají související se tento konkrétní prostředek.

Na webu Azure Portal můžete filtrovat protokol aktivit na základě těchto polí:
* Interval TimeSpan – počáteční a koncový čas pro události.
* Kategorie – kategorie událostí, jak je popsáno výše.
* Předplatné – jeden nebo více názvů předplatného Azure.
* Skupina prostředků - skupiny jednoho nebo více zdrojů v rámci těchto předplatných.
* Prostředek (název) – název konkrétní prostředek.
* Typ prostředku – typ prostředku, například Microsoft.Compute/virtualmachines.
* Název operace - název operace Azure Resource Manageru, třeba Microsoft.SQL/servers/Write.
* Závažnost – úroveň závažnosti události (Informativní upozornění, chyby, kritické).
* Událost inicioval(a) – "volající" nebo uživatele, který provedl operaci.
* Otevřít hledání – to je otevřít textové vyhledávací pole, který vyhledá tento řetězec ve všech polích ve všech událostí.

Po definování sady filtrů můžete připnout dotaz na řídicím panelu Azure vždy dohlížet na konkrétní události.

Pro ještě větší výkon, můžete kliknout na **protokoly** ikonu, která zobrazuje data protokolu aktivit v [řešení Log Analytics Activity Log Analytics](../azure-monitor/platform/collect-activity-logs.md). Okno Protokol aktivit nabízí prostředí základní filtr nebo přejděte na protokoly, ale Log Analytics umožňuje otáčení, dotazování a vizualizace dat výkonnější způsoby.

## <a name="export-the-activity-log-with-a-log-profile"></a>Exportovat protokol aktivit k profilu protokolu
A **profilu protokolu** řídí, jak exportovat protokol aktivit. Pomocí profilu protokolu, můžete nakonfigurovat:

* Pokud má být odeslána protokolu aktivit (účet úložiště nebo Event Hubs)
* Poslat kategorií, které události (akce zápisu, odstranění,). *Význam "kategorie" v profily protokolů a událostí protokolu aktivit se liší. V profilu protokolu "Kategorie" představuje typ operace (akce zápisu, odstranění,). V události protokolu aktivit vlastnost "kategorie" představuje zdroj nebo typu události (například správu ServiceHealth, upozornění a další).*
* Které oblasti (umístění) by měly být exportovány. Nezapomeňte uvést "globální", protože mnoho událostí v protokolu aktivit jsou globální události.
* Jak dlouho se uchovávají v účtu úložiště protokolu aktivit.
    - Uchování 0 dnů znamená, že protokoly se uchovávají navždy. V opačném případě hodnota může být libovolný počet dnů mezi 1 a 2147483647.
    - Pokud nejsou nastavené zásady uchovávání informací, ale ukládání protokolů v účtu úložiště je zakázaný (například pokud pouze jsou vybrané možnosti služby Event Hubs nebo Log Analytics), zásady uchovávání informací nemají žádný vliv.
    - Zásady uchovávání informací jsou použitých za den, takže na konci za den (UTC), tento počet protokolů ze dne, který je nyní mimo uchovávání se zásada odstraní. Například pokud máte zásady uchovávání informací o jeden den, na začátku dne dnes protokoly ze včerejška před den se odstraní. Proces odstraňování začíná o půlnoci UTC, ale Všimněte si, že může trvat až 24 hodin pro protokoly, které mají být odstraněny z vašeho účtu úložiště.

Můžete použít úložiště účtu nebo událostí centra oboru názvů, který není ve stejném předplatném jako jeden vysílá protokoly. Uživatel, který konfiguruje nastavení, musí mít správný přístup RBAC k oběma předplatným.

> [!NOTE]
>  Nelze aktuálně archivace dat do účtu úložiště, který je za zabezpečené virtuální síti.

> [!WARNING]
> Formát data protokolu v aktuálním účtu úložiště změnit na řádky JSON na 1. listopadu 2018. [Informace o dopadu a postup pro aktualizaci nástrojů, aby si s novým formátem poradily, najdete v tomto článku](./monitor-diagnostic-logs-append-blobs.md). 
>
> 

Tato nastavení můžete nakonfigurovat přes možnost "Export" v okně protokolu aktivit na portálu. Se taky dají konfigurovat prostřednictvím kódu programu [pomocí REST API služby Azure Monitor](https://msdn.microsoft.com/library/azure/dn931927.aspx), rutin prostředí PowerShell nebo rozhraní příkazového řádku. Předplatné může mít jenom jeden profil protokolu.

### <a name="configure-log-profiles-using-the-azure-portal"></a>Konfigurovat profily protokolů pomocí webu Azure portal
Můžete streamování protokolu aktivit do centra událostí nebo je uložíte na účet úložiště pomocí možnosti "Export do centra událostí" na webu Azure Portal.

1. Přejděte do **protokolu aktivit** pomocí nabídky na levé straně na portálu.

    ![Přejít na protokol aktivit v portálu](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate-v2.png)
2. Klikněte na tlačítko **exportovat do centra událostí** tlačítko v horní části okna.

    ![Tlačítko pro export na portálu](./media/monitoring-overview-activity-logs/activity-logs-portal-export-v2.png)
3. V zobrazeném okně můžete vybrat:  
  * oblasti, pro které chcete exportovat události
  * Účet úložiště, do kterého chcete uložit události
  * počet dní, které chcete uchovávat tyto události v úložišti. Nastavení 0 dnů uchová protokoly navždy.
  * Namespace služby Service Bus, ve kterém chcete vytvořit pro streamování tyto události v Centru událostí.

     ![Okno Export protokolu aktivit](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. Klikněte na tlačítko **Uložit** nastavení uložte. Nastavení se okamžitě použijí pro vaše předplatné.

### <a name="configure-log-profiles-using-the-azure-powershell-cmdlets"></a>Konfigurovat profily protokolů pomocí rutin Azure Powershellu

#### <a name="get-existing-log-profile"></a>Získat stávající profilu protokolu

```
Get-AzureRmLogProfile
```

#### <a name="add-a-log-profile"></a>Přidání profilu protokolu

```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
```

| Vlastnost | Požaduje se | Popis |
| --- | --- | --- |
| Název |Ano |Název profilu protokolu. |
| StorageAccountId |Ne |ID prostředku účtu úložiště, ke kterému má být uložen v protokolu aktivit. |
| serviceBusRuleId |Ne |ID pravidla služby Service Bus pro obor názvů služby Service Bus, by měl mít vytvořené v služby event hubs. Je řetězec v tomto formátu: `{service bus resource ID}/authorizationrules/{key name}`. |
| Umístění |Ano |Čárkami oddělený seznam oblasti, pro které chcete shromažďovat události protokolu aktivit. |
| RetentionInDays |Ano |Počet dní pro události, které by měla být zachována, od 1 do 2147483647. Hodnota nula ukládá protokoly po neomezenou dobu (trvale). |
| Kategorie |Ne |Čárkami oddělený seznam kategorie událostí, které se mají shromažďovat. Možné hodnoty jsou Write, Delete a akce. |

#### <a name="remove-a-log-profile"></a>Odebrání profilu protokolu
```
Remove-AzureRmLogProfile -name my_log_profile
```

### <a name="configure-log-profiles-using-the-azure-cli"></a>Konfigurovat profily protokolů pomocí Azure CLI

#### <a name="get-existing-log-profile"></a>Získat stávající profilu protokolu

```azurecli
az monitor log-profiles list
az monitor log-profiles show --name <profile name>
```

`name` Vlastnost by měla být název vašeho profilu protokolu.

#### <a name="add-a-log-profile"></a>Přidání profilu protokolu

```azurecli
az monitor log-profiles create --name <profile name> \
    --locations <location1 location2 ...> \
    --location <location> \
    --categories <category1 category2 ...>
```

Kompletní dokumentaci k vytvoření profilu sledování pomocí rozhraní příkazového řádku, najdete v článku [reference k příkazům rozhraní příkazového řádku](/cli/azure/monitor/log-profiles#az-monitor-log-profiles-create)

#### <a name="remove-a-log-profile"></a>Odebrání profilu protokolu

```azurecli
az monitor log-profiles delete --name <profile name>
```

## <a name="next-steps"></a>Další kroky
* [Další informace o protokolu aktivit (dříve protokoly auditu)](../azure-resource-manager/resource-group-audit.md)
* [Stream protokolů aktivit Azure do služby Event Hubs](monitoring-stream-activity-logs-event-hubs.md)
