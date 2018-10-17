---
title: Striim rychlý start s Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Začněte rychle pomocí Striim a Azure SQL Data Warehouse.
services: sql-data-warehouse
author: hirokib
manager: jrj
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 10/12/2018
ms.author: elbutter
ms.reviewer: craigg
ms.openlocfilehash: 09f3126725b9a2cd6a8277447c0eed4a16838429
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2018
ms.locfileid: "49355423"
---
# <a name="striim-azure-sql-dw-marketplace-offering-install-guide"></a>Striim Azure SQL data Warehouse Marketplace nabízí Instalační příručka

V tomto rychlém startu se předpokládá, že už máte existující instanci služby SQL Data Warehouse.

Vyhledejte Striim na webu Azure Marketplace a vyberte Striim pro integraci dat do SQL Data Warehouse (Rozfázováno) možnost 

![Nainstalujte Striim][install]

Konfigurace virtuálního počítače Striim pomocí zadané vlastnosti poznamenat Striim clusteru jméno, heslo a heslo správce

![Konfigurace Striim][configure]

Po nasazení, klikněte na <VM Name>masternode – na webu Azure Portal, klikněte na připojit a kopírovat přihlášení pomocí místního účtu virtuálního počítače 

![Striim připojení k SQL Data Warehouse][connect]

Stáhněte si sqljdbc42.jar z <https://www.microsoft.com/en-us/download/details.aspx?id=54671> do místního počítače. 

Otevřete okno příkazového řádku a změňte adresáře na které jste stáhli soubor jar JDBC. Spojovací bod služby na soubor jar k vašemu virtuálnímu počítači Striim, získávání adresy a hesla z webu Azure portal

![Zkopírujte soubor jar k vašemu virtuálnímu počítači][copy-jar]

Otevřete další okno příkazového řádku nebo pomocí ssh nástroje ssh do Striim clusteru

![Připojte se přes SSH ke clusteru][ssh]

Spuštěním následujících příkazů do adresáře Striim lib, přesunout soubor .JAR s JDBC a spuštění a zastavení serveru.

   1. sudo su
   2. CD TMP
   3. MV sqljdbc42.jar /opt/striim/lib
   4. zastavení systemctl striim-node
   5. zastavení systemctl striim-dbms
   6. Počáteční systemctl striim-dbms
   7. Počáteční systemctl striim uzel

![Spustit Striim cluster][start-striim]

Nyní, otevřete svůj oblíbený prohlížeč a přejděte do <DNS Name>: 9080

![Přejít na přihlašovací obrazovku][navigate]

Přihlaste se pomocí uživatelského jména a hesla nastavit na webu Azure Portal a vyberte upřednostňovaný Průvodce Začínáme, nebo přejděte na stránku aplikace a začít používat přetahování a uživatelské prostředí

![Přihlaste se pomocí přihlašovacích údajů serveru][login]



[install]: ./media/striim-quickstart/install.png
[configure]: ./media/striim-quickstart/configure.png
[connect]:./media/striim-quickstart/connect.png
[copy-jar]:./media/striim-quickstart/copy-jar.png
[ssh]:./media/striim-quickstart/ssh.png
[start-striim]:./media/striim-quickstart/start-striim.png
[navigate]:./media/striim-quickstart/navigate.png
[login]:./media/striim-quickstart/login.png