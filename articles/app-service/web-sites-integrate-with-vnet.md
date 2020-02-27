---
title: Integrace aplikace s využitím Azure Virtual Network
description: Integrujte aplikace v Azure App Service s virtuálními sítěmi Azure.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 02/27/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 76139716fe11536faa0ff792185ba1643801c641
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77648990"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrace aplikace s Virtual Network Azure
Tento dokument popisuje funkci Integrace virtuální sítě Azure App Service a jak ji nastavit pomocí aplikací v [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). [Virtuální sítě Azure][VNETOverview] (virtuální sítě) umožňují umístit spoustu vašich prostředků Azure do sítě směrovatelné do jiné sítě.  

Azure App Service má dvě variace. 

1. Víceklientské systémy, které podporují celou škálu cenových plánů s výjimkou izolovaného
2. App Service Environment (pomocného mechanismu), který se nasadí do vaší virtuální sítě a podporuje aplikace izolovaného cenového plánu

Tento dokument projde funkcí integrace virtuální sítě, která je určena pro použití ve více tenantů App Service. Pokud je vaše aplikace v [App Service Environment][ASEintro], pak už je ve virtuální síti a nevyžaduje použití funkce integrace virtuální sítě k přístupu k prostředkům ve stejné virtuální síti. Podrobnosti o všech funkcích App Service sítě najdete v článku [funkce App Service sítě](networking-features.md) .

Integrace virtuální sítě poskytuje vaší webové aplikaci přístup k prostředkům ve vaší virtuální síti, ale neuděluje příchozímu privátnímu přístupu do vaší webové aplikace z virtuální sítě. Přístup k soukromému webu znamená, že aplikace je přístupná jenom z privátní sítě, jako je například z Azure Virtual Network. Integrace virtuální sítě je jenom pro odchozí volání z vaší aplikace do vaší virtuální sítě. Funkce integrace virtuální sítě se chová jinak, když se používá s virtuální sítě ve stejné oblasti a v virtuální sítě v jiných oblastech. Funkce integrace virtuální sítě má dvě variace.

1. Místní integrace virtuální sítě – při připojování k Správce prostředků virtuální sítě ve stejné oblasti musíte mít ve virtuální síti, ve které se integruje, vyhrazenou podsíť. 
2. Brána vyžaduje integraci virtuální sítě – při připojování k virtuální sítě v jiných oblastech nebo v klasické virtuální síti ve stejné oblasti potřebujete bránu Virtual Network zřízenou v cílové virtuální síti VNet.

Funkce integrace virtuální sítě:

* vyžadování cenového plánu Standard, Premium, PremiumV2 nebo elastické Premium 
* Podpora protokolů TCP a UDP
* práce s aplikacemi App Service a aplikacemi funkcí

Integrace virtuální sítě nepodporuje zahrnutí následujících věcí:

* připojení jednotky
* Integrace služby AD 
* NetBIOS

Požadovaná integrace virtuální sítě pro bránu poskytuje přístup k prostředkům v cílové virtuální síti nebo v sítích připojených k cílové virtuální síti s partnerským vztahem nebo VPN. Požadovaná integrace virtuální sítě brány nepovoluje přístup k prostředkům dostupným napříč ExpressRoute připojeními nebo pracuje s koncovými body služby. 

Bez ohledu na použitou verzi poskytuje integrace virtuální sítě přístup k prostředkům ve vaší virtuální síti, ale neuděluje příchozímu privátnímu přístupu do vaší webové aplikace z virtuální sítě. Přístup k soukromému webu znamená, že aplikace je přístupná jenom z privátní sítě, jako je například z Azure Virtual Network. Integrace virtuální sítě je jenom pro odchozí volání z vaší aplikace do vaší virtuální sítě. 

## <a name="enable-vnet-integration"></a>Povolit integraci virtuální sítě 

1. Přejít na uživatelské rozhraní sítě na portálu App Service. V části Integrace virtuální sítě vyberte *kliknutím sem proveďte konfiguraci*. 

1. Vyberte **Přidat virtuální síť**.  

   ![Vybrat integraci virtuální sítě][1]

1. Rozevírací seznam bude obsahovat všechny Správce prostředků virtuální sítě ve vašem předplatném ve stejné oblasti a níže je seznam všech Správce prostředků virtuální sítě ve všech ostatních oblastech. Vyberte virtuální síť, se kterou chcete integrovat.

   ![Vyberte virtuální síť.][2]

   * Pokud je virtuální síť ve stejné oblasti, pak buď vytvořte novou podsíť, nebo vyberte prázdnou již existující podsíť. 

   * Pokud chcete vybrat virtuální síť v jiné oblasti, musíte mít zřízenou bránu Virtual Network s povoleným odkazem na lokalitu.

   * Pokud chcete integrovat s klasickou virtuální sítí, místo kliknutí na rozevírací nabídku virtuální síť vyberte **kliknutím sem se připojte k klasické virtuální**síti. Vyberte požadovanou klasickou virtuální síť. Cílová virtuální síť již musí mít zřízenou bránu Virtual Network s povoleným odkazem na lokalitu.

    ![Vybrat klasickou virtuální síť][3]
    
