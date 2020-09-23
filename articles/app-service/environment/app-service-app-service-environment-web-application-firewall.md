---
title: Konfigurace WAF
description: Přečtěte si, jak nakonfigurovat Firewall webových aplikací (WAF) před vaším App Service Environment, a to buď pomocí Azure Application Gateway, nebo WAF třetí strany.
author: ccompy
ms.assetid: a2101291-83ba-4169-98a2-2c0ed9a65e8d
ms.topic: tutorial
ms.date: 03/03/2018
ms.author: stefsch
ms.custom: mvc, seodec18
ms.openlocfilehash: 354568fa3ab3816b643a8f08305ab55868a9b0b6
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90973707"
---
# <a name="configuring-a-web-application-firewall-waf-for-app-service-environment"></a>Konfigurace brány firewall webových aplikací pro službu App Service Environment
## <a name="overview"></a>Přehled

Brány firewall webových aplikací (WAF) pomáhají zabezpečit webové aplikace tím, že zkoumají příchozí webový provoz a blokují injektáže SQL, skriptování napříč weby, nahrávání malwaru, útoky DDoS a další útoky. Zkoumají také odpovědi webových serverů pro zajištění ochrany před únikem informací (DLP). V kombinaci s izolací a dalším škálováním, které poskytuje služba App Service Environment, to představuje ideální prostředí pro hostování důležitých obchodních webových aplikací, které potřebují odolat škodlivým požadavkům a velkému objemu provozu. Azure poskytuje funkci WAF prostřednictvím služby [Application Gateway](../../application-gateway/overview.md).  Pokud chcete zjistit, jak integrovat službu App Service Environment se službou Application Gateway, přečtěte si dokument [Integrace služby ASE s interním nástrojem pro vyrovnávání zatížení se službou Application Gateway](./integrate-with-application-gateway.md).

