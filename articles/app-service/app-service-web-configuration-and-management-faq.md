---
title: Nejčastější dotazy k konfigurace pro webové aplikace Azure | Microsoft Docs
description: Získejte odpovědi na nejčastější dotazy týkající se konfigurace a správy problémů pro funkce Web Apps služby Azure App Service.
services: app-service\web
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: efbed264babe0b192590380639cdc1c8861b4f38
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="configuration-and-management-faqs-for-web-apps-in-azure"></a>Konfigurace a správy nejčastější dotazy pro webové aplikace v Azure

Tento článek obsahuje odpovědi na nejčastější dotazy (FAQ) o konfiguraci a správě problémy [funkce Web Apps služby Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="are-there-limitations-i-should-be-aware-of-if-i-want-to-move-app-service-resources"></a>Existují omezení, které I měli vědět, pokud chcete přesunout prostředky služby App Service?

Pokud budete chtít přesunout prostředky služby App Service pro novou skupinu prostředků nebo předplatného, je třeba mít na paměti několik omezení. Další informace najdete v tématu [omezení služby App Service](../azure-resource-manager/resource-group-move-resources.md#app-service-limitations).

## <a name="how-do-i-use-a-custom-domain-name-for-my-web-app"></a>Použití vlastního názvu domény pro webová aplikace

Odpovědi na časté otázky týkající se použití vlastního názvu domény s vaší webové aplikace Azure, najdete v tématu naše sedm dvouminutové video [přidání vlastního názvu domény](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name). Video nabízí návod, jak přidat vlastní název domény. Popisuje, jak používat vlastní adresu URL místo *. azurewebsites.net URL s vaší webové aplikace služby App Service. Také můžete zobrazit podrobný návod k [jak namapovat vlastní název domény](app-service-web-tutorial-custom-domain.md).


## <a name="how-do-i-purchase-a-new-custom-domain-for-my-web-app"></a>Jak zakoupit novou vlastní doménu pro webová aplikace?

Další informace o nákupu a nastavit vlastní doménu pro webovou aplikaci služby App Service naleznete v tématu [zakoupení a konfigurace vlastního názvu domény ve službě App Service](custom-dns-web-site-buydomains-web-app.md).


## <a name="how-do-i-upload-and-configure-an-existing-ssl-certificate-for-my-web-app"></a>Jak nahrání a konfigurace existující certifikát SSL pro webová aplikace?

Naučte se nahrát a nastavit stávající vlastní certifikát SSL, najdete v tématu [vytvořit vazbu existujícího certifikátu SSL vlastní webové aplikace Azure](app-service-web-tutorial-custom-ssl.md#upload).


## <a name="how-do-i-purchase-and-configure-a-new-ssl-certificate-in-azure-for-my-web-app"></a>Jak zakoupit a konfigurovat nový certifikát SSL pro moje webovou aplikaci v Azure?

Další informace o nákupu a nastavit certifikát SSL pro webovou aplikaci služby App Service naleznete v tématu [přidat certifikát SSL do aplikace služby App Service](web-sites-purchase-ssl-web-site.md).


## <a name="how-do-i-move-application-insights-resources"></a>Přesunutí prostředků Application Insights

V současné době nepodporuje Azure Application Insights operaci přesunutí. Pokud vaše původní skupina prostředků obsahuje prostředek Application Insights, nemůžete přesunout prostředku. Pokud při pokusu o přesunutí aplikace služby App Service zahrnete prostředku Application Insights, přesuňte celý operace selže. Application Insights a plán služby App Service však není nutné být ve stejné skupině prostředků jako aplikace pro aplikaci správné fungování.

Další informace najdete v tématu [omezení služby App Service](../azure-resource-manager/resource-group-move-resources.md#app-service-limitations).

## <a name="where-can-i-find-a-guidance-checklist-and-learn-more-about-resource-move-operations"></a>Kde můžu najít kontrolní seznam pokyny a další informace o prostředku přesunout operace?

[Omezení služby App Service](../azure-resource-manager/resource-group-move-resources.md#app-service-limitations) ukazuje, jak přesunout prostředky do nového předplatného nebo do nové skupiny prostředků ve stejném předplatném. Můžete získat informace o přesunutí kontrolního seznamu prostředků, přečtěte si služby, které podporují operace přesunutí a další informace o omezení služby App Service a další témata.

## <a name="how-do-i-set-the-server-time-zone-for-my-web-app"></a>Jak nastavit časové pásmo serveru pro webová aplikace?

Chcete-li nastavit časové pásmo serveru pro webové aplikace:

1. Na portálu Azure v rámci vašeho předplatného služby App Service, přejděte na **nastavení aplikace** nabídky.
2. V části **nastavení aplikace**, přidejte toto nastavení:
    * Klíč = WEBSITE_TIME_ZONE
    * Hodnota = *časové pásmo, které chcete*
3. Vyberte **Uložit**.

## <a name="why-do-my-continuous-webjobs-sometimes-fail"></a>Proč moje nepřetržité webové úlohy někdy nezdaří?

Ve výchozím nastavení jsou uvolněna webové aplikace, pokud jsou na nastavenou dobu nečinnosti. To umožňuje ušetřit prostředky systému. V plánech Basic a Standard, můžete zapnout **Always On** nastavení zachovat webové aplikace načíst vždy. Pokud nepřetržité webové úlohy běží vaše webová aplikace, třeba zapnout **Always On**, nebo webové úlohy nemusí být možné spustit spolehlivě. Další informace najdete v tématu [vytvořit nepřetržitě běží webové úlohy](web-sites-create-web-jobs.md#CreateContinuous).

## <a name="how-do-i-get-the-outbound-ip-address-for-my-web-app"></a>Jak získat IP adresu odchozí pro webová aplikace?

Pokud chcete získat seznam odchozí IP adres pro webovou aplikaci:

1. Na portálu Azure v okně vaší webové aplikace, přejděte na **vlastnosti** nabídky.
2. Vyhledejte **odchozí ip adresy**.

Zobrazí se seznam odchozí IP adres.

Pokud váš web hostovaný ve službě App Service Environment pro PowerApps, zjistěte, jak získat odchozí IP adresu, najdete v článku [odchozí síťové adresy](environment/app-service-app-service-environment-network-architecture-overview.md#outbound-network-addresses).

## <a name="how-do-i-get-a-reserved-or-dedicated-inbound-ip-address-for-my-web-app"></a>Jak získám vyhrazené vyhrazené příchozí IP adresu nebo webová aplikace?

Pokud chcete nastavit vyhrazené nebo jinak vyhrazená IP adresa pro příchozí volání na váš web aplikace Azure, nainstalujte a nakonfigurujte certifikát protokolu SSL založenou na protokolu IP.

Všimněte si, že používat vyhrazené nebo jinak vyhrazená IP adresa pro příchozí volání, plán služby App Service musí být v plánu služby základní nebo vyšší.

## <a name="can-i-export-my-app-service-certificate-to-use-outside-azure-such-as-for-a-website-hosted-elsewhere"></a>Můžete exportovat certifikát Moje aplikace služby k použití mimo Azure, jako například pro web někde hostovaná? 

Certifikáty App Service jsou považovány za prostředky Azure. Nejsou určeny k použití mimo služeb Azure. Nelze exportovat, aby uživatelé používali mimo Azure. Další informace najdete v tématu [nejčastější dotazy pro certifikáty App Service a vlastní domény](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).

## <a name="can-i-export-my-app-service-certificate-to-use-with-other-azure-cloud-services"></a>Můžete exportovat Moje certifikátu služby App Service pro použití s jinými službami Azure cloud?

Na portálu zajišťuje prvotřídní prostředí pro nasazení do aplikace služby App Service certifikát služby App Service pomocí Azure Key Vault. Však budeme mít přijímá požadavky od zákazníků, aby používaly tyto certifikáty mimo platformě App Service, například pomocí Azure Virtual Machines. Zjistěte, jak vytvořit místní kopii PFX certifikátu služby App Service, abyste je mohli používat certifikát s další prostředky Azure, najdete v tématu [vytvořit místní kopii PFX certifikátu služby App Service](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/).

Další informace najdete v tématu [nejčastější dotazy pro certifikáty App Service a vlastní domény](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).


## <a name="why-do-i-see-the-message-partially-succeeded-when-i-try-to-back-up-my-web-app"></a>Proč se při pokusu o zálohování webová aplikace zobrazuje zpráva "Částečně bylo úspěšné"?

Obvyklou příčinou selhání zálohování je, že některé soubory jsou používány aplikací. Soubory, které jsou používány jsou zamčené při provádění zálohování. To zabrání zálohovaných tyto soubory a může mít za následek stav "Částečně bylo úspěšné". Vám může potenciálně tomu zabránit tak, že soubory vyloučíte z procesu zálohování. Můžete zálohovat pouze to, co je potřeba. Další informace najdete v tématu [zálohovat jenom důležité části serveru s webové aplikace Azure](http://www.zainrizvi.io/2015/06/05/creating-partial-backups-of-your-site-with-azure-web-apps/).

## <a name="how-do-i-remove-a-header-from-the-http-response"></a>Odebrání hlavičku z odpovědi HTTP

K odebrání hlavičky HTTP odpovědi, aktualizujte soubor web.config. Další informace najdete v tématu [odebrat záhlaví standardní server na vaše weby Azure](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

## <a name="is-app-service-compliant-with-pci-standard-30-and-31"></a>Je kompatibilní s PCI standardní 3.0 a 3.1 služby App Service?

V současné době funkce Web Apps služby Azure App Service je v souladu s PCI Data zabezpečení DSS (Standard) verze 3.0 úroveň 1. PCI DSS verze 3.1 je na našem plán. Plánování již probíhá jak přijetí nejnovější standardní bude pokračovat.

PCI DSS verze 3.1 certifikační vyžaduje zakázání zabezpečení TLS (Transport Layer) 1.0. V současné době zákaz protokolu TLS 1.0 není možnost pro většinu plány služby App Service. Pokud používáte služby App Service Environment nebo chcete migrovat do služby App Service Environment velikosti pracovní zátěže, však můžete získat větší kontrolu nad prostředí. To zahrnuje zákaz protokolu TLS 1.0 ve obrátíte na podporu Azure. V blízké budoucnosti plánujeme přístupný uživatelům, aby se tato nastavení.

Další informace najdete v tématu [Microsoft Azure App Service webové aplikace dodržování PCI standardní 3.0 a 3.1](https://support.microsoft.com/help/3124528).

## <a name="how-do-i-use-the-staging-environment-and-deployment-slots"></a>Použití přípravné sloty prostředí a nasazení

V plánech Standard a Premium služby App Service při nasazení webové aplikace do služby App Service můžete nasadit na samostatný nasazovací slot místo na produkční slot výchozí. Nasazovací sloty jsou za chodu webových aplikací, které mají své vlastní názvy hostitelů. Webové aplikace obsah a konfiguraci elementy lze vzájemně zaměněny mezi dvěma sloty nasazení, včetně produkční slot.

Další informace o používání nasazovací sloty najdete v tématu [nastavení pracovní prostředí ve službě App Service](web-sites-staged-publishing.md).

## <a name="how-do-i-access-and-review-webjob-logs"></a>Jak přístup a prohlédněte si protokoly webovou úlohu?

Postup pro kontrolu protokolů webové úlohy:

1. Přihlaste se k vaší [Kudu webu](https://*yourwebsitename*.scm.azurewebsites.net).
2. Vyberte vytvářené webové úlohy.
3. Vyberte **přepnutí výstup** tlačítko.
4. Chcete-li stáhnout výstupní soubor, vyberte **Stáhnout** odkaz.
5. Pro jednotlivé běží, vyberte **jednotlivých vyvolání**.
6. Vyberte **přepnutí výstup** tlačítko.
7. Vyberte odkaz ke stažení.

## <a name="im-trying-to-use-hybrid-connections-with-sql-server-why-do-i-see-the-message-systemoverflowexception-arithmetic-operation-resulted-in-an-overflow"></a>Pokouším mohly používat hybridní připojení se serverem SQL Server. Proč se zobrazuje zpráva "System.OverflowException: Výsledkem Přetečení aritmetické operace"?

Pokud používáte hybridní připojení pro přístup k systému SQL Server, aktualizace rozhraní Microsoft .NET 10. května 2016, může dojít k selhání připojení. Může se tato zpráva:

```
Exception: System.Data.Entity.Core.EntityException: The underlying provider failed on Open. —> System.OverflowException: Arithmetic operation resulted in an overflow. or (64 bit Web app) System.OverflowException: Array dimensions exceeded supported range, at System.Data.SqlClient.TdsParser.ConsumePreLoginHandshake
```

### <a name="resolution"></a>Řešení

Pomocí Správce hybridního připojení, protože byl opraven problém způsobila výjimku. Nezapomeňte [aktualizovat váš správce hybridního připojení](https://go.microsoft.com/fwlink/?LinkID=841308) k vyřešení tohoto problému.

## <a name="how-do-i-add-or-edit-a-url-rewrite-rule"></a>Jak přidat nebo upravit pravidlo přepisování adresu URL?

Můžete přidat nebo upravit pravidlo přepsání adresy URL:

1. Nastavení Správce Internetové informační služby (IIS) tak, aby se připojí k vaší webové aplikace služby App Service. Další informace o připojení Správce služby IIS do služby App Service naleznete v tématu [vzdálené správy Azure webů pomocí Správce služby IIS](https://azure.microsoft.com/blog/remote-administration-of-windows-azure-websites-using-iis-manager/).
2. Ve Správci služby IIS přidat nebo upravit pravidlo přepsání adresy URL. Zjistěte, jak přidat nebo upravit pravidlo přepsání adresy URL, najdete v tématu [modul pro přepisování pravidel přepisování vytvořit pro adresu URL](https://www.iis.net/learn/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module).

## <a name="how-do-i-control-inbound-traffic-to-app-service"></a>Jak řídit příchozí přenosy do služby App Service?

Na úrovni webu máte dvě možnosti pro řízení příchozí přenosy do služby App Service:

* Zapněte funkci Dynamická omezení IP adres. Informace o zapnutí dynamická omezení IP adres najdete v tématu [omezení domény a IP adresy pro weby Azure](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/).
* Zapněte modulu zabezpečení. Další postup zapnutí modulu zabezpečení najdete v tématu [brány firewall webových aplikací ModSecurity na webech Azure](https://azure.microsoft.com/blog/modsecurity-for-azure-websites/).

Pokud používáte služby App Service Environment, můžete použít [brány firewall Barracuda](https://azure.microsoft.com/blog/configuring-barracuda-web-application-firewall-for-azure-app-service-environment/).

## <a name="how-do-i-block-ports-in-an-app-service-web-app"></a>Jak blokovat porty ve webové aplikaci služby App Service?

V prostředí služby App Service sdílené klienta není možné blokovat určité porty vzhledem k povaze infrastruktury. Porty TCP 4016, 4018 a 4020 také může být otevřené pro vzdálené ladění v sadě Visual Studio.

V App Service Environment máte plnou kontrolu nad příchozí a odchozí přenosy. Skupiny zabezpečení sítě můžete omezit nebo blokovat určité porty. Další informace o App Service Environment, najdete v části [Představení služby App Service Environment](https://azure.microsoft.com/blog/introducing-app-service-environment/).

## <a name="how-do-i-capture-an-f12-trace"></a>Jak zaznamenat při trasování F12?

Máte dvě možnosti pro zaznamenání při F12 trasování:

* Trasování F12 HTTP
* Výstup konzoly F12

### <a name="f12-http-trace"></a>Trasování F12 HTTP

1. V Internet Exploreru přejděte na svůj web. Je důležité k přihlášení před provedením další kroky. Trasování F12, jinak hodnota zaznamená citlivá data přihlášení.
2. Stisknutím klávesy F12.
3. Ověřte, zda **sítě** karta je vybraná a pak vyberte zeleným **přehrání** tlačítko.
4. Proveďte kroky, které reprodukujte problém.
5. Vyberte červený **Zastavit** tlačítko.
6. Vyberte **Uložit** tlačítko (ikona disku) a uložte soubor HAR (v Internet Exploreru a  Microsoft Edge) *nebo* pravým tlačítkem na soubor HAR a potom vyberte **uložit jako HAR s obsahem** (v prohlížeči Chrome).

### <a name="f12-console-output"></a>Výstup konzoly F12

1. Vyberte **konzoly** kartě.
2. Pro každé kartě, která obsahuje větší než nula. položky, vyberte kartu (**chyba**, **upozornění**, nebo **informace**). Pokud není vybrána na kartě, je na kartě ikonu šedé nebo černé při přesunutí kurzoru od ho.
3. Klikněte pravým tlačítkem myši v oblasti zpráv v podokně a pak vyberte **zkopírujte všechny**.
4. Vložte zkopírovaný text v souboru a pak soubor uložte.

Chcete-li zobrazit soubor HAR, můžete použít [HAR prohlížeč](http://www.softwareishard.com/har/viewer/).

## <a name="why-do-i-get-an-error-when-i-try-to-connect-an-app-service-web-app-to-a-virtual-network-that-is-connected-to-expressroute"></a>Proč se zobrazí chybu při pokusu připojit aplikační službu webové aplikace na virtuální síť, která je připojená k ExpressRoute?

Pokud se pokusíte připojit k virtuální síti, která je připojená k Azure ExpressRoute webové aplikace Azure, dojde k chybě. Zobrazí se následující zpráva: "Brány není brána sítě VPN."

V současné době nemůže mít připojení k síti VPN point-to-site k virtuální síti, která je připojená k ExpressRoute. Point-to-site VPN a ExpressRoute nemůžou existovat pro stejnou virtuální síť. Další informace najdete v tématu [ExpressRoute a VPN typu site-to-site připojení limity a omezení](../expressroute/expressroute-howto-coexist-classic.md#limits-and-limitations).

## <a name="how-do-i-connect-an-app-service-web-app-to-a-virtual-network-that-has-a-static-routing-policy-based-gateway"></a>Připojení webové aplikace služby App Service k virtuální síti, která má statické směrování brány (zásadové)

V současné době webové aplikace služby App Service nepodporuje připojení k virtuální síti, která má statické směrování brány (zásadové). Pokud cílový virtuální síti již existuje, musí mít point-to-site VPN povoleny v rámci brány dynamického směrování, než může být připojen k aplikaci. Pokud vaše brána je nastavené na statické směrování, nelze povolit síť VPN point-to-site. 

Další informace najdete v tématu [integraci aplikace s virtuální sítě Azure](web-sites-integrate-with-vnet.md#getting-started).

## <a name="in-my-app-service-environment-why-can-i-create-only-one-app-service-plan-even-though-i-have-two-workers-available"></a>V mé App Service Environment, proč lze vytvořit pouze jeden plán služby App Service, přestože má dva pracovníci, které jsou k dispozici?

App Service Environment zajistit odolnost proti chybám, vyžaduje, že každý fond pracovních procesů musí mít alespoň jeden další výpočetních prostředků. Další výpočetní prostředek nelze přiřadit zatížení.

Další informace najdete v tématu [postup vytvoření služby App Service Environment](environment/app-service-web-how-to-create-an-app-service-environment.md).

## <a name="why-do-i-see-timeouts-when-i-try-to-create-an-app-service-environment"></a>Proč se při pokusu o vytvoření služby App Service Environment zobrazuje časové limity?

V některých případech vytváření služby App Service Environment se nezdaří. V takovém případě se zobrazí následující chyba v protokolech aktivity:
```
ResourceID: /subscriptions/{SubscriptionID}/resourceGroups/Default-Networking/providers/Microsoft.Web/hostingEnvironments/{ASEname}
Error:{"error":{"code":"ResourceDeploymentFailure","message":"The resource provision operation did not complete within the allowed timeout period.”}}
```

Pokud chcete tento problém vyřešili, ujistěte se, že žádný z následujících podmínek jsou splněny:
* Podsíť je příliš malá.
* Podsíť není prázdný.
* ExpressRoute zabraňuje požadavků na připojení síťové App Service Environment.
* Chybný skupinu zabezpečení sítě brání požadavků na připojení síťové App Service Environment.
* Vynucené tunelování je zapnutý.

Další informace najdete v tématu [častých problémů při nasazování (vytvoření) nové služby Azure App Service Environment](https://blogs.msdn.microsoft.com/waws/2016/05/13/most-frequent-issues-when-deploying-creating-a-new-azure-app-service-environment-ase/).

## <a name="why-cant-i-delete-my-app-service-plan"></a>Proč nelze odstranit mé plán služby App Service?

Plán služby App Service nelze odstranit, pokud jsou všechny aplikace služby App Service přidružené plán služby App Service. Chcete odstranit plán služby App Service, odeberte všechny přidružené aplikace služby App Service z plán služby App Service.

## <a name="how-do-i-schedule-a-webjob"></a>Jak naplánovat webovou úlohu?

Plánované webové úlohy můžete vytvořit pomocí procesu Cron výrazů:

1. Vytvořte soubor settings.job.
2. V tomto souboru JSON zahrnout pomocí výraz Cron vlastnost plánu: 
    ```
    { "schedule": "{second}
    {minute} {hour} {day}
    {month} {day of the week}" }
    ```

Další informace o plánované webové úlohy najdete v tématu [plánované webové úlohy vytvoření pomocí výrazu Cron](web-sites-create-web-jobs.md#CreateScheduledCRON).

## <a name="how-do-i-perform-penetration-testing-for-my-app-service-app"></a>Jak provádět průnikům testování pro Moje aplikace App Service?

Provést průnikům testování, [odeslat žádost o](https://portal.msrc.microsoft.com/en-us/engage/pentest).

## <a name="how-do-i-configure-a-custom-domain-name-for-an-app-service-web-app-that-uses-traffic-manager"></a>Jak lze nakonfigurovat vlastní název domény pro webové aplikace služby App Service, která používá Traffic Manager?

Naučte se používat vlastní název domény k aplikaci služby App Service, která používá Azure Traffic Manager pro vyrovnávání zatížení, najdete v tématu [nakonfigurovat vlastní název domény pro webové aplikace Azure s nástrojem Traffic Manager](web-sites-traffic-manager-custom-domain-name.md).

## <a name="my-app-service-certificate-is-flagged-for-fraud-how-do-i-resolve-this"></a>Moje certifikát služby App Service je označeny pro podvodů. Jak to lze vyřešit?

Při ověřování domény nákupu certifikát služby App Service zobrazí se následující zpráva:

"Svůj certifikát byl označen příznakem pro možném podvodu. Žádost aktuálně probíhá kontrola. Pokud certifikát není začnou použitelné do 24 hodin, kontaktujte prosím podporu Azure."

Jak zpráva značí, tento proces ověření podvod může trvat až 24 hodin. Během této doby budete nadále zobrazovat zprávy.

Pokud váš certifikát služby App Service nadále zobrazovat tuto zprávu po 24 hodinách, spusťte následující skript prostředí PowerShell. Kontakty skriptu [poskytovatel certifikátu](https://www.godaddy.com/) přímo k vyřešení problému.

```
Login-AzureRmAccount
Set-AzureRmContext -SubscriptionId <subId>
$actionProperties = @{
    "Name"= "<Customer Email Address>"
    };
Invoke-AzureRmResourceAction -ResourceGroupName "<App Service Certificate Resource Group Name>" -ResourceType Microsoft.CertificateRegistration/certificateOrders -ResourceName "<App Service Certificate Resource Name>" -Action resendRequestEmails -Parameters $actionProperties -ApiVersion 2015-08-01 -Force   
```

## <a name="how-do-authentication-and-authorization-work-in-app-service"></a>Jak ověřování a autorizace fungují ve službě App Service?

Podrobnou dokumentaci pro ověřování a autorizace ve službě App Service naleznete v dokumentaci pro různé identifikovat zprostředkovatele přihlášení:
* [Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md)
* [Facebook](app-service-mobile-how-to-configure-facebook-authentication.md)
* [Google](app-service-mobile-how-to-configure-google-authentication.md)
* [Účet Microsoft](app-service-mobile-how-to-configure-microsoft-authentication.md)
* [Twitter](app-service-mobile-how-to-configure-twitter-authentication.md)

## <a name="how-do-i-redirect-the-default-azurewebsitesnet-domain-to-my-azure-web-apps-custom-domain"></a>Jak přesměrovat výchozí *. azurewebsites.net domény k vlastní doméně Moje Azure webové aplikace?

Když vytvoříte nový web pomocí webových aplikací v Azure, výchozí *sitename*. azurewebsites.net domény je přiřazen k webu. Pokud můžete přidat název hostitele vlastní na váš web a nechcete, aby uživatelé mohli mít přístup k výchozí *. azurewebsites.net domény, výchozí adresa URL lze přesměrovat. Zjistěte, jak přesměrovat veškerý provoz z výchozí doménu vašeho webu pro vaši vlastní doménu, najdete v tématu [přesměrovat výchozí doménu na vlastní domény ve službě Azure web apps](http://www.zainrizvi.io/2016/04/07/block-default-azure-websites-domain/).

## <a name="how-do-i-determine-which-version-of-net-version-is-installed-in-app-service"></a>Jak určit, jaká verze rozhraní .NET verze je nainstalována ve službě App Service?

Nejrychlejší způsob, jak najít verzi rozhraní Microsoft .NET, který je nainstalován ve službě App Service je pomocí modulu Kudu konzoly. Kudu konzoly můžete přistupovat z portálu nebo pomocí adresy URL aplikace služby App Service. Podrobné pokyny najdete v tématu [určit nainstalovanou verzi rozhraní .NET ve službě App Service](https://blogs.msdn.microsoft.com/waws/2016/11/02/how-to-determine-the-installed-net-version-in-azure-app-services/).

## <a name="why-isnt-autoscale-working-as-expected"></a>Proč není škálování fungují dle očekávání?

Pokud škálování Azure nebyla změněna v nebo škálovat na více systémů instanci webové aplikace podle očekávání, může být příčinou do scénář, ve kterém jsme záměrně nechtějí škálovat, aby se zabránilo nekonečné smyčce kvůli "netřepotá." Obvykle se to stane, když není k dispozici odpovídající okraje mezi prahovými hodnotami Škálováním na více systémů a škálování. Informace o tom, aby se zabránilo "netřepotá" a informace o dalších škálování osvědčené postupy, najdete v části [škálování osvědčené postupy](../monitoring-and-diagnostics/insights-autoscale-best-practices.md#autoscale-best-practices).

## <a name="why-does-autoscale-sometimes-scale-only-partially"></a>Proč škálování někdy škálovat jenom částečně?

Škálování se aktivuje při překročení předkonfigurované hranice. V některých případech můžete si všimnout, že kapacitu je plná jen částečně ve srovnání se co jste očekávali. Tomu může dojít, pokud se počet instancí, které chcete nejsou k dispozici. V tomto scénáři doplní škálování částečně pomocí dostupný počet instancí. Škálování pak spustí logice obnovte rovnováhu získat větší kapacitu. Přiděluje zbývající instancí. Všimněte si, že to může trvat několik minut.

Pokud nevidíte očekávaný počet instancí za několik minut, může to být způsobeno částečné doplnění byl dostatek aby metriky v rámci hranic. Nebo může mít škálování zmenšování, protože bylo dosaženo dolní hranice metriky.

Pokud žádná z těchto podmínek použití a potíže potrvají, odešlete žádost o podporu.

## <a name="how-do-i-turn-on-http-compression-for-my-content"></a>Jak zapnout komprese protokolu HTTP pro moje obsahu?

Chcete-li na komprese pro statické a dynamické typy obsahu, přidejte následující kód v souboru web.config na úrovni aplikace:

```
<system.webServer>
<urlCompression doStaticCompression="true" doDynamicCompression="true" />
< /system.webServer>
```

Také můžete zadat konkrétní dynamických a statických typy MIME, který chcete komprimovat. Další informace najdete v tématu naše odpověď na otázku fórum v [httpCompression nastavení na jednoduchý Azure web](https://social.msdn.microsoft.com/Forums/azure/890b6d25-f7dd-4272-8970-da7798bcf25d/httpcompression-settings-on-a-simple-azure-website?forum=windowsazurewebsitespreview).

## <a name="how-do-i-migrate-from-an-on-premises-environment-to-app-service"></a>Jak se migraci z místního prostředí do služby App Service?

Pokud chcete migrovat lokality z webových serverů Windows a Linux do služby App Service, můžete použít Azure App Service migrace pomocníka. Nástroj pro migraci vytvoří webové aplikace a databáze v Azure podle potřeby a následně publikuje obsah. Další informace najdete v tématu [Azure App Service migrace pomocníka](https://www.migratetoazure.net/).