Během integrace se aplikace restartuje.  Po dokončení integrace se zobrazí podrobnosti o virtuální síti, do které jste integrováni. 

Jakmile se vaše aplikace integruje do vaší virtuální sítě, bude používat stejný server DNS, se kterým je nakonfigurovaná vaše virtuální síť, pokud není Azure DNS Private Zones. V tuto chvíli nemůžete použít integraci virtuální sítě s Azure DNS Private Zones.

## <a name="regional-vnet-integration"></a>Místní integrace virtuální sítě

Použití místní integrace virtuální sítě umožňuje aplikacím přístup k těmto akcím:

* prostředky ve virtuální síti ve stejné oblasti, ve které jste integraci s nástrojem 
* prostředky v virtuální sítě s partnerským vztahem k virtuální síti, které jsou ve stejné oblasti
* zabezpečené služby koncového bodu služby
* prostředky mezi ExpressRoute připojeními
* prostředky ve virtuální síti, ke které jste připojení
* prostředky napříč partnerskými připojeními, včetně připojení ExpressRoute
* Soukromé koncové body 

Při použití integrace virtuální sítě s virtuální sítě ve stejné oblasti můžete použít následující funkce sítě Azure:

* Skupiny zabezpečení sítě (skupin zabezpečení sítě) – odchozí přenosy můžete blokovat pomocí skupiny zabezpečení sítě, která je umístěná v podsíti integrace. Příchozí pravidla neplatí, protože nemůžete použít integraci virtuální sítě k poskytnutí příchozího přístupu do vaší webové aplikace.
* Směrovací tabulky (udr) – směrovací tabulku můžete umístit do podsítě Integration, abyste mohli odesílat odchozí přenosy tam, kde chcete. 

Ve výchozím nastavení bude vaše aplikace směrovat jenom RFC1918 provoz do vaší virtuální sítě. Pokud chcete směrovat veškerý odchozí provoz do vaší virtuální sítě, použijte nastavení aplikace WEBSITE_VNET_ROUTE_ALL pro vaši aplikaci. Konfigurace nastavení aplikace:

1. Na portálu aplikací přejdete na uživatelské rozhraní konfigurace. Vybrat **nové nastavení aplikace**
1. Do pole název zadejte **WEBSITE_VNET_ROUTE_ALL** a do pole hodnota zadejte hodnotu **1** .

   ![Zadat nastavení aplikace][4]

1. Vyberte **OK**.
1. Vyberte **Uložit**.

Pokud budete směrovat veškerý odchozí provoz do virtuální sítě, bude se vztahovat na skupin zabezpečení sítě a udr, které se použijí pro vaši podsíť integrace. Při směrování veškerého odchozího provozu do virtuální sítě budou vaše odchozí adresy pořád odchozími adresami, které jsou uvedené ve vlastnostech vaší aplikace, pokud nezadáte trasy k odeslání provozu jinde. 

Při použití integrace virtuální sítě s virtuální sítě ve stejné oblasti je potřeba mít určitá omezení:

* Nemůžete se připojit k prostředkům napříč globálními připojeními partnerských vztahů.
* Tato funkce je dostupná jenom z novějších jednotek škálování App Service, které podporují plány App Service PremiumV2.
* Podsíť integrace se dá použít jenom v jednom plánu App Service.
* Tuto funkci nemůže používat aplikace izolovaného plánu, které jsou ve App Service Environment.
* Tato funkce vyžaduje nepoužitou podsíť, která je/27 s 32 adresou nebo větší ve Správce prostředků VNet.
* Aplikace a virtuální síť musí být ve stejné oblasti.
* Virtuální síť nejde odstranit pomocí integrované aplikace. Před odstraněním virtuální sítě odeberte integraci. 
* Integraci s virtuální sítě můžete integrovat jenom do stejného předplatného, jako je webová aplikace.
* Pro každý App Service plán můžete mít jenom jednu místní integraci virtuální sítě. Víc aplikací ve stejném plánu App Service může používat stejnou virtuální síť. 
* Pokud máte aplikaci, která používá místní integraci virtuální sítě, nemůžete změnit předplatné aplikace ani plán App Service.

