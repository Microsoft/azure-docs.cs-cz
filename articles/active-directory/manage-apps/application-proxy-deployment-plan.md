---
title: Plánování nasazení Proxy aplikací služby Azure Active Directory
description: Komplexní průvodce plánováním nasazení aplikačního proxy serveru v rámci vaší organizace
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/31/2020
ms.author: kenwith
ms.openlocfilehash: 08289987dac81905584ee65b808cfcbf35525728
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2020
ms.locfileid: "97835138"
---
# <a name="plan-an-azure-ad-application-proxy-deployment"></a>Naplánování nasazení proxy aplikací služby Azure AD

Proxy aplikace Azure Active Directory (Azure AD) je zabezpečené a nákladově efektivní řešení vzdáleného přístupu pro místní aplikace. Poskytuje okamžitou přechodovou cestu pro "cloudové" organizace pro správu přístupu ke starším místním aplikacím, které ještě neumožňují použití moderních protokolů. Další úvodní informace najdete v tématu [co je proxy aplikace](./application-proxy.md).

Proxy aplikací se doporučuje pro poskytování přístupu vzdálených uživatelů k interním prostředkům. Pro tyto případy použití vzdáleného přístupu nahrazuje proxy aplikace nutnost připojení k síti VPN nebo reverzního proxy serveru. Není určena pro uživatele, kteří jsou v podnikové síti. Tito uživatelé, kteří používají proxy aplikace pro intranetový přístup, můžou vyskytnout nežádoucí problémy s výkonem.

Tento článek obsahuje prostředky, které potřebujete k plánování, provozování a správě Azure Proxy aplikací služby AD.

## <a name="plan-your-implementation"></a>Plánování implementace

V následující části najdete širokou škálu klíčových prvků plánování, které nastavíte jako efektivní prostředí pro nasazení.

### <a name="prerequisites"></a>Požadavky

Než začnete s implementací, musíte splnit následující požadavky. V tomto [kurzu](application-proxy-add-on-premises-application.md)můžete zobrazit další informace o nastavení prostředí, včetně těchto požadavků.

* **Konektory**: konektory jsou odlehčení agenti, které můžete nasadit na:
   * Fyzický hardware místně
   * Virtuální počítač hostovaný v jakémkoli řešení hypervisoru
   * Virtuální počítač hostovaný v Azure, který umožňuje odchozí připojení ke službě proxy aplikací.

