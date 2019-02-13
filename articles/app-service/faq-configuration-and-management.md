---
title: Konfigurace – nejčastější dotazy služby Azure App Service | Dokumentace Microsoftu
description: Získejte odpovědi na časté otázky ke správě konfigurace a správa problémy pro funkci Web Apps služby Azure App Service.
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
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 88051c45f21bdf11807ffcc63d8248cba81ae70b
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56118441"
---
# <a name="configuration-and-management-faqs-for-web-apps-in-azure"></a>Konfigurace a správa nejčastější dotazy ke službě Web Apps v Azure

Tento článek obsahuje odpovědi na nejčastější dotazy (FAQ) o konfiguraci a správě problémy [funkce Web Apps služby Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="are-there-limitations-i-should-be-aware-of-if-i-want-to-move-app-service-resources"></a>Existují omezení, který bych měl(a) vědět když budu chtít přesunout prostředky App Service?

Pokud budete chtít přesunout prostředky App Service pro novou skupinu prostředků nebo předplatného, existuje několik omezení je potřeba vědět. Další informace najdete v tématu [omezení App Service](../azure-resource-manager/resource-group-move-resources.md#app-service-limitations).

## <a name="how-do-i-use-a-custom-domain-name-for-my-web-app"></a>Použití vlastního názvu domény pro svou webovou aplikaci

Odpovědi na běžné dotazy týkající se použití vlastního názvu domény s vaší webové aplikace Azure, najdete v našich sedm pětiminutové video [přidání názvu vlastní domény](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name). Video nabízí návod, jak přidat vlastní název domény. Popisuje způsob použití vlastní adresou URL místo *. azurewebsites.net URL s vaší webovou aplikací služby App Service. Také můžete zobrazit podrobný návod k [jak namapovat vlastní název domény](app-service-web-tutorial-custom-domain.md).


## <a name="how-do-i-purchase-a-new-custom-domain-for-my-web-app"></a>Jak koupit novou vlastní doménu pro svou webovou aplikaci?

Zjistěte, jak zakoupit a nastavení vlastní domény pro webovou aplikaci služby App Service, najdete v článku [koupě a konfigurace vlastního názvu domény ve službě App Service](manage-custom-dns-buy-domain.md).


## <a name="how-do-i-upload-and-configure-an-existing-ssl-certificate-for-my-web-app"></a>Jak nahrát a nakonfigurovat existujícího certifikátu SSL pro svou webovou aplikaci?

Zjistěte, jak nahrát a nastavit existujícího vlastního certifikátu SSL, najdete v článku [vytvoření vazby existujícího vlastního certifikátu SSL na webovou aplikaci Azure](app-service-web-tutorial-custom-ssl.md#upload).


## <a name="how-do-i-purchase-and-configure-a-new-ssl-certificate-in-azure-for-my-web-app"></a>Jak zakoupit a nakonfigurovat nový certifikát protokolu SSL v Azure pro svou webovou aplikaci?

Zjistěte, jak zakoupit a nastavit certifikát SSL pro webovou aplikaci služby App Service, najdete v článku [přidání certifikátu SSL do aplikace služby App Service](web-sites-purchase-ssl-web-site.md).


## <a name="how-do-i-move-application-insights-resources"></a>Jak můžu přesunout prostředky Application Insights?

Azure Application Insights v současné době nepodporuje operaci přesunutí. Pokud váš původním skupina prostředků obsahuje prostředek Application Insights, se nedá přesunout tento prostředek. Zadáte-li prostředek Application Insights při pokusu o přesunutí aplikaci služby App Service, přesunout celé operace se nezdaří. Ale služba Application Insights a plán služby App Service nemusíte být ve stejné skupině prostředků jako aplikace pro aplikaci fungovat správně.

Další informace najdete v tématu [omezení App Service](../azure-resource-manager/resource-group-move-resources.md#app-service-limitations).

## <a name="where-can-i-find-a-guidance-checklist-and-learn-more-about-resource-move-operations"></a>Kde můžu najít kontrolní pokyny a další informace o prostředku přesunout operations?

[Omezení služby App Service](../azure-resource-manager/resource-group-move-resources.md#app-service-limitations) ukazuje, jak prostředky přesunout do nového předplatného nebo do nové skupiny prostředků ve stejném předplatném. Můžete získat informace o kontrolní seznam přesunutí prostředku, další služby, které podporují operace přesunu a další informace o omezení App Service a dalšími tématy.

## <a name="how-do-i-set-the-server-time-zone-for-my-web-app"></a>Jak nastavit časové pásmo serveru pro svou webovou aplikaci?

Chcete-li nastavit časové pásmo serveru pro vaši webovou aplikaci:

1. Na portálu Azure ve vašem předplatném služby App Service, přejděte **nastavení aplikace** nabídky.
2. V části **nastavení aplikace**, přidejte tato nastavení:
    * Klíč = WEBSITE_TIME_ZONE
    * Hodnota = *časové pásmo má*
3. Vyberte **Uložit**.

Najdete v článku **časové pásmo** sloupec v [výchozí časových pásem](https://docs.microsoft.com/windows-hardware/manufacture/desktop/default-time-zones) článku platných hodnot.

## <a name="why-do-my-continuous-webjobs-sometimes-fail"></a>Proč moje průběžné WebJobs někdy nezdaří?

Ve výchozím nastavení webové aplikace jsou uvolněna, pokud jsou nastavte dobu nečinnosti. To umožňuje ušetřit prostředky systému. V plánech Basic a Standard můžete zapnout **Always On** nastavení Udržovat webové aplikace načíst celou dobu. Pokud vaše webová aplikace spouští průběžné WebJobs, měli byste zapnout **Always On**, nebo webové úlohy nemusí fungovat spolehlivě. Další informace najdete v tématu [vytvořit nepřetržitě běžící webová úloha](webjobs-create.md#CreateContinuous).

## <a name="how-do-i-get-the-outbound-ip-address-for-my-web-app"></a>Jak získat odchozí IP adresa pro svou webovou aplikaci?

Pokud chcete získat seznam odchozí IP adresy pro vaši webovou aplikaci:

1. Na webu Azure Portal, v okně vaší webové aplikace, přejděte **vlastnosti** nabídky.
2. Vyhledejte **odchozí ip adresy**.

Zobrazí se seznam odchozí IP adresy.

Zjistěte, jak zjistit odchozí IP adresu, pokud váš web je hostovaný ve službě App Service Environment, najdete v článku [odchozí síťové adresy](environment/app-service-app-service-environment-network-architecture-overview.md#outbound-network-addresses).

## <a name="how-do-i-get-a-reserved-or-dedicated-inbound-ip-address-for-my-web-app"></a>Jak získat rezervovanou nebo vyhrazenou příchozí IP adresu pro svou webovou aplikaci?

Pokud chcete nastavit rezervovanou nebo vyhrazenou IP adresu pro příchozí volání směrovaná na váš web aplikace Azure, instalace a konfigurace certifikátu SSL na základě IP adresy.

Mějte na paměti, že pokud chcete použít vyhrazený nebo rezervovanou IP adresu pro příchozí volání, plán služby App Service musí být plán služby Basic nebo vyšší.

## <a name="can-i-export-my-app-service-certificate-to-use-outside-azure-such-as-for-a-website-hosted-elsewhere"></a>Můžete exportovat certifikát služby App Service pro použití mimo Azure, například pro web hostovaný jinde? 

Certifikáty služby App Service jsou považovány za prostředky Azure. Nejsou určeny pro použití mimo služeb Azure. Nelze exportovat je, aby používaly mimo Azure. Další informace najdete v tématu [nejčastější dotazy k certifikátům služby App Service a vlastních domén](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).

## <a name="can-i-export-my-app-service-certificate-to-use-with-other-azure-cloud-services"></a>Můžete exportovat certifikát služby App Service pro použití s dalšími službami Azure cloud?

Portál poskytuje prvotřídní prostředí pro nasazení služby App Service certificate prostřednictvím Azure Key Vault do aplikací App Service. Ale budeme mít byla přijímat žádosti od zákazníků, které používat tyto certifikáty i mimo platformu App Service, třeba s Azure Virtual Machines. Zjistěte, jak vytvořit místní kopii PFX služby App Service Certificate, abyste mohli použít certifikát v jiných prostředcích Azure, najdete v článku [vytvořit místní kopii PFX služby App Service Certificate](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/).

Další informace najdete v tématu [nejčastější dotazy k certifikátům služby App Service a vlastních domén](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).


## <a name="why-do-i-see-the-message-partially-succeeded-when-i-try-to-back-up-my-web-app"></a>Proč se při pokusu o zálohování webové aplikace zobrazí zpráva, "Bylo částečně dokončeno"?

Obvyklou příčinou selhání zálohování je, že některé soubory jsou právě používány aplikace. Soubory, které se používají jsou zamknuté při současném provádění zálohování. To zabraňuje zálohovaných tyto soubory a může vést k stavem "Částečně úspěšné". To může potenciálně zabránit výskytu že vyloučíte soubory z procesu zálohování. Můžete k zálohování jenom toho, co je potřeba. Další informace najdete v tématu [zálohovat jenom důležité části vašeho webu pomocí služby Azure web apps](https://zainrizvi.io/blog/creating-partial-backups-of-your-site-with-azure-web-apps/).

## <a name="how-do-i-remove-a-header-from-the-http-response"></a>Jak odebrat hlavičku HTTP odpovědi

Odebrání hlaviček z odpovědi HTTP, aktualizujte soubor web.config. Další informace najdete v tématu [odebrání hlaviček standardní server na Azure websites](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

## <a name="is-app-service-compliant-with-pci-standard-30-and-31"></a>Je kompatibilní s PCI Standard 3.0 a 3.1 služby App Service?

V současné době funkce Web Apps služby Azure App Service je v souladu s PCI Data Security Standard (DSS) verze 3.0 úrovně 1. PCI DSS verze 3.1 je do našeho plánu. Plánování je již probíhá pro jak přechod na nejnovější úrovně Standard bude pokračovat.

Certifikace PCI DSS verze 3.1 vyžaduje zakázání zabezpečení TLS (Transport Layer) 1.0. V současné době zákaz protokolu TLS 1.0 není možnost pro většinu plány služby App Service. Pokud používáte službu App Service Environment nebo ochotni migrace vašich úloh do služby App Service Environment, ale můžete získat větší kontrolu nad vaším prostředím. To zahrnuje zákaz protokolu TLS 1.0 ve kontaktovat podporu Azure. Plánujeme v blízké budoucnosti, aby byly tyto nastavení uživatele.

Další informace najdete v tématu [Microsoft Azure App Service web aplikace dodržování standardu PCI 3.0 a 3.1](https://support.microsoft.com/help/3124528).

## <a name="how-do-i-use-the-staging-environment-and-deployment-slots"></a>Použití přípravného prostředí a nasazovací sloty

V plánech Standard a Premium služby App Service když nasadíte webovou aplikaci do služby App Service můžete nasadit do samostatného nasazení slotu místo na produkční slot výchozí. Sloty nasazení jsou živé webové aplikace, které mají své vlastní názvy hostitelů. Mezi dvěma sloty nasazení, včetně produkčního slotu, je možné Prohodit prvky obsah a konfiguraci webové aplikace.

Další informace o používání slotů nasazení najdete v tématu [nastavit testovací prostředí ve službě App Service](deploy-staging-slots.md).

## <a name="how-do-i-access-and-review-webjob-logs"></a>Jak získat přístup a zkontrolovat protokoly?

Zkontrolujte protokoly:

1. Přihlaste se k vaší [webu Kudu](https://*yourwebsitename*.scm.azurewebsites.net).
2. Vyberte webová úloha.
3. Vyberte **přepnout výstup** tlačítko.
4. Stáhněte výstupní soubor, vyberte **Stáhnout** odkaz.
5. Pro jednotlivé testy, vyberte **jednotlivých vyvolat**.
6. Vyberte **přepnout výstup** tlačítko.
7. Vyberte odkaz ke stažení.

## <a name="im-trying-to-use-hybrid-connections-with-sql-server-why-do-i-see-the-message-systemoverflowexception-arithmetic-operation-resulted-in-an-overflow"></a>Chci používat hybridní připojení se serverem SQL Server. Proč se zobrazí zpráva "System.OverflowException: Aritmetická operace skončila přetečením"?

Pokud používáte Hybrid Connections pro přístup k systému SQL Server, Microsoft .NET aktualizace 10. května 2016, může dojít k selhání připojení. Může se zobrazit tato zpráva:

```
Exception: System.Data.Entity.Core.EntityException: The underlying provider failed on Open. —> System.OverflowException: Arithmetic operation resulted in an overflow. or (64 bit Web app) System.OverflowException: Array dimensions exceeded supported range, at System.Data.SqlClient.TdsParser.ConsumePreLoginHandshake
```

### <a name="resolution"></a>Řešení

Pomocí Správce hybridního připojení, které od té doby jsme opravili problém způsobila výjimku. Nezapomeňte [aktualizace správce hybridních připojení](https://go.microsoft.com/fwlink/?LinkID=841308) k vyřešení tohoto problému.

## <a name="how-do-i-add-or-edit-a-url-rewrite-rule"></a>Jak přidat nebo upravit pravidlo pro přepis adres URL?

Můžete přidat nebo upravit pravidlo pro přepis adres URL:

1. Nastavte Správce Internetové informační služby (IIS) tak, aby se připojuje ke službě App Service webovou aplikaci. Informace o připojení Správce služby IIS do služby App Service, najdete v článku [Vzdálená správa Azure websites pomocí Správce služby IIS](https://azure.microsoft.com/blog/remote-administration-of-windows-azure-websites-using-iis-manager/).
2. Ve Správci služby IIS přidat nebo upravit pravidlo pro přepis adres URL. Zjistěte, jak přidat nebo upravit pravidlo pro přepis adres URL, najdete v článku [vytvořit pravidla pro přepis pro adresu URL revize modulu](https://www.iis.net/learn/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module).

## <a name="how-do-i-control-inbound-traffic-to-app-service"></a>Jak můžu řízení příchozího provozu do služby App Service?

Na úrovni serveru máte dvě možnosti pro řízení příchozího provozu do služby App Service:

* Zapnutí dynamické omezení IP adres. Zjistěte, jak zapnout dynamická omezení IP adres, najdete v článku [omezení domény a IP adresy pro Azure websites na úrovni](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/).
* Zapnutí modul zabezpečení. Zjistěte, jak zapnout modulu zabezpečení, najdete v článku [ModSecurity firewallu webových aplikací ve službě Azure websites](https://azure.microsoft.com/blog/modsecurity-for-azure-websites/).

Pokud používáte službu App Service Environment, můžete použít [brány firewall Barracuda](https://azure.microsoft.com/blog/configuring-barracuda-web-application-firewall-for-azure-app-service-environment/).

## <a name="how-do-i-block-ports-in-an-app-service-web-app"></a>Jak můžu zablokovat porty ve webové aplikaci App Service?

V App Service environment sdílené tenanta není možné zablokují konkrétní porty vzhledem k povaze infrastruktury. Porty TCP 4016 4018 a 4020 také může být otevřené pro vzdálené ladění sady Visual Studio.

V App Service Environment máte plnou kontrolu nad příchozí a odchozí provoz. Skupiny zabezpečení sítě můžete použít k omezení nebo zablokují konkrétní porty. Další informace o službě App Service Environment najdete v tématu [Úvod do služby App Service Environment](https://azure.microsoft.com/blog/introducing-app-service-environment/).

## <a name="how-do-i-capture-an-f12-trace"></a>Jak můžu zaznamenat trasování F12?

Máte dvě možnosti pro zaznamenání trasování F12:

* Trasování F12 HTTP
* Výstup na konzole F12

### <a name="f12-http-trace"></a>Trasování F12 HTTP

1. V Internet Exploreru přejděte na svůj web. Je důležité pro přihlášení, než se pustíte do dalších kroků. V opačném případě F12 trasování zachytí citlivá data přihlášení.
2. Stisknutím klávesy F12.
3. Ověřte, že **sítě** karta je vybrána a potom vyberte zelené **Přehrát** tlačítko.
4. Proveďte kroky, které problém reprodukovat.
5. Vyberte červený **Zastavit** tlačítko.
6. Vyberte **Uložit** tlačítko (ikona disku) a soubor HAR uložte (v Internet Exploreru a Microsoft Edge) *nebo* klikněte pravým tlačítkem na soubor HAR a pak vyberte **uložit jako HAR s obsahem**(v prohlížeči Chrome).

### <a name="f12-console-output"></a>Výstup na konzole F12

1. Vyberte **konzoly** kartu.
2. Pro každou kartu, která obsahuje více než nulové položky, vyberte kartu (**chyba**, **upozornění**, nebo **informace**). Pokud není vybrána na kartu, kartu ikona je šedé nebo černé při přesunutí kurzoru od jeho.
3. Klikněte pravým tlačítkem v oblasti zpráv v podokně a pak vyberte **Kopírovat vše**.
4. Vložit zkopírovaný text do souboru a pak soubor uložte.

Chcete-li zobrazit soubor HAR, můžete použít [HAR prohlížeč](https://www.softwareishard.com/har/viewer/).

## <a name="why-do-i-get-an-error-when-i-try-to-connect-an-app-service-web-app-to-a-virtual-network-that-is-connected-to-expressroute"></a>Proč získám k chybě při pokusu o připojení služby App Service webovou aplikaci k virtuální síti, která je připojená k ExpressRoute?

Pokud se pokusíte připojit webovou aplikaci Azure k virtuální síti, která je připojená k Azure ExpressRoute, dojde k chybě. Zobrazí se následující zpráva: "Brána není bránou sítě VPN."

V současné době nemůžete mít připojení VPN typu point-to-site k virtuální síti, která je připojená k ExpressRoute. Point-to-site VPN a ExpressRoute nemůžou být společně pro stejnou virtuální síť. Další informace najdete v tématu [ExpressRoute a VPN typu site-to-site připojení limity a omezení](../expressroute/expressroute-howto-coexist-classic.md#limits-and-limitations).

## <a name="how-do-i-connect-an-app-service-web-app-to-a-virtual-network-that-has-a-static-routing-policy-based-gateway"></a>Jak se připojit k webové aplikaci App Service k virtuální síti, která má statické směrování brány (zásadové)?

V současné době připojení k webové aplikaci App Service k virtuální síti, která má statické směrování brány (zásadové) není podporováno. Pokud cílová virtuální síť už existuje, musí mít povoleno brány dynamického směrování, než může být připojen k aplikaci VPN point-to-site. Pokud vaše brána je nastavena na statické směrování, nelze povolit síť VPN point-to-site. 

Další informace najdete v tématu [integrovat aplikace s Azure virtual network](web-sites-integrate-with-vnet.md#getting-started).

## <a name="in-my-app-service-environment-why-can-i-create-only-one-app-service-plan-even-though-i-have-two-workers-available"></a>Do své služby App Service Environment, proč je možné vytvořit jenom jeden plán služby App Service, i když mám k dispozici dva pracovní procesy?

Zajištění odolnosti proti chybám vyžaduje App Service Environment, že každý fond pracovních procesů musí alespoň jednoho dalšího výpočetního prostředku. Úlohu nelze přiřadit dalšího výpočetního prostředku.

Další informace najdete v tématu [vytvoření služby App Service Environment](environment/app-service-web-how-to-create-an-app-service-environment.md).

## <a name="why-do-i-see-timeouts-when-i-try-to-create-an-app-service-environment"></a>Proč se zobrazuje vypršení časového limitu při pokusu o vytvoření služby App Service Environment?

V některých případech vytváření služby App Service Environment se nezdaří. V takovém případě se zobrazí následující chybu v protokolech aktivit:
```
ResourceID: /subscriptions/{SubscriptionID}/resourceGroups/Default-Networking/providers/Microsoft.Web/hostingEnvironments/{ASEname}
Error:{"error":{"code":"ResourceDeploymentFailure","message":"The resource provision operation did not complete within the allowed timeout period.”}}
```

Chcete-li tento problém vyřešit, ujistěte se, že žádný z následujících podmínek jsou splněny:
* Podsíť je příliš malá.
* Podsíť není prázdná.
* ExpressRoute brání požadavky síťového připojení ze služby App Service Environment.
* Chybná skupina zabezpečení sítě brání požadavky síťového připojení ze služby App Service Environment.
* Vynucené tunelování je zapnutá.

Další informace najdete v tématu [časté problémy při nasazení (vytvoření) nové Azure App Service Environment](https://blogs.msdn.microsoft.com/waws/2016/05/13/most-frequent-issues-when-deploying-creating-a-new-azure-app-service-environment-ase/).

## <a name="why-cant-i-delete-my-app-service-plan"></a>Proč nelze odstranit tento plán služby App Service?

Plán služby App Service nejde odstranit, pokud všechny aplikace služby App Service jsou spojeny s plánem služby App Service. Před odstraněním plán služby App Service, odeberte všechny přidružené aplikace služby App Service z plánu služby App Service.

## <a name="how-do-i-schedule-a-webjob"></a>Jak naplánovat webovou úlohu?

Můžete vytvořit naplánovanou webovou úlohu pomocí výrazů Cron:

1. Vytvořte soubor settings.job.
2. V tomto souboru JSON patří vlastnost schedule pomocí výrazu Cron: 
    ```json
    { "schedule": "{second}
    {minute} {hour} {day}
    {month} {day of the week}" }
    ```

Další informace o plánované webové úlohy, naleznete v tématu [vytvořit naplánovanou webovou úlohu pomocí výrazů Cron](webjobs-create.md#CreateScheduledCRON).

## <a name="how-do-i-perform-penetration-testing-for-my-app-service-app"></a>Jak provedu ho pro moji aplikaci služby App Service?

Provádět testování průniku [odeslat žádost o](https://portal.msrc.microsoft.com/en-us/engage/pentest).

## <a name="how-do-i-configure-a-custom-domain-name-for-an-app-service-web-app-that-uses-traffic-manager"></a>Jak nakonfigurovat vlastní název domény pro webovou aplikaci služby App Service, který používá Traffic Manager?

Další informace o použití vlastního názvu domény aplikace služby App Service, která používá Azure Traffic Manager pro vyrovnávání zatížení, najdete v článku [konfigurace vlastního názvu domény pro webovou aplikaci Azure s využitím Traffic Manageru](web-sites-traffic-manager-custom-domain-name.md).

## <a name="my-app-service-certificate-is-flagged-for-fraud-how-do-i-resolve-this"></a>Certifikát App Service je označen jako podvodů. Jak to můžu vyřešit?

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Při ověřování domény nákupu certifikátů služby App Service může se zobrazit následující zpráva:

"Váš certifikát se označil jako potenciálně podvodný. Požadavek je právě probíhá kontrola. Pokud certifikát není autentický během 24 hodin, kontaktujte prosím podporu Azure. "

Jak zpráva označuje, tento proces ověření podvod může trvat až 24 hodin. Během této doby bude nadále zobrazovat zprávy.

Pokud služby App Service certificate se nadále zobrazovat tento dialog po 24 hodinách, spusťte následující skript prostředí PowerShell. Skript kontakty [poskytovatel certifikátu](https://www.godaddy.com/) přímo k vyřešení daného problému.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subId>
$actionProperties = @{
    "Name"= "<Customer Email Address>"
    };
Invoke-AzResourceAction -ResourceGroupName "<App Service Certificate Resource Group Name>" -ResourceType Microsoft.CertificateRegistration/certificateOrders -ResourceName "<App Service Certificate Resource Name>" -Action resendRequestEmails -Parameters $actionProperties -ApiVersion 2015-08-01 -Force   
```

## <a name="how-do-authentication-and-authorization-work-in-app-service"></a>Jak ověřování a autorizace funguje ve službě App Service?

Podrobnou dokumentaci pro ověřování a autorizace ve službě App Service najdete v dokumentaci pro různé identifikovat zprostředkovatele přihlášení:
* [Azure Active Directory](configure-authentication-provider-aad.md)
* [Facebook](configure-authentication-provider-facebook.md)
* [Google](configure-authentication-provider-google.md)
* [Účet Microsoft](configure-authentication-provider-microsoft.md)
* [Twitter](configure-authentication-provider-twitter.md)

## <a name="how-do-i-redirect-the-default-azurewebsitesnet-domain-to-my-azure-web-apps-custom-domain"></a>Jak přesměrovat výchozí *. azurewebsites.net domény k vlastní doméně Moje webové aplikace Azure?

Při vytváření nového webu pomocí Web Apps v Azure, výchozí *sitename*. doménu azurewebsites.net je přiřazen k webu. Pokud přidáte do webu vlastním názvem hostitele a nechcete, aby uživatelé měli přístup k výchozí *. doméně azurewebsites.net, můžete přesměrovat výchozí adresy URL. Zjistěte, jak přesměrovat veškerý provoz z vašeho webu výchozí doménu k vaší vlastní doméně, najdete v článku [výchozí doménu pro přesměrování do vaší vlastní domény ve službě Azure web apps](https://zainrizvi.io/blog/block-default-azure-websites-domain/).

## <a name="how-do-i-determine-which-version-of-net-version-is-installed-in-app-service"></a>Jak zjistím, která verze rozhraní .NET verze nainstalovaná ve službě App Service?

Nejrychlejší způsob, jak najít verzi rozhraní Microsoft .NET, který je nainstalován ve službě App Service je pomocí konzoly Kudu. Konzola Kudu můžete přistupovat z portálu nebo pomocí adresy URL aplikace app Service. Podrobné pokyny najdete v tématu [určení Instalovatelné verze rozhraní .NET ve službě App Service](https://blogs.msdn.microsoft.com/waws/2016/11/02/how-to-determine-the-installed-net-version-in-azure-app-services/).

## <a name="why-isnt-autoscale-working-as-expected"></a>Proč automatické škálování nefunguje podle očekávání?

Případně Azure Autoscale nebyl horizontálně škálovat instance webové aplikace podle očekávání, může běžet na scénář, ve kterém záměrně zvolíme nechcete škálovat, aby nekonečnou smyčku kvůli "netřepotá." Obvykle se to stane, když není k dispozici odpovídající okraje mezi prahovými hodnotami horizontální navýšení kapacity a škálování na méně instancí. Naučte se, abyste se vyhnuli "netřepotá" a informace o dalších osvědčené postupy automatického škálování, najdete v článku [osvědčené postupy automatického škálování](../azure-monitor/platform/autoscale-best-practices.md#autoscale-best-practices).

## <a name="why-does-autoscale-sometimes-scale-only-partially"></a>Proč automatické škálování někdy škálovat jenom částečně?

Automatické škálování se aktivuje, když metriky překročí hranice předem. V některých případech můžete všimnout, že kapacita vyplněno pouze částečně ve srovnání s co jste očekávali. Tato situace může nastat, pokud počet instancí, které chcete, aby nejsou k dispozici. V tomto scénáři automatické škálování částečně vyplní pomocí dostupný počet instancí. Logika obnovení rovnováhy získat větší kapacitu pak spustí automatické škálování. Přiděluje zbývající instancí. Všimněte si, že to může trvat několik minut.

Pokud nevidíte očekávaný počet instancí za pár minut, může to být způsobeno částečné doplnění bylo dost informací k používání metrik v rámci hranic. Nebo automatické škálování může mít kapacitu vertikálně snížit protože bylo dosaženo dolní hranice metriky.

Pokud žádná z těchto podmínek použití a problém přetrvává, odešlete žádost o podporu.

## <a name="how-do-i-turn-on-http-compression-for-my-content"></a>Jak zapnout komprese protokolu HTTP pro můj obsah?

Zapnutí komprese pro statické a dynamické typy obsahu, přidejte následující kód do souboru web.config na úrovni aplikace:

```xml
<system.webServer>
    <urlCompression doStaticCompression="true" doDynamicCompression="true" />
</system.webServer>
```

Také můžete zadat konkrétní dynamického a statického typy MIME, které chcete komprimovat. Další informace najdete v tématu naše reakce na fóru dotaz v [httpCompression nastavení na jednoduchý web Azure](https://social.msdn.microsoft.com/Forums/azure/890b6d25-f7dd-4272-8970-da7798bcf25d/httpcompression-settings-on-a-simple-azure-website?forum=windowsazurewebsitespreview).

## <a name="how-do-i-migrate-from-an-on-premises-environment-to-app-service"></a>Jak migrovat z místního prostředí do služby App Service?

Pokud chcete migrovat servery z webových serverů Windows a Linux do služby App Service, můžete použít Azure App Service Pomocníka s migrací. Nástroj pro migraci vytvoří webových aplikací a databází v Azure podle potřeby a pak publikuje obsah. Další informace najdete v tématu [Azure App Service Pomocníka s migrací](https://www.migratetoazure.net/).
