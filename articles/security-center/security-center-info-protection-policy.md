---
title: Vlastní nastavení zásad ochrany informací SQL ve službě Azure Security Center | Dokumentace Microsoftu
description: Zjistěte, jak přizpůsobit zásad ochrany informací ve službě Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 2ebf2bc7-232a-45c4-a06a-b3d32aaf2500
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: rkarlin
ms.openlocfilehash: 9b63fb963408b8f22453c7ea78e36a49402273a7
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56105718"
---
# <a name="customize-the-sql-information-protection-policy-in-azure-security-center-preview"></a>Vlastní nastavení zásad ochrany informací SQL ve službě Azure Security Center (Preview)
 
Zásady služby SQL information protection je možné definovat a přizpůsobená pro váš celého tenanta Azure, ve službě Azure Security Center.

Služby Information protection je schopností pokročilé zabezpečení pro zjišťování, klasifikaci, označování popisky a ochranu citlivých dat ve vašich prostředcích Azure data. Zjišťování a klasifikace vaše nejcitlivější data (business, finanční, zdravotní péče, identifikovatelné osobní údaje, atd.) můžete přehrát velmi důležitou roli v zásadní roli. ochrany vaší organizace. Může sloužit jako infrastruktura pro:
- Pomáhá splnit požadavky na dodržování legislativních předpisů a standardy ochrany osobních údajů dat
- Různé scénáře zabezpečení, jako je například monitorování (auditování) a upozorňuje na neobvyklé přístup k citlivým datům
- Řízení přístupu k a posílení zabezpečení dat ukládá obsahující vysoce citlivá data
 
[Služba SQL Information Protection](../sql-database/sql-database-data-discovery-and-classification.md) implementuje toto paradigma pro svá úložiště dat SQL, pro Azure SQL Database aktuálně nepodporuje. Služba SQL Information Protection automaticky zjistí a klasifikuje potenciálně citlivá data, poskytuje mechanismus pro používání popisků pro trvalé označování citlivých dat pomocí klasifikace atributy a poskytuje podrobné řídicí panel zobrazuje klasifikace stavu databáze. Kromě toho vypočítá výsledek nastavit citlivost příkazů jazyka SQL, tak, aby dotazy, které extrahují citlivá data dají explicitně auditovat a data můžou být chráněné. Podrobné informace o SQL Information Protection, najdete v článku [Azure SQL Database Data zjišťování a klasifikace](../sql-database/sql-database-data-discovery-and-classification.md).
 
Klasifikace mechanismus je založen na dvě primární konstrukce, které tvoří taxonomie klasifikace - **popisky** a **typy informací**.
- **Popisky** – atributy hlavní klasifikace, používá k definování úroveň citlivosti dat uložených ve sloupci. 
- **Typy informací** – poskytuje další členitosti do typ dat uložených ve sloupci.
 
Information Protection se dodává s integrovanou sadou popisky a typy informací, které se používají ve výchozím nastavení. Chcete-li přizpůsobit, můžete upravit zásady služby information protection ve službě Azure Security Center.
 
## <a name="customize-the-information-protection-policy"></a>Přizpůsobení zásad Information Protection
Chcete-li upravit zásady služby information protection pro vašeho tenanta Azure, musíte mít [oprávnění správce pro vašeho tenanta skupinu root management](security-center-management-groups.md). 
 
1. V hlavní nabídce služby Security Center, vyberte **zásady zabezpečení**.
2. Zvolte **hierarchické zobrazení (preview)** a potom v části **Tenanta kořenovou skupinu**, klikněte na tlačítko **upravit nastavení**.
 
   ![Nakonfigurovat zásady služby Information protection](./media/security-center-info-protection-policy/security-policy.png) 
 
3. V části **součásti zásad**, klikněte na tlačítko **Information protection**. V **informace o nastavení ochrany** stránku, uvidíte aktuální sadu popisky. Toto jsou hlavní klasifikace atributy, které slouží ke kategorizaci úroveň citlivosti vašich dat. Z tohoto místa můžete nakonfigurovat **Information protection popisky** a **typy informací** pro příslušného tenanta. 
 
### <a name="customizing-labels"></a>Přizpůsobení popisků
 
