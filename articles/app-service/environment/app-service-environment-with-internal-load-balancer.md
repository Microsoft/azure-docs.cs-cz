---
title: Vytvoření pomocného mechanismu interního nástroje v1
description: Vytvoření a použití pomocného objektu s interního nástroje. Tento dokument je k dispozici pouze pro zákazníky, kteří používají starší pomocného uživatele v1.
author: ccompy
ms.assetid: ad9a1e00-d5e5-413e-be47-e21e5b285dbf
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: c4e5dedf2075a2e13cc91c5eed2c0f03ba498b97
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88962549"
---
# <a name="using-an-internal-load-balancer-with-an-app-service-environment"></a>Použití interního Load Balancer s App Service Environment

> [!NOTE] 
> Tento článek se týká App Service Environment v1. Existuje novější verze App Service Environment, kterou je snazší použít a která je spuštěná na výkonnější infrastruktuře. Další informace o nové verzi začíná [úvodem do App Service Environment](intro.md).
>

Funkce App Service Environment (pomocného mechanismu) je možnost služby Premium v Azure App Service, která poskytuje rozšířenou možnost konfigurace, která není dostupná u razítek s více klienty. Funkce pomocného mechanismu IT v podstatě nasadí Azure App Service ve službě Azure Virtual Network (VNet). Pokud chcete získat větší přehled o funkcích, které nabízí App Service prostředí, přečtěte si článek [co je App Service Environment][WhatisASE] dokumentace. Pokud si nejste jisti výhodami provozu ve virtuální síti, přečtěte si [Nejčastější dotazy k Azure Virtual Network][virtualnetwork]. 

## <a name="overview"></a>Přehled
Pomocí pomocného bodu se dá nasadit s přístupem k Internetu nebo s IP adresou ve vaší virtuální síti. Aby bylo možné nastavit IP adresu na adresu virtuální sítě, musíte nasadit svůj pomocného správce pomocí interního Load Balancer (interního nástroje). Když nakonfigurujete pomocného správce s interního nástroje, zadáte:

* vaše vlastní doména nebo subdoména. V tomto dokumentu se předpokládá, že tento dokument má subdoménu, ale můžete ho nakonfigurovat tak, jak je to možné. 
* certifikát používaný pro protokol HTTPS
* Správa DNS pro subdoménu 

Díky tomu budete moct provádět třeba tyto úkony:

* hostování intranetových aplikací, jako jsou obchodní aplikace, bezpečně v cloudu, ke kterým přistupujete přes lokalitu nebo ExpressRoute VPN
* hostování aplikací v cloudu, které nejsou uvedené na veřejných serverech DNS
* Vytvářejte internetové aplikace s izolovaným back-end, se kterými se můžou vaše klientské aplikace bezpečně integrovat.

#### <a name="disabled-functionality"></a>Zakázané funkce
K dispozici jsou některé věci, které nemůžete použít při použití interního nástroje pomocného programu. Mezi tyto věci patří:

* použití IPSSL
* přiřazují se IP adresy konkrétním aplikacím.
* nákup a použití certifikátu s aplikací prostřednictvím portálu Certifikáty můžete samozřejmě i nadále získávat přímo s certifikační autoritou a používat je s vašimi aplikacemi, ale ne prostřednictvím Azure Portal.

## <a name="creating-an-ilb-ase"></a>Vytvoření pomocného mechanismu pro interního nástroje
Vytvoření pomocného mechanismu pro interního nástroje se neliší od normálního vytváření pomocného mechanismu. Podrobnější diskuzi o vytváření pomocného mechanismu řízení najdete v tématu [jak vytvořit App Service Environment][HowtoCreateASE]. Proces vytvoření interního nástroje pomocného mechanismu je stejný mezi vytvořením virtuální sítě během vytváření pomocného procesu nebo výběrem již existující virtuální sítě. Při vytváření služby ASE s interním nástrojem pro vyrovnávání zatížení postupujte takto: 

