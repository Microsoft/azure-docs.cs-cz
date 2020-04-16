---
title: Ověřování založené na certifikátech X.509 v clusteru Prostředků infrastruktury služby
description: Informace o ověřování na základě certifikátů v clusterech Service Fabric a o tom, jak zjistit, zmírnit a opravit problémy související s certifikáty.
ms.topic: conceptual
ms.date: 03/16/2020
ms.custom: sfrev
ms.openlocfilehash: 699015e322c599dea996b3a8b9dbc0a4589440ab
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429665"
---
# <a name="x509-certificate-based-authentication-in-service-fabric-clusters"></a>Ověřování založené na certifikátech X.509 v clusterech Service Fabric

Tento článek doplňuje úvod [do zabezpečení clusteru Service Fabric](service-fabric-cluster-security.md)a přejde do podrobností ověřování na základě certifikátu v clusterech Service Fabric. Předpokládáme, že čtenář je obeznámen s základní koncepty zabezpečení a také s ovládacími prvky, které Service Fabric zveřejňuje řídit zabezpečení clusteru.  

Témata zahrnutá pod tímto názvem:

* Základy ověřování založené na certifikátech
* Identity a jejich příslušné role
* Pravidla konfigurace certifikátu
* Řešení potíží a nejčastější dotazy

## <a name="certificate-based-authentication-basics"></a>Základy ověřování založené na certifikátech
Jako stručný opakovací certifikát je v zabezpečení nástroj určený k vázání informací týkajících se entity (subjektu) s jejich vlastnictvím dvojice asymetrických kryptografických klíčů, a proto představuje základní konstrukci kryptografie s veřejným klíčem. Klíče reprezentované certifikátem lze použít k ochraně dat nebo k prokázání totožnosti držitelů klíčů. Při použití ve spojení se systémem infrastruktury veřejných klíčů (PKI) může certifikát představovat další znaky subjektu, například vlastnictví internetové domény, nebo určitá oprávnění, která mu udělil vystavitel certifikátu (označovaný jako certifikační úřad nebo certifikační úřad). Běžnou aplikací certifikátů je podpora kryptografického protokolu TLS (Transport Layer Security), který umožňuje zabezpečenou komunikaci prostřednictvím počítačové sítě. Konkrétně klient a server používají certifikáty k zajištění ochrany osobních údajů a integrity jejich komunikace a také k provádění vzájemného ověřování.

V Service Fabric, základní vrstva clusteru (Federace) také staví na TLS (mimo jiné protokoly) k dosažení spolehlivé, zabezpečené sítě zúčastněných uzlů. Připojení do clusteru prostřednictvím klientských api service fabric používají také TLS k ochraně provozu a také k vytvoření identity stran. Konkrétně při použití pro ověřování v Service Fabric, certifikát lze použít k prokázání následujících deklarací: a) předvádějící pověření certifikátu má vlastní soukromý klíč certifikátu b) hash SHA-1 certifikátu ("kryptografický tisk") odpovídá deklaraci obsažené v definici clusteru nebo c) rozlišující předmět obecný název certifikátu odpovídá prohlášení obsaženému v definici clusteru a vystavitel certifikátu je znám nebo důvěryhodný.

Ve výše uvedeném seznamu je "b" hovorově známé jako "připnutí otisku palce"; v tomto případě prohlášení odkazuje na konkrétní certifikát a síla schématu ověřování spočívá na předpokladu, že je výpočtově neproveditelné zfalšovat certifikát, který vytváří stejnou hodnotu hash jako jiný, zatímco ve všech ostatních ohledech je platným, dobře tvarovaným objektem. Položka "c" představuje alternativní formu prohlášení osvědčení, pokud síla režimu spočívá na kombinaci předmětu osvědčení a vydávajícího orgánu. V tomto případě se prohlášení vztahuje na třídu osvědčení - všechny dva certifikáty se stejnými vlastnostmi se považují za plně rovnocenné. 

V následujících částech podrobně vysvětlíte, jak runtime Service Fabric používá a ověřuje certifikáty k zajištění zabezpečení clusteru.

## <a name="identities-and-their-respective-roles"></a>Identity a jejich příslušné role
Před ponořením do podrobností ověřování nebo zabezpečení komunikačních kanálů je důležité uvést zúčastněné subjekty a odpovídající role, které hrají v clusteru:
- Service Fabric runtime, označované jako "systém": sada služeb, které poskytují abstrakce a funkce představující clusteru. Když se odkazuje na komunikaci v clusteru mezi instancemi systému, budeme používat termín "identita clusteru"; když odkazujeme na cluster jako na příjemce/cíl provozu mimo cluster, použijeme termín "identita serveru".
- hostované aplikace, označované jako "aplikace": kód poskytnutý vlastníkem clusteru, který je orchestrován a spouštěn v clusteru
- klienti: entity, které se mohou připojit k clusteru a spouštět je v clusteru podle konfigurace clusteru. Rozlišujeme mezi dvěma úrovněmi oprávnění - "uživatel" a "admin". Klient "uživatele" je omezen především na operace jen pro čtení (ale ne všechny funkce jen pro čtení), zatímco klient "správce" má neomezený přístup k funkcím clusteru. (Další podrobnosti naleznete [v rolích zabezpečení v clusteru Service Fabric](service-fabric-cluster-security-roles.md).)
- (Pouze Azure) service fabric služby služby, které orchestrovat a vystavit ovládací prvky pro provoz a správu clusterů Service Fabric, označované jako jednoduše "služba". V závislosti na prostředí může "služba" odkazovat na zprostředkovatele prostředků Azure Service Fabric nebo jiných poskytovatelů prostředků vlastněných a provozovaných týmem Service Fabric.

