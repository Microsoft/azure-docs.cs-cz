---
title: Ukázka – CIS Microsoft Azure Foundation – mapování doporučení
description: Mapování doporučení pro ukázka srovnávacího testu služby CIS Microsoft Azure Foundation pro Azure Policy.
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/09/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: d02dca4438b55d51cccb5cc1b55679eb72b73991
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232797"
---
# <a name="recommendation-mapping-of-the-cis-microsoft-azure-foundations-benchmark-blueprint-sample"></a>Mapování doporučení ukázka srovnávacího plánu pro CIS Microsoft Azure Foundation

Následující článek podrobně popisuje, jak se vzorový test služby Azure modrotisky Microsoft Azure Foundation Maps mapuje na referenční doporučení pro CIS Microsoft Azure Foundation. Další informace o doporučeních najdete v článku věnovaném standardu [CIS Microsoft Azure Foundation](https://www.cisecurity.org/benchmark/azure/).

Následující mapování jsou uvedená v doporučeních **1.1.0 Foundation v rámci testu služby Microsoft Azure CI v** . Pomocí navigace na pravé straně můžete přejít přímo k určitému mapování doporučení.
Mnohé z mapovaných doporučení jsou implementovány s [Azure Policy](../../../policy/overview.md) iniciativou. Chcete-li si projít kompletní iniciativu, otevřete **zásadu** v Azure Portal a vyberte stránku **definice** . Pak vyhledejte a vyberte možnost  **\[\] auditovat audit CIS Microsoft Azure Foundation srovnávací testy v 1.1.0 a nasaďte specifická rozšíření virtuálních počítačů, která budou podporovat požadavky na audit na** integrovanou iniciativu zásad.

> [!NOTE]
> Brzo se připravuje kompletní ukázka podrobného plánu. Přidružená iniciativa Azure Policy je nyní k dispozici.

## <a name="11-ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>1,1 Zajistěte, aby bylo u všech privilegovaných uživatelů povoleno Multi-Factor Authentication

Tento podrobný plán přiřadí definice [Azure Policy](../../../policy/overview.md) , které odpovídají tomuto doporučení CIS.

- Pro účty s oprávněním vlastníka pro vaše předplatné by se měla povolit vícefaktorové ověřování.
- Pro účty s oprávněním k zápisu do vašeho předplatného by se měla povolit vícefaktorové ověřování.

## <a name="12-ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>1,2 Zajistěte, aby bylo pro všechny uživatele bez oprávnění povoleno vícefaktorové ověřování.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která se zarovnává s tímto doporučením CIS.

- Pro účty s oprávněním ke čtení vašeho předplatného by se měla povolit vícefaktorové ověřování.

## <a name="13-ensure-that-there-are-no-guest-users"></a>1,3 zajistěte, aby žádní uživatelé typu Host

Tento podrobný plán přiřadí definice [Azure Policy](../../../policy/overview.md) , které odpovídají tomuto doporučení CIS.

- Z vašeho předplatného byste měli odebrat externí účty s oprávněním vlastníka.
- Z vašeho předplatného by se měly odebrat externí účty s oprávněním ke čtení.
- Z předplatného by se měly odebrat externí účty s oprávněními pro zápis

## <a name="23-ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>2,3 zajistěte, aby nastavení výchozí zásady ASC "monitorované aktualizace systému" nebylo zakázané.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která se zarovnává s tímto doporučením CIS.

- Do vašich počítačů by se měly nainstalovat aktualizace systému

## <a name="24-ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>2,4 zajistěte, aby nastavení výchozí zásady ASC "monitorování slabých míst operačního systému" nebylo "zakázáno".

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která se zarovnává s tímto doporučením CIS.

- Ohrožení zabezpečení v konfiguraci zabezpečení na vašich počítačích by mělo být opraveno

## <a name="25-ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>2,5 zajistěte, aby nastavení výchozí zásady ASC "monitor Endpoint Protection" nebylo "zakázáno"

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která se zarovnává s tímto doporučením CIS.

- Monitorovat chybějící Endpoint Protection v Azure Security Center

## <a name="26-ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>2,6 zajistěte, aby nastavení výchozí zásady ASC "monitorované disky" nebylo zakázané.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která se zarovnává s tímto doporučením CIS.

- Na virtuálních počítačích by se mělo použít šifrování disku

## <a name="28-ensure-asc-default-policy-setting-monitor-web-application-firewall-is-not-disabled"></a>2,8 zajistěte, aby nastavení výchozí zásady ASC "monitorovat bránu firewall webových aplikací" nebylo zakázané.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která se zarovnává s tímto doporučením CIS.

- Pravidla skupin zabezpečení sítě pro webové aplikace v IaaS by se měla posílit.

## <a name="210-ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>2,10 zajistěte, aby nastavení výchozí zásady ASC "posouzení ohrožení zabezpečení" nebylo zakázané.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která se zarovnává s tímto doporučením CIS.

- Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení.

## <a name="212-ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>2,12 zajistěte, aby nastavení výchozí zásady ASC "monitorovat síťový přístup JIT" nebylo zakázané.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která se zarovnává s tímto doporučením CIS.

- Na virtuálních počítačích by se mělo použít řízení přístupu k síti za běhu

## <a name="215-ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>2,15 zajistěte, aby nastavení výchozí zásady ASC "monitor SQL Encryption" nebylo zakázané.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která se zarovnává s tímto doporučením CIS.

- Je třeba povolit transparentní šifrování dat databází SQL.

## <a name="31-ensure-that-secure-transfer-required-is-set-to-enabled"></a>3,1 Zajistěte, aby byl povinný zabezpečený přenos nastaven na povoleno.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která se zarovnává s tímto doporučením CIS.

- Zabezpečený přenos do účtů úložiště by měl být povolený.

## <a name="37-ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>3,7 zajistěte, aby výchozí pravidlo přístupu k síti pro účty úložiště bylo nastaveno na odepřít.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která se zarovnává s tímto doporučením CIS.

- Auditování neomezeného síťového přístupu k účtům úložiště

## <a name="41-ensure-that-auditing-is-set-to-on"></a>4,1 Ujistěte se, že možnost audit je nastavená na zapnuto.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která se zarovnává s tímto doporučením CIS.

- Auditování by mělo být povolené pro pokročilá nastavení zabezpečení dat na SQL Server

## <a name="42-ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>4,2 Ujistěte se, že je správně nastavená zásada ' AuditActionGroups ' v zásadách auditování pro systém SQL Server.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která se zarovnává s tímto doporučením CIS.

- Nastavení auditování SQL by mělo mít akce – skupiny konfigurované pro zachycení kritických aktivit.

## <a name="43-ensure-that-auditing-retention-is-greater-than-90-days"></a>4,3 zajistěte, aby uchování auditování bylo větší než 90 dnů.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která se zarovnává s tímto doporučením CIS.

- SQL servery by měly být nakonfigurované s uchováním dat po dobu delší než 90 dní.

## <a name="44-ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>4,4 Ujistěte se, že je možnost Rozšířená zabezpečení dat na SQL serveru nastavená na zapnuto.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která se zarovnává s tímto doporučením CIS.

- Na vašich serverech SQL by mělo být povolené pokročilé zabezpečení dat.

## <a name="45-ensure-that-threat-detection-types-is-set-to-all"></a>4,5 zajistěte, aby se typy detekce hrozeb nastavily na ALL.

Tento podrobný plán přiřadí definice [Azure Policy](../../../policy/overview.md) , které odpovídají tomuto doporučení CIS.

- V rozšířených nastaveních zabezpečení dat SQL serveru by se měly nastavit rozšířené typy ochrany před internetovými útoky na ALL.
- V rozšířených nastaveních zabezpečení dat spravované instance SQL by se měly nastavit rozšířené typy ochrany před internetovými útoky na ALL.

## <a name="46-ensure-that-send-alerts-to-is-set"></a>4,6 Ujistěte se, že je nastavena možnost Odeslat výstrahy do.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která se zarovnává s tímto doporučením CIS.

- Pokročilá nastavení zabezpečení dat pro SQL Server by měla obsahovat e-mailovou adresu pro příjem výstrah zabezpečení.

## <a name="47-ensure-that-email-service-and-co-administrators-is-enabled"></a>4,7 zajistěte, aby byla povolená e-mailová služba a spolusprávci.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která se zarovnává s tímto doporučením CIS.

- Pokročilá nastavení zabezpečení dat pro spravovanou instanci SQL by měla obsahovat e-mailovou adresu pro příjem výstrah zabezpečení.

## <a name="48-ensure-that-azure-active-directory-admin-is-configured"></a>4,8 zajistěte, aby byl nakonfigurovaný správce Azure Active Directory.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která se zarovnává s tímto doporučením CIS.

- Pro SQL servery by se měl zřídit správce Azure Active Directory.

## <a name="49-ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>4,9 Ujistěte se, že možnost šifrování dat je u SQL Database nastavená na zapnuto.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která se zarovnává s tímto doporučením CIS.

- Je třeba povolit transparentní šifrování dat databází SQL.

## <a name="410-ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>4,10 zajistěte, aby ochrana TDE systému SQL Server byla zašifrovaná pomocí BYOK (použijte vlastní klíč).

Tento podrobný plán přiřadí definice [Azure Policy](../../../policy/overview.md) , které odpovídají tomuto doporučení CIS.

- Ochrana TDE serverů SQL by měla být šifrovaná pomocí vašeho vlastního klíče.
- Ochrana TDE spravované instance SQL by se měla šifrovat pomocí vlastního klíče.

## <a name="517-ensure-that-logging-for-azure-keyvault-is-enabled"></a>5.1.7 zajistěte, aby protokolování pro Azure webtrezor bylo povolené.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která se zarovnává s tímto doporučením CIS.

- Měly by být povolené diagnostické protokoly v Key Vault.

## <a name="71-ensure-that-os-disk-are-encrypted"></a>7,1 Ujistěte se, že je disk s operačním systémem zašifrovaný.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která se zarovnává s tímto doporučením CIS.

- Na virtuálních počítačích by se mělo použít šifrování disku

## <a name="72-ensure-that-data-disks-are-encrypted"></a>7,2 Ujistěte se, že jsou datové disky šifrované.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která se zarovnává s tímto doporučením CIS.

- Na virtuálních počítačích by se mělo použít šifrování disku

## <a name="75-ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>7,5 zajistěte, aby byly aplikovány nejnovější opravy operačního systému pro všechny Virtual Machines

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která se zarovnává s tímto doporučením CIS.

- Do vašich počítačů by se měly nainstalovat aktualizace systému

## <a name="76-ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>7,6 Ujistěte se, že je nainstalovaná ochrana koncového bodu pro všechny Virtual Machines.

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která se zarovnává s tímto doporučením CIS.

- Monitorovat chybějící Endpoint Protection v Azure Security Center

## <a name="85-enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>8,5 povolení řízení přístupu na základě role (RBAC) v rámci služeb Azure Kubernetes Services

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která se zarovnává s tímto doporučením CIS.

- \[Verze\]Preview: Pro služby Kubernetes by se měla použít Access Control na základě rolí (RBAC).

## <a name="92-ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>9,2 Ujistěte se, že webová aplikace přesměruje veškerý provoz HTTP na HTTPS v Azure App Service

Tento podrobný plán přiřadí definici [Azure Policy](../../../policy/overview.md) , která se zarovnává s tímto doporučením CIS.

- Webová aplikace by měla být přístupná jen přes HTTPS

## <a name="next-steps"></a>Další postup

Teď, když jste zkontrolovali mapování ovládacího prvku pro kontrolní plán služby CIS Microsoft Azure Foundation, přečtěte si následující článek, kde najdete informace o podrobném plánu nebo návštěvě Azure Policy v Azure Portal k přiřazení iniciativy:

> [!div class="nextstepaction"]
> [Ci Microsoft Azure Foundation – přehled srovnávacích testů – přehled](./index.md)
> [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions)

Další články věnované podrobným plánům a postupu jejich využití:

- Přečtěte si informace o [životním cyklu](../../concepts/lifecycle.md)podrobného plánu.
- Principy použití [statických a dynamických parametrů](../../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../../how-to/update-existing-assignments.md)