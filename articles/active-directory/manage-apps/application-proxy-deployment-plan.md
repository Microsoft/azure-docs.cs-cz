---
title: Plánování nasazení proxy aplikace služby Azure active directory
description: Komplexní průvodce pro plánování nasazení proxy aplikací v rámci vaší organizace
services: active-directory
documentationcenter: azure
author: barbaraselden
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: baselden
ms.reviewer: ''
ms.openlocfilehash: edd607c4d708df9fcfd3cbd5fdb71f0a7652d6c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330910"
---
# <a name="plan-an-azure-ad-application-proxy-deployment"></a>Plánování nasazení proxy aplikace Azure AD

Azure Active Directory (Azure AD) Proxy aplikace je zabezpečené a nákladově efektivní řešení vzdáleného přístupu pro místní aplikace. Poskytuje okamžitou cestu přechodu pro organizace "Cloud First" pro správu přístupu ke starším místním aplikacím, které ještě nejsou schopné používat moderní protokoly. Další úvodní informace naleznete v tématu [Co je proxy aplikace](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy).

Proxy aplikace se doporučuje pro poskytování přístupu vzdálených uživatelů k interním prostředkům. Proxy aplikace nahrazuje potřebu VPN nebo reverzní proxy pro tyto případy použití vzdáleného přístupu. Není určen pro uživatele, kteří jsou v podnikové síti. Tito uživatelé, kteří používají proxy aplikace pro přístup k intranetu, mohou zaznamenat nežádoucí problémy s výkonem.

Tento článek obsahuje prostředky, které potřebujete k plánování, provozu a správě proxy aplikací Azure AD. 

## <a name="plan-your-implementation"></a>Naplánujte si implementaci

Následující část obsahuje široký přehled klíčových prvků plánování, které vás nastaví pro efektivní nasazení. 

### <a name="prerequisites"></a>Požadavky

Před zahájením implementace je třeba splnit následující požadavky. Další informace o nastavení prostředí, včetně těchto předpokladů, naleznete v tomto [kurzu](application-proxy-add-on-premises-application.md).

* **Konektory**: Konektory jsou zjednodušené agenty, které můžete nasadit na:
   * Místní fyzický hardware
   * Virtuální virtuální mše hostovaný v libovolném hypervisorovém řešení
   * Virtuální počítač hostovaný v Azure, který umožňuje odchozí připojení ke službě Proxy aplikace.

