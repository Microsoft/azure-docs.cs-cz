---
title: Ukázkové ovládací prvky podrobného plánu pro Azure Security test
description: Mapování ovládacího prvku ukázka testu výkonnosti Azure Security test na Azure Policy.
ms.date: 04/16/2020
ms.topic: sample
ms.openlocfilehash: f80b72f06532adef28bf5e5afd1eb94c2e34ee2d
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691305"
---
# <a name="control-mapping-of-the-azure-security-benchmark-blueprint-sample"></a>Mapování ovládacího prvku pro ukázku Azure Security test details

Následující článek podrobně popisuje, jak se ukázka Azure modrotisky Azure Security test Details mapuje na ovládací prvky srovnávacích testů zabezpečení Azure. Další informace o ovládacích prvcích najdete v článku [Srovnávací test zabezpečení Azure](../../../../security/benchmarks/overview.md).

Následující mapování jsou k ovládacím prvkům **benchmark zabezpečení Azure** . Pomocí navigace na pravé straně můžete přejít přímo k určitému mapování ovládacího prvku. Namapované ovládací prvky jsou implementovány s [Azure Policy](../../../policy/overview.md) iniciativou. Chcete-li si projít kompletní iniciativu, otevřete **zásadu** v Azure Portal a vyberte stránku **definice** . Pak vyhledejte a vyberte ve ** \[verzi Preview\]: Auditovat doporučení Azure Security test a nasaďte specifická podpůrná iniciativa rozšíření virtuálních počítačů** .

> [!IMPORTANT]
> Každý ovládací prvek níže je přidružen k jedné nebo více definicím [Azure Policy](../../../policy/overview.md) . Tyto zásady vám pomůžou [zhodnotit dodržování předpisů](../../../policy/how-to/get-compliance-data.md) pomocí ovládacího prvku. často však není 1:1 nebo Úplná shoda mezi ovládacím prvkem a jednou nebo více zásadami. V takovém případě **vyhovuje** v Azure Policy pouze zásadám, které jsou samotné. Tím se nezajistí, že budete plně kompatibilní se všemi požadavky ovládacího prvku. Standard dodržování předpisů může navíc zahrnovat ovládací prvky, které nejsou v tuto chvíli řešeny žádnou definicí Azure Policy. Proto je dodržování předpisů v Azure Policy jenom částečný pohled na celkový stav dodržování předpisů. Přidružení mezi ovládacími prvky a definicemi Azure Policy pro tuto ukázku podrobného plánu dodržování předpisů se mohou v průběhu času měnit. Historii změn si můžete prohlédnout v [historii potvrzení GitHubu](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/azure-security-benchmark/control-mapping.md).

## <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1 Ochrana prostředků pomocí skupin zabezpečení sítě nebo Azure Firewall v Virtual Network

- Podsítě by měly být přidružené ke skupině zabezpečení sítě.
- Doporučení adaptivního posílení zabezpečení sítě by se měla použít na internetových virtuálních počítačích.
- Virtuální počítače by měly být připojené ke schválené virtuální síti
- Virtuální počítače s přístupem k Internetu by měly být chráněné pomocí skupin zabezpečení sítě
- Service Bus by měl používat koncový bod služby virtuální sítě
- App Service by měl používat koncový bod služby virtuální sítě
- SQL Server by měl používat koncový bod služby virtuální sítě
- Centrum událostí by mělo používat koncový bod služby virtuální sítě.
- Cosmos DB by měl používat koncový bod služby virtuální sítě
- Key Vault by měl používat koncový bod služby virtuální sítě
- Auditování neomezeného síťového přístupu k účtům úložiště
- Účty úložiště by měly používat koncový bod služby virtuální sítě.
- Container Registry by měl používat koncový bod služby virtuální sítě
- Virtuální sítě by měly používat zadanou bránu virtuální sítě
- Pro služby Kubernetes by měly být definované rozsahy povolených IP adres.
- \[Verze\]Preview: předávání IP na virtuálním počítači by mělo být zakázané.
- Virtuální počítače s přístupem k Internetu by měly být chráněné pomocí skupin zabezpečení sítě
- Na virtuálních počítačích by se mělo používat řízení přístupu k síti podle potřeby
- Porty pro správu by měly být uzavřeny na virtuálních počítačích

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2 monitorování a protokolování konfigurace a provozu virtuální sítě, podsítí a síťových karet

