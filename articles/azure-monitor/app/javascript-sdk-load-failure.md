---
title: Řešení potíží s načtením sady SDK pro webové aplikace v jazyce JavaScript – Azure Application Insights
description: Řešení potíží se selháním načtení sady SDK pro webové aplikace v jazyce JavaScript
ms.topic: conceptual
author: MSNev
ms.author: newylie
ms.date: 06/05/2020
ms.custom: devx-track-js
ms.openlocfilehash: 30c7caef4143b1a7cdba959971ff7689f986cb9e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91333252"
---
# <a name="troubleshooting-sdk-load-failure-for-javascript-web-apps"></a>Řešení potíží s načtením sady SDK pro webové aplikace v jazyce JavaScript

<!-- 
Editor Note: This link name above "SDK Load Failure" has a direct references by https://go.microsoft.com/fwlink/?linkid=2128109 which is embedded in the snippet and from the JavaScript Page. If you change this text you MUST also update these references.
-->

Výjimka selhání načtení sady SDK je vytvořena a hlášena fragmentem kódu jazyka JavaScript (V3 nebo novější), když zjistí, že se nepovedlo stáhnout nebo inicializovat skript SDK. Simplistically, klient pro koncové uživatele (prohlížeč) nemohl stáhnout sadu Application Insights SDK nebo inicializovat z identifikované hostující stránky, a proto nebudou hlášeny žádné telemetrie ani události.

![Azure Portal – přehled selhání prohlížeče](./media/javascript-sdk-load-failure/overview.png)

> [!NOTE]
> Tato výjimka je podporovaná ve všech hlavních prohlížečích, které podporují rozhraní API Fetch () nebo XMLHttpRequest. Tím se vyloučí IE 8 a nižší, takže nebudete tento typ výjimky nahlášený z těchto prohlížečů (Pokud vaše prostředí neobsahuje výplň pro načtení).

![Podrobnosti o výjimce prohlížeče](./media/javascript-sdk-load-failure/exception.png)

Podrobnosti zásobníku zahrnují základní informace o adresách URL používaných koncovým uživatelem.

| Název                      | Popis                                                                                                  |
|---------------------------|--------------------------------------------------------------------------------------------------------------|
| &lt;&nbsp;Koncový bod CDN&gt; | Adresa URL, která byla použita (a nebyla úspěšná) pro stažení sady SDK.                                                      |
| &lt;Odkaz na Help &nbsp;&gt;    | Adresa URL, která odkazuje na dokumentaci k řešení potíží (Tato stránka).                                              |
| &lt;&nbsp;Adresa URL hostitele&gt;     | Úplná adresa URL stránky, kterou použil koncový uživatel.                                                    |
| &lt;Adresa URL koncového bodu &nbsp;&gt; | Adresa URL, která byla použita k nahlášení výjimky, může být užitečná při identifikaci, zda se stránka hostování přistupovala z veřejného Internetu nebo privátního cloudu.

Nejběžnější důvody, kterými se tato výjimka vyskytuje:

- Přerušované výpadky síťového připojení.
- Application Insights výpadku CDN.
- Inicializace sady SDK po načtení skriptu se nezdařila.
- Application Insights JavaScript CDN je zablokované.

