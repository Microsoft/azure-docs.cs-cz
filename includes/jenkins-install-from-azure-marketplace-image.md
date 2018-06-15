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
ms.openlocfilehash: 552e93e9bd1b17c73fb1638fbae2ac30b051c261
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
ms.locfileid: "29957545"
---
1. V prohlížeči otevřete [Azure Marketplace bitovou kopii pro volaných](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.jenkins?tab=Overview).

1. Vyberte **získat IT teď**.

    ![Tuto možnost vyberte GIT IT teď ke spuštění procesu instalace bitové kopie volaných Marketplace.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-get-it-now.png)

1. Po zkontrolování informací cenovou podrobnosti a podmínky, vyberte **pokračovat**.

    ![Ceny a podmínky informace z webu Marketplace volaných bitové kopie.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-pricing-and-terms.png)

1. Vyberte **vytvořit** konfigurace serveru volaných na portálu Azure. 

    ![Nainstalujte bitovou kopii volaných Marketplace.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-create.png)

1. V **Základy** kartě, zadejte následující hodnoty:

    - **Název** -zadejte `Jenkins`.
    - **Uživatelské jméno** -zadejte uživatelské jméno pro použití při přihlašování do virtuálního počítače, na kterém je spuštěn volaných. Uživatelské jméno musí splňovat [určité požadavky](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm).
    - **Typ ověřování** – vyberte **veřejný klíč SSH**.
    - **Veřejný klíč SSH** -kopírování a vkládání veřejného klíče RSA ve formátu jeden řádek (počínaje `ssh-rsa`) nebo formátu PEM víceřádkový. Můžete vygenerovat klíče SSH pomocí ssh-keygen na Linuxu a systému macOS nebo PuTTYGen ve Windows. Další informace o klíčů SSH a Azure, najdete v článku [jak klíče použití SSH se systémem Windows v Azure](/azure/virtual-machines/linux/ssh-from-windows).
    - **Předplatné** -vyberte předplatné Azure, do kterého chcete nainstalovat volaných.
    - **Skupina prostředků** – vyberte **vytvořit nový**a zadejte název pro skupinu prostředků, která slouží jako logický kontejner pro kolekci prostředků, které tvoří vaši volaných instalaci.
    - **Umístění** – vyberte **východní USA**.

    ![Zadejte skupiny informace o ověřování a prostředků pro volaných na kartě Základní.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-basic.png)

1. Vyberte **OK** Chcete-li pokračovat **další nastavení** kartě. 

1. V **další nastavení** kartě, zadejte následující hodnoty:

    - **Velikost** -vyberte možnost odpovídající velikosti vašeho volaných virtuálního počítače.
    - **Typ disku virtuálního počítače** – zadejte buď HDD (jednotku pevného disku) nebo SSD (jednotka SSD) k označení disku typů úložiště, které je povoleno pro virtuální počítač volaných.
    - **Virtuální síť** – (volitelné) vyberte **virtuální síť** můžete upravit výchozí nastavení.
    - **Podsítě** – vyberte **podsítě**, zkontrolujte zadané informace a vyberte **OK**.
    - **Veřejná IP adresa** -výchozí název adresy IP na volaných název, který jste zadali na předchozí stránce s příponou - IP. Můžete vybrat možnost změnit tuto výchozí nastavení.
    - **Popisek názvu domény** – zadejte hodnotu pro plně kvalifikovanou adresu URL pro virtuální počítač volaných.
    - **Typ verze volaných** – typ požadovanou verzi, vyberte z možností: `LTS`, `Weekly build`, nebo `Azure Verified`. `LTS` a `Weekly build` možnosti je podrobně popsaný v článku [volaných LTS verze řádku](https://jenkins.io/download/lts/). `Azure Verified` Možnost odkazuje [volaných LTS verze](https://jenkins.io/download/lts/) , byla ověřena ke spuštění v Azure. 

    ![Zadejte nastavení virtuálního počítače pro volaných na kartě nastavení.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-settings.png)

1. Vyberte **OK** Chcete-li pokračovat **nastavení integrace** kartě.

1. V **nastavení integrace** kartě, zadejte následující hodnoty:

    - **Instanční objekt** -objektu služby se přidá do volaných jako pověření pro ověřování s Azure. `Auto` znamená, že objekt se vytvoří pomocí Instalační služby MSI (identita spravované služby). `Manual` znamená, že jste by měl vytvořit objekt zabezpečení. 
        - **ID aplikace** a **tajný klíč** – Pokud vyberete `Manual` možnost **instanční objekt** možnost, budete muset zadat `Application ID` a `Secret` pro vaše instanční objekt. Když [vytvoření objektu služby](/cli/azure/create-an-azure-service-principal-azure-cli), Všimněte si, že je výchozí role **Přispěvatel**, což je dostatečný pro práci s prostředky Azure.
    - **Aktivovat agenty cloudu** – zadejte výchozí šablony cloudu pro agenty, kde `ACI` odkazuje na kontejner instanci Azure a `VM` odkazuje na virtuální počítače. Můžete také zadat `No` Pokud si chcete povolit agenta cloudu.

1. Vyberte **OK** Chcete-li pokračovat **Souhrn** kartě.

1. Když **Souhrn** karta zobrazuje, se ověří zadané informace. Jakmile se zobrazí **ověření proběhlo úspěšně.** zpráv (v horní části na kartě), vyberte **OK**. 

    ![Souhrn zobrazí a ověří vybrané možnosti.](./media/jenkins-install-from-azure-marketplace-image/jenkins-configure-summary.png)

1. Když **vytvořit** zobrazí kartu, vyberte **vytvořit** k vytvoření virtuálního počítače volaných. Pokud váš server je připraven, zobrazí se oznámení na portálu Azure.

    ![Volaných je připraven oznámení.](./media/jenkins-install-from-azure-marketplace-image/jenkins-install-notification.png)