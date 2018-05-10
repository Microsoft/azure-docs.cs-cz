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
ms.date: 05/07/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 49a80805c976e5584bb158965583a03eda68cc46
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="tutorial-deploy-apps-to-azure-and-azure-stack"></a>Kurz: Nasazení aplikací do Azure a Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Kanál hybridní nepřetržité integrace/průběžné doručování (CI/CD) umožňuje vytvoření, testování a nasazení aplikace do víc cloudů.  V tomto kurzu vytvoříte ukázkové prostředí:
 
> [!div class="checklist"]
> * Zahájení nové sestavení podle potvrzení kód do úložiště Visual Studio Team Services (VSTS).
> * Nově vytvořený kód automaticky nasaďte do globální Azure pro testování přijetí uživateli.
> * Po uplynutí kódu testování, automaticky nasaďte do Azure zásobníku.

### <a name="about-the-hybrid-delivery-build-pipe"></a>O hybridní doručení vytvoření kanálu

Kontinuity nasazení aplikací, zabezpečení a spolehlivost je nezbytně nutné, aby vaše organizace a pro váš vývojový tým důležité. S hybridní CI/CD kanál může konsolidovat kanály mezi místním prostředím a veřejného cloudu. Můžete změnit umístění, aniž by se přepnula vaší aplikace.

Tento přístup umožňuje udržovat konzistentní sady nástrojů pro vývoj. Konzistentní nástroje napříč veřejného cloudu Azure a prostředí Azure zásobníku místní znamená, že je mnohem jednodušší, abyste mohli implementovat postup dev CI/CD. Aplikace a služby, které jsou nasazené v Azure nebo Azure zásobníku se zaměňovat a má stejný kód můžete spustit v některém umístění s využitím místní a veřejný cloud funkce a možnosti.