1. V Azure Portal vyberte **vytvořit prostředek-> web a mobilní zařízení-> App Service Environment**.
2. Vyberte své předplatné.
3. Vyberte nebo vytvořte skupinu prostředků.
4. Vyberte nebo vytvořte virtuální síť.
5. Pokud vybíráte virtuální síť, vytvořte podsíť.
6. Vyberte **Virtual Network/umístění-> konfigurace virtuální** sítě a nastavte typ VIP na interní.
7. Zadejte název subdomény (Tento název je subdoménou, která se používá pro aplikace vytvořené v tomto mechanismu pro nápovědu).
8. Vyberte **OK** a pak **vytvořit**.

![Zobrazuje obrazovky, které slouží k vytvoření pomocného nástroje interního nástroje.][1]

V podokně Virtual Network existuje možnost konfigurace virtuální sítě, která umožňuje vybrat mezi externí VIP nebo interní VIP. Výchozí hodnota je Externí. Pokud máte nastavenou hodnotu externí, váš přístupový protokol používá internetovou IP adresu s přístupem. Pokud vyberete možnost Interní, vaše služba ASE bude mít nakonfigurovaný interní nástroj pro vyrovnávání zatížení s IP adresou v rámci vaší virtuální sítě. 

Po výběru interního výběru možnosti přidat další IP adresy do pomocného mechanismu služby dojde k odebrání a místo toho musíte zadat subdoménu pomocného mechanismu. V rámci služby pomocného objektu s externí virtuální IP adresou se název pomocného mechanismu používá v subdoméně pro aplikace vytvořené v tomto pomocném formuláři. Pokud má váš správce přihlášený s názvem ***contosotest*** a vaše aplikace v tomto pomocném programu má název ***MyTest***, je poddoménou formát ***contosotest.p.azurewebsites.NET*** a adresa URL této aplikace je ***MyTest.contosotest.p.azurewebsites.NET***. Pokud nastavíte typ VIP na interní, název pomocného mechanismu se v subdoméně pro pomocného mechanismu názvů nepoužije. Subdoménu zadáte explicitně. Pokud je vaše subdoména ***contoso.corp.NET*** a v tomto pomocném objektu s názvem ***timereporting***jste vytvořili aplikaci, adresa URL této aplikace je ***timereporting.contoso.corp.NET***.

## <a name="apps-in-an-ilb-ase"></a>Aplikace v pomocném protokolu interního nástroje
Vytvoření aplikace v pomocném okně interního nástroje je stejné jako vytvoření aplikace v pomocném mechanismu. 

1. V Azure Portal vyberte **vytvořit prostředek-> web a mobilní zařízení-> web** nebo **mobilní** **aplikace nebo aplikaci API**.
2. Zadejte název aplikace.
3. Vyberte své předplatné.
4. Vyberte nebo vytvořte skupinu prostředků.
5. Vyberte nebo vytvořte plán App Service (ASP). Pokud vytváříte nové prostředí ASP, jako umístění vyberte svůj pomocným mechanismem a vyberte fond pracovních procesů, ve kterém chcete vytvořit ASP. Při vytváření ASP vyberete jako umístění a fond pracovních procesů správce přidaných objektů. Když zadáte název aplikace, uvidíte, že subdoména v názvu vaší aplikace je nahrazena subdoménou pro váš přístup k pomocnému programu. 
6. Vyberte **Vytvořit**. Nezapomeňte zaškrtnout políčko **Připnout na řídicí panel** , pokud chcete, aby se aplikace zobrazovala na řídicím panelu. 

![Ukazuje, jak vytvořit aplikaci v interního nástroje pomocném mechanismu pro zobrazení v Azure Portal.][2]

V části název aplikace se název subdomény aktualizuje tak, aby odrážel subdoménu vašeho pomocného programu. 

## <a name="post-ilb-ase-creation-validation"></a>Ověřování vytvořením interního nástroje pomocného mechanismu odeslání
Služba ASE s interním nástrojem pro vyrovnávání zatížení se trochu liší od služby bez interního nástroje pro vyrovnávání zatížení. Jak už jste si poznamenali, musíte si spravovat vlastní DNS a musíte taky zadat vlastní certifikát pro připojení HTTPS. 

