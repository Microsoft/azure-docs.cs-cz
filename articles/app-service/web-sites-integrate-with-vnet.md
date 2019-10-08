---
title: Integrace aplikace s využitím Azure Virtual Network-Azure App Service
description: Ukazuje, jak připojit aplikaci v Azure App Service k nové nebo existující službě Azure Virtual Network.
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/21/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: a6d0cba41e694e154da32a878cb4c076aae13e65
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72034723"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrace aplikace s Virtual Network Azure
Tento dokument popisuje funkci Integrace virtuální sítě Azure App Service a jak ji nastavit pomocí aplikací v [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). [Virtuální sítě Azure][VNETOverview] (virtuální sítě) umožňují umístit spoustu vašich prostředků Azure do sítě směrovatelné do jiné sítě.  

Azure App Service má dvě variace. 

1. Víceklientské systémy, které podporují celou škálu cenových plánů s výjimkou izolovaného
2. App Service Environment (pomocného mechanismu), který se nasadí do vaší virtuální sítě a podporuje aplikace izolovaného cenového plánu

Tento dokument přechází ze dvou funkcí integrace virtuální sítě, které je pro použití ve více tenantůch App Service. Pokud je vaše aplikace v [App Service Environment][ASEintro], pak už je ve virtuální síti a nevyžaduje použití funkce integrace virtuální sítě k přístupu k prostředkům ve stejné virtuální síti. Podrobnosti o všech funkcích App Service sítě najdete v článku [funkce App Service sítě](networking-features.md) .

Existují dvě formy funkce integrace virtuální sítě.

1. Jedna verze umožňuje integraci s virtuální sítě ve stejné oblasti. Tato forma funkce vyžaduje podsíť ve virtuální síti ve stejné oblasti. Tato funkce je stále ve verzi Preview, ale podporuje se pro produkční úlohy aplikací pro Windows s některými výstrahami, které jsou uvedené níže.
2. Druhá verze umožňuje integraci s virtuální sítě v jiných oblastech nebo s klasickým virtuální sítě. Tato verze funkce vyžaduje nasazení Virtual Network brány do vaší virtuální sítě. Toto je funkce založená na síti VPN typu Point-to-site, která je podporovaná jenom pro aplikace pro Windows.

Aplikace může v jednom okamžiku používat jenom jednu formu funkce integrace virtuální sítě. Otázka pak, jakou funkci byste měli použít. Pro mnoho věcí můžete použít kteroukoli z nich. Jasné rozdíly v těchto i:

| Problém  | Řešení | 
|----------|----------|
| Chcete se spojit s adresou RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) ve stejné oblasti. | Místní integrace virtuální sítě |
| Chcete se připojit k prostředkům v klasické virtuální síti nebo virtuální síti v jiné oblasti. | požadovaná brána Integration VNet |
| Chcete se dostat ke koncovým bodům RFC 1918 napříč ExpressRoute | Místní integrace virtuální sítě |
| Chcete oslovit prostředky napříč koncovými body služby | Místní integrace virtuální sítě |

Ani jedna z funkcí vám neumožní přístup k adresám, které nejsou RFC 1918, mezi ExpressRoute. K tomu je potřeba pro teď použít pomocného mechanismu.

Použití místní integrace virtuální sítě nepřipojuje vaši virtuální síť k místnímu nasazení nebo konfiguraci koncových bodů služby. To je samostatná konfigurace sítě. Regionální integrace virtuální sítě jednoduše umožňuje, aby vaše aplikace provedla volání prostřednictvím těchto typů připojení.

Bez ohledu na použitou verzi poskytuje integrace virtuální sítě přístup k prostředkům ve vaší virtuální síti, ale neuděluje příchozímu privátnímu přístupu do vaší webové aplikace z virtuální sítě. Přístup k soukromému webu znamená, že aplikace je přístupná jenom z privátní sítě, jako je například z Azure Virtual Network. Integrace virtuální sítě je jenom pro odchozí volání z vaší aplikace do vaší virtuální sítě. 

