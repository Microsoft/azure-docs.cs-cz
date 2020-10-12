---
title: Ověřování pomocí certifikátu X. 509 v clusteru Service Fabric
description: Přečtěte si o ověřování pomocí certifikátů v Service Fabric clusterech a o tom, jak detekovat, zmírnit a opravovat problémy související s certifikátem.
ms.topic: conceptual
ms.date: 03/16/2020
ms.custom: sfrev
ms.openlocfilehash: 36717f526f88af753f3929d62e84ee65be4320e9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86259018"
---
# <a name="x509-certificate-based-authentication-in-service-fabric-clusters"></a>Ověřování pomocí certifikátu X. 509 v clusterech Service Fabric

Tento článek doplňuje Úvod do [Service Fabric zabezpečení clusteru](service-fabric-cluster-security.md)a přechází do podrobností o ověřování pomocí certifikátů v clusterech Service Fabric. Předpokládáme, že čtenář je obeznámen se základními koncepcemi zabezpečení a také s ovládacími prvky, které Service Fabric zpřístupňují řízení zabezpečení clusteru.  

Témata zahrnutá pod tímto názvem:

* Základy ověřování založené na certifikátech
* Identity a jejich příslušné role
* Pravidla konfigurace certifikátu
* Řešení problémů a nejčastější dotazy

## <a name="certificate-based-authentication-basics"></a>Základy ověřování založené na certifikátech
Jako stručný aktualizační program je certifikát v zabezpečení instrumentací, který je určen k vázání informací týkajících se entity (subjektu) na jejich vlastnictví dvojice asymetrických kryptografických klíčů, a proto představuje základní konstrukci kryptografie s veřejným klíčem. Klíče reprezentované certifikátem lze použít k ochraně dat nebo k prokázání identity držitelů klíčů. Při použití ve spojení se systémem infrastruktury veřejných klíčů (PKI) může certifikát představovat další vlastnosti předmětu, jako je vlastnictví internetové domény nebo určitá oprávnění, která mu byla udělena vystavitelem certifikátu (označovaného jako certifikační autorita nebo certifikační autorita). Společná aplikace certifikátů podporuje kryptografický protokol TLS (Transport Layer Security), který umožňuje zabezpečenou komunikaci přes počítačovou síť. Konkrétně klient a server používají certifikáty k zajištění ochrany osobních údajů a integrity jejich komunikace a také k provádění vzájemného ověřování.

V Service Fabric základní vrstva clusteru (federace) také vytváří protokol TLS (mezi jinými protokoly), aby bylo možné zajistit spolehlivou a zabezpečenou síť zúčastněných uzlů. Připojení ke clusteru pomocí Service Fabric rozhraní API klienta používají protokol TLS i k ochraně provozu a také k navázání identit smluvních stran. Konkrétně při použití pro ověřování v Service Fabric se certifikát dá použít k prokázání následujících deklarací identity: a) předvádějící přihlašovací údaje certifikátu má vlastní klíč b). hodnota hash SHA-1 certifikátu se shoduje s deklarací obsaženou v definici clusteru, nebo c) rozlišující běžný název subjektu certifikátu odpovídá deklaraci zahrnuté v definici clusteru. a Vystavitel certifikátu je známý nebo důvěryhodný.

V seznamu výše je "b" colloquially známé jako "připnutí miniatur"; v tomto případě se deklarace odkazuje na konkrétní certifikát a sílu schématu ověřování, které jsou v zásadě neproveditelné, aby se zfalšovat certifikát, který má stejnou hodnotu hash jako jiný, a přitom je ve všech ostatních ohledech stále platný objekt ve správném formátu. Položka "c" představuje alternativní formu deklarace certifikátu, kde síla schématu vychází z kombinace předmětu certifikátu a vydávající autority. V tomto případě deklarace odkazuje na třídu certifikátů – všechny dva certifikáty se stejnými vlastnostmi se považují za plně ekvivalentní. 

V následujících částech se dozvíte, jak Service Fabric runtime používá a ověřuje certifikáty, aby se zajistilo zabezpečení clusteru.

## <a name="identities-and-their-respective-roles"></a>Identity a jejich příslušné role
Předtím, než se začnete do podrobností o ověřování nebo zabezpečení komunikačních kanálů, je důležité seznam zúčastněných účastníků a odpovídajících rolí, které hrají v clusteru:
- modul runtime Service Fabric, označovaný jako systém: sada služeb, které poskytují abstrakce a funkce představující cluster. Při odkazování na komunikaci mezi clustery mezi instancemi systému budeme používat pojem identitu clusteru. při odkazování na cluster jako příjemce nebo cíl provozu mimo cluster budeme používat termín identity serveru.
- hostované aplikace, označované jako aplikace: kód poskytnutý vlastníkem clusteru, který se orchestruje a spustí v clusteru.
- Klienti: v závislosti na konfiguraci clusteru můžou být v clusteru povolené připojení a spouštění funkcí v clusteru. Rozlišujeme dvě úrovně oprávnění – "uživatel" a "admin" v uvedeném pořadí. Klient "uživatel" je omezen primárně na operace jen pro čtení (ale ne na všechny funkce jen pro čtení), zatímco klient admin má neomezený přístup k funkcím clusteru. (Další informace najdete [v tématu role zabezpečení v clusteru Service Fabric](service-fabric-cluster-security-roles.md).)
- (Jenom Azure) Service Fabric služby, které orchestrují a vystavují ovládací prvky pro provoz a správu Service Fabricch clusterů, označované jako jednoduše "Service". V závislosti na prostředí se služba může vztahovat na poskytovatele prostředků Azure Service Fabric nebo na jiné poskytovatele prostředků vlastněné a provozované Service Fabric týmem.

