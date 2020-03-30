---
title: HIPAA HITRUST vzorové ovládací prvky
description: Mapování řízení vzorků plánů HIPAA HITRUST. Každý ovládací prvek je mapován na jednu nebo více zásad Azure, které pomáhají s hodnocením.
ms.date: 11/26/2019
ms.topic: sample
ms.openlocfilehash: c6680055cfe41a2f893d46cba797783913d880ce
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75472307"
---
# <a name="control-mapping-of-the-hipaa-hitrust-blueprint-sample"></a>Mapování řízení ukázky plánu HIPAA HITRUST

V následujícím článku podrobně popisuje, jak Azure Blueprints HIPAA HITRUST podrobný plán ukázka mapuje hipaa HITRUST ovládací prvky. Další informace o ovládacích prvcích naleznete v tématu [HIPAA HITRUST](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

Následující mapování jsou **hipaa HITRUST** ovládací prvky. Pomocí navigace vpravo přejděte přímo na konkrétní mapování ovládacího prvku. Mnoho mapovaných ovládacích prvků se implementuje pomocí iniciativy [Zásad azure.](../../../policy/overview.md) Pokud chcete zkontrolovat celou iniciativu, otevřete **zásady** na webu Azure portal a vyberte stránku **Definice.** Potom vyhledejte a vyberte ** \[náhled\]: Audit hipaa HITRUST řídí** integrovanou iniciativu zásad.

> [!IMPORTANT]
> Každý ovládací prvek níže je přidružen k jedné nebo více definic [zásad Azure.](../../../policy/overview.md) Tyto zásady vám mohou pomoci [posoudit dodržování](../../../policy/how-to/get-compliance-data.md) ovládacího prvku; však často není 1:1 nebo úplnou shodu mezi ovládacím prvkem a jednu nebo více zásad. Jako **takový, kompatibilní** v zásadách Azure odkazuje pouze na zásady samotné; Tím nezajistíte, že jste plně kompatibilní se všemi požadavky ovládacího prvku. Kromě toho standard dodržování předpisů zahrnuje ovládací prvky, které nejsou v tuto chvíli adresovány žádnými definicemi zásad Azure. Dodržování předpisů v zásadách Azure je tedy pouze částečným zobrazením celkového stavu dodržování předpisů. Přidružení mezi ovládacími prvky a definicemi zásad Azure pro tento ukázkový plán dodržování předpisů se může v průběhu času měnit. Chcete-li zobrazit historii změn, podívejte se na [historii potvrzení GitHubu](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/HIPAA-HITRUST/control-mapping.md).

## <a name="control-against-malicious-code"></a>Řízení proti škodlivému kódu

Tento podrobný plán vám pomůže spravovat ochranu koncových bodů, včetně ochrany škodlivého kódu, přiřazením definic [zásad Azure,](../../../policy/overview.md) které monitorují chybějící ochranu koncových bodů na virtuálních počítačích v Azure Security Center a vynucují antimalwarové řešení Microsoftu na virtuálních počítačích s Windows.

- Nasazení výchozího rozšíření Microsoft IaaS Antimalware pro systém Windows Server
- Diagnostické protokoly v dávkových účtech by měly být povoleny.
- Na počítače by se měly nainstalovat aktualizace systému


## <a name="management-of-removable-media"></a>Správa vyměnitelných médií

Organizace na základě úrovně klasifikace dat registruje média (včetně přenosných počítačů) před použitím, přiměřeně omezuje způsob použití těchto médií a poskytuje odpovídající úroveň fyzické a logické ochrany (včetně šifrování) pro obsahující chod zahrnutých informací, dokud nebudou řádně zničeny nebo dezinfikovány.

- Vyžadovat šifrování na účtech Data Lake Store
- Ochrana TDE spravované SQL by měla být šifrována pomocí vlastního klíče
- Šifrování disku by mělo být použito na virtuálních počítačích.
- Transparentní šifrování dat v databázích SQL by mělo být povoleno


## <a name="information-exchange-policies-and-procedures"></a>Zásady a postupy výměny informací

Poskytovatelé cloudových služeb používají průmyslově uznávanou virtualizační platformu a standardní formáty virtualizace (např. formát Open Virtualization Format, OVF), aby zajistili interoperabilitu, a zdokumentovali vlastní změny provedené v jakémkoli hypervisoru a všech virtualizační háčky specifické pro řešení, které jsou k dispozici pro kontrolu zákazníků.

- Nasazení požadavků pro auditování virtuálních aplikací systému Windows, které nemají nainstalované zadané aplikace

## <a name="control-of-operational-software"></a>Řízení provozního softwaru 

Organizace identifikuje neoprávněný software v informačním systému, včetně serverů, pracovních stanic a notebooků, používá zásady povolit vše, odepřít podle výjimky, aby zakázala spuštění známého neautorizovaného softwaru v informačním systému, a pravidelně kontroluje a aktualizuje seznam neautorizovaného softwaru, avšak nejméně jednou ročně.

- \[Preview\] Zobrazit výsledky auditu z konfigurací virtuálních počítačů se systémem Windows v části Možnosti zabezpečení–audit
- \[Preview\] Zobrazit výsledky auditu z konfigurací virtuálních počítačů se systémem Windows v části Zásady auditu systému – správa účtů

## <a name="change-control-procedures"></a>Postupy řízení změn

Integrita všech bitových kopií virtuálních strojů je vždy zajištěna protokolováním a upozorněním na jakékoli změny provedené v iblakci virtuálních strojů a zpřístupněním vlastníkům a/nebo zákazníkům prostřednictvím elektronických metod (např. portálů nebo výstrah) výsledků změny nebo přesunutí a následného ověření integrity obrázku.

- \[Preview\] Zobrazit výsledky auditu z konfigurace virtuálních počítačů systému Windows v části Zásady auditu systému –podrobné sledování

## <a name="inventory-of-assets"></a>Soupis aktiv 

Soupis aktiv a služeb je veden

- Diagnostické protokoly ve vyhledávacích službách by měly být povoleny.
- \[Preview\] Požadavky na nasazení pro audit konfigurace virtuálních počítačů se systémem Windows v části Možnosti zabezpečení – microsoft síťový server
- \[Preview\] Požadavky na nasazení pro audit konfigurace virtuálních počítačů se systémem Windows v části Šablony pro správu – síť

## <a name="control-of-technical-vulnerabilities"></a>Kontrola technických zranitelností 

Pro všechny součásti systému a sítě existuje konhardenovaný konfigurační standard.

- Auditovat virtuální počítače bez nakonfigurovaného zotavení po havárii
- Posouzení ohrožení zabezpečení by mělo být povoleno ve vašich instancích spravovaných SQL.
- Chyba zabezpečení by měla být opravena řešením pro posouzení zranitelnosti

## <a name="segregation-in-networks"></a>Segregace v sítích

Brány zabezpečení organizace (např. brány firewall) vynucují zásady zabezpečení a jsou nakonfigurovány tak, aby filtrovaly provoz mezi doménami, blokovaly neoprávněný přístup a používají se k udržování segregace mezi interním kabelovým, interním bezdrátovým a externím (např. internet) včetně DMZ a vynucovat zásady řízení přístupu pro každou z domén.

- Automatické zřizování agenta monitorování zabezpečení
- Nasazení sledovacího procesu sítě při vytváření virtuálních sítí

## <a name="input-data-validation"></a>Ověření vstupních dat

Pro všechny veřejné webové aplikace jsou pro řízení provozu implementovány brány firewall na úrovni aplikace. Pro veřejné aplikace, které nejsou založeny na webu, organizace implementovala síťovou bránu firewall specifickou pro typ aplikace. Pokud je provoz na veřejnou aplikaci šifrován, zařízení buď sedí za šifrováním, nebo je schopno dešifrovat provoz před analýzou.

- \[Náhled\] Zobrazit výsledky auditu z konfigurace virtuálních počítačů se systémem Windows v části Vlastnosti brány Windows Firewall


## <a name="network-connection-control"></a>Řízení síťového připojení

Síťový provoz je řízen v souladu se zásadami řízení přístupu organizace prostřednictvím brány firewall a dalších omezení souvisejících se sítí pro každý přístupový bod sítě nebo spravované rozhraní externí telekomunikační služby.

- Přístup přes koncové body směřující k Internetu by měl být omezen
- Ochrana TDE spravované SQL by měla být šifrována pomocí vlastního klíče
- Vzdálené ladění by mělo být vypnuto pro aplikace rozhraní API.

## <a name="network-controls"></a>Ovládací prvky sítě

Organizace používá zabezpečené a šifrované komunikační kanály při migraci fyzických serverů, aplikací nebo dat na virtualizované servery.

- Šifrování disku by se mělo použít na virtuálních počítačích.
- Ochrana TDE serveru SQL server by měla být šifrována pomocí vlastního klíče
- \[Náhled\] Zobrazit výsledky auditů z konfigurací virtuálních počítačů se systémem Windows v části Možnosti zabezpečení – přístup k síti
- Auditovat neomezený přístup k účtům úložiště v síti
- \[Náhled\] Zobrazit výsledky auditů z konfigurace virtuálních počítačů se systémem Windows v části Vlastnosti brány Windows Firewall
- Nasazení nastavení diagnostiky ze skupin zabezpečení sítě
- Přístup přes koncové body směřující k Internetu by měl být omezen

## <a name="sensitive-system-isolation"></a>Citlivá izolace systému

Sdílené systémové prostředky (např. registry, hlavní paměť, sekundární úložiště) jsou uvolněny zpět do systému, chráněny před zveřejněním jiným systémům/aplikacím/uživatelům, a uživatelé nemohou úmyslně nebo neúmyslně přistupovat ke zbytkům informací.

- Virtuální počítače by se měly migrovat do nových prostředků Azure Resource Manageru.

## <a name="security-of-network-services"></a>Zabezpečení síťových služeb

Dohodnuté služby poskytované poskytovatelem/správcem síťových služeb jsou formálně spravovány a monitorovány, aby bylo zajištěno jejich bezpečné poskytování.

- Virtuální počítače by se měly migrovat do nových prostředků Azure Resource Manageru.

## <a name="network-routing-control"></a>Řízení směrování sítě

Ovládací prvky směrování jsou implementovány prostřednictvím bezpečnostních bran (např. firewallů) používaných mezi interními a externími sítěmi (např. sítěmi Internetu a třetích stran).

- Adaptivní ovládací prvky aplikací by měly být povoleny na virtuálních počítačích

## <a name="information-exchange-policies-and-procedures"></a>Zásady a postupy výměny informací

Organizace omezuje používání přenosných paměťových médií řízených organizací oprávněnými osobami v externích informačních systémech.

- Auditovat neomezený přístup k účtům úložiště v síti
- Vzdálené ladění by mělo být vypnuto pro webové aplikace.
- Aplikace APi by měla být přístupná pouze přes protokol HTTPS

## <a name="electronic-messaging"></a>Elektronické zasílání zpráv

Schválení jsou získána před použitím externích veřejných služeb, včetně zasílání rychlých zpráv nebo sdílení souborů.

- \[Preview\] Zobrazit výsledky auditu z virtuálních počítačů s Linuxem, které nemají oprávnění k souboru hesel nastavená na 0644

## <a name="on-line-transactions"></a>On-line transakce

Organizace vyžaduje použití šifrování mezi a použití elektronických podpisů každou ze stran zapojených do transakce. Organizace zajišťuje, aby byly údaje o transakci umístěny mimo všechna veřejně přístupná prostředí (např. na platformě úložiště existující na intranetu organizace) a nebyly uchovávány a vystaveny na paměťovém médiu přímo přístupném z Internetu.Pokud se používá důvěryhodný orgán (např. pro účely vydávání a údržby digitálních podpisů a/nebo digitálních certifikátů), je zabezpečení integrováno a integrováno do celého koncového/certifikačního úřadu Proces.

- Šifrování disku by se mělo použít na virtuálních počítačích.
- \[Náhled\] Zobrazit výsledky auditu z virtuálních aplikací windows, které neobsahují zadané certifikáty v důvěryhodném kořenovém adresáři

## <a name="password-management"></a>Správa hesel

Hesla jsou během přenosu a ukládání šifrována na všech systémových součástech.

- \[Náhled\] Zobrazit výsledky auditu z virtuálních počítače windows, u kterých není povoleno nastavení složitosti hesla

## <a name="user-authentication-for-external-connections"></a>Ověřování uživatelů pro externí připojení

Pro všechna externí připojení k síti organizací jsou implementovány silné metody ověřování, jako je vícefaktorový, Radius nebo Kerberos (pro privilegovaný přístup) a protokol CHAP (pro šifrování pověření pro metody telefonického připojení).

- MFA by měly mít povolené účty s oprávněními k zápisu ve vašem předplatném.
- Just In Time řízení přístupu k síti by měla být použita na virtuálních počítačích

## <a name="user-identification-and-authentication"></a>Identifikace a ověření uživatele

Uživatelé, kteří vykonávali privilegované funkce (např. správa systému), používají při provádění těchto privilegovaných funkcí samostatné účty. Vícefaktorové metody ověřování se používají v souladu s organizačními zásadami (např. pro vzdálený přístup k síti).

- MFA by měly mít povolené účty s oprávněními k zápisu ve vašem předplatném.
- Just In Time řízení přístupu k síti by měla být použita na virtuálních počítačích

## <a name="privilege-management"></a>Správa oprávnění

Přístup k funkcím správy nebo k administrativním konzolím pro systémy hostující virtualizované systémy je omezen na pracovníky na základě principu nejnižších oprávnění a je podporován technickými kontrolami.

- Just In Time řízení přístupu k síti by měla být použita na virtuálních počítačích
- \[Ve\] službách Kubernetes by měl být použit náhled řízení přístupu na základě role (RBAC).

## <a name="review-of-user-access-rights"></a>Kontrola přístupových práv uživatelů

Organizace vede zdokumentovaný seznam oprávněných uživatelů informačních prostředků.

- \[Náhled\] Zobrazit výsledky auditů z konfigurací virtuálních počítačů se systémem Windows v části Možnosti zabezpečení – účty

## <a name="remote-diagnostic-and-configuration-port-protection"></a>Ochrana vzdálené diagnostiky a konfiguračního portu

Porty, služby a podobné aplikace nainstalované v počítači nebo síťových systémech, které nejsou výslovně vyžadovány pro obchodní funkce, jsou zakázány nebo odebrány.

- Porty pro správu by měly být uzavřeny na virtuálních počítačích
- Chyby zabezpečení v konfiguraci zabezpečení ve škálovacích sadách virtuálních počítačů by měly být opraveny.

## <a name="audit-logging"></a>Protokolování auditu

Protokoly odeslaných a přijatých zpráv jsou udržovány včetně data, času, původu a cíle zprávy, nikoli však jejího obsahu. Auditování je vždy k dispozici, když je systém aktivní a sleduje klíčové události, úspěšný/neúspěšný přístup k datům, změny konfigurace zabezpečení systému, privilegované nebo užitkové použití, všechny alarmy, aktivaci a deaktivaci ochranných systémů (např. a/V a IDS), aktivaci a deaktivaci identifikačních a ověřovacích mechanismů a vytváření a mazání objektů na úrovni systému.

- Diagnostické protokoly v centru událostí by měly být povoleny
- Aktualizace systému na škálovacísady virtuálních strojů by měly být nainstalovány

## <a name="monitoring-system-use"></a>Využití monitorovacího systému

Automatizované systémy nasazené v celém prostředí organizace se používají ke sledování klíčových událostí a anomální aktivity a k analýze systémových protokolů, jejichž výsledky jsou pravidelně kontrolovány. Monitorování zahrnuje privilegované operace, oprávněný přístup nebo pokusy o neoprávněný přístup, včetně pokusů o přístup k deaktivovaným účtům a systémových výstrah nebo selhání.

- Diagnostické protokoly ve škálovacích sadách virtuálních strojů by měly být povoleny

## <a name="segregation-of-duties"></a>Oddělení povinností

Oddělení povinností se používá k omezení rizika neoprávněné nebo neúmyslné změny informací a systémů. Žádná osoba nemá přístup k informačním systémům, upravovat je ani používat bez autorizace nebo detekce. Přístup pro osoby odpovědné za správu a kontrolu přístupu je omezen na nezbytné minimum na základě úlohy a odpovědnosti každého uživatele a tito jednotlivci nemají přístup k funkcím auditu souvisejícím s těmito kontrolami.

- MFA by měly mít povolené účty s oprávněními k zápisu ve vašem předplatném.

## <a name="administrator-and-operator-logs"></a>Protokoly správce a operátora

Organizace zajišťuje správné protokolování je povolena za účelem auditu činnosti správce; a pravidelně přezkoumává protokoly správce systému a operátora.

- MFA by měly mít povolené účty s oprávněními k zápisu ve vašem předplatném.

## <a name="identification-of-risks-related-to-external-parties"></a>Identifikace rizik souvisejících s externími stranami

Připojení vzdáleného přístupu mezi organizací a externími stranami jsou šifrována

- Šifrování disku by mělo být použito ve virtuálních počítačích.

## <a name="business-continuity-and-risk-assessment"></a>Kontinuita podnikání a hodnocení rizik

Organizace identifikuje své kritické obchodní procesy a integruje požadavky na řízení bezpečnosti informací týkající se kontinuity provozu s dalšími požadavky na kontinuitu týkajícími se takových aspektů, jako jsou operace, personální obsazení, materiály, doprava a zařízení.

- \[Náhled\] Zobrazit výsledky auditu z konfigurace virtuálních počítačů se systémem Windows v části Možnosti zabezpečení – konzola pro zotavení

> [!NOTE]
> Dostupnost konkrétních definic zásad Azure se může lišit v Azure Government a dalších národních cloudech. 

## <a name="next-steps"></a>Další kroky

Zkontrolovali jste mapování ovládacích prvku ukázky plánu HIPAA HITRUST. Dále navštivte následující články, kde se dozvíte o přehledu a o tom, jak tuto ukázku nasadit:

> [!div class="next step action"]
> [Plán HIPAA HITRUST – přehled](./control-mapping.md)
> [plánu HIPAA HITRUST – nasazení kroků](./deploy.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Přečtěte si o [životním cyklu podrobného plánu](../../concepts/lifecycle.md).
- Pochopit, jak používat [statické a dynamické parametry](../../concepts/parameters.md).
- Naučte se přizpůsobit [pořadí sekvencování podrobných plánů](../../concepts/sequencing-order.md).
- Zjistěte, jak využít [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md).
- Přečtěte si, jak [aktualizovat existující přiřazení](../../how-to/update-existing-assignments.md).
