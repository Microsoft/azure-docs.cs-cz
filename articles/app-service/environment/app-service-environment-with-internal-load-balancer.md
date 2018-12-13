---
title: Vytváření a používání interního nástroje load balancer s App Service environment – Azure | Dokumentace Microsoftu
description: Vytváření a používání služby ASE s ILB
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: ''
ms.assetid: ad9a1e00-d5e5-413e-be47-e21e5b285dbf
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 88f100bc780d8df0202cfcce9b390085a71fc905
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53310466"
---
# <a name="using-an-internal-load-balancer-with-an-app-service-environment"></a>Službě interní služby Load Balancer pomocí služby App Service Environment

> [!NOTE] 
> Tento článek je o App Service Environment v1. Existuje novější verze služby App Service Environment, která se snadněji používá a běží na výkonnější infrastruktuře. Další informace o nové verzi spuštění s [Úvod do služby App Service Environment](intro.md).
>

Funkce App Service Environment (ASE) je možnost Premium služby Azure App Service, která poskytuje rozšířené konfigurace schopností, který není k dispozici v razítka pro více tenantů. Funkce služby ASE v podstatě nasadí Azure App Service ve vaší virtuální sítě Azure. Abyste získali větší představu o funkce, které nabízí služba App Service Environment, přečtěte si [novinky službě App Service Environment] [ WhatisASE] dokumentaci. Pokud neznáte výhody provoz ve virtuální síti čtení [Azure Virtual Network, nejčastější dotazy k][virtualnetwork]. 

## <a name="overview"></a>Přehled
Služba ASE je možné nasadit pomocí Internetu přístupném koncovém bodu nebo s IP adresou ve vaší virtuální síti. Pokud chcete nastavit IP adresu na adresu ve virtuální síti je potřeba nasadit službu ASE s interním Balancer(ILB) zatížení. Pokud vaše služba ASE je nakonfigurovaná s ILB, zadejte:

* vlastní domény nebo subdomény. Abyste usnadnili snadnou, tento dokument předpokládá subdoménu můžete ji ale konfigurovat v obou případech. 
* certifikát používaný pro protokol HTTPS.
* Správa služby DNS pro vaši subdomény. 

Díky tomu budete moct provádět třeba tyto úkony:

* hostování intranetových aplikací, jako jsou – obchodní aplikace, bezpečně v cloudu, ke které přistupujete přes lokalitu k lokalitě nebo virtuální privátní sítě ExpressRoute
* hostování aplikací v cloudu, které nejsou uvedené na veřejných serverech DNS
* Vytvoření internet izolovaný režim back-endu aplikace, které umožňují zabezpečenou integraci vašich front-endu aplikací

#### <a name="disabled-functionality"></a>Zakázané funkce
Existuje několik věcí, které nelze provést, pokud pomocí ILB ASE. Zahrnout tyto věci:

* pomocí IPSSL
* přiřazení IP adres na konkrétní aplikace
* Nákup a používání certifikát s aplikací na portálu. Samozřejmě, stále můžete získat certifikáty přímo s certifikační autority a použijte ho s vašimi aplikacemi, ale ne prostřednictvím webu Azure portal.

## <a name="creating-an-ilb-ase"></a>Vytvoření ILB ASE
Vytvoření ILB ASE není výrazně liší od vytvoření prostředí ASE normálně. Podrobnější diskuzi o vytvoření služby ASE najdete v tématu [vytvoření služby App Service Environment][HowtoCreateASE]. Proces vytvoření ILB ASE je mezi vytvořením virtuální sítě během vytváření služby ASE nebo vyberte existující virtuální sítě. Při vytváření služby ASE s interním nástrojem pro vyrovnávání zatížení postupujte takto: 

1. Na webu Azure Portal, vyberte **vytvořit prostředek -> Web + mobilní zařízení -> Služba App Service Environment**.
2. Vyberte své předplatné.
3. Vyberte nebo vytvořte skupinu prostředků.
4. Vyberte nebo vytvořte virtuální síť.
5. Pokud vyberete virtuální síť vytvořte podsíť.
6. Vyberte **virtuální síť/umístění-> Konfigurace virtuální sítě** a nastavte typ virtuální IP adresy na interní.
7. Zadejte název subdomény (Tento název se subdoménou používat pro aplikace vytvořené v této službě ASE).
8. Vyberte **OK** a potom **vytvořit**.

![][1]

V podokně virtuální sítě je konfigurace virtuální sítě, vyberte možnost, která vám umožní mezi externí IP adresu nebo interní virtuální IP ADRESE. Výchozí hodnota je externí. Pokud máte nastavené na externí služby ASE používá k Internetu virtuální IP adresu. Pokud vyberete interní, vaše služba ASE je nakonfigurovaný s ILB na IP adresu v rámci vaší virtuální sítě. 