V zabezpečeném clusteru je možné každou z těchto rolí nakonfigurovat se svou vlastní odlišnou identitou, která je deklarována jako párování předdefinovaného názvu role a odpovídajícího přihlašovacího údaje. Service Fabric podporuje deklaraci přihlašovacích údajů jako certifikátů nebo instančního objektu založeného na doméně. (Identity Windows-/Kerberos-based jsou také podporovány, ale jsou nad rámec tohoto článku; informace o [zabezpečení systému Windows v Service Fabricch clusterech](service-fabric-windows-cluster-windows-security.md).) V clusterech Azure můžou být klientské role taky deklarované jako [identity založené na Azure Active Directory](service-fabric-cluster-creation-setup-aad.md).

Jak zmiňovali výše, modul runtime Service Fabric definuje dvě úrovně oprávnění v clusteru: admin a User. Klient správce a komponenta System budou spolupracovat s oprávněními admin, a proto je nerozlišuje od sebe. Po navázání připojení v/v clusteru udělí ověřený volající Service Fabric jednu ze dvou rolí jako základ pro následné autorizaci. Podrobně probereme ověřování v následujících oddílech.

## <a name="certificate-configuration-rules"></a>Pravidla konfigurace certifikátu
### <a name="validation-rules"></a>Ověřovací pravidla
Nastavení zabezpečení Service Fabric clusteru popisují v zásadě následující aspekty:
- typ ověřování; Jedná se o čas vytvoření, neproměnlivý charakteristický cluster. Příklady takových nastavení jsou "ClusterCredentialType", "ServerCredentialType" a povolené hodnoty jsou "none", "x509" nebo "Windows". Tento článek se zaměřuje na ověřování typu x509.
- ověřovací pravidla (ověřování); Tato nastavení jsou nastavena vlastníkem clusteru a popisují, která pověření budou přijata pro danou roli. Příklady budou přezkoumány v hloubkě hned níže.
- nastavení, které slouží k vylepšení nebo nepostupné změně výsledku ověřování; zde uvedené příklady zahrnují příznaky (de-) omezující vynucování seznamů odvolaných certifikátů atd.

> [!NOTE]
> Níže uvedené příklady konfigurace clusteru jsou výňatky z manifestu clusteru ve formátu XML, který podporuje přímo Service Fabric funkce popsané v tomto článku. Stejná nastavení je možné vyjádřit přímo v vyjádřeních reprezentace definice clusteru ve formátu JSON, ať už je to samostatný manifest clusteru JSON, nebo šablona Management prostředků Azure.

Ověřovací pravidlo certifikátu zahrnuje následující prvky:
- odpovídající role: klient, klient pro správu (privilegovaná role)
- přihlašovací údaje přijaté pro roli deklarované buď pomocí kryptografického otisku nebo běžného názvu subjektu

#### <a name="thumbprint-based-certificate-validation-declarations"></a>Deklarace ověřování certifikátů založené na kryptografických otiskech
V případě ověřovacích pravidel založených na kryptografických otiskech se ověří přihlašovací údaje prezentované volajícím požadující připojení v/ke clusteru takto:
  - přihlašovací údaje jsou platným, dobře vytvořeným certifikátem: jeho řetězec se dá sestavit, signatury se shodují.
  - certifikát je časově platný (NotBefore <= Now < NotAfter).
  - hodnota hash SHA-1 certifikátu odpovídá deklaraci, protože porovnávání řetězců nerozlišuje malá a velká písmena, kromě všech prázdných znaků.

Všechny chyby důvěryhodnosti zjištěné během sestavení řetězení nebo ověřování se potlačí pro deklarace založené na kryptografických otiskech, s výjimkou certifikátů, u kterých vypršela platnost, ale u tohoto případu existují i předpisy. Konkrétně chyby související s: stav odvolání je neznámý nebo offline, nedůvěryhodné kořenové, neplatné použití klíče, částečný řetěz se považuje za méně závažné chyby; místní, v tomto případě je certifikát pouze obálkou pro dvojici klíčů – zabezpečení spočívá ve skutečnosti, že vlastník clusteru nastavil míru ochrany privátního klíče.

Následující úryvek z manifestu clusteru exemplifies taková sada ověřovacích pravidel založených na kryptografických otiskech:

```xml
<Section Name="Security">
  <Parameter Name="ClusterCredentialType" Value="X509" />
  <Parameter Name="ServerAuthCredentialType" Value="X509" />
  <Parameter Name="AdminClientCertThumbprints" Value="d5ec...4264" />
  <Parameter Name="ClientCertThumbprints" Value="7c8f...01b0" />
  <Parameter Name="ClusterCertThumbprints" Value="abcd...1234,ef01...5678" />
  <Parameter Name="ServerCertThumbprints" Value="ef01...5678" />
</Section>
```

