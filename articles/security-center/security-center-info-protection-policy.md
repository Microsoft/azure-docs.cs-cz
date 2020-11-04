---
title: Zásady SQL Information Protection v Azure Security Center
description: Přečtěte si, jak přizpůsobit zásady ochrany informací v Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2ebf2bc7-232a-45c4-a06a-b3d32aaf2500
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2020
ms.author: memildin
ms.openlocfilehash: 0a487f778693e87e680033edd0d80c55d1a85f66
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348623"
---
# <a name="sql-information-protection-policy-in-azure-security-center"></a>Zásady SQL Information Protection v Azure Security Center
 
[Mechanismus zjišťování dat a klasifikace dat](../azure-sql/database/data-discovery-and-classification-overview.md) v SQL Information Protection poskytují pokročilé možnosti pro zjišťování, klasifikaci, označování a vytváření sestav citlivých dat ve vašich databázích. Je integrována do [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md), [spravované instance Azure SQL](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)a [Azure synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md).

Klasifikační mechanizmus je založen na následujících dvou prvcích:

- **Labels** – hlavní atributy klasifikace, které slouží k definování *úrovně citlivosti dat* uložených ve sloupci. 
- **Typy informací** – poskytuje další členitost na *typ dat* uložených ve sloupci.

Možnosti zásad ochrany informací v rámci Security Center poskytují předdefinovanou sadu popisků a typů informací, které slouží jako výchozí hodnoty pro modul klasifikace. Zásady můžete přizpůsobit podle potřeb vaší organizace, jak je popsáno níže.

> [!IMPORTANT]
> K přizpůsobení zásad služby Information Protection pro vašeho tenanta Azure budete potřebovat oprávnění správce pro kořenovou skupinu pro správu tenanta. Další informace najdete v informacích [o Azure Security Center v nejrůznějších klientech](security-center-management-groups.md).

:::image type="content" source="./media/security-center-info-protection-policy/sql-information-protection-policy-page.png" alt-text="Stránka zobrazující zásady služby SQL Information Protection":::
 



## <a name="how-do-i-access-the-sql-information-protection-policy"></a>Návody získat přístup k zásadám SQL Information Protection?

Existují tři způsoby, jak získat přístup k zásadám ochrany informací:

- **(Doporučeno)** Na stránce ceny a nastavení Security Center
- Z doporučení zabezpečení by se měla klasifikovat citlivá data v databázích SQL.
- Stránka pro zjišťování dat databáze SQL Azure

Každá z nich je zobrazena na příslušné kartě níže.



### <a name="from-security-centers-settings"></a>[**Z nastavení Security Center**](#tab/sqlip-tenant)

### <a name="access-the-policy-from-security-centers-pricing-and-settings-page"></a>Přístup k zásadě ze stránky s cenami a nastaveními Security Center <a name="sqlip-tenant"></a>

Na stránce **ceny a nastavení** ze Security Center vyberte **SQL Information Protection**.

> [!NOTE]
> Tato možnost se zobrazí jenom pro uživatele s oprávněními na úrovni tenanta. 

:::image type="content" source="./media/security-center-info-protection-policy/pricing-settings-link-to-information-protection.png" alt-text="Přístup k zásadám SQL Information Protection ze stránky ceny a nastavení Azure Security Center":::



### <a name="from-security-centers-recommendation"></a>[**Z doporučení Security Center**](#tab/sqlip-db)

### <a name="access-the-policy-from-the-security-center-recommendation"></a>Přístup k zásadám z Security Center doporučení <a name="sqlip-db"></a>

Použijte doporučení Security Center, "citlivá data ve vašich databázích SQL by měla být klasifikovaná", aby se zobrazila stránka pro zjišťování a klasifikace dat pro vaši databázi. Tady uvidíte také zjištěné sloupce, které obsahují informace, které doporučujeme klasifikovat.

1. Na stránce **doporučení** pro Security Center vyhledejte, že by se **měla klasifikovat citlivá data v databázích SQL**.

    :::image type="content" source="./media/security-center-info-protection-policy/sql-sensitive-data-recommendation.png" alt-text="Hledání doporučení, které poskytuje přístup k zásadám SQL Information Protection":::

1. Na stránce s podrobnostmi o doporučení vyberte databázi z **nefunkčních** **karet nebo** z nich.

1. Otevře se stránka **klasifikace & zjišťování dat** . Vyberte **Konfigurovat**.

    :::image type="content" source="./media/security-center-info-protection-policy/access-policy-from-security-center-recommendation.png" alt-text="Otevřete zásady služby SQL Information Protection z příslušného doporučení v Azure Security Center":::



