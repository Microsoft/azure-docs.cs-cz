---
title: Přizpůsobení SQL Information Protection – Azure Security Center
description: Přečtěte si, jak přizpůsobit zásady ochrany informací v Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2ebf2bc7-232a-45c4-a06a-b3d32aaf2500
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/11/2020
ms.author: memildin
ms.openlocfilehash: c7586cca21d470bcb0cf637ca0370f5dfcb59406
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90901098"
---
# <a name="customize-the-sql-information-protection-policy-in-azure-security-center-preview"></a>Přizpůsobení zásad SQL Information Protection v Azure Security Center (Preview)
 
Můžete definovat a přizpůsobit zásady služby SQL Information Protection pro celý tenant Azure v Azure Security Center.

Ochrana informací je pokročilá funkce zabezpečení pro zjišťování, klasifikaci, označování a vytváření sestav citlivých dat v datových prostředcích Azure. Vyhledávání a klasifikace vašich citlivých dat (podniková, finanční, zdravotní péče, osobní údaje atd.) mohou hrát pivotovou roli v stature organizace Information Protection. Může sloužit jako infrastruktura na:
- Pomoc se splněním standardů ochrany osobních údajů a požadavků na dodržování legislativních předpisů
- Scénáře zabezpečení, jako je monitorování (auditování) a upozorňování na neobvyklé přístup k citlivým datům
- Řízení přístupu a posílení zabezpečení úložišť dat obsahujících vysoce citlivá data
 
[SQL Information Protection](../azure-sql/database/data-discovery-and-classification-overview.md) implementuje toto paradigma pro vaše úložiště dat SQL, které se v současné době podporují pro Azure SQL Database. SQL Information Protection automaticky zjišťuje a klasifikuje potenciálně citlivá data, poskytuje mechanismus označování pro trvalé označení citlivých dat pomocí atributů klasifikace a poskytuje podrobný řídicí panel, který ukazuje stav klasifikace databáze. Kromě toho počítá sadu výsledků dotazu SQL, takže dotazy, které extrahují citlivá data, lze explicitně auditovat a data lze chránit. Další informace o Information Protection SQL najdete v tématu [Azure SQL Database zjišťování a klasifikace dat](../azure-sql/database/data-discovery-and-classification-overview.md).
 
Klasifikační mechanizmus je založen na dvou primárních konstrukcích, které tvoří klasifikaci klasifikace – **popisky** a **typy informací**.
- **Labels** – hlavní atributy klasifikace, které slouží k definování úrovně citlivosti dat uložených ve sloupci. 
- **Typy informací** – poskytuje další členitost na typ dat uložených ve sloupci.
 
Information Protection obsahuje integrovanou sadu popisků a typů informací, které se používají ve výchozím nastavení. Pokud chcete tyto popisky a typy přizpůsobit, můžete zásady ochrany informací v Security Center přizpůsobit.
 
## <a name="customize-the-information-protection-policy"></a>Přizpůsobení zásad Information Protection
Chcete-li upravit zásady ochrany informací pro vašeho tenanta Azure, musíte mít [oprávnění správce pro kořenovou skupinu pro správu klienta](security-center-management-groups.md). 
 
1. V hlavní nabídce Security Center v části **hygiena zabezpečení prostředků** přejděte na **data & úložiště** a klikněte na tlačítko **SQL Information Protection** .

   ![Konfigurace zásad ochrany informací](./media/security-center-info-protection-policy/security-policy.png) 
 
2. Na stránce **Information Protection SQL** můžete zobrazit aktuální sadu štítků. Jedná se o hlavní atributy klasifikace, které se používají k kategorizaci úrovně citlivosti vašich dat. Tady můžete nakonfigurovat **popisky ochrany informací** a **typy informací** pro tenanta. 
 
### <a name="customizing-labels"></a>Přizpůsobení popisků
 
