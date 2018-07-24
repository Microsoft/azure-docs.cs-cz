---
title: Nastavit zdrojové prostředí (fyzických serverů do Azure) | Dokumentace Microsoftu
description: Tento článek popisuje, jak nastavit v místním prostředí ke spuštění replikace fyzických serverů s Windows nebo Linuxem do Azure.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 07/21/2018
ms.author: raynew
ms.openlocfilehash: 0cbba45ce49667293d8f16bf370424acd70ff78b
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213479"
---
# <a name="set-up-the-source-environment-physical-server-to-azure"></a>Nastavit zdrojové prostředí (z fyzického serveru do Azure)

Tento článek popisuje, jak nastavit v místním prostředí ke spuštění replikace fyzických serverů s Windows nebo Linuxem do Azure.

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že již máte:
- V trezoru služby Recovery Services [webu Azure portal](http://portal.azure.com "webu Azure portal").
- Fyzický počítač, na kterém se má nainstalovat konfigurační server.
- Pokud jste jste zakázali protokol TLS 1.0 na počítači, na kterém instalujete konfiguračního serveru, ujistěte se, že je povoleno TLs 1.2, a rozhraní .NET Framework verze 4.6 nebo novější byl nainstalován na počítači (pomocí silného šifrování, zakázáno). [Další informace](https://support.microsoft.com/help/4033999/how-to-resolve-azure-site-recovery-agent-issues-after-disabling-tls-1).

### <a name="configuration-server-minimum-requirements"></a>Minimální požadavky na konfiguraci serveru
V následující tabulce jsou uvedeny minimální hardware, software a požadavky na síť pro konfigurační server.
[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> Konfigurační server nejsou podporovány servery proxy server založený na protokolu HTTPS.

## <a name="choose-your-protection-goals"></a>Volba cílů ochrany

1. Na webu Azure Portal, přejděte **služby Recovery Services** trezory Recovery Services okna a vyberte svůj trezor.
2. V **prostředků** nabídky trezoru, klikněte na tlačítko **Začínáme** > **Site Recovery** > **krok 1: Příprava infrastruktury**   >  **Cíl ochrany**.

    ![Zvolte cíle.](./media/physical-azure-set-up-source/choose-goals.png)
3. V **cíl ochrany**vyberte **do Azure** a **nevirtualizované/jiné**a potom klikněte na tlačítko **OK**.

    ![Zvolte cíle.](./media/physical-azure-set-up-source/physical-protection-goal.png)

## <a name="set-up-the-source-environment"></a>Nastavení zdrojového prostředí

1. V **připravit zdroj**, pokud nemáte konfiguračního serveru, klikněte na tlačítko **+ konfigurační server** aby vám ho přidal.

  ![Nastavení zdroje](./media/physical-azure-set-up-source/plus-config-srv.png)
2. V **přidat Server** okno, zkontrolujte, že **konfigurační Server** se zobrazí v **typ serveru**.
4. Stáhněte si instalační soubor sjednocené instalace Site Recovery.
5. Stáhněte registrační klíč trezoru. Při spuštění sjednocené instalace potřebujete registrační klíč. Klíč je platný pět dní od jeho vygenerování.

    ![Nastavení zdroje](./media/physical-azure-set-up-source/set-source2.png)
6. Na počítači, který používáte jako konfigurační server, spusťte **Azure Site Recovery Unified Setup** nainstalovat konfigurační server, procesový server a hlavní cílový server.

#### <a name="run-azure-site-recovery-unified-setup"></a>Spuštění Azure Site Recovery sjednocené instalace

> [!TIP]
> Konfigurace serveru registrace selže, pokud hodiny systému v počítači je více než pět minut mimo místní čas. Synchronizace se systémové hodiny [časovým serverem](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service) před zahájením instalace.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Konfigurační server můžete nainstalovat pomocí příkazového řádku. [Další informace](physical-manage-configuration-server.md#install-from-the-command-line).


## <a name="common-issues"></a>Běžné problémy

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Další postup

Další krok zahrnuje [nastavení cílového prostředí](physical-azure-set-up-target.md) v Azure.