Každá z výše uvedených položek odkazuje na konkrétní identitu, jak je popsáno výše. Každá položka také umožňuje zadat více hodnot jako seznam řetězců oddělených čárkami. V tomto příkladu po úspěšném ověření příchozích přihlašovacích údajů se předvádějící certifikát s kryptografickým otiskem SHA-1 5ec... 4264 ' bude udělena role ' admin '; v opačném případě se volající ověřování pomocí certifikátu ' 7c8f... 01B0 bude udělena role uživatel, která je omezená na primárně jenom operace jen pro čtení. Příchozí volající, který prezentuje certifikát, jehož kryptografický otisk je buď ABCD... 1234 ' nebo ' ef01... 5678 ' bude přijat jako rovnocenný uzel v clusteru. Klient, který se připojuje ke koncovému bodu správy clusteru, očekává, že kryptografický otisk certifikátu serveru bude ef01... 5678. 

Jak už bylo zmíněno výše, Service Fabric v souladu s předpisy pro přijetí certifikátů, jejichž platnost vypršela; Důvodem je to, že certifikáty mají omezenou životnost a při deklaraci pomocí kryptografického otisku (která odkazuje na konkrétní instanci certifikátu) může vypršení platnosti certifikátu způsobit buď neúspěšné připojení ke clusteru, nebo nepřímou sbalení clusteru. Je příliš snadné zapomenout nebo opomíjení otočit certifikát připojený k kryptografickým otiskům, ale obnovení z takového stavu je obtížné.

K tomuto účelu vlastník clusteru může explicitně uvést certifikáty podepsané svým držitelem deklarované kryptografickým otiskem, a to takto:

```xml
  <Section Name="Security">
    <Parameter Name="AcceptExpiredPinnedClusterCertificate" Value="true" />
  </Section>
```
Toto chování se nerozšiřuje na certifikáty vydané certifikační autoritou. Pokud by se jednalo o tento případ, mohl by zrušený, známý, neprošlý certifikát, který by měl být "platný", jakmile již nebude v seznamu odvolaných certifikátů certifikační autority uveden, a proto představuje bezpečnostní riziko. U certifikátů podepsaných svým držitelem se vlastník clusteru považuje za jedinou stranu odpovědnou za zabezpečení privátního klíče certifikátu, který není v případě certifikátů vydaných certifikační autoritou. vlastník clusteru nemusí vědět, jak nebo kdy se jejich certifikát deklaroval jako napadený.

#### <a name="common-name-based-certificate-validation-declarations"></a>Běžné deklarace ověřování certifikátů založeného na názvech
Společné deklarace založené na názvech mají jednu z následujících forem:
- běžný název subjektu (jenom)
- běžný název subjektu s připnutím vystavitele

Nejdřív si představte, že výňatk z manifestu clusteru exemplifying oba styly deklarace:
```xml
    <Section Name="Security/ServerX509Names">
      <Parameter Name="server.demo.system.servicefabric.azure-int" Value="" />
    </Section>
    <Section Name="Security/ClusterX509Names">
      <Parameter Name="cluster.demo.system.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```
Deklarace odkazují na identity serveru a clusteru, v uvedeném pořadí. deklarace založené na CN mají vlastní oddíly v manifestu clusteru, oddělené od standardního zabezpečení. V obou deklaracích představuje "název" běžný název rozlišujícího subjektu a pole hodnota představuje očekávané vystavitele, a to následujícím způsobem:

- v prvním případě deklarace uvádí, že běžný název elementu rozlišujícího předmětu certifikátu serveru by měl odpovídat řetězci "server.demo.system. servicefabric. Azure-int"; prázdné pole hodnota označuje, že kořen řetězu certifikátů je důvěryhodný na uzlu nebo počítači, kde je certifikát serveru ověřený. v systému Windows to znamená, že certifikát může řetězit k libovolnému certifikátu nainstalovanému v úložišti Důvěryhodné kořenové certifikační autority.
- v druhém případě deklarace uvádí, že se jako partnerský uzel v clusteru přijímá jako rovnocenný uzel, pokud se běžný název certifikátu shoduje s řetězcem "cluster.demo.system. servicefabric. Azure-int" *a* kryptografický otisk přímého vystavitele certifikátu odpovídá jedné z položek oddělených čárkami v poli hodnota. (Tento typ pravidla se colloquially označuje jako běžný název s připnutím vystavitele.)

V obou případech je řetězec certifikátu sestavený a očekává se, že bude bez chyb. To znamená, že chyby odvolání, částečný řetěz nebo čas – neplatné chyby důvěryhodnosti se považují za závažné a ověření certifikátu se nezdaří. Připnutím vystavitelů dojde k tomu, že se stav nedůvěryhodných kořenových certifikátů považuje za nezávažnou chybu. bez ohledu na vzhled je to přísnější forma ověřování, protože umožňuje vlastníkovi clusteru omezit sadu autorizovaných a přijímaných vystavitelů na vlastní infrastrukturu veřejných klíčů (PKI).

Po sestavení řetězu certifikátů se ověří standardní zásada TLS/SSL s deklarovaným subjektem jako vzdálený název; certifikát se považuje za shodný s tím, že se běžný název subjektu nebo některé alternativní názvy subjektu shodují s deklarací CN z manifestu clusteru. V tomto případě jsou podporovány zástupné znaky a porovnávání řetězců nerozlišuje malá a velká písmena.