- Network Watcher by měl být povolený.

## <a name="13-protect-critical-web-applications"></a>1,3 chránit kritické webové aplikace

- Zajistěte, aby webová aplikace měla klientské certifikáty (příchozí klientské certifikáty) nastavené na zapnuto.
- CORS by neměl umožňovat každému prostředku přístup k vašim webovým aplikacím
- CORS by neměl umožňovat každému prostředku přístup k aplikacím funkcí
- CORS by neměl umožňovat každému prostředku přístup k vaší aplikaci API.
- Vzdálené ladění by mělo být pro webové aplikace vypnuté.
- Pro aplikace Function app by mělo být vypnuto vzdálené ladění.
- Vzdálené ladění by mělo být pro API Apps vypnuté.

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4 zamítnout komunikaci se známými škodlivými IP adresami

- Měla by být povolená DDoS Protection Standard.
- Na virtuálních počítačích by se mělo používat řízení přístupu k síti podle potřeby
- Doporučení adaptivního posílení zabezpečení sítě by se měla použít na internetových virtuálních počítačích.

## <a name="15-record-network-packets-and-flow-logs"></a>1,5 záznam síťových paketů a protokolů toků

- Network Watcher by měl být povolený.

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11 pomocí automatizovaných nástrojů můžete monitorovat konfigurace síťových prostředků a zjišťovat změny.

- Nasazení požadavků pro audit konfigurací virtuálních počítačů s Windows v možnosti zabezpečení-síťový přístup
- Zobrazit výsledky auditu z konfigurací virtuálních počítačů s Windows v možnosti zabezpečení – Klient sítě Microsoft
- Nasazení požadavků pro audit konfigurací virtuálních počítačů s Windows v možnosti zabezpečení – zabezpečení sítě
- Zobrazit výsledky auditu z konfigurací virtuálních počítačů s Windows v možnosti zabezpečení – zabezpečení sítě
- Nasazení požadavků pro audit konfigurací virtuálních počítačů s Windows v možnosti zabezpečení – síťový server Microsoftu
- Zobrazit výsledky auditu z konfigurací virtuálních počítačů s Windows v možnosti zabezpečení – síťový server Microsoftu
- Nasazení požadavků pro audit konfigurací virtuálních počítačů s Windows v síti Šablony pro správu-Network
- Zobrazit výsledky auditu z konfigurací virtuálních počítačů s Windows v síti Šablony pro správu-Network

## <a name="22-configure-central-security-log-management"></a>2,2 konfigurace centrální správy protokolů zabezpečení

- Agent Log Analytics by měl být nainstalovaný na virtuálních počítačích
- Agent Log Analytics by měl být nainstalovaný na Virtual Machine Scale Sets
- Nasaďte požadavky na auditování virtuálních počítačů s Windows, na kterých agent Log Analytics není připojený podle očekávání.
- Zobrazit výsledky auditu z virtuálních počítačů s Windows, na kterých je agent Log Analytics nepřipojený podle očekávání
- Profil protokolu Azure Monitor by měl shromažďovat protokoly pro kategorie Write, DELETE a Action.
- Azure Monitor by měly shromažďovat protokoly aktivit ze všech oblastí
- V předplatném by mělo být povolené Automatické zřizování agenta monitorování Log Analytics.

## <a name="23-enable-audit-logging-for-azure-resources"></a>2,3 povolení protokolování auditu pro prostředky Azure

