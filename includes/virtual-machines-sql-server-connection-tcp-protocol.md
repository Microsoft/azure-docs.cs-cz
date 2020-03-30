---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 8b919608dfc562d8db77619d5215a6828a53a4aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67174949"
---
1. Když jste připojeni k virtuálnímu počítači pomocí Vzdálené plochy, vyhledejte nástroj **Configuration Manager**:

    ![Otevření SSCM](./media/virtual-machines-sql-server-connection-tcp-protocol/sql-server-configuration-manager.png)

1. V podokně konzoly SQL Server Configuration Manageru rozbalte **SQL Server Network Configuration** (Konfigurace sítě SQL Serveru).

1. V podokně konzoly klikněte na **protokoly pro mssqlserver** (výchozí název instance).) V podokně podrobností klikněte pravým tlačítkem myši na **tcp** a klikněte na **Povolit,** pokud ještě není povolen.

    ![Povolení protokolu TCP](./media/virtual-machines-sql-server-connection-tcp-protocol/enable-tcp.png)

1. V podokně konzoly klikněte na **SQL Server Services** (Služby SQL Serveru). V podokně podrobností klepněte pravým tlačítkem myši na **SQL Server *(název instance*)** (výchozí instance je SQL Server **(MSSQLSERVER)** a potom klepněte na tlačítko **Restartovat**, chcete-li zastavit a restartovat instanci serveru SQL Server.

    ![Restartování databázového stroje](./media/virtual-machines-sql-server-connection-tcp-protocol/restart-sql-server.png)

1. Zavřete SQL Server Configuration Manager.

Další informace o povolení protokolů pro databázový stroj SQL Serveru najdete v tématu věnovaném [povolení nebo zákazu síťového protokolu serveru](https://msdn.microsoft.com/library/ms191294.aspx).