V zabezpečeném clusteru může být každá z těchto rolí nakonfigurována s vlastní, odlišnou identitou deklarovanou jako párování předdefinovaného názvu role a odpovídajícího pověření. Service Fabric podporuje deklarování pověření jako certifikáty nebo instanční objekt založený na doméně. (Identity založené na systému Windows/Kerberos jsou také podporovány, ale jsou nad rámec tohoto článku; viz [zabezpečení založené na systému Windows v clusterech Service Fabric](service-fabric-windows-cluster-windows-security.md).) V clusterech Azure mohou být role klientů deklarovány také jako [identity založené na službě Azure Active Directory](service-fabric-cluster-creation-setup-aad.md).

Jak je uvedeno výše, service fabric runtime definuje dvě úrovně oprávnění v clusteru: 'admin' a 'uživatel'. Klient správce a součást systému by fungovaly s oprávněními správce, a proto jsou od sebe navzájem nerozlišitelné. Po navázání připojení v clusteru nebo clusteru bude ověřenému volajícímu udělena runtime Service Fabric jedna ze dvou rolí jako základ pro následnou autorizaci. Ověření prozkoumáme podrobně v následujících částech.

## <a name="certificate-configuration-rules"></a>Pravidla konfigurace certifikátu
### <a name="validation-rules"></a>Ověřovací pravidla
Nastavení zabezpečení clusteru Service Fabric popisují v zásadě následující aspekty:
- typ ověřování; toto je vytvoření čas, neměnné charakteristika clusteru. Příklady takových nastavení jsou "ClusterCredentialType", "ServerCredentialType" a povolené hodnoty jsou "none", 'x509' nebo "windows". Tento článek se zaměřuje na ověřování typu x509.
- ověřovací pravidla (ověřování); Tato nastavení jsou nastavena vlastníkem clusteru a popisují, která pověření budou pro danou roli přijata. Příklady budou zkoumány do hloubky bezprostředně pod.
- nastavení používaná k vyladění nebo nenápadně změnit výsledek ověřování; Příklady zde zahrnují příznaky (de-)omezení vynucení seznamů odvolaných certifikátů atd.

> [!NOTE]
> Příklady konfigurace clusteru uvedené níže jsou výňatky z manifestu clusteru ve formátu XML, jako nejvíce stravitelný formát, který podporuje přímo service fabric funkce popsané v tomto článku. Stejná nastavení lze vyjádřit přímo v reprezentacích JSON definice clusteru, zda samostatný manifest clusteru json nebo šablonu Azure Resource Mangement.

Ověřovací pravidlo certifikátu se skládá z těchto prvků:
- odpovídající role: klient, admin klient (privilegovaná role)
- pověření přijaté pro roli, deklarované kryptografickým otiskem nebo běžným názvem subjektu

#### <a name="thumbprint-based-certificate-validation-declarations"></a>Prohlášení o ověření certifikátu založeného na kryptografickém tisku
V případě ověřovacích pravidel založených na kryptografickém tisku budou pověření prezentovaná volajícím požadujícím připojení v clusteru nebo clusteru ověřena následujícím způsobem:
  - pověření je platný, dobře tvarovaný certifikát: jeho řetěz může být sestaven, podpisy odpovídají
  - certifikát je platný čas (NotBefore <= nyní < NotAfter)
  - Hash SHA-1 certifikátu odpovídá deklaraci jako porovnání řetězců bez rozlišování velkých a malých písmen s výjimkou všech mezer

Jakékoli chyby důvěryhodnosti zjištěné během vytváření řetězu nebo ověřování budou potlačeny pro deklarace založené na kryptografickém tisku, s výjimkou certifikátů, jejichž platnost vypršela – i když pro tento případ existují i ustanovení. Konkrétně chyby související s: stav odvolání je neznámý nebo offline, nedůvěryhodný kořen, neplatné použití klíče, částečné řetězce jsou považovány za nezávažné chyby; předpokladem je v tomto případě, že certifikát je pouze obálka pro dvojici klíčů - zabezpečení spočívá v tom, že vlastník clusteru nastavil v místech opatření k ochraně soukromého klíče.

Následující výňatek z manifestu clusteru ilustruje takovou sadu ověřovacích pravidel založených na kryptografickém tisku:

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

