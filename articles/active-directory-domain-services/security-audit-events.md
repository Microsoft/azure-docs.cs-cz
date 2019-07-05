---
title: Povolit auditování zabezpečení služby Azure Active Directory Domain Services | Dokumentace Microsoftu
description: Povolit auditování zabezpečení služby Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 662362c3-1a5e-4e94-ae09-8e4254443697
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: iainfou
ms.openlocfilehash: 3105296b3c670d3d44789c93878fa1fc6076973b
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/04/2019
ms.locfileid: "67566492"
---
# <a name="enable-security-audits-for-azure-ad-domain-services-preview"></a>Povolit auditování zabezpečení služby Azure AD Domain Services (Preview)
Azure AD Domain Service zabezpečení auditování umožňuje zákazníkům používat portál Azure AD Domain Services pro události auditu zabezpečení datového proudu k cílovým prostředkům. Prostředky, které můžou přijímat tyto události patří Azure Storage, pracovních prostorů Azure Log Analytics nebo centra událostí Azure. Služba Azure AD Domain krátce po povolení auditování událostí zabezpečení, odešle všechny auditované události pro vybranou kategorii na cílový prostředek. Události auditu zabezpečení zákazníkům umožnit, aby archivovat auditované události do služby Azure storage. Zákazníci navíc můžete streamování událostí do zabezpečení informací a události software pro správu (SIEM) (nebo ekvivalentní) pomocí služby event hubs nebo provádět vlastní analýzy a přehledy s využitím Azure Log Analytics z portálu Azure portal. 

> [!IMPORTANT]
> Auditování zabezpečení v Azure AD Domain Services je dostupná jenom na instance založené na Azure Resource Manageru pro službu Azure AD Domain Services.
>
>

## <a name="auditing-event-categories"></a>Kategorie auditování událostí
Auditování zabezpečení v Azure AD Domain Services v souladu s tradiční auditování, která se dodává pro řadiče domény Active Directory Domain Services. Opětovné použití existujícího vzory auditu zajišťuje, že se že stejnou logiku lze při analýze událostí. Auditování zabezpečení v Azure AD Domain Services obsahuje následující kategorie události.

