---
title: Vytváření a správa projektů
description: Umožňuje najít, vytvořit, spravovat a odstranit projekty v Azure Migrate.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: cb0ac41d469ad9a7670ce4b1bae23b315a17dc38
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104871056"
---
# <a name="create-and-manage-projects"></a>Vytváření a správa projektů

Tento článek popisuje, jak vytvořit, spravovat a odstranit [projekty](migrate-services-overview.md). 

Vyřazení z klasického Azure Migrate do února 2024. Po 2024. února již nebude klasická verze Azure Migrate podporována a metadata inventáře v klasickém projektu budou odstraněna. Pokud používáte klasické projekty, odstraňte tyto projekty a postupujte podle pokynů pro vytvoření nového projektu. Klasické projekty nebo komponenty nemůžete upgradovat na Azure Migrate. Před zahájením procesu vytváření se můžete podívat na [Nejčastější dotazy](./resources-faq.md#i-have-a-project-with-the-previous-classic-experience-of-azure-migrate-how-do-i-start-using-the-new-version) .

Projekt se používá k ukládání metadat zjišťování, hodnocení a migrace shromážděných z prostředí, které vyhodnotilte nebo migrujete. V projektu můžete sledovat zjištěné prostředky, vytvářet posouzení a orchestrovat migrace do Azure.  

## <a name="verify-permissions"></a>Ověření oprávnění

Ověřte, zda máte správná oprávnění k vytvoření projektu:

1. V Azure Portal otevřete příslušné předplatné a vyberte **řízení přístupu (IAM)**.
2. V části **kontrolovat přístup** Najděte příslušný účet a vyberte oprávnění zobrazit. Měli byste mít oprávnění *Přispěvatel* nebo *Owner* . 


## <a name="create-a-project-for-the-first-time"></a>První vytvoření projektu

Nastavte nový projekt v předplatném Azure.

1. V Azure Portal vyhledejte *Azure Migrate*.
2. V **služby** vyberte **Azure Migrate**.
3. V části **Přehled** vyberte **Posoudit a migrovat servery**.

    :::image type="content" source="./media/create-manage-projects/assess-migrate-servers.png" alt-text="Možnost v přehledu k vyhodnocení a migraci serverů":::

4. V na **serverech** vyberte **vytvořit projekt**.

    :::image type="content" source="./media/create-manage-projects/create-project.png" alt-text="Tlačítko pro zahájení vytváření projektu":::

5. V části **vytvořit projekt** vyberte předplatné Azure a skupinu prostředků. Vytvořte skupinu prostředků, pokud ji nemáte.
6. V části **Project Details (podrobnosti projektu**) zadejte název projektu a zeměpisnou oblast, ve které chcete vytvořit projekt.
    - Zeměpisná oblast se používá jenom k ukládání metadat shromážděných z místních serverů. Můžete vybrat libovolnou cílovou oblast pro migraci. 
    - Projděte si podporované geografické oblasti pro cloudy [veřejné](migrate-support-matrix.md#supported-geographies-public-cloud) a [státní správy](migrate-support-matrix.md#supported-geographies-azure-government).

8. Vyberte **Vytvořit**.

     :::image type="content" source="./media/create-manage-projects/project-details.png" alt-text="Nastavení stránky na vstupní projekt":::


Počkejte několik minut, než se projekt nasadí.

## <a name="create-a-project-in-a-specific-region"></a>Vytvoření projektu v konkrétní oblasti

Na portálu můžete vybrat zeměpisnou oblast, ve které chcete projekt vytvořit. Pokud chcete vytvořit projekt v konkrétní oblasti Azure, použijte následující příkaz rozhraní API k vytvoření projektu.

```rest
PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"
```

## <a name="create-additional-projects"></a>Vytvořit další projekty

Pokud již máte projekt a chcete vytvořit další projekt, udělejte toto:  

1. Na [veřejném portálu Azure](https://portal.azure.com) nebo [Azure Government](https://portal.azure.us)vyhledejte **Azure Migrate**.
2. Na Azure Migrate řídicím panelu > **servery** vyberte v pravém horním rohu možnost **změnit** .

    :::image type="content" source="./media/create-manage-projects/switch-project.png" alt-text="Změnit projekt":::

3. Chcete-li vytvořit nový projekt, vyberte **možnost klikněte sem**.


## <a name="find-a-project"></a>Najít projekt

Vyhledejte projekt následujícím způsobem:

1. V [Azure Portal](https://portal.azure.com)vyhledejte *Azure Migrate*.
2. V řídicím panelu Azure Migrate > **servery** vyberte v pravém horním rohu možnost **změnit** .

    :::image type="content" source="./media/create-manage-projects/switch-project.png" alt-text="Přepnout na existující projekt":::

3. Vyberte příslušné předplatné a projekt.


### <a name="find-a-classic-project"></a>Najít klasický projekt

Pokud jste projekt vytvořili v [předchozí verzi](migrate-services-overview.md#azure-migrate-versions) Azure Migrate, najděte ho následujícím způsobem:

1. V [Azure Portal](https://portal.azure.com)vyhledejte *Azure Migrate*.
2. Pokud jste v řídicím panelu Azure Migrate vytvořili projekt v předchozí verzi, zobrazí se banner odkazující na starší projekty. Vyberte banner.

    :::image type="content" source="./media/create-manage-projects/access-existing-projects.png" alt-text="Přístup ke stávajícím projektům":::

3. Zkontrolujte seznam starých projektů.


## <a name="delete-a-project"></a>Odstranit projekt

Odstraňte následujícím způsobem:

1. Otevřete skupinu prostředků Azure, ve které se projekt vytvořil.
2. Na stránce skupina prostředků vyberte **Zobrazit skryté typy**.
3. Vyberte projekt, který chcete odstranit, a jeho přidružené prostředky.
    - Typ prostředku je **Microsoft. migruje/migrateprojects**.
    - Pokud se skupina prostředků používá výhradně v projektu, můžete odstranit celou skupinu prostředků.

Poznámky:

- Při odstranění se odstraní projekt i metadata o zjištěných serverech.
- Pokud používáte starší verzi Azure Migrate, otevřete skupinu prostředků Azure, ve které se projekt vytvořil. Vyberte projekt, který chcete odstranit (typ prostředku je **projekt migrace**).
- Pokud používáte analýzu závislostí s pracovním prostorem Azure Log Analytics:
    - Pokud jste připojili Log Analytics pracovní prostor k nástroji pro vyhodnocení serveru, pracovní prostor se automaticky neodstraní. Stejný pracovní prostor Log Analytics lze použít pro více scénářů.
    - Pokud chcete pracovní prostor Log Analytics odstranit, udělejte to ručně.
- Odstranění projektu je nevratné. Odstraněné objekty se nedají obnovit.

### <a name="delete-a-workspace-manually"></a>Ruční odstranění pracovního prostoru

1. Přejděte do pracovního prostoru Log Analytics připojeného k projektu.

    - Pokud jste neodstranili projekt, můžete najít odkaz na pracovní prostor v rámci   >  **posouzení serveru** Essentials.
    :::image type="content" source="./media/create-manage-projects/loganalytics-workspace.png" alt-text="Pracovní prostor LA":::
       
    - Pokud jste projekt už odstranili, vyberte **skupiny prostředků** v levém podokně Azure Portal a najděte pracovní prostor.
       
2. [Podle pokynů](../azure-monitor/logs/delete-workspace.md) odstraňte pracovní prostor.

## <a name="next-steps"></a>Další kroky

Přidejte nástroje pro [posouzení](how-to-assess.md) nebo nástroj pro [migraci](how-to-migrate.md) do projektů.