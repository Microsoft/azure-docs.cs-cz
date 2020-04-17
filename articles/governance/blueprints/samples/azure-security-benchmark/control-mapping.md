---
title: Ovládací prvky ukázkového plánu azure benchmarku zabezpečení
description: Řízení mapování ukázky plánu azure standardu zabezpečení na zásady Azure.
ms.date: 04/16/2020
ms.topic: sample
ms.openlocfilehash: 0f2d24d7d8c9f7ce2568f11b15e65ed1fcd02afb
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538730"
---
# <a name="control-mapping-of-the-azure-security-benchmark-blueprint-sample"></a>Mapování ovládacího prvku ukázky podrobného plánu azure standardu zabezpečení

V následujícím článku je podrobně popisuje, jak azure plán y Azure Security Benchmark ukázka mapuje na ovládací prvky Azure Security Benchmark. Další informace o ovládacích prvcích naleznete v [tématu Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/overview).

Následující mapování jsou pro ovládací **prvky Azure Security Benchmark.** Pomocí navigace vpravo přejděte přímo na konkrétní mapování ovládacího prvku. Mapované ovládací prvky se implementují pomocí iniciativy [Zásad azure.](../../../policy/overview.md) Pokud chcete zkontrolovat celou iniciativu, otevřete **zásady** na webu Azure portal a vyberte stránku **Definice.** Potom najděte a vyberte ** \[náhled\]: Auditujte doporučení azure security benchmarku a nasaďte konkrétní podpůrnou** iniciativu integrovaných zásad rozšíření virtuálních počítače.

> [!IMPORTANT]
> Každý ovládací prvek níže je přidružen k jedné nebo více definic [zásad Azure.](../../../policy/overview.md) Tyto zásady vám mohou pomoci [posoudit dodržování](../../../policy/how-to/get-compliance-data.md) ovládacího prvku; však často není 1:1 nebo úplnou shodu mezi ovládacím prvkem a jednu nebo více zásad. Jako **takový, kompatibilní** v zásadách Azure odkazuje pouze na zásady samotné; Tím nezajistíte, že jste plně kompatibilní se všemi požadavky ovládacího prvku. Kromě toho může standard dodržování předpisů zahrnovat ovládací prvky, které nejsou v tuto chvíli adresovány žádnými definicemi zásad Azure. Dodržování předpisů v zásadách Azure je tedy pouze částečným zobrazením celkového stavu dodržování předpisů. Přidružení mezi ovládacími prvky a definicemi zásad Azure pro tento ukázkový plán dodržování předpisů se může v průběhu času měnit. Chcete-li zobrazit historii změn, podívejte se na [historii potvrzení GitHubu](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/azure-security-benchmark/control-mapping.md).

## <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1 Ochrana prostředků pomocí skupin zabezpečení sítě nebo brány Azure Firewall ve virtuální síti

- Podsítě by měly být přidruženy ke skupině zabezpečení sítě.
- Adaptivní síť Kalení doporučení by měla být použita na internetu čelí virtuální počítače
- Virtuální počítače by měly být připojené ke schválené virtuální síti.
- Virtuální počítače orientované na Internet by měly být chráněny pomocí skupin zabezpečení sítě.
- Služba Service Bus by měla používat koncový bod služby virtuální sítě.
- Služba App Service by měla používat koncový bod služby virtuální sítě.
- SQL Server by měl používat koncový bod služby virtuální sítě.
- Centrum událostí by mělo používat koncový bod služby virtuální sítě.
- Cosmos DB by měl používat koncový bod služby virtuální sítě
- Trezor klíčů by měl používat koncový bod služby virtuální sítě.
- Auditovat neomezený přístup k účtům úložiště v síti
- Účty úložiště by měly používat koncový bod služby virtuální sítě.
- Registr kontejnerů by měl používat koncový bod služby virtuální sítě.
- Virtuální sítě by měly používat zadanou bránu virtuální sítě.
- Autorizované rozsahy IP adres by měly být definovány ve službách Kubernetes
- \[Náhled\]: Předávání IP adres na vašem virtuálním počítači by mělo být zakázáno
- Virtuální počítače orientované na Internet by měly být chráněny pomocí skupin zabezpečení sítě.
- Na virtuálních počítačích by se mělo používat řízení přístupu k síti podle potřeby
- Porty pro správu by měly být uzavřeny na virtuálních počítačích

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2 Sledování a protokolování konfigurace a provozu sítí, podsítí a síťových rozhraní

