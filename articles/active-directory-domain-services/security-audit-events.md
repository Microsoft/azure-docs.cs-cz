---
title: Povolit audity zabezpečení pro Azure AD Domain Services | Microsoft Docs
description: Naučte se, jak povolit audity zabezpečení pro centralizaci protokolování událostí pro analýzu a výstrahy v Azure AD Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 662362c3-1a5e-4e94-ae09-8e4254443697
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: 98e64f1ea1196b6fd773514d4dad5b6e178a6898
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88722530"
---
# <a name="enable-security-audits-for-azure-active-directory-domain-services"></a>Povolit audity zabezpečení pro Azure Active Directory Domain Services

Audity zabezpečení služby Azure Active Directory Domain Services (Azure služba AD DS) umožňují zacílit události zabezpečení Azure streamu na cílené prostředky. Mezi tyto prostředky patří Azure Storage, pracovní prostory Azure Log Analytics nebo centrum událostí Azure. Po povolení událostí auditu zabezpečení pošle Azure služba AD DS všechny auditované události pro vybranou kategorii do cílového prostředku.

Události můžete archivovat do služby Azure Storage a streamovat události do softwaru SIEM (Security Information and Event Management) (nebo ekvivalentní) pomocí Azure Event Hubs nebo vlastní analýzy a používat pracovní prostory Azure Log Analytics z Azure Portal.

> [!IMPORTANT]
> Audity zabezpečení Azure služba AD DS jsou dostupné jenom pro spravované domény založené na Azure Resource Manager. Informace o migraci najdete v tématu [migrace Azure služba AD DS z modelu klasických virtuálních sítí na správce prostředků][migrate-azure-adds].

## <a name="security-audit-destinations"></a>Cíle auditu zabezpečení

Jako cílový prostředek pro audity zabezpečení Azure služba AD DS můžete použít Azure Storage, Azure Event Hubs nebo Azure Log Analytics pracovní prostory. Tyto cíle lze kombinovat. Můžete například použít Azure Storage pro archivaci událostí auditu zabezpečení, ale pracovní prostor Azure Log Analytics k analýze a hlášení informací v krátkém období.

Následující tabulka obsahuje přehled scénářů pro každý typ cílového prostředku.

> [!IMPORTANT]
> Než povolíte audity zabezpečení Azure služba AD DS, je potřeba vytvořit cílový prostředek. Tyto prostředky můžete vytvořit pomocí Azure Portal, Azure PowerShell nebo rozhraní příkazového řádku Azure.

| Cílový prostředek | Scénář |
|:---|:---|
|Azure Storage| Tento cíl by měl být použit v případě, že vaše primární potřeba ukládá události auditu zabezpečení pro účely archivace. Pro účely archivace lze použít jiné cíle, ale tyto cíle poskytují možnosti nad rámec hlavní potřeby archivace. <br /><br />Než povolíte události auditu zabezpečení Azure služba AD DS, [vytvořte nejdřív Azure Storage účet](../storage/common/storage-account-create.md).|
|Azure Event Hubs| Tento cíl by měl být použit v případě, že je potřeba, aby se události auditu zabezpečení sdílely pomocí dalšího softwaru, jako je například software pro analýzu dat & nebo software pro správu událostí (SIEM).<br /><br />Než povolíte události auditu zabezpečení Azure služba AD DS, [vytvořte centrum událostí pomocí Azure Portal](../event-hubs/event-hubs-create.md)|
|Pracovní prostor Azure Log Analytics| Tento cíl by měl být použit, pokud je vaše primární potřeba analyzovat a kontrolovat zabezpečené audity přímo z Azure Portal.<br /><br />Než povolíte události auditu zabezpečení Azure služba AD DS, [vytvořte v Azure Portal pracovní prostor Log Analytics.](../azure-monitor/learn/quick-create-workspace.md)|

## <a name="enable-security-audit-events-using-the-azure-portal"></a>Povolit události auditu zabezpečení pomocí Azure Portal

Pokud chcete povolit události auditu zabezpečení Azure služba AD DS pomocí Azure Portal, proveďte následující kroky.

