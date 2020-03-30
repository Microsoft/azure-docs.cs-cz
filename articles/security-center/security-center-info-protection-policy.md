---
title: Přizpůsobení ochrany informací SQL – Azure Security Center
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
ms.date: 04/29/2019
ms.author: memildin
ms.openlocfilehash: 9c776a32b4a35c72fc40a16afb87db9896a763cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75611062"
---
# <a name="customize-the-sql-information-protection-policy-in-azure-security-center-preview"></a>Přizpůsobení zásad ochrany informací SQL v Azure Security Center (Preview)
 
Můžete definovat a přizpůsobit zásady ochrany informací SQL pro celý klient Azure, v Azure Security Center.

Ochrana informací je pokročilá funkce zabezpečení pro zjišťování, klasifikaci, označování a vytváření sestav citlivých dat ve vašich datových prostředcích Azure. Zjišťování a klasifikace nejcitlivějších údajů (obchodní, finanční, zdravotní péče, osobní údaje atd.) může hrát klíčovou roli ve vaší organizační ochraně informací. Může sloužit jako infrastruktura na:
- Pomáháme splnit standardy ochrany osobních údajů a požadavky na dodržování předpisů
- Scénáře zabezpečení, jako je monitorování (auditování) a upozorňování na neobvyklý přístup k citlivým datům
- Řízení přístupu k úložištím dat obsahujícím vysoce citlivá data a posílení jeho zabezpečení
 
[SQL Information Protection](../sql-database/sql-database-data-discovery-and-classification.md) implementuje toto paradigma pro vaše úložiště dat SQL, aktuálně podporované pro Azure SQL Database. SQL Information Protection automaticky zjišťuje a klasifikuje potenciálně citlivá data, poskytuje mechanismus označování pro trvalé označování citlivých dat pomocí atributů klasifikace a poskytuje podrobný řídicí panel zobrazující stav klasifikace databáze. Kromě toho vypočítá citlivost sady výsledků dotazů SQL, takže dotazy, které extrahují citlivá data, mohou být explicitně auditovány a data mohou být chráněna. Další informace o ochraně informací SQL najdete v [tématu Zjišťování a klasifikace dat databáze Azure SQL](../sql-database/sql-database-data-discovery-and-classification.md).
 
Klasifikační mechanismus je založen na dvou primárních konstrukcích, které tvoří klasifikační taxonomii - **popisky** a **typy informací**.
- **Popisky** – hlavní atributy klasifikace, které slouží k definování úrovně citlivosti dat uložených ve sloupci. 
- **Typy informací** – poskytuje další rozlišovací schopnost do typu dat uložených ve sloupci.
 
Ochrana informací je dodávána s integrovanou sadou popisků a typů informací, které se používají ve výchozím nastavení. Chcete-li přizpůsobit tyto popisky a typy, můžete přizpůsobit zásady ochrany informací v Centru zabezpečení.
 
## <a name="customize-the-information-protection-policy"></a>Přizpůsobení zásad Information Protection
Chcete-li přizpůsobit zásady ochrany informací pro vašeho tenanta Azure, musíte mít [oprávnění správce v kořenové skupině správy klienta](security-center-management-groups.md). 
 
1. V hlavní nabídce Security Center přejděte v části **RESOURCE SECURITY HYGIENE** do data & **úložiště** a klikněte na tlačítko OCHRANA **INFORMACÍ SQL.**

   ![Konfigurace zásad ochrany informací](./media/security-center-info-protection-policy/security-policy.png) 
 
2. Na stránce **SQL Information Protection** můžete zobrazit aktuální sadu popisků. Jedná se o hlavní atributy klasifikace, které se používají ke kategorizaci úrovně citlivosti dat. Zde můžete nakonfigurovat **popisky ochrany informací** a **typy informací** pro klienta. 
 
### <a name="customizing-labels"></a>Přizpůsobení popisků
 
