---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 8b919608dfc562d8db77619d5215a6828a53a4aa
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51264196"
---
1. Když jste připojeni k virtuálnímu počítači pomocí Vzdálené plochy, vyhledejte nástroj **Configuration Manager**:

    ![Otevření SSCM](./media/virtual-machines-sql-server-connection-tcp-protocol/sql-server-configuration-manager.png)

1. V podokně konzoly SQL Server Configuration Manageru rozbalte **SQL Server Network Configuration** (Konfigurace sítě SQL Serveru).

1. V podokně konzoly klikněte na **Protokoly pro MSSQLSERVER** (název výchozí instance). V podokně podrobností klikněte pravým tlačítkem na **TCP** a klikněte na **Povolit** (pokud tento protokol ještě není povolený).

    ![Povolení protokolu TCP](./media/virtual-machines-sql-server-connection-tcp-protocol/enable-tcp.png)

1. V podokně konzoly klikněte na **SQL Server Services** (Služby SQL Serveru). V podokně podrobností klikněte pravým tlačítkem na **systému SQL Server (*název instance*)** (výchozí instancí je **SQL Server (MSSQLSERVER)**) a potom klikněte na tlačítko **restartovat**se zastaví a restartuje instanci systému SQL Server.

    ![Restartování databázového stroje](./media/virtual-machines-sql-server-connection-tcp-protocol/restart-sql-server.png)

1. Zavřete SQL Server Configuration Manager.

Další informace o povolení protokolů pro databázový stroj SQL Serveru najdete v tématu věnovaném [povolení nebo zákazu síťového protokolu serveru](https://msdn.microsoft.com/library/ms191294.aspx).