Po vytvoření pomocného bodu obnovení si všimněte, že se v subdoméně zobrazuje zadaná subdoména a v nabídce **Nastavení** s názvem **interního nástroje Certificate**je nová položka. Pomocného programu se vytvoří s certifikátem podepsaným svým držitelem, který usnadňuje testování HTTPS. Na portálu se dozvíte, že potřebujete zadat vlastní certifikát pro protokol HTTPS, ale doporučujeme vám, abyste měli certifikát, který se dokončí s vaší subdoménou. 

![Zobrazuje subdoménu, kterou jste zadali při vytváření pomocného mechanismu.][3]

Pokud jednoduše vyzkoušíte něco a nevíte, jak vytvořit certifikát, můžete k vytvoření certifikátu podepsaného svým držitelem použít konzolovou aplikaci IIS MMC. Jakmile je vytvořený, můžete ho exportovat jako soubor. pfx a pak ho nahrát do uživatelského rozhraní certifikátu interního nástroje. Když přistupujete k webu zabezpečenému pomocí certifikátu podepsaného svým držitelem, zobrazí se vám upozornění, že web, ke kterému přistupujete, není zabezpečený z důvodu neschopnosti ověřit certifikát. Pokud se chcete tomuto upozornění vyhnout, budete potřebovat správně podepsaný certifikát, který odpovídá vaší subdoméně a má řetěz důvěryhodnosti, který rozpozná váš prohlížeč.

![Ukazuje, jak použít konzolovou aplikaci IIS MMC k vytvoření certifikátu podepsaného svým držitelem.][6]

Pokud chcete tento tok vyzkoušet s vlastními certifikáty a otestovat přístup HTTP i HTTPS k vašemu přimocnému programu:

1. Přejít na uživatelské rozhraní pomocného uživatelského rozhraní po vytvoření pomocného mechanismu **řízení-> nastavení-> certifikátů interního nástroje**.
2. Nastavte interního nástroje certifikát tak, že vyberete soubor PFX certifikátu a zadáte heslo. Tento krok chvíli trvá a zobrazí se zpráva, že probíhá operace škálování.
3. Získejte interního nástroje adresu pro váš pomocného správce (**Pomocného > vlastností – > virtuální IP adresa**).
4. Po vytvoření vytvořte webovou aplikaci v pomocném mechanismu. 
5. Vytvořte virtuální počítač, pokud ho v této virtuální síti nemáte (ne ve stejné podsíti jako pomocného mechanismu řízení nebo věci).
6. Nastavte DNS pro subdoménu. Můžete použít zástupný znak s vaší subdoménou v DNS nebo pokud chcete provést některé jednoduché testy, upravte soubor hostitelů na VIRTUÁLNÍm počítači a nastavte název webové aplikace na VIP IP adresa. Pokud byl váš správce přihlášený jako název subdomény. ilbase.com a provedli jste webovou aplikaci MyTestApp, aby byla řešena v mytestapp.ilbase.com, nastavte ji v souboru Hosts. (V systému Windows je soubor hostitelů na adrese C:\Windows\System32\drivers\etc\)
7. Použijte na tomto virtuálním počítači prohlížeč a pak na `https://mytestapp.ilbase.com` (nebo na základě názvu vaší webové aplikace ve vaší subdoméně).
8. V prohlížeči na tomto virtuálním počítači přejděte na adresu `https://mytestapp.ilbase.com`. Pokud používáte certifikát podepsaný svým držitelem, musíte přijmout nedostatečné zabezpečení. 

IP adresa pro váš interního nástroje je uvedena ve vašich vlastnostech jako virtuální IP adresa.

![Zobrazuje, že IP adresa pro váš interního nástroje je uvedená ve vašich vlastnostech jako virtuální IP adresa.][4]

## <a name="using-an-ilb-ase"></a>Použití pomocného mechanismu interního nástroje
#### <a name="network-security-groups"></a>Network Security Groups (Skupiny zabezpečení sítě)
INTERNÍHO nástroje pomocného mechanismu umožňuje izolaci sítě pro vaše aplikace. Aplikace nejsou přístupné nebo ani známy v Internetu. Tento přístup je skvělý pro hostování intranetových webů, jako jsou třeba obchodní aplikace. Pokud potřebujete přístup ještě více omezit, můžete k řízení přístupu na úrovni sítě dál používat skupiny zabezpečení sítě (skupin zabezpečení sítě). 