> [!IMPORTANT]
> Audity zabezpečení Azure služba AD DS nejsou zpětně. Nemůžete načíst nebo přehrát události z minulosti. Azure služba AD DS může posílat jenom události, ke kterým dojde po povolení auditů zabezpečení.

1. Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.
1. V horní části Azure Portal vyhledejte a vyberte **Azure AD Domain Services**. Vyberte spravovanou doménu, například *aaddscontoso.com*.
1. V okně Azure služba AD DS vyberte **nastavení diagnostiky** na levé straně.
1. Ve výchozím nastavení nejsou nakonfigurované žádné diagnostiky. Začněte tím, že vyberete **Přidat nastavení diagnostiky**.

    ![Přidat nastavení diagnostiky pro Azure AD Domain Services](./media/security-audit-events/add-diagnostic-settings.png)

1. Zadejte název konfigurace diagnostiky, jako je například *aadds-audit*.

    Zaškrtněte políčko pro cíl auditu zabezpečení, který chcete. Můžete si vybrat z účtu Azure Storage, centra událostí Azure nebo pracovního prostoru Log Analytics. Tyto cílové prostředky už musí existovat ve vašem předplatném Azure. V tomto průvodci nemůžete vytvořit cílové prostředky.

    ![Povolit požadovaný cíl a typ událostí auditu, které se mají zachytit](./media/security-audit-events/diagnostic-settings-page.png)

    * **Úložiště Azure**
        * Vyberte možnost **archivovat do účtu úložiště**a pak zvolte **Konfigurovat**.
        * Vyberte **předplatné** a **účet úložiště** , který chcete použít k archivaci událostí auditu zabezpečení.
        * Až budete připraveni, klikněte na **tlačítko OK**.
    * **Centra událostí Azure**
        * Vyberte **Stream do centra událostí**a pak zvolte **Konfigurovat**.
        * Vyberte **předplatné** a **obor názvů centra událostí**. V případě potřeby také zvolte **název centra událostí** a **název zásady centra událostí**.
        * Až budete připraveni, klikněte na **tlačítko OK**.
    * **Analytické pracovní prostory pro Azure log**
        * Vyberte **odeslat Log Analytics**a pak zvolte pracovní prostor **odběr** a **Log Analytics** , který chcete použít k ukládání událostí auditu zabezpečení.

1. Vyberte kategorie protokolů, které chcete zahrnout do konkrétního cílového prostředku. Pokud odešlete události auditu na účet Azure Storage, můžete také nakonfigurovat zásady uchovávání informací, které definují počet dní uchovávání dat. Výchozí nastavení *0* zachovává všechna data a po uplynutí určité doby neotáčí události.

    V rámci jedné konfigurace můžete pro každý cílový prostředek vybrat různé kategorie protokolů. Díky této možnosti si můžete vybrat, které kategorie protokolů chcete zachovat Log Analytics a které protokoly mají být zachovány, například.

1. Až budete hotovi, vyberte **Uložit** a potvrďte provedené změny. Prostředky cíle začnou přijímat události auditu služby Azure služba AD DS Security brzy po uložení konfigurace.

## <a name="enable-security-audit-events-using-azure-powershell"></a>Povolit události auditu zabezpečení pomocí Azure PowerShell