| Název kategorie auditu | Popis |
|:---|:---|
| Přihlášení k účtu|Auditování pokusů o ověření dat účtu v řadiči domény nebo na místní zabezpečení Správce účtů (SAM).</p>Přihlášení a odhlášení nastavení zásad a události sledovat pokusy o přístup ke konkrétním počítači. Nastavení a události v této kategorii se zaměřují na databázi účtů, který se používá. Tato kategorie obsahuje následující podkategorie:<ul><li>[Auditovat ověřování pověření](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-credential-validation)</li><li>[Auditovat ověřovací službu protokolu Kerberos](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-authentication-service)</li><li>[Auditovat operace lístku služby Kerberos](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-service-ticket-operations)</li><li>[Auditovat jiné události přihlášení/odhlášení](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li></ul>|
| Správa účtů|Audity se změní na účty uživatelů a počítačů a skupiny. Tato kategorie obsahuje následující podkategorie:<ul><li>[Auditovat správu skupin aplikací](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-group-management)</li><li>[Auditovat správu účtů počítače](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-computer-account-management)</li><li>[Auditovat správu skupin distribuce](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-distribution-group-management)</li><li>[Auditovat správu jiných účtů](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-account-management-events)</li><li>[Auditovat správu skupiny zabezpečení](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-group-management)</li><li>[Auditovat správu účtů uživatelů](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-account-management)</li></ul>|
| Podrobnosti o sledování|Auditování aktivity jednotlivých aplikací a uživatelů na tomto počítači a pochopit, jak počítač používá. Tato kategorie obsahuje následující podkategorie:<ul><li>[Auditovat aktivitu DPAPI](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-dpapi-activity)</li><li>[Auditovat aktivitu PNP](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-pnp-activity)</li><li>[Auditovat vytvoření procesu](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-creation)</li><li>[Auditovat ukončení procesu](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-termination)</li><li>[Auditovat události RPC](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-rpc-events)</li></ul>|
| Přístup adresářových služeb|Auditování pokusů o přístup a úpravy objektů v Active Directory Domain Services (AD DS). Audit, tyto události jsou protokolovány jenom na řadičích domény. Tato kategorie obsahuje následující podkategorie:<ul><li>[Auditovat podrobnou replikaci adresářové služby](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-directory-service-replication)</li><li>[Auditovat přístup k adresářové službě](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-access)</li><li>[Auditovat změny adresářové služby](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-changes)</li><li>[Auditovat replikaci adresářové služby](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-replication)</li></ul>|
| Přihlášení a odhlášení|Auditování pokusů o přihlášení k počítači se interaktivně nebo přes síť. Tyto události jsou užitečné pro sledování činnosti uživatelů a identifikovat potenciální útoky na síťové prostředky. Tato kategorie obsahuje následující podkategorie:<ul><li>[Auditovat uzamčení účtu](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-account-lockout)</li><li>[Auditovat deklarace identity uživatele nebo zařízení](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-device-claims)</li><li>[Auditovat rozšířený režim protokolu IPsec](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-extended-mode)</li><li>[Členství ve skupině auditu](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-group-membership)</li><li>[Auditovat hlavní režim protokolu IPsec](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-main-mode)</li><li>[Auditovat rychlý režim protokolu IPsec](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-quick-mode)</li><li>[Auditovat odhlášení](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logoff)</li><li>[Auditovat přihlášení](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logon)</li><li>[Audit Network Policy Server](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-network-policy-server)</li><li>[Auditovat jiné události přihlášení/odhlášení](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li><li>[Auditovat zvláštní přihlášení](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-special-logon)</li></ul>|
|Přístup k objektům| Auditování pokusů o přístup k určité objekty nebo typy objektů v síti nebo na počítači. Tato kategorie obsahuje následující podkategorie:<ul><li>[Auditovat generování aplikacemi](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-generated)</li><li>[Auditovat certifikační služby](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-certification-services)</li><li>[Auditovat podrobnou sdílenou složku](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-file-share)</li><li>[Auditovat sdílenou složku](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-share)</li><li>[Auditovat systém souborů](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-system)</li><li>[Auditovat připojení architektury Filtering Platform](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection)</li><li>[Auditovat zahození paketu filtrování platformy](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-packet-drop)</li><li>[Auditovat manipulaci s popisovačem](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-handle-manipulation)</li><li>[Auditovat objekt jádra](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kernel-object)</li><li>[Auditovat jiné události přístupu k objektu](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-object-access-events)</li><li>[Auditovat registr](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-registry)</li><li>[Audit vyměnitelného úložiště](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-removable-storage)</li><li>[Audit SAM](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sam)</li><li>[Audit přípravy centrální zásady přístupu](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-central-access-policy-staging)</li></ul>|
|Změna zásad|Audity se změní na důležité zabezpečení Zásady místního systému nebo síti. Zásady jsou obvykle určeny správcům pomoct zabezpečených síťových prostředků. Monitorování změn nebo pokusy o změnu tyto zásady se dá důležitou součástí správy zabezpečení sítě. Tato kategorie obsahuje následující podkategorie:<ul><li>[Auditovat změnu zásad auditování](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-audit-policy-change)</li><li>[Auditovat změnu zásad ověřování](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authentication-policy-change)</li><li>[Auditovat změnu zásad autorizace](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authorization-policy-change)</li><li>[Auditovat změnu zásad filtrování platformy](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-policy-change)</li><li>[Auditovat změnu zásad úrovně pravidla MPSSVC](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-mpssvc-rule-level-policy-change)</li><li>[Auditovat jiné změny zásad](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-policy-change-events)</li></ul>|
|Použití oprávnění| Audituje používání určitá oprávnění na jeden nebo více systémů. Tato kategorie obsahuje následující podkategorie:<ul><li>[Auditovat použití méně citlivá oprávnění](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-non-sensitive-privilege-use)</li><li>[Auditovat použití citlivých oprávnění](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)</li><li>[Auditovat události použití jiných oprávnění](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-privilege-use-events)</li></ul>|
|Systémový| Audity změny úrovni systému do počítače nejsou zahrnuty v ostatních kategoriích a které mají potenciální vliv na zabezpečení. Tato kategorie obsahuje následující podkategorie:<ul><li>[Auditovat ovladač IPsec](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-driver)</li><li>[Auditovat jiné systémové události](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-system-events)</li><li>[Auditovat změnu stavu zabezpečení](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-state-change)</li><li>[Auditovat rozšíření systému zabezpečení](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-system-extension)</li><li>[Auditovat integritu systému](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-system-integrity)</li></ul>|

## <a name="event-ids-per-category"></a>ID událostí podle jednotlivých kategorií
 Následující ID událostí auditování zabezpečení v Azure AD Domain Services zaznamenává, když konkrétní akce aktivuje událost auditovatelných.

| Název kategorie události | ID události |
|:---|:---|
|Zabezpečení přihlašovací účet|4767, 4774, 4775, 4776, 4777|
|Správa zabezpečení účtů|4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4730, 4731, 4732, 4733, 4734, 4735, 4737, 4738, 4740, 4741, 4742, 4743, 4754, 4755, 4756, 4757, 4758, 4764, 4765, 4766, 4780, 4781, 4782, 4793, 4798, 4799, 5376, 5377|
|Podrobnosti o sledování zabezpečení|Žádný|
|Přístup k zabezpečení|5136, 5137, 5138, 5139, 5141|
|Zabezpečení přihlášení a odhlášení|4624, 4625, 4634, 4647, 4648, 4672, 4675, 4964|
|Objekt zabezpečení přístupu|Žádný|
|Zásady změn zabezpečení|4670, 4703, 4704, 4705, 4706, 4707, 4713, 4715, 4716, 4717, 4718, 4719, 4739, 4864, 4865, 4866, 4867, 4904, 4906, 4911, 4912|
|Zabezpečení pomocí oprávnění|4985|
|Zabezpečení systému|4612, 4621|

## <a name="enable-security-audit-events"></a>Povolení auditování událostí zabezpečení
Následující pokyny vám umožní úspěšně se přihlásit k odběru události auditu zabezpečení Azure AD Domain Services.

> [!IMPORTANT]
> Audity zabezpečení Azure AD Domain Services nejsou zpětně. Není možné načíst události z minulosti nebo přehrát události z minulosti. Službu lze provést odeslání pouze události, ke kterým dochází po jeho povolení.
>

### <a name="choose-the-target-resource"></a>Zvolte cílový prostředek
Můžete použít libovolnou kombinaci služby Azure Storage, Azure Event Hubs nebo pracovních prostorů Azure Log Analytics jako cílový prostředek pro bezpečnostní audity. Vezměte v úvahu následující tabulce najdete doporučené zdroje pro případy použití.

> [!IMPORTANT]
> Je potřeba vytvořit cílového prostředku před povolením audity zabezpečení Azure AD Domain Services.
>

| Cílový prostředek | Scénář |
|:---|:---|
|Azure Storage|Zvažte použití tento cíl, když je vaše primární potřebují k uložení událostí auditu zabezpečení pro archivační účely. Další cíle lze použít pro archivační účely; ale tyto cíle poskytují možnosti nad rámec primární potřeby archivace. Chcete-li vytvořit účet služby Azure Storage, postupujte podle [vytvoření účtu úložiště.](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal#create-a-storage-account-1)|
|Azure Event Hubs|Zvážit použití tento cíl, když je vaše primární potřebují sdílet události auditu zabezpečení s další software, jako je například software pro analýzu dat nebo software pro informace & událostí (SIEM) správu zabezpečení. Při vytváření centra událostí, postupujte podle [rychlý start: Vytvoření centra událostí pomocí webu Azure portal.](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)|
|Pracovní prostor Azure Log Analytics|Zvažte použití tento cíl, když je vaše primární potřebují k analýze a přímo zkontrolovat audity zabezpečení na webu Azure Portal.  Chcete-li vytvořit pracovní prostor Log Analytics, postupujte podle [vytvořit pracovní prostor Log Analytics na portálu Azure portal.](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)|

## <a name="using-the-azure-portal-to-enable-security-audit-events"></a>Pomocí webu Azure portal k povolení auditování událostí zabezpečení 
1. Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.  Na webu Azure Portal klikněte na všechny služby. V seznamu prostředků zadejte **domény**. Seznam se průběžně filtruje podle zadávaného textu. Klikněte na tlačítko **Azure AD Domain Services**.
2. Klikněte na instanci Azure AD Domain Services ze seznamu.
3. Klikněte na tlačítko **nastavení diagnostiky (preview)** ze seznamu akcí na levé straně.</p>
![Akce nastavení diagnostiky](./media/security-audit-events/diagnostic-settings-action.png)
4. Zadejte název konfigurace diagnostiky (**auditování aadds** jako příklad).</p>
![Stránka nastavení diagnostiky](./media/security-audit-events/diagnostic-settings-page.png)
5. Zaškrtněte příslušné políčko vedle cílové prostředky, které budete používat pro události auditu zabezpečení.
    > [!NOTE]
    > Nelze vytvořit cílové prostředky z této stránky.
    >
    
    **Azure storage:**</p>
    Vyberte **archivovat do účtu úložiště**. Klikněte na **Konfigurovat**. Vyberte **předplatné** a **účtu úložiště** chcete použít pro archivaci auditování událostí zabezpečení. Klikněte na **OK**.</p>
    
    ![Nastavení úložiště diagnostiky](./media/security-audit-events/diag-settings-storage.png)
    
    **Služba Azure event hubs:**</p>
    Vyberte **Stream do centra událostí**. Klikněte na **Konfigurovat**. V **stránka centra událostí vyberte**, vyberte **předplatné** použitý k vytvoření centra událostí. V dalším kroku vyberte **obor názvů centra událostí**, **název centra událostí**, a **název zásady centra událostí**. Klikněte na **OK**.</p>
    ![nastavení centra diagnostických událostí](./media/security-audit-events/diag-settings-eventhub.png)
    
    **Azure Log analytické pracovní prostory:**</p>
    Vyberte **odesílat do Log Analytics**. Vyberte **předplatné** a **pracovní prostor Log Analytics** používá k ukládání událostí auditu zabezpečení.</p>
    ![nastavení diagnostiky pracovního prostoru](./media/security-audit-events/diag-settings-log-analytics.png)

6. Vyberte příslušné kategorie protokolu, které chcete zahrnout pro konkrétní cílový prostředek. Pokud používáte účty úložiště, můžete nakonfigurovat zásady uchovávání informací.

    > [!NOTE]
    > Můžete vybrat kategorie různých protokolů pro každý cílový prostředek v rámci jediné konfiguraci. To umožňuje zvolit, které protokoly kategorií, které chcete sledovat pro Log Analytics a které protokoly kategorie chcete archivovat.
    >

7. Klikněte na tlačítko **Uložit** potvrďte provedené změny. Cílové prostředky budou přijímat události auditu zabezpečení Azure AD Domain Services krátce po uložte konfiguraci.

## <a name="using-azure-powershell-to-enable-security-audit-events"></a>Povolení auditování událostí zabezpečení pomocí prostředí Azure PowerShell
 
### <a name="prerequisites"></a>Požadavky

Postupujte podle pokynů v článku [instalace modulu Azure PowerShell a připojte se ke svému předplatnému Azure](https://docs.microsoft.com/powershell/azure/install-az-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="enable-security-audits"></a>Povolit auditování zabezpečení

1. Ověření na Azure Resource Manageru pro příslušné tenanta a předplatné **připojit AzAccount** rutiny Azure Powershellu.
2. Vytvořte cílový prostředek pro zabezpečení událostí auditu.</p>
    **Azure storage:**</p>
    Postupujte podle [vytvořit účet úložiště](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-powershell) k vytvoření účtu úložiště.</p>
    **Služba Azure event hubs:**</p>
    Postupujte podle [rychlý start: Vytvoření centra událostí pomocí Azure Powershellu](https://docs.microsoft.com/azure/event-hubs/event-hubs-quickstart-powershell) pro vytvoření centra událostí. Můžete také použít [New-AzEventHubAuthorizationRule](https://docs.microsoft.com/powershell/module/az.eventhub/new-azeventhubauthorizationrule?view=azps-2.3.2) rutiny Azure Powershellu vytvořit autorizační pravidlo povolit oprávnění služby Active Directory AD Domain Services do centra událostí **obor názvů**. Musí obsahovat autorizační pravidlo **spravovat**, **naslouchání**, a **odeslat** práva.
    > [!IMPORTANT]
    > Ujistěte se, že nastavíte autorizačního pravidla pro obor názvů centra událostí a centra událostí.
       
    </p>
    
    **Azure Log analytické pracovní prostory:**</p>
    Postupujte podle [vytvořit pracovní prostor Log Analytics pomocí Azure Powershellu](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace-posh) k vytvoření pracovního prostoru.
3. Získejte ID prostředku pro vaši instanci Azure AD Domain Services. V konzole Windows Powershellu otevřené, ověření zadejte následující příkaz. Použití **$aadds. ResourceId** proměnné jako parametr pro ID prostředku Azure AD Domain Services pro budoucí rutiny.
    ```powershell
    $aadds = Get-AzResource -name aaddsDomainName
    ``` 
4. Použít **Set-AzDiagnosticSetting** rutiny můžete nakonfigurovat nastavení diagnostiky Azure pro účely cílový prostředek události auditu zabezpečení Azure AD Domain Services. V příkladech níže $aadds proměnné. ResourceId představuje ID prostředku vaší instance služby Azure AD Domain Services (viz krok 3).</p>
    **Azure storage:**
    ```powershell
    Set-AzDiagnosticSetting `
    -ResourceId $aadds.ResourceId` 
    -StorageAccountId storageAccountId `
    -Enabled $true
    ```
    Nahraďte *storageAccountId* ID účtu úložiště</p>
    
    **Služba Azure event hubs:**
    ```powershell
    Set-AzDiagnosticSetting -ResourceId $aadds.ResourceId ` 
    -EventHubName eventHubName `
    -EventHubAuthorizationRuleId eventHubRuleId `
    -Enabled $true
    ```
    Nahraďte *eventHubName* s názvem vašeho centra událostí. Nahraďte *eventHubRuleId* identifikátorem autorizační pravidlo jste předtím vytvořili.</p>
    
    **Azure Log analytické pracovní prostory:**
    ```powershell
    Set-AzureRmDiagnosticSetting -ResourceId $aadds.ResourceId ` 
    -WorkspaceID workspaceId `
    -Enabled $true
    ```
    Nahraďte *ID pracovního prostoru* s ID pracovního prostoru Log Analytics, kterou jste vytvořili dřív. 

## <a name="view-security-audit-events-using-azure-monitor"></a>Zobrazit události auditu zabezpečení pomocí Azure monitoru
Analytické pracovní prostory log umožňují zobrazit a analyzovat události auditu zabezpečení pomocí Azure monitoru a Kusto dotazovací jazyk. Dotazovací jazyk je určen pro použití jen pro čtení, která se může pochlubit analytické možnosti napájení se syntaxí snadné čtení.
Tady jsou některé materiály, které pomohou vám začít s Kusto dotazovací jazyky.
* [Dokumentace ke službě Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)
* [Začínáme se službou Log Analytics ve službě Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)
* [Začínáme s dotazy protokolu ve službě Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)
* [Vytváření a sdílení řídicích panelů s daty Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-logs-dashboards)

## <a name="sample-queries"></a>Ukázkové dotazy

### <a name="sample-query-1"></a>Ukázkový dotaz 1
Všechny účtu uzamčení události za posledních sedm dní.
```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

### <a name="sample-query-2"></a>Ukázkový dotaz 2
Všechny účtu uzamčení události (4740) mezi 26. června 2019 v 9: 00 a 1. července 2019 půlnoc, seřazeno vzestupně podle data a času.
```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2019-06-26 09:00) and TimeGenerated <= datetime(2019-07-01) 
| where OperationName has "4740"
| sort by TimeGenerated asc
```

### <a name="sample-query-3"></a>Ukázkový dotaz 3
Účet protokolu událostí před 7 dny (od tohoto okamžiku) pro účet uživatele s názvem.
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-4"></a>Ukázkový dotaz 4
Události přihlášení k účtu sedm dnů od teď pro účet uživatele, který se pokusil přihlásit pomocí nesprávného hesla (0xC0000006a) s názvem.
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc000006a" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-5"></a>Ukázkový dotaz 5
Události přihlášení k účtu sedm dnů od teď pro účet uživatele, který se pokusil přihlásit, zatímco účet byl uzamčen (0xC0000234) s názvem.
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-6"></a>Ukázkový dotaz 6
Počet událostí přihlášení k účtu sedm dní před z nyní u všech přihlášení pokusy, ke kterým došlo u všech uzamčení uživatele.
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| summarize count()
```

## <a name="related-content"></a>Související obsah
* [Přehled](https://docs.microsoft.com/azure/kusto/query/) z Kusto dotazovací jazyk.
* [Kurz Kusto](https://docs.microsoft.com/azure/kusto/query/tutorial) se můžete seznámit s základy dotazů.
* [Ukázkové dotazy](https://docs.microsoft.com/azure/kusto/query/samples) umožňující další nové způsoby, jak vaše data.
* Kusto [osvědčené postupy](https://docs.microsoft.com/azure/kusto/query/best-practices) – optimalizaci dotazů k dosažení úspěchu.














 