Pokud chcete k dalšímu omezení přístupu použít skupin zabezpečení sítě, musíte se ujistit, že jste nepřerušili komunikaci, kterou vyžaduje pomocný přístup k provozu. I když přístup HTTP/HTTPS je jenom prostřednictvím interního nástroje, který používá přístupový modul pro zápis, pořád závisí na prostředcích mimo virtuální síť. Informace o tom, jaký přístup k síti stále potřebujete, najdete v tématu [řízení příchozího provozu do App Service Environment][ControlInbound] a  [podrobností konfigurace sítě pro App Service prostředí pomocí ExpressRoute][ExpressRoute]. 

Abyste mohli nakonfigurovat skupin zabezpečení sítě, musíte znát IP adresu, kterou Azure používá ke správě vašeho pomocného mechanismu. Tato IP adresa je také odchozí IP adresa z vašeho přimocného mechanismu, pokud vytváří internetové požadavky. Odchozí IP adresa pro pomocného správce sítě zůstane po celou dobu životnosti vašeho pomocného mechanismu. Pokud odstraníte a znovu vytvoříte správce přidaných mechanismů, zobrazí se nová IP adresa. IP adresu najdete tak, že přejdete na **Nastavení-> vlastnosti** a vyhledáte **odchozí IP adresu**. 

![Zobrazuje, kde můžete najít odchozí IP adresu pro váš pomocného správce.][5]

#### <a name="general-ilb-ase-management"></a>Obecná Správa pomocného mechanismu interního nástroje
Správa interního nástroje pomocného mechanismu je převážně stejná jako správa pomocného mechanismu. Je nutné škálovat fondy pracovních procesů, aby bylo možné hostovat více instancí ASP, a škálovat servery front-end tak, aby zpracovávala zvýšené množství přenosů HTTP/HTTPS. Obecné informace o správě konfigurace pomocného mechanismu služby najdete v tématu [konfigurace App Service Environment][ASEConfig]. 

Další položky správy jsou Správa certifikátů a správa DNS. Certifikát, který se používá pro HTTPS po vytvoření pomocného nástroje interního nástroje, musíte získat a odeslat a nahradit ho před jeho vypršením. Vzhledem k tomu, že Azure vlastní základní doménu, může poskytovat certifikáty pro služby ASE s externí virtuální IP adresou. Vzhledem k tomu, že subdoména, kterou používá správce interního nástroje, může být cokoli, musíte zadat vlastní certifikát pro protokol HTTPS. 

#### <a name="dns-configuration"></a>Konfigurace DNS
Při použití externí virtuální IP adresy se DNS spravuje přes Azure. Všechny aplikace vytvořené ve vaší službě ASE se automaticky přidají do Azure DNS, což je veřejná služba DNS. Ve službě ASE s interním nástrojem pro vyrovnávání zatížení musíte spravovat vlastní službu DNS. Pro danou subdoménu, jako je například contoso.corp.net, je nutné vytvořit záznamy DNS A, které odkazují na vaši interního nástroje adresu:

- \*
- *. SCM
- ftp
- publish

## <a name="getting-started"></a>Začínáme
Pokud chcete začít pracovat se App Service prostředími, přečtěte si téma [Úvod do App Service prostředí][WhatisASE] .

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createilbase.png
[2]: ./media/app-service-environment-with-internal-load-balancer/ilbase-createapp.png
[3]: ./media/app-service-environment-with-internal-load-balancer/ilbase-newase.png
[4]: ./media/app-service-environment-with-internal-load-balancer/ilbase-vip.png
[5]: ./media/app-service-environment-with-internal-load-balancer/ilbase-externalvip.png
[6]: ./media/app-service-environment-with-internal-load-balancer/ilbase-ilbcertificate.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[ControlInbound]: app-service-app-service-environment-control-inbound-traffic.md
[virtualnetwork]: ../../virtual-network/virtual-networks-faq.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[vnetnsgs]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[ASEConfig]: app-service-web-configure-an-app-service-environment.md