* Viz [Principy konektorů proxy aplikací Azure AD](application-proxy-connectors.md) pro podrobnější přehled.

     * Před instalací konektorů musí [být pro TLS 1.2 povoleny](application-proxy-add-on-premises-application.md) spojovací stroje.

     * Pokud je to možné, nasaďte konektory ve [stejné síti](application-proxy-network-topology.md) a segmentu jako servery back-endových webových aplikací. Je nejlepší nasadit konektory po dokončení zjišťování aplikací.
     * Doporučujeme, aby každá skupina konektorů má alespoň dva konektory pro zajištění vysoké dostupnosti a škálování. Mít tři konektory je optimální v případě, že budete potřebovat servis stroje v libovolném bodě. Projděte si [tabulku kapacity konektorů,](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#capacity-planning) která vám pomůže s rozhodnutím, do jakého typu počítače se mají instalovat konektory. Čím větší je počítač, tím více vyrovnávací paměti a výkonu konektorbude.

* **Nastavení přístupu k síti:** Konektory proxy aplikací Azure AD [se připojují k Azure přes PROTOKOL HTTPS (TCP Port 443) a HTTP (TCP Port 80).](application-proxy-add-on-premises-application.md) 

   * Ukončení konektoru TLS provoz není podporována a zabrání konektory z vytvoření zabezpečeného kanálu s jejich příslušné koncové body Azure App Proxy.

   * Vyhněte se všem formám inline kontroly odchozí komunikace TLS mezi konektory a Azure. Vnitřní kontrola mezi konektorem a back-endové aplikace je možné, ale může snížit uživatelské prostředí a jako takové se nedoporučuje.

   * Vyrovnávání zatížení samotných konektorů také není podporováno, nebo dokonce nutné.

### <a name="important-considerations-before-configuring-azure-ad-application-proxy"></a>Důležité informace před konfigurací proxy aplikace Azure AD

Následující základní požadavky musí být splněny, aby bylo možné nakonfigurovat a implementovat proxy aplikace Azure AD.

*  **Registrace do Azure**: Před nasazením proxy aplikace musí být identity uživatelů synchronizovány z místního adresáře nebo vytvořeny přímo v rámci vašich klientů Azure AD. Synchronizace identit umožňuje Azure AD předem ověřit uživatele před udělením přístupu k aplikacím publikované proxy aplikace a mít potřebné informace o identifikátoru uživatele k provedení jednotného přihlašování (SSO).

* **Požadavky na podmíněný přístup**: Nedoporučujeme používat proxy aplikace pro přístup k intranetu, protože to přidává latenci, která bude mít vliv na uživatele. Pro vzdálený přístup z Internetu doporučujeme používat proxy aplikace s předběžným ověřováním a zásadami podmíněného přístupu.  Přístup k poskytování podmíněného přístupu pro použití v intranetu je modernizovat aplikace tak, aby mohly přímo ověřit pomocí aad. Další informace naleznete v informacích v informacích v souboru Zdroje informací o prostředcích pro migraci aplikací do aplikace [AAD.](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources) 

* **Omezení služeb**: Chcete-li chránit před nadměrnou spotřebou prostředků jednotlivými tenanty jsou omezení nastavena na aplikaci a klienta. Chcete-li zobrazit tato omezení naleznete [omezení a omezení služby Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions). Tyto omezení omezení jsou založeny na benchmark u konce nad rámec typického objemu využití a poskytuje dostatek vyrovnávací paměti pro většinu nasazení.

* **Veřejný certifikát**: Pokud používáte vlastní názvy domén, musíte získat certifikát TLS/SSL. V závislosti na vašich organizačních požadavcích může získání certifikátu nějakou dobu trvat a doporučujeme začít proces co nejdříve. Azure Application Proxy podporuje standardní, [zástupné nebo](application-proxy-wildcard.md)san založené certifikáty. Další podrobnosti viz [Konfigurace vlastních domén pomocí proxy aplikace Azure AD](application-proxy-configure-custom-domain.md).

* **Požadavky na doménu**: Jednotné přihlášení k publikovaným aplikacím pomocí delegování s omezenou vazbou protokolu Kerberos (KCD) vyžaduje, aby server se spuštěnou konektorem a server se spuštěnou aplikací byly připojeny k doméně a součástí stejné domény nebo důvěřujících domén.
Podrobné informace o tématu najdete [v tématu KCD pro jednotné přihlášení](application-proxy-configure-single-sign-on-with-kcd.md) s proxy aplikací. Služba konektoru běží v kontextu místního systému a neměla by být nakonfigurována tak, aby používala vlastní identitu.

* **Záznamy DNS pro adresy URL**

   * Před použitím vlastních domén v proxy aplikaci je nutné vytvořit záznam CNAME ve veřejném DNS, který klientům umožní přeložit vlastní definovanou externí adresu URL na předdefinovanou adresu proxy aplikace. Pokud se nepodaří vytvořit záznam CNAME pro aplikaci, která používá vlastní doménu, zabráníte vzdáleným uživatelům v připojení k aplikaci. Kroky potřebné k přidání záznamů CNAME se u jednotlivých poskytovatelů DNS mohou lišit, takže se dozvíte, jak [spravovat záznamy DNS a sady záznamů pomocí portálu Azure Portal](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-portal).

   * Podobně musí být hostitelé konektoru schopni vyřešit interní adresu URL publikovaných aplikací.

* **Správní práva a role**

   * **Instalace konektoru** vyžaduje práva místních správců k serveru Windows, na který je nainstalován. Vyžaduje také minimální roli *správce aplikace* k ověření a registraci instance konektoru do vašeho klienta Azure AD. 

   * **Publikování a správa aplikací** vyžadují roli *správce aplikace.* Správci aplikací mohou spravovat všechny aplikace v adresáři, včetně registrací, nastavení přihlašování, přiřazení uživatelů a skupin a licencování, nastavení proxy aplikací a souhlasu. Neuděluje možnost spravovat podmíněný přístup. Role *správce cloudových aplikací* má všechny schopnosti správce aplikace s tím rozdílem, že neumožňuje správu nastavení proxy aplikace.

* **Licencování**: Proxy aplikace je k dispozici prostřednictvím předplatného Azure AD Premium. Úplný seznam možností a funkcí licencování najdete na [stránce Ceny služby Azure Active Directory.](https://azure.microsoft.com/pricing/details/active-directory/)  

### <a name="application-discovery"></a>Zjišťování aplikace

Sestavte soupis všech aplikací v oboru, které jsou publikovány prostřednictvím proxy aplikace, shromažďováním následujících informací:

| Typ informací| Informace ke shromažďování |
|---|---|
| Typ služby| Příklad: SharePoint, SAP, CRM, Vlastní webová aplikace, ROZHRANÍ API |
| Aplikační platforma | Například: Windows IIS, Apache na Linuxu, Tomcat, NGINX |
| Členství domény| Plně kvalifikovaný název domény webového serveru (FQDN) |
| Umístění aplikace | Kde se webový server nebo farma nachází ve vaší infrastruktuře |
| Interní přístup | Přesná adresa URL použitá při interním přístupu k aplikaci. <br> Pokud je farma, jaký typ vyrovnávání zatížení se používá? <br> Určuje, zda aplikace čerpá obsah z jiných zdrojů než ze sebe.<br> Zjistěte, zda aplikace pracuje přes WebSockets. |
| Externí přístup | Řešení dodavatele, které je aplikace již vystavena externě. <br> Adresa URL, kterou chcete použít pro externí přístup. Pokud sharepoint, ujistěte se, že alternativní mapování přístupu jsou nakonfigurovány podle [tohoto návodu](https://docs.microsoft.com/SharePoint/administration/configure-alternate-access-mappings). Pokud ne, budete muset definovat externí adresy URL. |
| Veřejný certifikát | Pokud používáte vlastní doménu, obklijte certifikát s odpovídajícím názvem subjektu. Pokud certifikát existuje, poznamenejte si sériové číslo a umístění, odkud jej lze získat. |
| Typ ověřování| Typ ověřování podporovaný podporou aplikací, jako je základní, ověřování integrace systému Windows, založené na formulářích, záhlaví a deklarace identity. <br>Pokud je aplikace nakonfigurována tak, aby se spouštěla pod určitým účtem domény, poznamenejte si plně kvalifikovaný název domény (Plně kvalifikovaný název domény) účtu služby.<br> Pokud saml-založené, identifikátor a odpovědi adresy URL. <br> Pokud je založen na záhlaví, řešení dodavatele a konkrétní požadavek pro zpracování typu ověřování. |
| Název skupiny konektorů | Logický název pro skupinu spojnic, které budou určeny k poskytování potrubí a přihlašování k této back-endové aplikace. |
| Přístup k uživatelům/skupinám | Uživatelé nebo skupiny uživatelů, kterým bude udělen externí přístup k aplikaci. |
| Dodatečné požadavky | Všimněte si všech dalších požadavků na vzdálený přístup nebo zabezpečení, které by měly být zohledněny do publikování aplikace. |

Tuto tabulku [inventáře aplikací](https://aka.ms/appdiscovery) si můžete stáhnout a vytvořit inventuru aplikací.

### <a name="define-organizational-requirements"></a>Definování organizačních požadavků

Níže jsou uvedeny oblasti, pro které byste měli definovat obchodní požadavky vaší organizace. Každá oblast obsahuje příklady požadavků

 **Přístup**

* Vzdálení uživatelé s připojenou doménou nebo zařízeními připojená k Azure AD mají bezpečně přístup k publikovaným aplikacím pomocí bezproblémového jednotného přihlašování (SSO).

* Vzdálení uživatelé se schválenými osobními zařízeními mají bezpečně přístup k publikovaným aplikacím za předpokladu, že jsou zaregistrovaní do vícefaktorové ověřování a zaregistrovali aplikaci Microsoft Authenticator na svém mobilním telefonu jako metodu ověřování.

**Správy** 

* Správci mohou definovat a sledovat životní cyklus přiřazení uživatelů k aplikacím publikovaným prostřednictvím proxy aplikace.

**Zabezpečení**

* K těmto aplikacím mají přístup pouze uživatelé přiřazení k aplikacím prostřednictvím členství ve skupině nebo jednotlivě.

**Výkon**

* Ve srovnání s přístupem k aplikaci z interní sítě nedochází ke snížení výkonu aplikace.

**Zkušenosti uživatele**

* Uživatelé jsou si vědomi toho, jak přistupovat ke svým aplikacím pomocí známých firemních adres URL na libovolné platformě zařízení.

**Auditování**
* Správci mohou auditovat aktivitu přístupu uživatelů.


### <a name="best-practices-for-a-pilot"></a>Osvědčené postupy pro pilota

Určete množství času a úsilí potřebné k úplnému uvedení do provozu jedné aplikace pro vzdálený přístup pomocí jednotného přihlašování (SSO). Proveďte spuštěním pilotního projektu, který bere v úvahu jeho počáteční zjišťování, publikování a obecné testování. Použití jednoduché webové aplikace založené na službě IIS, která je již předkonfigurována pro integrované ověřování systému Windows (IWA), by pomohlo vytvořit směrný plán, protože toto nastavení vyžaduje minimální úsilí k úspěšnému pilotování vzdáleného přístupu a přiřazování a přihlašování.

Následující prvky návrhu by měly zvýšit úspěšnost pilotní implementace přímo v produkčním tenantovi.  

**Správa konektorů**:  

* Konektory hrají klíčovou roli při poskytování místního vedení pro vaše aplikace. Použití **výchozí** konektor skupiny je vhodné pro počáteční pilotní testování publikovaných aplikací před jejich uvedení do provozu do produkčního prostředí. Úspěšně testované aplikace pak lze přesunout do skupin výrobních konektorů.

**Správa aplikací**:

* Vaši zaměstnanci si s největší pravděpodobností zapamatují, že externí adresa URL je známá a relevantní. Vyhněte se publikování aplikace pomocí našich předdefinovaných msappproxy.net nebo onmicrosoft.com přípony. Místo toho zadejte známou ověřenou doménu nejvyšší úrovně s předponou s logickým názvem hostitele, například *intranet.<customers_domain>.com*.

* Omezte viditelnost ikony pilotní aplikace na pilotní skupinu skrytím ikony spuštění z portálu Azure MyApps. Když jste připraveni k produkčnímu prostředí, můžete aplikaci převést na příslušnou cílovou skupinu, buď ve stejném předprodukčním tenantovi, nebo také publikováním aplikace ve vašem produkčním tenantovi.

**Nastavení jednotného přihlašování**: Některá nastavení jednotného přihlašování mají specifické závislosti, jejichž nastavení může nějakou dobu trvat, takže se vyhněte zpožděním řízení změn tím, že zajistíte, že závislosti budou řešeny předem. To zahrnuje hostitele konektoru připojení domény k provedení s přihlašování pomocí kerberos omezené delegování (KCD) a péči o další časově náročné aktivity. Například nastavení instance přístupping, pokud potřebujete přihlašování založené na záhlaví.

**TLS mezi hostitelem konektoru a cílovou aplikací**: Zabezpečení je prvořadé, takže tls mezi hostitelem konektoru a cílovými aplikacemi by měl y být vždy používány. Zejména v případě, že webová aplikace je nakonfigurován pro ověřování na základě formulářů (FBA), jako pověření uživatele jsou pak efektivně přenášeny ve prostém textu.

**Implementujte postupně a otestujte každý krok**. Po publikování aplikace proveďte základní funkční testování, abyste zajistili, že všechny požadavky uživatelů a firmy budou splněny podle následujících pokynů:

1. Otestujte a ověřte obecný přístup k webové aplikaci se zakázaným předběžným ověřováním.
2. Pokud je úspěšná povolit předběžné ověření a přiřadit uživatele a skupiny. Otestujte a ověřte přístup.
3. Pak přidejte metodu spřimit pro vaši aplikaci a znovu otestujte přístup.
4. Podle potřeby použijte zásady podmíněného přístupu a vícefaktorové žádosti. Otestujte a ověřte přístup.

**Nástroje pro odstraňování potíží**: Při řešení potíží vždy začněte ověřením přístupu k publikované aplikaci z prohlížeče na hostiteli konektoru a potvrďte, že aplikace funguje podle očekávání. Jednodušší nastavení, snadněji určit hlavní příčinu, takže zvažte pokus o reprodukci problémů s minimální konfigurací, jako je například použití pouze jeden konektor a žádné jednotné připojování. V některých případech se nástroje pro ladění webu, jako je Telerik's Fiddler, mohou ukázat jako nepostradatelné pro řešení problémů s přístupem nebo obsahem v aplikacích, ke kterým se přistupuje prostřednictvím proxy serveru. Fiddler může také působit jako proxy pomoci sledovat a ladit provoz pro mobilní platformy, jako je iOS a Android, a prakticky cokoliv, co lze nakonfigurovat tak, aby trasovat přes proxy server. Další informace naleznete v [příručce pro řešení potíží.](application-proxy-troubleshoot.md)

## <a name="implement-your-solution"></a>Implementace vašeho řešení

### <a name="deploy-application-proxy"></a>Nasazení proxy aplikace

Kroky nasazení proxy aplikace jsou zahrnuty v tomto [kurzu pro přidání místní aplikace pro vzdálený přístup](application-proxy-add-on-premises-application.md). Pokud instalace není úspěšná, vyberte **na portálu poradce při potížích s proxy serverem aplikace** nebo použijte průvodce odstraňováním potíží [s instalací konektoru agenta proxy aplikace](application-proxy-connector-installation-problem.md).

### <a name="publish-applications-via-application-proxy"></a>Publikování žádostí prostřednictvím proxy aplikace

Publikování aplikací předpokládá, že jste splnili všechny předpoklady a že máte několik konektorů, které se zobrazují jako registrované a aktivní na stránce Proxy aplikace.

Aplikace můžete také publikovat pomocí [prostředí PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview).

Níže jsou uvedeny některé doporučené postupy, které je třeba dodržovat při publikování aplikace:

* **Použít skupiny spojnic**: Přiřazení skupiny konektorů, která byla určena pro publikování jednotlivých aplikací. Doporučujeme, aby každá skupina konektorů má alespoň dva konektory pro zajištění vysoké dostupnosti a škálování. Mít tři konektory je optimální v případě, že budete potřebovat servis stroje v libovolném bodě. Kromě toho najdete [v tématu Publikování aplikací v samostatných sítích a umístěnípomocí skupin konektorů, abyste zjistili,](application-proxy-connector-groups.md) jak můžete také použít skupiny konektorů k segmentaci konektorů podle sítě nebo umístění.

* **Nastavit časový čas back-endu:** Toto nastavení je užitečné ve scénářích, kde může zpracování klientské transakce vyžadovat více než 75 sekund. Například když klient odešle dotaz do webové aplikace, která funguje jako front-end do databáze. Front-end odešle tento dotaz na jeho back-end databázový server a čeká na odpověď, ale v době, kdy obdrží odpověď, klientstraně konverzace časový limit. Nastavení časového času na long poskytuje 180 sekund pro delší transakce k dokončení.

* **Použít vhodné typy souborů cookie**

   * **Soubor cookie pouze http:** Poskytuje další zabezpečení tím, že proxy aplikace obsahuje příznak HTTPOnly v hlavičkách odpovědí HTTP set-cookie. Toto nastavení pomáhá zmírnit zneužití, jako je skriptování mezi weby (XSS). Tuto sadu ponechte na Ne pro klienty nebo uživatelské agenty, kteří vyžadují přístup k souboru cookie relace. Například klient RDP/MTSC, který se připojuje k bráně vzdálené plochy publikované prostřednictvím proxy aplikace.

   * **Zabezpečený soubor cookie**: Pokud je soubor cookie nastaven pomocí atributu Secure, uživatelský agent (aplikace na straně klienta) zahrne soubor cookie do požadavků HTTP pouze v případě, že je požadavek přenášen prostřednictvím zabezpečeného kanálu TLS. To pomáhá zmírnit riziko ohrožení souboru cookie prostřednictvím kanálů prostého textu, proto by mělo být povoleno.

   * **Trvalý soubor cookie**: Umožňuje zachovat soubor cookie relace proxy aplikace mezi uzavírkami prohlížeče tím, že zůstane platný, dokud nevyprší jeho platnost nebo dokud nebude odstraněn. Používá se pro scénáře, kde bohatá aplikace, jako je například kancelář, přistupuje k dokumentu v rámci publikované webové aplikace, aniž by byl uživatel znovu vyzván k ověření. Povolit však s opatrností, protože trvalé soubory cookie mohou nakonec zanechat službu v nebezpečí neoprávněného přístupu, pokud nejsou používány ve spojení s jinými kompenzačními ovládacími prvky. Toto nastavení by se mělo používat pouze pro starší aplikace, které nemohou sdílet soubory cookie mezi procesy. Je lepší aktualizovat aplikaci tak, aby zpracovávala sdílení souborů cookie mezi procesy namísto použití tohoto nastavení.

* **Přeložit adresy URL v záhlaví**: Tuto možnost povolíte pro scénáře, kdy interní DNS nelze nakonfigurovat tak, aby odpovídalveřejnému oboru názvů organizace (aka Split DNS). Pokud vaše aplikace vyžaduje původní hlavičku hostitele v požadavku klienta, ponechte tuto hodnotu nastavenou na Hodnotu Ano. Alternativou je, aby konektor používal hlavní název doménové adresy v interní adrese URL pro směrování skutečného provozu a hlavní název doménv externí adrese URL jako záhlaví hostitele. Ve většině případů by tato alternativa měla umožnit, aby aplikace fungovala jako normální, při vzdáleném přístupu, ale uživatelé ztrácejí výhody odpovídajícího vnitřního & mimo adresu URL.

* **Přeložit adresy URL v těle aplikace**: Zapněte překlad odkazu Tělo aplikace pro aplikaci, pokud chcete, aby odkazy z této aplikace byly přeloženy v odpovědích zpět klientovi. Pokud je tato funkce povolena, poskytuje nejlepší pokus o pokus o překlad všech interních odkazů, které proxy aplikace najde v odpovědích HTML a CSS, které jsou vráceny klientům. Je užitečné při publikování aplikací, které obsahují pevně zakódované absolutní nebo NetBIOS zkrácený název odkazy v obsahu nebo aplikace s obsahem, který odkazuje na jiné místní aplikace.

Pro scénáře, kde publikovaná aplikace odkazuje na jiné publikované aplikace, povolte překlad odkazů pro každou aplikaci, abyste měli kontrolu nad uživatelským prostředím na úrovni jednotlivých aplikací.

Předpokládejme například, že máte tři aplikace publikované prostřednictvím proxy aplikace, které všechny odkazují na sebe navzájem: Výhody, Výdaje a Cestování, plus čtvrtá aplikace, Zpětná vazba, která není publikována prostřednictvím proxy aplikace.

![Obrázek 1](media/App-proxy-deployment-plan/link-translation.png)

Pokud povolíte překlad odkazů pro aplikaci Výhody, odkazy na Výdaje a cestování budou přesměrovány na externí adresy URL těchto aplikací, aby k nim měli přístup uživatelé, kteří přistupují k aplikacím mimo podnikovou síť. Odkazy z výdajů a cesty zpět na výhody nefungují, protože překlad odkazů nebyl povolen pro tyto dvě aplikace. Odkaz na zpětnou vazbu není přesměrován, protože neexistuje žádná externí adresa URL, takže uživatelé, kteří používají aplikaci Výhody, nebudou mít přístup k aplikaci pro zpětnou vazbu mimo podnikovou síť. Podívejte se na podrobné informace o [překladu odkazů a dalších možnostech přesměrování](application-proxy-configure-hard-coded-link-translation.md).

### <a name="access-your-application"></a>Přístup k aplikaci

Existuje několik možností pro správu přístupu k prostředkům publikovaným na proxy app, takže zvolte nejvhodnější pro daný scénář a potřeby škálovatelnosti. Mezi běžné přístupy patří: použití místních skupin, které se synchronizují prostřednictvím služby Azure AD Connect, vytváření dynamických skupin ve službě Azure AD na základě atributů uživatelů, použití samoobslužných skupin, které jsou spravované vlastníkem prostředku, nebo jejich kombinace. Podívejte se na propojené zdroje pro výhody každého z nich.

Nejpřímější způsob přiřazení přístupu uživatelů k aplikaci je v možnostech **Uživatelé a skupiny** z levého podokna publikované aplikace a přímého přiřazení skupin nebo jednotlivců.

![Obrázek 24](media/App-proxy-deployment-plan/add-user.png)

Uživatelům můžete také povolit samoobslužný přístup k vaší aplikaci přiřazením skupiny, jejíž nejsou aktuálně členy, a konfigurací samoobslužných možností.

![Obrázek 25](media/App-proxy-deployment-plan/allow-access.png)

Pokud je tato možnost povolena, uživatelé se pak budou moci přihlásit k portálu MyApps a požádat o přístup a buď budou automaticky schváleni a přidáni do již povolené samoobslužné skupiny, nebo budou potřebovat schválení od určeného schvalovatele.

Uživatelé typu Host mohou být také [pozváni k přístupu k interním aplikacím publikovaným prostřednictvím proxy aplikace prostřednictvím azure ad b2b](https://docs.microsoft.com/azure/active-directory/b2b/add-users-information-worker).

Pro místní aplikace, které jsou obvykle přístupné anonymně, vyžadující žádné ověřování, můžete raději zakázat možnost umístěnou v **vlastnosti**aplikace .

![Obrázek 26](media/App-proxy-deployment-plan/assignment-required.png)


Ponechání této možnosti nastavené na ne umožňuje uživatelům přístup k místní aplikaci prostřednictvím Azure AD App Proxy bez oprávnění, takže buďte opatrní.

Jakmile je aplikace publikována, měla by být přístupná zadáním její externí [https://myapps.microsoft.com](https://myapps.microsoft.com/)adresy URL v prohlížeči nebo ikonou na adrese .

### <a name="enable-pre-authentication"></a>Povolení předběžného ověřování

Ověřte, zda je aplikace přístupná prostřednictvím proxy aplikace, která k ní přistupuje prostřednictvím externí adresy URL. 

1. Přejděte do**aplikací** >  **Azure Active Directory** > Enterprise**Všechny aplikace** a vyberte aplikaci, kterou chcete spravovat.

2. Vyberte **proxy aplikace**.

3. V poli **Předběžné ověřování** vyberte pomocí rozevíracího seznamu **službu Azure Active Directory**a vyberte **Uložit**.

Pokud je povoleno předběžné ověřování, Azure AD vyzve uživatele nejprve k ověření a pokud je nakonfigurováno jednotné přihlášení, pak back-endová aplikace také ověří uživatele před udělením přístupu k aplikaci. Změna režimu předběžného ověřování z průchodu na Azure AD také nakonfiguruje externí adresu URL pomocí protokolu HTTPS, takže všechny aplikace původně nakonfigurované pro protokol HTTP budou nyní zabezpečeny protokolem HTTPS.

### <a name="enable-single-sign-on"></a>Povolení jednotného přihlašování

SSO poskytuje nejlepší možné uživatelské prostředí a zabezpečení, protože uživatelé stačí přihlásit jednou při přístupu k Azure AD. Jakmile má uživatel předem ověřené, semadorinky provádí konektor proxy aplikace ověřování místní aplikace jménem uživatele. Back-endová aplikace zpracovává přihlášení, jako by to byl samotný uživatel. 

Volba **passthrough** možnost umožňuje uživatelům přístup k publikované aplikace bez nutnosti ověření azure ad.

Provádění služby Přihlašování je možné pouze v případě, že Azure AD můžete identifikovat uživatele požadující přístup k prostředku, takže vaše aplikace musí být nakonfigurované k předběžné ověření uživatelů s Azure AD při přístupu pro přihlašování k správě, jinak možnosti automatického přihlašování bude zakázáno.

Přečtěte si [jednotné přihlašování k aplikacím ve službě Azure AD,](what-is-single-sign-on.md) které vám pomohou vybrat nejvhodnější metodu jednotného přihlašování při konfiguraci aplikací.

###  <a name="working-with-other-types-of-applications"></a>Práce s jinými typy aplikací

Proxy aplikace Azure AD můžete také podporovat aplikace, které byly vyvinuty pro použití naší knihovny ověřování Azure AD[(ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)nebo Microsoft Authentication Library[(MSAL).](https://azure.microsoft.com/blog/start-writing-applications-today-with-the-new-microsoft-authentication-sdks/) Podporuje nativní klientské aplikace tím, že spotřebovává tokeny vydané službou Azure AD přijaté v informacích záhlaví požadavku klienta k provedení předběžného ověření jménem uživatelů.

Přečtěte si [publikování nativních a mobilních klientských aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-native-client) a [aplikací založených na deklaracích identity,](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-claims-aware-apps) kde najdete informace o dostupných konfiguracích proxy aplikací.

### <a name="use-conditional-access-to-strengthen-security"></a>Použití podmíněného přístupu k posílení zabezpečení

Zabezpečení aplikací vyžaduje pokročilou sadu funkcí zabezpečení, které mohou chránit před složitými hrozbami v místním i cloudu a reagovat na ně. Útočníci nejčastěji získají přístup k podnikové síti prostřednictvím slabých, výchozích nebo odcizených uživatelských pověření.  Zabezpečení založené na identitách společnosti Microsoft omezuje používání odcizených přihlašovacích údajů tím, že spravuje a chrání privilegované i neprivilegované identity.

Následující možnosti lze použít pro podporu proxy aplikací Azure AD:

* Podmíněný přístup založený na uživateli a umístění: Udržujte citlivá data chráněná omezením přístupu uživatelů na základě zeměpisné polohy nebo IP adresy pomocí [zásad podmíněného přístupu založeného na poloze](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations).

* Podmíněný přístup založený na zařízení: Zajistěte, aby k podnikovým datům přistupovala pouze zaregistrovaná, schválená a kompatibilní zařízení pomocí [podmíněného přístupu založeného na zařízení](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications).

* Podmíněný přístup založený na aplikaci: Práce se nemusí zastavit, když uživatel není v podnikové síti. [Zabezpečte přístup k podnikovým cloudovým a místním aplikacím](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam) a udržujte kontrolu pomocí podmíněného přístupu.

* Podmíněný přístup založený na rizicích: Chraňte svá data před škodlivými hackery pomocí [zásad podmíněného přístupu založeného na rizicích,](https://www.microsoft.com/cloud-platform/conditional-access) které lze použít na všechny aplikace a všechny uživatele, ať už místně nebo v cloudu.

* Přístupový panel Azure AD: S nasazenou službou Proxy aplikace a bezpečně publikovanými aplikacemi nabídněte uživatelům jednoduché centrum pro zjišťování a přístup ke všem jejich aplikacím. Zvyšte produktivitu pomocí samoobslužných funkcí, jako je možnost požádat o přístup k novým aplikacím a skupinám nebo spravovat přístup k těmto prostředkům jménem jiných uživatelů prostřednictvím [přístupového panelu](https://aka.ms/AccessPanelDPDownload).

## <a name="manage-your-implementation"></a>Správa implementace

### <a name="required-roles"></a>Požadované role

Microsoft obhajuje princip udělení co nejmenšího oprávnění k provádění potřebných úkolů s Azure AD. [Zkontrolujte různé role Azure, které jsou k dispozici,](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) a vyberte ten správný, abyste vyřešili potřeby každé osoby. Některé role může být nutné dočasně použít a odebrat po dokončení nasazení.

| Obchodní role| Obchodní úkoly| Role Azure AD |
|---|---|---|
| Správce technické podpory | Obvykle omezena na kvalifikované koncového uživatele hlášeny problémy a provádění omezených úkolů, jako je změna hesla uživatelů, zrušení aktualizace tokeny a sledování stavu služby. | Správce technické podpory |
| Správce identity| Přečtěte si azure ad přihlášení sestavy a protokoly auditu ladit proxy aplikace související problémy.| Čtenář zabezpečení |
| Vlastník aplikace| Vytvářejte a spravujte všechny aspekty podnikových aplikací, registrací aplikací a nastavení proxy serveru aplikace.| Správce aplikace |
| Správce infrastruktury | Vlastník převrácení certifikátu | Správce aplikace |

Minimalizace počtu uživatelů, kteří mají přístup k zabezpečeným informacím nebo prostředkům, pomůže snížit pravděpodobnost, že škodlivý objekt actor získá neoprávněný přístup nebo oprávněný uživatel neúmyslně ovlivní citlivý prostředek. 
 
Uživatelé však stále potřebují provádět každodenní privilegované operace, takže vynucení zásad [správy privilegovaných identit](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) založených na just-in-time (JIT) s cílem poskytnout privilegovaný přístup k prostředkům Azure na vyžádání a Azure AD je náš doporučený přístup k efektivní správě přístupu správce a auditování.

### <a name="reporting-and-monitoring"></a>Sledování a vytváření sestav

Azure AD poskytuje další přehled o využití aplikací vaší organizace a provozní stav prostřednictvím [protokolů auditu a sestav](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). Proxy aplikace také umožňuje velmi snadné sledovat konektory z portálu Azure AD a protokolů událostí systému Windows.

#### <a name="application-audit-logs"></a>Protokoly auditu aplikací

Tyto protokoly poskytují podrobné informace o přihlášení k aplikacím nakonfigurovaným pomocí proxy aplikace a zařízení a uživateli, který přistupuje k aplikaci. [Protokoly auditu](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) jsou umístěné na portálu Azure a v [rozhraní API pro audit](https://docs.microsoft.com/graph/api/resources/directoryaudit?view=graph-rest-beta) pro export. Navíc sestavy [využití a přehledy](../reports-monitoring/concept-usage-insights-report.md?context=azure/active-directory/manage-apps/context/manage-apps-context) jsou k dispozici také pro vaši aplikaci.

#### <a name="application-proxy-connector-monitoring"></a>Monitorování konektoru proxy aplikace

Konektory a služba se starají o všechny úkoly s vysokou dostupností. Můžete sledovat stav konektorů ze stránky Proxy aplikace na webu Azure AD Portal. Další informace o údržbě konektorů naleznete [v tématu Principy konektorů proxy aplikací Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#maintenance).

![Příklad: Konektory proxy aplikací Azure AD](./media/application-proxy-connectors/app-proxy-connectors.png)

#### <a name="windows-event-logs-and-performance-counters"></a>Protokoly událostí systému Windows a čítače výkonu

Konektory mají protokoly správce i relace. Protokoly správce obsahují klíčové události a jejich chyby. Protokoly relace obsahují všechny transakce a jejich podrobnosti o zpracování. Protokoly a čítače jsou umístěny v protokolech událostí systému Windows další informace naleznete [v tématu Principy konektorů proxy aplikací Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#under-the-hood). Podle tohoto [kurzu nakonfigurujte zdroje dat protokolu událostí v Azure Monitoru](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-windows-events).

### <a name="troubleshooting-guide-and-steps"></a>Průvodce odstraňováním potíží a kroky

Další informace o běžných problémech a jejich řešení naleznete v našem průvodci [odstraňováním chybových](application-proxy-troubleshoot.md) zpráv. 

Následující články popisují běžné scénáře, které lze také použít k vytvoření pokynů pro řešení potíží pro vaši organizaci podpory. 

* [Potíže při zobrazování stránky aplikace](application-proxy-page-appearance-broken-problem.md)
* [Načítání aplikace trvá příliš dlouho](application-proxy-page-load-speed-problem.md)
* [Odkazy na stránce aplikace nefungují](application-proxy-page-links-broken-problem.md)
* [Jaké porty je potřeba odemknout pro mou aplikaci](application-proxy-connectivity-ports-how-to.md)
* [Ve skupině konektorů není žádný fungující konektor pro mou aplikaci](application-proxy-connectivity-no-working-connector.md)
* [Konfigurace na portálu pro správu](application-proxy-config-how-to.md)
* [Konfigurace jednotného přihlašování k mé aplikaci](application-proxy-config-sso-how-to.md)
* [Potíže při vytváření aplikace na portálu pro správu](application-proxy-config-problem.md)
* [Konfigurace omezeného delegování Kerberos](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Konfigurace s využitím PingAccess](application-proxy-back-end-ping-access-how-to.md)
* [Nelze získat přístup k této chybě podnikové aplikace](application-proxy-sign-in-bad-gateway-timeout-error.md)
* [Potíže při instalaci konektoru agenta proxy aplikací](application-proxy-connector-installation-problem.md)
* [Potíže s přihlašováním](application-sign-in-problem-on-premises-application-proxy.md)
