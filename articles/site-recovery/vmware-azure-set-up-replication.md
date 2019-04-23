---
title: Konfigurace a Správa zásad replikace pro zotavení po havárii VMware do Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Popisuje postup konfigurace nastavení replikace pro zotavení po havárii VMware do Azure pomocí Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: sutalasi
ms.openlocfilehash: b60d8a8fb9b9300a6914ad33b2f760fb5adde3b4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59792449"
---
# <a name="configure-and-manage-replication-policies-for-vmware-disaster-recovery-to-azure"></a>Konfigurace a Správa zásad replikace pro zotavení po havárii VMware do Azure
Tento článek popisuje, jak nakonfigurovat zásadu replikace, když budete replikovat virtuální počítače VMware do Azure, pomocí [Azure Site Recovery](site-recovery-overview.md).

## <a name="create-a-policy"></a>Vytvořit zásadu

1. Vyberte **Spravovat** > **Infrastruktura Site Recovery**.
2. V **pro VMware a fyzických počítačů**vyberte **zásady replikace**.
3. Klikněte na tlačítko **+ zásada replikace**a zadejte název zásady.
4. V části **Prahová hodnota cíle bodu obnovení (RPO)** zadejte omezení RPO. Výstrahy jsou generovány, pokud průběžná replikace překročí tento limit.
5. V části **Uchování bodu obnovení** zadejte (v hodinách) délku intervalu uchovávání dat pro jednotlivé body obnovení. Chráněné počítače je možné obnovit do libovolného bodu v rámci tohoto intervalu. Pro počítače replikované do úložiště úrovně Premium se podporuje interval uchování až 24 hodin. Je pro úložiště úrovně standard podporuje až 72 hodin.
6. V **frekvence snímků konzistentní vzhledem k**, vyberte z rozevíracího seznamu, jak často (v hodinách) mají být vytvořeny body obnovení obsahující snímky konzistentní s aplikací. Pokud chcete vypnout generování body konzistence aplikací, zvolte "hodnotu v rozevíracím seznamu Off".
7. Klikněte na **OK**. Zásada by se měla vytvořit během přibližně 30–60 sekund.

Když vytvoříte zásady replikace, odpovídající zásady navrácení služeb po obnovení replikace se automaticky vytvoří, s příponou "navrácení služeb po obnovení". Po vytvoření zásady, můžete upravit ji tak, že ji vyberete > **upravit nastavení**.

## <a name="associate-a-configuration-server"></a>Přidružení konfiguračního serveru

Přidružte zásady replikace s vaší místní konfigurační server.

1. Klikněte na tlačítko **přidružit**a vyberte konfigurační server.

    ![Přidružení konfiguračního serveru](./media/vmware-azure-set-up-replication/associate1.png)
2. Klikněte na **OK**. Přidružení konfiguračního serveru by se mělo provést během přibližně 1 až 2 minut.

    ![Přidružení konfiguračního serveru](./media/vmware-azure-set-up-replication/associate2.png)

## <a name="edit-a-policy"></a>Upravit zásadu

1. Vyberte **spravovat** > **infrastruktura Site Recovery** > **zásady replikace**.
2. Vyberte zásadu replikace, kterou chcete upravit.
3. Klikněte na tlačítko **upravit nastavení**a aktualizovat pole cíle bodu obnovení prahovou hodnotu nebo obnovení bodu uchování hodin/aplikace – snímek konzistentní vzhledem k frekvenci podle potřeby.
4. Pokud chcete vypnout generování body konzistence aplikací, zvolte "hodnotu v rozevíracím seznamu pole Off" **frekvence snímků konzistentní vzhledem k**.
5. Klikněte na **Uložit**. Zásady se musí aktualizovat během přibližně 30 – 60 sekund.

## <a name="disassociate-or-delete-a-replication-policy"></a>Zrušit nebo odstranit zásady replikace

1. Vyberte zásadu replikace.
    a. Zrušení přidružení zásad z konfiguračního serveru, ujistěte se, že žádné replikované počítače budou používat zásady. Potom klikněte na **zrušte aktuální přidružení**.
    b. Chcete-li odstranit zásady, ujistěte se, zda není přidružený k konfiguračního serveru. Potom klikněte na **odstranit**. Mělo by to trvat 30 – 60 sekund se má odstranit.
2. Klikněte na **OK**.