1. Můžete upravit nebo odstranit existující popisek nebo přidat nový popisek. Pokud chcete upravit existující popisek, vyberte tento popisek a pak klikněte na **Konfigurovat**, a to buď v horní části, nebo v místní nabídce napravo. Chcete-li přidat nový popisek, klikněte na tlačítko **vytvořit popisek** v horním řádku nabídky nebo v dolní části tabulky popisků.
2. Na obrazovce pro **označení citlivosti konfigurace** můžete vytvořit nebo změnit název popisku a jeho popis. Můžete také nastavit, zda je popisek aktivní nebo zakázaný přepnutím přepínače **Enabled** zapnuto nebo vypnuto. Nakonec můžete přidat nebo odebrat typy informací přidružené k popisku. Všechna zjištěná data, která odpovídají danému typu informací, budou automaticky zahrnovat popisek související citlivosti v doporučeních klasifikace.
3. Klikněte na **OK**.
 
   ![Konfigurovat popisek citlivosti](./media/security-center-info-protection-policy/config-sensitivity-label.png)
 
4. Popisky jsou uvedené v pořadí podle vzestupné citlivosti. Chcete-li změnit pořadí mezi popisky, přetáhněte popisky, abyste je změnili v tabulce, nebo použijte tlačítka **nahoru a** **dolů** pro změnu pořadí. 
 
    ![Seznam popisků](./media/security-center-info-protection-policy/move-up.png)
 
5. Až skončíte, nezapomeňte kliknout na **Uložit** v horní části obrazovky.
 
 
## <a name="adding-and-customizing-information-types"></a>Přidávání a přizpůsobení typů informací
 
1. Kliknutím na **Spravovat typy informací**můžete spravovat a upravovat typy informací.
2. Chcete-li přidat nový **typ informací**, v horní nabídce vyberte možnost **vytvořit typ informací** . Pro **typ informací**můžete nakonfigurovat řetězec název, popis a vzor hledání. Řetězce vzorů hledání mohou volitelně používat klíčová slova se zástupnými znaky (pomocí znaku '% '), který modul automatizovaného zjišťování používá k identifikaci citlivých dat ve vašich databázích na základě metadat sloupců.
 
    ![Vytvořit typ informací](./media/security-center-info-protection-policy/info-types.png)
 
3. Můžete také nakonfigurovat integrované **typy informací** přidáním dalších řetězců vzorů hledání, zakázáním některých existujících řetězců nebo změnou popisu. Předdefinované **typy informací** nemůžete odstranit ani upravit jejich názvy. 
4. **Typy informací** jsou uvedené v pořadí podle vzestupného řazení, což znamená, že se typy vyšší v seznamu pokusí porovnat jako první. Chcete-li změnit hodnocení mezi typy informací, přetáhněte typy na pravé místo v tabulce nebo pomocí tlačítek **nahoru** a **dolů** změňte pořadí. 
5. Až skončíte, klikněte na **OK** .
6. Po dokončení správy typů informací nezapomeňte přidružit relevantní typy k příslušným popiskům kliknutím na možnost **Konfigurovat** pro konkrétní popisek a podle potřeby přidat nebo odstranit typy informací.
7. V okně Hlavní **Štítky** nezapomeňte kliknout na **Uložit** , aby se všechny změny projevily.
 
Po úplném definování a uložení zásad služby Information Protection se tato zásada bude vztahovat na klasifikaci dat ve všech databázích Azure SQL ve vašem tenantovi.

## <a name="manage-sql-information-protection-using-azure-powershell"></a>Správa SQL Information Protection pomocí Azure PowerShell

- [Get-AzSqlInformationProtectionPolicy](https://docs.microsoft.com/powershell/module/az.security/get-azsqlinformationprotectionpolicy): načte efektivní zásadu SQL Information Protection tenanta.
- [Set-AzSqlInformationProtectionPolicy](https://docs.microsoft.com/powershell/module/az.security/set-azsqlinformationprotectionpolicy): nastaví efektivní zásady pro SQL Information Protection tenanta.
 
## <a name="next-steps"></a>Další kroky
 
V tomto článku jste se dozvěděli o definování zásad Information Protection SQL v Azure Security Center. Další informace o použití Information Protection SQL ke klasifikaci a ochraně citlivých dat ve vašich databázích SQL najdete v tématu [Azure SQL Database zjišťování a klasifikace dat](../azure-sql/database/data-discovery-and-classification-overview.md). 

Další informace o zásadách zabezpečení a zabezpečení dat v Azure Security Center najdete v následujících článcích:
 
- [Nastavení zásad zabezpečení v Azure Security Center](tutorial-security-policy.md): Naučte se konfigurovat zásady zabezpečení pro vaše předplatná Azure a skupiny prostředků.
- [Azure Security Center zabezpečení dat](security-center-data-security.md): Přečtěte si, jak Security Center spravuje a chrání data.
