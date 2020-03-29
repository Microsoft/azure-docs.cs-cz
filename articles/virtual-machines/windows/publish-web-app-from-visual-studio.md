---
title: Publikování webové aplikace do virtuálního počítače Azure z Visual Studia
description: Publikování webové aplikace ASP.NET do virtuálního počítače Azure z Visual Studia
services: virtual-machines-windows
author: ghogen
manager: jillfra
tags: azure-service-management
ms.assetid: 70267837-3629-41e0-bb58-2167ac4932b3
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/03/2017
ms.author: ghogen
ms.openlocfilehash: 6e5db9b4e46019aa386057d51d956ff11d90f498
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71970866"
---
# <a name="publish-an-aspnet-web-app-to-an-azure-vm-from-visual-studio"></a>Publikování ASP.NET webové aplikace do virtuálního počítače Azure z Visual Studia

Tento dokument popisuje, jak publikovat ASP.NET webové aplikace do virtuálního počítače Azure (VM) pomocí funkce publikování **virtuálních počítačů Microsoft Azure** ve Visual Studiu 2019.  

## <a name="prerequisites"></a>Požadavky
Aby bylo možné použít Visual Studio k publikování projektu ASP.NET do virtuálního počítače Azure, musí být virtuální počítač správně nastaven.

- Počítač musí být nakonfigurován tak, aby spouštěl ASP.NET webové aplikace a měl nainstalovaný WebDeploy.

- Virtuální počítače musí mít nakonfigurovaný název DNS. Další informace najdete [v tématu Vytvoření plně kvalifikovaného názvu domény na webu Azure Portal pro virtuální počítač s Windows](portal-create-fqdn.md).

## <a name="publish-your-aspnet-web-app-to-the-azure-vm-using-visual-studio"></a>Publikování ASP.NET webové aplikace do virtuálního počítače Azure pomocí Visual Studia
Následující část popisuje, jak publikovat existující ASP.NET webové aplikace do virtuálního počítače Azure.

1. Otevřete řešení webové aplikace ve Visual Studiu 2019.
2. Klikněte pravým tlačítkem myši na projekt v Průzkumníku řešení a zvolte **Publikovat...**
3. Pomocí šipky vpravo od stránky můžete procházet možnosti publikování, dokud nenajdete **virtuální počítače Microsoft Azure**.  

   ![Publikovat stránku – šipka doprava]

4. Vyberte ikonu **Virtuální počítače Microsoft Azure** a vyberte **Publikovat**.

   ![Stránka publikování – ikona Virtuálního počítače Microsoft Azure]

5. Zvolte příslušný účet (s předplatným Azure připojeným k vašemu virtuálnímu počítači).  
   - Pokud jste přihlášeni k sadě Visual Studio, seznam účtů je naplněn všemi ověřenými účty.  
   - Pokud nejste přihlášeni nebo pokud účet, který potřebujete, není uveden, zvolte "Přidat účet..." a postupujte podle pokynů pro přihlášení.  
   ![Azure Account Selector]  

6. Vyberte příslušný virtuální počítač ze seznamu existujících virtuálních počítačů.

   > [!Note]
   > Vyplnění tohoto seznamu může nějakou dobu trvat.

   ![Výběr virtuálních počítačů Azure]

7. Chcete-li zahájit publikování, klepněte na tlačítko OK.

8. Po zobrazení výzvy k zadání pověření zadejme uživatelské jméno a heslo uživatelského účtu na cílovém virtuálním počítači, který je nakonfigurovaný s právy k publikování. Tato pověření jsou obvykle uživatelské jméno správce a heslo používané při vytváření virtuálního počítače.  

   ![Přihlášení webdeployu]

9. Přijměte certifikát zabezpečení.

   ![Chyba certifikátu]

10. Podívejte se na okno Výstup a zkontrolujte průběh operace publikování.

    ![Okno Výstup]

11. Pokud je publikování úspěšné, prohlížeč se spustí a otevře adresu URL nově publikovaného webu.

**Úspěch!**

Teď jste úspěšně publikovali webovou aplikaci do virtuálního počítače Azure.

## <a name="publish-page-options"></a>Možnosti stránky publikování

Po dokončení průvodce publikováním se stránka Publikovat otevře v dokumentu dobře s vybraným novým profilem publikování.

### <a name="re-publish"></a>Znovu publikovat

Chcete-li publikovat aktualizace webové aplikace, vyberte na stránce Publikovat tlačítko **Publikovat.**  
- Pokud se zobrazí výzva, zadejte uživatelské jméno a heslo.  
- Publikování začíná okamžitě.

![Publikovat stránku – tlačítko Publikovat]

### <a name="modify-publish-profile-settings"></a>Změna nastavení profilu publikování

Chcete-li zobrazit a upravit nastavení profilu publikování, vyberte **nastavení...**.  

![Publikovat stránku – tlačítko Nastavení]

Vaše nastavení by mělo vypadat nějak takto:  

![Nastavení publikování – stránka Připojení]

#### <a name="save-user-name-and-password"></a>Uložit uživatelské jméno a heslo
- Při každém publikování se vyhněte poskytování ověřovacích informací. Chcete-li tak učinit, vyplňte pole **Uživatelské jméno** a **Heslo** a zaškrtněte políčko **Uložit heslo.**
- Pomocí tlačítka **Ověřit připojení** ověřte, že jste zadali správné informace.

#### <a name="deploy-to-clean-web-server"></a>Nasazení na čistý webový server

- Pokud chcete zajistit, aby webový server měl po každém nahrání čistou kopii webové aplikace a aby z předchozího nasazení nezůstaly žádné další soubory, můžete zkontrolovat políčko **Odebrat další soubory v cílovém umístění na** kartě **Nastavení.**

- Upozornění: Publikování s tímto nastavením odstraní všechny soubory, které existují na webovém serveru (adresář wwwroot). Před publikováním se ujistěte, že znáte stav počítače s povolenou touto možností. 

![Nastavení publikování – stránka Nastavení]

## <a name="next-steps"></a>Další kroky

### <a name="set-up-cicd-for-automated-deployment-to-azure-vm"></a>Nastavení CI/CD pro automatizované nasazení do virtuálního počítače Azure

Pokud chcete nastavit kanál průběžného doručování s Azure Pipelines, přečtěte si informace [o nasazení na virtuální mste Windows](https://docs.microsoft.com/vsts/build-release/apps/cd/deploy-webdeploy-iis-deploygroups).

[VM Overview - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSName.png
[IP Address Config - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/IPAddressConfigDNSName.png
[VM Overview - DNS Configured]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSConfigured.png
[Publikovat stránku – šipka doprava]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageRightArrow.png
[Stránka publikování – ikona Virtuálního počítače Microsoft Azure]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageMicrosoftAzureVirtualMachineIcon.png
[Azure Account Selector]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectAccount.png
[Výběr virtuálních počítačů Azure]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectVM.png
[Přihlášení webdeployu]: ../../../includes/media/publish-web-app-from-visual-studio/WebDeployLogin.png
[Chyba certifikátu]: ../../../includes/media/publish-web-app-from-visual-studio/CertificateError.png
[Výstupní okno]: ../../../includes/media/publish-web-app-from-visual-studio/OutputWindow.png
[Publikovat stránku – tlačítko Publikovat]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPagePublishButton.png
[Publikovat stránku – tlačítko Nastavení]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageSettingsButton.png
[Nastavení publikování – stránka Připojení]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsConnectionPage.png
[Nastavení publikování – stránka Nastavení]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsSettingsPage.png