- Měly by být povolené diagnostické protokoly v Azure Data Lake Store.
- Měly by být povolené diagnostické protokoly v Logic Apps.
- Měly by být povolené diagnostické protokoly v IoT Hub.
- V účtech Batch by měly být povolené diagnostické protokoly.
- Měly by být povolené diagnostické protokoly v Virtual Machine Scale Sets.
- Měly by být povolené diagnostické protokoly v centru událostí.
- Měly by být povolené diagnostické protokoly ve vyhledávacích službách.
- Měly by být povolené diagnostické protokoly v App Services.
- Měly by být povolené diagnostické protokoly v Data Lake Analytics.
- Měly by být povolené diagnostické protokoly v Key Vault.
- Měly by být povolené diagnostické protokoly v Service Bus.
- Měly by být povolené diagnostické protokoly v Azure Stream Analytics.
- Auditování na SQL serveru by mělo být povolené.
- Auditování nastavení diagnostiky

## <a name="24-collect-security-logs-from-operating-systems"></a>2,4 shromažďování protokolů zabezpečení z operačních systémů

- V předplatném by mělo být povolené Automatické zřizování agenta monitorování Log Analytics.
- Agent Log Analytics by měl být nainstalovaný na virtuálních počítačích
- Agent Log Analytics by měl být nainstalovaný na Virtual Machine Scale Sets
- Nasaďte požadavky na auditování virtuálních počítačů s Windows, na kterých agent Log Analytics není připojený podle očekávání.
- Zobrazit výsledky auditu z virtuálních počítačů s Windows, na kterých je agent Log Analytics nepřipojený podle očekávání

## <a name="27-enable-alerts-for-anomalous-activity"></a>2,7 povolit výstrahy pro aktivitu neobvyklé

- Měla by se vybrat cenová úroveň Security Center Standard.
- Na vašich serverech SQL by mělo být povolené rozšířené zabezpečení dat
- Pro spravované instance SQL by mělo být povolené rozšířené zabezpečení dat
- V rozšířených nastaveních zabezpečení dat SQL serveru by se měly nastavit rozšířené typy ochrany před internetovými útoky na ALL.
- V rozšířených nastaveních zabezpečení dat spravované instance SQL by se měly nastavit rozšířené typy ochrany před internetovými útoky na ALL.

## <a name="28-centralize-anti-malware-logging"></a>2,8. centralizované protokolování proti malwaru

- Microsoft Antimalware pro Azure by měl být nakonfigurovaný tak, aby automaticky aktualizoval podpisy ochrany.
- Monitorovat chybějící Endpoint Protection v Azure Security Center
- Řešení ochrany koncových bodů by se mělo nainstalovat na Virtual Machine Scale Sets.

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1 udržování inventáře účtů pro správu

- Pro vaše předplatné by se mělo určit maximálně 3 vlastníci.
- K vašemu předplatnému by měl být přiřazený víc než jeden vlastník.
- Z vašeho předplatného byste měli odebrat externí účty s oprávněním vlastníka.
- Zastaralé účty s oprávněním vlastníka by se měly odebrat z vašeho předplatného.

## <a name="33-use-dedicated-administrative-accounts"></a>3,3 použít vyhrazené účty pro správu

- Nasaďte požadavky na auditování virtuálních počítačů s Windows, ve kterých skupina Administrators neobsahuje jenom zadané členy.
- Zobrazí výsledky auditu z virtuálních počítačů s Windows, ve kterých skupina Administrators neobsahuje jenom určité členy.
- Nasaďte požadavky na auditování virtuálních počítačů s Windows, ve kterých skupina Administrators neobsahuje všechny zadané členy.
- Zobrazit výsledky auditu z virtuálních počítačů s Windows, ve kterých skupina Administrators neobsahuje všechny zadané členy
- Nasaďte požadavky na auditování virtuálních počítačů s Windows, ve kterých skupina Administrators obsahuje některé ze zadaných členů.
- Zobrazit výsledky auditu z virtuálních počítačů s Windows, ve kterých skupina Administrators obsahuje některé ze zadaných členů
- Pro vaše předplatné by se mělo určit maximálně 3 vlastníci.
- K vašemu předplatnému by měl být přiřazený víc než jeden vlastník.

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5 použití služby Multi-Factor Authentication pro veškerý přístup založený na Azure Active Directory

- Pro účty s oprávněním vlastníka pro vaše předplatné by se měla povolit vícefaktorové ověřování.
- V rámci vašeho předplatného by měly být povolené účty s oprávněním k zápisu.
- Pro účty s oprávněním ke čtení vašeho předplatného by se měla povolit vícefaktorové ověřování.