Funkce integrace virtuální sítě:

* vyžaduje Cenový tarif Standard, Premium nebo PremiumV2. 
* podporuje protokoly TCP a UDP
* funguje s aplikacemi App Service a aplikacemi Function App

Integrace virtuální sítě nepodporuje zahrnutí následujících věcí:

* připojení jednotky
* Integrace služby AD 
* NetBIOS

## <a name="regional-vnet-integration"></a>Místní integrace virtuální sítě 

> [!NOTE]
> Partnerský vztah ještě není k dispozici pro App Service na bázi Linux.
>

Pokud se integrace virtuální sítě používá s virtuální sítě ve stejné oblasti jako vaše aplikace, vyžaduje použití delegované podsítě s minimálně 32 adresami. Podsíť se nedá použít pro cokoli jiného. Odchozí volání vytvořená z vaší aplikace budou provedena z adres v delegované podsíti. Při použití této verze integrace virtuální sítě jsou volání provedena z adres ve vaší virtuální síti. Používání adres ve vaší virtuální síti umožňuje vaší aplikaci:

* Volání zabezpečených služeb koncového bodu služby
* Přístup k prostředkům napříč ExpressRoute připojeními
* Přístup k prostředkům ve virtuální síti, ke které jste připojení
* Přístup k prostředkům napříč partnerskými připojeními, včetně připojení ExpressRoute

Tato funkce je ve verzi Preview, ale podporuje se pro produkční úlohy aplikací pro Windows s těmito omezeními:

* Je možné, že máte přístup pouze k adresám, které jsou v rozsahu RFC 1918. Ty jsou adresy v blocích adres 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16.
* Nemůžete se připojit k prostředkům napříč globálními připojeními partnerských vztahů.
* Nemůžete nastavit trasy přenášené z vaší aplikace do vaší virtuální sítě.
* Tato funkce je dostupná jenom z novějších jednotek škálování App Service, které podporují plány App Service PremiumV2.
* Podsíť integrace se dá použít jenom v jednom plánu App Service.
* Tuto funkci nemůže používat aplikace izolovaného plánu, které jsou ve App Service Environment.
* Tato funkce vyžaduje nepoužitou podsíť, která je/27 s 32 adresou nebo větší ve vaší virtuální síti Správce prostředků.
* Aplikace a virtuální síť musí být ve stejné oblasti.
* Virtuální síť nejde odstranit pomocí integrované aplikace. Nejdřív musíte odebrat integraci. 
* Pro každý App Service plán můžete mít jenom jednu místní integraci virtuální sítě. Víc aplikací ve stejném plánu App Service může používat stejnou virtuální síť. 
* Pokud máte aplikaci, která používá místní integraci virtuální sítě, nemůžete změnit předplatné aplikace ani plán App Service.

Pro každou instanci App Service plánu se používá jedna adresa. Pokud jste aplikaci škálovat na 5 instancí, použijí se 5 adres. Vzhledem k tomu, že velikost podsítě po přiřazení nelze změnit, je nutné použít podsíť, která je dostatečně velká, aby mohla pojmout libovolné škálování vaší aplikace. Doporučená velikost je a/26 s 64 adresami. A/27 s 32 adresami by vyhovovaly instancím Premium App Service Plan 20, pokud jste nezměnili velikost plánu App Service. Při horizontálním navýšení nebo zmenšení plánu App Service budete potřebovat dvakrát několik adres po krátkou dobu. 

Pokud chcete, aby vaše aplikace v jiném App Service naplánovaly přístup k virtuální síti, ke které už aplikace v jiném plánu App Service existuje, musíte vybrat jinou podsíť, než kterou používá již existující integrace virtuální sítě.  

Tato funkce je také ve verzi Preview pro Linux. Pokud chcete použít funkci Integrace virtuální sítě s Správce prostředkůovou virtuální sítí ve stejné oblasti:

1. V portálu přejdete na uživatelské rozhraní sítě. Pokud vaše aplikace může používat novou funkci, zobrazí se vám možnost Přidat virtuální síť (Preview).  

   ![Vybrat integraci virtuální sítě][6]

1. Vyberte **Přidat virtuální síť (Preview)** .  

1. Vyberte virtuální síť Správce prostředků, se kterou chcete integrovat, a pak buď vytvořte novou podsíť, nebo vyberte prázdnou již existující podsíť. Dokončení integrace trvá méně než minutu. Během integrace se aplikace restartuje.  Po dokončení integrace se zobrazí podrobnosti o virtuální síti, do které jste integrováni, a banner v horní části, který vám sdělí, že tato funkce je ve verzi Preview.

   ![Vyberte virtuální síť a podsíť.][7]

Jakmile se vaše aplikace integruje do vaší virtuální sítě, bude používat stejný server DNS, se kterým je nakonfigurovaná vaše virtuální síť. 

Regionální integrace virtuální sítě vyžaduje, aby vaše podsíť integrace byla delegovaná na Microsoft. Web.  Uživatelské rozhraní integrace virtuální sítě bude podsíť delegovat na Microsoft. Web automaticky. Pokud váš účet nemá dostatečná síťová oprávnění k nastavení, budete potřebovat někoho, kdo může nastavit atributy v podsíti Integration pro delegování podsítě. Pokud chcete integrační podsíť manuálně delegovat, vyhledejte v uživatelském rozhraní podsítě Azure Virtual Network a nastavte delegování pro Microsoft. Web.

Pokud chcete aplikaci odpojit od virtuální sítě, vyberte **Odpojit**. Tím dojde k restartování vaší webové aplikace. 


#### <a name="web-app-for-containers"></a>Web App for Containers

Pokud používáte App Service v systému Linux s vestavěnými bitovými kopiemi, funkce Místní integrace virtuální sítě funguje bez dalších změn. Pokud používáte Web App for Containers, je nutné upravit image Docker, aby bylo možné použít integraci virtuální sítě. V imagi Docker použijte proměnnou prostředí portu jako port naslouchání hlavního webového serveru namísto použití pevně zakódované čísla portu. Proměnná prostředí portu je automaticky nastavena App Service platformou v době spuštění kontejneru. Pokud používáte SSH, musí být démon procesu SSH nakonfigurovaný tak, aby naslouchal na čísle portu určeném proměnnou prostředí SSH_PORT při použití místní integrace virtuální sítě.

### <a name="service-endpoints"></a>Koncové body služeb

Nová funkce integrace virtuální sítě umožňuje používat koncové body služby.  Pokud chcete pro vaši aplikaci používat koncové body služby, připojte se k vybrané virtuální síti pomocí nové integrace virtuální sítě a potom nakonfigurujte koncové body služby v podsíti, kterou jste použili pro integraci. 


### <a name="how-vnet-integration-works"></a>Jak funguje integrace virtuální sítě

Aplikace v App Service jsou hostované na rolích pracovního procesu. Cenové tarify Basic a vyšší jsou vyhrazené plány hostování, ve kterých se na stejných pracovních procesech neběží žádné jiné zákazníky. Integrace virtuální sítě funguje připojením virtuálních rozhraní s adresami v delegované podsíti. Vzhledem k tomu, že adresa z je ve vaší virtuální síti, má přístup k většině věcí ve vaší virtuální síti, stejně jako virtuální počítač ve vaší virtuální síti. Implementace sítě se liší od spuštění virtuálního počítače ve virtuální síti, což znamená, proč některé síťové funkce nejsou při používání této funkce ještě dostupné.

![Integrace virtuální sítě](media/web-sites-integrate-with-vnet/vnet-integration.png)