Další informace o:
 - [Co je průběžnou integraci?](https://www.visualstudio.com/learn/what-is-continuous-integration/)
 - [Co je nastavené průběžné doručování?](https://www.visualstudio.com/learn/what-is-continuous-delivery/)


## <a name="prerequisites"></a>Požadavky

Musíte mít několik součástí vytvářet hybridní CI/CD kanálu. Jejich zajištění může trvat nějakou dobu připravit.
 
 - Partnerem Azure OEM nebo hardwaru může nasadit provozní zásobník Azure a všichni uživatelé mohou nasazení služby Azure zásobníku Development Kit (ASDK). 
 - Operátor zásobník Azure musí také nasadit App Service, vytvářet plány a nabízí, vytvořit odběr klienta a přidat bitovou kopii systému Windows Server 2016.

Pokud již máte některé z těchto součástí, ujistěte se, že splňují požadavky před zahájením.

Toto téma také předpokládá, že máte některé informace o Azure a Azure zásobníku. Pokud chcete další informace, než budete pokračovat, ujistěte se, že jste začínat tato témata:


V tomto kurzu také předpokládá, že máte některé informace o Azure a Azure zásobníku. 

Pokud chcete další informace, než budete pokračovat, můžete spustit v těchto tématech:
 - [Úvod do Azure](https://azure.microsoft.com/overview/what-is-azure/)
 - [Klíčové koncepty Azure zásobníku](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure"></a>Azure

 - Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

 - Vytvoření [webová aplikace](https://docs.microsoft.com/azure/app-service/app-service-web-overview) v Azure. Poznamenejte si nové URL webové aplikace, jako je později použít.

Azure Stack
 - Pomocí služby Azure zásobníku integrované systému nebo nasadit Azure zásobníku Development Kit (ASDK) vedou následující odkazy:
    - Najdete podrobné pokyny k nasazení ASDK v "[kurz: nasazení ASDK pomocí Instalační služby](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-deploy)"
    - Můžete automatizovat řadu kroků po nasazení vaší ASDK pomocí následujícího skriptu prostředí PowerShell [ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 ).

    > [!note]  
    > Instalace ASDK trvá sedm hodin k dokončení, takže Plánujte odpovídajícím způsobem.

 - Nasazení [služby App Service](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) služby PaaS do protokolů Azure. 
 - Vytvoření [plán nebo nabídky](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) v prostředí Azure zásobníku. 
 - Vytvoření [klienta předplatné](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) v prostředí Azure zásobníku. 
 - Vytvoření webové aplikace v rámci předplatného klienta. Poznamenejte si adresu URL nové aplikace webového pro později.
 - Nasazení služby VSTS virtuálního počítače, stále v rámci předplatného klienta.
 - Windows Server 2016 virtuální počítač se vyžaduje rozhraní .NET 3.5. Tento virtuální počítač bude založený na vaše zásobník Azure jako agent privátní sestavení. 

### <a name="developer-tools"></a>Vývojářské nástroje

 - Vytvoření [prostoru služby VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services). Proces registrace vytvoří projekt s názvem **MyFirstProject**.
 - [Nainstalovat Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) a [Přihlaste se do služby VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services).
 - Připojení k projektu a [klonovat místně](https://www.visualstudio.com/docs/git/gitquickstart).
 
 > [!note]  
 > Budete potřebovat zásobník Azure s správné obrázky souhrnné ke spuštění (Windows Server a SQL) a mít nasazené služby App Service.
 
## <a name="prepare-the-private-build-and-release-agent-for-visual-studio-team-services-integration"></a>Příprava privátní sestavení a verze agenta pro integraci produktů Visual Studio Team Services

### <a name="prerequisites"></a>Požadavky

Visual Studio Team Services (VSTS) ověřuje vůči správce Azure Resource Manager pomocí objektu služby. Služby VSTS moct zřídit prostředky v předplatné Azure zásobníku vyžaduje stav Přispěvatel.

Následují základní kroky, které je potřeba nakonfigurovat tak, aby takové ověřovat:

1. Měly by se vytvořit objekt služby nebo může použít existující šablonu.
2. Ověřovací klíče musí vytvořit pro objekt služby.
3. Musí se ověřit pomocí řízení přístupu na základě Role, které chcete povolit SPN být součástí role Přispěvatel na předplatné Azure zásobníku.
4. Nové definice služby v služby VSTS musí být vytvořen pomocí koncové body Azure zásobníku a také informace o SPN.

### <a name="service-principal-creation"></a>Vytvoření objektu služby

Odkazovat [vytvoření instančního objektu](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) pokyny k vytvoření instančního objektu a zvolte webové aplikace rozhraní API pro typ aplikace.

### <a name="access-key-creation"></a>Vytvoření klíče přístup

Objekt služby vyžaduje klíče pro ověřování, postupujte podle kroků v této části ke generování klíče.


1. V Azure Active Directory vyberte z **Registrace aplikací** svou aplikaci.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\000_01.png)

2.  Poznamenejte si hodnotu **ID aplikace**. Tuto hodnotu použijete při konfiguraci koncového bodu služby v služby VSTS.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\000_02.png)

3. Pokud chcete generovat ověřovací klíč, vyberte **Nastavení**.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\000_03.png)

4. Pokud chcete generovat ověřovací klíč, vyberte **Klíče**.
 
    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\000_04.png)

5. Zadejte popis klíče a jeho dobu platnosti. Až budete hotovi, vyberte **Uložit**.
 
    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\000_05.png)

    Jakmile klíč uložíte, zobrazí se jeho hodnota. Zkopírujte si ji, protože později už klíč nepůjde načíst. Zadáte **hodnotu klíče** s ID aplikace pro přihlášení jako aplikace. Hodnotu klíče uložte na místo, odkud ji aplikace může načíst.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\000_06.png)

### <a name="get-tenant-id"></a>Získání ID tenanta

Jako součást konfigurace koncového bodu služby, služby VSTS vyžaduje **ID klienta** odpovídající AAD adresáře, který vaše razítko Azure zásobníku byl nasazen na. Postupujte podle kroků v této části získat ID klienta.

