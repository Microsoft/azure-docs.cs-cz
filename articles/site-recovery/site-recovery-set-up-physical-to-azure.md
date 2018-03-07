---
title: "Nastavení prostředí pro zdroj (fyzických serverů do Azure) | Microsoft Docs"
description: "Tento článek popisuje, jak nastavit v místním prostředí k zahájení replikace fyzických serverů s Windows nebo Linuxem do Azure."
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/22/2018
ms.author: anoopkv
ms.openlocfilehash: 6c29ac32cb868cfdd72479add551cece266a5f9d
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/24/2018
---
# <a name="set-up-the-source-environment-physical-server-to-azure"></a>Nastavení prostředí pro zdroj (fyzického serveru do Azure)
> [!div class="op_single_selector"]
> * [Z VMware do Azure](./site-recovery-set-up-vmware-to-azure.md)
> * [Fyzické do Azure](./site-recovery-set-up-physical-to-azure.md)

Tento článek popisuje, jak nastavit v místním prostředí k zahájení replikace fyzických serverů s Windows nebo Linuxem do Azure.

## <a name="prerequisites"></a>Požadavky

Článek předpokládá, že už máte:
1. Trezor služeb zotavení v [portál Azure](http://portal.azure.com "portál Azure").
3. Fyzický počítač, na které se mají nainstalovat konfigurační server.

### <a name="configuration-server-minimum-requirements"></a>Minimální požadavky na konfiguraci serveru
Následující tabulka uvádí minimální hardwaru, softwaru a požadavky sítě pro konfigurační server.
[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> Servery proxy server HTTPS nejsou podporovány konfigurační server.

## <a name="choose-your-protection-goals"></a>Volba cílů ochrany

1. V portálu Azure přejděte do **služeb zotavení** trezory okno a vyberte svůj trezor.
2. V **prostředků** nabídce trezoru, klikněte na tlačítko **Začínáme** > **Site Recovery** > **krok 1: připravte infrastrukturu** > **cíl ochrany**.

    ![Zvolte cíle.](./media/site-recovery-set-up-physical-to-azure/choose-goals.png)
3. V **cíl ochrany**, vyberte **do Azure** a **není virtualizované/jiné**a potom klikněte na **OK**.

    ![Zvolte cíle.](./media/site-recovery-set-up-physical-to-azure/physical-protection-goal.PNG)

## <a name="set-up-the-source-environment"></a>Nastavení zdrojového prostředí

1. V **připravit zdroj**, pokud nemáte konfigurační server, klikněte na tlačítko **+ konfigurační server** aby vám ho přidal.

  ![Nastavení zdroje](./media/site-recovery-set-up-physical-to-azure/plus-config-srv.png)
2. V **přidat Server** okno, zkontrolujte, zda **konfigurační Server** se zobrazí v **typ serveru**.
4. Stáhněte instalační soubor nástroje Unified instalace nástroje Site Recovery.
5. Stáhnout registrační klíč trezoru Když spustíte instalační program Unified musíte registrační klíč. Klíč je platný pět dní od jeho vygenerování.

    ![Nastavení zdroje](./media/site-recovery-set-up-physical-to-azure/set-source2.png)
6. Na počítači, který používáte jako konfigurační server, spusťte **Unified instalace nástroje Azure Site Recovery** instalace konfigurační server, procesový server a hlavní cílový server.

#### <a name="run-azure-site-recovery-unified-setup"></a>Spuštění Azure Site Recovery sjednocený instalační program

> [!TIP]
> Konfigurace serveru registrace selže, pokud čas na systémových hodin vašeho počítače je delší než 5 minut z místního času. Synchronizovat systémových hodin s [čas serveru](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service) před zahájením instalace.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Konfigurační server lze nainstalovat pomocí příkazového řádku. Další informace najdete v tématu [instalace konfigurační server pomocí nástroje příkazového řádku](http://aka.ms/installconfigsrv).


## <a name="common-issues"></a>Běžné problémy

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Další postup

Dalším krokem zahrnuje [nastavení cílového prostředí](./site-recovery-prepare-target-physical-to-azure.md) v Azure.
