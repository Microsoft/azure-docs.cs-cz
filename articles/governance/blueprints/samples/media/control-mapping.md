---
title: Ovládací prvky ukázkového plánu médií
description: Mapování ovládacího prvku ukázky podrobného plánu média. Každý ovládací prvek je mapován na jednu nebo více zásad Azure, které pomáhají s hodnocením.
ms.date: 02/24/2020
ms.topic: sample
ms.openlocfilehash: bdc4ef65c6d61a9d4217d7873bb29bd787842e71
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78201903"
---
# <a name="control-mapping-of-the-media-blueprint-sample"></a>Mapování ovládacího prvku ukázky podrobného plánu média

V následujícím článku je podrobně popisuje, jak se ukázka podrobného plánu Azure Blueprint mapuje na ovládací prvky Media. Další informace o ovládacích prvcích naleznete v tématu [Media](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

Následující mapování jsou na ovládací prvky **Media.** Pomocí navigace vpravo přejděte přímo na konkrétní mapování ovládacího prvku. Mnoho mapovaných ovládacích prvků se implementuje pomocí iniciativy [Zásad azure.](../../../policy/overview.md) Pokud chcete zkontrolovat celou iniciativu, otevřete **zásady** na webu Azure portal a vyberte stránku **Definice.** Potom vyhledejte a vyberte ** \[náhled\]: Auditovat média řídí** integrovanou iniciativu zásad.

> [!IMPORTANT]
> Každý ovládací prvek níže je přidružen k jedné nebo více definic [zásad Azure.](../../../policy/overview.md) Tyto zásady vám mohou pomoci [posoudit dodržování](../../../policy/how-to/get-compliance-data.md) ovládacího prvku; však často není 1:1 nebo úplnou shodu mezi ovládacím prvkem a jednu nebo více zásad. Jako **takový, kompatibilní** v zásadách Azure odkazuje pouze na zásady samotné; Tím nezajistíte, že jste plně kompatibilní se všemi požadavky ovládacího prvku. Kromě toho standard dodržování předpisů zahrnuje ovládací prvky, které nejsou v tuto chvíli adresovány žádnými definicemi zásad Azure. Dodržování předpisů v zásadách Azure je tedy pouze částečným zobrazením celkového stavu dodržování předpisů. Přidružení mezi ovládacími prvky a definicemi zásad Azure pro tento ukázkový plán dodržování předpisů se může v průběhu času měnit. Chcete-li zobrazit historii změn, podívejte se na [historii potvrzení GitHubu](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/media/control-mapping.md).

## <a name="access-control"></a>Řízení přístupu

### <a name="ac-11--ensure-no-root-access-key-exists"></a>AC-1.1- Ujistěte se, že neexistuje žádný klíč pro přístup ke kořenu

- \[Náhled\]: Nasazení požadavků pro auditování virtuálních aplikací Windows, které neobsahují zadané certifikáty v důvěryhodném kořenovém adresáři

### <a name="ac-12---passwords-pins-and-tokens-must-be-protected"></a>AC-1.2 – Hesla, PIN kódy a tokeny musí být chráněny.

- \[Náhled\]: Nasazení předpokladů pro auditování virtuálních aplikací windows, které neomezují minimální délku hesla na 14 znaků

### <a name="ac-18---shared-account-access-is-prohibited"></a>AC-1.8 - Přístup ke sdílenému účtu je zakázán.

- Všechna autorizační pravidla s výjimkou rootmanagesharedaccesskey by měla být odebrána z oboru názvů Service Bus

### <a name="ac-19--system-must-restrict-access-to-authorized-users"></a>AC-1.9 -Systém musí omezit přístup oprávněným uživatelům.

- Auditovat neomezený přístup k účtům úložiště v síti

### <a name="ac--114--system-must-enforce-access-rights"></a>AC- 1.14 -Systém musí vynutit přístupová práva.

- \[Náhled\]: Nasazení předpokladů pro audit konfigurace virtuálních počítačů se systémem Windows v části Přiřazení uživatelských práv

### <a name="ac--115--prevent-unauthorized-access-to-security-relevant-information-or-functions"></a>AC- 1.15 -Zabránit neoprávněnému přístupu k bezpečnostním informacím nebo funkcím.

- \[Náhled\]: Zobrazení výsledků auditu z konfigurací virtuálních počítačů se systémem Windows v části Možnosti zabezpečení – nastavení systému

### <a name="ac-1-21---separation-of-duties-must-be-enforced-through-appropriate-assignment-of-role"></a>AC-1-21 - Oddělení povinností musí být vymáháno vhodným postoupením role.

- [Preview\]: Řízení přístupu na základě rolí (RBAC) by mělo být použito ve službách Kubernetes

### <a name="ac-140--ensure-that-systems-are-not-connecting-trusted-network-and-untrusted-networks-at-the-same-time"></a>AC-1.40 - Ujistěte se, že systémy nejsou připojení důvěryhodné sítě a nedůvěryhodné sítě ve stejnou dobu.

- \[Náhled\]: Nasazení předpokladů pro audit konfigurace virtuálních počítačů se systémem Windows v části Možnosti zabezpečení – přístup k síti

### <a name="ac-142--ac--143---remote-access-for-non-employees-must-be-restricted-to-allow-access-only-to-specifically-approved-information-systems"></a>AC-1.42 & AC-1.43 - Vzdálený přístup pro nezaměstnance musí být omezen tak, aby umožňoval přístup pouze k speciálně schváleným informačním systémům

- \[Náhled\]: Zobrazení výsledků auditu z virtuálních počítačů s Linuxem, které umožňují vzdálená připojení z účtů bez hesel

### <a name="ac-150--log-security-related-events-for-all-information-system-components"></a>AC-1.50- Protokolovat události související se zabezpečením pro všechny součásti informačního systému.

- Diagnostické protokoly v logic apps by měly být povoleny

### <a name="ac-154--ensure-multi-factor-authentication-mfa-is-enabled-for-all-cloud-console-users"></a>AC-1.54 - Ujistěte se, že vícefaktorové ověřování (MFA) je povoleno pro všechny uživatele cloudové konzoly.

- MFA by měly mít povolené účty s oprávněními k zápisu ve vašem předplatném.
- Vícefaktorové ověřování (MFA) by měla být povolena pro všechny účty předplatného s oprávněními k zápisu, aby se zabránilo porušení účtů nebo prostředků.

## <a name="auditing--logging"></a>Auditování protokolování &

### <a name="al-21--successful-and-unsuccessful-events-must-be-logged"></a>AL-2.1- Úspěšné a neúspěšné události musí být zaznamenány.

- Diagnostické protokoly ve vyhledávacích službách by měly být povoleny.

### <a name="al--216---network-devicesinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-network-deviceinstance-elbs-web-application-firewalls-etc"></a>AL -2.16 - Síťová zařízení/instance musí zaznamenávat každou událost klasifikovanou jako kritická událost zabezpečení tímto síťovým zařízením/instancí (ELB, brány firewall webových aplikací atd.)

- \[Náhled\]: Zobrazení výsledků auditu z konfigurací virtuálních počítačů se systémem Windows v části Možnosti zabezpečení – účty

### <a name="al-217--serversinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-serverinstance"></a>AL-2.17- Servery/instance musí protokolovat každou událost klasifikovanou jako kritická událost zabezpečení tímto serverem/instancí

- \[Náhled\]: Zobrazení výsledků auditu z konfigurací virtuálních počítačů se systémem Windows v části Možnosti zabezpečení – účty

### <a name="al-219---domain-events-must-log-any-event-classified-as-a-critical-or-high-security-event-by-the-domain-management-software"></a>AL-2.19 - Události domény musí zaznamenávat všechny události klasifikované jako kritická nebo vysoká událost zabezpečení softwarem pro správu domény

- \[Náhled\]: Zobrazení výsledků auditu z konfigurací virtuálních počítačů se systémem Windows v části Možnosti zabezpečení – účty
- \[Náhled\]: Nasazení předpokladů pro audit konfigurace virtuálních počítačů se systémem Windows v části Možnosti zabezpečení – síťový klient společnosti Microsoft

### <a name="al-220--domain-events-must-log-any-event-classified-as-a-critical-security-event-by-domain-security-controls"></a>AL-2.20 - Události domény musí zaznamenávat všechny události klasifikované jako kritická událost zabezpečení pomocí ovládacích prvků zabezpečení domény

- \[Náhled\]: Zobrazení výsledků auditu z konfigurací virtuálních počítačů se systémem Windows v části Možnosti zabezpečení – účty

### <a name="al-221--domain-events-must-log-any-access-or-changes-to-the-domain-log"></a>AL-2.21- Události domény musí zaznamenávat jakýkoli přístup nebo změny protokolu domény

- \[Náhled\]: Zobrazení výsledků auditu z konfigurací virtuálních počítačů se systémem Windows v části Možnosti zabezpečení – konzola pro zotavení

## <a name="cryptographic-controls"></a>Kryptografické ovládací prvky

### <a name="cc-42--applications-and-systems-must-use-current-cryptographic-solutions-for-protecting-data"></a>CC-4.2 - Aplikace a systémy musí používat aktuální kryptografická řešení pro ochranu dat.

- Transparentní šifrování dat v databázích SQL by mělo být povoleno
- Transparentní šifrování dat by mělo být povoleno pro ochranu dat v klidovém stavu a splnění požadavků na dodržování předpisů

### <a name="cc-45--digital-certificates-must-be-signed-by-an-approved-certificate-authority"></a>CC-4.5- Digitální certifikáty musí být podepsány schválenou certifikační autoritou.

- \[Náhled\]: Zobrazit výsledky auditu z virtuálních aplikací windows, které obsahují certifikáty končící v rámci zadaného počtu dní

### <a name="cc-46--digital-certificates-must-be-uniquely-assigned-to-a-user-or-device"></a>CC-4.6- Digitální certifikáty musí být jednoznačně přiřazeny uživateli nebo zařízení.

- \[Náhled\]: Nasazení požadavků pro auditování virtuálních aplikací windows, které obsahují certifikáty, jejichž platnost vyprší v rámci zadaného počtu dní

### <a name="cc-47--cryptographic-material-must-be-stored-to-enable-decryption-of-the-records-for-the-length-of-time-the-records-are-retained"></a>CC-4.7- Kryptografický materiál musí být uložen, aby bylo možné dešifrovat záznamy po dobu, po kterou jsou záznamy uchovávány.

- Šifrování disku by mělo být použito na virtuálních počítačích.
- Virtuální počítače bez povoleného šifrování disku bude monitorovat Azure Security Center jako doporučení

### <a name="cc-48--secret-and-private-keys-must-be-stored-securely"></a>CC-4.8 - Tajné a soukromé klíče musí být bezpečně uloženy.

- Transparentní šifrování dat v databázích SQL by mělo být povoleno
- Transparentní šifrování dat by mělo být povoleno pro ochranu dat v klidovém stavu a splnění požadavků na dodržování předpisů

## <a name="change--config-management"></a>Změnit správu konfigurace &

### <a name="cm-52--only-authorized-users-may-implement-approved-changes-on-the-system"></a>CM-5.2- Schválené změny v systému mohou provádět pouze oprávnění uživatelé.

- Na počítače by se měly nainstalovat aktualizace systému
- Chybějící aktualizace systému zabezpečení na vašich serverech budou monitorovány službou Azure Security Center podle doporučení.

### <a name="cm-512--maintain-an-up-to-date-complete-accurate-and-readily-available-baseline-configuration-of-the-information-system"></a>CM-5.12 - Udržovat aktuální, kompletní, přesné a snadno dostupné základní konfigurace informačního systému.

- Na počítače by se měly nainstalovat aktualizace systému
- Chybějící aktualizace systému zabezpečení na vašich serverech budou monitorovány službou Azure Security Center podle doporučení.

### <a name="cm-513--employ-automated-tools-to-maintain-a-baseline-configuration-of-the-information-system"></a>CM-5.13 - Používejte automatizované nástroje pro udržení základní konfigurace informačního systému.

- Na počítače by se měly nainstalovat aktualizace systému
- Chybějící aktualizace systému zabezpečení na vašich serverech budou monitorovány službou Azure Security Center podle doporučení.

### <a name="cm-514--identify-and-disable-unnecessary-andor-non-secure-functions-ports-protocols-and-services"></a>CM-5.14 - Identifikujte a zakažte nepotřebné a/nebo nezabezpečené funkce, porty, protokoly a služby.

- Síťová rozhraní by měla zakázat předávání IP adres
- \[Náhled\]: Předávání IP adres na vašem virtuálním počítači by mělo být zakázáno

### <a name="cm-519--monitor-changes-to-the-security-configuration-settings"></a>CM-5.19 - Sledování změn nastavení konfigurace zabezpečení.

- Nasazení nastavení diagnostiky pro skupiny zabezpečení sítě

### <a name="cm-522--ensure-that-only-authorized-software-and-updates-are-installed-on-company-systems"></a>CM-5.22 - Ujistěte se, že v systémech společnosti je nainstalován pouze autorizovaný software a aktualizace.

- Na počítače by se měly nainstalovat aktualizace systému
- Chybějící aktualizace systému zabezpečení na vašich serverech budou monitorovány službou Azure Security Center podle doporučení.

## <a name="identity--authentication"></a>Ověřování identity &

### <a name="ia-71--user-accounts-must-be-uniquely-assigned-to-individuals-for-access-to-information-that-is-not-classified-as-public-account-ids-must-be-constructed-using-a-standardized-logical-format"></a>IA-7.1 - Uživatelské účty musí být jednoznačně přiřazeny jednotlivcům pro přístup k informacím, které nejsou klasifikovány jako veřejné. ID účtu musí být vytvořena pomocí standardizovaného logického formátu.

- Z předplatného by měly být odebrány externí účty s oprávněními vlastníka.
- Externí účty s oprávněními vlastníka by měly být odebrány z vašeho předplatného, aby se zabránilo nemonitorovaný přístup.

## <a name="network-security"></a>Zabezpečení sítě

### <a name="ns-92--access-to-network-device-management-functionality-is-restricted-to-authorized-users"></a>NS-9.2 - Přístup k funkcím správy síťových zařízení je omezen na oprávněné uživatele.

- \[Náhled\]: Nasazení předpokladů pro audit konfigurace virtuálních počítačů se systémem Windows v části Možnosti zabezpečení – přístup k síti

### <a name="ns-93--all-network-devices-must-be-configured-using-their-most-secure-configurations"></a>NS-9.3 - Všechna síťová zařízení musí být konfigurována pomocí jejich nejbezpečnějšíkonfigurace.

- \[Náhled\]: Nasazení předpokladů pro audit konfigurace virtuálních počítačů se systémem Windows v části Možnosti zabezpečení – přístup k síti

### <a name="ns-95--all-network-connections-to-a-system-through-a-firewall-must-be-approved-and-audited-on-a-regular-basis"></a>NS-9.5 - Všechna síťová připojení k systému přes bránu firewall musí být pravidelně schvalována a auditována.

- \[Náhled\]: Zobrazení výsledků auditu z konfigurací virtuálních počítačů se systémem Windows v části Vlastnosti brány Windows Firewall

### <a name="ns-97--appropriate-controls-must-be-present-at-any-boundary-between-a-trusted-network-and-any-untrusted-or-public-network"></a>NS-9.7- Příslušné ovládací prvky musí být přítomny na jakékoli hranici mezi důvěryhodnou sítí a jakoukoli nedůvěryhodnou nebo veřejnou sítí.

- \[Náhled\]: Nasazení předpokladů pro audit konfigurace virtuálních počítačů se systémem Windows v části Vlastnosti brány Windows Firewall

## <a name="security-planning"></a>Plánování zabezpečení

### <a name="sp-113--threats-must-be-identified-that-could-negatively-impact-the-confidentiality-integrity-or-availability-of-company-information-and-content-along-with-the-likelihood-of-their-occurrence"></a>SP-11.3 - Musí být identifikovány hrozby, které by mohly negativně ovlivnit důvěrnost, integritu nebo dostupnost informací a obsahu společnosti spolu s pravděpodobností jejich výskytu.

- Rozšířené typy ochrany před internetovými hrozbami by měly být nastaveny na hodnotu "Vše" v nastavení rozšířeného zabezpečení dat spravované ho SQL.

### <a name="security-continuity"></a>Kontinuita zabezpečení

## <a name="sc-125--data-in-long-term-storage-must-be-accessible-throughout-the-retention-period-and-protected-against-media-degradation-and-technology-changes"></a>SC-12.5 - Data v dlouhodobém úložišti musí být přístupná po celou dobu uchovávání a chráněna před degradací médií a technologickými změnami.

- Sql servery by měly být konfigurovány s auditování retenční dny větší než 90 dnů.
- Auditování serverů SQL nakonfigurovaných s dobou uchovávání auditování kratší než 90 dní.

## <a name="system-integrity"></a>Integrita systému

### <a name="si-143--only-authorized-personnel-may-monitor-network-and-user-activities"></a>SI-14.3 - Pouze autorizovaní pracovníci mohou sledovat síťové a uživatelské aktivity.

- Chyby zabezpečení v databázích SQL by měly být opraveny
- Monitorujte výsledky hodnocení ohrožení zabezpečení a doporučení, jak napravit chyby zabezpečení databáze.

### <a name="si-144--internet-facing-systems-must-have-intrusion-detection"></a>SI-14.4 - Systémy směřující k internetu musí být vystižení vniknutí.

- Nasazení zjišťování hrozeb na serverech SQL

### <a name="si-1413--standardized-centrally-managed-anti-malware-software-should-be-implemented-across-the-company"></a>SI-14.13 - Standardizovaný centrálně řízený antimalwarový software by měl být implementován v celé společnosti.

- Nasazení výchozího rozšíření Microsoft IaaSAntimalware pro Windows Server

### <a name="si-1414--anti-malware-software-must-scan-computers-and-media-weekly-at-a-minimum"></a>SI-14.14 - Anti-malware software musí skenovat počítače a média týdně minimálně.

- Nasazení výchozího rozšíření Microsoft IaaSAntimalware pro Windows Server

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

### <a name="vm-154--ensure-that-applications-are-scanned-for-vulnerabilities-on-a-monthly-basis"></a>VM-15.4 - Ujistěte se, že aplikace jsou kontrolovány na základě chyb zabezpečení na měsíční bázi.

- Chyby zabezpečení v konfiguraci zabezpečení ve škálovacích sadách virtuálních počítačů by měly být opraveny.
- Auditujte chyby zabezpečení operačního systému na škálovacích sadách virtuálních strojů, abyste je ochránili před útoky.

### <a name="vm-155--ensure-that-vulnerabilities-are-identified-paired-to-threats-and-evaluated-for-risk"></a>VM-15.5 - Ujistěte se, že chyby zabezpečení jsou identifikovány, spárovány s hrozbami a vyhodnoceny z hlediska rizika.

- Chyby zabezpečení v konfiguraci zabezpečení ve škálovacích sadách virtuálních počítačů by měly být opraveny.
- Auditujte chyby zabezpečení operačního systému na škálovacích sadách virtuálních strojů, abyste je ochránili před útoky.

### <a name="vm-156--ensure-that-identified-vulnerabilities-have-been-remediated-within-a-mutually-agreed-upon-timeline"></a>VM-15.6- Ujistěte se, že identifikované chyby zabezpečení byly opraveny v rámci vzájemně dohodnutéčasové lhůty.

- Chyby zabezpečení v konfiguraci zabezpečení ve škálovacích sadách virtuálních počítačů by měly být opraveny.
- Auditujte chyby zabezpečení operačního systému na škálovacích sadách virtuálních strojů, abyste je ochránili před útoky.

### <a name="vm-157--access-to-and-use-of-vulnerability-management-systems-must-be-restricted-to-authorized-personnel"></a>VM-15.7 - Přístup k systémům pro správu zranitelnosti a jejich používání musí být omezen na oprávněné pracovníky.

- Chyby zabezpečení v konfiguraci zabezpečení ve škálovacích sadách virtuálních počítačů by měly být opraveny.
- Auditujte chyby zabezpečení operačního systému na škálovacích sadách virtuálních strojů, abyste je ochránili před útoky.

> [!NOTE]
> Dostupnost konkrétních definic zásad Azure se může lišit v Azure Government a dalších národních cloudech. 

## <a name="next-steps"></a>Další kroky

Zkontrolovali jste mapování ovládacího prvku ukázky podrobného plánu média. Dále navštivte následující články, kde se dozvíte o přehledu a o tom, jak tuto ukázku nasadit:

> [!div class="next step action"]
> [Podrobný plán médií – podrobný plán pro přehled](./control-mapping.md)
> [médií – nasazení kroků](./deploy.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Přečtěte si o [životním cyklu podrobného plánu](../../concepts/lifecycle.md).
- Pochopit, jak používat [statické a dynamické parametry](../../concepts/parameters.md).
- Naučte se přizpůsobit [pořadí sekvencování podrobných plánů](../../concepts/sequencing-order.md).
- Zjistěte, jak využít [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md).
- Přečtěte si, jak [aktualizovat existující přiřazení](../../how-to/update-existing-assignments.md).
