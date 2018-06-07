---
title: Nasazení v režimu offline v zásobníku Azure App Service | Microsoft Docs
description: Podrobné pokyny k nasazení služby App Service v odpojeném prostředí Azure zásobníku zabezpečeným službou AD FS.
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
ms.date: 05/22/2018
ms.author: anwestg
ms.openlocfilehash: 7084243c0fc84429b585c3e8fd9e5c64df469ec4
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604280"
---
# <a name="add-an-app-service-resource-provider-to-a-disconnected-azure-stack-environment-secured-by-ad-fs"></a>Přidání poskytovatele prostředků služby App Service pro odpojené prostředí Azure zásobníku zabezpečeným službou AD FS

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

> [!IMPORTANT]
> Použitím 1804 aktualizace v zásobníku Azure integrované systému nebo nasadit nejnovější development kit zásobník Azure před nasazením Azure App Service 1.2.
>
>

Podle pokynů v tomto článku, můžete nainstalovat [zprostředkovatele prostředků služby App Service](azure-stack-app-service-overview.md) do prostředí Azure zásobníku, které je:

- není připojen k Internetu
- zabezpečeny již ve službě Active Directory Federation Services (AD FS).

Pro přidání poskytovatele prostředků služby App Service pro offline nasazení zásobník Azure, musíte provést tyto úlohy nejvyšší úrovně:

1. Dokončení [požadovaných krocích](azure-stack-app-service-before-you-get-started.md) (jako nákup certifikátů, která může trvat několik dní přijímat).
2. [Stažení a extrakci instalační a pomocné soubory](azure-stack-app-service-before-you-get-started.md) pro počítač připojený k Internetu.
3. Vytvoření balíčku offline instalace.
4. Spusťte soubor appservice.exe Instalační služby.

## <a name="create-an-offline-installation-package"></a>Vytvoření offline instalačního balíčku

Abyste mohli nasadit služby App Service v odpojeném prostředí, musíte nejdřív vytvořit offline instalačního balíčku na počítači, který je připojený k Internetu.

1. Spusťte instalační program AppService.exe na počítači, který je připojený k Internetu.

2. Klikněte na tlačítko **Upřesnit** > **vytvořit offline instalační balíček**.

    ![Instalační program aplikace služby][1]

3. Instalační program služby App Service vytvoří offline instalačního balíčku a zobrazuje cestu k němu. Můžete kliknout na **otevřít složku** otevření složky v Průzkumníku vaší souborů.

    ![Instalační program aplikace služby](media/azure-stack-app-service-deploy-offline/image02.png)

4. Zkopírujte instalační program (AppService.exe) a offline instalačního balíčku do vaší zásobník Azure hostitelský počítač.

## <a name="complete-the-offline-installation-of-app-service-on-azure-stack"></a>Dokončení offline instalace služby App Service v Azure zásobníku

1. Appservice.exe spusťte jako správce z počítače, který můžete dosáhnout koncového bodu správy prostředků Azure zásobníku správce Azure.

2. Klikněte na tlačítko **Upřesnit** > **dokončení offline instalace**.

    ![Instalační program aplikace služby][2]

3. Přejděte do umístění, do režimu offline instalačního balíčku, který jste dříve vytvořili a pak klikněte na **Další**.

    ![Instalační program aplikace služby](media/azure-stack-app-service-deploy-offline/image04.png)

4. Zkontrolujte a přijměte licenční podmínky pro Software společnosti Microsoft a pak klikněte na tlačítko **Další**.

5. Zkontrolujte a souhlas s podmínkami licence třetích stran a pak klikněte na tlačítko **Další**.

6. Ujistěte se, zda je správný informace pro konfiguraci cloudu App Service. Pokud jste použili výchozí nastavení během nasazování Azure zásobníku Development Kit, můžete přijmout výchozí hodnoty. Ale pokud jste si přizpůsobili možností při nasazení Azure zásobníku nebo nasazujete na integrovaný systém, je nutné upravit hodnoty v tomto okně tak, aby odrážela který. Například pokud používáte mycloud.com příponu domény, musíte změnit váš koncový bod Azure zásobníku klienta Azure Resource Manageru pro správu. <region>. mycloud.com. Jakmile potvrdíte vaše informace, klikněte na tlačítko **Další**.

    ![Instalační program aplikace služby][3]

7. Na další stránce:
    1. Klikněte na tlačítko **připojit** vedle položky **předplatných Azure zásobníku** pole.
        - Zadejte účet správce. Například, cloudadmin@azurestack.local. Zadejte své heslo a klikněte na tlačítko **přihlásit**.
    2. V **předplatných Azure zásobníku** vyberte **výchozí předplatné zprostředkovatele**.
    
    > [!NOTE]
    > Aplikace služby lze nasadit pouze do **výchozí zprostředkovatel odběru** v tuto chvíli.  V budoucí aktualizaci služby App Service nasadí do nové předplatné měření byla zavedená v zásobníku 1804 Azure a všechna existující nasazení budou migrovat do této nové předplatné také.
    >
    >
    
    3. V **umístění zásobník Azure** , vyberte umístění, které odpovídá oblast, že nasazujete. Vyberte například **místní** Pokud vaše nasazení do Azure zásobníku Development Kit.
    4. Klikněte na **Další**.

    ![Instalační program aplikace služby][4]