- Sledování sítě by mělo být povoleno.

## <a name="13-protect-critical-web-applications"></a>1.3 Ochrana kritických webových aplikací

- Ujistěte se, že webová aplikace má "Klientské certifikáty (příchozí klientské certifikáty)' nastavené na "Zapnuto"
- CORS by neměl povolit přístup k webovým aplikacím pro každý prostředek.
- CORS by neměl a umožnit každému zdroji přístup k vašim funkčním aplikacím
- CORS by neměl a) neumožňovat všem prostředkům přístup k vaší aplikaci API.
- Vzdálené ladění by mělo být vypnuto pro webové aplikace.
- Vzdálené ladění by mělo být vypnuto pro aplikace funkcí
- Vzdálené ladění by mělo být vypnuto pro aplikace rozhraní API.

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4 Odepřít komunikaci se známými škodlivými IP adresami

- DDoS Protection Standard by měla být povolena
- Na virtuálních počítačích by se mělo používat řízení přístupu k síti podle potřeby
- Adaptivní síť Kalení doporučení by měla být použita na internetu čelí virtuální počítače

## <a name="15-record-network-packets-and-flow-logs"></a>1.5 Záznam síťových paketů a protokolů toku

- Sledování sítě by mělo být povoleno.

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11 Používání automatizovaných nástrojů ke sledování konfigurace síťových prostředků a zjišťování změn

- Nasazení předpokladů pro audit konfigurace virtuálních počítačů se systémem Windows v části Možnosti zabezpečení – přístup k síti
- Zobrazit výsledky auditu z konfigurací virtuálních počítačů se systémem Windows v části Možnosti zabezpečení – síťový klient společnosti Microsoft
- Nasazení předpokladů pro audit konfigurace virtuálních počítačů se systémem Windows v části Možnosti zabezpečení – zabezpečení sítě
- Zobrazit výsledky auditu z konfigurací virtuálních počítačů se systémem Windows v části Možnosti zabezpečení – zabezpečení sítě
- Nasazení předpokladů pro audit konfigurace virtuálních počítačů se systémem Windows v části Možnosti zabezpečení – microsoft network server
- Zobrazit výsledky auditu z konfigurací virtuálních počítačů se systémem Windows v části Možnosti zabezpečení – microsoft network server
- Nasazení předpokladů pro audit konfigurace virtuálních počítačů se systémem Windows v části Šablony pro správu – síť
- Zobrazit výsledky auditu z konfigurací virtuálních počítačů se systémem Windows v části Šablony pro správu – síť

## <a name="22-configure-central-security-log-management"></a>2.2 Konfigurace centrální správy protokolu zabezpečení

- Agent Log Analytics by měl být nainstalován na virtuálních počítačích
- Agent Log Analytics by měl být nainstalován v škálovacích sadách virtuálních strojů.
- Nasazení požadavků pro auditování virtuálních počítačů se systémem Windows, ke kterým není připojen agent Log Analytics podle očekávání
- Zobrazit výsledky auditu z virtuálních počítačů se systémem Windows, ke kterým není připojen agent Analýzy protokolů podle očekávání
- Profil protokolu Azure Monitor by měl shromažďovat protokoly pro kategorie "zápis", "odstranit" a "akce"
- Azure Monitor by měl shromažďovat protokoly aktivit ze všech oblastí.
- Automatické zřizování agenta monitorování Analýzy protokolů by mělo být povoleno ve vašem předplatném.

## <a name="23-enable-audit-logging-for-azure-resources"></a>2.3 Povolení protokolování auditu pro prostředky Azure

