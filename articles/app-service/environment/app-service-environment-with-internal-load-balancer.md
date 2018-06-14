---
title: Vytváření a používání interní pro vyrovnávání zatížení s služby App Service Environment | Microsoft Docs
description: Vytvoření a použití s ILB App Service Environment
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
ms.openlocfilehash: f7c94b790c6aa7c75c62fd05671f016b7185b2a2
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2018
ms.locfileid: "29388811"
---
# <a name="using-an-internal-load-balancer-with-an-app-service-environment"></a>Interní nástroj pomocí služby App Service Environment

> [!NOTE] 
> Tento článek je o v1 App Service Environment. Existuje novější verze App Service Environment, který je jednodušší použít a běží na výkonnější infrastruktury. Další informace o nové verzi spuštění s [Úvod do služby App Service Environment](intro.md).
>

Funkci prostředí App Service (App Service Environment) je možnost Premium služby Azure App Service, který doručí funkci rozšířené konfigurace, která není k dispozici v víceklientské razítka. Funkci App Service Environment v podstatě nasadí Azure App Service ve vaší virtuální Network(VNet) Azure. K získání lépe porozumět funkce nabízené číst prostředí App Service [novinky služby App Service Environment] [ WhatisASE] dokumentaci. Pokud nevíte, výhod pracuje v čtení virtuální sítě [Azure virtuální sítě, – nejčastější dotazy][virtualnetwork]. 

## <a name="overview"></a>Přehled
App Service Environment se dá nasadit pomocí Internetu přístupném koncovém bodu nebo IP adresu ve vaší virtuální síti. Chcete-li nastavit adresu IP adresu virtuální sítě je třeba nasadit vaší App Service Environment se vnitřní Balancer(ILB) zatížení. Pokud vaše App Service Environment je konfigurován s ILB, zadejte:

* vaše vlastní doména nebo subdoména. Chcete-li snadno, tento dokument předpokládá subdomény, ale můžete ji nakonfigurovat v obou případech. 
* certifikát použitý pro protokol HTTPS
* Správa služby DNS pro vaše subdomény. 

Díky tomu budete moct provádět třeba tyto úkony:

* aplikace v síti intranet hostitele, jako-obchodní aplikace, bezpečně v cloudu, ve kterém přístup přes lokalitu k lokalitě nebo ExpressRoute VPN
* aplikace pro hostitele v cloudu, které nejsou uvedené v veřejné servery DNS
* Vytvoření internet izolované back-end aplikace, pomocí kterých můžete bezpečně integrovat aplikace front-endu

#### <a name="disabled-functionality"></a>Zakázané funkce
Existují některé věci, které nejde dělat, když pomocí ILB App Service Environment. Zahrnout všechny tyto věci:

* pomocí IPSSL
* přiřazení IP adres na konkrétní aplikace
* nakupovat a certifikát pomocí aplikace prostřednictvím portálu. Vám může, samozřejmě stále získat certifikáty přímo s certifikační autority a použít ho s vaší aplikací, ale ne prostřednictvím portálu Azure.

## <a name="creating-an-ilb-ase"></a>Vytváření ILB App Service Environment
Vytváření ILB App Service Environment není výrazně liší od vytvoření App Service Environment normálně. Podrobnější informace o vytvoření App Service Environment, naleznete v [postup vytvoření služby App Service Environment][HowtoCreateASE]. Proces vytvoření App Service Environment ILB je stejný mezi vytvoření virtuální sítě během vytváření App Service Environment nebo vyberte existující virtuální síť. Při vytváření služby ASE s interním nástrojem pro vyrovnávání zatížení postupujte takto: 

1. Na portálu Azure vyberte **vytvořit prostředek -> Web + mobilní zařízení -> Služba App Service Environment**.
2. Vyberte své předplatné.
3. Vyberte nebo vytvořte skupinu prostředků.
4. Vyberte nebo vytvořte virtuální síť.
5. Pokud vyberete virtuální síť vytvořte podsíť.
6. Vyberte **virtuální sítě nebo umístění-> Konfigurace virtuální sítě** a nastavte typ VIP na interní.
7. Zadejte název subdomény (Tento název je subdoménou používá pro aplikace vytvořené v této App Service Environment).
8. Vyberte **OK** a potom **vytvořit**.

![][1]

V podokně virtuální sítě je možnost, která vám umožní vybrat, jestli chcete externí virtuální IP adresy nebo interní virtuální IP adresy konfiguraci virtuální sítě. Výchozí hodnota je externí. Pokud máte nastavené na externí, používá vaše App Service Environment dostupný VIP Internetu. Pokud vyberete interní, vaše App Service Environment je nakonfigurovaná s ILB na IP adresu ve virtuální síti. 