Každá z výše uvedených položek odkazuje na konkrétní identitu, jak je popsáno výše; každá položka také umožňuje zadat více hodnot, jako seznam řetězců oddělených čárkami. V tomto příkladu po úspěšném ověření příchozí pověření, předvádějící certifikát s kryptografickýotisk SHA-1 'd5ec... 4264" bude přidělena role "správce"; naopak volající, který ověřuje s certifikátem '7c8f... 01b0' bude udělena role "uživatele", omezena především na operace jen pro čtení. Příchozí volající, který představuje certifikát, jehož kryptografický otisk je buď abcd... 1234" nebo "ef01... 5678' bude přijat jako partnerský uzel v clusteru. Nakonec klient, který se připojuje ke koncovému bodu správy clusteru, bude očekávat, že kryptografický otisk certifikátu serveru bude 'ef01... 5678'. 

Jak již bylo zmíněno dříve, Service Fabric dělá ustanovení pro přijímání certifikáty prošlé platností; Důvodem je, že certifikáty mají omezenou životnost a při deklarování kryptografickým otiskem (který odkazuje na konkrétní instanci certifikátu), povolení vypršení platnosti certifikátu bude mít za následek selhání připojení ke clusteru nebo přímé zhroucení clusteru. Je až příliš snadné zapomenout nebo zanedbávat otáčení certifikátu s otiskem palce a bohužel zotavení z takové situace je obtížné.

Za tímto účelem může vlastník clusteru výslovně uvést, že certifikáty podepsané svým držitelem deklarované kryptografickým otiskem se považují za platné, a to následovně:

```xml
  <Section Name="Security">
    <Parameter Name="AcceptExpiredPinnedClusterCertificate" Value="true" />
  </Section>
```
Toto chování se nevztahuje na certifikáty vydané certifikační autoritou. Pokud by tomu tak bylo, zrušený certifikát, jehož platnost, kterému byla známa, by se mohl stát "platným", jakmile by již nebyl uveden v seznamu odvolaných certifikátů certifikační autority, a představoval by tak bezpečnostní riziko. U certifikátů podepsaných svým držitelem je vlastník clusteru považován za jedinou stranu odpovědnou za zabezpečení soukromého klíče certifikátu, což není případ certifikátů vydaných certifikační autoritou – vlastník clusteru nemusí vědět, jak nebo kdy byl jejich certifikát prohlášen za ohrožený.

#### <a name="common-name-based-certificate-validation-declarations"></a>Prohlášení o ověření certifikátu na základě běžných názvů
Běžná deklarace založená na názvu mají jednu z následujících forem:
- předmět běžný název (pouze)
- předmět běžný název s připnutím vystavitel

Podívejme se nejprve na výňatek z manifestu clusteru, který ilustruje oba styly deklarací:
```xml
    <Section Name="Security/ServerX509Names">
      <Parameter Name="server.demo.system.servicefabric.azure-int" Value="" />
    </Section>
    <Section Name="Security/ClusterX509Names">
      <Parameter Name="cluster.demo.system.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```
Deklarace odkazují na identity serveru a clusteru; všimněte si, že deklarace založené na KN mají své vlastní oddíly v manifestu clusteru, oddělené od standardního "Zabezpečení". V obou deklaracích představuje "Název" rozlišující předmět běžný název certifikátu a pole Hodnota představuje očekávaného vystavitna takto:

- v prvním případě prohlášení uvádí, že společný název prvku rozlišující předmět certifikátu serveru se očekává, že bude odpovídat řetězec "server.demo.system.servicefabric.azure-int"; prázdné pole Hodnota označuje očekávání, že kořen řetězce certifikátů je důvěryhodný v uzlu/počítači, kde je certifikát serveru ověřován; v systému Windows to znamená, že certifikát může být zřetězen na libovolný certifikát nainstalovaný v úložišti Důvěryhodného kořenového certifikačního systému;
- v druhém případě prohlášení uvádí, že předkladatel certifikátu je přijat jako uzel partnera v clusteru, pokud běžný název certifikátu odpovídá řetězci "cluster.demo.system.servicefabric.azure-int" *a* kryptografický otisk přímého vystavittele certifikátu odpovídá jedné z položek oddělených čárkami v poli Hodnota. (Tento typ pravidla je hovorově označován jako "běžný název s připnutím vystavitelu".)

V obou případech je vytvořen řetěz certifikátu a očekává se, že bude bezchybný; to znamená, že chyby odvolání, částečné řetězové nebo časově neplatné chyby důvěryhodnosti jsou považovány za závažné a ověření certifikátu se nezdaří. Připnutí emitentů bude mít za následek zvážení stavu "nedůvěryhodného kořene" jako nezávažné chyby; navzdory vzhledu se jedná o přísnější formu ověření, protože umožňuje vlastníkovi clusteru omezit sadu autorizovaných/přijatých emitentů na vlastní zásady pkI.

Po vytvoření řetězu certifikátů je ověřen standardní zásadou TLS/SSL s deklarovaným subjektem jako vzdáleným názvem; certifikát bude považován za shodu, pokud jeho společný název předmětu nebo některý z jeho podmětových alternativních názvů odpovídá deklaraci Cn z manifestu clusteru. Zástupné znaky jsou podporovány v tomto případě a porovnávání řetězců je malá a velká písmena.

