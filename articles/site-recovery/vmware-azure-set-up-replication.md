---
title: Nastavení zásad replikace pro zotavení po havárii společnosti VMware pomocí obnovení webu Azure| Dokumenty společnosti Microsoft
description: Popisuje, jak nakonfigurovat nastavení replikace pro zotavení po havárii v systému VMware do Azure pomocí Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: sutalasi
ms.openlocfilehash: 45921bdf802a649b7b802f44d2842a543e44f02b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257118"
---
# <a name="configure-and-manage-replication-policies-for-vmware-disaster-recovery"></a>Konfigurace a správa zásad replikace pro zotavení po havárii společnosti VMware

Tento článek popisuje, jak nakonfigurovat zásady replikace při replikaci virtuálních počítačů VMware do Azure pomocí [Azure Site Recovery](site-recovery-overview.md).

## <a name="create-a-policy"></a>Vytvoření zásad

1. Vyberte **spravovat** > **infrastrukturu pro obnovení lokality**.
2. V **oblasti Pro počítače VMware a Fyzické počítače**vyberte **zásady replikace**.
3. Klepněte na **tlačítko +Replikace**a zadejte název zásady.
4. V **prahové hodnotě RPO**zadejte limit RPO. Výstrahy jsou generovány, pokud nepřetržitá replikace překročí tento limit.
5. V části **Uchování bodu obnovení** zadejte (v hodinách) délku intervalu uchovávání dat pro jednotlivé body obnovení. Chráněné počítače je možné obnovit do libovolného bodu v rámci tohoto intervalu. Pro počítače replikované do úložiště úrovně Premium se podporuje interval uchování až 24 hodin. Pro standardní úložiště je podporováno až 72 hodin.
6. V **četnosti snímků konzistentní chod aplikace**vyberte z rozevírací nabídky, jak často (v hodinách) body obnovení, které obsahují snímky konzistentní s aplikací, by měly být vytvořeny. Pokud chcete vypnout generování bodů konzistence aplikace, zvolte hodnotu "Vypnuto" v rozevíracím seznamu.
7. Klikněte na tlačítko **OK**. Zásada by se měla vytvořit během přibližně 30–60 sekund.

Při vytváření zásad replikace se automaticky vytvoří odpovídající zásada replikace navrácení služeb po obnovení s příponou "navrácení služeb po obnovení". Po vytvoření zásady ji můžete upravit tak, že ji vyberete > **Upravit nastavení**.

## <a name="associate-a-configuration-server"></a>Přidružení konfiguračního serveru

Přidružte zásady replikace k místnímu konfiguračnímu serveru.

1. Klepněte na tlačítko **Přidružit**a vyberte konfigurační server.

    ![Přidružení konfiguračního serveru](./media/vmware-azure-set-up-replication/associate1.png)
2. Klikněte na tlačítko **OK**. Přidružení konfiguračního serveru by se mělo provést během přibližně 1 až 2 minut.

    ![Přidružení konfiguračního serveru](./media/vmware-azure-set-up-replication/associate2.png)

## <a name="edit-a-policy"></a>Úprava zásady

Zásady replikace můžete po vytvoření upravit.

- Změny v zásadách se použijí na všechny počítače používající zásady.
- Pokud chcete přidružit replikované počítače s různými zásadami replikace, je třeba zakázat a znovu povolit ochranu pro příslušné počítače.

Upravte zásadu takto:
1. Vyberte **možnost Spravovat** > **zásady replikace****infrastruktury** > obnovení lokality .
2. Vyberte zásadu replikace, kterou chcete upravit.
3. Podle potřeby klikněte na **Upravit nastavení**a aktualizujte hodiny uchovávání bodů RPO/bod obnovení/pole frekvence snímků konzistentních s aplikací.
4. Pokud chcete vypnout generování bodů konzistence aplikace, zvolte hodnotu "Vypnuto" v rozevíracím seznamu pole **Frekvence snímků konzistentních s aplikací**.
5. Klikněte na **Uložit**. Zásady by měly být aktualizovány za 30 až 60 sekund.



## <a name="disassociate-or-delete-a-replication-policy"></a>Zrušení přidružení nebo odstranění zásad replikace

1. Zvolte zásady replikace.
    a. Chcete-li oddělit zásady od konfiguračního serveru, ujistěte se, že žádné replikované počítače používají zásadu. Potom **klepněte**na tlačítko Oddělit .
    b. Chcete-li zásadu odstranit, ujistěte se, že není přidružena k konfiguračnímu serveru. Potom klepněte na tlačítko **Odstranit**. Odstranění by mělo trvat 30-60 sekund.
2. Klikněte na tlačítko **OK**.
