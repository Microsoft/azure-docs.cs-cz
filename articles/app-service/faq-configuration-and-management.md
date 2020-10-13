---
title: Nejčastější dotazy ke konfiguraci
description: Získejte odpovědi na nejčastější dotazy týkající se problémů s konfigurací a správou pro Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 5545acbfd6bb239b9518fbe352b819f300dafaf0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88962345"
---
# <a name="configuration-and-management-faqs-for-web-apps-in-azure"></a>Nejčastější dotazy týkající se konfigurace a správy pro Web Apps v Azure

V tomto článku najdete odpovědi na nejčastější dotazy týkající se problémů s konfigurací a správou [funkce Web Apps Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="are-there-limitations-i-should-be-aware-of-if-i-want-to-move-app-service-resources"></a>Existují nějaká omezení, o kterých byste měli vědět, pokud chci přesunout App Service prostředky?

Pokud plánujete přesunout App Service prostředků do nové skupiny prostředků nebo předplatného, existuje několik omezení, o kterých byste měli vědět. Další informace najdete v tématu [omezení App Service](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md).

## <a name="how-do-i-use-a-custom-domain-name-for-my-web-app"></a>Návody použít pro moji webovou aplikaci vlastní název domény?

Odpovědi na běžné dotazy týkající se používání vlastního názvu domény ve webové aplikaci Azure najdete v našem sedmi minutách. [přidejte si vlastní název domény](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name). Video nabízí návod, jak přidat vlastní název domény. Popisuje, jak použít vlastní adresu URL místo adresy URL *. azurewebsites.net ve vaší webové aplikaci App Service. Můžete si také prohlédnout podrobný návod, [jak namapovat vlastní název domény](app-service-web-tutorial-custom-domain.md).


## <a name="how-do-i-purchase-a-new-custom-domain-for-my-web-app"></a>Návody koupit novou vlastní doménu pro moji webovou aplikaci?

Informace o tom, jak koupit a nastavit vlastní doménu pro App Service webovou aplikaci, najdete v tématu [Nákup a konfigurace vlastního názvu domény v App Service](manage-custom-dns-buy-domain.md).


## <a name="how-do-i-upload-and-configure-an-existing-tlsssl-certificate-for-my-web-app"></a>Návody nahrát a nakonfigurovat existující certifikát TLS/SSL pro moji webovou aplikaci?

Informace o tom, jak nahrát a nastavit stávající vlastní certifikát TLS/SSL, najdete v tématu [Přidání certifikátu TLS/SSL do aplikace App Service](configure-ssl-certificate.md).


## <a name="how-do-i-purchase-and-configure-a-new-tlsssl-certificate-in-azure-for-my-web-app"></a>Návody koupit a nakonfigurovat nový certifikát TLS/SSL v Azure pro moji webovou aplikaci?

Informace o nákupu a nastavení certifikátu TLS/SSL pro App Service webové aplikace najdete v tématu [Přidání certifikátu TLS/SSL do aplikace App Service](configure-ssl-certificate.md).


## <a name="how-do-i-move-application-insights-resources"></a>Návody přesunout prostředky Application Insights?

V současné době Azure Application Insights nepodporuje operaci přesunu. Pokud původní skupina prostředků zahrnuje prostředek Application Insights, nemůžete tento prostředek přesunout. Pokud při pokusu o přesunutí aplikace App Service přidáte prostředek Application Insights, celá operace přesunu se nezdařila. Application Insights a plán App Service ale nemusí být ve stejné skupině prostředků, jako aplikace, aby aplikace správně fungovala.

Další informace najdete v tématu [omezení App Service](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md).

## <a name="where-can-i-find-a-guidance-checklist-and-learn-more-about-resource-move-operations"></a>Kde najdu kontrolní seznam pokynů a další informace o operacích přesunutí prostředků?

[App Service omezením](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md) se dozvíte, jak přesunout prostředky do nového předplatného nebo do nové skupiny prostředků ve stejném předplatném. Můžete získat informace o kontrolním seznamu pro přesunutí prostředků, zjistit, které služby podporují operaci přesunu, a získat další informace o omezeních App Service a dalších tématech.

## <a name="how-do-i-set-the-server-time-zone-for-my-web-app"></a>Návody nastavit časové pásmo serveru pro moji webovou aplikaci?

Nastavení časového pásma serveru pro vaši webovou aplikaci:

1. V Azure Portal v předplatném App Service přejděte do nabídky **nastavení aplikace** .
2. V části **nastavení aplikace**přidejte toto nastavení:
    * Key = WEBSITE_TIME_ZONE
    * Hodnota = *časové pásmo, které chcete*
3. Vyberte **Uložit**.

Pro služby App Service, které jsou spuštěné v systému Windows, se ve sloupci **timezone (časové pásmo** ) v článku [výchozí časové pásmo](/windows-hardware/manufacture/desktop/default-time-zones) pro přijaté hodnoty zobrazí. Pro služby App Services spuštěné v systému Linux nastavte jako hodnotu časového pásma [název TZ databáze](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones) . Tady je příklad názvu TZ databáze: America/Adak.

## <a name="why-do-my-continuous-webjobs-sometimes-fail"></a>Proč dojde k selhání mých nepřetržitých webových úloh?

Ve výchozím nastavení jsou webové aplikace uvolněny, pokud nejsou po stanovenou dobu nečinné. To umožňuje systému šetřit prostředky. V plánech Basic a Standard můžete zapnout nastavení **Always On** , aby se webová aplikace neustále načetla. Pokud vaše webová aplikace spouští nepřetržité webové úlohy, měli byste zapnout funkci **Always On**, jinak nemusí být webové úlohy spolehlivě fungovat. Další informace najdete v tématu [Vytvoření nepřetržitě spuštěné úlohy WebJob](webjobs-create.md#CreateContinuous).

## <a name="how-do-i-get-the-outbound-ip-address-for-my-web-app"></a>Návody získat odchozí IP adresu pro moji webovou aplikaci?

Získání seznamu odchozích IP adres pro vaši webovou aplikaci:

1. V okně Azure Portal v okně webové aplikace přejděte do nabídky **vlastnosti** .
2. Vyhledejte **odchozí IP adresy**.

Zobrazí se seznam odchozích IP adres.

Informace o tom, jak získat odchozí IP adresu, pokud je váš web hostovaný v App Service Environment, najdete v tématu [odchozí síťové adresy](environment/app-service-app-service-environment-network-architecture-overview.md#outbound-network-addresses).

## <a name="how-do-i-get-a-reserved-or-dedicated-inbound-ip-address-for-my-web-app"></a>Návody získat rezervovanou nebo vyhrazenou příchozí IP adresu pro moji webovou aplikaci?

K nastavení vyhrazené nebo rezervované IP adresy pro příchozí hovory provedené na webu Azure App nainstalujte a nakonfigurujte certifikát TLS/SSL založený na protokolu IP.

Všimněte si, že pokud chcete použít vyhrazenou nebo rezervovanou IP adresu pro příchozí volání, musí být plán App Service v plánu služeb Basic nebo vyšší.

## <a name="can-i-export-my-app-service-certificate-to-use-outside-azure-such-as-for-a-website-hosted-elsewhere"></a>Můžu exportovat App Service můj certifikát pro použití mimo Azure, například pro web hostující jinde? 

Ano, můžete je exportovat, abyste je mohli používat mimo Azure. Další informace najdete v tématu [Nejčastější dotazy týkající se App Service certifikátů a vlastních domén](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).

## <a name="can-i-export-my-app-service-certificate-to-use-with-other-azure-cloud-services"></a>Můžu exportovat svůj App Service certifikát pro použití s dalšími Cloud Services Azure?

Portál poskytuje prvotřídní prostředí pro nasazení App Serviceho certifikátu prostřednictvím Azure Key Vault pro App Service aplikací. Dostali jsme ale žádosti od zákazníků, aby tyto certifikáty používali mimo App Service platformu, například pomocí Azure Virtual Machines. Pokud se chcete dozvědět, jak vytvořit místní kopii PFX certifikátu App Service, abyste mohli používat certifikát s dalšími prostředky Azure, přečtěte si téma [Vytvoření místní kopie PFX certifikátu App Service](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/).

Další informace najdete v tématu [Nejčastější dotazy týkající se App Service certifikátů a vlastních domén](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).


## <a name="why-do-i-see-the-message-partially-succeeded-when-i-try-to-back-up-my-web-app"></a>Proč se při pokusu o zálohování webové aplikace zobrazí zpráva "částečně úspěšné"?

Běžnou příčinou selhání zálohování je, že aplikace používá některé soubory. Soubory, které jsou používány, jsou při provádění zálohy uzamčeny. Tím se zabrání v zálohování těchto souborů a výsledkem může být stav "částečně úspěšné". Můžete tak zabránit tomu, aby k tomu došlo, vyloučením souborů z procesu zálohování. Můžete se rozhodnout zálohovat jenom to, co je potřeba. Další informace najdete v tématu [zálohování pouze důležitých částí vaší lokality pomocí Azure Web Apps](https://zainrizvi.io/blog/creating-partial-backups-of-your-site-with-azure-web-apps/).

## <a name="how-do-i-remove-a-header-from-the-http-response"></a>Návody odebrat hlavičku z odpovědi HTTP?

Chcete-li odebrat hlavičky z odpovědi HTTP, aktualizujte soubor web.config vaší lokality. Další informace najdete v tématu [Odebrání standardních hlaviček serveru na Azure websites](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

## <a name="is-app-service-compliant-with-pci-standard-30-and-31"></a>Je App Service kompatibilní se standardem PCI 3,0 a 3,1?

V současné době je funkce Web Apps Azure App Service v souladu s verzí DSS (PCI data Security Standard) 3,0 úrovně 1. PCI DSS verze 3,1 se nachází v naší mapě. Plánování už probíhá, pokud budete pokračovat v přijímání nejnovějšího standardního standardu.

PCI DSS certifikace verze 3,1 vyžaduje zakázání protokolu TLS (Transport Layer Security) 1,0. V současné době zakázání TLS 1,0 není možností pro většinu plánů App Service. Pokud ale použijete App Service Environment nebo jste ochotni migrovat vaše zatížení do App Service Environment, můžete získat větší kontrolu nad prostředím. To zahrnuje zakázání TLS 1,0 kontaktováním podpory Azure. V blízké budoucnosti plánujete, aby tato nastavení byla uživatelům přístupná.

Další informace najdete v tématu [Microsoft Azure App Service dodržování předpisů webových aplikací pomocí standardu PCI 3,0 a 3,1](https://support.microsoft.com/help/3124528).

## <a name="how-do-i-use-the-staging-environment-and-deployment-slots"></a>Návody použít testovací prostředí a nasazovací sloty?

Když v plánech Standard a Premium App Service nasadíte webovou aplikaci na App Service, můžete ji nasadit do samostatného slotu nasazení místo do výchozí produkční přihrádky. Sloty nasazení jsou živé webové aplikace, které mají vlastní názvy hostitelů. Prvky obsahu a konfigurace webové aplikace je možné prohodit mezi dvěma sloty nasazení, včetně produkčního slotu.

Další informace o použití slotů nasazení najdete v tématu [Nastavení přípravného prostředí v App Service](deploy-staging-slots.md).

## <a name="how-do-i-access-and-review-webjob-logs"></a>Návody přístup a kontrola protokolů webové úlohy?

Kontrola protokolů webové úlohy:

1. Přihlaste se k **webu Kudu** ( `https://*yourwebsitename*.scm.azurewebsites.net` ).
2. Vyberte webovou úlohu.
3. Vyberte tlačítko **přepnout výstup** .
4. Pokud chcete stáhnout výstupní soubor, vyberte odkaz **ke stažení** .
5. Pro jednotlivé běhy vyberte **jednotlivé vyvolání**.
6. Vyberte tlačítko **přepnout výstup** .
7. Vyberte odkaz ke stažení.

## <a name="im-trying-to-use-hybrid-connections-with-sql-server-why-do-i-see-the-message-systemoverflowexception-arithmetic-operation-resulted-in-an-overflow"></a>Snažím se použít Hybrid Connections s SQL Server. Proč se zobrazí zpráva "System. OverflowException: aritmetická operace vedla k přetečení"?

Pokud používáte Hybrid Connections pro přístup k SQL Server, může aktualizace Microsoft .NET na 10, 2016 způsobit selhání připojení. Může se zobrazit tato zpráva:

```
Exception: System.Data.Entity.Core.EntityException: The underlying provider failed on Open. —> System.OverflowException: Arithmetic operation resulted in an overflow. or (64 bit Web app) System.OverflowException: Array dimensions exceeded supported range, at System.Data.SqlClient.TdsParser.ConsumePreLoginHandshake
```

### <a name="resolution"></a>Řešení

Výjimka byla způsobena problémem s Správce hybridního připojení, která od byla opravena. Nezapomeňte [aktualizovat správce hybridního připojení](https://go.microsoft.com/fwlink/?LinkID=841308) pro vyřešení tohoto problému.

## <a name="how-do-i-add-a-url-rewrite-rule"></a>Návody přidat pravidlo pro přepsání adresy URL?

Chcete-li přidat pravidlo přepsání adresy URL, vytvořte web.config soubor s odpovídajícími konfiguračními položkami ve složce **wwwroot** . Další informace najdete v tématu [Azure App Services: principy přepsání adresy URL](/archive/blogs/madhurabharadwaj/azure-app-services-understanding-url-re-write).

## <a name="how-do-i-control-inbound-traffic-to-app-service"></a>Návody řízení příchozího provozu do App Service?

Na úrovni webu máte dvě možnosti řízení příchozího provozu do App Service:

* Zapněte dynamická omezení IP adres. Pokud se chcete dozvědět, jak zapnout dynamická omezení IP adres, přečtěte si téma [omezení IP adresy a domény pro Azure websites](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/).
* Zapněte zabezpečení modulů. Informace o tom, jak zapnout zabezpečení modulů, najdete v tématu [ModSecurity Firewall webových aplikací v Azure websites](https://azure.microsoft.com/blog/modsecurity-for-azure-websites/).

Pokud používáte App Service Environment, můžete použít [bránu firewall Barracuda](https://azure.microsoft.com/blog/configuring-barracuda-web-application-firewall-for-azure-app-service-environment/).

## <a name="how-do-i-block-ports-in-an-app-service-web-app"></a>Návody blokovat porty ve App Service webové aplikaci?

Ve sdíleném prostředí klienta App Service není možné zablokovat konkrétní porty kvůli povaze infrastruktury. Pro vzdálené ladění sady Visual Studio se můžou otevřít taky porty TCP 4020, 4022 a 4024.

V App Service Environment máte plnou kontrolu nad příchozím a odchozím provozem. Skupiny zabezpečení sítě můžete použít k omezení nebo blokování konkrétních portů. Další informace o App Service Environment najdete v tématu [Úvod do App Service Environment](https://azure.microsoft.com/blog/introducing-app-service-environment/).

## <a name="how-do-i-capture-an-f12-trace"></a>Návody zachytit trasování F12?

Pro zachycení trasování F12 máte dvě možnosti:

* Trasování protokolu HTTP F12
* Výstup konzoly F12

### <a name="f12-http-trace"></a>Trasování protokolu HTTP F12

1. V Internet Exploreru přejdete na web. Před provedením dalších kroků je důležité se přihlásit. V opačném případě trasování F12 zachytí citlivá přihlašovací data.
2. Stiskněte klávesu F12.
3. Ověřte, že je vybraná karta **síť** , a pak vyberte zelené tlačítko **Přehrát** .
4. Proveďte kroky, které reprodukovaly problém.
5. Vyberte tlačítko červeného **zastavení** .
6. Vyberte tlačítko **Uložit** (ikona disku) a uložte soubor Har (v Internet Exploreru a Microsoft Edge) *nebo* klikněte pravým tlačítkem myši na soubor Har a pak vyberte **Uložit jako Har s obsahem** (v Chrome).

### <a name="f12-console-output"></a>Výstup konzoly F12

1. Vyberte kartu **Konzola** .
2. Pro každou kartu, která obsahuje více než 0 položek, vyberte kartu (**Chyba**, **varování**nebo **informace**). Pokud karta není vybraná, ikona karty je po přesunutí kurzoru z ní šedá nebo černá.
3. V oblasti zpráv v podokně klikněte pravým tlačítkem myši a vyberte možnost **Kopírovat vše**.
4. Vložte zkopírovaný text do souboru a pak soubor uložte.

Chcete-li zobrazit soubor HAR, můžete použít [prohlížeč Har](http://www.softwareishard.com/har/viewer/).

## <a name="why-do-i-get-an-error-when-i-try-to-connect-an-app-service-web-app-to-a-virtual-network-that-is-connected-to-expressroute"></a>Proč se při pokusu o připojení webové aplikace App Service k virtuální síti, která je připojená k ExpressRoute, zobrazí chyba?

Pokud se pokusíte připojit webovou aplikaci Azure k virtuální síti, která je připojená k Azure ExpressRoute, dojde k jejímu chybě. Zobrazí se následující zpráva: "brána není bránou sítě VPN."

V současné době nemůžete mít připojení VPN typu Point-to-site k virtuální síti, která je připojená k ExpressRoute. Sítě VPN typu Point-to-site a ExpressRoute nemohou existovat současně pro stejnou virtuální síť. Další informace najdete v tématu [omezení a omezení připojení ExpressRoute a Site-to-Site VPN](../expressroute/expressroute-howto-coexist-classic.md#limits-and-limitations).

## <a name="how-do-i-connect-an-app-service-web-app-to-a-virtual-network-that-has-a-static-routing-policy-based-gateway"></a>Návody připojit webovou aplikaci App Service k virtuální síti s bránou statického směrování (na základě zásad)?

V současné době se připojení webové aplikace App Service k virtuální síti s bránou statických směrování (založenou na zásadách) nepodporuje. Pokud vaše cílová virtuální síť již existuje, musí mít povolenou síť VPN typu Point-to-site s bránou dynamického směrování, aby ji bylo možné připojit k aplikaci. Pokud je brána nastavená na statické směrování, nemůžete povolit síť VPN typu Point-to-site. 

Další informace najdete v tématu [integrace aplikace do služby Azure Virtual Network](web-sites-integrate-with-vnet.md).

## <a name="in-my-app-service-environment-why-can-i-create-only-one-app-service-plan-even-though-i-have-two-workers-available"></a>Proč se v mém App Service Environment dá vytvořit jenom jeden plán App Service, i když mám k dispozici dva pracovní procesy?

Aby se zajistila odolnost proti chybám, App Service Environment vyžaduje, aby každý pracovní fond měl aspoň jeden další výpočetní prostředek. K dodatečnému výpočetnímu prostředku nelze přiřadit úlohu.

Další informace najdete v tématu [vytvoření App Service Environment](environment/app-service-web-how-to-create-an-app-service-environment.md).

## <a name="why-do-i-see-timeouts-when-i-try-to-create-an-app-service-environment"></a>Proč se při pokusu o vytvoření App Service Environment zobrazí časové limity?

V některých případech se vytváření App Service Environment nezdařilo. V takovém případě se v protokolech aktivit zobrazí následující chyba:
```
ResourceID: /subscriptions/{SubscriptionID}/resourceGroups/Default-Networking/providers/Microsoft.Web/hostingEnvironments/{ASEname}
Error:{"error":{"code":"ResourceDeploymentFailure","message":"The resource provision operation did not complete within the allowed timeout period."}}
```

Chcete-li tento problém vyřešit, ujistěte se, že není splněna žádná z následujících podmínek:
* Podsíť je příliš malá.
* Podsíť není prázdná.
* ExpressRoute zabraňuje požadavkům na připojení k síti App Service Environment.
* Nesprávná skupina zabezpečení sítě brání požadavkům na připojení k síti App Service Environment.
* Vynucené tunelování je zapnuté.

Další informace najdete v tématu [časté problémy při nasazení (vytvoření) nového Azure App Service Environment](/archive/blogs/waws/most-frequent-issues-when-deploying-creating-a-new-azure-app-service-environment-ase).

## <a name="why-cant-i-delete-my-app-service-plan"></a>Proč nemůžu odstranit plán App Service?

Plán App Service nemůžete odstranit, pokud jsou nějaké App Service aplikace spojené s plánem App Service. Než odstraníte plán App Service, odeberte všechny přidružené App Service aplikace z plánu App Service.

## <a name="how-do-i-schedule-a-webjob"></a>Návody naplánovat úlohu WebJob?

Naplánovanou webovou úlohu můžete vytvořit pomocí výrazů cron:

1. Vytvoří soubor Settings. job.
2. V tomto souboru JSON zahrňte vlastnost plánu pomocí výrazu cron: 
    ```json
    { "schedule": "{second}
    {minute} {hour} {day}
    {month} {day of the week}" }
    ```

Další informace o naplánovaných webových úlohách najdete v tématu [Vytvoření plánované webové úlohy pomocí výrazu cron](webjobs-create.md#CreateScheduledCRON).

## <a name="how-do-i-perform-penetration-testing-for-my-app-service-app"></a>Návody udělat testování průniku pro moji aplikaci App Service?

Chcete-li provést testování průniku, [odešlete žádost](https://portal.msrc.microsoft.com/engage/pentest).

## <a name="how-do-i-configure-a-custom-domain-name-for-an-app-service-web-app-that-uses-traffic-manager"></a>Návody nakonfigurovat vlastní název domény pro webovou aplikaci App Service, která používá Traffic Manager?

Pokud chcete zjistit, jak použít vlastní název domény s aplikací App Service, která používá Azure Traffic Manager pro vyrovnávání zatížení, přečtěte si téma [Konfigurace vlastního názvu domény pro webovou aplikaci Azure s Traffic Manager](configure-domain-traffic-manager.md).

## <a name="my-app-service-certificate-is-flagged-for-fraud-how-do-i-resolve-this"></a>Můj certifikát služby App Service je označený příznakem podvodu. Jak to můžu vyřešit?

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Při ověřování App Service nákupu certifikátů se může zobrazit následující zpráva:

"Certifikát byl označen jako označený jako možný podvod. V tuto chvíli je žádost v současnosti pod kontrolou. Pokud se certifikát nestane použitelným během 24 hodin, kontaktujte podporu Azure.

Jak zpráva indikuje, může dokončení tohoto procesu ověřování podvodů trvat až 24 hodin. Během této doby se tato zpráva zobrazí i nadále.

Pokud váš App Service certifikát dál zobrazuje tuto zprávu po 24 hodinách, spusťte prosím následující skript PowerShellu. Skript kontaktuje [poskytovatele certifikátů](https://www.godaddy.com/) přímo za účelem vyřešení tohoto problému.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subId>
$actionProperties = @{
    "Name"= "<Customer Email Address>"
    };
Invoke-AzResourceAction -ResourceGroupName "<App Service Certificate Resource Group Name>" -ResourceType Microsoft.CertificateRegistration/certificateOrders -ResourceName "<App Service Certificate Resource Name>" -Action resendRequestEmails -Parameters $actionProperties -ApiVersion 2015-08-01 -Force   
```

## <a name="how-do-authentication-and-authorization-work-in-app-service"></a>Jak funguje ověřování a autorizace v App Service?

Podrobnou dokumentaci k ověřování a autorizaci v App Service najdete v článku dokumentace pro různá přihlášení poskytovatele identity:
* [Azure Active Directory](configure-authentication-provider-aad.md)
* [Facebook](configure-authentication-provider-facebook.md)
* [Google](configure-authentication-provider-google.md)
* [Účet Microsoft](configure-authentication-provider-microsoft.md)
* [Twitter](configure-authentication-provider-twitter.md)

## <a name="how-do-i-redirect-the-default-azurewebsitesnet-domain-to-my-azure-web-apps-custom-domain"></a>Návody přesměrovat výchozí doménu *. azurewebsites.net na vlastní doménu webové aplikace Azure?

Když vytváříte nový web pomocí Web Apps v Azure, k vaší lokalitě se přiřadí výchozí *název_webu*. azurewebsites.NET doména. Pokud do svého webu přidáte vlastní název hostitele a nechcete, aby uživatelé měli přístup k vaší výchozí doméně *. azurewebsites.net, můžete přesměrovat výchozí adresu URL. Informace o tom, jak přesměrovat veškerý provoz z výchozí domény vašeho webu do vlastní domény, najdete v tématu [přesměrování výchozí domény do vlastní domény ve službě Azure Web Apps](https://zainrizvi.io/blog/block-default-azure-websites-domain/).

## <a name="how-do-i-determine-which-version-of-net-version-is-installed-in-app-service"></a>Návody určit, která verze rozhraní .NET je nainstalovaná v App Service?

Nejrychlejší způsob, jak najít verzi rozhraní Microsoft .NET, která je nainstalovaná v App Service, je použití konzoly Kudu. Ke konzole Kudu můžete přistupovat z portálu nebo pomocí adresy URL vaší aplikace App Service. Podrobné pokyny najdete v tématu [určení nainstalované verze rozhraní .NET v App Service](/archive/blogs/waws/how-to-determine-the-installed-net-version-in-azure-app-services).

## <a name="why-isnt-autoscale-working-as-expected"></a>Proč automatické škálování nefunguje podle očekávání?

Pokud se automatické škálování Azure neškáluje nebo škáluje instanci webové aplikace, jak jste očekávali, může se stát, že se rozhodnete, že záměrně nemusíte škálovat, aby nedocházelo k nekonečné smyčce v důsledku "přepíná". K tomu obvykle dochází v případě, že se nejedná o odpovídající okraj mezi mezními hodnotami škálování a škálování. Další informace o tom, jak se vyhnout "přepíná" a získat informace o dalších osvědčených postupech automatického škálování, najdete v tématu [osvědčené postupy pro automatické škálování](../azure-monitor/platform/autoscale-best-practices.md#autoscale-best-practices).

## <a name="why-does-autoscale-sometimes-scale-only-partially"></a>Proč se automatické škálování někdy škáluje pouze částečně?

Automatické škálování se aktivuje, když metriky překročí předem nakonfigurované hranice. V některých případech se může stát, že se kapacita vyplní pouze částečně v porovnání s očekávaným způsobem. K tomu může dojít v případě, že počet instancí, které chcete použít, není k dispozici. V takovém případě se automatické škálování částečně vyplní pomocí dostupného počtu instancí. Automatické škálování potom spustí logiku pro vyrovnávání zatížení a získá větší kapacitu. Přidělí zbývající instance. Všimněte si, že to může trvat několik minut.

Pokud nevidíte očekávaný počet instancí po několika minutách, může to být způsobeno tím, že částečné doplňování bylo dostačující k uvedení metrik v rámci hranic. Nebo se automatické škálování mohlo škálovat dolů, protože dosáhlo se dolní hranice metriky.

Pokud se žádná z těchto podmínek nepoužije a problém přetrvává, odešlete žádost o podporu.

## <a name="how-do-i-turn-on-http-compression-for-my-content"></a>Návody zapnout kompresi HTTP pro můj obsah?

Chcete-li zapnout kompresi pro statický i dynamický typ obsahu, přidejte do souboru web.config na úrovni aplikace následující kód:

```xml
<system.webServer>
    <urlCompression doStaticCompression="true" doDynamicCompression="true" />
</system.webServer>
```

Můžete také zadat konkrétní dynamické a statické typy MIME, které chcete zkomprimovat. Další informace najdete v naší reakci na otázku fóra v tématu [Nastavení httpCompression na jednoduchém webu Azure](https://social.msdn.microsoft.com/Forums/azure/890b6d25-f7dd-4272-8970-da7798bcf25d/httpcompression-settings-on-a-simple-azure-website?forum=windowsazurewebsitespreview).

## <a name="how-do-i-migrate-from-an-on-premises-environment-to-app-service"></a>Návody migraci z místního prostředí do App Service?

Chcete-li migrovat lokality z webových serverů Windows a Linux na App Service, můžete použít Pomocník s migrací Azure App Service. Nástroj pro migraci v Azure podle potřeby vytvoří webové aplikace a databáze a publikuje obsah. Další informace najdete v tématu [Azure App Service Pomocník s migrací](https://appmigration.microsoft.com/).