1. Vyberte **Azure Active Directory**.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\000_07.png)

2. K získání ID tenanta vyberte v tenantovi Azure AD možnost **Vlastnosti**.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\000_08.png)
 
3. Zkopírujte **ID adresáře**. Tato hodnota představuje ID tenanta.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\000_09.png)

### <a name="grant-the-service-principal-rights-to-deploy-resources-in-the-azure-stack-subscription"></a>Udělit práva hlavní služby pro nasazení prostředků v předplatném Azure zásobníku 

Pro přístup k prostředkům ve vašem předplatném, je nutné přiřadit aplikace k roli. Rozhodněte, jakou roli představuje správná oprávnění pro aplikaci. Další informace o dostupných rolí najdete v tématu [RBAC: integrovaným rolím](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Rozsah můžete nastavit na úrovni předplatné, skupinu prostředků nebo prostředek. Na nižších úrovních oboru dědí oprávnění. Například přidání aplikace do role Čtenář pro skupinu prostředků znamená, že ho může číst skupině prostředků a všechny prostředky, které obsahuje.

1. Přejděte na úroveň oboru, který chcete přiřadit aplikaci. Například přiřazení role v oboru předplatné, vyberte **odběry**. Místo toho můžete třeba vybrat skupinu prostředků nebo prostředek.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\000_10.png)

2. Vyberte **předplatné** (skupinu prostředků nebo prostředek) přiřazení aplikace k.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\000_11.png)

3. Vyberte **přístup k ovládacímu prvku (IAM)**.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\000_12.png)

4. Vyberte **Přidat**.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\000_13.png)

5. Vyberte roli, kterou chcete přiřadit k aplikaci. Na následujícím obrázku **vlastníka** role.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\000_14.png)

6. Ve výchozím nastavení nejsou aplikace Azure Active Directory zobrazí v dostupných možností. Najít aplikaci, musíte **zadejte název** do pole hledání. Vyberte ho.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\000_16.png)

7. Vyberte **Uložit** k dokončení přiřazení role. Zobrazí aplikace v seznamu Uživatelé s přiřazenou rolí pro tento obor.

### <a name="role-based-access-control"></a>Řízení přístupu na základě rolí

Azure na základě rolí řízení přístupu (RBAC) umožňuje vyladění správy přístupu pro Azure. Pomocí řízení přístupu na základě role v Azure můžete uživatelům poskytnout pouze takovou úroveň přístupu, kterou potřebují k provádění svých úloh. Další informace o řízení přístupu na základě rolí najdete v tématu [spravovat přístup k prostředkům předplatné Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json).

### <a name="vsts-agent-pools"></a>Služby VSTS agenta fondy

Místo samostatně spravovat každý agent, uspořádání agentů do fondů agenta. Fond agenta definuje sdílení hranic pro všechny agenty v tomto fondu. Služby VSTS agent fondy jsou vymezené k účtu služby VSTS; Proto je možné sdílet fond agenta v týmové projekty. Kurz týkající se postup vytvoření fondu služby VSTS agenta a další informace najdete v tématu [vytvořit fondy agenta a fronty](https://docs.microsoft.com/vsts/build-release/concepts/agents/pools-queues?view=vsts).

### <a name="add-a-personal-access-token-pat-for-azure-stack"></a>Přidat osobní přístupový token (Jan) pro Azure zásobníku

1. Přihlaste se k účtu služby VSTS a vyberte název profilu účtu.
2. Vyberte **spravovat zabezpečení** stránku vytvoření tokenu přístupu.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\000_17.png)

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\000_18.png)

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\000_18a.png)

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\000_18b.png)

3. Zkopírujte token.
    
    > [!note]  
    > Získejte informace o tokenu. Nebude se zobrazovat znovu po opuštění této obrazovce. 
    
    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\000_19.png)
    

### <a name="install-the-vsts-build-agent-on-the-azure-stack-hosted-build-server"></a>Instalace, které služby VSTS sestavení agenta v zásobníku Azure hostovaný Server sestavení

