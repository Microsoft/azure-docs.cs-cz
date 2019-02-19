---
title: Nastavení Azure Active Directory pro ověřování klientů Service Fabric | Dokumentace Microsoftu
description: Zjistěte, jak nastavit službu Azure Active Directory (Azure AD) k ověřování klientů pro clustery Service Fabric.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/15/2019
ms.author: aljo
ms.openlocfilehash: 15561969e27512c4882eccc10f75aa932bcf23df
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2019
ms.locfileid: "56338984"
---
# <a name="set-up-azure-active-directory-for-client-authentication"></a>Nastavení Azure Active Directory pro ověřování klientů

Pro clustery spuštěné v Azure Azure Active Directory (Azure AD) se doporučuje pro zabezpečení přístupu ke koncovým bodům správy.  Tento článek popisuje, jak se nastavení Azure AD k ověřování klientů pro cluster Service Fabric, které je třeba provést před [vytváření clusteru](service-fabric-cluster-creation-via-arm.md).  Azure AD umožňuje organizacím (označuje se jako tenantů) ke správě přístupu uživatelů k aplikacím. Aplikace se dělí na ty, které mají webové přihlašovacího uživatelského rozhraní a ty, které mají nativní klientské prostředí. 

Cluster Service Fabric nabízí několik vstupních bodů do jeho funkce správy, včetně webová [Service Fabric Explorer] [ service-fabric-visualizing-your-cluster] a [sady Visual Studio] [ service-fabric-manage-application-in-visual-studio]. Proto vytvoříte dvě aplikace Azure AD pro řízení přístupu ke clusteru: jeden webové aplikace a jedné nativní aplikace.  Po vytvoření aplikace můžete přiřadit uživatele jen pro čtení a role správce.

> [!NOTE]
> Před vytvořením clusteru, musíte dokončit následující kroky. Vzhledem k tomu, že skripty očekávají názvů clusterů a koncových bodů, hodnoty plánování byste měli využít a ne hodnoty, že jste již vytvořili.

## <a name="prerequisites"></a>Požadavky
V tomto článku předpokládáme, že jste již vytvořili tenanta. Pokud ne, začněte tím, že čtení [získání tenanta služby Azure Active Directory][active-directory-howto-tenant].

Pro zjednodušení některé kroky při konfiguraci Azure AD s clusterem Service Fabric, vytvořili jsme sadu skriptů prostředí Windows PowerShell.

