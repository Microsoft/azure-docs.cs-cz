---
title: Konfigurovat a spravovat zásady replikace pro VMware replikaci s Azure Site Recovery | Microsoft Docs
description: Popisuje postup konfigurace nastavení replikace pro VMware replikaci do Azure s Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: sutalasi
ms.openlocfilehash: 6a8e6e7c6fbdbcbf58557a0896e976a608164041
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
ms.locfileid: "29812667"
---
# <a name="configure-and-manage-replication-policies-for-vmware-replication"></a>Konfigurovat a spravovat zásady replikace pro VMware replikace
Tento článek popisuje, jak nakonfigurovat zásadu replikace, když se replikace virtuálních počítačů VMware do Azure, pomocí [Azure Site Recovery](site-recovery-overview.md).


## <a name="create-a-policy"></a>Vytvořit zásadu

1. Vyberte **Spravovat** > **Infrastruktura Site Recovery**.
2. V **pro VMware a fyzické počítače**, vyberte **zásady replikace**. 
3. Klikněte na tlačítko **+ zásady replikace**a zadejte název zásady.
5. V části **Prahová hodnota cíle bodu obnovení (RPO)** zadejte omezení RPO. Výstrahy jsou generovány, pokud tento limit překračuje průběžnou replikaci.
6. V části **Uchování bodu obnovení** zadejte (v hodinách) délku intervalu uchovávání dat pro jednotlivé body obnovení. Chráněné počítače je možné obnovit do libovolného bodu v rámci tohoto intervalu. Pro počítače replikované do úložiště úrovně Premium se podporuje interval uchování až 24 hodin. Až 72 hodin je podporován pro standardní úložiště.
7. V nastavení **Frekvence snímků konzistentní vzhledem k aplikacím** určete, jak často (v minutách) se mají vytvářet body obnovení obsahující snímky konzistentní vzhledem k aplikacím.
8. Klikněte na **OK**. Zásada by se měla vytvořit během přibližně 30–60 sekund.

Když vytvoříte zásadu replikace, odpovídající zásada replikace navrácení služeb po obnovení je automaticky vytvořen s příponou "navrácení služeb po obnovení". Po vytvoření zásady, ho můžete upravit tak, že ho vyberete > **upravit nastavení**.

## <a name="associate-a-configuration-server"></a>Přidružení konfigurace serveru 

Přidružení zásady replikace s vaší místní konfigurační server.

1. Klikněte na tlačítko **přidružit**a vyberte konfigurační server.

    ![Přidružení konfigurace serveru](./media/vmware-azure-set-up-replication/associate1.png)

2. Klikněte na **OK**. Přidružení konfiguračního serveru by se mělo provést během přibližně 1 až 2 minut.

    ![Přidružení konfiguračního serveru](./media/vmware-azure-set-up-replication/associate2.png)


## <a name="disassociate-or-delete-a-replication-policy"></a>Zrušte přidružení nebo odstranění zásady replikace
1. Zvolte požadovanou zásadu replikace.
    a. Chcete-li zrušit přidružení zásad z konfiguračního serveru, ujistěte se, že žádné replikované počítače jsou pomocí zásad. Potom klikněte na **zrušte aktuální přidružení**.
    b. Chcete odstranit zásady, ujistěte se, zda není přidružený konfigurační server. Potom klikněte na **odstranit**. Má trvat 30 – 60 sekund odstranit.
2. Klikněte na **OK**.
