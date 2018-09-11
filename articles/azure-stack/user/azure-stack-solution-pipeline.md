---
title: Nasazení aplikace do Azure a Azure Stack | Dokumentace Microsoftu
description: Informace o nasazování aplikací do Azure a Azure Stackem hybridní kanálu CI/CD.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/04/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 773acd3a22244403548ef4ce35164291f5c0be7d
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44300831"
---
# <a name="tutorial-deploy-apps-to-azure-and-azure-stack"></a>Kurz: nasazení aplikace do Azure a Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Zjistěte, jak nasadit aplikaci do Azure a využitím kanálu průběžné integrace a doručování (CI/CD) hybridní službě Azure Stack.

V tomto kurzu vytvoříte ukázkové prostředí:

> [!div class="checklist"]
> * Zahájení nového sestavení založené na potvrzení změn kódu do úložiště služby Azure DevOps.
> * Automaticky nasadíte vaši aplikaci do globální Azure pro testování přijetí u zákazníků.
> * Když je váš kód úspěšné, testování, automaticky Nasaďte aplikaci do služby Azure Stack.

## <a name="benefits-of-the-hybrid-delivery-build-pipe"></a>Výhody hybridního doručení vytvoření kanálu

Provozní kontinuity, zabezpečení a spolehlivost jsou klíčové prvky nasazení aplikace. Tyto prvky jsou zásadní pro vaši organizaci a důležité pro váš vývojový tým. Kanál CI/CD hybridní umožňuje konsolidovat vaše kanály sestavení mezi místním prostředím a veřejným cloudem. Model dodávání hybridní také umožňuje změnit umístění nasazení bez změny vašich aplikací.

Další výhody hybridního přístupu jsou:

* Abyste mohli konzistentní sadu nástrojů pro vývoj napříč vaším prostředím Azure Stack s místními a veřejného cloudu Azure.  Společnou sadu nástrojů usnadňuje implementaci CI/CD vzory a postupy.
* Jsou zaměnitelné aplikací a služeb nasazených v Azure nebo ve službě Azure Stack a stejný kód lze spustit v některém umístění. Můžete využít výhod místní a veřejným cloudem funkce a možnosti.

Další informace o CI a CD:

