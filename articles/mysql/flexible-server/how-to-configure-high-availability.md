---
title: Správa redundantního vysoké dostupnosti zóny – Azure Portal-Azure Database for MySQL flexibilní Server
description: Tento článek popisuje, jak povolit nebo zakázat redundantní vysokou dostupnost zóny v Azure Database for MySQL flexibilním serveru prostřednictvím Azure Portal.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.custom: references_regions
ms.openlocfilehash: e217dcaeafd553803f5c9699ab6d7779ed755b67
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818276"
---
# <a name="manage-zone-redundant-high-availability-in-azure-database-for-mysql-flexible-server-preview"></a>Správa redundantní vysoké dostupnosti zóny v Azure Database for MySQL flexibilním serveru (Preview)

Tento článek popisuje, jak na flexibilním serveru můžete povolit nebo zakázat konfiguraci redundantního vysoké dostupnosti zóny.

Funkce vysoké dostupnosti zřídí fyzicky samostatnou primární a pohotovostní repliku v různých zónách. Další podrobnosti najdete v [dokumentaci k konceptům vysoké dostupnosti](./concepts/../concepts-high-availability.md). 

> [!IMPORTANT]
> Při vytváření flexibilního serveru můžete povolit jenom redundantní vysokou dostupnost zóny.

Tato stránka poskytuje pokyny, jak můžete povolit nebo zakázat vysokou dostupnost. Tato operace nemění vaše další nastavení, včetně konfigurace virtuální sítě, nastavení brány firewall a uchovávání záloh. Podobně vypnutí vysoké dostupnosti je online operace, která nemá vliv na připojení a operace vaší aplikace.

> [!IMPORTANT]
> Redundantní vysoká dostupnost zóny je k dispozici v omezené sadě oblastí: jihovýchodní Asie, WestUS 2, Západní Evropa a Východní USA.  

## <a name="enable-high-availability-during-server-creation"></a>Povolit vysokou dostupnost během vytváření serveru

V této části najdete podrobnosti konkrétně pro pole související s HA. Pomocí těchto kroků můžete nasadit vysokou dostupnost při vytváření flexibilního serveru.

1.  V [Azure Portal](https://portal.azure.com/)vyberte flexibilní Server a klikněte na **vytvořit**.  Podrobnosti o tom, jak vyplnit podrobnosti, jako je například **předplatné**, **Skupina prostředků**, **název serveru**, **oblast** a další pole, najdete v tématu dokumentace k vytvoření serveru.

2.  V možnosti dostupnosti klikněte na zaškrtávací políčko pro **redundantní vysokou dostupnost zóny** .

3.  Pokud chcete změnit výchozí výpočetní výkon a úložiště, klikněte na  **Konfigurovat Server**.

4.  Pokud je zaškrtnuté políčko vysoká dostupnost, nebudete moct zvolit vrstvu s možností vysoké dostupnosti. Můžete si vybrat buď výpočetní úrovně pro **obecné účely** , nebo **paměťově optimalizované** .

    > [!IMPORTANT]
    > Podporujeme jenom redundantní vysokou dostupnost zóny pro cenovou úroveň ***obecné účely** _ a _ *_paměť optimalizované pro paměť_*.

5.  Z rozevíracího seznamu vyberte požadovanou **Velikost** pro výběr.

6.  V GiB vyberte **velikost úložiště** pomocí posuvných pruhů a vyberte **dobu uchovávání záloh** mezi 7 dny a 35 dny.   

## <a name="disable-high-availability"></a>Zakázat vysokou dostupnost

Pomocí těchto kroků zakážete vysokou dostupnost pro flexibilní Server, který je už nakonfigurovaný s možností redundance zóny.

1.  V [Azure Portal](https://portal.azure.com/)vyberte svůj existující Azure Database for MySQL flexibilní Server.

2.  Na stránce flexibilní Server klikněte na horním panelu na možnost **Vysoká dostupnost** a otevřete stránku vysoké dostupnosti.

3.  Kliknutím na zaškrtávací políčko **zóna redundantního vysoké dostupnosti** zakažte možnost a kliknutím na **Uložit** změny uložte.

4.  Zobrazí se potvrzovací dialogové okno, ve kterém můžete potvrdit zakázání HA.

5.  Kliknutím na tlačítko **Zakázat ha** zakážete vysokou dostupnost.

6.  V oznámení se zobrazí vyřazení nasazení vysoké dostupnosti z provozu.


## <a name="forced-failover"></a>Vynucené převzetí služeb při selhání

Pomocí těchto kroků vynutíte převzetí služeb při selhání z primárního na pohotovostní server flexibilního serveru.

1.  V [Azure Portal](https://portal.azure.com/)vyberte svůj existující Azure Database for MySQL flexibilní Server, který má povolenou funkci vysoké dostupnosti.

2.  Na stránce flexibilní Server klikněte na horním panelu na možnost **Vysoká dostupnost** a otevřete stránku vysoké dostupnosti.

3.  Ověřte **primární zónu dostupnosti** a **zónu dostupnosti v pohotovostním stavu** .

4.  Kliknutím na **Vynucené převzetí služeb při** selhání spusťte ruční postup převzetí služeb při selhání. Automaticky otevírané okno vás bude informovat o očekávané době převzetí služeb při selhání v závislosti na aktuálním zatížení primárního a recency posledního kontrolního bodu, přečtěte si zprávu a klikněte na OK.
 
5. V oznámení se zobrazí zmínka o tom, že probíhá převzetí služeb při selhání.

6. Po úspěšném převzetí služeb při selhání do pohotovostního serveru se zobrazí oznámení.

7. Ověřte novou **primární zónu dostupnosti** a **zónu dostupnosti v pohotovostním režimu**.

![Postup vynuceného převzetí služeb při selhání](media/how-to-configure-high-availability/how-to-forced-failover.png) 

## <a name="next-steps"></a>Další kroky

-   Informace o [kontinuitě podnikových aplikací](./concepts-business-continuity.md)
-   Přečtěte si o [vysoké dostupnosti zóny jako redundantní](./concepts-high-availability.md) .
