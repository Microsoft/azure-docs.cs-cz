---
title: Jak vytvořit a nasadit cloudovou službu | Dokumenty společnosti Microsoft
description: Zjistěte, jak vytvořit a nasadit cloudovou službu pomocí portálu Azure.
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 05/18/2017
ms.author: tagore
ms.openlocfilehash: 53f53976b20359afc45abe1b25ca60325b5d6a2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75386166"
---
# <a name="how-to-create-and-deploy-a-cloud-service"></a>Jak vytvořit a nasadit cloudovou službu
Portál Azure poskytuje dva způsoby, jak vytvořit a nasadit cloudovou službu: *Rychlé vytvoření* a *Vlastní vytvoření*.

Tento článek vysvětluje, jak použít metodu Rychlé vytvoření k vytvoření nové cloudové služby a potom použít **Upload** k nahrání a nasazení balíčku cloudové služby v Azure. Při použití této metody portál Azure zpřístupní pohodlné odkazy pro dokončení všech požadavků as you as you. Pokud jste připraveni nasadit cloudovou službu při vytváření, můžete provést obojí současně pomocí vlastního vytvoření.

> [!NOTE]
> Pokud plánujete publikovat cloudovou službu z Azure DevOps, použijte rychlé vytvoření a pak nastavte publikování Azure DevOps z Azure Quickstart nebo řídicího panelu. Další informace najdete [v tématu průběžné doručování do Azure pomocí Azure DevOps][TFSTutorialForCloudService], nebo najdete v nápovědě pro stránku **Rychlý start.**
>
>

## <a name="concepts"></a>Koncepty
K nasazení aplikace jako cloudové služby v Azure jsou potřeba tři součásti:

* **Definice služby**  
  Soubor definice cloudové služby (.csdef) definuje model služby, včetně počtu rolí.
* **Konfigurace služby**  
  Konfigurační soubor cloudové služby (.cscfg) poskytuje nastavení konfigurace pro cloudovou službu a jednotlivé role, včetně počtu instancí rolí.
* **Balíček služeb**  
  Balíček služeb (.cspkg) obsahuje kód aplikace a konfigurace a soubor definice služby.

Můžete se dozvědět více o těchto a jak vytvořit balíček [zde](cloud-services-model-and-package.md).

