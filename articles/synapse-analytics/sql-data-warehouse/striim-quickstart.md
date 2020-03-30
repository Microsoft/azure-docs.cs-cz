---
title: Striim rychlý start
description: Rychle můžete začít se Striimem a Azure SQL Data Warehouse.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: e963d62df8d2c416726852ee3d46daf1cd22936e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349942"
---
# <a name="striim-azure-sql-dw-marketplace-offering-install-guide"></a>Průvodce instalací striim Azure SQL DW Marketplace

Tento rychlý start předpokládá, že již máte již existující instanci SQL Data Warehouse.

Vyhledejte striim na Azure Marketplace a vyberte možnost Striim for Data Integration to SQL Data Warehouse (Staged) 

![Instalace Striim][install]

Konfigurace virtuálního počítače Striim se zadanými vlastnostmi, přičemž si můžete zaznapat název clusteru Striim, heslo a heslo správce.

![Konfigurace striimu][configure]

Po nasazení klikněte \<na název virtuálního počítače>-masternode na webu Azure Portal, klikněte na Připojit a zkopírujte přihlášení pomocí místního účtu virtuálního počítače. 

![Připojení striimu k datovému skladu SQL][connect]

Stáhněte sqljdbc42.jar <https://www.microsoft.com/en-us/download/details.aspx?id=54671> z místního počítače. 

Otevřete okno příkazového řádku a změňte adresáře na místo, kde jste stáhli nádobu JDBC. SCP jar soubor do vašeho Virtuálního počítače Striim, získání adresy a hesla z portálu Azure

![Kopírování souboru jar do virtuálního počítače][copy-jar]

Otevřete jiné okno příkazového řádku nebo použijte nástroj ssh pro ssh do clusteru Striim

![SSH do clusteru][ssh]

Proveďte následující příkazy přesunout jdbc jar soubor do adresáře Lib Striim a spusťte a zastavte server.

   1. sudo su
   2. cd /tmp
   3. mv sqljdbc42.jar /opt/striim/lib
   4. systemctl stop striim-node systemctl stop
   5. systemctl stop striim-dbms systemctl stop striim-dbms systemctl stop striim-dbms systemct
   6. systemctl start striim-dbms systemctl start striim-dbms systemctl start striim-dbms systemct
   7. systemctl start striim-node systemctl start

![Spuštění clusteru Striim][start-striim]

Nyní otevřete svůj oblíbený \<prohlížeč a přejděte na DNS Name>:9080

![Přechod na přihlašovací obrazovku][navigate]

Přihlaste se pomocí uživatelského jména a hesla, které jste nastavili na webu Azure Portal, a vyberte preferovaného průvodce, abyste mohli začít, nebo přejděte na stránku Aplikace a začněte používat uživatelské rozhraní přetažení.

![Přihlášení pomocí pověření serveru][login]



[install]: ./media/striim-quickstart/install.png
[configure]: ./media/striim-quickstart/configure.png
[connect]:./media/striim-quickstart/connect.png
[copy-jar]:./media/striim-quickstart/copy-jar.png
[ssh]:./media/striim-quickstart/ssh.png
[start-striim]:./media/striim-quickstart/start-striim.png
[navigate]:./media/striim-quickstart/navigate.png
[login]:./media/striim-quickstart/login.png