Po výběru interní, možnost přidávat další IP adresy k vaší App Service Environment se odstraní a místo toho je nutné zadat subdoméně App Service Environment. V App Service Environment externí VIP název App Service Environment se používá v subdoméně pro aplikace vytvořené v této App Service Environment. Pokud je název vaší App Service Environment ***contosotest*** a název aplikace v rámci této App Service Environment ***test***, je subdoménou formátu ***contosotest.p.azurewebsites.net*** a že je adresa URL pro tuto aplikaci ***mytest.contosotest.p.azurewebsites.net***. Pokud nastavíte na interní typ VIP, váš název App Service Environment nepoužívá v subdoméně pro App Service Environment. Zadejte subdoméně explicitně. Pokud je vaše subdomény ***contoso.corp.net*** a k aplikaci v tom, že s názvem App Service Environment ***timereporting***, adresa URL pro tuto aplikaci je ***timereporting.contoso.corp.net***.

## <a name="apps-in-an-ilb-ase"></a>Aplikace v App Service Environment ILB
Vytvoření aplikace v App Service Environment ILB je stejný jako při vytváření aplikace v App Service Environment normálně. 

1. Na portálu Azure vyberte **vytvořit prostředek -> Web + mobilní zařízení -> Web** nebo **Mobile** nebo **aplikace API**.
2. Zadejte název aplikace.
3. Vyberte své předplatné.
4. Vyberte nebo vytvořte skupinu prostředků.
5. Vyberte nebo vytvořte Plan(ASP) aplikaci služby. Pokud vytváříte novou ASP, vyberte vaše App Service Environment jako umístění a vyberte fond pracovních procesů, který chcete, aby vaše ASP vytvořit v. Když vytvoříte ASP, vyberete vaší App Service Environment jako umístění a fondu pracovních procesů. Když zadáte název aplikace, zobrazí se, že je pro vaše App Service Environment subdoméně nahrazen subdoméně pod názvem vaší aplikace. 
6. Vyberte **Vytvořit**. Je nutné vybrat **připnout na řídicí panel** zaškrtávací políčko, pokud chcete, aby aplikace objeví na řídicím panelu. 

![][2]

V části název aplikace získá název subdomény aktualizovat tak, aby odrážela subdoméně vaší App Service Environment. 

## <a name="post-ilb-ase-creation-validation"></a>Ověření vytvoření POST ILB App Service Environment
Služba ASE s interním nástrojem pro vyrovnávání zatížení se trochu liší od služby bez interního nástroje pro vyrovnávání zatížení. Jak již poznamenat, budete muset spravovat vlastní DNS a je nutné také zadat svůj vlastní certifikát pro připojení prostřednictvím protokolu HTTPS. 

Po vytvoření vaší App Service Environment, si všimnete, že subdoméně ukazuje subdoméně jste zadali, a je novou položku v **nastavení** nabídky s názvem **ILB certifikát**. App Service Environment se vytvoří s certifikát podepsaný svým držitelem, který usnadňuje testování HTTPS. Na portálu informuje, že budete muset zadat svůj vlastní certifikát pro protokol HTTPS, ale toto nastavení slouží k doporučujeme mít certifikát, který prochází s vaší subdomény. 

![][3]

Pokud jsou jednoduše vyzkoušení věcí a nemusíte vědět, jak vytvořit certifikát, můžete vytvořit certifikát podepsaný svým držitelem aplikace konzoly MMC služby IIS. Jakmile je vytvořen, můžete ho exportovat jako soubor .pfx a nahrajte ho v uživatelském rozhraní ILB certifikátu. Při přístupu k webu zabezpečenému protokolem certifikát podepsaný svým držitelem, prohlížeč vám dává upozornění, že lokality, ke které přistupujete není zabezpečený z důvodu nemůže ověřit certifikát. Pokud chcete, aby se zabránilo tohoto upozornění, budete potřebovat správně podepsaný certifikát, který odpovídá vaší subdomény a má řetěz certifikátů, který váš prohlížeč rozpozná.

![][6]

Pokud chcete opakujte postup s vlastní certifikáty a testování HTTP a HTTPS přístup k vaší App Service Environment:

1. Přejděte na uživatelské rozhraní App Service Environment po vytvoření App Service Environment **App Service Environment -> Nastavení -> ILB certifikáty**.
2. Nastavte ILB certifikát tak, že vyberete soubor pfx certifikátu a zadejte heslo. Tento krok vezme chvíli zpracovat a zobrazí se zpráva, která probíhá škálování operace.
3. Získat adresu ILB pro vaše App Service Environment (**App Service Environment -> Vlastnosti -> virtuální IP adresy**).
4. Vytvoření webové aplikace v App Service Environment po vytvoření. 
5. Vytvoření virtuálního počítače, pokud nemáte v ní (ne ve stejné podsíti jako rozdělení App Service Environment nebo věcí).
6. Nastavení DNS pro vaše subdomény. Můžete použít zástupný znak s vaší subdomény v DNS nebo pokud budete chtít provést několik jednoduchých testů, úprava souboru hostitelů na vašem virtuálním počítači nastavit název webové aplikace na virtuální IP adresy IP adresu. Pokud vaše App Service Environment měli název subdomény. ilbase.com a můžete provedeny mytestapp aplikaci web tak, že by se řešit na mytestapp.ilbase.com, nastavte ho v souboru hostitelů. (V systému Windows, soubor hostitele je v C:\Windows\System32\drivers\etc\)
7. Použít prohlížeč na tento virtuální počítač a přejděte k http://mytestapp.ilbase.com (nebo ať je název vaší webové aplikace s vaší subdomény).
8. Otevřete v tomto virtuálním počítači prohlížeč a přejděte na adresu https://mytestapp.ilbase.com. Pokud používáte certifikát podepsaný svým držitelem, je nutné přijmout nedostatek zabezpečení. 

IP adresu pro vaše ILB je uveden ve vlastnostech jako virtuální IP adresy.

![][4]

## <a name="using-an-ilb-ase"></a>Používá službu App Service Environment s interním nástrojem pro vyrovnávání zatížení.
#### <a name="network-security-groups"></a>Network Security Groups (Skupiny zabezpečení sítě)
App Service Environment ILB umožňuje izolaci sítě pro vaše aplikace. Aplikace nejsou přístupné nebo i známých pomocí Internetu. Tento přístup je vynikající pro hostování intranetových serverů, jako jsou například-obchodní aplikace. Když potřebujete omezit přístup ještě víc, stále můžete Groups(NSGs) zabezpečení sítě pro řízení přístupu na úrovni sítě. 

Pokud chcete použít skupiny Nsg a dál omezit přístup, musí se ujistěte, že nedojde k narušení komunikaci, která App Service Environment potřebuje, aby provoz. I když přístup protokolu HTTP nebo HTTPS se pouze prostřednictvím ILB používané App Service Environment, App Service Environment se stále závisí na prostředky mimo síť VNet. Informace, které přístup k síti se stále vyžadují, najdete v tématu [řízení příchozí přenosy do služby App Service Environment] [ ControlInbound] a [podrobnosti konfigurace sítě pro prostředí App Service pomocí ExpressRoute][ExpressRoute]. 

Pokud chcete konfigurovat skupiny Nsg, musíte znát IP adresu, která se používají v Azure ke správě vašeho App Service Environment. Tuto IP adresu je také odchozí IP adresu z vaší App Service Environment, pokud se zadá žádosti internetových. Odchozí IP adresu pro vaše App Service Environment zůstane statické po celou dobu životnosti vaší App Service Environment. Pokud odstraníte a znovu vytvořte vaše App Service Environment, obdržíte novou IP adresu. Chcete-li najít IP adresu, přejděte na **Nastavení -> vlastnosti** a najděte **odchozí IP adresu**. 

![][5]

#### <a name="general-ilb-ase-management"></a>Správa obecné ILB App Service Environment
Správa App Service Environment ILB je z velké části stejný jako správa App Service Environment normálně. Musí škálovat vaše fondy pracovních procesů pro hostování více instancí ASP a škálovat vaše servery front-end pro zpracování vyšší objemy přenosy HTTP/HTTPS. Obecné informace o správě konfigurace App Service Environment, najdete v části [konfigurace služby App Service Environment][ASEConfig]. 

Další správu položky jsou správy certifikátů a správu DNS. Je nutné získat a nahrát na server certifikát pro protokol HTTPS používá po vytvoření ILB App Service Environment a nahraďte ji, než vyprší její platnost. Protože Azure vlastní základní doménu, můžete získat certifikáty pro ASEs externí VIP. Vzhledem k tomu, že subdomény používané ILB App Service Environment může být jakýkoli, je nutné zadat svůj vlastní certifikát pro protokol HTTPS. 

#### <a name="dns-configuration"></a>Konfigurace služby DNS
Pokud používáte externí VIP, DNS spravuje Azure. Všechny aplikace vytvořené ve vaší službě ASE se automaticky přidají do Azure DNS, což je veřejná služba DNS. Ve službě ASE s interním nástrojem pro vyrovnávání zatížení musíte spravovat vlastní službu DNS. Pro danou subdomény, jako je například contoso.corp.net je nutné vytvořit DNS A záznamy, které ukazují na vaši adresu ILB pro:

    * 
    publikování *.SCM ftp 


## <a name="getting-started"></a>Začínáme
Chcete-li začít pracovat s prostředí App Service, přečtěte si téma [Úvod do prostředí App Service][WhatisASE]

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
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[vnetnsgs]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[ASEConfig]: app-service-web-configure-an-app-service-environment.md
