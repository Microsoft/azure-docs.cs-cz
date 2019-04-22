---
title: Vytvoření Azure Data Catalogu
description: Rychlý start pro vytvoření služby Azure Data Catalog.
author: markingmyname
ms.author: maghan
ms.service: data-catalog
ms.topic: quickstart
ms.date: 04/05/2019
ms.openlocfilehash: 66d178497f3403579990340b88a1153905b2a2bf
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59271029"
---
# <a name="quickstart-create-an-azure-data-catalog"></a>Rychlý start: Vytvoření Azure Data Catalogu

Azure Data Catalog je plně spravovaná cloudová služba, která slouží jako systém pro registraci a zjišťování podnikových datových assetů. Podrobný přehled naleznete v části [Co je Azure Data Catalog?](overview.md).

Tento rychlý start vám pomůže začít s vytvářením služby Azure Data Catalog.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, musíte mít:

* A [Microsoft Azure](https://azure.microsoft.com/) předplatného.
* Potřebujete mít vlastní [tenanta Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

Nastavení katalogu dat, musí být vlastník nebo spoluvlastník předplatného Azure.

## <a name="create-a-data-catalog"></a>Vytvoření katalogu dat

Můžete zřídit pouze jeden katalog dat na organizaci (doména Azure Active Directory). Proto pokud vlastník nebo spoluvlastník předplatného Azure, který patří k této doméně Azure Active Directory byla již vytvořena katalog, pak nelze vytvořit katalog znovu i v případě, že máte více předplatných Azure. Pokud chcete otestovat, jestli byl ve vaší doméně Azure Active Directory uživatelem vytvořen katalog dat, přejděte na [domovskou stránku Azure Data Catalog ](http://azuredatacatalog.com) a podívejte, jestli se vám tu katalog zobrazuje. Pokud byl pro vás už katalog vytvořen, přeskočte následující postup a přejděte do další sekce.

1. Přejděte [webu Azure portal](https://portal.azure.com) > **vytvořit prostředek** a vyberte **katalogu Data Catalog**.

    ![Vytvoření katalogu dat](media/data-catalog-get-started/data-catalog-create.png)

2. Zadejte **název** pro katalog data catalog **předplatné** chcete použít, **umístění** katalogu a **cenovou úroveň**. Potom vyberte **Vytvořit**.

3. Přejděte na [domovskou stránku služby Azure Data Catalog](http://azuredatacatalog.com) a klikněte na tlačítko **Publikovat data**.

   ![Azure Data Catalog – tlačítko Publikovat data](media/data-catalog-get-started/data-catalog-publish-data.png)

   Můžete také získat na domovskou stránku katalogu dat z [stránku služby Data Catalog](https://azure.microsoft.com/services/data-catalog) tak, že vyberete **Začínáme**.

   ![Azure Data Catalog – marketingová cílová stránka](media/data-catalog-get-started/data-catalog-marketing-landing-page.png)

4. Přejděte **nastavení** stránky.

    ![Azure Data Catalog – zřízení katalogu dat](media/data-catalog-get-started/data-catalog-create-azure-data-catalog.png)

5. Rozbalte **ceny** a ověřte váš Azure Data Catalog **edition** (Free nebo Standard).

    ![Azure Data Catalog – výběr edice](media/data-catalog-get-started/data-catalog-create-catalog-select-edition.png)

6. Pokud se rozhodnete *standardní* edition jako svou cenovou úroveň můžete rozbalit **skupiny zabezpečení** a povolit autorizaci skupin zabezpečení služby Active Directory pro přístup ke katalogu dat a povolit automatickou úpravu fakturace.

    ![Skupiny zabezpečení služby Azure Data Catalog](media/data-catalog-get-started/data-catalog-standard-security-groups.png)

7. Rozbalte položku **Uživatelé katalogu** a kliknutím na tlačítko **Přidat** přidejte pro katalog dat uživatele. Můžete automaticky přidané do této skupiny.

    ![Azure Data Catalog – uživatelé](media/data-catalog-get-started/data-catalog-add-catalog-user.png)

8. Pokud se rozhodnete *standardní* edition jako svou cenovou úroveň můžete rozbalit **správci glosáře** a klikněte na tlačítko **přidat** pro přidání uživatelů Správce glosáře. Můžete automaticky přidané do této skupiny.

    ![Správci glosáře služby Azure Data Catalog](media/data-catalog-get-started/data-catalog-standard-glossary-admin.png)

9. Rozbalte položku **Správci katalogu** a kliknutím na tlačítko**Přidat**přidejte pro katalog dat další správce. Můžete automaticky přidané do této skupiny.

    ![Azure Data Catalog – správci](media/data-catalog-get-started/data-catalog-add-catalog-admins.png)

10. Rozbalte **nadpis portálu** a přidat další text, který se zobrazí v nadpis portálu.

    ![Azure Data Catalog – portál název](media/data-catalog-get-started/data-catalog-portal-title.png)

11. Po dokončení **nastavení** stránce, potom přejděte **publikovat** stránky.

    ![Azure Data Catalog – vytvořeno](media/data-catalog-get-started/data-catalog-created.png)

## <a name="find-a-data-catalog-in-the-azure-portal"></a>Vyhledání datového katalogu na webu Azure Portal

1. Na samostatné kartě nebo v samostatném okně webového prohlížeče přejděte na web [Azure Portal](https://portal.azure.com) a přihlaste se pomocí stejného účtu, který jste v předchozím kroku použili k vytvoření katalogu dat.

2. Vyberte **všechny služby** a potom klikněte na tlačítko **katalogu Data Catalog**.

    ![Azure Data Catalog – procházení Azure](media/data-catalog-get-started/data-catalog-browse-azure-portal.png)

    Uvidíte katalog dat, který jste vytvořili.

    ![Azure Data Catalog – zobrazení katalogu v seznamu](media/data-catalog-get-started/data-catalog-azure-portal-show-catalog.png)

3. Klikněte na katalog, který jste vytvořili. Na portálu se vám zobrazí okno **Data Catalog**.

   ![Azure Data Catalog – okno na portálu](media/data-catalog-get-started/data-catalog-blade-azure-portal.png)

4. Můžete zobrazit vlastnosti katalogu dat a aktualizovat je. Můžete například kliknout na **Cenová úroveň** a změnit edici.

    ![Azure Data Catalog – cenová úroveň](media/data-catalog-get-started/data-catalog-change-pricing-tier.png)

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste zjistili, jak vytvořit službu Azure Data Catalog pro vaši organizaci. Teď můžete registrovat zdroje dat ve službě data catalog.

> [!div class="nextstepaction"]
> [Registrace zdrojů dat ve službě Azure Data Catalog](data-catalog-how-to-register.md)
