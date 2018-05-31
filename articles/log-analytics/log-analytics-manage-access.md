---
title: Správa pracovních prostorů v Azure Log Analytics a na portálu OMS | Dokumentace Microsoftu
description: Pracovní prostory můžete spravovat v Log Analytics a na portálu OMS pomocí různých úloh správy prováděných s uživateli, účty, pracovními prostory a účty Azure.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: d0e5162d-584b-428c-8e8b-4dcaa746e783
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/16/2018
ms.author: magoedte
ms.openlocfilehash: d2480936ed54ec58ba289eae1ba605a16e27f0b3
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
ms.locfileid: "34271666"
---
# <a name="manage-workspaces"></a>Správa pracovních prostorů

Pro správu přístupu k Log Analytics provádíte různé úlohy správy související s pracovními prostory. Tento článek obsahuje postupy a rady založené na osvědčených postupech pro správu pracovních prostorů. Pracovní prostor je v podstatě kontejner, který obsahuje informace o účtu a jednoduché konfigurační informace. Vy nebo další členové vaší organizace můžete používat víc pracovních prostorů ke správě různých sad dat, která se shromažďují ze všech částí vaší infrastruktury IT.

K vytvoření pracovního prostoru budete muset:

1. Mít předplatné Azure.
2. Zvolit název pracovního prostoru.
3. Přiřadit pracovní prostor ke svému předplatnému.
4. Vybrat zeměpisné umístění.

## <a name="determine-the-number-of-workspaces-you-need"></a>Určení potřebného počtu pracovních prostorů
Pracovní prostor je prostředek Azure v podobě kontejneru, ve kterém se data shromažďují, agregují, analyzují a zobrazují na webu Azure Portal.

V rámci jednoho předplatného Azure můžete mít více pracovních prostorů a přístup k více než jednomu pracovnímu prostoru s možností snadného dotazování napříč pracovními prostory. Tato část popisuje případy, kdy je užitečné mít víc než jeden pracovní prostor.

V současné době pracovní prostor nabízí:

* Zeměpisné umístění úložiště dat
* Členitost fakturace
* Izolaci dat
* Obor pro konfiguraci

Na základě výše uvedených charakteristik můžete vytvořit víc pracovních prostorů, pokud:

* Jste globální společnost a potřebujete ukládat data v různých oblastech z důvodů suverenity dat nebo dodržování předpisů.
* Používáte Azure a chcete se vyhnout poplatkům za odchozí datové přenosy tím, že budete mít pracovní prostor ve stejné oblasti jako prostředky Azure, které spravuje.
* Chcete rozdělit náklady na různá oddělení nebo obchodní skupiny na základě využití. Když vytvoříte pracovní prostor pro každé oddělení nebo obchodní skupinu, vaše vyúčtování Azure a údaje o využití budou uvádět poplatky za každý pracovní prostor zvlášť.
* Jste poskytovatel spravované služby a potřebujete uchovávat data Log Analytics pro každého zákazníka odděleně od dat ostatních zákazníků.
* Spravujete několik zákazníků a chcete, aby se každý zákazník, oddělení nebo pracovní skupina mohli podívat na svá vlastní data, ale ne na data ostatních.

Při používání agentů Windows ke shromažďování dat můžete [každého agenta konfigurovat tak, aby ukládal data do jednoho nebo více pracovních prostorů](log-analytics-windows-agents.md).

Pokud používáte System Center Operations Manager, můžete připojit každou skupinu nástroje Operations Manager jen do jednoho pracovního prostoru. Můžete nainstalovat Microsoft Monitoring Agent do počítačů spravovaných nástrojem Operations Manager a nastavit agenta tak, aby odesílal data do nástroje Operations Manager i do jiného pracovního prostoru Log Analytics.

### <a name="workspace-information"></a>Informace o pracovním prostoru

Podrobnosti o pracovním prostoru můžete zobrazit na webu Azure Portal. Podrobnosti je možné zobrazit také na portálu OMS.

#### <a name="view-workspace-information-in-the-azure-portal"></a>Zobrazení informací o pracovním prostoru na webu Azure Portal

