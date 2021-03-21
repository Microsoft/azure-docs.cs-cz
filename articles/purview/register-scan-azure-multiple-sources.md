---
title: Jak kontrolovat víc zdrojů v Azure
description: Naučte se kontrolovat celé předplatné Azure nebo skupinu prostředků ve službě Azure dosah Data Catalog.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 2/26/2021
ms.openlocfilehash: 098f62365971fd634001706ab99fd414a6b25056
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102123560"
---
# <a name="register-and-scan-azure-multiple-sources"></a>Registrace a skenování více zdrojů Azure

Tento článek popisuje, jak v dosah zaregistrovat více zdrojů Azure (předplatná Azure nebo skupiny prostředků) a jak na nich nastavovat kontrolu.

## <a name="supported-capabilities"></a>Podporované funkce

Azure Multiple source podporuje kontroly, které zachytí metadata a schéma pro většinu typů prostředků Azure, které podporuje dosah. Také klasifikuje data automaticky podle systémových a vlastních pravidel klasifikace.

## <a name="prerequisites"></a>Předpoklady

- Před registrací zdrojů dat vytvořte účet Azure dosah. Další informace o vytvoření účtu dosah najdete v tématu [rychlý Start: vytvoření účtu Azure dosah](create-catalog-portal.md).
- Musíte být správcem zdroje dat služby Azure dosah.
- Nastavení ověřování, jak je popsáno v následujících částech

### <a name="setting-up-authentication-for-enumerating-resources-under-a-subscription-or-resource-group"></a>Nastavení ověřování pro vytváření výčtu prostředků v rámci předplatného nebo skupiny prostředků

1. Přejděte do předplatného nebo do skupiny prostředků v Azure Portal.  
1. V navigační nabídce vlevo vyberte **Access Control (IAM)**.   
1. Abyste mohli přidat roli do předplatného nebo skupiny prostředků, musíte být vlastníkem nebo správcem uživatelského přístupu. Tlačítko vybrat *+ Přidat* . 
1. Nastavte roli **Čtenář** a v části Vybrat vstupní pole zadejte název vašeho účtu Azure dosah (který představuje jeho MSI). Kliknutím na *Uložit* dokončete přiřazení role.

### <a name="setting-up-authentication-to-scan-resources-under-a-subscription-or-resource-group"></a>Nastavení ověřování pro kontrolu prostředků v rámci předplatného nebo skupiny prostředků

Existují dva způsoby, jak nastavit ověřování pro Azure Multiple Source:

- Spravovaná identita
- Instanční objekt

> [!NOTE]
> Je nutné nastavit ověřování u každého prostředku v rámci předplatného nebo skupiny prostředků, které máte v úmyslu registrovat a kontrolovat. Typy prostředků úložiště Azure (Azure Blob Storage a Azure Data Lake Storage Gen2) usnadňují přidání MSI nebo instančního objektu na úrovni předplatného nebo skupiny prostředků jako čtečky dat objektů BLOB úložiště. Tato oprávnění se pak trickle dolů na každý účet úložiště v rámci daného předplatného nebo skupiny prostředků. U všech ostatních typů prostředků musíte použít soubor MSI nebo instanční objekt u každého prostředku nebo skript, který to udělá, na zařízení. Tady je postup, jak přidat oprávnění pro jednotlivé typy prostředků v rámci předplatného nebo skupiny prostředků.
    
