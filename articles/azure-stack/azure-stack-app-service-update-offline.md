---
title: "Aktualizace služby Azure App Service Offline | Microsoft Docs"
description: "Podrobné pokyny pro aktualizaci služby Azure App Service v zásobníku Azure do offline režimu"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: anwestg
ms.openlocfilehash: 61a3169229cc121c078a934f6b979bdaffafd565
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
---
# <a name="offline-update-of-azure-app-service-on-azure-stack"></a>Offline aktualizace služby Azure App Service v Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

> [!IMPORTANT]
> Použitím 1802 aktualizace v zásobníku Azure integrované systému nebo nasadit nejnovější development kit zásobník Azure před nasazením služby Azure App Service.
>
>

Podle pokynů v tomto článku, můžete upgradovat [zprostředkovatele prostředků služby App Service](azure-stack-app-service-overview.md) nasadit v prostředí Azure zásobníku, které je:

* není připojen k Internetu
* zabezpečeny již ve službě Active Directory Federation Services (AD FS).

> [!IMPORTANT]
> Před spuštěním upgradu, ujistěte se, že jste již dokončili [nasazení služby Azure App Service na poskytovatel prostředků Azure zásobníku](azure-stack-app-service-deploy-offline.md)
>
>

## <a name="run-the-app-service-resource-provider-installer"></a>Spusťte instalační program zprostředkovatele prostředků služby App Service

A upgradujte zprostředkovatele služby App Service prostředků v prostředí Azure zásobníku, musíte provést tyto úlohy:

1. Stažení [instalační program aplikace služby](https://aka.ms/appsvcupdate1installer)
2. Vytvořte balíček s upgradem do režimu offline.
3. Spusťte instalační program služby App Service (appservice.exe) a dokončit upgrade.

Během tohoto procesu upgradu bude:

* Detekovat předchozí nasazení služby App Service
* Nahrání do úložiště
* Upgradovat všechny role služby App Service (řadiče, správu, front-endu, vydavatele a pracovní role)
* Aktualizovat definice sady škálování služby App Service
* Aktualizace služby App Service Manifest zprostředkovatele prostředků

## <a name="create-an-offline-upgrade-package"></a>Vytvořit balíček s upgradem offline

K upgradu služby App Service v odpojeném prostředí, musíte nejdřív vytvořit balíček s upgradem offline na počítači, který je připojený k Internetu.

1. Spustit appservice.exe jako správce

    ![Instalační program aplikace služby][1]

2. Klikněte na tlačítko **Upřesnit** > **vytvořit balíček offline**

    ![Instalační program aplikace služby Upřesnit][2]

3. Instalační program služby App Service vytvoří balíček s upgradem do režimu offline a zobrazuje cestu k němu.  Můžete kliknout na **otevřít složku** otevření složky v Průzkumníku vaší souborů.

4. Zkopírujte instalační program (AppService.exe) a offline balíček s upgradem do zásobníku Azure hostitelském počítači.

## <a name="complete-the-upgrade-of-app-service-on-azure-stack"></a>Dokončení upgradu služby App Service v Azure zásobníku

> [!IMPORTANT]
> Instalační program služby App Service musí spustit na počítači, který můžete dosáhnout koncového bodu Azure zásobníku správce Azure Resource Manager.
>
>

1. Appservice.exe spusťte jako správce.  

    ![Instalační program aplikace služby][1]

2. Klikněte na tlačítko **Upřesnit** > **dokončení offline instalace nebo upgradu**.

    ![Instalační program aplikace služby Upřesnit][2]

3. Přejděte do umístění, které jste dříve vytvořili balíčku s upgradem do režimu offline a pak klikněte na **Další**.

4. Zkontrolujte a přijměte licenční podmínky pro Software společnosti Microsoft a pak klikněte na tlačítko **Další**.

5. Zkontrolujte a přijměte podmínky licenční třetích stran a pak klikněte na **Další**.

6. Ujistěte se, že koncový bod Azure zásobník Azure Resource Manager a klienta služby Active Directory je správná. Pokud jste použili výchozí nastavení během nasazování Azure zásobníku Development Kit, můžete přijmout výchozí hodnoty. Ale pokud jste si přizpůsobili možností při nasazení zásobník Azure, je nutné upravit hodnoty v tomto okně tak, aby odrážela který. Například, pokud používáte příponu domény *mycloud.com*, váš koncový bod Azure zásobník Azure Resource Manager, musíte změnit na *management.region.mycloud.com*. Jakmile potvrdíte vaše informace, klikněte na tlačítko **Další**.

    ![Informace o cloudu Azure zásobníku][3]

7. Na další stránce:

   1. Klikněte na tlačítko **připojit** vedle položky **předplatných Azure zásobníku** pole.
        * Pokud používáte Azure Active Directory (Azure AD), zadejte účet správce Azure AD a heslo, které jste zadali při nasazení Azure zásobníku. Klikněte na tlačítko **přihlášení**.
        * Pokud používáte služby Active Directory Federation Services (AD FS), zadejte účet správce. Například  *cloudadmin@azurestack.local* . Zadejte své heslo a klikněte na tlačítko **přihlásit**.
   2. V **předplatných Azure zásobníku** pole, vyberte své předplatné.
   3. V **umístění zásobník Azure** , vyberte umístění, které odpovídá oblast, že nasazujete. Vyberte například **místní** Pokud vaše nasazení do Azure zásobníku Development Kit.
   4. Pokud je zjištěno stávajícího nasazení služby App Service, pak prostředku skupiny a účet úložiště bude naplněno a šedá.
   5. Klikněte na tlačítko **Další** k Zkontrolujte souhrn upgradu.

    ![Instalace služby App Service zjistila][4]

8. Na stránce Shrnutí:
   1. Zkontrolujte provedený výběr. Chcete-li změnit, použijte **předchozí** tlačítka pro návštěvu předchozí stránky.
   2. Pokud konfigurace jsou správné, zaškrtněte políčko.
   3. Chcete-li spustit upgrade, klikněte na tlačítko **Další**.

       ![Souhrn upgradu služby App Service][5]

9. Průběh upgradu stránky:
    1. Sledovat průběh upgradu. Doba trvání upgradu služby App Service v Azure zásobníku závisí na počet instancí role nasazení se může lišit.
    2. Po úspěšném dokončení upgradu, klikněte na tlačítko **ukončení**.

        ![Průběh upgradu služby App Service][6]

<!--Image references-->
[1]: ./media/azure-stack-app-service-update-offline/app-service-exe.png
[2]: ./media/azure-stack-app-service-update-offline/app-service-exe-advanced.png
[3]: ./media/azure-stack-app-service-update-offline/app-service-azure-resource-manager-endpoints.png
[4]: ./media/azure-stack-app-service-update-offline/app-service-installation-detected.png
[5]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-summary.png
[6]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-complete.png

## <a name="next-steps"></a>Další postup

Můžete také zkusit dalších [platforma jako služba (PaaS) služby](azure-stack-tools-paas-services.md).

* [Poskytovatel prostředků SQL serveru](azure-stack-sql-resource-provider-deploy.md)
* [Poskytovatel prostředků MySQL](azure-stack-mysql-resource-provider-deploy.md)