Pokud chcete povolit události auditu zabezpečení Azure služba AD DS pomocí Azure PowerShell, proveďte následující kroky. V případě potřeby nejdřív [nainstalujte modul Azure PowerShell a připojte se k předplatnému Azure](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Audity zabezpečení Azure služba AD DS nejsou zpětně. Nemůžete načíst nebo přehrát události z minulosti. Azure služba AD DS může posílat jenom události, ke kterým dojde po povolení auditů zabezpečení.

1. K ověření předplatného Azure použijte rutinu [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) . Po zobrazení výzvy zadejte své přihlašovací údaje k účtu.

    ```azurepowershell
    Connect-AzAccount
    ```

1. Vytvořte cílový prostředek pro události auditu zabezpečení.

    * **Úložiště Azure**  -  [Vytvoření účtu úložiště pomocí Azure PowerShell](../storage/common/storage-account-create.md?tabs=azure-powershell)
    * **Centra**  -  událostí Azure [Vytvořte centrum událostí pomocí Azure PowerShell](../event-hubs/event-hubs-quickstart-powershell.md). K vytvoření autorizačního pravidla, které uděluje službě Azure služba AD DS oprávnění k *oboru názvů*centra událostí, možná budete muset použít rutinu [New-AzEventHubAuthorizationRule](/powershell/module/az.eventhub/new-azeventhubauthorizationrule) . Autorizační pravidlo musí zahrnovat práva **Spravovat**, **naslouchat**a **odesílat** .

        > [!IMPORTANT]
        > Ujistěte se, že jste nastavili autorizační pravidlo v oboru názvů centra událostí a ne na samotném centru událostí.

    * **Analytické pracovní prostory**  -  pro Azure log [Vytvoří Log Analytics pracovní prostor s Azure PowerShell](../azure-monitor/platform/powershell-workspace-configuration.md).

1. Pomocí rutiny [Get-AzResource](/powershell/module/Az.Resources/Get-AzResource) Získejte ID prostředku pro spravovanou doménu Azure služba AD DS. Vytvořte proměnnou s názvem *$aadds. ResourceId* pro uchování hodnoty:

    ```azurepowershell
    $aadds = Get-AzResource -name aaddsDomainName
    ```

1. Nakonfigurujte nastavení diagnostiky Azure pomocí rutiny [set-AzDiagnosticSetting](/powershell/module/Az.Monitor/Set-AzDiagnosticSetting) pro použití cílového prostředku pro Azure AD Domain Services události auditu zabezpečení. V následujících příkladech proměnná *$aadds. ResourceId* se používá z předchozího kroku.

    * **Azure Storage** – nahraďte *storageAccountId* názvem vašeho účtu úložiště:

        ```powershell
        Set-AzDiagnosticSetting `
            -ResourceId $aadds.ResourceId `
            -StorageAccountId storageAccountId `
            -Enabled $true
        ```

    * **Centra událostí Azure** – nahraďte *eventHubName* názvem vašeho centra událostí a *eventHubRuleId* s ID autorizačního pravidla:

        ```powershell
        Set-AzDiagnosticSetting -ResourceId $aadds.ResourceId `
            -EventHubName eventHubName `
            -EventHubAuthorizationRuleId eventHubRuleId `
            -Enabled $true
        ```

    * **Analytické pracovní prostory Azure log** – nahraďte *ID pracovního prostoru* číslem ID pracovního prostoru Log Analytics:

        ```powershell
        Set-AzureRmDiagnosticSetting -ResourceId $aadds.ResourceId `
            -WorkspaceID workspaceId `
            -Enabled $true
        ```

## <a name="query-and-view-security-audit-events-using-azure-monitor"></a>Dotazování a zobrazení událostí auditu zabezpečení pomocí Azure Monitor

V protokolu analytické pracovní prostory můžete zobrazit a analyzovat události auditu zabezpečení pomocí Azure Monitor a jazyka dotazů Kusto. Tento dotazovací jazyk je navržený pro použití jen pro čtení, které Boasts možnosti analytického analytika se snadno čitelným syntaxí. Další informace o tom, jak začít používat jazyky dotazů Kusto, najdete v následujících článcích:

* [Dokumentace k Azure Monitor](../azure-monitor/index.yml)
* [Začínáme s Log Analytics v Azure Monitor](../azure-monitor/log-query/get-started-portal.md)
* [Začínáme s dotazy protokolů v Azure Monitoru](../azure-monitor/log-query/get-started-queries.md)
* [Vytváření a sdílení řídicích panelů s daty Log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md)

Pomocí následujících ukázkových dotazů můžete začít s analýzou událostí auditu zabezpečení z Azure služba AD DS.

### <a name="sample-query-1"></a>Vzorový dotaz 1

Zobrazit všechny události uzamčení účtu za posledních sedm dní:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

### <a name="sample-query-2"></a>Vzorový dotaz 2

Zobrazit všechny události uzamčení účtu (*4740*) od 3. června 2020 v 9:00 a od 10. června 2020, seřazené vzestupně podle data a času:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2020-06-03 09:00) and TimeGenerated <= datetime(2020-06-10)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

### <a name="sample-query-3"></a>Vzorový dotaz 3

