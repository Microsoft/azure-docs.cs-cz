---
title: Správa certifikátů v clusteru Service Fabric
description: Přečtěte si o správě certifikátů v clusteru Service Fabric zabezpečených pomocí certifikátů X. 509.
ms.topic: conceptual
ms.date: 04/10/2020
ms.custom: sfrev
ms.openlocfilehash: a8a7e8954f3c9d5b54c2e1ed9caa330ef92d4512
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/10/2021
ms.locfileid: "100099502"
---
# <a name="certificate-management-in-service-fabric-clusters"></a>Správa certifikátů v Service Fabric clusterech

Tento článek se věnuje aspektům správy certifikátů, které se používají k zabezpečení komunikace v clusterech Azure Service Fabric, a doplňuje Úvod do [Service Fabric zabezpečení clusteru](service-fabric-cluster-security.md) a také vysvětlení pro [ověřování na základě certifikátu X. 509 v Service Fabric](cluster-security-certificates.md). Předpokládáme, že čtenář je obeznámen se základními koncepcemi zabezpečení a také s ovládacími prvky, které Service Fabric zveřejňují ke konfiguraci zabezpečení clusteru.  

Aspekty, na které se vztahuje tato hlava:

* Co přesně je Správa certifikátů?
* Role a entity zapojené do správy certifikátů
* Cesta certifikátu
* Podrobně na příklad
* Řešení problémů a nejčastější dotazy

Ale první právní omezení: Tento článek se snaží spárovat svou teoretickou stranu s praktickými příklady, které vyžadují, aby byly dobře, konkrétní služby, technologie atd. Vzhledem k tomu, že výraznouá součást cílové skupiny je interní Microsoftem, budeme odkazovat na služby, technologie a produkty, které jsou specifické pro Microsoft Azure. V části s poznámkami se můžete zeptat na vysvětlení nebo pokyny, kde se v tomto případě nevztahují podrobnosti specifické pro společnost Microsoft.

## <a name="defining-certificate-management"></a>Definování správy certifikátů
Jak jsme viděli v [doprovodném článku](cluster-security-certificates.md), je certifikát kryptografickým objektem, který v podstatě váže dvojici asymetrických klíčů s atributy, které popisují entitu, kterou představuje. Je však také objekt s možnou zkáze v tom, že jeho životnost je konečná a je náchylná k navázání náhodného zpřístupnění nebo úspěšnému zneužití může z hlediska zabezpečení vykreslovat nevýhodný certifikát. To znamená nutnost měnit certifikáty – ať už rutiny, nebo v reakci na incident zabezpečení. Další aspekt správy (a jedná se o své vlastní téma) je zabezpečení privátních klíčů certifikátu nebo tajných klíčů, které chrání identity subjektů zapojených do vyřazování a zřizování certifikátů. Na postupy a postupy, které se používají k získání certifikátů a k bezpečnému (a bezpečnému) přenosu, které jsou potřeba k tomu, aby je bylo možné používat jako správu certifikátů, se říkáme. Některé operace správy, jako je registrace, nastavení zásad a autorizační ovládací prvky, jsou nad rámec tohoto článku. I ostatní – například zřizování, obnovování, opětovné vytvoření klíče nebo odvolání – pouze incidenty týkající se Service Fabric; Přesto je budeme řešit na určitou míru, protože porozumění těmto operacím pomůžete zajistit správné zabezpečení clusteru. 

Cílem je co nejvíc automatizovat správu certifikátů, abyste zajistili nepřerušovanou dostupnost clusteru a nabízeli zabezpečení, a to s ohledem na to, že proces je bez dotykového ovládání. Tento cíl je nyní dosažitelný v clusterech Azure Service Fabric; zbývající část článku bude nejprve dekonstruovat správu certifikátů a později se zaměří na povolení autorollover.

Konkrétně jsou témata v oboru:
  - předpoklady týkající se oddělení přiřazení mezi vlastníkem a platformou v kontextu správy certifikátů
  - dlouhý kanál certifikátů od vystavení do spotřeby
  - otočení certifikátu – proč, jak a kdy
  - Co by mohlo být chybné?

Tento článek popisuje, jako je například zabezpečení/Správa názvů domén, Registrace certifikátů nebo nastavení autorizačních ovládacích prvků k vystavování certifikátů. Podívejte se na registrační autoritu vaší oblíbené služby infrastruktura veřejných klíčů (PKI). Interní spotřebitelé Microsoftu: Přečtěte si prosím zabezpečení Azure.

