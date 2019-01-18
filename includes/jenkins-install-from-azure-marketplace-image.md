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
ms.openlocfilehash: 4025dcc5824991baa9a52dbb912a5c07f4273d58
ms.sourcegitcommit: ba9f95cf821c5af8e24425fd8ce6985b998c2982
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54383295"
---
1. V prohlížeči se otevře [image Jenkinse na webu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.jenkins?tab=Overview).

1. Vyberte **získat teď**.

    ![Vyberte GIT IT teď spustit proces instalace pro image Marketplace Jenkinse.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-get-it-now.png)

1. Prohlédněte si informace o cenách podrobnosti a podmínky, vyberte **pokračovat**.

    ![Ceny a podmínky informace z Jenkins Marketplace bitové kopie.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-pricing-and-terms.png)

1. Vyberte **vytvořit** ke konfiguraci serveru Jenkins na webu Azure Portal. 

    ![Instalace image Marketplace Jenkinse.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-create.png)

1. V **Základy** kartu, zadejte následující hodnoty:

    - **Název** – zadejte `Jenkins`.
    - **Uživatelské jméno** – zadejte uživatelské jméno pro použití při přihlášení k virtuálnímu počítači, na kterém je spuštěný Jenkinse. Uživatelské jméno musí splňovat [určité požadavky](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm).
    - **Typ ověřování** – vyberte **veřejný klíč SSH**.
    - **Veřejný klíč SSH** -zkopírujte a vložte veřejný klíč RSA v jednořádkovém formátu (začíná `ssh-rsa`) nebo víceřádkovém formátu PEM. Můžete vygenerovat klíče SSH pomocí ssh-keygen v Linuxu a macOS nebo PuTTYGen ve Windows. Další informace o klíči SSH a Azure, najdete v článku, [jak používat klíče SSH s Windows v Azure](/azure/virtual-machines/linux/ssh-from-windows).
    - **Předplatné** – vyberte předplatné Azure, do kterého chcete nainstalovat Jenkinse.
    - **Skupina prostředků** – vyberte **vytvořit nový**a zadejte název pro skupinu prostředků, která slouží jako logický kontejner pro kolekci prostředků, které tvoří vaši instalaci Jenkinse.
    - **Umístění** – vyberte **USA – východ**.

    ![Zadání informací o skupiny ověřování a prostředků pro Jenkinse kartě Základní.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-basic.png)

1. Vyberte **OK** pokračujte **další nastavení** kartu. 

1. V **další nastavení** kartu, zadejte následující hodnoty:

    - **Velikost** – výběr možnosti příslušné nastavení velikosti pro virtuální počítač Jenkinse.
    - **Typ disku virtuálního počítače** – zadejte buď pevný disk (jednotku pevného disku) nebo SSD (jednotky SSD) k označení jaký typ disku úložiště je povolený pro virtuální počítač Jenkinse.
    - **Virtuální síť** – (volitelné) vyberte **virtuální síť** upravit výchozí nastavení.
    - **Podsítě** – vyberte **podsítě**, zkontrolujte zadané informace a vyberte **OK**.
    - **Veřejná IP adresa** – název IP adresy výchozím názvem Jenkins jste zadali na předchozí stránce s příponou - IP adresy. Můžete vybrat možnost změnit tuto výchozí hodnotu.
    - **Popisek názvu domény** – zadejte hodnotu pro plně kvalifikovanou adresu URL pro virtuální počítač Jenkinse.
    - **Typ verze Jenkins** – vyberte typ požadované verze z možností: `LTS`, `Weekly build`, nebo `Azure Verified`. `LTS` a `Weekly build` možnosti jsou popsány v následujícím článku [Jenkins LTS verze řádku](https://jenkins.io/download/lts/). `Azure Verified` Možnost odkazuje na [verze Jenkins LTS](https://jenkins.io/download/lts/) , která byla ověřena ke spuštění v Azure. 
    - **Typ sady JDK** -JDK k instalaci. Výchozí hodnota je testována, Zulu certifikovaných sestavení OpenJDK.

    ![Zadejte nastavení virtuálního počítače pro Jenkinse kartě nastavení.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-settings.png)

1. Vyberte **OK** pokračujte **nastavení integrace** kartu.

1. V **nastavení integrace** kartu, zadejte následující hodnoty:

    - **Instanční objekt služby** -instanční objekt služby je přidán do Jenkinse jako přihlašovací údaje pro ověřování pomocí služby Azure. `Auto` znamená to, že objekt zabezpečení, bude vytvořen pomocí MSI (identita spravované služby). `Manual` znamená to, že by měl být objekt zabezpečení vytvořené vámi. 
        - **ID aplikace** a **tajný kód** – Pokud vyberete `Manual` možnost **instanční objekt služby** možnost, budete muset zadat `Application ID` a `Secret` pro vaše instanční objekt. Když [vytvoření instančního objektu](/cli/azure/create-an-azure-service-principal-azure-cli), Všimněte si, že je výchozí role **Přispěvatel**, což je dostatečná pro práci s prostředky Azure.
    - **Povolení cloudové agentů** -zadat výchozí šablona cloud pro agenty kde `ACI` odkazuje na instanci kontejneru Azure, a `VM` odkazuje na virtuální počítače. Můžete také určit `No` pokud ho nechcete povolit agenta do cloudu.

1. Vyberte **OK** pokračujte **Souhrn** kartu.

1. Když **Souhrn** zobrazí kartu, se ověří informace zadané. Jakmile se zobrazí **ověření proběhlo úspěšně.** zprávy (v horní části karty), vyberte **OK**. 

    ![Karta Souhrn zobrazuje a ověří vybrané možnosti.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-summary.png)

1. Když **vytvořit** zobrazí kartu, vyberte **vytvořit** vytvořit virtuální počítač Jenkinse. Pokud váš server připravený, zobrazí se oznámení na webu Azure Portal.

    ![Jenkins je připraven oznámení.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-notification.png)
