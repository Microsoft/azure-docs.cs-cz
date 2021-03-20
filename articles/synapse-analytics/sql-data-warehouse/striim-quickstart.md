---
title: Rychlé spuštění Striim
description: Striim a Azure synapse Analytics vám umožní rychle začít.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 10/12/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: d6b5fa1a42201479c5d426a5bbb9188d0cb8e03d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89485970"
---
# <a name="striim-azure-synapse-analytics-marketplace-offering-install-guide"></a>Průvodce instalací nabídky Azure synapse Analytics Marketplace Striim

V tomto rychlém startu se předpokládá, že už máte již existující instanci služby Azure synapse Analytics.

V Azure Marketplace vyhledejte Striim a vyberte možnost Striim pro integraci dat do Azure synapse Analytics (dvoufázové). 

![Nainstalovat Striim][install]

Konfigurace virtuálního počítače s Striim pomocí zadaných vlastností, který označuje název clusteru Striim, heslo a heslo správce

![Konfigurace Striim][configure]

Po nasazení klikněte na \<VM Name> masternode v Azure Portal, klikněte na připojit a zkopírujte přihlašovací údaje pomocí místního účtu VM. 

![Připojení Striim ke službě Azure synapse Analytics][connect]

Stáhněte si sqljdbc42. jar z <https://www.microsoft.com/en-us/download/details.aspx?id=54671> webu do místního počítače. 

Otevřete okno příkazového řádku a změňte adresáře na místo, kam jste stáhli JDBC jar. Zascp soubor JAR na virtuální počítač s Striim a získá adresu a heslo z Azure Portal

![Kopírovat soubor JAR do virtuálního počítače][copy-jar]

Otevření jiného okna příkazového řádku nebo použití nástroje SSH k SSH do clusteru Striim

![SSH do clusteru][ssh]

Spusťte následující příkazy pro přesunutí souboru jar JDBC do adresáře lib Striim a spusťte a zastavte Server.

   1. sudo Su
   2. adresáře/TMP CD
   3. MV sqljdbc42. jar/opt/striim/lib
   4. systemctl zastavit striim-Node
   5. systemctl zastavit striim – DBMS
   6. systemctl Start striim – DBMS
   7. systemctl Start striim-Node

![Spuštění clusteru Striim][start-striim]

Teď otevřete oblíbený prohlížeč a přejděte na \<DNS Name> : 9080

![Přejít na přihlašovací obrazovku][navigate]

Přihlaste se pomocí uživatelského jména a hesla, které jste nastavili v Azure Portal, vyberte preferovaného průvodce, abyste mohli začít, nebo přejít na stránku aplikace a začněte používat uživatelské rozhraní přetažení.

![Přihlášení pomocí přihlašovacích údajů serveru][login]



[install]: ./media/striim-quickstart/install.png
[configure]: ./media/striim-quickstart/configure.png
[connect]:./media/striim-quickstart/connect.png
[copy-jar]:./media/striim-quickstart/copy-jar.png
[ssh]:./media/striim-quickstart/ssh.png
[start-striim]:./media/striim-quickstart/start-striim.png
[navigate]:./media/striim-quickstart/navigate.png
[login]:./media/striim-quickstart/login.png