1.  Připojení k serveru sestavení, které jste nasadili na hostiteli zásobník Azure.

2.  Stažení a nasazení agenta sestavení jako služby pomocí osobní přístupový token (Jan) a správce virtuálního počítače účet Spustit jako.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\010_downloadagent.png)

3. Přejděte do složky agenta extrahované sestavení. Spustit **run.cmd** souboru z příkazového řádku se zvýšenými oprávněními. 

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\000_20.png)

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\000_21.png)

4.  Po dokončení run.cmd složku s extrahované obsah by měl vypadat asi takto:

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\009_token_file.png)

    Nyní můžete vidět agenta ve složce služby VSTS.

## <a name="endpoint-creation-permissions"></a>Oprávnění pro vytvoření koncového bodu

Uživatelé mohou vytvářet koncové body, takže VSTO sestavení můžete nasadit aplikace Azure Service do zásobníku. Služby VSTS se připojí k sestavení agenta, který pak připojí zásobníkem Azure. 

![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\012_securityendpoints.png)

1. Na **nastavení** nabídce vyberte možnost **zabezpečení**.
2. V **skupiny služby VSTS** seznamu na levé straně vyberte **koncový bod Creators**. 

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\013_endpoint_creators.png)

3. Na **členy** vyberte **+ přidat**. 

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\014_members_tab.png)

4. Zadejte uživatelské jméno a ze seznamu vyberte tohoto uživatele.
5. Klikněte na tlačítko **uložit změny**.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\015_save_endpoint.png)

6. V **skupiny služby VSTS** seznamu na levé straně vyberte **koncový bod správci**.
7. Na **členy** vyberte **+ přidat**.
8. Zadejte uživatelské jméno a ze seznamu vyberte tohoto uživatele.
9. Klikněte na tlačítko **uložit změny**.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\016_save_changes.png)

    Agent sestavení v zásobníku Azure získá pokyny z služby VSTS, který pak přenese tak informace koncového bodu pro komunikaci se zásobníkem Azure. 
    
    Služby VSTS připojení zásobník Azure je nyní připravena.

## <a name="develop-your-application"></a>Vývoj aplikace

Nastavení hybridní CI/CD a po nasazení webové aplikace do Azure a Azure zásobníku automaticky změny do obou cloudy.

> [!note]  
> Budete potřebovat zásobník Azure s správné obrázky souhrnné ke spuštění (Windows Server a SQL) a mít nasazené služby App Service. Najdete v dokumentaci služby App Service části "Požadavky" požadavky na Azure zásobníku operátor.

### <a name="add-code-to-vsts-project"></a>Přidejte do projektu služby VSTS kód

1. Přihlaste se k sadě Visual Studio pomocí účtu, který má práva pro vytvoření projektu v Azure zásobníku.

    Hybridní CI/CD můžete použít kód aplikace a infrastrukturu kódu. Použití [šablon Azure Resource Manager, například webovou ](https://azure.microsoft.com/resources/templates/) kód aplikace ze služby VSTS pro oba cloudy.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\017_connect_to_project.png)

2. **Naklonujte úložiště** ve vytváření a otevírání výchozí webové aplikace.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\018_link_arm.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Vytvořit samostatný webové aplikace nasazení pro aplikační služby v obou cloudy

1. Upravit **WebApplication.csproj** souboru: vyberte **Runtimeidentifier** a přidejte `win10-x64.` Další informace najdete v tématu [samostatná nasazení](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) dokumentace .

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\019_runtimeidentifer.png)

2. Zkontrolujte kód do služby VSTS pomocí Team Explorer.

3. Potvrďte, že byl vrácen kód aplikace do sady Visual Studio Team Services. 

### <a name="create-the-build-definition"></a>Vytvořit definici sestavení

1. Přihlaste se k služby VSTS potvrďte schopnost vytvářet definice sestavení.