Pro každou instanci App Service plánu se používá jedna adresa. Při škálování aplikace na pět instancí se použije pět adres. Vzhledem k tomu, že velikost podsítě po přiřazení nelze změnit, je nutné použít podsíť, která je dostatečně velká, aby mohla pojmout libovolné škálování vaší aplikace. Doporučená velikost je a/26 s 64 adresami. A/26 s 64 adresami se pro plán Premium App Service přizpůsobí 30 instancím. Při horizontálním navýšení nebo zmenšení plánu App Service budete potřebovat dvakrát několik adres po krátkou dobu. 

Pokud chcete, aby vaše aplikace v jiném App Service naplánovaly přístup k virtuální síti, ke které už aplikace v jiném plánu App Service existuje, musíte vybrat jinou podsíť, než kterou používá již existující integrace virtuální sítě.  

Tato funkce je ve verzi Preview pro Linux. Forma systému Linux funkce podporuje pouze volání adres RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16).

### <a name="web-app-for-containers"></a>Web App for Containers

Pokud používáte App Service v systému Linux s vestavěnými bitovými kopiemi, místní integrace virtuální sítě funguje bez dalších změn. Pokud používáte Web App for Containers, je nutné upravit image Docker, aby bylo možné použít integraci virtuální sítě. V imagi Docker použijte proměnnou prostředí portu jako port naslouchání hlavního webového serveru namísto použití pevně zakódované čísla portu. Proměnná prostředí portu je automaticky nastavena App Service platformou v době spuštění kontejneru. Pokud používáte SSH, musí být démon procesu SSH nakonfigurovaný tak, aby naslouchal na čísle portu určeném proměnnou prostředí SSH_PORT při použití místní integrace virtuální sítě.  V systému Linux není dostupná žádná podpora pro bránu VNet. 

### <a name="service-endpoints"></a>Koncové body služeb

Místní integrace virtuální sítě umožňuje používat koncové body služby.  Pokud chcete pro svou aplikaci používat koncové body služby, použijte místní integraci virtuální sítě pro připojení k vybrané virtuální síti a pak nakonfigurujte koncové body služby v podsíti, kterou jste použili pro integraci. 

### <a name="network-security-groups"></a>Network Security Groups (Skupiny zabezpečení sítě)

Skupiny zabezpečení sítě umožňují blokovat příchozí a odchozí provoz do prostředků ve virtuální síti. Webová aplikace, která používá místní integraci virtuální sítě, může používat [skupinu zabezpečení sítě][VNETnsg] k blokování odchozího provozu do prostředků ve vaší virtuální síti nebo v Internetu. Chcete-li zablokovat provoz na veřejné adresy, je nutné, aby nastavení aplikace WEBSITE_VNET_ROUTE_ALL nastaveno na hodnotu 1. Příchozí pravidla ve NSG se nevztahují na vaši aplikaci, protože integrace virtuální sítě má vliv jenom na odchozí přenosy z vaší aplikace. K řízení příchozího provozu do webové aplikace použijte funkci omezení přístupu. NSG, který se aplikuje na vaši podsíť integrace, bude platit bez ohledu na to, jaké trasy jste v podsíti pro integraci nastavili. Pokud byla WEBSITE_VNET_ROUTE_ALL nastavena na hodnotu 1 a žádné trasy, které mají vliv na provoz veřejných adres v podsíti integrace, budou mít všechny odchozí přenosy i nadále skupin zabezpečení sítě přiřazené k podsíti integrace. Pokud není WEBSITE_VNET_ROUTE_ALL nastavené, skupin zabezpečení sítě se použije jenom na RFC1918 provoz.

### <a name="routes"></a>Trasy

Směrovací tabulky vám umožní směrovat odchozí přenosy z vaší aplikace do libovolného, kdykoli potřebujete. Ve výchozím nastavení budou směrovací tabulky mít vliv pouze na váš cílový provoz RFC1918.  Pokud WEBSITE_VNET_ROUTE_ALL nastavíte na 1, bude to mít vliv na všechna odchozí volání. Trasy, které jsou nastavené ve vaší podsíti Integration, nebudou mít vliv na odpovědi na příchozí žádosti o aplikace. Mezi běžné cíle můžou patřit zařízení firewallu nebo brány. Pokud chcete směrovat veškerý odchozí provoz do místní sítě, můžete k posílání všech odchozích přenosů do brány ExpressRoute použít směrovací tabulku. Pokud provedete směrování provozu do brány, nezapomeňte nastavit trasy v externí síti pro posílání odpovědí zpět.

