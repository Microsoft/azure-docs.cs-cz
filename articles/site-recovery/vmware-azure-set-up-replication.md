---
title: Konfigurace a Správa zásad replikace pro replikaci VMware pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Popisuje postup konfigurace nastavení replikace pro replikaci VMware do Azure pomocí Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: sutalasi
ms.openlocfilehash: 115f4e76630a0ecd659fdd3aec0c6aa67d503817
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2018
ms.locfileid: "37920483"
---
# <a name="configure-and-manage-replication-policies-for-vmware-replication"></a>Konfigurace a Správa zásad replikace pro replikaci VMware
Tento článek popisuje, jak nakonfigurovat zásadu replikace, když budete replikovat virtuální počítače VMware do Azure, pomocí [Azure Site Recovery](site-recovery-overview.md).


## <a name="create-a-policy"></a>Vytvořit zásadu

1. Vyberte **Spravovat** > **Infrastruktura Site Recovery**.
2. V **pro VMware a fyzických počítačů**vyberte **zásady replikace**. 
3. Klikněte na tlačítko **+ zásada replikace**a zadejte název zásady.
5. V části **Prahová hodnota cíle bodu obnovení (RPO)** zadejte omezení RPO. Výstrahy jsou generovány, pokud průběžná replikace překročí tento limit.
6. V části **Uchování bodu obnovení** zadejte (v hodinách) délku intervalu uchovávání dat pro jednotlivé body obnovení. Chráněné počítače je možné obnovit do libovolného bodu v rámci tohoto intervalu. Pro počítače replikované do úložiště úrovně Premium se podporuje interval uchování až 24 hodin. Je pro úložiště úrovně standard podporuje až 72 hodin.
7. V nastavení **Frekvence snímků konzistentní vzhledem k aplikacím** určete, jak často (v minutách) se mají vytvářet body obnovení obsahující snímky konzistentní vzhledem k aplikacím.
8. Klikněte na **OK**. Zásada by se měla vytvořit během přibližně 30–60 sekund.

Když vytvoříte zásady replikace, odpovídající zásady navrácení služeb po obnovení replikace se automaticky vytvoří, s příponou "navrácení služeb po obnovení". Po vytvoření zásady, můžete upravit ji tak, že ji vyberete > **upravit nastavení**.

## <a name="associate-a-configuration-server"></a>Přidružení konfiguračního serveru 

Přidružte zásady replikace s vaší místní konfigurační server.

1. Klikněte na tlačítko **přidružit**a vyberte konfigurační server.

    ![Přidružení konfiguračního serveru](./media/vmware-azure-set-up-replication/associate1.png)

2. Klikněte na **OK**. Přidružení konfiguračního serveru by se mělo provést během přibližně 1 až 2 minut.

    ![Přidružení konfiguračního serveru](./media/vmware-azure-set-up-replication/associate2.png)


## <a name="disassociate-or-delete-a-replication-policy"></a>Zrušit nebo odstranit zásady replikace
1. Vyberte zásadu replikace.
    a. Zrušení přidružení zásad z konfiguračního serveru, ujistěte se, že žádné replikované počítače budou používat zásady. Potom klikněte na **zrušte aktuální přidružení**.
    b. Chcete-li odstranit zásady, ujistěte se, zda není přidružený k konfiguračního serveru. Potom klikněte na **odstranit**. Mělo by to trvat 30 – 60 sekund se má odstranit.
2. Klikněte na **OK**.
