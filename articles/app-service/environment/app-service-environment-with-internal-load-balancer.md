---
title: Vytvoření služby ASE služby ILB v1
description: Vytváření a používání služby ASE s ILB. Tento dokument je k dispozici pouze pro zákazníky, kteří používají starší verze v1 ASE.
author: ccompy
ms.assetid: ad9a1e00-d5e5-413e-be47-e21e5b285dbf
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 0c03905017629e28e41cce2adaa65eac347b8185
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294719"
---
# <a name="using-an-internal-load-balancer-with-an-app-service-environment"></a>Použití interního vyrovnávání zatížení s prostředím služby App Service

> [!NOTE] 
> Tento článek je o prostředí služby App Service v1. K dispozici je novější verze prostředí služby App Service, která se snadněji používá a běží na výkonnější infrastruktuře. Další informace o nové verzi začněte [s úvodem do prostředí služby App Service](intro.md).
>

Funkce Prostředí služby App Service (ASE) je možnost služby Premium služby Azure App Service, která poskytuje rozšířené možnosti konfigurace, která není k dispozici ve víceklientských razítkách. Funkce služby ASE v podstatě nasazuje službu Azure App Service ve vaší virtuální síti Azure(virtuální síti). Chcete-li získat větší přehled o možnostech nabízených prostředí služby App Service, přečtěte si dokumentaci [What is a App Service Environment.][WhatisASE] Pokud nevíte, jaké výhody provozu ve virtuální síti načtete nejčastější dotazy k [virtuální síti Azure][virtualnetwork]. 

## <a name="overview"></a>Přehled
ASE lze nasadit s koncovým bodem přístupným pro internet nebo s IP adresou ve vaší virtuální síti. Chcete-li nastavit IP adresu na adresu virtuální sítě, musíte nasadit službu ASE pomocí interního vyrovnávání zatížení (ILB). Když je vaše služba ASE nakonfigurována pomocí služby ILB, poskytnete:

* vlastní doménu nebo subdoménu. Aby to bylo snadné, tento dokument předpokládá subdoménu, ale můžete ji nakonfigurovat oběma směry. 
* certifikát použitý pro protokol HTTPS
* Správa DNS pro vaši subdoménu. 

Díky tomu budete moct provádět třeba tyto úkony:

* hostujte intranetové aplikace, jako jsou obchodní aplikace, bezpečně v cloudu, ke kterému přistupujete prostřednictvím webu na web nebo ExpressRoute VPN
* hostitelské aplikace v cloudu, které nejsou uvedeny na veřejných dns serverech
* vytvářet izolované back-endové aplikace pro internet, se kterými se vaše aplikace front-endu mohou bezpečně integrovat

#### <a name="disabled-functionality"></a>Zakázané funkce
Existují některé věci, které nelze provést při použití služby ASE ILB. Mezi tyto věci patří:

* pomocí IPSSL
* přiřazení IP adres konkrétním aplikacím
* nákupu a používání certifikátu s aplikací prostřednictvím portálu. Certifikáty můžete samozřejmě stále získat přímo u certifikační autority a používat je s aplikacemi, ale ne prostřednictvím portálu Azure.

## <a name="creating-an-ilb-ase"></a>Vytvoření služby ASE služby ILB
Vytvoření služby ASE ilb se příliš neliší od normálního vytvoření služby ASE. Hlubší diskusi o vytvoření služby ASE najdete v [tématu Jak vytvořit prostředí služby App Service][HowtoCreateASE]. Proces vytvoření služby ASE ILB je stejný mezi vytvořením virtuální sítě během vytváření služby ASE nebo výběrem již existující virtuální sítě. Při vytváření služby ASE s interním nástrojem pro vyrovnávání zatížení postupujte takto: 

1. Na webu Azure Portal vyberte **Vytvořit prostředek -> Web + Mobile -> App Service Environment**.
2. Vyberte své předplatné.
3. Vyberte nebo vytvořte skupinu prostředků.
4. Vyberte nebo vytvořte virtuální síť.
5. Pokud vyberete virtuální síť, vytvořte podsíť.
6. Vyberte **Virtuální síť/Umístění -> konfigurace virtuální sítě** a nastavte typ VIP na interní.
7. Zadejte název subdomény (tento název je subdoménou používanou pro aplikace vytvořené v této službě ASE).
8. Vyberte **OK** a potom **vytvořit**.

![][1]

V podokně Virtuální síť je možnost Konfigurace virtuální sítě, která umožňuje vybrat mezi externí VIP nebo interní VIP. Výchozí hodnota je Externí. Pokud máte nastavena na externí, vaše ASE používá internet přístupné VIP. Pokud vyberete možnost Interní, vaše služba ASE bude mít nakonfigurovaný interní nástroj pro vyrovnávání zatížení s IP adresou v rámci vaší virtuální sítě. 

Po výběru interní, možnost přidat další IP adresy do služby ASE je odebrána a místo toho je nutné zadat subdoménu služby ASE. Ve službě ASE s externí vip název služby ASE se používá v subdoméně pro aplikace vytvořené v této službě ASE. Pokud je vaše služba ASE pojmenována ***contosotest*** a vaše aplikace v této ase se nazývá ***mytest***, subdoména je formátu ***contosotest.p.azurewebsites.net*** a adresa URL pro tuto aplikaci je ***mytest.contosotest.p.azurewebsites.net***. Pokud nastavíte typ VIP na interní, název služby ASE se nepoužívá v subdoméně pro službu ASE. Subdoménu zadáte explicitně. Pokud je vaše subdoména ***contoso.corp.net*** a vy jste vytvořili aplikaci v tomto ***přehledu času služby ASE***, adresa URL této aplikace je ***timereporting.contoso.corp.net***.

## <a name="apps-in-an-ilb-ase"></a>Aplikace ve službě ASE ilb
Vytvoření aplikace ve službě ASE ILB je stejné jako normální vytvoření aplikace ve službě ASE. 

1. Na webu Azure Portal vyberte **Vytvořit prostředek -> Web + Mobile -> web** nebo **mobilní** nebo **aplikaci API**.
2. Zadejte název aplikace.
3. Vyberte své předplatné.
4. Vyberte nebo vytvořte skupinu prostředků.
5. Vyberte nebo vytvořte plán služby App Service (ASP). Pokud vytváříte novou asp, vyberte ase jako umístění a vyberte fond pracovních pracovníků, ve kterém chcete asp vytvořit. Při vytváření technologie ASP vyberete službu ASE jako umístění a fond pracovních pracovníků. Když zadáte název aplikace, uvidíte, že subdoména pod názvem aplikace je nahrazena subdoménou pro vaši službu ASE. 
6. Vyberte **Vytvořit**. Pokud chcete, aby se aplikace zobrazovala na řídicím panelu, zaškrtněte **políčko Připnout na řídicí panel.** 

![][2]

Pod názvem aplikace se název subdomény aktualizuje tak, aby odrážel subdoménu služby ASE. 

## <a name="post-ilb-ase-creation-validation"></a>Ověření vytvoření služby ASE po ILB
Služba ASE s interním nástrojem pro vyrovnávání zatížení se trochu liší od služby bez interního nástroje pro vyrovnávání zatížení. Jak již bylo uvedeno, musíte spravovat vlastní DNS a musíte také poskytnout vlastní certifikát pro připojení HTTPS. 

Po vytvoření služby ASE zjistíte, že subdoména zobrazuje zadanou subdoménu a v nabídce **Nastavení** je nová položka s názvem **Certifikát ILB**. ASE je vytvořen s certifikátem podepsaným svým držitelem, který usnadňuje testování protokolu HTTPS. Portál vám řekne, že je třeba zadat vlastní certifikát pro protokol HTTPS, ale to je povzbudit, abyste měli certifikát, který se hodí k vaší subdoméně. 

![][3]

Pokud jednoduše zkoušíte věci a nevíte, jak vytvořit certifikát, můžete pomocí konzolové aplikace MMC služby IIS vytvořit certifikát podepsaný svým držitelem. Po vytvoření jej můžete exportovat jako soubor .pfx a potom jej nahrát do uj. Při přístupu k webu zabezpečenému certifikátem podepsaným svým držitelem vás prohlížeč upozorní, že web, ke kterému přistupujete, není zabezpečený z důvodu neschopnosti certifikátu ověřit. Pokud se chcete tomuto upozornění vyhnout, budete potřebovat řádně podepsaný certifikát, který odpovídá vaší subdoméně a má řetězec důvěryhodnosti, který je rozpoznán vaším prohlížečem.

![][6]

Pokud chcete vyzkoušet tok s vlastními certifikáty a otestovat přístup http i HTTPS ke své ase:

1. Po vytvoření služby ASE přejděte na službu ASE **ASE -> Nastavení -> certifikáty ILB**.
2. Nastavte certifikát ILB výběrem souboru pfx certifikátu a zadejte heslo. Tento krok chvíli trvá, než se zpracuje a zobrazí se zpráva, že probíhá operace škálování.
3. Získejte adresu ILB pro službu ASE (**ASE -> Vlastnosti -> virtuální IP adresu**).
4. Vytvořte webovou aplikaci ve službě ASE po vytvoření. 
5. Vytvořte virtuální ho, pokud nemáte v této virtuální síti (Není ve stejné podsíti jako ase nebo věci přerušit).
6. Nastavte DNS pro svou subdoménu. Můžete použít zástupný znak se svou subdoménou ve službě DNS nebo pokud chcete provést některé jednoduché testy, upravit soubor hosts na vašem virtuálním počítači a nastavit název webové aplikace na IP adresu VIP. Pokud vaše služba ASE měla název subdomény .ilbase.com a vytvořili jste webovou aplikaci mytestapp tak, aby byla adresována na mytestapp.ilbase.com, nastavte to v souboru hosts. (V systému Windows je soubor hosts v c:\Windows\System32\drivers\etc\)
7. Použijte prohlížeč na tomto virtuálním `https://mytestapp.ilbase.com` počítači a přejděte na (nebo jakýkoli název webové aplikace s vaší subdoménou).
8. V prohlížeči na tomto virtuálním počítači přejděte na adresu `https://mytestapp.ilbase.com`. Pokud používáte certifikát podepsaný svým držitelem, musíte přijmout nedostatek zabezpečení. 

IP adresa pro ilb je uvedena ve vašich vlastnostech jako virtuální IP adresa.

![][4]

## <a name="using-an-ilb-ase"></a>Použití služby ASE služby ILB
#### <a name="network-security-groups"></a>Network Security Groups (Skupiny zabezpečení sítě)
Služba ASE ILB umožňuje izolaci sítě pro vaše aplikace. Aplikace nejsou přístupné nebo dokonce známé na internetu. Tento přístup je vynikající pro hostování intranetových webů, jako jsou obchodní aplikace. Pokud potřebujete přístup ještě více omezit, můžete stále používat skupiny zabezpečení sítě (skupiny zabezpečení sítě) k řízení přístupu na úrovni sítě. 

Pokud chcete použít nsgs k dalšímu omezení přístupu, musíte se ujistit, že není přerušení komunikace, které vyžaduje služby ASE pro provoz. I když přístup HTTP/HTTPS je pouze prostřednictvím ILB používá služby ASE, ase stále závisí na prostředky mimo virtuální sítě. Informace o tom, jaký přístup k síti je stále vyžadován, [najdete v tématu Řízení příchozího provozu v prostředí služby App Service][ControlInbound] a [podrobnosti o konfiguraci sítě pro prostředí služby App Service s technologií ExpressRoute][ExpressRoute]. 

Chcete-li nakonfigurovat sítě zabezpečení sítě, musíte znát IP adresu, kterou Azure používá ke správě služby ASE. Tato IP adresa je také odchozí IP adresa ze služby ASE, pokud provádí internetové požadavky. Odchozí IP adresa pro vaši ase zůstává statická po celou dobu životnosti ase. Pokud smažete a znovu vytvoříte svou ase, získáte novou IP adresu. Chcete-li najít adresu IP, přejděte na **nastavení -> vlastnosti** a vyhledejte **odchozí adresu IP**. 

![][5]

#### <a name="general-ilb-ase-management"></a>Obecné řízení služby ILB ASE
Správa služby ASE ILB je do značné míry stejná jako normální správa služby ASE. Je nutné vertikálně navýšit kapacitu prostředků z fondů pracovních procesů, abyste mohli hostovat více instancí ASP a vertikálně navýšit kapacitu front-endových serverů, aby bylo zpracovávalo zvýšené množství přenosů HTTP/HTTPS. Obecné informace o správě konfigurace služby ASE naleznete v [tématu Konfigurace prostředí služby App Service][ASEConfig]. 

Dalšípoložky správy jsou správa certifikátů a správa DNS. Po vytvoření služby ASE ILB a nahraďte jej před vypršením jeho platnosti a nahrát certifikát použitý pro protokol HTTPS. Vzhledem k tomu, že Azure vlastní základní doménu, může poskytovat certifikáty pro s externí VIP. Vzhledem k tomu, že subdoména používaná službou ASE ILB může být cokoli, musíte zadat vlastní certifikát pro protokol HTTPS. 

#### <a name="dns-configuration"></a>Konfigurace DNS
Při použití externí VIP, DNS se spravuje Azure. Všechny aplikace vytvořené ve vaší službě ASE se automaticky přidají do Azure DNS, což je veřejná služba DNS. Ve službě ASE s interním nástrojem pro vyrovnávání zatížení musíte spravovat vlastní službu DNS. Pro danou subdoménu, například contoso.corp.net, musíte vytvořit záznamy DNS A, které odkazují na vaši adresu ILB pro:

    * 
    *.scm ftp publikovat 


## <a name="getting-started"></a>Začínáme
Pokud chcete začít s prostředím služby App Service, [přečtěte si informace o úvodu do prostředí služby App Service][WhatisASE]

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
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[vnetnsgs]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[ASEConfig]: app-service-web-configure-an-app-service-environment.md