(Doporučujeme, abyste si vyjasnění výše popsané sekvence mohli provést pro každý typ použití klíče deklarovaného certifikátem; Pokud certifikát určuje použití klíče pro ověření klienta, řetěz se sestaví a vyhodnotí jako první pro roli klienta. V případě úspěchu se vyhodnocení dokončí a ověření proběhne úspěšně. Pokud certifikát nemá použití ověřování klienta nebo ověření se nezdařilo, modul runtime Service Fabric sestaví a vyhodnotí řetěz pro ověřování serveru.)

K dokončení příkladu ukazuje následující výpis deklarace klientských certifikátů podle běžného názvu:
```xml
    <Section Name="Security/AdminClientX509Names">
      <Parameter Name="admin.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
    <Section Name="Security/ClientX509Names">
      <Parameter Name="user.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```

Výše uvedené deklarace odpovídají identitám správců a uživatelů, v uvedeném pořadí. ověřování certifikátů deklarovaných tímto způsobem je přesně popsané v předchozích příkladech certifikátů clusteru a serveru.

> [!NOTE]
> Společné deklarace založené na názvech jsou určeny pro zjednodušení rotace a obecně správu certifikátů clusteru. Doporučuje se ale dodržovat následující doporučení, aby se zajistila nepřetržitá dostupnost a zabezpečení clusteru:
  * preferovat připnutí vystavitele při spoléhání na důvěryhodné kořeny
  * Vyhněte se kombinování vydavatelů z různých infrastruktury veřejných klíčů
  * Ujistěte se, že všechny očekávané vystavitele jsou uvedeny v deklaraci certifikátu; Neshoda vystavitele způsobí neúspěšné ověření.
  * Ujistěte se, že koncové body zásad certifikátu PKI jsou zjistitelná, dostupná a přístupná – to znamená, že koncové body AIA, CRL nebo OCSP jsou deklarované na listovém certifikátu a že jsou přístupné, aby bylo možné dokončit sestavení řetězu certifikátů.

Díky tomu, že při přijetí žádosti o připojení v clusteru zabezpečeném pomocí certifikátů X. 509 je vše spojeno s tím, že modul runtime Service Fabric použije nastavení zabezpečení clusteru k ověření přihlašovacích údajů vzdálené strany, jak je popsáno výše. v případě úspěchu se volající/Vzdálená strana považuje za ověřenou. Pokud přihlašovací údaje odpovídají více pravidlům ověřování, modul runtime udělí volajícímu roli nejvyšší privilegované role kteréhokoli z odpovídajících pravidel. 

### <a name="presentation-rules"></a>Prezentační pravidla
Předchozí část popisuje, jak ověřování funguje v clusteru zabezpečeném certifikátem. v této části se dozvíte, jak modul runtime Service Fabric sám zjistí a načte certifikáty, které používá pro komunikaci v clusteru. Říkáme jim pravidla pro prezentaci.

Stejně jako u pravidel ověřování určují pravidla prezentace roli a přidruženou deklaraci přihlašovacích údajů, která je vyjádřená kryptografickým otiskem nebo běžným názvem. Na rozdíl od pravidel ověřování nemají běžné deklarace založené na názvech předpisy pro připnutí vystavitele; Díky tomu je lepší flexibilita a Vylepšený výkon. Pravidla prezentace jsou deklarována v oddílech ' NodeType ' manifestu clusteru, pro každý typ samostatného uzlu; nastavení jsou rozdělena z oddílů zabezpečení clusteru, aby každý typ uzlu měl úplnou konfiguraci v jednom oddílu. V clusterech Azure Service Fabric je typ uzlu ve výchozím nastavení deklarací certifikátů odpovídajícím nastavením v sekci zabezpečení definice clusteru.

#### <a name="thumbprint-based-certificate-presentation-declarations"></a>Deklarace prezentace certifikátů založené na kryptografických otiskech
Jak je popsáno výše, Service Fabric modul runtime rozlišuje mezi jeho rolí jako partnerským uzlem dalších uzlů v clusteru a jako serverem pro operace správy clusteru. V zásadě se tato nastavení dají nakonfigurovat samostatně, ale v praxi je zarovnejte. Ve zbývající části tohoto článku předpokládáme, že nastavení bude mít pro jednoduchost shodu.

Z manifestu clusteru můžeme vzít v úvahu následující úryvek:
```xml
  <NodeTypes>
    <NodeType Name="nt1vm">
      <Certificates>
        <ClusterCertificate X509FindType="FindByThumbprint" X509FindValue="cc71...1984" X509FindValueSecondary="49e2...19d6" X509StoreName="my" Name="ClusterCertificate" />
        <ServerCertificate X509FindValue="cc71...1984" Name="ServerCertificate" />
        <ClientCertificate X509FindValue="cc71...1984" Name="ClientCertificate" />
      </Certificates>
    </NodeType>
  </NodeTypes>
```
Element ClusterCertificate demonstruje úplné schéma, včetně volitelných parametrů (' X509FindValueSecondary ') nebo těch s příslušnými výchozími hodnotami (' X509StoreName '); ostatní deklarace zobrazují zkrácený tvar. Deklarace certifikátu clusteru výše uvádí, že nastavení zabezpečení uzlů typu nt1vm se inicializuje s certifikátem cc71. 1984 ' jako primární a ' 49e2.. certifikát 19d6 jako sekundární; u obou certifikátů se očekává, že se nacházejí v \' úložišti certifikátů LocalMachine (nebo ekvivalentní cestě Linux, *var/lib/sfcerts*).