(Měli bychom objasnit, že výše popsaná sekvence by mohla být spuštěna pro každý typ použití klíče deklarované certifikátem; pokud certifikát určuje využití ověřovacího klíče klienta, je řetěz sestaven a vyhodnocen jako první pro roli klienta. V případě úspěchu je hodnocení dokončeno a ověření úspěšné. Pokud certifikát nemá využití ověřování klienta nebo ověření se nezdařilo, bude runtime Service Fabric sestavit a vyhodnotit řetěz pro ověřování serveru.)

Chcete-li dokončit příklad, následující výňatek ilustruje deklarování klientských certifikátů běžným názvem:
```xml
    <Section Name="Security/AdminClientX509Names">
      <Parameter Name="admin.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
    <Section Name="Security/ClientX509Names">
      <Parameter Name="user.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```

Výše uvedená prohlášení odpovídají identitám administrátorů a uživatelů; ověření certifikátů deklarovaných tímto způsobem je přesně tak, jak je popsáno v předchozích příkladech, certifikátů clusteru a serveru.

> [!NOTE]
> Deklarace založené na běžných názvech jsou určeny ke zjednodušení rotace a obecně ke správě certifikátů clusteru. Doporučujeme však dodržovat následující doporučení, abyste zajistili trvalou dostupnost a zabezpečení clusteru:
  * upřednostňovat připnutí vystavitena před spoléháním se na důvěryhodné kořeny
  * vyhnout se míchání emitentů z různých pki
  * zajistí, aby všichni očekávaní emitenti byli uvedeni v prohlášení o osvědčení; Nesouladný vystavitr bude mít za následek neúspěšné ověření
  * zajistit, aby koncové body zásad certifikátu PKI byly zjistitelné, dostupné a přístupné – to znamená, že koncové body AIA, CRL nebo OCSP jsou deklarovány na listu certifikátu a že jsou přístupné tak, aby bylo možné dokončit vytváření řetězů certifikátů.

Po přijetí žádosti o připojení v clusteru zabezpečeném certifikáty X.509 bude zaběhový čas Service Fabric používat nastavení zabezpečení clusteru k ověření pověření vzdálené strany, jak je popsáno výše; v případě úspěchu je volající/vzdálená strana považována za ověřenou. Pokud pověření odpovídá více ověřovacích pravidel, runtime udělí volajícímu nejvyšší privilegovanou roli libovolného z odpovídajících pravidel. 

### <a name="presentation-rules"></a>Pravidla prezentace
Předchozí část popisuje, jak ověřování funguje v clusteru zabezpečeném certifikátem. Tato část bude vysvětleno, jak samotný runtime Service Fabric zjišťuje a načítá certifikáty, které používá pro komunikaci v clusteru; nazýváme je "prezentační" pravidla.

Stejně jako u ověřovacích pravidel pravidla prezentace určují roli a přidružené deklarace pověření, vyjádřené buď kryptografickým otiskem nebo běžným názvem. Na rozdíl od ověřovacích pravidel deklarace založené na běžném názvu nemají ustanovení pro připnutí vystavitela; to umožňuje větší flexibilitu a lepší výkon. Pravidla prezentace jsou deklarována v části "NodeType" manifestu clusteru pro každý odlišný typ uzlu; nastavení jsou rozdělena z částí Zabezpečení clusteru, aby každý typ uzlu měl úplnou konfiguraci v jedné části. V clusterech Azure Service Fabric jsou deklarace certifikátu typu uzlu ve výchozím nastavení výchozí pro odpovídající nastavení v části Zabezpečení definice clusteru.

#### <a name="thumbprint-based-certificate-presentation-declarations"></a>Prohlášení o předložení certifikátu založenýna kryptografickým otiskem
Jak již bylo popsáno, za běhu Service Fabric rozlišuje mezi jeho role jako partner jiných uzlů v clusteru a jako server pro operace správy clusteru. V zásadě lze tato nastavení nakonfigurovat zřetelně, ale v praxi mají tendenci zarovnat. Pro zbytek tohoto článku budeme předpokládat, že nastavení zápas pro jednoduchost.

Podívejme se na následující výňatek z manifestu clusteru:
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
Element ClusterCertificate demonstruje úplné schéma, včetně volitelných parametrů ('X509FindValueSecondary') nebo parametrů s příslušnými výchozími hodnotami ('X509StoreName'); ostatní prohlášení vykazují zkrácený formulář. Výše uvedená deklarace certifikátu clusteru uvádí, že nastavení zabezpečení uzlů typu nt1vm jsou inicializována certifikátem cc71. 1984" jako primární a "49e2.. osvědčení 19d6 jako druhotné osvědčení; očekává se, že oba certifikáty\'budou nalezeny v úložišti certifikátů LocalMachine My (nebo ekvivalentní cestě Linuxu *var/lib/sfcerts).*

#### <a name="common-name-based-certificate-presentation-declarations"></a>Běžná prohlášení o předložení certifikátu založeného na názvu
Certifikáty typu uzlu mohou být také deklarovány podle společného názvu subjektu, jak je uvedeno níže:

```xml
  <NodeTypes>
    <NodeType Name="nt1vm">
      <Certificates>
        <ClusterCertificate X509FindType="FindBySubjectName" X509FindValue="demo.cluster.azuredocpr.system.servicefabric.azure-int" Name="ClusterCertificate" />
      </Certificates>
    </NodeType>
  </NodeTypes>
```

Pro oba typy deklarací uzel Service Fabric bude číst konfiguraci při spuštění, vyhledejte a načtěte zadané certifikáty a seřadí je v sestupném pořadí podle jejich atributu NotAfter; certifikáty s ukončenou platností jsou ignorovány a první prvek seznamu je vybrán jako pověření klienta pro jakékoli připojení Service Fabric, o které se tento uzel pokusil. (Service Fabric ve skutečnosti upřednostňuje nejvzdálenější certifikát s končící platností.)

Všimněte si, že pro běžné názvy na základě prohlášení prezentace certifikát je považován za shodu, pokud jeho předmět běžný název se rovná X509FindValue (nebo X509FindValueSecondary) pole deklarace jako případ ověnčení, přesné porovnání řetězců. To je v rozporu s ověřovacípravidla, která podporuje porovnávání zástupných symbolů, stejně jako porovnání řetězců bez rozlišování velkých a malých písmen.  

### <a name="miscellaneous-certificate-configuration-settings"></a>Různá nastavení konfigurace certifikátu
Dříve bylo zmíněno, že nastavení zabezpečení clusteru Service Fabric také umožňují jemně měnit chování ověřovacího kódu. Zatímco článek o [nastavení clusteru Service Fabric](service-fabric-cluster-fabric-settings.md) představuje komplexní a nejaktuálnější seznam nastavení, rozbalíme význam několika vybraných nastavení zabezpečení, abychom dokončili úplné vystavení ověřování na základě certifikátu. Pro každé nastavení vysvětlíme záměr, výchozí hodnotu/chování, jak ovlivňuje ověřování a které hodnoty jsou přijatelné.

