---
title: Vytvoření Azure Data Catalogu
description: Tento rychlý start popisuje, jak vytvořit katalog dat Azure pomocí portálu Azure.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: quickstart
ms.date: 08/01/2019
ms.openlocfilehash: 854760ea0158d356cc5449c9f1fdab6161588640
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "68976859"
---
# <a name="quickstart-create-an-azure-data-catalog"></a>Úvodní příručka: Vytvoření katalogu dat Azure

Azure Data Catalog je plně spravovaná cloudová služba, která slouží jako systém pro registraci a zjišťování podnikových datových assetů. Podrobný přehled naleznete v části [Co je Azure Data Catalog?](overview.md).

Tento rychlý start vám pomůže začít s vytvářením katalogu dat Azure.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, musíte mít:

* Předplatné [Microsoft Azure.](https://azure.microsoft.com/)
* Musíte mít vlastního [klienta Služby Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

Chcete-li nastavit katalog dat, musíte být vlastníkem nebo spoluvlastníkem předplatného Azure.

## <a name="create-a-data-catalog"></a>Vytvoření katalogu dat

Můžete zřídit pouze jeden katalog dat na organizaci (doména Azure Active Directory). Pokud tedy vlastník nebo spoluvlastník předplatného Azure, který patří do této domény Azure Active Directory, už vytvořil katalog, nemůžete katalog znovu vytvořit, i když máte více předplatných Azure. Pokud chcete otestovat, jestli byl ve vaší doméně Azure Active Directory uživatelem vytvořen katalog dat, přejděte na [domovskou stránku Azure Data Catalog ](http://azuredatacatalog.com) a podívejte, jestli se vám tu katalog zobrazuje. Pokud byl pro vás už katalog vytvořen, přeskočte následující postup a přejděte do další sekce.

1. Přejděte na [portál](https://portal.azure.com) > Azure**Vytvořte prostředek** a vyberte **Katalog dat**.

    ![Tlačítko vytvořit katalog dat Azure](media/data-catalog-get-started/data-catalog-create.png)

2. Zadejte **název** katalogu dat, **odběr,** který chcete použít, **umístění** katalogu a **cenovou úroveň**. Pak vyberte **Vytvořit**.

3. Přejděte na [domovskou stránku služby Azure Data Catalog](http://azuredatacatalog.com) a klikněte na tlačítko **Publikovat data**.

   ![Azure Data Catalog – tlačítko Publikovat data](media/data-catalog-get-started/data-catalog-publish-data.png)

   Na domovskou stránku katalogu dat se také můžete dostat na [stránce služby Katalog dat](https://azure.microsoft.com/services/data-catalog) tak, že vyberete **Začínáme**.

   ![Azure Data Catalog – marketingová cílová stránka](media/data-catalog-get-started/data-catalog-marketing-landing-page.png)

4. Přejděte na stránku **Nastavení.**

    ![Azure Data Catalog – zřízení katalogu dat](media/data-catalog-get-started/data-catalog-create-azure-data-catalog.png)

5. Rozbalte **ceny** a ověřte **edici** Katalogu dat Azure (zdarma nebo standardní).

    ![Azure Data Catalog – výběr edice](media/data-catalog-get-started/data-catalog-create-catalog-select-edition.png)

6. Pokud jako cenovou úroveň zvolíte *standardní* edici, můžete rozbalit **skupiny zabezpečení** a povolit autorizaci skupin zabezpečení služby Active Directory pro přístup ke katalogu dat a povolit automatickou úpravu fakturace.

    ![Skupiny zabezpečení katalogu dat Azure](media/data-catalog-get-started/data-catalog-standard-security-groups.png)

7. Rozbalte položku **Uživatelé katalogu** a kliknutím na tlačítko **Přidat** přidejte pro katalog dat uživatele. Budete automaticky přidáni do této skupiny.

    ![Azure Data Catalog – uživatelé](media/data-catalog-get-started/data-catalog-add-catalog-user.png)

8. Pokud jako cenovou úroveň zvolíte *standardní* edice, můžete rozbalit **položku Správci glosáře** a kliknutím na **Přidat** přidejte uživatele správce glosáře. Budete automaticky přidáni do této skupiny.

    ![Správci glosáře datových katalogů Azure](media/data-catalog-get-started/data-catalog-standard-glossary-admin.png)

9. Rozbalte položku **Správci katalogu** a kliknutím na tlačítko**Přidat**přidejte pro katalog dat další správce. Budete automaticky přidáni do této skupiny.

    ![Azure Data Catalog – správci](media/data-catalog-get-started/data-catalog-add-catalog-admins.png)

10. Rozbalte **název portálu** a přidejte další text, který se zobrazí v názvu portálu.

    ![Název portálu Azure Data Catalog-Portal](media/data-catalog-get-started/data-catalog-portal-title.png)

11. Po dokončení stránky **Nastavení** přejděte na stránku **Publikovat.**

    ![Azure Data Catalog – vytvořeno](media/data-catalog-get-started/data-catalog-created.png)

## <a name="find-a-data-catalog-in-the-azure-portal"></a>Vyhledání datového katalogu na webu Azure Portal

1. Na samostatné kartě nebo v samostatném okně webového prohlížeče přejděte na web [Azure Portal](https://portal.azure.com) a přihlaste se pomocí stejného účtu, který jste v předchozím kroku použili k vytvoření katalogu dat.

2. Vyberte **Všechny služby** a klepněte na **položku Katalog dat**.

    ![Katalog dat Azure – procházení Azure](media/data-catalog-get-started/data-catalog-browse-azure-portal.png)

    Zobrazí se katalog dat, který jste vytvořili.

    ![Azure Data Catalog – zobrazení katalogu v seznamu](media/data-catalog-get-started/data-catalog-azure-portal-show-catalog.png)

3. Klikněte na katalog, který jste vytvořili. Na portálu se vám zobrazí okno **Data Catalog**.

   ![Azure Data Catalog – okno na portálu](media/data-catalog-get-started/data-catalog-blade-azure-portal.png)

4. Můžete zobrazit vlastnosti katalogu dat a aktualizovat je. Můžete například kliknout na **Cenová úroveň** a změnit edici.

    ![Azure Data Catalog – cenová úroveň](media/data-catalog-get-started/data-catalog-change-pricing-tier.png)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak vytvořit katalog dat Azure pro vaši organizaci. Nyní můžete registrovat zdroje dat v katalogu dat.

> [!div class="nextstepaction"]
> [Registrace zdrojů dat v katalogu dat Azure](data-catalog-how-to-register.md)