1. Pokud jste to ještě neudělali, přihlaste se na webu [Azure Portal](https://portal.azure.com) pomocí svého předplatného Azure.
2. V nabídce **Centra** klikněte na **Další služby** a v seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Klikněte na **Log Analytics**.  
    ![Centrum Azure](./media/log-analytics-manage-access/hub.png)  
3. V okně Předplatná Log Analytics vyberte pracovní prostor.
4. V okně pracovního prostoru se zobrazí podrobnosti o pracovním prostoru a odkazy na další informace.  
    ![Podrobnosti o pracovním prostoru](./media/log-analytics-manage-access/workspace-details.png)  


## <a name="manage-accounts-and-users"></a>Správa účtů a uživatelů
Každý pracovní prostor může mít přiřazených více účtů, z nichž každý (účet Microsoft nebo účet organizace) může mít přístup k několika pracovním prostorům.

Ve výchozím se správcem pracovního prostoru stává účet Microsoft nebo účet organizace použitý při vytvoření pracovního prostoru.

Existují dva modely oprávnění, které řídí přístup k pracovnímu prostoru Log Analytics:

1. Starší uživatelské role Log Analytics
2. [Přístup na základě rolí Azure](../active-directory/role-based-access-control-configure.md)

Následující tabulka shrnuje možná nastavení přístupu pomocí jednotlivých modelů oprávnění:

|                          | Portál Log Analytics | Azure Portal | Rozhraní API (včetně PowerShellu) |
|--------------------------|----------------------|--------------|----------------------------|
| Uživatelské role Log Analytics | Ano                  | Ne           | Ne                         |
| Přístup na základě rolí Azure  | Ano                  | Ano          | Ano                        |

> [!NOTE]
> Log Analytics přechází na model oprávnění používající přístup na základě rolí Azure, čímž nahrazuje uživatelské role Log Analytics.
>
>

Starší uživatelské role Log Analytics řídí přístup pouze k aktivitám prováděným na [portálu Log Analytics](https://mms.microsoft.com).

Následující aktivity také vyžadují oprávnění Azure:

| Akce                                                          | Potřebná oprávnění Azure | Poznámky |
|-----------------------------------------------------------------|--------------------------|-------|
| Přidání a odebrání řešení pro správu                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | |
| Změna cenové úrovně                                       | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Zobrazení dat na dlaždicích řešení *Backup* a *Site Recovery* | Správce nebo spolusprávce | Má přístup k prostředkům nasazeným pomocí modelu nasazení Azure Classic. |
| Vytvoření pracovního prostoru na webu Azure Portal                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||


### <a name="managing-access-to-log-analytics-using-azure-permissions"></a>Správa přístupu k Log Analytics pomocí oprávnění Azure
Pokud chcete udělit přístup k Log Analytics pomocí oprávnění Azure, postupujte podle kroků v tématu [Použití přiřazení rolí ke správě přístupu k prostředkům předplatného Azure](../active-directory/role-based-access-control-configure.md).

Azure má pro Log Analytics dvě předdefinované role uživatele:
- Čtenář Log Analytics
- Přispěvatel Log Analytics

Členové role *Čtenář Log Analytics* můžou provádět:
- Zobrazení a prohledávání všech dat monitorování 
- Zobrazení nastavení monitorování, včetně zobrazení konfigurace diagnostiky Azure pro všechny prostředky Azure

| Typ    | Oprávnění | Popis |
| ------- | ---------- | ----------- |
| Akce | `*/read`   | Možnost zobrazit všechny prostředky a jejich konfiguraci. To zahrnuje zobrazení: <br> Stavu rozšíření virtuálního počítače <br> Konfigurace diagnostiky Azure pro prostředky <br> Všech vlastnosti a nastavení všech prostředků |
| Akce | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Možnost provádět dotazy prohledávání protokolů v2 |
| Akce | `Microsoft.OperationalInsights/workspaces/search/action` | Možnost provádět dotazy prohledávání protokolů v1 |
| Akce | `Microsoft.Support/*` | Možnost otevírat případy podpory |
|Jiný než akce | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Brání čtení klíče pracovního prostoru požadovaného pro instalaci agentů a použití rozhraní API pro shromažďování dat |


Členové role *Přispěvatel Log Analytics* můžou provádět:
- Čtení všech dat monitorování 
- Vytváření a konfigurace účtů služby Automation
- Přidání a odebrání řešení pro správu
- Čtení klíčů účtu úložiště 
- Konfigurace shromažďování protokolů ze služby Azure Storage
- Úprava nastavení monitorování pro prostředky Azure, včetně
  - Přidání rozšíření virtuálního počítače na virtuální počítače
  - Konfigurace diagnostiky Azure pro všechny prostředky Azure

> [!NOTE] 
> Možnost přidat rozšíření virtuálního počítače na virtuální počítač můžete použít k získání úplné kontroly nad virtuálním počítačem.

| Oprávnění | Popis |
| ---------- | ----------- |
| `*/read`     | Možnost zobrazit všechny prostředky a jejich konfiguraci. To zahrnuje zobrazení: <br> Stavu rozšíření virtuálního počítače <br> Konfigurace diagnostiky Azure pro prostředky <br> Všech vlastnosti a nastavení všech prostředků |
| `Microsoft.Automation/automationAccounts/*` | Možnost vytvořit a konfigurovat účty služby Azure Automation, včetně přidávání a úprav runbooků |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | Přidání, aktualizace a odebrání rozšíření virtuálního počítače, včetně rozšíření Microsoft Monitoring Agent a rozšíření Agent OMS pro Linux |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | Zobrazení klíče účtu úložiště. Požadovaný ke konfiguraci Log Analytics pro čtení protokolů z účtů služby Azure Storage |
| `Microsoft.Insights/alertRules/*` | Přidání, aktualizace a odebrání pravidel upozornění |
| `Microsoft.Insights/diagnosticSettings/*` | Přidání, aktualizace a odebrání nastavení diagnostiky pro prostředky Azure |
| `Microsoft.OperationalInsights/*` | Přidání, aktualizace a odebrání konfigurace pro pracovní prostory Log Analytics |
| `Microsoft.OperationsManagement/*` | Přidání a odebrání řešení pro správu |
| `Microsoft.Resources/deployments/*` | Vytvoření a odstranění nasazení. Požadováno pro přidávání a odebírání řešení, pracovních prostorů a účtů služby Automation |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | Vytvoření a odstranění nasazení. Požadováno pro přidávání a odebírání řešení, pracovních prostorů a účtů služby Automation |

Pokud chcete přidat uživatele do role uživatele nebo je z ní odebrat, je potřeba mít oprávnění `Microsoft.Authorization/*/Delete` a `Microsoft.Authorization/*/Write`.

Pomocí těchto rolí můžete uživatelům udělit přístup v různých oborech:
- Předplatné – Přístup ke všem pracovním prostorům v rámci předplatného
- Skupina prostředků – Přístup ke všem pracovním prostorům v rámci skupiny prostředků
- Prostředek – Přístup pouze k zadanému pracovnímu prostoru

Pomocí [vlastních rolí](../active-directory/role-based-access-control-custom-roles.md) můžete vytvářet role s konkrétními požadovanými oprávněními.

### <a name="azure-user-roles-and-log-analytics-portal-user-roles"></a>Role uživatele Azure a role uživatele portálu Log Analytics
Pokud máte k pracovnímu prostoru Log Analytics alespoň oprávnění Azure ke čtení, můžete otevřít portál Log Analytics kliknutím na úlohu **Portál OMS** při procházení pracovního prostoru Log Analytics.

Když otevřete portál Log Analytics, přejdete na používání starších uživatelských rolí Log Analytics. Pokud na portálu Log Analytics nemáte přiřazení role, služba [zkontroluje oprávnění Azure, které máte k pracovnímu prostoru](https://docs.microsoft.com/rest/api/authorization/permissions#Permissions_ListForResource).
Vaše přiřazení role na portálu Log Analytics se určuje následujícím způsobem:

| Podmínky                                                   | Přiřazená uživatelské role Log Analytics | Poznámky |
|--------------------------------------------------------------|----------------------------------|-------|
| Váš účet patří ke starší uživatelské roli Log Analytics.     | Zadaná uživatelská role Log Analytics | |
| Váš účet nepatří ke starší uživatelské roli Log Analytics. <br> Úplné oprávnění Azure k pracovnímu prostoru (oprávnění `*` <sup>1</sup>) | Správce ||
| Váš účet nepatří ke starší uživatelské roli Log Analytics. <br> Úplné oprávnění Azure k pracovnímu prostoru (oprávnění `*` <sup>1</sup>) <br> *ne akce*`Microsoft.Authorization/*/Delete` a `Microsoft.Authorization/*/Write` | Přispěvatel ||
| Váš účet nepatří ke starší uživatelské roli Log Analytics. <br> Oprávnění Azure ke čtení | Jen pro čtení ||
| Váš účet nepatří ke starší uživatelské roli Log Analytics. <br> Oprávnění Azure nejsou rozpoznána. | Jen pro čtení ||
| Pro předplatná spravovaná poskytovatelem Cloud Solution Provider (CSP) <br> Účet, pomocí kterého jste přihlášeni, je ve službě Azure Active Directory propojené s pracovním prostorem. | Správce | Obvykle zákazník poskytovatele CSP |
| Pro předplatná spravovaná poskytovatelem Cloud Solution Provider (CSP) <br> Účet, pomocí kterého jste přihlášeni, není ve službě Azure Active Directory propojené s pracovním prostorem. | Přispěvatel | Obvykle poskytovatel CSP |

<sup>1</sup> Další informace o definicích rolí najdete v tématu [Oprávnění Azure](../active-directory/role-based-access-control-custom-roles.md). Při vyhodnocování rolí není akce `*` stejná jako `Microsoft.OperationalInsights/workspaces/*`.

Ohledně webu Azure Portal je třeba pamatovat na několik věcí:

* Když se přihlásíte k portálu OMS přes stránku http://mms.microsoft.com, zobrazí se seznam **Vybrat pracovní prostor**. Tento seznam obsahuje pouze pracovní prostory, ve kterých máte uživatelskou roli Log Analytics. Když chcete zobrazit pracovní prostory, ke kterým máte přístup s předplatným Azure, bude potřeba zadat tenanta jako část adresy URL. Například `mms.microsoft.com/?tenant=contoso.com`. Identifikátor tenanta je často poslední částí e-mailové adresy, se kterou se přihlašujete.
* Pokud chcete přejít přímo na portál, ke kterému máte přístup s použitím oprávnění Azure, je třeba zadat prostředek jako část adresy URL. Tuto adresu URL můžete získat pomocí prostředí PowerShell.

  Například, `(Get-AzureRmOperationalInsightsWorkspace).PortalUrl`.

  Adresa URL vypadá takto: `https://eus.mms.microsoft.com/?tenant=contoso.com&resource=%2fsubscriptions%2faaa5159e-dcf6-890a-a702-2d2fee51c102%2fresourcegroups%2fdb-resgroup%2fproviders%2fmicrosoft.operationalinsights%2fworkspaces%2fmydemo12`

### <a name="managing-users-in-the-oms-portal"></a>Správa uživatelů v portálu OMS
Uživatele a skupiny můžete spravovat na kartě **Manage Users** (Správa uživatelů) v rámci karty **Accounts** (Účty) na stránce nastavení.   

![Správa uživatelů](./media/log-analytics-manage-access/setup-workspace-manage-users.png)


#### <a name="add-a-user-to-an-existing-workspace"></a>Přidání uživatele do existujícího pracovního prostoru
Pomocí následujícího postupu přidáte k pracovnímu prostoru uživatele nebo skupinu.

1. Na portálu OMS klikněte na dlaždici **Nastavení**.
2. Klikněte na kartu **Accounts** (Účty) a potom na kartu **Manage Users** (Správa uživatelů).
3. V oddílu **Manage Users** (Správa uživatelů) vyberte typ účtu, který chcete přidat: **Organizational Account** (Účet organizace), **Microsoft Account** (Účet Microsoft), nebo **Microsoft Support** (Podpora Microsoft).

   * Pokud vyberete možnost Microsoft Account, zadejte e-mailovou adresu uživatele účtu Microsoft.
   * Pokud vyberete možnost Organizational Account, zadejte část e-mailového aliasu nebo jména uživatele nebo skupiny a v rozevíracím seznamu se zobrazí seznam vyhovujících uživatelů a skupin. Vyberte uživatele nebo skupinu.
   * Prostřednictvím podpory Microsoftu můžete pracovníkovi podpory nebo jinému zaměstnanci Microsoftu poskytnout dočasný přístup k vašemu pracovnímu prostoru kvůli odstraňování potíží.

     > [!NOTE]
     > Nejlepšího výkonu dosáhnete, když omezíte počet skupin Active Directory přidružených k jednomu účtu OMS na tři – jednu pro správce, jednu pro přispěvatele a jednu pouze pro čtení. Použití více skupin může ovlivnit výkon služby Log Analytics.
     >
     >
4. Vyberte typ uživatele nebo skupiny pro přidání: **Správce**, **Přispěvatel** nebo **Jen pro čtení**.  
5. Klikněte na tlačítko **Add** (Přidat).

   Pokud přidáváte účet Microsoft, bude pozvánka k připojení k danému pracovnímu prostoru odeslána na zadaný e-mail. Až uživatel provede postup připojení k OMS popsaný v e-mailové pozvánce, bude mít přístup k tomuto pracovnímu prostoru.
   Pokud přidáváte účet organizace, uživatel bude mít přístup ke službě Log Analysis okamžitě.  

#### <a name="edit-an-existing-user-type"></a>Úprava typu existujícího uživatele
Můžete změnit roli účtu pro uživatele přidruženého k účtu OMS. Máte následující možnosti rolí:

* *Správce*: může spravovat uživatele, zobrazovat a reagovat na všechny výstrahy a přidávat a odebírat servery.
* *Přispěvatel*: může zobrazovat a reagovat na všechny výstrahy a přidávat a odebírat servery.
* *Jen pro čtení*: takto označení uživatelé nemůžou:

  1. Přidávat a odebírat řešení. Galerie řešení je skrytá.
  2. Přidávat, upravovat nebo odebírat dlaždice na řídicím panelu **My Dashboard** (Můj řídicí panel).
  3. Zobrazit stránky **Settings** (Nastavení). Tyto stránky jsou skryté.
  4. V zobrazení hledání jsou skryté konfigurace Power BI, uložená hledání a úlohy výstrah.

#### <a name="to-edit-an-account"></a>Postup úpravy účtu
1. Na portálu OMS klikněte na dlaždici **Nastavení**.
2. Klikněte na kartu **Accounts** (Účty) a potom na kartu **Manage Users** (Správa uživatelů).
3. Vyberte roli pro uživatele, kterého chcete upravit.
4. V potvrzovacím dialogovém okně klikněte na **Yes**.

### <a name="remove-a-user-from-a-workspace"></a>Odebrání uživatele z pracovního prostoru
Pomocí následujícího postupu můžete odebrat uživatele z pracovního prostoru. Odebrání uživatel nezavře pracovní prostor. Místo toho se jen odebere přidružení mezi pracovním prostorem a uživatelem. Pokud je uživateli přidruženo několik pracovních prostorů, tento uživatel se pořád bude moci přihlašovat do OMS a zobrazit své ostatní pracovní prostory.

1. Na portálu OMS klikněte na dlaždici **Nastavení**.
2. Klikněte na kartu **Accounts** (Účty) a potom na kartu **Manage Users** (Správa uživatelů).
3. Klikněte na možnost **Remove** (Odebrat) vedle jména uživatele, kterého chcete odebrat.
4. V potvrzovacím dialogovém okně klikněte na **Yes**.

### <a name="add-a-group-to-an-existing-workspace"></a>Přidání skupiny do existujícího pracovního prostoru
1. Postupujte podle kroků 1–4 v předchozí části „Přidání uživatele do existujícího pracovního prostoru“.
2. V části **Choose User/Group** (Vyberte uživatele/skupinu) vyberte **Group** (Skupina).  
   ![Přidání skupiny do existujícího pracovního prostoru](./media/log-analytics-manage-access/add-group.png)
3. Zadejte zobrazovaný název nebo e-mailovou adresu pro skupinu, kterou chcete přidat.
4. Vyberte skupinu ze seznamu výsledků a klikněte na **Add** (Přidat).

## <a name="link-an-existing-workspace-to-an-azure-subscription"></a>Připojení existujícího pracovního prostoru k předplatnému Azure
Všechny pracovní prostory vytvořené po 26. září 2016 musí být propojené s předplatným Azure v okamžiku vytvoření. Pracovní prostory vytvořené před tímto datem je potřeba propojit s předplatným při přihlášení. Když vytváříte pracovní prostor z webu Azure Portal nebo propojujete pracovní prostor s předplatným Azure, služba Azure Active Directory se propojí jako účet vaší organizace.

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-oms-portal"></a>Propojení pracovního prostoru s předplatným Azure v portálu OMS

- Když se přihlásíte k portálu OMS, budete vyzváni k výběru předplatného Azure. Vyberte předplatné, které chcete propojit s pracovním prostorem a potom klikněte na **Propojit**.  
    ![propojení s předplatným Azure](./media/log-analytics-manage-access/required-link.png)

    > [!IMPORTANT]
    > K propojení s pracovním prostorem musí váš účet Azure už mít k tomuto pracovnímu prostoru přístup.  Jinými slovy – účet, který používáte pro přístup k webu Azure Portal, musí být **stejný** jako účet, který používáte pro přístup k pracovnímu prostoru. Pokud ne, přečtěte si část [Přidání uživatele do existujícího pracovního prostoru](#add-a-user-to-an-existing-workspace).

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-azure-portal"></a>Propojení pracovního prostoru s předplatným Azure na webu Azure Portal
1. Přihlaste se k webu [Azure Portal](http://portal.azure.com).
2. Přejděte na **Log Analytics** a vyberte tuto možnost.
3. Uvidíte svůj seznam existujících pracovních prostorů. Klikněte na tlačítko **Add** (Přidat).  
   ![seznam pracovních prostorů](./media/log-analytics-manage-access/manage-access-link-azure01.png)
4. V oddílu **OMS Workspace** klikněte na možnost **Or link existing** (Nebo propojit existující).  
   ![propojení existujícího](./media/log-analytics-manage-access/manage-access-link-azure02.png)
5. Klikněte na **Configure required settings** (Konfigurovat požadovaná nastavení).  
   ![konfigurace požadovaných nastavení](./media/log-analytics-manage-access/manage-access-link-azure03.png)
6. Uvidíte seznam pracovních prostorů, které ještě nejsou propojené s vaším účtem Azure. Vyberte pracovní prostor.  
   ![Výběr pracovního prostoru](./media/log-analytics-manage-access/manage-access-link-azure04.png)
7. Je-li to třeba, můžete změnit hodnoty následujících položek:
   * Předplatné
   * Skupina prostředků
   * Umístění
   * Cenová úroveň  
     ![Změna hodnot](./media/log-analytics-manage-access/manage-access-link-azure05.png)
8. Klikněte na **OK**. Pracovní prostor je teď propojený s vaším účtem Azure.

> [!NOTE]
> Pokud nevidíte pracovní prostor, s kterým chcete účet propojit, znamená to, že vaše předplatné Azure nemá přístup k pracovnímu prostoru, který jste vytvořili na portálu OMS.  Informace o udělení přístupu k tomuto účtu z portálu OMS najdete v části [Přidání uživatele do existujícího pracovního prostoru](#add-a-user-to-an-existing-workspace).
>
>

## <a name="upgrade-a-workspace-to-a-paid-plan"></a>Upgrade pracovního prostoru na placený tarif
Ve službě OMS existují tři tarify pracovního prostoru: **Free**, **Standalone** a **Premium**.  Pokud používáte tarif *Free*, existuje limit 500 MB pro odesílání dat do služby Log Analytics.  Pokud toto množství překročíte, budete muset přejít na placený tarif, aby se vám neshromažďovala data nad tento limit. Svůj tarif můžete kdykoli změnit.  Informace o cenách OMS najdete na stránce [podrobností o cenách](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-pricing).

### <a name="using-entitlements-from-an-oms-subscription"></a>Používání nároků z předplatného OMS
Pokud chcete používat nároky z nákupu OMS E1, OMS E2 OMS nebo doplňku OMS pro System Center, zvolte tarif *OMS* služby OMS Log Analytics.

Když si koupíte předplatné OMS, nároky se přidají do vaší smlouvy Enterprise. Jakékoli předplatné vytvořené v rámci této smlouvy může tento nárok uplatnit. Všechny pracovní prostory v těchto předplatných uplatňují nárok na OMS.

Pokud se chcete ujistit, že váš pracovní prostor využívá nárok plynoucí z předplatného OMS, proveďte následující:

1. Vytvořte pracovní prostor v předplatném Azure, které je součástí smlouvy Enterprise obsahující předplatné OMS.
2. Vyberte pro pracovní prostor tarif *OMS*.

> [!NOTE]
> Pokud jste pracovní prostor vytvořili před 26. zářím 2016 a cenový tarif služby Log Analytics je *Premium*, bude tento pracovní prostor uplatňovat nárok z doplňku OMS pro System Center. Své nároky můžete využít také tak, že přejdete na cenovou úroveň *OMS*.
>
>

Nároky na předplatné OMS nejsou viditelné na Azure nebo na portálu OMS. Tento nárok uvidíte jen na webu Enterprise Portal.  

Pokud potřebujete změnit předplatné Azure, se kterým je pracovní prostor propojený, můžete použít rutinu prostředí Azure PowerShell [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx).

### <a name="using-azure-commitment-from-an-enterprise-agreement"></a>Využití závazků Azure ze smlouvy Enterprise
Pokud nemáte předplatné OMS, platíte za každou součást OMS zvlášť a využití se zobrazí na faktuře Azure.

Pokud jsou vaše předplatná Azure propojena se smlouvou Enterprise s finančním závazkem, využití služby Log Analytics se bude automaticky strhávat ze zbývajícího finančního závazku.

Pokud potřebujete změnit předplatné Azure, se kterým je pracovní prostor propojený, můžete použít rutinu prostředí Azure PowerShell [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx).  

### <a name="change-a-workspace-to-a-paid-pricing-tier-in-the-azure-portal"></a>Změna pracovního prostoru na placenou cenovou úroveň na webu Azure Portal
1. Přihlaste se k webu [Azure Portal](http://portal.azure.com).
2. Přejděte na **Log Analytics** a vyberte tuto možnost.
3. Uvidíte svůj seznam existujících pracovních prostorů. Vyberte pracovní prostor.  
4. V okně pracovního prostoru v části **Obecné** klikněte na **Cenová úroveň**.  
5. V části **Cenová úroveň** vyberte cenovou úroveň a klikněte na **Vybrat**.  
    ![výběr tarifu](./media/log-analytics-manage-access/manage-access-change-plan03.png)
6. Po aktualizaci zobrazení na webu Azure Portal uvidíte položku **Cenová úroveň** aktualizovanou na vybranou úroveň.  
    ![aktualizovaný plán](./media/log-analytics-manage-access/manage-access-change-plan04.png)

> [!NOTE]
> Pokud je váš pracovní prostor propojený s účtem Automation, musíte před tím, než budete moci vybrat cenovou úroveň *Standalone (za GB)*, odstranit všechna řešení **Automation and Control** a zrušit propojení s účtem Automation. V okně pracovního prostoru v části **Obecné** klikněte na **Řešení**. Zobrazí se řešení a můžete je odstranit. Propojení s účtem Automation zrušíte kliknutím na název účtu Automation v okně **Cenová úroveň**.
>
>

### <a name="change-a-workspace-to-a-paid-pricing-tier-in-the-oms-portal"></a>Změna pracovního prostoru na placenou cenovou úroveň na portálu OMS

Pokud chcete změnit cenovou úroveň pomocí portálu OMS, musíte mít předplatné Azure.

1. Na portálu OMS klikněte na dlaždici **Nastavení**.
2. Klikněte na kartu **Accounts** (Účty) a potom na kartu **Azure Subscription & Data Plan** (Předplatné a datový tarif Azure).
3. Klikněte na cenovou úroveň, kterou chcete použít.
4. Klikněte na **Uložit**.  
   ![Předplatné a datové tarify](./media/log-analytics-manage-access/subscription-tab.png)

Váš nový datový tarif se zobrazí pásu karet portálu OMS v horní části webové stránky.

![Pás karet OMS](./media/log-analytics-manage-access/data-plan-changed.png)


## <a name="change-how-long-log-analytics-stores-data"></a>Změna doby, po kterou služba Log Analytics ukládá data

Na cenové úrovni Free služba Log Analytics zpřístupňuje data za posledních sedm dnů.
Na cenové úrovni Standard služba Log Analytics zpřístupňuje data za posledních 30 dnů.
Na cenové úrovni Premium služba Log Analytics zpřístupňuje data za posledních 365 dnů.
Na cenových úrovních Standalone a OMS služba Log Analytics ve výchozím nastavení zpřístupňuje data za posledních 31 dnů.

Při použití cenových úrovní Standalone a OMS můžete uchovávat až 2 roky dat (730 dnů). Za data uložená déle, než je výchozích 31 dnů, se účtuje poplatek za uchování. Další informace o cenách najdete v tématu věnovaném [poplatkům za nadlimitní využití](https://azure.microsoft.com/pricing/details/log-analytics/).

Informace o změně doby uchovávání dat najdete v tématu [Správa nákladů pomocí řízení objemu dat a jejich uchovávání ve službě Log Analytics](log-analytics-manage-cost-storage.md).

## <a name="change-an-azure-active-directory-organization-for-a-workspace"></a>Změna organizace Azure Active Directory pracovního prostoru

Můžete změnit organizaci Azure Active Directory pracovního prostoru. Změna organizace Azure Active Directory vám umožní přidávat uživatele a skupiny z tohoto adresáře do pracovního prostoru.

### <a name="to-change-the-azure-active-directory-organization-for-a-workspace"></a>Změna organizace Azure Active Directory pracovního prostoru

1. Na stránce Nastavení na portálu OMS klikněte na **Účty** a pak na kartu **Spravovat uživatele**.  
2. Zkontrolujte informace o účtech organizace a klikněte na **Změnit organizaci**.  
    ![změna organizace](./media/log-analytics-manage-access/manage-access-add-adorg01.png)
3. Zadejte informace o identitě správce vaší domény Azure Active Directory. Následně se zobrazí potvrzení o tom, že je váš pracovní prostor propojený s doménou Azure Active Directory.  
    ![Potvrzení o propojení pracovního prostoru](./media/log-analytics-manage-access/manage-access-add-adorg02.png)


## <a name="delete-a-log-analytics-workspace"></a>Odstranění pracovního prostoru Log Analytics
Když odstraníte pracovní prostor Log Analytics, odstraní se všechna data související s vaším pracovním prostorem ze služby Log Analytics během 30 dní.

Pokud jste správce a k pracovnímu prostoru bylo přidruženo víc uživatelů, přidružení těchto uživatelů s pracovním prostorem se přeruší. Pokud byli tito uživatelé přidruženi s jinými pracovními prostory, můžou pokračovat v používání služby Log Analytics s těmito prostory. Pokud ale s jinými pracovními prostory přidružení nejsou, budou muset pro další používání služby vytvořit pracovní prostor. Informace o odstranění pracovního prostoru najdete v tématu [Odstranění pracovního prostoru Azure Log Analytics](log-analytics-manage-del-workspace.md)

## <a name="next-steps"></a>Další kroky
* V článku [Shromažďování dat z počítačů v prostředí se službou Log Analytics](log-analytics-concept-hybrid.md) najdete informace o shromažďování dat z počítačů v datacentru nebo jiném cloudovém prostředí.
* V článku [Shromažďování dat o virtuálních počítačích Azure](log-analytics-quick-collect-azurevm.md) najdete informace o konfiguraci shromažďování dat z virtuálních počítačů Azure.  
* Článek [Přidání řešení Log Analytics z galerie řešení](log-analytics-add-solutions.md) popisuje přidání funkcí a shromažďování dat.

