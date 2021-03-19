---
title: Správa redundantního vysoké dostupnosti zóny – Azure Portal-Azure Database for PostgreSQL-flexibilní Server
description: Tento článek popisuje, jak povolit nebo zakázat redundantní vysokou dostupnost zóny v Azure Database for PostgreSQL flexibilním serveru prostřednictvím Azure Portal.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: fc1bca1265139a438fad86bfce770026866d9a2f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "90934907"
---
# <a name="manage-zone-redundant-high-availability-in-flexible-server"></a>Správa redundantní vysoké dostupnosti zóny v flexibilním serveru

> [!IMPORTANT]
> Azure Database for PostgreSQL – flexibilní Server je ve verzi Preview.

Tento článek popisuje, jak na flexibilním serveru můžete povolit nebo zakázat konfiguraci redundantního vysoké dostupnosti zóny.

Funkce vysoké dostupnosti zřídí fyzicky samostatnou primární a pohotovostní repliku v různých zónách. Další podrobnosti najdete v [dokumentaci k konceptům vysoké dostupnosti](./concepts-high-availability.md). V době flexibilního vytvoření serveru nebo po vytvoření se můžete rozhodnout povolit vysokou dostupnost. Tato stránka poskytuje pokyny, jak můžete povolit nebo zakázat vysokou dostupnost. Tato operace nemění vaše další nastavení, včetně konfigurace virtuální sítě, nastavení brány firewall a uchovávání záloh. Podobně je aktivace a zákaz vysoké dostupnosti online operace a nemá vliv na připojení a operace vaší aplikace.

## <a name="pre-requisites"></a>Požadavky

Redundantní vysoká dostupnost zóny je k dispozici pouze v oblastech, kde je podporováno více zón. 

## <a name="enable-high-availability-during-server-creation"></a>Povolit vysokou dostupnost během vytváření serveru

V této části najdete podrobnosti konkrétně pro pole související s HA. Pomocí těchto kroků můžete nasadit vysokou dostupnost při vytváření flexibilního serveru.

1.  V [Azure Portal](https://portal.azure.com/)vyberte flexibilní Server a klikněte na vytvořit.  Podrobnosti o tom, jak vyplnit podrobnosti, jako je například **předplatné**, **Skupina prostředků**, **název serveru**, **oblast** a další pole, najdete v tématu dokumentace k vytvoření serveru.
   
    :::image type="content" source="./media/how-to-manage-high-availability-portal/subscription-region.png" alt-text="Zobrazení předplatného a oblasti":::

2.  Vyberte si **zónu dostupnosti**. To je užitečné, pokud chcete, aby se aplikace společné umístění ve stejné zóně dostupnosti jako databáze, aby se snížila latence. Pokud chcete, aby byl flexibilní Server nasazený v jakékoli zóně dostupnosti, vyberte možnost **bez preference** .
    ![AZ výběr ]() :::image type="content" source="./media/how-to-manage-high-availability-portal/zone-selection.png" alt-text="zóny dostupnosti"::: výběr  

3.  V možnosti dostupnosti klikněte na zaškrtávací políčko pro **redundantní vysokou dostupnost zóny** .

    :::image type="content" source="./media/how-to-manage-high-availability-portal/high-availability-checkbox.png" alt-text="Zaškrtávací políčko pro vysokou dostupnost":::

4.  Pokud chcete změnit výchozí výpočetní výkon a úložiště, klikněte na  **Konfigurovat Server**.
 
    :::image type="content" source="./media/how-to-manage-high-availability-portal/configure-server.png" alt-text="Konfigurace serveru – COMPUTE + Storage":::  

5.  Pokud je zaškrtnuté políčko vysoká dostupnost, nebudete moct zvolit vrstvu s možností vysoké dostupnosti. Můžete si vybrat buď výpočetní úrovně pro **obecné účely** , nebo **paměťově optimalizované** . Pak můžete z rozevíracího seznamu vybrat **Velikost výpočetní velikosti** pro vaši volbu.

    :::image type="content" source="./media/how-to-manage-high-availability-portal/select-compute.png" alt-text="Výběr na výpočetní úrovni":::  


6.  V GiB vyberte **velikost úložiště** pomocí posuvných pruhů a vyberte **dobu uchovávání záloh** mezi 7 dny a 35 dny.
   
    :::image type="content" source="./media/how-to-manage-high-availability-portal/storage-backup.png" alt-text="Zálohování úložiště"::: 

7. Klikněte na **Uložit**. 

## <a name="enable-high-availability-post-server-creation"></a>Povolit vytvoření post serveru s vysokou dostupností

Pomocí těchto kroků povolíte vysokou dostupnost stávajícího flexibilního serveru.

1.  V [Azure Portal](https://portal.azure.com/)vyberte svůj existující server PostgreSQL flexibilní.

2.  Na stránce flexibilní Server klikněte na tlačítko **Vysoká dostupnost** na levém panelu a otevřete stránku vysoké dostupnosti.
   
     :::image type="content" source="./media/how-to-manage-high-availability-portal/high-availability-left-panel.png" alt-text="Výběr na levém panelu"::: 

3.  Kliknutím na zaškrtávací políčko **zóna redundantního vysoké dostupnosti** **Povolte** možnost a kliknutím na **Uložit** změny uložte.

     :::image type="content" source="./media/how-to-manage-high-availability-portal/enable-high-availability.png" alt-text="Povolit vysokou dostupnost"::: 

4.  V potvrzovacím dialogovém okně se zobrazí stav, který umožňuje vysokou dostupnost. vaše náklady se zvýší kvůli dalšímu nasazení serveru a úložiště.

5.  Pokud chcete povolit vysokou dostupnost, klikněte na tlačítko **Povolit ha** .

6.  Zobrazí se oznámení o tom, že probíhá nasazení vysoké dostupnosti.

## <a name="disable-high-availability"></a>Zakázat vysokou dostupnost

Pomocí těchto kroků zakážete vysokou dostupnost pro flexibilní Server, který je už nakonfigurovaný s možností redundance zóny.

1.  V [Azure Portal](https://portal.azure.com/)vyberte svůj existující Azure Database for PostgreSQL-flexibilní Server.

2.  Na stránce flexibilní Server klikněte na horním panelu na možnost **Vysoká dostupnost** a otevřete stránku vysoké dostupnosti.
   
    :::image type="content" source="./media/how-to-manage-high-availability-portal/high-availability-left-panel.png" alt-text="Výběr na levém panelu"::: 

3.  Kliknutím na zaškrtávací políčko **zóna redundantního vysoké dostupnosti** **zakažte** možnost. Pak kliknutím na **Uložit** změnu uložte.

     :::image type="content" source="./media/how-to-manage-high-availability-portal/disable-high-availability.png" alt-text="Zakázat vysokou dostupnost"::: 

4.  Zobrazí se potvrzovací dialogové okno, ve kterém můžete potvrdit zakázání vysoké dostupnosti.

5.  Kliknutím na tlačítko **Zakázat ha** zakážete vysokou dostupnost.

6.  V oznámení se zobrazí vyřazení nasazení vysoké dostupnosti z provozu.

## <a name="next-steps"></a>Další kroky

-   Informace o [kontinuitě podnikových aplikací](./concepts-business-continuity.md)
-   Přečtěte si o [vysoké dostupnosti zóny jako redundantní](./concepts-high-availability.md) .
