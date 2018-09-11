---
title: Publikování webové aplikace na Virtuálním počítači Azure ze sady Visual Studio
description: Publikování webové aplikace ASP.NET do virtuálního počítače Azure ze sady Visual Studio
services: virtual-machines-windows
author: ghogen
manager: douge
tags: azure-service-management
ms.assetid: 70267837-3629-41e0-bb58-2167ac4932b3
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/03/2017
ms.author: ghogen
ms.openlocfilehash: d5fd6041f21700ffddd3b2c4755b7ce01691681e
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44304078"
---
# <a name="publish-an-aspnet-web-app-to-an-azure-vm-from-visual-studio"></a>Publikování webové aplikace v ASP.NET do virtuálního počítače Azure ze sady Visual Studio

Tento dokument popisuje, jak publikovat webovou aplikaci ASP.NET do virtuálních počítačů (VM) Azure pomocí **Microsoft Azure Virtual Machines** publikování funkce v sadě Visual Studio 2017.  

## <a name="prerequisites"></a>Požadavky
Chcete-li použít Visual Studio k publikování projektu ASP.NET na Virtuálním počítači Azure, virtuálního počítače musí být správně nastavena.

- Počítač musí být nakonfigurován ke spuštění webové aplikace ASP.NET a mít nainstalované nástroje WebDeploy.

- Virtuální počítač musí mít nakonfigurovaný název DNS. Další informace najdete v tématu [vytvořit použitím plně kvalifikovaného názvu domény pro virtuální počítač s Windows na webu Azure Portal](portal-create-fqdn.md).

## <a name="publish-your-aspnet-web-app-to-the-azure-vm-using-visual-studio"></a>Publikování webové aplikace ASP.NET do virtuálního počítače Azure pomocí sady Visual Studio
Následující část popisuje, jak publikovat existující webové aplikace ASP.NET na virtuálním počítači Azure.

1. Otevřete řešení webové aplikace v sadě Visual Studio 2017.
2. Klikněte pravým tlačítkem na projekt v Průzkumníku řešení a zvolte **publikování...**
3. Použijte šipku na pravé straně stránky procházejte možnosti publikování, dokud nenajdete **Microsoft Azure Virtual Machines**.  

   ![Publikovat stránku – šipka doprava]

4. Vyberte **Microsoft Azure Virtual Machines** ikonu a vyberte **publikovat**.

   ![Publikovat stránku – ikona virtuálního počítače Microsoft Azure]

5. Vyberte příslušný účet (s předplatným Azure připojené k virtuálnímu počítači).  
   - Pokud jste přihlášeni k sadě Visual Studio, seznam účtů se vyplní všechny vaše účty ověřené.  
   - Pokud nejste přihlášení nebo pokud budete potřebovat účet není uveden, zvolte "Přidat účet..." a postupujte podle výzev k přihlášení.  
   ![Výběr účtu Azure]  

6. Vyberte příslušný virtuální počítač ze seznamu existujících virtuálních počítačů.

   > [!Note]
   > Naplnění tohoto seznamu může chvíli trvat.

   ![Azure VM selektor]

7. Kliknutím na tlačítko OK spusťte publikování.

8. Když se zobrazí výzva k zadání přihlašovacích údajů, zadejte uživatelské jméno a heslo uživatelského účtu na cílovém virtuálním počítači, který je nakonfigurovaný s publikováním práva (obvykle správce uživatelské jméno a heslo použité při vytváření virtuálního počítače).  

   ![Nástroj WebDeploy přihlášení]

9. Přijměte certifikát zabezpečení.

   ![Chyba certifikátu]

10. Podívejte se na okno výstupu a zkontrolovat průběh operace publikování.

    ![Okno výstup]

11. Při úspěšném publikování spustí prohlížeči otevřít adresu URL nově publikovaného webu.

**Úspěch!**

Nyní byla úspěšně publikována vaší webové aplikace na virtuálním počítači Azure.

## <a name="publish-page-options"></a>Stránka Možnosti publikování

Po dokončení Průvodce publikovat, na stránce publikování otevřít v dokumentu s novou vybraný profil publikování.

### <a name="re-publish"></a>Opakované publikování

Chcete-li publikovat aktualizace webové aplikace, vyberte **publikovat** tlačítko na stránce publikování.  
- Pokud se zobrazí výzva, zadejte uživatelské jméno a heslo.  
- Publikování začne okamžitě.

![Publikovat stránku – tlačítko Publikovat]

### <a name="modify-publish-profile-settings"></a>Upravit nastavení profilu publikování

Chcete-li zobrazit a upravit nastavení profilu publikování, vyberte **nastavení...** .  

![Publikovat stránku – tlačítko Nastavení]

Vaše nastavení by měl vypadat přibližně takto:  

![Publikovat nastavení – stránka připojení]

#### <a name="save-user-name-and-password"></a>Uložit uživatelské jméno a heslo
- Pokud chcete vyhnout, poskytuje informace o ověřování při každém publikování, která můžete naplnit **uživatelské jméno** a **heslo** pole a vyberte možnost **uložit heslo** pole.
- Použití **ověřit připojení** potvrďte, že jste zadali správné informace.

#### <a name="deploy-to-clean-web-server"></a>Nasazení do čistého webového serveru

- Pokud chcete zajistit, že kopii webové aplikace za každý nahraný čisté webový server (a, že žádné soubory zůstanou Předsazení z předchozích nasazení), můžete zkontrolovat **odebrat další soubory v cílovém umístění** zaškrtávací políčko ve **nastavení** kartu.

- Upozornění: Publikování s tímto nastavením odstraní všechny soubory, které existují na webovém serveru (adresář wwwroot). Ujistěte se, že víte, stav počítače před publikováním tyto volby zapnuty. 

![Nastavení – stránka Nastavení publikování]

## <a name="next-steps"></a>Další postup

### <a name="set-up-cicd-for-automated-deployment-to-azure-vm"></a>Nastavení CI/CD pro automatické nasazení do virtuálních počítačů Azure

Pokud chcete nastavit kanál průběžného doručování s kanály Azure, přečtěte si [nasadit na virtuální počítač Windows](https://docs.microsoft.com/vsts/build-release/apps/cd/deploy-webdeploy-iis-deploygroups).

[VM Overview - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSName.png
[IP Address Config - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/IPAddressConfigDNSName.png
[VM Overview - DNS Configured]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSConfigured.png
[Publikovat stránku – šipka doprava]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageRightArrow.png
[Publikovat stránku – ikona virtuálního počítače Microsoft Azure]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageMicrosoftAzureVirtualMachineIcon.png
[Výběr účtu Azure]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectAccount.png
[Azure VM selektor]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectVM.png
[Nástroj WebDeploy přihlášení]: ../../../includes/media/publish-web-app-from-visual-studio/WebDeployLogin.png
[Chyba certifikátu]: ../../../includes/media/publish-web-app-from-visual-studio/CertificateError.png
[Okno výstup]: ../../../includes/media/publish-web-app-from-visual-studio/OutputWindow.png
[Publikovat stránku – tlačítko Publikovat]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPagePublishButton.png
[Publikovat stránku – tlačítko Nastavení]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageSettingsButton.png
[Publikovat nastavení – stránka připojení]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsConnectionPage.png
[Nastavení – stránka Nastavení publikování]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsSettingsPage.png
