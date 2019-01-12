---
title: 'Nasazení služeb App Services: Azure Stack | Dokumentace Microsoftu'
description: Podrobné pokyny k nasazení služby App Service ve službě Azure Stack
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2019
ms.author: anwestg
ms.openlocfilehash: bbb8e39e60500a2d8310159cfbac5659964fc387
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2019
ms.locfileid: "54230012"
---
# <a name="add-an-app-service-resource-provider-to-azure-stack"></a>Přidání poskytovatele prostředků App Service do služby Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

K nasazení služby App Service ve službě Azure Stack, použijte pokyny v tomto článku.

> [!IMPORTANT]  
> Aktualizace 1809 do služby Azure Stack integrované systému nebo nasadit nejnovější Azure Stack Development Kit (ASDK) před nasazením Azure App Service 1.4.

Může dát uživatelům možnost vytvářet webové aplikace a aplikace API. Umožníte uživatelům vytvářet tyto aplikace, budete muset:

 - Přidat [poskytovatele prostředků App Service](azure-stack-app-service-overview.md) k nasazení Azure Stack pomocí kroků popsaných v tomto článku.
 - Po instalaci poskytovatele prostředků App Service, můžete jej zahrnout do nabídky a plány. Uživatelé můžou potom přihlásit k odběru službu a začněte vytvářet aplikace.

> [!IMPORTANT]  
> Než spustíte instalační program zprostředkovatele prostředků, ujistěte se, že jste postupovali podle pokynů v [před zahájením práce](azure-stack-app-service-before-you-get-started.md).

## <a name="run-the-app-service-resource-provider-installer"></a>Spusťte instalační program zprostředkovatele prostředků App Service

Instalace poskytovatele prostředků App Service má aspoň hodinu. Čas potřebný, závisí na tom, kolik role instance můžete nasadit. Během nasazování Instalační program spustí následující úlohy:

 - Vytvořte kontejner objektů blob v zadaném účtu úložiště Azure Stack.
 - Vytvořte záznamy a zóny DNS pro službu App Service.
 - Zaregistrujte poskytovatele prostředků App Service.
 - Registrace položek galerie služby App Service.

 > [!IMPORTANT]
 > Před nasazením poskytovatele prostředků, přečtěte si poznámky k verzi pro další informace o nové funkce, opravy a známých problémech, které můžou ovlivnit nasazení.

Nasazení poskytovatele prostředků App Service, postupujte podle těchto kroků:

1. Spusťte appservice.exe jako správce na počítači, který můžete přístup ke koncovému bodu Azure zásobníku správce Azure Resource Management.

2. Vyberte **nasazení služby App Service nebo upgradujte na nejnovější verzi**.

    ![Instalační program služby App Service][1]

3. Přečtěte si a přijměte licenční podmínky pro Software společnosti Microsoft a pak vyberte **Další**.

4. Zkontrolujte a přijměte podmínky licence třetích stran a pak vyberte **Další**.

5. Ujistěte se, že informace o konfiguraci cloudové služby App Service je správná. Pokud jste použili výchozí nastavení při nasazení Azure Stack Development Kit (ASDK), můžete přijmout výchozí hodnoty. Ale pokud jste si přizpůsobili možností při nasazení ASDK, nebo provádíte nasazení na systémech pro Azure Stack integrované, musíte upravit hodnoty v tomto okně tak, aby odrážely rozdíly.

   Například pokud používáte mycloud.com příponu domény, musíte změnit koncový bod služby Azure Stack Tenanta Azure Resource Manageru pro správu. &lt;oblasti&gt;. mycloud.com. Zkontrolujte nastavení a pak vyberte **Další** uložte nastavení.

   ![Instalační program služby App Service][2]

6. Na další stránce Instalační služby App Service postupujte podle těchto kroků:

    a. Vyberte **připojit** vedle **předplatná Azure Stack**.

     - Pokud používáte Azure Active Directory (Azure AD), zadejte účet správce Azure AD a heslo, které jste zadali při nasazení Azure Stack. Vyberte **přihlášení**.
     - Pokud používáte služby Active Directory Federation Services (AD FS), zadejte účet správce. Například, cloudadmin@azurestack.local. Zadejte své heslo a pak vyberte **Sign In**.

   b. V **předplatná Azure Stack**, vyberte **výchozí předplatné poskytovatele**.

     > [!IMPORTANT]  
     > App Service **musí** nasadit **výchozí předplatné poskytovatele**.

   c. V **lokality Azure Stack**, vyberte umístění, které odpovídá nasazujete do oblasti. Vyberte například **místní** Pokud vaše nasazení Azure Stack Development Kit.

    ![Instalační program služby App Service][3]