- Diagnostické protokoly v Azure Data Lake Store by měly být povolené
- Diagnostické protokoly v logic apps by měly být povoleny
- Diagnostické protokoly v centru IoT Hub by měly být povoleny
- Diagnostické protokoly v dávkových účtech by měly být povoleny.
- Diagnostické protokoly ve škálovacích sadách virtuálních strojů by měly být povoleny
- Diagnostické protokoly v centru událostí by měly být povoleny
- Diagnostické protokoly ve vyhledávacích službách by měly být povoleny.
- Diagnostické protokoly ve službách App Services by měly být povoleny
- Diagnostické protokoly v Data Lake Analytics by měly být povoleny
- Diagnostické protokoly v trezoru klíčů by měly být povoleny
- Diagnostické protokoly v service bus by měly být povoleny
- Diagnostické protokoly ve službě Azure Stream Analytics by měly být povolené
- Auditování na serveru SQL by mělo být povoleno.
- Auditování nastavení diagnostiky

## <a name="24-collect-security-logs-from-operating-systems"></a>2.4 Shromažďování protokolů zabezpečení z operačních systémů

- Automatické zřizování agenta monitorování Analýzy protokolů by mělo být povoleno ve vašem předplatném.
- Agent Log Analytics by měl být nainstalován na virtuálních počítačích
- Agent Log Analytics by měl být nainstalován v škálovacích sadách virtuálních strojů.
- Nasazení požadavků pro auditování virtuálních počítačů se systémem Windows, ke kterým není připojen agent Log Analytics podle očekávání
- Zobrazit výsledky auditu z virtuálních počítačů se systémem Windows, ke kterým není připojen agent Analýzy protokolů podle očekávání

## <a name="27-enable-alerts-for-anomalous-activity"></a>2.7 Povolit upozornění na anomální aktivitu

- Měla by být vybrána standardní cenová úroveň Centra zabezpečení.
- Na serverech SQL by mělo být povoleno rozšířené zabezpečení dat.
- Rozšířené zabezpečení dat by mělo být povoleno ve vašich instancích spravovaných SQL.
- Rozšířené typy ochrany před hrozbami by měly být v nastavení rozšířeného zabezpečení dat serveru SQL nastaveny na hodnotu Vše.
- Rozšířené typy ochrany před internetovými hrozbami by měly být nastaveny na hodnotu "Vše" v nastavení rozšířeného zabezpečení dat spravované ho SQL.

## <a name="28-centralize-anti-malware-logging"></a>2.8 Centralizovat protokolování antimalwaru

- Microsoft Antimalware pro Azure by měl být nakonfigurován tak, aby automaticky aktualizoval podpisy ochrany.
- Sledování chybějící ochrany koncového bodu v Azure Security Center
- Řešení ochrany koncových bodů by mělo být nainstalováno na škálovacích sadách virtuálních strojů.

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1 Vedení soupisu administrativních účtů

- Pro vaše předplatné by měli být určeni maximálně 3 vlastníci.
- K vašemu předplatnému by mělo být přiřazeno více než jednoho vlastníka.
- Z předplatného by měly být odebrány externí účty s oprávněními vlastníka.
- Zastaralé účty s oprávněními vlastníka by měly být odebrány z vašeho předplatného

## <a name="33-use-dedicated-administrative-accounts"></a>3.3 Používání vyhrazených administrativních účtů

- Nasazení požadavků pro auditování virtuálních aplikací systému Windows, ve kterých skupina Administrators neobsahuje pouze zadané členy
- Zobrazit výsledky auditu z virtuálních aplikací systému Windows, ve kterých skupina Administrators neobsahuje pouze zadané členy
- Nasazení požadavků pro auditování virtuálních aplikací systému Windows, ve kterých skupina Administrators neobsahuje všechny zadané členy
- Zobrazit výsledky auditu z virtuálních aplikací systému Windows, ve kterých skupina Administrators neobsahuje všechny zadané členy
- Nasazení požadavků pro auditování virtuálních účtů systému Windows, ve kterých skupina Administrators obsahuje některý ze zadaných členů
- Zobrazit výsledky auditu z virtuálních aplikací systému Windows, ve kterých skupina Administrators obsahuje některý ze zadaných členů
- Pro vaše předplatné by měli být určeni maximálně 3 vlastníci.
- K vašemu předplatnému by mělo být přiřazeno více než jednoho vlastníka.

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5 Použití vícefaktorového ověřování pro veškerý přístup založený na službě Azure Active Directory