## <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7 protokolování a upozornění na podezřelou aktivitu z účtů pro správu

- Měla by se vybrat cenová úroveň Security Center Standard.

## <a name="39-use-azure-active-directory"></a>3,9 Azure Active Directory použít

- Pro SQL servery by se měl zřídit správce Azure Active Directory.
- Clustery Service Fabric by se měly používat jenom Azure Active Directory pro ověřování klientů.
- Ujistěte se, že v aplikaci API je povolená možnost registrovat v Azure Active Directory.
- Zajistěte, aby byl ve webové aplikaci povolený registr s Azure Active Directory.
- Ujistěte se, že je v Function App povolená možnost registrovat v Azure Active Directory.

## <a name="310-regularly-review-and-reconcile-user-access"></a>3,10 pravidelně kontrolovat a sjednotit přístup uživatelů

- Zastaralé účty by se měly odebírat z předplatného.
- Zastaralé účty s oprávněním vlastníka by se měly odebrat z vašeho předplatného.
- Z vašeho předplatného by se měly odebrat externí účty s oprávněním ke čtení.
- Z vašeho předplatného byste měli odebrat externí účty s oprávněním k zápisu.
- Z vašeho předplatného byste měli odebrat externí účty s oprávněním vlastníka.

## <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1 udržování inventáře citlivých informací

- Citlivá data v databázích SQL by měla být klasifikována

## <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4 šifrování všech citlivých informací během přenosu

- Měl by se povolit zabezpečený přenos do účtů úložiště
- V aplikaci API by se měla použít nejnovější verze TLS.
- Ve vaší webové aplikaci by se měla použít nejnovější verze TLS.
- V Function App by se měla použít nejnovější verze TLS.
- Function App by měl být přístupný jenom přes HTTPS
- Webová aplikace by měla být přístupná jen přes protokol HTTPS
- Aplikace API by měla být přístupná jen přes protokol HTTPS
- Pro databázové servery MySQL by mělo být povoleno připojení SSL vynutilo.
- Pro databázové servery PostgreSQL by mělo být povoleno připojení SSL vynutilo.
- Měla by být povolená jenom zabezpečená připojení k vašemu Redis Cache.

## <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5 k identifikaci citlivých dat použít aktivní nástroj zjišťování

- Citlivá data v databázích SQL by měla být klasifikována
- Na vašich serverech SQL by mělo být povolené rozšířené zabezpečení dat
- Pro spravované instance SQL by mělo být povolené rozšířené zabezpečení dat

## <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6 použití služby Azure RBAC k řízení přístupu k prostředkům

- Pro služby Kubernetes by se měla použít Access Control na základě rolí (RBAC).
- Auditovat využití vlastních pravidel RBAC

## <a name="48-encrypt-sensitive-information-at-rest"></a>4,8 šifrování citlivých informací v klidovém umístění

- Je třeba povolit transparentní šifrování dat databází SQL.
- Na virtuálních počítačích by se mělo použít šifrování disku
- Nepřipojené disky by měly být zašifrované
- Ochrana TDE systému SQL Server by měla být zašifrovaná pomocí vlastního klíče
- Ochrana TDE spravované instance SQL by se měla šifrovat pomocí vlastního klíče.
- Proměnné účtu Automation by se měly šifrovat.
- Clustery Service Fabric musí mít vlastnost ClusterProtectionLevel nastavenou na EncryptAndSign.

## <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>Protokol 4,9 a upozornění na změny důležitých prostředků Azure

- Azure Monitor by měly shromažďovat protokoly aktivit ze všech oblastí

## <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1 spuštění automatizovaných nástrojů pro kontrolu ohrožení zabezpečení

- Na vašich serverech SQL by mělo být povolené posouzení ohrožení zabezpečení
- Na spravovaných instancích SQL by mělo být povolené posouzení ohrožení zabezpečení
- \[Posouzení\] ohrožení zabezpečení ve verzi Preview by mělo být povolené na Virtual Machines
- Nastavení posouzení ohrožení zabezpečení pro SQL Server by mělo obsahovat e-mailovou adresu pro příjem sestav kontroly.

