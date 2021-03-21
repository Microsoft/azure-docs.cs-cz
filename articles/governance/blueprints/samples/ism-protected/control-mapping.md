---
title: Ukázkové ovládací prvky podrobného plánu podrobného plánu australské vlády ISM
description: Mapování ovládacího prvku australského plánu CHRÁNĚNÉho plánu pro státní správu ISM Každý ovládací prvek je namapován na jednu nebo více Azure Policy definic, které pomáhají s posouzením.
ms.date: 01/21/2021
ms.topic: sample
ms.openlocfilehash: df143a871db3d871fe6354eab23b7ca6dc012b78
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98679993"
---
# <a name="control-mapping-of-the-australian-government-ism-protected-blueprint-sample"></a>Mapování ovládacího prvku pro ukázkovou verzi plánu CHRÁNĚNÉho plánu australské vlády ISM

Následující článek podrobně popisuje, jak ukázka Azure Modrotiskys australské vlády ISM (PROTECTed Details) mapuje na chráněné ovládací prvky ISM. Další informace o ovládacích prvcích najdete v tématu [chráněném ISM](https://www.cyber.gov.au/ism).

Následující mapování jsou k ovládacím prvkům **chráněným pro ISM** . Pomocí navigace na pravé straně můžete přejít přímo k určitému mapování ovládacího prvku. Mnohé z mapovaných ovládacích prvků jsou implementovány s [Azure Policy](../../../policy/overview.md) iniciativou. Chcete-li si projít kompletní iniciativu, otevřete **zásadu** v Azure Portal a vyberte stránku **definice** . Pak vyhledejte a vyberte ve **\[ verzi Preview \] : audit australské vlády ISM chráněné ovládací prvky a nasaďte specifická rozšíření virtuálních počítačů, aby podporovaly** integrovanou zásadu požadavky na audit.

> [!IMPORTANT]
> Každý ovládací prvek níže je přidružen k jedné nebo více definicím [Azure Policy](../../../policy/overview.md) . Tyto zásady vám pomůžou [zhodnotit dodržování předpisů](../../../policy/how-to/get-compliance-data.md) pomocí ovládacího prvku. Nicméně často není jedna nebo Úplná shoda mezi ovládacím prvkem a jednou nebo více zásadami. V takovém případě **vyhovuje** v Azure Policy pouze zásadám, které jsou samotné. Tím se nezajistí, že budete plně kompatibilní se všemi požadavky ovládacího prvku. Standard kompatibility zahrnuje i ovládací prvky, které nejsou v tuto chvíli řešeny žádnými definicemi Azure Policy. Proto je dodržování předpisů v Azure Policy jenom částečný pohled na celkový stav dodržování předpisů. Přidružení mezi ovládacími prvky a definicemi Azure Policy pro tuto ukázku podrobného plánu dodržování předpisů se mohou v průběhu času měnit. Historii změn si můžete prohlédnout v [historii potvrzení GitHubu](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/ism-protected/control-mapping.md).


## <a name="location-constraints"></a>Omezení umístění

Tento podrobný plán vám pomůže omezit umístění nasazení všech prostředků a skupin prostředků na "Austrálie Central", "Austrálie Central2", "Austrálie – východ" a "Austrálie – jihovýchod" přiřazením následujících Azure Policych definic:

- Povolená umístění (byla pevně zakódována do "Austrálie Central", "Austrálie Central2", "Austrálie – východ" a "Austrálie – jihovýchod")
- Povolená umístění pro skupiny prostředků (byla pevně zakódována do "Austrálie Central", "Austrálie Central2", "Austrálie – východ" a "Austrálie – jihovýchod")

## <a name="guidelines-for-personnel-security---access-to-systems-and-their-resources"></a>Pokyny pro personální zabezpečení – přístup k systémům a jejich prostředkům

### <a name="0414-personnel-granted-access-to-a-system-and-its-resources-are-uniquely-identifiable"></a>0414 personál, kterému je udělený přístup k systému, a jeho prostředky jsou jednoznačně identifikovatelné

- Pro účty s oprávněním vlastníka pro vaše předplatné by se měla povolit vícefaktorové ověřování.
- Pro účty s oprávněními k zápisu v předplatném by se mělo povolit MFA.
- Pro účty s oprávněním ke čtení vašeho předplatného by se měla povolit vícefaktorové ověřování.

### <a name="1503-standard-access-to-systems-applications-and-data-repositories-is-limited-to-that-required-for-personnel-to-undertake-their-duties"></a>1503 standardní přístup k systémům, aplikacím a úložištím dat je omezený na to, aby pracovníci mohli plnit své povinnosti.

- Pro vaše předplatné by se mělo určit maximálně 3 vlastníci.
- K vašemu předplatnému by měl být přiřazený víc než jeden vlastník.
- Zobrazit výsledky auditu z virtuálních počítačů s Windows, ve kterých skupina Administrators obsahuje některé ze zadaných členů
- Nasaďte požadavky na auditování virtuálních počítačů s Windows, ve kterých skupina Administrators obsahuje některé ze zadaných členů.

### <a name="1507-privileged-access-to-systems-applications-and-data-repositories-is-validated-when-first-requested-and-revalidated-on-an-annual-or-more-frequent-basis"></a>1507 privilegovaný přístup k systémům, aplikacím a úložištím dat se ověřuje, když se poprvé vyžádá a znovu ověří na roční nebo častější úrovni.

- Zobrazit výsledky auditu z virtuálních počítačů s Windows, ve kterých skupina Administrators obsahuje některé ze zadaných členů
- Nasaďte požadavky na auditování virtuálních počítačů s Windows, ve kterých skupina Administrators obsahuje některé ze zadaných členů.

### <a name="1508-privileged-access-to-systems-applications-and-data-repositories-is-limited-to-that-required-for-personnel-to-undertake-their-duties"></a>1508 privilegovaný přístup k systémům, aplikacím a úložištím dat je omezený na to, aby pracovníci mohli plnit své povinnosti.

- Pro vaše předplatné by se mělo určit maximálně 3 vlastníci.
- K vašemu předplatnému by měl být přiřazený víc než jeden vlastník.
- Zobrazit výsledky auditu z virtuálních počítačů s Windows, ve kterých skupina Administrators obsahuje některé ze zadaných členů
- Nasaďte požadavky na auditování virtuálních počítačů s Windows, ve kterých skupina Administrators obsahuje některé ze zadaných členů.
- Na virtuálních počítačích by se mělo používat řízení přístupu k síti podle potřeby

### <a name="0415-the-use-of-shared-user-accounts-is-strictly-controlled-and-personnel-using-such-accounts-are-uniquely-identifiable"></a>0415 používání sdílených uživatelských účtů je čistě řízeno a zaměstnanci, kteří tyto účty používají, jsou jednoznačně identifikovatelné.

- Zobrazit výsledky auditu z virtuálních počítačů s Windows, ve kterých skupina Administrators obsahuje některé ze zadaných členů
- Nasaďte požadavky na auditování virtuálních počítačů s Windows, ve kterých skupina Administrators obsahuje některé ze zadaných členů.

### <a name="0445-privileged-users-are-assigned-a-dedicated-privileged-account-to-be-used-solely-for-tasks-requiring-privileged-access"></a>0445 privilegovaným uživatelům se přiřadí vyhrazený privilegovaný účet, který se má použít výhradně pro úlohy vyžadující privilegovaný přístup.

- Zobrazit výsledky auditu z virtuálních počítačů s Windows, ve kterých skupina Administrators obsahuje některé ze zadaných členů
- Nasaďte požadavky na auditování virtuálních počítačů s Windows, ve kterých skupina Administrators obsahuje některé ze zadaných členů.

### <a name="0430-access-to-systems-applications-and-data-repositories-is-removed-or-suspended-on-the-same-day-personnel-no-longer-have-a-legitimate-requirement-for-access"></a>0430 přístup k systémům, aplikacím a úložištím dat se na stejný den odeberou nebo pozastaví zaměstnanci, kteří už nemají oprávněný požadavek na přístup.

- Zastaralé účty by se měly odebírat z předplatného.
- Zastaralé účty s oprávněním vlastníka by se měly odebrat z vašeho předplatného.

### <a name="0441-when-personnel-are-granted-temporary-access-to-a-system-effective-security-controls-are-put-in-place-to-restrict-their-access-to-only-information-required-for-them-to-undertake-their-duties"></a>0441 když se zaměstnancům udělí dočasný přístup k systému, jsou zavedené účinné bezpečnostní mechanismy, které omezují jejich přístup jenom na informace, které by jim mohly plnit své povinnosti.

- Z vašeho předplatného byste měli odebrat externí účty s oprávněním vlastníka.
- Z vašeho předplatného byste měli odebrat externí účty s oprávněním k zápisu.
- Zastaralé účty by se měly odebírat z předplatného.
- Zastaralé účty s oprávněním vlastníka by se měly odebrat z vašeho předplatného.

## <a name="guidelines-for-system-hardening---operating-system-hardening"></a>Pokyny pro posílení zabezpečení systému – posílení zabezpečení operačního systému

### <a name="1407-the-latest-version-n-or-n-1-version-of-an-operating-system-is-used-for-standard-operating-environments-soes"></a>1407 pro standardní provozní prostředí se používá nejnovější verze (N) nebo N-1 verze operačního systému (SOEs).

- Na počítače by se měly nainstalovat aktualizace systému
- Musí být nainstalované aktualizace systému ve virtuálních počítačích Virtual Machine Scale Sets.

### <a name="0380-unneeded-operating-system-accounts-software-components-services-and-functionality-are-removed-or-disabled"></a>0380 nepotřebné účty operačního systému, software, součásti, služby a funkce se odeberou nebo deaktivují.

- Zastaralé účty by se měly odebírat z předplatného.
- Zastaralé účty s oprávněním vlastníka by se měly odebrat z vašeho předplatného.

### <a name="1490-an-application-whitelisting-solution-is-implemented-on-all-servers-to-restrict-the-execution-of-executables-software-libraries-scripts-and-installers-to-an-approved-set"></a>1490 řešení na seznamu povolených aplikací se implementuje na všech serverech, aby se omezilo spouštění spustitelných souborů, knihoven softwaru, skriptů a instalačních programů do schválené sady.

- Na virtuálních počítačích by měly být povolené Adaptivní řízení aplikací.

### <a name="1417-antivirus-software-is-implemented-on-workstations-and-servers-and-configured-with-signature-based-detection-enabled-and-set-to-a-high-level-heuristic-based-detection-enabled-and-set-to-a-high-level-detection-signatures-checked-for-currency-and-updated-on-at-least-a-daily-basis-automatic-and-regular-scanning-configured-for-all-fixed-disks-and-removable-media"></a>1417 antivirový software se implementuje na pracovních stanicích a serverech a nakonfigurovaném pomocí: detekce na základě podpisu je zapnutá a nastavená na vysokou úroveň, povolená detekce heuristického běhu a nastavená na vysokou úroveň, signatury, které jsou povolené pro všechny pevné disky a vyměnitelná média aspoň denně

- Rozšíření Microsoft IaaSAntimalware Extension by mělo být nasazeno na serverech Windows
- Řešení ochrany koncových bodů by se mělo nainstalovat na Virtual Machine Scale Sets.
- Monitorovat chybějící Endpoint Protection v Azure Security Center

## <a name="guidelines-for-system-hardening---authentication-hardening"></a>Pokyny pro posílení zabezpečení systému – posílení zabezpečení při ověřování

### <a name="1546-users-are-authenticated-before-they-are-granted-access-to-a-system-and-its-resources"></a>1546 uživatelů se ověřuje předtím, než jim udělí přístup k systému a jeho prostředkům.

- Auditování neomezeného síťového přístupu k účtům úložiště
- Clustery Service Fabric by se měly používat jenom Azure Active Directory pro ověřování klientů.
- Zobrazit výsledky auditu z virtuálních počítačů se systémem Linux, které umožňují vzdálená připojení z účtů bez hesel
- Nasazení požadavků pro audit virtuálních počítačů Linux, které umožňují vzdálená připojení z účtů bez hesel
- Zobrazit výsledky auditu z virtuálních počítačů se systémem Linux, které mají účty bez hesla
- Nasazení požadavků pro audit virtuálních počítačů se systémem Linux, které mají účty bez hesla

### <a name="0974-multi-factor-authentication-is-used-to-authenticate-standard-users"></a>pro ověřování standardních uživatelů se používá 0974 Multi-Factor Authentication.

- Pro účty s oprávněním ke čtení vašeho předplatného by se měla povolit vícefaktorové ověřování.

### <a name="1173-multi-factor-authentication-is-used-to-authenticate-all-privileged-users-and-any-other-positions-of-trust"></a>1173 Multi-Factor Authentication se používá k ověřování všech privilegovaných uživatelů a všech dalších pozic důvěry.

- Pro účty s oprávněním vlastníka pro vaše předplatné by se měla povolit vícefaktorové ověřování.
- Pro účty s oprávněními k zápisu v předplatném by se mělo povolit MFA.

### <a name="0421-passphrases-used-for-single-factor-authentication-are-a-minimum-of-14-characters-with-complexity-ideally-as-4-random-words"></a>0421 přístupových hesel používaných pro jednotné vícefaktorové ověřování je minimálně 14 znaků a složitá, v ideálním případě 4 náhodná slova.

- Zobrazit výsledky auditu z konfigurací virtuálních počítačů s Windows v nastavení zabezpečení – Zásady účtů
- Nasazení požadavků pro audit konfigurací virtuálních počítačů s Windows v nastavení zabezpečení – Zásady účtů

## <a name="guidelines-for-system-management---system-administration"></a>Pokyny pro správu systému – Správa systému

### <a name="1384-multi-factor-authentication-is-used-to-authenticate-users-each-time-they-perform-privileged-actions"></a>1384 Multi-Factor Authentication se používá k ověřování uživatelů při každém provedení privilegovaných akcí.

- Pro účty s oprávněním vlastníka pro vaše předplatné by se měla povolit vícefaktorové ověřování.
- Pro účty s oprávněními k zápisu v předplatném by se mělo povolit MFA.
- Pro účty s oprávněním ke čtení vašeho předplatného by se měla povolit vícefaktorové ověřování.

### <a name="1386-management-traffic-is-only-allowed-to-originate-from-network-zones-that-are-used-to-administer-systems-and-applications"></a>1386 provoz správy je povolený jenom ze síťových zón, které se používají ke správě systémů a aplikací.

- Na virtuálních počítačích by se mělo používat řízení přístupu k síti podle potřeby
- Vzdálené ladění by mělo být pro API Apps vypnuté.
- Pro aplikace Function app by mělo být vypnuto vzdálené ladění.
- Vzdálené ladění by mělo být pro webové aplikace vypnuté.

## <a name="guidelines-for-system-management---system-patching"></a>Pokyny pro správu systému – systémové opravy

### <a name="1144-security-vulnerabilities-in-applications-and-drivers-assessed-as-extreme-risk-are-patched-updated-or-mitigated-within-48-hours-of-the-security-vulnerabilities-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1144 chyby zabezpečení v aplikacích a ovladačích vyhodnocených jako extrémní riziko jsou opraveny, aktualizovány nebo omezeny do 48 hodin zabezpečení zjištěných v rámci dodavatelů, nezávislých třetích stran, správců systému nebo uživatelů

- Ohrožení zabezpečení vašich databází SQL by mělo být opraveno
- Na vašich serverech SQL by mělo být povolené posouzení ohrožení zabezpečení
- Na spravovaných instancích SQL by mělo být povolené posouzení ohrožení zabezpečení
- Posouzení ohrožení zabezpečení by mělo být povoleno na počítačích
- V konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů by se měly napravit ohrožení zabezpečení.
- Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení.
- Ohrožení zabezpečení v konfiguraci zabezpečení na vašich počítačích by mělo být opraveno
- Ohrožení zabezpečení v konfiguraci zabezpečení kontejneru by mělo být opraveno
- Nastavení posouzení ohrožení zabezpečení pro SQL Server by mělo obsahovat e-mailovou adresu pro příjem sestav kontroly.

### <a name="0940-security-vulnerabilities-in-applications-and-drivers-assessed-as-high-risk-are-patched-updated-or-mitigated-within-two-weeks-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>0940 chyby zabezpečení v aplikacích a ovladačích vyhodnocených jako vysoké riziko jsou opraveny, aktualizovány nebo zmírněny během dvou týdnů od zjištění ohrožení zabezpečení dodavatelem, nezávislých třetích stran, správců systému nebo uživatelů.

- Ohrožení zabezpečení vašich databází SQL by mělo být opraveno
- Na vašich serverech SQL by mělo být povolené posouzení ohrožení zabezpečení
- Na spravovaných instancích SQL by mělo být povolené posouzení ohrožení zabezpečení
- Posouzení ohrožení zabezpečení by mělo být povoleno na Virtual Machines
- V konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů by se měly napravit ohrožení zabezpečení.
- Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení.
- Ohrožení zabezpečení v konfiguraci zabezpečení na vašich počítačích by mělo být opraveno
- Ohrožení zabezpečení v konfiguraci zabezpečení kontejneru by mělo být opraveno
- Nastavení posouzení ohrožení zabezpečení pro SQL Server by mělo obsahovat e-mailovou adresu pro příjem sestav kontroly.

### <a name="1472-security-vulnerabilities-in-applications-and-drivers-assessed-as-moderate-or-low-risk-are-patched-updated-or-mitigated-within-one-month-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1472 chyby zabezpečení v aplikacích a ovladačích vyhodnocených jako střední nebo nízké riziko jsou opraveny, aktualizovány nebo omezeny na jeden měsíc, který identifikuje dodavatelé, nezávislí třetí strany, správci systému nebo uživatelé

- Ohrožení zabezpečení vašich databází SQL by mělo být opraveno
- Na vašich serverech SQL by mělo být povolené posouzení ohrožení zabezpečení
- Na spravovaných instancích SQL by mělo být povolené posouzení ohrožení zabezpečení
- Posouzení ohrožení zabezpečení by mělo být povoleno na Virtual Machines
- V konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů by se měly napravit ohrožení zabezpečení.
- Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení.
- Ohrožení zabezpečení v konfiguraci zabezpečení na vašich počítačích by mělo být opraveno
- Ohrožení zabezpečení v konfiguraci zabezpečení kontejneru by mělo být opraveno
- Nastavení posouzení ohrožení zabezpečení pro SQL Server by mělo obsahovat e-mailovou adresu pro příjem sestav kontroly.

### <a name="1494-security-vulnerabilities-in-operating-systems-and-firmware-assessed-as-extreme-risk-are-patched-updated-or-mitigated-within-48-hours-of-the-security-vulnerabilities-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1494 chyby zabezpečení v operačních systémech a firmware vyhodnocených jako extrémní riziko jsou opraveny, aktualizovány nebo omezeny do 48 hodin zabezpečení zjištěných v rámci dodavatelů, nezávislých třetích stran, správců systému nebo uživatelů

- Ohrožení zabezpečení vašich databází SQL by mělo být opraveno
- Na vašich serverech SQL by mělo být povolené posouzení ohrožení zabezpečení
- Na spravovaných instancích SQL by mělo být povolené posouzení ohrožení zabezpečení
- Posouzení ohrožení zabezpečení by mělo být povoleno na Virtual Machines
- V konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů by se měly napravit ohrožení zabezpečení.
- Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení.
- Ohrožení zabezpečení v konfiguraci zabezpečení na vašich počítačích by mělo být opraveno
- Ohrožení zabezpečení v konfiguraci zabezpečení kontejneru by mělo být opraveno
- Nastavení posouzení ohrožení zabezpečení pro SQL Server by mělo obsahovat e-mailovou adresu pro příjem sestav kontroly.

### <a name="1495-security-vulnerabilities-in-operating-systems-and-firmware-assessed-as-high-risk-are-patched-updated-or-mitigated-within-two-weeks-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1495 chyby zabezpečení v operačních systémech a firmware vyhodnocených jako vysoké riziko jsou opraveny, aktualizovány nebo zmírněny během dvou týdnů od zjištění ohrožení zabezpečení dodavatelem, nezávislých třetích stran, správců systému nebo uživatelů.

- Ohrožení zabezpečení vašich databází SQL by mělo být opraveno
- Na vašich serverech SQL by mělo být povolené posouzení ohrožení zabezpečení
- Na spravovaných instancích SQL by mělo být povolené posouzení ohrožení zabezpečení
- Posouzení ohrožení zabezpečení by mělo být povoleno na Virtual Machines
- V konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů by se měly napravit ohrožení zabezpečení.
- Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení.
- Ohrožení zabezpečení v konfiguraci zabezpečení na vašich počítačích by mělo být opraveno
- Ohrožení zabezpečení v konfiguraci zabezpečení kontejneru by mělo být opraveno
- Nastavení posouzení ohrožení zabezpečení pro SQL Server by mělo obsahovat e-mailovou adresu pro příjem sestav kontroly.

### <a name="1496-security-vulnerabilities-in-operating-systems-and-firmware-assessed-as-moderate-or-low-risk-are-patched-updated-or-mitigated-within-one-month-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1496 chyby zabezpečení v operačních systémech a firmware vyhodnocených jako střední nebo nízké riziko jsou opraveny, aktualizovány nebo omezeny na jeden měsíc, který identifikuje dodavatelé, nezávislí třetí strany, správci systému nebo uživatelé

- Ohrožení zabezpečení vašich databází SQL by mělo být opraveno
- Na vašich serverech SQL by mělo být povolené posouzení ohrožení zabezpečení
- Na spravovaných instancích SQL by mělo být povolené posouzení ohrožení zabezpečení
- Posouzení ohrožení zabezpečení by mělo být povoleno na Virtual Machines
- V konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů by se měly napravit ohrožení zabezpečení.
- Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení.
- Ohrožení zabezpečení v konfiguraci zabezpečení na vašich počítačích by mělo být opraveno
- Ohrožení zabezpečení v konfiguraci zabezpečení kontejneru by mělo být opraveno
- Nastavení posouzení ohrožení zabezpečení pro SQL Server by mělo obsahovat e-mailovou adresu pro příjem sestav kontroly.

## <a name="guidelines-for-system-management---data-backup-and-restoration"></a>Pokyny pro správu systému – zálohování a obnovení dat

### <a name="1511-backups-of-important-information-software-and-configuration-settings-are-performed-at-least-daily"></a>1511 zálohování důležitých informací, nastavení softwaru a konfigurace se provádí alespoň denně.

- Auditovat virtuální počítače bez nakonfigurovaného zotavení po havárii

## <a name="guidelines-for-system-monitoring---event-logging-and-auditing"></a>Pokyny pro monitorování systému – protokolování událostí a auditování

### <a name="1405-a-centralised-logging-facility-is-implemented-and-systems-are-configured-to-save-event-logs-to-the-centralised-logging-facility-as-soon-as-possible-after-each-event-occurs"></a>1405 je implementováno centralizované Protokolovací zařízení a systémy jsou nakonfigurovány pro ukládání protokolů událostí do centralizovaného protokolovacího zařízení co nejdříve po výskytu každé události.

- Předplatná Azure by měla mít profil protokolu aktivit.

### <a name="0582-the-following-events-are-logged-for-operating-systems-access-to-important-data-and-processes-application-crashes-and-any-error-messages-attempts-to-use-special-privileges-changes-to-accounts-changes-to-security-policy-changes-to-system-configurations-domain-name-system-dns-and-hypertext-transfer-protocol-http-requests-failed-attempts-to-access-data-and-system-resources-service-failures-and-restarts-system-startup-and-shutdown-transfer-of-data-to-external-media-user-or-group-management-use-of-special-privileges"></a>0582 následující události jsou protokolovány pro operační systémy: přístup k důležitým datům a procesům, selháním aplikací a jakýmkoli chybovým zprávám, se pokusí použít zvláštní oprávnění, změny účtů, změny v zásadách zabezpečení, změny konfigurace systému, DNS (Domain Name System) a HTTP (Hypertext Transfer Protocol), neúspěšné pokusy o přístup k datům a systémovým prostředkům, spuštění a restartování systému , přenos dat na externí média, uživatele nebo správu skupin, použití zvláštních oprávnění

- \[Verze Preview \] : Audit Log Analytics Deployment Agent – image virtuálního počítače (OS) bez seznamu
- Audit Log Analytics nasazení agenta v VMSS-VM Image (OS) bez seznamu
- Audit Log Analytics pracovní prostor pro virtuální počítač – neshoda sestavy
- Auditování nastavení diagnostiky

### <a name="1537-the-following-events-are-logged-for-databases-access-to-particularly-important-information-addition-of-new-users-especially-privileged-users-any-query-containing-comments-any-query-containing-multiple-embedded-queries-any-query-or-database-alerts-or-failures-attempts-to-elevate-privileges-attempted-access-that-is-successful-or-unsuccessful-changes-to-the-database-structure-changes-to-user-roles-or-database-permissions-database-administrator-actions-database-logons-and-logoffs-modifications-to-data-use-of-executable-commands"></a>1537 pro databáze jsou protokolovány následující události: přístup k obzvláště důležitým informacím, přidání nových uživatelů, zvláště privilegovaných uživatelů, všech dotazech obsahujících komentáře, všech dotazech, které obsahují několik vložených dotazů, všech dotazech a výstrahách nebo selháních databáze, se pokusí o zvýšení oprávnění, pokus o přístup, který je úspěšný nebo neúspěšný, změny ve struktuře databáze, změny rolí uživatele nebo oprávnění databáze a akce správce databáze , přihlášení a odhlášení databáze, úpravy dat, použití spustitelných příkazů

- Na vašich serverech SQL by mělo být povolené rozšířené zabezpečení dat
- Auditování nastavení diagnostiky
- Pro spravované instance SQL by mělo být povolené rozšířené zabezpečení dat

## <a name="guidelines-for-system-monitoring---vulnerability-management"></a>Pokyny pro monitorování systému – Správa ohrožení zabezpečení

### <a name="0911-vulnerability-assessments-and-penetration-tests-are-conducted-by-suitably-skilled-personnel-before-a-system-is-deployed-after-a-significant-change-to-a-system-and-at-least-annually-or-as-specified-by-the-system-owner"></a>0911 posouzení ohrožení zabezpečení a testy průniku provádějí vhodně kvalifikovaný personál před nasazením systému, po významné změně systému a nejméně jednou ročně nebo podle určení vlastníkem systému.

- Ohrožení zabezpečení vašich databází SQL by mělo být opraveno
- Na vašich serverech SQL by mělo být povolené posouzení ohrožení zabezpečení
- Na spravovaných instancích SQL by mělo být povolené posouzení ohrožení zabezpečení
- Posouzení ohrožení zabezpečení by mělo být povoleno na Virtual Machines
- V konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů by se měly napravit ohrožení zabezpečení.
- Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení.
- Ohrožení zabezpečení v konfiguraci zabezpečení na vašich počítačích by mělo být opraveno
- Ohrožení zabezpečení v konfiguraci zabezpečení kontejneru by mělo být opraveno

## <a name="guidelines-for-database-systems-management---database-servers"></a>Pokyny pro správu databázových systémů – databázové servery

### <a name="1425-hard-disks-of-database-servers-are-encrypted-using-full-disk-encryption"></a>1425 pevné disky databázových serverů se šifrují pomocí úplného šifrování disku.

- Na virtuálních počítačích by se mělo použít šifrování disku
- Je třeba povolit transparentní šifrování dat databází SQL.

### <a name="1277-information-communicated-between-database-servers-and-web-applications-is-encrypted"></a>informace 1277 mezi databázovými servery a webovými aplikacemi jsou zašifrované.

- Měla by být povolená jenom zabezpečená připojení k vašemu Redis Cache.
- Měl by se povolit zabezpečený přenos do účtů úložiště
- Zobrazit výsledky auditu z webových serverů Windows, které nepoužívají protokoly zabezpečené komunikace
- Nasazení požadavků pro audit webových serverů Windows, které nepoužívají zabezpečené komunikační protokoly

## <a name="guidelines-for-database-systems-management---database-management-system-software"></a>Pokyny pro správu databázových systémů – software pro správu databází

### <a name="1260-default-database-administrator-accounts-are-disabled-renamed-or-have-their-passphrases-changed"></a>1260 výchozí účty správců databáze jsou zakázané, přejmenované nebo se změnila jejich hesla.

- Pro SQL servery by se měl zřídit správce Azure Active Directory.

### <a name="1262-database-administrators-have-unique-and-identifiable-accounts"></a>1262 správci databáze mají jedinečné a identifikovatelné účty.

- Pro SQL servery by se měl zřídit správce Azure Active Directory.

### <a name="1261-database-administrator-accounts-are-not-shared-across-different-databases"></a>účty správců databáze 1261 nejsou sdíleny mezi různými databázemi.

- Pro SQL servery by se měl zřídit správce Azure Active Directory.

### <a name="1263-database-administrator-accounts-are-used-exclusively-for-administrative-tasks-with-standard-database-accounts-used-for-general-purpose-interactions-with-database"></a>účty správce databáze 1263 se používají výhradně pro úlohy správy, které mají standardní databázové účty používané pro interakce pro obecné účely s databází.

- Pro SQL servery by se měl zřídit správce Azure Active Directory.

### <a name="1264-database-administrator-access-is-restricted-to-defined-roles-rather-than-accounts-with-default-administrative-permissions-or-all-permissions"></a>1264 přístup správce databáze je omezen na definované role, nikoli na účty s výchozími oprávněními pro správu nebo na všechna oprávnění.

- Pro SQL servery by se měl zřídit správce Azure Active Directory.

## <a name="guidelines-for-using-cryptography---cryptographic-fundamentals"></a>Pokyny pro použití kryptografie – základy kryptografie

### <a name="0459-encryption-software-used-for-data-at-rest-implements-full-disk-encryption-or-partial-encryption-where-access-controls-will-only-allow-writing-to-the-encrypted-partition"></a>0459 šifrovací software, který se používá pro data v klidovém stavu, implementuje úplné šifrování disků nebo částečné šifrování, pokud řízení přístupu povoluje zápis pouze do šifrovaného oddílu.

- Na virtuálních počítačích by se mělo použít šifrování disku

## <a name="guidelines-for-using-cryptography---transport-layer-security"></a>Pokyny pro použití kryptografie-Transport Layer Security

### <a name="1139-only-the-latest-version-of-tls-is-used"></a>1139 pouze nejnovější verze TLS se používá.

- V aplikaci API by se měla použít nejnovější verze TLS.
- Ve vaší webové aplikaci by se měla použít nejnovější verze TLS.
- V Function App by se měla použít nejnovější verze TLS.
- Nasazení požadavků pro audit webových serverů Windows, které nepoužívají zabezpečené komunikační protokoly
- Zobrazit výsledky auditu z webových serverů Windows, které nepoužívají protokoly zabezpečené komunikace

## <a name="guidelines-for-data-transfers-and-content-filtering---content-filtering"></a>Pokyny pro přenos dat a filtrování obsahu – filtrování obsahu

### <a name="1288-antivirus-scanning-using-multiple-different-scanning-engines-is-performed-on-all-content"></a>1288 antivirová kontrola, použití více různých skenovacích strojů, se provádí u veškerého obsahu.

- Rozšíření Microsoft IaaSAntimalware Extension by mělo být nasazeno na serverech Windows
- Řešení ochrany koncových bodů by se mělo nainstalovat na Virtual Machine Scale Sets.
- Monitorovat chybějící Endpoint Protection v Azure Security Center

## <a name="guidelines-for-data-transfers-and-content-filtering---web-application-development"></a>Pokyny pro přenos dat a filtrování obsahu – vývoj webových aplikací

### <a name="1552-all-web-application-content-is-offered-exclusively-using-https"></a>1552 veškerý obsah webových aplikací se nabízí výhradně pomocí protokolu HTTPS.

- Function App by měl být přístupný jenom přes HTTPS
- Aplikace API by měla být přístupná jen přes protokol HTTPS
- Webová aplikace by měla být přístupná jen přes protokol HTTPS
- Měla by být povolená jenom zabezpečená připojení k vašemu Redis Cache.

### <a name="1424-web-browser-based-security-controls-are-implemented-for-web-applications-in-order-to-help-protect-both-web-applications-and-their-users"></a>1424 ovládací prvky zabezpečení na základě webového prohlížeče jsou implementovány pro webové aplikace, aby bylo možné lépe chránit webové aplikace i jejich uživatele.

- CORS by neměl umožňovat každému prostředku přístup k vašim webovým aplikacím

## <a name="guidelines-for-network-management---network-design-and-configuration"></a>Pokyny pro správu sítě – návrh a konfigurace sítě

### <a name="0520-network-access-controls-are-implemented-on-networks-to-prevent-the-connection-of-unauthorised-network-devices"></a>0520 řízení přístupu k síti je implementováno v sítích, aby nedocházelo k připojení neautorizovaných síťových zařízení.

- Auditování neomezeného síťového přístupu k účtům úložiště

### <a name="1182-network-access-controls-are-implemented-to-limit-traffic-within-and-between-network-segments-to-only-those-that-are-required-for-business-purposes"></a>1182 řízení přístupu k síti je implementováno pro omezení provozu v rámci a mezi segmenty sítě pouze do těch, které jsou požadovány pro obchodní účely.

- Virtuální počítače s přístupem k Internetu by měly být chráněné pomocí skupin zabezpečení sítě
- Auditování neomezeného síťového přístupu k účtům úložiště
- Doporučení adaptivního posílení zabezpečení sítě by se měla použít na internetových virtuálních počítačích.

## <a name="guidelines-for-network-management---service-continuity-for-online-services"></a>Pokyny pro správu sítě – Kontinuita služeb pro online služby

### <a name="1431-denial-of-service-attack-prevention-and-mitigation-strategies-are-discussed-with-service-providers-specifically-their-capacity-to-withstand-denial-of-service-attacks-any-costs-likely-to-be-incurred-by-customers-resulting-from-denial-of-service-attacks-thresholds-for-notifying-customers-or-turning-off-their-online-services-during-denial-of-service-attacks-pre-approved-actions-that-can-be-undertaken-during-denial-of-service-attacks-denial-of-service-attack-prevention-arrangements-with-upstream-providers-to-block-malicious-traffic-as-far-upstream-as-possible"></a>1431 odmítnutí útoků na služby a strategie pro zmírnění rizik jsou popsány v tématu poskytovatelé služeb, konkrétně: jejich kapacita k vypadnutí útoků s cílem odepření služeb. všechny náklady, které by se mohly vysílat v důsledku útoků DOS (Denial-of-Service), prahové hodnoty pro oznamování zákazníkům nebo vypnutí jejich online služby při útokech DOS, předem schválených akcí, které je možné provést během útoků s cílem odepření služeb.

- Měla by být povolená DDoS Protection Standard.


> [!NOTE]
> Dostupnost konkrétních definic Azure Policy se může v Azure Government a dalších národních cloudech lišit. 

## <a name="next-steps"></a>Další kroky

Další články věnované podrobným plánům a postupu jejich využití:

> [!div class="nextstepaction"]
> [Chráněný podrobný plán pro ISM – přehled](./index.md) 
>  [Chráněný podrobný plán pro ISM – postup nasazení](./deploy.md)

- Další informace o [životním cyklu podrobného plánu](../../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../../how-to/update-existing-assignments.md)
