---
title: Nejčastější dotazy ke správě rozhraní API Azure | Dokumenty společnosti Microsoft
description: Seznamte se s odpověďmi na nejčastější dotazy, vzory a osvědčené postupy ve správě rozhraní Azure API.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 2fa193cd-ea71-4b33-a5ca-1f55e5351e23
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: c32fdc67c74e100e0e31dad3afde128c05c356d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335969"
---
# <a name="azure-api-management-faqs"></a>Nejčastější dotazy ke správě rozhraní API Azure
Získejte odpovědi na běžné otázky, vzory a osvědčené postupy pro Azure API Management.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="contact-us"></a>Kontaktujte nás
* [Jak se můžu týmu Microsoft Azure API Management na něco zeptat?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question)

## <a name="frequently-asked-questions"></a>Nejčastější dotazy
* [Co to znamená, když je funkce ve verzi Preview?](#what-does-it-mean-when-a-feature-is-in-preview)
* [Jak můžu zabezpečit připojení mezi bránou služby API Management a back-endovými službami?](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
* [Jak můžu zkopírovat instanci služby API Management do nové instance?](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance)
* [Můžu spravovat instanci správy rozhraní API programově?](#can-i-manage-my-api-management-instance-programmatically)
* [Jak přidám uživatele do skupiny správců?](#how-do-i-add-a-user-to-the-administrators-group)
* [Proč je zásada, kterou chci přidat, nedostupná v editoru zásad?](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
* [Jak nastavím více prostředí v jednom rozhraní API?](#how-do-i-set-up-multiple-environments-in-a-single-api)
* [Můžu se správou rozhraní API používat SOAP?](#can-i-use-soap-with-api-management)
* [Mohu nakonfigurovat autorizační server OAuth 2.0 se zabezpečením služby AD FS?](#can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security)
* [Jakou metodu směrování používá správa rozhraní API v nasazeních do více geografických umístění?](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
* [Můžu použít šablonu Azure Resource Managerka k vytvoření instance služby Api Management?](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
* [Mohu pro back-end použít certifikát TLS/SSL podepsaný svým držitelem?](#can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end)
* [Proč se při pokusu o klonování úložiště GIT zobrazí chyba ověřování?](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
* [Funguje správa rozhraní API s Azure ExpressRoute?](#does-api-management-work-with-azure-expressroute)
* [Proč vyžadujeme vyhrazenou podsíť ve stylu VNET správce prostředků, když je do nich nasazena správa rozhraní API?](#why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them)
* [Jaká je minimální velikost podsítě potřebná při nasazování správy rozhraní API do virtuální sítě?](#what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet)
* [Můžu přesunout službu API Management z jednoho předplatného do jiného?](#can-i-move-an-api-management-service-from-one-subscription-to-another)
* [Existují omezení nebo známé problémy s importem mého rozhraní API?](#are-there-restrictions-on-or-known-issues-with-importing-my-api)

### <a name="how-can-i-ask-the-microsoft-azure-api-management-team-a-question"></a>Jak se můžu týmu Microsoft Azure API Management na něco zeptat?
Můžete nás kontaktovat pomocí jedné z těchto možností:

* Zveřejněte své dotazy v našem [fóru MSDN pro správu rozhraní API](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt).
* Odešlete email na adresu <mailto:apimgmt@microsoft.com>.
* Pošlete nám žádost o funkci ve [fóru pro zpětnou vazbu Azure](https://feedback.azure.com/forums/248703-api-management).

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>Co to znamená, když je funkce ve verzi Preview?
Když je funkce ve verzi Preview, znamená to, že aktivně hledáme zpětnou vazbu o tom, jak tato funkce pracuje pro vás. Funkce ve verzi Preview je funkčně dokončená, ale je možné, že v reakci na zpětnou vazbu od zákazníků provedeme zásadní změnu. Doporučujeme, abyste nebyli závislí na funkci, která je ve verzi Preview ve vašem produkčním prostředí. Pokud máte nějakou zpětnou vazbu k funkcím náhledu, dejte nám vědět prostřednictvím jedné z možností kontaktu v [části Jak se mohu týmu Microsoft Azure API Management na něco zeptat?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question)

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>Jak můžu zabezpečit připojení mezi bránou služby API Management a back-endovými službami?
Máte několik možností, jak zabezpečit připojení mezi bránou pro správu rozhraní API a back-endovými službami. Můžete:

* Použijte základní ověřování HTTP. Další informace naleznete v [tématu Import a publikování prvního rozhraní API](import-and-publish.md).
* Použijte vzájemné ověřování TLS, jak je popsáno v [části Zabezpečení back-endových služeb pomocí ověřování klientských certifikátů ve správě rozhraní Azure API](api-management-howto-mutual-certificates.md).
* Použijte IP whitelisting na back-end služby. Ve všech úrovních správy rozhraní API s výjimkou úrovně Consumption zůstane IP adresa brány konstantní, s několika upozorněními popsanými v [článku dokumentace IP](api-management-howto-ip-addresses.md).
* Připojte instanci správy rozhraní API k virtuální síti Azure.

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>Jak můžu zkopírovat instanci služby API Management do nové instance?
Máte několik možností, pokud chcete zkopírovat instanci správy rozhraní API do nové instance. Můžete:

* Funkce zálohování a obnovení použijte ve správě rozhraní API. Další informace najdete v [tématu Jak implementovat zotavení po havárii pomocí zálohování a obnovení služby ve správě rozhraní Azure API](api-management-howto-disaster-recovery-backup-restore.md).
* Vytvořte si vlastní funkci zálohování a obnovení pomocí rozhraní [API API PRO správu rozhraní API](/rest/api/apimanagement/). Pomocí rozhraní REST API uložte a obnovte entity z instance služby, kterou chcete.
* Stáhněte si konfiguraci služby pomocí Gitu a pak ji nahrajte do nové instance. Další informace naleznete v tématu [Jak uložit a nakonfigurovat konfiguraci služby API Management pomocí Gitu](api-management-configuration-repository-git.md).

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>Můžu spravovat instanci správy rozhraní API programově?
Ano, správu rozhraní API můžete spravovat programově pomocí:

* Rozhraní [REST API pro správu rozhraní API rozhraní API](/rest/api/apimanagement/).
* [Sada SDK knihovny Microsoft Azure ApiManagement Service Management Library](https://aka.ms/apimsdk).
* Rutiny Prostředí PowerShell [pro nasazení a](https://docs.microsoft.com/powershell/module/wds) [služby.](https://docs.microsoft.com/powershell/azure/servicemanagement/overview)

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>Jak přidám uživatele do skupiny správců?
Uživatele můžete přidat do skupiny Administrators takto:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Přejděte do skupiny prostředků, která má instanci správy rozhraní API, kterou chcete aktualizovat.
3. Ve správě rozhraní API přiřaďte uživateli roli **přispěvatele služby Api Management Service.**

Nově přidaný přispěvatel teď může používat [rutiny](https://docs.microsoft.com/powershell/azure/overview)Prostředí Azure PowerShell . Zde je postup, jak se přihlásit jako správce:

1. Pomocí `Connect-AzAccount` rutiny se přihlaste.
2. Nastavte kontext na předplatné, které má `Set-AzContext -SubscriptionID <subscriptionGUID>`službu pomocí .
3. Získejte adresu URL pro `Get-AzApiManagementSsoToken -ResourceGroupName <rgName> -Name <serviceName>`jednotné přihlášení pomocí aplikace .
4. Pomocí adresy URL přejděte na portál pro správu.

### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>Proč je zásada, kterou chci přidat, nedostupná v editoru zásad?
Pokud zásady, které chcete přidat, se v editoru zásad zobrazí šedě nebo stínované, ujistěte se, že jste ve správném oboru pro zásadu. Každé prohlášení o zásadách je navrženo pro použití v konkrétních oborech a oddílech zásad. Části zásad a obory zásad najdete v části Využití zásad v [zásadách správy rozhraní API](/azure/api-management/api-management-policies).

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>Jak nastavím více prostředí v jednom rozhraní API?
Chcete-li nastavit více prostředí, například testovací prostředí a produkční prostředí, v jednom rozhraní API, máte dvě možnosti. Můžete:

* Hostovat různá api na stejném tenantovi.
* Hostovat stejná api na různých klientů.

### <a name="can-i-use-soap-with-api-management"></a>Můžu se správou rozhraní API používat SOAP?
[Soap předávací](https://blogs.msdn.microsoft.com/apimanagement/2016/10/13/soap-pass-through/) podpora je nyní k dispozici. Správci mohou importovat WSDL své služby SOAP a Azure API Management vytvoří front-end SOAP. Dokumentace vývojářského portálu, testovací konzola, zásady a analýzy jsou k dispozici pro služby SOAP.

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>Mohu nakonfigurovat autorizační server OAuth 2.0 se zabezpečením služby AD FS?
Informace o konfiguraci autorizačního serveru OAuth 2.0 se zabezpečením služby AD FS (AD FS) naleznete [v tématu Použití služby ADFS ve správě rozhraní API](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/).

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>Jakou metodu směrování používá správa rozhraní API v nasazeních do více geografických umístění?
Správa rozhraní API používá [metodu směrování provozu výkonu](../traffic-manager/traffic-manager-routing-methods.md#performance) v nasazeních do více geografických umístění. Příchozí provoz je směrován do nejbližší brány rozhraní API. Pokud jedna oblast přejde do offline, příchozí provoz se automaticky směruje na nejbližší nejbližší bránu. Další informace o metodách směrování v [metodách směrování Traffic Manageru](../traffic-manager/traffic-manager-routing-methods.md).

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>Můžu použít šablonu Azure Resource Managerka k vytvoření instance služby Api Management?
Ano. Podívejte se na šablony rychlého startu [služby Azure API Management Service.](https://aka.ms/apimtemplate)

### <a name="can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end"></a>Mohu pro back-end použít certifikát TLS/SSL podepsaný svým držitelem?
Ano. To lze provést prostřednictvím prostředí PowerShell nebo přímým odesláním do rozhraní API. Tím zakážete ověřování řetězu certifikátů a umožníte vám používat certifikáty podepsané svým držitelem nebo soukromě podepsané při komunikaci ze správy rozhraní API do back-endových služeb.

#### <a name="powershell-method"></a>Metoda Powershell ####
Použijte (pro nový back-end) [`Set-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/set-azapimanagementbackend) nebo (pro existující back-end) `-SkipCertificateChainValidation` Rutiny `True`Prostředí PowerShell a nastavte parametr na . [`New-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/new-azapimanagementbackend)

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

#### <a name="direct-api-update-method"></a>Metoda aktualizace rozhraní DIRECT API ####
1. Vytvořte [back-endovou entitu](/rest/api/apimanagement/) pomocí správy rozhraní API.     
2. Nastavte vlastnost **skipChainChainValidation** na **hodnotu true**.     
3. Pokud již nechcete povolit certifikáty podepsané svým držitelem, odstraňte entitu Back-end nebo nastavte vlastnost **skipChainChainValidation** na **hodnotu false**.

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>Proč při pokusu o klonování úložiště Git dochází k selhání ověřování?
Pokud používáte Git Credential Manager, nebo pokud se pokoušíte klonovat úložiště Git pomocí Sady Visual Studio, můžete narazit na známý problém s dialogovém oknem pověření systému Windows. Dialogové okno omezuje délku hesla na 127 znaků a zkrátí heslo generované společností Microsoft. Pracujeme na zkrácení hesla. Prozatím použijte Git Bash ke klonování úložiště Git.

### <a name="does-api-management-work-with-azure-expressroute"></a>Funguje správa rozhraní API s Azure ExpressRoute?
Ano. Správa rozhraní API funguje s Azure ExpressRoute.

### <a name="why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them"></a>Proč vyžadujeme vyhrazenou podsíť ve stylu VNET správce prostředků, když je do nich nasazena správa rozhraní API?
Vyhrazené požadavek podsítě pro správu rozhraní API pochází ze skutečnosti, že je postaven na klasickém (vrstva PATY V1) nasazení modelu. Zatímco můžeme nasadit do virtuální sítě Správce prostředků (V2 vrstva), existují důsledky. Model klasického nasazení v Azure není úzce spojen s modelem Resource Manager, a proto pokud vytvoříte prostředek ve vrstvě V2, vrstva V1 o tom neví a může dojít k problémům, jako je správa rozhraní API, která se pokouší použít IP adresu, která je již přidělena nic ( na V2).
Další informace o rozdílech v modelech Classic a Resource Manager v Azure najdete [v rozdílech v modelech nasazení](../azure-resource-manager/management/deployment-models.md).

### <a name="what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet"></a>Jaká je minimální velikost podsítě potřebná při nasazování správy rozhraní API do virtuální sítě?
Minimální velikost podsítě potřebná k nasazení správy rozhraní API je [/29](../virtual-network/virtual-networks-faq.md#configuration), což je minimální velikost podsítě, kterou Azure podporuje.

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>Můžu přesunout službu API Management z jednoho předplatného do jiného?
Ano. Postup najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/management/move-resource-group-and-subscription.md).

### <a name="are-there-restrictions-on-or-known-issues-with-importing-my-api"></a>Existují omezení nebo známé problémy s importem mého rozhraní API?
[Známé problémy a omezení](api-management-api-import-restrictions.md) pro formáty Open API(Swagger), WSDL a WADL.