2. Přidat **- r win10-x64** kódu. To je potřeba aktivovat samostatná nasazení s .net Core. 

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\020_publish_additions.png)

3. Spusťte sestavení. [Samostatná nasazení sestavení](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) proces bude publikovat artefakty, pomocí kterých můžete spustit v Azure a Azure zásobníku.

### <a name="using-an-azure-hosted-agent"></a>Pomocí Azure hostované agenta

Použití v služby VSTS hostovaného agenta je vhodná možnost pro sestavení a nasazení webové aplikace. Údržba a upgrady jsou automaticky provádí Microsoft Azure, povolení potom, bez přerušení vývoj, testování a nasazení.

### <a name="manage-and-configure-the-continuous-deployment-cd-process"></a>Správa a konfigurace procesu nasazení průběžné (CD)

Visual Studio Team Services (služby VSTS) a Team Foundation Server (TFS) poskytovat vysoce Konfigurovatelný a spravovat kanál pro verze pro více prostředích, jako je vývoj, pracovní QA a provozní prostředí; včetně, které vyžadují schválení na konkrétní fázích.

### <a name="create-release-definition"></a>Vytvoření definice verze

![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\021a_releasedef.png)

1. Vyberte  **\[ +]** přidat novou verzi v části **verzích karta** na stránce sestavení a verze VSO.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\102.png)

2. Použít **nasazení služby Azure App** šablony.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\103.png)

3. V rozevírací nabídce Přidat artefaktů **přidat artefaktu** cloudu Azure sestavení aplikace.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\104.png)

4. V části karty kanálu, vyberte **fáze**, **úloh** odkaz prostředí a nastavovat hodnoty prostředí cloudu Azure.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\105.png)

5. Nastavte **název prostředí** a vyberte Azure **předplatné** pro koncový bod cloudu Azure.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\106.png)

6. V části název prostředí, nastavte požadované **název služby Azure app service**.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\107.png)

7. Zadejte **hostované VS2017** pod fronty agenta pro cloudu Azure hostované prostředí.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\108.png)

8. V nabídce nasazení služby Azure App Service, vyberte platném **balíček nebo složku** pro prostředí. Vyberte **OK** k **umístění složky**.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\109.png)

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\110.png)

9. Uložte všechny změny a vraťte se zpátky a **verze kanálu**.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\111.png)

10. Přidat **nové artefaktů** výběr sestavení pro aplikaci Azure zásobníku.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\112.png)

11. Přidejte jeden další použití prostředí **nasazení služby Azure App**.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\113.png)

12. Název nové prostředí **zásobník Azure**.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\114.png)

13. Najít prostředí zásobníku Azure v části **úloh** kartě.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\115.png)

14. Vyberte **předplatné** pro koncový bod Azure zásobníku.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\116.png)

15. Nastavte název webové aplikace Azure zásobníku jako **název služby App service**.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\117.png)

16. Vyberte **agenta Azure zásobníku**.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\118.png)

17. V části nasazení Azure App Service vyberte části platném **balíček nebo složku** pro prostředí. Kliknutím na tlačítko OK **umístění složky**.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\119.png)

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\120.png)

18. Proměnné kartě přidat proměnné s názvem **VSTS_ARM_REST_IGNORE_SSL_ERRORS**, nastavte ho na hodnotu jako **true**a obor pro **zásobník Azure**.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\121.png)

19. Vyberte **souvislý** nasazení aktivovat ikonu v obou artefaktů a povolte aktivační událost Continues nasazení.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\122.png)

20. Vyberte **před nasazením** podmínky ikona ve službě azure zásobníku prostředí a nastavte aktivační události na **po vydání**.

21. Všechny změny uložte.

