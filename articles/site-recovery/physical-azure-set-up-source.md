---
title: Nastavení konfiguračního serveru pro zotavení fyzických serverů po havárii do Azure pomocí Azure Site Recovery | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak nastavit místní konfigurační server pro zotavení po havárii místních fyzických serverů do Azure.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: ramamill
ms.openlocfilehash: 902c14211e91a1500c6b50cd790b347e337c4f70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257872"
---
# <a name="set-up-the-configuration-server-for-disaster-recovery-of-physical-servers-to-azure"></a>Nastavení konfiguračního serveru pro zotavení fyzických serverů po havárii do Azure

Tento článek popisuje, jak nastavit místní prostředí pro zahájení replikace fyzických serverů se systémem Windows nebo Linux do Azure.

## <a name="prerequisites"></a>Požadavky

Článek předpokládá, že již máte:
- Trezor služby Recovery Services na [webu Azure Portal](https://portal.azure.com "portál Azure").
- Fyzický počítač, do kterého se má nainstalovat konfigurační server.
- Pokud jste zakázali protokol TLS 1.0 v počítači, do kterého instalujete konfigurační server, zkontrolujte, zda je povolena technologie TLs 1.2 a zda je v počítači nainstalována rozhraní .NET Framework verze 4.6 nebo novější (s povolenou silnou kryptografií). [Další informace](https://support.microsoft.com/help/4033999/how-to-resolve-azure-site-recovery-agent-issues-after-disabling-tls-1).

### <a name="configuration-server-minimum-requirements"></a>Minimální požadavky na konfigurační server
V následující tabulce jsou uvedeny minimální požadavky na hardware, software a síť pro konfigurační server.
[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> Server proxy založený na protokolu HTTPS není konfiguračním serverem podporován.

## <a name="choose-your-protection-goals"></a>Vyberte si cíle ochrany

1. Na webu Azure Portal přejděte do okna trezorů **služby Recovery Services** a vyberte trezor.
2. V nabídce **Zdroje** v úschovně klikněte na **Položku Začínáme** > s**obnovou** > webu**Step 1: Připravit** > **cíl ochrany**infrastruktury .

    ![Zvolte cíle.](./media/physical-azure-set-up-source/choose-goals.png)
3. V **cíli Ochrana**vyberte Možnost Do **Azure** a **Není virtualizovaná/Jiná**a klikněte na **OK**.

    ![Zvolte cíle.](./media/physical-azure-set-up-source/physical-protection-goal.png)

## <a name="set-up-the-source-environment"></a>Nastavení zdrojového prostředí

1. Pokud ve **zdroji Připravit**nemáte konfigurační server, přidejte ho klepnutím na **tlačítko +Konfigurační server.**

   ![Nastavení zdroje](./media/physical-azure-set-up-source/plus-config-srv.png)
2. V okně **Přidat server** zkontrolujte, zda se **konfigurační server** zobrazuje v části Server **typu**.
4. Stáhněte instalační soubor sjednoceného instalačního programu pro obnovení webu.
5. Stáhněte registrační klíč trezoru. Při spuštění sjednoceného nastavení potřebujete registrační klíč. Klíč je platný pět dní od jeho vygenerování.

    ![Nastavení zdroje](./media/physical-azure-set-up-source/set-source2.png)
6. V počítači, který používáte jako konfigurační server, spusťte **sjednocené nastavení azure site recovery** a nainstalujte konfigurační server, procesní server a hlavní cílový server.

#### <a name="run-azure-site-recovery-unified-setup"></a>Spuštění sjednoceného nastavení obnovení webu Azure

> [!TIP]
> Registrace konfiguračního serveru se nezdaří, pokud je čas v systémových hodinách počítače více než pět minut mimo místní čas. Před zahájením instalace synchronizujte systémové hodiny s [časovým serverem.](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service)

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Konfigurační server lze nainstalovat pomocí příkazového řádku. [Další informace](physical-manage-configuration-server.md#install-from-the-command-line).


## <a name="common-issues"></a>Běžné problémy

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Další kroky

Dalším krokem je [nastavení cílového prostředí](physical-azure-set-up-target.md) v Azure.
