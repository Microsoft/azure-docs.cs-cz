---
title: Ukázka srovnávacího testu modelu CI Microsoft Azure Foundation – mapování doporučení
description: Mapování doporučení pro ukázka srovnávacího testu služby CIS Microsoft Azure Foundation pro Azure Policy.
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/01/2019
ms.topic: sample
ms.service: blueprints
ms.openlocfilehash: 6549cf030772d805b063510b9931968027639511
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73583140"
---
# <a name="recommendation-mapping-of-the-cis-microsoft-azure-foundations-benchmark-blueprint-sample"></a>Mapování doporučení ukázka srovnávacího plánu pro CIS Microsoft Azure Foundation

Následující článek podrobně popisuje, jak se vzorový test služby Azure modrotisky Microsoft Azure Foundation Maps mapuje na referenční doporučení pro CIS Microsoft Azure Foundation. Další informace o doporučeních najdete v článku věnovaném standardu [CIS Microsoft Azure Foundation](https://www.cisecurity.org/benchmark/azure/).

Následující mapování jsou uvedená v doporučeních **1.1.0 Foundation v rámci testu služby Microsoft Azure CI v** . Pomocí navigace na pravé straně můžete přejít přímo k určitému mapování doporučení.
Mnohé z mapovaných doporučení jsou implementovány s [Azure Policy](../../../policy/overview.md) iniciativou. Chcete-li si projít kompletní iniciativu, otevřete **zásadu** v Azure Portal a vyberte stránku **definice** . Pak vyhledejte a vyberte **\[Preview\] auditovat CIS Microsoft Azure Foundation test v 1.1.0 doporučení a nasaďte specifická rozšíření virtuálních počítačů, která budou podporovat požadavky na audit** integrovaných iniciativ zásad.

> [!IMPORTANT]
> Každý ovládací prvek níže je přidružen k jedné nebo více definicím [Azure Policy](../../../policy/overview.md) . Tyto zásady vám pomůžou [zhodnotit dodržování předpisů](../../../policy/how-to/get-compliance-data.md) pomocí ovládacího prvku. často však není 1:1 nebo Úplná shoda mezi ovládacím prvkem a jednou nebo více zásadami. V takovém případě **vyhovuje** v Azure Policy pouze zásadám, které jsou samotné. Tím se nezajistí, že budete plně kompatibilní se všemi požadavky ovládacího prvku. Standard kompatibility zahrnuje i ovládací prvky, které nejsou v tuto chvíli řešeny žádnými definicemi Azure Policy. Proto je dodržování předpisů v Azure Policy jenom částečný pohled na celkový stav dodržování předpisů. Přidružení mezi ovládacími prvky a definicemi Azure Policy pro tuto ukázku podrobného plánu dodržování předpisů se mohou v průběhu času měnit. Historii změn si můžete prohlédnout v [historii potvrzení GitHubu](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/cis-azure-1.1.0/control-mapping.md).

## <a name="11-ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>1,1 Zajistěte, aby bylo u všech privilegovaných uživatelů povoleno Multi-Factor Authentication

Tento podrobný plán přiřadí [Azure Policy](../../../policy/overview.md) definice, které vám pomůžou monitorovat, kdy není u privilegovaných Azure Active Directory účtů povoleno ověřování Multi-Factor Authentication.

- Pro účty s oprávněním vlastníka pro vaše předplatné by se měla povolit vícefaktorové ověřování.
- Pro účty s oprávněním k zápisu do vašeho předplatného by se měla povolit vícefaktorové ověřování.

## <a name="12-ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>1,2 Zajistěte, aby bylo pro všechny uživatele bez oprávnění povoleno vícefaktorové ověřování.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže monitorovat v případě, že není povolená aplikace Multi-Factor Authentication pro účty, které nejsou privilegované Azure Active Directory.

- Pro účty s oprávněním ke čtení vašeho předplatného by se měla povolit vícefaktorové ověřování.

## <a name="13-ensure-that-there-are-no-guest-users"></a>1,3 zajistěte, aby žádní uživatelé typu Host

Tento podrobný plán přiřadí [Azure Policy](../../../policy/overview.md) definice, které vám pomůžou monitorovat účty hostů, které by mohly být nutné odebrat.

- Z vašeho předplatného byste měli odebrat externí účty s oprávněním vlastníka.
- Z vašeho předplatného by se měly odebrat externí účty s oprávněním ke čtení.
- Z předplatného by se měly odebrat externí účty s oprávněními pro zápis

## <a name="21-ensure-that-standard-pricing-tier-is-selected"></a>2,1 Ujistěte se, že je vybraná cenová úroveň Standard.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže monitorovat sítě a virtuální počítače, na kterých není povolená úroveň Security Center úrovně Standard.

 - Měla by se vybrat cenová úroveň Security Center Standard.

## <a name="22-ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>2,2 Zajistěte, aby se Automatické zřizování agenta monitorování nastavilo na zapnuto.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže zajistit, že Automatické zřizování agenta Log Analytics je povolené.

- V předplatném by mělo být povolené Automatické zřizování agenta monitorování Log Analytics.

## <a name="23-ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>2,3 zajistěte, aby nastavení výchozí zásady ASC "monitorované aktualizace systému" nebylo zakázané.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže zajistit, že se na virtuální počítače nainstalují aktualizace systému.

- Do vašich počítačů by se měly nainstalovat aktualizace systému

## <a name="24-ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>2,4 zajistěte, aby nastavení výchozí zásady ASC "monitorování slabých míst operačního systému" nebylo "zakázáno".

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže monitorovat ohrožení zabezpečení virtuálních počítačů s unremediated.

- Ohrožení zabezpečení v konfiguraci zabezpečení na vašich počítačích by mělo být opraveno

## <a name="25-ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>2,5 zajistěte, aby nastavení výchozí zásady ASC "monitor Endpoint Protection" nebylo "zakázáno"

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže zajistit, aby byla na virtuálních počítačích povolená služba Endpoint Protection.

- Monitorovat chybějící Endpoint Protection v Azure Security Center

## <a name="26-ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>2,6 zajistěte, aby nastavení výchozí zásady ASC "monitorované disky" nebylo zakázané.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže zajistit, aby byly disky virtuálního počítače šifrované.

- Na virtuálních počítačích by se mělo použít šifrování disku

## <a name="27-ensure-asc-default-policy-setting-monitor-network-security-groups-is-not-disabled"></a>2,7 zajistěte, aby nastavení výchozí zásady ASC "monitorovat skupiny zabezpečení sítě" nebylo zakázané.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže chránit internetové virtuální počítače.

- Pravidla skupiny zabezpečení sítě pro virtuální počítače s přístupem k Internetu by měla být zesílená.

## <a name="28-ensure-asc-default-policy-setting-monitor-web-application-firewall-is-not-disabled"></a>2,8 zajistěte, aby nastavení výchozí zásady ASC "monitorovat bránu firewall webových aplikací" nebylo zakázané.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která pomáhá chránit virtuální počítače s webovými aplikacemi.

- Pravidla skupin zabezpečení sítě pro webové aplikace v IaaS by se měla posílit.

## <a name="29-ensure-asc-default-policy-setting-enable-next-generation-firewallngfw-monitoring-is-not-disabled"></a>2,9 zajistěte, aby nastavení výchozí zásady ASC "povolit bránu firewall nové generace (NGFW)" nebylo "zakázáno"

Tento podrobný plán přiřadí [Azure Policy](../../../policy/overview.md) definice, které pomůžou chránit podsítě a virtuální počítače před hrozbami omezením přístupu. Zásady Security Center, na které odkazuje toto doporučení týkající se pravidla CIS Microsoft Azure Foundation, se nahradila dvěma novými doporučeními. Zásady, na které se odkazuje, řeší nová doporučení.

- Podsítě by měly být přidružené ke skupině zabezpečení sítě.
- Virtuální počítače by měly být přidružené ke skupině zabezpečení sítě.

## <a name="210-ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>2,10 zajistěte, aby nastavení výchozí zásady ASC "posouzení ohrožení zabezpečení" nebylo zakázané.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže zajistit detekci a nápravu chyb zabezpečení.

- Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení.

## <a name="211-ensure-asc-default-policy-setting-monitor-storage-blob-encryption-is-not-disabled"></a>2,11 zajistěte, aby nastavení výchozí zásady ASC "monitor" šifrování úložiště objektů blob "není zakázané".

Pro všechny nové a existující účty úložiště je povolené šifrování Azure Storage a nedá se zakázat. (Toto je výchozí schopnost Azure. neexistuje žádné přiřazení zásad.)

## <a name="212-ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>2,12 zajistěte, aby nastavení výchozí zásady ASC "monitorovat síťový přístup JIT" nebylo zakázané.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže řídit přístup k virtuálním počítačům.

- Na virtuálních počítačích by se mělo použít řízení přístupu k síti za běhu

## <a name="213-ensure-asc-default-policy-setting-monitor-adaptive-application-whitelisting-is-not-disabled"></a>2,13 zajistěte, aby nastavení výchozí zásady ASC "monitorování adaptivních aplikací" nebylo zakázané.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže zajistit, že se na virtuálních počítačích povolí Adaptivní řízení aplikací.

- Na virtuálních počítačích by měly být povolené Adaptivní řízení aplikací.

## <a name="214-ensure-asc-default-policy-setting-monitor-sql-auditing-is-not-disabled"></a>2,14 zajistěte, aby nastavení výchozí zásady ASC "monitor SQL audit" nebylo zakázané.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která pomáhá zajistit, aby bylo povoleno auditování SQL serveru.

- Auditování by mělo být povolené pro pokročilá nastavení zabezpečení dat na SQL Server

## <a name="215-ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>2,15 zajistěte, aby nastavení výchozí zásady ASC "monitor SQL Encryption" nebylo zakázané.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže zajistit, že transparentní šifrování dat je povolený pro databáze SQL.

- Je třeba povolit transparentní šifrování dat databází SQL.

## <a name="216-ensure-that-security-contact-emails-is-set"></a>2,16 Ujistěte se, že je nastavená možnost e-maily kontaktů zabezpečení.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže zajistit, aby byla oznámení zabezpečení správně povolena.

- Pro vaše předplatné by se měla zadat e-mailová adresa kontaktu pro zabezpečení.

## <a name="217-ensure-that-security-contact-phone-number-is-set"></a>2,17 Ujistěte se, že je nastavená možnost kontakt na zabezpečení telefonní číslo.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže zajistit, aby byla oznámení zabezpečení správně povolena.

- Pro vaše předplatné by se mělo zadat telefonní číslo kontaktu zabezpečení.

## <a name="218-ensure-that-send-email-notification-for-high-severity-alerts-is-set-to-on"></a>2,18 zajistěte, aby upozornění "Odeslat e-mail s vysokou závažností" bylo nastaveno na hodnotu Zapnuto.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže zajistit, aby byla oznámení zabezpečení správně povolena.

- Mělo by být povolené e-mailové oznámení pro výstrahy s vysokou závažností.

## <a name="219-ensure-that-send-email-also-to-subscription-owners-is-set-to-on"></a>2,19 Ujistěte se, že možnost Odeslat e-mail také vlastníkům předplatného je nastavená na zapnuto.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže zajistit, aby byla oznámení zabezpečení správně povolena.

- Mělo by se povolit e-mailové oznámení vlastníkovi předplatného pro upozornění s vysokou závažností.

## <a name="31-ensure-that-secure-transfer-required-is-set-to-enabled"></a>3,1 Zajistěte, aby byl povinný zabezpečený přenos nastaven na povoleno.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže monitorovat účty úložiště, které umožňují nezabezpečená připojení.

- Zabezpečený přenos do účtů úložiště by měl být povolený.

## <a name="37-ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>3,7 zajistěte, aby výchozí pravidlo přístupu k síti pro účty úložiště bylo nastaveno na odepřít.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže monitorovat účty úložiště, které povolují neomezený přístup.

- Auditování neomezeného síťového přístupu k účtům úložiště

## <a name="38-ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>3,8 zajistěte, aby se pro přístup k účtu úložiště povolily důvěryhodné služby Microsoftu.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže monitorovat účty úložiště, které nepovolují přístup z důvěryhodných služeb Microsoftu.

- Účty úložiště by měly umožňovat přístup z důvěryhodných služeb Microsoftu.

## <a name="41-ensure-that-auditing-is-set-to-on"></a>4,1 Ujistěte se, že možnost audit je nastavená na zapnuto.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která pomáhá zajistit, aby bylo povoleno auditování SQL serveru. 

- Auditování by mělo být povolené pro pokročilá nastavení zabezpečení dat na SQL Server

## <a name="42-ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>4,2 Ujistěte se, že je správně nastavená zásada ' AuditActionGroups ' v zásadách auditování pro systém SQL Server.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže zajistit správnou konfiguraci auditování SQL serveru.

- Nastavení auditování SQL by mělo mít akce – skupiny konfigurované pro zachycení kritických aktivit.

## <a name="43-ensure-that-auditing-retention-is-greater-than-90-days"></a>4,3 zajistěte, aby uchování auditování bylo větší než 90 dnů.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže zajistit, že protokoly systému SQL Server budou uchovány alespoň 90 dní.

- SQL servery by měly být nakonfigurované s uchováním dat po dobu delší než 90 dní.

## <a name="44-ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>4,4 Ujistěte se, že je možnost Rozšířená zabezpečení dat na SQL serveru nastavená na zapnuto.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže zajistit, aby byla na serverech SQL a spravovaných instancích SQL povolena Pokročilá zabezpečení dat.

- Pro spravované instance SQL by mělo být povolené rozšířené zabezpečení dat
- Na vašich serverech SQL by mělo být povolené pokročilé zabezpečení dat.

## <a name="45-ensure-that-threat-detection-types-is-set-to-all"></a>4,5 zajistěte, aby se typy detekce hrozeb nastavily na ALL.

Tento podrobný plán přiřadí [Azure Policy](../../../policy/overview.md) definice, které vám pomůžou zajistit správnou konfiguraci rozšířené ochrany před internetovými útoky na serverech SQL a spravovaných instancích SQL.

- V rozšířených nastaveních zabezpečení dat SQL serveru by se měly nastavit rozšířené typy ochrany před internetovými útoky na ALL.
- V rozšířených nastaveních zabezpečení dat spravované instance SQL by se měly nastavit rozšířené typy ochrany před internetovými útoky na ALL.

## <a name="46-ensure-that-send-alerts-to-is-set"></a>4,6 Ujistěte se, že je nastavena možnost Odeslat výstrahy do.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže zajistit správné povolení pokročilých oznámení o zabezpečení dat.

- Pokročilá nastavení zabezpečení dat pro spravovanou instanci SQL by měla obsahovat e-mailovou adresu pro příjem výstrah zabezpečení.
- Pokročilá nastavení zabezpečení dat pro SQL Server by měla obsahovat e-mailovou adresu pro příjem výstrah zabezpečení.

## <a name="47-ensure-that-email-service-and-co-administrators-is-enabled"></a>4,7 zajistěte, aby byla povolená e-mailová služba a spolusprávci.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže zajistit správné povolení pokročilých oznámení o zabezpečení dat.

- E-mailová oznámení správcům a vlastníkům předplatného by se měla povolit v rozšířených nastaveních zabezpečení dat spravované instance SQL
- V nastaveních pokročilého zabezpečení dat serverů SQL by měla být povolená e-mailová oznámení pro správce a vlastníky předplatného.

## <a name="48-ensure-that-azure-active-directory-admin-is-configured"></a>4,8 zajistěte, aby byl nakonfigurovaný správce Azure Active Directory.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže zajistit zřízení správce Azure Active Directory pro SQL servery.

- Pro SQL servery by se měl zřídit správce Azure Active Directory.

## <a name="49-ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>4,9 Ujistěte se, že možnost šifrování dat je u SQL Database nastavená na zapnuto.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže zajistit, že transparentní šifrování dat je povolený pro databáze SQL.

- Je třeba povolit transparentní šifrování dat databází SQL.

## <a name="410-ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>4,10 zajistěte, aby ochrana TDE systému SQL Server byla zašifrovaná pomocí BYOK (použijte vlastní klíč).

Tento podrobný plán přiřadí [Azure Policy](../../../policy/overview.md) definice, které vám pomůžou zajistit, aby byla transparentní šifrování dat pro SQL servery a spravované instance SQL šifrované pomocí vlastního klíče.

- Ochrana TDE spravované instance SQL by se měla šifrovat pomocí vlastního klíče.
- Ochrana TDE serverů SQL by měla být šifrovaná pomocí vašeho vlastního klíče.

## <a name="411-ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>4,11 zajistěte, aby se vynutilo připojení SSL u databázového serveru MySQL nastavené na povoleno.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže zajistit, že databázové servery MySQL vynutily připojení SSL.

- Pro databázové servery MySQL by mělo být povoleno připojení SSL vynutilo.

## <a name="413-ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>4,13 zajistěte, aby se vynutilo připojení SSL u databázového serveru PostgreSQL nastavené na povoleno.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže zajistit, že databázové servery PostgreSQL vynutily připojení SSL.

- Pro databázové servery PostgreSQL by mělo být povoleno připojení SSL vynutilo.

## <a name="417-ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>4,17 zajistěte, aby parametr serveru ' connection_throttling ' byl pro databázový server PostgreSQL nastaven na hodnotu ' ON '

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže zmírnit útoky hrubou silou na databázové servery PostgreSQL.

- Pro databázové servery PostgreSQL by mělo být povoleno omezování připojení.

## <a name="419-ensure-that-azure-active-directory-admin-is-configured"></a>4,19 zajistěte, aby byl nakonfigurovaný správce Azure Active Directory.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže zajistit zřízení správce Azure Active Directory pro SQL servery. Srovnávací test CIS Microsoft Azure základy zahrnuje toto doporučení; je však duplicitní [doporučení 4,8](#48-ensure-that-azure-active-directory-admin-is-configured).

- Pro SQL servery by se měl zřídit správce Azure Active Directory.

## <a name="511-ensure-that-a-log-profile-exists"></a>5.1.1 Zajistěte, aby existoval profil protokolu

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže zajistit, aby profil protokolu existoval pro všechna předplatná Azure. 

- Předplatná Azure by měla mít profil protokolu aktivit.

## <a name="512-ensure-that-activity-log-retention-is-set-365-days-or-greater"></a>5.1.2 Zajistěte, aby uchování protokolu aktivit bylo nastaveno na 365 dní nebo více.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže zajistit, že protokoly aktivit se uchovávají aspoň po dobu jednoho roku.

- Protokol aktivit by se měl uchovávat aspoň po dobu jednoho roku.

## <a name="513-ensure-audit-profile-captures-all-the-activities"></a>5.1.3 Ujistěte se, že profil auditu zachycuje všechny aktivity.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže zajistit správnou konfiguraci profilu protokolu.

- Profil protokolu Azure Monitor by měl shromažďovat protokoly pro kategorie Write, DELETE a Action.

## <a name="514-ensure-the-log-profile-captures-activity-logs-for-all-regions-including-global"></a>5.1.4 Ujistěte se, že profil protokolu zachycuje protokoly aktivit pro všechny oblasti, včetně globálních

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže zajistit správnou konfiguraci profilu protokolu.

- Azure Monitor by měly shromažďovat protokoly aktivit ze všech oblastí

## <a name="517-ensure-that-logging-for-azure-keyvault-is-enabled"></a>5.1.7 zajistěte, aby protokolování pro Azure webtrezor bylo povolené.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže zajistit, aby byly pro trezory klíčů povolené diagnostické protokoly.

- Měly by být povolené diagnostické protokoly v Key Vault.

## <a name="65-ensure-that-network-watcher-is-enabled"></a>6,5 zajistěte, aby byl Network Watcher povolený.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže zajistit, že Network Watcher je povolený pro všechny oblasti, ve kterých jsou nasazené prostředky. Tato zásada vyžaduje pole parametrů, které určuje všechny použitelné oblasti. Výchozí hodnota v této definici iniciativy zásad je "eastus".

- Network Watcher by měl být povolený.

## <a name="71-ensure-that-os-disk-are-encrypted"></a>7,1 Ujistěte se, že je disk s operačním systémem zašifrovaný.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže zajistit, aby na virtuálních počítačích bylo povolené šifrování disku.

- Na virtuálních počítačích by se mělo použít šifrování disku

## <a name="72-ensure-that-data-disks-are-encrypted"></a>7,2 Ujistěte se, že jsou datové disky šifrované.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže zajistit, aby na virtuálních počítačích bylo povolené šifrování disku.

- Na virtuálních počítačích by se mělo použít šifrování disku

## <a name="73-ensure-that-unattached-disks-are-encrypted"></a>7,3 zajistěte, aby byly nepřipojené disky šifrované

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže zajistit, aby nepřipojené disky byly šifrované.

- Nepřipojené disky by měly být zašifrované

## <a name="74-ensure-that-only-approved-extensions-are-installed"></a>7,4 zajistěte, aby byla nainstalována pouze schválená rozšíření.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže zajistit, že jsou nainstalovaná jenom schválená rozšíření virtuálních počítačů. Tato zásada vyžaduje pole parametrů, které určuje všechna schválená rozšíření virtuálních počítačů. Tato definice iniciativy zásad obsahuje navrhované výchozí hodnoty, které by zákazníci měli ověřit. 

 - Musí být nainstalovaná jenom schválená rozšíření virtuálních počítačů.

## <a name="75-ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>7,5 zajistěte, aby byly aplikovány nejnovější opravy operačního systému pro všechny Virtual Machines

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže zajistit, že se na virtuální počítače nainstalují aktualizace systému.

- Do vašich počítačů by se měly nainstalovat aktualizace systému

## <a name="76-ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>7,6 Ujistěte se, že je nainstalovaná ochrana koncového bodu pro všechny Virtual Machines.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže zajistit, aby byla na virtuálních počítačích povolená služba Endpoint Protection.

- Monitorovat chybějící Endpoint Protection v Azure Security Center

## <a name="84-ensure-the-key-vault-is-recoverable"></a>8,4 zajistěte, aby byl Trezor klíčů obnovitelný.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže zajistit, aby objekty trezoru klíčů byly obnovitelné v případě náhodného odstranění.

- Key Vault objekty by měly být obnovitelné

## <a name="85-enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>8,5 povolení řízení přístupu na základě role (RBAC) v rámci služeb Azure Kubernetes Services

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže zajistit, aby se řízení přístupu na základě role používalo ke spravovaným oprávněním v clusterech služby Kubernetes.

- \[Preview\]: v Kubernetes službách by se měla používat Access Control na základě rolí (RBAC).

## <a name="92-ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>9,2 Ujistěte se, že webová aplikace přesměruje veškerý provoz HTTP na HTTPS v Azure App Service

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která vám pomůže zajistit, že webové aplikace budou přístupné jenom přes zabezpečená připojení.

- Webová aplikace by měla být přístupná jen přes protokol HTTPS

## <a name="next-steps"></a>Další kroky

Teď, když jste zkontrolovali mapování ovládacího prvku pro kontrolní plán služby CIS Microsoft Azure Foundation, najdete v následujících článcích informace o podrobném plánu nebo návštěvě Azure Policy v Azure Portal k přiřazení iniciativy:

> [!div class="nextstepaction"]
> [Ci Microsoft Azure Foundation – přehled testu výkonnosti – přehled](./index.md)
> [SNS Microsoft Azure Foundation – postup nasazení](./deploy.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Další informace o [životním cyklu podrobného plánu](../../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../../how-to/update-existing-assignments.md)