> [!note]  
> Některá nastavení pro úlohy může byla automaticky definována jako [proměnné prostředí](https://docs.microsoft.com/vsts/build-release/concepts/definitions/release/variables?view=vsts#custom-variables) při vytváření definice verze ze šablony. Toto nastavení nelze změnit v nastavení úloh; Místo toho musíte vybrat nadřazené položky prostředí pro úpravu těchto nastavení.

## <a name="create-a-release"></a>Vytvořit verzi

Teď, když jste dokončili úpravy definice vydání, je čas ke spuštění nasazení. K tomu musíte vytvořit verzi z definice verzí. Verze, které mohou být vytvořeny automaticky. průběžné nasazování aktivační událost je třeba nastavit v definici verze. To znamená, že změnu zdrojového kódu spustí nového sestavení a, ze které novou verzi. Ale v této části vytvoříte novou verzi ručně.

1. Otevřete **verze** rozevírací seznam a vyberte **vytvořit verzi**.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\200.png)
 
2. Zadejte popis pro tuto verzi, zkontrolujte, zda jsou vybrané správné artefakty a potom zvolte **vytvořit**. Po chvíli se zobrazí nápis informující o označující, zda byla vytvořena nová verze. Vyberte odkaz (název vydání).

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\201.png)
 
3. Souhrnná stránka verze otevře s podrobnostmi verze. V **prostředí** části, zobrazí se stav nasazení pro prostředí "QA" změnit "Probíhající" na "SUCCEEDED" a v tomto bodě nápis informující o zobrazí se okno oznamující, že verze je nyní čekání na schválení. Při nasazení do prostředí čeká na vyřízení nebo se nezdařila, modrá (i) se zobrazí ikona informace. Přejděte na zjistíte, automaticky otevírané okno obsahující z důvodu.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\202.png)

Ostatních zobrazeních, jako je například seznam verzí, také zobrazení ikonu, která označuje schválení čeká na vyřízení. Ikonu zobrazí automaticky otevírané okno obsahující název prostředí a další podrobnosti při bodu k němu. To usnadňuje správce verzí, které čekají na schválení, jakož i zjistit celkový průběh všech verzí.

### <a name="monitor-and-track-deployments"></a>Monitorování a sledování nasazení

V této části se zobrazí, jak můžete monitorovat a sledování nasazení – v tomto příkladu na dva weby Azure App Services - od verze, které jste vytvořili v předchozí části.

1. Na stránce shrnutí verzí, vyberte **protokoly** odkaz. Během nasazení probíhající, tato stránka zobrazuje protokol za provozu z agenta a v levém podokně, údaje o stavu jednotlivých operací ve procesu nasazení pro každé prostředí.

    Zvolte ikonu v **akce** sloupec o schválení před nasazením nebo po nasazení můžete zobrazit podrobnosti o kdo schválit (ani odmítnout) nasazení a zpráva Tento uživatel zadal.

2. Po dokončení nasazení celý soubor protokolu se zobrazí v pravém podokně. Vyberte některé z **zpracovat kroky** v levém podokně zobrazí obsah souboru pro tento krok jenom do protokolu. To usnadňuje trasování a ladění jednotlivé části celkové nasazení. Můžete také stáhněte jednotlivých protokolových souborů nebo zip všechny soubory protokolu, z ikony a odkazy na stránce.

    ![Alternativní Text](media\azure-stack-solution-hybrid-pipeline\203.png)
 
3. Otevřete **Souhrn** zjistit celkový podrobností verze. Zobrazuje podrobnosti o sestavení a prostředí, které bylo nasazeno do – stav nasazení a další informace o verzi.

4. Vyberte jednotlivé **prostředí odkazy** zobrazíte další podrobnosti o existující a čeká se na nasazení pro konkrétní prostředí. Tyto stránky můžete použít k ověření, že ve stejném sestavení byl nasazen na obou prostředích.

5. Otevřete **produkční aplikace nasazená** ve vašem Procházet. Například pro web Azure App Services, z adresy URL `http://[your-app-name].azurewebsites.net`.

## <a name="next-steps"></a>Další postup

- Další informace o vzorů cloudu Azure, najdete v části [vzory návrhu cloudové](https://docs.microsoft.com/azure/architecture/patterns).
