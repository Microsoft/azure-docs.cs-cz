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
ms.date: 08/15/2018
ms.author: aljo
ms.openlocfilehash: 9b00f6e5c4dbd2fe2c6aab4c62cecc2f2e2640f0
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2018
ms.locfileid: "42057211"
---
# <a name="set-up-azure-active-directory-for-client-authentication"></a>Nastavení Azure Active Directory pro ověřování klientů

Pro clustery spuštěné v Azure Azure Active Directory (Azure AD) se doporučuje pro zabezpečení přístupu ke koncovým bodům správy.  Tento článek popisuje, jak se nastavení Azure AD k ověřování klientů pro cluster Service Fabric, které je třeba provést před [vytváření clusteru](service-fabric-cluster-creation-via-arm.md).  Azure AD umožňuje organizacím (označuje se jako tenantů) ke správě přístupu uživatelů k aplikacím. Aplikace se dělí na ty, které mají webové přihlašovacího uživatelského rozhraní a ty, které mají nativní klientské prostředí. V tomto článku předpokládáme, že jste již vytvořili tenanta. Pokud ne, začněte tím, že čtení [získání tenanta služby Azure Active Directory][active-directory-howto-tenant].

## <a name="create-azure-ad-applications"></a>Vytvoření aplikace Azure AD
Cluster Service Fabric nabízí několik vstupních bodů do jeho funkce správy, včetně webová [Service Fabric Explorer] [ service-fabric-visualizing-your-cluster] a [sady Visual Studio] [ service-fabric-manage-application-in-visual-studio]. Proto vytvoříte dvě aplikace Azure AD pro řízení přístupu ke clusteru: jeden webové aplikace a jedné nativní aplikace.  Po vytvoření aplikace můžete přiřadit uživatele jen pro čtení a role správce.

Pro zjednodušení některé kroky při konfiguraci Azure AD s clusterem Service Fabric, vytvořili jsme sadu skriptů prostředí Windows PowerShell.

> [!NOTE]
> Před vytvořením clusteru, musíte dokončit následující kroky. Vzhledem k tomu, že skripty očekávají názvů clusterů a koncových bodů, hodnoty plánování byste měli využít a ne hodnoty, že jste již vytvořili.

1. [Stáhněte si skripty] [ sf-aad-ps-script-download] k vašemu počítači.
2. Klikněte pravým tlačítkem na soubor zip, vyberte **vlastnosti**, vyberte **Odblokovat** zaškrtněte políčko a potom klikněte na tlačítko **použít**.
3. Rozbalte soubor zip.
4. Spustit `SetupApplications.ps1`a zadejte ID Tenanta, název clusteru a WebApplicationReplyUrl jako parametry. Příklad:

```PowerShell
.\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'
```

> [!NOTE]
> Pro národní cloudy (Azure Government, Azure China, Azure Germany), by měly také určit `-Location` parametru.

Vaše ID Tenanta zjistíte spuštěním příkazu Powershellu `Get-AzureSubscription`. Spuštění tohoto příkazu se zobrazí ID Tenanta pro každé předplatné.

Název clusteru je používat jako předpona aplikací v Azure AD, které jsou vytvořeny skriptem. Nemusí přesně odpovídat skutečný název clusteru. Je určena pouze k usnadňují mapovat do clusteru Service Fabric, který se právě používají s artefakty Azure AD.

WebApplicationReplyUrl je výchozí koncový bod, který Azure AD vrací uživatelům po jejich dokončení přihlašování. Nastavte jako koncový bod Service Fabric Exploreru pro váš cluster, který ve výchozím nastavení je tento koncový bod:

https://&lt;cluster_domain&gt;:19080/Explorer

Zobrazí se výzva k přihlášení k účtu, který má oprávnění správce pro tenanta Azure AD. Po přihlášení, skript vytvoří webové a nativní aplikace pro reprezentaci vašeho clusteru Service Fabric. Když se podíváte na aplikace vašeho tenanta v [webu Azure portal][azure-portal], měli byste vidět dvě nové položky:

   * *Název clusteru*\_clusteru
   * *Název clusteru*\_klienta

Skript vypíše soubor JSON potřebný pomocí šablony Azure Resource Manageru při vytvoření clusteru v další části, takže je vhodné ponechat otevřené okno Powershellu.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

<a name="assign-roles"></a>

## <a name="assign-users-to-roles"></a>Přiřazení uživatelů k rolím
Po vytvoření aplikace, které chcete cluster představují přiřazení uživatele k rolím nepodporuje Service Fabric: jen pro čtení a správce. Pomocí můžete přiřadit role [webu Azure portal][azure-portal].

1. Na webu Azure Portal vyberte v pravém horním rohu vašeho tenanta.

    ![Vyberte tlačítko tenanta][select-tenant-button]
2. Vyberte **Azure Active Directory** na levé kartě a poté vyberte možnost "podnikové aplikace".
3. Vyberte "Všechny aplikace" a pak vyhledejte a vyberte webovou aplikaci, která má název například `myTestCluster_Cluster`.
4. Klikněte na tlačítko **uživatelů a skupin** kartu.

    ![Kartu uživatelé a skupiny][users-and-groups-tab]
5. Klikněte na tlačítko **přidat uživatele** tlačítko Nová stránka, vyberte uživatele a roli chcete přiřadit a potom klikněte na tlačítko **vyberte** tlačítko v dolní části stránky.

    ![Přiřadit uživatele na stránku rolí][assign-users-to-roles-page]
6. Klikněte na tlačítko **přiřadit** tlačítko v dolní části stránky.

    ![Přidat přiřazení potvrzení][assign-users-to-roles-confirm]

> [!NOTE]
> Další informace o rolích v Service Fabric najdete v tématu [řízení přístupu na základě rolí pro Service Fabric klienty](service-fabric-cluster-security-roles.md).
>
>

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

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>Připojení pomocí prostředí PowerShell se nezdaří s chybou: "zadané přihlašovací údaje jsou neplatné."
#### <a name="problem"></a>Problém
Při použití prostředí PowerShell pro připojení ke clusteru pomocí "AzureActiveDirectory" režim zabezpečení, po přihlášení úspěšně do služby Azure AD, připojení se nezdaří s chybou: "zadané přihlašovací údaje jsou neplatné."

#### <a name="solution"></a>Řešení
Toto řešení je stejná jako ta předchozí.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>Service Fabric Explorer vrátí chybu při přihlášení: "AADSTS50011"
#### <a name="problem"></a>Problém
Při pokusu o přihlášení k Azure AD v Service Fabric Exploreru na stránce vrátí chybu: "AADSTS50011: adresa pro odpovědi &lt;url&gt; neodpovídá adresám pro odpovědi nakonfigurovaným pro aplikaci: &lt;guid&gt;."

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
[select-tenant-button]: ./media/service-fabric-cluster-creation-setup-aad/select-tenant-button.png
[users-and-groups-tab]: ./media/service-fabric-cluster-creation-setup-aad/users-and-groups-tab.png
[assign-users-to-roles-page]: ./media/service-fabric-cluster-creation-setup-aad/assign-users-to-roles-page.png
[assign-users-to-roles-confirm]: ./media/service-fabric-cluster-creation-setup-aad/assign-users-to-roles-confirm.png
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-setup-aad/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-setup-aad/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-setup-aad/web-application-reply-url.png
