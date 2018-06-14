---
title: Postup vytvoření a nasazení cloudové služby | Microsoft Docs
description: Zjistěte, jak vytvořit a nasadit cloudové služby pomocí portálu Azure.
services: cloud-services
documentationcenter: ''
author: Thraka
manager: timlt
editor: ''
ms.assetid: 56ea2f14-34a2-4ed9-857c-82be4c9d0579
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: adegeo
ms.openlocfilehash: 96b92690cd164b1012380f82a1d1bd3336350e57
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2018
ms.locfileid: "29388080"
---
# <a name="how-to-create-and-deploy-a-cloud-service"></a>Postup vytvoření a nasazení cloudové služby
Portál Azure nabízí dva způsoby, můžete vytvořit a nasadit cloudové služby: *rychle vytvořit* a *vytvořit vlastní*.

Tento článek vysvětluje, jak pomocí metody rychlého vytvoření vytvořit novou cloudovou službu a pak použít **nahrát** nahrání a nasazení balíčku cloudové služby v Azure. Pokud použijete tuto metodu, umožňuje portálu Azure k dispozici užitečné odkazy pro dokončení všechny požadavky rovnou. Pokud jste připravení nasadit cloudové služby, když ho vytvoříte, můžete to udělat i ve stejnou dobu pomocí vytvořit vlastní.

> [!NOTE]
> Pokud hodláte publikovat cloudové služby z Visual Studio Team Services (služby VSTS), použijte rychle vytvořit a potom nastavit publikování služby VSTS z rychlého startu Azure nebo na řídicím panelu. Další informace najdete v tématu [nastavené průběžné doručování do Azure pomocí pomocí Visual Studio Team Services][TFSTutorialForCloudService], nebo naleznete v nápovědě **rychlý Start** stránky.
>
>

## <a name="concepts"></a>Koncepty
Tři součásti jsou nutné k nasazení aplikace jako cloudové služby v Azure:

* **Definice služby**  
  Soubor definice služby (.csdef) cloudu definuje model služeb, včetně počet rolí.
* **Konfigurace služby**  
  Cloudové služby konfiguračního souboru (.cscfg) poskytuje nastavení konfigurace pro cloudové služby a jednotlivé role, včetně počtu instancí role.
* **Balíček služby**  
  Balíček služby (.cspkg) obsahuje kódu aplikace a konfigurace a soubor definice služby.

Další informace o těchto a postup vytvoření balíčku [zde](cloud-services-model-and-package.md).

