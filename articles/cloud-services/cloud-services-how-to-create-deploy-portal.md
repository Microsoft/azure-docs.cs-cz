---
title: Jak vytvořit a nasadit cloudovou službu | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit a nasadit cloudovou službu, pomocí webu Azure portal.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 56ea2f14-34a2-4ed9-857c-82be4c9d0579
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: jeconnoc
ms.openlocfilehash: ece3511d0d3c12be13a649385ea9b6dd5c75bfe0
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44303704"
---
# <a name="how-to-create-and-deploy-a-cloud-service"></a>Jak vytvořit a nasadit cloudovou službu
Na webu Azure portal poskytuje dva způsoby, jak vám pomůže vytvořit a nasadit cloudovou službu: *rychlé vytvoření* a *vytvořit vlastní*.

Tento článek vysvětluje, jak používat metodu rychlého vytvoření k vytvořit novou cloudovou službu a pak pomocí **nahrát** nahrát a nasadit balíček cloudové služby v Azure. Při použití této metody s portálem Azure je k dispozici užitečné odkazy pro dokončení všech požadavků, co využijete. Pokud jste připravení nasadit cloudovou službu, při vytváření, můžete mít obojí současně pomocí vytvořit vlastní.

> [!NOTE]
> Pokud chcete publikovat vaše Cloudová služba z Azure DevOps, použijte funkci pro rychlé vytvoření a nastavení publikování Azure DevOps z tohoto rychlého startu Azure nebo na řídicím panelu. Další informace najdete v tématu [průběžné doručování do Azure s využitím Azure DevOps][TFSTutorialForCloudService], nebo si zobrazte nápovědu pro **rychlý Start** stránky.
>
>

## <a name="concepts"></a>Koncepty
Tři součásti jsou potřeba k nasazení aplikace jako cloudové služby v Azure:

* **Definice služby**  
  Definiční soubor cloudové služby (.csdef) definuje model služeb, včetně počtu rolí.
* **Konfigurace služby**  
  Konfigurační soubor cloudové služby (.cscfg) obsahuje nastavení konfigurace pro cloud service a jednotlivé role, včetně počtu instancí rolí.
* **Balíček služby**  
  Balíček služby (.cspkg) obsahuje kód aplikace a konfigurace a definičního souboru služby.

Další informace o těchto a o tom, jak vytvořit balíček [tady](cloud-services-model-and-package.md).