#### <a name="common-name-based-certificate-presentation-declarations"></a>Běžné deklarace prezentace certifikátů na základě názvů
Certifikáty typu uzlu lze také deklarovat pomocí běžného názvu subjektu, jak je uvedeno v následujícím exemplified:

```xml
  <NodeTypes>
    <NodeType Name="nt1vm">
      <Certificates>
        <ClusterCertificate X509FindType="FindBySubjectName" X509FindValue="demo.cluster.azuredocpr.system.servicefabric.azure-int" Name="ClusterCertificate" />
      </Certificates>
    </NodeType>
  </NodeTypes>
```

Pro oba typy deklarací načtou uzel Service Fabric konfiguraci při spuštění, vyhledá a načte zadané certifikáty a seřadí je v sestupném pořadí podle jejich atributu NotAfter; certifikáty s vypršenou platností jsou ignorovány a první prvek seznamu je vybrán jako pověření klienta pro jakékoli Service Fabric v rámci tohoto uzlu. (V důsledku toho Service Fabric upřednostní certifikát, který brzy vyprší.)

Všimněte si, že pro deklarace prezentace s běžným názvem se certifikát považuje za shodu, pokud se běžný název daného subjektu rovná poli X509FindValue (nebo X509FindValueSecondary) deklarace jako přesné porovnání řetězců s rozlišováním velkých a malých písmen. To je na rozdíl od ověřovacích pravidel, která podporují porovnávání se zástupnými znaky, stejně jako porovnávání řetězců bez rozlišení velkých a malých písmen.  

### <a name="miscellaneous-certificate-configuration-settings"></a>Různá nastavení konfigurace certifikátu
Dříve jsme uvedli, že nastavení zabezpečení Service Fabricho clusteru také umožňují velmi velmi měnit chování ověřovacího kódu. I když článek o [Service Fabric nastavení clusteru](service-fabric-cluster-fabric-settings.md) představuje komplexní a nejaktuálnější seznam nastavení, rozšíříme to podle toho, co tady využijeme, aby se plně vystavení ověřování na základě certifikátu dokončilo. Pro každé nastavení vyvysvětlíme záměr, výchozí hodnotu/chování, jak má vliv na ověřování a jaké hodnoty jsou přijatelné.

Jak už bylo zmíněno, ověřování certifikátu vždy implikuje sestavování a vyhodnocování řetězu certifikátů. V případě certifikátů vydaných certifikační autoritou toto zjevně jednoduché volání rozhraní API pro operační systém obvykle zahrnuje několik odchozích volání do různých koncových bodů vydávajícího PKI, ukládání odpovědí do mezipaměti atd. Vzhledem k tomu, že je v clusteru Service Fabrica volání ověřování certifikátů, mohou všechny problémy v koncových bodech infrastruktury veřejných klíčů způsobit snížení dostupnosti clusteru nebo navýšení pravého rozpisu. I když odchozí volání nelze potlačit (Další informace najdete níže v části Nejčastější dotazy), je možné použít následující nastavení k maskování chyb ověřování způsobených neúspěšnými voláními CRL.

  * CrlCheckingFlag – v části zabezpečení se řetězec převedl na UINT. Hodnota tohoto nastavení je používána Service Fabric k maskování chyb stavu řetězu certifikátů změnou chování řetězového sestavení; do volání Win32 CryptoAPI [CertGetCertificateChain](/windows/win32/api/wincrypt/nf-wincrypt-certgetcertificatechain) se předává jako parametr dwFlags a dá se nastavit na jakoukoli platnou kombinaci příznaků přijatou funkcí. Hodnota 0 vynutí, aby modul runtime Service Fabric ignoroval chyby stavu důvěryhodnosti – to se nedoporučuje, protože jeho použití by představovalo významnou expozici zabezpečení. Výchozí hodnota je 0x40000000 (CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT).

  Kdy použít: pro místní testování s certifikáty podepsanými držitelem nebo certifikáty pro vývojáře, které nejsou plně vytvořené/nemají správnou infrastrukturu veřejného klíče pro podporu certifikátů. Může také používat jako zmírnění rizik v prostředí AIR gapped během přechodu mezi infrastruktury veřejných klíčů.

  Jak použít: budeme pořizovat příklad, který vynucuje kontrolu odvolání pro přístup pouze k adresám URL uloženým v mezipaměti. Přičemž
  ```C++
  #define CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY         0x80000000
  ```
  deklarace v manifestu clusteru pak bude:
  ```xml
    <Section Name="Security">
      <Parameter Name="CrlCheckingFlag" Value="0x80000000" />
    </Section>
  ```

  * IgnoreCrlOfflineError – v části zabezpečení je logická hodnota s výchozí hodnotou false. Představuje zástupce pro potlačení stavu chyby při vytváření řetězu odvolání v režimu offline (nebo následného stavu chyby ověřování zásad řetězení).

  Kdy použít: místní testování nebo s certifikáty pro vývojáře, které nejsou zajištěny řádnou infrastrukturou veřejných klíčů. Používejte jako zmírnění rizik v prostředí AIR gapped nebo v případě, že je známo, že je infrastruktura veřejných klíčů nepřístupná.

  Jak používat:
  ```xml
    <Section Name="Security">
      <Parameter Name="IgnoreCrlOfflineError" Value="true" />
    </Section>
  ```

  Další významná nastavení (vše v části zabezpečení):
  * AcceptExpiredPinnedClusterCertificate – popsáno v oddílu vyhrazeném pro ověřování certifikátu založeném na kryptografických otiskech; povoluje přijímání certifikátů clusteru podepsaných držitelem, jejichž platnost vypršela. 
  * CertificateExpirySafetyMargin – interval vyjádřený v minutách před časovým razítkem NotAfter certifikátu a během kterého se certifikát považuje za neriziku pro vypršení platnosti. Service Fabric sleduje certifikáty clusteru a pravidelně generuje sestavy o stavu podle jejich zbývající dostupnosti. V intervalu "zabezpečení" jsou tyto zprávy o stavu zvýšeny na stav "upozornění". Výchozí hodnota je 30 dní.
  * CertificateHealthReportingInterval – řídí četnost zpráv o stavu, které se týkají zbývající doby platnosti certifikátů clusteru. Sestavy budou vygenerovány pouze jednou za tento interval. Hodnota se vyjadřuje v sekundách, přičemž výchozí je 8 hodin.
  * EnforcePrevalidationOnSecurityChanges-Boolean řídí chování upgradu clusteru při zjišťování změn nastavení zabezpečení. Pokud je nastavená hodnota true, upgrade clusteru se pokusí zajistit, aby aspoň jeden z certifikátů, které odpovídají některým pravidlům prezentace, mohl předat odpovídající ověřovací pravidlo. Před použitím nového nastavení na libovolný uzel se provede předběžné ověření, ale spustí se jenom v uzlu hostujícím primární repliku služby Správce clusterů v době zahájení upgradu. V době psaní tohoto nastavení má nastavení hodnotu false a pro nové clustery Azure Service Fabric se nastaví na true s verzí modulu runtime počínaje 7,1.
 
