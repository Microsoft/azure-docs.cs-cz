---
title: Nejčastější dotazy k Azure API Management | Microsoft Docs
description: Další odpovědi na nejčastější dotazy (FAQ), v případě vzorů a osvědčených postupů v Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 2fa193cd-ea71-4b33-a5ca-1f55e5351e23
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 243f0b8206166e3e054dfc423259bd3444d8a2db
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="azure-api-management-faqs"></a>Nejčastější dotazy Azure API Management
Získejte odpovědi na časté otázky, vzorce a osvědčené postupy pro Azure API Management.

## <a name="contact-us"></a>Kontaktujte nás
* [Jak můžete I zeptejte týmem Microsoft Azure API Management?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question)

## <a name="frequently-asked-questions"></a>Nejčastější dotazy
* [Co znamená funkce je ve verzi preview?](#what-does-it-mean-when-a-feature-is-in-preview)
* [Jak můžete zabezpečit připojení mezi bránou rozhraní API Management a Moje back endové služby?](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
* [Jak kopírovat Moje instance služby API Management na novou instanci?](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance)
* [Můžete spravovat instance Moje API Management prostřednictvím kódu programu?](#can-i-manage-my-api-management-instance-programmatically)
* [Jak přidat uživatele do skupiny Administrators?](#how-do-i-add-a-user-to-the-administrators-group)
* [Proč se zásadami, které chcete přidat k dispozici v editoru zásad?](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
* [Jak nastavím prostředí s více v jediného rozhraní API?](#how-do-i-set-up-multiple-environments-in-a-single-api)
* [Můžete použít protokolu SOAP s API Management?](#can-i-use-soap-with-api-management)
* [Je konstanta IP adresu brány API Management? Můžete je používat v pravidlech brány firewall?](#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules)
* [Můžete nakonfigurovat serveru ověřování OAuth 2.0 pomocí služby AD FS zabezpečení?](#can-i-configure-an-oauth-20-authorization-server-with-adfs-security)
* [V nasazení na více geografické polohy jaké metody směrování používá rozhraní API Management?](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
* [Můžete použít šablonu Azure Resource Manageru k vytvoření instance služby API Management?](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
* [Můžete použít certifikát podepsaný svým držitelem SSL pro back-end?](#can-i-use-a-self-signed-ssl-certificate-for-a-back-end)
* [Proč se při pokusu o klonování úložiště GIT zobrazí chybu ověřování?](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
* [Funguje s Azure ExpressRoute API Management?](#does-api-management-work-with-azure-expressroute)
* [Proč jsme vyžaduje vyhrazenou podsíť ve stylu Resource Manager virtuální sítě, když API Management se nasadí do nich?](#why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them)
* [Jaká je velikost podsítě minimální potřebné při nasazování API Management do virtuální sítě?](#what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet)
* [Lze přesunout služby API Management z jedno předplatné do jiného?](#can-i-move-an-api-management-service-from-one-subscription-to-another)
* [Existují známé problémy s importem Moje rozhraní API nebo omezení?](#are-there-restrictions-on-or-known-issues-with-importing-my-api)

### <a name="how-can-i-ask-the-microsoft-azure-api-management-team-a-question"></a>Jak můžete I zeptejte týmem Microsoft Azure API Management?
Kontaktujte nás pomocí jedné z těchto možností:

* Zveřejněte svoje otázky v našem [fórum MSDN správy rozhraní API](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt).
* E-mailovou zprávu na <mailto:apimgmt@microsoft.com>.
* Pošlete nám žádosti o funkci [fóru pro zpětnou vazbu Azure](https://feedback.azure.com/forums/248703-api-management).

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>Co znamená funkce je ve verzi preview?
Když je funkce ve verzi preview, znamená to, že jsme se aktivně vyhledávání zpětnou vazbu na tom, jak funkce funguje pro vás. Funkce ve verzi preview je funkčně dokončení, ale je možné, že budete uděláme narušující, změňte v reakci na zpětnou. Doporučujeme vám, že nemáte závisí na funkce, která je ve verzi preview v provozním prostředí. Pokud máte jakékoli názor na funkce verze preview, dejte nám vědět prostřednictvím jednoho z možnosti kontaktů v [jak můžete I zeptejte týmem Microsoft Azure API Management?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question).

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>Jak můžete zabezpečit připojení mezi bránou rozhraní API Management a Moje back endové služby?
Máte několik možností k zabezpečení připojení mezi bránou rozhraní API Management a back endové služby. Můžete:

* Základní ověřování pomocí protokolu HTTP. Další informace najdete v tématu [importu a publikování vašeho prvního rozhraní API](import-and-publish.md).
* Používá vzájemné ověřování SSL, jak je popsáno v [jak zabezpečit back endové služby pomocí klienta ověření certifikátu ve službě Azure API Management](api-management-howto-mutual-certificates.md).
* Vytvoření seznamu povolených IP použijte na back endové službě. Na všech úrovních služby API Management, IP adresu brány, zůstane konstantní, s několika [upozornění](#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules). Můžete nastavit seznamu povolených serverů povolit tuto IP adresu. Na řídicím panelu portálu Azure můžete získat IP adresu instanci služby API Management.
* Vaše instance služby API Management se připojte k virtuální síti Azure.

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>Jak kopírovat Moje instance služby API Management na novou instanci?
Máte několik možností, pokud chcete zkopírovat do nové instance instance služby API Management. Můžete:

* Použít funkci zálohování a obnovení funkce ve službě API Management. Další informace najdete v tématu [implementace zotavení po havárii pomocí služby zálohování a obnovení v Azure API Management](api-management-howto-disaster-recovery-backup-restore.md).
* Vytvoření vlastního zálohování a obnovení funkce pomocí [rozhraní API REST API správy](https://msdn.microsoft.com/library/azure/dn776326.aspx). K uložení a obnovení entity z instance služby, který chcete použijte rozhraní API REST.
* Stažení konfiguraci služby pomocí Git a nahrajte ho do nové instance. Další informace najdete v tématu [uložit a konfiguraci konfigurace služby API Management pomocí Git](api-management-configuration-repository-git.md).

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>Můžete spravovat instance Moje API Management prostřednictvím kódu programu?
Ano, rozhraní API správy programově spravovat pomocí:

* [Rozhraní API REST API správy](https://msdn.microsoft.com/library/azure/dn776326.aspx).
* [Knihovna správy ApiManagement služby Microsoft Azure SDK](http://aka.ms/apimsdk).
* [Nasazení služby](https://msdn.microsoft.com/library/mt619282.aspx) a [Správa služeb](https://msdn.microsoft.com/library/mt613507.aspx) rutiny prostředí PowerShell.

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>Jak přidat uživatele do skupiny Administrators?
Zde je, jak můžete přidat uživatele do skupiny správců:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Přejděte do skupiny prostředků, který má instanci služby API Management, které chcete aktualizovat.
3. Ve službě API Management, přiřadit **rozhraní Api správy Přispěvatel** role pro uživatele.

Teď nově přidané Přispěvatel můžete použít Azure PowerShell [rutiny](https://msdn.microsoft.com/library/mt613507.aspx). Zde je postup Přihlaste se jako správce:

1. Použití `Connect-AzureRmAccount` rutiny k přihlášení.
2. Nastavte kontext předplatného, který má služba pomocí `Set-AzureRmContext -SubscriptionID <subscriptionGUID>`.
3. Získat jednu adresu URL přihlašování pomocí `Get-AzureRmApiManagementSsoToken -ResourceGroupName <rgName> -Name <serviceName>`.
4. Použijte adresu URL pro přístup k portálu pro správu.

### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>Proč se zásadami, které chcete přidat k dispozici v editoru zásad?
Pokud neaktivní nebo stínování editoru zásad, ujistěte se, že jste ve správném oboru pro zásadu, zobrazí se zásadami, které chcete přidat. Každý prohlášení o zásadách je určená pro v konkrétní obory a části zásady použití. Zkontrolovat zásady části a obory pro zásady, najdete v tématu Použití nastavení zásad [zásady služby API Management](https://msdn.microsoft.com/library/azure/dn894080.aspx).

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>Jak nastavím prostředí s více v jediného rozhraní API?
Nastavit několik prostředí, například testovací prostředí a provozním prostředí, do jediného rozhraní API, máte dvě možnosti. Můžete:

* Hostitele jiný rozhraní API na stejném tenantovi.
* Hostitel stejná rozhraní API na jiných klientů.

### <a name="can-i-use-soap-with-api-management"></a>Můžete použít protokolu SOAP s API Management?
[Průchozí SOAP](http://blogs.msdn.microsoft.com/apimanagement/2016/10/13/soap-pass-through/) podpora je k dispozici. Správci mohou importovat WSDL své služby SOAP. Azure API Management se vytvoří front-end protokolu SOAP. Portál dokumentaci pro vývojáře, testovací konzoly, zásady a analýzy byly všechny dostupné pro služby SOAP.

### <a name="is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules"></a>Je konstanta IP adresu brány API Management? Můžete je používat v pravidlech brány firewall?
Na všech úrovních služby API Management veřejnou adresu IP (VIP) klienta API Management je statická po dobu jeho existence klientovi, na několik výjimek. Změny IP adresy v těchto případech:

* Služba je odstranit a pak znovu vytvoří.
* Předplatné služby je [pozastaveno](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) nebo [upozorněn](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) (například nonpayment) a pak obnoveny.
* Přidat nebo odebrat virtuální sítě Azure (virtuální sítě se dá použít jenom na vývojáře a úroveň Premium).

Pro nasazení s více oblast, místní adresa změní Pokud oblast vacated a pak obnoveny (můžete použít nasazení s více oblast jenom na úrovni Premium).

Premium vrstvy klientů, které jsou nakonfigurovány pro nasazení s více oblast přiřazené jednu veřejnou IP adresu podle oblastí.

IP adresa (nebo adresy, v nasazení s více oblast) můžete získat na stránce klienta na portálu Azure.

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>Můžete nakonfigurovat serveru ověřování OAuth 2.0 pomocí služby AD FS zabezpečení?
Zjistěte, jak nakonfigurovat zabezpečení služby Active Directory Federation Services (AD FS) serveru ověřování OAuth 2.0, najdete v tématu [pomocí služby AD FS ve službě API Management](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/).

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>V nasazení na více geografické polohy jaké metody směrování používá rozhraní API Management?
Používá rozhraní API Management [metoda směrování provozu výkonu](../traffic-manager/traffic-manager-routing-methods.md#performance) v nasazení na více geografické polohy. Příchozí provoz se směruje na nejbližší Brána rozhraní API. Pokud jedné oblasti přejde do režimu offline, se automaticky příchozí provoz směruje na další nejbližší brány. Další informace o metodách směrování v [metody směrování Traffic Manageru](../traffic-manager/traffic-manager-routing-methods.md).

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>Můžete použít šablonu Azure Resource Manageru k vytvoření instance služby API Management?
Ano. Najdete v článku [Azure API Management Service](http://aka.ms/apimtemplate) šablony rychlý start.

### <a name="can-i-use-a-self-signed-ssl-certificate-for-a-back-end"></a>Můžete použít certifikát podepsaný svým držitelem SSL pro back-end?
Ano. Tento krok můžete provést pomocí prostředí PowerShell nebo odesláním přímo do rozhraní API. Tím se zakázat ověřování řetězu certifikátů a vám umožní použít certifikáty podepsané svým držitelem nebo soukromě podepsané při komunikaci ze správy rozhraní API na back-end služby.

#### <a name="powershell-method"></a>PowerShell – metoda ####
Použití [ `New-AzureRmApiManagementBackend` ](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/new-azurermapimanagementbackend) (pro nový back-end) nebo [ `Set-AzureRmApiManagementBackend` ](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/set-azurermapimanagementbackend) (pro existující back-end) rutiny prostředí PowerShell a nastavte `-SkipCertificateChainValidation` parametru `True`. 

```
$context = New-AzureRmApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzureRmApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

#### <a name="direct-api-update-method"></a>Přímá metoda aktualizace rozhraní API ####
1. Vytvoření [back-end](https://msdn.microsoft.com/library/azure/dn935030.aspx) entity pomocí rozhraní API Management.       
2. Nastavte **skipCertificateChainValidation** vlastnost **true**.     
3. Pokud již nechcete povolit certifikáty podepsané svým držitelem, odstranit back-end entitu, nebo nastavte **skipCertificateChainValidation** vlastnost **false**.

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>Proč se při pokusu o klonování úložiště Git zobrazí chybu ověřování?
Pokud použijete Správce přihlašovacích údajů Git, nebo pokud se snažíte klonovat úložiště Git pomocí sady Visual Studio, můžete narazit na známý problém s dialogové okno přihlašovací údaje systému Windows. Dialogové okno omezuje délku hesla nastavit 127 znaků a se zkrátí Microsoft vytvořené heslo. Pracujeme na zkrátit heslo. Prozatím použijte Git Bash klonovat úložiště Git.

### <a name="does-api-management-work-with-azure-expressroute"></a>Funguje s Azure ExpressRoute API Management?
Ano. API Management pracuje s Azure ExpressRoute.

### <a name="why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them"></a>Proč jsme vyžaduje vyhrazenou podsíť ve stylu Resource Manager virtuální sítě, když API Management se nasadí do nich?
Požadavek na vyhrazené podsítě pro API Management pochází z fakt, který je založen na modelu nasazení Classic (PAAS V1 layer). Když jsme můžete nasadit do virtuální sítě Resource Manageru (V2 layer), nejsou důsledky, na který. Model nasazení Classic v Azure není pevně pomocí modelu Resource Manager a tak když vytvoříte prostředek v V2 vrstvy, vrstvě V1 neví o něm a může dojít problémům, jako je například Správa API pokusu o použití IP adresy, která je již přidělena síťový adaptér (založený na V2).
Další informace o rozdílu Classic a Resource Manager modelů v Azure najdete v tématu [rozdíl v modelech nasazení](../azure-resource-manager/resource-manager-deployment-model.md).

### <a name="what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet"></a>Jaká je velikost podsítě minimální potřebné při nasazování API Management do virtuální sítě?
Velikost minimální podsítě, které jsou potřeba k nasazení API Management je [/29](../virtual-network/virtual-networks-faq.md#configuration), což je velikost minimální podsítě, který podporuje Azure.

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>Lze přesunout služby API Management z jedno předplatné do jiného?
Ano. Další informace, jak zjistit, [přesunutím prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/resource-group-move-resources.md).

### <a name="are-there-restrictions-on-or-known-issues-with-importing-my-api"></a>Existují známé problémy s importem Moje rozhraní API nebo omezení?
[Známé problémy a omezení](api-management-api-import-restrictions.md) otevřete API(Swagger) WSDL a WADL formáty.
