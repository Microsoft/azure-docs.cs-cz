---
title: Ukázkové ovládací prvky pro Media details
description: Mapování ovládacího prvku ukázek pro Media details Každý ovládací prvek je namapován na jednu nebo více Azure Policy definic, které pomáhají s posouzením.
ms.date: 01/08/2021
ms.topic: sample
ms.openlocfilehash: e1356acfd7b6b9693322000077b221781b58681f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98035032"
---
# <a name="control-mapping-of-the-media-blueprint-sample"></a>Mapování ovládacího prvku ukázka pro Media details

Následující článek podrobně popisuje, jak se vzorová ukázka Azure modrotisky Media Details mapuje na ovládací prvky multimédií. Další informace o ovládacích prvcích naleznete v tématu [Media](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

Následující mapování jsou k ovládacím prvkům **média** . Pomocí navigace na pravé straně můžete přejít přímo k určitému mapování ovládacího prvku. Mnohé z mapovaných ovládacích prvků jsou implementovány s [Azure Policy](../../../policy/overview.md) iniciativou. Chcete-li si projít kompletní iniciativu, otevřete **zásadu** v Azure Portal a vyberte stránku **definice** . Pak vyhledejte a vyberte ve **\[ verzi Preview \] : audit Media Controls** (předdefinovaná iniciativa zásad).

> [!IMPORTANT]
> Každý ovládací prvek níže je přidružen k jedné nebo více definicím [Azure Policy](../../../policy/overview.md) . Tyto zásady vám pomůžou [zhodnotit dodržování předpisů](../../../policy/how-to/get-compliance-data.md) pomocí ovládacího prvku. Nicméně často není jedna nebo Úplná shoda mezi ovládacím prvkem a jednou nebo více zásadami. V takovém případě **vyhovuje** v Azure Policy pouze zásadám, které jsou samotné. Tím se nezajistí, že budete plně kompatibilní se všemi požadavky ovládacího prvku. Standard kompatibility zahrnuje i ovládací prvky, které nejsou v tuto chvíli řešeny žádnými definicemi Azure Policy. Proto je dodržování předpisů v Azure Policy jenom částečný pohled na celkový stav dodržování předpisů. Přidružení mezi ovládacími prvky a definicemi Azure Policy pro tuto ukázku podrobného plánu dodržování předpisů se mohou v průběhu času měnit. Historii změn si můžete prohlédnout v [historii potvrzení GitHubu](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/media/control-mapping.md).

## <a name="access-control"></a>Řízení přístupu

### <a name="ac-11--ensure-no-root-access-key-exists"></a>AC-1,1 – Zajistěte, aby neexistuje žádný kořenový přístupový klíč.

- \[Verze Preview \] : nasaďte požadavky na auditování virtuálních počítačů s Windows, které neobsahují zadané certifikáty v důvěryhodném kořenovém adresáři.

### <a name="ac-12---passwords-pins-and-tokens-must-be-protected"></a>AC-1,2 – hesla, PIN kódy a tokeny musí být chráněné.

- \[Verze Preview \] : nasaďte požadavky pro audit virtuálních počítačů s Windows, které neomezují minimální délku hesla na 14 znaků.

### <a name="ac-18---shared-account-access-is-prohibited"></a>AC-1,8 – přístup ke sdílenému účtu je zakázaný

- Všechna autorizační pravidla s výjimkou RootManageSharedAccessKey by měla být odebrána z oboru názvů Service Bus

### <a name="ac-19--system-must-restrict-access-to-authorized-users"></a>AC-1,9 – systém musí omezit přístup autorizovaných uživatelů.

- Auditování neomezeného síťového přístupu k účtům úložiště

### <a name="ac--114--system-must-enforce-access-rights"></a>AC-1,14 – systém musí vyhovět přístupovým právům.

- \[Verze Preview \] : nasazení požadavků pro audit konfigurací virtuálních počítačů s Windows v přiřazení uživatelských práv

### <a name="ac--115--prevent-unauthorized-access-to-security-relevant-information-or-functions"></a>AC-1,15 – zabrání neoprávněnému přístupu k příslušným informacím nebo funkcím zabezpečení.

- \[Preview \] : Zobrazit výsledky auditu z konfigurací virtuálních počítačů s Windows v možnosti zabezpečení – systémové nastavení

### <a name="ac-1-21---separation-of-duties-must-be-enforced-through-appropriate-assignment-of-role"></a>AC-1-21 – oddělení cel se musí vyhovět odpovídajícím přiřazením role.

- [Preview \] : Role-Based Access Control (RBAC) by se mělo používat pro služby Kubernetes Services.

### <a name="ac-140--ensure-that-systems-are-not-connecting-trusted-network-and-untrusted-networks-at-the-same-time"></a>AC-1,40 – zajistěte, aby systémy nepřipojovaly důvěryhodnou síť a nedůvěryhodné sítě ve stejnou dobu.

- \[Verze Preview \] : nasazení požadavků pro audit konfigurací virtuálních počítačů s Windows v možnosti zabezpečení-síťový přístup

### <a name="ac-142--ac--143---remote-access-for-non-employees-must-be-restricted-to-allow-access-only-to-specifically-approved-information-systems"></a>AC-1,42 & AC-1,43-vzdálený přístup pro zaměstnance, kteří nejsou zaměstnanci, se musí omezit tak, aby povoloval přístup jenom k výslovně schváleným informačním systémům.

- \[Preview \] : Zobrazit výsledky auditu z virtuálních počítačů se systémem Linux, které umožňují vzdálená připojení z účtů bez hesla

### <a name="ac-150--log-security-related-events-for-all-information-system-components"></a>AC-1,50 – protokoluje události související se zabezpečením pro všechny součásti informačního systému.

- Měly by být povolené diagnostické protokoly v Logic Apps.

### <a name="ac-154--ensure-multi-factor-authentication-mfa-is-enabled-for-all-cloud-console-users"></a>AC-1,54 – Ujistěte se, že je povoleno vícefaktorové ověřování (MFA) pro všechny uživatele konzoly cloudu.

- Pro účty s oprávněními k zápisu v předplatném by se mělo povolit MFA.
- Multi-Factor Authentication (MFA) by mělo být povolené pro všechny účty předplatného s oprávněním k zápisu, aby se zabránilo narušení účtů nebo prostředků.

## <a name="auditing--logging"></a>Auditování & protokolování

### <a name="al-21--successful-and-unsuccessful-events-must-be-logged"></a>AL-2,1 – úspěšné a neúspěšné události musí být protokolovány.

- Měly by být povolené diagnostické protokoly ve vyhledávacích službách.

### <a name="al--216---network-devicesinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-network-deviceinstance-elbs-web-application-firewalls-etc"></a>AL-2,16 – síťová zařízení/instance musí protokolovat jakoukoli událost klasifikovanou jako kritickou událost zabezpečení prostřednictvím tohoto síťového zařízení/instance (ELBs, Firewall webových aplikací atd.).

- \[Preview \] : Zobrazit výsledky auditu z konfigurací virtuálních počítačů s Windows v možnosti zabezpečení – účty

### <a name="al-217--serversinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-serverinstance"></a>AL-2,17 – servery/instance musí protokolovat všechny události klasifikované jako kritická událost zabezpečení daného serveru/instance.

- \[Preview \] : Zobrazit výsledky auditu z konfigurací virtuálních počítačů s Windows v možnosti zabezpečení – účty

### <a name="al-219---domain-events-must-log-any-event-classified-as-a-critical-or-high-security-event-by-the-domain-management-software"></a>AL-2,19 – události domény musí protokolovat jakoukoli událost klasifikovanou jako kritickou nebo vysokou událost zabezpečení, kterou používá software pro správu domény.

- \[Preview \] : Zobrazit výsledky auditu z konfigurací virtuálních počítačů s Windows v možnosti zabezpečení – účty
- \[Verze Preview \] : nasazení požadavků pro audit konfigurací virtuálních počítačů s Windows v možnosti zabezpečení – Klient sítě Microsoft

### <a name="al-220--domain-events-must-log-any-event-classified-as-a-critical-security-event-by-domain-security-controls"></a>AL-2,20 – události domény musí protokolovat jakoukoli událost klasifikovanou jako kritická událost zabezpečení pomocí ovládacích prvků zabezpečení domény.

- \[Preview \] : Zobrazit výsledky auditu z konfigurací virtuálních počítačů s Windows v možnosti zabezpečení – účty

### <a name="al-221--domain-events-must-log-any-access-or-changes-to-the-domain-log"></a>AL-2,21 – události domény musí protokolovat jakýkoli přístup nebo změny do protokolu domény.

- \[Preview \] : Zobrazit výsledky auditu z konfigurací virtuálních počítačů s Windows v možnosti zabezpečení – konzola pro zotavení

## <a name="cryptographic-controls"></a>Ovládací prvky kryptografie

### <a name="cc-42--applications-and-systems-must-use-current-cryptographic-solutions-for-protecting-data"></a>CC-4,2 – aplikace a systémy musí používat aktuální kryptografická řešení pro ochranu dat.

- Je třeba povolit transparentní šifrování dat databází SQL.
- Transparentní šifrování dat by mělo být povoleno pro ochranu neaktivních dat a splnění požadavků na dodržování předpisů.

### <a name="cc-45--digital-certificates-must-be-signed-by-an-approved-certificate-authority"></a>CC-4,5-digitální certifikáty musí být podepsány schválenou certifikační autoritou.

- \[Preview \] : Zobrazit výsledky auditu z virtuálních počítačů s Windows, které v zadaném počtu dní obsahují certifikáty, jejichž platnost vyprší

### <a name="cc-46--digital-certificates-must-be-uniquely-assigned-to-a-user-or-device"></a>CC-4,6-digitální certifikáty musí být jednoznačně přiřazeny uživateli nebo zařízení.

- \[Verze Preview \] : nasazení požadavků pro audit virtuálních počítačů s Windows, které obsahují certifikáty vyprší během zadaného počtu dnů.

### <a name="cc-47--cryptographic-material-must-be-stored-to-enable-decryption-of-the-records-for-the-length-of-time-the-records-are-retained"></a>CC-4,7 – šifrovací materiál musí být uložen, aby umožňoval dešifrování záznamů po dobu uchování záznamů.

- Na virtuálních počítačích by se mělo použít šifrování disku
- Virtuální počítače bez povoleného šifrování disku se budou monitorovat Azure Security Center jako doporučení.

### <a name="cc-48--secret-and-private-keys-must-be-stored-securely"></a>CC-4,8 – tajný klíč a privátní klíče musí být bezpečně uložené.

- Je třeba povolit transparentní šifrování dat databází SQL.
- Transparentní šifrování dat by mělo být povoleno pro ochranu neaktivních dat a splnění požadavků na dodržování předpisů.

## <a name="change--config-management"></a>Změna správy konfigurace &

### <a name="cm-52--only-authorized-users-may-implement-approved-changes-on-the-system"></a>CM-5,2 – schválené změny systému můžou implementovat jenom autorizovaní uživatelé.

- Na počítače by se měly nainstalovat aktualizace systému
- Chybějící aktualizace systému zabezpečení na vašich serverech se monitorují Azure Security Center jako doporučení.

### <a name="cm-512--maintain-an-up-to-date-complete-accurate-and-readily-available-baseline-configuration-of-the-information-system"></a>CM-5,12 – udržování aktuálnosti, kompletního, přesného a snadno dostupného základního nastavení informačního systému.

- Na počítače by se měly nainstalovat aktualizace systému
- Chybějící aktualizace systému zabezpečení na vašich serverech se monitorují Azure Security Center jako doporučení.

### <a name="cm-513--employ-automated-tools-to-maintain-a-baseline-configuration-of-the-information-system"></a>CM-5,13 – použití automatizovaných nástrojů pro udržování základní konfigurace informačního systému.

- Na počítače by se měly nainstalovat aktualizace systému
- Chybějící aktualizace systému zabezpečení na vašich serverech se monitorují Azure Security Center jako doporučení.

### <a name="cm-514--identify-and-disable-unnecessary-andor-non-secure-functions-ports-protocols-and-services"></a>CM-5,14 – identifikujte a zakažte nepotřebné nebo nezabezpečené funkce, porty, protokoly a služby.

- Síťová rozhraní by měla zakázat předávání IP
- \[Verze Preview \] : předávání IP na virtuálním počítači by mělo být zakázané.

### <a name="cm-519--monitor-changes-to-the-security-configuration-settings"></a>CM-5,19 – Sledujte změny nastavení konfigurace zabezpečení.

- Nasadit nastavení diagnostiky pro skupiny zabezpečení sítě

### <a name="cm-522--ensure-that-only-authorized-software-and-updates-are-installed-on-company-systems"></a>CM-5,22 – zajistěte, aby byly v systémech společnosti nainstalovány pouze autorizované software a aktualizace.

- Na počítače by se měly nainstalovat aktualizace systému
- Chybějící aktualizace systému zabezpečení na vašich serverech se monitorují Azure Security Center jako doporučení.

## <a name="identity--authentication"></a>Ověřování identity &

### <a name="ia-71--user-accounts-must-be-uniquely-assigned-to-individuals-for-access-to-information-that-is-not-classified-as-public-account-ids-must-be-constructed-using-a-standardized-logical-format"></a>IA-7,1 – uživatelské účty musí být jedinečné přiřazení jednotlivcům pro přístup k informacím, které nejsou klasifikovány jako veřejné. ID účtu musí být vytvořené pomocí standardizovaného logického formátu.

- Z vašeho předplatného byste měli odebrat externí účty s oprávněním vlastníka.
- Aby nedocházelo k nemonitorovanému přístupu, měly by se z předplatného odebrat externí účty s oprávněním vlastníka.

## <a name="network-security"></a>Zabezpečení sítě

### <a name="ns-92--access-to-network-device-management-functionality-is-restricted-to-authorized-users"></a>NS-9,2 – přístup k funkcím správy síťových zařízení je omezený na autorizované uživatele.

- \[Verze Preview \] : nasazení požadavků pro audit konfigurací virtuálních počítačů s Windows v možnosti zabezpečení-síťový přístup

### <a name="ns-93--all-network-devices-must-be-configured-using-their-most-secure-configurations"></a>NS-9,3 – všechna síťová zařízení musí být nakonfigurovaná pomocí jejich nejbezpečnější konfigurace.

- \[Verze Preview \] : nasazení požadavků pro audit konfigurací virtuálních počítačů s Windows v možnosti zabezpečení-síťový přístup

### <a name="ns-95--all-network-connections-to-a-system-through-a-firewall-must-be-approved-and-audited-on-a-regular-basis"></a>NS-9,5 – všechna síťová připojení k systému prostřednictvím brány firewall musí být schválena a pravidelně auditována.

- \[Preview \] : Zobrazit výsledky auditu z konfigurací virtuálních počítačů s Windows ve vlastnostech brány Windows Firewall

### <a name="ns-97--appropriate-controls-must-be-present-at-any-boundary-between-a-trusted-network-and-any-untrusted-or-public-network"></a>NS-9,7: příslušné ovládací prvky musí být k dispozici na hranici mezi důvěryhodnou sítí a jakoukoli nedůvěryhodnou nebo veřejnou sítí.

- \[Verze Preview \] : nasazení požadavků pro audit konfigurací virtuálních počítačů s Windows ve vlastnostech brány Windows Firewall

## <a name="security-planning"></a>Plánování zabezpečení

### <a name="sp-113--threats-must-be-identified-that-could-negatively-impact-the-confidentiality-integrity-or-availability-of-company-information-and-content-along-with-the-likelihood-of-their-occurrence"></a>SP-11,3 – hrozby musí být identifikovány, které by mohly negativně ovlivnit důvěrnost, integritu a dostupnost informací společnosti a obsahu a také pravděpodobnost jejich výskytu.

- V rozšířených nastaveních zabezpečení dat spravované instance SQL by se měly nastavit rozšířené typy ochrany před internetovými útoky na ALL.

### <a name="security-continuity"></a>Kontinuita zabezpečení

## <a name="sc-125--data-in-long-term-storage-must-be-accessible-throughout-the-retention-period-and-protected-against-media-degradation-and-technology-changes"></a>SC-12,5 – data v dlouhodobém úložišti musí být přístupná po dobu uchování a chráněná proti změnám v oblasti médií a technologií.

- SQL servery by měly být nakonfigurované s uchováním dat po dobu delší než 90 dní.
- Auditovat SQL servery nakonfigurované s dobou uchování auditování kratší než 90 dní.

## <a name="system-integrity"></a>Integrita systému

### <a name="si-143--only-authorized-personnel-may-monitor-network-and-user-activities"></a>Od-14,3 mohou sledovat aktivity sítě a uživatele jenom autorizovaní zaměstnanci.

- Ohrožení zabezpečení vašich databází SQL by mělo být opraveno
- Sledujte výsledky kontroly ohrožení zabezpečení a doporučení pro napravení ohrožení zabezpečení databáze.

### <a name="si-144--internet-facing-systems-must-have-intrusion-detection"></a>V-14,4-internetové systémy musí mít detekci vniknutí.

- Nasazení detekce hrozeb na SQL serverech

### <a name="si-1413--standardized-centrally-managed-anti-malware-software-should-be-implemented-across-the-company"></a>A-14,13 – standardní centrálně spravovaný antimalwarový software by se měl implementovat napříč společností.

- Nasadit výchozí rozšíření Microsoft IaaSAntimalware pro Windows Server

### <a name="si-1414--anti-malware-software-must-scan-computers-and-media-weekly-at-a-minimum"></a>-14,14 – antimalwarový software musí naskenovat počítače a média na minimum.

- Nasadit výchozí rozšíření Microsoft IaaSAntimalware pro Windows Server

## <a name="vulnerability-management"></a>Správa ohrožení zabezpečení

### <a name="vm-154--ensure-that-applications-are-scanned-for-vulnerabilities-on-a-monthly-basis"></a>VM-15,4 – zajistěte, aby aplikace kontrolovaly ohrožení zabezpečení měsíčně.

- V konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů by se měly napravit ohrožení zabezpečení.
- Auditujte ohrožení zabezpečení operačního systému ve službě Virtual Machine Scale Sets, abyste je chránili před útoky.

### <a name="vm-155--ensure-that-vulnerabilities-are-identified-paired-to-threats-and-evaluated-for-risk"></a>VM-15,5 – zajistěte, aby se chyby zabezpečení identifikovaly, spárovány s hrozbami a vyhodnotily riziko.

- V konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů by se měly napravit ohrožení zabezpečení.
- Auditujte ohrožení zabezpečení operačního systému ve službě Virtual Machine Scale Sets, abyste je chránili před útoky.

### <a name="vm-156--ensure-that-identified-vulnerabilities-have-been-remediated-within-a-mutually-agreed-upon-timeline"></a>VM-15,6 – zajistěte, aby zjištěná ohrožení zabezpečení byla opravena v rámci vzájemného schválení na časové ose.

- V konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů by se měly napravit ohrožení zabezpečení.
- Auditujte ohrožení zabezpečení operačního systému ve službě Virtual Machine Scale Sets, abyste je chránili před útoky.

### <a name="vm-157--access-to-and-use-of-vulnerability-management-systems-must-be-restricted-to-authorized-personnel"></a>VM-15,7 – přístup k systémům pro správu ohrožení zabezpečení a jeho použití musí být omezen na autorizované zaměstnance.

- V konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů by se měly napravit ohrožení zabezpečení.
- Auditujte ohrožení zabezpečení operačního systému ve službě Virtual Machine Scale Sets, abyste je chránili před útoky.

> [!NOTE]
> Dostupnost konkrétních definic Azure Policy se může v Azure Government a dalších národních cloudech lišit. 

## <a name="next-steps"></a>Další kroky

Zkontrolovali jste mapování ovládacího prvku ukázka pro Media details. Další informace o přehledu a způsobu nasazení této ukázky najdete v následujících článcích:

> [!div class="next step action"]
> [Media Details – přehled](./control-mapping.md) 
>  [Media Details – postup nasazení](./deploy.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Další informace o [životním cyklu podrobného plánu](../../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../../how-to/update-existing-assignments.md)
