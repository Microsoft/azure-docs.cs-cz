---
title: Registrace a kontrola klienta Power BIu (Preview)
description: Naučte se používat portál Azure dosah k registraci a skenování klienta Power BI.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/19/2020
ms.openlocfilehash: 8fb4c797df7961726ca785a56a6ab25807999842
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600858"
---
# <a name="register-and-scan-a-power-bi-tenant-preview"></a>Registrace a kontrola klienta Power BIu (Preview)

Tento článek popisuje, jak pomocí portálu Azure dosah zaregistrovat a kontrolovat klienta Power BI.

> [!Note]
> Pokud je instance dosah a tenant Power BI ve stejném tenantovi Azure, můžete k nastavení kontroly Power BI tenanta použít jenom ověřování pomocí spravované identity (MSI). 

## <a name="create-a-security-group-for-permissions"></a>Vytvoření skupiny zabezpečení pro oprávnění

Pokud chcete nastavit ověřování, vytvořte skupinu zabezpečení a přidejte do ní spravovanou identitu dosah.

1. V [Azure Portal](https://portal.azure.com)vyhledejte **Azure Active Directory**.
1. Vytvořte ve svém Azure Active Directory novou skupinu zabezpečení, a to tak, že [vytvoříte základní skupinu a přidáte členy pomocí Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

    > [!Tip]
    > Pokud již máte skupinu zabezpečení, kterou chcete použít, můžete tento krok přeskočit.

1. Jako **typ skupiny** vyberte **zabezpečení** .

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/security-group.png" alt-text="Typ skupiny zabezpečení":::

1. Přidejte spravovanou identitu dosah do této skupiny zabezpečení. Vyberte **členy** a pak vybrat **+ přidat členy**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-group-member.png" alt-text="Přidejte spravovanou instanci katalogu do skupiny.":::

1. Vyhledejte spravovanou identitu dosah a vyberte ji.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-catalog-to-group-by-search.png" alt-text="Přidání katalogu hledáním":::

    Mělo by se zobrazit oznámení o úspěšnosti, které vám ukáže, že bylo přidáno.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/success-add-catalog-msi.png" alt-text="Přidání úspěšného souboru MSI katalogu":::

## <a name="associate-the-security-group-with-the-tenant"></a>Přidružte skupinu zabezpečení ke klientovi.

1. Přihlaste se na [portál pro správu Power BI](https://app.powerbi.com/admin-portal/tenantSettings).
1. Vyberte stránku **Nastavení tenanta** .

    > [!Important]
    > Aby bylo možné zobrazit stránku nastavení klienta, musíte být správcem Power BI.

1. Vybrat **nastavení rozhraní API pro správu**  >  **umožňuje instančním objektům používat rozhraní API pro správu Power BI jen pro čtení (Preview)**.
1. Vyberte **konkrétní skupiny zabezpečení**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/allow-service-principals-power-bi-admin.png" alt-text="Obrázek ukazující, jak povolíte instančním objektům oprávnění k přístupu Power BI oprávnění API pro správu":::

    > [!Caution]
    > Pokud povolíte vytvořenou skupinu zabezpečení (která má vaši spravovanou identitu dosah jako člen), abyste mohli používat rozhraní API Správce Power BI jen pro čtení, umožníte mu také přístup k metadatům (např. řídicím panelem a názvům sestav, vlastníkům, popisům atd.) pro všechny vaše Power BI artefaktů v tomto tenantovi. Po navýšení metadat do oprávnění Azure dosah, dosah, ne Power BI oprávnění Zjistěte, kdo uvidí tato metadata.

    > [!Note]
    > Skupinu zabezpečení můžete odebrat z nastavení pro vývojáře, ale metadata, která byla dříve extrahována, nebudou odebrána z účtu dosah. Pokud chcete, můžete ho odstranit samostatně.

## <a name="register-your-power-bi-and-set-up-a-scan"></a>Zaregistrujte Power BI a nastavte kontrolu.

Teď, když jste udělili oprávnění ke spravovaným identitám dosah pro připojení k rozhraní API pro správu vašeho tenanta Power BI, můžete si nastavit kontrolu z Azure dosah studia.

1. V levém navigačním panelu vyberte **zdroje** .

1. Pak vyberte **Register** (Registrovat).

    Jako zdroj dat vyberte **Power BI** .

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/select-power-bi-data-source.png" alt-text="Obrázek znázorňující seznam zdrojů dat, které jsou k dispozici pro výběr":::

3. Zadejte popisný název instance Power BI.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-friendly-name.png" alt-text="Obrázek znázorňující Power BI popisný název zdroje dat":::

    Název musí být dlouhý 3-63 znaků a musí obsahovat jenom písmena, číslice, podtržítka a spojovníky.  Mezery nejsou povoleny.

    Ve výchozím nastavení systém najde klienta Power BI, který existuje ve stejném předplatném Azure.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-datasource-registered.png" alt-text="Power BI zaregistrovaný zdroj dat":::

    > [!Note]
    > Pro Power BI je registrace a kontrola zdroje dat povolena pouze pro jednu instanci.


4. Pojmenujte svoji kontrolu. Pak vyberte možnost zahrnout nebo vyloučit osobní pracovní prostory. Všimněte si, že jedinou podporovanou metodou ověřování je **spravovaná identita**.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-scan-setup.png" alt-text="Obrázek znázorňující nastavení kontroly Power BI":::

    > [!Note]
    > * Přepnutí konfigurace vyhledávání do zahrnutí nebo vyloučení osobního pracovního prostoru spustí úplnou kontrolu zdroje PowerBI.
    > * Název kontroly musí být dlouhý 3-63 znaků a musí obsahovat jenom písmena, číslice, podtržítka a spojovníky. Mezery nejsou povoleny.

5. Nastavte aktivační událost kontroly. Vaše možnosti jsou **jednou**, **každých 7 dní**, a **každých 30 dní**.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/scan-trigger.png" alt-text="Obrázek triggeru pro kontrolu":::

6. Po **kontrole nového skenování** vyberte **Uložit a spustit** , aby se spustila kontrola.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/save-run-power-bi-scan.png" alt-text="Uložení a spuštění obrázku Power BI obrazovky":::

## <a name="next-steps"></a>Další kroky

- [Procházet Azure dosah Data Catalog](how-to-browse-catalog.md)
- [Hledání ve službě Azure dosah Data Catalog](how-to-search-catalog.md)
