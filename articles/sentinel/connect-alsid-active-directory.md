---
title: Připojení Alsid pro Active Directory ke službě Azure Sentinel | Microsoft Docs
description: Naučte se, jak pomocí konektoru Alsid pro službu Active Directory získávat do služby Azure Sentinel protokoly Alsid. Zobrazení dat Alsid v sešitech, vytváření výstrah a zlepšení šetření.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 654ecb65068e4321b85594d96e8ca7a7f73cde7e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99566730"
---
# <a name="connect-your-alsid-for-active-directory-ad-to-azure-sentinel"></a>Připojení Alsid pro Active Directory (AD) k Azure Sentinel

> [!IMPORTANT]
> Alsid pro konektor služby Active Directory je momentálně ve **verzi Preview**. Další právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti, najdete v tématu dodatečné [podmínky použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview.

Tento článek vysvětluje, jak připojit Alsid pro řešení AD do Azure Sentinel. Alsid pro datový konektor služby Active Directory umožňuje snadno připojit své Alsidy k protokolům služby AD pomocí Azure Sentinel, takže můžete zobrazit data v sešitech, dotazovat se na ně a vytvořit vlastní výstrahy a začlenit je k vylepšení šetření. Integrace mezi Alsid pro AD a Sentinel Azure využívá server syslog s nainstalovaným agentem Log Analytics. Používá také uživatelsky sestavený analyzátor protokolů založený na funkci Kusto.

> [!NOTE]
> Data budou uložená v geografickém umístění pracovního prostoru, na kterém běží Azure Sentinel.

## <a name="prerequisites"></a>Předpoklady

- V pracovním prostoru Sentinel Azure musíte mít oprávnění k zápisu.

- Vaše řešení Alsid for AD je potřeba nakonfigurovat tak, aby se protokoly exportovali přes syslog.

## <a name="send-alsid-for-ad-logs-to-azure-sentinel-via-the-syslog-agent"></a>Odesílání protokolů služby AD Alsid do Azure Sentinel prostřednictvím agenta syslog

Nakonfigurujte Alsid pro službu AD tak, aby přeposlala zprávy syslog do vašeho pracovního prostoru Azure Sentinel prostřednictvím agenta syslog.

1. V nabídce navigace v Azure Sentinel vyberte **datové konektory**.

1. Z Galerie **datových konektorů** vyberte konektor **Alsid for Active Directory (Preview)** a pak **otevřete stránku konektor**.

1. Postupujte podle pokynů na stránce konektoru **Alsid pro službu Active Directory** :

    1. Konfigurace serveru syslog

        1. Pokud ho ještě nemáte, vytvořte server Linux syslog pro Alsid, aby služba AD odesílala protokoly do. Sentinel Azure podporuje procesy démona **rsyslog** a **syslog-ng** . 

        1. Nakonfigurujte server syslog na výstup Alsid pro protokoly služby AD v samostatném souboru.

    1. Konfigurace Alsid pro AD k odesílání protokolů na váš server syslog

        1. Na portálu **služby Alsid pro službu AD** přejít na *systém*, *Konfigurace* a pak na *syslog*. Odtud můžete vytvořit nové upozornění syslog na váš server syslog. Pro vzdálený server použijte IP adresu počítače se systémem Linux, na který jste nainstalovali agenta pro Linux.

        1. Zkontrolujte, jestli jsou protokoly správně shromážděné na vašem serveru v samostatném souboru (k tomu můžete použít tlačítko **Test konfigurace** v konfiguraci upozornění *syslog* v Alsid pro AD).

    1. Instalace a zprovoznění agenta Log Analytics pro Linux

        - Vyberte virtuální počítač Azure Linux nebo počítač se systémem Linux bez platformy Azure (fyzický nebo virtuální). Postupujte podle odkazů a pokynů na obrazovce. Další podrobnosti najdete v tématu [Konfigurace počítače nebo zařízení se systémem Linux](connect-syslog.md#configure-your-linux-machine-or-appliance) .

    1. Konfigurovat protokoly shromažďované agentem Log Analytics

        - V konfiguraci rozšířených nastavení v pracovním prostoru vyberte zařízení a závažnost.

            1. Klikněte na odkaz **otevřít konfiguraci rozšířených nastavení >pracovního prostoru** na stránce konektoru.

            1. Na obrazovce **Upřesnit nastavení** vyberte **data** a pak **vlastní protokoly**.

            1. Zaškrtněte políčko **použít níže konfiguraci na počítače se systémem Linux** a klikněte na tlačítko **Přidat**.

            1. Klikněte na **zvolit soubor** a nahrajte ukázkový Alsid pro soubor služby AD syslog z počítače se systémem Linux, na kterém běží server syslog, a klikněte na **Další**.

            1. Ověřte, zda je **oddělovač záznamů nastavení** nastaven na **nový řádek** a klikněte na tlačítko **Další**.

            1. Vyberte **Linux** a zadejte cestu k souboru syslog, klikněte na tlačítko **+** a potom na **Další**.

            1. Do pole název zadejte *AlsidForADLog* před příponu _CL a pak klikněte na **Hotovo**.
    
Může trvat až 20 minut, než se vaše protokoly začnou zobrazovat v Log Analytics.

## <a name="find-your-data"></a>Hledání dat

Po navázání úspěšného připojení se data objeví v **protokolech** v části **vlastní protokoly** v *AlsidForADLog_CL* tabulce.

Tento datový konektor závisí na analyzátoru založeném na funkci Kusto, která bude fungovat podle očekávání. Pomocí následujících kroků nastavte funkci **afad_parser** Kusto, která se má používat v dotazech a sešitech.

1. V nabídce navigace v Azure Sentinel vyberte **protokoly**.

1. Zkopírujte následující dotaz a vložte ho do okna dotazu.
    ```kusto
    let CodenameTable=datatable(Codename: string, Explanation: string) [
    "test-checker-codename", "This is a test checker",
    "", "Not an alert",
    "C-ADM-ACC-USAGE", "Recent use of the default administrator account",
    "C-UNCONST-DELEG", "Dangerous delegation",
    "C-PASSWORD-DONT-EXPIRE", "Accounts with never expiring passwords",
    "C-USERS-CAN-JOIN-COMPUTERS", "Users allowed to join computers to the domain",
    "C-CLEARTEXT-PASSWORD", "Potential clear-text password",
    "C-PROTECTED-USERS-GROUP-UNUSED", "Protected Users group not used",
    "C-PASSWORD-POLICY", "Weak password policies are applied on users",
    "C-GPO-HARDENING", "Domain without computer-hardening GPOs",
    "C-LAPS-UNSECURE-CONFIG", "Local administrative account management",
    "C-AAD-CONNECT", "Verify permissions related to AAD Connect accounts",
    "C-AAD-SSO-PASSWORD", "Verify AAD SSO account password last change",
    "C-GPO-SD-CONSISTENCY", "Verify sensitive GPO objects and files permissions",
    "C-DSHEURISTICS", "Domain using a dangerous backward-compatibility configuration",
    "C-DOMAIN-FUNCTIONAL-LEVEL", "Domains have an outdated functional level",
    "C-DISABLED-ACCOUNTS-PRIV-GROUPS", "Disabled accounts in privileged groups",
    "C-DCSHADOW", "Rogue domain controllers",
    "C-DC-ACCESS-CONSISTENCY", "Domain controllers managed by illegitimate users",
    "C-DANGEROUS-TRUST-RELATIONSHIP", "Dangerous trust relationship",
    "C-DANGEROUS-SENSITIVE-PRIVILEGES", "Dangerous sensitive privileges",
    "C-DANG-PRIMGROUPID", "User Primary Group ID",
    "C-BAD-PASSWORD-COUNT", "Brute-force attack detection",
    "C-ADMINCOUNT-ACCOUNT-PROPS", "AdminCount attribute set on standard users",
    "C-ACCOUNTS-DANG-SID-HISTORY", "Accounts having a dangerous SID History attribute",
    "C-ABNORMAL-ENTRIES-IN-SCHEMA", "Dangerous rights in AD's schema",
    "C-GPOLICY-DISABLED-UNLINKED", "Unlinked, disabled or orphan GPO",
    "C-KERBEROS-CONFIG-ACCOUNT", "Kerberos configuration on user account",
    "C-KRBTGT-PASSWORD", "KDC password last change",
    "C-LAPS-UNSECURE-CONFIG", "Local administrative account management",
    "C-NATIVE-ADM-GROUP-MEMBERS", "Native administrative group members",
    "C-NETLOGON-SECURITY", "Unsecured configuration of Netlogon protocol",
    "C-OBSOLETE-SYSTEMS", "Computers running an obsolete OS",
    "C-PASSWORD-NOT-REQUIRED", "Account that might have an empty password",
    "C-PKI-WEAK-CRYPTO", "Use of weak cryptography algorithms into Active Directory PKI",
    "C-PRE-WIN2000-ACCESS-MEMBERS", "Accounts using a pre-Windows 2000 compatible access control",
    "C-PRIV-ACCOUNTS-SPN", "Privileged accounts running Kerberos services",
    "C-REVER-PWD-GPO", "Reversible passwords in GPO",
    "C-ROOTOBJECTS-SD-CONSISTENCY", "Root objects permissions allowing DCSync-like attacks",
    "C-SDPROP-CONSISTENCY", "Ensure SDProp consistency",
    "C-SENSITIVE-CERTIFICATES-ON-USER", "Ensure SDProp consistency",
    "C-SLEEPING-ACCOUNTS", "Sleeping accounts",
    "C-USER-PASSWORD", "User account using old password",
    "C-USERS-REVER-PWDS", "Reversible passwords"
    ];
    let Common = AlsidForADLog_CL
    | parse RawData with
                         Time:datetime  " "
                         Host:string  " "
                         Product:string "["
                         PID:int "]: \""
                         MessageType:int "\" \""
                         AlertID:int "\" \""
                         Forest:string "\" \""
                         Domain:string "\" "
                         DistinctPart:string;
    let Deviances = Common
    | where MessageType == 0 | parse DistinctPart with "\""
                         Codename:string "\" \""
                         Severity:string "\" \""
                         ADObject:string "\" \""
                         DevianceID:string "\" \""
                         ProfileID:string "\" \""
                         ReasonCodename:string "\" \""
                         EventID:string "\""
                         Attributes:string "\r\n";
    let Changes = Common
    | where MessageType == 1
    | parse kind=regex DistinctPart with "\""
                         ADObject:string "\" \""
                         EventID:string "\" \""
                         EventType:string "\" "
                         Attributes:string "\r?\n";
    union Changes, Deviances
    | project-away DistinctPart, Product, _ResourceId, _SubscriptionId
    | lookup kind=leftouter CodenameTable on Codename;
    ```

1. Klikněte na rozevírací seznam **Uložit** a pak klikněte na **Uložit**. V panelu **uložení**

    1. Do **pole název** zadejte **afad_parser**.

    1. V části **Uložit jako** vyberte **funkce**.

    1. Do pole **alias funkce** zadejte **afad_parser**.

    1. V části **kategorie** zadejte **Functions**.

    1. Klikněte na **Uložit**.

    Aktivace aplikací funkcí obvykle trvá od 10 do 15 minut.

Teď jste připraveni dotazovat se na Alsid pro data reklam zadáním `afad_parser` do horního řádku okna dotazu.

Další ukázky dotazů najdete na kartě **Další kroky** na stránce konektor.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak připojit Alsid pro AD ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:

- Naučte se, jak [získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte [s detekcí hrozeb pomocí služby Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Pomocí sešitů](tutorial-monitor-your-data.md) můžete monitorovat data.