Trasy protokolu Border Gateway Protocol (BGP) budou mít vliv i na přenosy aplikací. Pokud máte trasy protokolu BGP z nějakého ExpressRoute brány, bude to mít vliv na odchozí přenosy aplikace. Ve výchozím nastavení budou trasy protokolu BGP ovlivnit jenom váš cílový provoz RFC1918. Pokud je WEBSITE_VNET_ROUTE_ALL nastavené na 1, všechny odchozí přenosy můžou být ovlivněné vašimi trasami protokolu BGP. 

### <a name="how-regional-vnet-integration-works"></a>Jak funguje místní integrace virtuální sítě

Aplikace v App Service jsou hostované na rolích pracovního procesu. Cenové tarify Basic a vyšší jsou vyhrazené plány hostování, ve kterých se na stejných pracovních procesech neběží žádné jiné zákazníky. Regionální integrace virtuální sítě funguje připojením virtuálních rozhraní k adresám v delegované podsíti. Vzhledem k tomu, že adresa z je ve vaší virtuální síti, může mít přístup k většině věcí ve vaší virtuální síti, stejně jako virtuální počítač ve vaší virtuální síti. Implementace sítě se liší od spuštění virtuálního počítače ve virtuální síti, což znamená, proč některé síťové funkce nejsou při používání této funkce ještě dostupné.

![Jak funguje místní integrace virtuální sítě][5]

Pokud je povolená místní integrace virtuální sítě, bude vaše aplikace pořád provádět odchozí volání na Internet přes stejné kanály jako normální. Odchozí adresy, které jsou uvedené na portálu vlastností aplikace, jsou pořád adresy používané vaší aplikací. K jakým změnám vaší aplikace patří volání služeb zabezpečeného koncového bodu služby nebo adres RFC 1918 do vaší virtuální sítě. Pokud je WEBSITE_VNET_ROUTE_ALL nastavené na hodnotu 1, pak se do vaší virtuální sítě dá odeslat veškerý odchozí provoz. 

Funkce podporuje jenom jedno virtuální rozhraní na pracovní proces.  Jedno virtuální rozhraní na pracovní proces znamená jednu místní integraci virtuální sítě podle plánu App Service. Všechny aplikace ve stejném plánu App Service můžou používat stejnou integraci virtuální sítě, ale pokud potřebujete aplikaci, aby se připojila k další virtuální síti, budete muset vytvořit jiný plán App Service. Použité virtuální rozhraní není prostředkem, ke kterému mají zákazníci přímý přístup.

Vzhledem k povaze toho, jak tato technologie funguje, se nezobrazuje přenos, který se používá pro integraci virtuální sítě, v Network Watcher ani v protokolech toku NSG.  

## <a name="gateway-required-vnet-integration"></a>požadovaná brána Integration VNet

Požadovaná brána VNet Integration VNet podporuje připojení k virtuální síti v jiné oblasti nebo k klasické virtuální síti. Požadovaná brána Integration VNet: 

* Umožňuje aplikaci připojit se v jednu chvíli k jenom 1 síti VNet.
* Umožňuje integraci až pěti virtuální sítě do plánu App Service. 
* Umožňuje, aby se stejná síť VNet používala ve více aplikacích v plánu App Service, aniž by to mělo vliv na celkový počet, který může App Service plán použít.  Pokud máte šest aplikací využívajících stejnou virtuální síť ve stejném plánu App Service, počítá se jako 1 virtuální síť. 
* V důsledku smlouvy SLA pro bránu podporuje smlouvu SLA o úrovni 99,9%.
* Umožňuje aplikacím používat službu DNS, se kterou je nakonfigurovaná virtuální síť.
* Aby bylo možné připojit k aplikaci, je nutné, aby byla nakonfigurovaná Virtual Network brána založená na trasách s protokolem SSTP typu Point-to-Site VPN. 

Nemůžete použít bránu, která vyžaduje integraci virtuální sítě:

* S aplikacemi pro Linux
* S virtuální sítí připojenou s ExpressRoute 
* Přístup k zabezpečeným prostředkům koncových bodů služby
* S bránou koexistence, která podporuje ExpressRoute i Point-to-site/site pro sítě VPN

### <a name="set-up-a-gateway-in-your-vnet"></a>Nastavení brány ve virtuální síti ###

Postup vytvoření brány:

1. Vytvořte ve virtuální síti [podsíť brány][creategatewaysubnet] .  

1. [Vytvořte bránu VPN][creategateway]. Vyberte typ sítě VPN založený na trasách.