1. [Stáhněte si skripty](https://github.com/robotechredmond/Azure-PowerShell-Snippets/tree/master/MicrosoftAzureServiceFabric-AADHelpers/AADTool) k vašemu počítači.
2. Klikněte pravým tlačítkem na soubor zip, vyberte **vlastnosti**, vyberte **Odblokovat** zaškrtněte políčko a potom klikněte na tlačítko **použít**.
3. Rozbalte soubor zip.

## <a name="create-azure-ad-applications-and-asssign-users-to-roles"></a>Vytvoření aplikace Azure AD a asssign uživatelů k rolím
Vytvořit dvě aplikace Azure AD pro řízení přístupu ke clusteru: jeden webové aplikace a jedné nativní aplikace. Po vytvoření aplikace pro reprezentaci clusteru přiřadit uživatelům, aby [role podporuje Service Fabric](service-fabric-cluster-security-roles.md): jen pro čtení a správce.

Spustit `SetupApplications.ps1`a zadat tenanta jako parametry ID, název clusteru a adresy URL odpovědi webové aplikace.  Také zadejte uživatelská jména a hesla pro uživatele.  Příklad:

```PowerShell
$Configobj = .\SetupApplications.ps1 -TenantId '0e3d2646-78b3-4711-b8be-74a381d9890c' -ClusterName 'mysftestcluster' -WebApplicationReplyUrl 'https://mysftestcluster.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> Pro národní cloudy (například Azure Government, Azure China, Azure Germany), by měly také určit `-Location` parametru.

Můžete najít vaše *TenantId* spuštěním příkazu Powershellu `Get-AzureSubscription`. Spuštění tohoto příkazu se zobrazí ID Tenanta pro každé předplatné.

*Název clusteru* používat jako předpona aplikací v Azure AD, které jsou vytvořeny skriptem. Nemusí přesně odpovídat skutečný název clusteru. Je určena pouze k usnadňují mapovat do clusteru Service Fabric, který se právě používají s artefakty Azure AD.

*WebApplicationReplyUrl* je výchozí koncový bod, který Azure AD vrací uživatelům po jejich dokončení přihlašování. Nastavte jako koncový bod Service Fabric Exploreru pro váš cluster, který ve výchozím nastavení je tento koncový bod:

https://&lt;cluster_domain&gt;:19080/Explorer

Zobrazí se výzva k přihlášení k účtu, který má oprávnění správce pro tenanta Azure AD. Po přihlášení, skript vytvoří webové a nativní aplikace pro reprezentaci vašeho clusteru Service Fabric. Když se podíváte na aplikace vašeho tenanta v [webu Azure portal][azure-portal], měli byste vidět dvě nové položky:

   * *Název clusteru*\_clusteru
   * *Název clusteru*\_klienta

Skript vypíše soubor JSON potřebný pomocí šablony Azure Resource Manageru při vám [vytvořit cluster](service-fabric-cluster-creation-create-template.md#add-azure-ad-configuration-to-use-azure-ad-for-client-access), takže je vhodné ponechat otevřené okno Powershellu.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>Poradce při potížích v nastavení Azure Active Directory
Nastavení Azure AD a jeho použití může být náročné, takže tady je několik tipů, na co můžete dělat k ladění těchto potíží.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer vyzve k výběru certifikátu
#### <a name="problem"></a>Problém
Po přihlášení úspěšně do služby Azure AD v Service Fabric Explorer, prohlížeč se vrátí na domovskou stránku, ale zobrazí se výzva k výběru certifikátu.

![Dialogové okno certifikát SFX][sfx-select-certificate-dialog]

#### <a name="reason"></a>Důvod
Uživatel není přiřazena role v clusteru aplikaci Azure AD. Ověřování Azure AD díky tomu se nezdaří v clusteru Service Fabric. Service Fabric Explorer spadne zpět na ověření certifikátu.

#### <a name="solution"></a>Řešení
Postupujte podle pokynů pro nastavení služby Azure AD a přiřadit role uživatele. Taky doporučujeme zapnout "Přiřazení uživatelů povinné pro přístup k aplikaci," jako `SetupApplications.ps1` nepodporuje.

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>Připojení pomocí prostředí PowerShell se nezdaří s chybou: "Zadané přihlašovací údaje jsou neplatné."
#### <a name="problem"></a>Problém
Při použití prostředí PowerShell pro připojení ke clusteru pomocí "AzureActiveDirectory" režim zabezpečení, po přihlášení úspěšně do služby Azure AD, připojení se nezdaří s chybou: "Zadané přihlašovací údaje jsou neplatné."

#### <a name="solution"></a>Řešení
Toto řešení je stejná jako ta předchozí.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>Service Fabric Explorer vrátí chybu při přihlášení: "AADSTS50011"
#### <a name="problem"></a>Problém
Při pokusu o přihlášení k Azure AD v Service Fabric Exploreru na stránce vrátí chybu: "AADSTS50011: Adresa pro odpovědi &lt;url&gt; neodpovídá adresám pro odpovědi nakonfigurovaným pro aplikaci: &lt;guid&gt;. "

![Adresa pro odpovědi SFX neodpovídá.][sfx-reply-address-not-match]

#### <a name="reason"></a>Důvod
Aplikace clusteru (web), který představuje Service Fabric Exploreru pokusí ověřit ve službě Azure AD a jako součást požadavku poskytuje návratová adresa URL přesměrování. Adresa URL není uvedena v aplikaci Azure AD, ale **adresy URL odpovědi** seznamu.

#### <a name="solution"></a>Řešení
Vyberte "Registrace aplikací" na stránce AAD, vyberte svou aplikaci clusteru a pak vyberte **adresy URL odpovědí** tlačítko. Na stránce "Adresy URL odpovědí" adresa URL nástroje Service Fabric Exploreru přidejte do seznamu nebo nahradit jednu z položek v seznamu. Jakmile budete hotovi, uložte změnu.

![Adresa url odpovědi webové aplikace][web-application-reply-url]

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>Připojení ke clusteru pomocí ověřování Azure AD prostřednictvím prostředí PowerShell
Pro připojení ke clusteru Service Fabric, použijte následující ukázkový příkaz Powershellu:

```PowerShell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

Další informace najdete v tématu [rutina Connect-ServiceFabricCluster](https://docs.microsoft.com/powershell/module/servicefabric/connect-servicefabriccluster).

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>Můžete znovu použít stejným tenantem Azure AD v několika clusterech?
Ano. Ale nezapomeňte přidat adresu URL nástroje Service Fabric Exploreru pro vaši aplikaci clusteru (web). V opačném případě se Service Fabric Exploreru nefunguje.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Proč musím i certifikát serveru služby Azure AD je povolen?
FabricClient a FabricGateway provedeno vzájemné ověření. Během ověřování Azure AD integrace služby Azure AD poskytuje identitu klienta na server a server certifikát se používá k ověření identity serveru. Další informace o certifikátech pro Service Fabric najdete v tématu [certifikáty X.509 a Service Fabric][x509-certificates-and-service-fabric].

## <a name="next-steps"></a>Další postup
Po nastavení aplikace Azure Active Directory a nastavení role pro uživatele, [nakonfigurujte a nasaďte cluster](service-fabric-cluster-creation-via-arm.md).


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
