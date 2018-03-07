---
title: "Konfigurace nastavení replikace pro Azure Site Recovery | Dokumentace Microsoftu"
description: "Tento dokument popisuje způsob nasazení Site Recovery za účelem orchestrace replikace, převzetí služeb při selhání a obnovení virtuálních počítačů Hyper-V v cloudech VMM do Azure."
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: rayne-wiselman
ms.assetid: 8e7d868e-00f3-4e8b-9a9e-f23365abf6ac
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 02/22/2018
ms.author: sutalasi
ms.openlocfilehash: 0a567f31bf1991d4c2a95468d2abc31c51a878f3
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/24/2018
---
# <a name="manage-replication-policy-for-vmware-to-azure"></a>Správa zásad replikace pro VMware do Azure


## <a name="create-a-replication-policy"></a>Vytvoření zásady replikace

1. Vyberte **Spravovat** > **Infrastruktura Site Recovery**.
2. V části **Pro VMware a fyzické počítače** vyberte **Zásady replikace**.
3. Vyberte **+Zásada replikace**.

    ![Vytvoření zásady replikace](./media/site-recovery-setup-replication-settings-vmware/createpolicy.png)

4. Zadejte název zásady.

5. V části **Prahová hodnota cíle bodu obnovení (RPO)** zadejte omezení RPO. Když toto omezení průběžná replikace překročí, vygenerují se upozornění.
6. V části **Uchování bodu obnovení** zadejte (v hodinách) délku intervalu uchovávání dat pro jednotlivé body obnovení. Chráněné počítače je možné obnovit do libovolného bodu v rámci tohoto intervalu.

    > [!NOTE]
    > Pro počítače replikované do úložiště úrovně Premium se podporuje interval uchování až 24 hodin. Pro počítače replikované do úložiště úrovně Standard se podporuje interval uchování až 72 hodin.

    > [!NOTE]
    > Zásada replikace pro navrácení služeb po obnovení se vytvoří automaticky.

7. V nastavení **Frekvence snímků konzistentní vzhledem k aplikacím** určete, jak často (v minutách) se mají vytvářet body obnovení obsahující snímky konzistentní vzhledem k aplikacím.

8. Klikněte na **OK**. Zásada by se měla vytvořit během přibližně 30–60 sekund.

![Generování zásad replikace](./media/site-recovery-setup-replication-settings-vmware/Creating-Policy.png)

## <a name="associate-a-configuration-server-with-a-replication-policy"></a>Přidružení konfiguračního serveru k zásadě replikace
1. Vyberte zásadu replikace, ke které chcete přidružit konfigurační server.
2. Klikněte na **Přidružit**.
![Přidružení konfiguračního serveru](./media/site-recovery-setup-replication-settings-vmware/Associate-CS-1.PNG)

3. Ze seznamu serverů vyberte konfigurační server.
4. Klikněte na **OK**. Přidružení konfiguračního serveru by se mělo provést během přibližně 1 až 2 minut.

![Přidružení konfiguračního serveru](./media/site-recovery-setup-replication-settings-vmware/Associate-CS-2.png)

## <a name="edit-a-replication-policy"></a>Úprava zásady replikace
1. Vyberte zásadu replikace, pro kterou chcete nastavení replikace změnit.
![Úprava zásady replikace](./media/site-recovery-setup-replication-settings-vmware/Select-Policy.png)

2. Klikněte na **Upravit nastavení**.
![Úprava nastavení zásady replikace](./media/site-recovery-setup-replication-settings-vmware/Edit-Policy.png)

3. Změňte nastavení podle vašich potřeb.
4. Klikněte na **Uložit**. Zásada by se měla uložit během přibližně 2 až 5 minut v závislosti na tom, kolik virtuálních počítačů tuto zásadu replikace používá.

![Uložení zásady replikace](./media/site-recovery-setup-replication-settings-vmware/Save-Policy.png)

## <a name="dissociate-a-configuration-server-from-a-replication-policy"></a>Zrušení přidružení konfiguračního serveru k zásadě replikace
1. Vyberte zásadu replikace, ke které chcete přidružit konfigurační server.
2. Klikněte na **Zrušit přidružení**.
3. Ze seznamu serverů vyberte konfigurační server.
4. Klikněte na **OK**. Přidružení konfiguračního serveru by se mělo zrušit během přibližně 1 až 2 minut.

    > [!NOTE]
    > Přidružení konfiguračního serveru nejde zrušit, pokud existuje alespoň jedna replikovaná položka, která zásadu používá. Před zrušením přidružení konfiguračního serveru zkontrolujte, že žádné replikované položky zásadu nepoužívají.

## <a name="delete-a-replication-policy"></a>Odstranění zásady replikace

1. Vyberte zásadu replikace, kterou chcete odstranit.
2. Klikněte na **Odstranit**. Zásada by se měla odstranit během přibližně 30–60 sekund.

    > [!NOTE]
    > Zásadu replikace nejde odstranit, pokud má přidružený aspoň jeden konfigurační server. Zkontrolujte, že neexistují žádné replikované položky, které zásadu používají, a před odstraněním zásady odstraňte všechny přidružené konfigurační servery.
