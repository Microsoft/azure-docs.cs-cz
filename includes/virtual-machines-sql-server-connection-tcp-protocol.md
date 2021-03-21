---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: f7af6962c3343cd9d3e35e96d88650aa6a42c6b3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95557484"
---
1. Když jste připojeni k virtuálnímu počítači pomocí Vzdálené plochy, vyhledejte nástroj **Configuration Manager**:

    ![Otevření SSCM](./media/virtual-machines-sql-server-connection-tcp-protocol/sql-server-configuration-manager.png)

1. V podokně konzoly SQL Server Configuration Manageru rozbalte **SQL Server Network Configuration** (Konfigurace sítě SQL Serveru).

1. V podokně konzoly klikněte na **protokoly pro MSSQLSERVER** (výchozí název instance). V podokně podrobností klikněte pravým tlačítkem na **TCP** a pak klikněte na **Povolit** , pokud už není povolený.

    ![Povolení protokolu TCP](./media/virtual-machines-sql-server-connection-tcp-protocol/enable-tcp.png)

1. V podokně konzoly klikněte na **SQL Server Services** (Služby SQL Serveru). V podokně podrobností klikněte pravým tlačítkem na **SQL Server (*název instance*)** (výchozí instance je **SQL Server (MSSQLSERVER)**) a pak klikněte na **restartovat**, pokud chcete zastavit a restartovat instanci SQL Server.

    ![Restartování databázového stroje](./media/virtual-machines-sql-server-connection-tcp-protocol/restart-sql-server.png)

1. Zavřete SQL Server Configuration Manager.

Další informace o povolení protokolů pro databázový stroj SQL Serveru najdete v tématu věnovaném [povolení nebo zákazu síťového protokolu serveru](/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol).