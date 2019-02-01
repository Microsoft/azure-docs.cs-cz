---
title: Vytvořit plán pro testovací prostředí v učebnách ve službě Azure Lab Services | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit plány pro testovací prostředí v učebnách ve službě Azure Lab Services tak, aby virtuální počítače v Tato praktická cvičení spouštění a vypínání v určitou dobu.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2019
ms.author: spelluru
ms.openlocfilehash: 34bc8263053cd4a701c16ee1832cf1b27340a345
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55501358"
---
# <a name="create-and-manage-schedules-for-classroom-labs-in-azure-lab-services"></a>Vytvořit a spravovat plány pro testovací prostředí v učebnách ve službě Azure Lab Services 
Plány umožňují konfigurovat testovacího prostředí v učebně, tak, aby virtuální počítače v testovacím prostředí automaticky spuštění a vypnutí v určenou dobu. Můžete definovat jednorázového plánu nebo plánu opakování. Následující postupy vám postup vytvoření a správě plánů pro prostředí v učebně: 

> [!IMPORTANT]
> Plánovaná doba spuštění virtuálních počítačů proti nepočítá [odpadkový uživateli](how-to-configure-student-usage.md#set-quotas-per-user). Kvóta je na dobu mimo hodiny plánu, které student stráví na virtuálních počítačích. 

## <a name="add-a-schedule-once"></a>Přidat plán (jednou)

1. Přepněte **plány** stránku a vybrat **přidat plán** na panelu nástrojů. 

    ![Přidejte tlačítko plán na stránce plány](../media/how-to-create-schedules/add-schedule-button.png)
2. Na **přidat plán** stránky, ujistěte se, že **jednou** je vybraná možnost v horní části. Pokud není, vyberte **jednou**. 
3. Pro **naplánovat datum (povinné)**, zadejte datum, nebo vyberte ikonu kalendáře pro výběr data. 
4. Pro **počáteční čas**, vyberte čas, kdy chcete spustit virtuální počítače. Počáteční čas je vyžadována, pokud čas ukončení není nastaven. Vyberte **odebrat zahájit událost** Pokud chcete určit pouze čas ukončení. Pokud **počáteční čas** je zakázaná, vyberte **přidat počáteční událost** vedle rozevíracího seznamu, aby je. 
5. Pro **čas ukončení**, vyberte čas, kdy chcete vypnout virtuální počítače. Čas ukončení je vyžadována, pokud není nastaven počáteční čas. Vyberte **událost odebrání po zastavení** Pokud chcete zadat čas spuštění. Pokud **čas ukončení** je zakázaná, vyberte **události zastavení přidat** vedle rozevíracího seznamu, aby je.
6. Pro **časové pásmo (povinné)**, vyberte časové pásmo pro spuštění a zastavení časy, které jste zadali. 
7. Pro **poznámky**, zadejte popis případné poznámky pro daný plán. 
8. Vyberte **Uložit**. 

    ![Jednorázová plán](../media/how-to-create-schedules/add-schedule-page.png)

## <a name="add-a-recurring-schedule-weekly"></a>Přidat (týdně) plánu opakování

1. Přepněte **plány** stránku a vybrat **přidat plán** na panelu nástrojů. 

    ![Přidejte tlačítko plán na stránce plány](../media/how-to-create-schedules/add-schedule-button.png)
2. Na **přidat plán** stránce, přepněte na **týdenní** v horní části. 
3. Pro **naplánovat dnů (povinné)**, vyberte dny, ve které chcete plánu se projeví. V následujícím příkladu je vybrána pondělí až pátek. 
4. Pro **z** zadejte **naplánovat datum zahájení** nebo vyberte datum tak, že vyberete **kalendáře** tlačítko. Toto pole je povinné. 
5. Pro **datum ukončení plánu**, zadejte nebo vyberte koncové datum, na kterém jsou virtuální počítače vypnou. 
6. Pro **počáteční čas**, vyberte čas, kdy chcete spustit virtuální počítače. Počáteční čas je vyžadována, pokud čas ukončení není nastaven. Vyberte **odebrat zahájit událost** Pokud chcete určit pouze čas ukončení. Pokud **počáteční čas** je zakázaná, vyberte **přidat počáteční událost** vedle rozevíracího seznamu, aby je. 
7. Pro **čas ukončení**, vyberte čas, kdy chcete vypnout virtuální počítače. Čas ukončení je vyžadována, pokud není nastaven počáteční čas. Vyberte **událost odebrání po zastavení** Pokud chcete zadat čas spuštění. Pokud **čas ukončení** je zakázaná, vyberte **události zastavení přidat** vedle rozevíracího seznamu, aby je.
8. Pro **časové pásmo (povinné)**, vyberte časové pásmo pro spuštění a zastavení časy, které jste zadali.  
9. Pro **poznámky**, zadejte popis případné poznámky pro daný plán. 
10. Vyberte **Uložit**. 

    ![Týdenní plán](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="view-schedules-in-calendar"></a>Zobrazit plány v kalendáři
Můžete zobrazit plánované dat a časů v zobrazení Kalendář zvýrazněn, jak je znázorněno na následujícím obrázku:

![Plány v zobrazení Kalendář](../media/how-to-create-schedules/schedules-in-calendar.png)

Vyberte **Dnes** tlačítko v pravém horním rohu přepnout na aktuální datum v kalendáři. Vyberte **šipku vlevo** přejděte do předchozího týdne a **šipku vpravo** přejděte do následujícího týdne v kalendáři. 

## <a name="edit-a-schedule"></a>Upravit plán
Když dvakrát kliknete na zvýrazněný plánu v kalendáři nebo vyberte **tužky** tlačítko na panelu nástrojů zobrazí **upravit plán** stránky. Aktualizuje se nastavení na této stránce je stejný jako při aktualizaci nastavení v **přidat plán** stránce, jak je popsáno v [přidat opakovaně](#add-a-recurring-schedule-weekly) oddílu. 

![Upravit plán stránku](../media/how-to-create-schedules/edit-schedule-page.png)

## <a name="delete-a-schedule"></a>Odstranění plánu

1. Pokud chcete odstranit plán, vyberte Koš (odstranit) na panelu nástrojů, jak je znázorněno na následujícím obrázku:

    ![Odstranění tlačítka na panelu nástrojů](../media/how-to-create-schedules/delete-schedule-button.png)

    Můžete použít tlačítko pro odstranění pro všechny naplánované datum a čas v kalendáři a vybrat **odstranit**. 
2. Na **odstranit plány** stránce **Ano**.

    ![Plány potvrzení odstranění](../media/how-to-create-schedules/delete-schedules-confirmation.png)




## <a name="next-steps"></a>Další postup
Viz následující články:

- [Jako správce vytvářet a spravovat účty pro testovací prostředí](how-to-manage-lab-accounts.md)
- [Jako vlastník testovacího prostředí vytvoření a správa testovacích prostředí](how-to-manage-classroom-labs.md)
- [Jako vlastník testovacího prostředí konfigurovat a spravovat využití testovacího prostředí](how-to-configure-student-usage.md)
- [Jako uživatel, který testovací prostředí přístup k testovacím prostředím v učebnách](how-to-use-classroom-lab.md)