- Vícefaktorové informace by měly být povoleny u účtů s oprávněními vlastníka k vašemu předplatnému.
- MFA by měly mít povolené účty s oprávněními k zápisu ve vašem předplatném.
- Vícefaktorové informace by měly být povoleny u účtů s oprávněními ke čtení v rámci vašeho předplatného.

## <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7 Protokolování a upozornění na podezřelou aktivitu z administrativních účtů

- Měla by být vybrána standardní cenová úroveň Centra zabezpečení.

## <a name="39-use-azure-active-directory"></a>3.9 Použití služby Azure Active Directory

- Správce služby Azure Active Directory by měl být zřízen pro servery SQL.
- Clustery Service Fabric by měly používat azure active directory jenom pro ověřování klientů.
- Ujistěte se, že je v aplikaci API povolena registrace pomocí Služby Azure Active Directory.
- Ujistěte se, že je v aplikaci WEB App povolena registrace pomocí služby Azure Active Directory.
- Ujistěte se, že je v aplikaci Function App povolena registrace pomocí služby Azure Active Directory.

## <a name="310-regularly-review-and-reconcile-user-access"></a>3.10 Pravidelně kontrolujte a slaďujte přístup uživatelů

- Zastaralé účty by měly být odebrány z vašeho předplatného
- Zastaralé účty s oprávněními vlastníka by měly být odebrány z vašeho předplatného
- Z předplatného by měly být odebrány externí účty s oprávněním ke čtení.
- Z předplatného by měly být odebrány externí účty s oprávněními k zápisu.
- Z předplatného by měly být odebrány externí účty s oprávněními vlastníka.

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1 Udržovat soupis citlivých informací

- Citlivá data ve vašich databázích SQL by měla být klasifikována

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4 Šifrování všech citlivých informací při přepravě

- Měl by se povolit zabezpečený přenos do účtů úložiště
- Nejnovější verze TLS by měla být použita ve vaší aplikaci API
- Ve webové aplikaci by měla být použita nejnovější verze TLS.
- Nejnovější verze TLS by měla být použita ve vaší funkční aplikaci
- Aplikace function by měla být přístupná pouze přes protokol HTTPS
- Webová aplikace by měla být přístupná pouze přes protokol HTTPS.
- Aplikace API by měla být přístupná pouze přes protokol HTTPS
- Vynutit připojení SSL by mělo být povoleno pro databázové servery MySQL
- Vynutit připojení SSL by mělo být povoleno pro databázové servery PostgreSQL.
- Měla by být povolena pouze zabezpečená připojení k mezipaměti Redis.

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5 Použití aktivního nástroje pro zjišťování k identifikaci citlivých dat

- Citlivá data ve vašich databázích SQL by měla být klasifikována
- Na serverech SQL by mělo být povoleno rozšířené zabezpečení dat.
- Rozšířené zabezpečení dat by mělo být povoleno ve vašich instancích spravovaných SQL.

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6 Použití Azure RBAC k řízení přístupu k prostředkům

- Řízení přístupu na základě rolí (RBAC) by mělo být použito ve službách Kubernetes
- Auditování použití vlastních pravidel RBAC

## <a name="48-encrypt-sensitive-information-at-rest"></a>4.8 Šifrování citlivých informací v klidovém stavu

- Transparentní šifrování dat v databázích SQL by mělo být povoleno
- Šifrování disku by mělo být použito na virtuálních počítačích.
- Nepřipojené disky by měly být šifrovány
- Ochrana TDE serveru SQL server by měla být šifrována pomocí vlastního klíče
- Ochrana TDE spravované SQL by měla být šifrována pomocí vlastního klíče
- Proměnné účtu automatizace by měly být šifrovány
- Clustery Service Fabric by měly mít vlastnost ClusterProtectionLevel nastavenou na EncryptAndSign.

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9 Protokolování a upozornění na změny kritických prostředků Azure

- Azure Monitor by měl shromažďovat protokoly aktivit ze všech oblastí.

## <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1 Spuštění automatizovaných nástrojů pro vyhledávání chyb zabezpečení

- Posouzení ohrožení zabezpečení by mělo být povoleno na serverech SQL
- Posouzení ohrožení zabezpečení by mělo být povoleno ve vašich instancích spravovaných SQL.
- \[Předběžné\] posouzení ohrožení zabezpečení by mělo být povoleno ve virtuálních počítačích
- Nastavení posouzení ohrožení zabezpečení pro sql server by měla obsahovat e-mailovou adresu pro příjem zpráv o skenování

