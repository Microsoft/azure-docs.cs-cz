---
title: Nasazení Azure Stack App Service v konfiguraci s vysokou dostupností | Dokumentace Microsoftu
description: Zjistěte, jak nasadit službu App Service ve službě Azure Stack pomocí konfiguraci s vysokou dostupností.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: ''
ms.date: 03/23/2019
ms.author: jeffgilb
ms.reviewer: anwestg
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: 1c105548f19994c4ca0ce161eedcfe11736864c7
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370019"
---
# <a name="deploy-app-service-in-a-highly-available-configuration"></a>Nasadit službu App Service v konfiguraci s vysokou dostupností

Tento článek ukazuje, jak nasadit službu App Service pro službu Azure Stack v konfiguraci s vysokou dostupností pomocí položky marketplace služby Azure Stack. Kromě položek z marketplace k dispozici, toto řešení také využívá [služby App Service – sdílení souborů – systému SQL Server – ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) šablony Quickstart pro Azure Stack. Tato šablona umožňuje automatizovat vytvoření infrastrukturu s vysokou dostupností k hostování poskytovatele prostředků App Service. App Service nainstaluje na tuto infrastrukturu virtuálních počítačů s vysokou dostupností. 

## <a name="deploy-the-highly-available-app-service-infrastructure-vms"></a>Nasazení služby App Service infrastrukturu s vysokou dostupností virtuálních počítačů
[Služby App Service – sdílení souborů – systému SQL Server – ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) šablony Quickstart pro Azure Stack zjednodušuje nasazení služby App Service v konfiguraci s vysokou dostupností. Musí být nasazené v rámci předplatného výchozí zprostředkovatel. 

Pokud se použije k vytvoření vlastních prostředků ve službě Azure Stack, vytvoří šablona dílčí:
- Virtuální síť a požadované podsítě.
- Skupiny zabezpečení sítě pro souborový server, SQL Server a podsítě služby Active Directory Domain Services (AD DS).
- Účty služby Storage pro disky virtuálních počítačů a nastavit cloudovou kopii clusteru.
- Jedna interní nástroj pro vyrovnávání zatížení virtuálních počítačů SQL s privátní IP Adresou vázán k naslouchacímu procesu SQL AlwaysOn.
- Tři skupin dostupnosti pro služby AD DS, cluster souborových serverů a SQL cluster.
- Dva uzly clusteru SQL.
- Clusteru souborových serverů se dvěma uzly.
- Two domain controllers.

### <a name="required-azure-stack-marketplace-items"></a>Požadované položky marketplace služby Azure Stack
Před použitím této šablony, ujistěte se, že následující [položky marketplace služby Azure Stack](azure-stack-marketplace-azure-items.md) jsou k dispozici ve vaší instanci služby Azure Stack:

- Bitové kopie systému Windows Server 2016 Datacenter jádro (pro AD DS a soubor server virtuální počítače)
- SQL Server 2016 SP2 v systému Windows Server 2016 (Enterprise)
- Nejnovější rozšíření SQL IaaS 
- Nejnovější PowerShell Desired State Configuration rozšíření 

> [!TIP]
> Kontrola [soubor readme šablony](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) na Githubu najdete další podrobnosti o požadavcích šablony a výchozí hodnoty. 

### <a name="deploy-the-app-service-infrastructure"></a>Nasazení infrastruktury služby App Service
Postupujte podle kroků v této části k vytvoření vlastního nasazení pomocí **služby App Service – sdílení souborů – systému SQL Server – ha** šablony Quickstart pro Azure Stack.

1. [!INCLUDE [azs-admin-portal](../../includes/azs-admin-portal.md)]

2. Vyberte **\+** **vytvořit prostředek** > **vlastní**a potom **nasazení šablony**.

   ![Nasazení vlastní šablony](media/app-service-deploy-ha/1.png)


3. Na **vlastní nasazení** okně vyberte **úpravy šablony** > **šablonu pro rychlý Start** a pak použijte rozevírací seznam dostupných vlastních šablon pro Vyberte **služby App Service – sdílení souborů – systému SQL Server – ha** šablony, klikněte na tlačítko **OK**a potom **Uložit**.

   ![Vyberte šablonu služby App Service – sdílení souborů – systému SQL Server – ha rychlý start](media/app-service-deploy-ha/2.png)

4. Na **vlastní nasazení** okně vyberte **upravit parametry** a přejděte dolů k Zkontrolujte výchozí hodnoty šablony. Tyto hodnoty upravit podle potřeby poskytují všechny informace o povinný parametr a potom klikněte na **OK**.<br><br> Minimálně zadejte pro parametry ADMINPASSWORD FILESHAREOWNERPASSWORD, FILESHAREUSERPASSWORD, SQLSERVERSERVICEACCOUNTPASSWORD a SQLLOGINPASSWORD složitá hesla.
    
   ![Upravit parametry vlastní nasazení](media/app-service-deploy-ha/3.png)

