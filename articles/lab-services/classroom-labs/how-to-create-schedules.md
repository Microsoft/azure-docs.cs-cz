---
title: Vytvořte plán pro učebn Labs v Azure Lab Services | Microsoft Docs
description: Naučte se vytvářet plány pro učebn Labs v Azure Lab Services tak, aby se virtuální počítače v laboratořích spouštěly v určitou dobu.
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
ms.openlocfilehash: f607ba68563aa92797f45cf77db0575ae6802fee
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385599"
---
# <a name="create-and-manage-schedules-for-classroom-labs-in-azure-lab-services"></a>Vytváření a Správa plánů pro učebn Labs v Azure Lab Services 
Plány umožňují nakonfigurovat prostředí učebny tak, aby se virtuální počítače v testovacím prostředí automaticky spouštěly a vypnuly v určitou dobu. Můžete definovat Jednorázový plán nebo plán opakování. Následující postupy vám poskytnou kroky pro vytváření a správu plánů pro prostředí učebny: 

> [!IMPORTANT]
> Plánovaná doba spuštění virtuálních počítačů se nepočítá s kvótou [přidělenou uživateli](how-to-configure-student-usage.md#set-quotas-for-users). Kvóta je určena pro dobu mimo plánované hodiny, kterou student stráví na virtuálních počítačích. 

## <a name="add-a-schedule-once"></a>Přidat plán (jednou)

1. Přepněte na stránku **plány** a na panelu nástrojů vyberte **Přidat plán** . 

    ![Tlačítko Přidat plán na stránce plány](../media/how-to-create-schedules/add-schedule-button.png)
2. Na stránce **Přidat plán** potvrďte, že je v horní části vybraná možnost **jednou** . Pokud není, vyberte **jednou**. 
3. Pro **Datum plánu (povinné)** zadejte datum nebo vyberte ikonu kalendáře a vyberte datum. 
4. V části **čas spuštění**vyberte čas, kdy se mají virtuální počítače spustit. Čas spuštění je vyžadován, pokud není nastaven čas zastavení. Vyberte možnost **Odebrat událost spuštění** , pokud chcete určit pouze dobu zastavení. Pokud je **počáteční čas** zakázaný, vyberte **Přidat spustit událost** vedle rozevíracího seznamu a povolte ho. 
5. V poli **čas zastavení**vyberte čas, kdy se mají virtuální počítače vypnout. Pokud není nastaven počáteční čas, je nutné čas zastavení. Vyberte **Odebrat událost zastavení** , pokud chcete zadat pouze počáteční čas. Pokud je **Doba zastavení** zakázaná, můžete ji povolit výběrem **Přidat událost zastavení** vedle rozevíracího seznamu.
6. V poli **časové pásmo (povinné)** vyberte časové pásmo pro dobu zahájení a ukončení, kterou jste zadali. 
7. Pro **poznámky**zadejte libovolný popis nebo poznámky k plánu. 
8. Vyberte **Uložit**. 

    ![Plán jednorázová](../media/how-to-create-schedules/add-schedule-page.png)

## <a name="add-a-recurring-schedule-weekly"></a>Přidání opakovaného plánu (týdně)

1. Přepněte na stránku **plány** a na panelu nástrojů vyberte **Přidat plán** . 

    ![Tlačítko Přidat plán na stránce plány](../media/how-to-create-schedules/add-schedule-button.png)
2. Na stránce **Přidat plán** přepněte v horní části  na týdně. 
3. Pro **dny plánu (povinné)** vyberte dny, ve kterých se má plán projevit. V následujícím příkladu je vybrána možnost pondělí – pátek. 
4. V poli **od** zadejte **Datum zahájení plánu** nebo vyberte datum výběrem tlačítka **Kalendář** . Toto pole je povinné. 
5. Do pole **Datum ukončení plánu**zadejte nebo vyberte koncové datum, kdy se mají virtuální počítače vypnout. 
6. V části **čas spuštění**vyberte čas, kdy se mají virtuální počítače spustit. Čas spuštění je vyžadován, pokud není nastaven čas zastavení. Vyberte možnost **Odebrat událost spuštění** , pokud chcete určit pouze dobu zastavení. Pokud je **počáteční čas** zakázaný, vyberte **Přidat spustit událost** vedle rozevíracího seznamu a povolte ho. 
7. V poli **čas zastavení**vyberte čas, kdy se mají virtuální počítače vypnout. Pokud není nastaven počáteční čas, je nutné čas zastavení. Vyberte **Odebrat událost zastavení** , pokud chcete zadat pouze počáteční čas. Pokud je **Doba zastavení** zakázaná, můžete ji povolit výběrem **Přidat událost zastavení** vedle rozevíracího seznamu.
8. V poli **časové pásmo (povinné)** vyberte časové pásmo pro dobu zahájení a ukončení, kterou jste zadali.  
9. Pro **poznámky**zadejte libovolný popis nebo poznámky k plánu. 
10. Vyberte **Uložit**. 

    ![Týdenní plán](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="view-schedules-in-calendar"></a>Zobrazit plány v kalendáři
Naplánovaná data a časy zvýrazněné v zobrazení Kalendář můžete zobrazit, jak je znázorněno na následujícím obrázku:

![Plány v zobrazení Kalendář](../media/how-to-create-schedules/schedules-in-calendar.png)

V pravém horním rohu vyberte tlačítko **dnes** a přepněte tak na aktuální datum v kalendáři. Vyberte **šipku doleva** pro přepnutí na předchozí týden a **šipku vpravo** , abyste přešli na další týden v kalendáři. 

## <a name="edit-a-schedule"></a>Upravit plán
Když dvakrát kliknete na zvýrazněný plán v kalendáři nebo na panelu nástrojů vyberete  tlačítko tužky, zobrazí se stránka **Upravit plán** . Aktualizace nastavení na této stránce je stejná jako aktualizace nastavení na stránce **Přidat plán** , jak je popsáno v části [Přidání opakovaného plánu](#add-a-recurring-schedule-weekly) . 

![Stránka upravit plán](../media/how-to-create-schedules/edit-schedule-page.png)

## <a name="delete-a-schedule"></a>Odstranění plánu

1. Pokud chcete odstranit plán, vyberte na panelu nástrojů tlačítko odpadkový koš (odstranit), jak je znázorněno na následujícím obrázku:

    ![Tlačítko Odstranit na panelu nástrojů](../media/how-to-create-schedules/delete-schedule-button.png)

    Tlačítko Odstranit můžete použít pro jakékoli naplánované datum a časy v kalendáři a vybrat **Odstranit**. 
2. Na stránce **Odstranit plány** vyberte **Ano**.

    ![Potvrzení odstranění plánů](../media/how-to-create-schedules/delete-schedules-confirmation.png)




## <a name="next-steps"></a>Další postup
Viz následující články:

- [Účty testovacího prostředí se vytvářejí a spravují jako správce.](how-to-manage-lab-accounts.md)
- [Jako vlastník testovacího prostředí vytvářet a spravovat cvičení](how-to-manage-classroom-labs.md)
- [Jako vlastník testovacího prostředí konfigurace a řízení využití testovacího prostředí](how-to-configure-student-usage.md)
- [Jako uživatel testovacího prostředí, Access učeben Labs](how-to-use-classroom-lab.md)