## <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2 nasazení automatizovaného řešení pro správu oprav operačního systému

- Na počítače by se měly nainstalovat aktualizace systému
- Musí být nainstalované aktualizace systému ve virtuálních počítačích Virtual Machine Scale Sets.
- Zajistěte, aby byla verze .NET Framework nejnovější, pokud se používá jako součást Function App
- Zajistěte, aby byla verze .NET Framework nejnovější, pokud se používá jako součást webové aplikace.
- Zajistěte, aby byla verze .NET Framework nejnovější, pokud se používá jako součást aplikace API.

## <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3 nasazení automatizovaného řešení pro správu oprav softwaru třetí strany

- Zajistěte, aby byla verze PHP nejnovější, pokud se používá jako součást aplikace API.
- Zajistěte, aby byla verze PHP nejnovější, pokud se používá jako součást webové aplikace.
- Zajistěte, aby byla verze PHP nejnovější, pokud se používá jako součást aplikace Function App.
- Zajistěte, aby byl jazyk Java verze nejnovější, pokud se používá jako součást webové aplikace.
- Zajistěte, aby byl jazyk Java verze nejnovější, pokud se používá jako součást aplikace Function App.
- Zajistěte, aby byl jazyk Java verze nejnovější, pokud se používá jako součást aplikace API.
- Zajistěte, aby byla nejnovější verze Pythonu, pokud se používá jako součást webové aplikace.
- Zajistěte, aby byla nejnovější verze Pythonu, pokud se používá jako součást aplikace Function App.
- Zajistěte, aby byla nejnovější verze Pythonu, pokud se používá jako součást aplikace API.
- Služby Kubernetes by se měly upgradovat na nezranitelnou Kubernetes verzi.

## <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5 pomocí procesu hodnocení rizik můžete určit prioritu nápravy zjištěných chyb zabezpečení.

- Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení.
- Ohrožení zabezpečení v konfiguraci zabezpečení na vašich počítačích by mělo být opraveno
- V konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů by se měly napravit ohrožení zabezpečení.
- Ohrožení zabezpečení v konfiguraci zabezpečení kontejneru by mělo být opraveno
- Ohrožení zabezpečení vašich databází SQL by mělo být opraveno

## <a name="68-use-only-approved-applications"></a>6,8 použít pouze schválené aplikace

- Měla by se vybrat cenová úroveň Security Center Standard.
- Na virtuálních počítačích by měly být povolené Adaptivní řízení aplikací.

## <a name="69-use-only-approved-azure-services"></a>6,9 použít jenom schválené služby Azure

- Virtuální počítače by se měly migrovat na nové prostředky Azure Resource Manager.
- Účty úložiště by se měly migrovat na nové prostředky Azure Resource Manager.

## <a name="610-implement-approved-application-list"></a>6,10 implementace seznamu schválených aplikací

- Měla by se vybrat cenová úroveň Security Center Standard.
- Na virtuálních počítačích by měly být povolené Adaptivní řízení aplikací.

## <a name="73-maintain-secure-azure-resource-configurations"></a>7,3 udržování zabezpečených konfigurací prostředků Azure

- \[Preview\]: v Kubernetes službách by se měly definovat zásady zabezpečení pod.

## <a name="74-maintain-secure-operating-system-configurations"></a>7,4 udržování zabezpečených konfigurací operačního systému

- Ohrožení zabezpečení v konfiguraci zabezpečení na vašich počítačích by mělo být opraveno
- Ohrožení zabezpečení v konfiguraci zabezpečení kontejneru by mělo být opraveno
- V konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů by se měly napravit ohrožení zabezpečení.

## <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9 implementace automatizovaného monitorování konfigurace pro služby Azure

- \[Preview\]: v Kubernetes službách by se měly definovat zásady zabezpečení pod.

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10 implementace automatizovaného monitorování konfigurace pro operační systémy