1. Můžete upravit nebo odstranit všechny existující popisek nebo přidat nový popisek. Chcete-li upravit existující popisek, vyberte tento popisek a potom klikněte na tlačítko **konfigurovat**, v horní části nebo v místní nabídce na pravé straně. Chcete-li přidat nový popisek, klikněte na tlačítko **vytvořit popisek** v horní nabídce nebo dolní tabulku popisky.
2. V **označení citlivosti konfigurovat** obrazovky, můžete vytvořit nebo změnit název popisku a popis. Můžete také nastavit, jestli je popisek aktivní nebo zakázaný přepnutím **povoleno** zapnout nebo vypnout. Nakonec můžete přidat nebo odebrat typy informací, které jsou spojené s popiskem. Žádná data zjištění odpovídající, že typ informací automaticky zahrne označení citlivosti přidružené doporučení klasifikace.
3. Klikněte na **OK**.
 
   ![Nakonfigurovat popisek citlivosti](./media/security-center-info-protection-policy/config-sensitivity-label.png)
 
4. Popisky jsou uvedeny v pořadí podle vzestupně citlivosti. Chcete-li změnit pořadí mezi popisky, přetáhnout a změnit jejich pořadí v tabulce, nebo použít popisky **nahoru** a **přesunout dolů** tlačítka, chcete-li změnit pořadí. 
 
    ![Nakonfigurovat zásady služby Information protection](./media/security-center-info-protection-policy/move-up.png)
 
5. Nezapomeňte kliknout na **Uložit** v horní části obrazovky, až budete hotovi.
 
 
## <a name="adding-and-customizing-information-types"></a>Přidání a přizpůsobení typy informací
 
1. Můžete spravovat a upravit typy informací tak, že kliknete na **spravovat typy informací**.
2. Chcete-li přidat nový **typ informací**vyberte **vytvořit typ informací** v horní nabídce. Můžete nakonfigurovat název, popis a řetězce vzor pro hledání **typ informací**. Vzor řetězce můžete volitelně použít klíčová slova se zástupnými znaky (použití znaku '%'), které modul automatizované zjišťování používá k identifikaci citlivá data ve vašich databázích na základě metadat sloupců.
 
    ![Nakonfigurovat zásady služby Information protection](./media/security-center-info-protection-policy/info-types.png)
 
3. Můžete nakonfigurovat také integrovaného **typy informací** tak, že přidáte další vyhledávací vzor řetězce, některé z existujících řetězců, zakázání nebo změnou popis. Nelze odstranit integrované **typy informací** nebo upravit jejich názvy. 
4. **Typy informací** jsou uvedeny v pořadí podle vzestupném pořadí vyhledávání, což znamená, že výše v seznamu typů se pokusí nejprve srovnat. Chcete-li změnit pořadí mezi typy informací, přetáhněte typy na správné místo v tabulce, nebo použijte **nahoru** a **přesunout dolů** tlačítka, chcete-li změnit pořadí. 
5. Klikněte na tlačítko **OK** až budete hotovi.
6. Po dokončení Správa vaše typy informací, je potřeba přidružit relevantní popisků odpovídajících typů kliknutím **konfigurovat** pro konkrétní popisek a přidávání nebo odstraňování typy informací, podle potřeby.
7. Nezapomeňte kliknout na **Uložit** v hlavním **popisky** okno, aby byly použity všechny změny.
 
Jakmile zásady služby Information protection je plně definována a uložíte, bude platit pro klasifikaci dat ve všech databázích Azure SQL ve vašem tenantovi.
 
 
## <a name="next-steps"></a>Další postup
 
V tomto článku jste se dozvěděli o definování zásady SQL Information Protection ve službě Azure Security Center. Další informace o používání SQL Information Protection ke klasifikaci a ochranu citlivých dat ve vašich databázích SQL najdete v tématu [Azure SQL Database Data zjišťování a klasifikace](../sql-database/sql-database-data-discovery-and-classification.md). 

Další informace o zabezpečení zásady a zabezpečení dat ve službě Azure Security Center najdete v následujících článcích:
 
- [Přehled zásad zabezpečení](security-center-policies-overview.md): Získejte přehled o zásad zabezpečení ve službě Security Center
- [Nastavení zásad zabezpečení ve službě Azure Security Center](tutorial-security-policy.md): Další informace o konfiguraci zásad zabezpečení pro vaše předplatná Azure a skupiny prostředků
- [Zabezpečení dat ve službě Azure Security Center](security-center-data-security.md): Zjistěte, jak se spravuje Security Center a chrání data