## <a name="prepare-your-app"></a>Příprava aplikace
Před nasazením cloudové služby je nutné vytvořit balíček cloudové služby (.cspkg) z kódu aplikace a konfiguračního souboru cloudové služby (.cscfg). Sada Azure SDK poskytuje nástroje pro přípravu těchto požadovaných souborů nasazení. Sadu SDK můžete nainstalovat ze stránky [Stahování Azure](https://azure.microsoft.com/downloads/) v jazyce, ve kterém dáváte přednost vývoji kódu aplikace.

Tři funkce cloudové služby vyžadují před exportem balíčku služeb speciální konfigurace:

* Pokud chcete nasadit cloudovou službu, která používá ssl (Secure Sockets Layer) pro šifrování dat, [nakonfigurujte aplikaci](cloud-services-configure-ssl-certificate-portal.md#modify) pro SSL.
* Chcete-li konfigurovat připojení ke vzdálené ploše na instance rolí, [nakonfigurujte role](cloud-services-role-enable-remote-desktop-new-portal.md) pro vzdálenou plochu.
* Pokud chcete nakonfigurovat podrobné monitorování pro vaši cloudovou službu, povolte Diagnostika Azure pro cloudovou službu. *Minimální monitorování* (výchozí úroveň monitorování) používá čítače výkonu shromážděné z hostitelských operačních systémů pro instance rolí (virtuální počítače). *Podrobné monitorování* shromažďuje další metriky založené na datech o výkonu v rámci instancí role, aby bylo možné důkladněji analýzu problémů, ke kterým dochází během zpracování aplikace. Informace o tom, jak povolit diagnostiku Azure, najdete [v tématu Povolení diagnostiky v Azure](cloud-services-dotnet-diagnostics.md).

Chcete-li vytvořit cloudovou službu s nasazeními webových rolí nebo rolí pracovních prostředků, musíte [vytvořit balíček služeb](cloud-services-model-and-package.md#servicepackagecspkg).

## <a name="before-you-begin"></a>Než začnete
* Pokud jste nenainstalovali Azure SDK, klikněte na **nainstalovat Azure SDK** otevřete [stránku Stahování Azure a](https://azure.microsoft.com/downloads/)stáhněte si sdk pro jazyk, ve kterém chcete vyvíjet kód. (Budete mít příležitost to udělat později.)
* Pokud některé instance role vyžadují certifikát, vytvořte certifikáty. Cloudové služby vyžadují soubor Pfx se soukromým klíčem. Certifikáty můžete nahrát do Azure při vytváření a nasazování cloudové služby.

## <a name="create-and-deploy"></a>Vytváření a nasazování
1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. Klikněte **na Vytvořit prostředek > Compute**a potom přejděte dolů na **Cloudovou službu**.

    ![Publikování cloudové služby](media/cloud-services-how-to-create-deploy-portal/create-cloud-service.png)
3. V novém podokně **Cloudservice** zadejte hodnotu **názvu DNS**.
4. Vytvořte novou **skupinu prostředků** nebo vyberte existující skupinu.
5. Vyberte **umístění**.
6. Klepněte na tlačítko **Balíček**. Otevře se podokno **Nahrát balíček.** Vyplňte požadovaná pole. Pokud některá z vašich rolí obsahuje jednu instanci, ujistěte se, **že je vybrána nasadit i v případě, že je vybrána jedna nebo více rolí.**
7. Ujistěte se, že je **vybráno nasazení Start.**
8. Klepněte na tlačítko **OK,** které zavře podokno **Nahrát balíček.**
9. Pokud nemáte žádné certifikáty, které byste mohli přidat, klepněte na tlačítko **Vytvořit**.

    ![Publikování cloudové služby](media/cloud-services-how-to-create-deploy-portal/select-package.png)

## <a name="upload-a-certificate"></a>Nahrání certifikátu
Pokud byl balíček nasazení [nakonfigurován pro použití certifikátů](cloud-services-configure-ssl-certificate-portal.md#modify), můžete certifikát odeslat nyní.

1. Vyberte **certifikáty**a v podokně **Přidat certifikáty** vyberte soubor SSL .pfx certifikátu A zadejte **heslo** pro certifikát.
2. Klikněte na **Připojit certifikát**a potom v podokně **Přidat certifikáty** klikněte na **OK.**
3. V podokně **Cloudová služba** klikněte na **Vytvořit.** Po dokončení nasazení stavu **Připraveno** můžete přejít k dalším krokům.

    ![Publikování cloudové služby](media/cloud-services-how-to-create-deploy-portal/attach-cert.png)

## <a name="verify-your-deployment-completed-successfully"></a>Ověření úspěšného dokončení nasazení
1. Klikněte na instanci cloudové služby.

    Stav by měl zobrazit, že služba je **spuštěna**.
2. V části **Essentials**klikněte na **adresu URL webu** a otevřete cloudovou službu ve webovém prohlížeči.

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy-portal/running.png)

[TFSTutorialForCloudService]: https://go.microsoft.com/fwlink/?LinkID=251796

## <a name="next-steps"></a>Další kroky
* [Obecná konfigurace cloudové služby](cloud-services-how-to-configure-portal.md).
* Konfigurace [vlastního názvu domény](cloud-services-custom-domain-name-portal.md).
* [Spravujte cloudovou službu](cloud-services-how-to-manage-portal.md).
* Konfigurace [certifikátů ssl](cloud-services-configure-ssl-certificate-portal.md).



