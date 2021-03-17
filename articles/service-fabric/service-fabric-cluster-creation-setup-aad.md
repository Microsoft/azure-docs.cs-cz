---
title: Nastavit Azure Active Directory pro ověřování klientů
description: Přečtěte si, jak nastavit Azure Active Directory (Azure AD) pro ověřování klientů pro Service Fabric clustery.
ms.topic: conceptual
ms.date: 6/28/2019
ms.openlocfilehash: a1f89e144f9cef12c5bff87befb00a88bad8d7d9
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102215965"
---
# <a name="set-up-azure-active-directory-for-client-authentication"></a>Nastavit Azure Active Directory pro ověřování klientů

Pro clustery běžící v Azure se doporučuje Azure Active Directory (Azure AD) pro zabezpečení přístupu ke koncovým bodům správy. Tento článek popisuje, jak nastavit Azure AD pro ověřování klientů pro cluster Service Fabric.

V tomto článku se termín "aplikace" použije pro odkazování na [Azure Active Directory aplikace](../active-directory/develop/developer-glossary.md#client-application), ne pro Service Fabric aplikace. v případě potřeby se rozlišení provede. Azure AD umožňuje organizacím (označovaným jako klienti) spravovat přístup uživatelů k aplikacím.

Cluster Service Fabric nabízí několik vstupních bodů ke svým funkcím správy, včetně webových [Service Fabric Explorer][service-fabric-visualizing-your-cluster] a sady [Visual Studio][service-fabric-manage-application-in-visual-studio]. V důsledku toho vytvoříte dvě aplikace Azure AD pro řízení přístupu ke clusteru: jednu webovou aplikaci a jednu nativní aplikaci. Po vytvoření aplikací přiřadíte uživatele k rolím jen pro čtení a správcům.

> [!NOTE]
> V systému Linux je před vytvořením clusteru nutné provést následující kroky. Ve Windows máte také možnost [nakonfigurovat ověřování Azure AD pro existující cluster](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/Configure%20Azure%20Active%20Directory%20Authentication%20for%20Existing%20Cluster.md).

> [!NOTE]
> Jedná se o [známý problém](https://github.com/microsoft/service-fabric/issues/399) , že aplikace a uzly na clusterech s podporou AAD na platformě Linux nejde zobrazit na webu Azure Portal.



## <a name="prerequisites"></a>Požadavky
V tomto článku předpokládáme, že jste už tenanta vytvořili. Pokud ne, začněte tím, že si přečtete, [Jak získat klienta Azure Active Directory][active-directory-howto-tenant].

Abychom zjednodušili některé kroky týkající se konfigurace služby Azure AD pomocí Service Fabricho clusteru, vytvořili jsme sadu skriptů prostředí Windows PowerShell.

1. [Naklonujte úložiště](https://github.com/Azure-Samples/service-fabric-aad-helpers) do počítače.
2. [Ujistěte se, že máte všechny požadavky](https://github.com/Azure-Samples/service-fabric-aad-helpers#getting-started) na nainstalované skripty.

## <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Vytváření aplikací Azure AD a přiřazení uživatelů k rolím

Pomocí skriptů vytvoříme dvě aplikace Azure AD pro řízení přístupu ke clusteru: jednu webovou aplikaci a jednu nativní aplikaci. Po vytvoření aplikací, které reprezentují váš cluster, vytvoříte uživatele pro [role podporované Service Fabric](service-fabric-cluster-security-roles.md): jen pro čtení a správce.

Spusťte `SetupApplications.ps1` příkaz a jako parametry zadejte ID klienta, název clusteru a adresu URL odpovědi webové aplikace.  Zadejte také uživatelská jména a hesla pro uživatele. Například:

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '0e3d2646-78b3-4711-b8be-74a381d9890c' -ClusterName 'mysftestcluster' -WebApplicationReplyUrl 'https://mysftestcluster.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> Pro národní cloudy (například Azure Government, Azure Čína, Azure Německo) byste měli zadat i `-Location` parametr.

*TenantId* můžete najít spuštěním příkazu PowerShellu `Get-AzureSubscription` . Spuštění tohoto příkazu zobrazí TenantId pro každé předplatné.

*Název_clusteru* slouží k vytvoření předpony aplikací služby Azure AD, které jsou vytvořeny pomocí skriptu. Nemusí přesně odpovídat samotnému názvu clusteru. Je určena jenom k tomu, aby bylo snazší mapovat artefakty Azure AD na cluster Service Fabric, ve kterém se používají.

*WebApplicationReplyUrl* je výchozí koncový bod, který Azure AD vrátí vašim uživatelům po dokončení přihlášení. Nastavte tento koncový bod jako koncový bod Service Fabric Explorer pro váš cluster. Pokud vytváříte aplikace Azure AD, které představují existující cluster, zajistěte, aby tato adresa URL odpovídala vašemu stávajícímu koncovému bodu clusteru. Pokud vytváříte aplikace pro nový cluster, naplánujte koncový bod, který bude mít cluster, a ujistěte se, že nepoužíváte koncový bod existujícího clusteru. Ve výchozím nastavení je koncový bod Service Fabric Explorer:

https:// &lt; cluster_domain &gt; : 19080/Explorer

Zobrazí se výzva, abyste se přihlásili k účtu, který má oprávnění správce pro tenanta Azure AD. Po přihlášení vytvoří skript webové a nativní aplikace, které reprezentují váš Service Fabric cluster. Pokud se podíváte na aplikace klienta v [Azure Portal][azure-portal], měli byste vidět dvě nové položky:

   * *Název clusteru* \_ Služby
   * *Název clusteru* \_ Služba

Skript vytiskne JSON vyžadovaný šablonou Azure Resource Manager při [vytváření clusteru s povoleným AAD](service-fabric-cluster-creation-create-template.md#add-azure-ad-configuration-to-use-azure-ad-for-client-access), takže je vhodné ponechat okno PowerShellu otevřené.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>Poradce při potížích s nastavením Azure Active Directory
Nastavení Azure AD a jeho používání může být náročné, takže tady je několik ukazatelů na to, co můžete udělat k ladění problému.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer vás vyzve k výběru certifikátu.
#### <a name="problem"></a>Problém
Po úspěšném přihlášení do Azure AD v Service Fabric Explorer se prohlížeč vrátí na domovskou stránku, ale zobrazí se zpráva s výzvou k výběru certifikátu.

![Dialog certifikátu SFX][sfx-select-certificate-dialog]

#### <a name="reason"></a>Důvod
Uživatel nemá přiřazenou roli v Clusterové aplikaci Azure AD. Proto se ověřování Azure AD v clusteru Service Fabric nezdařilo. Service Fabric Explorer se vrátí k ověřování certifikátu.

#### <a name="solution"></a>Řešení
Postupujte podle pokynů pro nastavení služby Azure AD a přiřaďte uživatelské role. Také doporučujeme, abyste zapnuli přiřazení uživatele vyžadované pro přístup k aplikaci, jak to `SetupApplications.ps1` dělá.

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>Připojení k PowerShellu se nezdařilo s chybou: "zadané přihlašovací údaje jsou neplatné"
#### <a name="problem"></a>Problém
Když použijete PowerShell pro připojení ke clusteru pomocí režimu zabezpečení "Azureactivedirectory selhala", po úspěšném přihlášení do služby Azure AD se připojení nezdaří s chybou: "zadané přihlašovací údaje jsou neplatné."

#### <a name="solution"></a>Řešení
Toto řešení je stejné jako předchozí.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>Service Fabric Explorer vrátí selhání při přihlášení: "AADSTS50011"
#### <a name="problem"></a>Problém
Při pokusu o přihlášení ke službě Azure AD v Service Fabric Explorer vrátí stránka chybu: "AADSTS50011: adresa URL adresy pro odpověď neodpovídá &lt; &gt; adresám odpovědí nakonfigurovaným pro aplikaci: &lt; GUID &gt; ".

![Adresa odpovědi SFX se neshoduje.][sfx-reply-address-not-match]

#### <a name="reason"></a>Důvod
Aplikace clusteru (Web), která představuje Service Fabric Explorer se pokouší o ověření vůči službě Azure AD a jako součást požadavku, poskytuje návratovou adresu URL pro přesměrování. Adresa URL ale není uvedená v seznamu **adres URL odpovědi** aplikace Azure AD.

#### <a name="solution"></a>Řešení
Na stránce registrace aplikace Azure AD pro váš cluster Vyberte **ověřování** a v části **identifikátory uri přesměrování** přidejte Service Fabric Explorer URL do seznamu. Uložte změnu.

![Adresa URL odpovědi webové aplikace][web-application-reply-url]

### <a name="connecting-to-the-cluster-using-azure-ad-authentication-via-powershell-gives-an-error-when-you-sign-in-aadsts50011"></a>Připojení ke clusteru pomocí ověřování Azure AD prostřednictvím PowerShellu přináší při přihlášení chybu: "AADSTS50011"
#### <a name="problem"></a>Problém
Když se pokusíte připojit ke clusteru Service Fabric pomocí Azure AD přes PowerShell, přihlašovací stránka vrátí chybu: "AADSTS50011: adresa URL odpovědi zadaná v požadavku neodpovídá adresám URL odpovědí nakonfigurovaným pro aplikaci: &lt; GUID &gt; ".

#### <a name="reason"></a>Důvod
Podobně jako u předchozího problému se PowerShell pokusí ověřit vůči službě Azure AD, která poskytuje adresu URL pro přesměrování, která není uvedená v seznamu **adres URL odpovědí** aplikace Azure AD.  

#### <a name="solution"></a>Řešení
Použijte stejný proces jako v předchozím problému, ale adresa URL musí být nastavená na `urn:ietf:wg:oauth:2.0:oob` , speciální přesměrování pro ověřování pomocí příkazového řádku.

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>Připojení clusteru pomocí ověřování Azure AD prostřednictvím PowerShellu
Pokud chcete připojit Cluster Service Fabric, použijte následující příklad příkazu PowerShellu:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

Další informace najdete v tématu [rutina Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster).

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>Můžu znovu použít stejného tenanta služby Azure AD ve více clusterech?
Ano. Nezapomeňte ale přidat adresu URL Service Fabric Explorer do vaší Clusterové (webové) aplikace. V opačném případě Service Fabric Explorer nefunguje.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Proč stále potřebuji certifikát serveru, když je povolená služba Azure AD?
FabricClient a FabricGateway provádějí vzájemné ověřování. Při ověřování Azure AD poskytuje integrace služby Azure AD identitu klienta serveru a certifikát serveru používá klient k ověření identity serveru. Další informace o Service Fabric certifikátů najdete v tématu [certifikáty X. 509 a Service Fabric][x509-certificates-and-service-fabric].

## <a name="next-steps"></a>Další kroky
Po nastavení Azure Active Directory aplikací a nastavení rolí pro uživatele, [konfiguraci a nasazení clusteru](service-fabric-cluster-creation-via-arm.md).


<!-- Links -->
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli
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