## <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2 Nasazení řešení pro správu automatických oprav operačního systému

- Na počítače by se měly nainstalovat aktualizace systému
- Aktualizace systému na škálovacísady virtuálních strojů by měly být nainstalovány
- Ujistěte se, že verze rozhraní .NET Framework je nejnovější, pokud je použita jako součást aplikace funkce
- Ujistěte se, že verze rozhraní .NET Framework je nejnovější, pokud je použita jako součást webové aplikace
- Ujistěte se, že verze rozhraní .NET Framework je nejnovější, pokud se používá jako součást aplikace ROZHRANÍ API

## <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3 Nasazení automatizovaného řešení pro správu softwarových oprav od jiných výrobců

- Ujistěte se, že "php verze" je nejnovější, pokud se používá jako součást aplikace Api
- Ujistěte se, že "PHP verze" je nejnovější, pokud je použit jako součást webové aplikace
- Ujistěte se, že "php verze" je nejnovější, pokud je použit jako součást aplikace Funkce
- Ujistěte se, že "Java verze" je nejnovější, pokud se používá jako součást webové aplikace
- Ujistěte se, že "Java verze" je nejnovější, pokud se používá jako součást aplikace Funkce
- Ujistěte se, že "Java verze" je nejnovější, pokud se používá jako součást aplikace Api
- Ujistěte se, že "Verze Pythonu" je nejnovější, pokud je použita jako součást webové aplikace
- Ujistěte se, že "Verze Pythonu" je nejnovější, pokud je použita jako součást aplikace Function
- Ujistěte se, že "Verze Pythonu" je nejnovější, pokud se používá jako součást aplikace Api
- Služby Kubernetes by měly být upgradovány na nezranitelnou verzi Kubernetes

## <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5 Použití procesu hodnocení rizik a upřednostnění nápravy zjištěných zranitelných míst

- Chyby zabezpečení by měly být opraveny řešením pro posouzení zranitelnosti
- Chyby zabezpečení v konfiguraci zabezpečení na vašich počítačích by měly být opraveny
- Chyby zabezpečení v konfiguraci zabezpečení ve škálovacích sadách virtuálních počítačů by měly být opraveny.
- Chyby zabezpečení v konfiguracích zabezpečení kontejnerů by měly být opraveny
- Chyby zabezpečení v databázích SQL by měly být opraveny

## <a name="68-use-only-approved-applications"></a>6.8 Používejte pouze schválené aplikace

- Měla by být vybrána standardní cenová úroveň Centra zabezpečení.
- Adaptivní ovládací prvky aplikací by měly být povoleny na virtuálních počítačích

## <a name="69-use-only-approved-azure-services"></a>6.9 Používejte jenom schválené služby Azure

- Virtuální počítače by se měly migrovat do nových prostředků Azure Resource Manageru.
- Účty úložiště by se měly migrovat do nových prostředků Azure Resource Manageru.

## <a name="610-implement-approved-application-list"></a>6.10 Implementace schváleného seznamu žádostí

- Měla by být vybrána standardní cenová úroveň Centra zabezpečení.
- Adaptivní ovládací prvky aplikací by měly být povoleny na virtuálních počítačích

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3 Udržovat zabezpečené konfigurace prostředků Azure

- \[Náhled\]: Zásady zabezpečení podbytů by měly být definovány ve službách Kubernetes

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4 Udržovat bezpečné konfigurace operačního systému

- Chyby zabezpečení v konfiguraci zabezpečení na vašich počítačích by měly být opraveny
- Chyby zabezpečení v konfiguracích zabezpečení kontejnerů by měly být opraveny
- Chyby zabezpečení v konfiguraci zabezpečení ve škálovacích sadách virtuálních počítačů by měly být opraveny.

## <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9 Implementace automatického monitorování konfigurace pro služby Azure

- \[Náhled\]: Zásady zabezpečení podbytů by měly být definovány ve službách Kubernetes

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10 Implementace automatického monitorování konfigurace operačních systémů

