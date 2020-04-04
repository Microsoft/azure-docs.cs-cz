---
title: Ovládací prvky ukázkového plánu srovnávacího plánu cis Microsoft Azure Foundations
description: Doporučení mapování CIS Microsoft Azure Foundations srovnávací plán ukázka zásad Azure.
ms.date: 11/04/2019
ms.topic: sample
ms.openlocfilehash: ea61ae4ea05b34c785485cbb5fd39c8a772565e3
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656967"
---
# <a name="recommendation-mapping-of-the-cis-microsoft-azure-foundations-benchmark-blueprint-sample"></a>Mapování doporučení ukázky plánu srovnávacího plánu nadace CIS Microsoft Azure Foundations

V následujícím článku je podrobně uveden podrobný plán azure blueprints CIS Microsoft Azure Foundations Benchmark, který se mapuje na doporučení benchmarku CIS Microsoft Azure Foundations. Další informace o doporučeních naleznete v tématu [CZS Microsoft Azure Foundations Benchmark](https://www.cisecurity.org/benchmark/azure/).

Následující mapování jsou k **doporučení mj.** Pomocí navigace vpravo přejděte přímo na konkrétní mapování doporučení.
Mnoho mapovaných doporučení se implementuje pomocí iniciativy [Zásad azure.](../../../policy/overview.md) Pokud chcete zkontrolovat celou iniciativu, otevřete **zásady** na webu Azure portal a vyberte stránku **Definice.** Potom vyhledejte a vyberte ** \[doporučení pro předběžnou auditní\] hodnotu CIS Microsoft Azure Foundations benchmark u1.1.0 a nasaďte konkrétní rozšíření virtuálních her, která podporují** předdefinovanou iniciativu zásad na audit.

> [!IMPORTANT]
> Každý ovládací prvek níže je přidružen k jedné nebo více definic [zásad Azure.](../../../policy/overview.md) Tyto zásady vám mohou pomoci [posoudit dodržování](../../../policy/how-to/get-compliance-data.md) ovládacího prvku; však často není 1:1 nebo úplnou shodu mezi ovládacím prvkem a jednu nebo více zásad. Jako **takový, kompatibilní** v zásadách Azure odkazuje pouze na zásady samotné; Tím nezajistíte, že jste plně kompatibilní se všemi požadavky ovládacího prvku. Kromě toho standard dodržování předpisů zahrnuje ovládací prvky, které nejsou v tuto chvíli adresovány žádnými definicemi zásad Azure. Dodržování předpisů v zásadách Azure je tedy pouze částečným zobrazením celkového stavu dodržování předpisů. Přidružení mezi ovládacími prvky a definicemi zásad Azure pro tento ukázkový plán dodržování předpisů se může v průběhu času měnit. Chcete-li zobrazit historii změn, podívejte se na [historii potvrzení GitHubu](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/cis-azure-1.1.0/control-mapping.md).

## <a name="11-ensure-that-multi-factor-authentication-is-enabled-for-all-privileged-users"></a>1.1 Zajistěte, aby bylo pro všechny privilegované uživatele povoleno vícefaktorové ověřování

Tento podrobný plán přiřazuje definice [zásad Azure,](../../../policy/overview.md) které vám pomohou sledovat, když vícefaktorové ověřování není povoleno na privilegovaných účtech Azure Active Directory.

- Vícefaktorové informace by měly být povoleny u účtů s oprávněními vlastníka k vašemu předplatnému.
- Vícefaktorové informace by měly být povoleny u účtů s oprávněními k zápisu v rámci vašeho předplatného.

## <a name="12-ensure-that-multi-factor-authentication-is-enabled-for-all-non-privileged-users"></a>1.2 Zajistit, aby bylo pro všechny neprivilegované uživatele povoleno vícefaktorové ověřování

Tento podrobný plán přiřazuje [definici zásad Azure,](../../../policy/overview.md) která vám pomůže sledovat, když vícefaktorové ověřování není povoleno na neprivilegovaných účtech Azure Active Directory.

- Vícefaktorové informace by měly být povoleny u účtů s oprávněními ke čtení v rámci vašeho předplatného.

## <a name="13-ensure-that-there-are-no-guest-users"></a>1.3 Ujistěte se, že nejsou žádní uživatelé typu Host

Tento podrobný plán přiřazuje definice [zásad Azure,](../../../policy/overview.md) které vám pomůžou sledovat účty hosta, které můžou být potřeba odebrat.

- Z předplatného by měly být odebrány externí účty s oprávněními vlastníka.
- Z předplatného by měly být odebrány externí účty s oprávněním ke čtení.
- Z předplatného by měly být odebrány externí účty s oprávněními k zápisu.

## <a name="123-ensure-that-no-custom-subscription-owner-roles-are-created"></a>1.23 Ujistěte se, že nejsou vytvořeny žádné vlastní role vlastníka předplatného.

Tento podrobný plán přiřazuje definice [zásad Azure,](../../../policy/overview.md) které vám pomůžou sledovat vlastní role vlastníka předplatného, které můžou být potřeba odebrat.

- Vlastní role vlastníka předplatného by neměly existovat.

## <a name="21-ensure-that-standard-pricing-tier-is-selected"></a>2.1 Ujistěte se, že je vybrána standardní cenová úroveň

Tento podrobný plán přiřazuje [definici zásad Azure,](../../../policy/overview.md) která vám pomůže monitorovat sítě a virtuální počítače, kde není povolena úroveň standardu Security Center.

 - Měla by být vybrána standardní cenová úroveň Centra zabezpečení.

## <a name="22-ensure-that-automatic-provisioning-of-monitoring-agent-is-set-to-on"></a>2.2 Zajistit, aby "Automatické zřizování monitorovacího agenta" bylo nastaveno na "Zapnuto"

Tento podrobný plán přiřadí [definici zásad Azure,](../../../policy/overview.md) která vám pomůže zajistit automatické zřizování agenta Analýzy protokolů je povolena.

- Automatické zřizování agenta monitorování Analýzy protokolů by mělo být povoleno ve vašem předplatném.

## <a name="23-ensure-asc-default-policy-setting-monitor-system-updates-is-not-disabled"></a>2.3 Ujistěte se, že výchozí nastavení zásad ASC "Sledování aktualizací systému" není "Zakázáno"

Tento podrobný plán přiřadí [definici zásad Azure,](../../../policy/overview.md) která vám pomůže zajistit, aby se aktualizace systému nainstalovaly na virtuálních počítačích.

- Na počítače by se měly nainstalovat aktualizace systému

## <a name="24-ensure-asc-default-policy-setting-monitor-os-vulnerabilities-is-not-disabled"></a>2.4 Ujistěte se, že výchozí nastavení zásad ASC "Monitor OS Vulnerabilities" není "Zakázáno"

Tento podrobný plán přiřazuje [definici zásad Azure,](../../../policy/overview.md) která vám pomůže sledovat neopravené chyby zabezpečení virtuálních strojů.

- Chyby zabezpečení v konfiguraci zabezpečení na vašich počítačích by měly být opraveny

## <a name="25-ensure-asc-default-policy-setting-monitor-endpoint-protection-is-not-disabled"></a>2.5 Ujistěte se, že výchozí nastavení zásad ASC "Ochrana koncového bodu monitoru" není "Zakázáno"

Tento podrobný plán přiřadí [definici zásad Azure,](../../../policy/overview.md) která vám pomůže zajistit, že ochrana koncového bodu je povolená na virtuálních počítačích.

- Sledování chybějící ochrany koncového bodu v Azure Security Center

## <a name="26-ensure-asc-default-policy-setting-monitor-disk-encryption-is-not-disabled"></a>2.6 Ujistěte se, že výchozí nastavení zásad ASC "Šifrování disku monitoru" není "Zakázáno"

Tento podrobný plán přiřadí [definici zásad Azure,](../../../policy/overview.md) která vám pomůže zajistit, že disky virtuálních počítačů jsou šifrované.

- Šifrování disku by mělo být použito na virtuálních počítačích.

## <a name="27-ensure-asc-default-policy-setting-monitor-network-security-groups-is-not-disabled"></a>2.7 Ujistěte se, že výchozí nastavení zásad ASC "Monitor network security groups" není "Zakázáno"

Tento podrobný plán přiřadí [definici zásad Azure,](../../../policy/overview.md) která vám pomůže chránit virtuální počítače orientované na Internet.

- Pravidla skupiny zabezpečení sítě pro virtuální počítače orientované na Internet by měla být posílena

## <a name="28-ensure-asc-default-policy-setting-monitor-web-application-firewall-is-not-disabled"></a>2.8 Ujistěte se, že výchozí nastavení zásad ASC "Monitor Web Application Firewall" není "Zakázáno"

Tento podrobný plán přiřazuje [definici zásad Azure,](../../../policy/overview.md) která vám pomůže chránit virtuální počítače se spuštěným webovými aplikacemi.

- Pravidla nsg pro webové aplikace na IaaS by měla být posílena

## <a name="29-ensure-asc-default-policy-setting-enable-next-generation-firewallngfw-monitoring-is-not-disabled"></a>2.9 Ujistěte se, že výchozí nastavení zásad ASC "Povolit monitorování brány firewall nové generace (NGFW) není "zakázáno"

Tento podrobný plán přiřazuje definice [zásad Azure,](../../../policy/overview.md) které pomáhají chránit podsítě a virtuální počítače před hrozbami omezením přístupu. Zásady Centra zabezpečení, na které odkazuje toto doporučení benchmarku CIS Microsoft Azure Foundations, byly nahrazeny dvěma novými doporučeními. Níže uvedené politiky se zabývají novými doporučeními.

- Podsítě by měly být přidruženy ke skupině zabezpečení sítě.
- Virtuální počítače by měly být přidruženy ke skupině zabezpečení sítě.

## <a name="210-ensure-asc-default-policy-setting-monitor-vulnerability-assessment-is-not-disabled"></a>2.10 Ujistěte se, že výchozí nastavení zásad ASC "Sledování posouzení zranitelnosti" není "zakázáno"

Tento podrobný plán přiřadí [definici zásad Azure,](../../../policy/overview.md) která vám pomůže zajistit, aby byly zjištěny a opraveny chyby zabezpečení.

- Chyby zabezpečení by měly být opraveny řešením pro posouzení zranitelnosti

## <a name="211-ensure-asc-default-policy-setting-monitor-storage-blob-encryption-is-not-disabled"></a>2.11 Ujistěte se, že výchozí nastavení zásad ASC "Šifrování objektů blob úložiště monitoru" není "zakázáno"

Šifrování služby Azure Storage je u všech nových a existujících účtů úložiště povolené a nelze ho zakázat. (Toto je výchozí funkce Azure; neexistuje žádné přiřazení zásad.)

## <a name="212-ensure-asc-default-policy-setting-monitor-jit-network-access-is-not-disabled"></a>2.12 Ujistěte se, že výchozí nastavení zásad ASC "Monitor JIT Network Access" není "Zakázáno"

Tento podrobný plán přiřadí [definici zásad Azure,](../../../policy/overview.md) která vám pomůže řídit přístup k virtuálním počítačům.

- Na virtuálních počítačích by se mělo používat řízení přístupu k síti podle potřeby

## <a name="213-ensure-asc-default-policy-setting-monitor-adaptive-application-whitelisting-is-not-disabled"></a>2.13 Ujistěte se, že výchozí nastavení zásad ASC "Monitor Adaptive Application Whitelisting" není "Zakázáno"

Tento podrobný plán přiřadí [definici zásad Azure,](../../../policy/overview.md) která vám pomůže zajistit adaptivní řízení aplikací je povolená na virtuálních počítačích.

- Adaptivní ovládací prvky aplikací by měly být povoleny na virtuálních počítačích

## <a name="214-ensure-asc-default-policy-setting-monitor-sql-auditing-is-not-disabled"></a>2.14 Ujistěte se, že výchozí nastavení zásad ASC "Sledování auditování SQL" není "Zakázáno"

Tento podrobný plán přiřadí [definici zásad Azure,](../../../policy/overview.md) která pomáhá zajistit, že je povoleno auditování serveru SQL.

- Auditování by mělo být povoleno v rozšířeném nastavení zabezpečení dat na serveru SQL Server.

## <a name="215-ensure-asc-default-policy-setting-monitor-sql-encryption-is-not-disabled"></a>2.15 Ujistěte se, že výchozí nastavení zásad ASC "Monitor SQL Encryption" není "Zakázáno"

Tento podrobný plán přiřadí [definici zásad Azure,](../../../policy/overview.md) která vám pomůže zajistit, že je v databázích SQL povoleno transparentní šifrování dat.

- Transparentní šifrování dat v databázích SQL by mělo být povoleno

## <a name="216-ensure-that-security-contact-emails-is-set"></a>2.16 Zajistit, aby byly nastaveny "e-maily bezpečnostních kontaktů"

Tento podrobný plán přiřazuje [definici zásad Azure,](../../../policy/overview.md) která vám pomůže zajistit, že oznámení zabezpečení jsou správně povolena.

- Pro vaše předplatné by měla být poskytnuta e-mailová adresa bezpečnostního kontaktu.

## <a name="217-ensure-that-security-contact-phone-number-is-set"></a>2.17 Ujistěte se, že je nastaven bezpečnostní kontakt "Telefonní číslo"

Tento podrobný plán přiřazuje [definici zásad Azure,](../../../policy/overview.md) která vám pomůže zajistit, že oznámení zabezpečení jsou správně povolena.

- Pro vaše předplatné by mělo být k dispozici telefonní číslo bezpečnostního kontaktu.

## <a name="218-ensure-that-send-email-notification-for-high-severity-alerts-is-set-to-on"></a>2.18 Zajistěte, aby bylo "Odeslat e-mailové oznámení pro výstrahy vysoké závažnosti" nastaveno na "Zapnuto"

Tento podrobný plán přiřazuje [definici zásad Azure,](../../../policy/overview.md) která vám pomůže zajistit, že oznámení zabezpečení jsou správně povolena.

- E-mailová upozornění na upozornění s vysokou závažností by měla být povolena.

## <a name="219-ensure-that-send-email-also-to-subscription-owners-is-set-to-on"></a>2.19 Zajistěte, aby "Odeslat e-mail také vlastníkům předplatného" bylo nastaveno na "Zapnuto"

Tento podrobný plán přiřazuje [definici zásad Azure,](../../../policy/overview.md) která vám pomůže zajistit, že oznámení zabezpečení jsou správně povolena.

- E-mailové oznámení vlastníkovi předplatného pro výstrahy vysoké závažnosti by měly být povoleny

## <a name="31-ensure-that-secure-transfer-required-is-set-to-enabled"></a>3.1 Ujistěte se, že je "vyžadován bezpečný přenos" nastaven na "Povoleno"

Tento podrobný plán přiřadí [definici zásad Azure,](../../../policy/overview.md) která vám pomůže monitorovat účty úložiště, které umožňují nezabezpečené připojení.

- Měl by se povolit zabezpečený přenos do účtů úložiště

## <a name="37-ensure-default-network-access-rule-for-storage-accounts-is-set-to-deny"></a>3.7 Zajistěte, aby bylo nastaveno výchozí pravidlo přístupu k síti pro účty úložiště, které by bylo odepřeno

Tento podrobný plán přiřadí [definici zásad Azure,](../../../policy/overview.md) která vám pomůže monitorovat účty úložiště, které umožňují neomezený přístup.

- Auditovat neomezený přístup k účtům úložiště v síti

## <a name="38-ensure-trusted-microsoft-services-is-enabled-for-storage-account-access"></a>3.8 Ujistěte se, že je pro přístup k účtu úložiště povolena možnost Důvěryhodné služby Microsoft Services

Tento podrobný plán přiřazuje [definici zásad Azure,](../../../policy/overview.md) která vám pomůže monitorovat účty úložiště, které neumožňují přístup z důvěryhodných služeb Microsoftu.

- Účty úložiště by měly umožňovat přístup z důvěryhodných služeb společnosti Microsoft.

## <a name="41-ensure-that-auditing-is-set-to-on"></a>4.1 Zajistit, aby byl "audit" nastaven na "Zapnuto"

Tento podrobný plán přiřadí [definici zásad Azure,](../../../policy/overview.md) která pomáhá zajistit, že je povoleno auditování serveru SQL. 

- Auditování by mělo být povoleno v rozšířeném nastavení zabezpečení dat na serveru SQL Server.

## <a name="42-ensure-that-auditactiongroups-in-auditing-policy-for-a-sql-server-is-set-properly"></a>4.2 Ujistěte se, že jsou správně nastaveny zásady AuditActionGroups v zásadách "auditování" pro SQL server

Tento podrobný plán přiřadí [definici zásad Azure,](../../../policy/overview.md) která vám pomůže zajistit, že auditování serveru SQL je správně nakonfigurované.

- Nastavení auditování SQL by mělo mít nakonfigurované skupiny akcí pro zachycení kritických aktivit

## <a name="43-ensure-that-auditing-retention-is-greater-than-90-days"></a>4.3 Zajistit, aby uchovávání "auditů" bylo "delší než 90 dní"

Tento podrobný plán přiřadí [definici zásad Azure,](../../../policy/overview.md) která vám pomůže zajistit, že protokoly serveru SQL budou zachovány alespoň 90 dní.

- Sql servery by měly být konfigurovány s auditování retenční dny větší než 90 dnů.

## <a name="44-ensure-that-advanced-data-security-on-a-sql-server-is-set-to-on"></a>4.4 Ujistěte se, že "Rozšířené zabezpečení dat" na serveru SQL je nastaveno na "Zapnuto"

Tento podrobný plán přiřazuje [definici zásad Azure,](../../../policy/overview.md) která vám pomůže zajistit, že je pokročilé zabezpečení dat povoleno na serverech SQL a instancích spravovaných SQL.

- Rozšířené zabezpečení dat by mělo být povoleno ve vašich instancích spravovaných SQL.
- Na serverech SQL by mělo být povoleno rozšířené zabezpečení dat.

## <a name="45-ensure-that-threat-detection-types-is-set-to-all"></a>4.5 Zajistěte, aby byly "typy detekce hrozeb" nastaveny na "Všechny"

Tento podrobný plán přiřazuje definice [zásad Azure,](../../../policy/overview.md) které vám pomohou zajistit, že pokročilá ochrana před hrozbami je správně nakonfigurovaná na serverech SQL a instancích spravovaných SQL.

- Rozšířené typy ochrany před hrozbami by měly být v nastavení rozšířeného zabezpečení dat serveru SQL nastaveny na hodnotu Vše.
- Rozšířené typy ochrany před internetovými hrozbami by měly být nastaveny na hodnotu "Vše" v nastavení rozšířeného zabezpečení dat spravované ho SQL.

## <a name="46-ensure-that-send-alerts-to-is-set"></a>4.6 Ujistěte se, že je nastaveno "Odeslat upozornění"

Tento podrobný plán přiřadí [definici zásad Azure,](../../../policy/overview.md) která vám pomůže zajistit, že pokročilá oznámení o zabezpečení dat jsou správně povolena.

- Pokročilá nastavení zabezpečení dat pro spravovanou instanci SQL by měla obsahovat e-mailovou adresu pro příjem výstrah zabezpečení
- Rozšířené nastavení zabezpečení dat pro SQL server by měl obsahovat e-mailovou adresu pro příjem výstrah zabezpečení

## <a name="47-ensure-that-email-service-and-co-administrators-is-enabled"></a>4.7 Zajistěte, aby "e-mailová služba a spolusprávci" byli "povoleni"

Tento podrobný plán přiřadí [definici zásad Azure,](../../../policy/overview.md) která vám pomůže zajistit, že pokročilá oznámení o zabezpečení dat jsou správně povolena.

- E-mailová oznámení správcům a vlastníkům předplatného by měla být povolena v rozšířeném nastavení zabezpečení dat spravované sql.
- E-mailová oznámení správcům a vlastníkům předplatného by měla být povolena v rozšířeném nastavení zabezpečení dat serveru SQL Server.

## <a name="48-ensure-that-azure-active-directory-admin-is-configured"></a>4.8 Ujistěte se, že je nakonfigurovaný správce služby Azure Active Directory

Tento podrobný plán přiřadí [definici zásad Azure,](../../../policy/overview.md) která vám pomůže zajistit, že správce služby Azure Active Directory je zřízen pro servery SQL.

- Správce služby Azure Active Directory by měl být zřízen pro servery SQL.

## <a name="49-ensure-that-data-encryption-is-set-to-on-on-a-sql-database"></a>4.9 Ujistěte se, že je v databázi SQL nastaveno šifrování dat na zapnuto

Tento podrobný plán přiřadí [definici zásad Azure,](../../../policy/overview.md) která vám pomůže zajistit, že je v databázích SQL povoleno transparentní šifrování dat.

- Transparentní šifrování dat v databázích SQL by mělo být povoleno

## <a name="410-ensure-sql-servers-tde-protector-is-encrypted-with-byok-use-your-own-key"></a>4.10 Ujistěte se, že ochrana TDE serveru SQL server je šifrována pomocí BYOK (Použijte vlastní klíč)

Tento podrobný plán přiřazuje definice [zásad Azure,](../../../policy/overview.md) které vám pomohou zajistit, aby transparentní ochrana šifrovaná data pro servery SQL a instance spravované SQL byla šifrována pomocí vlastního klíče.

- Ochrana TDE spravované SQL by měla být šifrována pomocí vlastního klíče
- Ochrana TDE serveru SQL server by měla být šifrována pomocí vlastního klíče

## <a name="411-ensure-enforce-ssl-connection-is-set-to-enabled-for-mysql-database-server"></a>4.11 Ujistěte se, že je pro databázový server MySQL nastaveno na hodnotu "Vynutit připojení SSL"

Tento podrobný plán přiřazuje [definici zásad Azure,](../../../policy/overview.md) která vám pomůže zajistit, aby databázové servery MySQL vynucovali připojení TLS/SSL.

- Vynutit připojení SSL by mělo být povoleno pro databázové servery MySQL

## <a name="412-ensure-server-parameter-log_checkpoints-is-set-to-on-for-postgresql-database-server"></a>4.12 Ujistěte se, že parametr serveru 'log_checkpoints' je nastaven na 'ON' pro PostgreSQL Database Server

Tento podrobný plán přiřadí [definici zásad Azure,](../../../policy/overview.md) která vám pomůže zajistit, aby databázové servery PostgreSQL zaznamenávalo kontrolní body.

- Protokolovat kontrolní body by měly být povoleny pro databázové servery PostgreSQL

## <a name="413-ensure-enforce-ssl-connection-is-set-to-enabled-for-postgresql-database-server"></a>4.13 Ujistěte se, že je pro databázový server PostgreSQL nastaveno na "Vynutit připojení SSL"

Tento podrobný plán přiřazuje [definici zásad Azure,](../../../policy/overview.md) která vám pomůže zajistit, aby databázové servery PostgreSQL vynucovali připojení TLS/SSL.

- Vynutit připojení SSL by mělo být povoleno pro databázové servery PostgreSQL.

## <a name="414-ensure-server-parameter-log_connections-is-set-to-on-for-postgresql-database-server"></a>4.14 Ujistěte se, že parametr serveru 'log_connections' je nastaven na 'ON' pro PostgreSQL Database Server

Tento podrobný plán přiřadí [definici zásad Azure,](../../../policy/overview.md) která vám pomůže zajistit připojení databázových serverů PostgreSQL protokolu.

- Pro databázové servery PostgreSQL by měla být povolena připojení protokolu.

## <a name="415-ensure-server-parameter-log_disconnections-is-set-to-on-for-postgresql-database-server"></a>4.15 Ujistěte se, že parametr serveru 'log_disconnections' je nastaven na 'ON' pro PostgreSQL Database Server

Tento podrobný plán přiřadí [definici zásad Azure,](../../../policy/overview.md) která vám pomůže zajistit odpojení databázových serverů PostgreSQL.

- Odpojení by měla být zaznamenána pro databázové servery PostgreSQL.

## <a name="416-ensure-server-parameter-log_duration-is-set-to-on-for-postgresql-database-server"></a>4.16 Ujistěte se, že parametr serveru 'log_duration' je nastaven na 'ON' pro PostgreSQL Database Server

Tento podrobný plán přiřadí [definici zásad Azure,](../../../policy/overview.md) která vám pomůže zajistit, aby databázové servery PostgreSQL zaznamenávalo dobu trvání dokončených příkazů.

- Doba trvání protokolu by měla být povolena pro databázové servery PostgreSQL.

## <a name="417-ensure-server-parameter-connection_throttling-is-set-to-on-for-postgresql-database-server"></a>4.17 Ujistěte se, že parametr serveru 'connection_throttling' je nastaven na 'ON' pro PostgreSQL Database Server

Tento podrobný plán přiřadí [definici zásad Azure,](../../../policy/overview.md) která vám pomůže zmírnit útoky hrubou silou na databázových serverech PostgreSQL.

- Omezení připojení by mělo být povoleno pro databázové servery PostgreSQL.

## <a name="419-ensure-that-azure-active-directory-admin-is-configured"></a>4.19 Ujistěte se, že je nakonfigurovaný správce služby Azure Active Directory

Tento podrobný plán přiřadí [definici zásad Azure,](../../../policy/overview.md) která vám pomůže zajistit, že správce služby Azure Active Directory je zřízen pro servery SQL. Srovnávací test nadace CIS Microsoft Azure Foundations obsahuje toto doporučení. jedná se však o duplikát [doporučení 4.8](#48-ensure-that-azure-active-directory-admin-is-configured).

- Správce služby Azure Active Directory by měl být zřízen pro servery SQL.

## <a name="511-ensure-that-a-log-profile-exists"></a>5.1.1 Ujistěte se, že existuje profil protokolu

Tento podrobný plán přiřadí [definici zásad Azure,](../../../policy/overview.md) která vám pomůže zajistit, že profil protokolu existuje pro všechna předplatná Azure. 

- Předplatná Azure by měla mít profil protokolu pro protokol aktivit

## <a name="512-ensure-that-activity-log-retention-is-set-365-days-or-greater"></a>5.1.2 Ujistěte se, že uchovávání protokolu aktivit je nastaveno na 365 dní nebo více

Tento podrobný plán přiřadí [definici zásad Azure,](../../../policy/overview.md) která vám pomůže zajistit, aby protokoly aktivit byly zachovány po dobu nejméně jednoho roku.

- Protokol aktivit by měl být uchováván po dobu nejméně jednoho roku.

## <a name="513-ensure-audit-profile-captures-all-the-activities"></a>5.1.3 Zajistěte, aby profil auditu zachytil všechny činnosti

Tento podrobný plán přiřadí [definici zásad Azure,](../../../policy/overview.md) která vám pomůže zajistit, že profil protokolu je správně nakonfigurovaný.

- Profil protokolu Azure Monitor by měl shromažďovat protokoly pro kategorie "zápis", "odstranit" a "akce"

## <a name="514-ensure-the-log-profile-captures-activity-logs-for-all-regions-including-global"></a>5.1.4 Ujistěte se, že profil protokolu zachycuje protokoly aktivit pro všechny oblasti včetně globálních

Tento podrobný plán přiřadí [definici zásad Azure,](../../../policy/overview.md) která vám pomůže zajistit, že profil protokolu je správně nakonfigurovaný.

- Azure Monitor by měl shromažďovat protokoly aktivit ze všech oblastí.

## <a name="517-ensure-that-logging-for-azure-keyvault-is-enabled"></a>5.1.7 Ujistěte se, že protokolování pro Azure KeyVault je povoleno

Tento podrobný plán přiřadí [definici zásad Azure,](../../../policy/overview.md) která vám pomůže zajistit, že diagnostické protokoly jsou povolené pro trezory klíčů.

- Diagnostické protokoly v trezoru klíčů by měly být povoleny

## <a name="65-ensure-that-network-watcher-is-enabled"></a>6.5 Ujistěte se, že je sledovací proces sítě povolen

Tento podrobný plán přiřadí [definici zásad Azure,](../../../policy/overview.md) která vám pomůže zajistit, že sledování sítě je povoleno pro všechny oblasti, kde se nasazují prostředky. Tato zásada vyžaduje pole parametrů, které určuje všechny příslušné oblasti. Výchozí hodnota v této definici iniciativy zásad je "eastus".

- Sledování sítě by mělo být povoleno.

## <a name="71-ensure-that-os-disk-are-encrypted"></a>7.1 Ujistěte se, že je "disk operačního systému" šifrován

Tento podrobný plán přiřadí [definici zásad Azure,](../../../policy/overview.md) která vám pomůže zajistit, že šifrování disku je povoleno na virtuálních počítačích.

- Šifrování disku by mělo být použito na virtuálních počítačích.

## <a name="72-ensure-that-data-disks-are-encrypted"></a>7.2 Zajistěte, aby byly "datové disky" šifrovány

Tento podrobný plán přiřadí [definici zásad Azure,](../../../policy/overview.md) která vám pomůže zajistit, že šifrování disku je povoleno na virtuálních počítačích.

- Šifrování disku by mělo být použito na virtuálních počítačích.

## <a name="73-ensure-that-unattached-disks-are-encrypted"></a>7.3 Ujistěte se, že jsou "nepřipojené disky" šifrovány

Tento podrobný plán přiřadí [definici zásad Azure,](../../../policy/overview.md) která vám pomůže zajistit, aby byly zašifrované nepřipojené disky.

- Nepřipojené disky by měly být šifrovány

## <a name="74-ensure-that-only-approved-extensions-are-installed"></a>7.4 Ujistěte se, že jsou instalována pouze schválená rozšíření

Tento podrobný plán přiřadí [definici zásad Azure,](../../../policy/overview.md) která vám pomůže zajistit, že jsou nainstalována pouze schválená rozšíření virtuálních strojů. Tato zásada vyžaduje pole parametrů, které určuje všechna schválená rozšíření virtuálních strojů. Tato definice iniciativy zásad obsahuje navrhované výchozí hodnoty, které by zákazníci měli ověřit. 

 - Měla by být nainstalována pouze schválená rozšíření virtuálních zařízení.

## <a name="75-ensure-that-the-latest-os-patches-for-all-virtual-machines-are-applied"></a>7.5 Ujistěte se, že jsou použity nejnovější opravy operačního režimu pro všechny virtuální počítače

Tento podrobný plán přiřadí [definici zásad Azure,](../../../policy/overview.md) která vám pomůže zajistit, aby se aktualizace systému nainstalovaly na virtuálních počítačích.

- Na počítače by se měly nainstalovat aktualizace systému

## <a name="76-ensure-that-the-endpoint-protection-for-all-virtual-machines-is-installed"></a>7.6 Ujistěte se, že je nainstalována ochrana koncového bodu pro všechny virtuální počítače

Tento podrobný plán přiřadí [definici zásad Azure,](../../../policy/overview.md) která vám pomůže zajistit, že ochrana koncového bodu je povolená na virtuálních počítačích.

- Sledování chybějící ochrany koncového bodu v Azure Security Center

## <a name="84-ensure-the-key-vault-is-recoverable"></a>8.4 Ujistěte se, že trezor klíčů je obnovitelný

Tento podrobný plán přiřadí [definici zásad Azure,](../../../policy/overview.md) která vám pomůže zajistit, že objekty trezoru klíčů jsou obnovitelné v případě náhodného odstranění.

- Objekty trezoru klíčů by měly být obnovitelné

## <a name="85-enable-role-based-access-control-rbac-within-azure-kubernetes-services"></a>8.5 Povolení řízení přístupu na základě rolí (RBAC) v rámci služeb Azure Kubernetes Services

Tento podrobný plán přiřazuje [definici zásad Azure,](../../../policy/overview.md) která vám pomůže zajistit, aby se řízení přístupu na základě rolí používalo ke spravovaným oprávněním v clusterech služeb Kubernetes.

- \[Náhled\]: Řízení přístupu na základě rolí (RBAC) by mělo být použito ve službách Kubernetes

## <a name="92-ensure-web-app-redirects-all-http-traffic-to-https-in-azure-app-service"></a>9.2 Ujistěte se, že webová aplikace přesměruje veškerý provoz HTTP na HTTPS ve službě Azure App Service

Tento podrobný plán přiřazuje [definici zásad Azure,](../../../policy/overview.md) která vám pomůže zajistit, aby webové aplikace byly přístupné jenom přes zabezpečená připojení.

- Webová aplikace by měla být přístupná pouze přes protokol HTTPS.

## <a name="93-ensure-web-app-is-using-the-latest-version-of-tls-encryption"></a>9.3 Ujistěte se, že webová aplikace používá nejnovější verzi šifrování TLS

Tento podrobný plán přiřazuje definice [zásad Azure,](../../../policy/overview.md) které vám pomůžou zajistit, aby webové aplikace používaly nejnovější verzi TLS.

- Nejnovější verze TLS by měla být použita ve vaší aplikaci API
- Nejnovější verze TLS by měla být použita ve vaší funkční aplikaci
- Ve webové aplikaci by měla být použita nejnovější verze TLS.

## <a name="94-ensure-the-web-app-has-client-certificates-incoming-client-certificates-set-to-on"></a>9.4 Ujistěte se, že webová aplikace má "Klientské certifikáty (příchozí klientské certifikáty)' nastavené na "Zapnuto"

Tento podrobný plán přiřazuje definice [zásad Azure,](../../../policy/overview.md) které vám pomohou zajistit, aby se do webové aplikace dostali jenom klienti s platnými certifikáty.

- Ujistěte se, že aplikace API má "Klientské certifikáty (příchozí klientské certifikáty)' nastavené na "Zapnuto"
- Zajistit, aby aplikace funkce byla nastavena na hodnotu Klientské certifikáty (příchozí klientské certifikáty), nastavené na zapnuto.
- Ujistěte se, že webová aplikace má "Klientské certifikáty (příchozí klientské certifikáty)' nastavené na "Zapnuto"

## <a name="95-ensure-that-register-with-azure-active-directory-is-enabled-on-app-service"></a>9.5 Ujistěte se, že je ve službě App Service povolena registrace pomocí služby Azure Active Directory.

Tento podrobný plán přiřazuje definice [zásad Azure,](../../../policy/overview.md) které vám pomůžou zajistit, aby webové aplikace používaly spravovanou identitu.

- Ujistěte se, že je v aplikaci API povolena registrace pomocí Služby Azure Active Directory.
- Ujistěte se, že je v aplikaci Function App povolena registrace pomocí služby Azure Active Directory.
- Ujistěte se, že je v aplikaci WEB App povolena registrace pomocí služby Azure Active Directory.

## <a name="96-ensure-that-net-framework-version-is-the-latest-if-used-as-a-part-of-the-web-app"></a>9.6 Ujistěte se, že verze rozhraní ".Net Framework" je nejnovější, pokud je používána jako součást webové aplikace

Tento podrobný plán přiřazuje definice [zásad Azure,](../../../policy/overview.md) které vám pomůžou zajistit, aby webové aplikace používaly nejnovější verzi rozhraní .Net Framework.

- Ujistěte se, že verze rozhraní ".Net Framework" je nejnovější, pokud se používá jako součást aplikace API
- Ujistěte se, že verze rozhraní ".Net Framework" je nejnovější, pokud je použita jako součást aplikace function App
- Ujistěte se, že verze rozhraní .Net Framework je nejnovější, pokud je použita jako součást webové aplikace

## <a name="97-ensure-that-php-version-is-the-latest-if-used-to-run-the-web-app"></a>9.7 Ujistěte se, že "verze PHP" je nejnovější, pokud se používá ke spuštění webové aplikace

Tento podrobný plán přiřazuje definice [zásad Azure,](../../../policy/overview.md) které vám pomůžou zajistit, aby webové aplikace používaly nejnovější verzi PHP.

- Ujistěte se, že "php verze" je nejnovější, pokud se používá jako součást aplikace Api
- Ujistěte se, že "php verze" je nejnovější, pokud je použit jako součást aplikace Funkce
- Ujistěte se, že "PHP verze" je nejnovější, pokud je použit jako součást webové aplikace

## <a name="98-ensure-that-python-version-is-the-latest-if-used-to-run-the-web-app"></a>9.8 Ujistěte se, že "verze Pythonu" je nejnovější, pokud se používá ke spuštění webové aplikace

Tento podrobný plán přiřazuje definice [zásad Azure,](../../../policy/overview.md) které vám pomůžou zajistit, aby webové aplikace používaly nejnovější verzi Pythonu.

- Ujistěte se, že "Verze Pythonu" je nejnovější, pokud se používá jako součást aplikace Api
- Ujistěte se, že "Verze Pythonu" je nejnovější, pokud je použita jako součást aplikace Function
- Ujistěte se, že "Verze Pythonu" je nejnovější, pokud je použita jako součást webové aplikace

## <a name="99-ensure-that-java-version-is-the-latest-if-used-to-run-the-web-app"></a>9.9 Ujistěte se, že "java verze" je nejnovější, pokud se používá ke spuštění webové aplikace

Tento podrobný plán přiřazuje definice [zásad Azure,](../../../policy/overview.md) které vám pomůžou zajistit, aby webové aplikace používaly nejnovější verzi Javy.

- Ujistěte se, že "Java verze" je nejnovější, pokud se používá jako součást aplikace Api
- Ujistěte se, že "Java verze" je nejnovější, pokud se používá jako součást aplikace Funtion
- Ujistěte se, že "Java verze" je nejnovější, pokud se používá jako součást webové aplikace

## <a name="910-ensure-that-http-version-is-the-latest-if-used-to-run-the-web-app"></a>9.10 Ujistěte se, že "verze HTTP" je nejnovější, pokud se používá ke spuštění webové aplikace

Tento podrobný plán přiřazuje definice [zásad Azure,](../../../policy/overview.md) které vám pomůžou zajistit, aby webové aplikace používaly nejnovější verzi protokolu HTTP.

- Ujistěte se, že "verze HTTP" je nejnovější, pokud se používá ke spuštění aplikace Api
- Ujistěte se, že 'VERZE HTTP' je nejnovější, pokud se používá ke spuštění aplikace Funkce
- Ujistěte se, že "verze HTTP" je nejnovější, pokud se používá ke spuštění webové aplikace


## <a name="next-steps"></a>Další kroky

Teď, když jste zkontrolovali mapování ovládacích prvku podrobného plánu benchmarku CIS Microsoft Azure Foundations, navštivte následující články, kde se dozvíte o podrobném plánu, nebo navštivte zásady Azure na webu Azure portal a přiřaďte iniciativu:

> [!div class="nextstepaction"]
> [Podrobný plán srovnávacího testu nadace CIS Microsoft Azure Foundations – přehled](./index.md)
> [podrobného plánu srovnávacího testu nadace CIS Microsoft Azure Foundations – nasazení kroků](./deploy.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Přečtěte si o [životním cyklu podrobného plánu](../../concepts/lifecycle.md).
- Pochopit, jak používat [statické a dynamické parametry](../../concepts/parameters.md).
- Naučte se přizpůsobit [pořadí sekvencování podrobných plánů](../../concepts/sequencing-order.md).
- Zjistěte, jak využít [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md).
- Přečtěte si, jak [aktualizovat existující přiřazení](../../how-to/update-existing-assignments.md).