1. [Nastavte odkaz na adresy webu][setp2saddresses]. Pokud brána není v skladové jednotce Basic, pak musí být IKEV2 zakázaná v umístění Konfigurace lokality a musí být vybraný protokol SSTP. Odkaz na adresní prostor lokality musí být v blocích adres RFC 1918, 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16.

Pokud jenom vytváříte bránu pro použití s App Service integrací virtuální sítě, nemusíte nahrávat certifikát. Vytvoření brány může trvat až 30 minut. Dokud bránu nezřídíte, nebudete moct svoji aplikaci integrovat s vaší virtuální sítí. 

### <a name="how-gateway-required-vnet-integration-works"></a>Jak funguje požadovaná integrace virtuální sítě v bráně

Požadovaná integrace virtuální sítě v bráně je postavená na technologii VPN v lokalitě. Point-to Site VPN omezuje síťový přístup jenom na virtuální počítač, který hostuje aplikaci. Aplikace jsou omezené jenom na přenos přes Internet, prostřednictvím Hybrid Connections nebo prostřednictvím integrace virtuální sítě. Když je vaše aplikace nakonfigurovaná s portálem, aby používala bránu, která vyžaduje integraci virtuální sítě, při vytváření a přiřazování certifikátů v bráně a na straně aplikace se pro vás bude spravovat složité vyjednávání. Konečným výsledkem je, že pracovní procesy používané k hostování vašich aplikací se můžou přímo připojit k bráně virtuální sítě ve vybrané virtuální síti. 

![Jak funguje požadovaná integrace virtuální sítě v bráně][6]

### <a name="accessing-on-premises-resources"></a>Přístup k místním prostředkům

Aplikace mají přístup k místním prostředkům integrací s virtuální sítě, které mají připojení Site-to-site. Pokud používáte bránu, která vyžaduje integraci virtuální sítě, je potřeba aktualizovat trasy místních bran VPN pomocí bloků adres typu Point-to-site. Při prvním nastavení sítě VPN typu Site-to-site musí být skripty používané ke konfiguraci správně nastaveny. Pokud přidáte adresy typu Point-to-site po vytvoření sítě VPN typu Site-to-site, je nutné trasy aktualizovat ručně. Podrobnosti o tom, jak to udělat, se liší podle brány a nejsou popsány zde. Nejde nakonfigurovat protokol BGP s připojením VPN typu Site-to-site.

Není nutná žádná další konfigurace, aby funkce Místní integrace virtuální sítě dosáhla přes vaši síť VNet a místní. Potřebujete jednoduše připojit virtuální síť k místní síti pomocí ExpressRoute nebo VPN typu Site-to-site. 

> [!NOTE]
> Funkce integrace virtuální sítě požadovaná bránou Neintegruje aplikaci s virtuální sítí, která má bránu ExpressRoute. I v případě, že je brána ExpressRoute nakonfigurovaná v [režimu koexistence][VPNERCoex] , integrace virtuální sítě nefunguje. Pokud potřebujete přístup k prostředkům prostřednictvím připojení ExpressRoute, můžete použít funkci regionální integrace virtuální sítě nebo [App Service Environment][ASE], která běží ve vaší virtuální síti. 
> 
> 

### <a name="peering"></a>Partnerské vztahy

Pokud používáte partnerský vztah s místní integrací virtuální sítě, nemusíte provádět žádnou další konfiguraci. 

Pokud používáte bránu, která vyžaduje integraci virtuální sítě s partnerským vztahem, budete muset nakonfigurovat několik dalších položek. Konfigurace partnerského vztahu pro práci s vaší aplikací:

1. Přidejte připojení partnerského vztahu do virtuální sítě, ke které se aplikace připojuje. Při přidávání partnerského připojení povolte povolit **přístup k virtuální síti** a zaškrtněte **Povolit přesměrované přenosy** a **Povolte přenos brány**.
1. Přidejte připojení partnerského vztahu do virtuální sítě, která je v partnerském vztahu k virtuální síti, ke které jste se připojili. Když do cílové virtuální sítě přidáte připojení partnerského vztahu, povolte možnost **Povolit přístup k virtuální síti** a zaškrtněte **Povolit přesměrované přenosy** a **Povolte vzdálené brány**.
1. Na portálu přejdete na App Service plán > síťové rozhraní integrace virtuální sítě >.  Vyberte virtuální síť, ke které se aplikace připojuje. V části směrování přidejte rozsah adres virtuální sítě, která má partnerský vztah s virtuální sítí, ke které je vaše aplikace připojená.  

## <a name="managing-vnet-integration"></a>Správa integrace virtuální sítě 