- Chyby zabezpečení v konfiguraci zabezpečení na vašich počítačích by měly být opraveny
- Chyby zabezpečení v konfiguracích zabezpečení kontejnerů by měly být opraveny
- Chyby zabezpečení v konfiguraci zabezpečení ve škálovacích sadách virtuálních počítačů by měly být opraveny.

## <a name="711-manage-azure-secrets-securely"></a>7.11 Bezpečná správa tajných kódů Azure

- Objekty trezoru klíčů by měly být obnovitelné

## <a name="712-manage-identities-securely-and-automatically"></a>7.12 Bezpečná a automatická správa identit

- Spravovanou identitu by měla být použita ve vaší aplikaci funkce
- Spravovanou identitu by měla být použita ve webové aplikaci.
- Spravovanou identitu by se měla používat v aplikaci API.

## <a name="81-use-centrally-managed-anti-malware-software"></a>8.1 Používejte centrálně spravovaný antimalwarový software

- Sledování chybějící ochrany koncového bodu v Azure Security Center
- Řešení ochrany koncových bodů by mělo být nainstalováno na škálovacích sadách virtuálních strojů.

## <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2 Předprohledávací soubory, které se mají nahrát do nevýpočetních prostředků Azure

- Měla by být vybrána standardní cenová úroveň Centra zabezpečení.

## <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3 Zajistěte aktualizaci antimalwarového softwaru a podpisů

- Microsoft Antimalware pro Azure by měl být nakonfigurován tak, aby automaticky aktualizoval podpisy ochrany.

## <a name="91-ensure-regular-automated-back-ups"></a>9.1 Zajistěte pravidelné automatické zálohování

- Pro Azure SQL Databases by měla být povolena dlouhodobá geograficky redundantní záloha.
- Geograficky redundantní záloha by měla být povolena pro Azure Database for MySQL
- Geograficky redundantní záloha by měla být povolena pro Azure Database for PostgreSQL
- Geograficky redundantní záloha by měla být povolena pro Azure Database pro MariaDB
- Azure Backup by měla být povolená pro virtuální počítače

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2 Proveďte kompletní zálohy systému a zálohujte všechny klienty spravované klíče

- Pro Azure SQL Databases by měla být povolena dlouhodobá geograficky redundantní záloha.
- Geograficky redundantní záloha by měla být povolena pro Azure Database for MySQL
- Geograficky redundantní záloha by měla být povolena pro Azure Database for PostgreSQL
- Geograficky redundantní záloha by měla být povolena pro Azure Database pro MariaDB
- Azure Backup by měla být povolená pro virtuální počítače

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4 Zajištění ochrany záloh a klíčů spravovaných zákazníkem

- Objekty trezoru klíčů by měly být obnovitelné

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2 Vytvoření postupu hodnocení incidentů a stanovení priorit

- Měla by být vybrána standardní cenová úroveň Centra zabezpečení.

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4 Poskytnutí kontaktních údajů k bezpečnostním incidentům a konfigurace upozornění na bezpečnostní incidenty

- Pro vaše předplatné by měla být poskytnuta e-mailová adresa bezpečnostního kontaktu.
- Pro vaše předplatné by mělo být k dispozici telefonní číslo bezpečnostního kontaktu.
- Rozšířené nastavení zabezpečení dat pro SQL server by měl obsahovat e-mailovou adresu pro příjem výstrah zabezpečení
- Pokročilá nastavení zabezpečení dat pro spravovanou instanci SQL by měla obsahovat e-mailovou adresu pro příjem výstrah zabezpečení
- E-mailová oznámení správcům a vlastníkům předplatného by měla být povolena v rozšířeném nastavení zabezpečení dat serveru SQL Server.
- E-mailová oznámení správcům a vlastníkům předplatného by měla být povolena v rozšířeném nastavení zabezpečení dat spravované sql.

## <a name="next-steps"></a>Další kroky

Teď, když jste zkontrolovali mapování ovládacích prvku podrobného plánu Azure Security Benchmark, navštivte zásady Azure na webu Azure Portal a přiřaďte iniciativu:

> [!div class="nextstepaction"]
> [Azure Policy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions)

Další články věnované podrobným plánům a postupu jejich využití:

- Další informace o [životním cyklu podrobného plánu](../../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../../how-to/update-existing-assignments.md)