## <a name="roles-and-entities-involved-in-certificate-management"></a>Role a entity zapojené do správy certifikátů
Přístup k zabezpečení ve Service Fabricm clusteru je případ, kdy ho vlastník clusteru deklaruje, Service Fabric runtime ho vynutil. To znamená, že téměř žádná z certifikátů, klíčů nebo jiných přihlašovacích údajů, které se účastní provozu clusteru, pochází z samotné služby. Všechny jsou deklarovány vlastníkem clusteru. Kromě toho vlastník clusteru také zodpovídá za zřízení certifikátů do clusteru, jejich obnovení podle potřeby a zajištění zabezpečení certifikátů. Konkrétně vlastník clusteru musí zajistit, aby:
  - certifikáty deklarované v oddílu NodeType manifestu clusteru najdete na každém uzlu tohoto typu podle [pravidel prezentace](cluster-security-certificates.md#presentation-rules) .
  - certifikáty deklarované výše jsou nainstalované včetně odpovídajících privátních klíčů.
  - certifikáty deklarované v pravidlech prezentace by měly projít [ověřovacími pravidly](cluster-security-certificates.md#validation-rules) . 

Service Fabric v rámci své součásti předpokládá zodpovědnost za:
  - Vyhledání a vyhledání certifikátů, které odpovídají deklaracím v definici clusteru  
  - udělení přístupu k odpovídajícím privátním klíčům Service Fabric entitám řízeným pro potřebu
  - ověřování certifikátů v přísném souladu se zavedenými osvědčenými postupy zabezpečení a definicí clusteru
  - zvyšování výstrahy na hrozící vypršení platnosti certifikátů nebo selhání při provádění základních kroků při ověřování certifikátu
  - ověřování (do určité míry), že aspekty definice clusteru související s certifikátem jsou splněné základní konfigurací hostitelů 

Postup správy certifikátů (jako aktivní operace) spadá výhradně na vlastníka clusteru. V následujících částech se podrobněji podíváme na jednotlivé operace správy, a to s dostupnými mechanismy a jejich dopadem na cluster.

## <a name="the-journey-of-a-certificate"></a>Cesta certifikátu
Pojďme rychle znovu navštívit průběh certifikátu od vystavení v kontextu Service Fabric clusteru:

  1. Vlastník domény se registruje s registrační autoritou infrastruktury veřejných klíčů a doménou nebo subjektem, které by chtěli přidružit k následným certifikátům; certifikáty pak budou sloužit k důkazu vlastnictví dané domény nebo subjektu.
  2. Vlastník domény také určí v RA identity autorizovaných žadatelů – entity, které mají nárok na žádost o registraci certifikátů v zadané doméně nebo předmětu; v Microsoft Azure je výchozí zprostředkovatel identit Azure Active Directory a autorizované žadatelé jsou označeny odpovídající identitou AAD (nebo prostřednictvím skupin zabezpečení).
  3. Autorizovaný žadatel pak zaregistruje certifikát přes službu pro správu tajných klíčů. v Microsoft Azure je server SMS pro výběr Azure Key Vault (integrace), který bezpečně ukládá a umožňuje načítání tajných klíčů a certifikátů podle autorizovaných entit. INTEGRACE také obnoví a znovu zaregistruje certifikát, jak je nakonfigurovaný v přidružených zásadách certifikátu. (Integrace používá AAD jako zprostředkovatele identity.)
  4. Autorizovaný spuštění metody Retriever – na který odkazujeme jako na zřizovací agent – načte certifikát (včetně jeho privátního klíče) z trezoru a nainstaluje ho do počítačů hostujících cluster.
  5. Služba Service Fabric (se spuštěnými zvýšenými oprávněními na jednotlivých uzlech) uděluje přístup k certifikátu pro povolené Service Fabric entity. Tyto jsou určené místními skupinami a rozdělené mezi ServiceFabricAdministrators a ServiceFabricAllowedUsers.
  6. Modul runtime Service Fabric přistupuje k certifikátu a používá ho k navázání federace nebo k ověřování příchozích požadavků od autorizovaných klientů.
  7. Zřizovací agent monitoruje certifikát trezoru a aktivuje tok zřizování při zjišťování obnovení; následně vlastník clusteru v případě potřeby aktualizuje definici clusteru, aby označoval záměr přenášet certifikát.
  8. Agent zřizování nebo vlastník clusteru zodpovídá taky za čištění a odstraňování nepoužívaných certifikátů.
  
Pro naše účely jsou první dva kroky v pořadí výše nesouvisející; jediným připojením je, že běžný název certifikátu je název DNS deklarovaný v definici clusteru.

Tyto kroky jsou popsané níže. Všimněte si rozdílů mezi zřizováním certifikátů deklarovaných pomocí kryptografického otisku a společného názvu v uvedeném pořadí.

*Obrázek 1.* Postup vystavování a zřizování certifikátů deklarovaných pomocí kryptografického otisku
![Zřizování certifikátů deklarovaných pomocí kryptografického otisku][Image1]

*Obrázek. 2.* Postup vystavování a zřizování certifikátů deklarovaných běžným názvem subjektu
![Zřizování certifikátů deklarované běžným názvem subjektu][Image2]

### <a name="certificate-enrollment"></a>Zápis certifikátu
Toto téma je podrobně popsáno v [dokumentaci](../key-vault/certificates/create-certificate.md)Key Vault. pro zajištění kontinuity a snazšího odkazování obsahujeme stručný přehled. V rámci Azure jako kontextu a používání Azure Key Vault jako služby pro správu tajných kódů musí mít autorizovaný žadatel o certifikát aspoň oprávnění pro správu certifikátů v trezoru, kterou udělí vlastník trezoru. Žadatel se pak zaregistruje do certifikátu následujícím způsobem:
    - vytvoří zásadu certifikátu v Azure Key Vault (integrace), která určuje doménu/předmět certifikátu, požadovaný Vystavitel, typ a délku klíče, zamýšlené použití klíče a další informace. Podrobnosti najdete [v tématu certifikáty v Azure Key Vault](../key-vault/certificates/certificate-scenarios.md) . 
    - vytvoří certifikát ve stejném trezoru se zásadami uvedenými výše. Tím se pak vygeneruje dvojice klíčů jako objekty trezoru, žádost o podepsání certifikátu podepsaná privátním klíčem, která se pak předává určenému vydavateli k podepsání.
    - po odpovědi vystavitele (certifikační autorita) k podepsanému certifikátu se výsledek sloučí do trezoru a certifikát je k dispozici pro následující operace:
      - v části {vaultUri}/Certificates/{Name}: certifikát, včetně veřejného klíče a metadat
      - v části {vaultUri}/Keys/{Name}: privátní klíč certifikátu je dostupný pro kryptografické operace (zabalení a rozbalení, přihlášení a ověření).
      - v části {vaultUri}/Secrets/{Name}: certifikát, včetně jeho privátního klíče, který je k dispozici pro stažení jako nechráněný soubor PFX nebo PEM.  
    Odvolání certifikátu trezoru je ve skutečnosti chronologický řádek instancí certifikátů, který sdílí zásady. Verze certifikátu se vytvoří podle atributů životnosti a obnovení zásady. Důrazně doporučujeme, aby certifikáty trezoru nesdílely předměty nebo domény/názvy DNS; v clusteru může být rušivé rušit, aby se zřídily instance certifikátů z různých certifikátů trezoru se stejnými předměty, ale s podstatně odlišnými atributy, jako je Vystavitel, použití klíčů atd.

V tomto okamžiku existuje certifikát v trezoru, který je připravený pro použití. Kromě:

### <a name="certificate-provisioning"></a>Zřizování certifikátů
Uvedli jsme "zřizovací agent", který je entita, která načte certifikát, včetně jeho privátního klíče, z trezoru a nainstaluje ho do každého hostitele clusteru. (Odvolání tohoto Service Fabric nezřizuje certifikáty.) V našem kontextu se cluster bude hostovat na kolekci virtuálních počítačů Azure a/nebo virtuálních počítačů s měřítkem. V Azure se dá zřízení certifikátu z trezoru na virtuální počítač nebo VMSS dosáhnout pomocí následujících mechanismů – za předpokladu, že agent zřizování předtím udělil oprávnění Get k trezoru vlastníkem trezoru: 
  - ad-hoc: operátor načte certifikát z trezoru (jako PFX/PKCS #12 nebo PEM) a nainstaluje ho do každého uzlu.
  - jako klíčová sada pro škálování virtuálního počítače "tajné" při nasazení: služba COMPUTE načítá, používá ke své první straně identitu prvního subjektu, certifikát z trezoru s povoleným nasazením šablony a nainstaluje ho do každého uzlu sady škálování virtuálních počítačů ([např](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#certificates).). Všimněte si, že to umožňuje zřizování pouze tajných klíčů.
  - pomocí [rozšíření virtuálního počítače Key Vault](../virtual-machines/extensions/key-vault-windows.md); To umožňuje zřizování certifikátů pomocí deklarací bez verzí s pravidelným obnovením pozorovaných certifikátů. V takovém případě se očekává, že virtuální počítač/VMSS bude mít [spravovanou identitu](../virtual-machines/security-policy.md#managed-identities-for-azure-resources), což byl udělen přístup k trezorům, které obsahují zjištěné certifikáty.

Mechanismus ad-hoc se nedoporučuje z několika důvodů od zabezpečení po dostupnost a nezabývá se tady. Podrobnosti najdete [v tématu certifikáty ve Virtual Machine Scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#certificates).

Zřizování VMSS-/COMPUTE-based představuje výhody zabezpečení a dostupnosti, ale také nabízí omezení. Vyžaduje – návrh – deklarace certifikátů jako tajných klíčů, která je vhodná jenom pro clustery zabezpečené pomocí certifikátů deklarovaných pomocí kryptografického otisku. Naproti tomu Key Vault zřizování na základě rozšíření virtuálních počítačů vždycky nainstaluje nejnovější verzi každého zjištěného certifikátu, který je vhodný jenom pro clustery zabezpečené pomocí certifikátů deklarovaných běžným názvem subjektu. Pro zdůraznění nepoužívejte mechanizmus zřizování AutoRefresh (například rozšíření KVVM) pro certifikáty deklarované instancí (to znamená podle kryptografického otisku) – riziko ztráty dostupnosti je značné.

Mohou existovat jiné mechanismy zřizování; výše uvedené jsou aktuálně přijímány pro clustery Azure Service Fabric.

### <a name="certificate-consumption-and-monitoring"></a>Spotřeba a monitorování certifikátů
Jak už bylo zmíněno dříve, Service Fabric runtime zodpovídá za lokalizaci a používání certifikátů deklarovaných v definici clusteru. Článek o [ověřování na základě certifikátů](cluster-security-certificates.md) podrobně vysvětluje, jak Service Fabric implementuje pravidla pro prezentace a ověřování, v uvedeném pořadí a nebude znovu navštěvovat. Podíváme se na přístup a udělení oprávnění a taky na monitorování.

Odvolání certifikátů v Service Fabric se používají pro velké množství účelů, od vzájemného ověřování ve federační vrstvě až po ověřování TLS pro koncové body správy. k tomu je potřeba, aby různé komponenty nebo systémové služby měly přístup k privátnímu klíči certifikátu. Modul runtime Service Fabric pravidelně kontroluje úložiště certifikátů a hledá shody pro všechna známá pravidla prezentace; pro každý z odpovídajících certifikátů je umístěný odpovídající privátní klíč a jeho volitelný seznam řízení přístupu se aktualizuje tak, aby zahrnoval oprávnění – obvykle se pro čtení a spouštění uděluje příslušné identitě, která je vyžaduje. (Tento proces se neformálně označuje jako ' funkce acling '.) Proces se spouští na 1 minutě tempo a také pokrývá certifikáty aplikace, jako jsou například ty, které se používají k zašifrování nastavení nebo jako certifikáty koncového bodu. Funkce acling se řídí prezentačními pravidly, takže je důležité pamatovat na to, že certifikáty deklarované kryptografickým otiskem a které se autoaktualizovat bez aktualizace konfigurace v clusteru nebudou přístupné.    

### <a name="certificate-rotation"></a>Rotace certifikátu
Jako Poznámka na straně: IETF [RFC 3647](https://tools.ietf.org/html/rfc3647) formálně definuje [obnovení](https://tools.ietf.org/html/rfc3647#section-4.4.6) jako vystavení certifikátu se stejnými atributy jako Vystavitel certifikátu, veřejný klíč a informace subjektu se zachovají a [znovu](https://tools.ietf.org/html/rfc3647#section-4.4.7) vytvoří klíč jako vystavení certifikátu s novou dvojicí klíčů a bez omezení, jestli se může Vystavitel změnit. Vzhledem k tomu, že rozlišení může být důležité (Zvažte případ certifikátů deklarovaných běžným názvem s připnutím vystavitele), požádáme o neutrální výraz "rotace", aby se pokryly oba scénáře. (Pamatujte na to, že pokud se ve skutečnosti používá označení "obnovení", bude se jednat o opětovné vytvoření klíče.) 

Dříve jsme se dozvěděli, že Azure Key Vault podporuje automatické otočení certifikátu: zásady přidružování certifikátů definují bod v čase, který je nastavený na základě dnů před vypršením platnosti nebo procentem celkové životnosti, když se certifikát v trezoru otočí. Agent zřizování musí být vyvolán po tomto bodu v čase a před vypršením platnosti certifikátu (nyní předchozí) pro distribuci tohoto nového certifikátu do všech uzlů clusteru. Service Fabric bude pomáhat vygenerováním upozornění na stav, když se datum vypršení platnosti certifikátu (a který je aktuálně používá v clusteru) vyskytne dřív než předem určený interval. Automatický agent zřizování (tj. rozšíření virtuálního počítače trezoru klíčů) nakonfigurovaný tak, aby sledoval certifikát trezoru, se pravidelně dotazuje trezoru, rozpozná rotaci a načte a nainstaluje nový certifikát. Zřizování přes virtuální počítač nebo VMSS – funkce tajné klíče bude vyžadovat, aby autorizovaný operátor aktualizoval virtuální počítač nebo VMSS s identifikátorem URI trezoru klíčů, který odpovídá novému certifikátu.

V obou případech je otočený certifikát teď zřízený všem uzlům a my jsme popsali mechanismus, který Service Fabric využívá ke zjišťování rotací. Podíváme se na to, co se stane dál – za předpokladu, že se rotace používá pro certifikát clusteru deklarovaný běžným názvem subjektu
  - u nových připojení v rámci nástroje i v clusteru Service Fabric modul runtime vyhledá a vybere naposledy vydaný certifikát, který odpovídá (největší hodnota vlastnosti ' NotBefore '). Všimněte si, že se jedná o změnu z předchozích verzí modulu runtime Service Fabric.
  - stávající připojení se budou udržovat aktivní/smí přirozeně vypršet platnost nebo jinak skončit; interní obslužná rutina bude upozorněna, že existuje nová shoda.

> [!NOTE] 
> Před verzí 7.2.445 (7,2 CU4) Service Fabric vybrat certifikát s nejstarším vypršením platnosti (certifikát s vlastností "NotAfter").

To se týká následujících důležitých pozorování:
  - Certifikát pro obnovení je možné ignorovat, pokud datum vypršení platnosti vychází z aktuálně používaného certifikátu.
  - Dostupnost clusteru nebo hostovaných aplikací má přednost před direktivou pro otočení certifikátu. cluster se nakonec konverguje na nový certifikát, ale bez záruk časování. Postupuje takto:
  - Nemusí být okamžitě zřejmé, že otočený certifikát úplně nahradil svůj předchůdce; jediný způsob, jak zajistit, aby se počítače hostitele restartovaly (pro certifikáty clusteru). Poznámka: není dostačující pro restartování Service Fabricch uzlů, protože komponenty režimu jádra, které tvoří zapůjčení připojení v clusteru, nebudou ovlivněny. Také Upozorňujeme, že restartování virtuálního počítače nebo VMSS může způsobit dočasnou ztrátu dostupnosti. (Pro certifikáty aplikací stačí restartovat pouze příslušné instance aplikace.)
  - Zavedení certifikátu opakovaného, který nesplňuje pravidla ověřování, může způsobit, že cluster nebude efektivně poškodit. Nejběžnějším příkladem je to, že se jedná o neočekávaného vystavitele: certifikáty clusteru jsou deklarované běžným názvem subjektu s připnutím vystavitele, ale otočený certifikát byl vydán novým/nedeklarovaným vystavitelem.     

### <a name="certificate-cleanup"></a>Vyčištění certifikátu
V tuto chvíli nejsou v Azure žádná ustanovení pro explicitní odebrání certifikátů. Často se jedná o netriviální úlohu, která určuje, jestli se daný certifikát v daný okamžik používá nebo není. To je obtížnější pro certifikáty aplikací než pro certifikáty clusteru. Service Fabric sám, nejedná se o agenta zřizování, neodstraní certifikát deklarovaný uživatelem za žádných okolností. Pro standardní mechanismy zřizování:
  - Certifikáty deklarované jako tajné kódy VM/VMSS budou zřízené tak dlouho, jak jsou odkazovány v definici VM/VMSS, a je možné je získat z trezoru (odstranění tajného klíče nebo certifikátu z trezoru způsobí selhání nasazení virtuálních počítačů/VMSS, podobně jako při zakázání tajné verze v trezoru dojde také k selhání nasazení virtuálního počítače/VMSS, který odkazuje na tuto tajnou verzi).
  - Předchozí verze certifikátů zřízené prostřednictvím rozšíření virtuálního počítače trezoru klíčů může nebo nemusí být k dispozici na uzlu VM/VMSS. Agent načte a nainstaluje jenom aktuální verzi a neodebere žádné certifikáty. Obnova kopie uzlu (ke kterému obvykle dochází každý měsíc) obnoví úložiště certifikátů na obsah image operačního systému, takže předchozí verze budou implicitně odebrány. Vezměte v úvahu, že při škálování sady škálování virtuálního počítače bude výsledkem jenom aktuální verze pozorovaných certifikátů, které se instalují. Nepoužívejte stejnorodost uzlů s ohledem na nainstalované certifikáty.   

## <a name="simplifying-management---an-autorollover-example"></a>Zjednodušení správy – příklad automatického přechodu
Popsali jsme mechanismy, omezení, popsaná komplikovaná pravidla a definice a udělali se nepříjemné předpovědiy výpadků. V takovém případě je třeba čas ukázat, jak nastavit automatickou správu certifikátů, aby se předešlo všem těmto obavám. V souvislosti s clusterem Azure Service Fabric, který běží na PaaSv2 sadě virtuálních počítačů s virtuálním počítačem s, používáme Azure Key Vault ke správě tajných klíčů a využití spravovaných identit takto:
  - Ověření certifikátů se změnilo z otisku – připnutí k předmětu + připnutí vystavitele: každý certifikát s daným subjektem z daného vystavitele je stejně důvěryhodný.
    - Certifikáty se registrují do a získávají z důvěryhodného úložiště (Key Vault) a aktualizují ho agent – v tomto případě se jedná o rozšíření virtuálního počítače trezoru klíčů.
    - Zřizování certifikátů se mění z doby nasazení a podle verze (jak je to ComputeRP) pro následné nasazení a používání identifikátorů URI trezoru klíčů bez verzí.
    - Přístup k trezoru klíčů se uděluje prostřednictvím spravovaných identit přiřazených uživatelem; Identita UA se vytvoří a přiřadí do sady škálování virtuálního počítače během nasazování.
    - Po nasazení bude agent (rozšíření pro virtuální počítače KV) dotazovat a aktualizovat pozorované certifikáty v každém uzlu sady škálování virtuálních počítačů. rotace certifikátu je tedy plně automatizovaná, protože SF automaticky vybere nejvýšený platný certifikát.

Sekvence je plně skriptovatelných nebo automatizovaná a umožňuje počáteční nasazení clusteru bez dotykového ovládání, které je nakonfigurované pro automatické přecházení k certifikátům. Podrobné pokyny jsou uvedené níže. Budeme používat kombinaci rutin PowerShellu a fragmentů šablon JSON. Stejné funkce jsou dosažitelné se všemi podporovanými prostředky pro interakci s Azure.

> [!NOTE]
> Tento příklad předpokládá, že už existuje certifikát v trezoru; zápis a obnovení certifikátu spravovaného trezorem klíčů vyžaduje ruční postup, jak je popsáno výše v tomto článku. V produkčních prostředích používejte certifikáty spravované trezorem klíčů – níže je uvedený ukázkový skript specifický pro interní infrastrukturu veřejných klíčů společnosti Microsoft.

> [!NOTE]
> Automatické přecházení certifikátu dává smysl jenom pro certifikáty vydané certifikační autoritou. pomocí certifikátů podepsaných svým držitelem, včetně těch, které se vygenerovaly při nasazení clusteru Service Fabric v Azure Portal, je nesmyslná, ale u místních nebo vývojářských nasazení je to možné, a to deklarováním kryptografického otisku vystavitele, který bude stejný jako na listovém certifikátu.

### <a name="starting-point"></a>Výchozí bod
V případě zkrácení budeme předpokládat následující počáteční stav:
  - Cluster Service Fabric existuje a je zabezpečený pomocí certifikátu vydaného certifikační autoritou deklarovaného kryptografickým otiskem.
  - Certifikát je uložený v trezoru a zřízený jako tajný klíč sady virtuálních počítačů s měřítkem.
  - Stejný certifikát se použije k převedení clusteru na běžné deklarace certifikátů založeného na názvech, a proto je možné ho ověřit podle předmětu a vystavitele. Pokud tomu tak není, Získejte certifikát vydaný certifikační autoritou určený k tomuto účelu a přidejte ho do definice clusteru podle kryptografického otisku, jak je popsáno [zde](service-fabric-cluster-security-update-certs-azure.md) .

Tady je výpis JSON ze šablony odpovídající takovému stavu – Poznámka: vynecháno mnoho požadovaných nastavení a ilustruje pouze aspekty týkající se certifikátů:
```json
  "resources": [
    {   ## VMSS definition
      "apiVersion": "[variables('vmssApiVersion')]",
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "name": "[variables('vmNodeTypeName')]",
      "location": "[variables('computeLocation')]",
      "properties": {
        "virtualMachineProfile": {
          "extensionProfile": {
            "extensions": [
            {
                "name": "[concat('ServiceFabricNodeVmExt','_vmNodeTypeName')]",
                "properties": {
                  "type": "ServiceFabricNode",
                  "autoUpgradeMinorVersion": true,
                  "publisher": "Microsoft.Azure.ServiceFabric",
                  "settings": {
                    "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                    "nodeTypeRef": "[variables('vmNodeTypeName')]",
                    "dataPath": "D:\\SvcFab",
                    "durabilityLevel": "Bronze",
                    "certificate": {
                        "thumbprint": "[parameters('primaryClusterCertificateTP')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                    }
                  },
                  "typeHandlerVersion": "1.1"
                }
            },}},
          "osProfile": {
            "adminPassword": "[parameters('adminPassword')]",
            "adminUsername": "[parameters('adminUsername')]",
            "secrets": [
            {
                "sourceVault": {
                    "id": "[resourceId('Microsoft.KeyVault/vaults', parameters('keyVaultName'))]"
                },
                "vaultCertificates": [
                {
                    "certificateStore": "[parameters('certificateStoreValue')]",
                    "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
                },
            ]}]
        },
    },
    {   ## cluster definition
        "apiVersion": "[variables('sfrpApiVersion')]",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "certificate": {
            "thumbprint": "[parameters('primaryClusterCertificateTP')]",
            "x509StoreName": "[parameters('certificateStoreValue')]"
        },
    }
  ]
```   

V podstatě uvádíme, že certifikát s kryptografickým otiskem ```json [parameters('primaryClusterCertificateTP')] ``` a nalezený v identifikátoru URI trezoru klíčů ```json [parameters('clusterCertificateUrlValue')] ``` je deklarovaný jako jediný certifikát v clusteru, podle kryptografického otisku. Dále nastavíme další prostředky, které jsou potřeba k tomu, aby se zajistila autovýměna certifikátu.

### <a name="setting-up-prerequisite-resources"></a>Nastavení požadovaných prostředků
Jak už bylo zmíněno dříve, certifikát zřízený jako tajný klíč sady virtuálních počítačů se načte z trezoru pomocí služby poskytovatele prostředků Microsoft. COMPUTE, pomocí své první identity a jménem operátoru nasazení. U automatické výměny změn, které se změní – my se přepneme na použití spravované identity přiřazené k sadě škálování virtuálního počítače a kterému se udělí oprávnění tajných kódů trezoru.

Všechny další výňatky by měly být nasazené concomitantly – jsou uvedeny jednotlivě pro analýzu a vysvětlení přehrávání.

Nejdřív definujte identitu přiřazenou uživatelem (výchozí hodnoty jsou zahrnuté jako příklady) – Podívejte se na [oficiální dokumentaci](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity) , kde najdete informace o aktuálním stavu:
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "userAssignedIdentityName": {
      "type": "string",
      "defaultValue": "sftstuaicus",
      "metadata": {
        "description": "User-assigned managed identity name"
      }
    },
  },
  "variables": {
      "vmssApiVersion": "2018-06-01",
      "sfrpApiVersion": "2018-02-01",
      "miApiVersion": "2018-11-30",
      "kvApiVersion": "2018-02-14",
      "userAssignedIdentityResourceId": "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('userAssignedIdentityName'))]"
  },    
  "resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('userAssignedIdentityName')]",
      "apiVersion": "[variables('miApiVersion')]",
      "location": "[resourceGroup().location]"
    },
  ]}
```

Pak této identitě udělte přístup k tajným tajným klíčům – informace o aktuálních informacích najdete v [oficiální dokumentaci](/rest/api/keyvault/vaults/updateaccesspolicy) :
```json
  "resources":
  [{
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "name": "[concat(parameters('keyVaultName'), '/add')]",
      "apiVersion": "[variables('kvApiVersion')]",
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[reference(variables('userAssignedIdentityResourceId'), variables('miApiVersion')).tenantId]",
            "objectId": "[reference(variables('userAssignedIdentityResourceId'), variables('miApiVersion')).principalId]",
            "dependsOn": [
              "[variables('userAssignedIdentityResourceId')]"
            ],
            "permissions": {
              "secrets": [
                "get",
                "list"
              ]}}]}}]
```

V dalším kroku budeme:
  - Přiřaďte uživateli přiřazenou identitu pro sadu škálování virtuálního počítače.
  - deklarace závislosti sady škálování virtuálního počítače na vytvoření spravované identity a na základě udělení přístupu k trezoru
  - deklarujete rozšíření virtuálního počítače trezoru klíčů, které vyžaduje, aby při spuštění nahlásilo zjištěné certifikáty ([oficiální dokumentace](../virtual-machines/extensions/key-vault-windows.md)).
  - Aktualizujte definici Service Fabric virtuálního počítače tak, aby byla závislá na rozšíření KVVM, a převeďte certifikát clusteru na běžný název (tyto změny provedeme v jednom kroku, protože spadají do oboru stejného prostředku).

```json
  "parameters": {
    "kvvmextPollingInterval": {
      "type": "string",
      "defaultValue": "3600",
      "metadata": {
        "description": "kv vm extension polling interval in seconds"
      }
    },
    "kvvmextLocalStoreName": {
      "type": "string",
      "defaultValue": "MY",
      "metadata": {
        "description": "kv vm extension local store name"
      }
    },
    "kvvmextLocalStoreLocation": {
      "type": "string",
      "defaultValue": "LocalMachine",
      "metadata": {
        "description": "kv vm extension local store location"
      }
    },
    "kvvmextObservedCertificates": {
      "type": "array",
      "defaultValue": [
                "https://sftestcus.vault.azure.net/secrets/sftstcncluster",
                "https://sftestcus.vault.azure.net/secrets/sftstcnserver"
            ],
      "metadata": {
        "description": "kv vm extension observed certificates versionless uri"
      }
    },
    "certificateCommonName": {
      "type": "string",
      "defaultValue": "cus.cluster.sftstcn.system.servicefabric.azure-int",
      "metadata": {
        "description": "Certificate Common name"
      }
    },
  },
  "resources": [
  {
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[variables('vmNodeTypeName')]",
    "location": "[variables('computeLocation')]",
    "dependsOn": [
      "[variables('userAssignedIdentityResourceId')]",
      "[concat('Microsoft.KeyVault/vaults/', concat(parameters('keyVaultName'), '/accessPolicies/add'))]"
    ],
    "identity": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
        "[variables('userAssignedIdentityResourceId')]": {}
      }
    },
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
        {
          "name": "KVVMExtension",
          "properties": {
            "publisher": "Microsoft.Azure.KeyVault",
            "type": "KeyVaultForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "secretsManagementSettings": {
                    "pollingIntervalInS": "[parameters('kvvmextPollingInterval')]",
                    "linkOnRenewal": false,
                    "observedCertificates": "[parameters('kvvmextObservedCertificates')]",
                    "requireInitialSync": true
                }
            }
          }
        },
        {
        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeTypeName')]",
        "properties": {
          "type": "ServiceFabricNode",
          "provisionAfterExtensions" : [ "KVVMExtension" ],
          "publisher": "Microsoft.Azure.ServiceFabric",
          "settings": {
            "certificate": {
                "commonNames": [
                    "[parameters('certificateCommonName')]"
                ],
                "x509StoreName": "[parameters('certificateStoreValue')]"
            }
            },
            "typeHandlerVersion": "1.0"
          }
        },
  ] } ## extension profile
  },  ## VM profile
  "osProfile": {
    "adminPassword": "[parameters('adminPassword')]",
    "adminUsername": "[parameters('adminUsername')]",
  } 
  }
  ]
