---
title: Vytvoření plánu pro testovací prostředí pro učebny ve službě Azure Lab Services | Dokumenty společnosti Microsoft
description: Zjistěte, jak vytvořit plány pro testovací prostředí pro učebny ve službě Azure Lab Services tak, aby virtuální počítače v testovacím prostředí spustit a vypnout v určený čas.
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
ms.date: 10/12/2019
ms.author: spelluru
ms.openlocfilehash: 4e3cf302437c3e4954ac977ac3f4ff6b2021a760
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72330536"
---
# <a name="create-and-manage-schedules-for-classroom-labs-in-azure-lab-services"></a>Vytváření a správa plánů pro laboratoře pro učebny ve službě Azure Lab Services 
Plány umožňují nakonfigurovat testovací prostředí učebny tak, aby se virtuální počítače v testovacím prostředí automaticky spouštěly a vypínaly v určený čas. Můžete definovat jednorázový nebo opakovaný plán. Následující postupy poskytují kroky k vytvoření a správě plánů pro testovací prostředí ve třídě: 

> [!IMPORTANT]
> Naplánovaná doba spuštění virtuálních disponecí se nezapočítává [do kvóty přidělené uživateli](how-to-configure-student-usage.md#set-quotas-for-users). Kvóta je pro čas mimo plánované hodiny, které student stráví na virtuálních počítačích. 

## <a name="set-a-schedule-for-the-lab"></a>Nastavení plánu pro testovací prostředí
Vytvořte naplánovanou událost pro testovací prostředí tak, aby virtuální chody v testovacím prostředí byly automaticky spuštěny nebo zastaveny v určitých časech. Kvóta uživatele, kterou jste zadali dříve, je dodatečný čas přiřazený každému uživateli mimo tento naplánovaný čas. 

1. Přepněte na stránku **Plány** a na panelu nástrojů vyberte **Přidat naplánovanou událost.** 

    ![Tlačítko Přidat plán na stránce Plány](../media/how-to-create-schedules/add-schedule-button.png)
2. Zkontrolujte, zda je vybrán typ **události** **Standard** . Vyberete **spustit pouze** určit pouze čas zahájení pro virtuální chod. Chcete-li zadat pouze čas zastavení virtuálních jevů, vyberte **možnost Zastavit** pouze pro umístění času zastavení. 
7. V části **Opakovat** vyberte aktuální plán. 

    ![Tlačítko Přidat plán na stránce Plány](../media/how-to-create-schedules/select-current-schedule.png)
5. V dialogovém okně **Opakovat** proveďte následující kroky:
    1. Zkontrolujte, zda je pro pole **Opakovat** nastaven **každý týden.** 
    3. Zadejte **počáteční datum**.
    4. Určete **čas zahájení,** ve kterém chcete virtuální chod spustit.
    5. Určete **čas zastavení,** kdy mají být virtuální počítači vypnuty. 
    6. Zadejte **časové pásmo** pro zadaný čas zahájení a zastavení. 
    2. Vyberte dny, kdy se má plán projevit. V následujícím příkladu je vybrána možnost Pondělí až čtvrtek. 
    8. Vyberte **Uložit**. 

        ![Nastavit plán opakování](../media/how-to-create-schedules/set-repeat-schedule.png)

3. Nyní na stránce **Přidat naplánovanou událost** pro **poznámky (volitelné)** zadejte libovolný popis nebo poznámky pro plán. 
4. Na stránce **Přidat naplánovanou událost** vyberte **Uložit**. 

    ![Týdenní rozvrh](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="view-schedules-in-calendar"></a>Zobrazení plánů v kalendáři
Naplánovaná data a časy zvýrazněné v zobrazení kalendáře můžete zobrazit tak, jak je znázorněno na následujícím obrázku:

![Plány v zobrazení kalendáře](../media/how-to-create-schedules/schedules-calendar.png)

Chcete-li přepnout na aktuální datum v kalendáři, vyberte tlačítko **Dnes** v pravém horním rohu. Výběrem **levé šipky** přepnete na předchozí týden a **šipku doprava** přepnete na další týden v kalendáři. 

## <a name="edit-a-schedule"></a>Úprava plánu
Když v kalendáři vyberete zvýrazněný plán, zobrazí se tlačítka **pro úpravu** nebo **odstranění** plánu. 

![Upravit stránku plánu](../media/how-to-create-schedules/schedule-edit-button.png)

Na stránce **Upravit naplánovanou událost** můžete plán aktualizovat a **vybrat**uložit . 

## <a name="delete-a-schedule"></a>Odstranění plánu

1. Chcete-li plán odstranit, vyberte zvýrazněný plán v kalendáři a vyberte tlačítko ikonu koše (odstranit):

    ![Tlačítko Odstranit na panelu nástrojů](../media/how-to-create-schedules/schedule-delete-button.png)
2. V dialogovém okně **Odstranit naplánovanou událost** vyberte **Ano,** chcete-li odstranění potvrdit. 



## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Jako správce vytvářejte a spravujte účty testovacího prostředí](how-to-manage-lab-accounts.md)
- [Jako vlastník testovacího prostředí vytvářejte a spravujte testovací prostředí](how-to-manage-classroom-labs.md)
- [Jako vlastník testovacího prostředí konfigurace a řízení využití testovacího prostředí](how-to-configure-student-usage.md)
- [Jako uživatel laboratoře, přístup k učebně labs](how-to-use-classroom-lab.md)