Jak již bylo zmíněno, ověření certifikátu vždy znamená vytváření a vyhodnocování řetězce certifikátu. U certifikátů vydaných certifikační autoritou toto zdánlivě jednoduché volání rozhraní API operačního systému obvykle zahrnuje několik odchozích volání různých koncových bodů vystavujícího pki, ukládání odpovědí do mezipaměti a tak dále. Vzhledem k výskytu volání ověření certifikátu v clusteru Service Fabric mohou mít všechny problémy v koncových bodech infrastruktury infrastruktury infrastruktury za následek nižší dostupnost clusteru nebo přímé rozdělení. Zatímco odchozí volání nelze potlačit (další informace naleznete v části Nejčastější dotazy), následující nastavení lze použít k zamaskování chyb ověření způsobených selháním volání seznamu odvolaných certifikátů.

  * CrlCheckingFlag - v části "Zabezpečení" řetězec převeden na UINT. Hodnota tohoto nastavení se používá Service Fabric maskovat chyby stavu řetězu certifikátu změnou chování řetězu; je předán a Win32 CryptoAPI [CertGetCertificateChain](https://docs.microsoft.com/windows/win32/api/wincrypt/nf-wincrypt-certgetcertificatechain) volání jako 'dwFlags' parametr a lze nastavit na libovolnou platnou kombinaci příznaků přijatých funkce. Hodnota 0 vynutí service fabric runtime ignorovat všechny chyby stavu důvěryhodnosti - to se nedoporučuje, protože jeho použití by představovalo významné ohrožení zabezpečení. Výchozí hodnota je 0x40000000 (CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT).

  Použití: pro místní testování s certifikáty podepsanými svým držitelem nebo vývojářskými certifikáty, které nejsou plně vytvořeny/nemají správnou infrastrukturu veřejných klíčů pro podporu certifikátů. Může také použít jako zmírnění v prostředích se vzduchovými mezerami během přechodu mezi pkI.

  Použití: vezmeme si příklad, který vynutí kontrolu odvolání pouze pro přístup k adresám URL uložených v mezipaměti. Za předpokladu:
  ```C++
  #define CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY         0x80000000
  ```
  pak se deklarace v manifestu clusteru stane:
  ```xml
    <Section Name="Security">
      <Parameter Name="CrlCheckingFlag" Value="0x80000000" />
    </Section>
  ```

  * IgnorCrlOfflineError - v části "Zabezpečení" logická hodnota s výchozí hodnotou 'false'. Představuje zástupce pro potlačení stavu chyby řetězu odvolání offline (nebo následného stavu chyby ověření zásady řetězce).

  Použití: místní testování nebo s vývojářskými certifikáty, které nejsou podporovány správnou místní sadou PKI. Používejte jako zmírnění v prostředích se vzduchovým mezerami nebo v případě, že je známo, že je pki nedostupná.

  Jak používat:
  ```xml
    <Section Name="Security">
      <Parameter Name="IgnoreCrlOfflineError" Value="true" />
    </Section>
  ```

  Další významná nastavení (vše v části "Bezpečnost"):
  * AcceptExpiredPinnedClusterCertificate - popsáno v části věnované ověření certifikátu založeného na kryptografickém tisku; umožňuje přijímat certifikáty clusteru s vlastním podpisem, jejichž platnost vypršela. 
  * CertificateExpirySafetyMargin - interval, vyjádřený v minutách před časovým razítkem NotAfter certifikátu a během kterého je certifikát považován za rizikový pro vypršení platnosti. Service Fabric monitoruje certifikáty clusteru a pravidelně vydává zprávy o stavu o jejich zbývající dostupnosti. V intervalu "bezpečnosti" jsou tyto zdravotní zprávy zvýšeny na stav "varování". Výchozí hodnota je 30 dní.
  * CertificateHealthReportingInterval - řídí četnost zpráv o stavu týkajících se zbývající časové platnosti certifikátů clusteru. Sestavy budou vyzařovány pouze jednou v tomto intervalu. Hodnota je vyjádřena v sekundách s výchozím nastavením 8 hodin.
  * EnforcePrevalidationOnSecurityChanges - logická, řídí chování upgradu clusteru při zjišťování změn nastavení zabezpečení. Pokud je nastavena na hodnotu "true", upgrade clusteru se pokusí zajistit, že alespoň jeden z certifikátů odpovídajících některému z pravidel prezentace může projít odpovídajícím ověřovacím pravidlem. Předběžné ověření je provedeno před použitím nového nastavení na libovolný uzel, ale běží pouze na uzlu hostujícím primární repliku služby Cluster Manager v době zahájení inovace. Od tohoto psaní nastavení má výchozí 'false' a bude nastavena na 'true' pro nové clustery Azure Service Fabric s runtime verze začínající 7.1.
 
### <a name="end-to-end-scenario-examples"></a>Scénář od konce na konec (příklady)
Podívali jsme se na pravidla prezentace, ověřovací pravidla a vyladění vlajek, ale jak to všechno funguje společně? V této části budeme pracovat prostřednictvím dvou příkladů end-to-end demonstrovat, jak nastavení zabezpečení lze využít pro bezpečné upgrady clusteru. Všimněte si, že to není určen jako komplexní disertační práce o správné správě certifikátů v Service Fabric, vyhledejte doprovodný článek na toto téma.

Oddělení pravidel pro prezentaci a validaci představuje zjevnou otázku (nebo obavu), zda se mohou lišit a jaké by byly důsledky. Je skutečně možné, že výběr uzlu ověřovacícertifikát neprojde ověřovací pravidla jiného uzlu. Ve skutečnosti je tato nesrovnalost primární příčinou incidentů souvisejících s ověřováním. Oddělení těchto pravidel zároveň umožňuje clusteru pokračovat v provozu během upgradu, který změní nastavení zabezpečení clusteru. Zvažte, že rozšířením nejprve ověřovací pravidla jako první krok, všechny uzly clusteru se sbíhají na nové nastavení při stále pomocí aktuální pověření. 

Připomeňme si, že v clusteru Service Fabric upgradu postupuje prostřednictvím (až 5) "upgradovací domény" nebo UD. V daném okamžiku jsou upgradovány nebo měněny pouze uzly v aktuálním ud a upgrade bude pokračovat na další ud pouze v případě, že to umožňuje dostupnost clusteru. (Další podrobnosti naleznete v [článku upgrady clusteru Service Fabric](service-fabric-cluster-upgrade.md) a další články na stejné téma.) Změny certifikátu/zabezpečení jsou obzvláště riskantní, protože mohou izolovat uzly od clusteru nebo ponechat cluster na okraji ztráty kvora.

K popisu nastavení zabezpečení uzlu použijeme následující zápis:

Nk: {P:{TP=A}, V:{TP=A}}, kde:
  - 'Nk' představuje uzel v upgradu domény *k*
  - 'P' představuje aktuální pravidla prezentace uzlu (za předpokladu, že odkazujeme pouze na clusterové certifikáty); 
  - 'V' představuje aktuální ověřovací pravidla uzlu (pouze certifikát clusteru)
  - "TP=A" představuje prohlášení založené na kryptografickém otisku (TP), přičemž "A" je kryptografický otisk certifikátu
  - "CN=B" představuje běžné deklarace založené na názvu (CN), přičemž "B" je obecný název předmětu certifikátu 

#### <a name="rotating-a-cluster-certificate-declared-by-thumbprint"></a>Otočení clusterového certifikátu deklarovaného kryptografickým otiskem
Následující sekvence popisuje, jak lze dvoufázový upgrade použít k bezpečnému zavedení sekundárního clusterového certifikátu deklarovaného kryptografickým otiskem. první fáze zavede nové prohlášení o certifikátu do pravidel pro validaci a druhá fáze ji zavede do pravidel prezentace:
  - počáteční stav: N0 = {P:{TP=A}, V:{TP=A}}, ... Nk = {P:{TP=A}, V:{TP=A}} - cluster je v klidovém stavu, všechny uzly sdílejí společnou konfiguraci
  - po dokončení upgradu domény 0: N0 = {P:{TP=A}, V:{TP=A, TP=B}}, ... Nk = {P:{TP=A}, V:{TP=A}} - uzly v UD0 budou prezentovat certifikát A a přijímat certifikáty A nebo B; všechny ostatní uzly přítomné a přijímají pouze certifikát A
  - po dokončení poslední domény upgradu: N0 = {P:{TP=A}, V:{TP=A, TP=B}}, ... Nk = {P:{TP=A}, V:{TP=A, TP=B}} - všechny uzly představují certifikát A, všechny uzly by přijaly buď certifikát A, nebo B
      
V tomto okamžiku je cluster opět v rovnováze a může být zahájena druhá fáze nastavení zabezpečení upgradu/změny:
  - po dokončení upgradu domény 0: N0 = {P:{TP=A, TP=B}, V:{TP=A, TP=B}}, ... Nk = {P:{TP=A}, V:{TP=A, TP=B}} - uzly v UD0 začnou prezentovat B, který je přijat jiným uzlem v clusteru.
  - po dokončení poslední domény upgradu: N0 = {P:{TP=A, TP=B}, V:{TP=A, TP=B}}, ... Nk = {P:{TP=A, TP=B}, V:{TP=A, TP=B}} - všechny uzly přešly na předváděcí certifikát B. Certifikát A lze nyní vyřadit nebo odebrat z definice clusteru s následnou sadou upgradů.

#### <a name="converting-a-cluster-from-thumbprint--to-common-name-based-certificate-declarations"></a>Převod clusteru z kryptografický otisk na deklarace certifikátů založených na běžném názvu
Podobně změna typu deklarace certifikátu (z kryptografického potisku na běžný název) bude následovat stejný vzor jako výše. Všimněte si, že ověřovací pravidla umožňují deklarovat certifikáty dané role kryptografickým otiskem i běžným názvem ve stejné definici clusteru. Naproti tomu pravidla prezentace umožňují pouze jednu formu prohlášení. Mimochodem bezpečný přístup k převodu certifikátu clusteru z kryptografický otisk na běžný název je zavést zamýšlený cílový certifikát nejprve kryptografickým otiskem a potom změnit tuto deklaraci na běžný název. V následujícím příkladu budeme předpokládat, že kryptografický otisk "A" a běžný název předmětu "B" odkazují na stejný certifikát. 

  - počáteční stav: N0 = {P:{TP=A}, V:{TP=A}}, ... Nk = {P:{TP=A}, V:{TP=A}} - cluster je v klidovém stavu, všechny uzly sdílejí společnou konfiguraci, přičemž A je primární kryptografický otisk certifikátu
  - po dokončení upgradu domény 0: N0 = {P:{TP=A}, V:{TP=A, CN=B}}, ... Nk = {P:{TP=A}, V:{TP=A}} - uzly v UD0 budou prezentovat certifikát A a přijímat certifikáty s kryptografickým otiskem A nebo běžným názvem B; všechny ostatní uzly přítomné a přijímají pouze certifikát A
  - po dokončení poslední domény upgradu: N0 = {P:{TP=A}, V:{TP=A, CN=B}}, ... Nk = {P:{TP=A}, V:{TP=A, CN=B}} - všechny uzly představují certifikát A, všechny uzly by přijaly buď certifikát A (TP) nebo B (CN)

V tomto okamžiku můžeme pokračovat ve změně pravidel prezentace s následným upgradem:
  - po dokončení upgradu domény 0: N0 = {P:{CN=B}, V:{TP=A, CN=B}}, ... Nk = {P:{TP=A}, V:{TP=A, CN=B}} - uzly v UD0 budou prezentovat certifikát B nalezený cn a přijímat certifikáty s kryptografickým otiskem A nebo běžným názvem B; všechny ostatní uzly, které jsou přítomny a přijímají pouze certifikát A, vybrané kryptografickým otiskem
  - po dokončení poslední domény upgradu: N0 = {P:{CN=B}, V:{TP=A, CN=B}}, ... Nk = {P:{CN=B}, V:{TP=A, CN=B}} - všechny uzly přítomné certifikátu B nalezenécnu, všechny uzly by přijaly buď certifikát A (TP) nebo B (CN)
    
Dokončení fáze 2 také označuje převod clusteru na certifikáty založené na běžných názvech; prohlášení ověření založená na kryptografickém tisku lze odebrat při následné inovaci clusteru.

> [!NOTE]
> V clusterech Azure Service Fabric jsou výše uvedené pracovní postupy řízeny zprostředkovatelem prostředků Service Fabric; vlastník clusteru je stále zodpovědný za zřizování certifikátů do clusteru podle uvedených pravidel (prezentace nebo ověření) a doporučuje se provádět změny ve více krocích.

V samostatném článku se budeme zabývat tématem správy a zřizování certifikátů do clusteru Service Fabric.

## <a name="troubleshooting-and-frequently-asked-questions"></a>Řešení potíží a nejčastější dotazy
Zatímco ladění problémů souvisejících s ověřováním v clusterech Service Fabric není snadné, doufáme, že následující rady a tipy mohou pomoci. Nejjednodušší způsob, jak začít vyšetřování je prozkoumat protokoly událostí Service Fabric na uzly clusteru - nemusí být nutně pouze ty, které vykazují příznaky, ale také uzly, které jsou nahoru, ale nejsou schopny se připojit k jednomu ze svých sousedů. V systému Windows jsou významné události obvykle protokolovány pod kanály "Protokoly aplikací a služeb\Microsoft-ServiceFabric\Admin" nebo "Provozní". Někdy to může být užitečné, aby [CAPI2 protokolování](https://docs.microsoft.com/archive/blogs/benjaminperkins/enable-capi2-event-logging-to-troubleshoot-pki-and-ssl-certificate-issues), zachytit více podrobností o ověření certifikátu, načítání CRL / CTL atd.(Nezapomeňte zakázat po dokončení repro, to může být docela podrobné.)

Typické příznaky, které se projevují v clusteru, který má problémy s ověřováním, jsou: 
  - uzly jsou dole / jízda na kole 
  - Pokusy o připojení jsou odmítnuty
  - Pokusy o připojení vypršel

Každý z příznaků může být způsoben různými problémy a stejná příčina může vykazovat různé projevy; jako takový, budeme jen seznam malý vzorek typických problémů, s doporučeními pro jejich opravu. 

* Uzly mohou vyměňovat zprávy, ale nemohou se připojit. Možnou příčinou pokusů o ukončení připojení je chyba "certifikát není spárován" – jedna ze stran v připojení service fabric představuje certifikát, který neplní ověřovací pravidla příjemce. Může být doprovázena některou z těchto chyb: 
  ```C++
  0x80071c44    -2147017660 FABRIC_E_SERVER_AUTHENTICATION_FAILED
  ```
  Chcete-li dále diagnostikovat/prozkoumat: na každém z uzlů, které se pokoušejí o připojení, určete, který certifikát je předkládán; zkontrolujte certifikát a pokuste se emulovat ověřovací pravidla (zkontrolujte kryptografický otisk nebo rovnost běžného názvu, zkontrolujte kryptografické otisky vystavitela, pokud je zadáno).

  Dalším běžným doprovodným kódem chyby může být:
  ```C++
  0x800b0109    -2146762487 CERT_E_UNTRUSTEDROOT
  ```
  V tomto případě je certifikát deklarován pod běžným názvem a platí jedna z těchto možností:
    - vystavitelé nejsou připnuti a kořenový certifikát není důvěryhodný, nebo
    - emitenti jsou připnuti, ale prohlášení neobsahuje kryptografický otisk přímého vystavittele tohoto certifikátu

* Uzel je nahoru, ale nelze se připojit k jiným uzlům; jiné uzly nepřijímají příchozí provoz z uzlu selhání. V tomto případě je možné, že načítání certifikátu se nezdaří na místním uzlu. Vyhledejte následující chyby:
  - Certifikát nebyl nalezen – ujistěte se, že certifikáty deklarované v pravidlech prezentace lze přeložit podle obsahu úložiště certifikátů LocalMachine\My (nebo jak je uvedeno). 
    Možné příčiny selhání mohou zahrnovat: 
      - neplatné znaky v deklaraci kryptografického otisku
      - certifikát není nainstalován
      - platnost certifikátu vypršela
      - deklarace běžného názvu obsahuje předponu "CN="
      - deklarace určuje zástupný znak a v úložišti certifikátů neexistuje žádná přesná shoda (prohlášení: CN=*.mydomain.com, skutečný certifikát: CN=server.mydomain.com)

  - neznámá pověření - označuje buď chybějící soukromý klíč odpovídající certifikátu, obvykle doplněný kódem chyby: 
    ```C++ 
    0x8009030d  -2146893043 SEC_E_UNKNOWN_CREDENTIALS
    0x8009030e  -2146893042 SEC_E_NO_CREDENTIALS
    ```
    Chcete-li nápravu, zkontrolujte existenci soukromého klíče; ověřte, zda je sfadminům udělen přístup k soukromému klíči.verify SFAdmins is granted 'read|execute' access to the private key.

  - chybný typ poskytovatele - označuje certifikát Crypto New Generation (CNG) ("Zprostředkovatel úložiště softwarových klíčů společnosti Microsoft"); v tomto okamžiku Service Fabric podporuje pouze certifikáty CAPI1. Obvykle doplněno kódem chyby:
    ```C++
    0x80090014  -2146893804 NTE_BAD_PROV_TYPE
    ```
    Chcete-li nápravu, znovu vytvořit certifikát clusteru pomocí CAPI1 (např. "Microsoft Enhanced RSA a AES Cryptographic Provider") poskytovatele. Další podrobnosti o poskytovatelích kryptografických služeb naleznete [v části Principy poskytovatelů kryptografických služeb](https://docs.microsoft.com/windows/win32/seccertenroll/understanding-cryptographic-providers)