Zobrazit události přihlášení k účtu před 7 dny (od teď) pro účet s názvem uživatel:

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-4"></a>Vzorový dotaz 4

Zobrazit události přihlášení k účtu před 7 dny pro účet s názvem uživatel, který se pokusil o přihlášení pomocí špatného hesla (*0xC0000006a*):

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc000006a" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-5"></a>Vzorový dotaz 5

Zobrazit události přihlášení k účtu pro účet s názvem uživatel, který se pokusil o přihlášení v době, kdy byl účet uzamčen (*0xC0000234*):

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-6"></a>Vzorový dotaz 6

Zobrazit počet událostí přihlášení k účtu před 7 dny od teď u všech pokusů o přihlášení, ke kterým došlo u všech zamčených uživatelů:

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| summarize count()
```

## <a name="audit-event-categories"></a>Kategorie událostí auditu

Audity zabezpečení Azure služba AD DS odpovídají tradičnímu auditu pro tradiční služba AD DS řadiče domény. V hybridních prostředích můžete znovu použít stávající vzorce auditu, aby se při analýze událostí mohla použít stejná logika. V závislosti na scénáři, který potřebujete k řešení potíží nebo k analýze, je třeba cílit na jiné kategorie událostí auditu.

K dispozici jsou následující kategorie událostí auditu:

| Název kategorie auditu | Description |
|:---|:---|
| Přihlášení k účtu|Audit se pokusí ověřit data účtu na řadiči domény nebo v místním správci zabezpečení účtů (SAM).</p>Nastavení zásad přihlášení a odhlášení a události sledují pokusy o přístup k určitému počítači. Nastavení a události v této kategorii se zaměřují na databázi účtů, která se používá. Tato kategorie obsahuje následující podkategorie:<ul><li>[Auditovat ověřování pověření](/windows/security/threat-protection/auditing/audit-credential-validation)</li><li>[Auditovat ověřovací službu protokolu Kerberos](/windows/security/threat-protection/auditing/audit-kerberos-authentication-service)</li><li>[Auditovat operace lístku služby Kerberos](/windows/security/threat-protection/auditing/audit-kerberos-service-ticket-operations)</li><li>[Auditovat jiné události přihlášení a odhlášení](/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li></ul>|
| Správa účtů|Audituje změny účtů uživatelů a počítačů a skupin. Tato kategorie obsahuje následující podkategorie:<ul><li>[Auditovat správu skupin aplikací](/windows/security/threat-protection/auditing/audit-application-group-management)</li><li>[Auditovat správu účtů počítače](/windows/security/threat-protection/auditing/audit-computer-account-management)</li><li>[Auditovat správu skupin distribuce](/windows/security/threat-protection/auditing/audit-distribution-group-management)</li><li>[Auditovat další správu účtů](/windows/security/threat-protection/auditing/audit-other-account-management-events)</li><li>[Auditovat správu skupiny zabezpečení](/windows/security/threat-protection/auditing/audit-security-group-management)</li><li>[Auditovat správu účtů uživatelů](/windows/security/threat-protection/auditing/audit-user-account-management)</li></ul>|
| Sledování podrobností|Audituje aktivity jednotlivých aplikací a uživatelů v daném počítači a pro pochopení, jak se počítač používá. Tato kategorie obsahuje následující podkategorie:<ul><li>[Auditovat aktivitu DPAPI](/windows/security/threat-protection/auditing/audit-dpapi-activity)</li><li>[Auditovat aktivitu PNP](/windows/security/threat-protection/auditing/audit-pnp-activity)</li><li>[Auditovat vytvoření procesu](/windows/security/threat-protection/auditing/audit-process-creation)</li><li>[Auditovat ukončení procesu](/windows/security/threat-protection/auditing/audit-process-termination)</li><li>[Auditovat události RPC](/windows/security/threat-protection/auditing/audit-rpc-events)</li></ul>|
| Přístup k adresářovým službám|Audit se pokouší o přístup k objektům v Active Directory Domain Services (služba AD DS) a jejich úpravě. Tyto události auditu se protokolují jenom na řadičích domény. Tato kategorie obsahuje následující podkategorie:<ul><li>[Auditovat podrobnou replikaci adresářové služby](/windows/security/threat-protection/auditing/audit-detailed-directory-service-replication)</li><li>[Auditovat přístup k adresářové službě](/windows/security/threat-protection/auditing/audit-directory-service-access)</li><li>[Auditovat změny adresářové služby](/windows/security/threat-protection/auditing/audit-directory-service-changes)</li><li>[Auditovat replikaci adresářové služby](/windows/security/threat-protection/auditing/audit-directory-service-replication)</li></ul>|
| Logon-Logoff|Audit pokusy o interaktivní přihlášení k počítači nebo přes síť. Tyto události jsou užitečné pro sledování aktivity uživatelů a identifikaci potenciálních útoků na síťové prostředky. Tato kategorie obsahuje následující podkategorie:<ul><li>[Auditovat uzamčení účtu](/windows/security/threat-protection/auditing/audit-account-lockout)</li><li>[Auditovat deklarace identity uživatelů a zařízení](/windows/security/threat-protection/auditing/audit-user-device-claims)</li><li>[Auditovat rozšířený režim protokolu IPsec](/windows/security/threat-protection/auditing/audit-ipsec-extended-mode)</li><li>[Členství ve skupině auditu](/windows/security/threat-protection/auditing/audit-group-membership)</li><li>[Auditovat hlavní režim protokolu IPsec](/windows/security/threat-protection/auditing/audit-ipsec-main-mode)</li><li>[Auditovat rychlý režim protokolu IPsec](/windows/security/threat-protection/auditing/audit-ipsec-quick-mode)</li><li>[Auditovat odhlášení](/windows/security/threat-protection/auditing/audit-logoff)</li><li>[Auditovat přihlášení](/windows/security/threat-protection/auditing/audit-logon)</li><li>[Auditovat server NPS (Network Policy Server)](/windows/security/threat-protection/auditing/audit-network-policy-server)</li><li>[Auditovat jiné události přihlášení a odhlášení](/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li><li>[Auditovat zvláštní přihlášení](/windows/security/threat-protection/auditing/audit-special-logon)</li></ul>|
|Přístup k objektu| Audity se pokouší o přístup k určitým objektům nebo typům objektů v síti nebo počítači. Tato kategorie obsahuje následující podkategorie:<ul><li>[Auditovat Generování aplikacemi](/windows/security/threat-protection/auditing/audit-application-generated)</li><li>[Auditovat Certifikační služby](/windows/security/threat-protection/auditing/audit-certification-services)</li><li>[Auditovat podrobnou sdílenou složku](/windows/security/threat-protection/auditing/audit-detailed-file-share)</li><li>[Auditovat sdílenou složku](/windows/security/threat-protection/auditing/audit-file-share)</li><li>[Auditovat systém souborů](/windows/security/threat-protection/auditing/audit-file-system)</li><li>[Auditovat připojení architektury Filtering Platform](/windows/security/threat-protection/auditing/audit-filtering-platform-connection)</li><li>[Auditovat zahození paketu architektury Filtering Platform](/windows/security/threat-protection/auditing/audit-filtering-platform-packet-drop)</li><li>[Manipulace s popisovačem auditu](/windows/security/threat-protection/auditing/audit-handle-manipulation)</li><li>[Auditovat objekt jádra](/windows/security/threat-protection/auditing/audit-kernel-object)</li><li>[Auditovat jiné události přístupu k objektu](/windows/security/threat-protection/auditing/audit-other-object-access-events)</li><li>[Audit registru](/windows/security/threat-protection/auditing/audit-registry)</li><li>[Audit vyměnitelného úložiště](/windows/security/threat-protection/auditing/audit-removable-storage)</li><li>[Auditovat Správce účtů zabezpečení (SAM)](/windows/security/threat-protection/auditing/audit-sam)</li><li>[Auditovat přípravu zásad centrálního přístupu](/windows/security/threat-protection/auditing/audit-central-access-policy-staging)</li></ul>|
|Změna zásad|Audituje změny v důležitých zásadách zabezpečení v místním systému nebo v síti. Zásady jsou většinou zřízené správci, aby lépe zabezpečily síťové prostředky. Sledování změn nebo pokusů o změnu těchto zásad může být důležitým aspektem správy zabezpečení sítě. Tato kategorie obsahuje následující podkategorie:<ul><li>[Auditovat změnu zásad auditování](/windows/security/threat-protection/auditing/audit-audit-policy-change)</li><li>[Auditovat změnu zásad ověřování](/windows/security/threat-protection/auditing/audit-authentication-policy-change)</li><li>[Auditovat změnu zásad autorizace](/windows/security/threat-protection/auditing/audit-authorization-policy-change)</li><li>[Auditovat změnu zásad architektury Filtering Platform](/windows/security/threat-protection/auditing/audit-filtering-platform-policy-change)</li><li>[Auditovat změnu zásad úrovně pravidla MPSSVC](/windows/security/threat-protection/auditing/audit-mpssvc-rule-level-policy-change)</li><li>[Auditovat jiné změny zásad](/windows/security/threat-protection/auditing/audit-other-policy-change-events)</li></ul>|
|Použití oprávnění| Audituje použití určitých oprávnění v jednom nebo více systémech. Tato kategorie obsahuje následující podkategorie:<ul><li>[Auditovat použití oprávnění, která nejsou citlivá](/windows/security/threat-protection/auditing/audit-non-sensitive-privilege-use)</li><li>[Auditovat použití citlivých oprávnění](/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)</li><li>[Auditovat události použití jiných oprávnění](/windows/security/threat-protection/auditing/audit-other-privilege-use-events)</li></ul>|
|Systém| Audituje změny na úrovni systému na počítač, který není zahrnutý v jiných kategoriích a který má možné dopady na zabezpečení. Tato kategorie obsahuje následující podkategorie:<ul><li>[Auditovat ovladač IPsec](/windows/security/threat-protection/auditing/audit-ipsec-driver)</li><li>[Auditovat jiné systémové události](/windows/security/threat-protection/auditing/audit-other-system-events)</li><li>[Auditovat změnu stavu zabezpečení](/windows/security/threat-protection/auditing/audit-security-state-change)</li><li>[Auditovat rozšíření systému zabezpečení](/windows/security/threat-protection/auditing/audit-security-system-extension)</li><li>[Auditovat integritu systému](/windows/security/threat-protection/auditing/audit-system-integrity)</li></ul>|

## <a name="event-ids-per-category"></a>ID událostí na kategorii

 Audity zabezpečení Azure služba AD DS zaznamenávají následující ID událostí, když konkrétní akce aktivuje událost s možností auditu:

| Název kategorie události | ID událostí |
|:---|:---|
|Zabezpečení přihlášení k účtu|4767, 4774, 4775, 4776, 4777|
|Zabezpečení správy účtů|4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4730, 4731, 4732, 4733, 4734, 4735, 4737, 4738, 4740, 4741, 4742, 4743, 4754, 4755, 4756, 4757, 4758, 4764, 4765, 4766, 4780, 4781, 4782, 4793, 4798, 4799, 5376, 5377,|
|Zabezpečení sledování podrobností|Žádné|
|Zabezpečení přístupu DS|5136, 5137, 5138, 5139, 5141|
|Logon-Logoff zabezpečení|4624, 4625, 4634, 4647, 4648, 4672, 4675, 4964|
|Zabezpečení přístupu k objektům|Žádné|
|Zabezpečení změny zásad|4670, 4703, 4704, 4705, 4706, 4707, 4713, 4715, 4716, 4717, 4718, 4719, 4739, 4864, 4865, 4866, 4867, 4904, 4906, 4911, 4912|
|Zabezpečení použití oprávnění|4985|
|Zabezpečení systému|4612, 4621|

## <a name="next-steps"></a>Další kroky

Konkrétní informace o Kusto najdete v následujících článcích:

* [Přehled](/azure/kusto/query/) dotazovacího jazyka Kusto
* [Kurz k Kusto](/azure/kusto/query/tutorial) , ve kterém se seznámíte se základy dotazů.
* [Ukázkové dotazy](/azure/kusto/query/samples) , které vám pomůžou naučit nové možnosti zobrazení dat.
* Kusto [osvědčené postupy](/azure/kusto/query/best-practices) k optimalizaci vašich dotazů pro úspěch.

<!-- LINKS - Internal -->
[migrate-azure-adds]: migrate-from-classic-vnet.md