Pokud je povolená integrace virtuální sítě, bude vaše aplikace pořád provádět odchozí volání na Internet přes stejné kanály jako normální. Odchozí adresy, které jsou uvedené na portálu vlastností aplikace, jsou pořád adresy používané vaší aplikací. K jakým změnám vaší aplikace patří volání služeb zabezpečeného koncového bodu služby nebo adres RFC 1918 do vaší virtuální sítě. 

Funkce podporuje jenom jedno virtuální rozhraní na pracovní proces.  Jedno virtuální rozhraní na pracovní proces znamená jednu místní integraci virtuální sítě podle plánu App Service. Všechny aplikace ve stejném plánu App Service můžou používat stejnou integraci virtuální sítě, ale pokud potřebujete aplikaci, aby se připojila k další virtuální síti, budete muset vytvořit jiný plán App Service. Použité virtuální rozhraní není prostředkem, ke kterému mají zákazníci přímý přístup.

Vzhledem k povaze toho, jak tato technologie funguje, se nezobrazuje přenos, který se používá pro integraci virtuální sítě, v Network Watcher ani v protokolech toku NSG.  

## <a name="gateway-required-vnet-integration"></a>požadovaná brána Integration VNet 

Funkce integrace virtuální sítě požadovaná bránou:

* Dá se použít k připojení k virtuální sítě v jakékoli oblasti Správce prostředků nebo klasický virtuální sítě
* Umožňuje aplikaci připojit se v jednu chvíli k jenom 1 síti VNet.
* Umožňuje integraci až pěti virtuální sítě do plánu App Service. 
* Umožňuje, aby se stejná síť VNet používala ve více aplikacích v plánu App Service, aniž by to mělo vliv na celkový počet, který může App Service plán použít.  Pokud máte 6 aplikací používajících stejnou virtuální síť ve stejném plánu App Service, počítá se jako 1 virtuální síť. 
* Vyžaduje bránu Virtual Network, která je nakonfigurovaná s Point-to-Site VPN.
* V důsledku smlouvy SLA pro bránu podporuje smlouvu SLA o úrovni 99,9%.

Tato funkce nepodporuje:
* Použití s aplikacemi pro Linux
* Přístup k prostředkům napříč ExpressRoute 
* Přístup k prostředkům napříč koncovými body služby 

### <a name="getting-started"></a>Začínáme

Tady je několik věcí, které je potřeba před připojením webové aplikace k virtuální síti vzít v úvahu:

* Cílová virtuální síť musí mít povolenou síť VPN typu Point-to-site s bránou založenou na trasách, aby ji bylo možné připojit k aplikaci. 
* Virtuální síť musí být ve stejném předplatném jako vaše App Service plán (ASP).
* Aplikace, které se integrují s virtuální sítí, používají DNS, které jsou zadané pro tuto virtuální síť.

### <a name="set-up-a-gateway-in-your-vnet"></a>Nastavení brány ve virtuální síti ###

Pokud už máte bránu nakonfigurovanou s adresami typu Point-to-site, můžete přejít na konfiguraci integrace virtuální sítě s vaší aplikací.  
Postup vytvoření brány:

1. Vytvořte ve virtuální síti [podsíť brány][creategatewaysubnet] .  

1. [Vytvořte bránu VPN][creategateway]. Vyberte typ sítě VPN založený na trasách.

1. [Nastavte odkaz na adresy webu][setp2saddresses]. Pokud brána není v skladové jednotce Basic, musí být IKEV2 v konfiguraci Point-to-site zakázaná a musí se vybrat protokol SSTP. Adresní prostor musí být v blocích adres RFC 1918, 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16.

Pokud jenom vytváříte bránu pro použití s App Service integrací virtuální sítě, nemusíte nahrávat certifikát. Vytvoření brány může trvat až 30 minut. Dokud bránu nezřídíte, nebudete moct svoji aplikaci integrovat s vaší virtuální sítí. 