- [Azure Blob Storage](register-scan-azure-blob-storage-source.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#setting-up-authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#setting-up-authentication-for-a-scan)
- [Azure SQL Database](register-scan-azure-sql-database.md)
- [Azure SQL Database spravovaná instance](register-scan-azure-sql-database-managed-instance.md#setting-up-authentication-for-a-scan)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#setting-up-authentication-for-a-scan)
 
## <a name="register-an-azure-multiple-source"></a>Registrace více zdrojů Azure

Pokud chcete zaregistrovat nový Azure Multiple source v katalogu dat, udělejte toto:

1. Přejděte k účtu dosah.
1. Výběr **zdrojů** na levém navigačním panelu
1. Vybrat **registraci**
1. V případě **registrování zdrojů** vyberte **Azure (více)** .
1. Vyberte **Pokračovat**.

   :::image type="content" source="media/register-scan-azure-multiple-sources/register-azure-multiple.png" alt-text="Registrovat Azure Multiple source":::

Na obrazovce **Registrovat zdroje (Azure Multiple)** udělejte toto:

1. Zadejte **název** , se kterým bude zdroj dat uveden v katalogu. 
1. Volitelně můžete zvolit **skupinu pro správu** , ke které se má filtrovat
1. V rozevíracím seznamu **Vyberte předplatné nebo konkrétní skupinu prostředků** v rámci daného předplatného. Rozsah registrace se nastaví na vybrané předplatné nebo skupinu prostředků.  
1. Vyberte **kolekci** nebo vytvořte novou (volitelné).
1. **Dokončit** pro registraci zdroje dat

   :::image type="content" source="media/register-scan-azure-multiple-sources/azure-multiple-source-setup.png" alt-text="Nastavení více zdrojů Azure":::

## <a name="creating-and-running-a-scan"></a>Vytvoření a spuštění kontroly

Pokud chcete vytvořit a spustit novou kontrolu, udělejte toto:

1. Přejděte do části **sources (zdroje** ).

1. Vyberte zdroj dat, který jste zaregistrovali.

1. Klikněte na Zobrazit podrobnosti a vyberte **+ Nová kontrola** nebo použijte ikonu prohledat rychlou akci na dlaždici zdroje.

1. Zadejte *název* a vyberte všechny typy prostředků, které chcete kontrolovat v rámci tohoto zdroje.

    1. Můžete ponechat to *vše* (to zahrnuje budoucí typy prostředků, které v daném předplatném nebo skupině prostředků aktuálně nemusí existovat).
    1. Můžete **vybrat konkrétní typy prostředků** , které chcete kontrolovat. Pokud zvolíte tuto možnost, budoucí typy prostředků, které se můžou vytvořit v rámci tohoto předplatného nebo skupiny prostředků, nebudou zahrnuté do kontrol, pokud se tato kontrola v budoucnu explicitně neupraví.
    
    :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-source-scan.png" alt-text="Kontrola více zdrojů v Azure":::

1. Vyberte přihlašovací údaje pro připojení k prostředkům v rámci zdroje dat. 
    1. Přihlašovací údaje můžete vybrat **na nadřazené úrovni** jako MSI nebo konkrétní pověření typu instančního objektu, které můžete použít pro všechny typy prostředků v rámci předplatného nebo skupiny prostředků.
    1. Můžete také konkrétně **Vybrat typ prostředku a použít** pro tento typ prostředku jiné přihlašovací údaje.
    1. Každé přihlašovací údaje se považují za metodu ověřování pro všechny prostředky v rámci určitého typu.
    1. Musíte nastavit zvolené přihlašovací údaje k prostředkům, aby je bylo možné úspěšně zkontrolovat, jak je popsáno v této [části](#setting-up-authentication-to-scan-resources-under-a-subscription-or-resource-group) výše.
1. V rámci každého typu můžete vybrat buď kontrolu všech prostředků, nebo jejich podmnožinu podle názvu.
    1. Pokud ponecháte tuto možnost **, budou** se také prověřit budoucí prostředky tohoto typu v budoucích běhůch kontroly.
    1. Pokud vyberete možnost konkrétní účty úložiště nebo databáze SQL, nebudou se v rámci tohoto předplatného nebo skupiny prostředků vycházet z budoucích prostředků vytvořených v rámci tohoto předplatného nebo skupiny prostředků, pokud se kontrola v budoucnu explicitně neupraví.
 
1.  Pokračujte kliknutím na **pokračovat** . Otestujeme přístup a zkontrolujeme, jestli jste jako čtecí modul pro předplatné nebo skupinu prostředků použili dosah MSI. Pokud se vyvolá chybová zpráva, postupujte [podle pokynů.](#setting-up-authentication-for-enumerating-resources-under-a-subscription-or-resource-group)

1.  Vyberte možnost **Prohledat sady pravidel** pro každý typ prostředku, který jste zvolili v předchozím kroku. Můžete také vytvořit sady pravidel skenování inline.
  :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-scan-rule-set.png" alt-text="Výběr sady pravidel pro více kontrol Azure":::

1. Vyberte aktivační událost kontroly. Můžete naplánovat, aby se spouštěla **týdně** nebo  měsíčně.

1. Zkontrolujte prověřování a vyberte Uložit a dokončete nastavení.   

## <a name="viewing-your-scans-and-scan-runs"></a>Zobrazení kontrol a spuštění kontroly

1. Zobrazte podrobnosti o zdroji kliknutím na tlačítko **Zobrazit podrobnosti** na dlaždici v části zdroje. 

      :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-source-detail.png" alt-text="Podrobnosti o více zdrojích Azure"::: 

1. Podívejte se na stránku s **podrobnostmi o prověřování** a Prohlédněte si podrobnosti o kontrole spuštění.
    1. *Stavový řádek* je stručný souhrn stavu podřízených prostředků, které jsou v provozu. Zobrazí se na úrovni předplatného nebo skupiny prostředků.
    1. Zelená znamená úspěšné, zatímco červené prostředky selhaly. Šedá znamená, že kontrola stále probíhá.
    1. Kliknutím na každou kontrolu můžete zobrazit podrobněji odstupňované podrobnosti.

      :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-scan-full-details.png" alt-text="Podrobnosti o více prověřování Azure":::

1. Zobrazí shrnutí posledních neúspěšných kontrol, které se spouští v dolní části stránky podrobností zdroje. Můžete také kliknout na zobrazit podrobnější informace týkající se těchto spuštění.

## <a name="manage-your-scans---edit-delete-or-cancel"></a>Správa kontrol – úpravy, odstranění nebo zrušení
Pokud chcete kontrolu spravovat nebo odstranit, postupujte takto:

- Přejděte do centra pro správu. V části zdroje a skenování vyberte zdroje dat a potom vyberte požadovaný zdroj dat.

- Vyberte kontrolu, kterou chcete spravovat. Kontrolu můžete upravit výběrem možnosti upravit.

- Kontrolu můžete odstranit výběrem možnosti Odstranit
- Pokud je kontrola spuštěná, můžete ji i zrušit.

## <a name="next-steps"></a>Další kroky

- [Procházet Azure dosah Data Catalog](how-to-browse-catalog.md)
- [Hledání ve službě Azure dosah Data Catalog](how-to-search-catalog.md)    
