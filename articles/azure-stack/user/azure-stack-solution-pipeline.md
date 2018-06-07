---
title: Nasazení aplikace do Azure a Azure zásobníku | Microsoft Docs
description: Zjistěte, jak nasadit aplikace do Azure a Azure zásobníku se zřetězením příkazů hybridní CI/CD.
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
ms.date: 05/15/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 41e6f64ada7c95674cc2573048eef8afc83e4385
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604348"
---
# <a name="tutorial-deploy-apps-to-azure-and-azure-stack"></a>Kurz: nasazení aplikací do Azure a Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Zjistěte, jak nasadit aplikaci do Azure a Azure zásobníku pomocí kanálu hybridní nepřetržité integrace/průběžné doručování (CI/CD).

V tomto kurzu vytvoříte ukázkové prostředí:

> [!div class="checklist"]
> * Zahájení nové sestavení podle potvrzení kód do úložiště Visual Studio Team Services (VSTS).
> * Automaticky nasaďte aplikace do globální Azure pro testování přijetí uživateli.
> * Pokud váš kód bude provedeno úspěšně, testování, automaticky Nasaďte aplikaci Azure zásobníku.

## <a name="benefits-of-the-hybrid-delivery-build-pipe"></a>Výhody hybridního doručení vytvoření kanálu

Kontinuity, zabezpečení a spolehlivost jsou klíčové prvky nasazení aplikace. Tyto prvky jsou nezbytné pro vaši organizaci a důležité pro váš vývojový tým. Kanál CI/CD hybridní umožňuje konsolidovat vaší kanály sestavení v místním prostředí a veřejného cloudu. Hybridního doručení modelu také umožňuje změnit umístění nasazení beze změny aplikace.

Další výhody, které přístup hybridní jsou:

* Je možné zachovat konzistentní sadu nástrojů pro vývoj v prostředí Azure zásobníku místní a veřejného cloudu Azure.  Společnou sadu nástrojů usnadňuje implementaci CI/CD vzory a postupy.
* Aplikace a služby, které jsou nasazené v Azure nebo Azure zásobníku se zaměňovat a má stejný kód můžete spustit v některém umístění. Můžete využít výhod místní a veřejný cloud funkce a možnosti.

Další informace o položek konfigurace a CD:

* [Co je průběžnou integraci?](https://www.visualstudio.com/learn/what-is-continuous-integration/)
* [Co je nastavené průběžné doručování?](https://www.visualstudio.com/learn/what-is-continuous-delivery/)

## <a name="prerequisites"></a>Požadavky

Je potřeba mít součásti zavedené k vytvoření kanálu hybridní CI/CD. Doba přípravy bude trvat následující součásti:

* Partnerem Azure OEM nebo hardwaru můžete nasadit provozní zásobník Azure. Všichni uživatelé můžete nasadit Azure zásobníku Development Kit (ASDK).
* Operátor zásobník Azure, musí taky: nasazení služby App Service, vytvářet plány a nabídky, vytvořit odběr klienta a přidat bitovou kopii systému Windows Server 2016.

>[!NOTE]
>Pokud již máte některé z těchto součástí nasazení, zkontrolujte, zda že splňují všechny požadavky, před zahájením tohoto kurzu.

Tento kurz předpokládá, že máte některé základní znalosti o Azure a Azure zásobníku. Další informace před zahájením tohoto kurzu, najdete v následujících článcích:

* [Úvod do Azure](https://azure.microsoft.com/overview/what-is-azure/)
* [Klíčové koncepty Azure zásobníku](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure-requirements"></a>Požadavky na Azure

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
* Vytvoření [webová aplikace](https://docs.microsoft.com/azure/app-service/app-service-web-overview) v Azure. Ujistěte se, Všimněte si, adresy URL webové aplikace, budete muset použít v tomto kurzu.

### <a name="azure-stack-requirements"></a>Požadavky pro Azure zásobníku

* Pomocí služby Azure zásobníku integrované systému nebo nasadit Azure zásobníku Development Kit (ASDK). Nasazení ASDK:
    * [Kurz: nasazení ASDK pomocí Instalační služby](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-deploy) poskytuje podrobné pokyny.
    * Použití [ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 ) skript prostředí PowerShell k automatizaci ASDK kroky následující po nasazení.

    > [!Note]
    > Instalace ASDK trvá přibližně sedm hodin k dokončení, takže Plánujte odpovídajícím způsobem.

 * Nasazení [služby App Service](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) služby PaaS do protokolů Azure.
 * Vytvoření [plán nebo nabídky](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) v Azure zásobníku.
 * Vytvoření [klienta předplatné](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) v zásobníku Azure.
 * Vytvoření webové aplikace v rámci předplatného klienta. Poznamenejte si adresu URL nové aplikace webového pro později.
 * Nasazení služby VSTS virtuálního počítače v rámci předplatného klienta.
* Zadejte bitovou kopii systému Windows Server 2016 pro virtuální počítač (VM) pomocí rozhraní .NET 3.5. Tento virtuální počítač bude založený na vaše zásobník Azure jako privátní sestavení agenta.

### <a name="developer-tool-requirements"></a>Požadavky na nástroj pro vývojáře

* Vytvoření [prostoru služby VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services). Proces registrace vytvoří projekt s názvem **MyFirstProject**.
* [Nainstalovat Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) a [Přihlaste se do služby VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services).
* Připojení k vašemu projektu a [naklonujte místně](https://www.visualstudio.com/docs/git/gitquickstart).

 > [!Note]
 > Prostředí Azure zásobníku musí správné bitové kopie souhrnné ke spuštění systému Windows Server a SQL Server. Musí mít rovněž nasazené služby App Service.

## <a name="prepare-the-private-build-and-release-agent-for-visual-studio-team-services-integration"></a>Příprava privátní sestavení a verze agenta pro integraci produktů Visual Studio Team Services

### <a name="prerequisites"></a>Požadavky

Visual Studio Team Services (VSTS) ověřuje vůči správce Azure Resource Manager pomocí objektu služby. Služby VSTS musí mít **Přispěvatel** role zřídit prostředky v předplatné Azure zásobníku.

Následující kroky popisují, co je potřeba nakonfigurovat ověřování:

1. Vytvoření instančního objektu, nebo použít stávající instanční objekt.
2. Vytvořte ověřovací klíče pro objekt služby.
3. Ověřování odběru zásobník Azure prostřednictvím řízení přístupu na základě Role, které chcete povolit hlavní název služby (SPN) jako součást role Přispěvatel společnosti.
4. Vytvořte novou definici služby v služby VSTS pomocí koncové body Azure zásobníku a SPN informace.

### <a name="create-a-service-principal"></a>Vytvoření instančního objektu

Odkazovat [vytvoření instančního objektu](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) pokyny k vytvoření instančního objektu a potom vyberte **webové aplikace rozhraní API** pro typ aplikace.

### <a name="create-an-access-key"></a>Vytvoření přístupové klávesy

Objekt služby vyžaduje klíče pro ověřování. Použijte následující postup ke generování klíče.

1. V Azure Active Directory vyberte z **Registrace aplikací** svou aplikaci.

    ![Vyberte aplikaci](media\azure-stack-solution-hybrid-pipeline\000_01.png)

2. Poznamenejte si hodnotu **ID aplikace**. Tuto hodnotu použijete při konfiguraci koncového bodu služby v služby VSTS.

    ![ID aplikace](media\azure-stack-solution-hybrid-pipeline\000_02.png)

3. Pokud chcete generovat ověřovací klíč, vyberte **Nastavení**.

    ![Upravit nastavení aplikace](media\azure-stack-solution-hybrid-pipeline\000_03.png)

4. Pokud chcete generovat ověřovací klíč, vyberte **Klíče**.

    ![Konfigurace nastavení klíče](media\azure-stack-solution-hybrid-pipeline\000_04.png)

5. Zadejte popis pro klíč a nastavení doby trvání klíče. Až budete hotovi, vyberte **Uložit**.

    ![Popis klíčů a doba trvání](media\azure-stack-solution-hybrid-pipeline\000_05.png)

    Po uložení klíče, klíče **hodnotu** se zobrazí. Zkopírujte tuto hodnotu, protože tuto hodnotu nelze získat později. Zadáte **hodnotu klíče** s ID aplikace pro přihlášení jako aplikace. Hodnotu klíče uložte na místo, odkud ji aplikace může načíst.

    ![Klíč hodnoty](media\azure-stack-solution-hybrid-pipeline\000_06.png)

### <a name="get-the-tenant-id"></a>Získání ID klienta

Jako součást konfigurace koncového bodu služby, služby VSTS vyžaduje **ID klienta** odpovídající AAD adresáře, který vaše razítko zásobník Azure je nasazen na. Použijte následující postup k získání ID klienta.

1. Vyberte **Azure Active Directory**.

    ![Azure Active Directory pro klienta](media\azure-stack-solution-hybrid-pipeline\000_07.png)

2. K získání ID tenanta vyberte v tenantovi Azure AD možnost **Vlastnosti**.

    ![Zobrazit vlastnosti klienta](media\azure-stack-solution-hybrid-pipeline\000_08.png)

3. Zkopírujte **ID adresáře**. Tato hodnota představuje ID tenanta.

    ![ID adresáře](media\azure-stack-solution-hybrid-pipeline\000_09.png)

### <a name="grant-the-service-principal-rights-to-deploy-resources-in-the-azure-stack-subscription"></a>Udělit práva hlavní služby pro nasazení prostředků v předplatném Azure zásobníku

Pro přístup k prostředkům ve vašem předplatném, je nutné přiřadit aplikace k roli. Rozhodněte, jakou roli představuje nejlepší oprávnění pro aplikaci. Další informace o dostupných rolí najdete v tématu [RBAC: integrovaným rolím](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Rozsah můžete nastavit na úrovni předplatné, skupinu prostředků nebo prostředek. Na nižších úrovních oboru dědí oprávnění. Například přidání aplikace do role Čtenář pro skupinu prostředků znamená, že ho může číst skupině prostředků a všechny její prostředky.

1. Přejděte na úroveň oboru, který chcete přiřadit aplikaci. Například přiřazení role v oboru předplatné, vyberte **odběry**.

    ![Vyberte předplatná](media\azure-stack-solution-hybrid-pipeline\000_10.png)

2. V **předplatné**, vyberte Visual Studio Enterprise.

    ![Visual Studio Enterprise](media\azure-stack-solution-hybrid-pipeline\000_11.png)

3. V sadě Visual Studio Enterprise vyberte **řízení přístupu (IAM)**.

    ![Řízení přístupu (IAM)](media\azure-stack-solution-hybrid-pipeline\000_12.png)

4. Vyberte **Přidat**.

    ![Přidat](media\azure-stack-solution-hybrid-pipeline\000_13.png)

5. V **přidat oprávnění**, vyberte roli, které chcete přiřadit k aplikaci. V tomto příkladu **vlastníka** role.

    ![Role vlastníka](media\azure-stack-solution-hybrid-pipeline\000_14.png)

6. Ve výchozím nastavení nejsou aplikace Azure Active Directory zobrazí v dostupných možností. K vyhledání aplikace, je nutné zadat jeho název v **vyberte** pole, které chcete ji najít. Vyberte aplikaci.

    ![Výsledek hledání aplikace](media\azure-stack-solution-hybrid-pipeline\000_16.png)

7. Vyberte **Uložit** k dokončení přiřazení role. Zobrazí aplikace v seznamu Uživatelé s přiřazenou rolí pro tento obor.

### <a name="role-based-access-control"></a>Řízení přístupu na základě rolí

Azure na základě rolí řízení přístupu (RBAC) poskytuje vyladění správy přístupu pro Azure. Pomocí RBAC můžete řídit úroveň přístupu, který uživatelé potřebují pro svou práci. Další informace o řízení přístupu na základě rolí najdete v tématu [spravovat přístup k prostředkům předplatné Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json).

### <a name="vsts-agent-pools"></a>Služby VSTS agenta fondy

Místo samostatně spravovat každý agent, můžete uspořádat agentů do fondů agenta. Fond agenta definuje sdílení hranic pro všechny agenty v tomto fondu. Služby VSTS jsou fondy agenta vymezené služby VSTS účet, což znamená, že je možné sdílet fond agenta v týmové projekty. Další informace o fondech agenta najdete v tématu [vytvořit fondy agenta a fronty](https://docs.microsoft.com/vsts/build-release/concepts/agents/pools-queues?view=vsts).

### <a name="add-a-personal-access-token-pat-for-azure-stack"></a>Přidat osobní přístupový Token (Jan) pro Azure zásobníku

Vytvořte osobní přístup tokenu pro přístup k služby VSTS.

1. Přihlaste se k účtu služby VSTS a vyberte název profilu účtu.
2. Vyberte **spravovat zabezpečení** stránku vytvoření tokenu přístupu.

    ![Přihlášení uživatele](media\azure-stack-solution-hybrid-pipeline\000_17.png)

    ![Vyberte týmového projektu](media\azure-stack-solution-hybrid-pipeline\000_18.png)

    ![Přidat osobní přístupový token](media\azure-stack-solution-hybrid-pipeline\000_18a.png)

    ![Vytvořit token](media\azure-stack-solution-hybrid-pipeline\000_18b.png)

3. Zkopírujte token.

    > [!Note]
    > Uložte informace o tokenu. Tyto informace nejsou uloženy a nezobrazí znovu, když necháte webové stránky.

    ![Osobní přístupový token](media\azure-stack-solution-hybrid-pipeline\000_19.png)

### <a name="install-the-vsts-build-agent-on-the-azure-stack-hosted-build-server"></a>Instalace, které služby VSTS sestavení agenta v zásobníku Azure hostovaný Server sestavení

1. Připojení k serveru sestavení, které jste nasadili na hostiteli zásobník Azure.
2. Stažení a nasazení agenta sestavení jako služby pomocí osobní přístupový token (Jan) a správce virtuálního počítače účet Spustit jako.

    ![Stáhnout agenta sestavení](media\azure-stack-solution-hybrid-pipeline\010_downloadagent.png)

3. Přejděte do složky pro agenta extrahované sestavení. Spustit **run.cmd** souboru z příkazového řádku se zvýšenými oprávněními.

    ![Extrahované sestavení agenta](media\azure-stack-solution-hybrid-pipeline\000_20.png)

    ![Registrace sestavení agenta](media\azure-stack-solution-hybrid-pipeline\000_21.png)

4. Po dokončení run.cmd složky agenta nástroje sestavení doplněné další soubory. Složku s extrahované obsah by měl vypadat asi takto:

    ![Vytvoření složky aktualizace agenta](media\azure-stack-solution-hybrid-pipeline\009_token_file.png)

    Můžete zobrazit agenta ve složce služby VSTS.

## <a name="endpoint-creation-permissions"></a>Oprávnění pro vytvoření koncového bodu

Sestavení Visual Studio Online (VSTO) tak, že vytvoříte koncové body, můžete nasadit aplikace Azure Service zásobník Azure. Služby VSTS se připojí k sestavení agenta, který se připojuje k Azure zásobníku.

![NorthwindCloud ukázkovou aplikaci v VSTO](media\azure-stack-solution-hybrid-pipeline\012_securityendpoints.png)

1. Přihlaste se k VSTO a přejděte na stránku nastavení aplikace.
2. Na **nastavení**, vyberte **zabezpečení**.
3. V **skupiny služby VSTS**, vyberte **koncový bod Creators**.

    ![Koncový bod NorthwindCloud Creators](media\azure-stack-solution-hybrid-pipeline\013_endpoint_creators.png)

4. Na **členy** vyberte **přidat**.

    ![Přidat člena](media\azure-stack-solution-hybrid-pipeline\014_members_tab.png)

5. V **přidání uživatelů a skupin**, zadejte uživatelské jméno a vyberte tohoto uživatele ze seznamu uživatelů.
6. Vyberte **uložit změny**.
7. V **skupiny služby VSTS** seznamu, vyberte **koncový bod správci**.

    ![Koncový bod NorthwindCloud správci](media\azure-stack-solution-hybrid-pipeline\015_save_endpoint.png)

8. Na **členy** vyberte **přidat**.
9. V **přidání uživatelů a skupin**, zadejte uživatelské jméno a vyberte tohoto uživatele ze seznamu uživatelů.
10. Vyberte **uložit změny**.

Teď, když informace o koncovém existuje, je připravený k použití služby VSTS zásobník Azure připojení. Agent sestavení v zásobníku Azure získá pokyny z služby VSTS a pak agenta přenese tak informace koncového bodu pro komunikaci se zásobníkem Azure.

![Sestavení agenta](media\azure-stack-solution-hybrid-pipeline\016_save_changes.png)

## <a name="develop-your-application-build"></a>Vývoj aplikací buildu

V této části kurzu budete:

* Přidání kódu do projektu služby VSTS.
* Vytvořte nasazení nezávislý webové aplikace.
* Konfigurace procesu průběžné nasazování.

> [!Note]
 > Prostředí Azure zásobníku musí správné bitové kopie souhrnné ke spuštění systému Windows Server a SQL Server. Musí mít rovněž nasazené služby App Service. Najdete v dokumentaci služby App Service části "Požadavky" požadavky na Azure zásobníku operátor.

Hybridní CI/CD můžete použít kód aplikace a infrastrukturu kódu. Použití [šablon Azure Resource Manager, například webovou ](https://azure.microsoft.com/resources/templates/) kód aplikace ze služby VSTS k nasazení pro oba cloudy.

### <a name="add-code-to-a-vsts-project"></a>Přidání kódu do projektu služby VSTS

1. Přihlásit se do služby VSTS pomocí účtu, který má práva k vytvoření projektu v Azure zásobníku. Další snímek obrazovky ukazuje, jak se připojit k HybridCICD projektu.

    ![Připojení k projektu](media\azure-stack-solution-hybrid-pipeline\017_connect_to_project.png)

2. **Naklonujte úložiště** ve vytváření a otevírání výchozí webové aplikace.

    ![Klonování úložiště](media\azure-stack-solution-hybrid-pipeline\018_link_arm.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Vytvořit samostatný webové aplikace nasazení pro aplikační služby v obou cloudy

1. Upravit **WebApplication.csproj** souboru: vyberte **Runtimeidentifier** a poté přidejte `win10-x64.` Další informace najdete v tématu [samostatná nasazení](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) dokumentace.

    ![Konfigurace Runtimeidentifier](media\azure-stack-solution-hybrid-pipeline\019_runtimeidentifer.png)

2. Průzkumník týmových projektů slouží ke kontrole kód do služby VSTS.

3. Potvrďte, že byla zaškrtnutá kódu aplikace do sady Visual Studio Team Services.

### <a name="create-the-build-definition"></a>Vytvořit definici sestavení

1. Přihlaste se k služby VSTS pomocí účtu, který můžete vytvořit definici buildu.
2. Přejděte na **sestavit Web Express** stránky pro projekt.

3. V **argumenty**, přidejte **- r win10-x64** kódu. To je potřeba aktivovat samostatná nasazení s .net Core.

    ![Přidat definici sestavení argument](media\azure-stack-solution-hybrid-pipeline\020_publish_additions.png)

4. Spusťte sestavení. [Samostatná nasazení sestavení](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) proces bude publikovat artefakty, pomocí kterých můžete spustit v Azure a Azure zásobníku.

### <a name="use-an-azure-hosted-build-agent"></a>Použití Azure hostované sestavení agenta

Pomocí agenta hostované sestavení v služby VSTS je vhodná možnost pro vytváření a nasazování webových aplikací. Agent Údržba a upgrady jsou automaticky provádí Microsoft Azure, což umožňuje nepřetržitý a bez přerušení vývojového cyklu.

### <a name="configure-the-continuous-deployment-cd-process"></a>Konfigurace procesu průběžné nasazování (CD)

Visual Studio Team Services (služby VSTS) a Team Foundation Server (TFS) zadejte vysoce Konfigurovatelný a spravovat kanál pro verze pro více prostředích, jako je vývoj, pracovní, zajištění kvality (QA) a provozní. Tento proces může zahrnovat, které vyžadují schválení na konkrétní fázích životního cyklu aplikace.

### <a name="create-release-definition"></a>Vytvoření definice verze

Vytvoření verze definice je proces sestavení v posledním kroku vaší aplikace. Tato verze definice slouží k vytvoření verze a nasazení sestavení.

1. Přihlaste se k služby VSTS a přejděte do **sestavení a verze** pro váš projekt.
2. Na **verze** vyberte  **\[ +]** a pak vyberte **vytvořit verze definice**.

   ![Vytvoření definice verze](media\azure-stack-solution-hybrid-pipeline\021a_releasedef.png)

3. Na **vyberte šablonu**, zvolte **nasazení služby Azure App**a potom vyberte **použít**.

    ![Použít šablonu](media\azure-stack-solution-hybrid-pipeline\102.png)

4. Na **přidat artefaktů**, z **zdroj (definice sestavení)** rozevírací nabídky vyberte aplikaci, sestavení cloudu Azure.

    ![Přidání artefaktu](media\azure-stack-solution-hybrid-pipeline\103.png)

5. Na **kanálu** vyberte **fáze 1**, **1 úloh** propojit **zobrazit úlohy prostředí**.

    ![Úlohy v zobrazení kanálu](media\azure-stack-solution-hybrid-pipeline\104.png)

6. Na **úlohy** zadejte Azure jako **název prostředí** a vyberte EP Traders webové AzureCloud z **předplatného Azure** rozevíracího seznamu.

    ![Nastavení proměnných prostředí](media\azure-stack-solution-hybrid-pipeline\105.png)

7. Zadejte **název služby Azure app service**, což je "firma" Další snímku obrazovky.

    ![Název služby App service](media\azure-stack-solution-hybrid-pipeline\106.png)

8. Pro fázi agenta vyberte **hostované VS2017** z **fronty agenta** rozevíracího seznamu.

    ![Hostovaný agent](media\azure-stack-solution-hybrid-pipeline\107.png)

9. V **nasazení služby Azure App Service**, vyberte platném **balíček nebo složku** pro prostředí.

    ![Vyberte balíček nebo složky](media\azure-stack-solution-hybrid-pipeline\108.png)

10. V **vyberte soubor nebo složku**, vyberte **OK** k **umístění**.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\109.png)

11. Uložte všechny změny a vraťte se zpátky a **kanálu**.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\110.png)

12. Na **kanálu** vyberte **přidat artefaktů**a vyberte **NorthwindCloud Traders-plavidlo** z **zdroje (sestavení definice)** rozevíracího seznamu.

    ![Přidat nové artefaktů](media\azure-stack-solution-hybrid-pipeline\111.png)

13. Na **vyberte šablonu**, přidejte další prostředí. Vyberte **nasazení služby Azure App** a pak vyberte **použít**.

    ![Vyberte šablonu](media\azure-stack-solution-hybrid-pipeline\112.png)

14. Zadejte "Azure zásobníku" jako **název prostředí**.

    ![Název prostředí](media\azure-stack-solution-hybrid-pipeline\113.png)

15. Na **úlohy** kartě, najděte a vyberte zásobník Azure.

    ![Prostředí Azure zásobníku](media\azure-stack-solution-hybrid-pipeline\114.png)

16. Z **předplatného Azure** rozevíracího seznamu vyberte "AzureStack Traders plavidla EP" pro koncový bod Azure zásobníku.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\115.png)

17. Zadejte název webové aplikace Azure zásobníku jako **název služby App service**.

    ![Název služby App service](media\azure-stack-solution-hybrid-pipeline\116.png)

18. V části **Výběr agenta**, vyberte "AzureStack - bDouglas Fir" z **fronty agenta** rozevíracího seznamu.

    ![Vyberte agenta](media\azure-stack-solution-hybrid-pipeline\117.png)

19. Pro **nasazení služby Azure App Service**, vyberte platném **balíček nebo složku** pro prostředí. Na **vyberte soubor nebo složku**, vyberte **OK** složky **umístění**.

    ![Vyberte balíček nebo složky](media\azure-stack-solution-hybrid-pipeline\118.png)

    ![Schválit umístění](media\azure-stack-solution-hybrid-pipeline\119.png)

20. Na **proměnná** kartě, vyhledejte proměnné s názvem **VSTS_ARM_REST_IGNORE_SSL_ERRORS**. Nastavte hodnotu proměnné na **true**a nastavte její obor na **zásobník Azure**.

    ![Nakonfigurujte proměnné](media\azure-stack-solution-hybrid-pipeline\120.png)

21. Na **kanálu** vyberte **aktivační událost průběžné nasazování** ikonu NorthwindCloud Traders – webový artefaktů a sadu **aktivační událost průběžné nasazování** na **Povoleno**.  To samé pro artefaktů "NorthwindCloud plavidlem Traders".

    ![Aktivační událost průběžné nasazování sady](media\azure-stack-solution-hybrid-pipeline\121.png)

22. Prostředí Azure zásobníku, vyberte **před nasazením podmínky** ikonu Nastavit aktivační události na **po vydání**.

    ![Aktivační podmínky před nasazením sady](media\azure-stack-solution-hybrid-pipeline\122.png)

23. Uložte všechny provedené změny.

> [!Note]
> Některá nastavení pro vydání úlohy může byla automaticky definována jako [proměnné prostředí](https://docs.microsoft.com/vsts/build-release/concepts/definitions/release/variables?view=vsts#custom-variables) při vytváření definice verze ze šablony. Toto nastavení nelze změnit v nastavení úloh. Tato nastavení v nadřazených položek prostředí však můžete upravit.

## <a name="create-a-release"></a>Vytvořit verzi

Teď, když jste dokončili úpravy definice vydání, je čas ke spuštění nasazení. K tomu musíte vytvořit verzi z definice verzí. Verze, které mohou být vytvořeny automaticky. průběžné nasazování aktivační událost je třeba nastavit v definici verze. To znamená, že změnu zdrojového kódu spustí nového sestavení a, ze které novou verzi. Ale v této části vytvoříte novou verzi ručně.

1. Na **kanálu** kartě, otevřete **verze** rozevírací seznam a vyberte **vytvořit verzi**.

    ![Vytvořit verzi](media\azure-stack-solution-hybrid-pipeline\200.png)

2. Zadejte popis pro tuto verzi, zkontrolujte, jestli jsou vybrané správné artefaktů a potom zvolte **vytvořit**. Po chvíli se zobrazí nápis informující o indikující, že byla vytvořena nová verze a verze název se zobrazí odkaz. Vyberte na odkaz zobrazíte souhrnnou stránku verze.

    ![Informační zpráva vytvoření verze](media\azure-stack-solution-hybrid-pipeline\201.png)

3. Souhrnná stránka verze obsahuje podrobnosti o verzi. Následující snímek obrazovky pro "Verze-2" **prostředí** část ukazuje **stav nasazení** pro Azure jako "Probíhající" a stav zásobník Azure je "bylo úspěšně DOKONČENO". Když stav nasazení pro prostředí Azure změní "SUCCEEDED", se zobrazí nápis informující o označující, že je verze připravené ke schválení. Při nasazení čeká na vyřízení nebo se nezdařila, modrá **(i)** informační ikona, která se zobrazí. Najeďte myší na ikonu zobrazíte automaticky otevírané okno, který obsahuje důvod zpoždění nebo selhání.

    ![Souhrnná stránka verze](media\azure-stack-solution-hybrid-pipeline\202.png)

Ostatních zobrazeních, jako je například seznam uvolní, se také zobrazí ikona, která označuje, že se čeká na schválení. Automaticky otevírané okno pro tato ikona se zobrazuje název prostředí a další podrobnosti týkající se nasazení. Je snadné pro správce najdete v části zjistit celkový průběh verze a zjistit, které verze čekají na schválení.

### <a name="monitor-and-track-deployments"></a>Monitorování a sledování nasazení

Tato část popisuje, jak můžete monitorovat a sledovat vaše nasazení. Verze pro nasazení dva weby Azure App Services poskytuje dobrým příkladem.

1. Na stránce Souhrn "Verze-2" vyberte **protokoly**. Během nasazení Tato stránka zobrazuje v za provozu protokolu od agenta. V levém podokně se zobrazuje stav jednotlivých operací v nasazení pro každé prostředí.

    Můžete zvolit ikonu osoby v **akce** sloupec o schválení před nasazením nebo po nasazení najdete v tématu, kdo schváleny (nebo odmítnuty) nasazení a zpráva poskytnou.

2. Po dokončení nasazení celý soubor protokolu se zobrazí v pravém podokně. Můžete vybrat libovolný **krok** v levém podokně naleznete v souboru protokolu pro jeden krok, jako je "Úlohy inicializace". Umožňuje zobrazit jednotlivé protokoly usnadňuje trasování a ladění součástí celkové nasazení. Můžete také **Uložit** soubor protokolu pro krok, nebo **stáhnout všechny protokoly jako zip**.

    ![Protokoly verze](media\azure-stack-solution-hybrid-pipeline\203.png)

3. Otevřete **Souhrn** karta Obecné informace o verzi. Toto zobrazení ukazuje údaje o sestavení, prostředí, který byl nasazen na, stav nasazení a další informace o verzi.

4. Vyberte odkaz prostředí (**Azure** nebo **zásobník Azure**) Chcete-li zobrazit informace o existující a čeká se na nasazení pro konkrétní prostředí. Tato zobrazení můžete použít jako rychlý způsob, jak ověřit, že ve stejném sestavení byl nasazen na obou prostředích.

5. Otevřete **produkční aplikace nasazená** v prohlížeči. Například otevřete adresu URL pro web Azure App Services `http://[your-app-name].azurewebsites.net`.

## <a name="next-steps"></a>Další postup

* Další informace o vzorů cloudu Azure, najdete v části [vzory návrhu cloudové](https://docs.microsoft.com/azure/architecture/patterns).