8. Nyní máte možnost nasadit do existující virtuální síť podle konfigurace provede [zde](azure-stack-app-service-before-you-get-started.md#virtual-network), nebo povolit instalační program aplikace služby k vytvoření virtuální sítě a přidružené podsítě.
    1. Vyberte **vytvořit virtuální síť s výchozím nastavením**, přijměte výchozí hodnoty a pak klikněte na tlačítko **Další**, nebo;
    2. Vyberte **použít existující virtuální síť a podsítě**.
        1. Vyberte **skupiny prostředků** obsahující vaší virtuální sítě.
        2. Vyberte správný **virtuální sítě** název chcete nasadit do;
        3. Vyberte správný **podsíť** hodnoty pro každé z podsítí požadované role;
        4. Klikněte na **Další**

    ![Instalační program aplikace služby][5]

9. Zadejte informace pro sdílené složky a potom klikněte na **Další**. Plně kvalifikovaný název domény nebo IP adresu svého souborového serveru, musíte použít adresu sdílené složky. Například \\\appservicefileserver.local.cloudapp.azurestack.external\websites, nebo \\\10.0.0.1\websites

    > [!NOTE]
    > Instalační program se pokusí o test připojení ke sdílení souborů než budete pokračovat.  Ale pokud jste vybrali k nasazení v existující virtuální síť, instalační program nemusí být možné se připojit ke sdílení souborů a zobrazí upozornění, s dotazem, jestli chcete pokračovat.  Zkontrolujte zadané informace o sdílení souborů a pokračovat, pokud jsou správné.
    >
    >

   ![Instalační program aplikace služby][8]

10. Na další stránce:
    1. V **ID aplikace Identity** zadejte identifikátor GUID pro aplikaci, kterou používáte pro identita (z Azure AD).
    2. V **soubor certifikátu Identity aplikace** pole, zadejte (nebo vyhledejte) umístění souboru certifikátu.
    3. V **heslo certifikátu Identity aplikace** zadejte heslo pro certifikát. Toto heslo je ten, který jste si poznamenali při skript jste použili k vytvoření certifikátů.
    4. V **soubor kořenového certifikátu Azure Resource Manager** pole, zadejte (nebo vyhledejte) umístění souboru certifikátu.
    5. Klikněte na **Další**.

    ![Instalační program aplikace služby][10]

11. Pro každou z jsou tři políčka soubor certifikátu, klikněte na tlačítko **Procházet** a pak přejděte k souboru příslušný certifikát. Pro každý certifikát, je nutné zadat heslo. Tyto certifikáty jsou ty, které jste vytvořili v [vytvořit požadované certifikáty krok](azure-stack-app-service-before-you-get-started.md#get-certificates). Klikněte na tlačítko **Další** po zadání všech informací.

    | Box | Příklad názvu souboru certifikátu |
    | --- | --- |
    | **Soubor certifikátu protokolu SSL výchozí služby App Service** | \_.appservice.local.AzureStack.external.pfx |
    | **Soubor certifikátu SSL služby App Service API** | api.appservice.local.AzureStack.external.pfx |
    | **Soubor certifikátu SSL služby vydavatele aplikace** | ftp.appservice.local.AzureStack.external.pfx |

    Pokud jste použili příponu jinou doménu, když jste vytvořili certifikáty, nepoužívejte názvy souborů certifikátů *místní. AzureStack.external*. Místo toho používejte vaše informace vlastní doménu.

    ![Instalační program aplikace služby][11]

12. Zadejte podrobnosti serveru SQL Server instance serveru použitý pro hostování databází zprostředkovatele prostředků služby App Service a pak klikněte na tlačítko **Další**. Instalační program ověří vlastnosti připojení SQL. Můžete **musí** zadejte interních ip nebo plně kvalifikovaný název domény pro název serveru SQL Server.

    > [!NOTE]
    > Instalační program se pokusí o test připojení k systému SQl Server, než budete pokračovat.  Ale pokud jste vybrali k nasazení v existující virtuální síť, instalační program nemusí být možné se připojit k systému SQL Server a zobrazí upozornění s dotazem, jestli chcete pokračovat.  Zkontrolujte zadané informace o systému SQL Server a pokračovat, pokud jsou správné.
    >
    >
   
   ![Instalační program aplikace služby][12]

13. Zkontrolujte možnosti role instance a SKU. Výchozí hodnoty se naplní minimální počet instancí a minimální SKU pro každou roli v ASDK nasazení. Souhrn požadavků virtuální procesory a paměť je určena k plánování nasazení. Po provedení výběru klikněte na tlačítko **Další**.

     > [!NOTE]
     > Pro nasazení v produkčním prostředí, postupujte podle pokynů v [kapacitní plánování rolí serveru služby Azure App Service v Azure zásobníku](azure-stack-app-service-capacity-planning.md).
     >
     >

    | Role | Minimální instancí | Minimální SKU | Poznámky |
    | --- | --- | --- | --- |
    | Kontroler | 1 | Standard_A1 - (1 virtuální procesor, 1792 MB) | Spravuje a udržuje stav cloudové služby App Service. |
    | Správa | 1 | Standard_A2 - (2 Vcpu, 3584 MB) | Spravuje koncových bodů aplikace služby Azure Resource Manageru a rozhraní API, portálu rozšíření (správce, klienta funkce portálu.) a službu data. Pro podporu převzetí služeb při selhání, vyšší doporučenou instancí 2. |
    | Vydavatel | 1 | Standard_A1 - (1 virtuální procesor, 1792 MB) | Publikuje obsahu prostřednictvím FTP a webové nasazení. |
    | FrontEnd | 1 | Standard_A1 - (1 virtuální procesor, 1792 MB) | Směruje požadavky na aplikace služby App Service. |
    | Sdílených pracovních | 1 | Standard_A1 - (1 virtuální procesor, 1792 MB) | Hostitele web nebo aplikace API a aplikace Azure Functions. Můžete přidat víc instancí. Jako operátor můžete definovat vaši nabídku a vyberte vrstvy jakékoli SKU. Vrstvy musí mít minimálně jeden virtuální procesor. |

    ![Instalační program aplikace služby][14]

    > [!NOTE]
    > **Jádro systému Windows Server 2016 není image podporované platformy pro použití se službou Azure App Service v Azure zásobníku.  Nepoužívejte vyhodnocení Image pro nasazení v produkčním prostředí.**

14. V **vyberte Image platformy** vyberte bitovou kopii vašeho nasazení systému Windows Server 2016 virtuálního počítače od těch, které jsou dostupné na poskytovateli výpočetních prostředků pro cloudové služby App Service. Klikněte na **Další**.

15. Na další stránce:
     1. Zadejte uživatelské jméno správce virtuálního počítače Role pracovního procesu a heslo.
     2. Zadejte další role virtuálního počítače správce uživatelské jméno a heslo.
     3. Klikněte na **Další**.

    ![Instalační program aplikace služby][16]

16. Na stránce Shrnutí:
    1. Zkontrolujte provedený výběr. Chcete-li změnit, použijte **předchozí** tlačítka pro návštěvu předchozí stránky.
    2. Pokud konfigurace jsou správné, zaškrtněte políčko.
    3. Chcete-li spustit nasazení, klikněte na tlačítko **Další**.

    ![Instalační program aplikace služby][17]

17. Na další stránce:
    1. Sledovat průběh instalace. Služby App Service v Azure zásobníku trvá asi 60 minut pro nasazení založené na výchozí nastavení.
    2. Po instalační program úspěšně dokončí, klikněte na tlačítko **ukončení**.

    ![Instalační program aplikace služby][18]

## <a name="validate-the-app-service-on-azure-stack-installation"></a>Ověření služby App Service na instalaci Azure zásobníku

1. Na portálu správy Azure zásobníku přejít na **správy – Služba App Service**.

2. V přehledu v části stav, zkontrolujte, který **stav** ukazuje **všech rolí jsou připravené**.

    ![Správa služby App Service](media/azure-stack-app-service-deploy/image12.png)
    
> [!NOTE]
> Pokud jste vybrali k nasazení do existující virtuální síť a interní IP adresu do conenct k vaší souborovém serveru, je nutné přidat pravidlo odchozí zabezpečení povolení přenosy SMB mezi podsíť pracovního procesu a souborovém serveru.  Chcete-li to provést, přejděte do WorkersNsg v portálu pro správu a přidejte pravidlo odchozí zabezpečení s následujícími vlastnostmi:
> * Zdroj: žádné
> * Zdroj rozsah portů: *
> * Cíl: IP adresy
> * Rozsah cílových IP adres: rozsah IP adres pro vaše souborovém serveru
> * Rozsah cílových portů: 445
> * Protokol: TCP
> * Akce: Povolit
> * Priorita: 700
> * Název: Outbound_Allow_SMB445
>

## <a name="test-drive-app-service-on-azure-stack"></a>Vyzkoušejte službu App Service v Azure zásobníku

Po nasazení a registrace zprostředkovatele prostředků služby App Service, otestujte a ujistit se, zda mohou uživatelé nasadit web a aplikace API.

> [!NOTE]
> Budete muset vytvořit nabídku, který má obor názvů Microsoft.Web v rámci plánu. Pak musíte mít předplatné klienta, která si předplatí v rámci této nabídky. Další informace najdete v tématu [vytvořit nabídku](azure-stack-create-offer.md) a [vytvořit plán](azure-stack-create-plan.md).
>
Můžete *musí* mít předplatné klienta k vytvoření aplikace, které používají služby App Service v Azure zásobníku. Jedinou možností, které správce služby můžete dokončit v rámci portálu správce souvisejí se správou zprostředkovatele prostředků služby App Service. Tyto možnosti zahrnují přidání kapacitu, konfigurace nasazení zdroje a přidání pracovní úrovně a SKU.
>
Od verze třetí technical preview k vytvoření webové rozhraní API a Azure funkce aplikace, musíte použít portál pro klienty a mít předplatné klienta.

1. Na portálu Azure zásobníku klienta, klikněte na tlačítko **nový** > **Web + mobilní** > **webové aplikace**.

2. Na **webové aplikace** okno, zadejte název v **webové aplikace** pole.

3. V části **skupiny prostředků**, klikněte na tlačítko **nový**. Zadejte název do **skupiny prostředků** pole.

4. Klikněte na **Plán nebo umístění služby App Service** > **Vytvořit nový**.

5. Na **plán služby App Service** okno, zadejte název v **plán služby App Service** pole.

6. Klikněte na tlačítko **cenová úroveň** > **volné sdílené** nebo **sdílené sdílené** > **vyberte**  >   **OK** > **vytvořit**.

7. V pod minuty, dlaždice pro nové webové aplikace se zobrazí na řídicím panelu. Klikněte na dlaždici.

8. Na **webové aplikace** okně klikněte na tlačítko **Procházet** zobrazit výchozí web pro tuto aplikaci.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>Nasazení na web WordPress, DNN nebo Django (volitelné)

1. Na portálu Azure zásobníku klienta, klikněte na tlačítko **+**, přejděte do Azure Marketplace, nasazení webu Django a čekání na úspěšné dokončení. Webová platforma Django používá databázi na základě systému souborů. Nevyžaduje žádné další prostředků poskytovatelé, například SQL nebo MySQL.

2. Pokud jste nasadili také MySQL poskytovatele prostředků, můžete nasadit web WordPress v Marketplace. Když se zobrazí výzva k databázi parametry, zadejte uživatelské jméno jako *User1@Server1*, uživatelské jméno a název serveru podle svého výběru.

3. Pokud jste nasadili také poskytovatele prostředků systému SQL Server, můžete nasadit na web DNN z Marketplace. Když se zobrazí výzva k parametry databáze, zvolte databázi v počítači se systémem SQL Server, který je připojený ke zprostředkovateli prostředků.

## <a name="next-steps"></a>Další postup

Můžete také zkusit dalších [platforma jako služba (PaaS) služby](azure-stack-tools-paas-services.md).

- [Poskytovatel prostředků SQL serveru](azure-stack-sql-resource-provider-deploy.md)
- [Poskytovatel prostředků MySQL](azure-stack-mysql-resource-provider-deploy.md)

<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[App_Service_Deployment]: http://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: http://go.microsoft.com/fwlink/?LinkId=733525

<!--Image references-->
[1]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-advanced-create-package.png
[2]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-advanced-complete-offline.png
[3]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-arm-endpoints.png
[4]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-subscription-information.png
[5]: ./media/azure-stack-app-service-deploy-offline/app-service-default-VNET-config.png
[6]: ./media/azure-stack-app-service-deploy-offline/app-service-custom-VNET-config.png
[7]: ./media/azure-stack-app-service-deploy-offline/app-service-custom-VNET-config-with-values.png
[8]: ./media/azure-stack-app-service-deploy-offline/app-service-fileshare-configuration.png
[9]: ./media/azure-stack-app-service-deploy-offline/app-service-fileshare-configuration-error.png
[10]: ./media/azure-stack-app-service-deploy-offline/app-service-identity-app.png
[11]: ./media/azure-stack-app-service-deploy-offline/app-service-certificates.png
[12]: ./media/azure-stack-app-service-deploy-offline/app-service-sql-configuration.png
[13]: ./media/azure-stack-app-service-deploy-offline/app-service-sql-configuration-error.png
[14]: ./media/azure-stack-app-service-deploy-offline/app-service-cloud-quantities.png
[15]: ./media/azure-stack-app-service-deploy-offline/app-service-windows-image-selection.png
[16]: ./media/azure-stack-app-service-deploy-offline/app-service-role-credentials.png
[17]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-deployment-summary.png
[18]: ./media/azure-stack-app-service-deploy-offline/app-service-deployment-progress.png
