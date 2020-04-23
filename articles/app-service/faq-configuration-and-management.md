---
title: Časté otázky ke konfiguraci
description: Získejte odpovědi na nejčastější dotazy týkající se problémů s konfigurací a správou služby Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 810219bca5c75e3ee7e65c6b7302531bca89788d
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869966"
---
# <a name="configuration-and-management-faqs-for-web-apps-in-azure"></a>Nejčastější dotazy ke konfiguraci a správě webových aplikací v Azure

Tento článek obsahuje odpovědi na nejčastější dotazy týkající se problémů s konfigurací a správou [funkce Webové aplikace služby Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="are-there-limitations-i-should-be-aware-of-if-i-want-to-move-app-service-resources"></a>Existují omezení, o kterých bych měl vědět, pokud chci přesunout prostředky služby App Service?

Pokud máte v plánu přesunout prostředky služby App Service do nové skupiny prostředků nebo předplatného, existuje několik omezení, které je třeba znát. Další informace naleznete v tématu [Omezení služby App Service](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md).

## <a name="how-do-i-use-a-custom-domain-name-for-my-web-app"></a>Jak se používá vlastní název domény pro svou webovou aplikaci?

Odpovědi na časté otázky týkající se používání vlastního názvu domény ve webové aplikaci Azure najdete v našem sedmiminutovém videu [Přidání vlastního názvu domény](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name). Video nabízí návod, jak přidat vlastní název domény. Popisuje, jak používat vlastní adresu URL namísto adresy URL *.azurewebsites.net s webovou aplikací Služby aplikací. Můžete také zobrazit podrobný [návod, jak mapovat vlastní název domény](app-service-web-tutorial-custom-domain.md).


## <a name="how-do-i-purchase-a-new-custom-domain-for-my-web-app"></a>Jak si koupím novou vlastní doménu pro webovou aplikaci?

Informace o tom, jak zakoupit a nastavit vlastní doménu pro webovou aplikaci Služby Aplikace, najdete [v tématu Nákup a konfigurace vlastního názvu domény ve službě App Service](manage-custom-dns-buy-domain.md).


## <a name="how-do-i-upload-and-configure-an-existing-tlsssl-certificate-for-my-web-app"></a>Jak nahraji a nakonfiguruji existující certifikát TLS/SSL pro svou webovou aplikaci?

Informace o tom, jak nahrát a nastavit existující vlastní certifikát TLS/SSL, najdete v článku [Přidání certifikátu TLS/SSL do aplikace App Service](configure-ssl-certificate.md).


## <a name="how-do-i-purchase-and-configure-a-new-tlsssl-certificate-in-azure-for-my-web-app"></a>Jak si v Azure pro webovou aplikaci koupím a nakonfiguruji nový certifikát TLS/SSL?

Informace o tom, jak zakoupit a nastavit certifikát TLS/SSL pro webovou aplikaci Služby aplikace, najdete [v tématu Přidání certifikátu TLS/SSL do aplikace App Service](configure-ssl-certificate.md).


## <a name="how-do-i-move-application-insights-resources"></a>Jak se přesunout prostředky Application Insights?

V současné době Azure Application Insights nepodporuje operaci přesunutí. Pokud původní skupina prostředků obsahuje prostředek Application Insights, nelze tento prostředek přesunout. Pokud při pokusu o přesunutí aplikace App Service zahrnete prostředek Application Insights, celá operace přesunutí se nezdaří. Přehledy aplikací a plán služby App Service však nemusí být ve stejné skupině prostředků jako aplikace, aby aplikace fungovala správně.

Další informace naleznete v tématu [Omezení služby App Service](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md).

## <a name="where-can-i-find-a-guidance-checklist-and-learn-more-about-resource-move-operations"></a>Kde najdu kontrolní seznam pokynů a další informace o operacích přesunu prostředků?

[Omezení služby App Service](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md) ukazuje, jak přesunout prostředky do nového předplatného nebo do nové skupiny prostředků ve stejném předplatném. Můžete získat informace o kontrolním seznamu přesunu prostředků, zjistit, které služby podporují operaci přesunutí a získat další informace o omezeních služby App Service a dalších tématech.

## <a name="how-do-i-set-the-server-time-zone-for-my-web-app"></a>Jak nastavím časové pásmo serveru pro webovou aplikaci?

Nastavení časového pásma serveru pro webovou aplikaci:

1. Na webu Azure Portal přejděte v předplacené službě App Service do nabídky **Nastavení aplikace.**
2. V části **Nastavení aplikace**přidejte toto nastavení:
    * Klíč = WEBSITE_TIME_ZONE
    * Hodnota = *požadované časové pásmo*
3. Vyberte **Uložit**.

Pro služby aplikace, které běží na Windows, naleznete ve sloupci **Časové pásmo** v článku [Výchozí časová pásma](https://docs.microsoft.com/windows-hardware/manufacture/desktop/default-time-zones) pro přijaté hodnoty. Pro služby App, které běží na Linuxu, nastavte [název databáze TZ](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones) jako hodnotu časového pásma. Zde je příklad názvu databáze TZ: America/Adak.

## <a name="why-do-my-continuous-webjobs-sometimes-fail"></a>Proč se moje průběžné webjobs někdy nezdaří?

Ve výchozím nastavení jsou webové aplikace uvolněny, pokud jsou nečinné po nastavenou dobu. To umožňuje systému šetřit prostředky. V základních a standardních plánech můžete zapnout nastavení **Vždy zapnuto,** aby webová aplikace byla neustále načtená. Pokud vaše webová aplikace běží souvislé WebJobs, měli byste zapnout **vždy zapnuto**, nebo webjobs nemusí běžet spolehlivě. Další informace naleznete [v tématu Vytvoření nepřetržitě spuštěné webové úlohy](webjobs-create.md#CreateContinuous).

## <a name="how-do-i-get-the-outbound-ip-address-for-my-web-app"></a>Jak získám odchozí IP adresu webové aplikace?

Získání seznamu odchozích IP adres webové aplikace:

1. Na webu Azure Portal přejděte v okně webové aplikace do nabídky **Vlastnosti.**
2. Vyhledejte **odchozí adresy IP**.

Zobrazí se seznam odchozích adres IP.

Informace o tom, jak získat odchozí IP adresu, pokud je váš web hostovaný v prostředí [Služby](environment/app-service-app-service-environment-network-architecture-overview.md#outbound-network-addresses)App Service, najdete v tématu Odchozí síťové adresy .

## <a name="how-do-i-get-a-reserved-or-dedicated-inbound-ip-address-for-my-web-app"></a>Jak získám vyhrazenou nebo vyhrazenou příchozí IP adresu pro svou webovou aplikaci?

Pokud chcete nastavit vyhrazenou nebo vyhrazenou IP adresu pro příchozí volání na web aplikace Azure, nainstalujte a nakonfigurujte certifikát TLS/SSL založený na IP adresách.

Všimněte si, že chcete-li použít vyhrazenou nebo vyhrazenou IP adresu pro příchozí volání, váš plán služby App Service musí být v plánu základní nebo vyšší služby.

## <a name="can-i-export-my-app-service-certificate-to-use-outside-azure-such-as-for-a-website-hosted-elsewhere"></a>Můžu exportovat certifikát služby App Service, aby se používal mimo Azure, třeba pro web hostovaný jinde? 

Ano, můžete je exportovat a používat mimo Azure. Další informace najdete v [tématu Nejčastější dotazy k certifikátům služby App Service a vlastním doménám](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).

## <a name="can-i-export-my-app-service-certificate-to-use-with-other-azure-cloud-services"></a>Můžu exportovat certifikát služby App Service pro použití s jinými cloudovými službami Azure?

Portál poskytuje prvotřídní prostředí pro nasazení certifikátu služby App Service prostřednictvím azure key vaultu do aplikací App Service. Od zákazníků jsme však dostávali žádosti o použití těchto certifikátů mimo platformu služby App Service, například s virtuálními počítači Azure. Informace o tom, jak vytvořit místní kopii Certifikátu služby App Service, abyste mohli certifikát používat s jinými prostředky Azure, najdete [v tématu Vytvoření místní kopie PFX certifikátu služby App Service](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/).

Další informace najdete v [tématu Nejčastější dotazy k certifikátům služby App Service a vlastním doménám](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).


## <a name="why-do-i-see-the-message-partially-succeeded-when-i-try-to-back-up-my-web-app"></a>Proč se při pokusu o zálohování webové aplikace zobrazuje zpráva Částečně úspěšná?

Častou příčinou selhání zálohování je, že některé soubory jsou používány aplikací. Soubory, které jsou používány, jsou při zálohování uzamčeny. Tím zabráníte zálohování těchto souborů a může dojít k částečnému úspěšnému stavu. Můžete potenciálně zabránit tomu, aby k tomu došlo vyloučením souborů z procesu zálohování. Můžete si vybrat zálohovat pouze to, co je potřeba. Další informace najdete v [tématu Zálohování pouze důležitých částí vašeho webu pomocí webových aplikací Azure](https://zainrizvi.io/blog/creating-partial-backups-of-your-site-with-azure-web-apps/).

## <a name="how-do-i-remove-a-header-from-the-http-response"></a>Jak odeberu záhlaví z odpovědi HTTP?

Chcete-li odebrat záhlaví z odpovědi HTTP, aktualizujte soubor web.config webu. Další informace najdete [v tématu Odebrání záhlaví standardních serverů na webech Azure](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

## <a name="is-app-service-compliant-with-pci-standard-30-and-31"></a>Je služba App Service kompatibilní se standardy PCI Standard 3.0 a 3.1?

V současné době je funkce Webové aplikace služby Azure App Service v souladu se standardem PCI Data Security Standard (DSS) verze 3.0 úrovně 1. PCI DSS verze 3.1 je na našem plánu. Plánování již probíhá, jak bude pokračovat přijetí nejnovějšínormy.

Certifikace PCI DSS verze 3.1 vyžaduje zakázání zabezpečení transportní vrstvy (TLS) 1.0. V současné době zakázání TLS 1.0 není možnost pro většinu plánů služby App Service. Pokud však používáte prostředí služby App Service nebo jste ochotni migrovat své úlohy do prostředí služby App Service, můžete získat větší kontrolu nad svým prostředím. To zahrnuje zakázání TLS 1.0 kontaktováním podpory Azure. V blízké budoucnosti plánujeme zpřístupnit tato nastavení uživatelům.

Další informace najdete v tématu [Kompatibilita webových aplikací služby Microsoft Azure App Service s PCI Standard 3.0 a 3.1](https://support.microsoft.com/help/3124528).

## <a name="how-do-i-use-the-staging-environment-and-deployment-slots"></a>Jak se používá pracovní prostředí a sloty nasazení?

V plánech Standardní a Premium App Service, když nasadíte webovou aplikaci do služby App Service, můžete nasadit do samostatného slotu pro nasazení namísto do výchozího produkčního slotu. Sloty pro nasazení jsou živé webové aplikace, které mají své vlastní názvy hostitelů. Obsah webové aplikace a konfigurační prvky lze přepínat mezi dvěma sloty nasazení, včetně produkčního slotu.

Další informace o používání slotů nasazení najdete [v tématu Nastavení pracovního prostředí ve službě App Service](deploy-staging-slots.md).

## <a name="how-do-i-access-and-review-webjob-logs"></a>Jak lze získat přístup k protokolům webjobů a zkontrolovat je?

Kontrola protokolů webjobů:

1. Přihlaste se na [své webové stránky Kudu](https://*yourwebsitename*.scm.azurewebsites.net).
2. Vyberte webovou úlohu.
3. Vyberte tlačítko **Přepnout výstup.**
4. Chcete-li stáhnout výstupní soubor, vyberte odkaz **Stáhnout.**
5. Pro jednotlivé spuštění vyberte **možnost Individuální vyvolání**.
6. Vyberte tlačítko **Přepnout výstup.**
7. Vyberte odkaz ke stažení.

## <a name="im-trying-to-use-hybrid-connections-with-sql-server-why-do-i-see-the-message-systemoverflowexception-arithmetic-operation-resulted-in-an-overflow"></a>Pokouším se použít hybridní připojení se serverem SQL Server. Proč se zobrazuje zpráva "System.OverflowException: Aritmetická operace vedla k přetečení"?

Pokud používáte hybridní připojení pro přístup k serveru SQL Server, aktualizace microsoft .NET na 10 května 2016, může způsobit selhání připojení. Může se zobrazit tato zpráva:

```
Exception: System.Data.Entity.Core.EntityException: The underlying provider failed on Open. —> System.OverflowException: Arithmetic operation resulted in an overflow. or (64 bit Web app) System.OverflowException: Array dimensions exceeded supported range, at System.Data.SqlClient.TdsParser.ConsumePreLoginHandshake
```

### <a name="resolution"></a>Řešení

Výjimka byla způsobena problémem s správcem hybridního připojení, který byl od té doby opraven. Chcete-li tento problém vyřešit, [aktualizujte správce hybridního připojení.](https://go.microsoft.com/fwlink/?LinkID=841308)

## <a name="how-do-i-add-a-url-rewrite-rule"></a>Jak přidám pravidlo pro přepis adresy URL?

Chcete-li přidat pravidlo přepisu adresy URL, vytvořte soubor web.config s příslušnými položkami konfigurace ve složce **wwwroot.** Další informace najdete [v tématu Azure App Services: Principy přepisu adres URL](https://blogs.msdn.microsoft.com/madhurabharadwaj/2018/06/01/azure-app-services-understanding-url-re-write/).

## <a name="how-do-i-control-inbound-traffic-to-app-service"></a>Jak můžu řídit příchozí provoz do Služby App Service?

Na úrovni webu máte dvě možnosti pro řízení příchozího provozu do služby App Service:

* Zapněte dynamická omezení IP adres. Informace o tom, jak zapnout dynamická omezení IP adres, najdete v [tématu Omezení IP adres a domén pro weby Azure](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/).
* Zapněte zabezpečení modulu. Informace o tom, jak zapnout zabezpečení modulů, najdete v tématu [Brána firewall webové aplikace ModSecurity na webech Azure](https://azure.microsoft.com/blog/modsecurity-for-azure-websites/).

Pokud používáte prostředí Služby App Service, můžete použít [bránu firewall Barracuda](https://azure.microsoft.com/blog/configuring-barracuda-web-application-firewall-for-azure-app-service-environment/).

## <a name="how-do-i-block-ports-in-an-app-service-web-app"></a>Jak zablokuji porty ve webové aplikaci App Service?

Ve sdíleném prostředí klienta služby App Service není možné blokovat konkrétní porty z důvodu povahy infrastruktury. TCP porty 4020, 4022 a 4024 může být také otevřena pro vzdálené ladění sady Visual Studio.

V prostředí služby App Service máte plnou kontrolu nad příchozím a odchozím provozem. Skupiny zabezpečení sítě můžete použít k omezení nebo zablokování určitých portů. Další informace o prostředí služby App Service [najdete v tématu Zavedení prostředí služby App Service](https://azure.microsoft.com/blog/introducing-app-service-environment/).

## <a name="how-do-i-capture-an-f12-trace"></a>Jak lze zachytit trasování F12?

Máte dvě možnosti pro zachycení trasování F12:

* Trasování HTTP F12
* Výstup konzoly F12

### <a name="f12-http-trace"></a>Trasování HTTP F12

1. V Aplikaci Internet Explorer přejděte na svůj web. Před dalším krokem je důležité se přihlásit. V opačném případě trasování F12 zachycuje citlivá data přihlášení.
2. Stiskněte klávesu F12.
3. Ověřte, zda je vybraná karta **Síť,** a pak vyberte zelené tlačítko **Přehrát.**
4. Proveďte kroky, které reprodukují problém.
5. Vyberte červené tlačítko **Zastavit.**
6. Vyberte tlačítko **Uložit** (ikona disku) a uložte soubor HAR (v aplikaci Internet Explorer a Microsoft Edge) *nebo* klepněte pravým tlačítkem myši na soubor HAR a pak vyberte **Uložit jako HAR s obsahem** (v Chromu).

### <a name="f12-console-output"></a>Výstup konzoly F12

1. Vyberte kartu **Konzola.**
2. Pro každou kartu, která obsahuje více než nula položek, vyberte kartu (**Chyba**, **Upozornění**nebo **Informace**). Pokud karta není vybraná, ikona karty je šedá nebo černá, když přesunete kurzor od ní.
3. Klepněte pravým tlačítkem myši do oblasti zprávy v podokně a pak vyberte **kopírovat vše**.
4. Vložte zkopírovaný text do souboru a uložte soubor.

Chcete-li zobrazit soubor HAR, můžete použít [prohlížeč HAR](http://www.softwareishard.com/har/viewer/).

## <a name="why-do-i-get-an-error-when-i-try-to-connect-an-app-service-web-app-to-a-virtual-network-that-is-connected-to-expressroute"></a>Proč se při pokusu o připojení webové aplikace App Service k virtuální síti připojené k ExpressRoute zobrazí chyba?

Pokud se pokusíte připojit webovou aplikaci Azure k virtuální síti, která je připojená k Azure ExpressRoute, nezdaří se. Zobrazí se následující zpráva: "Brána není brána VPN."

V současné době nelze mít připojení VPN z bodu na místo k virtuální síti, která je připojena k ExpressRoute. Vpn point-to-site a ExpressRoute nemohou existovat společně pro stejnou virtuální síť. Další informace naleznete v tématu [ExpressRoute a omezení připojení VPN mezi lokalitami](../expressroute/expressroute-howto-coexist-classic.md#limits-and-limitations).

## <a name="how-do-i-connect-an-app-service-web-app-to-a-virtual-network-that-has-a-static-routing-policy-based-gateway"></a>Jak připojím webovou aplikaci služby App Service k virtuální síti, která má statickou směrovací bránu (založenou na zásadách)?

V současné době není podporováno připojení webové aplikace služby App Service k virtuální síti, která má statickou bránu směrování (založené na zásadách). Pokud vaše cílová virtuální síť již existuje, musí mít před připojením k aplikaci povolenou síť VPN z bodu na lokalitu s dynamickou směrovací bránou. Pokud je brána nastavena na statické směrování, nelze povolit síť VPN z bodu na lokalitu. 

Další informace najdete [v tématu Integrace aplikace s virtuální sítí Azure](web-sites-integrate-with-vnet.md).

## <a name="in-my-app-service-environment-why-can-i-create-only-one-app-service-plan-even-though-i-have-two-workers-available"></a>Proč můžu v prostředí služby App Service vytvořit jenom jeden plán služby App Service, i když mám k dispozici dva pracovníky?

Chcete-li poskytnout odolnost proti chybám, prostředí služby App Service vyžaduje, aby každý fond pracovních prostředků potřeboval alespoň jeden další výpočetní prostředek. Další výpočetní prostředek nelze přiřadit pracovní vytížení.

Další informace naleznete v [tématu Jak vytvořit prostředí služby App Service](environment/app-service-web-how-to-create-an-app-service-environment.md).

## <a name="why-do-i-see-timeouts-when-i-try-to-create-an-app-service-environment"></a>Proč se při pokusu o vytvoření prostředí služby App Service zobrazují časové osy?

Někdy se nezdaří vytvoření prostředí služby App Service. V takovém případě se v protokolech aktivit zobrazí následující chyba:
```
ResourceID: /subscriptions/{SubscriptionID}/resourceGroups/Default-Networking/providers/Microsoft.Web/hostingEnvironments/{ASEname}
Error:{"error":{"code":"ResourceDeploymentFailure","message":"The resource provision operation did not complete within the allowed timeout period."}}
```

Chcete-li tento problém vyřešit, ujistěte se, že žádná z následujících podmínek jsou pravdivé:
* Podsíť je příliš malá.
* Podsíť není prázdná.
* ExpressRoute zabraňuje požadavkům na připojení k síti prostředí služby App Service.
* Chybná skupina zabezpečení sítě zabraňuje požadavkům na připojení k síti v prostředí služby App Service.
* Vynucené tunelování je zapnuto.

Další informace najdete [v tématu Časté problémy při nasazování (vytváření) nové prostředí služby Azure App Service](https://blogs.msdn.microsoft.com/waws/2016/05/13/most-frequent-issues-when-deploying-creating-a-new-azure-app-service-environment-ase/).

## <a name="why-cant-i-delete-my-app-service-plan"></a>Proč nemohu odstranit svůj plán služby App Service?

Plán služby App Service nelze odstranit, pokud jsou k plánu služby App Service přidruženy nějaké aplikace služby App Service. Než plán služby App Service odstraníte, odeberte všechny přidružené aplikace služby App Service z plánu služby App Service.

## <a name="how-do-i-schedule-a-webjob"></a>Jak naplánuji webovou úlohu?

Naplánovanou webovou úlohu můžete vytvořit pomocí výrazů Cron:

1. Vytvořte soubor settings.job.
2. Do tohoto souboru JSON zahrňte vlastnost plánu pomocí výrazu Cron: 
    ```json
    { "schedule": "{second}
    {minute} {hour} {day}
    {month} {day of the week}" }
    ```

Další informace o naplánovaných webových úlohách naleznete v [tématu Vytvoření naplánované webové úlohy pomocí výrazu Cron](webjobs-create.md#CreateScheduledCRON).

## <a name="how-do-i-perform-penetration-testing-for-my-app-service-app"></a>Jak můžu provést penetrační testy pro svou aplikaci App Service?

Chcete-li provést penetrační zkoušky, [odešlete žádost](https://portal.msrc.microsoft.com/engage/pentest).

## <a name="how-do-i-configure-a-custom-domain-name-for-an-app-service-web-app-that-uses-traffic-manager"></a>Jak nakonfiguruji vlastní název domény pro webovou aplikaci služby App Service, která používá Traffic Manager?

Informace o tom, jak používat vlastní název domény s aplikací App Service, která používá Azure Traffic Manager pro vyrovnávání zatížení, najdete v [tématu Konfigurace vlastního názvu domény pro webovou aplikaci Azure pomocí Traffic Manageru](configure-domain-traffic-manager.md).

## <a name="my-app-service-certificate-is-flagged-for-fraud-how-do-i-resolve-this"></a>Můj certifikát služby App Service je označený příznakem podvodu. Jak to můžu vyřešit?

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Během ověření domény nákupu certifikátu služby App Service se může zobrazit následující zpráva:

"Váš certifikát byl označen za možný podvod. Žádost je v současné době přezkoumávána. Pokud se certifikát nestane použitelným do 24 hodin, obraťte se na podporu Azure."

Jak zpráva naznačuje, může dokončení tohoto procesu ověření podvodu trvat až 24 hodin. Během této doby se zpráva bude dál zoavkovat.

Pokud váš certifikát služby App Service tuto zprávu zobrazuje i po 24 hodinách, spusťte následující skript prostředí PowerShell. Skript kontaktuje [poskytovatele certifikátu](https://www.godaddy.com/) přímo k vyřešení problému.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subId>
$actionProperties = @{
    "Name"= "<Customer Email Address>"
    };
Invoke-AzResourceAction -ResourceGroupName "<App Service Certificate Resource Group Name>" -ResourceType Microsoft.CertificateRegistration/certificateOrders -ResourceName "<App Service Certificate Resource Name>" -Action resendRequestEmails -Parameters $actionProperties -ApiVersion 2015-08-01 -Force   
```

## <a name="how-do-authentication-and-authorization-work-in-app-service"></a>Jak ověřování a autorizace fungují ve službě App Service?

Podrobnou dokumentaci pro ověřování a autorizaci ve službě App Service najdete v tématu dokumenty pro různá přihlášení zprostředkovatele:
* [Azure Active Directory](configure-authentication-provider-aad.md)
* [Facebook](configure-authentication-provider-facebook.md)
* [Google](configure-authentication-provider-google.md)
* [Účet Microsoft](configure-authentication-provider-microsoft.md)
* [Twitter](configure-authentication-provider-twitter.md)

## <a name="how-do-i-redirect-the-default-azurewebsitesnet-domain-to-my-azure-web-apps-custom-domain"></a>Jak přesměruji výchozí doménu *.azurewebsites.net do vlastní domény své webové aplikace Azure?

Když vytvoříte nový web pomocí webových aplikací v Azure, bude k vašemu webu přiřazen výchozí *název webu*.azurewebsites.net doména. Pokud na web přidáte vlastní název hostitele a nechcete, aby uživatelé měli přístup k výchozí doméně *.azurewebsites.net, můžete výchozí adresu URL přesměrovat. Informace o přesměrování veškerého provozu z výchozí domény webu na vlastní doménu najdete v [tématu Přesměrování výchozí domény na vlastní doménu ve webových aplikacích Azure](https://zainrizvi.io/blog/block-default-azure-websites-domain/).

## <a name="how-do-i-determine-which-version-of-net-version-is-installed-in-app-service"></a>Jak zjistím, která verze verze rozhraní .NET je nainstalovaná ve službě App Service?

Nejrychlejší způsob, jak najít verzi microsoftu .NET, která je nainstalovaná ve službě App Service, je pomocí konzoly Kudu. Ke konzoli Kudu můžete přistupovat z portálu nebo pomocí adresy URL aplikace App Service. Podrobné pokyny naleznete v [tématu Určení nainstalované verze rozhraní .NET ve službě App Service](https://blogs.msdn.microsoft.com/waws/2016/11/02/how-to-determine-the-installed-net-version-in-azure-app-services/).

## <a name="why-isnt-autoscale-working-as-expected"></a>Proč automatické škálování nefunguje podle očekávání?

Pokud automatické škálování Azure není škálovat v nebo škálovat instanci webové aplikace, jak jste očekávali, můžete být spuštěna do scénáře, ve kterém jsme záměrně rozhodnete neškálovat, aby se zabránilo nekonečné smyčky z důvodu "mávání." K tomu obvykle dochází, když mezi prahovými hodnotami škálování a škálování není dostatečná rezerva. Informace o tom, jak se vyhnout mávání a přečtěte si další doporučené postupy automatického škálování, naleznete v [tématu Doporučené postupy automatického škálování](../azure-monitor/platform/autoscale-best-practices.md#autoscale-best-practices).

## <a name="why-does-autoscale-sometimes-scale-only-partially"></a>Proč se automatické škálování někdy škáluje pouze částečně?

Automatické škálování se aktivuje, když metriky překročí předem nakonfigurované hranice. Někdy si můžete všimnout, že kapacita je naplněna pouze částečně ve srovnání s tím, co jste očekávali. Tato situace může nastat, pokud není k dispozici požadovaný počet instancí. V tomto scénáři automatické škálování částečně vyplní dostupný počet instancí. Automatické škálování pak spustí logiku opětovného vyvážení získat větší kapacitu. Přiděluje zbývající instance. Všimněte si, že to může trvat několik minut.

Pokud nevidíte očekávaný počet instancí po několika minutách, může to být proto, že částečné doplnění stačilo k tomu, aby metriky byly v rámci hranic. Nebo automatické škálování může mít zmenšen, protože dosáhl a dolní hranice metriky.

Pokud žádná z těchto podmínek neplatí a problém přetrvává, odešlete žádost o podporu.

## <a name="how-do-i-turn-on-http-compression-for-my-content"></a>Jak lze pro svůj obsah zapnout kompresi HTTP?

Chcete-li zapnout kompresi pro statické i dynamické typy obsahu, přidejte do souboru web.config na úrovni aplikace následující kód:

```xml
<system.webServer>
    <urlCompression doStaticCompression="true" doDynamicCompression="true" />
</system.webServer>
```

Můžete také určit konkrétní dynamické a statické typy MIME, které chcete komprimovat. Další informace najdete v odpovědi na otázku fóra v [nastavení httpCompression na jednoduchém webu Azure](https://social.msdn.microsoft.com/Forums/azure/890b6d25-f7dd-4272-8970-da7798bcf25d/httpcompression-settings-on-a-simple-azure-website?forum=windowsazurewebsitespreview).

## <a name="how-do-i-migrate-from-an-on-premises-environment-to-app-service"></a>Jak se migruje z místního prostředí do služby App Service?

Chcete-li migrovat weby z webových serverů Windows a Linux u app service, můžete použít Pomocníka pro migraci služby Azure App Service. Nástroj pro migraci vytvoří webové aplikace a databáze v Azure podle potřeby a pak publikuje obsah. Další informace najdete v tématu [Azure App Service Migration Assistant](https://appmigration.microsoft.com/).