## <a name="prepare-your-app"></a>Příprava aplikace
Před nasazením cloudové služby, musíte vytvořit balíček cloudové služby (.cspkg) z kódu aplikace a cloudové služby konfiguračního souboru (.cscfg). Azure SDK poskytuje nástroje pro přípravu tyto soubory požadované k nasazení. Můžete nainstalovat sadu SDK z [Azure stáhne](https://azure.microsoft.com/downloads/) stránky v jazyce, ve kterém chcete vyvíjet kódu aplikace.

Funkce služby tři cloudu vyžadovat zvláštní konfigurace před exportem balíčku služby:

* Pokud chcete nasadit cloudovou službu, která používá Secure Sockets Layer (SSL) pro šifrování dat [konfiguraci aplikace](cloud-services-configure-ssl-certificate-portal.md#modify) pro protokol SSL.
* Pokud chcete nakonfigurovat připojení ke vzdálené ploše do instance rolí, [konfiguraci rolí](cloud-services-role-enable-remote-desktop-new-portal.md) pro vzdálenou plochu.
* Pokud chcete nakonfigurovat podrobné monitorování pro cloudové služby, povolte pro Cloudová služba Azure Diagnostics. *Minimální monitorování* (výchozí možnost monitorování úroveň) použije čítače výkonu shromážděné z hostitele operační systémy pro instance rolí (virtuální počítače). *Podrobné monitorování* shromažďuje další metriky na základě dat o výkonu v rámci instance rolí povolit blíže analyzovat problémy, ke kterým došlo během zpracování aplikace. Postup povolení Azure Diagnostics naleznete v tématu [povolení diagnostiky v Azure](cloud-services-dotnet-diagnostics.md).

Postup vytvoření cloudové služby se nasazení webové role nebo rolí pracovního procesu, je nutné [vytvořit balíček služby](cloud-services-model-and-package.md#servicepackagecspkg).

## <a name="before-you-begin"></a>Než začnete
* Pokud jste nenainstalovali sadu Azure SDK, klikněte na tlačítko **instalovat sadu SDK Azure** otevřete [Azure stáhne stránky](https://azure.microsoft.com/downloads/)a pak stažení sady SDK pro jazyk, ve kterém chcete vyvíjet kódu. (Budete mít příležitost udělat to později.)
* Pokud všechny instance role vyžadovat certifikát, vytvořte certifikáty. Cloudové služby vyžadují soubor .pfx s privátním klíčem. Certifikáty můžete nahrát do Azure, jak vytvořit a nasadit cloudové služby.

## <a name="create-and-deploy"></a>Vytvoření a nasazení
1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com/).
2. Klikněte na tlačítko **vytvořit prostředek > výpočetní**a poté přejděte dolů k a klikněte na tlačítko **Cloudová služba**.

    ![Publikování cloudové služby](media/cloud-services-how-to-create-deploy-portal/create-cloud-service.png)
3. V novém **Cloudová služba** podokně, zadejte hodnotu **název DNS**.
4. Vytvořte novou **skupiny prostředků** nebo vyberte nějaký existující.
5. Vyberte **Umístění**.
6. Klikněte na tlačítko **balíček**. Tím se otevře **nahrání balíčku** podokně. Vyplňte požadovaná pole. Pokud některé z vaší rolí obsahuje jednu instanci, ujistěte se, **nasadit, i když jedna nebo víc rolí obsahuje jednu instanci** je vybrána.
7. Ujistěte se, že **spuštění nasazení** je vybrána.
8. Klikněte na tlačítko **OK** který bude ukončen **nahrání balíčku** podokně.
9. Pokud nemáte žádné certifikáty, které chcete přidat, klikněte na tlačítko **vytvořit**.

    ![Publikování cloudové služby](media/cloud-services-how-to-create-deploy-portal/select-package.png)

## <a name="upload-a-certificate"></a>Nahrát certifikát
Pokud byl balíček pro nasazení [nakonfigurované na používání certifikátů](cloud-services-configure-ssl-certificate-portal.md#modify), teď můžete nahrát certifikát.

1. Vyberte **certifikáty**a na **přidat certifikáty** podokně, vyberte soubor .pfx certifikátu SSL a pak zadejte **heslo** pro certifikát
2. Klikněte na tlačítko **připojit certifikát**a potom klikněte na **OK** na **přidat certifikáty** podokně.
3. Klikněte na tlačítko **vytvořit** na **Cloudová služba** podokně. Pokud bylo dosaženo nasazení **připraven** stav, můžete pokračovat na další kroky.

    ![Publikování cloudové služby](media/cloud-services-how-to-create-deploy-portal/attach-cert.png)

## <a name="verify-your-deployment-completed-successfully"></a>Zkontrolujte vaše nasazení bylo úspěšně dokončeno
1. Klikněte na tlačítko instance cloudové služby.

    Stav by měl zobrazit, zda je služba **systémem**.
2. V části **Essentials**, klikněte **adresa URL webu** ve webovém prohlížeči otevřít cloudové služby.

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy-portal/running.png)

[TFSTutorialForCloudService]: http://go.microsoft.com/fwlink/?LinkID=251796

## <a name="next-steps"></a>Další postup
* [Obecná konfigurace cloudové služby](cloud-services-how-to-configure-portal.md).
* Konfigurace [vlastní název domény](cloud-services-custom-domain-name-portal.md).
* [Správa služby cloud](cloud-services-how-to-manage-portal.md).
* Konfigurace [certifikáty ssl](cloud-services-configure-ssl-certificate-portal.md).