## <a name="prepare-your-app"></a>Příprava aplikace
Než bude možné nasadit cloudovou službu, musíte vytvořit balíček cloudové služby (.cspkg) od kódu aplikace a konfigurační soubor cloudové služby (.cscfg). Sada Azure SDK poskytuje nástroje pro přípravu tyto soubory požadované nasazení. Můžete nainstalovat sadu SDK z [soubory ke stažení Azure](https://azure.microsoft.com/downloads/) stránky v jazyce, ve kterém chcete vyvíjíte kód aplikací.

Tři cloudové služby funkce vyžadují zvláštní konfigurace před exportem balíčku služby:

* Pokud chcete nasadit cloudovou službu, která používá vrstvy SSL (Secure Sockets) pro šifrování dat [nakonfigurovat svoji aplikaci](cloud-services-configure-ssl-certificate-portal.md#modify) pro protokol SSL.
* Pokud chcete konfigurovat připojení ke vzdálené ploše instance role [konfiguraci rolí](cloud-services-role-enable-remote-desktop-new-portal.md) pro vzdálenou plochu.
* Pokud chcete provést konfiguraci podrobné monitorování pro cloudové služby, povolení diagnostiky Azure pro cloudovou službu. *Minimální monitorování* (výchozí úroveň sledování) používá čítače výkonu shromážděné z hostitele operační systémy pro instance rolí (virtuální počítače). *Podrobné sledování* shromáždí další metriky na základě dat o výkonu v rámci instance rolí umožňuje podrobněji analyzovat problémy, ke kterým dochází při zpracování aplikace. Povolení diagnostiky Azure najdete v tématu [povolení diagnostiky v Azure](cloud-services-dotnet-diagnostics.md).

Chcete-li vytvořit cloudovou službu s nasazení webové role nebo role pracovního procesu, musíte [vytvořit balíček služby](cloud-services-model-and-package.md#servicepackagecspkg).

## <a name="before-you-begin"></a>Než začnete
* Pokud jste nenainstalovali sadu Azure SDK, klikněte na tlačítko **nainstalujte sadu Azure SDK** otevřít [stránku soubory ke stažení Azure](https://azure.microsoft.com/downloads/)a pak si stáhnout sady SDK pro jazyk, ve kterém chcete vyvíjet váš kód. (Budete mít možnost to udělat později.)
* Pokud všechny instance rolí v nástroji vyžadují certifikát, vytvořte certifikáty. Cloudové služby vyžadují soubor .pfx s privátním klíčem. Certifikáty můžete nahrát do Azure, jak vytvořit a nasadit cloudovou službu.

## <a name="create-and-deploy"></a>Vytvoření a nasazení
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2. Klikněte na tlačítko **vytvořit prostředek > Compute**a potom přejděte dolů k položce a klikněte na tlačítko **Cloudovou službu**.

    ![Publikovat cloudovou službu](media/cloud-services-how-to-create-deploy-portal/create-cloud-service.png)
3. Na novém **Cloudovou službu** podokně zadejte hodnotu **název DNS**.
4. Vytvořte nový **skupiny prostředků** nebo vyberte existující.
5. Vyberte **Umístění**.
6. Klikněte na tlačítko **balíčku**. Tím se otevře **nahrát balíček** podokně. Vyplňte požadovaná pole. Pokud některý z vašich rolí obsahuje jednu instanci, ujistěte se, **nasadit, i když jedna nebo víc rolí obsahuje jednu instanci** zaškrtnuto.
7. Ujistěte se, že **spuštění nasazení** zaškrtnuto.
8. Klikněte na tlačítko **OK** který bude ukončen **nahrát balíček** podokně.
9. Pokud nemáte žádné certifikáty, které chcete přidat, klikněte na tlačítko **vytvořit**.

    ![Publikovat cloudovou službu](media/cloud-services-how-to-create-deploy-portal/select-package.png)

## <a name="upload-a-certificate"></a>Nahrát certifikát
Pokud byl balíček pro nasazení [nakonfigurovaný tak, aby používali certifikáty](cloud-services-configure-ssl-certificate-portal.md#modify), teď můžete nahrát certifikát.

1. Vyberte **certifikáty**a na **přidat certifikáty** podokně, vyberte soubor PFX certifikátu SSL a potom zadejte **heslo** pro certifikát
2. Klikněte na tlačítko **připojit certifikát**a potom klikněte na tlačítko **OK** na **přidat certifikáty** podokně.
3. Klikněte na tlačítko **vytvořit** na **Cloudovou službu** podokně. Pokud bylo dosaženo nasazení **připravené** stav, můžete přejít k další kroky.

    ![Publikovat cloudovou službu](media/cloud-services-how-to-create-deploy-portal/attach-cert.png)

## <a name="verify-your-deployment-completed-successfully"></a>Ověření vašeho nasazení byla úspěšně dokončena
1. Klikněte na instanci cloudové služby.

    Stav by měl zobrazit, zda je služba **systémem**.
2. V části **Essentials**, klikněte na tlačítko **adresa URL webu** ve webovém prohlížeči otevřít cloudové služby.

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy-portal/running.png)

[TFSTutorialForCloudService]: http://go.microsoft.com/fwlink/?LinkID=251796

## <a name="next-steps"></a>Další postup
* [Obecná konfigurace cloudové služby](cloud-services-how-to-configure-portal.md).
* Konfigurace [vlastního názvu domény](cloud-services-custom-domain-name-portal.md).
* [Správa cloudové služby](cloud-services-how-to-manage-portal.md).
* Konfigurace [certifikáty ssl](cloud-services-configure-ssl-certificate-portal.md).
