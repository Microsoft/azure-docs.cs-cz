---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: e18d0a6a01a86f844edc213fc95003cf4f4b46c9
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50166361"
---
* Připojte se k virtuálnímu počítači procesového serveru pomocí Připojení ke vzdálené ploše.
* Program cspsconfigtool.exe můžete spustit kliknutím na zkratku na ploše. (Nástroje budou automaticky spuštěné, pokud se to při prvním přihlášení do procesového serveru).
  - Plně kvalifikovaný název (FQDN) nebo IP adresa konfiguračního serveru.
  - Port, na kterém konfigurační server naslouchá. Hodnota by měla být 443.
  - Přístupové heslo připojení pro připojení ke konfiguračnímu serveru.
  - Port pro přenos dat, který se má pro tento procesový server nakonfigurovat. Ponechte výchozí hodnotu, pokud jste nezměnili číslo portu ve svém prostředí.

    ![Registrace procesového serveru](./media/site-recovery-vmware-register-process-server/register-ps.png)
* Kliknutím na tlačítko Uložit uložte konfiguraci a zaregistrujte procesový server.
