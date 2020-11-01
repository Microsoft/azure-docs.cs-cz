---
title: Publikování webové aplikace na virtuálním počítači Azure ze sady Visual Studio
description: Publikování webové aplikace v ASP.NET ve virtuálním počítači Azure ze sady Visual Studio
author: ghogen
manager: jillfra
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: how-to
ms.date: 11/03/2017
ms.author: ghogen
ms.openlocfilehash: bdc03e8c136606ab7768705b0c8dbcc97782966f
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2020
ms.locfileid: "87088389"
---
# <a name="publish-an-aspnet-web-app-to-an-azure-vm-from-visual-studio"></a>Publikování webové aplikace v ASP.NET ve virtuálním počítači Azure ze sady Visual Studio

Tento dokument popisuje, jak publikovat webovou aplikaci v ASP.NET na virtuálním počítači Azure pomocí funkce publikování **Microsoft Azure Virtual Machines** v aplikaci Visual Studio 2019.  

## <a name="prerequisites"></a>Předpoklady
Aby bylo možné použít Visual Studio k publikování projektu ASP.NET na virtuálním počítači Azure, musí být virtuální počítač správně nastavený.

- Počítač musí být nakonfigurovaný tak, aby spouštěl webovou aplikaci v ASP.NET a nainstaloval nástroj WebDeploy. Další informace najdete v tématu [Vytvoření virtuálního počítače s ASP.NET pomocí nástroje WebDeploy](https://github.com/aspnet/Tooling/blob/AspNetVMs/docs/create-asp-net-vm-with-webdeploy.md).

- Virtuální počítač musí mít nakonfigurovaný název DNS. Další informace najdete v tématu [Vytvoření plně kvalifikovaného názvu domény v Azure Portal pro virtuální počítač s Windows](portal-create-fqdn.md).

## <a name="publish-your-aspnet-web-app-to-the-azure-vm-using-visual-studio"></a>Publikování webové aplikace v ASP.NET na virtuálním počítači Azure pomocí sady Visual Studio
Následující část popisuje, jak publikovat stávající webovou aplikaci v ASP.NET na virtuálním počítači Azure.

1. Otevřete řešení webové aplikace v aplikaci Visual Studio 2019.
2. Klikněte pravým tlačítkem na projekt v Průzkumník řešení a vyberte **publikovat...**
3. Pomocí šipky vpravo od stránky se posuňte mezi možnosti publikování, dokud nenajdete **Microsoft Azure Virtual Machines** .  

   ![Stránka pro publikování – šipka doprava]

4. Vyberte ikonu **Microsoft Azure Virtual Machines** a vyberte **publikovat** .

   ![Stránka pro publikování – ikona Microsoft Azure virtuálního počítače]

5. Vyberte příslušný účet (s předplatným Azure připojeným k vašemu virtuálnímu počítači).  
   - Pokud jste přihlášeni k aplikaci Visual Studio, seznam účtů se vyplní všemi vašimi ověřenými účty.  
   - Pokud nejste přihlášení nebo pokud účet, který potřebujete, není uveden v seznamu, vyberte Přidat účet... a podle zobrazených výzev se přihlaste.  
   ![Selektor účtu Azure]  

6. Vyberte příslušný virtuální počítač ze seznamu existujících Virtual Machines.

   > [!Note]
   > Naplnění tohoto seznamu může nějakou dobu trvat.

   ![Selektor virtuálních počítačů Azure]

7. Kliknutím na OK zahajte publikování.

8. Po zobrazení výzvy k zadání přihlašovacích údajů zadejte uživatelské jméno a heslo uživatelského účtu na cílovém virtuálním počítači, který je nakonfigurovaný s právy pro publikování. Tyto přihlašovací údaje jsou obvykle uživatelské jméno a heslo správce, které se používá při vytváření virtuálního počítače.  

   ![Přihlášení k webdeployu]

9. Přijměte certifikát zabezpečení.

   ![Chyba certifikátu]

10. Sledujte okno výstup a ověřte průběh operace publikování.

    ![Okno Výstup]

11. Pokud publikování proběhlo úspěšně, spustí se prohlížeč a otevře se adresa URL nově publikovaného webu.

**Nástup!**

Vaše webová aplikace se teď úspěšně publikovala na virtuálním počítači Azure.

## <a name="publish-page-options"></a>Možnosti publikování stránky

Po dokončení Průvodce publikováním se stránka publikování otevře v dokumentu dobře s vybraným novým profilem publikování.

### <a name="re-publish"></a>Znovu publikovat

Chcete-li publikovat aktualizace webové aplikace, vyberte tlačítko **publikovat** na stránce Publikovat.  
- Po zobrazení výzvy zadejte uživatelské jméno a heslo.  
- Publikování začne okamžitě.

![Stránka pro publikování – tlačítko publikovat]

### <a name="modify-publish-profile-settings"></a>Upravit nastavení profilu publikování

Chcete-li zobrazit a upravit nastavení profilu publikování, vyberte **nastavení...** .  

![Stránka pro publikování – tlačítko nastavení]

Vaše nastavení by mělo vypadat přibližně takto:  

![Nastavení publikování – stránka připojení]

#### <a name="save-user-name-and-password"></a>Uložit uživatelské jméno a heslo
- Vyhněte se zadání ověřovacích informací při každém publikování. Provedete to tak, že naplníte pole **uživatelské jméno** a **heslo** a zaškrtnete políčko **Uložit heslo** .
- Pomocí tlačítka **ověřit připojení** potvrďte, že jste zadali správné informace.

#### <a name="deploy-to-clean-web-server"></a>Nasadit na čistý webový server

- Pokud chcete zajistit, aby webový server měl po každém nahrání čistou kopii webové aplikace a nezůstaly žádné jiné soubory z předchozího nasazení, můžete zaškrtnout políčko **odebrat další soubory v cílovém umístění** na kartě **Nastavení** .

- Upozornění: publikování pomocí tohoto nastavení odstraní všechny soubory, které existují na webovém serveru (adresář wwwroot). Ujistěte se, že znáte stav počítače před publikováním s povolenou možností. 

![Nastavení publikování – stránka nastavení]

## <a name="next-steps"></a>Další kroky

### <a name="set-up-cicd-for-automated-deployment-to-azure-vm"></a>Nastavení CI/CD pro automatizované nasazení na virtuální počítač Azure

Postup nastavení kanálu průběžného doručování s Azure Pipelines najdete v tématu [nasazení na virtuální počítač s Windows](/vsts/build-release/apps/cd/deploy-webdeploy-iis-deploygroups).

[VM Overview - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSName.png
[IP Address Config - DNS Name]: ../../../includes/media/publish-web-app-from-visual-studio/IPAddressConfigDNSName.png
[VM Overview - DNS Configured]: ../../../includes/media/publish-web-app-from-visual-studio/VMOverviewDNSConfigured.png
[Stránka pro publikování – šipka doprava]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageRightArrow.png
[Stránka pro publikování – ikona Microsoft Azure virtuálního počítače]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageMicrosoftAzureVirtualMachineIcon.png
[Selektor účtu Azure]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectAccount.png
[Selektor virtuálních počítačů Azure]: ../../../includes/media/publish-web-app-from-visual-studio/ChooseVM-SelectVM.png
[Přihlášení k webdeployu]: ../../../includes/media/publish-web-app-from-visual-studio/WebDeployLogin.png
[Chyba certifikátu]: ../../../includes/media/publish-web-app-from-visual-studio/CertificateError.png
[okno Výstup]: ../../../includes/media/publish-web-app-from-visual-studio/OutputWindow.png
[Stránka pro publikování – tlačítko publikovat]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPagePublishButton.png
[Stránka pro publikování – tlačítko nastavení]: ../../../includes/media/publish-web-app-from-visual-studio/PublishPageSettingsButton.png
[Nastavení publikování – stránka připojení]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsConnectionPage.png
[Nastavení publikování – stránka nastavení]: ../../../includes/media/publish-web-app-from-visual-studio/PublishSettingsSettingsPage.png