* [Co je kontinuální integrace?](https://www.visualstudio.com/learn/what-is-continuous-integration/)
* [Co je průběžné doručování?](https://www.visualstudio.com/learn/what-is-continuous-delivery/)

## <a name="prerequisites"></a>Požadavky

Musíte mít komponenty v místo pro vytvoření kanálu CI/CD hybridní. Následující komponenty budou trvat dobu připravit:

* Partnerem Azure OEM/hardwaru můžete nasadit produkčního prostředí Azure Stack. Všichni uživatelé můžou nasadit Azure Stack Development Kit (ASDK).
* Operátor Azure stacku, musí taky: nasazení služby App Service, vytvořte plány a nabídky, vytvořit tenanta předplatného a přidejte image Windows serveru 2016.

>[!NOTE]
>Pokud už máte některé z těchto součástí nasazení, ujistěte se, že splňují všechny požadavky před zahájením tohoto kurzu.

V tomto kurzu se předpokládá, že máte některé základní znalosti o Azure a Azure Stack. Další informace před zahájením tohoto kurzu, přečtěte si následující články:

* [Úvod do Azure](https://azure.microsoft.com/overview/what-is-azure/)
* [Klíčové koncepty služby Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure-requirements"></a>Požadavky na Azure

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
* Vytvoření [webová aplikace](https://docs.microsoft.com/azure/app-service/app-service-web-overview) v Azure. Zkontrolujte si adresu URL webové aplikace, budete muset použít v tomto kurzu.

### <a name="azure-stack-requirements"></a>Požadavky služby Azure Stack

* Použít systémech pro Azure Stack integrované nebo nasadit Azure Stack Development Kit (ASDK). Nasazení ASDK:
    * [Kurz: nasazení ASDK pomocí instalačního programu](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-deploy) poskytuje podrobné pokyny.
    * Použití [ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 ) skript Powershellu pro automatizaci ASDK kroky po nasazení.

    > [!Note]
    > Instalace ASDK trvá přibližně sedm hodin k dokončení, takže Plánujte odpovídajícím způsobem.

 * Nasazení [služby App Service](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) služeb PaaS do služby Azure Stack.
 * Vytvoření [plánu nebo nabídky](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) ve službě Azure Stack.
 * Vytvoření [tenanta předplatného](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) ve službě Azure Stack.
 * Vytvoření webové aplikace v rámci předplatného tenanta. Poznamenejte si nový URL webové aplikace pro později použít.
 * Nasazení virtuálního počítače Azure DevOps služby v rámci předplatného tenanta.
* Zadejte bitovou kopii systému Windows Server 2016 s .NET 3.5 pro virtuální počítač (VM). Tento virtuální počítač bude vytvořen ve vaší službě Azure Stack jako privátní sestavovacího agenta.

### <a name="developer-tool-requirements"></a>Požadavky na nástroj pro vývojáře

* Vytvoření [pracovní prostor služby Azure DevOps](https://docs.microsoft.com/azure/devops/repos/tfvc/create-work-workspaces). Proces registrace vytvoří projekt s názvem **MyFirstProject**.
* [Instalace sady Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) a [přihlášení ke službám Azure DevOps](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services).
* Připojte se k projektu a [místně ho naklonujte](https://www.visualstudio.com/docs/git/gitquickstart).

 > [!Note]
 > Vaším prostředím Azure Stack potřebuje správný imagí syndikovat do spuštění systému Windows Server a SQL Server. Musí také mít nasazení služby App Service.

## <a name="prepare-the-private-azure-pipelines-agent-for-azure-devops-services-integration"></a>Příprava privátní agent Azure kanály pro integraci služby Azure DevOps

### <a name="prerequisites"></a>Požadavky

Služby Azure DevOps se ověřuje na Azure Resource Manageru pomocí instančního objektu. Služby Azure DevOps musí mít **Přispěvatel** role pro zřízení prostředků v předplatném služby Azure Stack.

Následující kroky popisují, co je potřeba nakonfigurovat ověřování:

1. Vytvoření instančního objektu, nebo použít existující instanční objekt služby.
2. Vytvořte ověřovací klíče pro instanční objekt.
3. Předplatné Azure Stack prostřednictvím řízení přístupu na základě rolí umožňuje hlavní název služby (SPN) jako součást role přispěvatele pro ověření.
4. Vytvořte novou definici služby ve službě Azure DevOps Services pomocí koncových bodů služby Azure Stack a informace o SPN.

### <a name="create-a-service-principal"></a>Vytvoření instančního objektu

Odkazovat [vytvoření instančního objektu](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) pokyny k vytvoření instančního objektu. Zvolte **webové aplikace nebo rozhraní API** pro typ aplikace nebo [pomocí skriptu prostředí PowerShell](https://github.com/Microsoft/vsts-rm-extensions/blob/master/TaskModules/powershell/Azure/SPNCreation.ps1#L5) jak je popsáno v článku [existující službu vytvořte připojení služby Azure Resource Manageru hlavní ](https://docs.microsoft.com/vsts/pipelines/library/connect-to-azure?view=vsts#create-an-azure-resource-manager-service-connection-with-an-existing-service-principal).

 > [!Note]  
 > Pokud použijete skript k vytvoření koncového bodu Azure stacku Azure Resource Manageru, je potřeba předat **- azureStackManagementURL** parametr a **- environmentName** parametru. Příklad:  
> `-azureStackManagementURL https://management.local.azurestack.external -environmentName AzureStack`

### <a name="create-an-access-key"></a>Vytvořit přístupový klíč

Instanční objekt služby vyžaduje klíče pro ověřování. Použijte následující postup ke generování klíče.

1. V Azure Active Directory vyberte z **Registrace aplikací** svou aplikaci.

    ![Vyberte aplikaci](media\azure-stack-solution-hybrid-pipeline\000_01.png)

2. Poznamenejte si hodnotu **ID aplikace**. Tuto hodnotu použijete při konfiguraci koncového bodu služby v Azure DevOps služby.

    ![ID aplikace](media\azure-stack-solution-hybrid-pipeline\000_02.png)

3. Pokud chcete generovat ověřovací klíč, vyberte **Nastavení**.

    ![Upravit nastavení aplikace](media\azure-stack-solution-hybrid-pipeline\000_03.png)

4. Pokud chcete generovat ověřovací klíč, vyberte **Klíče**.

    ![Konfigurace nastavení klíče](media\azure-stack-solution-hybrid-pipeline\000_04.png)

5. Zadejte popis klíče a nastavte dobu trvání klíče. Až budete hotovi, vyberte **Uložit**.

    ![Popis klíče a doba trvání](media\azure-stack-solution-hybrid-pipeline\000_05.png)

    Po uložení klíče, klíče **hodnotu** se zobrazí. Zkopírujte tuto hodnotu, protože tuto hodnotu nelze získat později. Můžete zadat **hodnotu klíče** s ID aplikace se přihlásit jako aplikace. Hodnotu klíče uložte na místo, odkud ji aplikace může načíst.

    ![Klíč hodnoty](media\azure-stack-solution-hybrid-pipeline\000_06.png)

### <a name="get-the-tenant-id"></a>Získání ID tenanta

Jako součást konfigurace koncového bodu služby Azure DevOps Services vyžaduje **ID Tenanta** , který odpovídá v adresáři AAD, který se nasazuje do Azure stacku razítka. Pomocí následujících kroků k získání ID Tenanta.

1. Vyberte **Azure Active Directory**.

    ![Pro tenanta Azure Active Directory](media\azure-stack-solution-hybrid-pipeline\000_07.png)

2. K získání ID tenanta vyberte v tenantovi Azure AD možnost **Vlastnosti**.

    ![Zobrazení vlastností klienta](media\azure-stack-solution-hybrid-pipeline\000_08.png)

3. Zkopírujte **ID adresáře**. Tato hodnota představuje ID tenanta.

    ![ID adresáře](media\azure-stack-solution-hybrid-pipeline\000_09.png)

### <a name="grant-the-service-principal-rights-to-deploy-resources-in-the-azure-stack-subscription"></a>Udělení oprávnění instančního objektu služby k nasazení prostředků v předplatném služby Azure Stack

Pro přístup k prostředkům ve vašem předplatném, musíte přiřadit aplikace k roli. Rozhodněte, jakou roli představuje nejlepší oprávnění pro aplikaci. Další informace o dostupných rolí, najdete v článku [RBAC: vestavěné role](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Nastavit obor na úrovni předplatného, skupinu prostředků nebo prostředek. Oprávnění se dědí do oboru na nižších úrovních. Například přidáním aplikace k roli Čtenář pro skupinu prostředků znamená, že můžete přečíst, skupinu prostředků a všechny její prostředky.

1. Přejděte na úrovni oboru, který chcete přiřadit aplikaci. Například vyberte přiřazení role v oboru předplatného, **předplatná**.

    ![Vyberte předplatné.](media\azure-stack-solution-hybrid-pipeline\000_10.png)

2. V **předplatné**, vyberte Visual Studio Enterprise.

    ![Visual Studio Enterprise](media\azure-stack-solution-hybrid-pipeline\000_11.png)

3. V sadě Visual Studio Enterprise, vyberte **řízení přístupu (IAM)**.

    ![Řízení přístupu (IAM)](media\azure-stack-solution-hybrid-pipeline\000_12.png)

4. Vyberte **Přidat**.

    ![Přidat](media\azure-stack-solution-hybrid-pipeline\000_13.png)

5. V **přidat oprávnění**, vyberte roli, kterou chcete přiřadit k aplikaci. V tomto příkladu **vlastníka** role.

    ![Role vlastníka](media\azure-stack-solution-hybrid-pipeline\000_14.png)

6. Ve výchozím nastavení aplikace Azure Active Directory nejsou zobrazeny v dostupných možnostech. Pokud chcete najít aplikace, musíte zadat jeho název v **vyberte** pole, které chcete ji najít. Vyberte aplikaci.

    ![Výsledek hledání aplikací](media\azure-stack-solution-hybrid-pipeline\000_16.png)

7. Vyberte **Uložit** k dokončení přiřazení role. Zobrazí se vaše aplikace v seznamu Uživatelé přiřazení k roli pro tento obor.

### <a name="role-based-access-control"></a>Řízení přístupu na základě rolí

Azure na základě rolí řízení přístupu (RBAC) poskytuje propracovanou správu přístupu pro Azure. Pomocí RBAC můžete řídit úroveň přístupu, který uživatelé potřebují ke své práci. Další informace o řízení přístupu na základě rolí najdete v tématu [spravovat přístup k prostředkům předplatného Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json).

### <a name="azure-devops-services-agent-pools"></a>Fondy agentů služby Azure DevOps

Místo správy každého agenta samostatně, můžete uspořádat agentů do fondy agentů. Fond agentů definuje hranice sdílení pro všechny agenty v tomto fondu. Ve službě Azure DevOps Services fondy agentů oborem pro organizaci služeb Azure DevOps, což znamená, že fond agentů můžete sdílet mezi projekty. Další informace o fondech agentů najdete v tématu [vytvořit fondy agentů a fronty](https://docs.microsoft.com/azure/devops/pipelines/agents/pools-queues?view=vsts).

### <a name="add-a-personal-access-token-pat-for-azure-stack"></a>Přidejte osobní přístupový Token PAT pro Azure Stack

Vytvoření osobní přístupový Token pro přístup ke službám Azure DevOps.

1. Přihlaste se k vaší organizaci Azure DevOps služby a vyberte název profilu vaší organizace.

2. Vyberte **spravovat zabezpečení** na stránku vytvoření tokenu přístupu.

    ![Přihlášení uživatele](media\azure-stack-solution-hybrid-pipeline\000_17.png)

    ![Vyberte projekt](media\azure-stack-solution-hybrid-pipeline\000_18.png)

    ![Přidat token pat](media\azure-stack-solution-hybrid-pipeline\000_18a.png)

    ![Vytvořit token](media\azure-stack-solution-hybrid-pipeline\000_18b.png)

3. Zkopírujte token.

    > [!Note]
    > Uložte informace o tokenu. Tyto informace se neuloží a znovu nezobrazí při opuštění webové stránky.

    ![Token pat](media\azure-stack-solution-hybrid-pipeline\000_19.png)

### <a name="install-the-azure-devops-services-build-agent-on-the-azure-stack-hosted-build-server"></a>Nainstalujte agenta služby Azure DevOps sestavení ve službě Azure Stack hostování serveru pro sestavení

1. Připojení k serveru sestavení, který jste nasadili na hostitele služby Azure Stack.
2. Stažení a nasazení agenta sestavení jako služby pomocí osobní přístup token PAT a správce virtuálního počítače účet Spustit jako.

    ![Stáhnout agenta sestavení](media\azure-stack-solution-hybrid-pipeline\010_downloadagent.png)

3. Přejděte do složky agenta sestavení byl extrahován. Spustit **config.cmd** soubor z příkazového řádku se zvýšenými oprávněními.

    ![Extrahované sestavovacího agenta](media\azure-stack-solution-hybrid-pipeline\000_20.png)

    ![Registraci agenta sestavení](media\azure-stack-solution-hybrid-pipeline\000_21.png)

4. Po dokončení config.cmd složka agenta sestavení je aktualizován další soubory. Složka s extrahované obsah by měl vypadat nějak takto:

    ![Aktualizace složky agenta sestavení](media\azure-stack-solution-hybrid-pipeline\009_token_file.png)

    Vidíte agenta ve složce služby Azure DevOps.

## <a name="endpoint-creation-permissions"></a>Oprávnění pro vytváření koncového bodu

Tím, že vytvoříte koncové body, Visual Studio Online (VSTO) build aplikace Azure Service nasadit do služby Azure Stack. Služby Azure DevOps se připojí k agenta sestavení, který se připojuje ke službě Azure Stack.

![NorthwindCloud ukázkovou aplikaci v VSTO](media\azure-stack-solution-hybrid-pipeline\012_securityendpoints.png)

1. Přihlaste se k VSTO a přejděte na stránku nastavení aplikací.
2. Na **nastavení**vyberte **zabezpečení**.
3. V **skupin služby Azure DevOps**vyberte **koncový bod Creators**.

    ![Koncový bod NorthwindCloud Tvůrce](media\azure-stack-solution-hybrid-pipeline\013_endpoint_creators.png)

4. Na **členy** kartu, vyberte možnost **přidat**.

    ![Přidat člena](media\azure-stack-solution-hybrid-pipeline\014_members_tab.png)

5. V **přidávat uživatele a skupiny**, zadejte uživatelské jméno a vyberte uživatele ze seznamu uživatelů.
6. Vyberte **uložit změny**.
7. V **skupin služby Azure DevOps** seznamu vyberte **koncový bod správci**.

    ![Koncový bod NorthwindCloud správci](media\azure-stack-solution-hybrid-pipeline\015_save_endpoint.png)

8. Na **členy** kartu, vyberte možnost **přidat**.
9. V **přidávat uživatele a skupiny**, zadejte uživatelské jméno a vyberte uživatele ze seznamu uživatelů.
10. Vyberte **uložit změny**.

Teď, když existuje informace o koncovém bodu služby Azure DevOps pro připojení služby Azure Stack je připravený k použití. Agent sestavení ve službě Azure Stack získá pokyny ze služeb Azure DevOps a pak agenta přenáší informace o koncovém bodu pro komunikaci pomocí služby Azure Stack.
## <a name="create-an-azure-stack-endpoint"></a>Vytvoření koncového bodu služby Azure Stack

Můžete podle pokynů v [vytvořte připojení služby Azure Resource Manageru existující službu objektu zabezpečení ](https://docs.microsoft.com/vsts/pipelines/library/connect-to-azure?view=vsts#create-an-azure-resource-manager-service-connection-with-an-existing-service-principal) článku o vytvoření připojení služby pomocí existující službu objektu zabezpečení a použijte následující mapování:

- Prostředí: AzureStack
- Adresa URL prostředí: Něco jako `https://management.local.azurestack.external`
- ID předplatného: ID předplatného uživatele ze služby Azure Stack
- Název předplatného: Název předplatného uživatele ze služby Azure Stack
- ID klienta instančního objektu: ID objektu zabezpečení z [to](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-pipeline#create-a-service-principal) části v tomto článku.
- Klíč objektu služby: klíč ze stejného článku (nebo heslo, pokud jste použili skriptu).
- ID tenanta: ID tenanta můžete načíst následující instrukce na [získání ID tenanta](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-pipeline#get-the-tenant-id).

Teď, když je vytvořen koncový bod, VSTS pro připojení služby Azure Stack je připravený k použití. Agent sestavení ve službě Azure Stack získá pokyny z VSTS, a pak agenta přenáší informace o koncovém bodu pro komunikaci s Azure Stackem.

![Agent sestavení](media\azure-stack-solution-hybrid-pipeline\016_save_changes.png)

## <a name="develop-your-application-build"></a>Vývoj aplikace sestavení

V této části kurzu, je nutné:

* Přidání kódu do projektu služby Azure DevOps.
* Vytvoření nasazení samostatné webové aplikace.
* Konfigurace procesu průběžného nasazování

> [!Note]
 > Vaším prostředím Azure Stack potřebuje správný imagí syndikovat do spuštění systému Windows Server a SQL Server. Musí také mít nasazení služby App Service. Najdete v dokumentaci služby App Service "Požadavky" v tématu požadavky na operátor Azure stacku.

Hybridní CI/CD můžete použít kód aplikace a kódu infrastruktury. Použití [šablony Azure Resource Manageru, jako je web ](https://azure.microsoft.com/resources/templates/) kód aplikace z Azure DevOps služby pro nasazení pro oba cloudy.

### <a name="add-code-to-an-azure-devops-services-project"></a>Přidání kódu do projektu služby Azure DevOps

1. Přihlaste se ke službám Azure DevOps s organizací, který má práva k vytvoření projektu ve službě Azure Stack. Následující snímek obrazovky ukazuje, jak se připojit k projektu HybridCICD.

    ![Připojení k projektu](media\azure-stack-solution-hybrid-pipeline\017_connect_to_project.png)

2. **Naklonujte úložiště** ve vytváření a otevírání výchozí webové aplikace.

    ![Klonování úložiště](media\azure-stack-solution-hybrid-pipeline\018_link_arm.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Vytvoření nasazení samostatné webové aplikace pro App Service v oba cloudy

1. Upravit **WebApplication.csproj** souboru: vyberte **Runtimeidentifier** a pak přidejte `win10-x64.` Další informace najdete v tématu [samostatná nasazení](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) dokumentace ke službě.

    ![Konfigurace Runtimeidentifier](media\azure-stack-solution-hybrid-pipeline\019_runtimeidentifer.png)

2. Zkontrolujte kód do služby Azure DevOps pomocí Team Exploreru.

3. Potvrďte, že kód aplikace došlo k zaškrtnutí do služby Azure DevOps.

### <a name="create-the-build-pipeline"></a>Vytvoření kanálu sestavení

1. Přihlaste se ke službám Azure DevOps s organizací, které můžete vytvořit kanál sestavení.

2. Přejděte **sestavit Web Express** stránky pro projekt.

3. V **argumenty**, přidejte **- r win10-x64** kódu. To se vyžaduje k aktivaci samostatná nasazení s.Net Core.

    ![Přidat argument sestavení kanálu](media\azure-stack-solution-hybrid-pipeline\020_publish_additions.png)

4. Spuštění sestavení. [Samostatná nasazení sestavení](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) procesu budete publikovat artefakty, které lze spustit v Azure a Azure Stack.

### <a name="use-an-azure-hosted-build-agent"></a>Použití Azure hostovaný agent sestavení

Pomocí agenta sestavení hostované ve službě Azure DevOps Services je vhodné možnosti pro vytváření a nasazování webových aplikací. Údržba agenta a upgradů automaticky provádí Microsoft Azure, což umožňuje nepřetržitý a bez přerušení vývojového cyklu.

### <a name="configure-the-continuous-deployment-cd-process"></a>Konfigurace procesu průběžného nasazování (CD)

Azure DevOps služeb a Team Foundation Server (TFS) poskytuje vysoce konfigurovatelné a spravovatelné kanálu pro vydané verze do více prostředí, jako je vývoj, pracovní, kontrola kvality (dotazů a odpovědí) a provoz. Tento proces může obsahovat, která vyžadují schválení určitým fázím životního cyklu aplikací.

### <a name="create-release-pipeline"></a>Vytvoření kanálu pro vydávání verzí

Vytvoření kanál pro vydávání verzí je posledním krokem v aplikaci procesu sestavení. Tento kanál pro vydávání verzí se používá k vytvoření vydané verze a nasazení sestavení.

1. Přihlaste se ke službám Azure DevOps a přejděte do **kanály Azure** pro váš projekt.
2. Na **verze** kartu, vyberte možnost  **\[ +]** a potom si vyberte **definice vydané verze vytvořit**.

   ![Vytvoření kanálu pro vydávání verzí](media\azure-stack-solution-hybrid-pipeline\021a_releasedef.png)

3. Na **vyberte šablonu**, zvolte **nasazení služby Azure App Service**a pak vyberte **použít**.

    ![Použít šablonu](media\azure-stack-solution-hybrid-pipeline\102.png)

4. Na **přidání artefaktu**, z **zdroj (definice sestavení)** rozevírací nabídky vyberte aplikaci sestavení cloudu Azure.

    ![Přidání artefaktu](media\azure-stack-solution-hybrid-pipeline\103.png)

5. Na **kanálu** kartu, vyberte možnost **1 fáze**, **1 úloha** propojit **zobrazit úlohy prostředí**.

    ![Úlohy v zobrazení kanálu](media\azure-stack-solution-hybrid-pipeline\104.png)

6. Na **úlohy** kartu, zadejte jako Azure **název prostředí** a vyberte EP Traders webové AzureCloud z **předplatného Azure** rozevíracího seznamu.

    ![Nastavení proměnných prostředí](media\azure-stack-solution-hybrid-pipeline\105.png)

7. Zadejte **název služby Azure app service**, což je "firma" dalšího snímku obrazovky.

    ![Název služby App service](media\azure-stack-solution-hybrid-pipeline\106.png)

8. Fáze agenta vyberte **hostované VS2017** z **frontu agenta** rozevíracího seznamu.

    ![Hostovaný agent](media\azure-stack-solution-hybrid-pipeline\107.png)

9. V **nasazení služby Azure App Service**, vyberte platnými **balíčku nebo složky** pro prostředí.

    ![Vyberte balíček nebo složky](media\azure-stack-solution-hybrid-pipeline\108.png)

10. V **vybrat soubor nebo složku**vyberte **OK** k **umístění**.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\109.png)

11. Uložte všechny změny a vraťte se do **kanálu**.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\110.png)

12. Na **kanálu** kartu, vyberte možnost **přidání artefaktu**a zvolte **NorthwindCloud Traders-lodi** z **zdroj (definice sestavení)** rozevíracího seznamu.

    ![Přidání nové artefaktu](media\azure-stack-solution-hybrid-pipeline\111.png)

13. Na **vyberte šablonu**, přidat jiné prostředí. Vyberte si **nasazení služby Azure App Service** a pak vyberte **použít**.

    ![Vyberte šablonu](media\azure-stack-solution-hybrid-pipeline\112.png)

14. Zadejte "Azure Stack" jako **název prostředí**.

    ![Název prostředí](media\azure-stack-solution-hybrid-pipeline\113.png)

15. Na **úlohy** kartu, vyhledejte a vyberte Azure Stack.

    ![Prostředí Azure Stack](media\azure-stack-solution-hybrid-pipeline\114.png)

16. Z **předplatného Azure** rozevíracího seznamu vyberte "EP AzureStack lodi Traders" pro koncový bod služby Azure Stack.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\115.png)

17. Zadejte název webové aplikace služby Azure Stack jako **název služby App service**.

    ![Název služby App service](media\azure-stack-solution-hybrid-pipeline\116.png)

18. V části **Výběr agenta**, můžete si vybrat "AzureStack - bDouglas do části" z **frontu agenta** rozevíracího seznamu.

    ![Výběr agenta](media\azure-stack-solution-hybrid-pipeline\117.png)

19. Pro **nasazení služby Azure App Service**, vyberte platnými **balíčku nebo složky** pro prostředí. Na **vybrat soubor nebo složku**vyberte **OK** složky **umístění**.

    ![Vyberte balíček nebo složky](media\azure-stack-solution-hybrid-pipeline\118.png)

    ![Schválit umístění](media\azure-stack-solution-hybrid-pipeline\119.png)

20. Na **proměnnou** kartu, vyhledejte proměnnou s názvem **VSTS_ARM_REST_IGNORE_SSL_ERRORS**. Nastavte hodnotu proměnné **true**a nastavte jeho rozsah **Azure Stack**.

    ![Nakonfigurujte proměnné](media\azure-stack-solution-hybrid-pipeline\120.png)

21. Na **kanálu** kartu, vyberte možnost **trigger průběžného nasazování** ikonu pro artefakt NorthwindCloud Traders – Web a nastavte **trigger průběžného nasazování** do **Povolené**.  To samé udělá pro artefakt "NorthwindCloud lodi Traders".

    ![Trigger průběžného nasazování sady](media\azure-stack-solution-hybrid-pipeline\121.png)

22. Prostředí Azure Stack, vyberte **podmínky před nasazením** ikony nastavte aktivační události na **po vydání**.

    ![Aktivační podmínky před nasazením sady](media\azure-stack-solution-hybrid-pipeline\122.png)

23. Uložte všechny provedené změny.

> [!Note]
> Některá nastavení pro uvolnění úloh může automaticky definovaná jako [proměnné prostředí](https://docs.microsoft.com/azure/devops/pipelines/release/variables?view=vsts#custom-variables) při vytváření vydávání ze šablony. Tato nastavení nelze změnit v nastavení úkolu. Však můžete upravit tato nastavení v nadřazených položek prostředí.

## <a name="create-a-release"></a>Vytvoření vydané verze

Teď, když jste dokončili změny kanál pro vydávání verzí, je čas spustit nasazení. K tomuto účelu vytvoříte vydání z kanál pro vydávání verzí. Verze mohou být vytvořeny automaticky. trigger průběžného nasazování je třeba nastavit v kanál pro vydávání verzí. To znamená, že změna zdrojového kódu se spustí nové sestavení do a z té, nová verze. Ale v této části vytvoříte nové vydané verze ručně.

1. Na **kanálu** otevřenou kartou **Release** rozevírací seznam a zvolte **vytvořit vydání**.

    ![Vytvoření vydané verze](media\azure-stack-solution-hybrid-pipeline\200.png)

2. Zadejte popis pro vydání, zkontrolujte, zda jsou vybrány správné artefakty a pak zvolte **vytvořit**. Po chvíli se zobrazí banner s označující, že byla vytvořena nová verze a verze název se zobrazí jako odkaz. Klikněte na odkaz zobrazíte na stránce souhrnu vydání.

    ![Banner vytvoření verze](media\azure-stack-solution-hybrid-pipeline\201.png)

3. Na stránce souhrnu vydání pro zobrazuje podrobnosti o verzi. Na následujícím snímku obrazovky pro "Release-2" **prostředí** části ukazuje **stav nasazení** pro Azure jako "Probíhající" a stav pro službu Azure Stack je "bylo DOKONČENO". Kdy se stav nasazení pro prostředí Azure změní na "ÚSPĚCH", zobrazí se banner označující, že verze je připravené ke schválení. Při nasazení čeká na vyřízení nebo se nezdařila, modrý **(i)** informační ikona, která se zobrazí. Najeďte myší na ikonu si zobrazíte automaticky otevírané okno, které obsahuje důvodem zpoždění nebo selhání.

    ![Stránce souhrnu vydaných verzí](media\azure-stack-solution-hybrid-pipeline\202.png)

Jiných zobrazení, jako je například seznam verzí, se také zobrazí ikonu, která indikuje, že se čeká na schválení. Automaticky otevírané okno pro tato ikona zobrazuje název prostředí a další podrobnosti související s nasazením. Je snadné správce naleznete v části celkový průběh vydaných verzí a zjistěte, která verze se čeká na schválení.

### <a name="monitor-and-track-deployments"></a>Monitorování a sledování nasazení

Tato část ukazuje, jak můžete monitorovat a sledujte všechna nasazení. Verze pro nasazení dva weby Azure App Service poskytuje dobrý příklad.

1. Na stránce se souhrnem "Release-2", vyberte **protokoly**. Během nasazení Tato stránka zobrazuje v za provozu protokolu z agenta. V levém podokně se zobrazí stav jednotlivých operací v nasazení pro každé prostředí.

    Můžete zvolit ikonu osoby v **akce** sloupec o schválení před nasazením nebo po nasazení a podívat se, kdo schválit (ani odmítnout) nasazení zprávy jsou k dispozici.

2. Po dokončení nasazení se v pravém podokně zobrazí celý soubor protokolu. Můžete vybrat libovolný **krok** v levém podokně najdete v jediném kroku, jako je například "Inicializovat úloha" v souboru protokolu. Možnost zobrazit jednotlivé protokoly usnadňuje trasování a ladění součástí celkové nasazení. Můžete také **Uložit** soubor protokolu pro krok, nebo **stáhnout všechny protokoly jako soubor zip**.

    ![Protokoly](media\azure-stack-solution-hybrid-pipeline\203.png)

3. Otevřít **Souhrn** kartu a zobrazí se obecné informace o verzi. Toto zobrazení ukazuje údaje o sestavení, prostředí, který byl nasazen na, stav nasazení a další informace o verzi.

4. Vyberte odkaz na prostředí (**Azure** nebo **Azure Stack**) zobrazíte informace o existujících a čeká se na nasazení do konkrétního prostředí. Tato zobrazení slouží jako rychlý způsob, jak ověřit, že stejný build nasadila do obou prostředích.

5. Otevřít **nasadili aplikaci v produkčním prostředí** v prohlížeči. Například otevřít adresu URL pro web Azure App Services `http://[your-app-name].azurewebsites.net`.

## <a name="next-steps"></a>Další postup

* Další informace o vzorech cloudu Azure, najdete v článku [vzory návrhu v cloudu](https://docs.microsoft.com/azure/architecture/patterns).
