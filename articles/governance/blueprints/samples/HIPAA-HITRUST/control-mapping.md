---
title: Ukázkové ovládací prvky HIPAA HITRUST details
description: Mapování ovládacího prvku pro ukázky HIPAA HITRUST details Každý ovládací prvek je namapován na jednu nebo více zásad Azure, které pomáhají s posouzením.
ms.date: 08/03/2020
ms.topic: sample
ms.openlocfilehash: 10b771e3cfb18a28bd720332a26e13bb1d1f6022
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2020
ms.locfileid: "88209423"
---
# <a name="control-mapping-of-the-hipaa-hitrust-blueprint-sample"></a>Mapování ovládacího prvku Sample HIPAA HITRUST details

Následující článek podrobně popisuje, jak se vzor Azure modrotisky HIPAA HITRUST Details mapuje na ovládací prvky HIPAA HITRUST. Další informace o ovládacích prvcích najdete v tématu [HIPAA HiTRUST](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

Následující mapování jsou k ovládacím prvkům **HIPAA HiTRUST** . Pomocí navigace na pravé straně můžete přejít přímo k určitému mapování ovládacího prvku. Mnohé z mapovaných ovládacích prvků jsou implementovány s [Azure Policy](../../../policy/overview.md) iniciativou. Chcete-li si projít kompletní iniciativu, otevřete **zásadu** v Azure Portal a vyberte stránku **definice** . Pak vyhledejte a vyberte ve ** \[ verzi Preview \] : audit HIPAA HiTRUST ovládá** integrovanou iniciativu zásad.

> [!IMPORTANT]
> Každý ovládací prvek níže je přidružen k jedné nebo více definicím [Azure Policy](../../../policy/overview.md) . Tyto zásady vám pomůžou [zhodnotit dodržování předpisů](../../../policy/how-to/get-compliance-data.md) pomocí ovládacího prvku. často však není 1:1 nebo Úplná shoda mezi ovládacím prvkem a jednou nebo více zásadami. V takovém případě **vyhovuje** v Azure Policy pouze zásadám, které jsou samotné. Tím se nezajistí, že budete plně kompatibilní se všemi požadavky ovládacího prvku. Standard kompatibility zahrnuje i ovládací prvky, které nejsou v tuto chvíli řešeny žádnými definicemi Azure Policy. Proto je dodržování předpisů v Azure Policy jenom částečný pohled na celkový stav dodržování předpisů. Přidružení mezi ovládacími prvky a definicemi Azure Policy pro tuto ukázku podrobného plánu dodržování předpisů se mohou v průběhu času měnit. Historii změn si můžete prohlédnout v [historii potvrzení GitHubu](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/HIPAA-HITRUST/control-mapping.md).

## <a name="control-against-malicious-code"></a>Řízení proti škodlivému kódu

Tento podrobný plán vám pomůže spravovat ochranu koncových bodů včetně ochrany škodlivých kódů tím, že přiřazuje definice [Azure Policy](../../../policy/overview.md) , které monitorují chybějící Endpoint Protection na virtuálních počítačích v Azure Security Center a vynutila řešení Microsoft Antimalware na virtuálních počítačích s Windows.

- Microsoft Antimalware pro Azure by měl být nakonfigurovaný tak, aby automaticky aktualizoval podpisy ochrany.
- Monitorovat chybějící Endpoint Protection v Azure Security Center
- Řešení ochrany koncových bodů by se mělo nainstalovat na Virtual Machine Scale Sets.
- Na virtuálních počítačích by měly být povolené Adaptivní řízení aplikací.


## <a name="management-of-removable-media"></a>Správa vyměnitelného média

Organizace na základě úrovně klasifikace dat registruje média (včetně přenosných počítačů) před použitím, omezuje přiměřená omezení způsobu použití těchto médií a poskytuje odpovídající úroveň fyzické a logické ochrany (včetně šifrování) pro média obsahující zahrnuté informace, dokud není správně zničena nebo upravena.

- Je třeba povolit transparentní šifrování dat databází SQL.
- Na virtuálních počítačích by se mělo použít šifrování disku
- Nepřipojené disky by měly být zašifrované
- Vyžadovat šifrování u Data Lake Store účtů
- Ochrana TDE systému SQL Server by měla být zašifrovaná pomocí vlastního klíče
- Ochrana TDE spravované instance SQL by se měla šifrovat pomocí vlastního klíče.

## <a name="control-of-operational-software"></a>Řízení provozního softwaru 

Organizace identifikuje neoprávněný software v informačním systému, včetně serverů, pracovních stanic a přenosných počítačů, vykonává zásady pro povolení všech výjimek, které zakazují spouštění známého neoprávněného softwaru v informačním systému, a pravidelně kontroluje a aktualizuje seznam neautorizovaného softwaru, ale ne méně než jednou ročně.

- Ohrožení zabezpečení v konfiguraci zabezpečení na vašich počítačích by mělo být opraveno
- Ohrožení zabezpečení v konfiguraci zabezpečení kontejneru by mělo být opraveno
- V konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů by se měly napravit ohrožení zabezpečení.
- Na virtuálních počítačích by měly být povolené Adaptivní řízení aplikací.

## <a name="change-control-procedures"></a>Změnit řídicí procedury

Integrita všech imagí virtuálních počítačů se neustále zajišťuje protokolováním a vyvoláním výstrahy pro všechny změny provedené v imagí virtuálních počítačů a zpřístupnění podnikovým vlastníkům a/nebo zákazníkům prostřednictvím elektronických metod (například portály nebo výstrahy) výsledky změny nebo přesunu a následného ověření integrity image.

- \[Ukázka \] Zobrazit výsledky auditu z konfigurace virtuálních počítačů s Windows v zásadách auditování systému – podrobné sledování
- \[Preview \] : Zobrazit výsledky auditu z konfigurací virtuálních počítačů s Windows v zásadách auditu systému – podrobné sledování

## <a name="control-of-technical-vulnerabilities"></a>Kontrola technických ohrožení zabezpečení 

Pro všechny systémové a síťové součásti existuje zpřísněný standardní konfigurace.

- Na vašich serverech SQL by mělo být povolené posouzení ohrožení zabezpečení
- Na spravovaných instancích SQL by mělo být povolené posouzení ohrožení zabezpečení
- \[\]Posouzení ohrožení zabezpečení ve verzi Preview by mělo být povolené na Virtual Machines
- Ohrožení zabezpečení by se mělo opravit řešením posouzení ohrožení zabezpečení.
- Ohrožení zabezpečení v konfiguraci zabezpečení na vašich počítačích by mělo být opraveno
- V konfiguraci zabezpečení v rámci sady škálování virtuálních počítačů by se měly napravit ohrožení zabezpečení.
- Ohrožení zabezpečení v konfiguraci zabezpečení kontejneru by mělo být opraveno
- Ohrožení zabezpečení vašich databází SQL by mělo být opraveno
- \[Preview \] : v Kubernetes službách by se měly definovat zásady zabezpečení pod.

## <a name="segregation-in-networks"></a>Oddělení v sítích

Brány zabezpečení organizace (např. brány firewall) vynutily zásady zabezpečení a jsou nakonfigurované pro filtrování provozu mezi doménami, blokují neoprávněný přístup a používají se k udržení oddělení mezi interními pevnými, interními bezdrátovými a externími segmenty sítě (např. Internet), včetně zóny DMZ a prosazování zásad řízení přístupu pro jednotlivé domény.

- Podsítě by měly být přidružené ke skupině zabezpečení sítě.
- Virtuální počítače by měly být připojené ke schválené virtuální síti
- Virtuální počítače by měly být přidružené ke skupině zabezpečení sítě.
- Service Bus by měl používat koncový bod služby virtuální sítě
- App Service by měl používat koncový bod služby virtuální sítě
- SQL Server by měl používat koncový bod služby virtuální sítě
- Centrum událostí by mělo používat koncový bod služby virtuální sítě.
- Cosmos DB by měl používat koncový bod služby virtuální sítě
- Key Vault by měl používat koncový bod služby virtuální sítě
- Podsítě brány by se neměly konfigurovat se skupinou zabezpečení sítě.
- Účty úložiště by měly používat koncový bod služby virtuální sítě.
- \[Verze Preview \] : Container Registry by měl používat koncový bod služby virtuální sítě
- Doporučení adaptivního posílení zabezpečení sítě by se měla použít na interních virtuálních počítačích.

## <a name="network-connection-control"></a>Řízení připojení k síti

Síťový provoz se řídí v souladu se zásadami řízení přístupu organizace prostřednictvím brány firewall a dalšími omezeními týkajícími se sítě pro každý přístupový bod sítě nebo spravované rozhraní externí telekomunikační služby.

- Měl by se povolit zabezpečený přenos do účtů úložiště
- Ve vašem přístupovém bodu API by se měla použít nejnovější verze TLS.
- Ve vaší webové aplikaci by se měla použít nejnovější verze TLS.
- V Function App by se měla použít nejnovější verze TLS.
- Function App by měl být přístupný jenom přes HTTPS
- Webová aplikace by měla být přístupná jen přes protokol HTTPS
- Aplikace API by měla být přístupná jen přes protokol HTTPS
- Pro databázové servery MySQL by mělo být povoleno připojení SSL vynutilo.
- Pro databázové servery PostgreSQL by mělo být povoleno připojení SSL vynutilo.
- Měla by být povolená jenom zabezpečená připojení k vašemu Redis Cache.
- Podsítě by měly být přidružené ke skupině zabezpečení sítě.
- Pravidla skupin zabezpečení sítě pro webové aplikace v IaaS by se měla posílit.
- Pravidla skupiny zabezpečení sítě pro virtuální počítače s přístupem k Internetu by měla být zesílená.
- Virtuální počítače by měly být připojené ke schválené virtuální síti
- Virtuální počítače by měly být přidružené ke skupině zabezpečení sítě.

## <a name="network-controls"></a>Ovládací prvky sítě

Organizace používá zabezpečené a šifrované komunikační kanály při migraci fyzických serverů, aplikací nebo dat na virtualizované servery.

- Na virtuálních počítačích by se mělo používat řízení přístupu k síti podle potřeby
- Doporučení adaptivního posílení zabezpečení sítě by se měla použít na interních virtuálních počítačích.
- Service Bus by měl používat koncový bod služby virtuální sítě
- App Service by měl používat koncový bod služby virtuální sítě
- SQL Server by měl používat koncový bod služby virtuální sítě
- Centrum událostí by mělo používat koncový bod služby virtuální sítě.
- Cosmos DB by měl používat koncový bod služby virtuální sítě
- Key Vault by měl používat koncový bod služby virtuální sítě
- Auditování neomezeného síťového přístupu k účtům úložiště
- Účty úložiště by měly používat koncový bod služby virtuální sítě.
- \[Verze Preview \] : Container Registry by měl používat koncový bod služby virtuální sítě

## <a name="security-of-network-services"></a>Zabezpečení Network Services

Dohodnuté služby, které poskytuje poskytovatel nebo správce síťové služby, jsou spravovány a monitorovány, aby byly zajištěny jejich bezpečné zabezpečení.

- \[Preview \] : na virtuálních počítačích s Windows by měl být nainstalovaný Agent pro shromažďování dat pro síťové přenosy.
- \[Verze Preview \] : Agent shromažďování dat pro síťové přenosy by měl být nainstalovaný na virtuálních počítačích se systémem Linux Network Watcher by měl být povolený.

## <a name="information-exchange-policies-and-procedures"></a>Zásady a postupy výměny informací

Organizace omezuje použití přenosného úložného média řízeného organizací autorizovanými osobami v externích informačních systémech.

- Zajistěte, aby webová aplikace měla klientské certifikáty (příchozí klientské certifikáty) nastavené na zapnuto.
- CORS by neměl umožňovat každému prostředku přístup k vašim webovým aplikacím
- CORS by neměl umožňovat každému prostředku přístup k aplikacím funkcí
- CORS by neměl umožňovat každému prostředku přístup k vaší aplikaci API.
- Vzdálené ladění by mělo být pro webové aplikace vypnuté.
- Pro aplikace Function app by mělo být vypnuto vzdálené ladění.
- Vzdálené ladění by mělo být pro API Apps vypnuté.

## <a name="on-line-transactions"></a>Transakce na řádku

Organizace vyžaduje použití šifrování mezi a používání elektronických podpisů každou ze smluvních stran, které jsou zapojeny do transakce. Organizace zajišťuje, že úložiště podrobností transakcí se nachází mimo všechna veřejně přístupná prostředí (např. na platformě úložiště existující v intranetu organizace) a Nezachované a zpřístupněné na úložném médiu, které je přímo přístupné z Internetu. při použití důvěryhodné autority (např. pro účely vydávání a údržby digitálních podpisů a/nebo digitálních certifikátů) je zabezpečení integrováno a vloženo v celém kompletním procesu správy certifikátů a podpisů.

- Měl by se povolit zabezpečený přenos do účtů úložiště
- Ve vašem přístupovém bodu API by se měla použít nejnovější verze TLS.
- Ve vaší webové aplikaci by se měla použít nejnovější verze TLS.
- V Function App by se měla použít nejnovější verze TLS.
- Function App by měl být přístupný jenom přes HTTPS
- Webová aplikace by měla být přístupná jen přes protokol HTTPS
- Aplikace API by měla být přístupná jen přes protokol HTTPS
- Pro databázové servery MySQL by mělo být povoleno připojení SSL vynutilo.
- Pro databázové servery PostgreSQL by mělo být povoleno připojení SSL vynutilo.
- Měla by být povolená jenom zabezpečená připojení k vašemu Redis Cache.
- \[Verze Preview \] : nasaďte požadavky na auditování virtuálních počítačů s Windows, které neobsahují zadané certifikáty v důvěryhodném kořenovém adresáři.
- \[Preview \] : Zobrazit výsledky auditu z virtuálních počítačů s Windows, které neobsahují zadané certifikáty v důvěryhodném kořenovém adresáři

## <a name="user-password-management"></a>Správa hesel uživatelů

Hesla se šifrují během přenosu a ukládání na všechny součásti systému.

- \[Náhled \] auditu virtuálních počítačů s nezabezpečeným nastavením zabezpečení hesla

## <a name="user-authentication-for-external-connections"></a>Ověřování uživatelů pro externí připojení

Metody silného ověřování, jako je Multi-Factor, RADIUS nebo Kerberos (pro privilegovaný přístup) a CHAP (pro šifrování přihlašovacích údajů pro metody telefonického připojení), se implementují pro všechna externí připojení k síti organizací.

- Pro účty s oprávněním vlastníka pro vaše předplatné by se měla povolit vícefaktorové ověřování.
- Pro účty s oprávněním k zápisu do vašeho předplatného by se měla povolit vícefaktorové ověřování.
- Pro účty s oprávněním ke čtení vašeho předplatného by se měla povolit vícefaktorové ověřování.
- Na virtuálních počítačích by se mělo používat řízení přístupu k síti podle potřeby

## <a name="user-identification-and-authentication"></a>Identifikace a ověřování uživatelů

Uživatelé, kteří použili privilegované funkce (například Správa systému), při provádění těchto privilegovaných funkcí používají samostatné účty. Metody Multi-Factor Authentication se používají v souladu se zásadami organizace (například pro vzdálený přístup k síti).

- Pro účty s oprávněním vlastníka pro vaše předplatné by se měla povolit vícefaktorové ověřování.
- Pro účty s oprávněním k zápisu do vašeho předplatného by se měla povolit vícefaktorové ověřování.
- Pro účty s oprávněním ke čtení vašeho předplatného by se měla povolit vícefaktorové ověřování.
- Pro vaše předplatné by se mělo určit maximálně 3 vlastníci.
- K vašemu předplatnému by měl být přiřazený víc než jeden vlastník.
- Nasaďte požadavky na auditování virtuálních počítačů s Windows, ve kterých skupina Administrators obsahuje některé ze zadaných členů.
- Zobrazit výsledky auditu z virtuálních počítačů s Windows, ve kterých skupina Administrators obsahuje některé ze zadaných členů
- Nasaďte požadavky na auditování virtuálních počítačů s Windows, ve kterých skupina Administrators neobsahuje všechny zadané členy.
- Zobrazit výsledky auditu z virtuálních počítačů s Windows, ve kterých skupina Administrators neobsahuje všechny zadané členy
- Nasaďte požadavky na auditování virtuálních počítačů s Windows, ve kterých skupina Administrators neobsahuje jenom zadané členy.
- Zobrazí výsledky auditu z virtuálních počítačů s Windows, ve kterých skupina Administrators neobsahuje jenom určité členy.

## <a name="privilege-management"></a>Správa oprávnění

Přístup ke správcovským funkcím a konzolám pro správu pro systémy hostující virtualizované systémy jsou omezené na pracovníky založené na principu minimálního oprávnění a podporovaných prostřednictvím technických ovládacích prvků.

- Na virtuálních počítačích by se mělo používat řízení přístupu k síti podle potřeby
- Porty pro správu by měly být uzavřeny na virtuálních počítačích
- Pro vaše předplatné by se mělo určit maximálně 3 vlastníci.
- K vašemu předplatnému by měl být přiřazený víc než jeden vlastník.
- Z vašeho předplatného byste měli odebrat externí účty s oprávněním vlastníka.
- Zastaralé účty s oprávněním vlastníka by se měly odebrat z vašeho předplatného.
- Auditovat využití vlastních pravidel RBAC
- Pro služby Kubernetes by se měla použít Access Control na základě rolí (RBAC).

## <a name="review-of-user-access-rights"></a>Kontrola přístupových práv uživatele

Organizace udržuje dokumentovaný seznam autorizovaných uživatelů informačních prostředků.

- Auditovat využití vlastních pravidel RBAC

## <a name="remote-diagnostic-and-configuration-port-protection"></a>Ochrana portů pro vzdálenou diagnostiku a konfiguraci

Porty, služby a podobné aplikace nainstalované v počítači nebo v síťových systémech, které nejsou konkrétně vyžadované pro obchodní funkce, jsou zakázané nebo odebrané.

- Na virtuálních počítačích by se mělo používat řízení přístupu k síti podle potřeby
- Porty pro správu by měly být uzavřeny na virtuálních počítačích
- Vzdálené ladění by mělo být pro webové aplikace vypnuté.
- Pro aplikace Function app by mělo být vypnuto vzdálené ladění.
- Vzdálené ladění by mělo být pro API Apps vypnuté.
- Na virtuálních počítačích by měly být povolené Adaptivní řízení aplikací.

## <a name="audit-logging"></a>Protokolování auditu

Protokoly odesílaných a přijatých zpráv se uchovávají včetně data, času, původu a cíle zprávy, ale ne jejího obsahu. Auditování je vždy k dispozici, když je systém aktivní a sleduje klíčové události, úspěšný/neúspěšný přístup k datům, změny konfigurace zabezpečení systému, privilegovaného nebo utility použití, všechna vyvolaná alarmy, aktivace a deaktivace systémů ochrany (např. a/V a ID), aktivaci a deaktivaci ověřovacích mechanismů a vytváření a mazání objektů na úrovni systému.

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
- Azure Monitor by měly shromažďovat protokoly aktivit ze všech oblastí

## <a name="monitoring-system-use"></a>Monitorování využití systému

Automatizované systémy nasazené v prostředí organizace se používají ke sledování klíčových událostí a aktivit neobvyklé a k analýze systémových protokolů. výsledky se pravidelně kontrolují. Monitorování zahrnuje privilegované operace, autorizovaný přístup nebo neoprávněné pokusy o přístup, včetně pokusů o přístup k deaktivovaným účtům a upozornění systému nebo selhání.

- Azure Monitor by měly shromažďovat protokoly aktivit ze všech oblastí
- Agent Log Analytics by měl být nainstalovaný na virtuálních počítačích
- Agent Log Analytics by měl být nainstalovaný na Virtual Machine Scale Sets
- \[Verze Preview \] : nasazení požadavků pro audit virtuálních počítačů s Windows, na kterých je agent Log Analytics nepřipojený podle očekávání
- \[Preview \] : Zobrazit výsledky auditu z virtuálních počítačů s Windows, na kterých je agent Log Analytics nepřipojený podle očekávání
- Profil protokolu Azure Monitor by měl shromažďovat protokoly pro kategorie Write, DELETE a Action.
- V předplatném by mělo být povolené Automatické zřizování agenta monitorování Log Analytics.

## <a name="segregation-of-duties"></a>Oddělení povinností

Oddělení cel slouží k omezení rizika neoprávněné nebo neúmyslné úpravy informací a systémů. Žádná jediná osoba nemůže přistupovat k informačním systémům, měnit je ani používat, aniž by bylo možné provádět autorizaci nebo detekci. Přístup jednotlivců zodpovědný za správu a řízení přístupu je omezený na minimum nezbytné na základě role a odpovědností jednotlivých uživatelů a tito jednotlivci nemají přístup k funkcím auditu, které souvisejí s těmito ovládacími prvky.

- Pro služby Kubernetes by se měla použít Access Control na základě rolí (RBAC).
- Auditovat využití vlastních pravidel RBAC
- \[Verze Preview \] : nasazení požadavků pro audit konfigurací virtuálních počítačů s Windows v přiřazení uživatelských práv
- \[Preview \] : zobrazení výsledků auditu z konfigurací virtuálních počítačů s Windows v přiřazení uživatelských práv
- \[Verze Preview \] : nasazení požadavků pro audit konfigurací virtuálních počítačů s Windows v možnosti zabezpečení – řízení uživatelských účtů
- \[Preview \] : Zobrazit výsledky auditu z konfigurací virtuálních počítačů s Windows v možnosti zabezpečení – řízení uživatelských účtů
- Vlastní role vlastníka předplatného by neměly existovat.

## <a name="administrator-and-operator-logs"></a>Protokoly správců a operátorů

Organizace zajišťuje správné protokolování, aby mohl auditovat činnosti správců. a pravidelně kontroluje správce a obsluha systému.

- Pro konkrétní operace správy by měla existovat výstraha protokolu aktivit.

## <a name="identification-of-risks-related-to-external-parties"></a>Identifikace rizik souvisejících s externími stranami

Připojení vzdáleného přístupu mezi organizací a externími stranami jsou zašifrovaná.

- Měl by se povolit zabezpečený přenos do účtů úložiště
- Function App by měl být přístupný jenom přes HTTPS
- Webová aplikace by měla být přístupná jen přes protokol HTTPS
- Aplikace API by měla být přístupná jen přes protokol HTTPS
- Pro databázové servery MySQL by mělo být povoleno připojení SSL vynutilo.
- Pro databázové servery PostgreSQL by mělo být povoleno připojení SSL vynutilo.
- Měla by být povolená jenom zabezpečená připojení k vašemu Redis Cache.

## <a name="business-continuity-and-risk-assessment"></a>Provozní kontinuita a hodnocení rizik

Organizace identifikuje své důležité obchodní procesy a integruje požadavky na správu zabezpečení informací pro provozní kontinuitu s dalšími požadavky na kontinuitu, které se týkají takových aspektů, jako jsou provoz, personální oddělení, materiály, doprava a vybavení.

- Auditovat virtuální počítače bez nakonfigurovaného zotavení po havárii
- Key Vault objekty by měly být obnovitelné
- \[Verze Preview \] : nasazení požadavků pro audit konfigurací virtuálních počítačů s Windows v možnosti zabezpečení – konzola pro zotavení
- \[Ve verzi Preview \] Zobrazit výsledky auditu z konfigurace virtuálních počítačů s Windows v části Možnosti zabezpečení – konzola pro zotavení

## <a name="back-up"></a>Zálohování

Tento podrobný plán přiřadí Azure Policy definice, které v elektronické podobě auditují informace o zálohování systému v organizaci na alternativním webu úložiště. Pro fyzickou expedici metadat úložiště zvažte použití Azure Data Box.

- Pro databáze SQL Azure by mělo být povolené dlouhodobé geograficky redundantní zálohování.
- Pro Azure Database for MySQL by měla být povolená geograficky redundantní záloha.
- Pro Azure Database for PostgreSQL by měla být povolená geograficky redundantní záloha.
- Pro Azure Database for MariaDB by měla být povolená geograficky redundantní záloha.
- Azure Backup by měla být povolená Virtual Machines

> [!NOTE]
> Dostupnost konkrétních definic Azure Policy se může v Azure Government a dalších národních cloudech lišit. 

## <a name="next-steps"></a>Další kroky

Zkontrolovali jste mapování ovládacího prvku ukázky HIPAA HITRUST details. Další informace o přehledu a způsobu nasazení této ukázky najdete v následujících článcích:

> [!div class="next step action"]
> [HIPAA HiTRUST Details – přehled](./control-mapping.md) 
>  [HIPAA HiTRUST Details – postup nasazení](./deploy.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Další informace o [životním cyklu podrobného plánu](../../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../../how-to/update-existing-assignments.md)