1. Můžete upravit nebo odstranit libovolný existující popisek nebo přidat nový popisek. Chcete-li upravit existující popisek, vyberte tento popisek a klepněte na tlačítko **Konfigurovat**nahoře nebo v místní nabídce vpravo. Pokud chcete přidat nový popisek, klikněte na **Vytvořit popisek** v horním řádku nabídek nebo dole v tabulce popisků.
2. Na obrazovce **Konfigurace popisku citlivosti** můžete vytvořit nebo změnit název popisku a popis. Můžete také nastavit, zda je popisek aktivní nebo zakázaný přepnutím **přepínače Povoleno.** Nakonec můžete přidat nebo odebrat typy informací přidružené k popisku. Všechna zjištěná data, která odpovídají danému typu informací, budou automaticky obsahovat přidružený popisek citlivosti v doporučeních klasifikace.
3. Klikněte na tlačítko **OK**.
 
   ![Konfigurace popisku citlivosti](./media/security-center-info-protection-policy/config-sensitivity-label.png)
 
4. Popisky jsou seřazeny podle vzestupné citlivosti. Chcete-li změnit pořadí mezi popisky, přetažením popisků změňte jejich pořadí v tabulce nebo změňte pořadí pomocí tlačítek **Přesunout nahoru** a **Přesunout dolů.** 
 
    ![Konfigurace zásad ochrany informací](./media/security-center-info-protection-policy/move-up.png)
 
5. Nezapomeňte po dokončení kliknout na **Uložit** v horní části obrazovky.
 
 
## <a name="adding-and-customizing-information-types"></a>Přidání a přizpůsobení typů informací
 
1. Typy informací můžete spravovat a upravovat kliknutím na **položku Spravovat typy informací**.
2. Chcete-li přidat nový **typ Informace**, vyberte v horní nabídce možnost Vytvořit **typ informací.** Můžete nakonfigurovat řetězec názvu, popisu a vyhledávacího vzoru pro **typ Informace**. Řetězce vyhledávacích vzorů mohou volitelně používat klíčová slova se zástupnými znaky (pomocí znaku %), které automatizovaný modul zjišťování používá k identifikaci citlivých dat ve vašich databázích na základě metadat sloupců.
 
    ![Konfigurace zásad ochrany informací](./media/security-center-info-protection-policy/info-types.png)
 
3. Předdefinované **typy informací** můžete také nakonfigurovat přidáním dalších řetězců vzorníku hledání, zakázáním některých existujících řetězců nebo změnou popisu. Předdefinované **typy informací** nelze odstranit ani upravit jejich názvy. 
4. **Typy informací** jsou uvedeny v pořadí vzestupně zjišťování pořadí, což znamená, že typy vyšší v seznamu se pokusí odpovídat jako první. Chcete-li změnit pořadí mezi typy informací, přetáhněte typy na správné místo v tabulce nebo změňte pořadí pomocí tlačítek **Přesunout nahoru** a **Přesunout dolů.** 
5. Až budete hotovi, klikněte na **OK.**
6. Po dokončení správy typů informací nezapomeňte přidružit příslušné typy k příslušným popiskům, kliknutím na **Konfigurovat** pro konkrétní popisek a přidáním nebo odstraněním typů informací podle potřeby.
7. Nezapomeňte kliknout na **Uložit** v hlavním okně **Štítky,** abyste použili všechny změny.
 
Jakmile budou zásady ochrany informací plně definovány a uloženy, budou se vztahovat na klasifikaci dat ve všech databázích Azure SQL ve vašem tenantovi.
 
 
## <a name="next-steps"></a>Další kroky
 
V tomto článku jste se dozvěděli o definování zásad ochrany informací SQL v Azure Security Center. Další informace o použití ochrany informací SQL ke klasifikaci a ochraně citlivých dat v databázích SQL najdete v [tématu Azure SQL Database Data Discovery and Classification](../sql-database/sql-database-data-discovery-and-classification.md). 

Další informace o zásadách zabezpečení a zabezpečení dat v Azure Security Center najdete v následujících článcích:
 
- [Nastavení zásad zabezpečení v Azure Security Center:](tutorial-security-policy.md)Zjistěte, jak nakonfigurovat zásady zabezpečení pro vaše předplatná Azure a skupiny prostředků
- [Zabezpečení dat Azure Security Center:](security-center-data-security.md)Zjistěte, jak Security Center spravuje a zabezpečuje data