Připojení a odpojení pomocí virtuální sítě se nachází na úrovni aplikace. Operace, které mohou ovlivnit integraci virtuální sítě napříč více aplikacemi, jsou na úrovni plánu App Service. Ze služby App > Networking > portál Integration Portal můžete získat podrobnosti o vaší virtuální síti. Podobné informace najdete na úrovni ASP na webu ASP > Networking > na portálu Integration VNet.

Jedinou operací, kterou můžete provést v zobrazení aplikace integrace virtuální sítě, je odpojení vaší aplikace od virtuální sítě, ke které je aktuálně připojená. Pokud chcete aplikaci odpojit od virtuální sítě, vyberte **Odpojit**. Když se odpojíte od virtuální sítě, vaše aplikace se restartuje. Odpojením se nemění vaše virtuální síť. Podsíť nebo brána se neodeberou. Pokud budete chtít virtuální síť odstranit, musíte nejdřív odpojit aplikaci od virtuální sítě a odstranit prostředky, jako jsou brány. 

V uživatelském rozhraní integrace virtuální sítě ASP se zobrazí všechny integrace virtuální sítě používané aplikacemi ve vašem ASP. Pokud chcete zobrazit podrobnosti o každé virtuální síti, klikněte na virtuální síť, které vás zajímá. Existují dvě akce, které můžete provést pro bránu, která vyžaduje integraci virtuální sítě.

* **Synchronizovat síť** Operace synchronizace sítě je jenom pro funkci Integrace virtuální sítě závislé na bráně. Při provádění synchronizace sítě se zajistí, že budou synchronizovány i certifikáty a informace o síti. Pokud přidáváte nebo měníte DNS vaší virtuální sítě, musíte provést **synchronizační operaci sítě** . Tato operace restartuje všechny aplikace, které používají tuto virtuální síť.
* **Přidat trasy** Přidání tras bude prosazovat odchozí přenosy do vaší virtuální sítě. 

**Směrování požadované virtuální sítě pro bránu** Trasy, které jsou definované ve vaší virtuální síti, se používají k přímému směrování provozu do vaší virtuální sítě z vaší aplikace. Pokud potřebujete poslat do virtuální sítě další odchozí přenosy, pak sem můžete přidat tyto bloky adres. Tato možnost funguje jenom s potřebnou integrací virtuální sítě VNet s bránou. Směrovací tabulky nemají vliv na provoz vaší aplikace při použití brány požadovaná integrace virtuální sítě způsobem, který se v místní integraci virtuální sítě používá.

**Vyžadované brány certifikáty pro integraci virtuální** sítě Pokud brána vyžaduje integraci virtuální sítě, k zajištění zabezpečení připojení se vyžaduje certifikát Exchange. Společně s certifikáty jsou to konfigurace DNS, trasy a další podobné věci, které popisují síť.
Pokud se změní certifikáty nebo informace o síti, musíte kliknout na synchronizovat síť. Po kliknutí na synchronizovat síť dojde k krátkému výpadku připojení mezi vaší aplikací a vaší virtuální sítí. I když se vaše aplikace nerestartuje, ztráta připojení by mohla způsobit, že vaše lokalita nebude správně fungovat. 

## <a name="pricing-details"></a>Podrobnosti o cenách
Funkce regionální integrace virtuální sítě nemá žádné další poplatky za použití nad rámec cen na cenové úrovni ASP.

Existují tři související poplatky za použití funkce integrace virtuální sítě VNet vyžadované bránou:

* Poplatky za cenové úrovně ASP – vaše aplikace musí být ve schématu Standard, Premium nebo PremiumV2 App Service. Další podrobnosti o těchto nákladech najdete tady: [App Service ceny][ASPricing]. 
* Náklady na přenos dat – odchozí data se účtují i v případě, že virtuální síť je ve stejném datovém centru. Tyto poplatky jsou popsané v [podrobnostech o cenách přenos dat][DataPricing]. 
* VPN Gateway náklady – pro bránu virtuální sítě, která je vyžadována pro síť VPN typu Point-to-site, se účtují náklady. Podrobnosti najdete na stránce s [cenami VPN Gateway][VNETPricing] .

## <a name="troubleshooting"></a>Řešení potíží
I když se tato funkce dá snadno nastavit, neznamená to, že vaše zkušenosti budou bez problémů. Pokud máte problémy s přístupem k požadovanému koncovému bodu, můžete použít některé nástroje, pomocí kterých můžete testovat připojení z konzoly aplikace. Můžete použít dvě konzoly. Jedním z nich je konzola Kudu a druhá je konzola v Azure Portal. Pokud se chcete připojit ke konzole Kudu z vaší aplikace, použijte nástroje-> Kudu. Ke konzole Kudo se můžete dostat i na adrese [název_webu]. SCM. azurewebsites. NET. Po načtení webu přejdete na kartu ladit konzolu. Pokud se chcete dostat do Azure Portal hostované konzoly, pak z aplikace přejdete do konzoly nástroje->. 