7. Teď můžete nasadit do virtuální sítě, kterou jste nakonfigurovali [tímto postupem](azure-stack-app-service-before-you-get-started.md#virtual-network), nebo nechat instalační program služby App Service vytvořit novou virtuální síť a podsítě. Pokud chcete vytvořit virtuální síť, postupujte takto:

   a. Vyberte **vytvořit virtuální síť s výchozími nastaveními**, přijměte výchozí hodnoty a pak vyberte **Další**.

   b. Můžete také vybrat **použít existující virtuální síť a podsítě**. Proveďte následující akce:

     - Vyberte **skupiny prostředků** , která obsahuje virtuální síť.
     - Zvolte **virtuální sítě** název, který chcete nasadit.
     - Vyberte správné **podsítě** hodnoty pro každé z podsítí požadované role.
     - Vyberte **Další**.

   ![Instalační program služby App Service][4]

8. Zadejte informace pro svou sdílenou složku a potom vyberte **Další**. Plně kvalifikovaný název domény (FQDN) nebo IP adresa souborového serveru se musí používat adresu sdílené složky. Například \\\appservicefileserver.local.cloudapp.azurestack.external\websites, nebo \\\10.0.0.1\websites.

   >[!NOTE]
   >Instalační program se pokusí o test připojení ke sdílené složce než budete pokračovat. Ale pokud nasazení provádíte do existující virtuální síť, tento test připojení se nemusí podařit. Jsou uvedeny upozornění a výzvy, chcete-li pokračovat. Pokud je správný soubor sdílet informace, pokračujte v nasazení.

   ![Instalační program služby App Service][7]

9. Na další stránce Instalační služby App Service postupujte podle těchto kroků:

   a. V **ID aplikace Identity** zadejte identifikátor GUID pro aplikace, který používáte pro identita (z Azure AD).

   b. V **soubor certifikátu aplikace Identity** pole, zadejte (nebo vyhledejte) umístění souboru certifikátu.

   c. V **heslo certifikátu aplikace Identity** zadejte heslo pro certifikát. Toto heslo je ta, kterou jste si poznamenali při použití skript k vytvoření certifikátů.

   d. V **soubor kořenového certifikátu Azure Resource Manageru** pole, zadejte (nebo vyhledejte) umístění souboru certifikátu.

   e. Vyberte **Další**.

   ![Instalační program služby App Service][9]

10. Pro každý soubor polí tři certifikátu vyberte **Procházet** a přejděte k souboru příslušný certifikát. Musíte zadat heslo pro každý certifikát. Tyto certifikáty jsou ty, které jste vytvořili [vytvořit požadované certifikáty krok](azure-stack-app-service-before-you-get-started.md#get-certificates). Vyberte **Další** po zadání všech informací.

    | Box | Příklad názvu souboru certifikátu |
    | --- | --- |
    | **Výchozí soubor certifikátu SSL služby App Service** | \_.appservice.local.AzureStack.external.pfx |
    | **Soubor certifikátu SSL služby App Service API** | api.appservice.local.AzureStack.external.pfx |
    | **Soubor certifikátu SSL služby vydavatele aplikace** | ftp.appservice.local.AzureStack.external.pfx |

    Pokud jste použili jinou doménu přípona při vytvoření certifikátů, nepoužívejte názvy souborů certifikátů *místní. AzureStack.external*. Místo toho použijte informace o vaší vlastní doméně.

    ![Instalační program služby App Service][10]

11. Zadejte podrobnosti serveru SQL Server instance serveru použitý k hostování databáze poskytovatele prostředků App Service a potom vyberte **Další**. Instalační program ověří vlastnosti připojení SQL.

    > [!NOTE]
    > Instalační program se pokusí o test připojení k SQL serveru, než budete pokračovat. Ale pokud nasazení provádíte do existující virtuální síť, tento test připojení se nemusí podařit. Jsou uvedeny upozornění a výzvy, chcete-li pokračovat. Informace o SQL serveru je správná, pokračujte v nasazení.
    >
    > Ze služby Azure App Service v Azure stacku a vyšší 1.3 instalační program bude zkontrolujte, zda SQL Server databáze členství ve skupině na úrovni serveru SQL povoleno.  Pokud není, zobrazí výzva k následující výjimce:
    > ```sql
    >    Enable contained database authentication for SQL server by running below command on SQL server (Ctrl+C to copy)
    >    ***********************************************************
    >    sp_configure 'contained database authentication', 1;  
    >    GO  
    >    RECONFIGURE;  
    >    GO
    >    ***********************************************************
    > ```
    > Odkazovat [poznámky k verzi pro služby Azure App Service v Azure stacku 1.3](azure-stack-app-service-release-notes-update-three.md) další podrobnosti.

    ![Instalační program služby App Service][11]

12. Seznamte se s role instance a skladovou Položku možnosti. Minimální počet instancí a minimální SKU pro každou roli v nasazení ASDK naplnit výchozí hodnoty. Abyste mohli naplánovat nasazení poskytuje přehled požadavků na virtuální procesor a paměť. Vyberte po provedení výběru **Další**.

    >[!NOTE]
    >Pro nasazení v produkčním prostředí, následující pokyny v [kapacitní plánování role serveru služby Azure App Service ve službě Azure Stack](azure-stack-app-service-capacity-planning.md).

    | Role | Minimální instance | Minimální SKU | Poznámky |
    | --- | --- | --- | --- |
    | Kontrolér | 1 | Standard_A2 - (2 virtuální procesory a 3584 MB) | Spravuje a udržuje stav cloudové služby App Service. |
    | Správa | 1 | Standard_A2 - (2 virtuální procesory, 3584 MB) | Slouží ke správě koncových bodů aplikace služby Azure Resource Manageru a rozhraní API, rozšíření portálu (admin, tenanta, portálu Functions) a datové služby. Pro podporu převzetí služeb při selhání, vyšší doporučenou instance až na 2. |
    | Vydavatel | 1 | Standard_A1 - (1 virtuální procesor, 1792 MB) | Publikuje obsahu prostřednictvím nasazení pomocí FTP a web. |
    | FrontEnd | 1 | Standard_A1 - (1 virtuální procesor, 1792 MB) | Směruje žádosti do aplikace služby App Service. |
    | Sdílených pracovních serverů | 1 | Standard_A1 - (1 virtuální procesor, 1792 MB) | Hostitelé web nebo aplikace rozhraní API a aplikace Azure Function App. Můžete chtít přidat další instance. Jako operátor můžete definovat vaši nabídku a zvolte všechny úroveň skladové položky. Na úrovních musí mít minimálně jeden virtuální procesor. |

    ![Instalační program služby App Service][13]

    >[!NOTE]
    >**Jádru Windows serveru 2016 není image platformy podporované pro použití se službou Azure App Service ve službě Azure Stack.  Nepoužívejte vyhodnocení Image pro nasazení v produkčním prostředí.**

13. V **vyberte Image platformy** zvolte image virtuálního počítače nasazení Windows serveru 2016 z imagí dostupných v poskytovateli výpočetních prostředků pro cloudové služby App Service. Vyberte **Další**.

14. Na další stránce Instalační služby App Service postupujte podle těchto kroků:

     a. Zadejte uživatelské jméno správce virtuálního počítače Role pracovního procesu a heslo.

     b. Zadejte uživatelské jméno správce jiné role virtuálního počítače a heslo.

     c. Vyberte **Další**.

    ![Instalační program služby App Service][15]

15. Na stránce se souhrnem Instalační služby App Service postupujte podle těchto kroků:

    a. Zkontrolujte provedený výběr. Chcete-li provést změny, použijte **předchozí** tlačítka najdete předchozí stránky.

    b. Pokud tyto konfigurace jsou správné, zaškrtněte políčko.

    c. Chcete-li spustit nasazení, vyberte **Další**.

    ![Instalační program služby App Service][16]

16. Na další stránce Instalační služby App Service postupujte podle těchto kroků:

    a. Sledujte průběh instalace. App Service ve službě Azure Stack trvá přibližně 60 minut pro nasazení na základě výběru výchozí.

    b. Jakmile se instalační program úspěšně dokončí, vyberte **ukončovací**.

    ![Instalační program služby App Service][17]

## <a name="validate-the-app-service-on-azure-stack-installation"></a>Ověření služby App Service na instalaci služby Azure Stack

1. V portálu pro správu služby Azure Stack, přejděte na **Správa – App Service**.

2. V přehledu, v oblasti stav, zkontrolujte, **stav** zobrazí **připravené jsou všechny role**.

    ![Správa služby App Service](media/azure-stack-app-service-deploy/image12.png)

    Pokud nasazení provádíte do existující virtuální sítě a pomocí interní IP adresu pro připojení k souborového serveru, je nutné přidat odchozí pravidlo zabezpečení. Toto pravidlo aktivuje přenosy SMB mezi podsítě pracovního procesu a souborového serveru.  Chcete-li to provést, přejděte na WorkersNsg v portálu pro správu a přidat odchozí pravidlo zabezpečení s následujícími vlastnostmi:

    - Zdroj: Všechny
    - Zdrojový rozsah portů: *
    - Cíl: IP adresy
    - Rozsah cílových IP adres: Rozsah IP adres pro souborového serveru
    - Rozsah cílových portů: 445
    - Protokol: TCP
    - Akce: Povolit
    - Priorita: 700
    - Název: Outbound_Allow_SMB445

## <a name="test-drive-app-service-on-azure-stack"></a>Vyzkoušejte si App Service ve službě Azure Stack

Po nasazení a zaregistrujte poskytovatele prostředků App Service, otestujte a ujistit se, že uživatelé můžou nasazovat webové aplikace a aplikace API.

>[!NOTE]
>Je potřeba vytvořit nabídku, která má obor názvů Microsoft.Web v plánu. Budete také potřebovat předplatné tenanta, která si předplatí nabídky. Další informace najdete v tématu [vytvořit nabídku](azure-stack-create-offer.md) a [vytvořit plán](azure-stack-create-plan.md).
>
>Můžete *musí* předplatného tenanta k vytvoření aplikace, které používají služby App Service ve službě Azure Stack. Pouze úlohy, které správce služby můžete dokončit v portálu pro správu se vztahují k správy poskytovatele prostředků App Service. To zahrnuje zvýšení kapacity, konfigurace zdrojů nasazení a přidání vrstvy pracovních procesů a skladové položky.
>
>Vytvoření webového rozhraní API a Azure funkce aplikace, musíte použít portál pro klienty a mít předplatné tenanta.
>

Pokud chcete vytvořit test webové aplikace, postupujte takto:

1. Prostřednictvím uživatelského portálu služby Azure Stack, vyberte **+ vytvořit prostředek** > **Web + mobilní zařízení** > **webovou aplikaci**.

2. V části **webovou aplikaci**, zadejte název do **webovou aplikaci**.

3. V části **skupiny prostředků**vyberte **nový**. Zadejte název **skupiny prostředků**.

4. Vyberte **plán služby App Service/umístění** > **vytvořit nový**.

5. V části **plán služby App Service**, zadejte název **plán služby App Service**.

6. Vyberte **cenová úroveň** > **Free Shared** nebo **Shared Shared** > **vyberte**  >  **OK** > **vytvořit**.

7. Na řídicím panelu zobrazí dlaždice pro novou webovou aplikaci. Vyberte dlaždici.

8. Na **webovou aplikaci**vyberte **Procházet** zobrazit výchozí web pro tuto aplikaci.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>Nasazení webu WordPress, DNN nebo Django (volitelné)

1. Na portálu Azure Stack tenanta, vyberte **+**, přejděte na Azure Marketplace, nasazení webu Django a potom počkejte na dokončení nasazení. Webové platformě Django používá databázi založené na systému souborů. Nevyžaduje žádné další prostředků poskytovatele, jako je SQL nebo MySQL.

2. Pokud jste nasadili také poskytovatele prostředků MySQL, můžete nasadit web ve Wordpressu z Tržiště. Po zobrazení výzvy pro parametry databáze, zadejte uživatelské jméno jako *User1@Server1*, uživatelské jméno a název serveru podle vašeho výběru.

3. Pokud jste nasadili také poskytovatele prostředků SQL serveru, můžete nasadit DNN webu z Tržiště. Po zobrazení výzvy pro parametry databáze, zvolte databázi v počítači se systémem SQL Server, který je připojený ke zprostředkovateli prostředků.

## <a name="next-steps"></a>Další postup

Můžete také vyzkoušet ostatní [platforma jako služba (PaaS) služby](azure-stack-tools-paas-services.md).

 - [Poskytovatele prostředků SQL serveru](azure-stack-sql-resource-provider-deploy.md)
 - [Poskytovatele prostředků MySQL](azure-stack-mysql-resource-provider-deploy.md)

<!--Links-->
[Azure_Stack_App_Service_preview_installer]: https://go.microsoft.com/fwlink/?LinkID=717531
[App_Service_Deployment]: https://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: https://go.microsoft.com/fwlink/?LinkId=733525

<!--Image references-->
[1]: ./media/azure-stack-app-service-deploy/app-service-installer.png
[2]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-arm-endpoints.png
[3]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-subscription-information.png
[4]: ./media/azure-stack-app-service-deploy/app-service-default-VNET-config.png
[5]: ./media/azure-stack-app-service-deploy/app-service-custom-VNET-config.png
[6]: ./media/azure-stack-app-service-deploy/app-service-custom-VNET-config-with-values.png
[7]: ./media/azure-stack-app-service-deploy/app-service-fileshare-configuration.png
[8]: ./media/azure-stack-app-service-deploy/app-service-fileshare-configuration-error.png
[9]: ./media/azure-stack-app-service-deploy/app-service-identity-app.png
[10]: ./media/azure-stack-app-service-deploy/app-service-certificates.png
[11]: ./media/azure-stack-app-service-deploy/app-service-sql-configuration.png
[12]: ./media/azure-stack-app-service-deploy/app-service-sql-configuration-error.png
[13]: ./media/azure-stack-app-service-deploy/app-service-cloud-quantities.png
[14]: ./media/azure-stack-app-service-deploy/app-service-windows-image-selection.png
[15]: ./media/azure-stack-app-service-deploy/app-service-role-credentials.png
[16]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-deployment-summary.png
[17]: ./media/azure-stack-app-service-deploy/app-service-deployment-progress.png
