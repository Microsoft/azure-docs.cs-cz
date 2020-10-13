---
title: Vytvořte plán pro učebn Labs v Azure Lab Services | Microsoft Docs
description: Naučte se vytvářet plány pro učebn Labs v Azure Lab Services tak, aby se virtuální počítače v laboratořích spouštěly v určitou dobu.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 4eb4e2459069b61990f51f8d8de76522b736723e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91326435"
---
# <a name="create-and-manage-schedules-for-classroom-labs-in-azure-lab-services"></a>Vytváření a Správa plánů pro učebn Labs v Azure Lab Services 
Plány umožňují nakonfigurovat prostředí učebny tak, aby se virtuální počítače v testovacím prostředí automaticky spouštěly a vypnuly v určitou dobu. Můžete definovat Jednorázový plán nebo plán opakování. Následující postupy vám poskytnou kroky pro vytváření a správu plánů pro prostředí učebny: 

> [!IMPORTANT]
> Plánovaná doba spuštění virtuálních počítačů se nepočítá s [kvótou přidělenou uživateli](how-to-configure-student-usage.md#set-quotas-for-users). Kvóta je určena pro dobu mimo plánované hodiny, kterou student stráví na virtuálních počítačích. 

## <a name="set-a-schedule-for-the-lab"></a>Nastavte plán pro testovací prostředí.
Vytvořte naplánovanou událost pro testovací prostředí, aby se virtuální počítače v testovacím prostředí automaticky spustily nebo zastavily v určitých časech. Kvóta uživatele, kterou jste zadali dříve, je další čas přiřazený každému uživateli mimo tento naplánovaný čas. 

> [!NOTE]
> Než začneme, tady je postup, jak plány ovlivňují virtuální počítače testovacího prostředí: 
>- Virtuální počítač šablony není zahrnutý v plánech. 
>- Spuštěny jsou pouze přiřazené virtuální počítače. To znamená, že pokud počítač není vyžádán koncovým uživatelem (student), počítač se v naplánovaných hodinách nespustí. 
>- Všechny virtuální počítače (bez ohledu na to, jestli je uživatel vyžádal), se zastaví v závislosti na plánu testovacího prostředí. 

1. Přepněte na stránku **plány** a na panelu nástrojů vyberte **Přidat naplánovanou událost** . 

    ![Snímek obrazovky zobrazující stránku Azure Lab Services "plán" a vybraným tlačítkem Přidat plán.](./media/how-to-create-schedules/add-schedule-button.png)
2. Potvrďte, že **Standard** je vybraný jako **Typ události**. Vyberte **pouze spustit** , chcete-li zadat pouze počáteční čas pro virtuální počítače. Vyberte možnost **zastavit pouze** a zadejte pouze dobu zastavení virtuálních počítačů. 
7. V části **Opakovat** vyberte aktuální plán. 

    ![Tlačítko Přidat plán na stránce plány](./media/how-to-create-schedules/select-current-schedule.png)
5. V dialogovém okně **Opakovat** proveďte následující kroky:
    1. Potvrďte, že je pro pole **Opakovat** nastaven **každý týden** . 
    3. Zadejte **počáteční datum**.
    4. Zadejte **čas spuštění** , ve kterém chcete virtuální počítače spustit.
    5. Zadejte **čas ukončení** , ve kterém mají být virtuální počítače vypnuté. 
    6. Zadejte **časové pásmo** pro dobu zahájení a ukončení, kterou jste zadali. 
    2. Vyberte dny, ve kterých se má plán projevit. V následujícím příkladu je vybrána možnost Monday-Thursday. 
    8. Vyberte **Uložit**. 

        ![Nastavit plán opakování](./media/how-to-create-schedules/set-repeat-schedule.png)

3. Nyní na stránce **Přidat naplánovanou událost** pro **poznámky (volitelné)** zadejte libovolný popis nebo poznámky k plánu. 
4. Na stránce **Přidat naplánovanou událost** vyberte **Uložit**. 

    ![Týdenní plán](./media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="view-schedules-in-calendar"></a>Zobrazit plány v kalendáři
Naplánovaná data a časy zvýrazněné v zobrazení Kalendář můžete zobrazit, jak je znázorněno na následujícím obrázku:

![Plány v zobrazení Kalendář](./media/how-to-create-schedules/schedules-calendar.png)

V pravém horním rohu vyberte tlačítko **dnes** a přepněte tak na aktuální datum v kalendáři. Vyberte **šipku doleva** pro přepnutí na předchozí týden a **šipku vpravo** , abyste přešli na další týden v kalendáři. 

## <a name="edit-a-schedule"></a>Upravit plán
Když v kalendáři vyberete zvýrazněný plán, zobrazí se tlačítka pro **úpravu** nebo **odstranění** plánu. 

![Stránka upravit plán](./media/how-to-create-schedules/schedule-edit-button.png)

Na stránce **Upravit naplánovanou událost** můžete plán aktualizovat a vybrat **Uložit**. 

## <a name="delete-a-schedule"></a>Odstranění plánu

1. Chcete-li odstranit plán, vyberte zvýrazněný plán v kalendáři a vyberte ikonu odpadkového koše (odstranit):

    ![Tlačítko Odstranit na panelu nástrojů](./media/how-to-create-schedules/schedule-delete-button.png)
2. Kliknutím na **tlačítko Ano** v dialogovém okně **odstranit naplánovanou událost** potvrďte odstranění. 



## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Účty testovacího prostředí se vytvářejí a spravují jako správce.](how-to-manage-lab-accounts.md)
- [Jako vlastník testovacího prostředí vytvářet a spravovat cvičení](how-to-manage-classroom-labs.md)
- [Jako vlastník testovacího prostředí konfigurace a řízení využití testovacího prostředí](how-to-configure-student-usage.md)
- [Jako uživatel testovacího prostředí, Access učeben Labs](how-to-use-classroom-lab.md)