```
Všimněte si, že není výslovně uvedeno výše, odebrali jsme z oddílu "OsProfile" v sadě škálování virtuálního počítače adresu URL certifikátu trezoru.
Posledním krokem je aktualizace definice clusteru, aby se změnila deklarace certifikátu z kryptografického otisku na běžný název – tady připnete také kryptografické otisky vystavitele:

```json
  "parameters": {
    "certificateCommonName": {
      "type": "string",
      "defaultValue": "cus.cluster.sftstcn.system.servicefabric.azure-int",
      "metadata": {
        "description": "Certificate Common name"
      }
    },
    "certificateIssuerThumbprint": {
      "type": "string",
      "defaultValue": "1b45ec255e0668375043ed5fe78a09ff1655844d,d7fe717b5ff3593764f4d90654d86e8362ec26c8,3ac7c3cac8de0dd392c02789c8be97474f456960,96ea05926e2e42cc207e358668be2c316857fb5e",
      "metadata": {
        "description": "Certificate issuer thumbprints separated by comma"
      }
    },
  },
  "resources": [
    {
      "apiVersion": "[variables('sfrpApiVersion')]",
      "type": "Microsoft.ServiceFabric/clusters",
      "name": "[parameters('clusterName')]",
      "location": "[parameters('clusterLocation')]",
      "properties": {
        "certificateCommonNames": {
          "commonNames": [{
              "certificateCommonName": "[parameters('certificateCommonName')]",
              "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
          }],
          "x509StoreName": "[parameters('certificateStoreValue')]"
        },
  ]
```

V tomto okamžiku můžete spustit výše uvedené aktualizace v jednom nasazení. Služba poskytovatele prostředků Service Fabric v rámci své součásti rozdělí upgrade clusteru v několika krocích, jak je popsáno v segmentu o [převodech certifikátů clusteru z kryptografického otisku na běžný název](cluster-security-certificates.md#converting-a-cluster-from-thumbprint--to-common-name-based-certificate-declarations).

### <a name="analysis-and-observations"></a>Analýza a pozorování
Tato část je určena k objasnění kroků popsaných výše a také k vyčerpání důležitých aspektů.

#### <a name="certificate-provisioning-explained"></a>Zřizování certifikátů, vysvětlení
Rozšíření KVVM jako agent zřizování běží nepřetržitě na předem určené frekvenci. Při neúspěšném načtení zjištěného certifikátu by došlo k dalšímu v řádku a pak přejde do režimu hibernace až do dalšího cyklu. Rozšíření SFVM, jako zaváděcí agent clusteru, bude vyžadovat, aby deklarované certifikáty předtím, než bude moci cluster tvořit. To zase znamená, že rozšíření SFVM může běžet až po úspěšném načtení certifikátů clusteru, označených ```json "provisionAfterExtensions" : [ "KVVMExtension" ]"``` klauzulí a nastavením rozšíření KeyVaultVM ```json "requireInitialSync": true``` . To znamená, že rozšíření KVVM, které se při prvním spuštění (po nasazení nebo restartování), musí probíhat cyklicky prostřednictvím pozorovaných certifikátů, dokud se všechny úspěšně nestáhnou. Nastavení tohoto parametru na hodnotu false, společně s selháním načtení certifikátů clusteru, by způsobilo selhání nasazení clusteru. Naopak požadavek na počáteční synchronizaci s nesprávným/neplatným seznamem pozorovaných certifikátů způsobí selhání rozšíření KVVM, a proto znovu selže nasazení clusteru.  

#### <a name="certificate-linking-explained"></a>Odkazy na certifikát – vysvětlení
Možná jste si všimli příznaku linkOnRenewal rozšíření KVVM a fakt, že je nastavený na false. Tady řešíme chování řízené tímto příznakem a jeho dopad na fungování clusteru. Všimněte si, že toto chování je specifické pro systém Windows.

Podle [definice](../virtual-machines/extensions/key-vault-windows.md#extension-schema):

```json
"linkOnRenewal": <Only Windows. This feature enables auto-rotation of SSL certificates, without necessitating a re-deployment or binding.  e.g.: false>,
```

Certifikáty, které se používají k navázání připojení TLS, se obvykle [získávají jako popisovač](/windows/win32/api/sspi/nf-sspi-acquirecredentialshandlea) prostřednictvím zprostředkovatele podpory zabezpečení s kanálem – to znamená, že klient nemá přímý přístup k privátnímu klíči samotného certifikátu. Kanál S-podporuje přesměrování (propojení) přihlašovacích údajů ve formě rozšíření certifikátu ([CERT_RENEWAL_PROP_ID](/windows/win32/api/wincrypt/nf-wincrypt-certsetcertificatecontextproperty#cert_renewal_prop_id)): Pokud je tato vlastnost nastavená, představuje její hodnota kryptografický otisk certifikátu pro prodloužení platnosti a kanál s-Channel se místo toho pokusí načíst propojený certifikát. Ve skutečnosti se tento seznam propojených (a snad acyklického) přesměruje do té doby, než ukončí Poslední certifikát – jedna bez značky obnovení. Tato funkce, pokud se používá uvážlivě, je vynikající zmírnění proti ztrátě dostupnosti způsobené certifikáty s vypršenou platností (například). V jiných případech může být příčinou výpadků, které je obtížné diagnostikovat a zmírnit. Kanál S-Channel provádí přecházení certifikátů na své vlastnosti obnovení bez podmíněného nezávisle na předmětu, emitentech nebo jakýchkoli jiných specifických atributů, které se účastní ověřování výsledného certifikátu klientem. Je to ale možné, že výsledný certifikát nemá přidružený žádný privátní klíč, nebo klíč nebyl ACLed k potenciálnímu spotřebiteli. 
 
Pokud je povoleno propojení, rozšíření virtuálního počítače trezoru klíčů po načtení zjištěného certifikátu z trezoru se pokusí najít vyhovující existující certifikáty, aby je propojí přes vlastnost rozšíření pro obnovení. Shoda je (exkluzivně) založená na alternativním názvu subjektu (SAN) a funguje jako exemplified níže.
Předpokládejme dva existující certifikáty následujícím způsobem: A: CN = "příslušenství Alice", SAN = {"alice.universalexports.com"}, obnovení = ' B: CN = "Bob 's bity", SAN = {"bob.universalexports.com", "bob.universalexports.net"}, obnovení = ' '
 
Předpokládat, že se certifikát C načte pomocí KVVM ext: CN = "v rámci Malloryho", SAN = {"alice.universalexports.com", "bob.universalexports.com", "mallory.universalexports.com"}
 
Seznam SAN je plně zahrnutý v C, a tak dále. rerenew = C. kryptografický otisk; Seznam SAN b má společný průnik s C, ale není plně zahrnutý do něj, takže B. obnovení zůstává prázdné.
 
Jakékoli pokus o vyvolání funkce AcquireCredentialsHandle (S kanálem) v tomto stavu u certifikátu A bude ve skutečnosti končit odesláním C vzdálené straně. V případě Service Fabric [přenosový subsystém](service-fabric-architecture.md#transport-subsystem) clusteru používá pro vzájemné ověřování kanál S-Channel, takže výše popsané chování má vliv na základní komunikaci clusteru přímo. V předchozím příkladu, za předpokladu, že je certifikát clusteru, co se stane dál, závisí:
  - Pokud se privátní klíč C ACLd k účtu, pod kterým je Fabric spuštěný, uvidíme, že selhání získá privátní klíč (SEC_E_UNKNOWN_CREDENTIALS nebo podobný).
  - Pokud je přístup k privátnímu klíči C dostupný, uvidíme chyby autorizace vrácené ostatními uzly (CertificateNotMatched, Neautorizováno atd.). 
 
V obou případech přenos selhává a cluster může pokračovat. příznaky se liší. Kvůli horšímu propojení závisí na pořadí obnovování, které se řídí pořadím seznamu pozorovaných certifikátů rozšíření KVVM, plánu obnovení v trezoru nebo dokonce přechodnými chybami, které by mohly změnit pořadí načítání.

Pro zmírnění těchto incidentů doporučujeme:
  - Nekombinujte sítě SAN s různými certifikáty trezoru; každý certifikát trezoru by měl sloužit k odlišnému účelu a jejich předmět a síť SAN by měly odrážet konkrétní význam
  - v seznamu sítě SAN uveďte běžný název subjektu (jako, doslova, "CN = <subject common name> ").  
  - Pokud si nejste jistí, zakažte propojování při obnovení pro certifikáty zřízené s rozšířením KVVM. 

#### <a name="why-use-a-user-assigned-managed-identity-what-are-the-implications-of-using-it"></a>Proč použít spravovanou identitu přiřazenou uživatelem? Jaké jsou důsledky jeho použití?
Jak je uvedeno výše v fragmentech kódu JSON, je nutné určit sekvencování operací/aktualizací, aby bylo zaručeno úspěch převodu, a zachovat dostupnost clusteru. Konkrétně prostředek sady škálování virtuálního počítače deklaruje a používá jeho identitu k načítání tajných kódů v jednom (z perspektivy uživatele). Rozšíření virtuálního počítače Service Fabric (které cluster Bootstrap) závisí na dokončení rozšíření virtuálního počítače trezoru klíčů, které závisí na úspěšném načtení pozorovaných certifikátů. Rozšíření KVVM používá k přístupu k trezoru identitu sady škálování virtuálního počítače. to znamená, že před nasazením sady škálování virtuálního počítače musí být zásady přístupu v trezoru už aktualizované. 

Aby bylo možné vytvořit spravovanou identitu nebo ji přiřadit jinému prostředku, musí mít operátor nasazení požadovanou roli (ManagedIdentityOperator) v předplatném nebo skupině prostředků, kromě rolí potřebných ke správě dalších prostředků, na které se odkazuje v šabloně. 

Z hlediska zabezpečení si vyvoláte, že virtuální počítač (sada škálování) se považuje za bezpečnostní hranici v souvislosti s identitou Azure. To znamená, že jakákoliv aplikace hostovaná na virtuálním počítači může v zásadě získat přístupový token představující tokeny přístupu spravované virtuálním počítačem z neověřeného koncového bodu IMDS. Pokud považujete virtuální počítač za sdílené nebo víceklientské prostředí, možná se tato metoda načítání certifikátů clusteru neuvádí. Jediným mechanismem zřizování vhodným pro autovýměnu certifikátu je však.

## <a name="troubleshooting-and-frequently-asked-questions"></a>Řešení problémů a nejčastější dotazy

*Otázka*: jak programově zaregistrovat certifikát spravovaný trezorem klíčů?
Odpověď *: Zjistěte* název vystavitele z dokumentace k trezoru klíčů a pak ho nahraďte ve skriptu níže.  
```PowerShell
  $issuerName=<depends on your PKI of choice>
    $clusterVault="sftestcus"
    $clusterCertVaultName="sftstcncluster"
    $clusterCertCN="cus.cluster.sftstcn.system.servicefabric.azure-int"
    Set-AzKeyVaultCertificateIssuer -VaultName $clusterVault -Name $issuerName -IssuerProvider $issuerName
    $distinguishedName="CN=" + $clusterCertCN
    $policy = New-AzKeyVaultCertificatePolicy `
        -IssuerName $issuerName `
        -SubjectName $distinguishedName `
        -SecretContentType 'application/x-pkcs12' `
        -Ekus "1.3.6.1.5.5.7.3.1", "1.3.6.1.5.5.7.3.2" `
        -ValidityInMonths 4 `
        -KeyType 'RSA' `
        -RenewAtPercentageLifetime 75        
    Add-AzKeyVaultCertificate -VaultName $clusterVault -Name $clusterCertVaultName -CertificatePolicy $policy
    
    # poll the result of the enrollment
    Get-AzKeyVaultCertificateOperation -VaultName $clusterVault -Name $clusterCertVaultName 
```

*Otázka*: co se stane, když se certifikát vystaví nedeklarovaným/nespecifikovaným vystavitelem? Kde můžu získat vyčerpávající seznam aktivních vystavitelů dané infrastruktury veřejných klíčů?
Odpověď *: Pokud* deklarace certifikátu určuje kryptografické otisky vystavitele a přímý Vydavatel certifikátu není zahrnutý v seznamu připnutých vystavitelů, bude se certifikát považovat za neplatný – bez ohledu na to, jestli je jeho kořen důvěryhodný pro klienta. Proto je důležité zajistit, aby byl seznam vystavitelů aktuální a aktuální. Zavedení nového vystavitele je vzácná událost a před tím, než se začne vystavovat certifikáty, je třeba ji široce zveřejnit. 

Infrastruktura veřejných klíčů (PKI) bude obecně publikovat a udržovat prohlášení o certifikaci v souladu se [specifikací IETF RFC 7382](https://tools.ietf.org/html/rfc7382). Kromě dalších informací bude obsahovat všechny aktivní vystavitele. Načítání tohoto seznamu prostřednictvím kódu programu se může lišit od infrastruktury veřejných klíčů do jiné.   

Pro interní infrastruktury veřejných klíčů společnosti Microsoft si přečtěte interní dokumentaci k koncovým bodům nebo sadám SDK, které slouží k načtení autorizovaných vystavitelů. je zodpovědností vlastníka clusteru pravidelně kontrolovat tento seznam a zajistit, aby definice clusteru zahrnovala *všechny* očekávané vystavitele.

*Otázka*: jsou podporovány vícenásobné infrastruktury veřejných klíčů? 
Odpověď *: Ano*; v manifestu clusteru nemůžete deklarovat více položek CN se stejnou hodnotou, ale může vypsat vystavitele z více infrastruktury veřejných klíčů odpovídajících stejnému CN. Nejedná se o doporučený postup a postupy transparentnosti certifikátů můžou zabránit vydání takových certifikátů. Nicméně jako způsob migrace z jedné infrastruktury veřejných klíčů do jiné se jedná o přijatelný mechanismus.

*Otázka*: Co když aktuální certifikát clusteru není vydán certifikační autoritou nebo nemá zamýšlený předmět? 
Odpověď *: Získejte* certifikát se zamýšleným subjektem a přidejte ho do definice clusteru jako sekundární, podle kryptografického otisku. Po úspěšném dokončení upgradu spusťte jiný upgrade konfigurace clusteru, aby se deklarace certifikátu převedla na běžný název. 

[Image1]:./media/security-cluster-certificate-mgmt/certificate-journey-thumbprint.png
[Image2]:./media/security-cluster-certificate-mgmt/certificate-journey-common-name.png
