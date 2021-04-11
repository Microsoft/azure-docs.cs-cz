---
title: 'Rychlý Start: Začínáme'
description: V tomto rychlém startu se dozvíte, jak začít s porozuměním základnímu pracovnímu postupu pro nasazení v programu Defender pro IoT.
ms.topic: quickstart
ms.date: 2/18/2021
ms.openlocfilehash: aa26ea26a3fb0a08d931657cb7ad236c68972e2f
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384949"
---
# <a name="quickstart-get-started-with-defender-for-iot"></a>Rychlý Start: Začínáme s Defenderem pro IoT

Tento článek poskytuje přehled kroků, které budete provádět při nastavování Azure Defenderu pro IoT. Proces vyžaduje:

- Zaregistrujte své předplatné a senzory na portálu Azure Defender pro IoT Portal.
- Nainstalujte software senzoru a místní konzolu pro správu.
- Proveďte počáteční aktivaci senzoru a konzoly pro správu.

## <a name="prerequisites"></a>Požadavky

- Žádné

## <a name="permission-requirements"></a>Požadavky na oprávnění

Některé kroky instalace vyžadují specifická oprávnění uživatele.

Aby bylo možné aktivovat senzory a konzolu pro správu, nahrajte certifikáty SSL/TLS a generovat nová hesla, jsou nutná oprávnění správce.

Následující tabulka popisuje oprávnění pro přístup uživatelů k Azure Defenderu pro nástroje portálu IoT:

| Oprávnění | Čtenář zabezpečení | Správce zabezpečení | Přispěvatel předplatného | Vlastník předplatného |
|--|--|--|--|--|
| Zobrazit všechny Defendery pro obrazovky a data IoT | ✓ | ✓ | ✓ | ✓ |
| Připojení snímače  |  |  ✓ | ✓ | ✓ |
| Aktualizovat ceny  |  |  ✓ | ✓ | ✓ |
| Obnovit heslo  | ✓  |  ✓ | ✓ | ✓ |

## <a name="identify-the-solution-infrastructure"></a>Identifikace infrastruktury řešení

**Vysvětlení potřeb pro instalaci sítě**

Zkoumání síťové architektury, monitorované šířky pásma a dalších podrobností o síti. Další informace najdete v tématu [o nastavení sítě Azure Defender pro IoT](how-to-set-up-your-network.md).

**Vyjasnění, které senzory a zařízení konzoly pro správu jsou nutné ke zpracování zatížení sítě**

Azure Defender pro IoT podporuje fyzické i virtuální nasazení. U fyzických nasazení si můžete koupit různá certifikovaná zařízení. Další informace najdete v tématu [určení požadovaných zařízení](how-to-identify-required-appliances.md).

Doporučujeme vypočítat přibližný počet zařízení, která se budou monitorovat. Když později zaregistrujete předplatné Azure na portál, zobrazí se výzva k zadání tohoto čísla. Čísla lze přidat v intervalech po 1 000 sekundách. Počet monitorovaných zařízení se nazývá *potvrzená zařízení*.

## <a name="register-with-azure-defender-for-iot"></a>Registrace v Azure Defenderu pro IoT

Registrace zahrnuje:

- Připojování předplatných Azure k programu Defender pro IoT.
- Definování potvrzených zařízení.
- Stahuje se aktivační soubor pro místní konzolu pro správu.

Postup registrace:

1. Přejít na portál Azure Defender pro IoT Portal.

1. Vyberte **zaregistrování předplatného**.

1. Na stránce s **cenami** vyberte předplatné nebo vytvořte nové a přidejte počet potvrzených zařízení.

1. Vyberte kartu **Stáhnout místní konzolu pro správu** a uložte stažený aktivační soubor. Tento soubor obsahuje agregovaná potvrzená zařízení, která jste definovali. Po počátečním přihlášení se soubor nahraje do konzoly pro správu.