Po výběru interní odebrána možnost přidávat do služby ASE další IP adresy a místo toho je nutné zadat subdoménu služby ASE. Ve službě ASE s externí virtuální IP Adresou se název služby ASE používá v subdoménu pro aplikace vytvořené v této službě ASE. Pokud je název vaší služby ASE ***contosotest*** a je název vaší aplikace v této službě ASE ***test***, subdoménu je ve formátu ***contosotest.p.azurewebsites.net*** a adresa URL pro tuto aplikaci je ***mytest.contosotest.p.azurewebsites.net***. Pokud nastavíte typ virtuální IP adresy na interní, název vaší služby ASE se nepoužívá v subdoménu pro danou službu ASE. Zadejte subdoménu explicitně. Pokud je vaše subdoménu ***contoso.corp.net*** a aplikace v tom, že služba ASE s názvem ***timereporting***, adresa URL pro tuto aplikaci je ***timereporting.contoso.corp.net***.

## <a name="apps-in-an-ilb-ase"></a>Aplikace v prostředí ILB ASE
Vytvoření aplikace ve službě ASE s ILB je stejné jako vytvoření aplikace ve službě ASE normálně. 

1. Na webu Azure Portal, vyberte **vytvořit prostředek -> Web + mobilní zařízení -> Web** nebo **Mobile** nebo **aplikace API**.
2. Zadejte název aplikace.
3. Vyberte své předplatné.
4. Vyberte nebo vytvořte skupinu prostředků.
5. Vyberte nebo vytvořte Plan(ASP) App Service. Pokud vytváříte novou ASP, vyberte jako umístění svoji službu ASE a vyberte fond pracovních procesů, který chcete, aby vaše ASP vytvořit v. Když vytvoříte ASP, vyberte svoji službu ASE jako umístění a fond pracovních procesů. Když zadáte název aplikace, uvidíte, že subdoménu pod názvem vašeho aplikace nahrazuje subdoménu pro vaši službu ASE. 
6. Vyberte **Vytvořit**. Je nutné vybrat **připnout na řídicí panel** zaškrtávací políčko, pokud chcete, aby aplikace zobrazovala na řídicím panelu. 

![][2]

Pod názvem aplikace aktualizuje tak, aby odrážely subdoménu vaše služba ASE název subdomény. 

## <a name="post-ilb-ase-creation-validation"></a>Ověření vytvoření ILB ASE příspěvku
Služba ASE s interním nástrojem pro vyrovnávání zatížení se trochu liší od služby bez interního nástroje pro vyrovnávání zatížení. Které jsou už jste si poznamenali je potřeba spravovat vlastní službu DNS a také je nutné zadat vlastní certifikát pro připojení prostřednictvím protokolu HTTPS. 

Po vytvoření služby ASE, všimnete si, že subdoménu ukazuje subdoménu zadaná a je nová položka v **nastavení** nabídky s názvem **ILB certifikát**. Služba ASE se vytvoří s certifikátem podepsaným svým držitelem, který usnadňuje testování protokolu HTTPS. Portálu zjistíte, které budete muset poskytnout vlastní certifikát pro protokol HTTPS, ale to se vám doporučujeme mít certifikát, který doprovází vaše subdomény. 

![][3]

Pokud jsou jednoduše vyzkoušet věci a nevíte, jak vytvořit certifikát, můžete použít konzolovou aplikaci konzoly MMC služby IIS vytvořit certifikát podepsaný svým držitelem. Jakmile ho vytvoříte, můžete exportovat jako soubor .pfx a pak ho nahrajte do uživatelské rozhraní konektoru Certificate ILB. Při přístupu na web zabezpečený pomocí certifikátu podepsaného svým držitelem, prohlížeči vám upozornění, že není zabezpečený lokalizovat pro ověření certifikátu webu, ke kterému přistupujete. Pokud chcete se vyhnout této upozornění, musíte správně podepsaný certifikát, který odpovídá vaší subdoménu a má řetěz certifikátů, který je rozpoznán prohlížeče.

![][6]

Pokud chcete akci toku s vlastní certifikáty a otestovat přístup protokolu HTTP a HTTPS do služby ASE:

1. Přejděte na uživatelské rozhraní služby ASE po vytvoření služby ASE **ASE -> Nastavení -> certifikáty ILB**.
2. Nastavení certifikátu ILB tak, že vyberete soubor pfx certifikátu a zadejte heslo. Tento krok vezme za malou chvíli ke zpracování a zobrazí se zpráva, která probíhá škálování operace.
3. Získat adresu ILB vaší služby ase (**ASE -> Vlastnosti -> virtuální IP adresu**).
4. Vytvoření webové aplikace ve službě ASE po jeho vytvoření. 
5. Vytvoření virtuálního počítače, pokud nemáte v dané virtuální síti (není ve stejné podsíti jako službu ASE nebo věci konec).
6. Nastavení DNS pro vaši subdomény. Můžete použít zástupný znak vaše subdoménu ve službě DNS nebo pokud chcete provést několik jednoduchých testů, upravte soubor hostitelů na vašem virtuálním počítači nastavit název webové aplikace na virtuální IP adresu. Pokud vaše služba ASE název subdomény. ilbase.com a tak, že by se řešit v mytestapp.ilbase.com, provedli mytestapp webové aplikace, které nastavíte v souboru hostitelů. (Na Windows, je soubor hostitelů v C:\Windows\System32\drivers\etc\)
7. Na tomto virtuálním počítači prohlížeč a přejděte na https://mytestapp.ilbase.com (nebo cokoli, co je název vaší webové aplikace s vaší subdoménu).
8. V prohlížeči na tomto virtuálním počítači přejděte na adresu https://mytestapp.ilbase.com. Pokud používáte certifikát podepsaný svým držitelem, musíte přijmout chybějící zabezpečení. 

IP adresa pro vaše ILB je uveden ve vlastnostech jako virtuální IP adresu.

![][4]

## <a name="using-an-ilb-ase"></a>Používá službu App Service Environment s interním nástrojem pro vyrovnávání zatížení.
#### <a name="network-security-groups"></a>Network Security Groups (Skupiny zabezpečení sítě)
Službu ASE umožňuje izolaci sítě pro vaše aplikace. Aplikace nejsou přístupné nebo dokonce známé pomocí Internetu. Tento přístup je ideální pro hostování intranetových serverů, jako jsou třeba – obchodní aplikace. Pokud potřebujete omezit přístup ještě více, pořád můžete Groups(NSGs) zabezpečení sítě pro řízení přístupu na úrovni sítě. 

Pokud chcete pro další omezení přístupu pomocí skupin zabezpečení sítě, musí se ujistěte, že nedojde k narušení komunikaci, která služby ASE potřebuje, aby provoz. I když přístup HTTP/HTTPS je pouze prostřednictvím ILB ASE používá, služby ASE se stále závisí na prostředky mimo virtuální síť. Je nutné použít co přístup k síti najdete v tématu [řízení příchozího provozu do služby App Service Environment] [ ControlInbound] a [podrobnosti o konfiguraci sítě pro App Service Environment používající ExpressRoute][ExpressRoute]. 

Ke konfiguraci vaší skupiny zabezpečení sítě, musíte znát IP adresu, která se používá Azure ke správě vaší služby ASE. Tuto IP adresu je také odchozí IP adresu z vaší služby ASE, pokud to dává požadavky na Internetu. Odchozí IP adresa pro vaše služba ASE zůstane statické po celou dobu životnosti vaší služby ASE. Pokud odstraníte a znovu vytvořit službu ASE, získáte novou IP adresu. Pokud chcete zjistit IP adresu, přejděte na **Nastavení -> vlastnosti** a najít **odchozí IP adresa**. 

![][5]

#### <a name="general-ilb-ase-management"></a>Služba ASE s ILB obecné správy
Správa prostředí ILB ASE je z velké části stejný jako obvykle Správa služby ASE. Nutné navýšit vaše fondy pracovních procesů pro hostování více instancí ASP a vertikálně navýšit kapacitu front-endové servery pro zpracování zvýšené množství přenosy HTTP/HTTPS. Obecné informace o správě konfigurace služby ASE najdete v tématu [konfigurace služby App Service Environment][ASEConfig]. 

Další Správa položek se správy certifikátů a Správa služby DNS. Je nutné získat a nahrát certifikát používaný pro protokol HTTPS. Po vytvoření ILB ASE a nahraďte ho, než vyprší její platnost. Vzhledem k tomu, že Azure je vlastníkem základní doméně, může poskytovat certifikáty pro služby ase s externí virtuální IP Adresou. Protože subdomény používané službu ASE může být cokoli, je nutné zadat vlastní certifikát pro protokol HTTPS. 

#### <a name="dns-configuration"></a>Konfigurace služby DNS
Pokud používáte externí virtuální IP Adresou, DNS spravuje Azure. Všechny aplikace vytvořené ve vaší službě ASE se automaticky přidají do Azure DNS, což je veřejná služba DNS. Ve službě ASE s interním nástrojem pro vyrovnávání zatížení musíte spravovat vlastní službu DNS. Pro daný subdomény, jako je například contoso.corp.net musíte vytvořit DNS A záznamy, které odkazují na vaši adresu ILB pro:

    * 
    publikování *.SCM ftp 


## <a name="getting-started"></a>Začínáme
Začínáme s App Service Environment najdete v článku [Úvod do služby App Service Environment][WhatisASE]

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
