---
description: zahrnout soubor
author: tomarcher
manager: rloutlaw
ms.service: multiple
ms.workload: web
ms.devlang: na
ms.topic: include
ms.date: 03/12/2018
ms.author: tarcher
ms.custom: Jenkins
ms.openlocfilehash: 5439de30b02b0ce05853c8112f9e29239743ef98
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "67175627"
---
1. V prohlížeči otevřete [image Azure Marketplace pro Jenkinse](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.jenkins?tab=Overview).

1. Vyberte **MOŽNOST ZÍSKAT NYNÍ**.

    ![Chcete-li spustit proces instalace image Jenkins Marketplace, vyberte git it now.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-get-it-now.png)

1. Po kontrole podrobností o cenách a informací o podmínkách vyberte **Pokračovat**.

    ![Informace o cenách obrázků a podmínkách jenkins marketplace.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-pricing-and-terms.png)

1. Vyberte **Vytvořit** a nakonfigurujete server Jenkins na webu Azure Portal. 

    ![Nainstalujte bitovou kopii tržiště Jenkins Marketplace.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-create.png)

1. Na kartě **Základy** zadejte následující hodnoty:

   - **Název** - `Jenkins`Zadejte .
   - **Uživatelské jméno** – Zadejte uživatelské jméno, které se má použít při přihlašování k virtuálnímu počítači, na kterém je Jenkins spuštěn. Uživatelské jméno musí splňovat [určité požadavky](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm).
   - **Typ ověřování** – vyberte **veřejný klíč SSH**.
   - **SSH public key** - Zkopírujte a vložte veřejný klíč `ssh-rsa`RSA v jednořádkovém formátu (začínající) nebo víceřádkového formátu PEM. Můžete generovat SSH klíče pomocí ssh-keygen na Linuxu a macOS, nebo PuTTYGen na Windows. Další informace o klíčích SSH a Azure najdete v článku [Jak používat klíče SSH s Windows v Azure](/azure/virtual-machines/linux/ssh-from-windows).
   - **Předplatné** – vyberte předplatné Azure, do kterého chcete nainstalovat Jenkinse.
   - **Skupina prostředků** – vyberte **Vytvořit nový**a zadejte název skupiny prostředků, který slouží jako logický kontejner pro kolekci prostředků, které tvoří instalaci Jenkinse.
   - **Umístění** – vyberte **možnost Východ USA**.

     ![Na kartě Základní zadejte informace o ověřování a skupině prostředků pro Jenkinse.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-basic.png)

1. Chcete-li přejít na kartu **Další nastavení,** vyberte **možnost OK.** 

1. Na kartě **Další nastavení** zadejte následující hodnoty:

   - **Velikost** – vyberte příslušnou možnost velikosti pro váš virtuální počítač Jenkins.
   - **Typ disku virtuálního počítače** – Zadejte pevný disk (pevný disk) nebo SSD (ssd disk), který označuje, který typ disku úložiště je povolen pro virtuální počítač Jenkins.
   - **Virtuální síť** - (Volitelné) Vyberte **virtuální síť,** chcete-li změnit výchozí nastavení.
   - **Podsítě** - Vyberte **podsítě**, ověřte informace a vyberte **OK**.
   - **Veřejná IP adresa** – název IP adresy je výchozí pro název Jenkinse, který jste zadali na předchozí stránce s příponou -IP. Můžete vybrat možnost změnit toto výchozí nastavení.
   - **Popisek názvu domény** – zadejte hodnotu plně kvalifikované adresy URL virtuálního počítače Jenkins.
   - **Typ vydání Jenkinse** - Vyberte požadovaný `LTS` `Weekly build`typ `Azure Verified`verze z možností: , , nebo . `LTS` Možnosti `Weekly build` a jsou vysvětleny v článku [Jenkins LTS Release Line](https://jenkins.io/download/lts/). Tato `Azure Verified` možnost odkazuje na [verzi Jenkins LTS,](https://jenkins.io/download/lts/) která byla ověřena pro spuštění v Azure. 
   - **JDK Typ** - JDK, které mají být instalovány. Výchozí hodnota je Zulu testován, certifikované sestavení OpenJDK.

     ![Na kartě Nastavení zadejte nastavení virtuálního počítače pro Jenkinse.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-settings.png)

1. Chcete-li přejít na kartu **Nastavení integrace,** vyberte **možnost OK.**

1. Na kartě **Nastavení integrace** zadejte následující hodnoty:

    - **Instanční objekt** – instanční objekt služby se přidá do Jenkinse jako přihlašovací údaje pro ověřování pomocí Azure. `Auto`znamená, že objekt zabezpečení bude vytvořen MSI (Identita spravované služby). `Manual`znamená, že hlavní objekt by měl být vytvořen vámi. 
        - **ID aplikace** a **tajné** – `Manual` pokud vyberete možnost **pro instanční** `Application ID` objekt, budete muset zadat a `Secret` pro váš instanční objekt. Při [vytváření instančního objektu](/cli/azure/create-an-azure-service-principal-azure-cli)si všimněte, že výchozí role je **Přispěvatel**, který je dostatečný pro práci s prostředky Azure.
    - **Povolit cloudové agenty** – zadejte výchozí cloudovou šablonu pro agenty, kde `ACI` odkazuje na Azure Container Instance a `VM` odkazuje na virtuální počítače. Můžete také `No` určit, pokud nechcete povolit agenta cloudu.

1. Výběrem **možnosti OK** přejdete na kartu **Souhrn.**

1. Když se zobrazí karta **Souhrn,** zadané informace se ověří. Jakmile se zobrazí zpráva **Ověření předáno** (v horní části karty), vyberte **OK**. 

     ![Karta Souhrn zobrazí a ověří vybrané možnosti.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-summary.png)

1. Když se zobrazí karta **Vytvořit,** vyberte **Vytvořit** a vytvořte virtuální počítač Jenkins. Když je váš server připravený, zobrazí se na webu Azure Portal oznámení.

     ![Jenkins je připraven k oznámení.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-notification.png)