### <a name="from-azure-sql"></a>[**Z Azure SQL**](#tab/sqlip-azuresql)

### <a name="access-the-policy-from-azure-sql"></a>Přístup k zásadám z Azure SQL <a name="sqlip-azuresql"></a>

1. Z Azure Portal otevřete Azure SQL.

    :::image type="content" source="./media/security-center-info-protection-policy/open-azure-sql.png" alt-text="Otevření Azure SQL z Azure Portal":::

1. Vyberte libovolnou databázi.

1. Z oblasti **zabezpečení** v nabídce otevřete stránku **klasifikace & zjišťování dat** (1) a vyberte **Konfigurovat** (2).

    :::image type="content" source="./media/security-center-info-protection-policy/access-policy-from-azure-sql.png" alt-text="Otevření zásad služby SQL Information Protection z Azure SQL":::

--- 


## <a name="customize-your-information-types"></a>Přizpůsobení typů informací

Správa a přizpůsobení typů informací:

1. Vyberte možnost **Spravovat typy informací**.

    :::image type="content" source="./media/security-center-info-protection-policy/manage-types.png" alt-text="Správa typů informací pro zásady služby Information Protection":::

1. Chcete-li přidat nový typ, vyberte možnost **vytvořit typ informací**. Pro typ informací můžete nakonfigurovat řetězec název, popis a vzor hledání. Řetězce vzorů hledání mohou volitelně používat klíčová slova se zástupnými znaky (pomocí znaku '% '), který modul automatizovaného zjišťování používá k identifikaci citlivých dat ve vašich databázích na základě metadat sloupců.
 
    :::image type="content" source="./media/security-center-info-protection-policy/configure-new-type.png" alt-text="Konfigurace nového typu informací pro zásady služby Information Protection":::

1. Můžete také upravit předdefinované typy přidáním dalších řetězců vzorů hledání, zakázáním některých existujících řetězců nebo změnou popisu. 

    > [!TIP]
    > Předdefinované typy nemůžete odstranit ani změnit jejich názvy. 

1. **Typy informací** jsou uvedené v pořadí podle vzestupného řazení, což znamená, že se typy vyšší v seznamu pokusí porovnat jako první. Chcete-li změnit hodnocení mezi typy informací, přetáhněte typy na pravé místo v tabulce nebo pomocí tlačítek **nahoru** a **dolů** změňte pořadí. 

1. Po dokončení vyberte **OK** .

1. Po dokončení správy typů informací nezapomeňte přidružit relevantní typy k příslušným popiskům kliknutím na možnost **Konfigurovat** pro konkrétní popisek a podle potřeby přidat nebo odstranit typy informací.

1. Chcete-li použít změny, vyberte možnost **Uložit** na stránce hlavní **Štítky** .
 

## <a name="exporting-and-importing-a-policy"></a>Export a Import zásady

Můžete si stáhnout soubor JSON s definovanými popisky a typy informací, upravit soubor v libovolném editoru a pak importovat aktualizovaný soubor. 

:::image type="content" source="./media/security-center-info-protection-policy/export-import.png" alt-text="Export a import zásad služby Information Protection":::

> [!NOTE]
> K importu souboru zásad budete potřebovat oprávnění na úrovni tenanta. 


## <a name="manage-sql-information-protection-using-azure-powershell"></a>Správa SQL Information Protection pomocí Azure PowerShell

- [Get-AzSqlInformationProtectionPolicy](/powershell/module/az.security/get-azsqlinformationprotectionpolicy): načte efektivní zásadu SQL Information Protection tenanta.
- [Set-AzSqlInformationProtectionPolicy](/powershell/module/az.security/set-azsqlinformationprotectionpolicy): nastaví efektivní zásady pro SQL Information Protection tenanta.
 

## <a name="next-steps"></a>Další kroky
 
V tomto článku jste se dozvěděli o definování zásad služby Information Protection v Azure Security Center. Další informace o použití Information Protection SQL ke klasifikaci a ochraně citlivých dat ve vašich databázích SQL najdete v tématu [Azure SQL Database zjišťování a klasifikace dat](../azure-sql/database/data-discovery-and-classification-overview.md).

Další informace o zásadách zabezpečení a zabezpečení dat v Security Center najdete v následujících článcích:
 
- [Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md): Naučte se konfigurovat zásady zabezpečení pro vaše předplatná Azure a skupiny prostředků.
- [Azure Security Center zabezpečení dat](security-center-data-security.md): Přečtěte si, jak Security Center spravuje a chrání data.