#### <a name="tools"></a>Nástroje
Nástroje **příkazového testu**, **nslookup**a **tracert** nefungují prostřednictvím konzoly z důvodu omezení zabezpečení. K vyplnění void se přidaly dva samostatné nástroje. K otestování funkcí DNS jsme přidali nástroj s názvem nameresolver. exe. Syntaxe je:

    nameresolver.exe hostname [optional: DNS Server]

**Nameresolver** můžete použít ke kontrole názvů hostitelů, na kterých vaše aplikace závisí. Tímto způsobem můžete testovat, jestli máte nějaké chybné konfigurace služby DNS, nebo možná nemáte přístup k vašemu serveru DNS. Server DNS, který bude aplikace používat v konzole, si můžete prohlédnout na základě proměnných prostředí WEBSITE_DNS_SERVER a WEBSITE_DNS_ALT_SERVER.

Další nástroj umožňuje testovat připojení TCP k hostiteli a kombinaci portů. Tento nástroj se nazývá **tcpping** a syntaxe je:

    tcpping.exe hostname [optional: port]

Nástroj **tcpping** vám oznamuje, jestli můžete kontaktovat konkrétního hostitele a port. Může se zobrazit jenom v případě, že existuje aplikace, která naslouchá na hostiteli a portu, a má přístup k síti z vaší aplikace na zadaného hostitele a port.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Ladění přístupu k prostředkům hostovaným ve virtuální síti
K dispozici je několik věcí, které můžou zabránit tomu, aby vaše aplikace dosáhla konkrétního hostitele a portu. Většina času představuje jednu ze tří věcí:

* **Brána firewall je v cestě.** Pokud máte bránu firewall způsobem, zadáte časový limit TCP. V tomto případě je časový limit TCP 21 sekund. K otestování připojení použijte nástroj **tcpping** . K vypršení časového limitu TCP může docházet z mnoha věcí za branami firewall, ale na začátku. 
* **Služba DNS není přístupná.** Časový limit DNS je tři sekundy na jeden server DNS. Pokud máte dva servery DNS, časový limit je 6 sekund. Pomocí nameresolver můžete zjistit, jestli služba DNS funguje. Pamatujte, že nemůžete použít nástroj nslookup, protože nepoužívá DNS, se kterým je nakonfigurovaná vaše virtuální síť. Pokud je nepřístupná, můžete mít bránu firewall nebo NSG blokující přístup k DNS nebo může být vypnutá.

Pokud tyto položky neodpovídají na vaše problémy, podívejte se na první věci: 

**Místní integrace virtuální sítě**
* je vaše cílové místo na RFC1918 adrese a nemáte WEBSITE_VNET_ROUTE_ALL nastavené na 1.
* Existuje NSG blokující výstup z podsítě integrace
* Pokud budete předávat napříč ExpressRoute nebo VPN, je místní brána nakonfigurovaná pro směrování provozu zpět do Azure? Pokud máte přístup ke koncovým bodům ve vaší virtuální síti, ale ne místně, Projděte si své trasy.
* máte dostatečná oprávnění k nastavení delegování v podsíti Integration? Během konfigurace integrace místní virtuální sítě bude vaše podsíť integrace delegována na Microsoft. Web. Uživatelské rozhraní integrace virtuální sítě bude podsíť delegovat na Microsoft. Web automaticky. Pokud váš účet nemá dostatečná síťová oprávnění k nastavení delegování, budete potřebovat někoho, kdo může nastavit atributy v podsíti Integration pro delegování podsítě. Pokud chcete integrační podsíť manuálně delegovat, vyhledejte v uživatelském rozhraní podsítě Azure Virtual Network a nastavte delegování pro Microsoft. Web. 

**požadovaná brána Integration VNet**
* je rozsah adres Point-to-site v rozsahu RFC 1918 (10.0.0.0-10.255.255.255/172.16.0.0-172.31.255.255/192.168.0.0-192.168.255.255)?
* Zobrazuje se brána na portálu? Pokud vaše brána nefunguje, přeneste ji do záložního prostředí.
* Zobrazují se certifikáty jako synchronizované nebo se domníváte, že se změnila konfigurace sítě?  Pokud vaše certifikáty nejsou synchronizované nebo máte podezření, že došlo ke změně konfigurace vaší virtuální sítě, která nebyla synchronizovaná s vaší ASP, přejděte na "synchronizovat síť".
* Pokud přecházíte přes síť VPN, je místní brána nakonfigurovaná pro směrování provozu zpět do Azure? Pokud máte přístup ke koncovým bodům ve vaší virtuální síti, ale ne místně, Projděte si své trasy.
* Pokoušíte se použít bránu koexistence, která podporuje obě body na lokalitu a ExpressRoute? Pro integraci virtuální sítě se nepodporují brány koexistence. 