### <a name="end-to-end-scenario-examples"></a>Koncový scénář (příklady)
Prohlédli jsme si pravidla pro prezentace, ověřovací pravidla a přizpůsobování příznaků, ale jak to vše funguje? V této části budeme pracovat na dvou ucelených příkladech, které demonstrují, jak se dá nastavení zabezpečení využít při bezpečném upgradu clusteru. Všimněte si, že se nejedná o komplexní Dissertation správy certifikátů v Service Fabric. v tomto tématu najdete doprovodný článek.

Oddělení prezentačních a ověřovacích pravidel představuje zřejmou otázku (nebo obavy), zda se mohou odchýlit a co by bylo možné důsledky. Je to ve skutečnosti možné, že výběr uzlu ověřovacího certifikátu neprojde ověřovací pravidla jiného uzlu. V podstatě je tato neshoda primární příčinou incidentů souvisejících s ověřováním. Oddělení těchto pravidel zároveň umožňuje, aby cluster pokračoval v provozu během upgradu, který změní nastavení zabezpečení clusteru. Je třeba zvážit, že rozšířením prvních pravidel ověřování jako prvního kroku budou všechny uzly clusteru sblíženy s novými nastaveními a budou přitom pořád používat aktuální přihlašovací údaje. 

Pokud v Service Fabric clusteru dojde k upgradu, postupuje se (až 5) "upgradovací domény" nebo UDs. V daném okamžiku jsou upgradovány/měněny pouze uzly v aktuální UD a upgrade bude pokračovat na další UD pouze v případě, že to umožňuje dostupnost clusteru. (Další informace najdete v tématu [Service Fabric upgradů clusteru](service-fabric-cluster-upgrade.md) a dalších článcích na stejném tématu.) Změny certifikátu nebo zabezpečení jsou obzvláště rizikové, protože můžou izolovat uzly z clusteru nebo nechat cluster na hranici ztráty kvora.

K popisu nastavení zabezpečení uzlu použijeme následující zápis:

NK: {P:{TP = A}, V:{TP = A}}, kde:
  - "NK" představuje uzel v upgradovací doméně *k*
  - ' P ' představuje aktuální pravidla prezentace uzlu (za předpokladu, že odkazuje pouze na certifikáty clusteru); 
  - ' V ' představuje aktuální ověřovací pravidla uzlu (pouze certifikát clusteru)
  - "Transakční = A" představuje deklaraci na základě kryptografických otisků (TP) s certifikátem A jako kryptografickým otiskem certifikátu.
  - CN = B představuje běžnou deklaraci založenou na názvu (CN), přičemž B se jedná o běžný název subjektu certifikátu. 