5. Na **vlastní nasazení** okně zkontrolujte **výchozí předplatné poskytovatele** je zvolen jako předplatné, které chce použít a poté vytvořit novou skupinu prostředků nebo vyberte existující skupinu prostředků, pro vlastní nasazení.<br><br> Potom vyberte umístění skupiny prostředků (**místní** pro instalace ASDK) a potom klikněte na tlačítko **vytvořit**. Nasazení vlastního nastavení budou ověřena, před zahájením nasazování šablony.

    ![Vytvoření vlastního nasazení](media/app-service-deploy-ha/4.png)

6. V portálu pro správu, vyberte **skupiny prostředků** a klikněte na název skupiny prostředků vytvořené pro vlastní nasazení (**app-service-ha** v tomto příkladu). Zobrazte stav nasazení tak, aby Ujistěte se, že všechna nasazení byly úspěšně dokončeny.

   > [!NOTE]
   > Nasazení šablony bude trvat přibližně hodinu.

   [![](media/app-service-deploy-ha/5-sm.png "Zkontrolujte stav nasazení šablony")](media/app-service-deploy-ha/5-lg.png#lightbox)


### <a name="record-template-outputs"></a>Vypíše šablony záznamu
Za šablony nasazení dokončí úspěšně, záznamu, který vypíše nasazení šablony. Je potřeba poskytnout tyto informace při spuštění Instalační služby App Service. 

Zajistěte, aby že každá z těchto hodnot výstupu zaznamenat:
- FileSharePath
- FileShareOwner
- FileShareUser
- SQLserver
- SQLuser

Použijte následující postup zjistit výstupní hodnoty šablony:

1. [!INCLUDE [azs-admin-portal](../../includes/azs-admin-portal.md)]

2. V portálu pro správu, vyberte **skupiny prostředků** a klikněte na název skupiny prostředků vytvořené pro vlastní nasazení (**app-service-ha** v tomto příkladu). 

3. Klikněte na tlačítko **nasazení** a vyberte **Microsoft.Template**.

    ![Microsoft.Template nasazení](media/app-service-deploy-ha/6.png)

4. Po výběru **Microsoft.Template** nasazení, vyberte **výstupy** a zaznamenejte výstupní parametr šablony. Tyto informace nutné při nasazování služby App Service.

    ![Výstupní parametr](media/app-service-deploy-ha/7.png)


## <a name="deploy-app-service-in-a-highly-available-configuration"></a>Nasadit službu App Service v konfiguraci s vysokou dostupností
Postupujte podle kroků v této části k nasazení služby App Service pro službu Azure Stack v konfiguraci s vysokou dostupností na základě [služby App Service – sdílení souborů – systému SQL Server – ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) šablony Quickstart pro Azure Stack. 

Po instalaci poskytovatele prostředků App Service, můžete jej zahrnout do nabídky a plány. Uživatelé můžou potom přihlásit k odběru službu a začněte vytvářet aplikace.

> [!IMPORTANT]
> Předtím, než spustíte instalační program zprostředkovatele prostředků, ujistěte se, že jste si přečetli zpráva k vydání verze, které doprovázejí jednotlivé verze služby App Service, najdete informace o nové funkce, opravy a známých problémech, které by mohly ovlivnit vaše nasazení.

### <a name="prerequisites"></a>Požadavky
Předtím, než spustíte instalační program služby App Service, je potřeba, jak je popsáno v několika kroků [před zahájením práce se službou App Service v Azure stacku článku](azure-stack-app-service-before-you-get-started.md):

> [!TIP]
> Ne všechny kroky popsané v před zahájením práce článku jsou povinné, protože šablona nasazení konfiguruje infrastrukturu virtuálních počítačů za vás. 

- [Stáhněte si instalační program a pomocné skripty služby App Service](azure-stack-app-service-before-you-get-started.md#download-the-installer-and-helper-scripts).
- [Stáhněte si nejnovější rozšíření vlastních skriptů na webu Marketplace služby Azure Stack](azure-stack-app-service-before-you-get-started.md#syndicate-the-custom-script-extension-from-the-marketplace).
- [Generovat požadované certifikáty](azure-stack-app-service-before-you-get-started.md#get-certificates).
- Vytvoření ID aplikace založená na zprostředkovateli identifikovat, který jste zvolili pro službu Azure Stack. ID aplikace můžete provést buď [Azure AD](azure-stack-app-service-before-you-get-started.md#create-an-azure-active-directory-application) nebo [Active Directory Federation Services](azure-stack-app-service-before-you-get-started.md#create-an-active-directory-federation-services-application) a zaznamenejte ID aplikace.
- Ujistěte se, že jste přidali image Windows serveru 2016 Datacenter do Tržiště Azure Stack. Toto je požadována pro instalaci služby App Service.

### <a name="deploy-app-service-in-highly-available-configuration"></a>Nasadit službu App Service v konfiguraci s vysokou dostupností
Instalace poskytovatele prostředků App Service má aspoň hodinu. Čas potřebný, závisí na tom, kolik role instance můžete nasadit. Během nasazování Instalační program spustí následující úlohy:

- Vytvořte kontejner objektů blob v zadaném účtu úložiště Azure Stack.
- Vytvořte záznamy a zóny DNS pro službu App Service.
- Zaregistrujte poskytovatele prostředků App Service.
- Registrace položek galerie služby App Service.

Nasazení poskytovatele prostředků App Service, postupujte podle těchto kroků:

1. Spusťte stažený instalační program služby App Service (**appservice.exe**) jako správce na počítači, který můžete přístup ke koncovému bodu Azure zásobníku správce Azure Resource Management.

2. Vyberte **nasazení služby App Service nebo upgradujte na nejnovější verzi**.

    ![Nasazení nebo upgrade](media/app-service-deploy-ha/01.png)

3. Přijměte licenční podmínky společnosti Microsoft a klikněte na tlačítko **Další**.

    ![Licenční podmínky společnosti Microsoft](media/app-service-deploy-ha/02.png)

4. Přijměte licenční podmínky jiného subjektu než Microsoft a klikněte na tlačítko **Další**.

    ![Licenční podmínky třetích stran](media/app-service-deploy-ha/03.png)

5. App Service poskytují konfigurace koncového bodu cloudu pro vaše prostředí Azure Stack.

    ![Konfigurace koncového bodu cloudu služby App Service](media/app-service-deploy-ha/04.png)

6. **Připojit** na předplatné služby Azure Stack můžete použít k instalaci a zvolte umístění. 

    ![Připojte se k předplatnému Azure Stack](media/app-service-deploy-ha/05.png)

7. Vyberte **použít existující virtuální síť a podsítě** a **název skupiny prostředků** pro skupinu prostředků, které jsou používány k nasazení šablony s vysokou dostupností.<br><br>V dalším kroku vyberte virtuální síť vytvořili jako součást nasazení šablony a pak vyberte příslušné role podsítě z možností rozevíracího seznamu. 

    ![Vybrat virtuální síť](media/app-service-deploy-ha/06.png)

8. Zadejte, že se dříve zaznamenané šablony Vypíše informace pro cestu ke sdílené složce soubor a parametry vlastníka sdílené složky souborů. Až budete hotovi, klikněte na tlačítko **Další**.

    ![Informace o výstupu sdílené složky souboru](media/app-service-deploy-ha/07.png)

9. Vzhledem k tomu, že počítač, který používáte k instalaci služby App Service se nenachází na stejné virtuální síti jako souborový server se používá k hostování sdílené služby App Service, nebudete schopni vyřešit název. **Toto chování je očekávané**.<br><br>Ověřte správnost informací zadaných pro sdílené složky UNC cestu a účty informace a stiskněte klávesu **Ano** v dialogovém okně upozornění a pokračujte v instalaci služby App Service.

    ![Očekávané chybové dialogové okno](media/app-service-deploy-ha/08.png)

    Pokud jste se rozhodli nasadit do existující virtuální sítě a interní IP adresu pro připojení k souborovému serveru, je nutné přidat odchozí pravidlo zabezpečení, povolení provozu SMB mezi podsítě pracovního procesu a souborový server. Přejděte na WorkersNsg portálu pro správu a přidat odchozí pravidlo zabezpečení s následujícími vlastnostmi:
    - Zdroj: Všechny
    - Zdrojový rozsah portů: *
    - Cíl: IP adresy
    - Rozsah cílových IP adres: Rozsah IP adres pro souborový server
    - Rozsah cílových portů: 445
    - Protokol: TCP
    - Akce: Povolit
    - Priorita: 700
    - Název: Outbound_Allow_SMB445

10. Zadejte ID aplikace Identity a cestu a hesel, která se certifikáty identity a klikněte na tlačítko **Další**:
    - Certifikát aplikace identity (ve formátu **sso.appservice.local.azurestack.external.pfx**)
    - Azure Resource Manageru kořenový certifikát (**AzureStackCertificationAuthority.cer**)

    ![ID aplikace certifikátu a kořenový certifikát](media/app-service-deploy-ha/008.png)

10. Dále zadejte zbývající požadované informace pro tyto certifikáty a klikněte na tlačítko **Další**:
    - Výchozí certifikát Azure Stack SSL (ve formátu **_.appservice.local.azurestack.external.pfx**)
    - Certifikát SSL rozhraní API (ve formátu **api.appservice.local.azurestack.external.pfx**)
    - Certifikát vydavatele (ve formě **ftp.appservice.local.azurestack.external.pfx**) 

    ![Další konfigurace certifikátů](media/app-service-deploy-ha/09.png)

11. Zadání informací o připojení systému SQL Server pomocí informací o připojení systému SQL Server z výstupů nasazení vysoké dostupnosti šablony:

    ![Informace o připojení SQL serveru](media/app-service-deploy-ha/10.png)

12. Vzhledem k tomu, že počítač, který používáte k instalaci služby App Service se nenachází na stejné virtuální síti jako SQL server používá k hostování databáze aplikace služby, nebudete schopni vyřešit název.  **Toto chování je očekávané**.<br><br>Ověřte správnost informací zadaných pro informace o systému SQL Server název a účty a stiskněte klávesu **Ano** a pokračujte v instalaci služby App Service. Klikněte na **Další**.

    ![Informace o připojení SQL serveru](media/app-service-deploy-ha/11.png)

13. Přijměte výchozí hodnoty konfigurace role nebo změňte na doporučené hodnoty a klikněte na tlačítko **Další**.<br><br>Doporučujeme vám, že výchozí hodnoty pro instance rolí služby App Service infrastruktury následujícím způsobem změnit pro konfigurace s vysokou dostupností:

    |Role|Výchozí|Doporučení s vysokou dostupností|
    |-----|-----|-----|
    |Role kontroleru|2|2|
    |Role správy|1|3|
    |Role vydavatele|1|3|
    |Role front-endu|1|3|
    |Role sdíleného pracovního procesu|1|10|
    |     |     |     |

    ![Hodnoty instance role infrastruktury](media/app-service-deploy-ha/12.png)

    > [!NOTE]
    > Změny z výchozí hodnoty na ty doporučení v tomto tutoral zvyšuje požadavky na hardware pro instalaci služby App Service. Celkem 26 jader a 46,592 MB paměti RAM je potřeba k podpoře doporučených virtuálních počítačů 21 místo výchozí 18 jader a 32,256 MB paměti RAM pro virtuální počítače s 15.

14. Vyberte image platformy použít k instalaci služby App Service infrastruktury virtuálních počítačů a klikněte na tlačítko **Další**:

    ![Výběr image platformy](media/app-service-deploy-ha/13.png)

15. App Service poskytují infrastrukturu role přihlašovací údaje a klikněte na tlačítko **Další**:

    ![Infrastrukturu role pověření](media/app-service-deploy-ha/14.png)

16. Přečtěte si informace, které se použije k nasazení služby App Service a klikněte na tlačítko **Další** zahájíte nasazení. 

    ![Zkontrolujte souhrn instalace](media/app-service-deploy-ha/15.png)

17. Zkontrolujte průběh nasazení služby App Service. To může trvat přes hodinu v závislosti na konkrétní konfiguraci vašeho nasazení a hardwaru. Jakmile se instalační program úspěšně dokončí, vyberte **ukončovací**.

    ![Nastavení je dokončené.](media/app-service-deploy-ha/16.png)


## <a name="next-steps"></a>Další postup

[Horizontální navýšení kapacity služby App Service](azure-stack-app-service-add-worker-roles.md). Můžete potřebovat přidat další služby App Service infrastrukturu role pracovní procesy pro splnění požadavků očekávané aplikace ve vašem prostředí. Ve výchozím nastavení podporuje služby App Service ve službě Azure Stack vrstvy bezplatných a sdílených pracovních procesů. Přidání další vrstvy pracovních procesů, budete muset přidat další role pracovního procesu.

[Konfigurace zdrojů nasazení](azure-stack-app-service-configure-deployment-sources.md). Další konfigurace je nutná pro podporu nasazení na vyžádání od více poskytovatelů zdrojového ovládacího prvku, jako jsou GitHub, BitBucket, OneDrive nebo DropBox.

[Zálohování služby App Service](app-service-back-up.md). Po úspěšné nasazení a konfiguraci služby App Service měli byste zajistit, že všechny komponenty potřebné pro zotavení po havárii se zálohují na zabraňují úniku dat a vyhněte se výpadkům zbytečné service během operace obnovení.