[Občasné selhání připojení k síti](#intermittent-network-connectivity-failure) je nejběžnějším důvodem pro tuto výjimku, zejména ve scénářích mobilního roamingu, kdy uživatelé ztratí připojení k síti přerušovaně.

V následujících částech se dozvíte, jak řešit potíže s každou možnou hlavní příčinou této chyby.

> [!NOTE]
> Několik kroků při řešení potíží předpokládá, že vaše aplikace má přímou kontrolu nad &lt; skriptem fragmentu nebo &gt; značkou a její konfigurací, která se vrací jako součást hostitelské stránky HTML. Pokud to neuděláte, nebudou pro váš scénář platit žádné identifikované kroky.

## <a name="intermittent-network-connectivity-failure"></a>Chyba přerušovaného připojení k síti

**Pokud uživatel má přerušované výpadky síťového připojení, je k dispozici méně možných řešení a obecně se vyřeší v krátké době.** Pokud uživatel například znovu načte web (aktualizuje stránku), soubory budou (nakonec) staženy a ukládány do mezipaměti místně, dokud nebude uvolněna aktualizovaná verze.

> [!NOTE]
> Pokud je tato výjimka trvalá a probíhá napříč spoustou uživatelů (diagnostikovaná rychlou a trvalou úrovní této výjimky) a omezením normální telemetrie klientů, pak občasné problémy se síťovým připojením _nemusí_ být skutečným důvodem problému a měli byste pokračovat v diagnostice s dalšími známými možnými problémy.

V případě, že je tato situace hostující sadu SDK ve vaší vlastní síti CDN pravděpodobně neposkytuje nebo neomezuje výskyt této výjimky, protože váš vlastní CDN bude ovlivněn stejným problémem.

Totéž platí také při použití sady SDK prostřednictvím řešení NPM Packages. Nicméně od perspektivy koncových uživatelů v případě, že dojde k tomu, že se celá aplikace nepovede načíst/inicializovat (spíše než _jenom_ sadu SDK telemetrie, kterou nevidí vizuálně), bude pravděpodobně aktualizovat vaši lokalitu, dokud nebude úplně načtena.

Můžete také zkusit použít [balíčky npm](#use-npm-packages-to-embed-the-application-insight-sdk) k vložení sady SDK Application Insights.

Aby se minimalizovala přerušovaná Chyba připojení k síti, implementovali jsme do všech souborů CDN hlavičky pro řízení mezipaměti, takže jakmile prohlížeč koncového uživatele stáhne aktuální verzi sady SDK, nebude nutné ho znovu stáhnout a prohlížeč znovu použije dříve získanou kopii (viz [Jak funguje ukládání do mezipaměti](../../cdn/cdn-how-caching-works.md)). Pokud se kontrolu ukládání do mezipaměti nepovede nebo k ní dojde v nové verzi, bude si muset stáhnout aktualizovanou verzi v prohlížeči koncového uživatele. Takže se ve scénáři selhání při kontrole nebo v dočasném zásobníku může zobrazit _úroveň na pozadí_ , když dojde k nové vydaným verzím, která je všeobecně dostupná (nasazená do sítě CDN).
 
## <a name="application-insights-cdn-outage"></a>Výpadek sítě CDN Application Insights

Pokud se pokusíte o přístup ke koncovému bodu CDN přímo z prohlížeče ( https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js) za předpokladu, že vaše organizace tuto doménu nezablokovala), můžete potvrdit, že dojde k výpadku Application Insights sítě CDN.

Pokud ověříte, že dojde k výpadku, můžete [vytvořit novou lístek podpory](https://azure.microsoft.com/support/create-ticket/) nebo se pokusit změnit adresu URL použitou ke stažení sady SDK.

### <a name="change-the-cdn-endpoint"></a>Změna koncového bodu CDN
  
V případě, že se fragment a jeho konfigurace vrátí v rámci vaší aplikace jako součást každé generované stránky, můžete změnit konfiguraci fragmentu, `src` aby pro sadu SDK používala jinou adresu URL. Pomocí tohoto přístupu můžete obejít problém blokovaný CDN, protože nová adresa URL by neměla být blokovaná.

Aktuální koncové body CDN sady JavaScript SDK pro Application Insights
- `https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js`
- `https://js.monitor.azure.com/scripts/b/ai.2.min.js`

> [!NOTE]
> `https://js.monitor.azure.com/`Koncový bod je alias, který umožňuje přepínat mezi poskytovateli CDN během přibližně 5 minut, aniž byste museli měnit konfiguraci. To nám umožní opravit zjištěné problémy související s CDN rychleji, pokud poskytovatel CDN má místní nebo globální problémy, aniž by musel upravovat nastavení.

## <a name="sdk-failed-to-initialize-after-loading-the-script"></a>Inicializace sady SDK po načtení skriptu se nezdařila.

Když se sada SDK nepodaří inicializovat, &lt; skript nebo &gt; se úspěšně STÁHL z CDN, ale během inicializace dojde k chybě. To může být způsobeno chybějícími nebo neplatnými závislostmi nebo nějakou formou výjimky jazyka JavaScript.

První věc, kterou je třeba kontrolovat, je, jestli se sada SDK úspěšně stáhla, pokud se skript nestáhl, __ale__ tento scénář není příčinou výjimky selhání načtení sady SDK.

Rychlá kontrola: pomocí prohlížeče, který podporuje nástroje pro vývojáře (F12), ověřte na kartě síť, že se skript definovaný v ```src``` konfiguraci fragmentů stáhl s kódem odpovědi 200 (úspěch) nebo 304 (nezměněno). K revizi síťového provozu můžete použít také nástroj, jako je Fiddler.

Následující části obsahují různé možnosti vytváření sestav, doporučí si buď vytvoření lístku podpory, nebo vyvolání problému na GitHubu.

 Základní pravidla vytváření sestav:

- Pokud se problém týká pouze malého počtu uživatelů a konkrétní nebo podmnožiny verze prohlížeče (Zkontrolujte podrobnosti o hlášené výjimce), je pravděpodobné, že vaše aplikace bude pravděpodobně vyžadovat další `polyfill` implementace. Pro tyto soubory zapište [problém na GitHubu](https://github.com/Microsoft/ApplicationInsights-JS/issues).
- Pokud se tento problém týká vaší celé aplikace a všech uživatelů, které jsou ovlivněné, je pravděpodobné, že se jedná o problém související s verzí, a proto byste měli [vytvořit nový lístek podpory](https://azure.microsoft.com/support/create-ticket/).

### <a name="javascript-exceptions"></a>JavaScript – výjimky

Nejprve umožňuje vyhledat výjimky JavaScriptu pomocí prohlížeče, který podporuje nástroje pro vývojáře (F12), načíst stránku a zkontrolovat, zda došlo k výjimkám.

Pokud jsou v rámci skriptu sady SDK hlášeny výjimky (například ai.2.min.js), může to znamenat, že konfigurace předaná do sady SDK obsahuje neočekávanou nebo chybějící požadovanou konfiguraci nebo byla do sítě CDN nasazena vadná verze.

Chcete-li zjistit chybnou konfiguraci, změňte konfiguraci předanou do fragmentu (Pokud ještě není), aby obsahovala pouze klíč instrumentace jako řetězcovou hodnotu.

> src: " https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js ",<br />
> cfg: {<br />
> instrumentationKey: "INSTRUMENTATION_KEY"<br />
> }});<br />

Pokud při použití této minimální konfigurace stále zobrazujete výjimku JavaScriptu ve skriptu SDK, [vytvořte nový lístek podpory](https://azure.microsoft.com/support/create-ticket/) , který bude vyžadovat vrácení vadného sestavení zpátky, protože se pravděpodobně jedná o problém s nově nasazenou verzí.

Pokud výjimka zmizí, pak problém je pravděpodobně způsoben neshodou typu nebo neočekávanou hodnotou. Začněte přidávat konfigurační možnosti zpátky jednou a testovat, dokud nedojde k výjimce znovu. Pak vyhledejte v dokumentaci položku, která tento problém způsobila. Pokud je dokumentace nejasná nebo potřebujete pomoc, zapište [problém na GitHubu](https://github.com/Microsoft/ApplicationInsights-JS/issues).

Pokud byla konfigurace dříve nasazena a pracovala, ale právě začala nahlásit tuto výjimku, může se jednat o problém s nově nasazenou verzí, ověřit, jestli má vliv jenom na malou skupinu uživatelů nebo prohlížečů, a jak [zaslat problém na GitHubu](https://github.com/Microsoft/ApplicationInsights-JS/issues) nebo  [vytvořit nový lístek podpory](https://azure.microsoft.com/support/create-ticket/).

### <a name="enable-console-debugging"></a>Povolit ladění konzoly

Za předpokladu, že nejsou vyvolány žádné výjimky, je dalším krokem povolení ladění konzoly přidáním `loggingLevelConsole` nastavení do konfigurace, které pošle všechny chyby a upozornění inicializace do konzoly prohlížeče (obvykle dostupné prostřednictvím vývojářských nástrojů (F12)). Všechny hlášené chyby by měly být vyjasněné a pokud potřebujete další pomoc [se souborem na GitHubu](https://github.com/Microsoft/ApplicationInsights-JS/issues).

> cfg: {<br />
> instrumentationKey: "INSTRUMENTATION_KEY",<br />
> loggingLevelConsole: 2<br />
> }});<br />

> [!NOTE]
> Během inicializace sada SDK provádí některé základní kontroly známých hlavních závislostí. Pokud nejsou poskytovány aktuálním modulem runtime, nahlásí selhání do konzoly jako varovné zprávy, ale pouze v případě, že `loggingLevelConsole` je hodnota větší než nula.

Pokud se ještě nedaří inicializovat, zkuste povolit ```enableDebug``` nastavení konfigurace. Tím dojde k vyvolání všech vnitřních chyb jako výjimky (což způsobí ztrátu telemetrie). Vzhledem k tomu, že se jedná o nastavení jenom pro vývojáře, bude pravděpodobně mít problémy s výjimkami, které se vyvolají jako součást některých interních kontrol, takže budete muset zkontrolovat každou výjimku, abyste zjistili, který problém způsobuje selhání sady SDK. Použijte verzi skriptu bez minifikovaného (Všimněte si, že rozšíření je ". js" a nikoli ".min.js") jinak budou výjimky nečitelná.

> [!WARNING]
> Toto je nastavení jenom pro vývojáře a nikdy by nemělo být povolené v plném provozním prostředí, protože ztratíte telemetrii.

> src: " https://az416426.vo.msecnd.net/scripts/b/ai.2.js ",<br />
> cfg: {<br />
> instrumentationKey: "INSTRUMENTATION_KEY",<br />
> enableDebug: true<br />
> }});<br />

Pokud to ještě neposkytuje žádné přehledy, měli byste [na GitHubu zaslat nějaký problém](https://github.com/Microsoft/ApplicationInsights-JS/issues) s podrobnostmi a ukázkovým webem, pokud ho máte. K identifikaci problému můžete použít podrobnosti o verzi prohlížeče, operačním systému a rozhraní JS.

## <a name="the-application-insights-javascript-cdn-has-been-blocked"></a>Rozhraní Application Insights JavaScript CDN bylo zablokováno.

Blokovaná síť CDN je možná v případě, že byl hlášen koncový bod služby Application Insights JavaScript SDK CDN nebo identifikován jako nebezpečný. Pokud k tomu dojde, bude koncový bod v seznamu veřejně blokovaný a příjemci těchto seznamů začnou blokovat veškerý přístup.

Pro vyřešení vyžaduje vlastník koncového bodu CDN, aby fungoval s entitou se seznamem blokovaných bodů, která je označena jako nebezpečná, aby ji bylo možné odebrat z příslušného seznamu.

Zkontroluje, jestli byl koncový bod CDN označený jako nebezpečný.
- [Sestava transparentnosti Google](https://transparencyreport.google.com/safe-browsing/search)
- [VirusTotal](https://www.virustotal.com/gui/home/url)
- [Sucuri SiteCheck](https://sitecheck.sucuri.net/)

V závislosti na frekvenci, kterou aplikace, brána firewall nebo prostředí aktualizují své místní kopie těchto seznamů, může trvat značnou dobu a/nebo vyžadovat ruční zásah koncovými uživateli nebo firemních oddělení IT k vynucení aktualizace nebo explicitnímu povolení koncovým bodům CDN problém vyřešit.

Pokud je koncový bod CDN označený jako nebezpečný, [vytvořte lístek podpory](https://azure.microsoft.com/support/create-ticket/) , který zajistí, že se problém vyřeší co nejdříve.

Chcete-li *potenciálně* obejít tento problém rychleji, můžete [změnit koncový bod sady SDK CDN](#change-the-cdn-endpoint).

### <a name="application-insights-javascript-cdn-is-blocked-by-end-user---blocked-by-browser-installed-blocker-personal-firewall"></a>Application Insights JavaScript CDN je blokováno (koncový uživatel – blokováno prohlížečem; nainstalovaný blokující; osobní brána firewall)

Podívejte se, jestli koncoví uživatelé mají:

- Byl nainstalován modul plug-in prohlížeče (obvykle se jedná o nějaký druh služby AD, malwaru nebo místního blokování).
- Blokováno (nebo není povoleno) Application Insights koncové body CDN v prohlížeči nebo proxy serveru.
- Nakonfigurovali jste pravidlo brány firewall, které způsobuje, že doména CDN má být blokovaná pro sadu SDK (nebo záznam DNS, který se má vyřešit).

Pokud jste nakonfigurovali některou z těchto možností, budete s nimi muset pracovat (nebo poskytnout dokumentaci) a umožnit tak koncovým bodům CDN.

Je možné, že modul plug-in, který nainstaloval, používá veřejný seznamu blokovaných. Pokud tomu tak není, je nejpravděpodobnější, že se jedná o některé jiné ručně nakonfigurované řešení nebo že používá privátní doménu seznamu blokovaných.

#### <a name="add-exceptions-for-cdn-endpoints"></a>Přidat výjimky pro koncové body CDN

Spolupracujte s koncovými uživateli nebo poskytněte dokumentaci, že by měly umožnit stažení skriptů z Application Insights koncových bodů CDN, které se mají stáhnout, a to tak, že se budou zobrazovat v prohlížeči nebo v seznamu výjimek pravidla brány firewall (liší se v závislosti na prostředí koncového uživatele).

Tady je příklad, jak nakonfigurovat [Chrome pro povolení nebo blokování přístupu k webům.](https://support.google.com/chrome/a/answer/7532419?hl=en)

### <a name="application-insights-cdn-is-blocked-by-corporate-firewall"></a>Application Insights CDN je blokované (podniková brána firewall).

Pokud se koncoví uživatelé nacházejí v podnikové síti, je nejpravděpodobnější řešení brány firewall a jejich IT oddělení implementovalo určitou formu systému pro filtrování Internetu. V takovém případě budete muset s nimi pracovat, abyste mohli koncovým uživatelům dovolit potřebná pravidla.

#### <a name="add-exceptions-for-cdn-endpoints-for-corporations"></a>Přidání výjimek pro koncové body CDN pro společnosti

  To se podobá [přidávání výjimek koncovým uživatelům](#add-exceptions-for-cdn-endpoints), ale budete muset spolupracovat s IT oddělením, aby bylo možné v nich nakonfigurovat Application Insights koncových bodů CDN, které se mají stáhnout, a to zahrnutím (nebo jejich odebráním) do libovolného seznamu doménových bloků nebo povolených služeb.

  > [!WARNING]
  > Pokud uživatel vaší firmy používá [privátní cloud](https://azure.microsoft.com/overview/what-is-a-private-cloud/) a nemůže povolit žádnou formu výjimky, aby poskytoval interním uživatelům veřejný přístup k koncovým bodům CDN, budete muset použít [balíčky Application Insights npm](https://www.npmjs.com/package/applicationinsights) nebo hostovat sadu Application Insights SDK na vlastní síti CDN.  

### <a name="additional-troubleshooting-for-blocked-cdn"></a>Další řešení potíží s blokovaným CDN

> [!NOTE]
> Pokud uživatelé používají [privátní cloud](https://azure.microsoft.com/overview/what-is-a-private-cloud/) a nemají přístup k veřejnému Internetu, budete muset sadu SDK hostovat na vlastní síti CDN nebo používat balíčky npm.

#### <a name="host-the-sdk-on-your-own-cdn"></a>Hostování sady SDK na vlastní síti CDN

 Místo toho, aby vaši koncoví uživatelé stáhli sadu Application Insights SDK z veřejné sítě CDN, mohli byste sadu Application Insights SDK hostovat z vlastního koncového bodu CDN. Doporučuje se používat konkrétní verzi (AI. 2. #. # .min.js), aby bylo snazší zjistit, kterou verzi používáte. Také pravidelně aktualizujte na aktuální verzi (ai.2.min.js), abyste mohli využívat jakékoli opravy chyb a nové funkce, které jsou k dispozici.

#### <a name="use-npm-packages-to-embed-the-application-insight-sdk"></a>Použití balíčků NPM k vložení sady Application Insights SDK

Místo používání fragmentů a veřejných koncových bodů CDN můžete použít [balíčky npm](https://www.npmjs.com/package/applicationinsights) k zahrnutí sady SDK jako součást vašich vlastních souborů JavaScriptu. Sada SDK se stane jenom jiným balíčkem ve vašich vlastních skriptech.

> [!NOTE]
> Doporučuje se při použití balíčků NPM použít také nějaký tvar sady [JavaScript](https://www.bing.com/search?q=javascript+bundler) Package, který vám pomůže s rozdělením kódu a minifikace.

Stejně jako u fragmentu je také možné, že vaše vlastní skripty (s nebo bez použití balíčků sady SDK NPM) mohou být ovlivněny stejnými blokujícími problémy, takže v závislosti na vaší aplikaci, vašich uživatelích a vašem rozhraní byste měli zvážit implementaci podobné logice ve fragmentu kódu, aby bylo možné tyto problémy detekovat a ohlásit.


## <a name="next-steps"></a>Další kroky 
- [Získat další nápovědu naplněním problému na GitHubu](https://github.com/Microsoft/ApplicationInsights-JS/issues)
- [Monitorování využití webové stránky](javascript.md)