### <a name="configure-vnet-integration-with-your-app"></a>Konfigurace integrace virtuální sítě s vaší aplikací 

Povolení integrace virtuální sítě ve vaší aplikaci: 

1. V Azure Portal otevřete nastavení aplikace a vyberte síť > integraci virtuální sítě. Vaše ASP musí být ve standardní SKU nebo lepší používat funkci Integrace virtuální sítě. 
 @no__t 0VNet Integration UI @ no__t-1

1. Vyberte **Přidat virtuální síť**. 
 ![Add VNet Integration @ no__t-1

1. Vyberte svou virtuální síť. 
  @no__t 0Select vaší virtuální sítě @ no__t-1
  
Vaše aplikace se po tomto posledním kroku restartuje.  

### <a name="how-the-gateway-required-vnet-integration-feature-works"></a>Jak funguje funkce pro integraci virtuální sítě pro bránu

Funkce pro integraci virtuální sítě typu brána je postavená na technologii VPN typu Point-to-site. Technologie Point-to-site omezuje síťový přístup jenom na virtuální počítač, který hostuje aplikaci. Aplikace jsou omezené jenom na přenos přes Internet, prostřednictvím Hybrid Connections nebo prostřednictvím integrace virtuální sítě. 

![Jak funguje integrace virtuální sítě][3]

## <a name="managing-vnet-integration"></a>Správa integrace virtuální sítě
Možnost připojení a odpojení k virtuální síti se nachází na úrovni aplikace. Operace, které mohou ovlivnit integraci virtuální sítě napříč více aplikacemi, jsou na úrovni plánu App Service. Ze služby App > Networking > portál Integration Portal můžete získat podrobnosti o vaší virtuální síti. Podobné informace najdete na úrovni ASP na webu ASP > Networking > portál VNet Integration Portal, včetně toho, jaké aplikace v tomto App Service plánu používají danou integraci.

 ![Podrobnosti virtuální sítě][4]

Informace, které máte k dispozici v uživatelském rozhraní integrace virtuální sítě, jsou stejné mezi portálem aplikace a ASP.

* Název virtuální sítě – odkazy na uživatelské rozhraní virtuální sítě
* Location – odráží umístění vaší virtuální sítě. Integrace s virtuální sítí v jiném umístění může u vaší aplikace způsobovat problémy latence. 
* Stav certifikátu – odráží, jestli jsou vaše certifikáty synchronizované mezi plánem App Service a vaší virtuální sítí.
* Stav brány – chcete-li použít bránu, která vyžaduje integraci virtuální sítě, můžete zobrazit stav brány.
* Adresní prostor virtuální sítě – zobrazuje adresní prostor IP adres vaší virtuální sítě. 
* Adresní prostor Point-to-site – zobrazuje odkaz na adresní prostor IP adres lokality pro virtuální síť. Při volání do vaší virtuální sítě při použití funkce vyžadované bránou bude vaše aplikace z adresy jedna z těchto adres. 
* Adresní prostor typu Site-to-site – připojení VPN typu Site-to-site můžete použít k propojení vaší virtuální sítě s místními prostředky nebo k jiné virtuální síti. Rozsahy IP adres definované s tímto připojením k síti VPN se zobrazí zde.
* DNS servery – zobrazí servery DNS nakonfigurované ve vaší virtuální síti.
* IP adresy směrované na virtuální síť – zobrazuje bloky adres směrované za použití k řízení provozu do vaší virtuální sítě. 

Jedinou operací, kterou můžete provést v zobrazení aplikace integrace virtuální sítě, je odpojení vaší aplikace od virtuální sítě, ke které je aktuálně připojená. Pokud chcete aplikaci odpojit od virtuální sítě, vyberte **Odpojit**. Když se odpojíte od virtuální sítě, vaše aplikace se restartuje. Odpojením se nemění vaše virtuální síť. Podsíť nebo brána se neodeberou. Pokud budete chtít virtuální síť odstranit, musíte nejdřív odpojit aplikaci od virtuální sítě a odstranit prostředky, jako jsou brány. 

Pokud chcete získat přístup k uživatelskému rozhraní integrace virtuální sítě ASP, otevřete uživatelské rozhraní ASP a vyberte **sítě**.  V části Integrace virtuální sítě vyberte **kliknutím sem pro konfiguraci** otevřete uživatelské rozhraní stavu síťové funkce.

![Informace o integraci virtuální sítě ASP][5]

V uživatelském rozhraní integrace virtuální sítě ASP se zobrazí všechny virtuální sítě používané aplikacemi ve vašem ASP. Pokud chcete zobrazit podrobnosti o každé virtuální síti, klikněte na virtuální síť, které vás zajímá. Tady můžete udělat dvě akce.

* **Synchronizovat síť** Operace synchronizace sítě je jenom pro funkci Integrace virtuální sítě závislé na bráně. Při provádění synchronizace sítě se zajistí, že budou synchronizovány i certifikáty a informace o síti. Pokud přidáváte nebo měníte DNS vaší virtuální sítě, musíte provést **synchronizační operaci sítě** . Tato operace restartuje všechny aplikace, které používají tuto virtuální síť.
* **Přidat trasy** Přidání tras bude prosazovat odchozí přenosy do vaší virtuální sítě.

**Směrování** Trasy, které jsou definované ve vaší virtuální síti, se používají k přímému směrování provozu do vaší virtuální sítě z vaší aplikace. Pokud potřebujete poslat do virtuální sítě další odchozí přenosy, pak sem můžete přidat tyto bloky adres. Tato možnost funguje jenom s potřebnou integrací virtuální sítě VNet s bránou.

**Certifikáty** Pokud brána vyžaduje integraci virtuální sítě, k zajištění zabezpečení připojení se vyžaduje certifikát Exchange. Společně s certifikáty jsou to konfigurace DNS, trasy a další podobné věci, které popisují síť.
Pokud se změní certifikáty nebo informace o síti, musíte kliknout na synchronizovat síť. Po kliknutí na synchronizovat síť dojde k krátkému výpadku připojení mezi vaší aplikací a vaší virtuální sítí. I když se vaše aplikace nerestartuje, ztráta připojení by mohla způsobit, že vaše lokalita nebude správně fungovat. 

## <a name="accessing-on-premises-resources"></a>Přístup k místním prostředkům
Aplikace mají přístup k místním prostředkům integrací s virtuální sítě, které mají připojení Site-to-site. Pokud používáte bránu, která vyžaduje integraci virtuální sítě, je potřeba aktualizovat trasy místních bran VPN pomocí bloků adres typu Point-to-site. Při prvním nastavení sítě VPN typu Site-to-site musí být skripty používané ke konfiguraci správně nastaveny. Pokud přidáte adresy typu Point-to-site po vytvoření sítě VPN typu Site-to-site, je nutné trasy aktualizovat ručně. Podrobnosti o tom, jak to udělat, se liší podle brány a nejsou popsány zde. Nejde nakonfigurovat protokol BGP s připojením VPN typu Site-to-site.

Není nutná žádná další konfigurace, aby funkce Místní integrace virtuální sítě dosáhla přes vaši síť VNet a místní. Potřebujete jednoduše připojit virtuální síť k místní síti pomocí ExpressRoute nebo VPN typu Site-to-site. 

> [!NOTE]
> Funkce integrace virtuální sítě požadovaná bránou Neintegruje aplikaci s virtuální sítí, která má bránu ExpressRoute. I v případě, že je brána ExpressRoute nakonfigurovaná v [režimu koexistence][VPNERCoex] , integrace virtuální sítě nefunguje. Pokud potřebujete přístup k prostředkům prostřednictvím připojení ExpressRoute, můžete použít funkci regionální integrace virtuální sítě nebo [App Service Environment][ASE], která běží ve vaší virtuální síti. 
> 
> 

## <a name="peering"></a>Partnerské vztahy
Pokud používáte partnerský vztah s místní integrací virtuální sítě, nemusíte provádět žádnou další konfiguraci. 

Pokud používáte bránu, která vyžaduje integraci virtuální sítě s partnerským vztahem, budete muset nakonfigurovat několik dalších položek. Konfigurace partnerského vztahu pro práci s vaší aplikací:

1. Přidejte připojení partnerského vztahu do virtuální sítě, ke které se aplikace připojuje. Při přidávání partnerského připojení povolte povolit **přístup k virtuální síti** a zaškrtněte **Povolit přesměrované přenosy** a **Povolte přenos brány**.
1. Přidejte připojení partnerského vztahu do virtuální sítě, která je v partnerském vztahu k virtuální síti, ke které jste se připojili. Když do cílové virtuální sítě přidáte připojení partnerského vztahu, povolte možnost **Povolit přístup k virtuální síti** a zaškrtněte **Povolit přesměrované přenosy** a **Povolte vzdálené brány**.
1. Na portálu přejdete na App Service plán > síťové rozhraní integrace virtuální sítě >.  Vyberte virtuální síť, ke které se aplikace připojuje. V části směrování přidejte rozsah adres virtuální sítě, která má partnerský vztah s virtuální sítí, ke které je vaše aplikace připojená.  


## <a name="pricing-details"></a>Podrobnosti o cenách
Funkce regionální integrace virtuální sítě nemá žádné další poplatky za použití nad rámec cen na cenové úrovni ASP.

Existují tři související poplatky za použití funkce integrace virtuální sítě VNet vyžadované bránou:

* Poplatky za cenové úrovně ASP – vaše aplikace musí být ve schématu Standard, Premium nebo PremiumV2 App Service. Další podrobnosti o těchto nákladech najdete tady: [App Service ceny][ASPricing]. 
* Náklady na přenos dat – odchozí data se účtují i v případě, že virtuální síť je ve stejném datovém centru. Tyto poplatky jsou popsané v [podrobnostech o cenách přenos dat][DataPricing]. 
* VPN Gateway náklady – pro bránu virtuální sítě, která je vyžadována pro síť VPN typu Point-to-site, se účtují náklady. Podrobnosti najdete na stránce s [cenami VPN Gateway][VNETPricing] .


## <a name="troubleshooting"></a>Řešení potíží
I když se tato funkce dá snadno nastavit, neznamená to, že vaše zkušenosti budou bez problémů. Pokud máte problémy s přístupem k požadovanému koncovému bodu, můžete použít některé nástroje, pomocí kterých můžete testovat připojení z konzoly aplikace. Můžete použít dvě konzoly. Jedním z nich je konzola Kudu a druhá je konzola v Azure Portal. Pokud se chcete připojit ke konzole Kudu z vaší aplikace, použijte nástroje-> Kudu. Ke konzole Kudo se můžete dostat i na adrese [název_webu]. SCM. azurewebsites. NET. Po načtení webu přejdete na kartu ladit konzolu. Pokud se chcete dostat do Azure Portal hostované konzoly, pak z aplikace přejdete do konzoly nástroje->. 

#### <a name="tools"></a>Nástroje
**Příkazy příkazového testu**a nástroje **nslookup** a **tracert** nebudou prostřednictvím konzoly fungovat z důvodu omezení zabezpečení. K vyplnění void se přidaly dva samostatné nástroje. K otestování funkcí DNS jsme přidali nástroj s názvem nameresolver. exe. Syntaxe je:

    nameresolver.exe hostname [optional: DNS Server]

**Nameresolver** můžete použít ke kontrole názvů hostitelů, na kterých vaše aplikace závisí. Tímto způsobem můžete testovat, jestli máte nějaké chybné konfigurace služby DNS, nebo možná nemáte přístup k vašemu serveru DNS. Server DNS, který bude aplikace používat v konzole, si můžete prohlédnout v části environmentální proměnné WEBSITE_DNS_SERVER a WEBSITE_DNS_ALT_SERVER.

Další nástroj umožňuje testovat připojení TCP k hostiteli a kombinaci portů. Tento nástroj se nazývá **tcpping** a syntaxe je:

    tcpping.exe hostname [optional: port]

Nástroj **tcpping** vám oznamuje, jestli můžete kontaktovat konkrétního hostitele a port. Může se zobrazit jenom v případě, že existuje aplikace, která naslouchá na hostiteli a portu, a má přístup k síti z vaší aplikace na zadaného hostitele a port.

#### <a name="debugging-access-to-vnet-hosted-resources"></a>Ladění přístupu k prostředkům hostovaným ve virtuální síti
K dispozici je několik věcí, které můžou zabránit tomu, aby vaše aplikace dosáhla konkrétního hostitele a portu. Většina času představuje jednu ze tří věcí:

* **Brána firewall je v cestě.** Pokud máte bránu firewall způsobem, zadáte časový limit TCP. V tomto případě je časový limit TCP 21 sekund. K otestování připojení použijte nástroj **tcpping** . K vypršení časového limitu TCP může docházet z mnoha věcí za branami firewall, ale na začátku. 
* **Služba DNS není přístupná.** Časový limit DNS je tři sekundy na jeden server DNS. Pokud máte dva servery DNS, časový limit je 6 sekund. Pomocí nameresolver můžete zjistit, jestli služba DNS funguje. Pamatujte, že nemůžete použít nástroj nslookup, protože nepoužívá DNS, se kterým je nakonfigurovaná vaše virtuální síť. Pokud je nepřístupná, můžete mít bránu firewall nebo NSG blokující přístup k DNS nebo může být vypnutá.

Pokud tyto položky neodpovídají na vaše problémy, podívejte se na první věci: 

**Místní integrace virtuální sítě**
* je vaším cílem adresa RFC 1918.
* Existuje NSG blokující výstup z podsítě integrace
* Pokud budete předávat napříč ExpressRoute nebo VPN, je místní brána nakonfigurovaná pro směrování provozu zpět do Azure? Pokud se můžete dostat ke koncovým bodům ve vaší virtuální síti, ale ne místně, je vhodné kontrolu.

**požadovaná brána Integration VNet**
* je rozsah adres Point-to-site v rozsahu RFC 1918 (10.0.0.0-10.255.255.255/172.16.0.0-172.31.255.255/192.168.0.0-192.168.255.255)?
* Zobrazuje se brána na portálu? Pokud vaše brána nefunguje, přeneste ji do záložního prostředí.
* Zobrazují se certifikáty jako synchronizované nebo se domníváte, že se změnila konfigurace sítě?  Pokud vaše certifikáty nejsou synchronizované nebo máte podezření, že došlo ke změně konfigurace vaší virtuální sítě, která nebyla synchronizovaná s vaší ASP, přejděte na "synchronizovat síť".
* Pokud budete předávat napříč ExpressRoute nebo VPN, je místní brána nakonfigurovaná pro směrování provozu zpět do Azure? Pokud se můžete dostat ke koncovým bodům ve vaší virtuální síti, ale ne místně, je vhodné kontrolu.

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


## <a name="powershell-automation"></a>Automatizace PowerShellu

App Service můžete integrovat s Azure Virtual Network pomocí prostředí PowerShell. Skript připravený ke spuštění najdete v tématu [připojení aplikace v Azure App Service k Azure Virtual Network](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-howitworks.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-details.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-aspdetails.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-newvnet.png
[7]: ./media/web-sites-integrate-with-vnet/vnetint-newvnetdetails.png
[8]: ./media/web-sites-integrate-with-vnet/vnetint-selectvnet.png

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