Kromě služby Azure Application Gateway je k dispozici několik možností na webu [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.waf?tab=PlansAndPrice), například [Barracuda WAF pro Azure](https://www.barracuda.com/programs/azure). Zbytek tohoto dokumentu se zaměřuje na integraci služby App Service Environment se zařízením Barracuda WAF.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="setup"></a>Nastavení
Pro účely tohoto dokumentu nakonfigurujeme službu App Service Environment za několika instancemi Barracuda WAF s vyrovnáváním zatížení, aby se do služby App Service Environment dostal pouze provoz z této brány WAF a aby služba nebyla přístupná z DMZ. Před instancí Barracuda WAF také máme službu Azure Traffic Manager, která vyrovnává zatížení napříč datovými centry a oblastmi Azure. Podrobný diagram nastavení bude podobný jako na následujícím obrázku:

![Diagram znázorňuje volitelnou službu Azure Traffic Manager připojující se k instancím brány firewall webových aplikací, která se připojuje k síti A C L, aby povolovala přenosy jenom z brány firewall v App Service Environment, která obsahuje web, P I a mobilní aplikace pro dvě oblasti.][Architecture] 

> [!NOTE]
> Díky zavedení [podpory interního nástroje pro vyrovnávání zatížení pro službu App Service Environment](app-service-environment-with-internal-load-balancer.md) můžete nakonfigurovat službu ASE tak, aby nebyla přístupná z DMZ a byla dostupná pouze pro privátní síť. 
> 
> 

## <a name="configuring-your-app-service-environment"></a>Konfigurace služby App Service Environment
Informace o konfiguraci služby App Service Environment najdete v [naší dokumentaci](app-service-web-how-to-create-an-app-service-environment.md) k tomuto tématu. Po vytvoření služby App Service Environment můžete v tomto prostředí vytvářet funkce Web Apps, API Apps a [Mobile Apps](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop) chráněné za bránou WAF, kterou nakonfigurujeme v další části.

## <a name="configuring-your-barracuda-waf-cloud-service"></a>Konfigurace cloudové služby Barracuda WAF
Barracuda nabízí [podrobný článek](https://campus.barracuda.com/product/webapplicationfirewall/article/WAF/DeployWAFInAzure) věnovaný nasazení jejich brány WAF na virtuální počítač v Azure. Protože však chceme zajistit redundanci a vyhnout se kritickému prvku způsobujícímu selhání, měli byste do stejné cloudové služby nasadit alespoň dvě instance virtuálních počítačů s WAF, pokud budete postupovat podle těchto pokynů.

### <a name="adding-endpoints-to-cloud-service"></a>Přidání koncových bodů do cloudové služby
Jakmile ve své cloudové službě budete mít alespoň 2 instance virtuálních počítačů s WAF, můžete pomocí webu [Azure Portal](https://portal.azure.com/) přidat koncové body HTTP a HTTPS, které bude využívat vaše aplikace, jak je znázorněno na následujícím obrázku:

![Konfigurace koncového bodu][ConfigureEndpoint]

Pokud vaše aplikace používá i jiné koncové body, nezapomeňte je také přidat do tohoto seznamu. 

### <a name="configuring-barracuda-waf-through-its-management-portal"></a>Konfigurace brány Barracuda WAF prostřednictvím jejího portálu pro správu
Barracuda WAF ke konfiguraci prostřednictvím svého portálu pro správu používá port TCP 8000. Pokud máte několik instancí virtuálních počítačů s WAF, je potřeba zde uvedené kroky zopakovat pro všechny instance virtuálních počítačů. 

> [!NOTE]
> Jakmile budete hotovi s konfigurací WAF, zajistěte zabezpečení WAF odebráním koncového bodu TCP/8000 ze všech virtuálních počítačů s WAF.
> 
> 

Přidejte koncový bod správy umožňující konfiguraci Barracuda WAF, jak je znázorněno na následujícím obrázku.

![Přidání koncového bodu správy][AddManagementEndpoint]

Pomocí prohlížeče přejděte na koncový bod správy vaší cloudové služby. Pokud má vaše cloudová aplikace název test.cloudapp.net, můžete k tomuto koncovému bodu přejít na adrese `http://test.cloudapp.net:8000`. Měla by se zobrazit přihlašovací stránka jako na následujícím obrázku, kde se můžete přihlásit pomocí přihlašovacích údajů, které jste zadali ve fázi nastavování virtuálního počítače s WAF.

![Správa – Přihlašovací stránka][ManagementLoginPage]

Po přihlášení by se měl zobrazit podobný řídicí panel jako na následujícím obrázku, kde najdete základní statistiky o ochraně WAF.

![Správa – Řídicí panel][ManagementDashboard]

Po kliknutí na kartu **Services** (Služby) můžete konfigurovat bránu WAF pro služby, které chrání. Další podrobnosti o konfiguraci Barracuda WAF najdete v příslušné [dokumentaci](https://techlib.barracuda.com/waf/getstarted1). V následujícím příkladu je nakonfigurované App Service aplikace obsluhující provoz na protokolech HTTP a HTTPS.

![Správa – Přidání služeb][ManagementAddServices]

> [!NOTE]
> V závislosti na tom, jak jsou vaše aplikace nakonfigurované a jaké funkce se ve vašem App Service Environment používají, musíte přesměrováním provozu u jiných portů TCP než 80 a 443, například pokud máte nastavení protokolu IP TLS pro aplikaci App Service. Seznam síťových portů používaných ve službě App Service Environment najdete v části Síťové porty v [dokumentaci k řízení příchozího provozu](app-service-app-service-environment-control-inbound-traffic.md).
> 
> 

## <a name="configuring-microsoft-azure-traffic-manager-optional"></a>Konfigurace služby Microsoft Azure Traffic Manager (VOLITELNÉ)
Pokud je vaše aplikace dostupná ve více oblastech, měli byste vyrovnávat jejich zatížení za službou [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md). Pokud to chcete provést, můžete pro svou bránu WAF přidat koncový bod s použitím názvu cloudové služby na webu [Azure Portal](https://portal.azure.com) v profilu služby Traffic Manager, jak je znázorněno na následujícím obrázku. 

![Koncový bod služby Traffic Manager][TrafficManagerEndpoint]

Pokud vaše aplikace vyžaduje ověřování, ujistěte se, že máte některé prostředky, které nevyžadují ověřování, aby služba Traffic Manager mohla pomocí příkazu ping testovat dostupnost vaší aplikace. Adresu URL tohoto prostředku můžete nakonfigurovat na stránce **Konfigurace** na webu [Azure Portal](https://portal.azure.com), jak je znázorněno na následujícím obrázku:

![Konfigurace služby Traffic Manager][ConfigureTrafficManager]

Pokud chcete směrovat příkazy ping služby Traffic Manager z brány WAF do aplikace, musíte v bráně Barracuda WAF nastavit funkci Website Translations (Překlad webů) pro směrování provozu do vaší aplikace, jak je znázorněno v následujícím příkladu:

![Website Translations (Překlady webů)][WebsiteTranslations]

## <a name="securing-traffic-to-app-service-environment-using-network-security-groups-nsg"></a>Zabezpečení provozu do služby App Service Environment pomocí skupin zabezpečení sítě (NSG)
Podrobnosti o omezení provozu do služby App Service Environment z brány WAF pouze pomocí virtuální IP adresy cloudové služby najdete v [dokumentaci k řízení příchozího provozu](app-service-app-service-environment-control-inbound-traffic.md). Zde je ukázkový příkaz prostředí PowerShell pro provádění této úlohy pro port TCP 80.

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "RestrictWestUSAppAccess" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP Barracuda" -Type Inbound -Priority 201 -Action Allow -SourceAddressPrefix '191.0.0.1'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
```

Hodnotu SourceAddressPrefix nahraďte virtuální IP adresou vaší cloudové služby WAF.

> [!NOTE]
> Pokud cloudovou službu odstraníte a znovu vytvoříte, její virtuální IP adresa se změní. Pokud do provedete, nezapomeňte aktualizovat IP adresu ve skupině síťových prostředků. 
> 
> 

<!-- IMAGES -->
[Architecture]: ./media/app-service-app-service-environment-web-application-firewall/Architecture.png
[ConfigureEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureEndpoint.png
[AddManagementEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/AddManagementEndpoint.png
[ManagementAddServices]: ./media/app-service-app-service-environment-web-application-firewall/ManagementAddServices.png
[ManagementDashboard]: ./media/app-service-app-service-environment-web-application-firewall/ManagementDashboard.png
[ManagementLoginPage]: ./media/app-service-app-service-environment-web-application-firewall/ManagementLoginPage.png
[TrafficManagerEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/TrafficManagerEndpoint.png
[ConfigureTrafficManager]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureTrafficManager.png
[WebsiteTranslations]: ./media/app-service-app-service-environment-web-application-firewall/WebsiteTranslations.png