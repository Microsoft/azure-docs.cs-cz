---
title: Nastavení služby Azure Active Directory pro ověřování klientů
description: Zjistěte, jak nastavit Azure Active Directory (Azure AD) k ověřování klientů pro clustery Service Fabric.
ms.topic: conceptual
ms.date: 6/28/2019
ms.openlocfilehash: 28c4c65cfcc77607dfe9a463a09ecd10389a6eca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78193372"
---
# <a name="set-up-azure-active-directory-for-client-authentication"></a>Nastavení služby Azure Active Directory pro ověřování klientů

Pro clustery spuštěné v Azure se doporučuje Azure Active Directory (Azure AD) zabezpečit přístup ke koncovým bodům správy. Tento článek popisuje, jak nastavit Azure AD k ověření klientů pro cluster Service Fabric.

V tomto článku termín "aplikace" se použije k odkazování na [aplikace Služby Azure Active Directory](../active-directory/develop/developer-glossary.md#client-application), nikoli aplikace Service Fabric; v případě potřeby bude rozlišováno. Azure AD umožňuje organizacím (označované jako tenanty) spravovat přístup uživatelů k aplikacím.

Cluster Service Fabric nabízí několik vstupních bodů pro své funkce správy, včetně webové aplikace [Service Fabric Explorer][service-fabric-visualizing-your-cluster] a sady Visual [Studio][service-fabric-manage-application-in-visual-studio]. V důsledku toho vytvoříte dvě aplikace Azure AD pro řízení přístupu ke clusteru: jedna webová aplikace a jedna nativní aplikace. Po vytvoření aplikací přiřadíte uživatele k rolím jen pro čtení a k rolím správce.

> [!NOTE]
> V systému Linux je nutné před vytvořením clusteru provést následující kroky. V systému Windows máte také možnost [nakonfigurovat ověřování Azure AD pro existující cluster](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/Configure%20Azure%20Active%20Directory%20Authentication%20for%20Existing%20Cluster.md).

> [!NOTE]
> Je [známo,](https://github.com/microsoft/service-fabric/issues/399) že aplikace a uzly v clusterech s podporou AAD Linux nelze zobrazit v Azure Portal.



## <a name="prerequisites"></a>Požadavky
V tomto článku předpokládáme, že jste již vytvořili klienta. Pokud tak nechcete, začněte tím, že si přečtete [jak získat klienta služby Azure Active Directory][active-directory-howto-tenant].

Abychom zjednodušili některé kroky, které se týkají konfigurace azure ad s clusterem Service Fabric, vytvořili jsme sadu skriptů prostředí Windows PowerShell.

1. [Naklonujte repo](https://github.com/Azure-Samples/service-fabric-aad-helpers) do počítače.
2. [Ujistěte se, že máte všechny požadavky](https://github.com/Azure-Samples/service-fabric-aad-helpers#getting-started) na nainstalované skripty.

## <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Vytváření aplikací Azure AD a přiřazování uživatelů k rolím

Pomocí skriptů vytvoříme dvě aplikace Azure AD pro řízení přístupu ke clusteru: jednu webovou aplikaci a jednu nativní aplikaci. Po vytvoření aplikací představujících váš cluster vytvoříte uživatele pro [role podporované service fabricem](service-fabric-cluster-security-roles.md): jen pro čtení a správce.

Spusťte `SetupApplications.ps1`a zadejte jako parametry ID klienta, název clusteru a adresu URL odpovědi webové aplikace.  Zadejte také uživatelská jména a hesla pro uživatele. Například:

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '0e3d2646-78b3-4711-b8be-74a381d9890c' -ClusterName 'mysftestcluster' -WebApplicationReplyUrl 'https://mysftestcluster.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> Pro národní cloudy (například Azure Government, Azure China, `-Location` Azure Germany) byste měli také zadat parametr.

Id *tenanta* můžete najít spuštěním příkazu `Get-AzureSubscription`PowerShell . Spuštění tohoto příkazu zobrazí TenantId pro každé předplatné.

*Název clusteru* se používá k předponě aplikací Azure AD, které jsou vytvořeny skriptem. Není nutné přesně odpovídat názvu skutečného clusteru. Je určena pouze k usnadnění mapování artefaktů Azure AD na cluster Service Fabric, se kterým se používají.

*WebApplicationReplyUrl* je výchozí koncový bod, který Azure AD vrátí uživatelům po dokončení přihlášení. Nastavte tento koncový bod jako koncový bod aplikace Service Fabric Explorer pro váš cluster. Pokud vytváříte aplikace Azure AD představující existující cluster, ujistěte se, že tato adresa URL odpovídá koncovému bodu vašeho stávajícího clusteru. Pokud vytváříte aplikace pro nový cluster, naplánujte koncový bod, který bude mít váš cluster, a ujistěte se, že koncový bod existujícího clusteru nepoužíváte. Ve výchozím nastavení je koncový bod Aplikace Service Fabric Explorer:

https://&lt;&gt;cluster_domain :19080/Explorer

Budete vyzváni k přihlášení k účtu, který má oprávnění správce pro klienta Azure AD. Po přihlášení skript vytvoří webové a nativní aplikace představující cluster Service Fabric. Pokud se podíváte na aplikace klienta na [webu Azure Portal][azure-portal], měli byste vidět dvě nové položky:

   * *ClusterName*\_Cluster Název clusteru
   * *Klient Název_clusteru*\_

Skript vytiskne JSON vyžadovaný šablonou Azure Resource Manager při [vytváření clusteru s povoleným aad](service-fabric-cluster-creation-create-template.md#add-azure-ad-configuration-to-use-azure-ad-for-client-access), takže je vhodné ponechat okno PowerShell otevřené.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>Poradce při potížích s nastavením Azure Active Directory
Nastavení Azure AD a jeho použití může být náročné, takže tady jsou některé ukazatele na to, co můžete udělat pro ladění problému.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Průzkumník Service Fabric vás vyzve k výběru certifikátu
#### <a name="problem"></a>Problém
Po úspěšném přihlášení k Azure AD v aplikaci Service Fabric Explorer se prohlížeč vrátí na domovskou stránku, ale zobrazí se zpráva s výzvou k výběru certifikátu.

![Dialogové okno certifikátu SFX][sfx-select-certificate-dialog]

#### <a name="reason"></a>Důvod
Uživateli není přiřazena role v aplikaci clusteru Azure AD. Ověřování Azure AD se tedy nezdaří v clusteru Service Fabric. Aplikace Service Fabric Explorer se vrátí k ověřování certifikátu.

#### <a name="solution"></a>Řešení
Postupujte podle pokynů pro nastavení Azure AD a přiřadit uživatelské role. Doporučujeme také zapnout "Přiřazení uživatele potřebné pro `SetupApplications.ps1` přístup k aplikaci", stejně jako to dělá.

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>Připojení k prostředí PowerShell se nezdaří s chybou: "Zadaná pověření jsou neplatná"
#### <a name="problem"></a>Problém
Při použití prostředí PowerShell pro připojení ke clusteru pomocí režimu zabezpečení "AzureActiveDirectory" po úspěšném přihlášení do služby Azure AD se připojení nezdaří s chybou: "Zadané přihlašovací údaje jsou neplatné."

#### <a name="solution"></a>Řešení
Toto řešení je stejné jako předchozí.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>Service Fabric Explorer vrátí selhání při přihlášení: "AADSTS50011"
#### <a name="problem"></a>Problém
Při pokusu o přihlášení k Azure AD v aplikaci Service Fabric Explorer, stránka vrátí selhání: &lt;"AADSTS50011: Adresa&gt; url odpovědi neodpovídá odpovědi adresy nakonfigurované pro aplikaci: &lt;guid&gt;."

![Adresa odpovědi SFX se neshoduje][sfx-reply-address-not-match]

#### <a name="reason"></a>Důvod
Cluster (web) aplikace, která představuje Service Fabric Explorer pokusí o ověření proti Azure AD a jako součást požadavku poskytuje adresu URL zpětného přesměrování. Ale adresa URL není uvedena v seznamu **adres URL** odpovědi aplikace Azure AD.

#### <a name="solution"></a>Řešení
Na stránce registrace aplikace Azure AD pro váš cluster vyberte **Ověřování**a v části **Přesměrovat identifikátory URI** přidejte adresu URL aplikace Service Fabric Explorer do seznamu. Uložte si drobné.

![Adresa URL odpovědi webové aplikace][web-application-reply-url]

### <a name="connecting-to-the-cluster-using-azure-ad-authentication-via-powershell-gives-an-error-when-you-sign-in-aadsts50011"></a>Připojení ke clusteru pomocí ověřování Azure AD přes PowerShell dává chybu při přihlášení: "AADSTS50011"
#### <a name="problem"></a>Problém
Při pokusu o připojení ke clusteru Service Fabric pomocí Azure AD přes PowerShell, přihlašovací stránka vrátí selhání: "AADSTS50011: Adresa url odpovědi &lt;zadaná v požadavku neodpovídá adresurl odpovědi nakonfigurovaných pro aplikaci: guid&gt;."

#### <a name="reason"></a>Důvod
Podobně jako předchozí problém se Prostředí PowerShell pokusí ověřit ve službě Azure AD, která poskytuje adresu URL přesměrování, která není uvedená v seznamu **adres URL odpovědí** aplikace Azure AD.  

#### <a name="solution"></a>Řešení
Použijte stejný proces jako v předchozím vydání, ale adresa `urn:ietf:wg:oauth:2.0:oob`URL musí být nastavena na , speciální přesměrování pro ověřování příkazového řádku.

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>Připojení clusteru pomocí ověřování Azure AD přes PowerShell
Chcete-li připojit cluster Service Fabric, použijte následující příklad příkazu PowerShell:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

Další informace naleznete v [tématu Rutina Connect-ServiceFabricCluster](https://docs.microsoft.com/powershell/module/servicefabric/connect-servicefabriccluster).

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>Můžu znovu použít stejného klienta Azure AD ve více clusterech?
Ano. Nezapomeňte však přidat adresu URL aplikace Service Fabric Explorer do clusteru (webové) aplikace. V opačném případě Service Fabric Explorer nefunguje.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Proč stále potřebuji certifikát serveru, když je Azure AD povolená?
FabricClient a FabricGateway provádět vzájemné ověřování. Během ověřování Azure AD integrace Azure AD poskytuje identitu klienta na server a certifikát serveru se používá klienta k ověření identity serveru. Další informace o certifikátech Service Fabric naleznete v [tématu Certifikáty X.509 a Service Fabric][x509-certificates-and-service-fabric].

## <a name="next-steps"></a>Další kroky
Po nastavení aplikací Azure Active Directory a nastavení rolí pro uživatele [nakonfigurujte a nasaďte cluster](service-fabric-cluster-creation-via-arm.md).


<!-- Links -->
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[azure-portal]: https://portal.azure.com/
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric

<!-- Images -->
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-setup-aad/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-setup-aad/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-setup-aad/web-application-reply-url.png
