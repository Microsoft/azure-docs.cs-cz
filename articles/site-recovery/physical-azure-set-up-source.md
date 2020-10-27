---
title: Nastavení konfiguračního serveru pro zotavení po havárii fyzických serverů do Azure pomocí Azure Site Recovery | Microsoft Docs
description: Tento článek popisuje, jak nastavit místní konfigurační server pro zotavení po havárii místních fyzických serverů do Azure.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: ramamill
ms.openlocfilehash: 327e995a8fe2f66903548fba054804768d2538ab
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92534290"
---
# <a name="set-up-the-configuration-server-for-disaster-recovery-of-physical-servers-to-azure"></a>Nastavení konfiguračního serveru pro zotavení po havárii fyzických serverů do Azure

Tento článek popisuje, jak nastavit místní prostředí pro zahájení replikace fyzických serverů se systémem Windows nebo Linux do Azure.

## <a name="prerequisites"></a>Požadavky

Článek předpokládá, že už máte:
- Recovery Services trezor v [Azure Portal](https://portal.azure.com "portál Azure").
- Fyzický počítač, na který se má nainstalovat konfigurační server
- Pokud jste na počítači, na který instalujete konfigurační server, zakázali protokol TLS 1,0, zkontrolujte, zda je povolena možnost TLs 1,2 a zda je na počítači nainstalovaná verze .NET Framework 4,6 nebo novější (se zapnutým silným šifrováním). [Další informace](https://support.microsoft.com/help/4033999/how-to-resolve-azure-site-recovery-agent-issues-after-disabling-tls-1).

### <a name="configuration-server-minimum-requirements"></a>Minimální požadavky konfiguračního serveru
Následující tabulka uvádí minimální požadavky na hardware, software a síť pro konfigurační server.
[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> Konfigurační server nepodporuje proxy servery založené na protokolu HTTPS.

## <a name="choose-your-protection-goals"></a>Výběr cílů ochrany

1. V Azure Portal otevřete okno trezory **Recovery Services** a vyberte svůj trezor.
2. V nabídce **prostředků** trezoru klikněte na **Začínáme**  >  **Site Recovery**  >  **Krok 1: Příprava** na  >  **cíl ochrany** infrastruktury.

    ![Snímek obrazovky, který ukazuje, kde vybrat cíl ochrany](./media/physical-azure-set-up-source/choose-goals.png)
3. V **cíli ochrany** vyberte **do Azure** a **nevirtualizovaný/jiný** a pak klikněte na **OK** .

    ![Zvolte cíle.](./media/physical-azure-set-up-source/physical-protection-goal.png)

## <a name="set-up-the-source-environment"></a>Nastavení zdrojového prostředí

1. Pokud v nabídce **Příprava zdroje** nemáte konfigurační server, přidejte ho kliknutím na **+ konfigurační server** .

   ![Snímek obrazovky, který ukazuje, jak vybrat konfigurační server.](./media/physical-azure-set-up-source/plus-config-srv.png)
2. V okně **Přidat server** ověřte, že se **konfigurační server** zobrazuje v poli **Typ serveru** .
4. Stáhněte instalační soubor sjednocené instalace Site Recovery.
5. Stáhněte registrační klíč trezoru. Registrační klíč potřebujete při spuštění sjednocené instalace. Klíč je platný pět dní od jeho vygenerování.

    ![Nastavení zdroje](./media/physical-azure-set-up-source/set-source2.png)
6. Na počítači, který používáte jako konfigurační server, spusťte **Azure Site Recovery Unified Setup** a nainstalujte konfigurační server, procesový Server a hlavní cílový server.

#### <a name="run-azure-site-recovery-unified-setup"></a>Spustit Azure Site Recovery Unified Setup

> [!TIP]
> Registrace konfiguračního serveru se nezdařila, pokud je čas v systémových hodinách počítače více než pět minut mimo místní čas. Před zahájením instalace synchronizujte systémové hodiny s [časovým serverem](/windows-server/networking/windows-time-service/windows-time-service-top) .

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Konfigurační server lze nainstalovat pomocí příkazového řádku. [Další informace](physical-manage-configuration-server.md#install-from-the-command-line).


## <a name="common-issues"></a>Běžné problémy

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Další kroky

Další krok zahrnuje [nastavení cílového prostředí](physical-azure-set-up-target.md) v Azure.
