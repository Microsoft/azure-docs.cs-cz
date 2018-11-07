---
title: Azure API Management – nejčastější dotazy | Dokumentace Microsoftu
description: Přečtěte si odpovědi na nejčastější dotazy (FAQ), vzorů a doporučené postupy ve službě Azure API Management.
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
ms.openlocfilehash: 760feae2c9b58e162dae487e240dda72099ed91b
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51227973"
---
# <a name="azure-api-management-faqs"></a>Nejčastější dotazy k Azure API Management
Získejte odpovědi na běžné dotazy, vzory a osvědčené postupy pro službu Azure API Management.

## <a name="contact-us"></a>Kontaktujte nás
* [Jak můžu pokládat ve službě Microsoft Azure API Management team dotaz?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question)

## <a name="frequently-asked-questions"></a>Nejčastější dotazy
* [Co to znamená, je-je funkce ve verzi preview?](#what-does-it-mean-when-a-feature-is-in-preview)
* [Jak můžu zabezpečit připojení mezi bránou služby API Management a back-endovými službami?](#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services)
* [Jak můžu zkopírovat Moje instance služby API Management do nové instance?](#how-do-i-copy-my-api-management-service-instance-to-a-new-instance)
* [Můžete spravovat své instance služby API Management prostřednictvím kódu programu?](#can-i-manage-my-api-management-instance-programmatically)
* [Jak přidat uživatele do skupiny Administrators?](#how-do-i-add-a-user-to-the-administrators-group)
* [Proč se zásadami, které chcete přidat k dispozici v editoru zásad?](#why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor)
* [Jak můžu nastavit více prostředí v jediné rozhraní API?](#how-do-i-set-up-multiple-environments-in-a-single-api)
* [Můžete použít protokol SOAP službou API Management?](#can-i-use-soap-with-api-management)
* [Je konstanta IP adresa brány API Management? Můžete použít ho v pravidlech brány firewall?](#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules)
* [Můžete nakonfigurovat serveru ověřování OAuth 2.0 se zabezpečení služby AD FS?](#can-i-configure-an-oauth-20-authorization-server-with-adfs-security)
* [V nasazení do několika geografických umístěních jaké metody směrování používá rozhraní API Management?](#what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations)
* [Můžete použít šablony Azure Resource Manageru k vytvoření instance služby API Management?](#can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance)
* [Můžete použít certifikát podepsaný svým držitelem SSL pro back-end?](#can-i-use-a-self-signed-ssl-certificate-for-a-back-end)
* [Proč se při klonování úložiště GIT získat chybu ověřování?](#why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository)
* [Služba API Management funguje s Azure ExpressRoute?](#does-api-management-work-with-azure-expressroute)
* [Proč jsme vyžaduje vyhrazenou podsíť ve stylu virtuálním sítím Resource Manageru, když API Management se nasadí do nich?](#why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them)
* [Jaká je velikost podsítě minimální potřebné při nasazování API Management do virtuální sítě?](#what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet)
* [Můžu přesunout služby API Management z jednoho předplatného do druhého?](#can-i-move-an-api-management-service-from-one-subscription-to-another)
* [Existují omezení nebo známé problémy s importem Moje rozhraní API?](#are-there-restrictions-on-or-known-issues-with-importing-my-api)

### <a name="how-can-i-ask-the-microsoft-azure-api-management-team-a-question"></a>Jak můžu pokládat ve službě Microsoft Azure API Management team dotaz?
Kontaktujte nás pomocí jedné z těchto možností:

* Zveřejněte své dotazy v našich [fóru pro API Management MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt).
* Odešlete email na adresu <mailto:apimgmt@microsoft.com>.
* Nám pošlete žádost o funkci [fóru názorů na Azure](https://feedback.azure.com/forums/248703-api-management).

### <a name="what-does-it-mean-when-a-feature-is-in-preview"></a>Co to znamená, je-je funkce ve verzi preview?
Když je funkce ve verzi preview, znamená to, že nám budete aktivně hledání zpětnou vazbu na fungování funkce pro vás. Funkce ve verzi preview je funkčně úplný, ale je možné, že teď uděláme rozbíjející změny v reakci na zpětnou vazbu od zákazníků. Doporučujeme vám, že nejsou závislé na funkci, která je ve verzi preview v produkčním prostředí. Pokud máte nějakou zpětnou vazbu na funkce ve verzi preview, dejte nám vědět, prostřednictvím jednoho z možností kontaktu v [Jak můžu pokládat ve službě Microsoft Azure API Management team dotaz?](#how-can-i-ask-the-microsoft-azure-api-management-team-a-question).

### <a name="how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services"></a>Jak lze zabezpečit připojení mezi bránou rozhraní API Management a Moje back endovým službám?
Máte několik možností, jak zabezpečit připojení mezi bránou rozhraní API Management a back endové služby. Můžete:

* Základní ověřování pomocí protokolu HTTP. Další informace najdete v tématu [Import a publikování vašeho prvního rozhraní API](import-and-publish.md).
* Používá vzájemné ověřování SSL, jak je popsáno v [zabezpečení back endovým službám pomocí klienta ověření certifikátu ve službě Azure API Management](api-management-howto-mutual-certificates.md).
* Použijte na seznam povolených IP na back-end služby. Ve všech úrovních služby API Management, IP adresu brány konstantní, s několika [upozornění](#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules). Můžete nastavit vašeho seznamu povolených IP adres, chcete-li povolit tuto IP adresu. Na řídicím panelu na webu Azure Portal můžete získat IP adresu vaší instance služby API Management.
* Vaše instance služby API Management se připojte ke službě Azure Virtual Network.

### <a name="how-do-i-copy-my-api-management-service-instance-to-a-new-instance"></a>Jak můžu zkopírovat Moje instance služby API Management do nové instance?
Máte několik možností, pokud chcete zkopírovat do nové instance instance služby API Management. Můžete:

* Pomocí zálohování a obnovení funkce ve službě API Management. Další informace najdete v tématu [implementovat zotavení po havárii pomocí služby zálohování a obnovení ve službě Azure API Management](api-management-howto-disaster-recovery-backup-restore.md).
* Vytvořit vlastní zálohu a obnovení funkce pomocí [REST API služby API Management](https://msdn.microsoft.com/library/azure/dn776326.aspx). K uložení a obnovení entity z instance služby, který chcete použijte rozhraní REST API.
* Stáhnout konfiguraci služby pomocí Git a pak ho nahrajte do nové instance. Další informace najdete v tématu [k uložení a konfigurace konfigurace služby API Management s použitím Gitu](api-management-configuration-repository-git.md).

### <a name="can-i-manage-my-api-management-instance-programmatically"></a>Můžete spravovat své instance služby API Management prostřednictvím kódu programu?
Ano, API Management prostřednictvím kódu programu spravovat pomocí:

* [Rozhraní REST API služby API Management](https://msdn.microsoft.com/library/azure/dn776326.aspx).
* [Knihovna správy ApiManagement služby Microsoft Azure SDK](https://aka.ms/apimsdk).
* [Nasazení služby](https://docs.microsoft.com/powershell/module/wds) a [Správa služeb](https://docs.microsoft.com/powershell/azure/servicemanagement/overview) rutin prostředí PowerShell.

### <a name="how-do-i-add-a-user-to-the-administrators-group"></a>Jak přidat uživatele do skupiny Administrators?
Zde je, jak můžete přidat uživatele do skupiny správců:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Přejděte do skupiny prostředků, který má instance služby API Management, kterou chcete aktualizovat.
3. Ve službě API Management, přiřaďte **Přispěvatel správy rozhraní Api** role pro uživatele.

Teď nově přidané Přispěvatel můžete použít Azure PowerShell [rutiny](https://docs.microsoft.com/powershell/azure/overview). Tady je postup přihlášení jako správce:

1. Použití `Connect-AzureRmAccount` rutiny pro přihlášení.
2. Nastavte kontext předplatného, který má služba s využitím `Set-AzureRmContext -SubscriptionID <subscriptionGUID>`.
3. Získat adresu URL jednotné přihlašování s použitím `Get-AzureRmApiManagementSsoToken -ResourceGroupName <rgName> -Name <serviceName>`.
4. Použijte adresu URL pro přístup k portálu pro správu.

### <a name="why-is-the-policy-that-i-want-to-add-unavailable-in-the-policy-editor"></a>Proč se zásadami, které chcete přidat k dispozici v editoru zásad?
Pokud se zásadami, které chcete přidat aktivní nebo stínované v editoru zásad, ujistěte se, že jste ve správném oboru zásady. Každý prohlášení o zásadách je určen pro použití v určité obory a části zásad. Zkontrolujte zásady oddíly a obory pro zásady, najdete v tématu Zásady využití [zásady služby API Management](https://msdn.microsoft.com/library/azure/dn894080.aspx).

### <a name="how-do-i-set-up-multiple-environments-in-a-single-api"></a>Jak můžu nastavit více prostředí v jediné rozhraní API?
K nastavení více prostředí, například testovací prostředí a produkční prostředí, v jediné rozhraní API, máte dvě možnosti. Můžete:

* Hostitel různých rozhraní API na stejném tenantovi.
* Hostování stejná rozhraní API na různých tenantech.

### <a name="can-i-use-soap-with-api-management"></a>Můžete použít protokol SOAP službou API Management?
[Průchod SOAP](https://blogs.msdn.microsoft.com/apimanagement/2016/10/13/soap-pass-through/) je teď dostupná podpora. Správci mohou importovat WSDL jejich službu SOAP a Azure API Management se vytvoří front-endu SOAP. Dokumentaci k portálu pro vývojáře, testovací konzole, zásady a analytics jsou všechny dostupné služby SOAP.

### <a name="is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules"></a>Je konstanta IP adresa brány API Management? Můžete použít ho v pravidlech brány firewall?
Ve všech úrovních služby API Management veřejnou IP adresu (VIP) tenanta služby API Management je statická po dobu životnosti tenanta, s několika výjimkami. Změny IP adresy za těchto okolností:

* Služba je odstranit a znovu vytvořili.
* Předplatné služby [pozastaveno](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) nebo [upozornění](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/subscription-lifecycle-api-reference.md#subscription-states) (například nezaplacení) a potom obnoveny.
* Přidat nebo odebrat virtuální sítě Azure (virtuální sítě můžete použít pouze na vývojáře a úroveň Premium).

Pro nasazení ve více oblastech, místní adresa se změní, když je oblast uvolněné a potom finančních (nasazení v různých oblastech lze použít pouze na úrovni Premium).

Tenanti úrovně Premium, které jsou nakonfigurované pro nasazení v různých oblastech jsou přiřazeny jednu veřejnou IP adresu v jedné oblasti.

Na stránce tenanta na portálu Azure můžete získat IP adresu (nebo adresy v nasazení ve více oblastech).

### <a name="can-i-configure-an-oauth-20-authorization-server-with-ad-fs-security"></a>Můžete nakonfigurovat serveru ověřování OAuth 2.0 se zabezpečení služby AD FS?
Další informace o konfiguraci serveru autorizace OAuth 2.0 se zabezpečením služby Active Directory Federation Services (AD FS), najdete v článku [pomocí služby AD FS ve službě API Management](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/).

### <a name="what-routing-method-does-api-management-use-in-deployments-to-multiple-geographic-locations"></a>V nasazení do několika geografických umístěních jaké metody směrování používá rozhraní API Management?
API Management použije [metodu směrování provozu výkonu](../traffic-manager/traffic-manager-routing-methods.md#performance) v nasazení do několika geografických umístěních. Příchozí provoz se směruje na nejbližší Brána rozhraní API. Pokud jedna oblast přejde do režimu offline, příchozí provoz automaticky směrují na nejbližší další brány. Další informace o metodách směrování v [metody směrování Traffic Manageru](../traffic-manager/traffic-manager-routing-methods.md).

### <a name="can-i-use-an-azure-resource-manager-template-to-create-an-api-management-service-instance"></a>Můžete použít šablony Azure Resource Manageru k vytvoření instance služby API Management?
Ano. Zobrazit [služby Azure API Management](https://aka.ms/apimtemplate) šablony pro rychlý start.

### <a name="can-i-use-a-self-signed-ssl-certificate-for-a-back-end"></a>Můžete použít certifikát podepsaný svým držitelem SSL pro back-end?
Ano. To lze provést pomocí Powershellu nebo přímo, odešlete do rozhraní API. To zakáže ověřování řetězu certifikátů a vám umožní použít certifikáty podepsané svým držitelem nebo soukromě podepsané při komunikaci ze služby API Management se back-end služby.

#### <a name="powershell-method"></a>Metoda využívající PowerShell ####
Použití [ `New-AzureRmApiManagementBackend` ](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/new-azurermapimanagementbackend) (pro nový back-end) nebo [ `Set-AzureRmApiManagementBackend` ](https://docs.microsoft.com/powershell/module/azurerm.apimanagement/set-azurermapimanagementbackend) (pro existující back-end) rutiny prostředí PowerShell a nastavte `-SkipCertificateChainValidation` parametr `True`. 

```
$context = New-AzureRmApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzureRmApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

#### <a name="direct-api-update-method"></a>Přímé metody aktualizace rozhraní API ####
1. Vytvoření [back-endu](https://msdn.microsoft.com/library/azure/dn935030.aspx) entity pomocí služby API Management.       
2. Nastavte **skipCertificateChainValidation** vlastnost **true**.     
3. Pokud už nechcete povolit certifikáty podepsané svým držitelem, odstraňte entity back-end, nebo nastavte **skipCertificateChainValidation** vlastnost **false**.

### <a name="why-do-i-get-an-authentication-failure-when-i-try-to-clone-a-git-repository"></a>Proč se při klonování úložiště Git získat chybu ověřování?
Pokud používáte Git Credential Manageru nebo pokud se snažíte naklonujte úložiště Git s použitím sady Visual Studio, můžete narazit na známý problém s dialogovým oknem přihlašovací údaje Windows. Dialogové okno omezení délky hesla na 127 znaků a zkrátí Microsoft vytvořené heslo. Pracujeme na zkrácení heslo. Prozatím použijte Git Bash a klonování úložiště Git.

### <a name="does-api-management-work-with-azure-expressroute"></a>Služba API Management funguje s Azure ExpressRoute?
Ano. API Management pracuje s Azure ExpressRoute.

### <a name="why-do-we-require-a-dedicated-subnet-in-resource-manager-style-vnets-when-api-management-is-deployed-into-them"></a>Proč jsme vyžaduje vyhrazenou podsíť ve stylu virtuálním sítím Resource Manageru, když API Management se nasadí do nich?
Požadavek na vyhrazenou podsíť pro službu API Management vychází ze skutečnosti, který je založený na modelu nasazení Classic (vrstvu PAAS V1). Zatímco můžeme nasadit do virtuální sítě Resource Manageru (verze 2 layer), důsledky, které již existují. Model nasazení Classic do Azure není pevně pomocí modelu Resource Manager a proto pokud vytvoříte prostředek ve vrstvě V2, o něm neví, vrstvě V1 a může dojít problémům, jako je API Management došlo k pokusu o použití IP adresy, která je už přidělená k síťové KARTĚ  (založená na V2).
Přečtěte si další informace o rozdílu modely Classic a Resource Manageru v Azure najdete v tématu [rozdíl v modelech nasazení](../azure-resource-manager/resource-manager-deployment-model.md).

### <a name="what-is-the-minimum-subnet-size-needed-when-deploying-api-management-into-a-vnet"></a>Jaká je velikost podsítě minimální potřebné při nasazování API Management do virtuální sítě?
Velikost minimální podsítě, které jsou potřebné k nasazení služby API Management je [/29](../virtual-network/virtual-networks-faq.md#configuration), což je velikost minimální podsítě, které Azure podporuje.

### <a name="can-i-move-an-api-management-service-from-one-subscription-to-another"></a>Můžu přesunout služby API Management z jednoho předplatného do druhého?
Ano. Další informace o postupu [přesunutí prostředků do nové skupiny prostředků nebo předplatného](../azure-resource-manager/resource-group-move-resources.md).

### <a name="are-there-restrictions-on-or-known-issues-with-importing-my-api"></a>Existují omezení nebo známé problémy s importem Moje rozhraní API?
[Známé problémy a omezení](api-management-api-import-restrictions.md) otevřít API(Swagger) WSDL a WADL formáty.