#### <a name="rotating-a-cluster-certificate-declared-by-thumbprint"></a>Otočení certifikátu clusteru deklarovaného kryptografickým otiskem
Následující text popisuje, jak se dá použít upgrade na 2 fáze k bezpečnému zavedení sekundárního certifikátu clusteru deklarovaného kryptografickým otiskem. první fáze zavádí novou deklaraci certifikátu v ověřovacích pravidlech a druhá fáze je v prezentačních pravidlech zavádí:
  - počáteční stav: N0 = {P:{TP = A}, V:{TP = A}},... NK = {P:{TP = A}, V:{TP = A}} – cluster je v klidovém umístění, všechny uzly sdílejí společnou konfiguraci.
  - Po dokončení upgradu domény 0: N0 = {P:{TP = A}, V:{TP = A, TP = B}},... NK = {P:{TP = A}, V:{TP = A}} – uzly v UD0 budou představovat certifikát a a přijmou certifikáty a nebo B; všechny ostatní uzly jsou přítomné a přijímají jenom certifikát.
  - Po dokončení poslední domény upgradu: N0 = {P:{TP = A}, V:{TP = A, TP = B}},... NK = {P:{TP = A}, V:{TP = A, TP = B}} – všechny uzly obsahují certifikát A, všechny uzly přijmou buď certifikát A, nebo B.
      
V tomto okamžiku je cluster znovu ve rovnováze a druhá fáze nastavení zabezpečení upgrade/změna může začít:
  - Po dokončení upgradu domény 0: N0 = {P:{TP = A, TP = B}, V:{TP = A, TP = B}},... NK = {P:{TP = A}, V:{TP = A, TP = B}} – uzly v UD0 začnou prezentovat B, který je přijímán jakýmkoli jiným uzlem v clusteru.
  - Po dokončení poslední domény upgradu: N0 = {P:{TP = A, TP = B}, V:{TP = A, TP = B}},... NK = {P:{TP = A, TP = B}, V:{TP = A, TP = B}} – všechny uzly byly přepnuty na předložení certifikátu B. certifikát A lze nyní vyřadit/odebrat z definice clusteru s následnou sadou upgradů.

#### <a name="converting-a-cluster-from-thumbprint--to-common-name-based-certificate-declarations"></a>Převod clusteru z kryptografického otisku na deklarace certifikátů na základě běžných názvů
Podobně Změna typu deklarace certifikátu (od kryptografického otisku na běžný název) bude následovat po stejném vzoru jako výše. Všimněte si, že ověřovací pravidla umožňují deklarovat certifikáty dané role pomocí kryptografického otisku i obecného názvu ve stejné definici clusteru. Naproti tomu však pravidla prezentace umožňují pouze jednu formu deklarace. V případě incidentu bezpečného přístupu k převodu certifikátu clusteru z kryptografického otisku na běžný název je první uvedení zamýšleného cílového certifikátu pomocí kryptografického otisku a následná změna této deklarace na společný název založený na názvu. V následujícím příkladu se předpokládá, že kryptografický otisk a a běžný název subjektu B odkazují na stejný certifikát. 

  - počáteční stav: N0 = {P:{TP = A}, V:{TP = A}},... NK = {P:{TP = A}, V:{TP = A}} – cluster je v klidovém umístění, všechny uzly sdílejí společnou konfiguraci s primárním kryptografickým otiskem certifikátu.
  - Po dokončení upgradu domény 0: N0 = {P:{TP = A}, V:{TP = A, CN = B}},... NK = {P:{TP = A}, V:{TP = A}} – uzly v UD0 budou představovat certifikát a a přijmou certifikáty s buď kryptografickým otiskem, nebo běžným názvem B; všechny ostatní uzly jsou přítomné a přijímají jenom certifikát.
  - Po dokončení poslední domény upgradu: N0 = {P:{TP = A}, V:{TP = A, CN = B}},... NK = {P:{TP = A}, V:{TP = A, CN = B}} – všechny uzly obsahují certifikát A, všechny uzly přijmou buď certifikát A (TP), nebo B (CN).

V tuto chvíli můžeme pokračovat se změnou pravidel prezentace s následným upgradem:
  - Po dokončení upgradu domény 0: N0 = {P:{CN = B}, V:{TP = A, CN = B}},... NK = {P:{TP = A}, V:{TP = A, CN = B}} – uzly v UD0 budou prezentovat certifikát B nalezený v rámci CN a přijímat certifikáty s buď kryptografickým otiskem, nebo běžným názvem B; všechny ostatní uzly jsou přítomné a přijímají jenom certifikát, který je vybraný pomocí kryptografického otisku.
  - Po dokončení poslední domény upgradu: N0 = {P:{CN = B}, V:{TP = A, CN = B}},... NK = {P:{CN = B}, V:{TP = A, CN = B}} – všechny uzly prezentují certifikát B, který našel CN, všechny uzly přijmou buď certifikát A (TP), nebo B (CN).
    
Dokončení fáze 2 také označuje převod clusteru na běžné certifikáty založené na názvech. deklarace ověřování založené na kryptografických otiskech je možné odebrat v následném upgradu clusteru.

> [!NOTE]
> V clusterech Azure Service Fabric jsou výše uvedené pracovní postupy Orchestrované poskytovatelem prostředků Service Fabric; vlastník clusteru je stále zodpovědný za zřizování certifikátů do clusteru podle uvedených pravidel (prezentace nebo ověřování) a je doporučován při provádění změn v několika krocích.

V samostatném článku budeme řešit téma Správa a zřizování certifikátů do clusteru Service Fabric.

