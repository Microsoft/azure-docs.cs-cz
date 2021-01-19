---
title: Vytváření a správa projektů Azure Migrate
description: Umožňuje najít, vytvořit, spravovat a odstranit projekty v Azure Migrate.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: de0c48bb775b96052fe16d60aa58049bfd58ca4d
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567782"
---
# <a name="create-and-manage-azure-migrate-projects"></a>Vytváření a správa projektů Azure Migrate

Tento článek popisuje, jak vytvářet, spravovat a odstraňovat [Azure Migrate](migrate-services-overview.md) projekty. Pokud používáte klasické Azure Migrate projekty, odstraňte je prosím a postupujte podle pokynů pro vytvoření nového projektu Azure Migrate. Nemůžete upgradovat klasické Azure Migrate projekty ani komponenty na Azure Migrate.

Azure Migrate projekt se používá k ukládání metadat zjišťování, hodnocení a migrace shromážděných z prostředí, které vyhodnotili nebo migrujete. V projektu můžete sledovat zjištěné prostředky, vytvářet posouzení a orchestrovat migrace do Azure.  

## <a name="verify-permissions"></a>Ověření oprávnění

Ověřte, zda máte správná oprávnění k vytvoření Azure Migrate projektu:

1. V Azure Portal otevřete příslušné předplatné a vyberte **řízení přístupu (IAM)**.
2. V části **kontrolovat přístup** Najděte příslušný účet a vyberte oprávnění zobrazit. Měli byste mít oprávnění *Přispěvatel* nebo *Owner* . 


## <a name="create-a-project-for-the-first-time"></a>První vytvoření projektu

Nastavte nový projekt Azure Migrate v předplatném Azure.

1. V Azure Portal vyhledejte *Azure Migrate*.
2. V **služby** vyberte **Azure Migrate**.
3. V části **Přehled** vyberte **Posoudit a migrovat servery**.

    ![Možnost v přehledu k vyhodnocení a migraci serverů](./media/create-manage-projects/assess-migrate-servers.png)

4. V na **serverech** vyberte **vytvořit projekt**.

    ![Tlačítko pro zahájení vytváření projektu](./media/create-manage-projects/create-project.png)

5. V části **vytvořit projekt** vyberte předplatné Azure a skupinu prostředků. Vytvořte skupinu prostředků, pokud ji nemáte.
6. V části **Project Details (podrobnosti projektu**) zadejte název projektu a zeměpisnou oblast, ve které chcete vytvořit projekt.
    - Zeměpisná oblast se používá jenom k ukládání metadat shromážděných z místních počítačů. Můžete vybrat libovolnou cílovou oblast pro migraci. 
    - Projděte si podporované geografické oblasti pro cloudy [veřejné](migrate-support-matrix.md#supported-geographies-public-cloud) a [státní správy](migrate-support-matrix.md#supported-geographies-azure-government).

8. Vyberte **Vytvořit**.

   ![Nastavení stránky na vstupní projekt](./media/create-manage-projects/project-details.png)


Počkejte několik minut, než se projekt Azure Migrate nasadí.

## <a name="create-a-project-in-a-specific-region"></a>Vytvoření projektu v konkrétní oblasti

Na portálu můžete vybrat zeměpisnou oblast, ve které chcete projekt vytvořit. Pokud chcete vytvořit projekt v konkrétní oblasti Azure, použijte následující příkaz rozhraní API k vytvoření projektu.

```rest
PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"
```

## <a name="create-additional-projects"></a>Vytvořit další projekty

Pokud již máte projekt Azure Migrate a chcete vytvořit další projekt, udělejte toto:  

1. Na [veřejném portálu Azure](https://portal.azure.com) nebo [Azure Government](https://portal.azure.us)vyhledejte **Azure Migrate**.
2. Na Azure Migrate řídicím panelu > **servery** vyberte v pravém horním rohu možnost **změnit** .

   ![Změnit Azure Migrate projekt](./media/create-manage-projects/switch-project.png)

3. Chcete-li vytvořit nový projekt, vyberte **možnost klikněte sem**.


## <a name="find-a-project"></a>Najít projekt

Vyhledejte projekt následujícím způsobem:

1. V [Azure Portal](https://portal.azure.com)vyhledejte *Azure Migrate*.
2. V řídicím panelu Azure Migrate > **servery** vyberte v pravém horním rohu možnost **změnit** .

    ![Přepnout na existující Azure Migrate projekt](./media/create-manage-projects/switch-project.png)

3. Vyberte odpovídající předplatné a Azure Migrate projekt.


### <a name="find-a-legacy-project"></a>Najít starší projekt

Pokud jste projekt vytvořili v [předchozí verzi](migrate-services-overview.md#azure-migrate-versions) Azure Migrate, najděte ho následujícím způsobem:

1. V [Azure Portal](https://portal.azure.com)vyhledejte *Azure Migrate*.
2. Pokud jste v řídicím panelu Azure Migrate vytvořili projekt v předchozí verzi, zobrazí se banner odkazující na starší projekty. Vyberte banner.

    ![Přístup ke stávajícím projektům](./media/create-manage-projects/access-existing-projects.png)

3. Zkontrolujte seznam starých projektů.


## <a name="delete-a-project"></a>Odstranit projekt

Odstraňte následujícím způsobem:

1. Otevřete skupinu prostředků Azure, ve které se projekt vytvořil.
2. Na stránce skupina prostředků vyberte **Zobrazit skryté typy**.
3. Vyberte projekt migrace, který chcete odstranit, a jeho přidružené prostředky.
    - Typ prostředku je **Microsoft. migruje/migrateprojects**.
    - Pokud se skupina prostředků používá výhradně v projektu Azure Migrate, můžete odstranit celou skupinu prostředků.

Poznámky:

- Při odstranění se odstraní projekt i metadata zjištěných počítačů.
- Pokud používáte starší verzi Azure Migrate, otevřete skupinu prostředků Azure, ve které se projekt vytvořil. Vyberte projekt migrace, který chcete odstranit (typ prostředku je **projekt migrace**).
- Pokud používáte analýzu závislostí s pracovním prostorem Azure Log Analytics:
    - Pokud jste připojili Log Analytics pracovní prostor k nástroji pro vyhodnocení serveru, pracovní prostor se automaticky neodstraní. Stejný pracovní prostor Log Analytics lze použít pro více scénářů.
    - Pokud chcete pracovní prostor Log Analytics odstranit, udělejte to ručně.
- Odstranění projektu je nevratné. Odstraněné objekty se nedají obnovit.

### <a name="delete-a-workspace-manually"></a>Ruční odstranění pracovního prostoru

1. Přejděte do pracovního prostoru Log Analytics připojeného k projektu.

    - Pokud jste neodstranili Azure Migrate projekt, můžete najít odkaz na pracovní prostor v rámci   >  **posouzení serveru** Essentials.
       ![LA pracovní prostor ](./media/create-manage-projects/loganalytics-workspace.png) .
       
    - Pokud jste již Azure Migrate projekt odstranili, vyberte **skupiny prostředků** v levém podokně Azure Portal a vyhledejte pracovní prostor.
       
2. [Podle pokynů](../azure-monitor/platform/delete-workspace.md) odstraňte pracovní prostor.

## <a name="next-steps"></a>Další kroky

Přidejte nástroje pro [posouzení](how-to-assess.md) nebo [migraci](how-to-migrate.md) do Azure Migrate projektů.