Informace o tom, jak odpojení předplatné, najdete v tématu [odpojení a Subscription](how-to-manage-subscriptions.md#offboard-a-subscription).

## <a name="install-and-set-up-the-on-premises-management-console"></a>Instalace a nastavení místní konzoly pro správu

Po získání místního zařízení konzoly pro správu:

- Stáhněte si balíček ISO z Azure Defenderu pro IoT Portal.
- Nainstalujte software.
- Aktivovat a provést úvodní instalaci konzoly pro správu.

Instalace a nastavení:

1. V programu Defender pro IoT Portal vyberte **Začínáme** .
1. Vyberte kartu místní **Konzola pro správu** .
1. Zvolte verzi a vyberte **Stáhnout**.
1. Nainstalujte software pro místní konzolu pro správu. Další informace najdete v tématu [Defender pro instalaci IoT](how-to-install-software.md).
1. Aktivujte a nastavte konzolu pro správu. Další informace najdete v tématu [Aktivace a nastavení místní konzoly pro správu](how-to-activate-and-set-up-your-on-premises-management-console.md).

## <a name="onboard-a-sensor"></a>Připojení snímače

Zaveďte senzor tak, že ho zaregistrujete do Azure Defenderu pro IoT a stáhnete soubor aktivace senzoru:

1. Definujte název snímače a přidružte ho k předplatnému.

1. Vyberte režim správy senzorů:

   - **Senzory připojené ke cloudu**: informace, které detekuje senzory, se zobrazují v konzole senzorů. Kromě toho jsou informace o výstrahách doručovány prostřednictvím služby IoT Hub a mohou být sdíleny s ostatními službami Azure, jako je například Azure Sentinel.

   - **Místně spravované senzory**: informace, které detekuje senzory, se zobrazí v konzole senzorů. Pokud pracujete v letecké gapped síti a chcete mít jednotný přehled o všech informacích zjištěných více místně spravovanými senzory, pracujte s místní konzolou pro správu. 

1. Stáhněte si soubor aktivace senzoru.

Další informace najdete v tématu [zprovoznění a správa senzorů v programu Defender pro IoT Portal](how-to-manage-sensors-on-the-cloud.md).

## <a name="install-and-set-up-the-sensor"></a>Instalace a nastavení senzoru

Stáhněte si balíček ISO z Azure Defenderu pro IoT Portal, nainstalujte software a nastavte snímač.

1. V programu Defender pro IoT Portal vyberte **Začínáme** .

1. Vyberte **nastavit senzor**.

1. Zvolte verzi a vyberte **Stáhnout**.

1. Nainstalujte software snímače. Další informace najdete v tématu [Defender pro instalaci IoT](how-to-install-software.md).

1. Aktivujte a nastavte svůj senzor. Další informace najdete v tématu [přihlášení a aktivace snímače](how-to-activate-and-set-up-your-sensor.md).

## <a name="connect-sensors-to-an-on-premises-management-console"></a>Připojení senzorů k místní konzole pro správu

Připojte senzory ke konzole pro správu, abyste měli jistotu, že:

- Senzory odesílají informace o upozorněních a inventáři zařízení do místní konzoly pro správu.

- Místní Konzola pro správu může provádět zálohování senzorů, spravovat výstrahy, které Senzory zjišťují, prozkoumávat odpojení senzorů a provádět další činnosti na připojených senzorech.

Doporučujeme seskupit více senzorů, které sledují stejné sítě v jedné zóně. Provedete to tak, že zachytíte informace shromažďované více senzory.

Další informace najdete v tématu [propojení senzorů s místní konzolou pro správu](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console).

## <a name="populate-azure-sentinel-with-alert-information-optional"></a>Naplnit Sentinel Azure pomocí informací o výstraze (volitelné)

Pomocí konfigurace Azure Sentinelu odešlete informace o výstrahách do Azure Sentinel. Podívejte [se na téma připojení dat z Defenderu pro IoT do Azure Sentinel](how-to-configure-with-sentinel.md).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vítá vás Azure Defender pro IoT](overview.md) 
>  [Azure Defender pro architekturu IoT](architecture.md)
