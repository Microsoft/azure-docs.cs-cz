---
title: Povolení auditů zabezpečení pro služby Azure AD Domain Services | Dokumenty společnosti Microsoft
description: Zjistěte, jak povolit audity zabezpečení k centralizaci protokolování událostí pro analýzy a výstrahy ve službě Azure AD Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 662362c3-1a5e-4e94-ae09-8e4254443697
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: b2138818a9092999dd54b14664f7146f087c4fed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78328642"
---
# <a name="enable-security-audits-for-azure-active-directory-domain-services"></a>Povolení auditů zabezpečení pro službu Azure Active Directory Domain Services

Audity zabezpečení služby Azure Active Directory Domain Services (Azure AD DS) umožňují azure streamovat události zabezpečení do cílových prostředků. Mezi tyto prostředky patří Azure Storage, pracovní prostory Azure Log Analytics nebo Azure Event Hub. Po povolení událostí auditování zabezpečení Azure AD DS odešle všechny auditované události pro vybranou kategorii do cílového prostředku.

Události můžete archivovat do úložiště Azure a streamovat události do softwaru pro správu informací o zabezpečení a událostí (SIEM) (nebo ekvivalentního) pomocí Azure Event Hubs nebo provést vlastní analýzu a používat pracovní prostory Azure Log Analytics z portálu Azure.

> [!IMPORTANT]
> Audity zabezpečení Azure AD DS jsou dostupné jenom pro instance založené na Azure Resource Manageru. Informace o tom, jak migrovat, [najdete v tématu Migrace Azure AD DS z modelu klasické virtuální sítě do Správce prostředků][migrate-azure-adds].

## <a name="audit-event-categories"></a>Kategorie událostí auditu

Audity zabezpečení Azure AD DS jsou v souladu s tradičními auditováním pro tradiční řadiče domény služby AD DS. V hybridních prostředích můžete znovu použít existující vzory auditu, takže stejnou logiku lze použít při analýze událostí. V závislosti na scénáři, který potřebujete k řešení potíží nebo k analýze, je třeba cílit na různé kategorie událostí auditu.

K dispozici jsou následující kategorie událostí auditu:

| Název kategorie auditu | Popis |
|:---|:---|
| Přihlášení k účtu|Audituje pokusy o ověření dat účtu v řadiči domény nebo u místního správce účtů zabezpečení (SAM).</p>Nastavení zásad přihlášení a odhlášení a události sledují pokusy o přístup k určitému počítači. Nastavení a události v této kategorii se zaměřují na databázi účtů, která se používá. Tato kategorie zahrnuje následující podkategorie:<ul><li>[Auditovat ověřování pověření](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-credential-validation)</li><li>[Auditovat ověřovací službu protokolu Kerberos](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-authentication-service)</li><li>[Auditovat operace lístku služby Kerberos](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-service-ticket-operations)</li><li>[Auditovat jiné události přihlášení/odhlášení](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li></ul>|
| Správa účtů|Audituje změny uživatelských účtů a účtů počítačů a skupin. Tato kategorie zahrnuje následující podkategorie:<ul><li>[Auditovat správu skupin aplikací](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-group-management)</li><li>[Auditovat správu účtů počítače](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-computer-account-management)</li><li>[Auditovat správu skupin distribuce](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-distribution-group-management)</li><li>[Auditovat ostatní správu účtů](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-account-management-events)</li><li>[Auditovat správu skupiny zabezpečení](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-group-management)</li><li>[Auditovat správu účtů uživatelů](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-account-management)</li></ul>|
| Sledování podrobností|Audituje aktivity jednotlivých aplikací a uživatelů v tomto počítači a chápe, jak je počítač používán. Tato kategorie zahrnuje následující podkategorie:<ul><li>[Auditovat aktivitu DPAPI](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-dpapi-activity)</li><li>[Audit ní aktivity PNP](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-pnp-activity)</li><li>[Auditovat vytvoření procesu](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-creation)</li><li>[Auditovat ukončení procesu](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-termination)</li><li>[Auditovat události RPC](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-rpc-events)</li></ul>|
| Přístup k adresářovým službám|Audituje pokusy o přístup k objektům a jejich úpravu ve službě AD DS (AD DS). Tyto události auditu jsou protokolovány pouze v řadičích domény. Tato kategorie zahrnuje následující podkategorie:<ul><li>[Auditovat podrobnou replikaci adresářové služby](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-directory-service-replication)</li><li>[Auditovat přístup k adresářové službě](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-access)</li><li>[Auditovat změny adresářové služby](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-changes)</li><li>[Auditovat replikaci adresářové služby](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-replication)</li></ul>|
| Přihlášení-odhlášení|Audituje pokusy o interaktivní přihlášení k počítači nebo prostředně prostředně. Tyto události jsou užitečné pro sledování aktivity uživatelů a identifikaci potenciálních útoků na síťové prostředky. Tato kategorie zahrnuje následující podkategorie:<ul><li>[Auditovat uzamčení účtu](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-account-lockout)</li><li>[Auditovat deklarace identity uživatele/zařízení](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-device-claims)</li><li>[Auditovat rozšířený režim protokolu IPsec](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-extended-mode)</li><li>[Členství ve skupině auditů](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-group-membership)</li><li>[Auditovat hlavní režim protokolu IPsec](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-main-mode)</li><li>[Auditovat rychlý režim protokolu IPsec](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-quick-mode)</li><li>[Auditovat odhlášení](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logoff)</li><li>[Auditovat přihlášení](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logon)</li><li>[Auditovat server NPS (Network Policy Server)](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-network-policy-server)</li><li>[Auditovat jiné události přihlášení/odhlášení](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li><li>[Auditovat zvláštní přihlášení](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-special-logon)</li></ul>|
|Přístup k objektům| Audituje pokusy o přístup k určitým objektům nebo typům objektů v síti nebo počítači. Tato kategorie zahrnuje následující podkategorie:<ul><li>[Auditovat Generování aplikacemi](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-generated)</li><li>[Auditovat Certifikační služby](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-certification-services)</li><li>[Auditovat podrobnou sdílenou složku](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-file-share)</li><li>[Auditovat sdílenou složku](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-share)</li><li>[Auditovat souborový systém](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-system)</li><li>[Auditovat připojení architektury Filtering Platform](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection)</li><li>[Auditovat zahození paketu architektury Filtering Platform](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-packet-drop)</li><li>[Manipulace s popisovačem auditu](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-handle-manipulation)</li><li>[Auditovat objekt jádra](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kernel-object)</li><li>[Auditovat jiné události přístupu k objektu](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-object-access-events)</li><li>[Registr auditu](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-registry)</li><li>[Auditovat vyměnitelné úložiště](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-removable-storage)</li><li>[Auditovat Správce účtů zabezpečení (SAM)](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sam)</li><li>[Protokolování zásad centrálního přístupu auditu](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-central-access-policy-staging)</li></ul>|
|Změna zásad|Audituje změny důležitých zásad zabezpečení v místním systému nebo síti. Zásady jsou obvykle vytvořeny správci, aby pomohli zabezpečit síťové prostředky. Sledování změn nebo pokusů o změnu těchto zásad může být důležitým aspektem správy zabezpečení sítě. Tato kategorie zahrnuje následující podkategorie:<ul><li>[Auditovat změnu zásad auditování](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-audit-policy-change)</li><li>[Auditovat změnu zásad ověřování](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authentication-policy-change)</li><li>[Auditovat změnu zásad autorizace](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authorization-policy-change)</li><li>[Auditovat změnu zásad architektury Filtering Platform](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-policy-change)</li><li>[Auditovat změnu zásad úrovně pravidla MPSSVC](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-mpssvc-rule-level-policy-change)</li><li>[Auditovat další změny zásad](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-policy-change-events)</li></ul>|
|Použití oprávnění| Audituje použití určitých oprávnění v jednom nebo více systémech. Tato kategorie zahrnuje následující podkategorie:<ul><li>[Auditovat použití oprávnění, která nejsou citlivá](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-non-sensitive-privilege-use)</li><li>[Auditovat použití citlivých oprávnění](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)</li><li>[Auditovat události použití jiných oprávnění](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-privilege-use-events)</li></ul>|
|Systém| Audituje změny na úrovni systému počítače, který není zahrnut v jiných kategoriích a který má potenciální důsledky pro zabezpečení. Tato kategorie zahrnuje následující podkategorie:<ul><li>[Auditovat ovladač IPsec](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-driver)</li><li>[Auditovat jiné systémové události](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-system-events)</li><li>[Auditovat změnu stavu zabezpečení](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-state-change)</li><li>[Auditovat rozšíření systému zabezpečení](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-system-extension)</li><li>[Auditovat integritu systému](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-system-integrity)</li></ul>|

## <a name="event-ids-per-category"></a>ID událostí podle kategorie

 Audity zabezpečení Služby Azure AD DS zaznamenávají následující ID událostí, když konkrétní akce spustí auditovatelnou událost:

| Název kategorie události | ID událostí |
|:---|:---|
|Zabezpečení přihlášení k účtu|4767, 4774, 4775, 4776, 4777|
|Zabezpečení správy účtů|4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4730, 4731, 4732, 4733, 4734, 4735, 4737, 4738, 4740, 4741, 4742, 4743, 4754, 4755, 4756, 4757, 4758, 4764, 4765, 4766, 4780, 4781, 4782, 4793, 4798, 4799, 5376, 5377|
|Zabezpečení sledování podrobností|Žádný|
|Zabezpečení aplikace DS Access|5136, 5137, 5138, 5139, 5141|
|Zabezpečení přihlášení a odhlášení|4624, 4625, 4634, 4647, 4648, 4672, 4675, 4964|
|Zabezpečení přístupu k objektům|Žádný|
|Zabezpečení změny zásad|4670, 4703, 4704, 4705, 4706, 4707, 4713, 4715, 4716, 4717, 4718, 4719, 4739, 4864, 4865, 4866, 4867, 4904, 4906, 4911, 4912|
|Oprávnění Použít zabezpečení|4985|
|Zabezpečení systému|4612, 4621|

## <a name="security-audit-destinations"></a>Cíle auditu zabezpečení

Azure Storage, Azure Event Hubs nebo Azure Log Analytics pracovní prostory jako cílový prostředek pro audity zabezpečení Azure AD DS. Tyto cíle lze kombinovat. Azure Storage můžete například použít k archivaci událostí auditu zabezpečení, ale pracovní prostor Azure Log Analytics k analýze a vykazování informací v krátkodobém horizontu.

V následující tabulce jsou popsány scénáře pro každý cílový typ prostředku.

> [!IMPORTANT]
> Před povolením auditů zabezpečení služby Azure AD DS je potřeba vytvořit cílový prostředek. Tyto prostředky můžete vytvořit pomocí portálu Azure, Azure PowerShell nebo Azure CLI.

| Cílový zdroj | Scénář |
|:---|:---|
|Azure Storage| Tento cíl by měl být použit v případě, že primární potřeba je ukládat události auditu zabezpečení pro účely archivace. Jiné cíle lze použít pro účely archivace, ale tyto cíle poskytují možnosti nad rámec primární potřeby archivace. <br /><br />Než povolíte události auditu zabezpečení služby Azure AD DS, [nejprve vytvořte účet úložiště Azure](../storage/common/storage-account-create.md).|
|Azure Event Hubs| Tento cíl by měl být použit v případě, že je primární potřebou sdílení událostí auditu zabezpečení s dalším softwarem, jako je software pro analýzu dat nebo software pro správu informací o zabezpečení & správu událostí (SIEM).<br /><br />Než povolíte události auditu zabezpečení Azure AD DS, [vytvořte centrum událostí pomocí portálu Azure Portal](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)|
|Pracovní prostor Azure Log Analytics| Tento cíl by se měl použít, když je vaší primární potřebou analyzovat a kontrolovat zabezpečené audity přímo z portálu Azure.<br /><br />Než povolíte události auditu zabezpečení služby Azure AD DS, [vytvořte pracovní prostor Analýzy protokolů na webu Azure Portal.](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)|

## <a name="enable-security-audit-events-using-the-azure-portal"></a>Povolení událostí auditu zabezpečení pomocí portálu Azure

Chcete-li povolit události auditu zabezpečení služby Azure AD DS pomocí portálu Azure, proveďte následující kroky.

> [!IMPORTANT]
> Azure AD DS audity zabezpečení nejsou retroaktivní. Události z minulosti nelze načíst ani přehrát. Azure AD DS můžete odeslat pouze události, ke kterým dochází po povolení auditů zabezpečení.

1. Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.
1. V horní části portálu Azure vyhledejte a vyberte **služby Azure AD Domain Services**. Vyberte spravovanou doménu, například *aaddscontoso.com*.
1. V okně Azure AD DS vyberte **nastavení diagnostiky** na levé straně.
1. Ve výchozím nastavení nejsou konfigurovány žádné diagnostiky. Chcete-li začít, vyberte **Přidat diagnostické nastavení**.

    ![Přidání diagnostického nastavení pro službu Azure AD Domain Services](./media/security-audit-events/add-diagnostic-settings.png)

1. Zadejte název pro konfiguraci diagnostiky, *například aadds-auditing*.

    Zaškrtněte políčko u požadovaného cíle auditu zabezpečení. Můžete si vybrat z účtu Azure Storage, centra událostí Azure nebo pracovního prostoru Log Analytics. Tyto cílové prostředky už musí existovat ve vašem předplatném Azure. Cílové prostředky v tomto průvodci nelze vytvořit.

    ![Povolit zachycení požadovaného cíle a typu událostí auditu](./media/security-audit-events/diagnostic-settings-page.png)

    * **Úložiště Azure**
        * Vyberte **Archivovat u účtu úložiště**a pak zvolte **Konfigurovat**.
        * Vyberte **předplatné** a **účet úložiště,** který chcete použít k archivaci událostí auditu zabezpečení.
        * Až budete připraveni, zvolte **OK**.
    * **Centra událostí Azure**
        * Vyberte **Streamovat do centra událostí**a pak zvolte **Konfigurovat**.
        * Vyberte **předplatný** a **obor názvů centra událostí**. V případě potřeby také zvolte **název centra událostí** a potom název zásad centra **událostí**.
        * Až budete připraveni, zvolte **OK**.
    * **Pracovní prostory Azure Log Analytic**
        * Vyberte **Odeslat do analýzy protokolů**a pak zvolte pracovní prostor **Odběr** a **Analýza protokolů,** který chcete použít k ukládání událostí auditu zabezpečení.

1. Vyberte kategorie protokolu, které chcete zahrnout pro konkrétní cílový zdroj. Pokud odešlete události auditu do účtu azure úložiště, můžete také nakonfigurovat zásady uchovávání informací, které definují počet dní pro uchovávání dat. Výchozí nastavení *0* zachová všechna data a neotočí události po určité době.

    Můžete vybrat různé kategorie protokolů pro každý cílový prostředek v rámci jedné konfigurace. Tato možnost umožňuje zvolit, které kategorie protokolů chcete zachovat pro Analýzu protokolů a které kategorie protokolů chcete archivovat, například.

1. Až bude hotovo, vyberte **Uložit,** chcete-li potvrdit změny. Cílové prostředky začnou přijímat události auditu zabezpečení služby Azure AD DS brzy po uložení konfigurace.

## <a name="enable-security-audit-events-using-azure-powershell"></a>Povolení událostí auditu zabezpečení pomocí Azure PowerShellu

Pokud chcete povolit události auditu zabezpečení Azure AD DS pomocí Azure PowerShellu, proveďte následující kroky. V případě potřeby [nejprve nainstalujte modul Azure PowerShell a připojte se k předplatnému Azure](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Azure AD DS audity zabezpečení nejsou retroaktivní. Události z minulosti nelze načíst ani přehrát. Azure AD DS můžete odeslat pouze události, ke kterým dochází po povolení auditů zabezpečení.

1. Ověřte si předplatné Azure pomocí rutiny [Connect-AzAccount.](/powershell/module/Az.Accounts/Connect-AzAccount) Po zobrazení výzvy zadejte přihlašovací údaje svého účtu.

    ```azurepowershell
    Connect-AzAccount
    ```

1. Vytvořte cílový prostředek pro události auditu zabezpečení.

    * **Úložiště Azure** - [Vytvoření účtu úložiště pomocí Azure PowerShellu](../storage/common/storage-account-create.md?tabs=azure-powershell)
    * **Centra událostí** - Azure[Vytvořte centrum událostí pomocí Azure PowerShellu](../event-hubs/event-hubs-quickstart-powershell.md). Může být také nutné použít rutinu [New-AzEventHubAuthorizationRule](/powershell/module/az.eventhub/new-azeventhubauthorizationrule) k vytvoření autorizačního pravidla, které uděluje oprávnění Azure AD DS *oboru názvů*centra událostí . Autorizační pravidlo musí obsahovat práva **Spravovat**, **Naslouchat**a **Odeslat.**

        > [!IMPORTANT]
        > Ujistěte se, že jste nastavili pravidlo autorizace v oboru názvů centra událostí a ne v samotném centru událostí.

    * **Pracovní prostory** - Azure Log Analytic[Vytvořte pracovní prostor analýzy protokolů s Azure PowerShellem](../azure-monitor/learn/quick-create-workspace-posh.md).

1. Získejte ID prostředku pro vaši spravovanou doménu Azure AD DS pomocí rutiny [Get-AzResource.](/powershell/module/Az.Resources/Get-AzResource) Vytvořte proměnnou s názvem *$aadds. ResourceId* pro uložení hodnoty:

    ```azurepowershell
    $aadds = Get-AzResource -name aaddsDomainName
    ```

1. Nakonfigurujte nastavení diagnostiky Azure pomocí rutiny [Set-AzDiagnosticSetting](/powershell/module/Az.Monitor/Set-AzDiagnosticSetting) tak, aby používala cílový prostředek pro události auditu zabezpečení služby Azure AD Domain Services. V následujících příkladech *$aadds proměnná. ResourceId* se používá z předchozího kroku.

    * **Úložiště Azure** – nahraďte *id úložiště* s názvem účtu úložiště:

        ```powershell
        Set-AzDiagnosticSetting `
            -ResourceId $aadds.ResourceId `
            -StorageAccountId storageAccountId `
            -Enabled $true
        ```

    * **Centra událostí Azure** – nahraďte *eventHubName* názvem centra událostí a *eventHubRuleId* s ID pravidla autorizace:

        ```powershell
        Set-AzDiagnosticSetting -ResourceId $aadds.ResourceId `
            -EventHubName eventHubName `
            -EventHubAuthorizationRuleId eventHubRuleId `
            -Enabled $true
        ```

    * **Pracovní prostory Azure Log Analytic** – nahraďte *id pracovního prostoru* pracovního prostoru v pracovním prostoru Analýzy protokolů:

        ```powershell
        Set-AzureRmDiagnosticSetting -ResourceId $aadds.ResourceId `
            -WorkspaceID workspaceId `
            -Enabled $true
        ```

## <a name="query-and-view-security-audit-events-using-azure-monitor"></a>Dotazování a zobrazení událostí auditu zabezpečení pomocí Azure Monitoru

Pracovní prostory Log Analytic umožňují zobrazit a analyzovat události auditu zabezpečení pomocí Azure Monitora a dotazovacího jazyka Kusto. Tento dotazovací jazyk je určen pro použití jen pro čtení, který se může pochlubit funkcemi power analytic s snadno čitelnou syntaxí. Další informace o tom, jak začít s jazyky dotazů Kusto, naleznete v následujících článcích:

* [Dokumentace ke službě Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)
* [Začínáme s Analýzou protokolů ve službě Azure Monitor](../azure-monitor/log-query/get-started-portal.md)
* [Začínáme s dotazy protokolů v Azure Monitoru](../azure-monitor/log-query/get-started-queries.md)
* [Vytváření a sdílená řídicích panelů s daty Log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md)

Následující ukázkové dotazy lze použít k zahájení analýzy událostí auditování zabezpečení z Azure AD DS.

### <a name="sample-query-1"></a>Ukázkový dotaz 1

Zobrazení všech událostí uzamčení účtu za posledních sedm dní:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

### <a name="sample-query-2"></a>Ukázkový dotaz 2

Zobrazit všechny události uzamčení účtu (*4740*) od 3. a 10. února 2020 o půlnoci, seřazeno vzestupně podle data a času:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2020-02-03 09:00) and TimeGenerated <= datetime(2020-02-10)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

### <a name="sample-query-3"></a>Ukázkový dotaz 3

Zobrazit události přihlášení k účtu před sedmi dny (od této chvíle) pro účet s názvem uživatele:

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-4"></a>Ukázkový dotaz 4

Zobrazit události přihlášení k účtu před sedmi dny pro účet s názvem uživatele, který se pokusil přihlásit pomocí chybného hesla *(0xC000006a*):

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc000006a" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-5"></a>Ukázkový dotaz 5

Zobrazit události přihlášení k účtu před sedmi dny pro účet s názvem uživatele, který se pokusil přihlásit, když byl účet uzamčen *(0xC0000234*):

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-6"></a>Ukázkový dotaz 6

Zobrazení počtu událostí přihlášení k účtu před sedmi dny pro všechny pokusy o přihlášení, ke kterým došlo pro všechny uzamčené uživatele:

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| summarize count()
```

## <a name="next-steps"></a>Další kroky

Konkrétní informace o Kusto naleznete v následujících článcích:

* [Přehled](/azure/kusto/query/) dotazovacího jazyka Kusto.
* [Kusto výukový program,](/azure/kusto/query/tutorial) který vás seznámí se základy dotazů.
* [Ukázkové dotazy,](/azure/kusto/query/samples) které vám pomohou naučit se nové způsoby zobrazení dat.
* Kusto [osvědčené postupy](/azure/kusto/query/best-practices) pro optimalizaci dotazů na úspěch.

<!-- LINKS - Internal -->
[migrate-azure-adds]: migrate-from-classic-vnet.md
