---
title: 'Rychlý Start: vytvoření Azure Data Catalog'
description: V tomto rychlém startu se dozvíte, jak vytvořit Azure Data Catalog pomocí Azure Portal.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: quickstart
ms.date: 05/26/2020
ms.openlocfilehash: a56e5a4cae7c5a8e931b074f08a7152e53a8eb31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "104674729"
---
# <a name="quickstart-create-an-azure-data-catalog"></a>Rychlý Start: vytvoření Azure Data Catalog

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

Azure Data Catalog je plně spravovaná cloudová služba, která slouží jako systém pro registraci a zjišťování podnikových datových assetů. Podrobný přehled naleznete v části [Co je Azure Data Catalog?](overview.md).

Tento rychlý Start vám pomůže začít s vytvářením Azure Data Catalog.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

> [!Note]
> Z důvodu požadavků na zabezpečení Azure Azure Data Catalog má protokol TLS (enfored Transport Layer Security) 1,2. TLS 1,0 a TLS 1,1 jsou zakázané. Pokud počítač není aktualizovaný pro TLS 1,2, může dojít k chybám při spuštění registračního nástroje. Pokud chcete aktualizovat počítač pro TLS 1,2, přečtěte si téma [Povolení zabezpečení transportní vrstvy (1,2)](/mem/configmgr/core/plan-design/security/enable-tls-1-2) .

Abyste mohli začít, musíte mít:

* Předplatné [Microsoft Azure](https://azure.microsoft.com/)
* Musíte mít vlastního [klienta Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

Pokud chcete nastavit Data Catalog, musíte být vlastníkem nebo spoluvlastníkem předplatného Azure.

## <a name="create-a-data-catalog"></a>Vytvoření katalogu Data Catalog

Můžete zřídit pouze jeden katalog dat na organizaci (doména Azure Active Directory). Proto pokud vlastník nebo Spoluvlastník předplatného Azure, který patří do této Azure Active Directory domény, vytvořil katalog, nemůžete vytvořit katalog znovu ani v případě, že máte více předplatných Azure. Pokud chcete otestovat, jestli byl ve vaší doméně Azure Active Directory uživatelem vytvořen katalog dat, přejděte na [domovskou stránku Azure Data Catalog ](http://azuredatacatalog.com) a podívejte, jestli se vám tu katalog zobrazuje. Pokud byl pro vás už katalog vytvořen, přeskočte následující postup a přejděte do další sekce.

1. Přejít na [Azure Portal](https://portal.azure.com)  >  **vytvořit prostředek** a vybrat **Data Catalog**.

    ![Azure Data Catalog tlačítko vytvořit](media/data-catalog-get-started/data-catalog-create.png)

2. Zadejte **název** katalogu dat, **předplatné** , které chcete použít, **umístění** katalogu a **cenovou úroveň**. Potom vyberte **Vytvořit**.

3. Přejděte na [domovskou stránku služby Azure Data Catalog](http://azuredatacatalog.com) a klikněte na tlačítko **Publikovat data**.

   ![Azure Data Catalog – tlačítko Publikovat data](media/data-catalog-get-started/data-catalog-publish-data.png)

   Můžete se **také dostat na** domovskou stránku Data Catalog na [stránce služby Data Catalog](https://azure.microsoft.com/services/data-catalog) výběrem možnosti Začínáme.

   ![Azure Data Catalog – marketingová cílová stránka](media/data-catalog-get-started/data-catalog-marketing-landing-page.png)

4. Přejít na stránku **Nastavení** .

    ![Azure Data Catalog – zřízení katalogu dat](media/data-catalog-get-started/data-catalog-create-azure-data-catalog.png)

5. Rozbalte položku **ceny** a ověřte **edici** Azure Data Catalog (zdarma nebo Standard).

    ![Azure Data Catalog – výběr edice](media/data-catalog-get-started/data-catalog-create-catalog-select-edition.png)

6. Pokud jako cenovou úroveň zvolíte *Standard* Edition, můžete rozbalit **skupiny zabezpečení** a povolit autorizaci skupin zabezpečení služby Active Directory pro přístup k Data Catalog a povolení automatické úpravy fakturace.

    ![Azure Data Catalog skupiny zabezpečení](media/data-catalog-get-started/data-catalog-standard-security-groups.png)

7. Rozbalte položku **Uživatelé katalogu** a kliknutím na tlačítko **Přidat** přidejte pro katalog dat uživatele. Do této skupiny se automaticky přidávají.

    ![Azure Data Catalog – uživatelé](media/data-catalog-get-started/data-catalog-add-catalog-user.png)

8. Pokud jako cenovou úroveň zvolíte *Standard* Edition, můžete rozbalit správce **glosáře** a kliknutím na **Přidat** přidat uživatele glosářových uživatelů. Do této skupiny se automaticky přidávají.

    ![Správci glosáře Azure Data Catalog](media/data-catalog-get-started/data-catalog-standard-glossary-admin.png)

9. Rozbalte položku **Správci katalogu** a kliknutím na tlačítko **Přidat** přidejte pro katalog dat další správce. Do této skupiny se automaticky přidávají.

    ![Azure Data Catalog – správci](media/data-catalog-get-started/data-catalog-add-catalog-admins.png)

10. Rozbalte **Název portálu** a přidejte další text, který se zobrazí v nadpisu portálu.

    ![Název Catalog-Portal dat Azure](media/data-catalog-get-started/data-catalog-portal-title.png)

11. Po dokončení stránky **Nastavení** přejděte na stránku publikování a potom klikněte na **publikovat** .

    ![Azure Data Catalog – vytvořeno](media/data-catalog-get-started/data-catalog-created.png)

## <a name="find-a-data-catalog-in-the-azure-portal"></a>Vyhledání datového katalogu na webu Azure Portal

1. Na samostatné kartě nebo v samostatném okně webového prohlížeče přejděte na web [Azure Portal](https://portal.azure.com) a přihlaste se pomocí stejného účtu, který jste v předchozím kroku použili k vytvoření katalogu dat.

2. Vyberte **všechny služby** a pak klikněte na **Data Catalog**.

    ![Azure Data Catalog – procházení Azure](media/data-catalog-get-started/data-catalog-browse-azure-portal.png)

    Zobrazí se Katalog dat, který jste vytvořili.

    ![Azure Data Catalog – zobrazení katalogu v seznamu](media/data-catalog-get-started/data-catalog-azure-portal-show-catalog.png)

3. Klikněte na katalog, který jste vytvořili. Na portálu se vám zobrazí okno **Data Catalog**.

   ![Azure Data Catalog – okno na portálu](media/data-catalog-get-started/data-catalog-blade-azure-portal.png)

4. Můžete zobrazit vlastnosti katalogu dat a aktualizovat je. Můžete například kliknout na **Cenová úroveň** a změnit edici.

    ![Azure Data Catalog – cenová úroveň](media/data-catalog-get-started/data-catalog-change-pricing-tier.png)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak vytvořit Azure Data Catalog pro vaši organizaci. Nyní můžete zaregistrovat zdroje dat v katalogu dat.

> [!div class="nextstepaction"]
> [Registrace zdrojů dat v Azure Data Catalog](data-catalog-how-to-register.md)