* Podrobnější přehled najdete v tématu [vysvětlení konektorů proxy serveru aplikace Azure AD](application-proxy-connectors.md) .

     * Než budete moct nainstalovat konektory, musí být počítače konektoru [povolené pro TLS 1,2](application-proxy-add-on-premises-application.md) .

     * Pokud je to možné, nasaďte konektory ve [stejné síti](application-proxy-network-topology.md) a segmentu jako servery back-end webové aplikace. Je nejlepší nasadit konektory po dokončení zjišťování aplikací.
     * Doporučujeme, aby každá skupina konektorů měla aspoň dva konektory, aby se zajistila vysoká dostupnost a škálování. Pro případ, že budete chtít mít k dispozici tři konektory, může být nutné provozovat počítač v jakémkoli bodě. V [tabulce kapacity konektoru](./application-proxy-connectors.md#capacity-planning) pomůžete se rozhodnout, na jaký typ počítače se mají nainstalovat konektory. Čím větší je počítač, tím více vyrovnávací paměti a provedení konektoru bude.

* **Nastavení přístupu k síti**: konektory Azure proxy aplikací služby AD [se připojují k Azure prostřednictvím protokolu HTTPS (TCP port 443) a http (TCP port 80)](application-proxy-add-on-premises-application.md).

   * Ukončení provozu konektoru TLS se nepodporuje a zabrání konektorům vytvořit zabezpečený kanál s příslušnými koncovými body proxy aplikací Azure.

   * Vyhněte se všem formám vložené kontroly na odchozí komunikaci TLS mezi konektory a Azure. Interní kontrola mezi konektorem a back-end aplikacemi je možná, ale může snížit činnost koncového uživatele a nedoporučuje se.

   * Vyrovnávání zatížení samotných konektorů se nepodporuje ani to není možné.

### <a name="important-considerations-before-configuring-azure-ad-application-proxy"></a>Důležité informace před konfigurací Azure Proxy aplikací služby AD

Aby bylo možné konfigurovat a implementovat Azure Proxy aplikací služby AD, musí být splněné následující základní požadavky.

*  Registrace do **Azure**: před nasazením proxy aplikací je potřeba, aby se identity uživatelů synchronizovaly z místního adresáře nebo vytvořily přímo v klientech Azure AD. Synchronizace identity umožňuje službě Azure AD předem ověřit uživatele předtím, než jim udělí přístup k aplikacím publikovaným v proxy aplikaci, a bude mít potřebné informace o uživatelském identifikátoru k provedení jednotného přihlašování (SSO).

* **Požadavky na podmíněný přístup**: Nedoporučujeme používat proxy aplikace pro přístup k intranetu, protože to přidá latenci, která bude mít vliv na uživatele. Pro vzdálený přístup z Internetu doporučujeme používat proxy aplikace s předběžným ověřením a zásadami podmíněného přístupu.  Přístup k zajištění podmíněného přístupu pro použití v intranetu je modernizovat aplikace, aby je bylo možné přímo ověřit pomocí AAD. Další informace najdete v tématu [zdroje pro migraci aplikací do AAD](./migration-resources.md) .

* **Omezení služby**: pro zajištění ochrany před zneužitím prostředků jednotlivými klienty jsou nastavené limity omezení pro jednotlivé aplikace a klienty. Pokud se chcete podívat na tato omezení, přečtěte si omezení [a omezení služby Azure AD](../enterprise-users/directory-service-limits-restrictions.md). Tyto limity omezování jsou založené na testu výkonnosti mnohem nad běžným objemem využití a poskytují rozsáhlou vyrovnávací paměť pro většinu nasazení.

* **Veřejný certifikát**: Pokud používáte vlastní názvy domén, musíte certifikát TLS/SSL obstarat. V závislosti na požadavcích vaší organizace může získání certifikátu nějakou dobu trvat a doporučujeme začít proces co nejdříve. Azure Application proxy podporuje certifikáty založené na standardu, [zástupném znaku](application-proxy-wildcard.md)nebo síti SAN. Další podrobnosti najdete v tématu [Konfigurace vlastních domén pomocí Azure proxy aplikací služby AD](application-proxy-configure-custom-domain.md).

* **Požadavky na doménu**: jednotné přihlašování k publikovaným aplikacím pomocí omezeného delegování protokolu Kerberos (KCD) vyžaduje, aby server, na kterém běží konektor, a server, na kterém je spuštěná aplikace, byl připojený k doméně a součástí stejné domény nebo důvěřujících domén.
Podrobné informace o tomto tématu najdete v tématu [KCD pro jednotné přihlašování](application-proxy-configure-single-sign-on-with-kcd.md) pomocí proxy aplikací. Služba konektoru běží v kontextu místního systému a neměla by být nakonfigurována pro použití vlastní identity.

* **Záznamy DNS pro adresy URL**

   * Před použitím vlastních domén v proxy aplikací musíte ve veřejném DNS vytvořit záznam CNAME, který klientům umožní přeložit vlastní definovanou externí adresu URL na předem definovanou adresu proxy serveru aplikace. Selhání vytvoření záznamu CNAME pro aplikaci, která používá vlastní doménu, znemožní vzdáleným uživatelům, aby se připojili k aplikaci. Kroky vyžadované pro přidání záznamů CNAME se můžou u poskytovatele DNS lišit, takže se dozvíte, jak [Spravovat záznamy a sady záznamů DNS pomocí Azure Portal](../../dns/dns-operations-recordsets-portal.md).

   * Podobně hostitelé konektorů musí být schopni přeložit interní adresu URL publikovaných aplikací.

* **Administrativní práva a role**

   * **Instalace konektoru** vyžaduje oprávnění místního správce k serveru Windows, na kterém je nainstalovaný. K ověření a registraci instance konektoru pro vašeho tenanta Azure AD taky vyžaduje minimálně roli *správce aplikace* .

   * **Publikování a Správa aplikací** vyžaduje roli *správce aplikace* . Správci aplikací můžou spravovat všechny aplikace v adresáři včetně registrací, nastavení jednotného přihlašování, přiřazení uživatelů a skupin a licencí, nastavení proxy aplikací a souhlasu. Neuděluje možnost spravovat podmíněný přístup. Role *správce cloudové aplikace* má všechny schopnosti správce aplikace, s tím rozdílem, že neumožňuje správu nastavení proxy aplikací.

* **Licencování**: proxy aplikací je k dispozici prostřednictvím předplatného Azure AD Premium. Úplný seznam možností a funkcí licencování najdete na [stránce s cenami Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/) .

### <a name="application-discovery"></a>Zjišťování aplikací

Zkompilujte inventář všech aplikací v oboru, které jsou publikovány prostřednictvím proxy aplikací, shromážděním následujících informací:

| Typ informací| Informace, které se mají shromáždit |
|---|---|
| Typ služby| Příklad: SharePoint, SAP, CRM, vlastní webová aplikace, rozhraní API |
| Aplikační platforma | Například: Windows IIS, Apache v systému Linux, Tomcat, NGINX |
| Členství domény| Plně kvalifikovaný název domény webového serveru (FQDN) |
| Umístění aplikace | Kde se webový server nebo farma nachází ve vaší infrastruktuře |
| Interní přístup | Přesná adresa URL použitá při interním přístupu k aplikaci <br> Pokud se jedná o farmu, který typ vyrovnávání zatížení používá? <br> Určuje, zda aplikace nakreslí obsah z jiných zdrojů než do sebe samé.<br> Určete, jestli aplikace funguje přes objekty WebSockets. |
| Externí přístup | Řešení dodavatele, ve kterém je aplikace již vystavená externě. <br> Adresa URL, kterou chcete použít pro externí přístup. Pokud SharePoint, ujistěte se, že jsou mapování alternativních adres URL nakonfigurována podle [těchto pokynů](/SharePoint/administration/configure-alternate-access-mappings). V takovém případě budete muset definovat externí adresy URL. |
| Veřejný certifikát | Pokud používáte vlastní doménu, opatřete si certifikát s odpovídajícím názvem subjektu. Pokud certifikát existuje, poznamenejte si sériové číslo a umístění, ze kterého se dá získat. |
| Typ ověřování| Typ ověřování podporovaný podporou aplikace, jako je například Basic, ověřování Windows Integration, formuláře založené na hlavičkách a deklarace identity. <br>Pokud je aplikace nakonfigurovaná tak, aby běžela v určitém účtu domény, poznamenejte si plně kvalifikovaný název domény (FQDN) účtu služby.<br> V případě identifikátoru URL a adresy URL pro odpovědi založené na SAML. <br> Pokud je na základě hlaviček, řešení dodavatele a konkrétní požadavek na zpracování typu ověřování. |
| Název skupiny konektorů | Logický název pro skupinu konektorů, která bude určena k poskytnutí trubky a jednotného přihlašování k této back-endové aplikaci. |
| Přístup uživatelů/skupin | Uživatelům nebo skupinám uživatelů, kterým bude aplikace udělena externí přístup. |
| Další požadavky | Poznamenejte si všechny další požadavky na vzdálený přístup nebo zabezpečení, které by měly být zavázány do publikování aplikace. |

Tuto [tabulku inventáře aplikací](https://aka.ms/appdiscovery) si můžete stáhnout, abyste mohli inventář svých aplikací.

### <a name="define-organizational-requirements"></a>Definovat požadavky organizace

Níže jsou uvedené oblasti, ve kterých byste měli definovat obchodní požadavky vaší organizace. Každá oblast obsahuje příklady požadavků.

 **Přístup**

* Vzdálení uživatelé, kteří mají připojené k doméně nebo zařízení připojená k Azure AD, můžou bezpečně přistupovat k publikovaným aplikacím pomocí bezproblémového jednotného přihlašování (SSO).

* Vzdálení uživatelé, kteří mají schválená osobní zařízení, můžou zabezpečeně přistupovat k publikovaným aplikacím, pokud jsou zaregistrované v MFA a zaregistrovali Microsoft Authenticator aplikaci na svém mobilním telefonu jako metodu ověřování.

**Zásady správného řízení**

* Správci můžou definovat a monitorovat životní cyklus přiřazení uživatelů k aplikacím, které jsou publikované prostřednictvím proxy aplikací.

**Zabezpečení**

* K těmto aplikacím mají přístup pouze uživatelé přiřazení k aplikacím prostřednictvím členství ve skupině nebo jednotlivě.

**Výkon**

* V porovnání s přístupem k aplikaci z interní sítě neexistuje žádná snížení výkonu aplikace.

**Činnost koncového uživatele**

* Uživatelé si vědomi, jak přistupovat ke svým aplikacím pomocí známých adres URL společnosti na libovolné platformě zařízení.

**Auditování**
* Správci můžou auditovat činnost přístupu uživatelů.


### <a name="best-practices-for-a-pilot"></a>Osvědčené postupy pro pilotní nasazení

Určete množství času a úsilí potřebné k úplnému vyřazení jedné aplikace pro vzdálený přístup s jednotným přihlašováním (SSO). Provedete to spuštěním pilotního projektu, který považuje počáteční zjišťování, publikování a obecné testování. Použití jednoduché webové aplikace založené na službě IIS, která je již předem nakonfigurovaná pro integrované ověřování systému Windows (IWA), by dokázalo vytvořit směrný plán, protože tato instalace vyžaduje minimální úsilí k úspěšnému nasazení vzdáleného přístupu a jednotného přihlašování.

Následující prvky návrhu by měly zvýšit úspěšnost své pilotní implementace přímo v provozním tenantovi.

**Správa konektorů**:

* Konektory hrají klíčovou roli v rámci poskytování místních přenosů do vašich aplikací. Použití **výchozí** skupiny konektorů je vhodné pro počáteční pilotní testování publikovaných aplikací před jejich vyřazením do produkčního prostředí. Úspěšně testované aplikace se pak dají přesunout do skupin produkčních konektorů.

**Správa aplikací**:

* Vaše zaměstnanci si pravděpodobně pamatují, že externí adresa URL je známá a relevantní. Vyhněte se publikování aplikace pomocí našich předem definovaných přípon msappproxy.net nebo onmicrosoft.com. Místo toho poskytněte dobře známou ověřenou doménu nejvyšší úrovně, s předponou logického názvu hostitele, jako je například *intranet. <customers_domain>. com*.

* Omezení viditelnosti ikony pilotní aplikace na pilotní skupinu skrytím ikony jejího spuštění z portálu Azure MyApps. Po přípravě na produkční prostředí můžete aplikaci nastavit na příslušnou cílovou cílovou skupinu, a to buď ve stejném předprodukčním tenantovi, nebo také publikováním aplikace ve vašem provozním tenantovi.

**Nastavení jednotného přihlašování**: některá nastavení jednotného přihlašování mají specifické závislosti, které můžou určitou dobu trvat, takže se nemusíte zabývat prodlevami při řízení, protože se závislosti budou řešit předem. To zahrnuje připojení hostitelů konektorů k doméně k provádění jednotného přihlašování pomocí omezeného delegování protokolu Kerberos (KCD) a pořizování dalších časově náročných aktivit. Například vytvoření instance přístupu k nástroji PŘÍKAZového řádku, pokud je potřeba jednotné přihlašování založené na hlavičkách.

**TLS mezi hostitelem konektoru a cílovou aplikací**: zabezpečení je nejdůležitější, proto by se měl vždycky používat protokol TLS mezi hostitelem konektoru a cílovými aplikacemi. Zejména pokud je webová aplikace nakonfigurována pro ověřování založené na formulářích (FBA), pak jsou přihlašovací údaje uživatele efektivně přenášeny ve formě prostého textu.

**Implementujte přírůstkově a otestujte každý krok**.
Po publikování aplikace proveďte základní funkční testování, aby se zajistilo splnění všech požadavků na uživatele a firmy pomocí následujících pokynů:

1. Otestuje a ověří obecný přístup k webové aplikaci se zakázaným předběžným ověřením.
2. Pokud byla úspěšná možnost Povolit předběžné ověřování a přiřadit uživatele a skupiny. Otestujte a ověřte přístup.
3. Pak do své aplikace přidejte metodu SSO a znovu ji otestujte, abyste mohli ověřit přístup.
4. Podle potřeby aplikujte zásady podmíněného přístupu a vícefaktorového ověřování. Otestujte a ověřte přístup.

**Nástroje pro řešení potíží**: při odstraňování potíží vždy Začněte tím, že ověříte přístup k publikované aplikaci z prohlížeče na hostiteli konektoru a ověříte, že aplikace funguje podle očekávání. Jednodušší nastavení, což je snazší určit hlavní příčinu, zvažte pokus o reprodukování problémů s minimální konfigurací, jako je například použití jediného konektoru a žádného jednotného přihlašování. V některých případech se nástroje webového ladění, jako je Fiddler Telerik, můžou ukázat jako nepostradatelné pro odstraňování potíží s přístupem nebo problémy s obsahem v aplikacích, ke kterým se přistupuje prostřednictvím proxy serveru. Fiddler může také fungovat jako proxy server, který usnadňuje trasování a ladění provozu pro mobilní platformy, jako jsou iOS a Android, a prakticky cokoli, co je možné nakonfigurovat pro směrování prostřednictvím proxy serveru. Další informace najdete v [Průvodci odstraňováním potíží](application-proxy-troubleshoot.md) .

## <a name="implement-your-solution"></a>Implementace řešení

### <a name="deploy-application-proxy"></a>Nasadit proxy aplikace

Postup nasazení proxy aplikace je popsaný v tomto [kurzu pro přidání místní aplikace pro vzdálený přístup](application-proxy-add-on-premises-application.md). Pokud instalace není úspěšná, vyberte  **řešení potíží s proxy aplikací**  na portálu nebo použijte Průvodce odstraňováním potíží, [kde najdete problémy při instalaci konektoru agenta proxy aplikace](application-proxy-connector-installation-problem.md).

### <a name="publish-applications-via-application-proxy"></a>Publikování aplikací prostřednictvím proxy aplikace

Publikování aplikací předpokládá, že jste splnili všechny požadavky a že máte několik konektorů, které se zobrazují na stránce proxy aplikace jako registrované a aktivní.

Můžete také publikovat aplikace pomocí [prostředí PowerShell](/powershell/module/azuread/?view=azureadps-2.0-preview).

Níže jsou uvedeny některé osvědčené postupy při publikování aplikace:

* **Použijte skupiny konektorů**: přiřaďte skupinu konektorů, která je určená pro publikování každé příslušné aplikace. Doporučujeme, aby každá skupina konektorů měla aspoň dva konektory, aby se zajistila vysoká dostupnost a škálování. Pro případ, že budete chtít mít k dispozici tři konektory, může být nutné provozovat počítač v jakémkoli bodě. Kromě toho si přečtěte téma [publikování aplikací v samostatných sítích a umístěních pomocí skupin konektorů](application-proxy-connector-groups.md) , abyste viděli, jak můžete použít skupiny konektorů k segmentaci konektorů podle sítě nebo umístění.

* **Nastavit časový limit aplikace back-endu**: Toto nastavení je užitečné ve scénářích, kde aplikace může vyžadovat více než 75 sekund ke zpracování transakce klienta. Například když klient odešle dotaz do webové aplikace, která funguje jako front-end databáze. Front-end pošle tento dotaz do svého back-end databázového serveru a čeká na odpověď, ale v době, kdy obdrží odpověď, vyprší časový limit konverzace na straně klienta. Nastavení časového limitu na dlouhé poskytuje 180 sekund, než se dokončí delší transakce.

* **Použít vhodné typy souborů cookie**

   * **Soubor cookie jen pro protokol HTTP**: poskytuje dodatečné zabezpečení tím, že proxy aplikací zahrnuje příznak HttpOnly v hlavičkách odpovědí HTTP Set-cookie. Toto nastavení pomáhá zmírnit zneužití, jako je například skriptování mezi weby (XSS). U klientů/uživatelských agentů, kteří potřebují přístup k souboru cookie relace, nechte tuto hodnotu nastavenou na ne. Například klient RDP/MTSC se připojuje k Brána vzdálené plochy publikované prostřednictvím proxy aplikací.

   * **Zabezpečený soubor cookie**: když je v zabezpečeném atributu nastavený soubor cookie, bude uživatel (klientská aplikace) do požadavků HTTP zahrnovat jenom soubor cookie, pokud se požadavek přenáší přes zabezpečený kanál TLS. To pomáhá zmírnit riziko napadení souborů cookie prostřednictvím nešifrovaných textových kanálů, takže by mělo být povoleno.

   * **Trvalý soubor cookie**: umožňuje, aby soubor cookie relace proxy aplikace trval mezi zavíracími dny v prohlížeči, a to tak, aby jeho platnost nevypršela, nebo se odstraní. Používá se ve scénářích, kde bohatá aplikace, jako je například Office, přistupuje k dokumentu v publikované webové aplikaci, aniž by uživatel musel znovu zobrazit výzvu k ověření. Povolit s upozorněním, protože trvalé soubory cookie můžou nakonec opustit službu, která má neoprávněný přístup, pokud se nepoužije ve spojení s jinými kompenzačními ovládacími prvky. Toto nastavení by se mělo používat jenom pro starší aplikace, které nemůžou sdílet soubory cookie mezi procesy. Je lepší aktualizovat aplikaci tak, aby zpracovávala soubory cookie sdílení mezi procesy namísto použití tohoto nastavení.

* **Přeložit adresy URL v hlavičkách**: tuto možnost povolíte u scénářů, kde interní DNS nejde nakonfigurovat tak, aby odpovídaly veřejnému oboru názvů organizace (a. k. dělené DNS). Pokud vaše aplikace nevyžaduje v žádosti klienta hlavičku původního hostitele, nechte tuto hodnotu nastavenou na Ano. Alternativou je, že konektor používá plně kvalifikovaný název domény v interní adrese URL pro směrování skutečného provozu a plně kvalifikovaný název domény v externí adrese URL jako hlavičku hosta. Ve většině případů by tato alternativa měla aplikaci při vzdáleném přístupu umožňovat fungování v normálním režimu, ale uživatelé ztratí výhody, které jsou v & mimo adresu URL.

* **Přeložit adresy URL v těle aplikace**: Pokud chcete, aby byly odkazy z této aplikace přeloženy v odpovědích zpět na klienta, zapněte překlad odkazu na tělo aplikace pro aplikaci. Pokud je tato funkce povolená, poskytuje se nejlepší snaha při překladu všech vnitřních odkazů, které proxy aplikace najde v odpovědích HTML a CSS, které se vracejí klientům. To je užitečné při publikování aplikací, které v obsahu obsahují buď pevně zakódované absolutní odkazy nebo krátkých odkazů na názvy NetBIOS, nebo aplikací s obsahem, který odkazuje na jiné místní aplikace.

U scénářů, ve kterých se publikovaná aplikace odkazuje na jiné publikované aplikace, povolte Překlad propojení pro každou aplikaci, abyste měli kontrolu nad uživatelským prostředím na úrovni jednotlivých aplikací.

Předpokládejme například, že máte tři aplikace publikované prostřednictvím proxy aplikací, které všechny vzájemně odkazují: výhody, výdaje a cestování a navíc čtvrtou aplikaci, zpětnou vazbu, která není publikovaná prostřednictvím proxy aplikací.

![Obrázek 1](media/App-proxy-deployment-plan/link-translation.png)

Pokud povolíte Překlad propojení pro aplikaci s výhodami, odkazy na výdaje a cestovné se přesměrují na externí adresy URL těchto aplikací, aby k nim měli přístup uživatelé, kteří přistupují k aplikacím mimo podnikovou síť. Odkazy z výdajů a cestování zpátky na výhody nefungují, protože u těchto dvou aplikací není povolený Překlad propojení. Odkaz na zpětnou vazbu není přesměrován, protože není k dispozici externí adresa URL, takže uživatelé, kteří používají aplikaci výhody, nebudou mít přístup k aplikaci pro zpětnou vazbu z oblasti mimo podnikovou síť. Přečtěte si podrobné informace o [překladu odkazů a dalších možnostech přesměrování](application-proxy-configure-hard-coded-link-translation.md).

### <a name="access-your-application"></a>Přístup k aplikaci

K dispozici je několik možností pro správu přístupu k prostředkům publikovaným na proxy aplikací, takže vyberte nejvhodnější pro daný scénář a škálovatelnost. Mezi běžné přístupy patří: použití místních skupin, které se synchronizují prostřednictvím Azure AD Connect, vytváření dynamických skupin ve službě Azure AD na základě atributů uživatelů pomocí skupin samoobslužných služeb, které jsou spravované vlastníkem prostředku, nebo kombinací všech těchto. Další informace o výhodách jednotlivých prostředků najdete v tématu věnovaném propojeným prostředkům.

Nejpřímější způsob, jak přiřadit uživatelům přístup k aplikaci, přichází k možnostem **uživatelů a skupin** v levém podokně publikované aplikace a k přímému přiřazení skupin nebo jednotlivců.

![Obrázek 24](media/App-proxy-deployment-plan/add-user.png)

Uživatelům můžete taky dovolit Samoobslužný přístup k vaší aplikaci tím, že přiřadíte skupinu, které nejsou aktuálně členem, a nakonfigurujete možnosti samoobslužného ovládání.

![Obrázek 25](media/App-proxy-deployment-plan/allow-access.png)

Pokud je tato možnost povolená, uživatelé se pak budou moci přihlásit k portálu MyApp a požádat o přístup a buď automaticky schválit a přidat do již povolené samoobslužné skupiny, nebo potřebují schválení od určeného schvalovatele.

Uživatelům typu Host se dá také [pozvat k přístupu k interním aplikacím, které jsou publikované prostřednictvím proxy aplikací prostřednictvím Azure AD B2B](../external-identities/add-users-information-worker.md).

U místních aplikací, které jsou běžně přístupné anonymně, nevyžadují žádné ověřování, můžete chtít zakázat možnost, která se nachází ve **vlastnostech** aplikace.

![Obrázek 26](media/App-proxy-deployment-plan/assignment-required.png)


Když necháte tuto možnost nastavenou na ne, umožníte uživatelům přístup k místní aplikaci prostřednictvím proxy serveru Aplikace Azure AD bez oprávnění, takže používejte s opatrností.

Po publikování aplikace by měla být přístupná zadáním její externí adresy URL v prohlížeči nebo její ikonou na [https://myapps.microsoft.com](https://myapps.microsoft.com/) .

### <a name="enable-pre-authentication"></a>Povolit předběžné ověřování

Ověřte, že je vaše aplikace přístupná prostřednictvím proxy aplikace, která k ní přistupuje přes externí adresu URL.

1. Přejděte na **Azure Active Directory**  >  **podnikové aplikace**  >  **všechny aplikace** a vyberte aplikaci, kterou chcete spravovat.

2. Vyberte **proxy aplikace**.

3. V poli **před ověřením** vyberte v rozevíracím seznamu **Azure Active Directory** a vyberte **Uložit**.

S povoleným předběžným ověřením služba Azure AD nejdřív vyzve uživatele k ověření a pokud je nakonfigurované jednotné přihlašování, pak back-end aplikace taky ověří uživatele před tím, než se udělí přístup k aplikaci. Změna režimu předběžného ověřování z průchodu na Azure AD také nakonfiguruje externí adresu URL s protokolem HTTPS, takže všechny aplikace původně nakonfigurované pro protokol HTTP budou teď zabezpečené pomocí protokolu HTTPS.

### <a name="enable-single-sign-on"></a>Povolit jeden Sign-On

Jednotné přihlašování poskytuje nejlepší možné uživatelské prostředí a zabezpečení, protože se k Azure AD potřebují přihlašovat jenom jednou. Po předběžném ověření je jednotné přihlašování provedeno pomocí konektoru proxy aplikací ověřujícího místní aplikaci jménem uživatele. Back-end aplikace zpracovává přihlášení, jako by šlo o samotného uživatele.

Když zvolíte možnost **Passthrough** , umožníte uživatelům přístup k publikované aplikaci, aniž byste museli provádět ověřování ve službě Azure AD.

Provádění jednotného přihlašování je možné jenom v případě, že Azure AD může identifikovat uživatele, který žádá o přístup k prostředku, takže vaše aplikace musí být nakonfigurovaná tak, aby předem ověřovala uživatele s Azure AD při přístupu k funkci jednotného přihlašování. v opačném případě se možnosti jednotného přihlašování zakážou.

Načtěte si [jednotné přihlašování k aplikacím ve službě Azure AD](what-is-single-sign-on.md) , které vám pomůžou při konfiguraci vašich aplikací zvolit nejvhodnější metodu jednotného přihlašování.

###  <a name="working-with-other-types-of-applications"></a>Práce s jinými typy aplikací

Azure Proxy aplikací služby AD také podporuje aplikace vyvinuté pro použití [knihovny Microsoft Authentication Library (MSAL)](../develop/v2-overview.md). Podporuje nativní klientské aplikace tím, že spotřebovávají vydané tokeny Azure AD obdržené v hlavičkových požadavcích klienta, aby prováděly předběžné ověřování jménem uživatelů.

Další informace o dostupných konfiguracích proxy aplikací najdete v tématu [publikování nativních a mobilních klientských aplikací](./application-proxy-configure-native-client-application.md) a [aplikací založených na deklaracích](./application-proxy-configure-for-claims-aware-applications.md) .

### <a name="use-conditional-access-to-strengthen-security"></a>Použití podmíněného přístupu k posílení zabezpečení

Zabezpečení aplikací vyžaduje pokročilou sadu funkcí zabezpečení, které mohou chránit a reagovat na komplexní hrozby v místním prostředí i v cloudu. Útočníci nejčastěji získávají přístup k podnikové síti prostřednictvím slabých, výchozích nebo odcizených uživatelských přihlašovacích údajů.  Zabezpečení založené na identitách od Microsoftu omezuje použití odcizených přihlašovacích údajů správou a ochranou privilegovaných i neprivilegovaných identit.

K podpoře Proxy aplikací služby AD Azure je možné použít následující funkce:

* Podmíněný přístup na základě uživatele a umístění: chránit citlivá data omezením přístupu uživatelů na základě geografického umístění nebo IP adresy se [zásadami podmíněného přístupu na základě umístění](../conditional-access/location-condition.md).

* Podmíněný přístup podle zařízení: Zajistěte, aby k podnikovým datům měli přístup jenom registrovaná, schválená a vyhovující zařízení s [podmíněným přístupem na základě zařízení](../conditional-access/require-managed-devices.md).

* Podmíněný přístup založený na aplikaci: Pokud se uživatel nenachází v podnikové síti, nemusí se tato práce zastavit. [Zabezpečený přístup k podnikovému cloudu a místním aplikacím](../conditional-access/app-based-conditional-access.md) a udržování řízení pomocí podmíněného přístupu.

* Podmíněný přístup na základě rizik: Chraňte svá data před škodlivými hackery pomocí [zásad podmíněného přístupu na základě rizik](https://www.microsoft.com/cloud-platform/conditional-access) , které je možné použít pro všechny aplikace a všechny uživatele, ať už místně, nebo v cloudu.

* Azure AD moje aplikace: u nasazené služby proxy aplikací a aplikací, které jsou bezpečně publikované, nabízejí uživatelům jednoduché centrum pro zjišťování a přístup ke všem jejich aplikacím. Zvyšte produktivitu díky samoobslužným funkcím, jako je třeba možnost požádat o přístup k novým aplikacím a skupinám nebo spravovat přístup k těmto prostředkům jménem jiné prostřednictvím [mých aplikací](https://aka.ms/AccessPanelDPDownload).

## <a name="manage-your-implementation"></a>Správa implementace

### <a name="required-roles"></a>Požadované role

Společnost Microsoft si povede zásadu, jak udělit nejnižší možné oprávnění k provádění potřebných úkolů s Azure AD. [Projděte si různé role Azure, které jsou k dispozici,](../roles/permissions-reference.md) a vyberte tu, kterou máte k dispozici pro všechny potřeby. Některé role může být potřeba použít dočasně a odebrat po dokončení nasazení.

| Obchodní role| Obchodní úkoly| Role Azure AD |
|---|---|---|
| Správce helpdesku | Obvykle omezena na opravňující uživatele, kteří ohlásili problémy a provádějí omezené úlohy, jako je změna hesel uživatelů, zrušení platnosti aktualizačních tokenů a stav služby monitorování. | Správce helpdesku |
| Správce identit| Přečtěte si sestavy přihlášení k Azure AD a protokoly auditu, které vám umožní ladit problémy související s proxy aplikacemi.| Čtenář zabezpečení |
| Vlastník aplikace| Vytvářejte a spravujte všechny aspekty podnikových aplikací, registrací aplikací a nastavení proxy aplikací.| Správce aplikace |
| Správce infrastruktury | Vlastník změna certifikátu | Správce aplikace |

Minimalizace počtu uživatelů, kteří mají přístup k zabezpečeným informacím nebo prostředkům, pomůžete snížit pravděpodobnost, že škodlivý přístup získá škodlivá osoba, nebo autorizovaný uživatel neúmyslně ovlivnit citlivý prostředek.

Uživatelé ale stále potřebují provádět privilegované operace, aby se vynutily zásady [Privileged Identity Management](../privileged-identity-management/pim-configure.md) založené na JIT, aby poskytovaly privilegovaný přístup k prostředkům Azure na vyžádání a Azure AD byl doporučeným přístupem k efektivní správě přístupu a auditování pro správu.

### <a name="reporting-and-monitoring"></a>Sledování a vytváření sestav

Azure AD poskytuje další přehledy o využití aplikací a provozním stavu vaší organizace prostřednictvím [protokolů auditu a sestav](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). Proxy aplikace také velmi usnadňuje monitorování konektorů z portálu Azure AD a protokolů událostí systému Windows.

#### <a name="application-audit-logs"></a>Protokoly auditu aplikací

Tyto protokoly poskytují podrobné informace o přihlášení k aplikacím nakonfigurovaným pomocí proxy aplikací a zařízení a uživatele, který přistupuje k aplikaci. [Protokoly auditu](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) se nacházejí v Azure Portal a v [rozhraní audit API](/graph/api/resources/directoryaudit?view=graph-rest-beta) pro export. Navíc jsou k dispozici také [sestavy využití a přehled](../reports-monitoring/concept-usage-insights-report.md?context=azure/active-directory/manage-apps/context/manage-apps-context) pro vaši aplikaci.

#### <a name="application-proxy-connector-monitoring"></a>Monitorování konektoru proxy aplikací

Konektory a služba se budou starat o všechny úlohy s vysokou dostupností. Stav konektorů můžete monitorovat na stránce proxy aplikace na portálu Azure AD. Další informace o maintainence konektoru najdete v tématu [vysvětlení konektorů služby Azure proxy aplikací služby AD](./application-proxy-connectors.md#maintenance).

![Příklad: konektory Azure Proxy aplikací služby AD](./media/application-proxy-connectors/app-proxy-connectors.png)

#### <a name="windows-event-logs-and-performance-counters"></a>Protokoly událostí systému Windows a čítače výkonu

Konektory mají protokoly správců i relací. Protokoly správců zahrnují klíčové události a jejich chyby. Protokoly relací zahrnují všechny transakce a jejich podrobnosti o zpracování. Protokoly a čítače se nacházejí v protokolech událostí systému Windows. Další informace najdete v tématu [vysvětlení konektorů služby Azure proxy aplikací služby AD](./application-proxy-connectors.md#under-the-hood). Podle tohoto [kurzu nakonfigurujte zdroje dat protokolu událostí v Azure monitor](../../azure-monitor/platform/data-sources-windows-events.md).

### <a name="troubleshooting-guide-and-steps"></a>Průvodce odstraňováním potíží a postup

Přečtěte si další informace o běžných problémech a o tom, jak je vyřešit pomocí našeho průvodce pro [řešení](application-proxy-troubleshoot.md) chybových zpráv.

Následující články se týkají běžných scénářů, které lze použít také k vytvoření průvodců odstraňování potíží pro vaši organizaci podpory.

* [Potíže při zobrazování stránky aplikace](application-proxy-page-appearance-broken-problem.md)
* [Načítání aplikace trvá příliš dlouho](application-proxy-page-load-speed-problem.md)
* [Odkazy na stránce aplikace nefungují](application-proxy-page-links-broken-problem.md)
* [Jaké porty je potřeba odemknout pro mou aplikaci](application-proxy-add-on-premises-application.md)
* [Ve skupině konektorů není žádný fungující konektor pro mou aplikaci](application-proxy-connectivity-no-working-connector.md)
* [Konfigurace na portálu pro správu](application-proxy-config-how-to.md)
* [Konfigurace jednotného přihlašování k mé aplikaci](application-proxy-config-sso-how-to.md)
* [Potíže při vytváření aplikace na portálu pro správu](application-proxy-config-problem.md)
* [Konfigurace omezeného delegování Kerberos](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Konfigurace s využitím PingAccess](./application-proxy-ping-access-publishing-guide.md)
* [Chyba typu Není přístup k podnikové aplikaci](application-proxy-sign-in-bad-gateway-timeout-error.md)
* [Potíže při instalaci konektoru agenta proxy aplikací](application-proxy-connector-installation-problem.md)
* [Potíže s přihlašováním](application-sign-in-problem-on-premises-application-proxy.md)
