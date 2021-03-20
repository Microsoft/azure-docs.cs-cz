---
title: Nastavení zásad replikace pro zotavení po havárii VMware pomocí Azure Site Recovery | Microsoft Docs
description: Popisuje postup konfigurace nastavení replikace pro zotavení po havárii VMware do Azure pomocí Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: sutalasi
ms.openlocfilehash: 45921bdf802a649b7b802f44d2842a543e44f02b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "84699596"
---
# <a name="configure-and-manage-replication-policies-for-vmware-disaster-recovery"></a>Konfigurace a správa zásad replikace pro zotavení po havárii VMware

Tento článek popisuje, jak nakonfigurovat zásady replikace při replikaci virtuálních počítačů VMware do Azure pomocí [Azure Site Recovery](site-recovery-overview.md).

## <a name="create-a-policy"></a>Vytvoření zásad

1. Vyberte možnost **Spravovat**  >  **infrastrukturu Site Recovery**.
2. V nástroji **pro VMware a fyzické počítače** vyberte **Zásady replikace**.
3. Klikněte na **+ zásada replikace** a zadejte název zásady.
4. V části **prahová hodnota** cíle bodu obnovení zadejte limit RPO. Výstrahy jsou generovány, pokud průběžná replikace překročí tento limit.
5. V části **Uchování bodu obnovení** zadejte (v hodinách) délku intervalu uchovávání dat pro jednotlivé body obnovení. Chráněné počítače je možné obnovit do libovolného bodu v rámci tohoto intervalu. Pro počítače replikované do úložiště úrovně Premium se podporuje interval uchování až 24 hodin. Pro standardní úložiště se podporuje až 72 hodin.
6. V nastavení **frekvence snímků konzistentní vzhledem k aplikacím** vyberte z rozevíracího seznamu, jak často (v hodinách) se mají vytvářet body obnovení obsahující snímky konzistentní vzhledem k aplikacím. Pokud chcete vypnout generování bodů konzistence aplikací, v rozevíracím seznamu vyberte hodnotu OFF.
7. Klikněte na **OK**. Zásada by se měla vytvořit během přibližně 30–60 sekund.

Když vytvoříte zásadu replikace, automaticky se vytvoří odpovídající zásada replikace pro navrácení služeb po obnovení s příponou "navrácení služeb po obnovení". Jakmile zásadu vytvoříte, můžete ji upravit tak, že ji vyberete > **Upravit nastavení**.

## <a name="associate-a-configuration-server"></a>Přidružení konfiguračního serveru

Přidružte zásady replikace k vašemu místnímu konfiguračnímu serveru.

1. Klikněte na tlačítko **přidružit** a vyberte konfigurační server.

    ![Přidružení konfiguračního serveru](./media/vmware-azure-set-up-replication/associate1.png)
2. Klikněte na **OK**. Přidružení konfiguračního serveru by se mělo provést během přibližně 1 až 2 minut.

    ![Přidružení konfiguračního serveru](./media/vmware-azure-set-up-replication/associate2.png)

## <a name="edit-a-policy"></a>Upravit zásadu

Po vytvoření můžete zásady replikace upravit.

- Změny v zásadách se aplikují na všechny počítače, které používají tuto zásadu.
- Chcete-li přidružit replikované počítače k jiné zásadě replikace, je třeba zakázat a znovu povolit ochranu pro příslušné počítače.

Zásadu upravte takto:
1. Vyberte **Spravovat**  >    >  **Zásady replikace** Site Recovery infrastruktury.
2. Vyberte zásadu replikace, kterou chcete upravit.
3. Klikněte na **Upravit nastavení** a v případě potřeby aktualizujte pole četnosti snímků RPO/doba uchovávání bodů obnovení/konzistentní doba uchování pro aplikaci.
4. Pokud chcete vypnout generování bodů konzistence aplikací, vyberte v rozevíracím seznamu **frekvence snímků konzistentních vzhledem k aplikacím** pole hodnota vypnuto.
5. Klikněte na **Uložit**. Zásada by se měla aktualizovat v rozmezí 30 až 60 sekund.



## <a name="disassociate-or-delete-a-replication-policy"></a>Zrušení přidružení nebo odstranění zásady replikace

1. Vyberte zásadu replikace.
    a. Pokud chcete zásadu oddělit od konfiguračního serveru, zajistěte, aby zásady nepoužívaly žádné replikované počítače. Pak klikněte na zrušit **přidružení**.
    b. Pokud chcete zásadu odstranit, ujistěte se, že není přidružená ke konfiguračnímu serveru. Pak klikněte na **Odstranit**. Odstranění by mělo trvat 30-60 sekund.
2. Klikněte na **OK**.
