---
title: Registrace a kontrola klienta Power BIu (Preview)
description: Naučte se používat portál Azure dosah k registraci a skenování klienta Power BI.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/19/2020
ms.openlocfilehash: 1f08bfd6b7c0439dd08a3091b5ea927781af9b84
ms.sourcegitcommit: ea17e3a6219f0f01330cf7610e54f033a394b459
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2020
ms.locfileid: "97387577"
---
# <a name="register-and-scan-a-power-bi-tenant-preview"></a>Registrace a kontrola klienta Power BIu (Preview)

Tento článek popisuje, jak pomocí portálu Azure dosah zaregistrovat a kontrolovat klienta Power BI.

> [!Note]
> Pokud je instance dosah a tenant Power BI ve stejném tenantovi Azure, můžete k nastavení kontroly Power BI tenanta použít jenom ověřování pomocí spravované identity (MSI). 

## <a name="create-a-security-group-for-permissions"></a>Vytvoření skupiny zabezpečení pro oprávnění

Pokud chcete nastavit ověřování, vytvořte skupinu zabezpečení a přidejte do ní spravovanou identitu katalogu.

1. V [Azure Portal](https://portal.azure.com)vyhledejte **Azure Active Directory**.
1. Vytvořte ve svém Azure Active Directory novou skupinu zabezpečení, a to tak, že [vytvoříte základní skupinu a přidáte členy pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

    > [!Tip]
    > Pokud již máte skupinu zabezpečení, kterou chcete použít, můžete tento krok přeskočit.

1. Jako **typ skupiny** vyberte **zabezpečení** .

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/security-group.png" alt-text="Typ skupiny zabezpečení":::

1. Do této skupiny zabezpečení přidejte spravovanou identitu vašeho katalogu. Vyberte **členy** a pak vybrat **+ přidat členy**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-group-member.png" alt-text="Přidejte spravovanou instanci katalogu do skupiny.":::

1. Vyhledejte svůj katalog a vyberte ho.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-catalog-to-group-by-search.png" alt-text="Přidání katalogu hledáním":::

    Mělo by se zobrazit oznámení o úspěšnosti, které vám ukáže, že bylo přidáno.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/success-add-catalog-msi.png" alt-text="Přidání úspěšného souboru MSI katalogu":::

## <a name="associate-the-security-group-with-the-tenant"></a>Přidružte skupinu zabezpečení ke klientovi.

1. Přihlaste se na [portál pro správu Power BI](https://app.powerbi.com/admin-portal/tenantSettings).

    > [!Important]
    > Aby bylo možné zobrazit stránku nastavení klienta, musíte být správcem Power BI.

1. Vyberte **Nastavení vývojářů**  >  **, aby instanční objekty mohly používat rozhraní API pro správu Power BI jen pro čtení (Preview)**.
1. Vyberte **konkrétní skupiny zabezpečení**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/allow-service-principals-power-bi-admin.png" alt-text="Obrázek ukazující, jak povolíte instančním objektům oprávnění k přístupu Power BI oprávnění API pro správu":::

    > [!Caution]
    > Když povolíte skupinu zabezpečení, kterou jste vytvořili (která má vaši identitu spravovanou v katalogu Data Catalog), aby používala rozhraní API Správce Power BI jen pro čtení, umožníte jí také přístup k metadatům (např. řídicím panelem a názvům sestav, vlastníkům, popisům atd.) pro všechny vaše Power BI artefakty v tomto tenantovi. Po navýšení metadat do oprávnění Azure dosah, dosah, ne Power BI oprávnění Zjistěte, kdo uvidí tato metadata.

    > [!Note]
    > Skupinu zabezpečení můžete odebrat z nastavení pro vývojáře, ale metadata, která byla dříve extrahována, nebudou odebrána z účtu dosah. Pokud chcete, můžete ho odstranit samostatně.

## <a name="register-your-power-bi-and-set-up-a-scan"></a>Zaregistrujte Power BI a nastavte kontrolu.

Teď, když jste udělili oprávnění ke katalogu pro připojení k rozhraní API pro správu vašeho tenanta Power BI, můžete nastavit kontrolu z portálu katalogu.

Nejdřív přidejte příznak speciální funkce na adresu URL dosah. 

1. Přidejte následující řetězec na konec identifikátoru URI instance dosah: `?feature.ext.catalog={"pbi":"true"}` . Tím povolíte možnost registrace Power BI ve vašem katalogu.

1. Vyberte ikonu **centra pro správu** .

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/management-center.png" alt-text="Ikona centra pro správu":::

1. Pak vyberte **+ Nový** ve **zdrojích dat**.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/data-sources.png" alt-text="Obrázek tlačítka nový zdroj dat":::

    Jako zdroj dat vyberte **Power BI** .

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/select-power-bi-data-source.png" alt-text="Obrázek znázorňující seznam zdrojů dat, které jsou k dispozici pro výběr":::

1. Zadejte popisný název instance Power BI.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-friendly-name.png" alt-text="Obrázek znázorňující Power BI popisný název zdroje dat":::

    Název musí být dlouhý 3-63 znaků a musí obsahovat jenom písmena, číslice, podtržítka a spojovníky.  Mezery nejsou povoleny.

    Ve výchozím nastavení systém najde klienta Power BI, který existuje ve stejném předplatném Azure.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-datasource-registered.png" alt-text="Power BI zaregistrovaný zdroj dat":::

1. Pojmenujte svoji kontrolu. Všimněte si, že jedinou podporovanou metodou ověřování je **spravovaná identita**.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-scan-setup.png" alt-text="Obrázek znázorňující nastavení kontroly Power BI":::

    Název kontroly musí být dlouhý 3-63 znaků a musí obsahovat jenom písmena, číslice, podtržítka a spojovníky.  Mezery nejsou povoleny.

1. Nastavte aktivační událost kontroly. Vaše možnosti jsou **jednou**, **každých 7 dní**, a **každých 30 dní**.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/scan-trigger.png" alt-text="Obrázek triggeru pro kontrolu":::

1. Po **kontrole nového skenování** vyberte **Uložit a spustit** , aby se spustila kontrola.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/save-run-power-bi-scan.png" alt-text="Uložení a spuštění obrázku Power BI obrazovky":::

## <a name="next-steps"></a>Další kroky

Další informace o použití rutin PowerShellu k registraci a kontrole Power BI tenanta najdete tady:
  
- [Použití PowerShellu k registraci a kontrole Power BI](powershell-register-scan-power-bi.md)
