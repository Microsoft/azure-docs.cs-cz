---
title: Nejčastější dotazy k Azure API Management | Microsoft Docs
description: Přečtěte si odpovědi na nejčastější dotazy, vzory a osvědčené postupy v Azure API Management.
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
ms.openlocfilehash: eea3c8525d31a3ca551e9cbc7d21d7dde163b5cc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94697981"
---
# <a name="azure-api-management-faqs"></a>Nejčastější dotazy k Azure API Management
Získejte odpovědi na běžné otázky, vzory a osvědčené postupy pro Azure API Management.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="frequently-asked-questions"></a>Nejčastější dotazy
* [Co to znamená, když je funkce ve verzi Preview?](#what-does-it-mean-when-a-feature-is-in-preview)
* [Jak můžu zabezpečit připojení mezi bránou služby API Management a back-endovými službami?](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
* [Jak můžu zkopírovat instanci služby API Management do nové instance?](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance)
* [Můžu programově spravovat instanci API Management?](#can-i-manage-my-api-management-instance-programmatically)
* [Jak přidám uživatele do skupiny správců?](#how-do-i-add-a-user-to-the-administrators-group)
* [Proč je zásada, kterou chci přidat k dispozici v editoru zásad?](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
* [Návody nastavit více prostředí v jednom rozhraní API?](#how-do-i-set-up-multiple-environments-in-a-single-api)
* [Je možné použít protokol SOAP s API Management?](#can-i-use-soap-with-api-management)
* [Můžu nakonfigurovat autorizační Server OAuth 2,0 se zabezpečením AD FS?](#can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security)
* [Jakou metodu směrování používá API Management v nasazeních do více zeměpisných míst?](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
* [Můžu použít šablonu Azure Resource Manager k vytvoření instance služby API Management?](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
* [Můžu pro back-end použít certifikát TLS/SSL podepsaný svým držitelem?](#can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end)
* [Proč se při pokusu o naklonování úložiště GIT zobrazí chyba ověřování?](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
* [Funguje API Management s Azure ExpressRoute?](#does-api-management-work-with-azure-expressroute)
* [Proč vyžadujeme vyhrazenou podsíť ve stylu Správce prostředků virtuální sítě, když do nich API Management nasazená?](#why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them)
* [Jaká je minimální velikost podsítě potřebnou při nasazení API Management do virtuální sítě?](#what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet)
* [Můžu přesunout službu API Management z jednoho předplatného do jiného?](#can-i-move-an-api-management-service-from-one-subscription-to-another)
* [Existují omezení nebo známé problémy při importu rozhraní API?](#are-there-restrictions-on-or-known-issues-with-importing-my-api)

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>Co to znamená, když je funkce ve verzi Preview?
Když je funkce ve verzi Preview, znamená to, že aktivně hledáte zpětnou vazbu o tom, jak funkce funguje. Funkce ve verzi Preview je funkčně dokončená, ale je možné, že v reakci na zpětnou vazbu od zákazníků provedeme zásadní změnu. Doporučujeme, abyste nemuseli záviset na funkci, která je ve verzi Preview v produkčním prostředí.

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>Jak můžu zabezpečit připojení mezi bránou služby API Management a back-endovými službami?
Máte několik možností, jak zabezpečit spojení mezi API Management bránou a vašimi back-end službami. Další možnosti:

* Použijte ověřování HTTP Basic. Další informace najdete v tématu [Import a publikování vašeho prvního rozhraní API](import-and-publish.md).
* Vzájemné ověřování TLS použijte, jak je popsáno v tématu [zabezpečení back-endové služby pomocí ověřování klientského certifikátu v Azure API Management](api-management-howto-mutual-certificates.md).
* Použijte filtrování IP na vaší back-endové službě. Na všech úrovních API Management s výjimkou úrovně spotřeby zůstane IP adresa brány konstantní, s několika upozorněními popsanými v [článku dokumentace k protokolu IP](api-management-howto-ip-addresses.md).
* Připojte svoji instanci API Management k Azure Virtual Network.

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>Jak můžu zkopírovat instanci služby API Management do nové instance?
Máte několik možností, pokud chcete zkopírovat instanci API Management do nové instance. Další možnosti:

* Použijte funkci zálohování a obnovení v API Management. Další informace najdete v tématu [implementace zotavení po havárii pomocí zálohování a obnovení služby v Azure API Management](api-management-howto-disaster-recovery-backup-restore.md).
* Vytvořte vlastní funkci zálohování a obnovení pomocí [REST API API Management](/rest/api/apimanagement/). Použijte REST API k uložení a obnovení entit z instance služby, kterou chcete.
* Stáhněte konfiguraci služby pomocí Gitu a pak ji nahrajte do nové instance. Další informace najdete v tématu [Jak uložit a nakonfigurovat konfiguraci služby API Management pomocí Gitu](api-management-configuration-repository-git.md).

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>Můžu programově spravovat instanci API Management?
Ano, API Management můžete spravovat programově pomocí:

* [REST API API Management](/rest/api/apimanagement/).
* [Sada SDK Microsoft Azure ApiManagement Service Management Library](https://aka.ms/apimsdk).
* Rutiny PowerShellu [pro](/powershell/azure/servicemanagement/overview) [nasazení](/powershell/module/wds) a správu služeb.

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>Jak přidám uživatele do skupiny správců?
Skupiny správců jsou neměnné systémové skupiny. Správci předplatného Azure jsou členy této skupiny. Do této skupiny nemůžete přidat uživatele. Další informace najdete v tématu [Vytvoření a používání skupin pro správu vývojářských účtů v Azure API Management](./api-management-howto-create-groups.md) .

### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>Proč je zásada, kterou chci přidat k dispozici v editoru zásad?
Pokud se zásada, kterou chcete přidat, zobrazuje v editoru zásad šedě nebo je vystínovaná, ujistěte se, že jste ve správném rozsahu pro danou zásadu. Jednotlivé příkazy zásad jsou navržené tak, aby je bylo možné použít v určitých oborech a oddílech zásad. Pokud chcete zkontrolovat oddíly a obory zásad, přečtěte si část použití zásad v tématu [zásady API Management](./api-management-policies.md).

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>Návody nastavit více prostředí v jednom rozhraní API?
K nastavení více prostředí, například testovací prostředí a produkční prostředí, v jednom rozhraní API máte dvě možnosti. Další možnosti:

* Hostování různých rozhraní API na stejném tenantovi.
* Hostovat stejná rozhraní API v různých klientech.

### <a name="can-i-use-soap-with-api-management"></a>Je možné použít protokol SOAP s API Management?
K dispozici je teď podpora [předávacího protokolu SOAP](https://azure.microsoft.com/blog/soap-pass-through/) . Správci mohou importovat WSDL své služby SOAP a Azure API Management vytvoří front-end SOAP. Dokumentace k portálu pro vývojáře, testovací konzola, zásady a analýzy jsou k dispozici pro služby SOAP.

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>Můžu nakonfigurovat autorizační Server OAuth 2,0 se zabezpečením AD FS?
Informace o tom, jak nakonfigurovat autorizační Server OAuth 2,0 se zabezpečením Active Directory Federation Services (AD FS) (AD FS), najdete [v tématu používání služby AD FS v API Management](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/).

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>Jakou metodu směrování používá API Management v nasazeních do více zeměpisných míst?
API Management využívá [metodu směrování provozu výkonu](../traffic-manager/traffic-manager-routing-methods.md#performance) v nasazení do několika geografických umístění. Příchozí provoz se směruje na nejbližší bránu API. Pokud se jedna oblast přepne do režimu offline, příchozí provoz se automaticky směruje na další nejbližší bránu. Přečtěte si další informace o metodách směrování v [Traffic Manager metody směrování](../traffic-manager/traffic-manager-routing-methods.md).

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>Můžu použít šablonu Azure Resource Manager k vytvoření instance služby API Management?
Ano. Další informace najdete v tématu šablony rychlý Start pro [službu Azure API Management](https://aka.ms/apimtemplate) .

### <a name="can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end"></a>Můžu pro back-end použít certifikát TLS/SSL podepsaný svým držitelem?
Ano. To se dá udělat prostřednictvím PowerShellu nebo přímo odeslání do rozhraní API. Tím se zakáže ověřování řetězu certifikátů, které vám umožní používat certifikáty podepsané svým držitelem nebo soukromým podpisem při komunikaci od API Management až po služby back-endu.

#### <a name="powershell-method"></a>PowerShell – metoda ####
Použijte [`New-AzApiManagementBackend`](/powershell/module/az.apimanagement/new-azapimanagementbackend) (pro nový back-end) nebo [`Set-AzApiManagementBackend`](/powershell/module/az.apimanagement/set-azapimanagementbackend) (pro stávající back-end) rutiny PowerShellu a nastavte `-SkipCertificateChainValidation` parametr na `True` .

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

#### <a name="direct-api-update-method"></a>Přímá metoda aktualizace rozhraní API ####
1. Vytvořte [back-end](/rest/api/apimanagement/) entitu pomocí API Management.     
2. Nastavte vlastnost **skipCertificateChainValidation** na **hodnotu true**.     
3. Pokud již nechcete povolené certifikáty podepsané svým držitelem, odstraňte back-end entitu nebo nastavte vlastnost **skipCertificateChainValidation** na **hodnotu false**.

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>Proč při pokusu o klonování úložiště Git dochází k selhání ověřování?
Pokud používáte Git Credential Manager nebo pokud se pokoušíte klonovat úložiště Git pomocí sady Visual Studio, můžete se setkat se známým problémem s dialogovým oknem pověření systému Windows. Dialogové okno omezuje délku hesla na 127 znaků a zkrátí heslo generované společností Microsoft. Pracujeme na zkrácení hesla. Prozatím prosím použijte Git bash k naklonování úložiště Git.

### <a name="does-api-management-work-with-azure-expressroute"></a>Funguje API Management s Azure ExpressRoute?
Ano. API Management funguje se službou Azure ExpressRoute.

### <a name="why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them"></a>Proč vyžadujeme vyhrazenou podsíť ve stylu Správce prostředků virtuální sítě, když do nich API Management nasazená?
Požadavek na vyhrazenou podsíť pro API Management pochází ze skutečnosti, který je postaven na modelu nasazení Classic (PAAS v1 Layer). I když můžeme nasadit do virtuální sítě typu Správce prostředků (v2 Layer), jsou k dispozici důsledky. Model nasazení Classic v Azure není pevně spojený s modelem Správce prostředků, takže pokud vytvoříte prostředek ve vrstvě v2, vrstva v1 o ní neví a problémy, jako je například API Management pokus o použití IP adresy, která je již přidělena síťovému rozhraní (postavené na v2).
Pokud se chcete dozvědět víc o rozdílech mezi klasickými a Správce prostředků modely v Azure, přečtěte si o [rozdílech v modelech nasazení](../azure-resource-manager/management/deployment-models.md).

### <a name="what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet"></a>Jaká je minimální velikost podsítě potřebnou při nasazení API Management do virtuální sítě?
Minimální velikost podsítě potřebná k nasazení API Management je [/29](../virtual-network/virtual-networks-faq.md#configuration), což je minimální velikost podsítě, kterou Azure podporuje.

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>Můžu přesunout službu API Management z jednoho předplatného do jiného?
Ano. Další informace najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/management/move-resource-group-and-subscription.md).

### <a name="are-there-restrictions-on-or-known-issues-with-importing-my-api"></a>Existují omezení nebo známé problémy při importu rozhraní API?
[Známé problémy a omezení](api-management-api-import-restrictions.md) pro Open API (Swagger), formáty WSDL a WADL.