- Ohrožení zabezpečení v konfiguraci zabezpečení na vašich počítačích by mělo být opraveno
- Ohrožení zabezpečení v konfiguraci zabezpečení kontejneru by mělo být opraveno
- V konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů by se měly napravit ohrožení zabezpečení.

## <a name="711-manage-azure-secrets-securely"></a>7,11 spravujte zabezpečená tajná klíčová úložiště

- Key Vault objekty by měly být obnovitelné

## <a name="712-manage-identities-securely-and-automatically"></a>7,12 Správa identit zabezpečeně a automaticky

- Ve vašem Function App by se měla používat spravovaná identita.
- Ve vaší webové aplikaci by se měla použít spravovaná identita.
- Ve vaší aplikaci API by se měla používat spravovaná identita.

## <a name="81-use-centrally-managed-anti-malware-software"></a>8,1 použití centrálně spravovaného malwarového softwaru

- Monitorovat chybějící Endpoint Protection v Azure Security Center
- Řešení ochrany koncových bodů by se mělo nainstalovat na Virtual Machine Scale Sets.

## <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2 předem kontrolovat soubory, které se mají nahrát do prostředků Azure, které nejsou COMPUTE

- Měla by se vybrat cenová úroveň Security Center Standard.

## <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3 zajistěte, aby byl antimalwarový software a signatury aktualizované

- Microsoft Antimalware pro Azure by měl být nakonfigurovaný tak, aby automaticky aktualizoval podpisy ochrany.

## <a name="91-ensure-regular-automated-back-ups"></a>9,1 zajištění normálního automatického zálohování

- Pro databáze SQL Azure by mělo být povolené dlouhodobé geograficky redundantní zálohování.
- Pro Azure Database for MySQL by měla být povolená geograficky redundantní záloha.
- Pro Azure Database for PostgreSQL by měla být povolená geograficky redundantní záloha.
- Pro Azure Database for MariaDB by měla být povolená geograficky redundantní záloha.
- Azure Backup by měla být povolená Virtual Machines

## <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2 proveďte kompletní systémové zálohy a zálohujte všechny spravované klíče zákazníka.

- Pro databáze SQL Azure by mělo být povolené dlouhodobé geograficky redundantní zálohování.
- Pro Azure Database for MySQL by měla být povolená geograficky redundantní záloha.
- Pro Azure Database for PostgreSQL by měla být povolená geograficky redundantní záloha.
- Pro Azure Database for MariaDB by měla být povolená geograficky redundantní záloha.
- Azure Backup by měla být povolená Virtual Machines

## <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4 Zajistěte ochranu záloh a spravovaných klíčů zákazníků.

- Key Vault objekty by měly být obnovitelné

## <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2 Vytvoření bodování incidentu a postupu stanovení priorit

- Měla by se vybrat cenová úroveň Security Center Standard.

## <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4 Zadejte podrobnosti o incidentu zabezpečení a nakonfigurujte oznámení o výstrahách pro incidenty zabezpečení.

- Pro vaše předplatné by se měla zadat e-mailová adresa kontaktu pro zabezpečení.
- Pro vaše předplatné by se mělo zadat telefonní číslo kontaktu zabezpečení.
- Pokročilá nastavení zabezpečení dat pro SQL Server by měla obsahovat e-mailovou adresu pro příjem výstrah zabezpečení.
- Pokročilá nastavení zabezpečení dat pro spravovanou instanci SQL by měla obsahovat e-mailovou adresu pro příjem výstrah zabezpečení.
- E-mailová oznámení správcům a vlastníkům předplatného by měla být povolená v nastavení SQL Server Advanced Data Security
- E-mailová oznámení správcům a vlastníkům předplatného by se měla povolit v rozšířených nastaveních zabezpečení dat spravované instance SQL

## <a name="next-steps"></a>Další kroky

Teď, když jste zkontrolovali mapování ovládacího prvku Azure Security test test, přejděte k iniciativě v Azure Portal Azure Policy.

> [!div class="nextstepaction"]
> [Azure Policy](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions)

Další články věnované podrobným plánům a postupu jejich využití:

- Další informace o [životním cyklu podrobného plánu](../../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../../how-to/update-existing-assignments.md)