Ladění problémů se sítí je problém, protože nemůžete zjistit, co blokuje přístup ke konkrétnímu hostiteli: kombinace portů. Mezi tyto příčiny patří:

* máte bránu firewall na hostiteli, která brání přístupu k portu aplikace z vašeho bodu do rozsahu IP adres lokality. Překročení podsítí často vyžaduje veřejný přístup.
* váš cílový hostitel je mimo provoz.
* vaše aplikace je mimo provoz.
* Měli jste nesprávnou IP adresu nebo název hostitele.
* vaše aplikace naslouchá na jiném portu, než jste očekávali. ID procesu můžete porovnat s portem naslouchání pomocí příkazu netstat-aon na hostiteli koncového bodu. 
* vaše skupiny zabezpečení sítě jsou nakonfigurovány takovým způsobem, že brání přístupu k hostiteli a portu vaší aplikace z vašeho bodu do rozsahu IP adres lokality

Mějte na paměti, že nevíte, jaká adresa bude vaše aplikace skutečně používat. Může to být jakákoli adresa v podsíti integrace nebo rozsah adres Point-to-site, takže je potřeba umožnit přístup z celého rozsahu adres. 

Mezi další kroky ladění patří:

* Připojte se k VIRTUÁLNÍmu počítači ve virtuální síti a pokuste se připojit k hostiteli prostředků: port. K otestování přístupu TCP použijte příkaz PowerShellu **test-NetConnection**. Syntaxe je:

      test-netconnection hostname [optional: -Port]

* Vyvolejte aplikaci na virtuálním počítači a otestujte přístup k tomuto hostiteli a portu z konzoly z vaší aplikace pomocí **tcpping** .

#### <a name="on-premises-resources"></a>Místní prostředky ####

Pokud se vaše aplikace nemůže připojit k místnímu prostředku, zkontrolujte, jestli se můžete připojit k prostředku z vaší virtuální sítě. K ověření přístupu TCP použijte příkaz prostředí PowerShell **test-NetConnection** . Pokud se váš virtuální počítač nemůže připojit k místnímu prostředku, připojení VPN nebo ExpressRoute možná není správně nakonfigurované.

Pokud se virtuální počítač s hostovaným virtuálním počítačem může dostat k vašemu místnímu systému, ale vaše aplikace nefunguje, může to být způsobeno jedním z následujících důvodů:

* vaše trasy nejsou u vaší podsítě nakonfigurované nebo odkazují na rozsahy adres lokality v místní bráně.
* vaše skupiny zabezpečení sítě blokují přístup k rozsahu IP adres Point-to-site.
* vaše místní brány firewall blokují provoz z rozsahu IP adres Point-to-site.
* Snažíte se spojit s adresou, kterou nenajdete v dokumentu RFC 1918, pomocí funkce Místní integrace virtuální sítě.


## <a name="automation"></a>Automation

Pro místní integraci virtuální sítě je k dispozici podpora rozhraní příkazového řádku. Pokud chcete získat přístup k následujícím příkazům, nainstalujte rozhraní příkazového [řádku Azure][installCLI]. 

        az webapp vnet-integration --help

        Group
            az webapp vnet-integration : Methods that list, add, and remove virtual network integrations
            from a webapp.
                This command group is in preview. It may be changed/removed in a future release.
        Commands:
            add    : Add a regional virtual network integration to a webapp.
            list   : List the virtual network integrations on a webapp.
            remove : Remove a regional virtual network integration from webapp.

        az appservice vnet-integration --help

        Group
            az appservice vnet-integration : A method that lists the virtual network integrations used in an
            appservice plan.
                This command group is in preview. It may be changed/removed in a future release.
        Commands:
            list : List the virtual network integrations used in an appservice plan.

Pro bránu, která vyžaduje integraci virtuální sítě, můžete App Service integrovat s Azure Virtual Network pomocí PowerShellu. Skript připravený ke spuštění najdete v tématu [připojení aplikace v Azure App Service k Azure Virtual Network](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-classic.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-appsetting.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-regionalworks.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-gwworks.png


<!--Links-->
[VNETOverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: https://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ASEintro]: environment/intro.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/
[VNETRouteTables]: https://docs.microsoft.com/azure/virtual-network/manage-route-table/
[installCLI]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest/
