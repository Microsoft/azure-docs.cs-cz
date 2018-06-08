---
title: Publikování webové aplikace na virtuální počítač Azure ze sady Visual Studio
description: Publikování aplikace technologie ASP.NET pro virtuální počítač Azure ze sady Visual Studio
services: virtual-machines-windows
author: ghogen
manager: douge
tags: azure-service-management
ms.assetid: 70267837-3629-41e0-bb58-2167ac4932b3
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: aspnet
ms.topic: conceptual
ms.date: 11/03/2017
ms.author: ghogen
ms.openlocfilehash: d9e2c1a941c25aedd9048f2784350106b8739dba
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "31797189"
---
# <a name="publish-an-aspnet-web-app-to-an-azure-vm-from-visual-studio"></a>Publikování aplikace technologie ASP.NET pro virtuální počítač Azure ze sady Visual Studio

Tento dokument popisuje, jak publikovat webovou aplikaci ASP.NET k virtuálních počítačů (VM) Azure pomocí **Microsoft Azure Virtual Machines** funkce publikování aplikace Visual Studio 2017.  

## <a name="prerequisites"></a>Požadavky
Chcete-li publikování projektu ASP.NET do virtuálního počítače Azure pomocí sady Visual Studio, virtuální počítač musí být správně nastavena.

- Počítač se musí nakonfigurovat pro spuštění webové aplikace ASP.NET a mají nainstalované aplikace WebDeploy.

- Virtuální počítač musí mít nakonfigurovaný název DNS. Další informace najdete v tématu [v portálu Azure vytvořit platný plně kvalifikovaný název domény pro virtuální počítač s Windows](portal-create-fqdn.md).

## <a name="publish-your-aspnet-web-app-to-the-azure-vm-using-visual-studio"></a>Publikování webové aplikace ASP.NET do virtuálního počítače Azure pomocí sady Visual Studio
Následující část popisuje, jak publikovat stávající webovou aplikaci ASP.NET do virtuálního počítače Azure.

1. Otevřete řešení webové aplikace ve Visual Studio 2017.
2. Klikněte pravým tlačítkem na projekt v Průzkumníku řešení a zvolte **publikování...**
3. Použijte šipku na pravé straně stránky můžete procházet pomocí možnosti publikování vyhledejte **Microsoft Azure Virtual Machines**.  

   ![Publikovat stránku – šipka doprava]

4. Vyberte **Microsoft Azure Virtual Machines** ikonu a vyberte **publikovat**.

   ![Publikovat stránku – ikona virtuální počítač Microsoft Azure]

5. Vyberte odpovídající účet (s předplatným Azure připojené k virtuálnímu počítači).  
   - Pokud jste přihlášení k sadě Visual Studio, seznamu účet bude zahrnovat všechny ověřené účty.  
   - Pokud nejste přihlášení nebo pokud budete potřebovat účet není uveden, zvolte "Přidat účet..." a postupujte podle výzev k přihlášení.  
   ![Selektor účet Azure]  

6. Vyberte odpovídající virtuální počítač ze seznamu existujících virtuálních počítačů.

   > [!Note]
   > Naplnění seznamu může chvíli trvat.

   ![Výběr virtuálních počítačů Azure]

7. Klikněte na tlačítko OK zahájíte publikování.

8. Po zobrazení výzvy k zadání pověření zadejte uživatelské jméno a heslo uživatelského účtu na cílovém virtuálním počítači, který je nakonfigurovaný s publikováním práva (obvykle správce uživatelské jméno a heslo použité při vytváření virtuálního počítače).  

   ![Web Deploy přihlášení]

9. Přijměte certifikát zabezpečení.

   ![Chyba certifikátu]

10. Podívejte se ve výstupním okně zkontrolovat průběh operace publikování.

    ![Výstup – okno]

11. Pokud publikování úspěšné, spustí se prohlížeče otevřete adresu URL webu přehled nově publikovaných.

**Úspěšné!**

Nyní byla úspěšně publikována vaší webové aplikace na virtuální počítač Azure.

## <a name="publish-page-options"></a>Možnosti stránky publikování

Po dokončení Průvodce publikování publikovat otevření stránky v dokumentu s novou vybraný profil publikování.

### <a name="re-publish"></a>Znovu publikovat

Chcete-li publikovat aktualizace webové aplikace, vyberte **publikovat** tlačítko na stránce publikovat.  
- Pokud se zobrazí výzva, zadejte uživatelské jméno a heslo.  
- Publikování začne okamžitě.

![Publikovat stránku – tlačítko Publikovat]

### <a name="modify-publish-profile-settings"></a>Upravit nastavení profilu publikování

Chcete-li zobrazit a upravit nastavení profilu publikování, vyberte **nastavení...** .  

![Publikovat stránku – tlačítko Nastavení]

Vaše nastavení by měl vypadat přibližně takto:  

![Publikovat nastavení – stránka připojení]

#### <a name="save-user-name-and-password"></a>Uložit uživatelské jméno a heslo
- Abyste se vyhnuli, poskytuje informace o ověřování pokaždé, když publikujete, můžete naplnit **uživatelské jméno** a **heslo** pole a vyberte možnost **uložit heslo** pole.
- Použití **ověřit připojení** tlačítko potvrďte, zda jste zadali správné informace.

#### <a name="deploy-to-clean-web-server"></a>Nasazení pro čištění webového serveru

- Pokud chcete zajistit, že webový server má kopii k webové aplikaci po každém odeslání čistou (a že nezbývají žádné další soubory hanging z předchozích nasazení), můžete zkontrolovat **odebrat další soubory v cílovém umístění** zaškrtnout políčko **nastavení** kartě.

- Upozornění: Publikování s tímto nastavením odstraní všechny soubory, které existují na webovém serveru (adresář wwwroot). Ujistěte se, že znáte stav počítače před publikováním když povolíte tuto možnost. 

![Nastavení – stránka Nastavení publikování]

## <a name="next-steps"></a>Další postup

### <a name="set-up-cicd-for-automated-deployment-to-azure-vm"></a>Nastavení položky konfigurace nebo CD pro automatické nasazení do virtuálního počítače Azure

Kanál nastavené průběžné doručování pomocí sady Visual Studio Team Service, naleznete v tématu [nasadit na virtuální počítač Windows](https://docs.microsoft.com/vsts/build-release/apps/cd/deploy-webdeploy-iis-deploygroups).

[VM Overview - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSName.png
[IP Address Config - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/IPAddressConfigDNSName.png
[VM Overview - DNS Configured]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSConfigured.png
[Publikovat stránku – šipka doprava]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageRightArrow.png
[Publikovat stránku – ikona virtuální počítač Microsoft Azure]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageMicrosoftAzureVirtualMachineIcon.png
[Selektor účet Azure]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectAccount.png
[Výběr virtuálních počítačů Azure]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectVM.png
[Web Deploy přihlášení]: ../../../includes/media/publish-web-app-from-visual-studio/WebDeployLogin.png
[Chyba certifikátu]: ../../../includes/media/publish-web-app-from-visual-studio/CertificateError.png
[Výstup – okno]: ../../../includes/media/publish-web-app-from-visual-studio/OutputWindow.png
[Publikovat stránku – tlačítko Publikovat]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPagePublishButton.png
[Publikovat stránku – tlačítko Nastavení]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageSettingsButton.png
[Publikovat nastavení – stránka připojení]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsConnectionPage.png
[Nastavení – stránka Nastavení publikování]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsSettingsPage.png