## <a name="troubleshooting-and-frequently-asked-questions"></a>Řešení problémů a nejčastější dotazy
Při ladění potíží souvisejících s ověřováním v Service Fabric clusterech není jednoduché, hopeful se vám mohou pomoci následující rady a tipy. Nejjednodušší způsob, jak začít s vyšetřováním, je prozkoumávat protokoly událostí Service Fabric v uzlech clusteru – nemusí nutně jenom zobrazovat příznaky, ale také uzly, které se nemůžou připojit k jednomu ze svých sousedů. V systému Windows se události významnosti obvykle protokolují do kanálů "Applications and Services Logs\Microsoft-ServiceFabric\Admin" nebo "Operational" (v uvedeném pořadí). V některých případech může být užitečné [Povolit protokolování CAPI2](/archive/blogs/benjaminperkins/enable-capi2-event-logging-to-troubleshoot-pki-and-ssl-certificate-issues), zachytit další podrobnosti týkající se ověření certifikátu, načtení seznamu CRL/seznamu CTL atd. (Nezapomeňte ho po dokončení reprodukci zablokovat, může být poměrně podrobný.)

Typickými příznaky, které v clusteru dochází k problémům s ověřováním, jsou tyto problémy: 
  - uzly jsou mimo provoz nebo cykly. 
  - pokusy o připojení byly odmítnuty.
  - pokusy o připojení čekají na vypršení

Jednotlivé příznaky mohou být způsobeny různými problémy a stejná hlavní příčina může zobrazovat různé manifesty; v takovém případě přesně vypíšeme malý vzorek typických problémů s doporučeními pro jejich opravu. 

* Uzly můžou vyměňovat zprávy, ale nemůžou se připojit. Možnou příčinou, proč se pokusy o připojení ukončí, je chyba "Neshoda s certifikátem" – jedna ze stran v připojeních Service Fabric-to-Service Fabric prezentuje certifikát, který nesplňuje ověřovací pravidla příjemce. Může doprovázet některá z následujících chyb: 
  ```C++
  0x80071c44    -2147017660 FABRIC_E_SERVER_AUTHENTICATION_FAILED
  ```
  Chcete-li diagnostikovat/prozkoumat další kroky: na všech uzlech, které se pokoušejí o připojení, určete, který certifikát se má prezentovat; Prohlédněte si certifikát a vyzkoušením a emulaci ověřovacích pravidel (kontrola kryptografického otisku nebo rovnosti běžných názvů, pokud je zadaná), ověřte kryptografické otisky emitenta

  Dalším běžným kódem chyby může být:
  ```C++
  0x800b0109    -2146762487 CERT_E_UNTRUSTEDROOT
  ```
  V tomto případě se certifikát deklaruje pomocí společného názvu a platí jedna z následujících možností:
    - Vystavitelé nejsou připnutý a kořenový certifikát není důvěryhodný nebo
    - Vystavitelé jsou připnuté, ale deklarace nezahrnuje kryptografický otisk přímého vystavitele tohoto certifikátu.

* Uzel je v síti, ale nemůže se připojit k jiným uzlům; ostatní uzly neobdrží příchozí provoz z neúspěšného uzlu. V tomto případě je možné, že se nepovede načíst certifikát na místním uzlu. Vyhledejte následující chyby:
  - certifikát nebyl nalezen – zajistěte, aby certifikáty deklarované v pravidlech prezentace mohly být vyřešeny obsahem úložiště certifikátů úložišti LocalMachine\MY (nebo jako zadané). 
    Možné příčiny selhání můžou zahrnovat: 
      - neplatné znaky v deklaraci kryptografického otisku
      - certifikát není nainstalovaný.
      - platnost certifikátu vypršela.
      - deklarace Common-Name obsahuje předponu CN =.
      - deklarace určuje zástupný znak a v úložišti certifikátů neexistuje žádná přesná shoda (deklarace: CN = *. mydomain. com, skutečný certifikát: CN = Server. mydomain. com).

  - neznámé přihlašovací údaje – označuje buď chybějící privátní klíč, který odpovídá certifikátu, obvykle doprovází kód chyby: 
    ```C++ 
    0x8009030d  -2146893043 SEC_E_UNKNOWN_CREDENTIALS
    0x8009030e  -2146893042 SEC_E_NO_CREDENTIALS
    ```
    Pokud chcete problém vyřešit, ověřte existenci privátního klíče. Ověřte, že SFAdmins má přístup k privátnímu klíči oprávnění Read | Execute.

  - Chybný typ poskytovatele – označuje certifikát kryptografické nové generace (CNG) ("poskytovatel úložiště klíčů Microsoftu"); v tuto chvíli Service Fabric podporuje jenom certifikáty CAPI1. Obvykle doprovází kód chyby:
    ```C++
    0x80090014  -2146893804 NTE_BAD_PROV_TYPE
    ```
    Pokud chcete problém vyřešit, znovu vytvořte certifikát clusteru pomocí CAPI1 (např. "poskytovatel kryptografických služeb standardu RSA a AES"). Další informace o zprostředkovatelích kryptografických služeb najdete v tématu [Principy zprostředkovatelů kryptografických služeb](/windows/win32/seccertenroll/understanding-cryptographic-providers) .
