---
title: Ukázkové ovládací prvky HIPAA HITRUST details
description: Mapování ovládacího prvku pro ukázky HIPAA HITRUST details Každý ovládací prvek je namapován na jednu nebo více zásad Azure, které pomáhají s posouzením.
ms.date: 11/26/2019
ms.topic: sample
ms.openlocfilehash: c6680055cfe41a2f893d46cba797783913d880ce
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "75472307"
---
# <a name="control-mapping-of-the-hipaa-hitrust-blueprint-sample"></a>Mapování ovládacího prvku Sample HIPAA HITRUST details

Následující článek podrobně popisuje, jak se vzor Azure modrotisky HIPAA HITRUST Details mapuje na ovládací prvky HIPAA HITRUST. Další informace o ovládacích prvcích najdete v tématu [HIPAA HiTRUST](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

Následující mapování jsou k ovládacím prvkům **HIPAA HiTRUST** . Pomocí navigace na pravé straně můžete přejít přímo k určitému mapování ovládacího prvku. Mnohé z mapovaných ovládacích prvků jsou implementovány s [Azure Policy](../../../policy/overview.md) iniciativou. Chcete-li si projít kompletní iniciativu, otevřete **zásadu** v Azure Portal a vyberte stránku **definice** . Pak vyhledejte a vyberte ve ** \[verzi Preview\]: audit HIPAA HiTRUST ovládá** integrovanou iniciativu zásad.

> [!IMPORTANT]
> Každý ovládací prvek níže je přidružen k jedné nebo více definicím [Azure Policy](../../../policy/overview.md) . Tyto zásady vám pomůžou [zhodnotit dodržování předpisů](../../../policy/how-to/get-compliance-data.md) pomocí ovládacího prvku. často však není 1:1 nebo Úplná shoda mezi ovládacím prvkem a jednou nebo více zásadami. V takovém případě **vyhovuje** v Azure Policy pouze zásadám, které jsou samotné. Tím se nezajistí, že budete plně kompatibilní se všemi požadavky ovládacího prvku. Standard kompatibility zahrnuje i ovládací prvky, které nejsou v tuto chvíli řešeny žádnými definicemi Azure Policy. Proto je dodržování předpisů v Azure Policy jenom částečný pohled na celkový stav dodržování předpisů. Přidružení mezi ovládacími prvky a definicemi Azure Policy pro tuto ukázku podrobného plánu dodržování předpisů se mohou v průběhu času měnit. Historii změn si můžete prohlédnout v [historii potvrzení GitHubu](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/HIPAA-HITRUST/control-mapping.md).

## <a name="control-against-malicious-code"></a>Řízení proti škodlivému kódu

Tento podrobný plán vám pomůže spravovat ochranu koncových bodů včetně ochrany škodlivých kódů tím, že přiřazuje definice [Azure Policy](../../../policy/overview.md) , které monitorují chybějící Endpoint Protection na virtuálních počítačích v Azure Security Center a vynutila řešení Microsoft Antimalware na virtuálních počítačích s Windows.

- Nasazení výchozího antimalwarového rozšíření Microsoft IaaS pro Windows Server
- V účtech Batch by měly být povolené diagnostické protokoly.
- Na počítače by se měly nainstalovat aktualizace systému


## <a name="management-of-removable-media"></a>Správa vyměnitelného média

Organizace na základě úrovně klasifikace dat registruje média (včetně přenosných počítačů) před použitím, omezuje přiměřená omezení způsobu použití těchto médií a poskytuje odpovídající úroveň fyzické a logické ochrany (včetně šifrování) pro média obsahující zahrnuté informace, dokud není správně zničena nebo upravena.

- Vyžadovat šifrování u Data Lake Store účtů
- Ochrana TDE spravované instance SQL by se měla šifrovat pomocí vlastního klíče.
- Na virtuálních počítačích by se mělo použít šifrování disku
- Je třeba povolit transparentní šifrování dat databází SQL.


## <a name="information-exchange-policies-and-procedures"></a>Zásady a postupy výměny informací

Poskytovatelé cloudových služeb používají pro zajištění interoperability rozpoznávanou virtualizační platformu a standardní virtualizační formáty (například Open Virtualization Format, OVF) a popsaly vlastní změny provedené v jakémkoli používaném hypervisoru a všechny moduly virtualizace pro konkrétní řešení, které jsou k dispozici pro kontrolu zákazníků.

- Nasazení požadavků pro audit virtuálních počítačů s Windows, u kterých nejsou nainstalované zadané aplikace

## <a name="control-of-operational-software"></a>Řízení provozního softwaru 

Organizace identifikuje neoprávněný software v informačním systému, včetně serverů, pracovních stanic a přenosných počítačů, vykonává zásady pro povolení všech výjimek, které zakazují spouštění známého neoprávněného softwaru v informačním systému, a pravidelně kontroluje a aktualizuje seznam neautorizovaného softwaru, ale ne méně než jednou ročně.

- \[Ve\] verzi Preview se zobrazují výsledky auditu z konfigurací virtuálních počítačů s Windows v části Možnosti zabezpečení – audit.
- \[Ukázka\] zobrazit výsledky auditu z konfigurací virtuálních počítačů s Windows v tématu Zásady auditu systému – Správa účtů

## <a name="change-control-procedures"></a>Změnit řídicí procedury

Integrita všech imagí virtuálních počítačů se neustále zajišťuje protokolováním a vyvoláním výstrahy pro všechny změny provedené v imagí virtuálních počítačů a zpřístupnění podnikovým vlastníkům a/nebo zákazníkům prostřednictvím elektronických metod (například portály nebo výstrahy) výsledky změny nebo přesunu a následného ověření integrity image.

- \[Ukázka\] zobrazit výsledky auditu z konfigurace virtuálních počítačů s Windows v zásadách auditování systému – podrobné sledování

## <a name="inventory-of-assets"></a>Inventář prostředků 

Inventář prostředků a služeb se zachovává.

- Měly by být povolené diagnostické protokoly ve vyhledávacích službách.
- \[Požadavky\] na nasazení verze Preview pro audit konfigurací virtuálních počítačů s Windows v části Možnosti zabezpečení – síťový server Microsoftu
- \[Požadavky\] na nasazení verze Preview pro audit konfigurací virtuálních počítačů s Windows v síti "šablony pro správu-Network"

## <a name="control-of-technical-vulnerabilities"></a>Kontrola technických ohrožení zabezpečení 

Pro všechny systémové a síťové součásti existuje zpřísněný standardní konfigurace.

- Audit Virtual Machines bez nakonfigurovaného zotavení po havárii
- Na spravovaných instancích SQL by mělo být povolené posouzení ohrožení zabezpečení
- Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení.

## <a name="segregation-in-networks"></a>Oddělení v sítích

Brány zabezpečení organizace (např. brány firewall) vynutily zásady zabezpečení a jsou nakonfigurované pro filtrování provozu mezi doménami, blokují neoprávněný přístup a používají se k udržení oddělení mezi interními pevnými, interními bezdrátovými a externími segmenty sítě (např. Internet), včetně zóny DMZ a prosazování zásad řízení přístupu pro jednotlivé domény.

- Automatické zřizování agenta zabezpečení monitoring
- Při vytváření virtuálních sítí nasadit sledovací proces sítě

## <a name="input-data-validation"></a>Ověření vstupních dat

Pro všechny veřejné webové aplikace jsou pro řízení provozu implementovány brány firewall na úrovni aplikace. Pro veřejné aplikace, které nejsou založené na webu, implementuje organizace bránu firewall založenou na síti specifickou pro daný typ aplikace. Pokud je přenos dat do veřejné aplikace zašifrovaný, zařízení buď zastaví za šifrováním, nebo je schopné dešifrovat provoz před analýzou.

- \[Ve\] verzi Preview zobrazit výsledky auditu z konfigurace virtuálních počítačů s Windows ve vlastnostech brány Windows Firewall


## <a name="network-connection-control"></a>Řízení připojení k síti

Síťový provoz se řídí v souladu se zásadami řízení přístupu organizace prostřednictvím brány firewall a dalšími omezeními týkajícími se sítě pro každý přístupový bod sítě nebo spravované rozhraní externí telekomunikační služby.

- Přístup prostřednictvím internetových koncových bodů by měl být omezený.
- Ochrana TDE spravované instance SQL by se měla šifrovat pomocí vlastního klíče.
- Pro aplikace API by mělo být vypnuto vzdálené ladění.

## <a name="network-controls"></a>Ovládací prvky sítě

Organizace používá zabezpečené a šifrované komunikační kanály při migraci fyzických serverů, aplikací nebo dat na virtualizované servery.

- Na virtuálních počítačích by se mělo použít šifrování disku
- Ochrana TDE systému SQL Server by měla být zašifrovaná pomocí vlastního klíče
- \[Ukázka\] zobrazení výsledků auditování z konfigurací virtuálních počítačů s Windows v části Možnosti zabezpečení – přístup k síti
- Auditování neomezeného síťového přístupu k účtům úložiště
- \[Ve\] verzi Preview zobrazit výsledky auditu z konfigurace virtuálních počítačů s Windows ve vlastnostech brány Windows Firewall
- Nasadit nastavení diagnostiky ze skupin zabezpečení sítě
- Přístup prostřednictvím internetových koncových bodů by měl být omezený.

## <a name="sensitive-system-isolation"></a>Citlivá izolace systému

Sdílené systémové prostředky (například Registry, hlavní paměť, sekundární úložiště) se uvolňují zpátky do systému, chráněny před zveřejněním jiným systémům, aplikacím/uživatelům a uživatelé nemůžou úmyslně nebo neúmyslně přistupovat ke zbytkům informací.

- Virtual Machines by se měly migrovat do nových prostředků Azure Resource Manager

## <a name="security-of-network-services"></a>Zabezpečení Network Services

Dohodnuté služby, které poskytuje poskytovatel nebo správce síťové služby, jsou spravovány a monitorovány, aby byly zajištěny jejich bezpečné zabezpečení.

- Virtual Machines by se měly migrovat do nových prostředků Azure Resource Manager

## <a name="network-routing-control"></a>Řízení směrování sítě

Ovládací prvky směrování se implementují prostřednictvím bran zabezpečení (například bran firewall) používaných mezi interními a externími sítěmi (například sítě Internet a třetích stran).

- Na virtuálních počítačích by měly být povolené Adaptivní řízení aplikací.

## <a name="information-exchange-policies-and-procedures"></a>Zásady a postupy výměny informací

Organizace omezuje použití přenosného úložného média řízeného organizací autorizovanými osobami v externích informačních systémech.

- Auditování neomezeného síťového přístupu k účtům úložiště
- Vzdálené ladění by mělo být pro webové aplikace vypnuté.
- Aplikace APi by měla být přístupná jen přes protokol HTTPS

## <a name="electronic-messaging"></a>Elektronické zasílání zpráv

Schválení se získávají před použitím externích veřejných služeb, včetně zasílání rychlých zpráv nebo sdílení souborů.

- \[Náhled\] zobrazuje výsledky auditu z virtuálních počítačů se systémem Linux, které nemají oprávnění k souboru s heslem nastavenou na 0644

## <a name="on-line-transactions"></a>Transakce na řádku

Organizace vyžaduje použití šifrování mezi a používání elektronických podpisů každou ze smluvních stran, které jsou zapojeny do transakce. Organizace zajišťuje, že úložiště podrobností transakcí se nachází mimo všechna veřejně přístupná prostředí (např. na platformě úložiště existující v intranetu organizace) a Nezachované a zpřístupněné na úložném médiu, které je přímo přístupné z Internetu. při použití důvěryhodné autority (např. pro účely vydávání a údržby digitálních podpisů a/nebo digitálních certifikátů) je zabezpečení integrováno a vloženo v celém kompletním procesu správy certifikátů a podpisů.

- Na virtuálních počítačích by se mělo použít šifrování disku
- \[Náhled\] zobrazuje výsledky auditu z virtuálních počítačů s Windows, které neobsahují zadané certifikáty v důvěryhodném kořenovém adresáři.

## <a name="password-management"></a>Správa hesel

Hesla se šifrují během přenosu a ukládání na všechny součásti systému.

- \[Zobrazit\] náhled výsledků auditu z virtuálních počítačů s Windows, u kterých není povolené nastavení složitosti hesla

## <a name="user-authentication-for-external-connections"></a>Ověřování uživatelů pro externí připojení

Metody silného ověřování, jako je Multi-Factor, RADIUS nebo Kerberos (pro privilegovaný přístup) a CHAP (pro šifrování přihlašovacích údajů pro metody telefonického připojení), se implementují pro všechna externí připojení k síti organizací.

- V rámci vašeho předplatného by měly být povolené účty s oprávněním k zápisu.
- Řízení přístupu k síti na virtuálních počítačích by se mělo použít jenom v čase

## <a name="user-identification-and-authentication"></a>Identifikace a ověřování uživatelů

Uživatelé, kteří použili privilegované funkce (například Správa systému), při provádění těchto privilegovaných funkcí používají samostatné účty. Metody Multi-Factor Authentication se používají v souladu se zásadami organizace (například pro vzdálený přístup k síti).

- V rámci vašeho předplatného by měly být povolené účty s oprávněním k zápisu.
- Řízení přístupu k síti na virtuálních počítačích by se mělo použít jenom v čase

## <a name="privilege-management"></a>Správa oprávnění

Přístup ke správcovským funkcím a konzolám pro správu pro systémy hostující virtualizované systémy jsou omezené na pracovníky založené na principu minimálního oprávnění a podporovaných prostřednictvím technických ovládacích prvků.

- Řízení přístupu k síti na virtuálních počítačích by se mělo použít jenom v čase
- \[Ve\] službě Kubernetes Services by měla být použita verze Preview Access Control na základě rolí (RBAC).

## <a name="review-of-user-access-rights"></a>Kontrola přístupových práv uživatele

Organizace udržuje dokumentovaný seznam autorizovaných uživatelů informačních prostředků.

- \[Ukázka\] zobrazení výsledků auditování z konfigurací virtuálních počítačů s Windows v části Možnosti zabezpečení – účty

## <a name="remote-diagnostic-and-configuration-port-protection"></a>Ochrana portů pro vzdálenou diagnostiku a konfiguraci

Porty, služby a podobné aplikace nainstalované v počítači nebo v síťových systémech, které nejsou konkrétně vyžadované pro obchodní funkce, jsou zakázané nebo odebrané.

- Porty pro správu by měly být uzavřeny na virtuálních počítačích
- V konfiguraci zabezpečení na virtuálních počítačích musí být opravené ohrožení zabezpečení.

## <a name="audit-logging"></a>Protokolování auditu

Protokoly odesílaných a přijatých zpráv se uchovávají včetně data, času, původu a cíle zprávy, ale ne jejího obsahu. Auditování je vždy k dispozici, když je systém aktivní a sleduje klíčové události, úspěšný/neúspěšný přístup k datům, změny konfigurace zabezpečení systému, privilegovaného nebo utility použití, všechna vyvolaná alarmy, aktivace a deaktivace systémů ochrany (např. a/V a ID), aktivaci a deaktivaci ověřovacích mechanismů a vytváření a mazání objektů na úrovni systému.

- Měly by být povolené diagnostické protokoly v centru událostí.
- Musí být nainstalované aktualizace systému ve virtuálních počítačích Virtual Machine Scale Sets.

## <a name="monitoring-system-use"></a>Monitorování využití systému

Automatizované systémy nasazené v prostředí organizace se používají ke sledování klíčových událostí a aktivit neobvyklé a k analýze systémových protokolů. výsledky se pravidelně kontrolují. Monitorování zahrnuje privilegované operace, autorizovaný přístup nebo neoprávněné pokusy o přístup, včetně pokusů o přístup k deaktivovaným účtům a upozornění systému nebo selhání.

- Měly by být povolené diagnostické protokoly v sadách škálování virtuálních počítačů.

## <a name="segregation-of-duties"></a>Oddělení povinností

Oddělení cel slouží k omezení rizika neoprávněné nebo neúmyslné úpravy informací a systémů. Žádná jediná osoba nemůže přistupovat k informačním systémům, měnit je ani používat, aniž by bylo možné provádět autorizaci nebo detekci. Přístup jednotlivců zodpovědný za správu a řízení přístupu je omezený na minimum nezbytné na základě role a odpovědností jednotlivých uživatelů a tito jednotlivci nemají přístup k funkcím auditu, které souvisejí s těmito ovládacími prvky.

- V rámci vašeho předplatného by měly být povolené účty s oprávněním k zápisu.

## <a name="administrator-and-operator-logs"></a>Protokoly správců a operátorů

Organizace zajišťuje správné protokolování, aby mohl auditovat činnosti správců. a pravidelně kontroluje správce a obsluha systému.

- V rámci vašeho předplatného by měly být povolené účty s oprávněním k zápisu.

## <a name="identification-of-risks-related-to-external-parties"></a>Identifikace rizik souvisejících s externími stranami

Připojení vzdáleného přístupu mezi organizací a externími stranami jsou zašifrovaná.

- Na virtuálních počítačích by se mělo použít šifrování disku

## <a name="business-continuity-and-risk-assessment"></a>Provozní kontinuita a hodnocení rizik

Organizace identifikuje své důležité obchodní procesy a integruje požadavky na správu zabezpečení informací pro provozní kontinuitu s dalšími požadavky na kontinuitu, které se týkají takových aspektů, jako jsou provoz, personální oddělení, materiály, doprava a vybavení.

- \[Ve\] verzi Preview zobrazit výsledky auditu z konfigurace virtuálních počítačů s Windows v části Možnosti zabezpečení – konzola pro zotavení

> [!NOTE]
> Dostupnost konkrétních definic Azure Policy se může v Azure Government a dalších národních cloudech lišit. 

## <a name="next-steps"></a>Další kroky

Zkontrolovali jste mapování ovládacího prvku ukázky HIPAA HITRUST details. Další informace o přehledu a způsobu nasazení této ukázky najdete v následujících článcích:

> [!div class="next step action"]
> [HIPAA HiTRUST Details-Overview](./control-mapping.md)
> [HIPAA HiTRUST detailed – kroky nasazení](./deploy.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Další informace o [životním cyklu podrobného plánu](../../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../../how-to/update-existing-assignments.md)
