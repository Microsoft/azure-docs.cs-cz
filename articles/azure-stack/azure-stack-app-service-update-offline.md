---
title: Aktualizace služby Azure App Service Offline | Dokumentace Microsoftu
description: Podrobné pokyny pro offline aktualizaci služby Azure App Service ve službě Azure Stack
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
ms.date: 08/15/2018
ms.author: anwestg
ms.openlocfilehash: f48872d1853dfd4c40022f42c8e237973ac70fe6
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2018
ms.locfileid: "42059404"
---
# <a name="offline-update-of-azure-app-service-on-azure-stack"></a>Offline aktualizace služby Azure App Service ve službě Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

> [!IMPORTANT]
> Aktualizace 1807 do služby Azure Stack integrované systému nebo nasadit nejnovější sady Azure Stack development kit před nasazením Azure App Service 1.3.
>
>

Podle pokynů v tomto článku, můžete upgradovat [poskytovatele prostředků App Service](azure-stack-app-service-overview.md) nasazené v prostředí Azure Stack, která je:

* nejsou připojené k Internetu
* zabezpečené službou Active Directory Federation Services (AD FS).

> [!IMPORTANT]
> Před spuštěním upgradu, ujistěte se, že jste již dokončili [nasazení služby Azure App Service na poskytovatele prostředků služby Azure Stack](azure-stack-app-service-deploy-offline.md)
>
>

## <a name="run-the-app-service-resource-provider-installer"></a>Spusťte instalační program zprostředkovatele prostředků App Service

Pokud chcete upgradovat poskytovatele prostředků App Service v prostředí Azure Stack, musíte dokončit tyto úlohy:

1. Stáhněte si [instalační program služby App Service](https://aka.ms/appsvcupdate3installer)
2. Vytvořte offline balíček s upgradem.
3. Spusťte instalační program služby App Service (appservice.exe) a dokončit upgrade.

Během tohoto procesu inovace bude:

* Detekovat předchozí nasazení služby App Service
* Nahrání do úložiště
* Upgradovat všechny role služby App Service (řadiče, správu front-endu, vydavatele a pracovní role)
* Aktualizovat definice škálovací sady služby App Service
* Aktualizujte Manifest poskytovatele prostředků App Service

## <a name="create-an-offline-upgrade-package"></a>Vytvořit offline balíček s upgradem

Chcete-li upgradovat službu App Service v odpojeném prostředí, musíte nejdřív vytvořit offline balíček s upgradem na počítači, který je připojený k Internetu.

1. Spustit appservice.exe jako správce

    ![Instalační program služby App Service][1]

2. Klikněte na tlačítko **Upřesnit** > **vytvořit balíček pro offline instalaci**

    ![Instalační program služby App Service, Upřesnit][2]

3. Instalační program služby App Service vytvoří offline balíček s upgradem a zobrazuje cestu k němu.  Můžete kliknout na **otevřít složku** otevřete složku v Průzkumníku souborů.

4. Zkopírujte instalační program (AppService.exe) a offline balíček s upgradem na hostitelském počítači Azure Stack.

## <a name="complete-the-upgrade-of-app-service-on-azure-stack"></a>Dokončení upgradu služby App Service ve službě Azure Stack

> [!IMPORTANT]
> Instalační program služby App Service je třeba spustit na počítači, který můžete oslovit koncový bod Azure Stack správce Azure Resource Manageru.
>
>

1. Appservice.exe spusťte jako správce.

    ![Instalační program služby App Service][1]

2. Klikněte na tlačítko **Upřesnit** > **dokončit offline instalaci nebo upgrade**.

    ![Instalační program služby App Service, Upřesnit][2]

3. Přejděte do umístění v režimu offline balíček s upgradem jste dříve vytvořili a pak klikněte na tlačítko **Další**.

4. Přečtěte si a přijměte licenční podmínky pro Software společnosti Microsoft a klikněte na **Další**.

5. Zkontrolujte a přijměte podmínky licence třetí strany a klikněte na **Další**.

6. Ujistěte se, že koncový bod Azure stacku Azure Resource Manageru a Tenanta Active Directory správnost informací. Pokud jste použili výchozí nastavení při nasazení Azure Stack Development Kit, můžete přijmout výchozí hodnoty. Pokud jste si přizpůsobili možností při nasazení Azure Stack, však musíte upravit hodnoty v tomto okně tak, aby odrážely, který. Například, pokud používáte příponu domény *mycloud.com*, koncový bod služby Azure Stack Azure Resource Managerem musíte změnit na *management.region.mycloud.com*. Jakmile potvrdíte vaše informace, klikněte na tlačítko **Další**.

    ![Informace o cloudu Azure Stack][3]

7. Na další stránce:

   1. Klikněte na tlačítko **připojit** vedle **předplatná Azure Stack** pole.
        * Pokud používáte Azure Active Directory (Azure AD), zadejte účet správce Azure AD a heslo, které jste zadali při nasazení Azure Stack. Klikněte na tlačítko **přihlášení**.
        * Pokud používáte služby Active Directory Federation Services (AD FS), zadejte účet správce. Příklad: *cloudadmin@azurestack.local*. Zadejte své heslo a klikněte na tlačítko **Sign In**.
   2. V **předplatná Azure Stack** vyberte **výchozí předplatné poskytovatele**.
   3. V **lokality Azure Stack** , vyberte umístění, které odpovídá nasazujete do oblasti. Vyberte například **místní** Pokud vaše nasazení Azure Stack Development Kit.
   4. Pokud je zjištěno existující nasazení služby App Service, pak prostředku skupiny a účet úložiště bude naplněna a šedě.
   5. Klikněte na tlačítko **Další** Zkontrolujte souhrn upgradu.

    ![Nezjistila se instalace služby App Service][4]

8. Na stránce souhrnu:
   1. Zkontrolujte provedený výběr. Chcete-li provést změny, použijte **předchozí** tlačítka najdete předchozí stránky.
   2. Pokud tyto konfigurace jsou správné, zaškrtněte políčko.
   3. Spusťte upgrade, klikněte na tlačítko **Další**.

       ![Souhrn upgradu služby App Service][5]

9. Stránky uvidíte průběh upgradu:
    1. Sledujte průběh upgradu. Trvání upgradu služby App Service ve službě Azure Stack se liší v závislosti na počtu nasazené instance role.
    2. Po úspěšném dokončení upgradu klikněte na tlačítko **ukončovací**.

        ![Průběh upgradu služby App Service][6]

<!--Image references-->
[1]: ./media/azure-stack-app-service-update-offline/app-service-exe.png
[2]: ./media/azure-stack-app-service-update-offline/app-service-exe-advanced.png
[3]: ./media/azure-stack-app-service-update-offline/app-service-azure-resource-manager-endpoints.png
[4]: ./media/azure-stack-app-service-update-offline/app-service-installation-detected.png
[5]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-summary.png
[6]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-complete.png

## <a name="next-steps"></a>Další postup

Můžete také vyzkoušet ostatní [platforma jako služba (PaaS) služby](azure-stack-tools-paas-services.md).

* [Poskytovatele prostředků SQL serveru](azure-stack-sql-resource-provider-deploy.md)
* [Poskytovatele prostředků MySQL](azure-stack-mysql-resource-provider-deploy.md)
